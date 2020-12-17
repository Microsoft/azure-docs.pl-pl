---
title: Samouczek — dostęp do aplikacji sieci Web Microsoft Graph jako aplikacja | Azure
description: W tym samouczku dowiesz się, jak uzyskać dostęp do danych w Microsoft Graph przy użyciu zarządzanych tożsamości.
services: microsoft-graph, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 12/16/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: 3d6e9d1d66425655f0db5751a28746b6a6a31c86
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97631787"
---
# <a name="tutorial-access-microsoft-graph-from-a-secured-app-as-the-app"></a>Samouczek: dostęp do Microsoft Graph z zabezpieczonej aplikacji jako aplikacji

Dowiedz się, jak uzyskać dostęp do Microsoft Graph z aplikacji sieci Web działającej na Azure App Service.

:::image type="content" alt-text="Diagram przedstawiający dostęp do Microsoft Graph." source="./media/scenario-secure-app-access-microsoft-graph/web-app-access-graph.svg" border="false":::

Chcesz wywołać Microsoft Graph aplikacji sieci Web. Bezpiecznym sposobem zapewnienia dostępu aplikacji sieci Web do danych jest użycie [tożsamości zarządzanej przypisanej do systemu](../active-directory/managed-identities-azure-resources/overview.md). Zarządzana tożsamość z Azure Active Directory umożliwia App Service dostępu do zasobów za pośrednictwem kontroli dostępu opartej na rolach (RBAC), bez konieczności korzystania z poświadczeń aplikacji. Po przypisaniu zarządzanej tożsamości do aplikacji sieci Web, platforma Azure bierze pod uwagę tworzenie i dystrybucję certyfikatu. Nie musisz martwić się o zarządzanie wpisami tajnymi lub poświadczeniami aplikacji.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
>
> * Tworzenie tożsamości zarządzanej przypisanej przez system w aplikacji sieci Web.
> * Dodaj Microsoft Graph uprawnienia interfejsu API do zarządzanej tożsamości.
> * Wywołaj Microsoft Graph z aplikacji sieci Web przy użyciu tożsamości zarządzanych.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Aplikacja sieci Web działająca na Azure App Service z [włączonym modułem uwierzytelniania App Service/autoryzacji](scenario-secure-app-authentication-app-service.md).

## <a name="enable-managed-identity-on-app"></a>Włącz zarządzaną tożsamość w aplikacji

Jeśli utworzysz i opublikujesz aplikację sieci Web za pomocą programu Visual Studio, zarządzana tożsamość została włączona w Twojej aplikacji. W usłudze App Service wybierz pozycję **Identity (tożsamość** ) w okienku po lewej stronie, a następnie wybierz pozycję **przypisane do systemu**. Sprawdź, czy **stan** jest ustawiony na wartość **włączone**. W przeciwnym razie wybierz pozycję **Zapisz** , a następnie wybierz pozycję **tak** , aby włączyć zarządzane przez system tożsamość zarządzaną. Gdy zarządzana tożsamość jest włączona, stan jest ustawiony na **włączone** , a identyfikator obiektu jest dostępny.

Zanotuj wartość **identyfikatora obiektu** , która będzie potrzebna w następnym kroku.

:::image type="content" alt-text="Zrzut ekranu przedstawiający tożsamość przypisaną do systemu." source="./media/scenario-secure-app-access-microsoft-graph/create-system-assigned-identity.png":::

## <a name="grant-access-to-microsoft-graph"></a>Udzielanie dostępu Microsoft Graph

Podczas uzyskiwania dostępu do Microsoft Graph zarządzana tożsamość musi mieć odpowiednie uprawnienia do operacji, którą chce wykonać. Obecnie nie ma możliwości przypisywania takich uprawnień za pomocą Azure Portal. Poniższy skrypt doda żądane uprawnienia interfejsu API Microsoft Graph do obiektu głównego usługi tożsamości zarządzanej.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

