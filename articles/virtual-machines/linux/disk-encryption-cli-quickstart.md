---
title: Tworzenie i szyfrowanie maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć i zaszyfrować maszynę wirtualną z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines
ms.collection: linux
ms.subservice: disks
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5b98fde5e15a3c57b56ecc8aea60023ffb8c22a8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774309"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-with-the-azure-cli"></a>Szybki start: tworzenie i szyfrowanie maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów Azure i zarządzanie nimi z poziomu wiersza polecenia lub skryptów. W tym przewodniku Szybki start pokazano, jak za pomocą interfejsu wiersza polecenia platformy Azure utworzyć i zaszyfrować maszynę wirtualną z systemem Linux.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia platformy Azure i używać go lokalnie, ten przewodnik Szybki start będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w *lokalizacji eastus:*

```azurecli-interactive
az group create --name "myResourceGroup" --location "eastus"
```

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm#az_vm_create). W poniższym przykładzie utworzono maszynę wirtualną o nazwie *myVM*.

```azurecli-interactive
az vm create \
    --resource-group "myResourceGroup" \
    --name "myVM" \
    --image "Canonical:UbuntuServer:16.04-LTS:latest" \
    --size "Standard_D2S_V3"\
    --generate-ssh-keys
```

Utworzenie maszyny wirtualnej i zasobów pomocniczych potrwa kilka minut. Następujące przykładowe dane wyjściowe pokazują, że operacja utworzenia maszyny wirtualnej zakończyła się pomyślnie.

```
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Tworzenie klucza Key Vault skonfigurowanego dla kluczy szyfrowania

Usługa Azure Disk Encryption przechowuje klucz szyfrowania w Azure Key Vault. Utwórz nową Key Vault za pomocą [az keyvault create](/cli/azure/keyvault#az_keyvault_create). Aby włączyć Key Vault do przechowywania kluczy szyfrowania, użyj parametru --enabled-for-disk-encryption.

> [!Important]
> Każdy magazyn kluczy musi mieć nazwę, która jest unikatowa na platformie Azure. W poniższych przykładach zastąp <your-unique-keyvault-name> nazwą.

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>Szyfrowanie maszyny wirtualnej

Zaszyfruj maszynę wirtualną za pomocą Key Vault , podając unikatową nazwę maszyny wirtualnej dla parametru --disk-encryption-keyvault. [](/cli/azure/vm/encryption)

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>"
```

Po chwili proces zwróci "Żądanie szyfrowania zostało zaakceptowane. Użyj polecenia "show", aby monitorować postęp.". Polecenie "show" to [az vm show](/cli/azure/vm/encryption#az_vm_encryption_show).

```azurecli-interactive
az vm encryption show --name "myVM" -g "MyResourceGroup"
```

Po włączeniu szyfrowania w zwracanych danych wyjściowych zobaczysz następujące informacje:

```
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, maszyna wirtualna i grupa zasobów nie będą już potrzebne, możesz je usunąć za pomocą polecenia [az group delete](/cli/azure/group) Key Vault. 

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono maszynę wirtualną, utworzono maszynę wirtualną, Key Vault włączyć klucze szyfrowania i zaszyfrowano maszynę wirtualną.  Aby dowiedzieć się więcej o większej Azure Disk Encryption maszyn wirtualnych z systemem Linux, należy przejść do następnego artykułu.

> [!div class="nextstepaction"]
> [Azure Disk Encryption omówienie](disk-encryption-overview.md)
