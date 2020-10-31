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
ms.date: 05/22/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-python
ms.openlocfilehash: 9d6de9b29f0e68d48eab0b2c4081470858d3d69c
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93076439"
---
# <a name="quickstart-use-python-to-call-the-bing-web-search-api"></a>Szybki start: wywoływanie interfejsu API wyszukiwania w sieci Web Bing za pomocą języka Python  

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](https://aka.ms/cogsvcs/bingmove).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Umowa Enterprise, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](https://aka.ms/cogsvcs/bingmigration).

Użyj tego przewodnika Szybki Start, aby wykonać pierwsze wywołanie do interfejs API wyszukiwania w sieci Web Bing. Ta aplikacja w języku Python wysyła żądanie wyszukiwania do interfejsu API i wyświetla odpowiedź JSON. Mimo że aplikacja jest zapisywana w języku Python, interfejs API jest usługą sieci Web RESTful zgodną z większością języków programowania.

Ten przykład działa jako notes Jupyter w usłudze [MyBinder](https://mybinder.org). Aby go uruchomić, wybierz wskaźnik uruchamiania spinacza:

[![Obiekt](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)

## <a name="prerequisites"></a>Wymagania wstępne

* [Python 2. x lub 3. x](https://www.python.org/)

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="define-variables"></a>Definiowanie zmiennych

1. Zamień wartość `subscription_key` na odpowiedni klucz subskrypcji ze swojego konta platformy Azure.

   ```python
   subscription_key = "YOUR_ACCESS_KEY"
   assert subscription_key
   ```

2. Zadeklaruj punkt końcowy interfejsu API wyszukiwania w sieci Web Bing. Możesz użyć globalnego punktu końcowego w poniższym kodzie lub użyć punktu końcowego [niestandardowej domeny](../../../cognitive-services/cognitive-services-custom-subdomains.md) podrzędnej wyświetlanego w Azure Portal dla zasobu.

   ```python
   search_url = "https://api.cognitive.microsoft.com/bing/v7.0/search"
   ```

3. Opcjonalnie możesz dostosować zapytanie wyszukiwania, zastępując wartość parametru `search_term` .

   ```python
   search_term = "Azure Cognitive Services"
   ```

## <a name="make-a-request"></a>Wysyłanie żądania

Ten kod używa `requests` biblioteki do wywoływania interfejs API wyszukiwania w sieci Web Bing i zwracania wyników jako obiektu JSON. Klucz interfejsu API jest przekazywany do słownika `headers`, a wyszukiwany termin i parametry zapytania są przekazywane do słownika `params`. 

Aby uzyskać pełną listę opcji i parametrów, zobacz [interfejs API wyszukiwania w sieci Web Bing wersji 7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference).

```python
import requests

headers = {"Ocp-Apim-Subscription-Key": subscription_key}
params = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

## <a name="format-and-display-the-response"></a>Formatowanie i wyświetlanie odpowiedzi

`search_results`Obiekt zawiera wyniki wyszukiwania oraz takie metadane jak powiązane zapytania i strony. Ten kod formatuje i wyświetla odpowiedź w przeglądarce za pomocą biblioteki `IPython.display`.

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

Aby uruchomić ten kod lokalnie, zobacz kompletny [przykład dostępny w witrynie GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingWebSearchv7.py).

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek interfejs API wyszukiwania w sieci Web Bing aplikacji jednostronicowej](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
