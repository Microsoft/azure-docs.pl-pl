---
title: 'Samouczek: Konfigurowanie komunikacji równorzędnej dla obwodu usługi ExpressRoute — Azure Portal'
description: W tym samouczku pokazano, jak utworzyć i udostępnić komunikację równorzędną ExpressRoute prywatną i Microsoft przy użyciu Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 01/11/2021
ms.author: duau
ms.openlocfilehash: 11a4798c0cb3bc010bbdbae1fcb709951c67781a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101721907"
---
# <a name="tutorial-create-and-modify-peering-for-an-expressroute-circuit-using-the-azure-portal"></a>Samouczek: Tworzenie i modyfikowanie komunikacji równorzędnej dla obwodu usługi ExpressRoute przy użyciu Azure Portal

W tym samouczku pokazano, jak utworzyć konfigurację routingu dla Azure Resource Manager obwodu usługi ExpressRoute przy użyciu Azure Portal i zarządzać nią. Możesz również sprawdzić stan, aktualizowanie i usuwanie komunikacji równorzędnej dla obwodu usługi ExpressRoute. Jeśli chcesz użyć innej metody do pracy z obwodem, wybierz artykuł z następującej listy:

> [!div class="op_single_selector"]
> * [Witryna Azure Portal](expressroute-howto-routing-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-routing-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-routing-cli.md)
> * [Publiczna komunikacja równorzędna](about-public-peering.md)
> * [Wideo — prywatna Komunikacja równorzędna](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Wideo — Komunikacja równorzędna firmy Microsoft](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-routing-classic.md)
> 

Można skonfigurować prywatną komunikację równorzędną i komunikację równorzędną firmy Microsoft dla obwodu usługi ExpressRoute (publiczna Komunikacja równorzędna Azure jest przestarzała dla nowych obwodów). Komunikację równorzędną można skonfigurować w dowolnej wybranej kolejności. Musisz jednak pamiętać, aby kończyć konfiguracje poszczególnych komunikacji równorzędnych pojedynczo. Aby uzyskać więcej informacji o domenach routingu i komunikacji równorzędnej, zobacz [ExpressRoute Routing domen](expressroute-circuit-peerings.md). Aby uzyskać informacje na temat publicznej komunikacji równorzędnej, zobacz [ExpressRoute publicznej komunikacji równorzędnej](about-public-peering.md).

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:
> [!div class="checklist"]
> - Konfigurowanie, aktualizowanie i usuwanie komunikacji równorzędnej firmy Microsoft dla obwodu
> - Konfigurowanie, aktualizowanie i usuwanie prywatnej komunikacji równorzędnej Azure dla obwodu

## <a name="prerequisites"></a>Wymagania wstępne

* Przed rozpoczęciem konfiguracji upewnij się, że zostały sprawdzone następujące strony:
    * [Wymagania wstępne](expressroute-prerequisites.md) 
    * [Wymagania routingu](expressroute-routing.md)
    * [Przepływy pracy](expressroute-workflows.md)
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

    :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/not-provisioned.png" alt-text="Zrzut ekranu przedstawiający stronę przegląd obwodu demonstracyjnego ExpressRoute z czerwonym prostokątem z wyróżnioną stanem dostawcy ustawionym na nieinicjowany":::

   **Obwód — stan dostawcy: zainicjowano**

    :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/provisioned.png" alt-text="Zrzut ekranu przedstawiający stronę przegląd obwodu demonstracyjnego ExpressRoute z czerwonym prostokątem z wyróżnionym stanem dostawcy ustawionym na wartość aprowizacji":::

