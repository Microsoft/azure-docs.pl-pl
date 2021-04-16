---
title: Często zadawane pytania — USŁUGA LUIS
description: Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Language Understanding (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: troubleshooting
ms.date: 04/13/2021
ms.openlocfilehash: 97b7c02a418a87a0700414e19bc939bda899d073
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503826"
---
# <a name="language-understanding-frequently-asked-questions-faq"></a>Usługa Language Understanding — często zadawane pytania

Ten artykuł zawiera odpowiedzi na często zadawane pytania dotyczące usługi Language Understanding (LUIS).

## <a name="whats-new"></a>Co nowego

[Dowiedz się więcej](whats-new.md) o nowościach w Language Understanding (LUIS).

<a name="luis-authoring"></a>

## <a name="authoring"></a>Tworzenie

### <a name="what-are-the-luis-best-practices"></a>Jakie są najlepsze rozwiązania dotyczące usługi LUIS?
Rozpocznij od [cyklu tworzenia,](luis-concept-app-iteration.md)a następnie zapoznaj się z [najlepszymi rozwiązaniami.](luis-concept-best-practices.md)

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>Jaki jest najlepszy sposób, aby rozpocząć tworzenie aplikacji w u usługi LUIS?

Najlepszym sposobem kompilowania aplikacji jest proces [przyrostowy](luis-concept-app-iteration.md).

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>Jakie jest dobre rozwiązanie do modelowania intencji mojej aplikacji? Czy należy utworzyć bardziej szczegółowe lub bardziej ogólne intencje?

Wybierz intencje, które nie są tak ogólne, aby się nakładały, ale nie są tak specyficzne, że utrudnia ujmowanie podobnych intencji przez usługi LUIS. Tworzenie rozłącznych określonych intencji jest jednym z najlepszych rozwiązań modelowania usługi LUIS.

### <a name="is-it-important-to-train-the-none-intent"></a>Czy ważne jest szkolenie intencji None?

Tak, dobrze jest trenować intencję **None** przy użyciu większej liczby wypowiedzi podczas dodawania kolejnych etykiet do innych intencji. Dobry współczynnik to 1 lub 2 etykiety dodane do wartości **Brak** dla każdej 10 etykiet dodanych do intencji. Ten współczynnik zwiększa moc rozróżniacyjną usługi LUIS.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Jak poprawić błędy pisowni w wypowiedziach?

Zobacz [samouczek dotyczący sprawdzanie pisowni Bing API w wersji 7.](luis-tutorial-bing-spellcheck.md) Usługa LUIS wymusza limity narzucone przez sprawdzanie pisowni Bing API w wersji 7.

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Jak mogę edytować aplikację usługi LUIS programowo?
Aby programowo edytować aplikację luis, użyj [interfejsu API tworzenia.](https://go.microsoft.com/fwlink/?linkid=2092087) Zobacz [Call LUIS authoring API](./get-started-get-model-rest-apis.md) (Wywołaj interfejs API tworzenia usługi LUIS) i Build a LUIS app [programmatically using Node.js(Programowe ](./luis-tutorial-node-import-utterances-csv.md) tworzenie aplikacji USŁUGI LUIS przy użyciu interfejsu API tworzenia), aby uzyskać przykłady dotyczące sposobu wywołania interfejsu API tworzenia. Interfejs API tworzenia wymaga użycia klucza [tworzenia,](luis-how-to-azure-subscription.md#azure-resources-for-luis) a nie klucza punktu końcowego. Tworzenie programowe umożliwia maksymalnie 1 000 000 wywołań miesięcznie i pięć transakcji na sekundę. Aby uzyskać więcej informacji na temat kluczy, których używasz z programem LUIS, zobacz [Zarządzanie kluczami](./luis-how-to-azure-subscription.md).

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Gdzie jest funkcja Wzorzec, która zapewnia dopasowywanie wyrażeń regularnych?
Poprzednia **funkcja wzorzec** jest obecnie przestarzała i została zastąpiona przez **[wzorce](luis-concept-patterns.md)**.

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Jak mogę jednostki w celu ściągnięcie poprawnych danych?
Zobacz [jednostki i](luis-concept-entity-types.md) [wyodrębnianie danych.](luis-concept-data-extraction.md)

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Czy wariacje przykładowej wypowiedzi powinny zawierać interpunkcja?
Użyj jednego z następujących rozwiązań:
* Ignorowanie [interpunkcji](luis-reference-application-settings.md#punctuation-normalization)
* Dodawanie różnych odmian jako przykładowych wypowiedzi do intencji
* Dodaj wzorzec przykładowej wypowiedzi ze składnią , aby [zignorować](luis-concept-patterns.md#pattern-syntax) interpunkcja.

### <a name="does-luis-currently-support-cortana"></a>Czy usługa LUIS obsługuje obecnie Cortanę?

Wstępnie utworzone aplikacje Cortany zostały wycofane w 2017 r. Nie są już obsługiwane.

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Jak mogę przenieść własność aplikacji usługi LUIS?
Aby przenieść aplikację LUIS do innej subskrypcji platformy Azure, wyeksportuj aplikację LUIS i zaimportuj ją przy użyciu nowego konta. Zaktualizuj identyfikator aplikacji LUIS w aplikacji klienckiej, która go wywołuje. Nowa aplikacja może zwrócić nieco inne wyniki usługi LUIS niż oryginalna aplikacja.

### <a name="a-prebuilt-entity-is-tagged-in-an-example-utterance-instead-of-my-custom-entity-how-do-i-fix-this"></a>Wstępnie sprecyzowana jednostka jest otagowana w przykładowej wypowiedzi, a nie w mojej jednostce niestandardowej. Jak mogę rozwiązać ten problem?

W portalu usługi LUIS możesz oznaczać etykietami tekst dla konkretnej jednostki, która cię interesuje. Jeśli w portalu usługi LUIS nie jest wyświetlane poprawne przewidywanie jednostek, może być konieczne dodanie większej liczby wypowiedzi i oznaczenie jednostki w tekście etykietą lub dodanie funkcji.

### <a name="i-tried-to-import-an-app-or-version-file-but-i-got-an-error-what-happened"></a>Próbowano zaimportować aplikację lub plik wersji, ale wystąpił błąd. Co się stało?

Przeczytaj więcej na temat [błędów importowania wersji.](luis-how-to-manage-versions.md#import-errors)

<a name="luis-collaborating"></a>

## <a name="collaborating-and-contributing"></a>Współpraca i współtworowanie

### <a name="how-do-i-give-collaborators-access-to-luis-with-azure-active-directory-azure-ad-or-azure-role-based-access-control-azure-rbac"></a>Jak mogę współpracownikom dostęp do usługi LUIS za pomocą usługi Azure Active Directory (Azure AD) lub kontroli dostępu opartej na rolach (RBAC) platformy Azure?

Zobacz [Azure Active Directory zasobów i](luis-how-to-collaborate.md#azure-active-directory-resources)  Azure Active Directory [dzierżawy,](luis-how-to-collaborate.md#azure-active-directory-tenant-user) aby dowiedzieć się, jak udzielić współpracownikom dostępu.

<a name="luis-endpoint"></a>

## <a name="endpoint"></a>Punkt końcowy

### <a name="i-received-an-http-403-error-status-code-how-do-i-fix-it"></a>Otrzymano kod stanu błędu HTTP 403. Jak go naprawić?

Kody stanu błędów 403 i 429 są zwracane po przekroczeniu liczby transakcji na sekundę lub transakcji miesięcznie w warstwie cenowej. Zwiększ warstwę cenową lub użyj Language Understanding [kontenerów](luis-container-howto.md).

W przypadku użycia wszystkich bezpłatnych 1000 zapytań dotyczących punktów końcowych lub przekroczenia miesięcznego limitu przydziału transakcji w warstwie cenowej otrzymasz kod stanu błędu HTTP 403.

Aby naprawić ten błąd, [](luis-how-to-azure-subscription.md#change-the-pricing-tier) musisz zmienić warstwę cenową [](get-started-portal-deploy-app.md#create-the-endpoint-resource) na wyższą lub utworzyć nowy zasób i [przypisać go do aplikacji.](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal)

Rozwiązania tego błędu obejmują:

* W [Azure Portal](https://portal.azure.com)w zasobie usługi Language Understanding w warstwie cenowej zarządzanie zasobami **—>** zmień warstwę cenową na wyższą warstwę TPS. Nie musisz nic robić w portalu Language Understanding, jeśli zasób jest już przypisany do Language Understanding aplikacji.
*  Jeśli użycie przekracza najwyższą warstwę cenową, dodaj więcej Language Understanding zasobów przy użyciu usługi równoważenia obciążenia. Kontener [Language Understanding z](luis-container-howto.md) usługą Kubernetes lub Docker Compose może w tym pomóc.

### <a name="i-received-an-http-429-error-status-code-how-do-i-fix-it"></a>Został odebrany kod stanu błędu HTTP 429. Jak go naprawić?

Kody stanu błędów 403 i 429 są zwracane po przekroczeniu liczby transakcji na sekundę lub transakcji miesięcznie w warstwie cenowej. Zwiększ warstwę cenową lub użyj Language Understanding [kontenerów](luis-container-howto.md).

Ten kod stanu jest zwracany, gdy transakcje na sekundę przekraczają warstwę cenową.

Rozwiązania obejmują:

* Możesz zwiększyć [warstwę cenową](luis-how-to-azure-subscription.md#change-the-pricing-tier), jeśli nie jesteś w najwyższej warstwie.
* Jeśli użycie przekracza najwyższą warstwę cenową, dodaj Language Understanding zasobów przy użyciu usługi równoważenia obciążenia. Kontener [Language Understanding z](luis-container-howto.md) usługą Kubernetes lub Docker Compose może w tym pomóc.
* Żądania aplikacji klienckiej można [](/azure/architecture/best-practices/transient-faults#general-guidelines) samodzielnie wdrożyć przy użyciu zasad ponawiania, gdy otrzymasz ten kod stanu.

### <a name="my-endpoint-query-returned-unexpected-results-what-should-i-do"></a>Moje zapytanie punktu końcowego zwróciło nieoczekiwane wyniki. Co mam zrobić?

Nieoczekiwane wyniki przewidywania zapytania są oparte na stanie opublikowanego modelu. Aby poprawić model, może być konieczne jego zmiana, wyt szkolenie i opublikowanie.

Poprawianie modelu rozpoczyna się od uczenia [aktywnego.](luis-how-to-review-endpoint-utterances.md)

Trenowania nie deterministycznego można usunąć, aktualizując interfejs [API ustawień wersji](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/versions-update-application-version-settings) aplikacji w celu użycia wszystkich danych szkoleniowych.

Zapoznaj się [z najlepszymi rozwiązaniami,](luis-concept-best-practices.md) aby uzyskać inne porady.

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Dlaczego usługa LUIS dodaje spacje do zapytania wokół lub w środku wyrazów?
Usługa LUIS [tokenizuje](luis-glossary.md#token) wypowiedź na podstawie [kultury](luis-language-support.md#tokenization). Zarówno oryginalna wartość, jak i wartość tokenizowana są dostępne do [wyodrębniania danych](luis-concept-data-extraction.md#tokenized-entity-returned).

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Jak mogę utworzyć i przypisać klucz punktu końcowego usługi LUIS?
[Utwórz klucz punktu końcowego na](luis-how-to-azure-subscription.md) platformie Azure dla [poziomu](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) usługi. [Przypisz klucz](luis-how-to-azure-subscription.md) na stronie **[Zasoby platformy Azure.](luis-how-to-azure-subscription.md)** Nie ma odpowiedniego interfejsu API dla tej akcji. Następnie należy zmienić żądanie HTTP na punkt końcowy, aby [użyć nowego klucza punktu końcowego](luis-how-to-azure-subscription.md).

### <a name="how-do-i-interpret-luis-scores"></a>Jak mogę interpretację wyników usługi LUIS?
System powinien używać najwyżej ocenianej intencji niezależnie od jej wartości. Na przykład wynik poniżej 0,5 (mniej niż 50%) nie musi oznaczać, że usługa LUIS ma niski poziom pewności. Podanie większej liczby danych treningowych może pomóc [w zwiększeniu wyniku](luis-concept-prediction-score.md) najbardziej prawdopodobnego zamiaru.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Dlaczego nie widzę trafień punktu końcowego na pulpicie nawigacyjnym mojej aplikacji?
Łączna liczba trafień punktu końcowego na pulpicie nawigacyjnym aplikacji jest okresowo aktualizowana, ale metryki skojarzone z kluczem punktu końcowego usługi LUIS w Azure Portal są aktualizowane częściej.

Jeśli nie widzisz zaktualizowanych trafień punktu końcowego na pulpicie nawigacyjnym, zaloguj się do usługi  Azure Portal i znajdź zasób skojarzony z kluczem punktu końcowego usługi LUIS, a następnie otwórz pozycję Metryki, aby wybrać metrykę Łączna **liczba** wywołań. Jeśli klucz punktu końcowego jest używany dla więcej niż jednej aplikacji usługi LUIS, metryka w Azure Portal pokazuje zagregowaną liczbę wywołań ze wszystkich aplikacji usługi LUIS, które z nich korzystają.

### <a name="is-there-a-powershell-command-get-to-the-endpoint-quota"></a>Czy istnieje polecenie programu PowerShell, aby uzyskać limit przydziału punktu końcowego?

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Aby wyświetlić limit przydziału punktu końcowego, możesz użyć polecenia programu PowerShell:

```powershell
Get-AzCognitiveServicesAccountUsage -ResourceGroupName <your-resource-group> -Name <your-resource-name>
```

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>Moja aplikacja LUIS działała dzisiaj, ale obecnie występują błędy 403. Nie zmieniłem aplikacji. Jak go naprawić?
Postępuj zgodnie z [tymi instrukcjami,](#how-do-i-create-and-assign-a-luis-endpoint-key) aby utworzyć klucz punktu końcowego usługi LUIS i przypisać go do aplikacji. Następnie należy zmienić żądanie HTTP aplikacji klienckiej na punkt końcowy, aby [użyć nowego klucza punktu końcowego](luis-how-to-azure-subscription.md). Jeśli nowy zasób został utworzony w innym regionie, zmień również region żądania klienta HTTP.

### <a name="how-do-i-secure-my-luis-endpoint"></a>Jak mogę zabezpieczyć punkt końcowy usługi LUIS?
Zobacz [Zabezpieczanie punktu końcowego](luis-how-to-azure-subscription.md#securing-the-endpoint).

## <a name="working-within-luis-limits"></a>Praca w ramach limitów usługi LUIS

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>Jaka jest maksymalna liczba intencji i jednostek, które może obsługiwać aplikacja LUIS?
Zobacz [informacje o granicach.](luis-limits.md)

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Chcę utworzyć aplikację usługi LUIS z większą niż maksymalną liczbą intencji. Co mam zrobić?

Zobacz [Najlepsze rozwiązania dotyczące intencji](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents).

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Jakie są limity liczby i rozmiaru list fraz?
Aby uzyskać informacje o maksymalnej długości [listy fraz,](./luis-concept-feature.md)zobacz [informacje o granicach.](luis-limits.md)

### <a name="what-are-the-limits-on-example-utterances"></a>Jakie są limity przykładowych wypowiedzi?
Zobacz [informacje o granicach.](luis-limits.md)

## <a name="testing-and-training"></a>Testowanie i szkolenie

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>W okienku testowania wsadowego występują błędy niektórych modeli w mojej aplikacji. Jak mogę rozwiązać ten problem?

Błędy wskazują, że występują pewne rozbieżności między etykietami i przewidywaniami z modeli. Aby rozwiązać ten problem, wykonaj jedno lub oba z następujących zadań:
* Aby pomóc ujmować w uciek między intencjami usługi LUIS, dodaj więcej etykiet.
* Aby przyspieszyć naukę, dodaj funkcje listy fraz, które wprowadzają słownictwo specyficzne dla danej domeny.

Zobacz samouczek [dotyczący testowania wsadowego.](./luis-how-to-batch-test.md)

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>Gdy aplikacja jest eksportowana, a następnie ponownie importowana do nowej aplikacji (z nowym identyfikatorem aplikacji), wyniki przewidywania usługi LUIS są różne. Dlaczego tak się dzieje?

Zobacz Prediction differences between copies of same app (Różnice [przewidywania między kopiami tej samej aplikacji).](luis-concept-prediction-score.md#review-intents-with-similar-scores)

### <a name="some-utterances-go-to-the-wrong-intent-after-i-made-changes-to-my-app-the-issue-seems-to-disappear-at-random-how-do-i-fix-it"></a>Niektóre wypowiedzi trafiają do niewłaściwej intencji po wymuśeniu zmian w aplikacji. Wydaje się, że problem znika losowo. Jak go naprawić?

Zobacz [Train with all data (Trenuj ze wszystkimi danymi).](luis-how-to-train.md#train-with-all-data)

## <a name="app-publishing"></a>Publikowanie aplikacji

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Jaki jest identyfikator dzierżawy w oknie "Dodawanie klucza do aplikacji"?
Na platformie Azure dzierżawa reprezentuje klienta lub organizację skojarzoną z usługą. Znajdź swój identyfikator dzierżawy w Azure Portal polu **Identyfikator** katalogu, wybierając pozycję **Azure Active Directory**  >  **Właściwości.**  >  

![Identyfikator dzierżawy w Azure Portal](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
<a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>


### <a name="why-are-there-more-endpoint-keys-assigned-to-my-app-than-i-assigned"></a>Dlaczego do mojej aplikacji jest przypisanych więcej kluczy punktu końcowego niż przypisano mi?
Dla wygody każda aplikacja luis ma klucz tworzenia/początkowego na liście punktów końcowych. Ten klucz umożliwia tylko kilka trafień punktu końcowego, dzięki czemu można wypróbować usługi LUIS.

Jeśli twoja aplikacja istniała, zanim usługa LUIS była ogólnie dostępna, klucze punktu końcowego usługi LUIS w Twojej subskrypcji są przypisywane automatycznie. Zostało to zrobione, aby ułatwić migrację z dostępem do sieci. Wszystkie nowe klucze punktu końcowego usługi LUIS w Azure Portal _nie są_ automatycznie przypisywane do usługi LUIS.

## <a name="key-management"></a>Zarządzanie kluczami

### <a name="how-do-i-know-what-key-i-need-where-i-get-it-and-what-i-do-with-it"></a>Jak mogę, jakiego klucza potrzebuję, skąd go uzyskać i co robię z nim?

Zobacz [Authoring and query prediction endpoint keys in LUIS](luis-how-to-azure-subscription.md) (Tworzenie kluczy punktu końcowego przewidywania i wykonywanie zapytań w u usługi LUIS), aby poznać różnice między kluczem tworzenia a kluczem środowiska uruchomieniowego przewidywania.

### <a name="i-got-an-error-about-being-out-of-quota-how-do-i-fix-it"></a>Wystąpił błąd z informacjami o tym, że przekroczono limit przydziału. Jak go naprawić?

Aby dowiedzieć się więcej, zobacz Fix HTTP status code 403 and 429 (Naprawianie kodu stanu HTTP [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) i [429).](#i-received-an-http-429-error-status-code-how-do-i-fix-it)

### <a name="i-need-to-handle-more-endpoint-queries-how-do-i-do-that"></a>Muszę obsługiwać więcej zapytań dotyczących punktów końcowych. Jak mogę to zrobić?

Aby dowiedzieć się więcej, zobacz Fix HTTP status code 403 and 429 (Naprawianie kodu stanu HTTP [403](#i-received-an-http-403-error-status-code-how-do-i-fix-it) i [429).](#i-received-an-http-429-error-status-code-how-do-i-fix-it)

### <a name="i-created-an-authoring-key-but-it-isnt-showing-in-the-luis-portal-what-happened"></a>Utworzono klucz tworzenia, ale nie jest on pokazywany w portalu usługi LUIS. Co się stało?

Klucze tworzenia są dostępne w portalu usługi LUIS po migracji do obsługi [klucza tworzenia.](luis-migration-authoring.md)

## <a name="app-management"></a>Zarządzanie aplikacjami

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Jak mogę pobrać dziennik wypowiedzi użytkownika?
Domyślnie aplikacja usługi LUIS rejestruje wypowiedzi od użytkowników. Aby pobrać dziennik wypowiedzi, które użytkownicy wysyłają do aplikacji usługi LUIS, przejdź do Moje aplikacje **i** wybierz aplikację. Na kontekstowym pasku narzędzi wybierz pozycję **Eksportuj dzienniki punktu końcowego.** Dziennik jest sformatowany jako plik wartości rozdzielanych przecinkami (CSV).

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Jak mogę wyłączyć rejestrowanie wypowiedzi?
Rejestrowanie wypowiedzi użytkowników można wyłączyć, ustawiając w adresie URL punktu końcowego adres URL używany przez aplikację klienczą `log=false` do wykonywania zapytań do usługi LUIS. Jednak wyłączenie rejestrowania wyłącza możliwość sugerowania wypowiedzi lub poprawiania wydajności aplikacji LUIS w oparciu o [aktywną naukę.](luis-concept-review-endpoint-utterances.md#what-is-active-learning) Jeśli ustawisz rekord ze względu na kwestie związane z prywatnością danych, nie możesz pobrać rekordu tych wypowiedzi użytkownika z usługi LUIS ani użyć tych wypowiedzi do ulepszenia `log=false` aplikacji.

Rejestrowanie jest jedynym magazynem wypowiedzi.

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Dlaczego nie chcę chcieć, aby wszystkie moje wypowiedzi punktu końcowego zostały zarejestrowane?
Jeśli używasz dziennika do analizy przewidywania, nie przechwytuj w dzienniku wypowiedzi testowych.

## <a name="data-management"></a>Zarządzanie danymi

### <a name="can-i-delete-data-from-luis"></a>Czy mogę usunąć dane z usługi LUIS?

* Zawsze możesz usuwać przykładowe wypowiedzi używane do trenowania usługi LUIS. Jeśli usuniesz przykładową wypowiedź z aplikacji usługi LUIS, zostanie ona usunięta z usługi internetowej LUIS i nie będzie można jej wyeksportować.
* Możesz usunąć wypowiedzi z listy wypowiedzi użytkownika, które sugeruje usługa LUIS na stronie **Review endpoint utterances** (Przeglądanie wypowiedzi punktu końcowego). Usunięcie wypowiedzi z tej listy uniemożliwia sugerowanie ich, ale nie usuwa ich z dzienników.
* Jeśli usuniesz konto, wszystkie aplikacje wraz z przykładami wypowiedzi i dziennikami zostaną usunięte. Dane są przechowywane na serwerach przez 60 dni, zanim zostaną trwale usunięte.

### <a name="how-does-microsoft-manage-data-i-send-to-luis"></a>Jak firma Microsoft zarządza danymi, które wysyłam do usługi LUIS?

Centrum [zaufania wyjaśnia](https://www.microsoft.com/trustcenter) nasze zobowiązania i opcje zarządzania danymi i uzyskiwania do nich dostępu w usługach platformy Azure.

## <a name="language-and-translation-support"></a>Obsługa języka i tłumaczenia

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Mam aplikację w jednym języku i chcę utworzyć aplikację równoległą w innym języku. Jaki jest najłatwiejszy sposób?
1. Wyeksportuj aplikację.
2. Przetłumacz oznaczone wypowiedzi w pliku JSON wyeksportowanych aplikacji na język docelowy.
3. Może być konieczne zmiana nazw intencji i jednostek lub pozostawienie ich bez zmian.
4. Na koniec zaimportuj aplikację, aby mieć aplikację usługi LUIS w języku docelowym.

## <a name="app-notification"></a>Powiadomienie aplikacji

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Dlaczego otrzymuję wiadomość e-mail z informacją, że prawie nie przekroczono limitu przydziału?
Klucz tworzenia/początkowego jest dozwolony tylko dla 1000 zapytań punktu końcowego miesięcznie. Utwórz klucz punktu końcowego usługi LUIS (bezpłatny lub płatny) i użyj go podczas tworzenia zapytań dotyczących punktu końcowego. Jeśli wykonujesz zapytania dotyczące punktu końcowego z bota lub innej aplikacji klienckiej, musisz zmienić klucz punktu końcowego usługi LUIS.

## <a name="bots"></a>Boty

### <a name="my-luis-bot-isnt-working-what-do-i-do"></a>Mój bot usługi LUIS nie działa. Co mam zrobić?

Pierwszym problemem jest wyizolowanie, jeśli problem jest związany z usługą LUIS lub występuje poza oprogramowaniem pośredniczącem usługi LUIS.

#### <a name="resolve-issue-in-luis"></a>Rozwiązywanie problemu z programem LUIS
Przekaż tę samą wypowiedź do usługi LUIS z punktu [końcowego usługi LUIS.](luis-get-started-create-app.md#query-the-v2-api-prediction-endpoint) Jeśli wystąpi błąd, rozwiąż problem w u usługi LUIS, dopóki błąd nie zostanie już zwrócony. Typowe błędy to:

* `Out of call volume quota. Quota will be replenished in <time>.`— Ten problem wskazuje, że musisz zmienić klucz [](luis-how-to-azure-subscription.md) tworzenia z na klucz punktu końcowego lub musisz zmienić [warstwy usług.](luis-how-to-azure-subscription.md#change-the-pricing-tier)

#### <a name="resolve-issue-in-azure-bot-service"></a>Rozwiązywanie problemu w programie Azure Bot Service

Jeśli używasz konta usługi Azure Bot Service problem jest  taki, że test w czat internetowy `Sorry, my bot code is having an issue` zwraca wartość , sprawdź dzienniki:

1. W Azure Portal bota w sekcji Zarządzanie **botem** wybierz pozycję **Build (Skompilowanie).**
1. Otwórz edytor kodu online.
1. Na górnym niebieskim pasku nawigacyjnym wybierz nazwę bota (drugi element po prawej stronie).
1. Z wynikowej listy rozwijanej wybierz pozycję **Otwórz konsolę Kudu.**
1. Wybierz **pozycję LogFiles,** a następnie wybierz **pozycję Aplikacja.** Przejrzyj wszystkie pliki dziennika. Jeśli nie widzisz błędu w folderze aplikacji, przejrzyj wszystkie pliki dziennika w obszarze **LogFiles**.
1. Pamiętaj, aby ponownie skompilować projekt, jeśli używasz skompilowanego języka, takiego jak C#.

> [!Tip]
> Konsola programu może również instalować pakiety.

#### <a name="resolve-issue-while-debugging-on-local-machine-with-bot-framework"></a>Rozwiąż problem podczas debugowania na komputerze lokalnym za pomocą Bot Framework.

Aby dowiedzieć się więcej na temat lokalnego debugowania bota, zobacz [Debugowanie bota](/azure/bot-service/bot-service-debug-bot).

## <a name="integrating-luis"></a>Integrowanie usługi LUIS

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Gdzie jest tworzona moja aplikacja LUIS podczas procesu subskrypcji bota aplikacji internetowej platformy Azure?
Jeśli wybierzesz szablon usługi LUIS  i wybierzesz przycisk Wybierz w okienku szablonu, okienko po lewej stronie zmieni się, aby uwzględnić typ szablonu, i zapyta, w jakim regionie utworzyć szablon usługi LUIS. Proces bota aplikacji internetowej nie tworzy jednak subskrypcji usługi LUIS.

![Region bota aplikacji internetowej szablonu usługi LUIS](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Które regiony usługi LUIS obsługują Bot Framework zasyłania mowy?
[Zasyłanie mowy](/bot-framework/bot-service-manage-speech-priming) jest obsługiwane tylko w przypadku aplikacji USŁUGI LUIS w wystąpieniu centralnym (USA).

## <a name="api-programming-strategies"></a>Strategie programowania interfejsu API

### <a name="how-do-i-programmatically-get-the-luis-region-of-a-resource"></a>Jak mogę programowo pobrać region usługi LUIS zasobu?

Użyj przykładu usługi LUIS, [aby znaleźć region programowo](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/find-region) przy użyciu języka C# lub Node.Js.

## <a name="luis-service"></a>Usługa LUIS

### <a name="is-language-understanding-luis-available-on-premises-or-in-private-cloud"></a>Czy Language Understanding (LUIS) jest dostępna lokalnie, czy w chmurze prywatnej?

Tak, możesz użyć kontenera usługi LUIS [w](luis-container-howto.md) tych scenariuszach, jeśli masz niezbędną łączność z użyciem miernika.

## <a name="migrating-to-the-next-version"></a>Migrowanie do następnej wersji

### <a name="how-do-i-migrate-to-preview-v3-api"></a>Jak mogę przeprowadzić migrację do interfejsu API w wersji 3 w wersji zapoznawczej?

Zobacz [Przewodnik migracji interfejsu API od wersji 2 do 3 dla aplikacji usługi LUIS](luis-migration-api-v3.md)

## <a name="build-2019-conference-announcements"></a>Ogłoszenia dotyczące konferencji Build 2019

Na konferencji Build 2019 zostały wydane następujące funkcje:

* [Przewodnik migracji interfejsu API w wersji 3 w wersji zapoznawczej](luis-migration-api-v3.md)
* [Ulepszony pulpit nawigacyjny analizy](luis-how-to-use-dashboard.md)
* [Ulepszone wstępnie utworzone domeny](luis-reference-prebuilt-domains.md)
* [Jednostki listy dynamicznej](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Jednostki zewnętrzne](luis-migration-api-v3.md#external-entities-passed-in-at-prediction-time)

Filmy wideo:

* [Jak używać usługi Azure Conversational AI do skalowania firmy na potrzeby nowej generacji](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat usługi LUIS, zobacz następujące zasoby:
* [Stack Overflow pytania otagowane za pomocą usługi LUIS](https://stackoverflow.com/questions/tagged/luis)
* [Microsoft Q&Strona pytania dla usługi MSDN Language Understanding Intelligent Services (LUIS)](/answers/topics/azure-language-understanding.html)