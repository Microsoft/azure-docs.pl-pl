---
title: Wzorzec Dodawanie dokładności — LUIS
titleSuffix: Azure Cognitive Services
description: Dodawanie szablonów wzorców w celu poprawy dokładności przewidywania w aplikacjach Language Understanding (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/17/2020
ms.openlocfilehash: 914ca77b18d0469c3ea926848be4c60aab04c9c6
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91539108"
---
# <a name="how-to-add-patterns-to-improve-prediction-accuracy"></a>Jak dodać wzorce, aby poprawić dokładność przewidywania
Gdy aplikacja LUIS otrzymuje punkt końcowy wyrażenia długości, użyj [wzorca](luis-concept-patterns.md) , aby poprawić dokładność przewidywania dla wyrażenia długości, które ujawnia wzorzec w kolejności słów i wyborze wyrazu. Wzorce używają określonej [składni](luis-concept-patterns.md#pattern-syntax) , aby wskazać lokalizację: [jednostki](luis-concept-entity-types.md), [role](luis-concept-roles.md)jednostek i tekst opcjonalny.

> [!CAUTION]
> Wzorce obejmują tylko nadrzędne jednostki uczenia maszynowego, a nie podjednostki.

## <a name="add-template-utterance-using-correct-syntax"></a>Dodaj wypowiedź szablonu przy użyciu poprawnej składni

1. Zaloguj się do [portalu Luis](https://www.luis.ai)i wybierz swoją **subskrypcję** i **zasób tworzenia** , aby wyświetlić aplikacje przypisane do tego zasobu.
1. Otwórz aplikację, wybierając jej nazwę na stronie **Moje aplikacje** .
1. Wybierz pozycję **wzorce** w lewym panelu, w obszarze **Popraw wydajność aplikacji**.

1. Wybierz odpowiedni cel dla wzorca.

1. W polu tekstowym szablon wpisz wypowiedź szablonu i wybierz ENTER. Jeśli chcesz wprowadzić nazwę jednostki, użyj poprawnej składni jednostki wzorca. Rozpocznij składnię jednostki za pomocą polecenia `{` . Zostanie wyświetlona lista jednostek. Wybierz poprawną jednostkę.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu jednostki dla wzorca](./media/luis-how-to-model-intent-pattern/patterns-3.png)

    Jeśli jednostka zawiera [rolę](luis-concept-roles.md), wskaż rolę z pojedynczym dwukropkiem, `:` po nazwie jednostki, takiej jak `{Location:Origin}` . Lista ról dla jednostek zostanie wyświetlona na liście. Wybierz rolę, a następnie wybierz klawisz ENTER.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu jednostki z rolą](./media/luis-how-to-model-intent-pattern/patterns-4.png)

    Po wybraniu prawidłowej jednostki Zakończ wprowadzanie wzorca, a następnie wybierz klawisz ENTER. Gdy skończysz wprowadzać wzorce, [uczenie](luis-how-to-train.md) aplikacji.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający wprowadzony wzorzec z obydwoma typami jednostek](./media/luis-how-to-model-intent-pattern/patterns-5.png)

## <a name="create-a-patternany-entity"></a>Tworzenie wzorca. dowolna jednostka

[Wzorzec. wszystkie](luis-concept-entity-types.md) jednostki są prawidłowe tylko w [wzorcach](luis-how-to-model-intent-pattern.md), a nie na przykład wyrażenia długości. Ten typ jednostki ułatwia LUIS znalezienie końca jednostek o różnej długości i wyborze wyrazu. Ponieważ ta jednostka jest używana we wzorcu, LUIS wie, gdzie koniec jednostki znajduje się w szablonie wypowiedź.

1. Zaloguj się do [portalu Luis](https://www.luis.ai)i wybierz swoją **subskrypcję** i **zasób tworzenia** , aby wyświetlić aplikacje przypisane do tego zasobu.
1. Otwórz aplikację, wybierając jej nazwę na stronie **Moje aplikacje** .
1. W sekcji **kompilacja** wybierz pozycję **jednostki** w lewym panelu, a następnie wybierz pozycję **+ Utwórz**.

1. W oknie dialogowym **Wybierz typ jednostki** wprowadź nazwę jednostki w polu **Nazwa** , a następnie wybierz pozycję **wzorzec. dowolny** **Typ** , a następnie wybierz pozycję **Utwórz**.

    Po [utworzeniu wypowiedź wzorca](luis-how-to-model-intent-pattern.md) przy użyciu tej jednostki jednostka jest wyodrębniana z połączonym algorytmem uczenia maszynowego i dopasowywania tekstu.

## <a name="adding-example-utterances-as-pattern"></a>Dodawanie przykładu wyrażenia długości jako wzorca

Jeśli chcesz dodać wzorzec dla jednostki, _najprostszym_ sposobem jest utworzenie wzorca na stronie szczegółów obiektu. Dzięki temu składnia pasuje do przykładu wypowiedź.

1. Zaloguj się do [portalu Luis](https://www.luis.ai)i wybierz swoją **subskrypcję** i **zasób tworzenia** , aby wyświetlić aplikacje przypisane do tego zasobu.
1. Otwórz aplikację, wybierając jej nazwę na stronie **Moje aplikacje** .
1. Na stronie listy **intencje** wybierz nazwę zamiaru przykładu wypowiedź, z którego chcesz utworzyć szablon wypowiedź.
1. Na stronie Szczegóły zamiaru wybierz wiersz dla przykładowej wypowiedź, który ma być używany jako szablon wypowiedź, a następnie wybierz pozycję **+ Dodaj jako wzorzec** na pasku narzędzi kontekstu.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający Wybieranie przykładowej wypowiedź jako wzorzec szablonu na stronie Szczegóły intencji.](./media/luis-how-to-model-intent-pattern/add-example-utterances-as-pattern-template-utterance-from-intent-detail-page.png)

    Wypowiedź musi zawierać jednostkę, aby można było utworzyć wzorzec z wypowiedź.

1. W oknie podręcznym wybierz pozycję **gotowe** na stronie **Potwierdzanie wzorców** . Nie musisz definiować podjednostek ani funkcji jednostek. Wystarczy tylko wyświetlić listę jednostek uczenia maszynowego.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający przykładowy przykład wypowiedź jako wzorzec szablonu na stronie Szczegóły intencji.](./media/luis-how-to-model-intent-pattern/confirm-patterns-from-example-utterance-intent-detail-page.png)

1. Jeśli musisz edytować szablon, na przykład wybierając opcję tekst jako opcjonalny, z `[]` nawiasami kwadratowymi, musisz wprowadzić tę edycję ze strony **wzorców** .

1. Na pasku nawigacyjnym wybierz pozycję **szkolenie** , aby nauczyć aplikację z nowym wzorcem.

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
