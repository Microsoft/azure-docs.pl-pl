---
title: Metryki dysku
description: Przykłady metryk dotyczącej obciążeń dysku
author: roygara
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 60486c41ad843cf193ee0648dfcfef66f7668e47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101674977"
---
# <a name="disk-performance-metrics"></a>Metryki wydajności dysku
Platforma Azure oferuje metryki w Azure Portal, które zapewniają wgląd w działanie maszyn wirtualnych i dysków. Metryki mogą być również pobierane za pomocą wywołania interfejsu API. Ten artykuł jest podzielony na 3 podsekcje:

- **Metryki operacji we/wy dysku, przepływności i głębokości kolejki** — te metryki umożliwiają wyświetlenie wydajności magazynu z perspektywy dysku i maszyny wirtualnej.
- **Metryki** dotyczące obciążeń dysku — są to metryki zapewniające wgląd w [nasze funkcje związane](disk-bursting.md) z rozbiciem na nasze dyski w warstwie Premium.
- **Metryki wykorzystania operacji we/wy magazynu** — te metryki ułatwiają diagnozowanie wąskich gardeł w wydajności magazynu z użyciem dysków. 

Wszystkie metryki są emitowane co minutę, z wyjątkiem metryki procentowej, która jest emitowana co 5 minut.

## <a name="disk-io-throughput-and-queue-depth-metrics"></a>Metryki operacji we/wy dysku, przepływności i głębokości kolejki
Następujące metryki są dostępne w celu uzyskania szczegółowych informacji na temat wydajności maszyn wirtualnych i operacji we/wy na dysku, przepływności i głębokości kolejki:

- **Głębokość kolejki dysku systemu operacyjnego**: liczba bieżących oczekujących żądań we/wy, które oczekują na odczytanie lub zapis na dysku systemu operacyjnego.
- **Bajty odczytu z dysku systemu operacyjnego/s**: liczba bajtów odczytanych w drugim z dysku systemu operacyjnego.
- **Operacje odczytu z dysku systemu operacyjnego/s**: liczba operacji wejściowych, które są odczytywane w drugim z dysku systemu operacyjnego.
- **Bajty zapisu na dysku systemu operacyjnego/s**: liczba bajtów zapisanych w drugim z dysku systemu operacyjnego.
- **Operacje zapisu na dysku systemu operacyjnego/s**: liczba operacji wyjściowych, które są zapisywane w drugim z dysku systemu operacyjnego.
- **Głębokość kolejki dysku danych**: liczba bieżących oczekujących żądań we/wy, które oczekują na odczyt lub zapis na dyskach danych.
- **Bajty odczytu z dysku danych/s**: liczba bajtów odczytanych w s z dysku danych.
- **Operacje odczytu z dysku danych/s**: liczba operacji wejściowych, które są odczytywane w s z dysku danych.
- **Bajty zapisu na dysku danych/s**: liczba bajtów zapisanych sekundowo z dysków danych.
- **Operacje zapisu na dysku danych/s**: liczba operacji wyjściowych, które są zapisywane w s z dysku danych.
- **Bajty odczytu dysku/s**: całkowita liczba bajtów odczytywanych w drugim ze wszystkich dysków dołączonych do maszyny wirtualnej.
- **Operacje odczytu z dysku/s**: liczba operacji wejścia, które są odczytywane w sekundę ze wszystkich dysków dołączonych do maszyny wirtualnej.
- **Bajty zapisu dysku/s**: liczba bajtów napisanych w drugim ze wszystkich dysków dołączonych do maszyny wirtualnej.
- **Operacje zapisu na dysku/s**: liczba operacji wyjściowych, które są zapisywane w drugim ze wszystkich dysków dołączonych do maszyny wirtualnej.

## <a name="bursting-metrics"></a>Metryki dotyczące przenoszenia
Następujące metryki ułatwiają przestrzeganie [naszej funkcji masowej na naszych](disk-bursting.md) dyskach Premium:

