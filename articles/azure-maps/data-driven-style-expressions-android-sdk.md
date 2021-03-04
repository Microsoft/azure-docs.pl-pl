---
title: Wyrażenia stylów oparte na danych w usłudze mapy systemu Android | Mapy Microsoft Azure
description: Dowiedz się więcej na temat wyrażeń stylów opartych na danych. Zobacz, jak używać tych wyrażeń w Android SDK Azure Maps, aby dostosować style w usłudze Maps.
author: rbrundritt
ms.author: richbrun
ms.date: 12/1/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 7e4af0647a2810a27001c15a5030fca660828147
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102047744"
---
# <a name="data-driven-style-expressions-android-sdk"></a>Wyrażenia w stylu opartym na danych (Android SDK)

Wyrażenia umożliwiają stosowanie logiki biznesowej do opcji stylu, które obserwują właściwości zdefiniowane w poszczególnych kształtach w źródle danych. Wyrażenia mogą odfiltrować dane w źródle danych lub warstwie. Wyrażenia mogą składać się z logiki warunkowej, takich jak if-Statements. I można ich używać do manipulowania danymi przy użyciu: operatory String, operatory logiczne i operatory matematyczne.

Style oparte na danych zmniejszają ilość kodu wymaganą do zaimplementowania logiki biznesowej wokół stylów. W przypadku użycia z warstwami wyrażenia są oceniane w czasie renderowania w osobnym wątku. Ta funkcja zapewnia zwiększoną wydajność w porównaniu do oceny logiki biznesowej w wątku interfejsu użytkownika.

Azure Maps Android SDK obsługuje niemal wszystkie te same wyrażenia stylu co Azure Maps zestaw SDK sieci Web, więc wszystkie te same Koncepcje opisane w [wyrażeniach stylów opartych na danych (zestaw SDK sieci Web)](data-driven-style-expressions-web-sdk.md) mogą być przenoszone do aplikacji systemu Android. Wszystkie wyrażenia stylów w Azure Maps Android SDK są dostępne pod `com.microsoft.azure.maps.mapcontrol.options.Expression` przestrzenią nazw. Istnieje wiele różnych typów wyrażeń stylów.

