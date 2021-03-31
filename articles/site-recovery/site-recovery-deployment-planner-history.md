---
title: Historia wersji Planista wdrażania usługi Azure Site Recovery
description: Znane różne Site Recovery wersje Planista wdrażania i znane ograniczenia wraz z ich datami wydania.
services: site-recovery
author: Daya-Patil
manager: carmonm
ms.topic: article
ms.service: site-recovery
ms.date: 6/4/2020
ms.author: dapatil
ms.openlocfilehash: feb4f6a24653aca8da825af90341c8016255e8b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86133808"
---
# <a name="azure-site-recovery-deployment-planner-version-history"></a>Historia wersji Planista wdrażania usługi Azure Site Recovery

W tym artykule przedstawiono historię wszystkich wersji Planista wdrażania usługi Azure Site Recovery wraz z poprawkami, znanymi ograniczeniami w każdej i ich datach wydania.

## <a name="version-252"></a>Wersja 2,52

**Data wydania: 4 czerwca 2020**

**Prefix**

- Dodano obsługę programu vCenter 7,0
- Dodano obsługę następujących systemów operacyjnych:

    - SUSE Linux Enterprise 15 (ze wszystkimi wersjami pomocniczymi)
    - Red Hat Enterprise Linux 8 (ze wszystkimi wersjami pomocniczymi)


## <a name="version-251"></a>Wersja 2,51

**Data wydania: 22 sierpnia 2019**

**Prefix**

- Rozwiązano problem z rekomendacją kosztów w Planista wdrażania wersja 2,5

## <a name="version-25"></a>Wersja 2,5

**Data wydania: 29 lipca 2019**

**Prefix**

- W przypadku maszyn wirtualnych VMware i maszyn fizycznych zalecenie jest aktualizowane w oparciu o replikację do Managed Disks.
- Dodano obsługę systemu Windows 10 (x64), Windows 8.1 (x64), Windows 8 (x64), Windows 7 (x64) z dodatkiem SP1 lub nowszym

## <a name="version-24"></a>Wersja 2,4

**Data wydania: 17 kwietnia 2019**

**Prefix**

- Ulepszona zgodność systemu operacyjnego, w przypadku obsługi błędów na podstawie lokalizacji.
- Dodano maszyny wirtualne z maksymalnie 20 MB/s szybkości zmian danych (zmiany) do listy kontrolnej zgodności.
- Ulepszono komunikaty o błędach
- Dodano obsługę programu vCenter 6,7.
- Dodano obsługę stacji roboczej z systemem Windows Server 2019 i Red Hat Enterprise Linux (RHEL).



## <a name="version-23"></a>Wersja 2,3

**Data wydania: 3 grudnia 2018**

**Prefix**

- Rozwiązano problem uniemożliwiający Planista wdrażania wygenerowania raportu z podaną lokalizacją docelową i subskrypcją.

## <a name="version-22"></a>Wersja 2,2 

**Data wydania: 25 kwietnia 2018**

**Prefix**

- Operacje GetVMList:
  - Rozwiązano problem, który spowodował niepowodzenie GetVMList, jeśli określony folder nie istnieje. Teraz tworzy katalog domyślny lub tworzy katalog określony w parametrze plik_wyjściowy.
  - Dodano bardziej szczegółowe przyczyny niepowodzenia dla GetVMList.
- Dodano informacje o typie maszyny wirtualnej jako kolumnę w arkuszu zgodnych maszyn wirtualnych raportu Planista wdrażania.
- Odzyskiwanie po awarii funkcji Hyper-V do platformy Azure:
  - Wykluczone maszyny wirtualne z udostępnionymi dyskami VHD i przekazywaniem dysków z profilowania. Operacja Startprofiling wyświetla listę wykluczonych maszyn wirtualnych w konsoli programu.
  - Dodano maszyny wirtualne z więcej niż 64 dyskami do listy niezgodnych maszyn wirtualnych.
  - Zaktualizowano współczynnik kompresji replikacji początkowej (IR) i replikacji różnicowej (DR).
  - Dodano ograniczoną obsługę magazynu SMB.

## <a name="version-21"></a>Wersja 2,1

**Data wydania: 3 stycznia 2018**

**Prefix**

- Zaktualizowano raport programu Excel.
- Rozwiązano błędy w operacji getprzepływności.
- Dodano opcję, aby ograniczyć liczbę maszyn wirtualnych do profilowania lub wygenerowania raportu. Domyślny limit to 1 000 maszyn wirtualnych.
- Odzyskiwanie po awarii programu VMware do platformy Azure:
  - Rozwiązano problem z maszyną wirtualną z systemem Windows Server 2016 do niezgodnej tabeli. 
  - Zaktualizowane komunikaty zgodności dla maszyn wirtualnych z systemem operacyjnym Windows (Extensible Firmware Interface EFI).
- Zaktualizowano oprogramowanie VMware na platformę Azure i funkcję Hyper-V do platformy Azure, ograniczenie liczby zmian danych maszyny wirtualnej na maszynę wirtualną. 
- Ulepszona niezawodność analizy pliku listy maszyn wirtualnych.

