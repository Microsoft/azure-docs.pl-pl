---
title: Dodawanie intencji — LUIS
titleSuffix: Azure Cognitive Services
description: Dodaj intencje do aplikacji LUIS, aby zidentyfikować grupy pytań lub poleceń, które mają takie same intencje.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: diberry
ms.service: cognitive-services
ms.openlocfilehash: b2cb7494ae3d26fa14bef906b8f5222b9dbc70e1
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83584978"
---
# <a name="add-intents-to-determine-user-intention-of-utterances"></a>Dodawanie intencji w celu określenia zamiaru użytkownika wyrażenia długości

Dodaj [intencje](luis-concept-intent.md) do aplikacji Luis, aby zidentyfikować grupy pytań lub poleceń, które mają ten sam zamiar.

Intencje są zarządzane z sekcji **kompilacja** górnego paska nawigacyjnego, a następnie od **intencji**panelu po lewej stronie.

## <a name="add-intent"></a>Dodawanie intencji

1. Zaloguj się do [portalu Luis](https://www.luis.ai)i wybierz swoją **subskrypcję** i **zasób tworzenia** , aby wyświetlić aplikacje przypisane do tego zasobu.
1. Otwórz aplikację, wybierając jej nazwę na stronie **Moje aplikacje** .
1. Na stronie **intencje** wybierz pozycję **+ Utwórz**.
1. W oknie dialogowym **Tworzenie nowego zamiaru** wprowadź nazwę celu, na przykład `ModifyOrder` , a następnie wybierz pozycję **gotowe**.

    > [!div class="mx-imgBorder"]
    > ![Dodaj cel](./media/luis-how-to-add-intents/Addintent-dialogbox.png)

    Wymaga przykład wyrażenia długości.

## <a name="add-an-example-utterance"></a>Dodawanie przykładu wypowiedź

Przykład wyrażenia długości to tekstowe przykłady pytań lub poleceń użytkownika. Aby nauczyć się Language Understanding (LUIS) w celu przewidywania tego celu, należy dodać przykład wyrażenia długości do celu. LUIS muszą mieć zakres od 15 do 30 przykład wyrażenia długości, aby rozpocząć zrozumienie zamiaru. Nie dodawaj przykładu wyrażenia długości zbiorczo. Każdy wypowiedź powinien być starannie wybierany w taki sposób, w jaki różni się od przykładów już w zamierzeniu.

1. Na stronie Szczegóły zamiaru wprowadź odpowiednie wypowiedź od użytkowników, takie jak `Deliver a large cheese pizza` w polu tekstowym poniżej nazwy celu, a następnie naciśnij klawisz ENTER.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu strony szczegółów intencji z wyróżnioną pozycją wypowiedź](./media/luis-how-to-add-intents/add-new-utterance-to-intent.png)

    LUIS konwertuje wszystkie wyrażenia długości na małe litery i dodaje spacje wokół [tokenów](luis-language-support.md#tokenization) , takich jak łączniki.

<a name="#intent-prediction-discrepancy-errors"></a>

## <a name="intent-prediction-errors"></a>Błędy przewidywania intencji

Przykład wypowiedź w zamiarach może mieć błąd przewidywania intencji między zamiarem, a przykładem wypowiedź jest obecnie w i intencją określoną podczas szkolenia.

Aby znaleźć błędy przewidywania wypowiedź i rozwiązać te problemy, użyj opcji **filtru** nieprawidłowych i niejasnych w połączeniu z opcją **Widok** **widoku szczegółowego**.

![Aby znaleźć błędy przewidywania wypowiedź i rozwiązać te problemy, użyj opcji filtrowania.](./media/luis-how-to-add-intents/find-intent-prediction-errors.png)

Gdy filtry i widok są stosowane i występuje przykład wyrażenia długości z błędami, przykładowa lista wypowiedź zawiera wyrażenia długości i problemy.

> [!div class="mx-imgBorder"]
> ![! [Kiedy filtry i widok są stosowane i występuje przykład wyrażenia długości z błędami, przykładowa lista wypowiedź zawiera wyrażenia długości i problemy.] (./media/luis-how-to-add-intents/find-errors-in-utterances.png)](./media/luis-how-to-add-intents/find-errors-in-utterances.png#lightbox)

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

Dowiedz się więcej na temat dodawania [przykładowej wyrażenia długości](luis-how-to-add-example-utterances.md) z obiektami.
