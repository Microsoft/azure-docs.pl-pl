---
title: Hierarchia magazynu Azure NetApp Files | Microsoft Docs
description: W tym artykule opisano hierarchię magazynu usługi Azure NetApp Files, obejmującą konta, pule pojemności i woluminy.
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
ms.topic: overview
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 435d74e771a9d887c87c9d10e6b525ac77cf97e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91278332"
---
# <a name="storage-hierarchy-of-azure-netapp-files"></a>Hierarchia magazynu usługi Azure NetApp Files

Przed utworzeniem woluminu w usłudze Azure NetApp Files należy kupić i skonfigurować pulę aprowizowanej pojemności.  Do skonfigurowania puli pojemności potrzebne jest konto usługi NetApp. Zrozumienie hierarchii magazynu pomaga w konfigurowaniu zasobów usługi Azure NetApp Files i zarządzaniu nimi.

> [!IMPORTANT] 
> Azure NetApp Files obecnie nie obsługuje migracji zasobów między subskrypcjami.

## <a name="netapp-accounts"></a><a name="azure_netapp_files_account"></a>Konta usługi NetApp

- Konto usługi NetApp grupuje składowe pule pojemności do celów administracyjnych.  
- Konto usługi NetApp różni się od ogólnego konta usługi Azure Storage. 
- Konto usługi NetApp ma zasięg regionalny.   
- W jednym regionie można mieć wiele kont usługi NetApp, ale każde konto usługi NetApp jest powiązane tylko z jednym regionem.

## <a name="capacity-pools"></a><a name="capacity_pools"></a>Pule pojemności

Zrozumienie, jak działają pule pojemności, ułatwia wybór odpowiednich typów puli pojemności dla potrzeb magazynu. 

### <a name="general-rules-of-capacity-pools"></a>Ogólne reguły pul pojemności

- Pule pojemności są mierzone według aprowizowanej pojemności.   
    Aby uzyskać dodatkowe informacje, zobacz [typy QoS](#qos_types) .  
- Pojemność jest aprowizowana zgodnie z liczbą zakupionych stałych jednostek SKU (zawierających na przykład 4 TiB pojemności).
- Pula pojemności może mieć tylko jeden poziom usługi.  
- Każda pula pojemności może należeć tylko do jednego konta usługi NetApp. Można jednak mieć wiele pul pojemności w ramach konta usługi NetApp.  
- Nie można przenosić pul pojemności między kontami usługi NetApp.   
  Na przykład na poniższym [diagramie koncepcyjnym hierarchii magazynu](#conceptual_diagram_of_storage_hierarchy) nie można przenieść puli pojemności 1 z konta usługi NetApp w regionie Wschodnie stany USA na konto usługi NetApp w regionie Zachodnie stany USA 2.  
- Puli pojemności nie można usunąć, dopóki nie zostaną usunięte wszystkie woluminy w puli pojemności.

### <a name="quality-of-service-qos-types-for-capacity-pools"></a><a name="qos_types"></a>Typy Quality of Service (QoS) dla pul pojemności

Typ QoS jest atrybutem puli pojemności. Azure NetApp Files udostępnia dwa typy usług QoS dla pul pojemności. 

- *Automatyczne (lub automatycznie)* Typ QoS  

    Podczas tworzenia puli pojemności domyślny typ QoS to Autokonfiguracja.

    W puli pojemności automatycznej usługi QoS przepływność jest przypisywana automatycznie do woluminów w puli proporcjonalnie do przydziału rozmiaru przypisanego do woluminów. 

    Maksymalna przepływność przypisana do woluminu zależy od poziomu usługi puli pojemności i przydziału rozmiaru woluminu. Zobacz [poziomy usług dla Azure NetApp Files](azure-netapp-files-service-levels.md) na przykład obliczanie.

- <a name="manual_qos_type"></a>*Ręczne* Typ QoS  

     > [!IMPORTANT] 
     > Korzystanie z ręcznego typu QoS dla puli pojemności wymaga rejestracji.  Zobacz [Zarządzanie ręczną pulą pojemności usługi QoS](manage-manual-qos-capacity-pool.md).  

    Istnieje możliwość użycia ręcznego typu QoS dla puli pojemności.

    W ręcznej puli pojemności usługi QoS można przypisać pojemność i przepływność dla woluminu niezależnie. Całkowita przepływność wszystkich woluminów utworzonych z ręczną pulą pojemności usługi QoS jest ograniczona przez łączną przepływność puli.  Jest ona określana na podstawie kombinacji rozmiaru puli i przepływności na poziomie usług. 

    Na przykład Pula pojemności 4-TiB z poziomem usługi Ultra Service ma całkowitą wydajność przepływności wynoszącą 512 MiB/s (4 TiB x 128 MiB/s/TiB) dostępną dla woluminów.


## <a name="volumes"></a><a name="volumes"></a>Woluminy

- Woluminy są mierzone według użycia pojemności logicznej i można je skalować. 
- Użycie pojemności woluminu jest liczone jako użycie aprowizowanej pojemności puli.
- Użycie przepływności woluminu jest traktowane względem dostępnej przepływności puli. Zobacz [ręczny typ QoS](#manual_qos_type).
- Każdy wolumin należy tylko do jednej puli, ale jedna pula może zawierać wiele woluminów. 

## <a name="conceptual-diagram-of-storage-hierarchy"></a><a name="conceptual_diagram_of_storage_hierarchy"></a>Diagram koncepcyjny hierarchii magazynu 
Poniższy przykład przedstawia zależności między subskrypcją platformy Azure, kontami usługi NetApp, pulami pojemności i woluminami.   

![Diagram koncepcyjny hierarchii magazynu](../media/azure-netapp-files/azure-netapp-files-storage-hierarchy.png)

## <a name="next-steps"></a>Następne kroki

- [Limity zasobów dla usługi Azure NetApp Files](azure-netapp-files-resource-limits.md)
- [Rejestrowanie w usłudze Azure NetApp Files](azure-netapp-files-register.md)
- [Poziomy usług dla usługi Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Kwestie dotyczące wydajności dla usługi Azure NetApp Files](azure-netapp-files-performance-considerations.md)
- [Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)
- [Zarządzanie ręczną pulą pojemności usługi QoS](manage-manual-qos-capacity-pool.md)
