---
title: Samouczek — aplikacja internetowa uzyskuje dostęp Microsoft Graph jako aplikacja| Azure
description: Z tego samouczka dowiesz się, jak uzyskać dostęp do danych w Microsoft Graph przy użyciu tożsamości zarządzanych.
services: microsoft-graph, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 01/28/2021
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1, devx-track-azurepowershell
ms.openlocfilehash: 5bb52799836b1975de9d936e04fb53987effb300
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832630"
---
# <a name="tutorial-access-microsoft-graph-from-a-secured-app-as-the-app"></a>Samouczek: Microsoft Graph dostępu z zabezpieczonej aplikacji jako aplikacji

Dowiedz się, jak uzyskać dostęp Microsoft Graph aplikacji internetowej działającej na platformie Azure App Service.

:::image type="content" alt-text="Diagram przedstawiający uzyskiwanie dostępu do Microsoft Graph." source="./media/scenario-secure-app-access-microsoft-graph/web-app-access-graph.svg" border="false":::

Chcesz wywołać Microsoft Graph dla aplikacji internetowej. Bezpiecznym sposobem na nadanie aplikacji internetowej dostępu do danych jest użycie przypisanej przez [system tożsamości zarządzanej](../active-directory/managed-identities-azure-resources/overview.md). Tożsamość zarządzana z usługi Azure Active Directory umożliwia App Service dostępu do zasobów za pośrednictwem kontroli dostępu opartej na rolach (RBAC) bez konieczności poświadczeń aplikacji. Po przypisaniu tożsamości zarządzanej do aplikacji internetowej platforma Azure zajmuje się tworzeniem i dystrybucją certyfikatu. Nie musisz martwić się o zarządzanie wpisami tajnymi ani poświadczeniami aplikacji.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Utwórz tożsamość zarządzaną przypisaną przez system w aplikacji internetowej.
> * Dodawanie Microsoft Graph interfejsu API do tożsamości zarządzanej.
> * Wywołaj Microsoft Graph z aplikacji internetowej przy użyciu tożsamości zarządzanych.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Aplikacja internetowa uruchomiona na Azure App Service z włączonym [modułem App Service uwierzytelniania/autoryzacji.](scenario-secure-app-authentication-app-service.md)

## <a name="enable-managed-identity-on-app"></a>Włączanie tożsamości zarządzanej w aplikacji

Jeśli utworzysz i opublikujesz aplikację internetową za pośrednictwem Visual Studio, tożsamość zarządzana została włączona w Twojej aplikacji. W usłudze App Service wybierz pozycję **Tożsamość w** okienku po lewej stronie, a następnie wybierz **pozycję Przypisana przez system**. Sprawdź, **czy dla ustawienia Stan** ustawiono wartość **Wł.** Jeśli nie, wybierz **pozycję Zapisz,** a następnie wybierz pozycję **Tak,** aby włączyć tożsamość zarządzaną przypisaną przez system. Gdy tożsamość zarządzana jest włączona, stan jest ustawiony na **Wł.,** a identyfikator obiektu jest dostępny.

Zanotuj **wartość Identyfikator** obiektu, która będzie potrzebna w następnym kroku.

:::image type="content" alt-text="Zrzut ekranu przedstawiający tożsamość przypisaną przez system." source="./media/scenario-secure-app-access-microsoft-graph/create-system-assigned-identity.png":::

## <a name="grant-access-to-microsoft-graph"></a>Udzielanie dostępu do Microsoft Graph

Podczas uzyskiwania Microsoft Graph tożsamości zarządzanej musi mieć odpowiednie uprawnienia do operacji, które chce wykonać. Obecnie nie ma możliwości przypisania takich uprawnień za pośrednictwem Azure Portal. Poniższy skrypt doda żądane uprawnienia interfejsu API Microsoft Graph do obiektu jednostki usługi tożsamości zarządzanej.

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

