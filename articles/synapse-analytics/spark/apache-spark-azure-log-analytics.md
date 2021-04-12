---
title: Zbieranie i wizualizowanie metryk i dzienników przy użyciu usługi Azure Log Analytics (wersja zapoznawcza)
description: Dowiedz się, jak włączyć Synapse wbudowanego łącznika usługi Azure Log Analytics na potrzeby zbierania i wysyłania Apache Spark metryk aplikacji i dzienników do obszaru roboczego Log Analytics platformy Azure.
services: synapse-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 03/25/2021
ms.custom: references_regions
ms.openlocfilehash: 243618192593d93bba9d5229e7becfb2af62ce32
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108565"
---
# <a name="tutorial-use-azure-log-analytics-to-collect-and-visualize-metrics-and-logs-preview"></a>Samouczek: korzystanie z usługi Azure Log Analytics do zbierania i wizualizacji metryk i dzienników (wersja zapoznawcza)

W tym samouczku dowiesz się, jak włączyć Synapse wbudowanego łącznika usługi Azure Log Analytics do zbierania i wysyłania Apache Spark metryk aplikacji i dzienników do [obszaru roboczego log Analytics platformy Azure](/azure/azure-monitor/logs/quick-create-workspace). Następnie można wykorzystać skoroszyt usługi Azure monitor do wizualizacji metryk i dzienników.

## <a name="configure-azure-log-analytics-workspace-information-in-synapse-studio"></a>Konfigurowanie informacji o obszarze roboczym usługi Azure Log Analytics w programie Synapse Studio

### <a name="step-1-create-an-azure-log-analytics-workspace"></a>Krok 1. Tworzenie obszaru roboczego usługi Azure Log Analytics

