---
title: Mapowanie pola niestandardowego na schemat Azure Event Grid
description: W tym artykule opisano sposób konwertowania niestandardowego schematu na schemat Azure Event Grid, gdy dane zdarzenia nie są zgodne ze schematem Event Grid.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 836e7b340c5c89100207e2f9409710b8dfa5e3bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86105527"
---
# <a name="map-custom-fields-to-event-grid-schema"></a>Mapowanie pól niestandardowych na schemat usługi Event Grid

Jeśli dane zdarzenia nie są zgodne z oczekiwanym [schematem Event Grid](event-schema.md), można nadal używać Event Grid do kierowania zdarzenia do subskrybentów. W tym artykule opisano sposób mapowania schematu do schematu Event Grid.

[!INCLUDE [requires-azurerm](../../includes/requires-azurerm.md)]

## <a name="install-preview-feature"></a>Instalowanie funkcji w wersji zapoznawczej

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]

## <a name="original-event-schema"></a>Oryginalny schemat zdarzeń

Załóżmy, że masz aplikację, która wysyła zdarzenia w następującym formacie:

```json
[
  {
    "myEventTypeField":"Created",
    "resource":"Users/example/Messages/1000",
    "resourceData":{"someDataField1":"SomeDataFieldValue"}
  }
]
```

Chociaż ten format nie jest zgodny z wymaganym schematem, Event Grid umożliwia mapowanie pól na schemat. Lub można odbierać wartości w oryginalnym schemacie.

## <a name="create-custom-topic-with-mapped-fields"></a>Tworzenie tematu niestandardowego z zamapowanymi polami

Podczas tworzenia tematu niestandardowego Określ sposób mapowania pól z oryginalnego zdarzenia do schematu usługi Event Grid. Istnieją trzy wartości, które służą do dostosowywania mapowania:

* Wartość **schematu wejściowego** określa typ schematu. Dostępne opcje to schemat CloudEvents, niestandardowy schemat zdarzeń lub schemat Event Grid. Wartość domyślna to Event Grid Schema. Podczas tworzenia mapowania niestandardowego między schematem i schematem siatki zdarzeń należy użyć niestandardowego schematu zdarzeń. Gdy zdarzenia znajdują się w schemacie CloudEvents, Użyj schematu Cloudevents.

* Właściwość **mapowania wartości domyślnych** określa wartości domyślne dla pól w schemacie Event Grid. Można ustawić wartości domyślne dla `subject` , `eventtype` , i `dataversion` . Zazwyczaj ten parametr jest używany, gdy niestandardowy schemat nie zawiera pola odpowiadającego jednemu z tych trzech pól. Można na przykład określić, że wersja danych ma zawsze wartość **1,0**.

* Wartość **pola mapowania** mapuje pola ze schematu do schematu usługi Event Grid. Należy określić wartości w parach klucz/wartość rozdzielaną spacją. Dla nazwy klucza Użyj nazwy pola usługi Event Grid. Dla wartości Użyj nazwy pola. Nazwy kluczy można użyć dla,,,, `id` `topic` `eventtime` `subject` `eventtype` , i `dataversion` .

Aby utworzyć niestandardowy temat za pomocą interfejsu wiersza polecenia platformy Azure, użyj polecenia:

```azurecli-interactive
# If you have not already installed the extension, do it now.
# This extension is required for preview features.
az extension add --name eventgrid

az eventgrid topic create \
  -n demotopic \
  -l eastus2 \
  -g myResourceGroup \
  --input-schema customeventschema \
  --input-mapping-fields eventType=myEventTypeField \
  --input-mapping-default-values subject=DefaultSubject dataVersion=1.0
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
# If you have not already installed the module, do it now.
# This module is required for preview features.
Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery

New-AzureRmEventGridTopic `
  -ResourceGroupName myResourceGroup `
  -Name demotopic `
  -Location eastus2 `
  -InputSchema CustomEventSchema `
  -InputMappingField @{eventType="myEventTypeField"} `
  -InputMappingDefaultValue @{subject="DefaultSubject"; dataVersion="1.0" }
```

## <a name="subscribe-to-event-grid-topic"></a>Subskrybowanie do usługi Event Grid — temat

Podczas subskrybowania tematu niestandardowego należy określić schemat, który ma być używany do otrzymywania zdarzeń. Należy określić schemat CloudEvents, niestandardowy schemat zdarzeń lub schemat Event Grid. Wartość domyślna to Event Grid Schema.

Poniższy przykład subskrybuje temat usługi Event Grid i używa schematu Event Grid. W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
topicid=$(az eventgrid topic show --name demoTopic -g myResourceGroup --query id --output tsv)

