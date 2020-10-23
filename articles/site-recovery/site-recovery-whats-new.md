---
title: Co nowego w Azure Site Recovery
description: Zawiera podsumowanie nowych funkcji i najnowszych aktualizacji usługi Azure Site Recovery.
ms.topic: conceptual
ms.date: 10/22/2020
ms.openlocfilehash: 0c35c8e041e47f02d53c680b5812b10d25d75a67
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92425714"
---
# <a name="whats-new-in-site-recovery"></a>Co nowego w usłudze Site Recovery

Usługa [Azure Site Recovery](site-recovery-overview.md) jest regularnie aktualizowana i ulepszana. Aby pomóc Ci zachować aktualność, ten artykuł zawiera informacje o najnowszych wersjach, nowych funkcjach i nowej zawartości. Ta strona jest regularnie aktualizowana.

Możesz postępować zgodnie z Site Recovery powiadomieniami o aktualizacji w kanale [aktualizacji platformy Azure](https://azure.microsoft.com/updates/?product=site-recovery) .

## <a name="supported-updates"></a>Obsługiwane aktualizacje

W przypadku składników Site Recovery obsługiwane są wersje N-4, gdzie N to najnowsza wydana wersja. Podsumowanie dotyczące tych raportów można znaleźć w poniższej tabeli.

**Aktualizowanie** |  **Ujednolicona konfiguracja** | **Komórki jajowe serwera konfiguracji** | **Agent usługi mobilności** | **Dostawca Site Recovery** | **Agent Recovery Services**
--- | --- | --- | --- | --- | ---
[Pakiet zbiorczy 51](https://support.microsoft.com/help/4590304)  | 9.38.5761.1 | 5.1.6400.0 | 9.38.5761.1 | 5.1.6400.0  | 2.0.9193.0
[Pakiet zbiorczy 50](https://support.microsoft.com/help/4582666/) | 9.37.5724.1 | 5.1.6347.0 | 9.37.5724.1 | 5.1.6347.0  | 2.0.9192.0
[Pakiet zbiorczy 49](https://support.microsoft.com/help/4578241/) | 9.36.5696.1 | 5.1.6315.0 | 9.36.5696.1 | 5.1.6315.0 | 2.0.9188.0
[Pakiet zbiorczy 48](https://support.microsoft.com/help/4573888/) | 9.35.5659.1 | 5.1.6200.0 | 9.35.5659.1 | 5.1.6200.0 | 2.0.9186.0
[Pakiet zbiorczy 47](https://support.microsoft.com/help/4570609/) | 9.34.5634.1 | 5.1.6100.0 | 9.34.5634.1 | 5.1.6100.0 | Maszyny z funkcją Hyper-v — 2.0.9183.0 <br> Maszyny VMware — 2.0.9177.0


[Dowiedz się więcej](service-updates-how-to.md) na temat instalacji i obsługi aktualizacji.


### <a name="update-rollup-51"></a>Pakiet zbiorczy aktualizacji 51

[Pakiet zbiorczy aktualizacji 51](https://support.microsoft.com/help/4590304/update-rollup-51-for-azure-site-recovery) udostępnia następujące aktualizacje:

**Aktualizowanie** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacje Site Recovery agentów i dostawców zgodnie z opisem w zestawie zbiorczym.
**Rozwiązywanie poprawek/ulepszeń** | Szereg poprawek i ulepszeń, które są szczegółowo opisane w pakiecie zbiorczym, w tym o nowej obsłudze systemu Linux dla usługi mobilności.

## <a name="update-september-2020"></a>Aktualizacja (2020 września)

[Pakiet zbiorczy aktualizacji 50](https://support.microsoft.com/help/4582666/update-rollup-50-for-azure-site-recovery) udostępnia następujące aktualizacje:

**Aktualizowanie** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacje Site Recovery agentów i dostawców zgodnie z opisem w zestawie zbiorczym.
**Rozwiązywanie poprawek/ulepszeń** | Szereg poprawek i ulepszeń, które opisano szczegółowo w pakiecie zbiorczym.

## <a name="updates-august-2020"></a>Aktualizacje (sierpień 2020)

### <a name="update-rollup-49"></a>Pakiet zbiorczy aktualizacji 49

[Pakiet zbiorczy aktualizacji 49](https://support.microsoft.com/help/4578241/update-rollup-49-for-azure-site-recovery) udostępnia następujące aktualizacje:

**Aktualizowanie** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacje Site Recovery agentów i dostawców zgodnie z opisem w zestawie zbiorczym.
**Rozwiązywanie poprawek/ulepszeń** | Szereg poprawek i ulepszeń, które są szczegółowo opisane w pakiecie zbiorczym, w tym o nowej obsłudze systemu Linux dla usługi mobilności.

## <a name="updates-july-2020"></a>Aktualizacje (lipiec 2020)

### <a name="update-rollup-48"></a>Pakiet zbiorczy aktualizacji 48

[Pakiet zbiorczy aktualizacji 48](https://support.microsoft.com/help/4573888/update-rollup-48-for-azure-site-recovery) udostępnia następujące aktualizacje:

**Aktualizowanie** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacje Site Recovery agentów i dostawców zgodnie z opisem w zestawie zbiorczym.
**Rozwiązywanie poprawek/ulepszeń** | Szereg poprawek i ulepszeń, które opisano szczegółowo w pakiecie zbiorczym.

> [!NOTE]
> Pakiet zbiorczy aktualizacji 48 ma znany problem dotyczący włączania replikacji dla maszyn z systemem Linux zaszyfrowanych przy użyciu systemu ADE. [Dowiedz się więcej](./azure-to-azure-troubleshoot-errors.md#enable-protection-failed-as-the-installer-is-unable-to-find-the-root-disk-error-code-151137).

### <a name="update-rollup-47"></a>Pakiet zbiorczy aktualizacji 47

[Pakiet zbiorczy aktualizacji 47](https://support.microsoft.com/help/4570609/update-rollup-47-for-azure-site-recovery) udostępnia następujące aktualizacje:

**Aktualizowanie** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacje Site Recovery agentów i dostawców zgodnie z opisem w zestawie zbiorczym.
**Rozwiązywanie poprawek/ulepszeń** | Szereg poprawek i ulepszeń, które opisano szczegółowo w pakiecie zbiorczym.

## <a name="updates-june-2020"></a>Aktualizacje (Czerwiec 2020)

### <a name="update-rollup-46"></a>Pakiet zbiorczy aktualizacji 46

[Pakiet zbiorczy aktualizacji 46](https://support.microsoft.com/help/4564347/update-rollup-46-for-azure-site-recovery) udostępnia następujące aktualizacje:

**Aktualizowanie** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacje Site Recovery agentów i dostawców zgodnie z opisem w zestawie zbiorczym.
**Rozwiązywanie poprawek/ulepszeń** | Szereg poprawek i ulepszeń, które opisano szczegółowo w pakiecie zbiorczym.

## <a name="updates-march-2020"></a>Aktualizacje (marzec 2020)

### <a name="update-rollup-45"></a>Pakiet zbiorczy aktualizacji 45

[Pakiet zbiorczy aktualizacji 45](https://support.microsoft.com/help/4550047/update-rollup-45-for-azure-site-recovery) udostępnia następujące aktualizacje:

**Aktualizowanie** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacje Site Recovery agentów i dostawców zgodnie z opisem w zestawie zbiorczym.
**Rozwiązywanie poprawek/ulepszeń** | Szereg poprawek i ulepszeń, które opisano szczegółowo w pakiecie zbiorczym.

## <a name="updates-january-2020"></a>Aktualizacje (styczeń 2020)

### <a name="update-rollup-44"></a>Pakiet zbiorczy aktualizacji 44

[Pakiet zbiorczy aktualizacji 44](https://support.microsoft.com/help/4538187/update-rollup-44-for-azure-site-recovery) udostępnia następujące aktualizacje.

**Aktualizowanie** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Nie było żadnych aktualizacji dla dostawców Site Recovery i agentów.
**Rozwiązywanie poprawek/ulepszeń** | Szereg poprawek i ulepszeń, które opisano szczegółowo w pakiecie zbiorczym.

### <a name="azure-vmware-disaster-recovery"></a>Odzyskiwanie po awarii programu VMware Azure

Usługi Azure Virtual Machines obsługują teraz maszyny wirtualne, które umożliwiają szyfrowanie w spoczynku z kluczami zarządzanymi przez klienta. [Dowiedz się więcej](azure-to-azure-how-to-enable-replication-cmk-disks.md).


### <a name="update-rollup-43"></a>Pakiet zbiorczy aktualizacji 43

[Pakiet zbiorczy aktualizacji 43](https://support.microsoft.com/help/4537047/update-rollup-43-for-azure-site-recovery) udostępnia następujące aktualizacje.

**Aktualizowanie** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacje Site Recovery agentów i dostawców (zgodnie z opisem w pakiecie zbiorczym)
**Rozwiązywanie poprawek/ulepszeń** | Szereg poprawek i ulepszeń (zgodnie z opisem w pakiecie zbiorczym)


## <a name="updates-november-2019"></a>Aktualizacje (listopad 2019)

### <a name="update-rollup-42"></a>Pakiet zbiorczy aktualizacji 42

[Pakiet zbiorczy aktualizacji 42](https://support.microsoft.com/help/4531426/update-rollup-42-for-azure-site-recovery) udostępnia następujące aktualizacje.

**Aktualizowanie** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacje Site Recovery agentów i dostawców (zgodnie z opisem w pakiecie zbiorczym)
**Rozwiązywanie poprawek/ulepszeń** | Szereg poprawek i ulepszeń (zgodnie z opisem w pakiecie zbiorczym)


### <a name="azure-vm-disaster-recovery"></a>Odzyskiwanie po awarii maszyny wirtualnej platformy Azure

W tabeli zestawiono nowe funkcje odzyskiwania po awarii maszyny wirtualnej platformy Azure.

**Funkcja** | **Szczegóły**
--- | ---
**UEFI** | Site Recovery teraz obsługuje odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure z architekturą rozruchową opartą na interfejsie UEFI.
**Linux** | Site Recovery teraz obsługuje maszyny wirtualne platformy Azure z systemem Linux z Azure Disk Encryption (ADE).
**Druga generacja** | Wszystkie maszyny wirtualne platformy Azure generacji 2 są teraz obsługiwane na potrzeby odzyskiwania po awarii.
**Regiony** | Teraz można włączyć odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure w geograficznie.

### <a name="vmware-to-azure-disaster-recovery"></a>Odzyskiwanie po awarii VMware–Azure

Nowe funkcje odzyskiwania po awarii oprogramowania VMware na platformę Azure zostały podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**UEFI** | Site Recovery teraz obsługuje odzyskiwanie po awarii dla maszyn wirtualnych VMware z architekturą rozruchową opartą na interfejsie UEFI.<br/><br/> Obsługiwane systemy operacyjne obejmują Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, SLES 12 SP4, RHEL 8.

## <a name="update-to-servicing-stack-updatesha-2"></a>Aktualizacja do obsługi aktualizacji stosu/SHA-2

W przypadku odzyskiwania po awarii maszyn wirtualnych platformy Azure do regionu pomocniczego lub lokalnych maszyn wirtualnych VMware lub serwerów fizycznych na platformie Azure należy pamiętać o następujących kwestiach:

- Z wersji 9.30.5407.1 rozszerzenia usługi mobilności (dla maszyn wirtualnych platformy Azure) i agenta usługi mobilności (dla komputerów z oprogramowaniem VMware/Physical) w niektórych systemach operacyjnych maszyny musi być uruchomiona aktualizacja stosu obsługi i algorytm SHA-2. Szczegóły przedstawiono w poniższej tabeli.
- Zainstaluj aktualizację i algorytm SHA-2 zgodnie z połączoną bazą wiedzy. Algorytm SHA-1 nie jest obsługiwany z września 2019 i jeśli podpisywanie kodu SHA-2 nie jest włączone, rozszerzenie agenta nie zostanie zainstalowane/uaktualnione zgodnie z oczekiwaniami.
- Dowiedz się więcej o [uaktualnieniu i wymaganiach algorytmu SHA-2](https://aka.ms/SHA-2KB).

**System operacyjny** | **Maszyna wirtualna platformy Azure** | **Maszyna wirtualna VMware/komputer fizyczny**
--- | --- | ---
**Windows 2008 R2 z dodatkiem SP1** | [Obsługa aktualizacji stosu](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Obsługa aktualizacji stosu](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 2008 z dodatkiem SP2** | [Obsługa aktualizacji stosu](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Obsługa aktualizacji stosu](https://support.microsoft.com/help/4493730)<br/> [SHA-2](https://support.microsoft.com/help/4474419)
**Windows 7 z dodatkiem SP1** | [Obsługa aktualizacji stosu](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419)| [Obsługa aktualizacji stosu](https://support.microsoft.com/help/4490628)<br/> [SHA-2](https://support.microsoft.com/help/4474419).



## <a name="updates-october-2019"></a>Aktualizacje (październik 2019)

### <a name="update-rollup-41"></a>Pakiet zbiorczy aktualizacji 41

[Pakiet zbiorczy aktualizacji 41](https://support.microsoft.com/help/4528026/update-rollup-41-for-azure-site-recovery) udostępnia następujące aktualizacje.

**Aktualizowanie** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacje Site Recovery agentów i dostawców (zgodnie z opisem w pakiecie zbiorczym)
**Rozwiązywanie poprawek/ulepszeń** | Szereg poprawek i ulepszeń (zgodnie z opisem w pakiecie zbiorczym)



### <a name="azure-vm-disaster-recovery"></a>Odzyskiwanie po awarii maszyny wirtualnej platformy Azure

W tabeli zestawiono nowe funkcje odzyskiwania po awarii maszyny wirtualnej platformy Azure.

**Funkcja** | **Szczegóły**
--- | ---
**Ustawienia testu pracy w trybie failover** | Podczas konfigurowania testowej pracy w trybie failover można teraz skonfigurować ustawienia dla maszyny wirtualnej w trybie failover i sieci, w tym adresu IP, sieciowej grupy zabezpieczeń, wewnętrznego równoważenia obciążenia i publicznego adresu IP dla każdej karty sieciowej komputera. Te ustawienia są opcjonalne i nie zmieniają bieżącego zachowania. Jeśli te ustawienia nie są skonfigurowane, możesz wybrać sieć wirtualną platformy Azure w momencie testowania pracy w trybie failover. [Dowiedz się więcej](https://azure.microsoft.com/blog/customize-networking-for-dr-drills-azure-site-recovery/).
**Plany odzyskiwania** | Plany odzyskiwania są teraz ograniczone do 100 maszyn wirtualnych, aby zapewnić niezawodność trybu failover.

### <a name="vmware-to-azure-disaster-recovery"></a>Odzyskiwanie po awarii VMware–Azure

Nowe funkcje odzyskiwania po awarii oprogramowania VMware na platformę Azure zostały podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Plany odzyskiwania** | Plany odzyskiwania są teraz ograniczone do 100 maszyn wirtualnych, aby zapewnić niezawodność trybu failover.


## <a name="updates-september-2019"></a>Aktualizacje (2019 września)

### <a name="update-rollup-40"></a>Pakiet zbiorczy aktualizacji 40

[Pakiet zbiorczy aktualizacji 40](https://support.microsoft.com/help/4521530/update-rollup-40-for-azure-site-recovery) udostępnia następujące aktualizacje.

**Aktualizowanie** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacje Site Recovery agentów i dostawców (zgodnie z opisem w pakiecie zbiorczym)
**Rozwiązywanie poprawek/ulepszeń** | Szereg poprawek i ulepszeń (zgodnie z opisem w pakiecie zbiorczym)




### <a name="azure-vm-disaster-recovery"></a>Odzyskiwanie po awarii maszyny wirtualnej platformy Azure

W tabeli zestawiono nowe funkcje odzyskiwania po awarii maszyny wirtualnej platformy Azure.

**Funkcja** | **Szczegóły**
--- | ---
**Oczyść po powrocie po awarii** | Po przejściu w tryb failover na pomocniczą platformę Azure, a następnie po awarii do regionu podstawowego program Site Recovery automatycznie czyści maszyny w regionie pomocniczym. Nie ma potrzeby ręcznego usuwania maszyn wirtualnych i kart interfejsu sieciowego.
**Test pracy w trybie failover zachowuje adres IP** | Teraz można zachować adres IP źródłowej maszyny wirtualnej podczas przechodzenia do szczegółów odzyskiwania po awarii i wybrać statyczny adres IP dla testowego trybu failover.

### <a name="vmwarephysical-server-disaster-recovery"></a>Odzyskiwanie po awarii oprogramowania VMware/serwera fizycznego

Funkcje dodane w tym miesiącu zostały podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
Nowe alerty serwera przetwarzania | Dodaliśmy nowe alerty serwera przetwarzania. [Dowiedz się więcej](vmware-physical-azure-monitor-process-server.md).

### <a name="hyper-v-disaster-recovery"></a>Odzyskiwanie awaryjne funkcji Hyper-V

Funkcje dodane w tym miesiącu zostały podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
Konto magazynu | Site Recovery teraz obsługuje używanie kont magazynu z włączoną zaporą dla funkcji Hyper-V na potrzeby odzyskiwania po awarii platformy Azure.  Można wybrać konta magazynu z obsługą zapory jako konto docelowe lub magazyn pamięci podręcznej. W przypadku korzystania z konta z włączoną zaporą upewnij się, że opcja zezwala na używanie zaufanych usług firmy Microsoft.<br/><br/> Jest to obsługiwane w przypadku maszyn wirtualnych funkcji Hyper-V z programem System Center VMM lub bez niego.


## <a name="updates-august-2019"></a>Aktualizacje (sierpień 2019)

### <a name="update-rollup-39"></a>Pakiet zbiorczy aktualizacji 39

[Pakiet zbiorczy aktualizacji 39](https://support.microsoft.com/help/4517283/update-rollup-39-for-azure-site-recovery) udostępnia następujące aktualizacje.

**Aktualizowanie** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacje Site Recovery agentów i dostawców (zgodnie z opisem w pakiecie zbiorczym)
**Rozwiązywanie poprawek/ulepszeń** | Szereg poprawek i ulepszeń (zgodnie z opisem w pakiecie zbiorczym)


### <a name="azure-vm-disaster-recovery"></a>Odzyskiwanie po awarii maszyny wirtualnej platformy Azure

W tabeli zestawiono nowe funkcje odzyskiwania po awarii maszyny wirtualnej platformy Azure.

**Funkcja** | **Szczegóły**
--- | ---
**Szyfrowanie bez usługi Azure AD** | Szyfrowanie bez aplikacji usługi Azure AD jest teraz obsługiwane na potrzeby replikacji maszyny wirtualnej platformy Azure do dysków zarządzanych z systemem Windows.
**Zasoby sieciowe do przejścia w tryb failover** | W przypadku przechodzenia w tryb failover do innego regionu można teraz dołączać ustawienia zasobów sieciowych (sieciowych grup zabezpieczeń, równoważenie obciążenia, publiczny adres IP) do maszyny wirtualnej.

## <a name="updates-july-2019"></a>Aktualizacje (lipiec 2019)

### <a name="update-rollup-38"></a>Pakiet zbiorczy aktualizacji 38

[Pakiet zbiorczy aktualizacji 38](https://support.microsoft.com/help/4513507/) udostępnia następujące aktualizacje.

**Aktualizowanie** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacje Site Recovery agentów i dostawców (zgodnie z opisem w pakiecie zbiorczym)
**Rozwiązywanie poprawek/ulepszeń** | Szereg poprawek i ulepszeń (zgodnie z opisem w pakiecie zbiorczym)


### <a name="general"></a>Ogólne

Site Recovery teraz obsługuje używanie kont magazynu ogólnego przeznaczenia w wersji 2 dla magazynu pamięci podręcznej lub magazynu docelowego. Obsługiwane były poprzednio tylko v1.

### <a name="vmware-to-azure-disaster-recovery"></a>Odzyskiwanie po awarii VMware–Azure

Teraz można replikować dyski do 8 TB w przypadku replikowania do maszyny wirtualnej platformy Azure z dyskami zarządzanymi.


## <a name="updates-june-2019"></a>Aktualizacje (czerwiec 2019)

### <a name="update-rollup-37"></a>Pakiet zbiorczy aktualizacji 37

[Pakiet zbiorczy aktualizacji 37](https://support.microsoft.com/help/4508614/) udostępnia następujące aktualizacje.

**Aktualizowanie** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacje Site Recovery agentów i dostawców (zgodnie z opisem w pakiecie zbiorczym)
**Rozwiązywanie poprawek/ulepszeń** | Szereg poprawek i ulepszeń (zgodnie z opisem w pakiecie zbiorczym)


### <a name="vmwarephysical-server-disaster-recovery"></a>Odzyskiwanie po awarii oprogramowania VMware/serwera fizycznego

Funkcje dodane w tym miesiącu zostały podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Partycje GPT** | Z pakietu zbiorczego aktualizacji 37 (usługa mobilności w wersji 9.25.5241.1), w interfejsie UEFI są obsługiwane maksymalnie pięć partycji GPT. Przed tą aktualizacją, cztery były obsługiwane.



## <a name="updates-may-2019"></a>Aktualizacje (2019 maja)

### <a name="update-rollup-36"></a>Pakiet zbiorczy aktualizacji 36

[Pakiet zbiorczy aktualizacji 36](https://support.microsoft.com/help/4503156) udostępnia następujące aktualizacje.

**Aktualizowanie** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacja Site Recovery agentów i dostawców (zgodnie z opisem w zestawie zbiorczym)
**Rozwiązywanie poprawek/ulepszeń** | Szereg poprawek i ulepszeń (zgodnie z opisem w pakiecie zbiorczym)

### <a name="azure-vm-disaster-recovery"></a>Odzyskiwanie po awarii maszyny wirtualnej platformy Azure

Funkcje dodane w tym miesiącu zostały podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Replikuj dodane dyski** | Włącz replikację dla dysków danych dodanych do maszyny wirtualnej platformy Azure, która jest już włączona na potrzeby odzyskiwania po awarii. [Dowiedz się więcej](azure-to-azure-enable-replication-added-disk.md).
**Automatyczne aktualizacje** | Podczas konfigurowania automatycznych aktualizacji rozszerzenia usługi mobilności uruchomionego na maszynach wirtualnych platformy Azure z włączoną obsługą odzyskiwania po awarii można teraz wybrać istniejące konto usługi Automation, które ma być używane, zamiast korzystać z domyślnego konta utworzonego przez Site Recovery. [Dowiedz się więcej](azure-to-azure-autoupdate.md).


### <a name="vmwarephysical-server-disaster-recovery"></a>Odzyskiwanie po awarii oprogramowania VMware/serwera fizycznego

Funkcje dodane w tym miesiącu zostały podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Monitorowanie serwera przetwarzania** | W przypadku odzyskiwania po awarii lokalnych maszyn wirtualnych programu VMware i serwerów fizycznych, monitorowania i rozwiązywania problemów z serwerem przetwarzania przy ulepszonych raportach o kondycji serwera i alertach. [Dowiedz się więcej](vmware-physical-azure-monitor-process-server.md).





## <a name="updates-march-2019"></a>Aktualizacje (marzec 2019)

### <a name="update-rollup-35"></a>Pakiet zbiorczy aktualizacji 35

[Pakiet zbiorczy aktualizacji 35](https://support.microsoft.com/en-us/help/4494485/update-rollup-35-for-azure-site-recovery) udostępnia następujące aktualizacje.

**Aktualizowanie** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacja Site Recovery agentów i dostawców (zgodnie z opisem w zestawie zbiorczym)
**Rozwiązywanie poprawek/ulepszeń** | Szereg poprawek i ulepszeń (zgodnie z opisem w pakiecie zbiorczym)

### <a name="vmwarephysical-server-disaster-recovery"></a>Odzyskiwanie po awarii oprogramowania VMware/serwera fizycznego

Funkcje dodane w tym miesiącu zostały podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Dyski zarządzane** | Replikacja lokalnych maszyn wirtualnych programu VMware i serwerów fizycznych jest teraz bezpośrednio w przypadku dysków zarządzanych na platformie Azure. Dane lokalne są wysyłane do konta magazynu pamięci podręcznej na platformie Azure, a punkty odzyskiwania są tworzone na dyskach zarządzanych w lokalizacji docelowej. Dzięki temu nie trzeba zarządzać wieloma docelowymi kontami magazynu.
**Serwer konfiguracji** | Site Recovery teraz obsługuje serwery konfiguracji z wieloma kartami sieciowymi. Przed zarejestrowaniem serwera konfiguracji w magazynie należy dodać dodatkowe karty do maszyny wirtualnej serwera konfiguracji. Po dodaniu później należy ponownie zarejestrować serwer w magazynie.


## <a name="updates-february-2019"></a>Aktualizacje (luty 2019)

### <a name="update-rollup-34"></a>Pakiet zbiorczy aktualizacji 34

[Pakiet zbiorczy aktualizacji 34](https://support.microsoft.com/help/4490016/update-rollup-34-for-azure-site-recovery) udostępnia następujące aktualizacje.

**Aktualizowanie** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacja Site Recovery agentów i dostawców (zgodnie z opisem w pakiecie zbiorczym).
**Rozwiązywanie poprawek/ulepszeń** | Szereg poprawek i ulepszeń (zgodnie z opisem w pakiecie zbiorczym).


### <a name="update-rollup-33"></a>Pakiet zbiorczy aktualizacji 33

[Pakiet zbiorczy aktualizacji 33](https://support.microsoft.com/help/4489582/update-rollup-33-for-azure-site-recovery) udostępnia następujące aktualizacje.

**Aktualizowanie** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacja Site Recovery agentów i dostawców (zgodnie z opisem w pakiecie zbiorczym).
**Rozwiązywanie poprawek/ulepszeń** | Szereg poprawek i ulepszeń (zgodnie z opisem w pakiecie zbiorczym).


### <a name="azure-vm-disaster-recovery"></a>Odzyskiwanie po awarii maszyny wirtualnej platformy Azure

Funkcje dodane w tym miesiącu zostały podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Mapowanie sieci** | W przypadku odzyskiwania po awarii maszyny wirtualnej platformy Azure można teraz używać dowolnej dostępnej sieci docelowej po włączeniu replikacji.
**Dysk SSD w warstwie Standardowa** | Teraz można skonfigurować odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure przy użyciu [dysków SSD w warstwie Standardowa](../virtual-machines/disks-types.md#standard-ssd).
**Bezpośrednie miejsca do magazynowania** | Odzyskiwanie po awarii można skonfigurować dla aplikacji działających w usłudze Azure VM Apps przy użyciu [bezpośrednie miejsca do magazynowania](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) w celu zapewnienia wysokiej dostępności.  Używanie Bezpośrednie miejsca do magazynowania (S2D) wraz z Site Recovery zapewnia kompleksową ochronę obciążeń maszyn wirtualnych platformy Azure. Funkcja S2D umożliwia hostowanie klastra gościa na platformie Azure. Jest to szczególnie przydatne w przypadku, gdy maszyna wirtualna obsługuje aplikację krytyczną, taką jak warstwa SAP ASCS, SQL Server lub serwer plików skalowalny w poziomie.


### <a name="vmwarephysical-server-disaster-recovery"></a>Odzyskiwanie po awarii oprogramowania VMware/serwera fizycznego

Funkcje dodane w tym miesiącu zostały podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**System plików BRTFS systemu Linux** | Site Recovery teraz obsługuje replikację maszyn wirtualnych VMware przy użyciu systemu plików BRTFS. Replikacja nie jest obsługiwana, jeśli:<br/><br/>-Wolumin systemu plików BTRFS jest zmieniany po włączeniu replikacji.<br/><br/>— System plików jest rozłożony na wiele dysków.<br/><br/>— System plików BTRFS obsługuje macierz RAID.
**Windows Server 2019** | Dodano obsługę dla maszyn z systemem Windows Server 2019.


## <a name="updates-january-2019"></a>Aktualizacje (styczeń 2019)


### <a name="accelerated-networking-azure-vms"></a>Przyspieszona sieć (maszyny wirtualne platformy Azure)

Przyspieszona sieć umożliwia wirtualizację we/wy z jednym elementem głównym (SR-IOV) do maszyny wirtualnej, co zwiększa wydajność sieci. Po włączeniu replikacji dla maszyny wirtualnej platformy Azure Site Recovery wykrywa, czy jest włączona szybsza sieć. Jeśli tak jest, po przejściu w tryb failover Site Recovery automatycznie skonfigurować przyspieszone sieci na maszynie wirtualnej platformy Azure dla repliki docelowej dla [systemów Windows](../virtual-network/create-vm-accelerated-networking-powershell.md#enable-accelerated-networking-on-existing-vms) i [Linux](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

[Dowiedz się więcej](azure-vm-disaster-recovery-with-accelerated-networking.md).

### <a name="update-rollup-32"></a>Pakiet zbiorczy aktualizacji 32

[Pakiet zbiorczy aktualizacji 32](https://support.microsoft.com/help/4485985/update-rollup-32-for-azure-site-recovery) udostępnia następujące aktualizacje.

**Aktualizowanie** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacja Site Recovery agentów i dostawców (zgodnie z opisem w pakiecie zbiorczym).
**Rozwiązywanie poprawek/ulepszeń** | Szereg poprawek i ulepszeń (zgodnie z opisem w pakiecie zbiorczym).

### <a name="azure-vm-disaster-recovery"></a>Odzyskiwanie po awarii maszyny wirtualnej platformy Azure

Funkcje dodane w tym miesiącu zostały podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Obsługa systemu Linux** | Dodano obsługę dla RedHat stacji roboczych 6/7 i nowych wersji jądra dla Ubuntu, Debian i SUSE.
**Bezpośrednie miejsca do magazynowania** | Site Recovery obsługuje maszyny wirtualne platformy Azure przy użyciu funkcji Bezpośrednie miejsca do magazynowania (S2D).

### <a name="vmware-vmsphysical-servers-disaster-recovery"></a>Odzyskiwanie po awarii maszyn wirtualnych VMware/serwerów fizycznych

Funkcje dodane w tym miesiącu zostały podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Obsługa systemu Linux** | Dodano obsługę do RedHat Enterprise Linux 7,6, RedHat Workstation 6/7, Oracle Linux 6,10 i Oracle Linux 7,6 oraz nowych wersji jądra dla Ubuntu, Debian i SUSE.


### <a name="update-rollup-31"></a>Pakiet zbiorczy aktualizacji 31

[Pakiet zbiorczy aktualizacji 31](https://support.microsoft.com/help/4478871/update-rollup-31-for-azure-site-recovery) udostępnia następujące aktualizacje.

**Aktualizowanie** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacja Site Recovery agentów i dostawców (zgodnie z opisem w pakiecie zbiorczym).
**Rozwiązywanie poprawek/ulepszeń** | Szereg poprawek i ulepszeń (zgodnie z opisem w pakiecie zbiorczym).

### <a name="vmware-vmsphysical-servers-replication"></a>Replikacja maszyn wirtualnych VMware/serwerów fizycznych

Funkcje dodane w tym miesiącu zostały podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Obsługa systemu Linux** |  Dodano pomoc techniczną dla Oracle Linux 6,8, Oracle Linux 6,9 i Oracle Linux 7,0 z jądrem zgodnym z systemem Red Hat oraz dla jądra w wersji 5 dla przedsiębiorstw (UEK).
**LVM** | Dodano obsługę dla woluminów LVM i LVM2.<br/><br/> Katalog/Boot na partycji dysku i na woluminach LVM jest teraz obsługiwany.
**Katalogu** | Dodano obsługę tych katalogów jako osobne partycje lub systemy plików, które nie znajdują się na tym samym dysku systemowym:<br/><br/> /(root),/Boot,/usr,/usr/local,/var,/etc.
**Windows Server 2008** | Dodano obsługę dla dysków dynamicznych.
**Tryb failover** | Ulepszony czas pracy w trybie failover dla maszyn wirtualnych VMware, gdzie storvsc i vsbus nie są sterownikami rozruchowymi
**Obsługa interfejsu UEFI** | Maszyny wirtualne platformy Azure nie obsługują typu rozruchu UEFI. Teraz można migrować lokalne serwery fizyczne z interfejsem UEFI do platformy Azure za pomocą Site Recovery. Site Recovery migruje serwer, konwertując typ rozruchu na system BIOS przed migracją. Site Recovery poprzednio obsługiwać tę konwersję tylko dla maszyn wirtualnych. Pomoc techniczna jest dostępna dla serwerów fizycznych z systemem Windows Server 2012 lub nowszym.

### <a name="azure-vm-disaster-recovery"></a>Odzyskiwanie po awarii maszyny wirtualnej platformy Azure

Funkcje dodane w tym miesiącu zostały podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Obsługa systemu Linux** | Dodano pomoc techniczną dla Oracle Linux 6,8, Oracle Linux 6,9 i Oracle Linux 7,0 z jądrem zgodnym z systemem Red Hat oraz dla jądra w wersji 5 dla przedsiębiorstw (UEK).
**System plików BRTFS systemu Linux** | Obsługiwane przez maszyny wirtualne platformy Azure.
**Maszyny wirtualne platformy Azure w strefach dostępności** | Replikację można włączyć w innym regionie dla maszyn wirtualnych platformy Azure wdrożonych w strefach dostępności. Teraz można włączyć replikację dla maszyny wirtualnej platformy Azure i ustawić docelowy tryb failover na jedno wystąpienie maszyny wirtualnej, maszynę wirtualną w zestawie dostępności lub maszynę wirtualną w strefie dostępności. To ustawienie nie ma wpływu na replikację. [Przeczytaj](https://azure.microsoft.com/blog/disaster-recovery-of-zone-pinned-azure-virtual-machines-to-another-region/) ogłoszenie.
**Magazyn z obsługą zapory (Portal/program PowerShell)** | Dodano obsługę [kont magazynu obsługujących zaporę](../storage/common/storage-network-security.md).<br/><br/> Maszyny wirtualne platformy Azure z dyskami niezarządzanymi można replikować na konta magazynu z obsługą zapory w innym regionie świadczenia usługi Azure na potrzeby odzyskiwania po awarii.<br/><br/> Kont magazynu z obsługą zapory można używać jako docelowych kont magazynu dla dysków niezarządzanych.<br/><br/> Obsługiwane w portalu i przy użyciu programu PowerShell.

## <a name="updates-december-2018"></a>Aktualizacje (Grudzień 2018)

### <a name="automatic-updates-for-the-mobility-service-azure-vms"></a>Aktualizacje automatyczne dla usługi mobilności (maszyny wirtualne platformy Azure)

Site Recovery dodano opcję aktualizacji automatycznych do rozszerzenia usługi mobilności. Rozszerzenie usługi mobilności jest instalowane na każdej maszynie wirtualnej platformy Azure zreplikowanej przez Site Recovery. Po włączeniu replikacji należy określić, czy zezwolić Site Recovery na zarządzanie aktualizacjami rozszerzenia.

Aktualizacje nie wymagają ponownego uruchomienia maszyny wirtualnej i nie mają wpływu na replikację. [Dowiedz się więcej](azure-to-azure-autoupdate.md).

### <a name="pricing-calculator-for-azure-vm-disaster-recovery"></a>Kalkulator cen odzyskiwania po awarii maszyny wirtualnej platformy Azure

Odzyskiwanie po awarii maszyn wirtualnych platformy Azure wiąże się z kosztami licencjonowania maszyny wirtualnej oraz kosztami sieci i magazynowania. Platforma Azure oferuje [Kalkulator cen](https://aka.ms/a2a-cost-estimator) , który pomoże Ci ustalić te koszty. Site Recovery teraz zawiera [przykładowe oszacowanie cen](https://aka.ms/a2a-cost-estimator) , które stanowią cenę przykładowego wdrożenia opartego na aplikacji trójwarstwowej korzystającej z sześciu maszyn wirtualnych z 12 dyskami HDD w warstwie Standardowa i 6 dyskami SSD w warstwie Premium.

- Przykład zakłada zmianę danych o 10 GB dziennie dla warstwy Standardowa i 20 GB dla warstwy Premium.
- W przypadku konkretnego wdrożenia można zmienić zmienne w celu oszacowania kosztów.
- Można określić liczbę maszyn wirtualnych, liczbę i typ dysków zarządzanych oraz oczekiwaną łączną częstotliwość zmian danych na maszynach wirtualnych.
- Ponadto można zastosować współczynnik kompresji do oszacowania kosztów przepustowości.

[Przeczytaj](https://azure.microsoft.com/blog/know-exactly-how-much-it-will-cost-for-enabling-dr-to-your-azure-vm/) ogłoszenie.


## <a name="updates-october-2018"></a>Aktualizacje (październik 2018)

### <a name="update-rollup-30"></a>Pakiet zbiorczy aktualizacji 30

[Pakiet zbiorczy aktualizacji 30](https://support.microsoft.com/help/4468181/azure-site-recovery-update-rollup-30) udostępnia następujące aktualizacje.

**Aktualizowanie** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacja Site Recovery agentów i dostawców (zgodnie z opisem w pakiecie zbiorczym).
**Rozwiązywanie poprawek/ulepszeń** | Szereg poprawek i ulepszeń (zgodnie z opisem w pakiecie zbiorczym).

### <a name="azure-vm-disaster-recovery"></a>Odzyskiwanie po awarii maszyny wirtualnej platformy Azure
Funkcje dodane w tym miesiącu zostały podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Obsługa regionów** | Dodano Site Recovery wsparcia dla Australii środkowej 1 i Australii środkowej 2.
**Obsługa szyfrowania dysków** | Dodano obsługę odzyskiwania po awarii maszyn wirtualnych platformy Azure zaszyfrowanych przy użyciu Azure Disk Encryption (ADE) za pomocą aplikacji usługi Azure AD. [Dowiedz się więcej](azure-to-azure-how-to-enable-replication-ade-vms.md).
**Wykluczanie dysku** | Niezainicjowane dyski są teraz automatycznie wykluczane podczas replikacji maszyny wirtualnej platformy Azure.
**Magazyn z obsługą zapory (PowerShell)** | Dodano obsługę [kont magazynu obsługujących zaporę](../storage/common/storage-network-security.md).<br/><br/> Maszyny wirtualne platformy Azure z dyskami niezarządzanymi można replikować na konta magazynu z obsługą zapory w innym regionie świadczenia usługi Azure na potrzeby odzyskiwania po awarii.<br/><br/> Kont magazynu z obsługą zapory można używać jako docelowych kont magazynu dla dysków niezarządzanych.<br/><br/> Obsługiwane tylko przy użyciu programu PowerShell.


### <a name="update-rollup-29"></a>Pakiet zbiorczy aktualizacji 29

[Pakiet zbiorczy aktualizacji 29](https://support.microsoft.com/help/4466466/update-rollup-29-for-azure-site-recovery) udostępnia następujące aktualizacje.

**Aktualizowanie** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacja Site Recovery agentów i dostawców (zgodnie z opisem w pakiecie zbiorczym).
**Rozwiązywanie poprawek/ulepszeń** | Szereg poprawek i ulepszeń (zgodnie z opisem w pakiecie zbiorczym).


## <a name="updates-august-2018"></a>Aktualizacje (sierpień 2018)

### <a name="update-rollup-28"></a>Pakiet zbiorczy aktualizacji 28

[Pakiet zbiorczy aktualizacji 28](https://support.microsoft.com/help/4460079/update-rollup-28-for-azure-site-recovery) zawiera następujące aktualizacje.

**Aktualizowanie** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacja Site Recovery agentów i dostawców (zgodnie z opisem w pakiecie zbiorczym).
**Rozwiązywanie poprawek/ulepszeń** | Szereg poprawek i ulepszeń (zgodnie z opisem w pakiecie zbiorczym).

### <a name="azure-vm-disaster-recovery"></a>Odzyskiwanie po awarii maszyny wirtualnej platformy Azure
Funkcje dodane w tym miesiącu zostały podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Obsługa systemu Linux** | Dodano obsługiwane dla RedHat Enterprise Linux 6,10; CentOS 6,10.<br/><br/>
**Obsługa chmury** | Obsługiwane odzyskiwanie po awarii dla maszyn wirtualnych platformy Azure w chmurze w Niemczech.
**Odzyskiwanie po awarii między subskrypcjami** | Obsługa replikowania maszyn wirtualnych platformy Azure w jednym regionie do innego regionu w ramach innej subskrypcji w ramach tej samej dzierżawy Azure Active Directory. [Dowiedz się więcej](https://aka.ms/cross-sub-blog).

### <a name="vmware-vmphysical-server-disaster-recovery"></a>Odzyskiwanie po awarii maszyn wirtualnych VMware/serwerów fizycznych
Funkcje dodane w tym miesiącu zostały podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Obsługa systemu Linux** | Dodano obsługę do RedHat Enterprise Linux 6,10, CentOS 6,10.<br/><br/> Maszyny wirtualne oparte na systemie Linux, które używają stylu partycji tabeli partycji GUID (GPT) w trybie zgodności ze starszymi wersjami systemu BIOS, są teraz obsługiwane. Aby uzyskać więcej informacji, przejrzyj [często zadawane pytania dotyczące maszyn wirtualnych platformy Azure](../virtual-machines/faq-for-disks.md) .
**Odzyskiwanie po awarii dla maszyn wirtualnych po migracji** | Obsługa włączenia odzyskiwania po awarii do regionu pomocniczego dla lokalnej maszyny wirtualnej VMware migrowanej do platformy Azure bez konieczności odinstalowywania usługi mobilności na maszynie wirtualnej przed włączeniem replikacji.
**Windows Server 2008** | Obsługa migracji maszyn z systemem Windows Server 2008 R2/2008 64-bit i 32-bit.<br/><br/> Tylko migracja (replikacja i tryb failover). Powrót po awarii nie jest obsługiwany.

## <a name="updates-july-2018"></a>Aktualizacje (lipiec 2018)

### <a name="update-rollup-27-july-2018"></a>Pakiet zbiorczy aktualizacji 27 (lipiec 2018)

[Pakiet zbiorczy aktualizacji 27](https://support.microsoft.com/help/4055712/update-rollup-27-for-azure-site-recovery) udostępnia następujące aktualizacje.

**Aktualizowanie** | **Szczegóły**
--- | ---
**Dostawcy i agenci** | Aktualizacja Site Recovery agentów i dostawców (zgodnie z opisem w pakiecie zbiorczym).
**Rozwiązywanie poprawek/ulepszeń** | Szereg poprawek i ulepszeń (zgodnie z opisem w pakiecie zbiorczym).

### <a name="azure-vm-disaster-recovery"></a>Odzyskiwanie po awarii maszyny wirtualnej platformy Azure

Funkcje dodane w tym miesiącu zostały podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Obsługa systemu Linux** | Dodano obsługę dla Red Hat Enterprise Linux 7,5.

### <a name="vmware-vmphysical-server-disaster-recovery"></a>Odzyskiwanie po awarii maszyn wirtualnych VMware/serwerów fizycznych

Funkcje dodane w tym miesiącu zostały podsumowane w tabeli.

**Funkcja** | **Szczegóły**
--- | ---
**Obsługa systemu Linux** | Dodano obsługę Red Hat Enterprise Linux 7,5, SUSE Linux Enterprise Server 12.



## <a name="next-steps"></a>Następne kroki

Bądź na bieżąco z naszymi aktualizacjami na stronie [aktualizacji platformy Azure](https://azure.microsoft.com/updates/?product=site-recovery) .