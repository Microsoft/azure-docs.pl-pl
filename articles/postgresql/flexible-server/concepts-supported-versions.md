---
title: Obsługiwane wersje — Azure Database for PostgreSQL — serwer elastyczny
description: Opisuje obsługiwane wersje głównych i pomocniczych Postgres w Azure Database for PostgreSQL-elastycznym serwerze.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 9d651a38361ccaa7f8788fb4a375b39b2953228b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90936969"
---
# <a name="supported-postgresql-major-versions-in-azure-database-for-postgresql---flexible-server"></a>Obsługiwane wersje główne PostgreSQL na serwerze elastycznym Azure Database for PostgreSQL

> [!IMPORTANT]
> Azure Database for PostgreSQL — serwer elastyczny jest w wersji zapoznawczej

Serwer elastyczny Azure Database for PostgreSQL obecnie obsługuje następujące wersje główne:

## <a name="postgresql-version-12"></a>PostgreSQL wersja 12

Bieżąca wersja pomocnicza to 12,1. Zapoznaj się z [dokumentacją PostgreSQL](https://www.postgresql.org/docs/12/static/release-12-1.html) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="postgresql-version-11"></a>PostgreSQL, wersja 11

Bieżąca wersja pomocnicza to 11,8. Zapoznaj się z [dokumentacją PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-8.html) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="postgresql-version-10-and-older"></a>PostgreSQL wersja 10 i starsze

Nie obsługujemy PostgreSQL w wersji 10 i starszej dla Azure Database for PostgreSQL-elastyczny serwer. Jeśli wymagane są starsze wersje, należy użyć opcji wdrożenia [pojedynczego serwera](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) .

## <a name="managing-upgrades"></a>Zarządzanie uaktualnieniami

Projekt PostgreSQL regularnie wydaje drobne wersje, aby naprawić zgłoszone błędy. Azure Database for PostgreSQL automatycznie poprawek serwerów z drobnymi wersjami podczas comiesięcznych wdrożeń usługi.

Automatyczne uaktualnianie wersji głównej nie jest jeszcze obsługiwane. Na przykład obecnie nie ma żadnego automatycznego uaktualnienia z PostgreSQL 11 do PostgreSQL 12.<!-- To upgrade to the next major version, create a [database dump and restore](howto-migrate-using-dump-and-restore.md) to a server that was created with the new engine version.-->

<!--
## Next steps

For information on supported PostgreSQL extensions, see [the extensions document](concepts-extensions.md).
-->
