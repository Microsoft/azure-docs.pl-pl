---
title: Często zadawane pytania — LUIS
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące Language Understanding (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: troubleshooting
ms.date: 05/06/2020
ms.openlocfilehash: b5e25e9ed25ced96d38994928bcb6275ce79420f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102612800"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Usługa Language Understanding — często zadawane pytania

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące Language Understanding (LUIS).

## <a name="whats-new"></a>Co nowego

[Dowiedz się więcej](whats-new.md) na temat Nowości w programie Language UNDERSTANDING (Luis).

<a name="luis-authoring"></a>

## <a name="authoring"></a>Tworzenie

### <a name="what-are-the-luis-best-practices"></a>Jakie są najlepsze rozwiązania dotyczące LUIS?
Zacznij od [cyklu tworzenia](luis-concept-app-iteration.md), a następnie zapoznaj się z [najlepszymi rozwiązaniami](luis-concept-best-practices.md).

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>Jaki jest najlepszy sposób rozpoczęcia tworzenia aplikacji w usłudze LUIS?

Najlepszym sposobem na skompilowanie aplikacji jest [przetworzenie przyrostu](luis-concept-app-iteration.md).

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>Co to jest dobre rozwiązanie w zakresie modelowania intencji mojej aplikacji? Czy należy utworzyć bardziej szczegółowe lub bardziej ogólne intencje?

Wybierz intencje, które nie są tak ogólne, aby można je było nakładać, ale nie tak, aby utrudnić LUIS do rozróżnienia między podobnymi intencjami. Tworzenie konkretnych discriminative jest jednym z najlepszych rozwiązań dotyczących modelowania LUIS.

### <a name="is-it-important-to-train-the-none-intent"></a>Czy jest ważne, aby szkolić zamiaru braku?

Tak. dobrym sposobem jest uczenie się, że **nie ma żadnych** zamiaru wyrażenia długości w miarę dodawania kolejnych etykiet do innych intencji. Dobrym wskaźnikiem są etykiety 1 lub 2 dodane do elementu **none** dla każdej 10 etykiet dodanych do zamiaru. Ten współczynnik zwiększa możliwości discriminative LUIS.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Jak można poprawić błędy pisowni w wyrażenia długości?

Zobacz samouczek [Sprawdzanie pisowni Bing API wersji 7](luis-tutorial-bing-spellcheck.md) . LUIS wymusza limity narzucone przez sprawdzanie pisowni Bing API wersji 7.

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Jak mogę programowo edytować moją aplikację LUIS?
Aby programowo edytować aplikację LUIS, użyj [interfejsu API tworzenia](https://go.microsoft.com/fwlink/?linkid=2092087). Zobacz [wywołanie interfejsu API tworzenia Luis](./get-started-get-model-rest-apis.md) i [skompiluj aplikację Luis programowo przy użyciu Node.js](./luis-tutorial-node-import-utterances-csv.md) , aby poznać Przykłady sposobu wywoływania interfejsu API tworzenia. Interfejs API tworzenia wymaga użycia [klucza tworzenia](luis-how-to-azure-subscription.md#azure-resources-for-luis) zamiast klucza punktu końcowego. Program programistyczny umożliwia tworzenie do 1 000 000 wywołań miesięcznie i pięć transakcji na sekundę. Aby uzyskać więcej informacji na temat kluczy używanych z usługą LUIS, zobacz [Zarządzanie kluczami](./luis-how-to-azure-subscription.md).

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Gdzie jest funkcja wzorca, która zapewnia dopasowanie wyrażenia regularnego?
Funkcja poprzedniego **wzorca** jest obecnie przestarzała, zastępowana przez **[wzorce](luis-concept-patterns.md)**.

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Jak mogę użyć jednostki, aby pobrać poprawne dane?
Zobacz temat [jednostki](luis-concept-entity-types.md) i [wyodrębnianie danych](luis-concept-data-extraction.md).

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Czy Wariacje przykładu wypowiedź zawierają znaki interpunkcyjne?
Użyj jednego z następujących rozwiązań:
* Ignoruj [interpunkcję](luis-reference-application-settings.md#punctuation-normalization)
* Dodaj różne odmiany jako przykład wyrażenia długości do celu
* Dodaj wzorzec przykładowej wypowiedź z [składnią, aby zignorować](luis-concept-patterns.md#pattern-syntax) interpunkcję.

### <a name="does-luis-currently-support-cortana"></a>Czy usługa LUIS obecnie obsługuje Cortana?

Wstępnie utworzone aplikacje Cortany były przestarzałe w 2017. Nie są już obsługiwane.

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Jak mogę przenieść własności aplikacji LUIS?
Aby przenieść aplikację LUIS do innej subskrypcji platformy Azure, wyeksportuj aplikację LUIS i zaimportuj ją przy użyciu nowego konta. Zaktualizuj identyfikator aplikacji LUIS w aplikacji klienckiej, która ją wywołuje. Nowa aplikacja może zwracać nieco inne wyniki LUIS z oryginalnej aplikacji.

### <a name="a-prebuilt-entity-is-tagged-in-an-example-utterance-instead-of-my-custom-entity-how-do-i-fix-this"></a>Wstępnie utworzona jednostka jest oznaczona jako przykład wypowiedź zamiast mojej jednostki niestandardowej. Jak mogę rozwiązać ten problem?

W portalu LUIS można oznaczyć tekst dla dokładnej jednostki, która ma zostać wyodrębniona. Jeśli portal LUIS nie pokazuje poprawnego przewidywania jednostek, może być konieczne dodanie większej liczby wyrażenia długości i oznaczenie jednostki w tekście lub dodanie funkcji.

### <a name="i-tried-to-import-an-app-or-version-file-but-i-got-an-error-what-happened"></a>Próbuję zaimportować plik aplikacji lub wersji, ale wystąpił błąd, co się stało?

Przeczytaj więcej na temat [błędów importowania wersji](luis-how-to-manage-versions.md#import-errors).

<a name="luis-collaborating"></a>

## <a name="collaborating-and-contributing"></a>Współpraca i Współtworzenie

### <a name="how-do-i-give-collaborators-access-to-luis-with-azure-active-directory-azure-ad-or-azure-role-based-access-control-azure-rbac"></a>Jak mogę zapewnić współpracownikom dostęp do LUIS przy użyciu usługi Azure Active Directory (Azure AD) lub kontroli dostępu opartej na rolach (Azure RBAC)?

Zapoznaj się z tematem [Azure Active Directory zasobów](luis-how-to-collaborate.md#azure-active-directory-resources)  i [Azure Active Directory użytkownika dzierżawy](luis-how-to-collaborate.md#azure-active-directory-tenant-user) , aby dowiedzieć się, jak zapewnić współpracownikom dostęp.

<a name="luis-endpoint"></a>

## <a name="endpoint"></a>Punkt końcowy

### <a name="i-received-an-http-403-error-status-code-how-do-i-fix-it"></a>Otrzymałem kod stanu błędu HTTP 403. Jak go naprawić?

Kody stanu błędów 403 i 429 są uzyskiwane w przypadku przekroczenia liczby transakcji na sekundę lub transakcji miesięcznie dla warstwy cenowej. Zwiększ swoją warstwę cenową lub Użyj [kontenerów](luis-container-howto.md)Language Understanding.

W przypadku korzystania ze wszystkich bezpłatnych zapytań dotyczących punktów końcowych 1000 lub przekroczenia limitu przydziału miesięcznych transakcji warstwy cenowej występuje kod stanu błędu HTTP 403.

Aby naprawić ten błąd, należy [zmienić warstwę cenową](luis-how-to-azure-subscription.md#change-the-pricing-tier) na wyższą lub [utworzyć nowy zasób](get-started-portal-deploy-app.md#create-the-endpoint-resource) i [przypisać go do aplikacji](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal).

Rozwiązania tego błędu obejmują:

* W [Azure Portal](https://portal.azure.com), w Language Understanding zasobów, w **warstwie cenowej zarządzanie zasobami — >** Zmień warstwę cenową na wyższą warstwę TPS. Nie musisz wykonywać żadnych czynności w portalu Language Understanding, jeśli zasób został już przypisany do aplikacji Language Understanding.
*  Jeśli użycie przekroczy najwyższą warstwę cenową, należy dodać więcej Language Understanding zasobów przy użyciu modułu równoważenia obciążenia przed nimi. [Language Understanding kontener](luis-container-howto.md) z Kubernetes lub Docker Compose może Ci pomóc.

### <a name="i-received-an-http-429-error-status-code-how-do-i-fix-it"></a>Otrzymałem kod stanu błędu HTTP 429. Jak go naprawić?

Kody stanu błędów 403 i 429 są uzyskiwane w przypadku przekroczenia liczby transakcji na sekundę lub transakcji miesięcznie dla warstwy cenowej. Zwiększ swoją warstwę cenową lub Użyj [kontenerów](luis-container-howto.md)Language Understanding.

Ten kod stanu jest zwracany, gdy liczba transakcji na sekundę przekracza Twoją warstwę cenową.

Rozwiązania obejmują:

* Możesz [zwiększyć swoją warstwę cenową](luis-how-to-azure-subscription.md#change-the-pricing-tier), jeśli nie masz najwyższej warstwy.
* Jeśli użycie przekroczy najwyższą warstwę cenową, należy dodać więcej Language Understanding zasobów przy użyciu modułu równoważenia obciążenia przed nimi. [Language Understanding kontener](luis-container-howto.md) z Kubernetes lub Docker Compose może Ci pomóc.
* Po otrzymaniu tego kodu stanu można posłużyć do żądania aplikacji klienckich przy użyciu [zasad ponawiania](/azure/architecture/best-practices/transient-faults#general-guidelines) .

### <a name="my-endpoint-query-returned-unexpected-results-what-should-i-do"></a>Zapytanie o punkt końcowy zwróciło nieoczekiwane wyniki. Co mam zrobić?

Nieoczekiwane wyniki prognozowania zapytań są oparte na stanie opublikowanego modelu. Aby poprawić model, należy zmienić model, uczenie i opublikować ponownie.

Korygowanie modelu rozpoczyna się od [aktywnego uczenia](luis-how-to-review-endpoint-utterances.md)się.

Aby usunąć niedeterministyczne szkolenie, należy zaktualizować [interfejs API ustawień wersji aplikacji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) , aby można było używać wszystkich danych szkoleniowych.

Zapoznaj się z [najlepszymi rozwiązaniami](luis-concept-best-practices.md) dotyczącymi innych wskazówek.

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Dlaczego LUIS dodawać odstępy do zapytania na podstawie lub w środku wyrazów?
LUIS [tokenizes](luis-glossary.md#token) wypowiedź w oparciu o [kulturę](luis-language-support.md#tokenization). Zarówno oryginalna wartość, jak i wartość z tokenami są dostępne do [wyodrębnienia danych](luis-concept-data-extraction.md#tokenized-entity-returned).

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Jak mogę utworzyć i przypisać klucz punktu końcowego LUIS?
[Utwórz klucz punktu końcowego](luis-how-to-azure-subscription.md) na platformie Azure dla poziomu [usługi](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) . [Przypisz klucz](luis-how-to-azure-subscription.md) na stronie **[zasobów platformy Azure](luis-how-to-azure-subscription.md)** . Brak odpowiedniego interfejsu API dla tej akcji. Następnie należy zmienić żądanie HTTP do punktu końcowego, aby [użyć nowego klucza punktu końcowego](luis-how-to-azure-subscription.md).

### <a name="how-do-i-interpret-luis-scores"></a>Jak mogę interpretować wyników LUIS?
System powinien używać najwyższego przeznaczenie oceny, niezależnie od jego wartości. Na przykład, wynik poniżej 0,5 (mniejszy niż 50%) nie musi oznaczać, że LUIS ma niski poziom pewności. Zapewnianie większej ilości danych szkoleniowych może pomóc w zwiększeniu [oceny](luis-concept-prediction-score.md) najbardziej najprawdopodobniej zamierzonego celu.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Dlaczego nie widzę moich trafień punktu końcowego na pulpicie nawigacyjnym mojej aplikacji?
Łączna liczba trafień punktów końcowych w pulpicie nawigacyjnym aplikacji jest okresowo aktualizowana, ale metryki skojarzone z kluczem punktu końcowego LUIS w Azure Portal są aktualizowane częściej.

Jeśli na pulpicie nawigacyjnym nie widzisz zaktualizowanych trafień punktów końcowych, zaloguj się do Azure Portal i Znajdź zasób skojarzony z kluczem punktu końcowego LUIS, a następnie otwórz **metryki** , aby wybrać metrykę **całkowitej liczby wywołań** . Jeśli klucz punktu końcowego jest używany przez więcej niż jedną aplikację LUIS, Metryka w Azure Portal pokazuje zagregowaną liczbę wywołań ze wszystkich aplikacji LUIS, które go używają.

### <a name="is-there-a-powershell-command-get-to-the-endpoint-quota"></a>Czy istnieje polecenie programu PowerShell z przydziałem punktów końcowych?

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aby wyświetlić limit przydziału punktu końcowego, można użyć polecenia programu PowerShell:

```powershell
Get-AzCognitiveServicesAccountUsage -ResourceGroupName <your-resource-group> -Name <your-resource-name>
```

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>Moja aplikacja LUIS działała wczoraj, ale dzisiaj otrzymuję błędy 403. Aplikacja nie została zmieniona. Jak go naprawić?
Postępuj zgodnie z tymi [instrukcjami](#how-do-i-create-and-assign-a-luis-endpoint-key) , aby utworzyć klucz punktu końcowego Luis i przypisać go do aplikacji. Następnie należy zmienić żądanie HTTP aplikacji klienta do punktu końcowego, aby [użyć nowego klucza punktu końcowego](luis-how-to-azure-subscription.md). Jeśli nowy zasób został utworzony w innym regionie, Zmień również region żądania klienta HTTP.

### <a name="how-do-i-secure-my-luis-endpoint"></a>Jak mogę zabezpieczyć mojego punktu końcowego LUIS?
Zobacz [Zabezpieczanie punktu końcowego](luis-how-to-azure-subscription.md#securing-the-endpoint).

## <a name="working-within-luis-limits"></a>Praca w granicach LUIS

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>Jaka jest maksymalna liczba zamiar i jednostek, które może obsłużyć aplikacja LUIS?
Zobacz informacje o [granicach](luis-limits.md) .

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Chcę skompilować aplikację LUIS z więcej niż maksymalną liczbą intencji. Co mam zrobić?

Zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi intencji](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents).

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>Chcę skompilować aplikację w LUIS z więcej niż maksymalną liczbą jednostek. Co mam zrobić?

Zobacz [najlepsze rozwiązania dotyczące jednostek](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities)

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Jakie są limity liczby i rozmiaru list fraz?
Aby uzyskać maksymalną długość [listy fraz](./luis-concept-feature.md), zobacz informacje o [granicach](luis-limits.md) .

### <a name="what-are-the-limits-on-example-utterances"></a>Jakie są limity przykładu wyrażenia długości?
Zobacz informacje o [granicach](luis-limits.md) .

## <a name="testing-and-training"></a>Testowanie i uczenie

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>Widzę kilka błędów w okienku testowanie wsadowe dla niektórych modeli w mojej aplikacji. Jak mogę rozwiązać ten problem?

Błędy wskazują, że występuje niezgodność między etykietami i przewidywaniami z modeli. Aby rozwiązać ten problem, wykonaj jedno lub oba z następujących zadań:
* Aby pomóc LUIS poprawić dyskryminację przed intencjami, Dodaj więcej etykiet.
* Aby ułatwić LUIS szybsze uczenie się, Dodaj funkcje listy fraz, które wprowadzają słownictwo dla domeny.

Zobacz Samouczek dotyczący [testowania wsadowego](./luis-how-to-batch-test.md) .

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>Gdy aplikacja zostanie wyeksportowana i ponownie zaimportowana do nowej aplikacji (przy użyciu nowego identyfikatora aplikacji), wyniki prognozowania LUIS są różne. Dlaczego tak się dzieje?

Zobacz [różnice prognoz między kopiami tej samej aplikacji](luis-concept-prediction-score.md#review-intents-with-similar-scores).

### <a name="some-utterances-go-to-the-wrong-intent-after-i-made-changes-to-my-app-the-issue-seems-to-disappear-at-random-how-do-i-fix-it"></a>Niektóre wyrażenia długości przechodzą do niewłaściwego celu po wprowadzeniu zmian w aplikacji. Problem wydaje się zniknąć losowo. Jak go naprawić?

Zobacz [uczenie ze wszystkimi danymi](luis-how-to-train.md#train-with-all-data).

## <a name="app-publishing"></a>Publikowanie aplikacji

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Jaki jest identyfikator dzierżawy w oknie "Dodawanie klucza do aplikacji"?
Na platformie Azure dzierżawca reprezentuje klienta lub organizację, która jest skojarzona z usługą. Znajdź swój identyfikator dzierżawy w Azure Portal w polu **Identyfikator katalogu** , wybierając pozycję **Azure Active Directory**  >  **Zarządzanie**  >  **właściwościami**.

![Identyfikator dzierżawy w Azure Portal](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
<a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>


### <a name="why-are-there-more-endpoint-keys-assigned-to-my-app-than-i-assigned"></a>Dlaczego do mojej aplikacji są przypisane więcej kluczy punktu końcowego niż przypisano?
Każda aplikacja LUIS ma klucz tworzenia/Start na liście punktów końcowych jako wygodę. Ten klucz umożliwia tylko kilka trafień punktu końcowego, co pozwala wypróbować LUIS.

Jeśli aplikacja istniała, zanim LUIS była ogólnie dostępna (GA), klucze punktu końcowego LUIS w subskrypcji są przypisywane automatycznie. Zostało to zrobione w celu ułatwienia migracji. Wszystkie nowe klucze punktów końcowych LUIS w Azure Portal _nie_ są automatycznie przypisywane do Luis.

## <a name="key-management"></a>Zarządzanie kluczami

### <a name="how-do-i-know-what-key-i-need-where-i-get-it-and-what-i-do-with-it"></a>Jak mogę dowiedzieć się, jakiego klucza potrzebuję, gdzie go mam, i co mam z nim korzystać?

Aby dowiedzieć się więcej o różnicach między kluczem tworzenia i kluczem środowiska uruchomieniowego przewidywania, zobacz [Tworzenie i wykonywanie zapytań dotyczących kluczy punktu końcowego prognoz w programie Luis](luis-how-to-azure-subscription.md) .

### <a name="i-got-an-error-about-being-out-of-quota-how-do-i-fix-it"></a>Wystąpił błąd dotyczący braku limitu przydziału. Jak go naprawić?

Zobacz, Popraw kod stanu HTTP [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) i [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) , aby dowiedzieć się więcej.

### <a name="i-need-to-handle-more-endpoint-queries-how-do-i-do-that"></a>Muszę obsługiwać więcej zapytań punktów końcowych. Jak mogę to zrobić?

Zobacz, Popraw kod stanu HTTP [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) i [429](#i-received-an-http-429-error-status-code-how-do-i-fix-it) , aby dowiedzieć się więcej.

### <a name="i-created-an-authoring-key-but-it-isnt-showing-in-the-luis-portal-what-happened"></a>Klucz tworzenia został utworzony, ale nie jest wyświetlany w portalu LUIS. Co się stało?

Klucze tworzenia są dostępne w portalu LUIS po przeprowadzeniu [migracji do środowiska tworzenia klucza](luis-migration-authoring.md).

## <a name="app-management"></a>Zarządzanie aplikacjami

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Jak mogę pobrać dziennika wyrażenia długości użytkownika?
Domyślnie aplikacja LUIS rejestruje wyrażenia długości użytkowników. Aby pobrać dziennik wyrażenia długości, który użytkownicy wysyłają do aplikacji LUIS, przejdź do obszaru **Moje aplikacje** i wybierz aplikację. Na kontekstowym pasku narzędzi wybierz pozycję **Eksportuj dzienniki punktów końcowych**. Dziennik jest sformatowany jako plik z wartościami rozdzielanymi przecinkami (CSV).

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Jak mogę wyłączyć rejestrowanie wyrażenia długości?
Rejestrowanie użytkownika wyrażenia długości można wyłączyć przez ustawienie `log=false` w adresie URL punktu końcowego używanym przez aplikację kliencką do wysyłania zapytań do Luis. Wyłączenie rejestrowania uniemożliwia jednak aplikacji LUIS zasugerowanie wyrażenia długości lub zwiększenie wydajności, która jest oparta na [aktywnej uczeniu](luis-concept-review-endpoint-utterances.md#what-is-active-learning). W przypadku ustawienia `log=false` z powodu problemów z ochroną prywatności nie można pobrać rekordu tego użytkownika wyrażenia długości z Luis lub użyć tych wyrażenia długości do usprawnienia aplikacji.

Rejestrowanie jest jedynym magazynem wyrażenia długości.

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Dlaczego nie chcę, aby wszystkie moje punkty końcowe wyrażenia długości się?
Jeśli używasz dziennika do analizy predykcyjnej, nie Przechwytuj wyrażenia długości testowych w dzienniku.

## <a name="data-management"></a>Zarządzanie danymi

### <a name="can-i-delete-data-from-luis"></a>Czy mogę usunąć dane z LUIS?

* Można zawsze usunąć przykład wyrażenia długości używany do uczenia LUIS. Jeśli usuniesz przykład wypowiedź z aplikacji LUIS, zostanie on usunięty z usługi sieci Web LUIS i jest niedostępny do eksportowania.
* Wyrażenia długości można usunąć z listy wyrażenia długości użytkownika, którą LUIS sugeruje na stronie **Recenzja punktu końcowego wyrażenia długości** . Usunięcie wyrażenia długości z tej listy uniemożliwi ich sugerowanie, ale nie usunie ich z dzienników.
* Po usunięciu konta zostaną usunięte wszystkie aplikacje wraz z przykładami wyrażenia długości i dzienników. Dane są przechowywane na serwerach przez 60 dni przed ich trwałe usunięciem.

### <a name="how-does-microsoft-manage-data-i-send-to-luis"></a>Jak firma Microsoft zarządza danymi wysyłanymi do LUIS?

[Centrum zaufania](https://www.microsoft.com/trustcenter) objaśnia nasze zobowiązania i opcje zarządzania danymi i uzyskiwania dostępu do usług platformy Azure.

## <a name="language-and-translation-support"></a>Obsługa języka i tłumaczenia

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Mam aplikację w jednym języku i chcesz utworzyć aplikację równoległą w innym języku. Co to jest najprostszy sposób, aby to zrobić?
1. Wyeksportuj aplikację.
2. Przetłumacz etykietę wyrażenia długości w pliku JSON wyeksportowanej aplikacji na język docelowy.
3. Może być konieczna zmiana nazw intencji i jednostek lub pozostawienie ich w dowolnej lokalizacji.
4. Na koniec zaimportuj aplikację do aplikacji LUIS w języku docelowym.

## <a name="app-notification"></a>Powiadomienie aplikacji

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Dlaczego otrzymuję wiadomość e-mail z informacją, że prawie nie ma przydziału?
Twój klucz do tworzenia/uruchamiania jest dozwolony tylko na 1000 zapytań o punkty końcowe miesięcznie. Utwórz klucz punktu końcowego LUIS (bezpłatny lub płatny) i Użyj tego klucza podczas wykonywania zapytań dotyczących punktów końcowych. Jeśli tworzysz zapytania punktu końcowego z bot lub innej aplikacji klienckiej, musisz zmienić klucz punktu końcowego LUIS.

## <a name="bots"></a>Boty

### <a name="my-luis-bot-isnt-working-what-do-i-do"></a>LUIS bot nie działa. Co mam zrobić?

Pierwszym problemem jest wyizolowanie, jeśli problem związany z LUISem lub zachodzi poza oprogramowanie pośredniczące LUIS.

#### <a name="resolve-issue-in-luis"></a>Rozwiązywanie problemu w programie LUIS
Przekaż ten sam wypowiedź do LUIS z [punktu końcowego Luis](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint). Jeśli zostanie wyświetlony komunikat o błędzie, należy rozwiązać problem w LUIS, dopóki błąd nie zostanie już zwrócony. Typowe błędy obejmują:

* `Out of call volume quota. Quota will be replenished in <time>.` — Ten problem oznacza konieczność zmiany z klucza tworzenia do [klucza punktu końcowego](luis-how-to-azure-subscription.md) lub należy zmienić [warstwy usług](luis-how-to-azure-subscription.md#change-the-pricing-tier).

#### <a name="resolve-issue-in-azure-bot-service"></a>Rozwiąż problem w Azure Bot Service

Jeśli używasz Azure Bot Service, a problem polega na tym, że **test w usłudze Web Chat** wraca `Sorry, my bot code is having an issue` , Sprawdź dzienniki:

1. W Azure Portal, w bot, w sekcji **Zarządzanie bot** wybierz opcję **Kompiluj**.
1. Otwórz Edytor kodu online.
1. Na górnym, niebieskim pasku nawigacyjnym wybierz nazwę bot (drugi element w prawo).
1. Z listy rozwijanej wyniki wybierz pozycję **Otwórz konsolę kudu**.
1. Wybierz pozycję **LogFiles**, a następnie wybierz pozycję **aplikacja**. Przejrzyj wszystkie pliki dziennika. Jeśli błąd nie jest widoczny w folderze aplikacji, przejrzyj wszystkie pliki dziennika w **pliku dziennika**.
1. Pamiętaj, aby ponownie skompilować projekt, jeśli używasz skompilowanego języka, takiego jak C#.

> [!Tip]
> Konsola programu może również instalować pakiety.

#### <a name="resolve-issue-while-debugging-on-local-machine-with-bot-framework"></a>Rozwiąż problem podczas debugowania na maszynie lokalnej przy użyciu platformy bot Framework.

Aby dowiedzieć się więcej na temat debugowania lokalnego bot, zobacz [debugowanie a bot](/azure/bot-service/bot-service-debug-bot).

## <a name="integrating-luis"></a>Integrowanie LUIS

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Gdzie jest moja aplikacja LUIS utworzona podczas procesu subskrypcji bot aplikacji sieci Web platformy Azure?
Jeśli wybierzesz szablon LUIS, a następnie wybierzesz przycisk **Wybierz** w okienku szablonu, okienko po lewej stronie zostanie zmienione w celu uwzględnienia typu szablonu i zostanie wyświetlony monit o określenie regionu, w którym ma zostać utworzony szablon Luis. Proces bot aplikacji sieci Web nie tworzy subskrypcji LUIS, chociaż.

![LUIS szablon aplikacji sieci Web Bot region](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Jakie regiony LUIS obsługują bot Framework napełnianiu Speech?
Funkcja [Speech napełnianiu](/bot-framework/bot-service-manage-speech-priming) jest obsługiwana tylko w przypadku aplikacji Luis w wystąpieniu centralnym (US).

## <a name="api-programming-strategies"></a>Strategie programowania interfejsu API

### <a name="how-do-i-programmatically-get-the-luis-region-of-a-resource"></a>Jak mogę programowo pobrać region LUIS zasobu?

Użyj przykładu LUIS, aby [znaleźć region](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/find-region) programowo przy użyciu języka C# lub Node.Js.

## <a name="luis-service"></a>Usługa LUIS

### <a name="is-language-understanding-luis-available-on-premises-or-in-private-cloud"></a>Czy Language Understanding (LUIS) jest dostępna lokalnie lub w chmurze prywatnej?

Tak, można użyć [kontenera](luis-container-howto.md) Luis dla tych scenariuszy, jeśli masz wymagane połączenie z użyciem liczników.

## <a name="migrating-to-the-next-version"></a>Migrowanie do następnej wersji

### <a name="how-do-i-migrate-to-preview-v3-api"></a>Jak mogę przeprowadzić migrację do wersji zapoznawczej interfejsu API v3?

Zobacz [Przewodnik migracji interfejsu API v2 do wersji v3 dla aplikacji Luis](luis-migration-api-v3.md)

## <a name="build-2019-conference-announcements"></a>Powiadomienia konferencyjne 2019

Następujące funkcje zostały wydane na konferencji Build 2019:

* [Przewodnik migracji interfejsu API v3](luis-migration-api-v3.md)
* [Udoskonalony pulpit nawigacyjny analizy](luis-how-to-use-dashboard.md)
* [Ulepszone prebudowane domeny](luis-reference-prebuilt-domains.md)
* [Jednostki listy dynamicznej](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Jednostki zewnętrzne](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

Filmy wideo:

* [Jak skalować swoją firmę do nowej generacji przy użyciu funkcji Azure Conversation AI](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat LUIS, zobacz następujące zasoby:
* [Pytania Stack Overflow oznaczone za pomocą LUIS](https://stackoverflow.com/questions/tagged/luis)
* [Microsoft Q&stronę pytania dla usług MSDN Language Understanding Intelligent Services (LUIS)](/answers/topics/azure-language-understanding.html)