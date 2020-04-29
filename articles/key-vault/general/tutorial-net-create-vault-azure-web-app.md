---
title: Samouczek — korzystanie z usługi Azure Key Vault za pomocą aplikacji internetowej platformy Azure na platformie .NET | Microsoft Docs
description: W tym samouczku skonfigurujesz aplikację ASP.NET Core w celu odczytu wpisu tajnego z magazynu kluczy.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: bb907c809d411128ee799d5057379a2022144882
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81422879"
---
# <a name="tutorial-use-azure-key-vault-with-an-azure-web-app-in-net"></a>Samouczek: Korzystanie z usługi Azure Key Vault za pomocą aplikacji internetowej platformy Azure na platformie .NET

Usługa Azure Key Vault umożliwia ochronę wpisów tajnych, takich jak klucze interfejsu API i parametry połączenia bazy danych. Umożliwia dostęp do aplikacji, usług i zasobów informatycznych.

W tym samouczku dowiesz się, jak utworzyć aplikację internetową platformy Azure, która umożliwia odczytanie informacji z usługi Azure Key Vault. W tym procesie używane są tożsamości zarządzane dla zasobów platformy Azure. Aby uzyskać więcej informacji na temat aplikacji internetowych platformy Azure, zobacz [Azure App Service](../../app-service/overview.md).

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Tworzenie magazynu kluczy.
> * Dodawanie wpisu tajnego do magazynu kluczy.
> * Pobieranie wpisu tajnego z magazynu kluczy.
> * Utwórz aplikację internetową platformy Azure.
> * Włączanie tożsamości zarządzanej dla aplikacji internetowej.
> * Przypisz uprawnienie do aplikacji sieci Web.
> * Uruchom aplikację sieci Web na platformie Azure.

Przed rozpoczęciem Przeczytaj [Key Vault podstawowe pojęcia](basic-concepts.md). 

Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

