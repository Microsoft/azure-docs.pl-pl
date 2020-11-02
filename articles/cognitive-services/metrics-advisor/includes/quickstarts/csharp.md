---
title: Klasyfikator metryk — Szybki Start C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 10/14/2020
ms.author: mbullwin
ms.openlocfilehash: 8e9b2c69344a19c027f72983a02834aee2e14ccf
ms.sourcegitcommit: 7a7b6c7ac0aa9dac678c3dfd4b5bcbc45dc030ca
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/02/2020
ms.locfileid: "93186852"
---
[Dokumentacja](https://aka.ms/azsdk/net/docs/ref/metricsadvisor)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src)  |  źródłowy biblioteki [Pakiet (NuGet)](https://www.nuget.org/packages/Azure.AI.MetricsAdvisor)  |  [Przykłady](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/samples/README.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Bieżąca wersja [platformy .NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* Gdy masz subskrypcję platformy Azure, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title=" Utwórz zasób klasyfikatora metryk "  target="_blank"> Utwórz zasób klasyfikatora metryk <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby wdrożyć wystąpienie usługi Advisor metryk.  
* Twoja baza danych SQL z danymi szeregów czasowych.
   
> [!TIP]
> * Przykłady doradców metryk platformy .NET można znaleźć w witrynie [GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/samples/README.md).
> * Zainicjowanie wystąpienia usługi do użycia przez zasób klasyfikatora metryk może potrwać od 10 do 30 minut. Po pomyślnym wdrożeniu kliknij pozycję **Przejdź do zasobu** . Po wdrożeniu można rozpocząć korzystanie z wystąpienia klasyfikatora metryk z portalem internetowym i interfejsem API REST. 
> * Adres URL interfejsu API REST w Azure Portal można znaleźć w sekcji **Omówienie** zasobu. Będzie to wyglądać następująco:
>    * `https://<instance-name>.cognitiveservices.azure.com/`
   
## <a name="setting-up"></a>Konfigurowanie

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką 

Po utworzeniu nowego projektu Zainstaluj bibliotekę kliencką, klikając prawym przyciskiem myszy rozwiązanie projektu w **Eksplorator rozwiązań** i wybierając pozycję **Zarządzaj pakietami NuGet** . W Menedżerze pakietów, który otwiera Wybierz pozycję **Przeglądaj** , zaznacz pozycję **Uwzględnij wersję wstępną** i Wyszukaj `Azure.AI.MetricsAdvisor` . Wybierz wersję `1.0.0-beta.1` , a następnie **Zainstaluj** . 

W oknie konsoli (na przykład cmd, PowerShell lub bash) Użyj `dotnet new` polecenia, aby utworzyć nową aplikację konsolową o nazwie `metrics-advisor-quickstart` . To polecenie tworzy prosty projekt C# "Hello world" z pojedynczym plikiem źródłowym: *program.cs* . 

```console
dotnet new console -n metrics-advisor-quickstart
```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można skompilować przy użyciu:

```console
dotnet build
```

Dane wyjściowe kompilacji nie powinny zawierać ostrzeżeń ani błędów. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką 

Jeśli używasz środowiska IDE innego niż Visual Studio, możesz zainstalować bibliotekę klienta klasyfikatora metryk dla platformy .NET za pomocą następującego polecenia:

```console
dotnet add package Azure.AI.MetricsAdvisor --version 1.0.0-beta.1
```

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można je znaleźć w usłudze [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/Azure.AI.MetricsAdvisor_1.0.0-beta.1/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/samples), która zawiera przykłady kodu w tym przewodniku Szybki Start.

W katalogu projektu Otwórz plik *program.cs* i Dodaj następujące `using` dyrektywy:

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Azure.AI.MetricsAdvisor.Administration;
using Azure.AI.MetricsAdvisor.Models;
```

W `Main()` metodzie aplikacji Dodaj wywołania metod używanych w tym przewodniku Szybki Start. Zostaną one utworzone później.

```csharp
static void Main(string[] args){
    // You will create the below methods later in the quickstart
    exampleTask1();
}
```

## <a name="object-model"></a>Model obiektów

Poniższe klasy obsługują niektóre główne funkcje zestawu metryk SDK języka C#.

|Nazwa|Opis|
|---|---|
| [MetricsAdvisorClient](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorClient.cs) | **Używane dla** : <br> -Wyświetlanie zdarzeń <br> — Wyświetlanie listy głównych przyczyn zdarzeń <br> — Pobieranie oryginalnych danych szeregów czasowych i danych szeregów czasowych ulepszonych przez usługę. <br> -Wyświetlanie alertów <br> Dodaj opinię, aby dostroić model |
| [MetricsAdvisorAdministrationClient](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorAdministrationClient.cs)| **Umożliwia:** <br> — Zarządzanie źródłami danych <br> -Konfigurowanie konfiguracji wykrywania anomalii <br> -Konfigurowanie konfiguracji alertów o anomalii <br> — Zarządzanie punktami zaczepienia  |
| [Datapaszowy](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/Models/DataFeed/DataFeed.cs)| **Jakie metryki Metric pobiera ze źródła danych. A `DataFeed` zawiera wiersze:** <br> -Timestamps <br> -Zero lub więcej wymiarów <br> -Co najmniej jedna miara  |
| [Metryka](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/Models/DataFeedMetric.cs)| `Metric`Jest miarą wymierną, która służy do monitorowania i oceniania stanu określonego procesu biznesowego. Może to być kombinacja wielu wartości szeregów czasowych podzielona na wymiary. Na przykład Metryka kondycji sieci Web może zawierać wymiary liczby użytkowników i rynku en-us. |

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu przedstawiają sposób wykonywania następujących zadań przy użyciu biblioteki klienta klasyfikatora metryk dla platformy .NET:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Dodaj strumieniowe źródło danych](#add-a-data-feed)
* [Sprawdź stan pozyskiwania](#check-the-ingestion-status)
* [Konfigurowanie wykrywania anomalii](#configure-anomaly-detection)
* [Tworzenie punktu zaczepienia](#create-a-hook)
* [Tworzenie konfiguracji alertu](#create-an-alert-configuration)
* [Zapytanie dotyczące alertu](#query-the-alert)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

W `Program` klasie aplikacji Utwórz zmienne dla kluczy i punktów końcowych zasobu.

> [!IMPORTANT]
> Przejdź do witryny Azure Portal. Jeśli zasób klasyfikatora metryki został utworzony w sekcji **wymagania wstępne** wdrożony pomyślnie, kliknij przycisk **Przejdź do zasobu** w obszarze **następne kroki** . Klucze subskrypcji i punkt końcowy można znaleźć na stronie **klucz zasobu i punkt końcowy** w obszarze **Zarządzanie zasobami** . <br><br>Aby pobrać klucz interfejsu API, należy przejść do [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net) . Wybierz odpowiednie pozycje: **katalog** , **subskrypcje** i **obszar roboczy** dla zasobu i wybierz pozycję **Rozpocznij pracę** . Następnie będzie można pobrać klucze interfejsu API z programu [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key) .   
>
> Pamiętaj, aby usunąć klucz z kodu, gdy skończysz, i nigdy nie Publikuj go publicznie. W przypadku produkcji należy rozważyć użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [zabezpieczeń](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) Cognitive Services.

Gdy masz subskrypcję i klucze interfejsu API, Utwórz MetricsAdvisorKeyCredential. Za pomocą punktu końcowego i poświadczenia klucza można utworzyć [`MetricsAdvisorClient`](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorClient.cs) :

```csharp
string endpoint = "<endpoint>";
string subscriptionKey = "<subscriptionKey>";
string apiKey = "<apiKey>";
var credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);
var client = new MetricsAdvisorClient(new Uri(endpoint), credential);
```

Można również utworzyć [`MetricsAdvisorAdministrationClient`](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor/src/MetricsAdvisorAdministrationClient.cs) do wykonywania operacji administracyjnych:

```csharp
string endpoint = "<endpoint>";
string subscriptionKey = "<subscriptionKey>";
string apiKey = "<apiKey>";
var credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);
var adminClient = new MetricsAdvisorAdministrationClient(new Uri(endpoint), credential);
```

## <a name="add-a-data-feed"></a>Dodaj strumieniowe źródło danych

Klasyfikator metryk obsługuje wiele typów źródeł danych. W tym przykładzie przedstawiono sposób tworzenia `DataFeed` wyodrębniania danych z programu SQL Server. Zastąp `connection_String` wartość własnymi parametrami połączenia z programem SQL Server i Zastąp `query` zapytanie, które zwraca dane przy użyciu pojedynczej sygnatury czasowej. Należy również dostosować `metric` wartości i w `dimension` oparciu o dane niestandardowe.


```csharp
string sqlServerConnectionString = "<connection_String>";
string sqlServerQuery = "<query>";

