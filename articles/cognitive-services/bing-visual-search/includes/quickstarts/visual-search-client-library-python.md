---
title: wyszukiwanie wizualne Bing przewodniku szybki start dla biblioteki klienta języka Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/26/2020
ms.author: aahi
ms.openlocfilehash: debc5130824a0f661a3736b8d24d950fc789f2f3
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "80550002"
---
Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć pobieranie szczegółowych informacji o obrazach z usługi wyszukiwanie wizualne Bing przy użyciu biblioteki klienta języka Python. Chociaż wyszukiwanie wizualne Bing ma interfejs API REST zgodny z większością języków programowania, Biblioteka klienta zapewnia łatwy sposób integracji usługi z aplikacjami. Kod źródłowy dla tego przykładu można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/visual_search_samples.py) 

[Dokumentacja](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/visualsearch?view=azure-python)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-visualsearch)  |  źródłowy biblioteki [Pakiet (PyPi)](https://pypi.org/project/azure-cognitiveservices-search-visualsearch/)  |  [Przykłady](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/)

## <a name="prerequisites"></a>Wymagania wstępne

* Język [Python](https://www.python.org/) 2. x lub 3. x
* Zalecane jest użycie [środowiska wirtualnego](https://docs.python.org/3/tutorial/venv.html). Zainstaluj i zainicjuj środowisko wirtualne przy użyciu [modułu venv](https://pypi.python.org/pypi/virtualenv).
* Biblioteka klienta wyszukiwanie wizualne Bing dla języka Python. Możesz go zainstalować za pomocą następujących poleceń:
    1. `cd mytestenv`
    2. `python -m pip install azure-cognitiveservices-search-visualsearch`

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](~/includes/cognitive-services-bing-visual-search-signup-requirements.md)]


## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy plik w języku Python w ulubionym środowisku IDE lub edytorze i dodaj następujące instrukcje importu. 

    ```python
    import http.client, urllib.parse
    import json
    import os.path
    from azure.cognitiveservices.search.visualsearch import VisualSearchClient
    from azure.cognitiveservices.search.visualsearch.models import (
        VisualSearchRequest,
        CropArea,
        ImageInfo,
        Filters,
        KnowledgeRequest,
    )
    from msrest.authentication import CognitiveServicesCredentials
    ```
2. Utwórz zmienne dla klucza subskrypcji, identyfikatora konfiguracji niestandardowej i obrazu, który chcesz przekazać. 
    
    ```python
    subscription_key = 'YOUR-VISUAL-SEARCH-ACCESS-KEY'
    PATH = 'C:\\Users\\USER\\azure-cognitive-samples\\mytestenv\\TestImages\\'
    image_path = os.path.join(PATH, "image.jpg")
    
    ```

3. Tworzenie wystąpienia klienta

    ```python
    client = VisualSearchClient(endpoint="https://api.cognitive.microsoft.com", credentials=CognitiveServicesCredentials(subscription_key))
    ```

## <a name="send-the-search-request"></a>Wysyłanie żądania wyszukiwania

1. Mając otwarty plik obrazu, zserializuj funkcję `VisualSearchRequest()` i przekaż ją jako parametr `knowledge_request` dla funkcji `visual_search()`.

    ```python
    with open(image_path, "rb") as image_fd:
        # You need to pass the serialized form of the model
        knowledge_request = json.dumps(VisualSearchRequest().serialize())
    
        print("\r\nSearch visual search request with binary of dog image")
        result = client.images.visual_search(image=image_fd, knowledge_request=knowledge_request)
    ```

2. Jeśli zostaną zwrócone jakiekolwiek wyniki, wydrukuj te wyniki, tagi oraz akcje w pierwszym tagu.

    ```python
    if not result:
            print("No visual search result data.")
    
            # Visual Search results
        if result.image.image_insights_token:
            print("Uploaded image insights token: {}".format(result.image.image_insights_token))
        else:
            print("Couldn't find image insights token!")
    
        # List of tags
        if result.tags:
            first_tag = result.tags[0]
            print("Visual search tag count: {}".format(len(result.tags)))
    
            # List of actions in first tag
            if first_tag.actions:
                first_tag_action = first_tag.actions[0]
                print("First tag action count: {}".format(len(first_tag.actions)))
                print("First tag action type: {}".format(first_tag_action.action_type))
            else:
                print("Couldn't find tag actions!")
        else:
            print("Couldn't find image tags!")
    ```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie jednostronicowej aplikacji internetowej](../../tutorial-bing-visual-search-single-page-app.md)