* Dla systemu Windows: [zestaw .NET Core 2,1 SDK lub nowszy](https://www.microsoft.com/net/download/windows)
* Dla komputerów Mac: [Visual Studio dla komputerów Mac](https://visualstudio.microsoft.com/vs/mac/)
* Dla systemów Windows, Mac i Linux:
  * [Git](https://git-scm.com/downloads)
  * Ten samouczek wymaga uruchomienia interfejsu wiersza polecenia platformy Azure lokalnie. Musisz mieć zainstalowany interfejs wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja wiersza polecenia lub jego uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).
  * [.NET Core](https://www.microsoft.com/net/download/dotnet-core/2.1)

## <a name="about-managed-service-identity"></a>Informacje o tożsamości usługi zarządzanej

Azure Key Vault przechowuje poświadczenia bezpiecznie, więc nie są wyświetlane w kodzie. Aby jednak pobrać klucze, musisz uwierzytelnić się w usłudze Azure Key Vault. W tym celu potrzebujesz poświadczenia. To klasyczny przykład dylematu dotyczącego uruchamiania. Tożsamość usługi zarządzanej rozwiązuje ten problem poprzez zapewnienie _tożsamości uruchamiania_, która upraszcza ten proces.

Po włączeniu MSI dla usługi platformy Azure, takiej jak Azure Virtual Machines, Azure App Service lub Azure Functions, platforma Azure tworzy jednostkę [usługi](basic-concepts.md). Plik MSI robi to w przypadku wystąpienia usługi w Azure Active Directory (Azure AD) i wprowadza poświadczenia nazwy głównej usługi do tego wystąpienia.

![Diagram dotyczący tożsamości usługi zarządzanej](../media/MSI.png)

Następnie w celu uzyskania tokenu dostępu kod wywołuje lokalną usługę metadanych, która jest dostępna w ramach zasobu platformy Azure. W celu uwierzytelnienia w usłudze Azure Key Vault kod używa tokenu dostępu, który otrzymuje z lokalnego punktu końcowego tożsamości usługi zarządzanej.

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Aby zalogować się do platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure, wpisz:

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi.

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create).

Następnie wybierz nazwę grupy zasobów i wypełnij symbol zastępczy. Poniższy przykład obejmuje tworzenie grupy zasobów w lokalizacji Zachodnie stany USA:

   ```azurecli
   # To list locations: az account list-locations --output table
   az group create --name "<YourResourceGroupName>" --location "West US"
   ```

Możesz używać tej grupy zasobów w całym samouczku.

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy

Aby utworzyć magazyn kluczy w grupie zasobów, należy podać następujące informacje:

* Nazwa magazynu kluczy: ciąg od 3 do 24 znaków, który może zawierać tylko cyfry (0-9), litery (a-z, A-Z) i łączniki (-)
* Nazwa grupy zasobów
* Lokalizacja: **zachodnie stany USA**

W interfejsie wiersza polecenia platformy Azure wprowadź następujące polecenie:

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

W tym momencie Twoje konto platformy Azure jest jedyną osobą, która ma uprawnienia do wykonywania operacji na tym nowym magazynie.

## <a name="add-a-secret-to-the-key-vault"></a>Dodawanie wpisu tajnego do magazynu kluczy

Teraz możesz dodać wpis tajny. Mogą to być parametry połączenia SQL lub inne informacje, które muszą być przechowywane bezpiecznie, ale jednocześnie muszą być dostępne dla aplikacji.

Aby utworzyć wpis tajny w magazynie kluczy o nazwie **AppSecret**, wprowadź następujące polecenie: 

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Ten wpis tajny zawiera wartość **MySecret**.

Aby wyświetlić wartość, która jest zawarta w kluczu tajnym jako zwykły tekst, wprowadź następujące polecenie:

```azurecli
az keyvault secret show --name "AppSecret" --vault-name "<YourKeyVaultName>"
```

To polecenie wyświetla informacje o kluczu tajnym, w tym identyfikator URI. 

Po wykonaniu tych kroków będziesz mieć identyfikator URI wpisu tajnego w magazynie kluczy. Zanotuj te informacje do późniejszego użycia w tym samouczku. 

## <a name="create-a-net-core-web-app"></a>Tworzenie nowej aplikacji internetowej platformy .NET Core

Aby utworzyć aplikację internetową platformy .NET Core i opublikować ją na platformie Azure, postępuj zgodnie z instrukcjami zawartymi w temacie [Tworzenie aplikacji internetowej ASP.NET Core na platformie Azure](../../app-service/app-service-web-get-started-dotnet.md). 

Możesz również obejrzeć to wideo:

>[!VIDEO https://www.youtube.com/embed/EdiiEH7P-bU]

## <a name="open-and-edit-the-solution"></a>Otwieranie i edytowanie rozwiązania

1. Przejdź do pliku **Pages** > **about.cshtml.cs** strony.

1. Zainstaluj następujące pakiety NuGet:
   - [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication)
   - [KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault)

1. Zaimportuj następujący kod do pliku *about.cshtml.cs* :

   ```csharp
    using Microsoft.Azure.KeyVault;
    using Microsoft.Azure.KeyVault.Models;
    using Microsoft.Azure.Services.AppAuthentication;
   ```

   Kod w klasie AboutModel powinien wyglądać następująco:

   ```csharp
    public class AboutModel : PageModel
    {
        public string Message { get; set; }

        public async Task OnGetAsync()
        {
            Message = "Your application description page.";
            int retries = 0;
            bool retry = false;
            try
            {
                /* The next four lines of code show you how to use AppAuthentication library to fetch secrets from your key vault */
                AzureServiceTokenProvider azureServiceTokenProvider = new AzureServiceTokenProvider();
                KeyVaultClient keyVaultClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
                var secret = await keyVaultClient.GetSecretAsync("https://<YourKeyVaultName>.vault.azure.net/secrets/AppSecret")
                        .ConfigureAwait(false);
                Message = secret.Value;
            }
            /* If you have throttling errors see this tutorial https://docs.microsoft.com/azure/key-vault/tutorial-net-create-vault-azure-web-app */
            /// <exception cref="KeyVaultErrorException">
            /// Thrown when the operation returned an invalid status code
            /// </exception>
            catch (KeyVaultErrorException keyVaultException)
            {
                Message = keyVaultException.Message;
            }
        }

        // This method implements exponential backoff if there are 429 errors from Azure Key Vault
        private static long getWaitTime(int retryCount)
        {
            long waitTime = ((long)Math.Pow(2, retryCount) * 100L);
            return waitTime;
        }

        // This method fetches a token from Azure Active Directory, which can then be provided to Azure Key Vault to authenticate
        public async Task<string> GetAccessTokenAsync()
        {
            var azureServiceTokenProvider = new AzureServiceTokenProvider();
            string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
            return accessToken;
        }
    }
    ```

## <a name="run-the-web-app"></a>Uruchamianie aplikacji internetowej

1. W menu głównym programu Visual Studio 2019 wybierz polecenie **Debuguj** > **Start**, z lub bez debugowania. 
1. W przeglądarce przejdź do strony **informacje** .  
    Wyświetlona zostanie wartość wpisu **AppSecret**.

## <a name="enable-a-managed-identity"></a>Włącz zarządzaną tożsamość

Usługa Azure Key Vault oferuje bezpieczny sposób przechowywania poświadczeń oraz innych wpisów tajnych, ale w celu ich pobrania należy uwierzytelnić kod w usłudze Key Vault. [Tożsamości zarządzane dla zasobów platformy Azure](../../active-directory/managed-identities-azure-resources/overview.md) pomagają rozwiązać ten problem, udostępniając usługom platformy Azure automatycznie zarządzaną tożsamość w usłudze Azure AD. Tej tożsamości można użyć do uwierzytelniania w dowolnej usłudze, która obsługuje uwierzytelnianie usługi Azure AD, w tym Key Vault, bez konieczności wyświetlania poświadczeń w kodzie.

W interfejsie wiersza polecenia platformy Azure, aby utworzyć tożsamość dla tej aplikacji, uruchom polecenie Assign-Identity:

```azurecli
az webapp identity assign --name "<YourAppName>" --resource-group "<YourResourceGroupName>"
```

Zastąp \<YourAppName> nazwą opublikowanej aplikacji na platformie Azure.  
    Na przykład jeśli nazwa opublikowanej aplikacji to **MyAwesomeapp.azurewebsites.NET**, Zastąp \<YourAppName> **MyAwesomeapp**.

Zapisz identyfikator `PrincipalId` po opublikowaniu aplikacji na platformie Azure. Dane wyjściowe polecenia w kroku 1 powinny być w następującym formacie:

```json
{
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "SystemAssigned"
}
```

>[!NOTE]
>Polecenie w tej procedurze jest odpowiednikiem przechodzenia do [Azure Portal](https://portal.azure.com) i przełączania ustawienia **tożsamość/system przypisany** na wartość **włączone** we właściwościach aplikacji sieci Web.

## <a name="assign-permissions-to-your-app"></a>Przypisywanie uprawnień do aplikacji

Zastąp \<YourKeyVaultName> nazwą magazynu kluczy i Zastąp \<PrincipalId> wartością **PrincipalId** w następującym poleceniu:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <PrincipalId> --secret-permissions get list
```

To polecenie daje tożsamości (MSI) uprawnienia usługi App Service do wykonywania operacji **pobierania** i **wyświetlania listy** w magazynie kluczy.

## <a name="publish-the-web-app-to-azure"></a>Publikowanie aplikacji internetowej na platformie Azure

Opublikuj ponownie aplikację sieci Web na platformie Azure, aby sprawdzić, czy na działającej aplikacji sieci Web można pobrać wartość klucza tajnego.

1. W programie Visual Studio wybierz projekt **key-vault-dotnet-core-quickstart**.
2. Wybierz pozycję **Publikuj** > **Rozpocznij**.
3. Wybierz przycisk **Utwórz**.

Gdy uruchomisz aplikację, zobaczysz, że może ona pobrać wartość wpisu tajnego.

Teraz aplikacja internetowa została pomyślnie utworzona w programie .NET, która przechowuje i pobiera wpisy tajne z magazynu kluczy.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy nie są już potrzebne, możesz usunąć maszynę wirtualną i Magazyn kluczy.

## <a name="next-steps"></a>Następne kroki

>[!div class="nextstepaction"]
>[Przewodnik dewelopera usługi Azure Key Vault](developers-guide.md)
