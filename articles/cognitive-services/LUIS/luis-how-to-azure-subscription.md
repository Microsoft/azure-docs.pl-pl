---
title: Jak używać kluczy tworzenia i środowiska uruchomieniowego — LUIS
description: Przy pierwszym użyciu Language Understanding (LUIS) nie trzeba tworzyć klucza tworzenia. Jeśli zamierzasz opublikować aplikację, użyj punktu końcowego środowiska uruchomieniowego, aby utworzyć aplikację i przypisać do niej klucz środowiska uruchomieniowego.
services: cognitive-services
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 09/07/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 949ad4176cc7bf65e07e40323fc72a0a144b53b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91327225"
---
# <a name="create-luis-resources"></a>Tworzenie zasobów LUIS

Tworzenie zasobów środowiska uruchomieniowego i prognozowanie zapytań zapewnia uwierzytelnianie w aplikacji LUIS i punkcie końcowym przewidywania.

<a name="azure-resources-for-luis"></a>
<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

## <a name="luis-resources"></a>Zasoby LUIS

LUIS umożliwia korzystanie z trzech typów zasobów platformy Azure i jednego zasobu niezwiązanego z platformą Azure:

|Zasób|Przeznaczenie|Usługa poznawcze `kind`|Usługa poznawcze `type`|
|--|--|--|--|
|Tworzenie zasobu|Umożliwia tworzenie, uczenie i publikowanie aplikacji oraz zarządzanie nimi. [Utwórz zasób tworzenia Luis](luis-how-to-azure-subscription.md#create-luis-resources-in-azure-portal) , jeśli zamierzasz tworzyć aplikacje Luis programtically lub z portalu Luis. Najpierw należy [przeprowadzić migrację konta usługi Luis](luis-migration-authoring.md#what-is-migration) , aby można było połączyć zasoby Authroring platformy Azure z Twoją aplikacją. Możesz kontrolować uprawnienia do zasobu tworzenia, przypisując osoby do [roli współautor](#contributions-from-other-authors). <br><br> Istnieje jedna warstwa avialable dla zasobu tworzenia LUIS:<br> * **Bezpłatny zasób tworzenia F0** , który daje półroczne transakcje tworzenia i 1000 bezpłatnych żądań punktów końcowych przewidywania testowania. |`LUIS.Authoring`|`Cognitive Services`|
|Zasób predykcyjny| Po opublikowaniu aplikacji LUIS Użyj zasobu predykcyjny/Key do wysyłania zapytań do żądań punktów końcowych przewidywania. Utwórz zasób przewidywania LUIS, zanim aplikacja kliencka zażąda prognoz wykraczających poza żądania 1 000 dostarczone przez tworzenie lub zasób początkowy. <br><br> Istnieją dwie warstwy avialble dla zasobu przewidywania:<br> * **Bezpłatny zasób predykcyjny F0** , który daje miesięcznie 10 000 bezpłatnych żądań punktu końcowego przewidywania<br> * **Standardowy zasób predykcyjny S0** , który jest warstwą płatną. [Dowiedz się więcej na temat szczegółów cennika](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/)|`LUIS`|`Cognitive Services`|
|Zasób Starter/Wersja próbna|Umożliwia tworzenie, uczenie i publikowanie aplikacji oraz zarządzanie nimi. Jest to tworzone przez domyślną w przypadku wybrania opcji zasobów początkowych podczas pierwszej rejestracji w usłudze TP LUIS. Klucz początkowy będzie jednak ostatecznie przestarzały, a wszyscy użytkownicy LUIS będą musieli [migrować swoje konta](luis-migration-authoring.md#what-is-migration) i połączyć ich aplikacje Luis z zasobem tworzenia. Ten zasób nie daje uprawnień do kontroli dostępu opartej na rolach, takiej jak zasób tworzenia. <br><br> Tak samo jak zasób do tworzenia, zasób początkowy umożliwia korzystanie z bezpłatnych transakcji tworzenia i 1000 bezpłatnych żądań dla punktów końcowych przewidywania testów.|-|To nie jest zasób platformy Azure|
|[Klucz zasobu usługi poznawczej — wiele usług](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|Żądania punktu końcowego przewidywania zapytań udostępniane z LUIS i innymi obsługiwanymi Cognitive Services.|`CognitiveServices`|`Cognitive Services`|


> [!Note]
> Istnieją dwa typy zasobów F0 (warstwy Bezpłatna), które zapewnia LUIS. Jeden na potrzeby tworzenia transakcji i jeden dla transakcji predykcyjnych. W przypadku korzystania z bezpłatnego przydziału dla transakcji predykcyjnych upewnij się, że jesteś w rzeczywistości przy użyciu zasobu przewidywania F0, który zapewnia 10 000 bezpłatnych transakcji miesięcznie, a nie do zasobu tworzenia 1000, co oznacza comiesięczne transakcje predykcyjne.

Po zakończeniu procesu tworzenia zasobów platformy Azure [Przypisz zasób](#assign-a-resource-to-an-app) do aplikacji w portalu Luis.

Ważne jest, aby tworzyć aplikacje LUIS w [regionach](luis-reference-regions.md#publishing-regions) , w których chcesz publikować i wysyłać zapytania.

## <a name="resource-ownership"></a>Własność zasobów

Zasób platformy Azure, taki jak LUIS, należy do subskrypcji zawierającej zasób.

Aby przenieść własność zasobu, można:
* Przenieś [własność](../../cost-management-billing/manage/billing-subscription-transfer.md) subskrypcji
* Wyeksportuj aplikację LUIS jako plik, a następnie zaimportuj aplikację w innej subskrypcji. Eksport jest dostępny na stronie **Moje aplikacje** w portalu Luis.


## <a name="resource-limits"></a>Limity zasobów

### <a name="authoring-key-creation-limits"></a>Tworzenie ograniczeń tworzenia kluczy

Można utworzyć maksymalnie 10 kluczy tworzenia na region na subskrypcję.

Zobacz [limity kluczy](luis-limits.md#key-limits) i [regiony platformy Azure](luis-reference-regions.md).

Publikowanie regionów różni się od regionów tworzenia. Upewnij się, że tworzysz aplikację w regionie tworzenia odpowiadającą regionowi publikowania, który ma znajdować się aplikacja kliencka.

### <a name="key-usage-limit-errors"></a>Błędy limitu użycia klucza

Limity użycia są zależne od warstwy cenowej.

W przypadku przekroczenia limitu przydziału transakcji na sekundę (TPS) otrzymasz błąd HTTP 429. W przypadku przekroczenia limitu przydziału transakcji na miesiąc (TPS) otrzymasz błąd HTTP 403.


### <a name="reset-authoring-key"></a>Resetuj klucz tworzenia

W przypadku [tworzenia aplikacji migrowanych zasobów](luis-migration-authoring.md) : Jeśli klucz autorstwa został naruszony, zresetuj klucz w Azure Portal na stronie **klucze** dla tego zasobu tworzenia.

W przypadku aplikacji, które nie zostały jeszcze zmigrowane: klucz jest resetowany we wszystkich aplikacjach w portalu LUIS. W przypadku tworzenia aplikacji za pośrednictwem interfejsów API tworzenia należy zmienić wartość parametru OCP-APIM-Subscription-Key na nowy klucz.

### <a name="regenerate-azure-key"></a>Wygeneruj ponownie klucz platformy Azure

Wygeneruj ponownie klucze platformy Azure z Azure Portal na stronie **klucze** .


<a name="securing-the-endpoint"></a>

## <a name="app-ownership-access-and-security"></a>Własność aplikacji, dostęp i zabezpieczenia

Aplikacja jest definiowana przez zasoby platformy Azure, które są określane przez subskrypcję właściciela.

Możesz przenieść swoją aplikację LUIS. Skorzystaj z następujących zasobów dokumentacji w Azure Portal lub interfejs wiersza polecenia platformy Azure:

* [Przenoszenie aplikacji między zasobami tworzenia LUIS](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Przenieś zasób do nowej grupy zasobów lub subskrypcji](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Przenoszenie zasobu w ramach tej samej subskrypcji lub między subskrypcjami](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)


### <a name="contributions-from-other-authors"></a>Wkłady od innych autorów

W przypadku [tworzenia migrowanych aplikacji zasobów](luis-migration-authoring.md) : _Współautorzy_ są zarządzani w Azure Portal dla zasobu tworzenia przy użyciu strony **Kontrola dostępu (IAM)** . Dowiedz się, [jak dodać użytkownika](luis-how-to-collaborate.md)przy użyciu adresu e-mail współpracownika i roli _współautor_ .

W przypadku aplikacji, które nie zostały jeszcze zmigrowane: Wszyscy _współpracownicy_ są zarządzani w portalu Luis na stronie **Zarządzanie > współpracownikami** .

### <a name="query-prediction-access-for-private-and-public-apps"></a>Dostęp do prognozowania zapytań dla prywatnych i publicznych aplikacji

W przypadku aplikacji **prywatnej** dostęp do środowiska uruchomieniowego przewidywania zapytań jest dostępny dla właścicieli i współautorów. W przypadku aplikacji **publicznej** dostęp do środowiska uruchomieniowego jest dostępny dla wszystkich użytkowników, którzy korzystają z własnej [usługi poznawczej](../cognitive-services-apis-create-account.md) platformy Azure lub zasobu środowiska uruchomieniowego [Luis](#create-resources-in-the-azure-portal) , i mają identyfikator aplikacji publicznej.

Obecnie nie istnieje katalog aplikacji publicznych.

### <a name="authoring-permissions-and-access"></a>Uprawnienia do tworzenia i dostęp
Dostęp do aplikacji z portalu [Luis](luis-reference-regions.md#luis-website) lub [interfejsów API tworzenia](https://go.microsoft.com/fwlink/?linkid=2092087) jest kontrolowany przez zasób tworzenia platformy Azure.

Właściciel i wszyscy Współautorzy mają dostęp do tworzenia aplikacji.

|Dostęp do tworzenia obejmuje|Uwagi|
|--|--|
|Dodaj lub Usuń klucze punktów końcowych||
|Eksportowanie wersji||
|Eksportowanie dzienników punktów końcowych||
|Wersja importowana||
|Tworzenie aplikacji jako publicznej|Gdy aplikacja jest publiczna, każda osoba mająca klucz tworzenia lub punktu końcowego może wysyłać zapytania do aplikacji.|
|Modyfikuj model|
|Opublikuj|
|Zapoznaj się z punktem końcowym wyrażenia długości na potrzeby [aktywnego uczenia](luis-how-to-review-endpoint-utterances.md)|
|Szkolenie|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>Dostęp w czasie wykonywania przewidywania punktu końcowego

Dostęp do zapytania do punktu końcowego przewidywania jest kontrolowany przez ustawienie na stronie **Informacje o aplikacji** w sekcji **Zarządzanie** .

|[Prywatny punkt końcowy](#runtime-security-for-private-apps)|[Publiczny punkt końcowy](#runtime-security-for-public-apps)|
|:--|:--|
|Dostępne dla właściciela i współautorów|Dostępne dla właściciela, współautorów i innych osób, które wiedzą o IDENTYFIKATORze aplikacji|

Można kontrolować, kto widzi klucz środowiska uruchomieniowego LUIS, wywołując go w środowisku serwer-serwer. Jeśli używasz LUIS z bot, połączenie między bot i LUIS jest już bezpieczne. W przypadku bezpośredniego wywoływania punktu końcowego LUIS należy utworzyć interfejs API po stronie serwera (na przykład [funkcję](https://azure.microsoft.com/services/functions/)platformy Azure) z dostępem kontrolowanym (na przykład [AAD](https://azure.microsoft.com/services/active-directory/)). Gdy interfejs API po stronie serwera jest wywoływany i uwierzytelniany, a Autoryzacja jest weryfikowana, należy przekazać wywołanie do LUIS. Chociaż ta strategia nie zapobiega atakom typu man-in-the-Middle, zasłania klucz i adres URL punktu końcowego od użytkowników, umożliwia śledzenie dostępu i umożliwia dodanie rejestrowania odpowiedzi na punkt końcowy (na przykład [Application Insights](https://azure.microsoft.com/services/application-insights/)).

### <a name="runtime-security-for-private-apps"></a>Zabezpieczenia środowiska uruchomieniowego dla prywatnych aplikacji

Środowisko uruchomieniowe aplikacji prywatnej jest dostępne tylko dla następujących:

|Klucz i użytkownik|Objaśnienie|
|--|--|
|Klucz autorstwa właściciela| Do 1000 trafień punktów końcowych|
|Współautorzy/klucze autorstwa| Do 1000 trafień punktów końcowych|
|Dowolny klucz przypisany do LUIS przez autora lub współpracownika/współautora|Na podstawie warstwy użycia klucza|

### <a name="runtime-security-for-public-apps"></a>Zabezpieczenia środowiska uruchomieniowego dla aplikacji publicznych

Gdy aplikacja zostanie skonfigurowana jako publiczna, _dowolny_ prawidłowy klucz Luis Authoring lub klucz punktu końcowego Luis może wysyłać zapytania do aplikacji, o ile klucz nie użył całego limitu przydziału punktów końcowych.

Użytkownik, który nie jest właścicielem lub współautorem, może uzyskać dostęp do środowiska uruchomieniowego aplikacji publicznej tylko wtedy, gdy podano identyfikator aplikacji. LUIS nie ma publicznego _rynku_ ani innego sposobu wyszukiwania aplikacji publicznej.

Publiczna aplikacja jest publikowana we wszystkich regionach, dzięki czemu użytkownik z kluczem zasobów LUIS opartym na regionie może uzyskać dostęp do aplikacji w dowolnym regionie, który jest skojarzony z kluczem zasobu.


### <a name="securing-the-query-prediction-endpoint"></a>Zabezpieczanie punktu końcowego przewidywania zapytań

Można kontrolować, kto może wyświetlać klucz punktu końcowego środowiska uruchomieniowego LUIS prognoz, wywołując go w środowisku serwer-serwer. Jeśli używasz LUIS z bot, połączenie między bot i LUIS jest już bezpieczne. W przypadku bezpośredniego wywoływania punktu końcowego LUIS należy utworzyć interfejs API po stronie serwera (na przykład [funkcję](https://azure.microsoft.com/services/functions/)platformy Azure) z dostępem kontrolowanym (na przykład [AAD](https://azure.microsoft.com/services/active-directory/)). Gdy wywoływany jest interfejs API po stronie serwera, a uwierzytelnianie i autoryzacja są weryfikowane, należy przekazać wywołanie do LUIS. Chociaż ta strategia nie zapobiega atakom typu man-in-the-Middle, zasłania swój punkt końcowy od użytkowników, umożliwia śledzenie dostępu i umożliwia dodanie rejestrowania odpowiedzi na punkt końcowy (na przykład [Application Insights](https://azure.microsoft.com/services/application-insights/)).

<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>Zaloguj się do portalu LUIS i zacznij tworzenie

1. Zaloguj się do [portalu Luis](https://www.luis.ai) i zaakceptuj warunki użytkowania.
1. Rozpocznij swoją aplikację LUIS, wybierając klucz tworzenia LUIS Azure.

   ![Wybierz typ zasobu Language Understanding tworzenia](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Po zakończeniu procesu wyboru zasobów [Utwórz nową aplikację](luis-how-to-start-new-app.md#create-new-app-in-luis).


<a name="create-azure-resources"></a>
<a name="create-resources-in-the-azure-portal"></a>

[!INCLUDE [Create LUIS resource in Azure portal](includes/create-luis-resource.md)]

### <a name="create-resources-in-azure-cli"></a>Tworzenie zasobów w interfejsie wiersza polecenia platformy Azure

Utwórz poszczególne zasoby przy użyciu [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) .

Zasób `kind` :

* Projekt `LUIS.Authoring`
* Przewidując `LUIS`

1. Zaloguj się do interfejsu wiersza polecenia platformy Azure:

    ```azurecli
    az login
    ```

    Spowoduje to otwarcie przeglądarki w celu umożliwienia wybrania odpowiedniego konta i zapewnienia uwierzytelniania.

1. Utwórz **zasób autorstwa Luis**, którego `LUIS.Authoring` nazwa jest określona `my-luis-authoring-resource` w _istniejącej_ grupie zasobów o nazwie `my-resource-group` dla `westus` regionu.

    ```azurecli
    az cognitiveservices account create -n my-luis-authoring-resource -g my-resource-group --kind LUIS.Authoring --sku F0 -l westus --yes
    ```

1. Utwórz **zasób punktu końcowego przewidywania Luis**o `LUIS` nazwie `my-luis-prediction-resource` w _istniejącej_ grupie zasobów o nazwie `my-resource-group` dla `westus` regionu. Jeśli potrzebujesz wyższej przepływności niż warstwa Bezpłatna, Zmień wartość `F0` na `S0` . Dowiedz się więcej o [warstwach cenowych i przepływności](luis-limits.md#key-limits).

    ```azurecli
    az cognitiveservices account create -n my-luis-prediction-resource -g my-resource-group --kind LUIS --sku F0 -l westus --yes
    ```

    > [!Note]
    > Te klucze nie są używane przez portal LUIS, dopóki **nie** zostaną przypisane do portalu Luis w **zasobach platformy Azure zarządzanie >**.

<a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>

### <a name="assign-resource-in-the-luis-portal"></a>Przypisywanie zasobu w portalu LUIS

Można przypisać zasób tworzenia dla jednej aplikacji lub dla wszystkich aplikacji w LUIS. Poniższa procedura przypisuje wszystkie aplikacje do pojedynczego zasobu tworzenia.

1. Zaloguj się do [portalu Luis](https://www.luis.ai).
1. Na górnym pasku nawigacyjnym po prawej stronie wybierz konto użytkownika, a następnie wybierz pozycję **Ustawienia**.
1. Na stronie **Ustawienia użytkownika** wybierz pozycję **Dodaj zasób tworzenia** , a następnie wybierz istniejący zasób tworzenia. Wybierz pozycję **Zapisz**.

## <a name="assign-a-resource-to-an-app"></a>Przypisywanie zasobu do aplikacji

Należy pamiętać, że jeśli nie masz subskrypcji platformy Azure, nie będziesz w stanie przypisywać ani tworzyć nowych zasobów. Musisz najpierw przejść i utworzyć [bezpłatną wersję próbną platformy Azure](https://azure.microsoft.com/en-us/free/) , a następnie powrócić do Luis w celu utworzenia nowego zasobu z poziomu portalu.

Do aplikacji można przypisać lub utworzyć zasób do tworzenia lub przewidywania, wykonując poniższą procedurę:

1. Zaloguj się do [portalu Luis](https://www.luis.ai), a następnie wybierz aplikację z listy **Moje aplikacje**
1. Przejdź do strony **zarządzaj > zasobami platformy Azure**

    ![Wybierz pozycję Zarządzaj > zasobów platformy Azure w portalu LUIS, aby przypisać zasób do aplikacji.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Wybierz kartę zasób przewidywania lub tworzenia, a następnie wybierz przycisk **Dodaj zasób predykcyjny** lub **Dodaj zasób tworzenia zasobów**
1. Wybierz pola w formularzu, aby znaleźć poprawny zasób, a następnie wybierz pozycję **Zapisz** .
1. Jeśli nie masz istniejącego zasobu, możesz go utworzyć, wybierając pozycję "Utwórz nowy zasób LUIS?". w dolnej części okna


### <a name="assign-query-prediction-runtime-resource-without-using-luis-portal"></a>Przypisywanie zasobu środowiska uruchomieniowego przewidywania zapytań bez użycia portalu LUIS

W celach automatyzacji, takich jak potok ciągłej integracji/ciągłego wdrażania, można zautomatyzować przypisanie zasobu środowiska uruchomieniowego LUIS do aplikacji LUIS. Aby to zrobić, należy wykonać następujące czynności:

1. Pobierz token Azure Resource Manager z tej [witryny sieci Web](https://resources.azure.com/api/token?plaintext=true). Token wygasa, dlatego należy od razu korzystać z niego. Żądanie zwraca token Azure Resource Manager.

    ![Token Azure Resource Manager żądania i Odbierz token Azure Resource Manager](./media/luis-manage-keys/get-arm-token.png)

1. Użyj tokenu, aby zażądać zasobów środowiska uruchomieniowego LUIS w różnych subskrypcjach, za pomocą [interfejsu API kont platformy Azure Get Luis](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), do którego Twoje konto użytkownika ma dostęp.

    Ten POST API wymaga następujących ustawień:

    |Header|Wartość|
    |--|--|
    |`Authorization`|Wartość `Authorization` to `Bearer {token}` . Zwróć uwagę, że wartość tokenu musi być poprzedzona słowem `Bearer` i spacją.|
    |`Ocp-Apim-Subscription-Key`|Twój klucz tworzenia.|

    Ten interfejs API zwraca tablicę obiektów JSON subskrypcji LUIS, w tym identyfikator subskrypcji, grupę zasobów i nazwę zasobu, zwracaną jako nazwa konta. Znajdź jeden element w tablicy, który jest zasobem LUIS do przypisania do aplikacji LUIS.

1. Przypisz token do zasobu LUIS za pomocą [konta platformy Azure Luis do](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) interfejsu API aplikacji.

    Ten POST API wymaga następujących ustawień:

    |Type|Ustawienie|Wartość|
    |--|--|--|
    |Header|`Authorization`|Wartość `Authorization` to `Bearer {token}` . Zwróć uwagę, że wartość tokenu musi być poprzedzona słowem `Bearer` i spacją.|
    |Header|`Ocp-Apim-Subscription-Key`|Twój klucz tworzenia.|
    |Header|`Content-type`|`application/json`|
    |Kwerendy|`appid`|Identyfikator aplikacji usługi LUIS.
    |Treść||{"AzureSubscriptionId":"ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceName": "resourceName-2",<br>"AccountName": "Luis-uswest-S0-2"}|

    Po pomyślnym wykonaniu tego interfejsu API zostanie zwrócony stan 201.

## <a name="unassign-resource"></a>Cofnij przypisanie zasobu

1. Zaloguj się do [portalu Luis](https://www.luis.ai), a następnie wybierz aplikację z listy **Moje aplikacje** .
1. Przejdź do strony **zarządzaj > zasobami platformy Azure** .
1. Wybierz kartę zasób przewidywania lub tworzenia, a następnie wybierz przycisk **Cofnij przypisanie zasobu** dla zasobu.

Po odpisaniu zasobu nie jest on usuwany z platformy Azure. Jest on odłączany od LUIS.


## <a name="delete-account"></a>Usuń konto

Zobacz [magazynowanie i usuwanie danych,](luis-concept-data-storage.md#accounts) Aby uzyskać informacje o tym, jakie dane są usuwane po usunięciu konta.

## <a name="change-pricing-tier"></a>Zmiana warstwy cenowej

1.  Na [platformie Azure](https://portal.azure.com)Znajdź swoją subskrypcję usługi Luis. Wybierz subskrypcję usługi LUIS.
    ![Znajdź subskrypcję usługi LUIS](./media/luis-usage-tiers/find.png)
1.  Wybierz pozycję **warstwa cenowa** , aby wyświetlić dostępne warstwy cenowe.
    ![Wyświetl warstwy cenowe](./media/luis-usage-tiers/subscription.png)
1.  Wybierz warstwę cenową, a następnie wybierz pozycję **Wybierz** , aby zapisać zmiany.
    ![Zmień warstwę płatności LUIS](./media/luis-usage-tiers/plans.png)
1.  Po zakończeniu zmiany cen okno podręczne weryfikuje nową warstwę cenową.
    ![Weryfikowanie warstwy płatności LUIS](./media/luis-usage-tiers/updated.png)
1. Należy pamiętać o [przypisaniu tego klucza punktu końcowego](#assign-a-resource-to-an-app) na stronie **publikowania** i używania go we wszystkich zapytaniach punktów końcowych.

## <a name="viewing-azure-resource-metrics"></a>Wyświetlanie metryk zasobów platformy Azure

### <a name="viewing-azure-resource-summary-usage"></a>Wyświetlanie użycia podsumowania zasobów platformy Azure
Możesz wyświetlić informacje o użyciu LUIS na platformie Azure. Na stronie **Przegląd** są wyświetlane najnowsze informacje podsumowujące, w tym wywołania i błędy. Jeśli wprowadzisz żądanie punktu końcowego LUIS, natychmiast Obejrzyj **stronę przeglądu**, aby umożliwić wyświetlenie użycia do 5 minut.

![Wyświetlanie podsumowania użycia](./media/luis-usage-tiers/overview.png)

### <a name="customizing-azure-resource-usage-charts"></a>Dostosowywanie wykresów użycia zasobów platformy Azure
Metryki zapewniają bardziej szczegółowy wgląd w dane.

![Metryki domyślne](./media/luis-usage-tiers/metrics-default.png)

Wykresy metryk można skonfigurować dla okresu czasu i typu metryki.

![Metryki niestandardowe](./media/luis-usage-tiers/metrics-custom.png)

### <a name="total-transactions-threshold-alert"></a>Alert dotyczący łącznego progu transakcji
Jeśli chcesz się dowiedzieć, gdy osiągnięto określony próg transakcji, na przykład 10 000 transakcji, możesz utworzyć alert.

![Alerty domyślne](./media/luis-usage-tiers/alert-default.png)

Dodaj alert dotyczący metryki **łącznej liczby wywołań** przez określony czas. Dodaj adresy e-mail wszystkich osób, które powinny odebrać alert. Dodaj elementy webhook dla wszystkich systemów, które powinny otrzymać Alert. Możesz również uruchomić aplikację logiki, gdy zostanie wyzwolony alert.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, [jak za pomocą wersji](luis-how-to-manage-versions.md) sterować cyklem życia aplikacji.
* Migrowanie do nowego [zasobu tworzenia](luis-migration-authoring.md)
