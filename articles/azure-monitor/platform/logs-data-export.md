---
title: Log Analytics eksportu danych obszaru roboczego w Azure Monitor (wersja zapoznawcza)
description: Log Analytics eksportu danych umożliwia ciągłe eksportowanie danych z wybranych tabel z obszaru roboczego Log Analytics do konta usługi Azure Storage lub Event Hubs platformy Azure w miarę ich zbierania.
ms.subservice: logs
ms.topic: conceptual
ms.custom: references_regions, devx-track-azurecli
author: bwren
ms.author: bwren
ms.date: 10/14/2020
ms.openlocfilehash: bd929d06bca370ffab53ce2023188bc12a1d8bd1
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186443"
---
# <a name="log-analytics-workspace-data-export-in-azure-monitor-preview"></a>Log Analytics eksportu danych obszaru roboczego w Azure Monitor (wersja zapoznawcza)
Log Analytics eksport danych obszaru roboczego w programie Azure Monitor umożliwia ciągłe eksportowanie danych z wybranych tabel w obszarze roboczym Log Analytics do konta usługi Azure Storage lub usługi Azure Event Hubs w miarę ich zbierania. Ten artykuł zawiera szczegółowe informacje dotyczące tej funkcji oraz czynności konfigurowania eksportu danych w obszarach roboczych.

## <a name="overview"></a>Omówienie
Po skonfigurowaniu eksportu danych dla obszaru roboczego Log Analytics wszystkie nowe dane wysyłane do wybranych tabel w obszarze roboczym zostaną automatycznie wyeksportowane do konta magazynu co godzinę lub do centrum zdarzeń w czasie zbliżonym do rzeczywistego.

![Eksportowanie danych — omówienie](media/logs-data-export/data-export-overview.png)

Wszystkie dane z dołączonych tabel są eksportowane bez filtra. Na przykład podczas konfigurowania reguły eksportu danych dla tabeli *SecurityEvent* wszystkie dane wysyłane do tabeli *SecurityEvent* są eksportowane od czasu konfiguracji.


## <a name="other-export-options"></a>Inne opcje eksportu
Log Analytics eksport danych obszaru roboczego ciągle eksportuje dane z Log Analytics obszaru roboczego. Inne opcje eksportu danych dla konkretnych scenariuszy obejmują następujące elementy:

