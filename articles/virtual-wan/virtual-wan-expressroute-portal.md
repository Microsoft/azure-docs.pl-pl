---
title: Samouczek — Tworzenie połączeń ExpressRoute przy użyciu wirtualnej sieci WAN platformy Azure
description: W tym samouczku pokazano, jak używać usługi Azure Virtual WAN do tworzenia połączeń usługi ExpressRoute z platformą Azure i środowiskami lokalnymi.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: tutorial
ms.date: 10/07/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to connect my corporate on-premises network(s) to my VNets using Virtual WAN and ExpressRoute.
ms.openlocfilehash: 1694c8a602315ab5f0ffa5d4e0bc218f03220c30
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91821892"
---
# <a name="tutorial-create-an-expressroute-association-using-azure-virtual-wan"></a>Samouczek: Tworzenie skojarzenia ExpressRoute przy użyciu wirtualnej sieci WAN platformy Azure

W tym samouczku pokazano, jak używać wirtualnej sieci WAN do łączenia się z zasobami na platformie Azure za pośrednictwem obwodu usługi ExpressRoute. Aby uzyskać więcej informacji na temat wirtualnych sieci WAN i wirtualnych zasobów sieci WAN, zobacz [Omówienie wirtualnej sieci WAN](virtual-wan-about.md).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie wirtualnej sieci WAN
> * Tworzenie centrum i bramy
> * Łączenie sieci wirtualnej z koncentratorem
> * Łączenie obwodu z bramą centrum
> * Testowanie łączności
> * Zmiana rozmiaru bramy
> * Anonsowanie trasy domyślnej

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem konfiguracji sprawdź, czy są spełnione następujące kryteria:

* Masz sieć wirtualną, z którą chcesz nawiązać połączenie. Sprawdź, czy żadna z podsieci sieci lokalnych nie nakłada się na sieci wirtualne, z którymi chcesz nawiązać połączenie. Aby utworzyć sieć wirtualną w Azure Portal, zobacz [Przewodnik Szybki Start](../virtual-network/quick-create-portal.md).

* Twoja sieć wirtualna nie ma żadnych bram sieci wirtualnej. Jeśli sieć wirtualna ma bramę (VPN lub ExpressRoute), należy usunąć wszystkie bramy. Ta konfiguracja wymaga, aby w zamian były połączone sieci wirtualne z bramą wirtualnego centrum sieci WAN.

* Uzyskaj zakres adresów IP w regionie koncentratora. Centrum jest siecią wirtualną, która jest tworzona i używana przez wirtualną sieć WAN. Zakres adresów określony dla centrum nie może pokrywać się z żadną z istniejących sieci wirtualnych, z którymi się łączysz. Nie może ona również nakładać się na zakresy adresów, które są połączone z lokalnymi. Jeśli nie znasz zakresów adresów IP znajdujących się w konfiguracji sieci lokalnej, koordynuj się z osobą, która może podać te szczegóły.

* Obwód ExpressRoute musi być obwodem w warstwie Premium/Standard, aby można było połączyć się z bramą centrum.

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Tworzenie wirtualnej sieci WAN

