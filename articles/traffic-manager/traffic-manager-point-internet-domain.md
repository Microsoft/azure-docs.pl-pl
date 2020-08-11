---
title: Wskaż domenę internetową, aby Traffic Manager na platformie Azure Traffic Manager
description: Ten artykuł pomoże Ci skonfigurować nazwę domeny firmowej w taki sposób, aby wskazywała nazwę domeny usługi Traffic Manager.
services: traffic-manager
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: rohink
ms.openlocfilehash: 69bdf9a0e04b4d9c2a55f1c0f346d601830ded09
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2020
ms.locfileid: "88053069"
---
# <a name="point-a-company-internet-domain-to-an-azure-traffic-manager-domain"></a>Ustawianie firmowej domeny internetowej tak, aby wskazywała domenę usługi Azure Traffic Manager

Podczas tworzenia profilu usługi Traffic Manager platforma Azure automatycznie przypisuje nazwę DNS dla danego profilu. Aby użyć nazwy z bieżącej strefy DNS, należy utworzyć rekord DNS CNAME, który będzie mapowany na nazwę domeny profilu usługi Traffic Manager. Nazwa domeny usługi Traffic Manager jest wyświetlana w sekcji **Ogólne** na stronie konfiguracji profilu usługi Traffic Manager.

Na przykład aby nazwa domeny `www.contoso.com` wskazywała nazwę DNS `contoso.trafficmanager.net` usługi Traffic Manager, należy utworzyć następujący rekord zasobu DNS:

`www.contoso.com IN CNAME contoso.trafficmanager.net.`

Wszystkie żądania ruchu do *www \. contoso.com* są kierowane do *contoso.trafficmanager.NET*.

> [!IMPORTANT]
> Domena drugiego poziomu, na przykład *contoso.com*, nie może wskazywać domeny usługi Traffic Manager. Standardy protokołu DNS nie zezwalają na ustanawianie rekordów CNAME dla nazw domen drugiego poziomu.

## <a name="next-steps"></a>Następne kroki

* [Metody routingu w usłudze Traffic Manager](traffic-manager-routing-methods.md)
* [Traffic Manager — wyłączanie, Włączanie lub usuwanie profilu](disable-enable-or-delete-a-profile.md)
* [Traffic Manager — wyłączanie lub włączanie punktu końcowego](disable-or-enable-an-endpoint.md)
