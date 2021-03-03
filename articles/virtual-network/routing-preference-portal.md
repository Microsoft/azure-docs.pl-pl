---
title: Skonfiguruj preferencję routingu dla publicznego adresu IP — Azure Portal
description: Dowiedz się, jak utworzyć publiczny adres IP z preferencją routingu ruchu internetowego
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2021
ms.author: mnayak
ms.openlocfilehash: f445eab65e8d2448e57bad19c52a4b72732016bb
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101672918"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-the-azure-portal"></a>Skonfiguruj preferencję routingu dla publicznego adresu IP przy użyciu Azure Portal

W tym artykule pokazano, jak skonfigurować [preferencję routingu](./routing-preference-overview.md) za pośrednictwem sieci usługodawcy internetowego (opcja **internetowa** ) dla publicznego adresu IP. Po utworzeniu publicznego adresu IP możesz skojarzyć go z poniższymi zasobami platformy Azure dla ruchu przychodzącego i wychodzącego do Internetu:

* Maszyna wirtualna
* Zestaw skalowania maszyn wirtualnych
* Azure Kubernetes Service (AKS)
* Moduł równoważenia obciążenia dostępny z Internetu
* Application Gateway
* Azure Firewall

Domyślnie preferencja routingu dla publicznego adresu IP jest ustawiana na sieć globalna firmy Microsoft dla wszystkich usług platformy Azure i może być skojarzona z dowolną usługą platformy Azure.

Jeśli nie masz subskrypcji platformy Azure, utwórz teraz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-public-ip-address-with-a-routing-preference"></a>Tworzenie publicznego adresu IP z preferencją routingu
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Wybierz pozycję **Utwórz zasób**.
3. W polu wyszukiwania wpisz *publiczny adres IP*.
3. W wynikach wyszukiwania wybierz pozycję **publiczny adres IP**. Następnie na stronie **publiczny adres IP** wybierz pozycję **Utwórz**.
1. W obszarze jednostka SKU wybierz pozycję **standardowa**.
1. W obszarze **Preferencje routingu** wybierz pozycję **Internet**.

      ![Tworzenie publicznego adresu IP](./media/routing-preference-portal/public-ip-new.png)
1. W sekcji **Konfiguracja adresu IP IPv4** wprowadź lub wybierz następujące informacje:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Subskrypcja | Wybierz subskrypcję.|
    | Grupa zasobów | Wybierz pozycję **Utwórz nowy**, wpisz *RoutingPreferenceResourceGroup*, a następnie wybierz przycisk **OK**. |
    | Lokalizacja | Wybierz pozycję **Wschodnie stany USA**.|
    | Strefa dostępności | Zachowaj domyślną wartość **strefowo nadmiarowe**. |
1. Wybierz przycisk **Utwórz**.

    > [!NOTE]
    > Publiczne adresy IP są tworzone przy użyciu adresu IPv4 lub IPv6. Jednak preferencja routingu obsługuje obecnie tylko protokół IPV4.

Powyższy utworzony publiczny adres IP można skojarzyć z maszyną wirtualną z [systemem Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . Użyj sekcji interfejsu wiersza polecenia na stronie samouczka: [Skojarz publiczny adres IP z maszyną wirtualną](associate-public-ip-address-vm.md#azure-cli) w celu skojarzenia publicznego adresu IP z maszyną wirtualną. Możesz również skojarzyć publiczny adres IP utworzony powyżej z [Azure Load Balancer](../load-balancer/load-balancer-overview.md), przypisując go do konfiguracji **frontonu** modułu równoważenia obciążenia. Publiczny adres IP służy jako wirtualny adres IP (VIP) o zrównoważonym obciążeniu.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [publicznym adresie IP z preferencją routingu](routing-preference-overview.md).
- [Skonfiguruj preferencję routingu dla maszyny wirtualnej](tutorial-routing-preference-virtual-machine-portal.md).
- [Skonfiguruj preferencję routingu dla publicznego adresu IP przy użyciu programu PowerShell](routing-preference-powershell.md).
- Dowiedz się więcej o [publicznych adresach IP](./public-ip-addresses.md#public-ip-addresses) na platformie Azure.
- Dowiedz się więcej na temat wszystkich [ustawień publicznego adresu IP](virtual-network-public-ip-address.md#create-a-public-ip-address).