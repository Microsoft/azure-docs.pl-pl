---
title: Log Analytics eksportu danych obszaru roboczego w Azure Monitor (wersja zapoznawcza)
description: Log Analytics eksportu danych umożliwia ciągłe eksportowanie danych z wybranych tabel z obszaru roboczego Log Analytics do konta usługi Azure Storage lub Event Hubs platformy Azure w miarę ich zbierania.
ms.subservice: logs
ms.topic: conceptual
ms.custom: references_regions, devx-track-azurecli
author: bwren
ms.author: bwren
ms.date: 02/07/2021
ms.openlocfilehash: ea33eff30e712c1597c3606d74cb6d56683211ae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102615588"
---
# <a name="log-analytics-workspace-data-export-in-azure-monitor-preview"></a>Log Analytics eksportu danych obszaru roboczego w Azure Monitor (wersja zapoznawcza)
Log Analytics eksport danych obszaru roboczego w programie Azure Monitor umożliwia ciągłe eksportowanie danych z wybranych tabel w obszarze roboczym Log Analytics do konta usługi Azure Storage lub usługi Azure Event Hubs w miarę ich zbierania. Ten artykuł zawiera szczegółowe informacje dotyczące tej funkcji oraz czynności konfigurowania eksportu danych w obszarach roboczych.

## <a name="overview"></a>Omówienie
Po skonfigurowaniu eksportu danych dla obszaru roboczego Log Analytics wszystkie nowe dane wysyłane do wybranych tabel w obszarze roboczym zostaną automatycznie wyeksportowane do konta magazynu w ciągu godzinowo dołączanych obiektów blob lub do centrum zdarzeń w czasie niemal rzeczywistym.

![Eksportowanie danych — omówienie](media/logs-data-export/data-export-overview.png)

Wszystkie dane z dołączonych tabel są eksportowane bez filtra. Na przykład podczas konfigurowania reguły eksportu danych dla tabeli *SecurityEvent* wszystkie dane wysyłane do tabeli *SecurityEvent* są eksportowane od czasu konfiguracji.


## <a name="other-export-options"></a>Inne opcje eksportu
Log Analytics eksport danych obszaru roboczego ciągle eksportuje dane z Log Analytics obszaru roboczego. Inne opcje eksportu danych dla konkretnych scenariuszy obejmują następujące elementy:

