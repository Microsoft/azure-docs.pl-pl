---
title: Wyrażenia stylów oparte na danych w zestawie SDK sieci Web Azure Maps | Mapy Microsoft Azure
description: Dowiedz się więcej na temat wyrażeń stylów opartych na danych. Zobacz, jak używać tych wyrażeń w zestawie SDK sieci Web Azure Maps do dostosowywania stylów w programie Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 4/4/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendleton
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 539145836849bb66bcf1f12a97ea405fe84c47bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91311380"
---
# <a name="data-driven-style-expressions-web-sdk"></a>Wyrażenia stylów oparte na danych (zestaw SDK sieci Web)

Wyrażenia umożliwiają stosowanie logiki biznesowej do opcji stylu, które obserwują właściwości zdefiniowane w poszczególnych kształtach w źródle danych. Wyrażenia mogą odfiltrować dane w źródle danych lub warstwie. Wyrażenia mogą składać się z logiki warunkowej, takich jak if-Statements. I można ich używać do manipulowania danymi przy użyciu: operatory String, operatory logiczne i operatory matematyczne.

Style oparte na danych zmniejszają ilość kodu wymaganą do zaimplementowania logiki biznesowej wokół stylów. W przypadku użycia z warstwami wyrażenia są oceniane w czasie renderowania w osobnym wątku. Ta funkcja zapewnia zwiększoną wydajność w porównaniu do oceny logiki biznesowej w wątku interfejsu użytkownika.

Ten film wideo zawiera omówienie stylów opartych na danych w zestawie SDK sieci Web Azure Maps.

<br/>

<iframe src="https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Wyrażenia są reprezentowane jako tablice JSON. Pierwszy element wyrażenia w tablicy jest ciągiem, który określa nazwę operatora wyrażenia. Na przykład "+" lub "Case". Następne elementy (jeśli istnieją) są argumentami wyrażenia. Każdy argument jest wartością literału (ciągiem, liczbą, wartością logiczną lub `null` ) lub inną tablicą wyrażeń. Poniższy pseudokodzie definiuje podstawową strukturę wyrażenia. 

```javascript
[ 
    expression_operator, 
    argument0, 
    argument1, 
    …
] 
```

Zestaw SDK sieci Web Azure Maps obsługuje wiele typów wyrażeń. Wyrażenia mogą być używane samodzielnie lub w połączeniu z innymi wyrażeniami.

