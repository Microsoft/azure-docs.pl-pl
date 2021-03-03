---
title: Samouczek — Konfigurowanie sieci dla Twojej chmury prywatnej VMware na platformie Azure
description: Dowiedz się, jak utworzyć i skonfigurować sieć potrzebną do wdrożenia chmury prywatnej na platformie Azure
ms.topic: tutorial
ms.date: 02/23/2021
ms.openlocfilehash: b3afdffa127c23a3be3d247f12acc31604370ef6
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101738450"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>Samouczek: Konfigurowanie sieci dla Twojej chmury prywatnej VMware na platformie Azure

Chmura prywatna rozwiązania Azure VMware wymaga Virtual Network platformy Azure. Ponieważ rozwiązanie Azure VMware nie obsługuje lokalnego programu vCenter, wymagane są dodatkowe kroki integracji ze środowiskiem lokalnym. Skonfigurowanie obwodu ExpressRoute i bramy sieci wirtualnej również jest wymagane.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Tworzenie sieci wirtualnej
> * Tworzenie bramy sieci wirtualnej
> * Łączenie obwodu ExpressRoute z bramą
> * Lokalizowanie adresów URL dla programu vCenter i NSX Manager

## <a name="prerequisites"></a>Wymagania wstępne 
Sieć wirtualna, w której utworzono [chmurę prywatną rozwiązania Azure VMware](tutorial-create-private-cloud.md). 

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Przejdź do grupy zasobów utworzonej w [samouczku tworzenie chmury prywatnej](tutorial-create-private-cloud.md) , a następnie wybierz pozycję **+ Dodaj** , aby zdefiniować nowy zasób. 

1. W polu tekstowym **Wyszukaj w witrynie Marketplace** wpisz **Virtual Network**. Znajdź zasób Virtual Network i wybierz go.

1. Na stronie **Virtual Network** wybierz pozycję **Utwórz** , aby skonfigurować sieć wirtualną dla chmury prywatnej.

1. Na stronie **tworzenie Virtual Network** wprowadź szczegóły sieci wirtualnej.

1. Na karcie **podstawowe** wprowadź nazwę sieci wirtualnej, wybierz odpowiedni region, a **następnie wybierz Dalej: adresy IP**.

1. Na karcie **adresy IP** w obszarze **przestrzeń adresów IPv4** wprowadź przestrzeń adresową utworzoną w poprzednim samouczku.

   > [!IMPORTANT]
   > Należy użyć przestrzeni adresowej, która **nie** nakłada się na przestrzeń adresową, która została użyta podczas tworzenia chmury prywatnej w poprzednim samouczku.

1. Wybierz pozycję **+ Dodaj podsieć**, a następnie na stronie **Dodawanie podsieci** Nadaj nazwę podsieci i odpowiedni zakres adresów. Po zakończeniu wybierz pozycję **Dodaj**.

1. Wybierz pozycję **Przejrzyj i utwórz**.

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="Wybierz pozycję Recenzja + Utwórz." border="true":::

1. Sprawdź informacje i wybierz pozycję **Utwórz**. Po zakończeniu wdrażania zobaczysz sieć wirtualną w grupie zasobów.

## <a name="create-a-virtual-network-gateway"></a>Tworzenie bramy sieci wirtualnej

Po utworzeniu sieci wirtualnej należy utworzyć bramę sieci wirtualnej.

1. W grupie zasobów wybierz pozycję **+ Dodaj** , aby dodać nowy zasób.

1. W polu tekstowym **Wyszukaj w witrynie Marketplace** , **Brama sieci wirtualnej**. Znajdź zasób Virtual Network i wybierz go.

1. Na stronie **brama Virtual Network** wybierz pozycję **Utwórz**.

1. Na karcie podstawowe strony **Tworzenie bramy sieci wirtualnej** podaj wartości pól, a następnie wybierz pozycję **Przegląd + Utwórz**. 

   | Pole | Wartość |
   | --- | --- |
   | **Subskrypcja** | Wstępnie uzupełniona wartość na subskrypcję, do której należy Grupa zasobów. |
   | **Grupa zasobów** | Wstępnie wypełniona wartość dla bieżącej grupy zasobów. Wartość powinna być grupą zasobów utworzoną w poprzednim teście. |
   | **Nazwa** | Wprowadź unikatową nazwę bramy sieci wirtualnej. |
   | **Region** | Wybierz lokalizację geograficzną bramy sieci wirtualnej. |
   | **Typ bramy** | Wybierz pozycję **ExpressRoute**. |
   | **SKU** | Pozostaw wartość domyślną: **standardowa**. |
   | **Sieć wirtualna** | Wybierz utworzoną wcześniej sieć wirtualną. Jeśli nie widzisz sieci wirtualnej, upewnij się, że region bramy jest zgodny z regionem sieci wirtualnej. |
   | **Zakres adresów podsieci bramy** | Ta wartość jest wypełniana po wybraniu sieci wirtualnej. Nie zmieniaj wartości domyślnej. |
   | **Publiczny adres IP** | Wybierz pozycję **Utwórz nowy**. |

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="Podaj wartości dla pól, a następnie wybierz pozycję Recenzja + Utwórz." border="true":::

1. Sprawdź, czy szczegóły są poprawne, a następnie wybierz pozycję **Utwórz** , aby rozpocząć wdrażanie bramy sieci wirtualnej. 
1. Po zakończeniu wdrażania przejdź do następnej sekcji, aby połączyć połączenie ExpressRoute z bramą sieci wirtualnej zawierającą chmurę prywatną rozwiązania Azure VMware.

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>Połącz ExpressRoute z bramą sieci wirtualnej

Po wdrożeniu bramy sieci wirtualnej należy dodać do niej połączenie i chmurę prywatną rozwiązania Azure VMware.

[!INCLUDE [connect-expressroute-to-vnet](includes/connect-expressroute-vnet.md)]


## <a name="locate-the-urls-for-vcenter-and-nsx-manager"></a>Lokalizowanie adresów URL dla programu vCenter i NSX Manager

Aby zalogować się do programu vCenter i NSX Manager, musisz mieć adresy URL dla klienta sieci Web vCenter i lokacji Menedżera NSX-T. 

Przejdź do chmury prywatnej rozwiązania Azure VMware, w obszarze **Zarządzanie** wybierz pozycję **tożsamość**. w tym miejscu znajdziesz potrzebne informacje.

:::image type="content" source="./media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Zrzut ekranu przedstawiający poświadczenia programu vCenter i NSX-T oraz adresy URL klienta sieci Web." border="true":::

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie sieci wirtualnej
> * Tworzenie bramy sieci wirtualnej
> * Łączenie obwodu ExpressRoute z bramą
> * Lokalizowanie adresów URL dla programu vCenter i NSX Manager

Przejdź do następnego samouczka, aby dowiedzieć się, jak utworzyć segmenty sieci NSX-T, które są używane dla maszyn wirtualnych w programie vCenter.

> [!div class="nextstepaction"]
> [Tworzenie segmentu sieci NSX-T](tutorial-nsx-t-network-segment.md)