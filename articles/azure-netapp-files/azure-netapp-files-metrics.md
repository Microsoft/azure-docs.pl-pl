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
ms.date: 09/10/2020
ms.author: b-juche
ms.openlocfilehash: 1690a844ff700a2975be8e972fd90ba71eeb937c
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/16/2020
ms.locfileid: "90707785"
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

- *Łączny rozmiar migawki puli*    
    Suma rozmiaru migawki wszystkich woluminów w puli.

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

- *Średnie opóźnienie odczytu*   
    Średni czas odczytu z woluminu (w milisekundach).
- *Średnie opóźnienie zapisu*   
    Średni czas zapisu z woluminu w milisekundach.
- *Odczyt operacji we/wy*   
    Liczba odczytów do woluminu na sekundę.
- *Zapisz operacje we/wy*   
    Liczba zapisów w woluminie na sekundę.

## <a name="volume-replication-metrics"></a><a name="replication"></a>Metryki replikacji woluminów

- *Kondycja stanu replikacji woluminu*   
    Warunek relacji replikacji. 

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
