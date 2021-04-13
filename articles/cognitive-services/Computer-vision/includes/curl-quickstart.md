---
title: 'Szybki start: interfejs API REST optycznego rozpoznawania znaków'
titleSuffix: Azure Cognitive Services
description: W tym przewodniku Szybki start rozpoczniesz pracę z interfejsem API REST optycznego rozpoznawania znaków.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 01149eed5cc4195ca501507e7fe1d66fffecb84d
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107327096"
---
Interfejs API REST optycznego rozpoznawania znaków umożliwia odczytywanie tekstu drukowanego i odręcznego.

> [!NOTE]
> W tym przewodniku Szybki start do wywołania interfejsu API REST są używane polecenia cURL. Interfejs API REST można również wywołać przy użyciu języka programowania. Zobacz przykłady w serwisie GitHub, aby uzyskać przykłady w [językach C#,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/ComputerVision/REST) [Python,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/ComputerVision/REST) [Java,](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/ComputerVision/REST) [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/ComputerVision/REST)i [Go.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/ComputerVision/REST)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [utwórz subskrypcję bezpłatnie](https://azure.microsoft.com/free/cognitive-services/) 
* Po utworzeniu subskrypcji platformy Azure utwórz zasób przetwarzanie obrazów zasobów przetwarzanie obrazów witrynie Azure Portal, aby uzyskać <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" "  target="_blank"> klucz i punkt </a> końcowy. Po wdrożeniu kliknij pozycję **Przejdź do zasobu**.
  * Klucz i punkt końcowy z zasobu, który utworzysz, będą potrzebne do połączenia aplikacji z przetwarzanie obrazów usługą. Klucz i punkt końcowy wkleisz do poniższego kodu w dalszej części tego przewodnika Szybki start.
  * Możesz użyć warstwy cenowej Bezpłatna ( ), aby wypróbować usługę, i przejść później na warstwę płatną `F0` na użytek produkcji.
* [Zainstalowany program cURL](https://curl.haxx.se/)



## <a name="read-printed-and-handwritten-text"></a>Odczytywanie tekstu drukowanego i odręcznego

Usługa OCR może odczytywać widoczny tekst na obrazie i konwertować go na strumień znaków. Aby uzyskać więcej informacji na temat rozpoznawania tekstu, zobacz [Omówienie optycznego rozpoznawania znaków (OCR).](../overview-ocr.md)

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

Zapoznaj się z interfejsem API OCR bardziej szczegółowo. Aby szybko poeksperymentować z interfejsem API, wypróbuj [konsolę testowania interfejsu Open API.](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005/console)

> [!div class="nextstepaction"]
> [Eksplorowanie interfejsu API OCR](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)
