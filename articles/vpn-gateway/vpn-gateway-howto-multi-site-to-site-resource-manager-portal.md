---
title: 'Dodawanie wielu połączeń typu lokacja-lokacja bramy sieci VPN do sieci wirtualnej: Azure Portal'
description: Dodawanie połączeń S2S obejmujących wiele lokacji do bramy sieci VPN z istniejącym połączeniem
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: ec2516010768eded939b0ffa44c197f102c7766b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89401201"
---
# <a name="add-a-site-to-site-connection-to-a-vnet-with-an-existing-vpn-gateway-connection"></a>Dodawanie połączenia lokacja-lokacja do sieci wirtualnej z istniejącym połączeniem bramy sieci VPN

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)
> * [PowerShell (klasyczny)](vpn-gateway-multi-site.md)
>
> 

Ten artykuł ułatwia dodawanie połączeń typu lokacja-lokacja (S2S) do bramy sieci VPN z istniejącym połączeniem przy użyciu Azure Portal. Ten typ połączenia jest często określany mianem konfiguracji "wiele lokacji". Można dodać połączenie S2S do sieci wirtualnej, która ma już połączenie S2S, połączenie punkt-lokacja lub połączenie między sieciami wirtualnymi. Podczas dodawania połączeń są pewne ograniczenia. Przed rozpoczęciem konfiguracji zapoznaj się z sekcją [przed rozpoczęciem](#before) w tym artykule. 

Ten artykuł ma zastosowanie do Menedżer zasobów sieci wirtualnych z bramą sieci VPN RouteBased. Te kroki nie mają zastosowania do nowych konfiguracji połączeń ExpressRoute/lokacja-lokacja. Jednakże w przypadku dodawania nowego połączenia sieci VPN do już istniejącej konfiguracji współdziałania można użyć tych kroków. Zobacz [współistniejące połączenia ExpressRoute/S2S](../expressroute/expressroute-howto-coexist-resource-manager.md) , aby uzyskać informacje dotyczące współistniejących połączeń.

### <a name="deployment-models-and-methods"></a>Modele i metody wdrażania
[!INCLUDE [vpn-gateway-classic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

Ta tabela jest aktualizowana w miarę jak nowe artykuły i dodatkowe narzędzia stają się dostępne dla tej konfiguracji. Gdy artykuł jest dostępny, prosimy o bezpośredni link do niego z tej tabeli.

[!INCLUDE [vpn-gateway-table-multi-site](../../includes/vpn-gateway-table-multisite-include.md)]

## <a name="before-you-begin"></a><a name="before"></a>Zanim rozpoczniesz
Sprawdź następujące elementy:

* Nie konfigurujesz nowej ExpressRoute i konfiguracji VPN Gateway.
* Masz sieć wirtualną, która została utworzona przy użyciu modelu wdrażania Menedżer zasobów z istniejącym połączeniem.
* Brama sieci wirtualnej dla sieci wirtualnej to RouteBased. Jeśli masz bramę sieci VPN PolicyBased, należy usunąć bramę sieci wirtualnej i utworzyć nową bramę sieci VPN jako RouteBased.
* Żaden z zakresów adresów nie pokrywa się z sieci wirtualnych, z którym jest nawiązywane połączenie z siecią wirtualną.
* Masz zgodne urządzenie sieci VPN i osoba, która jest w stanie ją skonfigurować. Zobacz artykuł [About VPN Devices](vpn-gateway-about-vpn-devices.md) (Urządzenia sieci VPN — informacje). Jeśli nie dysponujesz wiedzą niezbędną do skonfigurowania urządzenia sieci VPN lub nie znasz zakresu adresów IP ujętego w konfiguracji sieci lokalnej, skontaktuj się z osobą, która może podać Ci te dane.
* Masz zewnętrzny adres IP dla urządzenia sieci VPN.

## <a name="part-1---configure-a-connection"></a><a name="part1"></a>Część 1 — Konfigurowanie połączenia
1. W przeglądarce przejdź do witryny [Azure Portal](https://portal.azure.com) i, jeśli to konieczne, zaloguj się przy użyciu konta platformy Azure.
2. Kliknij pozycję **wszystkie zasoby** i Znajdź **bramę sieci wirtualnej** z listy zasobów i kliknij ją.
3. Na stronie **Brama sieci wirtualnej** kliknij pozycję **połączenia**.
   
    ![Strona połączeń](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/connectionsblade.png "Strona połączeń")<br>
4. Na stronie **połączenia** kliknij pozycję **+ Dodaj**.
   
    ![Przycisk Dodaj połączenie](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addbutton.png "Przycisk Dodaj połączenie")<br>
5. Na stronie **Dodawanie połączenia** wypełnij następujące pola:
   
   * **Nazwa:** Nazwa, która ma zostać przydana do witryny, z którą chcesz nawiązać połączenie.
   * **Typ połączenia:** Wybierz pozycję **lokacja-lokacja (IPSec)**.
     
     ![Strona dodawania połączenia](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/addconnectionblade.png "Strona dodawania połączenia")<br>

## <a name="part-2---add-a-local-network-gateway"></a><a name="part2"></a>Część 2 — Dodawanie bramy sieci lokalnej
1. Kliknij pozycję **Brama sieci lokalnej** ***Wybierz bramę sieci lokalnej***. Spowoduje to otwarcie strony **Wybierz bramę sieci lokalnej** .
   
    ![Wybieranie bramy sieci lokalnej](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/chooselng.png "Wybieranie bramy sieci lokalnej")<br>
2. Kliknij przycisk **Utwórz nowy** , aby otworzyć stronę **Utwórz bramę sieci lokalnej** .
   
    ![Strona tworzenia bramy sieci lokalnej](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/createlngblade.png "Utwórz bramę sieci lokalnej")<br>
3. Na stronie **Tworzenie bramy sieci lokalnej** wypełnij następujące pola:
   
   * **Nazwa:** Nazwa, która ma zostać przydana do zasobu bramy sieci lokalnej.
   * **Adres IP:** Publiczny adres IP urządzenia sieci VPN w lokacji, z którą chcesz nawiązać połączenie.
   * **Przestrzeń adresowa:** Przestrzeń adresowa, która ma być kierowana do nowej lokacji sieci lokalnej.
4. Kliknij przycisk **OK** na stronie **Tworzenie bramy sieci lokalnej** , aby zapisać zmiany.

## <a name="part-3---add-the-shared-key-and-create-the-connection"></a><a name="part3"></a>Część 3 — Dodawanie klucza współużytkowanego i Tworzenie połączenia
1. Na stronie **Dodawanie połączenia** Dodaj klucz współużytkowany, który ma zostać użyty do utworzenia połączenia. Możesz pobrać klucz współużytkowany z urządzenia sieci VPN lub wprowadzić go w tym miejscu, a następnie skonfigurować urządzenie sieci VPN tak, aby korzystało z tego samego klucza współużytkowanego. Ważnym czynnikiem jest to, że klucze są dokładnie takie same.
   
    ![Klucz współużytkowany](./media/vpn-gateway-howto-multi-site-to-site-resource-manager-portal/sharedkey.png "Klucz wspólny")<br>
2. W dolnej części strony kliknij przycisk **OK** , aby utworzyć połączenie.

## <a name="part-4---verify-the-vpn-connection"></a><a name="part4"></a>Część 4 — Weryfikowanie połączenia sieci VPN


[!INCLUDE [vpn-gateway-verify-connection-ps-rm](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)]

## <a name="next-steps"></a>Następne kroki

Po zakończeniu procesu nawiązywania połączenia można dodać do sieci wirtualnych maszyny wirtualne. Aby uzyskać więcej informacji, zobacz [ścieżkę szkoleniową dotyczącą maszyn wirtualnych](/learn/paths/deploy-a-website-with-azure-virtual-machines/) .
