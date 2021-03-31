---
title: Wdrażanie aplikacji podwójnego stosu IPv6 przy użyciu podstawowego Load Balancer w szablonie usługi Azure Virtual Network — Menedżer zasobów
titlesuffix: Azure Virtual Network
description: W tym artykule pokazano, jak wdrożyć aplikację dwustosową protokołu IPv6 w usłudze Azure Virtual Network przy użyciu szablonów maszyn wirtualnych Azure Resource Manager.
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
ms.openlocfilehash: 548b416ed0f21df83dafdb1c2d7015c625c36e4c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95244297"
---
# <a name="deploy-an-ipv6-dual-stack-application-with-basic-load-balancer-in-azure---template"></a>Wdrażanie aplikacji dwustosowej IPv6 przy użyciu podstawowego Load Balancer na platformie Azure — szablon

Ten artykuł zawiera listę zadań konfiguracyjnych IPv6 z częścią szablonu maszyny wirtualnej Azure Resource Manager, która ma zastosowanie do. Użyj szablonu opisanego w tym artykule, aby wdrożyć aplikację podwójnego stosu (IPv4 + IPv6) z podstawową Load Balancer, która obejmuje sieć wirtualną o podwójnej stercie z podsieciami IPv4 i IPv6, podstawowa Load Balancer z dwoma (IPv4 + IPv6) konfiguracjami frontonu, maszyn wirtualnych z kartami sieciowymi, które mają dwie konfiguracje IP, sieciowe grupy zabezpieczeń i publiczne adresy IP.

Aby wdrożyć aplikację z podwójnym stosem (IPV4 + IPv6) za pomocą usługa Load Balancer w warstwie Standardowa, zobacz [wdrażanie aplikacji dwustosowej przy użyciu protokołu IPv6 z szablonem usługa Load Balancer w warstwie Standardowa-Template](ipv6-configure-standard-load-balancer-template-json.md).

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
              "sourceAddressPrefix": "fd00:db8:deca:deed::/64",
              "destinationAddressPrefix": "fd00:db8:deca:deed::/64",
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
              "addressPrefix": "fd00:db8:deca:deed::/64",
              "nextHopType": "VirtualAppliance",
              "nextHopIpAddress": "fd00:db8:ace:f00d::1"
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
      "properties": {
        "publicIPAllocationMethod": "Dynamic",
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
Aby wdrożyć aplikację dwustosową IPv6 z podstawową Load Balancer w usłudze Azure Virtual Network przy użyciu szablonu Azure Resource Manager, Wyświetl przykładowy szablon w [tym miejscu](https://azure.microsoft.com/resources/templates/ipv6-in-vnet/).

## <a name="next-steps"></a>Następne kroki

Możesz znaleźć szczegółowe informacje o cenach [publicznych adresów IP](https://azure.microsoft.com/pricing/details/ip-addresses/), [przepustowości sieci](https://azure.microsoft.com/pricing/details/bandwidth/)lub [Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).
