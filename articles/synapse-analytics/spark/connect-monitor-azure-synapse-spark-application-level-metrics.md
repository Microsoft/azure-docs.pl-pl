---
title: Samouczek — łączenie i monitorowanie metryk na poziomie aplikacji platformy Azure Synapse Spark
description: Samouczek — Dowiedz się, jak zintegrować istniejący lokalny serwer Prometheus z obszarem roboczym usługi Azure Synapse dla niemal w czasie rzeczywistym metryk aplikacji platformy Azure Spark przy użyciu łącznika Synapse Prometheus.
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 01/22/2021
ms.openlocfilehash: cb27401b2925c800ebde3b554c076f0d8cb747ca
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104593020"
---
# <a name="tutorial-connect-and-monitor-azure-synapse-spark-application-level-metrics"></a>Samouczek: łączenie i monitorowanie metryk na poziomie aplikacji platformy Azure Synapse Spark

## <a name="overview"></a>Omówienie

W tym samouczku dowiesz się, jak zintegrować istniejący lokalny serwer Prometheus z obszarem roboczym usługi Azure Synapse dla niemal w czasie rzeczywistym metryk aplikacji platformy Azure Spark przy użyciu łącznika Synapse Prometheus. 

W tym samouczku przedstawiono również interfejsy API metryk REST usługi Azure Synapse. Możesz pobrać dane metryk aplikacji platformy Spark za pomocą interfejsów API REST w celu utworzenia własnego zestawu narzędzi do monitorowania i diagnozowania lub integracji z systemami monitorowania.

## <a name="use-azure-synapse-prometheus-connector-for-your-on-premises-prometheus-servers"></a>Korzystanie z łącznika usługi Azure Synapse Prometheus dla lokalnych serwerów Prometheus

