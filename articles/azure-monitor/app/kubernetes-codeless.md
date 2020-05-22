---
title: Monitoruj aplikacje w usłudze Azure Kubernetes Service (AKS) przy użyciu Application Insights-Azure Monitor | Microsoft Docs
description: Azure Monitor bezproblemowo integruje się z aplikacją działającą w systemie Kubernetes i umożliwia użytkownikom rozwiązywanie problemów z aplikacjami.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/13/2020
ms.openlocfilehash: 76f9f922697ef6be6c959ea7f9bafd0872dba6ff
ms.sourcegitcommit: 318d1bafa70510ea6cdcfa1c3d698b843385c0f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83773768"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes---azure-monitor-application-insights"></a>Zero monitorowania aplikacji Instrumentacji dla Kubernetes Azure Monitor Application Insights

> [!IMPORTANT]
>  Obecnie można włączyć monitorowanie aplikacji Java działających w systemie Kubernetes bez Instrumentacji kodu — Użyj [autonomicznego agenta Java](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent). Chociaż rozwiązanie do bezproblemowego włączania monitorowania aplikacji działa w innych językach, użyj zestawów SDK do monitorowania aplikacji działających w AKS: [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core), [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net), [Node. js](https://docs.microsoft.com/azure/azure-monitor/app/nodejs), [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript)i [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python).

## <a name="application-monitoring-without-instrumenting-the-code"></a>Monitorowanie aplikacji bez Instrumentacji kodu
Obecnie tylko język Java umożliwia włączenie monitorowania aplikacji bez Instrumentacji kodu. Aby monitorować aplikacje w innych językach, użyj zestawów SDK. 

## <a name="java"></a>Java
Po włączeniu Agent Java będzie automatycznie zbierać wiele żądań, zależności, dzienniki i metryki z najczęściej używanych bibliotek i struktur.

Postępuj zgodnie [ze szczegółowymi instrukcjami](https://docs.microsoft.com/azure/azure-monitor/app/java-in-process-agent) , aby monitorować aplikacje Java działające w aplikacjach Kubernetes, a także inne środowiska. 

## <a name="other-languages"></a>Inne języki

W przypadku aplikacji w innych językach zalecamy korzystanie z zestawów SDK:
* [ASP.NET Core](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)
* [ASP.NET](https://docs.microsoft.com/azure/azure-monitor/app/asp-net)
* [Node.js](https://docs.microsoft.com/azure/azure-monitor/app/nodejs) 
* [JavaScript](https://docs.microsoft.com/azure/azure-monitor/app/javascript)
* [Python](https://docs.microsoft.com/azure/azure-monitor/app/opencensus-python)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) i [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview)
* Zapoznaj się z omówieniem [rozproszonego śledzenia](https://docs.microsoft.com/azure/azure-monitor/app/distributed-tracing) i zobacz, jakie [Mapowanie aplikacji](https://docs.microsoft.com/azure/azure-monitor/app/app-map?tabs=net) może być dla Twojej firmy