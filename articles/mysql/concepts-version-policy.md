---
title: Zasady obsługi wersji — Azure Database for MySQL — pojedynczy serwer i serwer elastyczny (wersja zapoznawcza)
description: Opisuje zasady dotyczące wersji głównych i pomocniczych programu MySQL w programie Azure Database for MySQL
author: sr-msft
ms.author: srranga
ms.service: mysql
ms.topic: conceptual
ms.date: 11/03/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 49e59c43e9eaedf770b1a8e052dd73aa331d31ce
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389574"
---
# <a name="azure-database-for-mysql-version-support-policy"></a>Azure Database for MySQL obsługi wersji

Na tej stronie opisano zasady Azure Database for MySQL wersji i ma zastosowanie do trybów wdrażania Azure Database for MySQL — pojedynczy serwer i Azure Database for MySQL — elastyczny serwer (wersja zapoznawcza).

## <a name="supported--mysql-versions"></a>Obsługiwane wersje programu MySQL

Azure Database for MySQL została opracowana z [programu MySQL Community Edition](https://www.mysql.com/products/community/)przy użyciu aparatu magazynu InnoDB. Usługa obsługuje wszystkie bieżące wersje główne obsługiwane przez społeczność, czyli MySQL 5.6, 5.7 i 8.0. Program MySQL używa schematu nazewnictwa X.Y.Z, gdzie X jest wersją główną, Y jest wersją pomocniczą, a Z jest wydaniem poprawki usterki. Aby uzyskać więcej informacji na temat schematu, zobacz [dokumentację programu MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

Azure Database for MySQL obecnie obsługuje następujące główne i pomocnicze wersje programu MySQL:

| Wersja | [Pojedynczy serwer](overview.md) <br/> Bieżąca wersja pomocnicza |[Serwer elastyczny (wersja zapoznawcza)](/azure/mysql/flexible-server/overview) <br/> Bieżąca wersja pomocnicza  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|MySQL w wersji 5.6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html)(wycofane) | Nieobsługiwane|
|MySQL w wersji 5.7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|MySQL w wersji 8.0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

