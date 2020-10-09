---
title: 'Samouczek: Konfigurowanie komunikacji równorzędnej dla obwodu usługi ExpressRoute — Azure Portal'
description: W tym samouczku pokazano, jak utworzyć i udostępnić komunikację równorzędną ExpressRoute prywatną i Microsoft przy użyciu Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.openlocfilehash: cad098ed2dedc7abba57394ef1e26b9b7c87cd9c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91855475"
---
# <a name="tutorial-create-and-modify-peering-for-an-expressroute-circuit-using-the-azure-portal"></a>Samouczek: Tworzenie i modyfikowanie komunikacji równorzędnej dla obwodu usługi ExpressRoute przy użyciu Azure Portal

W tym samouczku pokazano, jak utworzyć konfigurację routingu dla Azure Resource Manager obwodu usługi ExpressRoute przy użyciu Azure Portal i zarządzać nią. Możesz również sprawdzić stan, aktualizowanie i usuwanie komunikacji równorzędnej dla obwodu usługi ExpressRoute. Jeśli chcesz użyć innej metody do pracy z obwodem, wybierz artykuł z następującej listy:

> [!div class="op_single_selector"]
> * [Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-routing-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-routing-cli.md)
> * [Publiczna Komunikacja równorzędna](about-public-peering.md)
> * [Wideo — prywatna Komunikacja równorzędna](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Wideo — Komunikacja równorzędna firmy Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-routing-classic.md)
> 

Można skonfigurować prywatną komunikację równorzędną i komunikację równorzędną firmy Microsoft dla obwodu usługi ExpressRoute (publiczna Komunikacja równorzędna Azure jest przestarzała dla nowych obwodów). Komunikację równorzędną można skonfigurować w dowolnej wybranej kolejności. Musisz jednak pamiętać, aby kończyć konfiguracje poszczególnych komunikacji równorzędnych pojedynczo. Aby uzyskać więcej informacji o domenach routingu i komunikacji równorzędnej, zobacz [ExpressRoute Routing domen](expressroute-circuit-peerings.md). Aby uzyskać informacje na temat publicznej komunikacji równorzędnej, zobacz [ExpressRoute publicznej komunikacji równorzędnej](about-public-peering.md).

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> - Konfigurowanie, aktualizowanie i usuwanie komunikacji równorzędnej firmy Microsoft dla obwodu
> - Konfigurowanie, aktualizowanie i usuwanie prywatnej komunikacji równorzędnej Azure dla obwodu

## <a name="prerequisites"></a>Wymagania wstępne

* Przed rozpoczęciem konfiguracji upewnij się, że zostały sprawdzone następujące strony:
    * [Wymagania wstępne](expressroute-prerequisites.md) 
    * [Wymagania routingu](expressroute-routing.md)
    * [Przepływy](expressroute-workflows.md)
* Musisz mieć aktywny obwód usługi ExpressRoute. Postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) i mieć obwód włączony przez dostawcę łączności przed kontynuowaniem. Aby skonfigurować komunikację równorzędną, obwód ExpressRoute musi być w stanie aprowizacji i włączony. 
* Jeśli planujesz użycie skrótu klucza wspólnego/MD5, pamiętaj, aby użyć klucza po obu stronach tunelu. Limit nie przekracza 25 znaków alfanumerycznych. Znaki specjalne nie są obsługiwane. 

Te instrukcje dotyczą tylko obwodów utworzonych przy pomocy dostawców oferujących usługi łączności warstwy 2. Jeśli używasz dostawcy usług oferującego zarządzane usługi warstwy 3 (zwykle IPVPN, takie jak MPLS), dostawca łączności konfiguruje i zarządza routingiem. 

> [!IMPORTANT]
> Obecnie nie anonsujemy komunikacji równorzędnej skonfigurowanej przez dostawców usług w portalu zarządzania usługami. Pracujemy nad tym, by wkrótce włączyć tę funkcję. Przed skonfigurowaniem komunikacji równorzędnej BGP skontaktuj się z dostawcą usług.
> 

## <a name="microsoft-peering"></a><a name="msft"></a>Komunikacja równorzędna firmy Microsoft

Ta sekcja ułatwia tworzenie, pobieranie, aktualizowanie i usuwanie konfiguracji komunikacji równorzędnej firmy Microsoft dla obwodu usługi ExpressRoute.

