---
title: Odczytywanie dzienników przepływu sieciowej grupy zabezpieczeń | Microsoft Docs
description: Dowiedz się, jak używać Azure PowerShell do analizowania dzienników przepływu sieciowych grup zabezpieczeń, które są tworzone co godzinę i aktualizowane co kilka minut na platformie Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: damendo
ms.openlocfilehash: 18023f5a5b36dd971080d7a62fdd44698a205bb4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88272539"
---
# <a name="read-nsg-flow-logs"></a>Odczytywanie dzienników przepływu sieciowych grup zabezpieczeń

Informacje dotyczące odczytywania wpisów dzienników przepływu sieciowej grupy zabezpieczeń przy użyciu programu PowerShell.

Dzienniki przepływu sieciowej grupy zabezpieczeń są przechowywane na koncie magazynu w [blokowych](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)obiektach Blob. Blokowe obiekty blob składają się z mniejszych bloków. Każdy dziennik jest osobnym blokowym obiektem BLOB, który jest generowany co godzinę. Nowe dzienniki są generowane co godzinę. dzienniki są aktualizowane co kilka minut przy użyciu najnowszych danych. W tym artykule dowiesz się, jak czytać fragmenty dzienników przepływów.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario"></a>Scenariusz

W poniższym scenariuszu przedstawiono przykładowy dziennik przepływu, który jest przechowywany na koncie magazynu. Dowiesz się, jak wybiórczo odczytywać najnowsze zdarzenia w dziennikach przepływu sieciowej grupy zabezpieczeń. W tym artykule użyto programu PowerShell, jednak koncepcje omówione w artykule nie są ograniczone do języka programowania i są stosowane do wszystkich języków obsługiwanych przez interfejsy API usługi Azure Storage.

## <a name="setup"></a>Konfigurowanie

Przed rozpoczęciem należy włączyć rejestrowanie przepływu sieciowych grup zabezpieczeń dla jednej lub wielu sieciowych grup zabezpieczeń na Twoim koncie. Instrukcje dotyczące włączania dzienników przepływu zabezpieczeń sieci można znaleźć w następującym artykule: [wprowadzenie do rejestrowania przepływu dla sieciowych grup zabezpieczeń](network-watcher-nsg-flow-logging-overview.md).

## <a name="retrieve-the-block-list"></a>Pobierz listę zablokowanych

