---
title: Usługa Azure Load Balancer w warstwie Standardowa i usługa Virtual Machine Scale Sets
titleSuffix: Azure Standard Load Balancer and Virtual Machine Scale Sets
description: Korzystając z tej ścieżki szkoleniowej, Rozpocznij pracę z usługą Azure usługa Load Balancer w warstwie Standardowa i Virtual Machine Scale Sets.
services: load-balancer
documentationcenter: na
author: irenehua
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/17/2020
ms.author: irenehua
ms.openlocfilehash: 7e1df754a4a4ca5878d93d53282fd39191313b54
ms.sourcegitcommit: 6d6030de2d776f3d5fb89f68aaead148c05837e2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97883167"
---
# <a name="azure-load-balancer-with-azure-virtual-machine-scale-sets"></a>Azure Load Balancer z zestawami skalowania maszyn wirtualnych platformy Azure

Podczas pracy z zestawami skalowania maszyn wirtualnych i usługą równoważenia obciążenia należy wziąć pod uwagę następujące wytyczne:

## <a name="port-forwarding-and-inbound-nat-rules"></a>Przekazywanie portów i reguły NAT dla ruchu przychodzącego:
  * Po utworzeniu zestawu skalowania nie można zmodyfikować portu zaplecza dla reguły równoważenia obciążenia używanej przez sondę kondycji modułu równoważenia obciążenia. Aby zmienić port, można usunąć sondę kondycji, aktualizując zestaw skalowania maszyn wirtualnych platformy Azure, zaktualizować port, a następnie ponownie skonfiguruj sondę kondycji.
  * W przypadku korzystania z zestawu skalowania maszyn wirtualnych w puli zaplecza modułu równoważenia obciążenia domyślne reguły NAT dla ruchu przychodzącego zostaną utworzone automatycznie.
  
## <a name="inbound-nat-pool"></a>Pula NAT dla ruchu przychodzącego:
  * Każdy zestaw skalowania maszyn wirtualnych musi mieć co najmniej jedną przychodzącą pulę NAT. 
  * Pula NAT dla ruchu przychodzącego jest kolekcją reguł NAT dla ruchu przychodzącego. Jedna pula NAT dla ruchu przychodzącego nie obsługuje wielu zestawów skalowania maszyn wirtualnych.

## <a name="load-balancing-rules"></a>Reguły równoważenia obciążenia:
  * W przypadku korzystania z zestawu skalowania maszyn wirtualnych w puli zaplecza modułu równoważenia obciążenia zostanie automatycznie utworzona domyślna reguła równoważenia obciążenia.
  
## <a name="outbound-rules"></a>Reguły ruchu wychodzącego:
  *  Aby utworzyć regułę ruchu wychodzącego dla puli zaplecza, do której już odwołuje się reguła równoważenia obciążenia, należy najpierw zaznaczyć **opcję "Utwórz niejawne reguły wychodzące"** **w portalu, gdy** zostanie utworzona reguła równoważenia obciążenia przychodzącego.

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="Tworzenie reguły równoważenia obciążenia" border="true":::

Poniższe metody umożliwiają wdrożenie zestawu skalowania maszyn wirtualnych z istniejącym modułem równoważenia obciążenia platformy Azure.

* [Skonfiguruj zestaw skalowania maszyn wirtualnych za pomocą istniejącego Azure Load Balancer przy użyciu Azure Portal](./configure-vm-scale-set-portal.md).
* [Skonfiguruj zestaw skalowania maszyn wirtualnych za pomocą istniejącego Azure Load Balancer przy użyciu Azure PowerShell](./configure-vm-scale-set-powershell.md).
* [Skonfiguruj zestaw skalowania maszyn wirtualnych za pomocą istniejącego Azure Load Balancer przy użyciu interfejsu wiersza polecenia platformy Azure](./configure-vm-scale-set-cli.md).
* [Zaktualizuj lub Usuń istniejące Azure Load Balancer używane przez zestaw skalowania maszyn wirtualnych](./update-load-balancer-with-vm-scale-set.md)