> [!IMPORTANT]
> Komunikacja równorzędna firmy Microsoft ze obwodów usługi ExpressRoute, które zostały skonfigurowane przed 1 sierpnia 2017, będzie miała wszystkie prefiksy usług anonsowane za pomocą komunikacji równorzędnej firmy Microsoft, nawet jeśli filtry tras nie są zdefiniowane. Komunikacja równorzędna firmy Microsoft obwodów usługi ExpressRoute, które są skonfigurowane w dniu lub po 1 sierpnia 2017, nie będzie miała żadnych prefiksów anonsowanych do momentu dołączenia do obwodu filtru tras. Aby uzyskać więcej informacji, zobacz [Konfigurowanie filtru tras dla komunikacji równorzędnej firmy Microsoft](how-to-routefilter-powershell.md).
> 
> 

### <a name="to-create-microsoft-peering"></a>Aby utworzyć komunikację równorzędną Microsoft

1. Skonfiguruj obwód usługi ExpressRoute. Sprawdź **stan dostawcy** , aby upewnić się, że obwód jest w pełni obsługiwany przez dostawcę łączności przed dalszym kontynuowaniem.

   Jeśli dostawca połączenia oferuje zarządzane usługi warstwy 3, możesz polecić dostawcę połączenia, aby włączyć komunikację równorzędną firmy Microsoft. Nie trzeba postępować zgodnie z instrukcjami podanymi w następnych sekcjach. Jeśli jednak dostawca łączności nie zarządza usługą dla Ciebie, po utworzeniu obwodu wykonaj te kroki.

   **Obwód — stan dostawcy: nie zainicjowano obsługi administracyjnej**

   [![Zrzut ekranu przedstawiający stronę przegląd obwodu demonstracyjnego ExpressRoute z czerwonym prostokątem z wyróżnioną stanem dostawcy ustawionym na "nie zainicjowano obsługi".](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m.png)](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-m-lightbox.png#lightbox)


   **Obwód — stan dostawcy: zainicjowano**

   [![Zrzut ekranu przedstawiający stronę przegląd obwodu demonstracyjnego ExpressRoute z czerwonym prostokątem z wyróżnionym stanem dostawcy ustawionym na "zainicjowano".](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m.png)](./media/expressroute-howto-routing-portal-resource-manager/provisioned-m-lightbox.png#lightbox)

2. Skonfiguruj komunikację równorzędną Microsoft dla obwodu. Przed kontynuowaniem upewnij się, że masz poniższe informacje.

   * Podsieć /30 dla połączenia podstawowego. Blok adresu musi być prawidłowym publicznym prefiksem IPv4 należącym do użytkownika i zarejestrowanym w RIR/IRR. Z tej podsieci przypiszesz pierwszy użyteczny adres IP do routera, ponieważ firma Microsoft używa drugiego adresu IP dla jego routera.
   * Podsieć /30 dla połączenia dodatkowego. Blok adresu musi być prawidłowym publicznym prefiksem IPv4 należącym do użytkownika i zarejestrowanym w RIR/IRR. Z tej podsieci przypiszesz pierwszy użyteczny adres IP do routera, ponieważ firma Microsoft używa drugiego adresu IP dla jego routera.
   * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN. Zarówno linki podstawowe, jak i pomocnicze muszą używać tego samego identyfikatora sieci VLAN.
   * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS.
   * Anonsowane prefiksy: zawiera listę wszystkich prefiksów, które planujesz anonsować za pośrednictwem sesji BGP. Akceptowane są tylko prefiksy publicznych adresów IP. Jeśli planujesz wysłanie zestawu prefiksów, możesz wysłać listę rozdzieloną przecinkami. Prefiksy te muszą być zarejestrowane na Ciebie w RIR/IRR.
   * **Opcjonalne-** Numer ASN klienta: w przypadku anonsowania prefiksów, które nie są zarejestrowane w komunikacji równorzędnej jako liczba, można określić liczbę AS, do której są zarejestrowani.
   * Nazwa rejestru routingu: możesz określić RIR/IRR, względem którego rejestrowany jest numer AS i prefiksy.
   * **Opcjonalne-** Skrót MD5, jeśli zdecydujesz się na użycie jednego z nich.
3. Możesz wybrać komunikację równorzędną, którą chcesz skonfigurować, jak pokazano w poniższym przykładzie. Zaznacz wiersz dotyczący komunikacji równorzędnej firmy Microsoft.

   [![Wybierz wiersz komunikacji równorzędnej firmy Microsoft](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m.png "Wybierz wiersz komunikacji równorzędnej firmy Microsoft")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-m-lightbox.png#lightbox)
4. Skonfiguruj komunikację równorzędną firmy Microsoft. **Zapisz** konfigurację po określeniu wszystkich parametrów. Na poniższej ilustracji przedstawiono przykładową konfigurację:

   ![Konfigurowanie komunikacji równorzędnej firmy Microsoft](./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png)

> [!IMPORTANT]
> Firma Microsoft sprawdza, czy określone "anonsowane publiczne prefiksy" i "peer ASN" (lub "ASN klienta") są przypisane do Ciebie w rejestrze routingu internetowego. Jeśli otrzymujesz publiczne prefiksy z innej jednostki i jeśli przypisanie nie zostanie zarejestrowane w rejestrze routingu, automatyczne sprawdzanie poprawności nie zostanie ukończone i będzie wymagać ręcznej walidacji. Jeśli automatyczne sprawdzanie poprawności nie powiedzie się, zostanie wyświetlony komunikat "weryfikacja nie jest konieczna". 
>
> Jeśli zobaczysz komunikat "wymagana Walidacja", Zbierz dokumenty, które pokazują, że publiczne prefiksy są przypisane do organizacji przez jednostkę wymienioną jako właściciel prefiksów w rejestrze routingu i prześlij te dokumenty w celu ręcznej weryfikacji przez otwarcie biletu pomocy technicznej. 
>

   Jeśli obwód jest w stanie "wymagana Walidacja", musisz otworzyć bilet pomocy technicznej, aby wyświetlić dowód własności prefiksów dla naszego zespołu pomocy technicznej. Możesz otworzyć bilet pomocy technicznej bezpośrednio z portalu, jak pokazano w następującym przykładzie:

   ![Wymagana weryfikacja — bilet pomocy technicznej](./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png)

5. Po pomyślnym zaakceptowaniu konfiguracji zobaczysz coś podobnego do poniższej ilustracji:

   ![Stan komunikacji równorzędnej: skonfigurowano](./media/expressroute-howto-routing-portal-resource-manager/configured-m.png "Stan komunikacji równorzędnej: skonfigurowano")

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>Aby wyświetlić szczegóły dotyczące komunikacji równorzędnej firmy Microsoft

Możesz wyświetlić właściwości komunikacji równorzędnej firmy Microsoft, wybierając wiersz dla komunikacji równorzędnej.

[![Wyświetl właściwości komunikacji równorzędnej firmy Microsoft](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png "Wyświetl właściwości")](./media/expressroute-howto-routing-portal-resource-manager/view-peering-m-lightbox.png#lightbox)
### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Aby zaktualizować konfigurację komunikacji równorzędnej firmy Microsoft

Możesz wybrać wiersz dla komunikacji równorzędnej, którą chcesz zmodyfikować, a następnie zmodyfikować właściwości komunikacji równorzędnej i zapisać modyfikacje.

![Wybierz wiersz komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/update-peering-m.png)

## <a name="azure-private-peering"></a><a name="private"></a>Prywatna komunikacja równorzędna platformy Azure

Ta sekcja ułatwia tworzenie, pobieranie, aktualizowanie i usuwanie konfiguracji prywatnej komunikacji równorzędnej Azure dla obwodu usługi ExpressRoute.

### <a name="to-create-azure-private-peering"></a>Aby utworzyć prywatną komunikację równorzędną

1. Skonfiguruj obwód usługi ExpressRoute. Zanim przejdziesz dalej, upewnij się, że obwód jest w całości obsługiwany przez dostawcę połączenia. 

   Jeśli dostawca połączenia oferuje zarządzane usługi warstwy 3, możesz polecić dostawcę połączenia, aby włączyć prywatną komunikację równorzędną Azure. Nie trzeba postępować zgodnie z instrukcjami podanymi w następnych sekcjach. Jeśli jednak dostawca połączenia nie zarządza usługą routingu, po utworzeniu obwodu przejdź do następnych kroków.

   **Obwód — stan dostawcy: nie zainicjowano obsługi administracyjnej**

   [![Zrzut ekranu przedstawiający stronę przegląd obwodu demonstracyjnego ExpressRoute z czerwonym prostokątem wyróżnienie stanu dostawcy, który jest ustawiony na "nieinicjowany".](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p.png)](./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-p-lightbox.png#lightbox)

   **Obwód — stan dostawcy: zainicjowano**

   [![Zrzut ekranu przedstawiający stronę przegląd obwodu demonstracyjnego ExpressRoute z czerwonym prostokątem z wyróżnionym stanem dostawcy ustawionym na "zainicjowano".](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p.png)](./media/expressroute-howto-routing-portal-resource-manager/provisioned-p-lightbox.png#lightbox)

2. Skonfiguruj prywatną komunikację równorzędną Azure dla obwodu. Przed przejściem do następnego kroku upewnij się, że masz następujące elementy:

   * Podsieć /30 dla połączenia podstawowego. Podsieć nie może być częścią żadnej przestrzeni adresowej zarezerwowanej dla sieci wirtualnych. Z tej podsieci przypiszesz pierwszy użyteczny adres IP do routera, ponieważ firma Microsoft używa drugiego adresu IP dla jego routera.
   * Podsieć /30 dla połączenia dodatkowego. Podsieć nie może być częścią żadnej przestrzeni adresowej zarezerwowanej dla sieci wirtualnych. Z tej podsieci przypiszesz pierwszy użyteczny adres IP do routera, ponieważ firma Microsoft używa drugiego adresu IP dla jego routera.
   * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN. Zarówno linki podstawowe, jak i pomocnicze muszą używać tego samego identyfikatora sieci VLAN.
   * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS. Możesz użyć numeru prywatnego jako dla tej komunikacji równorzędnej z wyjątkiem liczby od 65515 do 65520 włącznie.
   * Trasy należy anonsować od lokalnego routera brzegowego do platformy Azure za pośrednictwem protokołu BGP podczas konfigurowania prywatnej komunikacji równorzędnej.
   * **Opcjonalne-** Skrót MD5, jeśli zdecydujesz się na użycie jednego z nich.
3. Wybierz wiersz prywatnej komunikacji równorzędnej Azure, jak pokazano w następującym przykładzie:

   [![Wybierz wiersz prywatnej komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p.png "Wybierz wiersz prywatnej komunikacji równorzędnej")](./media/expressroute-howto-routing-portal-resource-manager/select-peering-p-lightbox.png#lightbox)
4. Skonfiguruj prywatną komunikację równorzędną. **Zapisz** konfigurację po określeniu wszystkich parametrów.

   ![Skonfiguruj prywatną komunikację równorzędną](./media/expressroute-howto-routing-portal-resource-manager/configuration-p.png)
5. Po pomyślnym zaakceptowaniu konfiguracji zobaczysz coś podobnego do poniższego przykładu:

   ![Zapisano prywatną komunikację równorzędną](./media/expressroute-howto-routing-portal-resource-manager/save-p.png)

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>Aby wyświetlić szczegóły dotyczące prywatnej komunikacji równorzędnej Azure

Możesz wyświetlić właściwości prywatnej komunikacji równorzędnej Azure, wybierając ją.

[![Wyświetl właściwości prywatnych komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/view-p.png "Wyświetl właściwości prywatnych komunikacji równorzędnej")](./media/expressroute-howto-routing-portal-resource-manager/view-p-lightbox.png#lightbox)

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Aby zaktualizować konfigurację prywatnej komunikacji równorzędnej Azure

Można wybrać wiersz dotyczący komunikacji równorzędnej i zmodyfikować jej właściwości. Po uaktualnieniu Zapisz zmiany.

![Aktualizowanie prywatnej komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/update-peering-p.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Aby usunąć komunikację równorzędną firmy Microsoft

Aby usunąć konfigurację komunikacji równorzędnej, kliknij ikonę Usuń, jak pokazano na poniższej ilustracji:

![Usuwanie komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/delete-peering-m.png)

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Aby usunąć prywatną komunikację równorzędną Azure

Konfigurację komunikacji równorzędnej można usunąć, wybierając ikonę Usuń, jak pokazano na poniższej ilustracji:

> [!WARNING]
> Przed uruchomieniem tego przykładu należy upewnić się, że wszystkie sieci wirtualne i ExpressRoute Global Reach są usuwane. 
> 
> 

![Usuwanie prywatnej komunikacji równorzędnej](./media/expressroute-howto-routing-portal-resource-manager/delete-p.png)

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu prywatnej komunikacji równorzędnej Azure Możesz utworzyć bramę ExpressRoute, aby połączyć sieć wirtualną z obwodem. 

> [!div class="nextstepaction"]
> [Konfigurowanie bramy sieci wirtualnej dla usługi ExpressRoute](expressroute-howto-add-gateway-resource-manager.md)
