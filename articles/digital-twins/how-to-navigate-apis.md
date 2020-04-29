---
title: Nawigowanie po interfejsach API usługi Azure Digital bliźniaczych reprezentacji | Microsoft Docs
description: Dowiedz się, jak często spotykane są zapytania dotyczące interfejsów API usługi Azure Digital bliźniaczych reprezentacji Management.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: e9cdfd40a9672d19ef32dede0baadcdd56266bab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79265170"
---
# <a name="how-to-use-azure-digital-twins-management-apis"></a>Jak używać interfejsów API usługi Azure Digital bliźniaczych reprezentacji Management

Interfejsy API usługi Azure Digital bliźniaczych reprezentacji Management zapewniają zaawansowane funkcje dla aplikacji IoT. W tym artykule pokazano, jak nawigować przez strukturę interfejsu API.  

## <a name="api-summary"></a>Podsumowanie interfejsu API

Na poniższej liście przedstawiono składniki interfejsów API Digital bliźniaczych reprezentacji.

* [/Spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces): te interfejsy API współpracują z lokalizacjami fizycznymi w instalatorze. Te informacje ułatwiają tworzenie, usuwanie i Zarządzanie cyfrowymi mapowaniami lokalizacji fizycznych w postaci [wykresu przestrzennego](concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph).

* [/Devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices): te interfejsy API współpracują z urządzeniami w instalatorze. Urządzenia te mogą zarządzać jedną lub większą liczbą czujników. Na przykład urządzenie może być telefonem lub Raspberry z czujnikiem pi lub bramą Lora utworzonej i tak dalej.

* [/Sensors](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Sensors): te interfejsy API pomagają w komunikacji z czujnikami skojarzonymi z urządzeniami i lokalizacjami fizycznymi. Czujniki rejestrują i wysyłają wartości otoczenia, których następnie można użyć do manipulowania środowiskiem przestrzennym.  

* [/resources](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Resources): te interfejsy API ułatwiają konfigurowanie zasobów, takich jak centrum IoT, dla wystąpienia Digital bliźniaczych reprezentacji.

* [/Types](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Types): te interfejsy API umożliwiają kojarzenie rozszerzonych typów z obiektami cyfrowymi bliźniaczych reprezentacji, aby dodać do tych obiektów określone cechy. Te typy umożliwiają łatwe filtrowanie i grupowanie obiektów w interfejsie użytkownika oraz funkcji niestandardowych, które przetwarzają dane telemetryczne. Przykłady rozszerzonych typów to typ *urządzenia*, *SensorType*, *SensorDataType*, *spacetype*, *SpaceSubType*, *SpaceBlobType*, *SpaceResourceType*i tak dalej.

* [/ontologies](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Ontologies): te interfejsy API ułatwiają zarządzanie ontologie, które są kolekcjami typów rozszerzonych. Ontologie Podaj nazwy dla typów obiektów zgodnie z ich rozmiarem fizycznym. Na przykład *BACnet* Ontology dostarcza określonych nazw dla *typów czujników*, *typów*danych, *datasubtypes*i *dataunittypes*. Ontologie są zarządzane i tworzone przez usługę. Użytkownicy mogą ładować i zwalniać ontologie. Po załadowaniu Ontology wszystkie skojarzone z nim nazwy typów są włączane i gotowe do aprowizacji na wykresie przestrzennym. 

* [/propertyKeys](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/PropertyKeys): można użyć tych interfejsów API do tworzenia niestandardowych właściwości dla Twoich *miejsc*, *urządzeń*, *użytkowników*i *czujników*. Te właściwości są tworzone jako pary klucz/wartość. Można zdefiniować typ danych dla tych właściwości, ustawiając ich *PrimitiveDataType*. Na przykład można zdefiniować właściwość o nazwie *BasicTemperatureDeltaProcessingRefreshTime* typu *uint* dla czujników, a następnie przypisać wartość tej właściwości dla każdej z czujników. Możesz również dodać ograniczenia dla tych wartości podczas tworzenia właściwości, takiej jak *minimalna* i *Maksymalna liczba* zakresów, a także dozwolonych wartości jako *ValidationData*.

