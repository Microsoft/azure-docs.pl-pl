---
title: 'Łączenie sieci lokalnej z siecią wirtualną platformy Azure: sieci VPN typu lokacja-lokacja: Portal'
description: Utwórz połączenie IPsec lokacja-VPN Gateway lokacja z sieci lokalnej do sieci wirtualnej platformy Azure za pośrednictwem publicznego Internetu przy użyciu portalu.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/10/2020
ms.author: cherylmc
ms.openlocfilehash: bbfd00af62f783b0f5122ddb59b921c1ad9e7019
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92331230"
---
# <a name="create-a-site-to-site-connection-in-the-azure-portal"></a>Tworzenie połączenia typu lokacja-lokacja w witrynie Azure Portal

Ten artykuł pokazuje, jak używać witryny Azure Portal do tworzenia połączenia bramy sieci VPN lokacja-lokacja z sieci lokalnej do sieci wirtualnej. Kroki podane w tym artykule mają zastosowanie do modelu wdrażania przy użyciu usługi Resource Manager. Tę konfigurację możesz również utworzyć przy użyciu innego narzędzia wdrażania lub modelu wdrażania, wybierając inną opcję z następującej listy:

> [!div class="op_single_selector"]
> * [Witryna Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Program PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Interfejs wiersza polecenia](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Portal Azure (klasyczny)](vpn-gateway-howto-site-to-site-classic-portal.md)
> 
>

Połączenie bramy sieci VPN typu lokacja-lokacja umożliwia łączenie sieci lokalnej z siecią wirtualną platformy Azure za pośrednictwem tunelu sieci VPN IPsec/IKE (IKEv1 lub IKEv2). Ten typ połączenia wymaga lokalnego urządzenia sieci VPN z przypisanym publicznym adresem IP dostępnym z zewnątrz. Więcej informacji o bramach sieci VPN można znaleźć w artykule [Informacje dotyczące bram sieci VPN](vpn-gateway-about-vpngateways.md).

![Diagram połączenia bramy VPN Gateway typu lokacja-lokacja obejmującego wiele lokalizacji](./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png)

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed rozpoczęciem konfiguracji sprawdź, czy są spełnione następujące kryteria:

* Upewnij się, że masz zgodne urządzenie sieci VPN i dostępna jest osoba, która umie je skonfigurować. Aby uzyskać więcej informacji o zgodnych urządzeniach sieci VPN i konfiguracji urządzeń, zobacz artykuł [Informacje o urządzeniach sieci VPN](vpn-gateway-about-vpn-devices.md).
* Sprawdź, czy masz dostępny zewnętrznie publiczny adres IPv4 urządzenia sieci VPN.
* Jeśli nie znasz zakresów adresów IP w konfiguracji swojej sieci lokalnej, skontaktuj się z osobą, która może podać Ci te dane. Tworząc tę konfigurację, musisz określić prefiksy zakresu adresów IP, które platforma Azure będzie kierować do Twojej lokalizacji lokalnej. Żadna z podsieci sieci lokalnej nie może się nakładać na podsieci sieci wirtualnej, z którymi chcesz nawiązać połączenie. 

### <a name="example-values"></a><a name="values"></a>Przykładowe wartości

W przykładach w tym artykule są stosowane następujące wartości. Tych wartości możesz użyć do tworzenia środowiska testowego lub odwoływać się do nich, aby lepiej zrozumieć przykłady w niniejszym artykule. Aby uzyskać więcej informacji o typach bram sieci VPN, zobacz [About VPN Gateway configuration settings (Informacje o ustawieniach konfiguracji bramy sieci VPN)](vpn-gateway-about-vpn-gateway-settings.md).

* **Nazwa sieci wirtualnej:** VNet1
* **Przestrzeń adresowa:** 10.1.0.0/16
* **Subskrypcja:** subskrypcja, której chcesz użyć
* **Grupa zasobów:** TestRG1
* **Region:** Wschodnie stany USA
* **Podsieć:** FrontEnd: 10.1.0.0/24, BackEnd: 10.1.1.0/24 (opcjonalnie w tym ćwiczeniu)
* **Zakres adresów podsieci bramy:** 10.1.255.0/27
* **Nazwa bramy sieci wirtualnej:** VNet1GW
* **Publiczny adres IP:** VNet1GWpip
* **Typ sieci VPN:** oparta na trasach
* **Typ połączenia:** Lokacja-lokacja (IPsec)
* **Typ bramy:** VPN
* **Nazwa bramy sieci lokalnej:** Site1
* **Nazwa połączenia:** VNet1toSite1
* **Klucz współużytkowany:** w tym przykładzie użyjemy klucza abc123. Jednak możesz użyć dowolnej wartości zgodnej ze sprzętem sieci VPN. Ważne, żeby wartości były zgodne po obu stronach połączenia.

