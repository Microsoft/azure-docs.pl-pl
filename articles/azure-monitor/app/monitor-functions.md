---
title: Monitoruj aplikacje działające na Azure Functions przy użyciu Application Insights-Azure Monitor | Microsoft Docs
description: Azure Monitor bezproblemowo integruje się z aplikacją działającą na Azure Functions i pozwala na monitorowanie wydajności i rozwiązywanie problemów z aplikacjami w niepełnym czasie.
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 06/26/2020
ms.openlocfilehash: 6d790a6f02fdc07609e374639c6e452b9088262e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87024575"
---
# <a name="monitoring-azure-functions-with-azure-monitor-application-insights"></a>Azure Functions monitorowania z Azure Monitor Application Insights

[Azure Functions](../../azure-functions/functions-overview.md) oferuje wbudowaną integrację z usługą Azure Application Insights do monitorowania funkcji. 

Application Insights zbiera dane dotyczące dzienników, wydajności i błędów oraz automatycznie wykrywa anomalie wydajności. Application Insights obejmuje zaawansowane narzędzia analityczne ułatwiające diagnozowanie problemów oraz zrozumienie sposobu korzystania z funkcji. Jeśli masz wgląd w dane aplikacji, możesz stale poprawić wydajność i użyteczność. Możesz nawet używać Application Insights podczas tworzenia projektu aplikacji funkcji lokalnych. 

Wymagana Application Insights Instrumentacja jest wbudowana w Azure Functions. Jedyną potrzebną kwestią jest prawidłowy klucz Instrumentacji służący do łączenia aplikacji funkcji z zasobem Application Insights. Klucz Instrumentacji należy dodać do ustawień aplikacji podczas tworzenia zasobu aplikacji funkcji na platformie Azure. Jeśli aplikacja funkcji nie ma jeszcze tego klucza, możesz ją ustawić ręcznie. Aby uzyskać więcej informacji, Przeczytaj więcej na temat [monitorowania Azure Functions](../../azure-functions/functions-monitoring.md?tabs=cmd).

## <a name="distributed-tracing-for-java-applications-on-windows-public-preview"></a>Śledzenie rozproszone dla aplikacji Java w systemie Windows (publiczna wersja zapoznawcza)

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w publicznej wersji zapoznawczej dla Azure Functions Java w systemie Windows, śledzenie rozproszone dla Azure Functions Java w systemie Linux nie jest obsługiwane. Plan zużycia ma zimny Początek 8-9 sekund.

Jeśli aplikacje są zapisywane w języku Java, można wyświetlać bogatsze dane z aplikacji usługi Functions, takich jak żądania, zależności, dzienniki i metryki. Dodatkowe dane umożliwiają również wyświetlanie i diagnozowanie transakcji kompleksowych i wyświetlanie mapy aplikacji, która agreguje wiele transakcji, aby pokazać widok topologiczny, w jaki działają systemy, oraz obliczyć średnią wydajność i częstotliwość błędów.

Kompleksowa diagnostyka i mapa aplikacji zapewniają widoczność jednej pojedynczej transakcji/żądania. Obie te funkcje są bardzo przydatne do znajdowania głównej przyczyny problemów z niezawodnością i wąskich gardeł wydajności na podstawie żądania.

### <a name="how-to-enable-distributed-tracing-for-java-function-apps"></a>Jak włączyć śledzenie rozproszone dla aplikacji funkcji Java?

Przejdź do bloku przegląd aplikacji funkcji, przejdź do pozycji konfiguracje. W obszarze Ustawienia aplikacji kliknij pozycję "+ nowe ustawienie aplikacji". Dodaj poniższe dwa ustawienia aplikacji z poniższymi wartościami, a następnie kliknij przycisk Zapisz w lewym górnym rogu. ODBYWAĆ!

```
XDT_MicrosoftApplicationInsights_Java -> 1
ApplicationInsightsAgent_EXTENSION_VERSION -> ~2
```

## <a name="next-steps"></a>Następne kroki

* Przeczytaj więcej instrukcji i informacji na temat monitorowania monitorowania [Azure Functions](../../azure-functions/functions-monitoring.md)
* Zapoznaj się z omówieniem [śledzenia rozproszonego](./distributed-tracing.md)
* Zobacz, jakie [Mapowanie aplikacji](./app-map.md?tabs=net) może być dla Twojej firmy
* Przeczytaj informacje o [żądaniach i zależnościach dla aplikacji Java](./java-in-process-agent.md)
* Dowiedz się więcej o [Azure monitor](../overview.md) i [Application Insights](./app-insights-overview.md)