```powershell
# Install the module. (You need admin on the machine.)
# Install-Module AzureAD.

# Your tenant ID (in the Azure portal, under Azure Active Directory > Overview).
$TenantID="<tenant-id>"
$resourceGroup = "securewebappresourcegroup"
$webAppName="SecureWebApp-20201102125811"

# Get the ID of the managed identity for the web app.
$spID = (Get-AzWebApp -ResourceGroupName $resourceGroup -Name $webAppName).identity.principalid

# Check the Microsoft Graph documentation for the permission you need for the operation.
$PermissionName = "User.Read.All"

Connect-AzureAD -TenantId $TenantID

# Get the service principal for Microsoft Graph.
# First result should be AppId 00000003-0000-0000-c000-000000000000
$GraphServicePrincipal = Get-AzureADServicePrincipal -SearchString "Microsoft Graph" | Select-Object -first 1

# Assign permissions to the managed identity service principal.
$AppRole = $GraphServicePrincipal.AppRoles | `
Where-Object {$_.Value -eq $PermissionName -and $_.AllowedMemberTypes -contains "Application"}

New-AzureAdServiceAppRoleAssignment -ObjectId $spID -PrincipalId $spID `
-ResourceId $GraphServicePrincipal.ObjectId -Id $AppRole.Id
```

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli-interactive
az login

webAppName="SecureWebApp-20201106120003"

spId=$(az resource list -n $webAppName --query [*].identity.principalId --out tsv)

graphResourceId=$(az ad sp list --display-name "Microsoft Graph" --query [0].objectId --out tsv)

appRoleId=$(az ad sp list --display-name "Microsoft Graph" --query "[0].appRoles[?value=='User.Read.All' && contains(allowedMemberTypes, 'Application')].id" --output tsv)

uri=https://graph.microsoft.com/v1.0/servicePrincipals/$spId/appRoleAssignments

body="{'principalId':'$spId','resourceId':'$graphResourceId','appRoleId':'$appRoleId'}"

az rest --method post --uri $uri --body $body --headers "Content-Type=application/json"
```

---

Po wykonaniu skryptu można sprawdzić w [Azure Portal](https://portal.azure.com) , że żądane uprawnienia interfejsu API są przypisane do tożsamości zarządzanej.

Przejdź do **Azure Active Directory**, a następnie wybierz pozycję **aplikacje dla przedsiębiorstw**. W tym okienku są wyświetlane wszystkie nazwy główne usługi w dzierżawie. We **wszystkich aplikacjach** wybierz nazwę główną usługi dla tożsamości zarządzanej. 

Jeśli korzystasz z tego samouczka, istnieją dwie jednostki usługi o tej samej nazwie wyświetlanej (na przykład SecureWebApp2020094113531). Nazwa główna usługi, która zawiera **adres URL strony głównej** , reprezentuje aplikację sieci Web w dzierżawie. Nazwa główna usługi bez **adresu URL strony głównej** reprezentuje przypisaną przez system tożsamość zarządzaną dla aplikacji sieci Web. Wartość **identyfikatora obiektu** dla tożsamości zarządzanej jest zgodna z identyfikatorem obiektu tożsamości zarządzanej, która została wcześniej utworzona.

Wybierz nazwę główną usługi dla tożsamości zarządzanej.

:::image type="content" alt-text="Zrzut ekranu pokazujący opcję Wszystkie aplikacje." source="./media/scenario-secure-app-access-microsoft-graph/enterprise-apps-all-applications.png":::

W obszarze **Przegląd** wybierz pozycję **uprawnienia**, a zobaczysz uprawnienia dodane do Microsoft Graph.

:::image type="content" alt-text="Zrzut ekranu przedstawiający okienko uprawnień." source="./media/scenario-secure-app-access-microsoft-graph/enterprise-apps-permissions.png":::

## <a name="call-microsoft-graph-net"></a>Microsoft Graph wywołań (.NET)

Klasa [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) jest używana do uzyskiwania poświadczeń tokenu dla kodu, aby autoryzować żądania do Microsoft Graph. Utwórz wystąpienie klasy [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) , która używa tożsamości zarządzanej do pobierania tokenów i dołączania ich do klienta usługi. Poniższy przykład kodu Pobiera poświadczenia uwierzytelnionego tokenu i używa go do utworzenia obiektu klienta usługi, który pobiera użytkowników w grupie.

Aby wyświetlić ten kod jako część przykładowej aplikacji, zobacz [przykład w witrynie GitHub](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/3-WebApp-graphapi-managed-identity).

### <a name="install-the-microsoftgraph-client-library-package"></a>Instalowanie pakietu biblioteki klienta Microsoft. Graph

Zainstaluj [pakiet NuGet Microsoft. Graph](https://www.nuget.org/packages/Microsoft.Graph) w projekcie przy użyciu interfejsu wiersza polecenia platformy .NET Core lub konsoli Menedżera pakietów w programie Visual Studio.

# <a name="command-line"></a>[Wiersz polecenia](#tab/command-line)

Otwórz wiersz polecenia i przejdź do katalogu, który zawiera plik projektu.

Uruchom polecenia instalacji.

```dotnetcli
dotnet add package Microsoft.Graph
```

# <a name="package-manager"></a>[Menedżer pakietów](#tab/package-manager)

Otwórz projekt/rozwiązanie w programie Visual Studio i Otwórz konsolę programu przy użyciu narzędzia Menedżer   >  **pakietów NuGet**  >  **konsola Menedżera pakietów** .

Uruchom polecenia instalacji.
```powershell
Install-Package Microsoft.Graph
```

---

### <a name="example"></a>Przykład

```csharp
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc.RazorPages;
using Azure.Identity;
using Microsoft.Graph.Core;
using System.Net.Http.Headers;

