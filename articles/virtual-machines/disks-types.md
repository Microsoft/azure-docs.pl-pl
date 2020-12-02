---
title: Wybierz typ dysku dla maszyn wirtualnych Azure IaaS — dyski zarządzane
description: Dowiedz się więcej na temat dostępnych typów dysków platformy Azure dla maszyn wirtualnych, w tym Ultra disks, Premium dysków SSD, standard dysków SSD i Standard HDD.
author: roygara
ms.author: rogarana
ms.date: 09/30/2020
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 1a7e8e71e26af241d16095a5fa1e6a02a7e3d4c2
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96500771"
---
# <a name="what-disk-types-are-available-in-azure"></a>Jakie typy dysków są dostępne na platformie Azure?

Usługa Azure Managed disks obecnie oferuje cztery typy dysków, a każdy typ jest skierowany do określonych scenariuszy klientów.

## <a name="disk-comparison"></a>Porównanie dysków

Poniższa tabela zawiera porównanie dysków twardych, dysków półprzewodnikowych (SSD), standardowego dysku SSD i standardowych dysków twardych na potrzeby dysków zarządzanych, które ułatwią podjęcie decyzji o tym, czego używać.

| Szczegół | Dysk w warstwie Ultra | Dysk SSD w warstwie Premium | Dysk SSD w warstwie Standardowa | Dysk HDD w warstwie Standardowa |
| ------ | ---------- | ----------- | ------------ | ------------ |
|Typ dysku   |SSD   |SSD   |SSD   |HDD   |
|Scenariusz   |Obciążenia intensywnie korzystające z operacji we/wy, takie jak [SAP HANA](workloads/sap/hana-vm-operations-storage.md), baz danych najwyższej warstwy (na przykład SQL, Oracle) i innych obciążeń intensywnie korzystających z transakcji.   |Obciążenia produkcyjne i wrażliwe na wydajność   |Serwery internetowe, aplikacje dla przedsiębiorstw używane w niewielkim stopniu oraz tworzenie i testowanie rozwiązań   |Kopia zapasowa, rozwiązania niekrytyczne, rzadki dostęp   |
|Maksymalny rozmiar dysku   |65 536 gibibajtów (GiB)    |32 767 GiB    |32 767 GiB   |32 767 GiB   |
|Maksymalna przepustowość   |2 000 MB/s    |900 MB/s   |750 MB/s   |500 MB/s   |
|Maks. liczba operacji we/wy na sekundę   |160 000    |20 000   |6000   |2000   |

## <a name="ultra-disk"></a>Dysk w warstwie Ultra

Dyski platformy Azure w warstwie Ultra oferują magazyn danych na dysku z wysoką przepływnością, dużą liczbą operacji we/wy na sekundę i stałym małym opóźnieniem dla maszyn wirtualnych usługi Azure IaaS. Niektóre dodatkowe zalety funkcji Ultra disks to możliwość dynamicznego zmieniania wydajności dysku wraz z obciążeniami bez konieczności ponownego uruchamiania maszyn wirtualnych. Dyski w warstwie Ultra to rozwiązanie odpowiednie w przypadku obciążeń intensywnie korzystających z danych, takich jak platforma SAP HANA, bazy danych górnej warstwy i obciążenia z dużą liczbą transakcji. Dysków w warstwie Ultra można używać tylko jako dysków danych. Zalecamy używanie dysków SSD w warstwie Premium jako dysków systemu operacyjnego.

### <a name="performance"></a>Wydajność

Podczas aprowizacji Ultra Disk można niezależnie skonfigurować pojemność i wydajność dysku. Niezwykle dyski są dostępne w kilku stałych rozmiarach, z przedziału od 4 GiB do 64 TiB i oferują elastyczny model konfiguracji wydajności, który pozwala niezależnie konfigurować operacje we/wy i przepływność.

Niektóre kluczowe możliwości Ultra disks to:

