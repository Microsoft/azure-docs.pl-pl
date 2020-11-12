---
title: Obsługiwane wersje — Azure Database for MariaDB
description: Dowiedz się, które wersje serwera MariaDB są obsługiwane w usłudze Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 7/20/2020
ms.openlocfilehash: dbf32626714200e6712c67b701ebc597c4a7ba7e
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541032"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Obsługiwane wersje serwera Azure Database for MariaDB

Azure Database for MariaDB został opracowany z [serwera MariaDB](https://downloads.mariadb.org/)"open source" przy użyciu aparatu InnoDB.

MariaDB używa schematu nazewnictwa X. Y. Z. X jest wersją główną, Y jest wersją pomocniczą i Z jest wersją poprawki.

> [!NOTE]
> W usłudze brama służy do przekierowywania połączeń do wystąpień serwera. Po nawiązaniu połączenia klient MySQL wyświetla wersję zestawu MariaDB w bramie, a nie rzeczywistą wersję działającą w wystąpieniu serwera MariaDB. Aby określić wersję wystąpienia serwera MariaDB, użyj `SELECT VERSION();` polecenia.

Azure Database for MariaDB obecnie obsługuje następującą wersję:

## <a name="mariadb-version-102"></a>MariaDB w wersji 10,2

Wersja poprawki: 10.2.32

Zapoznaj się z [dokumentacją MariaDB](https://mariadb.com/kb/en/mariadb-10232-release-notes/) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji.

## <a name="mariadb-version-103"></a>MariaDB w wersji 10,3

Wersja poprawki: 10.3.23

Zapoznaj się z [dokumentacją MariaDB](https://mariadb.com/kb/en/mariadb-10323-release-notes/) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji.

## <a name="managing-updates-and-upgrades"></a>Zarządzanie aktualizacjami i uaktualnieniami
Usługa automatycznie zarządza uaktualnieniami aktualizacji poprawek. Na przykład 10.2.21 do 10.2.23.  

Obecnie uaktualnienia wersji pomocniczych i głównych nie są obsługiwane. Na przykład uaktualnienie bazy danych MariaDB 10.2 do wersji 10.3 nie jest obsługiwane. Jeśli chcesz przeprowadzić uaktualnienie z 10,2 do 10,3, zrób [zrzut i Przywróć](./howto-migrate-dump-restore.md) go do serwera, który został utworzony przy użyciu nowej wersji aparatu.

## <a name="next-steps"></a>Następne kroki

- Aby uzyskać informacje na temat konkretnych przydziałów zasobów i ograniczeń na podstawie **warstwy usług** , zobacz [warstwy usług](./concepts-pricing-tiers.md).
