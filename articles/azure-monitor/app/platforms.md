---
title: 'Application Insights: Języki, platformy i integracje | Microsoft Docs'
description: Języki, platformy i integracje dostępne dla Application Insights
ms.topic: conceptual
ms.date: 07/18/2019
ms.reviewer: olegan
ms.openlocfilehash: a5403162a2511862dd1c649dc273a35a550abaaf
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87024286"
---
# <a name="supported-languages"></a>Obsługiwane języki

* [C#|VB (.NET)](../../azure-monitor/app/asp-net.md)
* [Java](../../azure-monitor/app/java-get-started.md)
* [JavaScript](../../azure-monitor/app/javascript.md)
* [Node.JS](../../azure-monitor/app/nodejs.md)
* [Python](../../azure-monitor/app/opencensus-python.md)

## <a name="supported-platforms-and-frameworks"></a>Obsługiwane platformy i struktury

### <a name="instrumentation-for-already-deployed-applications-codeless-agent-based"></a>Instrumentacja dla już wdrożonych aplikacji (bez kodu, oparta na agentach)
* [Maszyny wirtualne platformy Azure i zestawy skalowania maszyn wirtualnych platformy Azure](../../azure-monitor/app/azure-vm-vmss-apps.md)
* [Azure App Service](../../azure-monitor/app/azure-web-apps.md)
* [ASP.NET — dla aplikacji, które już działają](../../azure-monitor/app/monitor-performance-live-website-now.md)
* [Cloud Services platformy Azure](../../azure-monitor/app/cloudservices.md), w tym role sieci Web i procesu roboczego
* [Azure Functions](../../azure-functions/functions-monitoring.md)
### <a name="instrumentation-through-code-sdks"></a>Instrumentacja poprzez kod (SDK)
* [ASP.NET](../../azure-monitor/app/asp-net.md)
* [ASP.NET Core](../../azure-monitor/app/asp-net-core.md)
* [Android](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [iOS](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Java EE](../../azure-monitor/app/java-get-started.md)
* [Node.JS](https://www.npmjs.com/package/applicationinsights)
* [Python](../../azure-monitor/app/opencensus-python.md)
* [Aplikacja uniwersalna systemu Windows](../../azure-monitor/learn/mobile-center-quickstart.md) (App Center)
* [Aplikacje klasyczne, usługi i role procesów roboczych systemu Windows](../../azure-monitor/app/windows-desktop.md)

## <a name="logging-frameworks"></a>Struktury rejestrowania
* [ILogger](./ilogger.md)
* [Log4Net, NLog lub System.Diagnostics.Trace](../../azure-monitor/app/asp-net-trace-logs.md)
* [Java, Log4J lub Logback](../../azure-monitor/app/java-trace-logs.md)
* [Dodatek LogStash](https://github.com/Azure/azure-diagnostics-tools/tree/master/Logstash/logstash-output-applicationinsights)
* [Azure Monitor](/archive/blogs/msoms/application-insights-connector-in-oms)

## <a name="export-and-data-analysis"></a>Eksportowanie i analiza danych
* [Power BI](https://powerbi.microsoft.com/blog/explore-your-application-insights-data-with-power-bi/)
* [Stream Analytics](../../azure-monitor/app/export-power-bi.md)

## <a name="unsupported-sdks"></a>Nieobsługiwane zestawy SDK
Mamy świadomość, że istnieją różne zestawy SDK obsługiwane przez społeczność. Jednak Azure Monitor zapewnia pomoc techniczną tylko w przypadku korzystania z obsługiwanych zestawów SDK wymienionych na tej stronie. Nieustannie oceniamy możliwości rozszerzania pomocy technicznej dla innych języków. Aby uzyskać najnowsze informacje o zestawie SDK, postępuj zgodnie z naszą stroną [anonsów usługi GitHub](https://github.com/microsoft/ApplicationInsights-Announcements/issues) . 
