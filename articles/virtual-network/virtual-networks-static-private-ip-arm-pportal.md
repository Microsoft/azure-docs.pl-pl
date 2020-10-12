---
title: Konfigurowanie prywatnych adresów IP dla maszyn wirtualnych — Azure Portal
description: Dowiedz się, jak skonfigurować prywatne adresy IP dla maszyn wirtualnych przy użyciu Azure Portal.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
tags: azure-resource-manager
ms.assetid: 11245645-357d-4358-9a14-dd78e367b494
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/07/2020
ms.author: kumud
ms.openlocfilehash: c8fdba59a8d31c064745c7a1904204359b386a7f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84707858"
---
# <a name="configure-a-private-ip-address-for-a-vm-using-the-azure-portal"></a>Konfigurowanie prywatnego adresu IP dla maszyny wirtualnej przy użyciu Azure Portal

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Następujące przykładowe kroki oczekują na utworzenie prostego środowiska. Jeśli chcesz uruchomić kroki, które są wyświetlane w tym dokumencie, najpierw [Utwórz sieć wirtualną](quick-create-portal.md#create-a-virtual-network). Jednakże w kroku 3 Użyj następujących wartości:

| Ustawienie | Wartość |
| ------- | ----- |
| Nazwa | *TestVNet* |
| Przestrzeń adresowa | *192.168.0.0/16* |
| Grupa zasobów | **TestRG** (jeśli to konieczne, wybierz pozycję **Utwórz nowy** , aby ją utworzyć) |
| Podsieć — nazwa | *Frontonu* |
| Zakres adresów podsieci: 10.41.0.0/24 | *192.168.1.0/24* |

## <a name="create-a-vm-for-testing-static-private-ip-addresses"></a>Tworzenie maszyny wirtualnej do testowania statycznych prywatnych adresów IP
Podczas tworzenia maszyny wirtualnej w trybie wdrażania Menedżer zasobów nie można ustawić statycznego prywatnego adresu IP przy użyciu Azure Portal. Zamiast tego należy najpierw utworzyć maszynę wirtualną. Następnie możesz ustawić swój prywatny adres IP jako statyczny.

Aby utworzyć maszynę wirtualną o nazwie *DNS01* w podsieci *frontonu* sieci wirtualnej o nazwie *TestVNet*, wykonaj następujące kroki:

1. Z menu [Azure Portal](https://portal.azure.com) wybierz pozycję **Utwórz zasób**.

    ![Utwórz zasób, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-resource.png)
2. Wybierz pozycję **Oblicz**  >  **maszynę wirtualną**.

    ![Tworzenie maszyny wirtualnej, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/compute-virtual-machine.png)
3. W obszarze **podstawowe**Określ wartości dla elementów zgodnie z opisem w poniższej tabeli. Następnie wybierz kolejno pozycje **dalej &nbsp; : &nbsp; dyski** , a następnie **dalej &nbsp; : &nbsp; sieci**.

    | Element | Wartość |
    | --- | --- |
    | **Subskrypcja** | Twoja bieżąca subskrypcja |
    | **Grupa zasobów** | **TestRG** (wybierz z listy rozwijanej) |
    | **Nazwa maszyny wirtualnej** | *DNS01* |
    | **Region** | **(USA) Wschodnie stany USA** |
    | **Obraz** | **Windows Server 2019 Datacenter** |
    | **Rozmiar** | **Rozmiar maszyny wirtualnej** **B1ls**, **Oferta** **standardu** |
    | **Nazwa użytkownika** | Nazwa użytkownika konta administratora |
    | **Hasło** | Hasło dla nazwy użytkownika konta administratora |
    | **Potwierdź hasło** | Hasło ponownie |

    ![Karta podstawy — Tworzenie maszyny wirtualnej, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-basics.png)
4. W obszarze **Sieć**Określ wartości dla elementów zgodnie z opisem w poniższej tabeli, a następnie wybierz przycisk **dalej**.

    | Element | Wartość |
    | --- | --- |
    | **Sieć wirtualna** | **TestVNet** |
    | **Podsieć** | **Frontonu** |

    ![Karta Sieć, Tworzenie maszyny wirtualnej, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-networking.png)
5. W obszarze **Zarządzanie**w obszarze **konto magazynu diagnostyki**wybierz pozycję **vnetstorage**. Jeśli konto magazynu nie pojawia się na liście, wybierz opcję **Utwórz nową**, określ **nazwę** *vnetstorage*i wybierz **przycisk OK**. Na koniec wybierz pozycję **Przegląd &nbsp; + &nbsp; Utwórz**.

    ![Karta Zarządzanie, Tworzenie maszyny wirtualnej, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-management.png)
6. W oknie **Recenzja + tworzenie**Przejrzyj informacje przeglądowe, a następnie wybierz pozycję **Utwórz**.

    ![Przejrzyj i Utwórz kartę, Utwórz maszynę wirtualną, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/create-a-virtual-machine-review-create.png)

Po utworzeniu maszyny wirtualnej zostanie wyświetlony następujący komunikat.

![Komunikat dotyczący uzupełniania wdrożenia, Tworzenie maszyny wirtualnej, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/deployment-is-complete.png)

## <a name="retrieve-private-ip-address-information-for-a-vm"></a>Pobieranie informacji o prywatnym adresie IP dla maszyny wirtualnej
Aby wyświetlić informacje o prywatnym adresie IP dla nowej maszyny wirtualnej:

1. Przejdź do [Azure Portal](https://portal.azure.com) , aby znaleźć maszynę wirtualną. Wyszukaj i wybierz pozycję **maszyny wirtualne**.

    ![Maszyny wirtualne, pole wyszukiwania, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/search-box-virtual-machines.png)

2. Wybierz nazwę nowej maszyny wirtualnej (**DNS01**).

    ![Lista maszyn wirtualnych, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/virtual-machine-list.png)

3. Wybierz pozycję **Sieć**, a następnie wybierz jeden z wymienionych interfejsów sieciowych.

    ![Interfejs sieciowy, Sieć, maszyna wirtualna, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/networking-network-interface.png)

4. Wybierz pozycję **konfiguracje adresów IP**, a następnie wybierz konfigurację protokołu IP wymienioną w tabeli.

    ![Konfiguracja protokołu IP, interfejs sieciowy, Sieć, maszyna wirtualna, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/network-interface-ip-configurations.png)

5. W obszarze **Ustawienia prywatnych adresów IP**w obszarze Sieć wirtualna/podsieć **TestVNet/frontonu** Zanotuj wartość **przypisania** (**dynamiczna** lub **statyczna**) oraz **adres IP**.

    ![Dynamiczne lub statyczne przypisanie, stare ustawienia prywatnych adresów IP, Konfiguracja protokołu IP, interfejs sieciowy, Sieć, maszyna wirtualna, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-old.png)

## <a name="add-a-static-private-ip-address-to-an-existing-vm"></a>Dodawanie statycznego prywatnego adresu IP do istniejącej maszyny wirtualnej
Aby dodać statyczny prywatny adres IP do nowej maszyny wirtualnej:

1. Na stronie Konfiguracja protokołu IP ustaw opcję przypisanie prywatnego adresu IP na **statyczny**.
2. Zmień prywatny **adres IP** na *192.168.1.101*, a następnie wybierz pozycję **Zapisz**.
   
    ![Dynamiczne lub statyczne przypisanie, nowe ustawienia prywatnych adresów IP, Konfiguracja protokołu IP, interfejs sieciowy, Sieć, maszyna wirtualna, Azure Portal](./media/virtual-networks-static-ip-arm-pportal/private-ip-address-settings-new.png)

> [!NOTE]
> Jeśli zauważysz, że po wybraniu pozycji **Zapisz** , że przypisanie jest nadal ustawione na **dynamiczną**, wpisanego adresu IP jest już w użyciu. Spróbuj użyć innego adresu IP.

## <a name="remove-a-static-private-ip-address-from-a-vm"></a>Usuwanie statycznego prywatnego adresu IP z maszyny wirtualnej
Aby usunąć statyczny prywatny adres IP z maszyny wirtualnej:

Na stronie Konfiguracja protokołu IP ustaw opcję przypisanie prywatnego adresu IP na **dynamiczną**, a następnie wybierz pozycję **Zapisz**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Ustaw adresy IP w ramach systemu operacyjnego

W ramach systemu operacyjnego maszyny wirtualnej nie należy statycznie przypisywać *prywatnego* adresu IP przypisanego do maszyny wirtualnej platformy Azure. Tylko statyczne przypisanie prywatnego adresu IP należy wykonać, na przykład podczas [przypisywania wielu adresów IP do maszyn wirtualnych](virtual-network-multiple-ip-addresses-portal.md). Jeśli ręcznie ustawisz prywatny adres IP w ramach systemu operacyjnego, upewnij się, że jest on zgodny z prywatnym adresem IP przypisanym do [interfejsu sieciowego](virtual-network-network-interface-addresses.md#change-ip-address-settings)platformy Azure. W przeciwnym razie można utracić łączność z maszyną wirtualną. Dowiedz się więcej o ustawieniach [prywatnych adresów IP](virtual-network-network-interface-addresses.md#private) .

Ponadto nigdy nie należy ręcznie przypisywać *publicznego* adresu IP przypisanego do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat zarządzania [ustawieniami adresów IP](virtual-network-network-interface-addresses.md).
