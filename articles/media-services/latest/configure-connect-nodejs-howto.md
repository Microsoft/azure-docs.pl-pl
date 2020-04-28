---
title: Nawiązywanie połączenia z interfejsem API programu Azure Media Services v3 — Node. js
description: W tym artykule pokazano, jak nawiązać połączenie z interfejsem API programu Media Services v3 przy użyciu środowiska Node. js.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: juliako
ms.openlocfilehash: 0381a2e2b8fd2a8b60e7cb702e0336a5678df057
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74896097"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Nawiązywanie połączenia z interfejsem API programu Media Services v3 — Node. js

W tym artykule opisano sposób nawiązywania połączenia z zestawem SDK środowiska Node. js Azure Media Services v3 przy użyciu głównej metody logowania.

## <a name="prerequisites"></a>Wymagania wstępne

- Zainstaluj program [Node. js](https://nodejs.org/en/download/).
- [Utwórz konto Media Services](create-account-cli-how-to.md). Pamiętaj, aby zapamiętać nazwę grupy zasobów i nazwę konta Media Services.

> [!IMPORTANT]
> Zapoznaj się z [konwencjami nazewnictwa](media-services-apis-overview.md#naming-conventions).

## <a name="create-packagejson"></a>Utwórz plik Package. JSON

1. Utwórz plik Package. JSON przy użyciu ulubionego edytora.
1. Otwórz plik i wklej następujący kod:

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.js",
  "dependencies": {
    "azure-arm-mediaservices": "^4.1.0",
    "azure-storage": "^2.8.0",
    "ms-rest": "^2.3.3",
    "ms-rest-azure": "^2.5.5"
  }
}
```

Należy określić następujące pakiety:

|Pakiet|Opis|
|---|---|
|`azure-arm-mediaservices`|Azure Media Services SDK. <br/>Aby upewnić się, że używasz najnowszego pakietu Azure Media Services, zaznacz pozycję [npm Install Azure-ARM-MediaServices](https://www.npmjs.com/package/azure-arm-mediaservices/).|
|`azure-storage`|Zestaw SDK magazynu. Używane podczas przekazywania plików do zasobów.|
|`ms-rest-azure`| Używane do logowania.|

Możesz uruchomić następujące polecenie, aby upewnić się, że używasz najnowszego pakietu:

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Łączenie z klientem Node. js

1. Utwórz plik js przy użyciu ulubionego edytora.
1. Otwórz plik i wklej następujący kod.
1. Ustaw wartości w sekcji "Konfiguracja punktu końcowego" na wartości, które pochodzą z [interfejsów API dostępu](access-api-cli-how-to.md).

```js
'use strict';

const MediaServices = require('azure-arm-mediaservices');
const msRestAzure = require('ms-rest-azure');
const msRest = require('ms-rest');
const azureStorage = require('azure-storage');

// endpoint config
// make sure your URL values end with '/'
const armAadAudience = "";
const aadEndpoint = "";
const armEndpoint = "";
const subscriptionId = "";
const accountName = "";
const region = "";
const aadClientId = "";
const aadSecret = "";
const aadTenantId = "";
const resourceGroup = "";

let azureMediaServicesClient;

///////////////////////////////////////////
//     Entrypoint for sample script      //
///////////////////////////////////////////

msRestAzure.loginWithServicePrincipalSecret(aadClientId, aadSecret, aadTenantId, {
  environment: {
    activeDirectoryResourceId: armAadAudience,
    resourceManagerEndpointUrl: armEndpoint,
    activeDirectoryEndpointUrl: aadEndpoint
  }
}, async function(err, credentials, subscriptions) {
    if (err) return console.log(err);
    azureMediaServicesClient = new MediaServices(credentials, subscriptionId, armEndpoint, { noRetryPolicy: true });
    
    console.log("connected");

});
```

## <a name="run-your-app"></a>Uruchamianie aplikacji

Otwórz wiersz polecenia. Przejdź do katalogu przykładowego i wykonaj następujące polecenia:

```
npm install 
node index.js
```

## <a name="see-also"></a>Zobacz także

- [Koncepcje Media Services](concepts-overview.md)
- [Instalacja menedżera NPM — azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/)

## <a name="next-steps"></a>Następne kroki

Przeanalizuj [dokumentację oprogramowania Node.js](/javascript/api/overview/azure/mediaservices/management) usługi Media Services i zapoznaj się z [przykładami](https://github.com/Azure-Samples/media-services-v3-node-tutorials) pokazującymi, jak używać interfejsu API usługi Media Services przy użyciu środowiska node.js.

