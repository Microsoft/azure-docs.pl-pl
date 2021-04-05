---
title: Monitorowanie usługi Azure Functions
description: Dowiedz się, jak używać usługi Azure Application Insights z usługą Azure Functions do monitorowania wykonywania funkcji.
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.topic: conceptual
ms.date: 10/14/2020
ms.custom: devx-track-csharp, fasttrack-edit, contperf-fy21q2, devx-track-js
ms.openlocfilehash: 637f09c5ee52928631b965dfa6caea9368b44991
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99550763"
---
# <a name="monitor-azure-functions"></a>Monitorowanie usługi Azure Functions

[Azure Functions](functions-overview.md) oferuje wbudowaną integrację z [usługą Azure Application Insights](../azure-monitor/app/app-insights-overview.md) do monitorowania funkcji. Ten artykuł zawiera omówienie możliwości monitorowania udostępnianych przez platformę Azure na potrzeby monitorowania Azure Functions.

Application Insights zbiera dane o dzienniku, wydajności i błędach. Dzięki automatycznemu wykrywaniu anomalii wydajności i opracowywaniu zaawansowanych narzędzi analitycznych można łatwiej diagnozować problemy i lepiej zrozumieć sposób korzystania z funkcji. Te narzędzia zostały zaprojektowane, aby pomóc w ciągłym ulepszaniu wydajności i użyteczności funkcji. Możesz nawet używać Application Insights podczas tworzenia projektu aplikacji funkcji lokalnych. Aby uzyskać więcej informacji, zobacz [co to jest Application Insights?](../azure-monitor/app/app-insights-overview.md).

