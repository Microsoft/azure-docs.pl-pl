---
title: Ręczna Pula pojemności usługi Azure NetApp Files | Microsoft Docs
description: Zawiera wprowadzenie do ręcznej puli pojemności i odwołań do dodatkowych informacji.
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
ms.date: 09/23/2020
ms.author: b-juche
ms.openlocfilehash: 14b905c69f0dab933159b414028db3e985d314a3
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91935837"
---
# <a name="manual-qos-capacity-pool"></a>Ręczna Pula pojemności QoS

Ten artykuł zawiera wprowadzenie do funkcji ręcznego puli pojemności Quality of Service (QoS).

## <a name="how-manual-qos-differs-from-auto-qos"></a>Jak różnią się ręczne ustawienia QoS od autoqos

[Typ QoS](azure-netapp-files-understand-storage-hierarchy.md#qos_types) jest atrybutem puli pojemności. Azure NetApp Files oferuje dwa typy usług QoS o pojemności — Funkcja Auto(domyślna) i ręczna.  

W *ręcznej* puli pojemności usługi QoS można przypisać pojemność i przepływność dla woluminu niezależnie. Całkowita przepływność wszystkich woluminów utworzonych z ręczną pulą pojemności usługi QoS jest ograniczona przez łączną przepływność puli. Jest ona określana na podstawie kombinacji rozmiaru puli i przepływności na poziomie usług. 

W puli pojemności *automatycznej* usługi QoS przepływność jest przypisywana automatycznie do woluminów w puli proporcjonalnie do przydziału rozmiaru przypisanego do woluminów.  

Zobacz [Hierarchia magazynu Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md) i [zagadnienia dotyczące wydajności dla Azure NetApp Files](azure-netapp-files-performance-considerations.md) , aby uzyskać informacje o typach QoS.

## <a name="how-to-specify-the-manual-qos-type"></a>Jak określić typ ręcznych ustawień QoS

Podczas [tworzenia puli pojemności](azure-netapp-files-set-up-capacity-pool.md)można określić, aby Pula pojemności korzystała z ręcznego typu QoS.  Możesz również [zmienić istniejącą pulę pojemności](manage-manual-qos-capacity-pool.md#change-to-qos) tak, aby korzystała z ręcznego typu QoS. 

Ustawienie typu pojemności na ręczną QoS jest trwałą zmianą. Nie można skonwertować ręcznego narzędzia pojemności typu QoS na pulę wydajności autoqos. 

Korzystanie z ręcznego typu QoS wymaga [zarejestrowania tej funkcji](manage-manual-qos-capacity-pool.md#register-the-feature).  

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie ręczną pulą pojemności usługi QoS](manage-manual-qos-capacity-pool.md)
* [Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)
* [Hierarchia magazynu](azure-netapp-files-understand-storage-hierarchy.md) 
* [Poziomy usług dla usługi Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Kwestie dotyczące wydajności dla usługi Azure NetApp Files](azure-netapp-files-performance-considerations.md)
* [Model kosztów usługi Azure NetApp Files](azure-netapp-files-cost-model.md)
* [Limity zasobów dla usługi Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Tworzenie woluminu NFS](azure-netapp-files-create-volumes.md)
* [Tworzenie woluminu SMB](azure-netapp-files-create-volumes-smb.md)
* [Tworzenie woluminu dwuprotokołowego](create-volumes-dual-protocol.md)
* [Metryki dla usługi Azure NetApp Files](azure-netapp-files-metrics.md)
* [Rozwiązywanie problemów z pulą pojemności](troubleshoot-capacity-pools.md)
