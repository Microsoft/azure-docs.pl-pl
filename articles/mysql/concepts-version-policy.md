---
title: Zasady obsługi wersji — Azure Database for MySQL — pojedynczy serwer i elastyczny serwer (wersja zapoznawcza)
description: Opisuje zasady dotyczące wersji głównych i pomocniczych programu MySQL w Azure Database for MySQL
author: sr-msft
ms.author: srranga
ms.service: mysql
ms.topic: conceptual
ms.date: 11/03/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 35ed677c3176fb990f752f3204a1ae11bf39896c
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121697"
---
# <a name="azure-database-for-mysql-version-support-policy"></a>Zasady obsługi wersji Azure Database for MySQL

Ta strona zawiera opis zasad dotyczących wersji Azure Database for MySQL i ma zastosowanie do trybu wdrożenia serwera Azure Database for MySQL i Azure Database for MySQL-elastycznym (wersja zapoznawcza).

## <a name="supported--mysql-versions"></a>Obsługiwane wersje programu MySQL

Azure Database for MySQL został opracowany z firmy [MySQL Community Edition](https://www.mysql.com/products/community/)przy użyciu aparatu magazynu InnoDB. Usługa obsługuje wszystkie bieżące wersje główne obsługiwane przez społeczność, mianowicie MySQL 5,6, 5,7 i 8,0. Baza danych MySQL używa schematu nazewnictwa X. Y, gdzie X jest wersją główną, Y jest wersją pomocniczą, a Z Z to Poprawka błędu. Aby uzyskać więcej informacji na temat schematu, zobacz [dokumentację programu MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

Azure Database for MySQL obecnie obsługuje następujące główne i pomocnicze wersje programu MySQL:

| Wersja | [Pojedynczy serwer](overview.md) <br/> Bieżąca wersja pomocnicza |[Serwer elastyczny (wersja zapoznawcza)](/../flexible-server/overview.md) <br/> Bieżąca wersja pomocnicza  |
|:-------------------|:-------------------------------------------|:---------------------------------------------|
|Baza danych MySQL w wersji 5,6 |  [5.6.47](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html)(wycofane) | Nieobsługiwane|
|Baza danych MySQL w wersji 5,7 | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) | [5.7.29](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html)|
|Baza danych MySQL w wersji 8,0 | [8.0.15](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) | [8.0.21](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)|

