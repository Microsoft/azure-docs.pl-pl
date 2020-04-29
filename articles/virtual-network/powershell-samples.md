---
title: Przykłady programu Azure PowerShell dla sieci wirtualnej
description: Przykłady programu Azure PowerShell dla sieci wirtualnej.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: de752cdacf17193d5be95b2b9f887938ace2d50f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74091869"
---
# <a name="azure-powershell-samples-for-virtual-network"></a>Przykłady programu Azure PowerShell dla sieci wirtualnej

Poniższa tabela zawiera linki do skryptów programu Azure PowerShell:

| | |
|----|----|
| [Tworzenie sieci wirtualnej dla aplikacji wielowarstwowych](./scripts/virtual-network-powershell-sample-multi-tier-application.md) | Tworzy sieć wirtualną z podsieciami frontonu i zaplecza. Ruch do podsieci frontonu jest ograniczony do protokołu HTTP, a ruch do podsieci zaplecza jest ograniczony do portu 1433 środowiska SQL. |
| [Komunikacja równorzędna dwóch sieci wirtualnych](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md) | Tworzy i łączy dwie sieci wirtualne w tym samym regionie. |
| [Kierowanie ruchu przez wirtualne urządzenie sieciowe](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md) | Tworzy sieć wirtualną z podsieciami frontonu i zaplecza oraz maszyną wirtualną, która może kierować ruchem między dwiema podsieciami. |
| [Filtrowanie przychodzącego i wychodzącego ruchu sieciowego maszyny wirtualnej](./scripts/virtual-network-powershell-sample-filter-network-traffic.md) | Tworzy sieć wirtualną z podsieciami frontonu i zaplecza. Ruch sieciowy przychodzący do podsieci frontonu jest ograniczony do protokołów HTTP i HTTPS. Ruch wychodzący do Internetu z podsieci zaplecza jest niedozwolony. |
|[Konfigurowanie sieci wirtualnej o podwójnym stosie IPv4 + IPv6 z podstawową Load Balancer](./scripts/virtual-network-powershell-sample-ipv6-dual-stack.md)|Wdraża sieć wirtualną o podwójnej stercie (IPv4 + IPv6) z dwiema maszynami wirtualnymi i podstawową Load Balancer platformy Azure przy użyciu adresów IPv4 i IPv6. |
|[Konfigurowanie sieci wirtualnej o podwójnym stosie IPv4 + IPv6 przy użyciu usługa Load Balancer w warstwie Standardowa](./scripts/virtual-network-powershell-sample-ipv6-dual-stack-standard-load-balancer.md)|Wdraża sieć wirtualną dual-stack (IPv4 + IPv6) z dwiema maszynami wirtualnymi i usługa Load Balancer w warstwie Standardowa platformy Azure z publicznymi adresami IP w protokołach IPv4 i IPv6. |
