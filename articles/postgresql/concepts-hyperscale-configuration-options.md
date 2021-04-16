---
title: Opcje konfiguracji — Hiperskala (Citus) — Azure Database for PostgreSQL
description: Opcje dla grupy Hiperskala (Citus), w tym obliczenia węzłów, magazyn i regiony.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.custom: references_regions
ms.date: 04/07/2021
ms.openlocfilehash: 1dd0666c2946896ed324fb3986bb7946890b73de
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388707"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>Azure Database for PostgreSQL — Hiperskala (Citus) konfiguracji

## <a name="compute-and-storage"></a>Obliczenia i magazyn
 
Ustawienia obliczeń i magazynu można wybrać niezależnie dla węzłów procesu roboczego i węzła koordynatora w Hiperskala (Citus) grupy serwerów.  Zasoby obliczeniowe są udostępniane jako rdzenie wirtualne, które reprezentują logiczny procesor cpu bazowego sprzętu. Rozmiar magazynu do aprowowania odnosi się do pojemności dostępnej dla węzłów koordynatora i procesu roboczego w Hiperskala (Citus) grupy serwerów. Magazyn zawiera pliki bazy danych, pliki tymczasowe, dzienniki transakcji i dzienniki serwera Postgres.

### <a name="standard-tier"></a>Warstwa Standardowa
 
| Zasób              | Węzeł procesu roboczego           | Węzeł koordynatora      |
|-----------------------|-----------------------|-----------------------|
| Obliczenia, rdzenie wirtualne       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Pamięć na rdzeń wirtualnych, GiB | 8                     | 4                     |
| Rozmiar magazynu, TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| Typ magazynu          | Ogólnego przeznaczenia (SSD) | Ogólnego przeznaczenia (SSD) |
| Liczba operacji we/wy na sekundę                  | Do 3 IOPS/GiB      | Do 3 IOPS/GiB      |

Całkowita ilość pamięci RAM w jednym węźle Hiperskala (Citus) jest oparta na wybranej liczbie rdzeni wirtualnych.

| Rdzenie wirtualne | Jeden węzeł procesu roboczego, gib pamięci RAM | Węzeł koordynatora, gib pamięci RAM |
|--------|--------------------------|---------------------------|
| 4      | 32                       | 16                        |
| 8      | 64                       | 32                        |
| 16     | 128                      | 64                        |
| 32     | 256                      | 128                       |
| 64     | 432                      | 256                       |

Całkowita ilość aprowizatora magazynu definiuje również pojemność we/wy dostępną dla każdego węzła roboczego i koordynatora.

| Rozmiar magazynu, TiB | Maksymalna liczba IOPS |
|-------------------|--------------|
| 0,5               | 1536        |
| 1                 | 3072        |
| 2                 | 6,148        |

W całym klastrze Hiperskala (Citus) zagregowana wartość IOPS działa z następującymi wartościami:

| Węzły procesu roboczego | 0,5 TiB, łączna liczba IOPS | 1 TiB, łączna liczba IOPS | 2 TiB, łączna liczba IOPS |
|--------------|---------------------|-------------------|-------------------|
| 2            | 3072               | 6144             | 12,296            |
| 3            | 4,608               | 9,216             | 18,444            |
| 4            | 6144               | 12,288            | 24,592            |
| 5            | 7,680               | 15,360            | 30,740            |
| 6            | 9,216               | 18,432            | 36,888            |
| 7            | 10,752              | 21,504            | 43,036            |
| 8            | 12,288              | 24,576            | 49,184            |
| 9            | 13,824              | 27,648            | 55,332            |
| 10           | 15,360              | 30,720            | 61,480            |
| 11           | 16,896              | 33,792            | 67,628            |
| 12           | 18,432              | 36,864            | 73,776            |
| 13           | 19,968              | 39,936            | 79,924            |
| 14           | 21,504              | 43,008            | 86,072            |
| 15           | 23,040              | 46,080            | 92,220            |
| 16           | 24,576              | 49,152            | 98,368            |
| 17           | 26,112              | 52,224            | 104,516           |
| 18           | 27,648              | 55,296            | 110,664           |
| 19           | 29,184              | 58,368            | 116,812           |
| 20           | 30,720              | 61,440            | 122,960           |