## <a name="1-create-a-virtual-network"></a><a name="CreatVNet"></a>1. Tworzenie sieci wirtualnej

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="2-create-the-vpn-gateway"></a><a name="VNetGateway"></a>2. Tworzenie bramy sieci VPN

W tym kroku zostaje utworzona brama dla sieci wirtualnej użytkownika. Tworzenie bramy często może trwać 45 minut lub dłużej, w zależności od wybranej jednostki SKU bramy.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="example-settings"></a>Przykładowe ustawienia

* **Szczegóły wystąpienia > region:** Wschodnie stany USA
* **Virtual Network > sieci wirtualnej:** VNet1
* **Nazwa > szczegółów wystąpienia:** VNet1GW
* **Szczegóły wystąpienia > typ bramy:** POŁĄCZENIE
* **Szczegóły wystąpienia > sieci VPN:** Oparta na trasach
* **Zakres adresów podsieci bramy > Virtual Network:** 10.1.255.0/27
* **Publiczny adres ip > nazwa publicznego adresu IP:** VNet1GWpip

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]


## <a name="3-create-the-local-network-gateway"></a><a name="LocalNetworkGateway"></a>3. Utwórz bramę sieci lokalnej

Brama sieci lokalnej jest określonym obiektem, który reprezentuje lokalizację lokalną (lokację) do celów routingu. Nadaj lokacji nazwę, za pomocą której platforma Azure może odwołać się do niej, a następnie określ adres IP lokalnego urządzenia sieci VPN, z którym będzie tworzone połączenie. Określ również prefiksy adresów IP, które będą kierowane za pośrednictwem bramy sieci VPN do urządzenia sieci VPN. Określone prefiksy adresów są prefiksami znajdującymi się w Twojej sieci lokalnej. W przypadku zmiany sieci lokalnej lub jeśli trzeba zmienić publiczny adres IP urządzenia sieci VPN, można łatwo zaktualizować wartości później.

**Przykładowe wartości**

* **Nazwa:** Site1
* **Grupa zasobów:** TestRG1
* **Lokalizacja:** Wschodnie stany USA


[!INCLUDE [Add a local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-include.md)]

## <a name="4-configure-your-vpn-device"></a><a name="VPNDevice"></a>4. Skonfiguruj urządzenie sieci VPN

Połączenia typu lokacja-lokacja z siecią lokalną wymagają urządzenia sieci VPN. W tym kroku konfigurowane jest urządzenie sieci VPN. Podczas konfigurowania urządzenia sieci VPN potrzebne będą:

- Klucz współużytkowany. To ten sam klucz współużytkowany, który jest określany podczas tworzenia połączenia sieci VPN typu lokacja-lokacja. W naszych przykładach używamy podstawowego klucza współużytkowanego. Zalecamy, aby do użycia wygenerować bardziej złożony klucz.
- Publiczny adres IP bramy sieci wirtualnej. Publiczny adres IP można wyświetlić za pomocą witryny Azure Portal, programu PowerShell lub interfejsu wiersza polecenia. Aby znaleźć publiczny adres IP bramy sieci VPN za pomocą witryny Azure Portal, przejdź do pozycji **Bramy sieci wirtualnej**, a następnie kliknij nazwę bramy.

[!INCLUDE [Configure a VPN device](../../includes/vpn-gateway-configure-vpn-device-include.md)]

## <a name="5-create-the-vpn-connection"></a><a name="CreateConnection"></a>5. Tworzenie połączenia sieci VPN

Utwórz połączenie sieci VPN typu lokacja-lokacja między bramą sieci wirtualnej a lokalnym urządzeniem sieci VPN.

[!INCLUDE [Add a site-to-site connection](../../includes/vpn-gateway-add-site-to-site-connection-portal-include.md)]

