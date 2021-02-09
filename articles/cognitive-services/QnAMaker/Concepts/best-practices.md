---
title: Najlepsze rozwiązania — QnA Maker
description: Skorzystaj z tych najlepszych rozwiązań, aby ulepszyć bazę wiedzy i zapewnić lepsze wyniki dla użytkowników końcowych aplikacji/rozmowy z bot.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 8a6189a613c9a6768428a13e35e0e009894b8b3f
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988035"
---
# <a name="best-practices-of-a-qna-maker-knowledge-base"></a>Najlepsze rozwiązania dotyczące QnA Maker bazy wiedzy

[Cykl programowania dla programu Knowledge Base opiera](../Concepts/development-lifecycle-knowledge-base.md) się na sposobach zarządzania bazą wiedzy od początku do końca. Skorzystaj z tych najlepszych rozwiązań, aby ulepszyć bazę wiedzy i zapewnić lepsze wyniki dla aplikacji klienckiej lub użytkowników końcowych programu Chat bot.

## <a name="extraction"></a>Wyodrębnianie

Usługa QnA Maker ciągle ulepsza algorytmy wyodrębniające bazami z zawartości i rozwijania listy obsługiwanych formatów plików i formatu HTML. Postępuj zgodnie z [wytycznymi](../index.yml) dotyczącymi wyodrębniania danych w oparciu o typ dokumentu.

Ogólnie rzecz biorąc, często zadawane pytania powinny być autonomiczne i nie są łączone z innymi informacjami. Podręczniki produktu powinny mieć jasne nagłówki i najlepiej stronę indeksu.

### <a name="configuring-multi-turn"></a>Konfigurowanie wielostopniowego

