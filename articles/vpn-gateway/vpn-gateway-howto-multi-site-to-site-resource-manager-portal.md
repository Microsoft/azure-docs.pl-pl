---
title: 'Dodawanie wielu połączeń między lokacjami bramy sieci VPN do sieci wirtualnej: Azure Portal'
description: Dodawanie połączeń S2S obejmujących wiele lokacji do bramy sieci VPN z istniejącym połączeniem
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/27/2020
ms.author: cherylmc
ms.openlocfilehash: 92d39b0d39511571701fd092f641cb8ca3ae42c7
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92890172"
---
# <a name="add-additional-s2s-connections-to-a-vnet-azure-portal"></a>Dodawanie dodatkowych połączeń S2S do sieci wirtualnej: Azure Portal

> [!div class="op_single_selector"]
> * [Witryna Azure Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klasyczny)](vpn-gateway-multi-site.md)
>

W tym artykule opisano Dodawanie dodatkowych połączeń między lokacjami (S2S) do bramy sieci VPN z istniejącym połączeniem. Ta architektura jest często określana mianem konfiguracji "wiele lokacji". Można dodać połączenie S2S do sieci wirtualnej, która ma już połączenie S2S, połączenie punkt-lokacja lub połączenie między sieciami wirtualnymi. Podczas dodawania połączeń są pewne ograniczenia. Zapoznaj się z sekcją [wymagania wstępne](#before) w tym artykule, aby sprawdzić przed rozpoczęciem konfiguracji.

Ten artykuł ma zastosowanie do Menedżer zasobów sieci wirtualnych z bramą sieci VPN RouteBased. Te kroki nie mają zastosowania do nowych konfiguracji połączeń ExpressRoute/lokacja-lokacja. Jednakże w przypadku dodawania nowego połączenia sieci VPN do już istniejącej konfiguracji współdziałania można użyć tych kroków. Zobacz [współistniejące połączenia ExpressRoute/S2S](../expressroute/expressroute-howto-coexist-resource-manager.md) , aby uzyskać informacje dotyczące współistniejących połączeń.

## <a name="prerequisites"></a><a name="before"></a>Wymagania wstępne

Sprawdź następujące elementy:

* Nie konfigurujesz nowej ExpressRoute i konfiguracji VPN Gateway.
* Masz sieć wirtualną, która została utworzona przy użyciu modelu wdrażania Menedżer zasobów z istniejącym połączeniem.
* Brama sieci wirtualnej dla sieci wirtualnej to RouteBased. Jeśli masz bramę sieci VPN PolicyBased, należy usunąć bramę sieci wirtualnej i utworzyć nową bramę sieci VPN jako RouteBased.
* Żaden z zakresów adresów nie pokrywa się z sieci wirtualnych, z którym jest nawiązywane połączenie z siecią wirtualną.
* Masz zgodne urządzenie sieci VPN i osoba, która jest w stanie ją skonfigurować. Zobacz artykuł [About VPN Devices](vpn-gateway-about-vpn-devices.md) (Urządzenia sieci VPN — informacje). Jeśli nie dysponujesz wiedzą niezbędną do skonfigurowania urządzenia sieci VPN lub nie znasz zakresu adresów IP ujętego w konfiguracji sieci lokalnej, skontaktuj się z osobą, która może podać Ci te dane.
* Masz zewnętrzny adres IP dla urządzenia sieci VPN.

## <a name="configure-a-connection"></a><a name="configure"></a>Konfigurowanie połączenia

1. W przeglądarce przejdź do witryny [Azure Portal](https://portal.azure.com) i, jeśli to konieczne, zaloguj się przy użyciu konta platformy Azure.
1. Wybierz pozycję **wszystkie zasoby** i Znajdź **bramę sieci wirtualnej** z listy zasobów i wybierz ją.
1. Na stronie **Brama sieci wirtualnej** wybierz pozycję **połączenia** .

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connections.png" alt-text="Połączenia z bramą sieci VPN":::
1. Na stronie **połączenia** wybierz pozycję **+ Dodaj** .
1. Spowoduje to otwarcie strony **Dodawanie połączenia** .

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/add-connection.png" alt-text="Połączenia z bramą sieci VPN":::
1. Na stronie **Dodawanie połączenia** wypełnij następujące pola:

   * **Nazwa:** Nazwa, która ma zostać przydana do witryny, z którą chcesz nawiązać połączenie.
   * **Typ połączenia:** Wybierz pozycję **lokacja-lokacja (IPSec)** .

## <a name="add-a-local-network-gateway"></a><a name="local"></a>Dodawanie bramy sieci lokalnej

1. W polu **Brama sieci lokalnej** wybierz pozycję **_Wybierz bramę sieci lokalnej_*_. Spowoduje to otwarcie strony a* Wybierz bramę sieci lokalnej** .
1. Wybierz pozycję **+ Utwórz nowy** , aby otworzyć stronę **Utwórz bramę sieci lokalnej** .

   :::image type="content" source="./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/create-local-network-gateway.png" alt-text="Połączenia z bramą sieci VPN":::
1. Na stronie **Tworzenie bramy sieci lokalnej** wypełnij następujące pola:

   * **Nazwa:** Nazwa, która ma zostać przydana do zasobu bramy sieci lokalnej.
   * **Punkt końcowy:** Publiczny adres IP urządzenia sieci VPN w lokacji, z którą chcesz nawiązać połączenie, lub nazwa FQDN punktu końcowego.
   * **Przestrzeń adresowa:** Przestrzeń adresowa, która ma być kierowana do nowej lokacji sieci lokalnej.
1. Aby zapisać zmiany, wybierz **przycisk OK** na stronie **Tworzenie bramy sieci lokalnej** .

## <a name="add-the-shared-key"></a><a name="part3"></a>Dodaj klucz współużytkowany

1. Po utworzeniu bramy sieci lokalnej Wróć do strony **Dodawanie połączenia** .
1. Wypełnij pozostałe pola. Dla **klucza współużytkowanego (PSK)** można pobrać klucz współużytkowany z urządzenia sieci VPN lub wprowadzić go w tym miejscu, a następnie skonfigurować urządzenie sieci VPN tak, aby korzystało z tego samego klucza współużytkowanego. Ważnym czynnikiem jest to, że klucze są dokładnie takie same.

## <a name="create-the-connection"></a><a name="create"></a>Tworzenie połączenia

1. W dolnej części strony wybierz pozycję **OK** , aby utworzyć połączenie. Połączenie rozpocznie się natychmiast.
1. Po zakończeniu połączenia można je wyświetlić i sprawdzić.

## <a name="view-and-verify-the-vpn-connection"></a><a name="verify"></a>Wyświetl i sprawdź połączenie sieci VPN

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="next-steps"></a>Następne kroki

Po zakończeniu procesu nawiązywania połączenia można dodać do sieci wirtualnych maszyny wirtualne. Aby uzyskać więcej informacji, zobacz [ścieżki uczenia maszyn wirtualnych](/learn/paths/deploy-a-website-with-azure-virtual-machines/).
