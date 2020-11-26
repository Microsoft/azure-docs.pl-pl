---
title: Wyeksportuj dane z Azure Monitor przy użyciu usługi Azure Eksplorator danych (wersja zapoznawcza)
description: Za pomocą usługi Azure Eksplorator danych można wykonywać zapytania dotyczące danych, które zostały wyeksportowane z obszaru roboczego Log Analytics do konta usługi Azure Storage.
ms.subservice: logs
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: 2735aec0f95f5e282bb1dffba6e4f42f966cf117
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96186545"
---
# <a name="query-exported-data-from-azure-monitor-using-azure-data-explorer-preview"></a>Wyeksportuj dane z Azure Monitor przy użyciu usługi Azure Eksplorator danych (wersja zapoznawcza)
Eksportowanie danych z Azure Monitor na konto usługi Azure Storage umożliwia przechowywanie w niskich kosztach i możliwość ponownego przydzielenia dzienników do różnych regionów. Użyj usługi Azure Eksplorator danych, aby wykonać zapytanie dotyczące danych, które zostały wyeksportowane z Log Analytics obszarów roboczych. Po skonfigurowaniu obsługiwane tabele, które są wysyłane z obszarów roboczych do konta usługi Azure Storage, będą dostępne jako źródło danych dla Eksplorator danych platformy Azure.

Przepływ procesu jest następujący: 

1.  Eksportuj dane z obszaru roboczego Log Analytics do konta usługi Azure Storage.
2.  Utwórz zewnętrzną tabelę w klastrze Eksplorator danych platformy Azure i Mapuj dla typów danych.
3.  Wykonywanie zapytań dotyczących danych z usługi Azure Eksplorator danych.

:::image type="content" source="media/azure-data-explorer-query-storage/exported-data-query.png" alt-text="Usługa Azure Eksplorator danych wyeksportował przepływ zapytań o dane.":::



## <a name="send-data-to-azure-storage"></a>Wysyłanie danych do usługi Azure Storage
Dzienniki Azure Monitor można eksportować do konta usługi Azure Storage przy użyciu dowolnej z poniższych opcji.

