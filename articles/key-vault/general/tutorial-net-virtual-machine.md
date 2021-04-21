---
title: Samouczek — używanie Azure Key Vault z maszyną wirtualną na platformie .NET | Microsoft Docs
description: W tym samouczku skonfigurujesz maszynę wirtualną jako podstawową ASP.NET do odczytu klucza tajnego z magazynu kluczy.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: mbaldwin
ms.custom: mvc, devx-track-csharp, devx-track-azurepowershell
ms.openlocfilehash: c08d0c210e992cba5bca2695fda0bcf08c4689dc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772095"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-net"></a>Samouczek: używanie Azure Key Vault z maszyną wirtualną na platformie .NET

Azure Key Vault pomaga chronić wpisy tajne, takie jak klucze interfejsu API, parametry połączenia bazy danych potrzebne do uzyskiwania dostępu do aplikacji, usług i zasobów IT.

Z tego samouczka dowiesz się, jak pobrać aplikację konsolą do odczytywania informacji z Azure Key Vault. Aplikacja będzie używać tożsamości zarządzanej maszyny wirtualnej do uwierzytelniania w Key Vault. 

Ten samouczek przedstawia sposób wykonania następujących czynności:

> [!div class="checklist"]
> * Utwórz grupę zasobów.
> * Utwórz magazyn kluczy.
> * Dodawanie wpisu tajnego do magazynu kluczy.
> * Pobieranie wpisu tajnego z magazynu kluczy.
> * Tworzenie maszyny wirtualnej platformy Azure.
> * Włącz tożsamość [zarządzaną](../../active-directory/managed-identities-azure-resources/overview.md) dla maszyny wirtualnej.
> * Przypisywanie uprawnień do tożsamości maszyny wirtualnej.

Przed rozpoczęciem zapoznaj się [z Key Vault podstawowymi pojęciami.](basic-concepts.md) 

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