- Pojemność dysku: pojemność Ultra disks z zakresu od 4 GiB do 64 TiB.
- Liczba operacji we/wy na sekundę: niezwykle dyski obsługują limity IOPS 300 operacji we/wy na sekundę i maksymalnie 160 K operacji we/wy na dysku. Aby osiągnąć liczbę operacji we/wy, która została zainicjowana, upewnij się, że wybrane operacje we/wy na dysku są mniejsze niż limit liczby IOPS maszyny wirtualnej. Minimalna gwarantowana liczba operacji we/wy na dysk wynosi 2 IOPS/GiB, a ogólna linia bazowa wynosi co najmniej 100 operacji we/wy. Na przykład jeśli masz 4 GiB Ultra Disk, będziesz mieć co najmniej 100 operacji we/wy, a nie osiem operacji we/wy na sekundę.
- Przepływność dysku: przy użyciu Ultra dysków limit przepływności pojedynczego dysku to 256 KiB/s dla każdej z zainicjowanych operacji we/wy, maksymalnie 2000 megabajtów na sekundę (MB/s = 10 ^ 6 b/s). Minimalną gwarantowaną przepływność na dysk to 4KiB/s dla każdej z zainicjowanych operacji we/wy z co najmniej 1 MB/s.
- Ultra disks obsługuje dostosowanie atrybutów wydajności dysku (IOPS i przepływności) w czasie wykonywania bez odłączania dysku od maszyny wirtualnej. Po wystawieniu operacji zmiany rozmiaru dysku na dysku może upłynąć do czasu, aż zmiana zacznie obowiązywać. Przekroczono limit czterech operacji zmiany rozmiaru wydajności w oknie 24-godzinnym. Istnieje możliwość, że operacja zmiany rozmiaru wydajności nie powiedzie się z powodu braku wydajności przepustowości.

### <a name="disk-size"></a>Rozmiar dysku

|Rozmiar dysku (GiB)  |Limit operacji we/wy  |Limit przepływności (MB/s)  |
|---------|---------|---------|
|4     |1200         |300         |
|8     |2 400         |600         |
|16     |4800         |1200         |
|32     |9600         |2000         |
|64     |19 200         |2000         |
|128     |38 400         |2000         |
|256     |76 800         |2000         |
|512     |153 600         |2000         |
|1024-65536 (rozmiary w tym zakresie zwiększają się w przyrostach 1 TiB)     |160 000         |2000         |

Ultra disks została zaprojektowana w celu zapewnienia opóźnień w milisekundach oraz docelowych operacji we/wy i przepływności opisanej w poprzedniej tabeli o 99,99% czasu.

### <a name="ga-scope-and-limitations"></a>Zakres i ograniczenia dotyczące GA

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](../../includes/managed-disks-ultra-disks-GA-scope-and-limitations.md)]


Jeśli chcesz zacząć korzystać z usługi Ultra disks, zapoznaj się z artykułem na temat tematu: [Korzystanie z platformy Azure Ultra disks](disks-enable-ultra-ssd.md).

## <a name="premium-ssd"></a>Dysk SSD w warstwie Premium

Usługa Azure Premium dysków SSD zapewnia obsługę dysków o wysokiej wydajności i małych opóźnieniach dla maszyn wirtualnych z intensywnymi obciążeniami we/wy (IO). Aby skorzystać z szybkości i wydajności dysków magazynu w warstwie Premium, można migrować istniejące dyski maszyn wirtualnych do dysków SSD Premium. Dysków SSD Premium są odpowiednie dla aplikacji produkcyjnych o kluczowym znaczeniu. Dysków SSD Premium można używać tylko z seriami maszyn wirtualnych, które są zgodne z magazynem w warstwie Premium.

Aby dowiedzieć się więcej o poszczególnych typach maszyn wirtualnych i rozmiarach na platformie Azure dla systemu Windows lub Linux, w tym o rozmiarach, które są zgodne z magazynem w warstwie Premium, zobacz [rozmiary maszyn wirtualnych na platformie Azure](sizes.md). W tym artykule należy sprawdzić każdy artykuł o rozmiarze poszczególnych maszyn wirtualnych, aby określić, czy jest on zgodny z magazynem w warstwie Premium.

### <a name="disk-size"></a>Rozmiar dysku
[!INCLUDE [disk-storage-premium-ssd-sizes](../../includes/disk-storage-premium-ssd-sizes.md)]

