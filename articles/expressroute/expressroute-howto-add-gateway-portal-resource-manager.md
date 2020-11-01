---
title: 'Samouczek: Azure ExpressRoute — Dodawanie bramy do sieci wirtualnej (Azure Portal)'
description: Ten samouczek przeprowadzi Cię przez proces dodawania bramy sieci wirtualnej do sieci wirtualnej dla usługi ExpressRoute przy użyciu Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/05/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 843d0b8cfd75e8cbdf45ac535cc9486aa42442d6
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2020
ms.locfileid: "91761836"
---
# <a name="tutorial-configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Samouczek: Konfigurowanie bramy sieci wirtualnej dla usługi ExpressRoute przy użyciu Azure Portal
> [!div class="op_single_selector"]
> * [Resource Manager — witryna Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager — program PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasyczny — PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Wideo — Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 

Ten samouczek przeprowadzi Cię przez kroki umożliwiające dodanie bramy sieci wirtualnej do istniejącej sieci wirtualnej. W tym artykule omówiono procedurę dodawania, zmieniania rozmiaru i usuwania bramy sieci wirtualnej (VNet) dla istniejącej sieci wirtualnej. Kroki tej konfiguracji są przeznaczone dla sieci wirtualnych, które zostały utworzone przy użyciu modelu wdrażania Menedżer zasobów, który będzie używany w konfiguracji ExpressRoute. Aby uzyskać więcej informacji na temat bram sieci wirtualnej i ustawień konfiguracji bramy dla usługi ExpressRoute, zobacz [Informacje o bramach sieci wirtualnej dla usługi ExpressRoute](expressroute-about-virtual-network-gateways.md). 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> - Utwórz podsieć bramy.
> - Utwórz bramę Virtual Network.

## <a name="prerequisites"></a>Wymagania wstępne

Kroki dla tego zadania używają sieci wirtualnej na podstawie wartości z poniższej listy odniesienia konfiguracji. Ta lista jest używana w przykładowych krokach. Możesz skopiować listę do użycia jako odwołanie, zastępując wartości własnymi.

**Lista odwołań do konfiguracji**

* Virtual Network Name = "TestVNet"
* Virtual Network przestrzeń adresowa = 192.168.0.0/16
* Nazwa podsieci = "fronton" 
    * Przestrzeń adresowa podsieci = "192.168.1.0/24"
* Grupa zasobów = "TestRG"
* Location = "Wschodnie stany USA"
* Nazwa podsieci bramy: "GatewaySubnet" zawsze należy nazwać sieci bramy *GatewaySubnet* .
    * Przestrzeń adresowa podsieci bramy = "192.168.200.0/26"
* Nazwa bramy = "ERGW"
* Publiczna nazwa IP bramy = "MyERGWVIP"
* Gateway Type = "ExpressRoute" Ten typ jest wymagany do konfiguracji ExpressRoute.

[Film wideo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network) dotyczący tych kroków można wyświetlić przed rozpoczęciem konfiguracji.

## <a name="create-the-gateway-subnet"></a>Tworzenie podsieci bramy

1. W [portalu](https://portal.azure.com) przejdź do sieci wirtualnej usługi Resource Manager, dla której chcesz utworzyć bramę sieci wirtualnej.
1. W sekcji **Ustawienia** sieci wirtualnej wybierz pozycję **podsieci** , aby rozwinąć ustawienia podsieci.
1. W obszarze Ustawienia **podsieci** wybierz pozycję **+ podsieć bramy** , aby dodać podsieć bramy. 
   
    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/add-gateway-subnet.png" alt-text="Dodaj podsieć bramy":::

1. **Nazwa** dla podsieci zostanie automatycznie wypełniona wartością „GatewaySubnet”. Ta wartość jest wymagana, aby platforma Azure mogła rozpoznać podsieć jako podsieć bramy. Dostosuj wartości **zakresu adresów** , które mają być zgodne z wymaganiami dotyczącymi konfiguracji. Zalecamy utworzenie podsieci bramy z/27 lub większą (/26,/25 itd.). Następnie wybierz przycisk **OK** , aby zapisać wartości i utworzyć podsieć bramy.

    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/add-subnet-gateway.png" alt-text="Dodaj podsieć bramy":::

## <a name="create-the-virtual-network-gateway"></a>Tworzenie bramy sieci wirtualnej

1. W portalu po lewej stronie wybierz pozycję **Utwórz zasób** , a następnie w polu wyszukiwania wpisz ciąg "Virtual Network Gateway". Znajdź **bramę sieci wirtualnej** w zwracanym wyszukiwaniu i wybierz wpis. Na stronie **Brama sieci wirtualnej** wybierz pozycję **Utwórz** .
1. Na stronie **Tworzenie bramy sieci wirtualnej** wprowadź lub wybierz następujące ustawienia:

    | Ustawienie | Wartość |
    | --------| ----- |
    | Subskrypcja | Sprawdź, czy wybrano poprawną subskrypcję. |
    | Grupa zasobów | Grupa zasobów zostanie wybrana automatycznie po wybraniu sieci wirtualnej. | 
    | Nazwa | Nadaj nazwę bramie. Ta wartość nie jest taka sama jak nazwa podsieci bramy. Jest to nazwa tworzonego obiektu bramy.|
    | Region (Region) | Zmień pole **region** tak, aby wskazywało lokalizację, w której znajduje się Twoja sieć wirtualna. Jeśli lokalizacja nie wskazuje regionu, w którym znajduje się Twoja sieć wirtualna, Sieć wirtualna nie zostanie wyświetlona na liście rozwijanej "Wybierz sieć wirtualną". |
    | Typ bramy | Wybierz **ExpressRoute**|
    | Jednostka SKU | Wybierz jednostkę SKU bramy z listy rozwijanej. |
    | Sieć wirtualna | Wybierz pozycję *TestVNet* . |
    | Publiczny adres IP | Wybierz pozycję **Utwórz nowy** .|
    | Nazwa publicznego adresu IP | Podaj nazwę publicznego adresu IP. |

1. Wybierz pozycję **Przegląd + Utwórz** , a następnie **Utwórz** , aby rozpocząć tworzenie bramy. Ustawienia zostaną zweryfikowane i brama zostanie wdrożona. Tworzenie bramy sieci wirtualnej może potrwać do 45 minut.

    :::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/gateway.png" alt-text="Dodaj podsieć bramy":::

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli Brama ExpressRoute nie jest już potrzebna, Znajdź bramę w grupie zasobów Sieć wirtualna i wybierz pozycję **Usuń** . Upewnij się, że brama nie ma żadnych połączeń z obwodem.

:::image type="content" source="./media/expressroute-howto-add-gateway-portal-resource-manager/delete-gateway.png" alt-text="Dodaj podsieć bramy":::

## <a name="next-steps"></a>Następne kroki
Po utworzeniu bramy sieci wirtualnej można połączyć sieć wirtualną z obwodem ExpressRoute. 

> [!div class="nextstepaction"]
> [Łączenie Virtual Network z obwodem ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
