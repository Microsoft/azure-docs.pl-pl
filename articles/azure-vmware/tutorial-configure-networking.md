---
title: Samouczek — Konfigurowanie sieci dla Twojej chmury prywatnej VMware na platformie Azure
description: Dowiedz się, jak utworzyć i skonfigurować sieć potrzebną do wdrożenia chmury prywatnej na platformie Azure
ms.topic: tutorial
ms.date: 07/22/2020
ms.openlocfilehash: ff071e0d6eaf1552634433a76e4eade530c603b6
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/22/2020
ms.locfileid: "88750507"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>Samouczek: Konfigurowanie sieci dla Twojej chmury prywatnej VMware na platformie Azure

Chmura prywatna rozwiązania Azure VMware wymaga Virtual Network platformy Azure. Ponieważ rozwiązanie VMware platformy Azure nie obsługuje lokalnego programu vCenter w wersji zapoznawczej, wymagane są dodatkowe kroki integracji ze środowiskiem lokalnym. Skonfigurowanie obwodu ExpressRoute i bramy sieci wirtualnej jest również wymagane i jest ono omówione w tym samouczku.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie sieci wirtualnej
> * Tworzenie bramy sieci wirtualnej
> * Łączenie obwodu ExpressRoute z bramą
> * Lokalizowanie adresów URL dla programu vCenter i NSX Manager

## <a name="prerequisites"></a>Wymagania wstępne 
Przed utworzeniem sieci wirtualnej upewnij się, że utworzono [chmurę prywatną rozwiązania Azure VMware](tutorial-create-private-cloud.md). 

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Przejdź do grupy zasobów utworzonej w [samouczku tworzenie chmury prywatnej](tutorial-create-private-cloud.md) , a następnie wybierz pozycję **+ Dodaj** , aby zdefiniować nowy zasób. 

1. W polu tekstowym **Wyszukaj w witrynie Marketplace** wpisz **Virtual Network**. Znajdź zasób Virtual Network i wybierz go.

1. Na stronie **Virtual Network** wybierz pozycję **Utwórz** , aby skonfigurować sieć wirtualną dla chmury prywatnej.

1. Na stronie **tworzenie Virtual Network** wprowadź szczegóły sieci wirtualnej.

1. Na karcie **podstawowe** wprowadź nazwę sieci wirtualnej i wybierz odpowiedni region, a następnie wybierz pozycję **Dalej: adresy IP**.

1. Na karcie **adresy IP** w obszarze **przestrzeń adresów IPv4**wprowadź przestrzeń adresową utworzoną w poprzednim samouczku.

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
   | **Subskrypcja** | Ta wartość jest już wypełniona z subskrypcją, do której należy Grupa zasobów. |
   | **Grupa zasobów** | Ta wartość jest już wypełniona dla bieżącej grupy zasobów. Powinna to być grupa zasobów utworzona w poprzednim teście. |
   | **Nazwa** | Wprowadź unikatową nazwę bramy sieci wirtualnej. |
   | **Region** | Wybierz lokalizację geograficzną bramy sieci wirtualnej. |
   | **Typ bramy** | Wybierz pozycję **ExpressRoute**. |
   | **SKU** | Pozostaw wartość domyślną: **standardowa**. |
   | **Sieć wirtualna** | Wybierz utworzoną wcześniej sieć wirtualną. Jeśli nie widzisz sieci wirtualnej, upewnij się, że region bramy jest zgodny z regionem sieci wirtualnej. |
   | **Zakres adresów podsieci bramy** | Ta wartość jest wypełniana po wybraniu sieci wirtualnej. Nie zmieniaj wartości domyślnej. |
   | **Publiczny adres IP** | Wybierz pozycję**Utwórz nowy**. |

   :::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="Na karcie podstawowe strony Tworzenie bramy sieci wirtualnej podaj wartości pól, a następnie wybierz pozycję przegląd + Utwórz." border="true":::

