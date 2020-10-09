---
title: 'Szybki start: wykrywanie twarzy na obrazie przy użyciu interfejsu API REST platformy Azure i języka Python'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start użyjesz interfejsu API REST rozpoznawania twarzy platformy Azure i języka Python do wykrywania twarzy na obrazie.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.custom: devx-track-python
ms.openlocfilehash: 4f747ce424e26a50aadcc84e375da230dff36fb3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858306"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-python"></a>Szybki start: wykrywanie twarzy na obrazie przy użyciu interfejsu API REST rozpoznawania twarzy i języka Python

W tym przewodniku szybki start użyjemy interfejsu API REST platformy Azure dla języka Python w celu wykrywania twarzy na obrazie. Skrypt narysuje ramki wokół twarzy i nałoży informacje o płci i wieku na obraz.

![Mężczyzna i kobieta, każde z prostokątem narysowanym wokół twarzy oraz informacjami o wieku i płci wyświetlonymi na obrazie](../images/labelled-faces-python.png)

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/cognitive-services/). 


## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Gdy masz subskrypcję platformy Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" Utwórz zasób czołowy "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu**.
    * Będziesz potrzebować klucza i punktu końcowego z zasobu, który tworzysz, aby połączyć aplikację z interfejs API rozpoznawania twarzy. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.

## <a name="run-the-jupyter-notebook"></a>Uruchamianie notesu Jupyter

Ten przewodnik Szybki start można uruchomić jako notes Jupyter w programie [MyBinder](https://mybinder.org). Aby uruchomić usługę Binder, wybierz poniższy przycisk. Następnie postępuj zgodnie z instrukcjami w notesie.

[![Obiekt](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=FaceAPI.ipynb)

## <a name="create-and-run-the-sample"></a>Tworzenie i uruchamianie próbki

Można też uruchomić ten przewodnik Szybki Start z poziomu wiersza polecenia, wykonując następujące czynności:

1. Skopiuj następujący kod do edytora tekstów.
1. W razie potrzeby wprowadź w kodzie następujące zmiany:
    1. Zastąp wartość `subscription_key` kluczem subskrypcji.
    1. Edytuj wartość, `face_api_url` Aby uwzględnić adres URL punktu końcowego dla zasobu interfejs API rozpoznawania twarzy.
    1. Opcjonalnie zastąp wartość `image_url` adresem URL innego obrazu, który chcesz analizować.
1. Zapisz kod jako plik z rozszerzeniem `.py`. Na przykład `detect-face.py`.
1. Otwórz okno wiersza polecenia.
1. W wierszu polecenia użyj polecenia `python`, aby uruchomić próbkę. Na przykład `python detect-face.py`.

:::code language="python" source="~/cognitive-services-quickstart-code/python/Face/rest/detect.py" :::

## <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON.

```json
[
  {
    "faceId": "e93e0db1-036e-4819-b5b6-4f39e0f73509",
    "faceRectangle": {
      "top": 621,
      "left": 616,
      "width": 195,
      "height": 195
    }
  }
]
```

## <a name="extract-face-attributes"></a>Wyodrębnij atrybuty kroju
 
Aby wyodrębnić atrybuty kroju, użyj modelu wykrywania 1 i Dodaj `returnFaceAttributes` parametr zapytania.

```python
params = {
    'detectionModel': 'detection_01',
    'returnFaceAttributes': 'age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise',
    'returnFaceId': 'true'
}
```

Odpowiedź zawiera teraz atrybuty czcionki. Na przykład:

```json
[
  {
    "faceId": "e93e0db1-036e-4819-b5b6-4f39e0f73509",
    "faceRectangle": {
      "top": 621,
      "left": 616,
      "width": 195,
      "height": 195
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 6.8,
        "yaw": 3.7
      },
      "gender": "male",
      "age": 37,
      "facialHair": {
        "moustache": 0.4,
        "beard": 0.4,
        "sideburns": 0.1
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.999,
        "sadness": 0.001,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "high",
        "value": 0.89
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.51
      },
      "noise": {
        "noiseLevel": "medium",
        "value": 0.59
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": false
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.04,
        "invisible": false,
        "hairColor": [
          {
            "color": "black",
            "confidence": 0.98
          },
          {
            "color": "brown",
            "confidence": 0.87
          },
          {
            "color": "gray",
            "confidence": 0.85
          },
          {
            "color": "other",
            "confidence": 0.25
          },
          {
            "color": "blond",
            "confidence": 0.07
          },
          {
            "color": "red",
            "confidence": 0.02
          }
        ]
      }
    }
  },
  {
    "faceId": "37c7c4bc-fda3-4d8d-94e8-b85b8deaf878",
    "faceRectangle": {
      "top": 693,
      "left": 1503,
      "width": 180,
      "height": 180
    },
    "faceAttributes": {
      "smile": 0.003,
      "headPose": {
        "pitch": 0,
        "roll": 2,
        "yaw": -2.2
      },
      "gender": "female",
      "age": 56,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0.001,
        "disgust": 0,
        "fear": 0,
        "happiness": 0.003,
        "neutral": 0.984,
        "sadness": 0.011,
        "surprise": 0
      },
      "blur": {
        "blurLevel": "high",
        "value": 0.83
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.41
      },
      "noise": {
        "noiseLevel": "high",
        "value": 0.76
      },
      "makeup": {
        "eyeMakeup": false,
        "lipMakeup": false
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0.06,
        "invisible": false,
        "hairColor": [
          {
            "color": "black",
            "confidence": 0.99
          },
          {
            "color": "gray",
            "confidence": 0.89
          },
          {
            "color": "other",
            "confidence": 0.64
          },
          {
            "color": "brown",
            "confidence": 0.34
          },
          {
            "color": "blond",
            "confidence": 0.07
          },
          {
            "color": "red",
            "confidence": 0.03
          }
        ]
      }
    }
  }
]
```

## <a name="next-steps"></a>Następne kroki

Następnie zapoznaj się z dokumentacją referencyjną interfejsu API rozpoznawania twarzy, aby dowiedzieć się więcej na temat obsługiwanych scenariuszy.

> [!div class="nextstepaction"]
> [Interfejs API rozpoznawania twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
