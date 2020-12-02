---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/15/2020
ms.author: alkohli
ms.openlocfilehash: 354811b1db31731913f2a99287b0e4a71b2aff61
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96468485"
---
Przed rozpoczęciem upewnij się, że:

* Twoja subskrypcja platformy Microsoft Azure obsługuje zasób rozwiązania Azure Stack Edge. Upewnij się, że użyto obsługiwanej subskrypcji, takiej jak [Microsoft Umowa Enterprise (EA)](https://azure.microsoft.com/overview/sales-number/), [dostawca rozwiązań w chmurze (CSP)](https://docs.microsoft.com/partner-center/azure-plan-lp)lub [dostęp sponsorowany Microsoft Azure](https://azure.microsoft.com/offers/ms-azr-0036p/).
* Masz uprawnienia właściciela lub współautora na poziomie grupy zasobów dla bramy Azure Stack Edge/Azure Storage Gateway, IoT Hub i zasobów usługi Azure Storage.

  * Aby utworzyć dowolny zasób Azure Stack Edge, należy mieć uprawnienia jako współautora (lub wyższe) w zakresie na poziomie grupy zasobów. Należy również upewnić się, że `Microsoft.DataBoxEdge` dostawca jest zarejestrowany. Aby uzyskać informacje na temat rejestrowania, przejdź do sekcji [Rejestrowanie dostawcy zasobów](../articles/databox-online/azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Aby utworzyć dowolny zasób IoT Hub, upewnij się, że jest zarejestrowany dostawca Microsoft. Devices. Aby uzyskać informacje na temat rejestrowania, przejdź do sekcji [Rejestrowanie dostawcy zasobów](../articles/databox-online/azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#register-resource-providers).
  * Aby utworzyć zasób konta magazynu, należy ponownie uzyskać wartość współautor lub wyższy dostęp do zakresu na poziomie grupy zasobów. Usługa Azure Storage jest domyślnie zarejestrowanym dostawcą zasobów.
* Masz uprawnienia administratora lub użytkownika do Azure Active Directory interfejs API programu Graph. Aby uzyskać więcej informacji, zobacz [Azure Active Directory interfejs API programu Graph](https://docs.microsoft.com/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#default-access-for-administrators-users-and-guest-users-).
* Masz konto magazynu platformy Microsoft Azure z poświadczeniami dostępu.

