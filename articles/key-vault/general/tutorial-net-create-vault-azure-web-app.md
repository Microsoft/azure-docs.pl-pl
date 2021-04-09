---
title: Samouczek — używanie Azure Key Vault z aplikacją sieci Web platformy Azure w programie .NET
description: W tym samouczku skonfigurujesz aplikację sieci Web platformy Azure w aplikacji ASP.NET Core, aby odczytać wpis tajny z magazynu kluczy.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 2960726cf687908e8e4aed9333fce490dd7ff006
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98788740"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-web-app-in-net"></a>Samouczek: łączenie Key Vault z aplikacją sieci Web platformy Azure w programie .NET przy użyciu tożsamości zarządzanej

[Azure Key Vault](./overview.md) zapewnia sposób przechowywania poświadczeń i innych wpisów tajnych ze zwiększonymi zabezpieczeniami. Jednak kod wymaga uwierzytelnienia, aby Key Vault je pobrać. [Zarządzane tożsamości dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md) pomagają rozwiązać ten problem, oferując usługi platformy Azure, które automatycznie zarządza tożsamość w Azure Active Directory (Azure AD). Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, w tym Key Vault, bez konieczności wyświetlania poświadczeń w kodzie.

W tym samouczku utworzysz i wdrożono aplikację sieci Web platformy Azure w celu [Azure App Service](../../app-service/overview.md). Tożsamość zarządzana zostanie użyta do uwierzytelnienia aplikacji sieci Web platformy Azure za pomocą magazynu kluczy platformy Azure przy użyciu [Azure Key Vault Secret Client Library dla platformy .NET](/dotnet/api/overview/azure/key-vault) i [interfejsu wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli). Te same podstawowe zasady mają zastosowanie w przypadku korzystania z wybranego języka deweloperskiego, Azure PowerShell i/lub Azure Portal.

