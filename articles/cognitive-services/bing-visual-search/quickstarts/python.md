---
title: 'Szybki Start: uzyskiwanie wglądu w dane przy użyciu interfejsu API REST i języka Python — wyszukiwanie wizualne Bing'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przekazać obraz do interfejsu API wyszukiwania wizualnego Bing i uzyskać szczegółowe informacje na jego temat.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: scottwhi
ms.openlocfilehash: b56f6743b642904349797ac5b6167194f7916b45
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75446586"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-python"></a>Szybki Start: uzyskiwanie informacji o obrazach przy użyciu interfejsu API REST wyszukiwanie wizualne Bing i języka Python

Użyj tego przewodnika Szybki Start, aby wykonać pierwsze wywołanie do interfejs API wyszukiwania wizualnego Bing i wyświetlić wyniki. Ta aplikacja w języku Python przekazuje obraz do interfejsu API i wyświetla informacje, które zwraca. Chociaż ta aplikacja jest zapisywana w języku Python, interfejs API jest usługą sieci Web RESTful zgodną z większością języków programowania.

## <a name="prerequisites"></a>Wymagania wstępne

* [Python 3.x](https://www.python.org/)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicjowanie aplikacji

1. Utwórz nowy plik w języku Python w ulubionym środowisku IDE lub edytorze i Dodaj następującą `import` instrukcję:

    ```python
    import requests, json
    ```

2. Utwórz zmienne dla klucza subskrypcji, punkt końcowy i ścieżkę do obrazu, który przekazujesz. `BASE_URI`może to być globalny punkt końcowy poniżej lub niestandardowy punkt końcowy [domeny](../../../cognitive-services/cognitive-services-custom-subdomains.md) podrzędnej wyświetlany w Azure Portal dla zasobu:

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```
    
    Po przekazaniu obrazu lokalnego dane formularza muszą zawierać `Content-Disposition` nagłówek. Należy ustawić jego `name` parametr na "Image", a `filename` parametr można ustawić na dowolny ciąg. Zawartość formularza zawiera dane binarne obrazu. Maksymalny rozmiar obrazu, który można przekazać, to 1 MB.
    
    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

3. Utwórz obiekt słownika, aby przechowywać informacje nagłówka żądania. Powiąż swój klucz subskrypcji z ciągiem `Ocp-Apim-Subscription-Key`, jak pokazano poniżej:

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

4. Utwórz kolejny słownik zawierający obraz, który jest otwierany i przekazywany po wysłaniu żądania:

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>Analizowanie odpowiedzi w formacie JSON

1. Utwórz metodę o nazwie `print_json()` , aby wykonać odpowiedź interfejsu API i wydrukować kod JSON:

    ```python
    def print_json(obj):
        """Print the object as json"""
        print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
    ```

## <a name="send-the-request"></a>Wysyłanie żądania

1. Użyj polecenia `requests.post()`, aby wysłać żądanie do interfejsu API wyszukiwania wizualnego Bing. Dołącz ciąg do punktu końcowego, nagłówka i informacji o pliku. Drukuj `response.json()` przy `print_json()`użyciu:

    ```python
    try:
        response = requests.post(BASE_URI, headers=HEADERS, files=file)
        response.raise_for_status()
        print_json(response.json())
    
    except Exception as ex:
        raise ex
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie wyszukiwanie wizualne jednostronicowej aplikacji sieci Web](../tutorial-bing-visual-search-single-page-app.md)
