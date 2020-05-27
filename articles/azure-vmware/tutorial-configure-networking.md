---
title: Samouczek — Konfigurowanie sieci dla Twojej chmury prywatnej VMware na platformie Azure
description: Dowiedz się, jak utworzyć i skonfigurować sieć potrzebną do wdrożenia chmury prywatnej na platformie Azure
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 5a8086f78f465f52d0f9107932c09c4690f505e8
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873862"
---
# <a name="tutorial-configure-networking-for-your-vmware-private-cloud-in-azure"></a>Samouczek: Konfigurowanie sieci dla Twojej chmury prywatnej VMWare na platformie Azure

Chmura prywatna (Automatyczna synchronizacja) z rozwiązaniem VMware Azure wymaga sieci wirtualnej. Ponieważ wersja zapoznawcza nie obsługuje lokalnego programu vCenter w okresie zapoznawczym, wymagane są dodatkowe kroki integracji ze środowiskiem lokalnym. Konfigurowanie obwodu ExpressRoute i bramy Virtual Network są również wymagane i zostaną uwzględnione w tym samouczku.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Tworzenie sieci wirtualnej
> * Tworzenie bramy Virtual Network
> * Łączenie obwodu ExpressRoute z bramą
> * Lokalizowanie adresów URL dla programu vCenter i NSX Manager

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

## <a name="create-a-virtual-network"></a>Tworzenie sieci wirtualnej

Przejdź do grupy zasobów utworzonej w [poprzednim samouczku](tutorial-create-private-cloud.md), a następnie wybierz pozycję **+ Dodaj** , aby zdefiniować nowy zasób.

W polu tekstowym Wyszukaj wpisz Portal **Marketplace** **Virtual Network**. Znajdź zasób Virtual Network i wybierz go.

Na stronie Virtual Network wybierz pozycję **Utwórz** , aby skonfigurować sieć wirtualną dla chmury prywatnej.

Na stronie **tworzenie Virtual Network** wprowadź odpowiednie szczegóły dotyczące sieci wirtualnej. w poniższej tabeli przedstawiono opis właściwości.

> [!IMPORTANT]
> Należy użyć przestrzeni adresowej, która **nie** nakłada się na przestrzeń adresową, która została użyta podczas tworzenia chmury prywatnej w poprzednim samouczku.

Na karcie **podstawowe** wprowadź nazwę sieci wirtualnej i wybierz odpowiedni region, a następnie wybierz pozycję **Dalej: adresy IP**

Na karcie **adresy IP** w obszarze **przestrzeń adresów IPv4**wprowadź przestrzeń adresową utworzoną w poprzednim samouczku.

Wybierz pozycję **+ Dodaj podsieć**, a następnie na stronie **Dodawanie podsieci** Nadaj nazwę podsieci i odpowiedni zakres adresów. Po zakończeniu wybierz pozycję **Dodaj**.

Wybieranie opcji **Recenzja + tworzenie**

:::image type="content" source="./media/tutorial-configure-networking/create-virtual-network.png" alt-text="Tworzenie sieci wirtualnej" border="true":::

Sprawdź informacje i wybierz pozycję **Utwórz**. Po zakończeniu wdrażania zobaczysz sieć wirtualną w grupie zasobów.

## <a name="create-a-virtual-network-gateway"></a>Tworzenie bramy Virtual Network

W poprzedniej sekcji została utworzona sieć wirtualna, teraz utworzysz bramę Virtual Network.

W grupie zasobów wybierz pozycję **+ Dodaj** , aby dodać nowy zasób.

W polu tekstowym **Wyszukaj w witrynie Marketplace** , **Brama sieci wirtualnej**. Znajdź zasób Virtual Network i wybierz go.

Na stronie **brama Virtual Network** wybierz pozycję **Utwórz**.

Na karcie podstawowe strony **Tworzenie bramy sieci wirtualnej** podaj wartości pól. opisy pól przedstawiono w poniższej tabeli:

| Pole | Wartość |
| --- | --- |
| **Subskrypcja** | Ta wartość jest już wypełniona z subskrypcją, do której należy Grupa zasobów. |
| **Grupa zasobów** | Ta wartość jest już wypełniona dla bieżącej grupy zasobów. Powinna to być grupa zasobów utworzona w poprzednim teście. |
| **Nazwa** | Wprowadź unikatową nazwę bramy sieci wirtualnej. |
| **Okolicy** | Wybierz lokalizację geograficzną bramy sieci wirtualnej. |
| **Typ bramy** | Wybierz pozycję **ExpressRoute**. |
| **Typ sieci VPN** | wybierz pozycję **Oparte na trasach**. |
| **Magazyn** | Pozostaw wartość domyślną: **standardowa**. |
| **Sieć wirtualna** | Wybierz utworzoną wcześniej sieć wirtualną. Jeśli nie widzisz sieci wirtualnej, upewnij się, że region bramy jest zgodny z regionem sieci wirtualnej. |
| **Zakres adresów podsieci bramy** | Ta wartość jest wypełniana po wybraniu sieci wirtualnej. Nie zmieniaj wartości domyślnej. |
| **Publiczny adres IP** | Wybierz pozycję**Utwórz nowy**. |

