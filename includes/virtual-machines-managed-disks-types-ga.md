---
title: plik dołączany
description: plik dołączany
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/03/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 90cd1d8968963d428eb2d1de4efb458b5f89ca74
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2020
ms.locfileid: "84436188"
---
## <a name="premium-ssd"></a>Dysk SSD w warstwie Premium

Usługa Azure Premium dysków SSD zapewnia obsługę dysków o wysokiej wydajności i małych opóźnieniach dla maszyn wirtualnych z intensywnymi obciążeniami we/wy (IO). Aby skorzystać z szybkości i wydajności dysków magazynu w warstwie Premium, można migrować istniejące dyski maszyn wirtualnych do dysków SSD Premium. Dysków SSD Premium są odpowiednie dla aplikacji produkcyjnych o kluczowym znaczeniu. Dysków SSD Premium można używać tylko z seriami maszyn wirtualnych, które są zgodne z magazynem w warstwie Premium.

Aby dowiedzieć się więcej o poszczególnych typach maszyn wirtualnych i rozmiarach na platformie Azure dla systemu Windows, w tym o rozmiarach, które są zgodne z magazynem Premium, zobacz [rozmiary maszyn wirtualnych z systemem Windows](../articles/virtual-machines/windows/sizes.md). Aby dowiedzieć się więcej o poszczególnych typach maszyn wirtualnych i rozmiarach na platformie Azure dla systemu Linux, w tym o rozmiarach, które są zgodne z magazynem Premium, zobacz [rozmiary maszyn wirtualnych z systemem Linux](../articles/virtual-machines/linux/sizes.md). Z dowolnego z tych artykułów należy sprawdzić każdy artykuł o rozmiarze poszczególnych maszyn wirtualnych, aby określić, czy jest on zgodny z magazynem w warstwie Premium.

### <a name="disk-size"></a>Rozmiar dysku
[!INCLUDE [disk-storage-premium-ssd-sizes](disk-storage-premium-ssd-sizes.md)]

Po udostępnieniu dysku magazynu w warstwie Premium, w przeciwieństwie do magazynu w warstwie Standardowa, gwarantujesz pojemność, liczbę operacji we/wy na sekundę oraz przepływność tego dysku. Na przykład, jeśli utworzysz dysk P50, platforma Azure ma 4 095 GB pamięci 7 500 masowej, szybkość operacji we/wy na sekundę i 250 MB/s dla tego dysku. Aplikacja może używać całości lub części pojemności i wydajności. SSD w warstwie Premium dyski zostały zaprojektowane w celu zapewnienia niskich opóźnień w milisekundach i docelowej liczby operacji we/wy i przepływności opisanej w poprzedniej tabeli o 99,9% czasu.

## <a name="bursting"></a>Rozszerzanie możliwości

SSD w warstwie Premium rozmiary mniejsze niż P30 teraz oferują możliwość naliczania dysków na dyskach, a ich liczba operacji we/wy na sekundę wynosi do 3 500 i przepustowość do 170 MB/s. Rozliczanie jest zautomatyzowane i działa na podstawie systemu kredytowego. Kredyty są automatycznie sumowane w zasobniku szeregowym, gdy ruch dysku jest niższy od zainicjowanej wartości docelowej wydajności, a kredyty są automatycznie zużywane, gdy ruch przekracza miejsce docelowe, do maksymalnego limitu. Maksymalny limit serii określa limit liczby operacji we/wy dysku & przepustowości, nawet jeśli masz kredyty na korzystanie z serii. Szeregowanie dysków zapewnia lepszą tolerancję w nieprzewidywalnych zmianach wzorców we/wy. Można go najlepiej wykorzystać do rozruchu z dysku systemu operacyjnego i aplikacji z ruchem pochodzącym.    

Obsługa serii dysków zostanie włączona w nowych wdrożeniach odpowiednich rozmiarów dysków domyślnie, bez konieczności wykonywania jakiejkolwiek czynności przez użytkownika. W przypadku istniejących dysków o odpowiednich rozmiarach można włączyć korzystanie z jednej z dwóch opcji: Odłącz i ponownie podłącz dysk lub zatrzymywanie i ponowne uruchamianie dołączonej maszyny wirtualnej. Wszystkie odpowiednie rozmiary dysku będą rozpoczynać się od pełnego zasobnika kredytowego, gdy dysk jest podłączony do maszyny wirtualnej, która obsługuje maksymalny czas trwania w szczycie maksymalnego limitu wynoszącym 30 minut. Aby dowiedzieć się więcej o sposobach tworzenia zadań na dyskach platformy Azure, zobacz [SSD w warstwie Premiuming](../articles/virtual-machines/linux/disk-bursting.md). 