| Typ wyrażeń | Opis |
|---------------------|-------------|
| [Wyrażenie agregujące](#aggregate-expression) | Wyrażenie definiujące obliczenia przetwarzane przez zestaw danych i może być używane z `clusterProperties` opcją elementu `DataSource` . |
| [Wyrażenia logiczne](#boolean-expressions) | Wyrażenia logiczne zawierają zestaw wyrażeń operatorów logicznych do oceniania porównania wartości logicznych. |
| [Wyrażenia koloru](#color-expressions) | Wyrażenia kolorów ułatwiają tworzenie wartości kolorów i manipulowanie nimi. |
| [Wyrażenia warunkowe](#conditional-expressions) | Wyrażenia warunkowe udostępniają operacje logiki, które są podobne do instrukcji if-Statement. |
| [Wyrażenia danych](#data-expressions) | Zapewnia dostęp do danych właściwości w funkcji. |
| [Wyrażenia interpolowane i krokowe](#interpolate-and-step-expressions) | Wyrażenia interpolacji i kroku mogą służyć do obliczania wartości w interpolowanej krzywej lub funkcji kroku. |
| [Wyrażenia specyficzne dla warstwy](#layer-specific-expressions) | Specjalne wyrażenia mające zastosowanie tylko do jednej warstwy. |
| [Wyrażenia matematyczne](#math-expressions) | Udostępnia operatory matematyczne do wykonywania obliczeń opartych na danych w ramach struktury wyrażeń. |
| [Wyrażenia operatora ciągu](#string-operator-expressions) | Wyrażenia operatora ciągu wykonują operacje konwersji na ciągach, takich jak łączenie i konwertowanie wielkości liter. |
| [Wyrażenia typu](#type-expressions) | Wyrażenia typu dostarczają narzędzia do testowania i konwertowania różnych typów danych, takich jak ciągi, liczby i wartości logiczne. |
| [Wyrażenia powiązań zmiennych](#variable-binding-expressions) | Wyrażenia powiązań zmiennych przechowują wyniki obliczeń w zmiennej i występujące w innym miejscu w wyrażeniu wielokrotnie bez konieczności ponownego obliczania przechowywanej wartości. |
| [Wyrażenie powiększenia](#zoom-expression) | Pobiera bieżący poziom powiększenia mapy w czasie renderowania. |

We wszystkich przykładach w tym dokumencie użyto następującej funkcji, aby przedstawić różne sposoby używania różnych typów wyrażeń. 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.13284, 47.63699]
    },
    "properties": { 
        "id": 123,
        "entityType": "restaurant",
        "revenue": 12345,
        "subTitle": "Building 40", 
        "temperature": 72,
        "title": "Cafeteria", 
        "zoneColor": "red",
        "abcArray": ['a', 'b', 'c'],
        "array2d": [['a', 'b'], ['x', 'y']],
        "_style": {
            "fillColor": 'red'
        }
    }
}
```

## <a name="data-expressions"></a>Wyrażenia danych

Wyrażenia danych zapewniają dostęp do danych właściwości w funkcji. 

| Wyrażenie | Typ zwracany | Opis |
|------------|-------------|-------------|
| `['at', number, array]` | object | Pobiera element z tablicy. |
| `['geometry-type']` | ciąg | Pobiera typ geometrii funkcji: punkt, MultiPoint, LineString, MultiLineString, Wielokąt, MultiPolygon. |
| `['get', string]` | value | Pobiera wartość właściwości z właściwości bieżącej funkcji. Zwraca wartość null, jeśli brakuje żądanej właściwości. |
| `['get', string, object]` | value | Pobiera wartość właściwości z właściwości podanego obiektu. Zwraca wartość null, jeśli brakuje żądanej właściwości. |
| `['has', string]` | boolean | Określa, czy właściwości funkcji mają określoną właściwość. |
| `['has', string, object]` | boolean | Określa, czy właściwości obiektu mają określoną właściwość. |
| `['id']` | value | Pobiera identyfikator funkcji, jeśli ma. |
| `['length', string | array]` | liczba | Pobiera długość ciągu lub tablicy. |
| `['in', boolean | string | number, array]` | boolean | Określa, czy element istnieje w tablicy |
| `['in', substring, string]` | boolean | Określa, czy podciąg istnieje w ciągu |
| `['index-of', boolean | string | number, array | string]`<br/><br/>`['index-of', boolean | string | number, array | string, number]` | liczba | Zwraca pierwszą pozycję, w której element można znaleźć w tablicy lub podciąg można znaleźć w ciągu lub w `-1` przypadku, gdy nie można znaleźć danych wejściowych. Akceptuje opcjonalny indeks z lokalizacji, w której ma zostać rozpoczęte wyszukiwanie. |
| `['slice', array | string, number]`<br/><br/>`['slice', array | string, number, number]` | `string`\|Tablica | Zwraca element z tablicy lub podciągu z ciągu z określonego indeksu początkowego lub między indeksem początkowym i indeksem końcowym, jeśli został ustawiony. Wartość zwracana jest włącznie z indeksem początkowym, ale nie indeksem końcowym. |

**Przykłady**

Do właściwości funkcji można uzyskać dostęp bezpośrednio w wyrażeniu przy użyciu `get` wyrażenia. W tym przykładzie użyta jest wartość "zoneColor" funkcji, aby określić Właściwość Color warstwy bąbelkowej. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: ['get', 'zoneColor'] //Get the zoneColor value.
});
```

Powyższy przykład będzie działał prawidłowo, jeśli wszystkie funkcje punktu mają `zoneColor` Właściwość. Jeśli nie, kolor będzie prawdopodobnie zawracać do "czerni". Aby zmodyfikować kolor rezerwowy, użyj `case` wyrażenia w połączeniu z `has` wyrażeniem, aby sprawdzić, czy właściwość istnieje. Jeśli właściwość nie istnieje, zwróć kolor rezerwowy.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case', //Use a conditional case expression.

        ['has', 'zoneColor'],   //Check to see if feature has a "zoneColor" property
        ['get', 'zoneColor'],   //If it does, use it.

        'blue'  //If it doesn't, default to blue.
    ]
});
```

Warstwy bąbelków i symboli domyślnie renderują współrzędne wszystkich kształtów w źródle danych. Takie zachowanie może wyróżnić wierzchołki wielokąta lub linii. `filter`Opcja warstwy może służyć do ograniczenia typu geometrii funkcji, które renderuje, przy użyciu `['geometry-type']` wyrażenia w wyrażeniu logicznym. Poniższy przykład ogranicza warstwę bąbelków, tak aby `Point` były renderowane tylko funkcje.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['==', ['geometry-type'], 'Point']
});
```

Poniższy przykład umożliwia `Point` `MultiPoint` renderowanie obu i funkcji. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: ['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]
});
```

Podobnie konspekt wielokątów będzie renderowany w warstwach liniowych. Aby wyłączyć to zachowanie w warstwie liniowej, Dodaj filtr, który umożliwia tylko `LineString` `MultiLineString` funkcje i.  

Oto kilka dodatkowych przykładów użycia wyrażeń danych:

```javascript
//Get item [2] from an array "properties.abcArray[1]" = "c"
['at', 2, ['get', 'abcArray']]

//Get item [0][1] from a 2D array "properties.array2d[0][1]" = "b"
['at', 1, ['at', 0, ['get', 'array2d']]]

//Check to see if a value is in an array "properties.abcArray.indexOf('a') !== -1" = true
['in', 'a', ['get', 'abcArray']]

//Gets the index of the value 'b' in an array "properties.abcArray.indexOf('b')" = 1
['index-of', 'b', ['get', 'abcArray']]

//Get the length of an array "properties.abcArray.length" = 3
['length', ['get', 'abcArray']]

//Get the value of a subproperty "properties._style.fillColor" = "red"
['get', 'fillColor', ['get', '_style']]

//Check that "fillColor" exists as a subproperty of "_style".
['has', 'fillColor', ['get', '_style']]

//Slice an array starting at index 2 "properties.abcArray.slice(2)" = ['c']
['slice', ['get', 'abcArray'], 2]

//Slice a string from index 0 to index 4 "properties.entityType.slice(0, 4)" = 'rest'
['slice', ['get', 'entityType'], 0, 4]
```

## <a name="math-expressions"></a>Wyrażenia matematyczne

Wyrażenia matematyczne zapewniają operatory matematyczne do wykonywania obliczeń opartych na danych w ramach struktury wyrażeń.

| Wyrażenie | Typ zwracany | Opis |
|------------|-------------|-------------|
| `['+', number, number, …]` | liczba | Oblicza sumę podanych liczb. |
| `['-', number]` | liczba | Odejmuje 0 według podanej liczby. |
| `['-', number, number]` | liczba | Odejmuje pierwsze numery według drugiej liczby. |
| `['*', number, number, …]` | liczba | Mnoży określone liczby razem. |
| `['/', number, number]` | liczba | Dzieli pierwszą liczbę przez drugą liczbę. |
| `['%', number, number]` | liczba | Oblicza resztę podczas dzielenia pierwszej liczby przez drugą liczbę. |
| `['^', number, number]` | liczba | Oblicza wartość pierwszej wartości podniesioną do potęgi drugiej liczby. |
| `['abs', number]` | liczba | Oblicza wartość bezwzględną podanej liczby. |
| `['acos', number]` | liczba | Oblicza arcus cosinus podanej liczby. |
| `['asin', number]` | liczba | Oblicza arcus sinus dla podanej liczby. |
| `['atan', number]` | liczba | Oblicza arcus tangens podanej liczby. |
| `['ceil', number]` | liczba | Zaokrągla liczbę w górę do najbliższej liczby całkowitej. |
| `['cos', number]` | liczba | Oblicza cos o podanej liczbie. |
| `['e']` | liczba | Zwraca stałą matematyczną `e` . |
| `['floor', number]` | liczba | Zaokrągla liczbę w dół do poprzedniej wartości całkowitej. |
| `['ln', number]` | liczba | Oblicza logarytm naturalny podanej liczby. |
| `['ln2']` | liczba | Zwraca stałą matematyczną `ln(2)` . |
| `['log10', number]` | liczba | Oblicza logarytm dziesiętny dla podanej liczby. |
| `['log2', number]` | liczba | Oblicza logarytm dziesiętny dla podanej liczby. |
| `['max', number, number, …]` | liczba | Oblicza maksymalną liczbę w określonym zestawie liczb. |
| `['min', number, number, …]` | liczba | Oblicza minimalną liczbę w określonym zestawie liczb. |
| `['pi']` | liczba | Zwraca stałą matematyczną `PI` . |
| `['round', number]` | liczba | Zaokrągla liczbę do najbliższej liczby całkowitej. Połowy wartości są zaokrąglane w kierunku od zera. Na przykład program `['round', -1.5]` oblicza wartość-2. |
| `['sin', number]` | liczba | Oblicza sinus dla podanej liczby. |
| `['sqrt', number]` | liczba | Oblicza pierwiastek kwadratowy z podanej liczby. |
| `['tan', number]` | liczba | Oblicza tangens podanej liczby. |

## <a name="aggregate-expression"></a>Wyrażenie agregujące

Wyrażenie agregujące definiuje obliczenia przetwarzane przez zestaw danych i może być używane z `clusterProperties` opcją elementu `DataSource` . Wynik tych wyrażeń musi być liczbą lub wartością logiczną. 

Wyrażenie agregujące przyjmuje trzy wartości: wartość operatora i wartość początkową oraz wyrażenie służące do pobierania właściwości z poszczególnych funkcji w danych w celu zastosowania operacji agregowania. To wyrażenie ma następujący format:

```javascript
[operator: string, initialValue: boolean | number, mapExpression: Expression]
```

- operator: funkcja wyrażenia, która następnie jest stosowana do wszystkich wartości obliczonych przez `mapExpression` dla każdego punktu w klastrze. Obsługiwane operatory: 
    - Dla liczb: `+` , `*` , `max` , `min`
    - W przypadku wartości logicznych: `all` , `any`
- initialValue: wartość początkowa, w której agregowana jest pierwsza wartość obliczeniowa.
- mapExpression: wyrażenie, które jest stosowane do każdego punktu w zestawie danych.

**Przykłady**

Jeśli wszystkie funkcje w zestawie danych mają `revenue` Właściwość, która jest liczbą. Następnie można obliczyć łączny przychód wszystkich punktów w klastrze, które są tworzone na podstawie zestawu danych. To obliczenie jest wykonywane przy użyciu następującego wyrażenia agregującego: `['+', 0, ['get', 'revenue']]`

## <a name="boolean-expressions"></a>Wyrażenia logiczne

Wyrażenia logiczne zawierają zestaw wyrażeń operatorów logicznych do oceniania porównania wartości logicznych.

Porównując wartości, porównanie jest ściśle wpisane. Wartości różnych typów są zawsze uznawane za nierówne. Przypadki, w których typy muszą być różne w czasie analizy, są uważane za nieprawidłowe i spowodują błąd analizy. 

| Wyrażenie | Typ zwracany | Opis |
|------------|-------------|-------------|
| `['!', boolean]` | boolean | Negacja logiczna. Zwraca wartość `true` , jeśli dane wejściowe to `false` , i `false` Jeśli dane wejściowe to `true` . |
| `['!=', value, value]` | boolean | Zwraca wartość `true` , jeśli wartości wejściowe nie są równe `false` . w przeciwnym razie. |
| `['<', value, value]` | boolean | Zwraca `true` czy pierwsze dane wejściowe są absolutnie mniejsze od drugiego, `false` w przeciwnym razie. Argumenty muszą być zarówno ciągami, jak i obiema numerami. |
| `['<=', value, value]` | boolean | Zwraca wartość `true` , jeśli pierwsze dane wejściowe są mniejsze lub równe sekundzie, `false` w przeciwnym razie. Argumenty muszą być zarówno ciągami, jak i obiema numerami. |
| `['==', value, value]` | boolean | Zwraca wartość `true` , jeśli wartości wejściowe są równe `false` . w przeciwnym razie. Argumenty muszą być zarówno ciągami, jak i obiema numerami. |
| `['>', value, value]` | boolean | Zwraca `true` czy pierwsze dane wejściowe są absolutnie większe od drugiego, `false` w przeciwnym razie. Argumenty muszą być zarówno ciągami, jak i obiema numerami. |
| `['>=' value, value]` | boolean | Zwraca wartość `true` , jeśli pierwsze dane wejściowe są większe lub równe drugiemu `false` . w przeciwnym razie. Argumenty muszą być zarówno ciągami, jak i obiema numerami. |
| `['all', boolean, boolean, …]` | boolean | Zwraca `true` czy wszystkie dane wejściowe są `true` , `false` w przeciwnym razie. |
| `['any', boolean, boolean, …]` | boolean | Zwraca wartość `true` , jeśli którekolwiek z danych wejściowych nie są `true` , `false` w przeciwnym razie. |

## <a name="conditional-expressions"></a>Wyrażenia warunkowe

Wyrażenia warunkowe udostępniają operacje logiki, które są podobne do instrukcji if-Statement.

Następujące wyrażenia wykonują warunkowe operacje logiki na danych wejściowych. Na przykład `case` wyrażenie zawiera logikę "if/then/else", podczas gdy `match` wyrażenie jest podobne do "Switch-Statement". 

### <a name="case-expression"></a>Wyrażenie CASE

`case`Wyrażenie jest typem wyrażenia warunkowego, które udostępnia logikę "if/then/else". Tego typu wyrażenie jest wykonywane przez listę warunków logicznych. Zwraca wartość wyjściową pierwszego warunku logicznego, który ma zostać obliczony na wartość true.

Poniższy pseudokodzie definiuje strukturę `case` wyrażenia. 

```javascript
[
    'case',
    condition1: boolean, 
    output1: value,
    condition2: boolean, 
    output2: value,
    ...,
    fallback: value
]
```

**Przykład**

Poniższy przykład przechodzi przez inne warunki logiczne do momentu znalezienia jednego, który jest obliczany przez `true` , a następnie zwraca tę skojarzoną wartość. Jeśli warunek logiczny nie zostanie obliczony `true` , zostanie zwrócona wartość rezerwowa. 

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'case',

        //Check to see if the first boolean expression is true, and if it is, return its assigned result.
        ['has', 'zoneColor'],
        ['get', 'zoneColor'],

        //Check to see if the second boolean expression is true, and if it is, return its assigned result.
        ['all', ['has', ' temperature '], ['>', ['get', 'temperature'], 100]],
        'red',

        //Specify a default value to return.
        'green'
    ]
});
```

### <a name="match-expression"></a>Wyrażenie dopasowania

`match`Wyrażenie jest typem wyrażenia warunkowego, które zawiera instrukcję Switch, taką jak Logic. Wejście może być dowolnym wyrażeniem, takim jak `['get', 'entityType']` , które zwraca ciąg lub liczbę. Każda etykieta musi być pojedynczą wartością literału lub tablicą wartości literałów, których wartości muszą być wszystkie ciągami lub liczbami. Dane wejściowe są zgodne, Jeśli dowolne wartości w tablicy pasują do siebie. Każda etykieta musi być unikatowa. Jeśli typ danych wejściowych nie jest zgodny z typem etykiet, wynik będzie wartością rezerwową.

Poniższy pseudokodzie definiuje strukturę `match` wyrażenia. 

```javascript
[
    'match',
    input: number | string,
    label1: number | string | (number | string)[], 
    output1: value,
    label2: number | string | (number | string)[], 
    output2: value,
    ...,
    fallback: value
]
```

**Przykłady**

Poniższy przykład sprawdza, czy `entityType` Właściwość funkcji punktu w warstwie bąbelka wyszukuje dopasowanie. Jeśli znajdzie dopasowanie, zostanie zwrócona określona wartość lub zwróci wartość rezerwową.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        'restaurant', 'red',
        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

W poniższym przykładzie zastosowano tablicę, aby wyświetlić zestaw etykiet, które powinny zwracać tę samą wartość. Takie podejście jest znacznie bardziej wydajne niż wymienianie każdej etykiety osobno. W takim przypadku, jeśli `entityType` Właściwość ma wartość "restauracji" lub "grocery_store", zostanie zwrócony kolor "Red".

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'match',

        //Get the property to match.
        ['get', 'entityType'],

        //List the values to match and the result to return for each match.
        ['restaurant', 'grocery_store'], 'red',

        'park', 'green',

        //Specify a default value to return if no match is found.
        'black'
    ]
});
```

Poniższy przykład używa wyrażenia dopasowania, aby wykonać filtr typu "in Array" lub "Array Contains". W takim przypadku wyrażenie filtruje dane, które mają wartość identyfikatora, która znajduje się na liście dozwolonych identyfikatorów. W przypadku używania wyrażeń z filtrami wynik musi być wartością logiczną.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    filter: [
        'match',  

        //Get the property to match.
        ['get', 'id'],  

         //List of values to match.
        [24, 53, 98], 

        //If there is a match, return true.
        true,
    
        //Otherwise return false.
        false
    ]
});
```

### <a name="coalesce-expression"></a>Wyrażenie łączenia

`coalesce`Wyrażenie jest wykonywane przez zestaw wyrażeń do momentu uzyskania pierwszej wartości innej niż null i zwrócenie tej wartości. 

Poniższy pseudokodzie definiuje strukturę `coalesce` wyrażenia. 

```javascript
[
    'coalesce', 
    value1, 
    value2, 
    …
]
```

**Przykład**

Poniższy przykład używa wyrażenia, `coalesce` Aby ustawić `textField` opcję warstwy symboli. Jeśli `title` brakuje właściwości w funkcji lub ustawiono ją `null` , wyrażenie będzie próbować wyszukać `subtitle` Właściwość, jeśli jej brakuje lub `null` , spowoduje powrót do pustego ciągu. 

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'coalesce',

            //Try getting the title property.
            ['get', 'title'],

            //If there is no title, try getting the subtitle. 
            ['get', 'subtitle'],

            //Default to an empty string.
            ''
        ]
    }
});
```

Poniższy przykład używa wyrażenia, `coalesce` Aby pobrać pierwszą dostępną ikonę obrazu dostępną na ikonie Sprite mapy z listy określonych nazw obrazów.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    iconOptions: {
        image: [
            'coalesce',

            //Try getting the image with id 'missing-image'.
            ['image', 'missing-image'],

            //Specify an image id to fallback to. 
            'marker-blue'
        ]
    }
});
``` 