[Łącznik usługi Azure Synapse Prometheus](https://github.com/microsoft/azure-synapse-spark-metrics) to projekt Open-Source. Łącznik Synapse Prometheus używa metody odnajdowania usługi opartej na plikach, aby umożliwić:
 - Uwierzytelnij się w obszarze roboczym Synapse za pośrednictwem nazwy głównej usługi AAD.
 - Pobierz listę Apache Spark aplikacji obszaru roboczego. 
 - Ściągaj metryki aplikacji Spark za pośrednictwem konfiguracji opartej na plikach Prometheus. 

### <a name="1-prerequisite"></a>1. wymaganie wstępne

Na maszynie wirtualnej z systemem Linux należy wdrożyć serwer Prometheus.

### <a name="2-create-a-service-principal"></a>2. Tworzenie nazwy głównej usługi

Aby skorzystać z łącznika usługi Azure Synapse Prometheus na lokalnym serwerze Prometheus, należy wykonać poniższe kroki, aby utworzyć jednostkę usługi.

#### <a name="21-create-a-service-principal"></a>2,1 Tworzenie jednostki usługi:

```bash
az ad sp create-for-rbac --name <service_principal_name>
```

Wynik powinien wyglądać następująco:

```json
{
  "appId": "abcdef...",
  "displayName": "<service_principal_name>",
  "name": "http://<service_principal_name>",
  "password": "abc....",
  "tenant": "<tenant_id>"
}
```

Zanotuj identyfikatory appId, hasło i dzierżawę.

#### <a name="22-add-corresponding-permissions-to-the-service-principal-created-in-the-above-step"></a>2,2. Dodaj odpowiednie uprawnienia do nazwy głównej usługi utworzonej w powyższym kroku.

![zrzut ekranu Przydziel uprawnienia srbac](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png)

1. Zaloguj się do [obszaru roboczego usługi Azure Synapse Analytics](https://web.azuresynapse.net/) jako administrator Synapse

2. W programie Synapse Studio w okienku po lewej stronie wybierz pozycję **zarządzaj > kontroli dostępu**

3. Kliknij przycisk Dodaj w lewym górnym rogu, aby **dodać przypisanie roli**

4. W obszarze Zakres wybierz pozycję **obszar roboczy** .

5. W obszarze Rola wybierz pozycję **Synapse obliczeniowy operator**

6. W obszarze Wybierz użytkownika wprowadź **<service_principal_name>** a następnie kliknij nazwę główną usługi

7. Kliknij przycisk **Zastosuj** (odczekaj 3 minuty, aby można było zastosować odpowiednie działania).


### <a name="3-download-the-azure-synapse-prometheus-connector"></a>3. Pobierz łącznik usługi Azure Synapse Prometheus

Użyj poleceń, aby zainstalować łącznik usługi Azure Synapse Prometheus.

```bash
git clone https://github.com/microsoft/azure-synapse-spark-metrics.git
cd ./azure-synapse-spark-metrics/synapse-prometheus-connector/src
python pip install -r requirements.txt
```

### <a name="4-create-a-config-file-for-azure-synapse-workspaces"></a>4. Utwórz plik konfiguracji dla obszarów roboczych usługi Azure Synapse

Utwórz plik config. YAML w folderze config i wypełnij następujące pola: workspace_name, tenant_id, service_principal_name i service_principal_password.
W konfiguracji YAML można dodać wiele obszarów roboczych.

```yaml
workspaces:
  - workspace_name: <your_workspace_name>
    tenant_id: <tenant_id>
    service_principal_name: <service_principal_app_id>
    service_principal_password: "<service_principal_password>"
```

### <a name="5-update-the-prometheus-config"></a>5. Zaktualizuj konfigurację Prometheus

Dodaj następującą sekcję konfiguracyjną w Prometheus scrape_config i Zastąp <your_workspace_name> nazwą obszaru roboczego i <path_to_synapse_connector> do sklonowanego folderu Synapse-Prometheus-Connector

```yaml
- job_name: synapse-prometheus-connector
  static_configs:
  - labels:
      __metrics_path__: /metrics
      __scheme__: http
    targets:
    - localhost:8000
- job_name: synapse-workspace-<your_workspace_name>
  bearer_token_file: <path_to_synapse_connector>/output/workspace/<your_workspace_name>/bearer_token
  file_sd_configs:
  - files:
    - <path_to_synapse_connector>/output/workspace/<your_workspace_name>/application_discovery.json
    refresh_interval: 10s
  metric_relabel_configs:
  - source_labels: [ __name__ ]
    target_label: __name__
    regex: metrics_application_[0-9]+_[0-9]+_(.+)
    replacement: spark_$1
  - source_labels: [ __name__ ]
    target_label: __name__
    regex: metrics_(.+)
    replacement: spark_$1
```


### <a name="6-start-the-connector-in-the-prometheus-server-vm"></a>6. Uruchom łącznik na maszynie wirtualnej serwera Prometheus

Uruchom serwer łącznika na maszynie wirtualnej serwera Prometheus w następujący sposób.

```
python main.py
```

Poczekaj kilka sekund, a łącznik powinien rozpocząć pracę. Można też zobaczyć "Synapse-Prometheus-Connector" na stronie odnajdowania usługi Prometheus.

## <a name="use-azure-synapse-prometheus-or-rest-metrics-apis-to-collect-metrics-data"></a>Zbieranie danych metryk przy użyciu interfejsów API usługi Azure Synapse Prometheus lub interfejsu REST

### <a name="1-authentication"></a>1. uwierzytelnianie
Możesz użyć przepływu poświadczeń klienta, aby uzyskać token dostępu. Aby uzyskać dostęp do interfejsu API metryk, należy uzyskać token dostępu usługi Azure AD dla jednostki usługi, która ma odpowiednie uprawnienia dostępu do interfejsów API.

| Parametr     | Wymagane | Opis                                                                                                   |
| ------------- | -------- | ------------------------------------------------------------------------------------------------------------- |
| tenant_id     | Prawda     | Identyfikator dzierżawy nazwy głównej usługi platformy Azure (aplikacji)                                                          |
| grant_type    | Prawda     | Określa typ żądanego przydziału. W przepływie przydzielenia poświadczeń klienta wartość musi być client_credentials. |
| client_id     | Prawda     | Identyfikator aplikacji (nazwy głównej usługi) aplikacji zarejestrowanej w Azure Portal lub interfejsu wiersza polecenia platformy Azure.        |
| client_secret | Prawda     | Wpis tajny wygenerowany dla aplikacji (nazwy głównej usługi)                                                  |
| zasób      | Prawda     | Identyfikator URI zasobu Synapse powinien być https://dev.azuresynapse.net                                                  |

```bash
curl -X GET -H 'Content-Type: application/x-www-form-urlencoded' \
  -d 'grant_type=client_credentials&client_id=<service_principal_app_id>&resource=<azure_synapse_resource_id>&client_secret=<service_principal_secret>' \
  https://login.microsoftonline.com/<tenant_id>/oauth2/token
```

Odpowiedź wygląda następująco:

```json
{
  "token_type": "Bearer",
  "expires_in": "599",
  "ext_expires_in": "599",
  "expires_on": "1575500666",
  "not_before": "1575499766",
  "resource": "2ff8...f879c1d",
  "access_token": "ABC0eXAiOiJKV1Q......un_f1mSgCHlA"
}
```

### <a name="2-list-running-applications-in-the-azure-synapse-workspace"></a>2. Wyświetlanie listy uruchomionych aplikacji w obszarze roboczym usługi Azure Synapse

Aby uzyskać listę aplikacji platformy Spark dla obszaru roboczego Synapse, możesz wykonać czynności opisane w tym dokumencie [monitorowanie — Pobierz listę zadań platformy Spark](/rest/api/synapse/data-plane/monitoring/getsparkjoblist).


### <a name="3-collect-spark-application-metrics-with-the-prometheus-or-rest-apis"></a>3. zbieranie metryk aplikacji platformy Spark za pomocą interfejsów API Prometheus lub REST


#### <a name="collect-spark-application-metrics-with-the-prometheus-api"></a>Zbieranie metryk aplikacji platformy Spark za pomocą interfejsu API Prometheus

Pobierz najnowsze metryki określonej aplikacji platformy Spark za pomocą interfejsu API Prometheus

```
GET https://{endpoint}/livyApi/versions/{livyApiVersion}/sparkpools/{sparkPoolName}/sessions/{sessionId}/applications/{sparkApplicationId}/metrics/executors/prometheus?format=html
```

| Parametr          | Wymagane | Opis                                                                               |
| ------------------ | -------- | ----------------------------------------------------------------------------------------- |
| endpoint           | Prawda     | Na przykład punkt końcowy tworzenia obszaru roboczego https://myworkspace.dev.azuresynapse.net . |
| livyApiVersion     | Prawda     | Prawidłowy interfejs API-Version dla żądania. Obecnie jest to 2019-11-01 — wersja zapoznawcza                    |
| sparkPoolName      | Prawda     | Nazwa puli platformy Spark.                                                                   |
| sessionId          | Prawda     | Identyfikator sesji.                                                               |
| sparkApplicationId | Prawda     | Identyfikator aplikacji platformy Spark                                                                      |

Przykładowe żądanie: 

```
GET https://myworkspace.dev.azuresynapse.net/livyApi/versions/2019-11-01-preview/sparkpools/mysparkpool/sessions/1/applications/application_1605509647837_0001/metrics/executors/prometheus?format=html
```

Przykładowa odpowiedź:

Kod stanu: 200 odpowiedź wygląda następująco:

```
metrics_executor_rddBlocks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_memoryUsed_bytes{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 74992
metrics_executor_diskUsed_bytes{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_totalCores{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_maxTasks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_activeTasks{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 1
metrics_executor_failedTasks_total{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 0
metrics_executor_completedTasks_total{application_id="application_1605509647837_0001", application_name="mynotebook_mysparkpool_1605509570802", executor_id="driver"} 2
...

```

#### <a name="collect-spark-application-metrics-with-the-rest-api"></a>Zbieranie metryk aplikacji platformy Spark za pomocą interfejsu API REST

```
GET https://{endpoint}/livyApi/versions/{livyApiVersion}/sparkpools/{sparkPoolName}/sessions/{sessionId}/applications/{sparkApplicationId}/executors
```

| Parametr          | Wymagane | Opis                                                                               |
| ------------------ | -------- | ----------------------------------------------------------------------------------------- |
| endpoint           | Prawda     | Na przykład punkt końcowy tworzenia obszaru roboczego https://myworkspace.dev.azuresynapse.net . |
| livyApiVersion     | Prawda     | Prawidłowy interfejs API-Version dla żądania. Obecnie jest to 2019-11-01 — wersja zapoznawcza                    |
| sparkPoolName      | Prawda     | Nazwa puli platformy Spark.                                                                   |
| sessionId          | Prawda     | Identyfikator sesji.                                                               |
| sparkApplicationId | Prawda     | Identyfikator aplikacji platformy Spark                                                                      |

Przykładowe żądanie

```
GET https://myworkspace.dev.azuresynapse.net/livyApi/versions/2019-11-01-preview/sparkpools/mysparkpool/sessions/1/applications/application_1605509647837_0001/executors
```

Przykładowy kod stanu odpowiedzi: 200

```json
[
    {
        "id": "driver",
        "hostPort": "f98b8fc2aea84e9095bf2616208eb672007bde57624:45889",
        "isActive": true,
        "rddBlocks": 0,
        "memoryUsed": 75014,
        "diskUsed": 0,
        "totalCores": 0,
        "maxTasks": 0,
        "activeTasks": 0,
        "failedTasks": 0,
        "completedTasks": 0,
        "totalTasks": 0,
        "totalDuration": 0,
        "totalGCTime": 0,
        "totalInputBytes": 0,
        "totalShuffleRead": 0,
        "totalShuffleWrite": 0,
        "isBlacklisted": false,
        "maxMemory": 15845975654,
        "addTime": "2020-11-16T06:55:06.718GMT",
        "executorLogs": {
            "stdout": "http://f98b8fc2aea84e9095bf2616208eb672007bde57624:8042/node/containerlogs/container_1605509647837_0001_01_000001/trusted-service-user/stdout?start=-4096",
            "stderr": "http://f98b8fc2aea84e9095bf2616208eb672007bde57624:8042/node/containerlogs/container_1605509647837_0001_01_000001/trusted-service-user/stderr?start=-4096"
        },
        "memoryMetrics": {
            "usedOnHeapStorageMemory": 75014,
            "usedOffHeapStorageMemory": 0,
            "totalOnHeapStorageMemory": 15845975654,
            "totalOffHeapStorageMemory": 0
        },
        "blacklistedInStages": []
    },
    // ...
]
```


### <a name="4-build-your-own-diagnosis-and-monitoring-tools"></a>4. Utwórz własne narzędzia do diagnostyki i monitorowania

Interfejs API Prometheus i interfejsy API REST zapewniają zaawansowane dane metryk aplikacji platformy Spark z uruchomionymi informacjami. Dane metryk związanych z aplikacją można zbierać za pomocą interfejsu API Prometheus i interfejsów API REST. I twórz własne narzędzia do diagnostyki i monitorowania, które są bardziej odpowiednie dla Twoich potrzeb.