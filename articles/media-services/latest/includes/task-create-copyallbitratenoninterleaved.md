---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 11/18/2020
ms.author: inhenkel
ms.custom: REST
ms.openlocfilehash: 0d248fa57d5288cedae8dc46441739575c48c129
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94918225"
---
<!--Create a copyAllNonInterleave transform with REST-->

Następujące polecenie REST platformy Azure tworzy transformację przy użyciu `copyAllBitrateNonInterleaved` z `#Microsoft.Media.BuiltInStandardEncoderPreset` ustawień domyślnych. Zastąp wartości `subscriptionID` , `resourceGroup` i wartościami, dla `accountName` których obecnie pracujesz. Nadaj nazwę przekształceniu według ustawienia `transformName` . 

Aby wyświetlić wszystkie dostępne transformacje interfejsu API REST, zobacz [transformacje-Create lub Update](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#definitions).

```REST
PUT https://management.azure.com/subscriptions/{{subscriptionId}}/resourceGroups/{{resourceGroup}}/providers/Microsoft.Media/mediaServices/{{accountName}}/transforms/{{transformName}}?api-version=2020-05-01

```

## <a name="body"></a>Treść

```json
{
    "name": "myTransform",
    "id": "/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/inhenkelRG/providers/Microsoft.Media/mediaservices/inhenkel/transforms/myTransform",
    "type": "Microsoft.Media/mediaservices/transforms",
    "properties": {
        "description": "Basic Transform using a copyAllBitrateNonInterleaved encoding preset from the library of built-in Standard Encoder presets",
        "outputs": [
            {
                "onError": "StopProcessingJob",
                "relativePriority": "Normal",
                "preset": {
                    "@odata.type": "#Microsoft.Media.BuiltInStandardEncoderPreset",
                    "presetName": "CopyAllBitrateNonInterleaved"
                }
            }
        ]
    }
}
```

## <a name="sample-response"></a>Przykładowa odpowiedź
```json
{
  "name":"myTransform",
    "id":"/subscriptions/35c2594a-23da-4fce-b59c-f6fb9513eeeb/resourceGroups/inhenkelRG/providers/Microsoft.Media/mediaservices/inhenkel/transforms/myTransform","type":"Microsoft.Media/mediaservices/transforms","properties":{
        "created":"2020-11-18T19:19:27.4405209Z",
        "description":"Basic Transform using a copyAllBitrateNonInterleaved encoding preset from the library of built-in Standard Encoder presets","lastModified":"2020-11-18T19:19:27.4405209Z",
        "outputs":[
            {
                "onError":"StopProcessingJob","relativePriority":"Normal","preset":{
                "@odata.type":"#Microsoft.Media.BuiltInStandardEncoderPreset",
                "presetName":"CopyAllBitrateNonInterleaved"
        }
      }
    ]
  }
}
```
