---
title: Dodawanie protokołu IPv6 do aplikacji IPv4 w usłudze Azure Virtual Network — interfejs wiersza polecenia platformy Azure
titlesuffix: Azure Virtual Network
description: W tym artykule pokazano, jak wdrażać adresy IPv6 w istniejącej aplikacji w usłudze Azure Virtual Network przy użyciu interfejsu wiersza polecenia platformy Azure.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: de8d63fb5181e324738e082cbb36a40ee59e36b0
ms.sourcegitcommit: 30906a33111621bc7b9b245a9a2ab2e33310f33f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/22/2020
ms.locfileid: "96005252"
---
# <a name="add-ipv6-to-an-ipv4-application-in-azure-virtual-network---azure-cli"></a>Dodawanie protokołu IPv6 do aplikacji IPv4 w usłudze Azure Virtual Network — interfejs wiersza polecenia platformy Azure

W tym artykule pokazano, jak dodać adresy IPv6 do aplikacji, która korzysta z publicznego adresu IP protokołu IPv4 w sieci wirtualnej platformy Azure dla usługa Load Balancer w warstwie Standardowa przy użyciu interfejsu wiersza polecenia platformy Azure. Uaktualnienie w miejscu obejmuje sieć wirtualną i podsieć, usługa Load Balancer w warstwie Standardowa z konfiguracjami frontonu IPv4 + IPV6, maszynami wirtualnymi z konfiguracjami IPv4 + IPv6, sieciową grupą zabezpieczeń i publicznymi adresami IP.

## <a name="prerequisites"></a>Wymagania wstępne

