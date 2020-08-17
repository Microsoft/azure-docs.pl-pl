---
title: Weryfikowanie analiza tonacji wystąpienia kontenera
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak zweryfikować wystąpienie kontenera analiza tonacji.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: c9b5411c044bb45d284cac0d30705c2b3d40ccd0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80876447"
---
### <a name="verify-the-sentiment-analysis-container-instance"></a>Weryfikowanie analiza tonacji wystąpienia kontenera

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

1. Zastąp dane wejściowe następującą zawartością JSON:

    ```json
    {
      "documents": [
        {
          "language": "en",
          "id": "7",
          "text": "I was fortunate to attend the KubeCon Conference in Barcelona, it is one of the best conferences I have ever attended. Great people, great sessions and I thoroughly enjoyed it!"
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
        "id": "7",
        "score": 0.9826303720474243,
        "statistics": {
          "charactersCount": 176,
            "transactionsCount": 1
          }
        }
      ],
      "errors": [],
      "statistics": {
        "documentsCount": 1,
        "validDocumentsCount": 1,
        "erroneousDocumentsCount": 0,
        "transactionsCount": 1
      }
    }
    ```

Teraz można skorelować dokument `id` danych JSON ładunku odpowiedzi do oryginalnego dokumentu ładunku żądania `id` . Wynik więcej niż `0.98` wskazuje bardzo pozytywny tonacji.