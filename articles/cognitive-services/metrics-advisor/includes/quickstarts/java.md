---
title: Klasyfikator metryk w języku Java — Szybki Start
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 10/14/2020
ms.author: mbullwin
ms.openlocfilehash: ff8a09e32a44f51571cca93655f91080e5df9a50
ms.sourcegitcommit: 7a7b6c7ac0aa9dac678c3dfd4b5bcbc45dc030ca
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/02/2020
ms.locfileid: "93186855"
---
[Dokumentacja](https://westus2.dev.cognitive.microsoft.com/docs/services/MetricsAdvisor/)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src)  |  źródłowy biblioteki [Artefakt (Maven)](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor)  |  [Przykłady](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src/samples)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Bieżąca wersja [zestawu Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Narzędzie kompilacji Gradle](https://gradle.org/install/)lub inny Menedżer zależności.
* Gdy masz subskrypcję platformy Azure, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title=" Utwórz zasób klasyfikatora metryk "  target="_blank"> Utwórz zasób klasyfikatora metryk <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby wdrożyć wystąpienie usługi Advisor metryk.  
* Twoja baza danych SQL z danymi szeregów czasowych.
  
  
> [!TIP]
> * Przykłady doradców metryk Java można znaleźć w witrynie [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src/samples).
> * Zainicjowanie wystąpienia usługi do użycia przez zasób klasyfikatora metryk może potrwać od 10 do 30 minut. Po pomyślnym wdrożeniu kliknij pozycję **Przejdź do zasobu** . Po wdrożeniu można rozpocząć korzystanie z wystąpienia klasyfikatora metryk z portalem internetowym i interfejsem API REST. 
> * Adres URL interfejsu API REST w Azure Portal można znaleźć w sekcji **Omówienie** zasobu. Będzie to wyglądać następująco:
>    * `https://<instance-name>.cognitiveservices.azure.com/`
    
## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-gradle-project"></a>Utwórz nowy projekt Gradle

Ten przewodnik Szybki Start używa Menedżera zależności Gradle. Więcej informacji o bibliotece klienta można znaleźć w [Maven centralnym repozytorium](https://search.maven.org/artifact/com.azure/azure-ai-metricsadvisor).

W oknie konsoli (na przykład cmd, PowerShell lub bash) Utwórz nowy katalog dla aplikacji i przejdź do niego. 

```console
mkdir myapp && cd myapp
```

Uruchom `gradle init` polecenie z katalogu roboczego. To polecenie spowoduje utworzenie podstawowych plików kompilacji dla Gradle, w tym *Build. Gradle. KTS* , który jest używany w środowisku uruchomieniowym do tworzenia i konfigurowania aplikacji.

```console
gradle init --type basic
```

Po wyświetleniu monitu wybierz pozycję **Język DSL** , a następnie **Kotlin** .

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Znajdź element *Build. Gradle. KTS* i otwórz go za pomocą PREFEROWANEGO środowiska IDE lub edytora tekstu. Następnie skopiuj w tej konfiguracji kompilacji. Pamiętaj, aby uwzględnić zależności projektu.

```kotlin
dependencies {
    compile("com.azure:azure-ai-metricsadvisor:1.0.0-beta.1")
}
```

### <a name="create-a-java-file"></a>Tworzenie pliku języka Java

Utwórz folder dla przykładowej aplikacji. W katalogu roboczym Uruchom następujące polecenie:

```console
mkdir -p src/main/java
```

Przejdź do nowego folderu i Utwórz plik o nazwie *MetricsAdvisorQuickstarts. Java* . Otwórz go w preferowanym edytorze lub środowisku IDE i Dodaj następujące `import` instrukcje:

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można je znaleźć w usłudze [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/src/samples), która zawiera przykłady kodu w tym przewodniku Szybki Start.

W `MetricsAdvisorQuickstarts` klasie aplikacji Utwórz zmienne dla klucza i punktu końcowego zasobu.


> [!IMPORTANT]
> Przejdź do witryny Azure Portal. Jeśli zasób klasyfikatora metryki został utworzony w sekcji **wymagania wstępne** wdrożony pomyślnie, kliknij przycisk **Przejdź do zasobu** w obszarze **następne kroki** . Klucze subskrypcji i punkt końcowy można znaleźć na stronie **klucz zasobu i punkt końcowy** w obszarze **Zarządzanie zasobami** . <br><br>Aby pobrać klucz interfejsu API, należy przejść do [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net) . Wybierz odpowiednie pozycje: **katalog** , **subskrypcje** i **obszar roboczy** dla zasobu i wybierz pozycję **Rozpocznij pracę** . Następnie będzie można pobrać klucze interfejsu API z programu [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key) .   
>
> Pamiętaj, aby usunąć klucz z kodu, gdy skończysz, i nigdy nie Publikuj go publicznie. W przypadku produkcji należy rozważyć użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [zabezpieczeń](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) Cognitive Services.

```java
private static String SUBSCRIPTION_KEY = "<replace-with-your-metrics-advisor-subscription-key-here>";
private static String API_KEY = "<replace-with-your-metrics-advisor-api-key-here>"
private static String ENDPOINT = "<replace-with-your-metrics-advisor-endpoint-here>";
```

W `Main()` metodzie aplikacji Dodaj wywołania metod używanych w tym przewodniku Szybki Start. Zostaną one utworzone później.

```java
static void Main(string[] args){

    // You will create the below methods later in the quickstart
    exampleTask1();
}
```

## <a name="object-model"></a>Model obiektów

Poniższe klasy obsługują niektóre główne funkcje zestawu SDK usługi Java Advisor.

|Nazwa|Opis|
|---|---|
| [MetricsAdvisorClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.1/com/azure/ai/metricsadvisor/MetricsAdvisorClient.html) | **Używane dla** : <br> -Wyświetlanie zdarzeń <br> — Wyświetlanie listy głównych przyczyn zdarzeń <br> — Pobieranie oryginalnych danych szeregów czasowych i danych szeregów czasowych ulepszonych przez usługę. <br> -Wyświetlanie alertów <br> Dodaj opinię, aby dostroić model |
| [MetricsAdvisorAdministrationClient](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.1/com/azure/ai/metricsadvisor/administration/MetricsAdvisorAdministrationClient.html)| **Umożliwia:** <br> — Zarządzanie źródłami danych <br> -Konfigurowanie konfiguracji wykrywania anomalii <br> -Konfigurowanie konfiguracji alertów o anomalii <br> — Zarządzanie punktami zaczepienia  |
| [Datapaszowy](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.1/com/azure/ai/metricsadvisor/models/DataFeed.html)| **Jakie metryki Metric pobiera ze źródła danych. A `DataFeed` zawiera wiersze:** <br> -Timestamps <br> -Zero lub więcej wymiarów <br> -Co najmniej jedna miara  |
| [Metryka](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-ai-metricsadvisor/1.0.0-beta.1/com/azure/ai/metricsadvisor/models/Metric.html) | `Metric`Jest miarą wymierną, która służy do monitorowania i oceniania stanu określonego procesu biznesowego. Może to być kombinacja wielu wartości szeregów czasowych podzielona na wymiary. Na przykład Metryka kondycji sieci Web może zawierać wymiary liczby użytkowników i rynku en-us. |

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu przedstawiają sposób wykonywania następujących zadań przy użyciu biblioteki klienta klasyfikatora metryk dla języka Java:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Dodaj strumieniowe źródło danych](#add-a-data-feed)
* [Sprawdź stan pozyskiwania](#check-the-ingestion-status)
* [Konfigurowanie wykrywania anomalii](#configure-anomaly-detection)
* [Tworzenie punktu zaczepienia](#create-a-hook)
* [Tworzenie konfiguracji alertu](#create-an-alert-configuration)
* [Zapytanie dotyczące alertu](#query-the-alert)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

### <a name="create-a-metrics-advisor-client-using-metricsadvisorkeycredential"></a>Tworzenie klienta klasyfikatora metryk przy użyciu MetricsAdvisorKeyCredential

```java
MetricsAdvisorKeyCredential credential = new MetricsAdvisorKeyCredential(SUBSCRIPTION_KEY, API_KEY);
MetricsAdvisorClient metricsAdvisorClient = new MetricsAdvisorClientBuilder()
    .endpoint(ENDPOINT)
    .credential(credential)
    .buildClient();
```

### <a name="create-a-metrics-administration-client-using-metricsadvisorkeycredential"></a>Tworzenie klienta administracyjnego metryk przy użyciu MetricsAdvisorKeyCredential

```java
MetricsAdvisorKeyCredential credential = new MetricsAdvisorKeyCredential(SUBSCRIPTION_KEY, API_KEY);
MetricsAdvisorAdministrationClient metricsAdvisorAdministrationClient =
    new MetricsAdvisorAdministrationClientBuilder()
        .endpoint(ENDPOINT)
        .credential(credential)
        .buildClient();

``` 

## <a name="add-a-data-feed"></a>Dodaj strumieniowe źródło danych

Zastąp `sql_server_connection_string` wartość własnymi parametrami połączenia z programem SQL Server i Zastąp `query` zapytanie, które zwraca dane przy użyciu pojedynczej sygnatury czasowej. Należy również dostosować `metric` wartości i w `dimension` oparciu o dane niestandardowe.

> [!IMPORTANT]
> Zapytanie powinno zwrócić co najwyżej jeden rekord dla każdej kombinacji wymiarów w każdym znaczniku czasu. Wszystkie rekordy zwrócone przez zapytanie muszą mieć te same sygnatury czasowe. Doradca metryk uruchomi to zapytanie dla każdej sygnatury czasowej w celu pozyskania danych. Zapoznaj się z [sekcją często zadawanych pytań na temat zapytań](../../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data) , aby uzyskać więcej informacji i przykłady. 

```java
final DataFeed createdSqlDataFeed = metricsAdvisorAdministrationClient.createDataFeed(
    "My data feed name",
    new SQLServerDataFeedSource("sql_server_connection_string", "query"),
    new DataFeedGranularity().setGranularityType(DataFeedGranularityType.DAILY),
    new DataFeedSchema(Arrays.asList(
        new Metric().setName("cost"),
        new Metric().setName("revenue")))
        .setDimensions(Arrays.asList(
            new Dimension().setName("category"),
            new Dimension().setName("city"))),
    new DataFeedIngestionSettings(OffsetDateTime.parse("2020-01-01T00:00:00Z")),
    new DataFeedOptions()
        .setDescription("My data feed description")
        .setRollupSettings(
            new DataFeedRollupSettings()
                .setAutoRollup(DataFeedAutoRollUpMethod.SUM, Arrays.asList("cost"), "__CUSTOM_SUM__"))
        .setMissingDataPointFillSettings(
            new DataFeedMissingDataPointFillSettings()
                .setFillType(DataSourceMissingDataPointFillType.SMART_FILLING))
        .setAccessMode(DataFeedAccessMode.PUBLIC));

System.out.printf("Data feed Id : %s%n", createdSqlDataFeed.getId());
System.out.printf("Data feed name : %s%n", createdSqlDataFeed.getName());
System.out.printf("Is the query user is one of data feed administrator : %s%n", createdSqlDataFeed.isAdmin());
System.out.printf("Data feed created time : %s%n", createdSqlDataFeed.getCreatedTime());
System.out.printf("Data feed granularity type : %s%n",
    createdSqlDataFeed.getGranularity().getGranularityType());
System.out.printf("Data feed granularity value : %d%n",
    createdSqlDataFeed.getGranularity().getCustomGranularityValue());
System.out.println("Data feed related metric Ids:");
createdSqlDataFeed.getMetricIds().forEach(metricId -> System.out.println(metricId));
System.out.printf("Data feed source type: %s%n", createdSqlDataFeed.getSourceType());
createdSqlDataFeed.getSchema().getMetrics().forEach(metric -> {
    System.out.printf("metric name: %s metric id:%s", metric.getName(), metric.getId());
});

System.out.printf("Data feed sql server query: %s%n",
    ((SQLServerDataFeedSource) createdSqlDataFeed.getSource()).getQuery());
```

## <a name="check-the-ingestion-status"></a>Sprawdź stan pozyskiwania

Ten przykład sprawdza stan pozyskiwania poprzednio podanego źródła danych.

```java
String dataFeedId = "<use-the-data-feed-id-from-createdSqlDataFeed.getId()"; 

metricsAdvisorAdministrationClient.listDataFeedIngestionStatus(
    dataFeedId,
    new ListDataFeedIngestionOptions(
        OffsetDateTime.parse("2020-01-01T00:00:00Z"),
        OffsetDateTime.parse("2020-09-09T00:00:00Z"))
).forEach(dataFeedIngestionStatus -> {
    System.out.printf("Message : %s%n", dataFeedIngestionStatus.getMessage());
    System.out.printf("Timestamp value : %s%n", dataFeedIngestionStatus.getTimestamp());
    System.out.printf("Status : %s%n", dataFeedIngestionStatus.getStatus());
});
```
## <a name="configure-anomaly-detection"></a>Konfigurowanie wykrywania anomalii 

W tym przykładzie pokazano, jak użytkownik może skonfigurować konfigurację wykrywania anomalii dla swoich danych.

```java
String metricId = "<metric-id-from-adding-data-feed>";

ChangeThresholdCondition changeThresholdCondition = new ChangeThresholdCondition()
    .setAnomalyDetectorDirection(AnomalyDetectorDirection.BOTH)
    .setChangePercentage(20)
    .setShiftPoint(10)
    .setWithinRange(true)
    .setSuppressCondition(new SuppressCondition().setMinNumber(1).setMinRatio(2));

HardThresholdCondition hardThresholdCondition = new HardThresholdCondition()
    .setAnomalyDetectorDirection(AnomalyDetectorDirection.DOWN)
    .setLowerBound(5.0)
    .setSuppressCondition(new SuppressCondition().setMinNumber(1).setMinRatio(1));

SmartDetectionCondition smartDetectionCondition = new SmartDetectionCondition()
    .setAnomalyDetectorDirection(AnomalyDetectorDirection.UP)
    .setSensitivity(10.0)
    .setSuppressCondition(new SuppressCondition().setMinNumber(1).setMinRatio(2));

final AnomalyDetectionConfiguration anomalyDetectionConfiguration =
    metricsAdvisorAdministrationClient.createMetricAnomalyDetectionConfiguration(
        metricId,
        new AnomalyDetectionConfiguration("My Anomaly detection configuration")
            .setDescription("anomaly detection config description")
            .setWholeSeriesDetectionCondition(
                new MetricWholeSeriesDetectionCondition()
                    .setChangeThresholdCondition(changeThresholdCondition)
                    .setHardThresholdCondition(hardThresholdCondition)
                    .setSmartDetectionCondition(smartDetectionCondition)
                    .setCrossConditionOperator(DetectionConditionsOperator.OR))
    );
```

## <a name="create-a-hook"></a>Tworzenie punktu zaczepienia

Ten przykład powoduje utworzenie punktu zaczepienia wiadomości e-mail, który odbiera alerty zdarzeń anomalii.

```java
Hook emailHook = new EmailHook("email hook")
    .setDescription("my email hook")
    .addEmailToAlert("alertme@alertme.com")
    .setExternalLink("https://adwiki.azurewebsites.net/articles/howto/alerts/create-hooks.html");

final Hook hook = metricsAdvisorAdministrationClient.createHook(emailHook);
EmailHook createdEmailHook = (EmailHook) hook;
System.out.printf("Hook Id: %s%n", createdEmailHook.getId());
System.out.printf("Hook Name: %s%n", createdEmailHook.getName());
System.out.printf("Hook Description: %s%n", createdEmailHook.getDescription());
System.out.printf("Hook External Link: %s%n", createdEmailHook.getExternalLink());
System.out.printf("Hook Emails: %s%n", String.join(",", createdEmailHook.getEmailsToAlert()));
```

##  <a name="create-an-alert-configuration"></a>Tworzenie konfiguracji alertu

W tym przykładzie pokazano, jak użytkownik może skonfigurować konfigurację alertów dla wykrytych anomalii w danych.

```java
String detectionConfigurationId1 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
String detectionConfigurationId2 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
String hookId1 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
String hookId2 = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

final AnomalyAlertConfiguration anomalyAlertConfiguration
    = metricsAdvisorAdministrationClient.createAnomalyAlertConfiguration(
        new AnomalyAlertConfiguration("My Alert config name")
            .setDescription("alert config description")
            .setMetricAlertConfigurations(
                Arrays.asList(
                    new MetricAnomalyAlertConfiguration(detectionConfigurationId1,
                        MetricAnomalyAlertScope.forWholeSeries()),
                    new MetricAnomalyAlertConfiguration(detectionConfigurationId2,
                        MetricAnomalyAlertScope.forWholeSeries())
                        .setAlertConditions(new MetricAnomalyAlertConditions()
                            .setSeverityCondition(new SeverityCondition()
                                .setMaxAlertSeverity(Severity.HIGH)))
                ))
            .setCrossMetricsOperator(MetricAnomalyAlertConfigurationsOperator.AND)
            .setIdOfHooksToAlert(Arrays.asList(hookId1, hookId2)));
```

## <a name="query-the-alert"></a>Zapytanie dotyczące alertu

W tym przykładzie pokazano, jak użytkownik może wysyłać zapytania dotyczące alertów wyzwalanych przez konfigurację wykrywania alertów i uzyskać anomalie dla tego alertu.

```java
String alertConfigurationId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";
metricsAdvisorClient.listAlerts(
    alertConfigurationId,
    new ListAlertOptions(OffsetDateTime.parse("2020-01-01T00:00:00Z"),
        OffsetDateTime.now(),
        TimeMode.ANOMALY_TIME))
    .forEach(alert -> {
        System.out.printf("Alert Id: %s%n", alert.getId());
        System.out.printf("Alert created on: %s%n", alert.getCreatedTime());

        // List anomalies for returned alerts
        metricsAdvisorClient.listAnomaliesForAlert(
            alertConfigurationId,
            alert.getId())
            .forEach(anomaly -> {
                System.out.printf("Anomaly was created on: %s%n", anomaly.getCreatedTime());
                System.out.printf("Anomaly severity: %s%n", anomaly.getSeverity().toString());
                System.out.printf("Anomaly status: %s%n", anomaly.getStatus());
                System.out.printf("Anomaly related series key: %s%n", anomaly.getSeriesKey().asMap());
            });
    });
```

Aplikację można skompilować przy użyciu:

```console
gradle build
```
### <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację z `run` celem:

```console
gradle run
```