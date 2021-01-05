---
title: Migracja oparta na agencie w migracji Azure Migrate serwera
description: Zawiera omówienie migracji maszyn wirtualnych VMware opartych na agentach w Azure Migrate.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: c605c21307cda874f34ae5ea9f4e4959e5e6c183
ms.sourcegitcommit: aeba98c7b85ad435b631d40cbe1f9419727d5884
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/04/2021
ms.locfileid: "97861957"
---
# <a name="agent-based-migration-architecture"></a>Architektura migracji z użyciem agentów

Ten artykuł zawiera omówienie architektury i procesów używanych w ramach replikacji opartej na agentach maszyn wirtualnych VMware za pomocą narzędzia do [migracji serwera Azure Migrate:](migrate-services-overview.md#azure-migrate-server-assessment-tool) .

Korzystając z Azure Migrate: Migracja serwera, można replikować maszyny wirtualne VMware z kilkoma opcjami:

- Migrowanie maszyn wirtualnych przy użyciu replikacji opartej na agentach, zgodnie z opisem w tym artykule.
- Migrowanie maszyn wirtualnych VMware przy użyciu replikacji bez wykorzystania agentów. Powoduje to migrację maszyn wirtualnych bez konieczności instalowania na nich jakichkolwiek elementów.

Dowiedz się więcej na temat [wybierania i porównywania](server-migrate-overview.md) metod migracji maszyn wirtualnych VMware. 


## <a name="agent-based-migration"></a>Migracja oparta na agencie

Migracja oparta na agencie służy do migrowania lokalnych maszyn wirtualnych VMware i serwerów fizycznych na platformę Azure. Może również służyć do migrowania innych lokalnych serwerów zwirtualizowanych, a także maszyn wirtualnych chmur prywatnych i publicznych, w tym wystąpień AWS i maszyn wirtualnych GCP. Migracja oparta na agentach w Azure Migrate używa niektórych funkcji zaplecza z usługi [Azure Site Recovery](../site-recovery/site-recovery-overview.md) .


## <a name="architectural-components"></a>Składniki architektury

Diagram ilustruje składniki związane z migracją opartą na agentach.

![Diagram przedstawia składniki dla migracji opartej na agencie, które objaśniono w tabeli.](./media/agent-based-replication-architecture/architecture.png)

W tabeli zestawiono składniki używane do migracji opartej na agentach.

**Składnik** | **Szczegóły** | **Instalacja**
--- | --- | ---
**Urządzenie replikacji** | Urządzenie replikacji (serwer konfiguracji/serwer przetwarzania) jest maszyną lokalną, która działa jako Most między środowiskiem lokalnym i migracją serwera. Urządzenie odnajduje Spis maszyn lokalnych, dzięki czemu Migracja serwera może organizować replikację i migrację. Urządzenie ma dwa składniki:<br/><br/> **Serwer konfiguracji**: nawiązuje połączenie z migracją serwera i koordynuje replikację.<br/> **Serwer przetwarzania**: obsługuje replikację danych. Serwer przetwarzania odbiera dane maszyn, kompresuje je i szyfruje oraz wysyła do platformy Azure. Na platformie Azure Migracja serwera zapisuje dane na dyskach zarządzanych. | Domyślnie serwer przetwarzania jest instalowany razem z serwerem konfiguracji na urządzeniu replikacji.
**Usługa mobilności** | Usługa mobilności jest agentem zainstalowanym na każdej maszynie, która ma być replikowana i migrowana. Wysyła dane replikacji z komputera do serwera przetwarzania. | Pliki instalacyjne dla różnych wersji usługi mobilności znajdują się na urządzeniu replikacji. Należy pobrać i zainstalować agenta, którego potrzebujesz, zgodnie z systemem operacyjnym i wersją maszyny, która ma być replikowana.

## <a name="mobility-service-installation"></a>Instalacja usługi mobilności

Usługę mobilności można wdrożyć przy użyciu następujących metod:

- **Instalacja wypychana**: usługa mobilności jest instalowana przez serwer przetwarzania po włączeniu ochrony dla komputera. 
- **Zainstaluj ręcznie**: usługę mobilności można zainstalować ręcznie na każdym komputerze za pomocą interfejsu użytkownika lub wiersza polecenia.

Usługa mobilności komunikuje się z urządzeniem replikacji i replikowanymi maszynami. W przypadku oprogramowania antywirusowego uruchomionego na urządzeniu replikacji, serwerów przetwarzania lub replikowanych maszyn należy wykluczyć następujące foldery z skanowania:


- C:\Program Files\Microsoft Azure Recovery Services Agent
- C:\ProgramData\ASR
- C:\ProgramData\ASRLogs
- C:\ProgramData\ASRSetupLogs
- C:\ProgramData\LogUploadServiceLogs
- C:\ProgramData\Microsoft Azure Site Recovery
- C:\Program Files (x86) \Microsoft Azure Site Recovery
- C:\ProgramData\ASR\agent (na maszynach z systemem Windows z zainstalowaną usługą mobilności)

## <a name="replication-process"></a>Proces replikacji

1. Po włączeniu replikacji dla maszyny rozpocznie się replikacja początkowa na platformę Azure.
2. Podczas replikacji początkowej usługa mobilności odczytuje dane z dysków komputera i wysyła je do serwera przetwarzania.
3. Te dane są używane do wypełniania kopii dysku w ramach subskrypcji platformy Azure. 
4. Po zakończeniu replikacji początkowej zostanie rozpoczęta replikacja zmian różnicowych na platformie Azure. Replikacja jest na poziomie bloku i blisko ciągła.
4. Usługa mobilności przechwytuje operacje zapisu do pamięci dyskowej przez integrację z podsystemem magazynowania systemu operacyjnego. Ta metoda pozwala uniknąć operacji we/wy dysku na replikacji przyrostowej. 
5. Śledzone zmiany dla maszyny są wysyłane do serwera przetwarzania na porcie wejściowym HTTPS 9443. Ten port może być modyfikowany. Serwer przetwarzania kompresuje i szyfruje go i wysyła do platformy Azure. 

## <a name="ports"></a>Porty

**Urządzenie** | **Połączenie**
--- | --- 
**Replikowanie maszyn** | Usługa mobilności działająca na maszynach wirtualnych komunikuje się z lokalnym urządzeniem do replikacji na porcie HTTPS 443 ruchu przychodzącego na potrzeby zarządzania replikacją.<br/><br/> Maszyny wysyłają dane replikacji do serwera przetwarzania na porcie HTTPS 9443 w ruchu przychodzącym. Ten port może być modyfikowany.
**Urządzenie replikacji** | Urządzenie replikacji organizuje replikację za pomocą platformy Azure przez port HTTPS 443.
**Serwer przetwarzania** | Serwer przetwarzania odbiera dane replikacji, optymalizuje je i szyfruje oraz wysyła do usługi Azure Storage przez port 443 wychodzące.


## <a name="performance-and-scaling"></a>Wydajność i skalowanie

Domyślnie wdrażane jest pojedyncze urządzenie do replikacji, na którym działa serwer konfiguracji i serwer przetwarzania. W przypadku replikowania tylko kilku maszyn to wdrożenie jest wystarczające. Jednak w przypadku replikowania i migrowania setek maszyn jeden serwer przetwarzania może nie być w stanie obsłużyć całego ruchu związanego z replikacją. W takim przypadku można wdrożyć dodatkowe serwery przetwarzania skalowalnego w poziomie.

### <a name="plan-vmware-deployment"></a>Planowanie wdrożenia VMware

Jeśli replikujesz maszyny wirtualne VMware, możesz użyć [Planista wdrażania Site Recovery dla programu VMware](../site-recovery/site-recovery-deployment-planner.md), aby pomóc określić wymagania dotyczące wydajności, w tym dzienny współczynnik zmian danych i serwery przetwarzania, których potrzebujesz.

### <a name="replication-appliance-capacity"></a>Pojemność urządzenia replikacji

Użyj wartości w tej tabeli, aby ustalić, czy potrzebujesz dodatkowego serwera przetwarzania w danym wdrożeniu.

- Jeśli dzienny współczynnik zmian (tempo zmiany) przekracza 2 TB, wdróż dodatkowy serwer przetwarzania.
- W przypadku replikowania więcej niż 200 maszyn należy wdrożyć dodatkowe urządzenie do replikacji.

**Procesor CPU** | **Pamięć** | **Wolne miejsce — buforowanie danych** | **Współczynnik zmian** | **Limity replikacji**
--- | --- | --- | --- | ---
8 procesorów wirtualnych vCPU (2 gniazda * 4 rdzenie \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB lub mniej | Maszyny < 100 
12 procesorów wirtualnych vCPU (2 gniazda * 6 rdzeni \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB do 1 TB | 100-150 maszyn.
16 procesorów wirtualnych vCPU (2 gniazda * 8 rdzeni \@ 2,5 GHz) | 32 GB |  1 TB | 1 TB do 2 TB | 151-200 maszyn.

### <a name="sizing-scale-out-process-servers"></a>Ustalanie rozmiaru serwerów przetwarzania skalowalnego w poziomie

Jeśli musisz wdrożyć serwer przetwarzania skalowalnego w poziomie, Użyj tej tabeli, aby ustalić rozmiar serwera.

**Serwer przetwarzania** | **Wolne miejsce dla buforowania danych** | **Współczynnik zmian** | **Limity replikacji**
--- | --- | --- | --- 
4 procesorów wirtualnych vCPU (2 gniazda * 2 rdzenie \@ 2,5 GHz), 8 GB pamięci | 300 GB | 250 GB lub mniej | Do 85 maszyn 
8 procesorów wirtualnych vCPU (2 gniazda * 4 rdzenie \@ 2,5 GHz), 12 GB pamięci | 600 GB | 251 GB do 1 TB | 86-150 maszyn.
12 procesorów wirtualnych vCPU (2 gniazda * 6 rdzeni \@ 2,5 GHz), 24 GB pamięci | 1 TB | 1-2 TB | 151-225 maszyn.

## <a name="throttle-upload-bandwidth"></a>Ogranicz przepustowość przekazywania.

Ruch VMware replikowany do platformy Azure odbywa się za pomocą określonego serwera przetwarzania. Przepływność przekazywania można ograniczyć, ograniczając przepustowość na komputerach, na których działają jako serwery przetwarzania. Korzystając z tego klucza rejestru, można mieć wpływ na przepustowość:

- Wartość rejestru HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM określa liczbę wątków używanych na potrzeby transferu danych (replikacja początkowa lub różnicowa) dysku. Wyższa wartość zwiększa przepustowość sieci używaną podczas replikacji. Wartość domyślna to 4. Wartość maksymalna to 32. Monitoruj ruch, aby zoptymalizować tę wartość.
- Ponadto można ograniczyć przepustowość na komputerze serwera przetwarzania w następujący sposób:

    1. Na komputerze serwera przetwarzania Otwórz przystawkę MMC Azure Backup. Istnieje skrót na pulpicie lub w folderze C:\Program Files\Microsoft Azure Recovery Services Agent\bin. 
    2. W przystawce wybierz pozycję **Zmień właściwości**.
    3. W obszarze **ograniczenie przepustowości** wybierz opcję **Włącz ograniczenie przepustowości Internetu dla operacji tworzenia kopii zapasowej**. Ustaw limity dla godzin pracy i czasu wolnego. Prawidłowe zakresy są z zakresu od 512 KB/s do 1 023 MB/s.


## <a name="next-steps"></a>Następne kroki

Wypróbuj [migrację opartą na agencie](tutorial-migrate-vmware-agent.md) dla oprogramowania [VMware](tutorial-migrate-vmware-agent.md) lub [serwerów fizycznych](tutorial-migrate-physical-virtual-machines.md).
