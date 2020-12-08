---
title: Korzystanie z usługi API Analytics na platformie Azure API Management | Microsoft Docs
description: Użyj funkcji analizy w usłudze Azure API Management, aby ułatwić zrozumienie i kategoryzację użycia interfejsów API i wydajności interfejsu API.
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 11/24/2020
ms.author: apimpm
ms.openlocfilehash: ca7bd70bbf99a6d0079717a7a02328b11528d2e0
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96841513"
---
# <a name="get-api-analytics-in-azure-api-management"></a>Pobierz usługę API Analytics na platformie Azure API Management

Usługa Azure API Management oferuje wbudowaną analizę interfejsów API. Analizuj użycie i wydajność interfejsów API w wystąpieniu API Management w różnych wymiarach, w tym:

* Godzina
* Lokalizacja geograficzna
* Interfejsy API
* Operacje interfejsu API
* Produkty
* Subskrypcje
* Użytkownicy
* Żądania

:::image type="content" source="media/howto-use-analytics/analytics-report-portal.png" alt-text="Analiza osi czasu w portalu":::

Używaj analiz do monitorowania wysokiego poziomu i rozwiązywania problemów z interfejsami API. Aby uzyskać dodatkowe funkcje monitorowania, w tym wszystkie metryki w czasie rzeczywistym i dzienniki zasobów na potrzeby diagnostyki i inspekcji, zobacz [Samouczek: monitorowanie opublikowanych interfejsów API](api-management-howto-use-azure-monitor.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="analytics---portal"></a>Analiza — Portal

Użyj Azure Portal, aby szybko przejrzeć dane analityczne dla wystąpienia API Management.

1. W [Azure Portal](https://portal.azure.com)przejdź do wystąpienia API Management. 
1. W menu po lewej stronie w obszarze **monitorowanie** wybierz pozycję **Analiza**.

    :::image type="content" source="media/howto-use-analytics/monitoring-menu-analytics.png" alt-text="Wybieranie analiz dla wystąpienia API Management w portalu":::  
1. Wybierz zakres czasu dla danych lub wprowadź niestandardowy zakres czasu.
1. Wybierz kategorię raportu dla danych analitycznych, takich jak **oś czasu**, **Geografia** i tak dalej.
1. Opcjonalnie można filtrować raport przy użyciu co najmniej jednej dodatkowej kategorii.

## <a name="analytics---rest-api"></a>Analiza — interfejs API REST

Użyj operacji [raportów](/rest/api/apimanagement/2019-12-01/reports) w interfejsie API REST API Management, aby pobrać i filtrować dane analityczne dla wystąpienia API Management.

Dostępne operacje zwracają rekordy raportów według interfejsu API, lokalizacji geograficznej, operacji interfejsu API, produktu, żądania, subskrypcji, godziny lub użytkownika.

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do Azure Monitor funkcji w API Management, zobacz [Samouczek: monitorowanie opublikowanych interfejsów API](api-management-howto-use-azure-monitor.md)
* Aby zapoznać się ze szczegółowym rejestrowaniem i monitorowaniem HTTP, zobacz [monitorowanie interfejsów API za pomocą usługi Azure API Management, Event Hubs i Moesif](api-management-log-to-eventhub-sample.md).
* Dowiedz się więcej na temat integrowania [usługi azure API Management z usługą azure Application Insights](api-management-howto-app-insights.md).