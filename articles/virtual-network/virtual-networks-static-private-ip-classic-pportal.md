---
title: Konfigurowanie prywatnych adresów IP dla maszyn wirtualnych (klasycznych) — witryna Azure portal | Dokumenty firmy Microsoft
description: Dowiedz się, jak skonfigurować prywatne adresy IP dla maszyn wirtualnych (Classic) przy użyciu witryny Azure portal.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
tags: azure-service-management
ms.assetid: b8ef8367-58b2-42df-9f26-3269980950b8
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0bc080ed41f32ae2af018e9316e67ab38c2d0650
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81449905"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Konfigurowanie prywatnych adresów IP dla maszyny wirtualnej (Classic) przy użyciu witryny Azure portal

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

W tym artykule opisano klasyczny model wdrażania. Statyczny prywatny adres IP można również [zarządzać statycznym adresem IP w modelu wdrażania Menedżera zasobów](virtual-networks-static-private-ip-arm-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Przykładowe kroki, które należy oczekiwać prostego środowiska już utworzone. Jeśli chcesz uruchomić kroki, ponieważ są one wyświetlane w tym dokumencie, najpierw skompiluj środowisko testowe opisane w [create a vnet](virtual-networks-create-vnet-classic-pportal.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Jak określić statyczny prywatny adres IP podczas tworzenia maszyny Wirtualnej
Aby utworzyć maszynę wirtualną o nazwie *DNS01* w podsieci *FrontEnd* sieci wirtualnej o nazwie *TestVNet* o statycznym prywatnym adresie IP *192.168.1.101,* wykonaj następujące kroki:

1. W przeglądarce przejdź do witryny https://portal.azure.com i, jeśli to konieczne, zaloguj się przy użyciu konta platformy Azure.
2. Wybierz **pozycję NOWE** > **centrum danych** > **systemu Windows Server 2012 R2**, zwróć uwagę, że na liście **Wybierz model wdrożenia** jest już wyświetlany **klasyczny**, a następnie wybierz pozycję **Utwórz**.
   
    ![Tworzenie maszyny wirtualnej w witrynie Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. W obszarze **Tworzenie maszyny Wirtualnej**wprowadź nazwę maszyny Wirtualnej, która ma zostać utworzona *(DNS01* w scenariuszu), konto administratora lokalnego i hasło.
   
    ![Tworzenie maszyny wirtualnej w witrynie Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Wybierz **opcjonalną konfigurację** > **sieci** > **wirtualnej,** a następnie wybierz **testvnet**. Jeśli **TestVNet** nie jest dostępny, upewnij się, że używasz centralnej lokalizacji *w stanach zjednoczonych* i utworzono środowisko testowe opisane na początku tego artykułu.
   
    ![Tworzenie maszyny wirtualnej w witrynie Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. W obszarze **Sieć**upewnij się, że aktualnie wybrana podsieć jest *FrontEnd*, a następnie wybierz **pozycję Adres IP**, w obszarze **Przypisanie adresu IP** wybierz **statyczne**, a następnie wprowadź *192.168.1.101* dla **adresu IP,** jak poniżej.
   
    ![Tworzenie maszyny wirtualnej w witrynie Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. Wybierz **przycisk OK** w obszarze **Adresy IP**wybierz pozycję **OK** w obszarze **Sieć**, a następnie wybierz pozycję **OK** w obszarze **Opcjonalna konfigura .**
7. W obszarze **Tworzenie maszyny wirtualnej**wybierz pozycję **Utwórz**. Zwróć uwagę na poniższy kafelek wyświetlany na pulpicie nawigacyjnym:
   
    ![Tworzenie maszyny wirtualnej w witrynie Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Jak pobrać statyczne prywatne informacje o adresie IP dla maszyny Wirtualnej
Aby wyświetlić statyczne prywatne informacje o adresie IP dla maszyny Wirtualnej utworzonej za pomocą powyższych kroków, wykonaj poniższe kroki.

1. W witrynie Azure portal wybierz pozycję PRZEGLĄDAJ > WSZYSTKIE**maszyny wirtualne (klasyczne)** > **DNS01** > **Wszystkie ustawienia** > **adresów IP** i zwróć uwagę na przypisanie adresu IP i adres IP, jak poniżej. **BROWSE ALL**
   
    ![Tworzenie maszyny wirtualnej w witrynie Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Jak usunąć statyczny prywatny adres IP z maszyny Wirtualnej

W obszarze **Adresy IP**wybierz pozycję **Dynamiczny** z prawej strony **przypisania adresu IP**, wybierz pozycję **Zapisz**, a następnie wybierz pozycję **Tak**, jak pokazano na poniższym rysunku:
   
    ![Create VM in Azure portal](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Jak dodać statyczny prywatny adres IP do istniejącej maszyny Wirtualnej

1. W obszarze **Adresy IP**, pokazane wcześniej, wybierz **pozycję Statyczne** po prawej stronie **przypisania adresu IP**.
2. Wpisz *192.168.1.101* dla **adresu IP**, wybierz **zapisz**, a następnie **wybierz**tak .

## <a name="set-ip-addresses-within-the-operating-system"></a>Ustawianie adresów IP w systemie operacyjnym

Zaleca się, aby nie statycznie przypisywać prywatny adres IP przypisany do maszyny wirtualnej platformy Azure w systemie operacyjnym maszyny wirtualnej, chyba że jest to konieczne. Jeśli ręcznie ustawisz prywatny adres IP w systemie operacyjnym, upewnij się, że jest to ten sam adres, co prywatny adres IP przypisany do maszyny Wirtualnej platformy Azure lub możesz utracić łączność z maszyną wirtualną. Nigdy nie należy ręcznie przypisywać publicznego adresu IP przypisanego do maszyny wirtualnej platformy Azure w systemie operacyjnym maszyny wirtualnej.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [zarezerwowanych publicznych](virtual-networks-reserved-public-ip.md) adresach IP.
* Dowiedz się więcej o publicznych adresach [IP (ILPIP) na poziomie wystąpienia.](virtual-networks-instance-level-public-ip.md)
* Zapoznaj się z [zastrzeżonymi interfejsami API odpoczynku IP](https://msdn.microsoft.com/library/azure/dn722420.aspx).

