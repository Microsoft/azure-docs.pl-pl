---
title: Ciągłość biznesowa sieci wirtualnej | Microsoft Docs
description: Dowiedz się, co należy zrobić w przypadku przerwania działania usługi platformy Azure, które ma wpływ na sieci wirtualne platformy Azure.
services: virtual-network
documentationcenter: ''
author: NarayanAnnamalai
manager: jefco
editor: ''
ms.assetid: ad260ab9-d873-43b3-8896-f9a1db9858a5
ms.service: virtual-network
ms.workload: virtual-network
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2016
ms.author: narayan
ms.reviewer: aglick
ms.openlocfilehash: 3f91d24bff0bec540ff0e7964f21c2f47c03638c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67876167"
---
# <a name="virtual-network--business-continuity"></a>Virtual Network — ciągłość działania firmy

## <a name="overview"></a>Omówienie
Virtual Network (VNet) to logiczna reprezentacja sieci w chmurze. Pozwala on definiować własną prywatną przestrzeń adresów IP i dzielić sieć na podsieci. Sieci wirtualnych służy jako granica zaufania do hostowania zasobów obliczeniowych, takich jak Azure Virtual Machines i Cloud Services (role sieci Web/proces roboczy). Sieć wirtualna umożliwia bezpośrednią komunikację z prywatnym adresem IP między znajdującymi się w niej zasobami. Możesz połączyć sieć wirtualną z siecią lokalną za pomocą VPN Gateway lub ExpressRoute.

Sieć wirtualna jest tworzona w ramach zakresu regionu. Można *utworzyć* sieci wirtualnych z tą samą przestrzenią adresową w dwóch różnych regionach (na przykład Wschodnie stany USA i stany USA), ale ponieważ mają one taką samą przestrzeń adresową, nie można połączyć się ze sobą. 

## <a name="business-continuity"></a>Ciągłość działalności biznesowej

Może istnieć kilka różnych sposobów zakłócania działania aplikacji. Region można całkowicie wyciąć z powodu klęski żywiołowej lub częściowej awarii z powodu awarii wielu urządzeń lub usług. Wpływ na usługę sieci wirtualnej różni się w każdej z tych sytuacji.

**P: Jeśli wystąpi awaria dla całego regionu, co mam zrobić? Na przykład jeśli region jest całkowicie wycięty ze względu na klęskę żywiołową? Co się dzieje z sieciami wirtualnymi hostowanymi w regionie?**

Odp.: Sieć wirtualna i zasoby w regionie, którego to dotyczy, pozostają niedostępne w czasie przerwania działania usługi.

![Prosty diagram Virtual Network](./media/virtual-network-disaster-recovery-guidance/vnet.png)

**P: co mogę zrobić, aby ponownie utworzyć tę samą sieć wirtualną w innym regionie?**

Odp.: sieci wirtualne to dość lekkie zasoby. Możesz wywołać interfejsy API platformy Azure, aby utworzyć sieć wirtualną z tą samą przestrzenią adresową w innym regionie. Aby odtworzyć to samo środowisko, które było obecne w danym regionie, należy wykonać wywołania interfejsu API w celu ponownego wdrożenia Cloud Services ról sieci Web i procesów roboczych oraz maszyn wirtualnych. W przypadku połączenia lokalnego, na przykład w ramach wdrożenia hybrydowego, należy wdrożyć nowe VPN Gateway i nawiązać połączenie z siecią lokalną.

Aby utworzyć sieć wirtualną, zobacz [Tworzenie sieci wirtualnej](manage-virtual-network.md#create-a-virtual-network).

**P: czy replika sieci wirtualnej w danym regionie zostanie utworzona w innym regionie przed czasem?**

Odp.: tak, możesz utworzyć dwa sieci wirtualnych przy użyciu tej samej prywatnej przestrzeni adresów IP i zasobów w dwóch różnych regionach przed czasem. W przypadku hostowania usług dostępnych z Internetu w sieci wirtualnej można ustawić Traffic Manager na ruch tras geograficznych do regionu, który jest aktywny. Nie można jednak połączyć dwóch sieci wirtualnych z tą samą przestrzenią adresową z siecią lokalną, ponieważ może to powodować problemy z routingiem. W chwili awarii i utraty sieci wirtualnej w jednym regionie można podłączyć drugą sieć wirtualną w dostępnym regionie, ze zgodną przestrzenią adresową do sieci lokalnej.

Aby utworzyć sieć wirtualną, zobacz [Tworzenie sieci wirtualnej](manage-virtual-network.md#create-a-virtual-network).

