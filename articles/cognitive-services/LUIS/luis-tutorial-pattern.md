---
title: 'Samouczek: wzorce — LUIS'
description: Użyj wzorców, aby zwiększyć zamiar i prognozowanie jednostek, jednocześnie dostarczając mniejszą liczbę przykładów wyrażenia długości w tym samouczku. Wzorzec jest dostępny jako przykład wypowiedź szablonu, który zawiera składnię identyfikującą jednostki i tekst, który można zignorować.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 07/06/2020
ms.openlocfilehash: 9814304aed4d7a5f307fb2179491b0fa9635fd68
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91324658"
---
# <a name="tutorial-add-common-pattern-template-utterance-formats-to-improve-predictions"></a>Samouczek: Dodawanie typowych formatów wypowiedź szablonów wzorców w celu usprawnienia prognoz

W tym samouczku Użyj wzorców, aby zwiększyć cel i prognozowanie jednostek, co pozwala na dostarczenie mniejszej przykładowej wyrażenia długości. Wzorzec to szablon wypowiedź przypisany do zamiaru, który zawiera składnię identyfikującą jednostki i tekst, który można zignorować.

**Ten samouczek zawiera informacje na temat wykonywania następujących czynności:**

> [!div class="checklist"]
> * Tworzenie wzorca
> * Sprawdzanie ulepszeń przewidywania wzorców
> * Oznaczanie tekstu jako możliwego do zignorowania i zagnieżdżanie we wzorcu
> * Używanie panelu testowego do sprawdzania powodzenia wzorca

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="utterances-in-intent-and-pattern"></a>Wyrażenia długości w zamierzeniu i wzorcu

Istnieją dwa typy wyrażenia długości przechowywanych w aplikacji LUIS:

* Przykład wyrażenia długości w zamierzeniu
* Wyrażenia długości szablonu we wzorcu

Dodanie wyrażenia długości szablonu jako wzorca pozwala na zapewnienie mniejszej ilości przykładowych wyrażenia długości do zamiaru.

Stosowanie wzorca opiera się na połączeniu dopasowania wyrażeń i uczenia maszynowego.  Wypowiedź szablonu we wzorcu wraz z przykładowymi wypowiedziami intencji pozwala usłudze LUIS lepiej zrozumieć, jakie wypowiedzi pasują do intencji.

## <a name="import-example-app-and-clone-to-new-version"></a>Importowanie przykładowej aplikacji i klonowanie do nowej wersji

Wykonaj następujące kroki:

1.  Pobierz i Zapisz [plik JSON aplikacji](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/tutorial-fix-unsure-predictions.json?raw=true).

