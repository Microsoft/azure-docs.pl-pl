---
title: Obsługiwane wersje — Azure Database for PostgreSQL — pojedynczy serwer
description: Opisuje obsługiwane wersje głównych i pomocniczych Postgres w Azure Database for PostgreSQL-pojedynczym serwerze.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/17/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: 61d9712026daab63a002793adf2e17b9db943f94
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84976901"
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

Automatyczne uaktualnianie wersji głównej nie jest obsługiwane. Na przykład nie jest możliwe automatyczne uaktualnienie bazy danych PostgreSQL 9.5 do wersji 9.6. Aby przeprowadzić uaktualnienie do następnej wersji głównej, wykonaj [zrzut bazy danych i przywróć go](./howto-migrate-using-dump-and-restore.md) na serwer, który został utworzony przy użyciu nowej wersji aparatu.

### <a name="version-syntax"></a>Składnia wersji
Przed PostgreSQL w wersji 10 [zasady dotyczące wersji PostgreSQL](https://www.postgresql.org/support/versioning/) uznawane za uaktualnienie _wersji głównej_ o zwiększenie liczby pierwszej _lub_ drugiej. Na przykład 9,5 do 9,6 zostało uznane za uaktualnienie wersji _głównej_ . Począwszy od wersji 10, tylko zmiana pierwszego numeru jest uznawana za uaktualnienie wersji głównej. Na przykład 10,0 do 10,1 jest _dodatkowym_ uaktualnieniem wersji. Wersja 10 do 11 to uaktualnienie wersji _głównej_ .

## <a name="next-steps"></a>Następne kroki
Informacje na temat obsługiwanych rozszerzeń PostgreSQL można znaleźć [w dokumencie rozszerzenia](concepts-extensions.md).