## <a name="type-expressions"></a>Wyrażenia typu

Wyrażenia typu dostarczają narzędzia do testowania i konwertowania różnych typów danych, takich jak ciągi, liczby i wartości logiczne.

| Wyrażenie | Typ zwracany | Opis |
|------------|-------------|-------------|
| `['literal', array]`<br/><br/>`['literal', object]` | Array — \| obiekt | Zwraca tablicę literałową lub wartość obiektu. Użyj tego wyrażenia, aby zapobiec ocenie tablicy lub obiektu jako wyrażenia. Jest to konieczne, gdy tablica lub obiekt musi zostać zwrócony przez wyrażenie. |
| `['image', string]` | ciąg | Sprawdza, czy określony identyfikator obrazu jest ładowany do Sprite obrazu mapy. Jeśli jest, zwracany jest identyfikator, w przeciwnym razie zwracana jest wartość null. |
| `['to-boolean', value]` | boolean | Konwertuje wartość wejściową do wartości logicznej. Wynikiem jest to, `false` że dane wejściowe są ciągiem pustym, `0` ,, `false` `null` lub `NaN` ; w przeciwnym razie `true` . |
| `['to-color', value]`<br/><br/>`['to-color', value1, value2…]` | color | Konwertuje wartość wejściową na kolor. Jeśli podano wiele wartości, każda z nich jest szacowana w kolejności do momentu uzyskania pierwszej pomyślnej konwersji. Jeśli żadne dane wejściowe nie mogą być konwertowane, wyrażenie jest błędem. |
| `['to-number', value]`<br/><br/>`['to-number', value1, value2, …]` | liczba | Konwertuje wartość wejściową na liczbę, jeśli jest to możliwe. Jeśli dane wejściowe to `null` lub `false` , wynik wynosi 0. Jeśli dane wejściowe to `true` , wynik wynosi 1. Jeśli dane wejściowe są ciągami, są konwertowane na liczbę przy użyciu funkcji [ToNumber](https://tc39.github.io/ecma262/#sec-tonumber-applied-to-the-string-type) String specyfikacji języka ECMAScript. Jeśli podano wiele wartości, każda z nich jest szacowana w kolejności do momentu uzyskania pierwszej pomyślnej konwersji. Jeśli żadne dane wejściowe nie mogą być konwertowane, wyrażenie jest błędem. |
| `['to-string', value]` | ciąg | Konwertuje wartość wejściową na ciąg. Jeśli dane wejściowe to `null` , wynik jest `""` . Jeśli dane wejściowe są wartością logiczną, wynikiem jest `"true"` lub `"false"` . Jeśli dane wejściowe są liczbami, są konwertowane na ciąg przy użyciu funkcji [ToString](https://tc39.github.io/ecma262/#sec-tostring-applied-to-the-number-type) Number specyfikacji języka ECMAScript. Jeśli dane wejściowe są kolorem, są konwertowane na ciąg koloru CSS RGBA `"rgba(r,g,b,a)"` . W przeciwnym razie dane wejściowe są konwertowane na ciąg przy użyciu funkcji [JSON. stringify](https://tc39.github.io/ecma262/#sec-json.stringify) specyfikacji języka ECMAScript. |
| `['typeof', value]` | ciąg | Zwraca ciąg opisujący typ danej wartości. |

> [!TIP]
> Jeśli komunikat o błędzie podobny do `Expression name must be a string, but found number instead. If you wanted a literal array, use ["literal", [...]].` pojawi się w konsoli przeglądarki, oznacza to, że w kodzie znajduje się wyrażenie, które zawiera tablicę, która nie ma ciągu dla swojej pierwszej wartości. Jeśli chcesz, aby wyrażenie zwracało tablicę, zawiń tablicę przy użyciu `literal` wyrażenia. W poniższym przykładzie ustawiono opcję ikony `offset` warstwy symboli, która musi być tablicą zawierającą dwie liczby, przy użyciu `match` wyrażenia, aby wybrać między dwoma wartościami przesunięcia na podstawie wartości  `entityType` właściwości funkcji Point.
>
> ```javascript
> var layer = new atlas.layer.SymbolLayer(datasource, null, {
>     iconOptions: {
>         offset: [
>             'match',
>
>             //Get the entityType value.
>             ['get', 'entityType'],
>
>             //If the entity type is 'restaurant', return a different pixel offset. 
>             'restaurant', ['literal', [0, -10]],
>
>             //Default to value.
>             ['literal', [0, 0]]
>         ]
>     }
> });
> ```

## <a name="color-expressions"></a>Wyrażenia koloru

Wyrażenia kolorów ułatwiają tworzenie wartości kolorów i manipulowanie nimi.

| Wyrażenie | Typ zwracany | Opis |
|------------|-------------|-------------|
| `['rgb', number, number, number]` | color | Tworzy wartość koloru dla składników *czerwony*, *zielony*i *niebieski* , które muszą namieścić się w zakresie od `0` do i `255` i składnika alfa elementu `1` . Jeśli dowolny składnik znajduje się poza zakresem, wyrażenie jest błędem. |
| `['rgba', number, number, number, number]` | color | Tworzy wartość koloru na podstawie *czerwonych*, *zielonych*, *niebieskich* składników, które muszą należeć do zakresu od `0` do `255` , i składnika alfa w zakresie `0` i `1` . Jeśli dowolny składnik znajduje się poza zakresem, wyrażenie jest błędem. |
| `['to-rgba']` | \[Number, Number, Number, Number\] | Zwraca tablicę z czterema elementami zawierającą składniki *czerwony*, *zielony*, *niebieski*i *alfa* koloru wejścia w tej kolejności. |

**Przykład**

W poniższym przykładzie jest tworzona wartość koloru RGB, która ma *czerwoną* wartość `255` , oraz wartości *zielony* i *niebieski* , które są obliczane przez pomnożenie `2.5` przez wartość `temperature` właściwości. W miarę zmieniania temperatury kolor zmieni się na różne odcienie *czerwieni*.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'rgb', //Create a RGB color value.

        255,    //Set red value to 255.

        ['*', 2.5, ['get', 'temperature']], //Multiple the temperature by 2.5 and set the green value.

        ['*', 2.5, ['get', 'temperature']]  //Multiple the temperature by 2.5 and set the blue value.
    ]
});
```

## <a name="string-operator-expressions"></a>Wyrażenia operatora ciągu

Wyrażenia operatora ciągu wykonują operacje konwersji na ciągach, takich jak łączenie i konwertowanie wielkości liter. 

| Wyrażenie | Typ zwracany | Opis |
|------------|-------------|-------------|
| `['concat', string, string, …]` | ciąg | Łączy wiele ciągów ze sobą. Każda wartość musi być ciągiem. Użyj `to-string` wyrażenia typu, aby przekonwertować inne typy wartości na ciąg w razie potrzeby. |
| `['downcase', string]` | ciąg | Konwertuje określony ciąg na małe litery. |
| `['upcase', string]` | ciąg | Konwertuje określony ciąg na wielkie litery. |

**Przykład**

Poniższy przykład konwertuje `temperature` Właściwość punktu na ciąg, a następnie łączy "°f" na końcu.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: ['concat', ['to-string', ['get', 'temperature']], '°F'],

        //Some additional style options.
        offset: [0, -1.5],
        size: 12,
        color: 'white'
    }
});
```

