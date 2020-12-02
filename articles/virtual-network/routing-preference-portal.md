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
ms.date: 05/18/2020
ms.author: mnayak
ms.openlocfilehash: 21d9f318ef18b7ffb49a95ce495c09f1fa46ec1a
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96491319"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-the-azure-portal"></a>Skonfiguruj preferencję routingu dla publicznego adresu IP przy użyciu Azure Portal

W tym artykule pokazano, jak skonfigurować [preferencję routingu](https://docs.microsoft.com/azure/virtual-network/routing-preference-overview) za pośrednictwem sieci usługodawcy internetowego (opcja **internetowa** ) dla publicznego adresu IP. Po utworzeniu publicznego adresu IP możesz skojarzyć go z poniższymi zasobami platformy Azure dla ruchu przychodzącego i wychodzącego do Internetu:

* Maszyna wirtualna
* Zestaw skalowania maszyn wirtualnych
* Azure Kubernetes Service (AKS)
* Moduł równoważenia obciążenia dostępny z Internetu
* Application Gateway
* Azure Firewall

Domyślnie preferencja routingu dla publicznego adresu IP jest ustawiana na sieć globalna firmy Microsoft dla wszystkich usług platformy Azure i może być skojarzona z dowolną usługą platformy Azure.

> [!IMPORTANT]
> Preferencje routingu są obecnie dostępne w publicznej wersji zapoznawczej.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone. Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Jeśli nie masz subskrypcji platformy Azure, utwórz teraz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-public-ip-address-with-a-routing-preference"></a>Tworzenie publicznego adresu IP z preferencją routingu
1. Zaloguj się w witrynie [Azure Portal](https://preview.portal.azure.com/).
2. Wybierz pozycję **Utwórz zasób**. 
3. W polu wyszukiwania wpisz *publiczny adres IP*.
3. W wynikach wyszukiwania wybierz pozycję **publiczny adres IP**. Następnie na stronie **publiczny adres IP** wybierz pozycję **Utwórz**.
3. W opcji **preferencji routingu** wybierz opcję **Internet**.

      ![Tworzenie publicznego adresu IP](./media/routing-preference-portal/pip-new.png)

    > [!NOTE]
    > Publiczne adresy IP są tworzone przy użyciu adresu IPv4 lub IPv6. Jednak preferencja routingu obsługuje obecnie tylko protokół IPV4.

Powyższy utworzony publiczny adres IP można skojarzyć z maszyną wirtualną z [systemem Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) lub [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) . Użyj sekcji interfejsu wiersza polecenia na stronie samouczka: [Skojarz publiczny adres IP z maszyną wirtualną](associate-public-ip-address-vm.md#azure-cli) w celu skojarzenia publicznego adresu IP z maszyną wirtualną. Możesz również skojarzyć publiczny adres IP utworzony powyżej z [Azure Load Balancer](../load-balancer/load-balancer-overview.md), przypisując go do konfiguracji **frontonu** modułu równoważenia obciążenia. Publiczny adres IP służy jako wirtualny adres IP (VIP) o zrównoważonym obciążeniu.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [publicznym adresie IP z preferencją routingu](routing-preference-overview.md).
- [Skonfiguruj preferencję routingu dla maszyny wirtualnej](tutorial-routing-preference-virtual-machine-portal.md).
- [Skonfiguruj preferencję routingu dla publicznego adresu IP przy użyciu programu PowerShell](routing-preference-powershell.md).
- Dowiedz się więcej o [publicznych adresach IP](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) na platformie Azure.
- Dowiedz się więcej na temat wszystkich [ustawień publicznego adresu IP](virtual-network-public-ip-address.md#create-a-public-ip-address).
