---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 8e0e549f88caf4a541642bab77faf54b5e536b29
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "71174976"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Wdrażanie obrazu za pomocą warunków rynkowych

Niektóre obrazy maszyn wirtualnych w portalu Azure Marketplace mają dodatkowe warunki licencji i zakupu, które należy zaakceptować, zanim będzie można programowo wdrożyć je.  

Aby wdrożyć maszynę wirtualną na podstawie takiego obrazu, musisz zaakceptować warunki obrazu i włączyć wdrażanie programistyczne. Wystarczy wykonać tę czynność tylko raz dla każdej subskrypcji. Następnie przy każdym wdrożeniu maszyny wirtualnej programowo z obrazu należy również określić parametry *planu zakupu* .

W poniższych sekcjach pokazano, jak:

* Dowiedz się, czy obraz portalu Marketplace ma dodatkowe postanowienia licencyjne 
* Zaakceptuj warunki programowo
* Podaj parametry planu zakupu podczas programistycznego wdrażania maszyny wirtualnej

