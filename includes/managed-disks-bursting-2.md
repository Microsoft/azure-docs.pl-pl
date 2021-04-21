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
ms.openlocfilehash: 4162fe12ff54f16cd5f982f6a576905227c9a107
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107821050"
---
## <a name="disk-level-bursting"></a>Bursting (Bursting) na poziomie dysku

### <a name="on-demand-bursting-preview"></a>Bursting on-demand (wersja zapoznawcza)

Dyski korzystające z modelu serii dysków na żądanie mogą przekraczać oryginalne aprowizowane cele, tak często, jak jest to wymagane przez ich obciążenie, aż do maksymalnego celu serii. Na przykład na dysku 1 TiB P30 aprowizowana ilość IOPS wynosi 5000 IOPS. Gdy na tym dysku włączono funkcję serii dysków, obciążenia mogą wystawiać na ten dysk urządzenia we/wy do maksymalnej wydajności 30 000 IOPS i 1000 MB/s.

Jeśli spodziewasz się, że obciążenia będą często przekraczać zaaprowizowany cel wydajności, nie będzie to opłacalne. W takim przypadku zalecamy zmianę warstwy wydajności dysku na [](../articles/virtual-machines/disks-performance-tiers.md) wyższą warstwę w celu lepszej wydajności linii bazowej. Przejrzyj szczegóły rozliczeń i oceń je pod względem wzorca ruchu obciążeń.

Przed włączeniem funkcji bursting na żądanie należy zrozumieć następujące kwestie:

[!INCLUDE [managed-disk-bursting-regions-limitations](managed-disk-bursting-regions-limitations.md)]

#### <a name="regional-availability"></a>Dostępność w regionach

[!INCLUDE [managed-disk-bursting-availability](managed-disk-bursting-availability.md)]

#### <a name="billing"></a>Rozliczenia

Dyski korzystające z modelu z serii na żądanie są obciążane godzinową opłatą ryczałtną za włączenie serii, a koszty transakcji mają zastosowanie do wszystkich transakcji z serii poza aprowizowany cel. Opłaty za transakcje są naliczane przy użyciu modelu płatności zgodnie z użyciem, opartego na nieskakowanych we/wy dysków, w tym operacji odczytu i zapisu, które przekraczają aprowizowane obiekty docelowe. Poniżej przedstawiono przykład wzorców ruchu dyskowego w ciągu godziny rozliczeniowej:

Konfiguracja dysku: SSD w warstwie Premium – 1 TiB (P30), włączone serii dysków.

- 00:00:00 – 00:10:00 Disk IOPS below provisioned target of 5000 IOPS (00:00:00 Disk IOPS below provisioned target of 5000 IOPS ) 
- 00:10:01 – 00:10:10 Aplikacja wystawiła zadanie wsadowe powodujące, że disk IOPS burst at 6000 IOPS for 10 seconds (Aplikacja wystawiła zadanie wsadowe powodujące, że disk IOPS burst at 6000 IOPS for 10 s) 
- 00:10:11 – 00:59:00 Disk IOPS below provisioned target of 5000 IOPS (00:59:00 Disk IOPS below provisioned target of 5000 IOPS )00:10:11 –00:59:00 Disk IOPS below provisioned target of 5000 IOPS (00:10:11 – 00: 
- 00:59:01 – 01:00:00 Aplikacja wystawiła kolejne zadanie wsadowe, powodując, że disk IOPS burst at 7000 IOPS for 60 s 

W tej godzinie rozliczeniowej koszt serii składa się z dwóch opłat:

Pierwsza opłata to ryczałtna opłata za włączenie $X (określona przez region). Ta opłata płaska jest zawsze naliczana w przypadku pomijania stanu dołączania dysku, dopóki nie zostanie wyłączona. 

Drugi to koszt transakcji serii. Nastąpiła seria dysków w dwóch gniazdach czasowych. Od 00:10:01 do 00:10:10 skumulowana transakcja serii wynosi (6000–5000) X 10 = 10 000. Od 00:59:01 – 01:00:00 skumulowana transakcja serii wynosi (7000 –5000) X 60 = 120 000. Łączna liczba transakcji serii wynosi 10 000 + 120 000 = 130 000. Koszt transakcji z serii zostanie naliczony przy $Y na podstawie 13 jednostek po 10 000 transakcji (na podstawie cen regionalnych).

W związku z tym łączny koszt na dysku, który ma miejsce w tej godzinie rozliczeniowej, jest równy wartościom $X + $Y. To samo obliczenie dotyczyłoby przesuwu nad aprowizowany cel MB/s. Przetłumaczymy na transakcje, których rozmiar operacji we/wy wynosi 256 KB. Jeśli ruch dyskowy przekracza aprowizowane wartości IOPS i MB/s, możesz zapoznać się z poniższym przykładem, aby obliczyć transakcje z serii. 

Konfiguracja dysku: SSD w warstwie Premium — 1 TB (P30), włączone jest bursting dysku.

- 00:00:01 – 00:00:05 Aplikacja wystawiła zadanie wsadowe powodujące, że dane we/wy dysku z 10 000 IOPS i 300 MB/s na pięć sekund.
- 00:00:06 – 00:00:10 Aplikacja wystawiła zadanie odzyskiwania, które powodowało, że na dysku we/wy na sekundę na 6000 I 600 MB/s na pięć sekund.

Transakcja serii jest uwzględniana jako maksymalna liczba transakcji z operacji we/wy na wy na wy nas lub mb/s z serii. Od 00:00:01 – 00:00:05 skumulowana transakcja serii jest maksymalna ((10 000–5000), (300–200) * 1024 /256)) * 5 = 25 000 transakcji. Od 00:00:06 – 00:00:10, skumulowana transakcja serii jest maksymalna ((6000 –5000), (600 –200) * 1024 / 256)) * 5 = 8000 transakcji. Do tego dołączyć opłatę płaską za włączenie funkcji burst, aby uzyskać całkowity koszt włączania serii dysków na żądanie. 

