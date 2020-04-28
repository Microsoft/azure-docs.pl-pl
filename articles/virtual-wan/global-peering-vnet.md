---
title: Konfigurowanie globalnej komunikacji równorzędnej sieci wirtualnej dla usługi Azure Virtual WAN | Microsoft Docs
description: Połącz sieć wirtualną w innym regionie z wirtualnym koncentratorem sieci WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 340472f84d2dd2c4f46d180992745a57e8ad1884
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "73588228"
---
# <a name="configure-global-vnet-peering-cross-region-vnet-for-virtual-wan"></a>Skonfiguruj globalne wirtualne sieci równorzędne (Sieć wirtualna między regionami) dla wirtualnej sieci WAN

Sieć wirtualną można połączyć w innym regionie z wirtualnym koncentratorem sieci WAN.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Sprawdź, czy zostały spełnione następujące kryteria:

* Sieć wirtualna między regionami (szprych) nie jest połączona z innym wirtualnym koncentratorem sieci WAN. Szprycha może być połączona tylko z jednym koncentratorem wirtualnym.
* Sieć wirtualna (szprych) nie zawiera bramy sieci wirtualnej (na przykład usługi Azure VPN Gateway lub bramy sieci wirtualnej ExpressRoute). Jeśli sieć wirtualna zawiera bramę sieci wirtualnej, należy usunąć bramę przed nawiązaniem połączenia z siecią wirtualną szprychy z koncentratorem.

## <a name="register-this-feature"></a><a name="register"></a>Rejestrowanie tej funkcji

Możesz zarejestrować się w celu korzystania z tej funkcji przy użyciu programu PowerShell. W przypadku wybrania opcji "Wypróbuj" z poniższego przykładu zostanie otwarte środowisko Azure Cloud Shell i nie będzie konieczne lokalne zainstalowanie poleceń cmdlet programu PowerShell na komputerze. W razie potrzeby można zmienić subskrypcje przy użyciu polecenia cmdlet "Select-AzSubscription- <subid>Subscription".

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
Register-AzResourceProvider -ProviderNamespace 'Microsoft.Network'
```

## <a name="verify-registration"></a><a name="verify"></a>Weryfikuj rejestrację

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName AllowCortexGlobalVnetPeering -ProviderNamespace Microsoft.Network
```

## <a name="connect-a-vnet-to-the-hub"></a><a name="hub"></a>Łączenie sieci wirtualnej z centrum

W tym kroku utworzysz połączenie komunikacji równorzędnej między koncentratorem a siecią wirtualną między regionami. Powtórz te czynności dla każdej sieci wirtualnej, z którą chcesz się połączyć.

1. Na stronie sieci wirtualnej WAN kliknij pozycję **Połączenia sieci wirtualnych**.
2. Na stronie połączenia sieci wirtualnej kliknij polecenie **+ Dodaj połączenie**.
3. Na stronie **Dodaj połączenie** wypełnij następujące pola:

    * **Nazwa połączenia** — nazwij połączenie.
    * **Koncentratory** — wybierz koncentrator, z którym chcesz skojarzyć to połączenie.
    * **Subskrypcja** — sprawdź, czy wybrano właściwą subskrypcję.
    * **Sieć wirtualna** — wybierz sieć wirtualną, którą chcesz połączyć z tym koncentratorem. Sieć wirtualna nie może mieć istniejącej bramy sieci wirtualnej.
4. Kliknij przycisk **OK**, aby utworzyć połączenie równorzędne.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o wirtualnej sieci WAN, zobacz [Omówienie usługi Virtual WAN](virtual-wan-about.md).