### <a name="basic-tier-preview"></a>Warstwa Podstawowa (wersja zapoznawcza)

> [!IMPORTANT]
> Warstwa Hiperskala (Citus) podstawowa jest obecnie dostępna w wersji zapoznawczej.  Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
>
> Pełna lista innych nowych funkcji dostępnych w wersji zapoznawczej dla programu [Hiperskala (Citus)](hyperscale-preview-features.md).

Warstwa Hiperskala (Citus) [podstawowa](concepts-hyperscale-tiers.md) to grupa serwerów z tylko jednym węzłem.  Ponieważ nie ma rozróżnienia między węzłami koordynatora i węzłami procesu roboczego, wybieranie zasobów obliczeniowych i magazynowych jest mniej skomplikowane.

| Zasób              | Dostępne opcje     |
|-----------------------|-----------------------|
| Obliczenia, rdzenie wirtualne       | 2, 4, 8               |
| Pamięć na rdzeń wirtualnych, GiB | 4                     |
| Rozmiar magazynu, GiB     | 128, 256, 512         |
| Typ magazynu          | Ogólnego przeznaczenia (SSD) |
| Liczba operacji we/wy na sekundę                  | Do 3 IOPS/GiB      |

Całkowita ilość pamięci RAM w jednym węźle Hiperskala (Citus) jest oparta na wybranej liczbie rdzeni wirtualnych.

| Rdzenie wirtualne | GiB RAM |
|--------|---------|
| 2      | 8       |
| 4      | 16      |
| 8      | 32      |

Całkowita ilość aprowizywnego magazynu definiuje również pojemność we/wy dostępną dla węzła warstwy Podstawowa.

| Rozmiar magazynu, GiB | Maksymalna liczba IOPS |
|-------------------|--------------|
| 128               | 384          |
| 256               | 768          |
| 512               | 1536        |

## <a name="regions"></a>Regiony
Hiperskala (Citus) grupy serwerów są dostępne w następujących regionach świadczenia usługi Azure:

* Americas:
    * Brazylia Południowa
    * Kanada Środkowa
    * Central US
    * Wschodnie usa *
    * Wschodnie stany USA 2
    * Północno-środkowe stany USA
    * Zachodnie stany USA 2
* Azja i Pacyfik:
    * Australia Wschodnia
    * Japan East
    * Korea Środkowa
    * Southeast Asia
* Europie:
    * Francja Środkowa
    * Europa Północna
    * Południowe Zjednoczone Królestwo
    * West Europe

( \* = obsługuje funkcje w wersji [zapoznawczej](hyperscale-preview-features.md))

Niektóre z tych regionów mogą nie być początkowo aktywowane we wszystkich subskrypcjach platformy Azure. Jeśli chcesz użyć regionu z powyższej listy i nie widzisz go w swojej subskrypcji lub jeśli chcesz użyć regionu, którego nie ma na tej liście, otwórz wniosek o pomoc [techniczną.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="pricing"></a>Ceny
Aby uzyskać najbardziej aktualne informacje o cenach, zobacz stronę [cennika usługi](https://azure.microsoft.com/pricing/details/postgresql/).
Aby wyświetlić koszt dla chętnych konfiguracji, Azure Portal [przedstawia](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) miesięczny koszt na karcie Konfigurowanie na podstawie wybranych opcji.  Jeśli nie masz subskrypcji platformy Azure, możesz użyć kalkulatora cen platformy Azure, aby uzyskać szacowaną cenę. W [witrynie internetowej kalkulatora](https://azure.microsoft.com/pricing/calculator/) cen platformy  Azure wybierz pozycję Dodaj **elementy,** rozwiń kategorię Bazy danych, a następnie wybierz pozycję Azure Database for PostgreSQL **— Hiperskala (Citus),** aby dostosować opcje.
 
## <a name="next-steps"></a>Następne kroki
Dowiedz się, [jak utworzyć Hiperskala (Citus) grupy serwerów w portalu.](quickstart-create-hyperscale-portal.md)
