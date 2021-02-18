---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 04/27/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 801f0f03b49d20c84a4531bd0daad7630a0ed01d
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585091"
---
## <a name="common-scenarios"></a>Typowe scenariusze
Poniższe scenariusze mogą znacznie wzczerpać korzyści z rozszeregowania:
- Skrócenie **czasu rozruchu** — dzięki rozbiciem wystąpienie zostanie uruchomione w znacznie szybszym tempie. Na przykład domyślnym dyskiem systemu operacyjnego dla maszyn wirtualnych z obsługą Premium jest dysk P4, który zapewnia wydajność o pojemności do 120 operacji we/wy i 25 MB/s. Dzięki rozruchom P4 można przystąpić do 3500 operacji we/wy i 170 MB/s, co umożliwia przyspieszenie czasu rozruchu przez 6X.
- **Obsługa zadań wsadowych** — pewne obciążenia aplikacji są cykliczne i wymagają wydajności linii bazowej przez większość czasu i wymagają większej wydajności przez krótki czas. Przykładem jest program księgowy, który codziennie przetwarza transakcje, które wymagają niewielkiego natężenia ruchu na dysku. Następnie na koniec miesiąca program wykonuje uzgadnianie raportów, które potrzebują znacznie większej ilości ruchu na dysku.
- **Przygotowanie do skoków ruchu** — serwery sieci Web i ich aplikacje mogą w dowolnym momencie powodować wzrosty ruchu. Jeśli serwer sieci Web jest obsługiwany przez maszyny wirtualne lub dyski korzystające z funkcji tworzenia serii, serwery są lepiej wyposażone w program obsługujący wzrosty ruchu. 

## <a name="bursting-flow"></a>Przepływ na rozerwanie
System przeciwstawny jest stosowany w taki sam sposób na poziomie maszyny wirtualnej i na poziomie dysku. Zasób, maszyna wirtualna lub dysk, rozpocznie się z pełnymi kredytami. Te kredyty umożliwią korzystanie z serii przez 30 minut przy maksymalnej szybkości serii. Kredyty na rozerwanie są gromadzone, gdy zasób jest uruchomiony w ramach limitów magazynowania na dysku wydajności. Dla wszystkich operacji we/wy i MB/s, których zasób jest używany, poniżej limitu wydajności rozpoczynającego się do sumowania kredytów. Jeśli zasób ma naliczane środki na korzystanie z obciążeń, a obciążenie wymaga dodatkowej wydajności, zasób może użyć tych kredytów do osiągnięcia wyższego poziomu wydajności w celu zapewnienia wydajności operacji we/wy dysku wymaganej do spełnienia wymagań.



![Diagram zasobników serii](media/managed-disks-bursting/bucket-diagram.jpg)

Jest to w pełni aktualne informacje o tym, jak chcesz korzystać z 30-minutowej serii. Można jej używać przez 30 minut po raz lub sporadycznie przez cały dzień. Gdy produkt zostanie wdrożony, jest gotowy z pełnymi środkami, a w przypadku wyczerpania kredytów trwa krócej niż dzień w celu ponownego zapełnienia środków w całości. Można zbierać i poświęcać swoje kredyty na swoje rozliczanie według własnego uznania, a przedział 30-minutowy nie musi być zapełniony ponownie w ramach serii. Jednym ze sposobów na zanotowanie akumulacji seryjnej jest to, że jest to różne dla każdego zasobu, ponieważ jest ona oparta na nieużywanych operacjach IOPS i MB/s poniżej ich wartości wydajności. Oznacza to, że wyższe produkty bazowe wydajności mogą naliczać swoje ilości na rozerwanie szybciej niż niższe produkty bazowe. Na przykład dysk P1 z biegu jałowego bez aktywności spowoduje naliczenie 120 operacji we/wy na sekundę, podczas gdy dysk P20 naliczy 2 300 operacji we/wy na sekundę podczas fazy biegu.

