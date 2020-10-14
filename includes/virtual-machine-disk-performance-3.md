---
title: plik dołączany
description: plik dołączany
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 10/12/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 14e74bfbcd087ccc1d8c5f2f10a8e44ed37cce84
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92016550"
---
![Menu metryk](media/vm-disk-performance/utilization-metrics-example/fio-output.jpg)

Jednak Standard_D8s_v3 może osiągnąć łączną liczbę operacji we/wy 28 600, przy użyciu metryk pozwala na zbadanie tego, co się dzieje, i zidentyfikowanie wąskiej przestrzeni dyskowej magazynu. Najpierw znajdź pozycję Metryka menu po lewej stronie i wybierz ją:

![Menu metryk](media/vm-disk-performance/utilization-metrics-example/metrics-menu.jpg)

Najpierw przyjrzyjmy się tej metryki, która **korzysta z wartości procentowej buforowanej operacji we/wy na sekundę** :

![Procent zużytych operacji wejścia/wyjścia w pamięci maszyny wirtualnej](media/vm-disk-performance/utilization-metrics-example/vm-cached.jpg)

Ta Metryka informuje nas o liczbie operacji we/wy 16 000, która została przydzielona do buforowanych operacji we/wy na maszynie wirtualnej, w której jest używany 61%. Oznacza to, że wąskie gardło we/wy magazynu nie znajduje się w pamięci podręcznej, ponieważ nie jest o 100%. Teraz przyjrzyjmy się niebuforowanej liczbie operacji we **/wy wykorzystanej przez maszynę wirtualną** :

![Procent zużytych operacji we/wy pamięci podręcznej maszyny wirtualnej](media/vm-disk-performance/utilization-metrics-example/vm-uncached.jpg)

Ta Metryka jest równa 100%, co oznacza, że wszystkie liczby operacji 12 800 we/wy na sekundę przydzielone do niebuforowanych operacji we/wy na maszynie wirtualnej są używane. Jednym ze sposobów rozwiązania tego rozwiązania jest zmiana rozmiaru naszej maszyny wirtualnej na większy rozmiar, który może obsłużyć dodatkowe operacje we/wy. Jednak przed wykonaniem tej czynności Przyjrzyjmy się dyskowi dołączonemu, aby zobaczyć liczbę IOPs, które widzi. Najpierw zapoznaj się z dyskiem systemu operacyjnego, sprawdzając **procent wykorzystania operacji we/wy dysku systemu operacyjnego**:

![Procent zużytych operacji we/wy dysku systemu operacyjnego](media/vm-disk-performance/utilization-metrics-example/os-disk.jpg)

Ta Metryka informuje nas o liczbie operacji we/wy 5 000 dla tego dysku systemu operacyjnego P30, około 90% użycia. Oznacza to, że na dysku systemu operacyjnego nie ma żadnego wąskiego gardła. Teraz przyjrzyjmy się dyskom z danymi, które są dołączone do maszyny wirtualnej, sprawdzając **procent użytych operacji we/wy na dysku danych**:

![Procent zużytych operacji we/wy dysku danych](media/vm-disk-performance/utilization-metrics-example/data-disks-no-splitting.jpg)

Ta Metryka informuje nas o tym, że średnia liczba operacji we/wy zużywanych przez wszystkie podłączone dyski wynosi około 42%. Ta wartość procentowa jest obliczana na podstawie liczby operacji we/wy używanej przez dyski i nie są obsługiwane z pamięci podręcznej hosta. Przechodźmy szczegółowo do tej metryki, aby zobaczyć, stosując **podział** na te metryki i dzieląc wartość według wartości LUN:

![Procent zużytych operacji we/wy dysku danych z podziałem](media/vm-disk-performance/utilization-metrics-example/data-disks-splitting.jpg)

Ta Metryka informuje nas, że dyski danych dołączone do jednostek LUN 3 i 2 są używane około 85% ich zainicjowanej liczby operacji we/wy na sekundę. Poniżej znajduje się Diagram przedstawiający wygląd operacji we/wy na podstawie architektury maszyn wirtualnych i dysków:

![Przykładowy diagram metryk operacji we/wy magazynu](media/vm-disk-performance/utilization-metrics-example/metrics-diagram.jpg)
