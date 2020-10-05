---
title: 'Szybki Start: uzyskiwanie wglądu w dane przy użyciu interfejsu API REST i języka Python — wyszukiwanie wizualne Bing'
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak przekazywać obraz przy użyciu interfejs API wyszukiwania wizualnego Bing i języka Python, a następnie uzyskiwać szczegółowe informacje o obrazie.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: scottwhi
ms.custom: devx-track-python
ms.openlocfilehash: ef265ab0cff9514695b40995e842518803e2e4c0
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91324590"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-python"></a>Szybki Start: uzyskiwanie informacji o obrazach przy użyciu interfejsu API REST wyszukiwanie wizualne Bing i języka Python

Użyj tego przewodnika Szybki Start, aby wykonać pierwsze wywołanie do interfejs API wyszukiwania wizualnego Bing. Ta aplikacja w języku Python przekazuje obraz do interfejsu API i wyświetla informacje, które zwraca. Mimo że aplikacja jest zapisywana w języku Python, interfejs API jest usługą sieci Web RESTful zgodną z większością języków programowania.

## <a name="prerequisites"></a>Wymagania wstępne

* [Python 3.x](https://www.python.org/)

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="initialize-the-application"></a>Inicjowanie aplikacji

1. Utwórz nowy plik w języku Python w ulubionym środowisku IDE lub edytorze i Dodaj następującą `import` instrukcję:

    ```python
    import requests, json
    ```

2. Utwórz zmienne dla klucza subskrypcji, punkt końcowy i ścieżkę do obrazu, który przekazujesz. W przypadku wartości `BASE_URI` można użyć globalnego punktu końcowego w poniższym kodzie lub użyć niestandardowego punktu końcowego [poddomeny](../../../cognitive-services/cognitive-services-custom-subdomains.md) wyświetlanego w Azure Portal dla zasobu.

    ```python

    BASE_URI = 'https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch'
    SUBSCRIPTION_KEY = 'your-subscription-key'
    imagePath = 'your-image-path'
    ```
    
3. Po przekazaniu obrazu lokalnego dane formularza muszą zawierać `Content-Disposition` nagłówek. Ustaw jej `name` parametr na "Image" i ustaw `filename` parametr na nazwę pliku obrazu. Zawartość formularza zawiera dane binarne obrazu. Maksymalny rozmiar obrazu, który można przekazać, to 1 MB.
    
    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

4. Utwórz obiekt słownika, aby przechowywać informacje nagłówka żądania. Powiąż klucz subskrypcji z ciągiem `Ocp-Apim-Subscription-Key` .

    ```python
    HEADERS = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY}
    ```

5. Utwórz kolejny słownik zawierający obraz, który jest otwierany i przekazywany po wysłaniu żądania.

    ```python
    file = {'image' : ('myfile', open(imagePath, 'rb'))}
    ```

## <a name="parse-the-json-response"></a>Analizowanie odpowiedzi w formacie JSON

Utwórz metodę o nazwie `print_json()` , aby zaakceptować odpowiedź interfejsu API i wydrukować kod JSON.

```python
def print_json(obj):
    """Print the object as json"""
    print(json.dumps(obj, sort_keys=True, indent=2, separators=(',', ': ')))
```

## <a name="send-the-request"></a>Wysyłanie żądania

Użyj polecenia `requests.post()`, aby wysłać żądanie do interfejsu API wyszukiwania wizualnego Bing. Dołącz ciąg do punktu końcowego, nagłówka i informacji o pliku. Drukuj `response.json()` przy użyciu `print_json()` .

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
