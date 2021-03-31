---
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 11/09/2018
ms.author: genli
ms.openlocfilehash: 3df4108907a4e1e65a444faf1049163966b7accf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "67183153"
---
## <a name="scenario"></a>Scenariusz
Maszyna wirtualna z jedną kartą sieciową jest tworzona i połączona z siecią wirtualną. Maszyna wirtualna wymaga trzech różnych *prywatnych* adresów IP i dwóch *publicznych* adresów IP. Adresy IP są przypisywane do następujących konfiguracji adresów IP:

* **Ipconfig-1:** Przypisuje *statyczny* prywatny adres IP i *statyczny* publiczny adres IP.
* **Ipconfig-2:** Przypisuje *statyczny* prywatny adres IP i *statyczny* publiczny adres IP.
* **Ipconfig-3:** Przypisuje *statyczny* prywatny adres IP bez publicznego adresu IP.
  
    ![Wiele adresów IP](./media/virtual-network-multiple-ip-addresses-scenario/multiple-ipconfigs.png)

Konfiguracje adresów IP są skojarzone z kartą sieciową po utworzeniu karty sieciowej i dołączeniu karty sieciowej do maszyny wirtualnej podczas tworzenia maszyny wirtualnej. Typy adresów IP używane dla scenariusza są przeznaczone do ilustracji. Można przypisać wszelkie wymagane typy adresów IP i przypisań.

> [!NOTE]
> Chociaż kroki opisane w tym artykule przypisuje wszystkie konfiguracje IP do jednej karty sieciowej, można także przypisać wiele konfiguracji IP do dowolnej karty sieciowej na maszynie wirtualnej z wieloma KARTAmi sieciowymi. Aby dowiedzieć się, jak utworzyć maszynę wirtualną z wieloma kartami sieciowymi, przeczytaj artykuł [Tworzenie maszyny wirtualnej z wieloma](../articles/virtual-machines/windows/multiple-nics.md) kartami sieciowymi.