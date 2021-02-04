---
title: Edytowanie bazy wiedzy — QnA Maker
description: QnA Maker umożliwia zarządzanie zawartością bazy wiedzy przez zapewnienie łatwego w użyciu funkcji edycji.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 07/16/2020
ms.openlocfilehash: f5d7094920a21af630e10aec2aa759ce9c505050
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550588"
---
# <a name="edit-qna-pairs-in-your-knowledge-base"></a>Edytuj pary QnA w bazie wiedzy

QnA Maker umożliwia zarządzanie zawartością bazy wiedzy przez zapewnienie łatwego w użyciu funkcji edycji.

Pary QnA są dodawane ze źródła danych, takiego jak plik lub adres URL, lub dodawane jako źródło redakcyjne. Źródło redakcyjne wskazuje, że para QnA została dodana ręcznie w portalu QnA. Wszystkie pary QnA są dostępne do edycji.

<a name="add-an-editorial-qna-set"></a>

## <a name="question-and-answer-pairs"></a>Pary pytań i odpowiedzi

Baza wiedzy składa się z par pytań i odpowiedzi (QnA).  Każda para ma jedną odpowiedź, a para zawiera wszystkie informacje skojarzone z tą _odpowiedzią_. Odpowiedź może być luźno podobna do wiersza bazy danych lub wystąpienia struktury danych. **Wymagane** ustawienia w parze pytań i odpowiedzi (QNA) są następujące:

* **pytanie** — tekst zapytania użytkownika, używany do QNA Maker uczenia maszynowego w celu wyrównania z tekstem pytania użytkownika z różnymi wyrazami, ale z tą samą odpowiedzią
* odpowiedź **— odpowiedź** pary jest odpowiedzią zwracaną, gdy zapytanie użytkownika jest dopasowane do skojarzonego pytania

Każda para jest reprezentowana przez **Identyfikator**.

**Opcjonalne** ustawienia dla pary obejmują:

* **Alternatywne formy pytania** — pomaga QNA Maker zwrócić poprawną odpowiedź w celu uzyskania większej liczby pytań frazy
* **Metadane**: metadane są tagami skojarzonymi z parą QNA i są reprezentowane jako pary klucz-wartość. Tagi metadanych są używane do filtrowania par QnA i ograniczają zestaw, w jakim jest wykonywane Dopasowywanie zapytań.
* Wyświetlanie wielowierszowych **ekranów**, służących do kontynuowania konwersacji z obsługą wielodostępności

![QnA Maker bazy wiedzy](../media/qnamaker-concepts-knowledgebase/knowledgebase.png)

## <a name="add-an-editorial-qna-pair"></a>Dodaj parę redakcyjną QnA