Po wykonaniu skryptu możesz sprawdzić na stronie [Azure Portal,](https://portal.azure.com) czy żądane uprawnienia interfejsu API są przypisane do tożsamości zarządzanej.

Przejdź do **Azure Active Directory**, a następnie wybierz pozycję **Aplikacje dla przedsiębiorstw.** W tym okienku są wyświetlane wszystkie jednostki usługi w dzierżawie. W **chmurze Wszystkie** aplikacje wybierz jednostkę usługi dla tożsamości zarządzanej. 

Jeśli używasz tego samouczka, istnieją dwie jednostki usługi o tej samej nazwie wyświetlanej (na przykład SecureWebApp2020094113531). Nazwa główna usługi, która ma **adres URL strony głównej,** reprezentuje aplikację internetową w dzierżawie. Nazwa główna usługi bez adresu **URL strony głównej** reprezentuje tożsamość zarządzaną przypisaną przez system dla aplikacji internetowej. Wartość **Identyfikator obiektu** dla tożsamości zarządzanej jest taka, jak identyfikator obiektu utworzonej wcześniej tożsamości zarządzanej.

Wybierz jednostkę usługi dla tożsamości zarządzanej.

:::image type="content" alt-text="Zrzut ekranu przedstawiający opcję Wszystkie aplikacje." source="./media/scenario-secure-app-access-microsoft-graph/enterprise-apps-all-applications.png":::

Na **stronie** Przegląd **wybierz pozycję Uprawnienia,** a zobaczysz dodane uprawnienia dla Microsoft Graph.

:::image type="content" alt-text="Zrzut ekranu przedstawiający okienko Uprawnienia." source="./media/scenario-secure-app-access-microsoft-graph/enterprise-apps-permissions.png":::

## <a name="call-microsoft-graph-net"></a>Wywołanie Microsoft Graph (.NET)

Klasa [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) służy do uzyskania poświadczeń tokenu dla kodu w celu autoryzowania żądań do Microsoft Graph. Utwórz wystąpienie klasy [DefaultAzureCredential,](/dotnet/api/azure.identity.defaultazurecredential) które używa tożsamości zarządzanej do pobierania tokenów i dołączania ich do klienta usługi. Poniższy przykład kodu pobiera poświadczenia uwierzytelnionego tokenu i używa go do utworzenia obiektu klienta usługi, który pobiera użytkowników w grupie.

Aby zobaczyć ten kod jako część przykładowej aplikacji, zobacz przykład [w witrynie GitHub](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/3-WebApp-graphapi-managed-identity).

### <a name="install-the-microsoftidentitywebmicrosoftgraph-client-library-package"></a>Instalowanie pakietu biblioteki klienta Microsoft.Identity.Web.MicrosoftGraph

Zainstaluj pakiet [NuGet Microsoft.Identity.Web.MicrosoftGraph](https://www.nuget.org/packages/Microsoft.Identity.Web.MicrosoftGraph) w projekcie przy użyciu interfejsu wiersza polecenia platformy .NET Core lub konsoli Menedżer pakietów w programie Visual Studio.

# <a name="command-line"></a>[Wiersz polecenia](#tab/command-line)

Otwórz wiersz polecenia i przejdź do katalogu zawierającego plik projektu.

Uruchom polecenia instalacji.

```dotnetcli
dotnet add package Microsoft.Identity.Web.MicrosoftGraph
```

# <a name="package-manager"></a>[Menedżer pakietów](#tab/package-manager)

Otwórz projekt/rozwiązanie w Visual Studio, a następnie otwórz konsolę przy użyciu polecenia Narzędzia   >  **NuGet Menedżer pakietów**  >  **Menedżer pakietów Console.**

Uruchom polecenia instalacji.
```powershell
Install-Package Microsoft.Identity.Web.MicrosoftGraph
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

Jeśli po zakończeniu pracy z tym samouczkiem aplikacja internetowa lub skojarzone zasoby nie będą już potrzebne, wyczyść [utworzone zasoby.](scenario-secure-app-clean-up-resources.md)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Utwórz tożsamość zarządzaną przypisaną przez system w aplikacji internetowej.
> * Dodawanie Microsoft Graph interfejsu API do tożsamości zarządzanej.
> * Wywołaj Microsoft Graph z aplikacji internetowej przy użyciu tożsamości zarządzanych.

Dowiedz się, jak połączyć [aplikację .NET Core,](tutorial-dotnetcore-sqldb-app.md)aplikację w języku [Python,](tutorial-python-postgresql-app.md)aplikację [Java](tutorial-java-spring-cosmosdb.md) [ lubNode.js z](tutorial-nodejs-mongodb-app.md) bazą danych.
