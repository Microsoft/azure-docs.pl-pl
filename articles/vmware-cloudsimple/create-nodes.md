---
title: Inicjowanie obsługi węzłów dla rozwiązań VMware przez CloudSimple — Azure
description: Dowiedz się, jak dodać węzły do programu VMWare z wdrożeniem CloudSimple w Azure Portal. W środowisku chmury prywatnej można skonfigurować funkcję płatność zgodnie z rzeczywistym użyciem.
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ccff5b4dc6ed5a571e25ab0569b9fa92674e1340
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88140738"
---
# <a name="provision-nodes-for-azure-vmware-solution-by-cloudsimple"></a>Inicjowanie obsługi administracyjnej węzłów dla rozwiązań VMware platformy Azure według CloudSimple

Inicjowanie obsługi węzłów w Azure Portal. Następnie możesz skonfigurować przepustowość zgodnie z rzeczywistym użyciem dla środowiska chmury prywatnej CloudSimple.

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="add-a-node-to-your-cloudsimple-private-cloud"></a>Dodawanie węzła do prywatnej chmury CloudSimple

1. Wybierz pozycję **Wszystkie usługi**.
2. Wyszukaj **węzły CloudSimple**.

   ![Wyszukaj węzły CloudSimple](media/create-cloudsimple-node-search.png)

3. Wybierz **węzły CloudSimple**.
4. Kliknij przycisk **Dodaj** , aby utworzyć węzły.

    ![Dodaj węzły CloudSimple](media/create-cloudsimple-node-add.png)

5. Wybierz subskrypcję, w której chcesz udostępnić węzły CloudSimple.
6. Wybierz grupę zasobów dla węzłów. Aby dodać nową grupę zasobów, kliknij pozycję **Utwórz nową**.
7. Wprowadź prefiks, aby zidentyfikować węzły.
8. Wybierz lokalizację dla zasobów węzła.
9. Wybierz dedykowaną lokalizację do hostowania zasobów węzła.
10. Wybierz [Typ węzła](cloudsimple-node.md).
11. Wybierz liczbę węzłów do aprowizacji.
12. Wybierz pozycję **Przejrzyj i utwórz**.
13. Przejrzyj ustawienia. Aby zmodyfikować wszystkie ustawienia, kliknij przycisk **Wstecz**.
14. Wybierz przycisk **Utwórz**.

## <a name="next-steps"></a>Następne kroki

* [Utwórz chmurę prywatną](create-private-cloud.md)
