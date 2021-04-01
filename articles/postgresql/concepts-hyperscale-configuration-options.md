---
title: Opcje konfiguracji — skalowanie (Citus) — Azure Database for PostgreSQL
description: Opcje grupy serwerów ze skalą (Citus), w tym obliczenia węzłów, magazyn i regiony.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 1/12/2021
ms.openlocfilehash: 48537483501165d4a978afdbd05560613170d187
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98165615"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>Opcje konfiguracji Azure Database for PostgreSQL — Citus)

## <a name="compute-and-storage"></a>Obliczenia i magazyn
 
Ustawienia obliczeń i magazynu można wybrać niezależnie dla węzłów procesu roboczego i węzła koordynatora w grupie serwerów Citus.  Zasoby obliczeniowe są udostępniane jako rdzeni wirtualnych, które reprezentują logicznego procesora bazowego sprzętu. Rozmiar magazynu dla aprowizacji odnosi się do pojemności dostępnej dla koordynatora i węzłów procesu roboczego w grupie serwerów Citus. Magazyn obejmuje pliki bazy danych, pliki tymczasowe, dzienniki transakcji i Dzienniki serwera Postgres.
 
| Zasób              | Węzeł procesu roboczego           | Węzeł koordynatora      |
|-----------------------|-----------------------|-----------------------|
| Obliczenia, rdzeni wirtualnych       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Pamięć na rdzeń wirtualny, GiB | 8                     | 4                     |
| Rozmiar magazynu, TiB     | 0,5, 1, 2             | 0,5, 1, 2             |
| Typ magazynu          | Ogólnego przeznaczenia (SSD) | Ogólnego przeznaczenia (SSD) |
| Liczba operacji we/wy na sekundę                  | Do 3 operacji we/wy na sekundę/GiB      | Do 3 operacji we/wy na sekundę/GiB      |

Całkowita ilość pamięci RAM w jednym węźle Citus jest oparta na wybranej liczbie rdzeni wirtualnych.

| Rdzeni wirtualnych | Jeden węzeł procesu roboczego, GiB pamięci RAM | Węzeł koordynatora, GiB pamięci RAM |
|--------|--------------------------|---------------------------|
| 4      | 32                       | 16                        |
| 8      | 64                       | 32                        |
| 16     | 128                      | 64                        |
| 32     | 256                      | 128                       |
| 64     | 432                      | 256                       |

Całkowita ilość dostępnego miejsca w magazynie definiuje również wydajność we/wy dostępną dla każdego węzła procesu roboczego i koordynatora.

| Rozmiar magazynu, TiB | Maksymalna liczba IOPS |
|-------------------|--------------|
| 0,5               | 1536        |
| 1                 | 3072        |
| 2                 | 6 148        |

W przypadku całego klastra z przedziałem (Citus) zagregowana liczba operacji we/wy na sekundę jest wykonywana na następujących wartościach:

| Węzły procesu roboczego | 0,5 TiB, całkowita liczba operacji we/wy | 1 TiB, całkowita liczba operacji we/wy | 2 TiB, całkowita liczba operacji we/wy |
|--------------|---------------------|-------------------|-------------------|
| 2            | 3072               | 6144             | 12 296            |
| 3            | 4 608               | 9 216             | 18 444            |
| 4            | 6144               | 12 288            | 24 592            |
| 5            | 7 680               | 15 360            | 30 740            |
| 6            | 9 216               | 18 432            | 36 888            |
| 7            | 10 752              | 21 504            | 43 036            |
| 8            | 12 288              | 24 576            | 49 184            |
| 9            | 13 824              | 27 648            | 55 332            |
| 10           | 15 360              | 30 720            | 61 480            |
| 11           | 16 896              | 33 792            | 67 628            |
| 12           | 18 432              | 36 864            | 73 776            |
| 13           | 19 968              | 39 936            | 79 924            |
| 14           | 21 504              | 43 008            | 86 072            |
| 15           | 23 040              | 46 080            | 92 220            |
| 16           | 24 576              | 49 152            | 98 368            |
| 17           | 26 112              | 52 224            | 104 516           |
| 18           | 27 648              | 55 296            | 110 664           |
| 19           | 29 184              | 58 368            | 116 812           |
| 20           | 30 720              | 61 440            | 122 960           |

