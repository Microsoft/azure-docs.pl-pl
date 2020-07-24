---
title: Różnice funkcji języka T-SQL w programie SQL Synapse
description: Lista funkcji języka Transact-SQL, które są dostępne w języku SQL Synapse.
services: synapse analytics
author: jovanpop-msft
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: jovanpop
ms.reviewer: jrasnick
ms.openlocfilehash: e1223a9cd5bd3a092bc35c9ba03f7352c6ac7eed
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87059410"
---
# <a name="transact-sql-features-supported-in-azure-synapse-sql"></a>Funkcje języka Transact-SQL obsługiwane przez usługę Azure Synapse SQL

Azure Synapse SQL to usługa analizy danych Big Data, która umożliwia wykonywanie zapytań i analizowanie danych przy użyciu języka T-SQL. Możesz użyć standardowego dialektu zgodnego ze standardem ANSI języka SQL używanego na SQL Server i Azure SQL Database na potrzeby analizy danych. 

Język Transact-SQL jest używany w Synapse bezserwerowym i nieobsługiwanym modelu programu SQL Server może odwoływać się do różnych obiektów i zawiera pewne różnice w zestawie obsługiwanych funkcji. Na tej stronie można znaleźć różnice w języku Transact-SQL dotyczące wysokiego poziomu między modelami użycia Synapse SQL.

## <a name="database-objects"></a>Obiekty bazy danych

Modele użycia w programie Synapse SQL umożliwiają używanie różnych obiektów bazy danych. Porównanie obsługiwanych typów obiektów przedstawiono w poniższej tabeli:

