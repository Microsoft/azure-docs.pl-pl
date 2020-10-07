---
title: Jak przetestować bazę wiedzy QnA Maker
description: Testowanie bazy wiedzy QnA Maker jest ważną częścią procesu iteracyjnego, aby poprawić dokładność zwracanych odpowiedzi. Bazę wiedzy można przetestować za pośrednictwem rozszerzonego interfejsu czatu, który umożliwia również edycję.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 05ae359182ac3d9c3f4cc17d7675a3f2fefa0bff
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776837"
---
# <a name="test-your-knowledge-base-in-qna-maker"></a>Przetestuj bazę wiedzy w QnA Maker

Testowanie bazy wiedzy QnA Maker jest ważną częścią procesu iteracyjnego, aby poprawić dokładność zwracanych odpowiedzi. Bazę wiedzy można przetestować za pośrednictwem rozszerzonego interfejsu czatu, który umożliwia również edycję.

## <a name="interactively-test-in-qna-maker-portal"></a>Interaktywny test w portalu QnA Maker

1. Uzyskaj dostęp do bazy wiedzy, wybierając jej nazwę na stronie **Moje bazy wiedzy** .
1. Aby uzyskać dostęp do panelu slajdu testu, wybierz opcję **test** w górnym panelu aplikacji.
1. Wprowadź zapytanie w polu tekstowym i wybierz klawisz ENTER.
1. Najlepsza dopasowana odpowiedź z bazy wiedzy jest zwracana jako odpowiedź.

### <a name="clear-test-panel"></a>Wyczyść panel testu

Aby wyczyścić wszystkie wprowadzone zapytania testowe i ich wyniki z konsoli testowej, wybierz pozycję **Rozpocznij** w lewym górnym rogu panelu test.

### <a name="close-test-panel"></a>Zamknij Panel testu

Aby zamknąć Panel test, ponownie wybierz przycisk **Testuj** . Gdy Panel testowy jest otwarty, nie można edytować zawartości bazy wiedzy.

### <a name="inspect-score"></a>Sprawdź ocenę

Sprawdzasz szczegóły wyniku testu w panelu Inspekcja.

1.  Po otwarciu panelu slajdu testowego wybierz pozycję **Sprawdź** , aby uzyskać więcej informacji na temat tej odpowiedzi.

    ![Inspekcja odpowiedzi](../media/qnamaker-how-to-test-kb/inspect.png)

2.  Zostanie wyświetlony panel Inspekcja. Panel zawiera górne przeznaczenie oceny, a także wszystkie zidentyfikowane jednostki. Panel pokazuje wynik wybranego wypowiedźu.

### <a name="correct-the-top-scoring-answer"></a>Popraw górną odpowiedź oceniania

Jeśli Górna odpowiedź oceniania jest niepoprawna, wybierz odpowiednią odpowiedź z listy i wybierz pozycję **Zapisz i pouczenie**.

![Popraw górną odpowiedź oceniania](../media/qnamaker-how-to-test-kb/choose-answer.png)

### <a name="add-alternate-questions"></a>Dodaj alternatywne pytania

Możesz dodać alternatywne formy pytania do danej odpowiedzi. Wpisz alternatywne odpowiedzi w polu tekstowym, a następnie kliknij klawisz ENTER, aby je dodać. Wybierz pozycję **Zapisz i pouczenie** , aby zapisać aktualizacje.

![Dodaj alternatywne pytania](../media/qnamaker-how-to-test-kb/add-alternate-question.png)

### <a name="add-a-new-answer"></a>Dodaj nową odpowiedź

Można dodać nową odpowiedź, jeśli którykolwiek z dopasowanych odpowiedzi jest nieprawidłowy lub odpowiedź nie istnieje w bazie wiedzy (nie znaleziono dobrego dopasowania w KB).

W dolnej części listy odpowiedzi Użyj pola tekstowego, aby wprowadzić nową odpowiedź, i naciśnij klawisz ENTER, aby go dodać.

Wybierz pozycję **Zapisz i poszkol,** aby zachować tę odpowiedź. Nowa para pytań i odpowiedzi została teraz dodana do bazy wiedzy.

