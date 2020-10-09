---
title: StorSimple specyfikacje techniczne | Microsoft Docs
description: Opisuje specyfikacje techniczne i informacje dotyczące zgodności ze standardami dotyczącymi składników sprzętowych StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 06/02/2017
ms.author: alkohli
ms.openlocfilehash: 061194422a8c1bc449dbef0c4f04bb8e1db10dea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "68965285"
---
# <a name="technical-specifications-and-compliance-for-the-storsimple-device"></a>Specyfikacje techniczne i zgodność dla urządzenia StorSimple

## <a name="overview"></a>Omówienie

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

Składniki sprzętowe urządzenia Microsoft Azure StorSimple są zgodne ze specyfikacjami technicznymi i standardami prawnymi opisanymi w tym artykule. Specyfikacje techniczne opisują moduły mocy i chłodzenia (PCMs), stacje dysków, pojemność magazynu i obudowy. Informacje o zgodności obejmują takie rzeczy jak standardy międzynarodowe, bezpieczeństwo i emisje oraz okablowanie.

## <a name="power-and-cooling-module-specifications"></a>Specyfikacje modułu dotyczącego mocy i chłodzenia

Urządzenie StorSimple ma dwa 100-240y podwójne wentylatory, zgodne z SBBmi moduły chłodzenia prądem (PCMs). Zapewnia to nadmiarową konfigurację zasilacza. Jeśli moduł PCM ulegnie awarii, urządzenie będzie nadal działać normalnie w innym module PCM do momentu zamienienia modułu, który zakończył się niepowodzeniem.

Obudowa EBOD używa 580 W module PCM, a Podstawowa obudowa używa 764 W module PCM. W poniższych tabelach przedstawiono specyfikacje techniczne skojarzone z PCMs.

| Specyfikacja | 580 W MODULE PCM (EBOD) | 764 W module PCM (podstawowy) |
| --- | --- | --- |
| Maksymalna moc wyjściowa |580 W |764 |
| Częstotliwość |50/60 Hz |50/60 Hz |
| Wybór zakresu napięcia |Wybór autowielkości: 90 – 264 V AC, 47/63 Hz |Wybór autowielkości: 90-264 V AC, 47/63 Hz |
| Maksymalna inrush |20 A |20 A |
| Korekcja współczynnika mocy |>95% znamionowy napięcia Wejścia |>95% znamionowy napięcia Wejścia |
| Harmoniczne |Spełnia wymagania EN61000-3-2 |Spełnia wymagania EN61000-3-2 |
| Dane wyjściowe |5V z napięciem gotowości \@ 2,0 A |5V z napięciem gotowości \@ 2,7 A |
| + 5V \@ 42 A |+ 5V \@ 40 A | |
| + \@ 38 |+ \@ 38 | |
| Możliwość podłączenia gorąca |Tak |Tak |
| Przełączniki i diody LED |Przełącznik WŁĄCZania/wyłączania zasilania i czterech diod LED wskaźnika stanu |Przełącznik WŁĄCZania/wyłączania zasilania i sześciu wskaźników stanu |
| Chłodzenie obudowy |Wentylator osiowy ze zmienną szybkością wentylatorów |Wentylator osiowy ze zmienną szybkością wentylatorów |

## <a name="power-consumption-statistics"></a>Statystyki zużycia mocy

W poniższej tabeli wymieniono typowe dane dotyczące zużycia mocy (rzeczywiste wartości mogą się różnić od opublikowanych) dla różnych modeli urządzeń StorSimple.

| Warunki | 240 V AC | 240 V AC | 240 V AC | 110 V AC | 110 V AC | 110 V AC |
| --- | --- | --- | --- | --- | --- | --- |
|  Wentylatory wolno, bezczynne dyski |1,45 A |0,31 kW |1057,76 BTU/godz. |3,19 A |0,34 kW |1160,13 BTU/godz. |
|  Wentylatory wolno, dostęp do dysków |1,54 A |0,33 kW |1126,01 BTU/godz. |3,27 A |0,36 kW |1228,37 BTU/godz. |
|  Wentylatory szybkie, bezczynne dyski, dwie PSUs zasilane |2,14 A |0,49 kW |1671,95 BTU/godz. |4,99 A |0,54 kW |1842,56 BTU/godz. |
|  Wentylatory szybkie, bezczynne dyski, jeden PSU zasilany jeden bezczynny |2,05 A |0,48 kW |1637,83 BTU/godz. |4,58 A |0,50 kW |1706,07 BTU/godz. |
|  Wentylatory szybkie, uzyskiwanie dostępu do dwóch PSUs |2,26 A |0,51 kW |1740,19 BTU/godz. |4,95 A |0,54 kW |1842,56 BTU/godz. |
|  Wentylatory szybkie, dostęp do dysków, jeden PSU zasilany jeden bezczynny |2,14 A |0,49 kW |1671,95 BTU/godz. |4,81 A |0,53 kW |1808,44 BTU/godz. |

