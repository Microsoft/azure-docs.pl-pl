---
title: Różnice funkcji T-SQL w usłudze Azure Synapse SQL
description: Lista funkcji Transact-SQL, które są dostępne w synapse SQL.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: b3e4d705bea7243966959038cf15373097c73ebc
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424895"
---
# <a name="transact-sql-features-supported-in-azure-synapse-sql"></a>Funkcje Transact-SQL obsługiwane w usłudze Azure Synapse SQL

Azure Synapse SQL to usługa analityczna dużych zbiorów danych, która umożliwia wykonywanie zapytań i analizowanie danych przy użyciu języka T-SQL. Do analizy danych można użyć standardowego dialektu języka SQL zgodnego ze standardem ANSI używanego w programie SQL Server i usłudze Azure SQL Database. 

Język Transact-SQL jest używany w Synapse SQL bez servera i aprowizacji modelu może odwoływać się do różnych obiektów i ma pewne różnice w zestawie obsługiwanych funkcji. Na tej stronie można znaleźć różnice językowe wysokiego poziomu transact-SQL między modelami użycia synapse SQL.

## <a name="database-objects"></a>Obiekty bazy danych

Modele użycia w synapse SQL umożliwiają używanie różnych obiektów bazy danych. Porównanie obsługiwanych typów obiektów jest pokazane w poniższej tabeli:

