---
title: Tożsamości zarządzane
description: Dowiedz się, jak zarządzane tożsamości działają w Azure App Service i Azure Functions, jak skonfigurować tożsamość zarządzaną i wygenerować token dla zasobu zaplecza.
author: mattchenderson
ms.topic: article
ms.date: 05/27/2020
ms.author: mahender
ms.reviewer: yevbronsh
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 8e1f7bb2faaf9d2c706e63ae73b9e265eb95d09b
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007215"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Jak używać tożsamości zarządzanych do App Service i Azure Functions

W tym temacie pokazano, jak utworzyć zarządzaną tożsamość dla App Service i Azure Functions aplikacji oraz jak używać jej do uzyskiwania dostępu do innych zasobów. 

> [!Important] 
> Tożsamości zarządzane dla App Service i Azure Functions nie będą działać zgodnie z oczekiwaniami, jeśli Twoja aplikacja jest migrowana między subskrypcjami/dzierżawcami. Aplikacja musi uzyskać nową tożsamość, która jest wykonywana przez wyłączenie i ponowne włączenie tej funkcji. Zobacz [usuwanie tożsamości](#remove) poniżej. Zasoby podrzędne muszą także mieć zaktualizowane zasady dostępu do korzystania z nowej tożsamości.

[!INCLUDE [app-service-managed-identities](../../includes/app-service-managed-identities.md)]

## <a name="add-a-system-assigned-identity"></a>Dodawanie tożsamości przypisanej do systemu

Utworzenie aplikacji z tożsamością przypisaną przez system wymaga, aby w aplikacji była ustawiona dodatkowa właściwość.

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Aby skonfigurować tożsamość zarządzaną w portalu, musisz najpierw utworzyć aplikację w zwykły sposób, a następnie włączyć tę funkcję.

1. Utwórz aplikację w portalu, jak zwykle. Przejdź do niej w portalu.

2. W przypadku korzystania z aplikacji funkcji przejdź do **opcji funkcje platformy**. W przypadku innych typów aplikacji przewiń w dół do grupy **ustawień** w okienku nawigacji po lewej stronie.

3. Wybierz pozycję **tożsamość**.

4. W ramach karty **przypisanej do systemu** Przełącz pozycję **stan** na wartość **włączone**. Kliknij pozycję **Zapisz**.

    ![Zrzut ekranu pokazujący lokalizację, w której ma zostać przełączony stan, a następnie wybierz pozycję Zapisz.](media/app-service-managed-service-identity/system-assigned-managed-identity-in-azure-portal.png)


> [!NOTE] 
> Aby znaleźć tożsamość zarządzaną aplikacji sieci Web lub aplikacji w gnieździe w Azure Portal, w obszarze **aplikacje dla przedsiębiorstw**, zapoznaj się z sekcją **Ustawienia użytkownika** . Zwykle nazwa gniazda jest podobna do `<app name>/slots/<slot name>` .


### <a name="using-the-azure-cli"></a>Przy użyciu interfejsu wiersza polecenia platformy Azure

Aby skonfigurować tożsamość zarządzaną za pomocą interfejsu wiersza polecenia platformy Azure, musisz użyć polecenia w `az webapp identity assign` stosunku do istniejącej aplikacji. Dostępne są trzy opcje uruchamiania przykładów w tej sekcji:

- Użyj [Azure Cloud Shell](../cloud-shell/overview.md) z Azure Portal.
- Użyj osadzonego Azure Cloud Shell za pomocą przycisku "Wypróbuj go" znajdującego się w prawym górnym rogu każdego bloku kodu poniżej.
- [Zainstaluj najnowszą wersję interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) (2.0.31 lub nowsza), jeśli wolisz korzystać z lokalnej konsoli interfejsu wiersza polecenia. 

Poniższe kroki przeprowadzą Cię przez proces tworzenia aplikacji sieci Web i przypisywania jej tożsamości przy użyciu interfejsu wiersza polecenia:

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure w konsoli lokalnej, najpierw zaloguj się do platformy Azure za pomocą polecenia [az login](/cli/azure/reference-index#az-login). Użyj konta skojarzonego z subskrypcją platformy Azure, w ramach której chcesz wdrożyć aplikację:

    ```azurecli-interactive
    az login
    ```

2. Utwórz aplikację sieci Web przy użyciu interfejsu wiersza polecenia. Aby uzyskać więcej przykładów użycia interfejsu wiersza polecenia w App Service, zobacz [App Service przykłady interfejsu wiersza polecenia](../app-service/samples-cli.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. Uruchom `identity assign` polecenie, aby utworzyć tożsamość dla tej aplikacji:

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Poniższe kroki przeprowadzą Cię przez proces tworzenia aplikacji i przypisywania jej tożsamości przy użyciu Azure PowerShell. Instrukcje dotyczące tworzenia aplikacji sieci Web i aplikacji funkcji są różne.

#### <a name="using-azure-powershell-for-a-web-app"></a>Korzystanie z Azure PowerShell aplikacji sieci Web

1. W razie potrzeby zainstaluj Azure PowerShell przy użyciu instrukcji znajdujących się w [przewodniku Azure PowerShell](/powershell/azure/), a następnie uruchom polecenie, `Login-AzAccount` Aby utworzyć połączenie z platformą Azure.

2. Utwórz aplikację sieci Web przy użyciu Azure PowerShell. Aby uzyskać więcej przykładów użycia Azure PowerShell z App Service, zobacz [App Service przykładów programu PowerShell](../app-service/samples-powershell.md):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $resourceGroupName -Tier Free

    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $resourceGroupName
    ```

3. Uruchom `Set-AzWebApp -AssignIdentity` polecenie, aby utworzyć tożsamość dla tej aplikacji:

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName $resourceGroupName 
    ```

#### <a name="using-azure-powershell-for-a-function-app"></a>Używanie Azure PowerShell dla aplikacji funkcji

1. W razie potrzeby zainstaluj Azure PowerShell przy użyciu instrukcji znajdujących się w [przewodniku Azure PowerShell](/powershell/azure/), a następnie uruchom polecenie, `Login-AzAccount` Aby utworzyć połączenie z platformą Azure.

2. Utwórz aplikację funkcji przy użyciu Azure PowerShell. Aby uzyskać więcej przykładów użycia Azure PowerShell z Azure Functions, zobacz [AZ. Functions Reference](/powershell/module/az.functions/#functions):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account.
    New-AzStorageAccount -Name $storageAccountName -ResourceGroupName $resourceGroupName -SkuName $sku

    # Create a function app with a system-assigned identity.
    New-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -Location $location -StorageAccountName $storageAccountName -Runtime $runtime -IdentityType SystemAssigned
    ```

Możesz również zaktualizować istniejącą aplikację funkcji przy użyciu polecenia `Update-AzFunctionApp` .

### <a name="using-an-azure-resource-manager-template"></a>Korzystanie z szablonu Azure Resource Manager

Szablon Azure Resource Manager może służyć do automatyzowania wdrażania zasobów platformy Azure. Aby dowiedzieć się więcej o wdrażaniu do App Service i funkcji, zobacz [Automatyzowanie wdrażania zasobów w App Service](../app-service/deploy-complex-application-predictably.md) i [Automatyzowanie wdrażania zasobów w Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Każdy zasób typu `Microsoft.Web/sites` można utworzyć za pomocą tożsamości, dołączając następującą właściwość w definicji zasobu:

```json
"identity": {
    "type": "SystemAssigned"
}
```

> [!NOTE]
> Aplikacja może mieć w tym samym czasie zarówno tożsamość przypisana do systemu, jak i przypisanych do użytkownika. W tym przypadku właściwość będzie `type``SystemAssigned,UserAssigned`

Dodanie typu przypisanego do systemu informuje platformę Azure, aby utworzył tożsamość aplikacji i zarządzać nią.

Na przykład aplikacja sieci Web może wyglądać następująco:

```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
    ]
}
```

Gdy witryna zostanie utworzona, ma następujące dodatkowe właściwości:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

Właściwość tenantId identyfikuje dzierżawę usługi Azure AD, do której należy tożsamość. PrincipalId jest unikatowym identyfikatorem nowej tożsamości aplikacji. W usłudze Azure AD nazwa główna usługi ma taką samą nazwę, która została nadana App Service lub wystąpieniu Azure Functions.

Jeśli musisz odwołać się do tych właściwości w późniejszym etapie szablonu, możesz to zrobić za pośrednictwem [ `reference()` funkcji szablonu](../azure-resource-manager/templates/template-functions-resource.md#reference) z `'Full'` flagą, jak w poniższym przykładzie:

```json
{
    "tenantId": "[reference(resourceId('Microsoft.Web/sites', variables('appName')), '2018-02-01', 'Full').identity.tenantId]",
    "objectId": "[reference(resourceId('Microsoft.Web/sites', variables('appName')), '2018-02-01', 'Full').identity.principalId]",
}
```

## <a name="add-a-user-assigned-identity"></a>Dodawanie tożsamości przypisanej do użytkownika

Utworzenie aplikacji z tożsamością przypisaną przez użytkownika wymaga utworzenia tożsamości, a następnie dodania jej identyfikatora zasobu do konfiguracji aplikacji.

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Najpierw należy utworzyć zasób tożsamości przypisany przez użytkownika.

1. Utwórz zasób tożsamości zarządzanej przez użytkownika zgodnie z [tymi instrukcjami](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Utwórz aplikację w portalu, jak zwykle. Przejdź do niej w portalu.

3. W przypadku korzystania z aplikacji funkcji przejdź do **opcji funkcje platformy**. W przypadku innych typów aplikacji przewiń w dół do grupy **ustawień** w okienku nawigacji po lewej stronie.

4. Wybierz pozycję **tożsamość**.

5. Na karcie **przypisane przez użytkownika** kliknij przycisk **Dodaj**.

6. Wyszukaj utworzoną wcześniej tożsamość i wybierz ją. Kliknij pozycję **Dodaj**.

    ![Tożsamość zarządzana w App Service](media/app-service-managed-service-identity/user-assigned-managed-identity-in-azure-portal.png)

### <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Poniższe kroki przeprowadzą Cię przez proces tworzenia aplikacji i przypisywania jej tożsamości przy użyciu Azure PowerShell.

> [!NOTE]
> Bieżąca wersja Azure PowerShell polecenia cmdlet dla Azure App Service nie obsługuje tożsamości przypisanych do użytkownika. Poniższe instrukcje dotyczą Azure Functions.

1. W razie potrzeby zainstaluj Azure PowerShell przy użyciu instrukcji znajdujących się w [przewodniku Azure PowerShell](/powershell/azure/), a następnie uruchom polecenie, `Login-AzAccount` Aby utworzyć połączenie z platformą Azure.

2. Utwórz aplikację funkcji przy użyciu Azure PowerShell. Aby uzyskać więcej przykładów użycia Azure PowerShell z Azure Functions, zobacz [AZ. Functions Reference](/powershell/module/az.functions/#functions). Poniższy skrypt korzysta również z programu `New-AzUserAssignedIdentity` , który należy zainstalować oddzielnie zgodnie z definicją [lub usunięciem tożsamości zarządzanej przypisanej przez użytkownika przy użyciu Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md).

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account.
    New-AzStorageAccount -Name $storageAccountName -ResourceGroupName $resourceGroupName -SkuName $sku

    # Create a user-assigned identity. This requires installation of the "Az.ManagedServiceIdentity" module.
    $userAssignedIdentity = New-AzUserAssignedIdentity -Name $userAssignedIdentityName -ResourceGroupName $resourceGroupName

    # Create a function app with a user-assigned identity.
    New-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -Location $location -StorageAccountName $storageAccountName -Runtime $runtime -IdentityType UserAssigned -IdentityId $userAssignedIdentity.Id
    ```

Możesz również zaktualizować istniejącą aplikację funkcji przy użyciu polecenia `Update-AzFunctionApp` .

### <a name="using-an-azure-resource-manager-template"></a>Korzystanie z szablonu Azure Resource Manager

Szablon Azure Resource Manager może służyć do automatyzowania wdrażania zasobów platformy Azure. Aby dowiedzieć się więcej o wdrażaniu do App Service i funkcji, zobacz [Automatyzowanie wdrażania zasobów w App Service](../app-service/deploy-complex-application-predictably.md) i [Automatyzowanie wdrażania zasobów w Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Każdy zasób typu `Microsoft.Web/sites` można utworzyć za pomocą tożsamości, dołączając następujący blok w definicji zasobu, ZASTĘPUJĄC `<RESOURCEID>` Identyfikator zasobu żądanej tożsamości:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

> [!NOTE]
> Aplikacja może mieć w tym samym czasie zarówno tożsamość przypisana do systemu, jak i przypisanych do użytkownika. W tym przypadku właściwość będzie `type``SystemAssigned,UserAssigned`

Dodanie typu przypisanego przez użytkownika oznacza, że platforma Azure będzie używać tożsamości przypisanej do użytkownika określonej dla danej aplikacji.

Na przykład aplikacja sieci Web może wyglądać następująco:

```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
        }
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
    ]
}
```

Gdy witryna zostanie utworzona, ma następujące dodatkowe właściwości:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

PrincipalId jest unikatowym identyfikatorem tożsamości, która jest używana do administrowania usługą Azure AD. ClientId jest unikatowym identyfikatorem nowej tożsamości aplikacji używanej do określania tożsamości, która ma być używana podczas wywołań środowiska uruchomieniowego.

## <a name="obtain-tokens-for-azure-resources"></a>Uzyskaj tokeny dla zasobów platformy Azure

Aplikacja może używać swojej tożsamości zarządzanej, aby uzyskiwać tokeny umożliwiające dostęp do innych zasobów chronionych przez usługę Azure AD, takich jak Azure Key Vault. Te tokeny reprezentują aplikację, która uzyskują dostęp do zasobu, a nie do określonego użytkownika aplikacji. 

Może być konieczne skonfigurowanie zasobu docelowego, aby zezwalać na dostęp z poziomu aplikacji. Na przykład, Jeśli zażądasz tokenu dostępu do Key Vault, musisz upewnić się, że dodano zasady dostępu zawierające tożsamość aplikacji. W przeciwnym razie wywołania Key Vault będą odrzucane, nawet jeśli zawierają token. Aby dowiedzieć się więcej o tym, które zasoby obsługują tokeny Azure Active Directory, zobacz [usługi platformy Azure, które obsługują uwierzytelnianie usługi Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

> [!IMPORTANT]
> Usługi zaplecza dla tożsamości zarządzanych przechowują pamięć podręczną według identyfikatora URI zasobu przez około 24 godziny. W przypadku aktualizacji zasad dostępu określonego zasobu docelowego i natychmiastowego pobrania tokenu dla tego zasobu można nadal uzyskać buforowany token z nieaktualnymi uprawnieniami do momentu wygaśnięcia tego tokenu. Obecnie nie ma możliwości wymuszenia odświeżenia tokenu.

Istnieje prosty protokół REST umożliwiający uzyskanie tokenu w App Service i Azure Functions. Można go używać dla wszystkich aplikacji i języków. W przypadku platformy .NET i środowiska Java zestaw Azure SDK zapewnia streszczenie za pośrednictwem tego protokołu i ułatwia lokalne środowisko programistyczne.

### <a name="using-the-rest-protocol"></a>Korzystanie z protokołu REST

> [!NOTE]
> Starsza wersja tego protokołu używająca wersji interfejsu API "2017-09-01", `secret` zamiast `X-IDENTITY-HEADER` i tylko zaakceptowana `clientid` Właściwość przypisana przez użytkownika. Zwraca również `expires_on` w formacie sygnatury czasowej. MSI_ENDPOINT może służyć jako alias IDENTITY_ENDPOINT, a MSI_SECRET może służyć jako alias dla IDENTITY_HEADER. Ta wersja protokołu jest obecnie wymagana w przypadku planów hostingu zużycia w systemie Linux.

Aplikacja z zarządzaną tożsamością ma zdefiniowane dwie zmienne środowiskowe:

- IDENTITY_ENDPOINT — adres URL usługi tokenu lokalnego.
- IDENTITY_HEADER — nagłówek służący do ograniczania ataków z wykorzystaniem fałszerstwa żądań po stronie serwera (SSRF). Wartość jest obracana przez platformę.

**IDENTITY_ENDPOINT** jest lokalnym adresem URL, z którego aplikacja może żądać tokenów. Aby uzyskać token dla zasobu, wprowadź żądanie HTTP GET do tego punktu końcowego, w tym następujące parametry:

> | Nazwa parametru    | W     | Opis                                                                                                                                                                                                                                                                                                                                |
> |-------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | zasób          | Zapytanie  | Identyfikator URI zasobu usługi Azure AD dla zasobu, dla którego ma zostać uzyskany token. Może to być jedna z [usług platformy Azure, które obsługują uwierzytelnianie usługi Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) lub dowolny inny identyfikator URI zasobu.    |
> | api-version       | Zapytanie  | Wersja interfejsu API tokenu, który ma być używany. Użyj "2019-08-01" lub nowszej (chyba że korzystasz z używania systemu Linux, które obecnie tylko oferują wartość "2017-09-01" — Zobacz uwagi powyżej).                                                                                                                                                                                                                                                                 |
> | ZNAK X-IDENTITY-HEADER | Nagłówek | Wartość zmiennej środowiskowej IDENTITY_HEADER. Ten nagłówek jest używany, aby pomóc w ograniczeniu ataków SSRF (po stronie serwera).                                                                                                                                                                                                    |
> | client_id         | Zapytanie  | Obowiązkowe Identyfikator klienta tożsamości przypisanej do użytkownika, który ma być używany. Nie można użyć w żądaniu, które zawiera `principal_id` , `mi_res_id` lub `object_id` . Jeśli zostaną pominięte wszystkie parametry identyfikatora ( `client_id` , `principal_id` , `object_id` i `mi_res_id` ), używana jest tożsamość przypisana do systemu.                                             |
> | principal_id      | Zapytanie  | Obowiązkowe Identyfikator podmiotu zabezpieczeń przypisany do tożsamości przypisanej do użytkownika. `object_id` jest aliasem, który może być używany w zamian. Nie można użyć dla żądania, które zawiera client_id, mi_res_id lub object_id. Jeśli zostaną pominięte wszystkie parametry identyfikatora ( `client_id` , `principal_id` , `object_id` i `mi_res_id` ), używana jest tożsamość przypisana do systemu. |
> | mi_res_id         | Zapytanie  | Obowiązkowe Identyfikator zasobu platformy Azure dla tożsamości przypisanej do użytkownika, który ma być używany. Nie można użyć w żądaniu, które zawiera `principal_id` , `client_id` lub `object_id` . Jeśli zostaną pominięte wszystkie parametry identyfikatora ( `client_id` , `principal_id` , `object_id` i `mi_res_id` ), używana jest tożsamość przypisana do systemu.                                      |

> [!IMPORTANT]
> Jeśli próbujesz uzyskać tokeny dla tożsamości przypisanych przez użytkownika, musisz dołączyć jedną z właściwości opcjonalnych. W przeciwnym razie usługa tokenów podejmie próbę uzyskania tokenu dla tożsamości przypisanej do systemu, która może być lub nie istnieje.

Pomyślne odpowiedź 200 OK zawiera treść JSON o następujących właściwościach:

> | Nazwa właściwości | Opis                                                                                                                                                                                                                                        |
> |---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | access_token  | Żądany token dostępu. Wywoływana usługa sieci Web może używać tego tokenu do uwierzytelniania w usłudze sieci Web otrzymującej.                                                                                                                               |
> | client_id     | Identyfikator klienta używanej tożsamości.                                                                                                                                                                                                       |
> | expires_on    | Wartość TimeSpan w przypadku wygaśnięcia tokenu dostępu. Data jest reprezentowana jako liczba sekund od "1970-01-01T0:0: 0Z UTC" (odpowiada na `exp` żądania tokenu).                                                                                |
> | not_before    | Przedział czasu, gdy token dostępu zaczyna obowiązywać i można go zaakceptować. Data jest reprezentowana jako liczba sekund od "1970-01-01T0:0: 0Z UTC" (odpowiada na `nbf` żądania tokenu).                                                      |
> | zasób      | Zasób, dla którego zażądano tokenu dostępu, który jest zgodny z `resource` parametrem ciągu zapytania żądania.                                                                                                                               |
> | token_type    | Wskazuje wartość typu tokenu. Jedynym typem obsługiwanym przez usługę Azure AD jest znak. Aby uzyskać więcej informacji o tokenach okaziciela, zobacz [Framework uwierzytelniania OAuth 2,0: użycie tokenu okaziciela (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |

Ta odpowiedź jest taka sama jak [odpowiedź na żądanie tokenu dostępu usługi Azure AD do usługi](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response).

### <a name="rest-protocol-examples"></a>Przykłady protokołu REST

Przykładowe żądanie może wyglądać następująco:

```http
GET /MSI/token?resource=https://vault.azure.net&api-version=2019-08-01 HTTP/1.1
Host: localhost:4141
X-IDENTITY-HEADER: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

I Przykładowa odpowiedź może wyglądać następująco:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "access_token": "eyJ0eXAi…",
    "expires_on": "1586984735",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer",
    "client_id": "5E29463D-71DA-4FE0-8E69-999B57DB23B0"
}
```

### <a name="code-examples"></a>Przykłady kodu

# <a name="net"></a>[.NET](#tab/dotnet)

> [!TIP]
> W przypadku języków .NET można także samodzielnie użyć [Microsoft. Azure. Services. AppAuthentication](#asal) zamiast tego żądania.

```csharp
private readonly HttpClient _client;
// ...
public async Task<HttpResponseMessage> GetToken(string resource)  {
    var request = new HttpRequestMessage(HttpMethod.Get, 
        String.Format("{0}/?resource={1}&api-version=2019-08-01", Environment.GetEnvironmentVariable("IDENTITY_ENDPOINT"), resource));
    request.Headers.Add("X-IDENTITY-HEADER", Environment.GetEnvironmentVariable("IDENTITY_HEADER"));
    return await _client.SendAsync(request);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const rp = require('request-promise');
const getToken = function(resource, cb) {
    let options = {
        uri: `${process.env["IDENTITY_ENDPOINT"]}/?resource=${resource}&api-version=2019-08-01`,
        headers: {
            'X-IDENTITY-HEADER': process.env["IDENTITY_HEADER"]
        }
    };
    rp(options)
        .then(cb);
}
```

# <a name="python"></a>[Python](#tab/python)

```python
import os
import requests

identity_endpoint = os.environ["IDENTITY_ENDPOINT"]
identity_header = os.environ["IDENTITY_HEADER"]

def get_bearer_token(resource_uri):
    token_auth_uri = f"{identity_endpoint}?resource={resource_uri}&api-version=2019-08-01"
    head_msi = {'X-IDENTITY-HEADER':identity_header}

    resp = requests.get(token_auth_uri, headers=head_msi)
    access_token = resp.json()['access_token']

    return access_token
```

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

```powershell
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:IDENTITY_ENDPOINT + "?resource=$resourceURI&api-version=2019-08-01"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"X-IDENTITY-HEADER"="$env:IDENTITY_HEADER"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

---

### <a name="using-the-microsoftazureservicesappauthentication-library-for-net"></a><a name="asal"></a>Korzystanie z biblioteki Microsoft. Azure. Services. AppAuthentication dla platformy .NET

W przypadku aplikacji i funkcji platformy .NET Najprostszym sposobem pracy z zarządzaną tożsamością jest pakiet Microsoft. Azure. Services. AppAuthentication. Ta biblioteka umożliwia również testowanie kodu lokalnie na komputerze deweloperskim przy użyciu konta użytkownika z programu Visual Studio, [interfejsu wiersza polecenia platformy Azure](/cli/azure)lub zintegrowanego uwierzytelniania Active Directory. Gdy usługa jest hostowana w chmurze, domyślnie będzie używać tożsamości przypisanej do systemu, ale można dostosować to zachowanie przy użyciu zmiennej środowiskowej parametrów połączenia, która odwołuje się do identyfikatora klienta tożsamości przypisanej do użytkownika. Aby uzyskać więcej informacji na temat opcji programistycznych w tej bibliotece, zobacz [odwołanie Microsoft. Azure. Services. AppAuthentication]. W tej sekcji pokazano, jak rozpocząć pracę z biblioteką w kodzie.

1. Dodaj odwołania do [programu Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) oraz innych niezbędnych pakietów NuGet do aplikacji. Poniższy przykład używa także [Microsoft. Azure.](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)kluczy.

2. Dodaj następujący kod do aplikacji, modyfikując go jako docelowy dla poprawnego zasobu. Ten przykład przedstawia dwa sposoby pracy z Azure Key Vault:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```

Jeśli chcesz użyć tożsamości zarządzanej przypisanej przez użytkownika, możesz ustawić `AzureServicesAuthConnectionString` ustawienie aplikacji na `RunAs=App;AppId=<clientId-guid>` . Zamień na `<clientId-guid>` Identyfikator klienta tożsamości, której chcesz użyć. Można zdefiniować wiele takich parametrów połączenia za pomocą niestandardowych ustawień aplikacji i przekazywać ich wartości do konstruktora AzureServiceTokenProvider.

```csharp
    var identityConnectionString1 = Environment.GetEnvironmentVariable("UA1_ConnectionString");
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider(identityConnectionString1);
    
    var identityConnectionString2 = Environment.GetEnvironmentVariable("UA2_ConnectionString");
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider(identityConnectionString2);
```

Aby dowiedzieć się więcej o konfigurowaniu AzureServiceTokenProvider i wykonywanych przez niego operacjach, zobacz artykuł [Microsoft. Azure. Services. AppAuthentication] oraz [App Service i Magazyn kluczy za pomocą przykładu MSI .NET](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-azure-sdk-for-java"></a>Korzystanie z zestawu Azure SDK dla języka Java

W przypadku aplikacji i funkcji języka Java Najprostszym sposobem pracy z zarządzaną tożsamością jest [zestaw Azure SDK dla języka Java](https://github.com/Azure/azure-sdk-for-java). W tej sekcji pokazano, jak rozpocząć pracę z biblioteką w kodzie.

1. Dodaj odwołanie do [biblioteki zestawu Azure SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure). W przypadku projektów Maven można dodać ten fragment kodu do `dependencies` sekcji pliku pliku pom projektu:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure</artifactId>
        <version>1.23.0</version>
    </dependency>
    ```

2. Użyj `AppServiceMSICredentials` obiektu do uwierzytelnienia. Ten przykład pokazuje, jak można użyć tego mechanizmu do pracy z Azure Key Vault:

    ```java
    import com.microsoft.azure.AzureEnvironment;
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.keyvault.Vault
    //...
    Azure azure = Azure.authenticate(new AppServiceMSICredentials(AzureEnvironment.AZURE))
            .withSubscription(subscriptionId);
    Vault myKeyVault = azure.vaults().getByResourceGroup(resourceGroup, keyvaultName);

    ```


## <a name="remove-an-identity"></a><a name="remove"></a>Usuwanie tożsamości

Tożsamość przypisana przez system można usunąć, wyłączając funkcję przy użyciu portalu, programu PowerShell lub interfejsu wiersza polecenia w taki sam sposób, jak został utworzony. Tożsamości przypisane do użytkownika można usuwać pojedynczo. Aby usunąć wszystkie tożsamości, ustaw dla opcji Typ tożsamości wartość "Brak".

Usunięcie tożsamości przypisanej do systemu w ten sposób spowoduje również usunięcie jej z usługi Azure AD. Tożsamości przypisane do systemu są również automatycznie usuwane z usługi Azure AD po usunięciu zasobu aplikacji.

Aby usunąć wszystkie tożsamości w [szablonie ARM](#using-an-azure-resource-manager-template):

```json
"identity": {
    "type": "None"
}
```

Aby usunąć wszystkie tożsamości w Azure PowerShell (tylko Azure Functions):

```azurepowershell-interactive
# Update an existing function app to have IdentityType "None".
Update-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -IdentityType None
```

> [!NOTE]
> Istnieje również ustawienie aplikacji, które można ustawić, WEBSITE_DISABLE_MSI, co spowoduje jedynie wyłączenie usługi tokenów lokalnych. Jednak opuszcza tożsamość, a funkcja narzędzi nadal będzie wyświetlać tożsamość zarządzaną jako "on" lub "Enabled". W związku z tym nie zaleca się używania tego ustawienia.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Bezpieczny dostęp SQL Database przy użyciu tożsamości zarządzanej](app-service-web-tutorial-connect-msi.md)

[Dokumentacja Microsoft. Azure. Services. AppAuthentication]: ../key-vault/general/service-to-service-authentication.md
