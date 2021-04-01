---
title: Jak przeprowadzić ocenę w trybie offline — Personalizowanie
titleSuffix: Azure Cognitive Services
description: W tym artykule opisano sposób użycia oceny w trybie offline do mierzenia skuteczności aplikacji oraz analizowania pętli szkoleniowej.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 02/20/2020
ms.openlocfilehash: a473085f9c94ca42a75d01b342d60cc33836b096
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "88244843"
---
# <a name="analyze-your-learning-loop-with-an-offline-evaluation"></a>Analizuj pętlę szkoleniową przy użyciu oceny w trybie offline

Dowiedz się, jak przeprowadzić ocenę w trybie offline i zrozumieć wyniki.

Oceny w trybie offline umożliwiają mierzenie, jak skuteczny program Personalizuj jest porównywany z domyślnym zachowaniem aplikacji, Dowiedz się, jakie funkcje są najbardziej związane z personalizacją i automatycznie odnajduj nowe wartości uczenia maszynowego.

Przeczytaj o [ocenach w trybie offline](concepts-offline-evaluation.md) , aby dowiedzieć się więcej.

## <a name="prerequisites"></a>Wymagania wstępne

* Skonfigurowana pętla personalizacji
* Pętla personalizacji musi mieć reprezentatywną ilość danych — jako przybliżoną zalecamy co najmniej 50 000 zdarzeń w swoich dziennikach w celu uzyskania istotnych wyników oceny. Opcjonalnie możesz również wcześniej wyeksportować pliki _zasad uczenia_ , które można porównywać i testować w tej samej ocenie.

## <a name="run-an-offline-evaluation"></a>Uruchamianie oceny w trybie offline

1. W [Azure Portal](https://azure.microsoft.com/free/cognitive-services)Znajdź zasób personalizacji.
1. W Azure Portal przejdź do sekcji **oceny** i wybierz pozycję **Utwórz ocenę**.
    ![Na Azure Portal przejdź do sekcji * * Evaluations * * i wybierz pozycję * * Utwórz ocenę * *.](./media/offline-evaluation/create-new-offline-evaluation.png)
1. Skonfiguruj następujące wartości:

    * Nazwa ewaluacyjna.
    * Data rozpoczęcia i zakończenia — te daty określają zakres danych do użycia podczas obliczania. Te dane muszą być obecne w dziennikach, jak określono w wartości [przechowywania danych](how-to-settings.md) .
    * Funkcja odnajdywania optymalizacji ma ustawioną **wartość tak**.

    > [!div class="mx-imgBorder"]
    > ![Wybieranie ustawień oceny w trybie offline](./media/offline-evaluation/create-an-evaluation-form.png)

1. Rozpocznij obliczanie, wybierając **przycisk OK**.

## <a name="review-the-evaluation-results"></a>Przejrzyj wyniki oceny

Obliczenia mogą zająć dużo czasu, w zależności od ilości danych do przetworzenia, liczby zasad uczenia do porównania oraz od tego, czy Optymalizacja zażądała.

Po zakończeniu możesz wybrać ocenę z listy ocen, a następnie wybrać opcję **PORÓWNAJ wynik swojej aplikacji z innymi ustawieniami uczenia**. Wybierz tę funkcję, jeśli chcesz zobaczyć, w jaki sposób bieżące zasady uczenia są wykonywane w porównaniu z nowymi zasadami.

1. Zapoznaj się z wydajnością [zasad nauki](concepts-offline-evaluation.md#discovering-the-optimized-learning-policy).

    > [!div class="mx-imgBorder"]
    > [![Przejrzyj wyniki oceny](./media/offline-evaluation/evaluation-results.png)](./media/offline-evaluation/evaluation-results.png#lightbox)

1. Wybierz pozycję **Zastosuj** , aby zastosować zasady usprawniające model dla danych.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o tym, [jak działają oceny w trybie offline](concepts-offline-evaluation.md).
