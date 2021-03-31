---
title: Przykłady interfejsu wiersza polecenia platformy Azure — sieć
description: Poznaj przykłady interfejsu wiersza polecenia platformy Azure dotyczące sieci, w tym przykłady łączności między zasobami platformy Azure i przykładami na potrzeby równoważenia obciążenia i kierunku ruchu.
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
ms.custom: devx-track-azurecli
ms.openlocfilehash: c038f0d238646f43b93ba2a2c6a1120ab5feccee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87497294"
---
# <a name="azure-cli-samples-for-networking"></a>Przykłady interfejsu wiersza polecenia platformy Azure dla sieci

Poniższa tabela zawiera linki do skryptów bash utworzonych za pomocą interfejsu wiersza polecenia platformy Azure.

| Skrypt | Opis |
|-|-|
|**Łączność między zasobami platformy Azure**||
| [Tworzenie sieci wirtualnej dla aplikacji wielowarstwowych](./scripts/virtual-network-cli-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy sieć wirtualną z podsieciami frontonu i zaplecza. Ruch do podsieci frontonu jest ograniczony do protokołów HTTP i SSH, a ruch do podsieci zaplecza jest ograniczony do portu 3306 środowiska MySQL. |
| [Komunikacja równorzędna dwóch sieci wirtualnych](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy i łączy dwie sieci wirtualne w tym samym regionie. |
| [Kierowanie ruchu przez wirtualne urządzenie sieciowe](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy sieć wirtualną z podsieciami frontonu i zaplecza oraz maszyną wirtualną, która może kierować ruchem między dwiema podsieciami. |
| [Filtrowanie przychodzącego i wychodzącego ruchu sieciowego maszyny wirtualnej](./scripts/virtual-network-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy sieć wirtualną z podsieciami frontonu i zaplecza. Ruch sieciowy przychodzący do podsieci frontonu jest ograniczony do protokołów HTTP, HTTPS i SSH. Ruch wychodzący do Internetu z podsieci zaplecza jest niedozwolony. |
|**Równoważenie obciążenia i kierunek ruchu**||
| [Równoważenie obciążenia wielu witryn sieci Web na maszynach wirtualnych](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | Tworzy dwie maszyny wirtualne z wieloma konfiguracjami adresów IP, które są przyłączone do zestawu dostępności platformy Azure, dostępne za pośrednictwem Azure Load Balancer. |
| [Bezpośredni ruch w wielu regionach w celu zapewnienia wysokiej dostępności aplikacji](./scripts/traffic-manager-cli-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Tworzy dwa plany usługi App Service, dwie aplikacje sieci Web, profil programu Traffic Manager i dwa punkty końcowe w usłudze Traffic Manager. |
| | |
