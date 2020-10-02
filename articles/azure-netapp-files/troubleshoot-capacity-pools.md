---
title: Rozwiązywanie problemów z pulą pojemności dla Azure NetApp Files | Microsoft Docs
description: Opisuje potencjalne problemy, które mogą wystąpić podczas zarządzania pulami pojemności i oferuje rozwiązania problemów.
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
ms.topic: troubleshooting
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: e7e3c2eb058d3549f2e1a10ffacf01fd354fbd47
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91651058"
---
# <a name="troubleshoot-capacity-pool-issues"></a>Rozwiązywanie problemów z pulą pojemności

W tym artykule opisano rozwiązania problemów, które mogą wystąpić podczas zarządzania pulami pojemności. 

## <a name="error-conditions-and-resolutions"></a>Warunki błędów i rozwiązania 

|     Błąd    |     Rozwiązanie    |
|-|-|
| Problemy z tworzeniem puli pojemności |  Upewnij się, że liczba pul pojemności nie przekracza limitu. Zobacz [limity zasobów dla Azure NetApp Files](azure-netapp-files-resource-limits.md).  Jeśli liczba jest mniejsza niż limit i nadal występują problemy, należy podać bilet pomocy technicznej i określić nazwę puli pojemności. |
| Problemy z usuwaniem puli pojemności  |  Upewnij się, że wszystkie woluminy Azure NetApp Files i migawki w subskrypcji, w której próbujesz usunąć pulę pojemności, zostaną usunięte. <br> Jeśli wszystkie woluminy i migawki zostały już usunięte, a nadal nie można usunąć puli pojemności, odwołania do zasobów mogą nadal istnieć bez wyświetlania w portalu. W takim przypadku należy utworzyć bilet pomocy technicznej i określić, że wykonano powyższe zalecane czynności. |
| Tworzenie lub modyfikowanie woluminu kończy się niepowodzeniem z `Requested throughput not available` powodu błędu | Dostępna przepływność dla woluminu jest określana na podstawie rozmiaru puli pojemności i poziomu usługi. Jeśli nie masz wystarczającej przepływności, Zwiększ rozmiar puli lub Dostosuj istniejącą przepływność woluminu. | 

## <a name="next-steps"></a>Następne kroki  

* [Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)
* [Zarządzanie ręczną pulą pojemności usługi QoS](manage-manual-qos-capacity-pool.md)
