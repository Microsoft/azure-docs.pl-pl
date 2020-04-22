---
title: Praca z maszynami wirtualnymi i sieciami sieciowymi w usłudze Azure Bastion
description: W tym artykule opisano sposób włączania dostępu do nsg za pomocą usługi Azure Bastion
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: charwen
ms.openlocfilehash: 0188f9bc1c7c0e8d7fed9f590d078085b175614f
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732195"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>Praca z dostępem do nsg i bastionem platformy Azure

Podczas pracy z bastionem platformy Azure można używać sieciowych grup zabezpieczeń (NSG). Aby uzyskać więcej informacji, zobacz [Grupy zabezpieczeń](../virtual-network/security-overview.md). 

![Architektura](./media/bastion-nsg/nsg-architecture.png)

Na tym diagramie:

* Host bastionu jest wdrażany w sieci wirtualnej.
* Użytkownik łączy się z portalem Azure przy użyciu dowolnej przeglądarki HTML5.
* Użytkownik przechodzi do maszyny wirtualnej platformy Azure do RDP/SSH.
* Integracja connect - sesja RDP/SSH jednym kliknięciem wewnątrz przeglądarki
* Nie jest wymagany publiczny adres IP na maszynie Wirtualnej platformy Azure.

## <a name="network-security-groups"></a><a name="nsg"></a>Grupy zabezpieczeń sieci

W tej sekcji przedstawiono ruch sieciowy między użytkownikiem a bastionem platformy Azure, a następnie do docelowych maszyn wirtualnych w sieci wirtualnej:

### <a name="azurebastionsubnet"></a><a name="apply"></a>Usługa AzureBastionSubnet

Usługa Azure Bastion jest wdrażana specjalnie w ***usłudze AzureBastionSubnet.***

* **Ruch przychodzący:**

   * **Ruch przychodzący z publicznego internetu:** Bastion platformy Azure utworzy publiczny adres IP, który wymaga włączonego portu 443 w publicznym adresie IP dla ruchu przychodzącego. Port 3389/22 NIE muszą być otwierane w sieci AzureBastionSubnet.
   * **Ruch przychodzący z płaszczyzny sterowania bastionu platformy Azure:** Aby uzyskać łączność z płaszczyzną sterowania, włącz przychodzące porty 443 z tagu usługi **GatewayManager.** Dzięki temu płaszczyzny sterowania, czyli Gateway Manager, aby móc rozmawiać z usługi Azure Bastion.

* **Ruch wychodzący:**

   * **Ruch wychodzący do docelowych maszyn wirtualnych:** Usługa Azure Bastion osiągnie docelowe maszyny wirtualne za pomocą prywatnego adresu IP. Sieciowe grupy zabezpieczeń muszą zezwalać na ruch wychodzący do innych docelowych podsieci maszyn wirtualnych dla portów 3389 i 22.
   * **Ruch wychodzący do innych publicznych punktów końcowych na platformie Azure:** Usługa Azure Bastion musi mieć możliwość łączenia się z różnymi publicznymi punktami końcowymi na platformie Azure (na przykład do przechowywania dzienników diagnostycznych i dzienników pomiarów). Z tego powodu usługa Bastion platformy Azure musi odlatywać do tagu usługi Usługi Usługi 443 do **usługi AzureCloud.**

### <a name="target-vm-subnet"></a>Podsieć docelowej maszyny Wirtualnej
Jest to podsieć, która zawiera docelową maszynę wirtualną, do której chcesz rdp/SSH.

   * **Ruch przychodzący z bastionu platformy Azure:** Usługa Azure Bastion dotrze do docelowej maszyny Wirtualnej za pomocą prywatnego adresu IP. Porty RDP/SSH (odpowiednio porty 3389/22) muszą być otwierane po docelowej stronie maszyny Wirtualnej nad prywatnym adresem IP. Najlepszym rozwiązaniem jest dodanie zakresu adresów IP podsieci Bastion platformy Azure w tej regule, aby umożliwić tylko bastionowi otwieranie tych portów na docelowych maszynach wirtualnych w docelowej podsieci maszyny wirtualnej.


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Azure Bastion, zobacz często zadawane [pytania](bastion-faq.md).
