---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 01/26/2021
ms.author: danlep
ms.openlocfilehash: a9dbedd8516f3a3a592c7fd4f4f5563011d6c6db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99491019"
---
#### <a name="requirements-for-key-vault-firewall"></a>Wymagania dotyczące zapory Key Vault

Jeśli w magazynie kluczy jest włączona [zapora Key Vault](../articles/key-vault/general/network-security.md) , poniżej przedstawiono dodatkowe wymagania:

* Aby uzyskać dostęp do magazynu kluczy, należy użyć tożsamości zarządzanej **przypisanej przez system** API Management.
* W zaporze Key Vault Włącz opcję **Zezwalaj na ominięcie tej zapory dla zaufanych usług firmy Microsoft** .

#### <a name="virtual-network-requirements"></a>Wymagania dotyczące sieci wirtualnej

Jeśli wystąpienie API Management zostało wdrożone w sieci wirtualnej, należy również skonfigurować następujące ustawienia sieciowe:

* Włącz [punkt końcowy usługi](../articles/key-vault/general/overview-vnet-service-endpoints.md) do Azure Key Vault w podsieci API Management.
* Skonfiguruj regułę sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń), aby zezwalać na ruch wychodzący do [tagów usługi](../articles/virtual-network/service-tags-overview.md)AzureKeyVault i usługi azureactivedirectory. 

Aby uzyskać szczegółowe informacje, zobacz szczegóły konfiguracji sieci w artykule [nawiązywanie połączenia z siecią wirtualną](../articles/api-management/api-management-using-with-vnet.md#-common-network-configuration-issues).