Po udostępnieniu dysku magazynu w warstwie Premium, w przeciwieństwie do magazynu w warstwie Standardowa, gwarantujesz pojemność, liczbę operacji we/wy na sekundę oraz przepływność tego dysku. Na przykład, jeśli utworzysz dysk P50, platforma Azure ma 4 095 GB pamięci 7 500 masowej, szybkość operacji we/wy na sekundę i 250 MB/s dla tego dysku. Aplikacja może używać całości lub części pojemności i wydajności. SSD w warstwie Premium dyski zostały zaprojektowane w celu zapewnienia niskich opóźnień w milisekundach i docelowej liczby operacji we/wy i przepływności opisanej w poprzedniej tabeli o 99,9% czasu.

## <a name="bursting"></a>Rozszerzanie możliwości

SSD w warstwie Premium rozmiary mniejsze niż P30 teraz oferują możliwość naliczania dysków na dyskach, a ich liczba operacji we/wy na sekundę wynosi nawet 3 500 i przepustowość do 170 MB/s. Rozliczanie jest zautomatyzowane i działa na podstawie systemu kredytowego. Kredyty są automatycznie sumowane w zasobniku szeregowym, gdy ruch dysku jest niższy od zainicjowanej wartości docelowej wydajności, a kredyty są automatycznie zużywane, gdy ruch przekracza miejsce docelowe, do maksymalnego limitu. Maksymalny limit serii określa limit liczby operacji we/wy dysku & przepustowości, nawet jeśli masz kredyty na korzystanie z serii. Szeregowanie dysków zapewnia lepszą tolerancję w nieprzewidywalnych zmianach wzorców we/wy. Można go najlepiej wykorzystać do rozruchu z dysku systemu operacyjnego i aplikacji z ruchem pochodzącym.    

Obsługa serii dysków zostanie włączona w nowych wdrożeniach odpowiednich rozmiarów dysków domyślnie, bez konieczności wykonywania jakiejkolwiek czynności przez użytkownika. W przypadku istniejących dysków o odpowiednich rozmiarach można włączyć korzystanie z jednej z dwóch opcji: Odłącz i ponownie podłącz dysk lub zatrzymywanie i ponowne uruchamianie dołączonej maszyny wirtualnej. Wszystkie odpowiednie rozmiary dysku będą rozpoczynać się od pełnego zasobnika kredytowego, gdy dysk jest podłączony do maszyny wirtualnej, która obsługuje maksymalny czas trwania w szczycie maksymalnego limitu wynoszącym 30 minut. Aby dowiedzieć się więcej o sposobach tworzenia zadań na dyskach platformy Azure, zobacz [SSD w warstwie Premiuming](./disk-bursting.md). 

### <a name="transactions"></a>Transakcje

W przypadku usługi Premium dysków SSD każda operacja we/wy mniejsza lub równa 256 KiB przepływności jest uznawana za pojedynczą operację we/wy. Operacje we/wy o rozmiarze większym niż 256 KiB przepływności są uważane za wiele systemów I/OS o rozmiarach 256 KiB.

## <a name="standard-ssd"></a>Dysk SSD w warstwie Standardowa

Azure Standard dysków SSD to ekonomiczna opcja magazynowania zoptymalizowana pod kątem obciążeń, które wymagają spójnej wydajności na niższych poziomach IOPS. SSD w warstwie Standardowa oferuje dobry komfort na poziomie wpisu dla osób, które chcą przejść do chmury, szczególnie w przypadku wystąpienia problemów z wariancją obciążeń uruchomionych na rozwiązaniach dysków twardych. W porównaniu do standardowej HDD, standardowa dysków SSD zapewnia lepszą dostępność, spójność, niezawodność i opóźnienia. Standardowe dysków SSD są odpowiednie dla serwerów sieci Web, serwerów aplikacji o małej liczbie IOPS, nieużywanych aplikacji dla przedsiębiorstw oraz obciążeń deweloperskich i testowych. Podobnie jak w przypadku standardowych HDD, standardowe dysków SSD są dostępne na wszystkich maszynach wirtualnych platformy Azure.

