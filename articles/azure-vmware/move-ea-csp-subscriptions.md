---
title: Przenoszenie subskrypcji rozwiązań VMware platformy Azure dla umowy EA i dostawcy CSP
description: Dowiedz się, jak przenieść chmurę prywatną z jednej subskrypcji do innej. Przenoszenie można wykonać z różnych powodów, takich jak rozliczenia.
ms.topic: how-to
ms.date: 03/15/2021
ms.openlocfilehash: 608f46dbd84d6bb899a3e7fcd1f8a63b3a5e85fb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103555615"
---
# <a name="move-ea-and-csp-azure-vmware-solution-subscriptions"></a>Przenoszenie subskrypcji rozwiązań VMware platformy Azure dla umowy EA i dostawcy CSP

W tym artykule dowiesz się, jak przenieść chmurę prywatną z jednej subskrypcji do innej. Przenoszenie można wykonać z różnych powodów, takich jak rozliczenia. 

>[!IMPORTANT]
>Należy mieć co najmniej uprawnienia współautora zarówno w przypadku subskrypcji źródłowej, jak i docelowej. Nie można przenieść bramy sieci wirtualnej i sieci wirtualnej z jednej subskrypcji do innej. Ponadto przeniesienie subskrypcji nie ma wpływu na zarządzanie i obciążenia, takie jak maszyny wirtualne vCenter, NSX i obciążenia.

1. Zaloguj się do Azure Portal i wybierz chmurę prywatną, którą chcesz przenieść.

1. Wybierz łącze **subskrypcja (Zmień)** .

   :::image type="content" source="media/private-cloud-overview-subscription-id.png" alt-text="Zrzut ekranu przedstawiający szczegóły chmury prywatnej.":::

1. Podaj szczegóły subskrypcji dla **elementu docelowego** i wybierz pozycję **dalej**.

   :::image type="content" source="media/move-resources-subscription-target.png" alt-text="Zrzut ekranu zasobu docelowego." lightbox="media/move-resources-subscription-target.png":::

1. Potwierdź sprawdzenie poprawności wybranych zasobów do przeniesienia i wybierz pozycję **dalej**. 

   :::image type="content" source="media/confirm-move-resources-subscription-target.png" alt-text="Zrzut ekranu przedstawiający przenoszony zasób." lightbox="media/confirm-move-resources-subscription-target.png":::

1. Zaznacz to pole wyboru, aby zrozumieć, że skojarzone narzędzia i skrypty nie będą działały, dopóki nie zaktualizujesz ich do używania nowych identyfikatorów zasobów. Następnie wybierz pozycję **Przenieś**.

   :::image type="content" source="media/review-move-resources-subscription-target.png" alt-text="Zrzut ekranu przedstawiający podsumowanie wybranego zasobu, który jest przenoszony. " lightbox="media/review-move-resources-subscription-target.png":::

   Po zakończeniu przenoszenia zasobów pojawia się powiadomienie. Nowa subskrypcja zostanie wyświetlona w Omówienie chmury prywatnej.

   :::image type="content" source="media/moved-subscription-target.png" alt-text="Zrzut ekranu przedstawiający nową subskrypcję." lightbox="media/moved-subscription-target.png":::

