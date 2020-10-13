---
title: Konfigurowanie prywatnych adresów IP dla maszyn wirtualnych (klasycznych) — Azure Portal | Microsoft Docs
description: Dowiedz się, jak skonfigurować prywatne adresy IP dla maszyn wirtualnych (klasycznych) przy użyciu Azure Portal.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
tags: azure-service-management
ms.assetid: b8ef8367-58b2-42df-9f26-3269980950b8
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 57df05918b590cedbf4af0464690ef2524f8ba79
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91650514"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Skonfiguruj prywatne adresy IP dla maszyny wirtualnej (klasycznej) przy użyciu Azure Portal

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

W tym artykule opisano klasyczny model wdrażania. Można także [zarządzać statycznym prywatnym adresem IP w modelu wdrażania Menedżer zasobów](virtual-networks-static-private-ip-arm-pportal.md).

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Przykładowe kroki, które należy wykonać, oczekują na utworzenie prostego środowiska. Jeśli chcesz uruchomić kroki w taki sposób, aby były wyświetlane w tym dokumencie, najpierw Skompiluj środowisko testowe opisane w temacie [Tworzenie sieci wirtualnej](virtual-networks-create-vnet-classic-pportal.md).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Jak określić statyczny prywatny adres IP podczas tworzenia maszyny wirtualnej
Aby utworzyć maszynę wirtualną o nazwie *DNS01* w podsieci *frontonu* sieci wirtualnej o nazwie *TestVNet* ze statycznym prywatnym adresem IP *192.168.1.101*, wykonaj następujące czynności:

1. W przeglądarce przejdź do witryny https://portal.azure.com i, jeśli to konieczne, zaloguj się przy użyciu konta platformy Azure.
2. Wybierz pozycję **Nowy**  >  **obliczeniowy**  >  **system Windows Server 2012 R2 Datacenter**, Zauważ, że lista **Wybierz model wdrażania ma** już **klasyczne**, a następnie wybierz pozycję **Utwórz**.
   
    ![Zrzut ekranu przedstawiający Azure Portal z nowym wyróżnionym kafelkiem > COMPUTE > systemu Windows Server 2012 R2 centrum danych.](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. W obszarze **Utwórz maszynę wirtualną**wprowadź nazwę maszyny wirtualnej, która ma zostać utworzona (*DNS01* w tym scenariuszu), konto administratora lokalnego i hasło.
   
    ![Zrzut ekranu pokazujący sposób tworzenia maszyny wirtualnej przez wprowadzenie nazwy maszyny wirtualnej, nazwy użytkownika administratora lokalnego i hasła.](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Wybierz opcję **opcjonalna konfiguracja**  >  **Network**  >  **Virtual Network**sieci, a następnie wybierz pozycję **TestVNet**. Jeśli **TestVNet** jest niedostępny, upewnij się, że używasz lokalizacji *środkowe stany USA* i utworzono środowisko testowe opisane na początku tego artykułu.
   
    ![Zrzut ekranu pokazujący opcjonalną konfigurację > > sieci Virtual Network > opcji TestVNet.](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. W obszarze **Sieć**upewnij się, że aktualnie wybrana podsieć to *fronton*, a następnie wybierz pozycję **adresy IP**, w obszarze **przypisanie adresu IP** wybierz pozycję **statyczny**, a następnie wprowadź *192.168.1.101* dla **adresu IP** , jak pokazano poniżej.
   
    ![Zrzut ekranu, który wyróżnia pole adresy IP, w którym jest wpisywany statyczny adres IP.](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. Wybierz pozycję **OK** w obszarze **adresy IP**, wybierz pozycję **OK** w obszarze **Sieć**, a następnie wybierz pozycję **OK** w obszarze **Konfiguracja opcjonalna**.
7. W obszarze **Utwórz maszynę wirtualną**wybierz pozycję **Utwórz**. Zwróć uwagę na kafelek poniżej wyświetlany na pulpicie nawigacyjnym:
   
    ![Zrzut ekranu pokazujący kafelek tworzenia systemu Windows Server 2012 R2 Datacenter.](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Jak pobrać informacje o statycznym prywatnym adresie IP dla maszyny wirtualnej
Aby wyświetlić informacje o statycznym prywatnym adresie IP dla maszyny wirtualnej utworzonej za pomocą powyższych kroków, wykonaj poniższe kroki.

1. W Azure Portal wybierz pozycję **Przeglądaj wszystkie**  >  **maszyny wirtualne (klasyczne)**  >  **DNS01**  >  **wszystkie ustawienia**  >  **adresy IP** i zwróć uwagę na przypisanie adresu IP i adres IP, jak pokazano poniżej.
   
    ![Tworzenie maszyny wirtualnej w Azure Portal](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Jak usunąć statyczny prywatny adres IP z maszyny wirtualnej

W obszarze **adresy IP**wybierz **pozycję dynamiczny** z prawej strony **przypisanie adresu IP**, wybierz pozycję **Zapisz**, a następnie wybierz pozycję **tak**, jak pokazano na poniższej ilustracji:
   
![Zrzut ekranu pokazujący, jak usunąć statyczny prywatny adres IP z maszyny wirtualnej, wybierając pozycję dynamiczny z prawej strony etykiety przypisywania adresów IP.](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Jak dodać statyczny prywatny adres IP do istniejącej maszyny wirtualnej

1. W obszarze **adresy IP**, pokazane wcześniej, wybierz pozycję **statyczny** po prawej stronie **przypisywania adresów IP**.
2. Wpisz *192.168.1.101* dla **adresu IP**, wybierz pozycję **Zapisz**, a następnie wybierz pozycję **tak**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Ustaw adresy IP w ramach systemu operacyjnego

Zaleca się, aby nie przypisywać statycznie prywatnego adresu IP przypisanego do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej, o ile nie jest to konieczne. Jeśli ręcznie ustawisz prywatny adres IP w ramach systemu operacyjnego, upewnij się, że jest to ten sam adres, co prywatny adres IP przypisany do maszyny wirtualnej platformy Azure, lub możesz utracić łączność z maszyną wirtualną. Nigdy nie należy ręcznie przypisywać publicznego adresu IP przypisanego do maszyny wirtualnej platformy Azure w ramach systemu operacyjnego maszyny wirtualnej.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej na temat [zarezerwowanych publicznych adresów IP](virtual-networks-reserved-public-ip.md) .
* Dowiedz się więcej o publicznych adresach [IP na poziomie wystąpienia (ILPIP)](virtual-networks-instance-level-public-ip.md) .
* Zapoznaj się z [interfejsami API REST zastrzeżony adres IP](https://msdn.microsoft.com/library/azure/dn722420.aspx).