> [!NOTE]
> W przypadku opcji wdrożenia pojedynczego serwera Brama jest używana do przekierowywania połączeń z wystąpieniami serwera. Po nawiązaniu połączenia klient oprogramowania MySQL wyświetla wersję oprogramowania MySQL ustawioną w bramie, a nie rzeczywistą wersję uruchomioną w wystąpieniu serwera MySQL. Aby określić wersję wystąpienia serwera MySQL, użyj polecenia `SELECT VERSION();` w wierszu polecenia MySQL. Jeśli aplikacja wymaga nawiązania połączenia z konkretną wersją główną 3.0 lub 8.0, można to zrobić, zmieniając port w parametrach połączenia serwera, jak wyjaśniono w naszej dokumentacji [.](concepts-supported-versions.md#connect-to-a-gateway-node-that-is-running-a-specific-mysql-version)

> [!IMPORTANT]
> Baza danych MySQL w wersji 5.6 została wycofana na pojedynczym serwerze z febuary 2021. Począwszy od 1 września 2021, nie będzie można tworzyć nowych serwerów z systemem v 5.6 w ramach opcji wdrażania na jednym serwerze Azure Database for MySQL. Można jednak przeprowadzić odzyskiwanie do punktu w czasie i utworzyć repliki odczytu dla istniejących serwerów.

Zapoznaj się z zasadami obsługi wersji dla wycofanych wersji w [dokumentacji zasad obsługi wersji.](concepts-version-policy.md#retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql)

## <a name="major-version-support"></a>Obsługa wersji głównej
Każda główna wersja programu MySQL będzie obsługiwana przez Azure Database for MySQL od daty, w której platforma Azure zacznie obsługiwać wersję, dopóki wersja nie zostanie wycofana przez społeczność MySQL zgodnie z [zasadami przechowywania wersji](https://www.mysql.com/support/eol-notice.html).

## <a name="minor-version-support"></a>Obsługa wersji pomocniczej
Azure Database for MySQL automatycznie przeprowadza uaktualnienia wersji pomocniczej do preferowanej wersji programu MySQL platformy Azure w ramach okresowej konserwacji. 

## <a name="major-version-retirement-policy"></a>Zasady wycofywania wersji głównych
Poniższa tabela zawiera szczegóły wycofania dla wersji głównych programu MySQL. Daty są zgodne z [zasadami przechowywania wersji programu MySQL](https://www.mysql.com/support/eol-notice.html).

| Wersja | Nowości | Data rozpoczęcia pomocy technicznej platformy Azure | Data wycofania|
| ----- | ----- | ------ | ----- |
| [MySQL 5,6](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/)| [Funkcje](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-49.html)  | 20 marca 2018 | Luty 2021 r.
| [MySQL 5,7](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/) | [Funkcje](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-31.html) | 20 marca 2018 | Październik 2023
| [Baza danych MySQL 8](https://mysqlserverteam.com/whats-new-in-mysql-8-0-generally-available/) | [Funkcje](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-21.html)) | 11 grudnia 2019 | Kwiecień 2026


## <a name="retired-mysql-engine-versions-not-supported-in-azure-database-for-mysql"></a>Wycofane wersje aparatu MySQL nie są obsługiwane w Azure Database for MySQL

Po upływie daty wycofania dla każdej wersji bazy danych MySQL należy pamiętać o następujących ograniczeniach:
- Ponieważ Wspólnota nie będzie zwalniać żadnych dalszych poprawek lub poprawek zabezpieczeń, Azure Database for MySQL nie będzie w żaden sposób naprawiać niewycofanego aparatu bazy danych pod kątem błędów lub problemów z zabezpieczeniami albo podjąć środki bezpieczeństwa w odniesieniu do wycofanego aparatu bazy danych. Jednak platforma Azure będzie kontynuowała okresowe konserwacje i stosowanie poprawek dla hosta, systemu operacyjnego, kontenerów i innych składników związanych z usługą.
- W przypadku problemów z obsługą, które mogą wystąpić w odniesieniu do bazy danych MySQL, firma Microsoft może nie mieć możliwości zapewnienia pomocy technicznej. W takich przypadkach trzeba będzie uaktualnić bazę danych, aby umożliwić nam zapewnienie pomocy technicznej.
- Nie będzie można tworzyć nowych serwerów baz danych dla wycofanej wersji. Można jednak przeprowadzić odzyskiwanie do punktu w czasie i utworzyć repliki odczytu dla istniejących serwerów.
- Nowe możliwości usługi opracowane przez Azure Database for MySQL mogą być dostępne tylko dla obsługiwanych wersji serwera bazy danych.
- Czas przestoju umowy SLA będzie stosowany wyłącznie do Azure Database for MySQL problemów związanych z usługą, a nie do przestojów spowodowanych błędami związanymi z aparatem bazy danych.  
- W skrajnym zdarzeniu w przypadku poważnych zagrożeń dla usługi spowodowanej przez lukę w zabezpieczeniach aparatu bazy danych MySQL zidentyfikowaną w wycofanej wersji bazy danych, platforma Azure może zrezygnować z wcześniejszego zabezpieczenia usługi w węźle obliczeniowym serwera bazy danych. Przed przełączeniem serwera w tryb online zostanie wyświetlony monit o uaktualnienie serwera. W trakcie procesu uaktualniania dane będą zawsze chronione przy użyciu automatycznych kopii zapasowych wykonywanych w ramach usługi, których można użyć do przywrócenia starszej wersji w razie potrzeby. 



## <a name="next-steps"></a>Następne kroki
- Zobacz Azure Database for MySQL — [obsługiwane wersje](./concepts-supported-versions.md) pojedynczego serwera
- Zobacz [obsługiwane wersje](flexible-server/concepts-supported-versions.md) serwera Azure Database for MySQL (wersja zapoznawcza)
- Aby przeprowadzić uaktualnienia, zobacz artykuł dotyczący [zrzutów i przywracania](./concepts-migrate-dump-restore.md) bazy danych MySQL.
