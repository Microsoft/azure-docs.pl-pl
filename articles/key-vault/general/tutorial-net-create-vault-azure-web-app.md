---
title: Samouczek — używanie Azure Key Vault z aplikacją internetową platformy Azure na platformie .NET
description: W tym samouczku skonfigurujesz aplikację internetową platformy Azure w aplikacji ASP.NET Core, aby odczytać klucz tajny z magazynu kluczy.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 479181e851e6f54246a2ad89e7529bf3f50bb8a4
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751980"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-web-app-in-net"></a>Samouczek: używanie tożsamości zarządzanej do nawiązywania połączenia Key Vault z aplikacją internetową platformy Azure na platformie .NET

[Azure Key Vault](./overview.md) umożliwia przechowywanie poświadczeń i innych wpisów tajnych ze zwiększonymi zabezpieczeniami. Jednak kod musi uwierzytelniać się, aby Key Vault je pobrać. [Tożsamości zarządzane dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md) pomagają rozwiązać ten problem, nadając usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure Active Directory (Azure AD). Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze obsługującej uwierzytelnianie usługi Azure AD, w tym Key Vault, bez konieczności wyświetlania poświadczeń w kodzie.

W tym samouczku utworzysz i wdrożysz aplikację internetową platformy Azure w [usłudze Azure App Service.](../../app-service/overview.md) Tożsamości zarządzanej użyjesz do uwierzytelniania aplikacji internetowej platformy Azure w usłudze Azure Key Vault przy użyciu Azure Key Vault tajnej biblioteki klienta dla platformy [.NET](/dotnet/api/overview/azure/key-vault) i interfejsu [wiersza polecenia platformy Azure.](/cli/azure/get-started-with-azure-cli) Te same podstawowe zasady mają zastosowanie w przypadku używania wybranego języka programowania, Azure PowerShell i/lub Azure Portal.