- **Maksymalna przepustowość dysku danych**: limit przepływności, do którego mogą się odistnieć dyski danych.
- **Maksymalna przepustowość dysku systemu operacyjnego**: limit przepływności, do którego może się połączyć dysk systemu operacyjnego.
- **Maksymalna liczba operacji we/wy na dysku danych**: limit operacji we/wy na sekundę, do których można wykonać operacje na dyskach danych.
- **Maksymalna liczba operacji we/wy dysku systemu operacyjnego**: limit liczby operacji we/wy na sekundę, do których może się połączyć dysk systemu operacyjnego.
- **Przepustowość docelowa dysku danych**: limit przepływności, który może osiągnąć dysk danych bez konieczności rozrywającania.
- **Przepustowość docelowa dysku systemu operacyjnego**: limit przepływności, który może być osiągnięty przez dysk systemu operacyjnego bez konieczności rozrywającania.
- Liczba operacji we **/wy na dysku danych**: liczba operacji we/wy na sekundę, jaką mogą osiągnąć dyski z danymi bez konieczności rozerwania.
- Liczba operacji we **/wy dysku systemu operacyjnego**: liczba operacji we/wy na sekundę, jaką mogą osiągnąć dyski z danymi bez konieczności rozerwania.
- **Procent kredytów na dysk danych używany** do przesyłania w BPS: skumulowany procentowy przepływności używany dla dysków danych. Emitowane w przedziale 5 minut.
- **Procent kredytów z użyciem dysku systemu operacyjnego (%)**: skumulowana wartość procentowa serii przepływności używana dla dysku systemu operacyjnego. Emitowane w przedziale 5 minut.
- **Wartość procentowa wyrażona w procentach liczba operacji we/wy na dysku danych**: skumulowana wartość procentowa serii IOPS użyta na dyskach danych. Emitowane w przedziale 5 minut.
- Na **dysku systemu operacyjnego jest używana wartość procentowa kredytów we/wy**: skumulowana wartość procentowa serii IOPS używana dla dysku systemu operacyjnego. Emitowane w przedziale 5 minut.

## <a name="storage-io-utilization-metrics"></a>Metryki wykorzystania operacji we/wy magazynu
Następujące metryki pomagają zdiagnozować wąskie gardło w maszynie wirtualnej i w kombinacji dysków. Te metryki są dostępne tylko w przypadku korzystania z maszyny wirtualnej z włączoną obsługą Premium. Te metryki są dostępne dla wszystkich typów dysków z wyjątkiem Ultra. 

Metryki pomagające zdiagnozować limitów operacji we/wy dysku:

- **Procent zużywanych operacji we/wy dysku danych**: procent obliczony przez operacje we/wy dysku danych wykonanych przez operacje we/wy na dysku danych zainicjowanych. Jeśli ta kwota wynosi 100%, aplikacja jest uruchomiona w ramach limitu liczby operacji we/wy na dysku danych.
- **Procent zużywanej przepustowości dysku danych**: procent obliczony przez przepływność dysku danych w ramach przepływności dysku danych z zainicjowaną obsługą. Jeśli ta kwota wynosi 100%, aplikacja jest uruchomiona we/wy z ograniczenia przepustowości dysku danych.
- **Procent zużytych operacji we/wy dysku systemu operacyjnego**: procent obliczony przez operacje we/wy dysku systemu operacyjnego wykonany przez operacje we/wy na dysku systemu operacyjnego. Jeśli ta kwota ma wartość 100%, uruchomiona aplikacja ma limit operacji we/wy na dysku systemu operacyjnego.
- **Procent zużytej przepustowości dysku systemu operacyjnego**: wartość procentowa obliczona przez przepływność dysku systemu operacyjnego została zakończona przez zainicjowaną przepływność dysku systemu operacyjnego. Jeśli ta kwota wynosi 100%, aplikacja jest uruchomiona w ramach limitu przepustowości dysku systemu operacyjnego.

