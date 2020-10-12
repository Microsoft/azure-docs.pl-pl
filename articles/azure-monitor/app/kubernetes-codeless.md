---
title: Monitoruj aplikacje w usłudze Azure Kubernetes Service (AKS) przy użyciu Application Insights-Azure Monitor | Microsoft Docs
description: Azure Monitor bezproblemowo integruje się z aplikacją działającą w systemie Kubernetes i umożliwia użytkownikom rozwiązywanie problemów z aplikacjami.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/13/2020
ms.openlocfilehash: 52bd6d2a98e5126ff2463de1ef99da03ca555567
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87075311"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes---azure-monitor-application-insights"></a>Zero monitorowania aplikacji Instrumentacji dla Kubernetes Azure Monitor Application Insights

> [!IMPORTANT]
>  Obecnie można włączyć monitorowanie aplikacji Java działających w systemie Kubernetes bez Instrumentacji kodu — Użyj [autonomicznego agenta Java](./java-in-process-agent.md). Chociaż rozwiązanie do bezproblemowego włączania monitorowania aplikacji działa w innych językach, użyj zestawów SDK do monitorowania aplikacji działających w AKS: [ASP.NET Core](./asp-net-core.md), [ASP.NET](./asp-net.md), [Node.js](./nodejs.md), [JavaScript](./javascript.md)i [Python](./opencensus-python.md).

## <a name="application-monitoring-without-instrumenting-the-code"></a>Monitorowanie aplikacji bez Instrumentacji kodu
Obecnie tylko język Java umożliwia włączenie monitorowania aplikacji bez Instrumentacji kodu. Aby monitorować aplikacje w innych językach, użyj zestawów SDK. 

## <a name="java"></a>Java
Po włączeniu Agent Java będzie automatycznie zbierać wiele żądań, zależności, dzienniki i metryki z najczęściej używanych bibliotek i struktur.

Postępuj zgodnie [ze szczegółowymi instrukcjami](./java-in-process-agent.md) , aby monitorować aplikacje Java działające w aplikacjach Kubernetes, a także inne środowiska. 

## <a name="other-languages"></a>Inne języki

W przypadku aplikacji w innych językach zalecamy korzystanie z zestawów SDK:
* [ASP.NET Core](./asp-net-core.md)
* [ASP.NET](./asp-net.md)
* [Node.js](./nodejs.md) 
* [JavaScript](./javascript.md)
* [Python](./opencensus-python.md)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [Azure monitor](../overview.md) i [Application Insights](./app-insights-overview.md)
* Zapoznaj się z omówieniem [rozproszonego śledzenia](./distributed-tracing.md) i zobacz, jakie [Mapowanie aplikacji](./app-map.md?tabs=net) może być dla Twojej firmy
