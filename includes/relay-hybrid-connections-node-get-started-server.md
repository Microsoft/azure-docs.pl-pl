---
author: clemensv
ms.service: service-bus-relay
ms.topic: include
ms.date: 11/09/2018
ms.author: clemensv
ms.openlocfilehash: 531866ece1c4acacb926c9e9c6598158c1aa077f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "67183821"
---
### <a name="create-a-nodejs-application"></a>Tworzenie aplikacji Node.js

Utwórz plik JavaScript o nazwie `listener.js`.

### <a name="add-the-relay-npm-package"></a>Dodawanie pakietu NPM usługi Relay

Uruchom polecenie `npm install hyco-ws` w wierszu polecenia języka Node w folderze projektu.

### <a name="write-some-code-to-receive-messages"></a>Pisanie kodu w celu odbierania komunikatów

1. Dodaj następującą stałą na początku pliku `listener.js`.
   
    ```js
    const WebSocket = require('hyco-ws');
    ```
2. Dodaj następujące stałe do pliku `listener.js` na potrzeby szczegółów połączenia hybrydowego. Zastąp symbole zastępcze w nawiasach wartościami uzyskanymi podczas tworzenia połączenia hybrydowego.
   
   1. `const ns` — obszar nazw usługi Relay. Pamiętaj, aby użyć w pełni kwalifikowanej nazwy obszaru nazw, na przykład `{namespace}.servicebus.windows.net`.
   2. `const path` — nazwa połączenia hybrydowego.
   3. `const keyrule` — nazwa klucza sygnatury dostępu współdzielonego.
   4. `const key` — wartość klucza sygnatury dostępu współdzielonego.

3. Dodaj następujący kod do pliku `listener.js`:
   
    ```js
    var wss = WebSocket.createRelayedServer(
    {
        server : WebSocket.createRelayListenUri(ns, path),
        token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
    }, 
    function (ws) {
        console.log('connection accepted');
        ws.onmessage = function (event) {
            console.log(event.data);
        };
        ws.on('close', function () {
            console.log('connection closed');
        });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```
    Plik listener.js powinien wyglądać w następujący sposób:
   
    ```js
    const WebSocket = require('hyco-ws');
   
    const ns = "{RelayNamespace}";
    const path = "{HybridConnectionName}";
    const keyrule = "{SASKeyName}";
    const key = "{SASKeyValue}";
   
    var wss = WebSocket.createRelayedServer(
        {
            server : WebSocket.createRelayListenUri(ns, path),
            token: WebSocket.createRelayToken('http://' + ns, keyrule, key)
        }, 
        function (ws) {
            console.log('connection accepted');
            ws.onmessage = function (event) {
                console.log(event.data);
            };
            ws.on('close', function () {
                console.log('connection closed');
            });       
    });
   
    console.log('listening');
   
    wss.on('error', function(err) {
        console.log('error' + err);
    });
    ```

