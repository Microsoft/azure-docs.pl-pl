---
title: Konfigurowanie Azure Key Vault przy użyciu interfejsu wiersza polecenia
description: Jak skonfigurować Key Vault dla maszyny wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure.
author: mimckitt
ms.service: virtual-machines
ms.subservice: security
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/24/2017
ms.author: mimckitt
ms.custom: devx-track-azurecli
ms.openlocfilehash: 84a88c9c563141d6e6e9ba1584d577addcade8fe
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102565227"
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli"></a>Jak skonfigurować Key Vault dla maszyn wirtualnych przy użyciu interfejsu wiersza polecenia platformy Azure

W stosie Azure Resource Manager klucze tajne/certyfikaty są modelowane jako zasoby dostarczone przez Key Vault. Aby dowiedzieć się więcej na temat Azure Key Vault, zobacz [co to jest Azure Key Vault?](../../key-vault/general/overview.md) Aby można było używać Key Vault z maszynami wirtualnymi Azure Resource Manager, właściwość *EnabledForDeployment* w Key Vault musi mieć wartość true. W tym artykule opisano sposób konfigurowania Key Vault do użycia z maszynami wirtualnymi platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure. 

Aby wykonać te kroki, należy zainstalować najnowszy [interfejs wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) i zalogować się na konto platformy Azure za pomocą polecenia [AZ login](/cli/azure/reference-index).

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy
Utwórz magazyn kluczy i przypisz zasady wdrażania za pomocą [AZ Key magazynu Create](/cli/azure/keyvault). Poniższy przykład tworzy magazyn kluczy o nazwie `myKeyVault` w `myResourceGroup` grupie zasobów:

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Aktualizowanie Key Vault do użycia z maszynami wirtualnymi
Ustaw zasady wdrażania dla istniejącego magazynu kluczy za pomocą opcji [AZ Key magazynu Update](/cli/azure/keyvault). Następujące aktualizuje Magazyn kluczy o nazwie `myKeyVault` w `myResourceGroup` grupie zasobów:

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>Użyj szablonów, aby skonfigurować Key Vault
Korzystając z szablonu, należy ustawić `enabledForDeployment` Właściwość na `true` dla zasobu Key Vault w następujący sposób:

```json
{
    "type": "Microsoft.KeyVault/vaults",
    "name": "ContosoKeyVault",
    "apiVersion": "2015-06-01",
    "location": "<location-of-key-vault>",
    "properties": {
    "enabledForDeployment": "true",
    ....
    ....
    }
}
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać inne opcje, które można skonfigurować podczas tworzenia Key Vault przy użyciu szablonów, zobacz [Tworzenie magazynu kluczy](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
