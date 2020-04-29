---
title: Aktualizowanie ofert portalu Marketplace | Portal Azure Marketplace
description: Aktualizuj oferty na platformie Azure i AppSource Marketplace przy użyciu portal Cloud Partner
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: dsindona
ms.openlocfilehash: 101369a050770be3acd9534cef6229037fe1c366
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80288516"
---
# <a name="update-azure-marketplace-and-appsource-offers"></a>Aktualizowanie ofert Azure Marketplace i AppSource

Istnieją różne rodzaje aktualizacji, które można zastosować do oferty po jej opublikowaniu.  [Portal Cloud partner](https://cloudpartner.azure.com/) pomaga w prawidłowym modyfikowaniu atrybutów oferty, w tym:

-  Dodawanie nowego obrazu maszyny wirtualnej lub wersji pakietu do istniejącej jednostki SKU
-  Zmiany regionów, w których jest dostępna jednostka SKU
-  Dodawanie nowych jednostek SKU
-  Aktualizowanie metadanych witryny Marketplace dla ofert lub jednostek SKU 
-  Aktualizowanie cen w ofertach z opcją płatność zgodnie z rzeczywistym użyciem

Portal zawiera również funkcje, takie jak możliwość porównywania funkcji i wyświetlania historii funkcji oferty, które ułatwiają zarządzanie zmianami.  Po zmodyfikowaniu oferty lub jednostki SKU należy ją ponownie opublikować przed wprowadzeniem zmian na żywo.  W tym artykule omówiono różne aspekty aktualizowania oferty portalu Marketplace.

## <a name="unpermitted-changes-to-an-offersku"></a>Niedozwolone zmiany w ofercie/jednostce SKU

Istnieją pewne atrybuty oferty lub jednostki SKU, które nie mogą być modyfikowane po opublikowaniu jej w portalu Marketplace.  Odpowiednie pola są wyłączone na karcie **Edytor** portalu, na przykład:  

- Identyfikator oferty i identyfikator wydawcy
- IDENTYFIKATOR JEDNOSTKI SKU 
- Liczba dysków danych istniejących jednostek SKU
- Zmiany modelu rozliczeń/licencji istniejących jednostek SKU
- Tagi wersji, na przykład:`1.0.1`


## <a name="common-update-operations"></a>Typowe operacje aktualizacji

W poniższych sekcjach opisano sposób wykonywania niektórych operacji aktualizacji.  Te operacje nie są dostępne dla wszystkich typów ofert.  Musisz zalogować się do portal Cloud Partner, aby uruchomić dowolną z tych operacji.


### <a name="update-offer-contacts"></a>Aktualizuj kontakty oferty

Wykonaj poniższe kroki, aby zaktualizować kontakty pomocy technicznej dla swojej oferty.
1. Na stronie **wszystkie oferty** wybierz ofertę.
2. Wybierz kartę **kontakty** . Zaktualizuj kontakty.
3. Wybierz ikonę **Zapisz**.
4. Wybierz pozycję **Publikuj** , aby rozpocząć proces publikowania.


### <a name="change-regions-an-offer-or-sku-is-available-in"></a>Zmień regiony oferta lub jednostka SKU jest dostępna w programie

W miarę upływu czasu możesz chcieć, aby oferta/jednostka SKU była dostępna w większej liczbie regionów.
Alternatywnie możesz chcieć zatrzymać obsługę oferty/jednostki SKU w danym regionie.
Aby zaimplementować te zmiany, wykonaj następujące czynności.

1. Na stronie **wszystkie oferty** Znajdź ofertę, którą chcesz zaktualizować.

Oferty w witrynie Azure Marketplace:

1. Wybierz kartę **jednostki SKU** .  Wybierz jednostkę SKU do zmodyfikowania.
1. Kliknij przycisk **Wybierz kraje** w polu **dostępność kraju/regionu** .
1. W oknie dialogowym dostępność regionu Dodaj lub Usuń regiony dla tej jednostki SKU.

Oferty AppSource:

1. Wybierz kartę **szczegóły witryny sklepu** .
1. Obok etykiety **Obsługiwane kraje/regiony** kliknij pozycję **Obsługiwane kraje/regiony**. 
1. W oknie dialogowym Obsługiwane kraje/regiony Dodaj lub Usuń regiony dla tej oferty.

Dla każdej witryny Marketplace:

1. Kliknij przycisk **Opublikuj** , aby rozpocząć proces publikowania. 

Jeśli jednostka SKU jest dostępna w nowym regionie, istnieje możliwość określenia cen dla danego regionu za pośrednictwem funkcji **Eksportuj dane cenowe** . W przypadku dodawania wcześniej dostępnego regionu nie można zaktualizować jego cen, ponieważ zmiany cen są niedozwolone.


### <a name="add-a-new-sku"></a>Dodaj nową jednostkę SKU 

Aby udostępnić nową jednostkę SKU dla istniejącej oferty, wykonaj następujące czynności:

1. Na stronie **wszystkie oferty** Znajdź ofertę.
3. W formularzu **jednostki SKU** kliknij pozycję **Dodaj nową jednostkę SKU** i podaj **Identyfikator jednostki SKU** w oknie podręcznym.
4. Wykonaj pozostałe kroki opisane w temacie [Publikowanie oferty maszyny wirtualnej](../virtual-machine/cpp-publish-offer.md).
5. Kliknij przycisk **Opublikuj** , aby rozpocząć proces publikowania.


### <a name="update-offer-marketplace-assets"></a>Aktualizowanie zasobów witryny Marketplace z ofertą

Możesz mieć scenariusze, w których należy zaktualizować zasoby tekstowe i graficzne na rynku, takie jak logo firmy, opis oferty itp. Aby zaktualizować te zasoby, wykonaj następujące czynności.

1. Na stronie **wszystkie oferty** Znajdź swoją ofertę. 
2. Wybierz kartę **Marketplace** i postępuj zgodnie z instrukcjami wyświetlanymi na *karcie Marketplace* Twojej oferty.
3. Kliknij przycisk **Opublikuj** , aby rozpocząć proces publikowania.


### <a name="update-pricing-on-published-offers"></a>Aktualizowanie cen dla opublikowanych ofert

Po opublikowaniu oferty z opcją płatność zgodnie z rzeczywistym użyciem nie można zwiększyć ceny istniejącej jednostki SKU.  Zamiast tego należy utworzyć jednostkę SKU w ramach tej samej oferty, usunąć starą jednostkę SKU, a następnie ponownie opublikować ofertę. Możesz obniżyć cenę z wcześniej opublikowanych ofert. Aby zmniejszyć cenę oferty:

1. Wybierz jednostkę SKU, dla której chcesz obniżyć Cennik.
2. Niższą cenę należy ustawić zgodnie z pierwotnie używanym mechanizmem: bezpośrednio w interfejsie użytkownika portalu lub za pomocą arkusza importowania/eksportowania.
3. Kliknij przycisk **Zapisz**.
4. Kliknij przycisk **Opublikuj** , aby rozpocząć proces publikowania.

Cennik jest widoczny dla nowych klientów, gdy jest on aktywny w portalu Marketplace, a wszyscy nowi klienci będą obciążani nową obniżoną cenę.  W przypadku istniejących klientów spadek cen został odzwierciedlony z mocą wsteczną od początku okresu rozliczeniowego, w którym spadek cen stał się skuteczny.  Jeśli opłaty za cykl zostały już rozliczone w okresie, w którym nastąpiła spadek cen, otrzymają refundację w następnym okresie rozliczeniowym, aby uwzględnić obniżoną cenę.


## <a name="compare-feature"></a>Porównaj funkcję

Po wprowadzeniu zmian w opublikowanej ofercie można użyć funkcji *porównania* , aby przeprowadzić inspekcję zmian. Aby korzystać z tej funkcji:

1. W dowolnym momencie procesu edycji można kliknąć przycisk **PORÓWNAJ** na karcie **Edytor** oferty.
2. W oknie porównania są wyświetlane bezpośrednie wersje zapisanych zmian w tej ofercie w porównaniu do oferty portalu Marketplace. 

![Przycisk porównania oferty na karcie Edytor](./media/offer-compare-button.png)


## <a name="history-of-publishing-actions"></a>Historia akcji publikowania

Aby wyświetlić historyczne działanie publikowania, wybierz kartę **historia** na pasku menu po lewej stronie Portal Cloud partner.  Strona Historia zawiera elastyczne filtrowanie według kilku cech i obsługuje porządkowanie kolumn.  Każde zdarzenie publikowania ma sygnaturę czasową.  Aby uzyskać więcej informacji, zobacz [stronę Historia inspekcji](../portal-tour/cpp-history-page.md).


## <a name="next-steps"></a>Następne kroki

Możesz również użyć portal Cloud Partner, aby [usunąć opublikowaną jednostkę SKU lub ofertę](./cpp-delete-offer.md).
