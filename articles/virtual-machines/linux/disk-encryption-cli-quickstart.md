---
title: Tworzenie i szyfrowanie maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure
description: W tym przewodniku szybki start dowiesz się, jak utworzyć i zaszyfrować maszynę wirtualną z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machines
ms.collection: linux
ms.subservice: disks
ms.topic: quickstart
ms.date: 05/17/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: b600c895f98c46dbaafce7deef86bbbee8f57c99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102561045"
---
# <a name="quickstart-create-and-encrypt-a-linux-vm-with-the-azure-cli"></a>Szybki Start: Tworzenie i szyfrowanie maszyny wirtualnej z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów Azure i zarządzanie nimi z poziomu wiersza polecenia lub skryptów. W tym przewodniku szybki start pokazano, jak utworzyć i zaszyfrować maszynę wirtualną z systemem Linux przy użyciu interfejsu wiersza polecenia platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia platformy Azure i korzystać z niego lokalnie, ten przewodnik Szybki Start będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.30 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure]( /cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group#az-group-create). Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *Wschodnie* :

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

## <a name="create-a-key-vault-configured-for-encryption-keys"></a>Utwórz Key Vault skonfigurowany pod kątem kluczy szyfrowania

Klucz szyfrowania w usłudze Azure Disk Encryption jest przechowywany w Azure Key Vault. Utwórz Key Vault przy użyciu [AZ kluczy Create](/cli/azure/keyvault#az_keyvault_create). Aby umożliwić Key Vault przechowywanie kluczy szyfrowania, użyj parametru--enabled-for-Disk-Encryption.

> [!Important]
> Każdy Magazyn kluczy musi mieć unikatową nazwę na platformie Azure. W poniższych przykładach Zastąp <unikatową nazwą magazynu kluczy> z wybraną nazwą.

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

## <a name="encrypt-the-virtual-machine"></a>Szyfruj maszynę wirtualną

Zaszyfruj maszynę wirtualną za pomocą [AZ VM Encryption](/cli/azure/vm/encryption), podając unikatową nazwę Key Vaultową dla parametru--Disk-Encryption-Parameter.

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>"
```

Po chwili, gdy proces zostanie zwrócony, "żądanie szyfrowania zostało zaakceptowane. Użyj polecenia "show", aby monitorować postęp ". Polecenie "show" to [AZ VM show](/cli/azure/vm/encryption#az-vm-encryption-show).

```azurecli-interactive
az vm encryption show --name "myVM" -g "MyResourceGroup"
```

Po włączeniu szyfrowania w zwróconych danych wyjściowych zostaną wyświetlone następujące elementy:

```
"EncryptionOperation": "EnableEncryption"
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, maszyna wirtualna i Key Vault nie będą już potrzebne, można je usunąć za pomocą polecenia [AZ Group Delete](/cli/azure/group) . 

```azurecli-interactive
az group delete --name "myResourceGroup"
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono maszynę wirtualną, która utworzyła Key Vault, w której włączono obsługę kluczy szyfrowania, oraz zaszyfrowaną MASZYNę wirtualną.  Przejdź do następnego artykułu, aby dowiedzieć się więcej o dodatkowych Azure Disk Encryption dotyczących maszyn wirtualnych z systemem Linux.

> [!div class="nextstepaction"]
> [Przegląd Azure Disk Encryption](disk-encryption-overview.md)