Powyższe wyrażenie renderuje kod PIN na mapie z tekstem "64 °F" na początku, jak pokazano na poniższej ilustracji.

<center>

![Przykład ](media/how-to-expressions/string-operator-expression.png) wyrażenia operatora ciągu </center>

## <a name="interpolate-and-step-expressions"></a>Wyrażenia interpolowane i krokowe

Wyrażenia interpolacji i kroku mogą służyć do obliczania wartości w interpolowanej krzywej lub funkcji kroku. Wyrażenia te przyjmują wyrażenie, które zwraca wartość liczbową jako dane wejściowe, na przykład `['get',  'temperature']` . Wartość wejściowa jest oceniana pod kątem par wartości wejściowych i wyjściowych, aby określić wartość, która najlepiej pasuje do interpolowanej krzywej lub funkcji Step. Wartości wyjściowe są nazywane "zatrzymania". Wartości wejściowe dla każdego zatrzymania muszą być liczbą i być uporządkowane rosnąco. Wartości wyjściowe muszą być liczbami, tablicami liczb lub kolorem.

### <a name="interpolate-expression"></a>Wyrażenie interpolacji

`interpolate`Wyrażenie może służyć do obliczania ciągłego, gładkiego zestawu wartości poprzez interpolację między wartościami zatrzymania. `interpolate`Wyrażenie zwracające wartości koloru generuje kolor gradientu, w którym są wybierane wartości wyniku.