- Aby wyeksportować wszystkie dane z obszaru roboczego Log Analytics do konta usługi Azure Storage lub centrum zdarzeń, użyj funkcji eksportu danych obszaru roboczego Log Analytics dzienników Azure Monitor. Zobacz [log Analytics eksportu danych obszaru roboczego w Azure monitor (wersja zapoznawcza)](logs-data-export.md)
- Zaplanowano eksport z zapytania dziennika przy użyciu aplikacji logiki. Jest to podobne do funkcji eksportu danych, ale umożliwia wysyłanie filtrowanych lub zagregowanych danych do usługi Azure Storage. Ta metoda jest niezależna od [limitów zapytania log](../service-limits.md#log-analytics-workspaces)  zobacz [dane archiwalne z obszaru roboczego log Analytics do usługi Azure Storage przy użyciu aplikacji logiki](logs-export-logic-app.md).
- Eksport jednorazowy przy użyciu aplikacji logiki. Zobacz [Azure monitor Logs łącznik Logic Apps i automatyzacji](logicapp-flow-connector.md).
- Jednorazowe Eksportowanie do komputera lokalnego przy użyciu skryptu programu PowerShell. Zobacz [Invoke-AzOperationalInsightsQueryExport](https://www.powershellgallery.com/packages/Invoke-AzOperationalInsightsQueryExport).

> [!TIP]
> Możesz użyć istniejącego klastra usługi Azure Eksplorator danych lub utworzyć nowy dedykowany klaster z wymaganymi konfiguracjami.

## <a name="create-an-external-table-located-in-azure-blob-storage"></a>Tworzenie tabeli zewnętrznej znajdującej się w usłudze Azure Blob Storage
Użyj [tabel zewnętrznych](/azure/data-explorer/kusto/query/schema-entities/externaltables) , aby połączyć Eksplorator danych platformy Azure z kontem usługi Azure Storage. Tabela zewnętrzna jest jednostką schematu Kusto, która odwołuje się do danych przechowywanych poza bazą danych Kusto. Podobnie jak w przypadku tabel tabela zewnętrzna ma dobrze zdefiniowany schemat. W przeciwieństwie do tabel, dane są przechowywane i zarządzane poza klastrem Kusto. Wyeksportowane dane z poprzedniej sekcji są zapisywane w wierszach JSON.

Aby utworzyć odwołanie, wymagany jest schemat wyeksportowanej tabeli. Użyj operatora [GetSchema](/azure/data-explorer/kusto/query/getschemaoperator) z log Analytics, aby pobrać te informacje, które obejmują kolumny tabeli i ich typy danych.

:::image type="content" source="media\azure-data-explorer-query-storage\exported-data-map-schema.jpg" alt-text="Log Analytics schemat tabeli.":::

Teraz można użyć danych wyjściowych do utworzenia zapytania Kusto na potrzeby tworzenia tabeli zewnętrznej.
Postępując zgodnie ze wskazówkami w temacie [Tworzenie i modyfikowanie tabel zewnętrznych w usłudze Azure Storage lub Azure Data Lake](/azure/data-explorer/kusto/management/external-tables-azurestorage-azuredatalake), Utwórz tabelę zewnętrzną w formacie JSON, a następnie uruchom zapytanie z bazy danych Azure Eksplorator danych.

>[!NOTE]
>Tworzenie tabeli zewnętrznej zostało utworzone na podstawie dwóch procesów. Pierwszy tworzy tabelę zewnętrzną, podczas gdy drugi tworzy mapowanie.

Poniższy skrypt programu PowerShell utworzy polecenia [tworzenia](/azure/data-explorer/kusto/management/external-tables-azurestorage-azuredatalake#create-external-table-mapping) dla tabeli i mapowania.

```powershell
PARAM(
    $resourcegroupname, #The name of the Azure resource group
    $TableName, # The Log Analytics table you wish to convert to external table
    $MapName, # The name of the map
    $subscriptionId, #The ID of the subscription
    $WorkspaceId, # The Log Analytics WorkspaceId
    $WorkspaceName, # The Log Analytics workspace name
    $BlobURL, # The Blob URL where the data is saved
    $ContainerAccessKey, # The blob container Access Key (Option to add a SAS url)
    $ExternalTableName = $null # The External Table name, null to use the same name
)

if($null -eq $ExternalTableName) {
    $ExternalTableName = $TableName
}

$query = $TableName + ' | getschema | project ColumnName, DataType'

$output = (Invoke-AzOperationalInsightsQuery -WorkspaceId $WorkspaceId -Query $query).Results

$FirstCommand = @()
$SecondCommand = @()

foreach ($record in $output) {
    if ($record.DataType -eq 'System.DateTime') {
        $dataType = 'datetime'
    } else {
        $dataType = 'string'
    }
    $FirstCommand += $record.ColumnName + ":" + "$dataType" + ","
    $SecondCommand += "{`"column`":" + "`"" + $record.ColumnName + "`"," + "`"datatype`":`"$dataType`",`"path`":`"$." + $record.ColumnName + "`"},"
}
$schema = ($FirstCommand -join '') -replace ',$'
$mapping = ($SecondCommand -join '') -replace ',$'

$CreateExternal = @'
.create external table {0} ({1})
kind=blob
partition by (TimeGeneratedPartition:datetime = bin(TimeGenerated, 1min))
pathformat = (datetime_pattern("'y='yyyy'/m='MM'/d='dd'/h='HH'/m='mm", TimeGeneratedPartition))
dataformat=multijson
(
   h@'{2}/WorkspaceResourceId=/subscriptions/{4}/resourcegroups/{6}/providers/microsoft.operationalinsights/workspaces/{5};{3}'
)
with
(
   docstring = "Docs",
   folder = "ExternalTables"
)
'@ -f $TableName, $schema, $BlobURL, $ContainerAccessKey, $subscriptionId, $WorkspaceName, $resourcegroupname,$WorkspaceId

$createMapping = @'
.create external table {0} json mapping "{1}" '[{2}]'
'@ -f $ExternalTableName, $MapName, $mapping

Write-Host -ForegroundColor Red 'Copy and run the following commands (one by one), on your Azure Data Explorer cluster query window to create the external table and mappings:'
write-host -ForegroundColor Green $CreateExternal
Write-Host -ForegroundColor Green $createMapping
```

Na poniższej ilustracji przedstawiono i przykład danych wyjściowych.

:::image type="content" source="media/azure-data-explorer-query-storage/external-table-create-command-output.png" alt-text="Dane wyjściowe polecenia CREATE zewnętrznego.":::

[![Przykładowe dane wyjściowe](media/azure-data-explorer-query-storage/external-table-create-command-output.png)](media/azure-data-explorer-query-storage/external-table-create-command-output.png#lightbox)

>[!TIP]
>* Skopiuj, wklej, a następnie Uruchom dane wyjściowe skryptu w narzędziu klienckim Eksplorator danych platformy Azure, aby utworzyć tabelę i mapowanie.
>* Aby używać wszystkich danych wewnątrz kontenera, należy zmienić skrypt i zmienić adres URL na " https://your.blob.core.windows.net/containername ; SecKey'

## <a name="query-the-exported-data-from-azure-data-explorer"></a>Wykonaj zapytanie dotyczące wyeksportowanych danych z usługi Azure Eksplorator danych 

Po skonfigurowaniu mapowania można wykonywać zapytania dotyczące wyeksportowanych danych z usługi Azure Eksplorator danych. Zapytanie wymaga funkcji [external_table](/azure/data-explorer/kusto/query/externaltablefunction) , takiej jak w poniższym przykładzie.

```kusto
external_table("HBTest","map") | take 10000
```

[![Zapytanie Log Analytics eksportowane dane](media/azure-data-explorer-query-storage/external-table-query.png)](media/azure-data-explorer-query-storage/external-table-query.png#lightbox)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [pisać zapytania w usłudze Azure Eksplorator danych](/azure/data-explorer/write-queries)