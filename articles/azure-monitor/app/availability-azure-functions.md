---
title: Tworzenie i uruchamianie niestandardowych testów dostępności przy użyciu Azure Functions
description: Ten dokument zawiera informacje dotyczące sposobu tworzenia funkcji platformy Azure z TrackAvailability (), która będzie uruchamiana okresowo zgodnie z konfiguracją podaną w funkcji TimerTrigger. Wyniki tego testu zostaną wysłane do zasobu Application Insights, w którym będzie można wykonywać zapytania o dane dotyczące wyników dostępności i je otrzymywać. Dostosowane testy umożliwiają pisanie bardziej złożonych testów dostępności niż jest to możliwe za pomocą interfejsu użytkownika portalu, monitorowania aplikacji wewnątrz sieci wirtualnej platformy Azure, zmiany adresu punktu końcowego lub tworzenia testu dostępności, jeśli nie jest on dostępny w Twoim regionie.
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 05/04/2020
ms.openlocfilehash: 7c7aba1c1f091efa0ddd4417ef0a03f3f4ca4d36
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91361011"
---
# <a name="create-and-run-custom-availability-tests-using-azure-functions"></a>Tworzenie i uruchamianie niestandardowych testów dostępności przy użyciu Azure Functions

W tym artykule opisano sposób tworzenia funkcji platformy Azure z TrackAvailability (), która będzie uruchamiana okresowo zgodnie z konfiguracją podaną w funkcji TimerTrigger z własną logiką biznesową. Wyniki tego testu zostaną wysłane do zasobu Application Insights, w którym będzie można wykonywać zapytania o dane dotyczące wyników dostępności i je otrzymywać. Dzięki temu można tworzyć niestandardowe testy podobne do tego, co można zrobić za pośrednictwem [monitorowania dostępności](./monitor-web-app-availability.md) w portalu. Dostosowane testy umożliwiają pisanie bardziej złożonych testów dostępności niż jest to możliwe za pomocą interfejsu użytkownika portalu, monitorowania aplikacji wewnątrz sieci wirtualnej platformy Azure, zmiany adresu punktu końcowego lub tworzenia testu dostępności, nawet jeśli ta funkcja nie jest dostępna w Twoim regionie.

> [!NOTE]
> Ten przykład został zaprojektowany wyłącznie w celu pokazywania Mechanics sposobu, w jaki wywołanie interfejsu API TrackAvailability () działa w ramach funkcji platformy Azure. Nie sposób pisania bazowego kodu testu HTTP/logiki biznesowej, które byłyby wymagane do przeprowadzenia tego w pełni funkcjonalnego testu dostępności. Domyślnie jeśli przejdziesz przez ten przykład, utworzysz Test dostępności, który będzie zawsze generował błąd.

## <a name="create-timer-triggered-function"></a>Utwórz funkcję wyzwalaną przez czasomierz

- Jeśli masz zasób Application Insights:
    - Domyślnie Azure Functions tworzy zasób Application Insights, ale jeśli chcesz użyć jednego z już utworzonych zasobów, musisz określić, że podczas tworzenia.
    - Postępuj zgodnie z instrukcjami dotyczącymi sposobu [tworzenia zainicjowanych zasobów Azure Functions i czasomierza](../../azure-functions/functions-create-scheduled-function.md) (Zatrzymaj przed oczyszczeniem) przy użyciu następujących opcji.
        -  Wybierz kartę **monitorowanie** w górnej części strony.

            ![ Tworzenie aplikacji Azure Functions przy użyciu własnego zasobu usługi App Insights](media/availability-azure-functions/create-function-app.png)

        - Zaznacz pole listy rozwijanej Application Insights i wpisz lub wybierz nazwę zasobu.

            ![Wybieranie istniejącego zasobu Application Insights](media/availability-azure-functions/app-insights-resource.png)

        - Wybierz pozycję **Przejrzyj i utwórz**
- Jeśli nie masz jeszcze utworzonego zasobu Application Insights dla funkcji wyzwalanej przez czasomierz:
    - Domyślnie podczas tworzenia aplikacji Azure Functions zostanie utworzony zasób Application Insights.
    - Postępuj zgodnie z instrukcjami dotyczącymi sposobu [tworzenia zasobu Azure Functions i wyzwalanej funkcji timer](../../azure-functions/functions-create-scheduled-function.md) (Zatrzymaj przed oczyszczeniem).

## <a name="sample-code"></a>Przykładowy kod

Skopiuj poniższy kod do pliku Run. CSX (spowoduje to zastąpienie istniejącego kodu). W tym celu przejdź do aplikacji Azure Functions i wybierz funkcję wyzwalacza czasomierz po lewej stronie.

>[!div class="mx-imgBorder"]
>![Funkcja Run. CSX w Azure Portal](media/availability-azure-functions/runcsx.png)

