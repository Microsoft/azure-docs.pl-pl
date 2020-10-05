---
title: wyszukiwanie jednostek Bing przewodniku szybki start dla biblioteki klienta języka Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2020
ms.author: aahi
ms.openlocfilehash: 0c0a124773eab8166806312dc47ded24e1cd841f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "79136760"
---
Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć wyszukiwanie jednostek przy użyciu biblioteki klienta wyszukiwanie jednostek Bing dla języka Python. Chociaż wyszukiwanie jednostek Bing ma interfejs API REST zgodny z większością języków programowania, Biblioteka klienta zapewnia łatwy sposób integracji usługi z aplikacjami. Kod źródłowy tego przykładu można znaleźć w usłudze [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/entity_search_samples.py).

## <a name="prerequisites"></a>Wymagania wstępne

* Środowisko Python [2.x lub 3.x](https://www.python.org/)

* [Zestaw SDK wyszukiwania jednostek Bing dla języka Python](https://pypi.org/project/azure-cognitiveservices-search-entitysearch/)

Zalecane jest użycie środowiska wirtualnego języka Python. Środowisko wirtualne możesz zainstalować i zainicjować przy użyciu modułu venv. Możesz zainstalować virtualenv z:

```Console
python -m venv mytestenv
```

Zainstaluj wyszukiwanie jednostek Bingą bibliotekę kliencką z:

```Console
cd mytestenv
python -m pip install azure-cognitiveservices-search-entitysearch
```

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](~/includes/cognitive-services-bing-entity-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy plik w języku Python w ulubionym środowisku IDE lub edytorze i dodaj następujące instrukcje importu. 

    ```python
    from azure.cognitiveservices.search.entitysearch import EntitySearchClient
    from azure.cognitiveservices.search.entitysearch.models import Place, ErrorResponseException
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Utwórz zmienną dla klucza subskrypcji i punktu końcowego. Utwórz wystąpienie klienta, tworząc nowy `CognitiveServicesCredentials` obiekt z kluczem.
    
    ```python
    subscription_key = "YOUR-SUBSCRIPTION-KEY"
    endpoint = "YOUR-ENDPOINT"
    client = EntitySearchclient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-a-search-request-and-receive-a-response"></a>Wysyłanie żądania wyszukiwania i odbieranie odpowiedzi

1. Wyślij żądanie wyszukiwania do wyszukiwania jednostek Bing za pomocą funkcji `client.entities.search()` zapytania wyszukiwania. 
    
    ```python
    entity_data = client.entities.search(query="Gibralter")
    ```

2. Jeśli jednostki zostały zwrócone, przekonwertuj element `entity_data.entities.value` do listy, a następnie wyświetl pierwszy wynik.
    ```python
    if entity_data.entities.value:
    
        main_entities = [entity for entity in entity_data.entities.value
                         if entity.entity_presentation_info.entity_scenario == "DominantEntity"]
    
        if main_entities:
            print(main_entities[0].description)
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](../../tutorial-bing-entities-search-single-page-app.md)

* [Czym jest interfejs API wyszukiwania jednostek Bing?](../../overview.md )