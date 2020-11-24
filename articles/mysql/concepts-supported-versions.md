---
title: Obsługiwane wersje — Azure Database for MySQL
description: Dowiedz się, które wersje serwera MySQL są obsługiwane w usłudze Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 8033117d9e3c31f8aa9bba06afb7c3b1b7bba67f
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95751032"
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

Uaktualnianie wersji głównej jest obecnie obsługiwane przez usługę do uaktualnień z programu MySQL v 5.6 do wersji 5.7. Aby uzyskać więcej informacji, zobacz [Jak przeprowadzić uaktualnienia wersji głównej](how-to-major-version-upgrade.md). Jeśli chcesz przeprowadzić uaktualnienie z 5,7 do 8,0, zalecamy wykonanie [zrzutu i przywrócenia](./concepts-migrate-dump-restore.md) na serwerze, który został utworzony przy użyciu nowej wersji aparatu.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać szczegółowe informacje na temat zasad dotyczących wersji Azure Database for MySQL, zobacz [ten dokument](concepts-version-policy.md).
- Aby uzyskać informacje na temat konkretnych przydziałów zasobów i ograniczeń na podstawie **warstwy usług**, zobacz [warstwy usług](./concepts-pricing-tiers.md)
