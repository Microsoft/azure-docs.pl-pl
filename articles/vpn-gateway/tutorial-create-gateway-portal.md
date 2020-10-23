---
title: 'Samouczek — Tworzenie VPN Gateway i zarządzanie nim: Azure Portal'
description: Postępuj zgodnie z tym samouczkiem, aby dowiedzieć się, jak tworzyć i wdrażać VPN Gateway platformy Azure oraz zarządzać nimi przy użyciu portalu
author: cherylmc
ms.author: cherylmc
ms.service: vpn-gateway
ms.topic: tutorial
ms.date: 10/21/2020
ms.openlocfilehash: 41bb69d675f3aa45061dab3236eae5c869bc346b
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92460771"
---
# <a name="tutorial-create-and-manage-a-vpn-gateway-using-azure-portal"></a>Samouczek: Tworzenie bramy VPN Gateway i zarządzanie nią przy użyciu Azure Portal

Bramy sieci VPN na platformie Azure zapewniają łączność między lokalizacjami klienta a platformą Azure. Ten samouczek obejmuje podstawowe etapy wdrażania bramy sieci VPN, takie jak tworzenie bramy i zarządzanie nią.

Z tego samouczka dowiesz się, jak wykonywać następujące czynności:

> [!div class="checklist"]
> * Tworzenie sieci wirtualnej
> * Tworzenie bramy sieci VPN
> * Wyświetlanie publicznego adresu IP bramy
> * Zmiana rozmiaru bramy sieci VPN (zmiana rozmiaru jednostki SKU)
> * Resetowanie bramy VPN Gateway

Poniższy diagram przedstawia sieć wirtualną oraz bramę sieci VPN utworzoną w ramach tego samouczka.

:::image type="content" source="./media/tutorial-create-gateway-portal/gateway-diagram.png" alt-text="Diagram sieci VNet i VPN Gateway":::

## <a name="prerequisites"></a>Wymagania wstępne

Konto platformy Azure z aktywną subskrypcją. Jeśli go nie masz, [Utwórz go bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

## <a name="create-a-virtual-network"></a><a name="CreatVNet"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną przy użyciu następujących wartości:

* **Grupa zasobów:** TestRG1
* **Nazwa:** VNet1
* **Region:** (US) Wschodnie stany USA
* **Przestrzeń adresów IPv4:** 10.1.0.0/16
* **Nazwa podsieci:** Frontonu
* **Przestrzeń adresowa podsieci:** 10.1.0.0/24

[!INCLUDE [Create a virtual network](../../includes/vpn-gateway-basic-vnet-rm-portal-include.md)]

## <a name="create-a-vpn-gateway"></a><a name="VNetGateway"></a>Tworzenie bramy sieci VPN

W tym kroku zostaje utworzona brama dla sieci wirtualnej użytkownika. Tworzenie bramy często może trwać 45 minut lub dłużej, w zależności od wybranej jednostki SKU bramy.

Utwórz bramę sieci wirtualnej przy użyciu następujących wartości:

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

## <a name="view-the-public-ip-address"></a><a name="view"></a>Wyświetlanie publicznego adresu IP

Publiczny adres IP bramy można wyświetlić na stronie **Przegląd** bramy.

:::image type="content" source="./media/tutorial-create-gateway-portal/address.png" alt-text="Diagram sieci VNet i VPN Gateway":::

Aby wyświetlić dodatkowe informacje na temat obiektu publicznego adresu IP, kliknij link nazwa/adres IP obok pozycji **publiczny adres IP**.

## <a name="resize-a-gateway-sku"></a><a name="resize"></a>Zmiana rozmiaru jednostki SKU bramy

Istnieją określone reguły dotyczące zmiany rozmiarów w porównaniu z zmianami jednostki SKU bramy. W tej sekcji zmienimy rozmiar jednostki SKU. Aby uzyskać więcej informacji, zobacz [Ustawienia bramy — zmienianie rozmiarów i zmiana jednostek SKU](vpn-gateway-about-vpn-gateway-settings.md#resizechange).

[!INCLUDE [resize a gateway](../../includes/vpn-gateway-resize-gw-portal-include.md)]

## <a name="reset-a-gateway"></a><a name="reset"></a>Resetowanie bramy

[!INCLUDE [reset a gateway](../../includes/vpn-gateway-reset-gw-portal-include.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji lub przejdź do następnego samouczka, usuń te zasoby, wykonując następujące czynności:

1. Wprowadź nazwę grupy zasobów w polu **wyszukiwania** w górnej części portalu i wybierz ją z wyników wyszukiwania.

1. Wybierz pozycję **Usuń grupę zasobów**.

1. Wprowadź grupę zasobów, aby **wpisać nazwę grupy zasobów** , a następnie wybierz pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak:

> [!div class="nextstepaction"]
> [Tworzenie połączeń sieci VPN typu lokacja-lokacja i zarządzanie nimi](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
