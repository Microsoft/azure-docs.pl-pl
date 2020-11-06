---
title: Zasady dotyczące obsługi wersji — Azure Database for PostgreSQL — pojedynczy serwer i elastyczny serwer (wersja zapoznawcza)
description: Zawiera opis zasad dotyczących Postgres wersji głównych i pomocniczych w Azure Database for PostgreSQL-pojedynczym serwerze.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/05/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: f92864cea4332157b0bf8b171a9d88b34f79a5ac
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93422114"
---
# <a name="azure-database-for-postgresql-versioning-policy"></a>Zasady dotyczące wersji Azure Database for PostgreSQL

Ta strona zawiera opis zasad dotyczących wersji Azure Database for PostgreSQL i ma zastosowanie do trybu wdrożenia serwera Azure Database for PostgreSQL i Azure Database for PostgreSQL-elastycznym (wersja zapoznawcza).

## <a name="supported--postgresql-versions"></a>Obsługiwane wersje PostgreSQL

Azure Database for PostgreSQL obsługuje następujące wersje bazy danych.

| Wersja | Pojedynczy serwer | Serwer elastyczny (wersja zapoznawcza) |
| ----- | :------: | :----: |
| PostgreSQL 12 |  | X  | 
| PostgreSQL 11 | X | X |
| PostgreSQL 10 | X |  |
| PostgreSQL 9,6 | X |  |
| PostgreSQL 9,5 | X |  |

## <a name="major-version-support"></a>Obsługa wersji głównej
Każda główna wersja PostgreSQL będzie obsługiwana przez Azure Database for PostgreSQL od daty, w której platforma Azure zacznie obsługiwać wersję, dopóki wersja nie zostanie wycofana przez społeczność PostgreSQL, zgodnie z zasadami dotyczącymi [wersji społeczności PostgreSQL](https://www.postgresql.org/support/versioning/).

## <a name="minor-version-support"></a>Obsługa wersji pomocniczej
Azure Database for PostgreSQL automatycznie przeprowadza uaktualnienia wersji pomocniczej do preferowanej wersji usługi Azure PostgreSQL w ramach okresowej konserwacji. 

## <a name="major-version-retirement-policy"></a>Zasady wycofywania wersji głównych
Poniższa tabela zawiera szczegóły wycofania dla wersji głównych PostgreSQL. Daty są zgodne z [zasadami wersji społeczności PostgreSQL](https://www.postgresql.org/support/versioning/).

| Wersja | Co nowego | Data rozpoczęcia pomocy technicznej platformy Azure | Data wycofania|
| ----- | ----- | ------ | ----- |
| PostgreSQL 9,5| [Funkcje](https://www.postgresql.org/docs/9.5/release-9-5.html)  | 18 kwietnia 2018 r.    | 11 lutego 2021
| [PostgreSQL 9,6](https://www.postgresql.org/about/news/postgresql-96-released-1703/) | [Funkcje](https://wiki.postgresql.org/wiki/NewIn96) | 18 kwietnia 2018 r.  | 11 listopada 2021
| [PostgreSQL 10](https://www.postgresql.org/about/news/postgresql-10-released-1786/) | [Funkcje](https://wiki.postgresql.org/wiki/New_in_postgres_10) | 4 czerwca 2018  | 10 listopada 2022
| [PostgreSQL 11](https://www.postgresql.org/about/news/postgresql-11-released-1894/) | [Funkcje](https://www.postgresql.org/docs/11/release-11.html) | 24 lipca 2019 r.  | 9 listopada, 2023
| [PostgreSQL 12](https://www.postgresql.org/about/news/postgresql-12-released-1976/) | [Funkcje](https://www.postgresql.org/docs/12/release-12.html) | Września 22, 2020  | 14 listopada 2024

## <a name="retired-postgresql-engine-versions-not-supported-in-azure-database-for-postgresql"></a>Wycofane wersje aparatu PostgreSQL nie są obsługiwane w Azure Database for PostgreSQL

Po upływie daty wycofania dla każdej wersji bazy danych PostgreSQL należy pamiętać o następujących ograniczeniach:
- Ponieważ Wspólnota nie będzie zwalniać żadnych dalszych poprawek lub poprawek zabezpieczeń, usługa Azure for PostgreSQL nie będzie w trakcie naprawiania nieaktualnego aparatu bazy danych pod kątem błędów lub problemów z zabezpieczeniami lub w inny sposób podejmować środki bezpieczeństwa w odniesieniu do wycofanego aparatu bazy danych. W związku z tym mogą wystąpić luki w zabezpieczeniach lub inne problemy. Jednak platforma Azure będzie kontynuowała okresowe konserwacje i stosowanie poprawek dla hosta, systemu operacyjnego, kontenerów i innych składników związanych z usługą.
- Jeśli dowolny problem z pomocą techniczną, który może się pojawić, odnosi się do bazy danych PostgreSQL, firma Microsoft może nie być w stanie zapewnić pomoc techniczną. W takich przypadkach trzeba będzie uaktualnić bazę danych, aby umożliwić nam zapewnienie pomocy technicznej.
- Nie będzie można tworzyć nowych serwerów baz danych dla wycofanej wersji. Można jednak przeprowadzić odzyskiwanie do punktu w czasie i utworzyć repliki odczytu dla istniejących serwerów.
- Nowe możliwości usługi opracowane przez Azure Database for PostgreSQL mogą być dostępne tylko dla obsługiwanych wersji serwera bazy danych.
- Czas przestoju umowy SLA będzie stosowany wyłącznie do Azure Database for PostgreSQL problemów związanych z usługą, a nie do przestojów spowodowanych błędami związanymi z aparatem bazy danych.  
- W skrajnym zdarzeniu poważnych zagrożeń dla usługi spowodowanej przez lukę w zabezpieczeniach aparatu bazy danych PostgreSQL zidentyfikowaną w wycofanej wersji bazy danych, platforma Azure może zrezygnować z użycia węzła obliczeniowego serwera bazy danych w celu zabezpieczenia usługi. W takim przypadku może zostać wyświetlony monit o uaktualnienie serwera przed przełączeniem serwera w tryb online.

## <a name="postgresql-version-syntax"></a>Składnia wersji PostgreSQL
Przed PostgreSQL w wersji 10 [zasady dotyczące wersji PostgreSQL](https://www.postgresql.org/support/versioning/) uznawane za uaktualnienie _wersji głównej_ o zwiększenie liczby pierwszej _lub_ drugiej. Na przykład 9,5 do 9,6 zostało uznane za uaktualnienie wersji _głównej_ . Począwszy od wersji 10, tylko zmiana pierwszego numeru jest uznawana za uaktualnienie wersji głównej. Na przykład 10,0 do 10,1 jest _dodatkowym_ uaktualnieniem wersji. Wersja 10 do 11 to uaktualnienie wersji _głównej_ .

## <a name="next-steps"></a>Następne kroki
- Zobacz Azure Database for PostgreSQL — [obsługiwane wersje](./concepts-supported-versions.md) pojedynczego serwera
- Zobacz [obsługiwane wersje](flexible-server/concepts-supported-versions.md) serwera Azure Database for PostgreSQL (wersja zapoznawcza)
- Aby uzyskać informacje na temat sposobu przeprowadzania uaktualnień głównych wersji, zobacz dokumentację dotyczącą [uaktualniania wersji głównych](how-to-upgrade-using-dump-and-restore.md) .
- Informacje na temat obsługiwanych rozszerzeń PostgreSQL można znaleźć [w dokumencie rozszerzenia](concepts-extensions.md).
