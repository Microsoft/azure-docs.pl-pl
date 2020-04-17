---
title: Często zadawane pytania (FAQ) — USŁUGA LUIS
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące rozumienia języka (LUIS).
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: diberry
ms.openlocfilehash: 59d9b1f21e1936b7d03293ec3d338677380a7c99
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81530270"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Często zadawane pytania dotyczące rozumienia języka

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące rozumienia języka (LUIS).

## <a name="whats-new"></a>Co nowego

[Dowiedz się więcej](whats-new.md) o nowościach w zrozumieni język (LUIS).

<a name="luis-authoring"></a>

## <a name="authoring"></a>Tworzenie

### <a name="what-are-the-luis-best-practices"></a>Jakie są najlepsze rozwiązania usługi LUIS?
Zacznij od [cyklu tworzenia,](luis-concept-app-iteration.md)a następnie przeczytaj [najlepsze wskazówki](luis-concept-best-practices.md).

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>Jaki jest najlepszy sposób rozpoczęcia tworzenia aplikacji w usłudze LUIS?

Najlepszym sposobem tworzenia aplikacji jest [proces przyrostowy.](luis-concept-app-iteration.md)

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>Co to jest dobra praktyka do modelowania intencji mojej aplikacji? Czy należy utworzyć bardziej szczegółowe lub bardziej ogólne intencje?

Wybierz intencje, które nie są tak ogólne, aby nakładać się na siebie, ale nie tak specyficzne, że utrudnia usługi LUIS do odróżnienia podobnych intencji. Tworzenie dyskryminujących określonych intencji jest jedną z najlepszych rozwiązań dla modelowania usługi LUIS.

### <a name="is-it-important-to-train-the-none-intent"></a>Czy ważne jest, aby szkolić intencji Brak?

Tak, dobrze jest trenować **brak** intencji z więcej wypowiedzi, jak dodać więcej etykiet do innych intencji. Dobry stosunek to 1 lub 2 etykiety dodane do **Brak** dla każdego 10 etykiet dodanych do intencji. Współczynnik ten zwiększa dyskryminującą moc usługi LUIS.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Jak mogę poprawić błędy pisowni w wypowiedziach?

