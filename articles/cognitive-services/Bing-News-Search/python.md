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
ms.date: 12/12/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 1c424c75a4df193ec412355607c68abeda0560a5
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75448491"
---
# <a name="quickstart-perform-a-news-search-using-python-and-the-bing-news-search-rest-api"></a>Szybki Start: wykonywanie wyszukiwania wiadomości za pomocą języka Python i interfejsu API REST wyszukiwanie wiadomości Bing

Ten przewodnik Szybki start umożliwi Ci utworzenie pierwszego wywołania do interfejsu API wyszukiwania wiadomości Bing i odebranie odpowiedzi JSON. Ta prosta aplikacja JavaScript wysyła zapytanie wyszukiwania do interfejsu API i przetwarza wyniki. Mimo że ta aplikacja jest napisana w języku Python, interfejs API jest usługą internetową zgodną ze standardem RESTful i większością języków programowania.

Ten przykład kodu można uruchomić jako notes programu Jupyter w witrynie [MyBinder](https://mybinder.org). W tym celu należy kliknąć znaczek Launch Binder: 

[![Obiekt](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

Kod źródłowy tego przykładu jest dostępny także w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingNewsSearchv7.py).

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy plik języka Python w ulubionym środowisku IDE lub edytorze, a następnie zaimportuj moduł żądania. Utwórz zmienne dla klucza subskrypcji, punktu końcowego i terminu wyszukiwania. Możesz użyć poniższego globalnego punktu końcowego lub niestandardowego punktu końcowego [poddomeny](../../cognitive-services/cognitive-services-custom-subdomains.md) , który jest wyświetlany w Azure Portal dla zasobu.

```python
import requests

subscription_key = "your subscription key"
search_term = "Microsoft"
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

### <a name="create-parameters-for-the-request"></a>Tworzenie parametrów dla żądania

1. Dodaj klucz subskrypcji do nowego słownika przy użyciu ciągu `"Ocp-Apim-Subscription-Key"` jako klucza. Zrób to samo dla parametrów wyszukiwania.

    ```python
    headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
    params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
    ```

## <a name="send-a-request-and-get-a-response"></a>Wysyłanie żądania i odbieranie odpowiedzi

1. Użyj biblioteki żądań, aby wywołać interfejs API wyszukiwania wizualnego Bing przy użyciu klucza subskrypcji i obiektów słownika utworzonych w ostatnim kroku.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = response.json()
    ```

2. Element `search_results` zawiera odpowiedź z interfejsu API w postaci obiektu JSON. Masz dostęp do opisów artykułów zawartych w odpowiedzi.
    
    ```python
    descriptions = [article["description"] for article in search_results["value"]]
    ```

## <a name="displaying-the-results"></a>Wyświetlanie wyników

Następnie opisy te można przedstawić w formie tabeli, w której słowa kluczowe wyszukiwania są wyróżnione za pomocą **pogrubienia**.

```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc)
                  for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](tutorial-bing-news-search-single-page-app.md)
