---
title: Metryki i alerty dla translatora adresów sieci wirtualnej platformy Azure
titleSuffix: Azure Virtual Network
description: Poznaj metryki i alerty usługi Azure Monitor dostępne dla translatora adresów sieci wirtualnych.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
Customer intent: As an IT administrator, I want to understand available Azure Monitor metrics and alerts for Virtual Network NAT.
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: 1c3a73cba6a2ece0c9c7459b3515f053a648d683
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81408608"
---
# <a name="azure-virtual-network-nat-metrics"></a>Metryki TRANSLATORA SIECI WIRTUALNEJ platformy Azure

Zasoby bramy translatora adresów sieci wirtualnej platformy Azure zapewniają metryki wielowymiarowe. Za pomocą tych wskaźników można obserwować operację i [rozwiązywać problemy](troubleshoot-nat.md).  Alerty można skonfigurować pod kątem krytycznych problemów, takich jak wyczerpanie SNAT.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" width="256" title="Virtual Network NAT dla wychodzących do Internetu">
</p>

*Rysunek: Virtual Network NAT dla wychodzących do Internetu*

## <a name="metrics"></a>Metryki

Zasoby bramy TRANSLATORa zapewniają następujące metryki wielowymiarowe w usłudze Azure Monitor:

| Metryka | Opis | Zalecana agregacja | Wymiary |
|---|---|---|---|
| Bajty | Bajty przetwarzane przychodzące i wychodzące | Suma | Kierunek (W; Out), Protokół (6 TCP; 17 UDP) |
| Pakiety | Pakiety przetworzone przychodzące i wychodzące | Suma | Kierunek (W; Out), Protokół (6 TCP; 17 UDP) |
| Porzucone pakiety | Pakiety porzucone przez bramę NAT | Suma | / |
| Liczba połączeń SNAT | Przejścia stanu na interwał | Suma | Stan połączenia, protokół (6 TCP; 17 UDP) |
| Całkowita liczba połączeń SNAT | Bieżące aktywne połączenia SNAT (~ porty SNAT w użyciu) | Suma | Protokół (6 TCP; 17 UDP) |


## <a name="alerts"></a>Alerty

Alerty dotyczące metryk można skonfigurować w usłudze Azure Monitor dla każdej z powyższych [metryk.](#metrics)

## <a name="limitations"></a>Ograniczenia

Kondycja zasobów nie jest obsługiwana.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [nat sieci wirtualnej](nat-overview.md)
* Dowiedz się więcej o [zasobie bramy NAT](nat-gateway-resource.md)
* Dowiedz się więcej o [usłudze Azure Monitor](../azure-monitor/overview.md)
* Dowiedz się więcej o [rozwiązywaniu problemów z zasobami bramy NAT](troubleshoot-nat.md).
* [Powiedz nam, co zbudować dalej dla Virtual Network NAT w UserVoice](https://aka.ms/natuservoice).


