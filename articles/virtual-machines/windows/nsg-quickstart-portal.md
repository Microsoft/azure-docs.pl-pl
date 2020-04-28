---
title: Otwieranie portów na maszynę wirtualną przy użyciu witryny Azure portal
description: Dowiedz się, jak otworzyć port / utworzyć punkt końcowy dla maszyny Wirtualnej systemu Windows przy użyciu portalu Azure Portal
author: cynthn
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 09/27/2018
ms.author: cynthn
ms.openlocfilehash: 1844236a77b688819832b3fe0bf6736beea4bfae
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81865484"
---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Jak otworzyć porty na maszynie wirtualnej za pomocą witryny Azure portal
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]


## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-network-security-group"></a>Tworzenie sieciowej grupy zabezpieczeń

1. Wyszukaj i wybierz grupę zasobów maszyny Wirtualnej, wybierz pozycję **Dodaj**, a następnie wyszukaj i wybierz pozycję **Grupa zabezpieczeń Sieć**.

2. Wybierz pozycję **Utwórz**.

    Zostanie otwarte okno **Tworzenie sieciowej grupy zabezpieczeń.**

    ![Tworzenie sieciowej grupy zabezpieczeń](./media/nsg-quickstart-portal/create-nsg.png)

2. Wprowadź nazwę sieciowej grupy zabezpieczeń. 

3. Zaznacz lub utwórz grupę zasobów, a następnie wybierz lokalizację.

4. Wybierz **pozycję Utwórz,** aby utworzyć grupę zabezpieczeń sieciowych.

## <a name="create-an-inbound-security-rule"></a>Tworzenie reguły zabezpieczeń ruchu przychodzącego

1. Wybierz nową grupę zabezpieczeń sieci. 

2. Wybierz **pozycję Przychodzące reguły zabezpieczeń**, a następnie wybierz pozycję **Dodaj**.

    ![Dodawanie reguły przychodzącej](./media/nsg-quickstart-portal/add-inbound-rule.png)

3. Wybierz **pozycję Zaawansowane**. 

4. Wybierz wspólną **usługę** z menu rozwijanego, takiego jak **HTTP**. Można również wybrać **opcję Niestandardowe,** jeśli chcesz podać określony port do użycia. 

5. Opcjonalnie zmień **priorytet** lub **nazwę**. Priorytet ma wpływ na kolejność, w jakiej reguły są stosowane: im niższa wartość liczbowa, tym wcześniej reguła jest stosowana.

6. Wybierz **pozycję Dodaj,** aby utworzyć regułę.

## <a name="associate-your-network-security-group-with-a-subnet"></a>Kojarzenie sieciowej grupy zabezpieczeń z podsiecią

Ostatni krok to skojarzenie sieciowej grupy zabezpieczeń z podsiecią lub określonym interfejsem sieciowym. W tym przykładzie skojarzymy grupę zabezpieczeń sieci z podsiecią. 

1. Wybierz **podsieci**, a następnie wybierz pozycję **Skojarz**.

    ![Kojarzenie sieciowej grupy zabezpieczeń z podsiecią](./media/nsg-quickstart-portal/associate-subnet.png)

2. Wybierz sieć wirtualną, a następnie wybierz odpowiednią podsieć.

    ![Kojarzenie sieciowej grupy zabezpieczeń z siecią wirtualną](./media/nsg-quickstart-portal/select-vnet-subnet.png)

    Wszystkie maszyny wirtualne, które łączysz z tą podsiecią, są teraz dostępne na porcie 80.

## <a name="additional-information"></a>Dodatkowe informacje

Kroki opisane w tym artykule można również [wykonać przy użyciu programu Azure PowerShell](nsg-quickstart-powershell.md).

Polecenia opisane w tym artykule umożliwiają szybkie uzyskanie ruchu przepływającego do maszyny Wirtualnej. Sieciowe grupy zabezpieczeń zapewniają wiele wspaniałych funkcji i szczegółowość do kontrolowania dostępu do zasobów. Aby uzyskać więcej informacji, zobacz [Filtrowanie ruchu sieciowego za pomocą sieciowej grupy zabezpieczeń](../../virtual-network/tutorial-filter-network-traffic.md).

W przypadku aplikacji sieci Web o wysokiej dostępności należy rozważyć umieszczenie maszyn wirtualnych za modułem równoważenia obciążenia platformy Azure. Moduł równoważenia obciążenia rozdziela ruch do maszyn wirtualnych z sieciową grupą zabezpieczeń, która zapewnia filtrowanie ruchu. Aby uzyskać więcej informacji, zobacz [Równoważenie obciążenia maszyn wirtualnych systemu Windows na platformie Azure w celu utworzenia aplikacji o wysokiej dostępności](tutorial-load-balancer.md).

## <a name="next-steps"></a>Następne kroki
W tym artykule utworzono sieciową grupę zabezpieczeń, utworzono regułę ruchu przychodzącego, która zezwala na ruch HTTP na porcie 80, a następnie skojarzyno tę regułę z podsiecią. 

Informacje na temat tworzenia bardziej szczegółowych środowisk można znaleźć w następujących artykułach:
- [Omówienie usługi Azure Resource Manager](../../azure-resource-manager/management/overview.md)
- [Grupy zabezpieczeń](../../virtual-network/security-overview.md)