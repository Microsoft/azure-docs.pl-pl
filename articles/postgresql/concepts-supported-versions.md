---
title: Obsługiwane wersje — Azure Database for PostgreSQL — pojedynczy serwer
description: Opisuje obsługiwane wersje głównych i pomocniczych Postgres w Azure Database for PostgreSQL-pojedynczym serwerze.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/22/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: cfe4b92dbed69440ee2c07cff758faad7e01293f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91707918"
---
# <a name="supported-postgresql-major-versions"></a>Obsługiwane wersje główne PostgreSQL
Firma Microsoft dąży do obsługi wersji n-2 aparatu PostgreSQL w Azure Database for PostgreSQL-pojedynczym serwerze. Wersje byłyby w bieżącej wersji głównej na platformie Azure (n) i dwóch wcześniejszych wersjach głównych (-2).

Azure Database for PostgreSQL obecnie obsługuje następujące wersje główne:

## <a name="postgresql-version-11"></a>PostgreSQL, wersja 11
Bieżąca wersja pomocnicza to 11,6. Zapoznaj się z [dokumentacją PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-6.html) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="postgresql-version-10"></a>PostgreSQL wersja 10
Bieżąca wersja pomocnicza to 10,11. Zapoznaj się z [dokumentacją PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-11.html) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="postgresql-version-96"></a>PostgreSQL w wersji 9,6
Bieżąca wersja pomocnicza to 9.6.16. Zapoznaj się z [dokumentacją PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-16.html) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="postgresql-version-95"></a>PostgreSQL w wersji 9,5
Bieżąca wersja pomocnicza to 9.5.20. Zapoznaj się z [dokumentacją PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-20.html) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="managing-upgrades"></a>Zarządzanie uaktualnieniami
Projekt PostgreSQL regularnie wydaje drobne wersje, aby naprawić zgłoszone błędy. Azure Database for PostgreSQL automatycznie poprawek serwerów z drobnymi wersjami podczas comiesięcznych wdrożeń usługi. 

Automatyczne uaktualnienia w miejscu dla wersji głównych nie są obsługiwane. Aby uaktualnić do następnej wersji głównej, możesz 
   * Używanie [pg_dump i pg_restore](./howto-migrate-using-dump-and-restore.md) do przenoszenia bazy danych na serwer utworzony przy użyciu nowej wersji aparatu
   * Alternatywnie można przeprowadzić uaktualnienie z PostgreSQL 10 do 11 przy użyciu [usługi Azure Database Migration Service](..\dms\tutorial-azure-postgresql-to-azure-postgresql-online-portal.md)

### <a name="version-syntax"></a>Składnia wersji
Przed PostgreSQL w wersji 10 [zasady dotyczące wersji PostgreSQL](https://www.postgresql.org/support/versioning/) uznawane za uaktualnienie _wersji głównej_ o zwiększenie liczby pierwszej _lub_ drugiej. Na przykład 9,5 do 9,6 zostało uznane za uaktualnienie wersji _głównej_ . Począwszy od wersji 10, tylko zmiana pierwszego numeru jest uznawana za uaktualnienie wersji głównej. Na przykład 10,0 do 10,1 jest _dodatkowym_ uaktualnieniem wersji. Wersja 10 do 11 to uaktualnienie wersji _głównej_ .

## <a name="next-steps"></a>Następne kroki
Informacje na temat obsługiwanych rozszerzeń PostgreSQL można znaleźć [w dokumencie rozszerzenia](concepts-extensions.md).