## <a name="6-verify-the-vpn-connection"></a><a name="VerifyConnection"></a>6. Sprawdź połączenie sieci VPN

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Nawiązywanie połączenia z maszyną wirtualną

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm-s2s-include.md)]

## <a name="how-to-reset-a-vpn-gateway"></a><a name="reset"></a>Jak zresetować bramę VPN Gateway

Resetowanie bramy Azure VPN Gateway przydaje się w przypadku utraty połączenia sieci VPN obejmującego wiele lokalizacji w jednym lub wielu tunelach VPN typu lokacja-lokacja. W takiej sytuacji urządzenia lokalnej sieci VPN działają prawidłowo, ale nie mogą nawiązać połączenia w ramach tuneli używających protokołu IPsec z bramami sieci VPN Azure. Aby uzyskać instrukcje, zobacz [Resetowanie bramy VPN Gateway](vpn-gateway-resetgw-classic.md).

## <a name="how-to-change-a-gateway-sku-resize-a-gateway"></a><a name="resize"></a>Jak zmienić jednostkę SKU bramy (zmienić rozmiar bramy)

Aby uzyskać instrukcje dotyczące zmiany jednostki SKU bramy, zobacz [Gateway SKUs (Jednostki SKU bramy)](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

## <a name="how-to-add-an-additional-connection-to-a-vpn-gateway"></a><a name="addconnect"></a>Jak dodać dodatkowe połączenie z bramą sieci VPN

Możesz dodać dodatkowe połączenia, pod warunkiem że żadna z przestrzeni adresowych nie nakładają się między połączeniami.

1. Aby dodać dodatkowe połączenie, przejdź do bramy sieci VPN, a następnie kliknij pozycję **Połączenia**, aby otworzyć stronę Połączenia.
2. Kliknij pozycję **+Dodaj**, aby dodać połączenie. Dostosuj typ połączenia, aby odzwierciedlał połączenie między sieciami wirtualnymi (jeśli nawiązywanie jest połączenie z inną bramą sieci wirtualnej) lub połączenie lokacja-lokacja.
3. Jeśli łączysz się przy użyciu połączenia lokacja-lokacja, a jeszcze nie utworzono brany sieci lokalnej dla lokacji, z którą chcesz nawiązać połączenie, możesz utworzyć nową.
4. Określ klucz współużytkowany, którego chcesz użyć, a następnie kliknij przycisk **OK**, aby utworzyć połączenie.

## <a name="next-steps"></a>Następne kroki

* Informacje na temat protokołu BGP można znaleźć w artykułach [BGP Overview](vpn-gateway-bgp-overview.md) (Omówienie protokołu BGP) i [How to configure BGP](vpn-gateway-bgp-resource-manager-ps.md) (Konfigurowanie protokołu BGP).
* Aby uzyskać informacje o wymuszonym tunelowaniu, zobacz [Informacje o wymuszonym tunelowaniu](vpn-gateway-forced-tunneling-rm.md).
* Aby uzyskać informacje o połączeniach o wysokiej dostępności typu aktywne-aktywne, zobacz [Połączenia obejmujące wiele lokalizacji i połączenia między sieciami wirtualnymi o wysokiej dostępności](vpn-gateway-highlyavailable.md).
* Aby uzyskać informacje na temat sposobu ograniczania ruchu sieciowego do zasobów w sieci wirtualnej, zobacz [Zabezpieczenia sieci](../virtual-network/security-overview.md).
* Aby uzyskać informacje na temat sposobu kierowania ruchu platformy Azure między platformą Azure a zasobami lokalnymi i internetowymi, zobacz artykuł [Routing ruchu w sieci wirtualnej](../virtual-network/virtual-networks-udr-overview.md).
* Aby uzyskać informacje dotyczące tworzenia połączenia sieci VPN typu lokacja-lokacja za pomocą szablonu usługi Azure Resource Manager, zobacz [Create a Site-to-Site VPN Connection (Tworzenie połączenia sieci VPN typu lokacja-lokacja)](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/).
* Aby uzyskać informacje dotyczące tworzenia połączenia sieci VPN typu sieć wirtualna-sieć wirtualna za pomocą szablonu usługi Azure Resource Manager, zobacz [Deploy HBase geo replication (Wdrażanie replikacji geograficznej bazy danych HBase)](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/).
