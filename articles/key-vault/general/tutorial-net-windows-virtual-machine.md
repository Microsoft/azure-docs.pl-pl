---
title: Samouczek — używanie Azure Key Vault z maszyną wirtualną z systemem Windows w środowisku .NET | Microsoft Docs
description: W tym samouczku skonfigurujesz aplikację ASP.NET Core w celu odczytu wpisu tajnego z magazynu kluczy.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 01/02/2019
ms.author: mbaldwin
ms.custom: mvc
ms.openlocfilehash: 8db1c511ab9defb140720655588b27279a0f08be
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86085484"
---
# <a name="tutorial-use-azure-key-vault-with-a-windows-virtual-machine-in-net"></a>Samouczek: używanie Azure Key Vault z maszyną wirtualną z systemem Windows w środowisku .NET

Azure Key Vault pomaga chronić wpisy tajne, takie jak klucze interfejsu API, parametry połączenia bazy danych potrzebne do uzyskiwania dostępu do aplikacji, usług i zasobów IT.

W ramach tego samouczka nauczysz się, jak uzyskać aplikację konsolową do odczytu informacji z Azure Key Vault. Aplikacja będzie używać tożsamości zarządzanej przez maszynę wirtualną do uwierzytelniania w Key Vault. 

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Utwórz grupę zasobów.
> * Tworzenie magazynu kluczy.
> * Dodawanie wpisu tajnego do magazynu kluczy.
> * Pobieranie wpisu tajnego z magazynu kluczy.
> * Tworzenie maszyny wirtualnej platformy Azure.
> * Włącz [zarządzaną tożsamość](../../active-directory/managed-identities-azure-resources/overview.md) maszyny wirtualnej.
> * Przypisz uprawnienia do tożsamości maszyny wirtualnej.

