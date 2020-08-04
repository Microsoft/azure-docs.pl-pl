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
ms.openlocfilehash: 639f1e09fdb5603965209e5b5ee6c224ad238b76
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87533125"
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

- [Strona cennika Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Model kosztów usługi Azure NetApp Files](azure-netapp-files-cost-model.md) 
- [Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)
- [Umowa dotycząca poziomu usług (SLA) dla Azure NetApp Files](https://azure.microsoft.com/support/legal/sla/netapp/)
- [Dynamiczna zmiana poziomu usługi woluminu](dynamic-change-volume-service-level.md) 