### <a name="disk-size"></a>Rozmiar dysku
[!INCLUDE [disk-storage-standard-ssd-sizes](../../includes/disk-storage-standard-ssd-sizes.md)]

Standard dysków SSD został zaprojektowany w celu zapewnienia jednocyfrowych opóźnień i liczby operacji we/wy na sekundę i przepływności do limitów opisanych w powyższej tabeli 99% czasu. Rzeczywiste operacje we/wy i przepływność mogą się czasami różnić w zależności od wzorców ruchu. Standardowa dysków SSD zapewnia bardziej spójną wydajność niż dyski twarde z mniejszym opóźnieniem.

### <a name="transactions"></a>Transakcje

W przypadku standardowej dysków SSD każda operacja we/wy mniejsza lub równa 256 KiB przepływności jest uznawana za pojedynczą operację we/wy. Operacje we/wy o rozmiarze większym niż 256 KiB przepływności są uważane za wiele systemów I/OS o rozmiarach 256 KiB. Te transakcje mają wpływ na rozliczenia.

## <a name="standard-hdd"></a>Dysk HDD w warstwie Standardowa

Usługa Azure Standard HDD zapewnia niezawodną, tańszą obsługę dysków dla maszyn wirtualnych, na których działają obciążenia bez uwzględniania opóźnień. W przypadku magazynu w warstwie Standardowa dane są przechowywane na dyskach twardych (HDD). Opóźnienia, operacje we/wy i przepływność dysków HDD w warstwie Standardowa mogą być różne w porównaniu z dyskami SSD. HDD w warstwie Standardowa dyski zostały zaprojektowane w celu zapewnienia opóźnień zapisu w obszarze 10 ms i odczyt opóźnień w obszarze 20ms dla większości operacji we/wy, jednak Rzeczywista wydajność może się różnić w zależności od rozmiaru operacji we/wy i wzorca obciążenia. Podczas pracy z maszynami wirtualnymi można używać standardowych dysków DYSKowych na potrzeby scenariuszy tworzenia i testowania oraz mniej krytycznych obciążeń. Standardowe HDD są dostępne we wszystkich regionach świadczenia usługi Azure i mogą być używane ze wszystkimi maszynami wirtualnymi platformy Azure.

### <a name="disk-size"></a>Rozmiar dysku
[!INCLUDE [disk-storage-standard-hdd-sizes](../../includes/disk-storage-standard-hdd-sizes.md)]

### <a name="transactions"></a>Transakcje

W przypadku standardowej HDD każda operacja we/wy jest uznawana za pojedynczą transakcję, niezależnie od rozmiaru wejścia/wyjścia. Te transakcje mają wpływ na rozliczenia.

## <a name="billing"></a>Rozliczenia

W przypadku korzystania z usługi Managed disks obowiązują następujące zagadnienia dotyczące rozliczeń:

- Typ dysku
- Rozmiar dysku zarządzanego
- Migawki
- Wychodzące transfery danych
- Liczba transakcji

**Rozmiar dysku zarządzanego**: dyski zarządzane są rozliczane zgodnie z rozmiarem aprowizacji. Platforma Azure mapuje przydziały (zaokrąglone w górę) do najbliższego dostępnego rozmiaru dysku. Szczegółowe informacje o oferowanych rozmiarach dysków można znaleźć w poprzednich tabelach. Każdy dysk jest mapowany do obsługiwanej oferty rozmiaru dysku i jest rozliczany zgodnie z potrzebami. Na przykład, jeśli udostępnisz 200 GiB SSD w warstwie Standardowa, mapuje do oferty rozmiaru dysku E15 (256 GiB). Opłaty za dowolny dysk z zainicjowaną obsługą są naliczane proporcjonalnie do liczby godzin przy użyciu ceny miesięcznej oferty magazynu. Na przykład jeśli udostępnisz dysk E10 i usuniesz go po 20 godzinach, opłaty są naliczane za ofertę E10, która jest naliczana proporcjonalnie do 20 godzin. Jest to niezależne od ilości danych, które są zapisywane na dysku.

