---
title: Metryki i alerty dotyczące usługi Azure Virtual Network translator adresów sieciowych
titleSuffix: Azure Virtual Network
description: Informacje dotyczące Azure Monitor metryk i alertów dostępnych dla Virtual Network translatora adresów sieciowych.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
Customer intent: As an IT administrator, I want to understand available Azure Monitor metrics and alerts for Virtual Network NAT.
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: e3c47a60a6cda074eba7b5c3292577c29f50c2ab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87424055"
---
# <a name="azure-virtual-network-nat-metrics"></a>Metryki translatora adresów sieciowych na platformie Azure Virtual Network

Zasoby bramy translatora adresów sieciowych w usłudze Azure Virtual Network zapewniają wielowymiarowe metryki. Te metryki służą do obserwowania operacji i [rozwiązywania problemów](troubleshoot-nat.md).  Alerty można skonfigurować pod kątem krytycznych problemów, takich jak wyczerpanie adresów.

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" alt="Figure depicts a NAT gateway resource that consumes all IP addresses for a public IP prefix and directs that traffic to and from two subnets of virtual machines and a virtual machine scale set." width="256" title="Virtual Network translator adresów sieciowych dla ruchu wychodzącego do Internetu">
</p>

*Rysunek: Virtual Network translator adresów sieciowych dla ruchu wychodzącego do Internetu*

## <a name="metrics"></a>Metryki

Zasoby bramy NAT zapewniają następujące wielowymiarowe metryki w Azure Monitor:

| Metric | Opis | Zalecana agregacja | Wymiary |
|---|---|---|---|
| Bajty | Przetworzone bajty przychodzące i wychodzące | Sum | Kierunek (w; Out), protokół (6 TCP; 17 UDP) |
| SPI | Pakiety przetworzone do ruchu przychodzącego i wychodzącego | Sum | Kierunek (w; Out), protokół (6 TCP; 17 UDP) |
| Pakiety opuszczone | Pakiety odrzucone przez bramę translatora adresów sieciowych | Sum | / |
| Liczba połączeń z translatorem adresów sieciowych | Przejścia stanu na interwał | Sum | Stan połączenia, protokół (6 TCP; 17 połączeń UDP) |
| Łączna liczba połączeń z wiązaniami adresów sieciowych | Bieżące aktywne połączenia z przydziałami (~ porty w użyciu) | Sum | Protokół (6 TCP; 17 UDP) |


## <a name="alerts"></a>Alerty

Alerty dotyczące metryk można skonfigurować w Azure Monitor dla każdej z powyższych [metryk](#metrics).

## <a name="limitations"></a>Ograniczenia

Resource Health nie jest obsługiwana.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [Virtual Network translatora adresów sieciowych](nat-overview.md)
* Informacje o [zasobie bramy translatora adresów sieciowych](nat-gateway-resource.md)
* Dowiedz się więcej o [Azure monitor](../azure-monitor/overview.md)
* Informacje o [rozwiązywaniu problemów z zasobami bramy translatora adresów sieciowych](troubleshoot-nat.md).
* [Powiedz nam, co należy utworzyć obok Virtual Network translatora adresów sieciowych w usłudze UserVoice](https://aka.ms/natuservoice).


