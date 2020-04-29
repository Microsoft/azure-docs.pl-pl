---
title: Wzorzec Dodawanie dokładności — LUIS
titleSuffix: Azure Cognitive Services
description: Dodawanie szablonów wzorców w celu poprawy dokładności przewidywania w aplikacjach Language Understanding (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: diberry
ms.openlocfilehash: 21afb12bf2464218119ebf52ebd980745e3d731d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76311720"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Jak dodać wzorce, aby poprawić dokładność przewidywania
Gdy aplikacja LUIS otrzymuje punkt końcowy wyrażenia długości, użyj [wzorca](luis-concept-patterns.md) , aby poprawić dokładność przewidywania dla wyrażenia długości, które ujawnia wzorzec w kolejności słów i wyborze wyrazu. Wzorce używają określonej [składni](luis-concept-patterns.md#pattern-syntax) , aby wskazać lokalizację: [jednostki](luis-concept-entity-types.md), [role](luis-concept-roles.md)jednostek i tekst opcjonalny.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

> [!CAUTION]
> Wzorce zawierają tylko obiekty nadrzędne, które są obsługiwane przez maszynę, a nie podskładniki.

## <a name="adding-example-utterances-as-pattern"></a>Dodawanie przykładu wyrażenia długości jako wzorca

Jeśli chcesz dodać wzorzec dla jednostki, _najprostszym_ sposobem jest utworzenie wzorca na stronie szczegółów obiektu. Dzięki temu składnia pasuje do przykładu wypowiedź.

1. W [portalu Luis w wersji zapoznawczej](https://preview.luis.ai)wybierz aplikację ze strony **Moje aplikacje** .
1. Na stronie listy **intencje** wybierz nazwę zamiaru przykładu wypowiedź, z którego chcesz utworzyć szablon wypowiedź.
1. Na stronie Szczegóły zamiaru wybierz wiersz dla przykładowej wypowiedź, który ma być używany jako szablon wypowiedź, a następnie wybierz pozycję **+ Dodaj jako wzorzec** na pasku narzędzi kontekstu.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający Wybieranie przykładowej wypowiedź jako wzorzec szablonu na stronie Szczegóły intencji.](./media/luis-how-to-model-intent-pattern/add-example-utterances-as-pattern-template-utterance-from-intent-detail-page.png)

1. W oknie podręcznym wybierz pozycję **gotowe** na stronie **Potwierdzanie wzorców** . Nie musisz definiować podskładników, ograniczeń ani deskryptorów jednostek. Wystarczy tylko wyświetlić listę jednostek, których dotyczy dana maszyna.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający przykładowy przykład wypowiedź jako wzorzec szablonu na stronie Szczegóły intencji.](./media/luis-how-to-model-intent-pattern/confirm-patterns-from-example-utterance-intent-detail-page.png)

1. Jeśli musisz edytować szablon, na przykład wybierając opcję tekst jako opcjonalny, z `[]` nawiasami kwadratowymi, musisz wprowadzić tę edycję ze strony **wzorców** .

1. Na pasku nawigacyjnym wybierz pozycję **szkolenie** , aby nauczyć aplikację z nowym wzorcem.

## <a name="add-template-utterance-using-correct-syntax"></a>Dodaj wypowiedź szablonu przy użyciu poprawnej składni

1. Otwórz aplikację, wybierając jej nazwę na stronie **Moje aplikacje** , a następnie wybierz pozycję **wzorce** w lewym panelu, w obszarze **Popraw wydajność aplikacji**.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający listę wzorców](./media/luis-how-to-model-intent-pattern/patterns-1.png)

1. Wybierz odpowiedni cel dla wzorca.

1. W polu tekstowym szablon wpisz wypowiedź szablonu i wybierz ENTER. Jeśli chcesz wprowadzić nazwę jednostki, użyj poprawnej składni jednostki wzorca. Rozpocznij składnię jednostki za `{`pomocą polecenia. Zostanie wyświetlona lista jednostek. Wybierz poprawną jednostkę.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu jednostki dla wzorca](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Jeśli jednostka zawiera [rolę](luis-concept-roles.md), wskaż rolę z pojedynczym dwukropkiem, `:`po nazwie jednostki, takiej jak. `{Location:Origin}` Lista ról dla jednostek zostanie wyświetlona na liście. Wybierz rolę, a następnie wybierz klawisz ENTER.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu jednostki z rolą](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Po wybraniu prawidłowej jednostki Zakończ wprowadzanie wzorca, a następnie wybierz klawisz ENTER. Gdy skończysz wprowadzać wzorce, [uczenie](luis-how-to-train.md) aplikacji.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający wprowadzony wzorzec z obydwoma typami jednostek](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="train-your-app-after-changing-model-with-patterns"></a>Uczenie aplikacji po zmianie modelu ze wzorcami
Po dodaniu, edycji, usunięciu lub ponownym przypisaniu wzorca należy [pouczenie](luis-how-to-train.md) i [opublikować](luis-how-to-publish-app.md) aplikację, aby zmiany dotyczyły zapytań punktów końcowych.

<a name="search-patterns"></a>
<a name="edit-a-pattern"></a>
<a name="reassign-individual-pattern-to-different-intent"></a>
<a name="reassign-several-patterns-to-different-intent"></a>
<a name="delete-a-single-pattern"></a>
<a name="delete-several-patterns"></a>
<a name="filter-pattern-list-by-entity"></a>
<a name="filter-pattern-list-by-intent"></a>
<a name="remove-entity-or-intent-filter"></a>
<a name="add-pattern-from-existing-utterance-on-intent-or-entity-page"></a>

## <a name="use-contextual-toolbar"></a>Użyj kontekstowego paska narzędzi

Kontekstowy pasek narzędzi powyżej listy wzorców pozwala:

* Wyszukaj wzorce
* Edytowanie wzorca
* Przypisz ponownie indywidualny wzorzec do innego celu
* Przypisz ponownie kilka wzorców do innego celu
* Delete-a-Single-wzorzec
* Usuń kilka wzorców
* Filtruj listę wzorców według jednostek
* Filtr-wzorzec-lista według intencji
* Usuń filtr jednostki lub celu
* Dodaj wzorzec z istniejącej wypowiedź na stronie intencji lub jednostki

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [utworzyć wzorzec](luis-tutorial-pattern.md) ze wzorcem. dowolne role i z samouczkiem.
* Dowiedz się, jak [szkolić](luis-how-to-train.md) aplikację.
