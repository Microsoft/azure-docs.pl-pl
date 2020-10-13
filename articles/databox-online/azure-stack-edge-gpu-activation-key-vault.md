---
title: Integracja Azure Key Vault z usługami Azure Stack Edge i aktywowanie urządzenia
description: Opisuje sposób, w jaki Azure Key Vault jest skojarzony z zarządzaniem kluczami tajnymi podczas aktywacji urządzenia Azure Stack brzeg Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: alkohli
ms.openlocfilehash: c841c96326f636e16f3b4f86fcb88a0962011c0f
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91976835"
---
# <a name="azure-key-vault-integration-with-azure-stack-edge"></a>Integracja Azure Key Vault z Azure Stack Edge 

Azure Key Vault jest zintegrowana z usługą Azure Stack Edge do zarządzania tajnymi zasobami. W tym artykule przedstawiono szczegółowe informacje na temat sposobu tworzenia Azure Key Vault Azure Stack zasobów brzegowych podczas aktywacji urządzenia, a następnie używany do zarządzania wpisami tajnymi. 


## <a name="about-key-vault-and-azure-stack-edge"></a>Informacje o magazynie kluczy i Azure Stack Edge

Usługa Azure Key Vault w chmurze służy do bezpiecznego przechowywania i kontrolowania dostępu do tokenów, haseł, certyfikatów, kluczy interfejsu API i innych wpisów tajnych. Key Vault ułatwia również tworzenie i kontrolowanie kluczy szyfrowania używanych do szyfrowania danych. 

W przypadku usługi Azure Stack Edge jeden z użytych kluczy tajnych jest kluczem integralności kanału (CIK). Ten klucz umożliwia zaszyfrowanie wpisów tajnych. W przypadku integracji magazynu kluczy CIK jest bezpiecznie przechowywane w magazynie kluczy. Aby uzyskać więcej informacji, zobacz [bezpieczne przechowywanie wpisów tajnych i kluczy](../key-vault/general/overview.md#securely-store-secrets-and-keys).


## <a name="key-vault-creation"></a>Tworzenie magazynu kluczy

Magazyn kluczy jest tworzony dla zasobu brzegowego Azure Stack podczas procesu generowania klucza aktywacji. 

- Podczas tworzenia zasobu Azure Stack Edge należy zarejestrować dostawcę zasobów *Microsoft.* kluczy. Dostawca zasobów jest automatycznie rejestrowany, jeśli masz uprawnienia właściciela lub współautora do subskrypcji. Magazyn kluczy jest tworzony w tej samej subskrypcji i grupie zasobów, co zasób Azure Stack Edge. 

- Podczas tworzenia zasobu Azure Stack Edge tworzony jest również tożsamość usługi zarządzanej (MSI), który będzie trwał okres istnienia zasobu i komunikuje się z dostawcą zasobów w chmurze. 

    Gdy plik MSI jest włączony, platforma Azure tworzy zaufaną tożsamość dla zasobu Azure Stack Edge.

- Po utworzeniu zasobu brzegowego Azure Stack i wygenerowaniu klucza aktywacji z poziomu Azure Portal zostanie utworzony magazyn kluczy. Ten magazyn kluczy jest używany na potrzeby zarządzania kluczami tajnymi i utrzymuje się w przypadku, gdy istnieje zasób Azure Stack Edge. 

    ![Key Vault utworzone podczas generowania klucza aktywacji](media/azure-stack-edge-gpu-deploy-prep/azure-stack-edge-resource-3.png)

- Możesz zaakceptować domyślną nazwę klucza lub określić niestandardową nazwę magazynu kluczy. Nazwa magazynu kluczy musi mieć długość od 3 do 24 znaków. Nie można użyć magazynu kluczy, który jest już używany. <!--The MSI is then used to authenticate to key vault to retrieve secrets.--> 

    ![Plik MSI utworzony podczas tworzenia zasobów Azure Stack Edge](media/azure-stack-edge-gpu-deploy-prep/create-resource-8.png)

- Aby zapobiec przypadkowemu usunięciu, w magazynie kluczy jest włączona blokada zasobów. W magazynie kluczy jest również włączona funkcja usuwania nietrwałego, która umożliwia przywrócenie magazynu kluczy w ciągu 90 dni w przypadku przypadkowego usunięcia. Aby uzyskać więcej informacji, zobacz [Azure Key Vault Omówienie usuwania nietrwałego](../key-vault/general/soft-delete-overview.md)

    Jeśli magazyn kluczy zostanie przypadkowo usunięty i nie upłynął okres trwania ochrony przed przeczyszczeniem 90 dni, wykonaj następujące kroki, aby [odzyskać Magazyn kluczy](../key-vault/general/soft-delete-powershell.md#recovering-a-key-vault). 

- Jeśli masz istniejący zasób Azure Stack Edge przed integracją Azure Key Vault z zasobem Azure Stack Edge, nie ma to żadnego oddziaływania. Możesz nadal korzystać z istniejącego zasobu Azure Stack Edge. 

- Po usunięciu zasobu brzegowego Azure Stack Azure Key Vault zostanie również usunięty z zasobem. Zostanie wyświetlony monit o potwierdzenie. Jeśli nie zamierzasz usunąć tego magazynu kluczy, możesz zrezygnować z podania zgody. Tylko zasób Azure Stack Edge zostanie usunięty, pozostawiając Magazyn kluczy nienaruszony. 

- Jeśli ten magazyn kluczy był używany do przechowywania innych kluczy, można nadal przywrócić go w ciągu 90 dni od usunięcia. Podczas tego okresu ochrony nie można utworzyć nowego magazynu kluczy przy użyciu nazwy magazynu kluczy.

W przypadku problemów związanych z magazynem kluczy i aktywacją urządzeń zobacz [Rozwiązywanie problemów dotyczących aktywacji urządzenia](azure-stack-edge-gpu-troubleshoot-activation.md).

<!--## Key vault secret management

When you generate an activation key, the following events occur:

1. You request an activation key in the Azure portal. The request is then sent to Key Vault resource provider. 
1. A standard tier key vault with access policy is created and is locked by default. This key vault uses the default name or the custom name that you specified.
1. The key vault authenticates with MSI the request to generate activation key. The MSI is also added to the key vault access policy and a channel integrity key is generated and placed in the key vault.
1. The activation key is returned to the Azure portal. You can then copy this key and use it in the local UI to activate your device.-->



## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o sposobach [generowania klucza aktywacji](azure-stack-edge-gpu-deploy-prep.md#get-the-activation-key).

