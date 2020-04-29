---
title: 'Szybki start: przeprowadzanie wyszukiwania za pomocą języka Python — interfejs API wyszukiwania w sieci Web Bing'
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki Start, aby wysyłać żądania do interfejsu API wyszukiwania w sieci Web Bing przy użyciu języka Python i otrzymywać odpowiedzi w formacie JSON.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 12/09/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: c1af1142faca76cc58b6b3ca9a7106bc0433ea18
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74976370"
---
# <a name="quickstart-use-python-to-call-the-bing-web-search-api"></a>Szybki start: wywoływanie interfejsu API wyszukiwania w sieci Web Bing za pomocą języka Python  

Ten przewodnik Szybki start umożliwi Ci utworzenie Twojego pierwszego wywołania interfejsu API wyszukiwania w Internecie Bing i odebranie odpowiedzi JSON. Ta aplikacja w języku Python wysyła żądanie wyszukiwania do interfejsu API i wyświetla odpowiedź. Chociaż ta aplikacja jest napisana w języku Python, interfejs API jest usługą internetową zgodną z wzorcem REST i większością języków programowania.

Ten przykład działa jako notes Jupyter w usłudze [MyBinder](https://mybinder.org). Wybierz wskaźnik uruchamiania spinacza:

[![Obiekt](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)

## <a name="prerequisites"></a>Wymagania wstępne

* [Python 2. x lub 3. x](https://www.python.org/)

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="define-variables"></a>Definiowanie zmiennych

Zamień wartość `subscription_key` na odpowiedni klucz subskrypcji ze swojego konta platformy Azure.

```python
subscription_key = "YOUR_ACCESS_KEY"
assert subscription_key
```

Zadeklaruj punkt końcowy interfejsu API wyszukiwania w sieci Web Bing. Możesz użyć poniższego globalnego punktu końcowego lub niestandardowego punktu końcowego [poddomeny](../../../cognitive-services/cognitive-services-custom-subdomains.md) , który jest wyświetlany w Azure Portal dla zasobu.

```python
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
```

Możesz dostosować zapytanie wyszukiwania, zamieniając wartość `search_term`.

```python
search_term = "Azure Cognitive Services"
```

## <a name="make-a-request"></a>Wysyłanie żądania

W tym bloku za pomocą biblioteki `requests` wywoływany jest interfejs API wyszukiwania w sieci Web Bing i zwracane są wyniki w postaci obiektu JSON. Klucz interfejsu API jest przekazywany do słownika `headers`, a wyszukiwany termin i parametry zapytania są przekazywane do słownika `params`. Aby uzyskać pełną listę opcji i parametrów, zobacz dokumentację [interfejsu API wyszukiwania w sieci Web Bing w wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference).

```python
import requests

headers = {"Ocp-Apim-Subscription-Key": subscription_key}
params = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

## <a name="format-and-display-the-response"></a>Formatowanie i wyświetlanie odpowiedzi

`search_results` Obiekt zawiera wyniki wyszukiwania i metadane, takie jak powiązane zapytania i strony. Ten kod formatuje i wyświetla odpowiedź w przeglądarce za pomocą biblioteki `IPython.display`.

```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"], v["name"], v["snippet"])
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="sample-code-on-github"></a>Kod przykładowy w witrynie GitHub

Jeśli chcesz uruchomić ten kod lokalnie, pełny [przykład jest dostępny w witrynie GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingWebSearchv7.py).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek dotyczący jednostronicowej aplikacji wyszukiwania w sieci Web Bing](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
