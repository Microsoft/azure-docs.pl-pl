---
title: Aktualizowanie istniejącej oferty kontenerów platformy Azure | Portal Azure Marketplace
description: Jak zaktualizować istniejącą ofertę kontenera w witrynie Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: cc378dcc1d5f777f4e81825e2f99dedd37a87f96
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82143822"
---
# <a name="update-an-existing-container-offer"></a>Aktualizowanie istniejącej oferty kontenera

> [!IMPORTANT]
> Od 13 kwietnia 2020 rozpocznie się zarządzanie ofertami kontenerów platformy Azure w centrum partnerskim. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [Tworzenie oferty kontenera platformy Azure](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer) , aby zarządzać migrowanymi ofertami.

W tym artykule opisano różne aspekty aktualizowania oferty kontenera w [Portal Cloud partner](https://cloudpartner.azure.com/).

Istnieje kilka powodów, dla których warto zaktualizować ofertę, na przykład:

-  Dodawanie nowej wersji obrazu kontenera do istniejących jednostek SKU.
-  Dodawanie nowych jednostek SKU.
-  Aktualizowanie metadanych witryny Marketplace dla oferty lub poszczególnych jednostek SKU.

Aby ułatwić Ci modyfikację, Portal udostępnia funkcje **porównania** i **historii** .  


## <a name="unpermitted-changes-to-a-container-offer-or-sku"></a>Niedozwolone zmiany w ofercie kontenera lub jednostce SKU

Istnieją atrybuty oferty kontenera lub jednostki SKU, których nie można zmienić po zakończeniu oferty w witrynie Azure Marketplace. Nie można zmienić następujących ustawień:

-  **Identyfikator oferty** i **identyfikator wydawcy** oferty
-  **Identyfikator jednostki SKU** istniejących jednostek SKU
-  Tagi wersji, na przykład:`1.0.1`
-  Zmiany modelu rozliczeń/licencji na istniejące jednostki SKU

## <a name="common-update-operations"></a>Typowe operacje aktualizacji

Następujące operacje aktualizacji są wspólne.

### <a name="update-container-image-version-for-a-sku"></a>Aktualizowanie wersji obrazu kontenera dla jednostki SKU

Często obraz kontenera jest okresowo aktualizowany za pomocą poprawek zabezpieczeń, dodatkowych funkcji i tak dalej. W tym scenariuszu należy zaktualizować obraz kontenera, do którego odwołuje się jednostka SKU, wykonując następujące czynności:

1. Zaloguj się do [Portal Cloud partner](https://cloudpartner.azure.com/).
2. W obszarze **wszystkie oferty**Znajdź ofertę, którą chcesz zaktualizować.
3. Na karcie **jednostki SKU** wybierz jednostkę SKU skojarzoną z obrazem kontenera do zaktualizowania.
4. W obszarze **obraz kontenera**wybierz pozycję **+ Nowa wersja obrazu** , aby dodać nowy obraz kontenera.
5. Podaj nowe **wersje obrazów**kontenerów. Wersja obrazu musi być zgodna z tymi samymi wskazówkami dotyczącymi tagów, co w poprzednich wersjach. Tagi wersji powinny mieć postać X. Y. Z, gdzie X, Y i Z są liczbami całkowitymi. Sprawdź, czy wprowadzona nowa wersja jest nowsza niż wszystkie poprzednie wersje.
6. Wybierz pozycję **Publikuj** , aby uruchomić przepływ pracy w celu opublikowania nowej wersji obrazu kontenera w witrynie Azure Marketplace.

### <a name="add-a-new-sku"></a>Dodaj nową jednostkę SKU

Wykonaj następujące kroki, aby udostępnić nową jednostkę SKU dla oferty:

1. Zaloguj się do [Portal Cloud partner](https://cloudpartner.azure.com/).
2. W obszarze **wszystkie oferty**Znajdź ofertę, którą chcesz zaktualizować.
3. Na karcie **jednostki SKU** wybierz pozycję **Dodaj nową jednostkę SKU** i podaj **Identyfikator jednostki SKU** w oknie podręcznym.
4. Opublikuj ponownie kontener przy użyciu kroków opisanych w [sekcji Publikowanie kontenera](./cpp-publish-offer.md).
5. Wybierz pozycję **Publikuj** , aby uruchomić przepływ pracy w celu opublikowania nowej jednostki SKU.

### <a name="update-offer-marketplace-metadata"></a>Aktualizowanie metadanych witryny Marketplace z ofertą

Wykonaj następujące kroki, aby zaktualizować metadane portalu Marketplace skojarzone z ofertą. (Na przykład: Nazwa firmy, logo i itp.)

1. Zaloguj się do [Portal Cloud partner](https://cloudpartner.azure.com/).
2. W obszarze **wszystkie oferty**Znajdź ofertę, którą chcesz zaktualizować.
3. Przejdź do karty **Marketplace** . Aby wprowadzić zmiany metadanych, Skorzystaj z instrukcji zawartych w artykule [Publikowanie oferty publikacji](./cpp-publish-offer.md) .
4. Wybierz pozycję **Publikuj** , aby uruchomić przepływ pracy w celu opublikowania zmian.

## <a name="compare-feature"></a>Porównaj funkcję

Po wprowadzeniu zmian w opublikowanej ofercie można użyć funkcji **porównania** , aby przeprowadzić inspekcję wprowadzonych zmian.

### <a name="to-use-the-compare-feature"></a>Aby użyć funkcji porównania:

1. W dowolnym momencie procesu edycji wybierz pozycję Porównaj z ofertą.
2. Zobacz obok siebie wersje zasobów i metadanych marketingowych.


## <a name="history-of-publishing-actions"></a>Historia akcji publikowania

Aby wyświetlić historyczne działanie publikowania, wybierz kartę **historia** na pasku menu nawigacji po lewej stronie Portal Cloud partner. Możesz zobaczyć działania z sygnaturami czasowymi podejmowane w okresie istnienia ofert portalu Azure Marketplace.
