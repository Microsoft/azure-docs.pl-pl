---
title: Obsługa sortowania
description: Typy sortowania obsługujące Synapse SQL w usłudze Azure Synapse Analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 436dbac814197556385a33d956928f97fd4716bf
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93311925"
---
# <a name="database-collation-support-for-synapse-sql-in-azure-synapse-analytics"></a>Obsługa sortowania bazy danych dla Synapse SQL w usłudze Azure Synapse Analytics 

Sortowanie udostępnia ustawienia regionalne, strony kodowej, porządek sortowania i reguły czułości znaków dla typów danych opartych na znakach. Po wybraniu tej opcji wszystkie kolumny i wyrażenia wymagające informacji o sortowaniu dziedziczą wybrane sortowanie z poziomu bazy danych. Dziedziczenie domyślne można przesłonić, jawnie podając inne sortowanie dla typu danych opartego na znakach.

Domyślne sortowanie baz danych można zmienić z Azure Portal podczas tworzenia nowej dedykowanej bazy danych puli SQL. Ta funkcja ułatwia tworzenie nowej bazy danych przy użyciu jednego z 3800 obsługiwanych baz danych.

Można określić domyślne sortowanie bazy danych puli SQL bez serwera podczas tworzenia przy użyciu instrukcji CREATE DATABASE.

## <a name="change-collation"></a>Zmień sortowanie
Aby zmienić domyślne sortowanie dla dedykowanej bazy danych puli SQL, należy zaktualizować do pola sortowania w środowisku aprowizacji. Na przykład jeśli chcesz zmienić domyślne sortowanie na wielkość liter, Zmień nazwę sortowania z SQL_Latin1_General_CP1_CI_AS na SQL_Latin1_General_CP1_CS_AS. 

Aby zmienić domyślne sortowanie dla bazy danych puli SQL bezserwerowej, można użyć instrukcji ALTER DATABASE.

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

Ponadto dedykowana Pula SQL nie obsługuje następujących typów sortowania:

*    SQL_EBCDIC1141_CP1_CS_AS
*    SQL_EBCDIC277_2_CP1_CS_AS
*    UTF-8

## <a name="check-the-current-collation"></a>Sprawdź bieżące sortowanie
Aby sprawdzić bieżące sortowanie bazy danych, można uruchomić następujące fragmenty kodu T-SQL:
```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Collation') AS Collation;
```
Po przekazaniu elementu "Collation" jako parametru właściwości funkcja DatabasePropertyEx zwraca bieżące sortowanie dla określonej bazy danych. Więcej informacji na temat funkcji DatabasePropertyEx można znaleźć w witrynie MSDN.

## <a name="next-steps"></a>Następne kroki

Dodatkowe informacje na temat najlepszych rozwiązań dotyczących dedykowanej puli SQL i bezserwerowej puli można znaleźć w następujących artykułach:

- [Najlepsze rozwiązania dotyczące dedykowanej puli SQL](best-practices-sql-pool.md)
- [Najlepsze rozwiązania dotyczące puli SQL bezserwerowej](best-practices-sql-on-demand.md)