> [!NOTE]
> Adres punktu końcowego, który ma być używany: `EndpointAddress= https://dc.services.visualstudio.com/v2/track` . Jeśli zasób nie znajduje się w regionie, takim jak Azure Government lub Chiny platformy Azure, w tym przypadku zapoznaj się z tym artykułem, aby zastąpić [domyślne punkty końcowe](./custom-endpoints.md#regions-that-require-endpoint-modification) i wybrać odpowiedni punkt końcowy kanału telemetrii dla danego regionu.

```C#
#load "runAvailabilityTest.csx"
 
using System;
using System.Diagnostics;
using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;
 
// The Application Insights Instrumentation Key can be changed by going to the overview page of your Function App, selecting configuration, and changing the value of the APPINSIGHTS_INSTRUMENTATIONKEY Application setting.
// DO NOT replace the code below with your instrumentation key, the key's value is pulled from the environment variable/application setting key/value pair.
private static readonly string instrumentationKey = Environment.GetEnvironmentVariable("APPINSIGHTS_INSTRUMENTATIONKEY");
 
//[CONFIGURATION_REQUIRED]
// If your resource is in a region like Azure Government or Azure China, change the endpoint address accordingly.
// Visit https://docs.microsoft.com/azure/azure-monitor/app/custom-endpoints#regions-that-require-endpoint-modification for more details.
private const string EndpointAddress = "https://dc.services.visualstudio.com/v2/track";
 
private static readonly TelemetryConfiguration telemetryConfiguration = new TelemetryConfiguration(instrumentationKey, new InMemoryChannel { EndpointAddress = EndpointAddress });
private static readonly TelemetryClient telemetryClient = new TelemetryClient(telemetryConfiguration);
 
public async static Task Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Entering Run at: {DateTime.Now}");
 
    if (myTimer.IsPastDue)
    {
        log.LogWarning($"[Warning]: Timer is running late! Last ran at: {myTimer.ScheduleStatus.Last}");
    }
 
    // [CONFIGURATION_REQUIRED] provide {testName} accordingly for your test function
    string testName = "AvailabilityTestFunction";
 
    // REGION_NAME is a default environment variable that comes with App Service
    string location = Environment.GetEnvironmentVariable("REGION_NAME");
 
    log.LogInformation($"Executing availability test run for {testName} at: {DateTime.Now}");
    string operationId = Guid.NewGuid().ToString("N");
 
    var availability = new AvailabilityTelemetry
    {
        Id = operationId,
        Name = testName,
        RunLocation = location,
        Success = false
    };
 
    var stopwatch = new Stopwatch();
    stopwatch.Start();
 
    try
    {
        await RunAvailbiltyTestAsync(log);
        availability.Success = true;
    }
    catch (Exception ex)
    {
        availability.Message = ex.Message;
 
        var exceptionTelemetry = new ExceptionTelemetry(ex);
        exceptionTelemetry.Context.Operation.Id = operationId;
        exceptionTelemetry.Properties.Add("TestName", testName);
        exceptionTelemetry.Properties.Add("TestLocation", location);
        telemetryClient.TrackException(exceptionTelemetry);
    }
    finally
    {
        stopwatch.Stop();
        availability.Duration = stopwatch.Elapsed;
        availability.Timestamp = DateTimeOffset.UtcNow;
 
        telemetryClient.TrackAvailability(availability);
        // call flush to ensure telemetry is sent
        telemetryClient.Flush();
    }
}

```

Po prawej stronie w obszarze Wyświetl pliki wybierz pozycję **Dodaj**. Wywołaj nowy plik **Funkcja. proj** z następującą konfiguracją.

```C#
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>
    <ItemGroup>
        <PackageReference Include="Microsoft.ApplicationInsights.AspNetCore" Version="2.8.2" /> <!-- Ensure you’re using the latest version -->
    </ItemGroup>
</Project>

```

>[!div class="mx-imgBorder"]
>![Po prawej stronie wybierz pozycję Dodaj. Nazwij plik Function. proj](media/availability-azure-functions/addfile.png)

Po prawej stronie w obszarze Wyświetl pliki wybierz pozycję **Dodaj**. Wywołaj nowy plik **runAvailabilityTest. CSX** z następującą konfiguracją.

```C#
public async static Task RunAvailbiltyTestAsync(ILogger log)
{
    // Add your business logic here.
    throw new NotImplementedException();
}

```

## <a name="check-availability"></a>Sprawdź dostępność

Aby upewnić się, że wszystko działa, możesz przyjrzeć się grafowi na karcie dostępność zasobu Application Insights.

> [!NOTE]
> Jeśli zaimplementowano własną logikę biznesową w programie runAvailabilityTest. CSX, zobaczysz pomyślne wyniki podobne do poniższych zrzutów ekranu, jeśli nie zobaczysz, że wyniki nie zostaną wyświetlone. Testy utworzone za pomocą `TrackAvailability()` będą wyświetlane z **niestandardowym** obok nazwy testu.

>[!div class="mx-imgBorder"]
>![Karta dostępność z pomyślnymi wynikami](media/availability-azure-functions/availability-custom.png)

Aby wyświetlić szczegółowe informacje o transakcjach, wybierz pozycję **powodzenie** lub **Niepowodzenie** w obszarze drążenie do, a następnie wybierz przykład. Możesz również uzyskać szczegółowe informacje o transakcjach, wybierając punkt danych na wykresie.

>[!div class="mx-imgBorder"]
>![Wybierz przykładowy Test dostępności](media/availability-azure-functions/sample.png)

>[!div class="mx-imgBorder"]
>![Szczegóły końca transakcji](media/availability-azure-functions/end-to-end.png)

Jeśli uruchomiono wszystko (bez dodawania logiki biznesowej), zobaczysz, że test zakończył się niepowodzeniem.

## <a name="query-in-logs-analytics"></a>Zapytanie w dziennikach (analiza)

Dzienników (analiz) można używać do wyświetlania wyników dostępności, zależności i innych. Aby dowiedzieć się więcej o dziennikach, odwiedź stronę [Omówienie zapytania dziennika](../log-query/log-query-overview.md).

>[!div class="mx-imgBorder"]
>![Wyniki dostępności](media/availability-azure-functions/availabilityresults.png)

>[!div class="mx-imgBorder"]
>![Zrzut ekranu przedstawia nową kartę zapytania z zależnościami ograniczonymi do 50.](media/availability-azure-functions/dependencies.png)

## <a name="next-steps"></a>Następne kroki

- [Mapa aplikacji](./app-map.md)
- [Diagnostyka transakcji](./transaction-diagnostics.md)

