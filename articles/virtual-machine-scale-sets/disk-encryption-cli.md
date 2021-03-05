---
title: Szyfrowanie dysków dla zestawów skalowania platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure
description: Dowiedz się, jak używać interfejsu wiersza polecenia platformy Azure do szyfrowania wystąpień maszyn wirtualnych i dołączonych dysków w zestawie skalowania maszyn wirtualnych z systemem Windows
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: disks
ms.date: 10/15/2019
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 302f53bd218a2e01a039be4780a0e2ff5974e7b4
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102215956"
---
# <a name="encrypt-os-and-attached-data-disks-in-a-virtual-machine-scale-set-with-the-azure-cli"></a>Szyfrowanie systemu operacyjnego i dołączonych dysków danych w zestawie skalowania maszyn wirtualnych za pomocą interfejsu wiersza polecenia platformy Azure

Interfejs wiersza polecenia platformy Azure umożliwia tworzenie zasobów Azure i zarządzanie nimi z poziomu wiersza polecenia lub skryptów. W tym przewodniku szybki start pokazano, jak utworzyć i zaszyfrować zestaw skalowania maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure. Aby uzyskać więcej informacji na temat stosowania szyfrowania dysków Azure do zestawu skalowania maszyn wirtualnych, zobacz [Azure Disk Encryption Virtual Machine Scale Sets](disk-encryption-overview.md).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga wersji 2.0.31 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="create-a-scale-set"></a>Tworzenie zestawu skalowania

Zanim będzie można utworzyć zestaw skalowania, utwórz grupę zasobów za pomocą polecenia [az group create](/cli/azure/group). Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w lokalizacji *Wschodnie* :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Teraz utwórz zestaw skalowania maszyn wirtualnych przy użyciu polecenia [az vmss create](/cli/azure/vmss). Poniższy przykład tworzy zestaw skalowania o nazwie *myScaleSet*, który jest skonfigurowany do automatycznej aktualizacji w miarę stosowania zmian, a następnie generuje klucze SSH, jeśli nie istnieją, w lokalizacji *~/.ssh/id_rsa*. Do każdego wystąpienia maszyny wirtualnej jest dołączony dysk z danymi o 32 GB danych, a [rozszerzenie niestandardowego skryptu](../virtual-machines/extensions/custom-script-linux.md) platformy Azure służy do przygotowywania dysków danych przy użyciu [opcji AZ VMSS Extension Set](/cli/azure/vmss/extension):

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

## <a name="create-an-azure-key-vault-enabled-for-disk-encryption"></a>Tworzenie magazynu kluczy platformy Azure z włączoną funkcją szyfrowania dysków

Azure Key Vault mogą przechowywać klucze, wpisy tajne lub hasła, które umożliwiają bezpieczne wdrażanie ich w aplikacjach i usługach. Klucze kryptograficzne są przechowywane w Azure Key Vault przy użyciu ochrony oprogramowania lub można importować lub generować klucze w sprzętowych modułach zabezpieczeń (sprzętowych modułów zabezpieczeń) z certyfikatem standardu FIPS 140-2 Level 2. Te klucze kryptograficzne są używane do szyfrowania i odszyfrowywania dysków wirtualnych dołączonych do maszyny wirtualnej. Zachowujesz kontrolę nad tymi kluczami kryptograficznymi i można przeprowadzić inspekcję ich użycia.

Zdefiniuj własne unikatowe *keyvault_name*. Następnie utwórz magazyn kluczy za pomocą [AZ kluczy Create](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-create) w tej samej subskrypcji i regionie, w której znajduje się zestaw skalowania, a następnie ustaw zasady dostępu *--Enabled-on-Encryption* .

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault create --resource-group myResourceGroup --name $keyvault_name --enabled-for-disk-encryption
```

### <a name="use-an-existing-key-vault"></a>Użyj istniejącego Key Vault

Ten krok jest wymagany tylko w przypadku istniejących Key Vault, które mają być używane z szyfrowaniem dysków. Pomiń ten krok, jeśli utworzono Key Vault w poprzedniej sekcji.

Zdefiniuj własne unikatowe *keyvault_name*. Następnie Zaktualizowano Magazyn kluczy za pomocą opcji [AZ Disk Update](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-update) i ustawienia zasad dostępu *--Enabled-for-Encryption* .

```azurecli-interactive
# Provide your own unique Key Vault name
keyvault_name=myuniquekeyvaultname

