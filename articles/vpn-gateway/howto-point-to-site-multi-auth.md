---
title: 'Łączenie się z siecią wirtualną przy użyciu sieci VPN P2S & wielu typów uwierzytelniania: Portal'
titleSuffix: Azure VPN Gateway
description: Połącz się z siecią wirtualną za pośrednictwem P2S przy użyciu wielu typów uwierzytelniania w Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 02/22/2021
ms.author: cherylmc
ms.openlocfilehash: d405f4b10808b7d39c0d116f2c9006c85532b4f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101746707"
---
# <a name="configure-a-point-to-site-vpn-connection-to-a-vnet-using-multiple-authentication-types-azure-portal"></a>Skonfiguruj połączenie sieci VPN typu punkt-lokacja z siecią wirtualną przy użyciu wielu typów uwierzytelniania: Azure Portal

W tym artykule opisano sposób bezpiecznego łączenia poszczególnych klientów z systemem Windows, Linux lub macOS do sieci wirtualnej platformy Azure. Połączenia sieci VPN typu punkt-lokacja przydają się w przypadku, gdy celem użytkownika jest połączenie się z siecią wirtualną z lokalizacji zdalnej, podczas pracy zdalnej z domu lub konferencji. Możesz również użyć połączenia typu punkt-lokacja zamiast połączenia sieci VPN typu lokacja-lokacja w przypadku niewielkiej liczby klientów, którzy muszą się łączyć z siecią wirtualną. Połączenia typu punkt-lokacja nie wymagają urządzenia sieci VPN ani publicznego adresu IP. Połączenie typu punkt-lokacja tworzy połączenie sieci VPN nawiązywane za pośrednictwem protokołu SSTP (Secure Socket Tunneling Protocol) lub IKEv2. Aby uzyskać więcej informacji na temat połączeń sieci VPN typu punkt-lokacja, zobacz [About Point-to-Site VPN (Informacje o sieci VPN typu punkt-lokacja)](point-to-site-about.md).

:::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/point-to-site-diagram.png" alt-text="Łączenie się z komputera z diagramem połączeń sieci wirtualnej platformy Azure":::

Więcej informacji o sieci VPN typu punkt-lokacja znajduje się w temacie [Informacje o sieci VPN typu punkt-lokacja](point-to-site-about.md). Aby utworzyć tę konfigurację przy użyciu Azure PowerShell, zobacz [Konfigurowanie sieci VPN typu punkt-lokacja przy użyciu Azure PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md).

## <a name="prerequisites"></a>Wymagania wstępne

Sprawdź, czy masz subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial).

Wiele typów uwierzytelniania w tej samej bramie sieci VPN jest obsługiwanych tylko z typem tunelu OpenVPN.

### <a name="example-values"></a><a name="example"></a>Przykładowe wartości

Następujących wartości możesz użyć do tworzenia środowiska testowego lub odwoływać się do tych wartości, aby lepiej zrozumieć przykłady w niniejszym artykule:

* **Nazwa sieci wirtualnej:** VNet1
* **Przestrzeń adresowa:** 10.1.0.0/16<br>W tym przykładzie zostanie wykorzystana tylko jedna przestrzeń adresowa. Istnieje możliwość użycia więcej niż jednej przestrzeni adresowej dla sieci wirtualnej.
* **Nazwa podsieci:** Frontonu
* **Zakres adresów podsieci:** 10.1.0.0/24
* **Subskrypcja:** jeśli masz więcej niż jedną subskrypcję, sprawdź, czy korzystasz z właściwej.
* **Grupa zasobów:** TestRG1
* **Lokalizacja:** Wschodnie stany USA
* **GatewaySubnet:** 10.1.255.0/27<br>
* **Nazwa bramy sieci wirtualnej:** VNet1GW
* **Typ bramy:** VPN
* **Typ sieci VPN:** oparta na trasach
* **Publiczny adres IP:** VNet1GWpip
* **Typ połączenia:** Punkt-lokacja
* **Pula adresów klienta:** 172.16.201.0/24<br>Klienci sieci VPN połączeni z siecią wirtualną, którzy korzystają z tego połączenia punkt-lokacja, otrzymują adresy IP z puli adresów klientów.

## <a name="create-a-virtual-network"></a><a name="createvnet"></a>Tworzenie sieci wirtualnej

Przed rozpoczęciem sprawdź, czy masz subskrypcję platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) lub utworzyć [bezpłatne konto](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [About cross-premises addresses](../../includes/vpn-gateway-cross-premises.md)]

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="virtual-network-gateway"></a><a name="creategw"></a>Brama sieci wirtualnej

W tym kroku zostaje utworzona brama dla sieci wirtualnej użytkownika. Tworzenie bramy często może trwać 45 minut lub dłużej, w zależności od wybranej jednostki SKU bramy.

>[!NOTE]
>Jednostka SKU bramy podstawowej nie obsługuje typu tunelu OpenVPN.
>

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

## <a name="client-address-pool"></a><a name="addresspool"></a>Pula adresów klienta

Pula adresów klienta to określony przez Ciebie zakres prywatnych adresów IP. Klienci łączący się dynamicznie przez połączenie VPN typu punkt-lokacja otrzymują adres IP z tego zakresu. Używaj zakresu prywatnych adresów IP nienakładającego się na lokalizację lokalną, z której się łączysz, ani na sieć wirtualną, z którą chcesz się łączyć. W przypadku skonfigurowania wielu protokołów i protokołu SSTP jest jednym z protokołów, skonfigurowana Pula adresów będzie równo dzielona między skonfigurowanymi protokołami.

