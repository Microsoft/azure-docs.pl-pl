---
title: Szybki start — wysyłanie zapytania do interfejsu API w języku Python — Wyszukiwanie lokalnych firm Bing
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki start, aby rozpocząć korzystanie z Wyszukiwanie lokalnych firm Bing API w języku Python.
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.date: 05/12/2020
ms.topic: quickstart
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.custom:
- devx-track-python
- mode-api
ms.openlocfilehash: adbf3d9abddf01ba67046cfa433ffd46f713ff83
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536671"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Szybki start: wysyłanie zapytania do interfejsu API Wyszukiwanie lokalnych firm Bing Python

> [!WARNING]
> Wyszukiwanie Bing API są przechodnie z Cognitive Services do Wyszukiwanie Bing Services. Od **30 października 2020** r. wszystkie nowe wystąpienia usługi Wyszukiwanie Bing należy aprowizować zgodnie z procesem [udokumentowanym tutaj.](/bing/search-apis/bing-web-search/create-bing-search-service-resource)
> Wyszukiwanie Bing aprowowane przy użyciu Cognitive Services będą obsługiwane przez następne trzy lata lub do końca Enterprise Agreement, w zależności od tego, co nastąpi najpierw.
> Aby uzyskać instrukcje dotyczące migracji, [zobacz Wyszukiwanie Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Skorzystaj z tego przewodnika Szybki start, aby dowiedzieć się, jak wysyłać żądania do interfejsu API usługi Wyszukiwanie lokalnych firm Bing, który jest usługą Azure Cognitive Service. Chociaż ta prosta aplikacja jest napisana w języku Python, interfejs API jest usługą internetową zgodną ze standardem RESTful i dowolnym językiem programowania umożliwiającym wykonywanie żądań HTTP i analizowanie danych JSON.

Ta przykładowa aplikacja pobiera lokalne dane odpowiedzi z interfejsu API dla zapytania wyszukiwania.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [tworzenie bezpłatnej subskrypcji](https://azure.microsoft.com/free/cognitive-services/)
* [Python](https://www.python.org/) 2.x lub 3.x.
* Po utworzeniu subskrypcji platformy Azure utwórz zasób Wyszukiwanie Bing utwórz zasób usługi Wyszukiwanie Bing w witrynie Azure Portal, aby uzyskać <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" "  target="_blank"> klucz i punkt </a> końcowy. Po wdrożeniu kliknij pozycję **Przejdź do zasobu**.

## <a name="run-the-complete-application"></a>Uruchamianie kompletnej aplikacji

Poniższy przykład pobiera zlokalizowane wyniki, które są implementowane w następujących krokach:
1. Zadeklaruj zmienne, aby określić punkt końcowy na podstawie hosta i ścieżki.
2. Określ parametr zapytania. 
3. Zdefiniuj funkcję wyszukiwania, która tworzy żądanie i dodaje `Ocp-Apim-Subscription-Key` nagłówek .
4. Ustaw `Ocp-Apim-Subscription-Key` nagłówek . 
5. Nawiązaniu połączenia i wysłaniu żądania.
6. Wydrukuj wyniki w formacie JSON.

Pełny kod dla tej wersji demonstracyjnej jest następujący:

```python
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.

> [!WARNING]
> Bing Search APIs are moving from Cognitive Services to Bing Search Services. Starting **October 30, 2020**, any new instances of Bing Search need to be provisioned following the process documented [here](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Bing Search APIs provisioned using Cognitive Services will be supported for the next three years or until the end of your Enterprise Agreement, whichever happens first.
> For migration instructions, see [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'api.cognitive.microsoft.com'
path = '/bing/v7.0/localbusinesses/search'

query = 'restaurant in Bellevue'

params = '?q=' + urllib.parse.quote (query) + '&mkt=en-us'

def get_local():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_local()
print (json.dumps(json.loads(result), indent=4))

```

## <a name="next-steps"></a>Następne kroki
- [Przewodnik Szybki start dla języka Java wyszukiwania lokalnych firm](local-search-java-quickstart.md)
- [Przewodnik Szybki start dla języka C# dla funkcji wyszukiwania lokalnych firm](local-quickstart.md)
- [Przewodnik Szybki start dla Node.js wyszukiwania lokalnych firm](local-search-node-quickstart.md)
