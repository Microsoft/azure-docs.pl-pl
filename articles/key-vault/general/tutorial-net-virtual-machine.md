---
title: Samouczek — używanie Azure Key Vault z maszyną wirtualną w programie .NET | Microsoft Docs
description: W tym samouczku skonfigurujesz maszynę wirtualną aplikacji ASP.NET Core w celu odczytywania wpisu tajnego z magazynu kluczy.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: a56c08e5bf6054d24af3ade571ec625969286a77
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102455648"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-net"></a>Samouczek: używanie Azure Key Vault z maszyną wirtualną w programie .NET

Azure Key Vault pomaga chronić wpisy tajne, takie jak klucze interfejsu API, parametry połączenia bazy danych potrzebne do uzyskiwania dostępu do aplikacji, usług i zasobów IT.

W ramach tego samouczka nauczysz się, jak uzyskać aplikację konsolową do odczytu informacji z Azure Key Vault. Aplikacja będzie używać tożsamości zarządzanej przez maszynę wirtualną do uwierzytelniania w Key Vault. 

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Utwórz grupę zasobów.
> * Utwórz magazyn kluczy.
> * Dodawanie wpisu tajnego do magazynu kluczy.
> * Pobieranie wpisu tajnego z magazynu kluczy.
> * Tworzenie maszyny wirtualnej platformy Azure.
> * Włącz [zarządzaną tożsamość](../../active-directory/managed-identities-azure-resources/overview.md) maszyny wirtualnej.
> * Przypisz uprawnienia do tożsamości maszyny wirtualnej.

Przed rozpoczęciem Przeczytaj [Key Vault podstawowe pojęcia](basic-concepts.md). 

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

Dla systemów Windows, Mac i Linux:
  * [Usługa Git](https://git-scm.com/downloads)
  * [Zestaw .NET Core 3,1 SDK lub nowszy](https://dotnet.microsoft.com/download/dotnet-core/3.1).
  * [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="create-resources-and-assign-permissions"></a>Tworzenie zasobów i przypisywanie uprawnień

Przed rozpoczęciem kodowania należy utworzyć pewne zasoby, umieścić wpis tajny w magazynie kluczy i przypisać uprawnienia.

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Aby zalogować się do platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure, wpisz:

```azurecli
az login
```

## <a name="create-a-resource-group-and-key-vault"></a>Tworzenie grupy zasobów i magazynu kluczy

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="populate-your-key-vault-with-a-secret"></a>Wypełnij swój magazyn kluczy kluczem tajnym

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej
Utwórz maszynę wirtualną z systemem Windows lub Linux przy użyciu jednej z następujących metod:

| Windows | Linux |
|--|--|
| [Interfejs wiersza polecenia platformy Azure](../../virtual-machines/windows/quick-create-cli.md) | [Interfejs wiersza polecenia platformy Azure](../../virtual-machines/linux/quick-create-cli.md) |  
| [Program PowerShell](../../virtual-machines/windows/quick-create-powershell.md) | [Program PowerShell](../../virtual-machines/linux/quick-create-powershell.md) |
| [Witryna Azure Portal](../../virtual-machines/windows/quick-create-portal.md) | [Witryna Azure Portal](../../virtual-machines/linux/quick-create-portal.md) |

## <a name="assign-an-identity-to-the-vm"></a>Przypisywanie tożsamości do maszyny wirtualnej
Utwórz tożsamość przypisaną do systemu dla maszyny wirtualnej za pomocą polecenia [AZ VM Identity Assign](/cli/azure/vm/identity#az-vm-identity-assign) :

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

## <a name="assign-permissions-to-the-vm-identity"></a>Przypisywanie uprawnień do tożsamości maszyny wirtualnej
Przypisz wcześniej utworzone uprawnienia tożsamości do magazynu kluczy za pomocą polecenia [AZ Key magazynu Set-Policy](/cli/azure/keyvault#az-keyvault-set-policy) :

```azurecli
az keyvault set-policy --name '<your-unique-key-vault-name>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="sign-in-to-the-virtual-machine"></a>Zaloguj się do maszyny wirtualnej

Aby zalogować się do maszyny wirtualnej, postępuj zgodnie z instrukcjami podanymi w temacie łączenie się z [maszyną wirtualną platformy Azure](../../virtual-machines/windows/connect-logon.md) lub Nawiązywanie połączenia z maszyną wirtualną platformy Azure z systemem [Linux i logowanie się do](../../virtual-machines/linux/login-using-aad.md)niej.

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

Otwórz plik *program. cs* i Dodaj następujące pakiety:

```csharp
using System;
using Azure.Core;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
```

Dodaj te wiersze, aktualizując identyfikator URI w celu odzwierciedlenia `vaultUri` Twojego magazynu kluczy. Poniższy kod używa  [elementu "DefaultAzureCredential ()"](/dotnet/api/azure.identity.defaultazurecredential) do uwierzytelniania w magazynie kluczy, który używa tokenu z tożsamości zarządzanej przez aplikację do uwierzytelniania. Jest również używany wycofywania wykładniczy do ponawiania prób w przypadku ograniczania magazynu kluczy.

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

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy nie są już potrzebne, Usuń maszynę wirtualną i Magazyn kluczy.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Interfejsy API REST usługi Azure Key Vault](/rest/api/keyvault/)
