---
title: Sortowanie
description: Typy sortowania obsługiwane w usłudze Azure Synapse SQL
author: filippopovic
ms.service: synapse-analytics
ms.topic: reference
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 4270677f8f5f77e1ada0b1d9385163dad762bbda
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431542"
---
# <a name="database-collation-support-for-synapse-sql"></a>Obsługa sortowania bazy danych dla synapse SQL

Sortowania zapewniają ustawienia regionalne, stronę kodową, kolejność sortowania i reguły wrażliwości znaków dla typów danych opartych na znakach. Po wybraniu wszystkie kolumny i wyrażenia wymagające informacji o sortowaniu dziedziczą wybrane sortowanie z ustawienia bazy danych. Domyślne dziedziczenie można zastąpić jawnie stwierdzając różne sortowanie dla typu danych opartych na znakach.

Podczas tworzenia nowej bazy danych puli SQL można zmienić domyślne sortowanie bazy danych z witryny Azure portal. Ta funkcja jeszcze bardziej ułatwia tworzenie nowej bazy danych przy użyciu jednego z 3800 obsługiwanych sortowania bazy danych.

Można określić domyślne sortowanie bazy danych Synapse SQL na żądanie w czasie tworzenia przy użyciu instrukcji CREATE DATABASE.

## <a name="changing-collation"></a>Zmiana sortowania
Aby zmienić domyślne sortowanie bazy danych puli SQL, należy proste zaktualizować do pola sortowanie w środowiska inicjowania obsługi administracyjnej. Na przykład, jeśli chcesz zmienić domyślne sortowanie na wielkość liter, należy po prostu zmienić nazwę sortowania z SQL_Latin1_General_CP1_CI_AS na SQL_Latin1_General_CP1_CS_AS. 

Aby zmienić domyślne sortowanie bazy danych SQL na żądanie, można użyć instrukcji ALTER DATABASE.

## <a name="list-of-unsupported-collation-types"></a>Lista nieobsługiwałych typów sortowania
*    Japanese_Bushu_Kakusu_140_BIN
*    Japanese_Bushu_Kakusu_140_BIN2
*    Japanese_Bushu_Kakusu_140_CI_AI_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI
*    Japanese_Bushu_Kakusu_140_CI_AI_WS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_WS
*    Japanese_Bushu_Kakusu_140_CI_AS
*    Japanese_Bushu_Kakusu_140_CI_AS_WS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_WS
*    Japanese_Bushu_Kakusu_140_CS_AI
*    Japanese_Bushu_Kakusu_140_CS_AI_WS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_WS
*    Japanese_Bushu_Kakusu_140_CS_AS
*    Japanese_Bushu_Kakusu_140_CS_AS_WS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_WS
*    Japanese_XJIS_140_BIN
*    Japanese_XJIS_140_BIN2
*    Japanese_XJIS_140_CI_AI_VSS
*    Japanese_XJIS_140_CI_AI_WS_VSS
*    Japanese_XJIS_140_CI_AI_KS_VSS
*    Japanese_XJIS_140_CI_AI_KS_WS_VSS
*    Japanese_XJIS_140_CI_AS_VSS
*    Japanese_XJIS_140_CI_AS_WS_VSS
*    Japanese_XJIS_140_CI_AS_KS_VSS
*    Japanese_XJIS_140_CI_AS_KS_WS_VSS
*    Japanese_XJIS_140_CS_AI_VSS
*    Japanese_XJIS_140_CS_AI_WS_VSS
*    Japanese_XJIS_140_CS_AI_KS_VSS
*    Japanese_XJIS_140_CS_AI_KS_WS_VSS
*    Japanese_XJIS_140_CS_AS_VSS
*    Japanese_XJIS_140_CS_AS_WS_VSS
*    Japanese_XJIS_140_CS_AS_KS_VSS
*    Japanese_XJIS_140_CS_AS_KS_WS_VSS
*    Japanese_XJIS_140_CI_AI
*    Japanese_XJIS_140_CI_AI_WS
*    Japanese_XJIS_140_CI_AI_KS
*    Japanese_XJIS_140_CI_AI_KS_WS
*    Japanese_XJIS_140_CI_AS
*    Japanese_XJIS_140_CI_AS_WS
*    Japanese_XJIS_140_CI_AS_KS
*    Japanese_XJIS_140_CI_AS_KS_WS
*    Japanese_XJIS_140_CS_AI
*    Japanese_XJIS_140_CS_AI_WS
*    Japanese_XJIS_140_CS_AI_KS
*    Japanese_XJIS_140_CS_AI_KS_WS
*    Japanese_XJIS_140_CS_AS
*    Japanese_XJIS_140_CS_AS_WS
*    Japanese_XJIS_140_CS_AS_KS
*    Japanese_XJIS_140_CS_AS_KS_WS

Ponadto pula SQL nie obsługuje następujących typów sortowania:

*    SQL_EBCDIC1141_CP1_CS_AS
*    SQL_EBCDIC277_2_CP1_CS_AS

## <a name="checking-the-current-collation"></a>Sprawdzanie bieżącego sortowania
Aby sprawdzić bieżące sortowanie bazy danych, można uruchomić następujący fragment kodu T-SQL:
```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Collation') AS Collation;
```
Po przekazaniu "Sortowanie" jako parametr właściwości, DatabasePropertyEx funkcja zwraca bieżące sortowanie dla określonej bazy danych. Więcej informacji na temat funkcji DatabasePropertyEx można uzyskać w u usługi MSDN.

## <a name="next-steps"></a>Następne kroki

Dodatkowe informacje na temat najlepszych rozwiązań dla puli SQL i SQL na żądanie można znaleźć w następujących artykułach:

- [Najważniejsze wskazówki dotyczące puli SQL](best-practices-sql-pool.md)
- [Najważniejsze wskazówki dotyczące języka SQL na żądanie](best-practices-sql-on-demand.md)