- Zaplanowano eksport z zapytania dziennika przy użyciu aplikacji logiki. Jest to podobne do funkcji eksportu danych, ale umożliwia wysyłanie filtrowanych lub zagregowanych danych do usługi Azure Storage. Ta metoda jest uzależniona od [limitów zapytania dziennika](../service-limits.md#log-analytics-workspaces), zobacz temat [archiwizowanie danych z obszaru roboczego log Analytics w usłudze Azure Storage przy użyciu aplikacji logiki](logs-export-logic-app.md).
- Jednorazowe Eksportowanie do komputera lokalnego przy użyciu skryptu programu PowerShell. Zobacz [Invoke-AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport).


## <a name="limitations"></a>Ograniczenia

- Konfigurację można wykonać przy użyciu interfejsu wiersza polecenia lub żądań REST. Azure Portal lub program PowerShell nie są jeszcze obsługiwane.
- ```--export-all-tables```Opcja w interfejsie wiersza polecenia i REST nie jest obsługiwana i zostanie usunięta. Należy jawnie podać listę tabel w regułach eksportowania.
- Obsługiwane tabele są obecnie ograniczone do określonych w poniższej sekcji [obsługiwane tabele](#supported-tables) . Na przykład niestandardowe tabele dzienników nie są obecnie obsługiwane.
- Jeśli reguła eksportu danych zawiera nieobsługiwaną tabelę, operacja zakończy się pomyślnie, ale żadne dane nie zostaną wyeksportowane do tej tabeli, dopóki tabela nie zostanie obsługiwana. 
- Jeśli reguła eksportu danych zawiera tabelę, która nie istnieje, wystąpi błąd ```Table <tableName> does not exist in the workspace``` .
- Obszar roboczy Log Analytics może znajdować się w dowolnym regionie, z wyjątkiem następujących:
  - Regiony Azure Government
  - Japonia Zachodnia
  - Brazylia Południowa Południowo-Wschodnia
  - Norwegia Wschodnia
  - Północne Zjednoczone Emiraty Arabskie
- Można utworzyć dwie reguły eksportowania w obszarze roboczym — w programie może istnieć jedna reguła do centrum zdarzeń i jedna reguła do konta magazynu.
- Docelowe konto magazynu lub centrum zdarzeń musi znajdować się w tym samym regionie co obszar roboczy Log Analytics.
- Nazwy tabel, które mają zostać wyeksportowane, nie mogą być dłuższe niż 60 znaków dla konta magazynu i nie więcej niż 47 znaków do centrum zdarzeń. Tabele o dłuższych nazwach nie zostaną wyeksportowane.
- Obsługa dołączania obiektów BLOB dla Azure Data Lake Storage jest teraz w [ograniczonej publicznej wersji zapoznawczej](https://azure.microsoft.com/updates/append-blob-support-for-azure-data-lake-storage-preview/)

## <a name="data-completeness"></a>Kompletność danych
Eksport danych będzie nadal ponawiać próbę wysłania danych przez maksymalnie 30 minut w przypadku, gdy miejsce docelowe jest niedostępne. Jeśli nadal nie jest dostępna po 30 minutach, dane zostaną odrzucone do momentu udostępnienia lokalizacji docelowej.

## <a name="cost"></a>Koszt
Nie są obecnie naliczane dodatkowe opłaty za funkcję eksportowania danych. Cennik dotyczący eksportu danych zostanie ogłoszony w przyszłości oraz powiadomienie podane przed rozpoczęciem rozliczania. Jeśli zdecydujesz się na kontynuowanie korzystania z eksportu danych po upływie okresu wypowiedzenia, zostanie naliczona stawka ze stosowną stawką.

## <a name="export-destinations"></a>Eksportuj miejsca docelowe

### <a name="storage-account"></a>Konto magazynu
Dane są wysyłane do kont magazynu, gdy dotrze do Azure Monitor i przechowywane w godzinie dołączanych obiektów BLOB. Konfiguracja eksportu danych tworzy kontener dla każdej tabeli na koncie magazynu o nazwie, po *której następuje nazwa* tabeli. Na przykład tabela *SecurityEvent* będzie wysyłana do kontenera o nazwie *am-SecurityEvent*.

Ścieżka obiektu BLOB konta magazynu to *WorkspaceResourceId =/subscriptions/Subscription-ID/ResourceGroups/ \<resource-group\> /providers/Microsoft.operationalinsights/Workspaces/ \<workspace\> /y = \<four-digit numeric year\> /m = \<two-digit numeric month\> /d = \<two-digit numeric day\> /h = \<two-digit 24-hour clock hour\> /m = 00/PT1H.js*. Ponieważ dołączane obiekty blob są ograniczone do 50 000 zapisów w magazynie, liczba eksportowanych obiektów BLOB może zostać rozszerzona, jeśli liczba dołączeń jest wysoka. Wzorzec nazewnictwa dla obiektów BLOB w takich przypadkach zostałby PT1H_ #. JSON, gdzie # to przyrostowa liczba obiektów BLOB.

Format danych konta magazynu to [wiersze JSON](../essentials/resource-logs-blob-format.md). Oznacza to, że każdy rekord jest rozdzielony znakiem nowego wiersza, bez tablicy rekordów zewnętrznych i bez przecinków między rekordami JSON. 

[![Dane przykładowe magazynu](media/logs-data-export/storage-data.png)](media/logs-data-export/storage-data.png#lightbox)

Log Analytics eksportu danych może pisać Dodawanie obiektów BLOB do niezmiennych kont magazynu, gdy zasady przechowywania oparte na czasie mają włączone ustawienie *allowProtectedAppendWrites* . Pozwala to na zapisywanie nowych bloków do dołączanego obiektu BLOB przy zachowaniu ochrony i zgodności niezmienności. Zobacz [Zezwalanie na chronione operacje Dołącz obiekty blob](../../storage/blobs/storage-blob-immutable-storage.md#allow-protected-append-blobs-writes).

> [!NOTE]
> Obsługa dołączania obiektów BLOB dla Azure Data Lake Storage jest teraz dostępna w wersji zapoznawczej we wszystkich regionach świadczenia usługi Azure. [Zarejestruj się w ograniczonej publicznej wersji zapoznawczej](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4mEEwKhLjlBjU3ziDwLH-pURDk2NjMzUTVEVzU5UU1XUlRXSTlHSlkxQS4u) przed utworzeniem reguły eksportowania w celu Azure Data Lake magazynu. Eksport nie będzie działać bez rejestracji.

### <a name="event-hub"></a>Centrum zdarzeń
Dane są wysyłane do centrum zdarzeń niemal w czasie rzeczywistym, gdy osiągnie Azure Monitor. Centrum zdarzeń jest tworzone dla każdego typu danych, który jest eksportowany *z nazwą i nazwą tabeli* . Na przykład tabela *SecurityEvent* będzie wysyłana do centrum zdarzeń o nazwie *am-SecurityEvent*. Jeśli chcesz, aby wyeksportowane dane miały dostęp do określonego centrum zdarzeń, lub jeśli masz tabelę o nazwie przekraczającej limit znaków 47, możesz podać własną nazwę centrum zdarzeń i wyeksportować wszystkie dane do określonych tabel.

> [!IMPORTANT]
> [Liczba obsługiwanych centrów zdarzeń na przestrzeń nazw wynosi 10](../../event-hubs/event-hubs-quotas.md#common-limits-for-all-tiers). Jeśli eksportujesz więcej niż 10 tabel, podaj własną nazwę centrum zdarzeń, aby wyeksportować wszystkie tabele do tego centrum zdarzeń.

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
Reguła eksportu danych definiuje tabele, dla których dane są eksportowane i miejsce docelowe. Dla każdego miejsca docelowego można utworzyć pojedynczą regułę.

Reguła eksportu powinna zawierać tabele, które znajdują się w obszarze roboczym. Uruchom to zapytanie, aby wyświetlić listę dostępnych tabel w obszarze roboczym.

```kusto
find where TimeGenerated > ago(24h) | distinct Type
```

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Nie dotyczy

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Nie dotyczy

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Użyj poniższego polecenia, aby utworzyć regułę eksportu danych do konta magazynu przy użyciu interfejsu wiersza polecenia.

```azurecli
$storageAccountResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $storageAccountResourceId
```

Użyj poniższego polecenia, aby utworzyć regułę eksportu danych do centrum zdarzeń przy użyciu interfejsu wiersza polecenia. Dla każdej tabeli tworzony jest osobne centrum zdarzeń.

```azurecli
$eventHubsNamespacesResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.EventHub/namespaces/namespaces-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubsNamespacesResourceId
```

Użyj poniższego polecenia, aby utworzyć regułę eksportu danych do określonego centrum zdarzeń przy użyciu interfejsu wiersza polecenia. Wszystkie tabele są eksportowane do podanej nazwy centrum zdarzeń. 

```azurecli
$eventHubResourceId = '/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.EventHub/namespaces/namespaces-name/eventHubName/eventhub-name'
az monitor log-analytics workspace data-export create --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --tables SecurityEvent Heartbeat --destination $eventHubResourceId
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

# <a name="template"></a>[Szablon](#tab/json)

Użyj poniższego polecenia, aby utworzyć regułę eksportu danych do konta magazynu przy użyciu szablonu.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "storageAccountRuleName": {
            "defaultValue": "storage-account-rule-name",
            "type": "string"
        },
        "storageAccountResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Storage/storageAccounts/storage-account-name",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
                {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/' , parameters('storageAccountRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('storageAccountResourceId')]"
                      },
                      "tableNames": [
                          "Heartbeat",
                          "InsightsMetrics",
                          "VMConnection",
                          "Usage"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

Użyj następującego polecenia, aby utworzyć regułę eksportu danych do centrum zdarzeń przy użyciu szablonu. Dla każdej tabeli tworzony jest osobne centrum zdarzeń.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "eventhubRuleName": {
            "defaultValue": "event-hub-rule-name",
            "type": "string"
        },
        "namespacesResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/microsoft.eventhub/namespaces/namespaces-name",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
              {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/', parameters('eventhubRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('namespacesResourceId')]"
                      },
                      "tableNames": [
                          "Usage",
                          "Heartbeat"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

Użyj poniższego polecenia, aby utworzyć regułę eksportu danych do określonego centrum zdarzeń przy użyciu szablonu. Wszystkie tabele są eksportowane do podanej nazwy centrum zdarzeń.

```
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaceName": {
            "defaultValue": "workspace-name",
            "type": "String"
        },
        "workspaceLocation": {
            "defaultValue": "workspace-region",
            "type": "string"
        },
        "eventhubRuleName": {
            "defaultValue": "event-hub-rule-name",
            "type": "string"
        },
        "namespacesResourceId": {
            "defaultValue": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/microsoft.eventhub/namespaces/namespaces-name",
            "type": "String"
        },
        "eventhubName": {
            "defaultValue": "event-hub-name",
            "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaceName')]",
            "location": "[parameters('workspaceLocation')]",
            "resources": [
              {
                  "type": "microsoft.operationalinsights/workspaces/dataexports",
                  "apiVersion": "2020-08-01",
                  "name": "[concat(parameters('workspaceName'), '/', parameters('eventhubRuleName'))]",
                  "dependsOn": [
                      "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaceName'))]"
                  ],
                  "properties": {
                      "destination": {
                          "resourceId": "[parameters('namespacesResourceId')]",
                          "metaData": {
                              "eventHubName": "[parameters('eventhubName')]"
                          }
                      },
                      "tableNames": [
                          "Usage",
                          "Heartbeat"
                      ],
                      "enable": true
                  }
              }
            ]
        }
    ]
}
```

---

## <a name="view-data-export-rule-configuration"></a>Wyświetl konfigurację reguły eksportu danych

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Nie dotyczy

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Nie dotyczy

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

# <a name="template"></a>[Szablon](#tab/json)

Nie dotyczy

---

## <a name="disable-an-export-rule"></a>Wyłącz regułę eksportu

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Nie dotyczy

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Nie dotyczy

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Reguły eksportu można wyłączyć w celu zatrzymania eksportowania, gdy nie trzeba przechowywać danych przez pewien okres, na przykład podczas przeprowadzania testowania. Użyj poniższego polecenia, aby wyłączyć regułę eksportu danych przy użyciu interfejsu wiersza polecenia.

```azurecli
az monitor log-analytics workspace data-export update --resource-group resourceGroupName --workspace-name workspaceName --name ruleName --enable false
```

# <a name="rest"></a>[REST](#tab/rest)

Reguły eksportu można wyłączyć w celu zatrzymania eksportowania, gdy nie trzeba przechowywać danych przez pewien okres, na przykład podczas przeprowadzania testowania. Aby wyłączyć regułę eksportu danych przy użyciu interfejsu API REST, należy użyć następującego żądania. Żądanie powinno korzystać z autoryzacji tokenu okaziciela.

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

# <a name="template"></a>[Szablon](#tab/json)

Reguły eksportu można wyłączyć w celu zatrzymania eksportowania, gdy nie trzeba przechowywać danych przez pewien okres, na przykład podczas przeprowadzania testowania. Ustaw ```"enable": false``` w szablonie, aby wyłączyć eksport danych.

---

## <a name="delete-an-export-rule"></a>Usuwanie reguły eksportu

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Nie dotyczy

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Nie dotyczy

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

# <a name="template"></a>[Szablon](#tab/json)

Nie dotyczy

---

## <a name="view-all-data-export-rules-in-a-workspace"></a>Wyświetlanie wszystkich reguł eksportu danych w obszarze roboczym

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Nie dotyczy

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Nie dotyczy

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

# <a name="template"></a>[Szablon](#tab/json)

Nie dotyczy

---

## <a name="unsupported-tables"></a>Nieobsługiwane tabele
Jeśli reguła eksportu danych zawiera nieobsługiwaną tabelę, konfiguracja zakończy się pomyślnie, ale żadne dane nie zostaną wyeksportowane dla tej tabeli. Jeśli tabela jest później obsługiwana, wówczas jej dane zostaną wyeksportowane w tym czasie.

Jeśli reguła eksportu danych zawiera tabelę, która nie istnieje, zostanie zakończona niepowodzeniem z błędem "tabela nie <tableName> istnieje w obszarze roboczym".


## <a name="supported-tables"></a>Obsługiwane tabele
Obsługiwane tabele są obecnie ograniczone do określonych poniżej. Wszystkie dane z tabeli zostaną wyeksportowane, chyba że zostaną określone ograniczenia. Ta lista zostanie zaktualizowana, ponieważ zostanie dodana obsługa dodatkowych tabel.


| Tabela | Ograniczenia |
|:---|:---|
| AADDomainServicesAccountLogon |  |
| AADDomainServicesAccountManagement |  |
| AADDomainServicesDirectoryServiceAccess |  |
| AADDomainServicesLogonLogoff |  |
| AADDomainServicesPolicyChange |  |
| AADDomainServicesPrivilegeUse |  |
| AADManagedIdentitySignInLogs |  |
| AADNonInteractiveUserSignInLogs |  |
| AADProvisioningLogs |  |
| AADServicePrincipalSignInLogs |  |
| ABSBotRequests |  |
| ACSBillingUsage |  |
| ACSSMSIncomingOperations |  |
| ADAssessmentRecommendation |  |
| ADFActivityRun |  |
| ADFPipelineRun |  |
| ADFTriggerRun |  |
| ADReplicationResult |  |
| ADSecurityAssessmentRecommendation |  |
| ADTDigitalTwinsOperation |  |
| ADTEventRoutesOperation |  |
| ADTModelsOperation |  |
| ADTQueryOperation |  |
| ADXCommand |  |
| ADXQuery |  |
| AegDeliveryFailureLogs |  |
| AegPublishFailureLogs |  |
| Alerty |  |
| AmlOnlineEndpointConsoleLog |  |
| ApiManagementGatewayLogs |  |
| AppCenterError |  |
| AppPlatformSystemLogs |  |
| AppServiceAppLogs |  |
| AppServiceAuditLogs |  |
| AppServiceConsoleLogs |  |
| AppServiceFileAuditLogs |  |
| AppServiceHTTPLogs |  |
| AppServicePlatformLogs |  |
| AuditLogs |  |
| AutoscaleEvaluationsLog |  |
| AutoscaleScaleActionsLog |  |
| AWSCloudTrail |  |
| AzureAssessmentRecommendation |  |
| AzureDevOpsAuditing |  |
| BehaviorAnalytics |  |
| BlockchainApplicationLog |  |
| BlockchainProxyLog |  |
| CommonSecurityLog |  |
| ComputerGroup |  |
| ConfigurationData | Pomoc techniczna częściowa — niektóre dane są pozyskane za pomocą usług wewnętrznych, które nie są obsługiwane w przypadku eksportowania. Ta część nie jest obecnie dostępna w eksporcie. |
| ContainerImageInventory |  |
| ContainerInventory |  |
| ContainerLog |  |
| ContainerNodeInventory |  |
| ContainerServiceLog |  |
| CoreAzureBackup |  |
| DatabricksAccounts |  |
| DatabricksClusters |  |
| DatabricksDBFS |  |
| DatabricksInstancePools |  |
| DatabricksJobs |  |
| DatabricksNotebook |  |
| DatabricksSecrets |  |
| DatabricksSQLPermissions |  |
| DatabricksSSH |  |
| DatabricksWorkspace |  |
| DnsEvents |  |
| DnsInventory |  |
| Dynamics365Activity |  |
| Zdarzenie | Pomoc techniczna częściowa — niektóre dane w tej tabeli są pozyskiwane za pomocą konta magazynu. Ta część nie jest obecnie dostępna w eksporcie. |
| ExchangeAssessmentRecommendation |  |
| FailedIngestion |  |
| FunctionAppLogs |  |
| Puls |  |
| HuntingBookmark |  |
| InsightsMetrics | Pomoc techniczna częściowa — niektóre dane są pozyskane za pomocą usług wewnętrznych, które nie są obsługiwane w przypadku eksportowania. Ta część nie jest obecnie dostępna w eksporcie. |
| IntuneAuditLogs |  |
| IntuneDevices |  |
| IntuneOperationalLogs |  |
| KubeEvents |  |
| KubeHealth |  |
| KubeMonAgentEvents |  |
| KubeNodeInventory |  |
| KubePodInventory |  |
| KubeServices |  |
| LAQueryLogs |  |
| McasShadowItReporting |  |
| MicrosoftAzureBastionAuditLogs |  |
| MicrosoftDataShareReceivedSnapshotLog |  |
| MicrosoftDataShareSentSnapshotLog |  |
| MicrosoftHealthcareApisAuditLogs |  |
| NWConnectionMonitorPathResult |  |
| NWConnectionMonitorTestResult |  |
| Pakiet Office | Pomoc techniczna częściowa — niektóre dane do pozyskiwania za pośrednictwem elementów webhook z usługi O365 do LA. Ta część nie jest obecnie dostępna w eksporcie. |
| Operacja | Pomoc techniczna częściowa — niektóre dane są pozyskane za pomocą usług wewnętrznych, które nie są obsługiwane w przypadku eksportowania. Ta część nie jest obecnie dostępna w eksporcie. |
| Wyd. | Obsługa częściowa — obecnie obsługiwane są tylko dane wydajności systemu Windows. Obecnie brak danych wydajności systemu Linux w eksporcie. |
| PowerBIDatasetsTenant |  |
| PowerBIDatasetsWorkspace |  |
| PowerBIDatasetsWorkspacePreview |  |
| SCCMAssessmentRecommendation |  |
| SCOMAssessmentRecommendation |  |
| SecurityAlert |  |
| SecurityBaseline |  |
| SecurityBaselineSummary |  |
| SecurityDetection |  |
| SecurityEvent | Pomoc techniczna częściowa — niektóre dane w tej tabeli są pozyskiwane za pomocą konta magazynu. Ta część nie jest obecnie dostępna w eksporcie. |
| SecurityIncident |  |
| SecurityIoTRawEvent |  |
| SecurityNestedRecommendation |  |
| SecurityRecommendation |  |
| SfBAssessmentRecommendation |  |
| SfBOnlineAssessmentRecommendation |  |
| SharePointOnlineAssessmentRecommendation |  |
| SignalRServiceDiagnosticLogs |  |
| SigninLogs |  |
| SPAssessmentRecommendation |  |
| SQLAssessmentRecommendation |  |
| SucceededIngestion |  |
| SynapseBigDataPoolApplicationsEnded |  |
| SynapseBuiltinSqlPoolRequestsEnded |  |
| SynapseGatewayApiRequests |  |
| SynapseIntegrationActivityRuns |  |
| SynapseIntegrationPipelineRuns |  |
| SynapseIntegrationTriggerRuns |  |
| SynapseRbacOperations |  |
| SynapseSqlPoolDmsWorkers |  |
| SynapseSqlPoolExecRequests |  |
| SynapseSqlPoolRequestSteps |  |
| SynapseSqlPoolSqlRequests |  |
| SynapseSqlPoolWaits |  |
| Dziennik systemu | Pomoc techniczna częściowa — niektóre dane w tej tabeli są pozyskiwane za pomocą konta magazynu. Ta część nie jest obecnie dostępna w eksporcie. |
| ThreatIntelligenceIndicator |  |
| Aktualizacja | Pomoc techniczna częściowa — niektóre dane są pozyskane za pomocą usług wewnętrznych, które nie są obsługiwane w przypadku eksportowania. Ta część nie jest obecnie dostępna w eksporcie. |
| UpdateRunProgress |  |
| UpdateSummary |  |
| Użycie |  |
| Listy do obejrzenia |  |
| WindowsEvent |  |
| WindowsFirewall |  |
| Typowe | Pomoc techniczna częściowa — niektóre dane są pozyskane za pomocą usług wewnętrznych, które nie są obsługiwane w przypadku eksportowania. Ta część nie jest obecnie dostępna w eksporcie. |
| WVDCheckpoints |  |
| WVDConnections |  |
| WVDErrors |  |
| WVDFeeds |  |
| WVDManagement |  |


## <a name="next-steps"></a>Następne kroki

- [Wykonaj zapytanie dotyczące wyeksportowanych danych z usługi Azure Eksplorator danych](../logs/azure-data-explorer-query-storage.md).
