---
title: Korzystanie z kluczy tworzenia i czasu wykonywania — LUIS
description: Przy pierwszym użyciu LUIS nie trzeba tworzyć klucza tworzenia. Aby opublikować aplikację, a następnie użyć punktu końcowego środowiska uruchomieniowego, należy utworzyć i przypisać do aplikacji klucz środowiska uruchomieniowego.
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 09/07/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 168833ea0a451913f4ed019cba832a16207e0d9c
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988155"
---
# <a name="create-luis-resources"></a>Tworzenie zasobów LUIS

Zasoby środowiska uruchomieniowego tworzenie i prognozowanie zapytań zapewniają uwierzytelnianie do aplikacji Language Understanding (LUIS) i punktu końcowego przewidywania.

<a name="azure-resources-for-luis"></a>
<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

## <a name="luis-resources"></a>Zasoby LUIS

LUIS umożliwia korzystanie z trzech typów zasobów platformy Azure i jednego zasobu niezwiązanego z platformą Azure:

|Zasób|Przeznaczenie|Usługa poznawcze `kind`|Usługa poznawcze `type`|
|--|--|--|--|
|Tworzenie zasobu|Umożliwia tworzenie, uczenie, testowanie i publikowanie aplikacji oraz zarządzanie nimi. [Utwórz zasób tworzenia Luis](luis-how-to-azure-subscription.md#create-luis-resources-in-the-azure-portal) , jeśli zamierzasz tworzyć aplikacje Luis programowo lub w portalu Luis. Przed połączeniem zasobów tworzenia platformy Azure z aplikacją należy [przeprowadzić migrację konta usługi Luis](luis-migration-authoring.md#what-is-migration) . Uprawnienia do zasobu tworzenia można kontrolować, przypisując osobom [rolę współautor](#contributions-from-other-authors). <br><br> Dostępna jest jedna warstwa dla zasobu LUIS Authoring:<br> <ul> <li>**Bezpłatny zasób tworzenia F0**, który zapewnia 1 000 000 bezpłatnych transakcji tworzenia i 1 000 bezpłatne żądania punktów końcowych przewidywania testów. |`LUIS.Authoring`|`Cognitive Services`|
|Zasób predykcyjny| Po opublikowaniu aplikacji LUIS Użyj zasobu predykcyjny/Key do wysyłania zapytań do żądań punktów końcowych przewidywania. Utwórz zasób przewidywania LUIS przed zażądaniem przez aplikację kliencką prognoz wykraczających poza żądania 1 000 dostarczone przez zasób tworzenia lub startu. <br><br> Dla zasobu przewidywania dostępne są dwie warstwy:<br><ul> <li> **Bezpłatny zasób predykcyjny F0**, który zapewnia 10 000 bezpłatnych żądań punktu końcowego przewidywania miesięcznie.<br> <li> **Standardowy zasób predykcyjny S0**, który jest warstwą płatną. [Dowiedz się więcej o cenach.](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)|`LUIS`|`Cognitive Services`|
|Zasób Starter/Wersja próbna|Umożliwia tworzenie, uczenie, testowanie i publikowanie aplikacji oraz zarządzanie nimi. Ten zasób jest tworzony domyślnie, jeśli wybierzesz opcję zasobów początkowych po pierwszym zalogowaniu się do LUIS. Klucz początkowy będzie ostatecznie przestarzały. Wszyscy użytkownicy LUIS będą musieli [migrować swoje konta](luis-migration-authoring.md#what-is-migration) i połączyć ich aplikacje Luis z zasobem tworzenia. W przeciwieństwie do zasobu tworzenia, ten zasób nie daje uprawnień do kontroli dostępu opartej na rolach platformy Azure. <br><br> Tak samo jak zasób tworzenia, zasób początkowy zapewnia 1 000 000 bezpłatnych transakcji tworzenia i 1 000 żądań punktów końcowych przewidywania bezpłatnych testów.|-|To nie jest zasób platformy Azure.|
|[Cognitive Services klucz zasobu wielousługowego](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|Żądania punktu końcowego przewidywania zapytań udostępniane w LUIS i innych obsługiwanych usługach poznawczych.|`CognitiveServices`|`Cognitive Services`|


> [!Note]
> LUIS udostępnia dwa typy zasobów F0 (warstwy Bezpłatna): jeden do tworzenia transakcji i jeden dla transakcji predykcyjnych. Jeśli korzystasz z bezpłatnego przydziału dla transakcji predykcyjnych, upewnij się, że korzystasz z zasobów przewidywania F0, co miesięcznie zawiera 10 000 bezpłatnych transakcji, a nie zasobów do tworzenia, co 1 000 pozwala na comiesięczne transakcję przewidywania.

Po zakończeniu procesu tworzenia zasobów platformy Azure [Przypisz zasób](#assign-a-resource-to-an-app) do aplikacji w portalu Luis.

> [!important]
> Aplikacje LUIS należy tworzyć w [regionach](luis-reference-regions.md#publishing-regions) , w których mają być publikowane i wyszukiwane.

## <a name="resource-ownership"></a>Własność zasobów

Zasób platformy Azure, taki jak zasób LUIS, należy do subskrypcji zawierającej zasób.

Aby zmienić własność zasobu, można wykonać jedną z następujących czynności:
* Przenieś [własność](../../cost-management-billing/manage/billing-subscription-transfer.md) subskrypcji.
* Wyeksportuj aplikację LUIS jako plik, a następnie zaimportuj aplikację w innej subskrypcji. Eksport jest dostępny na stronie **Moje aplikacje** w portalu Luis.

## <a name="resource-limits"></a>Limity zasobów

### <a name="authoring-key-creation-limits"></a>Tworzenie ograniczeń tworzenia kluczy

Dla każdej subskrypcji można utworzyć dowolną liczbę kluczy tworzenia dla każdego regionu. Publikowanie regionów różni się od regionów tworzenia. Upewnij się, że tworzysz aplikację w regionie tworzenia odpowiadającą regionowi publikowania, w którym ma znajdować się aplikacja kliencka. Aby uzyskać informacje dotyczące sposobu, w jaki regiony tworzenia są mapowane na publikowanie regionów, zobacz temat [Tworzenie i publikowanie regionów](luis-reference-regions.md). 

Aby uzyskać więcej informacji na temat limitów kluczy, zobacz [limity kluczy](luis-limits.md#key-limits).

### <a name="errors-for-key-usage-limits"></a>Błędy limitów użycia klucza

Limity użycia są zależne od warstwy cenowej.

W przypadku przekroczenia limitu przydziału transakcji na sekundę (TPS) otrzymasz błąd HTTP 429. W przypadku przekroczenia limitu przydziału za miesiąc (TPM) pojawia się błąd HTTP 403.


### <a name="reset-an-authoring-key"></a>Zresetuj klucz tworzenia

W przypadku [migrowanych aplikacji zasobów tworzenia](luis-migration-authoring.md) : Jeśli klucz autorstwa został naruszony, zresetuj klucz w Azure Portal na stronie **klucze** dla zasobu tworzenia.

W przypadku aplikacji, które nie zostały poddane migracji: klucz jest resetowany we wszystkich aplikacjach w portalu LUIS. W przypadku tworzenia aplikacji za pośrednictwem interfejsów API tworzenia należy zmienić wartość `Ocp-Apim-Subscription-Key` nowego klucza.

### <a name="regenerate-an-azure-key"></a>Wygeneruj ponownie klucz platformy Azure

Klucz platformy Azure można wygenerować ponownie ze strony **klucze** w Azure Portal.


<a name="securing-the-endpoint"></a>

## <a name="app-ownership-access-and-security"></a>Własność aplikacji, dostęp i zabezpieczenia

Aplikacja jest definiowana przez zasoby platformy Azure, które są określane przez subskrypcję właściciela.

Możesz przenieść swoją aplikację LUIS. Użyj następujących zasobów, aby to zrobić, korzystając z Azure Portal lub interfejsu wiersza polecenia platformy Azure:

* [Przenoszenie aplikacji między LUIS tworzenia zasobów](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Przenoszenie zasobu do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Przenoszenie zasobu w ramach tej samej subskrypcji lub między subskrypcjami](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)


### <a name="contributions-from-other-authors"></a>Wkłady od innych autorów

Dla [zmigrowanych aplikacji zasobów do tworzenia](luis-migration-authoring.md) : można zarządzać _współautorami_ dla zasobu tworzenia w Azure Portal przy użyciu strony **Kontrola dostępu (IAM)** . Dowiedz się, [jak dodać użytkownika](luis-how-to-collaborate.md) przy użyciu adresu e-mail współpracownika i roli współautor.

W przypadku aplikacji, które nie zostały jeszcze zmigrowane: można zarządzać wszystkimi _współpracownikami_ na stronie **Zarządzanie > współpracownikami** w portalu Luis.

### <a name="query-prediction-access-for-private-and-public-apps"></a>Dostęp do prognozowania zapytań dla prywatnych i publicznych aplikacji

W przypadku aplikacji prywatnych dostęp do przewidywania zapytań jest dostępny dla właścicieli i współautorów. W przypadku aplikacji publicznych dostęp do środowiska uruchomieniowego jest dostępny dla użytkowników mających własne [usługi poznawcze](../cognitive-services-apis-create-account.md) platformy Azure lub zasób środowiska uruchomieniowego [Luis](#create-resources-in-the-azure-portal) oraz identyfikator aplikacji publicznej.

Obecnie nie ma katalogu aplikacji publicznych.

### <a name="authoring-permissions-and-access"></a>Uprawnienia do tworzenia i dostęp
Dostęp do aplikacji z portalu [Luis](luis-reference-regions.md#luis-website) lub [interfejsów API tworzenia](https://go.microsoft.com/fwlink/?linkid=2092087) jest kontrolowany przez zasób tworzenia platformy Azure.

Właściciel i wszyscy Współautorzy mają dostęp do tworzenia aplikacji.

|Dostęp do tworzenia obejmuje:|Uwagi|
|--|--|
|Dodaj lub Usuń klucze punktów końcowych||
|Wersja eksportu||
|Eksportowanie dzienników punktów końcowych||
|Wersja importowana||
|Tworzenie aplikacji jako publicznej|Gdy aplikacja jest publiczna, każda osoba, która ma klucz tworzenia lub punktu końcowego, może wysyłać zapytania do aplikacji.|
|Modyfikuj model|
|Publikowanie|
|Zapoznaj się z punktem końcowym wyrażenia długości na potrzeby [aktywnego uczenia](luis-how-to-review-endpoint-utterances.md)|
|Szkolenie|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>Dostęp w czasie wykonywania przewidywania punktu końcowego

Dostęp do wykonywania zapytań dotyczących punktu końcowego przewidywania jest kontrolowany przez ustawienie na stronie **Informacje o aplikacji** w sekcji **Zarządzanie** .

|[Prywatny punkt końcowy](#runtime-security-for-private-apps)|[Publiczny punkt końcowy](#runtime-security-for-public-apps)|
|:--|:--|
|Dostępne dla właściciela i współautorów|Dostępne dla właściciela, współautorów i innych osób znających identyfikator aplikacji|

Można kontrolować, kto widzi klucz środowiska uruchomieniowego LUIS, wywołując go w środowisku serwer-serwer. Jeśli używasz LUIS z bot, połączenie między bot i LUIS jest już bezpieczniejsze. W przypadku bezpośredniego wywoływania punktu końcowego LUIS należy utworzyć interfejs API po stronie serwera (taki jak [Funkcja](https://azure.microsoft.com/services/functions/)platformy Azure) z dostępem kontrolowanym (za pośrednictwem [usługi Azure AD](https://azure.microsoft.com/services/active-directory/)). Gdy interfejs API po stronie serwera jest wywoływany i uwierzytelniany, a Autoryzacja jest weryfikowana, należy przekazać wywołanie do LUIS. Ta strategia nie zapobiega atakom typu man-in-the-middle. Jednak robi to, że klucz i adres URL punktu końcowego są używane przez użytkowników, umożliwiają śledzenie dostępu i Zezwalanie na Dodawanie rejestrowania odpowiedzi dla punktów końcowych (takich jak [Application Insights](https://azure.microsoft.com/services/application-insights/)).

### <a name="runtime-security-for-private-apps"></a>Zabezpieczenia środowiska uruchomieniowego dla prywatnych aplikacji

Środowisko uruchomieniowe aplikacji prywatnej jest dostępne tylko dla następujących kluczy:

|Klucz i użytkownik|Wyjaśnienie|
|--|--|
|Klucz autorstwa właściciela| Do 1 000 trafień punktów końcowych|
|Współautorzy/klucze autorstwa| Do 1 000 trafień punktów końcowych|
|Dowolny klucz przypisany do LUIS przez autora lub współpracownika/współautora|Na podstawie warstwy użycia klucza|

### <a name="runtime-security-for-public-apps"></a>Zabezpieczenia środowiska uruchomieniowego dla aplikacji publicznych

Gdy aplikacja jest skonfigurowana jako publiczna, _dowolny_ prawidłowy klucz Luis Authoring lub klucz punktu końcowego Luis może wykonać do niego zapytanie, o ile klucz nie użył całego przydziału punktów końcowych.

Użytkownik, który nie jest właścicielem lub współautorem, może uzyskać dostęp do środowiska uruchomieniowego aplikacji publicznej tylko wtedy, gdy podano identyfikator aplikacji. LUIS nie ma publicznego rynku ani nie pozwala użytkownikom na wyszukiwanie aplikacji publicznej.

Publiczna aplikacja jest publikowana we wszystkich regionach. W związku z tym użytkownik z kluczem zasobów LUIS opartym na regionie może uzyskać dostęp do aplikacji w dowolnym regionie, który jest skojarzony z kluczem zasobu.


### <a name="control-access-to-your-query-prediction-endpoint"></a>Kontrola dostępu do punktu końcowego przewidywania zapytań

Można kontrolować, kto może wyświetlać klucz punktu końcowego środowiska uruchomieniowego LUIS prognoz, wywołując go w środowisku serwer-serwer. Jeśli używasz LUIS z bot, połączenie między bot i LUIS jest już bezpieczniejsze. W przypadku bezpośredniego wywoływania punktu końcowego LUIS należy utworzyć interfejs API po stronie serwera (taki jak [Funkcja](https://azure.microsoft.com/services/functions/)platformy Azure) z dostępem kontrolowanym (za pośrednictwem [usługi Azure AD](https://azure.microsoft.com/services/active-directory/)). Gdy wywoływany jest interfejs API po stronie serwera, a uwierzytelnianie i autoryzacja są weryfikowane, należy przekazać wywołanie do LUIS. Ta strategia nie zapobiega atakom typu man-in-the-middle. Jednak powoduje to zamieszanie punktu końcowego od użytkowników, umożliwienie śledzenia dostępu i zezwala na Dodawanie rejestrowania odpowiedzi dla punktów końcowych (takich jak [Application Insights](https://azure.microsoft.com/services/application-insights/)).

<a name="starter-key"></a>

## <a name="sign-in-to-the-luis-portal-and-begin-authoring"></a>Zaloguj się do portalu LUIS i zacznij tworzenie

1. Zaloguj się do [portalu Luis](https://www.luis.ai) i zaakceptuj warunki użytkowania.
1. Rozpocznij tworzenie aplikacji LUIS, wybierając klucz tworzenia LUIS Azure:

   ![Zrzut ekranu przedstawiający ekran powitalny.](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Po zakończeniu procesu wyboru zasobów [Utwórz nową aplikację](luis-how-to-start-new-app.md#create-new-app-in-luis).


<a name="create-azure-resources"></a>
<a name="create-resources-in-the-azure-portal"></a>

[!INCLUDE [Create LUIS resource in Azure portal](includes/create-luis-resource.md)]

### <a name="create-resources-in-the-azure-cli"></a>Tworzenie zasobów w interfejsie wiersza polecenia platformy Azure

Utwórz poszczególne zasoby przy użyciu [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) .

Zasób `kind` :

* Projekt `LUIS.Authoring`
* Przewidując `LUIS`

1. Zaloguj się do interfejsu wiersza polecenia platformy Azure:

    ```azurecli
    az login
    ```

    To polecenie otwiera przeglądarkę, aby można było wybrać poprawne konto i zapewnić uwierzytelnianie.

1. Utwórz zasób LUIS tworzenia typu o `LUIS.Authoring` nazwie `my-luis-authoring-resource` . Utwórz go w _istniejącej_ grupie zasobów o nazwie `my-resource-group` dla `westus` regionu.

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Utwórz zasób punktu końcowego przewidywania LUIS o `LUIS` nazwie `my-luis-prediction-resource` . Utwórz go w _istniejącej_ grupie zasobów o nazwie `my-resource-group` dla `westus` regionu. Jeśli potrzebujesz wyższej przepływności niż w przypadku warstwy Bezpłatna, przejdź `F0` do `S0` . [Dowiedz się więcej o warstwach cenowych i przepływności.](luis-limits.md#key-limits)

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note]
    > Te klucze nie są używane przez portal Luis, dopóki nie zostaną przypisane na stronie **Zarządzanie**  >  **zasobami platformy Azure** w portalu Luis.

<a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>

### <a name="assign-resources-in-the-luis-portal"></a>Przypisywanie zasobów w portalu LUIS

Można przypisać zasób tworzenia dla jednej aplikacji lub dla wszystkich aplikacji w LUIS. Poniższa procedura przypisuje wszystkie aplikacje do pojedynczego zasobu tworzenia.

1. Zaloguj się do [portalu Luis](https://www.luis.ai).
1. W prawym górnym rogu wybierz swoje konto użytkownika, a następnie wybierz pozycję **Ustawienia**.
1. Na stronie **Ustawienia użytkownika** wybierz pozycję **Dodaj zasób autorstwa**, a następnie wybierz istniejący zasób tworzenia. Wybierz pozycję **Zapisz**.

## <a name="assign-a-resource-to-an-app"></a>Przypisywanie zasobu do aplikacji

>[!NOTE]
>Jeśli nie masz subskrypcji platformy Azure, nie będziesz w stanie przypisywać ani tworzyć nowych zasobów. Musisz utworzyć [bezpłatne konto platformy Azure](https://azure.microsoft.com/en-us/free/) , a następnie powrócić do Luis w celu utworzenia nowego zasobu z poziomu portalu.

Za pomocą tej procedury można utworzyć zasób tworzenia lub przewidywania lub przypisać go do aplikacji: 

1. Zaloguj się do [portalu Luis](https://www.luis.ai). Wybierz aplikację z listy **Moje aplikacje** .
1. Przejdź do obszaru **Zarządzanie**  >  **zasobami platformy Azure**:

    ![Zrzut ekranu przedstawiający stronę zasobów platformy Azure.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Na karcie **zasób predykcyjny** lub **Tworzenie zasobu** wybierz przycisk **Dodaj zasób predykcyjny** lub **Dodaj zasób tworzenia zasobów** .
1. Użyj pól w formularzu, aby znaleźć poprawny zasób, a następnie wybierz pozycję **Zapisz**.
1. Jeśli nie masz istniejącego zasobu, możesz go utworzyć, wybierając pozycję **Utwórz nowy zasób Luis?** w dolnej części okna.


### <a name="assign-a-query-prediction-runtime-resource-without-using-the-luis-portal"></a>Przypisywanie zasobu środowiska uruchomieniowego przewidywania zapytań bez korzystania z portalu LUIS

W przypadku zautomatyzowanych procesów, takich jak potoki ciągłej integracji/ciągłego wdrażania, możesz chcieć zautomatyzować przypisanie zasobu środowiska uruchomieniowego LUIS do aplikacji LUIS. Aby to zrobić, wykonaj następujące kroki:

1. Pobierz token Azure Resource Manager z [tej witryny sieci Web](https://resources.azure.com/api/token?plaintext=true). Token wygaśnie, dlatego należy od razu korzystać z niego. Żądanie zwraca token Azure Resource Manager.

    ```azurecli
    az account get-access-token --resource=https://management.core.windows.net/ --query accessToken --output tsv
    ```
    
    ![Zrzut ekranu, na którym jest wyświetlana witryna sieci Web żądająca tokenu Azure Resource Manager.](./media/luis-manage-keys/get-arm-token.png)

1. Użyj tokenu, aby zażądać zasobów środowiska uruchomieniowego LUIS w różnych subskrypcjach. Użyj [interfejsu API Luis systemu Azure](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), do którego Twoje konto użytkownika ma dostęp.

    Ten POST API wymaga następujących wartości:

    |Nagłówek|Wartość|
    |--|--|
    |`Authorization`|Wartość `Authorization` to `Bearer {token}` . Wartość tokenu musi być poprzedzona słowem `Bearer` i spacją.|
    |`Ocp-Apim-Subscription-Key`|Twój klucz tworzenia.|

    Interfejs API zwraca tablicę obiektów JSON, które reprezentują subskrypcje LUIS. Zwracane wartości obejmują identyfikator subskrypcji, grupę zasobów i nazwę zasobu zwracaną jako `AccountName` . Znajdź element w tablicy, który jest zasobem LUIS, który chcesz przypisać do aplikacji LUIS.

1. Przypisz token do zasobu LUIS, korzystając z [przypisywania kont platformy Azure Luis do](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) interfejsu API aplikacji.

    Ten POST API wymaga następujących wartości:

    |Typ|Ustawienie|Wartość|
    |--|--|--|
    |Nagłówek|`Authorization`|Wartość `Authorization` to `Bearer {token}` . Wartość tokenu musi być poprzedzona słowem `Bearer` i spacją.|
    |Nagłówek|`Ocp-Apim-Subscription-Key`|Twój klucz tworzenia.|
    |Nagłówek|`Content-type`|`application/json`|
    |Kwerendy|`appid`|Identyfikator aplikacji usługi LUIS.
    |Treść||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceName": "resourceName-2",<br>"AccountName": "Luis-uswest-S0-2"}|

    Po pomyślnym wykonaniu tego interfejsu API funkcja zwraca wartość `201 - created status` .

## <a name="unassign-a-resource"></a>Cofnij przypisanie zasobu

1. Zaloguj się do [portalu Luis](https://www.luis.ai), a następnie wybierz aplikację z listy **Moje aplikacje** .
1. Przejdź do obszaru **Zarządzanie**  >  **zasobami platformy Azure**.
1. Na karcie zasób **predykcyjny** lub **Tworzenie zasobu** wybierz przycisk **Cofnij przypisanie zasobu** dla zasobu.

Po odpisaniu zasobu nie jest on usuwany z platformy Azure. Jest on odłączany od LUIS.


## <a name="delete-an-account"></a>Usuwanie konta

Zobacz [magazynowanie i usuwanie danych,](luis-concept-data-storage.md#accounts) Aby uzyskać informacje o tym, jakie dane są usuwane po usunięciu konta.

## <a name="change-the-pricing-tier"></a>Zmiana warstwy cenowej

1.  W [Azure Portal](https://portal.azure.com)Znajdź i wybierz swoją subskrypcję Luis:

    ![Zrzut ekranu pokazujący subskrypcję LUIS w Azure Portal.](./media/luis-usage-tiers/find.png)
1.  Wybierz **warstwę cenową** , aby wyświetlić dostępne warstwy cenowe:

    ![Zrzut ekranu pokazujący element menu Warstwa cenowa.](./media/luis-usage-tiers/subscription.png)
1.  Wybierz warstwę cenową, a następnie kliknij pozycję **Wybierz** , aby zapisać zmiany:

    ![Zrzut ekranu pokazujący sposób wybierania i zapisywania warstwy cenowej.](./media/luis-usage-tiers/plans.png)

    Po zakończeniu zmiany cen okno podręczne weryfikuje aktualizację warstwy cenowej:

    ![Zrzut ekranu przedstawiający okno podręczne, które weryfikuje aktualizację cen.](./media/luis-usage-tiers/updated.png)
1. Należy pamiętać o [przypisaniu tego klucza punktu końcowego](#assign-a-resource-to-an-app) na stronie **publikowania** i używania go we wszystkich zapytaniach punktów końcowych.

## <a name="view-azure-resource-metrics"></a>Wyświetlanie metryk zasobów platformy Azure

### <a name="view-a-summary-of-azure-resource-usage"></a>Wyświetlanie podsumowania użycia zasobów platformy Azure
Informacje o użyciu LUIS można wyświetlić w Azure Portal. Na stronie **Przegląd** są wyświetlane podsumowanie, w tym ostatnie wywołania i błędy. Jeśli wprowadzisz żądanie punktu końcowego LUIS, poczekaj maksymalnie pięć minut na pojawienie się zmiany.

![Zrzut ekranu przedstawiający stronę przegląd.](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Dostosowywanie wykresów użycia zasobów platformy Azure
Strona **metryki** zawiera bardziej szczegółowy widok danych:

![Zrzut ekranu przedstawiający stronę metryki.](./media/luis-usage-tiers/metrics-default.png)

Wykresy metryk można skonfigurować dla określonego okresu i typu metryki:

![Zrzut ekranu pokazujący dostosowany wykres.](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>Alert dotyczący łącznego progu transakcji
Jeśli chcesz wiedzieć, Kiedy osiągniesz określony próg transakcji, na przykład 10 000 transakcji, możesz utworzyć alert:

![Zrzut ekranu przedstawiający stronę reguły alertów.](./media/luis-usage-tiers/alert-default.png)

Dodaj alert dotyczący metryki **łącznej liczby wywołań** przez określony czas. Dodaj adresy e-mail wszystkich osób, które powinny odebrać alert. Dodaj elementy webhook dla wszystkich systemów, które powinny otrzymać Alert. Możesz również uruchomić aplikację logiki, gdy zostanie wyzwolony alert.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, [jak za pomocą wersji](luis-how-to-manage-versions.md) sterować cyklem życia aplikacji.
* Przeprowadź migrację do nowego [zasobu tworzenia](luis-migration-authoring.md).
