---
title: Klasyfikator metryk — szybki start dla języka JavaScript
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: metrics-advisor
ms.topic: include
ms.date: 11/09/2020
ms.author: mbullwin
ms.openlocfilehash: 9222098f719241a6b3d6e50d4181106101c6d912
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356379"
---
[Dokumentacja](/javascript/api/overview/azure/ai-metrics-advisor-readme-pre?preserve-view=true&view=azure-node-preview)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/metricsadvisor/ai-metrics-advisor/README.md)  |  źródłowy biblioteki [Pakiet (npm)](https://www.npmjs.com/package/@azure/ai-metrics-advisor)  |  [Przykłady](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/metricsadvisor/ai-metrics-advisor/samples)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Bieżąca wersja [Node.js](https://nodejs.org/)
* Gdy masz subskrypcję platformy Azure, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title=" Utwórz zasób klasyfikatora metryk "  target="_blank"> Utwórz zasób klasyfikatora metryk <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby wdrożyć wystąpienie usługi Advisor metryk.  
* Twoja baza danych SQL z danymi szeregów czasowych.
  
> [!TIP]
> * Przykłady doradców metryk JavaScript można znaleźć w witrynie [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/metricsadvisor/ai-metrics-advisor/samples).
> * Zainicjowanie wystąpienia usługi do użycia przez zasób klasyfikatora metryk może potrwać od 10 do 30 minut. Po pomyślnym wdrożeniu kliknij pozycję **Przejdź do zasobu** . Po wdrożeniu można rozpocząć korzystanie z wystąpienia klasyfikatora metryk z portalem internetowym i interfejsem API REST. 
> * Adres URL interfejsu API REST w Azure Portal można znaleźć w sekcji **Omówienie** zasobu. Będzie to wyglądać następująco:
>    * `https://<instance-name>.cognitiveservices.azure.com/`
    
## <a name="setting-up"></a>Konfigurowanie

### <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js

W oknie konsoli (na przykład cmd, PowerShell lub bash) Utwórz nowy katalog dla aplikacji i przejdź do niego. 

```console
mkdir myapp && cd myapp
```

Uruchom `npm init` polecenie, aby utworzyć aplikację Node z `package.json` plikiem. 

```console
npm init
```

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Zainstaluj `@azure/ai-metrics-advisor` pakiet npm:

```console
npm install @azure/ai-metrics-advisor@1.0.0-beta.2
```

`package.json`Plik aplikacji zostanie zaktualizowany z zależnościami.

Utwórz plik o nazwie `index.js` i zaimportuj następujące biblioteki:

> [!TIP]
> Chcesz wyświetlić cały plik kodu szybkiego startu jednocześnie? Można je znaleźć w usłudze [GitHub](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/metricsadvisor/ai-metrics-advisor/samples/javascript), która zawiera przykłady kodu w tym przewodniku Szybki Start.

Utwórz zmienne dla punktu końcowego i klucza usługi Azure Resource. 

> [!IMPORTANT]
> Przejdź do witryny Azure Portal. Jeśli zasób klasyfikatora metryki został utworzony w sekcji **wymagania wstępne** wdrożony pomyślnie, kliknij przycisk **Przejdź do zasobu** w obszarze **następne kroki**. Klucze subskrypcji i punkt końcowy można znaleźć na stronie **klucz zasobu i punkt końcowy** w obszarze **Zarządzanie zasobami**. <br><br>Aby pobrać klucz interfejsu API, należy przejść do [https://metricsadvisor.azurewebsites.net](https://metricsadvisor.azurewebsites.net) . Wybierz odpowiednie pozycje: **katalog**, **subskrypcje** i **obszar roboczy** dla zasobu i wybierz pozycję **Rozpocznij pracę**. Następnie będzie można pobrać klucze interfejsu API z programu [https://metricsadvisor.azurewebsites.net/api-key](https://metricsadvisor.azurewebsites.net/api-key) .   
>
> Pamiętaj, aby usunąć klucz z kodu, gdy skończysz, i nigdy nie Publikuj go publicznie. W przypadku produkcji należy rozważyć użycie bezpiecznego sposobu przechowywania poświadczeń i uzyskiwania do nich dostępu. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [zabezpieczeń](../../../cognitive-services-security.md) Cognitive Services.

```javascript
subscriptionKey = "<paste-your-metrics-advisor-key-here>";
apiKey ="<paste-your-metrics-advisor-api-key-here>";
endpoint = "<paste-your-metrics-advisor-endpoint-here>";
```

## <a name="object-model"></a>Model obiektów

Poniższe klasy i interfejsy obsługują niektóre główne funkcje zestawu SDK języka JavaScript usługi Advisor.

|Nazwa|Opis|
|---|---|
| MetricsAdvisorClient | **Używane dla**: <br> -Wyświetlanie zdarzeń <br> — Wyświetlanie listy głównych przyczyn zdarzeń <br> — Pobieranie oryginalnych danych szeregów czasowych i danych szeregów czasowych ulepszonych przez usługę. <br> -Wyświetlanie alertów <br> Dodaj opinię, aby dostroić model |
| MetricsAdvisorAdministrationClient | **Umożliwia:** <br> — Zarządzanie źródłami danych <br> -Twórz, Konfiguruj, pobieraj, Wyświetlaj i usuwaj konfiguracje alertów o anomalii <br> — Zarządzanie punktami zaczepienia  |
| Datapaszowy | **Jakie metryki Metric pobiera ze źródła danych. A `DataFeed` zawiera wiersze:** <br> -Timestamps <br> -Zero lub więcej wymiarów <br> -Co najmniej jedna miara  |
| DataFeedMetric | `DataFeedMetric`Jest miarą wymierną, która służy do monitorowania i oceniania stanu określonego procesu biznesowego. Może to być kombinacja wielu wartości szeregów czasowych podzielona na wymiary. Na przykład Metryka kondycji sieci Web może zawierać wymiary liczby użytkowników i rynku en-us. |

## <a name="code-examples"></a>Przykłady kodu

Te fragmenty kodu pokazują, jak wykonać następujące czynności za pomocą biblioteki klienta klasyfikatora metryk dla języka JavaScript:

* [Uwierzytelnianie klienta](#authenticate-the-client)
* [Dodaj strumieniowe źródło danych](#add-a-data-feed)
* [Sprawdź stan pozyskiwania](#check-ingestion-status)
* [Konfigurowanie wykrywania anomalii](#configure-anomaly-detection)
* [Tworzenie punktu zaczepienia](#create-a-hook)
* [Tworzenie konfiguracji alertu](#create-an-alert-configuration)
* [Zapytanie dotyczące alertu](#query-the-alert)

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

Gdy masz dwa klucze i adres punktu końcowego, można użyć klasy MetricsAdvisorKeyCredential do uwierzytelniania klientów w następujący sposób:

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorClient,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);
const client = new MetricsAdvisorClient(endpoint, credential);
const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
```

## <a name="add-a-data-feed"></a>Dodaj strumieniowe źródło danych

Klasyfikator metryk obsługuje łączenie różnych typów źródeł danych. Oto przykład do pozyskiwania danych z SQL Server.

Zastąp `sql_server_connection_string` wartość własnymi parametrami połączenia z programem SQL Server i Zastąp `query` zapytanie, które zwraca dane przy użyciu pojedynczej sygnatury czasowej. Należy również dostosować `metric` wartości i w `dimension` oparciu o dane niestandardowe.

> [!IMPORTANT]
> Zapytanie powinno zwrócić co najwyżej jeden rekord dla każdej kombinacji wymiarów w każdym znaczniku czasu. Wszystkie rekordy zwrócone przez zapytanie muszą mieć te same sygnatury czasowe. Doradca metryk uruchomi to zapytanie dla każdej sygnatury czasowej w celu pozyskania danych. Zapoznaj się z [sekcją często zadawanych pytań na temat zapytań](../../faq.md#how-do-i-write-a-valid-query-for-ingesting-my-data) , aby uzyskać więcej informacji i przykłady. 

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  subscriptionKey = "<paste-your-metrics-advisor-key-here>";
  apiKey ="<paste-your-metrics-advisor-api-key-here>";
  endpoint = "<paste-your-metrics-advisor-endpoint-here>";
  const sqlServerConnectionString ="<sql_server_connection_string>";
  const sqlServerQuery ="<query>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);

  const created = await createDataFeed(adminClient, sqlServerConnectionString, sqlServerQuery);
  console.log(`Data feed created: ${created.id}`);
}

async function createDataFeed(adminClient, sqlServerConnectionString, sqlServerQuery) {
  console.log("Creating Datafeed...");
  const dataFeed = {
    name: "test_datafeed_" + new Date().getTime().toString(),
    source: {
      dataSourceType: "SqlServer",
      dataSourceParameter: {
        connectionString: sqlServerConnectionString,
        query: sqlServerQuery
      }
    },
    granularity: {
      granularityType: "Daily"
    },
    schema: {
      metrics: [
        {
          name: "revenue",
          displayName: "revenue",
          description: "Metric1 description"
        },
        {
          name: "cost",
          displayName: "cost",
          description: "Metric2 description"
        }
      ],
      dimensions: [
        { name: "city", displayName: "city display" },
        { name: "category", displayName: "category display" }
      ],
      timestampColumn: null
    },
    ingestionSettings: {
      ingestionStartTime: new Date(Date.UTC(2020, 5, 1)),
      ingestionStartOffsetInSeconds: 0,
      dataSourceRequestConcurrency: -1,
      ingestionRetryDelayInSeconds: -1,
      stopRetryAfterInSeconds: -1
    },
    rollupSettings: {
      rollupType: "AutoRollup",
      rollupMethod: "Sum",
      rollupIdentificationValue: "__CUSTOM_SUM__"
    },
    missingDataPointFillSettings: {
      fillType: "SmartFilling"
    },
    accessMode: "Private",
    adminEmails: ["xyz@example.com"]
  };
  const result = await adminClient.createDataFeed(dataFeed);

  return result;
}
```

## <a name="check-ingestion-status"></a>Sprawdź stan pozyskiwania

Po rozpoczęciu pozyskiwania danych można sprawdzić stan pozyskiwania.

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const dataFeedId = "<data feed id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
  await checkIngestionStatus(
    adminClient,
    dataFeedId,
    new Date(Date.UTC(2020, 8, 1)),
    new Date(Date.UTC(2020, 8, 12))
  );
}

async function checkIngestionStatus(adminClient, datafeedId, startTime, endTime) {
  // This shows how to use for-await-of syntax to list status
  console.log("Checking ingestion status...");
  const iterator = adminClient.listDataFeedIngestionStatus(datafeedId, startTime, endTime);
  for await (const status of iterator) {
    console.log(`  [${status.timestamp}] ${status.status} - ${status.message}`);
  }
}
```

## <a name="configure-anomaly-detection"></a>Konfigurowanie wykrywania anomalii

Potrzebujemy konfiguracji wykrywania anomalii, aby określić, czy punkt w szeregu czasowym jest anomalią. Podczas gdy domyślna konfiguracja wykrywania jest automatycznie stosowana do każdej metryki, można dostosowywać tryby wykrywania używane przez dane, tworząc dostosowaną konfigurację wykrywania anomalii.

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const metricId =  "<metric id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);

  const detectionConfig = await configureAnomalyDetectionConfiguration(adminClient, metricId);
  console.log(`Detection configuration created: ${detectionConfig.id}`);
}

async function configureAnomalyDetectionConfiguration(adminClient, metricId) {
  console.log(`Creating an anomaly detection configuration on metric '${metricId}'...`);
  const detectionConfig = {
    name: "test_detection_configuration" + new Date().getTime().toString(),
    metricId,
    wholeSeriesDetectionCondition: {
      smartDetectionCondition: {
        sensitivity: 100,
        anomalyDetectorDirection: "Both",
        suppressCondition: {
          minNumber: 1,
          minRatio: 1
        }
      }
    },
    description: "Detection configuration description"
  };
  return await adminClient.createDetectionConfig(detectionConfig);
}
```

### <a name="create-a-hook"></a>Tworzenie punktu zaczepienia

Korzystamy z punktów zaczepienia, aby subskrybować alerty w czasie rzeczywistym. W tym przykładzie utworzysz element webhook dla usługi Advisor metryki, w której będzie OGŁASZAny alert.

```javascript

const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
  const hook = await createWebhookHook(adminClient);
  console.log(`Webhook hook created: ${hook.id}`);
}

async function createWebhookHook(adminClient) {
  console.log("Creating a webhook hook");
  const hook = {
    hookType: "Webhook",
    name: "web hook " + new Date().getTime().toFixed(),
    description: "description",
    hookParameter: {
      endpoint: "https://example.com/handleAlerts", // you must enter a valid webhook url to post the alert payload
      username: "username",
      password: "password"
      // certificateKey: "certificate key",
      // certificatePassword: "certificate password"
    }
  };

  return await adminClient.createHook(hook);
}
```

##  <a name="create-an-alert-configuration"></a>Tworzenie konfiguracji alertu

Ten przykład pokazuje, jak skonfigurować warunki, aby alert musi zostać wyzwolony i które punkty zaczepienia mają być używane jako miejsce docelowe dla alertu.

```javascript
const {
  MetricsAdvisorKeyCredential,
  MetricsAdvisorAdministrationClient
} = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const detectionConfigId = "<detection id>";
  const hookId = "<hook id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const adminClient = new MetricsAdvisorAdministrationClient(endpoint, credential);
  const alertConfig = await configureAlertConfiguration(adminClient, detectionConfigId, [hookId]);
  console.log(`Alert configuration created: ${alertConfig.id}`);
}

async function configureAlertConfiguration(adminClient, detectionConfigId, hookIds) {
  console.log("Creating a new alerting configuration...");
  const anomalyAlertConfig = {
    name: "test_alert_config_" + new Date().getTime().toString(),
    crossMetricsOperator: "AND",
    metricAlertConfigurations: [
      {
        detectionConfigurationId: detectionConfigId,
        alertScope: {
          scopeType: "All"
        },
        alertConditions: {
          severityCondition: { minAlertSeverity: "Medium", maxAlertSeverity: "High" }
        },
        snoozeCondition: {
          autoSnooze: 0,
          snoozeScope: "Metric",
          onlyForSuccessive: true
        }
      }
    ],
    hookIds,
    description: "Alerting config description"
  };
  return await adminClient.createAlertConfig(anomalyAlertConfig);
}
```

## <a name="query-the-alert"></a>Zapytanie dotyczące alertu

```javascript
const { MetricsAdvisorKeyCredential, MetricsAdvisorClient } = require("@azure/ai-metrics-advisor");

async function main() {
  // You will need to set these environment variables or edit the following values
  const endpoint = "<service endpoint>";
  const subscriptionKey = "<subscription key>";
  const apiKey = "<api key>";
  const alertConfigId = "<alert config id>";
  const credential = new MetricsAdvisorKeyCredential(subscriptionKey, apiKey);

  const client = new MetricsAdvisorClient(endpoint, credential);

  const alertIds = await queryAlerts(
    client,
    alertConfigId,
    new Date(Date.UTC(2020, 8, 1)),
    new Date(Date.UTC(2020, 8, 12))
  );

  if (alertIds.length > 1) {
    // query anomalies using an alert id.
    await queryAnomaliesByAlert(client, alertConfigId, alertIds[0]);
  } else {
    console.log("No alerts during the time period");
  }
}

async function queryAlerts(client, alertConfigId, startTime, endTime) {
  let alerts = [];
  const iterator = client.listAlerts(alertConfigId, startTime, endTime, "AnomalyTime");
  for await (const alert of iterator) {
    alerts.push(alert);
  }

  return alerts;
}

async function queryAnomaliesByAlert(client, alert) {
  console.log(
    `Listing anomalies for alert configuration '${alert.alertConfigId}' and alert '${alert.id}'`
  );
  const iterator = client.listAnomalies(alert);
  for await (const anomaly of iterator) {
    console.log(
      `  Anomaly ${anomaly.severity} ${anomaly.status} ${anomaly.seriesKey.dimension} ${anomaly.timestamp}`
    );
  }
}
```

### <a name="run-the-application"></a>Uruchamianie aplikacji

Uruchom aplikację za pomocą `node` polecenia w pliku szybkiego startu.

```console
node index.js
```