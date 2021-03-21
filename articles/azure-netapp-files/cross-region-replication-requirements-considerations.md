---
title: Wymagania i zagadnienia dotyczące korzystania z Azure NetApp Files replikacji między regionami Microsoft Docs
description: W tym artykule opisano wymagania i zagadnienia dotyczące korzystania z funkcji replikacji między regionami w ramach programu Azure NetApp Files.
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
ms.date: 01/20/2021
ms.author: b-juche
ms.openlocfilehash: ed03e20f7a1a24d1a38e023b958959fdc6fdc326
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100579342"
---
# <a name="requirements-and-considerations-for-using-cross-region-replication"></a>Wymagania i zagadnienia dotyczące korzystania z replikacji między regionami 

Należy zwrócić uwagę na następujące wymagania i zagadnienia dotyczące korzystania z funkcji [replikacji między regionami w programie](cross-region-replication-create-peering.md) Azure NetApp Files:  

## <a name="requirements-and-considerations"></a>Wymagania i zagadnienia 

* Funkcja replikacji między regionami jest obecnie w publicznej wersji zapoznawczej. Musisz przesłać żądanie waitlist, aby uzyskać dostęp do funkcji za Azure NetApp Files pomocą [strony przesyłania waitlist replikacji między regionami](https://aka.ms/anfcrrpreviewsignup). Przed rozpoczęciem korzystania z funkcji replikacji między regionami poczekaj oficjalne powiadomienie e-mail z zespołu Azure NetApp Files.
* Replikacja Azure NetApp Files jest dostępna tylko w pewnych stałych parach regionów. Zobacz [obsługiwane pary regionów](cross-region-replication-introduction.md#supported-region-pairs). 
* Woluminy SMB są obsługiwane razem z woluminami NFS. Replikacja woluminów SMB wymaga połączenia Active Directory w źródłowym i docelowym koncie NetApp. Docelowe połączenie usługi AD musi mieć dostęp do serwerów DNS lub dodaje kontrolery domeny, które są dostępne z delegowanej podsieci w regionie docelowym. Aby uzyskać więcej informacji, zobacz [wymagania dotyczące połączeń Active Directory](create-active-directory-connections.md#requirements-for-active-directory-connections). 
* Konto docelowe musi znajdować się w innym regionie niż region woluminu źródłowego. Możesz również wybrać istniejące konto NetApp w innym regionie.  
* Wolumin docelowy replikacji jest tylko do odczytu do momentu przełączenia w tryb failover [do regionu docelowego](cross-region-replication-manage-disaster-recovery.md#fail-over-to-destination-volume) w celu włączenia woluminu docelowego do odczytu i zapisu. 
* Azure NetApp Files replikacja nie obsługuje obecnie wielu subskrypcji; wszystkie replikacje muszą być wykonywane w ramach jednej subskrypcji.
* Można skonfigurować maksymalnie pięć woluminów do replikacji w ramach jednej subskrypcji na region. Możesz otworzyć bilet pomocy technicznej, aby zażądać zwiększenia domyślnego limitu przydziału pięciu woluminów docelowych replikacji (na subskrypcję w regionie). 
* W celu odzwierciedlenia nowo dodanej migawki na woluminie źródłowym może wystąpić maksymalnie pięć minut.  
* Topologie kaskadowe i wentylatorowe nie są obsługiwane.
* Konfigurowanie replikacji woluminu dla woluminów źródłowych utworzonych na podstawie migawki nie jest w tej chwili obsługiwane.
* Po skonfigurowaniu replikacji między regionami proces replikacji tworzy *migawki SnapMirror* , aby zapewnić odwołania między woluminem źródłowym a woluminem docelowym. Migawki SnapMirror są automatycznie przetwarzane, gdy dla każdego transferu przyrostowego zostanie utworzony nowy. Nie można usunąć migawek SnapMirror, dopóki nie zostaną usunięte relacje i woluminy replikacji. 
* Ręczne migawki można usunąć na woluminie źródłowym relacji replikacji, gdy relacja replikacji jest aktywna lub zerwana, a także po usunięciu relacji replikacji. Nie można usunąć migawek ręcznych dla woluminu docelowego, dopóki relacja replikacji nie zostanie przerwana.
* Nie można przywrócić migawki, która została wykonana przed utworzeniem woluminu docelowego replikacji.

## <a name="next-steps"></a>Następne kroki
* [Tworzenie replikacji woluminu](cross-region-replication-create-peering.md)
* [Wyświetlanie stanu kondycji relacji replikacji](cross-region-replication-display-health-status.md)
* [Zarządzanie odzyskiwaniem po awarii](cross-region-replication-manage-disaster-recovery.md)
* [Metryki replikacji woluminów](azure-netapp-files-metrics.md#replication)
* [Usuwanie replikacji woluminów lub woluminów](cross-region-replication-delete.md)
* [Rozwiązywanie problemów z replikacją między regionami](troubleshoot-cross-region-replication.md)


