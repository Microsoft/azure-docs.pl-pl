---
title: Otwieranie portów do maszyny wirtualnej przy użyciu Azure Portal
description: Dowiedz się, jak otworzyć port/utworzyć punkt końcowy na maszynie wirtualnej przy użyciu Azure Portal
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 05/27/2020
ms.author: cynthn
ms.openlocfilehash: 28b3ee98d44e1567d9e25cf9237015396975ab01
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102550454"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Jak otworzyć porty na maszynie wirtualnej przy użyciu Azure Portal
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

1. Wyszukaj i wybierz grupę zasobów dla maszyny wirtualnej, wybierz pozycję **Dodaj**, a następnie wyszukaj i wybierz pozycję **sieciowa Grupa zabezpieczeń**.

1. Wybierz przycisk **Utwórz**.

    Zostanie otwarte okno **Tworzenie sieciowej grupy zabezpieczeń** .

    ![Tworzenie sieciowej grupy zabezpieczeń](./media/nsg-quickstart-portal/create-nsg.png)

1. Wprowadź nazwę sieciowej grupy zabezpieczeń. 

1. Wybierz lub Utwórz grupę zasobów, a następnie wybierz lokalizację.

1. Wybierz pozycję **Utwórz** , aby utworzyć grupę zabezpieczeń sieci.

## <a name="create-an-inbound-security-rule"></a>Tworzenie reguły zabezpieczeń dla ruchu przychodzącego

1. Wybierz nową sieciową grupę zabezpieczeń. 

1. Z menu po lewej stronie wybierz pozycję **reguły zabezpieczeń ruchu przychodzącego** , a następnie wybierz pozycję **Dodaj**.

    ![Przełącz na stronę zaawansowaną](./media/nsg-quickstart-portal/advanced.png)

1. Na stronie **Dodaj regułę zabezpieczeń dla ruchu przychodzącego** Przełącz na  pozycję Zaawansowane **w górnej** części strony. 

1. Wybierz wspólną **usługę** z menu rozwijanego, na przykład **http**. Możesz również wybrać opcję **niestandardowe** , jeśli chcesz podać konkretny port. 

1. Opcjonalnie można zmienić **priorytet** lub **nazwę**. Priorytet ma wpływ na kolejność stosowania reguł: mniejsza wartość liczbowa, przed zastosowaniem reguły.

1. Wybierz pozycję **Dodaj** , aby utworzyć regułę.

## <a name="associate-your-network-security-group-with-a-subnet"></a>Kojarzenie sieciowej grupy zabezpieczeń z podsiecią

Ostatni krok to skojarzenie sieciowej grupy zabezpieczeń z podsiecią lub określonym interfejsem sieciowym. Na potrzeby tego przykładu zostanie skojarzona sieciowa Grupa zabezpieczeń z podsiecią. 

1. Z menu po lewej stronie wybierz pozycję **podsieci** , a następnie wybierz pozycję **Skojarz**.

1. Wybierz sieć wirtualną, a następnie wybierz odpowiednią podsieć.

    ![Kojarzenie sieciowej grupy zabezpieczeń z sieciami wirtualnymi](./media/nsg-quickstart-portal/select-vnet-subnet.png)

1. Gdy wszystko będzie gotowe, wybierz przycisk **OK**.

## <a name="additional-information"></a>Dodatkowe informacje

[Kroki opisane w tym artykule można także wykonać przy użyciu Azure PowerShell](nsg-quickstart-powershell.md).

Polecenia opisane w tym artykule pozwalają szybko uzyskać ruch sieciowy przepływający na maszynę wirtualną. Sieciowe grupy zabezpieczeń zapewniają wiele doskonałych funkcji i szczegółowości kontroli dostępu do zasobów. Aby uzyskać więcej informacji, zobacz [Filtrowanie ruchu sieciowego za pomocą sieciowej grupy zabezpieczeń](../../virtual-network/tutorial-filter-network-traffic.md).

W przypadku aplikacji sieci Web o wysokiej dostępności Rozważ umieszczenie maszyn wirtualnych za modułem równoważenia obciążenia platformy Azure. Moduł równoważenia obciążenia dystrybuuje ruch do maszyn wirtualnych z sieciową grupą zabezpieczeń, która zapewnia filtrowanie ruchu. Aby uzyskać więcej informacji, zobacz [równoważenie obciążenia maszyn wirtualnych systemu Windows na platformie Azure w celu utworzenia aplikacji o wysokiej](tutorial-load-balancer.md)dostępności.

## <a name="next-steps"></a>Następne kroki
W tym artykule utworzono sieciową grupę zabezpieczeń, utworzoną regułę ruchu przychodzącego, która zezwala na ruch HTTP na porcie 80, a następnie skojarzono tę regułę z podsiecią. 

Informacje na temat tworzenia bardziej szczegółowych środowisk można znaleźć w następujących artykułach:
- [Przegląd Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- [Grupy zabezpieczeń](../../virtual-network/network-security-groups-overview.md)
