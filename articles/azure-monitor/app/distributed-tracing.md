---
title: Śledzenie rozproszone na platformie Azure Application Insights | Microsoft Docs
description: Zawiera informacje o pomocy technicznej firmy Microsoft w zakresie śledzenia rozproszonego przez nasze partnerstwo w projekcie OpenCensus
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: nikmd23
ms.author: nimolnar
ms.date: 09/17/2018
ms.reviewer: mbullwin
ms.openlocfilehash: a47b41a8b7f4e18be58c32c97cf279b9229f26da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579506"
---
# <a name="what-is-distributed-tracing"></a>Co to jest śledzenie rozproszone?

Pojawieniu nowoczesnej architektury chmury i [mikrousług](https://azure.com/microservices) ma podstawę prostych, niezależnie wdrażanych usług, które mogą pomóc w obniżeniu kosztów przy jednoczesnym zwiększeniu dostępności i przepływności. Jednak chociaż te przesunięcia zostały łatwiejsze do zrozumienia w całości, są one bardziej trudne do odłożenia i debugowania.

W przypadku architektur monolitycznych wykorzystamy do debugowania z stosów wywołań. Stosy wywołań to doskonały narzędzia do wyświetlania przepływu wykonania (Metoda wywołana metoda B, która nazywana metodą C), wraz ze szczegółami i parametrami dotyczącymi każdego z tych wywołań. Jest to doskonałe rozwiązanie dla monolitów lub usług działających w ramach jednego procesu, ale jak debugować, gdy wywołanie odbywa się na granicy procesu, a nie po prostu odwołaniem do lokalnego stosu? 

Jest to miejsce, w którym nastąpi śledzenie rozproszone.  

Śledzenie rozproszone to odpowiednik stosów wywołań dla nowoczesnych architektur chmury i mikrousług, z dodaniem profilera wydajności uproszczony w systemie. W Azure Monitor udostępniamy dwa środowiska do konsumowania dystrybuowanych danych śledzenia. Pierwszy to nasz widok [diagnostyki transakcji](./transaction-diagnostics.md) , który przypomina stos wywołań z wymiarem czasu dodanym w. Widok Diagnostyka transakcji umożliwia wgląd w jedną transakcję/żądanie i ułatwia znalezienie głównej przyczyny problemów z niezawodnością oraz wąskich gardeł wydajności na podstawie żądania.

Azure Monitor oferuje również widok [mapy aplikacji](./app-map.md) , który agreguje wiele transakcji, aby pokazać widok topologiczny, w jaki sposób działają systemy, oraz obliczyć średnią wydajność i częstotliwość błędów. 

## <a name="how-to-enable-distributed-tracing"></a>Jak włączyć śledzenie rozproszone

Włączenie śledzenia rozproszonego w ramach usług w aplikacji jest tak proste jak dodanie odpowiedniego agenta, zestawu SDK lub biblioteki do każdej usługi na podstawie języka, w którym usługa została zaimplementowana.

## <a name="enabling-via-application-insights-through-auto-instrumentation-or-sdks"></a>Włączanie za pośrednictwem Application Insights za pomocą funkcji autoinstrumentation lub SDK

Application Insights agenci i/lub zestawy SDK dla platform .NET, .NET Core, Java, Node.js i JavaScript obsługują śledzenie rozproszone natywnie. Instrukcje dotyczące instalowania i konfigurowania każdego zestawu Application Insights SDK są dostępne poniżej:

* [.NET](asp-net.md)
* [.NET Core](asp-net-core.md)
* [Java](./java-in-process-agent.md)
* [Node.js](../app/nodejs-quick-start.md)
* [JavaScript](./javascript.md)
* [Python](opencensus-python.md)

Po zainstalowaniu i skonfigurowaniu odpowiedniego zestawu Application Insights SDK informacje o śledzeniu są automatycznie zbierane dla popularnych struktur, bibliotek i technologii przez automatyczne zbieranie zależności zestawu SDK. Pełną listę obsługiwanych technologii można znaleźć w dokumentacji dotyczącej [autozbierania zależności](./auto-collect-dependencies.md).

 Ponadto jakakolwiek technologia może być śledzona ręcznie z wywołaniem [TrackDependency](./api-custom-events-metrics.md) na [TelemetryClient](./api-custom-events-metrics.md).

## <a name="enable-via-opencensus"></a>Włącz za pośrednictwem OpenCensus

Oprócz zestawów SDK Application Insights, Application Insights obsługuje również śledzenie rozproszone za pomocą [OpenCensus](https://opencensus.io/). OpenCensus to model typu open source, Vendor-niezależny od, pojedynczej dystrybucji bibliotek, aby zapewnić zbieranie metryk i rozproszone śledzenie dla usług. Umożliwia również społeczności typu "open source" Włączenie śledzenia rozproszonego przy użyciu popularnych technologii, takich jak Redis, Memcached lub MongoDB. [Firma Microsoft współpracuje z usługą OpenCensus przy użyciu kilku innych partnerów monitorujących i w chmurze](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/).

[Python](opencensus-python.md) 

Witryna sieci Web OpenCensus przechowuje dokumentację referencyjną interfejsu API dla języka [Python](https://opencensus.io/api/python/trace/usage.html) i języka [go](https://godoc.org/go.opencensus.io), a także różne różne przewodniki dotyczące korzystania z OpenCensus. 

## <a name="next-steps"></a>Następne kroki

* [Przewodnik po użyciu języka Python OpenCensus](https://opencensus.io/api/python/trace/usage.html)
* [Mapa aplikacji](./app-map.md)
* [Kompleksowe monitorowanie wydajności](../app/tutorial-performance.md)

