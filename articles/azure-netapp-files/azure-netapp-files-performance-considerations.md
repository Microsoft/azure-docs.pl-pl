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
ms.date: 06/25/2019
ms.author: b-juche
ms.openlocfilehash: 5f88b4755c7b4c0b20f27065cf9de2351251bc1c
ms.sourcegitcommit: 29400316f0c221a43aff3962d591629f0757e780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2020
ms.locfileid: "87513880"
---
# <a name="performance-considerations-for-azure-netapp-files"></a>Kwestie dotyczące wydajności dla usługi Azure NetApp Files

[Limit przepływności](azure-netapp-files-service-levels.md) dla woluminu zależy od kombinacji przydziału przypisanego do woluminu i wybranego poziomu usługi. Po wprowadzeniu planów wydajności dotyczących Azure NetApp Files należy zrozumieć kilka zagadnień. 

## <a name="quota-and-throughput"></a>Przydział i przepływność  

Limit przepływności jest tylko jednym wyznacznikiem rzeczywistej wydajności, który zostanie zrealizowany.  

Typowe zagadnienia dotyczące wydajności magazynu, w tym mieszanie odczytu i zapisu, rozmiar transferu, wzorce losowe lub sekwencyjne, a także wiele innych czynników, które przyczyniają się do całkowitej wydajności dostarczonej.  

Maksymalna przepływność empiryczna, która była obserwowana w testowaniu, to 4 500 MiB/s.  W warstwie Premium Storage przydział woluminu 70,31 TiB będzie zapewniać limit przepływności wystarczający do osiągnięcia tego poziomu wydajności.  

W przypadku rozważania przypisywania ilości przydziałów woluminów poza 70,31 TiB, do woluminu można przypisać dodatkowe dane. Jednak dodany przydział nie spowoduje dalszej podwyżki rzeczywistej przepływności.  

## <a name="overprovisioning-the-volume-quota"></a>Nadmierne Inicjowanie obsługi przydziału woluminu

Jeśli wydajność obciążenia jest powiązana z ograniczeniem przepływności, można nadmiernie zainicjować obsługę przydziału woluminu, aby ustawić wyższy poziom przepływności i osiągnąć wyższą wydajność.  

Jeśli na przykład wolumin w warstwie Premium Storage ma tylko 500 GiB danych, ale wymaga 128 MiB/s przepływności, można ustawić limit przydziału na 2 TiB, tak aby poziom przepływności został odpowiednio ustawiony (64 MiB/s na TB * 2 TiB = 128 MiB/s).  

Jeśli ciągle nadmiernie zastrzegasz wolumin do osiągnięcia wyższej przepływności, rozważ użycie wyższego poziomu usługi.  W powyższym przykładzie można osiągnąć ten sam limit przepływności z połowami przydziałów woluminu, korzystając z warstwy magazynu Ultra Storage (128 MiB/s na TiB * 1 TiBd = 128 MiB/s).

## <a name="dynamically-increasing-or-decreasing-volume-quota"></a>Dynamiczne zwiększanie lub zmniejszanie limitu przydziału woluminu

Jeśli wymagania dotyczące wydajności mają charakter tymczasowy lub w przypadku zwiększonej wydajności przez określony czas, można dynamicznie zwiększyć lub zmniejszyć przydział woluminu, aby natychmiast dostosować limit przepływności.  Należy zwrócić uwagę na następujące kwestie: 

* Przydział woluminu można zwiększyć lub zmniejszyć bez konieczności wstrzymywania operacji we/wy, a dostęp do tego woluminu nie jest przerywany ani nie ma na nie wpływu.  

    Limit przydziału można dostosować podczas aktywnej transakcji we/wy względem woluminu.  Należy pamiętać, że przydział woluminu nie można nigdy zmniejszyć poniżej ilości danych logicznych przechowywanych w woluminie.

* Gdy przydział woluminu zostanie zmieniony, odpowiednia zmiana limitu przepływności jest niemal natychmiastowa. 

    Zmiana nie przerywa ani nie wpływa na dostęp do woluminu lub we/wy.  

* Dostosowanie limitu przydziału woluminu wymaga zmiany rozmiaru puli pojemności.  

    Rozmiar puli pojemności można dostosować dynamicznie i bez wpływu na dostępność woluminu lub we/wy.

## <a name="next-steps"></a>Następne kroki

- [Poziomy usług dla usługi Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Testy porównawcze wydajności dla systemu Linux](performance-benchmarks-linux.md)