2. Skonfiguruj komunikację równorzędną Microsoft dla obwodu. Przed kontynuowaniem upewnij się, że masz poniższe informacje.

   * Para podsieci należących do użytkownika i zarejestrowana w RIR/IRR. Jedna podsieć zostanie użyta dla linku podstawowego, a druga zostanie użyta dla linku pomocniczego. Z każdej z tych podsieci zostanie przypisany pierwszy, przydatny adres IP do routera, ponieważ firma Microsoft używa drugiego adresu IP do użycia dla jego routera. Dla tej pary podsieci dostępne są trzy opcje:
       * IPv4: dwie podsieci/30. Muszą to być prawidłowe publiczne prefiksy IPv4.
       * IPv6: dwie podsieci/126. Muszą to być prawidłowe publiczne Prefiksy IPv6.
       * Oba: dwie podsieci/30 i dwie podsieci/126.
   * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN. W przypadku łączy podstawowych i pomocniczych należy użyć tego samego identyfikatora sieci VLAN.
   * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS.
   * Anonsowane prefiksy: zawiera listę wszystkich prefiksów, które planujesz anonsować za pośrednictwem sesji BGP. Akceptowane są tylko prefiksy publicznych adresów IP. Jeśli planujesz wysłanie zestawu prefiksów, możesz wysłać listę rozdzieloną przecinkami. Prefiksy te muszą być zarejestrowane na Ciebie w RIR/IRR.
   * **Opcjonalne-** Numer ASN klienta: w przypadku anonsowania prefiksów, które nie są zarejestrowane w komunikacji równorzędnej jako liczba, można określić liczbę AS, do której są zarejestrowani.
   * Nazwa rejestru routingu: możesz określić RIR/IRR, względem którego rejestrowany jest numer AS i prefiksy.
   * **Opcjonalne-** Skrót MD5, jeśli zdecydujesz się na użycie jednego z nich.
1. Możesz wybrać komunikację równorzędną, którą chcesz skonfigurować, jak pokazano w poniższym przykładzie. Zaznacz wiersz dotyczący komunikacji równorzędnej firmy Microsoft.

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/select-microsoft-peering.png" alt-text="Wybierz wiersz komunikacji równorzędnej firmy Microsoft":::

4. Skonfiguruj komunikację równorzędną firmy Microsoft. **Zapisz** konfigurację po określeniu wszystkich parametrów. Na poniższej ilustracji przedstawiono przykładową konfigurację:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/configuration-m-validation-needed.png" alt-text="Wymagana jest weryfikacja komunikacji równorzędnej firmy Microsoft":::

> [!IMPORTANT]
> Firma Microsoft sprawdza, czy określone "anonsowane publiczne prefiksy" i "peer ASN" (lub "ASN klienta") są przypisane do Ciebie w rejestrze routingu internetowego. Jeśli otrzymujesz publiczne prefiksy z innej jednostki i jeśli przypisanie nie zostanie zarejestrowane w rejestrze routingu, automatyczne sprawdzanie poprawności nie zostanie ukończone i będzie wymagać ręcznej walidacji. Jeśli automatyczne sprawdzanie poprawności nie powiedzie się, zostanie wyświetlony komunikat "weryfikacja nie jest konieczna". 
>
> Jeśli zobaczysz komunikat "wymagana Walidacja", Zbierz dokumenty, które pokazują, że publiczne prefiksy są przypisane do organizacji przez jednostkę wymienioną jako właściciel prefiksów w rejestrze routingu i prześlij te dokumenty w celu ręcznej weryfikacji przez otwarcie biletu pomocy technicznej. 
>

   Jeśli obwód jest w stanie "wymagana Walidacja", musisz otworzyć bilet pomocy technicznej, aby wyświetlić dowód własności prefiksów dla naszego zespołu pomocy technicznej. Możesz otworzyć bilet pomocy technicznej bezpośrednio z portalu, jak pokazano w następującym przykładzie:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/ticket-portal-m.png" alt-text="Wymagana weryfikacja — bilet pomocy technicznej":::

5. Po pomyślnym zaakceptowaniu konfiguracji zobaczysz coś podobnego do poniższej ilustracji:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/microsoft-peering-validation-configured.png" alt-text="Stan komunikacji równorzędnej: skonfigurowano":::

