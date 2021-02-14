---
title: Nawiązywanie połączenia z bezpiecznym kontem magazynu z poziomu obszaru roboczego usługi Azure Synapse
description: W tym artykule omówiono sposób nawiązywania połączenia z bezpiecznym kontem magazynu z poziomu obszaru roboczego usługi Azure Synapse
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 02/10/2021
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 599cf17e1ab2b85aac77893e8b2d520d412e1cea
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100417788"
---
# <a name="connect-to-a-secure-azure-storage-account-from-your-synapse-workspace"></a>Nawiązywanie połączenia z bezpiecznym kontem usługi Azure Storage z obszaru roboczego Synapse

W tym artykule omówiono sposób nawiązywania połączenia z bezpiecznym kontem usługi Azure Storage z poziomu obszaru roboczego usługi Azure Synapse. Konto usługi Azure Storage możesz połączyć z obszarem roboczym Synapse podczas tworzenia obszaru roboczego. Po utworzeniu obszaru roboczego można połączyć więcej kont magazynu.


## <a name="secured-azure-storage-accounts"></a>Zabezpieczone konta usługi Azure Storage
Usługa Azure Storage zapewnia warstwowy model zabezpieczeń, który umożliwia Zabezpieczanie i kontrolowanie dostępu do kont magazynu. Reguły zapory IP można skonfigurować w taki sposób, aby ruch z wybranych zakresów publicznych adresów IP miał dostęp do konta magazynu. Możesz również skonfigurować reguły sieciowe, aby przyznać ruch z wybranych sieci wirtualnych do konta magazynu. Można łączyć reguły zapory IP zezwalające na dostęp z wybranych zakresów adresów IP i reguł sieci, które zapewniają dostęp z wybranych sieci wirtualnych na tym samym koncie magazynu. Te reguły dotyczą publicznego punktu końcowego konta magazynu. Nie są potrzebne żadne reguły dostępu, aby zezwalać na ruch z zarządzanych prywatnych punktów końcowych utworzonych w obszarze roboczym na konto magazynu. Reguły zapory magazynu mogą być stosowane do istniejących kont magazynu lub do nowych kont magazynu podczas ich tworzenia. Więcej informacji na temat zabezpieczania konta magazynu można znaleźć [tutaj](https://docs.microsoft.com/azure/storage/common/storage-network-security).

## <a name="synapse-workspaces-and-virtual-networks"></a>Obszary robocze Synapse i sieci wirtualne
Podczas tworzenia obszaru roboczego Synapse można włączyć do niego skojarzenie zarządzanej sieci wirtualnej. Jeśli nie włączysz zarządzanej sieci wirtualnej dla obszaru roboczego podczas tworzenia, obszar roboczy znajduje się w udostępnionej sieci wirtualnej wraz z innymi obszarami roboczymi Synapse, do których nie jest skojarzona zarządzana Sieć wirtualna. Jeśli zarządzana Sieć wirtualna została włączona podczas tworzenia obszaru roboczego, obszar roboczy jest skojarzony z dedykowaną siecią wirtualną zarządzaną przez usługę Azure Synapse. Te sieci wirtualne nie są tworzone w ramach subskrypcji klienta. W związku z tym nie będzie można przydzielić ruchu z tych sieci wirtualnych do bezpiecznego konta magazynu przy użyciu opisanych powyżej reguł sieci.  

## <a name="access-a-secured-storage-account"></a>Dostęp do zabezpieczonego konta magazynu
Synapse działa z sieci, które nie mogą zostać uwzględnione w regułach sieci. Aby umożliwić dostęp z obszaru roboczego do konta bezpiecznego magazynu, należy wykonać następujące czynności.

* Utwórz obszar roboczy usługi Azure Synapse z skojarzoną z nią zarządzaną siecią wirtualną i Utwórz zarządzane prywatne punkty końcowe z tego konta na koncie bezpiecznego magazynu
* Przyznaj usłudze Azure Synapse obszar roboczy dostęp do swojego konta bezpiecznego magazynu jako zaufanej usługi platformy Azure. Usługa Azure Synapse będzie używać silnego uwierzytelniania do bezpiecznego łączenia się z kontem magazynu.   

### <a name="create-a-synapse-workspace-with-a-managed-virtual-network-and-create-managed-private-endpoints-to-your-storage-account"></a>Utwórz obszar roboczy Synapse z zarządzaną siecią wirtualną i Utwórz zarządzane prywatne punkty końcowe na koncie magazynu
Możesz wykonać następujące [kroki](./synapse-workspace-managed-vnet.md) , aby utworzyć obszar roboczy Synapse z skojarzoną z nim zarządzaną siecią wirtualną. Po utworzeniu obszaru roboczego z skojarzoną zarządzaną siecią wirtualną można utworzyć zarządzany prywatny punkt końcowy na koncie bezpiecznego magazynu, wykonując czynności opisane w [tym miejscu](./how-to-create-managed-private-endpoints.md). 

### <a name="grant-your-azure-synapse-workspace-access-to-your-secure-storage-account-as-a-trusted-azure-service"></a>Przyznawanie obszaru roboczego usługi Azure Synapse dostęp do konta bezpiecznego magazynu jako zaufanej usługi platformy Azure
Funkcje analityczne, takie jak dedykowana Pula SQL i bezserwerowa Pula SQL, używają infrastruktury z wieloma dzierżawcami, która nie jest wdrażana w zarządzanej sieci wirtualnej. Aby zapewnić dostęp z tych funkcji do bezpiecznego konta magazynu, należy skonfigurować dostęp do konta magazynu na podstawie tożsamości zarządzanej przypisanej do systemu obszaru roboczego, wykonując poniższe kroki.

W Azure Portal przejdź do bezpiecznego konta magazynu. Wybierz pozycję **Sieć** z okienka nawigacji po lewej stronie. W sekcji **wystąpienia zasobów** wybierz pozycję *Microsoft. Synapse/Workspaces* jako **Typ zasobu** , a następnie wprowadź nazwę obszaru roboczego dla **nazwy wystąpienia**. Wybierz pozycję **Zapisz**.

![Konfiguracja sieci konta magazynu.](./media/connect-to-a-secure-storage-account/secured-storage-access.png)

Teraz powinno być możliwe uzyskanie dostępu do bezpiecznego konta magazynu z obszaru roboczego.


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [sieci wirtualnej zarządzanego obszaru roboczego](./synapse-workspace-managed-vnet.md).

Dowiedz się więcej o [zarządzanych prywatnych punktach końcowych](./synapse-workspace-managed-private-endpoints.md).