W przypadku systemów Windows, Mac i Linux:
  * [Usługa Git](https://git-scm.com/downloads)
  * Zestaw [SDK platformy .NET Core 3.1 lub nowszy.](https://dotnet.microsoft.com/download/dotnet-core/3.1)
  * [Interfejs wiersza polecenia](/cli/azure/install-azure-cli) platformy Azure [lub Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="create-resources-and-assign-permissions"></a>Tworzenie zasobów i przypisywanie uprawnień

Przed rozpoczęciem kodowania należy utworzyć zasoby, umieścić klucz tajny w magazynie kluczy i przypisać uprawnienia.

### <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Aby zalogować się do platformy Azure za pomocą następującego polecenia:

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
```azurecli
az login
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Connect-AzAccount
```
---

## <a name="create-a-resource-group-and-key-vault"></a>Tworzenie grupy zasobów i magazynu kluczy

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="populate-your-key-vault-with-a-secret"></a>Wypełnianie magazynu kluczy kluczem tajnym

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej
Utwórz maszynę wirtualną z systemem Windows lub Linux przy użyciu jednej z następujących metod:

| Windows | Linux |
|--|--|
| [Interfejs wiersza polecenia platformy Azure](../../virtual-machines/windows/quick-create-cli.md) | [Interfejs wiersza polecenia platformy Azure](../../virtual-machines/linux/quick-create-cli.md) |  
| [Program PowerShell](../../virtual-machines/windows/quick-create-powershell.md) | [Program PowerShell](../../virtual-machines/linux/quick-create-powershell.md) |
| [Witryna Azure Portal](../../virtual-machines/windows/quick-create-portal.md) | [Witryna Azure Portal](../../virtual-machines/linux/quick-create-portal.md) |

## <a name="assign-an-identity-to-the-vm"></a>Przypisywanie tożsamości do maszyny wirtualnej
Utwórz tożsamość przypisaną przez system dla maszyny wirtualnej w następującym przykładzie:

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Zanotuj tożsamość przypisaną przez system, która jest wyświetlana w poniższym kodzie. Dane wyjściowe poprzedniego polecenia będą:

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$vm = Get-AzVM -Name <NameOfYourVirtualMachine>
Update-AzVM -ResourceGroupName <YourResourceGroupName> -VM $vm -IdentityType SystemAssigned
```

Zanotuj principalId, który jest wyświetlany w poniższym kodzie. Dane wyjściowe poprzedniego polecenia będą: 


```output
PrincipalId          TenantId             Type             UserAssignedIdentities
-----------          --------             ----             ----------------------
xxxxxxxx-xx-xxxxxx   xxxxxxxx-xxxx-xxxx   SystemAssigned
```
---

## <a name="assign-permissions-to-the-vm-identity"></a>Przypisywanie uprawnień do tożsamości maszyny wirtualnej
Przypisz utworzone wcześniej uprawnienia tożsamości do magazynu kluczy za pomocą [polecenia az keyvault set-policy:](/cli/azure/keyvault#az_keyvault_set_policy)

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)
```azurecli
az keyvault set-policy --name '<your-unique-key-vault-name>' --object-id <VMSystemAssignedIdentity> --secret-permissions  get list set delete
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Set-AzKeyVaultAccessPolicy -ResourceGroupName <YourResourceGroupName> -VaultName '<your-unique-key-vault-name>' -ObjectId '<VMSystemAssignedIdentity>' -PermissionsToSecrets  get,list,set,delete
```
---

## <a name="sign-in-to-the-virtual-machine"></a>Logowanie do maszyny wirtualnej

Aby zalogować się do maszyny wirtualnej, postępuj zgodnie z instrukcjami w temacie Connect and sign in to an Azure Windows virtual machine (Nawiązywanie połączenia z maszyną wirtualną z systemem Windows na platformie [Azure)](../../virtual-machines/windows/connect-logon.md) lub Connect and sign in to an Azure Linux virtual machine (Nawiązywanie połączenia z maszyną wirtualną z [systemem Linux na platformie Azure).](../../virtual-machines/linux/login-using-aad.md)

## <a name="set-up-the-console-app"></a>Konfigurowanie aplikacji konsolowej

Utwórz aplikację konsoli i zainstaluj wymagane pakiety za pomocą `dotnet` polecenia .

### <a name="install-net-core"></a>Instalowanie programu .NET Core

Aby zainstalować program .NET Core, przejdź do strony [pobierania programu .NET.](https://www.microsoft.com/net/download)

### <a name="create-and-run-a-sample-net-app"></a>Tworzenie i uruchamianie przykładowej aplikacji .NET

Otwórz wiersz polecenia.

Polecenie "Hello world" można wydrukować w konsoli, uruchamiając następujące polecenia:

```console
dotnet new console -n keyvault-console-app
cd keyvault-console-app
dotnet run
```

### <a name="install-the-package"></a>Instalowanie pakietu

W oknie konsoli zainstaluj bibliotekę klienta Azure Key Vault Secrets dla programu .NET:

```console
dotnet add package Azure.Security.KeyVault.Secrets
```

W tym przewodniku Szybki start musisz zainstalować następujący pakiet tożsamości w celu uwierzytelnienia w Azure Key Vault:

```console
dotnet add package Azure.Identity
```

## <a name="edit-the-console-app"></a>Edytowanie aplikacji konsolowej

Otwórz plik *Program.cs* i dodaj następujące pakiety:

```csharp
using System;
using Azure.Core;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
```

Dodaj następujące wiersze, aktualizując adres URI w celu `vaultUri` odzwierciedlenia wartości magazynu kluczy. Poniższy kod używa metody  ["DefaultAzureCredential()"](/dotnet/api/azure.identity.defaultazurecredential) do uwierzytelniania w magazynie kluczy, który używa tokenu z tożsamości zarządzanej aplikacji do uwierzytelniania. Korzysta również z wykładniczego odroczania dla ponownych prób w przypadku ograniczenia magazynu kluczy.

```csharp
  class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";
            string keyVaultName = "<your-key-vault-name>";
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

Gdy maszyna wirtualna i magazyn kluczy nie będą już potrzebne, usuń je.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Interfejsy API REST usługi Azure Key Vault](/rest/api/keyvault/)
