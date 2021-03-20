---
title: Filtry aspektów dla nawigacji wyszukiwania w aplikacjach
titleSuffix: Azure Cognitive Search
description: Kryteria filtrowania według tożsamości zabezpieczeń użytkownika, lokalizacji geograficznej lub wartości liczbowych w celu ograniczenia wyników wyszukiwania zapytań w usłudze Azure Wyszukiwanie poznawcze, hostowanej usługi wyszukiwania w chmurze w Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 9360fc000e01e1c52561cbaa3e2f2968e67e2fa2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91740874"
---
# <a name="how-to-build-a-facet-filter-in-azure-cognitive-search"></a>Jak utworzyć filtr aspektów w usłudze Azure Wyszukiwanie poznawcze 

Nawigacja aspektowa jest używana do samodzielnego filtrowania wyników zapytania w aplikacji wyszukiwania, w której aplikacja oferuje kontrolki interfejsu użytkownika do określania zakresu wyszukiwania do grup dokumentów (na przykład kategorii lub marek), a platforma Azure Wyszukiwanie poznawcze udostępnia strukturę danych w celu przywrócenia tego środowiska. W tym artykule szybko zapoznaj się z podstawowymi krokami dotyczącymi tworzenia struktury nawigacyjnej aspektów, która umożliwia tworzenie kopii zapasowej środowiska wyszukiwania, które chcesz podać. 

> [!div class="checklist"]
> * Wybierz pola do filtrowania i tworzenia aspektów
> * Ustaw atrybuty pola
> * Tworzenie indeksu i ładowanie danych
> * Dodawanie filtrów aspektów do zapytania
> * Obsługa wyników

Zestawy reguł są dynamiczne i zwracane w zapytaniu. Odpowiedzi wyszukiwania przyjmują do nich kategorie aspektów używane do nawigowania po wynikach. Jeśli nie znasz już aspektów, Poniższy przykład jest ilustracją struktury nawigacyjnej aspektu.

:::image type="complex" source="media/search-filters-facets/facet-nav.png" alt-text="filtrowane wyniki wyszukiwania":::
"Obraz przedstawiający okno dialogowe wyszukiwania z przefiltrowanymi wynikami wyszukiwania pogrupowane według tytułu biznesowego. Strzałka wskazuje, że wyniki są zestawami, które są wyświetlane w strukturze nawigacji zestawu reguł " :::image-end:::

Jesteś nowym w nawigacji aspektowej i chcesz uzyskać więcej szczegółów? Zobacz [, jak zaimplementować nawigację aspektową w usłudze Azure wyszukiwanie poznawcze](search-faceted-navigation.md).

## <a name="choose-fields"></a>Wybierz pola

Zestawy reguł można obliczyć na podstawie pojedynczych pól wartości, a także kolekcji. Pola, które najlepiej działają w nawigacji aspektowej, mają niską Kardynalność: niewielką liczbę unikatowych wartości powtarzanych w całym dokumencie w korpus wyszukiwania (na przykład lista kolorów, krajów/regionów lub nazw marek). 

Aspektowanie jest włączane w zależności od pola podczas tworzenia indeksu przez ustawienie `facetable` atrybutu na `true` . Należy również ustawić `filterable` atrybut na `true` dla takich pól, aby aplikacja wyszukiwania mogła filtrować te pola w oparciu o aspekty wybierane przez użytkownika końcowego. 

Podczas tworzenia indeksu przy użyciu interfejsu API REST każdy [Typ pola](/rest/api/searchservice/supported-data-types) , który może być używany w nawigacji aspektowej, jest domyślnie oznaczony jako `facetable` domyślny:

+ `Edm.String`
+ `Edm.DateTimeOffset`
+ `Edm.Boolean`
+ Typy pól liczbowych: `Edm.Int32` , `Edm.Int64` , `Edm.Double`
+ Kolekcje powyższych typów (na przykład `Collection(Edm.String)` lub `Collection(Edm.Double)` )

Nie można używać `Edm.GeographyPoint` ani `Collection(Edm.GeographyPoint)` pól w nawigacji aspektowej. Zestawy reguł działają najlepiej dla pól o niskiej kardynalności. Ze względu na rozdzielczość geograficzną, rzadko jest, że wszystkie dwa zestawy współrzędnych będą równe w danym zestawie danych. W związku z tym aspekty nie są obsługiwane dla współrzędnych geograficznych. Do zestawu reguł według lokalizacji potrzebne jest pole miasto lub region.

## <a name="set-attributes"></a>Ustawianie atrybutów

Atrybuty indeksu kontrolujące sposób użycia pola są dodawane do poszczególnych definicji pól w indeksie. W poniższym przykładzie pola o niskiej kardynalności, przydatne w przypadku aspektów, składają się z: `category` (Hotel, Motel, Hostel), `tags` i `rating` . Te pola mają `filterable` `facetable` jawnie ustawione atrybuty i w poniższym przykładzie dla celów ilustracyjnych. 

> [!Tip]
> Najlepszym rozwiązaniem w przypadku optymalizacji wydajności i magazynu jest wyłączenie reguł dla pól, które nigdy nie powinny być używane jako zestaw reguł. W szczególności pola ciągów dla unikatowych wartości, takich jak identyfikator lub nazwa produktu, powinny mieć ustawioną wartość, aby `"facetable": false` zapobiec przypadkowemu (i nieskutecznym) użyciu w nawigacji aspektowej.