1. Po utworzeniu bramy sieci wirtualnej przejdź do sekcji **Ustawienia** na stronie bramy sieci wirtualnej. W obszarze **Ustawienia** wybierz pozycję **Konfiguracja punktu do lokacji**. Wybierz pozycję **Konfiguruj teraz** , aby otworzyć stronę konfiguracja.

   :::image type="content" source="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png" alt-text="Zrzut ekranu przedstawiający stronę konfiguracji typu punkt-lokacja." lightbox="./media/vpn-gateway-howto-point-to-site-resource-manager-portal/configure-now.png":::
1. Na stronie **Konfiguracja typu punkt-lokacja** można skonfigurować różne ustawienia. W polu **Pula adresów** Dodaj prywatny zakres adresów IP, który ma być używany. Klienci sieci VPN dynamicznie otrzymują adres IP z określonego zakresu. Minimalna maska podsieci to 29 bitów dla aktywnych/pasywnych i 28 bitów w przypadku konfiguracji Active/Active.

   :::image type="content" source="./media/howto-point-to-site-multi-auth/address.jpg" alt-text="Zrzut ekranu puli adresów.":::

1. Przejdź do następnej sekcji, aby skonfigurować uwierzytelnianie i typy tuneli.

## <a name="authentication-and-tunnel-types"></a><a name="type"></a>Typy uwierzytelniania i tunelowania

W tej sekcji należy skonfigurować typ uwierzytelniania i typ tunelu. Jeśli nie widzisz **typu tunelu** lub **typu uwierzytelniania** na stronie **Konfiguracja punktu do lokacji** , brama używa podstawowej jednostki SKU. Podstawowa jednostka SKU nie obsługuje uwierzytelniania za pomocą protokołu IKEv2 ani usługi RADIUS. Aby użyć tych ustawień, należy usunąć i ponownie utworzyć bramę przy użyciu innej jednostki SKU bramy.

   :::image type="content" source="./media/howto-point-to-site-multi-auth/multiauth.jpg" alt-text="Zrzut ekranu typu uwierzytelniania.":::

### <a name="tunnel-type"></a><a name="tunneltype"></a>Typ tunelu

Na stronie **Konfiguracja punktu do lokacji** wybierz pozycję **OpenVPN (SSL)** jako typ tunelu.

### <a name="authentication-type"></a><a name="authenticationtype"></a>Typ uwierzytelniania

W polu **Typ uwierzytelniania** wybierz odpowiednie typy. Dostępne opcje:

* Certyfikat platformy Azure
* RADIUS
* Azure Active Directory

W zależności od wybranych typów uwierzytelniania zobaczysz inne pola ustawienia konfiguracji, które będą musiały zostać wypełnione. Wypełnij wymagane informacje i wybierz pozycję **Zapisz** w górnej części strony, aby zapisać wszystkie ustawienia konfiguracji.

Aby uzyskać więcej informacji na temat typu uwierzytelniania, zobacz:

* [Certyfikat platformy Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md#type)
* [RADIUS](point-to-site-how-to-radius-ps.md)
* [Azure Active Directory](openvpn-azure-ad-tenant.md)

## <a name="vpn-client-configuration-package"></a><a name="clientconfig"></a>Pakiet konfiguracji klienta VPN

Klienci sieci VPN muszą mieć skonfigurowane ustawienia konfiguracji klienta. Pakiet konfiguracji klienta sieci VPN zawiera pliki z ustawieniami służącymi do konfigurowania klientów sieci VPN w celu nawiązania połączenia z siecią wirtualną za pośrednictwem połączenia usługi P2S.

Aby uzyskać instrukcje dotyczące generowania i instalowania plików konfiguracji klienta sieci VPN, należy użyć artykułu dotyczącego konfiguracji:

* [Tworzenie i Instalowanie plików konfiguracji klienta sieci VPN dla natywnych konfiguracji P2S uwierzytelniania certyfikatu platformy Azure](point-to-site-vpn-client-configuration-azure-cert.md).
* [Uwierzytelnianie Azure Active Directory: Skonfiguruj klienta VPN dla połączeń protokołu P2S OpenVPN](openvpn-azure-ad-client.md).

## <a name="point-to-site-faq"></a><a name="faq"></a>Często zadawane pytania dotyczące połączeń punkt-lokacja

Ta sekcja zawiera informacje dotyczące często zadawanych pytań dotyczących konfiguracji połączeń punkt-lokacja. Możesz również wyświetlić [VPN Gateway często zadawane pytania](vpn-gateway-vpn-faq.md) , aby uzyskać dodatkowe informacje dotyczące VPN Gateway.

[!INCLUDE [Point-to-Site FAQ](../../includes/vpn-gateway-faq-p2s-azurecert-include.md)]

## <a name="next-steps"></a>Następne kroki

Po zakończeniu procesu nawiązywania połączenia można dodać do sieci wirtualnych maszyny wirtualne. Aby uzyskać więcej informacji, zobacz [Virtual Machines](../index.yml) (Maszyny wirtualne). Aby dowiedzieć się więcej o sieci i maszynach wirtualnych, zobacz [Azure and Linux VM network overview](../virtual-machines/network-overview.md) (Omówienie sieci maszyny wirtualnej z systemem Linux i platformy Azure).

Aby uzyskać informacje dotyczące rozwiązywania problemów z połączeniem typu punkt-lokacja, zobacz [Troubleshooting Azure point-to-site connections (Rozwiązywanie problemów z połączeniami typu punkt-lokacja na platformie Azure)](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