## <a name="version-201"></a>Wersja 2.0.1

**Data wydania: 7 grudnia 2017**

**Prefix**

- Dodano rekomendacje w celu zoptymalizowania przepustowości sieci.

## <a name="version-20"></a>Wersja 2,0

**Data wydania: 28 listopada 2017**

**Prefix**

- Dodano obsługę funkcji Hyper-V do odzyskiwania po awarii platformy Azure.
- Dodano Kalkulator kosztów.
- Dodano sprawdzenie wersji systemu operacyjnego dotyczącego odzyskiwania po awarii programu VMware do platformy Azure w celu ustalenia, czy maszyna wirtualna jest zgodna, czy niezgodna z ochroną. Narzędzie używa ciągu wersji systemu operacyjnego, który jest zwracany przez serwer vCenter dla tej maszyny wirtualnej. Jest to wersja systemu operacyjnego gościa wybrana przez użytkownika podczas tworzenia maszyny wirtualnej w oprogramowaniu VMware.

**Znane ograniczenia:**

- W przypadku odzyskiwania po awarii funkcji Hyper-V do platformy Azure maszyna wirtualna o nazwie zawierającej znaki takie jak: `,` , `"` ,, `[` `]` i ``` ` ``` nie jest obsługiwana. Jeśli profilowana, generowanie raportu zakończy się niepowodzeniem lub będzie mieć niepoprawny wynik.
- W przypadku odzyskiwania po awarii programu VMware do platformy Azure maszyna wirtualna o nazwie zawierającej przecinek nie jest obsługiwana. Po profilowaniu generowanie raportu kończy się niepowodzeniem lub będzie miało niepoprawny wynik.

## <a name="version-131"></a>Wersja 1.3.1

**Data wydania: 19 lipca 2017** 

**Prefix**

- Dodano obsługę dużych dysków (> 1 TB) podczas generowania raportu. Teraz można użyć Planista wdrażania do zaplanowania replikacji dla maszyn wirtualnych, które mają rozmiar dysku większy niż 1 TB (do 4095 GB).
Dowiedz się więcej na temat [obsługi dużych dysków w usłudze Azure Site Recovery](https://azure.microsoft.com/blog/azure-site-recovery-large-disks/)

## <a name="version-13"></a>Wersja 1,3

**Data wydania: 9 maja 2017**

**Prefix**

- Dodano obsługę dysku zarządzanego podczas generowania raportu. Liczba maszyn wirtualnych, które można umieścić na jednym koncie magazynu, jest obliczana na podstawie tego, czy dysk zarządzany jest wybrany do pracy w trybie failover/testu pracy w trybie failover.

## <a name="version-12"></a>Wersja 1,2

**Data wydania: 7 kwietnia 2017**

**Prefix**

- Dodano typ rozruchu (BIOS lub EFI) sprawdzanie dla każdej maszyny wirtualnej w celu ustalenia, czy maszyna wirtualna jest zgodna, czy niezgodna z ochroną.
- Dodano informacje o typie systemu operacyjnego dla każdej maszyny wirtualnej w arkuszach zgodnych maszyn wirtualnych i niezgodnych maszyn wirtualnych.
- Dodano obsługę operacji getprzepływności dla regionów rządowych Stanów Zjednoczonych i Chin Microsoft Azure.
- Dodano kilka testów wymagań wstępnych dla serwerów vCenter i ESXi.
- Rozwiązano problem dotyczący błędnego raportu, który jest generowany, gdy ustawienia regionalne są ustawione na inne niż angielski.

## <a name="version-11"></a>Wersja 1,1

**Data wydania: 9 marca 2017**

**Prefix**

- Rozwiązano problem, który uniemożliwił maszynom wirtualnym profilowania, gdy co najmniej dwie maszyny wirtualne o tej samej nazwie lub adresie IP znajdują się na różnych hostach vCenter ESXi.
- Rozwiązano problem, który spowodował wyłączenie kopiowania i wyszukiwania dla zgodnych maszyn wirtualnych i arkuszy maszyn wirtualnych niezgodnych.

## <a name="version-10"></a>Wersja 1,0

**Data wydania: 23 lutego 2017**

**Znane ograniczenia:**

- Obsługuje tylko scenariusze odzyskiwania po awarii programu VMware do platformy Azure. W przypadku scenariuszy odzyskiwania po awarii funkcji Hyper-V na platformie Azure Użyj [Narzędzia planisty wydajności funkcji Hyper-v](./hyper-v-deployment-planner-overview.md).
- Nie obsługuje operacji getprzepływności dla rządów USA i Chin Microsoft Azure regionów.
- Narzędzie cann't profiluje maszyny wirtualne, jeśli serwer vCenter ma co najmniej dwie maszyny wirtualne o tej samej nazwie lub adresie IP na różnych hostach ESXi.
W tej wersji narzędzie pomija profilowanie w przypadku zduplikowanych nazw lub adresów IP w parametrze VMListFile. Obejście polega na profilowaniu maszyn wirtualnych przy użyciu hosta ESXi zamiast serwera vCenter. Upewnij się, że uruchomiono jedno wystąpienie dla każdego hosta ESXi.