Metryki pomagające zdiagnozować limitów operacji we/wy maszyny wirtualnej:

- **Procent zużytych operacji we/wy w pamięci podręcznej maszyny** wirtualnej: procent obliczony przez łączną liczbę IOPS zakończonych przez maksymalną liczbę IOPS w pamięci podręcznej Jeśli ta kwota wynosi od 100%, aplikacja jest uruchomiona we/wy ograniczonego limitu operacji wejścia/wyjścia pamięci maszyny wirtualnej.
- **Procent zajętej pamięci podręcznej maszyny wirtualnej**: procent obliczony przez łączną przepływność dysku ukończoną przez maksymalną przepływność maszyny wirtualnej w pamięci podręcznej. Jeśli ta kwota wynosi 100%, aplikacja jest uruchomiona we/wy ograniczonego limitu przepustowości w pamięci podręcznej maszyny wirtualnej.
- **Procent zużytych operacji we/wy pamięci podręcznej maszyny** wirtualnej: procent obliczony przez łączną liczbę operacji we/wy na maszynach wirtualnych zakończonych maksymalną liczbą IOPS niebuforowanej maszyny wirtualnej. Jeśli ta kwota wynosi od 100%, aplikacja jest uruchomiona w ramach operacji we/wy z limitu liczby IOPS niebuforowanej maszyny wirtualnej.
- **Procent wykorzystania przepustowości niebuforowanej przez maszynę** wirtualną: procent obliczony przez łączną przepływność dysku maszyny wirtualnej, która została zakończona przez maksymalną zainicjowaną przepływność maszyny wirtualnej. Jeśli ta kwota wynosi 100%, aplikacja jest uruchomiona w ramach operacji we/wy ograniczonej do limitu przepustowości niebuforowanej maszyny wirtualnej.

## <a name="storage-io-metrics-example"></a>Przykład metryk operacji we/wy magazynu

Uruchommy Przykładowo, jak korzystać z nowych metryk użycia operacji we/wy magazynu, aby pomóc nam w debugowaniu, gdzie wąskie gardło w naszym systemie. Konfiguracja systemu jest taka sama jak w poprzednim przykładzie, z wyjątkiem tego, że dołączony dysk systemu operacyjnego *nie* jest buforowany.

**Instalator**

- Standardowa_D8s_v3
  - Buforowane operacje we/wy: 16 000
  - Liczba IOPS niebuforowanych w pamięci podręcznej: 12 800
- Dysk systemu operacyjnego P30
  - OPERACJE WE/WY: 5 000
  - Buforowanie hosta: **wyłączone**
- Dwa dyski z danymi P30 × 2
  - OPERACJE WE/WY: 5 000
  - Buforowanie hosta: **Odczyt/zapis**
- Dwa dyski z danymi P30 × 2
  - OPERACJE WE/WY: 5 000
  - Buforowanie hosta: **wyłączone**

Uruchommy test porównawczy dla tej maszyny wirtualnej i kombinacji dysków, która tworzy działanie we/wy. Aby dowiedzieć się, jak testować we/wy magazynu na platformie Azure, zobacz [test porównawczy aplikacji na Azure Disk Storage](disks-benchmarks.md). W narzędziu testowego można zobaczyć, że połączenie maszyny wirtualnej i dysku można osiągnąć 22 800 operacji we/wy na sekundę:

![Zrzut ekranu przedstawiający wyjście z języka f i o pokazujące r = 22.8 k.](media/disks-metrics/utilization-metrics-example/fio-output.jpg)



Standard_D8s_v3 może osiągnąć łączną liczbę operacji we/wy 28 600. Korzystając z metryk, zapoznaj się z informacjami o tym, co się dzieje, i zidentyfikuj nasze wąskie gardła we/wy magazynu. W okienku po lewej stronie wybierz pozycję **metryki**:

![Zrzut ekranu przedstawiający metryki wyróżnione w okienku po lewej stronie.](media/disks-metrics/utilization-metrics-example/metrics-menu.jpg)

