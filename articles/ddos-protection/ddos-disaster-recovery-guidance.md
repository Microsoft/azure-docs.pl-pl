---
title: Azure DDoS Protection Standardowa ciągłość działania | Microsoft Docs
description: Dowiedz się, co należy zrobić w przypadku zakłóceń w działaniu usługi platformy Azure w Azure DDoS Protection Standardowa.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/16/2021
ms.author: yitoh
ms.topic: article
ms.openlocfilehash: 5085f1584a737e75adccf4ec23bf709bede28a4b
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730500"
---
# <a name="azure-ddos-protection-standard--business-continuity"></a>Azure DDoS Protection Standard — ciągłość działalności biznesowej

Ciągłość działalności biznesowej i odzyskiwanie po awarii w Azure DDoS Protection Standard umożliwia firmie kontynuowanie działania w przypadku zakłóceń. W tym artykule omówiono dostępność (wewnątrz regionu) i odzyskiwanie po awarii.

## <a name="overview"></a>Omówienie
Usługa Azure DDoS Protection w warstwie Standardowa chroni publiczne adresy IP w sieciach wirtualnych. Ochronę można łatwo włączyć w dowolnej nowej lub istniejącej sieci wirtualnej i nie wymaga żadnych zmian aplikacji ani zasobów.

Sieć Virtual Network (VNet) to logiczna reprezentacja sieci w chmurze. Sieci wirtualne pełnią funkcję granicy zaufania do hostów zasobów, takich jak Azure Application Gateway, Azure Firewall i Azure Virtual Machines. Jest on tworzony w zakresie regionu. Sieci *wirtualne* z taką samą przestrzenią adresową można tworzyć w dwóch różnych regionach (na przykład Wschodnie stany USA i Zachodnie stany USA), ale ponieważ mają one tę samą przestrzeń adresową, nie można ich połączyć ze sobą. 

## <a name="business-continuity"></a>Ciągłość działalności biznesowej

Aplikacja może zostać zakłócona na kilka różnych sposobów. Region może zostać całkowicie odcięty z powodu klęski żywiołowej lub częściowej awarii z powodu awarii wielu urządzeń lub usług. Wpływ na chronione sieci wirtualne różni się w każdej z tych sytuacji.

**Pytanie: Co zrobić w przypadku wystąpienia awarii dla całego regionu? Jeśli na przykład region zostanie całkowicie odcięty z powodu klęski żywiołowej? Co się dzieje z sieciami wirtualnymi hostowanych w regionie?**

A: Sieć wirtualna i zasoby w regionie, którego dotyczy problem, pozostają niedostępne w czasie przerwy w działaniu usługi.

![Prosty Virtual Network diagram.](../virtual-network/media/virtual-network-disaster-recovery-guidance/vnet.png)

**Pytanie: Co mogę zrobić, aby ponownie utworzyć tę samą sieć wirtualną w innym regionie?**

O: Sieci wirtualne są dość lekkimi zasobami. Możesz wywołać interfejsy API platformy Azure, aby utworzyć sieć wirtualną z taką samą przestrzenią adresową w innym regionie. Aby odtworzyć to samo środowisko, które było obecne w regionie, którego dotyczy problem, należy wykonać wywołania interfejsu API w celu ponownego wdniania zasobów w sieciach wirtualnych, które miały. Jeśli masz łączność lokalną, na przykład we wdrożeniu hybrydowym, musisz wdrożyć nową VPN Gateway i połączyć się z siecią lokalną.

Aby utworzyć sieć wirtualną, zobacz [Tworzenie sieci wirtualnej](../virtual-network/manage-virtual-network.md#create-a-virtual-network).

**Pytanie: Czy replikę sieci wirtualnej w danym regionie można wcześniej utworzyć ponownie w innym regionie?**

Odpowiedź: Tak, możesz wcześniej utworzyć dwie sieci wirtualne korzystające z tej samej prywatnej przestrzeni adresowej IP i zasobów w dwóch różnych regionach. W przypadku hostowania usług internetowych w sieci wirtualnej można było skonfigurować Traffic Manager do geograficznego rozsyłania ruchu do regionu, który jest aktywny. Nie można jednak połączyć dwóch sieci wirtualnych o tej samej przestrzeni adresowej z siecią lokalną, ponieważ może to spowodować problemy z routingiem. W czasie awarii i utraty sieci wirtualnej w jednym regionie możesz połączyć drugą sieć wirtualną w dostępnym regionie z pasującą przestrzenią adresową z siecią lokalną.

Aby utworzyć sieć wirtualną, zobacz [Tworzenie sieci wirtualnej](../virtual-network/manage-virtual-network.md#create-a-virtual-network).

## <a name="next-steps"></a>Następne kroki

- Dowiedz się, [jak utworzyć plan ochrony przed DDoS.](manage-ddos-protection.md)