Ponieważ Instrumentacja Application Insights jest wbudowana w Azure Functions, musisz dysponować prawidłowym kluczem instrumentacji, aby połączyć aplikację funkcji z Application Insights zasobem. Klucz Instrumentacji jest dodawany do ustawień aplikacji podczas tworzenia zasobu aplikacji funkcji na platformie Azure. Jeśli aplikacja funkcji nie ma jeszcze tego klucza, możesz [ją ustawić ręcznie](configure-monitoring.md#enable-application-insights-integration).  

## <a name="application-insights-pricing-and-limits"></a>Application Insights ceny i limity

Możesz wypróbować Application Insights integrację z usługą Azure Functions bezpłatnie, aby uzyskać dzienny limit ilości danych, które są przetwarzane bezpłatnie.

Jeśli włączysz usługi Application Insights podczas opracowywania, możesz napotkać ten limit podczas testowania. Platforma Azure udostępnia powiadomienia w portalu i wiadomości e-mail po zbliżaniu się dziennego limitu. Jeśli pominięto te alerty i osiągniesz limit, nowe dzienniki nie będą wyświetlane w Application Insights zapytaniach. Należy pamiętać o limicie, aby uniknąć niepotrzebnego czasu rozwiązywania problemów. Aby uzyskać więcej informacji, zobacz [Zarządzanie cenami i ilością danych w Application Insights](../azure-monitor/app/pricing.md).

> [!IMPORTANT]
> Application Insights zawiera funkcję [próbkowania](../azure-monitor/app/sampling.md) , która umożliwia ochronę przed generowaniem zbyt dużej ilości danych telemetrycznych w przypadku zakończonych wykonań w czasie szczytowego ładowania. Próbkowanie jest domyślnie włączone. Jeśli wydaje się, że brakuje danych, może być konieczne dostosowanie ustawień próbkowania do określonego scenariusza monitorowania. Aby dowiedzieć się więcej, zobacz [Konfigurowanie próbkowania](configure-monitoring.md#configure-sampling).

Pełna lista funkcji Application Insights dostępnych dla aplikacji funkcji została szczegółowo opisana w [Application Insights dla Azure Functions obsługiwanych funkcji](../azure-monitor/app/azure-functions-supported-features.md).

## <a name="application-insights-integration"></a>Integracja Application Insights

Zazwyczaj tworzysz wystąpienie Application Insights podczas tworzenia aplikacji funkcji. W takim przypadku klucz Instrumentacji wymagany do integracji jest już ustawiony jako ustawienie aplikacji o nazwie *APPINSIGHTS_INSTRUMENTATIONKEY*. Jeśli z jakiegoś powodu aplikacja funkcji nie ma zestawu kluczy instrumentacji, należy [włączyć integrację Application Insights](configure-monitoring.md#enable-application-insights-integration).  

## <a name="collecting-telemetry-data"></a>Zbieranie danych telemetrycznych

Po włączeniu integracji Application Insights dane telemetryczne są wysyłane do wystąpienia połączonego Application Insights. Te dane obejmują dzienniki wygenerowane przez hosta funkcji, ślady zapisane na podstawie kodu usługi i dane wydajności. 

>[!NOTE]
>Oprócz danych z funkcji i hosta usługi Functions, można również zbierać dane z [kontrolera skalowania funkcji](#scale-controller-logs).   

### <a name="log-levels-and-categories"></a>Poziomy i kategorie dziennika

Podczas pisania śladów kodu aplikacji należy przypisać poziom dziennika do śladów. Poziomy dziennika umożliwiają ograniczenie ilości danych zbieranych ze śladów.  

[!INCLUDE [functions-log-levels](../../includes/functions-log-levels.md)]

Aby dowiedzieć się więcej na temat poziomów dziennika, zobacz [Konfigurowanie poziomów dzienników](configure-monitoring.md#configure-log-levels).

Przypisując zarejestrowane elementy do kategorii, masz większą kontrolę nad danymi telemetrycznymi wygenerowanymi z określonych źródeł w aplikacji funkcji. Kategorie ułatwiają wykonywanie analiz przez zebrane dane. Ślady zapisywane na podstawie kodu funkcji są przypisywane do poszczególnych kategorii na podstawie nazwy funkcji. Aby dowiedzieć się więcej na temat kategorii, zobacz [Konfigurowanie kategorii](configure-monitoring.md#configure-categories).

### <a name="custom-telemetry-data"></a>Niestandardowe dane telemetryczne

W [językach C#](functions-dotnet-class-library.md#log-custom-telemetry-in-c-functions) i [JavaScript](functions-reference-node.md#log-custom-telemetry)możesz użyć zestawu SDK Application Insights, aby napisać niestandardowe dane telemetryczne.

### <a name="dependencies"></a>Zależności

Począwszy od wersji 2. x funkcji, środowisko uruchomieniowe automatycznie zbiera dane dotyczące zależności, które używają niektórych zestawów SDK klienta. Application Insights zbiera dane z następujących zależności:

+ Azure Cosmos DB 
+ Azure Event Hubs
+ Azure Service Bus
+ Usługi Azure Storage (obiekty blob, kolejki i tabele)

Żądania HTTP i wywołania bazy danych używające `SqlClient` również są przechwytywane. Aby uzyskać pełną listę zależności obsługiwanych przez Application Insights, zobacz [automatycznie śledzone zależności](../azure-monitor/app/asp-net-dependencies.md#automatically-tracked-dependencies).

Application Insights generuje _mapę aplikacji_ zebranych danych zależności. Poniżej znajduje się przykład mapy aplikacji funkcji wyzwalacza HTTP z powiązaniem wyjściowym magazynu kolejki.  

![Mapa aplikacji z zależnością](./media/functions-monitoring/app-map.png)

Zależności są zapisywane na `Information` poziomie. Jeśli odfiltruje `Warning` się na poziomie lub powyżej, dane zależności nie będą widoczne. Ponadto Automatyczne zbieranie zależności występuje w zakresie nienależącym do użytkownika. Aby przechwytywać dane zależności, upewnij się, że poziom jest ustawiony na co najmniej `Information` poza zakresem użytkownika ( `Function.<YOUR_FUNCTION_NAME>.User` ) na hoście.

Oprócz automatycznego zbierania danych zależności można także użyć jednego z Application Insights zestawy SDK specyficzne dla języka do zapisania niestandardowych informacji o zależnościach w dziennikach. Przykład sposobu pisania zależności niestandardowych można znaleźć w jednym z następujących przykładów dotyczących języka:

+ [Log custom telemetry in C# functions (Rejestrowanie telemetrii niestandardowej w funkcjach języka C#)](functions-dotnet-class-library.md#log-custom-telemetry-in-c-functions)
+ [Log custom telemetry in JavaScript functions (Rejestrowanie telemetrii niestandardowej w funkcjach języka JavaScript)](functions-reference-node.md#log-custom-telemetry) 

## <a name="writing-to-logs"></a>Zapisywanie w dziennikach 

Sposób zapisu w dziennikach i używanych interfejsów API zależy od języka projektu aplikacji funkcji.   
Zapoznaj się z przewodnikiem dewelopera dotyczącym Twojego języka, aby dowiedzieć się więcej na temat pisania dzienników z funkcji.

+ [C# (Biblioteka klas .NET)](functions-dotnet-class-library.md#logging)
+ [Java](functions-reference-java.md#logger)
+ [JavaScript](functions-reference-node.md#write-trace-output-to-logs) 
+ [Program PowerShell](functions-reference-powershell.md#logging)
+ [Python](functions-reference-python.md#logging)

## <a name="analyze-data"></a>Analizowanie danych

Domyślnie dane zbierane z aplikacji funkcji są przechowywane w Application Insights. W [Azure Portal](https://portal.azure.com)Application Insights zawiera obszerny zestaw wizualizacji danych telemetrycznych. Możesz przejść do szczegółów dzienników błędów i zdarzeń zapytań i metryk. Aby dowiedzieć się więcej, zapoznaj się z podstawowymi przykładami wyświetlania zebranych danych i wykonywania na nich zapytań, zobacz [analizowanie Azure Functions telemetrii w Application Insights](analyze-telemetry-data.md). 

## <a name="streaming-logs"></a>Dzienniki przesyłania strumieniowego

Podczas tworzenia aplikacji często warto zobaczyć, co jest zapisywane w dziennikach niemal w czasie rzeczywistym podczas uruchamiania na platformie Azure.

Istnieją dwa sposoby wyświetlania strumienia danych dziennika generowanych przez wykonania funkcji.

* **Wbudowane przesyłanie strumieniowe dzienników**: platforma App Service umożliwia wyświetlenie strumienia plików dziennika aplikacji. Ten strumień jest równoważny z danymi wyjściowymi wyświetlanymi podczas debugowania funkcji podczas [lokalnego tworzenia](functions-develop-local.md) i korzystania z karty **test** w portalu. Wyświetlane są wszystkie informacje oparte na dzienniku. Aby uzyskać więcej informacji, zobacz [dzienniki przesyłania strumieniowego](../app-service/troubleshoot-diagnostic-logs.md#stream-logs). Ta metoda przesyłania strumieniowego obsługuje tylko jedno wystąpienie i nie może być używana z aplikacją działającą w systemie Linux w planie zużycia.

* **Live Metrics Stream**: gdy aplikacja funkcji jest [połączona z Application Insights](configure-monitoring.md#enable-application-insights-integration), można wyświetlać dane dziennika i inne metryki niemal w czasie rzeczywistym w Azure Portal przy użyciu [Live Metrics Stream](../azure-monitor/app/live-stream.md). Użyj tej metody, gdy funkcje monitorowania działają w wielu wystąpieniach lub w systemie Linux w planie zużycia. Ta metoda używa [danych próbkowanych](configure-monitoring.md#configure-sampling).

Strumienie dzienników można wyświetlać zarówno w portalu, jak i w większości lokalnych środowisk programistycznych. Aby dowiedzieć się, jak włączyć strumienie dzienników, zobacz [Włączanie dzienników wykonywania przesyłania strumieniowego w Azure Functions](streaming-logs.md).

## <a name="diagnostic-logs"></a>Dzienniki diagnostyczne

_Ta funkcja jest w wersji zapoznawczej._ 

Application Insights umożliwia eksportowanie danych telemetrycznych do magazynu długoterminowego lub innych usług Analysis Services.  

Ponieważ funkcje programu integrują się również z Azure Monitor, można także użyć ustawień diagnostycznych do wysyłania danych telemetrycznych do różnych miejsc docelowych, w tym Azure Monitor dzienników. Aby dowiedzieć się więcej, zobacz [Azure Functions monitorowania z dziennikami Azure monitor](functions-monitor-log-analytics.md).

## <a name="scale-controller-logs"></a>Dzienniki kontrolera skalowania

_Ta funkcja jest w wersji zapoznawczej._ 

[Kontroler Azure Functions skalowania](./event-driven-scaling.md#runtime-scaling) monitoruje wystąpienia Azure Functions hosta, na którym działa aplikacja. Ten kontroler podejmuje decyzje dotyczące sytuacji, w których należy dodawać lub usuwać wystąpienia na podstawie bieżącej wydajności. Kontroler skalowania może wysyłać dzienniki, aby Application Insights, aby lepiej zrozumieć decyzje podejmowane przez kontroler skalowania dla aplikacji funkcji. Możesz również przechowywać wygenerowane dzienniki w usłudze BLOB Storage na potrzeby analizy przez inną usługę. 

Aby włączyć tę funkcję, należy dodać ustawienie aplikacji o nazwie `SCALE_CONTROLLER_LOGGING_ENABLED` do ustawień aplikacji funkcji. Aby dowiedzieć się, jak to zrobić, zobacz [Konfigurowanie dzienników kontrolera skalowania](configure-monitoring.md#configure-scale-controller-logs).

## <a name="report-issues"></a>Zgłaszanie problemów

Aby zgłosić problem związany z integracją Application Insights w usłudze Functions lub uzyskać sugestię lub żądanie, [Utwórz problem w usłudze GitHub](https://github.com/Azure/Azure-Functions/issues/new).

## <a name="next-steps"></a>Następne kroki

Więcej informacji można znaleźć w następujących zasobach:

* [Application Insights](/azure/application-insights/)
* [Rejestrowanie ASP.NET Core](/aspnet/core/fundamentals/logging/)
