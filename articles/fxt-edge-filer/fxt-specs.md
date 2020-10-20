---
title: Specyfikacja Microsoft Azure FXT Edge | Microsoft Docs
description: Dowiedz się więcej na temat specyfikacji fizycznych i środowiskowych na potrzeby sprzętu Microsoft Azure FXT Edge.
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: b1a172adb54593421ef56e09c131e0ddcaee9f6e
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92215828"
---
# <a name="azure-fxt-edge-filer-specifications"></a>Specyfikacje usługi Azure FXT Edge

W tym artykule opisano specyfikacje sprzętu dla węzłów sprzętowych usługi Azure FXT Edge. W ramach tej działalności trzy lub więcej węzłów są skonfigurowane w celu zapewnienia klastrowanego systemu pamięci podręcznej.

## <a name="hardware-specifications"></a>Specyfikacje sprzętowe

| Składnik | FXT 6600 | FXT 6400 |
|----------|-----------|-----------|
| Rdzenie procesora CPU |  16 | 16 |
| PAMIĘCI  | 1536 GB | 768 GB |
| Porty sieciowe | 6 x 25/10 GB + 2 x 1 GB | 6 x 25/10 GB + 2 x 1 GB |
| Pojemność dysków SSD interfejsu NVMe | 25,6 TB | 12,8 TB |

## <a name="drive-specifications"></a>Specyfikacje dysku

System ma dziesięć kieszeni na napędy, z których można uzyskać dostęp z przodu. Każdy wypełniony dysk ma etykietę po prawej stronie z informacjami o pojemności.

Numery dysków są drukowane w przestrzeni między dyskami. W systemie plików usługi Azure FXT Edge dysk 0 jest lewym górnym dyskiem, a dysk 1 jest bezpośrednio poniżej.

![Fotografia jednej kieszeni dysku twardego w obudowie FXT, pokazująca numery dysków i etykiety pojemności](media/fxt-drives-photo.png)

| Numery dysków    |  Zastosowanie   |  Specyfikacje |
|------------------|--------|-----------------|
| 0, 1             | System operacyjny     | DYSK SSD 480 GB SATA |
| 2, 3, 4, 5, 6, 7, 8, 9 | Dane   | FXT 6600:3,2 TB dysków SSD interfejsu NVMe <br> FXT 6400:1,6 TB dysków SSD interfejsu NVMe |

## <a name="dimensions-and-weight"></a>Wymiary i waga

Usługa Azure FXT Edge Fileer została zaprojektowana tak, aby mieściła się w standardowym 19 "stojaku sprzętowym i jest wysoka jednostka stojaka (1U).

<!-- 10x2.5 inches version -->

| Wymiary pliku            | Wartość                    |
|-----------------------------|--------------------------|
| Height                      | 42,8 mm (1,68 cala)    |
| Szerokość (w tym Ears stojaka) | 482,0 mm (18,97 cala)  |
| Szerokość — obudowa główna      | 434,0 mm (17,08 cala) |
| Głębokość — stojak Ears do tyłu obudowy głównej                   | 733,82 mm (29,61 cala) |
| Głębokość — stojak Ears do najbardziej tylnej protrusion                 | 772,67 mm (30,42 cala) |
| Głębokość — stojak Ears do najbardziej protrusionego frontu, bez żadnej osłony | 22,0 mm (0,87 cala)  |
| Głębokość — stojak Ears do najbardziej protrusionego frontu, z ramką    | 35,84 mm (1,41 cala) |

| Waga | Wartość |
|-----------------|----------------------|
| Waga węzła (bez pakowania, bez akcesoriów) | 40 funtów (18,1 kg) |
| Waga netto (bez opakowania, z uwzględnieniem akcesoriów) | 51 funtów (23,1 kg)|
| Waga brutto (w miarę wysłania, obejmuje wszystkie opakowania) |  64 funtów (29,0 kg) |

### <a name="shipping-dimensions"></a>Wymiary wysyłki

