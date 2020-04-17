---
title: Przykłady programu Azure PowerShell — sieć
description: Przykłady dla programu Azure PowerShell
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/24/2017
ms.author: gwallace
ms.openlocfilehash: ca6ac145db0536d3cf7e5bcc72a58d72101ab12a
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81459121"
---
# <a name="azure-powershell-samples-for-networking"></a>Przykłady programu Azure PowerShell dla sieci

Poniższa tabela zawiera łącza do skryptów utworzonych przy użyciu programu Azure PowerShell.

| | |
|-|-|
|**Łączność między zasobami platformy Azure**||
| [Tworzenie sieci wirtualnej dla aplikacji wielowarstwowych](./scripts/virtual-network-powershell-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy sieć wirtualną z podsieciami frontonu i zaplecza. Ruch do podsieci frontonu jest ograniczony do protokołu HTTP, a ruch do podsieci zaplecza jest ograniczony do portu 1433 środowiska SQL. |
| [Komunikacja równorzędna dwóch sieci wirtualnych](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy i łączy dwie sieci wirtualne w tym samym regionie. |
| [Kierowanie ruchu przez wirtualne urządzenie sieciowe](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy sieć wirtualną z podsieciami frontonu i zaplecza oraz maszyną wirtualną, która może kierować ruchem między dwiema podsieciami. |
| [Filtrowanie przychodzącego i wychodzącego ruchu sieciowego maszyny wirtualnej](./scripts/virtual-network-powershell-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy sieć wirtualną z podsieciami frontonu i zaplecza. Przychodzący ruch sieciowy do podsieci frontowej jest ograniczony do protokołu HTTP i HTTPS.Inbound network traffic to the front-end subs is limited to HTTP and HTTPS.. Ruch wychodzący do Internetu z podsieci zaplecza jest niedozwolony. |
|**Równoważenie obciążenia i kierunek ruchu**||
| [Równoważenie obciążenia ruchem maszyn wirtualnych w celu uzyskania wysokiej dostępności](./scripts/load-balancer-windows-powershell-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy kilka maszyn wirtualnych w konfiguracji o wysokiej dostępności i z równoważenia obciążenia. |
| [Bezpośredni ruch w wielu regionach w celu zapewnienia wysokiej dostępności aplikacji](./scripts/traffic-manager-powershell-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Tworzy dwa plany usługi aplikacji, dwie aplikacje sieci web, profil menedżera ruchu i dwa punkty końcowe menedżera ruchu. |
| | |
