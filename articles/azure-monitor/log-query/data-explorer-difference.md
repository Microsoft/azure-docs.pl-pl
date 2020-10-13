---
title: Różnice w języku zapytań dziennika Azure Monitor | Microsoft Docs
description: Informacje referencyjne dotyczące języka zapytań Kusto używanego przez Azure Monitor. Zawiera dodatkowe elementy charakterystyczne dla Azure Monitor i elementy nieobsługiwane w zapytaniach dziennika Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/01/2020
ms.openlocfilehash: bfa27b0df7febbfb8c97f11f69f87c352810699b
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91993189"
---
# <a name="azure-monitor-log-query-language-differences"></a>Różnice w języku zapytań dziennika Azure Monitor

Podczas gdy [dzienniki w Azure monitor](log-query-overview.md) są oparte na [platformie Azure Eksplorator danych](/azure/data-explorer) i korzystają z tego samego [języka zapytań Kusto](/azure/kusto/query), wersja języka zawiera pewne różnice. Ten artykuł zawiera elementy, które różnią się w zależności od wersji języka używanej do Eksplorator danych i wersji używanej do Azure Monitor zapytań dzienników.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="kql-elements-not-supported-in-azure-monitor"></a>Elementy KQL nie są obsługiwane w Azure Monitor
W poniższych sekcjach opisano elementy języka zapytania Kusto, które nie są obsługiwane przez Azure Monitor.

### <a name="statements-not-supported-in-azure-monitor"></a>Instrukcje nie są obsługiwane w Azure Monitor

* [Użyj](/azure/kusto/query/aliasstatement)
* [Parametry zapytania](/azure/kusto/query/queryparametersstatement)

### <a name="functions-not-supported-in-azure-monitor"></a>Funkcje nie są obsługiwane w Azure Monitor

* [cluster()](/azure/kusto/query/clusterfunction)
* [cursor_after()](/azure/kusto/query/cursorafterfunction)
* [cursor_before_or_at()](/azure/kusto/query/cursorbeforeoratfunction)
* [cursor_current (), current_cursor ()](/azure/kusto/query/cursorcurrent)
* [Baza danych ()](/azure/kusto/query/databasefunction)
* [current_principal()](/azure/kusto/query/current-principalfunction)
* [extent_id()](/azure/kusto/query/extentidfunction)
* [extent_tags()](/azure/kusto/query/extenttagsfunction)

### <a name="operators-not-supported-in-azure-monitor"></a>Operatory nie są obsługiwane w Azure Monitor

* [Sprzężenie między klastrami](/azure/kusto/query/joincrosscluster)

### <a name="plugins-not-supported-in-azure-monitor"></a>Wtyczki nie są obsługiwane w Azure Monitor

* [Wtyczka języka Python](/azure/kusto/query/pythonplugin)
* [sql_request, wtyczka](/azure/kusto/query/sqlrequestplugin)


## <a name="additional-operators-in-azure-monitor"></a>Dodatkowe operatory w Azure Monitor
Następujące operatory obsługują określone funkcje Azure Monitor i nie są dostępne poza programem Azure Monitor.

* [Aplikacja ()](app-expression.md)
* [obszar roboczy ()](workspace-expression.md)

## <a name="next-steps"></a>Następne kroki

- Pobierz odwołania do różnych [zasobów na potrzeby zapisywania Azure monitor zapytań dzienników](/azure/data-explorer/kusto/query/).
- Uzyskaj dostęp do kompletnej [dokumentacji referencyjnej dotyczącej języka zapytań Kusto](/azure/kusto/query/).