---
title: 'Szybki Start: wykonywanie wyszukiwania wiadomości za pomocą języka Python i interfejsu API REST wyszukiwanie wiadomości Bing'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki Start, aby wysyłać żądania do interfejs API REST wyszukiwania wiadomości Bing przy użyciu języka Python i otrzymywać odpowiedzi w formacie JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 06/16/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-python
ms.openlocfilehash: 9d73bbbfb5d6339e4bd1c6c4d38ec268b2461150
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095496"
---
# <a name="quickstart-perform-a-news-search-using-python-and-the-bing-news-search-rest-api"></a>Szybki Start: wykonywanie wyszukiwania wiadomości za pomocą języka Python i interfejsu API REST wyszukiwanie wiadomości Bing

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](https://aka.ms/cogsvcs/bingmove).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](https://aka.ms/cogsvcs/bingmigration).

Użyj tego przewodnika Szybki Start, aby wykonać pierwsze wywołanie do interfejs API wyszukiwania wiadomości Bing. Ta prosta aplikacja języka Python wysyła zapytanie wyszukiwania do interfejsu API i przetwarza wynik JSON. 

Mimo że aplikacja jest zapisywana w języku Python, interfejs API jest usługą sieci Web RESTful zgodną z większością języków programowania.

Aby uruchomić ten przykład kodu jako Notes Jupyter na [oprawie](https://mybinder.org), wybierz wskaźnik **uruchamiania spinacza** : 

[![Uruchom spinacz](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

Kod źródłowy tego przykładu jest dostępny także w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingNewsSearchv7.py).

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

Utwórz nowy plik języka Python w ulubionym środowisku IDE lub edytorze, a następnie zaimportuj moduł żądania. Utwórz zmienne dla klucza subskrypcji, punktu końcowego i terminu wyszukiwania. Możesz użyć globalnego punktu końcowego w poniższym kodzie lub użyć punktu końcowego [niestandardowej domeny](../../cognitive-services/cognitive-services-custom-subdomains.md) podrzędnej wyświetlanego w Azure Portal dla zasobu.

```python
import requests

subscription_key = "your subscription key"
search_term = "Microsoft"
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

## <a name="create-parameters-for-the-request"></a>Tworzenie parametrów dla żądania

Dodaj klucz subskrypcji do nowego słownika przy użyciu ciągu `Ocp-Apim-Subscription-Key` jako klucza. Zrób to samo dla parametrów wyszukiwania.

```python
headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
```

## <a name="send-a-request-and-get-a-response"></a>Wysyłanie żądania i odbieranie odpowiedzi

1. Użyj biblioteki żądań, aby wywołać interfejs API wyszukiwania wizualnego Bing przy użyciu klucza subskrypcji i obiektów słownika utworzonych w poprzednim kroku.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = json.dumps(response.json())
    ```

2. Uzyskaj dostęp do opisów artykułów zawartych w odpowiedzi z interfejsu API, które są przechowywane w `search_results` postaci obiektu JSON. 
    
    ```python
    descriptions = [article["description"] for article in search_results["value"]]
    ```

## <a name="display-the-results"></a>Wyświetl wyniki

Następnie opisy te można przedstawić w formie tabeli, w której słowa kluczowe wyszukiwania są wyróżnione za pomocą pogrubienia.

```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc)
                  for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](tutorial-bing-news-search-single-page-app.md)
