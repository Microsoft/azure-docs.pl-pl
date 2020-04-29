---
title: 'Szybki Start: wywoływanie punktu końcowego wyszukiwanie niestandardowe Bing przy użyciu języka Python | Microsoft Docs'
titleSuffix: Azure Cognitive Services
description: Użyj tego przewodnika Szybki start, aby rozpocząć żądanie wyników wyszukiwania z wystąpienia wyszukiwania niestandardowego Bing w języku Python
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.openlocfilehash: a601b309d18e489f6b631cb26e5f3e13ef790b42
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80238827"
---
# <a name="quickstart-call-your-bing-custom-search-endpoint-using-python"></a>Szybki Start: wywoływanie punktu końcowego wyszukiwanie niestandardowe Bing przy użyciu języka Python

Użyj tego przewodnika Szybki start, aby rozpocząć żądanie wyników z wystąpienia wyszukiwania niestandardowego Bing. Chociaż ta aplikacja jest napisana w języku Python, interfejs API wyszukiwania niestandardowego Bing jest usługą internetową RESTful zgodną z większością języków programowania. Kod źródłowy dla tego przykładu jest dostępny w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingCustomSearchv7.py).

## <a name="prerequisites"></a>Wymagania wstępne

- Wystąpienie wyszukiwania niestandardowego Bing. Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie pierwszego wystąpienia wyszukiwanie niestandardowe Bing](quick-start.md) .
- Język [Python](https://www.python.org/) 2. x lub 3. x

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy plik w języku Python w ulubionym środowisku IDE lub edytorze i dodaj następujące instrukcje importu. Utwórz zmienne dla swojego klucza subskrypcji, identyfikator konfiguracji niestandardowej i termin wyszukiwania. 

    ```python
    import json
    import requests
    
    subscriptionKey = "YOUR-SUBSCRIPTION-KEY"
    customConfigId = "YOUR-CUSTOM-CONFIG-ID"
    searchTerm = "microsoft"
    ```

## <a name="send-and-receive-a-search-request"></a>Wysyłanie i odbieranie żądania wyszukiwania 

1. Skonstruuj adres URL żądania, dodając termin wyszukiwania do parametru zapytania `q=` oraz identyfikator konfiguracji niestandardowej wystąpienia wyszukiwania do parametru `customconfig=`. Oddziel parametry za pomocą znaku `&`. Możesz użyć poniższego globalnego punktu końcowego lub niestandardowego punktu końcowego [poddomeny](../../cognitive-services/cognitive-services-custom-subdomains.md) , który jest wyświetlany w Azure Portal dla zasobu.

    ```python
    url = 'https://api.cognitive.microsoft.com/bingcustomsearch/v7.0/search?' + 'q=' + searchTerm + '&' + 'customconfig=' + customConfigId
    ```

2. Wyślij żądanie do swojego wystąpienia wyszukiwania niestandardowego Bing i wyświetl zwrócone wyniki wyszukiwania.  

    ```python
    r = requests.get(url, headers={'Ocp-Apim-Subscription-Key': subscriptionKey})
    print(r.text)
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji internetowej z funkcją wyszukiwania niestandardowego](./tutorials/custom-search-web-page.md)
