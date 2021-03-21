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
ms.date: 02/17/2021
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: 33d84ca86ac3cd4696dce3797b015b861884182a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102216432"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Nawiązywanie połączenia z interfejsem API Media Services v3 — Node.js

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

W tym artykule opisano sposób nawiązywania połączenia z zestawem SDK node.js Azure Media Services v3 przy użyciu metody logowania jednostki usługi. Będziesz korzystać z plików w repozytorium przykładów *usługi Media-Services-v3-Node-samouczków* . Przykład *HelloWorld-ListAssets* zawiera kod służący do nawiązywania połączenia, a następnie wyświetlania listy zasobów na koncie.

## <a name="prerequisites"></a>Wymagania wstępne

- Instalacja Visual Studio Code.
- Zainstaluj [Node.js](https://nodejs.org/en/download/).
- Zainstaluj język [TypeScript](https://www.typescriptlang.org/download).
- [Utwórz konto Media Services](./create-account-howto.md). Pamiętaj, aby zapamiętać nazwę grupy zasobów i nazwę konta Media Services.
- Utwórz nazwę główną usługi dla swojej aplikacji. Zobacz [interfejsy API dostępu](./access-api-howto.md).<br/>**Porada Pro!** Pozostaw to okno otwarte lub Skopiuj wszystko na karcie JSON do Notatnika. 
- Upewnij się, że zapoznaj się z najnowszą wersją [zestawu AzureMediaServices SDK dla języka JavaScript](https://www.npmjs.com/package/@azure/arm-mediaservices).

> [!IMPORTANT]
> Zapoznaj się z [konwencjami nazewnictwa](media-services-apis-overview.md#naming-conventions) Azure Media Services, aby zrozumieć ważne ograniczenia nazewnictwa jednostek.

## <a name="clone-the-nodejs-samples-repo"></a>Klonowanie repozytorium przykładów Node.JS

Będziesz korzystać z niektórych plików w przykładach na platformie Azure. Sklonuj repozytorium przykładów Node.JS.

```git
git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
```

## <a name="install-the-packages"></a>Zainstaluj pakiety

### <a name="install-azurearm-mediaservices"></a>Zainstaluj @azure/arm-mediaservices

```bash
npm install @azure/arm-mediaservices
```

### <a name="install-azurems-rest-nodeauth"></a>Zainstaluj @azure/ms-rest-nodeauth

Zainstaluj minimalną wersję " @azure/ms-rest-nodeauth ": "^ 3.0.0".

```bash
npm install @azure/ms-rest-nodeauth@"^3.0.0"
```

Na potrzeby tego przykładu w pliku zostaną użyte następujące pakiety `package.json` .

|Pakiet|Opis|
|---|---|
|`@azure/arm-mediaservices`|Azure Media Services SDK. <br/>Aby upewnić się, że używasz najnowszego pakietu Azure Media Services, sprawdź [npm @azure/arm-mediaservices Install ](https://www.npmjs.com/package/@azure/arm-mediaservices).|
|`@azure/ms-rest-nodeauth` | Wymagane na potrzeby uwierzytelniania w usłudze AAD przy użyciu nazwy głównej usługi lub tożsamości zarządzanej|
|`@azure/storage-blob`|Zestaw SDK magazynu. Używane podczas przekazywania plików do zasobów.|
|`@azure/ms-rest-js`| Używane do logowania.|
|`@azure/storage-blob` | Służy do przekazywania i pobierania plików do zasobów w Azure Media Services do kodowania.|
|`@azure/abort-controller`| Używane razem z klientem magazynu w celu przekroczenia czasu długotrwałych operacji pobierania|

### <a name="create-the-packagejson-file"></a>Utwórz package.jsw pliku

1. Utwórz `package.json` plik za pomocą ulubionego edytora.
1. Otwórz plik i wklej następujący kod:

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.ts",
  "dependencies": {
    "@azure/arm-mediaservices": "^8.0.0",
    "@azure/abort-controller": "^1.0.2",
    "@azure/ms-rest-nodeauth": "^3.0.6",
    "@azure/storage-blob": "^12.4.0",
  }
}
```

## <a name="connect-to-nodejs-client-using-typescript"></a>Nawiązywanie połączenia z klientem Node.js przy użyciu języka TypeScript



### <a name="sample-env-file"></a>Przykładowy plik *ENV*

Skopiuj zawartość tego pliku do pliku o nazwie *. env*. Powinien być przechowywany w katalogu głównym repozytorium roboczego. Są to wartości ze strony dostęp do interfejsu API dla konta Media Services w portalu.

Po utworzeniu pliku *ENV* można rozpocząć pracę z przykładami.

```nodejs
# Values from the API Access page in the portal
AZURE_CLIENT_ID=""
AZURE_CLIENT_SECRET= ""
AZURE_TENANT_ID= ""

# Change this to match your AAD Tenant domain name. 
AAD_TENANT_DOMAIN = "microsoft.onmicrosoft.com"

# Set this to your Media Services Account name, resource group it is contained in, and location
AZURE_MEDIA_ACCOUNT_NAME = ""
AZURE_LOCATION= ""
AZURE_RESOURCE_GROUP= ""

# Set this to your Azure Subscription ID
AZURE_SUBSCRIPTION_ID= ""

# You must change these if you are using Gov Cloud, China, or other non standard cloud regions
AZURE_ARM_AUDIENCE= "https://management.core.windows.net"
AZURE_ARM_ENDPOINT="https://management.azure.com"

# DRM Testing
DRM_SYMMETRIC_KEY="add random base 64 encoded string here"
```

## <a name="run-the-sample-application-helloworld-listassets"></a>Uruchamianie przykładowej aplikacji *HelloWorld-ListAssets*

1. Zmień katalog na folder *AMSv3Samples*

```bash
cd AMSv3Samples
```

2. Zainstaluj pakiety używane w *packages.js* pliku.

```
npm install 
```

3. Zmień katalog na folder *HelloWorld-ListAssets* .

```bash
cd HelloWorld-ListAssets
```

4. Uruchom Visual Studio Code z folderu AMSv3Samples. Jest to wymagane do uruchomienia z folderu, w którym znajdują się folder ". programu vscode" i tsconfig.jsw plikach

```dotnetcli
cd ..
code .
```

Otwórz folder dla *HelloWorld-ListAssets* i Otwórz plik *index. ts* w edytorze Visual Studio Code.

W pliku *index. TS* należy nacisnąć klawisz F5, aby uruchomić debuger. Powinna zostać wyświetlona lista zasobów wyświetlana, jeśli masz już zasoby na koncie. Jeśli konto jest puste, zostanie wyświetlona pusta lista.  

Aby szybko wyświetlić wymienione zasoby, użyj portalu do przekazania kilku plików wideo. Elementy zawartości zostaną automatycznie utworzone, a następnie ponowne uruchomienie tego skryptu spowoduje zwrócenie ich nazw.

### <a name="a-closer-look-at-the-helloworld-listassets-sample"></a>Bliżej przykładu *HelloWorld-ListAssets*

Przykład *HelloWorld-ListAssets* pokazuje, jak nawiązać połączenie z klientem Media Services przy użyciu jednostki usługi i wyświetlić listę zasobów w ramach konta. Zobacz komentarze w kodzie, aby zapoznać się ze szczegółowym wyjaśnieniem tego, co robi.

```ts
import * as msRestNodeAuth from "@azure/ms-rest-nodeauth";
import { AzureMediaServices } from '@azure/arm-mediaservices';
import { AzureMediaServicesOptions } from "@azure/arm-mediaservices/esm/models";
// Load the .env file if it exists
import * as dotenv from "dotenv";
dotenv.config();

export async function main() {
  // Copy the samples.env file and rename it to .env first, then populate it's values with the values obtained 
  // from your Media Services account's API Access page in the Azure portal.
  const clientId = process.env.AZURE_CLIENT_ID as string;
  const secret = process.env.AZURE_CLIENT_SECRET as string;
  const tenantDomain = process.env.AAD_TENANT_DOMAIN as string;
  const subscriptionId = process.env.AZURE_SUBSCRIPTION_ID as string;
  const resourceGroup = process.env.AZURE_RESOURCE_GROUP as string;
  const accountName = process.env.AZURE_MEDIA_ACCOUNT_NAME as string;

  let clientOptions: AzureMediaServicesOptions = {
    longRunningOperationRetryTimeout: 5, // set the time out for retries to 5 seconds
    noRetryPolicy: false // use the default retry policy.
  }

  const creds = await msRestNodeAuth.loginWithServicePrincipalSecret(clientId, secret, tenantDomain);
  const mediaClient = new AzureMediaServices(creds, subscriptionId, clientOptions);

  // List Assets in Account
  console.log("Listing Assets Names in account:")
  var assets = await mediaClient.assets.list(resourceGroup, accountName);

  assets.forEach(asset => {
    console.log(asset.name);
  });

  if (assets.odatanextLink) {
    console.log("There are more than 1000 assets in this account, use the assets.listNext() method to continue listing more assets if needed")
    console.log("For example:  assets = await mediaClient.assets.listNext(assets.odatanextLink)");
  }
}

main().catch((err) => {
  console.error("Error running sample:", err.message);
});
```

## <a name="more-samples"></a>Więcej przykładów

Następujące przykłady są dostępne w [repozytorium](https://github.com/Azure-Samples/media-services-v3-node-tutorials)

|Project name (Nazwa projektu)|Przypadek użycia|
|---|---|
|Live/index. TS| Przykład podstawowego przesyłania strumieniowego na żywo. **Ostrzeżenie**, upewnij się, że wszystkie zasoby zostały oczyszczone i nie są już naliczane w portalu przy korzystaniu z usługi Live|
|StreamFilesSample/index. TS| Podstawowy przykład przekazywania pliku lokalnego lub kodowania ze źródłowego adresu URL. Przykład pokazuje, jak używać zestawu SDK magazynu do pobierania zawartości i pokazuje, jak przesyłać strumieniowo do odtwarzacza |
|StreamFilesWithDRMSample/index. TS| Demonstruje sposób kodowania i przesyłania strumieniowego przy użyciu Widevine i PlayReady DRM |
|VideoIndexerSample/index. TS| Przykład użycia ustawień predefiniowanych analizatora wideo i audio do generowania metadanych i szczegółowych informacji z pliku wideo lub audio |

## <a name="see-also"></a>Zobacz też

- [Dokumentacja referencyjna modułów Azure Media Services dla Node.js](/javascript/api/overview/azure/media-services)
- [Platforma Azure dla & języka JavaScript Node.js deweloperów](/azure/developer/javascript/)
- [Media Services kodu źródłowego w @azure/azure-sdk-for-js repozytorium centrum git](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Dokumentacja pakietu platformy Azure dla deweloperów Node.js](/javascript/api/overview/azure/)
- [Koncepcje Media Services](concepts-overview.md)
- [npm Zainstaluj @azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices)
- [Platforma Azure dla & języka JavaScript Node.js deweloperów](/azure/developer/javascript/)
- [Media Services kodu źródłowego w @azure/azure-sdk-for-js repozytorium](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)

## <a name="next-steps"></a>Następne kroki

Przeanalizuj [dokumentację oprogramowania Node.js](/javascript/api/overview/azure/mediaservices/management) usługi Media Services i zapoznaj się z [przykładami](https://github.com/Azure-Samples/media-services-v3-node-tutorials) pokazującymi, jak używać interfejsu API usługi Media Services przy użyciu środowiska node.js.
