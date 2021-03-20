---
title: Limity systemu serii 8000 StorSimple Microsoft Docs
description: Opisuje limity systemowe i zalecane rozmiary dla składników i połączeń z serii StorSimple 8000.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: c7392678-0924-46c6-9c59-1665cb9b6586
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/28/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 70f2d9542082ddf7ecf1d1e7361b0ecdb14c5ef8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "68963387"
---
# <a name="what-are-storsimple-8000-series-system-limits"></a>Co to są limity systemu serii StorSimple 8000?

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Omówienie

StorSimple zapewnia skalowalny i elastyczny magazyn dla centrum danych. Istnieją jednak pewne ograniczenia, które należy wziąć pod uwagę podczas planowania, wdrażania i obsługi rozwiązania StorSimple. W poniższej tabeli opisano te limity i udostępniono pewne zalecenia, dzięki którym można maksymalnie wykorzystać rozwiązanie StorSimple.

| Identyfikator limitu | Limit | Komentarze |
| --- | --- | --- |
| Maksymalna liczba poświadczeń konta magazynu |64 | |
| Maksymalna liczba kontenerów woluminów |64 | |
| Maksymalna liczba woluminów |255 | |
| Maksymalna liczba przypiętych lokalnie woluminów |32 | |
| Maksymalna liczba harmonogramów na szablon przepustowości |168 |Harmonogram na każdą godzinę, każdy dzień tygodnia (24 * 7). |
| Maksymalny rozmiar woluminu warstwowego na urządzeniach fizycznych |64 TB dla 8100 i 8600 |8100 i 8600 są urządzeniami fizycznymi. |
| Maksymalny rozmiar woluminu warstwowego na urządzeniach wirtualnych na platformie Azure |30 TB dla 8010 <br></br> 64 TB dla 8020 |8010 i 8020 to urządzenia wirtualne na platformie Azure korzystające z magazynu w warstwie Standardowa i odpowiednio Premium Storage. |
| Maksymalny rozmiar woluminu przypiętego lokalnie na urządzeniach fizycznych |8,5 TB dla 8100 <br></br> 22,5 TB dla 8600 |8100 i 8600 są urządzeniami fizycznymi. |
| Maksymalna liczba połączeń iSCSI |512 | |
| Maksymalna liczba połączeń iSCSI z inicjatorów |512 | |
| Maksymalna liczba rekordów kontroli dostępu na urządzenie |64 | |
| Maksymalna liczba woluminów na zasady kopii zapasowej |20 | |
| Maksymalna liczba kopii zapasowych zachowywanych według harmonogramu (w ramach zasad tworzenia kopii zapasowej) |64 | |
| Maksymalna liczba harmonogramów na zasady kopii zapasowych |10 | |
| Maksymalna liczba migawek dowolnego typu, które mogą być przechowywane na woluminie |256 |Ta liczba obejmuje migawki lokalne i migawki w chmurze. |
| Maksymalna liczba migawek, które mogą być obecne na dowolnym urządzeniu |10 000 | |
| Maksymalna liczba woluminów, które mogą być przetwarzane równolegle na potrzeby tworzenia kopii zapasowych, przywracania lub klonowania |16 |<ul><li>Jeśli istnieje więcej niż 16 woluminów, są one przetwarzane sekwencyjnie, ponieważ stają się dostępne.</li><li>Nowe kopie zapasowe sklonowanego lub przywróconego woluminu warstwowego nie mogą wystąpić do momentu zakończenia operacji. Jednak w przypadku woluminu lokalnego kopie zapasowe są dozwolone, gdy wolumin jest w trybie online.</li></ul> |
| Przywracanie i klonowanie czasu odzyskiwania dla woluminów warstwowych |< 2 minuty |<ul><li>Wolumin jest udostępniany w ciągu 2 minut od operacji przywracania lub klonowania, niezależnie od rozmiaru woluminu.</li><li>Wydajność woluminu może początkowo być wolniejsza niż normalne, ponieważ większość danych i metadanych nadal znajduje się w chmurze. Wydajność może wzrosnąć w miarę przepływów danych z chmury do urządzenia StorSimple.</li><li>Łączny czas pobierania metadanych zależy od przyznanego rozmiaru woluminu. Metadane są automatycznie wprowadzane do urządzenia w tle według stawki 5 minut na TB przydzielonej ilości danych woluminu. Ta stawka może mieć wpływ na przepustowość Internetu do chmury.</li><li>Operacja przywracania lub klonowania została ukończona, gdy wszystkie metadane są na urządzeniu.</li><li>Nie można wykonać operacji tworzenia kopii zapasowej, dopóki operacja przywracania lub klonowania nie zostanie w pełni zakończona. |
| Odtwórz czas odzyskiwania dla woluminów przypiętych lokalnie |< 2 minuty |<ul><li>Wolumin jest udostępniany w ciągu 2 minut od operacji przywracania, niezależnie od rozmiaru woluminu.</li><li>Wydajność woluminu może początkowo być wolniejsza niż normalne, ponieważ większość danych i metadanych nadal znajduje się w chmurze. Wydajność może wzrosnąć w miarę przepływów danych z chmury do urządzenia StorSimple.</li><li>Łączny czas pobierania metadanych zależy od przyznanego rozmiaru woluminu. Metadane są automatycznie wprowadzane do urządzenia w tle według stawki 5 minut na TB przydzielonej ilości danych woluminu. Ta stawka może mieć wpływ na przepustowość Internetu do chmury.</li><li>W przeciwieństwie do woluminów warstwowych dla woluminów przypiętych lokalnie, dane woluminu są również pobierane lokalnie na urządzeniu. Operacja przywracania została ukończona, gdy wszystkie dane woluminu zostały przeniesione na urządzenie.</li><li>Operacje przywracania mogą być długie. Łączny czas ukończenia przywracania zależy od rozmiaru przyznanego woluminu lokalnego, przepustowości Internetu i istniejących danych na urządzeniu. Operacje tworzenia kopii zapasowej na woluminie przypiętym lokalnie są dozwolone, gdy operacja przywracania jest w toku. |
| Szybkość przetwarzania migawek w chmurze |15 minut/TB |<ul><li>Minimalny czas przygotowania migawki w chmurze do przekazania na TB przydzieloną ilość danych woluminu w kopii zapasowej. </li><li> Łączny czas migawek w chmurze jest obliczany przez dodanie tego czasu do czasu przekazywania migawki, na który ma wpływ przepustowość Internetu na chmurę. |
| Maksymalna przepływność odczytu/zapisu klienta (gdy jest obsługiwana z warstwy SSD) * |920/720 MB/s z pojedynczym interfejsem sieciowym 10 GbE |Do 2 z MPIO i dwa interfejsy sieciowe. |
| Maksymalna przepływność odczytu/zapisu klienta (gdy jest obsługiwana z warstwy dysków twardych) * |120/250 MB/s | |
| Maksymalna przepływność odczytu/zapisu klienta (w przypadku obsługi z warstwy chmury) * dla aktualizacji Update 3 i nowszych * * |40/60 MB/s dla woluminów warstwowych<br><br>60/80 MB/s dla woluminów warstwowych z opcją archiwizowania wybraną podczas tworzenia woluminu |Przepływność odczytu zależy od klientów generujących i utrzymujących wystarczającą głębokość kolejki we/wy. <br><br>Osiągnięta szybkość zależy od szybkości użytego bazowego konta magazynu. |

&#42; maksymalna przepływność na typ we/wy była mierzona przy użyciu scenariuszy zapisu 100% do odczytu i 100%. Rzeczywista przepływność może być niższa i zależy od warunków mieszanych i sieciowych we/wy.

&#42;&#42; liczbami wydajności przed aktualizacją 3 może być niższa.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z [wymaganiami systemowymi StorSimple](storsimple-8000-system-requirements.md).

