---
title: Dobry przykład wyrażenia długości-LUIS
description: Wypowiedzi to dane wejściowe od użytkownika, które Twoja aplikacja musi zinterpretować. Zbierz frazy, które będą wprowadzane przez użytkowników. Uwzględnij wyrażenia długości, które oznaczają te same czynności, ale są konstruowane inaczej niż długość słowa i umieszczanie wyrazów.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/19/2020
ms.openlocfilehash: 4c3b5674fe039a89eb97b915d13b5d472a6a0568
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316396"
---
# <a name="understand-what-good-utterances-are-for-your-luis-app"></a>Dowiedz się, co to jest dobry wyrażenia długości dla aplikacji LUIS

**Wyrażenia długości** są danymi wejściowymi od użytkownika, który musi być interpretowany przez aplikację. Aby przeprowadzić uczenie LUIS w celu wyodrębnienia z nich intencji i jednostek, ważne jest, aby przechwycić wiele różnych przykładowych wyrażenia długości dla każdego zamiaru. Proaktywne uczenie się lub proces ciągłego uczenia się z nowymi wyrażenia długościami jest niezwykle istotny dla analizy Machine-Learning, która zapewnia LUIS.

Zbieraj wyrażenia długości, że użytkownicy będą wprowadzać. Uwzględnij wyrażenia długości, co oznacza, że są one takie same, ale są zbudowane na różne sposoby:

