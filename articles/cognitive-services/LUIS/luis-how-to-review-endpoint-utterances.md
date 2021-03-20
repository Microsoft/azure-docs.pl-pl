---
title: Przeglądanie użytkownika wyrażenia długości — LUIS
titleSuffix: Azure Cognitive Services
description: Przejrzyj wyrażenia długości przechwycone przez aktywną naukę, aby wybrać pozycję zamierzenia i oznaczyć jednostki do odczytu-World wyrażenia długości; Akceptowanie zmian, uczenie i publikowanie.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 12/08/2020
ms.openlocfilehash: ea2b44d05d25756a16b6b84f0734966b1f579848
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97007606"
---
# <a name="how-to-improve-the-luis-app-by-reviewing-endpoint-utterances"></a>Jak ulepszyć aplikację LUIS, przeglądając punkt końcowy wyrażenia długości

Proces recenzowania punktów końcowych wyrażenia długości pod kątem prawidłowych prognoz jest nazywany [aktywną nauką](luis-concept-review-endpoint-utterances.md). Usługa Active Learning przechwytuje zapytania punktów końcowych i wybiera punkt końcowy użytkownika wyrażenia długości, że nie ma pewności. Te wyrażenia długości są przeglądane w celu wybrania zamiar i oznaczenie jednostek dla rzeczywistych wyrażenia długości. Zaakceptuj zmiany w przykładowych wypowiedziach, a następnie wytrenuj i opublikuj aplikację. LUIS następnie identyfikuje wyrażenia długości dokładniej.

## <a name="log-user-queries-to-enable-active-learning"></a>Rejestruj zapytania użytkownika w celu włączenia aktywnego uczenia

Aby włączyć aktywną naukę, należy zarejestrować zapytania użytkownika. Jest to realizowane przez wywołanie [zapytania punktu końcowego](luis-get-started-create-app.md#query-the-v3-api-prediction-endpoint) z `log=true` parametrem QueryString i wartością.

Użyj portalu LUIS, aby utworzyć poprawną kwerendę punktu końcowego.

1. Zaloguj się do [portalu Luis](https://www.luis.ai)i wybierz swoją **subskrypcję** i **zasób tworzenia** , aby wyświetlić aplikacje przypisane do tego zasobu.
1. Otwórz aplikację, wybierając jej nazwę na stronie **Moje aplikacje** .
1. Przejdź do sekcji **Zarządzanie** , a następnie wybierz pozycję **zasoby platformy Azure**.
1. Dla przypisanego zasobu przewidywania wybierz pozycję **Zmień parametry zapytania**.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawia łącze Zmień parametry zapytania.](./media/luis-tutorial-review-endpoint-utterances/azure-portal-change-query-url-settings.png)

1. Przełącz pozycję **Zapisz dzienniki** i Zapisz, wybierając pozycję **gotowe**.

    > [!div class="mx-imgBorder"]
    > ![Użyj portalu LUIS do zapisania dzienników, które są wymagane do aktywnego uczenia.](./media/luis-tutorial-review-endpoint-utterances/luis-portal-manage-azure-resource-save-logs.png)

     Ta akcja powoduje zmianę przykładowego adresu URL przez dodanie `log=true` parametru QueryString. Skopiuj i użyj zmienionego przykładowego adresu URL zapytania podczas tworzenia zapytań prognozowania do punktu końcowego środowiska uruchomieniowego.

## <a name="correct-intent-predictions-to-align-utterances"></a>Popraw przewidywania zamierzeń, aby wyrównać wyrażenia długości

Każdy wypowiedź ma sugerowane zamierzone w kolumnie **cel** .

> [!div class="mx-imgBorder"]
> [![Zapoznaj się z punktem końcowym wyrażenia długości, że LUIS nie ma pewności](./media/label-suggested-utterances/review-endpoint-utterances.png)](./media/label-suggested-utterances/review-endpoint-utterances.png#lightbox)

Jeśli zgadzasz się z tym zamiarem, zaznacz znacznik wyboru. Jeśli nie zgadzasz się z sugestią, wybierz odpowiednią opcję z listy rozwijanej założeń, a następnie zaznacz pole wyboru na prawo od dopasowanego zamiaru. Po zaznaczeniu znacznika wyboru wypowiedź jest przenoszony do zamiaru i usunięty z listy **wyrażenia długości punktu końcowego** .

> [!TIP]
> Ważne jest, aby przejść do strony Szczegóły intencji, aby przejrzeć i skorygować przewidywania jednostek ze wszystkich przykładów wyrażenia długości z listy **wyrażenia długości punktu końcowego przeglądu** .

## <a name="delete-utterance"></a>Usuń wypowiedź

Każdy wypowiedź można usunąć z listy przegląd. Po usunięciu nie zostanie on wyświetlony na liście ponownie. Jest to prawdziwe, nawet jeśli użytkownik wprowadzi ten sam wypowiedź z punktu końcowego.

Jeśli nie masz pewności, czy należy usunąć wypowiedź, przenieś je do zamiaru brak lub Utwórz nowe przeznaczenie, takie jak `miscellaneous` i Przenieś wypowiedź do tego celu.

## <a name="disable-active-learning"></a>Wyłącz aktywną naukę

Aby wyłączyć aktywną naukę, nie Rejestruj zapytań użytkownika. Jest to realizowane przez ustawienie [zapytania punktu końcowego](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) z `log=false` parametrem QueryString i wartością lub nieużywaniem wartości QueryString, ponieważ wartością domyślną jest false.

## <a name="next-steps"></a>Następne kroki

Aby sprawdzić, jak wydajność zwiększa się po oznaczeniu sugerowanych wyrażenia długości, możesz uzyskać dostęp do konsoli testowej, wybierając **test** w górnym panelu. Aby uzyskać instrukcje dotyczące testowania aplikacji za pomocą konsoli testowej, zobacz [uczenie i testowanie aplikacji](luis-interactive-test.md).
