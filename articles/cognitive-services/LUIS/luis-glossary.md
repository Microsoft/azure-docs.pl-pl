---
title: Słownik — LUIS
description: Słownik objaśnia warunki, które mogą wystąpić podczas pracy z usługą interfejsu API LUIS.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 05/08/2020
ms.openlocfilehash: cc7dac3767ca5c9e2429f4691a458f88ad1af707
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151975"
---
# <a name="language-understanding-glossary-of-common-vocabulary-and-concepts"></a>Słownik dotyczący typowego słownictwa i koncepcji
W słowniku Language Understanding (LUIS) objaśniono warunki, które mogą wystąpić podczas pracy z usługą LUIS.

## <a name="active-version"></a>Aktywna wersja

Aktywna wersja to [wersja](luis-how-to-manage-versions.md) aplikacji, która jest aktualizowana po wprowadzeniu zmian w modelu przy użyciu portalu Luis. Jeśli chcesz wprowadzić zmiany w wersji, która nie jest wersją aktywną, w portalu LUIS należy najpierw ustawić tę wersję jako aktywną.

## <a name="active-learning"></a>Aktywna nauka

Active Learning to technika uczenia maszynowego, w której jest używany model uczenia maszynowego do identyfikowania nowych przykładów do etykiet. W LUIS aktywne uczenie odwołuje się do dodawania wyrażenia długości z ruchu punktu końcowego, którego bieżące przewidywania są niejasne, aby poprawić model. Kliknij pozycję "Przejrzyj punkt końcowy wyrażenia długości", aby wyświetlić wyrażenia długości do etykiet.

Zobacz też:
* [Informacje koncepcyjne](luis-concept-review-endpoint-utterances.md)
* [Samouczek dotyczący przeglądania punktu końcowego wyrażenia długości](luis-tutorial-review-endpoint-utterances.md)
* Jak ulepszyć aplikację LUIS, [przeglądając punkt końcowy wyrażenia długości](luis-how-to-review-endpoint-utterances.md)

## <a name="application-app"></a>Aplikacja (aplikacja)

W LUIS, aplikacja lub aplikacja to zbiór zebranych przez maszynę modeli opartych na tym samym zestawie danych, które współdziałają z przewidywaniam intencji i jednostek w konkretnym scenariuszu. Każda aplikacja ma oddzielny punkt końcowy przewidywania.

Jeśli tworzysz bot HR, możesz mieć zestaw intencji, na przykład "czas urlopu harmonogramu", "informacje o korzyściach" i "Aktualizuj informacje osobiste" oraz jednostki dla każdego z tych elementów, które można grupować w jedną aplikację.

## <a name="authoring"></a>Tworzenie

Tworzenie umożliwia tworzenie i wdrażanie aplikacji LUIS oraz zarządzanie nią przy użyciu portalu LUIS lub interfejsów API tworzenia.

### <a name="authoring-key"></a>Klucz tworzenia

