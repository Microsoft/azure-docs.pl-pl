---
title: Ustawianie alertów w Application Insights za pomocą programu PowerShell | Microsoft Docs
description: Automatyzacja konfiguracji Application Insights, aby otrzymywać wiadomości e-mail o zmianach metryk.
ms.topic: conceptual
ms.date: 07/23/2016
ms.openlocfilehash: 74d477b6660c0f7ec2ee32b34169bb85886936e5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87322469"
---
# <a name="use-powershell-to-set-alerts-in-application-insights"></a>Ustawianie alertów w usłudze Application Insights przy użyciu programu PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Konfigurację [alertów](../platform/alerts-log.md) można zautomatyzować w [Application Insights](./app-insights-overview.md).

Ponadto można [ustawić elementy webhook w celu zautomatyzowania odpowiedzi na alert](../platform/alerts-webhooks.md).

> [!NOTE]
> Jeśli chcesz utworzyć zasoby i alerty w tym samym czasie, rozważ [użycie szablonu Azure Resource Manager](powershell.md).

## <a name="one-time-setup"></a>Konfiguracja jednorazowa
Jeśli nie korzystasz z programu PowerShell z subskrypcją platformy Azure przed:

Zainstaluj moduł Azure PowerShell na komputerze, na którym chcesz uruchomić skrypty.

