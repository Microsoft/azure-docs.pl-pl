---
title: Wdrażanie aplikacji dwustosowej IPv6 przy użyciu szablonu usługi Azure Virtual Network — Menedżer zasobów
titlesuffix: Azure Virtual Network
description: W tym artykule pokazano, jak wdrożyć aplikację dwustosową IPv6 za pomocą usługa Load Balancer w warstwie Standardowa w usłudze Azure Virtual Network przy użyciu szablonów maszyn wirtualnych Azure Resource Manager.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 732c3305b9d678bedc29e490153fac650398a238
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95016177"
---
# <a name="deploy-an-ipv6-dual-stack-application-in-azure-virtual-network---template"></a>Wdrażanie aplikacji podwójnego stosu IPv6 w usłudze Azure Virtual Network — szablon

Ten artykuł zawiera listę zadań konfiguracyjnych IPv6 z częścią szablonu maszyny wirtualnej Azure Resource Manager, która ma zastosowanie do. Użyj szablonu opisanego w tym artykule, aby wdrożyć aplikację podwójnego stosu (IPv4 + IPv6) przy użyciu usługa Load Balancer w warstwie Standardowa na platformie Azure, która obejmuje sieć wirtualną o podwójnej stercie z podsieciami IPv4 i IPv6, usługa Load Balancer w warstwie Standardowa z podwójnymi konfiguracjami frontonu (IPv4 + IPv6), maszynami wirtualnymi z kartami sieciowymi, które mają dwie konfiguracje adresów IP, sieciowych grup zabezpieczeń i publicznych IP 

## <a name="required-configurations"></a>Wymagane konfiguracje

Wyszukaj sekcje szablonu w szablonie, aby zobaczyć, gdzie powinny wystąpić.

### <a name="ipv6-addressspace-for-the-virtual-network"></a>AddressSpace IPv6 dla sieci wirtualnej

Sekcja szablonu do dodania:

```JSON
        "addressSpace": {
          "addressPrefixes": [
            "[variables('vnetv4AddressRange')]",
            "[variables('vnetv6AddressRange')]"    
```

### <a name="ipv6-subnet-within-the-ipv6-virtual-network-addressspace"></a>Podsieć IPv6 w sieci wirtualnej IPv6 addressSpace

Sekcja szablonu do dodania:
```JSON
          {
            "name": "V6Subnet",
            "properties": {
              "addressPrefix": "[variables('subnetv6AddressRange')]"
            }

```

### <a name="ipv6-configuration-for-the-nic"></a>Konfiguracja protokołu IPv6 dla karty sieciowej

Sekcja szablonu do dodania:
```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

### <a name="ipv6-network-security-group-nsg-rules"></a>Reguły sieciowej grupy zabezpieczeń (sieciowej grupy zabezpieczeń) IPv6

```JSON
          {
            "name": "default-allow-rdp",
            "properties": {
              "description": "Allow RDP",
              "protocol": "Tcp",
              "sourcePortRange": "33819-33829",
              "destinationPortRange": "5000-6000",
              "sourceAddressPrefix": "2001:db8:deca:deed::/64",
              "destinationAddressPrefix": "2001:db8:deca:deed::/64",
              "access": "Allow",
              "priority": 1003,
              "direction": "Inbound"
            }
```

## <a name="conditional-configuration"></a>Konfiguracja warunkowa

Jeśli używasz sieciowego urządzenia wirtualnego, Dodaj trasy IPv6 w tabeli tras. W przeciwnym razie ta konfiguracja jest opcjonalna.

```JSON
    {
      "type": "Microsoft.Network/routeTables",
      "name": "v6route",
      "apiVersion": "[variables('ApiVersion')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "routes": [
          {
            "name": "v6route",
            "properties": {
              "addressPrefix": "2001:db8:deca:deed::/64",
              "nextHopType": "VirtualAppliance",
              "nextHopIpAddress": "2001:db8:ace:f00d::1"
            }
```

## <a name="optional-configuration"></a>Konfiguracja opcjonalna

### <a name="ipv6-internet-access-for-the-virtual-network"></a>Dostęp do Internetu IPv6 dla sieci wirtualnej

```JSON
{
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-public-ip-addresses"></a>Publiczne adresy IP protokołu IPv6

```JSON
    {
      "apiVersion": "[variables('ApiVersion')]",
      "type": "Microsoft.Network/publicIPAddresses",
      "name": "lbpublicip-v6",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard"
      },
      "properties": {
        "publicIPAllocationMethod": "Static",
        "publicIPAddressVersion": "IPv6"
      }
```

### <a name="ipv6-front-end-for-load-balancer"></a>Fronton IPv6 dla Load Balancer

```JSON
          {
            "name": "LBFE-v6",
            "properties": {
              "publicIPAddress": {
                "id": "[resourceId('Microsoft.Network/publicIPAddresses','lbpublicip-v6')]"
              }
```

### <a name="ipv6-back-end-address-pool-for-load-balancer"></a>Pula adresów zaplecza IPv6 dla Load Balancer

```JSON
              "backendAddressPool": {
                "id": "[concat(resourceId('Microsoft.Network/loadBalancers', 'loadBalancer'), '/backendAddressPools/LBBAP-v6')]"
              },
              "protocol": "Tcp",
              "frontendPort": 8080,
              "backendPort": 8080
            },
            "name": "lbrule-v6"
```

### <a name="ipv6-load-balancer-rules-to-associate-incoming-and-outgoing-ports"></a>Reguły modułu równoważenia obciążenia IPv6 do kojarzenia portów przychodzących i wychodzących

```JSON
          {
            "name": "ipconfig-v6",
            "properties": {
              "privateIPAllocationMethod": "Dynamic",
          "privateIPAddressVersion":"IPv6",
              "subnet": {
                "id": "[variables('v6-subnet-id')]"
              },
              "loadBalancerBackendAddressPools": [
                {
                  "id": "[concat(resourceId('Microsoft.Network/loadBalancers','loadBalancer'),'/backendAddressPools/LBBAP-v6')]"
                }
```

## <a name="sample-vm-template-json"></a>Przykładowy kod JSON szablonu maszyny wirtualnej
Aby wdrożyć aplikację dwustosową protokołu IPv6 w usłudze Azure Virtual Network przy użyciu szablonu Azure Resource Manager, Wyświetl przykładowy szablon w [tym miejscu](https://azure.microsoft.com/resources/templates/ipv6-in-vnet-stdlb/).

## <a name="next-steps"></a>Następne kroki

Możesz znaleźć szczegółowe informacje o cenach [publicznych adresów IP](https://azure.microsoft.com/pricing/details/ip-addresses/), [przepustowości sieci](https://azure.microsoft.com/pricing/details/bandwidth/)lub [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).