Przed rozpoczęciem Przeczytaj [Key Vault podstawowe pojęcia](basic-concepts.md). 

Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Dla systemów Windows, Mac i Linux:
  * [Git](https://git-scm.com/downloads)
  * [Zestaw .NET Core 3,1 SDK lub nowszy](https://dotnet.microsoft.com/download/dotnet-core/3.1).
  * [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-resources-and-assign-permissions"></a>Tworzenie zasobów i przypisywanie uprawnień

Przed rozpoczęciem kodowania należy utworzyć pewne zasoby, umieścić wpis tajny w magazynie kluczy i przypisać uprawnienia.

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Aby zalogować się do platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure, wpisz:

```azurecli
az login
```

### <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). 

Ten przykład tworzy grupę zasobów w lokalizacji zachodnie stany USA:

```azurecli
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Nowo utworzona grupa zasobów zostanie użyta w tym samouczku.

### <a name="create-a-key-vault-and-populate-it-with-a-secret"></a>Utwórz magazyn kluczy i wypełnij go wpisem tajnym

Utwórz magazyn kluczy w grupie zasobów, dostarczając polecenie AZ Key [magazynu Create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) z następującymi informacjami:

* Nazwa magazynu kluczy: ciąg od 3 do 24 znaków, który może zawierać tylko cyfry (0-9), litery (a-z, A-Z) i łączniki (-)
* Nazwa grupy zasobów
* Lokalizacja: **zachodnie stany USA**

```azurecli
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```
W tym momencie Twoje konto platformy Azure jest jedyną osobą, która ma uprawnienia do wykonywania operacji na tym nowym magazynie kluczy.

Teraz Dodaj wpis tajny do magazynu kluczy za pomocą polecenia AZ Key Key [Secret Set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set)


Aby utworzyć wpis tajny w magazynie kluczy o nazwie **AppSecret**, wprowadź następujące polecenie:

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

Ten wpis tajny zawiera wartość **MySecret**.

### <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej
Utwórz maszynę wirtualną za pomocą jednej z następujących metod:

* [Interfejs wiersza polecenia platformy Azure](../../virtual-machines/windows/quick-create-cli.md)
* [Program PowerShell](../../virtual-machines/windows/quick-create-powershell.md)
* [Witryna Azure Portal](../../virtual-machines/windows/quick-create-portal.md)

### <a name="assign-an-identity-to-the-vm"></a>Przypisywanie tożsamości do maszyny wirtualnej
Utwórz tożsamość przypisaną do systemu dla maszyny wirtualnej za pomocą polecenia [AZ VM Identity Assign](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign) :

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Zanotuj tożsamość przypisaną przez system, która jest wyświetlana w poniższym kodzie. Dane wyjściowe poprzedniego polecenia byłyby następujące: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

### <a name="assign-permissions-to-the-vm-identity"></a>Przypisywanie uprawnień do tożsamości maszyny wirtualnej
Przypisz wcześniej utworzone uprawnienia tożsamości do magazynu kluczy za pomocą polecenia [AZ Key magazynu Set-Policy](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-set-policy) :

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

### <a name="sign-in-to-the-virtual-machine"></a>Zaloguj się do maszyny wirtualnej

Aby zalogować się do maszyny wirtualnej, postępuj zgodnie z instrukcjami podanymi w temacie [łączenie i logowanie do maszyny wirtualnej platformy Azure z systemem Windows](../../virtual-machines/windows/connect-logon.md).

## <a name="set-up-the-console-app"></a>Konfigurowanie aplikacji konsolowej

Utwórz aplikację konsolową i zainstaluj wymagane pakiety przy użyciu `dotnet` polecenia.

### <a name="install-net-core"></a>Instalowanie programu .NET Core

Aby zainstalować program .NET Core, przejdź do strony [plików do pobrania platformy .NET](https://www.microsoft.com/net/download) .

### <a name="create-and-run-a-sample-net-app"></a>Tworzenie i uruchamianie przykładowej aplikacji .NET

Otwórz wiersz polecenia.

"Hello world" można wydrukować do konsoli programu, uruchamiając następujące polecenia:

```console
dotnet new console -n keyvault-console-app
cd keyvault-console-app
dotnet run
```

### <a name="install-the-package"></a>Zainstaluj pakiet

W oknie konsoli programu Zainstaluj bibliotekę klienta Azure Key Vault Secret dla platformy .NET:

```console
dotnet add package Azure.Security.KeyVault.Secrets
```

W tym przewodniku Szybki Start należy zainstalować następujący pakiet tożsamości w celu uwierzytelnienia w usłudze Azure Key Vault:

```console
dotnet add package Azure.Identity
```

## <a name="edit-the-console-app"></a>Edytowanie aplikacji konsolowej

Otwórz plik *program.cs* i Dodaj następujące pakiety:

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
```

Dodaj te wiersze, aktualizując identyfikator URI w celu odzwierciedlenia `vaultUri` Twojego magazynu kluczy. Poniższy kod używa [elementu "DefaultAzureCredential ()"](/dotnet/api/azure.identity.defaultazurecredential?view=azure-dotnet) do uwierzytelniania w magazynie kluczy, który używa tokenu z tożsamości zarządzanej przez aplikację do uwierzytelniania. Jest również używany wycofywania wykładniczy do ponawiania prób w przypadku ograniczania magazynu kluczy.

```csharp
  class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";

            var kvUri = "https://<your-key-vault-name>.vault.azure.net";
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

            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential(),options);

            Console.Write("Input the value of your secret > ");
            string secretValue = Console.ReadLine();

            Console.Write("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");

            client.SetSecret(secretName, secretValue);

            Console.WriteLine(" done.");

            Console.WriteLine("Forgetting your secret.");
            secretValue = "";
            Console.WriteLine("Your secret is '" + secretValue + "'.");

            Console.WriteLine("Retrieving your secret from " + keyVaultName + ".");

            KeyVaultSecret secret = client.GetSecret(secretName);

            Console.WriteLine("Your secret is '" + secret.Value + "'.");

            Console.Write("Deleting your secret from " + keyVaultName + " ...");

            client.StartDeleteSecret(secretName);

            System.Threading.Thread.Sleep(5000);
            Console.WriteLine(" done.");

        }
    }
```

Powyższy kod pokazuje, jak wykonać operacje w usłudze Azure Key Vault na maszynie wirtualnej z systemem Windows.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie są już potrzebne, Usuń maszynę wirtualną i Magazyn kluczy.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Interfejsy API REST usługi Azure Key Vault](https://docs.microsoft.com/rest/api/keyvault/)