1. Zaloguj się do [portalu QNA](https://www.qnamaker.ai/), a następnie wybierz bazę wiedzy, do której zostanie dodana para QNA.
1. Na stronie **Edytuj** bazy wiedzy wybierz pozycję **Dodaj parę QNA** , aby dodać nową parę QNA.

    > [!div class="mx-imgBorder"]
    > ![Dodaj parę QnA](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

1. W wierszu Nowa para QnA Dodaj wymagane pola pytania i odpowiedzi. Pozostałe pola są opcjonalne. Wszystkie pola można zmienić w dowolnym momencie.

1. Opcjonalnie Dodaj **[alternatywne sformułowanie](../Quickstarts/add-question-metadata-portal.md#add-additional-alternatively-phrased-questions)**. Alternatywne sformułowanie to jakakolwiek forma pytania, która znacznie różni się od oryginalnego pytania, ale powinna mieć taką samą odpowiedź.

    Po opublikowaniu bazy wiedzy i włączeniu [aktywnej uczenia](use-active-learning.md) QNA Maker gromadzi zaakceptowane opcje korekty. Te wybory są wybierane w celu zwiększenia dokładności przewidywania.

1. Opcjonalnie Dodaj **[metadane](../Quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)**. Aby wyświetlić metadane, wybierz pozycję **Wyświetl opcje** w menu kontekstowym. Metadane zawierają filtry do odpowiedzi, które zapewnia aplikacja kliencka, taka jak rozmowa bot.

1. Opcjonalnie możesz dodać **[monity](multiturn-conversation.md)** monitujące. Monity uzupełniające zawierają dodatkowe ścieżki konwersacji do aplikacji klienckiej, które mają być widoczne dla użytkownika.

1. Wybierz pozycję **Zapisz i poszkol** , aby zobaczyć przewidywania, w tym nową parę QNA.

## <a name="rich-text-editing-for-answer"></a>Edytowanie tekstu sformatowanego na potrzeby odpowiedzi

Edycja tekstu odpowiedzi w postaci tekstu sformatowanego umożliwia przeprowadzenie promocji stylów z prostego paska narzędzi.

1. Zaznacz obszar tekstu odpowiedzi, pasek narzędzi edytora tekstu sformatowanego zostanie wyświetlony w wierszu pary QnA.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający Edytor tekstu sformatowanego z pytaniem i odpowiedzią wiersza pary QnA.](../media/qnamaker-how-to-edit-kb/rich-text-control-qna-pair-row.png)

    Wszystkie teksty znajdujące się już w odpowiedzi są wyświetlane prawidłowo, ponieważ użytkownik zobaczy ją z bot.

1. Edytuj tekst. Wybierz pozycję formatowanie funkcji z paska narzędzi do edycji tekstu sformatowanego lub użyj funkcji przełączania, aby przełączyć się na składnię promocji.

    > [!div class="mx-imgBorder"]
    > ![Użyj edytora tekstu sformatowanego do pisania i formatowania tekstu i zapisywania jako promocji.](../media/qnamaker-how-to-edit-kb/rich-text-display-image.png)

    |Funkcje edytora tekstu sformatowanego|Skrót klawiaturowy|
    |--|--|
    |Przełączaj się między edytorem tekstu sformatowanego i zaawansowaniem. `</>`|CTRL+M|
    |Zapis. **B**|Rob + LB|
    |Kursywa, oznaczone kursywą **_I_**|CTRL+I|
    |Listy nieuporządkowane||
    |Lista uporządkowana||
    |Styl akapitu||
    |Obraz — umożliwia dodanie obrazu dostępnego z publicznego adresu URL.|CTRL + G|
    |Dodaj link do publicznie dostępnego adresu URL.|CTRL + K|
    |Emotikon — Dodaj z wybranych emotikonów.|CTRL + E|
    |Menu Zaawansowane — Cofnij|CTRL+Z|
    |Menu Zaawansowane — wykonaj ponownie|CTRL+Y|

1. Dodaj obraz do odpowiedzi przy użyciu ikony obrazu na pasku narzędzi tekstu sformatowanego. Edytor w miejscu wymaga dostępnego publicznie adresu URL i tekstu zastępczego obrazu.


    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawia Edytor w miejscu z adresem URL obrazu dostępnego publicznie i alternatywnym tekstem dla wprowadzonego obrazu.](../media/qnamaker-how-to-edit-kb/add-image-url-alternate-text.png)

1. Dodaj link do adresu URL, wybierając tekst w odpowiedzi, a następnie wybierając ikonę linku na pasku narzędzi lub wybierając ikonę link na pasku narzędzi, a następnie wprowadzając nowy tekst i adres URL.

    > [!div class="mx-imgBorder"]
    > ![Użyj edytora tekstu sformatowanego, Dodaj dostępny publicznie obraz i jego tekst ALTERNATYWny.](../media/qnamaker-how-to-edit-kb/add-link-to-answer-rich-text-editor.png)

## <a name="edit-a-qna-pair"></a>Edytowanie pary QnA

Wszystkie pola w dowolnej parze QnA można edytować niezależnie od oryginalnego źródła danych. Niektóre pola mogą być niewidoczne z powodu bieżących ustawień **opcji widoku** , które znajdują się na pasku narzędzi kontekstu.

## <a name="delete-a-qna-pair"></a>Usuwanie pary QnA

Aby usunąć QnA, kliknij ikonę **Usuń** znajdującą się po prawej stronie wiersza QNA. Jest to trwała operacja. Nie można jej cofnąć. Przed usunięciem par Rozważ wyeksportowanie bazy wiedzy ze strony **publikowania** .

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

Dodaj pary metadanych, zaznaczając najpierw **Opcje widoku**, a następnie wybierając **Pokaż metadane**. Spowoduje to wyświetlenie kolumny metadanych. Następnie wybierz znak, **+** Aby dodać parę metadanych. Ta para składa się z jednego klucza i jednej wartości.

Dowiedz się więcej na temat metadanych w portalu QnA Maker — szybki start dla metadanych:
* [Tworzenie — dodawanie metadanych do pary QnA](../quickstarts/add-question-metadata-portal.md#add-metadata-to-filter-the-answers)
* [Prognoza zapytania — filtrowanie odpowiedzi według metadanych](../quickstarts/get-answer-from-knowledge-base-using-url-tool.md)

## <a name="save-changes-to-the-qna-pairs"></a>Zapisz zmiany w parach QnA

Okresowo wybieraj pozycję **Zapisz i pouczenie** po wprowadzeniu zmian, aby uniknąć utraty zmian.

![Dodaj metadane](../media/qnamaker-how-to-edit-kb/add-metadata.png)

## <a name="when-to-use-rich-text-editing-versus-markdown"></a>Kiedy należy używać edycji tekstu sformatowanego w przeciwieństwie do promocji

[Edytowanie tekstu rozbudowanego](#add-an-editorial-qna-set) odpowiedzi pozwala użytkownikowi, jak autor, korzystać z paska narzędzi formatowania, aby szybko wybierać i formatować tekst.

[](../reference-markdown-format.md) Aby automatycznie generować zawartość w celu zaimportowania baz wiedzy w ramach potoku ciągłej integracji/ciągłego wdrażania lub [testowania wsadowego](../index.yml), należy jeszcze lepszym narzędziem.

## <a name="editing-your-knowledge-base-locally"></a>Lokalne edytowanie bazy wiedzy

Po utworzeniu bazy wiedzy zaleca się dokonanie edycji tekstu bazy wiedzy w [portalu QNA Maker](https://qnamaker.ai), a nie eksportowanie i ponowne importowanie za pomocą plików lokalnych. Mogą jednak wystąpić sytuacje, w których należy edytować bazę wiedzy lokalnie.

Wyeksportuj bazę wiedzy ze strony **Ustawienia** , a następnie przeprowadź edycję bazy wiedzy przy użyciu programu Microsoft Excel. Jeśli zdecydujesz się użyć innej aplikacji do edytowania wyeksportowanego pliku, aplikacja może wprowadzić błędy składniowe, ponieważ nie jest w pełni zgodna z TSV. Pliki TSV programu Microsoft Excel zwykle nie wprowadzają żadnych błędów formatowania.

Po dokonaniu edycji ponownie zaimportuj plik TSV ze strony **ustawień** . Spowoduje to całkowite zastąpienie bieżącej bazy wiedzy zaimportowaną bazą wiedzy.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Współpraca nad bazą wiedzy](../index.yml)

* [Zarządzanie zasobami platformy Azure używanymi przez QnA Maker](set-up-qnamaker-service-azure.md)
