---
title: Tworzenie & konfigurowania Log Analytics przy użyciu programu PowerShell
description: Log Analytics obszary robocze w Azure Monitor przechowywanie danych z serwerów w infrastrukturze lokalnej lub w chmurze. Dane dotyczące maszyn można zbierać z usługi Azure Storage, gdy są generowane przez diagnostykę platformy Azure.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/26/2020
ms.openlocfilehash: a1abe79b4ab1caad04a088f659c0afceb2668eac
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106925"
---
# <a name="create-and-configure-a-log-analytics-workspace-in-azure-monitor-using-powershell"></a>Tworzenie i Konfigurowanie obszaru roboczego Log Analytics w Azure Monitor przy użyciu programu PowerShell
W tym artykule przedstawiono dwa przykłady kodu, które pokazują, jak utworzyć i skonfigurować obszar roboczy Log Analytics w Azure Monitor.  


> [!NOTE]
> Log Analytics była wcześniej wywołana Operational Insights, dlatego jest to nazwa używana w poleceniach cmdlet.


## <a name="prerequisites"></a>Wymagania wstępne
Te przykłady działają w wersji 1.0.0 lub nowszej modułu AZ. OperationalInsights.

## <a name="create-workspace"></a>Tworzenie obszaru roboczego
Poniższy przykładowy skrypt tworzy obszar roboczy bez konfiguracji źródła danych. 

```powershell
$ResourceGroup = "my-resource-group"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique in resource group - Get-Random helps with this for the example code
$Location = "westeurope"

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
```

## <a name="create-workspace-and-configure-data-sources"></a>Utwórz obszar roboczy i skonfiguruj źródła danych

Poniższy przykładowy przykładowy skrypt tworzy obszar roboczy i konfiguruje wiele źródeł danych. Te źródła danych są wymagane tylko w przypadku monitorowania maszyn wirtualnych przy użyciu [agenta log Analytics](../agents/log-analytics-agent.md).

Ten skrypt wykonuje następujące funkcje:

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
$ResourceGroup = "my-resource-group"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique in resource group - Get-Random helps with this for the example code
$Location = "westeurope"

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

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
> Format parametru **CustomLogRawJson** , który definiuje konfigurację dla dziennika niestandardowego, może być skomplikowany. Użyj [Get-AzOperationalInsightsDataSource](/powershell/module/az.operationalinsights/get-azoperationalinsightsdatasource) , aby pobrać konfigurację istniejącego dziennika niestandardowego. Właściwość **Właściwości** jest konfiguracją wymaganą dla parametru **CustomLogRawJson** .

W powyższym przykładzie regexDelimiter został zdefiniowany jako " \\ n" dla nowego wiersza. Ogranicznik dziennika może być również sygnaturą czasową.  Są to obsługiwane formaty:

