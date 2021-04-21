---
title: Samouczek — używanie Azure Key Vault z maszyną wirtualną w języku Python | Microsoft Docs
description: W tym samouczku skonfigurujesz maszynę wirtualną w aplikacji języka Python, aby odczytała klucz tajny z magazynu kluczy.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 25182105db831724565c6bf3dbbbb79832b677f7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772064"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-python"></a>Samouczek: używanie Azure Key Vault z maszyną wirtualną w języku Python

Azure Key Vault pomaga chronić klucze, wpisy tajne i certyfikaty, takie jak klucze interfejsu API i parametry połączenia bazy danych.

W tym samouczku skonfigurujemy aplikację w języku Python do odczytywania informacji z usługi Azure Key Vault przy użyciu tożsamości zarządzanych dla zasobów platformy Azure. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie magazynu kluczy
> * Przechowywanie tajnego Key Vault
> * Tworzenie maszyny wirtualnej z systemem Linux na platformie Azure
> * Włączanie [tożsamości zarządzanej](../../active-directory/managed-identities-azure-resources/overview.md) dla maszyny wirtualnej
> * Przyznaj wymagane uprawnienia aplikacji konsolowej do odczytywania danych z Key Vault
> * Pobieranie tajnego z Key Vault

Przed rozpoczęciem zapoznaj się [z Key Vault podstawowymi pojęciami.](basic-concepts.md) 

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Wymagania wstępne

W przypadku systemów Windows, Mac i Linux:
  * [Usługa Git](https://git-scm.com/downloads)
  * Ten samouczek wymaga uruchomienia interfejsu wiersza polecenia platformy Azure lokalnie. Musisz mieć zainstalowany interfejs wiersza polecenia platformy Azure w wersji 2.0.4 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja wiersza polecenia lub jego uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0](/cli/azure/install-azure-cli).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Aby zalogować się do platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure, wpisz:

```azurecli
az login
```

## <a name="create-a-resource-group-and-key-vault"></a>Tworzenie grupy zasobów i magazynu kluczy

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="populate-your-key-vault-with-a-secret"></a>Wypełnianie magazynu kluczy kluczem tajnym

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę **wirtualną o nazwie myVM** przy użyciu jednej z następujących metod:

| Linux | Windows |
|--|--|
| [Interfejs wiersza polecenia platformy Azure](../../virtual-machines/linux/quick-create-cli.md) | [Interfejs wiersza polecenia platformy Azure](../../virtual-machines/windows/quick-create-cli.md) |
| [Program PowerShell](../../virtual-machines/linux/quick-create-powershell.md) | [Program PowerShell](../../virtual-machines/windows/quick-create-powershell.md) |
| [Witryna Azure Portal](../../virtual-machines/linux/quick-create-portal.md) | [Witryna Azure Portal](../../virtual-machines/windows/quick-create-portal.md) |

Aby utworzyć maszynę wirtualną z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure, użyj [polecenia az vm create.](/cli/azure/vm)  W poniższym przykładzie dodano konto użytkownika o nazwie *azureuser.* Parametr `--generate-ssh-keys` jest używany, aby automatycznie wygenerować klucz SSH i umieścić go w domyślnej lokalizacji klucza (*~/.ssh*). 

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Zanotuj `publicIpAddress` wartość w danych wyjściowych.

## <a name="assign-an-identity-to-the-vm"></a>Przypisywanie tożsamości do maszyny wirtualnej

Utwórz tożsamość przypisaną przez system dla maszyny wirtualnej za pomocą polecenia [az vm identity assign](/cli/azure/vm/identity#az_vm_identity_assign) interfejsu wiersza polecenia platformy Azure:

```azurecli
az vm identity assign --name "myVM" --resource-group "myResourceGroup"
```

Zanotuj tożsamość przypisaną przez system, która jest wyświetlana w poniższym kodzie. Dane wyjściowe poprzedniego polecenia będą: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Przypisywanie uprawnień do tożsamości maszyny wirtualnej

Teraz możesz przypisać wcześniej utworzone uprawnienia tożsamości do magazynu kluczy, uruchamiając następujące polecenie:

```azurecli
az keyvault set-policy --name "<your-unique-keyvault-name>" --object-id "<systemAssignedIdentity>" --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Logowanie się do maszyny wirtualnej

Aby zalogować się do maszyny wirtualnej, postępuj zgodnie z instrukcjami w temacie Connect and sign in to an Azure virtual machine running Linux (Nawiązywanie połączenia z maszyną wirtualną platformy Azure z systemem [Linux)](../../virtual-machines/linux/login-using-aad.md) lub Connect (Połącz) i zaloguj się do maszyny wirtualnej platformy Azure z [systemem Windows.](../../virtual-machines/windows/connect-logon.md)


Aby zalogować się do maszyny wirtualnej z systemem Linux, możesz użyć polecenia ssh z "" podaną w kroku <publicIpAddress> [Tworzenie maszyny wirtualnej:](#create-a-virtual-machine)

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-libraries-on-the-vm"></a>Instalowanie bibliotek języka Python na maszynie wirtualnej

Na maszynie wirtualnej zainstaluj dwie biblioteki języka Python, których będziemy używać w skrypcie języka Python: `azure-keyvault-secrets` i `azure.identity` .  

Na przykład na maszynie wirtualnej z systemem Linux można je zainstalować przy użyciu programu `pip3` :

```bash
pip3 install azure-keyvault-secrets

pip3 install azure.identity
```

## <a name="create-and-edit-the-sample-python-script"></a>Tworzenie i edytowanie przykładowego skryptu języka Python

Na maszynie wirtualnej utwórz plik w języku Python o **nazwie sample.py**. Edytuj plik tak, aby zawierał następujący kod, zastępując <"your-unique-keyvault-name>" nazwą magazynu kluczy:

```python
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = "<your-unique-keyvault-name>"
KVUri = f"https://{keyVaultName}.vault.azure.net"
secretName = "mySecret"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
retrieved_secret = client.get_secret(secretName)

print(f"The value of secret '{secretName}' in '{keyVaultName}' is: '{retrieved_secret.value}'")
```

## <a name="run-the-sample-python-app"></a>Uruchamianie przykładowej aplikacji w języku Python

Na koniec uruchom **sample.py**. Jeśli wszystko poszło dobrze, powinna zostać zwrócona wartość Twojego tajnego:

```bash
python3 sample.py

The value of secret 'mySecret' in '<your-unique-keyvault-name>' is: 'Success!'
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy maszyna wirtualna i magazyn kluczy nie będą już potrzebne, usuń je.  Możesz to zrobić szybko, po prostu usuwając grupę zasobów, do której należą:

```azurecli
az group delete -g myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

[Interfejsy API REST usługi Azure Key Vault](/rest/api/keyvault/)