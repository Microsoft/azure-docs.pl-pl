---
title: Metryki dla Azure NetApp Files | Microsoft Docs
description: Opisuje metryki dla Azure NetApp Files.
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
ms.date: 03/17/2020
ms.author: b-juche
ms.openlocfilehash: c8e3b616dee1ab4e6bb6e77c6a8bab5661d4e20b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "79460436"
---
# <a name="metrics-for-azure-netapp-files"></a>Metryki dla usługi Azure NetApp Files

Azure NetApp Files udostępnia metryki przydziału magazynu, rzeczywistego użycia magazynu, liczby operacji we/wy na sekundę oraz opóźnienia. Analizując te metryki, można lepiej zrozumieć wzorzec użycia i wydajność woluminu dla kont NetApp.  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>Metryki użycia dla pul pojemności

<!-- 
- *Pool Provisioned Size*  
    The logical space (GiB) the capacity pool is provisioned with.  
    This size is the size you selected during capacity pool creation. 
--> 
- *Pula przydzielono do rozmiaru woluminu*  
    Całkowita liczba przydziałów woluminu (GiB) w danej puli pojemności (czyli całkowita liczba przydziałów woluminów w puli pojemności).  
    Ten rozmiar to rozmiar wybrany podczas tworzenia woluminu.  
- *Rozmiar zużytego puli*  
    Całkowita ilość przestrzeni logicznej (GiB) używana między woluminami w puli pojemności.  
<!-- 
- *Pool Consumed Snapshot Size*  
    The total of logical space (GiB) used by snapshots across all volumes in a capacity pool. 
-->

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>Metryki użycia dla woluminów

<!--
- *Volume Quota Size*    
    The quota size (GiB) the volume is provisioned with.   
    This size is the size you selected during capacity pool creation. 
-->
- *Rozmiar zużytego woluminu*   
    Łączna ilość przestrzeni logicznej używanej w woluminie (GiB).  
    Ten rozmiar obejmuje przestrzeni logicznej używanej przez aktywne systemy plików i migawki.  
- *Rozmiar migawki woluminu*   
   Przyrostowe miejsce logiczne używane przez migawki w woluminie.  

## <a name="performance-metrics-for-volumes"></a>Metryki wydajności dla woluminów

- *AverageReadLatency*   
    Średni czas odczytu z woluminu (w milisekundach).
- *AverageWriteLatency*   
    Średni czas zapisu z woluminu w milisekundach.
- *ReadIops*   
    Liczba odczytów do woluminu na sekundę.
- *WriteIops*   
    Liczba zapisów w woluminie na sekundę.

## <a name="next-steps"></a>Następne kroki

* [Omówienie hierarchii magazynu usługi Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)
* [Tworzenie woluminu dla usługi Azure NetApp Files](azure-netapp-files-create-volumes.md)
