---
title: Konfigurowanie magazynu kluczy dla maszyn wirtualnych z systemem Windows w usłudze Azure Resource Manager
description: Jak skonfigurować magazyn kluczy do użytku z maszyną wirtualną usługi Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: mimckitt
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 33a483e2-cfbc-4c62-a588-5d9fd52491e2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 01/24/2017
ms.author: mimckitt
ms.openlocfilehash: 098d4df3938f591cb72b3bfd59b7a5549469785c
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81451741"
---
# <a name="set-up-key-vault-for-virtual-machines-in-azure-resource-manager"></a>Konfigurowanie magazynu kluczy dla maszyn wirtualnych w usłudze Azure Resource Manager

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-rm-include.md)]

W stosie usługi Azure Resource Manager wpisy tajne/certyfikaty są modelowane jako zasoby dostarczane przez dostawcę zasobów usługi Key Vault. Aby dowiedzieć się więcej o usłudze Key Vault, zobacz [Co to jest usługa Azure Key Vault?](../../key-vault/general/overview.md)

> [!NOTE]
> 1. Aby usługa Key Vault była używana z maszynami wirtualnymi usługi Azure Resource Manager, właściwość **EnabledForDeployment** w magazynie kluczy musi być ustawiona na true. Można to zrobić w różnych klientów.
> 2. Przechowalnia kluczy musi zostać utworzona w tej samej subskrypcji i lokalizacji co maszyna wirtualna.
>
>

## <a name="use-powershell-to-set-up-key-vault"></a>Konfigurowanie usługi Key Vault za pomocą programu PowerShell
Aby utworzyć magazyn kluczy przy użyciu programu PowerShell, zobacz [Ustawianie i pobieranie klucza tajnego z usługi Azure Key Vault przy użyciu programu PowerShell](../../key-vault/secrets/quick-create-powershell.md).

W przypadku nowych magazynów kluczy można użyć tego polecenia cmdlet programu PowerShell:

    New-AzKeyVault -VaultName 'ContosoKeyVault' -ResourceGroupName 'ContosoResourceGroup' -Location 'East Asia' -EnabledForDeployment

W przypadku istniejących magazynów kluczy można użyć tego polecenia cmdlet programu PowerShell:

    Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

## <a name="use-cli-to-set-up-key-vault"></a>Konfigurowanie usługi Key Vault za pomocą interfejsu wiersza polecenia
Aby utworzyć magazyn kluczy przy użyciu interfejsu wiersza polecenia (CLI), zobacz [Zarządzanie magazynem kluczy przy użyciu interfejsu wiersza polecenia](../../key-vault/general/manage-with-cli2.md#create-a-key-vault).

W przypadku interfejsu wiersza polecenia należy utworzyć magazyn kluczy przed przypisaniem zasad wdrażania. Możesz to zrobić za pomocą następującego polecenia:

    az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "EastAsia"
    
Następnie, aby włączyć usługę Key Vault do użycia z wdrożeniem szablonu, uruchom następujące polecenie:

    az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"

## <a name="use-templates-to-set-up-key-vault"></a>Konfigurowanie usługi Key Vault za pomocą szablonów
Podczas korzystania z szablonu, należy `enabledForDeployment` ustawić `true` właściwość dla zasobu usługi Key Vault.

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

Aby uzyskać inne opcje, które można skonfigurować podczas tworzenia magazynu kluczy przy użyciu szablonów, zobacz [Tworzenie magazynu kluczy](https://azure.microsoft.com/documentation/templates/101-key-vault-create/).