## <a name="regions"></a>Regiony
Grupy serwerów ze skalowaniem (Citus) są dostępne w następujących regionach platformy Azure:

* Południowej
    * Kanada Środkowa
    * Central US
    * East US
    * Wschodnie stany USA 2
    * Północno-środkowe stany USA
    * Zachodnie stany USA 2
* Azja i Pacyfik:
    * Australia Wschodnia
    * Japonia Wschodnia
    * Korea Środkowa
    * Southeast Asia
* Terenie
    * Europa Północna
    * Południowe Zjednoczone Królestwo
    * West Europe

Niektóre z tych regionów mogą nie być początkowo aktywowane we wszystkich subskrypcjach platformy Azure. Jeśli chcesz użyć regionu z powyższej listy i nie widzisz go w Twojej subskrypcji, lub jeśli chcesz użyć regionu, którego nie ma na tej liście, Otwórz [żądanie pomocy technicznej](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="limits-and-limitations"></a>Limity i ograniczenia

W poniższej sekcji opisano możliwości i limity funkcjonalne w usłudze Citus.

### <a name="maximum-connections"></a>Maksymalna liczba połączeń

Każde połączenie PostgreSQL (nawet bezczynne) korzysta z co najmniej 10 MB pamięci, dlatego ważne jest ograniczenie jednoczesnych połączeń. Poniżej przedstawiono limity, które zostały wybrane, aby zachować kondycję węzłów:

* Węzeł koordynatora
   * Maksymalna liczba połączeń: 300
   * Maksymalna liczba połączeń użytkowników: 297
* Węzeł procesu roboczego
   * Maksymalna liczba połączeń: 600
   * Maksymalna liczba połączeń użytkowników: 597

Próba nawiązania połączenia poza tymi limitami zakończy się niepowodzeniem z powodu błędu. System rezerwuje trzy połączenia dla węzłów monitorowania, co oznacza, że dla zapytań użytkowników jest dostępnych trzy mniejsze połączenia niż całkowita liczba połączeń.

Nawiązywanie nowych połączeń trwa. Działa to z większością aplikacji, które żądają wielu krótkich połączeń. Zalecamy używanie pulę połączeń, zarówno w celu ograniczenia bezczynnych transakcji, jak i ponownego użycia istniejących połączeń. Aby dowiedzieć się więcej, odwiedź nasz [wpis w blogu](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

### <a name="storage-scaling"></a>Skalowanie magazynu

Magazyn w ramach koordynatora i węzłów procesu roboczego można skalować w górę, ale nie może być skalowany w dół (zmniejszył się).

### <a name="storage-size"></a>Rozmiar magazynu

Do 2 TiB magazynu jest obsługiwana na węzłach koordynator i proces roboczy. Zapoznaj się z dostępnymi opcjami magazynu [i obliczeniami](#compute-and-storage) IOPS dla rozmiaru węzła i klastra.

### <a name="database-creation"></a>Tworzenie bazy danych

Azure Portal zawiera poświadczenia, aby połączyć się z dokładnie jedną bazą danych (Citus) grupą serwerów, `citus` bazę danych programu. Tworzenie innej bazy danych jest obecnie niedozwolone, a polecenie Utwórz bazę danych zakończy się niepowodzeniem z powodu błędu.

## <a name="pricing"></a>Ceny
Najbardziej aktualne informacje o cenach można znaleźć na [stronie cennika](https://azure.microsoft.com/pricing/details/postgresql/)usługi.
Aby wyświetlić koszt dla wybranej konfiguracji, [Azure Portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) przedstawia miesięczny koszt na karcie **Konfiguracja** na podstawie wybranych opcji. Jeśli nie masz subskrypcji platformy Azure, możesz skorzystać z kalkulatora cen platformy Azure, aby uzyskać szacowaną cenę. W witrynie sieci Web [kalkulatora cen platformy Azure](https://azure.microsoft.com/pricing/calculator/) wybierz pozycję **Dodaj elementy**, rozwiń kategorię **bazy danych** , a następnie wybierz pozycję **Azure Database for PostgreSQL — Citus** , aby dostosować opcje.
 
## <a name="next-steps"></a>Następne kroki
Dowiedz się [, jak utworzyć grupę serwerów ze skalą (Citus) w portalu](quickstart-create-hyperscale-portal.md).