Poniższe środowisko programu PowerShell konfiguruje zmienne, które są konieczne do wykonywania zapytań dotyczących obiektu BLOB dziennika przepływu sieciowej grupy zabezpieczeń i wyświetlania bloków w blokowym obiekcie blob [CloudBlockBlob](https://docs.microsoft.com/dotnet/api/microsoft.azure.storage.blob.cloudblockblob) . Zaktualizuj skrypt tak, aby zawierał prawidłowe wartości dla danego środowiska.

```powershell
function Get-NSGFlowLogCloudBlockBlob {
    [CmdletBinding()]
    param (
        [string] [Parameter(Mandatory=$true)] $subscriptionId,
        [string] [Parameter(Mandatory=$true)] $NSGResourceGroupName,
        [string] [Parameter(Mandatory=$true)] $NSGName,
        [string] [Parameter(Mandatory=$true)] $storageAccountName,
        [string] [Parameter(Mandatory=$true)] $storageAccountResourceGroup,
        [string] [Parameter(Mandatory=$true)] $macAddress,
        [datetime] [Parameter(Mandatory=$true)] $logTime
    )

    process {
        # Retrieve the primary storage account key to access the NSG logs
        $StorageAccountKey = (Get-AzStorageAccountKey -ResourceGroupName $storageAccountResourceGroup -Name $storageAccountName).Value[0]

        # Setup a new storage context to be used to query the logs
        $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey

        # Container name used by NSG flow logs
        $ContainerName = "insights-logs-networksecuritygroupflowevent"

        # Name of the blob that contains the NSG flow log
        $BlobName = "resourceId=/SUBSCRIPTIONS/${subscriptionId}/RESOURCEGROUPS/${NSGResourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/${NSGName}/y=$($logTime.Year)/m=$(($logTime).ToString("MM"))/d=$(($logTime).ToString("dd"))/h=$(($logTime).ToString("HH"))/m=00/macAddress=$($macAddress)/PT1H.json"

        # Gets the storage blog
        $Blob = Get-AzStorageBlob -Context $ctx -Container $ContainerName -Blob $BlobName

        # Gets the block blog of type 'Microsoft.Azure.Storage.Blob.CloudBlob' from the storage blob
        $CloudBlockBlob = [Microsoft.Azure.Storage.Blob.CloudBlockBlob] $Blob.ICloudBlob

        #Return the Cloud Block Blob
        $CloudBlockBlob
    }
}

function Get-NSGFlowLogBlockList  {
    [CmdletBinding()]
    param (
        [Microsoft.Azure.Storage.Blob.CloudBlockBlob] [Parameter(Mandatory=$true)] $CloudBlockBlob
    )
    process {
        # Stores the block list in a variable from the block blob.
        $blockList = $CloudBlockBlob.DownloadBlockListAsync()

        # Return the Block List
        $blockList
    }
}


$CloudBlockBlob = Get-NSGFlowLogCloudBlockBlob -subscriptionId "yourSubscriptionId" -NSGResourceGroupName "FLOWLOGSVALIDATIONWESTCENTRALUS" -NSGName "V2VALIDATIONVM-NSG" -storageAccountName "yourStorageAccountName" -storageAccountResourceGroup "ml-rg" -macAddress "000D3AF87856" -logTime "11/11/2018 03:00" 

$blockList = Get-NSGFlowLogBlockList -CloudBlockBlob $CloudBlockBlob
```

`$blockList`Zmienna zwraca listę bloków w obiekcie blob. Każdy blokowy obiekt BLOB zawiera co najmniej dwa bloki.  Pierwszy blok ma długość `12` bajtów, ten blok zawiera nawiasy otwierające dziennika JSON. Drugi blok jest nawiasem zamykającym i ma długość `2` bajtów.  Jak widać w poniższym przykładzie dziennik zawiera siedem wpisów, każdy z nich jest pojedynczym wpisem. Wszystkie nowe wpisy w dzienniku są dodawane do końca bezpośrednio przed końcowym blokiem.

```
Name                                         Length Committed
----                                         ------ ---------
ZDk5MTk5N2FkNGE0MmY5MTk5ZWViYjA0YmZhODRhYzY=     12      True
NzQxNDA5MTRhNDUzMGI2M2Y1MDMyOWZlN2QwNDZiYzQ=   2685      True
ODdjM2UyMWY3NzFhZTU3MmVlMmU5MDNlOWEwNWE3YWY=   2586      True
ZDU2MjA3OGQ2ZDU3MjczMWQ4MTRmYWNhYjAzOGJkMTg=   2688      True
ZmM3ZWJjMGQ0ZDA1ODJlOWMyODhlOWE3MDI1MGJhMTc=   2775      True
ZGVkYTc4MzQzNjEyMzlmZWE5MmRiNjc1OWE5OTc0OTQ=   2676      True
ZmY2MjUzYTIwYWIyOGU1OTA2ZDY1OWYzNmY2NmU4ZTY=   2777      True
Mzk1YzQwM2U0ZWY1ZDRhOWFlMTNhYjQ3OGVhYmUzNjk=   2675      True
ZjAyZTliYWE3OTI1YWZmYjFmMWI0MjJhNzMxZTI4MDM=      2      True
```

## <a name="read-the-block-blob"></a>Odczytaj blokowy obiekt BLOB

Następnie musisz odczytać `$blocklist` zmienną, aby pobrać dane. W tym przykładzie wykonujemy iterację przez listy blokowania, odczytuje bajty z każdego bloku i umieszcza je w tablicy. Użyj metody [DownloadRangeToByteArray](/dotnet/api/microsoft.azure.storage.blob.cloudblob.downloadrangetobytearray) , aby pobrać dane.

```powershell
function Get-NSGFlowLogReadBlock  {
    [CmdletBinding()]
    param (
        [System.Array] [Parameter(Mandatory=$true)] $blockList,
        [Microsoft.Azure.Storage.Blob.CloudBlockBlob] [Parameter(Mandatory=$true)] $CloudBlockBlob

    )
    # Set the size of the byte array to the largest block
    $maxvalue = ($blocklist | measure Length -Maximum).Maximum

    # Create an array to store values in
    $valuearray = @()

    # Define the starting index to track the current block being read
    $index = 0

    # Loop through each block in the block list
    for($i=0; $i -lt $blocklist.count; $i++)
    {
        # Create a byte array object to story the bytes from the block
        $downloadArray = New-Object -TypeName byte[] -ArgumentList $maxvalue

        # Download the data into the ByteArray, starting with the current index, for the number of bytes in the current block. Index is increased by 3 when reading to remove preceding comma.
        $CloudBlockBlob.DownloadRangeToByteArray($downloadArray,0,$index, $($blockList[$i].Length)) | Out-Null

        # Increment the index by adding the current block length to the previous index
        $index = $index + $blockList[$i].Length

        # Retrieve the string from the byte array

        $value = [System.Text.Encoding]::ASCII.GetString($downloadArray)

        # Add the log entry to the value array
        $valuearray += $value
    }
    #Return the Array
    $valuearray
}
$valuearray = Get-NSGFlowLogReadBlock -blockList $blockList -CloudBlockBlob $CloudBlockBlob
```

Teraz `$valuearray` Tablica zawiera wartość ciągu dla każdego bloku. Aby sprawdzić wpis, Pobierz sekundę do ostatniej wartości z tablicy, uruchamiając polecenie `$valuearray[$valuearray.Length-2]` . Ostatnia wartość nie jest potrzebna, ponieważ jest to nawias zamykający.

Wyniki tej wartości są pokazane w następującym przykładzie:

```json
        {
             "time": "2017-06-16T20:59:43.7340000Z",
             "systemId": "5f4d02d3-a7d0-4ed4-9ce8-c0ae9377951c",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/CONTOSORG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/CONTOSONSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_AllowInternetOutBound","flows":[{"mac":"000D3A18077E","flowTuples":["1497646722,10.0.0.4,168.62.32.14,44904,443,T,O,A","1497646722,10.0.0.4,52.240.48.24,45218,443,T,O,A","1497646725,10.
0.0.4,168.62.32.14,44910,443,T,O,A","1497646725,10.0.0.4,52.240.48.24,45224,443,T,O,A","1497646728,10.0.0.4,168.62.32.14,44916,443,T,O,A","1497646728,10.0.0.4,52.240.48.24,45230,443,T,O,A","1497646732,10.0.0.4,168.62.32.14,44922,443,T,O,A","14976
46732,10.0.0.4,52.240.48.24,45236,443,T,O,A","1497646735,10.0.0.4,168.62.32.14,44928,443,T,O,A","1497646735,10.0.0.4,52.240.48.24,45242,443,T,O,A","1497646738,10.0.0.4,168.62.32.14,44934,443,T,O,A","1497646738,10.0.0.4,52.240.48.24,45248,443,T,O,
A","1497646742,10.0.0.4,168.62.32.14,44942,443,T,O,A","1497646742,10.0.0.4,52.240.48.24,45256,443,T,O,A","1497646745,10.0.0.4,168.62.32.14,44948,443,T,O,A","1497646745,10.0.0.4,52.240.48.24,45262,443,T,O,A","1497646749,10.0.0.4,168.62.32.14,44954
,443,T,O,A","1497646749,10.0.0.4,52.240.48.24,45268,443,T,O,A","1497646753,10.0.0.4,168.62.32.14,44960,443,T,O,A","1497646753,10.0.0.4,52.240.48.24,45274,443,T,O,A","1497646756,10.0.0.4,168.62.32.14,44966,443,T,O,A","1497646756,10.0.0.4,52.240.48
.24,45280,443,T,O,A","1497646759,10.0.0.4,168.62.32.14,44972,443,T,O,A","1497646759,10.0.0.4,52.240.48.24,45286,443,T,O,A","1497646763,10.0.0.4,168.62.32.14,44978,443,T,O,A","1497646763,10.0.0.4,52.240.48.24,45292,443,T,O,A","1497646766,10.0.0.4,
168.62.32.14,44984,443,T,O,A","1497646766,10.0.0.4,52.240.48.24,45298,443,T,O,A","1497646769,10.0.0.4,168.62.32.14,44990,443,T,O,A","1497646769,10.0.0.4,52.240.48.24,45304,443,T,O,A","1497646773,10.0.0.4,168.62.32.14,44996,443,T,O,A","1497646773,
10.0.0.4,52.240.48.24,45310,443,T,O,A","1497646776,10.0.0.4,168.62.32.14,45002,443,T,O,A","1497646776,10.0.0.4,52.240.48.24,45316,443,T,O,A","1497646779,10.0.0.4,168.62.32.14,45008,443,T,O,A","1497646779,10.0.0.4,52.240.48.24,45322,443,T,O,A"]}]}
,{"rule":"DefaultRule_DenyAllInBound","flows":[]},{"rule":"UserRule_ssh-rule","flows":[]},{"rule":"UserRule_web-rule","flows":[{"mac":"000D3A18077E","flowTuples":["1497646738,13.82.225.93,10.0.0.4,1180,80,T,I,A","1497646750,13.82.225.93,10.0.0.4,
1184,80,T,I,A","1497646768,13.82.225.93,10.0.0.4,1181,80,T,I,A","1497646780,13.82.225.93,10.0.0.4,1336,80,T,I,A"]}]}]}
        }
```

W tym scenariuszu przedstawiono przykład sposobu odczytywania wpisów w dziennikach przepływu sieciowej grupy zabezpieczeń bez konieczności analizowania całego dziennika. Można odczytywać nowe wpisy w dzienniku w miarę ich zapisywania przy użyciu identyfikatora bloku lub śledząc długość bloków przechowywanych w blokowym obiekcie blob. Dzięki temu można odczytać tylko nowe wpisy.

## <a name="next-steps"></a>Następne kroki


Odwiedź stronę [Korzystanie z elastycznego stosu](network-watcher-visualize-nsg-flow-logs-open-source-tools.md), [Użyj Grafana](network-watcher-nsg-grafana.md)i [Skorzystaj z z narzędzia graylog](network-watcher-analyze-nsg-flow-logs-graylog.md) , aby dowiedzieć się więcej na temat sposobów wyświetlania dzienników przepływu sieciowej grupy zabezpieczeń. Podejście funkcji platformy Azure typu open source do konsumowania obiektów BLOB bezpośrednio i emitowania do różnych odbiorców usługi log Analytics można znaleźć tutaj: [Łącznik usługi Azure Network Watcher sieciowej grupy zabezpieczeń Flow Logs](https://github.com/Microsoft/AzureNetworkWatcherNSGFlowLogsConnector).

Możesz użyć [usługi Azure Analiza ruchu](https://docs.microsoft.com/azure/network-watcher/traffic-analytics) , aby uzyskać wgląd w przepływy ruchu. Analiza ruchu używa [log Analytics](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) , aby przepływ ruchu queryable.

Aby dowiedzieć się więcej na temat obiektów blob magazynu, odwiedź: [Azure Functions powiązania magazynu obiektów BLOB](../azure-functions/functions-bindings-storage-blob.md)
