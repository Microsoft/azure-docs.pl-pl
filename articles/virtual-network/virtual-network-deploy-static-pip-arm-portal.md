---
title: Tworzenie maszyny wirtualnej ze statycznym publicznym adresem IP — Azure Portal | Microsoft Docs
description: Dowiedz się, jak utworzyć maszynę wirtualną ze statycznym publicznym adresem IP przy użyciu Azure Portal.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/08/2018
ms.author: allensu
ms.openlocfilehash: 9e06e4079a5118e0aa9dedb1fca719f0b28e5716
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91448632"
---
# <a name="create-a-virtual-machine-with-a-static-public-ip-address-using-the-azure-portal"></a>Tworzenie maszyny wirtualnej ze statycznym publicznym adresem IP przy użyciu Azure Portal

Można utworzyć maszynę wirtualną ze statycznym publicznym adresem IP. Publiczny adres IP umożliwia komunikację z maszyną wirtualną z Internetu. Przypisz statyczny publiczny adres IP, a nie adres dynamiczny, aby upewnić się, że adres nigdy nie ulegnie zmianie. Dowiedz się więcej na temat [statycznych publicznych adresów IP](virtual-network-ip-addresses-overview-arm.md#allocation-method). Aby zmienić publiczny adres IP przypisany do istniejącej maszyny wirtualnej z dynamicznego na statyczny lub do pracy z prywatnymi adresami IP, zobacz [Dodawanie, zmienianie lub usuwanie adresów IP](virtual-network-network-interface-addresses.md). Publiczne adresy IP mają [opłata nominalną](https://azure.microsoft.com/pricing/details/ip-addresses), a liczba publicznych adresów IP, których można użyć na subskrypcję, jest [ograniczona](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) .

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **+ Utwórz zasób**.
2. Wybierz pozycję **obliczenia**, a następnie wybierz pozycję **maszyna wirtualna systemu Windows Server 2016**lub inny wybrany system operacyjny.
3. Wprowadź lub wybierz poniższe informacje, zaakceptuj wartości domyślne pozostałych ustawień, a następnie wybierz przycisk **OK**:

    |Ustawienie|Wartość|
    |---|---|
    |Nazwa|myVM|
    |Nazwa użytkownika| Wprowadź wybraną nazwę użytkownika.|
    |Hasło| Wprowadź wybrane hasło. Hasło musi mieć długość co najmniej 12 znaków i spełniać [zdefiniowane wymagania dotyczące złożoności](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
    |Subskrypcja| Wybierz subskrypcję.|
    |Grupa zasobów| Wybierz pozycję **Użyj istniejącej** i wybierz grupę **myResourceGroup**.|
    |Lokalizacja| Wybierz **Wschodnie stany USA**|

4. Wybierz rozmiar maszyny wirtualnej, a następnie wybierz pozycję **Wybierz**.
5. W obszarze **Ustawienia**wybierz pozycję **publiczny adres IP**.
6. Wprowadź *myPublicIpAddress*, wybierz pozycję **static**, a następnie wybierz przycisk **OK**, jak pokazano na poniższej ilustracji:

   ![Wybierz statyczny](./media/virtual-network-deploy-static-pip-arm-portal/select-static.png)

   Jeśli publiczny adres IP musi być standardową jednostką SKU, wybierz pozycję **standardowa** w obszarze **jednostka SKU**. Dowiedz się więcej o jednostkach [SKU publicznych adresów IP](virtual-network-ip-addresses-overview-arm.md#sku). Jeśli maszyna wirtualna zostanie dodana do puli zaplecza Azure Load Balancer publicznej, jednostka SKU publicznego adresu IP maszyny wirtualnej musi być zgodna z jednostką SKU publicznego adresu IP modułu równoważenia obciążenia. Aby uzyskać szczegółowe informacje, zobacz [Azure Load Balancer](../load-balancer/skus.md).

6. Wybierz port lub brak portów w obszarze **Wybierz publiczne porty przychodzące**. Wybrano Portal 3389, aby włączyć dostęp zdalny do maszyny wirtualnej z systemem Windows Server z Internetu. Nie zaleca się otwierania portu 3389 z Internetu w przypadku obciążeń produkcyjnych.

   ![Wybierz port](./media/virtual-network-deploy-static-pip-arm-portal/select-port.png)

7. Zaakceptuj pozostałe ustawienia domyślne i wybierz **przycisk OK**.
8. Na stronie **Podsumowanie** wybierz pozycję **Utwórz**. Wdrożenie maszyny wirtualnej trwa kilka minut.
9. Po wdrożeniu maszyny wirtualnej wprowadź *myPublicIpAddress* w polu wyszukiwania w górnej części portalu. Gdy **myPublicIpAddress** pojawia się w wynikach wyszukiwania, wybierz ją.
10. Można wyświetlić przypisany publiczny adres IP i adres przypisany do maszyny wirtualnej **myVM** , jak pokazano na poniższej ilustracji:

    ![Zrzut ekranu pokazuje okienko publiczny I P z adresem i nazwą o nazwie. ](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-overview.png)

    Na platformie Azure przypisano publiczny adres IP z adresów używanych w regionie, w którym została utworzona maszyna wirtualna. Możesz pobrać listę zakresów (prefiksów) dla następujących chmur platformy Azure: [Publiczna](https://www.microsoft.com/download/details.aspx?id=56519), [Instytucje rządowe USA](https://www.microsoft.com/download/details.aspx?id=57063), [Chiny](https://www.microsoft.com/download/details.aspx?id=57062) i [Niemcy](https://www.microsoft.com/download/details.aspx?id=57064).

11. Wybierz pozycję **Konfiguracja** , aby potwierdzić, że przypisanie jest **statyczne**.

    ![Zrzut ekranu przedstawia okienko publiczny I P z wybranym elementem konfiguracji.](./media/virtual-network-deploy-static-pip-arm-portal/public-ip-configuration.png)

> [!WARNING]
> Nie należy modyfikować ustawień adresu IP w ramach systemu operacyjnego maszyny wirtualnej. System operacyjny nie rozpoznaje publicznych adresów IP platformy Azure. Mimo że można dodać ustawienia prywatnych adresów IP do systemu operacyjnego, zaleca się, aby nie było to możliwe, chyba że jest to konieczne, a nie do momentu [dodania prywatnego adresu IP do systemu operacyjnego](virtual-network-network-interface-addresses.md#private).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie znajdujące się w niej zasoby nie będą już potrzebne, usuń je:

1. Wprowadź ciąg *myResourceGroup* w polu **Szukaj** w górnej części portalu. Gdy pozycja **myResourceGroup** pojawi się w wynikach wyszukiwania, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. Wprowadź dla elementu *Webresourcename* **Typ Nazwa grupy zasobów:** a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [publicznych adresach IP](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses) na platformie Azure
- Dowiedz się więcej na temat wszystkich [ustawień publicznego adresu IP](virtual-network-public-ip-address.md#create-a-public-ip-address)
- Dowiedz się więcej o prywatnych adresach [IP](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) i przypisywaniu [statycznego prywatnego adresu IP](virtual-network-network-interface-addresses.md#add-ip-addresses) do maszyny wirtualnej platformy Azure
- Dowiedz się więcej o tworzeniu maszyn wirtualnych z systemem [Linux](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json) i [Windows](../virtual-machines/windows/tutorial-manage-vm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)