Szczegółowe informacje o cenach można [znaleźć na stronie](https://azure.microsoft.com/pricing/details/managed-disks/) Dyski zarządzane cennika platformy Azure i skorzystać z kalkulatora cen platformy [Azure,](https://azure.microsoft.com/pricing/calculator/?service=storage) aby dokonać oceny obciążenia. 


Aby włączyć funkcję bursting na żądanie, zobacz Włączanie funkcji [bursting na żądanie.](../articles/virtual-machines/disks-enable-bursting.md)

### <a name="credit-based-bursting"></a>Credit-based bursting (Przesyłanie danych na podstawie środków)

Skalowanie na podstawie środków jest dostępne dla dysków o rozmiarach P20 i mniejszych we wszystkich regionach chmur publicznych, rządowych i chińskich platformy Azure. Domyślnie funkcja skalowania dysków jest włączona we wszystkich nowych i istniejących wdrożeniach obsługiwanych rozmiarów dysków. Tryb bursting na poziomie maszyny wirtualnej używa tylko serii danych opartych na kredytach.

## <a name="virtual-machine-level-bursting"></a>Tryb bursting na poziomie maszyny wirtualnej

Funkcja bursting na poziomie maszyny wirtualnej używa tylko modelu opartego na środków do obsługi serii. Jest ona domyślnie włączona dla wszystkich maszyn wirtualnych, które go obsługują.

Funkcja bursting na poziomie maszyny wirtualnej jest włączona we wszystkich regionach chmury publicznej Azure w obsługiwanych rozmiarach: 
- [Seria Lsv2](../articles/virtual-machines/lsv2-series.md)
- [Serie Dv3 i Dsv3](../articles/virtual-machines/dv3-dsv3-series.md)
- [Serie Ev3 i Esv3](../articles/virtual-machines/ev3-esv3-series.md)

## <a name="bursting-flow"></a>Przepływ z serii

System środków na przełamanie jest stosowana w taki sam sposób zarówno na poziomie maszyny wirtualnej, jak i na dysku. Zasób, maszyna wirtualna lub dysk, rozpocznie się od w pełni zaakusowanych środków w swoim własnym zasobniku serii. Te środki umożliwiają okres do 30 minut z maksymalną szybkością serii. Środki są gromadzone za każdym razem, gdy zasób ma wartość IOPS lub MB/s, która jest wykorzystywana poniżej docelowego poziomu wydajności zasobu. Jeśli zasób naliczał środki na zwiększenie ilości danych, a obciążenie wymaga dodatkowej wydajności, zasób może użyć tych środków, aby osiągnąć limity wydajności i zwiększyć wydajność w celu spełnienia wymagań obciążeń.

![Diagram zasobnika z serii.](media/managed-disks-bursting/bucket-diagram.jpg)

Sposób wydatków na dostępne środki należy do Ciebie. Możesz użyć środków na serii 30 minut kolejno lub sporadycznie w ciągu dnia. Po wdrożeniu zasobów są one w pełni alokacji środków. W przypadku wyczerpania zapasów potrzeba mniej niż jeden dzień. Środki można wydać według własnego uznania. Zasobnik z serii nie musi być pełny, aby zasoby się rozsyłały. Akumulacja serii różni się w zależności od każdego zasobu, ponieważ jest oparta na nieużywanych wartościach IOPS i MB/s poniżej ich docelowych wartości wydajności. Zasoby o wyższej wydajności planu bazowego mogą być naliczane szybciej niż w przypadku zasobów o mniejszej wydajności. Na przykład w przypadku idlinga dysku P1 będzie naliczane 120 IOPS na sekundę, podczas gdy bezserowy dysk P20 będzie naliczać 2300 IOPS na sekundę.

## <a name="bursting-states"></a>Stany z serii
Istnieją trzy stany, w których może być zasób z włączonym trybem bursting:
- **Naliczanie** — ruch we/wy zasobu używa wartości mniejszej niż docelowa wydajność. Gromadzenie środków na przełamanie w przypadku IOPS i MB/s odbywa się oddzielnie od siebie. Zasób może naliczać środki na IOPS i wydatki na środki w MB/s lub na odwrót.
- **Bursting** — ruch zasobu używa więcej niż docelowy poziom wydajności. Ruch o dużej przepustowości będzie niezależnie zużywać środki z przepustowości lub we/wy na dane.
- **Stała** — ruch zasobu dokładnie znajduje się w docelowym miejscu wydajności.

## <a name="bursting-examples"></a>Przykłady z serii

Poniższe przykłady pokazują, jak seria działa z różnymi kombinacjami maszyn wirtualnych i dysków. Aby ułatwić obserwowanie przykładów, skupimy się na MB/s, ale ta sama logika jest stosowana niezależnie do IOPS.

### <a name="non-burstable-virtual-machine-with-burstable-disks"></a>Maszyna wirtualna bez serii z dyskami z serii burstable
**Kombinacja maszyny wirtualnej i dysku:** 
- Standard_D8as_v4 
    - Niecachowane MB/s: 192
- Dysk systemu operacyjnego P4
    - Aprowizowane MB/s: 25
    - Maksymalna liczba mb/s: 170 
- 2 dyski danych P10 
    - Aprowizowane MB/s: 100
    - Maksymalna liczba mb/s: 170

 Gdy maszyna wirtualna jest uruchamiana, pobiera dane z dysku systemu operacyjnego. Ponieważ dysk systemu operacyjnego jest częścią maszyny wirtualnej, która jest uruchamiana, dysk systemu operacyjnego będzie pełny środków na przepełnienie. Te środki pozwolą na uruchomienie dysku systemu operacyjnego o 170 MB/s.

![Maszyna wirtualna wysyła żądanie przepływności 192 MB/s do dysku systemu operacyjnego, a dysk systemu operacyjnego odpowiada przy użyciu danych 170 MB/s.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-startup.jpg)

Po zakończeniu rozruchu aplikacja jest następnie uruchamiana na maszynie wirtualnej i ma obciążenie niekrytyce. To obciążenie wymaga 15 MB/s, które jest równomiernie rozłożone na wszystkich dyskach.

![Aplikacja wysyła żądanie o przepływności 15 MB/s do maszyny wirtualnej, maszyna wirtualna przyjmuje żądanie i wysyła żądanie dla każdego z dysków o rozmiarze 5 MB/s, każdy dysk zwraca 5 MB/s, maszyna wirtualna zwraca do aplikacji 15 MB/s.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-idling.jpg)

Następnie aplikacja musi przetworzyć zadanie wsadowe, które wymaga 192 MB/s. Dysk systemu operacyjnego używa 2 MB/s, a pozostałe dyski danych są równomiernie podzielone.

![Aplikacja wysyła żądanie przepływności 192 MB/s do maszyny wirtualnej, maszyna wirtualna przyjmuje żądanie i wysyła większość żądania do dysków danych (każdy z nich 95 MB/s) i 2 MB/s na dysk systemu operacyjnego, dyski danych są zwiększane w celu spełnienia wymagań, a wszystkie dyski zwracają żądaną przepływność do maszyny wirtualnej, która zwraca ją do aplikacji.](media/managed-disks-bursting/nonbursting-vm-bursting-disk/nonbursting-vm-bursting-disk-bursting.jpg)

### <a name="burstable-virtual-machine-with-non-burstable-disks"></a>Maszyna wirtualna z serii z dyskami bez serii
**Kombinacja maszyny wirtualnej i dysku:** 
- Standard_L8s_v2 
    - Uncached MB/s: 160
    - Maksymalna liczba mb/s: 1280
- Dysk systemu operacyjnego P50
    - Aprowizowane MB/s: 250 
- 2 dyski danych P10 
    - Aprowizowane MB/s: 250

 Po początkowym rozruchu aplikacja jest uruchamiana na maszynie wirtualnej i ma obciążenie niekrytyce. To obciążenie wymaga 30 MB/s, które jest równomiernie rozłożone na wszystkich dyskach.
![Aplikacja wysyła żądanie o przepływność 30 MB/s do maszyny wirtualnej, maszyna wirtualna przyjmuje żądanie i wysyła żądanie dla każdego z dysków o przepływności 10 MB/s, każdy dysk zwraca 10 MB/s, maszyna wirtualna zwraca do aplikacji 30 MB/s.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-normal.jpg)

Następnie aplikacja musi przetworzyć zadanie wsadowe, które wymaga 600 MB/s. Wymagania Standard_L8s_v2 się, a następnie żądania do dysków są równomiernie rozłożone na dyski P50.

![Aplikacja wysyła żądanie przepływności o przepływności 600 MB/s do maszyny wirtualnej, maszyna wirtualna ma serii, aby pobrać żądanie, i wysyła do każdego z dysków żądanie o przepływności 200 MB/s, każdy dysk zwraca 200 MB/s, maszyny wirtualne są zwiększane, aby zwrócić do aplikacji 600 MB/s.](media/managed-disks-bursting/bursting-vm-nonbursting-disk/burst-vm-nonbursting-disk-bursting.jpg)
### <a name="burstable-virtual-machine-with-burstable-disks"></a>Maszyna wirtualna z serii z dyskami z serii burstable
**Kombinacja maszyny wirtualnej i dysku:** 
- Standard_L8s_v2 
    - Niecachowane MB/s: 160
    - Maksymalna liczba mb/s: 1280
- Dysk systemu operacyjnego P4
    - Aprowizowane MB/s: 25
    - Maksymalna liczba mb/s: 170 
- 2 dyski danych P4 
    - Aprowizowane MB/s: 25
    - Maksymalna liczba mb/s: 170 

Po uruchamianiu maszyny wirtualnej zostanie ona rozsyłana, aby zażądać limitu serii 1280 MB/s z dysku systemu operacyjnego, a dysk systemu operacyjnego będzie odpowiadać z wydajnością serii 170 MB/s.

![Podczas uruchamiania maszyna wirtualna jest uruchamiana w trybie burst w celu wysłania żądania o rozmiarze 1280 MB/s na dysk systemu operacyjnego, a dysk systemu operacyjnego jest serii, aby zwrócić 1280 MB/s.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-startup.jpg)

Po uruchomieniu uruchamiasz aplikację z obciążeniem niekrytyczną. Ta aplikacja wymaga 15 MB/s, która jest rozłożona równomiernie na wszystkich dyskach.

![Aplikacja wysyła żądanie o przepływności 15 MB/s do maszyny wirtualnej, maszyna wirtualna przyjmuje żądanie i wysyła do każdego ze swoich dysków żądanie o rozmiarze 5 MB/s, każdy dysk zwraca odpowiedzi 5 MB/s, maszyna wirtualna zwraca do aplikacji 15 MB/s.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-idling.jpg)

Następnie aplikacja musi przetworzyć zadanie wsadowe, które wymaga 360 MB/s. Zapotrzebowanie Standard_L8s_v2 się na wzrosty, a następnie na żądanie. Dysk systemu operacyjnego wymaga tylko 20 MB/s. Pozostałe 340 MB/s są obsługiwane przez dyski danych P4 z serii.

![Aplikacja wysyła żądanie przepływności o przepływności 360 MB/s do maszyny wirtualnej, maszyna wirtualna pobiera żądania w seriach i wysyła do każdego z dysków danych żądanie o przepustowości 170 MB/s i 20 MB/s z dysku systemu operacyjnego, każdy dysk zwraca żądane MB/s, a maszyny wirtualne mają zwracać do aplikacji 360 MB/s.](media/managed-disks-bursting/bursting-vm-bursting-disk/burst-vm-burst-disk-bursting.jpg)
