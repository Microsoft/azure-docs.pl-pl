---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines
author: albecker1
ms.service: virtual-machines
ms.topic: include
ms.date: 09/25/2020
ms.author: albecker1
ms.custom: include file
ms.openlocfilehash: 4770ac0181c64ef800aa02ba87284c8add357e36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92518078"
---
Ten artykuł pomaga w wyjaśnieniu wydajności dysku i sposobie jego działania w przypadku łączenia usługi Azure Virtual Machines i dysków platformy Azure. Opisano w nim również, jak można zdiagnozować wąskie gardła dla operacji we/wy dysku oraz zmiany, które można zoptymalizować pod kątem wydajności.

## <a name="how-does-disk-performance-work"></a>Jak działa wydajność dysku?
Usługa Azure Virtual Machines ma limity wydajności operacji wejścia/wyjścia na sekundę (IOPS) i przepływności na podstawie typu i rozmiaru maszyny wirtualnej. Dyski systemu operacyjnego i dyski danych można dołączać do maszyn wirtualnych. Dyski mają własne limity IOPS i przepływności.

Wydajność aplikacji jest ograniczona, gdy żąda większej liczby operacji we/wy na sekundę, niż to, co jest przeznaczone dla maszyn wirtualnych lub dołączonych dysków. Gdy jest to ograniczone, aplikacja działa w nieoptymalny sposób. Może to prowadzić do negatywnych skutków, takich jak zwiększone opóźnienia. Uruchommy kilka przykładów, aby wyjaśnić ten pomysł. Aby skorzystać z tych przykładów, zobaczmy tylko liczby IOPS. Jednak taka sama logika dotyczy przepływności.

## <a name="disk-io-capping"></a>Ograniczenia operacji we/wy dysku

**Instalator**

- Standardowa_D8s_v3
  - Liczba IOPS niebuforowanych w pamięci podręcznej: 12 800
- Dysk systemu operacyjnego E30
  - OPERACJE WE/WY: 500
- Dwa dyski z danymi E30 × 2
  - OPERACJE WE/WY: 500

![Diagram przedstawiający przetworzenie pułapek na poziomie dysku.](media/vm-disk-performance/disk-level-throttling.jpg)

Aplikacja uruchamiana na maszynie wirtualnej wykonuje żądanie, które wymaga 10 000 operacji wejścia/wyjścia do maszyny wirtualnej. Wszystkie z nich są dozwolone przez maszynę wirtualną, ponieważ maszyna wirtualna Standard_D8s_v3 może wykonać maksymalnie 12 800 operacji we/wy na sekundę.

Żądania liczby operacji we/wy 10 000 są podzielone na trzy różne żądania do różnych dysków:

- do dysku systemu operacyjnego zażądano 1 000 operacji wejścia/wyjścia.
- Liczba operacji we/wy 4 500 dla każdego dysku danych jest wymagana.

Wszystkie dołączone dyski są dyskami E30 i mogą obsługiwać tylko 500 operacji we/wy na sekundę. Tak więc reagują na 500 operacji we/wy na sekundę. Wydajność aplikacji jest ograniczona przez dołączone dyski i może przetwarzać tylko 1 500 operacji we/wy na sekundę. Aplikacja może korzystać z wydajności szczytowej przy 10 000 operacji we/wy, jeśli używane są lepsze dyski, takie jak SSD w warstwie Premium dysków P30.

## <a name="virtual-machine-io-capping"></a>Limit operacji we/wy maszyny wirtualnej

**Instalator**

- Standardowa_D8s_v3
  - Liczba IOPS niebuforowanych w pamięci podręcznej: 12 800
- Dysk systemu operacyjnego P30
  - OPERACJE WE/WY: 5 000
- Dwa dyski z danymi P30 × 2
  - OPERACJE WE/WY: 5 000

![Diagram przedstawiający pułapek na poziomie maszyny wirtualnej.](media/vm-disk-performance/vm-level-throttling.jpg)

Aplikacja uruchamiana na maszynie wirtualnej wykonuje żądanie, które wymaga 15 000 operacji we/wy na sekundę. Niestety, Standard_D8s_v3 maszyna wirtualna jest obsługiwana tylko w celu obsługi liczby operacji we/wy 12 800. Ta aplikacja jest ograniczona przez limity maszyny wirtualnej i musi przydzielić przydzieloną liczbę operacji we/wy 12 800.

Żądania liczby IOPS 12 800 są podzielone na trzy różne żądania do różnych dysków:

- do dysku systemu operacyjnego zażądano 4 267 operacji wejścia/wyjścia.
- Liczba operacji we/wy 4 266 dla każdego dysku danych jest wymagana.

Wszystkie dołączone dyski są dyskami P30, które mogą obsłużyć 5 000 operacji we/wy na sekundę. W związku z tym reagują na zwroty od ich żądanych ilości.
