---
title: Konfigurowanie usługi Azure Key Vault dla maszyn wirtualnych z systemem Linux
description: Jak skonfigurować magazyn kluczy do użytku z maszyną wirtualną usługi Azure Resource Manager za pomocą interfejsu wiersza polecenia platformy Azure.
author: mimckitt
manager: vashan
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/24/2017
ms.author: mimckitt
ms.openlocfilehash: 9ae486ee522982b116af58cfb7cbfbca66a7ef4a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458747"
---
# <a name="how-to-set-up-key-vault-for-virtual-machines-with-the-azure-cli"></a>Jak skonfigurować magazyn kluczy dla maszyn wirtualnych za pomocą interfejsu wiersza polecenia platformy Azure

W stosie Usługi Azure Resource Manager wpisy tajne/certyfikaty są modelowane jako zasoby dostarczane przez usługę Key Vault. Aby dowiedzieć się więcej o usłudze Azure Key Vault, zobacz [Co to jest usługa Azure Key Vault?](../../key-vault/general/overview.md) Aby usługa Key Vault była używana z maszynami wirtualnymi usługi Azure Resource Manager, właściwość *EnabledForDeployment* w magazynie kluczy musi być ustawiona na true. W tym artykule pokazano, jak skonfigurować magazyn kluczy do użytku z maszynami wirtualnymi platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure. 

Aby wykonać te kroki, potrzebujesz najnowszego interfejsu [wiersza polecenia platformy Azure](/cli/azure/install-az-cli2) zainstalowanego i zalogowanego do konta platformy Azure przy użyciu [logowania az.](/cli/azure/reference-index)

## <a name="create-a-key-vault"></a>Tworzenie magazynu kluczy
Utwórz magazyn kluczy i przypisz zasady wdrażania za pomocą [programu az keyvault create](/cli/azure/keyvault). Poniższy przykład tworzy magazyn `myKeyVault` kluczy o nazwie w grupie `myResourceGroup` zasobów:

```azurecli
az keyvault create -l westus -n myKeyVault -g myResourceGroup --enabled-for-deployment true
```

## <a name="update-a-key-vault-for-use-with-vms"></a>Aktualizowanie magazynu kluczy do użytku z maszynami wirtualnymi
Ustaw zasady wdrażania w istniejącym magazynie kluczy z [aktualizacją az keyvault](/cli/azure/keyvault). Następujące aktualizacje magazynu `myKeyVault` kluczy `myResourceGroup` o nazwie w grupie zasobów:

```azurecli
az keyvault update -n myKeyVault -g myResourceGroup --set properties.enabledForDeployment=true
```

## <a name="use-templates-to-set-up-key-vault"></a>Konfigurowanie usługi Key Vault za pomocą szablonów
Korzystając z szablonu, należy ustawić `enabledForDeployment` właściwość `true` dla zasobu Usługi Key Vault w następujący sposób:

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
Aby uzyskać inne opcje, które można skonfigurować podczas tworzenia magazynu kluczy przy użyciu szablonów, zobacz [Tworzenie magazynu kluczy](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
