---
title: Aktualizowanie istniejącej oferty maszyny wirtualnej w portalu Azure Marketplace
description: Wyjaśnia, jak zaktualizować istniejącą ofertę maszyny wirtualnej w witrynie Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/27/2018
ms.author: dsindona
ms.openlocfilehash: 934be24d07c01c76c8caf5e16af4b765df79c964
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82142920"
---
# <a name="update-an-existing-vm-offer-on-azure-marketplace"></a>Aktualizowanie istniejącej oferty maszyny wirtualnej w witrynie Azure Marketplace

> [!IMPORTANT]
> Od 13 kwietnia 2020 rozpocznie się zarządzanie ofertami usługi Azure Virtual Machine w centrum partnerskim. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [Tworzenie oferty maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer) w celu zarządzania zmigrowanymi ofertami.

W tym artykule omówiono różne aspekty aktualizowania oferty maszyny wirtualnej (VM) w [Portal Cloud partner](https://cloudpartner.azure.com/) a następnie ponowne opublikowanie oferty.

Istnieje kilka commonplace powodów, dla których możesz zaktualizować swoją ofertę, w tym:

-  Dodaj nową wersję obrazu maszyny wirtualnej do istniejących jednostek SKU
-  Zmiany regionów, w których jest dostępna jednostka SKU
-  Dodaj nowe jednostki SKU
-  Aktualizowanie metadanych witryny Marketplace dla oferty lub poszczególnych jednostek SKU
-  Aktualizowanie cen dla ofert z opcją płatność zgodnie z rzeczywistym użyciem

Aby ułatwić Ci modyfikację, Portal oferuje funkcje **porównywania** i **historii** .  

>[!Note]
>Niedostępność kanału partnera dostawcy rozwiązań w chmurze (CSP) jest teraz dostępna.  Zobacz [dostawcy rozwiązań w chmurze](../../cloud-solution-providers.md) , aby uzyskać więcej informacji na temat marketingu oferty przez kanały partnerskie programu Microsoft CSP.

## <a name="unpermitted-changes-to-vm-offer-or-sku"></a>Niedozwolone zmiany w ofercie maszyny wirtualnej lub jednostce SKU

Istnieją pewne atrybuty oferty lub jednostki SKU maszyny wirtualnej, które nie mogą być modyfikowane, gdy oferta znajduje się w portalu Azure Marketplace, głównie:

-  **Identyfikator oferty** i **identyfikator wydawcy** oferty
-  **Identyfikator jednostki SKU** istniejących jednostek SKU
-  Liczba dysków danych istniejących jednostek SKU
-  Zmiany modelu rozliczeń/licencji na istniejące jednostki SKU
-  Cena wzrasta do opublikowanej jednostki SKU


## <a name="common-update-operations"></a>Typowe operacje aktualizacji

Chociaż istnieje szeroki zakres cech, które można zmienić w ramach oferty maszyny wirtualnej, typowe są następujące operacje.

### <a name="update-the-vm-image-version-for-a-sku"></a>Aktualizowanie wersji obrazu maszyny wirtualnej dla jednostki SKU

Jest ona często aktualizowana w celu okresowego aktualizowania obrazu maszyny wirtualnej za pomocą poprawek zabezpieczeń, dodatkowych funkcji i tak dalej.  W takich scenariuszach należy zaktualizować obraz maszyny wirtualnej, do którego odwołuje się jednostka SKU, wykonując następujące czynności:

1.  Zaloguj się do [Portal Cloud partner](https://cloudpartner.azure.com/).

2.  W obszarze **wszystkie oferty**Znajdź ofertę do zaktualizowania.

3.  Na karcie **jednostki SKU** kliknij jednostkę SKU skojarzoną z obrazem maszyny wirtualnej do zaktualizowania.

4.  W obszarze **wersja dysku**kliknij pozycję **+ nowy dysk** , aby dodać nowy obraz maszyny wirtualnej.

5.  Podaj nowe **wersje dysku**obrazów maszyn wirtualnych. Wersja dysku musi być zgodna z formatem [wersji semantycznej](https://semver.org/) . Wersje powinny mieć postać X. Y. Z, gdzie X, Y i Z są liczbami całkowitymi. Sprawdź, czy wprowadzona nowa wersja jest nowsza niż wszystkie poprzednie wersje; w przeciwnym razie po ponownym opublikowaniu nowej wersji nie będzie ona wyświetlana w portalu ani w witrynie Azure Marketplace.

6.  W polu **adres URL dysku VHD systemu**operacyjnego wprowadź [Identyfikator URI sygnatury dostępu współdzielonego](./cpp-get-sas-uri.md) utworzony dla wirtualnego dysku twardego systemu operacyjnego. 

    > [!WARNING] 
    > Nie można zmienić liczby dysków danych między różnymi wersjami jednostki SKU. Jeśli w poprzednich wersjach skonfigurowano dyski danych, ta nowa wersja musi mieć taką samą liczbę dysków danych.

7.  Kliknij pozycję **Publikuj** , aby uruchomić przepływ pracy w celu opublikowania nowej wersji maszyny wirtualnej w portalu Azure Marketplace.


### <a name="change-region-availability-of-a-sku"></a>Zmienianie dostępności regionu jednostki SKU

W miarę upływu czasu możesz chcieć, aby oferta/jednostka SKU była dostępna w większej liczbie regionów.  Alternatywnie możesz chcieć zatrzymać obsługę oferty/jednostki SKU w danym regionie.
Aby zmodyfikować dostępność, wykonaj następujące czynności:

1.  Zaloguj się do [Portal Cloud partner](https://cloudpartner.azure.com/).

2.  W obszarze **wszystkie oferty** Znajdź ofertę, którą chcesz zaktualizować.

3.  Na karcie **jednostki SKU** kliknij jednostkę SKU, która ma zostać zmodyfikowana.

4.  Kliknij przycisk **Wybierz kraje** w obszarze pola **dostępność kraju/regionu** .

5.  W oknie podręcznym dostępność regionu Dodaj lub Usuń regiony dla tej jednostki SKU.

6.  Kliknij pozycję **Publikuj** , aby rozpocząć publikowanie przepływu pracy w celu zaktualizowania dostępności regionu jednostek SKU.

Jeśli jednostka SKU jest dostępna w nowym regionie, będziesz mieć możliwość określenia cen dla danego regionu za pośrednictwem funkcji **Eksportuj dane cen** . Jeśli dodajesz region z tyłu, który był już dostępny wcześniej, nie będzie można zaktualizować jego cen, ponieważ zmiany cen nie są dozwolone.


### <a name="add-a-new-sku"></a>Dodaj nową jednostkę SKU

Wykonaj następujące kroki, aby udostępnić nową jednostkę SKU dla istniejącej oferty: 

1.  Zaloguj się do [Portal Cloud partner](https://cloudpartner.azure.com/).

2.  W obszarze **wszystkie oferty** Znajdź ofertę, którą chcesz zaktualizować.

3.  Na karcie **jednostki SKU** kliknij pozycję **Dodaj nową jednostkę SKU** i podaj **Identyfikator jednostki SKU** w oknie podręcznym.

4.  Opublikuj ponownie MASZYNę wirtualną zgodnie z opisem w artykule [Publikowanie maszyny wirtualnej w witrynie Azure Marketplace](./cpp-publish-offer.md).

5.  Kliknij pozycję **Publikuj** , aby uruchomić przepływ pracy w celu opublikowania nowej jednostki SKU.


### <a name="update-offer-marketplace-metadata"></a>Aktualizowanie metadanych witryny Marketplace z ofertą

Wykonaj następujące kroki, aby zaktualizować metadane witryny Marketplace — nazwę firmy, logo, itp., skojarzone z ofertą: 

1.  Zaloguj się do [Portal Cloud partner](https://cloudpartner.azure.com/).

2.  W obszarze **wszystkie oferty** Znajdź ofertę, którą chcesz zaktualizować.

3.  Przejdź do karty **Marketplace** , a następnie postępuj zgodnie z instrukcjami w artykule [Publikowanie maszyny wirtualnej w witrynie Azure Marketplace](./cpp-publish-offer.md) , aby wprowadzić zmiany metadanych.

4.  Kliknij pozycję **Publikuj** , aby uruchomić przepływ pracy w celu opublikowania zmian.


### <a name="update-pricing-on-published-offers"></a>Aktualizowanie cen dla opublikowanych ofert

Po opublikowaniu oferty z opcją płatność zgodnie z rzeczywistym użyciem nie można bezpośrednio zwiększyć cen jednostki SKU.  (Można jednak utworzyć nową jednostkę SKU w ramach tej samej oferty, usunąć starą jednostkę SKU i ponownie opublikować ofertę dla nowych klientów).  Z kolei można obniżyć cenę opublikowanej oferty, wykonując następujące czynności:

1.  Zaloguj się do [Portal Cloud partner](https://cloudpartner.azure.com/).

2.  W obszarze **wszystkie oferty**Znajdź ofertę do zaktualizowania.

3.  Kliknij jednostkę SKU, dla której chcesz obniżyć Cennik.

4.  W przypadku ustawienia cen w graficznym interfejsie użytkownika 1x1 można zmienić cenę bezpośrednio w interfejsie użytkownika. Jeśli ustawisz Cennik za pośrednictwem arkusza kalkulacyjnego Import/Export, możesz obniżyć ceny tylko za pomocą funkcji Import/Export.

3.  Kliknij przycisk **Zapisz**.

4.  Kliknij pozycję **Publikuj** , aby uruchomić przepływ pracy w celu opublikowania zmian.

Nowe obniżone ceny będą widoczne dla nowych klientów znajdujących się na żywo w witrynie sieci Web.  Ta nowa cena wpłynie na klientów w następujący sposób:

- Nowi klienci będą obciążani tą nową stawką. 
- W przypadku istniejących klientów spadek cen zostanie odzwierciedlony z mocą wsteczną od początku okresu rozliczeniowego, w którym spadek cen stał się skuteczny.
Jeśli opłaty za cykl zostały już rozliczone w okresie, w którym nastąpiła spadek cen, otrzymają refundację w następnym okresie rozliczeniowym, aby uwzględnić obniżoną cenę.


<!-- TD: This has been implemented, need to change the SKU Tab topic to reflect and move this section there. -->
### <a name="simplified-currency-pricing"></a>Uproszczone ceny walutowe

Od września 1 2018 nową sekcję o nazwie **uproszczone ceny waluty** zostaną dodane do portalu. Firma Microsoft usprawnia działalność biznesową w witrynie Azure Marketplace, zapewniając bardziej przewidywalne ceny i kolekcje od klientów na całym świecie. Ta ulepszona oferta obejmie zmniejszenie liczby walut, w których klienci są rozliczani.

Nowa sekcja zostanie uwzględniona w nowych walutach.Po przeprowadzeniu migracji wszystkich klientów do tych nowych walut rozliczeń oryginalna sekcja cenowa zostanie wycofana, a w dalszej części cennika uproszczonej waluty pozostanie.

Do 1 listopada 2018 zostanie ustawiona nowa cena w regionach, w których jest zmieniana waluta rozliczenia. Nie będzie można zwiększyć cen dla regionów, w którym waluta rozliczenia nie ulega zmianie.

> [!NOTE] 
> Jeśli używasz interfejsów API do publikowania oferty, w kodzie JSON oferty może zostać wyświetlona nowa sekcja. W zależności od typu oferty można `virtualMachinePricingV2` dodać `monthlyPricingV2`adnotację jako lub. 

Jeśli masz jakieś pytania dotyczące tej zmiany, skontaktuj się z [pomocą techniczną platformy Azure Marketplace](../../support-azure-marketplace.md).


## <a name="compare-feature"></a>Porównaj funkcję

Po wprowadzeniu zmian w już opublikowanej ofercie można użyć funkcji **porównania** , aby przeprowadzić inspekcję wprowadzonych zmian. Aby użyć tej funkcji:

1.  W dowolnym momencie w procesie edycji kliknij przycisk **PORÓWNAJ** dla swojej oferty.

    ![Przycisk porównania funkcji](./media/publishvm_037.png)


2.  Wyświetl wersje zasobów i metadanych marketingowych obok siebie.


## <a name="history-of-publishing-actions"></a>Historia akcji publikowania

Aby wyświetlić historię działań związanych z publikowaniem historycznym, kliknij element **historia** na lewym pasku nawigacyjnym Portal Cloud partner. W tym miejscu będzie można wyświetlać działania z sygnaturami czasowymi, które zostały wykonane w okresie istnienia ofert portalu Azure Marketplace.  
<!-- TD: Add after section authored: For more information, see [History page](../portal-tour/cpp-history-page.md). -->