**Migawki**: rozliczanie migawek odbywa się na podstawie używanego rozmiaru. Na przykład, jeśli utworzysz migawkę dysku zarządzanego o pojemności 64 GiB i rzeczywistej używanej wielkości danych wynoszącej 10 GiB, dla migawki zostanie naliczona tylko za użyty rozmiar danych wynoszący 10 GiB.

Aby uzyskać więcej informacji na temat migawek, zobacz sekcję dotyczącą migawek na [dysku zarządzanym — Omówienie](managed-disks-overview.md).

**Wychodzące transfery danych**: [wychodzące transfery danych](https://azure.microsoft.com/pricing/details/bandwidth/) (dane przesyłane z centrów danych platformy Azure) powodują naliczanie opłat za użycie przepustowości.

**Transakcje**: opłaty są naliczane za liczbę transakcji wykonywanych na dysku zarządzanym w warstwie Standardowa. W przypadku standardowej dysków SSD każda operacja we/wy mniejsza lub równa 256 KiB przepływności jest uznawana za pojedynczą operację we/wy. Operacje we/wy o rozmiarze większym niż 256 KiB przepływności są uważane za wiele systemów I/OS o rozmiarach 256 KiB. W przypadku standardowej HDD każda operacja we/wy jest uznawana za pojedynczą transakcję, niezależnie od rozmiaru wejścia/wyjścia.

Aby uzyskać szczegółowe informacje na temat cen Managed Disks, w tym kosztów transakcji, zobacz [Managed disks cennika](https://azure.microsoft.com/pricing/details/managed-disks).

### <a name="ultra-disk-vm-reservation-fee"></a>Opłata rezerwacji na maszynę wirtualną Ultra Disk

Maszyny wirtualne platformy Azure mają możliwość wskazywania, czy są one zgodne z Ultra Disks. Maszyna wirtualna zgodne z dyskami jest przydzielona dedykowanej przepustowości między wystąpieniem maszyny wirtualnej obliczeniowej a jednostką skali magazynu blokowego w celu zoptymalizowania wydajności i skrócenia opóźnień. Dodanie tej funkcji na maszynie wirtualnej spowoduje naliczenie opłaty za rezerwację, która jest nałożona tylko wtedy, gdy na maszynie wirtualnej jest włączona funkcja Ultra Disk bez dołączania do niej dysku. Gdy dysk jest podłączony do maszyny wirtualnej zgodnej z dyskiem Ultra, ta opłata nie zostanie zastosowana. Ta opłata jest naliczana za vCPU na maszynie wirtualnej. 

> [!Note]
> W przypadku [ograniczonych rozmiarów maszyn wirtualnych](constrained-vcpu.md)opłata za rezerwację jest oparta na rzeczywistej liczbie procesorów wirtualnych vCPU, a nie w ograniczonych rdzeniach. W przypadku Standard_E32-8s_v3 opłata za rezerwację będzie oparta na 32 rdzeniach. 

Zapoznaj się ze [stroną cennika usługi Azure disks](https://azure.microsoft.com/pricing/details/managed-disks/) , aby uzyskać szczegółowe informacje o cenach.

### <a name="azure-disk-reservation"></a>Rezerwacja dysku platformy Azure

Rezerwacja dysku to opcja zakupu jednego roku magazynu dyskowego z góry z rabatem, co zmniejsza całkowity koszt. Podczas kupowania rezerwacji dysku wybierana jest określona jednostka SKU dysku w regionie docelowym, na przykład 10 P30 (1TiB) Premium dysków SSD w regionie Wschodnie stany USA 2 przez okres jednego roku. Środowisko rezerwacji jest podobne do wystąpień zarezerwowanych maszyn wirtualnych. W celu zmaksymalizowania oszczędności można powiązać rezerwacje maszyn wirtualnych i dysków. Na razie rezerwacja Azure disks oferuje roczny plan zobowiązania dla jednostek SKU SSD w warstwie Premium z P30 (1TiB) do P80 (32 TiB) we wszystkich regionach produkcyjnych. Aby uzyskać więcej informacji na temat cen dysków zarezerwowanych, zobacz [stronę cennika usługi Azure disks](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć, zobacz [cennik Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/) .