Przejdź w przeglądarce do witryny [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.

1. Przejdź do strony wirtualna sieć WAN. W portalu kliknij pozycję **+Utwórz zasób**. W polu wyszukiwania wpisz **wirtualną sieć WAN** i wybierz pozycję Wprowadź.
2. Z wyników wybierz pozycję **wirtualna sieć WAN** . Na stronie wirtualna sieć WAN kliknij przycisk **Utwórz** , aby otworzyć stronę tworzenie sieci WAN.
3. Na stronie **Tworzenie sieci WAN** na karcie **podstawowe** wypełnij następujące pola:

   ![Tworzenie sieci WAN](./media/virtual-wan-expressroute-portal/createwan.png)

   * **Subskrypcja** — wybierz subskrypcję, która ma być używana.
   * **Grupa zasobów**— utwórz nową lub użyj istniejącej.
   * **Lokalizacja grupy zasobów** — wybierz lokalizację zasobu z listy rozwijanej. Sieć WAN to zasób globalny i nie znajduje się w konkretnym regionie. Konieczne jest jednak wybranie regionu, aby łatwiej zarządzać utworzonym zasobem sieci WAN i go lokalizować.
   * **Nazwa** — wpisz nazwę, która ma zostać wywołana w sieci WAN.
   * **Typ** — wybierz pozycję **Standardowy**. Nie można utworzyć bramy ExpressRoute przy użyciu podstawowej jednostki SKU.
4. Po zakończeniu wypełniania pól wybierz pozycję **Przegląd + Utwórz**.
5. Po zakończeniu walidacji wybierz pozycję **Utwórz** , aby utworzyć wirtualną sieć WAN.

## <a name="create-a-virtual-hub-and-gateway"></a><a name="hub"></a>Tworzenie koncentratora wirtualnego i bramy

Koncentrator wirtualny jest siecią wirtualną, która jest tworzona i używana przez wirtualną sieć WAN. Może zawierać różne bramy, takie jak sieci VPN i ExpressRoute. W tej sekcji utworzysz bramę ExpressRoute dla koncentratora wirtualnego. Bramę można utworzyć podczas [tworzenia nowego koncentratora wirtualnego](#newhub)lub można utworzyć bramę w [istniejącym centrum](#existinghub) , edytując ją. 

Bramy ExpressRoute są inicjowane w jednostkach 2 GB/s. 1 jednostka skalowania = 2 GB/s z obsługą do 10 jednostek skalowania = 20 GB/s. Aby można było w pełni utworzyć koncentrator wirtualny i Brama, zajmie około 30 minut.

### <a name="to-create-a-new-virtual-hub-and-a-gateway"></a><a name="newhub"></a>Aby utworzyć nowe centrum wirtualne i bramę

Utwórz nowe centrum wirtualne. Po utworzeniu centrum zostanie naliczona opłata za centrum, nawet jeśli nie dołączysz żadnych witryn.

[!INCLUDE [Create a hub](../../includes/virtual-wan-tutorial-er-hub-include.md)]

### <a name="to-create-a-gateway-in-an-existing-hub"></a><a name="existinghub"></a>Aby utworzyć bramę w istniejącym centrum

Możesz również utworzyć bramę w istniejącym centrum, edytując ją.

1. Przejdź do koncentratora wirtualnego, który chcesz edytować, i zaznacz go.
2. Na stronie **Edytuj koncentrator wirtualny** zaznacz pole wyboru **Uwzględnij bramę ExpressRoute**.
3. Wybierz pozycję **Potwierdź** , aby potwierdzić zmiany. Do pełnego utworzenia zasobów centrum i koncentratora trwa około 30 minut.

   ![istniejące centrum](./media/virtual-wan-expressroute-portal/edithub.png "Edytowanie centrum")

### <a name="to-view-a-gateway"></a>Aby wyświetlić bramę

Po utworzeniu bramy ExpressRoute można wyświetlić szczegóły bramy. Przejdź do centrum, wybierz pozycję **ExpressRoute**, a następnie sprawdź bramę.

![Wyświetl bramę](./media/virtual-wan-expressroute-portal/viewgw.png "Wyświetl bramę")

## <a name="connect-your-vnet-to-the-hub"></a><a name="connectvnet"></a>Łączenie sieci wirtualnej z centrum

W tej sekcji utworzysz połączenie komunikacji równorzędnej między centrum i siecią wirtualną. Powtórz te czynności dla każdej sieci wirtualnej, z którą chcesz się połączyć.

1. Na stronie usługi Virtual WAN kliknij pozycję **Połączenie sieci wirtualnej**.
2. Na stronie połączenia sieci wirtualnej kliknij polecenie **+ Dodaj połączenie**.
3. Na stronie **Dodaj połączenie** wypełnij następujące pola:

    * **Nazwa połączenia** — nazwij połączenie.
    * **Koncentratory** — wybierz koncentrator, z którym chcesz skojarzyć to połączenie.
    * **Subskrypcja** — sprawdź, czy wybrano właściwą subskrypcję.
    * **Sieć wirtualna** — wybierz sieć wirtualną, którą chcesz połączyć z tym koncentratorem. Sieć wirtualna nie może mieć już istniejącej bramy sieci wirtualnej (ani sieci VPN ani ExpressRoute).

## <a name="connect-your-circuit-to-the-hub-gateway"></a><a name="connectcircuit"></a>Łączenie obwodu z bramą centrum

Po utworzeniu bramy można podłączyć do niej [obwód ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md) . Obwody usługi ExpressRoute w warstwie Standardowa lub Premium, które znajdują się w lokalizacji obsługiwanej przez usługi ExpressRoute Global Reach, mogą łączyć się z wirtualną bramą ExpressRoute sieci WAN i korzystać ze wszystkich możliwości tranzytu wirtualnego sieci WAN (VPN-to-VPN, VPN i ExpressRoute). Obwody usługi ExpressRoute w warstwach Standardowa i Premium, które znajdują się w lokalizacjach innych niż Global Reach, mogą łączyć się z zasobami platformy Azure, ale nie będą mogły korzystać z możliwości tranzytu wirtualnej sieci WAN. ExpressRoute Local nie jest obsługiwany w przypadku wirtualnej sieci WAN platformy Azure.

### <a name="to-connect-the-circuit-to-the-hub-gateway"></a>Aby połączyć obwód z bramą centrum

W portalu przejdź do strony **Virtual Hub-> łączności > ExpressRoute** . Jeśli masz dostęp do obwodu usługi ExpressRoute, na liście obwodów zostanie wyświetlony obwód, którego chcesz użyć. Jeśli nie widzisz żadnych obwodów, ale zostały one dostarczone z kluczem autoryzacji i identyfikatorem URI obwodu równorzędnego, możesz zrealizować obwód i połączyć go. Zapoznaj się z tematem [, aby nawiązać połączenie przez zrealizowanie klucza autoryzacji](#authkey).

1. Wybierz obwód.
2. Wybierz pozycję **Połącz obwody**.

   ![Połącz obwody](./media/virtual-wan-expressroute-portal/cktconnect.png "Połącz obwody")

### <a name="to-connect-by-redeeming-an-authorization-key"></a><a name="authkey"></a>Aby nawiązać połączenie przez zrealizowanie klucza autoryzacji

Użyj klucza autoryzacji i identyfikatora URI obwodu, który podano w celu nawiązania połączenia.

1. Na stronie ExpressRoute kliknij pozycję **+ Zrealizuj klucz autoryzacji**

   ![Zrzut ekranu przedstawia ExpressRoute dla koncentratora wirtualnego z wybranym kluczem autoryzacji Zrealizuj.](./media/virtual-wan-expressroute-portal/redeem.png "Zrealizuj")
2. Na stronie klucz autoryzacji Zrealizuj wprowadź wartości.

   ![wartości klucza wykupu](./media/virtual-wan-expressroute-portal/redeemkey2.png "wartości klucza wykupu")
3. Wybierz pozycję **Dodaj** , aby dodać klucz.
4. Wyświetl obwód. Obwód zrealizowany wyświetla tylko nazwę (bez typu, dostawcy i innych informacji), ponieważ znajduje się w innej subskrypcji niż użytkownik.

## <a name="to-test-connectivity"></a>Aby przetestować łączność

Po nawiązaniu połączenia z obwodem stan połączenia centrum będzie wskazywał "to centrum", co oznacza, że połączenie jest nawiązywane z bramą ExpressRoute Hub. Zaczekaj około 5 minut, zanim przetestujesz łączność z klienta w ramach obwodu usługi ExpressRoute, na przykład maszynę wirtualną w sieci wirtualnej, która została utworzona wcześniej.

Jeśli lokacje są połączone z bramą sieci VPN wirtualnej sieci WAN w tym samym centrum co Brama ExpressRoute, można mieć połączenie dwukierunkowe między sieciami VPN i ExpressRoute punkt końcowy. Routing dynamiczny (BGP) jest obsługiwany. Numer ASN bram w centrum jest stały i nie można go edytować w tym momencie.

## <a name="to-change-the-size-of-a-gateway"></a>Aby zmienić rozmiar bramy

Jeśli chcesz zmienić rozmiar bramy usługi ExpressRoute, Znajdź bramę ExpressRoute w centrum i wybierz jednostki skalowania z listy rozwijanej. Zapisz zmiany. Aktualizacja bramy centrum będzie trwać około 30 minut.

![Zmień rozmiar bramy](./media/virtual-wan-expressroute-portal/changescale.png "Zmień rozmiar bramy")

## <a name="to-advertise-default-route-00000-to-endpoints"></a>Anonsowanie domyślnej trasy 0.0.0.0/0 do punktów końcowych

Jeśli chcesz, aby usługa Azure Virtual Hub anonsuje domyślną trasę 0.0.0.0/0 do punktów końcowych ExpressRoute, musisz włączyć opcję "Propaguj trasę domyślną".

1. Wybierz **obwód >...-> Edytuj połączenie**.

   ![Edytuj połączenie](./media/virtual-wan-expressroute-portal/defaultroute1.png "Edytuj połączenie")

2. Wybierz pozycję **Włącz** , aby propagować trasę domyślną.

   ![Propaguj trasę domyślną](./media/virtual-wan-expressroute-portal/defaultroute2.png "Propaguj trasę domyślną")

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat usługi Virtual WAN, zobacz stronę [Omówienie usługi Virtual WAN](virtual-wan-about.md).