Aby utworzyć obszar roboczy Log Analytics, można użyć poniższych dokumentów:
- [Tworzenie obszaru roboczego usługi Log Analytics w witrynie Azure Portal](https://docs.microsoft.com/azure/azure-monitor/logs/quick-create-workspace)
- [Tworzenie obszaru roboczego Log Analytics przy użyciu interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/azure-monitor/logs/quick-create-workspace-cli)
- [Tworzenie i Konfigurowanie obszaru roboczego Log Analytics w Azure Monitor przy użyciu programu PowerShell](https://docs.microsoft.com/azure/azure-monitor/logs/powershell-workspace-configuration)

### <a name="step-2-prepare-a-spark-configuration-file"></a>Krok 2. Przygotowywanie pliku konfiguracji platformy Spark

#### <a name="option-1-configure-with-azure-log-analytics-workspace-id-and-key"></a>Sposób 1. Konfigurowanie przy użyciu identyfikatora i klucza obszaru roboczego usługi Azure Log Analytics 

Skopiuj poniższą konfigurację platformy Spark, Zapisz ją jako **"spark_loganalytics_conf.txt"** i wypełnij parametry:

   - `<LOG_ANALYTICS_WORKSPACE_ID>`: Identyfikator obszaru roboczego usługi Azure Log Analytics.
   - `<LOG_ANALYTICS_WORKSPACE_KEY>`: Azure Log Analytics Key: **Azure Portal > azure log Analytics obszar roboczy > Zarządzanie agentami > kluczem podstawowym**

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.secret <LOG_ANALYTICS_WORKSPACE_KEY>
```

#### <a name="option-2-configure-with-an-azure-key-vault"></a>Sposób 2. Skonfiguruj przy użyciu Azure Key Vault

> [!NOTE]
>
> Należy przyznać uprawnienia do odczytu klucza tajnego użytkownikom, którzy będą przesyłać aplikacje platformy Spark. Zobacz [zapewnianie dostępu do Key Vault kluczy, certyfikatów i wpisów tajnych za pomocą kontroli dostępu opartej na rolach na platformie Azure](https://docs.microsoft.com/azure/key-vault/general/rbac-guide)

Aby skonfigurować Azure Key Vault do przechowywania klucza obszaru roboczego, wykonaj następujące czynności:

1. Utwórz i przejdź do magazynu kluczy w Azure Portal
2. Na stronie Ustawienia Key Vault wybierz pozycję wpisy **tajne**.
3. Kliknij pozycję **Wygeneruj/zaimportuj**.
4. Na ekranie **Tworzenie wpisu tajnego** wybierz następujące wartości:
   - **Nazwa**: wpisz nazwę wpisu tajnego, a następnie wpisz `"SparkLogAnalyticsSecret"` wartość domyślną.
   - **Wartość**: wpisz **<LOG_ANALYTICS_WORKSPACE_KEY>** dla wpisu tajnego.
   - Dla pozostałych opcji zostaw wartości domyślne. Kliknij pozycję **Utwórz**.
5. Skopiuj poniższą konfigurację platformy Spark, Zapisz ją jako **"spark_loganalytics_conf.txt"** i wypełnij parametry:

   - `<LOG_ANALYTICS_WORKSPACE_ID>`: Identyfikator obszaru roboczego usługi Azure Log Analytics.
   - `<AZURE_KEY_VAULT_NAME>`: Skonfigurowana nazwa Azure Key Vault.
   - `<AZURE_KEY_VAULT_SECRET_KEY_NAME>` (Opcjonalnie): Nazwa klucza tajnego w Azure Key Vault dla klucza obszaru roboczego, wartość domyślna: "SparkLogAnalyticsSecret".

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.keyVault.name <AZURE_KEY_VAULT_NAME>
spark.synapse.logAnalytics.keyVault.key.secret <AZURE_KEY_VAULT_SECRET_KEY_NAME>
```

> [!NOTE]
>
> Identyfikator obszaru roboczego Log Analytics można również zapisać w magazynie kluczy platformy Azure. Zapoznaj się z powyższymi krokami i Zapisz identyfikator obszaru roboczego z nazwą klucza tajnego `"SparkLogAnalyticsWorkspaceId"` . Lub użyj konfiguracji, `spark.synapse.logAnalytics.keyVault.key.workspaceId` Aby określić nazwę wpisu tajnego identyfikatora obszaru roboczego w magazynie kluczy platformy Azure.

#### <a name="option-3-configure-with-an-azure-key-vault-linked-service"></a>Sposób 3. Konfigurowanie przy użyciu połączonej usługi Azure Key Vault

> [!NOTE]
>
> Musisz udzielić uprawnienia do odczytu klucza tajnego w obszarze roboczym Synapse. Zobacz [zapewnianie dostępu do Key Vault kluczy, certyfikatów i wpisów tajnych za pomocą kontroli dostępu opartej na rolach na platformie Azure](https://docs.microsoft.com/azure/key-vault/general/rbac-guide)

Aby skonfigurować Azure Key Vault połączoną usługę w programie Synapse Studio do przechowywania klucza obszaru roboczego, wykonaj następujące czynności:

1. Wykonaj wszystkie kroki opisane w `Option 2. Configure with an Azure Key Vault` sekcji.
2. Tworzenie połączonej usługi magazynu kluczy Azure w programie Synapse Studio:

    a. Przejdź do **Synapse Studio > Zarządzaj połączonymi usługami >**, kliknij przycisk **Nowy** przycisk.

    b. Wyszukaj **Azure Key Vault** w polu wyszukiwania.

    c. Wpisz nazwę połączonej usługi.

    d. Wybierz magazyn kluczy platformy Azure. Kliknij pozycję **Utwórz**.

3. Dodaj `spark.synapse.logAnalytics.keyVault.linkedServiceName` element do konfiguracji platformy Spark.

```properties
spark.synapse.logAnalytics.enabled true
spark.synapse.logAnalytics.workspaceId <LOG_ANALYTICS_WORKSPACE_ID>
spark.synapse.logAnalytics.keyVault.name <AZURE_KEY_VAULT_NAME>
spark.synapse.logAnalytics.keyVault.key.secret <AZURE_KEY_VAULT_SECRET_KEY_NAME>
spark.synapse.logAnalytics.keyVault.linkedServiceName <LINKED_SERVICE_NAME>
```

#### <a name="available-spark-configuration"></a>Dostępna konfiguracja platformy Spark

| Nazwa konfiguracji                                  | Wartość domyślna                | Opis                                                                                                                                                                                                |
| --------------------------------------------------- | ---------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Spark. Synapse. logAnalytics. Enabled                  | fałsz                        | Aby włączyć funkcję ujścia Log Analytics platformy Azure dla aplikacji platformy Spark, należy wykonać wartość true. W przeciwnym razie wartość false.                                                                                                                  |
| Spark. Synapse. logAnalytics. identyfikator obszaru roboczego              | -                            | Identyfikator obszaru roboczego Log Analytics platformy Azure                                                                                                                                                          |
| Spark. Synapse. logAnalytics. Secret                   | -                            | Docelowy wpis tajny obszaru roboczego platformy Azure Log Analytics.                                                                                                                                                      |
| Spark. Synapse. logAnalytics. linkedServiceName   | -                            | Nazwa połączonej usługi magazynu kluczy platformy Azure dla identyfikatora i klucza obszaru roboczego usługi Azure Log Analytics                                                                                                                       |
| spark.synapse.logAnalytics.keyVault.name            | -                            | Nazwa magazynu kluczy platformy Azure dla identyfikatora i klucza Log Analytics platformy Azure                                                                                                                                                |
| Spark. Synapse. logAnalytics. Key. identyfikator obszaru roboczego | SparkLogAnalyticsWorkspaceId | Nazwa wpisu tajnego magazynu kluczy platformy Azure dla identyfikatora obszaru roboczego usługi Azure Log Analytics                                                                                                                                       |
| Spark. Synapse. logAnalytics. Key. klucz. Secret      | SparkLogAnalyticsSecret      | Nazwa wpisu tajnego magazynu kluczy platformy Azure dla klucza obszaru roboczego usługi Azure Log Analytics                                                                                                                                      |
| Spark. Synapse. logAnalytics. uriSuffix       | ods.opinsights.azure.com     | Docelowy [sufiks URI][uri_suffix]obszaru roboczego usługi Azure log Analytics. Jeśli obszar roboczy platformy Azure Log Analytics nie znajduje się na platformie Azure Global, należy zaktualizować sufiks identyfikatora URI zgodnie z odpowiednią chmurą. |

> [!NOTE]  
> - W przypadku chmur z platformą Azure (Chiny) parametr "Spark. Synapse. logAnalytics. uriSuffix" powinien mieć wartość "ods.opinsights.azure.cn". 
> - W przypadku chmur usługi Azure gov parametr "Spark. Synapse. logAnalytics. uriSuffix" powinien mieć wartość "ods.opinsights.azure.us". 

[uri_suffix]: https://docs.microsoft.com/azure/azure-monitor/logs/data-collector-api#request-uri


### <a name="step-3-upload-your-spark-configuration-to-a-spark-pool"></a>Krok 3. przekazywanie konfiguracji platformy Spark do puli platformy Spark
Plik konfiguracji można przekazać do puli Synapse Spark w programie Synapse Studio.

   1. Przejdź do puli Apache Spark w usłudze Azure Synapse Studio (pule Apache Spark zarządzania >)
   2. Kliknij przycisk **"..."** po prawej stronie puli Apache Spark
   3. Wybierz konfigurację Apache Spark 
   4. Kliknij przycisk **Przekaż** i wybierz utworzony **"spark_loganalytics_conf.txt"** .
   5. Kliknij przycisk **Przekaż** i **Zastosuj**.

      > [!div class="mx-imgBorder"]
      > ![Konfiguracja puli platformy Spark](./media/apache-spark-azure-log-analytics/spark-pool-configuration.png)

> [!NOTE] 
>
> Wszystkie aplikacje platformy Spark przesłane do puli Spark powyżej będą używały ustawienia konfiguracji do wypychania metryk aplikacji platformy Spark i dzienników do określonego obszaru roboczego usługi Azure Log Analytics.

## <a name="submit-a-spark-application-and-view-the-logs-and-metrics-in-azure-log-analytics"></a>Przesyłanie aplikacji platformy Spark i wyświetlanie dzienników i metryk w usłudze Azure Log Analytics

 1. Aplikację Spark można przesłać do puli platformy Spark skonfigurowanej w poprzednim kroku, korzystając z jednej z następujących metod:
    - Uruchom Notes programu Synapse Studio. 
    - Prześlij zadanie wsadowe Synapse Apache Spark za pomocą definicji zadania platformy Spark.
    - Uruchom potok zawierający działanie platformy Spark.

 2. Przejdź do określonego obszaru roboczego usługi Azure Log Analytics, a następnie Wyświetl metryki i dzienniki aplikacji podczas uruchamiania aplikacji platformy Spark.

## <a name="use-the-sample-azure-log-analytics-workbook-to-visualize-the-metrics-and-logs"></a>Użyj przykładowego skoroszytu Log Analytics platformy Azure do wizualizacji metryk i dzienników

1. [Pobierz skoroszyt](https://aka.ms/SynapseSparkLogAnalyticsWorkbook) tutaj.
2. Otwórz i **Skopiuj** zawartość pliku skoroszytu.
3. Przejdź do skoroszytu usługi Azure Log Analytics ([Azure Portal](https://portal.azure.com/) > log Analytics obszarze roboczym > skoroszytów)
4. Otwórz **pusty** skoroszyt log Analytics platformy Azure w trybie **"Edytor zaawansowany"** (naciśnij ikonę </>).
5. **Wklej** cały kod JSON, który istnieje.
6. Następnie naciśnij przycisk **Zastosuj** , aby **wykonać edycję**.

    > [!div class="mx-imgBorder"]
    > ![nowy skoroszyt](./media/apache-spark-azure-log-analytics/new-workbook.png)

    > [!div class="mx-imgBorder"]
    > ![Importuj skoroszyt](./media/apache-spark-azure-log-analytics/import-workbook.png)

Następnie Prześlij swoją aplikację Apache Spark do skonfigurowanej puli platformy Spark. Gdy aplikacja stanie się uruchomiona, wybierz działającą aplikację na liście rozwijanej skoroszyt.

> [!div class="mx-imgBorder"]
> ![imange skoroszytu](./media/apache-spark-azure-log-analytics/workbook.png)

Można też dostosować skoroszyt za pomocą zapytania Kusto i skonfigurować alerty.

> [!div class="mx-imgBorder"]
> ![Kusto zapytania i alerty](./media/apache-spark-azure-log-analytics/kusto-query-and-alerts.png)

## <a name="sample-kusto-queries"></a>Przykładowe zapytania Kusto

1. Przykład zapytania dotyczącego zdarzeń platformy Spark.

   ```kusto
   SparkListenerEvent_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | order by TimeGenerated desc
   | limit 100 
   ```

2. Przykład zapytań dotyczących sterownika aplikacji platformy Spark i dzienników wykonawców.

   ```kusto
   SparkLoggingEvent_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | order by TimeGenerated desc
   | limit 100
   ```

3. Przykład zapytań o metryki platformy Spark.

   ```kusto
   SparkMetrics_CL
   | where workspaceName_s == "{SynapseWorkspace}" and clusterName_s == "{SparkPool}" and livyId_s == "{LivyId}"
   | where name_s endswith "jvm.total.used"
   | summarize max(value_d) by bin(TimeGenerated, 30s), executorId_s
   | order by TimeGenerated asc
   ```

## <a name="create-and-manage-alerts-using-azure-log-analytics"></a>Tworzenie alertów i zarządzanie nimi przy użyciu usługi Azure Log Analytics

Azure Monitor alerty umożliwiają użytkownikom użycie zapytania Log Analytics w celu obliczenia metryk i dzienników dla każdej ustawionej częstotliwości oraz wyzwolenia alertu na podstawie wyników.

Aby uzyskać więcej informacji, zobacz [Tworzenie i wyświetlanie alertów dzienników oraz zarządzanie nimi za pomocą Azure monitor](https://docs.microsoft.com/azure/azure-monitor/alerts/alerts-log).

## <a name="limitation"></a>Ograniczenie

 - Obszar roboczy analizy usługi Azure Synapse z włączoną obsługą [zarządzanej sieci wirtualnej](https://docs.microsoft.com/azure/synapse-analytics/security/synapse-workspace-managed-vnet) nie jest obsługiwany.
 - Następujące regiony nie są obecnie obsługiwane:
   - Wschodnie stany USA 2
   - Norwegia Wschodnia
   - Północne Zjednoczone Emiraty Arabskie

## <a name="next-steps"></a>Następne kroki

 - Dowiedz się, jak [używać bezserwerowej puli Apache Spark w programie Synapse Studio](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-apache-spark-pool-studio).
 - Dowiedz się, jak [uruchomić aplikację Spark w notesie](https://docs.microsoft.com/azure/synapse-analytics/spark/apache-spark-development-using-notebooks).
 - Dowiedz się, jak [utworzyć Apache Spark definicję zadania w programie Synapse Studio](https://docs.microsoft.com/azure/synapse-analytics/spark/apache-spark-job-definitions).