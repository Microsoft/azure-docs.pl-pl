---
title: Obsługiwane wersje — Azure Database for MySQL
description: Dowiedz się, które wersje serwera MySQL są obsługiwane w usłudze Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: ed767055c1227d39fc29ac77a44c96b19030ad28
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94535864"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Obsługiwane wersje serwera Azure Database for MySQL

Azure Database for MySQL został opracowany z firmy [MySQL Community Edition](https://www.mysql.com/products/community/)przy użyciu aparatu InnoDB.

Baza danych MySQL używa schematu nazewnictwa X. Y. Z. X jest wersją główną, Y jest wersją pomocniczą, a Z Z to Poprawka błędu Release. Aby uzyskać więcej informacji na temat schematu, zobacz [dokumentację programu MySQL](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).


> [!NOTE]
> W usłudze brama służy do przekierowywania połączeń do wystąpień serwera. Po nawiązaniu połączenia klient oprogramowania MySQL wyświetla wersję oprogramowania MySQL ustawioną w bramie, a nie rzeczywistą wersję uruchomioną w wystąpieniu serwera MySQL. Aby określić wersję wystąpienia serwera MySQL, użyj polecenia `SELECT VERSION();` w wierszu polecenia MySQL.

Azure Database for MySQL obecnie obsługuje następujące wersje:

## <a name="mysql-version-56"></a>Baza danych MySQL w wersji 5,6

Poprawka poprawek usterek: 5.6.47

Zapoznaj się z [informacjami o wersji](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) programu MySQL, aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji.

## <a name="mysql-version-57"></a>Baza danych MySQL w wersji 5,7

Poprawka poprawek usterek: 5.7.29

Zapoznaj się z [informacjami o wersji](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) programu MySQL, aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji.

## <a name="mysql-version-80"></a>Baza danych MySQL w wersji 8,0

Poprawka poprawek usterek: 8.0.15

Zapoznaj się z [informacjami o wersji](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) programu MySQL, aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji.

## <a name="managing-updates-and-upgrades"></a>Zarządzanie aktualizacjami i uaktualnieniami
Usługa automatycznie zarządza poprawkami do poprawek błędów wersji. Na przykład 5.7.20 do 5.7.21.  

Obecnie uaktualnienia wersji pomocniczych i głównych nie są obsługiwane. Na przykład uaktualnienie bazy danych MySQL 5.6 do wersji 5.7 nie jest obsługiwane. Jeśli chcesz przeprowadzić uaktualnienie z wersji 5.6 do 5.7, wykonaj [zrzut i przywróć go](./concepts-migrate-dump-restore.md) na serwer, który został utworzony przy użyciu nowej wersji aparatu.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać szczegółowe informacje na temat zasad dotyczących wersji Azure Database for MySQL, zobacz [ten dokument](concepts-version-policy.md).
- Aby uzyskać informacje na temat konkretnych przydziałów zasobów i ograniczeń na podstawie **warstwy usług** , zobacz [warstwy usług](./concepts-pricing-tiers.md)
