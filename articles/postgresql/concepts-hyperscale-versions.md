---
title: Obsługiwane wersje — funkcja do skalowania (Citus) — Azure Database for PostgreSQL
description: Wersje PostgreSQL dostępne w Azure Database for PostgreSQL-(Citus)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: d8a584b6ba752e8f9220defa575f519828ba07e6
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107023991"
---
# <a name="supported-database-versions-in-azure-database-for-postgresql--hyperscale-citus"></a>Obsługiwane wersje bazy danych w Azure Database for PostgreSQL — funkcja Citus)

## <a name="postgresql-versions"></a>Wersje programu PostgreSQL

> [!IMPORTANT]
> Dostosowywalne wersje PostgreSQL w funkcji Citus) są obecnie dostępne w wersji zapoznawczej.  Ta wersja zapoznawcza jest dostępna bez umowy dotyczącej poziomu usług i nie jest zalecana w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
>
> Pełną listę innych nowych funkcji można znaleźć w artykule [dotyczącym funkcji Citus (wersja zapoznawcza)](hyperscale-preview-features.md).

Wersja PostgreSQL działająca w grupie serwerów w ramach funkcji wieloskalowania (Citus) jest dostosowywana podczas tworzenia. Wybór elementów innych niż 11 jest obecnie funkcją w wersji zapoznawczej.

Funkcja Citus) obecnie obsługuje następujące wersje główne:

### <a name="postgresql-version-13-preview"></a>PostgreSQL w wersji 13 (wersja zapoznawcza)

Bieżąca wersja pomocnicza to 13,2. Zapoznaj się z [dokumentacją PostgreSQL](https://www.postgresql.org/docs/13/static/release-13-2.html) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

### <a name="postgresql-version-12-preview"></a>PostgreSQL w wersji 12 (wersja zapoznawcza)

Bieżąca wersja pomocnicza to 12,6. Zapoznaj się z [dokumentacją PostgreSQL](https://www.postgresql.org/docs/12/static/release-12-6.html) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

### <a name="postgresql-version-11"></a>PostgreSQL, wersja 11

Bieżąca wersja pomocnicza to 11,11. Zapoznaj się z [dokumentacją PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-11.html) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

### <a name="postgresql-version-10-and-older"></a>PostgreSQL wersja 10 i starsze

Nie obsługujemy PostgreSQL w wersji 10 i starszej dla Azure Database for PostgreSQL-(Citus).

## <a name="citus-and-other-extension-versions"></a>Citus i inne wersje rozszerzeń

W zależności od wersji programu PostgreSQL, która jest uruchomiona w grupie serwerów, również zostaną zainstalowane różne [wersje rozszerzeń Postgres](concepts-hyperscale-extensions.md) .  W szczególności Postgres 13 jest dostarczany z Citus 10 i wcześniejszymi wersjami Postgres z Citus 9,5.

## <a name="next-steps"></a>Następne kroki

* Zobacz, które [rozszerzenia](concepts-hyperscale-extensions.md) są zainstalowane, w których wersjach.
* Zapoznaj się z tematem [Tworzenie grupy serwerów Citus](quickstart-create-hyperscale-portal.md).
