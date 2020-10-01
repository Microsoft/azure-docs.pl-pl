---
title: Szybki Start — Azure Key Vaulta Biblioteka kliencka dla platformy .NET (v4)
description: Dowiedz się, jak tworzyć, pobierać i usuwać wpisy tajne z magazynu kluczy platformy Azure przy użyciu biblioteki klienckiej platformy .NET (v4)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/30/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: cc5aa7f10d83edc757e99820b9591a953df72062
ms.sourcegitcommit: 06ba80dae4f4be9fdf86eb02b7bc71927d5671d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91612255"
---
# <a name="quickstart-azure-key-vault-client-library-for-net-sdk-v4"></a>Szybki Start: Azure Key Vaulta Biblioteka kliencka dla platformy .NET (SDK v4)

Wprowadzenie do biblioteki klienta Azure Key Vault dla platformy .NET. Wykonaj poniższe kroki, aby zainstalować pakiet i wypróbować przykładowy kod dla podstawowych zadań.

Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Użyj biblioteki klienta Key Vault dla platformy .NET, aby:

- Zwiększ bezpieczeństwo i kontrolę nad kluczami i hasłami.
- Utwórz i zaimportuj klucze szyfrowania w kilka minut.
- Ogranicz opóźnienia dzięki skali chmury i globalnej nadmiarowości.
- Uprość i automatyzuj zadania dla certyfikatów TLS/SSL.
- Użyj zweryfikowanej sprzętowych modułów zabezpieczeń poziomu 2 trybu FIPS 140-2.

[Dokumentacja](/dotnet/api/azure.security.keyvault.secrets?view=azure-dotnet)  |  interfejsu API [Kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)  |  źródłowy biblioteki [Pakiet (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure — [Utwórz ją bezpłatnie](https://azure.microsoft.com/free/dotnet)
* [.NET Core 3,1 SDK lub nowszy](https://dotnet.microsoft.com/download/dotnet-core)
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) lub [Azure PowerShell](/powershell/azure/)

## <a name="setup"></a>Konfigurowanie

### <a name="create-a-new-console-app"></a>Tworzenie nowej aplikacji konsolowej

Wykonaj następujące kroki, aby utworzyć i skompilować aplikację konsolową .NET Core.

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

W powłoce poleceń Zainstaluj Azure Key Vaultą bibliotekę kliencką dla platformy .NET:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Secrets
```

W tym przewodniku Szybki Start należy również zainstalować bibliotekę klienta zestawu Azure SDK dla tożsamości platformy Azure:

```dotnetcli
dotnet add package Azure.Identity
```

### <a name="create-a-resource-group-and-key-vault"></a>Tworzenie grupy zasobów i magazynu kluczy

[!INCLUDE[Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

### <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

[!INCLUDE[Create a service principal](../../../includes/key-vault-sp-creation.md)]

#### <a name="give-the-service-principal-access-to-your-key-vault"></a>Przyznaj jednostce usługi dostęp do magazynu kluczy

<!-- TODO: need to pass -g myResourceGroup to this command too -->
[!INCLUDE[Give the service principal access to your key vault](../../../includes/key-vault-sp-kv-access.md)]

#### <a name="set-environment-variables"></a>Ustawianie zmiennych środowiskowych

[!INCLUDE[Set environment variables](../../../includes/key-vault-set-environmental-variables.md)]

## <a name="object-model"></a>Model obiektów

Biblioteka klienta Azure Key Vault dla platformy .NET umożliwia zarządzanie kluczami i powiązanymi zasobami. Przykłady powiązanych zasobów obejmują certyfikaty i wpisy tajne. Sekcja [przykłady kodu](#code-examples) przedstawia sposób tworzenia klienta, ustawiania wpisu tajnego, pobierania klucza tajnego i usuwania klucza tajnego.

Cała Aplikacja konsolowa jest dostępna pod adresem https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app .

## <a name="code-examples"></a>Przykłady kodu

### <a name="add-directives"></a>Dodaj dyrektywy

Dodaj następujące dyrektywy na początku *program.cs*:

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>Uwierzytelnianie i tworzenie klienta

Uwierzytelnianie magazynu kluczy i tworzenie klienta zależą od zmiennych środowiskowych w kroku [Ustawianie zmiennych środowiskowych](#set-environment-variables) . Nazwa magazynu kluczy jest rozszerzana na identyfikator URI magazynu kluczy w formacie `https://<your-key-vault-name>.vault.azure.net` . Poniższy kod używa [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet) do uwierzytelniania w magazynie kluczy, który odczytuje zmienne środowiskowe w celu pobrania tokenu dostępu.

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>Zapisz klucz tajny

Teraz, gdy aplikacja konsoli jest uwierzytelniana, należy dodać wpis tajny do magazynu kluczy. W przypadku tego zadania Użyj [klienta programu. Metoda setsecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.setsecretasync) . Pierwszy parametr metody akceptuje nazwę wpisu tajnego "tajemnicy" &mdash; w tym przykładzie.

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=setsecret)]

Można sprawdzić, czy wpis tajny został ustawiony za pomocą polecenia [AZ The Secret show klucza tajnego](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

### <a name="retrieve-a-secret"></a>Pobierz klucz tajny

Teraz można pobrać wcześniej ustawioną wartość za pomocą [klienta. Metoda getsecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync) .

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=getsecret)]