| Format | Format wyrażenia regularnego JSON używa dwóch `\\` dla każdego `\` w standardowym wyrażeniach regularnych, dlatego jeśli testowanie w aplikacji wyrażenia regularnego zmniejszy `\\` się do `\` |
| --- | --- |
| `YYYY-MM-DD HH:MM:SS` | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` |
| `M/D/YYYY HH:MM:SS AM/PM` | `(([0-1]\\d)|[0-9])/(([0-3]\\d)|(\\d))/((\\d{2})|(\\d{4}))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]\\s(AM|PM|am|pm)` |
| `dd/MMM/yyyy HH:MM:SS` | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|May|Apr|Jul|Jun|Aug|Oct|Sep|Nov|Dec|jan|feb|mar|may|apr|jul|jun|aug|oct|sep|nov|dec)\\/((19|20)[0-9][0-9]))\\s((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9])` |
| `MMM dd yyyy HH:MM:SS` | `(((?:Jan(?:uary)?|Feb(?:ruary)?|Mar(?:ch)?|Apr(?:il)?|May|Jun(?:e)?|Jul(?:y)?|Aug(?:ust)?|Sep(?:tember)?|Sept|Oct(?:ober)?|Nov(?:ember)?|Dec(?:ember)?)).*?((?:(?:[0-2]?\\d{1})|(?:[3][01]{1})))(?![\\d]).*?((?:(?:[1]{1}\\d{1}\\d{1}\\d{1})|(?:[2]{1}\\d{3})))(?![\\d]).*?((?:(?:[0-1][0-9])|(?:[2][0-3])|(?:[0-9])):(?:[0-5][0-9])(?::[0-5][0-9])?(?:\\s?(?:am|AM|pm|PM))?))` |
| `yyMMdd HH:mm:ss` | `([0-9]{2}([0][1-9]|[1][0-2])([0-2][0-9]|[3][0-1])\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` |
| `ddMMyy HH:mm:ss` | `(([0-2][0-9]|[3][0-1])([0][1-9]|[1][0-2])[0-9]{2}\\s\\s?([0-1]?[0-9]|[2][0-3]):[0-5][0-9]:[0-5][0-9])` |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s?([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0-1]?[0-9]|[2][0-3]):([0-5][0-9]):([0-5][0-9])` |
| `MMM  d HH:mm:ss` <br> dwie spacje po MMM | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` |
| `MMM d HH:mm:ss` | `(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\s([0]?[1-9]|[1-2][0-9]|[3][0-1])\\s([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])` |
| `dd/MMM/yyyy:HH:mm:ss +zzzz` <br> gdzie + is + lub a- <br> gdzie zzzz przesunięcie czasu | `(([0-2][1-9]|[3][0-1])\\/(Jan|Feb|Mar|Apr|May|Jun|Jul|Aug|Sep|Oct|Nov|Dec)\\/((19|20)[0-9][0-9]):([0][0-9]|[1][0-2]):([0-5][0-9]):([0-5][0-9])\\s[\\+|\\-][0-9]{4})` |
| `yyyy-MM-ddTHH:mm:ss` <br> T to litera litera T | `((\\d{2})|(\\d{4}))-([0-1]\\d)-(([0-3]\\d)|(\\d))T((\\d)|([0-1]\\d)|(2[0-4])):[0-5][0-9]:[0-5][0-9]` |

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Podczas tworzenia obszaru roboczego, który został usunięty w ciągu ostatnich 14 dni i w [stanie usuwania nietrwałego](../logs/delete-workspace.md#soft-delete-behavior), operacja może mieć różny wynik w zależności od konfiguracji obszaru roboczego:
1. Jeśli podano tę samą nazwę obszaru roboczego, grupę zasobów, subskrypcję i region, jak w usuniętym obszarze roboczym, obszar roboczy zostanie odzyskany, w tym jego dane, konfiguracja i agenci połączone.
2. Nazwa obszaru roboczego musi być unikatowa dla każdej grupy zasobów. Jeśli używasz już istniejącej nazwy obszaru roboczego, również w przypadku usuwania nietrwałego w grupie zasobów, zostanie wyświetlony komunikat o błędzie *Nazwa obszaru roboczego "Przestrzeń nazw" nie jest unikatowa* lub powoduje *konflikt*. Aby zastąpić nietrwałe usuwanie i trwałe usuwanie obszaru roboczego i utworzyć nowy obszar roboczy o tej samej nazwie, wykonaj następujące kroki, aby najpierw odzyskać obszar roboczy i wykonać trwałe usuwanie:
   * [Odzyskiwanie](../logs/delete-workspace.md#recover-workspace) obszaru roboczego
   * [Trwałe usunięcie](../logs/delete-workspace.md#permanent-workspace-delete) obszaru roboczego
   * Utwórz nowy obszar roboczy przy użyciu tej samej nazwy obszaru roboczego


## <a name="next-steps"></a>Następne kroki
* Zapoznaj się z [log Analytics poleceniami cmdlet programu PowerShell](/powershell/module/az.operationalinsights/) , aby uzyskać dodatkowe informacje na temat konfigurowania log Analytics przy użyciu programu PowerShell.
