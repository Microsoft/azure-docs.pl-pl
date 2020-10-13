---
title: Monitoruj aplikacje bez zmian w kodzie — funkcja autoinstrumentacja dla Azure Monitor Application Insights | Microsoft Docs
description: Omówienie funkcji autoinstrumentacji Azure Monitor Application Insights — zarządzanie wydajnością aplikacji bezkodowej
ms.topic: conceptual
author: MS-jgol
ms.author: jgol
ms.date: 05/31/2020
ms.reviewer: mbullwin
ms.openlocfilehash: 234f772b4a77b780f0b0c1faf307c93847e5caf2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87310722"
---
# <a name="what-is-auto-instrumentation-or-codeless-attach---azure-monitor-application-insights"></a>Co to jest autoinstrumentation lub bezkodowe Azure Monitor Application Insights?

Funkcja autoinstrumentacja lub dołączanie bez kodu umożliwia włączanie monitorowania aplikacji przy użyciu Application Insights bez konieczności zmiany kodu.  

Application Insights jest zintegrowana z różnymi dostawcami zasobów i działa w różnych środowiskach. W zasadzie wszystkie czynności, które należy wykonać, są włączone i w niektórych przypadkach — skonfigurowanie agenta, który będzie zbierać dane telemetryczne z pola. W żadnym momencie zobaczysz metryki, dane i zależności w zasobie Application Insights, co umożliwi wyświetlenie źródła potencjalnych problemów przed ich wystąpieniem i przeanalizowanie głównej przyczyny przy użyciu kompleksowego widoku transakcji.

## <a name="supported-environments-languages-and-resource-providers"></a>Obsługiwane środowiska, Języki i dostawcy zasobów

W miarę dodawania większej liczby integracji macierz funkcji autoinstrumentacja jest złożona. W poniższej tabeli przedstawiono bieżący stan sprawy, jak to jest pomoc techniczna dla różnych dostawców zasobów, języków i środowisk.

|Dostawca środowisk/zasobów | .NET            | .NET Core       | Java            | Node.js         |
|------------------------------|-----------------|-----------------|-----------------|-----------------|
|Azure App Service w systemie Windows  | GA, OnBD *       | GA, zgoda      | Prywatna wersja zapoznawcza | Prywatna wersja zapoznawcza |
|Azure App Service w systemie Linux    | Nie dotyczy             | Nieobsługiwane   | Publiczna wersja zapoznawcza  | Publiczna wersja zapoznawcza  |
|Azure App Service w AKS      | Nie dotyczy             | W projekcie       | W projekcie       | W projekcie       |
|Azure Functions — podstawowa       | GA, OnBD *       | GA, OnBD *       | GA, OnBD *       | GA, OnBD *       |
|Azure Functions — zależności| Nieobsługiwane   | Nieobsługiwane   | Publiczna wersja zapoznawcza  | Nieobsługiwane   |
|Azure Kubernetes Service      | Nie dotyczy             | W projekcie       | Za poorednictwem agenta   | W projekcie       |
|Okna maszyn wirtualnych platformy Azure             | Publiczna wersja zapoznawcza  | Nieobsługiwane   | Nieobsługiwane   | Nieobsługiwane   |
|Lokalne okna maszyn wirtualnych       | GA, zgoda      | Nieobsługiwane   | Za poorednictwem agenta   | Nieobsługiwane   |
|Autonomiczny Agent — dowolna ENV.   | Nieobsługiwane   | Nieobsługiwane   | Publiczna wersja zapoznawcza  | Nieobsługiwane   |

* OnBD jest domyślnie skrócona — Application Insights zostanie włączona automatycznie po wdrożeniu aplikacji w obsługiwanych środowiskach. 

## <a name="azure-app-service"></a>Azure App Service

### <a name="windows"></a>Windows

Funkcja [monitorowania aplikacji na Azure App Service](./azure-web-apps.md?tabs=net) jest dostępna dla aplikacji .NET i jest włączona domyślnie. można włączyć platformę .NET Core za pomocą jednego kliknięcia, a Java i Node.js są w prywatnej wersji zapoznawczej.

### <a name="linux"></a>Linux 

Monitorowanie aplikacji Java i Node.js w App Service jest w publicznej wersji zapoznawczej i może być włączone w Azure Portal, dostępne we wszystkich regionach.

## <a name="azure-functions"></a>Azure Functions

Podstawowe monitorowanie Azure Functions jest domyślnie włączone w celu zbierania danych dotyczących dzienników, wydajności, błędów i żądań HTTP. W przypadku aplikacji Java można włączyć zaawansowane monitorowanie za pomocą rozproszonego śledzenia i uzyskać szczegółowe informacje o transakcjach. Ta funkcja dla języka Java jest w publicznej wersji zapoznawczej i można [ją włączyć w Azure Portal](./monitor-functions.md).

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Instrumentacja bezkodowa usługi Azure Kubernetes Service jest obecnie dostępna dla aplikacji Java za pomocą [autonomicznego agenta](./java-in-process-agent.md). 

## <a name="azure-windows-vms-and-virtual-machine-scale-set"></a>Maszyny wirtualne z systemem Windows Azure i zestaw skalowania maszyn wirtualnych

[Funkcja automatycznego oprzyrządowania dla maszyn wirtualnych platformy Azure i zestawu skalowania maszyn wirtualnych](./azure-vm-vmss-apps.md) jest dostępna dla aplikacji platformy .NET 

## <a name="on-premises-servers"></a>Serwery lokalne
Możesz łatwo włączyć monitorowanie [lokalnych serwerów z systemem Windows dla aplikacji platformy .NET](./status-monitor-v2-overview.md) i aplikacji [Java](./java-in-process-agent.md).

## <a name="other-environments"></a>Inne środowiska
Uniwersalny Agent autonomiczny Java działa w dowolnym środowisku, nie ma potrzeby Instrumentacji kodu. [Postępuj zgodnie z przewodnikiem,](./java-in-process-agent.md) aby włączyć Application Insights i zapoznać się z niezwykłymi możliwościami agenta Java. Agent jest w publicznej wersji zapoznawczej i jest dostępny we wszystkich regionach. 

## <a name="next-steps"></a>Następne kroki

* [Przegląd Application Insights](./app-insights-overview.md)
* [Mapa aplikacji](./app-map.md)
* [Kompleksowe monitorowanie wydajności](../learn/tutorial-performance.md)

