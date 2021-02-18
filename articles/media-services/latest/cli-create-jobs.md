---
title: Przykład skryptu interfejsu wiersza polecenia platformy Azure — tworzenie i przesyłanie zadania
description: Skrypt interfejsu wiersza polecenia platformy Azure w tym temacie pokazuje sposób przesyłania zadania do prostego przekształcenia kodowania przy użyciu adresu URL protokołu HTTPS.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 9291d53822f0fbb80f759908933db58f2224c3d7
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101091917"
---
# <a name="cli-example-create-and-submit-a-job"></a>Przykład interfejsu wiersza polecenia: tworzenie i przesyłanie zadania

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

W przypadku usługi Media Services 3 po przesłaniu zadań w celu przetworzenia wideo należy poinformować usługę Media Services o tym, gdzie można znaleźć wejściowe wideo. Jedną z opcji jest określenie adresu URL HTTPS jako dane wejściowe zadania (jak pokazano w tym artykule). 

## <a name="prerequisites"></a>Wymagania wstępne 

[Utwórz konto Media Services](./create-account-howto.md).

## <a name="example-script"></a>Przykładowy skrypt

Po uruchomieniu polecenia `az ams job start` można ustawić etykietę dla danych wyjściowych zadania. Etykieta może później służyć do identyfikowania przeznaczenia wyjściowego elementu zawartości. 

- W przypadku przypisywania wartości do etykiety należy dla parametru „--output-assets” ustawić wartość „assetname=etykieta”.
- W przeciwnym przypadku należy dla parametru „--output-assets” ustawić wartość „assetname=”.
  Zwróć uwagę na dodanie znaku „=” do parametru `output-assets`. 

```azurecli
az ams job start \
  --name testJob001 \
  --transform-name testEncodingTransform \
  --base-uri 'https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/' \
  --files 'Ignite-short.mp4' \
  --output-assets testOutputAssetName= \
  -a amsaccount \
  -g amsResourceGroup 
```

Uzyskasz odpowiedź podobną do następującej:

```
{
  "correlationData": {},
  "created": "2019-02-15T05:08:26.266104+00:00",
  "description": null,
  "id": "/subscriptions/<id>/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount/transforms/testEncodingTransform/jobs/testJob001",
  "input": {
    "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
    "files": [
      "Ignite-short.mp4"
    ],
    "label": null,
    "odatatype": "#Microsoft.Media.JobInputHttp"
  },
  "lastModified": "2019-02-15T05:08:26.266104+00:00",
  "name": "testJob001",
  "outputs": [
    {
      "assetName": "testOutputAssetName",
      "error": null,
      "label": "",
      "odatatype": "#Microsoft.Media.JobOutputAsset",
      "progress": 0,
      "state": "Queued"
    }
  ],
  "priority": "Normal",
  "resourceGroup": "amsResourceGroup",
  "state": "Queued",
  "type": "Microsoft.Media/mediaservices/transforms/jobs"
}
```

## <a name="next-steps"></a>Następne kroki

[AZ AMS Job (CLI)](/cli/azure/ams/job?view=azure-cli-latest)
