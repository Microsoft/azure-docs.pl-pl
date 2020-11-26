---
title: Szybki Start — Biblioteka klienta Azure Key Vault Certificates dla platformy .NET (wersja 4)
description: Dowiedz się, jak tworzyć, pobierać i usuwać certyfikaty z magazynu kluczy platformy Azure przy użyciu biblioteki klienckiej platformy .NET (wersja 4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: bbc90d0ba0bea0cd634602da2ebd4e07d6eadf0c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185168"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-net-sdk-v4"></a>Szybki Start: Azure Key Vault Biblioteka kliencka certyfikatów dla platformy .NET (SDK v4)

Rozpocznij pracę z biblioteką klienta Azure Key Vault Certificate dla platformy .NET. [Azure Key Vault](../general/overview.md) to usługa w chmurze, która zapewnia bezpieczny magazyn dla certyfikatów. Możesz bezpiecznie przechowywać klucze, hasła, certyfikaty i inne certyfikaty. Magazyny kluczy platformy Azure można tworzyć oraz nimi zarządzać za pośrednictwem witryny Azure Portal. W tym przewodniku szybki start dowiesz się, jak tworzyć, pobierać i usuwać certyfikaty z magazynu kluczy platformy Azure przy użyciu biblioteki klienckiej platformy .NET.

Key Vault zasoby biblioteki klienta:

[Dokumentacja](/dotnet/api/azure.security.keyvault.certificates)  |  interfejsu API [Kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)  |  źródłowy biblioteki [Pakiet (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)

Aby uzyskać więcej informacji o Key Vault i certyfikatach, zobacz:
- [Przegląd Key Vault](../general/overview.md)
- [Omówienie certyfikatów](about-certificates.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/dotnet)
* [.NET Core 3,1 SDK lub nowszy](https://dotnet.microsoft.com/download/dotnet-core)
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)
* Key Vault — można go utworzyć przy użyciu [Azure Portal](../general/quick-create-portal.md), [interfejsu wiersza polecenia platformy Azure](../general/quick-create-cli.md)lub [Azure PowerShell](../general/quick-create-powershell.md).

Ten przewodnik Szybki Start korzysta z narzędzia `dotnet` i interfejsu wiersza polecenia platformy Azure

## <a name="setup"></a>Konfigurowanie

Ten przewodnik Szybki Start korzysta z biblioteki Azure Identity Library z interfejsem wiersza polecenia platformy Azure w celu uwierzytelniania użytkowników w usługach platformy Azure. Deweloperzy mogą również używać programu Visual Studio lub Visual Studio Code do uwierzytelniania wywołań, aby uzyskać więcej informacji, zobacz [uwierzytelnianie klienta przy użyciu biblioteki klienta tożsamości platformy Azure](/dotnet/api/overview/azure/identity-readme?#authenticate-the-client&preserve-view=true).

### <a name="sign-in-to-azure"></a>Logowanie się do platformy Azure

1. Uruchom polecenie `login`.

    ```azurecli-interactive
    az login
    ```

    Jeśli interfejs wiersza polecenia może otworzyć domyślną przeglądarkę, spowoduje to załadowanie strony logowania platformy Azure.

    W przeciwnym razie Otwórz stronę przeglądarki pod adresem [https://aka.ms/devicelogin](https://aka.ms/devicelogin) i wprowadź kod autoryzacji wyświetlany w terminalu.

2. Zaloguj się w przeglądarce przy użyciu poświadczeń swojego konta.


### <a name="create-new-net-console-app"></a>Utwórz nową aplikację konsolową platformy .NET

1. W powłoce poleceń Uruchom następujące polecenie, aby utworzyć projekt o nazwie `key-vault-console-app` :

    ```dotnetcli
    dotnet new console --name key-vault-console-app
    ```

1. Przejdź do nowo utworzonego katalogu *Key-magazyn-Console-App* i uruchom następujące polecenie, aby skompilować projekt:

    ```dotnetcli
    dotnet build
    ```

    Dane wyjściowe kompilacji nie powinny zawierać ostrzeżeń ani błędów.
    
    ```console
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ```

### <a name="install-the-packages"></a>Zainstaluj pakiety

W powłoce poleceń Zainstaluj Azure Key Vault bibliotekę kliencką certyfikatów dla platformy .NET:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Certificates
```

W tym przewodniku Szybki Start należy również zainstalować bibliotekę klienta zestawu Azure SDK dla tożsamości platformy Azure:

```dotnetcli
dotnet add package Azure.Identity
```

#### <a name="grant-access-to-your-key-vault"></a>Udzielanie dostępu do magazynu kluczy

Tworzenie zasad dostępu dla magazynu kluczy, który przyznaje uprawnienia certyfikatu do konta użytkownika

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --certificate-permissions delete get list create purge
```

#### <a name="set-environment-variables"></a>Ustawianie zmiennych środowiskowych

Ta aplikacja używa nazwy magazynu kluczy jako zmiennej środowiskowej o nazwie `KEY_VAULT_NAME` .

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME=<your-key-vault-name>
```

macOS lub Linux
```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Model obiektów

Biblioteka klienta certyfikatu Azure Key Vault dla platformy .NET umożliwia zarządzanie certyfikatami. W sekcji [przykłady kodu](#code-examples) przedstawiono sposób tworzenia klienta, ustawiania certyfikatu, pobierania certyfikatu i usuwania certyfikatu.

## <a name="code-examples"></a>Przykłady kodu

### <a name="add-directives"></a>Dodaj dyrektywy

Dodaj następujące dyrektywy na początku *program.cs*:

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Certificates;
```

### <a name="authenticate-and-create-a-client"></a>Uwierzytelnianie i tworzenie klienta

W tym przewodniku szybki start zalogowany użytkownik jest używany do uwierzytelniania w magazynie kluczy, który jest preferowaną metodą tworzenia lokalnego. W przypadku aplikacji wdrożonych na platformie Azure tożsamość zarządzana powinna być przypisana do App Service lub maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Omówienie tożsamości zarządzanej](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

W poniższym przykładzie nazwa magazynu kluczy jest rozwinięta do identyfikatora URI magazynu kluczy w formacie "https:// \<your-key-vault-name\> . Vault.Azure.NET". W tym przykładzie użyto klasy  ["DefaultAzureCredential ()"](/dotnet/api/azure.identity.defaultazurecredential) , która umożliwia użycie tego samego kodu w różnych środowiskach z różnymi opcjami w celu zapewnienia tożsamości. Aby uzyskać więcej informacji o uwierzytelnianiu do magazynu kluczy, zobacz [przewodnik dewelopera](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

```csharp
string keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
var kvUri = "https://" + keyVaultName + ".vault.azure.net";

var client = new CertificateClient(new Uri(kvUri), new DefaultAzureCredential());
```

### <a name="save-a-certificate"></a>Zapisz certyfikat

W tym przykładzie dla uproszczenia można użyć certyfikatu z podpisem własnym z domyślnymi zasadami wystawiania. Dla tego zadania użyj metody [StartCreateCertificateAsync](/dotnet/api/azure.security.keyvault.certificates.certificateclient.startcreatecertificateasync) . Pierwszy parametr metody przyjmuje nazwę certyfikatu i[zasady certyfikatu](https://docs.microsoft.com/dotnet/api/azure.security.keyvault.certificates.certificatepolicy)zasad certyfikatów.

```csharp
CertificateOperation operation = await client.StartCreateCertificateAsync("MyCertificate", CertificatePolicy.Default);
var certificate = await operation.WaitForCompletionAsync();
```

> [!NOTE]
> Jeśli nazwa certyfikatu istnieje, powyżej kodu zostanie utworzona nowa wersja tego certyfikatu.

### <a name="retrieve-a-certificate"></a>Pobierz certyfikat

Teraz można pobrać wcześniej utworzony certyfikat przy użyciu metody [GetCertificateAsync](/dotnet/api/azure.security.keyvault.certificates.certificateclient.getcertificateasync) .

```csharp
var certificate = await client.GetCertificateAsync("myCertificate");
```

### <a name="delete-a-certificate"></a>Usuwanie certyfikatu

Na koniec Usuń i Przeczyść certyfikat z magazynu kluczy przy użyciu metod [StartDeleteCertificateAsync](/dotnet/api/azure.security.keyvault.certificates.certificateclient.startdeletecertificateasync) i [PurgeDeletedCertificateAsync](/dotnet/api/azure.security.keyvault.certificates.certificateclient.purgedeletedcertificateasync)  .

```csharp
var operation = await client.StartDeleteCertificateAsync("MyCertificate");

// You only need to wait for completion if you want to purge or recover the certificate.
await operation.WaitForCompletionAsync();

var certificate = operation.Value;
await client.PurgeDeletedCertificateAsync(certificate.Name);
```

## <a name="sample-code"></a>Przykładowy kod

Zmodyfikuj aplikację konsolową .NET Core, aby móc korzystać z Key Vault, wykonując następujące czynności:

1. Zastąp kod w pliku *Program.cs* następującym kodem:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Identity;
    using Azure.Security.KeyVault.Certificates;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string certificateName = "myCertificate";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new CertificateClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write($"Creating a certificate in {keyVaultName} called '{certificateName}' ...");
                CertificateOperation operation = await client.StartCreateCertificateAsync(certificateName, CertificatePolicy.Default);
                await operation.WaitForCompletionAsync();
                Console.WriteLine(" done.");
    
                Console.WriteLine($"Retrieving your certificate from {keyVaultName}.");
                var certificate = await client.GetCertificateAsync(certificateName);
                Console.WriteLine($"Your certificate value is '{certificate.Value.Properties.Version}'.");
    
                Console.Write($"Deleting your certificate from {keyVaultName} ...");
                DeleteCertificateOperation deleteOperation = await client.StartDeleteCertificateAsync(certificateName);
                // You only need to wait for completion if you want to purge or recover the certificate.
                await deleteOperation.WaitForCompletionAsync();
                Console.WriteLine(" done.");

                Console.Write($"Purging your certificate from {keyVaultName} ...");
                await client.PurgeDeletedCertificateAsync(certificateName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```
### <a name="test-and-verify"></a>Testowanie i weryfikowanie

1.  Wykonaj następujące polecenie, aby skompilować projekt

    ```dotnetcli
    dotnet build
    ```

1. Wykonaj następujące polecenie, aby uruchomić aplikację.

    ```dotnetcli
    dotnet run
    ```

1. Po wyświetleniu monitu wprowadź wartość klucza tajnego. Na przykład mySecretPassword.

    Zostanie wyświetlona odmiana następujących danych wyjściowych:

    ```console
    Creating a certificate in mykeyvault called 'myCertificate' ... done.
    Retrieving your certificate from mykeyvault.
    Your certificate version is '8532359bced24e4bb2525f2d2050738a'.
    Deleting your certificate from jl-kv ... done
    ```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, możesz użyć interfejsu wiersza polecenia platformy Azure lub Azure PowerShell, aby usunąć magazyn kluczy i odpowiednią grupę zasobów.

### <a name="delete-a-key-vault"></a>Usuwanie Key Vault

```azurecli
az keyvault delete --name <your-unique-keyvault-name>
```

```azurepowershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name>
```

### <a name="purge-a-key-vault"></a>Przeczyszczanie Key Vault

```azurecli
az keyvault purge --location eastus --name <your-unique-keyvault-name>
```

```azurepowershell
Remove-AzKeyVault -VaultName <your-unique-keyvault-name> -InRemovedState -Location eastus
```

### <a name="delete-a-resource-group"></a>Usuwanie grupy zasobów

```azurecli
az group delete -g "myResourceGroup"
```

```azurepowershell
Remove-AzResourceGroup -Name "myResourceGroup"
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono Magazyn kluczy, Zapisano certyfikat i pobrano ten certyfikat. 

Aby dowiedzieć się więcej na temat Key Vault i sposobu integrowania go z aplikacjami, zobacz następujące artykuły:

- Zapoznaj się [z omówieniem Azure Key Vault](../general/overview.md)
- Zapoznaj się z [omówieniem certyfikatów](about-certificates.md)
- Wyświetlanie [Key Vault dostępu z poziomu samouczka aplikacji App Service](../general/tutorial-net-create-vault-azure-web-app.md)
- Zobacz [Key Vault dostępu z poziomu samouczka maszyny wirtualnej](../general/tutorial-net-virtual-machine.md)
- Zobacz [przewodnik dewelopera Azure Key Vault](../general/developers-guide.md)
- Przegląd [Azure Key Vault najlepszych](../general/best-practices.md) rozwiązań