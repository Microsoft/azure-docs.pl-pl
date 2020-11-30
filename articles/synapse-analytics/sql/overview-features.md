---
title: Różnice funkcji języka T-SQL w programie SQL Synapse
description: Lista funkcji języka Transact-SQL, które są dostępne w języku SQL Synapse.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: 063373b2ad21f46e37145a544b156ed100621a4c
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327019"
---
# <a name="transact-sql-features-supported-in-azure-synapse-sql"></a>Funkcje języka Transact-SQL obsługiwane przez usługę Azure Synapse SQL

Azure Synapse SQL to usługa analizy danych Big Data, która umożliwia wykonywanie zapytań i analizowanie danych przy użyciu języka T-SQL. Możesz użyć standardowego dialektu zgodnego ze standardem ANSI języka SQL używanego na SQL Server i Azure SQL Database na potrzeby analizy danych. 

Język Transact-SQL jest używany w Synapse programu SQL Server, a dedykowany model może odwoływać się do różnych obiektów i zawiera pewne różnice w zestawie obsługiwanych funkcji. Na tej stronie można znaleźć różnice w języku Transact-SQL dotyczące wysokiego poziomu między modelami użycia Synapse SQL.

## <a name="database-objects"></a>Obiekty bazy danych

Modele użycia w programie Synapse SQL umożliwiają używanie różnych obiektów bazy danych. Porównanie obsługiwanych typów obiektów przedstawiono w poniższej tabeli:

