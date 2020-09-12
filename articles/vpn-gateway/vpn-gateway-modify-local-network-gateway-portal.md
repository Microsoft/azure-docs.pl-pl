---
title: 'VPN Gateway: modyfikowanie ustawień adresu IP bramy: Azure Portal'
description: W tym artykule pokazano, jak zmienić prefiksy adresów IP bramy sieci lokalnej przy użyciu Azure Portal.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 57288d49fdfa193e9ebebe5f2ce4d24327997980
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89392480"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-portal"></a>Modyfikowanie ustawień lokalnej bramy sieci w witrynie Azure Portal

Czasami ustawienia AddressPrefix lub GatewayIPAddress bramy sieci lokalnej. W tym artykule opisano sposób modyfikowania ustawień bramy sieci lokalnej. Możesz również zmodyfikować te ustawienia przy użyciu innej metody, wybierając inną opcję z następującej listy:

Przed usunięciem połączenia warto pobrać konfigurację dla podłączonych urządzeń w celu uzyskania zdefiniowanego klucza PSK. W ten sposób nie trzeba ponownie definiować go po drugiej stronie.

> [!div class="op_single_selector"]
> * [Witryna Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [Program PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Interfejs wiersza polecenia platformy Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>


## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Modyfikuj prefiksy adresów IP

Podczas modyfikowania prefiksów adresów IP, kroki, które należy wykonać, zależą od tego, czy Brama sieci lokalnej ma połączenie.

[!INCLUDE [modify prefix](../../includes/vpn-gateway-modify-ip-prefix-portal-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Modyfikowanie adresu IP bramy

W przypadku zmiany publicznego adresu IP urządzenia sieci VPN, z którym chcesz nawiązać połączenie, musisz zmodyfikować bramę sieci lokalnej w celu odzwierciedlenia tej zmiany. Gdy zmienisz publiczny adres IP, kroki, które należy wykonać, zależą od tego, czy Brama sieci lokalnej ma połączenie.

[!INCLUDE [modify gateway IP](../../includes/vpn-gateway-modify-lng-gateway-ip-portal-include.md)]

## <a name="next-steps"></a>Następne kroki

Możesz zweryfikować połączenie z bramą. Zobacz [Weryfikowanie połączenia bramy](vpn-gateway-verify-connection-resource-manager.md).