Aby uzyskać więcej informacji na temat aplikacji sieci Web usługi Azure App Service i wdrożenia przedstawionych w tym samouczku, zobacz:
- [Omówienie usługi App Service](../../app-service/overview.md)
- [Tworzenie aplikacji sieci Web ASP.NET Core w programie Azure App Service](../../app-service/quickstart-dotnetcore.md)
- [Lokalne wdrożenie narzędzia Git do Azure App Service](../../app-service/deploy-local-git.md)

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Subskrypcja platformy Azure. [Utwórz je bezpłatnie.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Zestaw SDK platformy .NET Core 3,1 (lub nowszy)](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* Instalacja usługi [git](https://www.git-scm.com/downloads) .
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub [Azure PowerShell](/powershell/azure/).
* [Azure Key Vault.](./overview.md) Magazyn kluczy można utworzyć przy użyciu [Azure Portal](quick-create-portal.md), [interfejsu wiersza polecenia platformy Azure](quick-create-cli.md)lub [Azure PowerShell](quick-create-powershell.md).
* [Wpis tajny](../secrets/about-secrets.md)Key Vault. Wpis tajny można utworzyć przy użyciu [Azure Portal](../secrets/quick-create-portal.md), [programu PowerShell](../secrets/quick-create-powershell.md)lub [interfejsu wiersza polecenia platformy Azure](../secrets/quick-create-cli.md).

Jeśli masz już wdrożoną aplikację sieci Web w Azure App Service, możesz pominąć, aby [skonfigurować dostęp aplikacji sieci Web do magazynu kluczy](#create-and-assign-a-managed-identity) i [zmodyfikować sekcje kodu aplikacji sieci Web](#modify-the-app-to-access-your-key-vault) .

## <a name="create-a-net-core-app"></a>Tworzenie aplikacji .NET Core
W tym kroku skonfigurujesz lokalny projekt .NET Core.

W oknie terminalu na komputerze Utwórz katalog o nazwie `akvwebapp` i ustaw go jako bieżący katalog:

```bash
mkdir akvwebapp
cd akvwebapp
```

Utwórz aplikację platformy .NET Core przy użyciu [nowego polecenia sieci Web dotnet](/dotnet/core/tools/dotnet-new) :

```bash
dotnet new web
```

Uruchom aplikację lokalnie, aby wiedzieć, jak powinna wyglądać podczas wdrażania na platformie Azure:

```bash
dotnet run
```

W przeglądarce internetowej przejdź do aplikacji w witrynie `http://localhost:5000` .

Zostanie wyświetlony komunikat „Hello World!” komunikat z przykładowej aplikacji wyświetlanej na stronie.

Aby uzyskać więcej informacji na temat tworzenia aplikacji sieci Web dla platformy Azure, zobacz [Tworzenie aplikacji internetowej ASP.NET Core w programie Azure App Service](../../app-service/quickstart-dotnetcore.md)

## <a name="deploy-the-app-to-azure"></a>Wdrażanie aplikacji na platformie Azure

W tym kroku zostanie wdrożona aplikacja .NET Core do Azure App Service przy użyciu lokalnego narzędzia Git. Aby uzyskać więcej informacji na temat tworzenia i wdrażania aplikacji, zobacz [Tworzenie aplikacji internetowej ASP.NET Core na platformie Azure](../../app-service/quickstart-dotnetcore.md).

### <a name="configure-the-local-git-deployment"></a>Konfigurowanie lokalnego wdrożenia narzędzia Git

W oknie terminalu wybierz **kombinację klawiszy CTRL + C** , aby zamknąć serwer sieci Web.  Zainicjuj repozytorium git dla projektu .NET Core:

```bash
git init
git add .
git commit -m "first commit"
```

Za pomocą protokołu FTP i lokalnego narzędzia Git można wdrożyć aplikację internetową platformy Azure przy użyciu *użytkownika wdrożenia*. Po skonfigurowaniu użytkownika wdrożenia można go użyć do wszystkich wdrożeń platformy Azure. Nazwa użytkownika i hasło wdrożenia na poziomie konta różnią się od poświadczeń subskrypcji platformy Azure. 

Aby skonfigurować użytkownika wdrożenia, uruchom polecenie [AZ webapp Deployment User Set](/cli/azure/webapp/deployment/user?#az-webapp-deployment-user-set) . Wybierz nazwę użytkownika i hasło, które są zgodne z następującymi wskazówkami: 

- Nazwa użytkownika musi być unikatowa w obrębie platformy Azure. W przypadku lokalnych powiadomień wypychanych Git nie może zawierać symbolu at znaku (@). 
- Hasło musi mieć długość co najmniej ośmiu znaków i zawierać dwa z następujących trzech elementów: litery, cyfry i symbole. 

```azurecli-interactive
az webapp deployment user set --user-name "<username>" --password "<password>"
```

Dane wyjściowe JSON przedstawiają hasło jako `null` . Jeśli `'Conflict'. Details: 409` wystąpi błąd, Zmień nazwę użytkownika. Jeśli wystąpił błąd `'Bad Request'. Details: 400`, użyj silniejszego hasła. 

Zapisz nazwę użytkownika i hasło, aby można było używać go do wdrażania aplikacji sieci Web.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów to logiczny kontener służący do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórz grupę zasobów, która będzie zawierać zarówno Magazyn kluczy, jak i aplikację sieci Web za pomocą polecenia [AZ Group Create](/cli/azure/group?#az-group-create) :

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

### <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

Utwórz [plan App Service](../../app-service/overview-hosting-plans.md) przy użyciu interfejsu wiersza polecenia platformy Azure [AZ appService plan Create](/cli/azure/appservice/plan) . Poniższy przykład tworzy App Service plan o nazwie `myAppServicePlan` w `FREE` warstwie cenowej:

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

Po utworzeniu planu App Service interfejs wiersza polecenia platformy Azure wyświetli informacje podobne do tego, co widzisz tutaj:

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

Utwórz [aplikację internetową platformy Azure](../../app-service/overview.md) w `myAppServicePlan` planie App Service. 

> [!Important]
> Podobnie jak w przypadku magazynu kluczy, aplikacja internetowa platformy Azure musi mieć unikatową nazwę. Zastąp ciąg `<your-webapp-name>` nazwą aplikacji sieci Web w poniższych przykładach.


```azurecli-interactive
az webapp create --resource-group "myResourceGroup" --plan "myAppServicePlan" --name "<your-webapp-name>" --deployment-local-git
```

Po utworzeniu aplikacji sieci Web interfejs wiersza polecenia platformy Azure wyświetli dane wyjściowe podobne do tego, co widzisz tutaj:

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


Adres URL zdalnego narzędzia Git jest pokazywany we `deploymentLocalGitUrl` właściwości w formacie `https://<username>@<your-webapp-name>.scm.azurewebsites.net/<your-webapp-name>.git` . Zapisz ten adres URL. Będzie on potrzebny później.

Przejdź do nowej aplikacji przy użyciu poniższego polecenia. Zamień `<your-webapp-name>` na nazwę aplikacji.

```bash
https://<your-webapp-name>.azurewebsites.net
```

Zostanie wyświetlona domyślna strona internetowa dla nowej aplikacji sieci Web platformy Azure.

### <a name="deploy-your-local-app"></a>Wdrażanie aplikacji lokalnej

W lokalnym oknie terminala dodaj zdalną platformę Azure do lokalnego repozytorium Git. W poniższym poleceniu Zastąp ciąg `<deploymentLocalGitUrl-from-create-step>` adresem URL zdalnego narzędzia Git, który zapisano w sekcji [Tworzenie aplikacji sieci Web](#create-a-web-app) .

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Użyj poniższego polecenia, aby wypchnąć do zdalnej platformy Azure w celu wdrożenia aplikacji. Gdy Menedżer poświadczeń git prosi o poświadczenia, Użyj poświadczeń utworzonych w sekcji [Konfigurowanie lokalnego wdrożenia narzędzia Git](#configure-the-local-git-deployment) .

```bash
git push azure main
```

Wykonanie tego polecenia może potrwać kilka minut. W trakcie jego działania wyświetlane są informacje podobne do tego, co widzisz tutaj:
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

Przejdź do (lub Odśwież) wdrożonej aplikacji przy użyciu przeglądarki sieci Web:

```bash
http://<your-webapp-name>.azurewebsites.net
```

Zostanie wyświetlony komunikat „Hello World!” komunikat wyświetlony wcześniej podczas odwiedzin `http://localhost:5000` .

Aby uzyskać więcej informacji o wdrażaniu aplikacji sieci Web za pomocą narzędzia Git, zobacz [lokalne wdrożenie Git w Azure App Service](../../app-service/deploy-local-git.md)
 
## <a name="configure-the-web-app-to-connect-to-key-vault"></a>Skonfiguruj aplikację internetową w celu nawiązania połączenia z usługą Key Vault

W tej sekcji skonfigurujesz dostęp do sieci Web, aby Key Vault i zaktualizować kod aplikacji w celu pobrania klucza tajnego z Key Vault.

### <a name="create-and-assign-a-managed-identity"></a>Tworzenie i przypisywanie tożsamości zarządzanej

W tym samouczku użyjemy [tożsamości zarządzanej](../../active-directory/managed-identities-azure-resources/overview.md) do uwierzytelniania w Key Vault. Tożsamość zarządzana automatycznie zarządza poświadczeniami aplikacji.

Aby utworzyć tożsamość aplikacji w interfejsie wiersza polecenia platformy Azure, uruchom polecenie [AZ webapp-Identity Assign](/cli/azure/webapp/identity?#az-webapp-identity-assign) :

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

Aby nadać aplikacji sieci Web uprawnienia do **pobierania** i **wyświetlania listy** w magazynie kluczy, Przekaż `principalId` polecenie do interfejsu wiersza polecenia platformy Azure [AZ Key Set-Policy](/cli/azure/keyvault?#az-keyvault-set-policy) :

```azurecli-interactive
az keyvault set-policy --name "<your-keyvault-name>" --object-id "<principalId>" --secret-permissions get list
```

Zasady dostępu można także przypisywać przy użyciu [Azure Portal](./assign-access-policy-portal.md) lub [programu PowerShell](./assign-access-policy-powershell.md).

### <a name="modify-the-app-to-access-your-key-vault"></a>Modyfikowanie aplikacji w celu uzyskania dostępu do magazynu kluczy

W tym samouczku użyjesz [Azure Key Vault poufnej biblioteki klienta](/dotnet/api/overview/azure/security.keyvault.secrets-readme) w celach demonstracyjnych. Można również użyć [Azure Key Vault Biblioteka klienta certyfikatu](/dotnet/api/overview/azure/security.keyvault.certificates-readme)lub [Azure Key Vault bibliotekę klienta klucza](/dotnet/api/overview/azure/security.keyvault.keys-readme).

#### <a name="install-the-packages"></a>Zainstaluj pakiety

W oknie terminalu Zainstaluj bibliotekę klienta Azure Key Vault Secret dla platformy .NET i pakietów biblioteki klienta tożsamości platformy Azure:

```console
dotnet add package Azure.Identity
dotnet add package Azure.Security.KeyVault.Secrets
```

#### <a name="update-the-code"></a>Aktualizacja kodu

Znajdź i Otwórz plik Start. cs w projekcie akvwebapp. 

Dodaj te wiersze do nagłówka:

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
using Azure.Core;
```

Dodaj następujące wiersze przed `app.UseEndpoints` wywołaniem, aktualizując identyfikator URI w celu odzwierciedlenia `vaultUri` Twojego magazynu kluczy. Ten kod używa  [DefaultAzureCredential ()](/dotnet/api/azure.identity.defaultazurecredential) do uwierzytelniania w Key Vault, który używa tokenu z zarządzanej tożsamości do uwierzytelniania. Aby uzyskać więcej informacji na temat uwierzytelniania do Key Vault, zobacz [przewodnik dewelopera](./developers-guide.md#authenticate-to-key-vault-in-code). Kod używa również wykładniczej wycofywania do ponawiania prób w przypadku, gdy Key Vault jest ograniczany. Aby uzyskać więcej informacji na temat limitów transakcji Key Vault, zobacz [Azure Key Vault wskazówki dotyczące ograniczania przepływności](./overview-throttling.md).

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

Zaktualizuj wiersz, `await context.Response.WriteAsync("Hello World!");` aby wyglądał następująco:

```csharp
await context.Response.WriteAsync(secretValue);
```

Przed przejściem do następnego kroku upewnij się, że Zapisano zmiany.

#### <a name="redeploy-your-web-app"></a>Ponowne wdrażanie aplikacji internetowej

Teraz, po zaktualizowaniu kodu, możesz go wdrożyć ponownie na platformie Azure za pomocą tych poleceń git:

```bash
git add .
git commit -m "Updated web app to access my key vault"
git push azure main
```

## <a name="go-to-your-completed-web-app"></a>Przejdź do ukończonej aplikacji sieci Web

```bash
http://<your-webapp-name>.azurewebsites.net
```

Gdzie przed rozpoczęciem "Hello world!" powinna zostać wyświetlona wartość klucza tajnego.

## <a name="next-steps"></a>Następne kroki

- [Używanie Azure Key Vault z aplikacjami wdrożonymi na maszynie wirtualnej w środowisku .NET](./tutorial-net-virtual-machine.md)
- Dowiedz się więcej o [tożsamościach zarządzanych dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md)
- Wyświetl [przewodnik dewelopera](./developers-guide.md)
- [Bezpieczny dostęp do magazynu kluczy](./secure-your-key-vault.md)