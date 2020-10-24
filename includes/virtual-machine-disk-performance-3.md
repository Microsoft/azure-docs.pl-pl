---
title: dołączanie pliku
description: dołączanie pliku
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 10/12/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 8eff9da82fdfa5749fd1c2bc04652d5c8ce8dfd2
ms.sourcegitcommit: 59f506857abb1ed3328fda34d37800b55159c91d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2020
ms.locfileid: "92518079"
---
![Zrzut ekranu przedstawiający wyjście z języka f i o pokazujące r = 22.8 k.](media/vm-disk-performance/utilization-metrics-example/fio-output.jpg)

Standard_D8s_v3 może osiągnąć łączną liczbę operacji we/wy 28 600. Korzystając z metryk, zapoznaj się z informacjami o tym, co się dzieje, i zidentyfikuj nasze wąskie gardła we/wy magazynu. W okienku po lewej stronie wybierz pozycję **metryki**:

![Zrzut ekranu przedstawiający metryki wyróżnione w okienku po lewej stronie.](media/vm-disk-performance/utilization-metrics-example/metrics-menu.jpg)

Najpierw przyjrzyjmy się tej metryki, która **korzysta z wartości procentowej buforowanej operacji we/wy na sekundę** :

![Zrzut ekranu przedstawiający wartość procentową wykorzystania operacji wejścia/wyjścia w pamięci podręcznej V M.](media/vm-disk-performance/utilization-metrics-example/vm-cached.jpg)

Ta Metryka informuje nas o tym, że jest używane 61% liczby operacji 16 000 we/wy na sekundę przydzielonej do buforowanych operacji we/wy na maszynie wirtualnej. Ten procent oznacza, że wąskie gardła we/wy magazynu nie jest z dyskami, które są buforowane, ponieważ nie jest o 100%. Teraz przyjrzyjmy się **wartości procentowej niebuforowanej operacji we/wy na sekundę** :

![Zrzut ekranu przedstawiający procent niebuforowanych operacji we/wy I O b.](media/vm-disk-performance/utilization-metrics-example/vm-uncached.jpg)

Ta Metryka jest równa 100%. Informuje nas, że są używane wszystkie liczby operacji we/wy 12 800 przypisane do niebuforowanych operacji we/wy na maszynie wirtualnej. Jednym ze sposobów rozwiązania tego problemu jest zmiana rozmiaru maszyny wirtualnej na większy rozmiar, który może obsłużyć dodatkowe operacje we/wy. Jednak przed wykonaniem tej czynności Przyjrzyjmy się dołączonemu dyskowi, aby dowiedzieć się, ile operacji na nich widzi. Sprawdź dysk systemu operacyjnego, sprawdzając **procent zużytych operacji we/wy dysku systemu operacyjnego**:

![Zrzut ekranu przedstawiający dysk O wartości procentowej zużytej przez 1 S.](media/vm-disk-performance/utilization-metrics-example/os-disk.jpg)

Ta Metryka informuje nas o tym, że jest używany około 90% liczby operacji 5 000 we/wy zainicjowanej dla tego dysku systemu operacyjnego P30. Ten procent oznacza brak wąskiego gardła na dysku systemu operacyjnego. Teraz sprawdźmy, czy dyski z danymi, które są dołączone do maszyny wirtualnej, są wyświetlane na podstawie procentowej liczby operacji we **/wy na dysku danych**:

![Zrzut ekranu przedstawiający procent wykorzystania dysku z danymi.](media/vm-disk-performance/utilization-metrics-example/data-disks-no-splitting.jpg)

Ta Metryka informuje nas o tym, że średnia liczba operacji we/wy zużywanych przez wszystkie podłączone dyski wynosi około 42%. Ta wartość procentowa jest obliczana na podstawie liczby operacji we/wy używanej przez dyski, które nie są obsługiwane przez pamięć podręczną hosta. Przechodźmy do tej metryki, stosując *podział* na te metryki i dzieląc wartość według wartości LUN:

![Zrzut ekranu przedstawiający procent wykorzystania dysku danych z podziałem.](media/vm-disk-performance/utilization-metrics-example/data-disks-splitting.jpg)

Ta Metryka informuje nas o tym, że dyski danych dołączone do jednostek LUN 3 i 2 używają około 85% ich zainicjowanych operacji we/wy. Poniżej znajduje się Diagram przedstawiający wygląd operacji we/wy na podstawie architektury maszyn wirtualnych i dysków:

![Diagram przykładu metryk magazynu I O.](media/vm-disk-performance/utilization-metrics-example/metrics-diagram.jpg)
