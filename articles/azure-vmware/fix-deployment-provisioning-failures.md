---
title: Uzyskaj pomoc dotyczącą wdrażania rozwiązań VMware platformy Azure lub niepowodzeń aprowizacji
description: Jak uzyskać potrzebne informacje z chmury prywatnej rozwiązania VMware platformy Azure, aby wysłać żądanie obsługi dla niepowodzeń wdrażania lub aprowizacji rozwiązań VMware platformy Azure.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 63d5440a9e2b15463e465e1d32762889508feca1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88752238"
---
# <a name="get-help-with-azure-vmware-solution-deployment-or-provisioning-failures"></a>Uzyskaj pomoc dotyczącą wdrażania rozwiązań VMware platformy Azure lub niepowodzeń aprowizacji

W tym artykule dowiesz się, jak uzyskać pomoc dotyczącą wdrażania rozwiązań VMware platformy Azure lub niepowodzeń aprowizacji w chmurze prywatnej, otwierając żądanie obsługi (SR) w Azure Portal. Najpierw należy zebrać pewne kluczowe informacje w Azure Portal. W większości przypadków potrzebujesz:

- Identyfikator korelacji (dla wdrożenia zakończonego niepowodzeniem)
- Identyfikator obwodu usługi ExpressRoute (podczas próby skalowania lub komunikacji równorzędnej istniejącej chmury prywatnej za pomocą obwodu prywatnego chmury ExpressRoute i niepowodzenia)

## <a name="collect-the-correlation-id"></a>Zbierz identyfikator korelacji
 
Najpierw przyjrzyjmy się IDENTYFIKATORowi korelacji. Podczas tworzenia chmury prywatnej (lub dowolnego zasobu na platformie Azure) jest generowany skojarzony identyfikator korelacji. Każde wdrożenie Azure Resource Manager generuje również unikatowy identyfikator korelacji. Ten identyfikator umożliwia szybsze tworzenie i rozwiązywanie SR. 
 
Oto przykład danych wyjściowych z niepowodzenia wdrożenia chmury prywatnej z wyróżnionym IDENTYFIKATORem korelacji.

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="Wdrożenie chmury prywatnej z IDENTYFIKATORem korelacji nie powiodło się.":::

