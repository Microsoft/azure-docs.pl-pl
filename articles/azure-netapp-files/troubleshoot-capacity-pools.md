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
ms.date: 10/13/2020
ms.author: b-juche
ms.openlocfilehash: 54e6f4abd5ca6d15a4cc5a7bc9015abb005296a0
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92013648"
---
# <a name="troubleshoot-capacity-pool-issues"></a>Rozwiązywanie problemów z pulą pojemności

W tym artykule opisano rozwiązania problemów, które mogą wystąpić podczas zarządzania pulami pojemności, łącznie z operacją zmiany puli. 

## <a name="issues-managing-a-capacity-pool"></a>Problemy związane z zarządzaniem pulą pojemności 

|     Błąd    |     Rozwiązanie    |
|-|-|
| Problemy z tworzeniem puli pojemności |  Upewnij się, że liczba pul pojemności nie przekracza limitu. Zobacz [limity zasobów dla Azure NetApp Files](azure-netapp-files-resource-limits.md).  Jeśli liczba jest mniejsza niż limit i nadal występują problemy, należy podać bilet pomocy technicznej i określić nazwę puli pojemności. |
| Problemy z usuwaniem puli pojemności  |  Upewnij się, że wszystkie woluminy Azure NetApp Files i migawki w subskrypcji, w której próbujesz usunąć pulę pojemności, zostaną usunięte. <br> Jeśli wszystkie woluminy i migawki zostały już usunięte, a nadal nie można usunąć puli pojemności, odwołania do zasobów mogą nadal istnieć bez wyświetlania w portalu. W takim przypadku należy utworzyć bilet pomocy technicznej i określić, że wykonano powyższe zalecane czynności. |
| Tworzenie lub modyfikowanie woluminu kończy się niepowodzeniem z `Requested throughput not available` powodu błędu | Dostępna przepływność dla woluminu jest określana na podstawie rozmiaru puli pojemności i poziomu usługi. Jeśli nie masz wystarczającej przepływności, Zwiększ rozmiar puli lub Dostosuj istniejącą przepływność woluminu. | 

## <a name="issues-moving-a-capacity-pool"></a>Problemy z przesuwaniem puli pojemności 
|     Błąd    |     Rozwiązanie    |
|-|-|
| Zmiana puli pojemności dla woluminu nie jest dozwolona. | Być może nie masz jeszcze autoryzacji do korzystania z tej funkcji. <br> Funkcja przenoszenia woluminu do innej puli pojemności jest obecnie w wersji zapoznawczej. Jeśli ta funkcja jest używana po raz pierwszy, należy najpierw zarejestrować funkcję i ustawić ją `-FeatureName ANFTierChange` . Zapoznaj się z procedurą rejestracji w celu [dynamicznego zmiany poziomu usługi woluminu](dynamic-change-volume-service-level.md). |
| Rozmiar puli pojemności jest za mały dla całkowitego rozmiaru woluminu. |  Ten błąd jest wynikiem docelowej puli pojemności, która nie ma dostępnej pojemności dla przenoszonego woluminu.  <br> Zwiększ rozmiar puli docelowej lub wybierz inną pulę, która jest większa.  Zobacz [zmiana rozmiaru puli pojemności lub woluminu](azure-netapp-files-resize-capacity-pools-or-volumes.md).   |
| Nie można przenieść woluminu, gdy docelowa Pula pojemności ma inny typ szyfrowania od oryginalnej puli pojemności.  Na przykład przechodzenie z podwójnego szyfrowania do jednego szyfrowania lub odwrotnie.  | Wybierz docelową pulę wydajności z tym samym typem szyfrowania co źródłowa Pula pojemności.   |
|  Nie można ukończyć zmiany puli, ponieważ wolumin o nazwie `'{source pool name}'` już istnieje w puli docelowej `'{target pool name}'` | Ten błąd występuje, ponieważ wolumin o tej samej nazwie już istnieje w docelowej puli pojemności.  Wybierz inną pulę pojemności, która nie ma woluminu o tej samej nazwie.   | 

## <a name="next-steps"></a>Następne kroki  

* [Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)
* [Zarządzanie ręczną pulą pojemności usługi QoS](manage-manual-qos-capacity-pool.md)
* [Dynamiczna zmiana poziomu usługi woluminu](dynamic-change-volume-service-level.md)
* [Zmienianie rozmiaru puli pojemności lub woluminu](azure-netapp-files-resize-capacity-pools-or-volumes.md)
