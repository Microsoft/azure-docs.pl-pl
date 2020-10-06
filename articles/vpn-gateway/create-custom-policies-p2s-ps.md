---
title: 'Tworzenie i ustawianie niestandardowych zasad protokołu IPsec dla połączeń punkt-lokacja: PowerShell'
titleSuffix: Azure VPN Gateway
description: Ten artykuł ułatwia tworzenie i ustawianie niestandardowych zasad protokołu IPSec dla VPN Gateway konfiguracji P2S
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/09/2020
ms.author: cherylmc
ms.openlocfilehash: a77a870304e20c179e22b4a4ffe404315894d8a0
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743696"
---
# <a name="create-and-set-custom-ipsec-policies-for-point-to-site-preview"></a>Tworzenie i ustawianie niestandardowych zasad protokołu IPsec dla połączeń punkt-lokacja (wersja zapoznawcza)

Jeśli środowisko wymaga niestandardowych zasad protokołu IPsec do szyfrowania, można łatwo skonfigurować obiekt zasad z wymaganymi ustawieniami. Ten artykuł pomaga utworzyć obiekt zasad niestandardowych, a następnie ustawić go przy użyciu programu PowerShell.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

### <a name="prerequisites"></a>Wymagania wstępne

Sprawdź, czy środowisko spełnia następujące wymagania wstępne:

* Istnieje już działająca sieć VPN typu punkt-lokacja. Jeśli tego nie zrobisz, skonfiguruj go, wykonując czynności opisane w artykule **Tworzenie sieci VPN typu punkt-lokacja**  przy użyciu [programu PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md)lub [Azure Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md).

### <a name="working-with-azure-powershell"></a>Praca z Azure PowerShell

[!INCLUDE [PowerShell](../../includes/vpn-gateway-cloud-shell-powershell.md)]

## <a name="1-set-variables"></a><a name="signin"></a>1. Ustaw zmienne

Zadeklaruj zmienne, których chcesz użyć. Użyj poniższego przykładu, zastępując wartości własnymi w razie potrzeby. Jeśli zamkniesz sesję programu PowerShell/Cloud Shell w dowolnym momencie podczas wykonywania, po prostu skopiuj i wklej ponownie wartości w celu ponownego zadeklarowania zmiennych.

  ```azurepowershell-interactive
  $RG = "TestRG"
  $GWName = "VNet1GW"
  ```

## <a name="2-create-policy-object"></a><a name="create"></a>2. Utwórz obiekt zasad

Utwórz niestandardowy obiekt zasad protokołu IPsec. Możesz dostosować wartości, aby spełniały kryteria, które są wymagane.

```azurepowershell-interactive
$vpnclientipsecpolicy = New-AzVpnClientIpsecPolicy -IpsecEncryption AES256 -IpsecIntegrity SHA256 -SALifeTime 86471 -SADataSize 429496 -IkeEncryption AES256 -IkeIntegrity SHA384 -DhGroup DHGroup2 -PfsGroup PFS2
```

## <a name="3-update-gateway-and-set-policy"></a><a name="update"></a>3. Zaktualizuj bramę i ustaw zasady

W tym kroku należy zaktualizować istniejącą bramę sieci VPN P2S i ustawić zasady protokołu IPsec.

```azurepowershell-interactive
$gateway = Get-AzVirtualNetworkGateway -ResourceGroupName $RG -name $GWName
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gateway -VpnClientIpsecPolicy $vpnclientipsecpolicy
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji o konfiguracjach P2S, zobacz [Informacje o sieci VPN typu punkt-lokacja](point-to-site-about.md).
