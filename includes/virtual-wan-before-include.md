---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4940bce453729d13ba76071fa59bcf95c7672a24
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94359507"
---
* Masz subskrypcję platformy Azure. Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Masz sieć wirtualną, z którą chcesz nawiązać połączenie. Sprawdź, czy żadna z podsieci sieci lokalnych nie nakłada się na sieci wirtualne, z którymi chcesz nawiązać połączenie. Aby utworzyć sieć wirtualną w Azure Portal, zapoznaj się z artykułem [Szybki Start](../articles/virtual-network/quick-create-portal.md) .

* Sieć wirtualna nie może mieć żadnych istniejących bram sieci wirtualnej. Jeśli sieć wirtualna ma już bramy (VPN lub ExpressRoute), przed kontynuowaniem należy usunąć wszystkie bramy. Ta konfiguracja wymaga, aby sieci wirtualne łączyły się tylko z bramą wirtualnego centrum sieci WAN.

* Koncentrator wirtualny jest siecią wirtualną, która jest tworzona i używana przez wirtualną sieć WAN. Jest to rdzeń wirtualnej sieci WAN w regionie. Uzyskaj zakres adresów IP dla regionu koncentratora wirtualnego. Zakres adresów określony dla centrum nie może pokrywać się z żadną z istniejących sieci wirtualnych, z którymi się łączysz. Nie może ona również nakładać się na lokalne zakresy adresów, z którymi się łączysz. Jeśli nie znasz zakresów adresów IP znajdujących się w konfiguracji sieci lokalnej, koordynuj się z osobą, która może podać te szczegóły.
