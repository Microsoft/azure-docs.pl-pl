---
title: Nawiązywanie połączenia z interfejsem API Azure Media Services v3 — Node.js
description: W tym artykule pokazano, jak nawiązać połączenie z interfejsem API programu Media Services v3 przy użyciu Node.js.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: c6ea238edd68413646dda59b22d1c0dc2557d57e
ms.sourcegitcommit: f6236e0fa28343cf0e478ab630d43e3fd78b9596
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94916835"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Nawiązywanie połączenia z interfejsem API Media Services v3 — Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

W tym artykule opisano sposób nawiązywania połączenia z zestawem SDK node.js Azure Media Services v3 przy użyciu metody logowania jednostki usługi.

## <a name="prerequisites"></a>Wymagania wstępne

- Zainstaluj [Node.js](https://nodejs.org/en/download/).
- [Utwórz konto Media Services](./create-account-howto.md). Pamiętaj, aby zapamiętać nazwę grupy zasobów i nazwę konta Media Services.

> [!IMPORTANT]
> Zapoznaj się z [konwencjami nazewnictwa](media-services-apis-overview.md#naming-conventions).

## <a name="create-packagejson"></a>Utwórz package.jsna

1. Utwórz package.jsw pliku przy użyciu ulubionego edytora.
1. Otwórz plik i wklej następujący kod:

   Upewnij się, że zapoznaj się z najnowszą wersją [zestawu AzureMediaServices SDK dla języka JavaScript](https://www.npmjs.com/package/@azure/arm-mediaservices).

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.js",
  "dependencies": {
    "azure-arm-mediaservices": "^8.0.0",
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

## <a name="connect-to-nodejs-client"></a>Nawiązywanie połączenia z klientem Node.js

1. Utwórz plik js przy użyciu ulubionego edytora.
1. Otwórz plik i wklej następujący kod.
1. Ustaw wartości w sekcji "Konfiguracja punktu końcowego" na wartości, które pochodzą z [interfejsów API dostępu](./access-api-howto.md).

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
