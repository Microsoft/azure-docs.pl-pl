---
title: wyszukiwanie obrazów Bing przewodniku szybki start dla biblioteki klienta języka Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/04/2020
ms.author: aahi
ms.openlocfilehash: 338e4f0c1c47a7786e21ad98bb3e56a1505f9bb2
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104450"
---
Ten przewodnik Szybki Start umożliwia przeszukiwanie pierwszego obrazu przy użyciu biblioteki klienta wyszukiwanie obrazów Bing, która jest otoką dla interfejsu API i zawiera te same funkcje. Ta prosta aplikacja w języku Python wysyła zapytanie dotyczące wyszukania obrazu, analizuje odpowiedź w formacie JSON i wyświetla adres URL pierwszego zwróconego obrazu.

Kod źródłowy dla tego przykładu jest dostępny [w witrynie GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/image-search-quickstart.py) z dodatkową obsługą błędów i adnotacjami.

## <a name="prerequisites"></a>Wymagania wstępne

* [Python 2,7 lub 3.6 +](https://www.python.org/).

* [Biblioteka kliencka wyszukiwanie obrazów platformy Azure](https://pypi.org/project/azure-cognitiveservices-search-imagesearch/) dla języka Python
    * Zainstaluj przy użyciu polecenia `pip install azure-cognitiveservices-search-imagesearch`

[!INCLUDE [cognitive-services-bing-image-search-signup-requirements](~/includes/cognitive-services-bing-image-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>Tworzenie i inicjowanie aplikacji

1. Utwórz nowy skrypt w języku Python w ulubionym środowisku IDE lub edytorze z następującymi instrukcjami importu:

    ```python
    from azure.cognitiveservices.search.imagesearch import ImageSearchClient
    from msrest.authentication import CognitiveServicesCredentials
    ```

2. Utwórz zmienne dla klucza subskrypcji i szukanego terminu.

    ```python
    subscription_key = "Enter your key here"
    subscription_endpoint = "Enter your endpoint here"
    search_term = "canadian rockies"
    ```

## <a name="create-the-image-search-client"></a>Tworzenie klienta wyszukiwania obrazów

1. Utwórz wystąpienie klasy `CognitiveServicesCredentials` i użyj go do utworzenia wystąpienia klienta:

    ```python
    client = ImageSearchClient(endpoint=subscription_endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```
1. Wyślij zapytanie wyszukiwania do interfejsu API wyszukiwania obrazów Bing:
    ```python
    image_results = client.images.search(query=search_term)
    ```
   ## <a name="process-and-view-the-results"></a>Przetwarzanie i wyświetlanie wyników

Analizuj wyniki obrazów zwróconych w odpowiedzi.


Jeśli odpowiedź zawiera wyniki wyszukiwania, zapisz pierwszy wynik i wyświetl jego szczegóły, takie jak adres URL miniatury i oryginalny adres URL, wraz z całkowitą liczbą zwróconych obrazów.  

```python
if image_results.value:
    first_image_result = image_results.value[0]
    print("Total number of images returned: {}".format(len(image_results.value)))
    print("First image thumbnail url: {}".format(
        first_image_result.thumbnail_url))
    print("First image content url: {}".format(first_image_result.content_url))
else:
    print("No image results returned!")
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek dotyczący jednostronicowej aplikacji wyszukiwania obrazów Bing](../../tutorial-bing-image-search-single-page-app.md)

## <a name="see-also"></a>Zobacz też

* [Czym jest funkcja wyszukiwania obrazów Bing?](../../overview.md)  
* [Wypróbuj interaktywny pokaz online](https://azure.microsoft.com/services/cognitive-services/bing-image-search-api/)  
* [Przykłady dla zestawu Azure Cognitive Services SDK w języku Python](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)  
* [Dokumentacja usługi Azure Cognitive Services](../../../index.yml)
* [Dokumentacja interfejsu API wyszukiwania obrazów Bing](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)