|   | Aprowizowane | Praca bezserwerowa |
| --- | --- | --- |
| **Tabele** | [Tak](/sql/t-sql/statements/create-table-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nie, model bez serwera może wysyłać zapytania tylko do danych zewnętrznych umieszczonych w [usłudze Azure Storage](#storage-options) |
| **Widoki** | [Tak](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Widoki mogą używać [elementów języka zapytań](#query-language) , które są dostępne w modelu aprowizacji. | [Tak](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Widoki mogą używać [elementów języka zapytań](#query-language) , które są dostępne w modelu bezserwerowym. |
| **Schematy** | [Tak](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Tak](/sql/t-sql/statements/create-schema-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| **Tabele tymczasowe** | [Tak](../sql-data-warehouse/sql-data-warehouse-tables-temporary.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Nie |
| **Procedury** | [Tak](/sql/t-sql/statements/create-procedure-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nie |
| **Funkcje** | [Tak](/sql/t-sql/statements/create-function-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Tak, tylko wbudowane funkcje z wartościami przechowywanymi w tabeli. |
| **Wyzwalacze** | Nie | Nie |
| **Tabele zewnętrzne** | [Tak](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Zobacz obsługiwane [formaty danych](#data-formats). | [Tak](/sql/t-sql/statements/create-external-table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). Zobacz obsługiwane [formaty danych](#data-formats). |
| **Buforowanie zapytań** | Tak, wiele formularzy (buforowanie oparte na dyskach SSD, pamięć podręczna zestawu wyników). Ponadto jest obsługiwany widok z materiałami | Nie |
| **Zmienne tabeli** | [Nie](/sql/t-sql/data-types/table-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), Użyj tabel tymczasowych | Nie |
| **[Dystrybucja tabel](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**               | Yes | Nie |
| **[Indeksy tabeli](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                           | Yes | Nie |
| **[Partycje tabeli](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)**                     | Yes | Nie |
| **[Statystyki](develop-tables-statistics.md)**            | Tak | Tak |
| **[Zarządzanie obciążeniami, klasy zasobów i kontrola współbieżności](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)** | Yes    | Nie |

## <a name="query-language"></a>Język zapytań

Języki zapytań używane w programie SQL Synapse mogą mieć różne obsługiwane funkcje w zależności od modelu zużycia. W poniższej tabeli przedstawiono najważniejsze różnice w języku zapytań w dialektach języka Transact-SQL:

|   | Aprowizowane | Praca bezserwerowa |
| --- | --- | --- |
| **SELECT — instrukcja** | Tak. Klauzule zapytań języka Transact-SQL [dla języka XML/for JSON](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)i [dopasowania](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) nie są obsługiwane. | Tak. Klauzule zapytań języka Transact-SQL [dotyczące wskazówek XML](/sql/t-sql/queries/select-for-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [dopasowywania](/sql/t-sql/queries/match-sql-graph?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [przewidywania](/sql/t-sql/queries/predict-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)i zapytań nie są obsługiwane. [Przesunięcia/pobierania](/sql/t-sql/queries/select-order-by-clause-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#using-offset-and-fetch-to-limit-the-rows-returned) i [Pivot/UNPIVOT](/sql/t-sql/queries/from-using-pivot-and-unpivot?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) mogą służyć do wykonywania zapytań względem obiektów systemowych (nie danych zewnętrznych). |
| **INSERT — instrukcja** | Yes | Nie |
| **Instrukcja UPDATE** | Yes | Nie |
| **DELETE — Instrukcja** | Yes | Nie |
| **MERGE — instrukcja** | Yes | Nie |
| **[Transakcje](develop-transactions.md)** | Yes | Nie |
| **[Etykiety](develop-label.md)** | Yes | Nie |
| **Ładowanie danych** | Tak. Preferowane narzędzie to [copy](/sql/t-sql/statements/copy-into-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) , ale system obsługuje ładowanie zbiorcze (bcp) i [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) na potrzeby ładowania danych. | Nie |
| **Eksportowanie danych** | Tak. Korzystanie z [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). | Tak. Korzystanie z [CETAS](/sql/t-sql/statements/create-external-table-as-select-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). |
| **Typy** | Tak, wszystkie typy Transact-SQL z wyjątkiem [Cursor](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [ntext, text i Image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [typy przestrzenne](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [ \_ Variant SQL](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)i [XML](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Tak, wszystkie typy Transact-SQL z wyjątkiem [Cursor](/sql/t-sql/data-types/cursor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [ntext, text i Image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [rowversion](/sql/t-sql/data-types/rowversion-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [typy przestrzenne](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [ \_ Variant SQL](/sql/t-sql/data-types/sql-variant-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [XML](/sql/t-sql/xml/xml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)i typ tabeli |
| **Zapytania obejmujące wiele baz danych** | Nie | Tak, łącznie z instrukcją [use](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) . |
| **Funkcje wbudowane (analiza)** | Tak, wszystkie funkcje [analityczne](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)języka Transact-SQL, konwersja, [Data i godzina](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), logiczne, [matematyczne](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) , z wyjątkiem [opcji Choose](/sql/t-sql/functions/logical-functions-choose-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [IIf](/sql/t-sql/functions/logical-functions-iif-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)i [Parse](/sql/t-sql/functions/parse-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Tak, wszystkie operacje [analityczne](/sql/t-sql/functions/analytic-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)języka Transact-SQL, konwersja, [Data i godzina](/sql/t-sql/functions/date-and-time-data-types-and-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), funkcje logiczne, [matematyczne](/sql/t-sql/functions/mathematical-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) . |
| **Wbudowane funkcje (tekst)** | Tak. Wszystkie funkcje [ciągów](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)i sortowania w języku Transact-SQL, z wyjątkiem [STRING_ESCAPE](/sql/t-sql/functions/string-escape-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) i [tłumaczenia](/sql/t-sql/functions/translate-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Tak. Wszystkie funkcje [ciągów](/sql/t-sql/functions/string-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)języka Transact-SQL, [JSON](/sql/t-sql/functions/json-functions-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)i sortowania. |
| **Wbudowane funkcje tabeli — wartości** | Tak, [funkcje zestawu wierszy Transact-SQL](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions), z wyjątkiem [OPENXML](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [OTWÓRZKWERENDĘ](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)i [OPENROWSET](/sql/t-sql/functions/openrowset-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Tak, [funkcje zestawu wierszy Transact-SQL](/sql/t-sql/functions/functions?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest#rowset-functions), z wyjątkiem [OPENXML](/sql/t-sql/functions/openxml-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [OPENDATASOURCE](/sql/t-sql/functions/opendatasource-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)i [OPENQUERY](/sql/t-sql/functions/openquery-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)  |
| **Agregacje** |  Wbudowane agregacje języka Transact-SQL z wyjątkiem [CHECKSUM_AGG](/sql/t-sql/functions/checksum-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) i [GROUPING_ID](/sql/t-sql/functions/grouping-id-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Wbudowane agregacje języka Transact-SQL z wyjątkiem [ciągów \_ AGG](/sql/t-sql/functions/string-agg-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) |
| **Operatory** | Tak, wszystkie [Operatory Transact-SQL](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) z wyjątkiem [! >](/sql/t-sql/language-elements/not-greater-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) i [! <](/sql/t-sql/language-elements/not-less-than-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Tak, wszystkie [operatory języka Transact-SQL](/sql/t-sql/language-elements/operators-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)  |
| **Kontrola przepływu** | Tak. Wszystkie [instrukcje kontroli języka Transact-SQL](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) z wyjątkiem [Continue](/sql/t-sql/language-elements/continue-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [goto](/sql/t-sql/language-elements/goto-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [Return](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest), [use](/sql/t-sql/language-elements/use-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)i [WAITFOR](/sql/t-sql/language-elements/waitfor-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Tak. Wszystkie [instrukcje kontroli w języku Transact-SQL](/sql/t-sql/language-elements/control-of-flow?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) z wyjątkiem [Return](/sql/t-sql/language-elements/return-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) i Select Query w `WHILE (...)` warunku |
| **Instrukcje języka DDL (CREATE, ALTER, DROP)** | Tak. Wszystkie instrukcje języka Transact-SQL DDL mające zastosowanie do obsługiwanych typów obiektów | Tak. Wszystkie instrukcje języka Transact-SQL DDL mające zastosowanie do obsługiwanych typów obiektów |

## <a name="security"></a>Zabezpieczenia

Synapse SQL umożliwia korzystanie z wbudowanych funkcji zabezpieczeń w celu zabezpieczania danych i kontroli dostępu. Poniższa tabela zawiera porównanie ogólnych różnic między modelami Synapse języka SQL.

|   | Aprowizowane | Praca bezserwerowa |
| --- | --- | --- |
| **Dane logowania** | Nie dotyczy (w bazach danych są obsługiwane tylko zawarte użytkownicy) | Tak |
| **Użytkownicy** |  Nie dotyczy (w bazach danych są obsługiwane tylko zawarte użytkownicy) | Tak |
| **[Zawarci użytkownicy](/sql/relational-databases/security/contained-database-users-making-your-database-portable?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)** | Tak. **Uwaga:** tylko jeden użytkownik usługi Azure AD może być nieograniczonym administratorem | Tak |
| **Uwierzytelnianie hasła/nazwy użytkownika SQL**| Tak | Tak |
| **Uwierzytelnianie Azure Active Directory (AAD)**| Tak, użytkownicy usługi Azure AD | Tak, logowania i użytkownicy usługi Azure AD |
| **Uwierzytelnianie przekazujące Azure Active Directory magazynu (AAD)** | Tak | Tak |
| **Uwierzytelnianie tokenu SAS magazynu** | Nie | Tak, przy użyciu [poświadczeń w zakresie bazy danych](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) w [zewnętrznym źródle danych](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) lub [poświadczenie](/sql/t-sql/statements/create-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)na poziomie wystąpienia. |
| **Uwierzytelnianie klucza dostępu do magazynu** | Tak, przy użyciu [poświadczeń w zakresie bazy danych](/sql/t-sql/statements/create-database-scoped-credential-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) w [ZEWNĘTRZNYm źródle danych](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nie |
| **Uwierzytelnianie [tożsamości zarządzanej](../security/synapse-workspace-managed-identity.md) przez magazyn** | Tak, przy użyciu [poświadczeń tożsamość usługi zarządzanej](../../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Tak, przy użyciu `Managed Identity` poświadczeń. |
| **Uwierzytelnianie tożsamości aplikacji magazynu** | [Tak](/sql/t-sql/statements/create-external-data-source-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nie |
| **Uprawnienia — poziom obiektu** | Tak, w tym możliwość UDZIELAnia, odmowy i ODWOŁYWANia uprawnień użytkownikom | Tak, w tym możliwość UDZIELAnia, ODMAWIAnia i ODWOŁYWANia uprawnień użytkownikom/logowaniem do obsługiwanych obiektów systemu |
| **Uprawnienia — poziom schematu** | Tak, w tym możliwość UDZIELAnia, odmowy i ODWOŁYWANia uprawnień użytkownikom/loginom w schemacie | Tak, w tym możliwość UDZIELAnia, odmowy i ODWOŁYWANia uprawnień użytkownikom/loginom w schemacie |
| **Uprawnienia — [poziom bazy danych](/sql/relational-databases/security/authentication-access/database-level-roles?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest)** | Tak | Tak |
| **Uprawnienia — [poziom serwera](/sql/relational-databases/security/authentication-access/server-level-roles)** | Nie | Tak, obsługiwane są role sysadmin i inne serwery |
| **Uprawnienia — [zabezpieczenia na poziomie kolumny](/azure/synapse-analytics/sql-data-warehouse/column-level-security?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)** | Tak | Tak |
| **Role/grupy** | Tak (zakres bazy danych) | Tak (zakres serwera i bazy danych) |
| **&amp;Funkcje tożsamości zabezpieczeń** | Niektóre funkcje i operatory zabezpieczeń Transact-SQL:,,,,,,,,,, `CURRENT_USER` `HAS_DBACCESS` `IS_MEMBER` `IS_ROLEMEMBER` `SESSION_USER` `SUSER_NAME` `SUSER_SNAME` `SYSTEM_USER` `USER` `USER_NAME` `EXECUTE AS` ,`OPEN/CLOSE MASTER KEY` | Niektóre funkcje zabezpieczeń i operatory języka Transact-SQL:,,,,,,,,,, `CURRENT_USER` `HAS_DBACCESS` `HAS_PERMS_BY_NAME` `IS_MEMBER', 'IS_ROLEMEMBER` `IS_SRVROLEMEMBER` `SESSION_USER` `SUSER_NAME` `SUSER_SNAME` `SYSTEM_USER` `USER` `USER_NAME` , `EXECUTE AS` , i `REVERT` . Funkcji zabezpieczeń nie można używać do wykonywania zapytań dotyczących danych zewnętrznych (przechowywania wyników w zmiennej, których można użyć w zapytaniu).  |
| **POŚWIADCZENIA W ZAKRESIE BAZY DANYCH** | Tak | Tak |
| **POŚWIADCZENIE ZAKRESU SERWERA** | Nie | Yes |
| **Zabezpieczenia na poziomie wiersza** | [Tak](https://docs.microsoft.com/sql/relational-databases/security/row-level-security?toc=%2Fazure%2Fsynapse-analytics%2Ftoc.json&bc=%2Fazure%2Fsynapse-analytics%2Fbreadcrumb%2Ftoc.json&view=sql-server-ver15) | Nie |
| **Transparent Data Encryption (TDE)** | [Tak](/azure/sql-database/transparent-data-encryption-azure-sql?toc=%2Fazure%2Fsynapse-analytics%2Ftoc.json&bc=%2Fazure%2Fsynapse-analytics%2Fbreadcrumb%2Ftoc.json&tabs=azure-portal) | Nie | 
| **Klasyfikacja & odnajdywania danych** | [Tak](/azure/sql-database/sql-database-data-discovery-and-classification?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Nie |
| **Ocena luk w zabezpieczeniach** | [Tak](/azure/sql-database/sql-vulnerability-assessment?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Nie |
| **Advanced Threat Protection** | [Tak](/azure/sql-database/sql-database-threat-detection-overview?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)
| **Inspekcja** | [Tak](/azure/sql-database/sql-database-auditing?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Nie |
| **[Reguły zapory](../security/synapse-workspace-ip-firewall.md)**| Tak | Tak |
| **[Prywatny punkt końcowy](../security/synapse-workspace-managed-private-endpoints.md)**| Tak | Tak |

Usługa SQL Pool i usługa SQL na żądanie używają standardowego języka Transact-SQL do wykonywania zapytań dotyczących danych. Aby uzyskać szczegółowe różnice, zapoznaj się z dokumentacją [języka Transact-SQL](/sql/t-sql/language-reference).

## <a name="tools"></a>Narzędzia

Za pomocą różnych narzędzi można nawiązać połączenie z usługą Synapse SQL w celu wykonywania zapytań dotyczących danych.

|   | Aprowizowane | Praca bezserwerowa |
| --- | --- | --- |
| **Synapse Studio** | Tak, skrypty SQL | Tak, skrypty SQL |
| **Power BI** | Tak | [Tak](tutorial-connect-power-bi-desktop.md) |
| **Usługa Azure Analysis Service** | Tak | Tak |
| **Azure Data Studio** | Tak | Tak, w wersji 1.18.0 lub nowszej. Obsługiwane są skrypty SQL i notesy SQL. |
| **SQL Server Management Studio** | Tak | Tak, wersja 18,5 lub nowsza |

> [!NOTE]
> Program SSMS umożliwia nawiązanie połączenia z usługą SQL na żądanie (wersja zapoznawcza) i kwerendą. Jest ona częściowo obsługiwana począwszy od wersji 18,5, można jej używać do nawiązywania połączeń i tylko zapytań.

Większość aplikacji korzysta ze standardowego języka Transact-SQL, który może wykonywać zapytania dotyczące nieobsługiwanych i bezserwerowych modeli zużycia Synapse SQL.

## <a name="storage-options"></a>Opcje magazynu

Analizowane dane mogą być przechowywane w różnych typach magazynu. W poniższej tabeli wymieniono wszystkie dostępne opcje magazynu:

|   | Aprowizowane | Praca bezserwerowa |
| --- | --- | --- |
| **Magazyn wewnętrzny** | Yes | Nie |
| **Azure Data Lake v2** | Tak | Tak |
| **Azure Blob Storage** | Tak | Tak |
| **Magazyn analityczny usługi Azure CosmosDB** | Nie | Tak, za pomocą [linku Synapse](/azure/cosmos-db/synapse-link?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) (w obszarze [wersja zapoznawcza](/azure/cosmos-db/synapse-link?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json#limitations)) |

## <a name="data-formats"></a>Formaty danych

Analizowane dane mogą być przechowywane w różnych formatach magazynu. W poniższej tabeli wymieniono wszystkie dostępne formaty danych, które można analizować:

|   | Aprowizowane | Praca bezserwerowa |
| --- | --- | --- |
| **Lista** | [Tak](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Tak](query-single-csv-file.md) |
| **CSV** | Tak (ograniczniki wieloznakowe nie są obsługiwane) | [Tak](query-single-csv-file.md) |
| **Parquet** | [Tak](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | [Tak](query-parquet-files.md), w tym pliki z [zagnieżdżonymi typami](query-parquet-nested-types.md) |
| **ORC Hive** | [Tak](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nie |
| **Gałąź RC** | [Tak](/sql/t-sql/statements/create-external-file-format-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) | Nie |
| **JSON** | Tak | [Tak](query-json-files.md) |
| **Avro** | Nie | Nie |
| **[Różnica — Lake](https://delta.io/)** | Nie | Nie |
| **[CDM](https://docs.microsoft.com/common-data-model/)** | Nie | Nie |

## <a name="next-steps"></a>Następne kroki
Dodatkowe informacje o najlepszych rozwiązaniach dotyczących puli SQL i SQL na żądanie można znaleźć w następujących artykułach:

- [Najlepsze rozwiązania dotyczące puli SQL](best-practices-sql-pool.md)
- [Najlepsze rozwiązania dotyczące programu SQL na żądanie](best-practices-sql-on-demand.md)
