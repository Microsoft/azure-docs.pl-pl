---
title: Obsługa wdrażania rozwiązań VMware platformy Azure lub niepowodzenia aprowizacji
description: Uzyskaj informacje z chmury prywatnej rozwiązań VMware platformy Azure, aby wysłać żądanie obsługi wdrożenia rozwiązania VMware lub błędu aprowizacji.
ms.topic: how-to
ms.date: 10/28/2020
ms.openlocfilehash: 27b645f4ca225fdd74bca6499b6581b3803e41a4
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542409"
---
# <a name="open-a-support-request-for-an-azure-vmware-solution-deployment-or-provisioning-failure"></a>Otwórz żądanie obsługi dla wdrożenia rozwiązania Azure VMware lub niepowodzenia aprowizacji

W tym artykule pokazano, jak otworzyć [żądanie obsługi](https://rc.portal.azure.com/#create/Microsoft.Support) i podać kluczowe informacje dotyczące wdrożenia rozwiązania VMware w ramach platformy Azure lub niepowodzenia aprowizacji. 

W przypadku wystąpienia błędu w chmurze prywatnej należy otworzyć żądanie pomocy technicznej w Azure Portal. Aby otworzyć żądanie pomocy technicznej, najpierw Uzyskaj pewne informacje o kluczu w Azure Portal:

- Identyfikator korelacji
- Identyfikator obwodu usługi Azure ExpressRoute
- Komunikaty o błędach

## <a name="get-the-correlation-id"></a>Pobierz identyfikator korelacji
 
Podczas tworzenia chmury prywatnej lub dowolnego zasobu na platformie Azure identyfikator korelacji dla zasobu jest generowany automatycznie dla zasobu. Dołącz identyfikator korelacji chmury prywatnej do żądania obsługi, aby szybciej otworzyć i rozwiązać żądanie.

W Azure Portal można uzyskać identyfikator korelacji dla zasobu na dwa sposoby:

* Okienko **Przegląd**
* Dzienniki wdrożenia
 
 ### <a name="get-the-correlation-id-from-the-resource-overview"></a>Pobieranie identyfikatora korelacji z przeglądu zasobów

Oto przykładowe szczegóły operacji wdrożenia chmury prywatnej zakończonej niepowodzeniem z wybranym IDENTYFIKATORem korelacji:

:::image type="content" source="media/fix-deployment-provisioning-failures/failed-private-cloud-deployment.png" alt-text="Zrzut ekranu przedstawiający niepowodzenie wdrożenia chmury prywatnej z wybranym IDENTYFIKATORem korelacji.":::

Aby uzyskać dostęp do wyników wdrożenia w okienku **przeglądu** chmury prywatnej:

1. W Azure Portal wybierz swoją chmurę prywatną.

1. W menu po lewej stronie wybierz pozycję **Przegląd**.

Po zainicjowaniu wdrożenia wyniki wdrożenia są wyświetlane w okienku **Przegląd** chmury prywatnej.

Skopiuj i Zapisz identyfikator korelacji wdrożenia chmury prywatnej do uwzględnienia w żądaniu obsługi.

### <a name="get-the-correlation-id-from-the-deployment-log"></a>Pobierz identyfikator korelacji z dziennika wdrożenia

Identyfikator korelacji dla niepowodzenia wdrożenia można uzyskać, przeszukując dziennik aktywności wdrożenia w Azure Portal.

Aby uzyskać dostęp do dziennika wdrożenia:

1. W Azure Portal Wybierz chmurę prywatną, a następnie wybierz ikonę powiadomienia.

   :::image type="content" source="media/fix-deployment-provisioning-failures/open-notifications.png" alt-text="Zrzut ekranu pokazujący ikonę powiadomienia w Azure Portal.":::

1. W okienku **powiadomienia** wybierz pozycję **więcej zdarzeń w dzienniku aktywności** :

    :::image type="content" source="media/fix-deployment-provisioning-failures/more-events-in-activity-log.png" alt-text="Zrzut ekranu pokazujący więcej zdarzeń w linku dziennika aktywności wybranym w okienku powiadomienia.":::

1. Aby znaleźć nieudane wdrożenie i jego identyfikator korelacji, wyszukaj nazwę zasobu lub inne informacje, które zostały użyte podczas tworzenia zasobu. 

    Poniższy przykład przedstawia wyniki wyszukiwania dla zasobu chmury prywatnej o nazwie pc03.
 
    :::image type="content" source="media/fix-deployment-provisioning-failures/find-past-deployments.png" alt-text="Zrzut ekranu pokazujący wyniki wyszukiwania przykładowego zasobu chmury prywatnej i okienka Tworzenie lub aktualizowanie PrivateCloud.":::
 
1. W wynikach wyszukiwania w okienku **Dziennik aktywności** wybierz nazwę operacji wdrożenia zakończonego niepowodzeniem.

1. W okienku **Utwórz lub zaktualizuj PrivateCloud** wybierz kartę **JSON** , a następnie wyszukaj `correlationId` w wyświetlonym dzienniku. Skopiuj `correlationId` wartość, aby dołączyć ją do żądania obsługi. 
 
## <a name="copy-error-messages"></a>Kopiuj komunikaty o błędach

Aby pomóc w rozwiązaniu problemu z wdrożeniem, Dołącz wszystkie komunikaty o błędach, które są wyświetlane w Azure Portal. Wybierz komunikat ostrzegawczy, aby wyświetlić podsumowanie błędów:
 
:::image type="content" source="media/fix-deployment-provisioning-failures/summary-of-errors.png" alt-text="Zrzut ekranu pokazujący szczegóły błędu na karcie Podsumowanie w okienku błędy z wybraną ikoną kopiowania.":::

Aby skopiować komunikat o błędzie, wybierz ikonę kopiowania. Zapisz skopiowaną wiadomość, aby dołączyć ją do żądania obsługi.
 
## <a name="get-the-expressroute-id-uri"></a>Pobierz identyfikator ExpressRoute (URI)
 
Być może próbujesz skalować istniejącą chmurę prywatną w ramach obwodu usługi Private Cloud ExpressRoute lub połączyć ją w równorzędny sposób. W tym scenariuszu potrzebny jest identyfikator ExpressRoute do dołączenia do żądania obsługi.

Aby skopiować identyfikator ExpressRoute:

1. W Azure Portal wybierz swoją chmurę prywatną.
1. W menu po lewej stronie w obszarze **Zarządzaj** wybierz pozycję **łączność**. 
1. W okienku po prawej stronie wybierz kartę **ExpressRoute** .
1. Wybierz ikonę kopiowania dla **identyfikatora ExpressRoute** i Zapisz wartość do użycia w żądaniu obsługi.
 
:::image type="content" source="media/fix-deployment-provisioning-failures/expressroute-id.png" alt-text="Skopiuj identyfikator ExpressRoute do Schowka."::: 
 
## <a name="pre-validation-failures"></a>Błędy przed walidacją

Jeśli sprawdzenie przed weryfikacją w chmurze prywatnej nie powiodło się (przed wdrożeniem), identyfikator korelacji nie zostanie wygenerowany. W tym scenariuszu w żądaniu pomocy technicznej można podać następujące informacje:

- Komunikaty o błędach i niepowodzeniu. Te komunikaty mogą być przydatne w wielu błędach, na przykład w przypadku problemów związanych z limitem przydziału. Ważne jest, aby skopiować te komunikaty i dołączyć je do żądania pomocy technicznej zgodnie z opisem w tym artykule.
- Informacje używane do tworzenia chmury prywatnej rozwiązania Azure VMware, w tym:
  - Lokalizacja
  - Grupa zasobów
  - Nazwa zasobu

## <a name="create-your-support-request"></a>Utwórz żądanie pomocy technicznej

Aby uzyskać ogólne informacje na temat tworzenia żądania pomocy technicznej, zobacz [jak utworzyć żądanie pomocy technicznej platformy Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). 

Aby utworzyć żądanie pomocy technicznej dotyczące wdrożenia rozwiązania Azure VMware lub niepowodzenia aprowizacji:

1. W Azure Portal wybierz ikonę **pomocy** , a następnie wybierz pozycję **nowe żądanie obsługi**.

    :::image type="content" source="media/fix-deployment-provisioning-failures/open-sr-on-avs.png" alt-text="Zrzut ekranu przedstawiający okienko nowe żądanie obsługi w Azure Portal.":::

1. Wprowadź lub wybierz wymagane informacje:

   1. Na karcie **Ustawienia podstawowe** :

      1. W obszarze **typ problemu** wybierz pozycję **Konfiguracja i problemy z instalacją**.

      1. W przypadku **problemu z podtypem** wybierz opcję **Zainicjuj obsługę chmury prywatnej**.

   1. Na karcie **szczegóły** :

      1. Wprowadź lub wybierz wymagane informacje.

      1. Wklej identyfikator korelacji lub identyfikator ExpressRoute, w którym są wymagane te informacje. Jeśli nie widzisz określonych pól tekstowych dla tych wartości, wklej je w polu tekstowym **Podaj szczegóły dotyczące problemu** .

    1. Wklej wszystkie szczegóły błędów, w tym błędy lub komunikaty o niepowodzeniu, które zostały skopiowane, w polu tekstowym **Podaj szczegóły dotyczące problemu** .

1. Przejrzyj wpisy, a następnie wybierz pozycję **Utwórz** , aby utworzyć żądanie pomocy technicznej.
