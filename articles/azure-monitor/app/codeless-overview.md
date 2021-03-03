---
title: Monitoruj aplikacje bez zmian w kodzie — funkcja autoinstrumentacja dla Azure Monitor Application Insights | Microsoft Docs
description: Omówienie funkcji autoinstrumentacji Azure Monitor Application Insights — zarządzanie wydajnością aplikacji bezkodowej
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/31/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 9ead123338a410daf53569ff577dfc8c728a8ddf
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101708496"
---
# <a name="what-is-auto-instrumentation-or-codeless-attach---azure-monitor-application-insights"></a>Co to jest autoinstrumentation lub bezkodowe Azure Monitor Application Insights?

Funkcja autoinstrumentacja lub dołączanie bez kodu umożliwia włączanie monitorowania aplikacji przy użyciu Application Insights bez konieczności zmiany kodu.  

Application Insights jest zintegrowana z różnymi dostawcami zasobów i działa w różnych środowiskach. W zasadzie wszystkie czynności, które należy wykonać, są włączone i w niektórych przypadkach — Skonfiguruj agenta, który będzie zbierać dane telemetryczne automatycznie. W żadnym momencie zobaczysz metryki, dane i zależności w zasobie Application Insights, co umożliwi wyświetlenie źródła potencjalnych problemów przed ich wystąpieniem i przeanalizowanie głównej przyczyny przy użyciu kompleksowego widoku transakcji.

## <a name="supported-environments-languages-and-resource-providers"></a>Obsługiwane środowiska, Języki i dostawcy zasobów

Podczas dodawania dodatkowych integracji macierz funkcji autoinstrumentacja jest złożona. W poniższej tabeli przedstawiono bieżący stan sprawy, jak to jest pomoc techniczna dla różnych dostawców zasobów, języków i środowisk.

|Dostawca środowisk/zasobów          | .NET            | .NET Core       | Java            | Node.js         | Python          |
|---------------------------------------|-----------------|-----------------|-----------------|-----------------|-----------------|
|Azure App Service w systemie Windows           | GA, OnBD *       | GA, zgoda      | W toku     | W toku     | Nieobsługiwane   |
|Azure App Service w systemie Linux             | Nie dotyczy             | Nieobsługiwane   | W toku     | Publiczna wersja zapoznawcza  | Nieobsługiwane   |
|Azure App Service w AKS               | Nie dotyczy             | W projekcie       | W projekcie       | W projekcie       | Nieobsługiwane   |
|Azure Functions — podstawowa                | GA, OnBD *       | GA, OnBD *       | GA, OnBD *       | GA, OnBD *       | GA, OnBD *       |
|Azure Functions zależności systemu Windows | Nieobsługiwane   | Nieobsługiwane   | Publiczna wersja zapoznawcza  | Nieobsługiwane   | Nieobsługiwane   |
|Azure Kubernetes Service               | Nie dotyczy             | W projekcie       | Za poorednictwem agenta   | W projekcie       | Nieobsługiwane   |
|Okna maszyn wirtualnych platformy Azure                      | Publiczna wersja zapoznawcza  | Nieobsługiwane   | Nieobsługiwane   | Nieobsługiwane   | Nieobsługiwane   |
|Lokalne okna maszyn wirtualnych                | GA, zgoda      | Nieobsługiwane   | Za poorednictwem agenta   | Nieobsługiwane   | Nieobsługiwane   |
|Autonomiczny Agent — dowolna ENV.            | Nieobsługiwane   | Nieobsługiwane   | Ogólna dostępność              | Nieobsługiwane   | Nieobsługiwane   |

* OnBD jest domyślnie skrócona — Application Insights zostanie włączona automatycznie po wdrożeniu aplikacji w obsługiwanych środowiskach. 

## <a name="azure-app-service"></a>Azure App Service

### <a name="windows"></a>Windows

#### <a name="net"></a>.NET
Funkcja monitorowania aplikacji na Azure App Service w systemie Windows jest dostępna dla [aplikacji .NET](./azure-web-apps.md?tabs=net) .NET i jest domyślnie włączona.