## <a name="disk-drive-specifications"></a>Specyfikacje dysku

Urządzenie StorSimple obsługuje do 12 napędów SAS (Serial Attached SCSI) o rozmiarach 3,5. Rzeczywiste dyski mogą być kombinacją dysków półprzewodnikowych (dysków SSD) lub dysków twardych (HDD), w zależności od konfiguracji produktu. Miejsca na dysku 12 znajdują się w konfiguracji 3 i 4 przed obudową. Obudowa EBOD umożliwia przechowywanie dodatkowych dysków na innych dyskach. Są one zawsze HDD.

## <a name="storage-specifications"></a>Specyfikacje magazynu

Urządzenia StorSimple mają kombinację dysków twardych i dysków SSD dla 8100 i 8600. Łączna pojemność do użycia dla 8100 i 8600 jest około 15 TB i 38 TB odpowiednio. W poniższej tabeli przedstawiono szczegóły pojemności dysków SSD, dysku twardego i chmury w kontekście pojemności rozwiązania StorSimple.

| Model/pojemność urządzenia | 8100 | 8600 |
| --- | --- | --- |
| Liczba stacji dysków twardych (HDD) |8 |19 |
| Liczba dysków półprzewodnikowych (dysków SSD) |4 |5 |
| Pojemność pojedynczego dysku twardego |4 TB |4 TB |
| Pojemność jednego dysku SSD |400 GB |800 GB |
| Pojemność zapasowa |4 TB |4 TB |
| Użyteczna pojemność dysku twardego |14 TB |36 TB |
| Użyteczna pojemność dysków SSD |800 GB |2 TB |
| Łączna pojemność użyteczna * |~ 15 TB |~ 38 TB |
| Maksymalna pojemność rozwiązania (w tym chmura) |200 TB |500 TB |

<sup>* </sup>- *Łączna pojemność do użycia obejmuje pojemność danych, metadanych i buforów. Woluminy przypięte lokalnie można inicjować do 8,5 TB na urządzeniu 8100 lub do 22,5 TB na większym urządzeniu 8600. Aby uzyskać więcej informacji, przejdź do obszaru [StorSimple lokalnie przypięte woluminy](storsimple-8000-local-volume-faq.md).*

## <a name="enclosure-dimensions-and-weight-specifications"></a>Wymiary obudowy i specyfikacje wagi

W poniższych tabelach przedstawiono różne specyfikacje obudowy dla wymiarów i wag.

### <a name="enclosure-dimensions"></a>Wymiary obudowy

W poniższej tabeli przedstawiono wymiary obudowy w milimetrach i calach.

| Szaf | Milimetrach | 15 |
| --- | --- | --- |
| Height |87,9 |3,46 |
| Szerokość między montażem kołnierza |483 |19,02 |
| Szerokość w obrębie treści obudowy |443 |17,44 |
| Głębokość od przodu od początku do krańca treści obudowy |577 |22,72 |
| Głębokość od panelu operacji do najdalej nasilenia obudowy |630,5 |24,82 |
| Głębokość od montażu kołnierza do najdalej krańca obudowy |603 |23,74 |

### <a name="enclosure-weight"></a>Waga obudowy

W zależności od konfiguracji w pełni załadowana obudowa podstawowa może być ważona z 21 do 33 KGS i musi obsługiwać dwie osoby.

| Szaf | Waga |
| --- | --- |
| Maksymalna waga (zależy od konfiguracji) |30 kg – 33 kg |
| Puste (Brak zainstalowanych dysków) |21 – 23 kg |

## <a name="enclosure-environment-specifications"></a>Specyfikacje środowiska obudowy

W tej sekcji przedstawiono specyfikacje dotyczące środowiska obudowy. Ta kategoria obejmuje temperaturę, wilgotność, wysokość, wstrząs, wibracje, orientację, bezpieczeństwo i kompatybilność elektromagnetyczną (EMC).

### <a name="temperature-and-humidity"></a>Temperatura i wilgotność

| Szaf | Zakres temperatury otoczenia | Wilgotność względna | Maksymalny zbiornik mokry |
| --- | --- | --- | --- |
| Operacyjne |5 °C – 35 °C (41 °F-95 °F) |20%-80% bez kondensacji- |28 °C (82 °F) |
| Niedziałające |-40 °C-70 °C (40 °F-158 °F) |5%-100% bez kondensacji |29 °C (84 °F) |

### <a name="airflow-altitude-shock-vibration-orientation-safety-and-emc"></a>Przepływ powietrza, wysokość, wstrząsy, wibracje, Orientacja, bezpieczeństwo i EMC

