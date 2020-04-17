---
title: Konfigurowanie prywatnych adresów IP dla maszyn wirtualnych — witryna Azure portal
description: Dowiedz się, jak skonfigurować prywatne adresy IP dla maszyn wirtualnych przy użyciu witryny Azure portal.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2020
ms.author: kumud
ms.openlocfilehash: 946926a8a805ec3c53ea3c57dc3eded2462f7673
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81461552"
---
# <a name="configure-a-private-ip-address-for-a-vm-using-the-azure-portal"></a>Konfigurowanie prywatnego adresu IP dla maszyny Wirtualnej przy użyciu portalu Azure

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Poniższe przykładowe kroki oczekują, że proste środowisko zostanie już utworzone. Jeśli chcesz uruchomić kroki wyświetlane w tym dokumencie, najpierw [utwórz sieć wirtualną](quick-create-portal.md#create-a-virtual-network). Jednak w kroku 3 należy użyć tych wartości zamiast:

| Ustawienie | Wartość |
| ------- | ----- |
| Nazwa | *TestVNet (właśc.* |
| Przestrzeń adresowa | *192.168.0.0/16* |
| Grupa zasobów | **TestRG** (w razie potrzeby wybierz **pozycję Utwórz nowy,** aby go utworzyć) |
| Podsieć — nazwa | *Frontend* |
| Zakres adresów podsieci: 10.41.0.0/24 | *192.168.1.0/24* |

## <a name="create-a-vm-for-testing-static-private-ip-addresses"></a>Tworzenie maszyny Wirtualnej do testowania statycznych prywatnych adresów IP
Podczas tworzenia maszyny Wirtualnej w trybie wdrażania Usługi Resource Manager nie można ustawić statycznego prywatnego adresu IP przy użyciu portalu Azure. Zamiast tego najpierw należy utworzyć maszynę wirtualną. Następnie można ustawić jego prywatny adres IP jako statyczny.

Aby utworzyć maszynę wirtualną o nazwie *DNS01* w podsieci *FrontEnd* sieci wirtualnej o nazwie *TestVNet*, wykonaj następujące kroki:

1. Z menu [Portalu platformy Azure](https://portal.azure.com) wybierz polecenie **Utwórz zasób**.

    ![Tworzenie zasobu w portalu Azure](./media/virtual-networks-static-ip-arm-pportal/create-a-resource.png)
2. Wybierz **opcję Compute** > **Virtual machine**.

    ![Tworzenie maszyny Wirtualnej, witryny Azure portal](./media/virtual-networks-static-ip-arm-pportal/compute-virtual-machine.png)
3. W **obszarze Podstawy**określ wartości dla elementów, zgodnie z opisem w poniższej tabeli. Następnie wybierz **przycisk Dalej&nbsp;:&nbsp;Dyski,** a następnie dalej **&nbsp;:&nbsp;Sieć**.

    | Element | Wartość |
    | --- | --- |
    | **Subskrypcja** | Twoja obecna subskrypcja |
    | **Grupa zasobów** | **TestRG** (wybierz z listy rozwijanej) |
    | **Nazwa maszyny wirtualnej** | *Dns01 (dns01)* |
    | **Region** | **(Stany Zjednoczone) Wschodnie stany USA** |
    | **Image (Obraz)** | **Windows Server 2019 Datacenter** |
    | **Rozmiar** | **Rozmiar maszyny Wirtualnej** **b1ls**, **oferta** **standardu** |
    | **Nazwę użytkownika** | Nazwa użytkownika konta administratora |
    | **Hasło** | Hasło do nazwy użytkownika konta administratora |
    | **Potwierdź hasło** | Hasło ponownie |

    ![Karta Podstawy, Tworzenie maszyny wirtualnej, witryna Azure portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-basics.png)
4. W **obszarze Sieć**określ wartości elementów zgodnie z opisem w poniższej tabeli, a następnie wybierz pozycję **Dalej**.

    | Element | Wartość |
    | --- | --- |
    | **Sieć wirtualna** | **TestVNet (właśc.** |
    | **Podsieci** | **Frontend** |

    ![Karta Sieć, Tworzenie maszyny wirtualnej, witryna Azure portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-networking.png)
5. W **obszarze Zarządzanie**w obszarze Konto magazynu **diagnostyki**wybierz opcję **vnetstorage**. Jeśli to konto magazynu nie jest wyświetlane na liście, wybierz pozycję **Utwórz nowy**, określ **nazwę** *vnetstorage*i wybierz **przycisk OK**. Na koniec wybierz **pozycję&nbsp;+&nbsp;Przejrzyj utwórz**.

    ![Karta Zarządzanie, Tworzenie maszyny wirtualnej, witryna Azure portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-management.png)
6. W **obszarze Recenzja + utwórz**przejrzyj informacje o przeglądzie, a następnie wybierz pozycję **Utwórz**.

    ![Recenzja + utwórz kartę, Tworzenie maszyny wirtualnej, witryna Azure portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-review-create.png)

Po utworzeniu maszyny Wirtualnej pojawi się następujący komunikat.

![Komunikat o zakończeniu wdrażania, Tworzenie maszyny wirtualnej, witryna Azure portal](./media/virtual-networks-static-ip-arm-pportal/deployment-is-complete.png)

## <a name="retrieve-private-ip-address-information-for-a-vm"></a>Pobieranie prywatnych informacji o adresie IP maszyny Wirtualnej
Aby wyświetlić prywatne informacje o adresie IP nowej maszyny Wirtualnej:

1. Przejdź do [witryny Azure portal,](https://portal.azure.com) aby znaleźć maszynę wirtualną. Wyszukaj i wybierz **maszyny wirtualne**.

    ![Maszyny wirtualne, pole wyszukiwania, witryna Azure portal](./media/virtual-networks-static-ip-arm-pportal/search-box-virtual-machines.png)

2. Wybierz nazwę nowej maszyny Wirtualnej (**DNS01**).

    ![Lista maszyn wirtualnych, witryna Azure portal](./media/virtual-networks-static-ip-arm-pportal/virtual-machine-list.png)

3. Wybierz **pozycję Sieć**i wybierz jedyny na liście interfejs sieciowy.

    ![Interfejs sieciowy, sieć, maszyna wirtualna, witryna Azure portal](./media/virtual-networks-static-ip-arm-pportal/networking-network-interface.png)

4. Wybierz **konfiguracje IP**i wybierz konfigurację IP wymienioną w tabeli.

    ![Konfiguracja IP, interfejs sieciowy, sieć, maszyna wirtualna, portal Azure](./media/virtual-networks-static-ip-arm-pportal/network-interface-ip-configurations.png)

5. W **ustawieniach prywatnego adresu IP**w obszarze Sieć wirtualna/podsieć **TestVNet/FrontEnd** zanotuj wartość **przydziału** **(dynamiczna** lub **statyczna)** oraz **adres IP**.

    ![Przypisanie dynamiczne lub statyczne, stare prywatne ustawienia adresów IP, konfiguracja IP, interfejs sieciowy, sieć, maszyna wirtualna, portal Azure](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-old.png)

## <a name="add-a-static-private-ip-address-to-an-existing-vm"></a>Dodawanie statycznego prywatnego adresu IP do istniejącej maszyny Wirtualnej
Aby dodać statyczny prywatny adres IP do nowej maszyny Wirtualnej:

1. Na stronie konfiguracji IP ustaw przypisanie prywatnego adresu IP na **Statyczny**.
2. Zmień swój prywatny **adres IP** na *192.168.1.101*, a następnie wybierz pozycję **Zapisz**.
   
    ![Przypisanie dynamiczne lub statyczne, nowe ustawienia prywatnego adresu IP, konfiguracja IP, interfejs sieciowy, sieć, maszyna wirtualna, portal Azure](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-new.png)

> [!NOTE]
> Jeśli po wybraniu opcji Zapisz zostanie **zauważone,** że przypisanie jest nadal ustawione na **Dynamiczny,** wpisany adres IP jest już używany. Spróbuj użyć innego adresu IP.

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Usuwanie statycznego prywatnego adresu IP z maszyny Wirtualnej
Aby usunąć statyczny prywatny adres IP z maszyny Wirtualnej:

Na stronie konfiguracji IP ustaw przypisanie prywatnego adresu IP na **Dynamiczny,** a następnie wybierz pozycję **Zapisz**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Ustawianie adresów IP w systemie operacyjnym

Z poziomu systemu operacyjnego maszyny Wirtualnej nie należy statycznie przypisywać *prywatnego* adresu IP przypisanego do maszyny Wirtualnej platformy Azure. Statyczne przypisywanie prywatnego adresu IP jest wymagane tylko wtedy, gdy jest to konieczne, na przykład podczas [przypisywania wielu adresów IP do maszyn wirtualnych.](virtual-network-multiple-ip-addresses-portal.md) Jeśli ręcznie ustawisz prywatny adres IP w systemie operacyjnym, upewnij się, że jest on zgodny z prywatnym adresem IP przypisanym do [interfejsu sieciowego](virtual-network-network-interface-addresses.md#change-ip-address-settings)platformy Azure . W przeciwnym razie można utracić łączność z maszyną wirtualną. Dowiedz się więcej o [ustawieniach prywatnych adresów IP.](virtual-network-network-interface-addresses.md#private)

Ponadto nigdy nie należy ręcznie przypisywać *publicznego* adresu IP przypisanego do maszyny wirtualnej platformy Azure w systemie operacyjnym maszyny wirtualnej.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o zarządzaniu [ustawieniami adresu IP](virtual-network-network-interface-addresses.md).
