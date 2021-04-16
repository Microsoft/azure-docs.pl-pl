---
title: Metryki dla Azure NetApp Files | Microsoft Docs
description: Azure NetApp Files metryki dotyczące przydzielonego magazynu, rzeczywistego użycia magazynu, liczby operacji we/wy na woluminie i opóźnienia. Użyj tych metryk, aby zrozumieć użycie i wydajność.
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
ms.date: 04/12/2021
ms.author: b-juche
ms.openlocfilehash: b581470a886ff73739edfee7f45c64295eeeb1f0
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388616"
---
# <a name="metrics-for-azure-netapp-files"></a>Metryki dla usługi Azure NetApp Files

Azure NetApp Files metryki dotyczące przydzielonego magazynu, rzeczywistego użycia magazynu, liczby operacji we/wy na woluminie i opóźnienia. Analizując te metryki, można lepiej zrozumieć wzorzec użycia i wydajność woluminu kont netApp.  

Metryki dla puli pojemności lub woluminu można znaleźć, wybierając **pulę pojemności lub** **wolumin**.  Następnie kliknij **pozycję Metryka,** aby wyświetlić dostępne metryki: 

[![Migawka, która pokazuje, jak przejść do menu rozwijanego Metryka. ](../media/azure-netapp-files/metrics-navigate-volume.png)](../media/azure-netapp-files/metrics-navigate-volume.png#lightbox)

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>Metryki użycia dla pul pojemności

- *Rozmiar przydzielonej puli*   
    Aprowizowany rozmiar puli.

- *Pula przydzielona do rozmiaru woluminu*  
    Łączna liczba przydziałów woluminów (GiB) w danej puli pojemności (czyli łączna liczba aprowizowanych rozmiarów woluminów w puli pojemności).  
    Jest to rozmiar wybrany podczas tworzenia woluminu.  

- *Rozmiar zużytej puli*  
    Całkowita ilość miejsca logicznego (GiB) używanego na woluminach w puli pojemności.  

- *Łączny rozmiar migawki dla puli*    
    Suma rozmiaru migawki ze wszystkich woluminów w puli.

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>Metryki użycia dla woluminów

- *Rozmiar procentowego zużytego woluminu*    
    Procent zużytego woluminu, w tym migawek.  
- *Rozmiar przydzielonego woluminu*   
    Aprowizowany rozmiar woluminu
- *Rozmiar limitu przydziału woluminu*    
    Rozmiar limitu przydziału (GiB), za pomocą których jest aprowowany wolumin.   
- *Rozmiar zużytego woluminu*   
    Rozmiar logiczny woluminu (używane bajty).  
    Ten rozmiar obejmuje miejsce logiczne używane przez aktywne systemy plików i migawki.  
- *Rozmiar migawki woluminu*   
   Rozmiar wszystkich migawek w woluminie.  

## <a name="performance-metrics-for-volumes"></a>Metryki wydajności dla woluminów

- *Średnie opóźnienie odczytu*   
    Średni czas odczytu z woluminu w milisekundach.
- *Średnie opóźnienie zapisu*   
    Średni czas zapisu z woluminu w milisekundach.
- *Odczyt IOPS*   
    Liczba odczytów woluminu na sekundę.
- *Write IOPS*   
    Liczba zapisu w woluminie na sekundę.
<!-- These two metrics are not yet available, until ~ 2020.09
- *Read MiB/s*   
    Read throughput in bytes per second.
- *Write MiB/s*   
    Write throughput in bytes per second.
--> 
<!-- ANF-4128; 2020.07
- *Pool Provisioned Throughput*   
    The total throughput a capacity pool can provide to its volumes based on "Pool Provisioned Size" and "Service Level".
- *Pool Allocated to Volume Throughput*   
    The total throughput allocated to volumes in a given capacity pool (that is, the total of the volumes' allocated throughput in the capacity pool).
-->

<!-- ANF-6443; 2020.11
- *Pool Consumed Throughput*    
    The total throughput being consumed by volumes in a given capacity pool.
-->


## <a name="volume-replication-metrics"></a><a name="replication"></a>Metryki replikacji woluminów

> [!NOTE] 
> * Rozmiar transferu sieciowego (na przykład metryki całkowitego transferu replikacji woluminów) może różnić się od woluminów źródłowych lub docelowych replikacji między regionami.  Jest to wynikiem efektywnego wykorzystania aparatu replikacji w celu zminimalizowania kosztów transferu sieciowego.
> * Metryki replikacji woluminów są obecnie wypełniane dla woluminów docelowych replikacji, a nie źródła relacji replikacji.

- *Czy stan replikacji woluminu jest w dobrej kondycji*   
    Warunek relacji replikacji. Stan dobrej kondycji jest oznaczany przez `1` . Stan złej kondycji jest oznaczony przez `0` .

- *Czy transfer replikacji woluminu*    
    Określa, czy stan replikacji woluminu to "transfer". 
 
- *Czas opóźnienia replikacji woluminu*   
    Czas w sekundach, o który dane dublowane są opóźniane w tyłu źródła. 

- *Czas trwania ostatniego transferu replikacji woluminu*   
    Czas (w sekundach) ostatniego transferu. 

- *Rozmiar ostatniego transferu replikacji woluminu*    
    Całkowita liczba bajtów przetransferowanych w ramach ostatniego transferu. 

- *Postęp replikacji woluminów*    
    Łączna ilość danych przesyłanych dla bieżącej operacji transferu. 

- *Łączny transfer replikacji woluminów*   
    Skumulowane bajty przekazane dla relacji. 

## <a name="next-steps"></a>Następne kroki

* [Omówienie hierarchii magazynu usługi Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)
* [Tworzenie woluminu dla usługi Azure NetApp Files](azure-netapp-files-create-volumes.md)
