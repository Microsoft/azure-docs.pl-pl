---
title: Metryki dla Azure NetApp Files | Microsoft Docs
description: Azure NetApp Files udostępnia metryki przydziału magazynu, rzeczywistego użycia magazynu, liczby operacji we/wy na sekundę oraz opóźnienia. Te metryki służą do zrozumienia użycia i wydajności.
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
ms.date: 10/07/2020
ms.author: b-juche
ms.openlocfilehash: 80746d7cef4bb0a70c6bb9eb6ff2e053ea6c7a0a
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91824683"
---
# <a name="metrics-for-azure-netapp-files"></a>Metryki dla usługi Azure NetApp Files

Azure NetApp Files udostępnia metryki przydziału magazynu, rzeczywistego użycia magazynu, liczby operacji we/wy na sekundę oraz opóźnienia. Analizując te metryki, można lepiej zrozumieć wzorzec użycia i wydajność woluminu dla kont NetApp.  

## <a name="usage-metrics-for-capacity-pools"></a><a name="capacity_pools"></a>Metryki użycia dla pul pojemności

- *Rozmiar przydzielony puli*   
    Rozmiar aprowizacji puli.

- *Pula przydzielono do rozmiaru woluminu*  
    Całkowita liczba przydziałów woluminu (GiB) w danej puli pojemności (czyli całkowita liczba przydziałów woluminów w puli pojemności).  
    Ten rozmiar to rozmiar wybrany podczas tworzenia woluminu.  

- *Rozmiar zużytego puli*  
    Całkowita ilość przestrzeni logicznej (GiB) używana między woluminami w puli pojemności.  

- *Łączny rozmiar migawki dla puli*    
    Suma rozmiaru migawki ze wszystkich woluminów w puli.

## <a name="usage-metrics-for-volumes"></a><a name="volumes"></a>Metryki użycia dla woluminów

<!-- ANF-5023: fixed version: 2020.08, 2020.09
- *Percentage Volume Consumed Size*    
    The percentage of the volume consumed, including snapshots.  
-->
- *Rozmiar przydzielony woluminu*   
    Rozmiar aprowizacji woluminu
- *Rozmiar przydziału woluminu*    
    Rozmiar przydziału (GiB), w którym jest inicjowany wolumin.   
- *Rozmiar zużytego woluminu*   
    Rozmiar logiczny woluminu (zużyte bajty).  
    Ten rozmiar obejmuje przestrzeni logicznej używanej przez aktywne systemy plików i migawki.  
- *Rozmiar migawki woluminu*   
   Rozmiar wszystkich migawek w woluminie.  

## <a name="performance-metrics-for-volumes"></a>Metryki wydajności dla woluminów

- *Średnie opóźnienie odczytu*   
    Średni czas odczytu z woluminu (w milisekundach).
- *Średnie opóźnienie zapisu*   
    Średni czas zapisu z woluminu w milisekundach.
- *Odczyt operacji we/wy*   
    Liczba odczytów do woluminu na sekundę.
- *Zapisz operacje we/wy*   
    Liczba zapisów w woluminie na sekundę.
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

- *Kondycja stanu replikacji woluminu*   
    Warunek relacji replikacji. Prawidłowy stan jest wskazywany przez `1` . Stan złej kondycji jest wskazywany przez `0` .

- *Czy transfer replikacji woluminu*    
    Czy stan replikacji woluminu to "Transfer". 
 
- *Czas zwłoki replikacji woluminu*   
    Czas (w sekundach), przez który dane na duplikacie spowolnienia za źródłem. 

- *Czas trwania ostatniego transferu replikacji woluminu*   
    Czas (w sekundach) oczekiwania na ukończenie ostatniego transferu. 

- *Rozmiar ostatniego transferu replikacji woluminu*    
    Całkowita liczba bajtów przetransferowanych w ramach ostatniego transferu. 

- *Postęp replikacji woluminu*    
    Całkowita ilość danych transferowanych dla bieżącej operacji transferu. 

- *Łączny transfer replikacji woluminów*   
    Skumulowane bajty transferowane dla relacji. 

## <a name="next-steps"></a>Następne kroki

* [Omówienie hierarchii magazynu usługi Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)
* [Tworzenie woluminu dla usługi Azure NetApp Files](azure-netapp-files-create-volumes.md)