1. Zaloguj się do [portalu Luis](https://www.luis.ai)i wybierz swoją **subskrypcję** i **zasób tworzenia** , aby wyświetlić aplikacje przypisane do tego zasobu.
1. Zaimportuj plik JSON do nowej aplikacji w [portalu Luis](https://www.luis.ai). Na stronie **Moje aplikacje** wybierz pozycję **+ Nowa aplikacja do konwersacji**, a następnie wybierz pozycję **Importuj jako plik JSON**. Wybierz plik, który został pobrany w poprzednim kroku, nadaj nazwę aplikacji `Patterns tutorial` .

## <a name="create-new-intents-and-their-utterances"></a>Tworzenie nowych intencji i ich wypowiedzi

Te dwa intencje odszukają Menedżera lub bezpośrednich raportów kierownika na podstawie tekstu wypowiedź. Trudności polegają na tym, że dwa intencje _oznaczają_ różne elementy, ale większość wyrazów jest taka sama. Tylko kolejność wyrazów jest inna. Aby zamiar został prawidłowo przewidywalna, konieczne byłoby posiadanie wielu przykładów.

1. Na pasku nawigacyjnym wybierz pozycję **kompilacja** .

1. Na stronie **intencje** wybierz pozycję **+ Utwórz** , aby utworzyć nowy cel.

1. Wprowadź ciąg `OrgChart-Manager` w wyświetlonym oknie dialogowym, a następnie wybierz pozycję **Done** (Gotowe).

    ![Okno podręczne Create new message (Tworzenie nowej wiadomości)](media/luis-tutorial-pattern/hr-create-new-intent-popup.png)

1. Dodaj przykładowe wypowiedzi do intencji. Te wyrażenia długości nie są _dokładnie_ takie same, ale mają wzorzec, który można wyodrębnić.

    |Przykładowe wypowiedzi|
    |--|
    |`Who is John W. Smith the subordinate of?`|
    |`Who does John W. Smith report to?`|
    |`Who is John W. Smith's manager?`|
    |`Who does Jill Jones directly report to?`|
    |`Who is Jill Jones supervisor?`|

1. Wybierz pozycję **Intents** (Intencje) na lewym pasku nawigacyjnym.

1. Wybierz pozycję **+ Utwórz** , aby utworzyć nowy cel. Wprowadź ciąg `OrgChart-Reports` w wyświetlonym oknie dialogowym, a następnie wybierz pozycję **Done** (Gotowe).

1. Dodaj przykładowe wypowiedzi do intencji.

    |Przykładowe wypowiedzi|
    |--|
    |`Who are John W. Smith's subordinates?`|
    |`Who reports to John W. Smith?`|
    |`Who does John W. Smith manage?`|
    |`Who are Jill Jones direct reports?`|
    |`Who does Jill Jones supervise?`|

### <a name="caution-about-example-utterance-quantity"></a>Ostrzeżenie o liczbie przykładowych wypowiedzi

[!INCLUDE [Too few examples](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]

### <a name="train-the-app-before-testing-or-publishing"></a>Trenowanie aplikacji przed testowaniem lub publikowaniem

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

### <a name="publish-the-app-to-query-from-the-endpoint"></a>Publikowanie aplikacji w celu wysyłania zapytań z punktu końcowego

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

### <a name="get-intent-and-entities-from-endpoint"></a>Pobieranie intencji i jednostek z punktu końcowego

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Przejdź na koniec adresu URL na pasku adresu i Zastąp _YOUR_QUERY_HERE_ : `Who is the boss of Jill Jones?` .

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.326605469
                },
                "OrgChart-Reports": {
                    "score": 0.127583548
                },
                "EmployeeFeedback": {
                    "score": 0.0299124215
                },
                "MoveEmployee": {
                    "score": 0.01159851
                },
                "GetJobInformation": {
                    "score": 0.0104600191
                },
                "ApplyForJob": {
                    "score": 0.007508645
                },
                "Utilities.StartOver": {
                    "score": 0.00359402061
                },
                "Utilities.Stop": {
                    "score": 0.00336530479
                },
                "FindForm": {
                    "score": 0.002653719
                },
                "Utilities.Cancel": {
                    "score": 0.00263288687
                },
                "None": {
                    "score": 0.00238638581
                },
                "Utilities.Help": {
                    "score": 0.00226386427
                },
                "Utilities.Confirm": {
                    "score": 0.00211663754
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "EmployeeListEntity": [
                    [
                        "Employee-45612"
                    ]
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "boss of Jill Jones",
                            "startIndex": 11,
                            "length": 18,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "EmployeeListEntity": [
                        {
                            "type": "EmployeeListEntity",
                            "text": "Jill Jones",
                            "startIndex": 19,
                            "length": 10,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

Oczekiwano poprawnego górnego celu, `OrgChart-Manager` ale wynik nie jest większy niż 70% i jest zbyt mały powyżej następnego najwyższego zamiaru. Aby poprawny wynik intencji był znacznie wyższy procentowo i bardziej oddalony od następnego najwyższego wyniku, należy użyć wzorców.

Pozostaw drugie okno przeglądarki otwarte. Będzie on używany ponownie później w samouczku.

## <a name="template-utterances"></a>Wypowiedzi szablonu
Ze względu na charakter domeny podmiotu zasobów ludzkich istnieje kilka typowych metod zaproszenia o relacje między pracownikami w organizacjach. Na przykład:

|Wypowiedzi|
|--|
|`Who does Jill Jones report to?`|
|`Who reports to Jill Jones?`|

Te wyrażenia długości są zbyt blisko, aby określić unikatowość kontekstową każdego z nich bez podawania _wielu_ wypowiedź przykładów. Dzięki dodaniu wzorca dla zamiaru LUIS uczy typowe wzorce wypowiedź dla zamiaru bez konieczności dostarczania wielu przykładów wypowiedź.

Przykładowe wypowiedzi szablonu dla tej intencji obejmują:

|Przykładowe wypowiedzi szablonu|Znaczenie składni|
|--|--|
|`Who does {EmployeeListEntity} report to[?]`|wymienne `{EmployeeListEntity}`<br>Ignoruj `[?]`|
|`Who reports to {EmployeeListEntity}[?]`|wymienne `{EmployeeListEntity}`<br>Ignoruj `[?]`|

Składnia `{EmployeeListEntity}` oznacza lokalizację jednostki w ramach wypowiedzi szablonu oraz to, która to jednostka. Opcjonalna składnia, `[?]` , znaki wyrazów lub [interpunkcja](luis-reference-application-settings.md#punctuation-normalization) , która jest opcjonalna. Usługa LUIS dopasowuje wypowiedź, ignorując opcjonalny tekst w nawiasie.

Chociaż składnia wygląda podobnie do wyrażenia regularnego, nie jest wyrażeniem regularnym. Obsługiwana jest tylko składnia w nawiasie klamrowym, `{}`, i nawiasie kwadratowym, `[]`. Mogą być one zagnieżdżone na najwyżej dwóch poziomach.

Aby wzorzec pasował do elementu wypowiedź, _najpierw_ jednostki w wypowiedź muszą być zgodne z jednostkami w szablonie wypowiedź. Oznacza to, że jednostki muszą mieć wystarczającą liczbę przykładów na przykład wyrażenia długości z dużym przewidywaniam, zanim wzorce z jednostkami zakończą się pomyślnie. Jednak szablon nie pomaga w przewidywaniu jednostek, tylko intencji.

**Wzorce umożliwiają dostarczenie mniejszej liczby wypowiedzi przykładowych, jednak jeśli jednostki nie zostaną wykryte, nie zostanie znalezione dopasowanie dla wzorca.**

### <a name="add-the-patterns-for-the-orgchart-manager-intent"></a>Dodaj wzorce dla zamiaru OrgChart-Manager

1. Wybierz pozycję **Build** (Kompiluj) w górnym menu.

1. Na lewym pasku nawigacyjnym w obszarze **Improve app performance** (Popraw wydajność aplikacji) wybierz opcję **Patterns** (Wzorce).

1. Wybierz intencję **OrgChart-Manager** (Schemat organizacyjny — menedżer), a następnie wprowadź następujące wypowiedzi szablonu:

    |Wypowiedzi szablonu|
    |:--|
    |`Who is {EmployeeListEntity} the subordinate of[?]`|
    |`Who does {EmployeeListEntity} report to[?]`|
    |`Who is {EmployeeListEntity}['s] manager[?]`|
    |`Who does {EmployeeListEntity} directly report to[?]`|
    |`Who is {EmployeeListEntity}['s] supervisor[?]`|
    |`Who is the boss of {EmployeeListEntity}[?]`|

    Te wyrażenia długości szablonu zawierają jednostkę **EmployeeListEntity** z notacją nawiasu klamrowego.

1. Na stronie wzorce wybierz opcję **schemat organizacyjny — raporty** , a następnie wprowadź następujący wyrażenia długości szablonu:

    |Wypowiedzi szablonu|
    |:--|
    |`Who are {EmployeeListEntity}['s] subordinates[?]`|
    |`Who reports to {EmployeeListEntity}[?]`|
    |`Who does {EmployeeListEntity} manage[?]`|
    |`Who are {EmployeeListEntity} direct reports[?]`|
    |`Who does {EmployeeListEntity} supervise[?]`|
    |`Who does {EmployeeListEntity} boss[?]`|

### <a name="query-endpoint-when-patterns-are-used"></a>Punkt końcowy zapytania, gdy są używane wzorce

Teraz, gdy wzorce są dodawane do aplikacji, uczenie, publikowanie i wysyłanie zapytań do aplikacji w punkcie końcowym przewidywania środowiska uruchomieniowego.

1. Wybierz pozycję **Przeszkol**. Po zakończeniu szkolenia wybierz pozycję **Publikuj** i wybierz miejsce **produkcyjne** , a następnie wybierz pozycję **gotowe**.

1. Po zakończeniu publikowania Przełącz karty przeglądarki z powrotem na kartę adres URL punktu końcowego.

1. Przejdź na koniec adresu URL na pasku adresu i sprawdź, czy zapytanie jest nadal przesyłane do `Who is the boss of Jill Jones?` nowego przewidywania.

    ```json
    {
        "query": "Who is the boss of Jill Jones?",
        "prediction": {
            "topIntent": "OrgChart-Manager",
            "intents": {
                "OrgChart-Manager": {
                    "score": 0.999999046
                },
                "OrgChart-Reports": {
                    "score": 3.237443E-05
                },
                "EmployeeFeedback": {
                    "score": 4.364242E-06
                },
                "GetJobInformation": {
                    "score": 1.616159E-06
                },
                "MoveEmployee": {
                    "score": 7.575752E-07
                },
                "ApplyForJob": {
                    "score": 5.234157E-07
                },
                "None": {
                    "score": 3.3E-09
                },
                "Utilities.StartOver": {
                    "score": 1.26E-09
                },
                "FindForm": {
                    "score": 1.13636367E-09
                },
                "Utilities.Cancel": {
                    "score": 1.13636367E-09
                },
                "Utilities.Confirm": {
                    "score": 1.13636367E-09
                },
                "Utilities.Help": {
                    "score": 1.13636367E-09
                },
                "Utilities.Stop": {
                    "score": 1.13636367E-09
                }
            },
            "entities": {
                "keyPhrase": [
                    "boss of Jill Jones"
                ],
                "EmployeeListEntity": [
                    [
                        "Employee-45612"
                    ]
                ],
                "$instance": {
                    "keyPhrase": [
                        {
                            "type": "builtin.keyPhrase",
                            "text": "boss of Jill Jones",
                            "startIndex": 11,
                            "length": 18,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "EmployeeListEntity": [
                        {
                            "type": "EmployeeListEntity",
                            "text": "Jill Jones",
                            "startIndex": 19,
                            "length": 10,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

Prognoza intencji jest teraz znacznie bardziej nieświadoma, a następna najwyższa ocena jest bardzo niska. Te dwa intencje nie będą przerzucać flop podczas uczenia się.

### <a name="working-with-optional-text-and-prebuilt-entities"></a>Praca z tekstem opcjonalnym i wstępnie skompilowanymi jednostkami

Poprzednie wypowiedzi szablonu wzorca w tym samouczku miały kilka przykładów opcjonalnego tekstu, np. użycie formy dzierżawczej pod postacią litery s `'s` (dotyczy języka angielskiego) i stosowanie znaku zapytania `?`. Załóżmy, że musisz zezwolić na bieżące i przyszłe daty w tekście wypowiedź.

Przykładowe wypowiedzi:

|Zamiar|Przykładowe wypowiedzi z opcjonalnym tekstem i wstępnie skompilowanymi jednostkami|
|:--|:--|
|OrgChart-Manager|`Who was Jill Jones manager on March 3?`|
|OrgChart-Manager|`Who is Jill Jones manager now?`|
|OrgChart-Manager|`Who will be Jill Jones manager in a month?`|
|OrgChart-Manager|`Who will be Jill Jones manager on March 3?`|

Każdy z tych przykładów używa czasowników w różnych czasach: `was`, `is`, `will be`, a także daty: `March 3`, `now` i `in a month`, których usługa LUIS potrzebuje do poprawnego przewidywania. Zwróć uwagę, że ostatnie dwa przykłady w tabeli używają niemal tego samego tekstu, z wyjątkiem `in` i `on` .

Przykładowy wyrażenia długości szablonu, który zezwala na te informacje opcjonalne:

|Zamiar|Przykładowe wypowiedzi z opcjonalnym tekstem i wstępnie skompilowanymi jednostkami|
|:--|:--|
|OrgChart-Manager|`who was {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|
|OrgChart-Manager|`who is {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|


Korzystanie z opcjonalnych składni z nawiasami kwadratowymi, `[]`, sprawia, że ten opcjonalny tekst jest łatwy do dodania do wypowiedzi szablonu i może być zagnieżdżony najwyżej do drugiego poziomu, `[[]]`, a także może zawierać tekst lub jednostki.


**Pytanie: dlaczego wszystkie `w` litery, pierwsza litera w każdym szablonie wypowiedź, małe litery? Nie powinno być opcjonalnie wielkie czy małe?** Wypowiedź przesłana do punktu końcowego zapytania przez aplikację kliencką jest konwertowana na małe litery. Wypowiedź szablonu może zaczynać się dużą lub małą literą, podobnie jak wypowiedź punktu końcowego. Porównanie odbywa się zawsze po konwersji na małe litery.

**Pytanie: dlaczego wstępnie skompilowany numer nie jest częścią wypowiedzi szablonu, jeśli March 3 (3 marca) jest przewidziany zarówno jako liczba `3`, jak i data `March 3`?** Wypowiedź szablonu kontekstowo używa daty — albo dosłownie jak w wypowiedzi `March 3`, albo abstrakcyjnie jak w wypowiedzi `in a month`. Data może zawierać liczbę, ale liczba nie musi być zawsze postrzegana jako data. Należy zawsze używać jednostki najlepiej reprezentującej typ, który ma być zwracany w wynikach przewidywania JSON.

**Pytanie: co z niepoprawnie zapisanymi wypowiedziami, takimi jak `Who will {EmployeeListEntity}['s] manager be on March 3?`.** Gramatycznie różne czasy czasowników, takie jak tu, gdzie `will` i `be` są rozdzielone, muszą być nową wypowiedzią szablonu. Istniejąca wypowiedź szablonu nie będzie z tym zgodna. Mimo że intencja wypowiedzi nie została zmieniona, zmieniło się umieszczenie słowa w wypowiedzi. Ta zmiana ma wpływ na przewidywanie w usłudze LUIS. Aby połączyć te wyrażenia długości [, można grupować i lub](#use-the-or-operator-and-groups) wystawić zlecenia.

> [!CAUTION]
> **Pamiętaj: najpierw znajdowane są jednostki, a następnie dopasowywany jest wzorzec.**

### <a name="add-new-pattern-template-utterances"></a>Dodawanie nowych wypowiedzi szablonu wzorca

1. W sekcji **Patterns** (Wzorce) części **Build** (Kompiluj) dodaj kilka nowych wypowiedzi szablonu wzorca. Wybierz pozycję **OrgChart-Manager** (Schemat organizacyjny — menedżer) z menu rozwijanego Intent (Intencja) i wprowadź każdą z następujących wypowiedzi szablonu:

    |Zamiar|Przykładowe wypowiedzi z opcjonalnym tekstem i wstępnie skompilowanymi jednostkami|
    |--|--|
    |OrgChart-Manager|`who was {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[in]{datetimeV2}?]`|
    |OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|

2. Wybierz pozycję **szkolenie** na pasku nawigacyjnym, aby szkolić aplikację.

3. Po zakończeniu szkolenia wybierz pozycję **Testuj** w górnej części panelu, aby otworzyć panel testowania.

4. Wprowadź kilka testowych wypowiedzi, aby sprawdzić, czy wzorzec jest dopasowany i czy wynik intencji jest znacząco wysoki.

    Po wprowadzeniu pierwszej wypowiedzi wybierz polecenie **Inspect** (Sprawdź) w obszarze wyniku, dzięki czemu można zobaczyć wszystkie wyniki przewidywania. Każdy wypowiedź powinien mieć zamiarowy **schemat organizacyjny** i powinien wyodrębnić wartości dla `EmployeeListEntity` jednostek i `datetimeV2` .

    |Wypowiedź|
    |--|
    |`Who will be Jill Jones manager`|
    |`who will be jill jones's manager`|
    |`Who will be Jill Jones's manager?`|
    |`who will be Jill jones manager on March 3`|
    |`Who will be Jill Jones manager next Month`|
    |`Who will be Jill Jones manager in a month?`|

Wszystkie te wypowiedzi znalazły jednostki wewnątrz, dlatego pasują do tego samego wzorca i mają wysoki wynik przewidywania. Dodano kilka wzorców, które będą odpowiadały wielu wahaniom wyrażenia długości. Nie musisz dodawać żadnego przykładu wyrażenia długości w celu, aby szablon wypowiedź działał we wzorcu.

Takie użycie wzorców:
* Wyższe wyniki przewidywania
* Z tym samym przykładem wyrażenia długości w zamiarze
* Za pomocą zaledwie kilku dobrze skonstruowanych szablonów wyrażenia długości w wzorcu

### <a name="use-the-or-operator-and-groups"></a>Używanie operatora OR i grup

Kilka z poprzedniego wyrażenia długości szablonu jest bardzo blisko. Użyj **grupy** `()` i **lub** `|` składni, aby zmniejszyć wyrażenia długości szablonu.

Poniższe dwa wzorce mogą łączyć się w jeden wzorzec przy użyciu grupy `()` i lub `|` składni.

|Zamiar|Przykładowe wypowiedzi z opcjonalnym tekstem i wstępnie skompilowanymi jednostkami|
|--|--|
|OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[in]{datetimeV2}?]`|
|OrgChart-Manager|`who will be {EmployeeListEntity}['s] manager [[on]{datetimeV2}?]`|

Nowy szablon wypowiedź będzie:

`who ( was | is | will be ) {EmployeeListEntity}['s] manager [([in]|[on]){datetimeV2}?]`.

Powoduje to użycie **grupy** wokół wymaganego `in` `on` przedziału i opcjonalne i z **lub** z potokiem między nimi.

> [!NOTE]
> Przy użyciu symbolu _lub_ `|` (potoku), pamiętaj, aby oddzielić Symbol potoku przed i po nim w przykładowym szablonie.

1. Na stronie **wzorce** wybierz filtr **schemat organizacyjny — Menedżer** . Zawęź listę, wyszukując frazę `manager` .

1. Zachowaj jedną wersję szablonu wypowiedź (do edycji w następnym kroku) i Usuń inne różnice.

1. Zmień wypowiedź szablonu na: 

    `who ( was | is | will be ) {EmployeeListEntity}['s] manager [([in]|[on]){datetimeV2}?]`

2. Wybierz pozycję **szkolenie** na pasku nawigacyjnym, aby szkolić aplikację.

3. Po zakończeniu szkolenia wybierz pozycję **Testuj** w górnej części panelu, aby otworzyć panel testowania.

    Użyj okienka test do testowania wersji wypowiedź:

    |Wyrażenia długości do wprowadzenia w okienku testu|
    |--|
    |`Who is Jill Jones manager this month`|
    |`Who is Jill Jones manager on July 5th`|
    |`Who was Jill Jones manager last month`|
    |`Who was Jill Jones manager on July 5th`|
    |`Who will be Jill Jones manager in a month`|
    |`Who will be Jill Jones manager on July 5th`|

Używając większej składni wzorców, można zmniejszyć liczbę wyrażenia długości szablonów, które mają być utrzymywane w aplikacji, przy jednoczesnym zachowaniu wysokiej prognozowanych wyników.

### <a name="use-the-utterance-beginning-and-ending-anchors"></a>Używanie kotwic początkowych i końcowych wypowiedź

Składnia wzorca zapewnia początkową i końcową składnię zakotwiczenia wypowiedź karetki `^` . Kotwice początkową i końcową wypowiedź mogą być używane razem w celu określania wartości docelowej i możliwego do oddzielenia literału wypowiedź lub użycia oddzielnie do celów docelowych.

## <a name="using-patternany-entity"></a>Używanie jednostki Pattern.any

[!INCLUDE [Pattern.any entity - concepts](./includes/pattern-any-entity.md)]

### <a name="add-example-utterances-with-patternany"></a>Dodaj przykład wyrażenia długości ze wzorcem. any

1. Wybierz pozycję **Build** (Kompiluj) na górnym pasku nawigacyjnym i wybierz pozycję **Intents** (Intencje) na lewym pasku nawigacyjnym.

1. Z listy intencji wybierz pozycję **FindForm** (Znajdź formularz).

1. Dodaj przykład wyrażenia długości. Tekst, który powinien być przewidziany jako wzorzec. dowolny jest **pogrubiony tekst**. Nazwa formularza jest trudna do określenia z innych wyrazów w wypowiedź. Wzorzec. wszystkie będą pomocne poprzez oznaczenie granic jednostki.

    |Przykładowa wypowiedź|Nazwa formularza|
    |--|--|
    |Where is the form **What to do when a fire breaks out in the Lab** and who needs to sign it after I read it? (Gdzie jest formularz Postępowanie w razie pożaru w laboratorium i kto musi go podpisać, gdy go przeczytam?)|Co zrobić, gdy pożar zostanie przerwany w laboratorium
    |Where is **Request relocation from employee new to the company 2018 version 5** on the server? (Gdzie jest formularz Żądanie przeniesienia nowego pracownika do firmy 2018 wersja 5 na serwerze?)|Żądaj relokacji od pracownika nowego do firmy|
    |Who authored "**Health and wellness requests on the main campus**" and what is the most current version? (Kto jest autorem formularza „Wymagania dotyczące zdrowia i dobrego samopoczucia na głównym kampusie” i jaka jest najbardziej aktualna wersja?)|Żądania dotyczące kondycji i wellness na głównym kampusie|
    |I'm looking for the form named "**Office move request including physical assets**". (Szukam formularza o nazwie „Żądanie przeniesienia biura łącznie z zasobami fizycznymi”.) |Żądanie przeniesienia pakietu Office z uwzględnieniem zasobów fizycznych|

    Bez jednostki Pattern.any usługa LUIS może mieć trudność ze zrozumieniem, gdzie kończy się tytuł formularza ze względu na wiele różnych nazw formularzy.

### <a name="create-a-patternany-entity"></a>Tworzenie jednostki Pattern.any
Jednostka Pattern.any wyodrębnia jednostki o różnej długości. Działa tylko we wzorcu, ponieważ wzorzec oznacza początek i koniec jednostki z składnią.

1. Wybierz pozycję **Entities** (Jednostki) na lewym pasku nawigacyjnym.

1. Wybierz pozycję **+ Utwórz**, wprowadź nazwę `FormName` i wybierz opcję **wzorzec. dowolny** jako typ. Wybierz przycisk **Utwórz**.

### <a name="add-a-pattern-that-uses-the-patternany"></a>Dodawanie wzorca, który używa jednostki Pattern.any

1. Wybierz pozycję **Patterns** (Wzorce) na lewym pasku nawigacyjnym.

1. Wybierz intencję **FindForm** (Znajdź formularz).

1. Wprowadź następujące wypowiedzi szablonu, które używają nowej jednostki:

    |Wypowiedzi szablonu|
    |--|
    |`Where is the form ["]{FormName}["] and who needs to sign it after I read it[?]`|
    |`Where is ["]{FormName}["] on the server[?]`|
    |`Who authored ["]{FormName}["] and what is the most current version[?]`|
    |`I'm looking for the form named ["]{FormName}["][.]`|

1. Przeszkol aplikację.

### <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Testowanie nowego wzorca do wyodrębnienia danych o formacie swobodnym
1. Na górnym pasku wybierz pozycję **Test** (Testuj), aby otworzyć panel testu.

1. Wprowadź następującą wypowiedź:

    `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`

1. W obszarze wyniku wybierz pozycję **Inspect** (Sprawdź), aby zobaczyć wyniki testu jednostki i intencji.

    Najpierw znaleziono jednostkę `FormName`, następnie znaleziono wzorzec określający intencję. Jeśli otrzymano wynik testu, w którym jednostki nie zostały wykryte, a w związku z tym nie znaleziono wzorca, należy dodać więcej przykładowych wypowiedzi do intencji (nie wzorca).

1. Zamknij panel testu, wybierając przycisk **Test** (Testuj) na górnym pasku nawigacyjnym.

### <a name="using-an-explicit-list"></a>Korzystanie z listy jawnej

Jeśli zauważysz, że Twój wzorzec, gdy zawiera jednostkę Pattern.any, niepoprawnie wyodrębnia jednostki, użyj elementu [explicit list](reference-pattern-syntax.md#explicit-lists) (listy jawnej), aby rozwiązać ten problem.

## <a name="what-did-this-tutorial-accomplish"></a>Co zrobił ten samouczek?

W tym samouczku dodano wzorce umożliwiające LUIS przewidywalność zamiaru z znacznie wyższym wynikiem, bez konieczności dodawania więcej przykładowych wyrażenia długości. Oznaczanie jednostek i tekst możliwy do zignorowania pozwoliły usłudze LUIS na zastosowanie wzorca do szerszego zakresu wypowiedzi.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Następne kroki


> [!div class="nextstepaction"]
> [Dowiedz się, jak używać ról ze wzorcem](luis-tutorial-pattern.md)