| Typ wyrażeń | Opis |
|---------------------|-------------|
| [Wyrażenia logiczne](#boolean-expressions) | Wyrażenia logiczne zawierają zestaw wyrażeń operatorów logicznych do oceniania porównania wartości logicznych. |
| [Wyrażenia koloru](#color-expressions) | Wyrażenia kolorów ułatwiają tworzenie wartości kolorów i manipulowanie nimi. |
| [Wyrażenia warunkowe](#conditional-expressions) | Wyrażenia warunkowe udostępniają operacje logiki, które są podobne do instrukcji if-Statement. |
| [Wyrażenia danych](#data-expressions) | Zapewnia dostęp do danych właściwości w funkcji. |
| [Wyrażenia interpolowane i krokowe](#interpolate-and-step-expressions) | Wyrażenia interpolacji i kroku mogą służyć do obliczania wartości w interpolowanej krzywej lub funkcji kroku. |
| [Wyrażenia oparte na notacji JSON](#json-based-expressions) | Ułatwia ponowne używanie wstępnie zdefiniowanych wyrażeń opartych na notacji JSON utworzonych dla zestawu SDK sieci Web przy użyciu Android SDK. |  
| [Wyrażenia specyficzne dla warstwy](#layer-specific-expressions) | Specjalne wyrażenia mające zastosowanie tylko do jednej warstwy. |
| [Wyrażenia matematyczne](#math-expressions) | Udostępnia operatory matematyczne do wykonywania obliczeń opartych na danych w ramach struktury wyrażeń. |
| [Wyrażenia operatora ciągu](#string-operator-expressions) | Wyrażenia operatora ciągu wykonują operacje konwersji na ciągach, takich jak łączenie i konwertowanie wielkości liter. |
| [Wyrażenia typu](#type-expressions) | Wyrażenia typu dostarczają narzędzia do testowania i konwertowania różnych typów danych, takich jak ciągi, liczby i wartości logiczne. |
| [Wyrażenia powiązań zmiennych](#variable-binding-expressions) | Wyrażenia powiązań zmiennych przechowują wyniki obliczeń w zmiennej i występujące w innym miejscu w wyrażeniu wielokrotnie bez konieczności ponownego obliczania przechowywanej wartości. |
| [Wyrażenie powiększenia](#zoom-expression) | Pobiera bieżący poziom powiększenia mapy w czasie renderowania. |

Wszystkie przykłady w tej sekcji dokumentu wykorzystują następującą funkcję, aby przedstawić różne sposoby używania tych wyrażeń.

```json
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
        "temperature": 64,
        "title": "Cafeteria", 
        "zoneColor": "purple",
        "abcArray": ["a", "b", "c"],
        "array2d": [["a", "b"], ["x", "y"]],
        "_style": {
            "fillColor": "red"
        }
    }
}
```

Poniższy kod pokazuje, jak ręcznie utworzyć tę funkcję GEOJSON w aplikacji.

```Java
//Create a point feature.
Feature feature = Feature.fromGeometry(Point.fromLngLat(-100, 45));

//Add properties to the feature.
feature.addNumberProperty("id", 123);
feature.addStringProperty("entityType", "restaurant");
feature.addNumberProperty("revenue", 12345);
feature.addStringProperty("subTitle", "Building 40");
feature.addNumberProperty("temperature", 64);
feature.addStringProperty("title", "Cafeteria");
feature.addStringProperty("zoneColor", "purple");

JsonArray abcArray = new JsonArray();
abcArray.add("a");
abcArray.add("b");
abcArray.add("c");

feature.addProperty("abcArray", abcArray);

JsonArray array1 = new JsonArray();
array1.add("a");
array1.add("b");

JsonArray array2 = new JsonArray();
array1.add("x");
array1.add("y");

JsonArray array2d = new JsonArray();
array2d.add(array1);
array2d.add(array2);

feature.addProperty("array2d", array2d);

JsonObject style = new JsonObject();
style.addProperty("fillColor", "red");

feature.addProperty("_style", style);
```

Poniższy kod ilustruje sposób deserializacji wersji skonwertowanej obiektu JSON do funkcji GEOJSON w aplikacji.

```java
String featureString = "{\"type\":\"Feature\",\"geometry\":{\"type\":\"Point\",\"coordinates\":[-122.13284,47.63699]},\"properties\":{\"id\":123,\"entityType\":\"restaurant\",\"revenue\":12345,\"subTitle\":\"Building 40\",\"temperature\":64,\"title\":\"Cafeteria\",\"zoneColor\":\"purple\",\"abcArray\":[\"a\",\"b\",\"c\"],\"array2d\":[[\"a\",\"b\"],[\"x\",\"y\"]],\"_style\":{\"fillColor\":\"red\"}}}";

Feature feature = Feature.fromJson(featureString);
```

## <a name="json-based-expressions"></a>Wyrażenia oparte na notacji JSON

Zestaw SDK sieci Web Azure Maps obsługuje również wyrażenia stylów oparte na danych, które są reprezentowane za pomocą tablicy JSON. Te same wyrażenia można odtworzyć przy użyciu klasy natywnej `Expression` w Android SDK. Alternatywnie te wyrażenia oparte na notacji JSON można przekonwertować na ciąg za pomocą funkcji sieci Web, takiej jak `JSON.stringify` i przekazaną do `Expression.raw(String rawExpression)` metody. Na przykład Wypełnij poniższe wyrażenie JSON.

```javascript
var exp = ['get','title'];
JSON.stringify(exp); // = "['get','title']"
```

Wersja skonwertowanej powyższego wyrażenia byłaby `"['get','title']"` i można ją odczytać w Android SDK w następujący sposób.

```java
Expression exp = Expression.raw("['get','title']")
```

Zastosowanie tego podejścia może ułatwić ponowne użycie wyrażeń stylu między aplikacjami mobilnymi i sieci Web, które używają Azure Maps.

Ten film wideo zawiera omówienie stylów opartych na danych w Azure Maps.

</br>

>[!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Data-Driven-Styling-with-Azure-Maps/player?format=ny]

## <a name="data-expressions"></a>Wyrażenia danych

Wyrażenia danych zapewniają dostęp do danych właściwości w funkcji.

| Wyrażenie | Typ zwracany | Opis |
|------------|-------------|-------------|
| `accumulated()` | liczba | Pobiera wartość właściwości klastra do tej pory. |
| `at(number | Expression, Expression)` | wartość | Pobiera element z tablicy. |
| `geometryType()` | ciąg | Pobiera typ geometrii funkcji: punkt, MultiPoint, LineString, MultiLineString, Wielokąt, MultiPolygon. |
| `get(string | Expression)` \| `get(string | Expression, Expression)` | wartość | Pobiera wartość właściwości z właściwości podanego obiektu. Zwraca wartość null, jeśli brakuje żądanej właściwości. |
| `has(string | Expression)` \| `has(string | Expression, Expression)` | boolean | Określa, czy właściwości funkcji mają określoną właściwość. |
| `id()` | wartość | Pobiera identyfikator funkcji, jeśli ma. |
| `in(string | number | Expression, Expression)` | boolean | Określa, czy element istnieje w tablicy |
| `length(string | Expression)` | liczba | Pobiera długość ciągu lub tablicy. |
| `properties()`| wartość | Pobiera obiekt właściwości funkcji. |

Następujące wyrażenia stylu zestawu SDK sieci Web nie są obsługiwane w Android SDK:

- indeks
- Cinek

**Przykłady**

Do właściwości funkcji można uzyskać dostęp bezpośrednio w wyrażeniu przy użyciu `get` wyrażenia. Ten przykład używa `zoneColor` wartości funkcji, aby określić Właściwość Color warstwy bąbelkowej.

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    //Get the zoneColor value.
    bubbleColor(get("zoneColor"))
);
```

Powyższy przykład będzie działał prawidłowo, jeśli wszystkie funkcje punktu mają `zoneColor` Właściwość. Jeśli nie, kolor będzie prawdopodobnie przywrócony do "czerni". Aby zmodyfikować kolor rezerwowy, użyj `switchCase` wyrażenia w połączeniu z `has` wyrażeniem, aby sprawdzić, czy właściwość istnieje. Jeśli właściwość nie istnieje, zwróć kolor rezerwowy.

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        //Use a conditional case expression.
        switchCase(
            //Check to see if feature has a "zoneColor" 
            has("zoneColor"), 

            //If it does, use it.
            get("zoneColor"), 

            //If it doesn't, default to blue.
            literal("blue")
        )
    )
);
```

Warstwy bąbelków i symboli domyślnie renderują współrzędne wszystkich kształtów w źródle danych. Takie zachowanie może wyróżnić wierzchołki wielokąta lub linii. `filter`Opcja warstwy może służyć do ograniczenia typu geometrii funkcji, które renderuje, przy użyciu `geometryType` wyrażenia w wyrażeniu logicznym. Poniższy przykład ogranicza warstwę bąbelków, tak aby `Point` były renderowane tylko funkcje.

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    filter(eq(geometryType(), "Point"))
);
```

Poniższy przykład umożliwia `Point` `MultiPoint` renderowanie obu i funkcji. 

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    filter(any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint")))
);
```

Podobnie konspekt wielokątów będzie renderowany w warstwach liniowych. Aby wyłączyć to zachowanie w warstwie liniowej, Dodaj filtr, który umożliwia tylko `LineString` `MultiLineString` funkcje i.  

Oto kilka dodatkowych przykładów użycia wyrażeń danych:

```java
//Get item [2] from an array "properties.abcArray[1]" = "c"
at(2, get("abcArray"))

//Get item [0][1] from a 2D array "properties.array2d[0][1]" = "b"
at(1, at(0, get("array2d")))

//Check to see if a value is in an array "properties.abcArray.indexOf('a') !== -1" = true
in("a", get("abcArray"))

//Get the length of an array "properties.abcArray.length" = 3
length(get("abcArray"))

//Get the value of a subproperty "properties._style.fillColor" = "red"
get("fillColor", get("_style"))

//Check that "fillColor" exists as a subproperty of "_style".
has("fillColor", get("_style"))
```

## <a name="math-expressions"></a>Wyrażenia matematyczne

Wyrażenia matematyczne zapewniają operatory matematyczne do wykonywania obliczeń opartych na danych w ramach struktury wyrażeń.

| Wyrażenie | Typ zwracany | Opis |
|------------|-------------|-------------|
| `abs(number | Expression)` | liczba | Oblicza wartość bezwzględną podanej liczby. |
| `acos(number | Expression)` | liczba | Oblicza arcus cosinus podanej liczby. |
| `asin(number | Expression)` | liczba | Oblicza arcus sinus dla podanej liczby. |
| `atan(number | Expression)` | liczba | Oblicza arcus tangens podanej liczby. |
| `ceil(number | Expression)` | liczba | Zaokrągla liczbę w górę do najbliższej liczby całkowitej. |
| `cos(number | Expression)` | liczba | Oblicza cos o podanej liczbie. |
| `division(number, number)` \| `division(Expression, Expression)` | liczba | Dzieli pierwszą liczbę przez drugą liczbę. Odpowiednik wyrażenia zestawu SDK sieci Web: `/` |
| `e()` | liczba | Zwraca stałą matematyczną `e` . |
| `floor(number | Expression)` | liczba | Zaokrągla liczbę w dół do poprzedniej wartości całkowitej. |
| `log10(number | Expression)` | liczba | Oblicza logarytm dziesiętny dla podanej liczby. |
| `log2(number | Expression)` | liczba | Oblicza logarytm dziesiętny dla podanej liczby. |
| `ln(number | Expression)` | liczba | Oblicza logarytm naturalny podanej liczby. |
| `ln2()` | liczba | Zwraca stałą matematyczną `ln(2)` . |
| `max(numbers... | expressions...)` | liczba | Oblicza maksymalną liczbę w określonym zestawie liczb. |
| `min(numbers... | expressions...)` | liczba | Oblicza minimalną liczbę w określonym zestawie liczb. |
| `mod(number, number)` \| | `mod(Expression, Expression)` | liczba | Oblicza resztę podczas dzielenia pierwszej liczby przez drugą liczbę. Odpowiednik wyrażenia zestawu SDK sieci Web: `%` |
| `pi()` | liczba | Zwraca stałą matematyczną `PI` . |
| `pow(number, number)` \| `pow(Expression, Expression)` | liczba | Oblicza wartość pierwszej wartości podniesioną do potęgi drugiej liczby. |
| `product(numbers... | expressions...)` | liczba | Mnoży określone liczby razem. Odpowiednik wyrażenia zestawu SDK sieci Web: `*` |
| `round(number | Expression)` | liczba | Zaokrągla liczbę do najbliższej liczby całkowitej. Połowy wartości są zaokrąglane w kierunku od zera. Na przykład program `round(-1.5)` zwraca wartość `-2` . |
| `sin(number | Expression)` | liczba | Oblicza sinus dla podanej liczby. |
| `sqrt(number | Expression)` | liczba | Oblicza pierwiastek kwadratowy z podanej liczby. |
| `subtract(number | Expression` | liczba | Odejmuje 0 według podanej liczby. |
| `subtract(number | Expression, number | Expression)` | liczba | Odejmuje pierwsze numery według drugiej liczby. |
| `sum(numbers... | expressions...)` | liczba | Oblicza sumę podanych liczb. |
| `tan(number | Expression)` | liczba | Oblicza tangens podanej liczby. |

## <a name="boolean-expressions"></a>Wyrażenia logiczne

Wyrażenia logiczne zawierają zestaw wyrażeń operatorów logicznych do oceniania porównania wartości logicznych.

Porównując wartości, porównanie jest ściśle wpisane. Wartości różnych typów są zawsze uznawane za nierówne. Przypadki, w których typy muszą być różne w czasie analizy, są uważane za nieprawidłowe i spowodują błąd analizy.

| Wyrażenie | Typ zwracany | Opis |
|------------|-------------|-------------|
| `all(Expression...)` | boolean | Zwraca `true` czy wszystkie dane wejściowe są `true` , `false` w przeciwnym razie. |
| `any(Expression...)` | boolean | Zwraca wartość `true` , jeśli którekolwiek z danych wejściowych nie są `true` , `false` w przeciwnym razie. |
| `eq(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `eq(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Zwraca wartość `true` , jeśli wartości wejściowe są równe `false` . w przeciwnym razie. Argumenty muszą być zarówno ciągami, jak i obiema numerami. |
| `gt(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `gt(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Zwraca `true` czy pierwsze dane wejściowe są absolutnie większe od drugiego, `false` w przeciwnym razie. Argumenty muszą być zarówno ciągami, jak i obiema numerami. |
| `gte(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `gte(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Zwraca wartość `true` , jeśli pierwsze dane wejściowe są większe lub równe drugiemu `false` . w przeciwnym razie. Argumenty muszą być zarówno ciągami, jak i obiema numerami. |
| `lt(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `lt(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Zwraca `true` czy pierwsze dane wejściowe są absolutnie mniejsze od drugiego, `false` w przeciwnym razie. Argumenty muszą być zarówno ciągami, jak i obiema numerami. |
| `lte(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `lte(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Zwraca wartość `true` , jeśli pierwsze dane wejściowe są mniejsze lub równe sekundzie, `false` w przeciwnym razie. Argumenty muszą być zarówno ciągami, jak i obiema numerami. |
| `neq(Expression compareOne, Expression | boolean | number | string compareTwo)` \| `neq(Expression compareOne, Expression | string compareTwo, Expression collator)` | boolean | Zwraca wartość `true` , jeśli wartości wejściowe nie są równe `false` . w przeciwnym razie. |
| `not(Expression | boolean)` | boolean | Negacja logiczna. Zwraca wartość `true` , jeśli dane wejściowe to `false` , i `false` Jeśli dane wejściowe to `true` . |

## <a name="conditional-expressions"></a>Wyrażenia warunkowe

Wyrażenia warunkowe udostępniają operacje logiki, które są podobne do instrukcji if-Statement.

Następujące wyrażenia wykonują warunkowe operacje logiki na danych wejściowych. Na przykład `switchCase` wyrażenie zawiera logikę "if/then/else", podczas gdy `match` wyrażenie jest podobne do "Switch-Statement". 

### <a name="switch-case-expression"></a>Wyrażenie CASE Switch

`switchCase`Wyrażenie jest typem wyrażenia warunkowego, które udostępnia logikę "if/then/else". Tego typu wyrażenie jest wykonywane przez listę warunków logicznych. Zwraca wartość wyjściową pierwszego warunku logicznego, który ma zostać obliczony na wartość true.

Poniższy pseudokodzie definiuje strukturę `switchCase` wyrażenia.

```java
switchCase(
    condition1: boolean expression, 
    output1: value,
    condition2: boolean expression, 
    output2: value,
    ...,
    fallback: value
)
```

**Przykład**

Poniższy przykład przechodzi przez inne warunki logiczne do momentu znalezienia jednego, który jest obliczany przez `true` , a następnie zwraca tę skojarzoną wartość. Jeśli warunek logiczny nie zostanie obliczony `true` , zostanie zwrócona wartość rezerwowa.

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        switchCase(
            //Check to see if the first boolean expression is true, and if it is, return its assigned result.
            //If it has a zoneColor property, use its value as a color.
            has("zoneColor"), toColor(get("zoneColor")),

            //Check to see if the second boolean expression is true, and if it is, return its assigned result.
            //If it has a temperature property with a value greater than or equal to 100, make it red.
            all(has("temperature"), gte(get("temperature"), 100)), color(Color.RED),
            
            //Specify a default value to return. In this case green.
            color(Color.GREEN)
        )
    )
);
```

### <a name="match-expression"></a>Wyrażenie dopasowania

`match`Wyrażenie jest typem wyrażenia warunkowego, które zawiera instrukcję Switch, taką jak Logic. Wejście może być dowolnym wyrażeniem, takim jak `get( "entityType")` , które zwraca ciąg lub liczbę. Każdy element Stop musi mieć etykietę, która jest pojedynczą wartością literału lub tablicą wartości literału, której wartości muszą zawierać wszystkie ciągi lub wszystkie liczby. Dane wejściowe są zgodne, Jeśli dowolne wartości w tablicy pasują do siebie. Każda etykieta zatrzymania musi być unikatowa. Jeśli typ danych wejściowych nie jest zgodny z typem etykiet, wynik będzie domyślną wartością rezerwową.

Poniższy pseudokodzie definiuje strukturę `match` wyrażenia.

```java
match(Expression input, Expression defaultOutput, Expression.Stop... stops)
```

**Przykłady**

Poniższy przykład sprawdza, czy `entityType` Właściwość funkcji punktu w warstwie bąbelka wyszukuje dopasowanie. Jeśli znajdzie dopasowanie, zostanie zwrócona określona wartość lub zwróci wartość rezerwową.

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" return "red".
            stop("restaurant", color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
);
```

W poniższym przykładzie zastosowano tablicę, aby wyświetlić zestaw etykiet, które powinny zwracać tę samą wartość. Takie podejście jest znacznie bardziej wydajne niż wymienianie każdej etykiety osobno. W takim przypadku, jeśli `entityType` Właściwość ma wartość "restauracji" lub "grocery_store", zostanie zwrócony kolor "Red".

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        match(
            //Get the input value to match.
            get("entityType"),

            //Specify a default value to return if no match is found.
            color(Color.BLACK),

            //List the values to match and the result to return for each match.

            //If value is "restaurant" or "grocery_store" return "red".
            stop(Arrays.asList("restaurant", "grocery_store"), color(Color.RED)),

            //If value is "park" return "green".
            stop("park", color(Color.GREEN))
        )
    )
);
```

### <a name="coalesce-expression"></a>Wyrażenie łączenia

`coalesce`Wyrażenie jest wykonywane przez zestaw wyrażeń do momentu uzyskania pierwszej wartości innej niż null i zwrócenie tej wartości.

Poniższy pseudokodzie definiuje strukturę `coalesce` wyrażenia.

```java
coalesce(Expression... input)
```

**Przykład**

Poniższy przykład używa wyrażenia, `coalesce` Aby ustawić `textField` opcję warstwy symboli. Jeśli `title` brakuje właściwości w funkcji lub ustawiono ją `null` , wyrażenie będzie próbować wyszukać `subTitle` Właściwość, jeśli jej brakuje lub `null` , spowoduje powrót do pustego ciągu. 

```java
SymbolLayer layer = new SymbolLayer(dataSource,
    textField(
        coalesce(
            //Try getting the title property.
            get("title"),

            //If there is no title, try getting the subTitle. 
            get("subTitle"),

            //Default to an empty string.
            literal("")
        )
    )
);
```

## <a name="type-expressions"></a>Wyrażenia typu

Wyrażenia typu dostarczają narzędzia do testowania i konwertowania różnych typów danych, takich jak ciągi, liczby i wartości logiczne.

| Wyrażenie | Typ zwracany | Opis |
|------------|-------------|-------------|
| `array(Expression)` | Obiekt [] | Potwierdza, że dane wejściowe są tablicami. |
| `bool(Expression)` | boolean | Potwierdza, że wartość wejściowa jest wartością logiczną. |
| `collator(boolean caseSensitive, boolean diacriticSensitive)` \| `collator(boolean caseSensitive, boolean diacriticSensitive, java.util.Locale locale)` \| `collator(Expression caseSensitive, Expression diacriticSensitive)` \| `collator(Expression caseSensitive, Expression diacriticSensitive, Expression locale)` | Collator | Zwraca zestawienie do użycia w operacjach porównania zależnych od ustawień regionalnych. Opcje z uwzględnieniem wielkości liter i znaków diakrytycznych domyślnie mają wartość false. Argument locale określa tag języka IETF dla ustawień regionalnych, które mają być używane. Jeśli żaden nie jest podany, używane są domyślne ustawienia regionalne. Jeśli żądane ustawienia regionalne nie są dostępne, dzielnik będzie używać ustawień regionalnych określonych przez system. Użyj rozpoznanych ustawień regionalnych, aby przetestować wyniki działania rezerwowego dla ustawień regionalnych.  |
| `literal(boolean \| number \| string \| Object \| Object[])` | wartość \| logiczna \| obiektu ciągu liczbowego \| \| [] | Zwraca tablicę literałową lub wartość obiektu. Użyj tego wyrażenia, aby zapobiec ocenie tablicy lub obiektu jako wyrażenia. Jest to konieczne, gdy tablica lub obiekt musi zostać zwrócony przez wyrażenie. |
| `number(Expression)` | liczba | Potwierdza, że wartość wejściowa jest liczbą. |
| `object(Expression)` | Obiekt | Potwierdza, że wartość wejściowa jest obiektem. |
| `string(Expression)` | ciąg | Potwierdza, że wartość wejściowa jest ciągiem. |
| `toArray(Expression)` | Obiekt [] | Konwertuje wyrażenie na tablicę obiektów JSON. |
| `toBool(Expression)` | boolean | Konwertuje wartość wejściową do wartości logicznej. |
| `toNumber(Expression)` | liczba | Konwertuje wartość wejściową na liczbę, jeśli jest to możliwe. |
| `toString(Expression)` | ciąg | Konwertuje wartość wejściową na ciąg. |
| `typeoOf(Expression)` | ciąg | Zwraca ciąg opisujący typ danej wartości. |

## <a name="color-expressions"></a>Wyrażenia koloru

Wyrażenia kolorów ułatwiają tworzenie wartości kolorów i manipulowanie nimi.

| Wyrażenie | Typ zwracany | Opis |
|------------|-------------|-------------|
| `color(int)` | color | Konwertuje liczbę całkowitą koloru na wyrażenie koloru. |
| `rgb(Expression red, Expression green, Expression blue)` \| `rgb(number red, number green, number blue)` | color | Tworzy wartość koloru dla składników *czerwony*, *zielony* i *niebieski* , które muszą namieścić się w zakresie od `0` do i `255` i składnika alfa elementu `1` . Jeśli dowolny składnik znajduje się poza zakresem, wyrażenie jest błędem. |
| `rgba(Expression red, Expression green, Expression blue, Expression alpha)` \| `rgba(number red, number green, number blue, number alpha)` | color | Tworzy wartość koloru na podstawie *czerwonych*, *zielonych*, *niebieskich* składników, które muszą należeć do zakresu od `0` do `255` , i składnika alfa w zakresie `0` i `1` . Jeśli dowolny składnik znajduje się poza zakresem, wyrażenie jest błędem. |
| `toColor(Expression)` | color  | Konwertuje wartość wejściową na kolor. |
| `toRgba(Expression)` | color | Zwraca tablicę z czterema elementami zawierającą składniki czerwony, zielony, niebieski i alfa koloru wejścia w tej kolejności. |

**Przykład**

W poniższym przykładzie jest tworzona wartość koloru RGB, która ma *czerwoną* wartość `255` , oraz wartości *zielony* i *niebieski* , które są obliczane przez pomnożenie `2.5` przez wartość `temperature` właściwości. W miarę zmieniania temperatury kolor zmieni się na różne odcienie *czerwieni*.

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            //Set red value to 255. Wrap with literal expression since using expressions for other values.
            literal(255f),    

            //Multiple the temperature by 2.5 and set the green value.
            product(literal(2.5f), get("temperature")), 

            //Multiple the temperature by 2.5 and set the blue value.
            product(literal(2.5f), get("temperature")) 
        )
    )
);
```

Jeśli wszystkie parametry koloru są liczbami, nie trzeba zawijać ich przy użyciu `literal` wyrażenia. Na przykład:

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        //Create a RGB color value.
        rgb(
            255f,  //Set red value to 255.

            150f,  //Set green value to 150.

            0f     //Set blue value to 0.
        )
    )
);
```

> [!TIP]
> Wartości koloru ciągu można przekonwertować na kolor przy użyciu `android.graphics.Color.parseColor` metody. Poniższe konwertuje ciąg koloru szesnastkowego na wyrażenie koloru, które może być używane z warstwą.
>
> ```java
> color(parseColor("#ff00ff"))
> ```

## <a name="string-operator-expressions"></a>Wyrażenia operatora ciągu

Wyrażenia operatora ciągu wykonują operacje konwersji na ciągach, takich jak łączenie i konwertowanie wielkości liter. 

| Wyrażenie | Typ zwracany | Opis |
|------------|-------------|-------------|
| `concat(string...)` \| `concat(Expression...)` | ciąg | Łączy wiele ciągów ze sobą. Każda wartość musi być ciągiem. Użyj `toString` wyrażenia typu, aby przekonwertować inne typy wartości na ciąg w razie potrzeby. |
| `downcase(string)` \| `downcase(Expression)` | ciąg | Konwertuje określony ciąg na małe litery. |
| `isSupportedScript(string)` \| `isSupportedScript(Expression)`| boolean | Określa, czy ciąg wejściowy używa zestawu znaków obsługiwanego przez bieżący stos czcionek. Na przykład: `isSupportedScript("ಗೌರವಾರ್ಥವಾಗಿ")` |
| `resolvedLocale(Expression collator)` | ciąg | Zwraca tag języka IETF dla ustawień regionalnych używanych przez dostarczony dzielnik. Może to służyć do określenia domyślnych ustawień regionalnych systemu lub określenia, czy żądane ustawienia regionalne zostały pomyślnie załadowane. |
| `upcase(string)` \| `upcase(Expression)` | ciąg | Konwertuje określony ciąg na wielkie litery. |

**Przykład**

Poniższy przykład konwertuje `temperature` Właściwość punktu na ciąg, a następnie łączy "°f" na końcu.

```java
SymbolLayer layer = new SymbolLayer(dataSource,
    textField(
        concat(Expression.toString(get("temperature")), literal("°F"))
    ),

    //Some additional style options.
    textOffset(new Float[] { 0f, -1.5f }),
    textSize(12f),
    textColor("white")
);
```

Powyższe wyrażenie renderuje kod PIN na mapie z tekstem "64 °F" na początku, jak pokazano na poniższej ilustracji.

![Przykład wyrażenia operatora ciągu](media/how-to-expressions/string-operator-expression.png)

## <a name="interpolate-and-step-expressions"></a>Wyrażenia interpolowane i krokowe

Wyrażenia interpolacji i kroku mogą służyć do obliczania wartości w interpolowanej krzywej lub funkcji kroku. Wyrażenia te przyjmują wyrażenie, które zwraca wartość liczbową jako dane wejściowe, na przykład `get("temperature")` . Wartość wejściowa jest oceniana pod kątem par wartości wejściowych i wyjściowych, aby określić wartość, która najlepiej pasuje do interpolowanej krzywej lub funkcji Step. Wartości wyjściowe są nazywane "zatrzymania". Wartości wejściowe dla każdego zatrzymania muszą być liczbą i być uporządkowane rosnąco. Wartości wyjściowe muszą być liczbami, tablicami liczb lub kolorem.

### <a name="interpolate-expression"></a>Wyrażenie interpolacji

`interpolate`Wyrażenie może służyć do obliczania ciągłego, gładkiego zestawu wartości poprzez interpolację między wartościami zatrzymania. `interpolate`Wyrażenie zwracające wartości koloru generuje kolor gradientu, w którym są wybierane wartości wyniku. `interpolate`Wyrażenie ma następujące formaty:

```java
//Stops consist of two expressions.
interpolate(Expression.Interpolator interpolation, Expression number, Expression... stops)

//Stop expression wraps two values.
interpolate(Expression.Interpolator interpolation, Expression number, Expression.Stop... stops)
```

Istnieją trzy typy metod interpolacji, których można użyć w `interpolate` wyrażeniu:

| Nazwa | Opis | 
|------|-------------|
| `linear()` | Interpoluje liniowo między parą przerwań.  |
| `exponential(number)` \| `exponential(Expression)` | Interpoluje wykładniczo między zatrzymaniem. Określono wartość "Base" i kontroluje szybkość, z jaką dane wyjściowe rosną. Wyższe wartości sprawiają, że dane wyjściowe zwiększają się w kierunku górnego końca zakresu. Wartość "podstawowa" bliska 1 powoduje generowanie danych wyjściowych, które zwiększają się liniowo.|
| `cubicBezier(number x1, number y1, number x2, number y2)` \| `cubicBezier(Expression x1, Expression y1, Expression x2, Expression y2)` | Interpoluje przy użyciu [zakrzywionej krzywej Beziera](https://developer.mozilla.org/docs/Web/CSS/timing-function) zdefiniowanej przez podaną punkty kontrolne. |

`stop`Wyrażenie ma format `stop(stop, value)` .
 
Oto przykład sposobu, w jaki wyglądają te różne typy interpolacji. 

| Liniowe  | Wykładniczy | Metria sześcienne |
|---------|-------------|--------------|
| ![Wykres interpolacji liniowej](media/how-to-expressions/linear-interpolation.png) | ![Wykres interpolacji wykładniczej](media/how-to-expressions/exponential-interpolation.png) | ![Wykres interpolacji "sześciennych"](media/how-to-expressions/bezier-curve-interpolation.png) |

**Przykład**

Poniższy przykład używa wyrażenia, `linear interpolate` Aby ustawić `bubbleColor` Właściwość warstwy bąbelkowej na podstawie `temperature` właściwości funkcji Point. W przypadku `temperature` wartości mniejszej niż 60 zostanie zwrócona wartość "niebieska". Jeśli jest to od 60 do 70, zostaną zwrócone żółte. Jeśli wartość jest z zakresu od 70 do 80, zostanie zwrócony "pomarańczowy" ( `#FFA500` ). Jeśli jest to 80 lub więcej, zostanie zwrócony czerwony znak "Red".

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        interpolate(
            linear(),
            get("temperature"),
            stop(50, color(Color.BLUE)),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
);
```

Na poniższej ilustracji przedstawiono, jak kolory są wybierane dla powyższego wyrażenia.

![Przykład wyrażenia interpolacji](media/how-to-expressions/interpolate-expression-example.png)

### <a name="step-expression"></a>Wyrażenie kroku

`step`Wyrażenie może służyć do obliczania dyskretnych, ocenianych wartości wyniku przez obliczenie [funkcji stałej rozkład elementowy](http://mathworld.wolfram.com/PiecewiseConstantFunction.html) zdefiniowanej przez zatrzymywanie. 

`interpolate`Wyrażenie ma następujące formaty:

```java
step(Expression input, Expression defaultOutput, Expression... stops)

step(Expression input, Expression defaultOutput, Expression.Stop... stops)

step(Expression input, number defaultOutput, Expression... stops)

step(Expression input, number defaultOutput, Expression.Stop... stops)

step(number input, Expression defaultOutput, Expression... stops)

step(number input, Expression defaultOutput, Expression.Stop... stops)

step(number input, number defaultOutput, Expression... stops)

step(number input, number defaultOutput, Expression.Stop... stops)
```

Wyrażenia kroków zwracają wartość wyjściową elementu Stop tuż przed wartością wejściową lub pierwszą wartość wejściową, jeśli dane wejściowe są mniejsze niż pierwsze zatrzymanie. 

**Przykład**

Poniższy przykład używa wyrażenia, `step` Aby ustawić `bubbleColor` Właściwość warstwy bąbelkowej na podstawie `temperature` właściwości funkcji Point. W przypadku `temperature` wartości mniejszej niż 60 zostanie zwrócona wartość "niebieska". Jeśli wartość jest z zakresu od 60 do 70, zostanie zwrócony "żółty". Jeśli wartość jest z zakresu od 70 do 80, zostanie zwrócony "pomarańczowy". Jeśli jest to 80 lub więcej, zostanie zwrócony czerwony znak "Red".

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(
        step(
            get("temperature"),
            color(Color.BLUE),
            stop(60, color(Color.YELLOW)),
            stop(70, color(parseColor("#FFA500"))),
            stop(80, color(Color.RED))
        )
    )
);
```

Na poniższej ilustracji przedstawiono, jak kolory są wybierane dla powyższego wyrażenia.
 
![Przykład wyrażenia kroku](media/how-to-expressions/step-expression-example.png)

## <a name="layer-specific-expressions"></a>Wyrażenia specyficzne dla warstwy

Specjalne wyrażenia mające zastosowanie tylko do określonych warstw.

### <a name="heat-map-density-expression"></a>Wyrażenie gęstości mapy cieplnej

Wyrażenie gęstości mapy cieplnej Pobiera wartość gęstości mapy cieplnej dla każdego piksela w warstwie mapy cieplnej i jest definiowana jako `heatmapDensity` . Ta wartość jest liczbą z zakresu od `0` do `1` . Jest on używany w połączeniu z `interpolation` `step` wyrażeniem or, aby zdefiniować gradient koloru używany do kolorowania mapy cieplnej. Tego wyrażenia można używać tylko w przypadku `heatmapColor` opcji warstwy mapy cieplnej.

> [!TIP]
> Kolor pod indeksem 0, w wyrażeniu interpolacji lub domyślnym kolorem kroku, definiuje kolor obszaru, w którym nie ma żadnych danych. Kolor pod indeksem 0 może służyć do definiowania koloru tła. Wiele woli ustawić tę wartość na przezroczystą lub częściowo przezroczystą czerń.

**Przykład**

W tym przykładzie zastosowano wyrażenie interpolacji liniowej w celu utworzenia gładkiego gradientu koloru na potrzeby renderowania mapy cieplnej. 

```java
HeatMapLayer layer = new HeatMapLayer(dataSource,
    heatmapColor(
        interpolate(
            linear(),
            heatmapDensity(),
            stop(0, color(Color.TRANSPARENT)),
            stop(0.01, color(Color.MAGENTA)),
            stop(0.5, color(parseColor("#fb00fb"))),
            stop(1, color(parseColor("#00c3ff")))
        )
    )
);
```

Oprócz używania gładkiego gradientu do kolorowania mapy cieplnej, kolory można określić w ramach zestawu zakresów przy użyciu `step` wyrażenia. Użycie `step` wyrażenia do kolorowania mapy cieplnej powoduje, że rozdzieli gęstość na zakresy podobne do mapy stylu konturu lub wykresu radarowego.  

```java
HeatMapLayer layer = new HeatMapLayer(dataSource,
    heatmapColor(
        step(
            heatmapDensity(),
            color(Color.TRANSPARENT),
            stop(0.01, color(parseColor("#000080"))),
            stop(0.25, color(parseColor("#000080"))),
            stop(0.5, color(Color.GREEN)),
            stop(0.5, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
);
```

Aby uzyskać więcej informacji, zobacz dokumentację dotyczącą [dodawania do warstwy mapy cieplnej](map-add-heat-map-layer-android.md) .

### <a name="line-progress-expression"></a>Wyrażenie postępu wiersza

Wyrażenie postępu wiersza pobiera postęp wzdłuż linii gradientu w warstwie liniowej i jest zdefiniowane jako `lineProgress()` . Ta wartość jest liczbą z zakresu od 0 do 1. Jest on używany w połączeniu z `interpolation` `step` wyrażeniem or. Tego wyrażenia można używać tylko z `strokeGradient` opcją warstwy linii.

> [!NOTE]
> `strokeGradient`Opcja warstwy liniowej wymaga, `lineMetrics` Aby opcja źródła danych była ustawiona na wartość `true` .

**Przykład**

Ten przykład używa `lineProgress()` wyrażenia, aby zastosować gradient koloru do obrysu wiersza.

```javascript
LineLayer layer = new LineLayer(source,
    strokeGradient(
        interpolate(
            linear(),
            lineProgress(),
            stop(0, color(Color.BLUE)),
            stop(0.1, color(Color.argb(255, 65, 105, 225))), //Royal Blue
            stop(0.3, color(Color.CYAN)),
            stop(0.5, color(Color.argb(255,0, 255, 0))), //Lime
            stop(0.7, color(Color.YELLOW)),
            stop(1, color(Color.RED))
        )
    )
);
```

[Zobacz przykład na żywo](map-add-line-layer.md#line-stroke-gradient)

### <a name="text-field-format-expression"></a>Wyrażenie formatu pola tekstowego

`format`Wyrażenie może być używane z `textField` opcją warstwy symbol, aby zapewnić formatowanie tekstu mieszanego. To wyrażenie przyjmuje co najmniej jedno `formatEntry` wyrażenie określające ciąg i zestaw `formatOptions` do dołączenia do pola tekstowego.

| Wyrażenie | Opis |
|------------|-------------|
| `format(Expression...)` | Zwraca sformatowany tekst zawierający adnotacje do użycia w przypadku wpisów pól tekstowych w formacie mieszanym. |
| `formatEntry(Expression text)` \| `formatEntry(Expression text, Expression.FormatOption... formatOptions)` \| `formatEntry(String text)` \| `formatEntry(String text, Expression.FormatOption... formatOptions)` | Zwraca sformatowany wpis ciągu do użycia w `format` wyrażeniu. |

Dostępne są następujące opcje formatu:

| Wyrażenie | Opis |
|------------|-------------|
| `formatFontScale(number)` \| `formatFontScale(Expression)` | Określa współczynnik skalowania rozmiaru czcionki. Jeśli ta wartość jest określona, spowoduje to zastąpienie `textSize` właściwości dla pojedynczego ciągu. |
| `formatTextFont(string[])` \| `formatTextFont(Expression)` | Określa kolor, który ma zostać zastosowany do tekstu podczas renderowania. |

**Przykład**

Poniższy przykład formatuje pole tekstowe przez dodanie pogrubionej czcionki i skalowanie w górę rozmiaru czcionki `title` właściwości funkcji. Ten przykład dodaje również `subTitle` Właściwość funkcji w wierszu wiersza z rozmiarem czcionki skalowanej w dół.

```java
SymbolLayer layer = new SymbolLayer(dataSource,
    textField(
        format(
            //Bold the title property and scale its font size up.
            formatEntry(
                get("title"),
                formatTextFont(new String[] { "StandardFont-Bold" }),
                formatFontScale(1.25)),

            //Add a new line without any formatting.
            formatEntry("\n"),

            //Scale the font size down of the subTitle property.
            formatEntry(
                get("subTitle"),
                formatFontScale(0.75))
        )
    )
);
```

Ta warstwa będzie renderować funkcję Point, jak pokazano na poniższej ilustracji:

![Obraz funkcji punktu z sformatowanym polem tekstowym](media/how-to-expressions/text-field-format-expression.png)

## <a name="zoom-expression"></a>Wyrażenie powiększenia

`zoom`Wyrażenie służy do pobierania bieżącego poziomu powiększenia mapy w czasie renderowania i jest zdefiniowane jako `zoom()` . To wyrażenie zwraca liczbę z przedziału minimalnego i maksymalnego poziomu powiększenia mapy. Azure Maps formanty interaktywnej mapy dla sieci Web i Android obsługują 25 poziomów powiększenia, numerowane od 0 do 24. Użycie `zoom` wyrażenia pozwala na dynamiczne modyfikowanie stylów, gdy poziom powiększenia mapy zostanie zmieniony. `zoom`Wyrażenia można używać tylko z `interpolate` `step` wyrażeniami i.

**Przykład**

Domyślnie promienie punktów danych renderowane w warstwie mapy cieplnej mają stały promień pikseli dla wszystkich poziomów powiększenia. W miarę powiększania mapy dane zagregowane i warstwy mapy cieplnej wyglądają inaczej. `zoom`Wyrażenie może służyć do skalowania promienia dla każdego poziomu powiększenia, tak że każdy punkt danych obejmuje ten sam obszar fizyczny mapy. Spowoduje to, że warstwa mapy cieplnej będzie wyglądać bardziej statyczna i spójna. Każdy poziom powiększenia mapy ma dwa piksele w pionie i poziomie jako poprzedni poziom powiększenia. Skalowanie promienia, w taki sposób, że podwaja się z każdym poziomem powiększenia, spowoduje utworzenie mapy cieplnej, która będzie wyglądać spójnie na wszystkich poziomach powiększenia. Można to zrobić przy użyciu `zoom` wyrażenia z `base 2 exponential interpolation` wyrażeniem, z zestawem pikseli ustawionym dla minimalnego poziomu powiększenia i skalowanego promienia dla maksymalnego poziomu powiększenia obliczonego w sposób `2 * Math.pow(2, minZoom - maxZoom)` pokazany poniżej.

```java 
HeatMapLayer layer = new HeatMapLayer(dataSource,
    heatmapRadius(
        interpolate(
            exponential(2),
            zoom(),

            //For zoom level 1 set the radius to 2 pixels.
            stop(1, 2),

            //Between zoom level 1 and 19, exponentially scale the radius from 2 pixels to 2 * (maxZoom - minZoom)^2 pixels.
            stop(19, 2 * Math.pow(2, 19 - 1))
        )
    )
);
```

## <a name="variable-binding-expressions"></a>Wyrażenia powiązań zmiennych

Wyrażenia powiązań zmiennych przechowują wyniki obliczeń w zmiennej. Tak więc wyniki obliczeń mogą być przywoływane w innym miejscu w wyrażeniu. Jest to przydatna Optymalizacja dla wyrażeń, które obejmują wiele obliczeń.

| Wyrażenie | Typ zwracany | Opis |
|--------------|---------------|--------------|
| `let(Expression... input)` | | Przechowuje co najmniej jedną wartość jako zmienne do użycia przez `var` wyrażenie w wyrażeniu podrzędnym, które zwraca wynik. |
| `var(Expression expression)` \| `var(string variableName)` | Obiekt | Odwołuje się do zmiennej, która została utworzona przy użyciu `let` wyrażenia. |

**Przykład**

W tym przykładzie używane jest wyrażenie, które oblicza przychód względem współczynnika temperatury, a następnie używa `case` wyrażenia, aby oszacować różne operacje logiczne dla tej wartości. `let`Wyrażenie służy do przechowywania przychodu względem współczynnika temperatury, tak aby tylko raz obliczyć tylko raz. `var`Wyrażenie odwołuje się do tej zmiennej tak często, jak to konieczne, bez konieczności ponownego obliczania.

```java
BubbleLayer layer = new BubbleLayer(dataSource,
    bubbleColor(           
        let(
            //Divide the point features `revenue` property by the `temperature` property and store it in a variable called `ratio`.
            literal("ratio"), division(get("revenue"), get("temperature")),

            //Evaluate the child expression in which the stored variable will be used.
            switchCase(
                //Check to see if the ratio is less than 100, return 'red'.
                lt(var("ratio"), 100), color(Color.RED),

                //Check to see if the ratio is less than 200, return 'green'.
                lt(var("ratio"), 200), color(Color.GREEN),

                //Return `blue` for values greater or equal to 200.
                color(Color.BLUE)
            )
        )
    )
);
```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o warstwach, które obsługują wyrażenia:

> [!div class="nextstepaction"]
> [Dodawanie warstwy symboli](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy bąbelkowej](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy linii](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Dodawanie warstwy wielokąta](how-to-add-shapes-to-android-map.md)

> [!div class="nextstepaction"]
> [Dodawanie mapy cieplnej](map-add-heat-map-layer-android.md)