- Zaplanowano eksport z zapytania dziennika przy użyciu aplikacji logiki. Jest to podobne do funkcji eksportu danych, ale umożliwia wysyłanie filtrowanych lub zagregowanych danych do usługi Azure Storage. Ta metoda jest niezależna od [limitów zapytania log](../service-limits.md#log-analytics-workspaces)  zobacz [dane archiwalne z obszaru roboczego log Analytics do usługi Azure Storage przy użyciu aplikacji logiki](logs-export-logic-app.md).
- Eksport jednorazowy przy użyciu aplikacji logiki. Zobacz [Azure monitor Logs łącznik Logic Apps i automatyzacji](logicapp-flow-connector.md).
- Jednorazowe Eksportowanie do komputera lokalnego przy użyciu skryptu programu PowerShell. Zobacz [Invoke-AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport).


## <a name="current-limitations"></a>Bieżące ograniczenia

- Konfigurację można obecnie wykonać tylko przy użyciu interfejsu wiersza polecenia lub żądania REST. Nie można użyć Azure Portal ani programu PowerShell.
- ```--export-all-tables```Opcja w interfejsie wiersza polecenia i REST nie jest obsługiwana i zostanie usunięta. Należy jawnie podać listę tabel w regułach eksportowania.
- Obsługiwane tabele są obecnie ograniczone do określonych w poniższej sekcji [obsługiwane tabele](#supported-tables) . Jeśli reguła eksportu danych zawiera nieobsługiwaną tabelę, operacja zakończy się pomyślnie, ale żadne dane nie zostaną wyeksportowane dla tej tabeli. Jeśli reguła eksportu danych zawiera tabelę, która nie istnieje, zostanie zakończona niepowodzeniem z powodu błędu ```Table <tableName> does not exist in the workspace.```
- Obszar roboczy Log Analytics może znajdować się w dowolnym regionie, z wyjątkiem następujących:
  - Szwajcaria Północna
  - Szwajcaria Zachodnia
  - Regiony platformy Azure dla instytucji rządowych
- Docelowe konto magazynu lub centrum zdarzeń musi znajdować się w tym samym regionie co obszar roboczy Log Analytics.
- Nazwy tabel, które mają zostać wyeksportowane, nie mogą być dłuższe niż 60 znaków dla konta magazynu i nie więcej niż 47 znaków do centrum zdarzeń. Tabele o dłuższych nazwach nie zostaną wyeksportowane.

> [!NOTE]
> Log Analytics eksportu danych zapisuje dane jako Dołącz obiekt BLOB, który jest obecnie w wersji zapoznawczej dla Azure Data Lake Storage Gen2. Musisz otworzyć żądanie obsługi przed skonfigurowaniem eksportu do tego magazynu. Poniżej przedstawiono szczegóły dotyczące tego żądania.
> - Typ wydania: Techniczne
> - Subskrypcja: Twoja subskrypcja
> - Usługa: Data Lake Storage Gen2
> - Zasób: nazwa zasobu
> - Podsumowanie: żądanie rejestracji subskrypcji w celu zaakceptowania danych z eksportu danych Log Analytics.
> - Typ problemu: łączność
> - Podtyp problemu: problem z łącznością

## <a name="data-completeness"></a>Kompletność danych
Eksport danych będzie nadal ponawiać próbę wysłania danych przez maksymalnie 30 minut w przypadku, gdy miejsce docelowe jest niedostępne. Jeśli nadal nie jest dostępna po 30 minutach, dane zostaną odrzucone do momentu udostępnienia lokalizacji docelowej.

## <a name="cost"></a>Cost (Koszt)
Nie są obecnie naliczane dodatkowe opłaty za funkcję eksportowania danych. Cennik dotyczący eksportu danych zostanie ogłoszony w przyszłości oraz powiadomienie podane przed rozpoczęciem rozliczania. Jeśli zdecydujesz się na kontynuowanie korzystania z eksportu danych po upływie okresu wypowiedzenia, zostanie naliczona stawka ze stosowną stawką.

## <a name="export-destinations"></a>Eksportuj miejsca docelowe

### <a name="storage-account"></a>Konto magazynu
Dane są wysyłane do kont magazynu co godzinę. Konfiguracja eksportu danych tworzy kontener dla każdej tabeli na koncie magazynu o nazwie, po *której następuje nazwa* tabeli. Na przykład tabela *SecurityEvent* będzie wysyłana do kontenera o nazwie *am-SecurityEvent*.

Ścieżka obiektu BLOB konta magazynu to *WorkspaceResourceId =/subscriptions/Subscription-ID/ResourceGroups/ \<resource-group\> /providers/Microsoft.operationalinsights/Workspaces/ \<workspace\> /y = \<four-digit numeric year\> /m = \<two-digit numeric month\> /d = \<two-digit numeric day\> /h = \<two-digit 24-hour clock hour\> /m = 00/PT1H.js*. Ponieważ dołączane obiekty blob są ograniczone do 50 000 zapisów w magazynie, liczba eksportowanych obiektów BLOB może zostać rozszerzona, jeśli liczba dołączeń jest wysoka. Wzorzec nazewnictwa dla obiektów BLOB w takich przypadkach zostałby PT1H_ #. JSON, gdzie # to przyrostowa liczba obiektów BLOB.

Format danych konta magazynu to [wiersze JSON](./resource-logs-blob-format.md). Oznacza to, że każdy rekord jest rozdzielony znakiem nowego wiersza, bez tablicy rekordów zewnętrznych i bez przecinków między rekordami JSON. 

[![Dane przykładowe magazynu](media/logs-data-export/storage-data.png)](media/logs-data-export/storage-data.png#lightbox)

Log Analytics eksportu danych może pisać Dodawanie obiektów BLOB do niezmiennych kont magazynu, gdy zasady przechowywania oparte na czasie mają włączone ustawienie *allowProtectedAppendWrites* . Pozwala to na zapisywanie nowych bloków do dołączanego obiektu BLOB przy zachowaniu ochrony i zgodności niezmienności. Zobacz [Zezwalanie na chronione operacje Dołącz obiekty blob](../../storage/blobs/storage-blob-immutable-storage.md#allow-protected-append-blobs-writes).

### <a name="event-hub"></a>Centrum zdarzeń
Dane są wysyłane do centrum zdarzeń niemal w czasie rzeczywistym, gdy osiągnie Azure Monitor. Centrum zdarzeń jest tworzone dla każdego typu danych, który jest eksportowany *z nazwą i nazwą tabeli* . Na przykład tabela *SecurityEvent* będzie wysyłana do centrum zdarzeń o nazwie *am-SecurityEvent*. Jeśli chcesz, aby wyeksportowane dane miały dostęp do określonego centrum zdarzeń, lub jeśli masz tabelę o nazwie przekraczającej limit znaków 47, możesz podać własną nazwę centrum zdarzeń i wyeksportować wszystkie dane do określonych tabel.

Zagadnienia do rozważenia:
1. Jednostka SKU centrum zdarzeń "Basic" obsługuje dolny [Limit](../../event-hubs/event-hubs-quotas.md#basic-vs-standard-tiers) rozmiaru zdarzenia, a niektóre dzienniki w obszarze roboczym mogą przekraczać tę wartość i zostać porzucone. Zalecamy używanie "standardowego" lub "dedykowanego" centrum zdarzeń jako miejsca docelowego eksportu.
2. Ilość wyeksportowanych danych często rośnie wraz z upływem czasu, a skalowanie centrum zdarzeń należy zwiększyć, aby obsługiwać większe szybkości transferu i uniknąć opóźnień i opóźnienia danych. Należy użyć funkcji automatycznego rozbudowy Event Hubs, aby automatycznie skalować w górę i zwiększyć liczbę jednostek przepływności oraz spełnić wymagania dotyczące użycia. Aby uzyskać szczegółowe informacje, zobacz [Automatyczne skalowanie jednostek przepływności usługi Azure Event Hubs](../../event-hubs/event-hubs-auto-inflate.md) .

## <a name="prerequisites"></a>Wymagania wstępne
Poniżej podano wymagania wstępne, które należy wykonać przed skonfigurowaniem eksportu danych Log Analytics.

- Konto magazynu i centrum zdarzeń muszą być już utworzone i muszą znajdować się w tym samym regionie co obszar roboczy Log Analytics. Jeśli musisz replikować dane na inne konta magazynu, możesz użyć dowolnej z [opcji nadmiarowości usługi Azure Storage](../../storage/common/storage-redundancy.md).  
- Konto magazynu musi mieć wartość StorageV1 lub StorageV2. Klasyczny magazyn nie jest obsługiwany  
- Jeśli konto magazynu zostało skonfigurowane tak, aby zezwalać na dostęp z wybranych sieci, należy dodać wyjątek w ustawieniach konta magazynu, aby umożliwić Azure Monitor zapisywania w magazynie.

## <a name="enable-data-export"></a>Włącz eksportowanie danych
Aby włączyć eksportowanie danych Log Analytics, należy wykonać poniższe czynności. Więcej informacji na temat każdej z nich można znaleźć w poniższych sekcjach.

- Zarejestruj dostawcę zasobów.
- Zezwalaj na zaufane usługi firmy Microsoft.
- Utwórz co najmniej jedną regułę eksportu danych, która definiuje tabele do eksportowania i ich miejsce docelowe.

### <a name="register-resource-provider"></a>Rejestrowanie dostawcy zasobów
Następujący dostawca zasobów platformy Azure musi być zarejestrowany dla Twojej subskrypcji, aby można było włączyć Log Analytics eksportu danych. 

- Microsoft. Insights

Ten dostawca zasobów prawdopodobnie jest już zarejestrowany dla większości użytkowników Azure Monitor. Aby sprawdzić, przejdź do pozycji **subskrypcje** w Azure Portal. Wybierz swoją subskrypcję, a następnie kliknij pozycję **dostawcy zasobów** w sekcji **Ustawienia** w menu. Znajdź **Microsoft. Insights**. Jeśli jego stan jest **zarejestrowany**, jest już zarejestrowany. W przeciwnym razie kliknij pozycję **zarejestruj** , aby go zarejestrować.

Możesz również użyć dowolnej z dostępnych metod, aby zarejestrować dostawcę zasobów zgodnie z opisem w temacie [dostawcy zasobów platformy Azure i typy](../../azure-resource-manager/management/resource-providers-and-types.md). Poniżej przedstawiono przykładowe polecenie przy użyciu programu PowerShell:

```PowerShell
Register-AzResourceProvider -ProviderNamespace Microsoft.insights
```

### <a name="allow-trusted-microsoft-services"></a>Zezwalaj na zaufane usługi firmy Microsoft
Jeśli konto magazynu zostało skonfigurowane tak, aby zezwalać na dostęp z wybranych sieci, musisz dodać wyjątek, aby zezwolić Azure Monitor na zapis na koncie. Z poziomu **zapór i sieci wirtualnych** dla konta magazynu wybierz opcję **Zezwalaj zaufanym usługom firmy Microsoft na dostęp do tego konta magazynu**.

[![Zapory i sieci wirtualne konta magazynu](media/logs-data-export/storage-account-vnet.png)](media/logs-data-export/storage-account-vnet.png#lightbox)


### <a name="create-or-update-data-export-rule"></a>Utwórz lub zaktualizuj regułę eksportu danych
Reguła eksportu danych definiuje dane, które mają zostać wyeksportowane dla zestawu tabel w jednym miejscu docelowym. Można utworzyć regułę dla każdego miejsca docelowego.


# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Poniższe polecenie interfejsu wiersza polecenia służy do wyświetlania tabel w obszarze roboczym. Może to pomóc w skopiowaniu żądanych tabel i uwzględnieniu w regule eksportowania danych.

```azurecli
az monitor log-analytics workspace table list -resource-group resourceGroupName --workspace-name workspaceName --query [].name --output table
```

Użyj poniższego polecenia, aby utworzyć regułę eksportu danych do konta magazynu przy użyciu interfejsu wiersza polecenia.

```azurecli
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $storageAccountId
```

Użyj poniższego polecenia, aby utworzyć regułę eksportu danych do centrum zdarzeń przy użyciu interfejsu wiersza polecenia.

```azurecli
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubsNamespacesId
```

# <a name="rest"></a>[REST](#tab/rest)

Użyj poniższego żądania, aby utworzyć regułę eksportu danych przy użyciu interfejsu API REST. Żądanie powinno korzystać z autoryzacji tokenu okaziciela i aplikacji typu Content/JSON.

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```

Treść żądania określa miejsce docelowe tabel. Poniżej znajduje się Przykładowa treść żądania REST dla konta magazynu.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
            "table1",
            "table2" 
        ],
        "enable": true
    }
}
```

Poniżej znajduje się Przykładowa treść żądania REST centrum zdarzeń.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
}
```

Poniżej znajduje się Przykładowa treść żądania REST dla centrum zdarzeń, w którym podano nazwę centrum zdarzeń. W takim przypadku wszystkie wyeksportowane dane są wysyłane do tego centrum zdarzeń.

```json
{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.EventHub/namespaces/eventhub-namespaces-name",
            "metaData": {
                "EventHubName": "eventhub-name"
        },
        "tablenames": [
            "table1",
            "table2"
        ],
        "enable": true
    }
  }
}
```
---

## <a name="view-data-export-configuration"></a>Wyświetl konfigurację eksportu danych

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Użyj następującego polecenia, aby wyświetlić konfigurację reguły eksportu danych przy użyciu interfejsu wiersza polecenia.

```azurecli
az monitor log-analytics workspace data-export show --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

# <a name="rest"></a>[REST](#tab/rest)

Użyj poniższego żądania, aby wyświetlić konfigurację reguły eksportu danych przy użyciu interfejsu API REST. Żądanie powinno korzystać z autoryzacji tokenu okaziciela.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```
---

## <a name="disable-an-export-rule"></a>Wyłącz regułę eksportu

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Reguły eksportu można wyłączyć w celu zatrzymania eksportowania, gdy nie trzeba przechowywać danych przez pewien okres, na przykład podczas przeprowadzania testowania. Użyj poniższego polecenia, aby wyłączyć regułę eksportu danych przy użyciu interfejsu wiersza polecenia.

```azurecli
az monitor log-analytics workspace data-export update --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --enable false
```

# <a name="rest"></a>[REST](#tab/rest)

Aby wyłączyć regułę eksportu danych przy użyciu interfejsu API REST, należy użyć następującego żądania. Żądanie powinno korzystać z autoryzacji tokenu okaziciela.

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
Authorization: Bearer <token>
Content-type: application/json

{
    "properties": {
        "destination": {
            "resourceId": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name"
        },
        "tablenames": [
"table1",
    "table2" 
        ],
        "enable": false
    }
}
```
---

## <a name="delete-an-export-rule"></a>Usuwanie reguły eksportu

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Użyj poniższego polecenia, aby usunąć regułę eksportu danych przy użyciu interfejsu wiersza polecenia.

```azurecli
az monitor log-analytics workspace data-export delete --resource-group resourceGroupName --workspace-name workspaceName --name ruleName
```

# <a name="rest"></a>[REST](#tab/rest)

Użyj poniższego żądania, aby usunąć regułę eksportu danych przy użyciu interfejsu API REST. Żądanie powinno korzystać z autoryzacji tokenu okaziciela.

```rest
DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports/<data-export-name>?api-version=2020-08-01
```
---

## <a name="view-all-data-export-rules-in-a-workspace"></a>Wyświetlanie wszystkich reguł eksportu danych w obszarze roboczym

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Użyj następującego polecenia, aby wyświetlić wszystkie reguły eksportu danych w obszarze roboczym przy użyciu interfejsu wiersza polecenia.

```azurecli
az monitor log-analytics workspace data-export list --resource-group resourceGroupName --workspace-name workspaceName
```

# <a name="rest"></a>[REST](#tab/rest)

Poniższe żądanie służy do wyświetlania wszystkich reguł eksportu danych w obszarze roboczym przy użyciu interfejsu API REST. Żądanie powinno korzystać z autoryzacji tokenu okaziciela.

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.operationalInsights/workspaces/<workspace-name>/dataexports?api-version=2020-08-01
```
---

## <a name="unsupported-tables"></a>Nieobsługiwane tabele
Jeśli reguła eksportu danych zawiera nieobsługiwaną tabelę, konfiguracja zakończy się pomyślnie, ale żadne dane nie zostaną wyeksportowane dla tej tabeli. Jeśli tabela jest później obsługiwana, wówczas jej dane zostaną wyeksportowane w tym czasie.

Jeśli reguła eksportu danych zawiera tabelę, która nie istnieje, zostanie zakończona niepowodzeniem z powodu błędu ```Table <tableName> does not exist in the workspace.```


## <a name="supported-tables"></a>Obsługiwane tabele
Obsługiwane tabele są obecnie ograniczone do określonych poniżej. Wszystkie dane z tabeli zostaną wyeksportowane, chyba że zostaną określone ograniczenia. Ta lista zostanie zaktualizowana, ponieważ zostanie dodana obsługa dodatkowych tabel.


| Tabela | Ograniczenia |
|:---|:---|
| AADDomainServicesAccountLogon | |
| AADDomainServicesAccountManagement | |
| AADDomainServicesDirectoryServiceAccess | |
| AADDomainServicesLogonLogoff | |
| AADDomainServicesPolicyChange | |
| AADDomainServicesPrivilegeUse | |
| AADManagedIdentitySignInLogs | |
| AADNonInteractiveUserSignInLogs | |
| AADProvisioningLogs | |
| AADServicePrincipalSignInLogs | |
| ADAssessmentRecommendation | |
| ADFActivityRun | |
| ADFPipelineRun | |
| ADFTriggerRun | |
| ADReplicationResult | |
| ADSecurityAssessmentRecommendation | |
| ADTDigitalTwinsOperation | |
| ADTEventRoutesOperation | |
| ADTModelsOperation | |
| ADTQueryOperation | |
| ADXCommand | |
| ADXQuery | |
| AegDeliveryFailureLogs | |
| AegPublishFailureLogs | |
| Alerty |Pomoc techniczna częściowa. Niektóre dane do tej tabeli są pozyskiwane za pomocą konta magazynu. Te dane nie są obecnie eksportowane. |
| Anomalie | |
| ApiManagementGatewayLogs | |
| AppCenterError | |
| AppPlatformSystemLogs | |
| AppServiceAppLogs | |
| AppServiceAuditLogs | |
| AppServiceConsoleLogs | |
| AppServiceFileAuditLogs | |
| AppServiceHTTPLogs | |
| AppServiceIPSecAuditLogs | |
| AppServicePlatformLogs | |
| AuditLogs | |
| AutoscaleEvaluationsLog | |
| AutoscaleScaleActionsLog | |
| AWSCloudTrail | |
| AzureAssessmentRecommendation | |
| AzureDevOpsAuditing | |
| BehaviorAnalytics | |
| BlockchainApplicationLog | |
| BlockchainProxyLog | |
| BlockchainProxyLog | |
| CommonSecurityLog | |
| CommonSecurityLog | |
| ComputerGroup | |
| ConfigurationData | Pomoc techniczna częściowa. Niektóre dane są pozyskiwane za pomocą usług wewnętrznych, które nie są obsługiwane w przypadku eksportowania. Te dane nie są obecnie eksportowane. |
| ContainerImageInventory | |
| ContainerInventory | |
| ContainerLog | |
| ContainerNodeInventory | |
| ContainerServiceLog | |
| CoreAzureBackup | |
| DatabricksAccounts | |
| DatabricksClusters | |
| DatabricksDBFS | |
| DatabricksInstancePools | |
| DatabricksJobs | |
| DatabricksNotebook | |
| DatabricksSecrets | |
| DatabricksSQLPermissions | |
| DatabricksSSH | |
| DatabricksWorkspace | |
| DnsEvents | |
| DnsInventory | |
| Dynamics365Activity | |
| Zdarzenie | Pomoc techniczna częściowa. Niektóre dane do tej tabeli są pozyskiwane za pomocą konta magazynu. Te dane nie są obecnie eksportowane. |
| ExchangeAssessmentRecommendation | |
| FailedIngestion | |
| FunctionAppLogs | |
| HDInsightAmbariClusterAlerts | |
| HDInsightAmbariSystemMetrics | |
| HDInsightGatewayAuditLogs | |
| HDInsightHadoopAndYarnLogs | |
| HDInsightHadoopAndYarnMetrics | |
| HDInsightHBaseLogs | |
| HDInsightHBaseMetrics | |
| HDInsightHiveAndLLAPLogsSample | |
| HDInsightKafkaLogs | |
| HDInsightKafkaMetrics | |
| HDInsightOozieLogs | |
| HDInsightSecurityLogs | |
| HDInsightSparkApplicationEvents | |
| HDInsightSparkBlockManagerEvents | |
| HDInsightSparkEnvironmentEvents | |
| HDInsightSparkEventsLog | |
| HDInsightSparkExecutorEvents | |
| HDInsightSparkExtraEvents | |
| HDInsightSparkJobEvents | |
| HDInsightSparkLogs | |
| HDInsightSparkSQLExecutionEvents | |
| HDInsightSparkStageEvents | |
| HDInsightSparkStageTaskAccumulables | |
| HDInsightSparkTaskEvents | |
| HDInsightStormLogs | |
| HDInsightStormMetrics | |
| HDInsightStormTopologyMetrics | |
| Puls | |
| HuntingBookmark | |
| InsightsMetrics | Pomoc techniczna częściowa. Niektóre dane są pozyskiwane za pomocą usług wewnętrznych, które nie są obsługiwane w przypadku eksportowania. Ta część nie jest obecnie dostępna w eksporcie. |
| IntuneAuditLogs | |
| IntuneDeviceComplianceOrg | |
| IntuneOperationalLogs | |
| KubeEvents | |
| KubeHealth | |
| KubeMonAgentEvents | |
| KubeNodeInventory | |
| KubePodInventory | |
| KubeServices | |
| KubeServices | |
| LAQueryLogs | |
| McasShadowItReporting | |
| MicrosoftAzureBastionAuditLogs | |
| MicrosoftDataShareReceivedSnapshotLog | |
| MicrosoftDataShareSentSnapshotLog | |
| MicrosoftDataShareShareLog | |
| MicrosoftHealthcareApisAuditLogs | |
| NWConnectionMonitorDestinationListenerResult | |
| NWConnectionMonitorDNSResult | |
| NWConnectionMonitorPathResult | |
| NWConnectionMonitorPathResult | |
| NWConnectionMonitorTestResult | |
| NWConnectionMonitorTestResult | |
| Pakiet Office | Pomoc techniczna częściowa. Niektóre dane do pozyskiwania za pośrednictwem elementów webhook z pakietu Office 365 do Log Analytics. Te dane nie są obecnie eksportowane. |
| Operacja | Pomoc techniczna częściowa. Niektóre dane są pozyskiwane za pomocą usług wewnętrznych, które nie są obsługiwane w przypadku eksportowania. Te dane nie są obecnie eksportowane. |
| Wyd. | Pomoc techniczna częściowa. Obecnie obsługiwane są tylko dane wydajności systemu Windows. Dane wydajności systemu Linux nie są obecnie eksportowane. |
| ProtectionStatus | |
| SCCMAssessmentRecommendation | |
| SCOMAssessmentRecommendation | |
| SecurityAlert | |
| SecurityBaseline | |
| SecurityBaselineSummary | |
| SecurityDetection | |
| SecurityEvent | |
| SecurityIncident | |
| SecurityIoTRawEvent | |
| SecurityNestedRecommendation | |
| SecurityRecommendation | |
| SfBAssessmentRecommendation | |
| SfBOnlineAssessmentRecommendation | |
| SharePointOnlineAssessmentRecommendation | |
| SignalRServiceDiagnosticLogs | |
| SigninLogs | |
| SPAssessmentRecommendation | |
| SQLAssessmentRecommendation | |
| SucceededIngestion | |
| SynapseGatewayEvents | |
| SynapseRBACEvents | |
| Dziennik systemu | Pomoc techniczna częściowa. Niektóre dane do tej tabeli są pozyskiwane za pomocą konta magazynu. Te dane nie są obecnie eksportowane. |
| ThreatIntelligenceIndicator | |
| Aktualizacja | Pomoc techniczna częściowa. Niektóre dane są pozyskiwane za pomocą usług wewnętrznych, które nie są obsługiwane w przypadku eksportowania. Te dane nie są obecnie eksportowane. |
| UpdateRunProgress | |
| UpdateSummary | |
| Użycie | |
| UserAccessAnalytics | |
| UserPeerAnalytics | |
| Listy do obejrzenia | |
| WindowsEvent | |
| WindowsFirewall | |
| Typowe | Pomoc techniczna częściowa. Niektóre dane są pozyskiwane za pomocą usług wewnętrznych, które nie są obsługiwane w przypadku eksportowania. Te dane nie są obecnie eksportowane. |
| WorkloadMonitoringPerf | |
| WVDAgentHealthStatus | |
| WVDCheckpoints | |
| WVDConnections | |
| WVDErrors | |
| WVDFeeds | |
| WVDManagement | |


## <a name="next-steps"></a>Następne kroki

- [Wykonaj zapytanie dotyczące wyeksportowanych danych z usługi Azure Eksplorator danych](azure-data-explorer-query-storage.md).