---
title: 'Szybki start: wykrywanie twarzy na obrazie przy użyciu interfejsu API REST platformy Azure i biblioteki cURL'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start użyjesz interfejsu API REST rozpoznawania twarzy platformy Azure i biblioteki cURL do wykrywania twarzy na obrazie.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: 7e7eb9ba942cd29db824388c02b2933c533c4b45
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81403014"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-curl"></a>Szybki start: wykrywanie twarzy na obrazie przy użyciu interfejsu API REST rozpoznawania twarzy i biblioteki cURL

W tym przewodniku Szybki start użyjesz interfejsu API rest usługi Azure Face z cURL do wykrywania ludzkich twarzy w obrazie.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem. 

## <a name="prerequisites"></a>Wymagania wstępne

- Klucz subskrypcji Face. Klucz subskrypcji bezpłatnej wersji próbnej możesz uzyskać na stronie [Wypróbuj usługi Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=face-api). Możesz też postępować zgodnie z instrukcjami w aplikacji [Utwórz konto usług Cognitive Services,](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) aby zasubskrybować usługę Face i uzyskać klucz.

## <a name="write-the-command"></a>Pisanie polecenia
 
Użyjesz polecenia, takiego jak poniższe, aby wywołać interfejs API twarzy i uzyskać dane atrybutu twarzy z obrazu. Najpierw skopiuj kod do edytora tekstów &mdash; konieczne będzie wprowadzenie zmian w niektórych częściach polecenia przed jego uruchomieniem.

```shell
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" "https://<My Endpoint String>.com/face/v1.0/detect?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise" -H "Content-Type: application/json" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}"
```

### <a name="subscription-key"></a>Klucz subskrypcji
Zastąp wartość `<Subscription Key>` prawidłowym kluczem subskrypcji interfejsu API rozpoznawania twarzy.

### <a name="face-endpoint-url"></a>Adres URL punktu końcowego interfejsu API rozpoznawania twarzy

Adres URL `https://<My Endpoint String>.com/face/v1.0/detect` wskazuje punkt końcowy interfejsu API rozpoznawania twarzy platformy Azure. Może być konieczna zmiana pierwszej części tego adresu URL, aby dopasować punkt końcowy odpowiadający kluczowi subskrypcji.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

### <a name="url-query-string"></a>Ciąg zapytania adresu URL

Ciąg zapytania adresu URL punktu końcowego interfejs API rozpoznawania twarzy określa atrybuty twarzy do pobrania. Możesz zmienić ten ciąg w zależności od zamierzonego użycia.

```
?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise
```

### <a name="image-source-url"></a>Adres URL źródła obrazu
Źródłowy adres URL wskazuje obraz, który zostanie użyty jako dane wejściowe. Można to zmienić, aby wskazać dowolny obraz, który chcesz przeanalizować.

```
https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg
``` 

## <a name="run-the-command"></a>Uruchamianie polecenia

Po dokonaniu zmian otwórz wiersz polecenia i wprowadź nowe polecenie. Informacje o twarzy powinny zostać wyświetlone jako dane JSON w oknie konsoli. Przykład:

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 0.1,
        "yaw": -32.9
      },
      "gender": "female",
      "age": 22.9,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.986,
        "sadness": 0.009,
        "surprise": 0.005
      },
      "blur": {
        "blurLevel": "low",
        "value": 0.06
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.67
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "black",
            "confidence": 0.87
          },
          {
            "color": "other",
            "confidence": 0.51
          },
          {
            "color": "blond",
            "confidence": 0.08
          },
          {
            "color": "red",
            "confidence": 0.08
          },
          {
            "color": "gray",
            "confidence": 0.02
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start napisano polecenie cURL, które wywołuje usługę Azure Face w celu wykrycia twarzy w obrazie i zwrócenia ich atrybutów. Następnie zapoznaj się z dokumentacją referencyjną interfejsu API rozpoznawania twarzy, aby dowiedzieć się więcej.

> [!div class="nextstepaction"]
> [Interfejs API rozpoznawania twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
