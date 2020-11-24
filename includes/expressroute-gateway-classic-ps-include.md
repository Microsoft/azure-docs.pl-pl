---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 12/13/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c0c05eb88df27ba9e71c0f61bad75457d893a7ce
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "95564109"
---
> [!NOTE]
> Te przykłady nie dotyczą konfiguracji współistnienia połączeń S2S/ExpressRoute.
> Aby uzyskać więcej informacji na temat pracy z bramami w konfiguracji współistnienia, zobacz [Konfigurowanie współistniejących połączeń.](../articles/expressroute/expressroute-howto-coexist-classic.md#gw)

## <a name="add-a-gateway"></a>Dodawanie bramy

Po dodaniu bramy do sieci wirtualnej przy użyciu klasycznego modelu zasobów należy zmodyfikować plik konfiguracji sieci bezpośrednio przed utworzeniem bramy. Aby można było utworzyć bramę, w pliku muszą być obecne poniższe wartości. Jeśli do sieci wirtualnej wcześniej była skojarzona brama, niektóre z tych wartości będą już obecne. Zmodyfikuj plik, aby odzwierciedlał poniższe wartości.

### <a name="download-the-network-configuration-file"></a>Pobierz plik konfiguracji sieciowej

1. Pobierz plik konfiguracji sieciowej, wykonując czynności opisane w artykule [plik konfiguracji sieci](/previous-versions/azure/virtual-network/virtual-networks-using-network-configuration-file) . Otwórz plik za pomocą edytora tekstu.
2. Dodaj do pliku lokację sieci lokalnej. Można użyć dowolnego prawidłowego prefiksu adresu. Można dodać dowolny prawidłowy adres IP dla bramy sieci VPN. Wartości adresu w tej sekcji nie są używane dla operacji ExpressRoute, ale są wymagane do walidacji plików. W przykładzie "Branch1" to nazwa witryny. Możesz użyć innej nazwy, ale pamiętaj, aby użyć tej samej wartości w sekcji bramy pliku.

   ```
   <VirtualNetworkConfiguration>
    <Dns />
    <LocalNetworkSites>
      <LocalNetworkSite name="branch1">
        <AddressSpace>
          <AddressPrefix>165.3.1.0/27</AddressPrefix>
        </AddressSpace>
        <VPNGatewayAddress>3.2.1.4</VPNGatewayAddress>
    </LocalNetworkSite>
   ```
3. Przejdź do VirtualNetworkSites i zmodyfikuj pola.

   * Sprawdź, czy istnieje podsieć bramy dla sieci wirtualnej. Jeśli tak nie jest, możesz dodać jeden z nich. Nazwa musi mieć wartość "GatewaySubnet".
   * Sprawdź, czy w pliku istnieje sekcja Gateway. Jeśli nie, Dodaj ją. Jest to wymagane do skojarzenia sieci wirtualnej z lokacją sieci lokalnej (która reprezentuje sieć, z którą nawiązywane jest połączenie).
   * Sprawdź, czy typ połączenia = dedykowany. Jest to wymagane w przypadku połączeń ExpressRoute.

   ```
   </LocalNetworkSites>
    <VirtualNetworkSites>
      <VirtualNetworkSite name="myAzureVNET" Location="East US">
        <AddressSpace>
          <AddressPrefix>10.0.0.0/16</AddressPrefix>
        </AddressSpace>
        <Subnets>
          <Subnet name="default">
            <AddressPrefix>10.0.0.0/24</AddressPrefix>
          </Subnet>
          <Subnet name="GatewaySubnet">
            <AddressPrefix>10.0.1.0/27</AddressPrefix>
          </Subnet>
        </Subnets>
        <Gateway>
          <ConnectionsToLocalNetwork>
            <LocalNetworkSiteRef name="branch1">
              <Connection type="Dedicated" />
            </LocalNetworkSiteRef>
          </ConnectionsToLocalNetwork>
        </Gateway>
      </VirtualNetworkSite>
    </VirtualNetworkSites>
   </VirtualNetworkConfiguration>
   </NetworkConfiguration>
   ```
4. Zapisz plik i przekaż go do platformy Azure.

### <a name="create-the-gateway"></a>Tworzenie bramy

Użyj poniższego polecenia, aby utworzyć bramę. Podstaw własne wartości.

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>Sprawdź, czy brama została utworzona

Użyj poniższego polecenia, aby sprawdzić, czy brama została utworzona. To polecenie umożliwia również pobranie identyfikatora bramy, który jest potrzebny do wykonywania innych operacji.

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>Zmienianie rozmiaru bramy

Istnieje wiele [jednostek SKU bramy](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Aby zmienić jednostkę SKU bramy w dowolnym momencie, można użyć poniższego polecenia.

> [!IMPORTANT]
> To polecenie nie działa w przypadku bramy UltraPerformance. Aby zmienić bramę na bramę UltraPerformance, najpierw usuń istniejącą bramę ExpressRoute, a następnie utwórz nową bramę UltraPerformance. Aby obniżyć poziom bramy z bramy UltraPerformance, najpierw usuń bramę UltraPerformance, a następnie utwórz nową bramę.
>
>

```powershell
Resize-AzureVNetGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="remove-a-gateway"></a>Usuwanie bramy

Użyj poniższego polecenia, aby usunąć bramę

```powershell
Remove-AzureVnetGateway -GatewayId <Gateway ID>
```