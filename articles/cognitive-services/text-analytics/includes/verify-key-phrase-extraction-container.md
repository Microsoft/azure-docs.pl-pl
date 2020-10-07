---
title: Weryfikowanie wyodrębnianie kluczowych fraz wystąpienia kontenera
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak zweryfikować wystąpienie kontenera wyodrębnianie kluczowych fraz.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 5dca4828a5c1127133461ddf9fc06099fc176b68
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779573"
---
### <a name="verify-the-key-phrase-extraction-container-instance"></a>Weryfikowanie wyodrębnianie kluczowych fraz wystąpienia kontenera

1. Wybierz kartę **Przegląd** i skopiuj adres IP.
1. Otwórz nową kartę przeglądarki, a następnie wprowadź adres IP. Na przykład wpisz `http://<IP-address>:5000 (http://55.55.55.55:5000` ). Zostanie wyświetlona strona główna kontenera, która informuje o tym, że kontener jest uruchomiony.

    ![Wyświetl stronę główną kontenera, aby sprawdzić, czy jest uruchomiona](../media/how-tos/container-instance/swagger-docs-on-container.png)

1. Wybierz link **opis interfejsu API usługi** , aby przejść do strony struktury Swagger kontenera.

1. Wybierz dowolny z **wpisów** interfejsów API, a następnie wybierz opcję **Wypróbuj**. Wyświetlane są parametry, które obejmują następujące przykładowe dane wejściowe:

    ```json
    {
      "documents": [
        {
          "id": "1",
          "text": "Hello world"
        },
        {
          "id": "2",
          "text": "Bonjour tout le monde"
        },
        {
          "id": "3",
          "text": "La carretera estaba atascada. Había mucho tráfico el día de ayer."
        },
        {
          "id": "4",
          "text": ":) :( :D"
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

    Model, który znajduje się w kontenerze, generuje wynik, który mieści się w zakresie od 0 do 1, gdzie 0 jest ujemna i 1 jest dodatnia.

    Zwracana odpowiedź JSON zawiera tonacji dla zaktualizowanych danych wejściowych tekstu:

    ```json
    {
      "documents": [
        {
          "id": "7",
          "keyPhrases": [
            "Great people",
            "great sessions",
            "KubeCon Conference",
            "Barcelona",
            "best conferences"
          ],
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

Teraz można skorelować dokument `id` danych JSON ładunku odpowiedzi do oryginalnego dokumentu ładunku żądania `id` . Dokument z wynikiem zawiera `keyPhrases` tablicę zawierającą listę fraz kluczowych, które zostały wyodrębnione z odpowiedniego dokumentu wejściowego. Ponadto istnieją różne statystyki, takie jak `characterCount` i `transactionCount` dla każdego uzyskanego dokumentu.