---
title: Uczenie aplikacji — LUIS
titleSuffix: Azure Cognitive Services
description: Szkolenie to proces uczenia wersji aplikacji Language Understanding (LUIS) w celu usprawnienia jej interpretacji języka naturalnego. Uczenie aplikacji LUIS po aktualizacji modelu, takich jak dodawanie, edytowanie, etykietowanie lub usuwanie jednostek, intencje lub wyrażenia długości.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 11/15/2019
ms.openlocfilehash: 47b006932aace3149dd94e136e334c1b6e5bfcef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "98762703"
---
# <a name="train-your-active-version-of-the-luis-app"></a>Uczenie aktywnej wersji aplikacji LUIS

Szkolenie to proces uczenia aplikacji Language Understanding (LUIS) w celu usprawnienia jej interpretacji języka naturalnego. Uczenie aplikacji LUIS po aktualizacji modelu, takich jak dodawanie, edytowanie, etykietowanie lub usuwanie jednostek, intencje lub wyrażenia długości.

Szkolenie i [testowanie](luis-concept-test.md) aplikacji jest procesem iteracyjnym. Po wytrenowaniu aplikacji usługi LUIS należy ją przetestować przy użyciu przykładowych wypowiedzi, aby zobaczyć, czy intencje i jednostki są prawidłowo rozpoznawane. Jeśli tak nie jest, wprowadź ponownie aktualizacje aplikacji LUIS, uczenia i testowania.

Szkolenia są stosowane do aktywnej wersji portalu LUIS.

## <a name="how-to-train-interactively"></a>Jak szkolić interaktywnie

Aby rozpocząć proces iteracyjny w [portalu Luis](https://www.luis.ai), najpierw musisz przeprowadzić uczenie aplikacji Luis co najmniej raz. Przed szkoleniem upewnij się, że każdy z zamiarów ma co najmniej jeden wypowiedź.

1. Uzyskaj dostęp do aplikacji, wybierając jej nazwę na stronie **Moje aplikacje** .

1. W aplikacji wybierz pozycję **szkolenie** w górnym panelu.

1. Po zakończeniu szkolenia w górnej części przeglądarki pojawia się powiadomienie.

## <a name="training-date-and-time"></a>Data i godzina szkolenia

Data i godzina szkolenia to GMT + 2.

## <a name="train-with-all-data"></a>Uczenie ze wszystkimi danymi

Szkolenie zużywa niewielką część próbkowania negatywnego. Możesz użyć wszystkich dostępnych danych zamiast portalu lub interfejsu API. 

### <a name="using-the-luis-portal"></a>Korzystanie z portalu LUIS

Zaloguj się do [portalu Luis](https://www.luis.ai/) i kliknij aplikację. Wybierz pozycję **Zarządzaj** w górnej części ekranu, a następnie wybierz pozycję **Ustawienia** , a następnie Włącz lub wyłącz opcję **szkoleniowe użycie-deterministyczna** . Po wyłączeniu szkolenie będzie używać wszystkich dostępnych danych.

![Przycisk służący do włączania lub wyłączania niedeterministycznych szkoleń](./media/non-determinstic-training.png)

### <a name="using-the-version-settings-api"></a>Korzystanie z interfejsu API ustawień wersji

Aby wyłączyć tę funkcję, użyj [interfejsu API ustawień wersji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) z `UseAllTrainingData` ustawioną wartością true.

## <a name="unnecessary-training"></a>Niepotrzebne szkolenie

Nie ma potrzeby uczenia się po każdej pojedynczej zmianie. Szkolenie należy wykonać po zastosowaniu grupy zmian do modelu i następnym kroku, który ma zostać przetestowany lub opublikowany. Jeśli nie ma potrzeby testowania ani publikowania, szkolenie nie jest konieczne.

## <a name="training-with-the-rest-apis"></a>Szkolenie przy użyciu interfejsów API REST

Szkolenie w portalu LUIS to pojedynczy krok po naciśnięciu przycisku **uczenie** . Szkolenie z użyciem interfejsów API REST jest procesem dwuetapowym. Pierwszy polega na [zażądaniu szkolenia](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c45) przy użyciu protokołu HTTP POST. Następnie Zażądaj [stanu szkolenia](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c46) przy użyciu protokołu HTTP GET.

Aby dowiedzieć się, kiedy szkolenie zostało zakończone, musisz sondować stan do momentu, w którym wszystkie modele zostały pomyślnie przeszkolone.

## <a name="next-steps"></a>Następne kroki

* [Testowanie interakcyjne](luis-interactive-test.md)
* [Testowanie wsadowe](luis-how-to-batch-test.md)
