---
title: 'Łączenie sieci lokalnej z siecią wirtualną platformy Azure: sieci VPN typu lokacja-lokacja: Portal'
description: Utwórz połączenie IPsec lokacja-VPN Gateway lokacja z sieci lokalnej do sieci wirtualnej platformy Azure za pośrednictwem publicznego Internetu przy użyciu portalu.
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/22/2020
ms.author: cherylmc
ms.openlocfilehash: b8c12048283cb8cc4220cb325762b272cde4a68f
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/01/2020
ms.locfileid: "92541319"
---
# <a name="create-a-site-to-site-connection-in-the-azure-portal"></a>Tworzenie połączenia typu lokacja-lokacja w witrynie Azure Portal

Ten artykuł pokazuje, jak używać witryny Azure Portal do tworzenia połączenia bramy sieci VPN lokacja-lokacja z sieci lokalnej do sieci wirtualnej. Kroki podane w tym artykule mają zastosowanie do modelu wdrażania przy użyciu usługi Resource Manager. Tę konfigurację możesz również utworzyć przy użyciu innego narzędzia wdrażania lub modelu wdrażania, wybierając inną opcję z następującej listy:

> [!div class="op_single_selector"]
> * [Witryna Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
> * [Program PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
> * [Interfejs wiersza polecenia](vpn-gateway-howto-site-to-site-resource-manager-cli.md)
> * [Portal Azure (klasyczny)](vpn-gateway-howto-site-to-site-classic-portal.md)
>

Połączenie bramy sieci VPN typu lokacja-lokacja umożliwia łączenie sieci lokalnej z siecią wirtualną platformy Azure za pośrednictwem tunelu sieci VPN IPsec/IKE (IKEv1 lub IKEv2). Ten typ połączenia wymaga lokalnego urządzenia sieci VPN z przypisanym publicznym adresem IP dostępnym z zewnątrz. Więcej informacji o bramach sieci VPN można znaleźć w artykule [Informacje dotyczące bram sieci VPN](vpn-gateway-about-vpngateways.md).

:::image type="content" source="./media/vpn-gateway-howto-site-to-site-resource-manager-portal/site-to-site-diagram.png" alt-text="Diagram połączenia bramy VPN Gateway typu lokacja-lokacja obejmującego wiele lokalizacji":::

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem konfiguracji sprawdź, czy są spełnione następujące kryteria:

* Konto platformy Azure z aktywną subskrypcją. Jeśli go nie masz, [Utwórz go bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* Upewnij się, że masz zgodne urządzenie sieci VPN i dostępna jest osoba, która umie je skonfigurować. Aby uzyskać więcej informacji o zgodnych urządzeniach sieci VPN i konfiguracji urządzeń, zobacz artykuł [Informacje o urządzeniach sieci VPN](vpn-gateway-about-vpn-devices.md).
* Sprawdź, czy masz dostępny zewnętrznie publiczny adres IPv4 urządzenia sieci VPN.
* Jeśli nie znasz zakresów adresów IP w konfiguracji swojej sieci lokalnej, skontaktuj się z osobą, która może podać Ci te dane. Tworząc tę konfigurację, musisz określić prefiksy zakresu adresów IP, które platforma Azure będzie kierować do Twojej lokalizacji lokalnej. Żadna z podsieci sieci lokalnej nie może się nakładać na podsieci sieci wirtualnej, z którymi chcesz nawiązać połączenie.

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną (VNet) przy użyciu następujących wartości:

* **Grupa zasobów:** TestRG1
* **Nazwa:** VNet1
* **Region:** (US) Wschodnie stany USA
* **Przestrzeń adresów IPv4:** 10.1.0.0/16
* **Nazwa podsieci:** Frontonu
* **Przestrzeń adresowa podsieci:** 10.1.0.0/24

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-a-vpn-gateway"></a><a name="VNetGateway"></a>Tworzenie bramy sieci VPN

W tym kroku zostaje utworzona brama dla sieci wirtualnej użytkownika. Tworzenie bramy często może trwać 45 minut lub dłużej, w zależności od wybranej jednostki SKU bramy.

### <a name="about-the-gateway-subnet"></a>Informacje o podsieci bramy

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

### <a name="create-the-gateway"></a>Tworzenie bramy

Utwórz bramę sieci VPN przy użyciu następujących wartości:

* **Nazwa:** VNet1GW
* **Region:** Wschodnie stany USA
* **Typ bramy:** VPN
* **Typ sieci VPN:** oparta na trasach
* **Jednostka SKU:** VpnGw1
* **Generowanie:** Generation1
* **Sieć wirtualna:** VNet1
* **Zakres adresów podsieci bramy:** 10.1.255.0/27
* **Publiczny adres IP:** Utwórz nowy
* **Publiczny adres IP:** VNet1GWpip
* **Włącz tryb aktywny-aktywny:** Wyłączony
* **Konfigurowanie protokołu BGP:** Wyłączony

[!INCLUDE [Create a vpn gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="create-a-local-network-gateway"></a><a name="LocalNetworkGateway"></a>Tworzenie bramy sieci lokalnej

Brama sieci lokalnej jest określonym obiektem, który reprezentuje lokalizację lokalną (lokację) do celów routingu. Nadaj lokacji nazwę, za pomocą której platforma Azure może odwołać się do niej, a następnie określ adres IP lokalnego urządzenia sieci VPN, z którym będzie tworzone połączenie. Określ również prefiksy adresów IP, które będą kierowane za pośrednictwem bramy sieci VPN do urządzenia sieci VPN. Określone prefiksy adresów są prefiksami znajdującymi się w Twojej sieci lokalnej. W przypadku zmiany sieci lokalnej lub jeśli trzeba zmienić publiczny adres IP urządzenia sieci VPN, można łatwo zaktualizować wartości później.

Utwórz bramę sieci lokalnej przy użyciu następujących wartości:

* **Nazwa:** Site1
* **Grupa zasobów:** TestRG1
* **Lokalizacja:** Wschodnie stany USA

[!INCLUDE [Add a local network gateway](../../includes/vpn-gateway-add-local-network-gateway-portal-include.md)]

## <a name="configure-your-vpn-device"></a><a name="VPNDevice"></a>Konfiguracja urządzenia sieci VPN

Połączenia typu lokacja-lokacja z siecią lokalną wymagają urządzenia sieci VPN. W tym kroku konfigurowane jest urządzenie sieci VPN. Podczas konfigurowania urządzenia sieci VPN potrzebne są następujące wartości:

* Klucz współużytkowany. To ten sam klucz współużytkowany, który jest określany podczas tworzenia połączenia sieci VPN typu lokacja-lokacja. W naszych przykładach używamy podstawowego klucza współużytkowanego. Zalecamy, aby do użycia wygenerować bardziej złożony klucz.
* Publiczny adres IP bramy sieci wirtualnej. Publiczny adres IP można wyświetlić za pomocą witryny Azure Portal, programu PowerShell lub interfejsu wiersza polecenia. Aby znaleźć publiczny adres IP bramy sieci VPN przy użyciu Azure Portal, przejdź do opcji **bramy sieci wirtualnej** , a następnie wybierz nazwę bramy.

[!INCLUDE [Configure a VPN device](../../includes/vpn-gateway-configure-vpn-device-include.md)]

## <a name="create-a-vpn-connection"></a><a name="CreateConnection"></a>Tworzenie połączenia sieci VPN

Utwórz połączenie sieci VPN typu lokacja-lokacja między bramą sieci wirtualnej a lokalnym urządzeniem sieci VPN.

Utwórz połączenie, używając następujących wartości:

* **Nazwa bramy sieci lokalnej:** Site1
* **Nazwa połączenia:** VNet1toSite1
* **Klucz współużytkowany:** w tym przykładzie użyjemy klucza abc123. Jednak możesz użyć dowolnej wartości zgodnej ze sprzętem sieci VPN. Ważne, żeby wartości były zgodne po obu stronach połączenia.

[!INCLUDE [Add a site-to-site connection](../../includes/vpn-gateway-add-site-to-site-connection-portal-include.md)]

## <a name="verify-the-vpn-connection"></a><a name="VerifyConnection"></a>Sprawdzenie połączenia sieci VPN

[!INCLUDE [Verify the connection](../../includes/vpn-gateway-verify-connection-portal-include.md)]

## <a name="how-to-connect-to-a-virtual-machine"></a><a name="connectVM"></a>Jak nawiązać połączenie z maszyną wirtualną

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

## <a name="how-to-reset-a-vpn-gateway"></a><a name="reset"></a>Jak zresetować bramę VPN Gateway

Resetowanie bramy Azure VPN Gateway przydaje się w przypadku utraty połączenia sieci VPN obejmującego wiele lokalizacji w jednym lub wielu tunelach VPN typu lokacja-lokacja. W takiej sytuacji urządzenia lokalnej sieci VPN działają prawidłowo, ale nie mogą nawiązać połączenia w ramach tuneli używających protokołu IPsec z bramami sieci VPN Azure. Aby uzyskać instrukcje, zobacz [Resetowanie bramy VPN Gateway](reset-gateway.md).

## <a name="how-to-change-a-gateway-sku-resize-a-gateway"></a><a name="resize"></a>Jak zmienić jednostkę SKU bramy (zmienić rozmiar bramy)

Aby uzyskać instrukcje dotyczące zmiany jednostki SKU bramy, zobacz [Gateway SKUs (Jednostki SKU bramy)](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

## <a name="how-to-add-an-additional-connection-to-a-vpn-gateway"></a><a name="addconnect"></a>Jak dodać dodatkowe połączenie z bramą sieci VPN

Możesz dodać dodatkowe połączenia, pod warunkiem że żadna z przestrzeni adresowych nie nakładają się między połączeniami.

1. Aby dodać dodatkowe połączenie, przejdź do bramy sieci VPN, a następnie wybierz pozycję **połączenia** , aby otworzyć stronę połączenia.
1. Wybierz pozycję **+ Dodaj** , aby dodać połączenie. Dostosuj typ połączenia, aby odzwierciedlał połączenie między sieciami wirtualnymi (jeśli nawiązywanie jest połączenie z inną bramą sieci wirtualnej) lub połączenie lokacja-lokacja.
1. Jeśli łączysz się przy użyciu połączenia lokacja-lokacja, a jeszcze nie utworzono brany sieci lokalnej dla lokacji, z którą chcesz nawiązać połączenie, możesz utworzyć nową.
1. Określ klucz współużytkowany, którego chcesz użyć, a następnie wybierz przycisk **OK** , aby utworzyć połączenie.

## <a name="next-steps"></a>Następne kroki

* Informacje na temat protokołu BGP można znaleźć w artykułach [BGP Overview](vpn-gateway-bgp-overview.md) (Omówienie protokołu BGP) i [How to configure BGP](vpn-gateway-bgp-resource-manager-ps.md) (Konfigurowanie protokołu BGP).
* Aby uzyskać informacje o wymuszonym tunelowaniu, zobacz [Informacje o wymuszonym tunelowaniu](vpn-gateway-forced-tunneling-rm.md).
* Aby uzyskać informacje o połączeniach o wysokiej dostępności typu aktywne-aktywne, zobacz [Połączenia obejmujące wiele lokalizacji i połączenia między sieciami wirtualnymi o wysokiej dostępności](vpn-gateway-highlyavailable.md).
* Aby uzyskać informacje na temat sposobu ograniczania ruchu sieciowego do zasobów w sieci wirtualnej, zobacz [Zabezpieczenia sieci](../virtual-network/security-overview.md).
* Aby uzyskać informacje na temat sposobu kierowania ruchu platformy Azure między platformą Azure a zasobami lokalnymi i internetowymi, zobacz artykuł [Routing ruchu w sieci wirtualnej](../virtual-network/virtual-networks-udr-overview.md).
* Aby uzyskać informacje dotyczące tworzenia połączenia sieci VPN typu lokacja-lokacja za pomocą szablonu usługi Azure Resource Manager, zobacz [Create a Site-to-Site VPN Connection (Tworzenie połączenia sieci VPN typu lokacja-lokacja)](https://azure.microsoft.com/resources/templates/101-site-to-site-vpn-create/).
* Aby uzyskać informacje dotyczące tworzenia połączenia sieci VPN typu sieć wirtualna-sieć wirtualna przy użyciu szablonu Azure Resource Manager, zobacz [wdrażanie replikacji geograficznej HBase](https://azure.microsoft.com/resources/templates/101-hdinsight-hbase-replication-geo/).
