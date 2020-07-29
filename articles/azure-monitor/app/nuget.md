---
title: Azure Monitor Application Insights pakietów NuGet
description: Azure Monitor Application Insights listy pakietów NuGet dla ASP.NET, ASP.NET Core, Python
ms.topic: reference
ms.date: 10/16/2018
ms.openlocfilehash: 27a3d89b4a64de159535d346641c21616833e21b
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87309957"
---
# <a name="application-insights-nuget-packages"></a>Application Insights pakietów NuGet

Poniżej znajduje się bieżąca lista stabilnych pakietów NuGet wydania dla Application Insights.

## <a name="common-packages-for-aspnet"></a>Popularne pakiety dla ASP.NET

| Nazwa pakietu | Stabilna wersja | Opis | Pobierz |
|-------------------------------|-----------------------|------------|----|
| Microsoft. ApplicationInsights | 2.12.0 | Zapewnia podstawowe funkcje przesyłania wszystkich typów telemetria usługi Application Insights i jest pakietem zależnym dla wszystkich innych pakietów Application Insights | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
|Microsoft. ApplicationInsights. Agent. przechwycenie | 2.4.0 | Włącza przechwycenie wywołań metod | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent.Intercept/) |
| Microsoft. ApplicationInsights. DependencyCollector | 2.12.0 | Application Insights moduł zbierający zależności dla aplikacji .NET. Jest to pakiet zależny dla Application Insights pakietów specyficznych dla platformy i zapewnia automatyczne zbieranie danych telemetrycznych zależności. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft. ApplicationInsights. PerfCounterCollector | 2.12.0 | Moduł zbierający liczników wydajności Application Insights umożliwia wysyłanie danych zbieranych przez liczniki wydajności do Application Insights. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft. ApplicationInsights. Web | 2.12.0 | Application Insights dla aplikacji sieci Web platformy .NET | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) |
| Microsoft. ApplicationInsights. WindowsServer | 2.12.0 | Pakiet NuGet systemu Windows Server Application Insights udostępnia Automatyczne zbieranie danych telemetrycznych usługi Application Insights dla aplikacji .NET. Ten pakiet może być używany jako pakiet zależny dla Application Insights pakietów specyficznych dla platformy lub jako pakiet autonomiczny dla aplikacji platformy .NET, które nie są objęte przez pakiety specyficzne dla platformy (takie jak dla ról procesów roboczych platformy .NET). | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  
| Microsoft. ApplicationInsights. WindowsServer. TelemetryChannel | 2.12.0 | Udostępnia kanał telemetrii do Application Insights Windows Server SDK, który będzie przechowywać dane telemetryczne w scenariuszach w trybie offline. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-aspnet-core"></a>Popularne pakiety dla ASP.NET Core

| Nazwa pakietu | Stabilna wersja | Opis | Pobierz |
|-------------------------------|-----------------------|------------|----|
| Microsoft. ApplicationInsights. AspNetCore | 2.5.0 | Application Insights dla ASP.NET Core aplikacji sieci Web. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AspNetCore/) |
| Microsoft. ApplicationInsights | 2.12.0 | Ten pakiet zapewnia podstawowe funkcje przesyłania wszystkich typów telemetria usługi Application Insights i jest pakietem zależnym dla wszystkich innych pakietów Application Insights | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) |
| Microsoft. ApplicationInsights. DependencyCollector | 2.12.0 | Application Insights moduł zbierający zależności dla aplikacji .NET. Jest to pakiet zależny dla Application Insights pakietów specyficznych dla platformy i zapewnia automatyczne zbieranie danych telemetrycznych zależności. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector/) |
| Microsoft. ApplicationInsights. PerfCounterCollector | 2.12.0 | Moduł zbierający liczników wydajności Application Insights umożliwia wysyłanie danych zbieranych przez liczniki wydajności do Application Insights. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector/) |
| Microsoft. ApplicationInsights. WindowsServer | 2.12.0 | Pakiet NuGet systemu Windows Server Application Insights udostępnia Automatyczne zbieranie danych telemetrycznych usługi Application Insights dla aplikacji .NET. Ten pakiet może być używany jako pakiet zależny dla Application Insights pakietów specyficznych dla platformy lub jako pakiet autonomiczny dla aplikacji platformy .NET, które nie są objęte przez pakiety specyficzne dla platformy (takie jak dla ról procesów roboczych platformy .NET). | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/)  |
| Microsoft. ApplicationInsights. WindowsServer. TelemetryChannel | 2.12.0 | Udostępnia kanał telemetrii do Application Insights Windows Server SDK, który będzie przechowywać dane telemetryczne w scenariuszach w trybie offline. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel/) |

