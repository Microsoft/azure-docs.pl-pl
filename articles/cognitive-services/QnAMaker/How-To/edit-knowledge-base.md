---
title: Edytowanie bazy wiedzy — QnA Maker
description: QnA Maker umożliwia zarządzanie zawartością bazy wiedzy przez zapewnienie łatwego w użyciu funkcji edycji.
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 3cf5dcedd4d2cfa82fda002b71abca1e5bea4b18
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80756717"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>Edytuj pary QnA w bazie wiedzy

QnA Maker umożliwia zarządzanie zawartością bazy wiedzy przez zapewnienie łatwego w użyciu funkcji edycji.

Pary QnA są dodawane ze źródła danych, takiego jak plik lub adres URL, lub dodawane jako źródło redakcyjne. Źródło redakcyjne wskazuje, że para QnA została dodana ręcznie w portalu QnA. Wszystkie pary QnA są dostępne do edycji.

## <a name="add-an-editorial-qna-pair"></a>Dodaj parę redakcyjną QnA
1. Zaloguj się do [portalu QNA](https://www.qnamaker.ai/), a następnie wybierz bazę wiedzy, do której zostanie dodana para QNA.
1. Na stronie **Edytuj** bazy wiedzy wybierz pozycję **Dodaj parę QNA** , aby dodać nową parę QNA.

1. W wierszu Nowa para QnA Dodaj wymagane pola **pytania** i **odpowiedzi** . Pozostałe pola są opcjonalne. Wszystkie pola można zmienić w dowolnym momencie.

1. Opcjonalnie Dodaj **alternatywne sformułowanie**. Alternatywne sformułowanie to jakakolwiek forma pytania, która znacznie różni się od oryginalnego pytania, ale powinna mieć taką samą odpowiedź.

    Po opublikowaniu bazy wiedzy i włączeniu [aktywnej uczenia](use-active-learning.md) QNA Maker gromadzi zaakceptowane opcje korekty. Te wybory są wybierane w celu zwiększenia dokładności przewidywania.

1. Opcjonalnie Dodaj **metadane**. Aby wyświetlić metadane, wybierz pozycję **Wyświetl opcje** w menu kontekstowym. Metadane zawierają filtry do odpowiedzi, które zapewnia aplikacja kliencka, taka jak rozmowa bot.

1. Opcjonalnie możesz dodać **monity**monitujące. Monity uzupełniające zawierają dodatkowe ścieżki konwersacji do aplikacji klienckiej, które mają być widoczne dla użytkownika.

1. Wybierz pozycję **Zapisz i poszkol** , aby zobaczyć przewidywania, w tym nową parę QNA.

## <a name="edit-a-qna-pair"></a>Edytowanie pary QnA

Wszystkie pola w dowolnej parze QnA można edytować niezależnie od oryginalnego źródła danych. Niektóre pola mogą być niewidoczne z powodu bieżących ustawień **opcji widoku** , które znajdują się na pasku narzędzi kontekstu.

## <a name="delete-a-qna-pair"></a>Usuwanie pary QnA

Aby usunąć QnA, kliknij ikonę **Usuń** znajdującą się po prawej stronie wiersza QNA. Jest to trwała operacja. Nie można jej cofnąć. Przed usunięciem zestawów Rozważ wyeksportowanie bazy wiedzy ze strony **publikowania** .

![Usuń parę QnA](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="find-the-qna-pair-id"></a>Znajdź identyfikator pary QnA

Jeśli konieczne jest znalezienie identyfikatora pary QnA, można go znaleźć w dwóch miejscach:

* Umieść kursor na ikonie usuwania w wierszu pary QnA, który Cię interesuje. Tekst aktywowany zawiera identyfikator pary QnA.
* Wyeksportuj bazę wiedzy. Każda para QnA w bazie wiedzy zawiera identyfikator pary QnA.

## <a name="add-alternate-questions"></a>Dodaj alternatywne pytania

Dodaj alternatywne pytania do istniejącej pary QnA, aby zwiększyć prawdopodobieństwo dopasowania do zapytania użytkownika.

![Dodaj alternatywne pytania](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="linking-qna-pairs"></a>Łączenie par QnA

Łącząc pary QnA z [monitami](multiturn-conversation.md). Jest to połączenie logiczne między parami QnA, zarządzane na poziomie bazy wiedzy. Monity monitujące można edytować w portalu QnA Maker.

Nie można połączyć par QnA w metadanych odpowiedzi.

## <a name="add-metadata"></a>Dodaj metadane

Dodaj zestawy metadanych, zaznaczając najpierw **Opcje widoku**, a następnie wybierając **Pokaż metadane**. Spowoduje to wyświetlenie kolumny metadanych. Następnie wybierz **+** znak, aby dodać zestaw metadanych. Ten zestaw składa się z jednego klucza i jednej wartości.

## <a name="save-changes-to-the-qna-pairs"></a>Zapisz zmiany w parach QnA

Okresowo wybieraj pozycję **Zapisz i pouczenie** po wprowadzeniu zmian, aby uniknąć utraty zmian.

![Dodaj metadane](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Współpraca nad bazą wiedzy](./collaborate-knowledge-base.md)

* [Zarządzanie zasobami platformy Azure używanymi przez QnA Maker](set-up-qnamaker-service-azure.md)