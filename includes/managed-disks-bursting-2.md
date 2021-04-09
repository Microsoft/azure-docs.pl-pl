---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 03/04/2021
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 3035b5d2803ff91e84bc6b47a99963185f9195d3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102623505"
---
## <a name="disk-level-bursting"></a>Szeregowanie na poziomie dysku

### <a name="on-demand-bursting-preview"></a>Szeregowanie na żądanie (wersja zapoznawcza)

Dyski korzystające z modelu serii na żądanie na potrzeby zadań związanych z przetwarzaniem dysków mogą przekroczyć pierwotne zarezerwowane elementy docelowe, tak często, jak to konieczne, przy użyciu obciążenia, do maksymalnego miejsca docelowego. Na przykład na dysku 1 TiB P30 liczba operacji we/wy na sekundę jest 5000 IOPS. Gdy dysk jest włączony na tym dysku, obciążenia mogą wydać system IOs na tym dysku do maksymalnej wydajności serii 30 000 operacji we/wy na sekundę i 1 000 MB/s.

Jeśli spodziewasz się, że obciążenia są często uruchamiane poza miejscem docelowym wydajności, przetwarzanie dysków nie będzie ekonomiczne. W takim przypadku zalecamy zmianę warstwy wydajności dysku na [wyższą warstwę](../articles/virtual-machines/disks-performance-tiers.md) , aby uzyskać lepszą wydajność bazową. Przejrzyj szczegóły dotyczące rozliczeń i Oceń je pod kątem wzorca ruchu obciążeń.

Przed włączeniem serii na żądanie należy zrozumieć następujące kwestie:

[!INCLUDE [managed-disk-bursting-regions-limitations](managed-disk-bursting-regions-limitations.md)]

#### <a name="regional-availability"></a>Dostępność w regionach

[!INCLUDE [managed-disk-bursting-availability](managed-disk-bursting-availability.md)]

#### <a name="billing"></a>Rozliczenia

Dyski korzystające z modelu przetwarzania na żądanie są obciążane naliczaną godzinową opłatą za włączenie, a koszty transakcji mają zastosowanie do wszystkich transakcji serii poza zainicjowanym miejscem docelowym. Koszty transakcji są naliczone przy użyciu modelu "płatność zgodnie z rzeczywistym użyciem" w oparciu o dysk bez pamięci podręcznej, w tym operacje odczytu i zapisu, które przekraczają zainicjowane obiekty docelowe. Poniżej przedstawiono przykład wzorców ruchu dyskowego w ciągu godziny rozliczeniowej:

Konfiguracja dysku: SSD w warstwie Premium – 1 TiB (P30), włączono obsługę serii dysków.

- 00:00:00 – 00:10:00 liczba operacji we/wy na sekundę z zainicjowaną obsługą operacji dla 5 000 IOPS 
- 00:10:01 – 00:10:10 Aplikacja wystawiła zadanie usługi Batch, które powoduje, że liczba operacji we/wy na sekundę na sekundę 6 000 w ciągu 10 sekund 
- 00:10:11 – 00:59:00 liczba operacji we/wy na sekundę z zainicjowaną obsługą operacji dla 5 000 IOPS 
- 00:59:01 – 01:00:00 aplikacja wydała kolejne zadanie usługi Batch, które powoduje, że liczba operacji we/wy na sekundę na sekundę w ciągu 60 7 000 

W tej godzinie rozliczeniowej koszt tworzenia serii obejmuje dwie opłaty:

Pierwsza opłata to płaska opłata z tytułu włączenia do $X (określona przez region). Ta płaska opłata jest zawsze naliczana za pominięcie stanu dołączania na dysku, dopóki nie zostanie wyłączona. 

Sekundę to koszt transakcji serii. Nastąpiło przekroczenie limitu czasu na dysku. Od 00:10:01 – 00:10:10 skumulowana transakcja serii to (6 000 – 5 000) X 10 = 10 000. Od 00:59:01 – 01:00:00 skumulowana transakcja seryjna to (7 000 – 5 000) X 60 = 120 000. Łączna liczba transakcji serii to 10 000 + 120 000 = 130 000. Koszt transakcji transobciążeniowych będzie naliczany na $Y w oparciu o 13 jednostek transakcji 10 000 (na podstawie cen regionalnych).

