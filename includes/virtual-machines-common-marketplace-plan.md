---
title: plik dołączany
description: plik dołączany
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 8e0e549f88caf4a541642bab77faf54b5e536b29
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "71174976"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Wdrażanie obrazu za pomocą warunków rynkowych

Niektóre obrazy maszyn wirtualnych w portalu Azure Marketplace mają dodatkowe warunki licencji i zakupu, które należy zaakceptować, zanim będzie można programowo wdrożyć je.  

Aby wdrożyć maszynę wirtualną na podstawie takiego obrazu, musisz zaakceptować warunki obrazu i włączyć wdrażanie programistyczne. Wystarczy wykonać tę czynność tylko raz dla każdej subskrypcji. Następnie przy każdym wdrożeniu maszyny wirtualnej programowo z obrazu należy również określić parametry *planu zakupu* .

W poniższych sekcjach pokazano, jak:

* Dowiedz się, czy obraz portalu Marketplace ma dodatkowe postanowienia licencyjne 
* Zaakceptuj warunki programowo
* Podaj parametry planu zakupu podczas programistycznego wdrażania maszyny wirtualnej