| Szaf | Specyfikacje operacyjne |
| --- | --- |
| Przepływ powietrza |Przepływ powietrza systemu jest przedsunięty do tyłu. System musi być obsługiwany przy użyciu instalacji z niską ilością wylotu. Ciśnienie wsteczne utworzone przez drzwi stojaków i przeszkody nie powinno przekraczać 5 Pascalów (0,5 mm miernika wody). |
| Wysokość, operacyjna |-30 metrów do 3045 metrów (– 100 stóp do 10 000 stóp) z maksymalną temperaturą operacyjną, która została zastąpiona przez 5 °C powyżej 7000 stóp. |
| Wysokość, niedziałająca |-305 metrów do 12 192 metrów (-1 000 stóp do 40 000 stóp) |
| Wstrząsy, operacyjne |5g 10 ms 1/2 — sinus |
| Wstrząsy, niedziałające |30g 10 ms 1/2 — sinus |
| Wibracje, operacyjne |0.21 g RMS 5-500 Hz losowo |
| Wibracje, niedziałające |1.04 g RMS 2-200 Hz losowo |
| Wibracje, przemieszczenie |sinus 3G 2-200 Hz |
| Orientacja i zamontowanie |19 "montaż w stojaku (2 jednostki EIA) |
| Szyny stelażowe |Dopasowanie do minimalnego stojaka głębi 700 mm (31,50 cala) zgodnego z IEC 297 |
| Bezpieczeństwo i zatwierdzenia |CE i UL EN 61000-3, IEC 61000-3, UL 61000-3 |
| ELEKTROMAGNETYCZNEJ |EN55022 (CISPR-A), FCC A |

## <a name="international-standards-compliance"></a>Zgodność ze standardami międzynarodowymi

Urządzenie Microsoft Azure StorSimple jest zgodne z następującymi międzynarodowymi standardami:  

* CE — EN 60950-1
* Raport CB do IEC 60950-1
* UL i cUL do UL 60950-1

## <a name="safety-compliance"></a>Zgodność z bezpieczeństwem

Urządzenie Microsoft Azure StorSimple spełnia następujące klasyfikacje bezpieczeństwa:

* Zatwierdzenie typu produktu systemu: UL, cUL, CE
* Zgodność z bezpieczeństwem: UL 60950, IEC 60950, EN 60950

## <a name="emc-compliance"></a>Zgodność ze standardem EMC

Twoje urządzenie Microsoft Azure StorSimple spełnia następujące oceny EMC.

### <a name="emissions"></a>Poziom

Urządzenie jest zgodne ze standardem EMC w celu przeprowadzenia i wypromieniowania poziomów emisji.

* Przeprowadzono limity poziomów emisji: CFR 47 część 15B Class A EN55022 klasy a CISPR klasy A
* Poziomy limitu wypromieniowanych emisji: CFR 47 część 15B Class A EN55022 klasy A CISPR klasy A

### <a name="harmonics-and-flicker"></a>Harmoniczne i migotanie

Urządzenie jest zgodne z EN61000-3-2/3.

### <a name="immunity-limit-levels"></a>Poziomy limitu odporności

Urządzenie jest zgodne z EN55024.

## <a name="ac-power-cord-compliance"></a>Zgodność zasilania AC

Zestaw Plug and Complete przewód zasilający muszą spełniać standardy odpowiednie dla kraju/regionu, w którym urządzenie jest używane, i muszą mieć zatwierdzenie w zakresie bezpieczeństwa, które są akceptowane w tym kraju/regionie. W poniższych tabelach przedstawiono standardy dla Stanów Zjednoczonych i Europy.

### <a name="ac-power-cords---usa-must-be-nrtl-listed"></a>Przewód zasilający AC-USA (musi być NRTL na liście)

| Składnik | Specyfikacja |
| --- | --- |
| Typ przewodu |OHR lub SVT, 18 AWG minimum, 3-przewodowe, 2,0 metrów maksymalnej długości |
| Wtyczki |NEMA 5-15Ping-plug-in-Type załącznika znamionowego 120 V, 10 A; lub IEC 320 C14, 250 V, 10 A |
| Używając |IEC 320 C-13, 250 V, 10 A |

### <a name="ac-power-cords---europe"></a>Przewód zasilający — Europa

| Składnik | Specyfikacja |
| --- | --- |
| Typ przewodu |Zharmonizowane, H05-VVF-3G 1.0 |
| Używając |IEC 320 C-13, 250 V, 10 A |

## <a name="supported-network-cables"></a>Obsługiwane kable sieciowe

W przypadku interfejsów sieciowych 10 GbE, danych 2 i danych 3 zapoznaj się z [listą obsługiwanych kabli i modułów sieciowych](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).

## <a name="next-steps"></a>Następne kroki

Teraz możesz przystąpić do wdrażania urządzenia StorSimple w centrum danych. Aby uzyskać więcej informacji, zobacz [wdrażanie urządzenia lokalnego](storsimple-8000-deployment-walkthrough-u2.md).