* Wypowiedź długość — krótko, średnia i długa dla aplikacji klienckiej
* Długość wyrazu i frazy
* Położenie wyrazów — jednostka na początku, na środku i na końcu wypowiedź
* Gramatyka
* Pluralizacja
* Uszkodzeniem
* Wybór rzeczowników i czasowników
* [Interpunkcja](luis-reference-application-settings.md#punctuation-normalization) — dobra odmiana przy użyciu poprawnych, niepoprawnych i bez gramatyki

## <a name="how-to-choose-varied-utterances"></a>Jak wybrać różne wyrażenia długości

Po pierwszym uruchomieniu dodając [przykład wyrażenia długości](luis-how-to-add-example-utterances.md) do modelu Luis, należy wziąć pod uwagę pewne zasady.

### <a name="utterances-arent-always-well-formed"></a>Wyrażenia długości nie zawsze są poprawnie sformułowane

Może to być zdanie, na przykład "książka biletowa do Paryż dla mnie" lub fragment zdania, taki jak "rezerwacja" lub "lot paryski".  Użytkownicy często sprawiają błędy pisowni. Podczas planowania aplikacji należy rozważyć, czy przed przekazaniem jej do LUIS należy używać [Sprawdzanie pisowni Bing](luis-tutorial-bing-spellcheck.md) do poprawienia danych wejściowych użytkownika.

Jeśli nie sprawdzisz pisowni wyrażenia długości użytkownika, należy poszkolić LUIS na wyrażenia długości, które zawierają literówki i błędy pisowni.

### <a name="use-the-representative-language-of-the-user"></a>Korzystanie z języka reprezentatywnego dla użytkownika

Podczas wybierania wyrażenia długości należy pamiętać, że to, co myślisz, jest powszechny termin lub fraza może być niepoprawna dla typowego użytkownika aplikacji klienckiej. Mogą nie mieć środowiska domeny. Należy zachować ostrożność w przypadku używania warunków lub fraz, które użytkownik może powiedzieć tylko, jeśli byli ekspertami.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Wybierz zróżnicowaną terminologię, a także sformułowanie

Zobaczysz, że nawet jeśli podejmujesz wysiłki w celu utworzenia różnych wzorców zdań, nadal powtarzamy niektóre słownictwo.

Wykonaj następujące przykładowe wyrażenia długości:

|Przykładowe wypowiedzi|
|--|
|Jak uzyskać komputer?|
|Gdzie mogę uzyskać komputer?|
|Chcę uzyskać komputer, jak go zrobić?|
|Kiedy mogę mieć komputer?|

W tym miejscu termin "Computer" nie jest zróżnicowany. Używaj rozwiązań alternatywnych, takich jak komputer stacjonarny, laptop, stacja robocza, lub nawet dla komputera. LUIS może inteligentnie wywnioskować synonimy z kontekstu, ale w przypadku tworzenia wyrażenia długości do szkolenia są one zawsze lepsze od siebie.

## <a name="example-utterances-in-each-intent"></a>Przykład wyrażenia długości w każdym zamiarze

Każdy cel musi mieć przykład wyrażenia długości, co najmniej 15. Jeśli masz intencję, która nie ma żadnego przykładu wyrażenia długości, nie będziesz mieć możliwości uczenia się LUIS. Jeśli masz zamiar z jednym lub kilkoma przykładami wyrażenia długości, LUIS może nie dokładnie przewidzieć zamiar.

## <a name="add-small-groups-of-15-utterances-for-each-authoring-iteration"></a>Dodaj małe grupy 15 wyrażenia długości dla każdej iteracji tworzenia

W każdej iteracji modelu nie należy dodawać dużej liczby wyrażenia długości. Dodaj wyrażenia długości w ilościach 15. Ponownie [nauczenie](luis-how-to-train.md), [opublikowanie](luis-how-to-publish-app.md)i [przetestowanie](luis-interactive-test.md) .

LUIS kompiluje wydajne modele z wyrażenia długości, które są starannie wybierane przez autora modelu LUIS. Dodanie zbyt wielu wyrażenia długości nie jest cenne, ponieważ wprowadza pomyłkę.

Lepiej zacząć od kilku wyrażenia długościów, a następnie [przejrzeć punkt końcowy wyrażenia długości](luis-how-to-review-endpoint-utterances.md) w celu poprawnego przewidywania zamierzeń i wyodrębnienia jednostek.

## <a name="utterance-normalization"></a>Normalizacja wypowiedź

Normalizacja wypowiedź jest procesem ignorowania efektów typów tekstu, takich jak Interpunkcja i znaki diakrytyczne, podczas uczenia i przewidywania.

Ustawienia normalizacji wypowiedź są domyślnie wyłączone. Do tych ustawień należą:

* Formularze programu Word
* Znaki diakrytyczne
* Znaki interpunkcyjne

W przypadku włączenia ustawienia normalizacji wyniki w okienku **testów** , testy wsadowe i kwerendy punktów końcowych zmienią się dla wszystkich wyrażenia długości dla tego ustawienia normalizacji.

Po sklonowaniu wersji w portalu LUIS ustawienia wersji są nadal w nowej sklonowanej wersji.

Ustaw ustawienia wersji za pośrednictwem portalu LUIS, w sekcji **Zarządzanie** na stronie **Ustawienia aplikacji** lub [interfejsu API ustawień wersji aktualizacji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings). Dowiedz się więcej o tych zmianach normalizacji w [odwołaniu](luis-reference-application-settings.md).

### <a name="word-forms"></a>Formularze programu Word

Normalizacja **formularzy wyrazów** ignoruje różnice w wyrazach, które wykraczają poza katalog główny.

<a name="utterance-normalization-for-diacritics-and-punctuation"></a>

### <a name="diacritics"></a>Znaki diakrytyczne

Znaki diakrytyczne są znakami lub znakami w tekście, na przykład:

```
İ ı Ş Ğ ş ğ ö ü
```

### <a name="punctuation-marks"></a>Znaki interpunkcyjne
Normalizacja **interpunkcji** oznacza, że zanim modele są przeszkolone i zanim zapytania punktu końcowego zostaną przewidywalne, interpunkcja zostanie usunięta z wyrażenia długości.

Interpunkcja jest osobnym tokenem w LUIS. Wypowiedź, który zawiera kropkę na końcu, a wypowiedź, który nie zawiera kropki na końcu, to dwa oddzielne wyrażenia długości i mogą uzyskać dwa różne przewidywania.

Jeśli interpunkcja nie jest znormalizowana, LUIS nie ignoruje znaków interpunkcyjnych, domyślnie, ponieważ niektóre aplikacje klienckie mogą umieścić istotny wpływ na te znaki. Upewnij się, że przykład wyrażenia długości używać interpunkcji i bez znaków interpunkcyjnych, aby oba style zwracały te same wyniki względne.

Upewnij się, że model obsługuje interpunkcję w przykład wyrażenia długości (bez znaków interpunkcyjnych) lub w [wzorcach](luis-concept-patterns.md) , w których jest łatwiejsze ignorowanie interpunkcji przy użyciu specjalnej składni: `I am applying for the {Job} position[.]`

Jeśli interpunkcja nie ma określonego znaczenia w aplikacji klienckiej, należy wziąć pod uwagę [Ignorowanie interpunkcji](#utterance-normalization) przez normalizowanie interpunkcji.

### <a name="ignoring-words-and-punctuation"></a>Ignorowanie wyrazów i interpunkcji

Jeśli chcesz zignorować określone wyrazy lub interpunkcję w wzorcach, użyj [wzorca](luis-concept-patterns.md#pattern-syntax) z _ignorowaną_ składnią nawiasów kwadratowych `[]` .

<a name="training-utterances"></a>

## <a name="training-with-all-utterances"></a>Szkolenia ze wszystkimi wyrażenia długości

Szkolenie jest ogólnie niedeterministyczne: przewidywania wypowiedź może się nieco różnić w różnych wersjach lub aplikacjach.
Można usunąć niedeterministyczne szkolenie przez zaktualizowanie interfejsu API [ustawień wersji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) za pomocą `UseAllTrainingData` pary nazwa/wartość, aby używać wszystkich danych szkoleniowych.

## <a name="testing-utterances"></a>Testowanie wyrażenia długości

Deweloperzy powinni zacząć testować swoją aplikację LUIS z rzeczywistym ruchem, wysyłając wyrażenia długości do adresu URL [punktu końcowego przewidywania](luis-how-to-azure-subscription.md) . Te wyrażenia długości są używane do ulepszania wydajności intencji i jednostek z [przeglądem wyrażenia długości](luis-how-to-review-endpoint-utterances.md). Testy przesłane za pomocą okienka testowania witryny sieci Web LUIS nie są wysyłane za pomocą punktu końcowego i nie przyczyniają się do aktywnego uczenia.

## <a name="review-utterances"></a>Przegląd wyrażenia długości

Po przeszkoleniu, opublikowaniu i odebraniu zapytania dotyczącego [punktu końcowego](luis-glossary.md#endpoint) [Sprawdź wyrażenia długości](luis-how-to-review-endpoint-utterances.md) sugerowane przez Luis. LUIS wybiera punkt końcowy wyrażenia długości, który ma niskie wyniki dla zamiaru lub jednostki.

## <a name="best-practices"></a>Najlepsze rozwiązania

Zapoznaj się z [najlepszymi rozwiązaniami](luis-concept-best-practices.md) i zastosuj je w ramach regularnego cyklu tworzenia.

## <a name="label-for-word-meaning"></a>Etykieta wyrazu znaczenie

Jeśli wybór wyrazu lub uzgodnienie wyrazu są takie same, ale nie oznacza to tego samego znaczenia, nie należy oznaczyć go jednostką.

Następujący wyrażenia długości, słowo `fair` to homograph. Nazwa jest taka sama, ale ma inne znaczenie:

|Wypowiedź|
|--|
|Jakiego rodzaju targi są wykonywane w regionie Seattle w tym lato?|
|Czy aktualna ocena jest atrakcyjna dla przeglądu w Seattle?|

Jeśli chcesz, aby jednostka zdarzenia znalazła wszystkie dane zdarzeń, Oznacz słowo `fair` w pierwszej wypowiedź, ale nie w drugim.


## <a name="next-steps"></a>Następne kroki
Zobacz [Dodawanie przykładu wyrażenia długości](luis-how-to-add-example-utterances.md) , aby uzyskać informacje na temat szkolenia aplikacji Luis, aby poznać wyrażenia długości użytkownika.

