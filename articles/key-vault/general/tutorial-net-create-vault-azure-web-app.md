---
title: Samouczek — używanie Azure Key Vault z platformą Azure webapp na platformie .NET | Microsoft Docs
description: W tym samouczku skonfigurujesz usługę i usługa Azure webapp w aplikacji ASP.NET Core w celu odczytywania wpisu tajnego z magazynu kluczy.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 05/06/2020
ms.author: mbaldwin
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: 4ed999e282aa9bcd80b000f3db2ecf9a8386a489
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95537955"
---
# <a name="tutorial-use-a-managed-identity-to-connect-key-vault-to-an-azure-web-app-with-net"></a>Samouczek: używanie tożsamości zarządzanej do łączenia Key Vault z aplikacją sieci Web platformy Azure przy użyciu platformy .NET

[Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview) zapewnia sposób bezpiecznego przechowywania poświadczeń i innych wpisów tajnych, ale Twój kod musi uwierzytelnić się, aby Key Vault je pobrać. [Tożsamości zarządzane dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md) pomagają rozwiązać ten problem, udostępniając usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure AD. Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, w tym Key Vault, bez konieczności wyświetlania poświadczeń w kodzie.

W tym samouczku jest stosowana tożsamość zarządzana do uwierzytelniania aplikacji internetowej platformy Azure za pomocą Azure Key Vault. Chociaż kroki używają [biblioteki klienta Azure Key Vault v4 dla platformy .NET](/dotnet/api/overview/azure/key-vault) i [interfejsu wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli), te same podstawowe zasady mają zastosowanie w przypadku korzystania z wybranego języka deweloperskiego, Azure PowerShell i/lub Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start:

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Zestaw .NET Core 3,1 SDK lub nowszy](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* [Zainstaluj narzędzie git](https://www.git-scm.com/downloads).
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) lub [Azure PowerShell](/powershell/azure/)
* [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview). Magazyn kluczy można utworzyć przy użyciu [Azure Portal](quick-create-portal.md), [interfejsu wiersza polecenia platformy Azure](quick-create-cli.md)lub [Azure PowerShell](quick-create-powershell.md).
* Key Vault [wpis tajny](https://docs.microsoft.com/azure/key-vault/secrets/about-secrets). Wpis tajny można utworzyć przy użyciu [Azure Portal](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal), [programu PowerShell](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-powershell)lub [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-cli)

## <a name="create-a-net-core-app-and-deploy-it-to-azure"></a>Tworzenie aplikacji platformy .NET Core i wdrażanie jej na platformie Azure
Ten krok umożliwia skonfigurowanie lokalnego projektu platformy .NET Core.

W oknie terminala na komputerze utwórz katalog o nazwie `akvwebapp` i zmień bieżący katalog na niego.

```bash
mkdir akvwebapp
cd akvwebapp
```

Teraz Utwórz nową aplikację platformy .NET Core przy użyciu [nowego polecenia sieci Web dotnet](/dotnet/core/tools/dotnet-new) :

```bash
dotnet new web
```

Uruchom aplikację lokalnie, aby zobaczyć, jak powinna ona wyglądać, gdy wdrożysz ją na platformie Azure. 

```bash
dotnet run
```

Otwórz przeglądarkę internetową i przejdź do aplikacji pod adresem `http://localhost:5000`.

Na stronie zostanie wyświetlony komunikat **Hello World** z przykładowej aplikacji.

## <a name="deploy-app-to-azure"></a>Wdrażanie aplikacji na platformie Azure

W tym kroku wdrażasz aplikację platformy .NET Core w celu App Service przy użyciu lokalnego narzędzia Git. Aby uzyskać więcej informacji na temat tworzenia i wdrażania aplikacji, zobacz [Tworzenie aplikacji internetowej ASP.NET Core na platformie Azure](https://docs.microsoft.com/azure/app-service/quickstart-dotnetcore)

### <a name="configure-local-git-deployment"></a>Konfigurowanie lokalnego wdrożenia narzędzia Git

W oknie terminalu naciśnij kombinację klawiszy **Ctrl + C**, aby zamknąć serwer sieci Web.  Inicjowanie repozytorium Git dla projektu platformy .NET Core.

```bash
git init
git add .
git commit -m "first commit"
```

Usługa FTP i lokalne narzędzia Git można wdrożyć w aplikacji sieci Web platformy Azure przy użyciu *użytkownika wdrożenia*. Po skonfigurowaniu użytkownika wdrożenia można go użyć do wszystkich wdrożeń platformy Azure. Nazwa użytkownika i hasło wdrożenia na poziomie konta różnią się od poświadczeń subskrypcji platformy Azure. 

Aby skonfigurować użytkownika wdrożenia, uruchom polecenie [AZ webapp Deployment User Set](/cli/azure/webapp/deployment/user?#az-webapp-deployment-user-set) . Wybierz nazwę użytkownika i hasło, które są zgodne z następującymi wskazówkami: 

- Nazwa użytkownika musi być unikatowa w ramach platformy Azure, a w przypadku lokalnych powiadomień wypychanych Git nie może zawierać symbolu "@". 
- Hasło musi składać się z co najmniej ośmiu znaków, a dwa z następujących trzech elementów: litery, cyfry i symbole. 

```azurecli-interactive
az webapp deployment user set --user-name "<username>" --password "<password>"
```

Dane wyjściowe JSON przedstawiają hasło jako `null` . Jeśli wystąpił błąd `'Conflict'. Details: 409`, zmień nazwę użytkownika. Jeśli wystąpił błąd `'Bad Request'. Details: 400`, użyj silniejszego hasła. 

Zapisz nazwę użytkownika i hasło, aby użyć do wdrożenia aplikacji sieci Web.

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórz grupę zasobów dla magazynu kluczy i aplikacji sieci Web za pomocą polecenia [AZ Group Create](/cli/azure/group?#az-group-create) :

```azurecli-interactive
az group create --name "myResourceGroup" -l "EastUS"
```

### <a name="create-an-app-service-plan"></a>Tworzenie planu usługi App Service

Utwórz [plan App Service](https://docs.microsoft.com/azure/app-service/overview-hosting-plans) przy użyciu interfejsu wiersza polecenia platformy Azure [AZ appService plan Create](/cli/azure/appservice/plan) . Poniższy przykład tworzy App Service plan o nazwie `myAppServicePlan` w warstwie cenowej **bezpłatna** :

```azurecli-interactive
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku FREE
```

Po utworzeniu planu usługi App Service interfejs wiersza polecenia platformy Azure wyświetli informacje podobne do następujących:

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

Aby uzyskać więcej informacji na temat zarządzania planami App Service, zobacz [Zarządzanie planem App Service na platformie Azure](https://docs.microsoft.com/azure/app-service/app-service-plan-manage)

### <a name="create-a-web-app"></a>Tworzenie aplikacji internetowej

Utwórz [aplikację internetową platformy Azure](../../app-service/overview.md) w `myAppServicePlan` planie App Service. 

> [!Important]
> Podobnie jak w przypadku Key Vault, aplikacja internetowa platformy Azure musi mieć unikatową nazwę. Zastąp ciąg \<your-webapp-name\> nazwą aplikacji sieci Web poniższymi przykładami.


```azurecli-interactive
az webapp create --resource-group "myResourceGroup" --plan "myAppServicePlan" --name "<your-webapp-name>" --deployment-local-git
```

Po utworzeniu aplikacji internetowej w interfejsie wiersza polecenia platformy Azure zostaną wyświetlone dane wyjściowe podobne do następujących:

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


Adres URL zdalnego repozytorium Git jest wyświetlany we właściwości `deploymentLocalGitUrl` w formacie `https://<username>@<your-webapp-name>.scm.azurewebsites.net/<your-webapp-name>.git`. Zapisz ten adres URL, ponieważ będzie on potrzebny później.

Przejdź do nowo utworzonej aplikacji. Zastąp _&lt; ciąg-webapp-Name>_ nazwą swojej aplikacji.

```bash
https://<your-webapp-name>.azurewebsites.net
```

Zostanie wyświetlona domyślna strona sieci Web dla nowo utworzonej aplikacji internetowej platformy Azure.

### <a name="deploy-your-local-app"></a>Wdrażanie aplikacji lokalnej

Wróć do okna terminalu lokalnego, Dodaj element zdalny platformy Azure do lokalnego repozytorium git, zastępując *\<deploymentLocalGitUrl-from-create-step>* adres URL zdalnego narzędzia Git zapisanego w kroku [Tworzenie aplikacji sieci Web](#create-a-web-app) .

```bash
git remote add azure <deploymentLocalGitUrl-from-create-step>
```

Wypchnij na zdalną platformę Azure w celu wdrożenia aplikacji za pomocą następującego polecenia. Gdy Menedżer poświadczeń git prosi o poświadczenia, Użyj poświadczeń utworzonych w sekcji [Konfigurowanie lokalnego wdrożenia narzędzia Git](#configure-local-git-deployment) .

```bash
git push azure master
```

Wykonanie tego polecenia może potrwać kilka minut. Podczas wykonywania polecenie wyświetli informacje podobne do następującego przykładu:
<pre>
Enumerating objects: 5, done.
Counting objects: 100% (5/5), done.
Compressing objects: 100% (3/3), done.
Writing objects: 100% (3/3), 285 bytes | 95.00 KiB/s, done.
Total 3 (delta 2), reused 0 (delta 0), pack-reused 0
remote: Deploy Async
remote: Updating branch 'master'.
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
   d87e6ca..d6b5447  master -> master
</pre>

Przejdź do (lub Odśwież) wdrożonej aplikacji przy użyciu przeglądarki sieci Web.

```bash
http://<your-webapp-name>.azurewebsites.net
```

Zobaczysz "Hello world!" komunikat, który został wcześniej wyświetlony podczas odwiedzania `http://localhost:5000` .
 
## <a name="configure-web-app-to-connect-to-key-vault"></a>Skonfiguruj aplikację internetową w celu nawiązania połączenia z usługą Key Vault

W tej sekcji utworzysz dostęp do sieci Web do magazynu kluczy i zaktualizujesz kod aplikacji w celu pobrania klucza tajnego z magazynu kluczy.

### <a name="create-and-assign-a-managed-identity"></a>Tworzenie i przypisywanie tożsamości zarządzanej

W tym samouczku będziemy używać [tożsamości zarządzanej](../../active-directory/managed-identities-azure-resources/overview.md) przez aplikację do uwierzytelniania w magazynie kluczy, który automatycznie zarządza poświadczeniami aplikacji.

W interfejsie wiersza polecenia platformy Azure, aby utworzyć tożsamość dla tej aplikacji, uruchom polecenie [AZ webapp-Identity Assign](/cli/azure/webapp/identity?#az-webapp-identity-assign) :

```azurecli-interactive
az webapp identity assign --name "<your-webapp-name>" --resource-group "myResourceGroup"
```

Operacja zwróci ten fragment kodu JSON:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

Aby nadać aplikacji sieci Web uprawnienia do **pobierania** i **wyświetlania listy** w magazynie kluczy, Przekaż principalID do interfejsu wiersza polecenia platformy Azure [AZ Key Set-Policy](/cli/azure/keyvault?#az-keyvault-set-policy) :

```azurecli-interactive
az keyvault set-policy --name "<your-keyvault-name>" --object-id "<principalId>" --secret-permissions get list
```

Zasady dostępu można także przypisywać przy użyciu [Azure Portal](https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-portal) lub [programu PowerShell](https://docs.microsoft.com/azure/key-vault/general/assign-access-policy-powershell).

### <a name="modify-the-app-to-access-your-key-vault"></a>Modyfikowanie aplikacji w celu uzyskania dostępu do magazynu kluczy

#### <a name="install-the-packages"></a>Zainstaluj pakiety

W oknie terminalu Zainstaluj Azure Key Vault bibliotekę kliencką dla pakietów .NET:

```console
dotnet add package Azure.Identity
dotnet add package Azure.Security.KeyVault.Secrets
```

#### <a name="update-the-code"></a>Aktualizacja kodu

Znajdź i Otwórz plik Startup.cs w projekcie akvwebapp. 

Dodaj te dwa wiersze do nagłówka:

```csharp
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
using Azure.Core;
```

Dodaj te wiersze przed `app.UseEndpoints` wywołaniem, aktualizując identyfikator URI w celu odzwierciedlenia `vaultUri` Twojego magazynu kluczy. Poniższy kod używa  [elementu "DefaultAzureCredential ()"](/dotnet/api/azure.identity.defaultazurecredential) do uwierzytelniania w magazynie kluczy, który używa tokenu z tożsamości zarządzanej przez aplikację do uwierzytelniania. Aby uzyskać więcej informacji na temat uwierzytelniania do magazynu kluczy, zobacz [przewodnik dewelopera](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code). Jest również używany wycofywania wykładniczy do ponawiania prób w przypadku ograniczania magazynu kluczy. Aby uzyskać więcej informacji na temat limitów transakcji magazynu kluczy, zobacz [wskazówki dotyczące ograniczania przepustowości Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/overview-throttling)

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

Zaktualizuj wiersz `await context.Response.WriteAsync("Hello World!");` do odczytania:

```csharp
await context.Response.WriteAsync(secretValue);
```

Przed przejściem do następnego kroku należy upewnić się, że zostały zapisane zmiany.

#### <a name="redeploy-your-web-app"></a>Ponowne wdrażanie aplikacji internetowej

Po zaktualizowaniu kodu można wdrożyć go ponownie na platformie Azure przy użyciu następujących poleceń git:

```bash
git add .
git commit -m "Updated web app to access my key vault"
git push azure master
```

### <a name="visit-your-completed-web-app"></a>Odwiedź ukończoną aplikację sieci Web

```bash
http://<your-webapp-name>.azurewebsites.net
```

Gdzie przed rozpoczęciem **Hello World** powinna zostać wyświetlona wartość wpisu tajnego: **sukces!**

## <a name="next-steps"></a>Następne kroki

- [Używanie Azure Key Vault z aplikacjami wdrożonymi na maszynie wirtualnej w programie .NET](https://docs.microsoft.com/azure/key-vault/general/tutorial-net-virtual-machine)
- Dowiedz się więcej o [tożsamościach zarządzanych dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md)
- Dowiedz się więcej o [tożsamościach zarządzanych dla App Service](../../app-service/overview-managed-identity.md?tabs=dotnet)
- [Przewodnik dewelopera](https://docs.microsoft.com/azure/key-vault/general/developers-guide)
- [Bezpieczny dostęp do magazynu kluczy](https://docs.microsoft.com/azure/key-vault/general/secure-your-key-vault)


