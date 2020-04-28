---
title: Poziomy usługi dla Azure NetApp Files | Microsoft Docs
description: Opisuje wydajność przepływności dla poziomów usługi Azure NetApp Files.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: b-juche
ms.openlocfilehash: 0398cc6a5336141f51dde26ed7cf4cce8c2c0bb4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75832579"
---
# <a name="service-levels-for-azure-netapp-files"></a>Poziomy usług dla usługi Azure NetApp Files
Poziomy usług są atrybutem puli pojemności. Poziomy usług są zdefiniowane i odróżniane przez maksymalną przepustowość dla woluminu w puli pojemności na podstawie przydziału przypisanego do woluminu.

## <a name="supported-service-levels"></a>Obsługiwane poziomy usług

Azure NetApp Files obsługuje trzy poziomy usługi: *Ultra*, *Premium*i *Standard*. 

* <a name="Ultra"></a>Ultra Storage

    Warstwa Ultra Storage zapewnia do 128 MiB/s przepływność na 1 TiB przydzielonego przydziału woluminu. 

* <a name="Premium"></a>Premium Storage

    Warstwa magazynu Premium Storage zapewnia do 64 MiB/s przepływność na 1 TiB przydzielonego przydziału woluminu. 

* <a name="Standard"></a>Magazyn w warstwie Standardowa

    Standardowa warstwa magazynowania oferuje do 16 MiB/s przepływność na 1 TiB przydzielonego przydziału woluminu.

## <a name="throughput-limits"></a>Limity przepływności

Limit przepływności dla woluminu jest określany przez połączenie następujących czynników:
* Poziom usługi puli pojemności, do której należy wolumin
* Przydział przypisany do woluminu  

Koncepcje te przedstawiono na poniższym diagramie:

![Ilustracja poziomu usługi](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

W powyższym przykładzie 1 wolumin z puli pojemności w warstwie Premium Storage, do której przypisano 2 TiB przydziału, zostanie przypisany limit przepływności równy 128 MiB/s (2 TiB * 64 MiB/s). Ten scenariusz dotyczy niezależnie od rozmiaru puli pojemności lub rzeczywistego zużycia woluminu.

W przykładzie 2 powyżej wolumin z puli pojemności w warstwie magazynu Premium przypisany 100 GiB przydziału zostanie przypisany limit przepływności równy 6,25 MiB/s (0,09765625 TiB * 64 MiB/s). Ten scenariusz dotyczy niezależnie od rozmiaru puli pojemności lub rzeczywistego zużycia woluminu.

## <a name="next-steps"></a>Następne kroki

- Informacje o różnych poziomach usługi można uzyskać na [stronie z cennikiem dla usługi Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
- Aby uzyskać Azure NetApp Files obliczania zużycia pojemności w puli pojemności, zobacz [model kosztów](azure-netapp-files-cost-model.md) . 
- [Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)
- Zobacz [Umowa dotycząca poziomu usług (SLA) dla Azure NetApp Files](https://azure.microsoft.com/support/legal/sla/netapp/)