---
title: Kategorie wyszukiwania dla interfejsu API wyszukiwania lokalnego usługi Bing
titleSuffix: Azure Cognitive Services
description: Skorzystaj z tego artykułu, aby dowiedzieć się, jak określić kategorie wyszukiwania dla punktu końcowego interfejsu API wyszukiwania w usłudze Bing Local Business.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: rosh
ms.openlocfilehash: 19b769d1fff431f95c20e607c17747f2ff483d2f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96487188"
---
# <a name="search-categories-for-the-bing-local-business-search-api"></a>Kategorie wyszukiwania dla interfejsu API wyszukiwania lokalnego usługi Bing

> [!WARNING]
> Interfejsy API wyszukiwania Bing są przenoszone z Cognitive Services do usług Wyszukiwanie Bing. Od **30 października 2020** wszystkie nowe wystąpienia wyszukiwanie Bing muszą być obsługiwane zgodnie z procesem opisanym [tutaj](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Interfejsy API wyszukiwania Bing obsługa administracyjna przy użyciu Cognitive Services będzie obsługiwana przez kolejne trzy lata lub do końca Enterprise Agreement, w zależności od tego, co nastąpi wcześniej.
> Instrukcje dotyczące migracji znajdują się w temacie [wyszukiwanie Bing Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Interfejs API wyszukiwania lokalnego usługi Bing pozwala wyszukiwać lokalne jednostki biznesowe w różnych kategoriach z priorytetem dla wyników zamknięcia lokalizacji użytkownika. Można uwzględnić te wyszukiwania w wyszukiwaniach wraz z `localCircularView` `localMapView` [parametrami](specify-geographic-search.md)i.


## <a name="toplevel-categories"></a>Kategorie TopLevel 

Poniższe typy definiują główne kategorie wyszukiwania.  Za pomocą listy rozdzielanej przecinkami przypisanej do parametru można określić więcej niż jedną kategorię `localCategories` .  
- EatDrink 
- SeeDo 
- Sklepu 
- HotelsAndMotels 
- BanksAndCreditUnions 
- Przy 
- Budowy 

## <a name="sub-categories"></a>Podkategorie
Podkategorie są przesyłane w taki sam sposób jak w przypadku `localCategories` . Podkategorie są bardziej szczegółowymi kategoriami. Są one podwładnymi w sensie, że jeśli określisz kategorię C i jedną z jej podkategorii S w tej samej liście rozdzielanej przecinkami, otrzymasz te same wyniki, co w przypadku, gdy określono tylko znak C.

### <a name="eat-drink"></a>Napój Eat

> BreweriesAndBrewPubs, CocktailLounges, AfricanRestaurants, AmericanRestaurants, Bagels, BarbecueRestaurants, Taverns, SportsBars, bar, BarsGrillsAndPubs, BuffetRestaurants | BelgianRestaurants, BritishRestaurants, CafeRestaurants, CaribbeanRestaurants, ChineseRestaurants, CoffeeAndTea, Delicatessens, DeliveryService, Diners, DiscountStores, Donuts, FastFood, FrenchRestaurants, FrozenYogurt, GermanRestaurants, Supermarkets, GreekRestaurants, spożywczy, HawaiianRestaurants, HungarianRestaurants, IceCreamAndFrozenDesserts, IndianRestaurants, ItalianRestaurants, JapaneseRestaurants, KoreanRestaurants, LiquorStores, MexicanRestaurants, MiddleEasternRestaurants, Pizza, PolishRestaurants, PortugueseRestaurants, pretzels, RussianAndUkrainianRestaurants, Sandwiches, SeafoodRestaurants, SpanishRestaurants, SteakHouseRestaurants

### <a name="see-do"></a>Zobacz zrobić

> AmusementParks, attractions, Carnivals, Casinos, LandmarksAndHistoricalSites, MiniatureGolfCourses, MovieTheaters, muzea, Parke, SightseeingTours, TouristInformation, ogrody

### <a name="shop"></a>Sklepu

> AntiqueStores, Bookstores, CDAndRecordStores, ChildrensClothingStores, CigarAndTobaccoShops, ComicBookStores, DepartmentStores, DiscountStores, FleaMarketsAndBazaars, FurnitureStores, HomeImprovementStores, JewelryAndWatchesStores, KitchenwareStores, LiquorStores, MallsAndShoppingCenters, MensClothingStores, MusicStores, OutletStores, PetShops, PetSupplyStores, SchoolAndOfficeSupplyStores, ShoeStores, SportingGoodsStores, ToyAndGameStores, VitaminAndSupplementStores, WomensClothingStores


## <a name="examples-of-local-categories-search"></a>Przykłady lokalnych kategorii wyszukiwania

Poniższe przykłady POBIERAją wyniki zgodnie z `localCategories` parametrem:

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=HotelsAndMotels`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=EatDrink`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Shop`

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localcategories=Hospitals`

Następujące zapytanie ogranicza liczbę wyników "Szpital" do pierwszych trzech zwróconych z interfejsu API lokalnego wyszukiwania biznesowego Bing:

`https://api.cognitive.microsoft.com/localbusinesses/v7.0/search?&q=&mkt=en-US&localCategories=Hospitals&count=3&offset=0`

Następująca przykładowa odpowiedź JSON obejmuje trzy Szpitale w większym obszarze Seattle:

```json
BingAPIs-TraceId: 68AFB51807C6485CAB8AAF20E232EFFF
BingAPIs-SessionId: F89E7B8539B34BF58AAF811485E83B20
X-MSEdge-ClientID: 1C44E64DBFAA6BCA1270EADDBE7D6A22
BingAPIs-Market: en-US
X-MSEdge-Ref: Ref A: 68AFB51807C6485CAB8AAF20E232EFFF Ref B: CO1EDGE0108 Ref C: 2018-10-22T18:52:28Z

{
   "_type": "SearchResponse",
   "queryContext": {
      "originalQuery": ""
   },
   "places": {
      "readLink": "https:\/\/www.bingapis.com\/api\/v7\/places\/search?q=",
      "totalEstimatedMatches": 0,
      "value": [
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.0",
            "name": "Overlake Hospital Medical Center",
            "url": "http:\/\/www.overlakehospital.org\/",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6204986572266,
               "longitude": -122.185829162598
            },
            "routablePoint": {
               "latitude": 47.6204986572266,
               "longitude": -122.185668945312
            },
            "address": {
               "streetAddress": "1035 116th Ave NE",
               "addressLocality": "Bellevue",
               "addressRegion": "WA",
               "postalCode": "98004",
               "addressCountry": "US",
               "neighborhood": "",
               "text": "1035 116th Ave NE, Bellevue, WA, 98004"
            },
            "telephone": "(425) 688-5000"
         },
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.1",
            "name": "Virginia Mason University Village Medical Center",
            "url": "https:\/\/virginiamason.org\/Seattle",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6095390319824,
               "longitude": -122.327941894531
            },
            "routablePoint": {
               "latitude": 47.6093978881836,
               "longitude": -122.328277587891
            },
            "address": {
               "streetAddress": "1100 9th Ave",
               "addressLocality": "Seattle",
               "addressRegion": "WA",
               "postalCode": "98101",
               "addressCountry": "US",
               "neighborhood": "University District",
               "text": "1100 9th Ave, Seattle, WA, 98101"
            },
            "telephone": "(206) 223-6600"
         },
         {
            "_type": "LocalBusiness",
            "id": "https:\/\/www.bingapis.com\/api\/v7\/#Places.2",
            "name": "Seattle Children’s Hospital",
            "url": "http:\/\/www.seattlechildrens.org\/",
            "entityPresentationInfo": {
               "entityScenario": "ListItem",
               "entityTypeHints": [
                  "Place",
                  "LocalBusiness"
               ]
            },
            "geo": {
               "latitude": 47.6625061035156,
               "longitude": -122.283760070801
            },
            "routablePoint": {
               "latitude": 47.6631507873535,
               "longitude": -122.284614562988
            },
            "address": {
               "streetAddress": "4800 Sand Point Way NE",
               "addressLocality": "Seattle",
               "addressRegion": "WA",
               "postalCode": "98105",
               "addressCountry": "US",
               "neighborhood": "Laurelhurst",
               "text": "4800 Sand Point Way NE, Seattle, WA, 98105"
            },
            "telephone": "(206) 987-2000"
         }
      ],
      "searchAction": {

      }
   }
}
```

## <a name="next-steps"></a>Następne kroki
- [Granice wyszukiwania geograficznego](specify-geographic-search.md)
- [Zapytanie i odpowiedź](local-search-query-response.md)
- [Przewodnik Szybki Start w języku C #](quickstarts/local-quickstart.md)