Wpis tajny jest teraz zapisywany jako `secret.Value` .

### <a name="delete-a-secret"></a>Usuń klucz tajny

Na koniec Usuń wpis tajny z magazynu kluczy z [klientem programu. DeleteSecret](/dotnet/api/microsoft.azure.keyvault.keyvaultclientextensions.getsecretasync) .

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=deletesecret)]

Możesz sprawdzić, czy klucz tajny został usunięty za pomocą polecenia [AZ The Secret show klucza tajnego](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-show) :

```azurecli
az keyvault secret show --vault-name <your-unique-keyvault-name> --name mySecret
```

```azurepowershell
(Get-AzKeyVaultSecret -VaultName <your-unique-keyvault-name> -Name mySecret).SecretValueText
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

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

## <a name="sample-code"></a>Przykładowy kod

Zmodyfikuj aplikację konsolową .NET Core, aby móc korzystać z Key Vault, wykonując następujące czynności:

1. Zastąp kod w pliku *Program.cs* następującym kodem:

    ```csharp
    using System;
    using Azure.Identity;
    using Azure.Security.KeyVault.Secrets;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static void Main(string[] args)
            {
                const string secretName = "mySecret";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write("Input the value of your secret > ");
                var secretValue = Console.ReadLine();
    
                Console.Write($"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...");
                client.SetSecret(secretName, secretValue);
                Console.WriteLine(" done.");
    
                Console.WriteLine("Forgetting your secret.");
                secretValue = string.Empty;
                Console.WriteLine($"Your secret is '{secretValue}'.");
    
                Console.WriteLine($"Retrieving your secret from {keyVaultName}.");
                KeyVaultSecret secret = client.GetSecret(secretName);
                Console.WriteLine($"Your secret is '{secret.Value}'.");
    
                Console.Write($"Deleting your secret from {keyVaultName} ...");
                client.StartDeleteSecret(secretName);
                System.Threading.Thread.Sleep(5000);
                Console.WriteLine(" done.");
            }
        }
    }
    ```

1. Wykonaj następujące polecenie, aby uruchomić aplikację.

    ```dotnetcli
    dotnet run
    ```

1. Po wyświetleniu monitu wprowadź wartość klucza tajnego. Na przykład mySecretPassword.

    Zostanie wyświetlona odmiana następujących danych wyjściowych:

    ```console
    Input the value of your secret > mySecretPassword
    Creating a secret in <your-unique-keyvault-name> called 'mySecret' with the value 'mySecretPassword' ... done.
    Forgetting your secret.
    Your secret is ''.
    Retrieving your secret from <your-unique-keyvault-name>.
    Your secret is 'mySecretPassword'.
    Deleting your secret from <your-unique-keyvault-name> ... done.    
    ```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono Magazyn kluczy, Zapisano wpis tajny i pobrano ten klucz tajny. Zobacz [całą aplikację konsolową w](https://github.com/Azure-Samples/key-vault-dotnet-core-quickstart/tree/master/key-vault-console-app)serwisie GitHub.

Aby dowiedzieć się więcej na temat Key Vault i sposobu integrowania go z aplikacjami, zobacz następujące artykuły:

- Zapoznaj się [z omówieniem Azure Key Vault](../general/overview.md)
- Zobacz [przewodnik dewelopera Azure Key Vault](../general/developers-guide.md)
- Przegląd [Azure Key Vault najlepszych](../general/best-practices.md) rozwiązań