- W tym artykule przyjęto założenie, że wdrożono usługa Load Balancer w warstwie Standardowa zgodnie z opisem w [przewodniku szybki start: tworzenie usługa Load Balancer w warstwie Standardowa interfejsu wiersza polecenia platformy Azure](../load-balancer/quickstart-load-balancer-standard-public-cli.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ten artykuł wymaga wersji 2.0.28 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="create-ipv6-addresses"></a>Tworzenie adresów IPv6

Utwórz publiczny adres IPv6 przy użyciu [AZ Network Public-IP Create](/cli/azure/network/public-ip) dla usługa Load Balancer w warstwie Standardowa. Poniższy przykład tworzy publiczny adres IP IPv6 o nazwie *PublicIP_v6* w grupie zasobów *myResourceGroupSLB* :

```azurecli-interactive
az network public-ip create \
--name PublicIP_v6 \
--resource-group MyResourceGroupSLB \
--location EastUS \
--sku Standard \
--allocation-method static \
--version IPv6
```

## <a name="configure-ipv6-load-balancer-frontend"></a>Konfigurowanie frontonu modułu równoważenia obciążenia IPv6

Skonfiguruj moduł równoważenia obciążenia za pomocą nowego adresu IP IPv6 za pomocą polecenia [AZ Network lb fronton-IP Create](https://docs.microsoft.com/cli/azure/network/lb/frontend-ip?view=azure-cli-latest#az-network-lb-frontend-ip-create) w następujący sposób:

```azurecli-interactive
az network lb frontend-ip create \
--lb-name myLoadBalancer \
--name dsLbFrontEnd_v6 \
--resource-group MyResourceGroupSLB \
--public-ip-address PublicIP_v6
```

## <a name="configure-ipv6-load-balancer-backend-pool"></a>Konfigurowanie puli zaplecza modułu równoważenia obciążenia IPv6

Utwórz pulę zaplecza dla kart sieciowych z adresami IPv6 przy użyciu polecenia [AZ Network lb Address-Pool Create](https://docs.microsoft.com/cli/azure/network/lb/address-pool?view=azure-cli-latest#az-network-lb-address-pool-create) w następujący sposób:

```azurecli-interactive
az network lb address-pool create \
--lb-name myLoadBalancer \
--name dsLbBackEndPool_v6 \
--resource-group MyResourceGroupSLB
```

## <a name="configure-ipv6-load-balancer-rules"></a>Konfigurowanie reguł modułu równoważenia obciążenia IPv6

Utwórz reguły modułu równoważenia obciążenia IPv6 za pomocą [AZ Network lb Rule Create](https://docs.microsoft.com/cli/azure/network/lb/rule?view=azure-cli-latest#az-network-lb-rule-create).

```azurecli-interactive
az network lb rule create \
--lb-name myLoadBalancer \
--name dsLBrule_v6 \
--resource-group MyResourceGroupSLB \
--frontend-ip-name dsLbFrontEnd_v6 \
--protocol Tcp \
--frontend-port 80 \
--backend-port 80 \
--backend-pool-name dsLbBackEndPool_v6
```

## <a name="add-ipv6-address-ranges"></a>Dodawanie zakresów adresów IPv6

Dodaj zakresy adresów IPv6 do sieci wirtualnej i podsieci obsługujących moduł równoważenia obciążenia w następujący sposób:

```azurecli-interactive
az network vnet update \
--name myVnet  \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/16"  "fd00:db8:deca::/48"

az network vnet subnet update \
--vnet-name myVnet \
--name mySubnet \
--resource-group MyResourceGroupSLB \
--address-prefixes  "10.0.0.0/24"  "fd00:db8:deca:deed::/64"  
```

## <a name="add-ipv6-configuration-to-nics"></a>Dodawanie konfiguracji protokołu IPv6 do kart sieciowych

Skonfiguruj karty sieciowe maszyn wirtualnych z adresem IPv6 za pomocą polecenia [AZ Network nic IP-config Create](https://docs.microsoft.com/cli/azure/network/nic/ip-config?view=azure-cli-latest#az-network-nic-ip-config-create) w następujący sposób:

```azurecli-interactive
az network nic ip-config create \
--name dsIp6Config_NIC1 \
--nic-name myNicVM1 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name dsLB

az network nic ip-config create \
--name dsIp6Config_NIC2 \
--nic-name myNicVM2 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer

az network nic ip-config create \
--name dsIp6Config_NIC3 \
--nic-name myNicVM3 \
--resource-group MyResourceGroupSLB \
--vnet-name myVnet \
--subnet mySubnet \
--private-ip-address-version IPv6 \
--lb-address-pools dsLbBackEndPool_v6 \
--lb-name myLoadBalancer
```

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Wyświetlanie sieci wirtualnej podwójnego stosu IPv6 w Azure Portal

Sieć wirtualną o podwójnym stosie IPv6 można wyświetlić w Azure Portal w następujący sposób:
1. Na pasku wyszukiwania portalu wprowadź *myVnet*.
2. Gdy **myVnet** pojawia się w wynikach wyszukiwania, wybierz ją. Spowoduje to uruchomienie strony **Przegląd** sieci wirtualnej o podwójnym stosie o nazwie *myVNet*. Sieć wirtualna o podwójnym stosie pokazuje trzy karty sieciowe z konfiguracją protokołów IPv4 i IPv6 znajdującą się w podsieci o podwójnym stosie o nazwie Moja *podsieć*.

  ![Sieć wirtualna o podwójnym stosie IPv6 na platformie Azure](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurecli-interactive
az group delete --name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Następne kroki

W tym artykule zostały zaktualizowane istniejące usługa Load Balancer w warstwie Standardowa z konfiguracją adresów IP frontonu IPv4 do konfiguracji podwójnego stosu (IPv4 i IPv6). Do kart sieciowych maszyn wirtualnych w puli zaplecza dodano także konfiguracje protokołu IPv6. Aby dowiedzieć się więcej o obsłudze protokołu IPv6 w sieciach wirtualnych platformy Azure, zobacz [co to jest protokół IPv6 dla systemu azure Virtual Network?](ipv6-overview.md)