var dataFeedName = "Sample data feed";
var dataFeedSource = new MySqlDataFeedSource(sqlServerConnectionString, sqlServerQuery);
var dataFeedGranularity = new DataFeedGranularity(DataFeedGranularityType.Daily);

var dataFeedMetrics = new List<DataFeedMetric>()
{
    new DataFeedMetric("cost"),
    new DataFeedMetric("revenue")
};
var dataFeedDimensions = new List<MetricDimension>()
{
    new MetricDimension("category"),
    new MetricDimension("city")
};
var dataFeedSchema = new DataFeedSchema(dataFeedMetrics)
{
    DimensionColumns = dataFeedDimensions
};

var ingestionStartTime = DateTimeOffset.Parse("2020-01-01T00:00:00Z");
var dataFeedIngestionSettings = new DataFeedIngestionSettings(ingestionStartTime);

Response<DataFeed> response = await adminClient.CreateDataFeedAsync(dataFeedName, dataFeedSource,
    dataFeedGranularity, dataFeedSchema, dataFeedIngestionSettings);

DataFeed dataFeed = response.Value;

Console.WriteLine($"Data feed ID: {dataFeed.Id}");

// Only the ID of the data feed is known at this point. You can perform another service
// call to get more information, such as status, created time, the list of administrators,
// or the metric IDs.