az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub1 \
  --event-delivery-schema eventgridschema \
  --endpoint <endpoint_URL>
```

W następnym przykładzie zastosowano schemat wejściowy zdarzenia:

```azurecli-interactive
az eventgrid event-subscription create \
  --source-resource-id $topicid \
  --name eventsub2 \
  --event-delivery-schema custominputschema \
  --endpoint <endpoint_URL>
```

Poniższy przykład subskrybuje temat usługi Event Grid i używa schematu Event Grid. W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
$topicid = (Get-AzureRmEventGridTopic -ResourceGroupName myResourceGroup -Name demoTopic).Id

New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub1 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema EventGridSchema
```

W następnym przykładzie zastosowano schemat wejściowy zdarzenia:

```azurepowershell-interactive
New-AzureRmEventGridSubscription `
  -ResourceId $topicid `
  -EventSubscriptionName eventsub2 `
  -EndpointType webhook `
  -Endpoint <endpoint-url> `
  -DeliverySchema CustomInputSchema
```

## <a name="publish-event-to-topic"></a>Publikuj zdarzenie w temacie

Teraz możesz wysłać zdarzenie do tematu niestandardowego i zobaczyć wynik mapowania. Następujący skrypt do opublikowania zdarzenia w [przykładowym schemacie](#original-event-schema):

W przypadku interfejsu wiersza polecenia platformy Azure użyj polecenia:

```azurecli-interactive
endpoint=$(az eventgrid topic show --name demotopic -g myResourceGroup --query "endpoint" --output tsv)
key=$(az eventgrid topic key list --name demotopic -g myResourceGroup --query "key1" --output tsv)

event='[ { "myEventTypeField":"Created", "resource":"Users/example/Messages/1000", "resourceData":{"someDataField1":"SomeDataFieldValue"} } ]'

curl -X POST -H "aeg-sas-key: $key" -d "$event" $endpoint
```

W przypadku programu PowerShell użyj polecenia:

```azurepowershell-interactive
$endpoint = (Get-AzureRmEventGridTopic -ResourceGroupName myResourceGroup -Name demotopic).Endpoint
$keys = Get-AzureRmEventGridTopicKey -ResourceGroupName myResourceGroup -Name demotopic

$htbody = @{
    myEventTypeField="Created"
    resource="Users/example/Messages/1000"
    resourceData= @{
        someDataField1="SomeDataFieldValue"
    }
}

$body = "["+(ConvertTo-Json $htbody)+"]"
Invoke-WebRequest -Uri $endpoint -Method POST -Body $body -Headers @{"aeg-sas-key" = $keys.Key1}
```

Teraz przyjrzyj się punktowi końcowemu elementu webhook. Dwie subskrypcje dostarczają zdarzenia w różnych schematach.

Pierwsza subskrypcja użyła schematu usługi Event Grid. Format dostarczonego zdarzenia to:

```json
{
  "id": "aa5b8e2a-1235-4032-be8f-5223395b9eae",
  "eventTime": "2018-11-07T23:59:14.7997564Z",
  "eventType": "Created",
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.EventGrid/topics/demotopic",
  "subject": "DefaultSubject",
  "data": {
    "myEventTypeField": "Created",
    "resource": "Users/example/Messages/1000",
    "resourceData": {
      "someDataField1": "SomeDataFieldValue"
    }
  }
}
```

Pola te zawierają mapowania z tematu niestandardowego. **myEventTypeField** jest mapowany na **Typ zdarzenia**. Używane są wartości domyślne dla elementu **dataversion** i **subject** . Obiekt **danych** zawiera oryginalne pola schematu zdarzenia.

Druga subskrypcja użyła schematu zdarzeń wejściowych. Format dostarczonego zdarzenia to:

```json
{
  "myEventTypeField": "Created",
  "resource": "Users/example/Messages/1000",
  "resourceData": {
    "someDataField1": "SomeDataFieldValue"
  }
}
```

Zwróć uwagę, że zostały dostarczone oryginalne pola.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje dotyczące dostarczania i ponawiania zdarzeń, [Event Grid dostarczania komunikatów i ponawiania próby](delivery-and-retry.md).
* Aby zapoznać się z wprowadzeniem do usługi Event Grid, zobacz [Wprowadzenie do usługi Azure Event Grid](overview.md).
* Aby szybko rozpocząć korzystanie z Event Grid, zobacz [Tworzenie i kierowanie zdarzeń niestandardowych z Azure Event Grid](custom-event-quickstart.md).
