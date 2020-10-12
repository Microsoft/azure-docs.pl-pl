---
title: Optymalizacja zbiorczych operacji wstawiania-Azure Database for PostgreSQL-pojedynczego serwera
description: W tym artykule opisano, jak można zoptymalizować operacje wstawiania zbiorczego na jednym serwerze Azure Database for PostgreSQL.
author: dianaputnam
ms.author: dianas
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: 4d10f06577738364e3f4a0ea40221d37ebfb31c0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86116288"
---
# <a name="optimize-bulk-inserts-and-use-transient-data-on-an-azure-database-for-postgresql---single-server"></a>Optymalizacja zbiorczych operacji wstawiania i używania danych przejściowych na Azure Database for PostgreSQL-pojedynczym serwerze 
W tym artykule opisano, jak można zoptymalizować operacje wstawiania zbiorczego i używać danych przejściowych na serwerze Azure Database for PostgreSQL.

## <a name="use-unlogged-tables"></a>Korzystanie z niezarejestrowanych tabel
Jeśli istnieją operacje związane z obciążeniem, które obejmują dane przejściowe lub wstawiają duże zbiory danych zbiorczo, należy rozważyć użycie niezarejestrowanych tabel.

Niezarejestrowane tabele to funkcja PostgreSQL, która może być efektywnie używana do optymalizacji zbiorczych operacji wstawiania. PostgreSQL używa rejestrowania Write-Ahead (WAL). Domyślnie zapewnia ona niepodzielność i trwałość. Wartość niepodzielności, spójności, izolacji i trwałości tworzą właściwości KWAŚNe. 

Wstawianie do nierejestrowanej tabeli oznacza, że PostgreSQL wstawia bez zapisywania w dzienniku transakcji, który sam jest operacją we/wy. W związku z tym te tabele są znacznie szybsze niż zwykłe tabele.

Użyj następujących opcji, aby utworzyć nierejestrowaną tabelę:
- Utwórz nową nierejestrowaną tabelę przy użyciu składni `CREATE UNLOGGED TABLE <tableName>` .
- Przekonwertuj istniejącą zarejestrowana tabelę na niezarejestrowana tabelę przy użyciu składni `ALTER TABLE <tableName> SET UNLOGGED` .  

Aby wycofać ten proces, użyj składni `ALTER TABLE <tableName> SET LOGGED` .

## <a name="unlogged-table-tradeoff"></a>Wady nierejestrowanej tabeli
Niezarejestrowane tabele nie są bezpieczne. Nierejestrowana tabela jest automatycznie obcinana po awarii lub podlega nieczystemu zamknięciu. Zawartość niezarejestrowanej tabeli również nie jest replikowana na serwery rezerwy. Wszystkie indeksy utworzone w nierejestrowanej tabeli są również automatycznie rejestrowane. Po zakończeniu operacji wstawiania Konwertuj tabelę na zarejestrowaną, aby wstawić ją do postaci trwałej.

Niektóre obciążenia klientów miały około 15% do 20% poprawy wydajności podczas korzystania z niezarejestrowanej tabeli.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z obciążeniem, aby korzystać z danych przejściowych i dużych wstawek zbiorczych. Zapoznaj się z następującą dokumentacją PostgreSQL:
 
- [Utwórz tabelę polecenia SQL](https://www.postgresql.org/docs/current/static/sql-createtable.html)
