---
title: Konwersja danych — LUIS
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak można zmienić wyrażenia długości przed przewidywaniami w Language Understanding (LUIS)
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 42a9caff0433808734ee853cbad90a2088bf4e1e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95019249"
---
# <a name="convert-data-format-of-utterances"></a>Konwertowanie formatu danych wyrażenia długości
LUIS udostępnia następujące konwersje wypowiedź użytkownika przed przewidywaniam "

* Zamiana mowy na tekst za pomocą usługi [Cognitive Services Speech](../Speech-Service/overview.md) .

## <a name="speech-to-text"></a>Zamiana mowy na tekst

Zamiana mowy na tekst jest zapewniana jako integracja z usługą LUIS.

### <a name="intent-conversion-concepts"></a>Pojęcia dotyczące konwersji intencji
Konwersja mowy na tekst w programie LUIS umożliwia wysyłanie mówionych wyrażenia długości do punktu końcowego i odbieranie odpowiedzi przewidywania LUIS. Ten proces jest integracją usługi [mowy](/azure/cognitive-services/Speech) z Luis. Dowiedz się więcej na temat zamiany mowy z [samouczkiem](../speech-service/how-to-recognize-intents-from-speech-csharp.md).

### <a name="key-requirements"></a>Podstawowe wymagania
Nie trzeba tworzyć klucza **interfejs API rozpoznawania mowy Bing** dla tej integracji. Klucz **Language Understanding** utworzony w Azure Portal działa dla tej integracji. Nie używaj klucza początkowego LUIS.

### <a name="pricing-tier"></a>Warstwa cenowa
Ta integracja korzysta z innego modelu [cen](luis-limits.md#key-limits) niż zwykle Language Understanding warstwy cenowe.

### <a name="quota-usage"></a>Użycie limitu przydziału
Aby uzyskać informacje, zobacz [kluczowe limity](luis-limits.md#key-limits) .

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wyodrębnianie danych](luis-concept-data-extraction.md)