> [!NOTE]
> W opcji wdrożenia pojedynczego serwera brama służy do przekierowywania połączeń do wystąpień serwera. Po nawiązaniu połączenia klient oprogramowania MySQL wyświetla wersję oprogramowania MySQL ustawioną w bramie, a nie rzeczywistą wersję uruchomioną w wystąpieniu serwera MySQL. Aby określić wersję wystąpienia serwera MySQL, użyj polecenia `SELECT VERSION();` w wierszu polecenia MySQL. Jeśli aplikacja ma wymagać połączenia z określoną wersją główną, np. v5.7 lub v8.0, możesz to zrobić, zmieniając port w parametrów połączenia serwera, jak wyjaśniono w naszej dokumentacji [tutaj.](concepts-supported-versions.md#connect-to-a-gateway-node-that-is-running-a-specific-mysql-version)

> [!IMPORTANT]
> Serwer MySQL w wersji 5.6 został wycofany na pojedynczym serwerze od lutego 2021 r. Od 1 września 2021 r. nie będzie można tworzyć nowych serwerów w wersji 5.6 na Azure Database for MySQL — opcja wdrażania pojedynczego serwera. Będzie można jednak przeprowadzić odzyskiwanie do punktu w czasie i utworzyć repliki do odczytu dla istniejących serwerów.

Zapoznaj się z zasadami obsługi wersji dla wycofanych wersji w [dokumentacji zasad obsługi wersji.](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql)

## <a name="major-version-support"></a>Obsługa wersji głównych
Każda główna wersja programu MySQL będzie obsługiwana przez usługę Azure Database for MySQL od daty rozpoczęcia obsługi wersji przez platformę Azure do momentu jej wycofanie przez społeczność programu MySQL, zgodnie z zasadami obsługi wersji [.](https://www.mysql.com/support/eol-notice.html)

## <a name="minor-version-support"></a>Obsługa wersji pomocniczej
Azure Database for MySQL automatycznie przeprowadza uaktualnienia wersji pomocniczej do preferowanej wersji programu MySQL na platformie Azure w ramach okresowej konserwacji. 

## <a name="major-version-retirement-policy"></a>Zasady wycofania wersji głównych
W poniższej tabeli przedstawiono szczegóły wycofania wersji głównych programu MySQL. Daty są zgodne z [zasadami wersji programu MySQL](https://www.mysql.com/support/eol-notice.html).

| Wersja | Nowości | Data rozpoczęcia pomocy technicznej platformy Azure | Data wycofania|
| ----- | ----- | ------ | ----- |
| [MySQL 5.6](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/)| [Funkcje](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-49.html)  | 20 marca 2018 r. | Luty 2021 r.
| [MySQL 5.7](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/) | [Funkcje](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-31.html) | 20 marca 2018 r. | Październik 2023 r.
| [MySQL 8](https://mysqlserverteam.com/whats-new-in-mysql-8-0-generally-available/) | [Funkcje](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)) | 11 grudnia 2019 r. | Kwiecień 2026 r.


## <a name="retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql"></a>Wycofane wersje aparatu MySQL nie są obsługiwane w Azure Database for MySQL

Po dacie wycofania dla każdej wersji bazy danych MySQL, jeśli nadal używasz wycofanej wersji, zwróć uwagę na następujące ograniczenia:
- Ponieważ społeczność nie będzie wydawać żadnych dalszych poprawek błędów ani poprawek zabezpieczeń, program Azure Database for MySQL nie będzie poprawiać wycofanych aparatów bazy danych w przypadku jakichkolwiek usterek lub problemów z zabezpieczeniami ani w inny sposób nie podejmie środków bezpieczeństwa dotyczących wycofanych aparatów bazy danych. Jednak platforma Azure będzie nadal przeprowadzać okresową konserwację i stosowanie poprawek dla hosta, systemu operacyjnego, kontenerów i innych składników związanych z usługą.
- Jeśli jakikolwiek problem z pomocą techniczną, który może wystąpić, jest związany z bazą danych MySQL, możemy nie być w stanie zapewnić Ci pomocy technicznej. W takich przypadkach należy uaktualnić bazę danych, aby umożliwić nam świadczenie pomocy technicznej.
- Nie będzie można tworzyć nowych serwerów baz danych dla wycofanej wersji. Będzie można jednak wykonywać odzyskiwanie do punktu w czasie i tworzyć repliki do odczytu dla istniejących serwerów.
- Nowe możliwości usługi opracowane przez Azure Database for MySQL mogą być dostępne tylko dla obsługiwanych wersji serwera bazy danych.
- Umowy SLA dotyczące czasu pracy będą stosowane wyłącznie do Azure Database for MySQL związanych z usługą, a nie do żadnych przestojów spowodowanych przez błędy związane z aparatem bazy danych.  
- W skrajnym przypadku poważnego zagrożenia dla usługi spowodowanego przez lukę w zabezpieczeniach aparatu bazy danych MySQL zidentyfikowaną w wycofanej wersji bazy danych platforma Azure może zdecydować się zatrzymać węzeł obliczeniowy serwera bazy danych, aby najpierw zabezpieczyć usługę. Przed wprowadzeniem serwera do trybu online zostanie poproszony o uaktualnienie serwera. Podczas procesu uaktualniania dane będą zawsze chronione przy użyciu automatycznych kopii zapasowych wykonywanych w usłudze, które w razie potrzeby mogą zostać przywrócone do starszej wersji. 



## <a name="next-steps"></a>Następne kroki
- Zobacz Azure Database for MySQL — obsługiwane wersje [pojedynczego serwera](./concepts-supported-versions.md)
- Zobacz Azure Database for MySQL — obsługiwane wersje serwera [elastycznego (wersja zapoznawcza)](flexible-server/concepts-supported-versions.md)
- Aby przeprowadzić uaktualnienia, zobacz Zrzut i [przywracanie](./concepts-migrate-dump-restore.md) bazy danych MySQL.