Aby uzyskać więcej informacji na temat aplikacji internetowych i wdrożeń usługi Azure App Service przedstawionych w tym samouczku, zobacz:
- [Omówienie usługi App Service](../../app-service/overview.md)
- [Tworzenie aplikacji ASP.NET Core w programie Azure App Service](../../app-service/quickstart-dotnetcore.md)
- [Lokalne wdrożenie usługi Git w Azure App Service](../../app-service/deploy-local-git.md)

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Subskrypcja platformy Azure. [Utwórz je bezpłatnie.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Zestaw SDK platformy .NET Core 3.1 (lub nowszy).](https://dotnet.microsoft.com/download/dotnet-core/3.1)
* Instalacja [usługi Git](https://www.git-scm.com/downloads) w wersji 2.28.0 lub większej.
* Interfejs [wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub [Azure PowerShell](/powershell/azure/).
* [Azure Key Vault.](./overview.md) Magazyn kluczy można utworzyć przy użyciu interfejsu [Azure Portal](quick-create-portal.md), interfejsu wiersza [polecenia platformy Azure](quick-create-cli.md)lub [Azure PowerShell](quick-create-powershell.md).
* Klucz Key Vault [tajny.](../secrets/about-secrets.md) Klucz tajny można utworzyć przy użyciu narzędzia [Azure Portal](../secrets/quick-create-portal.md), [programu PowerShell](../secrets/quick-create-powershell.md)lub interfejsu wiersza [polecenia platformy Azure.](../secrets/quick-create-cli.md)

Jeśli twoja aplikacja internetowa została już wdrożona w programie [](#create-and-assign-a-managed-identity) Azure App Service, możesz od razu przejść do konfigurowania dostępu aplikacji internetowej do magazynu kluczy i modyfikowania [sekcji kodu aplikacji](#modify-the-app-to-access-your-key-vault) internetowej.

## <a name="create-a-net-core-app"></a>Tworzenie aplikacji .NET Core
W tym kroku skonfigurujesz lokalny projekt .NET Core.

W oknie terminalu na maszynie utwórz katalog o nazwie `akvwebapp` i nadaj temu katalogowi nazwę:

```bash
mkdir akvwebapp
cd akvwebapp
```

Utwórz aplikację .NET Core za pomocą polecenia [dotnet new web:](/dotnet/core/tools/dotnet-new)

```bash
dotnet new web
```

Uruchom aplikację lokalnie, aby wiedzieć, jak powinna wyglądać po wdrożeniu na platformie Azure:

```bash
dotnet run
```

W przeglądarce internetowej przejdź do aplikacji pod adres `http://localhost:5000` .

Zostanie wyświetlony komunikat „Hello World!” z przykładowej aplikacji wyświetlanej na stronie.

Aby uzyskać więcej informacji na temat tworzenia aplikacji internetowych dla platformy Azure, zobacz Tworzenie aplikacji internetowej [ASP.NET Core w usłudze Azure App Service](../../app-service/quickstart-dotnetcore.md)

## <a name="deploy-the-app-to-azure"></a>Wdrażanie aplikacji na platformie Azure

W tym kroku wdrożysz aplikację .NET Core na platformie Azure App Service pomocą lokalnego narzędzia Git. Aby uzyskać więcej informacji na temat tworzenia i wdrażania aplikacji, zobacz Create an ASP.NET Core web app in Azure (Tworzenie aplikacji internetowej [ASP.NET Core na platformie Azure).](../../app-service/quickstart-dotnetcore.md)

### <a name="configure-the-local-git-deployment"></a>Konfigurowanie lokalnego wdrożenia usługi Git

W oknie terminalu naciśnij **klawisze Ctrl+C,** aby zamknąć serwer internetowy.  Zainicjuj repozytorium Git dla projektu .NET Core:

```bash
git init --initial-branch=main
git add .
git commit -m "first commit"
```

Za pomocą protokołu FTP i lokalnego narzędzia Git można wdrożyć aplikację internetową platformy Azure przy użyciu *użytkownika wdrożenia.* Po skonfigurowaniu użytkownika wdrożenia można go używać we wszystkich wdrożeniach platformy Azure. Nazwa użytkownika i hasło wdrożenia na poziomie konta różnią się od poświadczeń subskrypcji platformy Azure. 

Aby skonfigurować użytkownika wdrożenia, uruchom [polecenie az webapp deployment user set.](/cli/azure/webapp/deployment/user?#az-webapp-deployment-user-set) Wybierz nazwę użytkownika i hasło zgodne z tymi wytycznymi: 

- Nazwa użytkownika musi być unikatowa w obrębie platformy Azure. W przypadku lokalnych wypchnięć git nie może zawierać symbolu @. 
- Hasło musi mieć co najmniej osiem znaków i zawierać dwa z następujących trzech elementów: litery, cyfry i symbole. 

```azurecli-interactive
az webapp deployment user set --user-name "<username>" --password "<password>"
```

Dane wyjściowe JSON pokazują hasło jako `null` . Jeśli wystąpi `'Conflict'. Details: 409` błąd, zmień nazwę użytkownika. Jeśli wystąpił błąd `'Bad Request'. Details: 400`, użyj silniejszego hasła. 

Zanotuj nazwę użytkownika i hasło, aby można było ich używać do wdrażania aplikacji internetowych.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów to logiczny kontener, w którym wdrażasz zasoby platformy Azure i zarządzasz nimi. Utwórz grupę zasobów, która będzie zawierać zarówno magazyn kluczy, jak i aplikację internetową, za pomocą [polecenia az group create:](/cli/azure/group?#az-group-create)

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

### <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

Utwórz plan [App Service za](../../app-service/overview-hosting-plans.md) pomocą polecenia [az appservice plan create](/cli/azure/appservice/plan) interfejsu wiersza polecenia platformy Azure. Poniższy przykład tworzy plan App Service o `myAppServicePlan` nazwie w `FREE` warstwie cenowej:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

Po utworzeniu App Service interfejs wiersza polecenia platformy Azure wyświetli informacje podobne do tego, co widzisz tutaj:

<pre>
{ 
  "adminSiteName": null,
  "appServicePlanName": "myAppServicePlan",
  "geoRegion": "West Europe",
  "hostingEnvironmentProfile": null,
  "id": "/subscriptions/0000-0000/resourceGroups/myResourceGroup/providers/Microsoft.Web/serverfarms/myAppServicePlan",
  "kind": "app",
  "location": "West Europe",
  "maximumNumberOfWorkers": 1,
  "name": "myAppServicePlan",
  &lt; JSON data removed for brevity. &gt;
  "targetWorkerSizeId": 0,
  "type": "Microsoft.Web/serverfarms",
  "workerTierName": null
} 
</pre>

Aby uzyskać więcej informacji, zobacz [Zarządzanie planem usługi App Service na platformie Azure](../../app-service/app-service-plan-manage.md).

### <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

Utwórz aplikację [internetową platformy Azure](../../app-service/overview.md) w `myAppServicePlan` planie App Service usługi. 

> [!Important]
> Podobnie jak magazyn kluczy aplikacja internetowa platformy Azure musi mieć unikatową nazwę. W poniższych przykładach zastąp nazwą swojej aplikacji `<your-webapp-name>` internetowej.


```azurecli-interactive
az webapp create --resource-group "myResourceGroup" --plan "myAppServicePlan" --name "<your-webapp-name>" --deployment-local-git
```

Po utworzeniu aplikacji internetowej interfejs wiersza polecenia platformy Azure wyświetla dane wyjściowe podobne do tych, które widać tutaj:

<pre>
Local git is configured with url of 'https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;ayour-webapp-name&gt;.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "clientCertExclusionPaths": null,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "&lt;your-webapp-name&gt;.azurewebsites.net",
  "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;your-webapp-name&gt;.scm.azurewebsites.net/&lt;your-webapp-name&gt;.git",
  "enabled": true,
  &lt; JSON data removed for brevity. &gt;
}
</pre>

Adres URL zdalnego repozytorium Git jest wyświetlany we `deploymentLocalGitUrl` właściwości w formacie `https://<username>@<your-webapp-name>.scm.azurewebsites.net/<your-webapp-name>.git` . Zapisz ten adres URL. Będzie on potrzebny później.

Teraz skonfiguruj aplikację internetową do wdrożenia z gałęzi `main` :

```azurecli-interactive
 az webapp config appsettings set -g MyResourceGroup -name "<your-webapp-name>"--settings deployment_branch=main
```

Przejdź do nowej aplikacji, używając następującego polecenia. Zastąp `<your-webapp-name>` nazwą swojej aplikacji.

```bash
https://<your-webapp-name>.azurewebsites.net
```

Zostanie zobaczysz domyślną stronę internetową dla nowej aplikacji internetowej platformy Azure.

### <a name="deploy-your-local-app"></a>Wdrażanie aplikacji lokalnej

W lokalnym oknie terminala dodaj zdalną platformę Azure do lokalnego repozytorium Git. W poniższym poleceniu zastąp adresem URL zdalnego repozytorium Git zapisanego w sekcji `<deploymentLocalGitUrl-from-create-step>` [Tworzenie aplikacji](#create-a-web-app) internetowej.

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Użyj następującego polecenia, aby wypchnąć do zdalnego serwera Azure w celu wdrożenia aplikacji. Gdy usługa Git Menedżer poświadczeń monituje o poświadczenia, użyj poświadczeń utworzonych w sekcji Konfigurowanie [lokalnego wdrożenia usługi Git.](#configure-the-local-git-deployment)

```bash
git push azure main
```

Uruchomienie tego polecenia może potrwać kilka minut. Podczas jego pracy są wyświetlane informacje podobne do tych, które są tutaj wyświetlane:
<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'main'.
remote: Updating submodules.
remote: Preparing deployment for commit id 'd6b54472f7'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
remote:
remote: Oryx Version      : 0.2.20200114.13, Commit: 204922f30f8e8d41f5241b8c218425ef89106d1d, ReleaseTagName: 20200114.13
remote: Build Operation ID: |imoMY2y77/s=.40ca2a87_
remote: Repository Commit : d6b54472f7e8e9fd885ffafaa64522e74cf370e1
.
.
.
remote: Deployment successful.
remote: Deployment Logs : 'https://&lt;your-webapp-name&gt;.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/d6b54472f7e8e9fd885ffafaa64522e74cf370e1/log'
To https://&lt;your-webapp-name&gt;.scm.azurewebsites.net:443/&lt;your-webapp-name&gt;.git
   d87e6ca..d6b5447  main -> main
</pre>

Przejdź do wdrożonej aplikacji (lub odśwież go) przy użyciu przeglądarki internetowej:

```bash
http://<your-webapp-name>.azurewebsites.net
```

Zostanie wyświetlony komunikat „Hello World!” komunikat, który został wyświetlony wcześniej podczas odwiedzania strony `http://localhost:5000` .

Aby uzyskać więcej informacji na temat wdrażania aplikacji internetowej przy użyciu usługi Git, zobacz [Lokalne wdrażanie usługi Git w usłudze Azure App Service](../../app-service/deploy-local-git.md)
 
## <a name="configure-the-web-app-to-connect-to-key-vault"></a>Konfigurowanie aplikacji internetowej w celu nawiązania połączenia z Key Vault

W tej sekcji skonfigurujesz dostęp do Internetu w celu Key Vault i zaktualizujesz kod aplikacji, aby pobrać klucz tajny z Key Vault.

### <a name="create-and-assign-a-managed-identity"></a>Tworzenie i przypisywanie tożsamości zarządzanej

W tym samouczku użyjemy tożsamości [zarządzanej do](../../active-directory/managed-identities-azure-resources/overview.md) uwierzytelniania w Key Vault. Tożsamość zarządzana automatycznie zarządza poświadczeniami aplikacji.

Aby utworzyć tożsamość aplikacji w interfejsie wiersza polecenia platformy Azure, uruchom [polecenie az webapp-identity assign:](/cli/azure/webapp/identity?#az-webapp-identity-assign)

```azurecli-interactive
az webapp identity assign --name "<your-webapp-name>" --resource-group "myResourceGroup"
```

Polecenie zwróci ten fragment kodu JSON:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

Aby nadać aplikacji internetowej  uprawnienia do operacji get i **list** w magazynie kluczy, przekaż polecenie `principalId` az [keyvault set-policy](/cli/azure/keyvault?#az-keyvault-set-policy) interfejsu wiersza polecenia platformy Azure:

```azurecli-interactive
az keyvault set-policy --name "<your-keyvault-name>" --object-id "<principalId>" --secret-permissions get list
```

Zasady dostępu można także przypisać przy użyciu narzędzia [Azure Portal](./assign-access-policy-portal.md) lub [programu PowerShell.](./assign-access-policy-powershell.md)

### <a name="modify-the-app-to-access-your-key-vault"></a>Modyfikowanie aplikacji w celu uzyskania dostępu do magazynu kluczy

W tym samouczku do celów demonstracyjnych [użyjesz Azure Key Vault tajnej](/dotnet/api/overview/azure/security.keyvault.secrets-readme) biblioteki klienckiej. Można również użyć [Azure Key Vault klienta certyfikatu](/dotnet/api/overview/azure/security.keyvault.certificates-readme)lub Azure Key Vault [biblioteki klienta klucza.](/dotnet/api/overview/azure/security.keyvault.keys-readme)

#### <a name="install-the-packages"></a>Instalowanie pakietów

W oknie terminalu zainstaluj Azure Key Vault klienta dla pakietów bibliotek klienta tożsamości platformy .NET i platformy Azure:

```console
dotnet add package Azure.Identity
dotnet add package Azure.Security.KeyVault.Secrets
```

#### <a name="update-the-code"></a>Aktualizacja kodu

Znajdź i otwórz plik Startup.cs w projekcie akvwebapp. 

Dodaj następujące wiersze do nagłówka:

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
using Azure.Core;
```

Dodaj następujące wiersze przed `app.UseEndpoints` wywołaniem , aktualizując adres URI w celu `vaultUri` odzwierciedlenia klucza magazynu kluczy. Ten kod używa  [wartości DefaultAzureCredential()](/dotnet/api/azure.identity.defaultazurecredential) do uwierzytelniania Key Vault, która używa tokenu z tożsamości zarządzanej do uwierzytelniania. Aby uzyskać więcej informacji na temat uwierzytelniania Key Vault, zobacz [Developer's Guide (Przewodnik dewelopera).](./developers-guide.md#authenticate-to-key-vault-in-code) Kod używa również wykładniczego odgałęzienia dla ponownych prób w przypadku Key Vault jest ograniczana. Aby uzyskać więcej informacji na Key Vault limitów transakcji, [zobacz Azure Key Vault throttling guidance (Wskazówki dotyczące ograniczania przepustowości).](./overview-throttling.md)

```csharp
SecretClientOptions options = new SecretClientOptions()
    {
        Retry =
        {
            Delay= TimeSpan.FromSeconds(2),
            MaxDelay = TimeSpan.FromSeconds(16),
            MaxRetries = 5,
            Mode = RetryMode.Exponential
         }
    };
var client = new SecretClient(new Uri("https://<your-unique-key-vault-name>.vault.azure.net/"), new DefaultAzureCredential(),options);

KeyVaultSecret secret = client.GetSecret("<mySecret>");

string secretValue = secret.Value;
```

Zaktualizuj wiersz, `await context.Response.WriteAsync("Hello World!");` aby wyglądał jak ten wiersz:

```csharp
await context.Response.WriteAsync(secretValue);
```

Pamiętaj o zapisaniu zmian przed kontynuowaniem do następnego kroku.

#### <a name="redeploy-your-web-app"></a>Ponowne wdrażanie aplikacji internetowej

Teraz, gdy kod został zaktualizowany, możesz go ponownie wdusić na platformie Azure przy użyciu tych poleceń usługi Git:

```bash
git add .
git commit -m "Updated web app to access my key vault"
git push azure main
```

## <a name="go-to-your-completed-web-app"></a>Przejdź do ukończonej aplikacji internetowej

```bash
http://<your-webapp-name>.azurewebsites.net
```

Tam, gdzie wcześniej był Hello world", powinna być teraz wyświetlana wartość twojego tajnego.

## <a name="next-steps"></a>Następne kroki

- [Używanie Azure Key Vault z aplikacjami wdrożonymi na maszynie wirtualnej na platformie .NET](./tutorial-net-virtual-machine.md)
- Dowiedz się więcej o [tożsamościach zarządzanych dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md)
- Wyświetl [Przewodnik dewelopera](./developers-guide.md)
- [Bezpieczny dostęp do magazynu kluczy](./security-overview.md)