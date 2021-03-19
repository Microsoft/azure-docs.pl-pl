---
title: Szybsze etykietowanie obrazów za pomocą inteligentnego Labeler
titleSuffix: Azure Cognitive Services
description: W tym przewodniku dowiesz się, jak za pomocą inteligentnego Labeler wygenerować sugerowane Tagi dla obrazów. Dzięki temu można szybciej oznaczyć dużą liczbę obrazów podczas uczenia modelu Custom Vision.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: pafarley
ms.openlocfilehash: 94ca47e6114e4f8c3485f6072facd07c25e4b96a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "73647750"
---
# <a name="label-images-faster-with-smart-labeler"></a>Szybsze etykietowanie obrazów za pomocą inteligentnego Labeler

W tym przewodniku dowiesz się, jak za pomocą inteligentnego Labeler wygenerować sugerowane Tagi dla obrazów. Dzięki temu można szybciej oznaczyć dużą liczbę obrazów podczas uczenia modelu Custom Vision.

Po oznaczeniu obrazów dla modelu Custom Vision usługa używa najnowszej przeszkolonej iteracji modelu do przewidywania etykiet obrazów nieoznakowanych. Następnie pokazuje te przewidywania jako sugerowane Tagi, na podstawie wybranego progu ufności i niepewności przewidywania. Następnie można potwierdzić lub zmienić sugestie i przyspieszyć proces ręcznego tagowania obrazów do szkoleń.

## <a name="when-to-use-smart-labeler"></a>Kiedy używać inteligentnego Labeler

Należy pamiętać o następujących ograniczeniach:

* Należy zażądać tylko sugerowanych tagów dla obrazów, których zawartość została już przeszkolone. Nie pobieraj sugestii dotyczących nowego tagu, który właśnie zaczyna się nauczyć.

> [!IMPORTANT]
> Funkcja Smart Labeler używa tego samego [modelu cen](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) co regularne przewidywania. Gdy po raz pierwszy Wyzwalasz sugerowane Tagi dla zestawu obrazów, zostanie naliczona opłata taka sama jak w przypadku wywołań prognoz. Następnie usługa przechowuje wyniki dla wybranych obrazów w bazie danych przez 30 dni, a dostęp do nich będzie można uzyskać w dowolnym momencie w tym okresie. Po upływie 30 dni opłata zostanie naliczona w przypadku powtórnego żądania pożądanych tagów.

## <a name="smart-labeler-workflow"></a>Przepływ pracy Smart Labeler

W poniższych krokach pokazano, jak używać inteligentnego Labeler:

1. Przekaż wszystkie obrazy szkoleniowe do projektu Custom Vision.
1. Oznacz część zestawu danych, wybierając taką samą liczbę obrazów dla każdego tagu.
    > [!TIP]
    > Upewnij się, że używasz wszystkich tagów, dla których chcesz później uzyskać sugestie.
1. Rozpocznij proces szkolenia.
1. Po zakończeniu szkolenia przejdź **do widoku** nieoznaczonego i wybierz przycisk **Pobierz sugerowane Tagi** w okienku po lewej stronie.
    > [!div class="mx-imgBorder"]
    > ![Przycisk sugerowane Tagi jest pokazywany na karcie obrazy nieoznakowane.](./media/suggested-tags/suggested-tags-button.png)
1. W wyświetlonym oknie podręcznym Ustaw liczbę obrazów, dla których chcesz uzyskać sugestie. W celu uzyskania części nieoznakowanych obrazów należy uzyskać tylko sugestie dotyczące tagów początkowych. Będziesz otrzymywać lepsze sugestie dotyczące tagów podczas iteracji w ramach tego procesu.
1. Potwierdź sugerowane Tagi, rozwiązując, że nie są poprawne.
    > [!TIP]
    > Obrazy z sugerowanymi tagami są sortowane według ich niepewności prognozowania (niższe wartości wskazują wyższy poziom pewności). Kolejność sortowania można zmienić z opcją **Sortuj według niepewności** . Jeśli ustawisz kolejność na wartość **wysoki na niski**, możesz najpierw skorygować przewidywania o wysokiej pewności, a następnie szybko potwierdzić niski poziom pewności.
    * W projektach klasyfikacji obrazów można wybrać i potwierdzić Tagi w partiach. Filtruj widok według danego sugerowanego tagu, usuń zaznaczenie nieprawidłowych obrazków, a następnie potwierdź resztę w partii.
        > [!div class="mx-imgBorder"]
        > ![Sugerowane Tagi są wyświetlane w trybie wsadowym dla IC z filtrami.](./media/suggested-tags/ic-batch-mode.png)

        Możesz również użyć sugerowanych tagów w trybie pojedynczego obrazu, wybierając obraz z galerii.

        ![Sugerowane Tagi są wyświetlane w trybie pojedynczego obrazu dla międzyfirmowych.](./media/suggested-tags/ic-individual-image-mode.png)
    * W projektach wykrywania obiektów potwierdzenia wsadowe nie są obsługiwane, ale nadal można filtrować i sortować według sugerowanych tagów, aby poznać bardziej zorganizowane środowisko etykietowania. Miniatury obrazów nieoznakowanych będą zawierać nakładki pól ograniczenia wskazujące lokalizacje sugerowanych tagów. Jeśli nie wybierzesz sugerowanego filtru tagów, wszystkie obrazy nieoznaczone będą wyświetlane bez nakładania się pól ograniczenia.
        > [!div class="mx-imgBorder"]
        > ![Sugerowane Tagi są wyświetlane w trybie wsadowym dla filtrów OD i do.](./media/suggested-tags/od-batch-mode.png)

        Aby potwierdzić znaczniki wykrywania obiektów, należy zastosować je do poszczególnych obrazów w galerii.

        ![Sugerowane Tagi są wyświetlane w trybie pojedynczego obrazu dla elementu OD.](./media/suggested-tags/od-individual-image-mode.png)
1. Uruchom ponownie proces szkolenia.
1. Powtórz powyższe kroki, aż do osiągnięcia odpowiedniej jakości sugestii.

## <a name="next-steps"></a>Następne kroki

Skorzystaj z przewodnika Szybki Start, aby rozpocząć tworzenie i uczenie projektu Custom Vision.

* [Tworzenie klasyfikatora](getting-started-build-a-classifier.md)
* [Tworzenie detektora obiektu](get-started-build-detector.md)