...

public IList<MSGraphUser> Users { get; set; }

public async Task OnGetAsync()
{
    // Create the Microsoft Graph service client with a DefaultAzureCredential class, which gets an access token by using the available Managed Identity.
    var credential = new DefaultAzureCredential();
    var token = credential.GetToken(
        new Azure.Core.TokenRequestContext(
            new[] { "https://graph.microsoft.com/.default" }));

    var accessToken = token.Token;
    var graphServiceClient = new GraphServiceClient(
        new DelegateAuthenticationProvider((requestMessage) =>
        {
            requestMessage
            .Headers
            .Authorization = new AuthenticationHeaderValue("bearer", accessToken);

            return Task.CompletedTask;
        }));

    List<MSGraphUser> msGraphUsers = new List<MSGraphUser>();
    try
    {
        var users =await graphServiceClient.Users.Request().GetAsync();
        foreach(var u in users)
        {
            MSGraphUser user = new MSGraphUser();
            user.userPrincipalName = u.UserPrincipalName;
            user.displayName = u.DisplayName;
            user.mail = u.Mail;
            user.jobTitle = u.JobTitle;

            msGraphUsers.Add(user);
        }
    }
    catch(Exception ex)
    {
        string msg = ex.Message;
    }

    Users = msGraphUsers;
}
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli skończysz pracę z tym samouczkiem i nie potrzebujesz już aplikacji sieci Web ani skojarzonych zasobów, [Wyczyść utworzone zasoby](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Tworzenie tożsamości zarządzanej przypisanej przez system w aplikacji sieci Web.
> * Dodaj Microsoft Graph uprawnienia interfejsu API do zarządzanej tożsamości.
> * Wywołaj Microsoft Graph z aplikacji sieci Web przy użyciu tożsamości zarządzanych.

Dowiedz się, jak połączyć [aplikację platformy .NET Core](tutorial-dotnetcore-sqldb-app.md), aplikację [języka](tutorial-java-spring-cosmosdb.md) [Python](tutorial-python-postgresql-app.md), aplikację Java lub [Node.js aplikację](tutorial-nodejs-mongodb-app.md) z bazą danych.