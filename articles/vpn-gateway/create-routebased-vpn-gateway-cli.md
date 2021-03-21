---
title: 'Tworzenie opartej na trasach VPN Gateway platformy Azure: interfejs wiersza polecenia'
description: Szybkie tworzenie opartej na trasach bramy sieci VPN platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure dla połączenia sieci VPN z siecią lokalną lub do łączenia sieci wirtualnych.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/02/2020
ms.author: cherylmc
ms.openlocfilehash: 2bbd7e39ee65ba304ec62697b6fcc77bea133b41
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94737256"
---
# <a name="create-a-route-based-vpn-gateway-using-cli"></a>Tworzenie bramy sieci VPN opartej na trasach za pomocą interfejsu wiersza polecenia

Ten artykuł pomaga szybko utworzyć opartą na trasach bramę sieci VPN platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure. Brama sieci VPN jest używana podczas tworzenia połączenia sieci VPN z siecią lokalną. Do łączenia się z usługą sieci wirtualnych można także użyć bramy sieci VPN.

Kroki opisane w tym artykule spowodują utworzenie sieci wirtualnej, podsieci, podsieci bramy i bramy sieci VPN opartej na trasach (bramy sieci wirtualnej). Aby można było utworzyć bramę sieci wirtualnej, może upłynąć do 45 minut. Po zakończeniu tworzenia bramy można utworzyć połączenia. Te kroki wymagają subskrypcji platformy Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga wersji 2.0.4 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów przy użyciu polecenia [az group create](/cli/azure/group). Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. 


```azurecli-interactive
az group create --name TestRG1 --location eastus
```

## <a name="create-a-virtual-network"></a><a name="vnet"></a>Tworzenie sieci wirtualnej

Utwórz sieć wirtualną za pomocą polecenia [AZ Network VNET Create](/cli/azure/network/vnet) . Poniższy przykład tworzy sieć wirtualną o nazwie **VNet1** w lokalizacji **Wschodnie** :

```azurecli-interactive
az network vnet create \
  -n VNet1 \
  -g TestRG1 \
  -l eastus \
  --address-prefix 10.1.0.0/16 \
  --subnet-name Frontend \
  --subnet-prefix 10.1.0.0/24
```

## <a name="add-a-gateway-subnet"></a><a name="gwsubnet"></a>Dodawanie podsieci bramy

Podsieć bramy zawiera zastrzeżone adresy IP, z których korzystają usługi bramy sieci wirtualnej. Aby dodać podsieć bramy, użyj następujących przykładów:

```azurecli-interactive
az network vnet subnet create \
  --vnet-name VNet1 \
  -n GatewaySubnet \
  -g TestRG1 \
  --address-prefix 10.1.255.0/27 
```

## <a name="request-a-public-ip-address"></a><a name="PublicIP"></a>Przesłanie żądania dotyczącego publicznego adresu IP

Brama sieci VPN musi mieć dynamicznie przydzieloną publiczny adres IP. Publiczny adres IP zostanie przydzielony do bramy sieci VPN utworzonej dla sieci wirtualnej. Użyj poniższego przykładu, aby zażądać publicznego adresu IP:

```azurecli-interactive
az network public-ip create \
  -n VNet1GWIP \
  -g TestRG1 \
  --allocation-method Dynamic 
```

## <a name="create-the-vpn-gateway"></a><a name="CreateGateway"></a>Tworzenie bramy sieci VPN

Utwórz bramę sieci VPN za pomocą polecenia [az network vnet-gateway create](/cli/azure/group).

Jeśli uruchomisz to polecenie przy użyciu `--no-wait` parametru, nie zobaczysz żadnych informacji zwrotnych ani danych wyjściowych. `--no-wait`Parametr umożliwia utworzenie bramy w tle. Nie oznacza to, że Brama sieci VPN jest tworzona natychmiast.

```azurecli-interactive
az network vnet-gateway create \
  -n VNet1GW \
  -l eastus \
  --public-ip-address VNet1GWIP \
  -g TestRG1 \
  --vnet VNet1 \
  --gateway-type Vpn \
  --sku VpnGw1 \
  --vpn-type RouteBased \
  --no-wait
```

Tworzenie bramy sieci VPN może potrwać 45 minut lub dłużej.

## <a name="view-the-vpn-gateway"></a><a name="viewgw"></a>Wyświetlanie bramy sieci VPN

```azurecli-interactive
az network vnet-gateway show \
  -n VNet1GW \
  -g TestRG1
```

Odpowiedź wygląda podobnie do:

```output
{
  "activeActive": false,
  "bgpSettings": null,
  "enableBgp": false,
  "etag": "W/\"6c61f8cb-d90f-4796-8697\"",
  "gatewayDefaultSite": null,
  "gatewayType": "Vpn",
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW",
  "ipConfigurations": [
    {
      "etag": "W/\"6c61f8cb-d90f-4796-8697\"",
      "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/vnetGatewayConfig0",
      "name": "vnetGatewayConfig0",
      "privateIpAllocationMethod": "Dynamic",
      "provisioningState": "Updating",
      "publicIpAddress": {
        "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP",
        "resourceGroup": "TestRG1"
      },
      "resourceGroup": "TestRG1",
      "subnet": {
        "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG11/providers/Microsoft.Network/virtualNetworks/VNet1/subnets/GatewaySubnet",
        "resourceGroup": "TestRG1"
      }
    }
  ],
  "location": "eastus",
  "name": "VNet1GW",
  "provisioningState": "Updating",
  "resourceGroup": "TestRG1",
  "resourceGuid": "69c269e3-622c-4123-9231",
  "sku": {
    "capacity": 2,
    "name": "VpnGw1",
    "tier": "VpnGw1"
  },
  "tags": null,
  "type": "Microsoft.Network/virtualNetworkGateways",
  "vpnClientConfiguration": null,
  "vpnType": "RouteBased"
}
```

### <a name="view-the-public-ip-address"></a>Wyświetlanie publicznego adresu IP

Aby wyświetlić publiczny adres IP przypisany do bramy, użyj poniższego przykładu:

```azurecli-interactive
az network public-ip show \
  --name VNet1GWIP \
  --resource-group TestRG11
```

Wartością skojarzoną z polem **AdresIP** jest publiczny adres IP bramy sieci VPN.

Przykładowa odpowiedź:

```output
{
  "dnsSettings": null,
  "etag": "W/\"a12d4d03-b27a-46cc-b222-8d9364b8166a\"",
  "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/publicIPAddresses/VNet1GWIP",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "13.90.195.184",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/<subscription ID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW/ipConfigurations/vnetGatewayConfig0",
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy utworzone zasoby nie są już potrzebne, użyj polecenie [AZ Group Delete](/cli/azure/group) , aby usunąć grupę zasobów. Spowoduje to usunięcie grupy zasobów i wszystkich znajdujących się w niej zasobów.

```azurecli-interactive 
az group delete --name TestRG1 --yes
```

## <a name="next-steps"></a>Następne kroki

Po zakończeniu tworzenia bramy można utworzyć połączenie między Twoją siecią wirtualną a inną. Można też utworzyć połączenie między siecią wirtualną i lokalizacją lokalną.

> [!div class="nextstepaction"]
> [Tworzenie połączenia typu lokacja-lokacja](vpn-gateway-create-site-to-site-rm-powershell.md)<br><br>
> [Tworzenie połączenia typu punkt-lokacja](vpn-gateway-howto-point-to-site-rm-ps.md)<br><br>
> [Utwórz połączenie z inną siecią wirtualną](vpn-gateway-vnet-vnet-rm-ps.md)
