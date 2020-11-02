---
title: Klasyfikator metryk — szybki start Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 10/14/2020
ms.author: mbullwin
ms.openlocfilehash: cf4404bbfe7e8f0ad664c9ca8dda07ff61be12d9
ms.sourcegitcommit: 7a7b6c7ac0aa9dac678c3dfd4b5bcbc45dc030ca
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/02/2020
ms.locfileid: "93186859"
---
[Dokumentacja](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/metricsadvisor/azure-ai-metricsadvisor/README.md)  |  źródłowy biblioteki [Pakiet (PiPy)](https://pypi.org/project/azure-ai-metricsadvisor/)  |  [Przykłady](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/metricsadvisor/azure-ai-metricsadvisor/samples/README.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* [Python 3.x](https://www.python.org/)
* Gdy masz subskrypcję platformy Azure, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title=" Utwórz zasób klasyfikatora metryk "  target="_blank"> Utwórz zasób klasyfikatora metryk <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby wdrożyć wystąpienie usługi Advisor metryk.  
* Twoja baza danych SQL z danymi szeregów czasowych.
  
> [!TIP]
> * Przykłady doradców metryk języka Python można znaleźć w witrynie [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/metricsadvisor/azure-ai-metricsadvisor/samples).
> * Zainicjowanie wystąpienia usługi do użycia przez zasób klasyfikatora metryk może potrwać od 10 do 30 minut. Po pomyślnym wdrożeniu kliknij pozycję **Przejdź do zasobu** . Po wdrożeniu można rozpocząć korzystanie z wystąpienia klasyfikatora metryk z portalem internetowym i interfejsem API REST.
> * Adres URL interfejsu API REST w Azure Portal można znaleźć w sekcji **Omówienie** zasobu. Będzie to wyglądać następująco:
>    * `https://<instance-name>.cognitiveservices.azure.com/` 
 
## <a name="setting-up"></a>Konfigurowanie

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Po zainstalowaniu języka Python można zainstalować bibliotekę kliencką z:

```console
pip install azure-ai-metricsadvisor --pre
```

### <a name="create-a-new-python-application"></a>Tworzenie nowej aplikacji w języku Python

Utwórz nowy plik w języku Python i zaimportuj następujące biblioteki.

```python
import os
import datetime
```

Utwórz zmienne dla punktu końcowego i klucza usługi Azure Resource.

> [!IMPORTANT]
> Przejdź do witryny Azure Portal. Jeśli zasób klasyfikatora metryki został utworzony w sekcji **wymagania wstępne** wdrożony pomyślnie, kliknij przycisk **Przejdź do zasobu** w obszarze **następne kroki** . Klucze subskrypcji i punkt końcowy można znaleźć na stronie **klucz zasobu i punkt końcowy** w obszarze **Zarządzanie zasobami** . <br><br>Aby pobrać klucz interfejsu API, należy przejść do [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net) . Wybierz odpowiednie pozycje: **katalog** , **subskrypcje** i **obszar roboczy** dla zasobu i wybierz pozycję **Rozpocznij pracę** . Następnie będzie można pobrać klucze interfejsu API z programu [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key) .   
>
> Pamiętaj, aby usunąć klucz z kodu, gdy skończysz, i nigdy nie Publikuj go publicznie. W przypadku produkcji należy rozważyć użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [zabezpieczeń](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security) Cognitive Services.

```python
subscription_key = "<paste-your-metrics-advisor-subscription-key-here>"
api_key = "<paste-your-metrics-advisor-api-key-here>"
service_endpoint = "<paste-your-metrics-advisor-endpoint-here>"
```

## <a name="object-model"></a>Model obiektów

Poniższe klasy obsługują niektóre główne funkcje zestawu SDK w języku Python usługi Advisor.

|Nazwa|Opis|
|---|---|
| [MetricsAdvisorClient](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorClient) | **Używane dla** : <br> -Wyświetlanie zdarzeń <br> — Wyświetlanie listy głównych przyczyn zdarzeń <br> — Pobieranie oryginalnych danych szeregów czasowych i danych szeregów czasowych ulepszonych przez usługę. <br> -Wyświetlanie alertów <br> Dodaj opinię, aby dostroić model |
| [MetricsAdvisorAdministrationClient](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html?highlight=metricsadvisoradministrationclient#azure.ai.metricsadvisor.MetricsAdvisorAdministrationClient)| **Umożliwia:** <br> — Zarządzanie źródłami danych <br> — Tworzenie, konfigurowanie, pobieranie, wyświetlanie i usuwanie konfiguracji wykrywania anomalii <br> -Twórz, Konfiguruj, pobieraj, Wyświetlaj i usuwaj konfiguracje alertów o anomalii <br> — Zarządzanie punktami zaczepienia  | |
| [Datapaszowy](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.models.html?highlight=datafeed#azure.ai.metricsadvisor.models.DataFeed)| **Jakie metryki Metric pobiera ze źródła danych. A `DataFeed` zawiera wiersze:** <br> -Timestamps <br> -Zero lub więcej wymiarów <br> -Co najmniej jedna miara  |
| [Metryka](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.models.html?highlight=metric#azure.ai.metricsadvisor.models.Metric) | `Metric`Jest miarą wymierną, która służy do monitorowania i oceniania stanu określonego procesu biznesowego. Może to być kombinacja wielu wartości szeregów czasowych podzielona na wymiary. Na przykład Metryka kondycji sieci Web może zawierać wymiary liczby użytkowników i rynku en-us. |

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta klasyfikatora metryk dla języka Python:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Dodaj strumieniowe źródło danych](#add-a-data-feed)
* [Sprawdź stan pozyskiwania](#check-the-ingestion-status)
* [Konfiguracja wykrywania konfiguracji i alertu konfiguracji](#configure-anomaly-detection)
* [Tworzenie konfiguracji alertu](#create-an-alert-configuration)
* [Wyniki wykrywania anomalii zapytania](#query-the-alert)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Klient w tym przykładzie jest `MetricsAdvisorAdministrationClient` obiektem, który używa Twojego punktu końcowego i `MetricsAdvisorKeyCredential` obiektu, który zawiera Twoje klucze. Nie musisz kopiować tego przykładu kodu. Metody tworzone później spowodują utworzenie wystąpienia klienta. Alternatywny klient jest nazywany `MetricsAdvisorClient` więcej informacji na ten temat można znaleźć w [dokumentacji referencyjnej](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-ai-metricsadvisor/latest/azure.ai.metricsadvisor.html#azure.ai.metricsadvisor.MetricsAdvisorClient).

```python
client = MetricsAdvisorAdministrationClient(service_endpoint,
                                MetricsAdvisorKeyCredential(subscription_key, api_key))
```

## <a name="add-a-data-feed"></a>Dodaj strumieniowe źródło danych

W nowej metodzie Utwórz instrukcje importu podobne do poniższego przykładu. Zastąp `sql_server_connection_string` wartość własnymi parametrami połączenia z programem SQL Server i Zastąp `query` zapytanie, które zwraca dane przy użyciu pojedynczej sygnatury czasowej. Należy również dostosować `metric` wartości i w `dimension` oparciu o dane niestandardowe.

> [!IMPORTANT]
> Zapytanie powinno zwrócić co najwyżej jeden rekord dla każdej kombinacji wymiarów w każdym znaczniku czasu. Wszystkie rekordy zwrócone przez zapytanie muszą mieć te same sygnatury czasowe. Doradca metryk uruchomi to zapytanie dla każdej sygnatury czasowej w celu pozyskania danych. Zapoznaj się z [sekcją często zadawanych pytań na temat zapytań](../../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data) , aby uzyskać więcej informacji i przykłady. 

Utwórz klienta z kluczami i punktem końcowym, a następnie użyj, `client.create_data_feed()` Aby skonfigurować nazwę, źródło, stopień szczegółowości i schemat. Można również ustawić czas pozyskiwania, ustawienia zestawień i inne.


```python
def sample_create_data_feed():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        SQLServerDataFeed,
        DataFeedSchema,
        Metric,
        Dimension,
        DataFeedOptions,
        DataFeedRollupSettings,
        DataFeedMissingDataPointFillSettings
    )
    sql_server_connection_string = "<replace-with-your-sql-server-connection-string>"
    query = "<replace-with-metrics-advisor-sql-server-query>"

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

    data_feed = client.create_data_feed(
        name="My data feed",
        source=SQLServerDataFeed(
            connection_string=sql_server_connection_string,
            query=query,
        ),
        granularity="Daily",
        schema=DataFeedSchema(
            metrics=[
                Metric(name="cost", display_name="Cost"),
                Metric(name="revenue", display_name="Revenue")
            ],
            dimensions=[
                Dimension(name="category", display_name="Category"),
                Dimension(name="city", display_name="City")
            ],
            timestamp_column="Timestamp"
        ),
        ingestion_settings=datetime.datetime(2019, 10, 1),
        options=DataFeedOptions(
            data_feed_description="cost/revenue data feed",
            rollup_settings=DataFeedRollupSettings(
                rollup_type="AutoRollup",
                rollup_method="Sum",
                rollup_identification_value="__CUSTOM_SUM__"
            ),
            missing_data_point_fill_settings=DataFeedMissingDataPointFillSettings(
                fill_type="SmartFilling"
            ),
            access_mode="Private"
        )
    )

    return data_feed
sample_create_data_feed()
```

## <a name="check-the-ingestion-status"></a>Sprawdź stan pozyskiwania

W nowej metodzie utwórz instrukcję import, jak w poniższym przykładzie. Zamień na `data_feed_id` Identyfikator utworzonego źródła danych. Utwórz klienta z kluczami i punktem końcowym, a następnie użyj, `client.get_data_feed_ingestion_progress()` Aby uzyskać postęp pozyskiwania. Wydrukuj szczegóły, takie jak ostatnie aktywne i udane sygnatury czasowe.


```python
def sample_get_data_feed_ingestion_progress():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient

    data_feed_id = "<replace-with-your-metrics-advisor-data-feed-id>"

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

    progress = client.get_data_feed_ingestion_progress(data_feed_id)

    print("Latest active timestamp: {}".format(progress.latest_active_timestamp))
    print("Latest successful timestamp: {}".format(progress.latest_success_timestamp))
sample_get_data_feed_ingestion_progress()
```

## <a name="configure-anomaly-detection"></a>Konfigurowanie wykrywania anomalii

W nowej metodzie Utwórz instrukcje importu podobne do poniższego przykładu. Zamień na `metric_id` Identyfikator metryki, którą chcesz skonfigurować. Utwórz klienta z kluczami i punktem końcowym, a `client.create_metric_anomaly_detection_configuration` następnie użyj programu, aby utworzyć nową konfigurację wykrywania. Warunki progowe określają parametry wykrywania anomalii.

```python
def sample_create_detection_config():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        ChangeThresholdCondition,
        HardThresholdCondition,
        SmartDetectionCondition,
        SuppressCondition,
        MetricDetectionCondition,
    )
    metric_id = "replace-with-your-metric-id"

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

    change_threshold_condition = ChangeThresholdCondition(
        anomaly_detector_direction="Both",
        change_percentage=20,
        shift_point=10,
        within_range=True,
        suppress_condition=SuppressCondition(
            min_number=5,
            min_ratio=2
        )
    )
    hard_threshold_condition = HardThresholdCondition(
        anomaly_detector_direction="Up",
        upper_bound=100,
        suppress_condition=SuppressCondition(
            min_number=2,
            min_ratio=2
        )
    )
    smart_detection_condition = SmartDetectionCondition(
        anomaly_detector_direction="Up",
        sensitivity=10,
        suppress_condition=SuppressCondition(
            min_number=2,
            min_ratio=2
        )
    )

    detection_config = client.create_metric_anomaly_detection_configuration(
        name="my_detection_config",
        metric_id=metric_id,
        description="anomaly detection config for metric",
        whole_series_detection_condition=MetricDetectionCondition(
            cross_conditions_operator="OR",
            change_threshold_condition=change_threshold_condition,
            hard_threshold_condition=hard_threshold_condition,
            smart_detection_condition=smart_detection_condition
        )
    )

    return detection_config
sample_create_detection_config()
```


## <a name="create-a-hook"></a>Tworzenie punktu zaczepienia

W nowej metodzie Utwórz instrukcje importu podobne do poniższego przykładu. Utwórz klienta z kluczami i punktem końcowym, a `client.create_hook()` następnie użyj do utworzenia punktu zaczepienia. Wprowadź opis, listę wiadomości e-mail do wysłania alertu, a łącze zewnętrzne do odebrania alertu.  

```python
def sample_create_hook():

    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import EmailHook

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

    hook = client.create_hook(
        name="email hook",
        hook=EmailHook(
            description="my email hook",
            emails_to_alert=["alertme@contoso.com"],
            external_link="https://adwiki.azurewebsites.net/articles/howto/alerts/create-hooks.html"
        )
    )

    return hook
sample_create_hook()
```

##  <a name="create-an-alert-configuration"></a>Tworzenie konfiguracji alertu

W nowej metodzie Utwórz instrukcje importu podobne do poniższego przykładu. Zamień na `anomaly_detection_configuration_id` Identyfikator konfiguracji wykrywania anomalii i Zastąp ciąg `hook_id` utworzony wcześniej punktem zaczepienia. Utwórz klienta z kluczami i punktem końcowym, a `client.create_anomaly_alert_configuration()` następnie użyj programu w celu utworzenia konfiguracji alertu. `metric_alert_configurations` jest listą `MetricAlertConfiguration` obiektów, które określają warunki i zakres dla każdej konfiguracji.

```python
def sample_create_alert_config():
    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorAdministrationClient
    from azure.ai.metricsadvisor.models import (
        MetricAlertConfiguration,
        MetricAnomalyAlertScope,
        TopNGroupScope,
        MetricAnomalyAlertConditions,
        SeverityCondition,
        MetricBoundaryCondition,
        MetricAnomalyAlertSnoozeCondition
    )
    anomaly_detection_configuration_id = "<replace-with-your-detection-configuration-id"
    hook_id = "<replace-with-your-hook-id>"

    client = MetricsAdvisorAdministrationClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

    alert_config = client.create_anomaly_alert_configuration(
        name="my alert config",
        description="alert config description",
        cross_metrics_operator="AND",
        metric_alert_configurations=[
            MetricAlertConfiguration(
                detection_configuration_id=anomaly_detection_configuration_id,
                alert_scope=MetricAnomalyAlertScope(
                    scope_type="WholeSeries"
                ),
                alert_conditions=MetricAnomalyAlertConditions(
                    severity_condition=SeverityCondition(
                        min_alert_severity="Low",
                        max_alert_severity="High"
                    )
                )
            ),
            MetricAlertConfiguration(
                detection_configuration_id=anomaly_detection_configuration_id,
                alert_scope=MetricAnomalyAlertScope(
                    scope_type="TopN",
                    top_n_group_in_scope=TopNGroupScope(
                        top=10,
                        period=5,
                        min_top_count=5
                    )
                ),
                alert_conditions=MetricAnomalyAlertConditions(
                    metric_boundary_condition=MetricBoundaryCondition(
                        direction="Up",
                        upper=50
                    )
                ),
                alert_snooze_condition=MetricAnomalyAlertSnoozeCondition(
                    auto_snooze=2,
                    snooze_scope="Metric",
                    only_for_successive=True
                )
            ),
        ],
        hook_ids=[hook_id]
    )

    return alert_config
```

### <a name="query-the-alert"></a>Zapytanie dotyczące alertu

W nowej metodzie utwórz instrukcję import, jak w poniższym przykładzie. Zastąp ciąg `alert_id` identyfikatorem alertu i Zastąp ciąg `alert_config_id` identyfikatorem konfiguracji alertu. Utwórz klienta z kluczami i punktem końcowym, a następnie użyj `client.list_anomalies_for_alert()` programu, aby wyświetlić listę konfiguracji alertów. 

```python
def sample_list_anomalies_for_alert(alert_config_id, alert_id):

    from azure.ai.metricsadvisor import MetricsAdvisorKeyCredential, MetricsAdvisorClient
    
    alert_id = "<replace-with-your-alert-id>"
    alert_config_id = "<replace-with-your-alert-configuration-id"
    client = MetricsAdvisorClient(service_endpoint,
                                  MetricsAdvisorKeyCredential(subscription_key, api_key))

    results = client.list_anomalies_for_alert(
            alert_configuration_id=alert_config_id,
            alert_id=alert_id,
        )
    for result in results:
        print("Create on: {}".format(result.created_on))
        print("Severity: {}".format(result.severity))
        print("Status: {}".format(result.status))
```

### <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą `python` polecenia w pliku szybkiego startu.

```console
python quickstart-file.py
```