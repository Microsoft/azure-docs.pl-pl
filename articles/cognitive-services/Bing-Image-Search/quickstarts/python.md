---
title: 'Szybki Start: Wyszukiwanie obrazów przy użyciu interfejsu API REST wyszukiwanie obrazów Bing i języka Python'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki Start, aby wysyłać żądania wyszukiwania obrazów do interfejsu API wyszukiwania obrazów Bing przy użyciu języka Python i otrzymywać odpowiedzi w formacie JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-python
ms.openlocfilehash: 0d0f3e3578f1fe40794ec5697291d35e3d277419
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96341737"
---
# <a name="quickstart-search-for-images-using-the-bing-image-search-rest-api-and-python"></a>Szybki Start: Wyszukiwanie obrazów przy użyciu interfejsu API REST wyszukiwanie obrazów Bing i języka Python

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Skorzystaj z tego przewodnika Szybki Start, aby dowiedzieć się, jak wysyłać żądania wyszukiwania do interfejs API wyszukiwania obrazów Bing. Ta aplikacja języka Python wysyła zapytanie dotyczące wyszukiwania do interfejsu API i wyświetla adres URL pierwszego obrazu w wynikach. Mimo że aplikacja jest zapisywana w języku Python, interfejs API jest usługą sieci Web RESTful zgodną z większością języków programowania.

