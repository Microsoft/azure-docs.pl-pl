---
title: 'Szybki start: generowanie miniatury — REST, JavaScript'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start wygenerujesz miniaturę obrazu za pomocą interfejsu API przetwarzania obrazów przy użyciu języka JavaScript.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.custom: seodec18, devx-track-js
ms.openlocfilehash: c6e15795d3dde6b87125de30616136140218fe5a
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91262590"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-javascript"></a>Szybki Start: generowanie miniatury przy użyciu interfejsu API REST przetwarzanie obrazów i języka JavaScript

W tym przewodniku szybki start utworzysz miniaturę obrazu przy użyciu interfejsu API REST przetwarzanie obrazów. Należy określić wysokość i szerokość, która może się różnić od obrazu wejściowego. Interfejs API przetwarzania obrazów wykorzystuje inteligentne przycinanie, aby określić obszar zainteresowania i wygenerować współrzędne przycinania na podstawie tego obszaru.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Gdy masz subskrypcję platformy Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" Utwórz zasób przetwarzanie obrazów "  target="_blank"> utwórz zasób przetwarzanie obrazów <span class="docon docon-navigate-external x-hidden-focus"></span> </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu**.
    * Będziesz potrzebować klucza i punktu końcowego z zasobu, który utworzysz, aby połączyć aplikację z usługą przetwarzanie obrazów. Klucz i punkt końcowy zostaną wklejone do poniższego kodu w dalszej części przewodnika Szybki Start.
    * Możesz użyć warstwy cenowej bezpłatna ( `F0` ) w celu wypróbowania usługi i później przeprowadzić uaktualnienie do warstwy płatnej dla środowiska produkcyjnego.

## <a name="create-and-run-the-sample"></a>Tworzenie i uruchamianie próbki

Aby utworzyć i uruchomić przykład, wykonaj następujące kroki:

1. Utwórz plik o nazwie _get-thumbnail.html_, otwórz go w edytorze tekstów i skopiuj do niego następujący kod.
1. Opcjonalnie Zastąp wartość `value` atrybutu `inputImage` kontrolki adresem URL innego obrazu, który chcesz analizować.
1. Otwórz okno przeglądarki.
1. W przeglądarce przeciągnij plik i upuść go w oknie przeglądarki.
1. Gdy strona sieci Web zostanie wyświetlona w przeglądarce, Wklej klucz subskrypcji i adres URL punktu końcowego do odpowiednich pól wejściowych.
1. Na koniec wybierz przycisk **Generuj miniaturę** .

```html
<!DOCTYPE html>
<html>
<head>
    <title>Thumbnail Sample</title>
</head>
<body>

<script type="text/javascript">
    function processImage() {
        // **********************************************
        // *** Update or verify the following values. ***
        // **********************************************

        var subscriptionKey = document.getElementById("subscriptionKey").value;
        var endpoint = document.getElementById("endpointUrl").value;
        
        var uriBase = endpoint + "vision/v3.0/generateThumbnail";

        // Request parameters.
        var params = "?width=100&height=150&smartCropping=true";

        // Display the source image.
        var sourceImageUrl = document.getElementById("inputImage").value;
        document.querySelector("#sourceImage").src = sourceImageUrl;

        // Prepare the REST API call:

        // Create the HTTP Request object.
        var xhr = new XMLHttpRequest();

        // Identify the request as a POST, with the URL and parameters.
        xhr.open("POST", uriBase + params);

        // Add the request headers.
        xhr.setRequestHeader("Content-Type","application/json");
        xhr.setRequestHeader("Ocp-Apim-Subscription-Key", subscriptionKey);

        // Set the response type to "blob" for the thumbnail image data.
        xhr.responseType = "blob";

        // Process the result of the REST API call.
        xhr.onreadystatechange = function(e) {
            if(xhr.readyState === XMLHttpRequest.DONE) {

                // Thumbnail successfully created.
                if (xhr.status === 200) {
                    // Show response headers.
                    var s = JSON.stringify(xhr.getAllResponseHeaders(), null, 2);
                    document.getElementById("responseTextArea").value =
                        JSON.stringify(xhr.getAllResponseHeaders(), null, 2);

                    // Show thumbnail image.
                    var urlCreator = window.URL || window.webkitURL;
                    var imageUrl = urlCreator.createObjectURL(this.response);
                    document.querySelector("#thumbnailImage").src = imageUrl;
                } else {
                    // Display the error message. The error message is the response
                    // body as a JSON string. The code in this code block extracts
                    // the JSON string from the blob response.
                    var reader = new FileReader();

                    // This event fires after the blob has been read.
                    reader.addEventListener('loadend', (e) => {
                        document.getElementById("responseTextArea").value =
                            JSON.stringify(JSON.parse(e.srcElement.result), null, 2);
                    });

                    // Start reading the blob as text.
                    reader.readAsText(xhr.response);
                }
            }
        }

        // Make the REST API call.
        xhr.send('{"url": ' + '"' + sourceImageUrl + '"}');
    };
</script>

<h1>Generate thumbnail image:</h1>
Enter the URL to an image to use in creating a thumbnail image,
then click the <strong>Generate thumbnail</strong> button.
<br><br>
Subscription key: 
<input type="text" name="subscriptionKey" id="subscriptionKey"
    value="" /> 
Endpoint URL:
<input type="text" name="endpointUrl" id="endpointUrl"
    value="" />
<br><br>
Image for thumbnail:
<input type="text" name="inputImage" id="inputImage"
    value="https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg" />
<button onclick="processImage()">Generate thumbnail</button>
<br><br>
<div id="wrapper" style="width:1160px; display:table;">
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
    <div id="thumbnailDiv" style="width:140px; display:table-cell;">
        Thumbnail:
        <br><br>
        <img id="thumbnailImage" />
    </div>
</div>
</body>
</html>
```

## <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Pomyślna odpowiedź jest zwracana jako dane binarne, które reprezentują dane obrazu miniatury. Jeśli żądanie zakończy się powodzeniem, miniatura zostanie wygenerowana na podstawie danych binarnych w odpowiedzi i wyświetlona w oknie przeglądarki. Jeśli żądanie zakończy się niepowodzeniem, odpowiedź zostanie wyświetlona w oknie konsoli. Odpowiedź na nieudane żądanie zawiera kod błędu oraz komunikat, który umożliwi określenie, co poszło nie tak.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z aplikacją w języku JavaScript, w której zastosowano interfejs API przetwarzania obrazów do optycznego rozpoznawania znaków (OCR), inteligentnego przycinania miniatur oraz wykrywania, kategoryzowania, tagowania i opisywania elementów wizualnych, w tym twarzy, na obrazie. Aby szybko zacząć eksperymentować z interfejsem API przetwarzania obrazów, wypróbuj [konsolę testowania interfejsu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Samouczek języka JavaScript interfejsu API przetwarzania obrazów](../Tutorials/javascript-tutorial.md)