|   | Przygotowana | Praca bezserwerowa |
| --- | --- | --- |
| Tabele | [Tak](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nie, model bezserwerowy może wysyłać kwerendy tylko do danych zewnętrznych umieszczonych w [usłudze Azure Storage](#storage-options) |
| Widoki | [Tak](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Widoki można używać [elementów języka kwerendy,](#query-language) które są dostępne w modelu aprowizowanym. | [Tak](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Widoki można używać [elementów języka kwerendy,](#query-language) które są dostępne w modelu bezserwerowym. |
| Schematy | [Tak](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Tak](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| Tabele tymczasowe | [Tak](../sql-data-warehouse/sql-data-warehouse-tables-temporary.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Nie |
| Procedury | [Tak](/sql/t-sql/statements/create-procedure-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nie |
| Funkcje | [Tak](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nie |
| Wyzwalacze | Nie | Nie |
| Tabele zewnętrzne | [Tak](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Zobacz obsługiwane [formaty danych](#data-formats). | [Tak](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Zobacz obsługiwane [formaty danych](#data-formats). |
| Buforowanie zapytań | Tak, wiele formularzy (buforowanie oparte na ssd, w pamięci, buforowanie zestawów wyników). Ponadto zmaterializowany widok jest obsługiwany | Nie |
| Zmienne tabeli | [Nie](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), użyj tabel tymczasowych | Nie |

## <a name="query-language"></a>Język zapytań

Języki zapytań używane w synapse SQL mogą mieć różne obsługiwane funkcje w zależności od modelu użycia. W poniższej tabeli przedstawiono najważniejsze różnice językowe zapytań w dialektach Transact-SQL:

|   | Przygotowana | Praca bezserwerowa |
| --- | --- | --- |
| Instrukcja SELECT | Tak. Klauzule zapytania Transact-SQL [FOR XML/FOR JSON](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [MATCH](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) i [PREDICT](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) nie są obsługiwane. | Tak. Klauzule zapytania Transact-SQL [dla XML](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [MATCH](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [PREDICT](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)i wskazówki dotyczące kwerend nie są obsługiwane. [OFFSET/FETCH](/sql/t-sql/queries/select-order-by-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#using-offset-and-fetch-to-limit-the-rows-returned) i [PIVOT/UNPIVOT](/sql/t-sql/queries/from-using-pivot-and-unpivot?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) może służyć tylko do wykonywania zapytań o dane w tabelach tymczasowych (nie danych zewnętrznych). |
| Instrukcja INSERT | Tak | Nie |
| Instrukcja UPDATE | Tak | Nie |
| Instrukcja DELETE | Tak | Nie |
| INSTRUKCJA SCALANIE | Tak | Nie |
| Obciążenie danych | Tak. Preferowanym narzędziem jest [instrukcja COPY,](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) ale system obsługuje zarówno obciążenie luzem (BCP), jak i [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) do ładowania danych. | Nie |
| Eksport danych | Tak. Korzystanie z [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). | Tak. Korzystanie z [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). |
| Types | Tak, wszystkie typy Transact-SQL z wyjątkiem [kursora](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [ntext, tekstu i obrazu](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [konwersji wierszy,](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [typów przestrzennych,](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [wariantu sql\_](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)i [xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Tak, wszystkie typy Transact-SQL z wyjątkiem [kursora](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [ntext, tekstu i obrazu](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [konwersji wierszy,](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [typów przestrzennych,](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [wariantu sql,\_](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [xml](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)i typu tabeli |
| Zapytania obejmujące wiele baz danych | Nie | Tak, w tym [instrukcja USE.](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| Wbudowane funkcje (analiza) | Tak, wszystkie [funkcje analityczne](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)Transact-SQL, konwersja, data i [godzina,](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)funkcje logiczne, [matematyczne,](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) z wyjątkiem [funkcji CHOOSE](/sql/t-sql/functions/logical-functions-choose-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [IIF](/sql/t-sql/functions/logical-functions-iif-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)i [PARSE](/sql/t-sql/functions/parse-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Tak, wszystkie [funkcje analityczne](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)Transact-SQL, konwersja, data i [godzina,](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)funkcje logiczne, [matematyczne.](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| Wbudowane funkcje (tekst) | Tak. Wszystkie funkcje Transact-SQL [String,](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)i Sortowanie, z wyjątkiem [STRING_ESCAPE](/sql/t-sql/functions/string-escape-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) i [TRANSLATE](/sql/t-sql/functions/translate-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Tak. Wszystkie funkcje Transact-SQL [String,](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)i Sortowanie. |
| Wbudowane funkcje wartości tabeli | Tak, [funkcje zestawu wierszy Transact-SQL](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions), z wyjątkiem [OPENXML](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [OPENDATAsource](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)i [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Tak, [funkcje zestawu wierszy Transact-SQL](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions), z wyjątkiem [OPENXML](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [OPENDATASource](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)i [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)  |
| Agregacje |  Wbudowane agregaty transact-SQL z wyjątkiem [CHECKSUM_AGG](/sql/t-sql/functions/checksum-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) i [GROUPING_ID](/sql/t-sql/functions/grouping-id-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Wbudowane agregaty Transact-SQL z wyjątkiem [string agg\_](/sql/t-sql/functions/string-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| Operatory | Tak, wszystkie [operatory Transact-SQL](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) z wyjątkiem [!>](/sql/t-sql/language-elements/not-greater-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) i [!<](/sql/t-sql/language-elements/not-less-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Tak, wszystkie [operatory Transact-SQL](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)  |
| Sterowanie przepływem | Tak. Wszystkie [instrukcje kontroli przepływu Transact-SQL](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) z wyjątkiem [instrukcji CONTINUE](/sql/t-sql/language-elements/continue-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [GOTO](/sql/t-sql/language-elements/goto-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [RETURN,](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) [USE](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)i [WAITFOR](/sql/t-sql/language-elements/waitfor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Tak. Wszystkie [instrukcje Kontroli przepływu Transact-SQL](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) z wyjątkiem `WHILE (...)` [kwerendy RETURN](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) i SELECT w warunku |
| Instrukcje DDL (CREATE, ALTER, DROP) | Tak. Wszystkie instrukcje DDL Transact-SQL mające zastosowanie do obsługiwanych typów obiektów | Tak. Wszystkie instrukcje DDL Transact-SQL mające zastosowanie do obsługiwanych typów obiektów |

## <a name="security"></a>Zabezpieczenia

Synapse SQL umożliwia korzystanie z wbudowanych funkcji zabezpieczeń w celu zabezpieczenia danych i kontrolowania dostępu. W poniższej tabeli porównano różnice wysokiego poziomu między modelami użycia SQL Synapse.

|   | Przygotowana | Praca bezserwerowa |
| --- | --- | --- |
| Dane logowania | Nie dotyczy (tylko użytkownicy zawarte są obsługiwane w bazach danych) | Tak |
| Użytkownicy |  Nie dotyczy (tylko użytkownicy zawarte są obsługiwane w bazach danych) | Tak |
| [Zawarci użytkownicy](/sql/relational-databases/security/contained-database-users-making-your-database-portable?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Tak. **Uwaga:** tylko jeden użytkownik usługi AAD może być nieograniczonym administratorem | Tak |
| Uwierzytelnianie usługi Azure Active Directory (AAD) | Tak, użytkownicy AAD | Tak, loginy IAD i użytkownicy |
| Uwierzytelnianie przekazywane AAD magazynu | Tak | Tak |
| Uwierzytelnianie tokenów SYGNAtury dostępu Współdzielonego | Nie | Tak, przy użyciu [poświadczeń o zakresie bazy danych](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) w zewnętrznym źródle [danych](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) lub [poświadczenia](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)na poziomie wystąpienia . |
| Uwierzytelnianie klucza dostępu do magazynu | Tak, przy użyciu [poświadczeń o zakresie bazy danych](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) w zewnętrznym źródle [danych](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nie |
| Uwierzytelnianie tożsamości zarządzanej magazynu | Tak, przy użyciu [poświadczeń tożsamości usługi zarządzanej](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Tak, `Managed Identity` przy użyciu poświadczeń. |
| Uwierzytelnianie tożsamości aplikacji magazynu | [Tak](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nie |
| Uprawnienia — poziom obiektu | Tak, w tym możliwość udzielania, odrzucania i cofania uprawnień dla użytkowników | Tak, w tym możliwość udzielania, oddawania i cofania uprawnień do użytkowników/loginów w obsługiwanych obiektach systemowych |
| Uprawnienia — na poziomie schematu | Tak, w tym możliwość udzielania, odmów i cofania uprawnień do użytkowników/loginów w schemacie | Tak, w tym możliwość udzielania, odmów i cofania uprawnień do użytkowników/loginów w schemacie |
| Uprawnienia — [na poziomie bazy danych](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Tak | Tak |
| Uprawnienia — [na poziomie serwera](/sql/relational-databases/security/authentication-access/server-level-roles) | Nie | Tak, obsługiwane są sysadmin i inne role serwera |
| Role/grupy | Tak (zakres bazy danych) | Tak (zarówno serwer, jak i baza danych o zakresie) |
| Funkcje tożsamości zabezpieczeń &amp; | Niektóre funkcje zabezpieczeń i operatory `IS_MEMBER`zabezpieczeń `IS_ROLEMEMBER` `SESSION_USER`Transact-SQL: `USER_NAME` `CURRENT_USER`, `HAS_DBACCESS`, , , , `SUSER_NAME`, `SUSER_SNAME`, , `SYSTEM_USER`, `USER`, , `EXECUTE AS``OPEN/CLOSE MASTER KEY` | Niektóre funkcje zabezpieczeń i operatory `HAS_DBACCESS` `HAS_PERMS_BY_NAME`zabezpieczeń `IS_MEMBER', 'IS_ROLEMEMBER` `IS_SRVROLEMEMBER`Transact-SQL: `CURRENT_USER` `USER_NAME`, `EXECUTE AS`, `REVERT`, , , `SESSION_USER`, `SUSER_NAME`, `SUSER_SNAME`, `SYSTEM_USER`, `USER`, , , i . Funkcji zabezpieczeń nie można używać do wykonywania zapytań o dane zewnętrzne (przechowywania wyniku w zmiennej, która może być używana w kwerendzie).  |
| POŚWIADCZENIA ZAKRESU BAZY DANYCH | Tak | Tak |

Pula SQL i SQL na żądanie używają standardowego języka Transact-SQL do wykonywania zapytań o dane. Szczegółowe różnice można znaleźć w [odwołaniu do języka Transact-SQL](/sql/t-sql/language-reference).

## <a name="tools"></a>Narzędzia

Można użyć różnych narzędzi do łączenia się z Synapse SQL do zapytań danych.

|   | Przygotowana | Praca bezserwerowa |
| --- | --- | --- |
| Synapse Studio | Tak, skrypty SQL | Tak, skrypty SQL |
| Power BI | Tak | [Tak](tutorial-connect-power-bi-desktop.md) |
| Usługa analizy platformy Azure | Tak | Tak |
| Azure Data Studio | Tak | Tak, wersja 1.14 lub nowsza. Obsługiwane są skrypty SQL i notesy SQL. |
| SQL Server Management Studio | Tak | Tak, wersja 18.4 lub nowsza |

Większość aplikacji używa standardowego języka Transact-SQL, który może wysyłać zapytania zarówno do aprowizacji, jak i do modeli użycia bezserwerowego synapse SQL.

## <a name="storage-options"></a>Opcje magazynu

Dane, które są analizowane mogą być przechowywane na różnych typach magazynu. W poniższej tabeli wymieniono wszystkie dostępne opcje magazynowania:

|   | Przygotowana | Praca bezserwerowa |
| --- | --- | --- |
| Pamięć wewnętrzna | Tak | Nie |
| Usługa Azure Data Lake w wersji 2 | Tak | Tak |
| Azure Blob Storage | Tak | Tak |

## <a name="data-formats"></a>Formaty danych

Analizowane dane mogą być przechowywane w różnych formatach magazynu. W poniższej tabeli wymieniono wszystkie dostępne formaty danych, które można analizować:

|   | Przygotowana | Praca bezserwerowa |
| --- | --- | --- |
| Rozdzielany | [Tak](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Tak](query-single-csv-file.md) |
| CSV | Tak (ograniczniki wieloznakowe nie są obsługiwane) | [Tak](query-single-csv-file.md) |
| Parkiet | [Tak](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Tak](query-parquet-files.md), w tym pliki z [typami zagnieżdżenia](query-parquet-nested-types.md) |
| Orc ula | [Tak](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nie |
| Ul RC | [Tak](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nie |
| JSON | Tak | [Tak](query-json-files.md) |
| [Delta-jezioro](https://delta.io/) | Nie | Nie |
| [Cdm](https://docs.microsoft.com/common-data-model/) | Nie | Nie |

## <a name="next-steps"></a>Następne kroki
Dodatkowe informacje na temat najlepszych rozwiązań dla puli SQL i SQL na żądanie można znaleźć w następujących artykułach:

- [Najważniejsze wskazówki dotyczące puli SQL](best-practices-sql-pool.md)
- [Najważniejsze wskazówki dotyczące języka SQL na żądanie](best-practices-sql-on-demand.md)