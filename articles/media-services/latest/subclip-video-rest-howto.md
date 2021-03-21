---
title: Podcinanie wideo przy kodowaniu przy użyciu Media Services REST
description: W tym temacie opisano sposób podcinania wideo przy kodowaniu przy użyciu Azure Media Services za pomocą interfejsu REST
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: how-to
ms.date: 06/10/2019
ms.author: inhenkel
ms.openlocfilehash: 6c287e168289082b2bd717a2706dd011c7115691
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98955670"
---
# <a name="subclip-a-video-when-encoding-with-media-services---rest"></a>Podcinanie wideo przy kodowaniu przy użyciu Media Services-REST

Można przyciąć lub podciąć klip wideo przy kodowaniu przy użyciu [zadania](/rest/api/media/jobs). Ta funkcja działa z dowolnym [przekształceniem](/rest/api/media/transforms) utworzonym przy użyciu ustawień wstępnych [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) lub predefiniowanych ustawień [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) . 

W przykładzie w dalszej części tego tematu powstaje zadanie, które przycina wideo podczas przesyłania zadania kodowania. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym temacie, należy:

- [Utwórz konto Azure Media Services](./create-account-howto.md).
- [Skonfiguruj funkcję publikowania dla Azure Media Services wywołań interfejsu API REST](media-rest-apis-with-postman.md).
    
    Pamiętaj, aby postępować zgodnie z ostatnim krokiem w temacie [pobieranie tokenu usługi Azure AD](media-rest-apis-with-postman.md#get-azure-ad-token). 
- Utwórz transformację i wyjściowe zasoby. Możesz zobaczyć, jak utworzyć przekształcenia i zasoby wyjściowe w [kodowaniu pliku zdalnego na podstawie adresu URL i przesłać strumieniowo samouczek wideo-REST](stream-files-tutorial-with-rest.md) .
- Zapoznaj się z tematem [pojęć dotyczących kodowania](encoding-concept.md) .

## <a name="create-a-subclipping-job"></a>Tworzenie zadania wycinka

1. W pobranej kolekcji Poster wybierz pozycję **przekształcenia i zadania**  ->  **Utwórz zadanie z wycinkem podrzędnym**.
    
    Żądanie **Put** wygląda następująco:
    
    ```
    https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/transforms/:transformName/jobs/:jobName?api-version={{api-version}}
    ```
1. Zaktualizuj wartość zmiennej środowiskowej "transformname" przy użyciu nazwy przekształcenia. 
1. Wybierz kartę **treść** i zaktualizuj element "myOutputAsset" przy użyciu nazwy wyjściowego elementu zawartości.

    ```json
    {
      "properties": {
        "description": "A Job with transform cb9599fb-03b3-40eb-a2ff-7ea909f53735 and single clip.",
       
        "input": {
          "@odata.type": "#Microsoft.Media.JobInputHttp",
          "baseUri": "https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/",
          "files": [
            "Ignite-short.mp4"
          ],
          "start": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT10S"
          },
          "end": {
            "@odata.type": "#Microsoft.Media.AbsoluteClipTime",
            "time": "PT40S"
          }
        },
      
        "outputs": [
          {
            "@odata.type": "#Microsoft.Media.JobOutputAsset",
            "assetName": "myOutputAsset"
          }
        ],
        "priority": "Normal"
      }
    }
    ```
1. Kliknij pozycję **Wyślij**.

    Zostanie wyświetlona **odpowiedź** wraz z informacjami o zadaniu, które zostało utworzone i przesłane oraz o stanie zadania. 

## <a name="next-steps"></a>Następne kroki

[Jak kodować przy użyciu przekształcenia niestandardowego](custom-preset-rest-howto.md) 
