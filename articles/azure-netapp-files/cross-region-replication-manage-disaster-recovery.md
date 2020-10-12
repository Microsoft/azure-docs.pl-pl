---
title: Zarządzanie odzyskiwaniem po awarii przy użyciu Azure NetApp Files replikacji między regionami | Microsoft Docs
description: Opisuje sposób zarządzania odzyskiwaniem po awarii przy użyciu Azure NetApp Files replikacji między regionami.
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
ms.topic: how-to
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: ad006279a656758ba856cd3f39c17b0410e525e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708791"
---
# <a name="manage-disaster-recovery-using-cross-region-replication"></a>Zarządzanie odzyskiwaniem po awarii przy użyciu replikacji między regionami 

Ciągła replikacja między woluminami źródłowymi i docelowymi (zobacz [Tworzenie komunikacji równorzędnej replikacji](cross-region-replication-create-peering.md)) przygotowuje Cię do zdarzenia odzyskiwania po awarii. 

W przypadku wystąpienia takiego zdarzenia można [przełączyć się do trybu failover na woluminie docelowym](#break-replication-peering-to-activate-the-destination-volume), umożliwiając klientowi odczytywanie i zapisywanie na woluminie docelowym. 

Po odzyskiwaniu po awarii można wrócić do woluminu źródłowego z [operacją ponownej synchronizacji](#resync-replication-to-reactivate-the-source-volume) , która zastępuje dane woluminu źródłowego przy użyciu danych woluminu docelowego.  Następnie należy ponownie [nawiązać replikację źródłową i docelową](#reestablish-source-to-destination-replication) , a następnie ponownie zainstalować wolumin źródłowy, do którego ma dostęp klient. 

Szczegóły są opisane poniżej. 

## <a name="break-replication-peering-to-activate-the-destination-volume"></a>Przerwij replikację komunikacji równorzędnej, aby aktywować wolumin docelowy

Gdy konieczne jest aktywowanie woluminu docelowego (na przykład w przypadku przejścia w tryb failover do regionu docelowego), należy przerwać komunikację równorzędną replikacji, a następnie zainstalować wolumin docelowy.  

1. Aby przerwać replikację komunikacji równorzędnej, wybierz wolumin docelowy. Kliknij pozycję **replikacja** w obszarze usługa magazynu.  

2.  Przed kontynuowaniem sprawdź następujące pola:  
    * Upewnij się, że stan dublowania pokazuje ***dublowanie***.   
        Nie należy próbować przerwać replikacji komunikacji równorzędnej, jeśli stan dublowany pokazuje *niezainicjowany*.
    * Upewnij się, że stan relacji jest wyświetlany jako ***bezczynny***.   
        Nie należy próbować przerwać replikacji komunikacji równorzędnej, jeśli stan relacji pokazuje *transfer*.   

    Zobacz [Wyświetlanie stanu kondycji relacji replikacji](cross-region-replication-display-health-status.md). 

3.  Kliknij pozycję **Przerwij komunikację równorzędną**.  

4.  Wpisz **tak** po wyświetleniu monitu, a następnie kliknij przycisk **Przerwij** . 

    ![Przerwij replikację komunikacji równorzędnej](../media/azure-netapp-files/cross-region-replication-break-replication-peering.png)

5.  Zainstaluj wolumin docelowy, wykonując kroki opisane w sekcji [Instalowanie lub odinstalowywanie woluminu dla maszyn wirtualnych z systemem Windows lub Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).   
    Ten krok umożliwia klientowi dostęp do woluminu docelowego.

## <a name="resync-replication-to-reactivate-the-source-volume"></a>Wykonaj ponowną synchronizację replikacji, aby ponownie aktywować wolumin źródłowy   

Po odzyskiwaniu po awarii można ponownie aktywować wolumin źródłowy, wykonując operację ponownej synchronizacji.  Operacja ponownej synchronizacji odwraca proces replikacji i synchronizuje dane z woluminu docelowego do woluminu źródłowego.  

> [!IMPORTANT] 
> Operacja ponownej synchronizacji zastępuje dane woluminu źródłowego danymi woluminu docelowego.  Interfejs użytkownika ostrzega o potencjalnym utracie danych. Przed rozpoczęciem operacji zostanie wyświetlony monit o potwierdzenie akcji ponownej synchronizacji.

1. Aby ponownie zsynchronizować replikację, wybierz wolumin *źródłowy* . Kliknij pozycję **replikacja** w obszarze usługa magazynu. Następnie kliknij pozycję **ponowna synchronizacja**.  

2. Wpisz **tak** po wyświetleniu monitu, a następnie kliknij przycisk ponownie **Synchronizuj** . 
 
    ![Synchronizuj ponownie replikację](../media/azure-netapp-files/cross-region-replication-resync-replication.png)

3. Monitoruj stan kondycji woluminu źródłowego, wykonując czynności opisane w temacie [Wyświetlanie stanu kondycji relacji replikacji](cross-region-replication-display-health-status.md).   
    Gdy stan kondycji woluminu źródłowego zawiera następujące wartości, operacja ponownej synchronizacji zostanie zakończona, a zmiany wprowadzone w woluminie docelowym są teraz przechwytywane na woluminie źródłowym:   

    * Stan dublowany: *dublowany*  
    * Stan transferu: *bezczynne*  

## <a name="reestablish-source-to-destination-replication"></a>Ponowne ustanawianie replikacji źródła do lokalizacji docelowej

Po zakończeniu operacji ponownej synchronizacji z lokalizacji docelowej do źródła należy ponownie przerwać komunikację równorzędną replikacji, aby przywrócić replikację źródła do lokalizacji docelowej. Należy również ponownie zainstalować wolumin źródłowy, aby klient mógł uzyskać do niego dostęp.  

1. Przerwij komunikację równorzędną replikacji:  
    a. Wybierz wolumin *docelowy* . Kliknij pozycję **replikacja** w obszarze usługa magazynu.  
    b. Przed kontynuowaniem sprawdź następujące pola:   
    * Upewnij się, że stan dublowania pokazuje ***dublowanie***.   
    Nie należy próbować przerwać replikacji komunikacji równorzędnej, jeśli stan dublowany pokazuje *niezainicjowany*.  
    * Upewnij się, że stan relacji jest wyświetlany jako ***bezczynny***.   
    Nie należy próbować przerwać replikacji komunikacji równorzędnej, jeśli stan relacji pokazuje *transfer*.    

        Zobacz [Wyświetlanie stanu kondycji relacji replikacji](cross-region-replication-display-health-status.md). 

    c. Kliknij pozycję **Przerwij komunikację równorzędną**.   
    d. Wpisz **tak** po wyświetleniu monitu, a następnie kliknij przycisk **Przerwij** .  

2. Ponownie zsynchronizuj wolumin źródłowy z woluminem docelowym:  
    a. Wybierz wolumin *docelowy* . Kliknij pozycję **replikacja** w obszarze usługa magazynu. Następnie kliknij pozycję **ponowna synchronizacja**.   
    b. Wpisz **tak** po wyświetleniu monitu, a następnie kliknij przycisk ponownie **Synchronizuj** .

3. Zainstaluj ponownie wolumin źródłowy, wykonując kroki opisane w sekcji [Instalowanie lub odinstalowywanie woluminu dla maszyn wirtualnych z systemem Windows lub Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).  
    Ten krok umożliwia klientowi dostęp do woluminu źródłowego.

## <a name="next-steps"></a>Następne kroki  

* [Replikacja między regionami](cross-region-replication-introduction.md)
* [Wymagania i zagadnienia dotyczące korzystania z replikacji między regionami](cross-region-replication-requirements-considerations.md)
* [Wyświetlanie stanu kondycji relacji replikacji](cross-region-replication-display-health-status.md)
* [Metryki replikacji woluminów](azure-netapp-files-metrics.md#replication)
* [Rozwiązywanie problemów z replikacją między regionami](troubleshoot-cross-region-replication.md)