### <a name="to-view-microsoft-peering-details"></a><a name="getmsft"></a>Aby wyświetlić szczegóły dotyczące komunikacji równorzędnej firmy Microsoft

Możesz wyświetlić właściwości komunikacji równorzędnej firmy Microsoft, wybierając wiersz dla komunikacji równorzędnej.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png" alt-text="Wyświetl właściwości komunikacji równorzędnej firmy Microsoft":::

### <a name="to-update-microsoft-peering-configuration"></a><a name="updatemsft"></a>Aby zaktualizować konfigurację komunikacji równorzędnej firmy Microsoft

Możesz wybrać wiersz dla komunikacji równorzędnej, którą chcesz zmodyfikować, a następnie zmodyfikować właściwości komunikacji równorzędnej i zapisać modyfikacje.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/configuration-m.png" alt-text="Wybierz wiersz komunikacji równorzędnej":::

## <a name="azure-private-peering"></a><a name="private"></a>Prywatna komunikacja równorzędna Azure

Ta sekcja ułatwia tworzenie, pobieranie, aktualizowanie i usuwanie konfiguracji prywatnej komunikacji równorzędnej Azure dla obwodu usługi ExpressRoute.

> [!IMPORTANT]
> Obsługa protokołu IPv6 dla prywatnej komunikacji równorzędnej jest obecnie dostępna w **publicznej wersji zapoznawczej**. Jeśli chcesz połączyć sieć wirtualną z obwodem ExpressRoute przy użyciu skonfigurowanej prywatnej komunikacji równorzędnej IPv6, upewnij się, że sieć wirtualna jest podwójnym stosem, i postępuj zgodnie z wytycznymi opisanymi [tutaj](https://docs.microsoft.com/azure/virtual-network/ipv6-overview).
> 
> 

### <a name="to-create-azure-private-peering"></a>Aby utworzyć prywatną komunikację równorzędną

1. Skonfiguruj obwód usługi ExpressRoute. Zanim przejdziesz dalej, upewnij się, że obwód jest w całości obsługiwany przez dostawcę połączenia. 

   Jeśli dostawca połączenia oferuje zarządzane usługi warstwy 3, możesz polecić dostawcę połączenia, aby włączyć prywatną komunikację równorzędną Azure. Nie trzeba postępować zgodnie z instrukcjami podanymi w następnych sekcjach. Jeśli jednak dostawca połączenia nie zarządza usługą routingu, po utworzeniu obwodu przejdź do następnych kroków.

   **Obwód — stan dostawcy: nie zainicjowano obsługi administracyjnej**

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/not-provisioned-private.png" alt-text="Zrzut ekranu przedstawiający stronę przegląd obwodu demonstracyjnego ExpressRoute z czerwonym prostokątem z wyróżnionym stanem dostawcy ustawionym na nieinicjowany":::

   **Obwód — stan dostawcy: zainicjowano**

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/provisioned-private-peering.png" alt-text="Zrzut ekranu przedstawiający stronę przegląd obwodu demonstracyjnego ExpressRoute z czerwonym prostokątem z wyróżnionym stanem dostawcy ustawionym na wartość aprowizacji":::

2. Skonfiguruj prywatną komunikację równorzędną Azure dla obwodu. Przed przejściem do następnego kroku upewnij się, że masz następujące elementy:

   * Para podsieci, które nie są częścią przestrzeni adresowej zarezerwowanej dla sieci wirtualnych. Jedna podsieć zostanie użyta dla linku podstawowego, a druga zostanie użyta dla linku pomocniczego. Z każdej z tych podsieci zostanie przypisany pierwszy, przydatny adres IP do routera, ponieważ firma Microsoft używa drugiego adresu IP do użycia dla jego routera. Dla tej pary podsieci dostępne są trzy opcje:
       * IPv4: dwie podsieci/30.
       * IPv6: dwie podsieci/126.
       * Oba: dwie podsieci/30 i dwie podsieci/126.
   * Prawidłowy identyfikator sieci VLAN do ustanowienia tej komunikacji równorzędnej jest włączony. Upewnij się, że żadna inna komunikacja równorzędna w obwodzie nie używa tego samego identyfikatora VLAN. W przypadku łączy podstawowych i pomocniczych należy użyć tego samego identyfikatora sieci VLAN.
   * Numer AS do komunikacji równorzędnej. Możesz używać 2-bajtowych i 4-bajtowych numerów AS. Możesz użyć numeru prywatnego jako dla tej komunikacji równorzędnej z wyjątkiem liczby od 65515 do 65520 włącznie.
   * Trasy należy anonsować od lokalnego routera brzegowego do platformy Azure za pośrednictwem protokołu BGP podczas konfigurowania prywatnej komunikacji równorzędnej.
   * **Opcjonalne-** Skrót MD5, jeśli zdecydujesz się na użycie jednego z nich.
3. Wybierz wiersz prywatnej komunikacji równorzędnej Azure, jak pokazano w następującym przykładzie:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/select-private-peering.png" alt-text="Wybierz wiersz prywatnej komunikacji równorzędnej":::

4. Skonfiguruj prywatną komunikację równorzędną. **Zapisz** konfigurację po określeniu wszystkich parametrów.

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/private-peering-configuration.png" alt-text="Konfigurowanie prywatnej komunikacji równorzędnej":::

5. Po pomyślnym zaakceptowaniu konfiguracji zobaczysz coś podobnego do poniższego przykładu:

   :::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/save-private-peering.png" alt-text="Zapisano prywatną komunikację równorzędną":::

### <a name="to-view-azure-private-peering-details"></a><a name="getprivate"></a>Aby wyświetlić szczegóły dotyczące prywatnej komunikacji równorzędnej Azure

Możesz wyświetlić właściwości prywatnej komunikacji równorzędnej Azure, wybierając ją.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/view-peering-m.png" alt-text="Wyświetl właściwości prywatnych komunikacji równorzędnej":::

### <a name="to-update-azure-private-peering-configuration"></a><a name="updateprivate"></a>Aby zaktualizować konfigurację prywatnej komunikacji równorzędnej Azure

Można wybrać wiersz dotyczący komunikacji równorzędnej i zmodyfikować jej właściwości. Po uaktualnieniu Zapisz zmiany.

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/update-private-peering.png" alt-text="Aktualizowanie prywatnej komunikacji równorzędnej":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

### <a name="to-delete-microsoft-peering"></a><a name="deletemsft"></a>Aby usunąć komunikację równorzędną firmy Microsoft

Aby usunąć konfigurację komunikacji równorzędnej firmy Microsoft, kliknij prawym przyciskiem myszy pozycję Komunikacja równorzędna i wybierz polecenie **Usuń** , jak pokazano na poniższej ilustracji:

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/delete-microsoft-peering.png" alt-text="Usuń komunikację równorzędną firmy Microsoft":::

### <a name="to-delete-azure-private-peering"></a><a name="deleteprivate"></a>Aby usunąć prywatną komunikację równorzędną Azure

Konfigurację prywatnej komunikacji równorzędnej można usunąć, klikając prawym przyciskiem myszy komunikację równorzędną i wybierając pozycję **Usuń** , jak pokazano na poniższej ilustracji:

> [!WARNING]
> Przed uruchomieniem tej operacji należy upewnić się, że wszystkie sieci wirtualne i ExpressRoute Global Reach są usuwane. 
> 

:::image type="content" source="./media/expressroute-howto-routing-portal-resource-manager/delete-private-peering.png" alt-text="Usuwanie prywatnej komunikacji równorzędnej":::

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu prywatnej komunikacji równorzędnej Azure Możesz utworzyć bramę ExpressRoute, aby połączyć sieć wirtualną z obwodem. 

> [!div class="nextstepaction"]
> [Konfigurowanie bramy sieci wirtualnej dla usługi ExpressRoute](expressroute-howto-add-gateway-resource-manager.md)