Dzięki temu łączny koszt dysku w tej godzinie rozliczeniowej jest równy $X i $Y. To samo obliczenie będzie miało zastosowanie do rozliczania za pośrednictwem zainicjowanego miejsca docelowego MB/s. W przypadku transakcji o rozmiarze 256 KB można przetłumaczyć nadwyżkę MB. Jeśli ruch dysku przekroczy zarówno przyinicjowaną liczbę operacji we/wy i MB/s, możesz zajrzeć do poniższego przykładu, aby obliczyć transakcję serii. 

Konfiguracja dysku: SSD w warstwie Premium – 1 TB (P30), włączono obsługę serii dysków.

- 00:00:01 – 00:00:05 aplikacja wystawiła zadanie usługi Batch, które powoduje, że liczba operacji we/wy na sekundę na sekundę 300 i 10 000 szybkość operacji we/wy na pięć sekund.
- 00:00:06 – 00:00:10 Aplikacja wygenerowała zadanie odzyskiwania, które powoduje, że liczba operacji we/wy na sekundę na sekundę 600 i 6 000 szybkość MB/s przez pięć sekund.

Transakcja serii jest uwzględniana jako maksymalna liczba transakcji z serii IOPS lub MB/s. Od 00:00:01 – 00:00:05 skumulowana transakcja serii jest maksymalna ((10 000 – 5 000), (300-200) * 1024/256)) * 5 = 25 000 Transactions. Od 00:00:06 – 00:00:10 skumulowana transakcja serii jest maksymalna ((6 000 – 5 000), (600-200) * 1024/256)) * 5 = 8 000 Transactions. W tym celu należy uwzględnić płaską opłatę za włączenie serii, aby uzyskać łączny koszt włączania obciążeń dysków na żądanie. 

Aby uzyskać szczegółowe informacje na temat cen i użyć [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/?service=storage) do oceny obciążenia, możesz zapoznać się ze [stroną cennika Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/) . 

### <a name="credit-based-bursting"></a>Korzystanie z rozliczeń opartych na kredycie

Korzystanie z obciążeń opartych na kredytach jest dostępne na dyskach P20 i mniejszych we wszystkich regionach w chmurach publicznych, rządowych i chińskich platformy Azure. Domyślnie funkcja przenoszenia dysków jest włączona we wszystkich nowych i istniejących wdrożeniach obsługiwanych rozmiarów dysków. W przypadku korzystania z rozliczeń na poziomie maszyny wirtualnej odbywa się tylko rozliczanie na podstawie kredytu.

## <a name="virtual-machine-level-bursting"></a>Szeregowanie na poziomie maszyny wirtualnej

Funkcja wielowymiarowego na poziomie maszyny wirtualnej używa modelu opartego na kredycie na potrzeby rozliczania, jest domyślnie włączona dla wszystkich maszyn wirtualnych, które je obsługują.

Funkcja wielowymiarowego na poziomie maszyny wirtualnej jest włączona we wszystkich regionach w chmurze publicznej platformy Azure pod kątem obsługiwanych rozmiarów: 
- [Seria Lsv2](../articles/virtual-machines/lsv2-series.md)
- [Serie Dv3 i Dsv3](../articles/virtual-machines/dv3-dsv3-series.md)
- [Serie Ev3 i Esv3](../articles/virtual-machines/ev3-esv3-series.md)

## <a name="bursting-flow"></a>Przepływ na rozerwanie

System środków na korzystanie z rozliczeń ma zastosowanie w taki sam sposób na poziomie maszyny wirtualnej i na poziomie dysku. Zasób, maszyna wirtualna lub dysk, rozpocznie się z pełnymi środkami do magazynowania w osobnym zasobniku. Te Kredyty umożliwiają przeliczenie na maksymalnie 30 minut przy maksymalnej szybkości serii. Środki są sumowane za każdym razem, gdy liczba operacji we/wy zasobu jest używana poniżej wartości docelowej wydajności zasobu. Jeśli zasób ma naliczone środki na rozliczanie, a obciążenie wymaga dodatkowej wydajności, zasób może użyć tych kredytów, aby przekroczyć jego limity wydajności i zwiększyć jego wydajność w celu spełnienia wymagań dotyczących obciążenia.

![Diagram zasobników serii.](media/managed-disks-bursting/bucket-diagram.jpg)

