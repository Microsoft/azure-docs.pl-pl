---
title: 'Azure ExpressRoute: Dodawanie bramy do sieci wirtualnej: Portal'
description: W tym artykule pokazano, jak dodać bramę sieci wirtualnej do już utworzonej Menedżer zasobów wirtualnej usługi ExpressRoute przy użyciu Azure Portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 12/06/2018
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: 06f7e5d28017ee618adfeeec52c6f1226e1ae82c
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89396362"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-the-azure-portal"></a>Konfigurowanie bramy sieci wirtualnej dla usługi ExpressRoute przy użyciu Azure Portal
> [!div class="op_single_selector"]
> * [Resource Manager — witryna Azure Portal](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager — program PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Klasyczny — PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Wideo — Azure Portal](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

W tym artykule omówiono procedurę dodawania bramy sieci wirtualnej dla istniejącej sieci wirtualnej. W tym artykule omówiono procedurę dodawania, zmieniania rozmiaru i usuwania bramy sieci wirtualnej (VNet) dla istniejącej sieci wirtualnej. Kroki tej konfiguracji są przeznaczone dla sieci wirtualnych, które zostały utworzone przy użyciu modelu wdrażania Menedżer zasobów, który będzie używany w konfiguracji ExpressRoute. Aby uzyskać więcej informacji na temat bram sieci wirtualnej i ustawień konfiguracji bramy dla usługi ExpressRoute, zobacz [Informacje o bramach sieci wirtualnej dla usługi ExpressRoute](expressroute-about-virtual-network-gateways.md). 


## <a name="before-beginning"></a>Przed rozpoczęciem

Kroki dla tego zadania używają sieci wirtualnej na podstawie wartości z poniższej listy odniesienia konfiguracji. Ta lista jest używana w przykładowych krokach. Możesz skopiować listę do użycia jako odwołanie, zastępując wartości własnymi.

**Lista odwołań do konfiguracji**

* Virtual Network Name = "TestVNet"
* Virtual Network przestrzeń adresowa = 192.168.0.0/16
* Nazwa podsieci = "fronton" 
    * Przestrzeń adresowa podsieci = "192.168.1.0/24"
* Grupa zasobów = "TestRG"
* Location = "Wschodnie stany USA"
* Nazwa podsieci bramy: "GatewaySubnet" zawsze należy nazwać sieci bramy *GatewaySubnet*.
    * Przestrzeń adresowa podsieci bramy = "192.168.200.0/26"
* Nazwa bramy = "ERGW"
* Publiczna nazwa IP bramy = "MyERGWVIP"
* Gateway Type = "ExpressRoute" Ten typ jest wymagany do konfiguracji ExpressRoute.

[Film wideo](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network) dotyczący tych kroków można wyświetlić przed rozpoczęciem konfiguracji.

## <a name="create-the-gateway-subnet"></a>Tworzenie podsieci bramy

1. W [portalu](https://portal.azure.com) przejdź do sieci wirtualnej usługi Resource Manager, dla której chcesz utworzyć bramę sieci wirtualnej.
2. W sekcji **Ustawienia** bloku sieci wirtualnej kliknij pozycję **Podsieci**, aby rozwinąć blok Podsieci.
3. W bloku **Podsieci** kliknij pozycję **+Podsieć bramy**, aby otworzyć blok **Dodaj podsieć**. 
   
    ![Dodaj podsieć bramy](./media/expressroute-howto-add-gateway-portal-resource-manager/addgwsubnet.png "Dodaj podsieć bramy")


4. **Nazwa** dla podsieci zostanie automatycznie wypełniona wartością „GatewaySubnet”. Ta wartość jest wymagana, aby platforma Azure mogła rozpoznać podsieć jako podsieć bramy. Dostosuj automatycznie wypełnione wartości w polu **Zakres adresów** do wymagań konfiguracji. Zalecamy utworzenie podsieci bramy z/27 lub większą (/26,/25 itp.). Następnie kliknij przycisk **OK** , aby zapisać wartości i utworzyć podsieć bramy.

    ![Dodawanie podsieci](./media/expressroute-howto-add-gateway-portal-resource-manager/addsubnetgw.png "Dodawanie podsieci")

## <a name="create-the-virtual-network-gateway"></a>Tworzenie bramy sieci wirtualnej

1. W portalu po lewej stronie kliknij pozycję **+** i wpisz "Virtual Network Gateway" w polu wyszukiwania. Znajdź pozycję **Brama sieci wirtualnej** w wynikach wyszukiwania i kliknij tę pozycję. U dołu bloku **Brama sieci wirtualnej** kliknij pozycję **Utwórz**. Spowoduje to otwarcie bloku **Tworzenie bramy sieci wirtualnej**.
2. W bloku **Tworzenie bramy sieci wirtualnej** wypełnij wartości dla swojej bramy sieci wirtualnej.

    ![Tworzenie pól bloku bramy sieci wirtualnej](./media/expressroute-howto-add-gateway-portal-resource-manager/gw.png "Tworzenie pól bloku bramy sieci wirtualnej")
3. **Nazwa**: Nadaj nazwę bramie. Nie chodzi o nazwę podsieci bramy. Jest to nazwa obiektu bramy, który zostanie utworzony.
4. **Typ bramy**: wybierz pozycję **ExpressRoute**.
5. **Jednostka SKU**: Wybierz jednostkę SKU bramy z listy rozwijanej.
6. **Lokalizacja**: Dostosuj wartość w polu **Lokalizacja**, aby wskazywała lokalizację sieci wirtualnej. Jeśli lokalizacja nie wskazuje regionu, w którym znajduje się Twoja sieć wirtualna, sieć ta nie jest widoczna na liście rozwijanej „Wybierz sieć wirtualną”.
7. Wybierz sieć wirtualną, do której chcesz dodać bramę. Kliknij pozycję **Sieć wirtualna** , aby otworzyć blok **Wybieranie sieci wirtualnej** . Wybierz sieć wirtualną. Jeśli nie widzisz sieci wirtualnej, upewnij się, że pole **Lokalizacja** wskazuje region, w którym znajduje się Twoja sieć wirtualna.
9. Wybierz publiczny adres IP. Kliknij polecenie **Publiczny adres IP**, aby otworzyć blok **Wybieranie publicznego adresu IP**. Następnie kliknij przycisk **Utwórz nowy**, aby otworzyć **blok Tworzenie bramy sieci lokalnej**. Wprowadź nazwę dla publicznego adresu IP. W bloku zostanie utworzony obiekt publicznego adresu IP, do którego publiczny adres IP zostanie dynamicznie przypisany. Kliknij przycisk **OK**, aby zapisać zmiany w tym bloku.
10. **Subskrypcja**: Sprawdź, czy wybrano poprawną subskrypcję.
11. **Grupa zasobów**: To ustawienie jest określane przez wybraną sieć wirtualną.
12. Nie zmieniaj ustawienia **Lokalizacja** po określeniu poprzednich ustawień.
13. Sprawdź poprawność ustawień. Jeśli chcesz, aby brama była wyświetlana na pulpicie nawigacyjnym, możesz wybrać opcję **Przypnij do pulpitu nawigacyjnego** znajdującą się u dołu bloku.
14. Kliknij przycisk **Utwórz**, aby rozpocząć tworzenie bramy. Ustawienia zostaną zweryfikowane i brama zostanie wdrożona. Tworzenie bramy sieci wirtualnej może potrwać do 45 minut.

## <a name="next-steps"></a>Następne kroki
Po utworzeniu bramy sieci wirtualnej można połączyć sieć wirtualną z obwodem ExpressRoute. Zobacz [Link Virtual Network do obwodu ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md).
