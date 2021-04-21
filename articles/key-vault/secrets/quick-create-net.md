---
title: Szybki start — Azure Key Vault klienta wpisów tajnych dla programu .NET (wersja 4)
description: Dowiedz się, jak tworzyć, pobierać i usuwać wpisy tajne z usługi Azure Key Vault przy użyciu biblioteki klienta platformy .NET (wersja 4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 6338b76e2f14e9f842b3395113badadcd37c8d32
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814176"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-net-sdk-v4"></a>Szybki start: Azure Key Vault tajnej biblioteki klienta dla platformy .NET (ZESTAW SDK 4)

Wprowadzenie do biblioteki klienta Azure Key Vault tajnego dla programu .NET. [Azure Key Vault](../general/overview.md) to usługa w chmurze, która zapewnia bezpieczny magazyn wpisów tajnych. Możesz bezpiecznie przechowywać klucze, hasła, certyfikaty oraz inne wpisy tajne. Magazyny kluczy platformy Azure można tworzyć oraz nimi zarządzać za pośrednictwem witryny Azure Portal. Z tego przewodnika Szybki start dowiesz się, jak tworzyć, pobierać i usuwać wpisy tajne z usługi Azure Key Vault przy użyciu biblioteki klienta platformy .NET

Key Vault zasobów biblioteki klienta:

[Dokumentacja referencyjna interfejsu API](/dotnet/api/azure.security.keyvault.secrets)  |  [Kod źródłowy biblioteki](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)  |  [Pakiet (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

Aby uzyskać więcej informacji na Key Vault i wpisów tajnych, zobacz:
- [Key Vault omówienie](../general/overview.md)
- [Omówienie wpisów tajnych.](about-secrets.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure [— utwórz subskrypcję bezpłatnie](https://azure.microsoft.com/free/dotnet)
* [Zestaw .NET Core 3.1 SDK lub nowszy](https://dotnet.microsoft.com/download/dotnet-core)
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)
* Interfejs Key Vault — można go utworzyć przy użyciu interfejsu [wiersza Azure Portal](../general/quick-create-portal.md) [platformy Azure](../general/quick-create-cli.md)lub [Azure PowerShell](../general/quick-create-powershell.md)

Ten przewodnik Szybki start korzysta z interfejsu `dotnet` wiersza polecenia platformy Azure

## <a name="setup"></a>Konfiguracja

Ten przewodnik Szybki start używa biblioteki tożsamości platformy Azure z interfejsem wiersza polecenia platformy Azure do uwierzytelniania użytkownika w usługach platformy Azure. Deweloperzy mogą również używać Visual Studio lub Visual Studio Code do uwierzytelniania swoich wywołań. Aby uzyskać więcej informacji, zobacz Authenticate the client with Azure Identity client library (Uwierzytelnianie klienta za pomocą biblioteki klienta tożsamości [platformy Azure).](/dotnet/api/overview/azure/identity-readme?#authenticate-the-client&preserve-view=true)

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

1. Uruchom polecenie `login`.

    ```azurecli-interactive
    az login
    ```

    Jeśli interfejs wiersza polecenia może otworzyć domyślną przeglądarkę, zrobi to i załaduje stronę logowania platformy Azure.

    W przeciwnym razie otwórz stronę przeglądarki pod [https://aka.ms/devicelogin](https://aka.ms/devicelogin) adresem i wprowadź kod autoryzacji wyświetlany w terminalu.

2. Zaloguj się w przeglądarce przy użyciu poświadczeń swojego konta.

### <a name="grant-access-to-your-key-vault"></a>Udzielanie dostępu do magazynu kluczy

Tworzenie zasad dostępu dla magazynu kluczy, które przyznaje uprawnienia do kluczy tajnych do konta użytkownika

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set purge
```

### <a name="create-new-net-console-app"></a>Tworzenie nowej aplikacji konsolowej .NET

1. W powłoki poleceń uruchom następujące polecenie, aby utworzyć projekt o nazwie `key-vault-console-app` :

    ```dotnetcli
    dotnet new console --name key-vault-console-app
    ```

1. Zmień katalog na nowo utworzony katalog *key-vault-console-app* i uruchom następujące polecenie, aby skompilować projekt:

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

Z powłoki poleceń zainstaluj Azure Key Vault klienta tajnego dla programu .NET:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Secrets
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
```powershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS lub Linux
```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Model obiektu

Biblioteka Azure Key Vault klienta wpisów tajnych dla programu .NET umożliwia zarządzanie wpisami tajnymi. W [sekcji Przykłady](#code-examples) kodu pokazano, jak utworzyć klienta, ustawić klucz tajny, pobrać klucz tajny i usunąć klucz tajny.

## <a name="code-examples"></a>Przykłady kodu

### <a name="add-directives"></a>Dodawanie dyrektyw

Dodaj następujące dyrektywy na początku programu *Program.cs:*

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>Uwierzytelnianie i tworzenie klienta

W tym przewodniku Szybki start zalogowany użytkownik jest używany do uwierzytelniania w magazynie kluczy, co jest preferowaną metodą tworzenia aplikacji lokalnych. W przypadku aplikacji wdrożonych na platformie Azure tożsamość zarządzana powinna być przypisana do usługi App Service lub maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz Omówienie [tożsamości zarządzanej.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

W poniższym przykładzie nazwa magazynu kluczy jest rozszerzana do wartości URI magazynu kluczy w formacie "https:// \<your-key-vault-name\> .vault.azure.net". W tym przykładzie używa się klasy ["DefaultAzureCredential()"](/dotnet/api/azure.identity.defaultazurecredential) z biblioteki tożsamości platformy [Azure,](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme)która umożliwia używanie tego samego kodu w różnych środowiskach z różnymi opcjami w celu zapewnienia tożsamości. Aby uzyskać więcej informacji na temat uwierzytelniania w magazynie kluczy, zobacz [Developer's Guide (Przewodnik dewelopera).](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code)

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>Zapisywanie tajnego

Teraz, po uwierzytelnieniu aplikacji konsolowej, dodaj klucz tajny do magazynu kluczy. W tym zadaniu użyj [metody SetSecretAsync.](/dotnet/api/azure.security.keyvault.secrets.secretclient.setsecretasync) Pierwszy parametr metody akceptuje nazwę dla tajnego &mdash; "mySecret" w tym przykładzie.

```csharp
await client.SetSecretAsync(secretName, secretValue);
```

> [!NOTE]
> Jeśli istnieje nazwa tajnego, powyższy kod utworzy nową wersję tego tajnego.


### <a name="retrieve-a-secret"></a>Pobieranie tajnego

Możesz teraz pobrać wcześniej ustawioną wartość za pomocą [metody GetSecretAsync.](/dotnet/api/azure.security.keyvault.secrets.secretclient.getsecretasync)

```csharp
var secret = await client.GetSecretAsync(secretName);
```

Twój klucz tajny jest teraz zapisany jako `secret.Value` .

### <a name="delete-a-secret"></a>Usuń klucz tajny

Na koniec usuńmy klucz tajny z magazynu kluczy przy użyciu metod [StartDeleteSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.startdeletesecretasync) i [PurgeDeletedSecretAsync.](/dotnet/api/azure.security.keyvault.keys.keyclient)

```csharp
var operation = await client.StartDeleteSecretAsync("mySecret");
// You only need to wait for completion if you want to purge or recover the key.
await operation.WaitForCompletionAsync();

await client.PurgeDeletedKeyAsync("mySecret");
```

## <a name="sample-code"></a>Przykładowy kod

Zmodyfikuj aplikację konsolą .NET Core, aby wchodzić w Key Vault, wykonując następujące czynności:

1. Zastąp kod w pliku *Program.cs* następującym kodem:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Identity;
    using Azure.Security.KeyVault.Secrets;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string secretName = "mySecret";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write("Input the value of your secret > ");
                var secretValue = Console.ReadLine();
    
                Console.Write($"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...");
                await client.SetSecretAsync(secretName, secretValue);
                Console.WriteLine(" done.");
    
                Console.WriteLine("Forgetting your secret.");
                secretValue = string.Empty;
                Console.WriteLine($"Your secret is '{secretValue}'.");
    
                Console.WriteLine($"Retrieving your secret from {keyVaultName}.");
                var secret = await client.GetSecretAsync(secretName);
                Console.WriteLine($"Your secret is '{secret.Value.Value}'.");
    
                Console.Write($"Deleting your secret from {keyVaultName} ...");
                DeleteSecretOperation operation = await client.StartDeleteSecretAsync(secretName);
                // You only need to wait for completion if you want to purge or recover the secret.
                await operation.WaitForCompletionAsync();
                Console.WriteLine(" done.");
                
                Console.Write($"Purging your secret from {keyVaultName} ...");
                await client.PurgeDeletedSecretAsync(secretName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```
### <a name="test-and-verify"></a>Testowanie i weryfikowanie

1. Wykonaj następujące polecenie, aby uruchomić aplikację.

    ```dotnetcli
    dotnet run
    ```

1. Po wyświetleniu monitu wprowadź wartość wpisów tajnych. Na przykład mySecretPassword.

Zostanie wyświetlona odmiana następujących danych wyjściowych:

```console
Input the value of your secret > mySecretPassword
Creating a secret in <your-unique-keyvault-name> called 'mySecret' with the value 'mySecretPassword' ... done.
Forgetting your secret.
Your secret is ''.
Retrieving your secret from <your-unique-keyvault-name>.
Your secret is 'mySecretPassword'.
Deleting your secret from <your-unique-keyvault-name> ... done.    
Purging your secret from <your-unique-keyvault-name> ... done.
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej Key Vault o tym, jak zintegrować ją z aplikacjami, zobacz następujące artykuły:

- Przeczytaj omówienie [Azure Key Vault](../general/overview.md)
- Zobacz [samouczek dotyczący Key Vault dostępu z App Service aplikacji](../general/tutorial-net-create-vault-azure-web-app.md)
- Zobacz [samouczek dotyczący Key Vault dostępu z maszyny wirtualnej](../general/tutorial-net-virtual-machine.md)
- Zobacz Azure Key Vault [dewelopera](../general/developers-guide.md)
- Przejrzyj omówienie [Key Vault zabezpieczeń](../general/security-features.md)
