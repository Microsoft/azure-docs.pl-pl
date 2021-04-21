---
title: Tworzenie i szyfrowanie maszyny wirtualnej z systemem Windows przy użyciu interfejsu wiersza polecenia platformy Azure
description: Z tego przewodnika Szybki start dowiesz się, jak utworzyć i zaszyfrować maszynę wirtualną z systemem Windows przy użyciu interfejsu wiersza polecenia platformy Azure
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: e81d29922bee53ba9021c9c26816258f7922a59c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107759726"
---
# <a name="quickstart-create-and-encrypt-a-windows-vm-with-the-azure-cli"></a>Szybki start: tworzenie i szyfrowanie maszyny wirtualnej z systemem Windows przy użyciu interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów Azure i zarządzanie nimi z poziomu wiersza polecenia lub skryptów. W tym przewodniku Szybki start pokazano, jak za pomocą interfejsu wiersza polecenia platformy Azure utworzyć i zaszyfrować maszynę wirtualną z systemem Windows Server 2016.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az_group_create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład tworzy grupę zasobów o *nazwie myResourceGroup* w *lokalizacji eastus:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm#az_vm_create). W poniższym przykładzie utworzono maszynę wirtualną o nazwie *myVM*. W tym przykładzie *azureuser* to nazwa użytkownika administracyjnego, a *myPassword12* to hasło.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword12
```

Utworzenie maszyny wirtualnej i zasobów pomocniczych potrwa kilka minut. Następujące przykładowe dane wyjściowe pokazują, że operacja utworzenia maszyny wirtualnej zakończyła się pomyślnie.

```console
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

Usługa Azure Disk Encryption przechowuje klucz szyfrowania w Azure Key Vault. Utwórz nową Key Vault za pomocą [az keyvault create](/cli/azure/keyvault#az_keyvault_create). Aby włączyć Key Vault przechowywania kluczy szyfrowania, użyj parametru --enabled-for-disk-encryption.
> [!Important]
> Każda Key Vault musi mieć unikatową nazwę. W poniższym przykładzie zostanie Key Vault o nazwie *myKV,* ale musisz podać inną nazwę.

```azurecli-interactive
az keyvault create --name "myKV" --resource-group "myResourceGroup" --location eastus --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>Szyfrowanie maszyny wirtualnej

Zaszyfruj maszynę wirtualną za pomocą parametru [az vm encryption](/cli/azure/vm/encryption), podając unikatową nazwę Key Vault parametru --disk-encryption-keyvault.

```azurecli-interactive
az vm encryption enable -g MyResourceGroup --name MyVM --disk-encryption-keyvault myKV
```

Możesz sprawdzić, czy szyfrowanie jest włączone na maszynie wirtualnej, za pomocą [az vm show](/cli/azure/vm/encryption#az_vm_encryption_show)

```azurecli-interactive
az vm encryption show --name MyVM -g MyResourceGroup
```

W zwróconych danych wyjściowych zostaną zwrócone następujące dane:

```console
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, maszyna wirtualna i grupa zasobów nie będą już potrzebne, można je usunąć za pomocą polecenia [az group delete](/cli/azure/group) Key Vault.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono maszynę wirtualną, utworzono maszynę wirtualną, Key Vault włączyć klucze szyfrowania i zaszyfrowano maszynę wirtualną.  Przejdź do następnego artykułu, aby dowiedzieć się więcej na temat wymagań wstępnych usługi Azure Disk Encryption dotyczących maszyn wirtualnych IaaS.

> [!div class="nextstepaction"]
> [Azure Disk Encryption omówienie](disk-encryption-overview.md)
