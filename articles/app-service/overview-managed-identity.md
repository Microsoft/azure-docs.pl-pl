---
title: Tożsamości zarządzane
description: Dowiedz się, jak działają tożsamości zarządzane w Azure App Service i Azure Functions, jak skonfigurować tożsamość zarządzaną i wygenerować token dla zasobu back-end.
author: mattchenderson
ms.topic: article
ms.date: 05/27/2020
ms.author: mahender
ms.reviewer: yevbronsh
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: badc6b6f1b45938e950ffadeefe30d81ed383440
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107762447"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Jak używać tożsamości zarządzanych na App Service i Azure Functions

W tym temacie pokazano, jak utworzyć tożsamość zarządzaną dla aplikacji App Service i Azure Functions oraz jak używać jej do uzyskiwania dostępu do innych zasobów. 

> [!Important] 
> Tożsamości zarządzane dla App Service i Azure Functions nie będą działać zgodnie z oczekiwaniami, jeśli aplikacja zostanie zmigrowana między subskrypcjami/dzierżawami. Aplikacja musi uzyskać nową tożsamość, co jest wykonywane przez wyłączenie i ponowne włączenie funkcji. Zobacz [Usuwanie tożsamości](#remove) poniżej. Zasoby podrzędne muszą również mieć zaktualizowane zasady dostępu, aby używać nowej tożsamości.

[!INCLUDE [app-service-managed-identities](../../includes/app-service-managed-identities.md)]

## <a name="add-a-system-assigned-identity"></a>Dodawanie tożsamości przypisanej przez system

Tworzenie aplikacji z tożsamością przypisaną przez system wymaga ustawienia dodatkowej właściwości w aplikacji.

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Aby skonfigurować tożsamość zarządzaną w portalu, musisz najpierw utworzyć aplikację w zwykły sposób, a następnie włączyć tę funkcję.

1. Utwórz aplikację w portalu w zwykły sposób. Przejdź do niej w portalu.

2. Jeśli używasz aplikacji funkcji, przejdź do funkcji **platformy**. W przypadku innych typów aplikacji przewiń w dół do **grupy Ustawienia** na lewym pasku nawigacyjnym.

3. Wybierz pozycję **Tożsamość.**

4. Na karcie **Przypisane do systemu** przełącz pozycję **Stan** na **Wł.**. Kliknij pozycję **Zapisz**.

    ![Zrzut ekranu przedstawiający miejsce przełączenia opcji Stan na Wł., a następnie wybierz pozycję Zapisz.](media/app-service-managed-service-identity/system-assigned-managed-identity-in-azure-portal.png)


> [!NOTE] 
> Aby znaleźć tożsamość zarządzaną dla aplikacji internetowej lub aplikacji miejsca w aplikacji Azure Portal, w obszarze Aplikacje dla przedsiębiorstw znajdź sekcję **Ustawienia** użytkownika. Zazwyczaj nazwa miejsca jest podobna do `<app name>/slots/<slot name>` .


### <a name="using-the-azure-cli"></a>Przy użyciu interfejsu wiersza polecenia platformy Azure

Aby skonfigurować tożsamość zarządzaną przy użyciu interfejsu wiersza polecenia platformy Azure, należy użyć `az webapp identity assign` polecenia względem istniejącej aplikacji. Dostępne są trzy opcje uruchamiania przykładów w tej sekcji:

- Użyj [Azure Cloud Shell](../cloud-shell/overview.md) z Azure Portal.
- Użyj osadzonego Azure Cloud Shell za pomocą przycisku "Wypróbuj", który znajduje się w prawym górnym rogu każdego bloku kodu poniżej.
- [Zainstaluj najnowszą wersję interfejsu](/cli/azure/install-azure-cli) wiersza polecenia platformy Azure (2.0.31 lub nowszą), jeśli wolisz używać lokalnej konsoli interfejsu wiersza polecenia. 

Poniższe kroki będą zawierały instrukcje tworzenia aplikacji internetowej i przypisywania jej tożsamości przy użyciu interfejsu wiersza polecenia:

1. Jeśli używasz interfejsu wiersza polecenia platformy Azure w konsoli lokalnej, najpierw zaloguj się do platformy Azure za pomocą polecenia [az login](/cli/azure/reference-index#az_login). Użyj konta skojarzonego z subskrypcją platformy Azure, w ramach której chcesz wdrożyć aplikację:

    ```azurecli-interactive
    az login
    ```

2. Tworzenie aplikacji internetowej przy użyciu interfejsu wiersza polecenia. Aby uzyskać więcej przykładów użycia interfejsu wiersza polecenia z interfejsem App Service, zobacz [App Service przykłady interfejsu wiersza polecenia:](../app-service/samples-cli.md)

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. Uruchom polecenie `identity assign` , aby utworzyć tożsamość dla tej aplikacji:

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Poniższe kroki będą zawierały instrukcje tworzenia aplikacji i przypisywania jej tożsamości przy użyciu Azure PowerShell. Instrukcje tworzenia aplikacji internetowej i aplikacji funkcji są różne.

#### <a name="using-azure-powershell-for-a-web-app"></a>Używanie Azure PowerShell dla aplikacji internetowej

1. W razie potrzeby zainstaluj pakiet Azure PowerShell instrukcje w przewodniku Azure PowerShell [,](/powershell/azure/)a następnie uruchom program , aby utworzyć połączenie `Login-AzAccount` z platformą Azure.

2. Utwórz aplikację internetową przy użyciu Azure PowerShell. Aby uzyskać więcej przykładów użycia programu Azure PowerShell z App Service, [zobacz App Service przykłady programu PowerShell:](../app-service/samples-powershell.md)

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName $resourceGroupName -Tier Free

    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName $resourceGroupName
    ```

3. Uruchom polecenie `Set-AzWebApp -AssignIdentity` , aby utworzyć tożsamość dla tej aplikacji:

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName $resourceGroupName 
    ```

#### <a name="using-azure-powershell-for-a-function-app"></a>Używanie Azure PowerShell dla aplikacji funkcji

1. W razie potrzeby zainstaluj pakiet Azure PowerShell instrukcje w przewodniku Azure PowerShell [,](/powershell/azure/)a następnie uruchom program , aby utworzyć połączenie `Login-AzAccount` z platformą Azure.

2. Utwórz aplikację funkcji przy użyciu Azure PowerShell. Aby uzyskać więcej przykładów użycia funkcji Azure PowerShell z Azure Functions, zobacz informacje [dotyczące funkcji Az.Functions:](/powershell/module/az.functions/#functions)

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name $resourceGroupName -Location $location

    # Create a storage account.
    New-AzStorageAccount -Name $storageAccountName -ResourceGroupName $resourceGroupName -SkuName $sku

    # Create a function app with a system-assigned identity.
    New-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -Location $location -StorageAccountName $storageAccountName -Runtime $runtime -IdentityType SystemAssigned
    ```

Zamiast tego możesz również zaktualizować istniejącą aplikację funkcji przy `Update-AzFunctionApp` użyciu .

### <a name="using-an-azure-resource-manager-template"></a>Korzystanie z Azure Resource Manager szablonu

Szablon Azure Resource Manager może służyć do automatyzowania wdrażania zasobów platformy Azure. Aby dowiedzieć się więcej na temat wdrażania w usługach App Service i Functions, zobacz [Automatyzowanie](../app-service/deploy-complex-application-predictably.md) wdrażania zasobów w usługach App Service i [Automatyzowanie wdrażania zasobów](../azure-functions/functions-infrastructure-as-code.md)w Azure Functions .

Każdy zasób typu `Microsoft.Web/sites` można utworzyć za pomocą tożsamości, uwzględniając następującą właściwość w definicji zasobu:

```json
"identity": {
    "type": "SystemAssigned"
}
```

> [!NOTE]
> Aplikacja może mieć jednocześnie tożsamości przypisane przez system i przez użytkownika. W tym przypadku właściwość `type` będzie miała wartość `SystemAssigned,UserAssigned`

Dodanie typu przypisanego przez system nakazuje platformie Azure utworzenie tożsamości aplikacji i zarządzanie jej tożsamością.

Na przykład aplikacja internetowa może wyglądać następująco:

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

Po utworzeniu lokacji ma ona następujące dodatkowe właściwości:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

Właściwość tenantId identyfikuje dzierżawę usługi Azure AD, do której należy tożsamość. PrincipalId jest unikatowym identyfikatorem nowej tożsamości aplikacji. W usłudze Azure AD nazwa główna usługi ma tę samą nazwę, która została nadana App Service lub Azure Functions wystąpienia.

Jeśli musisz odwołać się do tych właściwości na późniejszym etapie w szablonie, możesz to zrobić za pomocą funkcji [ `reference()` szablonu](../azure-resource-manager/templates/template-functions-resource.md#reference) z `'Full'` flagą , jak w poniższym przykładzie:

```json
{
    "tenantId": "[reference(resourceId('Microsoft.Web/sites', variables('appName')), '2018-02-01', 'Full').identity.tenantId]",
    "objectId": "[reference(resourceId('Microsoft.Web/sites', variables('appName')), '2018-02-01', 'Full').identity.principalId]",
}
```

## <a name="add-a-user-assigned-identity"></a>Dodawanie tożsamości przypisanej przez użytkownika

Tworzenie aplikacji przy użyciu tożsamości przypisanej przez użytkownika wymaga utworzenia tożsamości, a następnie dodania jej identyfikatora zasobu do konfiguracji aplikacji.

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Najpierw musisz utworzyć zasób tożsamości przypisany przez użytkownika.

1. Utwórz zasób tożsamości zarządzanej przypisany przez użytkownika zgodnie z [tymi instrukcjami.](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)

2. Utwórz aplikację w portalu w zwykły sposób. Przejdź do niej w portalu.

3. Jeśli używasz aplikacji funkcji, przejdź do funkcji **platformy**. W przypadku innych typów aplikacji przewiń w dół do **grupy Ustawienia** na lewym pasku nawigacyjnym.

4. Wybierz pozycję **Tożsamość.**

5. Na karcie **Przypisano użytkownika** kliknij pozycję **Dodaj**.

6. Wyszukaj utworzoną wcześniej tożsamość i wybierz ją. Kliknij pozycję **Dodaj**.

    ![Tożsamość zarządzana w App Service](media/app-service-managed-service-identity/user-assigned-managed-identity-in-azure-portal.png)

### <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Poniższe kroki będą zawierały instrukcje tworzenia aplikacji i przypisywania jej tożsamości przy użyciu Azure PowerShell.

> [!NOTE]
> Bieżąca wersja poleceń Azure PowerShell dla Azure App Service nie obsługuje tożsamości przypisanych przez użytkownika. Poniższe instrukcje są Azure Functions.

1. W razie potrzeby zainstaluj pakiet Azure PowerShell instrukcje w przewodniku Azure PowerShell [,](/powershell/azure/)a następnie uruchom program , aby utworzyć połączenie `Login-AzAccount` z platformą Azure.

2. Utwórz aplikację funkcji przy użyciu Azure PowerShell. Aby uzyskać więcej przykładów użycia funkcji Azure PowerShell z Azure Functions, zobacz informacje [dotyczące funkcji Az.Functions.](/powershell/module/az.functions/#functions) Poniższy skrypt korzysta również z narzędzia , które należy zainstalować oddzielnie zgodnie z tematem `New-AzUserAssignedIdentity` [Create, list or delete a user-assigned managed identity using Azure PowerShell ( Tworzenie, tworzenie,](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)tworzenie, usuwanie tożsamości zarządzanej przypisanej przez użytkownika przy użyciu Azure PowerShell .

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

Zamiast tego możesz również zaktualizować istniejącą aplikację funkcji przy `Update-AzFunctionApp` użyciu .

### <a name="using-an-azure-resource-manager-template"></a>Korzystanie z Azure Resource Manager szablonu

Szablon Azure Resource Manager może służyć do automatyzowania wdrażania zasobów platformy Azure. Aby dowiedzieć się więcej na temat wdrażania w usługach App Service i Functions, zobacz Automatyzowanie wdrażania zasobów w [usługach App Service](../app-service/deploy-complex-application-predictably.md) i [Automatyzowanie wdrażania zasobów](../azure-functions/functions-infrastructure-as-code.md)w usługach Azure Functions .

Dowolny zasób typu można utworzyć przy użyciu tożsamości, uwzględniając następujący blok w definicji zasobu, zastępując element identyfikatorem zasobu `Microsoft.Web/sites` `<RESOURCEID>` żądanej tożsamości:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

> [!NOTE]
> Aplikacja może mieć jednocześnie tożsamości przypisane przez system i przez użytkownika. W tym przypadku właściwość `type` będzie miała wartość `SystemAssigned,UserAssigned`

Dodanie typu przypisanego przez użytkownika nakazuje platformie Azure użycie tożsamości przypisanej przez użytkownika określonej dla aplikacji.

Na przykład aplikacja internetowa może wyglądać następująco:

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

Po utworzeniu lokacji ma ona następujące dodatkowe właściwości:

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

PrincipalId to unikatowy identyfikator tożsamości używanej do administrowania usługą Azure AD. ClientId to unikatowy identyfikator nowej tożsamości aplikacji, który służy do określania tożsamości do użycia podczas wywołań środowiska uruchomieniowego.

## <a name="obtain-tokens-for-azure-resources"></a>Uzyskiwanie tokenów dla zasobów platformy Azure

Aplikacja może używać swojej tożsamości zarządzanej do uzyskiwania tokenów dostępu do innych zasobów chronionych przez usługę Azure AD, takich jak Azure Key Vault. Te tokeny reprezentują aplikację, która uzyskuje dostęp do zasobu, a nie żadnego określonego użytkownika aplikacji. 

Może być konieczne skonfigurowanie zasobu docelowego, aby zezwolić na dostęp z aplikacji. Jeśli na przykład żądasz tokenu w celu uzyskania dostępu do Key Vault, upewnij się, że dodano zasady dostępu, które obejmują tożsamość aplikacji. W przeciwnym razie wywołania Key Vault zostaną odrzucone, nawet jeśli zawierają token. Aby dowiedzieć się więcej o tym, które zasoby obsługują tokeny Azure Active Directory, zobacz Usługi platformy [Azure, które obsługują uwierzytelnianie usługi Azure AD.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

> [!IMPORTANT]
> Usługi back-end dla tożsamości zarządzanych utrzymują pamięć podręczną na każdy zasób przez około 24 godziny. Jeśli zaktualizujemy zasady dostępu określonego zasobu docelowego i natychmiast pobierzemy token dla tego zasobu, możesz nadal pobierać token z pamięci podręcznej z nieaktualnym uprawnieniami do momentu wygaśnięcia tego tokenu. Obecnie nie ma możliwości wymuś odświeżenia tokenu.

Istnieje prosty protokół REST do uzyskiwania tokenu w App Service i Azure Functions. Można jej używać we wszystkich aplikacjach i językach. W przypadku platform .NET i Java zestaw Azure SDK zapewnia abstrakcję tego protokołu i ułatwia lokalne środowisko deweloperskiego.

### <a name="using-the-rest-protocol"></a>Korzystanie z protokołu REST

> [!NOTE]
> Starsza wersja tego protokołu, korzystająca z interfejsu API "2017-09-01", użyła nagłówka zamiast i zaakceptowała tylko właściwość przypisaną przez `secret` `X-IDENTITY-HEADER` `clientid` użytkownika. Zwrócono również znacznik `expires_on` czasu w formacie znacznika czasu. MSI_ENDPOINT można użyć jako aliasu dla IDENTITY_ENDPOINT, a MSI_SECRET jako aliasu dla IDENTITY_HEADER. Ta wersja protokołu jest obecnie wymagana w przypadku Zużycie w systemie Linux hostingu.

Aplikacja z tożsamością zarządzaną ma dwie zdefiniowane zmienne środowiskowe:

- IDENTITY_ENDPOINT — adres URL lokalnej usługi tokenu.
- IDENTITY_HEADER — nagłówek ułatwiający łagodzenie ataków fałszowania żądań po stronie serwera (SSRF). Wartość jest obracana przez platformę.

Adres **IDENTITY_ENDPOINT** to lokalny adres URL, z którego aplikacja może żądać tokenów. Aby uzyskać token dla zasobu, należy wykonać żądanie HTTP GET do tego punktu końcowego, w tym następujące parametry:

> | Nazwa parametru    | W     | Opis                                                                                                                                                                                                                                                                                                                                |
> |-------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | zasób          | Zapytanie  | URI zasobu usługi Azure AD dla zasobu, dla którego należy uzyskać token. Może to być jedna z usług [platformy Azure, które obsługują uwierzytelnianie usługi Azure AD,](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) lub dowolny inny zasób URI.    |
> | api-version       | Zapytanie  | Wersja interfejsu API tokenu do użyciu. Użyj ciągu "2019-08-01" lub nowszego (chyba że używasz usługi Zużycie w systemie Linux, która obecnie oferuje tylko ofertę "2017-09-01" — patrz uwaga powyżej).                                                                                                                                                                                                                                                                 |
> | X-IDENTITY-HEADER | Nagłówek | Wartość zmiennej IDENTITY_HEADER środowiskowej. Ten nagłówek jest używany do ograniczania ataków fałszerczych (SSRF) po stronie serwera.                                                                                                                                                                                                    |
> | client_id         | Zapytanie  | (Opcjonalnie) Identyfikator klienta tożsamości przypisanej przez użytkownika, która ma być używana. Nie można używać w żądaniu, które zawiera `principal_id` `mi_res_id` elementy , lub `object_id` . Jeśli wszystkie parametry identyfikatora ( , , i ) zostaną pominięte, zostanie użyta tożsamość `client_id` `principal_id` `object_id` `mi_res_id` przypisana przez system.                                             |
> | Principal_id      | Zapytanie  | (Opcjonalnie) Identyfikator podmiotu zabezpieczeń tożsamości przypisanej przez użytkownika, która ma być używana. `object_id` to alias, który może być używany zamiast tego. Nie można używać w żądaniu, które obejmuje client_id, mi_res_id lub object_id. Jeśli zostaną pominięte wszystkie parametry identyfikatora ( , , i ), zostanie użyta tożsamość przypisana `client_id` `principal_id` przez `object_id` `mi_res_id` system. |
> | mi_res_id         | Zapytanie  | (Opcjonalnie) Identyfikator zasobu platformy Azure tożsamości przypisanej przez użytkownika do użycia. Nie można używać w żądaniu, które zawiera `principal_id` `client_id` elementy , lub `object_id` . Jeśli zostaną pominięte wszystkie parametry identyfikatora ( , , i ), zostanie użyta tożsamość przypisana `client_id` `principal_id` przez `object_id` `mi_res_id` system.                                      |

> [!IMPORTANT]
> Jeśli próbujesz uzyskać tokeny dla tożsamości przypisanych przez użytkownika, musisz dołączyć jedną z opcjonalnych właściwości. W przeciwnym razie usługa tokenu podejmie próbę uzyskania tokenu dla tożsamości przypisanej przez system, która może lub nie może istnieć.

Pomyślna odpowiedź 200 OK zawiera treść JSON z następującymi właściwościami:

> | Nazwa właściwości | Opis                                                                                                                                                                                                                                        |
> |---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | access_token  | Żądany token dostępu. Wywołująca usługa internetowa może użyć tego tokenu do uwierzytelnienia w odbieranej usłudze internetowej.                                                                                                                               |
> | client_id     | Identyfikator klienta użytej tożsamości.                                                                                                                                                                                                       |
> | expires_on    | Czas wygaśnięcia tokenu dostępu. Data jest reprezentowana jako liczba sekund od "1970-01-01T0:0:0Z UTC" (odpowiada oświadczenia `exp` tokenu).                                                                                |
> | not_before    | Okres, w którym token dostępu jest obowiązywać i można go zaakceptować. Data jest reprezentowana jako liczba sekund od "1970-01-01T0:0:0Z UTC" (odpowiada oświadczenia `nbf` tokenu).                                                      |
> | zasób      | Zasób, dla którego zażądano tokenu dostępu, pasuje do `resource` parametru ciągu zapytania żądania.                                                                                                                               |
> | token_type    | Wskazuje wartość typu tokenu. Jedynym typem, który obsługuje usługa Azure AD, jest Bearer. Aby uzyskać więcej informacji na temat tokenów okaziciela, zobacz [The OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750) (Struktury autoryzacji OAuth 2.0: użycie tokenu bearer (RFC 6750) ).](https://www.rfc-editor.org/rfc/rfc6750.txt) |

Ta odpowiedź jest taka sama jak odpowiedź na żądanie tokenu dostępu [service-to-service usługi Azure AD.](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response)

### <a name="rest-protocol-examples"></a>Przykłady protokołów REST

Przykładowe żądanie może wyglądać następująco:

```http
GET /MSI/token?resource=https://vault.azure.net&api-version=2019-08-01 HTTP/1.1
Host: localhost:4141
X-IDENTITY-HEADER: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

Przykładowa odpowiedź może wyglądać następująco:

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
> W przypadku języków platformy .NET można również użyć pakietu [Microsoft.Azure.Services.AppAuthentication](#asal) zamiast samodzielnie utworzyć to żądanie.

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

### <a name="using-the-microsoftazureservicesappauthentication-library-for-net"></a><a name="asal"></a>Korzystanie z biblioteki Microsoft.Azure.Services.AppAuthentication dla platformy .NET

W przypadku aplikacji i funkcji platformy .NET najprostszym sposobem pracy z tożsamością zarządzaną jest pakiet Microsoft.Azure.Services.AppAuthentication. Ta biblioteka umożliwia również testowanie kodu lokalnie na komputerze dewelopera przy użyciu konta użytkownika z witryny Visual Studio, interfejsu wiersza polecenia platformy [Azure](/cli/azure)lub zintegrowanego uwierzytelniania usługi Active Directory. W przypadku hostowania w chmurze domyślnie będzie używać tożsamości przypisanej przez system, ale to zachowanie można dostosować przy użyciu zmiennej środowiskowej parametrów połączenia, która odwołuje się do identyfikatora klienta tożsamości przypisanej przez użytkownika. Aby uzyskać więcej informacji na temat opcji programistyki dostępnych w tej bibliotece, zobacz informacje [dotyczące pakietu Microsoft.Azure.Services.AppAuthentication]. W tej sekcji pokazano, jak rozpocząć pracę z biblioteką w kodzie.

1. Dodaj odwołania do [pakietu Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) i wszelkich innych niezbędnych pakietów NuGet do aplikacji. W poniższym przykładzie użyto również [wartości Microsoft.Azure.KeyVault.](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)

2. Dodaj następujący kod do aplikacji, modyfikując element docelowy odpowiedniego zasobu. W tym przykładzie przedstawiono dwa sposoby pracy z Azure Key Vault:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```

Jeśli chcesz użyć tożsamości zarządzanej przypisanej przez użytkownika, możesz ustawić `AzureServicesAuthConnectionString` ustawienie aplikacji na wartość `RunAs=App;AppId=<clientId-guid>` . Zastąp `<clientId-guid>` identyfikatorem klienta tożsamości, której chcesz użyć. Wiele takich parametrów połączenia można zdefiniować przy użyciu niestandardowych ustawień aplikacji i przekazując ich wartości do konstruktora AzureServiceTokenProvider.

```csharp
    var identityConnectionString1 = Environment.GetEnvironmentVariable("UA1_ConnectionString");
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider(identityConnectionString1);
    
    var identityConnectionString2 = Environment.GetEnvironmentVariable("UA2_ConnectionString");
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider(identityConnectionString2);
```

Aby dowiedzieć się więcej na temat konfigurowania urządzenia AzureServiceTokenProvider i operacji, które uwidacznia, zobacz odwołanie [Microsoft.Azure.Services.AppAuthentication] oraz przykład App Service i [KeyVault z msi .NET](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-azure-sdk-for-java"></a>Korzystanie z zestawu Azure SDK dla języka Java

W przypadku aplikacji i funkcji języka Java najprostszym sposobem pracy z tożsamością zarządzaną jest korzystanie z [zestawu Azure SDK dla języka Java.](https://github.com/Azure/azure-sdk-for-java) W tej sekcji pokazano, jak rozpocząć pracę z biblioteką w kodzie.

1. Dodaj odwołanie do [biblioteki zestawu Azure SDK.](https://mvnrepository.com/artifact/com.microsoft.azure/azure) W przypadku projektów Maven możesz dodać ten fragment kodu do sekcji pliku `dependencies` POM projektu:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure</artifactId>
        <version>1.23.0</version>
    </dependency>
    ```

2. Użyj obiektu `AppServiceMSICredentials` do uwierzytelniania. W tym przykładzie pokazano, jak można użyć tego mechanizmu do pracy z Azure Key Vault:

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

Tożsamość przypisaną przez system można usunąć, wyłączając funkcję przy użyciu portalu, programu PowerShell lub interfejsu wiersza polecenia w taki sam sposób, w jaki została utworzona. Tożsamości przypisane przez użytkownika można usuwać indywidualnie. Aby usunąć wszystkie tożsamości, ustaw typ tożsamości na wartość "Brak".

Usunięcie w ten sposób tożsamości przypisanej przez system spowoduje również usunięcie jej z usługi Azure AD. Tożsamości przypisane przez system są również automatycznie usuwane z usługi Azure AD po usunięciu zasobu aplikacji.

Aby usunąć wszystkie tożsamości w [szablonie usługi ARM:](#using-an-azure-resource-manager-template)

```json
"identity": {
    "type": "None"
}
```

Aby usunąć wszystkie tożsamości w Azure PowerShell (tylko Azure Functions tożsamości):

```azurepowershell-interactive
# Update an existing function app to have IdentityType "None".
Update-AzFunctionApp -Name $functionAppName -ResourceGroupName $resourceGroupName -IdentityType None
```

> [!NOTE]
> Istnieje również ustawienie aplikacji, które można ustawić, WEBSITE_DISABLE_MSI, które po prostu wyłącza lokalną usługę tokenów. Jednak pozostawia tożsamość na miejscu, a narzędzia nadal będą wyświetlać tożsamość zarządzaną jako "włączona" lub "włączona". W związku z tym użycie tego ustawienia nie jest zalecane.

## <a name="next-steps"></a>Następne kroki

- [Uzyskiwanie SQL Database dostępu przy użyciu tożsamości zarządzanej](app-service-web-tutorial-connect-msi.md)
- [Bezpieczne uzyskiwanie dostępu do usługi Azure Storage przy użyciu tożsamości zarządzanej](scenario-secure-app-access-storage.md)
- [Bezpieczne Microsoft Graph przy użyciu tożsamości zarządzanej](scenario-secure-app-access-microsoft-graph-as-app.md)

[Microsoft.Azure.Services.AppAuthentication reference (Odwołanie do pakietu Microsoft.Azure.Services.AppAuthentication)]: /dotnet/api/overview/azure/service-to-service-authentication