response = await adminClient.GetDataFeedAsync(dataFeed.Id);

dataFeed = response.Value;

Console.WriteLine($"Data feed status: {dataFeed.Status.Value}");
Console.WriteLine($"Data feed created time: {dataFeed.CreatedTime.Value}");

Console.WriteLine($"Data feed administrators:");
foreach (string admin in dataFeed.Options.Administrators)
{
    Console.WriteLine($" - {admin}");
}

Console.WriteLine($"Metric IDs:");
foreach (DataFeedMetric metric in dataFeed.Schema.MetricColumns)
{
    Console.WriteLine($" - {metric.MetricName}: {metric.MetricId}");
}
```

## <a name="check-the-ingestion-status"></a>Sprawdź stan pozyskiwania

Sprawdź stan pozyskiwania wcześniej utworzonego elementu `DataFeed`

```csharp
string dataFeedId = "<dataFeedId>";

var startTime = DateTimeOffset.Parse("2020-01-01T00:00:00Z");
var endTime = DateTimeOffset.Parse("2020-09-09T00:00:00Z");
var options = new GetDataFeedIngestionStatusesOptions(startTime, endTime);

Console.WriteLine("Ingestion statuses:");
Console.WriteLine();

int statusCount = 0;

await foreach (DataFeedIngestionStatus ingestionStatus in adminClient.GetDataFeedIngestionStatusesAsync(dataFeedId, options))
{
    Console.WriteLine($"Timestamp: {ingestionStatus.Timestamp}");
    Console.WriteLine($"Status: {ingestionStatus.Status.Value}");
    Console.WriteLine($"Service message: {ingestionStatus.Message}");
    Console.WriteLine();

    // Print at most 10 statuses.
    if (++statusCount >= 10)
    {
        break;
    }
}
```

## <a name="configure-anomaly-detection"></a>Konfigurowanie wykrywania anomalii 

Utwórz konfigurację wykrywania anomalii, aby poinformować usługę, która punkty danych powinny być uznawane za anomalie.

```csharp
string metricId = "<metricId>";
string configurationName = "Sample anomaly detection configuration";

var hardThresholdSuppressCondition = new SuppressCondition(1, 100);
var hardThresholdCondition = new HardThresholdCondition(AnomalyDetectorDirection.Down, hardThresholdSuppressCondition)
{
    LowerBound = 5.0
};

var smartDetectionSuppressCondition = new SuppressCondition(4, 50);
var smartDetectionCondition = new SmartDetectionCondition(10.0, AnomalyDetectorDirection.Up, smartDetectionSuppressCondition);

var detectionCondition = new MetricWholeSeriesDetectionCondition()
{
    HardThresholdCondition = hardThresholdCondition,
    SmartDetectionCondition = smartDetectionCondition,
    CrossConditionsOperator = DetectionConditionsOperator.Or
};

