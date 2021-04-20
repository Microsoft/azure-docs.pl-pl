---
title: 'Szybki start: interfejs API REST optycznego rozpoznawania znaków'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start rozpoczniesz pracę z interfejsem API REST optycznego rozpoznawania znaków.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 04/19/2021
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 2f01b1d222470c49505638be64180948b6f7e046
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728262"
---
Interfejs API REST optycznego rozpoznawania znaków umożliwia odczytywanie tekstu drukowanego i odręcznego.

> [!NOTE]
> W tym przewodniku Szybki start do wywołania interfejsu API REST są używane polecenia cURL. Interfejs API REST można również wywołać przy użyciu języka programowania. Przykłady można znaleźć w przykładach w języku [C#,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/ComputerVision/REST) [Python,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/ComputerVision/REST) [Java,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/ComputerVision/REST) [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/ComputerVision/REST)i [Go.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/ComputerVision/REST)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [bezpłatne tworzenie subskrypcji](https://azure.microsoft.com/free/cognitive-services/) 
* Po utworzeniu subskrypcji platformy Azure utwórz zasób przetwarzanie obrazów zasobów przetwarzanie obrazów w witrynie Azure Portal, aby uzyskać <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" "  target="_blank"> klucz i punkt </a> końcowy. Po wdrożeniu kliknij pozycję **Przejdź do zasobu**.
  * Klucz i punkt końcowy będą potrzebne z zasobu, który utworzysz, aby połączyć aplikację z przetwarzanie obrazów usługą. Klucz i punkt końcowy wkleisz do poniższego kodu w dalszej części tego przewodnika Szybki start.
  * Możesz użyć bezpłatnej warstwy cenowej ( ), aby wypróbować usługę, a następnie uaktualnić ją do warstwy `F0` płatnej w środowisku produkcyjnym.
* [Zainstalowany program cURL](https://curl.haxx.se/)



## <a name="read-printed-and-handwritten-text"></a>Odczytywanie tekstu drukowanego i odręcznego

Usługa OCR może odczytywać widoczny tekst na obrazie i konwertować go na strumień znaków. Aby uzyskać więcej informacji na temat rozpoznawania tekstu, zobacz [Omówienie optycznego rozpoznawania znaków (OCR).](../overview-ocr.md)

### <a name="call-the-read-api"></a>Wywołanie interfejsu API odczytu

Aby utworzyć i uruchomić przykład, wykonaj następujące kroki:

1. Skopiuj następujące polecenie do edytora tekstów.
1. W razie potrzeby wprowadź w poleceniu następujące zmiany:
    1. Zastąp wartość `<subscriptionKey>` kluczem subskrypcji.
    1. Zastąp pierwszą część adresu URL żądania ( `westcentralus` ) tekstem we własnym adresie URL punktu końcowego.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. Opcjonalnie zmień adres URL obrazu w treści żądania (`https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\`) na adres URL innego obrazu do analizy.
1. Otwórz okno wiersza polecenia.
1. Wklej polecenie z edytora tekstów w oknie wiersza polecenia, a następnie uruchom polecenie.

```bash
curl -v -X POST "https://westcentralus.api.cognitive.microsoft.com/vision/v3.2/read/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\"}"
```

Odpowiedź będzie zawierać `Operation-Location` nagłówek, którego wartość jest unikatowym adresem URL. Ten adres URL umożliwia wykonywanie zapytań o wyniki operacji Odczytu. Adres URL wygasa po 48 godzinach.

### <a name="get-read-results"></a>Uzyskiwanie wyników odczytu

1. Skopiuj następujące polecenie do edytora tekstów.
1. Zastąp adres URL `Operation-Location` wartością skopiowaną w poprzednim kroku.
1. W razie potrzeby wprowadź w poleceniu następujące zmiany:
    1. Zastąp wartość `<subscriptionKey>` kluczem subskrypcji.
1. Otwórz okno wiersza polecenia.
1. Wklej polecenie z edytora tekstów w oknie wiersza polecenia, a następnie uruchom polecenie.

```bash
curl -v -X GET "https://westcentralus.api.cognitive.microsoft.com/vision/v3.2/read/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{body}" 
```

### <a name="examine-the-response"></a>Sprawdzanie odpowiedzi

Po pomyślnym przetworzeniu żądania zostanie zwrócona odpowiedź w formacie JSON. Przykładowa aplikacja analizuje i wyświetla pomyślną odpowiedź w oknie wiersza polecenia, podobnie jak w poniższym przykładzie:

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-04-08T21:56:17.6819115+00:00",
  "lastUpdatedDateTime": "2021-04-08T21:56:18.4161316+00:00",
  "analyzeResult": {
    "version": "3.2",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 338,
        "height": 479,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              25,
              14,
              318,
              14,
              318,
              59,
              25,
              59
            ],
            "text": "NOTHING",
            "appearance": {
              "style": {
                "name": "other",
                "confidence": 0.971
              }
            },
            "words": [
              {
                "boundingBox": [
                  27,
                  15,
                  294,
                  15,
                  294,
                  60,
                  27,
                  60
                ],
                "text": "NOTHING",
                "confidence": 0.994
              }
            ]
          }
        ]
      }
    ]
  }
}

```



## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start opisano sposób wywołania interfejsu API REST odczytywania. Następnie dowiedz się więcej o funkcjach interfejsu API odczytywania.

> [!div class="nextstepaction"]
>[Wywołanie interfejsu API odczytu](../Vision-API-How-to-Topics/call-read-api.md)

* [Omówienie OCR](../overview-ocr.md)
