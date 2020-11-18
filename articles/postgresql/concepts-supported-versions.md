---
title: Obsługiwane wersje — Azure Database for PostgreSQL — pojedynczy serwer
description: Opisuje obsługiwane wersje głównych i pomocniczych Postgres w Azure Database for PostgreSQL-pojedynczym serwerze.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/16/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 72d774b4ced6471ff7b355b2cb43c3c9127b5975
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658523"
---
# <a name="supported-postgresql-major-versions"></a>Obsługiwane wersje główne PostgreSQL

Aby uzyskać szczegółowe informacje dotyczące zasad pomocy technicznej, zobacz [zasady dotyczące wersji Azure Database for PostgreSQL](concepts-version-policy.md) .

Azure Database for PostgreSQL obecnie obsługuje następujące wersje główne:

## <a name="postgresql-version-11"></a>PostgreSQL, wersja 11
Bieżąca wersja pomocnicza to 11,6. Zapoznaj się z [dokumentacją PostgreSQL](https://www.postgresql.org/docs/11/static/release-11-6.html) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="postgresql-version-10"></a>PostgreSQL wersja 10
Bieżąca wersja pomocnicza to 10,11. Zapoznaj się z [dokumentacją PostgreSQL](https://www.postgresql.org/docs/10/static/release-10-11.html) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="postgresql-version-96"></a>PostgreSQL w wersji 9,6
Bieżąca wersja pomocnicza to 9.6.16. Zapoznaj się z [dokumentacją PostgreSQL](https://www.postgresql.org/docs/9.6/static/release-9-6-16.html) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

## <a name="postgresql-version-95"></a>PostgreSQL w wersji 9,5
Bieżąca wersja pomocnicza to 9.5.20. Zapoznaj się z [dokumentacją PostgreSQL](https://www.postgresql.org/docs/9.5/static/release-9-5-20.html) , aby dowiedzieć się więcej na temat ulepszeń i poprawek w tej wersji pomocniczej.

> [!NOTE]
> Dostosowanie przy użyciu Azure Database for PostgreSQL [zasad obsługi wersji](https://www.postgresql.org/support/versioning/)społeczności Postgres spowoduje wycofanie Postgres w wersji 9,5 w dniu 11 lutego 2021. Więcej szczegółów i ograniczeń można znaleźć w temacie [zasady dotyczące wersji Azure Database for PostgreSQL](concepts-version-policy.md) .

## <a name="managing-upgrades"></a>Zarządzanie uaktualnieniami
Projekt PostgreSQL regularnie wydaje drobne wersje, aby naprawić zgłoszone błędy. Azure Database for PostgreSQL automatycznie poprawek serwerów z drobnymi wersjami podczas comiesięcznych wdrożeń usługi. 

Automatyczne uaktualnienia w miejscu dla wersji głównych nie są obsługiwane. Aby uaktualnić do następnej wersji głównej, możesz 
   * Zobacz różne metody, aby przeprowadzić [uaktualnienia wersji głównych przy użyciu zrzutów i przywracania](./how-to-upgrade-using-dump-and-restore.md)
   * Używanie [pg_dump i pg_restore](./howto-migrate-using-dump-and-restore.md) do przenoszenia bazy danych na serwer utworzony przy użyciu nowej wersji aparatu
   * Alternatywnie można przeprowadzić uaktualnienie z PostgreSQL 10 do 11 przy użyciu [usługi Azure Database Migration Service](..\dms\tutorial-azure-postgresql-to-azure-postgresql-online-portal.md)

### <a name="version-syntax"></a>Składnia wersji
Przed PostgreSQL w wersji 10 [zasady dotyczące wersji PostgreSQL](https://www.postgresql.org/support/versioning/) uznawane za uaktualnienie _wersji głównej_ o zwiększenie liczby pierwszej _lub_ drugiej. Na przykład 9,5 do 9,6 zostało uznane za uaktualnienie wersji _głównej_ . Począwszy od wersji 10, tylko zmiana pierwszego numeru jest uznawana za uaktualnienie wersji głównej. Na przykład 10,0 do 10,1 jest _dodatkowym_ uaktualnieniem wersji. Wersja 10 do 11 to uaktualnienie wersji _głównej_ .

## <a name="next-steps"></a>Następne kroki
Informacje na temat obsługiwanych rozszerzeń PostgreSQL można znaleźć [w dokumencie rozszerzenia](concepts-extensions.md).
