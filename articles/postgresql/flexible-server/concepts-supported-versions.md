---
title: Obsługiwane wersje — Azure Database for PostgreSQL — serwer elastyczny
description: Opisuje obsługiwane wersje głównych i pomocniczych PostgreSQL w Azure Database for PostgreSQL-elastycznym serwerze.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 474545b7c5eca64b558e5fe26e23e6800b0cab96
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037170"
---
# <a name="supported-postgresql-major-versions-in-azure-database-for-postgresql---flexible-server"></a>Obsługiwane wersje główne PostgreSQL na serwerze elastycznym Azure Database for PostgreSQL

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

Serwer elastyczny Azure Database for PostgreSQL obecnie obsługuje następujące wersje główne:

## <a name="postgresql-version-12"></a>PostgreSQL wersja 12

Bieżąca wersja pomocnicza to 12,5. Zapoznaj się z [dokumentacją PostgreSQL](https://www.postgresql.org/docs/12/static/release-12-4.html) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="postgresql-version-11"></a>PostgreSQL, wersja 11

Bieżąca wersja pomocnicza to 11,10. Zapoznaj się z [dokumentacją PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-9.html) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="postgresql-version-10-and-older"></a>PostgreSQL wersja 10 i starsze

Nie obsługujemy PostgreSQL w wersji 10 i starszej dla Azure Database for PostgreSQL-elastyczny serwer. Jeśli wymagane są starsze wersje, należy użyć opcji wdrożenia [pojedynczego serwera](../concepts-supported-versions.md) .

## <a name="managing-upgrades"></a>Zarządzanie uaktualnieniami

Projekt PostgreSQL regularnie wydaje drobne wersje, aby naprawić zgłoszone błędy. Usługa Azure Database for PostgreSQL automatycznie instaluje na serwerach wersje podrzędne podczas comiesięcznych wdrożeń usługi.

Automatyzacja uaktualnienia wersji głównej nie jest jeszcze obsługiwana. Na przykład obecnie nie ma żadnego automatycznego uaktualnienia z PostgreSQL 11 do PostgreSQL 12.<!-- To upgrade to the next major version, create a [database dump and restore](howto-migrate-using-dump-and-restore.md) to a server that was created with the new engine version.-->

<!--
## Next steps

For information on supported PostgreSQL extensions, see [the extensions document](concepts-extensions.md).
-->