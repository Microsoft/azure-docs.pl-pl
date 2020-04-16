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
ms.date: 04/14/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: d60f1101fc858e9dcaa22162be97417544e13693
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81405041"
---
# <a name="quickstart-generate-a-thumbnail-using-the-computer-vision-rest-api-and-curl"></a>Szybki start: generowanie miniatury za pomocą interfejsu API REST i cURL

W tym przewodniku Szybki start można wygenerować miniaturę z obrazu przy użyciu interfejsu API REST przetwarzania wizji komputerowej. Należy określić żądaną wysokość i szerokość, które mogą różnić się racją proporcji od obrazu wejściowego. Funkcja Przetwarzania Obrazów wykorzystuje inteligentne przycinanie do inteligentnego identyfikowania obszaru zainteresowania i generowania współrzędnych przycinania wokół tego regionu.

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/ai/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=cognitive-services) przed rozpoczęciem.

## <a name="prerequisites"></a>Wymagania wstępne

- Musisz mieć bibliotekę [cURL](https://curl.haxx.se/windows).
- Musisz mieć klucz subskrypcji funkcji przetwarzania obrazów. Możesz uzyskać bezpłatny klucz próbny z [Try Cognitive Services](https://azure.microsoft.com/try/cognitive-services/?api=computer-vision). Możesz też postępować zgodnie z instrukcjami w aplikacji [Utwórz konto usług Cognitive Services,](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) aby zasubskrybować usługę Computer Vision i uzyskać klucz.

## <a name="create-and-run-the-sample-command"></a>Tworzenie i uruchamianie przykładowego polecenia

Aby utworzyć i uruchomić przykład, wykonaj następujące kroki:

1. Skopiuj następujące polecenie do edytora tekstów.
1. W razie potrzeby wprowadź w poleceniu następujące zmiany:
    1. Zastąp wartość `<subscriptionKey>` kluczem subskrypcji.
    1. Zastąp wartość `<thumbnailFile>` ścieżką i nazwą pliku, w którym chcesz zapisać miniaturę.
    1. Zastąp pierwszą część`westcentralus`adresu URL żądania ( ) tekstem we własnym adresie URL punktu końcowego.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Opcjonalnie zmień adres URL obrazu w treści żądania (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) na adres URL innego obrazu do analizy, z którego chcesz wygenerować miniaturę.
1. Otwórz okno wiersza polecenia.
1. Wklej polecenie z edytora tekstów w oknie wiersza polecenia, a następnie uruchom polecenie.

    ```bash
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v2.1/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

## <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Pomyślna odpowiedź powoduje zapisanie obrazu miniatury w pliku określonym w `<thumbnailFile>`. Jeśli żądanie zakończy się niepowodzeniem, w odpowiedzi zostanie wyświetlony kod błędu oraz komunikat, który umożliwi określenie, co poszło nie tak. Jeśli żądanie wydaje się pomyślnie, ale utworzona miniatura nie jest prawidłowym plikiem obrazu, może to być, że klucz subskrypcji jest nieprawidłowy.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z interfejsem API przetwarzania obrazów, analizowania obrazu, wykrywania gwiazd i punktów orientacyjnych, tworzenia miniatur oraz wyodrębniania drukowanego i odręcznego tekstu. Aby szybko zacząć eksperymentować z interfejsem API przetwarzania obrazów, wypróbuj [konsolę testowania interfejsu Open API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa/console).

> [!div class="nextstepaction"]
> [Zobacz, jak działa interfejs API przetwarzania obrazów](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)
