---
title: Limity i ograniczenia — skalowanie (Citus) — Azure Database for PostgreSQL
description: Bieżące limity dla grup serwerów Citus
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: cfd57c60c4bfd60976eb28822c696412cabda212
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023998"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-limits-and-limitations"></a>Limity Azure Database for PostgreSQL i ograniczenia (Citus)

W poniższej sekcji opisano możliwości i limity funkcjonalne w usłudze Citus.

## <a name="maximum-connections"></a>Maksymalna liczba połączeń

Każde połączenie PostgreSQL (nawet bezczynne) korzysta z co najmniej 10 MB pamięci, dlatego ważne jest ograniczenie jednoczesnych połączeń. Poniżej przedstawiono limity, które zostały wybrane, aby zachować kondycję węzłów:

* Węzeł koordynatora
   * Maksymalna liczba połączeń: 300
   * Maksymalna liczba połączeń użytkowników: 297
* Węzeł procesu roboczego
   * Maksymalna liczba połączeń: 600
   * Maksymalna liczba połączeń użytkowników: 597

> [!NOTE]
> W grupie serwerów z włączonymi [funkcjami w wersji zapoznawczej](hyperscale-preview-features.md) limity połączeń z koordynatorem są nieco inne:
>
> * Maksymalna liczba połączeń węzła koordynatora
>    * 300 dla 0-3 rdzeni wirtualnych
>    * 500 dla 4-15 rdzeni wirtualnych
>    * 1000 dla 16 + rdzeni wirtualnych

Próba nawiązania połączenia poza tymi limitami zakończy się niepowodzeniem z powodu błędu. System rezerwuje trzy połączenia dla węzłów monitorowania, co oznacza, że dla zapytań użytkowników jest dostępnych trzy mniejsze połączenia niż całkowita liczba połączeń.

### <a name="connection-pooling"></a>Buforowanie połączeń

Nawiązywanie nowych połączeń trwa. Działa to z większością aplikacji, które żądają wielu krótkich połączeń. Zalecamy używanie pulę połączeń, zarówno w celu ograniczenia bezczynnych transakcji, jak i ponownego użycia istniejących połączeń. Aby dowiedzieć się więcej, odwiedź nasz [wpis w blogu](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/not-all-postgres-connection-pooling-is-equal/ba-p/825717).

Możesz uruchomić własne połączenie pulę lub użyć PgBouncer zarządzanych przez platformę Azure.

#### <a name="managed-pgbouncer-preview"></a>Managed PgBouncer (wersja zapoznawcza)

> [!IMPORTANT]
> Zarządzane PgBouncer połączenia usługi pulę w ramach skalowania (Citus) jest obecnie w wersji zapoznawczej. Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
>
> Pełną listę innych nowych funkcji można znaleźć w artykule [dotyczącym funkcji Citus (wersja zapoznawcza)](hyperscale-preview-features.md).

Pule połączeń, takie jak PgBouncer, umożliwiają większej liczbie klientów łączenie się z węzłem koordynatora jednocześnie. Aplikacje łączą się z pulę i pulę przekazuje polecenia do docelowej bazy danych.

Gdy klienci łączą się za pomocą PgBouncer, liczba połączeń, które mogą aktywnie działać w bazie danych, nie ulega zmianie. Zamiast tego PgBouncer kolejki przekraczają połączenia i uruchamiają je, gdy baza danych jest gotowa.

Funkcja Citus) oferuje teraz zarządzane wystąpienie PgBouncer dla grup serwerów (w wersji zapoznawczej). Obsługuje do 2 000 jednoczesnych połączeń klientów.
Aby nawiązać połączenie za poorednictwem PgBouncer, wykonaj następujące kroki:

1. Przejdź do strony **Parametry połączenia** dla grupy serwerów w Azure Portal.
2. Włącz opcję CheckBox **PgBouncer parametry połączenia**. (Wymienione parametry połączenia zmienią się).
3. Zaktualizuj aplikacje klienckie, aby nawiązać połączenie z nowym ciągiem.

## <a name="storage-scaling"></a>Skalowanie magazynu

Magazyn w ramach koordynatora i węzłów procesu roboczego można skalować w górę, ale nie może być skalowany w dół (zmniejszył się).

## <a name="storage-size"></a>Rozmiar magazynu

Do 2 TiB magazynu jest obsługiwana na węzłach koordynator i proces roboczy. Zapoznaj się z dostępnymi opcjami magazynu [i obliczeniami](concepts-hyperscale-configuration-options.md#compute-and-storage) IOPS dla rozmiaru węzła i klastra.

## <a name="database-creation"></a>Tworzenie bazy danych

Azure Portal zawiera poświadczenia, aby połączyć się z dokładnie jedną bazą danych (Citus) grupą serwerów, `citus` bazę danych programu. Tworzenie innej bazy danych jest obecnie niedozwolone, a polecenie Utwórz bazę danych zakończy się niepowodzeniem z powodu błędu.

## <a name="columnar-storage"></a>Magazyn kolumnowy

Funkcja Moje skalowanie (Citus) ma obecnie te ograniczenia w [tabelach kolumnowych](concepts-hyperscale-columnar.md):

* Kompresja znajduje się na dysku, a nie w pamięci
* Tylko dołączanie (bez obsługi aktualizacji/usuwania)
* Brak odzyskiwania przestrzeni (na przykład transakcje wycofane mogą nadal zużywać miejsce na dysku)
* Brak obsługi indeksu, skanowania indeksów ani skanowania indeksów mapy bitowej
* Brak tidscans
* Brak skanowania przykładowego
* Brak pomocy technicznej (duże wartości są obsługiwane w tekście)
* Brak obsługi w przypadku instrukcji dotyczących konfliktu (z wyjątkiem braku akcji nie mających żadnego elementu docelowego).
* Brak obsługi blokad krotki (wybierz... W OBSZARZE UDOSTĘPNIJ WYBIERZ POZYCJĘ... FOR UPDATE)
* Brak obsługi dla możliwego do serializacji poziomu izolacji
* Obsługa programu PostgreSQL Server w wersji 12 +
* Brak obsługi kluczy obcych, ograniczeń unique lub ograniczeń wykluczenia
* Brak obsługi dekodowania logicznego
* Brak obsługi skanów równoległych wewnątrz węzła
* Brak obsługi dla po... DLA każdego wiersza wyzwalacza
* Brak zarejestrowanych tabel kolumnowy
* Brak tymczasowych tabel kolumnowych

## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak utworzyć grupę serwerów ze skalą (Citus) w portalu](quickstart-create-hyperscale-portal.md).
