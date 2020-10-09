---
title: 'Szybki start: wykrywanie twarzy na obrazie przy użyciu interfejsu API REST i języka PHP'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku szybki start wykryjesz twarze z obrazu za pomocą interfejsu API REST twarzy przy użyciu języka PHP.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: dc28f5a9c3faa9d1c963a441f79eb1eea3fcba47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858323"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-rest-api-and-php"></a>Szybki start: wykrywanie twarzy na obrazie przy użyciu interfejsu API REST i języka PHP

W tym przewodniku szybki start użyjemy interfejsu API REST platformy Azure dla języka PHP w celu wykrywania twarzy na obrazie.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Gdy masz subskrypcję platformy Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" Utwórz zasób czołowy "  target="_blank"> <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu**.
    * Będziesz potrzebować klucza i punktu końcowego z zasobu, który tworzysz, aby połączyć aplikację z interfejs API rozpoznawania twarzy. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.
* Edytor kodu, taki jak [Visual Studio Code](https://code.visualstudio.com/download).
* Pakiet [HTTP_REQUEST2](https://pear.php.net/package/HTTP_Request2) php.
* Przeglądarka sieci Web obsługująca język PHP. Jeśli nie zostało to jeszcze zrobione, możesz to zrobić przez zainstalowanie i skonfigurowanie [XAMPP](https://www.apachefriends.org/) na komputerze.

## <a name="initialize-the-html-file"></a>Inicjowanie pliku HTML

Utwórz nowy plik HTML o nazwie *detectFaces.html* i dodaj poniższy kod.

```html
<html>
    <head>
        <title>Face Detect Sample</title>
    </head>
    <body></body>
</html>
```

## <a name="write-the-php-script"></a>Pisanie skryptu w języku PHP

Dodaj następujący kod wewnątrz elementu `body` dokumentu. Ten kod konfiguruje podstawowy interfejs użytkownika z polem adresu URL, przyciskiem **Analizuj** swoją aplikację, okienkiem odpowiedzi oraz okienkiem wyświetlania obrazu.

:::code language="php" source="~/cognitive-services-quickstart-code/php/face/rest/detect.php":::

Musisz zaktualizować `subscriptionKey` pole przy użyciu wartości klucza subskrypcji i należy zmienić `uriBase` ciąg tak, aby zawierał prawidłowy ciąg punktu końcowego. Pole `returnFaceAttributes` określa atrybuty twarzy do pobrania; możesz zmienić ten ciąg w zależności od planowanego użycia.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="run-the-script"></a>Uruchamianie skryptu

Otwórz plik w przeglądarce internetowej z obsługą języka PHP. Powinien zostać wyświetlony ciąg JSON danych twarzy, podobny do poniższego.

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

```php
$parameters = array(
    // Request parameters
    'detectionModel' => 'detection_01',
    'returnFaceAttributes' => 'age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise',
   'returnFaceId' => 'true');
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

Eksplorowanie interfejs API rozpoznawania twarzy używanych do wykrywania ludzkich twarzy w obrazie, rozgraniczania twarzy z prostokątami i zwracania atrybutów, takich jak wiek i płeć.

> [!div class="nextstepaction"]
> [Interfejsy API rozpoznawania twarzy](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