| Wymiar pakietu | Milimetrach | 15 |
|-------------------|-------------|--------|
| Height            | 311,2       | 12,25 " |
| Width             | 642,8       | 25,31 " |
| Długość            | 1 051,1     | 41,38 " |

## <a name="power-and-thermal-specifications"></a>Specyfikacje dotyczące mocy i termiczne

Ta sekcja zawiera klasyfikacje i pomiary dotyczące mocy w przypadku usługi Azure FXT Edge.

### <a name="nameplate-ratings"></a>Klasyfikacje Nameplate

| Nameplate klasyfikacje dla modeli serii FXT 6000 |
|----------------|
| 100 – 240V ~    |
| 10A-5A (X2)  |
| 50/60 Hz         |

<!-- matches the Dell regulatory label exactly -->

### <a name="power-and-thermal-measurements"></a>Pomiary mocy i termiczne

W węzłach usługi Azure FXT Edge używane są zmienne szybkości, dlatego moc zależy od temperatury i obciążenia. Maksymalne szybkości wentylatorów można osiągnąć w przypadku niektórych kombinacji dużych obciążeń i podwyższonych temperatury otoczenia.

Te wykresy zapewniają zużycie mocy i termiczne pomiary wyjściowe dla często używanych kombinacji częstotliwości napięcia.

| FXT 6600 moc temperatury pokojowej <br />(22 ° C, 71,6 ° F) | 100, 60 Hz | 120, 60 Hz | 208, 60 Hz | 230, 50 Hz | 240, 50 Hz |
|---------|---|---|---|---|---|
| Napięcie (V) | 100 | 120 | 208 | 230 | 240 |
| Częstotliwość (Hz) | 60 | 60 | 60 | 50 | 50 |
| Bieżący (A) | 5,02 | 4,16 |2,40 | 2,20 | 2,16 |
| Pozorna moc (VA) | 502 | 499 | 499 | 506 | 518|
| Współczynnik mocy | 0,99 | 0,99 |0,98 | 0,98 | 0,98 |
| Rzeczywista moc (W) | 497 |494 | 489 | 496 | 508 |
| Rozpraszanie termiczne (BTU/godz.) |1696 | 1686 | 1669 | 1692 | 1733 |

| FXT 6600 moc z maksymalną prędkością wentylatora | 100, 60 Hz | 120, 60 Hz | 208, 60 Hz | 230, 50 Hz | 240, 50 Hz |
|---------|---|---|---|---|---|
| Napięcie (V) | 100 |120 | 208 | 230 | 240|
| Częstotliwość (Hz) | 60 | 60 | 60 | 50 | 50 |
| Bieżący (A) | 5,98 | 5,01 | 2,81 | 2.55 | 2,48 |
| Pozorna moc (VA) | 598 | 601 | 584 | 587 | 595 |
| Współczynnik mocy | 0,99 | 0,99 | 0,98 | 0,98 | 0,98 |
| Rzeczywista moc (W) | 592 | 595 | 573 | 575 | 583 |
| Rozpraszanie termiczne (BTU/godz.) | 2020 |2031 | 1954 | 1961 | 1990 |

| FXT 6400 moc temperatury pokojowej <br />(22 ° C, 71,6 ° F) | 100, 60 Hz | 120, 60 Hz | 208, 60 Hz | 230, 50 Hz | 240, 50 Hz |
|---------|---|---|---|---|---|
| Napięcie (V) | 100 | 120 | 208 | 230 | 240 |
| Częstotliwość (Hz) |60 | 60 | 60 | 50 | 50 |
| Bieżący (A) | 4,63 | 3,86 | 2.24 | 2,04 | 1,94 |
| Pozorna moc (VA) | 463 | 463 | 466 | 469 | 466 |
| Współczynnik mocy | 0,99 | 0,99 | 0,98 | 0,98 | 0,98 |
| Rzeczywista moc (W) | 458 | 459 | 457 | 460 | 456 |
| Rozpraszanie termiczne (BTU/godz.) | 1564 | 1565 | 1558 | 1569 | 1557 |

