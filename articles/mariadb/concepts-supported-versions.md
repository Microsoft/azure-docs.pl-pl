---
title: Obsługiwane wersje — Azure Database for MariaDB
description: Dowiedz się, które wersje serwera MariaDB są obsługiwane w usłudze Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 7/20/2020
ms.openlocfilehash: 0b495bf21b1f78e5e6a9285f67e1769d2d2a0db1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87065696"
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

- Aby uzyskać informacje na temat konkretnych przydziałów zasobów i ograniczeń na podstawie **warstwy usług**, zobacz [warstwy usług](./concepts-pricing-tiers.md).