Najpierw przyjrzyjmy się tej metryki, która **korzysta z wartości procentowej buforowanej operacji we/wy na sekundę** :

![Zrzut ekranu przedstawiający wartość procentową wykorzystania operacji wejścia/wyjścia w pamięci podręcznej V M.](media/disks-metrics/utilization-metrics-example/vm-cached.jpg)

Ta Metryka informuje nas o tym, że jest używane 61% liczby operacji 16 000 we/wy na sekundę przydzielonej do buforowanych operacji we/wy na maszynie wirtualnej. Ten procent oznacza, że wąskie gardła we/wy magazynu nie jest z dyskami, które są buforowane, ponieważ nie jest o 100%. Teraz przyjrzyjmy się **wartości procentowej niebuforowanej operacji we/wy na sekundę** :

![Zrzut ekranu przedstawiający procent niebuforowanych operacji we/wy I O b.](media/disks-metrics/utilization-metrics-example/vm-uncached.jpg)

Ta Metryka jest równa 100%. Informuje nas, że są używane wszystkie liczby operacji we/wy 12 800 przypisane do niebuforowanych operacji we/wy na maszynie wirtualnej. Jednym ze sposobów rozwiązania tego problemu jest zmiana rozmiaru maszyny wirtualnej na większy rozmiar, który może obsłużyć dodatkowe operacje we/wy. Jednak przed wykonaniem tej czynności Przyjrzyjmy się dołączonemu dyskowi, aby dowiedzieć się, ile operacji na nich widzi. Sprawdź dysk systemu operacyjnego, sprawdzając **procent zużytych operacji we/wy dysku systemu operacyjnego**:

![Zrzut ekranu przedstawiający dysk O wartości procentowej zużytej przez 1 S.](media/disks-metrics/utilization-metrics-example/os-disk.jpg)

Ta Metryka informuje nas o tym, że jest używany około 90% liczby operacji 5 000 we/wy zainicjowanej dla tego dysku systemu operacyjnego P30. Ten procent oznacza brak wąskiego gardła na dysku systemu operacyjnego. Teraz sprawdźmy, czy dyski z danymi, które są dołączone do maszyny wirtualnej, są wyświetlane na podstawie procentowej liczby operacji we **/wy na dysku danych**:

![Zrzut ekranu przedstawiający procent wykorzystania dysku z danymi.](media/disks-metrics/utilization-metrics-example/data-disks-no-splitting.jpg)

Ta Metryka informuje nas o tym, że średnia liczba operacji we/wy zużywanych przez wszystkie podłączone dyski wynosi około 42%. Ta wartość procentowa jest obliczana na podstawie liczby operacji we/wy używanej przez dyski i nie są obsługiwane z pamięci podręcznej hosta. Przechodźmy do tej metryki, stosując *podział* na te metryki i dzieląc wartość według wartości LUN:

![Zrzut ekranu przedstawiający procent wykorzystania dysku danych z podziałem.](media/disks-metrics/utilization-metrics-example/data-disks-splitting.jpg)

Ta Metryka informuje nas o tym, że dyski danych dołączone do jednostek LUN 3 i 2 używają około 85% ich zainicjowanych operacji we/wy. Poniżej znajduje się Diagram przedstawiający wygląd operacji we/wy na podstawie architektury maszyn wirtualnych i dysków:

![Diagram przykładu metryk magazynu I O.](media/disks-metrics/utilization-metrics-example/metrics-diagram.jpg)

## <a name="next-steps"></a>Następne kroki

- [Przegląd metryk Azure Monitor](../azure-monitor/essentials/data-platform-metrics.md)
- [Wyjaśnienie agregacji metryk](../azure-monitor/essentials/metrics-aggregation-explained.md)
- [Tworzenie i wyświetlanie alertów metryk oraz zarządzanie nimi w usłudze Azure Monitor](../azure-monitor/alerts/alerts-metric.md)