---
title: Monitorowanie na poziomie aplikacji Service Fabric platformy Azure
description: Dowiedz się więcej o zdarzeniach i dziennikach na poziomie aplikacji i usług oraz dzienniki używane do monitorowania i diagnozowania klastrów Service Fabric platformy Azure.
author: srrengar
ms.topic: conceptual
ms.date: 11/21/2018
ms.author: srrengar
ms.openlocfilehash: a1df5d033701195f4fe5f6b7174f3883b84393ab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791277"
---
# <a name="application-logging"></a>Rejestrowanie aplikacji

Instrumentacja kodu nie jest tylko sposobem na uzyskanie wglądu w informacje o użytkownikach, ale również to, w jaki sposób można sprawdzić, czy coś w aplikacji jest niewłaściwe, i zdiagnozować, co należy naprawić. Chociaż technicznie istnieje możliwość połączenia debugera z usługą produkcyjną, nie jest to typowa Metoda. Dlatego szczegółowe dane Instrumentacji są ważne.

Niektóre produkty automatycznie wyposażają swój kod. Chociaż te rozwiązania mogą działać prawidłowo, Instrumentacja ręczna jest prawie zawsze wymagana dla logiki biznesowej. Na koniec musisz mieć wystarczającą ilość informacji, aby forensically debugowanie aplikacji. Aplikacje Service Fabric mogą być Instrumentacją z dowolnymi strukturami rejestrowania. W tym dokumencie opisano kilka różnych metod służących do Instrumentacji kodu oraz kiedy należy wybrać jedno podejście na inne. 

Aby zapoznać się z przykładami dotyczącymi sposobu korzystania z tych sugestii, zobacz [Dodawanie rejestrowania do aplikacji Service Fabric](service-fabric-how-to-diagnostics-log.md).

## <a name="application-insights-sdk"></a>Zestaw SDK Application Insights

Application Insights ma rozbudowaną integrację z Service Fabricem. Użytkownicy mogą dodawać pakiety NuGet Service Fabric AI i odbierać dane oraz dzienniki utworzone i zebrane w Azure Portal. Ponadto użytkownicy są zachęcani do dodawania własnych danych telemetrycznych w celu diagnozowania i debugowania aplikacji oraz śledzenia, które usługi i części ich aplikacji są używane. Klasa [TelemetryClient](/dotnet/api/microsoft.applicationinsights.telemetryclient) w zestawie SDK zapewnia wiele sposobów śledzenia danych telemetrycznych w aplikacjach. Zapoznaj się z przykładem, jak instrumentować i dodawać usługi Application Insights do aplikacji w naszym samouczku dotyczącym [monitorowania i diagnozowania aplikacji .NET](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="eventsource"></a>EventSource

W przypadku tworzenia rozwiązania Service Fabric na podstawie szablonu w programie Visual Studio generowana jest Klasa pochodna elementu **EventSource** lub **ActorEventSource**). Tworzony jest szablon, w którym można dodawać zdarzenia dla aplikacji lub usługi. Nazwa  elementu EventSource **musi** być unikatowa i powinna być zmieniona z domyślnego ciągu szablonu &lt; &gt; - &lt; &gt; . Posiadanie wielu definicji elementu **EventSource** , które używają tej samej nazwy, powoduje problem w czasie wykonywania. Każde zdefiniowane zdarzenie musi mieć unikatowy identyfikator. Jeśli identyfikator nie jest unikatowy, wystąpi błąd w czasie wykonywania. Niektóre organizacje często przypisują zakresy wartości dla identyfikatorów, aby uniknąć konfliktów między oddzielnymi zespołami programistycznymi. Aby uzyskać więcej informacji, zobacz [blog Vance](/archive/blogs/vancem/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource) lub [dokumentację MSDN](/previous-versions/msp-n-p/dn774985(v=pandp.20)).

## <a name="aspnet-core-logging"></a>Rejestrowanie ASP.NET Core

Ważne jest, aby dokładnie zaplanować sposób Instrumentacji kodu. Odpowiedni plan Instrumentacji może pomóc uniknąć potencjalnie destabilizacji podstawy kodu, a następnie konieczność odtworzenia kodu. Aby zmniejszyć ryzyko, można wybrać bibliotekę instrumentacji, taką jak [Microsoft. Extensions. Logging](https://www.nuget.org/packages/Microsoft.Extensions.Logging/), która jest częścią Microsoft ASP.NET Core. ASP.NET Core ma interfejs [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) , którego można używać z wybranym przez siebie dostawcą, jednocześnie minimalizując wpływ na istniejący kod. Możesz użyć kodu w ASP.NET Core w systemach Windows i Linux oraz w pełnych .NET Framework, dzięki czemu kod Instrumentacji jest znormalizowany.

## <a name="next-steps"></a>Następne kroki

Po wybraniu dostawcy rejestrowania do instrumentowania aplikacji i usług dzienniki i zdarzenia muszą zostać zagregowane, zanim będą mogły być wysyłane do dowolnej platformy analizy. Przeczytaj informacje o [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) i [użyciu struktury eventflow](service-fabric-diagnostics-event-aggregation-eventflow.md) , aby lepiej zrozumieć niektóre Azure monitor zalecane opcje.