[Klucz autorstwo](luis-how-to-azure-subscription.md) służy do tworzenia aplikacji. Nieużywane dla zapytań punktów końcowych na poziomie produkcyjnym. Aby uzyskać więcej informacji, zobacz [limity kluczy](luis-limits.md#key-limits).

### <a name="authoring-resource"></a>Tworzenie zasobu

[Zasób autorstwa](luis-how-to-azure-subscription.md#azure-resources-for-luis) Luis jest elementem możliwym do zarządzania, który jest dostępny za pomocą platformy Azure. Zasób to dostęp do skojarzonych możliwości tworzenia, uczenia i publikowania usługi platformy Azure. Zasób obejmuje informacje o uwierzytelnianiu, autoryzacji i zabezpieczeniach potrzebne do uzyskania dostępu do skojarzonej usługi platformy Azure.

Zasób tworzenia ma "rodzaj" platformy Azure `LUIS-Authoring` .

## <a name="batch-test"></a>Test wsadowy

Testowanie wsadowe to możliwość sprawdzenia, czy bieżące modele aplikacji LUIS mają spójny i znany zestaw testów wyrażenia długości użytkownika. Test wsadowy jest zdefiniowany w [pliku sformatowanym w formacie JSON](luis-concept-batch-test.md#batch-file-format).

Zobacz też:
* [Pojęcia](luis-concept-batch-test.md)
* [Jak](luis-how-to-batch-test.md) uruchomić test wsadowy
* [Samouczek](luis-tutorial-batch-testing.md) — tworzenie i uruchamianie testu wsadowego

### <a name="f-measure"></a>Miara F

W testach wsadowych mierzy się dokładność testu.

### <a name="false-negative-fn"></a>Wartość fałszywa ujemna (FN)

W testach wsadowych punkty danych przedstawiają wyrażenia długości, w których aplikacja niepoprawnie przewidywalna nieobecność docelowego celu/jednostki.

### <a name="false-positive-fp"></a>Fałszywie dodatnie (FP)

W testach wsadowych punkty danych reprezentują wyrażenia długości, w których aplikacja niepoprawnie przewidywalna istnienie docelowego celu/jednostki.

### <a name="precision"></a>Precyzja
W testach wsadowych precyzja (zwana również dodatnią wartością predykcyjną) jest częścią odpowiednich wyrażenia długości wśród pobranego wyrażenia długościu.

Przykładem testu partii zwierząt jest liczba owiec przewidzianych przez łączną liczbę zwierząt (owiec i nieowiec).

### <a name="recall"></a>Recall

W testach wsadowych funkcja odwoływania (znana także jako czułość) jest LUIS do uogólniania.

Przykładem testu partii zwierząt jest liczba hodowli, które zostały przewidywalne podzielone przez łączną liczbę dostępnych owiec.

### <a name="true-negative-tn"></a>True ujemna (TN)

Wartość true oznacza, że aplikacja prawidłowo przewidywalna niezgodność. W testach wsadowych prawdziwa wartość true występuje, gdy aplikacja przewidywalna zamiar lub jednostkę dla przykładu, który nie został oznaczony etykietą z tym zamiarem lub jednostką.

### <a name="true-positive-tp"></a>Prawda dodatnia (TP)

Prawdziwe dodatnie (TP) wynik pozytywny to, gdy aplikacja prawidłowo przewidywalna dopasowanie. W testach wsadowych prawdziwa wartość dodatnia występuje, gdy aplikacja przewiduje zamiar lub jednostkę dla przykładu, który został oznaczony tym zamiarem lub jednostką.

## <a name="classifier"></a>Klasyfikator

Klasyfikator to komputerowy model, który przewiduje, jakie kategorie lub klasy są uwzględniane przez dane wejściowe.

[Zamiarem](#intent) jest przykład klasyfikatora.

## <a name="collaborator"></a>Współpracownik

Współpracownik jest koncepcyjnie taki sam jak [współautor](#contributor). Współpracownikowi uzyskuje się dostęp, gdy właściciel dodaje adres e-mail współpracownika do aplikacji, która nie jest kontrolowana przy użyciu kontroli dostępu opartej na rolach (Azure RBAC). Jeśli nadal używasz współpracowników, należy przeprowadzić migrację konta usługi LUIS i używać zasobów tworzenia LUIS do zarządzania współautorami przy użyciu funkcji kontroli dostępu platformy Azure.

## <a name="contributor"></a>Współautor

Współautor nie jest [właścicielem](#owner) aplikacji, ale ma takie same uprawnienia, aby dodawać, edytować i usuwać intencje, jednostki, wyrażenia długości. Współautor udostępnia funkcję kontroli dostępu opartej na rolach (Azure RBAC) na platformie Azure do aplikacji LUIS.

Zobacz też:
* [Jak](luis-how-to-collaborate.md#add-contributor-to-azure-authoring-resource) dodać współautorów

## <a name="descriptor"></a>Opis

Deskryptor jest terminem wcześniej używanym dla [funkcji](#features)uczenia maszynowego.

## <a name="domain"></a>Obszar

W kontekście LUIS domena jest obszarem wiedzy. Twoja domena jest specyficzna dla Twojego scenariusza. Różne domeny używają określonego języka i terminologii mającej znaczenie w kontekście domeny. Na przykład, jeśli tworzysz aplikację do odtwarzania muzyki, aplikacja będzie miała warunki i język specyficzne dla utworów muzycznych — słowa takie jak "utwór, śledzenie, album, teksty, b, wykonawca". Przykłady domen można znaleźć w temacie [prebudowane domeny](#prebuilt-domain).

## <a name="endpoint"></a>Punkt końcowy

### <a name="authoring-endpoint"></a>Punkt końcowy tworzenia

Adres URL punktu końcowego autorstwa LUIS to miejsce, w którym można tworzyć, wyszkolić i publikować aplikację. Adres URL punktu końcowego zawiera region lub niestandardową poddomenę opublikowanej aplikacji oraz identyfikator aplikacji.

Dowiedz się więcej o programistycznym tworzeniu aplikacji z [dokumentacji dla deweloperów](developer-reference-resource.md#rest-endpoints)

### <a name="prediction-endpoint"></a>Punkt końcowy przewidywania

Adres URL punktu końcowego przewidywania LUIS to miejsce, w którym przesyłane są zapytania LUIS po utworzeniu i opublikowaniu [aplikacji Luis](#application-app) . Adres URL punktu końcowego zawiera region lub niestandardową poddomenę opublikowanej aplikacji oraz identyfikator aplikacji. Punkt końcowy można znaleźć na stronie **[zasobów platformy Azure](luis-how-to-azure-subscription.md)** swojej aplikacji lub uzyskać adres URL punktu końcowego za pomocą interfejsu API [uzyskiwania informacji o aplikacji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c37) .

Dostęp do punktu końcowego przewidywania jest autoryzowany za pomocą klucza przewidywania LUIS.

## <a name="entity"></a>Jednostka

[Jednostki](luis-concept-entity-types.md) są słowami w wyrażenia długości, które opisują informacje używane do spełnienia lub identyfikacji zamierzenia. Jeśli jednostka jest złożona i chcesz, aby model zidentyfikował konkretne części, możesz przerwać model w podjednostkach. Na przykład możesz chcieć modelować, aby przewidzieć adres, ale również podjednostki ulica, miasto, Województwo i kod pocztowy. Jednostki mogą być również używane jako funkcje do modeli. Odpowiedź z aplikacji LUIS będzie zawierać zarówno przewidywane intencje, jak i wszystkie jednostki.

### <a name="entity-extractor"></a>Ekstraktor jednostek

Ekstraktor jednostki czasami znany tylko jako Ekstraktor jest typem modelu uczenia maszynowego, który LUIS używa do przewidywania jednostek.

### <a name="entity-schema"></a>Schemat jednostki

Schemat jednostki to struktura zdefiniowana dla maszyn z obiektami podobiektami. Punkt końcowy przewidywania zwraca wszystkie wyodrębnione jednostki i podjednostki zdefiniowane w schemacie.

### <a name="entitys-subentity"></a>Podjednostka jednostki

Podjednostka jest jednostką podrzędną jednostki uczenia maszynowego.

### <a name="non-machine-learning-entity"></a>Jednostka niebędąca maszyną szkoleniową

Jednostka, która używa dopasowywania tekstu do wyodrębniania danych:
* Jednostka listy
* Jednostka wyrażenia regularnego

### <a name="list-entity"></a>Jednostka listy

[Jednostka listy](reference-entity-list.md) reprezentuje stały, zamknięty zestaw powiązanych słów wraz z ich synonimami. Jednostki listy są dokładnymi dopasowaniami, w przeciwieństwie do jednostek obsługiwanych przez maszynę.

Jednostka zostanie przewidywalna, jeśli na liście zostanie uwzględniony wyraz znajdujący się na liście. Na przykład jeśli masz jednostkę listy o nazwie "size" i masz słowa "małe, średnie, duże" na liście, jednostka rozmiaru zostanie przewidywalna dla wszystkich wyrażenia długości, gdzie słowa "małe", "średnie" lub "duże" są używane niezależnie od kontekstu.

### <a name="regular-expression"></a>Wyrażenie regularne

[Jednostka wyrażenia regularnego](reference-entity-regular-expression.md) reprezentuje wyrażenie regularne. Jednostki wyrażenia regularnego są dokładnymi dopasowaniami, w przeciwieństwie do jednostek obsługiwanych przez maszynę.
### <a name="prebuilt-entity"></a>Wstępnie utworzona jednostka

Zobacz wpis prekompilowanego modelu dla [wstępnie skompilowanej jednostki](#prebuilt-entity)

## <a name="features"></a>Funkcje

W uczeniu maszynowym funkcja jest cechą, która pomaga modelowi rozpoznać konkretną koncepcję. Jest to wskazówka, która może być używana przez LUIS, ale nie na trudnej zasadzie.

Ten termin jest również nazywany **[funkcją uczenia maszynowego](luis-concept-feature.md)**.

Te wskazówki są używane w połączeniu z etykietami, aby dowiedzieć się, jak przewidzieć nowe dane. LUIS obsługuje obie listy fraz i używa innych modeli jako funkcji.

### <a name="required-feature"></a>Wymagana funkcja

Wymagana funkcja jest sposobem ograniczenia danych wyjściowych modelu LUIS. Gdy funkcja dla jednostki jest oznaczona jako wymagana, funkcja musi być obecna w przykładzie dla jednostki, która ma zostać przewidywalna, niezależnie od tego, co zapoznaje się z przewidywaniami modelu.

Rozważmy przykład, w którym masz wbudowaną funkcję, która została oznaczona jako wymagana w jednostce ilości dla kolejności menu bot. Gdy bot widzi `I want a bajillion large pizzas?` , bajillion nie będzie przewidywane jako ilość, niezależnie od kontekstu, w którym występuje. Bajillion nie jest prawidłową liczbą i nie będzie przewidywany przez numer wstępnie skompilowanej jednostki.

## <a name="intent"></a>Zamiar

[Cel](luis-concept-intent.md) reprezentuje zadanie lub akcję, które użytkownik chce wykonać. Jest to cel lub cel wyrażony w danych wejściowych użytkownika, na przykład w ramach rezerwacji lub płacenia rachunku. W LUIS, element wypowiedź jako całość jest sklasyfikowany jako cel, ale części wypowiedź są wyodrębniane jako jednostki

## <a name="labeling-examples"></a>Przykłady etykiet

Oznacza to, że jest to proces kojarzenia pozytywnego lub negatywnego przykładu z modelem.

### <a name="labeling-for-intents"></a>Etykietowanie dla intencji
W LUIS, intencje w aplikacji wykluczają się wzajemnie. Oznacza to, że po dodaniu wypowiedź do zamiaru jest on traktowany jako _pozytywny_ przykład dla tego celu i _negatywny_ przykład dla wszystkich innych intencji. Przykładami negatywnymi nie należy mylić z zamiarem "none", który reprezentuje wyrażenia długości, które są poza zakresem aplikacji.

### <a name="labeling-for-entities"></a>Etykietowanie jednostek
W LUIS można [oznaczyć](label-entity-example-utterance.md) słowo lub frazę w przykładowym przykładzie wypowiedź z jednostką jako _pozytywnego_ przykładu. Etykietowanie przedstawia zamierzenia, co powinno być przewidywalne dla tego wypowiedź. Etykiety wyrażenia długości są używane do uczenia zamiaru.

## <a name="luis-app"></a>Aplikacja LUIS

Zobacz definicję [aplikacji (aplikacji)](#application-app).

## <a name="model"></a>Model

Model (z maszyną) to funkcja, która wykonuje prognozowanie danych wejściowych. W programie LUIS odnosimy się do klasyfikatorów intencji i ekstraktorów jednostek ogólnie jako "models", a my odwołujemy się do kolekcji modeli, które są przeszkolone, publikowane i badane razem jako "aplikacja".

## <a name="normalized-value"></a>Znormalizowana wartość

Dodaj wartości do jednostek [listy](#list-entity) . Każda z tych wartości może zawierać listę jednego lub kilku synonimów. W odpowiedzi jest zwracana tylko znormalizowana wartość.

## <a name="overfitting"></a>Nadmierne dopasowanie

Przemontowanie następuje, gdy model jest odwzoruje się w określonych przykładach i nie jest w stanie uogólniać się prawidłowo.

## <a name="owner"></a>Właściciel

Każda aplikacja ma jednego właściciela, który jest osobą, która utworzyła aplikację. Właściciel zarządza uprawnieniami do aplikacji w Azure Portal.

## <a name="phrase-list"></a>Lista fraz

[Lista fraz](luis-concept-feature.md) jest określonym typem funkcji uczenia maszynowego, która obejmuje grupę wartości (wyrazów lub fraz) należących do tej samej klasy i musi być traktowana podobnie (na przykład nazwy miast lub produktów).

## <a name="prebuilt-model"></a>Prekompilowany model

[Wstępnie skonstruowany model](luis-concept-prebuilt-model.md) jest celem, jednostką lub kolekcją obu, wraz z przykładami z etykietami. Te popularne wstępnie skompilowane modele można dodać do aplikacji, aby zmniejszyć liczbę zadań związanych z programowaniem modelu wymaganych przez aplikację.

### <a name="prebuilt-domain"></a>Preskompilowana domena

Preskompilowana domena jest aplikacją LUIS skonfigurowaną dla konkretnej domeny, taką jak Automatyzacja główna (HomeAutomation) lub rezerwacje restauracji (RestaurantReservation). Zamiary, wyrażenia długości i jednostki są skonfigurowane dla tej domeny.

### <a name="prebuilt-entity"></a>Wstępnie utworzona jednostka

Wstępnie utworzona jednostka to LUIS jednostki zapewniające wspólne typy informacji, takie jak Number, URL i email. Są one tworzone na podstawie danych publicznych. Możesz dodać wstępnie utworzoną jednostkę jako jednostkę autonomiczną lub jako funkcję do jednostki

### <a name="prebuilt-intent"></a>Wstępnie skompilowany profil

Prebudowany zamiara to LUIS, który oferuje popularne typy informacji i ma własne przykładowe etykiety wyrażenia długości.

## <a name="prediction"></a>Przewidywanie

Prognoza to żądanie REST do usługi predykcyjnej Azure LUIS, która przyjmuje nowe dane (użytkownik wypowiedź), i stosuje przeszkolone i opublikowane aplikacje do tych danych, aby określić, jakie intencje i jednostki są dostępne.

### <a name="prediction-key"></a>Klucz predykcyjny

[Klucz predykcyjny](luis-how-to-azure-subscription.md) (wcześniej znany jako klucz subskrypcji) to klucz skojarzony z usługą Luis utworzoną na platformie Azure, która autoryzuje użycie punktu końcowego przewidywania.

Ten klucz nie jest kluczem autorskim. Jeśli masz klucz punktu końcowego przewidywania, powinien on być używany w przypadku żądań punktu końcowego zamiast klucza tworzenia. Bieżący klucz przewidywania można zobaczyć w adresie URL punktu końcowego w dolnej części strony zasobów platformy Azure w witrynie sieci Web LUIS. Jest to wartość pary nazwa/wartość klucza subskrypcji.

### <a name="prediction-resource"></a>Zasób predykcyjny

Zasób przewidywania LUIS jest elementem możliwym do zarządzania, który jest dostępny za pomocą platformy Azure. Zasób to dostęp do powiązanego przewidywania usługi platformy Azure. Zasób obejmuje przewidywania.

Zasób predykcyjny ma "rodzaj" na platformie Azure `LUIS` .

### <a name="prediction-score"></a>Współczynnik przewidywania

[Wynik](luis-concept-prediction-score.md) jest liczbą z przenoszącą od 0 do 1, która jest miarą, że system polega na tym, że dane wejściowe wypowiedź są zgodne z konkretnym zamiarem. Wynik zbliżony do 1 oznacza, że system jest bardzo istotny dla danych wyjściowych, a wynik zbliżony do 0 oznacza, że system jest przekonany, że dane wejściowe nie pasują do określonych danych wyjściowych. Wyniki w środkowym znaczeniu oznacza, że system jest bardzo niekonieczny do podejmowania decyzji.

Na przykład Utwórz model, który służy do identyfikowania, czy część tekstu klienta zawiera zamówienie żywności. Może to dać wynik 1 dla "Chcę zamówić jedną kawę" (system ma bardzo pewność, że jest to zamówienie), a wynik 0 dla "mój zespół wykupiła grę w ostatnim nocy" (system jest bardzo pewny, że nie jest to zamówienie). Może mieć wynik 0,5 dla "mamy kilka herbaty" (nie ma pewności, czy jest to zamówienie, czy nie).

## <a name="programmatic-key"></a>Klucz programistyczny

Zmieniono nazwę [klucza tworzenia](#authoring-key).

## <a name="publish"></a>Opublikuj

[Opublikowanie](luis-how-to-publish-app.md) oznacza, że dostępna jest Luis aktywna wersja na etapie przejściowym [lub produkcyjnym](#endpoint).

## <a name="quota"></a>limit przydziału

Limit przydziału LUIS to ograniczenie warstwy subskrypcji platformy Azure. Limit przydziału LUIS może być ograniczony przez oba żądania na sekundę (stan HTTP 429) i łączne żądania w miesiącu (stan HTTP 403).

## <a name="schema"></a>Schemat

Schemat zawiera twoje intencje i jednostki wraz z podjednostkami. Schemat jest początkowo planowany, a następnie powtarzany w miarę upływu czasu. Schemat nie obejmuje ustawień aplikacji, funkcji ani przykładu wyrażenia długości.

## <a name="sentiment-analysis"></a>Analiza tonacji
Tonacji Analysis zawiera wartości dodatnie lub ujemne wyrażenia długości dostarczone przez [Analiza tekstu](../text-analytics/overview.md).

## <a name="speech-priming"></a>Napełnianiu mowy

Usługa Speech napełnianiu ulepsza rozpoznawanie mówionych słów i fraz, które są często używane w Twoim scenariuszu dzięki [usługom mowy](../speech-service/overview.md). W przypadku aplikacji obsługujących funkcję Speech napełnianiu wszystkie przykłady z etykietami LUIS są używane do poprawiania dokładności rozpoznawania mowy, tworząc dostosowany model mowy dla tej konkretnej aplikacji. Na przykład w grze szachowej warto upewnić się, że gdy użytkownik mówi "Przenieś Knight", nie jest interpretowany jako "Przenieś nocne". Aplikacja LUIS powinna zawierać przykłady, w których znak "Knight" jest oznaczony jako obiekt.

## <a name="starter-key"></a>Klucz początkowy

Bezpłatny klucz, który będzie używany podczas pierwszego uruchamiania przy użyciu LUIS.

## <a name="synonyms"></a>Synonimy

W [jednostkach listy](reference-entity-list.md)Luis można utworzyć znormalizowaną wartość, która może zawierać listę synonimów. Jeśli na przykład utworzysz jednostkę rozmiaru, która ma znormalizowane wartości małe, średnie, duże i bardzo duże. Można utworzyć synonimy dla każdej wartości w następujący sposób:

|Nomalized wartość| Synonimy|
|--|--|
|Mały| nieco jeden, 8 uncji|
|Średniaa| regularne, 12 uncji|
|Duży| Big, 16 uncji|
|Xtra duże| największy jeden, 24 uncje|

Model zwróci wartość znormalizowaną dla jednostki, gdy którykolwiek z synonimów jest widoczny w danych wejściowych.

## <a name="test"></a>Test

[Testowanie](luis-concept-test.md) aplikacji Luis oznacza przeglądanie prognoz modeli.

## <a name="timezone-offset"></a>Przesunięcie strefy czasowej

Punkt końcowy zawiera [timezoneOffset](luis-concept-data-alteration.md#change-time-zone-of-prebuilt-datetimev2-entity). Jest to liczba minut, którą chcesz dodać lub usunąć ze wstępnie skompilowanej jednostki datetimeV2. Na przykład, jeśli wypowiedź jest "jaki jest teraz?", zwracana wartość datetimeV2 to bieżąca godzina żądania klienta. Jeśli żądanie klienta pochodzi z bot lub innej aplikacji, która nie jest taka sama jak użytkownika bot, należy przekazać przesunięcie między bot i użytkownikiem.

Zobacz [Zmiana strefy czasowej prekompilowanej jednostki datetimeV2](luis-concept-data-alteration.md?#change-time-zone-of-prebuilt-datetimev2-entity).

## <a name="token"></a>Token
[Token](luis-language-support.md#tokenization) jest najmniejszą jednostką tekstu, którą Luis może rozpoznać. Różni się to nieco w różnych językach.

W przypadku **języka angielskiego**token jest ciągłym zakresem (bez spacji i znaków interpunkcyjnych) liter i cyfr. Spacja nie jest tokenem.

|Fraza|Liczba tokenów|Objaśnienie|
|--|--|--|
|`Dog`|1|Pojedynczy wyraz bez znaków interpunkcyjnych ani spacji.|
|`RMT33W`|1|Numer lokalizatora rekordu. Może zawierać cyfry i litery, ale nie ma żadnych znaków interpunkcyjnych.|
|`425-555-5555`|5|Numer telefonu. Każdy znak interpunkcji jest pojedynczym tokenem, więc będzie  `425-555-5555` miał 5 tokenów:<br>`425`<br>`-`<br>`555`<br>`-`<br>`5555` |
|`https://luis.ai`|7|`https`<br>`:`<br>`/`<br>`/`<br>`luis`<br>`.`<br>`ai`<br>|

## <a name="train"></a>Szkolenie

[Szkolenie](luis-how-to-train.md) to proces uczenia Luis o wszelkich zmianach w aktywnej wersji od ostatniego szkolenia.

### <a name="training-data"></a>Dane szkoleniowe

Dane szkoleniowe to zbiór informacji, które są konieczne do uczenia modelu. Obejmuje to schemat, etykiety wyrażenia długości, funkcje i ustawienia aplikacji.

### <a name="training-errors"></a>Błędy szkoleniowe

Błędy szkoleniowe są przewidywaniami danych szkoleniowych, które nie pasują do etykiet.

## <a name="utterance"></a>Wypowiedź

[Wypowiedź](luis-concept-utterance.md) to dane wprowadzane przez użytkownika, czyli krótki tekst reprezentatywny zdania w konwersacji. Jest to fraza języka naturalnego, taka jak "Book 2 bilety do Seattle następnego wtorku". Przykład wyrażenia długości są dodawane do uczenia modelu i przewidywania modeli na nowym wypowiedź w czasie wykonywania

## <a name="version"></a>Wersja

[Wersja](luis-how-to-manage-versions.md) Luis to określone wystąpienie aplikacji Luis skojarzonej z identyfikatorem aplikacji Luis oraz opublikowanym punktem końcowym. Każda aplikacja LUIS ma co najmniej jedną wersję.
