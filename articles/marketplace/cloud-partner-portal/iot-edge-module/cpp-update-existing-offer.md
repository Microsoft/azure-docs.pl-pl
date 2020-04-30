---
title: Aktualizowanie istniejącej oferty modułu Azure IoT Edge | Portal Azure Marketplace
description: Jak zaktualizować istniejącą ofertę modułu IoT Edge w portalu Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: dsindona
ms.openlocfilehash: 17cce766f2d56766a9fcf260416d8bbf3e43d0c5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82142249"
---
# <a name="update-an-existing-iot-edge-module-offer"></a>Aktualizowanie istniejącej oferty modułu IoT Edge

>[!Important]
>Od 13 kwietnia 2020 rozpocznie się Zarządzanie ofertą modułu IoT Edge w centrum partnerskim. Po przeprowadzeniu migracji utworzysz oferty w centrum partnerskim i zarządzaj nimi. Postępuj zgodnie z instrukcjami w temacie [Create an IoT Edge module](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-iot-edge-module-creation) , aby zarządzać migrowanymi ofertami.

W tym artykule opisano różne aspekty aktualizowania oferty modułu IoT Edge w [Portal Cloud partner](https://cloudpartner.azure.com/) a następnie ponowne opublikowanie oferty.

Istnieje kilka powodów, dla których warto zaktualizować ofertę, na przykład:

-  Dodawanie nowej wersji obrazu modułu IoT Edge do istniejących jednostek SKU.
-  Dodawanie nowych jednostek SKU.
-  Aktualizowanie metadanych witryny Marketplace dla oferty lub poszczególnych jednostek SKU.

Aby ułatwić Ci modyfikację, Portal oferuje funkcje **porównywania** i **historii** .  


## <a name="unpermitted-changes-to-iot-edge-module-offer-or-sku"></a>Niedozwolone zmiany w ofercie modułu IoT Edge lub w jednostce SKU

Istnieją atrybuty oferty modułu IoT Edge lub jednostki SKU, których nie można zmienić po zakończeniu oferty w witrynie Azure Marketplace. Nie można zmienić następujących ustawień:

-  **Identyfikator oferty** i **identyfikator wydawcy** oferty
-  **Identyfikator jednostki SKU** istniejących jednostek SKU
-  Tagi wersji, na przykład:`1.0.1`
-  Zmiany modelu rozliczeń/licencji na istniejące jednostki SKU

## <a name="common-update-operations"></a>Typowe operacje aktualizacji

Następujące operacje aktualizacji są wspólne.

### <a name="update-the-iot-edge-module-image-version-for-a-sku"></a>Aktualizowanie wersji obrazu modułu IoT Edge dla jednostki SKU

Typowym sposobem aktualizowania obrazu modułu IoT Edge jest okresowe aktualizowanie poprawek zabezpieczeń, dodatkowych funkcji i tak dalej. W tym scenariuszu należy zaktualizować obraz modułu IoT Edge, do którego odwołuje się jednostka SKU, wykonując następujące czynności:

1.  Zaloguj się do [Portal Cloud partner](https://cloudpartner.azure.com/).

2.  W obszarze **wszystkie oferty**Znajdź ofertę, którą chcesz zaktualizować.

3.  Na karcie **jednostki SKU** wybierz jednostkę SKU skojarzoną z obrazem modułu IoT Edge, który ma zostać zaktualizowany.

4.  W obszarze **obraz modułu krawędzi**wybierz pozycję **+ Nowa wersja obrazu** , aby dodać nowy obraz modułu IoT Edge.

5.  Podaj nowe **wersje obrazu**modułu IoT Edge. Wersja obrazu musi być zgodna z tymi samymi wskazówkami dotyczącymi tagów, co w poprzednich wersjach. Tagi wersji powinny mieć postać X. Y. Z, gdzie X, Y i Z są liczbami całkowitymi. Sprawdź, czy wprowadzona nowa wersja jest nowsza niż wszystkie poprzednie wersje.

6.  Wybierz pozycję **Opublikuj** , aby uruchomić przepływ pracy w celu opublikowania nowej wersji modułu IoT Edge w portalu Azure Marketplace.

### <a name="add-a-new-sku"></a>Dodaj nową jednostkę SKU

Wykonaj następujące kroki, aby udostępnić nową jednostkę SKU dla oferty: 

1.  Zaloguj się do [Portal Cloud partner](https://cloudpartner.azure.com/).

2.  W obszarze **wszystkie oferty**Znajdź ofertę, którą chcesz zaktualizować.

3.  Na karcie **jednostki SKU** wybierz pozycję **Dodaj nową jednostkę SKU** i podaj **Identyfikator jednostki SKU** w oknie podręcznym.

4.  Opublikuj ponownie moduł IoT Edge, wykonując kroki opisane w sekcji [Publikowanie modułu IoT Edge w witrynie Azure Marketplace](./cpp-publish-offer.md).

5.  Wybierz pozycję **Publikuj** , aby uruchomić przepływ pracy w celu opublikowania nowej jednostki SKU.


### <a name="update-offer-marketplace-metadata"></a>Aktualizowanie metadanych witryny Marketplace z ofertą

Wykonaj następujące kroki, aby zaktualizować metadane portalu Marketplace skojarzone z ofertą. (Na przykład: Nazwa firmy, logo itp.)

1.  Zaloguj się do [Portal Cloud partner](https://cloudpartner.azure.com/).

2.  W obszarze **wszystkie oferty**Znajdź ofertę, którą chcesz zaktualizować.

3.  Przejdź do karty **Marketplace** . Skorzystaj z instrukcji przedstawionych w artykule [Publikowanie modułu IoT Edge w portalu Azure Marketplace](./cpp-publish-offer.md) , aby wprowadzić zmiany w metadanych.

4.  Wybierz pozycję **Publikuj** , aby uruchomić przepływ pracy w celu opublikowania zmian.

## <a name="compare-feature"></a>Porównaj funkcję

Po wprowadzeniu zmian w opublikowanej ofercie można użyć funkcji **porównania** , aby przeprowadzić inspekcję wprowadzonych zmian. 

**Aby użyć funkcji porównania:**

1.  W dowolnym momencie procesu edycji wybierz pozycję **PORÓWNAJ** z ofertą.

    ![Przycisk porównania funkcji](./media/iot-edge-module-compare.png)


2.  Zobacz obok siebie wersje zasobów i metadanych marketingowych.


## <a name="history-of-publishing-actions"></a>Historia akcji publikowania

Aby wyświetlić historyczne działanie publikowania, wybierz kartę **historia** na pasku menu nawigacji po lewej stronie Portal Cloud partner. Możesz zobaczyć działania z sygnaturami czasowymi podejmowane w okresie istnienia ofert portalu Azure Marketplace.  <!-- Need to find correct link here:  legal time windowsFor more information, see [History page](cpp-history-page.md) -->
