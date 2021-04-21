---
title: Dodawanie protokołu IPv6 do aplikacji IPv4 w sieci wirtualnej platformy Azure — interfejs wiersza polecenia platformy Azure
titlesuffix: Azure Virtual Network
description: W tym artykule przedstawiono sposób wdrażania adresów IPv6 w istniejącej aplikacji w sieci wirtualnej platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure.
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
ms.openlocfilehash: 5835ea4d80f9c4111b76672facc4a0250ae0079a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769863"
---
# <a name="add-ipv6-to-an-ipv4-application-in-azure-virtual-network---azure-cli"></a>Dodawanie protokołu IPv6 do aplikacji IPv4 w sieci wirtualnej platformy Azure — interfejs wiersza polecenia platformy Azure

W tym artykule pokazano, jak dodać adresy IPv6 do aplikacji, która używa publicznego adresu IP IPv4 w sieci wirtualnej platformy Azure dla serwera usługa Load Balancer w warstwie Standardowa interfejsu wiersza polecenia platformy Azure. Uaktualnienie w miejscu obejmuje sieć wirtualną i podsieć, serwer usługa Load Balancer w warstwie Standardowa z konfiguracjami frontonu IPv4 + IPV6, maszyny wirtualne z kartami sieciowym, które mają konfiguracje IPv4 i IPv6, sieciową grupę zabezpieczeń i publiczne ip.

## <a name="prerequisites"></a>Wymagania wstępne

- W tym artykule przyjęto założenie, że wdrożono usługa Load Balancer w warstwie Standardowa zgodnie z opisem w przewodniku Szybki start: tworzenie aplikacji usługa Load Balancer w warstwie Standardowa [interfejsie wiersza polecenia platformy Azure.](../load-balancer/quickstart-load-balancer-standard-public-cli.md)

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.28 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="create-ipv6-addresses"></a>Tworzenie adresów IPv6

Utwórz publiczny adres IPv6 za pomocą az [network public-ip create](/cli/azure/network/public-ip) dla swojego usługa Load Balancer w warstwie Standardowa. Poniższy przykład tworzy publiczny adres IP IPv6 o *nazwie PublicIP_v6* w grupie zasobów *myResourceGroupSLB:*

```azurecli-interactive
az network public-ip create \
--name PublicIP_v6 \
--resource-group MyResourceGroupSLB \
--location EastUS \
--sku Standard \
--allocation-method static \
--version IPv6
```

## <a name="configure-ipv6-load-balancer-frontend"></a>Konfigurowanie frontonu usługi równoważenia obciążenia IPv6

Skonfiguruj usługę równoważenia obciążenia przy użyciu nowego adresu IP IPv6 przy użyciu [narzędzia az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_create) w następujący sposób:

```azurecli-interactive
az network lb frontend-ip create \
--lb-name myLoadBalancer \
--name dsLbFrontEnd_v6 \
--resource-group MyResourceGroupSLB \
--public-ip-address PublicIP_v6
```

## <a name="configure-ipv6-load-balancer-backend-pool"></a>Konfigurowanie puli zaplecza usługi równoważenia obciążenia IPv6

Utwórz pulę zaplecza dla kart sieciowych z adresami IPv6 przy użyciu [narzędzia az network lb address-pool create](/cli/azure/network/lb/address-pool#az_network_lb_address_pool_create) w następujący sposób:

```azurecli-interactive
az network lb address-pool create \
--lb-name myLoadBalancer \
--name dsLbBackEndPool_v6 \
--resource-group MyResourceGroupSLB
```

## <a name="configure-ipv6-load-balancer-rules"></a>Konfigurowanie reguł równoważenia obciążenia IPv6

Utwórz reguły równoważenia obciążenia IPv6 za pomocą [az network lb rule create](/cli/azure/network/lb/rule#az_network_lb_rule_create).

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

Dodaj zakresy adresów IPv6 do sieci wirtualnej i podsieci hostowania usługi równoważenia obciążenia w następujący sposób:

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

## <a name="add-ipv6-configuration-to-nics"></a>Dodawanie konfiguracji protokołu IPv6 do karty sieciowej

Skonfiguruj karty sieciowe maszyny wirtualnej przy użyciu adresu IPv6 za pomocą [narzędzia az network nic ip-config create](/cli/azure/network/nic/ip-config#az_network_nic_ip_config_create) w następujący sposób:

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

## <a name="view-ipv6-dual-stack-virtual-network-in-azure-portal"></a>Wyświetlanie sieci wirtualnej podwójnego stosu IPv6 w sieci Azure Portal

Sieć wirtualną podwójnego stosu IPv6 można wyświetlić w Azure Portal w następujący sposób:
1. Na pasku wyszukiwania portalu wprowadź *myVnet*.
2. Gdy w wynikach wyszukiwania pojawi się sieć **myVnet,** wybierz ją. Zostanie uruchamiana strona **Przegląd** sieci wirtualnej z podwójnym stosem o *nazwie myVNet.* Sieć wirtualna z podwójnym stosem pokazuje trzy karty sieciowe z konfiguracjami IPv4 i IPv6, które znajdują się w podsieci z dwoma stosami o *nazwie mySubnet.*

  ![Sieć wirtualna podwójnego stosu IPv6 na platformie Azure](./media/ipv6-add-to-existing-vnet-powershell/ipv6-dual-stack-vnet.png)


## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy grupa zasobów, maszyna wirtualna i wszystkie pokrewne zasoby nie będą już potrzebne, można je usunąć za pomocą polecenia [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurecli-interactive
az group delete --name MyAzureResourceGroupSLB
```

## <a name="next-steps"></a>Następne kroki

W tym artykule zaktualizowano istniejącą usługę usługa Load Balancer w warstwie Standardowa konfiguracją adresu IP frontonu IPv4 do konfiguracji podwójnego stosu (IPv4 i IPv6). Dodano również konfiguracje protokołu IPv6 do karty sieciowe maszyn wirtualnych w puli zaplecza. Aby dowiedzieć się więcej na temat obsługi protokołu IPv6 w sieciach wirtualnych platformy Azure, zobacz Co to jest [protokół IPv6 dla](ipv6-overview.md) usługi Azure Virtual Network?