|   | Dedykowane | Praca bezserwerowa |
| --- | --- | --- |
| **Tabele** | [Tak](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Nie, model bez serwera może wysyłać zapytania tylko do danych zewnętrznych umieszczonych w [usłudze Azure Storage](#storage-options) |
| **Widoki** | [Tak](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Widoki mogą używać [elementów języka zapytań](#query-language) , które są dostępne w dedykowanym modelu. | [Tak](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Widoki mogą używać [elementów języka zapytań](#query-language) , które są dostępne w modelu bezserwerowym. |
| **Schematy** | [Tak](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | [Tak](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) |
| **Tabele tymczasowe** | [Tak](../sql-data-warehouse/sql-data-warehouse-tables-temporary.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Nie |
| **Procedury** | [Tak](/sql/t-sql/statements/create-procedure-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Tak |
| **Funkcje** | [Tak](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Tak, tylko wbudowane funkcje z wartościami przechowywanymi w tabeli. |
| **Wyzwalacze** | Nie | Nie |
| **Tabele zewnętrzne** | [Tak](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Zobacz obsługiwane [formaty danych](#data-formats). | [Tak](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). Zobacz obsługiwane [formaty danych](#data-formats). |
| **Buforowanie zapytań** | Tak, wiele formularzy (buforowanie oparte na dyskach SSD, pamięć podręczna zestawu wyników). Ponadto jest obsługiwany widok z materiałami | Nie |
| **Zmienne tabeli** | [Nie](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), Użyj tabel tymczasowych | Nie |
| **[Dystrybucja tabel](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**               | Tak | Nie |
| **[Indeksy tabeli](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                           | Tak | Nie |
| **[Partycje tabeli](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                     | Tak | Nie |
| **[Statystyki](develop-tables-statistics.md)**            | Tak | Tak |
| **[Zarządzanie obciążeniami, klasy zasobów i kontrola współbieżności](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)** | Tak    | Nie |
| **Kontrola kosztów** | Tak, przy użyciu akcji skalowania w górę i w dół. | Tak, przy użyciu [procedury Azure Portal lub T-SQL](https://docs.microsoft.com/azure/synapse-analytics/sql/data-processed#cost-control). |

## <a name="query-language"></a>Język zapytań

Języki zapytań używane w programie SQL Synapse mogą mieć różne obsługiwane funkcje w zależności od modelu zużycia. W poniższej tabeli przedstawiono najważniejsze różnice w języku zapytań w dialektach języka Transact-SQL:

|   | Dedykowane | Praca bezserwerowa |
| --- | --- | --- |
| **SELECT — instrukcja** | Tak. Klauzule zapytań języka Transact-SQL [dla języka XML/for JSON](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)i [dopasowania](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) nie są obsługiwane. | Tak. Klauzule zapytań języka Transact-SQL [dotyczące wskazówek XML](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [dopasowywania](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [przewidywania](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)i zapytań nie są obsługiwane. |
| **INSERT — instrukcja** | Tak | Nie |
| **Instrukcja UPDATE** | Tak | Nie |
| **DELETE — Instrukcja** | Tak | Nie |
| **MERGE — instrukcja** | Nie | Nie |
| **[Transakcje](develop-transactions.md)** | Tak | Tak, dotyczy obiektów meta-danych. |
| **[Etykiety](develop-label.md)** | Tak | Nie |
| **Ładowanie danych** | Tak. Preferowane narzędzie to [copy](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) , ale system obsługuje ładowanie zbiorcze (bcp) i [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) na potrzeby ładowania danych. | Nie |
| **Eksportowanie danych** | Tak. Korzystanie z [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). | Tak. Korzystanie z [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true). |
| **Typy** | Tak, wszystkie typy Transact-SQL z wyjątkiem [Cursor](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [ntext, text i Image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [typy przestrzenne](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [ \_ Variant SQL](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)i [XML](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Tak, wszystkie typy Transact-SQL z wyjątkiem [Cursor](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [ntext, text i Image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [typy przestrzenne](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [ \_ Variant SQL](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [XML](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)i typ tabeli |
| **Zapytania obejmujące wiele baz danych** | Nie | Tak, łącznie z instrukcją [use](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) . |
| **Funkcje wbudowane (analiza)** | Tak, wszystkie funkcje [analityczne](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)języka Transact-SQL, konwersja, [Data i godzina](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), logiczne, [matematyczne](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) , z wyjątkiem [opcji Choose](/sql/t-sql/functions/logical-functions-choose-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [IIf](/sql/t-sql/functions/logical-functions-iif-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)i [Parse](/sql/t-sql/functions/parse-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Tak, wszystkie operacje [analityczne](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)języka Transact-SQL, konwersja, [Data i godzina](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), funkcje logiczne, [matematyczne](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) . |
| **Wbudowane funkcje (tekst)** | Tak. Wszystkie funkcje [ciągów](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)i sortowania w języku Transact-SQL, z wyjątkiem [STRING_ESCAPE](/sql/t-sql/functions/string-escape-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) i [tłumaczenia](/sql/t-sql/functions/translate-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Tak. Wszystkie funkcje [ciągów](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)języka Transact-SQL, [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)i sortowania. |
| **Wbudowane funkcje tabeli — wartości** | Tak, [funkcje zestawu wierszy Transact-SQL](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions), z wyjątkiem [OPENXML](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [OTWÓRZKWERENDĘ](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)i [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Tak, [funkcje zestawu wierszy Transact-SQL](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions), z wyjątkiem [OPENXML](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)i [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  |
| **Agregacje** |  Wbudowane agregacje języka Transact-SQL z wyjątkiem [CHECKSUM_AGG](/sql/t-sql/functions/checksum-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) i [GROUPING_ID](/sql/t-sql/functions/grouping-id-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Wbudowane agregacje języka Transact-SQL. |
| **Operatory** | Tak, wszystkie [Operatory Transact-SQL](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) z wyjątkiem [! >](/sql/t-sql/language-elements/not-greater-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) i [! <](/sql/t-sql/language-elements/not-less-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Tak, wszystkie [operatory języka Transact-SQL](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)  |
| **Kontrola przepływu** | Tak. Wszystkie [instrukcje kontroli języka Transact-SQL](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) z wyjątkiem [Continue](/sql/t-sql/language-elements/continue-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [goto](/sql/t-sql/language-elements/goto-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [Return](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true), [use](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)i [WAITFOR](/sql/t-sql/language-elements/waitfor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Tak. Wszystkie [instrukcje kontroli w języku Transact-SQL](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) — wybór zapytania w `WHILE (...)` warunku |
| **Instrukcje języka DDL (CREATE, ALTER, DROP)** | Tak. Wszystkie instrukcje języka Transact-SQL DDL mające zastosowanie do obsługiwanych typów obiektów | Tak. Wszystkie instrukcje języka Transact-SQL DDL mające zastosowanie do obsługiwanych typów obiektów |

## <a name="security"></a>Zabezpieczenia

Synapse SQL umożliwia korzystanie z wbudowanych funkcji zabezpieczeń w celu zabezpieczania danych i kontroli dostępu. Poniższa tabela zawiera porównanie ogólnych różnic między modelami Synapse języka SQL.

|   | Dedykowane | Praca bezserwerowa |
| --- | --- | --- |
| **Dane logowania** | Nie dotyczy (w bazach danych są obsługiwane tylko zawarte użytkownicy) | Tak |
| **Użytkownicy** |  Nie dotyczy (w bazach danych są obsługiwane tylko zawarte użytkownicy) | Tak |
| **[Zawarci użytkownicy](/sql/relational-databases/security/contained-database-users-making-your-database-portable?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)** | Tak. **Uwaga:** tylko jeden użytkownik usługi Azure AD może być nieograniczonym administratorem | Nie |
| **Uwierzytelnianie hasła/nazwy użytkownika SQL**| Tak | Tak |
| **Uwierzytelnianie Azure Active Directory (Azure AD)**| Tak, użytkownicy usługi Azure AD | Tak, logowania i użytkownicy usługi Azure AD |
| **Uwierzytelnianie przekazujące Azure Active Directory magazynu (Azure AD)** | Tak | Tak |
| **Uwierzytelnianie tokenu SAS magazynu** | Nie | Tak, przy użyciu [poświadczeń w zakresie bazy danych](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) w [zewnętrznym źródle danych](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) lub [poświadczenie](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)na poziomie wystąpienia. |
| **Uwierzytelnianie klucza dostępu do magazynu** | Tak, przy użyciu [poświadczeń w zakresie bazy danych](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) w [ZEWNĘTRZNYm źródle danych](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Nie |
| **Uwierzytelnianie [tożsamości zarządzanej](../security/synapse-workspace-managed-identity.md) przez magazyn** | Tak, przy użyciu [poświadczeń tożsamość usługi zarządzanej](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Tak, przy użyciu `Managed Identity` poświadczeń. |
| **Uwierzytelnianie tożsamości aplikacji magazynu** | [Tak](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Nie |
| **Uprawnienia — poziom obiektu** | Tak, w tym możliwość UDZIELAnia, odmowy i ODWOŁYWANia uprawnień użytkownikom | Tak, w tym możliwość UDZIELAnia, ODMAWIAnia i ODWOŁYWANia uprawnień użytkownikom/logowaniem do obsługiwanych obiektów systemu |
| **Uprawnienia — poziom schematu** | Tak, w tym możliwość UDZIELAnia, odmowy i ODWOŁYWANia uprawnień użytkownikom/loginom w schemacie | Tak, w tym możliwość UDZIELAnia, odmowy i ODWOŁYWANia uprawnień użytkownikom/loginom w schemacie |
| **Uprawnienia — [poziom bazy danych](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)** | Tak | Tak |
| **Uprawnienia — [poziom serwera](/sql/relational-databases/security/authentication-access/server-level-roles)** | Nie | Tak, obsługiwane są role sysadmin i inne serwery |
| **Uprawnienia — [zabezpieczenia na poziomie kolumny](/azure/synapse-analytics/sql-data-warehouse/column-level-security?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)** | Tak | Tak |
| **Role/grupy** | Tak (zakres bazy danych) | Tak (zakres serwera i bazy danych) |
| **&amp;Funkcje tożsamości zabezpieczeń** | Niektóre funkcje i operatory zabezpieczeń Transact-SQL:,,,,,,,,,,  `CURRENT_USER` `HAS_DBACCESS` `IS_MEMBER` `IS_ROLEMEMBER` `SESSION_USER` `SUSER_NAME` `SUSER_SNAME` `SYSTEM_USER` `USER` `USER_NAME` `EXECUTE AS` , `OPEN/CLOSE MASTER KEY` | Niektóre funkcje zabezpieczeń i operatory języka Transact-SQL:,,,,,,,,,  `CURRENT_USER` `HAS_DBACCESS` `HAS_PERMS_BY_NAME` `IS_MEMBER', 'IS_ROLEMEMBER` `IS_SRVROLEMEMBER` `SESSION_USER` `SESSION_CONTEXT` `SUSER_NAME` `SUSER_SNAME` `SYSTEM_USER` , `USER` , `USER_NAME` ,, `EXECUTE AS` i `REVERT` . Funkcji zabezpieczeń nie można używać do wykonywania zapytań dotyczących danych zewnętrznych (przechowywania wyników w zmiennej, których można użyć w zapytaniu).  |
| **POŚWIADCZENIA W ZAKRESIE BAZY DANYCH** | Tak | Tak |
| **POŚWIADCZENIE ZAKRESU SERWERA** | Nie | Tak |
| **Zabezpieczenia na poziomie wiersza** | [Tak](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?toc=%2Fazure%2Fsynapse-analytics%2Ftoc.json&bc=%2Fazure%2Fsynapse-analytics%2Fbreadcrumb%2Ftoc.json&view=sql-server-ver15) | Nie |
| **Transparent Data Encryption (TDE)** | [Tak](/azure/sql-database/transparent-data-encryption-azure-sql?toc=%2Fazure%2Fsynapse-analytics%2Ftoc.json&bc=%2Fazure%2Fsynapse-analytics%2Fbreadcrumb%2Ftoc.json&tabs=azure-portal) | Nie | 
| **Odnajdywanie i klasyfikacja danych** | [Tak](/azure/sql-database/sql-database-data-discovery-and-classification?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Nie |
| **Ocena luk w zabezpieczeniach** | [Tak](/azure/sql-database/sql-vulnerability-assessment?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Nie |
| **Advanced Threat Protection** | [Tak](/azure/sql-database/sql-database-threat-detection-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
| **Inspekcja** | [Tak](/azure/sql-database/sql-database-auditing?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Nie |
| **[Reguły zapory](../security/synapse-workspace-ip-firewall.md)**| Tak | Tak |
| **[Prywatny punkt końcowy](../security/synapse-workspace-managed-private-endpoints.md)**| Tak | Tak |

Dedykowana Pula SQL i bezserwerowa Pula SQL używają standardowego języka Transact-SQL do wykonywania zapytań dotyczących danych. Aby uzyskać szczegółowe różnice, zapoznaj się z dokumentacją [języka Transact-SQL](/sql/t-sql/language-reference).

## <a name="tools"></a>Narzędzia

Za pomocą różnych narzędzi można nawiązać połączenie z usługą Synapse SQL w celu wykonywania zapytań dotyczących danych.

|   | Dedykowane | Praca bezserwerowa |
| --- | --- | --- |
| **Synapse Studio** | Tak, skrypty SQL | Tak, skrypty SQL |
| **Power BI** | Tak | [Tak](tutorial-connect-power-bi-desktop.md) |
| **Usługa Azure Analysis Service** | Tak | Tak |
| **Azure Data Studio** | Tak | Tak, w wersji 1.18.0 lub nowszej. Obsługiwane są skrypty SQL i notesy SQL. |
| **SQL Server Management Studio** | Tak | Tak, wersja 18,5 lub nowsza |

> [!NOTE]
> Możesz użyć programu SSMS do nawiązania połączenia z pulę SQL bezserwerową (wersja zapoznawcza) i kwerendą. Jest ona częściowo obsługiwana począwszy od wersji 18,5, można jej używać do nawiązywania połączeń i tylko zapytań.

Większość aplikacji korzysta ze standardowego języka Transact-SQL, który może wysyłać zapytania do modeli zużycia dedykowanych i bezserwerowych języka SQL Synapse.

## <a name="storage-options"></a>Opcje magazynu

Analizowane dane mogą być przechowywane w różnych typach magazynu. W poniższej tabeli wymieniono wszystkie dostępne opcje magazynu:

|   | Dedykowane | Praca bezserwerowa |
| --- | --- | --- |
| **Magazyn wewnętrzny** | Tak | Nie |
| **Azure Data Lake v2** | Tak | Tak |
| **Azure Blob Storage** | Tak | Tak |
| **Azure SQL (zdalny)** | Nie | Nie |
| **Magazyn transakcyjny usługi Azure CosmosDB** | Nie | Nie |
| **Magazyn analityczny usługi Azure CosmosDB** | Nie | Tak, za pomocą [linku Synapse (wersja zapoznawcza)](/azure/cosmos-db/synapse-link?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) ([publiczna wersja zapoznawcza](/azure/cosmos-db/synapse-link?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#limitations)) |
| **Tabele Apache Spark (w obszarze roboczym)** | Nie | Tabele PARQUET tylko z [synchronizacją metadanych](develop-storage-files-spark-tables.md) |
| **Tabele Apache Spark (zdalne)** | Nie | Nie |
| **Tabele datakostek (zdalna)** | Nie | Nie |

## <a name="data-formats"></a>Formaty danych

Analizowane dane mogą być przechowywane w różnych formatach magazynu. W poniższej tabeli wymieniono wszystkie dostępne formaty danych, które można analizować:

|   | Dedykowane | Praca bezserwerowa |
| --- | --- | --- |
| **Lista** | [Tak](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | [Tak](query-single-csv-file.md) |
| **CSV** | Tak (ograniczniki wieloznakowe nie są obsługiwane) | [Tak](query-single-csv-file.md) |
| **Parquet** | [Tak](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | [Tak](query-parquet-files.md), w tym pliki z [zagnieżdżonymi typami](query-parquet-nested-types.md) |
| **ORC Hive** | [Tak](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Nie |
| **Gałąź RC** | [Tak](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) | Nie |
| **JSON** | Tak | [Tak](query-json-files.md) |
| **Avro** | Nie | Nie |
| **[Różnica — Lake](https://delta.io/)** | Nie | Nie |
| **[CDM](https://docs.microsoft.com/common-data-model/)** | Nie | Nie |

## <a name="next-steps"></a>Następne kroki
Dodatkowe informacje na temat najlepszych rozwiązań dotyczących dedykowanej puli SQL i bezserwerowej puli można znaleźć w następujących artykułach:

- [Najlepsze rozwiązania dotyczące dedykowanej puli SQL](best-practices-sql-pool.md)
- [Najlepsze rozwiązania dotyczące puli SQL bezserwerowej](best-practices-sql-on-demand.md)
