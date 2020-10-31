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
ms.openlocfilehash: 712f86eeaa49c1afe281ad5ede7a6cf2cc0ada4b
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93074984"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-python"></a>Szybki Start: uzyskiwanie informacji o obrazach przy użyciu interfejsu API REST wyszukiwanie wizualne Bing i języka Python

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](https://aka.ms/cogsvcs/bingmove).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](https://aka.ms/cogsvcs/bingmigration).

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
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
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
