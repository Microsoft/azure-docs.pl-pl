---
title: " Tworzenie jednostronicowej aplikacji sieci Web — wyszukiwanie wizualne Bing"
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak zintegrować interfejs API wyszukiwania wizualnego Bing w jednostronicowej aplikacji sieci Web.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: tutorial
ms.date: 03/27/2020
ms.author: aahi
ms.custom: devx-track-js
ms.openlocfilehash: b9458f6080759f0948d103f7fe3d131f6a4c4e37
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93092725"
---
# <a name="tutorial-create-a-visual-search-single-page-web-app"></a>Samouczek: Tworzenie wyszukiwanie wizualne jednostronicowej aplikacji sieci Web

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](https://aka.ms/cogsvcs/bingmove).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](https://aka.ms/cogsvcs/bingmigration).

Interfejs API wyszukiwania wizualnego Bing zwraca szczegółowe informacje o obrazie. Możesz przekazać obraz lub podać adres URL. Szczegółowe dane są wizualnie podobnymi obrazami, źródłami zakupów, stronami sieci Web, które zawierają obraz i nie tylko. Szczegółowe informacje zwracane przez interfejs API wyszukiwania wizualnego Bing są podobne do pokazanych w Bing.com/images.

W tym samouczku wyjaśniono, jak rozciągnąć jednostronicową aplikację sieci Web dla interfejs API wyszukiwania obrazów Bing. Aby wyświetlić ten samouczek lub uzyskać użyty kod źródłowy, zobacz [Samouczek: Tworzenie aplikacji jednostronicowej dla interfejs API wyszukiwania obrazów Bing](../Bing-Image-Search/tutorial-bing-image-search-single-page-app.md).

Pełny kod źródłowy dla tej aplikacji (po rozszerzeniu go na korzystanie z interfejs API wyszukiwania wizualnego Bing) jest dostępny w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/Tutorials/Bing-Visual-Search/BingVisualSearchApp.html).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="call-the-bing-visual-search-api-and-handle-the-response"></a>Wywołaj interfejs API wyszukiwania wizualnego Bing i obsługuj odpowiedź

Edytuj samouczek wyszukiwanie obrazów Bing i Dodaj następujący kod na końcu `<script>` elementu (i przed tagiem zamykającym `</script>` ). Poniższy kod obsługuje odpowiedź wyszukiwania wizualnego z interfejsu API, wykonuje iterację w wyniku i wyświetla je:

``` javascript
function handleVisualSearchResponse(){
    if(this.status !== 200){
        console.log(this.responseText);
        return;
    }
    let json = this.responseText;
    let response = JSON.parse(json);
    for (let i =0; i < response.tags.length; i++) {
        let tag = response.tags[i];
        if (tag.displayName === '') {
            for (let j = 0; j < tag.actions.length; j++) {
                let action = tag.actions[j];
                if (action.actionType === 'VisualSearch') {
                    let html = '';
                    for (let k = 0; k < action.data.value.length; k++) {
                        let item = action.data.value[k];
                        let height = 120;
                        let width = Math.max(Math.round(height * item.thumbnail.width / item.thumbnail.height), 120);
                        html += "<img src='"+ item.thumbnailUrl + "&h=" + height + "&w=" + width + "' height=" + height + " width=" + width + "'>";
                    }
                    showDiv("insights", html);
                    window.scrollTo({top: document.getElementById("insights").getBoundingClientRect().top, behavior: "smooth"});
                }
            }
        }
    }
}
```

Poniższy kod wysyła żądanie wyszukiwania do interfejsu API przy użyciu odbiornika zdarzeń do wywołania `handleVisualSearchResponse()` :

```javascript
function bingVisualSearch(insightsToken){
    let visualSearchBaseURL = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch',
        boundary = 'boundary_ABC123DEF456',
        startBoundary = '--' + boundary,
        endBoundary = '--' + boundary + '--',
        newLine = "\r\n",
        bodyHeader = 'Content-Disposition: form-data; name="knowledgeRequest"' + newLine + newLine;

    let postBody = {
        imageInfo: {
            imageInsightsToken: insightsToken
        }
    }
    let requestBody = startBoundary + newLine;
    requestBody += bodyHeader;
    requestBody += JSON.stringify(postBody) + newLine + newLine;
    requestBody += endBoundary + newLine;

    let request = new XMLHttpRequest();

    try {
        request.open("POST", visualSearchBaseURL);
    } 
    catch (e) {
        renderErrorMessage("Error performing visual search: " + e.message);
    }
    request.setRequestHeader("Ocp-Apim-Subscription-Key", getSubscriptionKey());
    request.setRequestHeader("Content-Type", "multipart/form-data; boundary=" + boundary);
    request.addEventListener("load", handleVisualSearchResponse);
    request.send(requestBody);
}
```

## <a name="capture-insights-token"></a>Przechwytywanie tokenu szczegółowych informacji

Dodaj następujący kod do `searchItemsRenderer` obiektu. Ten kod dodaje link **znajdź podobne** , który po kliknięciu wywołuje funkcję `bingVisualSearch`. Funkcja otrzymuje `imageInsightsToken` jako argument.

``` javascript
html.push("<a href='javascript:bingVisualSearch(\"" + item.imageInsightsToken + "\");'>find similar</a><br>");
```

## <a name="display-similar-images"></a>Wyświetlenie podobnych obrazów

Dodaj następujący kod HTML w wierszu 601. Ten kod znaczników dodaje element, aby wyświetlić wyniki wywołania interfejs API wyszukiwania wizualnego Bing:

``` html
<div id="insights">
    <h3><a href="#" onclick="return toggleDisplay('_insights')">Similar</a></h3>
    <div id="_insights" style="display: none"></div>
</div>
```

Po umieszczeniu na miejscu całego nowego kodu JavaScript i wszystkich elementów HTML wyniki wyszukiwania są wyświetlane z linkiem **znajdź podobne** . Kliknij ten link, aby wypełnić sekcję **Podobne** obrazami podobnymi do obrazu wybranego przez Ciebie. W celu wyświetlenia tych obrazów może być konieczne rozwinięcie sekcji **Podobne** .

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Kadrowanie obrazu za pomocą zestawu SDK wyszukiwanie wizualne Bing dla języka C #](tutorial-visual-search-crop-area-results.md)
