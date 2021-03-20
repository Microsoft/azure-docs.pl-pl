---
title: Konfigurowanie ustawień aplikacji funkcji w Azure Functions
description: Dowiedz się, jak skonfigurować ustawienia aplikacji funkcji w Azure Functions.
ms.assetid: 81eb04f8-9a27-45bb-bf24-9ab6c30d205c
ms.topic: conceptual
ms.date: 04/13/2020
ms.custom: cc996988-fb4f-47, devx-track-azurecli
ms.openlocfilehash: 5080d16a7b14506b24e07e2ee4ba862c645f83a8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98875453"
---
# <a name="manage-your-function-app"></a>Zarządzanie aplikacją funkcji 

W Azure Functions aplikacja funkcji udostępnia kontekst wykonywania dla poszczególnych funkcji. Zachowania aplikacji funkcji dotyczą wszystkich funkcji hostowanych przez daną aplikację funkcji. Wszystkie funkcje w aplikacji funkcji muszą być w tym samym [języku](supported-languages.md). 

Poszczególne funkcje w aplikacji funkcji są wdrażane razem i są skalowane jednocześnie. Wszystkie funkcje w tym samym zakresie zasobów funkcji, na wystąpienie, jako aplikacja funkcji skaluje się. 

Parametry połączenia, zmienne środowiskowe i inne ustawienia aplikacji są definiowane osobno dla każdej aplikacji funkcji. Wszelkie dane, które muszą być współużytkowane przez aplikacje funkcji, powinny być przechowywane zewnętrznie w utrwalonym magazynie.

## <a name="get-started-in-the-azure-portal"></a>Rozpoczęcie pracy w witrynie Azure portal

1. Aby rozpocząć, przejdź do [Azure Portal] i zaloguj się na koncie platformy Azure. Na pasku wyszukiwania w górnej części portalu wprowadź nazwę aplikacji funkcji i wybierz ją z listy. 

2. W obszarze **Ustawienia** w okienku po lewej stronie wybierz pozycję **Konfiguracja**.

    :::image type="content" source="./media/functions-how-to-use-azure-function-app-settings/azure-function-app-main.png" alt-text="Przegląd aplikacji funkcji w Azure Portal":::

