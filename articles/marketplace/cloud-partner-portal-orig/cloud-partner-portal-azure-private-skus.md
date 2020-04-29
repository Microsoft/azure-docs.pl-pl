---
title: Prywatne jednostki SKU i plany | Portal Azure Marketplace
description: Jak używać prywatnych jednostek SKU do zarządzania dostępnością oferty.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/15/2019
ms.author: dsindona
ms.openlocfilehash: ee3ab7be4d15b13a3c0bb014a3ca4d4096299b4c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80280392"
---
<a name="private-skus-and-plans"></a>Prywatne jednostki SKU i plany
============

Prywatne jednostki SKU umożliwiają ograniczenie dostępności jednostek SKU do określonych klientów. Gdy jednostka SKU jest oznaczona jako prywatna, nie jest dostępna w żadnym katalogu publicznym, w tym w witrynie [Azure Marketplace](https://azuremarketplace.microsoft.com) i [Azure Portal](https://portal.azure.com). Na Azure Portal tylko klienci z dostępem do jednostki SKU mogą ją zobaczyć. Ponadto również zostanie wyświetlony monit z prośbą o dostęp do ofert prywatnych.

>[!NOTE]
>Prywatne jednostki SKU muszą mieć nowe unikatowe identyfikatory jednostki SKU/planu, aby uniknąć konfliktów z publicznymi jednostkami SKU.

Prywatnych jednostek SKU można używać do obsługi następujących scenariuszy:

1.  Publikuj oprogramowanie, które ma być dostępne wyłącznie dla określonych klientów i nie jest publicznie dostępne.
2.  Publikowanie odmian oprogramowania publicznego według dostosowanej ceny dla określonych klientów.
3.  Publikowanie odmian oprogramowania publicznego przy użyciu dostosowanego opisu i warunków (za pośrednictwem nowej oferty).

Jeśli chcesz tylko zmienić cenę, możesz ponownie użyć dysków z innej jednostki SKU w tej samej ofercie. W przypadku prywatnych jednostek SKU nie trzeba ponownie przesyłać dysków między jednostkami SKU.

<a name="mark-a-sku-private"></a>Oznacz jednostkę SKU jako prywatną
---------------------

Aby oznaczyć jednostkę SKU jako prywatną, przełącz opcję z pytaniem, czy jednostka SKU jest prywatna:

![Oznacz jednostkę SKU jako prywatną](./media/cloud-partner-portal-publish-virtual-machine/markingskuprivate.png)

Można ponownie użyć dysków w innej jednostce SKU i zmodyfikować Cennik lub opis. Aby ponownie użyć dysków, wybierz pozycję **tak** jako odpowiedź na monit "czy ta jednostka SKU ponownie wykorzystuje obrazy z publicznej jednostki SKU".

Jeśli jednostka SKU jest oznaczona jako prywatna, a oferta ma inne jednostki SKU z dyskami z możliwością ponownego użycia, wymagane jest wskazanie, że jednostka SKU ponownie używa dysków z innej jednostki SKU. Należy również określić docelowych odbiorców dla prywatnej jednostki SKU.

>[!NOTE]
>Po opublikowaniu nie można przeprowadzić prywatnej jednostki SKU.

<a name="select-an-image"></a>Wybierz obraz
------------------

Możesz udostępnić nowe dyski dla prywatnej jednostki SKU lub użyć tych samych dysków już udostępnionych w innej jednostce SKU, modyfikując Cennik lub opis. Aby ponownie użyć dysków, wybierz pozycję **tak** jako odpowiedź na monit "czy ta jednostka SKU ponownie używa obrazu z publicznej jednostki SKU".

![Wskaż ponowne użycie obrazu](./media/cloud-partner-portal-publish-virtual-machine/selectimage1.png)

Po potwierdzeniu, że jednostka SKU ponownie używa obrazów, wybierz źródłową lub *podstawową* jednostkę SKU dla obrazów:

![Wybierz obraz](./media/cloud-partner-portal-publish-virtual-machine/selectimage2.png)

Po opublikowaniu oferty obrazy z wybranej jednostki SKU byłyby udostępniane w ramach prywatnego identyfikatora jednostki SKU przy użyciu niestandardowych stawek/warunków. Prywatna jednostka SKU będzie widoczna tylko dla docelowych odbiorców.

W przypadku aktualizacji obrazów wymagane jest tylko zaktualizowanie obrazu podstawowej jednostki SKU. W tle obraz prywatnej jednostki SKU zostanie również zaktualizowany automatycznie. Podobnie po usunięciu obrazu z podstawowej jednostki SKU obraz zostanie również usunięty z prywatnej jednostki SKU.

<a name="restricting-the-audience"></a>Ograniczanie odbiorców
------------------------

Oferty prywatne można znaleźć i wdrożyć tylko dla użytkowników wybranych.
Obecnie obsługujemy kierowanie użytkowników przy użyciu identyfikatorów subskrypcji.

Te subskrypcje można wprowadzać za pośrednictwem formularza ręcznego wprowadzania do **10 subskrypcji**lub przez przekazanie pliku CSV, który umożliwia korzystanie **z maksymalnie 20 000 subskrypcji**.

Wpis ręczny dla odbiorców z ograniczeniami:

![Ręcznie Ogranicz odbiorców](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience1.png)

Przekazywanie woluminów CSV dla odbiorców z ograniczeniami:

![Używanie CSV do ograniczania odbiorców](./media/cloud-partner-portal-publish-virtual-machine/restrictaudience2.png)

Zawartość pliku przykładowego CSV:

            Type,Id,Description
            SubscriptionId,7738d703-3135-4e8d-8b81-1e70379abd9d,Private Customer

Po przełączeniu się z ręcznego wpisu do widoku przekazywania woluminów CSV lub z woluminu CSV do wpisu ręcznego stara lista identyfikatorów subskrypcji z dostępem do jednostki SKU nie jest zachowywana. Wyświetlane jest ostrzeżenie, a lista jest zastępowana tylko przy zapisywaniu oferty.

<a name="managing-private-audiences"></a>Zarządzanie prywatnymi odbiorcami
-------------------------

**Aby można było zaktualizować odbiorców bez ponownego publikowania całej oferty, należy wprowadzić zmiany wprowadzone przez odbiorców (przy użyciu interfejsu użytkownika lub interfejsu API), a następnie zainicjować akcję "Synchronizuj odbiorców prywatnych".**

Jeśli odbiorca ma 10 lub mniej subskrypcji, można nim zarządzać całkowicie przy użyciu interfejsu użytkownika CPP.

Jeśli odbiorcy są więcej niż 10 subskrypcji, możesz nimi zarządzać przy użyciu pliku CSV, który można przekazać do interfejsu użytkownika CPP lub przy użyciu interfejsu API.

Jeśli używasz interfejsu API i nie chcesz zachować pliku CSV, możesz zarządzać odbiorcami bezpośrednio przy użyciu interfejsu API zgodnie z poniższymi instrukcjami.

> [!NOTE]
> Użyj identyfikatora subskrypcji platformy Azure (planów i jednostek SKU) lub identyfikatora dzierżawy (tylko plany), aby dodać odbiorców do swojej prywatnej oferty.

###  <a name="managing-subscriptions-with-the-api"></a>Zarządzanie subskrypcjami przy użyciu interfejsu API

Za pomocą interfejsu API można przekazać wolumin CSV lub zarządzać odbiorcami bezpośrednio (bez użycia woluminu CSV). Ogólnie rzecz biorąc wystarczy pobrać ofertę, zaktualizować `restrictedAudience` obiekt, a następnie przesłać te zmiany z powrotem do oferty w celu dodania lub usunięcia członków grupy odbiorców.

Oto jak programowo zaktualizować listę odbiorców:

1. [Pobierz dane oferty](cloud-partner-portal-api-retrieve-specific-offer.md) :

    ```
    GET https://cloudpartner.azure.com/api/publishers//offers/?api-version=2017-10-31&includeAllPricing=true
    ```

2. Znajdź obiekty dla odbiorców z ograniczeniami w poszczególnych jednostkach SKU oferty przy użyciu tego zapytania JPath:

    ```
    $.definition.plans[*].restrictedAudience
    ```
3. Aktualizowanie obiektów odbiorców z ograniczeniami dla oferty.

    **Jeśli pierwotnie przekazano listę subskrypcji z pliku CSV do swojej prywatnej oferty:**

    Obiekty *restrictedAudience* będą wyglądać następująco.
    ```
    "restrictedAudience": {
                  "uploadedCsvUri": "{SasUrl}"
    }
    ```

    Dla każdego obiektu odbiorcy z ograniczeniami:

    a. Pobierz zawartość `restrictedAudience.uploadedCsvUri`. Zawartość jest po prostu plikiem CSV z nagłówkami. Przykład:

        type,id,description
        subscriptionId,541a269f-3df2-486e-8fe3-c8f9dcf28205,sub1
        subscriptionId,c0da499c-25ec-4e4b-a42a-6e75635253b9,sub2

    b. W razie konieczności Dodaj lub Usuń subskrypcje w pobranym pliku CSV.

    c. Przekaż zaktualizowany plik CSV do lokalizacji, takiej jak [Azure Blob Storage](../../storage/blobs/storage-blobs-overview.md) lub [OneDrive](https://onedrive.live.com), i Utwórz link tylko do odczytu do pliku. Będzie to Twój nowy *adresie sasurl*.

    d. Zaktualizuj `restrictedAudience.uploadedCsvUri` klucz przy użyciu nowego *adresie sasurl*.

    **Jeśli ręcznie wprowadzisz oryginalną listę subskrypcji dla swojej prywatnej oferty z portal Cloud Partner:**

    Obiekty *restrictedAudience* będą wyglądać następująco:

    ```
    "restrictedAudience": {
        "manualEntries": [{
            "type": "subscriptionId",
            "id": "541a269f-3df2-486e-8fe3-c8f9dcf28205",
            "description": "sub1"
            }, {
            "type": "subscriptionId",
            "id": "c0da499c-25ec-4e4b-a42a-6e75635253b9",
            "description": "sub2"
            }
        ]}
    ```

    a. Dla każdego obiektu odbiorcy z ograniczeniami Dodaj lub Usuń wpisy na `restrictedAudience.manualEntries` liście zgodnie z wymaganiami.

4. Po zakończeniu aktualizacji wszystkich obiektów *restrictedAudience* dla każdej jednostki SKU prywatnej oferty należy [zaktualizować ofertę](cloud-partner-portal-api-creating-offer.md):

    ```
    PUT https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>?api-version=2017-10-31
    ```
    Dzięki temu zaktualizowana lista odbiorców jest teraz aktywna.

<a name="previewing-private-offers"></a>Wyświetlanie podglądu ofert prywatnych
-------------------------

W trakcie kroku wersji zapoznawczej/przejściowej tylko subskrypcje wersji zapoznawczej na poziomie oferty będą mogły uzyskać dostęp do jednostki SKU. Na tym etapie testowania możesz wyświetlić podgląd oferty, która będzie widoczna dla klientów docelowych.

Subskrypcje wersji zapoznawczej poziomu oferty w celu uzyskania dostępu do przygotowanych ofert:

![Podgląd identyfikatorów subskrypcji](./media/cloud-partner-portal-publish-virtual-machine/previewoffer1.png)

Po zakończeniu oferty tylko subskrypcje z ograniczeniami (wprowadzone za pośrednictwem wpisu ręcznego lub CSV) będą mogły wyświetlać i wdrażać prywatną jednostkę SKU. Firma Microsoft zaleca, aby w przypadku użytkowników z **ograniczeniami w celu weryfikacji zawsze uwzględnić własne subskrypcje w** odniesieniu do jednostki SKU.

>[!NOTE]
>Na potrzeby debugowania, zespoły pomocy technicznej i inżynierów firmy Microsoft będą mieć dostęp do tych ofert prywatnych.