* Zainstaluj [Instalator platformy Microsoft Web (w wersji 5 lub nowszej)](https://www.microsoft.com/web/downloads/platform.aspx).
* Użyj jej do zainstalowania programu Microsoft Azure PowerShell

## <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure
Rozpocznij Azure PowerShell i [Nawiąż połączenie z subskrypcją](/powershell/azure/):

```azurepowershell
Add-AzAccount
```


## <a name="get-alerts"></a>Pobierz alerty

```azurepowershell
Get-AzAlertRule -ResourceGroup "Fabrikam" `
  [-Name "My rule"] `
  [-DetailedOutput]
```

## <a name="add-alert"></a>Dodawanie alertu

```azurepowershell
Add-AzMetricAlertRule -Name "{ALERT NAME}" `
  -Description "{TEXT}" `
  -ResourceGroup "{GROUP NAME}" `
  -TargetResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
  -MetricName "{METRIC NAME}" `
  -Operator GreaterThan `
  -Threshold {NUMBER}  `
  -WindowSize {HH:MM:SS} `
  [-SendEmailToServiceOwners] `
  [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM"] `
  -Location "East US" // must be East US at present `
  -RuleType Metric
```


## <a name="example-1"></a>Przykład 1
Wyślij do mnie wiadomość e-mail, jeśli odpowiedź serwera na żądania HTTP, średnia na 5 minut, jest mniejsza niż 1 sekunda. Zasób my Application Insights ma nazwę IceCreamWebApp i znajduje się w grupie zasobów fabrikam. Jestem właścicielem subskrypcji platformy Azure.

GUID jest IDENTYFIKATORem subskrypcji (nie kluczem Instrumentacji aplikacji).

```azurepowershell
Add-AzMetricAlertRule -Name "slow responses" `
  -ResourceGroup "Fabrikam" `
  -TargetResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
  -MetricName "request.duration" `
  -Operator GreaterThan `
  -Threshold 1 `
  -WindowSize 00:05:00 `
  -SendEmailToServiceOwners `
  -Location "East US" `
  -RuleType Metric
```

## <a name="example-2"></a>Przykład 2
Mam aplikację, w której używam [TrackMetric ()](./api-custom-events-metrics.md#trackmetric) do raportowania metryki o nazwie "salesPerHour". Wyślij wiadomość e-mail do moich współpracowników, jeśli wartość "salesPerHour" spadnie poniżej 100, średnia na 24 godziny.

```azurepowershell
Add-AzMetricAlertRule -Name "poor sales" `
  -Description "slow sales alert" `
  -ResourceGroup "Fabrikam" `
  -TargetResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
  -MetricName "salesPerHour" `
  -Operator LessThan `
  -Threshold 100 `
  -WindowSize 24:00:00 `
  -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
  -Location "East US" `
  -RuleType Metric
```

Ta sama reguła może być używana dla metryki raportowanej za pomocą [parametru pomiaru](./api-custom-events-metrics.md#properties) innego wywołania śledzenia, takiego jak poleceń trackEvent lub trackPageView.

## <a name="metric-names"></a>Nazwy metryk
| Nazwa metryki | Nazwa ekranu | Opis |
| --- | --- | --- |
| `basicExceptionBrowser.count` |Wyjątki przeglądarki |Liczba nieprzechwyconych wyjątków zgłoszonych w przeglądarce. |
| `basicExceptionServer.count` |Wyjątki serwera |Liczba nieobsłużonych wyjątków zgłoszonych przez aplikację |
| `clientPerformance.clientProcess.value` |Czas przetwarzania klienta |Czas między odebraniem ostatniego bajtu dokumentu do momentu załadowania modelu DOM. Żądania asynchroniczne nadal mogą być przetwarzane. |
| `clientPerformance.networkConnection.value` |Czas połączenia sieciowego ładowania strony |Czas, przez jaki przeglądarka będzie łączyć się z siecią. Może mieć wartość 0, jeśli jest buforowana. |
| `clientPerformance.receiveRequest.value` |Czas odpowiedzi na odebranie |Czas między wysłaniem żądania przez przeglądarkę do rozpoczęcia odbierania odpowiedzi. |
| `clientPerformance.sendRequest.value` |Czas żądania wysłania |Czas potrzebny na wysłanie żądania przez przeglądarkę. |
| `clientPerformance.total.value` |Czas ładowania strony w przeglądarce |Czas od żądania użytkownika do załadowania modelu DOM, arkuszy stylów, skryptów i obrazów. |
| `performanceCounter.available_bytes.value` |Dostępna pamięć |Pamięć fizyczna natychmiast dostępna dla procesu lub do użycia przez system. |
| `performanceCounter.io_data_bytes_per_sec.value` |Szybkość operacji we/wy procesu |Łączna liczba bajtów odczytanych i zapisywana w plikach, sieci i urządzeniach. |
| `performanceCounter.number_of_exceps_thrown_per_sec.value` |częstotliwość wyjątków |Zgłoszone wyjątki na sekundę. |
| `performanceCounter.percentage_processor_time.value` |Procesor CPU procesu |Wyrażony w procentach czas, przez jaki wszystkie wątki procesów używane przez procesor do wykonywania instrukcji dla procesu aplikacji. |
| `performanceCounter.percentage_processor_total.value` |Czas procesora |Procent czasu spędzanego przez procesor w wątkach, które nie są bezczynne. |
| `performanceCounter.process_private_bytes.value` |Prywatne bajty procesu |Pamięć przypisana wyłącznie do procesów monitorowanej aplikacji. |
| `performanceCounter.request_execution_time.value` |Czas wykonywania żądania ASP.NET |Czas wykonywania najnowszego żądania. |
| `performanceCounter.requests_in_application_queue.value` |Żądania ASP.NET w kolejce wykonywania |Długość kolejki żądań aplikacji. |
| `performanceCounter.requests_per_sec.value` |Częstotliwość żądania ASP.NET |Szybkość wszystkich żądań wysyłanych do aplikacji w ciągu sekundy od ASP.NET. |
| `remoteDependencyFailed.durationMetric.count` |Awarie zależności |Liczba wywołań zakończonych niepowodzeniem wykonanych przez aplikację serwera w zasobach zewnętrznych. |
| `request.duration` |Czas odpowiedzi serwera |Czas między odebraniem żądania HTTP i zakończeniem wysyłania odpowiedzi. |
| `request.rate` |Częstotliwość żądań |Szybkość wszystkich żądań wysyłanych do aplikacji na sekundę. |
| `requestFailed.count` |Żądania zakończone niepowodzeniem |Liczba żądań HTTP, które spowodowały powstanie kodu odpowiedzi >= 400 |
| `view.count` |Wyświetlenia stron |Liczba żądań użytkowników klienta dla strony sieci Web. Ruch syntetyczny jest odfiltrowany. |
| {Nazwa metryki niestandardowej} |{Twoja Nazwa metryki} |Wartość metryki zgłoszona przez [TrackMetric](./api-custom-events-metrics.md#trackmetric) lub [parametr pomiarów wywołania śledzenia](./api-custom-events-metrics.md#properties). |

Metryki są wysyłane przez różne moduły telemetrii:

| Grupa metryk | Moduł zbierający |
| --- | --- |
| basicExceptionBrowser,<br/>clientPerformance,<br/>widok |[Przeglądarka JavaScript](./javascript.md) |
| performanceCounter |[Wydajność](./configuration-with-applicationinsights-config.md) |
| remoteDependencyFailed |[Zależność](./configuration-with-applicationinsights-config.md) |
| żądając<br/>requestFailed |[Żądanie serwera](./configuration-with-applicationinsights-config.md) |

## <a name="webhooks"></a>Elementy webhook
Możesz [zautomatyzować swoją odpowiedź na alert](../platform/alerts-webhooks.md). Gdy zostanie zgłoszony alert, platforma Azure wywoła wybrany adres internetowy.

## <a name="see-also"></a>Zobacz też
* [Skrypt służący do konfigurowania Application Insights](./create-new-resource.md#creating-a-resource-automatically)
* [Tworzenie zasobów testowania Application Insights i sieci Web z szablonów](powershell.md)
* [Automatyzowanie Diagnostyka Microsoft Azure sprzęgu do Application Insights](powershell-azure-diagnostics.md)
* [Automatyzowanie odpowiedzi na alert](../platform/alerts-webhooks.md)

