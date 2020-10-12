---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 93f4f74d435cc14130668da102d1246c5fad5872
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183629"
---
| Identyfikator limitu | Limit | Komentarze |
| --- | --- | --- |
| Maksymalna liczba poświadczeń konta magazynu |64 | |
| Maksymalna liczba kontenerów woluminów |64 | |
| Maksymalna liczba woluminów |255 | |
| Maksymalna liczba harmonogramów na szablon przepustowości |168 |Harmonogram na każdą godzinę, każdy dzień tygodnia. |
| Maksymalny rozmiar woluminu warstwowego na urządzeniach fizycznych |64 TB dla StorSimple 8100 i StorSimple 8600 |StorSimple 8100 i StorSimple 8600 są urządzeniami fizycznymi. |
| Maksymalny rozmiar woluminu warstwowego na urządzeniach wirtualnych na platformie Azure |30 TB dla StorSimple 8010 <br></br> 64 TB dla StorSimple 8020 |StorSimple 8010 i StorSimple 8020 to urządzenia wirtualne na platformie Azure korzystające odpowiednio z magazynu w warstwie Standardowa i Premium. |
| Maksymalny rozmiar woluminu przypiętego lokalnie na urządzeniach fizycznych |9 TB dla StorSimple 8100 <br></br> 24 TB dla StorSimple 8600 |StorSimple 8100 i StorSimple 8600 są urządzeniami fizycznymi. |
| Maksymalna liczba połączeń iSCSI |512 | |
| Maksymalna liczba połączeń iSCSI z inicjatorów |512 | |
| Maksymalna liczba rekordów kontroli dostępu na urządzenie |64 | |
| Maksymalna liczba woluminów na zasady kopii zapasowej |24 | |
| Maksymalna liczba kopii zapasowych zachowywanych na zasady kopii zapasowej |64 | |
| Maksymalna liczba harmonogramów na zasady kopii zapasowych |10 | |
| Maksymalna liczba migawek dowolnego typu, które mogą być przechowywane na woluminie |256 |Ta kwota obejmuje migawki lokalne i migawki w chmurze. |
| Maksymalna liczba migawek, które mogą być obecne na dowolnym urządzeniu |10 000 | |
| Maksymalna liczba woluminów, które mogą być przetwarzane równolegle na potrzeby tworzenia kopii zapasowych, przywracania lub klonowania |16 |<ul><li>Jeśli istnieje więcej niż 16 woluminów, są one przetwarzane sekwencyjnie, ponieważ stają się dostępne.</li><li>Nie można wykonać nowych kopii zapasowych sklonowanego lub przywróconego woluminu warstwowego, dopóki operacja nie zostanie zakończona. W przypadku woluminu lokalnego kopie zapasowe są dozwolone, gdy wolumin jest w trybie online.</li></ul> |
| Przywracanie i klonowanie czasu odzyskiwania dla woluminów warstwowych |<2 minuty |<ul><li>Wolumin jest udostępniany w ciągu 2 minut od operacji przywracania lub klonowania, niezależnie od rozmiaru woluminu.</li><li>Wydajność woluminu może początkowo być wolniejsza niż normalne, ponieważ większość danych i metadanych nadal znajduje się w chmurze. Wydajność może wzrosnąć w miarę przepływów danych z chmury do urządzenia StorSimple.</li><li>Łączny czas pobierania metadanych zależy od przyznanego rozmiaru woluminu. Metadane są automatycznie wprowadzane do urządzenia w tle według stawki 5 minut na TB przydzielonej ilości danych woluminu. Ta stawka może mieć wpływ na przepustowość Internetu do chmury.</li><li>Operacja przywracania lub klonowania została ukończona, gdy wszystkie metadane są na urządzeniu.</li><li>Nie można wykonać operacji tworzenia kopii zapasowej, dopóki operacja przywracania lub klonowania nie zostanie w pełni zakończona. |
| Odtwórz czas odzyskiwania dla woluminów przypiętych lokalnie |<2 minuty |<ul><li>Wolumin jest udostępniany w ciągu 2 minut od operacji przywracania, niezależnie od rozmiaru woluminu.</li><li>Wydajność woluminu może początkowo być wolniejsza niż normalne, ponieważ większość danych i metadanych nadal znajduje się w chmurze. Wydajność może wzrosnąć w miarę przepływów danych z chmury do urządzenia StorSimple.</li><li>Łączny czas pobierania metadanych zależy od przyznanego rozmiaru woluminu. Metadane są automatycznie wprowadzane do urządzenia w tle według stawki 5 minut na TB przydzielonej ilości danych woluminu. Ta stawka może mieć wpływ na przepustowość Internetu do chmury.</li><li>W przeciwieństwie do woluminów warstwowych, jeśli woluminy są przypięte lokalnie, dane woluminu są również pobierane lokalnie na urządzeniu. Operacja przywracania została ukończona, gdy wszystkie dane woluminu zostały przeniesione na urządzenie.</li><li>Operacje przywracania mogą być długie i łączny czas ukończenia przywracania będzie zależeć od rozmiaru woluminu lokalnego, przepustowości Internetu i istniejących danych na urządzeniu. Operacje tworzenia kopii zapasowej na woluminie przypiętym lokalnie są dozwolone, gdy operacja przywracania jest w toku. |
| Dostępność cienkich i przywracania |Ostatni tryb failover | |
| Maksymalna przepływność odczytu/zapisu klienta, która jest obsługiwana z warstwy dysków SSD * |920/720 MB/s z pojedynczym interfejsem sieciowym Ethernet 10 GB |Maksymalnie dwa razy z wejściem do trybu MPIO i dwoma interfejsami sieciowymi. |
| Maksymalna przepływność odczytu/zapisu klienta, która jest obsługiwana z warstwy dysków twardych * |120/250 MB/s | |
| Maksymalna przepływność odczytu/zapisu klienta, gdy jest obsługiwany z warstwy chmury * |11/41 MB/s |Przepływność odczytu zależy od klientów generujących i utrzymujących wystarczającą głębokość kolejki we/wy. |

&#42;maksymalna przepływność na typ we/wy była mierzona przy użyciu scenariuszy zapisu 100% do odczytu i 100%. Rzeczywista przepływność może być niższa i zależy od warunków mieszanych i sieciowych we/wy.

