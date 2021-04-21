---
title: Szybki start — Azure Key Vault klienta kluczy sieciowych dla programu .NET (wersja 4)
description: Dowiedz się, jak tworzyć, pobierać i usuwać klucze z usługi Azure Key Vault przy użyciu biblioteki klienta platformy .NET (wersja 4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-csharp, devx-track-azurepowershell
ms.openlocfilehash: 5c1e4d64ba3359a07dddbbf89774e31815935230
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818426"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-net-sdk-v4"></a>Szybki start: Azure Key Vault klienta klucza dla platformy .NET (ZESTAW SDK 4)

Wprowadzenie do biblioteki Azure Key Vault klienta dla programu .NET. [Azure Key Vault](../general/overview.md) to usługa w chmurze, która zapewnia bezpieczny magazyn kluczy kryptograficznych. Możesz bezpiecznie przechowywać klucze kryptograficzne, hasła, certyfikaty i inne wpisy tajne. Magazyny kluczy platformy Azure można tworzyć oraz nimi zarządzać za pośrednictwem witryny Azure Portal. Z tego przewodnika Szybki start dowiesz się, jak tworzyć, pobierać i usuwać klucze z usługi Azure Key Vault przy użyciu biblioteki klienta kluczy platformy .NET

Key Vault zasobów biblioteki klienta klucza:

[Dokumentacja referencyjna interfejsu API](/dotnet/api/azure.security.keyvault.keys)  |  [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)  |  [Pakiet (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.keys/)

Aby uzyskać więcej informacji na Key Vault kluczy, zobacz:
- [Key Vault omówienie](../general/overview.md)
- [Klucze — omówienie.](about-keys.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure [— utwórz subskrypcję bezpłatnie](https://azure.microsoft.com/free/dotnet)
* [Zestaw SDK platformy .NET Core 3.1 lub nowszy](https://dotnet.microsoft.com/download/dotnet-core)
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)
* Interfejs Key Vault — można go utworzyć przy użyciu interfejsu [Azure Portal,](../general/quick-create-portal.md) [interfejsu wiersza](../general/quick-create-cli.md)polecenia platformy Azure [lub Azure PowerShell.](../general/quick-create-powershell.md)

## <a name="setup"></a>Konfiguracja

Ten przewodnik Szybki start używa biblioteki tożsamości platformy Azure do uwierzytelniania użytkownika w usługach platformy Azure. Deweloperzy mogą również Visual Studio lub Visual Studio Code do uwierzytelniania swoich wywołań. Aby uzyskać więcej informacji, zobacz Authenticate the client with Azure Identity client library (Uwierzytelnianie klienta za pomocą [biblioteki klienta tożsamości platformy Azure).](/dotnet/api/overview/azure/identity-readme?#authenticate-the-client&preserve-view=true)

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

1. Uruchom polecenie `login`.

    # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
    ```azurecli-interactive
    az login
    ```
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
    ---

    Jeśli interfejs wiersza polecenia Azure PowerShell platformy Azure może otworzyć domyślną przeglądarkę, zrobi to i załaduje stronę logowania platformy Azure.

    W przeciwnym razie otwórz stronę przeglądarki pod [https://aka.ms/devicelogin](https://aka.ms/devicelogin) adresem i wprowadź kod autoryzacji wyświetlany w terminalu.

2. Zaloguj się w przeglądarce przy użyciu poświadczeń swojego konta.

#### <a name="grant-access-to-your-key-vault"></a>Udzielanie dostępu do magazynu kluczy

Tworzenie zasad dostępu dla magazynu kluczy, które przyznaje uprawnienia klucza do konta użytkownika

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
```azurecli-interactive
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --key-permissions delete get list create purge
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <your-key-vault-name> -UserPrincipalName user@domain.com -PermissionsToSecrets delete,get,list,set,purge
```
---

### <a name="create-new-net-console-app"></a>Tworzenie nowej aplikacji konsolowej .NET

1. W powłoki poleceń uruchom następujące polecenie, aby utworzyć projekt o nazwie `key-vault-console-app` :

    ```dotnetcli
    dotnet new console --name key-vault-console-app
    ```

1. Zmień katalog na nowo utworzony *katalog key-vault-console-app* i uruchom następujące polecenie, aby skompilować projekt:

    ```dotnetcli
    dotnet build
    ```

    Dane wyjściowe kompilacji nie powinny zawierać ostrzeżeń ani błędów.
    
    ```console
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ```

### <a name="install-the-packages"></a>Instalowanie pakietów

Z powłoki poleceń zainstaluj bibliotekę klienta klucza Azure Key Vault dla programu .NET:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Keys
```

W tym przewodniku Szybki start musisz również zainstalować bibliotekę klienta zestawu Azure SDK dla tożsamości platformy Azure:

```dotnetcli
dotnet add package Azure.Identity
```

#### <a name="set-environment-variables"></a>Ustawianie zmiennych środowiskowych

Ta aplikacja używa nazwy magazynu kluczy jako zmiennej środowiskowej o nazwie `KEY_VAULT_NAME` .

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```azurepowershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS lub Linux
```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Model obiektów

Biblioteka Azure Key Vault klienta dla programu .NET umożliwia zarządzanie kluczami. Sekcja [Przykłady](#code-examples) kodu pokazuje, jak utworzyć klienta, ustawić klucz, pobrać klucz i usunąć klucz.

## <a name="code-examples"></a>Przykłady kodu

### <a name="add-directives"></a>Dodawanie dyrektyw

Dodaj następujące dyrektywy na początku programu *Program.cs:*

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Keys;
```

### <a name="authenticate-and-create-a-client"></a>Uwierzytelnianie i tworzenie klienta

W tym przewodniku Szybki start zalogowany użytkownik jest używany do uwierzytelniania w magazynie kluczy, co jest preferowaną metodą tworzenia aplikacji lokalnych. W przypadku aplikacji wdrożonych na platformie Azure tożsamość zarządzana powinna być przypisana do usługi App Service lub maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Omówienie tożsamości zarządzanej.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

W poniższym przykładzie nazwa magazynu kluczy jest rozszerzana do wartości URI magazynu kluczy w formacie "https:// \<your-key-vault-name\> .vault.azure.net". W tym przykładzie jest to klasa ["DefaultAzureCredential()"](/dotnet/api/azure.identity.defaultazurecredential) z biblioteki tożsamości platformy [Azure,](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme)która umożliwia używanie tego samego kodu w różnych środowiskach z różnymi opcjami w celu zapewnienia tożsamości. Aby uzyskać więcej informacji na temat uwierzytelniania w magazynie kluczy, zobacz [Developer's Guide (Przewodnik dewelopera).](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code)

```csharp
var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
var kvUri = "https://" + keyVaultName + ".vault.azure.net";

var client = new KeyClient(new Uri(kvUri), new DefaultAzureCredential());
```

### <a name="save-a-key"></a>Zapisywanie klucza

W tym zadaniu użyj [metody CreateKeyAsync.](/dotnet/api/azure.security.keyvault.keys.keyclient.createkeyasync) Parametry metody akceptują nazwę klucza i [typ klucza](https://docs.microsoft.com/dotnet/api/azure.security.keyvault.keys.keytype).

```csharp
var key = await client.CreateKeyAsync("myKey", KeyType.Rsa);
```

> [!NOTE]
> Jeśli nazwa klucza istnieje, powyższy kod utworzy nową wersję tego klucza.

### <a name="retrieve-a-key"></a>Pobieranie klucza

Teraz możesz pobrać wcześniej utworzony klucz za pomocą [metody GetKeyAsync.](/dotnet/api/azure.security.keyvault.keys.keyclient.getkeyasync)

```csharp
var key = await client.GetKeyAsync("myKey");
```

### <a name="delete-a-key"></a>Usuń klucz

Na koniec usuńmy i przeczyścimy klucz z magazynu kluczy za pomocą metod [StartDeleteKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.startdeletekeyasync) i [PurgeDeletedKeyAsync.](/dotnet/api/azure.security.keyvault.keys.keyclient.purgedeletedkeyasync)

```csharp
var operation = await client.StartDeleteKeyAsync("myKey");

// You only need to wait for completion if you want to purge or recover the key.
await operation.WaitForCompletionAsync();

var key = operation.Value;
await client.PurgeDeletedKeyAsync("myKey");
```

## <a name="sample-code"></a>Przykładowy kod

Zmodyfikuj aplikację konsoli .NET Core, aby współdziałała z Key Vault, wykonując następujące kroki:

- Zastąp kod w pliku *Program.cs* następującym kodem:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Identity;
    using Azure.Security.KeyVault.Keys;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string keyName = "myKey";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new KeyClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write($"Creating a key in {keyVaultName} called '{keyName}' ...");
                var createdKey = await client.CreateKeyAsync(keyName, KeyType.Rsa);
                Console.WriteLine("done.");
    
                Console.WriteLine($"Retrieving your key from {keyVaultName}.");
                var key = await client.GetKeyAsync(keyName);
                Console.WriteLine($"Your key version is '{key.Value.Properties.Version}'.");
    
                Console.Write($"Deleting your key from {keyVaultName} ...");
                var deleteOperation = await client.StartDeleteKeyAsync(keyName);
                // You only need to wait for completion if you want to purge or recover the key.
                await deleteOperation.WaitForCompletionAsync();
                Console.WriteLine("done.");

                Console.Write($"Purging your key from {keyVaultName} ...");
                await client.PurgeDeletedKeyAsync(keyName);
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

1. Po wyświetleniu monitu wprowadź wartość wpisów tajnych. Na przykład mySecretPassword.

    Zostanie wyświetlona odmiana następujących danych wyjściowych:

    ```console
    Creating a key in mykeyvault called 'myKey' ... done.
    Retrieving your key from mykeyvault.
    Your key version is '8532359bced24e4bb2525f2d2050738a'.
    Deleting your key from jl-kv ... done
    Purging your key from <your-unique-keyvault-name> ... done.   
    ```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono magazyn kluczy, przechowywano klucz i pobrano ten klucz. 

Aby dowiedzieć się więcej Key Vault o tym, jak zintegrować ją z aplikacjami, zobacz następujące artykuły:

- Przeczytaj omówienie [Azure Key Vault](../general/overview.md)
- Przeczytaj omówienie [kluczy](about-keys.md)
- Zobacz [samouczek dotyczący dostępu Key Vault z App Service aplikacji](../general/tutorial-net-create-vault-azure-web-app.md)
- Zobacz [samouczek dotyczący Key Vault dostępu z maszyny wirtualnej](../general/tutorial-net-virtual-machine.md)
- Zobacz Azure Key Vault [dewelopera](../general/developers-guide.md)
- Przejrzyj omówienie [Key Vault zabezpieczeń](../general/security-features.md)
