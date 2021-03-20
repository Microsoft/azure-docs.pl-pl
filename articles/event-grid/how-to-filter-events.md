---
title: Jak filtrować zdarzenia dla Azure Event Grid
description: W tym artykule przedstawiono sposób filtrowania zdarzeń (według typu zdarzenia, według operatorów i danych itp.) podczas tworzenia subskrypcji Event Grid.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 99fb00f99a055033ccfcd99e32a52d423878fb44
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86105578"
---
# <a name="filter-events-for-event-grid"></a>Filtruj zdarzenia dla Event Grid

W tym artykule przedstawiono sposób filtrowania zdarzeń podczas tworzenia subskrypcji Event Grid. Aby dowiedzieć się więcej na temat opcji filtrowania zdarzeń, zobacz [Omówienie filtrowania zdarzeń dla subskrypcji Event Grid](event-filtering.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="filter-by-event-type"></a>Filtruj według typu zdarzenia

Podczas tworzenia subskrypcji Event Grid można określić [typy zdarzeń](event-schema.md) do wysłania do punktu końcowego. Przykłady w tej sekcji tworzą subskrypcje zdarzeń dla grupy zasobów, ale ograniczają zdarzenia, które są wysyłane do `Microsoft.Resources.ResourceWriteFailure` i `Microsoft.Resources.ResourceWriteSuccess` . Jeśli potrzebujesz większej elastyczności podczas filtrowania zdarzeń według typów zdarzeń, zobacz filtrowanie według zaawansowanych operatorów i pól danych.

W przypadku programu PowerShell Użyj `-IncludedEventType` parametru podczas tworzenia subskrypcji.

```powershell
$includedEventTypes = "Microsoft.Resources.ResourceWriteFailure", "Microsoft.Resources.ResourceWriteSuccess"

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -Endpoint <endpoint-URL> `
  -IncludedEventType $includedEventTypes
```

W przypadku interfejsu wiersza polecenia platformy Azure Użyj `--included-event-types` parametru. W poniższym przykładzie pokazano użycie interfejsu wiersza polecenia platformy Azure w bash Shell:

```azurecli
includedEventTypes="Microsoft.Resources.ResourceWriteFailure Microsoft.Resources.ResourceWriteSuccess"

az eventgrid event-subscription create \
  --name demoSubToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --included-event-types $includedEventTypes
```

Dla szablonu Menedżer zasobów Użyj `includedEventTypes` właściwości.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [
          "Microsoft.Resources.ResourceWriteFailure",
          "Microsoft.Resources.ResourceWriteSuccess"
        ]
      }
    }
  }
]
```

## <a name="filter-by-subject"></a>Filtruj według tematu

Zdarzenia można filtrować według tematu w danych zdarzenia. Możesz określić wartość do dopasowania na początku lub na końcu tematu. Jeśli potrzebujesz większej elastyczności podczas filtrowania zdarzeń według tematu, zobacz filtrowanie według zaawansowanych operatorów i pól danych.

W poniższym przykładzie programu PowerShell utworzysz subskrypcję zdarzeń, która jest filtrowana według początku tematu. Należy użyć `-SubjectBeginsWith` parametru, aby ograniczyć zdarzenia do określonych zasobów. Należy przekazać identyfikator zasobu sieciowej grupy zabezpieczeń.

```powershell
$resourceId = (Get-AzResource -ResourceName demoSecurityGroup -ResourceGroupName myResourceGroup).ResourceId

New-AzEventGridSubscription `
  -Endpoint <endpoint-URL> `
  -EventSubscriptionName demoSubscriptionToResourceGroup `
  -ResourceGroupName myResourceGroup `
  -SubjectBeginsWith $resourceId
```

Następny przykład programu PowerShell tworzy subskrypcję magazynu obiektów BLOB. Ogranicza zdarzenia do tych elementów za pomocą podmiotu kończącego się na `.jpg` .

```powershell
$storageId = (Get-AzStorageAccount -ResourceGroupName myResourceGroup -AccountName $storageName).Id

New-AzEventGridSubscription `
  -EventSubscriptionName demoSubToStorage `
  -Endpoint <endpoint-URL> `
  -ResourceId $storageId `
  -SubjectEndsWith ".jpg"
```

W poniższym przykładzie interfejsu wiersza polecenia platformy Azure utworzysz subskrypcję zdarzeń, która jest filtrowana według początku tematu. Należy użyć `--subject-begins-with` parametru, aby ograniczyć zdarzenia do określonych zasobów. Należy przekazać identyfikator zasobu sieciowej grupy zabezpieczeń.

```azurecli
resourceId=$(az resource show --name demoSecurityGroup --resource-group myResourceGroup --resource-type Microsoft.Network/networkSecurityGroups --query id --output tsv)

az eventgrid event-subscription create \
  --name demoSubscriptionToResourceGroup \
  --resource-group myResourceGroup \
  --endpoint <endpoint-URL> \
  --subject-begins-with $resourceId
```

Następny przykład interfejsu wiersza polecenia platformy Azure tworzy subskrypcję magazynu obiektów BLOB. Ogranicza zdarzenia do tych elementów za pomocą podmiotu kończącego się na `.jpg` .

```azurecli
storageid=$(az storage account show --name $storageName --resource-group myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --resource-id $storageid \
  --name demoSubToStorage \
  --endpoint <endpoint-URL> \
  --subject-ends-with ".jpg"
```

W poniższym przykładzie szablonu Menedżer zasobów utworzysz subskrypcję zdarzeń, która jest filtrowana według początku tematu. `subjectBeginsWith`Właściwość służy do ograniczania liczby zdarzeń do określonych zasobów. Należy przekazać identyfikator zasobu sieciowej grupy zabezpieczeń.

```json
"resources": [
  {
    "type": "Microsoft.EventGrid/eventSubscriptions",
    "name": "[parameters('eventSubName')]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectBeginsWith": "[resourceId('Microsoft.Network/networkSecurityGroups','demoSecurityGroup')]",
        "subjectEndsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

Następny przykład szablonu Menedżer zasobów tworzy subskrypcję magazynu obiektów BLOB. Ogranicza zdarzenia do tych elementów za pomocą podmiotu kończącego się na `.jpg` .

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts/providers/eventSubscriptions",
    "name": "[concat(parameters('storageName'), '/Microsoft.EventGrid/', parameters('eventSubName'))]",
    "apiVersion": "2018-09-15-preview",
    "properties": {
      "destination": {
        "endpointType": "WebHook",
        "properties": {
          "endpointUrl": "[parameters('endpoint')]"
        }
      },
      "filter": {
        "subjectEndsWith": ".jpg",
        "subjectBeginsWith": "",
        "isSubjectCaseSensitive": false,
        "includedEventTypes": [ "All" ]
      }
    }
  }
]
```

## <a name="filter-by-operators-and-data"></a>Filtrowanie według operatorów i danych

Aby zapewnić większą elastyczność filtrowania, można użyć operatorów i właściwości danych do filtrowania zdarzeń.

### <a name="subscribe-with-advanced-filters"></a>Subskrybowanie filtrów zaawansowanych

Aby dowiedzieć się więcej na temat operatorów i kluczy, których można użyć do filtrowania zaawansowanego, zobacz [filtrowanie zaawansowane](event-filtering.md#advanced-filtering).

W tych przykładach opisano tworzenie tematu niestandardowego. Subskrybują temat niestandardowy i filtrują według wartości w obiekcie danych. Zdarzenia, które mają Właściwość Color ustawioną na niebieską, czerwoną lub zieloną, są wysyłane do subskrypcji.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli
topicName=<your-topic-name>
endpointURL=<endpoint-URL>

az group create -n gridResourceGroup -l eastus2
az eventgrid topic create --name $topicName -l eastus2 -g gridResourceGroup

topicid=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  -n demoAdvancedSub \
  --advanced-filter data.color stringin blue red green \
  --endpoint $endpointURL \
  --expiration-date "<yyyy-mm-dd>"
```

Należy zauważyć, że ustawiono [datę wygaśnięcia](concepts.md#event-subscription-expiration) subskrypcji.

W przypadku programu PowerShell użyj polecenia:

```powershell
$topicName = <your-topic-name>
$endpointURL = <endpoint-URL>

New-AzResourceGroup -Name gridResourceGroup -Location eastus2
New-AzEventGridTopic -ResourceGroupName gridResourceGroup -Location eastus2 -Name $topicName

$topicid = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Id

$expDate = '<mm/dd/yyyy hh:mm:ss>' | Get-Date
$AdvFilter1=@{operator="StringIn"; key="Data.color"; Values=@('blue', 'red', 'green')}

New-AzEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName <event_subscription_name> `
  -Endpoint $endpointURL `
  -ExpirationDate $expDate `
  -AdvancedFilter @($AdvFilter1)
```

### <a name="test-filter"></a>Filtr testu

Aby przetestować filtr, Wyślij zdarzenie z polem koloru ustawionym na zielony. Ponieważ zielona jest jedną z wartości w filtrze, zdarzenie jest dostarczane do punktu końcowego.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli
topicEndpoint=$(az eventgrid topic show --name $topicName -g gridResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name $topicName -g gridResourceGroup --query "key1" --output tsv)

event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "green"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```

W przypadku programu PowerShell użyj polecenia:

```powershell
$endpoint = (Get-AzEventGridTopic -ResourceGroupName gridResourceGroup -Name $topicName).Endpoint
$keys = Get-AzEventGridTopicKey -ResourceGroupName gridResourceGroup -Name $topicName

$eventID = Get-Random 99999
$eventDate = Get-Date -Format s

$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="green"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

Aby przetestować scenariusz, w którym zdarzenie nie jest wysyłane, Wyślij zdarzenie z polem koloru ustawionym na żółty. Żółty nie jest jedną z wartości określonych w subskrypcji, więc zdarzenie nie jest dostarczane do subskrypcji.

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli
event='[ {"id": "'"$RANDOM"'", "eventType": "recordInserted", "subject": "myapp/vehicles/cars", "eventTime": "'`date +%Y-%m-%dT%H:%M:%S%z`'", "data":{ "model": "SUV", "color": "yellow"},"dataVersion": "1.0"} ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $topicEndpoint
```
W przypadku programu PowerShell użyj polecenia:

```powershell
$htbody = @{
    id= $eventID
    eventType="recordInserted"
    subject="myapp/vehicles/cars"
    eventTime= $eventDate
    data= @{
        model="SUV"
        color="yellow"
    }
    dataVersion="1.0"
}

$body = "["+(ConvertTo-Json $htbody)+"]"

Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje na temat monitorowania dostarczania zdarzeń, zobacz [monitorowanie Event Grid dostarczania komunikatów](monitor-event-delivery.md).
* Aby uzyskać więcej informacji na temat klucza uwierzytelniania, zobacz [Event Grid zabezpieczenia i uwierzytelnianie](security-authentication.md).
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji Azure Event Grid, zobacz [Event Grid schematu subskrypcji](subscription-creation-schema.md).
