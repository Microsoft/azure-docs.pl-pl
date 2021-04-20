---
title: 'Szybki start: interfejs API REST analizy obrazów'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start rozpoczniesz pracę z interfejsem API REST analizy obrazów.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 04/19/2021
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 7a2e8613aab61beec3720cadaa20eb008386b43b
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728187"
---
Interfejs API REST analizy obrazów umożliwia:

* Przeanalizuj obraz, aby uzyskać tagi, opis tekstu, twarze, zawartość dla dorosłych i nie tylko.
* Generowanie miniatury za pomocą inteligentnego przycinania

> [!NOTE]
> W tym przewodniku Szybki start do wywołania interfejsu API REST są używane polecenia cURL. Interfejs API REST można również wywołać przy użyciu języka programowania. Przykłady można znaleźć w przykładach w języku [C#,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/ComputerVision/REST) [Python,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/ComputerVision/REST) [Java,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/ComputerVision/REST) [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/ComputerVision/REST)i [Go.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/ComputerVision/REST)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [bezpłatne tworzenie subskrypcji](https://azure.microsoft.com/free/cognitive-services/) 
* Po utworzeniu subskrypcji platformy Azure utwórz zasób przetwarzanie obrazów zasobów przetwarzanie obrazów w witrynie Azure Portal, aby uzyskać <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" "  target="_blank"> klucz i punkt </a> końcowy. Po wdrożeniu kliknij pozycję **Przejdź do zasobu**.
  * Klucz i punkt końcowy będą potrzebne z zasobu, który utworzysz, aby połączyć aplikację z przetwarzanie obrazów usługą. Klucz i punkt końcowy wkleisz do poniższego kodu w dalszej części tego przewodnika Szybki start.
  * Możesz użyć bezpłatnej warstwy cenowej ( ), aby wypróbować usługę, a następnie uaktualnić ją do warstwy `F0` płatnej w środowisku produkcyjnym.
* [Zainstalowany program cURL](https://curl.haxx.se/)

## <a name="analyze-an-image"></a>Analizowanie obrazu

Aby przeanalizować obraz pod celu przeanalizowania różnych funkcji wizualnych, wykonaj następujące kroki:

1. Skopiuj następujące polecenie do edytora tekstów.
1. W razie potrzeby wprowadź w poleceniu następujące zmiany:
    1. Zastąp wartość `<subscriptionKey>` kluczem subskrypcji.
    1. Zastąp pierwszą część adresu URL żądania ( `westcentralus` ) tekstem we własnym adresie URL punktu końcowego.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Opcjonalnie zmień adres URL obrazu w treści żądania (`http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\`) na adres URL innego obrazu do analizy.
1. Otwórz okno wiersza polecenia.
1. Wklej polecenie z edytora tekstów w oknie wiersza polecenia, a następnie uruchom polecenie.

```bash
curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v3.1/analyze?visualFeatures=Categories,Description&details=Landmarks" -d "{\"url\":\"http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\"}"
```

### <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON. Przykładowa aplikacja analizuje i wyświetla pomyślną odpowiedź w oknie wiersza polecenia, podobnie jak w poniższym przykładzie:

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "requestId": "b6e33879-abb2-43a0-a96e-02cb5ae0b795",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```


## <a name="generate-a-thumbnail"></a>Generowanie miniatur

Za pomocą analizy obrazów można wygenerować miniaturę z inteligentnym przycinaniem. Należy określić żądaną wysokość i szerokość, które mogą różnić się współczynnikiem proporcji od obrazu wejściowego. Analiza obrazów używa inteligentnego przycinania, aby inteligentnie identyfikować obszar zainteresowania i generować współrzędne przycinania wokół tego regionu.
 
Aby utworzyć i uruchomić przykład, wykonaj następujące kroki:

1. Skopiuj następujące polecenie do edytora tekstów.
1. W razie potrzeby wprowadź w poleceniu następujące zmiany:
    1. Zastąp wartość `<subscriptionKey>` kluczem subskrypcji.
    1. Zastąp wartość ścieżką i nazwą pliku, w którym chcesz `<thumbnailFile>` zapisać zwrócony obraz miniatury.
    1. Zastąp pierwszą część adresu URL żądania ( `westcentralus` ) tekstem we własnym adresie URL punktu końcowego.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Opcjonalnie zmień adres URL obrazu w treści żądania (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) na adres URL innego obrazu do analizy, z którego chcesz wygenerować miniaturę.
1. Otwórz okno wiersza polecenia.
1. Wklej polecenie z edytora tekstów w oknie wiersza polecenia.
1. Naciśnij klawisz Enter, aby uruchomić program.

    ```bash
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westus.api.cognitive.microsoft.com/vision/v3.1/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

### <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Pomyślna odpowiedź powoduje zapisanie obrazu miniatury w pliku określonym w `<thumbnailFile>`. Jeśli żądanie zakończy się niepowodzeniem, w odpowiedzi zostanie wyświetlony kod błędu oraz komunikat, który umożliwi określenie, co poszło nie tak. Jeśli żądanie wydaje się zakończyć się powodzeniem, ale utworzona miniatura nie jest prawidłowym plikiem obrazu, może to oznaczać, że klucz subskrypcji jest nieprawidłowy.


## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano sposób instalowania podstawowych wywołań analizy obrazów przy użyciu interfejsu API REST. Następnie dowiedz się więcej o funkcjach interfejsu API analizowania.

> [!div class="nextstepaction"]
>[Wywołanie interfejsu API analizy](../Vision-API-How-to-Topics/HowToCallVisionAPI.md)

* [Omówienie analizy obrazów](../overview-image-analysis.md)