---
title: 'Azure ExpressRoute: Konfigurowanie Global Reach przy użyciu Azure Portal'
description: Ten artykuł ułatwia łączenie obwodów usługi ExpressRoute w celu zapewnienia sieci prywatnej między sieciami lokalnymi i włączania Global Reach przy użyciu Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 03/05/2021
ms.author: duau
ms.openlocfilehash: 336bd4aaf881b7315921ef374c92a2ac95ff3c8c
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/07/2021
ms.locfileid: "102431319"
---
# <a name="configure-expressroute-global-reach-using-the-azure-portal"></a>Konfigurowanie Global Reach ExpressRoute przy użyciu Azure Portal

W tym artykule opisano sposób konfigurowania ExpressRoute Global Reach przy użyciu programu PowerShell. Aby uzyskać więcej informacji, zobacz [ExpressRouteRoute Global REACH](expressroute-global-reach.md).

 ## <a name="before-you-begin"></a>Zanim rozpoczniesz

Przed rozpoczęciem konfiguracji Potwierdź następujące kryteria:

* Rozumiesz [przepływy pracy](expressroute-workflows.md)inicjowania obsługi obwodu ExpressRoute.
* Obwody usługi ExpressRoute są w stanie aprowizacji.
* Prywatna Komunikacja równorzędna Azure jest konfigurowana na obwodach usługi ExpressRoute.
* Jeśli chcesz uruchomić program PowerShell lokalnie, sprawdź, czy na komputerze jest zainstalowana najnowsza wersja Azure PowerShell.

## <a name="identify-circuits"></a>Identyfikuj obwody

