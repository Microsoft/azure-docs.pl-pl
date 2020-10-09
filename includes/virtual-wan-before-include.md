---
title: plik dołączania
description: plik dołączania
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/06/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 5a3b9160f1fb28c68bdbaa540c78b2890dbcab82
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91812733"
---
* Masz sieć wirtualną, z którą chcesz nawiązać połączenie. Sprawdź, czy żadna z podsieci sieci lokalnych nie nakłada się na sieci wirtualne, z którymi chcesz nawiązać połączenie. Aby utworzyć sieć wirtualną w Azure Portal, zobacz [Przewodnik Szybki Start](../articles/virtual-network/quick-create-portal.md).

* Twoja sieć wirtualna nie ma żadnych bram sieci wirtualnej. Jeśli sieć wirtualna ma bramę (VPN lub ExpressRoute), należy usunąć wszystkie bramy. Ta konfiguracja wymaga, aby w zamian były połączone sieci wirtualne z bramą wirtualnego centrum sieci WAN.

* Uzyskaj zakres adresów IP w regionie koncentratora. Centrum jest siecią wirtualną, która jest tworzona i używana przez wirtualną sieć WAN. Zakres adresów określony dla centrum nie może pokrywać się z żadną z istniejących sieci wirtualnych, z którymi się łączysz. Nie może również pokrywać się z zakresami adresów, z którymi łączysz się lokalnie. Jeśli nie znasz zakresów adresów IP znajdujących się w konfiguracji sieci lokalnej, koordynuj się z osobą, która może podać te szczegóły.

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
