---
title: Sprawdzanie jakości danych dla usługi Custom Speech-Speech
titleSuffix: Azure Cognitive Services
description: Custom Speech udostępnia narzędzia umożliwiające wizualne badanie jakości rozpoznawania modelu przez porównanie danych audio z odpowiednim wynikiem rozpoznawania. Możesz odtworzyć przekazany dźwięk i określić, czy podany wynik rozpoznawania jest poprawny.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: erhopf
ms.openlocfilehash: e871d2c8e0fe00fa7db3144a787447163c82e62d
ms.sourcegitcommit: d7fba095266e2fb5ad8776bffe97921a57832e23
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84629041"
---
# <a name="inspect-custom-speech-data"></a>Inspekcja danych usługi Custom Speech

> [!NOTE]
> Na tej stronie założono, że załączono [przygotowanie danych testowych dla Custom Speech](how-to-custom-speech-test-data.md) i przekazano zestaw danych do inspekcji.

Custom Speech udostępnia narzędzia umożliwiające wizualne badanie jakości rozpoznawania modelu przez porównanie danych audio z odpowiednim wynikiem rozpoznawania. Z poziomu [portalu Custom Speech](https://speech.microsoft.com/customspeech)można odtworzyć załadowane audio i określić, czy podany wynik rozpoznawania jest poprawny. To narzędzie ułatwia sprawdzenie jakości modelu zamiany mowy na tekst linii bazowej firmy Microsoft, sprawdzenie przeszkolonego modelu niestandardowego lub porównanie transkrypcji przez dwa modele.

W tym dokumencie dowiesz się, jak wizualnie zbadać jakość modelu przy użyciu przekazanych wcześniej danych szkoleniowych.

Na tej stronie dowiesz się, jak wizualnie zbadać jakość linii bazowej tekstu i/lub modelu, który został przeszkolony przez firmę Microsoft. Do testowania zostaną użyte przekazane dane do karty **dane** .

## <a name="create-a-test"></a>Tworzenie testu

Postępuj zgodnie z poniższymi instrukcjami, aby utworzyć test:

1. Zaloguj się do [portalu Custom Speech](https://speech.microsoft.com/customspeech).
2. Przejdź do **> zamiany mowy na tekst Custom Speech > [nazwa projektu] > testowanie**.
3. Kliknij przycisk **Dodaj test**.
4. Wybierz pozycję **Inspekcja jakości (dane audio)**. Nadaj testowi nazwę, opis i wybierz zestaw danych audio.
5. Wybierz maksymalnie dwa modele, które chcesz przetestować.
6. Kliknij przycisk **Utwórz**.

Po pomyślnym utworzeniu testu można zobaczyć, jak model przekształca określony zestaw danych audio, lub Porównaj wyniki z dwóch modeli obok siebie.

[!INCLUDE [service-pricing-advisory](includes/service-pricing-advisory.md)]

## <a name="side-by-side-model-comparisons"></a>Porównania modeli obok siebie

Gdy test stanu zakończy _się pomyślnie_, kliknij w nazwie elementu testowego, aby zobaczyć szczegóły testu. Ta strona szczegółów zawiera listę wszystkich wyrażenia długości w zestawie danych i pokazuje wyniki rozpoznawania dwóch porównywanych modeli.

Aby pomóc w sprawdzeniu porównania obok siebie, można przełączać różne typy błędów, w tym Wstawianie, usuwanie i podstawianie. Nasłuchiwanie dźwięku i porównywanie wyników rozpoznawania w każdej kolumnie (pokazującej transkrypcję i wyniki dwóch modeli zamiany mowy na tekst) można zdecydować, który model spełnia Twoje potrzeby i gdzie są potrzebne ulepszenia.

Testowanie modelu side-by-Side jest przydatne do sprawdzania, który model rozpoznawania mowy jest najlepszy dla aplikacji. Aby określić obiektywną miarę dokładności wymagającą uzyskanego audio, postępuj zgodnie z instrukcjami znajdującymi się w [ocenie dokładności](how-to-custom-speech-evaluate-data.md).

## <a name="next-steps"></a>Następne kroki

- [Oceń dane](how-to-custom-speech-evaluate-data.md)
- [Trenowanie modelu](how-to-custom-speech-train-model.md)
- [Ulepszanie modelu](how-to-custom-speech-improve-accuracy.md)
- [Wdrażanie modelu](how-to-custom-speech-deploy-model.md)

## <a name="additional-resources"></a>Zasoby dodatkowe

- [Przygotuj dane testowe dla Custom Speech](how-to-custom-speech-test-data.md)