Aby uruchomić ten przykład jako Notes Jupyter na [oprawie](https://mybinder.org), wybierz wskaźnik **uruchamiania spinacza** :

[![Obiekt](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingImageSearchAPI.ipynb)


Kod źródłowy dla tego przykładu jest dostępny [w witrynie GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingImageSearchv7.py) z dodatkową obsługą błędów i adnotacjami.


## <a name="prerequisites"></a>Wymagania wstępne

* [Python 2. x lub 3. x](https://www.python.org/)
* [Biblioteka PIL (Python Imaging Library)](https://pillow.readthedocs.io/en/stable/index.html)
* [matplotlib](https://matplotlib.org/) 

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](../../../../includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy plik kodu Python w ulubionym środowisku IDE lub edytorze i zaimportuj poniższe moduły. Utwórz zmienne dla klucza subskrypcji, punktu końcowego wyszukiwania i terminu wyszukiwania. W przypadku programu `search_url` można użyć globalnego punktu końcowego w poniższym kodzie lub użyć niestandardowego punktu końcowego [poddomeny](../../../cognitive-services/cognitive-services-custom-subdomains.md) wyświetlanego w Azure Portal dla zasobu.

    ```python
    import requests
    import matplotlib.pyplot as plt
    from PIL import Image
    from io import BytesIO
    
    subscription_key = "your-subscription-key"
    search_url = "https://api.cognitive.microsoft.com/bing/v7.0/images/search"
    search_term = "puppies"
    ```

2. Dodaj klucz subskrypcji do nagłówka `Ocp-Apim-Subscription-Key`, tworząc słownik i dodając klucz jako wartość. 

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    ```

## <a name="create-and-send-a-search-request"></a>Tworzenie i wysyłanie żądania wyszukiwania

1. Utwórz słownik na potrzeby parametrów żądania wyszukiwania. Dodaj termin wyszukiwania do parametru `q`. Ustaw parametr na, aby `license` `public` wyszukać obrazy w domenie publicznej. Ustaw wartość na, aby `imageType` `photo` wyszukać tylko zdjęcia.

    ```python
    params  = {"q": search_term, "license": "public", "imageType": "photo"}
    ```

2. Użyj biblioteki `requests` w celu wywołania interfejsu API wyszukiwania obrazów Bing. Dodaj nagłówek i parametry do żądania, a następnie zwróć odpowiedź jako obiekt JSON. Pobierz adresy URL do kilku miniatur obrazów z `thumbnailUrl` pola odpowiedzi.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    thumbnail_urls = [img["thumbnailUrl"] for img in search_results["value"][:16]]
    ```

## <a name="view-the-response"></a>Wyświetlanie odpowiedzi

1. Utwórz nowy rysunek z czterema kolumnami i czterema wierszami przy użyciu biblioteki matplotlib. 

2. Iteruj kolumny i wiersze na rysunku, a następnie użyj metody `Image.open()` biblioteki PIL w celu dodania miniatury obrazu do każdej przestrzeni. 

3. Użyj metody `plt.show()` w celu narysowania rysunku i wyświetlenia obrazów.

    ```python
    f, axes = plt.subplots(4, 4)
    for i in range(4):
        for j in range(4):
            image_data = requests.get(thumbnail_urls[i+4*j])
            image_data.raise_for_status()
            image = Image.open(BytesIO(image_data.content))        
            axes[i][j].imshow(image)
            axes[i][j].axis("off")
    plt.show()
    ```


## <a name="example-json-response"></a>Przykładowa odpowiedź JSON

Odpowiedzi z interfejsu API wyszukiwania obrazów Bing są zwracane w formacie JSON. Ta przykładowa odpowiedź została obcięta w celu pokazania pojedynczego wyniku.

```json
{
"_type":"Images",
"instrumentation":{
    "_type":"ResponseInstrumentation"
},
"readLink":"images\/search?q=tropical ocean",
"webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=tropical ocean&FORM=OIIARP",
"totalEstimatedMatches":842,
"nextOffset":47,
"value":[
    {
        "webSearchUrl":"https:\/\/www.bing.com\/images\/search?view=detailv2&FORM=OIIRPO&q=tropical+ocean&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&simid=608027248313960152",
        "name":"My Life in the Ocean | The greatest WordPress.com site in ...",
        "thumbnailUrl":"https:\/\/tse3.mm.bing.net\/th?id=OIP.fmwSKKmKpmZtJiBDps1kLAHaEo&pid=Api",
        "datePublished":"2017-11-03T08:51:00.0000000Z",
        "contentUrl":"https:\/\/mylifeintheocean.files.wordpress.com\/2012\/11\/tropical-ocean-wallpaper-1920x12003.jpg",
        "hostPageUrl":"https:\/\/mylifeintheocean.wordpress.com\/",
        "contentSize":"897388 B",
        "encodingFormat":"jpeg",
        "hostPageDisplayUrl":"https:\/\/mylifeintheocean.wordpress.com",
        "width":1920,
        "height":1200,
        "thumbnail":{
        "width":474,
        "height":296
        },
        "imageInsightsToken":"ccid_fmwSKKmK*mid_8607ACDACB243BDEA7E1EF78127DA931E680E3A5*simid_608027248313960152*thid_OIP.fmwSKKmKpmZtJiBDps1kLAHaEo",
        "insightsMetadata":{
        "recipeSourcesCount":0,
        "bestRepresentativeQuery":{
            "text":"Tropical Beaches Desktop Wallpaper",
            "displayText":"Tropical Beaches Desktop Wallpaper",
            "webSearchUrl":"https:\/\/www.bing.com\/images\/search?q=Tropical+Beaches+Desktop+Wallpaper&id=8607ACDACB243BDEA7E1EF78127DA931E680E3A5&FORM=IDBQDM"
        },
        "pagesIncludingCount":115,
        "availableSizesCount":44
        },
        "imageId":"8607ACDACB243BDEA7E1EF78127DA931E680E3A5",
        "accentColor":"0050B2"
    }]
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek dotyczący jednostronicowej aplikacji wyszukiwania obrazów Bing](../tutorial-bing-image-search-single-page-app.md)

* [Co to jest interfejs API wyszukiwania obrazów Bing?](../overview.md)  
* [Szczegóły cennika interfejsy API wyszukiwania Bing](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/) 
* [Dokumentacja usługi Azure Cognitive Services](../../index.yml)
* [Dokumentacja interfejsu API wyszukiwania obrazów Bing](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)