| FXT 6400 moc z maksymalną prędkością wentylatora | 100, 60 Hz | 120, 60 Hz | 208, 60 Hz | 230, 50 Hz | 240, 50 Hz |
|---------|---|---|---|---|---|
| Napięcie (V) | 100 | 120 | 208 | 230 | 240 |
| Częstotliwość (Hz) | 60 | 60 | 60 | 50 | 50 |
| Bieżący (A) | 5,15 | 4,28 | 2,48 | 2,28 | 2,13 |
| Pozorna moc (VA) | 515 | 514 | 516 | 524 | 511 |
| Współczynnik mocy | 0,99 | 0,99 | 0,98 | 0,98 | 0,98 |
| Rzeczywista moc (W) | 510 | 508 | 506 | 514 | 501 |
| Rozpraszanie termiczne (BTU/godz.) | 1740 | 1735 | 1725 | 1753 | 1709 |

## <a name="environmental-requirements"></a>Wymagania dotyczące środowiska

Ta sekcja zawiera specyfikacje dotyczące środowiska otoczenia sprzętowego.

### <a name="temperature-and-humidity"></a>Temperatura i wilgotność

| Atrybut środowiska   | Zakres operacyjny                   | Zakres nieoperacyjny         |
|---------------------------|-----------------------------------|-----------------------------|
| Zakres temperatury otoczenia | 10 oC do 35 °C (50-86 °F)          | -40 °C do 65 °C (-40-149 °F) |
| Wilgotność względna | 10%-80% bez kondensacji          | 5%-95% bez kondensacji     |
| Maksymalny punkt Rosa         | 29 °C (84 °F)                       | 33 °C (91 °F)                 |
| Wysokości                  | do 3048 metrów (10 000 stóp), z zastrzeżeniem powyższej klasyfikacji temperatury | do 12 000 metrów (39 370 stóp) |

> [!NOTE]
> **Nieklasyfikacja temperatury wysokości:** Maksymalną temperaturę zmniejsza się o 1 c/300 m (1 °F/547 ft) powyżej 950 m (3 117 ft).

### <a name="airflow-shock-and-vibration"></a>Przepływ powietrza, wstrząsy i wibracje

| Atrybut         | Specyfikacja |
|-------------------|---------------|
| Przepływ powietrza                    | Przepływ powietrza systemu jest przedsunięty do tyłu. System musi działać z instalacją tylnej wydechu niskiego poziomu. |
| Wstrząsy, operacyjne         | 6 G przez 11 milisekund (przetestowane w 6 orientacjach) |
| Wstrząsy, niedziałające     | 71 G przez 2 milisekundy (przetestowane w 6 orientacjach) |
| Wibracje, operacyjne     | 0,26 G<sub>RMS</sub> 5 hz do 350 Hz         |
| Wibracje, niedziałające | 1,88 G<sub>RMS</sub> 10 Hz do 500 Hz przez 15 minut (wszystkie sześć stron przetestowanych)  |

## <a name="safety-regulation-compliance"></a>Zgodność z regulacjami bezpieczeństwa

Plik usługi Azure FXT Edge jest zgodny z wymienionymi przepisami.

| Kategoria       | Specyfikacja przepisów |
|----------------|--------------------------|
| Bezpieczeństwo ogólne | EN 60950-1:2006 + A1:2010 + a2:2013 + A11:2009 + A12:2011/IEC 60950-1:2005 ED2 + a1:2009 + a2:2013 <br>EN 62311:2008 |
| ELEKTROMAGNETYCZNEJ            | FCC A, ICES-003  <br>EN 55032:2012/CISPR 32:2012  <br>EN 55032:2015/CISPR 32:2015  <br>EN 55024:2010 + A1:2015/CISPR 24:2010 + A1:2015  <br>EN 61000-3-2:2014/IEC 61000-3-2:2014 (Klasa D)   <br>EN 61000-3-3:2013/IEC 61000-3-3:2013 |
| Energetyczna         | Rozporządzenie Komisji (UE) nr. 617/2013  |
| RoHS           |    EN 50581:2012   |
