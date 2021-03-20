---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: service-bus-relay
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 05/02/2018
ms.author: clemensv
ms.custom: include file
ms.openlocfilehash: 3c18efa7eb520b765c9bb3c2aff00104f971f5a8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67183469"
---
### <a name="create-a-nodejs-application"></a>Tworzenie aplikacji Node.js

Jeśli opcja „Wymaga autoryzacji klienta” została wyłączona podczas tworzenia usługi Relay, możesz wysyłać żądania na adres połączeń hybrydowych za pomocą dowolnej przeglądarki. Aby uzyskać dostęp do chronionych punktów końcowych, musisz utworzyć i przekazać token w nagłówku `ServiceBusAuthorization` pokazanym w tym miejscu.

Aby rozpocząć, utwórz nowy plik JavaScript o nazwie `sender.js`.

### <a name="add-the-relay-npm-package"></a>Dodawanie pakietu NPM usługi Relay

Uruchom polecenie `npm install hyco-https` w wierszu polecenia języka Node w folderze projektu. Ten pakiet importuje także zwykły pakiet `https`. Po stronie klienta najważniejsza różnica polega na tym, że pakiet udostępnia funkcje do tworzenia identyfikatorów URI usługi Relay i tokenów.

### <a name="write-some-code-to-send-messages"></a>Pisanie kodu w celu wysyłania komunikatów

1. Dodaj następujący element `constants` na początku pliku `sender.js`.
   
    ```js
    const https = require('hyco-https');
    ```

2. Dodaj następujące stałe do pliku `sender.js` na potrzeby szczegółów połączenia hybrydowego. Zastąp symbole zastępcze w nawiasach wartościami uzyskanymi podczas tworzenia połączenia hybrydowego.
   
   1. `const ns` — obszar nazw usługi Relay. Pamiętaj, aby użyć w pełni kwalifikowanej nazwy obszaru nazw, na przykład `{namespace}.servicebus.windows.net`.
   2. `const path` — nazwa połączenia hybrydowego.
   3. `const keyrule` — nazwa klucza sygnatury dostępu współdzielonego.
   4. `const key` — wartość klucza sygnatury dostępu współdzielonego.

3. Dodaj następujący kod do pliku `sender.js`. Zauważysz, że kod nie różni się znacząco od kodu korzystającego z klienta HTTPS oprogramowania Node.js w zwykły sposób — dodaje tylko nagłówek autoryzacji.
   
    ```js
   https.get({
        hostname : ns,
        path : (!path || path.length == 0 || path[0] !== '/'?'/':'') + path,
        port : 443,
        headers : {
            'ServiceBusAuthorization' : 
                https.createRelayToken(https.createRelayHttpsUri(ns, path), keyrule, key)
        }
    }, (res) => {
        let error;
        if (res.statusCode !== 200) {
            console.error('Request Failed.\n Status Code: ${statusCode}');
            res.resume();
        } 
        else {
            res.setEncoding('utf8');
            res.on('data', (chunk) => {
                console.log(`BODY: ${chunk}`);
            });
            res.on('end', () => {
                console.log('No more data in response.');
            });
        };
    }).on('error', (e) => {
        console.error(`Got error: ${e.message}`);
    });
    ```
    Oto jak powinien wyglądać plik sender.js:
   
    ```js
    const https = require('hyco-https');
       
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    https.get({
        hostname : ns,
        path : (!path || path.length == 0 || path[0] !== '/'?'/':'') + path,
        port : 443,
        headers : {
            'ServiceBusAuthorization' : 
                https.createRelayToken(https.createRelayHttpsUri(ns, path), keyrule, key)
        }
    }, (res) => {
        let error;
        if (res.statusCode !== 200) {
            console.error('Request Failed.\n Status Code: ${statusCode}');
            res.resume();
        } 
        else {
            res.setEncoding('utf8');
            res.on('data', (chunk) => {
                console.log(`BODY: ${chunk}`);
            });
            res.on('end', () => {
                console.log('No more data in response.');
            });
        };
    }).on('error', (e) => {
        console.error(`Got error: ${e.message}`);
    });
    ```

