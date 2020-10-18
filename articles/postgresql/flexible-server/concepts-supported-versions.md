---
title: Obsługiwane wersje — Azure Database for PostgreSQL — serwer elastyczny
description: Opisuje obsługiwane wersje głównych i pomocniczych PostgreSQL w Azure Database for PostgreSQL-elastycznym serwerze.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: 334eaa88ae927ae61ebb445343494cfdce570563
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/18/2020
ms.locfileid: "92167495"
---
# <a name="supported-postgresql-major-versions-in-azure-database-for-postgresql---flexible-server"></a>Obsługiwane wersje główne PostgreSQL na serwerze elastycznym Azure Database for PostgreSQL

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

Serwer elastyczny Azure Database for PostgreSQL obecnie obsługuje następujące wersje główne:

## <a name="postgresql-version-12"></a>PostgreSQL wersja 12

Bieżąca wersja pomocnicza to 12,4. Zapoznaj się z [dokumentacją PostgreSQL](https://www.postgresql.org/docs/12/static/release-12-4.html) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="postgresql-version-11"></a>PostgreSQL, wersja 11

Bieżąca wersja pomocnicza to 11,9. Zapoznaj się z [dokumentacją PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-9.html) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="postgresql-version-10-and-older"></a>PostgreSQL wersja 10 i starsze

Nie obsługujemy PostgreSQL w wersji 10 i starszej dla Azure Database for PostgreSQL-elastyczny serwer. Jeśli wymagane są starsze wersje, należy użyć opcji wdrożenia [pojedynczego serwera](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) .

## <a name="managing-upgrades"></a>Zarządzanie uaktualnieniami

Projekt PostgreSQL regularnie wydaje drobne wersje, aby naprawić zgłoszone błędy. Azure Database for PostgreSQL automatycznie poprawek serwerów z drobnymi wersjami podczas comiesięcznych wdrożeń usługi.

Automatyzacja uaktualnienia wersji głównej nie jest jeszcze obsługiwana. Na przykład obecnie nie ma żadnego automatycznego uaktualnienia z PostgreSQL 11 do PostgreSQL 12.<!-- To upgrade to the next major version, create a [database dump and restore](howto-migrate-using-dump-and-restore.md) to a server that was created with the new engine version.-->

<!--
## Next steps

For information on supported PostgreSQL extensions, see [the extensions document](concepts-extensions.md).
-->