Istnieją trzy typy metod interpolacji, których można użyć w `interpolate` wyrażeniu:
 
* `['linear']` — Interpoluje liniowo między parą przerwań.
* `['exponential', base]` — Interpolowane wykładniczo między zatrzymania. `base`Wartość określa szybkość, z jaką wzrasta dane wyjściowe. Wyższe wartości sprawiają, że dane wyjściowe zwiększają się w kierunku górnego końca zakresu. `base`Wartość bliska 1 powoduje utworzenie danych wyjściowych, które zwiększają się bardziej liniowo.
* `['cubic-bezier', x1, y1, x2, y2]` — Interpoluje przy użyciu [zakrzywionej krzywej Beziera](https://developer.mozilla.org/docs/Web/CSS/timing-function) zdefiniowanej przez podaną punkty kontrolne.

Oto przykład sposobu, w jaki wyglądają te różne typy interpolacji. 

| Liniowe  | Wykładniczy | Metria sześcienne |
|---------|-------------|--------------|
| ![Wykres interpolacji liniowej](media/how-to-expressions/linear-interpolation.png) | ![Wykres interpolacji wykładniczej](media/how-to-expressions/exponential-interpolation.png) | ![Wykres interpolacji "sześciennych"](media/how-to-expressions/bezier-curve-interpolation.png) |

Poniższy pseudokodzie definiuje strukturę `interpolate` wyrażenia. 

```javascript
[
    'interpolate',
    interpolation: ['linear'] | ['exponential', base] | ['cubic-bezier', x1, y1, x2, y2],
    input: number,
    stopInput1: number, 
    stopOutput1: value1,
    stopInput2: number, 
    stopOutput2: value2, 
    ...
]
```

**Przykład**

Poniższy przykład używa wyrażenia, `linear interpolate` Aby ustawić `color` Właściwość warstwy bąbelkowej na podstawie `temperature` właściwości funkcji Point. W przypadku `temperature` wartości mniejszej niż 60 zostanie zwrócona wartość "niebieska". Jeśli jest to od 60 do 70, zostaną zwrócone żółte. Jeśli wartość jest z zakresu od 70 do 80, zostanie zwrócony "pomarańczowy". Jeśli jest to 80 lub więcej, zostanie zwrócony czerwony znak "Red".

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['get', 'temperature'],
        50,        
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

Na poniższej ilustracji przedstawiono, jak kolory są wybierane dla powyższego wyrażenia.
 
<center>

![Przykład ](media/how-to-expressions/interpolate-expression-example.png) wyrażenia interpolacji </center>

### <a name="step-expression"></a>Wyrażenie kroku

`step`Wyrażenie może służyć do obliczania dyskretnych, ocenianych wartości wyniku przez obliczenie [funkcji stałej rozkład elementowy](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) zdefiniowanej przez zatrzymywanie. 

Poniższy pseudokodzie definiuje strukturę `step` wyrażenia. 

```javascript
[
    'step',
    input: number,
    output0: value0,
    stop1: number, 
    output1: value1,
    stop2: number, 
    output2: value2, 
    ...
]
```

Wyrażenia kroków zwracają wartość wyjściową elementu Stop tuż przed wartością wejściową lub pierwszą wartość wejściową, jeśli dane wejściowe są mniejsze niż pierwsze zatrzymanie. 

**Przykład**

Poniższy przykład używa wyrażenia, `step` Aby ustawić `color` Właściwość warstwy bąbelkowej na podstawie `temperature` właściwości funkcji Point. W przypadku `temperature` wartości mniejszej niż 60 zostanie zwrócona wartość "niebieska". Jeśli wartość jest z zakresu od 60 do 70, zostanie zwrócony "żółty". Jeśli wartość jest z zakresu od 70 do 80, zostanie zwrócony "pomarańczowy". Jeśli jest to 80 lub więcej, zostanie zwrócony czerwony znak "Red".

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        'step',
        ['get', 'temperature'],
        'blue',
        60,
        'yellow',
        70,
        'orange',
        80,
        'red'
    ]
});
```

Na poniższej ilustracji przedstawiono, jak kolory są wybierane dla powyższego wyrażenia.
 
<center>

![Przykład wyrażenia kroku](media/how-to-expressions/step-expression-example.png)
</center>

## <a name="layer-specific-expressions"></a>Wyrażenia specyficzne dla warstwy

Specjalne wyrażenia mające zastosowanie tylko do określonych warstw.

### <a name="heat-map-density-expression"></a>Wyrażenie gęstości mapy cieplnej

Wyrażenie gęstości mapy cieplnej Pobiera wartość gęstości mapy cieplnej dla każdego piksela w warstwie mapy cieplnej i jest definiowana jako `['heatmap-density']` . Ta wartość jest liczbą z zakresu od `0` do `1` . Jest on używany w połączeniu z `interpolation` `step` wyrażeniem or, aby zdefiniować gradient koloru używany do kolorowania mapy cieplnej. Tego wyrażenia można używać tylko w [opcji Color](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions#color) warstwy mapy cieplnej.

> [!TIP]
> Kolor pod indeksem 0, w wyrażeniu interpolacji lub domyślnym kolorem kroku, definiuje kolor obszaru, w którym nie ma żadnych danych. Kolor pod indeksem 0 może służyć do definiowania koloru tła. Wiele woli ustawić tę wartość na przezroczystą lub częściowo przezroczystą czerń.

**Przykład**

W tym przykładzie zastosowano wyrażenie interpolacji liniowej w celu utworzenia gładkiego gradientu koloru na potrzeby renderowania mapy cieplnej. 

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'interpolate',
        ['linear'],
        ['heatmap-density'],
        0, 'transparent',
        0.01, 'purple',
        0.5, '#fb00fb',
        1, '#00c3ff'
    ]
});
```

