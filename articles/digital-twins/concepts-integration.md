---
title: Integracja z innymi usługami
titleSuffix: Azure Digital Twins
description: Zapoznaj się z wymaganiami dotyczącymi ruchu przychodzącego i wychodzącego podczas wdrażania usługi Azure Digital bliźniaczych reprezentacji.
author: baanders
ms.author: baanders
ms.date: 3/16/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 7e360c158c7887109684d13f774cbbda1813373e
ms.sourcegitcommit: 638f326d02d108cf7e62e996adef32f2b2896fd5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91729138"
---
# <a name="integrate-azure-digital-twins-with-other-services"></a>Integrowanie usługi Azure Digital bliźniaczych reprezentacji z innymi usługami

Usługa Azure Digital bliźniaczych reprezentacji jest zwykle używana razem z innymi usługami. Korzystając z [**tras zdarzeń**](concepts-route-events.md), usługa Azure Digital bliźniaczych reprezentacji odbiera dane z usług nadrzędnych, takich jak [IoT Hub](../iot-hub/about-iot-hub.md) lub [Logic Apps](../logic-apps/logic-apps-overview.md), które są używane do dostarczania telemetrii i powiadomień. 

Usługa Azure Digital bliźniaczych reprezentacji umożliwia również kierowanie danych do usług podrzędnych, takich jak [Azure Maps](../azure-maps/about-azure-maps.md) i [Time Series Insights](../time-series-insights/time-series-insights-update-overview.md), do magazynu, integracji przepływów pracy, analiz i innych. 

## <a name="data-ingress"></a>Dane wejściowe

Usługa Azure Digital bliźniaczych reprezentacji może być oparta na danych i zdarzeniach z dowolnej usługi —[IoT Hub](../iot-hub/about-iot-hub.md), [Logic Apps](../logic-apps/logic-apps-overview.md), własnej usługi niestandardowej i nie tylko. Dzięki temu można zbierać dane telemetryczne z urządzeń fizycznych w środowisku i przetwarzać je przy użyciu grafu Digital bliźniaczych reprezentacji na platformie Azure w chmurze.

Usługa Azure Digital bliźniaczych reprezentacji nie ma wbudowanej IoT Hub. Możesz użyć istniejącego IoT Hub obecnie w środowisku produkcyjnym lub wdrożyć nowy. Zapewnia to pełen dostęp do wszystkich możliwości zarządzania urządzeniami IoT Hub.

Aby pozyskać dane z dowolnego źródła w usłudze Azure Digital bliźniaczych reprezentacji, użyj [**funkcji platformy Azure**](../azure-functions/functions-overview.md). Dowiedz się więcej o tym wzorcu w instrukcje: pozyskiwanie danych [*telemetrycznych z IoT Hub*](how-to-ingest-iot-hub-data.md)lub wypróbuj je w samouczku Digital bliźniaczych reprezentacji na platformie Azure [*: łączenie kompleksowego rozwiązania*](tutorial-end-to-end.md). 

Możesz również dowiedzieć się, jak połączyć usługę Azure Digital bliźniaczych reprezentacji z wyzwalaczem Logic Apps w poradach [*: integracja z Logic Apps*](how-to-integrate-logic-apps.md).

## <a name="data-egress-services"></a>Usługi danych wychodzących

Usługa Azure Digital bliźniaczych reprezentacji może wysyłać dane do podłączonych **punktów końcowych**. Obsługiwane punkty końcowe to:
* [Centrum zdarzeń](../event-hubs/event-hubs-about.md)
* [Event Grid](../event-grid/overview.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

Punkty końcowe są dołączone do usługi Azure Digital bliźniaczych reprezentacji przy użyciu interfejsów API zarządzania lub Azure Portal. Dowiedz się więcej na temat sposobu dołączania punktu końcowego do usługi Azure Digital bliźniaczych reprezentacji w temacie [*How to: Manage Endpoints and Routes*](how-to-manage-routes-apis-cli.md).

Istnieje wiele innych usług, w których możesz chcieć ostatecznie skierować dane, takie jak [usługa Azure Storage](../storage/common/storage-introduction.md), [Azure Maps](../azure-maps/about-azure-maps.md)lub [Time Series Insights](../time-series-insights/time-series-insights-update-overview.md). Aby wysłać dane do usług takich jak te, Dołącz usługę docelową do punktu końcowego.

Na przykład, jeśli używasz również Azure Maps i chcesz skorelować lokalizację z [grafem wieloosiowym](concepts-twins-graph.md)usługi Azure Digital bliźniaczych reprezentacji, możesz użyć Azure Functions z Event Grid, aby nawiązać komunikację między wszystkimi usługami w danym wdrożeniu. Dowiedz się więcej na ten temat na ten temat [ *: korzystanie z usługi Azure Digital bliźniaczych reprezentacji do aktualizowania Azure Mapsj mapy pomieszczeń*](how-to-integrate-maps.md)

Możesz również dowiedzieć się, jak kierować dane w podobny sposób do Time Series Insights, w poradach [*: integracja z Time Series Insights*](how-to-integrate-time-series-insights.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o punktach końcowych i zdarzeniach routingu do usług zewnętrznych:
* [*Koncepcje: kierowanie zdarzeń usługi Azure Digital bliźniaczych reprezentacji*](concepts-route-events.md)

Zobacz, jak skonfigurować usługę Azure Digital bliźniaczych reprezentacji do pozyskiwania danych z IoT Hub:
* [*Instrukcje: pozyskiwanie danych telemetrycznych z IoT Hub*](how-to-ingest-iot-hub-data.md)
