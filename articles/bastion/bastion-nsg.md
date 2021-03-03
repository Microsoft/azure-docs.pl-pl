---
title: Praca z maszynami wirtualnymi i sieciowych grup zabezpieczeń na platformie Azure bastionu
description: Za pomocą sieciowych grup zabezpieczeń można korzystać z usługi Azure bastionu. Dowiedz się więcej o podsieciach wymaganych do tej konfiguracji.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 12/09/2020
ms.author: cherylmc
ms.openlocfilehash: b6a0dee4c3fef1be4f4b9f910b4c6256b4924a2d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700222"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>Praca z usługami sieciowej grupy zabezpieczeń Access i Azure bastionu

Podczas pracy z usługą Azure bastionu można użyć sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń). Aby uzyskać więcej informacji, zobacz [grupy zabezpieczeń](../virtual-network/network-security-groups-overview.md).

:::image type="content" source="./media/bastion-nsg/figure-1.png" alt-text="Sieciowa grupa zabezpieczeń":::

Na tym diagramie:

* Host bastionu jest wdrażany w sieci wirtualnej.
* Użytkownik nawiązuje połączenie z Azure Portal przy użyciu dowolnej przeglądarki HTML5.
* Użytkownik przechodzi do maszyny wirtualnej platformy Azure do protokołu RDP/SSH.
* Połącz integrację — pojedyncze kliknięcie sesji RDP/SSH wewnątrz przeglądarki
* Na maszynie wirtualnej platformy Azure nie jest wymagany publiczny adres IP.

## <a name="network-security-groups"></a><a name="nsg"></a>Sieciowe grupy zabezpieczeń

W tej sekcji przedstawiono ruch sieciowy między użytkownikiem a usługą Azure bastionu oraz docelowymi maszynami wirtualnymi w sieci wirtualnej:

> [!IMPORTANT]
> Jeśli zdecydujesz się używać sieciowej grupy zabezpieczeń z zasobem platformy Azure bastionu, **musisz** utworzyć wszystkie poniższe reguły ruchu przychodzącego i wychodzącego. Pominięcie któregokolwiek z następujących reguł w programie sieciowej grupy zabezpieczeń spowoduje zablokowanie przez zasób usługi Azure bastionu otrzymywania niezbędnych aktualizacji w przyszłości i w związku z tym otwarcie zasobu do przyszłych luk w zabezpieczeniach.
> 

### <a name="azurebastionsubnet"></a><a name="apply"></a>AzureBastionSubnet

Usługa Azure bastionu jest wdrażana w odróżnieniu od ***AzureBastionSubnet***.

* **Ruch przychodzący:**

   * **Ruch przychodzący z publicznej sieci Internet:** Usługa Azure bastionu utworzy publiczny adres IP, który wymaga, aby port 443 był włączony w publicznym adresie IP dla ruchu przychodzącego. NIE trzeba otwierać portu 3389/22 w AzureBastionSubnet.
   * **Ruch przychodzący z płaszczyzny kontroli usługi Azure bastionu:** W przypadku łączności z płaszczyzną kontroli Włącz port 443 przychodzące z tagu usługi **bramy** . Dzięki temu płaszczyzna kontroli, czyli Menedżer bramy, może komunikować się z usługą Azure bastionu.
   * **Ruch przychodzący z płaszczyzny danych usługi Azure bastionu:** W przypadku komunikacji między podstawowymi składnikami usługi Azure bastionu należy włączyć porty 8080, 5701 przychodzące ze znacznika usługi **VirtualNetwork** do tagu usługi **VirtualNetwork** . Dzięki temu składniki platformy Azure bastionu mogą komunikować się ze sobą.
   * **Ruch przychodzący z Azure Load Balancer:** W przypadku sond kondycji Włącz port 443 dla ruchu przychodzącego z tagu usługi **AzureLoadBalancer** . Dzięki temu Azure Load Balancer wykryć łączność


   :::image type="content" source="./media/bastion-nsg/inbound.png" alt-text="Zrzut ekranu przedstawia reguły zabezpieczeń ruchu przychodzącego dla łączności z usługą Azure bastionu.":::

* **Ruch wychodzący:**

   * **Ruch wychodzący do docelowych maszyn wirtualnych:** Usługa Azure bastionu będzie docierać do docelowych maszyn wirtualnych za pośrednictwem prywatnego adresu IP. Sieciowych grup zabezpieczeń musi zezwalać na ruch przychodzący do innych docelowych podsieci maszyn wirtualnych dla portów 3389 i 22.
   * **Ruch wychodzący do płaszczyzny danych usługi Azure bastionu:** W przypadku komunikacji między podstawowymi składnikami usługi Azure bastionu należy włączyć porty 8080, 5701 wychodzące ze znacznika usługi **VirtualNetwork** do tagu usługi **VirtualNetwork** . Dzięki temu składniki platformy Azure bastionu mogą komunikować się ze sobą.
   * **Ruch przychodzący do innych publicznych punktów końcowych na platformie Azure:** Usługa Azure bastionu musi mieć możliwość łączenia się z różnymi publicznymi punktami końcowymi na platformie Azure (na przykład do przechowywania dzienników diagnostycznych i dzienników zliczania). Z tego powodu usługa Azure bastionu potrzebuje ruchu wychodzącego do 443 do **AzureCloud** Service Tag.
   * **Ruch wychodzący do Internetu:** Usługa Azure bastionu musi mieć możliwość komunikowania się z Internetem w celu weryfikacji sesji i certyfikatu. Z tego powodu zalecamy włączenie portu 80 wychodzącego do **Internetu.**


   :::image type="content" source="./media/bastion-nsg/outbound.png" alt-text="Zrzut ekranu przedstawia reguły zabezpieczeń ruchu wychodzącego dla łączności z usługą Azure bastionu.":::

### <a name="target-vm-subnet"></a>Docelowa podsieć maszyny wirtualnej
Jest to podsieć zawierająca docelową maszynę wirtualną, do której ma zostać zainstalowana protokół RDP/SSH.

   * **Ruch przychodzący z usługi Azure bastionu:** Usługa Azure bastionu będzie docierać do docelowej maszyny wirtualnej za pośrednictwem prywatnego adresu IP. Porty RDP/SSH (odpowiednio porty 3389/22) muszą być otwarte dla docelowej maszyny wirtualnej po stronie prywatnego adresu IP. Najlepszym rozwiązaniem jest dodanie w tej regule zakresu adresów IP podsieci usługi Azure bastionu, aby zezwolić tylko bastionu na otwieranie tych portów na docelowych maszynach wirtualnych w docelowej podsieci maszyn wirtualnych.


## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Azure bastionu, zobacz [często zadawane pytania](bastion-faq.md).
