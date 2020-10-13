---
title: 'VPN Gateway: modyfikowanie ustawień adresu IP bramy: Azure Portal'
description: W tym artykule pokazano, jak zmienić prefiksy adresów IP bramy sieci lokalnej przy użyciu Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/16/2020
ms.author: cherylmc
ms.openlocfilehash: af3513c4a4f3b3187e85c65de51ad2e6e2d7279c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90983160"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Modyfikowanie ustawień lokalnej bramy sieci w witrynie Azure Portal

Czasami ustawienia AddressPrefix lub GatewayIPAddress bramy sieci lokalnej. W tym artykule opisano sposób modyfikowania ustawień bramy sieci lokalnej. Możesz również zmodyfikować te ustawienia przy użyciu innej metody, wybierając inną opcję z następującej listy:

Przed usunięciem połączenia warto pobrać konfigurację dla podłączonych urządzeń w celu uzyskania zdefiniowanego klucza PSK. W ten sposób nie trzeba ponownie definiować go po drugiej stronie.

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [Program PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Interfejs wiersza polecenia platformy Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="local-network-gateway-configuration"></a><a name="configure-lng"></a>Konfiguracja bramy sieci lokalnej

Zrzut ekranu poniżej przedstawia stronę **konfiguracji** zasobu bramy sieci lokalnej przy użyciu punktu końcowego publicznego adresu IP:

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/ip-address.png" alt-text="Konfigurowanie bramy sieci lokalnej — adres IP":::

Jest to ta sama strona konfiguracji z punktem końcowym FQDN:

:::image type="content" source="./media/vpn-gateway-modify-local-network-gateway-portal/fqdn.png" alt-text="Konfigurowanie bramy sieci lokalnej — adres IP":::

## <a name="modify-the-gateway-ip-address"></a><a name="ip"></a>Modyfikowanie adresu IP bramy

W przypadku zmiany publicznego adresu IP urządzenia sieci VPN, z którym chcesz nawiązać połączenie, musisz zmodyfikować bramę sieci lokalnej w celu odzwierciedlenia tej zmiany.

1. W obszarze **Ustawienia** zasobu Brama sieci lokalnej kliknij pozycję **Konfiguracja**.
2. W polu **adres IP** zmodyfikuj adres IP.
3. Kliknij polecenie **Zapisz**, aby zapisać ustawienia.

## <a name="modify-the-gateway-fqdn"></a><a name="fqdn"></a>Modyfikowanie nazwy FQDN bramy

Jeśli urządzenie sieci VPN, z którym chcesz nawiązać połączenie, zmieniło nazwę FQDN (w pełni kwalifikowana nazwa domeny), należy zmodyfikować bramę sieci lokalnej w celu odzwierciedlenia tej zmiany.

1. W obszarze **Ustawienia** zasobu Brama sieci lokalnej kliknij pozycję **Konfiguracja**.
2. W polu Nazwa **FQDN** zmodyfikuj nazwę domeny.
3. Kliknij polecenie **Zapisz**, aby zapisać ustawienia.

> ! KORYGUJĄC Nie można zmienić bramy sieci lokalnej między punktem końcowym nazwy FQDN a punktem końcowym adresu IP. Musisz usunąć wszystkie połączenia skojarzone z tą bramą sieci lokalnej, utworzyć nową nazwę z nowym punktem końcowym (adresem IP lub nazwą FQDN), a następnie ponownie utworzyć połączenia.

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Modyfikuj prefiksy adresów IP

### <a name="to-add-additional-address-prefixes"></a>Aby dodać dodatkowe prefiksy adresów:

1. W obszarze **Ustawienia** zasobu Brama sieci lokalnej kliknij pozycję **Konfiguracja**.
2. Dodaj przestrzeń adresową IP w polu *Dodaj dodatkowy zakres adresów* .
3. Kliknij przycisk **Zapisz** , aby zapisać ustawienia.

### <a name="to-remove-address-prefixes"></a>Aby usunąć prefiksy adresów:

1. W obszarze **Ustawienia** zasobu Brama sieci lokalnej kliknij pozycję **Konfiguracja**.
2. Kliknij **"..."** w wierszu zawierającym prefiks, który chcesz usunąć.
3. Kliknij przycisk **Usuń**.
4. Kliknij przycisk **Zapisz** , aby zapisać ustawienia.

## <a name="modify-bgp-settings"></a><a name="bgp"></a>Modyfikowanie ustawień protokołu BGP

### <a name="to-add-or-update-bgp-settings"></a>Aby dodać lub zaktualizować ustawienia protokołu BGP:

1. W obszarze **Ustawienia** zasobu Brama sieci lokalnej kliknij pozycję **Konfiguracja**.
2. Wybierz pozycję **"Konfiguruj ustawienia protokołu BGP"** , aby wyświetlić lub zaktualizować konfiguracje protokołu BGP dla tej bramy sieci lokalnej
3. Dodaj lub zaktualizuj numer systemu autonomicznego lub adres IP elementu równorzędnego BGP w odpowiednich polach
4. Kliknij przycisk **Zapisz** , aby zapisać ustawienia.

### <a name="to-remove-bgp-settings"></a>Aby usunąć ustawienia protokołu BGP:

1. W obszarze **Ustawienia** zasobu Brama sieci lokalnej kliknij pozycję **Konfiguracja**.
2. Usuń zaznaczenie opcji **"Konfiguruj ustawienia protokołu BGP"** , aby usunąć istniejący adres IP ASN protokołu BGP i element RÓWNORZĘDNy BGP
3. Kliknij przycisk **Zapisz** , aby zapisać ustawienia.

## <a name="next-steps"></a>Następne kroki

Możesz zweryfikować połączenie z bramą. Zobacz [Weryfikowanie połączenia bramy](vpn-gateway-verify-connection-resource-manager.md).
