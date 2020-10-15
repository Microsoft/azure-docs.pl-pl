---
title: Szybki Start — Azure Key Vaulta Biblioteka kliencka dla platformy .NET (SDK v3)
description: Dowiedz się, jak tworzyć, pobierać i usuwać wpisy tajne z magazynu kluczy platformy Azure przy użyciu biblioteki klienckiej platformy .NET (wersja 3)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 11/05/2019
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.openlocfilehash: 816cf8d385c12046a5363cf94ab5fa60e5d77e48
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078871"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v3"></a>Szybki Start: Azure Key Vaulta Biblioteka kliencka dla platformy .NET (SDK v3)

Wprowadzenie do biblioteki klienta Azure Key Vault dla platformy .NET. Wykonaj poniższe kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

> [!NOTE]
> Ten przewodnik Szybki Start używa wersji 3.0.4 biblioteki klienta Microsoft. Azure. Aby użyć najnowszej wersji biblioteki klienta Key Vault, zobacz [Azure Key Vault biblioteki klienckiej dla platformy .NET (SDK v4)](quick-create-net.md). 

[Dokumentacja](https://docs.microsoft.com/dotnet/api/overview/azure/keyvault/v3)  |  interfejsu API [Kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault/Microsoft.Azure.KeyVault)  |  źródłowy biblioteki [Pakiet (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)

> [!NOTE]
> Każdy Magazyn kluczy musi mieć unikatową nazwę. Zastąp <unikatowym identyfikatorem magazynu kluczy> nazwą magazynu klucza w poniższych przykładach.


## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Zestaw .NET Core 3,1 SDK lub nowszy](https://dotnet.microsoft.com/download/dotnet-core/3.1).
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)

Ten przewodnik Szybki Start korzysta z narzędzia `dotnet` i interfejsu wiersza polecenia platformy Azure

## <a name="setting-up"></a>Konfigurowanie

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

