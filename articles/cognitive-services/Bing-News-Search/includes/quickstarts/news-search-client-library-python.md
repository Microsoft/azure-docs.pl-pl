---
title: wyszukiwanie wiadomości Bing przewodniku szybki start dla biblioteki klienta języka Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/12/2020
ms.author: aahi
ms.openlocfilehash: c1bd0d86a3fd9d19d67d84b9b05955421373e01e
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "79503881"
---
Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć wyszukiwanie wiadomości za pomocą biblioteki klienta wyszukiwanie wiadomości Bing dla języka Python. Chociaż wyszukiwanie wiadomości Bing ma interfejs API REST zgodny z większością języków programowania, Biblioteka klienta zapewnia łatwy sposób integracji usługi z aplikacjami. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/news_search_samples.py).

## <a name="prerequisites"></a>Wymagania wstępne

* Język [Python](https://www.python.org/) 2. x lub 3. x

Zalecane jest użycie [środowiska wirtualnego](https://docs.python.org/3/tutorial/venv.html) dla programowania w języku python. Środowisko wirtualne możesz zainstalować i zainicjować przy użyciu [modułu venv](https://pypi.python.org/pypi/virtualenv). Musi być zainstalowany moduł virtualenv dla języka Python 2.7. Środowisko wirtualne można utworzyć w następujący sposób:

```console
python -m venv mytestenv
```

Można zainstalować zależności biblioteki klienta wyszukiwanie wiadomości Bing za pomocą tego polecenia:
    
```console
python -m pip install azure-cognitiveservices-search-newssearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy plik kodu Python w ulubionym środowisku IDE lub edytorze i zaimportuj poniższe biblioteki. Utwórz zmienną dla klucza subskrypcji i termin wyszukiwania.

    ```python
    from azure.cognitiveservices.search.newssearch import NewsSearchClient
    from msrest.authentication import CognitiveServicesCredentials
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    endpoint = "YOUR-ENDPOINT"
    search_term = "Quantum Computing"
    ```

## <a name="initialize-the-client-and-send-a-request"></a>Inicjowanie klienta i wysyłanie żądania

1. Utwórz wystąpienie elementu `CognitiveServicesCredentials`.
    
    ```python
    client = NewsSearchClient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

2. Wyślij zapytanie wyszukiwania do interfejsu API wyszukiwania wiadomości i zapisz odpowiedź.

    ```python
    news_result = client.news.search(query=search_term, market="en-us", count=10)
    ```

## <a name="parse-the-response"></a>Analizowanie odpowiedzi

Jeśli zostaną zwrócone jakieś wyniki wyszukiwania, wydrukuj pierwszą stronę internetową wyników:

```python
if news_result.value:
    first_news_result = news_result.value[0]
    print("Total estimated matches value: {}".format(
        news_result.total_estimated_matches))
    print("News result count: {}".format(len(news_result.value)))
    print("First news name: {}".format(first_news_result.name))
    print("First news url: {}".format(first_news_result.url))
    print("First news description: {}".format(first_news_result.description))
    print("First published time: {}".format(first_news_result.date_published))
    print("First news provider: {}".format(first_news_result.provider[0].name))
else:
    print("Didn't see any news result data..")
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](../../tutorial-bing-news-search-single-page-app.md)