[Utwórz bazę wiedzy](../how-to/multiturn-conversation.md#create-a-multi-turn-conversation-from-a-documents-structure) z włączoną funkcją wyodrębniania wieloskładnikowego. Jeśli baza wiedzy lub powinna obsługiwać hierarchię pytań, ta hierarchia może zostać wyodrębniona z dokumentu lub utworzona po wyodrębnieniu dokumentu.

## <a name="creating-good-questions-and-answers"></a>Tworzenie dobrych pytań i odpowiedzi

### <a name="good-questions"></a>Dobre pytania

Najlepsze pytania są proste. Rozważ słowo kluczowe lub frazę dla każdego pytania, a następnie utwórz proste pytanie dla tego słowa kluczowego lub frazy.

Dodaj dowolną liczbę pytań alternatywnych, ale zachowuj proste zmiany. Dodanie więcej słów lub frazy, które nie są częścią głównego celu pytania, nie pomaga QnA Maker znaleźć dopasowania.


### <a name="add-relevant-alternative-questions"></a>Dodawanie istotnych pytań alternatywnych

Użytkownik może wprowadzać pytania z stylem konwersacji tekstowej `How do I add a toner cartridge to my printer?` lub wyszukiwaniem słów kluczowych, takich jak `toner cartridge` . Baza wiedzy powinna mieć oba style pytań, aby prawidłowo zwrócić najlepszą odpowiedź. Jeśli nie masz pewności, jakie słowa kluczowe są wprowadzane przez klienta, użyj Application Insights danych do analizowania zapytań.

### <a name="good-answers"></a>Dobre odpowiedzi

Najlepszym odpowiedziami są proste odpowiedzi, ale nie są one zbyt proste. Nie należy używać odpowiedzi, takich jak `yes` i `no` . Jeśli odpowiedź powinna łączyć się z innymi źródłami lub zapewniać bogate środowisko z nośnikami i łączami, należy użyć [tagowania metadanych](../how-to/edit-knowledge-base.md#add-metadata) do rozróżnienia między odpowiedziami, a następnie [przesłać zapytanie](../how-to/metadata-generateanswer-usage.md#generateanswer-request-configuration) ze znacznikiem metadanych we `strictFilters` właściwości, aby uzyskać poprawną wersję odpowiedzi.

|Odpowiedź|Follup|
|--|--|
|Wyłącz komputer przenośny przy użyciu przycisku energia na klawiaturze.|* Kombinację klawiszy do uśpienia, wyłączenia i ponownego uruchomienia.<br>* Jak wypróbować twardy laptop powierzchniowy<br>* Jak zmienić system BIOS dla komputera przenośnego<br>* Różnice między uśpieniem, wyłączaniem i ponownym uruchomieniem|
|Usługa klienta jest dostępna za pośrednictwem telefonu, Skype'a i wiadomości SMS przez 24 godziny dziennie.|* Informacje kontaktowe dotyczące sprzedaży.<br> * Lokalizacje biur i sklepów oraz godziny dla osób odwiedzających.<br> * Akcesoria dla komputera przenośnego.|

## <a name="chit-chat"></a>Chit-Chat
Dodaj czat Chit-Chat do bot, aby zwiększyć swoją botą konwersację i atrakcyjność przy małym wysiłku. Zestawy danych chit-chat można łatwo dodawać ze wstępnie zdefiniowanych osób prywatnych podczas tworzenia bazy wiedzy i zmieniać je w dowolnym momencie. Dowiedz się, jak [dodać czat Chit do swojej bazy wiedzy](../How-To/chit-chat-knowledge-base.md).

Chit — rozmowa jest obsługiwana w [wielu językach](../how-to/chit-chat-knowledge-base.md#language-support).

### <a name="choosing-a-personality"></a>Wybór charakteru
Chit — rozmowa jest obsługiwana w przypadku kilku wstępnie zdefiniowanych funkcji osobistych:

|Osobowość |Plik zestawu danych QnA Maker |
|---------|-----|
|Professional Edition |[qna_chitchat_professional. tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Wyświetlana |[qna_chitchat_friendly. tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Witty |[qna_chitchat_witty. tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Caring |[qna_chitchat_caring. tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|R |[qna_chitchat_enthusiastic. tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

Zakres odpowiedzi od formalnego do nieformalnego i irreverent. Należy wybrać osobowość, która jest najbardziej zbliżona do żądanego tonu bot. Można wyświetlić [zestawy danych](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets)i wybrać jeden, który służy jako podstawa dla bot, a następnie dostosować odpowiedzi.

### <a name="edit-bot-specific-questions"></a>Edytuj pytania dotyczące bot
Istnieją pewne pytania dotyczące bot, które są częścią zestawu danych Chit-Chat i zostały wypełnione przy użyciu odpowiedzi ogólnych. Zmień te odpowiedzi, aby najlepiej odzwierciedlić szczegóły bot.

Zalecamy, aby następujące Chit — czat bazami bardziej szczegółowy:

* Jaka jest Twoja rola?
* Co możesz zrobić?
* How old are you? (Ile masz lat)?
* Kto utworzył Cię?
* Hello

### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>Dodawanie niestandardowego Chit-Chat ze znacznikiem metadanych

Jeśli dodasz własne pary Chit-Chat QnA, pamiętaj o dodaniu metadanych, aby zostały zwrócone te odpowiedzi. Para nazwa/wartość metadanych to `editorial:chitchat` .

## <a name="searching-for-answers"></a>Wyszukiwanie odpowiedzi

Interfejs API GenerateAnswer używa pytań i odpowiedzi w celu wyszukania najlepszych odpowiedzi na zapytanie użytkownika.

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>Wyszukiwanie pytań tylko wtedy, gdy odpowiedź nie jest istotna

Użyj, [`RankerType=QuestionOnly`](#choosing-ranker-type) Jeśli nie chcesz wyszukiwać odpowiedzi.

Przykładem takiej sytuacji jest to, że baza wiedzy jest wykazem akronimów jako pytań z ich pełną formą jako odpowiedzią. Wartość odpowiedzi nie będzie pomocna w wyszukiwaniu odpowiedniej odpowiedzi.

## <a name="rankingscoring"></a>Klasyfikacja/Ocena
Upewnij się, że masz najlepsze użycie funkcji rankingu QnA Maker obsługiwane przez program. W ten sposób zwiększy prawdopodobieństwo, że odpowiedź danego użytkownika zostanie udzielona z odpowiednią odpowiedzią.

### <a name="choosing-a-threshold"></a>Wybieranie progu

Domyślny [wynik zaufania](confidence-score.md) używany jako próg to 0, jednak można [zmienić próg](confidence-score.md#set-threshold) dla bazy wiedzy w zależności od potrzeb. Ponieważ każda KB różni się od siebie, należy przetestować i wybrać próg, który jest najlepiej dostosowany do Twojej bazy wiedzy.

### <a name="choosing-ranker-type"></a>Wybieranie typu rangi
Domyślnie QnA Maker przeszukuje pytania i odpowiedzi. Jeśli chcesz wyszukać tylko pytania, aby wygenerować odpowiedź, użyj `RankerType=QuestionOnly` wpisu w treści post żądania GenerateAnswer.

### <a name="add-alternate-questions"></a>Dodaj alternatywne pytania
[Alternatywne pytania](../How-To/edit-knowledge-base.md) zwiększają prawdopodobieństwo dopasowania do zapytania użytkownika. Pytania alternatywne są przydatne w przypadku, gdy istnieje wiele sposobów, w których może zostać wyświetlony monit. Może to obejmować zmiany ze strukturą zdania i stylem wyrazu.

|Oryginalne zapytanie|Zapytania alternatywne|Zmiana|
|--|--|--|
|Czy parking jest dostępny?|Czy masz zaparkowane samochodu?|Struktura zdania|
 |Cześć|Yo<br>Hej tam!|styl Word lub żargonu|

<a name="use-metadata-filters"></a>

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Filtrowanie pytań i odpowiedzi za pomocą tagów metadanych

[Metadane](../How-To/edit-knowledge-base.md) umożliwiają aplikacji klienckiej, aby wiedzieć, że nie powinna przyjmować wszystkich odpowiedzi, ale zamiast tego zawęża wyniki zapytania użytkownika w oparciu o Tagi metadanych. Odpowiedź bazy wiedzy może się różnić w zależności od tagu metadanych, nawet jeśli zapytanie jest takie samo. Na przykład *"gdzie znajduje się parking"* może mieć inną odpowiedź, jeśli lokalizacja gałęzi restauracji jest inna — to oznacza, że metadane są *lokalizacją: Seattle* a *Lokalizacja: Redmond*.

### <a name="use-synonyms"></a>Użyj synonimów
# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (wersja stabilna)](#tab/v1)
W przypadku niektórych rodzajów synonimów w języku angielskim należy użyć zmian wyrazów bez uwzględniania wielkości liter za pośrednictwem [interfejsu API zmian](/rest/api/cognitiveservices/qnamaker/alterations/replace) , aby dodać synonimy do słów kluczowych, które mają różne formy. Synonimy są dodawane na poziomie usługi QnA Maker i **udostępniane przez wszystkie bazy wiedzy w usłudze**.

# <a name="qna-maker-managed-preview-release"></a>[Zarządzane QnA Maker (wersja zapoznawcza)](#tab/v2)
W przypadku niektórych rodzajów synonimów w języku angielskim należy użyć zmian wyrazów bez uwzględniania wielkości liter za pośrednictwem [interfejsu API zmian](/rest/api/cognitiveservices/qnamaker/alterations/replace) , aby dodać synonimy do słów kluczowych, które mają różne formy. Synonimy w QnA Maker zarządzane (wersja zapoznawcza) są **dodawane dla bazy wiedzy**.

|Oryginalne słowo|Synonimy|
|--|--|
|kupna|zakup<br>Sieć — bankowość<br>Bankowość netto|

---

### <a name="use-distinct-words-to-differentiate-questions"></a>Korzystanie z odrębnych słów w celu odróżnienia pytań
Algorytm klasyfikacji QnA Maker, który pasuje do zapytania użytkownika z pytaniem w bazie wiedzy, najlepiej sprawdza się, jeśli każde pytanie odniesie się do różnych potrzeb. Powtarzanie tego samego wyrazu między pytaniami zmniejsza prawdopodobieństwo wybrania odpowiedniej odpowiedzi dla danego zapytania użytkownika z tymi wyrazami.

Na przykład możesz mieć dwa osobne bazami z następującymi pytaniami:

|Bazami|
|--|
|gdzie jest *Lokalizacja* parkingów|
|gdzie jest *Lokalizacja* ATM|

Ponieważ te dwa bazami są oznaczane podobnymi wyrazami, taka podobieństwo może spowodować bardzo podobne wyniki dla wielu zapytań użytkowników, których frazy są podobne do  *"gdzie jest `<x>` lokalizacją"*. Zamiast tego spróbuj wyraźnie odróżnić się od zapytań, takich jak  *"gdzie jest to parkingi"* i *"gdzie jest ATM"*, unikając słów takich jak "lokalizacja", które mogą mieć wiele pytań w KB.

## <a name="collaborate"></a>Współpraca
QnA Maker umożliwia użytkownikom współpracę w bazie wiedzy. Aby uzyskać dostęp do baz wiedzy, użytkownicy muszą mieć dostęp do grupy zasobów usługi Azure QnA Maker. Niektóre organizacje mogą chcieć uzyskać informacje o edycji i obsłudze bazy wiedzy oraz nadal mieć możliwość ochrony dostępu do zasobów platformy Azure. Ten model zatwierdzania przez Edytor jest wykonywany przez skonfigurowanie dwóch identycznych [usług QNA Maker](../How-to/set-up-qnamaker-service-azure.md) w różnych subskrypcjach i wybranie jednej z nich dla cyklu edycji. Po zakończeniu testowania zawartość bazy wiedzy zostanie przetransferowana z procesem [importu eksportu](../Tutorials/migrate-knowledge-base.md) do usługi QNA Maker osoby zatwierdzającej, która ostatecznie opublikuje bazę wiedzy i zaktualizuje punkt końcowy.



## <a name="active-learning"></a>Aktywna nauka

[Usługa Active Learning](../How-to/use-active-learning.md) to najlepsze zadanie sugerujące alternatywne pytania, gdy ma szeroki zakres jakości i liczbę zapytań opartych na użytkownikach. Ważne jest, aby zezwolić na uczestnictwo użytkowników aplikacji klienta w aktywnej pętli opinii o uczeniu bez Censorship. Po wybraniu pytań w portalu QnA Maker można **[filtrować według sugestii](../How-To/improve-knowledge-base.md#accept-an-active-learning-suggestion-in-the-knowledge-base)** , a następnie przejrzeć i zaakceptować lub odrzucić te sugestie.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Edytowanie bazy wiedzy](../How-to/edit-knowledge-base.md)
