---
title: 'Szybki start: generowanie miniatury — REST, cURL'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start wygenerujesz miniaturę obrazu za pomocą interfejsu API przetwarzania obrazów przy użyciu biblioteki cURL.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 05/20/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: d6b55a7f3df7657e2b6b0b23b797d36788658f32
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83745163"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-curl"></a>Szybki Start: generowanie miniatury przy użyciu interfejsu API REST przetwarzanie obrazów i zwinięcie

W tym przewodniku szybki start utworzysz miniaturę obrazu przy użyciu interfejsu API REST przetwarzanie obrazów. Należy określić wymaganą wysokość i szerokość, która może się różnić w zależności od aspektu obrazu wejściowego. Przetwarzanie obrazów korzysta z inteligentnego przycinania, aby inteligentnie identyfikować obszar zainteresowania i generować współrzędne przycinania wokół tego regionu.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services).

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć bibliotekę [cURL](https://curl.haxx.se/windows).
- Musisz mieć klucz subskrypcji funkcji przetwarzania obrazów. Możesz uzyskać bezpłatny klucz wersji próbnej z usługi [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Lub postępuj zgodnie z instrukcjami w temacie [Tworzenie konta Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) , aby subskrybować przetwarzanie obrazów i uzyskać klucz.

## <a name="create-and-run-the-sample-command"></a>Tworzenie i uruchamianie przykładowego polecenia

Aby utworzyć i uruchomić przykład, wykonaj następujące kroki:

1. Skopiuj następujące polecenie do edytora tekstów.
1. W razie potrzeby wprowadź w poleceniu następujące zmiany:
    1. Zastąp wartość `<subscriptionKey>` kluczem subskrypcji.
    1. Zastąp wartość `<thumbnailFile>` ścieżką i nazwą pliku, w którym chcesz zapisać miniaturę.
    1. Zastąp pierwszą część adresu URL żądania ( `westcentralus` ) tekstem w adresie URL własnego punktu końcowego.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Opcjonalnie zmień adres URL obrazu w treści żądania (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) na adres URL innego obrazu do analizy, z którego chcesz wygenerować miniaturę.
1. Otwórz okno wiersza polecenia.
1. Wklej polecenie z edytora tekstu do okna wiersza polecenia.
1. Naciśnij klawisz ENTER, aby uruchomić program.

    ```bash
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westus.api.cognitive.microsoft.com/vision/v3.0/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

## <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Pomyślna odpowiedź powoduje zapisanie obrazu miniatury w pliku określonym w `<thumbnailFile>`. Jeśli żądanie zakończy się niepowodzeniem, w odpowiedzi zostanie wyświetlony kod błędu oraz komunikat, który umożliwi określenie, co poszło nie tak. Jeśli żądanie powiodło się, ale utworzona Miniatura nie jest prawidłowym plikiem obrazu, może to oznaczać, że klucz subskrypcji jest nieprawidłowy.

## <a name="next-steps"></a>Następne kroki

Eksploruj interfejs API przetwarzania obrazów w celu analizowania obrazu, wykrywania osobistości i punktów orientacyjnych, tworzenia miniatury oraz wyodrębniania tekstu napisanego i odręcznego. Aby szybko zacząć eksperymentować z interfejsem API przetwarzania obrazów, wypróbuj [konsolę testowania interfejsu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f20c).

> [!div class="nextstepaction"]
> [Zobacz, jak działa interfejs API przetwarzania obrazów](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-ga/operations/56f91f2e778daf14a499f21b)
