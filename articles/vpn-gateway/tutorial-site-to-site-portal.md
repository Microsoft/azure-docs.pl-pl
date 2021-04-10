---
title: 'Samouczek — łączenie sieci lokalnej z siecią wirtualną: Azure Portal'
description: Utwórz połączenie między lokacjami VPN Gateway IPsec z sieci lokalnej do sieci wirtualnej platformy Azure za pośrednictwem publicznego Internetu przy użyciu portalu.
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 12/04/2020
ms.openlocfilehash: ccb43c3e7efb9289450ad9a71c003f54e5362b66
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98945203"
---
# <a name="tutorial-create-a-site-to-site-connection-in-the-azure-portal"></a>Samouczek: Tworzenie połączenia typu lokacja-lokacja w Azure Portal

Bramy sieci VPN na platformie Azure zapewniają łączność między lokalizacjami klienta a platformą Azure. W tym samouczku pokazano, jak za pomocą Azure Portal utworzyć połączenie bramy sieci VPN typu lokacja-lokacja z sieci lokalnej z siecią wirtualną. Tę konfigurację można również utworzyć przy użyciu [Azure PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md) lub [interfejsu wiersza polecenia platformy Azure](vpn-gateway-howto-site-to-site-resource-manager-cli.md).

:::image type="content" source="./media/tutorial-site-to-site-portal/diagram.png" alt-text="Diagram połączenia bramy VPN Gateway typu lokacja-lokacja obejmującego wiele lokalizacji":::

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie sieci wirtualnej
> * Tworzenie bramy sieci VPN
> * Tworzenie bramy sieci lokalnej
> * Tworzenie połączenia sieci VPN
> * Weryfikowanie połączenia
> * Nawiązywanie połączenia z maszyną wirtualną

## <a name="prerequisites"></a>Wymagania wstępne

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

### <a name="view-the-public-ip-address"></a><a name="view"></a>Wyświetlanie publicznego adresu IP

Publiczny adres IP bramy można wyświetlić na stronie **Przegląd** bramy.

:::image type="content" source="./media/tutorial-create-gateway-portal/address.png" alt-text="Strona omówienia":::

Aby wyświetlić dodatkowe informacje na temat obiektu publicznego adresu IP, kliknij link nazwa/adres IP obok pozycji **publiczny adres IP**.

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
* Publiczny adres IP bramy sieci wirtualnej. Publiczny adres IP można wyświetlić za pomocą witryny Azure Portal, programu PowerShell lub interfejsu wiersza polecenia. Aby znaleźć publiczny adres IP bramy sieci VPN przy użyciu Azure Portal, przejdź do opcji **bramy sieci wirtualnej**, a następnie wybierz nazwę bramy.

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

## <a name="connect-to-a-virtual-machine"></a><a name="connectVM"></a>Nawiązywanie połączenia z maszyną wirtualną

[!INCLUDE [Connect to a VM](../../includes/vpn-gateway-connect-vm.md)]

## <a name="optional-steps"></a>Kroki opcjonalne

### <a name="add-additional-connections-to-the-gateway"></a><a name="addconnect"></a>Dodawanie dodatkowych połączeń do bramy

Możesz dodać dodatkowe połączenia, pod warunkiem że żadna z przestrzeni adresowych nie nakładają się między połączeniami.

1. Aby dodać dodatkowe połączenie, przejdź do bramy sieci VPN, a następnie wybierz pozycję **połączenia** , aby otworzyć stronę połączenia.
1. Wybierz pozycję **+ Dodaj** , aby dodać połączenie. Dostosuj typ połączenia, aby odzwierciedlał połączenie między sieciami wirtualnymi (jeśli nawiązywanie jest połączenie z inną bramą sieci wirtualnej) lub połączenie lokacja-lokacja.
1. Jeśli łączysz się przy użyciu połączenia lokacja-lokacja, a jeszcze nie utworzono brany sieci lokalnej dla lokacji, z którą chcesz nawiązać połączenie, możesz utworzyć nową.
1. Określ klucz współużytkowany, którego chcesz użyć, a następnie wybierz przycisk **OK** , aby utworzyć połączenie.

### <a name="resize-a-gateway-sku"></a><a name="resize"></a>Zmiana rozmiaru jednostki SKU bramy

Istnieją określone reguły dotyczące zmiany rozmiarów w porównaniu z zmianami jednostki SKU bramy. W tej sekcji zmienimy rozmiar jednostki SKU. Aby uzyskać więcej informacji, zobacz [Ustawienia bramy — zmienianie rozmiarów i zmiana jednostek SKU](vpn-gateway-about-vpn-gateway-settings.md#resizechange).

[!INCLUDE [resize a gateway](../../includes/vpn-gateway-resize-gw-portal-include.md)]

### <a name="reset-a-gateway"></a><a name="reset"></a>Resetowanie bramy

Resetowanie bramy Azure VPN Gateway przydaje się w przypadku utraty połączenia sieci VPN obejmującego wiele lokalizacji w jednym lub wielu tunelach VPN typu lokacja-lokacja. W takiej sytuacji urządzenia lokalnej sieci VPN działają prawidłowo, ale nie mogą nawiązać połączenia w ramach tuneli używających protokołu IPsec z bramami sieci VPN Azure.

[!INCLUDE [reset a gateway](../../includes/vpn-gateway-reset-gw-portal-include.md)]

### <a name="additional-configuration-considerations"></a><a name="additional"></a>Dodatkowe zagadnienia dotyczące konfiguracji

Konfiguracje S2S można dostosować na różne sposoby. Aby uzyskać więcej informacji, zobacz następujące artykuły:

* Informacje na temat protokołu BGP można znaleźć w artykułach [BGP Overview](vpn-gateway-bgp-overview.md) (Omówienie protokołu BGP) i [How to configure BGP](vpn-gateway-bgp-resource-manager-ps.md) (Konfigurowanie protokołu BGP).
* Aby uzyskać informacje o wymuszonym tunelowaniu, zobacz [Informacje o wymuszonym tunelowaniu](vpn-gateway-forced-tunneling-rm.md).
* Aby uzyskać informacje o połączeniach o wysokiej dostępności typu aktywne-aktywne, zobacz [Połączenia obejmujące wiele lokalizacji i połączenia między sieciami wirtualnymi o wysokiej dostępności](vpn-gateway-highlyavailable.md).
* Aby uzyskać informacje na temat sposobu ograniczania ruchu sieciowego do zasobów w sieci wirtualnej, zobacz [Zabezpieczenia sieci](../virtual-network/network-security-groups-overview.md).
* Aby uzyskać informacje na temat sposobu kierowania ruchu platformy Azure między platformą Azure a zasobami lokalnymi i internetowymi, zobacz artykuł [Routing ruchu w sieci wirtualnej](../virtual-network/virtual-networks-udr-overview.md).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji lub przejdź do następnego samouczka, usuń te zasoby, wykonując następujące czynności:

1. Wprowadź nazwę grupy zasobów w polu **wyszukiwania** w górnej części portalu i wybierz ją z wyników wyszukiwania.

1. Wybierz pozycję **Usuń grupę zasobów**.

1. Wprowadź grupę zasobów, aby **wpisać nazwę grupy zasobów** , a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu połączenia S2S można dodać połączenie P2S z tą samą bramą.

> [!div class="nextstepaction"]
> [Połączenia sieci VPN typu punkt-lokacja](vpn-gateway-howto-point-to-site-resource-manager-portal.md)
