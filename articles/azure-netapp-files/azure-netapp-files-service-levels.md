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
ms.date: 10/12/2020
ms.author: b-juche
ms.openlocfilehash: 7eac6a40476cffe875a03de49c9c9311ffbf4d39
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017070"
---
# <a name="service-levels-for-azure-netapp-files"></a>Poziomy usług dla usługi Azure NetApp Files
Poziomy usług są atrybutem puli pojemności. Poziomy usług są zdefiniowane i odróżniane przez maksymalną przepustowość dla woluminu w puli pojemności na podstawie przydziału przypisanego do woluminu.

## <a name="supported-service-levels"></a>Obsługiwane poziomy usług

Azure NetApp Files obsługuje trzy poziomy usługi: *Ultra*, *Premium* i *Standard*. 

* <a name="Ultra"></a>Ultra Storage

    Warstwa Ultra Storage zapewnia do 128 MiB/s przepływność na 1 TiB pojemności. 

* <a name="Premium"></a>Premium Storage

    Warstwa magazynu Premium Storage zapewnia do 64 MiB/s przepływność na 1 TiB pojemności. 

* <a name="Standard"></a>Magazyn w warstwie Standardowa

    Standardowa warstwa magazynowania oferuje do 16 MiB/s przepływność na 1 TiB pojemności.

## <a name="throughput-limits"></a>Limity przepływności

Limit przepływności dla woluminu jest określany przez połączenie następujących czynników:
* Poziom usługi puli pojemności, do której należy wolumin
* Przydział przypisany do woluminu  
* Typ *QoS (* autolub *ręczny*) puli pojemności  

### <a name="throughput-limit-examples-of-volumes-in-an-auto-qos-capacity-pool"></a>Przykładowe limity przepływności woluminów w puli pojemności usługi autoqos

Na poniższym diagramie przedstawiono przykładowe limity przepływności woluminów w puli wydajności autoqos:

![Ilustracja poziomu usługi](../media/azure-netapp-files/azure-netapp-files-service-levels.png)

* W przykładzie 1 wolumin z puli wydajności z funkcją autoqos z warstwą usługi Premium Storage przypisaną 2 TiB przydziału zostanie przypisany limitem przepływności wynoszącym 128 MiB/s (2 TiB * 64 MiB/s). Ten scenariusz dotyczy niezależnie od rozmiaru puli pojemności lub rzeczywistego zużycia woluminu.

* W przykładzie 2 wolumin z puli usługi autoqos o pojemności przypisanej do 100 GiB przydziału zostanie przypisany limit przepływności równy 6,25 MiB/s (0,09765625 TiB * 64 MiB/s). Ten scenariusz dotyczy niezależnie od rozmiaru puli pojemności lub rzeczywistego zużycia woluminu.

### <a name="throughput-limit-examples-of-volumes-in-a-manual-qos-capacity-pool"></a>Przykładowe limity przepływności woluminów w ręcznej puli pojemności usługi QoS 

W przypadku korzystania z ręcznej puli pojemności usługi QoS można niezależnie przypisać pojemność i przepływność woluminu. Podczas tworzenia woluminu w ręcznej puli pojemności usługi QoS można określić wartość przepływności (MiB/S). Całkowita przepływność przypisana do woluminów w ręcznej puli pojemności usługi QoS zależy od rozmiaru puli i poziomu usługi. Jest to ograniczone przez (rozmiar puli pojemności w TiB x przepływności poziomu usługi/TiB). Na przykład Pula pojemności 10 TiB z poziomem usługi Ultra Service ma łączną wydajność przepływności wynoszącą 1280 MiB/s (10 TiB x 128 MiB/s/TiB) dostępną dla woluminów.

Na przykład w przypadku systemu SAP HANA można użyć tej puli pojemności do utworzenia następujących woluminów. Każdy wolumin zapewnia indywidualny rozmiar i przepływność w celu spełnienia wymagań aplikacji:

* Wolumin danych SAP HANA: rozmiar 4 TiB z maksymalnie 704 MiB/s
* SAP HANA wolumin dziennika: rozmiar 0,5 TiB z maksymalnie 256 MiB/s
* SAP HANA udostępniony wolumin: rozmiar 1 TiB z maksymalnie 64 MiB/s
* Wolumin kopii zapasowej SAP HANA: rozmiar 4,5 TiB z maksymalnie 256 MiB/s

Na poniższym diagramie przedstawiono scenariusze dotyczące woluminów SAP HANA:

![Scenariusze dotyczące woluminów SAP HANA QoS](../media/azure-netapp-files/qos-sap-hana-volume-scenarios.png) 

## <a name="next-steps"></a>Następne kroki

- [Strona cennika Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Model kosztów usługi Azure NetApp Files](azure-netapp-files-cost-model.md) 
- [Tworzenie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)
- [Umowa dotycząca poziomu usług (SLA) dla Azure NetApp Files](https://azure.microsoft.com/support/legal/sla/netapp/)
- [Dynamiczna zmiana poziomu usługi woluminu](dynamic-change-volume-service-level.md) 
- [Cele poziomu usługi dotyczące replikacji między regionami](cross-region-replication-introduction.md#service-level-objectives)
