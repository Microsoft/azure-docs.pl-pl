---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 09/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 945d701a2a7dffc259c601b4dab9fa1333ccc066
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96023531"
---
Przed rozpoczęciem konfiguracji Sprawdź, czy spełniasz następujące kryteria:

* Jeśli masz już sieć wirtualną, z którą chcesz nawiązać połączenie, sprawdź, czy żadna z podsieci sieci lokalnej nie nakłada się na nią. Twoja sieć wirtualna nie wymaga podsieci bramy i nie może mieć żadnych bram sieci wirtualnej. Jeśli nie masz sieci wirtualnej, możesz ją utworzyć, wykonując czynności opisane w tym artykule.
* Uzyskaj zakres adresów IP w regionie koncentratora. Centrum jest siecią wirtualną, a zakres adresów określony dla regionu centrum nie może nakładać się na istniejącą sieć wirtualną, z którą się łączysz. Nie może ona również nakładać się na zakresy adresów, które są połączone z lokalnymi. Jeśli nie znasz zakresów adresów IP znajdujących się w konfiguracji sieci lokalnej, koordynuj się z osobą, która może podać te szczegóły.
* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).