1. Sprawdź, czy szczegóły są poprawne, a następnie wybierz pozycję **Utwórz** , aby rozpocząć wdrażanie bramy sieci wirtualnej. 
1. Po zakończeniu wdrażania przejdź do następnej sekcji, aby połączyć połączenie ExpressRoute z bramą sieci wirtualnej zawierającą chmurę prywatną rozwiązania Azure VMware.

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>Połącz ExpressRoute z bramą sieci wirtualnej

Po wdrożeniu bramy sieci wirtualnej należy dodać do niej połączenie i chmurę prywatną rozwiązania Azure VMware.

1. Przejdź do chmury prywatnej utworzonej w poprzednim samouczku i wybierz pozycję **łączność** w obszarze **Zarządzanie**, wybierz kartę **ExpressRoute** .

1. Skopiuj klucz autoryzacji. Jeśli nie istnieje klucz autoryzacji, należy go utworzyć, aby wykonać to polecenie SELECT **+ żądanie klucza autoryzacji**.

   :::image type="content" source="./media/tutorial-configure-networking/request-auth-key.png" alt-text="Skopiuj klucz autoryzacji. Jeśli nie istnieje klucz autoryzacji, należy go utworzyć, aby wykonać to polecenie select + żądanie klucza autoryzacji." border="true":::

1. Przejdź do bramy Virtual Network utworzonej w poprzednim kroku, a następnie w obszarze **Ustawienia**wybierz pozycję **połączenia**. Na stronie **połączenia** wybierz pozycję **+ Dodaj**.

1. Na stronie **Dodawanie połączenia** podaj wartości pól i wybierz **przycisk OK**. 

   | Pole | Wartość |
   | --- | --- |
   | **Nazwa**  | Wprowadź nazwę połączenia.  |
   | **Connection type** (Typ połączenia)  | Wybierz pozycję **ExpressRoute**.  |
   | **Zrealizuj autoryzację**  | Upewnij się, że to pole wyboru jest zaznaczone.  |
   | **Brama sieci wirtualnej** | Utworzona wcześniej Brama Virtual Network.  |
   | **Klucz autoryzacji**  | Skopiuj i Wklej klucz autoryzacji z karty ExpressRoute w grupie zasobów. |
   | **Identyfikator URI obwodu równorzędnego**  | Skopiuj i wklej identyfikator ExpressRoute z karty ExpressRoute dla grupy zasobów.  |

   :::image type="content" source="./media/tutorial-configure-networking/add-connection.png" alt-text="Na stronie Dodawanie połączenia podaj wartości pól i wybierz przycisk OK." border="true":::

Połączenie między obwodem usługi ExpressRoute a Virtual Network zostanie utworzone.



## <a name="locate-the-urls-for-vcenter-and-nsx-manager"></a>Lokalizowanie adresów URL dla programu vCenter i NSX Manager

Aby zalogować się do programu vCenter i NSX Manager, musisz mieć adresy URL dla klienta sieci Web vCenter i lokacji Menedżera NSX-T. 

Przejdź do chmury prywatnej rozwiązania Azure VMware, w obszarze **Zarządzanie**wybierz pozycję **tożsamość**. w tym miejscu znajdziesz potrzebne informacje.

:::image type="content" source="./media/tutorial-configure-networking/locate-urls.png" alt-text="Przejdź do chmury prywatnej rozwiązania Azure VMware, w obszarze Zarządzanie wybierz pozycję tożsamość. w tym miejscu znajdziesz potrzebne informacje." border="true":::

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie sieci wirtualnej
> * Tworzenie bramy sieci wirtualnej
> * Łączenie obwodu ExpressRoute z bramą
> * Lokalizowanie adresów URL dla programu vCenter i NSX Manager

Przejdź do następnego samouczka, aby dowiedzieć się, jak utworzyć pole skoku używane do nawiązywania połączenia ze środowiskiem, dzięki czemu możesz zarządzać chmurą prywatną lokalnie.

> [!div class="nextstepaction"]
> [Uzyskiwanie dostępu do chmury prywatnej](tutorial-access-private-cloud.md)
