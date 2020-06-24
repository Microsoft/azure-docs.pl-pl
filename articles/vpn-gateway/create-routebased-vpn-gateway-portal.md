---
title: 'Tworzenie bramy sieci VPN opartej na trasach: Portal'
titleSuffix: Azure VPN Gateway
description: Tworzenie VPN Gateway opartej na trasach przy użyciu Azure Portal
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 03/03/2020
ms.author: cherylmc
ms.openlocfilehash: 4409df576c23e0dbc3e663cc348b45bc23ad5460
ms.sourcegitcommit: 55b2bbbd47809b98c50709256885998af8b7d0c5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2020
ms.locfileid: "84987719"
---
# <a name="create-a-route-based-vpn-gateway-using-the-azure-portal"></a>Tworzenie bramy sieci VPN opartej na trasach za pomocą Azure Portal

Ten artykuł pomaga w szybkim tworzeniu bramy sieci VPN opartej na trasach przy użyciu Azure Portal.  Brama sieci VPN jest używana podczas tworzenia połączenia sieci VPN z siecią lokalną. Do łączenia się z usługą sieci wirtualnych można także użyć bramy sieci VPN. 

Kroki opisane w tym artykule spowodują utworzenie sieci wirtualnej, podsieci, podsieci bramy i bramy sieci VPN opartej na trasach (bramy sieci wirtualnej). Po zakończeniu tworzenia bramy można utworzyć połączenia. Te kroki wymagają subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Tworzenie sieci wirtualnej

[!INCLUDE [Basic Point-to-Site VNet](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="configure-and-create-the-gateway"></a><a name="gwvalues"></a>Konfigurowanie i tworzenie bramy

W tym kroku zostaje utworzona brama dla sieci wirtualnej użytkownika. Tworzenie bramy często może trwać 45 minut lub dłużej, w zależności od wybranej jednostki SKU bramy.

[!INCLUDE [About gateway subnets](../../includes/vpn-gateway-about-gwsubnet-portal-include.md)]

[!INCLUDE [Create a gateway](../../includes/vpn-gateway-add-gw-rm-portal-include.md)]

>[!NOTE]
>Jednostka SKU bramy podstawowej nie obsługuje uwierzytelniania IKEv2 ani RADIUS. Jeśli planujesz, aby klienci z systemem Mac mogli łączyć się z siecią wirtualną, nie używaj podstawowej jednostki SKU.

[!INCLUDE [NSG warning](../../includes/vpn-gateway-no-nsg-include.md)]

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>Wyświetlanie bramy sieci VPN

1. Po utworzeniu bramy przejdź do VNet1 w portalu. Brama sieci VPN jest wyświetlana na stronie Przegląd jako połączone urządzenie.

   ![Połączone urządzenia](./media/create-routebased-vpn-gateway-portal/view-connected-devices.png "Połączone urządzenia")

2. Na liście urządzeń kliknij pozycję **VNet1GW** , aby wyświetlić więcej informacji.

   ![Wyświetlanie bramy sieci VPN](./media/create-routebased-vpn-gateway-portal/view-gateway.png "Wyświetlanie bramy sieci VPN")

## <a name="next-steps"></a>Następne kroki

Po zakończeniu tworzenia bramy można utworzyć połączenie między Twoją siecią wirtualną a inną. Można też utworzyć połączenie między siecią wirtualną i lokalizacją lokalną.

> [!div class="nextstepaction"]
> [Tworzenie połączenia typu lokacja-lokacja](vpn-gateway-howto-site-to-site-resource-manager-portal.md)<br><br>
> [Tworzenie połączenia typu punkt-lokacja](vpn-gateway-howto-point-to-site-resource-manager-portal.md)<br><br>
> [Utwórz połączenie z inną siecią wirtualną](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
