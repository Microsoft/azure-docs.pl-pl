---
title: plik dołączany
description: plik dołączany
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 9f5a1010959658e75dcc809b2ee1d6d9222af056
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91540014"
---
Ten artykuł ułatwia wyjaśnienie wydajności dysku i sposobu działania programu podczas łączenia usługi Azure Virtual Machines i dysków platformy Azure. Opisano w nim również, jak można zdiagnozować wąskie gardła dla operacji we/wy dysku oraz zmiany, które można zoptymalizować pod kątem wydajności.

## <a name="how-does-disk-performance-work"></a>Jak działa wydajność dysku?
Usługa Azure Virtual Machines ma limity wydajności operacji we/wy i przepływności na podstawie typu i rozmiaru maszyny wirtualnej. Dyski systemu operacyjnego i dyski z danymi, które można dołączyć do maszyn wirtualnych, mają własne limity IOPs i przepływności. Gdy aplikacja działająca na maszynach wirtualnych żąda większej liczby operacji we/wy na sekundę, niż to, co jest przeznaczone dla maszyny wirtualnej lub dołączonych dysków, wydajność aplikacji jest ograniczona. W takim przypadku aplikacja będzie mieć nieoptymalną wydajność i może prowadzić do pewnych ujemnych skutków, takich jak zwiększone opóźnienia. Uruchommy kilka przykładów, aby zwiększyć to. Aby te przykłady były łatwe do obserwowania, zobaczymy tylko liczby IOPs, ale ta sama logika również dotyczy przepływności.

## <a name="disk-io-capping"></a>Ograniczenia operacji we/wy dysku
Konfiguracja:
- Standardowa_D8s_v3 
    - Liczba IOPS niebuforowanych w pamięci podręcznej: 12 800
- Dysk systemu operacyjnego E30
    - OPERACJE WE/WY: 500 
- 2 E30 dyski danych
    - OPERACJE WE/WY: 500

![Pułapek na poziomie dysku](media/vm-disk-performance/disk-level-throttling.jpg)

Aplikacja uruchamiana na maszynie wirtualnej wykonuje żądanie, które wymaga 10 000 operacji wejścia/wyjścia do maszyny wirtualnej. Wszystkie z nich są dozwolone przez maszynę wirtualną, ponieważ maszyna wirtualna Standard_D8s_v3 może wykonać maksymalnie 12 800 operacji we/wy na sekundę. Te żądania IOPs 10 000 są następnie podzielone na trzy różne żądania do różnych dysków. do dysku systemu operacyjnego zażądano 1 000 operacji wejścia/wyjścia na sekundę, a do każdego dysku danych są żądane żądania IOPs 4 500. Ponieważ wszystkie podłączone dyski są dyskami E30 i mogą obsługiwać tylko 500 operacji we/wy, reagują na żądania IOPs o identyfikatorze 500. Wydajność aplikacji jest następnie ograniczona przez dołączone dyski i może przetwarzać tylko 1 500 operacji we/wy na sekundę. Może działać z szczytową wydajnością przy 10 000 operacji we/wy na sekundę, jeśli lepsze są użycie dysków, takich jak SSD w warstwie Premium dysków P30.

## <a name="virtual-machine-io-capping"></a>Limit operacji we/wy maszyny wirtualnej
Konfiguracja:
- Standardowa_D8s_v3 
    - Liczba IOPS niebuforowanych w pamięci podręcznej: 12 800
- Dysk systemu operacyjnego P30
    - OPERACJE WE/WY: 5 000 
- 2 P30 dyski danych 
    - OPERACJE WE/WY: 5 000

![Pułapek na poziomie maszyny wirtualnej](media/vm-disk-performance/vm-level-throttling.jpg)

Aplikacja uruchamiana na maszynie wirtualnej wykonuje żądanie, które wymaga 15 000 operacji we/wy na sekundę. Niestety, Standard_D8s_v3 maszyna wirtualna jest obsługiwana tylko w celu obsługi liczby operacji we/wy 12 800. W takim przypadku aplikacja jest ograniczona przez limity maszyny wirtualnej, a następnie musi przydzielić przydzieloną liczbę operacji we/wy 12 800. Żądania liczby IOPs 12 800 są następnie podzielone na trzy różne żądania do różnych dysków. do dysku systemu operacyjnego zażądano 4 267 operacji wejścia/wyjścia na sekundę, a do każdego dysku danych są żądane żądania IOPs 4 266. Ponieważ wszystkie podłączone dyski są dyskami P30, które mogą obsłużyć 5 000 operacji we/wy na sekundę, reagują na ich żądane kwoty.