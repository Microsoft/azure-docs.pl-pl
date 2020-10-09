---
title: Przykłady szablonów usługi Azure Resource Manager dla sieci wirtualnej | Microsoft Docs
description: Dowiedz się więcej na temat różnych szablonów usługi Azure Resource Manager, za pomocą których można wdrażać sieci wirtualne platformy Azure.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: kumud
ms.openlocfilehash: e6e759fa75e1957e726dc9f6f3d0035869f35e3f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84707824"
---
# <a name="azure-resource-manager-template-samples-for-virtual-network"></a>Przykłady szablonów usługi Azure Resource Manager dla sieci wirtualnej

Poniższa tabela zawiera linki do przykładów szablonów usługi Azure Resource Manager. Szablony można wdrażać przy użyciu witryny Azure [Portal](../azure-resource-manager/templates/deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [interfejsu wiersza polecenia](../azure-resource-manager/templates/deploy-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json) platformy Azure lub programu Azure [PowerShell](../azure-resource-manager/templates/deploy-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Aby dowiedzieć się, jak tworzyć własne szablony, zobacz [Create your first template](../azure-resource-manager/resource-manager-create-first-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Tworzenie pierwszego szablonu) i [Understand the structure and syntax of Azure Resource Manager templates](../azure-resource-manager/templates/template-syntax.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (Omówienie struktury i składni szablonów usługi Azure Resource Manager).

Aby zapoznać się z właściwościami i składnią języka JSON, których należy użyć w szablonach, zobacz [Typy zasobów Microsoft.Network](/azure/templates/microsoft.network/allversions).


| Zadanie | Opis |
|----|----|
|[Tworzenie sieci wirtualnej z dwiema podsieciami](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets)| Tworzy sieć wirtualną z dwiema podsieciami.|
|[Kierowanie ruchu przez wirtualne urządzenie sieciowe](https://github.com/Azure/azure-quickstart-templates/tree/master/201-userdefined-routes-appliance)| Tworzy sieć wirtualną z trzema podsieciami. W każdej z tych podsieci wdraża maszynę wirtualną. Tworzy tabelę tras zawierającą trasy do kierowania ruchu z jednej podsieci do drugiej przez maszynę wirtualną w trzeciej podsieci. Kojarzy tabelę tras z jedną z podsieci.|
|[Tworzenie punktu końcowego usługi dla sieci wirtualnej dla usługi Azure Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vnet-2subnets-service-endpoints-storage-integration)|Tworzy nową sieć wirtualną z dwiema podsieciami oraz interfejs sieciowy w każdej podsieci. Włącza punkt końcowy usługi do usługi Azure Storage dla jednej z podsieci i przypisuje nowe konto magazynu do tej podsieci.|
|[Łączenie dwóch sieci wirtualnych](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vnet-to-vnet-peering)| Tworzy dwie sieci wirtualne i komunikację równorzędną między nimi.|
|[Tworzenie maszyny wirtualnej z wieloma adresami IP](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-multiple-ipconfig)| Tworzy maszynę wirtualną z systemem Windows lub Linux i wieloma adresami IP.|
|[Konfigurowanie sieci wirtualnej o podwójnym stosie IPv4 + IPv6](https://github.com/Azure/azure-quickstart-templates/tree/master/ipv6-in-vnet)|Wdraża sieć wirtualną o podwójnej stercie (IPv4 + IPv6) z dwiema maszynami wirtualnymi i podstawową Load Balancer platformy Azure przy użyciu adresów IPv4 i IPv6. |
