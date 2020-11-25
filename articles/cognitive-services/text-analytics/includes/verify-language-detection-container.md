---
title: Weryfikowanie wykrywanie języka wystąpienia kontenera
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak zweryfikować wystąpienie kontenera wykrywanie języka.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 543a4d85982adadc86435819679351c8ffaa9814
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96009891"
---
### <a name="verify-the-language-detection-container-instance"></a>Weryfikowanie wykrywanie języka wystąpienia kontenera

1. Wybierz kartę **Przegląd** i skopiuj adres IP.
1. Otwórz nową kartę przeglądarki, a następnie wprowadź adres IP. Na przykład wpisz `http://<IP-address>:5000 (http://55.55.55.55:5000` ). Zostanie wyświetlona strona główna kontenera, która informuje o tym, że kontener jest uruchomiony.

    ![Wyświetl stronę główną kontenera, aby sprawdzić, czy jest uruchomiona](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Wybierz link **opis interfejsu API usługi** , aby przejść do strony struktury Swagger kontenera.

1. Wybierz dowolny z **wpisów** interfejsów API, a następnie wybierz opcję **Wypróbuj**. Wyświetlane są parametry, które obejmują następujące przykładowe dane wejściowe:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "1",
          "text": "Hello world. This is some input text that I love."
        },
        {
          "language": "fr",
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "language": "es",
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        }
      ]
    }
    ```

1. Ustaw wartość **showStats** na `true` .

1. Wybierz pozycję **Execute (wykonaj** ), aby określić tonacji tekstu.

    Model, który znajduje się w kontenerze, generuje wynik, który mieści się w zakresie od 0 do 1, gdzie 0 jest ujemna tonacji i 1 to dodatnie tonacji.

    Zwracana odpowiedź JSON zawiera tonacji dla zaktualizowanych danych wejściowych tekstu:

    ```json
    {
      "documents": [
        {
          "id": "1",
          "detectedLanguages": [
            {
              "name": "English",
              "iso6391Name": "en",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 11,
            "transactionsCount": 1
          }
        },
        {
          "id": "2",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 21,
            "transactionsCount": 1
          }
        },
        {
          "id": "3",
          "detectedLanguages": [
            {
              "name": "Spanish",
              "iso6391Name": "es",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 65,
            "transactionsCount": 1
          }
        },
        {
          "id": "4",
          "detectedLanguages": [
            {
              "name": "French",
              "iso6391Name": "fr",
              "score": 1
            }
          ],
          "statistics": {
            "charactersCount": 8,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 4,
        "validDocumentsCount": 4,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 4
      }
    }
    ```

Teraz można skorelować dokumenty danych JSON ładunku odpowiedzi do oryginalnych dokumentów ładunku żądania przez odpowiadające im `id` . Każdy dokument jest traktowany niezależnie od różnych statystyk, takich jak `characterCount` i `transactionCount` . Ponadto każdy dokument wynikający zawiera `detectedLanguages` tablicę z `name` , `iso6391Name` i `score` dla każdego wykrytego języka. W przypadku wykrycia wielu języków `score` jest on używany do określenia najbardziej prawdopodobnego języka.