Możesz przejść do wszystkiego, czego potrzebujesz do zarządzania aplikacją funkcji, na stronie przeglądu, w szczególności z **[ustawieniami aplikacji](#settings)** i **[funkcjami platformy](#platform-features)**.

## <a name="work-with-application-settings"></a><a name="settings"></a>Pracuj z ustawieniami aplikacji

Ustawieniami aplikacji można zarządzać z poziomu [Azure Portal](functions-how-to-use-azure-function-app-settings.md?tabs=portal#settings) i przy użyciu [interfejsu wiersza polecenia platformy Azure](functions-how-to-use-azure-function-app-settings.md?tabs=azurecli#settings) i [Azure PowerShell](functions-how-to-use-azure-function-app-settings.md?tabs=powershell#settings). Można również zarządzać ustawieniami aplikacji z poziomu [Visual Studio Code](functions-develop-vs-code.md#application-settings-in-azure) i programu [Visual Studio](functions-develop-vs.md#function-app-settings). 

Te ustawienia są przechowywane w postaci zaszyfrowanej. Aby dowiedzieć się więcej, zobacz [Ustawienia aplikacji zabezpieczenia](security-concepts.md#application-settings).

# <a name="portal"></a>[Portal](#tab/portal)

Aby znaleźć ustawienia aplikacji, zobacz Rozpoczynanie [pracy w Azure Portal](#get-started-in-the-azure-portal). 

Karta **Ustawienia aplikacji** obsługuje ustawienia, które są używane przez aplikację funkcji. Musisz wybrać **Pokaż wartości** , aby wyświetlić wartości w portalu. Aby dodać ustawienie w portalu, wybierz pozycję **nowe ustawienie aplikacji** i Dodaj nową parę klucz-wartość.

![Ustawienia aplikacji funkcji w Azure Portal.](./media/functions-how-to-use-azure-function-app-settings/azure-function-app-settings-tab.png)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azurecli)

[`az functionapp config appsettings list`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-list)Polecenie zwraca istniejące ustawienia aplikacji, jak w poniższym przykładzie:

```azurecli-interactive
az functionapp config appsettings list --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME>
```

[`az functionapp config appsettings set`](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set)Polecenie dodaje lub aktualizuje ustawienie aplikacji. Poniższy przykład tworzy ustawienie z kluczem o nazwie `CUSTOM_FUNCTION_APP_SETTING` i wartości `12345` :


```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings CUSTOM_FUNCTION_APP_SETTING=12345
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

[`Get-AzFunctionAppSetting`](/powershell/module/az.functions/get-azfunctionappsetting)Polecenie cmdlet zwraca istniejące ustawienia aplikacji, jak w poniższym przykładzie: 

```azurepowershell-interactive
Get-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME>
```

[`Update-AzFunctionAppSetting`](/powershell/module/az.functions/update-azfunctionappsetting)Polecenie dodaje lub aktualizuje ustawienie aplikacji. Poniższy przykład tworzy ustawienie z kluczem o nazwie `CUSTOM_FUNCTION_APP_SETTING` i wartości `12345` :

```azurepowershell-interactive
Update-AzFunctionAppSetting -Name <FUNCTION_APP_NAME> -ResourceGroupName <RESOURCE_GROUP_NAME> -AppSetting @{"CUSTOM_FUNCTION_APP_SETTING" = "12345"}
```

---

### <a name="use-application-settings"></a>Użyj ustawień aplikacji

[!INCLUDE [functions-environment-variables](../../includes/functions-environment-variables.md)]

Podczas lokalnego tworzenia aplikacji funkcji należy zachować lokalne kopie tych wartości w local.settings.jsw pliku projektu. Aby dowiedzieć się więcej, zobacz [plik ustawień lokalnych](functions-run-local.md#local-settings-file).

## <a name="hosting-plan-type"></a>Typ planu hostingu

Podczas tworzenia aplikacji funkcji należy również utworzyć plan hostingu, w którym jest uruchamiana aplikacja. Plan może mieć co najmniej jedną aplikację funkcji. Funkcjonalność, skalowanie i Cennik funkcji zależą od typu planu. Aby dowiedzieć się więcej, zobacz [Azure Functions opcje hostingu](functions-scale.md).

Możesz określić typ planu używanego przez aplikację funkcji z Azure Portal lub przy użyciu interfejsu wiersza polecenia platformy Azure lub interfejsów API Azure PowerShell. 

Następujące wartości wskazują typ planu:

| Typ planu | Portal | Interfejs wiersza polecenia platformy Azure/PowerShell |
| --- | --- | --- |
| [Zużycie](consumption-plan.md) | **Zużycie** | `Dynamic` |
| [Premium](functions-premium-plan.md) | **ElasticPremium** | `ElasticPremium` |
| [Dedykowane (App Service)](dedicated-plan.md) | Poszczególne | Poszczególne |

# <a name="portal"></a>[Portal](#tab/portal)

Aby określić typ planu używany przez aplikację funkcji, zapoznaj się z tematem **App Service plan** na karcie **Przegląd** dla aplikacji funkcji w [Azure Portal](https://portal.azure.com). Aby wyświetlić warstwę cenową, wybierz nazwę **planu App Service**, a następnie wybierz pozycję **Właściwości** w okienku po lewej stronie.

![Wyświetlanie planu skalowania w portalu](./media/functions-scale/function-app-overview-portal.png)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azurecli)

Uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure, aby uzyskać typ planu hostingu:

```azurecli-interactive
functionApp=<FUNCTION_APP_NAME>
resourceGroup=FunctionMonitoringExamples
appServicePlanId=$(az functionapp show --name $functionApp --resource-group $resourceGroup --query appServicePlanId --output tsv)
az appservice plan list --query "[?id=='$appServicePlanId'].sku.tier" --output tsv

```  

W poprzednim przykładzie Zastąp `<RESOURCE_GROUP>` i `<FUNCTION_APP_NAME>` nazwą grupy zasobów i aplikacji funkcji, odpowiednie. 

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Uruchom następujące polecenie Azure PowerShell, aby uzyskać typ planu hostingu:

```azurepowershell-interactive
$FunctionApp = '<FUNCTION_APP_NAME>'
$ResourceGroup = '<RESOURCE_GROUP>'

$PlanID = (Get-AzFunctionApp -ResourceGroupName $ResourceGroup -Name $FunctionApp).AppServicePlan
(Get-AzFunctionAppPlan -Name $PlanID -ResourceGroupName $ResourceGroup).SkuTier
```
W poprzednim przykładzie Zastąp `<RESOURCE_GROUP>` i `<FUNCTION_APP_NAME>` nazwą grupy zasobów i aplikacji funkcji, odpowiednie. 

---

## <a name="plan-migration"></a>Planowanie migracji

Za pomocą poleceń interfejsu wiersza polecenia platformy Azure można migrować aplikację funkcji między planem zużycia a planem Premium w systemie Windows. Poszczególne polecenia zależą od kierunku migracji. Bezpośrednia migracja do dedykowanego planu (App Service) nie jest obecnie obsługiwana.

Ta migracja nie jest obsługiwana w systemie Linux.

### <a name="consumption-to-premium"></a>Użycie do Premium

Aby przeprowadzić migrację z planu zużycia do planu Premium w systemie Windows, wykonaj czynności opisane w poniższej procedurze:

1. Uruchom następujące polecenie, aby utworzyć nowy plan App Service (elastyczna Premium) w tym samym regionie i grupie zasobów co istniejąca aplikacja funkcji.  

    ```azurecli-interactive
    az functionapp plan create --name <NEW_PREMIUM_PLAN_NAME> --resource-group <MY_RESOURCE_GROUP> --location <REGION> --sku EP1
    ```

1. Uruchom następujące polecenie, aby przeprowadzić migrację istniejącej aplikacji funkcji do nowego planu Premium

    ```azurecli-interactive
    az functionapp update --name <MY_APP_NAME> --resource-group <MY_RESOURCE_GROUP> --plan <NEW_PREMIUM_PLAN>
    ```

1. Jeśli nie potrzebujesz już poprzedniego planu aplikacji do użycia, Usuń oryginalny plan aplikacji funkcji po potwierdzeniu, że pomyślnie przeprowadzono migrację do nowej. Uruchom następujące polecenie, aby uzyskać listę wszystkich planów zużycia w grupie zasobów.

    ```azurecli-interactive
    az functionapp plan list --resource-group <MY_RESOURCE_GROUP> --query "[?sku.family=='Y'].{PlanName:name,Sites:numberOfSites}" -o table
    ```

    Możesz bezpiecznie usunąć plan z zerowymi lokacjami, które są migrowane z programu.

1. Uruchom następujące polecenie, aby usunąć przeprowadzono migrację planu zużycia.

    ```azurecli-interactive
    az functionapp plan delete --name <CONSUMPTION_PLAN_NAME> --resource-group <MY_RESOURCE_GROUP>
    ```

### <a name="premium-to-consumption"></a>Premium do zużycia

Aby przeprowadzić migrację z planu Premium do planu zużycia w systemie Windows, wykonaj czynności opisane w poniższej procedurze:

1. Uruchom następujące polecenie, aby utworzyć nową aplikację funkcji (użycie) w tym samym regionie i grupie zasobów co istniejąca aplikacja funkcji. To polecenie powoduje także utworzenie nowego planu zużycia, w którym jest uruchamiana aplikacja funkcji.

    ```azurecli-interactive
    az functionapp create --resource-group <MY_RESOURCE_GROUP> --name <NEW_CONSUMPTION_APP_NAME> --consumption-plan-location <REGION> --runtime dotnet --functions-version 3 --storage-account <STORAGE_NAME>
    ```

1. Uruchom następujące polecenie, aby przeprowadzić migrację istniejącej aplikacji funkcji do nowego planu zużycia.

    ```azurecli-interactive
    az functionapp update --name <MY_APP_NAME> --resource-group <MY_RESOURCE_GROUP> --plan <NEW_CONSUMPTION_PLAN>
    ```

1. Usuń aplikację funkcji utworzoną w kroku 1, ponieważ potrzebujesz tylko planu, który został utworzony w celu uruchomienia istniejącej aplikacji funkcji.

    ```azurecli-interactive
    az functionapp delete --name <NEW_CONSUMPTION_APP_NAME> --resource-group <MY_RESOURCE_GROUP>
    ```

1. Jeśli nie potrzebujesz już poprzedniego planu aplikacji funkcji premium, Usuń oryginalny plan aplikacji funkcji po potwierdzeniu, że pomyślnie przeprowadzono migrację do nowej. Należy pamiętać, że jeśli plan nie zostanie usunięty, nadal będą naliczane opłaty za plan Premium. Uruchom następujące polecenie, aby uzyskać listę wszystkich planów Premium w grupie zasobów.

    ```azurecli-interactive
    az functionapp plan list --resource-group <MY_RESOURCE_GROUP> --query "[?sku.family=='EP'].{PlanName:name,Sites:numberOfSites}" -o table
    ```

1. Uruchom następujące polecenie, aby usunąć plan Premium, z którego wykonano migrację.

    ```azurecli-interactive
    az functionapp plan delete --name <PREMIUM_PLAN> --resource-group <MY_RESOURCE_GROUP>
    ```

## <a name="platform-features"></a>Funkcje platformy

Aplikacje funkcji działają w programie i są obsługiwane przez program, Azure App Service platformę. W związku z tym aplikacje funkcji mają dostęp do większości funkcji platformy hostingu w sieci Web platformy Azure. W lewym okienku znajduje się dostęp do wielu funkcji platformy App Service, których można używać w aplikacjach funkcji. 

> [!NOTE]
> Nie wszystkie funkcje App Service są dostępne, gdy aplikacja funkcji jest uruchamiana w ramach planu hostingu zużycia.

Pozostała część tego artykułu koncentruje się na następujących App Service funkcjach w Azure Portal, które są przydatne w przypadku funkcji:

+ [Edytor App Service](#editor)
+ [Konsola](#console)
+ [Narzędzia zaawansowane (kudu)](#kudu)
+ [Opcje wdrożenia](#deployment)
+ [CORS](#cors)
+ [Authentication](#auth)

Aby uzyskać więcej informacji na temat sposobu pracy z ustawieniami App Service, zobacz [konfigurowanie Azure App Service ustawień](../app-service/configure-common.md).

### <a name="app-service-editor"></a><a name="editor"></a>Edytor App Service

![Edytor App Service](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-appservice-editor.png)

Edytor App Service to zaawansowany edytor w portalu, którego można użyć do modyfikacji plików konfiguracji JSON i plików kodu. Wybranie tej opcji powoduje uruchomienie oddzielnej karty przeglądarki z podstawowym edytorem. Dzięki temu można zintegrować z repozytorium git, uruchamiać i debugować kod oraz modyfikować ustawienia aplikacji funkcji. Ten Edytor zapewnia ulepszone środowisko programistyczne dla funkcji w porównaniu z wbudowanym edytorem funkcji.  

Zalecamy rozważenie opracowywania funkcji na komputerze lokalnym. Podczas tworzenia lokalnie i publikowania na platformie Azure pliki projektu są tylko do odczytu w portalu. Aby dowiedzieć się więcej, zobacz temat [kod i test Azure Functions lokalnie](functions-develop-local.md).

### <a name="console"></a><a name="console"></a>Konsola

![Konsola aplikacji funkcji](./media/functions-how-to-use-azure-function-app-settings/configure-function-console.png)

Konsola w portalu jest idealnym narzędziem deweloperskim, gdy wolisz korzystać z aplikacji funkcji z poziomu wiersza polecenia. Typowe polecenia obejmują Tworzenie katalogów i plików oraz nawigację, a także wykonywanie plików wsadowych i skryptów. 

Podczas programowania lokalnego zalecamy używanie [Azure Functions Core Tools](functions-run-local.md) i [interfejsu wiersza polecenia platformy Azure].

### <a name="advanced-tools-kudu"></a><a name="kudu"></a>Narzędzia zaawansowane (kudu)

![Konfigurowanie kudu](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-kudu.png)

Zaawansowane narzędzia dla App Service (znane również jako kudu) zapewniają dostęp do zaawansowanych funkcji administracyjnych aplikacji funkcji. Z usługi kudu można zarządzać informacjami o systemie, ustawieniami aplikacji, zmiennymi środowiskowymi, rozszerzeniami witryn, nagłówkami HTTP i zmiennymi serwera. Możesz również uruchomić **kudu** , przechodząc do punktu końcowego SCM dla aplikacji funkcji, np. `https://<myfunctionapp>.scm.azurewebsites.net/` 


### <a name="deployment-center"></a><a name="deployment"></a>Centrum wdrażania

W przypadku korzystania z rozwiązania do kontroli źródła w celu opracowywania i konserwowania kodu funkcji centrum wdrażania umożliwia tworzenie i wdrażanie z kontroli źródła. Projekt został skompilowany i wdrożony na platformie Azure podczas wprowadzania aktualizacji. Aby uzyskać więcej informacji, zobacz [technologie wdrażania w Azure Functions](functions-deployment-technologies.md).

### <a name="cross-origin-resource-sharing"></a><a name="cors"></a>Współużytkowanie zasobów między źródłami

Aby zapobiec wykonywaniu złośliwego kodu na kliencie, nowoczesne przeglądarki blokują żądania z aplikacji sieci Web do zasobów uruchomionych w oddzielnej domenie. [Współużytkowanie zasobów między źródłami (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS) umożliwia `Access-Control-Allow-Origin` zadeklarować, które pochodzenia mogą wywoływać punkty końcowe w aplikacji funkcji.

#### <a name="portal"></a>Portal

Po skonfigurowaniu listy **dozwolonych źródeł** dla aplikacji funkcji `Access-Control-Allow-Origin` nagłówek jest automatycznie dodawany do wszystkich odpowiedzi z punktów końcowych HTTP w aplikacji funkcji. 

![Skonfiguruj listę CORS aplikacji funkcji](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-cors.png)

Gdy jest używany symbol wieloznaczny ( `*` ), wszystkie pozostałe domeny zostaną zignorowane. 

Użyj [`az functionapp cors add`](/cli/azure/functionapp/cors#az-functionapp-cors-add) polecenia, aby dodać domenę do listy dozwolonych źródeł. Poniższy przykład dodaje domenę contoso.com:

```azurecli-interactive
az functionapp cors add --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--allowed-origins https://contoso.com
```

Użyj [`az functionapp cors show`](/cli/azure/functionapp/cors#az-functionapp-cors-show) polecenia, aby wyświetlić listę bieżących dozwolonych źródeł.

### <a name="authentication"></a><a name="auth"></a>Authentication

![Konfigurowanie uwierzytelniania dla aplikacji funkcji](./media/functions-how-to-use-azure-function-app-settings/configure-function-app-authentication.png)

Gdy funkcje używają wyzwalacza HTTP, można wymagać, aby wywołania były najpierw uwierzytelniane. App Service obsługuje uwierzytelnianie Azure Active Directory i logowanie się przy użyciu dostawców społecznościowych, takich jak Facebook, Microsoft i Twitter. Aby uzyskać szczegółowe informacje na temat konfigurowania konkretnych dostawców uwierzytelniania, zobacz [Omówienie uwierzytelniania Azure App Service](../app-service/overview-authentication-authorization.md). 


## <a name="next-steps"></a>Następne kroki

+ [Skonfiguruj ustawienia Azure App Service](../app-service/configure-common.md)
+ [Ciągłe wdrażanie dla Azure Functions](functions-continuous-deployment.md)

[Interfejs wiersza polecenia platformy Azure]: /cli/azure/
[Witryna Azure Portal]: https://portal.azure.com
