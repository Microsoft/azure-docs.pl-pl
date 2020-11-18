---
title: 'VPN Gateway: modyfikowanie ustawień adresu IP bramy: interfejs wiersza polecenia platformy Azure'
description: W tym artykule omówiono Zmienianie prefiksów adresów IP bramy sieci lokalnej przy użyciu interfejsu wiersza polecenia platformy Azure.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: aadb2107cd009ac58cf8edb12853b10a5a0e3677
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94660002"
---
# <a name="modify-local-network-gateway-settings-using-the-azure-cli"></a>Modyfikowanie ustawień bramy sieci lokalnej przy użyciu interfejsu wiersza polecenia platformy Azure

Czasami ustawienia prefiksu adresu bramy sieci lokalnej lub adresu IP bramy są zmieniane. W tym artykule opisano sposób modyfikowania ustawień bramy sieci lokalnej. Możesz również zmodyfikować te ustawienia przy użyciu innej metody, wybierając inną opcję z następującej listy:

> [!div class="op_single_selector"]
> * [Witryna Azure Portal](vpn-gateway-modify-local-network-gateway-portal.md)
> * [Program PowerShell](vpn-gateway-modify-local-network-gateway.md)
> * [Interfejs wiersza polecenia platformy Azure](vpn-gateway-modify-local-network-gateway-cli.md)
>
>

## <a name="before-you-begin"></a><a name="before"></a>Zanim rozpoczniesz

Zainstaluj najnowszą wersję poleceń interfejsu wiersza polecenia (2,0 lub nowszej). Aby uzyskać informacje o instalowaniu poleceń interfejsu wiersza polecenia, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

[!INCLUDE [CLI-login](../../includes/vpn-gateway-cli-login-include.md)]

## <a name="modify-ip-address-prefixes"></a><a name="ipaddprefix"></a>Modyfikuj prefiksy adresów IP

[!INCLUDE [modify-prefix](../../includes/vpn-gateway-modify-ip-prefix-cli-include.md)]

## <a name="modify-the-gateway-ip-address"></a><a name="gwip"></a>Modyfikowanie adresu IP bramy

[!INCLUDE [modify-gateway-IP](../../includes/vpn-gateway-modify-lng-gateway-ip-cli-include.md)]

## <a name="next-steps"></a>Następne kroki

Możesz zweryfikować połączenie z bramą. Zobacz [Weryfikowanie połączenia bramy](vpn-gateway-verify-connection-resource-manager.md).