# Create Key Vault
az keyvault update --name $keyvault_name --enabled-for-disk-encryption
```

## <a name="enable-encryption"></a>Włącz szyfrowanie

Aby zaszyfrować wystąpienia maszyn wirtualnych w zestawie skalowania, najpierw Uzyskaj pewne informacje o IDENTYFIKATORze zasobu Key Vault przy użyciu [AZ klucza magazynu show](/cli/azure/ext/keyvault-preview/keyvault#ext-keyvault-preview-az-keyvault-show). Te zmienne są używane do uruchamiania procesu szyfrowania za pomocą [AZ VMSS Encryption Enable](/cli/azure/vmss/encryption#az-vmss-encryption-enable):

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

Rozpoczęcie procesu szyfrowania może potrwać minutę lub dwie.

Ponieważ zestaw skalowania jest ustawiony na zasady uaktualniania zestawu skalowania utworzonego w ramach wcześniejszego kroku, jest ustawiany na *automatyczny*, wystąpienia maszyn wirtualnych automatycznie uruchamiają proces szyfrowania. W przypadku zestawów skalowania, w których zasady uaktualniania są ręczne, należy uruchomić zasady szyfrowania dla wystąpień maszyn wirtualnych za pomocą [AZ VMSS Update-Instances](/cli/azure/vmss#az-vmss-update-instances).

### <a name="enable-encryption-using-kek-to-wrap-the-key"></a>Włącz szyfrowanie przy użyciu KEK, aby zawijać klucz

Klucz szyfrowania klucza można także użyć, aby zwiększyć bezpieczeństwo podczas szyfrowania zestawu skalowania maszyn wirtualnych.

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
>  Składnia wartości parametru dysk-szyfrowanie-kluczy jest pełnym ciągiem identyfikatora:</br>
/subscriptions/[Identyfikator subskrypcji — identyfikator GUID]/resourceGroups/[Resource-Group-Name]/providers/Microsoft.KeyVault/vaults/[nazwa magazynu kluczy]</br></br>
> Składnia wartości parametru klucz-szyfrowanie-klucz jest pełnym identyfikatorem URI KEK w programie:</br>
https://[nazwa magazynu kluczy]. magazyn. Azure. NET/Keys/[kekname]/[KEK-Unique-ID]

## <a name="check-encryption-progress"></a>Sprawdź postęp szyfrowania

Aby sprawdzić stan szyfrowania dysku, użyj [AZ VMSS Encryption show](/cli/azure/vmss/encryption#az-vmss-encryption-show):

```azurecli-interactive
az vmss encryption show --resource-group myResourceGroup --name myScaleSet
```

Gdy wystąpienia maszyn wirtualnych są szyfrowane, kod stanu raportuje *EncryptionState/szyfrowany*, jak pokazano w następujących przykładowych danych wyjściowych:

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

## <a name="disable-encryption"></a>Wyłącz szyfrowanie

Jeśli nie chcesz już używać szyfrowanych wystąpień maszyn wirtualnych, możesz wyłączyć szyfrowanie za pomocą [AZ VMSS Encryption Disable](/cli/azure/vmss/encryption#az-vmss-encryption-disable) w następujący sposób:

```azurecli-interactive
az vmss encryption disable --resource-group myResourceGroup --name myScaleSet
```

## <a name="next-steps"></a>Następne kroki

- W tym artykule użyto interfejsu wiersza polecenia platformy Azure w celu zaszyfrowania zestawu skalowania maszyn wirtualnych. Możesz również użyć szablonów [Azure PowerShell](disk-encryption-powershell.md) lub [Azure Resource Manager](disk-encryption-azure-resource-manager.md).
- Jeśli chcesz, aby Azure Disk Encryption zastosowały się po zainicjowaniu innego rozszerzenia, możesz użyć [sekwencjonowania rozszerzeń](virtual-machine-scale-sets-extension-sequencing.md). 
- Przykład kompleksowego pliku wsadowego dla szyfrowania dysku danych zestawu skalowania systemu Linux można znaleźć [tutaj](https://gist.githubusercontent.com/ejarvi/7766dad1475d5f7078544ffbb449f29b/raw/03e5d990b798f62cf188706221ba6c0c7c2efb3f/enable-linux-vmss.bat). Ten przykład tworzy grupę zasobów, zestaw skalowania systemu Linux, instaluje dysk danych 5 GB i szyfruje zestaw skalowania maszyn wirtualnych.