1. Przejdź w przeglądarce do witryny [Azure Portal](https://portal.azure.com) i zaloguj się przy użyciu konta platformy Azure.

2. Zidentyfikuj obwody usługi ExpressRoute, które mają być używane. Można włączyć ExpressRoute Global Reach między prywatną komunikację równorzędną dowolnego z dwóch obwodów usługi ExpressRoute, o ile znajdują się one w obsługiwanych krajach/regionach. Obwody muszą zostać utworzone w różnych lokalizacjach komunikacji równorzędnej. 

   * Jeśli Twoja subskrypcja jest właścicielem obu obwodów, możesz wybrać opcję obwód, aby uruchomić konfigurację w poniższych sekcjach.
   * Jeśli dwa obwody znajdują się w różnych subskrypcjach platformy Azure, musisz mieć autoryzację z jednej subskrypcji platformy Azure. Następnie można przekazać klucz autoryzacji po uruchomieniu polecenia konfiguracji w innej subskrypcji platformy Azure.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-global-reach-list.png" alt-text="Zrzut ekranu przedstawiający listę obwodów usługi ExpressRoute.":::

## <a name="enable-connectivity"></a>Włączanie łączności

Włącz łączność między sieciami lokalnymi. Istnieją oddzielne zestawy instrukcji dla obwodów znajdujących się w tej samej subskrypcji platformy Azure oraz obwodów, które są w różnych subskrypcjach.

### <a name="expressroute-circuits-in-the-same-azure-subscription"></a>Obwody usługi ExpressRoute w tej samej subskrypcji platformy Azure

1. Wybierz konfigurację **prywatnej** komunikacji równorzędnej Azure. 

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-private-peering.png" alt-text="Zrzut ekranu przedstawiający stronę przegląd ExpressRoute.":::

1. Wybierz pozycję **dodaj Global REACH** , aby otworzyć stronę *Dodawanie Global REACH* konfiguracji.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/private-peering-enable-global-reach.png" alt-text="Włącz globalny zasięg z prywatnej komunikacji równorzędnej":::

1. Na stronie *Dodawanie konfiguracji Global REACH* Nadaj nazwę tej konfiguracji. Wybierz *obwód usługi ExpressRoute* , który chcesz połączyć z tym obwodem, i wprowadź w **/29 adres IPv4** dla *podsieci Global REACH*. Adresy IP w tej podsieci są używane do nawiązywania łączności między dwoma obwodami usługi ExpressRoute. Nie używaj adresów w tej podsieci w sieciach wirtualnych platformy Azure ani w sieci lokalnej. Wybierz pozycję **Dodaj** , aby dodać obwód do konfiguracji prywatnej komunikacji równorzędnej.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/add-global-reach-configuration.png" alt-text="Zrzut ekranu przedstawiający Dodawanie Global Reach w prywatnej komunikacji równorzędnej.":::

1. Wybierz pozycję **Zapisz** , aby ukończyć konfigurację Global REACH. Po zakończeniu operacji będziesz mieć łączność między dwiema sieciami lokalnymi za pośrednictwem obu obwodów usługi ExpressRoute.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/save-private-peering-configuration.png" alt-text="Zrzut ekranu przedstawiający Zapisywanie konfiguracji prywatnej komunikacji równorzędnej.":::

### <a name="expressroute-circuits-in-different-azure-subscriptions"></a>Obwody usługi ExpressRoute w różnych subskrypcjach platformy Azure

Jeśli dwa obwody nie znajdują się w tej samej subskrypcji platformy Azure, będziesz potrzebować autoryzacji. W poniższej konfiguracji Autoryzacja jest generowana z subskrypcji obwodu 2. Klucz autoryzacji jest następnie przesyłany do obwodu 1.

1. Wygeneruj klucz autoryzacji.

   :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/create-authorization-expressroute-circuit.png" alt-text="Zrzut ekranu przedstawiający generowanie klucza autoryzacji."::: 

   Zanotuj identyfikator zasobu obwodu 2 i klucz autoryzacji.

1. Wybierz konfigurację **prywatnej** komunikacji równorzędnej Azure. 

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/expressroute-circuit-private-peering.png" alt-text="Zrzut ekranu przedstawiający prywatną komunikację równorzędną na stronie Przegląd.":::

1. Wybierz pozycję **dodaj Global REACH** , aby otworzyć stronę *Dodawanie Global REACH* konfiguracji.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/private-peering-enable-global-reach.png" alt-text="Zrzut ekranu przedstawiający Dodawanie Global Reach w prywatnej komunikacji równorzędnej.":::

1. Na stronie *Dodawanie konfiguracji Global REACH* Nadaj nazwę tej konfiguracji. Zaznacz pole wyboru **autoryzacja** . Wprowadź **klucz autoryzacji** i **Identyfikator obwodu ExpressRoute** , który został wygenerowany i uzyskany w kroku 1. Następnie podaj **/29 adresów IPv4** dla *podsieci Global REACH*. Adresy IP w tej podsieci są używane do nawiązywania łączności między dwoma obwodami usługi ExpressRoute. Nie używaj adresów w tej podsieci w sieciach wirtualnych platformy Azure ani w sieci lokalnej. Wybierz pozycję **Dodaj** , aby dodać obwód do konfiguracji prywatnej komunikacji równorzędnej.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/add-global-reach-configuration-with-authorization.png" alt-text="Zrzut ekranu przedstawiający Dodawanie Global Reach z kluczem autoryzacji.":::

1. Wybierz pozycję **Zapisz** , aby ukończyć konfigurację Global REACH. Po zakończeniu operacji będziesz mieć łączność między dwiema sieciami lokalnymi za pośrednictwem obu obwodów usługi ExpressRoute.

    :::image type="content" source="./media/expressroute-howto-set-global-reach-portal/save-private-peering-configuration.png" alt-text="Zrzut ekranu przedstawiający Zapisywanie konfiguracji prywatnej komunikacji równorzędnej za pomocą Global Reach.":::

## <a name="verify-the-configuration"></a>Weryfikowanie konfiguracji

Sprawdź konfigurację Global Reach, wybierając pozycję *prywatna Komunikacja równorzędna* w obszarze Konfiguracja obwodu ExpressRoute. Po poprawnym skonfigurowaniu konfiguracja powinna wyglądać następująco:

:::image type="content" source="./media/expressroute-howto-set-global-reach-portal/verify-global-reach-configuration.png" alt-text="Zrzut ekranu Global Reach skonfigurowany.":::

## <a name="disable-connectivity"></a>Wyłącz łączność

Aby wyłączyć łączność między pojedynczym obwodem, wybierz przycisk Usuń obok *nazwy Global REACH* , aby usunąć połączenie między nimi. Następnie wybierz pozycję **Zapisz** , aby ukończyć operację.

:::image type="content" source="./media/expressroute-howto-set-global-reach-portal/disable-global-reach-configuration.png" alt-text="Zrzut ekranu przedstawiający sposób wyłączania Global Reach.":::

Po zakończeniu operacji nie masz już łączności między siecią lokalną za pośrednictwem obwody usługi ExpressRoute.

## <a name="next-steps"></a>Następne kroki
- [Dowiedz się więcej o ExpressRoute Global Reach](expressroute-global-reach.md)
- [Weryfikowanie łączności ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
- [Łączenie obwodu usługi ExpressRoute z siecią wirtualną platformy Azure](expressroute-howto-linkvnet-arm.md)
