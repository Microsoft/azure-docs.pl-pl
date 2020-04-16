---
title: 'Szybki start: wyodrębnij wydrukowany tekst- REST, JavaScript'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start wyodrębnisz z obrazu tekst drukowany przy użyciu interfejsu API przetwarzania obrazów i języka JavaScript.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 2eac3f50dc6a973e496f8907534d5ab140355d89
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404723"
---
# <a name="quickstart-extract-printed-text-ocr-using-the-computer-vision-rest-api-and-javascript"></a>Szybki start: wyodrębnianie drukowanego tekstu (OCR) przy użyciu interfejsu API REST i języka JavaScript

> [!NOTE]
> Jeśli wyodrębniasz tekst w języku angielskim, rozważ użycie nowej [operacji Odczytu](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text). Dostępny jest [przewodnik JavaScript szybki start.](https://docs.microsoft.com/azure/cognitive-services/computer-vision/quickstarts/javascript-hand-text)

W tym przewodniku Szybki start można wyodrębnić wydrukowany tekst z optycznym rozpoznawaniem znaków (OCR) z obrazu przy użyciu interfejsu API REST przetwarzania wizji komputerowej. Metoda optycznego rozpoznawania znaków ([OCR](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fc)) pozwala wykrywać na obrazie tekst wydrukowany i wyodrębniać rozpoznane znaki do strumienia znaków, którego mogą używać komputery.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

Musisz mieć klucz subskrypcji funkcji przetwarzania obrazów. Możesz uzyskać bezpłatny klucz próbny z [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Możesz też postępować zgodnie z instrukcjami w aplikacji [Utwórz konto usług Cognitive Services,](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) aby zasubskrybować usługę Computer Vision i uzyskać klucz. Zapisz klucz subskrypcji i adres URL punktu końcowego w lokalizacji tymczasowej.

## <a name="create-and-run-the-sample"></a>Tworzenie i uruchamianie próbki

Aby utworzyć i uruchomić przykład, wykonaj następujące kroki:

1. Utwórz plik o nazwie _get-printed-text.html_, otwórz go w edytorze tekstu i skopiuj do niego następujący kod.
1. Opcjonalnie zastąp wartość `value` atrybutu dla kontrolki `inputImage` adresem URL innego obrazu, który chcesz analizować.
1. Otwórz okno przeglądarki.
1. W przeglądarce przeciągnij plik i upuść go w oknie przeglądarki.
1. Gdy strona sieci Web jest wyświetlana w przeglądarce, wklej klucz subskrypcji i adres URL punktu końcowego do odpowiednich pól wejściowych.
1. Wybierz przycisk **Odczyt obrazu.**

```html
<!DOCTYPE html>
<html>
<head>
    <title>OCR Sample</title>
    <script src="https://ajax.googleapis.com/ajax/libs/jquery/1.9.0/jquery.min.js"></script>
</head>
<body>

<script type="text/javascript">
    function processImage() {
        // **********************************************
        // *** Update or verify the following values. ***
        // **********************************************

        var subscriptionKey = document.getElementById("subscriptionKey").value;
        var endpoint = document.getElementById("endpointUrl").value;
        
        var uriBase = endpoint + "vision/v2.1/ocr";

        // Request parameters.
        var params = {
            "language": "unk",
            "detectOrientation": "true",
        };

        // Display the image.
        var sourceImageUrl = document.getElementById("inputImage").value;
        document.querySelector("#sourceImage").src = sourceImageUrl;

        // Perform the REST API call.
        $.ajax({
            url: uriBase + "?" + $.param(params),

            // Request headers.
            beforeSend: function(jqXHR){
                jqXHR.setRequestHeader("Content-Type","application/json");
                jqXHR.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey);
            },

            type: "POST",

            // Request body.
            data: '{"url": ' + '"' + sourceImageUrl + '"}',
        })

        .done(function(data) {
            // Show formatted JSON on webpage.
            $("#responseTextArea").val(JSON.stringify(data, null, 2));
        })

        .fail(function(jqXHR, textStatus, errorThrown) {
            // Display error message.
            var errorString = (errorThrown === "") ?
                "Error. " : errorThrown + " (" + jqXHR.status + "): ";
            errorString += (jqXHR.responseText === "") ? "" :
                (jQuery.parseJSON(jqXHR.responseText).message) ?
                    jQuery.parseJSON(jqXHR.responseText).message :
                    jQuery.parseJSON(jqXHR.responseText).error.message;
            alert(errorString);
        });
    };
</script>

<h1>Optical Character Recognition (OCR):</h1>
Enter the URL to an image of printed text, then
click the <strong>Read image</strong> button.
<br><br>
Subscription key: 
<input type="text" name="subscriptionKey" id="subscriptionKey"
    value="" /> 
Endpoint URL:
<input type="text" name="endpointUrl" id="endpointUrl"
    value="" />
<br><br>
Image to read:
<input type="text" name="inputImage" id="inputImage" 
    value="https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png" />
<button onclick="processImage()">Read image</button>
<br><br>
<div id="wrapper" style="width:1020px; display:table;">
    <div id="jsonOutput" style="width:600px; display:table-cell;">
        Response:
        <br><br>
        <textarea id="responseTextArea" class="UIInput"
                  style="width:580px; height:400px;"></textarea>
    </div>
    <div id="imageDiv" style="width:420px; display:table-cell;">
        Source image:
        <br><br>
        <img id="sourceImage" width="400" />
    </div>
</div>
</body>
</html>
```

## <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON. Przykładowa strona sieci Web analizuje i wyświetla pomyślną odpowiedź w oknie przeglądarki, podobnie jak w poniższym przykładzie:

```json
{
  "language": "en",
  "orientation": "Up",
  "textAngle": 0,
  "regions": [
    {
      "boundingBox": "21,16,304,451",
      "lines": [
        {
          "boundingBox": "28,16,288,41",
          "words": [
            {
              "boundingBox": "28,16,288,41",
              "text": "NOTHING"
            }
          ]
        },
        {
          "boundingBox": "27,66,283,52",
          "words": [
            {
              "boundingBox": "27,66,283,52",
              "text": "EXISTS"
            }
          ]
        },
        {
          "boundingBox": "27,128,292,49",
          "words": [
            {
              "boundingBox": "27,128,292,49",
              "text": "EXCEPT"
            }
          ]
        },
        {
          "boundingBox": "24,188,292,54",
          "words": [
            {
              "boundingBox": "24,188,292,54",
              "text": "ATOMS"
            }
          ]
        },
        {
          "boundingBox": "22,253,297,32",
          "words": [
            {
              "boundingBox": "22,253,105,32",
              "text": "AND"
            },
            {
              "boundingBox": "144,253,175,32",
              "text": "EMPTY"
            }
          ]
        },
        {
          "boundingBox": "21,298,304,60",
          "words": [
            {
              "boundingBox": "21,298,304,60",
              "text": "SPACE."
            }
          ]
        },
        {
          "boundingBox": "26,387,294,37",
          "words": [
            {
              "boundingBox": "26,387,210,37",
              "text": "Everything"
            },
            {
              "boundingBox": "249,389,71,27",
              "text": "else"
            }
          ]
        },
        {
          "boundingBox": "127,431,198,36",
          "words": [
            {
              "boundingBox": "127,431,31,29",
              "text": "is"
            },
            {
              "boundingBox": "172,431,153,36",
              "text": "opinion."
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Następne kroki

Następnie zapoznaj się z aplikacją JavaScript, która używa wizji komputerowej do optycznego rozpoznawania znaków (OCR); tworzenie miniatur przyciętych inteligentnie; i wykrywać, kategoryzować, oznaczać i opisywać funkcje wizualne na obrazach. 

> [!div class="nextstepaction"]
> [Samouczek języka JavaScript interfejsu API przetwarzania obrazów](../Tutorials/javascript-tutorial.md)

* Aby szybko zacząć eksperymentować z interfejsem API przetwarzania obrazów, wypróbuj [konsolę testowania interfejsu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).