Skopiuj i Zapisz ten identyfikator korelacji, aby uwzględnić go w żądaniu obsługi. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie żądania obsługi](#create-your-support-request) na końcu tego artykułu.

Jeśli błąd wystąpił w etapach weryfikacji wstępnej przed wdrożeniem chmury prywatnej, nie zostanie wygenerowany żaden identyfikator korelacji. W takim przypadku można po prostu podać informacje używane podczas tworzenia chmury prywatnej rozwiązania Azure VMware, w tym:

- Lokalizacja
- Grupa zasobów
- Nazwa zasobu
 
### <a name="collect-a-summary-of-errors"></a>Zbierz podsumowanie błędów

Szczegóły wszystkich błędów mogą być również pomocne w rozwiązaniu problemu. Na powyższym ekranie wybierz pozycję **kliknij tutaj, aby wyświetlić szczegóły** (wyróżnione) i wyświetlenie podsumowania błędów, jak pokazano na poniższym zrzucie ekranu.
 
 :::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="Wdrożenie chmury prywatnej z IDENTYFIKATORem korelacji nie powiodło się.":::

Ponownie skopiuj i Zapisz to podsumowanie, aby uwzględnić je w funkcji SR.
 
### <a name="retrieve-past-deployments"></a>Pobierz wcześniejsze wdrożenia

W przypadku wcześniejszych wdrożeń, w tym niepowodzenia, można wyszukać w dzienniku aktywności wdrożenia dostęp do programu, wybierając ikonę powiadomienia.

:::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="Wdrożenie chmury prywatnej z IDENTYFIKATORem korelacji nie powiodło się.":::

W obszarze powiadomienia wybierz pozycję **więcej zdarzeń w dzienniku aktywności**.

:::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="Wdrożenie chmury prywatnej z IDENTYFIKATORem korelacji nie powiodło się.":::

Następnie wyszukaj nazwę zasobu lub inny unikatowy element informacji użytych podczas tworzenia zasobu, aby znaleźć niepowodzenie wdrożenia i jego identyfikator korelacji. Poniższy przykład przedstawia wyniki wyszukiwania w zasobie chmury prywatnej (pc03).
 
:::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="Wdrożenie chmury prywatnej z IDENTYFIKATORem korelacji nie powiodło się.":::
 
Wybranie nazwy operacji wdrożenia zakończonego niepowodzeniem spowoduje otwarcie okna ze szczegółowymi informacjami. Wybierz kartę JSON i poszukaj korelacji. Skopiuj i Dołącz do funkcji SR. 
 
## <a name="collect-the-expressroute-id-uri"></a>Zbierz identyfikator ExpressRoute (URI)
 
Być może masz już chmurę prywatną i wystąpił błąd podczas próby skalowania jej lub komunikacji równorzędnej za pomocą obwodu usługi ExpressRoute w chmurze prywatnej. W takim przypadku Identyfikator ExpressRoute chmury prywatnej może służyć do identyfikowania go podczas tworzenia SR.

Podczas przeglądania chmury prywatnej w portalu wybierz pozycję **łączność > ExpressRoute** i skopiuj **Identyfikator ExpressRoute** do Schowka.
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="Wdrożenie chmury prywatnej z IDENTYFIKATORem korelacji nie powiodło się."::: 
 
Wklej identyfikator ExpressRoute do odpowiedniego pola w nowym żądaniu obsługi. Aby uzyskać więcej informacji, zobacz sekcję dotyczącą [tworzenia żądania pomocy technicznej](#create-your-support-request).
 
> [!NOTE]
> W przypadku sprawdzenia przed walidacją mogą się nie powieść przed wdrożeniem, a jedyne dostępne informacje będą komunikatami o błędach i/lub błędach. Mogą one być przydatne w wielu awariach, na przykład w przypadku problemów związanych z limitem przydziału i są ważne do uwzględnienia tych komunikatów w żądaniu obsługi. Aby zebrać te dane, zobacz wcześniejszą sekcję, [zbieranie informacji o błędach](#collect-a-summary-of-errors).

## <a name="create-your-support-request"></a>Utwórz żądanie pomocy technicznej

Ogólne wskazówki dotyczące tworzenia żądania pomocy technicznej można znaleźć w temacie [jak utworzyć żądanie pomocy technicznej platformy Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). 

Poniżej przedstawiono dodatkowe wskazówki dotyczące tworzenia rozwiązania SR dla systemu Azure VMware lub niepowodzeń aprowizacji.

1. Wybierz ikonę **pomocy** , a następnie **+ nowe żądanie obsługi**.

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="Wdrożenie chmury prywatnej z IDENTYFIKATORem korelacji nie powiodło się.":::

2. Wypełnij wszystkie wymagane pola i na karcie **podstawowe** :

    - W obszarze **typ problemu**wybierz pozycję **Konfiguracja i problemy z instalacją**.

    - W przypadku **problemu z podtypem**wybierz opcję **Zainicjuj obsługę chmury prywatnej**.

3. Na karcie **szczegóły** :

    - Wypełnij wszystkie wymagane pola.

    - Wklej identyfikator korelacji lub identyfikator ExpressRoute do określonych pól. Jeśli nie widzisz dla nich określonego pola, możesz je wkleić do pola tekstowego w obszarze **Podaj szczegóły problemu.**

    - Wklej wszystkie szczegóły błędów, w tym podsumowanie błędów, które zostały skopiowane, w polu tekstowym w obszarze **Podaj szczegóły dotyczące problemu.**

4. Przejrzyj i wybierz pozycję **Utwórz** , aby utworzyć wirtualizację SR.
