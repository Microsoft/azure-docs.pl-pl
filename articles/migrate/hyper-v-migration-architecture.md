---
title: Jak działa Migracja funkcji Hyper-V w Azure Migrate?
description: Informacje o migracji funkcji Hyper-V za pomocą Azure Migrate
author: bsiva
ms.author: bsiva
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: 239918cc19eefbef9e3c3f12d5ddd3bb5434b490
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96751023"
---
# <a name="how-does-hyper-v-replication-work"></a>Jak działa replikacja funkcji Hyper-V?

Ten artykuł zawiera omówienie architektury i procesów używanych podczas migrowania maszyn wirtualnych funkcji Hyper-V za pomocą narzędzia migracji Azure Migrate Server.

[Azure Migrate](migrate-services-overview.md) udostępnia centralne centrum do śledzenia odnajdywania, oceny i migracji lokalnych aplikacji i obciążeń oraz maszyn wirtualnych chmur prywatnych/publicznych na platformie Azure. Centrum udostępnia Azure Migrate narzędzia do oceny i migracji, a także oferty niezależnych dostawców oprogramowania (ISV) innych firm.

## <a name="agentless-migration"></a>Migracja bez wykorzystania agentów

Narzędzie do migracji Azure Migrate Server zapewnia replikację bezagentową dla lokalnych maszyn wirtualnych funkcji Hyper-V przy użyciu przepływu pracy migracji zoptymalizowanego pod kątem funkcji Hyper-V. Agenta oprogramowania można zainstalować tylko na hostach funkcji Hyper-V lub w węzłach klastra. Niczego nie trzeba instalować na maszynach wirtualnych funkcji Hyper-V.

## <a name="server-migration-and-azure-site-recovery"></a>Migracja serwera i Azure Site Recovery

Migracja serwera Azure Migrate to narzędzie służące do migrowania obciążeń lokalnych oraz maszyn wirtualnych opartych na chmurze na platformie Azure. Site Recovery to narzędzie odzyskiwania po awarii. Narzędzia te udostępniają niektóre popularne składniki technologiczne używane do replikacji danych, ale służą do różnych celów. 


## <a name="architectural-components"></a>Składniki architektury

![Diagram przedstawia źródłową sieć funkcji Hyper-V z kanałem danych H T t S do Microsoft Azure, ze szczegółami wyjaśnionymi w tabeli.](./media/hyper-v-replication-architecture/architecture.png)



**Składnik** | **Wdrożenie** | 
--- | --- 
**Dostawca replikacji** | Dostawca Site Recovery Microsoft Azure jest instalowany na hostach funkcji Hyper-V i zarejestrowano w ramach migracji serwera migracji platformy Azure.<br/> Dostawca organizuje replikację maszyn wirtualnych funkcji Hyper-V.
**Agent Recovery Services** | Agent usługi odzyskiwania Microsoft Azure obsługuje replikację danych. Współpracuje z dostawcą w celu replikowania danych z maszyn wirtualnych funkcji Hyper-V do platformy Azure.<br/> Zreplikowane dane są przekazywane do konta magazynu w ramach subskrypcji platformy Azure. Narzędzie do migracji serwera przetwarza zreplikowane dane i stosuje je do dysków repliki w ramach subskrypcji. Dyski repliki są używane do tworzenia maszyn wirtualnych platformy Azure podczas migracji.

- Składniki są instalowane za pomocą pojedynczego pliku instalacyjnego pobrane z migracji Azure Migrate serwera w portalu.
- Dostawca i urządzenie używają wychodzących połączeń HTTPS port 443 do komunikowania się z migracją serwera Azure Migrate.
- Komunikacja od dostawcy i agenta jest bezpieczna i szyfrowana.


## <a name="replication-process"></a>Proces replikacji

1. Po włączeniu replikacji dla maszyny wirtualnej funkcji Hyper-V rozpoczyna się replikacja początkowa.
2. Wykonywana jest migawka maszyny wirtualnej funkcji Hyper-V.
3. Wirtualne dyski twarde w maszynie wirtualnej są replikowane jeden do nich, dopóki nie zostaną skopiowane na platformę Azure. Czas replikacji początkowej zależy od rozmiaru maszyny wirtualnej i przepustowości sieci.
4. Zmiany dysku występujące podczas replikacji początkowej są śledzone przy użyciu funkcji Hyper-V Replica i przechowywane w plikach dziennika (pliki HRL).
    - Pliki dziennika znajdują się w tym samym folderze co dyski.
    - Każdy dysk ma skojarzony plik HRL, który jest wysyłany do magazynu pomocniczego.
    - Pliki migawki i dziennika zużywają zasoby dysku w trakcie replikacji początkowej.
4. Po zakończeniu replikacji początkowej migawka maszyny wirtualnej zostanie usunięta i rozpocznie się replikacja różnicowa.
5. Przyrostowe zmiany dysku są śledzone w plikach HRL. Dzienniki replikacji są okresowo przekazywane do konta usługi Azure Storage przez agenta Recovery Services.


## <a name="performance-and-scaling"></a>Wydajność i skalowanie

Wydajność replikacji dla funkcji Hyper-V ma wpływ na czynniki, które obejmują rozmiar maszyny wirtualnej, współczynnik zmian danych (zmiany) maszyn wirtualnych, dostępne miejsce na hoście funkcji Hyper-V do przechowywania plików dziennika, przepustowość przekazywania danych replikacji i magazyn docelowy na platformie Azure.

- W przypadku replikowania wielu maszyn w tym samym czasie Użyj [planista wdrażania usługi Azure Site Recovery](../site-recovery/hyper-v-deployment-planner-overview.md) funkcji Hyper-V, aby pomóc zoptymalizować replikację.
- Zaplanuj replikację funkcji Hyper-V i Dystrybuuj replikację za pośrednictwem kont usługi Azure Storage, zgodnie z pojemnością.

### <a name="control-upload-throughput"></a>Przepływność przekazywania kontroli

Można ograniczyć przepustowość używaną do przekazywania danych do platformy Azure na każdym hoście funkcji Hyper-V. Ostrożnie. Jeśli ustawisz zbyt niską wartość, będzie to mieć negatywny wpływ na replikację i Opóźnij migrację.


1. Zaloguj się do hosta lub węzła klastra funkcji Hyper-V.
2. Uruchom polecenie **C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.msc**, aby otworzyć przystawkę MMC Azure Backup systemu Windows.
3. W przystawce wybierz pozycję **Zmień właściwości**.
4. W obszarze **ograniczenie przepustowości** wybierz opcję **Włącz ograniczenie przepustowości Internetu dla operacji tworzenia kopii zapasowej**. Ustaw limity dla godzin pracy i czasu wolnego. Prawidłowe zakresy są z zakresu od 512 KB/s do 1 023 MB/s.
I

### <a name="influence-upload-efficiency"></a>Wpływ na wydajność przekazywania

Jeśli masz zapasową przepustowość na potrzeby replikacji, a chcesz zwiększyć liczbę operacji przekazywania, możesz zwiększyć ilość wątków przydzieloną dla zadania przekazania w następujący sposób:

1. Otwórz Rejestr przy użyciu programu regedit.
2. Przejdź do klucza HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM
3. Zwiększ wartość liczby wątków używanych do przekazywania danych dla każdej replikowanej maszyny wirtualnej. Wartość domyślna to 4, a wartość maksymalna to 32. 




## <a name="next-steps"></a>Następne kroki

Wypróbowanie [migracji funkcji Hyper-V](tutorial-migrate-hyper-v.md) za pomocą migracji serwera Azure Migrate.
