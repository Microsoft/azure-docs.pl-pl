---
title: 'Konfigurowanie połączenia między sieciami wirtualnymi VPN Gateway: Azure Portal'
description: Utwórz połączenie usługi bramy VPN Gateway między sieciami wirtualnymi przy użyciu usługi Resource Manager i witryny Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 12ab54e51b26c4b3d77f5518d4e4f44efe34affd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90983311"
---
# <a name="configure-a-vnet-to-vnet-vpn-gateway-connection-by-using-the-azure-portal"></a>Konfigurowanie połączenia bramy sieci VPN między sieciami wirtualnymi przy użyciu witryny Azure Portal

Ten artykuł pomoże Ci połączyć sieci wirtualne przy użyciu typu połączenia sieć wirtualna-sieć wirtualna. Sieci wirtualne mogą być zlokalizowane w różnych regionach i funkcjonować w ramach różnych subskrypcji. W przypadku łączenia sieci wirtualnych z różnych subskrypcji subskrypcje nie muszą być skojarzone z tą samą dzierżawą usługi Active Directory. 

![Diagram połączenia między sieciami wirtualnymi (v2v)](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/v2vrmps.png)

Kroki podane w tym artykule mają zastosowanie do modelu wdrażania przy użyciu usługi Azure Resource Manager i użyto w nich witryny Azure Portal. Tę konfigurację możesz utworzyć przy użyciu innego narzędzia lub modelu wdrażania, korzystając z opcji opisanych w następujących artykułach:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
> * [Program PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
> * [Interfejs wiersza polecenia platformy Azure](vpn-gateway-howto-vnet-vnet-cli.md)
> * [Portal Azure (klasyczny)](vpn-gateway-howto-vnet-vnet-portal-classic.md)
> * [Łączenie różnych modeli wdrażania — witryna Azure Portal](vpn-gateway-connect-different-deployment-models-portal.md)
> * [Łączenie różnych modeli wdrażania — program PowerShell](vpn-gateway-connect-different-deployment-models-powershell.md)
>
>

## <a name="about-connecting-vnets"></a>Łączenie sieci wirtualnych — informacje

W poniższych sekcjach opisano różne metody łączenia się z sieciami wirtualnymi.

### <a name="vnet-to-vnet"></a>Połączenia między sieciami wirtualnymi

Skonfigurowanie połączenia typu sieć wirtualna-sieć wirtualna jest prostym sposobem połączenia sieci wirtualnych. Proces łączenia dwóch sieci wirtualnych przy użyciu typu połączenia sieć wirtualna-sieć wirtualna (VNet2VNet) przebiega podobnie do procesu tworzenia połączenia IPsec typu lokacja-lokacja z lokacją lokalną. Oba typy połączeń używają bramy sieci VPN, aby zapewnić bezpieczny tunel korzystający z protokołu IPsec/IKE, oraz działają tak samo pod względem komunikacji. Różnią się jednak sposobem, w jaki skonfigurowana jest brama sieci lokalnej. 

Podczas tworzenia połączenia sieć wirtualna-sieć wirtualna przestrzeń adresowa bramy sieci lokalnej jest automatycznie tworzona i wypełniana. Po zaktualizowaniu przestrzeni adresowej dla jednej sieci wirtualnej druga sieć wirtualna automatycznie kieruje ruch do zaktualizowanej przestrzeni adresowej. Zwykle szybciej i łatwiej jest utworzyć połączenie sieć wirtualna-sieć wirtualna niż lokacja-lokacja.

### <a name="site-to-site-ipsec"></a>Lokacja-lokacja (IPsec)

W przypadku pracy ze złożoną konfiguracją sieci lepszym rozwiązaniem może być połączenie sieci wirtualnych za pomocą połączenia [lokacja-lokacja](vpn-gateway-howto-site-to-site-resource-manager-portal.md). W przypadku korzystania z procedury tworzenia połączenia IPsec typu lokacja-lokacja bramy sieci lokalnej są tworzone i konfigurowane ręcznie. Każda z bram sieci lokalnej sieci wirtualnej traktuje drugą sieć wirtualną jako lokację lokalną. Te kroki umożliwiają określenie dodatkowych przestrzeni adresowych dla bramy sieci lokalnej w celu kierowania ruchem. W przypadku zmiany przestrzeni adresowej sieci wirtualnej należy ręcznie zaktualizować odpowiednią bramę sieci lokalnej.

### <a name="vnet-peering"></a>Komunikacja równorzędna sieci wirtualnych

Można również połączyć sieci wirtualne za pomocą wirtualnych sieci równorzędnych. W przypadku wirtualnych sieci równorzędnych nie jest używana brama sieci VPN i występują inne ograniczenia. Ponadto [ceny dotyczące komunikacji równorzędnej sieci wirtualnych](https://azure.microsoft.com/pricing/details/virtual-network) są obliczane inaczej niż [ceny dotyczące usługi VPN Gateway połączenia sieć wirtualna-sieć wirtualna](https://azure.microsoft.com/pricing/details/vpn-gateway). Aby uzyskać więcej informacji, zobacz temat [Komunikacja równorzędna sieci wirtualnych](../virtual-network/virtual-network-peering-overview.md).

## <a name="why-create-a-vnet-to-vnet-connection"></a>Dlaczego warto utworzyć połączenie sieć wirtualna-sieć wirtualna?

Sieci wirtualne można łączyć za pomocą połączenia sieć wirtualna-sieć wirtualna z następujących powodów:

### <a name="cross-region-geo-redundancy-and-geo-presence"></a>Niezależna od regionu nadmiarowość i obecność geograficzna

  * Można tworzyć własne replikacje geograficzne lub przeprowadzać synchronizację z bezpiecznym połączeniem bez przechodzenia przez punkty końcowe dostępne z Internetu.
  * Usługi Azure Traffic Manager i Azure Load Balancer pozwalają skonfigurować obciążenie o wysokiej dostępności z nadmiarowością geograficzną w wielu regionach świadczenia usługi Azure. Można na przykład skonfigurować Zawsze włączone grupy dostępności usługi SQL Server w wielu regionach platformy Azure.

### <a name="regional-multi-tier-applications-with-isolation-or-administrative-boundaries"></a>Regionalne aplikacje wielowarstwowe z izolacją lub granicami administracyjnymi

  * W ramach jednego regionu można skonfigurować aplikacje wielowarstwowe z wieloma połączonymi ze sobą sieciami wirtualnymi, korzystając z izolacji lub wymagań administracyjnych.

Komunikację między sieciami wirtualnymi można łączyć z konfiguracjami obejmującymi wiele lokacji. Te konfiguracje pozwalają tworzyć topologie sieci, które łączą wdrożenia obejmujące wiele lokalizacji z połączeniami między sieciami wirtualnymi, jak pokazano na poniższym diagramie:

![Informacje o połączeniach](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/aboutconnections.png "Informacje o połączeniach")

W tym artykule przedstawiono sposób łączenia sieci wirtualnych przy użyciu typu połączenia sieć wirtualna-sieć wirtualna. Podczas wykonywania tych kroków w charakterze ćwiczenia można użyć przykładowych wartości ustawień. W tym przykładzie sieci wirtualne należą do tej samej subskrypcji, ale do różnych grup zasobów. Jeśli sieci wirtualne należą do różnych subskrypcji, nie można utworzyć połączenia w portalu. Zamiast tego użyj programu [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md) lub [interfejsu wiersza polecenia](vpn-gateway-howto-vnet-vnet-cli.md). Więcej informacji na temat połączeń między sieciami wirtualnymi znajduje się w sekcji [Często zadawane pytania dotyczące połączeń między sieciami wirtualnymi](#vnet-to-vnet-faq).

### <a name="example-settings"></a>Przykładowe ustawienia

**Wartości dla VNet1:**

- **Ustawienia sieci wirtualnej**
    - **Nazwa**: VNet1
    - **Przestrzeń adresowa**: 10.1.0.0/16
    - **Subskrypcja**: wybierz subskrypcję, której chcesz użyć.
    - **Grupa zasobów**: TestRG1
    - **Lokalizacja**: Wschodnie stany USA
    - **Podsieć**
        - **Nazwa**: fronton
        - **Zakres adresów**: 10.1.0.0/24
    - **Podsieć bramy**:
        - **Nazwa**: *GatewaySubnet* jest wypełniana
        - **Zakres adresów**: 10.1.255.0/27

- **Ustawienia bramy sieci wirtualnej**
    - **Nazwa**: VNet1GW
    - **Typ bramy**: Wybierz pozycję **Sieć VPN**.
    - **Typ sieci VPN**: wybierz pozycję **oparta na trasach**.
    - **Jednostka SKU**: Wybierz jednostkę SKU bramy, której chcesz użyć.
    - **Nazwa publicznego adresu IP**: VNet1GWpip
    - **Połączenie**
       - **Nazwa**: VNet1toVNet4
       - **Klucz współużytkowany**: możesz samodzielnie utworzyć klucz współużytkowany. Podczas tworzenia połączenia między sieciami wirtualnymi wartości muszą być zgodne. W tym ćwiczeniu należy użyć abc123.

**Wartości dla sieci vnet4:**

- **Ustawienia sieci wirtualnej**
   - **Nazwa**: sieci vnet4
   - **Przestrzeń adresowa**: 10.41.0.0/16
   - **Subskrypcja**: wybierz subskrypcję, której chcesz użyć.
   - **Grupa zasobów**: TestRG4
   - **Lokalizacja**: zachodnie stany USA
   - **Podsieć** 
      - **Nazwa**: fronton
      - **Zakres adresów**: 10.41.0.0/24
   - **GatewaySubnet** 
      - **Nazwa**: *GatewaySubnet* jest wypełniana
      - **Zakres adresów**: 10.41.255.0/27

- **Ustawienia bramy sieci wirtualnej** 
    - **Nazwa**: VNet4GW
    - **Typ bramy**: Wybierz pozycję **Sieć VPN**.
    - **Typ sieci VPN**: wybierz pozycję **oparta na trasach**.
    - **Jednostka SKU**: Wybierz jednostkę SKU bramy, której chcesz użyć.
    - **Nazwa publicznego adresu IP**: VNet4GWpip
    - **Połączenie** 
       - **Nazwa**: VNet4toVNet1
       - **Klucz współużytkowany**: możesz samodzielnie utworzyć klucz współużytkowany. Podczas tworzenia połączenia między sieciami wirtualnymi wartości muszą być zgodne. W tym ćwiczeniu należy użyć abc123.

## <a name="create-and-configure-vnet1"></a>Utwórz i skonfiguruj VNet1
Jeśli masz już sieć wirtualną, sprawdź, czy ustawienia są zgodne z projektem bramy sieci VPN. Zwróć szczególną uwagę na wszelkie podsieci, które mogą pokrywać się z innymi sieciami. Obecność nakładających się podsieci spowoduje, że połączenie nie będzie działać prawidłowo.

### <a name="to-create-a-virtual-network"></a>Aby utworzyć sieć wirtualną
[!INCLUDE [vpn-gateway-basic-vnet-rm-portal](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-the-vnet1-gateway"></a>Tworzenie bramy VNet1
W tym kroku zostaje utworzona brama dla sieci wirtualnej użytkownika. Tworzenie bramy często może trwać 45 minut lub dłużej, w zależności od wybranej jednostki SKU bramy. W przypadku tworzenia tej konfiguracji w ramach ćwiczenia zobacz [przykładowe ustawienia](#example-settings).

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="to-create-a-virtual-network-gateway"></a>Aby utworzyć bramę sieci wirtualnej
[!INCLUDE [vpn-gateway-add-gw-rm-portal](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="create-and-configure-vnet4"></a>Utwórz i skonfiguruj sieci vnet4
Po skonfigurowaniu VNet1 Utwórz sieci vnet4 i bramę sieci vnet4, powtarzając poprzednie kroki i zastępując wartości wartościami sieci vnet4. Nie musisz czekać do momentu zakończenia tworzenia bramy sieci wirtualnej dla usługi VNet1 przed skonfigurowaniem usługi sieci vnet4. Jeśli używasz własnych wartości, upewnij się, że przestrzenie adresowe nie pokrywają się z żadnymi sieciami wirtualnymi, z którymi chcesz się połączyć.

## <a name="configure-the-vnet1-gateway-connection"></a>Konfigurowanie połączenia bramy VNet1
Po zakończeniu bram sieci wirtualnej dla VNet1 i sieci vnet4 można utworzyć połączenia bramy sieci wirtualnej. W tej sekcji opisano tworzenie połączenia z sieci VNet1 do sieci VNet4. Następujące kroki działają tylko w przypadku sieci wirtualnych należących do tej samej subskrypcji. Jeśli Twoje sieci wirtualne znajdują się w różnych subskrypcjach, do utworzenia połączenia musisz użyć programu [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md). Jeśli jednak Twoje sieci wirtualne znajdują się w różnych grupach zasobów w tej samej subskrypcji, możesz utworzyć połączenie między nimi przy użyciu portalu.

1. W witrynie Azure Portal wybierz pozycję **Wszystkie zasoby**, wprowadź frazę *brama sieci wirtualnej* w polu wyszukiwania, a następnie przejdź do bramy sieci wirtualnej dla swojej sieci wirtualnej. Na przykład **VNet1GW**. Wybierz bramę, aby otworzyć stronę **bramy sieci wirtualnej** . W obszarze **Ustawienia**wybierz pozycję **połączenia**.

   ![Strona połączeń](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/connections.png "Strona połączeń")
2. Wybierz pozycję **+ Dodaj** , aby otworzyć stronę **Dodawanie połączenia** .

   ![Dodawanie połączenia](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/vnet1-vnet4-connection.png "Dodawanie połączenia")
3. Na stronie **Dodawanie połączenia** wprowadź wartości dla połączenia:

   - **Nazwa**: Wprowadź nazwę połączenia. Na przykład *VNet1toVNet4*.

   - **Typ połączenia**: wybierz z listy rozwijanej pozycję **Sieć wirtualna-sieć wirtualna** .

   - **Pierwsza Brama sieci wirtualnej**: to pole jest wypełniane automatycznie, ponieważ to połączenie jest tworzone z określonej bramy sieci wirtualnej.

   - **Druga Brama sieci wirtualnej**: to pole jest bramą sieci wirtualnej VNET, z którą chcesz utworzyć połączenie. Wybierz pozycję **Wybierz inną bramę sieci wirtualnej**, aby otworzyć stronę **Wybieranie bramy sieci wirtualnej**.

     - Wyświetl bramy sieci wirtualnej wymienione na tej stronie. Pamiętaj, że wyświetlane są tylko bramy sieci wirtualnej objęte subskrypcją. Jeśli chcesz połączyć się z bramą sieci wirtualnej, której nie ma w Twojej subskrypcji, użyj programu [PowerShell](vpn-gateway-vnet-vnet-rm-ps.md).

     - Wybierz bramę sieci wirtualnej, z którą chcesz nawiązać połączenie.

     - **Klucz współużytkowany (PSK)**: w tym polu Wprowadź klucz współużytkowany dla połączenia. Klucz można wygenerować lub utworzyć samodzielnie. W przypadku połączenia lokacja-lokacja używany klucz jest taki sam dla urządzenia lokalnego oraz połączenia bramy sieci wirtualnej. Koncepcja jest tutaj podobna, z tym że zamiast połączenia z urządzeniem sieci VPN następuje połączenie z inną bramą sieci wirtualnej.
    
4. Wybierz przycisk **OK**, aby zapisać zmiany.

## <a name="configure-the-vnet4-gateway-connection"></a>Konfigurowanie połączenia bramy sieci vnet4
Następnie utwórz połączenie od sieci vnet4 do VNet1. W portalu Znajdź bramę sieci wirtualnej skojarzonej z sieci vnet4. Postępuj zgodnie z instrukcjami z poprzedniej sekcji, zastępując wartości, aby utworzyć połączenie z sieci vnet4 do VNet1. Pamiętaj, aby użyć tego samego klucza współużytkowanego.

## <a name="verify-your-connections"></a>Sprawdź połączenia

1. W witrynie Azure Portal znajdź bramę sieci wirtualnej. 
2. Na stronie **Brama sieci wirtualnej** kliknij pozycję **Połączenia**, aby wyświetlić stronę **Połączenia** dla bramy sieci wirtualnej. Po nawiązaniu połączenia zostaną wyświetlone wartości **stanu** **połączona**.

   ![Weryfikuj połączenia](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/view-connections.png "Weryfikuj połączenia")
3. W kolumnie **Nazwa** wybierz jedno z połączeń, aby wyświetlić więcej informacji. Po rozpoczęciu przepływu danych zostaną wyświetlone wartości dla **danych wejściowych** i **danych wyjściowych**.

   ![Zrzut ekranu przedstawia grupę zasobów zawierającą wartości danych i danych.](./media/vpn-gateway-howto-vnet-vnet-resource-manager-portal/status.png "Stan")

## <a name="add-additional-connections"></a>Dodawanie kolejnych połączeń

Jeśli chcesz dodać więcej połączeń, przejdź do bramy sieci wirtualnej, z której chcesz utworzyć połączenie, a następnie wybierz pozycję **Połączenia**. Możesz utworzyć kolejne połączenie sieć wirtualna-sieć wirtualna lub połączenie IPsec lokacja-lokacja do lokalizacji lokalnej. Pamiętaj, aby dopasować wartość w polu **Typ połączenia** do typu połączenia, które chcesz utworzyć. Przed utworzeniem dodatkowych połączeń sprawdź, czy przestrzeń adresowa Twojej sieci wirtualnej nie nakłada się na żadne przestrzenie adresowe, z którymi chcesz nawiązać połączenie. Aby zapoznać się z procedurą tworzenia połączenia lokacja-lokacja, zobacz [Tworzenie połączenia typu lokacja-lokacja](vpn-gateway-howto-site-to-site-resource-manager-portal.md).

## <a name="vnet-to-vnet-faq"></a>Często zadawane pytania dotyczące połączeń między sieciami wirtualnymi
Ta sekcja zawiera odpowiedzi na często zadawane pytań dotyczące połączeń między sieciami wirtualnymi.

[!INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-faq-vnet-vnet-include.md)]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać informacje na temat sposobu ograniczania ruchu sieciowego do zasobów w sieci wirtualnej, zobacz [Zabezpieczenia sieci](../virtual-network/security-overview.md).

Aby uzyskać informacje na temat sposobu kierowania ruchu platformy Azure między platformą Azure a zasobami lokalnymi i internetowymi, zobacz artykuł [Routing ruchu w sieci wirtualnej](../virtual-network/virtual-networks-udr-overview.md).