## <a name="bursting-states"></a>Stany rozrywające
Istnieją trzy stany, w których zasób może być używany z włączonym rozruchem:
- **Naliczanie** — ruch we/wy zasobu jest używany mniej niż obiekt docelowy wydajności. Sumowanie kredytów dla operacji we/wy na sekundę i MB/s odbywa się niezależnie od siebie. W Twoim zasobie można naliczać kredyty na korzystanie z operacji we/wy na sekundę, a także na odwrót.
- **Rozbicie — ruch** zasobów jest używany więcej niż w celu wydajności. Ruch z serii będzie niezależnie zużywał kredyty z liczby operacji we/wy lub przepustowości.
- **Stała** — ruch zasobu jest dokładnie w miejscu docelowym wydajności.

## <a name="examples-of-bursting"></a>Przykłady dotyczące przenoszenia

W poniższych przykładach pokazano, jak działa rozbicie przy użyciu różnych maszyn wirtualnych i kombinacji dysków. Aby ułatwić wykonywanie przykładów, należy skoncentrować się na MB/s, ale ta sama logika jest stosowana niezależnie dla operacji we/wy.

### <a name="non-burstable-virtual-machine-with-burstable-disks"></a>Maszyna wirtualna niewymienna z dyskami z możliwością przełożenia
**Kombinacja maszyn wirtualnych i dysków:** 
- Standard_D8as_v4 
    - MB pamięci podręcznej/s: 192
- Dysk systemu operacyjnego P4
    - Liczba zainicjowanych MB/s: 25
    - Maksymalna liczba MB/s: 170 
- 2 P10 dyski danych 
    - Liczba zainicjowanych MB/s: 100
    - Maksymalna liczba MB/s: 170

 Po uruchomieniu maszyny wirtualnej pobiera dane z dysku systemu operacyjnego. Ponieważ dysk systemu operacyjnego jest częścią maszyny wirtualnej, która jest uruchamiana, dysk systemu operacyjnego będzie pełen kredyty na rozerwanie. Te kredyty umożliwią ponowne uruchomienie dysku systemu operacyjnego o 170 MB/s sekund.

![Maszyna wirtualna wysyła żądanie o 192 MB/s do dysku systemu operacyjnego, dysk systemu operacyjnego reaguje na dane 170 MB/s.](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-startup.jpg)

Po zakończeniu rozruchu aplikacja jest uruchamiana na maszynie wirtualnej i ma obciążenie niekrytyczne. To obciążenie wymaga 15 MB/S, które jest równomiernie rozłożone na wszystkich dyskach.

![Aplikacja wysyła żądanie o 15 MB/s do maszyny wirtualnej, maszyna wirtualna przyjmuje żądanie i wysyła do każdego z nich żądanie dotyczące 5 MB/s, a każdy dysk zwraca 5 MB/s, maszyna wirtualna zwraca 15 MB/s do aplikacji.](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-idling.jpg)

Następnie aplikacja musi przetworzyć zadanie wsadowe wymagające 192 MB/s. dysk systemu operacyjnego jest używany 2 MB/s, a reszta jest równomiernie dzielona między dyskami danych.

![Aplikacja wysyła żądanie dotyczące 192 MB/s przepływności do maszyny wirtualnej, maszyna wirtualna żąda i wysyła zbiorczo żądanie do dysków danych (95 MB/s każdy) i 2 MB/s do dysku systemu operacyjnego, seria dysków danych w celu spełnienia wymagań i wszystkich dysków zwróci żądaną przepływność do maszyny wirtualnej, która zwraca ją do aplikacji.](media/managed-disks-bursting/nonbursting-vm-busting-disk/nonbusting-vm-bursting-disk-bursting.jpg)

### <a name="burstable-virtual-machine-with-non-burstable-disks"></a>Maszyna wirtualna do maszyn wirtualnych z dyskami niewymiennymi
**Kombinacja maszyn wirtualnych i dysków:** 
- Standard_L8s_v2 
    - MB pamięci podręcznej/s: 160
    - Maksymalna liczba MB/s: 1 280
