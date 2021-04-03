---
title: 'VPN Gateway: modyfikowanie ustawień adresu IP bramy: Azure Portal'
description: W tym artykule pokazano, jak zmienić prefiksy adresów IP bramy sieci lokalnej przy użyciu Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 10/16/2020
ms.author: cherylmc
ms.openlocfilehash: 4ff4f1238764d7bdab6e74d29254a6388ea76d78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92143164"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Modyfikowanie ustawień lokalnej bramy sieci w witrynie Azure Portal

Czasami ustawienia AddressPrefix lub GatewayIPAddress bramy sieci lokalnej. W tym artykule opisano sposób modyfikowania ustawień bramy sieci lokalnej. Możesz również zmodyfikować te ustawienia przy użyciu innej metody, wybierając inną opcję z następującej listy:

> [!div class="op_single_selector"]
> * [Witryna Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [Program PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Interfejs wiersza polecenia platformy Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>

## <a name="local-network-gateway-configuration"></a><a name="configure-lng"></a>Konfiguracja bramy sieci lokalnej

Zrzut ekranu poniżej przedstawia stronę **konfiguracji** zasobu bramy sieci lokalnej przy użyciu punktu końcowego publicznego adresu IP:

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/settings.png" alt-text="Ustawienia adresu IP" lightbox="./media/vpn-gateway-modify-local-network-gateway-portal/settings-expand.png":::

Jest to strona konfiguracji z punktem końcowym nazwy FQDN:

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/name.png" alt-text="Ustawienia nazwy FQDN":::

## <a name="to-modify-the-gateway-ip-address-or-fqdn"></a><a name="ip"></a>Aby zmodyfikować adres IP lub nazwę FQDN bramy

> [!NOTE]
> Nie można zmienić bramy sieci lokalnej między punktem końcowym nazwy FQDN a punktem końcowym adresu IP. Musisz usunąć wszystkie połączenia skojarzone z tą bramą sieci lokalnej, utworzyć nową nazwę z nowym punktem końcowym (adresem IP lub nazwą FQDN), a następnie ponownie utworzyć połączenia.
>

Jeśli na urządzeniu sieci VPN, z którym chcesz nawiązać połączenie, zmieniono swój publiczny adres IP, zmodyfikuj bramę sieci lokalnej, wykonując następujące czynności:

1. W obszarze **Ustawienia** zasobu Brama sieci lokalnej wybierz pozycję **Konfiguracja**.
2. W polu **adres IP** zmodyfikuj adres IP.
3. Wybierz pozycję **Zapisz**, aby zapisać ustawienia.

Jeśli urządzenie sieci VPN, z którym chcesz nawiązać połączenie, zmieniło swoją nazwę FQDN (w pełni kwalifikowaną nazwę domeny), zmodyfikuj bramę sieci lokalnej, wykonując następujące czynności:

1. W obszarze **Ustawienia** zasobu Brama sieci lokalnej wybierz pozycję **Konfiguracja**.
2. W polu Nazwa **FQDN** zmodyfikuj nazwę domeny.
3. Wybierz pozycję **Zapisz**, aby zapisać ustawienia.

## <a name="to-modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Aby zmodyfikować prefiksy adresów IP

Aby dodać dodatkowe prefiksy adresów:

1. W obszarze **Ustawienia** zasobu Brama sieci lokalnej wybierz pozycję **Konfiguracja**.
2. Dodaj przestrzeń adresową IP w polu *Dodaj dodatkowy zakres adresów* .
3. Wybierz pozycję **Zapisz** , aby zapisać ustawienia.

Aby usunąć prefiksy adresów:

1. W obszarze **Ustawienia** zasobu Brama sieci lokalnej wybierz pozycję **Konfiguracja**.
2. Wybierz **"..."** w wierszu zawierającym prefiks, który chcesz usunąć.
3. Wybierz pozycję **Usuń**.
4. Wybierz pozycję **Zapisz** , aby zapisać ustawienia.

## <a name="to-modify-bgp-settings"></a><a name="bgp"></a>Aby zmodyfikować ustawienia protokołu BGP

Aby dodać lub zaktualizować ustawienia protokołu BGP:

1. W obszarze **Ustawienia** zasobu Brama sieci lokalnej wybierz pozycję **Konfiguracja**.
2. Wybierz pozycję **"Konfiguruj ustawienia protokołu BGP"** , aby wyświetlić lub zaktualizować konfiguracje protokołu BGP dla tej bramy sieci lokalnej
3. Dodaj lub zaktualizuj numer systemu autonomicznego lub adres IP elementu równorzędnego BGP w odpowiednich polach
4. Wybierz pozycję **Zapisz** , aby zapisać ustawienia.

Aby usunąć ustawienia protokołu BGP:

1. W obszarze **Ustawienia** zasobu Brama sieci lokalnej wybierz pozycję **Konfiguracja**.
2. Usuń zaznaczenie opcji **"Konfiguruj ustawienia protokołu BGP"** , aby usunąć istniejący adres IP elementu równorzędnego BGP i protokołu BGP
3. Wybierz pozycję **Zapisz** , aby zapisać ustawienia.

## <a name="next-steps"></a>Następne kroki

Możesz zweryfikować połączenie z bramą. Zobacz [Weryfikowanie połączenia bramy](vpn-gateway-verify-connection-resource-manager.md).
