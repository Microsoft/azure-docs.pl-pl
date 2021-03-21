---
title: Zmień rozmiar puli pojemności lub woluminu dla Azure NetApp Files | Microsoft Docs
description: Dowiedz się, jak zmienić rozmiar puli pojemności lub woluminu. Zmiana rozmiarów puli pojemności zmienia zakupione Azure NetApp Files pojemności.
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
ms.date: 03/10/2021
ms.author: b-juche
ms.openlocfilehash: 869f46207b940521ee0b66b5afa9c6e2718ab04f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594482"
---
# <a name="resize-a-capacity-pool-or-a-volume"></a>Zmienianie rozmiaru puli pojemności lub woluminu
W razie potrzeby można zmienić rozmiar puli pojemności lub woluminu. 

## <a name="resize-the-capacity-pool"></a>Zmiana rozmiaru puli pojemności 

Rozmiar puli pojemności można zmienić w 1-TiB przyrostach lub zmniejszeniu. Jednak rozmiar puli pojemności nie może być mniejszy niż 4 TiB. Zmiana rozmiarów puli pojemności zmienia zakupione Azure NetApp Files pojemności.

1. W bloku Zarządzanie kontem usługi NetApp kliknij pulę pojemności, której rozmiar chcesz zmienić. 
2. Kliknij prawym przyciskiem myszy nazwę puli pojemności lub kliknij "..." na końcu wiersza puli pojemności, aby wyświetlić menu kontekstowe. 
3. Użyj opcji menu kontekstowego, aby zmienić rozmiar lub usunąć pulę pojemności.

## <a name="resize-a-volume"></a>Zmiana rozmiaru woluminu

W razie potrzeby można zmienić rozmiar woluminu. Użycie pojemności woluminu jest liczone jako użycie aprowizowanej pojemności puli.

1. W bloku Zarządzanie kontem usługi NetApp kliknij pozycję **woluminy**. 
2. Kliknij prawym przyciskiem myszy nazwę woluminu, którego rozmiar chcesz zmienić, lub kliknij "..." na końcu wiersza woluminu, aby wyświetlić menu kontekstowe.
3. Użyj opcji menu kontekstowego, aby zmienić rozmiar lub usunąć wolumin.

## <a name="resize-a-cross-region-replication-destination-volume"></a>Zmiana rozmiaru woluminu docelowego replikacji między regionami 

W relacji [replikacji między regionami](cross-region-replication-introduction.md) wolumin docelowy jest automatycznie zmieniany na podstawie rozmiaru woluminu źródłowego. W związku z tym nie trzeba oddzielnie zmieniać rozmiaru woluminu docelowego. To automatyczne zmianę rozmiarów jest stosowane, gdy woluminy znajdują się w aktywnej relacji replikacji lub gdy Komunikacja równorzędna replikacji zostanie przerwana z [operacją ponownej synchronizacji](cross-region-replication-manage-disaster-recovery.md#resync-replication). 

W poniższej tabeli opisano zachowanie zmiany rozmiarów woluminu docelowego na podstawie [stanu dublowania](cross-region-replication-display-health-status.md):

|  Stan dublowania  | Zachowanie zmiany rozmiarów woluminu docelowego |
|-|-|
| *Dublowane* | Gdy wolumin docelowy został zainicjowany i jest gotowy do otrzymywania aktualizacji dublowania, zmiany rozmiaru woluminu źródłowego automatycznie zmienia rozmiar woluminów docelowych. |
| *Praw* | W przypadku zmiany rozmiaru woluminu źródłowego i stanu dublowania wolumin *docelowy zostanie automatycznie* zmieniony z [operacją ponownej synchronizacji](cross-region-replication-manage-disaster-recovery.md#resync-replication).  |
| *Niezainicjowane* | W przypadku zmiany rozmiaru woluminu źródłowego, gdy stan dublowany nadal jest *zainicjowany*, zmiana rozmiaru woluminu docelowego należy wykonać ręcznie. W związku z tym zaleca się zaczekanie na zakończenie inicjalizacji (to oznacza, gdy stan dublowany zostanie *zdublowany*), aby zmienić rozmiar woluminu źródłowego. | 

> [!IMPORTANT]
> Upewnij się, że masz wystarczająco dużo miejsca w pulach pojemności dla woluminów źródłowych i docelowych replikacji między regionami. Zmiana rozmiaru woluminu źródłowego powoduje automatyczne zmianę rozmiaru woluminu docelowego. Ale jeśli Pula pojemności hostującym wolumin docelowy nie ma wystarczającej wysokości, zmienianie rozmiarów woluminów źródłowych i docelowych zakończy się niepowodzeniem.

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)
- [Zarządzanie ręczną pulą pojemności usługi QoS](manage-manual-qos-capacity-pool.md)
- [Dynamiczna zmiana poziomu usługi woluminu](dynamic-change-volume-service-level.md) 