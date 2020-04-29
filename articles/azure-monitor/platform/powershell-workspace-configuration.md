---
title: Tworzenie & konfigurowania Log Analytics przy użyciu programu PowerShell
description: Log Analytics obszary robocze w Azure Monitor przechowywanie danych z serwerów w infrastrukturze lokalnej lub w chmurze. Dane dotyczące maszyn można zbierać z usługi Azure Storage, gdy są generowane przez diagnostykę platformy Azure.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/19/2019
ms.openlocfilehash: 2584cedceab1386cbab9c72bb4b510eebe2122bd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80054707"
---
# <a name="manage-log-analytics-workspace-in-azure-monitor-using-powershell"></a>Zarządzanie obszarem roboczym Log Analytics w Azure Monitor przy użyciu programu PowerShell

Za pomocą [poleceń cmdlet programu PowerShell log Analytics](https://docs.microsoft.com/powershell/module/az.operationalinsights/) można wykonywać różne funkcje w log Analytics obszarze roboczym w Azure monitor z wiersza polecenia lub jako część skryptu.  Przykłady zadań, które można wykonać za pomocą programu PowerShell, to m.in.:

* Tworzenie obszaru roboczego
* Dodawanie lub usuwanie rozwiązania
* Importuj i Eksportuj zapisane wyszukiwania
* Utwórz grupę komputerów
* Włącz zbieranie dzienników usług IIS z komputerów z zainstalowanym agentem systemu Windows
* Zbieranie liczników wydajności z komputerów z systemem Linux i Windows
* Zbieranie zdarzeń z dziennika systemowego na komputerach z systemem Linux
* Zbierz zdarzenia z dzienników zdarzeń systemu Windows
* Zbierz dzienniki zdarzeń niestandardowych
* Dodawanie agenta usługi log Analytics do maszyny wirtualnej platformy Azure
* Konfigurowanie usługi log Analytics do indeksowania danych zbieranych za pomocą diagnostyki Azure

Ten artykuł zawiera dwa przykłady kodu, które ilustrują niektóre funkcje, które można wykonać przy użyciu programu PowerShell.  Aby uzyskać informacje o innych funkcjach, można zapoznać się z artykułem [poleceń cmdlet programu log Analytics PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/) .

> [!NOTE]
> Log Analytics była wcześniej wywołana Operational Insights, dlatego jest to nazwa używana w poleceniach cmdlet.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne
Te przykłady działają w wersji 1.0.0 lub nowszej modułu AZ. OperationalInsights.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Tworzenie i Konfigurowanie obszaru roboczego Log Analytics
Poniższy przykładowy skrypt ilustruje sposób wykonywania następujących czynności:

1. Tworzenie obszaru roboczego
2. Wyświetl listę dostępnych rozwiązań
3. Dodawanie rozwiązań do obszaru roboczego
4. Importuj zapisane wyszukiwania
5. Eksportuj zapisane wyszukiwania
6. Utwórz grupę komputerów
7. Włącz zbieranie dzienników usług IIS z komputerów z zainstalowanym agentem systemu Windows
8. Zbieranie liczników wydajności dysku logicznego z komputerów z systemem Linux (% użytych węzłów i; Wolne megabajty; Zajęte miejsce:% Transfery dysku/s; Odczyty dysku/s; Zapisy dysku/s)
9. Zbieranie zdarzeń dziennika systemu z komputerów z systemem Linux
10. Zbieraj zdarzenia błędów i ostrzeżeń z dziennika zdarzeń aplikacji z komputerów z systemem Windows
11. Zbieranie danych licznika wydajności dostępna pamięć (MB) z komputerów z systemem Windows
12. Zbieranie dziennika niestandardowego

```powershell

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique across all Azure subscriptions - Get-Random helps with this for the example code
$Location = "westeurope"

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Perf | where ObjectName == "LogicalDisk" and CounterName == "Current Disk Queue Length" and InstanceName == "C:"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1",
    "description": "Example custom log datasource",
    "inputs": [
        {
            "location": {
            "fileSystemLocations": {
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ],
                "linuxFileTypeLogPaths": [ "/var/logs" ]
                }
            },
        "recordDelimiter": {
            "regexDelimiter": {
                "pattern": "\\n",
                "matchIndex": 0,
                "matchIndexSpecified": true,
                "numberedGroup": null
                }
            }
        }
    ],
    "extractions": [
        {
            "extractionName": "TimeGenerated",
            "extractionType": "DateTime",
            "extractionProperties": {
                "dateTimeExtraction": {
                    "regex": null,
                    "joinStringRegex": null
                    }
                }
            }
        ]
    }
"@

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($solution in $Solutions) {
    Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
}

# List enabled solutions
(Get-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json

# Create Computer Group based on a query
New-AzOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Enable IIS Log Collection using agent
Enable-AzOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzOperationalInsightsLinuxPerformanceCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernel syslog collection"
Enable-AzOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs

New-AzOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```

> [!NOTE]
> Format parametru **CustomLogRawJson** , który definiuje konfigurację dla dziennika niestandardowego, może być skomplikowany. Użyj [Get-AzOperationalInsightsDataSource](https://docs.microsoft.com/powershell/module/az.operationalinsights/get-azoperationalinsightsdatasource?view=azps-3.2.0) , aby pobrać konfigurację istniejącego dziennika niestandardowego. Właściwość **Właściwości** jest konfiguracją wymaganą dla parametru **CustomLogRawJson** .

W powyższym przykładzie regexDelimiter został zdefiniowany jako\\"n" dla nowego wiersza. Ogranicznik dziennika może być również sygnaturą czasową.  Są to obsługiwane formaty:

| Format | Format wyrażenia regularnego JSON \\ używa dwóch dla każdego elementu \ w standardowym wyrażeniach regularnych, dlatego jeśli \\ testowanie w aplikacji wyrażenia regularnego zmniejszy do \ | | |
| --- | --- | --- | --- |
| `YYYY-MM-DD HH:MM:SS` | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |
| `M/D/YYYY HH:MM:SS AM/PM` | `(([0-1]\\d)|[0-9])/(([0-3]\\d)|(\\d))/((\\d{2})|(\\d{4}))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]\\s(AM|PM|am|pm)` | | |
| `dd/MMM/yyyy HH:MM:SS` | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|May|Apr|Jul|Jun|Aug|Oct|Sep|Nov|Dec|jan|feb|mar|may|apr|jul|jun|aug|oct|sep|nov|dec)\\/((19|20)[0-9][0-9]))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9])` |
| `MMM dd yyyy HH:MM:SS` | `(((?:Jan(?:uary)?|Feb(?:ruary)?|Mar(?:ch)?|Apr(?:il)?|May|Jun(?:e)?|Jul(?:y)?|Aug(?:ust)?|Sep(?:tember)?|Sept|Oct(?:ober)?|Nov(?:ember)?|Dec(?:ember)?)).*?((?:(?:[0-2]?\\d{1})|(?:[3][01]{1})))(?![\\d]).*?((?:(?:[1]{1}\\d{1}\\d{1}\\d{1})|(?:[2]{1}\\d{3})))(?![\\d]).*?((?:(?:[0-1][0-9])|(?:[2][0-3])|(?:[0-9])):(?:[0-5][0-9])(?::[0-5][0-9])?(?:\\s?(?:am|AM|pm|PM))?))` | | |
| `yyMMdd HH:mm:ss` | `([0-9]{2}([0][1-9]|[1][0-2])([0-2][0-9]|[3][0-1])\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` | | |
| `ddMMyy HH:mm:ss` | `(([0-2][0-9]|[3][0-1])([0][1-9]|[1][0-2])[0-9]{2}\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` | | |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s?([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0-1]?[0-9]|[2][0-3]):([0-5][0-9]):([0-5][0-9])` | | |
| `MMM  d HH:mm:ss` <br> dwie spacje po MMM | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` | | |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` | | |
| `dd/MMM/yyyy:HH:mm:ss +zzzz` <br> gdzie + is + lub a- <br> gdzie zzzz przesunięcie czasu | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\/((19|20)[0-9][0-9]):([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])\\s[\\+|\\-][0-9]{4})` | | |
| `yyyy-MM-ddTHH:mm:ss` <br> T to litera litera T | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))T((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` | | |

## <a name="configuring-log-analytics-to-send-azure-diagnostics"></a>Konfigurowanie Log Analytics do wysyłania diagnostyki Azure
W przypadku monitorowania zasobów platformy Azure bez agentów zasoby muszą mieć włączoną diagnostykę Azure i skonfigurować do zapisu w obszarze roboczym Log Analytics. Takie podejście wysyła dane bezpośrednio do obszaru roboczego i nie wymaga zapisywania danych na koncie magazynu. Obsługiwane są następujące zasoby:

| Typ zasobu | Dzienniki | Metryki |
| --- | --- | --- |
| Bramy aplikacji    | Tak | Tak |
| Konta usługi Automation     | Tak | |
| Konta usługi Batch          | Tak | Tak |
| Analiza Data Lake     | Tak | |
| Magazyn Data Lake         | Tak | |
| Elastyczna Pula SQL        |     | Tak |
| Przestrzeń nazw centrum zdarzeń     |     | Tak |
| Centra IoT Hub                |     | Tak |
| Usługa Key Vault               | Tak | |
| Moduły równoważenia obciążenia          | Tak | |
| Logic Apps              | Tak | Tak |
| Grupy zabezpieczeń sieci | Tak | |
| Azure Cache for Redis             |     | Tak |
| Wyszukaj usługi         | Tak | Tak |
| Service Bus przestrzeń nazw   |     | Tak |
| SQL (V12)               |     | Tak |
| Witryny sieci Web               |     | Tak |
| Farmy serwerów sieci Web        |     | Tak |

Aby uzyskać szczegółowe informacje o dostępnych metrykach, zapoznaj się z tematem [obsługiwane metryki z Azure monitor](../../azure-monitor/platform/metrics-supported.md).

Szczegółowe informacje o dostępnych dziennikach znajdują się w tematach [obsługiwane usługi i schematy dzienników zasobów](../../azure-monitor/platform/diagnostic-logs-schema.md).

```powershell
$workspaceId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO"

Set-AzDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

Możesz również użyć powyższego polecenia cmdlet, aby zebrać dzienniki z zasobów, które znajdują się w różnych subskrypcjach. Polecenie cmdlet może współdziałać między subskrypcjami od momentu, gdy podajesz identyfikator zarówno zasobów tworzących dzienniki, jak i obszar roboczy, do którego są wysyłane dzienniki.


## <a name="configuring-log-analytics-workspace-to-collect-azure-diagnostics-from-storage"></a>Konfigurowanie obszaru roboczego Log Analytics w celu zbierania danych diagnostycznych platformy Azure z magazynu
Aby zbierać dane dziennika z uruchomionego wystąpienia klasycznej usługi w chmurze lub klastra usługi Service Fabric, należy najpierw zapisać dane w usłudze Azure Storage. Następnie zostanie skonfigurowany obszar roboczy Log Analytics do zbierania dzienników z konta magazynu. Obsługiwane są następujące zasoby:

* Klasyczne usługi Cloud Services (role sieć Web i proces roboczy)
* Klastry usługi Service Fabric

Poniższy przykład pokazuje, jak:

1. Wyświetl listę istniejących kont magazynu i lokalizacji, z których obszar roboczy będzie indeksować dane
2. Tworzenie konfiguracji do odczytu z konta magazynu
3. Zaktualizuj nowo utworzoną konfigurację, aby zindeksować dane z dodatkowych lokalizacji
4. Usuń nowo utworzoną konfigurację

```powershell
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable"
$workspace = (Get-AzOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want the workspace to index
$storageId = "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles")

# Remove the insight
Remove-AzOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight"

```

Możesz również użyć powyższego skryptu do zbierania dzienników z kont magazynu w różnych subskrypcjach. Skrypt może współdziałać między subskrypcjami od momentu, gdy podajesz identyfikator zasobu konta magazynu i odpowiedni klucz dostępu. Gdy zmienisz klucz dostępu, musisz zaktualizować szczegółowe dane usługi Storage, aby uzyskać nowy klucz.


## <a name="next-steps"></a>Następne kroki
* Zapoznaj się z [log Analytics poleceniami cmdlet programu PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/) , aby uzyskać dodatkowe informacje na temat konfigurowania log Analytics przy użyciu programu PowerShell.