Jak spędzać Twoje dostępne środki. W ciągu dnia możesz użyć 30-minutowych kredytów na korzystanie z serii. Po wdrożeniu zasobów są one w pełni alokowane. Kiedy te wyczerpywają się, zajmują mniej niż dzień, aby można było go rozprowadzić. Kredyty mogą być realizowane według własnego uznania, ale nie musi być pełny, aby można było uzyskać dostęp do zasobów na rozerwanie. Akumulacja seryjna zależy od poszczególnych zasobów, ponieważ jest ona oparta na nieużywanych operacjach IOPS i MB/s poniżej ich docelowych wydajności. Wyższe zasoby dotyczące wydajności linii bazowej mogą naliczać kredyty na rozerwanie szybciej niż niższe zasoby bazowe. Na przykład dysk P1 z biegu jałowego naliczy 120 operacji we/wy na sekundę, podczas gdy dysk o P20 biegu jałowego przyniesie 2 300 operacji we/wy na sekundę.

## <a name="bursting-states"></a>Stany rozrywające
Istnieją trzy stany, w których zasób może być używany z włączonym rozruchem:
- **Naliczanie** — ruch we/wy zasobu jest używany mniej niż obiekt docelowy wydajności. Sumowanie kredytów dla operacji we/wy na sekundę i MB/s odbywa się niezależnie od siebie. W Twoim zasobie można naliczać kredyty na korzystanie z operacji we/wy na sekundę, a także na odwrót.
- **Rozbicie — ruch** zasobów jest używany więcej niż w celu wydajności. Ruch z serii będzie niezależnie zużywał kredyty z liczby operacji we/wy lub przepustowości.
- **Stała** — ruch zasobu jest dokładnie w miejscu docelowym wydajności.

## <a name="bursting-examples"></a>Przykłady dotyczące rozszeregowania

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

![Maszyna wirtualna wysyła żądanie o 192 MB/s do dysku systemu operacyjnego, dysk systemu operacyjnego reaguje na dane 170 MB/s.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-startup.jpg)

Po zakończeniu rozruchu aplikacja jest uruchamiana na maszynie wirtualnej i ma obciążenie niekrytyczne. To obciążenie wymaga 15 MB/S, które jest równomiernie rozłożone na wszystkich dyskach.

![Aplikacja wysyła żądanie o 15 MB/s do maszyny wirtualnej, maszyna wirtualna przyjmuje żądanie i wysyła do każdego z nich żądanie dotyczące 5 MB/s, a każdy dysk zwraca 5 MB/s, maszyna wirtualna zwraca 15 MB/s do aplikacji.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-idling.jpg)

Następnie aplikacja musi przetworzyć zadanie wsadowe wymagające 192 MB/s. dysk systemu operacyjnego jest używany 2 MB/s, a reszta jest równomiernie dzielona między dyskami danych.

![Aplikacja wysyła żądanie dotyczące 192 MB/s przepływności do maszyny wirtualnej, maszyna wirtualna żąda i wysyła zbiorczo żądanie do dysków danych (95 MB/s każdy) i 2 MB/s do dysku systemu operacyjnego, seria dysków danych w celu spełnienia wymagań i wszystkich dysków zwróci żądaną przepływność do maszyny wirtualnej, która zwraca ją do aplikacji.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-bursting.jpg)

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

![Aplikacja wysyła żądanie dotyczące 15 MB/s przepływności do maszyny wirtualnej, maszyna wirtualna przyjmuje żądanie i wysyła do każdego z nich żądanie dotyczące 5 MB/s, a każdy dysk zwraca 5 MB/s odpowiedzi, maszyna wirtualna zwraca 15 MB/s do aplikacji.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-idling.jpg)

Następnie aplikacja musi przetworzyć zadanie wsadowe wymagające 360 MB/s. Standard_L8s_v2 serii, aby spełnić te zapotrzebowanie, a następnie żądania. Dysk systemu operacyjnego wymaga tylko 20 MB/s. Pozostałe 340 MB/s są obsługiwane przez dyski danych P4.

![Aplikacja wysyła żądanie o 360 MB/s do maszyny wirtualnej, maszyna wirtualna zajmuje się seriami, aby przetworzyć żądanie i wysyła do każdego z nich dyski danych żądanie o 170 MB/s i 20 MB/s z dysku systemu operacyjnego, każdy dysk zwróci żądane MB/s, a następnie ponownie przy360 wróci do aplikacji.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-bursting.jpg)
