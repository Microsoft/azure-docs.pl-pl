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
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: f2a50872fdb71419a0c3f068712ec67523a098e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708784"
---
# <a name="requirements-and-considerations-for-using-cross-region-replication"></a>Wymagania i zagadnienia dotyczące korzystania z replikacji między regionami 

Należy zwrócić uwagę na następujące wymagania i zagadnienia dotyczące korzystania z funkcji [replikacji między regionami w programie](cross-region-replication-create-peering.md) Azure NetApp Files:  

## <a name="requirements-and-considerations"></a>Wymagania i zagadnienia 

* Funkcja replikacji między regionami jest obecnie w publicznej wersji zapoznawczej. Musisz przesłać żądanie waitlist, aby uzyskać dostęp do funkcji za Azure NetApp Files pomocą [strony przesyłania waitlist replikacji między regionami](https://aka.ms/anfcrrpreviewsignup). Przed rozpoczęciem korzystania z funkcji replikacji między regionami poczekaj oficjalne powiadomienie e-mail z zespołu Azure NetApp Files.
* Replikacja Azure NetApp Files jest dostępna tylko w pewnych stałych parach regionów. Zobacz [obsługiwane pary regionów](cross-region-replication-introduction.md#supported-region-pairs). 
* Woluminy SMB są obsługiwane razem z woluminami NFS. Replikacja woluminów SMB wymaga połączenia Active Directory w źródłowym i docelowym koncie NetApp. Docelowe połączenie usługi AD musi mieć dostęp do serwerów DNS lub dodaje kontrolery domeny, które są dostępne z delegowanej podsieci w regionie docelowym. Aby uzyskać więcej informacji, zobacz [wymagania dotyczące połączeń Active Directory](azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections). 
* Konto docelowe musi znajdować się w innym regionie niż region woluminu źródłowego. Możesz również wybrać istniejące konto NetApp w innym regionie.  
* Azure NetApp Files replikacja nie obsługuje obecnie wielu subskrypcji; wszystkie replikacje muszą być wykonywane w ramach jednej subskrypcji.
* Można skonfigurować maksymalnie pięć woluminów do replikacji w ramach jednej subskrypcji na region. Możesz otworzyć bilet pomocy technicznej, aby zażądać zwiększenia domyślnego limitu przydziału pięciu woluminów docelowych replikacji (na subskrypcję w regionie). 
* W celu odzwierciedlenia nowo dodanej migawki na woluminie źródłowym może wystąpić maksymalnie pięć minut.  
* Topologie kaskadowe i wentylatorowe nie są obsługiwane.
* Konfigurowanie replikacji woluminu dla woluminów źródłowych utworzonych na podstawie migawki nie jest w tej chwili obsługiwane.
* Po skonfigurowaniu replikacji między regionami proces replikacji tworzy *migawki SnapMirror* , aby zapewnić odwołania między woluminem źródłowym a woluminem docelowym. Migawki SnapMirror są automatycznie przetwarzane, gdy dla każdego transferu przyrostowego zostanie utworzony nowy. Nie można usunąć migawek SnapMirror, dopóki nie zostaną usunięte relacje i woluminy replikacji. 
* Ręczne migawki można usunąć na woluminie źródłowym relacji replikacji, gdy relacja replikacji jest aktywna lub zerwana, a także po usunięciu relacji replikacji. Nie można usunąć migawek ręcznych dla woluminu docelowego, dopóki relacja replikacji nie zostanie przerwana.

## <a name="next-steps"></a>Następne kroki
* [Tworzenie komunikacji równorzędnej replikacji](cross-region-replication-create-peering.md)
* [Wyświetlanie stanu kondycji relacji replikacji](cross-region-replication-display-health-status.md)
* [Zarządzanie odzyskiwaniem po awarii](cross-region-replication-manage-disaster-recovery.md)
* [Metryki replikacji woluminów](azure-netapp-files-metrics.md#replication)
* [Rozwiązywanie problemów z replikacją między regionami](troubleshoot-cross-region-replication.md)


