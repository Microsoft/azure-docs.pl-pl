---
title: Uzyskaj pomoc dotyczącą wdrażania rozwiązań VMware platformy Azure lub niepowodzeń aprowizacji
description: Jak uzyskać potrzebne informacje z chmury prywatnej rozwiązania VMware platformy Azure, aby wysłać żądanie obsługi dla niepowodzeń wdrażania lub aprowizacji rozwiązań VMware platformy Azure.
ms.topic: how-to
ms.date: 06/09/2020
ms.openlocfilehash: 1f46dde895db417fd2b488a6203d5482e73d3c5e
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92779496"
---
# <a name="get-help-with-azure-vmware-solution-deployment-or-provisioning-failures"></a>Uzyskaj pomoc dotyczącą wdrażania rozwiązań VMware platformy Azure lub niepowodzeń aprowizacji

Ten artykuł pomaga w rozwiązywaniu problemów z wdrażaniem rozwiązań VMware platformy Azure i inicjowaniem obsługi administracyjnej. Jeśli masz błędy w chmurze prywatnej, musisz otworzyć [żądanie obsługi](https://rc.portal.azure.com/#create/Microsoft.Support) (SR) w Azure Portal. 

Najpierw należy zebrać pewne kluczowe informacje w Azure Portal:

- Identyfikator korelacji
- Identyfikator obwodu ExpressRoute

## <a name="collect-the-correlation-id"></a>Zbierz identyfikator korelacji
 
Identyfikator korelacji jest generowany podczas tworzenia chmury prywatnej lub dowolnego zasobu na platformie Azure. Każde wdrożenie Azure Resource Manager generuje również identyfikator korelacji. Ten identyfikator umożliwia szybsze tworzenie i rozwiązywanie SR. 
 
Oto przykład danych wyjściowych z niepowodzenia wdrożenia chmury prywatnej z wyróżnionym IDENTYFIKATORem korelacji.

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="Wdrożenie chmury prywatnej z IDENTYFIKATORem korelacji nie powiodło się.":::

Skopiuj i Zapisz ten identyfikator korelacji, aby uwzględnić go w żądaniu obsługi. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie żądania obsługi](#create-your-support-request) na końcu tego artykułu.

Jeśli błąd wystąpił w etapach weryfikacji wstępnej, nie zostanie wygenerowany identyfikator korelacji. W takim przypadku można podać informacje używane podczas tworzenia chmury prywatnej rozwiązania Azure VMware, w tym:

- Lokalizacja
- Grupa zasobów
- Nazwa zasobu
 
### <a name="collect-a-summary-of-errors"></a>Zbierz podsumowanie błędów

Szczegóły wszystkich błędów mogą również pomóc w rozwiązaniu problemu. Na poprzednim ekranie wybierz komunikat ostrzegawczy, aby wyświetlić podsumowanie błędów.
 
 :::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="Wdrożenie chmury prywatnej z IDENTYFIKATORem korelacji nie powiodło się.":::

Ponownie skopiuj i Zapisz to podsumowanie, aby uwzględnić je w funkcji SR.
 
### <a name="retrieve-past-deployments"></a>Pobierz wcześniejsze wdrożenia

W przypadku wcześniejszych wdrożeń, w tym niepowodzenia, można wyszukać w dzienniku aktywności wdrożenia dostęp do programu, wybierając ikonę powiadomienia.

:::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="Wdrożenie chmury prywatnej z IDENTYFIKATORem korelacji nie powiodło się.":::

W obszarze powiadomienia wybierz pozycję **więcej zdarzeń w dzienniku aktywności** .

:::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="Wdrożenie chmury prywatnej z IDENTYFIKATORem korelacji nie powiodło się.":::

Następnie wyszukaj nazwę zasobu lub innych informacji użytych do utworzenia zasobu, aby znaleźć nieudane wdrożenie i jego identyfikator korelacji. Poniższy przykład przedstawia wyniki wyszukiwania w zasobie chmury prywatnej (pc03).
 
:::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="Wdrożenie chmury prywatnej z IDENTYFIKATORem korelacji nie powiodło się.":::
 
Wybranie nazwy operacji wdrożenia zakończonego niepowodzeniem spowoduje otwarcie okna ze szczegółowymi informacjami. Wybierz kartę JSON i poszukaj korelacji. Skopiuj i Dołącz do funkcji SR. 
 
## <a name="collect-the-expressroute-id-uri"></a>Zbierz identyfikator ExpressRoute (URI)
 
Być może próbujesz skalować istniejącą chmurę prywatną w ramach obwodu usługi Private Cloud ExpressRoute lub połączyć ją w równorzędny sposób. W takim przypadku potrzebny będzie identyfikator ExpressRoute. 

W Azure Portal wybierz pozycję **łączność > ExpressRoute** i skopiuj **Identyfikator ExpressRoute** do Schowka.
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="Wdrożenie chmury prywatnej z IDENTYFIKATORem korelacji nie powiodło się."::: 
 
> [!NOTE]
> W przypadku sprawdzenia przed walidacją mogą zakończyć się niepowodzeniem przed wdrożeniem, a jedyne dostępne informacje będą komunikatami o błędach i błędach. Mogą one być przydatne w wielu awariach, na przykład w przypadku problemów związanych z limitem przydziału i są ważne do uwzględnienia tych komunikatów w żądaniu obsługi. Aby zebrać te dane, zobacz wcześniejszą sekcję, [zbieranie informacji o błędach](#collect-a-summary-of-errors).

## <a name="create-your-support-request"></a>Utwórz żądanie pomocy technicznej

Ogólne wskazówki dotyczące tworzenia żądania pomocy technicznej można znaleźć w temacie [jak utworzyć żądanie pomocy technicznej platformy Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). 

Poniżej przedstawiono szczegółowe wskazówki dotyczące tworzenia rozwiązania SR dla systemu Azure VMware lub niepowodzeń aprowizacji.

1. Wybierz ikonę **pomocy** , a następnie **+ nowe żądanie obsługi** .

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="Wdrożenie chmury prywatnej z IDENTYFIKATORem korelacji nie powiodło się.":::

2. Wypełnij wszystkie wymagane pola i na karcie **podstawowe** :

    - W obszarze **typ problemu** wybierz pozycję **Konfiguracja i problemy z instalacją** .

    - W przypadku **problemu z podtypem** wybierz opcję **Zainicjuj obsługę chmury prywatnej** .

3. Na karcie **szczegóły** :

    - Wypełnij wszystkie wymagane pola.

    - Wklej identyfikator korelacji lub identyfikator ExpressRoute do określonych pól. Jeśli nie widzisz konkretnego pola, możesz je wkleić w polu tekstowym w obszarze **Podaj szczegóły dotyczące problemu.**

    - Wklej wszystkie szczegóły błędów, w tym podsumowanie błędów, które zostały skopiowane, w polu tekstowym w obszarze **Podaj szczegóły dotyczące problemu.**

4. Przejrzyj i wybierz pozycję **Utwórz** , aby utworzyć wirtualizację SR.
