---
title: Szybki Start — wysyłanie zapytania do interfejsu API w języku Python — wyszukiwanie lokalne firmy Bing
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć korzystanie z interfejsu API wyszukiwania lokalnego firmy Bing w języku Python.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.custom: devx-track-python
ms.openlocfilehash: ff06d29c613b626c48c347628992576fc29b3a89
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430143"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Szybki Start: wysyłanie zapytania do lokalnego interfejsu API wyszukiwania biznesowego Bing w języku Python

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Enterprise Agreement, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Skorzystaj z tego przewodnika Szybki Start, aby dowiedzieć się, jak wysyłać żądania do lokalnego interfejsu API wyszukiwania biznesowego Bing, który jest usługą poznawczej platformy Azure. Chociaż ta prosta aplikacja jest zapisywana w języku Python, interfejs API jest usługą sieci Web RESTful zgodną z dowolnym językiem programowania, który umożliwia wykonywanie żądań HTTP i analizowanie kodu JSON.

Ta przykładowa aplikacja pobiera dane lokalnego odpowiedzi z interfejsu API dla zapytania wyszukiwania.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Język [Python](https://www.python.org/) 2. x lub 3. x.
* Gdy masz subskrypcję platformy Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" Utwórz zasób wyszukiwanie Bing "  target="_blank"> utwórz zasób Wyszukiwanie Bing </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu**.

## <a name="run-the-complete-application"></a>Uruchom kompletną aplikację

Poniższy przykład pobiera zlokalizowane wyniki, które są implementowane w następujących krokach:
1. Zadeklaruj zmienne, aby określić punkt końcowy na podstawie hosta i ścieżki.
2. Określ parametr zapytania. 
3. Zdefiniuj funkcję wyszukiwania, która tworzy żądanie i dodaje `Ocp-Apim-Subscription-Key` nagłówek.
4. Ustaw `Ocp-Apim-Subscription-Key` nagłówek. 
5. Utwórz połączenie i Wyślij żądanie.
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
- [Lokalne wyszukiwanie biznesowe w języku Java — Szybki Start](local-search-java-quickstart.md)
- [Lokalne wyszukiwanie biznesowe — Przewodnik Szybki Start](local-quickstart.md)
- [Lokalne wyszukiwanie biznesowe Node.js Szybki Start](local-search-node-quickstart.md)