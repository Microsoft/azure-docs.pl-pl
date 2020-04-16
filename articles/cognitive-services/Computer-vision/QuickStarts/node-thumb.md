---
title: 'Szybki start: generowanie miniatur — REST, Node.js'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start wygenerujesz miniaturę obrazu za pomocą interfejsu API przetwarzania obrazów przy użyciu języka Node.js.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 04/14/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: d2226e161d96a52834dc3d0c16a1a053d39f02e5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81404488"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-nodejs"></a>Szybki start: generowanie miniatury przy użyciu interfejsu API REST i pliku Node.js

W tym przewodniku Szybki start wygenerujesz miniaturę z obrazu przy użyciu interfejsu API REST przetwarzania wizji komputerowej. Metoda [Get Thumbnail](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fb) umożliwia wygenerowanie miniatury obrazu. Należy określić wysokość i szerokość, które mogą mieć inny współczynnik proporcji niż obraz wejściowy. Interfejs API przetwarzania obrazów wykorzystuje inteligentne przycinanie, aby określić obszar zainteresowania i wygenerować współrzędne przycinania na podstawie tego obszaru.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć zainstalowany język [Node.js](https://nodejs.org) w wersji 4.x lub nowszej.
- Musisz mieć zainstalowany program [npm](https://www.npmjs.com/).
- Musisz mieć klucz subskrypcji funkcji przetwarzania obrazów. Możesz uzyskać bezpłatny klucz próbny z [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Możesz też postępować zgodnie z instrukcjami w aplikacji [Utwórz konto usług Cognitive Services,](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) aby zasubskrybować usługę Computer Vision i uzyskać klucz. Następnie [należy utworzyć zmienne środowiskowe](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication) dla ciągu `COMPUTER_VISION_SUBSCRIPTION_KEY` punktu `COMPUTER_VISION_ENDPOINT`końcowego klucza i usługi, odpowiednio o nazwie i ,.

## <a name="create-and-run-the-sample"></a>Tworzenie i uruchamianie próbki

Aby utworzyć i uruchomić przykład, wykonaj następujące kroki:

1. Zainstaluj pakiet [`request`](https://www.npmjs.com/package/request) npm.
   1. Otwórz okno wiersza polecenia jako administrator.
   1. Uruchom następujące polecenie:

      ```console
      npm install request
      ```

   1. Po pomyślnym zainstalowaniu pakietu zamknij okno wiersza polecenia.

1. Skopiuj następujący kod do edytora tekstów.
1. Opcjonalnie zastąp wartość `imageUrl` adresem URL innego obrazu, który chcesz analizować.
1. Zapisz kod jako plik z rozszerzeniem `.js`. Na przykład `get-thumbnail.js`.
1. Otwórz okno wiersza polecenia.
1. W wierszu polecenia użyj polecenia `node`, aby uruchomić plik. Na przykład `node get-thumbnail.js`.

```javascript
'use strict';

const request = require('request');

let subscriptionKey = process.env['COMPUTER_VISION_SUBSCRIPTION_KEY'];
let endpoint = process.env['COMPUTER_VISION_ENDPOINT']
if (!subscriptionKey) { throw new Error('Set your environment variables for your subscription key and endpoint.'); }

var uriBase = endpoint + 'vision/v2.1/generateThumbnail';

const imageUrl =
    'https://upload.wikimedia.org/wikipedia/commons/9/94/Bloodhound_Puppy.jpg';

// Request parameters.
const params = {
    'width': '100',
    'height': '100',
    'smartCropping': 'true'
};

const options = {
    uri: uriBase,
    qs: params,
    body: '{"url": ' + '"' + imageUrl + '"}',
    headers: {
        'Content-Type': 'application/json',
        'Ocp-Apim-Subscription-Key' : subscriptionKey
    }
};

request.post(options, (error, response, body) => {
  if (error) {
    console.log('Error: ', error);
    return;
  }
});
```

## <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Pomyślna odpowiedź jest zwracana jako dane binarne, które reprezentują dane obrazu miniatury. Jeśli żądanie zakończy się niepowodzeniem, odpowiedź zostanie wyświetlona w oknie konsoli. Odpowiedź na nieudane żądanie zawiera kod błędu oraz komunikat, który umożliwi określenie, co poszło nie tak.

## <a name="next-steps"></a>Następne kroki

Następnie zapoznaj się z interfejsem API przetwarzania obrazów używanym do analizowania obrazu, wykrywania gwiazd i punktów orientacyjnych, tworzenia miniatury oraz wyodrębniania drukowanego i odręcznego tekstu.

> [!div class="nextstepaction"]
> [Zobacz, jak działa interfejs API przetwarzania obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
