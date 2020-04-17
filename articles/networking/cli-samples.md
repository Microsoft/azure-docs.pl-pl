---
title: Przykłady interfejsu wiersza polecenia platformy Azure — sieć
description: Przykłady interfejsu wiersza polecenia platformy Azure
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: timlt
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/25/2017
ms.author: kumud
ms.openlocfilehash: 84754a61bfe9537e928759aefbcb5dcddce33089
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457964"
---
# <a name="azure-cli-samples-for-networking"></a>Przykłady interfejsu wiersza polecenia platformy Azure dla sieci

Poniższa tabela zawiera linki do skryptów bash utworzonych za pomocą interfejsu wiersza polecenia platformy Azure.

| | |
|-|-|
|**Łączność między zasobami platformy Azure**||
| [Tworzenie sieci wirtualnej dla aplikacji wielowarstwowych](./scripts/virtual-network-cli-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy sieć wirtualną z podsieciami frontonu i zaplecza. Ruch do podsieci frontonu jest ograniczony do protokołów HTTP i SSH, a ruch do podsieci zaplecza jest ograniczony do portu 3306 środowiska MySQL. |
| [Komunikacja równorzędna dwóch sieci wirtualnych](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy i łączy dwie sieci wirtualne w tym samym regionie. |
| [Kierowanie ruchu przez wirtualne urządzenie sieciowe](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy sieć wirtualną z podsieciami frontonu i zaplecza oraz maszyną wirtualną, która może kierować ruchem między dwiema podsieciami. |
| [Filtrowanie przychodzącego i wychodzącego ruchu sieciowego maszyny wirtualnej](./scripts/virtual-network-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy sieć wirtualną z podsieciami frontonu i zaplecza. Przychodzący ruch sieciowy do podsieci frontowej jest ograniczony do protokołu HTTP, HTTPS i SSH. Ruch wychodzący do Internetu z podsieci zaplecza jest niedozwolony. |
|**Równoważenie obciążenia i kierunek ruchu**||
| [Równoważenie obciążenia wielu witryn sieci Web na maszynach wirtualnych](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy dwie maszyny wirtualne z wieloma konfiguracjami IP, przyłączone do zestawu dostępności platformy Azure, dostępne za pośrednictwem modułu równoważenia obciążenia platformy Azure. |
| [Bezpośredni ruch w wielu regionach w celu zapewnienia wysokiej dostępności aplikacji](./scripts/traffic-manager-cli-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Tworzy dwa plany usługi aplikacji, dwie aplikacje sieci web, profil menedżera ruchu i dwa punkty końcowe menedżera ruchu. |
| | |
