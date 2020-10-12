---
title: Szablony produktów na platformie Azure API Management | Microsoft Docs
description: Dowiedz się, jak dostosować zawartość stron produktu w portalu deweloperów usługi Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 49f9254c-4c5f-4ed4-9c8d-798f44e805ee
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 4c8cd4aa3e91c5d69c40e47683818ed8bc9be338
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86249907"
---
# <a name="product-templates-in-azure-api-management"></a>Szablony produktów w usłudze Azure API Management

Usługa Azure API Management umożliwia dostosowanie zawartości stron portalu dla deweloperów przy użyciu zestawu szablonów, które konfigurują ich zawartość. Korzystając z składni [DotLiquid](http://dotliquidmarkup.org/) i wybranego edytora, takiego jak [DotLiquid dla projektantów](https://github.com/dotliquid/dotliquid/wiki/DotLiquid-for-Designers), i dostępnego zestawu zlokalizowanych [zasobów ciągów](api-management-template-resources.md#strings), [zasobów glifów](api-management-template-resources.md#glyphs)i [kontrolek stron](api-management-page-controls.md), masz doskonałą elastyczność konfigurowania zawartości stron w miarę, w jakiej są one widoczne przy użyciu tych szablonów.  
  
 Szablony w tej sekcji umożliwiają dostosowanie zawartości stron produktu w portalu dla deweloperów.  
  
-   [Lista produktów](#ProductList)  
  
-   [Product](#Product)  
  
> [!NOTE]
>  Przykładowe szablony domyślne są zawarte w poniższej dokumentacji, ale mogą ulec zmianie ze względu na ciągłe ulepszenia. Możesz wyświetlić szablony domyślne na żywo w portalu dla deweloperów, przechodząc do żądanych poszczególnych szablonów. Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [How to dostosowywanie portalu deweloperów API Management przy użyciu szablonów](./api-management-developer-portal-templates.md).  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="product-list"></a><a name="ProductList"></a> Lista produktów  
 Szablon **Lista produktów** umożliwia dostosowanie treści strony Lista produktów w portalu dla deweloperów.  
  
 ![Lista produktów](./media/api-management-product-templates/APIM_ProductsListTemplatePage.png "APIM_ProductsListTemplatePage")  
  
### <a name="default-template"></a>Szablon domyślny  
  
```xml  
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
  
### <a name="controls"></a>Formanty  
 `Product list`Szablon może korzystać z następujących [kontrolek strony](api-management-page-controls.md).  
  
-   [Kontrola stronicowania](api-management-page-controls.md#paging-control)  
  
-   [Kontrolka wyszukiwania](api-management-page-controls.md#search-control)  
  
### <a name="data-model"></a>Model danych  
  
|Właściwość|Type|Opis|  
|--------------|----------|-----------------|  
|Stronicowanie|Jednostka [stronicowania](api-management-template-data-model-reference.md#Paging) .|Informacje o stronicowaniu dla kolekcji Products.|  
|Filtrowanie|Obiekt [filtrowania](api-management-template-data-model-reference.md#Filtering) .|Informacje o filtrowaniu na stronie listy produktów.|  
|Produkty|Kolekcja jednostek [produktu](api-management-template-data-model-reference.md#Product) .|Produkty widoczne dla bieżącego użytkownika.|  
  
### <a name="sample-template-data"></a>Przykładowe dane szablonu  
  
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
            "Id": "56f9445ffaf7560049060001",  
            "Title": "Starter",  
            "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
            "Terms": "",  
            "ProductState": 1,  
            "AllowMultipleSubscriptions": false,  
            "MultipleSubscriptionsCount": 1  
        },  
        {  
            "Id": "56f9445ffaf7560049060002",  
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
  
##  <a name="product"></a><a name="Product"></a> Iloczyn  
 Szablon **produktu** pozwala na dostosowanie treści strony produktu w portalu dla deweloperów.  
  
 ![Strona produktu portalu dla deweloperów](./media/api-management-product-templates/APIM_ProductPage.png "APIM_ProductPage")  
  
### <a name="default-template"></a>Szablon domyślny  
  
```xml  
<h2>{{Product.Title}}</h2>  
<p>{{Product.Description}}</p>  
  
{% assign replaceString0 = '{0}' %}  
  
{% if Limits and Limits.size > 0 %}  
<h3>{% localized "ProductDetailsStrings|WebProductsUsageLimitsHeader"%}</h3>  
<ul>  
  {% for limit in Limits %}  
  <li>{{limit.DisplayName}}</li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if apis.size > 0 %}  
<p>  
  <b>  
    {% if apis.size == 1 %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockSingleApisCount" %}{% endcapture %}  
    {% else %}  
    {% capture apisCountText %}{% localized "ProductDetailsStrings|TextblockMultipleApisCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture apisCount %}{{apis.size}}{% endcapture %}  
    {{ apisCountText | replace : replaceString0, apisCount }}  
  </b>  
</p>  
  
<ul>  
  {% for api in Apis %}  
  <li>  
    <a href="/docs/services/{{api.Id}}">{{api.Name}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
  
{% if subscriptions.size > 0 %}  
<p>  
  <b>  
    {% if subscriptions.size == 1 %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockSingleSubscriptionsCount" %}{% endcapture %}  
    {% else %}  
    {% capture subscriptionsCountText %}{% localized "ProductDetailsStrings|TextblockMultipleSubscriptionsCount" %}{% endcapture %}  
    {% endif %}  
  
    {% capture subscriptionsCount %}{{subscriptions.size}}{% endcapture %}  
    {{ subscriptionsCountText | replace : replaceString0, subscriptionsCount }}  
  </b>  
</p>  
  
<ul>  
  {% for subscription in subscriptions %}  
  <li>  
    <a href="/developer#{{subscription.Id}}">{{subscription.DisplayName}}</a>  
  </li>  
  {% endfor %}  
</ul>  
{% endif %}  
{% if CannotAddBecauseSubscriptionNumberLimitReached %}  
<b>{% localized "ProductDetailsStrings|TextblockSubscriptionLimitReached" %}</b>  
{% elsif CannotAddBecauseMultipleSubscriptionsNotAllowed == false %}  
<subscribe-button></subscribe-button>  
{% endif %}  
```  
  
### <a name="controls"></a>Formanty  
 `Product list`Szablon może korzystać z następujących [kontrolek strony](api-management-page-controls.md).  
  
-   [subskrypcja — przycisk](api-management-page-controls.md#subscribe-button)  
  
### <a name="data-model"></a>Model danych  
  
|Właściwość|Type|Opis|  
|--------------|----------|-----------------|  
|Produkt|[Product](api-management-template-data-model-reference.md#Product)|Określony produkt.|  
|IsDeveloperSubscribed|boolean|Czy bieżący użytkownik subskrybuje ten produkt.|  
|SubscriptionState|liczba|Stan subskrypcji. Możliwe stany to:<br /><br /> -   `0 - suspended` — subskrypcja została zablokowana i subskrybent nie może wywołać żadnych interfejsów API produktu.<br />-   `1 - active` — subskrypcja jest aktywna.<br />-   `2 - expired` — subskrypcja osiągnęła swoją datę wygaśnięcia i została zdezaktywowana.<br />-   `3 - submitted` — żądanie subskrypcji zostało wykonane przez dewelopera, ale jeszcze nie zostało zatwierdzone lub odrzucone.<br />-   `4 - rejected` — żądanie subskrypcji zostało odrzucone przez administratora.<br />-   `5 - cancelled` — subskrypcja została anulowana przez dewelopera lub administratora.|  
|Limity|array|Ta właściwość jest przestarzała i nie powinna być używana.|  
|DelegatedSubscriptionEnabled|boolean|Czy [delegowanie](./api-management-howto-setup-delegation.md) jest włączone dla tej subskrypcji.|  
|DelegatedSubscriptionUrl|ciąg|Jeśli Delegowanie jest włączone, delegowany adres URL subskrypcji.|  
|Nie zgadzam się|boolean|Jeśli produkt ma warunki, niezależnie od tego, czy bieżący użytkownik wyraził zgodę na warunki.|  
|Subskrypcje|Kolekcja jednostek [podsumowania subskrypcji](api-management-template-data-model-reference.md#SubscriptionSummary) .|Subskrypcje produktu.|  
|Programowania|Kolekcja jednostek [interfejsu API](api-management-template-data-model-reference.md#API) .|Interfejsy API w tym produkcie.|  
|CannotAddBecauseSubscriptionNumberLimitReached|boolean|Czy bieżący użytkownik ma uprawnienia do subskrybowania tego produktu w odniesieniu do limitu subskrypcji.|  
|CannotAddBecauseMultipleSubscriptionsNotAllowed|boolean|Czy bieżący użytkownik ma uprawnienia do subskrybowania tego produktu w odniesieniu do dozwolonej liczby subskrypcji, czy nie.|  
  
### <a name="sample-template-data"></a>Przykładowe dane szablonu  
  
```json  
{  
    "Product": {  
        "Id": "56f9445ffaf7560049060001",  
        "Title": "Starter",  
        "Description": "Subscribers will be able to run 5 calls/minute up to a maximum of 100 calls/week.",  
        "Terms": "",  
        "ProductState": 1,  
        "AllowMultipleSubscriptions": false,  
        "MultipleSubscriptionsCount": 1  
    },  
    "IsDeveloperSubscribed": true,  
    "SubscriptionState": 1,  
    "Limits": [],  
    "DelegatedSubscriptionEnabled": false,  
    "DelegatedSubscriptionUrl": null,  
    "IsAgreed": false,  
    "Subscriptions": [  
        {  
            "Id": "56f9445ffaf7560049070001",  
            "DisplayName": "Starter  (default)"  
        }  
    ],  
    "Apis": [  
        {  
            "id": "56f9445ffaf7560049040001",  
            "name": "Echo API",  
            "description": null,  
            "serviceUrl": "http://echoapi.cloudapp.net/api",  
            "path": "echo",  
            "protocols": [  
                2  
            ],  
            "authenticationSettings": null,  
            "subscriptionKeyParameterNames": null  
        }  
    ],  
    "CannotAddBecauseSubscriptionNumberLimitReached": false,  
    "CannotAddBecauseMultipleSubscriptionsNotAllowed": true  
}  
```

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [How to dostosowywanie portalu deweloperów API Management przy użyciu szablonów](api-management-developer-portal-templates.md).