var detectionConfiguration = new AnomalyDetectionConfiguration(metricId, configurationName, detectionCondition);

Response<AnomalyDetectionConfiguration> response = await adminClient.CreateMetricAnomalyDetectionConfigurationAsync(detectionConfiguration);

detectionConfiguration = response.Value;

Console.WriteLine($"Anomaly detection configuration ID: {detectionConfiguration.Id}");
```

### <a name="create-a-hook"></a>Tworzenie punktu zaczepienia

Klasyfikator metryk obsługuje `EmailHook` klasy i `WebHook` w sposób subskrybowania powiadomień o alertach. W tym przykładzie przedstawiono sposób tworzenia `EmailHook` . Musisz przekazać hak do konfiguracji alertów anomalii, aby zacząć otrzymywać powiadomienia. Aby uzyskać więcej informacji, zobacz przykład [Create an anomalia konfiguracja alertu](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/metricsadvisor/Azure.AI.MetricsAdvisor#create-an-anomaly-alert-configuration) .

```csharp
string hookName = "Sample hook";
var emailsToAlert = new List<string>()
{
    "email1@sample.com",
    "email2@sample.com"
};

var emailHook = new EmailNotificationHook(hookName, emailsToAlert);

Response<NotificationHook> response = await adminClient.CreateHookAsync(emailHook);

NotificationHook hook = response.Value;

Console.WriteLine($"Hook ID: {hook.Id}");
```

##  <a name="create-an-alert-configuration"></a>Tworzenie konfiguracji alertu

Utwórz, `AnomalyAlertConfiguration` aby poinformować usługę, której anomalie powinny wyzwolić alerty.

```csharp
string hookId = "<hookId>";
string anomalyDetectionConfigurationId = "<anomalyDetectionConfigurationId>";

string configurationName = "Sample anomaly alert configuration";
var idsOfHooksToAlert = new List<string>() { hookId };

var scope = MetricAnomalyAlertScope.GetScopeForWholeSeries();
var metricAlertConfigurations = new List<MetricAnomalyAlertConfiguration>()
{
    new MetricAnomalyAlertConfiguration(anomalyDetectionConfigurationId, scope)
};

AnomalyAlertConfiguration alertConfiguration = new AnomalyAlertConfiguration(configurationName, idsOfHooksToAlert, metricAlertConfigurations);

Response<AnomalyAlertConfiguration> response = await adminClient.CreateAnomalyAlertConfigurationAsync(alertConfiguration);

alertConfiguration = response.Value;

Console.WriteLine($"Alert configuration ID: {alertConfiguration.Id}");
```

### <a name="query-the-alert"></a>Zapytanie dotyczące alertu

Zapoznaj się z alertami utworzonymi przez daną konfigurację alertów anomalii i wystaw anomalie, które wywołały te alerty.

```csharp
string anomalyAlertConfigurationId = "<anomalyAlertConfigurationId>";

var startTime = DateTimeOffset.Parse("2020-01-01T00:00:00Z");
var endTime = DateTimeOffset.UtcNow;
var options = new GetAlertsOptions(startTime, endTime, TimeMode.AnomalyTime);

int alertCount = 0;

await foreach (AnomalyAlert alert in client.GetAlertsAsync(anomalyAlertConfigurationId, options))
{
    Console.WriteLine($"Alert at timestamp: {alert.Timestamp}");
    Console.WriteLine($"Id: {alert.Id}");
    Console.WriteLine($"Anomalies that triggered this alert:");

    await foreach (DataAnomaly anomaly in client.GetAnomaliesForAlertAsync(anomalyAlertConfigurationId, alert.Id))
    {
        Console.WriteLine("  Anomaly:");
        Console.WriteLine($"    Status: {anomaly.Status.Value}");
        Console.WriteLine($"    Severity: {anomaly.Severity}");
        Console.WriteLine($"    Series key:");

        foreach (KeyValuePair<string, string> keyValuePair in anomaly.SeriesKey.AsDictionary())
        {
            Console.WriteLine($"      Dimension '{keyValuePair.Key}': {keyValuePair.Value}");
        }

        Console.WriteLine();
    }

    // Print at most 3 alerts.
    if (++alertCount >= 3)
    {
        break;
    }
}
```

### <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację z katalogu aplikacji za pomocą `dotnet run` polecenia.

```dotnet
dotnet run
```