### <a name="transactions"></a>Transakcje

W przypadku usługi Premium dysków SSD każda operacja we/wy mniejsza lub równa 256 KiB przepływności jest uznawana za pojedynczą operację we/wy. Operacje we/wy o rozmiarze większym niż 256 KiB przepływności są uważane za wiele systemów I/OS o rozmiarach 256 KiB.

## <a name="standard-ssd"></a>Dysk SSD w warstwie Standardowa

Azure Standard dysków SSD to ekonomiczna opcja magazynowania zoptymalizowana pod kątem obciążeń, które wymagają spójnej wydajności na niższych poziomach IOPS. SSD w warstwie Standardowa oferuje dobry komfort na poziomie wpisu dla osób, które chcą przejść do chmury, szczególnie w przypadku wystąpienia problemów z wariancją obciążeń uruchomionych na rozwiązaniach dysków twardych. W porównaniu do standardowej HDD, standardowa dysków SSD zapewnia lepszą dostępność, spójność, niezawodność i opóźnienia. Standardowe dysków SSD są odpowiednie dla serwerów sieci Web, serwerów aplikacji o małej liczbie IOPS, nieużywanych aplikacji dla przedsiębiorstw oraz obciążeń deweloperskich i testowych. Podobnie jak w przypadku standardowych HDD, standardowe dysków SSD są dostępne na wszystkich maszynach wirtualnych platformy Azure.

### <a name="disk-size"></a>Rozmiar dysku
[!INCLUDE [disk-storage-standard-ssd-sizes](disk-storage-standard-ssd-sizes.md)]

Standard dysków SSD został zaprojektowany w celu zapewnienia jednocyfrowych opóźnień i liczby operacji we/wy na sekundę i przepływności do limitów opisanych w powyższej tabeli 99% czasu. Rzeczywiste operacje we/wy i przepływność mogą się czasami różnić w zależności od wzorców ruchu. Standardowa dysków SSD zapewnia bardziej spójną wydajność niż dyski twarde z mniejszym opóźnieniem.

### <a name="transactions"></a>Transakcje

W przypadku standardowej dysków SSD każda operacja we/wy mniejsza lub równa 256 KiB przepływności jest uznawana za pojedynczą operację we/wy. Operacje we/wy o rozmiarze większym niż 256 KiB przepływności są uważane za wiele systemów I/OS o rozmiarach 256 KiB. Te transakcje mają wpływ na rozliczenia.

## <a name="standard-hdd"></a>Dysk HDD w warstwie Standardowa

Usługa Azure Standard HDD zapewnia niezawodną, tańszą obsługę dysków dla maszyn wirtualnych, na których działają obciążenia bez uwzględniania opóźnień. W przypadku magazynu w warstwie Standardowa dane są przechowywane na dyskach twardych (HDD). Opóźnienia, operacje we/wy i przepływność dysków HDD w warstwie Standardowa mogą być różne w porównaniu z dyskami SSD. HDD w warstwie Standardowa dyski zostały zaprojektowane w celu zapewnienia opóźnień zapisu w obszarze 10 ms i odczyt opóźnień w obszarze 20ms dla większości operacji we/wy, jednak Rzeczywista wydajność może się różnić w zależności od rozmiaru operacji we/wy i wzorca obciążenia. Podczas pracy z maszynami wirtualnymi można używać standardowych dysków DYSKowych na potrzeby scenariuszy tworzenia i testowania oraz mniej krytycznych obciążeń. Standardowe HDD są dostępne we wszystkich regionach świadczenia usługi Azure i mogą być używane ze wszystkimi maszynami wirtualnymi platformy Azure.

### <a name="disk-size"></a>Rozmiar dysku
[!INCLUDE [disk-storage-standard-hdd-sizes](disk-storage-standard-hdd-sizes.md)]

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
