---
title: Dodawanie reguł dla usługi Azure usługa Load Balancer w warstwie Standardowa i zestawów skalowania maszyn wirtualnych
titleSuffix: Add rules for Azure Standard Load Balancer and virtual machine scale sets
description: Korzystając z tej ścieżki szkoleniowej, Rozpocznij pracę z usługą Azure usługa Load Balancer w warstwie Standardowa i zestawami skalowania maszyn wirtualnych.
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
ms.openlocfilehash: 7a2e0531427343a2ec267de54cee05b5eb25889f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99592283"
---
# <a name="add-rules-for-azure-load-balancer-with-virtual-machine-scale-sets"></a>Dodawanie reguł dla Azure Load Balancer przy użyciu zestawów skalowania maszyn wirtualnych

Podczas pracy z zestawami skalowania maszyn wirtualnych i Azure Load Balancer należy wziąć pod uwagę następujące wskazówki.

## <a name="port-forwarding-and-inbound-nat-rules"></a>Przekazywanie portów i reguły NAT dla ruchu przychodzącego

Po utworzeniu zestawu skalowania nie można zmodyfikować portu zaplecza dla reguły równoważenia obciążenia używanej przez sondę kondycji modułu równoważenia obciążenia. Aby zmienić port, Usuń sondę kondycji, aktualizując zestaw skalowania maszyn wirtualnych i aktualizując port. Następnie ponownie skonfiguruj sondę kondycji.

Jeśli używasz zestawu skalowania maszyn wirtualnych w puli zaplecza modułu równoważenia obciążenia, domyślne reguły NAT dla ruchu przychodzącego są tworzone automatycznie.
  
## <a name="inbound-nat-pool"></a>Pula NAT dla ruchu przychodzącego

Każdy zestaw skalowania maszyn wirtualnych musi mieć co najmniej jedną przychodzącą pulę NAT. Pula NAT dla ruchu przychodzącego jest kolekcją reguł NAT dla ruchu przychodzącego. Jedna pula NAT dla ruchu przychodzącego nie obsługuje wielu zestawów skalowania maszyn wirtualnych.

## <a name="load-balancing-rules"></a>Reguły równoważenia obciążenia

W przypadku korzystania z zestawu skalowania maszyn wirtualnych w puli zaplecza modułu równoważenia obciążenia zostanie automatycznie utworzona domyślna reguła równoważenia obciążenia.
  
## <a name="outbound-rules"></a>Reguły ruchu wychodzącego

Aby utworzyć regułę ruchu wychodzącego dla puli zaplecza, do której już odwołuje się reguła równoważenia obciążenia, wybierz pozycję **nie** w obszarze **Utwórz niejawne reguły wychodzące** w Azure Portal podczas tworzenia przychodzącej reguły równoważenia obciążenia.

  :::image type="content" source="./media/vm-scale-sets/load-balancer-and-vm-scale-sets.png" alt-text="Zrzut ekranu przedstawiający tworzenie reguły równoważenia obciążenia." border="true":::

Użyj następujących metod, aby wdrożyć zestaw skalowania maszyn wirtualnych z istniejącym wystąpieniem Load Balancer:

* [Konfigurowanie zestawu skalowania maszyn wirtualnych z istniejącym wystąpieniem Azure Load Balancer przy użyciu Azure Portal](./configure-vm-scale-set-portal.md)
* [Konfigurowanie zestawu skalowania maszyn wirtualnych z istniejącym wystąpieniem Azure Load Balancer przy użyciu Azure PowerShell](./configure-vm-scale-set-powershell.md)
* [Konfigurowanie zestawu skalowania maszyn wirtualnych z istniejącym wystąpieniem Azure Load Balancer przy użyciu interfejsu wiersza polecenia platformy Azure](./configure-vm-scale-set-cli.md)
* [Aktualizowanie lub usuwanie istniejącego wystąpienia Azure Load Balancer używanego przez zestaw skalowania maszyn wirtualnych](./update-load-balancer-with-vm-scale-set.md)
