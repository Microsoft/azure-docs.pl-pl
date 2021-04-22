---
title: Szyfrowanie dysków dla zestawów skalowania platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure do szyfrowania wystąpień maszyn wirtualnych i dołączonych dysków w zestawie skalowania maszyn wirtualnych z systemem Windows
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/15/2019
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: af870b3be9c2ab7022a05c9cf9e3a662c5850214
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107875075"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli"></a>Szyfrowanie systemu operacyjnego i dołączonych dysków danych w zestawie skalowania maszyn wirtualnych za pomocą interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów Azure i zarządzanie nimi z poziomu wiersza polecenia lub skryptów. W tym przewodniku Szybki start pokazano, jak za pomocą interfejsu wiersza polecenia platformy Azure utworzyć i zaszyfrować zestaw skalowania maszyn wirtualnych. Aby uzyskać więcej informacji na temat stosowania usługi Azure Disk Encryption do zestawu skalowania maszyn wirtualnych, zobacz [Azure Disk Encryption for Virtual Machine Scale Sets](disk-encryption-overview.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.31 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="create-a-scale-set"></a>Tworzenie zestawu skalowania

Zanim będzie można utworzyć zestaw skalowania, utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład tworzy grupę zasobów o *nazwie myResourceGroup* w *lokalizacji eastus:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Teraz utwórz zestaw skalowania maszyn wirtualnych przy użyciu polecenia [az vmss create](/cli/azure/vmss). Poniższy przykład tworzy zestaw skalowania o nazwie *myScaleSet*, który jest skonfigurowany do automatycznej aktualizacji w miarę stosowania zmian, a następnie generuje klucze SSH, jeśli nie istnieją, w lokalizacji *~/.ssh/id_rsa*. Do każdego wystąpienia maszyny wirtualnej jest dołączony dysk danych o rozmiarze 32 GB, a rozszerzenie niestandardowego skryptu platformy [Azure](../virtual-machines/extensions/custom-script-linux.md) służy do przygotowywania dysków danych za pomocą polecenia [az vmss extension set:](/cli/azure/vmss/extension)

```azurecli-interactive
# Create a scale set with attached data disk
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys \
  --data-disk-sizes-gb 32

# Prepare the data disk for use with the Custom Script Extension
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/prepare_vm_disks.sh"],"commandToExecute":"./prepare_vm_disks.sh"}'
```

Utworzenie i skonfigurowanie wszystkich zasobów zestawu skalowania i maszyn wirtualnych trwa kilka minut.

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Tworzenie magazynu kluczy platformy Azure z włączoną obsługą szyfrowania dysków

Azure Key Vault mogą przechowywać klucze, wpisy tajne lub hasła, które umożliwiają bezpieczne wdrożenie ich w aplikacjach i usługach. Klucze kryptograficzne są przechowywane w programie Azure Key Vault przy użyciu ochrony oprogramowania lub można zaimportować lub wygenerować klucze w sprzętowych modułach zabezpieczeń (HSM) certyfikowanych zgodnie ze standardami FIPS 140-2 poziom 2. Te klucze kryptograficzne są używane do szyfrowania i odszyfrowywania dysków wirtualnych dołączonych do maszyny wirtualnej. Zachowujesz kontrolę nad tymi kluczami kryptograficznymi i możesz kontrolować ich użycie.

Zdefiniuj własne *unikatowe keyvault_name*. Następnie utwórz usługę KeyVault za pomocą narzędzia [az keyvault create](/cli/azure/keyvault#az_keyvault_create) w tej samej subskrypcji i regionie co zestaw skalowania i ustaw zasady dostępu *--enabled-for-disk-encryption.*

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>Używanie istniejącego Key Vault

Ten krok jest wymagany tylko w przypadku istniejących Key Vault, których chcesz użyć z szyfrowaniem dysków. Pomiń ten krok, jeśli utworzono Key Vault w poprzedniej sekcji.

Zdefiniuj własne *unikatowe keyvault_name*. Następnie zaktualizowano usługę KeyVault za pomocą narzędzia [az keyvault update](/cli/azure/keyvault#az_keyvault_update) i ustawiono zasady dostępu *--enabled-for-disk-encryption.*

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Włączanie szyfrowania

Aby zaszyfrować wystąpienia maszyn wirtualnych w zestawie skalowania, najpierw uzyskaj informacje na temat identyfikatora zasobu Key Vault za pomocą az [keyvault show](/cli/azure/keyvault#az_keyvault_show). Te zmienne są używane do rozpoczęcia procesu szyfrowania za pomocą az [vmss encryption enable](/cli/azure/vmss/encryption#az_vmss_encryption_enable):

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

Uruchomienie procesu szyfrowania może potrwać minutę lub dwie.

Ponieważ zasady uaktualniania zestawu skalowania w zestawie skalowania utworzonym we wcześniejszym kroku są ustawione na automatyczne *,* wystąpienia maszyn wirtualnych automatycznie uruchamiają proces szyfrowania. W zestawach skalowania, w których zasady uaktualniania są ręczne, uruchom zasady szyfrowania na wystąpieniach maszyn wirtualnych za pomocą instrukcji [az vmss update-instances.](/cli/azure/vmss#az_vmss_update_instances)

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Włączanie szyfrowania przy użyciu klucza szyfrowania kluczy do zawijania klucza

Można również użyć klucza szyfrowania klucza w celu dodania zabezpieczeń podczas szyfrowania zestawu skalowania maszyn wirtualnych.

```azurecli-interactive
# Get the resource ID of the Key Vault
vaultResourceId=$(az keyvault show --resource-group myResourceGroup --name $keyvault_name --query id -o tsv)

# Enable encryption of the data disks in a scale set
az vmss encryption enable \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --disk-encryption-keyvault $vaultResourceId \
    --key-encryption-key myKEK \
    --key-encryption-keyvault $vaultResourceId \
    --volume-type DATA
```

> [!NOTE]
>  Składnia wartości parametru disk-encryption-keyvault jest ciągiem pełnego identyfikatora:</br>
/subscriptions/[subscription-id-guid]/resourceGroups/[nazwa grupy zasobów]/providers/Microsoft.KeyVault/vaults/[nazwa-magazynu-kluczy]</br></br>
> Składnia wartości parametru key-encryption-key to pełny adres URI klucza szyfrowania klucza, tak jak w:</br>
https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]

## <a name="check-encryption-progress"></a>Sprawdzanie postępu szyfrowania

Aby sprawdzić stan szyfrowania dysku, użyj [az vmss encryption show](/cli/azure/vmss/encryption#az_vmss_encryption_show):

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

Gdy wystąpienia maszyn wirtualnych są szyfrowane, kod stanu zgłasza stan *EncryptionState/encrypted,* jak pokazano w następujących przykładowych danych wyjściowych:

```console
[
  {
    "disks": [
      {
        "encryptionSettings": null,
        "name": "myScaleSet_myScaleSet_0_disk2_3f39c2019b174218b98b3dfae3424e69",
        "statuses": [
          {
            "additionalProperties": {},
            "code": "EncryptionState/encrypted",
            "displayStatus": "Encryption is enabled on disk",
            "level": "Info",
            "message": null,
            "time": null
          }
        ]
      }
    ],
    "id": "/subscriptions/guid/resourceGroups/MYRESOURCEGROUP/providers/Microsoft.Compute/virtualMachineScaleSets/myScaleSet/virtualMachines/0",
    "resourceGroup": "MYRESOURCEGROUP"
  }
]
```

## <a name="disable-encryption"></a>Wyłączanie szyfrowania

Jeśli nie chcesz już używać zaszyfrowanych dysków wystąpień maszyn wirtualnych, możesz wyłączyć szyfrowanie za pomocą opcji [az vmss encryption disable](/cli/azure/vmss/encryption#az_vmss_encryption_disable) w następujący sposób:

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>Następne kroki

- W tym artykule zaszyfrowanie zestawu skalowania maszyn wirtualnych za pomocą interfejsu wiersza polecenia platformy Azure. Można również użyć [szablonów Azure PowerShell](disk-encryption-powershell.md) lub [Azure Resource Manager szablonów.](disk-encryption-azure-resource-manager.md)
- Jeśli chcesz, aby Azure Disk Encryption po aprowieniu innego rozszerzenia, możesz użyć [sekwencjonowania rozszerzeń](virtual-machine-scale-sets-extension-sequencing.md). 
- Przykładowy plik wsadowy typu end-to-end dla szyfrowania dysków danych zestawu skalowania systemu Linux można [znaleźć tutaj.](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat) W tym przykładzie grupę zasobów , zestaw skalowania systemu Linux, zainstaluje dysk danych o pojemności 5 GB i zaszyfruje zestaw skalowania maszyn wirtualnych.