## <a name="common-packages-for-python-using-opencensus"></a>Popularne pakiety dla języka Python za pomocą OpenCensus
| Nazwa pakietu | Stabilna wersja | Opis | Pobierz |
|-------------------------------|-----------------------|------------|----|
| opencensus-EXT-Azure | 1.0.0 | Application Insights dla aplikacji języka Python w Azure Monitor za pośrednictwem OpenCensus. | [Pobierz pakiet](https://pypi.org/project/opencensus-ext-azure/) |
| opencensus-EXT-Django | 0.7.2 | Ten pakiet zapewnia integrację z biblioteką [Django](https://pypi.org/project/django/) języka Python. | [Pobierz pakiet](https://pypi.org/project/opencensus-ext-django/) |
| opencensus — Kolba stożkowa | 0.7.3 | Ten pakiet zapewnia integrację z biblioteką [kolby](https://pypi.org/project/flask/) języka Python. | [Pobierz pakiet](https://pypi.org/project/opencensus-ext-flask/) |
| opencensus-EXT-httplib | 0.7.2 | Ten pakiet zapewnia integrację z biblioteką [http](https://docs.python.org/3/library/http.client.html) języka Python dla python3 i [httplib](https://docs.python.org/2/library/httplib.html) dla python2. | [Pobierz pakiet](https://pypi.org/project/opencensus-ext-httplib/) |
| opencensus — rejestrowanie zewnętrzne | 0.1.0 | Ten pakiet wzbogaca rekordy dziennika o dane śledzenia. | [Pobierz pakiet](https://pypi.org/project/opencensus-ext-logging/) |
| opencensus — EXT — MySQL | 0.1.2 | Ten pakiet zapewnia integrację z biblioteką programu python [MySQL-Connector](https://pypi.org/project/mysql-connector/) . | [Pobierz pakiet](https://pypi.org/project/opencensus-ext-mysql/) |
| opencensus-EXT-PostgreSQL | 0.1.2 | Ten pakiet zapewnia integrację z biblioteką [psycopg2](https://pypi.org/project/psycopg2/) języka Python. | [Pobierz pakiet](https://pypi.org/project/opencensus-ext-postgresql/) |
| opencensus-EXT-pymongo | 0.7.1 | Ten pakiet zapewnia integrację z biblioteką [pymongo](https://pypi.org/project/pymongo/) języka Python. | [Pobierz pakiet](https://pypi.org/project/opencensus-ext-pymongo/) |
| opencensus-EXT-pymysql | 0.1.2 | Ten pakiet zapewnia integrację z biblioteką [PyMySQL](https://pypi.org/project/PyMySQL/) języka Python. | [Pobierz pakiet](https://pypi.org/project/opencensus-ext-pymysql/) |
| opencensus-roz — ostrosłup | 0.7.1 | Ten pakiet zapewnia integrację z biblioteką [ostrosłupową](https://pypi.org/project/pyramid/) języka Python. | [Pobierz pakiet](https://pypi.org/project/opencensus-ext-pyramid/) |
| opencensus — żądania | 0.7.2 | Ten pakiet zapewnia integrację z biblioteką [żądań](https://pypi.org/project/requests/) języka Python. | [Pobierz pakiet](https://pypi.org/project/opencensus-ext-requests/) |
| opencensus-EXT-SQLAlchemy | 0.1.2 | Ten pakiet zapewnia integrację z biblioteką [SQLAlchemy](https://pypi.org/project/SQLAlchemy/) języka Python. | [Pobierz pakiet](https://pypi.org/project/opencensus-ext-sqlalchemy/) |

## <a name="listenerscollectorsappenders"></a>Detektory/moduły zbierające/dołączania

| Nazwa pakietu | Stabilna wersja | Opis | Pobierz |
|-------------------------------|-----------------------|------------|----|
| Microsoft. ApplicationInsights. DiagnosticSourceListener | 2.7.2 |  Umożliwia przekazywanie zdarzeń z DiagnosticSource do Application Insights. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/) |
| Microsoft. ApplicationInsights. EventSourceListener | 2.7.2 | Application Insights EventSourceListener umożliwia wysyłanie danych ze zdarzeń EventSource do Application Insights. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/) |
| Microsoft. ApplicationInsights. EtwCollector | 2.7.2 | Application Insights EtwCollector umożliwia wysyłanie danych ze śledzenia zdarzeń systemu Windows (ETW) do Application Insights. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/) |
| Microsoft. ApplicationInsights. TraceListener | 2.7.2 | Niestandardowy TraceListener umożliwia wysyłanie komunikatów dziennika śledzenia do Application Insights. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/) |
| Microsoft. ApplicationInsights. Log4NetAppender | 2.7.2 | Niestandardowe dołączenie umożliwiające wysyłanie komunikatów dziennika Log4Net do Application Insights. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
| Microsoft. ApplicationInsights. NLogTarget | 2.7.2 |  niestandardowy obiekt docelowy, który umożliwia wysyłanie komunikatów dziennika NLog do Application Insights. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
| Microsoft. ApplicationInsights. SnapshotCollector | 1.3.1 | Monitoruje wyjątki w aplikacji i automatycznie zbiera migawki do analizy w trybie offline. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/)

## <a name="service-fabric"></a>Service Fabric

| Nazwa pakietu | Stabilna wersja | Opis | Pobierz |
|-------------------------------|-----------------------|------------|----|
| Microsoft. ApplicationInsights. servicefabric | 2.2.0 | Ten pakiet udostępnia automatyczne dekoracje telemetrii z kontekstem usługi Service Fabric, w którym jest uruchomiona aplikacja. Nie używaj tego NuGet dla natywnych aplikacji Service Fabric. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric/) |
| Microsoft. ApplicationInsights. servicefabric. Native | 2.2.0 | Moduł Application Insights dla aplikacji usługi Service Fabric. Użyj tego NuGet tylko dla natywnych aplikacji Service Fabric. W przypadku aplikacji uruchamianych w kontenerach Użyj pakietu Microsoft. ApplicationInsights. servicefabric. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.ServiceFabric.Native/) |  

## <a name="status-monitor"></a>Monitor stanu

| Nazwa pakietu | Stabilna wersja | Opis | Pobierz |
|-------------------------------|-----------------------|------------|----|
| Microsoft. ApplicationInsights. Agent_x64 | 2.2.1 |  Włącza zbieranie danych środowiska uruchomieniowego dla aplikacji x64 | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x64/) |
| Microsoft. ApplicationInsights. Agent_x86 | 2.2.1 |  Włącza zbieranie danych środowiska uruchomieniowego dla aplikacji x86. | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Agent_x86/) |

Te pakiety składają się na część podstawowych funkcji monitorowania środowiska uruchomieniowego w [Monitor stanu](./monitor-performance-live-website-now.md). Nie musisz bezpośrednio pobierać tych pakietów, korzystając z Instalatora monitor stanu. Jeśli chcesz dowiedzieć się więcej o tym, jak te pakiety działają na wyciągu, ten [wpis w blogu](https://apmtips.com/posts/2016-11-18-how-application-insights-status-monitor-not-monitors-dependencies/) z jednego z naszych deweloperów jest dobrym startem.

## <a name="additional-packages"></a>Dodatkowe pakiety

| Nazwa pakietu | Stabilna wersja | Opis | Pobierz |
|-------------------------------|-----------------------|------------|----|
| Microsoft. ApplicationInsights. AzureWebSites | 2.6.5 | To rozszerzenie włącza Application Insights monitorowania na Azure App Service. Wersja zestawu SDK. Instrukcje: Dodaj ustawienia aplikacji "APPINSIGHTS_INSTRUMENTATIONKEY" przy użyciu iKey i ponownie uruchom webapp, aby zastosować efekt.| [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.AzureWebSites/) |
| Microsoft. ApplicationInsights. wtryskiwacz | 2.6.7 | Ten pakiet zawiera pliki wymagane do iniekcji bezkodu Application Insights | [Pobierz pakiet](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Injector/) |

## <a name="next-steps"></a>Następne kroki

- Monitoruj [ASP.NET Core](./asp-net-core.md).
- Profile ASP.NET Core [aplikacje sieci Web platformy Azure](./profiler-aspnetcore-linux.md)w systemie Linux.
- Debuguj [migawki](./snapshot-debugger.md)ASP.NET.

