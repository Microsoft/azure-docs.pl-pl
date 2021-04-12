---
title: Skonfiguruj obie opcje preferencji routingu dla maszyny wirtualnej — Azure Portal
description: Informacje na temat włączania obu opcji preferencji routingu
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
ms.openlocfilehash: d9bb00077b1d96d13e15861cac477f913a002030
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101679661"
---
# <a name="configure-both-routing-preference-options-for-a-virtual-machine"></a>Skonfiguruj obie opcje preferencji routingu dla maszyny wirtualnej

W tym artykule opisano sposób konfigurowania opcji [preferencji routingu](./routing-preference-overview.md) (Internet i Microsoft Global Network) dla maszyny wirtualnej. Jest to osiągane przy użyciu dwóch interfejsów sieci wirtualnej, jednego interfejsu sieciowego z publicznym adresem IP kierowanym za pośrednictwem sieci globalnej firmy Microsoft, a drugi z publicznym adresem IP kierowanym przez sieć usługodawcy internetowego.

## <a name="prerequisites"></a>Wymagania wstępne

Utwórz maszynę wirtualną z publicznym adresem IP, postępując zgodnie z instrukcjami opisanymi w artykule [Tworzenie maszyny wirtualnej ze statycznym publicznym adresem IP przy użyciu Azure Portal](./virtual-network-deploy-static-pip-arm-portal.md) przy użyciu Azure Portal. Domyślny wybór preferencji routingu dla publicznego adresu IP to za pośrednictwem **sieci globalnej firmy Microsoft**. Gdy masz utworzoną maszynę wirtualną z publicznym adresem IP, Dodaj drugi publiczny adres IP do maszyny wirtualnej, która kieruje ruch za pośrednictwem sieci tranzytowej ISP z preferencjami routingu skonfigurowanymi jako **Internet**.

## <a name="create-a-public-ip-address-with-a-routing-preference-choice-internet"></a>Utwórz publiczny adres IP z wyborem preferencji routingu Internet
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
2. Wybierz pozycję **Utwórz zasób**. 
3. W polu wyszukiwania wpisz *publiczny adres IP*.
4. W wynikach wyszukiwania wybierz pozycję **publiczny adres IP**. Następnie na stronie **publiczny adres IP** wybierz pozycję **Utwórz**.
5. W opcji **preferencji routingu** wybierz opcję **Internet**.

      ![Tworzenie publicznego adresu IP](./media/routing-preference-portal/public-ip-new.png)

    > [!NOTE]
    > Publiczne adresy IP są tworzone przy użyciu adresu IPv4 lub IPv6. Jednak preferencja routingu obsługuje obecnie tylko protokół IPV4.

## <a name="create-a-network-interface-and-associate-the-public-ip"></a>Utwórz interfejs sieciowy i skojarz publiczny adres IP

1. Utwórz [interfejs sieciowy](./routing-preference-overview.md) przy użyciu Azure Portal z ustawieniami domyślnymi. 
1. [Skojarz publiczny adres IP z interfejsem sieciowym](./associate-public-ip-address-vm.md) utworzonym w poprzedniej sekcji. Pojawienie się adresu IP może potrwać kilka sekund. Wyświetl publiczny adres IP przypisany do konfiguracji protokołu IP, jak pokazano poniżej:

    ![Skojarz publiczny adres IP](./media/routing-preference-mixed-network-adapter-portal/public-ip.png)

## <a name="attach-network-interface-to-the-vm"></a>Dołącz interfejs sieciowy do maszyny wirtualnej

1. [Dołącz interfejs sieciowy utworzony w poprzedniej sekcji do maszyny wirtualnej](./virtual-network-network-interface-vm.md).
2. Teraz można wyświetlać dwa interfejsy sieci wirtualnej skojarzone z maszyną wirtualną, jedną z publicznym adresem IP, która jest kierowana za pośrednictwem sieci Microsoft Global Network, a druga — za pośrednictwem sieci usługodawcy internetowego, jak pokazano poniżej:  ![ dołączanie interfejsu sieciowego do maszyny wirtualnej](./media/routing-preference-mixed-network-adapter-portal/mixed-network-adapter.png) 

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [publicznym adresie IP z preferencją routingu](routing-preference-overview.md).
- [Skonfiguruj preferencję routingu dla maszyny wirtualnej](tutorial-routing-preference-virtual-machine-portal.md).
- [Skonfiguruj preferencję routingu dla publicznego adresu IP przy użyciu programu PowerShell](routing-preference-powershell.md).
- Dowiedz się więcej o [publicznych adresach IP](./public-ip-addresses.md#public-ip-addresses) na platformie Azure.
- Dowiedz się więcej na temat wszystkich [ustawień publicznego adresu IP](virtual-network-public-ip-address.md#create-a-public-ip-address).