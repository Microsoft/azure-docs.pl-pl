---
title: Zagadnienia dotyczące wydajności Azure NetApp Files | Microsoft Docs
description: Dowiedz się więcej na temat wydajności Azure NetApp Files, w tym relacje limitu przydziału i przepływności oraz sposób dynamicznego zwiększania/zmniejszania limitu przydziału woluminu.
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
ms.date: 02/19/2021
ms.author: b-juche
ms.openlocfilehash: f963c87148c08a4855befc5afb79d9c5ea0f4481
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101713392"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Kwestie dotyczące wydajności dla usługi Azure NetApp Files

[Limit przepływności](azure-netapp-files-service-levels.md) dla woluminu z automatyczną QoS jest określany przez kombinację przydziału przypisanego do woluminu i wybranego poziomu usługi. W przypadku woluminów z ręczną usługą QoS limit przepływności można zdefiniować pojedynczo. Po wprowadzeniu planów wydajności dotyczących Azure NetApp Files należy zrozumieć kilka zagadnień. 

## <a name="quota-and-throughput"></a>Przydział i przepływność  

Limit przepływności jest tylko jednym wyznacznikiem rzeczywistej wydajności, który zostanie zrealizowany.  

Typowe zagadnienia dotyczące wydajności magazynu, w tym mieszanie odczytu i zapisu, rozmiar transferu, wzorce losowe lub sekwencyjne, a także wiele innych czynników, które przyczyniają się do całkowitej wydajności dostarczonej.  

Maksymalna przepływność empiryczna, która była obserwowana w testowaniu, to 4 500 MiB/s.  W warstwie Premium Storage automatyczny limit przydziału woluminu QoS wynoszący 70,31 TiB zapewni limit przepływności wystarczający do osiągnięcia tego poziomu wydajności.  

W przypadku automatycznych woluminów QoS, Jeśli rozważasz przypisywanie ilości przydziałów woluminów poza 70,31 TiB, przydziały przydziału mogą być przypisane do woluminu do przechowywania dodatkowych danych. Jednak dodany przydział nie spowoduje dalszej podwyżki rzeczywistej przepływności.  

Ten sam, empiryczny pułap przepływności ma zastosowanie do woluminów z ręczną usługą QoS. Maksymalną przepływność można przypisać do woluminu to 4 500 MiB/s.

## <a name="automatic-qos-volume-quota-and-throughput"></a>Automatyczne przydziały i przepływność woluminu QoS

W tej sekcji opisano zarządzanie przydziałami i przepływność dla woluminów z typem automatycznej jakości usług.

### <a name="overprovisioning-the-volume-quota"></a>Nadmierne Inicjowanie obsługi przydziału woluminu

Jeśli wydajność obciążenia jest powiązana z ograniczeniem przepływności, można nadmiernie zainicjować obsługę administracyjną przydziału woluminu QoS, aby ustawić wyższy poziom przepływności i osiągnąć wyższą wydajność.  

Na przykład jeśli automatyczny wolumin QoS w warstwie Premium Storage ma tylko 500 GiB danych, ale wymaga 128 MiB/s przepływności, można ustawić limit przydziału na 2 TiB, tak aby poziom przepływności został odpowiednio ustawiony (64 MiB/s na TB * 2 TiB = 128 MiB/s).  

W przypadku ciągłej nadmiernej aprowizacji woluminu w celu osiągnięcia większej przepływności należy rozważyć użycie ręcznych woluminów QoS lub użycie wyższego poziomu usługi.  W powyższym przykładzie można osiągnąć ten sam limit przepływności z połowami automatycznego przydziału woluminu QoS przy użyciu warstwy Ultra Storage (128 MiB/s na TiB * 1 TiBd = 128 MiB/s).

### <a name="dynamically-increasing-or-decreasing-volume-quota"></a>Dynamiczne zwiększanie lub zmniejszanie limitu przydziału woluminu

Jeśli wymagania dotyczące wydajności mają charakter tymczasowy lub w przypadku zwiększonej wydajności przez określony czas, można dynamicznie zwiększyć lub zmniejszyć przydział woluminu, aby natychmiast dostosować limit przepływności.  Należy zwrócić uwagę na następujące kwestie: 

* Przydział woluminu można zwiększyć lub zmniejszyć bez konieczności wstrzymywania operacji we/wy, a dostęp do tego woluminu nie jest przerywany ani nie ma na nie wpływu.  

    Limit przydziału można dostosować podczas aktywnej transakcji we/wy względem woluminu.  Należy pamiętać, że przydział woluminu nie można nigdy zmniejszyć poniżej ilości danych logicznych przechowywanych w woluminie.

* Gdy przydział woluminu zostanie zmieniony, odpowiednia zmiana limitu przepływności jest niemal natychmiastowa. 

    Zmiana nie przerywa ani nie wpływa na dostęp do woluminu lub we/wy.  

* Dostosowanie limitu przydziału woluminu może wymagać zmiany rozmiaru puli pojemności.  

    Rozmiar puli pojemności można dostosować dynamicznie i bez wpływu na dostępność woluminu lub we/wy.

## <a name="manual-qos-volume-quota-and-throughput"></a>Ręczne przydziały i przepływność woluminu QoS 

W przypadku korzystania z ręcznych woluminów QoS nie trzeba nadmiernie udostępniać przydziału woluminu, aby osiągnąć wyższą przepływność, ponieważ przepływność może być przypisana do każdego woluminu niezależnie. Jednak nadal trzeba upewnić się, że Pula pojemności została wstępnie zainicjowana przy użyciu wystarczającej przepływności dla potrzeb związanych z wydajnością. Jest obsługiwana przepływność puli pojemności zgodnie z jej rozmiarem i poziomem usług. Aby uzyskać więcej informacji, zobacz [poziomy usług dla Azure NetApp Files](azure-netapp-files-service-levels.md) .


## <a name="next-steps"></a>Następne kroki

- [Kalkulator wydajności Azure NetApp Files](https://cloud.netapp.com/azure-netapp-files/tco?hs_preview=tIKQbfoF-41214739590)
- [Poziomy usług dla usługi Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Testy porównawcze wydajności dla systemu Linux](performance-benchmarks-linux.md)