Oprócz używania gładkiego gradientu do kolorowania mapy cieplnej, kolory można określić w ramach zestawu zakresów przy użyciu `step` wyrażenia. Użycie `step` wyrażenia do kolorowania mapy cieplnej powoduje, że rozdzieli gęstość na zakresy podobne do mapy stylu konturu lub wykresu radarowego.  

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    color: [
        'step',
        ['heatmap-density'],
        'transparent',
        0.01, 'navy',
        0.25, 'navy',
        0.5, 'green',
        0.75, 'yellow',
        1, 'red'
    ]
});
```

Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [dodawania do warstwy mapy cieplnej](map-add-heat-map-layer.md) .

### <a name="line-progress-expression"></a>Wyrażenie postępu wiersza

Wyrażenie postępu wiersza pobiera postęp wzdłuż linii gradientu w warstwie liniowej i jest zdefiniowane jako `['line-progress']` . Ta wartość jest liczbą z zakresu od 0 do 1. Jest on używany w połączeniu z `interpolation` `step` wyrażeniem or. Tego wyrażenia można używać tylko z [opcją strokeGradient]( https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions#strokegradient) warstwy liniowej. 

> [!NOTE]
> `strokeGradient`Opcja warstwy liniowej wymaga, `lineMetrics` Aby opcja źródła danych była ustawiona na wartość `true` .

**Przykład**

Ten przykład używa `['line-progress']` wyrażenia, aby zastosować gradient koloru do obrysu wiersza.

```javascript
var layer = new atlas.layer.LineLayer(datasource, null, {
    strokeGradient: [
        'interpolate',
        ['linear'],
        ['line-progress'],
        0, "blue",
        0.1, "royalblue",
        0.3, "cyan",
        0.5, "lime",
        0.7, "yellow",
        1, "red"
    ]
});
```

[Zobacz przykład na żywo](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Wyrażenie formatu pola tekstowego

Wyrażenie formatu pola tekstowego może być używane z `textField` opcją właściwości warstwy symboli, `textOptions` Aby zapewnić formatowanie tekstu mieszanego. To wyrażenie umożliwia określenie zestawu ciągów wejściowych i opcji formatowania. Dla każdego ciągu wejściowego w tym wyrażeniu można określić następujące opcje.

 * `'font-scale'` -Określa współczynnik skalowania dla rozmiaru czcionki. Jeśli ta wartość jest określona, spowoduje to zastąpienie `size` właściwości `textOptions` dla pojedynczego ciągu.
 * `'text-font'` -Określa co najmniej jedną rodzinę czcionek, która ma być używana dla tego ciągu. Jeśli ta wartość jest określona, spowoduje to zastąpienie `font` właściwości `textOptions` dla pojedynczego ciągu.
 * `'text-color'` -Określa kolor, który ma zostać zastosowany do tekstu podczas renderowania. 

Poniższy pseudokodzie definiuje strukturę wyrażenia formatu pola tekstowego. 

```javascript
[
    'format', 
    input1: string, 
    options1: { 
        'font-scale': number, 
        'text-font': string[],
        'text-color': color
    },
    input2: string, 
    options2: { 
        'font-scale': number, 
        'text-font': string[] ,
        'text-color': color
    },
    …
]
```

**Przykład**

Poniższy przykład formatuje pole tekstowe przez dodanie pogrubionej czcionki i skalowanie w górę rozmiaru czcionki `title` właściwości funkcji. Ten przykład dodaje również `subtitle` Właściwość funkcji w wierszu wiersza, z skalowanej czcionki o rozmiarze i kolorem czerwonym.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'format',

            //Bold the title property and scale its font size up.
            ['get', 'title'],
            {
                'text-font': ['literal', ['StandardFont-Bold']],
                'font-scale': 1.25
            },

            '\n', {},   //Add a new line without any formatting.

            //Scale the font size down of the subtitle property. 
            ['get', 'subtitle'],
            { 
                'font-scale': 0.75, 
                'text-color': 'red' 
            }
        ]
    }
});
```

