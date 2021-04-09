---
title: 'Samouczek: łączenie sieci wirtualnej z obwodem ExpressRoute — Azure Portal'
description: W tym samouczku pokazano, jak utworzyć połączenie w celu połączenia sieci wirtualnej z obwodem usługi Azure ExpressRoute przy użyciu Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 0ffc9c2ee17862497d3fd986da8e003f7a497056
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95998774"
---
# <a name="tutorial-connect-a-virtual-network-to-an-expressroute-circuit-using-the-portal"></a>Samouczek: łączenie sieci wirtualnej z obwodem usługi ExpressRoute przy użyciu portalu

> [!div class="op_single_selector"]
> * [Witryna Azure Portal](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Program PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Interfejs wiersza polecenia platformy Azure](howto-linkvnet-cli.md)
> * [Wideo — Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> * [PowerShell (klasyczny)](expressroute-howto-linkvnet-classic.md)
> 

Ten samouczek pomaga utworzyć połączenie w celu połączenia sieci wirtualnej z obwodem usługi Azure ExpressRoute przy użyciu Azure Portal. Sieci wirtualne, które można połączyć ze obwodem usługi Azure ExpressRoute, mogą znajdować się w tej samej subskrypcji lub być częścią innej subskrypcji.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> - Podłącz sieć wirtualną do obwodu w tej samej subskrypcji.
> - Połącz sieć wirtualną z obwodem w innej subskrypcji.
> - Usuń łącze między siecią wirtualną i obwodem ExpressRoute.

## <a name="prerequisites"></a>Wymagania wstępne

* Przed rozpoczęciem konfiguracji zapoznaj się z warunkami [wstępnymi](expressroute-prerequisites.md), [wymaganiami dotyczącymi routingu](expressroute-routing.md)i [przepływami pracy](expressroute-workflows.md) .

* Musisz mieć aktywny obwód usługi ExpressRoute.
  * Postępuj zgodnie z instrukcjami, aby [utworzyć obwód usługi ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) i mieć obwód włączony przez dostawcę połączenia.
  * Upewnij się, że masz skonfigurowaną prywatną komunikację równorzędną Azure dla obwodu. Zapoznaj się z instrukcjami dotyczącymi komunikacji równorzędnej i routingu w artykule [Tworzenie i modyfikowanie komunikacji równorzędnej dla obwodu usługi ExpressRoute](expressroute-howto-routing-portal-resource-manager.md) .
  * Upewnij się, że usługa prywatnej komunikacji równorzędnej Azure jest skonfigurowana i ustanawia komunikację równorzędną BGP między siecią a firmą Microsoft w celu kompleksowej łączności.
  * Upewnij się, że masz utworzoną sieć wirtualną i bramę sieci wirtualnej i została ona w pełni zainicjowana. Postępuj zgodnie z instrukcjami, aby [utworzyć bramę sieci wirtualnej dla usługi ExpressRoute](expressroute-howto-add-gateway-resource-manager.md). Brama sieci wirtualnej dla usługi ExpressRoute używa elementu Gatewaytype "ExpressRoute", a nie sieci VPN.

* Do obwodu standardowego ExpressRoute można połączyć maksymalnie 10 sieci wirtualnych. Wszystkie sieci wirtualne muszą znajdować się w tym samym regionie geopolitycznym w przypadku korzystania ze standardowego obwodu usługi ExpressRoute.

* Jedna sieć wirtualna może być połączona do maksymalnie czterech obwodów usługi ExpressRoute. Użyj poniższego procesu, aby utworzyć nowy obiekt połączenia dla każdego obwodu usługi ExpressRoute, z którym nawiązujesz połączenie. Obwody usługi ExpressRoute mogą znajdować się w tej samej subskrypcji, różnych subskrypcjach lub kombinacji obu tych elementów.

* Po włączeniu dodatku ExpressRoute Premium można połączyć sieci wirtualne poza regionem geopolitycznym obwodu ExpressRoute. Dodatek Premium umożliwia również łączenie więcej niż 10 sieci wirtualnych do obwodu ExpressRoute, w zależności od wybranej przepustowości. Zapoznaj się z [często zadawanymi pytaniami](expressroute-faqs.md) , aby uzyskać więcej informacji na temat dodatku Premium.

* [Film wideo można wyświetlić](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit) przed rozpoczęciem, aby lepiej zrozumieć kroki.

## <a name="connect-a-vnet-to-a-circuit---same-subscription"></a>Łączenie sieci wirtualnej z obwodem — ta sama subskrypcja

> [!NOTE]
> Informacje o konfiguracji protokołu BGP nie będą wyświetlane, jeśli dostawca warstwy 3 skonfigurował komunikację równorzędną. Jeśli obwód jest w stanie aprowizacji, powinno być możliwe tworzenie połączeń.
>

### <a name="to-create-a-connection"></a>Aby utworzyć połączenie

1. Upewnij się, że pomyślnie skonfigurowano obwód usługi ExpressRoute i prywatną komunikację równorzędną Azure. Postępuj zgodnie z instrukcjami w temacie [Tworzenie obwodu usługi ExpressRoute](expressroute-howto-circuit-arm.md) i [Tworzenie i modyfikowanie komunikacji równorzędnej dla obwodu usługi ExpressRoute](expressroute-howto-routing-arm.md). Obwód ExpressRoute powinien wyglądać jak na poniższej ilustracji:

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/express-route-circuit.png" alt-text="Zrzut ekranu obwodu ExpressRoute":::

1. Teraz można rozpocząć Inicjowanie obsługi połączenia w celu połączenia bramy sieci wirtualnej z obwodem usługi ExpressRoute. Wybierz pozycję **połączenie**  >  **Dodaj** , aby otworzyć stronę **Dodawanie połączenia** .

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/add-connection.png" alt-text="Dodaj zrzut ekranu połączenia":::

1. Wprowadź nazwę połączenia, a następnie wybierz pozycję **Next (dalej): Settings >**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-connection-basic.png" alt-text="Utwórz stronę podstawową połączenia":::

1. Wybierz bramę, która należy do sieci wirtualnej, którą chcesz połączyć z obwodem, a następnie wybierz kolejno pozycje **Przegląd + Utwórz**. Następnie wybierz pozycję **Utwórz** po zakończeniu walidacji.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-connection-settings.png" alt-text="Strona tworzenia ustawień połączenia":::

1. Po pomyślnym skonfigurowaniu połączenia obiekt połączenia będzie wyświetlał informacje dotyczące połączenia.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-object.png" alt-text="Zrzut ekranu obiektu połączenia":::

## <a name="connect-a-vnet-to-a-circuit---different-subscription"></a>Łączenie sieci wirtualnej z inną subskrypcją obwodu

Obwód ExpressRoute można udostępnić w wielu subskrypcjach. Na poniższej ilustracji przedstawiono prosty schemat działania udostępniania dla obwodów usługi ExpressRoute w wielu subskrypcjach.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png" alt-text="Łączność między subskrypcjami":::

Każda z mniejszych chmur w ramach dużej chmury służy do reprezentowania subskrypcji należących do różnych działów w organizacji. Każda z działów w organizacji korzysta z własnej subskrypcji na potrzeby wdrażania ich usług — ale może współdzielić jeden obwód usługi ExpressRoute, aby połączyć się z siecią lokalną. Pojedynczy dział (w tym przykładzie:) może być własnym obwodem ExpressRoute. Inne subskrypcje w organizacji mogą korzystać z obwodu ExpressRoute.

  > [!NOTE]
  > Naliczanie opłat za połączenia i przepustowość dla dedykowanego obwodu zostanie zastosowane do właściciela obwodu usługi ExpressRoute. Wszystkie sieci wirtualne mają tę samą przepustowość.
  >

### <a name="administration---about-circuit-owners-and-circuit-users"></a>Administracja — informacje o właścicielach obwodu i użytkowników obwodu

"Właściciel obwodu" jest autoryzowanym użytkownikiem zaawansowanym zasobu obwodu ExpressRoute. Właściciel obwodu może tworzyć autoryzacje, które mogą być zrealizowane przez "użytkowników obwodów". Użytkownicy obwodu to właściciele bram sieci wirtualnej, którzy nie należą do tej samej subskrypcji co obwód usługi ExpressRoute. Użytkownicy obwodów mogą wykorzystać autoryzacje (jedna autoryzacja na sieć wirtualną).

Właściciel obwodu ma uprawnienia do modyfikowania i odwoływania autoryzacji w dowolnym momencie. Odwoływanie autoryzacji powoduje usunięcie wszystkich połączeń linków z subskrypcji, której dostęp został odwołany.

### <a name="circuit-owner-operations"></a>Operacje właściciela obwodu

**Aby utworzyć autoryzację połączenia**

Właściciel obwodu tworzy autoryzację, która tworzy klucz autoryzacji, który ma być używany przez użytkownika obwodu do łączenia ich bram sieci wirtualnych z obwodem ExpressRoute. Autoryzacja jest prawidłowa tylko dla jednego połączenia.

> [!NOTE]
> Każde połączenie wymaga oddzielnej autoryzacji.
>

1. Na stronie ExpressRoute wybierz pozycję **autoryzacje** , a następnie wpisz **nazwę** autoryzacji i wybierz pozycję **Zapisz**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png" alt-text="Autoryzacje":::

2. Po zapisaniu konfiguracji Skopiuj **Identyfikator zasobu** i **klucz autoryzacji**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/authorization-key.png" alt-text="Klucz autoryzacji":::

**Aby usunąć autoryzację połączenia**

Połączenie można usunąć, wybierając ikonę **usuwania** dla klucza autoryzacji dla danego połączenia.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/delete-authorization-key.png" alt-text="Usuń klucz autoryzacji":::

Jeśli chcesz usunąć połączenie, ale zachować klucz autoryzacji, możesz usunąć połączenie ze strony połączenie obwodu.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/delete-connection-owning-circuit.png" alt-text="Usuwanie obwodu będącego właścicielem połączenia":::

### <a name="circuit-user-operations"></a>Operacje użytkownika obwodowego

Użytkownik obwodu potrzebuje identyfikatora zasobu i klucza autoryzacji od właściciela obwodu.

**Aby zrealizować autoryzację połączenia**

1. Wybierz przycisk **+ Utwórz zasób** . Wyszukaj **połączenie** i wybierz pozycję **Utwórz**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/create-new-resources.png" alt-text="Tworzenie nowych zasobów":::

1. Upewnij się, że *Typ połączenia* to **ExpressRoute**. Wybierz *grupę zasobów* i *lokalizację*, a następnie na stronie podstawowe wybierz pozycję **OK** .

    > [!NOTE]
    > Lokalizacja *musi* być zgodna z lokalizacją bramy sieci wirtualnej, dla której tworzysz połączenie.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-basics.png" alt-text="Strona Podstawy":::

1. Na stronie **Ustawienia** wybierz *bramę sieci wirtualnej* i zaznacz pole wyboru Wykorzystaj **autoryzację** . Wprowadź *klucz autoryzacji* i *Identyfikator URI obwodu równorzędnego* i nadaj nazwę połączenie. Wybierz przycisk **OK**. 
 
    > [!NOTE]
    > *URI obwodu równorzędnego* jest identyfikatorem zasobu obwodu ExpressRoute (który można znaleźć w okienku ustawienia właściwości obwodu ExpressRoute).

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-settings.png" alt-text="Strona Ustawienia":::

1. Przejrzyj informacje na stronie **Podsumowanie** i wybierz **przycisk OK**.

    :::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/connection-summary.png" alt-text="Strona podsumowania":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Możesz usunąć połączenie i odłączyć sieć wirtualną do obwodu usługi ExpressRoute, wybierając ikonę **usuwania** na stronie połączenia.

:::image type="content" source="./media/expressroute-howto-linkvnet-portal-resource-manager/delete-connection.png" alt-text="Usuń połączenie":::

## <a name="next-steps"></a>Następne kroki

W ramach tego samouczka nauczysz się połączyć sieć wirtualną z obwodem w ramach tej samej subskrypcji i innej subskrypcji. Aby uzyskać więcej informacji na temat bramy ExpressRoute, zobacz: 

> [!div class="nextstepaction"]
> [Informacje o bramach sieci wirtualnej ExpressRoute](expressroute-about-virtual-network-gateways.md)