```json
{
  "name": "hotels",  
  "fields": [
    { "name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false },
    { "name": "baseRate", "type": "Edm.Double" },
    { "name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
    { "name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene" },
    { "name": "hotelName", "type": "Edm.String", "facetable": false },
    { "name": "category", "type": "Edm.String", "filterable": true, "facetable": true },
    { "name": "tags", "type": "Collection(Edm.String)", "filterable": true, "facetable": true },
    { "name": "parkingIncluded", "type": "Edm.Boolean",  "filterable": true, "facetable": true, "sortable": false },
    { "name": "smokingAllowed", "type": "Edm.Boolean", "filterable": true, "facetable": true, "sortable": false },
    { "name": "lastRenovationDate", "type": "Edm.DateTimeOffset" },
    { "name": "rating", "type": "Edm.Int32", "filterable": true, "facetable": true },
    { "name": "location", "type": "Edm.GeographyPoint" }
  ]
}
```

> [!Note]
> Ta definicja indeksu jest kopiowana z [tworzenia indeksu wyszukiwanie poznawcze platformy Azure przy użyciu interfejsu API REST](./search-get-started-powershell.md). Jest on identyczny z wyjątkiem powierzchownych różnic w definicjach pól. `filterable`Atrybuty i `facetable` są jawnie dodawane do `category` pól, `tags` , `parkingIncluded` , `smokingAllowed` i `rating` . W `filterable` `facetable` przypadku korzystania z interfejsu API REST w ramach tych pól zostanie włączona wartość domyślna. W przypadku korzystania z zestawu .NET SDK te atrybuty muszą być włączone jawnie.

## <a name="build-and-load-an-index"></a>Kompilowanie i ładowanie indeksu

Krok pośredni (i prawdopodobnie oczywisty) jest konieczny do [kompilowania i wypełniania indeksu](./search-get-started-dotnet.md#1---create-an-index) przed wyrażeniem zapytania. Ten krok jest tutaj omawiany w celu zapewnienia kompletności. Jednym ze sposobów ustalenia, czy indeks jest dostępny, jest sprawdzenie listy indeksów w [portalu](https://portal.azure.com).

## <a name="add-facet-filters-to-a-query"></a>Dodawanie filtrów aspektów do zapytania

W kodzie aplikacji Utwórz zapytanie, które określa wszystkie części prawidłowego zapytania, w tym wyrażenia wyszukiwania, aspekty, filtry, profile oceniania — wszystko używane do tworzenia żądania. Poniższy przykład tworzy żądanie, które tworzy nawigację według zestawu reguł na podstawie typu zakwaterowania, klasyfikacji i innych obiektów.

```csharp
var sp = new SearchParameters()
{
    ...
    // Add facets
    Facets = new[] { "category", "rating", "parkingIncluded", "smokingAllowed" }.ToList()
};
```

### <a name="return-filtered-results-on-click-events"></a>Zwracaj filtrowane wyniki dla zdarzeń kliknięcia

Gdy użytkownik końcowy kliknie wartość aspektu, procedura obsługi dla zdarzenia kliknięcia powinna używać wyrażenia filtru, aby zrealizować intencję użytkownika. Uwzględniając zestaw `category` reguł, kliknięcie kategorii "Motel" jest implementowane za pomocą `$filter` wyrażenia, które wybiera przebudowy tego typu. Gdy użytkownik kliknie przycisk "Motel", aby wskazać, że tylko Motels powinien być pokazywany, następne zapytanie wysyłane przez aplikację zawiera `$filter=category eq 'motel'` .

Poniższy fragment kodu dodaje kategorię do filtru, jeśli użytkownik wybierze wartość z zestawu reguł.

```csharp
if (!String.IsNullOrEmpty(categoryFacet))
    filter = $"category eq '{categoryFacet}'";
```

Jeśli użytkownik kliknie wartość aspektu dla pola kolekcji `tags` , np., na przykład wartość "Pool", aplikacja powinna używać następującej składni filtru: `$filter=tags/any(t: t eq 'pool')`

## <a name="tips-and-workarounds"></a>Porady i obejścia

### <a name="initialize-a-page-with-facets-in-place"></a>Inicjowanie strony z zestawami na miejscu

Jeśli chcesz zainicjować stronę z zestawami na miejscu, można wysłać zapytanie jako część inicjowania strony, aby wypełnić stronę z początkową strukturą aspektu.

### <a name="preserve-a-facet-navigation-structure-asynchronously-of-filtered-results"></a>Zachowywanie struktury nawigacji aspektów asynchronicznie dla filtrowanych wyników

Jednym z wyzwań związanych z nawigacją aspektów na platformie Azure Wyszukiwanie poznawcze jest to, że zestawy reguł istnieją dla bieżących wyników. W tej metodzie często zachowuje się statyczny zestaw aspektów, aby użytkownik mógł nawigować w odwrotnej i prześledzonych krokach w celu eksplorowania ścieżek alternatywnych za pomocą wyszukiwania zawartości. 

Chociaż jest to typowy przypadek użycia, nie jest to coś, co jest obecnie dostępne w strukturze nawigacji aspektów. Deweloperzy, którzy chcą korzystać z zestawów reguł statycznych, zwykle obsłużą ograniczenia, wydając dwie przefiltrowane zapytania: jeden zakres wyników, drugi używany do tworzenia statycznej listy zestawów reguł do nawigacji.

## <a name="see-also"></a>Zobacz też

+ [Filtry na platformie Azure Wyszukiwanie poznawcze](search-filters.md)
+ [Tworzenie interfejsu API REST indeksu](/rest/api/searchservice/create-index)
+ [Interfejs API REST wyszukiwania dokumentów](/rest/api/searchservice/search-documents)