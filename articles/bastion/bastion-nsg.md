---
title: Praca z maszynami wirtualnymi i sieciowych grup zabezpieczeń na platformie Azure bastionu
description: Za pomocą sieciowych grup zabezpieczeń można korzystać z usługi Azure bastionu. Dowiedz się więcej o podsieciach wymaganych do tej konfiguracji.
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 07/07/2020
ms.author: charwen
ms.openlocfilehash: a69aa8d8a6dc324d6fe28219316c36ac2ec816a5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90987708"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>Praca z usługami sieciowej grupy zabezpieczeń Access i Azure bastionu

Podczas pracy z usługą Azure bastionu można użyć sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń). Aby uzyskać więcej informacji, zobacz [grupy zabezpieczeń](../virtual-network/security-overview.md).

:::image type="content" source="./media/bastion-nsg/figure-1.png" alt-text="Sieciowa grupa zabezpieczeń":::

Na tym diagramie:

* Host bastionu jest wdrażany w sieci wirtualnej.
* Użytkownik nawiązuje połączenie z Azure Portal przy użyciu dowolnej przeglądarki HTML5.
* Użytkownik przechodzi do maszyny wirtualnej platformy Azure do protokołu RDP/SSH.
* Połącz integrację — pojedyncze kliknięcie sesji RDP/SSH wewnątrz przeglądarki
* Na maszynie wirtualnej platformy Azure nie jest wymagany publiczny adres IP.

## <a name="network-security-groups"></a><a name="nsg"></a>Grupy zabezpieczeń sieci

W tej sekcji przedstawiono ruch sieciowy między użytkownikiem a usługą Azure bastionu oraz docelowymi maszynami wirtualnymi w sieci wirtualnej:

### <a name="azurebastionsubnet"></a><a name="apply"></a>AzureBastionSubnet

Usługa Azure bastionu jest wdrażana w odróżnieniu od ***AzureBastionSubnet***.

* **Ruch przychodzący:**

   * **Ruch przychodzący z publicznej sieci Internet:** Usługa Azure bastionu utworzy publiczny adres IP, który wymaga, aby port 443 był włączony w publicznym adresie IP dla ruchu przychodzącego. NIE trzeba otwierać portu 3389/22 w AzureBastionSubnet.
   * **Ruch przychodzący z płaszczyzny kontroli usługi Azure bastionu:** W przypadku łączności z płaszczyzną kontroli Włącz port 443 przychodzące z tagu usługi **bramy** . Dzięki temu płaszczyzna kontroli, czyli Menedżer bramy, może komunikować się z usługą Azure bastionu.


   :::image type="content" source="./media/bastion-nsg/inbound.png" alt-text="Sieciowa grupa zabezpieczeń":::

* **Ruch wychodzący:**

   * **Ruch wychodzący do docelowych maszyn wirtualnych:** Usługa Azure bastionu będzie docierać do docelowych maszyn wirtualnych za pośrednictwem prywatnego adresu IP. Sieciowych grup zabezpieczeń musi zezwalać na ruch przychodzący do innych docelowych podsieci maszyn wirtualnych dla portów 3389 i 22.
   * **Ruch przychodzący do innych publicznych punktów końcowych na platformie Azure:** Usługa Azure bastionu musi mieć możliwość łączenia się z różnymi publicznymi punktami końcowymi na platformie Azure (na przykład do przechowywania dzienników diagnostycznych i dzienników zliczania). Z tego powodu usługa Azure bastionu potrzebuje ruchu wychodzącego do 443 do **AzureCloud** Service Tag.


   :::image type="content" source="./media/bastion-nsg/outbound.png" alt-text="Sieciowa grupa zabezpieczeń":::

### <a name="target-vm-subnet"></a>Docelowa podsieć maszyny wirtualnej
Jest to podsieć zawierająca docelową maszynę wirtualną, do której ma zostać zainstalowana protokół RDP/SSH.

   * **Ruch przychodzący z usługi Azure bastionu:** Usługa Azure bastionu będzie docierać do docelowej maszyny wirtualnej za pośrednictwem prywatnego adresu IP. Porty RDP/SSH (odpowiednio porty 3389/22) muszą być otwarte dla docelowej maszyny wirtualnej po stronie prywatnego adresu IP. Najlepszym rozwiązaniem jest dodanie w tej regule zakresu adresów IP podsieci usługi Azure bastionu, aby zezwolić tylko bastionu na otwieranie tych portów na docelowych maszynach wirtualnych w docelowej podsieci maszyn wirtualnych.


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Azure bastionu, zobacz [często zadawane pytania](bastion-faq.md).