1. Uruchom polecenie `login`.

    ```azurecli-interactive
    az login
    ```

    Jeśli interfejs wiersza polecenia może otworzyć domyślną przeglądarkę, spowoduje to załadowanie strony logowania platformy Azure.

    W przeciwnym razie Otwórz stronę przeglądarki pod adresem [https://aka.ms/devicelogin](https://aka.ms/devicelogin) i wprowadź kod autoryzacji wyświetlany w terminalu.

2. Zaloguj się w przeglądarce przy użyciu poświadczeń swojego konta.

### <a name="create-new-net-console-app"></a>Utwórz nową aplikację konsolową platformy .NET

W oknie konsoli Użyj `dotnet new` polecenia, aby utworzyć nową aplikację konsolową .NET o nazwie `akv-dotnet` .


```console
dotnet new console -n akvdotnet
```

Zmień katalog na nowo utworzony folder aplikacji. Aplikację można skompilować przy użyciu:

```console
dotnet build
```

Dane wyjściowe kompilacji nie powinny zawierać ostrzeżeń ani błędów.

```console
Build succeeded.
 0 Warning(s)
 0 Error(s)
```

### <a name="install-the-package"></a>Zainstaluj pakiet

W oknie konsoli programu Zainstaluj Azure Key Vaultą bibliotekę kliencką dla programu .NET i biblioteki [AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) :

```console
dotnet add package Microsoft.Azure.KeyVault
dotnet add package Microsoft.Azure.Services.AppAuthentication
```

W tym przewodniku szybki start konieczne będzie zainstalowanie następujących pakietów:

```console
dotnet add package System.Threading.Tasks
dotnet add package Microsoft.IdentityModel.Clients.ActiveDirectory
dotnet add package Microsoft.Azure.Management.ResourceManager.Fluent
```

### <a name="create-a-resource-group-and-key-vault"></a>Tworzenie grupy zasobów i magazynu kluczy

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

#### <a name="grant-access-to-your-key-vault"></a>Udzielanie dostępu do magazynu kluczy

Tworzenie zasad dostępu dla magazynu kluczy, który przyznaje poufne uprawnienia do konta użytkownika

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set
```

## <a name="object-model"></a>Model obiektów

Biblioteka klienta Azure Key Vault dla platformy .NET umożliwia zarządzanie kluczami i powiązanymi zasobami, takimi jak certyfikaty i wpisy tajne. Poniższe przykłady kodu pokazują, jak ustawić wpis tajny i pobrać wpis tajny.

## <a name="code-examples"></a>Przykłady kodu

### <a name="add-directives"></a>Dodaj dyrektywy

Dodaj następujące dyrektywy na początku kodu:

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Azure.KeyVault;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Azure.Management.ResourceManager.Fluent;
using Microsoft.Azure.Management.ResourceManager.Fluent.Authentication;
using Microsoft.Azure.Services.AppAuthentication;
```

### <a name="authenticate-to-your-key-vault"></a>Uwierzytelnianie w magazynie kluczy

Skonfiguruj uwierzytelnianie do aplikacji w [klasie KeyVaultClient](/dotnet/api/microsoft.azure.keyvault.keyvaultclient). W naszym przykładzie zostanie użyty token dostarczony przez `AzureServiceTokenProvider` aktualnie zalogowanego użytkownika:

```csharp
var azureServiceTokenProvider1 = new AzureServiceTokenProvider();
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider1.KeyVaultTokenCallback));
```

Aby uzyskać więcej informacji na temat `AzureServiceTokenProvider` , zobacz [uwierzytelnianie w usługach platformy Azure](https://docs.microsoft.com/azure/key-vault/general/service-to-service-authentication#authenticating-to-azure-services) .

### <a name="save-a-secret"></a>Zapisz klucz tajny

Teraz, gdy aplikacja jest uwierzytelniana, możesz umieścić klucz tajny w magazynie kluczy przy użyciu [metody SetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) , która wymaga adresu URL Twojego magazynu, który znajduje się w formularzu `https://<your-unique-keyvault-name>.vault.azure.net/secrets/` . Wymaga również nazwy wpisu tajnego — używamy hasła ". 

[!code-csharp[Set secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=setsecret)]

Można sprawdzić, czy wpis tajny został ustawiony za pomocą polecenia [AZ The Secret show klucza tajnego](/cli/azure/keyvault/secret?#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

### <a name="retrieve-a-secret"></a>Pobierz klucz tajny

Teraz można pobrać wcześniej ustawioną wartość za pomocą [metody GetSecretAsync](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync)

[!code-csharp[Get secret](~/samples-key-vault-dotnet-quickstart/akvdotnet/Program.cs?name=getsecret)]

Wpis tajny jest teraz zapisywany jako `keyvaultSecret.Value;` .

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie jest już potrzebne, możesz użyć interfejsu wiersza polecenia platformy Azure lub Azure PowerShell, aby usunąć magazyn kluczy i odpowiednią grupę zasobów.

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="sample-code"></a>Przykładowy kod

Zmodyfikuj aplikację konsolową .NET Core, aby móc korzystać z Key Vault, wykonując następujące czynności:

1. Zastąp kod w pliku *Program.cs* następującym kodem:
```csharp
using System;
using System.Threading.Tasks;
using Microsoft.Azure.KeyVault;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.Management.ResourceManager.Fluent;
using Microsoft.Azure.Management.ResourceManager.Fluent.Authentication;
// </directives>

namespace akvdotnet
{
    class Program
    {
        static async Task Main(string[] args)
        {
            Program P = new Program();
            string secretName = "mySecret";

            // kvURL must be updated to the URL of your key vault
            string kvURL = "https://myKV.vault.azure.net";

            // <authentication>

            var azureServiceTokenProvider1 = new AzureServiceTokenProvider();
            var kvClient = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider1.KeyVaultTokenCallback));        
            // </authentication>


            Console.Write("Input the value of your secret > ");
            string secretValue = Console.ReadLine();

            Console.WriteLine("Your secret is '" + secretValue + "'.");

            Console.Write("Saving the value of your secret to your key vault ...");

            //set secret
            await kvClient.SetSecretAsync($"{kvURL}", secretName, secretValue);

            Console.WriteLine("done.");

            Console.WriteLine("Forgetting your secret.");
            secretValue = "";
            Console.WriteLine("Your secret is '" + secretValue + "'.");
            Console.WriteLine("Retrieving your secret from key vault.");

            //retrieve secret
            var keyvaultSecret = await kvClient.GetSecretAsync($"{kvURL}", secretName).ConfigureAwait(false);

            secretValue = keyvaultSecret.Result;
            Console.WriteLine("Your secret is " + secretValue);
        }
    }
}
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Key Vault i sposobu integrowania go z aplikacjami, przejdź do artykułu poniżej.

- Implementowanie uwierzytelniania między usługami [w celu Azure Key Vault przy użyciu platformy .NET](../general/service-to-service-authentication.md)
- Zapoznaj się [z omówieniem Azure Key Vault](../general/overview.md)
- Zobacz [przewodnik dewelopera Azure Key Vault](../general/developers-guide.md)
- Przegląd [Azure Key Vault najlepszych](../general/best-practices.md) rozwiązań
