---
title: 'Modyfikowanie ustawień adresu IP bramy: PowerShell'
description: W tym artykule omówiono Zmienianie prefiksów adresów IP bramy sieci lokalnej przy użyciu programu PowerShell
services: vpn-gateway
titleSuffix: Azure VPN Gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: b235d278cb3061f17068f4e5a3edf5e8f8899f17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89392463"
---
# <a name="modify-local-network-gateway-settings-using-powershell"></a>Modyfikowanie ustawień lokalnej bramy sieci przy użyciu programu PowerShell

Czasami ustawienia AddressPrefix lub GatewayIPAddress bramy sieci lokalnej. W tym artykule opisano sposób modyfikowania ustawień bramy sieci lokalnej. Możesz również zmodyfikować te ustawienia przy użyciu innej metody, wybierając inną opcję z następującej listy:

> [!div class="op_single_selector"]
> * [Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [Program PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Interfejs wiersza polecenia platformy Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before-you-begin"></a><a name="before"></a>Zanim rozpoczniesz

Zainstaluj najnowszą wersję poleceń cmdlet programu PowerShell usługi Azure Resource Manager. Aby uzyskać więcej informacji na temat instalowania poleceń cmdlet programu Azure PowerShell, zobacz artykuł [How to install and configure Azure PowerShell](/powershell/azure/) (Instalowanie i konfigurowanie programu Azure PowerShell).

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Modyfikuj prefiksy adresów IP

[!INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Modyfikowanie adresu IP bramy

[!INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Następne kroki

Możesz zweryfikować połączenie z bramą. Zobacz [Weryfikowanie połączenia bramy](vpn-gateway-verify-connection-resource-manager.md).