:::image type="content" source="./media/tutorial-configure-networking/create-virtual-network-gateway.png" alt-text="Tworzenie bramy" border="true":::

Wybierz pozycję **Recenzja + Utwórz**, na następnej stronie Sprawdź, czy szczegóły są poprawne, a następnie wybierz pozycję **Utwórz** , aby rozpocząć wdrażanie bramy sieci wirtualnej. Po zakończeniu wdrażania przejdź do następnej sekcji tego samouczka, aby połączyć połączenie ExpressRoute z siecią wirtualną zawierającą chmurę prywatną.

## <a name="connect-expressroute-to-the-virtual-network-gateway"></a>Łączenie ExpressRoute z bramą Virtual Network

Ta sekcja zawiera opis sposobu dodawania połączenia między chmurą prywatną i utworzoną bramą sieci wirtualnej.

Przejdź do chmury prywatnej utworzonej w poprzednim samouczku i wybierz pozycję **łączność** w obszarze **Zarządzanie**, wybierz kartę **ExpressRoute** .

Skopiuj klucz autoryzacji. Jeśli nie istnieje klucz autoryzacji, należy go utworzyć, aby wykonać to polecenie SELECT **+ żądanie klucza autoryzacji**

:::image type="content" source="./media/tutorial-configure-networking/request-auth-key.png" alt-text="Żądaj klucza autoryzacji" border="true":::

Przejdź do bramy Virtual Network utworzonej w poprzednim kroku, a następnie w obszarze **Ustawienia**wybierz pozycję **połączenia**. Na stronie **połączenia** wybierz pozycję **+ Dodaj**.

Na stronie **Dodawanie połączenia** podaj wartości pól. Opisy pól przedstawiono w poniższej tabeli:

| Pole | Wartość |
| --- | --- |
| **Nazwa**  | Wprowadź nazwę połączenia.  |
| **Typ połączenia**  | Wybierz pozycję **ExpressRoute**.  |
| **Zrealizuj autoryzację**  | Upewnij się, że to pole wyboru jest zaznaczone.  |
| **Brama sieci wirtualnej** | Utworzona wcześniej Brama sieci wirtualnej  |
| **Klucz autoryzacji**  | Skopiuj i Wklej klucz autoryzacji z karty ExpressRoute w grupie zasobów. |
| **Identyfikator URI obwodu równorzędnego**  | Skopiuj i wklej identyfikator ExpressRoute z karty ExpressRoute dla grupy zasobów.  |

Wybierz przycisk **OK**. Spowoduje to utworzenie połączenia między obwodem usługi ExpressRoute i siecią wirtualną.

:::image type="content" source="./media/tutorial-configure-networking/add-connection.png" alt-text="Dodawanie połączenia" border="true":::

## <a name="locate-the-urls-for-vcenter-and-nsx-manager"></a>Lokalizowanie adresów URL dla programu vCenter i NSX Manager

Aby zalogować się do programu vVenter i NSX Manager, musisz mieć adresy URL dla klienta sieci Web vCenter i lokacji Menedżera NSX-T. Aby znaleźć adresy URL:

Przejdź do swojej chmury prywatnej automatycznej synchronizacji, w obszarze **Zarządzanie**wybierz pozycję **tożsamość**. w tym miejscu znajdziesz potrzebne informacje.

:::image type="content" source="./media/tutorial-configure-networking/locate-urls.png" alt-text="Lokalizowanie adresów URL programu vCenter" border="true":::

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie sieci wirtualnej
> * Tworzenie bramy Virtual Network
> * Łączenie obwodu ExpressRoute z bramą
> * Lokalizowanie adresów URL dla programu vCenter i NSX Manager

Przejdź do następnego samouczka, aby dowiedzieć się, jak utworzyć pole skoku używane do nawiązywania połączenia ze środowiskiem, dzięki czemu możesz zarządzać chmurą prywatną lokalnie.

> [!div class="nextstepaction"]
> [Uzyskiwanie dostępu do chmury prywatnej](tutorial-access-private-cloud.md)
