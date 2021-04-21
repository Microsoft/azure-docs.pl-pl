---
title: Konfigurowanie ustawień aplikacji funkcji w Azure Functions
description: Dowiedz się, jak skonfigurować ustawienia aplikacji funkcji w Azure Functions.
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.topic: conceptual
ms.date: 04/13/2020
ms.custom: cc996988-fb4f-47, devx-track-azurecli
ms.openlocfilehash: ed87a5a744defb15d4a898aeabdce5267b7431fe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107775659"
---
# <a name="manage-your-function-app"></a>Zarządzanie aplikacją funkcji 

W Azure Functions aplikacja funkcji udostępnia kontekst wykonywania dla poszczególnych funkcji. Zachowania aplikacji funkcji mają zastosowanie do wszystkich funkcji hostowanych przez tę aplikację funkcji. Wszystkie funkcje w aplikacji funkcji muszą być w tym samym [języku.](supported-languages.md) 

Poszczególne funkcje w aplikacji funkcji są wdrażane razem i skalowane razem. Wszystkie funkcje w tej samej aplikacji funkcji współdzielą zasoby na wystąpienie w czasie skalowania aplikacji funkcji. 

Parametry połączenia, zmienne środowiskowe i inne ustawienia aplikacji są definiowane oddzielnie dla każdej aplikacji funkcji. Wszystkie dane, które muszą być współużytkcyjne między aplikacjami funkcji, powinny być przechowywane zewnętrznie w trwałym magazynie.

## <a name="get-started-in-the-azure-portal"></a>Rozpoczęcie pracy w witrynie Azure portal

1. Aby rozpocząć, przejdź do [Azure Portal] i zaloguj się do konta platformy Azure. Na pasku wyszukiwania w górnej części portalu wprowadź nazwę aplikacji funkcji i wybierz ją z listy. 

2. W **obszarze Ustawienia** w okienku po lewej stronie wybierz pozycję **Konfiguracja**.

    :::image type="content" source="./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png" alt-text="Omówienie aplikacji funkcji w Azure Portal":::

