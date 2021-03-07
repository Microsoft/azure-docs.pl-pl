---
title: Szybki Start — wysyłanie zapytania do interfejsu API przy użyciu lokalnego wyszukiwania biznesowego Node.js-Bing
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
ms.custom: devx-track-js
ms.openlocfilehash: 35d6aa7d606248796b3405d8fe1dabd64e0996d5
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102430109"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-using-nodejs"></a>Szybki Start: wysyłanie zapytania do lokalnego interfejsu API wyszukiwania biznesowego Bing przy użyciu Node.js

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Enterprise Agreement, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Skorzystaj z tego przewodnika Szybki Start, aby dowiedzieć się, jak wysyłać żądania do lokalnego interfejsu API wyszukiwania biznesowego Bing, który jest usługą poznawczej platformy Azure. Mimo że prosta aplikacja jest zapisywana w Node.js, interfejs API jest usługą sieci Web RESTful zgodną z dowolnym językiem programowania, który umożliwia wykonywanie żądań HTTP i analizowanie kodu JSON.

Ta przykładowa aplikacja pobiera dane lokalnego odpowiedzi z interfejsu API dla zapytania wyszukiwania.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* Najnowsza wersja środowiska [Node.js](https://nodejs.org/en/download/).
* [Biblioteka żądań JavaScript](https://github.com/request/request).
* Gdy masz subskrypcję platformy Azure, <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title=" Utwórz zasób wyszukiwanie Bing "  target="_blank"> utwórz zasób Wyszukiwanie Bing </a> w Azure Portal, aby uzyskać klucz i punkt końcowy. Po wdrożeniu programu kliknij pozycję **Przejdź do zasobu**.


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