Zobacz samouczek [interfejsu API sprawdzania pisowni Bing V7.](luis-tutorial-bing-spellcheck.md) Usługa LUIS wymusza limity nałożone przez interfejs API sprawdzania pisowni Bing V7.

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Jak edytować aplikację usługi LUIS programowo?
Aby programowo edytować aplikację usługi LUIS, użyj [interfejsu API tworzenia .](https://go.microsoft.com/fwlink/?linkid=2092087) Zobacz [Wywołanie interfejsu API tworzenia usługi LUIS](./get-started-get-model-rest-apis.md) i [programowego tworzenia aplikacji usługi LUIS przy użyciu pliku Node.js,](./luis-tutorial-node-import-utterances-csv.md) aby zapoznać się z przykładami wywoływania interfejsu API tworzenia. Interfejs API tworzenia wymaga użycia [klucza tworzenia,](luis-concept-keys.md#azure-resources-for-luis) a nie klucza końcowego. Programowe tworzenie umożliwia maksymalnie 1 000 000 połączeń miesięcznie i pięć transakcji na sekundę. Aby uzyskać więcej informacji na temat kluczy używanych w usłudze LUIS, zobacz [Zarządzanie kluczami](./luis-concept-keys.md).

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Gdzie jest funkcja Wzorzec, która zapewniała dopasowanie wyrażeń regularnych?
Poprzednia **operacja Szyku** jest obecnie przestarzała, zastąpiona przez **[Wzorce](luis-concept-patterns.md)**.

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Jak użyć jednostki do wyciągnięcia poprawnych danych?
Zobacz [jednostki](luis-concept-entity-types.md) i [wyodrębnianie danych](luis-concept-data-extraction.md).

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Czy odmiany wypowiedź przykład obejmują znaki interpunkcyjne?
Użyj jednego z następujących rozwiązań:
* Ignoruj [znaki interpunkcyjne](luis-reference-application-settings.md#punctuation-normalization)
* Dodaj różne odmiany jako przykładowe wypowiedzi do intencji
* Dodaj wzorzec wypowiedź przykład ze [składnią, aby zignorować](luis-concept-patterns.md#pattern-syntax) znaki interpunkcyjne.

### <a name="does-luis-currently-support-cortana"></a>Czy usługa LUIS obsługuje obecnie Cortanę?

Wstępnie utworzone aplikacje Cortany zostały przestarzałe w 2017 roku. Nie są już obsługiwane.

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Jak przenieść własność aplikacji usługi LUIS?
Aby przenieść aplikację usługi LUIS do innej subskrypcji platformy Azure, wyeksportuj aplikację usługi LUIS i zaimportuj ją przy użyciu nowego konta. Zaktualizuj identyfikator aplikacji usługi LUIS w aplikacji klienckiej, która ją wywołuje. Nowa aplikacja może zwracać nieco inne wyniki usługi LUIS z oryginalnej aplikacji.

### <a name="a-prebuilt-entity-is-tagged-in-an-example-utterance-instead-of-my-custom-entity-how-do-i-fix-this"></a>Wstępnie skompilowana jednostka jest oznaczana w wypowiedź przykład zamiast mojej jednostki niestandardowej. Jak rozwiązać ten problem?

W portalu usługi LUIS można oznaczyć tekst etykietą dla jednostki, która jest zainteresowana wyodrębnieniem. Jeśli portal usługi LUIS nie wyświetla przewidywania poprawne jednostki, może być konieczne dodanie więcej wypowiedzi i etykiety jednostki w tekście lub dodać deskryptora (na przykład funkcji).

### <a name="i-tried-to-import-an-app-or-version-file-but-i-got-an-error-what-happened"></a>Próbowałem zaimportować plik aplikacji lub wersji, ale wystąpił błąd, co się stało?

Dowiedz się więcej o [błędach importu wersji](luis-how-to-manage-versions.md#import-errors).

<a name="luis-collaborating"></a>

## <a name="collaborating-and-contributing"></a>Współpraca i współtworzenie

### <a name="how-do-i-give-collaborators-access-to-luis-with-azure-active-directory-azure-ad-or-role-based-access-control-rbac"></a>Jak przyznać współpracownikom dostęp do usługi LUIS za pomocą usługi Azure Active Directory (Azure AD) lub kontroli dostępu opartej na rolach (RBAC)?

Zobacz [zasoby usługi Azure Active Directory](luis-how-to-collaborate.md#azure-active-directory-resources) i użytkownik [dzierżawy usługi Azure Active Directory,](luis-how-to-collaborate.md#azure-active-directory-tenant-user) aby dowiedzieć się, jak przyznać współpracownikom dostęp.

<a name="luis-endpoint"></a>

## <a name="endpoint"></a>Endpoint

### <a name="i-received-an-http-403-error-status-code-how-do-i-fix-it"></a>Otrzymałem kod stanu błędu HTTP 403. Jak go naprawić?

Otrzymujesz kody stanu błędów 403 i 429, gdy przekraczasz transakcje na sekundę lub transakcje miesięcznie dla warstwy cenowej. Zwiększ warstwę cenową lub użyj [kontenerów Opisu języka.](luis-container-howto.md)

Jeśli używasz wszystkich tych bezpłatnych zapytań o punkt końcowy 1000 lub przekraczasz miesięczny przydział transakcji w warstwie cenowej, otrzymasz kod stanu błędu HTTP 403.

Aby naprawić ten błąd, musisz [zmienić warstwę cenową](luis-how-to-azure-subscription.md#change-pricing-tier) na wyższą warstwę lub [utworzyć nowy zasób](get-started-portal-deploy-app.md#create-the-endpoint-resource) i przypisać go do [aplikacji](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal).

Rozwiązania tego błędu obejmują:

* W [witrynie Azure portal](https://portal.azure.com)— w zasobie opisu języka — w **warstwie cennik zarządzanie zasobami — >**— zmień warstwę cenową na wyższą warstwę TPS. Nie musisz nic robić w portalu Zrozumienie języka, jeśli zasób jest już przypisany do aplikacji do rozumienia języka.
*  Jeśli użycie przekracza najwyższą warstwę cenową, dodaj więcej zasobów opisu języka z modułem równoważenia obciążenia przed nimi. [Język zrozumienia kontenera](luis-container-howto.md) z Kubernetes lub Docker Compose może pomóc w tym.

### <a name="i-received-an-http-429-error-status-code-how-do-i-fix-it"></a>Otrzymałem kod stanu błędu HTTP 429. Jak go naprawić?

Otrzymujesz kody stanu błędów 403 i 429, gdy przekraczasz transakcje na sekundę lub transakcje miesięcznie dla warstwy cenowej. Zwiększ warstwę cenową lub użyj [kontenerów Opisu języka.](luis-container-howto.md)

Ten kod stanu jest zwracany, gdy transakcje na sekundę przekraczają warstwę cenową.

Rozwiązania obejmują:

* Możesz [zwiększyć warstwę cenową](luis-how-to-azure-subscription.md#change-pricing-tier), jeśli nie jesteś na najwyższym poziomie.
* Jeśli użycie przekracza najwyższą warstwę cenową, dodaj więcej zasobów opisu języka z modułem równoważenia obciążenia przed nimi. [Język zrozumienia kontenera](luis-container-howto.md) z Kubernetes lub Docker Compose może pomóc w tym.
* Można bramy żądań aplikacji klienckiej z [zasad ponawiania próby](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines) można zaimplementować samodzielnie po otrzymaniu tego kodu stanu.

### <a name="my-endpoint-query-returned-unexpected-results-what-should-i-do"></a>Moja kwerenda punktu końcowego zwróciła nieoczekiwane wyniki. Co mam zrobić?

Nieoczekiwane wyniki przewidywania kwerend są oparte na stanie opublikowanego modelu. Aby poprawić model, może być konieczna zmiana modelu, szkolenie i ponowne opublikowanie.

Poprawianie modelu rozpoczyna się od [aktywnego uczenia się](luis-how-to-review-endpoint-utterances.md).

Szkolenie niedeterministyczne można usunąć, aktualizując [interfejs API ustawień wersji aplikacji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) w celu użycia wszystkich danych szkoleniowych.

Zapoznaj się z [najlepszymi wskazówkami.](luis-concept-best-practices.md)

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Dlaczego usługa LUIS dodaje spacje do zapytania wokół lub w środku słów?
Usługa LUIS [tokenizuje](luis-glossary.md#token) wypowiedź na podstawie [kultury](luis-language-support.md#tokenization). Zarówno oryginalna wartość, jak i wartość tokenizowana są dostępne do [wyodrębniania danych.](luis-concept-data-extraction.md#tokenized-entity-returned)

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Jak utworzyć i przypisać klucz punktu końcowego usługi LUIS?
[Utwórz klucz punktu końcowego](luis-how-to-azure-subscription.md) na platformie Azure dla poziomu [usług.](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) [Przypisz klucz](luis-how-to-azure-subscription.md) na stronie **[Zasoby platformy Azure.](luis-how-to-azure-subscription.md)** Nie ma odpowiedniego interfejsu API dla tej akcji. Następnie należy zmienić żądanie HTTP na punkt końcowy, aby [użyć nowego klucza punktu końcowego](luis-concept-keys.md).

### <a name="how-do-i-interpret-luis-scores"></a>Jak interpretować wyniki usługi LUIS?
System powinien używać najwyższej intencji punktacji, niezależnie od jego wartości. Na przykład wynik poniżej 0,5 (mniej niż 50%) nie musi oznaczać, że usługa LUIS ma niskie zaufanie. Podanie większej ilości danych szkoleniowych może pomóc zwiększyć [wynik](luis-concept-prediction-score.md) najbardziej prawdopodobne intencji.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Dlaczego nie widzę trafień punktu końcowego na pulpicie nawigacyjnym aplikacji?
Całkowita liczba trafień punktu końcowego w pulpicie nawigacyjnym aplikacji są okresowo aktualizowane, ale metryki skojarzone z kluczem punktu końcowego usługi LUIS w witrynie Azure portal są aktualizowane częściej.

Jeśli nie widzisz zaktualizowanych trafień punktu końcowego na pulpicie nawigacyjnym, zaloguj się do witryny Azure portal i znajdź zasób skojarzony z kluczem punktu końcowego usługi LUIS i otwórz **metryki,** aby wybrać metrykę **Łączna liczba połączeń.** Jeśli klucz punktu końcowego jest używany dla więcej niż jednej aplikacji usługi LUIS, metryka w witrynie Azure portal pokazuje łączną liczbę wywołań ze wszystkich aplikacji usługi LUIS, które go używają.

### <a name="is-there-a-powershell-command-get-to-the-endpoint-quota"></a>Czy istnieje polecenie PowerShell dostać się do przydziału punktu końcowego?

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aby wyświetlić przydział punktu końcowego, można użyć polecenia programu PowerShell:

```powershell
Get-AzCognitiveServicesAccountUsage -ResourceGroupName <your-resource-group> -Name <your-resource-name>
```

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>Moja aplikacja usługi LUIS działała wczoraj, ale dziś opadnę 403 błędy. Nie zmieniłem aplikacji. Jak go naprawić?
Postępuj zgodnie z tymi [instrukcjami,](#how-do-i-create-and-assign-a-luis-endpoint-key) aby utworzyć klucz punktu końcowego usługi LUIS i przypisać go do aplikacji. Następnie należy zmienić żądanie HTTP aplikacji klienckiej na punkt końcowy, aby [użyć nowego klucza punktu końcowego](luis-concept-keys.md). Jeśli utworzono nowy zasób w innym regionie, zmień również region żądania klienta HTTP.

### <a name="how-do-i-secure-my-luis-endpoint"></a>Jak zabezpieczyć punkt końcowy usługi LUIS?
Zobacz [Zabezpieczanie punktu końcowego](luis-concept-keys.md#securing-the-endpoint).

## <a name="working-within-luis-limits"></a>Praca w granicach usługi LUIS

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>Jaka jest maksymalna liczba intencji i jednostek, które aplikacja usługi LUIS może obsługiwać?
Zobacz odwołanie do [granic.](luis-boundaries.md)

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Chcę utworzyć aplikację usługi LUIS z więcej niż maksymalną liczbą intencji. Co mam zrobić?

Zobacz [Najważniejsze wskazówki dotyczące intencji](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents).

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>Chcę utworzyć aplikację w usłudze LUIS z więcej niż maksymalną liczbą jednostek. Co mam zrobić?

Zobacz [Najważniejsze wskazówki dotyczące encji](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities)

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Jakie są limity liczby i rozmiaru list fraz?
Maksymalna długość [listy fraz](./luis-concept-feature.md)można znaleźć w odwołaniu do [granic.](luis-boundaries.md)

### <a name="what-are-the-limits-on-example-utterances"></a>Jakie są limity wypowiedzi przykład?
Zobacz odwołanie do [granic.](luis-boundaries.md)

## <a name="testing-and-training"></a>Testowanie i szkolenie

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>Widzę kilka błędów w okienku testowania wsadowego dla niektórych modeli w mojej aplikacji. Jak rozwiązać ten problem?

Błędy wskazują, że istnieje pewna rozbieżność między etykietami a prognozami z modeli. Aby rozwiązać ten problem, wykonaj jedno lub oba z następujących zadań:
* Aby pomóc usługi LUIS poprawić dyskryminację wśród intencji, dodaj więcej etykiet.
* Aby ułatwić szybsze poznaanie usługi LUIS, dodaj funkcje listy fraz, które wprowadzają słownictwo specyficzne dla domeny.

Zobacz [samouczek testowania wsadowego.](luis-tutorial-batch-testing.md)

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>Gdy aplikacja jest eksportowana, a następnie ponownieimportowana do nowej aplikacji (z nowym identyfikatorem aplikacji), wyniki prognozowania usługi LUIS są różne. Dlaczego tak się dzieje?

Zobacz [Przewidywanie różnic między kopiami tej samej aplikacji](luis-concept-prediction-score.md#review-intents-with-similar-scores).

### <a name="some-utterances-go-to-the-wrong-intent-after-i-made-changes-to-my-app-the-issue-seems-to-disappear-at-random-how-do-i-fix-it"></a>Niektóre wypowiedzi przejść do niewłaściwego zamiaru po wykonaniu zmian w mojej aplikacji. Problem wydaje się zniknąć losowo. Jak go naprawić?

Zobacz [Pociąg ze wszystkimi danymi](luis-how-to-train.md#train-with-all-data).

## <a name="app-publishing"></a>Publikowanie aplikacji

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Jaki jest identyfikator dzierżawy w oknie "Dodaj klucz do aplikacji"?
Na platformie Azure dzierżawca reprezentuje klienta lub organizację, która jest skojarzona z usługą. Znajdź swój identyfikator dzierżawy w witrynie Azure portal w polu **Identyfikator katalogu,** wybierając pozycję **Azure Active Directory** > **Manage** > **Properties**.

![Identyfikator dzierżawy w witrynie Azure portal](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
<a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>


### <a name="why-are-there-more-endpoint-keys-assigned-to-my-app-than-i-assigned"></a>Dlaczego do mojej aplikacji jest przypisanych więcej kluczy punktów końcowych niż przypisanych?
Każda aplikacja usługi LUIS ma klucz tworzenia/rozrusznika na liście punktów końcowych jako wygodę. Ten klucz umożliwia tylko kilka trafień punktu końcowego, dzięki czemu można wypróbować usługi LUIS.

Jeśli aplikacja istniała przed ogólnie dostępna usługa LUIS (GA), klucze punktu końcowego usługi LUIS w subskrypcji są przypisywane automatycznie. Zostało to zrobione, aby ułatwić migrację ga. Wszystkie nowe klucze punktu końcowego usługi LUIS w witrynie Azure portal _nie_ są automatycznie przypisywane do usługi LUIS.

## <a name="key-management"></a>Zarządzanie kluczami

### <a name="how-do-i-know-what-key-i-need-where-i-get-it-and-what-i-do-with-it"></a>Skąd mam wiedzieć, jaki klucz potrzebuję, gdzie go dostanę i co z nim robię?

Zobacz [Tworzenie i przewidywanie zapytań kluczy końcowych punktu w usłudze LUIS,](luis-concept-keys.md) aby dowiedzieć się więcej o różnicach między kluczem tworzenia i klucza środowiska wykonawczego przewidywanie.

### <a name="i-got-an-error-about-being-out-of-quota-how-do-i-fix-it"></a>Dostałem błąd o braku limitu. Jak go naprawić?

Aby dowiedzieć się więcej, zobacz Napraw kod stanu HTTP [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) i [429.](#i-received-an-http-429-error-status-code-how-do-i-fix-it)

### <a name="i-need-to-handle-more-endpoint-queries-how-do-i-do-that"></a>Muszę obsługiwać więcej zapytań punktu końcowego. Jak mogę to zrobić?

Aby dowiedzieć się więcej, zobacz Napraw kod stanu HTTP [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) i [429.](#i-received-an-http-429-error-status-code-how-do-i-fix-it)

### <a name="i-created-an-authoring-key-but-it-isnt-showing-in-the-luis-portal-what-happened"></a>Utworzono klucz autora, ale nie jest wyświetlany w portalu usługi LUIS. Co się stało?

Klucze tworzenia są dostępne w portalu usługi LUIS po [migracji do środowiska klucza tworzenia](luis-migration-authoring.md).

## <a name="app-management"></a>Zarządzanie aplikacjami

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Jak pobrać dziennik wypowiedzi użytkowników?
Domyślnie aplikacja usługi LUIS rejestruje wypowiedzi od użytkowników. Aby pobrać dziennik wypowiedzi wysyłanych przez użytkowników do aplikacji usługi LUIS, przejdź do **witryny Moje aplikacje**i wybierz aplikację. Na pasku narzędzi kontekstowym wybierz pozycję **Eksportuj dzienniki punktów końcowych**. Dziennik jest sformatowany jako plik csv (csv).

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Jak mogę wyłączyć rejestrowanie wypowiedzi?
Rejestrowanie wypowiedzi użytkowników można wyłączyć, ustawiając `log=false` w adresie URL punktu końcowego używany do wykonywania zapytań usługi LUIS. Jednak wyłączenie rejestrowania wyłącza możliwość sugerowania wypowiedzi przez aplikację LUIS lub zwiększania wydajności opartej na [aktywnym uczeniu się.](luis-concept-review-endpoint-utterances.md#what-is-active-learning) Jeśli ustawisz `log=false` ze względu na obawy dotyczące prywatności danych, nie można pobrać rekord tych wypowiedzi użytkownika z usługi LUIS lub użyć tych wypowiedzi, aby poprawić aplikację.

Rejestrowanie jest jedynym magazynem wypowiedzi.

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Dlaczego nie chcę rejestrowane wszystkie moje wypowiedzi punktu końcowego?
Jeśli używasz dziennika do analizy przewidywania, nie przechwytuj wypowiedzi testowe w dzienniku.

## <a name="data-management"></a>Zarządzanie danymi

### <a name="can-i-delete-data-from-luis"></a>Czy mogę usunąć dane z usługi LUIS?

* Zawsze można usunąć wypowiedzi przykład używany do szkolenia usługi LUIS. Jeśli usuniesz wypowiedź przykład z aplikacji usługi LUIS, zostanie on usunięty z usługi sieci web usługi LUIS i jest niedostępny do eksportu.
* Wypowiedzi można usunąć z listy wypowiedzi użytkownika, które usługa LUIS sugeruje na stronie **Przejrzyj wypowiedzi punktów końcowych.** Usuwanie wypowiedzi z tej listy uniemożliwia im sugerowanie, ale nie usuwa ich z dzienników.
* Jeśli usuniesz konto, wszystkie aplikacje zostaną usunięte wraz z ich wypowiedzi przykład i dzienniki. Dane są przechowywane na serwerach przez 60 dni, zanim zostaną trwale usunięte.

### <a name="how-does-microsoft-manage-data-i-send-to-luis"></a>W jaki sposób firma Microsoft zarządza danymi wysyłane do usługi LUIS?

[Centrum zaufania](https://www.microsoft.com/trustcenter) wyjaśnia nasze zobowiązania i opcje zarządzania danymi i dostępu do nich w usługach platformy Azure.

## <a name="language-and-translation-support"></a>Obsługa języka i tłumaczenia

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Mam aplikację w jednym języku i chcę utworzyć aplikację równoległą w innym języku. Jaki jest najprostszy sposób, aby to zrobić?
1. Wyeksportuj aplikację.
2. Przetłumacz wypowiedzi z etykietą w pliku JSON eksportowanego aplikacji na język docelowy.
3. Może być konieczna zmiana nazw intencji i encji lub pozostawienie ich w takiej stanie, w jakim są.
4. Na koniec zaimportuj aplikację, aby mieć aplikację usługi LUIS w języku docelowym.

## <a name="app-notification"></a>Powiadomienie o aplikacji

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Dlaczego dostałem wiadomość e-mail z informacją, że jestem prawie poza kwotą?
Klucz tworzenia/startu jest dozwolony tylko 1000 zapytań o punkt końcowy miesięcznie. Utwórz klucz punktu końcowego usługi LUIS (bezpłatny lub płatny) i użyj tego klucza podczas wykonywania zapytań o punkt końcowy. Jeśli wysyłasz zapytania o punkt końcowy z bota lub innej aplikacji klienckiej, musisz zmienić klucz punktu końcowego usługi LUIS.

## <a name="bots"></a>Boty

### <a name="my-luis-bot-isnt-working-what-do-i-do"></a>Mój bot usługi LUIS nie działa. Co mam zrobić?

Pierwszym problemem jest wyizolowanie, jeśli problem jest związany z usługi LUIS lub występuje poza oprogramowaniem pośredniczącym usługi LUIS.

#### <a name="resolve-issue-in-luis"></a>Rozwiązywanie problemu w usłudze LUIS
Przekaż tę samą wypowiedź do usługi LUIS z [punktu końcowego usługi LUIS](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint). Jeśli zostanie wyświetlony błąd, rozwiąż problem w usłudze LUIS, dopóki błąd nie zostanie zwrócony. Typowe błędy obejmują:

* `Out of call volume quota. Quota will be replenished in <time>.`- Ten problem wskazuje, że trzeba albo zmienić z klucza tworzenia do [klucza punktu końcowego](luis-how-to-azure-subscription.md) lub trzeba zmienić [warstwy usług](luis-how-to-azure-subscription.md#change-pricing-tier).

#### <a name="resolve-issue-in-azure-bot-service"></a>Rozwiązywanie problemu w usłudze Azure Bot Service

Jeśli używasz usługi Azure Bot Service i problem polega na tym, że **test w czacie w sieci Web** zwraca, `Sorry, my bot code is having an issue`sprawdź dzienniki:

1. W witrynie Azure portal dla bota w sekcji **Zarządzanie botami** wybierz pozycję **Buduj**.
1. Otwórz edytor kodów online.
1. W górnej części niebieskiego paska nawigacyjnego wybierz nazwę bota (drugi element po prawej stronie).
1. Z powstałej listy rozwijanej wybierz pozycję **Otwórz konsolę Kudu**.
1. Wybierz **pozycję LogFiles**, a następnie wybierz pozycję **Aplikacja**. Przejrzyj wszystkie pliki dziennika. Jeśli nie widzisz błędu w folderze aplikacji, przejrzyj wszystkie pliki dziennika w obszarze **LogFiles**.
1. Pamiętaj, aby odbudować projekt, jeśli używasz skompilowanego języka, takiego jak C#.

> [!Tip]
> Konsola może również instalować pakiety.

#### <a name="resolve-issue-while-debugging-on-local-machine-with-bot-framework"></a>Rozwiąż problem podczas debugowania na komputerze lokalnym za pomocą programu Bot Framework.

Aby dowiedzieć się więcej na temat lokalnego debugowania bota, zobacz [Debugowanie bota](https://docs.microsoft.com/azure/bot-service/bot-service-debug-bot?view=azure-bot-service-4.0).

## <a name="integrating-luis"></a>Integracja usługi LUIS

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Gdzie jest moja aplikacja usługi LUIS tworzone podczas procesu subskrypcji bota aplikacji sieci Web platformy Azure?
Jeśli wybierzesz szablon usługi LUIS i wybierzesz przycisk **Wybierz** w okienku szablonu, okienko po lewej stronie zmieni się w celu uwzględnienia typu szablonu i zapyta w jakim regionie utworzyć szablon usługi LUIS. Proces bota aplikacji sieci web nie tworzy jednak subskrypcji usługi LUIS.

![Region bota szablonu aplikacji sieci LUIS](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Jakie regiony usługi LUIS obsługują tworzenie mowy programu Bot Framework?
[Tworzenie zasysania mowy](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming) jest obsługiwane tylko dla aplikacji usługi LUIS w wystąpieniu centralnym (USA).

## <a name="api-programming-strategies"></a>Strategie programowania API

### <a name="how-do-i-programmatically-get-the-luis-region-of-a-resource"></a>Jak programowo uzyskać region usługi LUIS zasobu?

Użyj próbki usługi LUIS, aby [znaleźć region](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/find-region) programowo przy użyciu języka C# lub Node.Js.

## <a name="luis-service"></a>Usługa LUIS

### <a name="is-language-understanding-luis-available-on-premises-or-in-private-cloud"></a>Czy rozumienie języka (LUIS) jest dostępne lokalnie lub w chmurze prywatnej?

Tak, można użyć [kontenera](luis-container-howto.md) usługi LUIS dla tych scenariuszy, jeśli masz niezbędną łączność z użyciem licznika.

## <a name="migrating-to-the-next-version"></a>Migracja do następnej wersji

### <a name="how-do-i-migrate-to-preview-v3-api"></a>Jak przeprowadzić migrację do interfejsu API w wersji zapoznawczej w wersji 3?

Zobacz [przewodnik po migracji interfejsu API w wersji 2 do wersji 3 dla aplikacji usługi LUIS](luis-migration-api-v3.md)

## <a name="build-2019-conference-announcements"></a>Tworzenie ogłoszeń konferencji 2019

Na konferencji Build 2019 zostały wydane następujące funkcje:

* [Przewodnik po migracji interfejsu API w wersji Zapoznawczej systemu V3](luis-migration-api-v3.md)
* [Ulepszony pulpit nawigacyjny analizy](luis-how-to-use-dashboard.md)
* [Ulepszone wstępnie utworzone domeny](luis-reference-prebuilt-domains.md)
* [Encje listy dynamicznej](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Podmioty zewnętrzne](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

Filmy wideo:

* [Jak używać sztucznej inteligencji konwersacyjnej platformy Azure do skalowania firmy dla następnej generacji](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o usłudze LUIS, zobacz następujące zasoby:
* [Pytania dotyczące przepełnienia stosu oznaczone tagiem usługi LUIS](https://stackoverflow.com/questions/tagged/luis)
* [Forum mrozu języka MSDN (LUIS)](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS)