Ta warstwa będzie renderować funkcję Point, jak pokazano na poniższej ilustracji:
 
<center>

![Obraz funkcji punktu z sformatowanym polem ](media/how-to-expressions/text-field-format-expression.png) tekstowym </center>

### <a name="number-format-expression"></a>Wyrażenie formatu liczbowego

`number-format`Wyrażenia można używać tylko z `textField` opcją warstwy symboli. To wyrażenie konwertuje podaną liczbę na sformatowany ciąg. To wyrażenie otacza funkcję [Number. ToLocalString](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) języka JavaScript i obsługuje następujący zestaw opcji.

 * `locale` -Należy określić tę opcję, aby przekonwertować liczby na ciągi w sposób, który jest wyrównany do określonego języka. Przekaż [tag języka BCP 47](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Intl#Locale_identification_and_negotiation) do tej opcji.
 * `currency` — Aby przekonwertować liczbę na ciąg reprezentujący walutę. Możliwe wartości to [ISO 4217 kody walutowe](https://en.wikipedia.org/wiki/ISO_4217), takie jak "USD" dla DOLARa USA, "EUR" dla euro lub "CNY" dla CHIŃSKIego RMBu.
 * `'min-fraction-digits'` -Określa minimalną liczbę miejsc dziesiętnych do uwzględnienia w wersji ciągu liczby.
 * `'max-fraction-digits'` -Określa maksymalną liczbę miejsc dziesiętnych do uwzględnienia w wersji ciągu liczby.

Poniższy pseudokodzie definiuje strukturę wyrażenia formatu pola tekstowego. 

```javascript
[
    'number-format', 
    input: number, 
    options: {
        locale: string, 
        currency: string, 
        'min-fraction-digits': number, 
        'max-fraction-digits': number
    }
]
```

**Przykład**

Poniższy przykład używa wyrażenia, `number-format` Aby zmodyfikować sposób `revenue` renderowania właściwości funkcji Point w `textField` opcji warstwy symboli, która jest wyświetlana jako wartość dolara USA.

```javascript
var layer = new atlas.layer.SymbolLayer(datasource, null, {
    textOptions: {
        textField: [
            'number-format', 
            ['get', 'revenue'], 
            { ‘currency': 'USD' }
        ],

        offset: [0, 0.75]
    }
});
```

Ta warstwa będzie renderować funkcję Point, jak pokazano na poniższej ilustracji:

<center>

![Przykład ](media/how-to-expressions/number-format-expression.png) wyrażenia formatu liczbowego </center>

### <a name="image-expression"></a>Wyrażenie obrazu

Wyrażenie obrazu może być używane z `image` `textField` opcjami i dla warstwy symbol oraz `fillPattern` opcją warstwy Wielokąt. To wyrażenie sprawdza, czy żądany obraz istnieje w stylu i zwróci rozpoznaną nazwę obrazu lub `null` , w zależności od tego, czy obraz jest aktualnie w stylu. Ten proces sprawdzania poprawności jest synchroniczny i wymaga dodania obrazu do stylu przed zażądaniem go w argumencie obrazu.

**Przykład**

Poniższy przykład używa wyrażenia, `image` Aby dodać ikonę wbudowaną z tekstem w warstwie symboli. 

```javascript
 //Load the custom image icon into the map resources.
map.imageSprite.add('wifi-icon', 'wifi.png').then(function () {

    //Create a data source and add it to the map.
    datasource = new atlas.source.DataSource();
    map.sources.add(datasource);

    //Create a point feature and add it to the data source.
    datasource.add(new atlas.data.Point(map.getCamera().center));

    //Add a layer for rendering point data as symbols.
    map.layers.add(new atlas.layer.SymbolLayer(datasource, null, {
        iconOptions: {
            image: 'none'
        },
        textOptions: {
            //Create a formatted text string that has an icon in it.
            textField: ["format", 'Ricky\'s ', ["image", "wifi-icon"], ' Palace']
        }
    }));
});
```

Ta warstwa będzie renderować pole tekstowe w warstwie symboli, jak pokazano na poniższej ilustracji:

<center>

![Przykład ](media/how-to-expressions/image-expression.png) wyrażenia obrazu </center>

## <a name="zoom-expression"></a>Wyrażenie powiększenia

`zoom`Wyrażenie służy do pobierania bieżącego poziomu powiększenia mapy w czasie renderowania i jest zdefiniowane jako `['zoom']` . To wyrażenie zwraca liczbę z przedziału minimalnego i maksymalnego poziomu powiększenia mapy. Azure Maps formanty interaktywnej mapy dla sieci Web i Android obsługują 25 poziomów powiększenia, numerowane od 0 do 24. Użycie `zoom` wyrażenia pozwala na dynamiczne modyfikowanie stylów, gdy poziom powiększenia mapy zostanie zmieniony. `zoom`Wyrażenia można używać tylko z `interpolate` `step` wyrażeniami i.

**Przykład**

Domyślnie promienie punktów danych renderowane w warstwie mapy cieplnej mają stały promień pikseli dla wszystkich poziomów powiększenia. W miarę powiększania mapy dane zagregowane i warstwy mapy cieplnej wyglądają inaczej. `zoom`Wyrażenie może służyć do skalowania promienia dla każdego poziomu powiększenia, tak że każdy punkt danych obejmuje ten sam obszar fizyczny mapy. Spowoduje to, że warstwa mapy cieplnej będzie wyglądać bardziej statyczna i spójna. Każdy poziom powiększenia mapy ma dwa piksele w pionie i poziomie jako poprzedni poziom powiększenia. Skalowanie promienia, w taki sposób, że podwaja się z każdym poziomem powiększenia, spowoduje utworzenie mapy cieplnej, która będzie wyglądać spójnie na wszystkich poziomach powiększenia. Można to zrobić przy użyciu `zoom` wyrażenia z `base 2 exponential interpolation` wyrażeniem, z zestawem pikseli ustawionym dla minimalnego poziomu powiększenia i skalowanego promienia dla maksymalnego poziomu powiększenia obliczonego w sposób `2 * Math.pow(2, minZoom - maxZoom)` pokazany poniżej.

```javascript 
var layer = new atlas.layer.HeatMapLayer(datasource, null, {
    radius: [
        'interpolate',
        ['exponential', 2],
        ['zoom'],
        
        //For zoom level 1 set the radius to 2 pixels.
        10, 2,

        //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * Math.pow(2, 19 - 1) pixels (524,288 pixels).
        19, 2 * Math.pow(2, 19 - 1)
    ]
};
```

[Zobacz przykład na żywo](map-add-heat-map-layer.md#consistent-zoomable-heat-map)

## <a name="variable-binding-expressions"></a>Wyrażenia powiązań zmiennych

Wyrażenia powiązań zmiennych przechowują wyniki obliczeń w zmiennej. Tak więc wyniki obliczeń mogą być przywoływane w innym miejscu w wyrażeniu. Jest to przydatna Optymalizacja dla wyrażeń, które obejmują wiele obliczeń.

| Wyrażenie | Typ zwracany | Opis |
|--------------|---------------|--------------|
| \[<br/>&nbsp;&nbsp;&nbsp;&nbsp;"let",<br/>&nbsp;&nbsp;&nbsp;&nbsp;Name1: ciąg,<br/>&nbsp;&nbsp;&nbsp;&nbsp;wartość1: any,<br/>&nbsp;&nbsp;&nbsp;&nbsp;NAME2: ciąg,<br/>&nbsp;&nbsp;&nbsp;&nbsp;wartość2: dowolne,<br/>&nbsp;&nbsp;&nbsp;&nbsp;…<br/>&nbsp;&nbsp;&nbsp;&nbsp;childExpression<br/>\] | | Przechowuje co najmniej jedną wartość jako zmienne do użycia przez `var` wyrażenie w wyrażeniu podrzędnym, które zwraca wynik. |
| `['var', name: string]` | dowolny | Odwołuje się do zmiennej, która została utworzona przy użyciu `let` wyrażenia. |

**Przykład**

W tym przykładzie używane jest wyrażenie, które oblicza przychód względem współczynnika temperatury, a następnie używa `case` wyrażenia, aby oszacować różne operacje logiczne dla tej wartości. `let`Wyrażenie służy do przechowywania przychodu względem współczynnika temperatury, tak aby tylko raz obliczyć tylko raz. `var`Wyrażenie odwołuje się do tej zmiennej tak często, jak to konieczne, bez konieczności ponownego obliczania.

```javascript
var layer = new atlas.layer.BubbleLayer(datasource, null, {
    color: [
        //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
        'let', 'ratio', ['/', ['get', 'revenue'], ['get', 'temperature']],
        //Evaluate the child expression in which the stored variable will be used.
        [
            'case',

            //Check to see if the ratio is less than 100, return 'red'.
            ['<', ['var', 'ratio'], 100],
            'red',

            //Check to see if the ratio is less than 200, return 'green'.
            ['<', ['var', 'ratio'], 200],
            'green',

            //Return `blue` for values greater or equal to 200.
            'blue'
        ]
    ]
});
```

## <a name="next-steps"></a>Następne kroki

Zobacz następujące artykuły, aby uzyskać więcej przykładów kodu, które implementują wyrażenia:

> [!div class="nextstepaction"] 
> [Dodawanie warstwy symboli](map-add-pin.md)

> [!div class="nextstepaction"] 
> [Dodawanie warstwy bąbelkowej](map-add-bubble-layer.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy linii](map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy wielokąta](map-add-shape.md)

> [!div class="nextstepaction"] 
> [Dodawanie warstwy mapy cieplnej](map-add-heat-map-layer.md)

Dowiedz się więcej o opcjach warstwy, które obsługują wyrażenia:

> [!div class="nextstepaction"] 
> [BubbleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.bubblelayeroptions)

> [!div class="nextstepaction"] 
> [HeatMapLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.heatmaplayeroptions)

> [!div class="nextstepaction"] 
> [LineLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.linelayeroptions)

> [!div class="nextstepaction"] 
> [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.polygonlayeroptions)

> [!div class="nextstepaction"] 
> [SymbolLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-control/atlas.symbollayeroptions)
