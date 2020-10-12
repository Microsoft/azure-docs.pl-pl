---
title: Dostosowywanie portalu deweloperów API Management przy użyciu szablonów
titleSuffix: Azure API Management
description: Dowiedz się, jak dostosować Portal deweloperów API Management platformy Azure przy użyciu szablonów.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: a195675b-f7d0-4fc9-90bf-860e6f17ccf7
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 7a8c348340be143f7059ce7e64a1c66b66074a45
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75430787"
---
# <a name="how-to-customize-the-azure-api-management-developer-portal-using-templates"></a>Jak dostosować Portal deweloperów API Management platformy Azure przy użyciu szablonów

Istnieją trzy podstawowe sposoby dostosowywania portalu deweloperów w usłudze Azure API Management:

* [Edycja zawartości stron statycznych i elementów układu strony][modify-content-layout]
* [Aktualizacja stylów używanych dla elementów strony w portalu deweloperów][customize-styles]
* [Modyfikowanie szablonów używanych dla stron wygenerowanych przez portal][portal-templates] (wyjaśnione w tym przewodniku)

Szablony służą do dostosowywania zawartości stron portalu deweloperów generowanych przez system (na przykład dokumentacji interfejsu API, produktów, uwierzytelniania użytkowników itp.). Za pomocą składni [DotLiquid](http://dotliquidmarkup.org/) i dostępnego zestawu zlokalizowanych zasobów ciągów, ikon i kontrolek stron, masz doskonałą elastyczność konfigurowania zawartości stron zgodnie z oczekiwaniami.

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="developer-portal-templates-overview"></a>Omówienie szablonów portalu dla deweloperów

Edytowanie szablonów odbywa się z poziomu **portalu dla deweloperów** , podczas gdy użytkownik jest zalogowany jako administrator. Aby najpierw otworzyć Azure Portal i kliknij przycisk Portal dla **deweloperów** na pasku narzędzi wystąpienia API Management.

Aby uzyskać dostęp do szablonów portalu dla deweloperów, kliknij ikonę Dostosuj po lewej stronie, aby wyświetlić menu Dostosowywanie, a następnie kliknij pozycję **Szablony**.

![Szablony portalu dla deweloperów][api-management-customize-menu]

Lista szablonów zawiera kilka kategorii szablonów obejmujących różne strony w portalu dla deweloperów. Każdy szablon jest różny, ale czynności do edycji i publikowania zmian są takie same. Aby edytować szablon, kliknij nazwę szablonu.

![Szablony portalu dla deweloperów][api-management-templates-menu]

Kliknięcie szablonu spowoduje przejście do strony portalu dla deweloperów, którą można dostosować za pomocą tego szablonu. W tym przykładzie zostanie wyświetlony szablon **Lista produktów** . Szablon **Lista produktów** steruje obszarem ekranu wskazywanym przez czerwony prostokąt.

![Szablon listy produktów][api-management-developer-portal-templates-overview]

Niektóre szablony, takie jak szablony **profilów użytkowników** , dostosowują różne części tej samej strony.

![Szablony profilów użytkowników][api-management-user-profile-templates]

Edytor dla każdego szablonu portalu dla deweloperów ma dwie sekcje wyświetlane w dolnej części strony. Po lewej stronie zostanie wyświetlone okienko edycji szablonu, a po prawej stronie zostanie wyświetlony model danych dla szablonu.

Okienko edycji szablonu zawiera znacznik, który kontroluje wygląd i zachowanie odpowiedniej strony w portalu dla deweloperów. Znaczniki w szablonie używają składni [DotLiquid](http://dotliquidmarkup.org/) . Jeden z popularnych edytorów dla DotLiquid jest [DotLiquid dla projektantów](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers). Wszystkie zmiany wprowadzone w szablonie podczas edytowania są wyświetlane w czasie rzeczywistym w przeglądarce, ale nie są widoczne dla klientów do momentu [zapisania](#to-save-a-template) i [opublikowanie](#to-publish-a-template) szablonu.

![Znaczniki szablonu][api-management-template]

Okienko **dane szablonu** zawiera Przewodnik dotyczący modelu danych dla jednostek, które są dostępne do użycia w konkretnym szablonie. Ten przewodnik zawiera informacje na temat wyświetlania danych na żywo, które są aktualnie wyświetlane w portalu dla deweloperów. Okienka szablonu można rozwinąć, klikając prostokąt w prawym górnym rogu okienka **dane szablonu** .

![Model danych szablonu][api-management-template-data]

W poprzednim przykładzie w portalu deweloperów są wyświetlane dwa produkty, które zostały pobrane z danych wyświetlanych w okienku **dane szablonu** , jak pokazano w następującym przykładzie:

```json
{
    "Paging": {
        "Page": 1,
        "PageSize": 10,
        "TotalItemCount": 2,
        "ShowAll": false,
        "PageCount": 1
    },
    "Filtering": {
        "Pattern": null,
        "Placeholder": "Search products"
    },
    "Products": [
        {
            "Id": "56ec64c380ed850042060001",
            "Title": "Starter",
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",
            "Terms": "",
            "ProductState": 1,
            "AllowMultipleSubscriptions": false,
            "MultipleSubscriptionsCount": 1
        },
        {
            "Id": "56ec64c380ed850042060002",
            "Title": "Unlimited",
            "Description": "Subscribers have completely unlimited access to the API. Administrator approval is required.",
            "Terms": null,
            "ProductState": 1,
            "AllowMultipleSubscriptions": false,
            "MultipleSubscriptionsCount": 1
        }
    ]
}
```

Znaczniki w szablonie **listy produktów** przetwarzają dane w celu zapewnienia żądanych danych wyjściowych przez iterację kolekcji produktów do wyświetlania informacji oraz łącza do poszczególnych produktów. Zanotuj `<search-control>` `<page-control>` elementy i w znaczniku. Kontrolują one wyświetlanie kontrolek wyszukiwania i stronicowania na stronie. `ProductsStrings|PageTitleProducts` to zlokalizowane odwołanie do ciągu, które zawiera `h2` tekst nagłówka strony. Listę zasobów ciągów, kontrolek stron i ikon dostępnych do użycia w szablonach portalu deweloperów można znaleźć w temacie [API Management dokumentacja szablonów portalu dla deweloperów](api-management-developer-portal-templates-reference.md).

```html
<search-control></search-control>
<div class="row">
    <div class="col-md-9">
        <h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>
    </div>
</div>
<div class="row">
    <div class="col-md-12">
    {% if products.size > 0 %}
    <ul class="list-unstyled">
    {% for product in products %}
        <li>
            <h3><a href="/products/{{product.id}}">{{product.title}}</a></h3>
            {{product.description}}
        </li>
    {% endfor %}
    </ul>
    <paging-control></paging-control>
    {% else %}
    {% localized "CommonResources|NoItemsToDisplay" %}
    {% endif %}
    </div>
</div>
```

## <a name="to-save-a-template"></a>Aby zapisać szablon
Aby zapisać szablon, kliknij przycisk Zapisz w edytorze szablonów.

![Zapisywanie szablonu][api-management-save-template]

Zapisane zmiany nie są aktywne w portalu dla deweloperów, dopóki nie zostaną opublikowane.

## <a name="to-publish-a-template"></a>Aby opublikować szablon
Zapisane szablony mogą być publikowane pojedynczo lub wspólnie. Aby opublikować pojedynczy szablon, kliknij przycisk Publikuj w edytorze szablonów.

![Publikowanie szablonu][api-management-publish-template]

Kliknij przycisk **tak** , aby potwierdzić i utworzyć szablon na żywo w portalu dla deweloperów.

![Potwierdź opublikowanie][api-management-publish-template-confirm]

Aby opublikować wszystkie aktualnie nieopublikowane wersje szablonów, kliknij przycisk **Publikuj** na liście szablonów. Nieopublikowane szablony są oznaczone gwiazdką poniżej nazwy szablonu. W tym przykładzie jest publikowana **Lista produktów** i szablony **produktów** .

![Publikowanie szablonów][api-management-publish-templates]

Kliknij przycisk **Publikuj dostosowania** w celu potwierdzenia.

![Potwierdź opublikowanie][api-management-publish-customizations]

Nowo opublikowane szablony zaczynają obowiązywać natychmiast w portalu dla deweloperów.

## <a name="to-revert-a-template-to-the-previous-version"></a>Aby przywrócić poprzednią wersję szablonu
Aby przywrócić szablon do poprzedniej opublikowanej wersji, kliknij przycisk Przywróć w edytorze szablonów.

![Przywróć szablon][api-management-revert-template]

Kliknij przycisk **Tak**, aby potwierdzić.

![Confirm][api-management-revert-template-confirm]

Wcześniej opublikowana wersja szablonu znajduje się w portalu dla deweloperów po zakończeniu operacji przywracania.

## <a name="to-restore-a-template-to-the-default-version"></a>Aby przywrócić wersję domyślną szablonu
Przywracanie szablonów do ich domyślnej wersji jest procesem dwuetapowym. Najpierw należy przywrócić szablony, a następnie przywrócić przywrócone wersje.

Aby przywrócić domyślną wersję jednego szablonu, kliknij przycisk Przywróć w edytorze szablonów.

![Przywróć szablon][api-management-reset-template]

Kliknij przycisk **Tak**, aby potwierdzić.

![Confirm][api-management-reset-template-confirm]

Aby przywrócić domyślne wersje wszystkich szablonów, kliknij przycisk **Przywróć domyślne szablony** na liście szablonów.

![Przywracanie szablonów][api-management-restore-templates]

Przywrócone szablony należy następnie opublikować pojedynczo lub wszystkie jednocześnie, wykonując czynności opisane w sekcji [Aby opublikować szablon](#to-publish-a-template).

## <a name="next-steps"></a>Następne kroki
Informacje referencyjne dotyczące szablonów portalu deweloperów, zasobów ciągów, ikon i kontrolek strony znajdują się w temacie [API Management dokumentacja szablonów portalu dla deweloperów](api-management-developer-portal-templates-reference.md).

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customize-menu]: ./media/api-management-developer-portal-templates/api-management-customize-menu.png
[api-management-templates-menu]: ./media/api-management-developer-portal-templates/api-management-templates-menu.png
[api-management-developer-portal-templates-overview]: ./media/api-management-developer-portal-templates/api-management-developer-portal-templates-overview.png
[api-management-template]: ./media/api-management-developer-portal-templates/api-management-template.png
[api-management-template-data]: ./media/api-management-developer-portal-templates/api-management-template-data.png
[api-management-developer-portal-menu]: ./media/api-management-developer-portal-templates/api-management-developer-portal-menu.png
[api-management-management-console]: ./media/api-management-developer-portal-templates/api-management-management-console.png
[api-management-browse]: ./media/api-management-developer-portal-templates/api-management-browse.png
[api-management-user-profile-templates]: ./media/api-management-developer-portal-templates/api-management-user-profile-templates.png
[api-management-save-template]: ./media/api-management-developer-portal-templates/api-management-save-template.png
[api-management-publish-template]: ./media/api-management-developer-portal-templates/api-management-publish-template.png
[api-management-publish-template-confirm]: ./media/api-management-developer-portal-templates/api-management-publish-template-confirm.png
[api-management-publish-templates]: ./media/api-management-developer-portal-templates/api-management-publish-templates.png
[api-management-publish-customizations]: ./media/api-management-developer-portal-templates/api-management-publish-customizations.png
[api-management-revert-template]: ./media/api-management-developer-portal-templates/api-management-revert-template.png
[api-management-revert-template-confirm]: ./media/api-management-developer-portal-templates/api-management-revert-template-confirm.png
[api-management-reset-template]: ./media/api-management-developer-portal-templates/api-management-reset-template.png
[api-management-reset-template-confirm]: ./media/api-management-developer-portal-templates/api-management-reset-template-confirm.png
[api-management-restore-templates]: ./media/api-management-developer-portal-templates/api-management-restore-templates.png
