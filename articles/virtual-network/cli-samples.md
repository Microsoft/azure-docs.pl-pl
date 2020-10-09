---
title: Przykłady interfejsu wiersza polecenia platformy Azure dla sieci wirtualnej
description: Poznaj różne przykładowe skrypty, których można użyć do wykonywania zadań w interfejsie wiersza polecenia platformy Azure, w tym tworzenie sieci wirtualnej dla aplikacji wielowarstwowych.
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
ms.date: 07/15/2019
ms.author: kumud
ms.custom: devx-track-azurecli
ms.openlocfilehash: 539d11205ffead52d7f40526f2c712e8cf8b5cdd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87501444"
---
# <a name="azure-cli-samples-for-virtual-network"></a>Przykłady interfejsu wiersza polecenia platformy Azure dla sieci wirtualnej

Poniższa tabela zawiera linki do skryptów bash utworzonych za pomocą poleceń interfejsu wiersza polecenia platformy Azure:

| Skrypt | Opis |
|----|----|
| [Tworzenie sieci wirtualnej dla aplikacji wielowarstwowych](./scripts/virtual-network-cli-sample-multi-tier-application.md) | Tworzy sieć wirtualną z podsieciami frontonu i zaplecza. Ruch do podsieci frontonu jest ograniczony do protokołów HTTP i SSH, a ruch do podsieci zaplecza jest ograniczony do portu 3306 środowiska MySQL. |
| [Komunikacja równorzędna dwóch sieci wirtualnych](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md) | Tworzy i łączy dwie sieci wirtualne w tym samym regionie. |
| [Kierowanie ruchu przez wirtualne urządzenie sieciowe](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md) | Tworzy sieć wirtualną z podsieciami frontonu i zaplecza oraz maszyną wirtualną, która może kierować ruchem między dwiema podsieciami. |
| [Filtrowanie przychodzącego i wychodzącego ruchu sieciowego maszyny wirtualnej](./scripts/virtual-network-cli-sample-filter-network-traffic.md) | Tworzy sieć wirtualną z podsieciami frontonu i zaplecza. Ruch sieciowy przychodzący do podsieci frontonu jest ograniczony do protokołów HTTP, HTTPS i SSH. Ruch wychodzący do Internetu z podsieci zaplecza jest niedozwolony. |
|[Konfigurowanie sieci wirtualnej o podwójnym stosie IPv4 + IPv6 z podstawową Load Balancer](./scripts/virtual-network-cli-sample-ipv6-dual-stack.md)|Wdraża sieć wirtualną o podwójnej stercie (IPv4 + IPv6) z dwiema maszynami wirtualnymi i podstawową Load Balancer platformy Azure przy użyciu adresów IPv4 i IPv6. |
|[Konfigurowanie sieci wirtualnej o podwójnym stosie IPv4 + IPv6 przy użyciu usługa Load Balancer w warstwie Standardowa](./scripts/virtual-network-cli-sample-ipv6-dual-stack-standard-load-balancer.md)|Wdraża sieć wirtualną dual-stack (IPv4 + IPv6) z dwiema maszynami wirtualnymi i usługa Load Balancer w warstwie Standardowa platformy Azure z publicznymi adresami IP w protokołach IPv4 i IPv6. |
|[Samouczek: Tworzenie i testowanie bramy translatora adresów sieciowych — interfejs wiersza polecenia platformy Azure](../virtual-network/tutorial-create-validate-nat-gateway-cli.md)|Utwórz i sprawdź poprawność bramy NAT przy użyciu źródłowej i docelowej maszyny wirtualnej. |
