---
title: Konfigurowanie preferencji routingu dla maszyny wirtualnej Azure Portal
description: Dowiedz się, jak utworzyć maszynę wirtualną z publicznym adresem IP z wyborem preferencji routingu za pomocą Azure Portal.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/01/2021
ms.author: mnayak
ms.openlocfilehash: 0559d02ec603d12578fa46d9790d0711fde5e38b
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670906"
---
# <a name="configure-routing-preference-for-a-vm-using-the-azure-portal"></a>Konfigurowanie preferencji routingu dla maszyny wirtualnej przy użyciu Azure Portal

W tym artykule opisano sposób konfigurowania preferencji routingu dla maszyny wirtualnej. Ruch związany z Internetem z maszyny wirtualnej będzie kierowany przez sieć usługodawcy internetowego w przypadku wybrania opcji **Internet** jako preferencji routingu. Domyślny Routing odbywa się za pośrednictwem sieci globalnej firmy Microsoft.

W tym artykule pokazano, jak utworzyć maszynę wirtualną z publicznym adresem IP, która jest ustawiona na kierowanie ruchu za pośrednictwem publicznej sieci Internet przy użyciu Azure Portal.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

## <a name="create-a-virtual-machine"></a>Tworzenie maszyny wirtualnej

1. W lewym górnym rogu witryny Azure Portal wybierz pozycję **+ Utwórz zasób**.
2. Wybierz pozycję **obliczenia**, a następnie wybierz pozycję **maszyna wirtualna systemu Windows Server 2016** lub inny wybrany system operacyjny.
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
5. W obszarze Karta **Sieć** kliknij pozycję **Utwórz nowy** dla **publicznego adresu IP**.
6. Wprowadź *myPublicIpAddress*, wybierz pozycję SKU jako **standardowa**, a następnie wybierz pozycję Preferencje routingu **Internet** , a następnie kliknij przycisk **OK**, jak pokazano na poniższej ilustracji:

   ![Wybierz statyczny](./media/tutorial-routing-preference-virtual-machine-portal/routing-preference-internet-new.png)

6. Wybierz port lub brak portów w obszarze **Wybierz publiczne porty przychodzące**. Wybrano Portal 3389, aby włączyć dostęp zdalny do maszyny wirtualnej z systemem Windows Server z Internetu. Nie zaleca się otwierania portu 3389 z Internetu w przypadku obciążeń produkcyjnych.

   ![Wybierz port](./media/tutorial-routing-preference-virtual-machine-portal/pip-ports-new.png)

7. Zaakceptuj pozostałe ustawienia domyślne i wybierz **przycisk OK**.
8. Na stronie **Podsumowanie** wybierz pozycję **Utwórz**. Wdrożenie maszyny wirtualnej trwa kilka minut.
9. Po wdrożeniu maszyny wirtualnej wprowadź *myPublicIpAddress* w polu wyszukiwania w górnej części portalu. Gdy **myPublicIpAddress** pojawia się w wynikach wyszukiwania, wybierz ją.
10. Można wyświetlić przypisany publiczny adres IP i adres przypisany do maszyny wirtualnej **myVM** , jak pokazano na poniższej ilustracji:

    ![Zrzut ekranu przedstawia kartę sieciową (P) dla interfejsu sieciowego mynic.](./media/tutorial-routing-preference-virtual-machine-portal/pip-properties-new.png)

11. Wybierz pozycję **Sieć**, a następnie kliknij pozycję Karta sieciowa **mynic** , a następnie wybierz publiczny adres IP, aby upewnić się, że preferencja routingu jest przypisana jako **Internet**.

    ![Zrzut ekranu przedstawia adres i preferencję routingu dla publicznego adresu P.](./media/tutorial-routing-preference-virtual-machine-portal/pip-routing-internet-new.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów i wszystkie znajdujące się w niej zasoby nie będą już potrzebne, usuń je:

1. Wprowadź ciąg *myResourceGroup* w polu **Szukaj** w górnej części portalu. Gdy pozycja **myResourceGroup** pojawi się w wynikach wyszukiwania, wybierz ją.
2. Wybierz pozycję **Usuń grupę zasobów**.
3. Wprowadź dla elementu *Webresourcename* **Typ Nazwa grupy zasobów:** a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [publicznym adresie IP z preferencją routingu](routing-preference-overview.md).
- Dowiedz się więcej o [publicznych adresach IP](./public-ip-addresses.md#public-ip-addresses) na platformie Azure.
- Dowiedz się więcej na temat wszystkich [ustawień publicznego adresu IP](virtual-network-public-ip-address.md#create-a-public-ip-address).