* [/matchers](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Matchers): te interfejsy API umożliwiają określenie warunków, które chcesz oszacować z danych urządzenia przychodzącego. Więcej informacji zawiera [ten artykuł](concepts-user-defined-functions.md#matchers). 

* [/userDefinedFunctions](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/UserDefinedFunctions): te interfejsy API umożliwiają tworzenie, usuwanie i aktualizowanie funkcji niestandardowej, która będzie wykonywana, gdy wystąpią warunki zdefiniowane przez *dopasowania* , aby przetwarzać dane pochodzące z Instalatora. Zobacz [ten artykuł](concepts-user-defined-functions.md#user-defined-functions) , aby uzyskać więcej informacji o tych funkcjach niestandardowych, nazywanych również *funkcjami zdefiniowanymi przez użytkownika*. 

* [/Endpoints](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Endpoints): te interfejsy API umożliwiają tworzenie punktów końcowych, dzięki czemu rozwiązanie Digital bliźniaczych reprezentacji może komunikować się z innymi usługami platformy Azure w celu przechowywania i analizowania danych. Przeczytaj [ten artykuł](concepts-events-routing.md) , aby uzyskać więcej informacji. 

* [/keyStores](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/KeyStores): te interfejsy API umożliwiają Zarządzanie magazynami kluczy zabezpieczeń dla miejsc. Te magazyny mogą zawierać kolekcję kluczy zabezpieczeń i umożliwiają łatwe pobieranie najnowszych prawidłowych kluczy.

* [/users](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Users): te interfejsy API umożliwiają kojarzenie użytkowników ze spacjami, aby odszukać te osoby w razie potrzeby. 

* [/odzyskiwanie](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/System): te interfejsy API umożliwiają zarządzanie ustawieniami całego systemu, takimi jak domyślne typy spacji i czujników. 

* [/roleAssignments](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/RoleAssignments): te interfejsy API umożliwiają kojarzenie ról z jednostkami, takimi jak identyfikator użytkownika, identyfikator funkcji zdefiniowany przez użytkownika itd. Każde przypisanie roli obejmuje identyfikator jednostki do skojarzenia, typ jednostki, identyfikator roli do skojarzenia, identyfikator dzierżawy i ścieżkę definiującą górny limit zasobu, do którego jednostka może uzyskać dostęp przy użyciu tego skojarzenia. Przeczytaj [ten artykuł](security-role-based-access-control.md) , aby uzyskać więcej informacji.


## <a name="api-navigation"></a>Nawigacja interfejsu API

Interfejsy API Digital bliźniaczych reprezentacji obsługują filtrowanie i nawigowanie na całym wykresie przestrzennym przy użyciu następujących parametrów:

- **spaceId**: interfejs API będzie filtrować wyniki według danego identyfikatora obszaru. Dodatkowo Flaga logiczna **useParentSpace** ma zastosowanie do interfejsów API [/Spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces) , co oznacza, że dany identyfikator obszaru odwołuje się do przestrzeni nadrzędnej zamiast bieżącego obszaru. 

- **minLevel** i **maxLevel**: spacje główne są uznawane za na poziomie 1. Spacje z przestrzenią nadrzędną na poziomie *n* są na poziomie *n + 1*. Po ustawieniu tych wartości można filtrować wyniki na określonych poziomach. Te wartości łączą się po ustawieniu. Urządzenia, czujniki i inne obiekty są uważane za znajdujące się na tym samym poziomie co najbliższe miejsce. Aby uzyskać wszystkie obiekty na danym poziomie, ustaw wartości **minLevel** i **maxLevel** na taką samą wartość.

- **minRelative** i **maxRelative**: Kiedy te filtry są podane, odpowiedni poziom jest określany względem poziomu danego obszaru:
   - Poziom względny *0* to ten sam poziom, co dany identyfikator obszaru.
   - Poziom względny *1* oznacza spacje na tym samym poziomie co elementy podrzędne danego identyfikatora obszaru. Poziom względny *n* reprezentuje spacje mniejsze niż określone miejsce o *n* poziomach.
   - Poziom względny *-1* oznacza spacje na tym samym poziomie co przestrzeń nadrzędna określonego obszaru.

- **Przechodzenie**: umożliwia przechodzenie w dowolnym kierunku od danego identyfikatora obszaru określonego przez następujące wartości.
   - **Brak**: Ta wartość domyślna jest filtrowana dla danego identyfikatora obszaru.
   - **W dół**: filtry według danego identyfikatora obszaru i jego elementów podrzędnych. 
   - W **górę**: filtry według danego identyfikatora obszaru i jego elementów nadrzędnych. 
   - **Zakres**: filtruje poziomą część wykresu przestrzennego na tym samym poziomie, co dany identyfikator obszaru. Wymaga to ustawienia wartości **minRelative** lub **maxRelative** . 


### <a name="examples"></a>Przykłady

Na poniższej liście przedstawiono kilka przykładów nawigacji za pomocą interfejsów API [/Devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices) . Należy zauważyć, że `YOUR_MANAGEMENT_API_URL` symbol zastępczy odnosi się do identyfikatora URI interfejsów API Digital `https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/api/v1.0/`bliźniaczych reprezentacji w `YOUR_INSTANCE_NAME` formacie, gdzie jest nazwą wystąpienia usługi Azure Digital bliźniaczych reprezentacji i `YOUR_LOCATION` jest regionem, w którym jest hostowane wystąpienie.

- `YOUR_MANAGEMENT_API_URL/devices?maxLevel=1`zwraca wszystkie urządzenia dołączone do miejsc głównych.
- `YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4`zwraca wszystkie urządzenia dołączone do miejsc o poziomach 2, 3 lub 4.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId`zwraca wszystkie urządzenia bezpośrednio dołączone do mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down`zwraca wszystkie urządzenia dołączone do mySpaceId lub jednego z jego obiektów podrzędnych.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true`zwraca wszystkie urządzenia dołączone do elementów podrzędnych elementu mySpaceId, z wyłączeniem mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true`zwraca wszystkie urządzenia dołączone do bezpośrednich elementów podrzędnych mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Up&maxLevel=-1&maxRelative=true`zwraca wszystkie urządzenia dołączone do jednego z elementów nadrzędnych mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&maxLevel=5`zwraca wszystkie urządzenia dołączone do elementów podrzędnych mySpaceId, które są na poziomie mniejszym lub równym 5.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true`zwraca wszystkie urządzenia dołączone do miejsc, które znajdują się na tym samym poziomie co mySpaceId.


## <a name="odata-support"></a>Obsługa protokołu OData

Większość interfejsów API, które zwracają kolekcje, takie jak wywołanie GET w systemie/Spaces, obsługują następujący podzestaw ogólnych opcji zapytania systemu [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData) :  

* **$filter**
* **$orderby** 
* **$top**
* **$Skip** — Jeśli zamierzasz wyświetlić całą kolekcję, należy zażądać jej jako całego zestawu w pojedynczym wywołaniu, a następnie wykonać stronicowanie w aplikacji. 

> [!NOTE]
> Niektóre opcje OData (takie jak opcje zapytania **$Count**, **$expand**i **$Search**) nie są obecnie obsługiwane.

### <a name="examples"></a>Przykłady

Poniższa lista przedstawia kilka zapytań z prawidłową składnią OData:

- `YOUR_MANAGEMENT_API_URL/devices?$top=3&$orderby=Name desc`
- `YOUR_MANAGEMENT_API_URL/keystores?$filter=endswith(Description,'space')`
- `YOUR_MANAGEMENT_API_URL/devices?$filter=TypeId eq 2`
- `YOUR_MANAGEMENT_API_URL/resources?$filter=StatusId ne 1`
- `YOUR_MANAGEMENT_API_URL/users?$top=4&$filter=endswith(LastName,'k')&$orderby=LastName`
- `YOUR_MANAGEMENT_API_URL/spaces?$orderby=Name desc&$top=3&$filter=substringof('Floor',Name)`
 
## <a name="next-steps"></a>Następne kroki

Aby poznać typowe wzorce zapytań interfejsu API, przeczytaj artykuł [jak wykonywać zapytania dotyczące interfejsów API Digital bliźniaczych reprezentacji na platformie Azure w celu wykonywania typowych zadań](./how-to-query-common-apis.md).

Aby dowiedzieć się więcej na temat punktów końcowych interfejsu API, zobacz [jak używać programu Digital bliźniaczych reprezentacji Swagger](./how-to-use-swagger.md).

Aby przejrzeć składnię OData i dostępne operatory porównania, Odczytaj [Operatory porównania OData na platformie Azure wyszukiwanie poznawcze](../search/search-query-odata-comparison-operators.md).
