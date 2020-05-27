---
title: Szybki Start — wysyłanie zapytania do interfejsu API za pomocą usługi Node. js-Bing Local Business Search
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego przewodnika Szybki Start, aby rozpocząć wysyłanie żądań do lokalnego interfejsu API wyszukiwania biznesowego Bing, który jest usługą poznawczej platformy Azure.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: quickstart
ms.date: 05/12/2020
ms.author: aahi
ms.openlocfilehash: 3bb31c36e8c614a72b86f95cb7e7d1c588692f97
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873089"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Szybki Start: wysyłanie zapytania do lokalnego interfejsu API wyszukiwania biznesowego Bing przy użyciu środowiska Node. js

Skorzystaj z tego przewodnika Szybki Start, aby dowiedzieć się, jak wysyłać żądania do lokalnego interfejsu API wyszukiwania biznesowego Bing, który jest usługą poznawczej platformy Azure. Chociaż ta prosta aplikacja jest zapisywana w języku Node. js, interfejs API jest usługą sieci Web RESTful zgodną z dowolnym językiem programowania, który umożliwia wykonywanie żądań HTTP i analizowanie kodu JSON.

Ta przykładowa aplikacja pobiera dane lokalnego odpowiedzi z interfejsu API dla zapytania wyszukiwania.

## <a name="prerequisites"></a>Wymagania wstępne

* Najnowsza wersja środowiska [Node.js](https://nodejs.org/en/download/).
* [Biblioteka żądań JavaScript](https://github.com/request/request).
* [Konto interfejsu API Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) z interfejsy API wyszukiwania Bing. W tym przewodniku szybki start wystarcza [bezpłatna wersja próbna](https://azure.microsoft.com/try/cognitive-services/?api=bing-web-search-api) . Zapisz klucz interfejsu API, który jest dostarczany podczas aktywacji bezpłatnej wersji próbnej. Aby uzyskać więcej informacji, zobacz [Cognitive Services Cennik — wyszukiwanie Bing API](https://azure.microsoft.com/pricing/details/cognitive-services/search-api/).


## <a name="code-scenario"></a>Scenariusz kodu

Poniższy kod definiuje i wysyła żądanie, które jest zaimplementowane w następujących krokach:

1. Zadeklaruj zmienne, aby określić punkt końcowy na podstawie hosta i ścieżki.
2. Określ zapytanie i Dodaj parametr zapytania.
3. Utwórz funkcję procedury obsługi odpowiedzi.
4. Zdefiniuj funkcję wyszukiwania, która tworzy żądanie i dodaje `Ocp-Apim-Subscription-Key` nagłówek.
5. Uruchom funkcję Search.


```javascript
'use strict';

let https = require('https');

// Replace the subscriptionKey string value with your valid subscription key.
let subscriptionKey = 'your-access-key';

let host = 'api.cognitive.microsoft.com/bing';
let path = '/v7.0/localbusinesses/search';

let mkt = 'en-US';
let q = 'hotel in Bellevue';

let params = '?q=' + encodeURI(q) + "&mkt=" + mkt;

let response_handler = function (response) {
    let body = '';
    response.on('data', function (d) {
        body += d;
    });
    response.on('end', function () {
        let body_ = JSON.parse(body);
        let body__ = JSON.stringify(body_, null, '  ');
        console.log(body__);
    });
    response.on('error', function (e) {
        console.log('Error: ' + e.message);
    });
};

let Search = function () {
    let request_params = {
        method: 'GET',
        hostname: host,
        path: path + params,
        headers: {
            'Ocp-Apim-Subscription-Key': subscriptionKey,
        }
    };

    let req = https.request(request_params, response_handler);
    req.end();
}

Search();

```

## <a name="next-steps"></a>Następne kroki

* [Lokalne wyszukiwanie biznesowe — Przewodnik Szybki Start](local-quickstart.md)
* [Lokalne wyszukiwanie biznesowe w języku Java — Szybki Start](local-search-java-quickstart.md)
* [Lokalne wyszukiwanie biznesowe w języku Python — Szybki Start](local-search-python-quickstart.md)