> [!NOTE]
> Wszystkie zmiany w bazie wiedzy są zapisywane tylko po naciśnięciu przycisku **Zapisz i pouczenie** .

### <a name="test-the-published-knowledge-base"></a>Testowanie opublikowanej bazy wiedzy

Opublikowaną wersję bazy wiedzy można testować w okienku testów. Po opublikowaniu bazy wiedzy wybierz pole **opublikowana baza wiedzy** i Wyślij zapytanie w celu uzyskania wyników z opublikowanej bazy wiedzy.

![Testuj względem opublikowanej bazy wiedzy](../media/qnamaker-how-to-test-kb/test-against-published-kb.png)

## <a name="batch-test-with-tool"></a>Test wsadowy z narzędziem

Użyj narzędzia testowania wsadowego, gdy chcesz:

* Określanie najważniejszych odpowiedzi i wyników dla zestawu pytań
* Weryfikuj oczekiwaną odpowiedź dla zestawu pytań

### <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/cognitive-services/)
* [Utwórz usługę QNA Maker](../Quickstarts/create-publish-knowledge-base.md) lub Użyj istniejącej usługi, która używa języka angielskiego.
* Pobieranie [przykładowego `.docx` pliku z obsługą kilku przełączań](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)
* Pobierz [Narzędzie do testowania wsadowego](https://aka.ms/qnamakerbatchtestingtool), Wyodrębnij plik wykonywalny z `.zip` pliku.

### <a name="sign-into-qna-maker-portal"></a>Zaloguj się do portalu QnA Maker

[Zaloguj](https://www.qnamaker.ai/) się do portalu QNA Maker.

### <a name="create-a-new-knowledge-base-from-the-multi-turn-sampledocx-file"></a>Utwórz nową bazę wiedzy na podstawie wieloskładnikowego pliku sample.docx

1. Wybierz pozycję **Utwórz bazę wiedzy** na pasku narzędzi.
1. Pomiń **krok 1** , ponieważ należy już mieć zasób QNA Maker, przechodząc do **kroku 2** , aby wybrać istniejące informacje o zasobie:
    * Identyfikator Azure Active Directory
    * Nazwa subskrypcji platformy Azure
    * Nazwa usługi Azure QnA
    * Język — język angielski
1. Wprowadź nazwę `Multi-turn batch test quickstart` jako nazwę bazy wiedzy.

1. W **kroku 4**Skonfiguruj ustawienia w następującej tabeli:

    |Ustawienie|Wartość|
    |--|--|
    |**Włącz wyodrębnianie wieloskładnikowe z adresów URL, plików PDF lub DOCX.**|Zaznaczono|
    |**Domyślny tekst odpowiedzi**| `Batch test - default answer not found.`|
    |**+ Dodaj plik**|Wybierz listę pobranego `.docx` pliku w sekcji wymagania wstępne.|
    |**Pogawędka**|Wybierz **profesjonalne**|

1. W **kroku 5**wybierz pozycję **Utwórz swoją KB**.

    Po zakończeniu procesu tworzenia w portalu zostanie wyświetlona edytowalna baza wiedzy.

### <a name="save-train-and-publish-knowledge-base"></a>Zapisywanie, uczenie i publikowanie bazy wiedzy

1. Wybierz pozycję **Zapisz i pouczenie** z paska narzędzi, aby zapisać bazę wiedzy.
1. Wybierz pozycję **Publikuj** na pasku narzędzi, a następnie wybierz pozycję **Opublikuj** ponownie, aby opublikować bazę wiedzy. Opublikowanie sprawia, że baza wiedzy jest dostępna dla zapytań z publicznego punktu końcowego adresu URL. Po zakończeniu publikowania Zapisz adres URL hosta i informacje o kluczu punktu końcowego widoczne na stronie **Publikuj** .

    |Wymagane dane| Przykład|
    |--|--|
    |Opublikowany Host|`https://YOUR-RESOURCE-NAME.azurewebsites.net`|
    |Klucz publikowany|`XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX` (ciąg znaków 32 wyświetlany po `Endpoint` )|
    |Identyfikator aplikacji|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (ciąg znaków 36 wyświetlany jako część `POST` ) |

### <a name="create-batch-test-file-with-question-ids"></a>Utwórz plik testu wsadowego z identyfikatorami pytań

Aby użyć narzędzia Test Batch, Utwórz plik o nazwie `batch-test-data-1.tsv` za pomocą edytora tekstu. Plik powinien być w formacie UTF-8 i musi mieć następujące kolumny oddzielone tabulatorem.

|Pola wejściowe plików TSV|Uwagi|Przykład|
|--|--|--|
|Identyfikator bazy wiedzy|Identyfikator bazy wiedzy został znaleziony na stronie publikowanie. Przetestuj kilka baz wiedzy w jednej usłudze jednocześnie w pojedynczym pliku przy użyciu różnych identyfikatorów bazy wiedzy w pojedynczym pliku.|`xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` (ciąg znaków 36 wyświetlany jako część `POST` ) |
|Pytanie|Tekst pytania, który użytkownik wprowadzi. maksymalnie 1 000 znaków.|`How do I sign out?`|
|Tagi metadanych|optional|`topic:power`używa formatu _wartości klucz: wartość_|
|Górny parametr|optional|`25`|
|Oczekiwany identyfikator odpowiedzi|optional|`13`|

W przypadku tego bazy wiedzy Dodaj 3 wiersze tylko dwóch wymaganych kolumn do pliku. Pierwszą kolumną jest identyfikator bazy wiedzy, a druga kolumna powinna być następującą listą pytań:

|Kolumna 2 — pytania|
|--|
|`Use Windows Hello to sign in`|
|`Charge your Surface Pro 4`|
|`Get to know Windows 10`|

Te pytania są dokładnymi wyrazami z bazy wiedzy i powinny zwracać 100 jako wynik pewności.

Następnie Dodaj kilka pytań, podobnie jak te pytania, ale nie dokładnie na trzy wiersze, używając tego samego identyfikatora bazy wiedzy:

|Kolumna 2 — pytania|
|--|
|`What is Windows Hello?`|
|`How do I charge the laptop?`|
|`What features are in Windows 10?`|

> [!CAUTION]
> Upewnij się, że każda kolumna jest oddzielona tylko ogranicznikiem tabulacji. Spacje wiodące i końcowe są dodawane do danych kolumny i spowodują, że program zgłosi wyjątki, gdy typ lub rozmiar są nieprawidłowe.

Plik testu wsadowego, po otwarciu w programie Excel wygląda jak na poniższej ilustracji. Identyfikator bazy wiedzy został zastąpiony `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` przez zabezpieczenia. W przypadku własnych testów wsadowych upewnij się, że w kolumnie jest wyświetlany identyfikator bazy wiedzy.

> [!div class="mx-imgBorder"]
> ![Wprowadzanie pierwszej wersji pliku TSV z testu wsadowego](../media/batch-test/batch-test-1-input.png)

### <a name="test-the-batch-file"></a>Testowanie pliku wsadowego

Uruchom program testów wsadowych przy użyciu poniższego formatu interfejsu wiersza polecenia.

Zastąp `YOUR-RESOURCE-NAME` `ENDPOINT-KEY` wartości i własnymi wartościami dla nazwy usługi i klucza punktu końcowego. Te wartości są dostępne na stronie **Ustawienia** w portalu QNA Maker.

```console
batchtesting.exe batch-test-data-1.tsv https://YOUR-RESOURCE-NAME.azurewebsites.net ENDPOINT-KEY out.tsv
```
Test zakończy się i wygeneruje `out.tsv` plik:

> [!div class="mx-imgBorder"]
> ![Wyjście pierwszej wersji pliku TSV z testu wsadowego](../media/batch-test/batch-test-1-output.png)

Identyfikator bazy wiedzy został zastąpiony `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` przez zabezpieczenia. W przypadku własnego testu wsadowego w kolumnie zostanie wyświetlony Identyfikator bazy wiedzy.

Test danych wyjściowych oceny ufności, w czwartej kolumnie, pokazuje 3 pierwsze pytania, które zwróciły wynik 100 zgodnie z oczekiwaniami, ponieważ każde pytanie jest dokładnie takie samo, jak w bazie wiedzy. Ostatnie 3 pytania z nowym sformułowaniem pytania nie zwracają 100 jako wyniku pewności. Aby zwiększyć wynik zarówno dla testu, jak i użytkowników, musisz dodać bardziej alternatywne pytania do bazy wiedzy.

### <a name="testing-with-the-optional-fields"></a>Testowanie przy użyciu pól opcjonalnych

Po zrozumieniu formatu i procesu można wygenerować plik testowy do uruchomienia względem bazy wiedzy ze źródła danych, na przykład z dzienników rozmowy.

Ze względu na to, że źródło danych i proces są zautomatyzowane, plik testowy może być uruchamiany wiele razy z różnymi ustawieniami w celu określenia poprawnych wartości.

Na przykład jeśli masz dziennik rozmowy i chcesz określić, który tekst dziennika rozmowy ma zastosowanie do tych pól metadanych, Utwórz plik testowy i Ustaw pola metadanych dla każdego wiersza. Uruchom test, a następnie sprawdź wiersze, które pasują do metadanych. Ogólnie rzecz biorąc, dopasowania powinny być dodatnie, ale należy przejrzeć wyniki dla fałszywych wartości dodatnich. Wynik fałszywie dodatni to wiersz pasujący do metadanych, ale oparty na tekście, nie powinien być zgodny.

### <a name="using-optional-fields-in-the-input-batch-test-file"></a>Używanie pól opcjonalnych w wejściowym pliku testu wsadowego

Skorzystaj z poniższej tabeli, aby zrozumieć, jak znaleźć wartości pól dla opcjonalnych danych.

|Numer kolumny|Opcjonalna kolumna|Lokalizacja danych|
|--|--|--|
|3|metadane|Eksportuj istniejącą bazę wiedzy dla istniejących _kluczy: par wartości_ .|
|4|top (pierwsze)|`25`Zalecaną wartością domyślną jest.|
|5|Identyfikator pytania i odpowiedzi|Wyeksportuj istniejącą bazę wiedzy dla wartości identyfikatora. Zauważ również, że identyfikatory zostały zwrócone w pliku wyjściowym.|

### <a name="add-metadata-to-the-knowledge-base"></a>Dodawanie metadanych do bazy wiedzy

1. Na stronie **Edytuj** w portalu QNA Dodaj metadane `topic:power` do następujących pytań:

    |Pytania|
    |--|
    |Naliczanie opłat za pakiet Pro 4|
    |Sprawdź poziom baterii|

    Dwie pary QnA mają zestaw metadanych.

    > [!TIP]
    > Aby wyświetlić metadane i identyfikatory QnA każdego zestawu, wyeksportuj bazę wiedzy. Wybierz stronę **Ustawienia** , a następnie wybierz pozycję **Eksportuj** jako `.xls` plik. Znajdź ten pobrany plik i otwórz go w programie Excel przegląd dla metadanych i identyfikatora.

1. Wybierz pozycję **Zapisz i pouczenie**, a następnie wybierz stronę **Publikowanie** , a następnie wybierz przycisk **Publikuj** . Te akcje sprawiają, że zmiany są dostępne dla testu wsadowego. Pobierz bazę wiedzy ze strony **ustawień** .

    Pobrany plik ma prawidłowy format dla metadanych oraz prawidłowy identyfikator pytania i odpowiedzi. Użyj tych pól w następnej sekcji

    > [!div class="mx-imgBorder"]
    > ![Wyeksportowana baza wiedzy z metadanymi](../media/batch-test/exported-knowledge-base-with-metadata.png)

### <a name="create-a-second-batch-test"></a>Utwórz drugi test wsadowy

Istnieją dwa główne scenariusze testowania wsadowego:
* **Przetwarzaj pliki dziennika rozmów** — Określ największą odpowiedź dla wcześniej niewidzianych pytań — najbardziej typową sytuacją jest, aby przetworzyć plik dziennika zapytań, taki jak pytania użytkownika z rozmowy bot. Utwórz test pliku wsadowego z tylko wymaganymi kolumnami. Test zwraca największą odpowiedź dla każdego pytania. Nie oznacza to, że jest to prawidłowa odpowiedź. Po zakończeniu tego testu przejdź do testu weryfikacyjnego.
* **Test weryfikacji** — Sprawdź poprawność oczekiwanej odpowiedzi. Ten test wymaga sprawdzenia poprawności wszystkich pytań i pasujących oczekiwanych odpowiedzi w teście wsadowym. Może to wymagać ręcznego procesu.

W poniższej procedurze przyjęto założenie, że scenariusz polega na przetwarzaniu dzienników rozmowy przy użyciu

1. Utwórz nowy plik testu wsadowego, aby uwzględnić opcjonalne dane `batch-test-data-2.tsv` . Dodaj 6 wierszy z oryginalnego pliku wejściowego testu wsadowego, a następnie Dodaj identyfikator pary metadanych, Top i QnA dla każdego wiersza.

    Aby zasymulować zautomatyzowany proces sprawdzania nowego tekstu z dzienników rozmowy względem bazy wiedzy, należy ustawić metadane dla każdej kolumny na taką samą wartość: `topic:power` .

    > [!div class="mx-imgBorder"]
    > ![Wprowadź drugą wersję pliku TSV z testu wsadowego](../media/batch-test/batch-test-2-input.png)

1. Uruchom ponownie test, zmieniając nazwy plików wejściowych i wyjściowych, aby wskazać, że jest drugim testem.

    > [!div class="mx-imgBorder"]
    > ![Wyprowadź drugą wersję pliku TSV z testu wsadowego](../media/batch-test/batch-test-2-output.png)

### <a name="test-results-and-an-automated-test-system"></a>Wyniki testów i zautomatyzowany system testów

Ten testowy plik wyjściowy można analizować jako część zautomatyzowanego potoku testów ciągłych.

Te konkretne dane wyjściowe testu powinny być odczytane jako: każdy wiersz został przefiltrowany przy użyciu metadanych, a ponieważ każdy wiersz nie jest zgodny z metadanymi w bazie wiedzy, odpowiedź domyślna dla tych niezgodnych wierszy ("brak dobrego dopasowania znalezionych w KB"). W tych wierszach, które były zgodne, zostały zwrócone identyfikatory i oceny QnA.

Wszystkie wiersze zwróciły etykietę nieprawidłową, ponieważ żaden wiersz nie jest zgodny z oczekiwanym IDENTYFIKATORem odpowiedzi.

Powinny być widoczne te wyniki, aby można było wykonać dziennik rozmowy i użyć tekstu jako zapytania dla każdego wiersza. Bez znajomości danych, wyniki informują o danych, które można następnie użyć do przeniesienia:

* meta — dane
* Identyfikator QnA
* wynik

Czy filtrowanie za pomocą meta danych jest dobrym pomysłem dla testu? Tak i nie. System testowy powinien tworzyć pliki testowe dla każdej pary meta-danych, a także test bez par metadanych.

### <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz kontynuować testowania bazy wiedzy, Usuń narzędzie plików wsadowych i pliki testowe.

Jeśli nie chcesz nadal korzystać z tej bazy wiedzy, Usuń bazę wiedzy z następującymi krokami:

1. W portalu QnA Maker wybierz pozycję **Moje bazy wiedzy** z górnego menu.
1. Na liście baz wiedzy wybierz ikonę **Usuń** w wierszu bazy wiedzy tego przewodnika Szybki Start.

[Dokumentacja dotycząca narzędzia](../reference-tsv-format-batch-testing.md) zawiera następujące informacje:

* przykładowy wiersz polecenia narzędzia
* Format plików wejściowych i wyjściowych TSV

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Publikowanie bazy wiedzy](./publish-knowledge-base.md)
