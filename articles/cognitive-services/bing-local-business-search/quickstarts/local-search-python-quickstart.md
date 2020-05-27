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
ms.openlocfilehash: 3a90d5455c0664ceabf80647fc94a37ad0c716b5
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873028"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>Szybki Start: wysyłanie zapytania do lokalnego interfejsu API wyszukiwania biznesowego Bing w języku Python

Skorzystaj z tego przewodnika Szybki Start, aby dowiedzieć się, jak wysyłać żądania do lokalnego interfejsu API wyszukiwania biznesowego Bing, który jest usługą poznawczej platformy Azure. Chociaż ta prosta aplikacja jest zapisywana w języku Python, interfejs API jest usługą sieci Web RESTful zgodną z dowolnym językiem programowania, który umożliwia wykonywanie żądań HTTP i analizowanie kodu JSON.

Ta przykładowa aplikacja pobiera dane lokalnego odpowiedzi z interfejsu API dla zapytania wyszukiwania.

## <a name="prerequisites"></a>Wymagania wstępne

* Język [Python](https://www.python.org/) 2. x lub 3. x.
* [Konto interfejsu API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z interfejsy API wyszukiwania Bing. W tym przewodniku szybki start wystarcza [bezpłatna wersja próbna](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) . Zapisz klucz interfejsu API, który jest dostarczany podczas aktywacji bezpłatnej wersji próbnej. Aby uzyskać więcej informacji, zobacz [Cognitive Services Cennik — wyszukiwanie Bing API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).

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
- [Lokalne wyszukiwanie biznesowe Node. js — Szybki Start](local-search-node-quickstart.md)