- Dysk systemu operacyjnego P50
    - Liczba zainicjowanych MB/s: 250 
- 2 P10 dyski danych 
    - Liczba zainicjowanych MB/s: 250

 Po rozruchu początkowym aplikacja jest uruchamiana na maszynie wirtualnej i ma obciążenie niekrytyczne. To obciążenie wymaga 30 MB/s, które jest równomiernie rozłożone na wszystkich dyskach.
![Aplikacja wysyła żądanie o 30 MB/s na maszynę wirtualną, a następnie wysyła do każdego z nich żądanie o 10 MB/s, a każdy dysk zwróci 10 MB/s, maszyna wirtualna zwróci 30 MB/s do aplikacji.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-normal.jpg)

Następnie aplikacja musi przetworzyć zadanie wsadowe wymagające 600 MB/s. Standard_L8s_v2 serii, aby spełnić to żądanie, a następnie żądania do dysków można równomiernie rozłożyć na dyski P50.

![Aplikacja wysyła żądanie dotyczące 600 MB/s przepływności do maszyny wirtualnej, maszyna wirtualna zajmuje się seriami, aby przetworzyć żądanie i wysyła do każdego z nich dysk żądania dla 200 MB/s, a każdy dysk zwraca 200 MB/s, szeregi maszyn wirtualnych w celu 600 zwrócenia wydajności do aplikacji.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-bursting.jpg)
### <a name="burstable-virtual-machine-with-burstable-disks"></a>Maszyna wirtualna z możliwością przerywającą z dyskami z możliwością przenoszenia
**Kombinacja maszyn wirtualnych i dysków:** 
- Standard_L8s_v2 
    - MB pamięci podręcznej/s: 160
    - Maksymalna liczba MB/s: 1 280
- Dysk systemu operacyjnego P4
    - Liczba zainicjowanych MB/s: 25
    - Maksymalna liczba MB/s: 170 
- 2 P4 dyski danych 
    - Liczba zainicjowanych MB/s: 25
    - Maksymalna liczba MB/s: 170 

Po uruchomieniu maszyny wirtualnej będzie ona żądała przekroczenia limitu 1 280 MB/s z dysku systemu operacyjnego, a dysk systemu operacyjnego reaguje na wydajność z serii 170 MB/s.

![Podczas uruchamiania maszyny wirtualne mogą wysyłać żądania o 1 280 MB/s do dysku systemu operacyjnego, a dyski systemu operacyjnego zwracały 1 280 MB/s.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-startup.jpg)

Po uruchomieniu należy uruchomić aplikację, która ma niekrytyczne obciążenie. Ta aplikacja wymaga 15 MB/s, która umożliwia równomierne rozmieszczenie na wszystkich dyskach.

![Aplikacja wysyła żądanie o 15 MB/s do maszyny wirtualnej, maszyna wirtualna przyjmuje żądanie i wysyła do każdego z nich żądanie dotyczące 5 MB/s, a każdy dysk zwraca 5 MB/s, maszyna wirtualna zwraca 15 MB/s do aplikacji.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-idling.jpg)

Następnie aplikacja musi przetworzyć zadanie wsadowe wymagające 360 MB/s. Standard_L8s_v2 serii, aby spełnić te zapotrzebowanie, a następnie żądania. Dysk systemu operacyjnego wymaga tylko 20 MB/s. Pozostałe 340 MB/s są obsługiwane przez dyski danych P4.

![Aplikacja wysyła żądanie o 360 MB/s do maszyny wirtualnej, maszyna wirtualna zajmuje się seriami, aby przetworzyć żądanie i wysyła do każdego z nich dyski danych żądanie o 170 MB/s i 20 MB/s z dysku systemu operacyjnego, każdy dysk zwróci żądane MB/s, a następnie ponownie przy360 wróci do aplikacji.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-bursting.jpg)