---
title: Dodawanie intencji — LUIS
titleSuffix: Azure Cognitive Services
description: Dodaj intencje do aplikacji LUIS, aby zidentyfikować grupy pytań lub poleceń, które mają takie same intencje.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.service: cognitive-services
ms.openlocfilehash: f2401c032f0fc90024e0049fad5f696b8a184544
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95018943"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Dodawanie intencji w celu określenia zamiaru użytkownika wyrażenia długości

Dodaj [intencje](luis-concept-intent.md) do aplikacji Luis, aby zidentyfikować grupy pytań lub poleceń, które mają ten sam zamiar.

W portalu LUIS, intencje są zarządzane z sekcji **kompilacja** górnego paska nawigacyjnego, a następnie od **intencji** panelu po lewej stronie.

## <a name="add-an-intent-to-your-app"></a>Dodawanie zamiaru do aplikacji

1. Zaloguj się do [portalu Luis](https://www.luis.ai)i wybierz swoją **subskrypcję** i **zasób tworzenia** , aby wyświetlić aplikacje przypisane do tego zasobu.
1. Otwórz aplikację, wybierając jej nazwę na stronie **Moje aplikacje** .
1. Na stronie **intencje** wybierz pozycję **+ Utwórz**.
1. W oknie dialogowym **Tworzenie nowego zamiaru** wprowadź nazwę celu, na przykład `ModifyOrder` , a następnie wybierz pozycję **gotowe**.

    > [!div class="mx-imgBorder"]
    > ![Dodaj cel](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    Celem wymaga [przykładu wyrażenia długości](luis-concept-utterance.md) w celu przewidywania wyrażenia długości w opublikowanym punkcie końcowym przewidywania.

## <a name="add-an-example-utterance"></a>Dodawanie przykładu wypowiedź

Przykład wyrażenia długości to tekstowe przykłady pytań lub poleceń użytkownika. Aby nauczyć się Language Understanding (LUIS) w celu przewidywania tego celu, należy dodać przykład wyrażenia długości do celu. LUIS muszą mieć zakres od 15 do 30 przykład wyrażenia długości, aby rozpocząć zrozumienie zamiaru. Nie dodawaj przykładu wyrażenia długości zbiorczo. Każdy wypowiedź powinien być starannie wybierany w taki sposób, w jaki różni się od przykładów już w zamierzeniu.

1. Na stronie Szczegóły zamiaru wprowadź odpowiednie wypowiedź od użytkowników, takie jak `Deliver a large cheese pizza` w polu tekstowym poniżej nazwy celu, a następnie naciśnij klawisz ENTER.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu strony szczegółów intencji z wyróżnioną pozycją wypowiedź](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png)

    LUIS konwertuje wszystkie wyrażenia długości na małe litery i dodaje spacje wokół [tokenów](luis-language-support.md#tokenization) , takich jak łączniki.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>Błędy przewidywania intencji

Błąd prognozowania intencji jest określany, gdy wypowiedź nie jest przewidziana dla przeszkolonej aplikacji dla zamiaru.

1. Aby znaleźć błędy przewidywania wypowiedź i rozwiązać te problemy, użyj opcji **filtru** nieprawidłowych i niejasnych.

    > [!div class="mx-imgBorder"]
    > ![Aby znaleźć błędy przewidywania wypowiedź i rozwiązać te problemy, użyj opcji filtrowania.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

1. Aby wyświetlić wartość wyniku na stronie Szczegóły intencji, wybierz pozycję **Pokaż wyniki** założeń z menu Opcje **widoku** .

    Gdy filtry i widok są stosowane i występuje przykład wyrażenia długości z błędami, przykładowa lista wypowiedź zawiera wyrażenia długości i problemy.

Każdy wiersz przedstawia ocenę bieżącego szkolenia dla przykładu wypowiedź, najbliższej oceny Rival, która jest różnicą w tych dwóch wskaźnikach.

### <a name="fixing-intents"></a>Ustalanie intencji

Aby dowiedzieć się, jak naprawić błędy prognozowania intencji, użyj [pulpitu nawigacyjnego podsumowanie](luis-how-to-use-dashboard.md). Pulpit nawigacyjny podsumowania zawiera analizę dla ostatniego szkolenia wersji aktywnej i oferuje najważniejsze sugestie dotyczące naprawienia modelu.

## <a name="using-the-contextual-toolbar"></a>Korzystanie z kontekstowego paska narzędzi

Pasek narzędzi kontekstowych zawiera inne akcje:

* Edytuj lub Usuń przykład wypowiedź
* Ponowne przypisanie przykładu wypowiedź do innego celu
* Filtry i widoki: Pokaż tylko wyrażenia długości zawierające odfiltrowane jednostki lub Wyświetl opcjonalne szczegóły
* Wyszukaj na przykład wyrażenia długości

## <a name="train-your-app-after-changing-model-with-intents"></a>Uczenie aplikacji po zmianie modelu z intencjami

Po dodaniu, przeprowadzeniu edycji lub usunięciu zamiarów możesz [nauczyć](luis-how-to-train.md) i [opublikować](luis-how-to-publish-app.md) aplikację, aby zmiany zostały zastosowane do zapytań punktów końcowych. Nie wolno uczenia się po każdej pojedynczej zmianie. Uczenie po grupie zmian.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat dodawania [przykładowej wyrażenia długości](./luis-how-to-add-entities.md) z obiektami.