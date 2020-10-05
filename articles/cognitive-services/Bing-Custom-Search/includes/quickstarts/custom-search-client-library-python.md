---
title: wyszukiwanie niestandardowe Bing przewodniku szybki start dla biblioteki klienta języka Python
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.author: aahi
ms.openlocfilehash: ec0ffdcf86e67a7126a3100c1e20b6e5c3474e35
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "78252884"
---
Rozpocznij pracę z biblioteką klienta wyszukiwanie niestandardowe Bing dla języka Python. Wykonaj następujące kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań. Interfejs API wyszukiwania niestandardowego Bing umożliwia tworzenie dostosowanych, bezpłatnych funkcji wyszukiwania w usłudze AD dla interesujących Cię tematów. Kod źródłowy dla tego przykładu można znaleźć w witrynie [GitHub](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/blob/master/samples/search/custom_search_samples.py).

Użyj biblioteki klienta wyszukiwanie niestandardowe Bing dla języka Python, aby:
* Znajdź wyniki wyszukiwania w sieci Web w wystąpieniu wyszukiwanie niestandardowe Bing.

[Dokumentacja](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/customsearch?view=azure-python)  |  referencyjna [Kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-customsearch)  |  źródłowy biblioteki [Pakiet (PyPi)](https://pypi.org/project/azure-cognitiveservices-search-customsearch/)  |  [Przykłady](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/)


## <a name="prerequisites"></a>Wymagania wstępne

- Wystąpienie wyszukiwania niestandardowego Bing. Aby uzyskać więcej informacji, zobacz [Szybki Start: Tworzenie pierwszego wystąpienia wyszukiwanie niestandardowe Bing](../../quick-start.md) .
- Środowisko Python [2.x lub 3.x](https://www.python.org/) 
- [Zestaw wyszukiwanie niestandardowe Bing SDK dla języka Python](https://pypi.org/project/azure-cognitiveservices-search-customsearch/) 

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="install-the-python-client-library"></a>Zainstaluj bibliotekę kliencką języka Python

Zainstaluj bibliotekę kliencką wyszukiwanie niestandardowe Bing przy użyciu następującego polecenia.

```Console
python -m pip install azure-cognitiveservices-search-customsearch
```


## <a name="create-a-new-application"></a>Tworzenie nowej aplikacji

Utwórz nowy plik w języku Python w ulubionym edytorze lub w środowisku IDE i Dodaj następujące Importy.

```python
from azure.cognitiveservices.search.customsearch import CustomSearchClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="create-a-search-client-and-send-a-request"></a>Tworzenie klienta wyszukiwania i wysyłanie żądania

1. Utwórz zmienną dla klucza subskrypcji i punktu końcowego.

    ```python
    subscription_key = 'your-subscription-key'
    endpoint = 'your-endpoint'
    ```

2. Utwórz wystąpienie `CustomSearchClient` , używając `CognitiveServicesCredentials` obiektu z kluczem subskrypcji. 

    ```python
    client = CustomSearchClient(endpoint=endpoint, credentials=CognitiveServicesCredentials(subscription_key))
    ```

3. Wyślij żądanie wyszukiwania za pomocą `client.custom_instance.search()` . Dołącz termin wyszukiwania do `query` parametru i ustaw `custom_config` na swój niestandardowy identyfikator konfiguracji, aby użyć Twojego wystąpienia wyszukiwania. Możesz uzyskać identyfikator z [portalu wyszukiwanie niestandardowe Bing](https://www.customsearch.ai/), klikając kartę **produkcja** .

    ```python
    web_data = client.custom_instance.search(query="xbox", custom_config="your-configuration-id")
    ```

## <a name="view-the-search-results"></a>Wyświetlanie wyników wyszukiwania

Jeśli znaleziono jakiekolwiek wyniki wyszukiwania stron sieci Web, Pobierz pierwszy z nich i wydrukuj jego nazwę, adres URL i łączną liczbę znalezionych stron sieci Web.

```python
if web_data.web_pages.value:
    first_web_result = web_data.web_pages.value[0]
    print("Web Pages result count: {}".format(len(web_data.web_pages.value)))
    print("First Web Page name: {}".format(first_web_result.name))
    print("First Web Page url: {}".format(first_web_result.url))
else:
    print("Didn't see any web data..")
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie aplikacji internetowej z funkcją wyszukiwania niestandardowego](../../tutorials/custom-search-web-page.md)
