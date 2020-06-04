---
title: Jak używać kluczy tworzenia i środowiska uruchomieniowego — LUIS
description: Przy pierwszym użyciu Language Understanding (LUIS) nie trzeba tworzyć klucza tworzenia. Jeśli zamierzasz opublikować aplikację, użyj punktu końcowego środowiska uruchomieniowego, aby utworzyć aplikację i przypisać do niej klucz środowiska uruchomieniowego.
services: cognitive-services
ms.topic: how-to
ms.date: 04/06/2020
ms.openlocfilehash: c566e8fe56d19856f5a577e472929b7610497d7c
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84344462"
---
# <a name="create-luis-resources"></a>Tworzenie zasobów LUIS

Zasoby tworzenia i środowiska uruchomieniowego zapewniają uwierzytelnianie w aplikacji LUIS i punkcie końcowym przewidywania.

<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"></a>

Po zalogowaniu się do portalu LUIS możesz kontynuować:

* bezpłatny [klucz wersji próbnej](#trial-key) — udostępniający tworzenie i kilka zapytań dotyczących punktów końcowych przewidywania.
* zasób [tworzenia Luis](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) platformy Azure.

<a name="starter-key"></a>

## <a name="sign-in-to-luis-portal-and-begin-authoring"></a>Zaloguj się do portalu LUIS i zacznij tworzenie

1. Zaloguj się do [portalu Luis](https://www.luis.ai) i zaakceptuj warunki użytkowania.
1. Rozpocznij swoją aplikację LUIS, wybierając typ klucza tworzenia LUIS, który ma być używany: bezpłatny klucz wersji próbnej lub nowy klucz tworzenia usługi Azure LUIS.

    ![Wybierz typ zasobu Language Understanding tworzenia](./media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Po zakończeniu procesu wyboru zasobów [Utwórz nową aplikację](luis-how-to-start-new-app.md#create-new-app-in-luis).

## <a name="trial-key"></a>Klucz wersji próbnej

Zostanie udostępniony klucz wersji próbnej (Starter). Służy jako klucz uwierzytelniania do wysyłania zapytań do środowiska uruchomieniowego punktu końcowego przewidywania, do 1000 zapytań miesięcznie.

Jest ona widoczna na stronie **Ustawienia użytkownika** oraz na stronach **Zarządzanie > zasobów platformy Azure** w portalu Luis.

Gdy wszystko będzie gotowe do opublikowania punktu końcowego przewidywania, [Utwórz](#create-luis-resources) i [Przypisz](#assign-a-resource-to-an-app) klucze środowiska uruchomieniowego tworzenia i przewidywania, aby zastąpić funkcję klucza początkowego.

<a name="create-resources-in-the-azure-portal"></a>


[!INCLUDE [Create LUIS resource](includes/create-luis-resource.md)]

## <a name="create-resources-in-azure-cli"></a>Tworzenie zasobów w interfejsie wiersza polecenia platformy Azure

Utwórz poszczególne zasoby przy użyciu [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) .

Zasób `kind` :

* Projekt`LUIS.Authoring`
* Przewidując`LUIS`

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

## <a name="assign-an-authoring-resource-in-the-luis-portal-for-all-apps"></a>Przypisywanie zasobów autorstwa w portalu LUIS dla wszystkich aplikacji

Można przypisać zasób tworzenia dla jednej aplikacji lub dla wszystkich aplikacji w LUIS. Poniższa procedura przypisuje wszystkie aplikacje do pojedynczego zasobu tworzenia.

1. Zaloguj się do [portalu Luis](https://www.luis.ai).
1. Na górnym pasku nawigacyjnym po prawej stronie wybierz konto użytkownika, a następnie wybierz pozycję **Ustawienia**.
1. Na stronie **Ustawienia użytkownika** wybierz pozycję **Dodaj zasób tworzenia** , a następnie wybierz istniejący zasób tworzenia. Wybierz pozycję **Zapisz**.

## <a name="assign-a-resource-to-an-app"></a>Przypisywanie zasobu do aplikacji

Do aplikacji można przypisać pojedynczy zasób, tworzenie lub przewidywanie środowiska uruchomieniowego punktu końcowego.

1. Zaloguj się do [portalu Luis](https://www.luis.ai), a następnie wybierz aplikację z listy **Moje aplikacje** .
1. Przejdź do strony **zarządzaj > zasobami platformy Azure** .

    ![Wybierz pozycję Zarządzaj > zasobów platformy Azure w portalu LUIS, aby przypisać zasób do aplikacji.](./media/luis-how-to-azure-subscription/manage-azure-resources-prediction.png)

1. Wybierz kartę zasób przewidywania lub tworzenia, a następnie wybierz przycisk **Dodaj zasób predykcyjny** lub **Dodaj zasób tworzenia zasobów** .
1. Wybierz pola w formularzu, aby znaleźć poprawny zasób, a następnie wybierz pozycję **Zapisz**.

### <a name="assign-runtime-resource-without-using-luis-portal"></a>Przypisywanie zasobu środowiska uruchomieniowego bez użycia portalu LUIS

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

    |Typ|Ustawienie|Wartość|
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

## <a name="reset-authoring-key"></a>Resetuj klucz tworzenia

**W przypadku [tworzenia aplikacji migrowanych zasobów](luis-migration-authoring.md) **: Jeśli klucz autorstwa został naruszony, zresetuj klucz w Azure Portal na stronie **klucze** dla tego zasobu tworzenia.

W **przypadku aplikacji, które nie zostały jeszcze zmigrowane**: klucz jest resetowany we wszystkich aplikacjach w portalu Luis. W przypadku tworzenia aplikacji za pośrednictwem interfejsów API tworzenia należy zmienić wartość parametru OCP-APIM-Subscription-Key na nowy klucz.

## <a name="regenerate-azure-key"></a>Wygeneruj ponownie klucz platformy Azure

Wygeneruj ponownie klucze platformy Azure z Azure Portal na stronie **klucze** .

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
* Zapoznaj się z pojęciami dotyczącymi [zasobów tworzenia](luis-concept-keys.md#authoring-key) i [współautorów](luis-concept-keys.md#contributions-from-other-authors) tego zasobu.
* Dowiedz się [, jak tworzyć zasoby dotyczące](luis-how-to-azure-subscription.md) tworzenia i wykonywania
* Migrowanie do nowego [zasobu tworzenia](luis-migration-authoring.md)
