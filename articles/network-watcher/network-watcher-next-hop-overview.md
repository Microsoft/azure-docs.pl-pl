---
title: Wprowadzenie do następnego skoku na platformie Azure Network Watcher | Microsoft Docs
description: Ten artykuł zawiera Network Watcher omówienie możliwości następnego przeskoku.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: febf7bca-e0b7-41d5-838f-a5a40ebc5aac
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 33c22b5b68b6677e8cf271dc185007316ec44500
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "76844062"
---
# <a name="use-next-hop-to-diagnose-virtual-machine-routing-problems"></a>Użyj następnego skoku do diagnozowania problemów z routingiem maszyn wirtualnych

Ruch z maszyny wirtualnej (VM) jest wysyłany do miejsca docelowego na podstawie efektywnych tras skojarzonych z interfejsem sieciowym (NIC). Następny przeskok Pobiera typ następnego przeskoku i adres IP pakietu z określonej maszyny wirtualnej i karty sieciowej. Znajomość następnego przeskoku pomaga określić, czy ruch jest kierowany do zamierzonego miejsca docelowego, czy też czy ruch jest wysyłany. Niewłaściwa konfiguracja tras, w której ruch jest kierowany do lokalizacji lokalnej lub urządzenia wirtualnego, może prowadzić do problemów z łącznością. Następny przeskok zwraca również tabelę tras skojarzoną z następnym przeskokiem. Jeśli trasa jest zdefiniowana jako trasa zdefiniowana przez użytkownika, ta trasa jest zwracana. W przeciwnym razie następny przeskok zwraca **trasę systemową**.

![Omówienie następnego przeskoku](./media/network-watcher-next-hop-overview/figure1.png)

Następne przeskoki, które mogą być zwracane przez funkcję następnego przeskoku są następujące:

* Internet
* VirtualAppliance
* VirtualNetworkGateway
* VirtualNetwork
* VirtualNetworkPeering
* VirtualNetworkServiceEndpoint 
* MicrosoftEdge
* Brak

Aby dowiedzieć się więcej na temat każdego typu następnego przeskoku, zobacz [Omówienie routingu](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się, jak używać następnego skoku do diagnozowania problemów z routingiem sieci maszyn wirtualnych, zobacz Diagnozowanie problemów z routingiem sieci maszyn wirtualnych przy użyciu [Azure Portal](diagnose-vm-network-routing-problem.md), [programu PowerShell](diagnose-vm-network-routing-problem-powershell.md)lub [interfejsu wiersza polecenia platformy Azure](diagnose-vm-network-routing-problem-cli.md).
