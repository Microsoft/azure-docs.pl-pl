---
title: Sortowanie
description: Typy sortowania obsługiwane w usłudze Azure Synapse SQL
author: filippopovic
ms.service: synapse-analytics
ms.topic: reference
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 2b10aea962a31ba600deca866a8d9f7ab3b81ea8
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133676"
---
# <a name="database-collation-support-for-synapse-sql"></a>Obsługa sortowania bazy danych dla Synapse SQL

Sortowania zapewniają ustawienia regionalne, strony kodowej, porządek sortowania i reguły czułości znaków dla typów danych opartych na znakach. Po wybraniu tej opcji wszystkie kolumny i wyrażenia wymagające informacji o sortowaniu dziedziczą wybrane sortowanie z poziomu bazy danych. Dziedziczenie domyślne można przesłonić, jawnie podając inne sortowanie dla typu danych opartego na znakach.

Podczas tworzenia nowej bazy danych puli SQL można zmienić domyślne sortowanie bazy danych z Azure Portal. Ta funkcja ułatwia tworzenie nowej bazy danych przy użyciu jednego z 3800 obsługiwanych baz danych.

Przy użyciu instrukcji CREATE DATABASE można określić domyślne sortowanie bazy danych na żądanie w usłudze SQL Synapse podczas tworzenia.

## <a name="changing-collation"></a>Zmiana sortowania
Aby zmienić domyślne sortowanie bazy danych puli SQL, można wykonać prostą aktualizację pola sortowania w środowisku aprowizacji. Na przykład jeśli chcesz zmienić domyślne sortowanie na uwzględniające wielkość liter, po prostu zmień nazwę sortowania z SQL_Latin1_General_CP1_CI_AS na SQL_Latin1_General_CP1_CS_AS. 

Aby zmienić domyślne sortowanie dla bazy danych SQL na żądanie, można użyć instrukcji ALTER DATABASE.

## <a name="list-of-unsupported-collation-types"></a>Lista nieobsługiwanych typów sortowania
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

Ponadto Pula SQL nie obsługuje następujących typów sortowania:

*    SQL_EBCDIC1141_CP1_CS_AS
*    SQL_EBCDIC277_2_CP1_CS_AS
*    UTF-8

## <a name="checking-the-current-collation"></a>Sprawdzanie bieżącego sortowania
Aby sprawdzić bieżące sortowanie bazy danych, można uruchomić następujące fragmenty kodu T-SQL:
```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Collation') AS Collation;
```
Po przekazaniu elementu "Collation" jako parametru właściwości funkcja DatabasePropertyEx zwraca bieżące sortowanie dla określonej bazy danych. Więcej informacji na temat funkcji DatabasePropertyEx można znaleźć w witrynie MSDN.

## <a name="next-steps"></a>Następne kroki

Dodatkowe informacje o najlepszych rozwiązaniach dotyczących puli SQL i SQL na żądanie można znaleźć w następujących artykułach:

- [Najlepsze rozwiązania dotyczące puli SQL](best-practices-sql-pool.md)
- [Najlepsze rozwiązania dotyczące programu SQL na żądanie](best-practices-sql-on-demand.md)