#### <a name="netcore"></a>. NETCore
Monitorowanie [. Aplikacje podstawowe](./azure-web-apps.md?tabs=netcore) można włączyć za pomocą jednego kliknięcia.

#### <a name="java"></a>Java
Integracja portalu do monitorowania aplikacji Java w App Service w systemie Windows jest obecnie niedostępna. można jednak dodać do aplikacji [niezależny agent Application Insights Java 3,0](./java-in-process-agent.md) , bez wprowadzania zmian w kodzie przed wdrożeniem aplikacji w App Service. Agent Application Insights Java 3,0 jest ogólnie dostępny.

#### <a name="nodejs"></a>Node.js
Nie można obecnie włączyć monitorowania dla aplikacji Node.js w systemie Windows z poziomu portalu. Aby monitorować aplikacje Node.js, użyj [zestawu SDK](./nodejs.md).

### <a name="linux"></a>Linux

#### <a name="netcore"></a>. NETCore
Do monitorowania. Podstawowe aplikacje działające w systemie Linux używają [zestawu SDK](./asp-net-core.md).

#### <a name="java"></a>Java 
Włączanie monitorowania aplikacji Java dla App Service w systemie Linux z portalu nie jest dostępne, ale do aplikacji można dodać Application Insights do programu [java 3,0 Agent](./java-in-process-agent.md) przed wdrożeniem aplikacji do App Service. Agent Application Insights Java 3,0 jest ogólnie dostępny.

#### <a name="nodejs"></a>Node.js
[Monitorowanie Node.js aplikacji w App Service w systemie Linux](./azure-web-apps.md?tabs=nodejs) jest w publicznej wersji zapoznawczej i może być włączone w Azure Portal, dostępne we wszystkich regionach. 

#### <a name="python"></a>Python
Używanie zestawu SDK do [monitorowania aplikacji](./opencensus-python.md) w języku Python 

## <a name="azure-functions"></a>Azure Functions

Podstawowe monitorowanie Azure Functions jest domyślnie włączone w celu zbierania danych dotyczących dzienników, wydajności, błędów i żądań HTTP. W przypadku aplikacji Java można włączyć zaawansowane monitorowanie za pomocą rozproszonego śledzenia i uzyskać szczegółowe informacje o transakcjach. Ta funkcja dla języka Java jest w publicznej wersji zapoznawczej i można [ją włączyć w Azure Portal](./monitor-functions.md).

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Instrumentacja bezkodowa usługi Azure Kubernetes Service jest obecnie dostępna dla aplikacji Java za pomocą [autonomicznego agenta](./java-in-process-agent.md). 

## <a name="azure-windows-vms-and-virtual-machine-scale-set"></a>Maszyny wirtualne z systemem Windows Azure i zestaw skalowania maszyn wirtualnych

Funkcja automatycznego oprzyrządowania dla maszyn wirtualnych platformy Azure i zestawu skalowania maszyn wirtualnych jest dostępna dla [platform .NET](./azure-vm-vmss-apps.md) i [Java](./java-in-process-agent.md).  

## <a name="on-premises-servers"></a>Serwery lokalne
Możesz łatwo włączyć monitorowanie [lokalnych serwerów z systemem Windows dla aplikacji platformy .NET](./status-monitor-v2-overview.md) i aplikacji [Java](./java-in-process-agent.md).

## <a name="other-environments"></a>Inne środowiska
Uniwersalny Agent autonomiczny Java działa w dowolnym środowisku, nie ma potrzeby Instrumentacji kodu. [Postępuj zgodnie z przewodnikiem,](./java-in-process-agent.md) aby włączyć Application Insights i zapoznać się z niezwykłymi możliwościami agenta Java. Agent jest w publicznej wersji zapoznawczej i jest dostępny we wszystkich regionach. 

## <a name="next-steps"></a>Następne kroki

* [Przegląd Application Insights](./app-insights-overview.md)
* [Mapa aplikacji](./app-map.md)
* [Kompleksowe monitorowanie wydajności](../app/tutorial-performance.md)