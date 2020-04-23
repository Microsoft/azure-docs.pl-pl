---
title: Tworzenie i szyfrowanie maszyny wirtualnej z systemem Windows przy użyciu interfejsu wiersza polecenia platformy Azure
description: W tym przewodniku Szybki start dowiesz się, jak używać interfejsu wiersza polecenia platformy Azure do tworzenia i szyfrowania maszyny wirtualnej systemu Windows
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: quickstart
ms.date: 05/17/2019
ms.openlocfilehash: aa0dd0bf55d51800d6a9b4283aa9e653887bf2f1
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082802"
---
# <a name="quickstart-create-and-encrypt-a-windows-vm-with-the-azure-cli"></a>Szybki start: tworzenie i szyfrowanie maszyny Wirtualnej systemu Windows za pomocą interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów Azure i zarządzanie nimi z poziomu wiersza polecenia lub skryptów. Ten przewodnik Szybki start pokazuje, jak używać interfejsu wiersza polecenia platformy Azure do tworzenia i szyfrowania maszyny wirtualnej systemu Windows Server 2016.This Quickstart shows you how to use the Azure CLI to create and encrypt a Windows Server 2016 virtual machine (VM).

Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) przed rozpoczęciem.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i używać interfejsu wiersza polecenia platformy Azure lokalnie, ten szybki start wymaga, aby uruchomić interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład tworzy grupę zasobów o nazwie *myResourceGroup* w lokalizacji *eastus:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

Utwórz maszynę wirtualną za pomocą polecenia [az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create). W poniższym przykładzie utworzono maszynę wirtualną o nazwie *myVM*. W tym przykładzie *azureuser* to nazwa użytkownika administracyjnego, a *myPassword12* to hasło.

```azurecli-interactive
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image win2016datacenter \
    --admin-username azureuser \
    --admin-password myPassword12
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

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Tworzenie magazynu kluczy skonfigurowanych dla kluczy szyfrowania

Szyfrowanie dysków platformy Azure przechowuje swój klucz szyfrowania w magazynie azure key vault. Utwórz magazyn kluczy za pomocą [programu az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create). Aby włączyć magazyn kluczy do przechowywania kluczy szyfrowania, należy użyć parametru --enabled-for-disk-encryption.
> [!Important]
> Każda przechowalnia kluczy musi mieć unikatową nazwę. Poniższy przykład tworzy magazyn kluczy o nazwie *myKV*, ale należy nazwać coś innego.

```azurecli-interactive
az keyvault create --name "myKV" --resource-group "myResourceGroup" --location eastus --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>Szyfruj maszynę wirtualną

Zaszyfruj [maszynę wirtualną za pomocą szyfrowania az vm,](/cli/azure/vm/encryption?view=azure-cli-latest)podając unikatową nazwę usługi Key Vault parametrowi --disk-encryption-keyvault.

```azurecli-interactive
az vm encryption enable -g MyResourceGroup --name MyVM --disk-encryption-keyvault myKV
```

Można sprawdzić, czy szyfrowanie jest włączone na maszynie wirtualnej za pomocą [programu AZ VM](/cli/azure/vm/encryption#az-vm-encryption-show)

```azurecli-interactive
az vm show --name MyVM -g MyResourceGroup
```

Na zwróconym wyjściu zostaną wyświetlene następujące informacje:

```
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie jest już potrzebne, można użyć polecenia [delete grupy az,](/cli/azure/group) aby usunąć grupę zasobów, maszynę wirtualną i magazyn kluczy.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono maszynę wirtualną, utworzono magazyn kluczy, który był włączony dla kluczy szyfrowania, i zaszyfrowano maszynę wirtualną.  Przejdź do następnego artykułu, aby dowiedzieć się więcej na temat wymagań wstępnych usługi Azure Disk Encryption dotyczących maszyn wirtualnych IaaS.

> [!div class="nextstepaction"]
> [Omówienie szyfrowania dysków platformy Azure](disk-encryption-overview.md)