Na stronie przeglądu możesz przejść do wszystkiego, czego potrzebujesz **[](#settings)** do zarządzania aplikacją funkcji, a w szczególności do ustawień aplikacji **[i funkcji platformy](#platform-features)**.

## <a name="work-with-application-settings"></a><a name="settings"></a>Praca z ustawieniami aplikacji

Ustawieniami aplikacji można zarządzać za pomocą [Azure Portal](functions-how-to-use-azure-function-app-settings.md?tabs=portal#settings) oraz interfejsu wiersza [polecenia platformy Azure](functions-how-to-use-azure-function-app-settings.md?tabs=azurecli#settings) [Azure PowerShell](functions-how-to-use-azure-function-app-settings.md?tabs=powershell#settings). Ustawieniami aplikacji można również [zarządzać](functions-develop-vs-code.md#application-settings-in-azure) z Visual Studio Code i [z Visual Studio](functions-develop-vs.md#function-app-settings). 

Te ustawienia są przechowywane w postaci zaszyfrowanej. Aby dowiedzieć się więcej, zobacz [Zabezpieczenia ustawień aplikacji](security-concepts.md#application-settings).

# <a name="portal"></a>[Portal](#tab/portal)

Aby znaleźć ustawienia aplikacji, zobacz [Wprowadzenie w Azure Portal](#get-started-in-the-azure-portal). 

Na **karcie Ustawienia** aplikacji są zachowywane ustawienia używane przez aplikację funkcji. Musisz wybrać pozycję **Pokaż wartości,** aby wyświetlić wartości w portalu. Aby dodać ustawienie w portalu, wybierz pozycję **Nowe ustawienie aplikacji** i dodaj nową parę klucz-wartość.

![Ustawienia aplikacji funkcji w Azure Portal.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azurecli)

Polecenie [`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_list) zwraca istniejące ustawienia aplikacji, jak w poniższym przykładzie:

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

Polecenie [`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set) dodaje lub aktualizuje ustawienie aplikacji. Poniższy przykład tworzy ustawienie z kluczem o nazwie `CUSTOM_FUNCTION_APP_SETTING` i `12345` wartością :


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Polecenie [`Get-AzFunctionAppSetting`](/powershell/module/az.functions/get-azfunctionappsetting) cmdlet zwraca istniejące ustawienia aplikacji, jak w poniższym przykładzie: 

```azurepowershell-interactive
Get-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME>
```

Polecenie [`Update-AzFunctionAppSetting`](/powershell/module/az.functions/update-azfunctionappsetting) dodaje lub aktualizuje ustawienie aplikacji. Poniższy przykład tworzy ustawienie z kluczem o nazwie `CUSTOM_FUNCTION_APP_SETTING` i `12345` wartością :

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"CUSTOM_FUNCTION_APP_SETTING" = "12345"}
```

---

### <a name="use-application-settings"></a>Korzystanie z ustawień aplikacji

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

Podczas tworzenia aplikacji funkcji lokalnie należy zachować lokalne kopie tych wartości w local.settings.jspliku projektu. Aby dowiedzieć się więcej, zobacz [Plik ustawień lokalnych](functions-run-local.md#local-settings-file).

## <a name="hosting-plan-type"></a>Typ planu hostingu

Podczas tworzenia aplikacji funkcji tworzysz również plan hostingu, w którym jest uruchamiana aplikacja. Plan może mieć jedną lub więcej aplikacji funkcji. Funkcjonalność, skalowanie i ceny funkcji zależą od typu planu. Aby dowiedzieć się więcej, [zobacz Azure Functions hostingu.](functions-scale.md)

Możesz określić typ planu używanego przez aplikację funkcji z witryny Azure Portal lub przy użyciu interfejsu wiersza polecenia platformy Azure Azure PowerShell API. 

Następujące wartości wskazują typ planu:

| Typ planu | Portal | Interfejs wiersza polecenia platformy Azure/program PowerShell |
| --- | --- | --- |
| [Zużycie](consumption-plan.md) | **Zużycie** | `Dynamic` |
| [Premium](functions-premium-plan.md) | **ElasticPremium** | `ElasticPremium` |
| [Dedykowane (App Service)](dedicated-plan.md) | Różnych | Różnych |

# <a name="portal"></a>[Portal](#tab/portal)

Aby określić typ planu używanego przez aplikację funkcji, zobacz  App Service **na** karcie Przegląd aplikacji funkcji w Azure Portal [.](https://portal.azure.com) Aby wyświetlić warstwę cenową, wybierz nazwę planu **App Service,** a następnie wybierz pozycję **Właściwości** w okienku po lewej stronie.

![Wyświetlanie planu skalowania w portalu](./media/functions-scale/function-app-overview-portal.png)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azurecli)

Uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure, aby uzyskać typ planu hostingu:

```azurecli-interactive
functionApp=<FUNCTION_APP_NAME>
resourceGroup=FunctionMonitoringExamples
appServicePlanId=$(az functionapp show --name $functionApp --resource-group $resourceGroup --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv

```  

W poprzednim przykładzie zastąp i odpowiednimi nazwami grup zasobów i `<RESOURCE_GROUP>` `<FUNCTION_APP_NAME>` aplikacji funkcji. 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Uruchom następujące polecenie Azure PowerShell, aby uzyskać typ planu hostingu:

```azurepowershell-interactive
$FunctionApp = '<FUNCTION_APP_NAME>'
$ResourceGroup = '<RESOURCE_GROUP>'

$PlanID = (Get-AzFunctionApp -ResourceGroupName $ResourceGroup -Name $FunctionApp).AppServicePlan
(Get-AzFunctionAppPlan -Name $PlanID -ResourceGroupName $ResourceGroup).SkuTier
```
W poprzednim przykładzie zastąp i odpowiednimi nazwami grup zasobów i `<RESOURCE_GROUP>` `<FUNCTION_APP_NAME>` aplikacji funkcji. 

---

## <a name="plan-migration"></a>Planowanie migracji

Za pomocą poleceń interfejsu wiersza polecenia platformy Azure można migrować aplikację funkcji między planem Zużycie a planem Premium w systemie Windows. Konkretne polecenia zależą od kierunku migracji. Bezpośrednia migracja do planu dedykowanego (App Service) nie jest obecnie obsługiwana.

Ta migracja nie jest obsługiwana w systemie Linux.

### <a name="consumption-to-premium"></a>Zużycie do premium

Użyj poniższej procedury, aby przeprowadzić migrację z planu Zużycie do planu Premium w systemie Windows:

1. Uruchom następujące polecenie, aby utworzyć nowy plan App Service (Elastic Premium) w tym samym regionie i grupie zasobów co istniejąca aplikacja funkcji.  

    ```azurecli-interactive
    az functionapp plan create --name <NEW_PREMIUM_PLAN_NAME> --resource-group <MY_RESOURCE_GROUP> --location <REGION> --sku EP1
    ```

1. Uruchom następujące polecenie, aby przeprowadzić migrację istniejącej aplikacji funkcji do nowego planu Premium

    ```azurecli-interactive
    az functionapp update --name <MY_APP_NAME> --resource-group <MY_RESOURCE_GROUP> --plan <NEW_PREMIUM_PLAN>
    ```

1. Jeśli nie potrzebujesz już poprzedniego planu aplikacji funkcji Zużycie, usuń oryginalny plan aplikacji funkcji po potwierdzeniu, że migracja do nowego planu została pomyślnie zmigrowana. Uruchom następujące polecenie, aby uzyskać listę wszystkich planów Użycia w grupie zasobów.

    ```azurecli-interactive
    az functionapp plan list --resource-group <MY_RESOURCE_GROUP> --query "[?sku.family=='Y'].{PlanName:name,Sites:numberOfSites}" -o table
    ```

    Możesz bezpiecznie usunąć plan bez lokacji, z której przeprowadzasz migrację.

1. Uruchom następujące polecenie, aby usunąć zmigrowany plan zużycie.

    ```azurecli-interactive
    az functionapp plan delete --name <CONSUMPTION_PLAN_NAME> --resource-group <MY_RESOURCE_GROUP>
    ```

### <a name="premium-to-consumption"></a>Premium do użycia

Aby przeprowadzić migrację z planu Premium do planu Zużycie w systemie Windows, użyj następującej procedury:

1. Uruchom następujące polecenie, aby utworzyć nową aplikację funkcji (Zużycie) w tym samym regionie i grupie zasobów co istniejąca aplikacja funkcji. To polecenie tworzy również nowy plan Zużycie, w którym działa aplikacja funkcji.

    ```azurecli-interactive
    az functionapp create --resource-group <MY_RESOURCE_GROUP> --name <NEW_CONSUMPTION_APP_NAME> --consumption-plan-location <REGION> --runtime dotnet --functions-version 3 --storage-account <STORAGE_NAME>
    ```

1. Uruchom następujące polecenie, aby przeprowadzić migrację istniejącej aplikacji funkcji do nowego planu Zużycie.

    ```azurecli-interactive
    az functionapp update --name <MY_APP_NAME> --resource-group <MY_RESOURCE_GROUP> --plan <NEW_CONSUMPTION_PLAN>
    ```

1. Usuń aplikację funkcji utworzoną w kroku 1, ponieważ potrzebujesz tylko planu, który został utworzony do uruchomienia istniejącej aplikacji funkcji.

    ```azurecli-interactive
    az functionapp delete --name <NEW_CONSUMPTION_APP_NAME> --resource-group <MY_RESOURCE_GROUP>
    ```

1. Jeśli nie potrzebujesz już poprzedniego planu aplikacji funkcji Premium, usuń oryginalny plan aplikacji funkcji po potwierdzeniu, że migracja do nowego planu została pomyślnie zmigrowana. Pamiętaj, że jeśli plan nie zostanie usunięty, nadal będą naliczane opłaty za plan Premium. Uruchom następujące polecenie, aby uzyskać listę wszystkich planów Premium w grupie zasobów.

    ```azurecli-interactive
    az functionapp plan list --resource-group <MY_RESOURCE_GROUP> --query "[?sku.family=='EP'].{PlanName:name,Sites:numberOfSites}" -o table
    ```

1. Uruchom następujące polecenie, aby usunąć zmigrowany plan Premium.

    ```azurecli-interactive
    az functionapp plan delete --name <PREMIUM_PLAN> --resource-group <MY_RESOURCE_GROUP>
    ```

## <a name="platform-features"></a>Funkcje platformy

Aplikacje funkcji działają na platformie Azure App Service i są przez nie utrzymywane. W związku z tym aplikacje funkcji mają dostęp do większości funkcji podstawowej platformy hostingu internetowego platformy Azure. W okienku po lewej stronie można uzyskać dostęp do wielu funkcji platformy App Service, których można używać w aplikacjach funkcji. 

> [!NOTE]
> Nie wszystkie App Service są dostępne, gdy aplikacja funkcji jest uruchamiana w planie hostingu Zużycie.

W pozostałej części tego artykułu skupiono się na następujących App Service funkcji w Azure Portal, które są przydatne w przypadku funkcji:

+ [App Service edytora](#editor)
+ [Konsola](#console)
+ [Narzędzia zaawansowane (Kudu)](#kudu)
+ [Opcje wdrożenia](#deployment)
+ [CORS](#cors)
+ [Authentication](#auth)

Aby uzyskać więcej informacji na temat pracy z ustawieniami App Service, zobacz [Konfigurowanie Azure App Service ustawień.](../app-service/configure-common.md)

### <a name="app-service-editor"></a><a name="editor"></a>App Service edytora

![Edytor App Service danych](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

Edytor App Service to zaawansowany edytor w portalu, który umożliwia modyfikowanie plików konfiguracji JSON i plików kodu. Wybranie tej opcji powoduje uruchomienie oddzielnej karty przeglądarki z edytorem podstawowym. Umożliwia to integrację z repozytorium Git, uruchamianie i debugowanie kodu oraz modyfikowanie ustawień aplikacji funkcji. Ten edytor udostępnia ulepszone środowisko programowe dla funkcji w porównaniu z wbudowanym edytorem funkcji.  

Zalecamy rozważenie opracowania funkcji na komputerze lokalnym. Podczas tworzenia aplikacji lokalnie i publikowania na platformie Azure pliki projektu są tylko do odczytu w portalu. Aby dowiedzieć się więcej, zobacz [Kod i testowanie Azure Functions lokalnie.](functions-develop-local.md)

### <a name="console"></a><a name="console"></a>Konsola

![Konsola aplikacji funkcji](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

Konsola w portalu jest idealnym narzędziem dewelopera, gdy wolisz korzystać z aplikacji funkcji z wiersza polecenia. Typowe polecenia obejmują tworzenie katalogów i plików oraz nawigację, a także wykonywanie plików wsadowych i skryptów. 

Podczas tworzenia aplikacji lokalnie zalecamy używanie interfejsu [Azure Functions Core Tools](functions-run-local.md) i interfejsu wiersza [polecenia platformy Azure.]

### <a name="advanced-tools-kudu"></a><a name="kudu"></a>Narzędzia zaawansowane (Kudu)

![Konfigurowanie usługi Kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

Zaawansowane narzędzia dla App Service (znane również jako Kudu) zapewniają dostęp do zaawansowanych funkcji administracyjnych aplikacji funkcji. Z usługi Kudu można zarządzać informacjami o systemie, ustawieniami aplikacji, zmiennymi środowiskowym, rozszerzeniami witryn, nagłówkami HTTP i zmiennymi serwera. Możesz również uruchomić aplikację **Kudu,** przeglądając punkt końcowy SCM dla aplikacji funkcji, na przykład `https://<myfunctionapp>.scm.azurewebsites.net/` 


### <a name="deployment-center"></a><a name="deployment"></a>Centrum wdrażania

Gdy używasz rozwiązania kontroli źródła do opracowywania i obsługi kodu funkcji, Centrum wdrażania umożliwia kompilowanie i wdrażanie z kontroli źródła. Projekt jest budowany i wdrażany na platformie Azure podczas aktualizacji. Aby uzyskać więcej informacji, zobacz [Technologie wdrażania w programie Azure Functions](functions-deployment-technologies.md).

### <a name="cross-origin-resource-sharing"></a><a name="cors"></a>Współużytkowanie zasobów między źródłami

Aby zapobiec wykonywaniu złośliwego kodu na kliencie, nowoczesne przeglądarki blokują żądania z aplikacji internetowych do zasobów uruchomionych w oddzielnej domenie. [Współużytkowanie zasobów między źródłami (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS) umożliwia nagłówkowi zadeklarowanie, które źródła mogą wywołać `Access-Control-Allow-Origin` punkty końcowe w aplikacji funkcji.

#### <a name="portal"></a>Portal

Po skonfigurowaniu listy **Dozwolone źródła** dla aplikacji funkcji nagłówek jest automatycznie dodawany do wszystkich odpowiedzi z punktów końcowych HTTP w `Access-Control-Allow-Origin` aplikacji funkcji. 

![Konfigurowanie listy CORS aplikacji funkcji](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

Gdy używany jest symbol `*` wieloznaczny ( ), wszystkie inne domeny są ignorowane. 

Użyj polecenia [`az functionapp cors add`](/cli/azure/functionapp/cors#az_functionapp_cors_add) , aby dodać domenę do listy dozwolonych źródeł. W poniższym przykładzie dodano contoso.com domeny:

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

Użyj polecenia [`az functionapp cors show`](/cli/azure/functionapp/cors#az_functionapp_cors_show) , aby wyświetlić listę bieżących dozwolonych źródeł.

### <a name="authentication"></a><a name="auth"></a>Uwierzytelniania

![Konfigurowanie uwierzytelniania dla aplikacji funkcji](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

Gdy funkcje korzystają z wyzwalacza HTTP, możesz wymagać, aby wywołania najpierw zostały uwierzytelnione. App Service obsługuje Azure Active Directory uwierzytelniania i logowania u dostawców sieci społecznościowych, takich jak Facebook, Microsoft i Twitter. Aby uzyskać szczegółowe informacje na temat konfigurowania określonych dostawców uwierzytelniania, [zobacz Azure App Service omówienie uwierzytelniania.](../app-service/overview-authentication-authorization.md) 


## <a name="next-steps"></a>Następne kroki

+ [Konfigurowanie Azure App Service konfiguracji](../app-service/configure-common.md)
+ [Ciągłe wdrażanie dla usługi Azure Functions](functions-continuous-deployment.md)

[Interfejs wiersza polecenia platformy Azure]: /cli/azure/
[Witryna Azure Portal]: https://portal.azure.com
