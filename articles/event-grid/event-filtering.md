---
title: Filtrowanie zdarzeń dla Azure Event Grid
description: Opisuje sposób filtrowania zdarzeń podczas tworzenia subskrypcji Azure Event Grid.
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: b5439b77b86d42d062cf9da66ce678f04f46f813
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/09/2021
ms.locfileid: "107256093"
---
# <a name="understand-event-filtering-for-event-grid-subscriptions"></a>Opis filtrowania zdarzeń dla subskrypcji Event Grid

W tym artykule opisano różne sposoby filtrowania zdarzeń wysyłanych do punktu końcowego. Podczas tworzenia subskrypcji zdarzeń dostępne są trzy opcje filtrowania:

* Typy zdarzeń
* Podmiot rozpoczyna się od lub kończąc z
* Zaawansowane pola i operatory

## <a name="event-type-filtering"></a>Filtrowanie typów zdarzeń

Domyślnie wszystkie [typy zdarzeń](event-schema.md) dla źródła zdarzeń są wysyłane do punktu końcowego. Możesz zdecydować się na wysłanie tylko niektórych typów zdarzeń do punktu końcowego. Można na przykład otrzymywać powiadomienia o aktualizacjach zasobów, ale nie powiadomienia o innych operacjach, takich jak usuwanie. W takim przypadku należy filtrować według `Microsoft.Resources.ResourceWriteSuccess` typu zdarzenia. Podaj tablicę z typami zdarzeń lub określ, `All` Aby pobrać wszystkie typy zdarzeń dla źródła zdarzeń.

Składnia JSON dla filtrowania według typu zdarzenia:

```json
"filter": {
  "includedEventTypes": [
    "Microsoft.Resources.ResourceWriteFailure",
    "Microsoft.Resources.ResourceWriteSuccess"
  ]
}
```

## <a name="subject-filtering"></a>Filtrowanie podmiotu

Dla prostego filtrowania według tematu Określ wartość początkową lub końcową podmiotu. Można na przykład określić, że temat ma kończyć się `.txt` tylko zdarzenia związane z przekazywaniem pliku tekstowego do konta magazynu. Można też odfiltrować temat od, `/blobServices/default/containers/testcontainer` Aby pobierać wszystkie zdarzenia dla tego kontenera, ale nie inne kontenery na koncie magazynu.

Podczas publikowania zdarzeń w niestandardowych tematach, należy utworzyć tematy dotyczące wydarzeń, które ułatwią subskrybentom dowiedzieć się, czy zainteresują się wydarzeniem. Subskrybenci używają właściwości podmiotu do filtrowania i kierowania zdarzeń. Rozważ dodanie ścieżki do miejsca wystąpienia zdarzenia, dzięki czemu Subskrybenci mogą filtrować według segmentów tej ścieżki. Ścieżka umożliwia subskrybentom Zawężanie lub szerokie filtrowanie zdarzeń. Jeśli podano trzy ścieżki segmentu jak `/A/B/C` w temacie, subskrybenci mogą odfiltrować według pierwszego segmentu, `/A` Aby uzyskać obszerny zestaw zdarzeń. Subskrybenci uzyskują zdarzenia z podmiotami takimi jak `/A/B/C` lub `/A/D/E` . Inni Subskrybenci mogą odfiltrować, `/A/B` Aby uzyskać węższy zestaw zdarzeń.

Składnia JSON dla filtrowania według tematu to:

```json
"filter": {
  "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
  "subjectEndsWith": ".jpg"
}

```

## <a name="advanced-filtering"></a>Filtrowanie zaawansowane

Aby filtrować według wartości w polach danych i określić operator porównania, użyj opcji filtrowania zaawansowanego. W zaawansowanym filtrowaniu należy określić:

* Typ operatora — typ porównania.
* klucz — pole w danych zdarzenia, które są używane do filtrowania. Może to być liczba, wartość logiczna, ciąg lub tablica.
* wartości — wartość lub wartości, które mają zostać porównane z kluczem.

## <a name="key"></a>Klucz
Klucz to pole w danych zdarzenia, które są używane do filtrowania. Może to być jeden z następujących typów:

- Liczba
- Boolean (wartość logiczna)
- String (ciąg)
- Macierzy. Należy ustawić `enableAdvancedFilteringOnArrays` Właściwość na wartość true, aby użyć tej funkcji. Obecnie Azure Portal nie obsługuje włączania tej funkcji. 

    ```json
    "filter":
    {
        "subjectBeginsWith": "/blobServices/default/containers/mycontainer/log",
        "subjectEndsWith": ".jpg",
        "enableAdvancedFilteringOnArrays": true
    }
    ```

Dla zdarzeń w **schemacie Event Grid** należy użyć następujących wartości dla klucza: `ID` ,,, `Topic` , `Subject` `EventType` `DataVersion` lub danych zdarzenia (na przykład `data.key1` ).

Dla zdarzeń w **schemacie zdarzenia w chmurze** Użyj następujących wartości dla klucza: `eventid` ,, `source` , `eventtype` `eventtypeversion` lub danych zdarzenia (na przykład `data.key1` ).

W przypadku **niestandardowego schematu wprowadzania** Użyj pól danych zdarzenia (takich jak `data.key1` ). Aby uzyskać dostęp do pól w sekcji danych, użyj `.` notacji (kropka). Na przykład `data.sitename` , `data.appEventTypeDetail.action` Aby uzyskać dostęp do `sitename` lub `action` dla następującego przykładowego zdarzenia.

```json
    "data": {
        "appEventTypeDetail": {
            "action": "Started"
        },
        "siteName": "<site-name>",
        "clientRequestId": "None",
        "correlationRequestId": "None",
        "requestId": "292f499d-04ee-4066-994d-c2df57b99198",
        "address": "None",
        "verb": "None"
    },
```

## <a name="values"></a>Wartości
Możliwe wartości to: Number, String, Boolean lub Array.

## <a name="operators"></a>Operatory

Dostępne operatory dla **liczb** to:

## <a name="numberin"></a>Numer w
Operator liczbowy ma wartość true, jeśli wartość **klucza** jest jedną z określonych wartości **filtru** . W poniższym przykładzie sprawdza, czy wartość `counter` atrybutu w `data` sekcji wynosi 5 lub 1. 

```json
"advancedFilters": [{
    "operatorType": "NumberIn",
    "key": "data.counter",
    "values": [
        5,
        1
    ]
}]
```


Jeśli klucz jest tablicą, wszystkie wartości w tablicy są sprawdzane względem tablicy wartości filtru. Oto pseudo kod z kluczem: `[v1, v2, v3]` i filtr: `[a, b, c]` . Wszystkie wartości klucza o typach danych, które nie pasują do typu danych filtru, są ignorowane.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            MATCH
```

## <a name="numbernotin"></a>NumberNotIn
NumberNotIn ma wartość true, jeśli wartość **klucza** **nie** jest żadną z określonych wartości **filtru** . W poniższym przykładzie sprawdza, czy wartość `counter` atrybutu w `data` sekcji nie jest 41 i 0. 

```json
"advancedFilters": [{
    "operatorType": "NumberNotIn",
    "key": "data.counter",
    "values": [
        41,
        0
    ]
}]
```

Jeśli klucz jest tablicą, wszystkie wartości w tablicy są sprawdzane względem tablicy wartości filtru. Oto pseudo kod z kluczem: `[v1, v2, v3]` i filtr: `[a, b, c]` . Wszystkie wartości klucza o typach danych, które nie pasują do typu danych filtru, są ignorowane.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            FAIL_MATCH
```

## <a name="numberlessthan"></a>NumberLessThan
Operator NumberLessThan ma wartość true, jeśli wartość **klucza** jest **mniejsza niż** określona wartość **filtru** . W poniższym przykładzie sprawdza, czy wartość `counter` atrybutu w `data` sekcji jest mniejsza niż 100. 

```json
"advancedFilters": [{
    "operatorType": "NumberLessThan",
    "key": "data.counter",
    "value": 100
}]
```

Jeśli klucz jest tablicą, wszystkie wartości w tablicy są sprawdzane względem wartości filtru. Oto pseudo kod z kluczem: `[v1, v2, v3]` . Wszystkie wartości klucza o typach danych, które nie pasują do typu danych filtru, są ignorowane.

```
FOR_EACH key IN (v1, v2, v3)
    IF key < filter
        MATCH
```

## <a name="numbergreaterthan"></a>NumberGreaterThan
Operator NumberGreaterThan ma wartość true, jeśli wartość **klucza** jest **większa niż** określona wartość **filtru** . W poniższym przykładzie sprawdza, czy wartość `counter` atrybutu w `data` sekcji jest większa niż 20. 

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThan",
    "key": "data.counter",
    "value": 20
}]
```

Jeśli klucz jest tablicą, wszystkie wartości w tablicy są sprawdzane względem wartości filtru. Oto pseudo kod z kluczem: `[v1, v2, v3]` . Wszystkie wartości klucza o typach danych, które nie pasują do typu danych filtru, są ignorowane.

```
FOR_EACH key IN (v1, v2, v3)
    IF key > filter
        MATCH
```

## <a name="numberlessthanorequals"></a>NumberLessThanOrEquals
Operator NumberLessThanOrEquals ma wartość true, jeśli wartość **klucza** jest **mniejsza lub równa** określonej wartości **filtru** . W poniższym przykładzie sprawdza, czy wartość `counter` atrybutu w `data` sekcji jest mniejsza niż lub równa 100. 

```json
"advancedFilters": [{
    "operatorType": "NumberLessThanOrEquals",
    "key": "data.counter",
    "value": 100
}]
```

Jeśli klucz jest tablicą, wszystkie wartości w tablicy są sprawdzane względem wartości filtru. Oto pseudo kod z kluczem: `[v1, v2, v3]` . Wszystkie wartości klucza o typach danych, które nie pasują do typu danych filtru, są ignorowane.

```
FOR_EACH key IN (v1, v2, v3)
    IF key <= filter
        MATCH
```

## <a name="numbergreaterthanorequals"></a>NumberGreaterThanOrEquals
Operator NumberGreaterThanOrEquals ma wartość true, jeśli wartość **klucza** jest **większa lub równa** określonej wartości **filtru** . W poniższym przykładzie sprawdza, czy wartość `counter` atrybutu w `data` sekcji jest większa od lub równa 30. 

```json
"advancedFilters": [{
    "operatorType": "NumberGreaterThanOrEquals",
    "key": "data.counter",
    "value": 30
}]
```

Jeśli klucz jest tablicą, wszystkie wartości w tablicy są sprawdzane względem wartości filtru. Oto pseudo kod z kluczem: `[v1, v2, v3]` . Wszystkie wartości klucza o typach danych, które nie pasują do typu danych filtru, są ignorowane.

```
FOR_EACH key IN (v1, v2, v3)
    IF key >= filter
        MATCH
```

## <a name="numberinrange"></a>NumberInRange
Operator NumberInRange ma wartość true, jeśli wartość **klucza** znajduje się w jednym z określonych **zakresów filtru**. W poniższym przykładzie sprawdza, czy wartość `key1` atrybutu w `data` sekcji znajduje się w jednym z dwóch zakresów: 3,14159-999,95, 3000-4000. 

```json
{
    "operatorType": "NumberInRange",
    "key": "data.key1",
    "values": [[3.14159, 999.95], [3000, 4000]]
}
```

`values`Właściwość jest tablicą zakresów. W poprzednim przykładzie jest to tablica dwóch zakresów. Oto przykład tablicy z jednym zakresem do sprawdzenia. 

**Tablica z jednym zakresem:** 
```json
{
    "operatorType": "NumberInRange",
    "key": "data.key1",
    "values": [[3000, 4000]]
}
```

Jeśli klucz jest tablicą, wszystkie wartości w tablicy są sprawdzane względem tablicy wartości filtru. Oto pseudo kod z kluczem: `[v1, v2, v3]` i filtr: tablicę zakresów. W tym pseudo kodzie `a` i `b` są niskie i wysokie wartości każdego zakresu w tablicy. Wszystkie wartości klucza o typach danych, które nie pasują do typu danych filtru, są ignorowane.

```
FOR_EACH (a,b) IN filter.Values
    FOR_EACH key IN (v1, v2, v3)
       IF key >= a AND key <= b
           MATCH
```


## <a name="numbernotinrange"></a>NumberNotInRange
Operator NumberNotInRange ma wartość true, jeśli wartość **klucza** **nie** należy do żadnego z określonych **zakresów filtru**. W poniższym przykładzie sprawdza, czy wartość `key1` atrybutu w `data` sekcji znajduje się w jednym z dwóch zakresów: 3,14159-999,95, 3000-4000. Jeśli jest, operator zwraca wartość false. 

```json
{
    "operatorType": "NumberNotInRange",
    "key": "data.key1",
    "values": [[3.14159, 999.95], [3000, 4000]]
}
```
`values`Właściwość jest tablicą zakresów. W poprzednim przykładzie jest to tablica dwóch zakresów. Oto przykład tablicy z jednym zakresem do sprawdzenia.

**Tablica z jednym zakresem:** 
```json
{
    "operatorType": "NumberNotInRange",
    "key": "data.key1",
    "values": [[3000, 4000]]
}
```

Jeśli klucz jest tablicą, wszystkie wartości w tablicy są sprawdzane względem tablicy wartości filtru. Oto pseudo kod z kluczem: `[v1, v2, v3]` i filtr: tablicę zakresów. W tym pseudo kodzie `a` i `b` są niskie i wysokie wartości każdego zakresu w tablicy. Wszystkie wartości klucza o typach danych, które nie pasują do typu danych filtru, są ignorowane.

```
FOR_EACH (a,b) IN filter.Values
    FOR_EACH key IN (v1, v2, v3)
        IF key >= a AND key <= b
            FAIL_MATCH
```


Operatorem dostępnym dla **wartości logicznych** jest: 

## <a name="boolequals"></a>BoolEquals
Operator BoolEquals ma wartość true, jeśli wartość **klucza** jest określony **Filtr** wartości logicznej. W poniższym przykładzie sprawdza, czy wartość `isEnabled` atrybutu w `data` sekcji jest `true` . 

```json
"advancedFilters": [{
    "operatorType": "BoolEquals",
    "key": "data.isEnabled",
    "value": true
}]
```

Jeśli klucz jest tablicą, wszystkie wartości w tablicy są sprawdzane względem wartości logicznej filtrowania. Oto pseudo kod z kluczem: `[v1, v2, v3]` . Wszystkie wartości klucza o typach danych, które nie pasują do typu danych filtru, są ignorowane.

```
FOR_EACH key IN (v1, v2, v3)
    IF filter == key
        MATCH
```

Dostępne operatory dla **ciągów** są następujące:

## <a name="stringcontains"></a>StringContains
**StringContains** daje w wyniku wartość true, jeśli  wartość klucza **zawiera** dowolne z określonych wartości **filtru** (jako podciągi). W poniższym przykładzie sprawdza, czy wartość `key1` atrybutu w `data` sekcji zawiera jeden z określonych podciągów: `microsoft` lub `azure` . Na przykład `azure data factory` zawiera `azure` . 

```json
"advancedFilters": [{
    "operatorType": "StringContains",
    "key": "data.key1",
    "values": [
        "microsoft", 
        "azure"
    ]
}]
```

Jeśli klucz jest tablicą, wszystkie wartości w tablicy są sprawdzane względem tablicy wartości filtru. Oto pseudo kod z kluczem: `[v1, v2, v3]` i filtr: `[a,b,c]` . Wszystkie wartości klucza o typach danych, które nie pasują do typu danych filtru, są ignorowane.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key CONTAINS filter
            MATCH
```

## <a name="stringnotcontains"></a>StringNotContains
Operator **StringNotContains** ma wartość true, jeśli **klucz** nie **zawiera** określonych wartości **filtru** jako podciągów. Jeśli klucz zawiera jedną z określonych wartości jako podciąg, operator zwraca wartość false. W poniższym przykładzie operator zwraca wartość true tylko wtedy, gdy wartość `key1` atrybutu w `data` sekcji nie ma `contoso` `fabrikam` znaków i jako podciągów. 

```json
"advancedFilters": [{
    "operatorType": "StringNotContains",
    "key": "data.key1",
    "values": [
        "contoso", 
        "fabrikam"
    ]
}]
```

Jeśli klucz jest tablicą, wszystkie wartości w tablicy są sprawdzane względem tablicy wartości filtru. Oto pseudo kod z kluczem: `[v1, v2, v3]` i filtr: `[a,b,c]` . Wszystkie wartości klucza o typach danych, które nie pasują do typu danych filtru, są ignorowane.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key CONTAINS filter
            FAIL_MATCH
```
Zobacz sekcję [ograniczenia](#limitations) bieżącego ograniczenia tego operatora.

## <a name="stringbeginswith"></a>StringBeginsWith
Operator **StringBeginsWith** ma wartość true, jeśli wartość **klucza** **zaczyna się** od którejkolwiek z określonych wartości **filtru** . W poniższym przykładzie sprawdza, czy wartość `key1` atrybutu w `data` sekcji zaczyna się od `event` lub `grid` . Na przykład `event hubs` zaczyna się od `event` .  

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "message"
    ]
}]
```

Jeśli klucz jest tablicą, wszystkie wartości w tablicy są sprawdzane względem tablicy wartości filtru. Oto pseudo kod z kluczem: `[v1, v2, v3]` i filtr: `[a,b,c]` . Wszystkie wartości klucza o typach danych, które nie pasują do typu danych filtru, są ignorowane.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key BEGINS_WITH filter
            MATCH
```

## <a name="stringnotbeginswith"></a>StringNotBeginsWith
Operator **StringNotBeginsWith** ma wartość true, jeśli wartość **klucza** **nie zaczyna się** od żadnej z określonych wartości **filtru** . W poniższym przykładzie sprawdza, czy wartość `key1` atrybutu w `data` sekcji nie zaczyna się od `event` ani `message` .

```json
"advancedFilters": [{
    "operatorType": "StringNotBeginsWith",
    "key": "data.key1",
    "values": [
        "event", 
        "message"
    ]
}]
```

Jeśli klucz jest tablicą, wszystkie wartości w tablicy są sprawdzane względem tablicy wartości filtru. Oto pseudo kod z kluczem: `[v1, v2, v3]` i filtr: `[a,b,c]` . Wszystkie wartości klucza o typach danych, które nie pasują do typu danych filtru, są ignorowane.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key BEGINS_WITH filter
            FAIL_MATCH
```

## <a name="stringendswith"></a>StringEndsWith
Operator **StringEndsWith** ma wartość true, jeśli wartość **klucza** zostanie **zakończona** jedną z określonych wartości **filtru** . W poniższym przykładzie sprawdza, czy wartość `key1` atrybutu w `data` sekcji kończą się znakiem `jpg` lub `jpeg` lub `png` . Na przykład ma `eventgrid.png` koniec `png` .


```json
"advancedFilters": [{
    "operatorType": "StringEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

Jeśli klucz jest tablicą, wszystkie wartości w tablicy są sprawdzane względem tablicy wartości filtru. Oto pseudo kod z kluczem: `[v1, v2, v3]` i filtr: `[a,b,c]` . Wszystkie wartości klucza o typach danych, które nie pasują do typu danych filtru, są ignorowane.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key ENDS_WITH filter
            MATCH
```

## <a name="stringnotendswith"></a>StringNotEndsWith
Operator **StringNotEndsWith** ma wartość true, jeśli wartość **klucza** **nie kończy się** żadnym z określonych wartości **filtru** . W poniższym przykładzie sprawdza, czy wartość `key1` atrybutu w `data` sekcji nie kończy się znakiem `jpg` ani `jpeg` lub `png` . 


```json
"advancedFilters": [{
    "operatorType": "StringNotEndsWith",
    "key": "data.key1",
    "values": [
        "jpg", 
        "jpeg", 
        "png"
    ]
}]
```

Jeśli klucz jest tablicą, wszystkie wartości w tablicy są sprawdzane względem tablicy wartości filtru. Oto pseudo kod z kluczem: `[v1, v2, v3]` i filtr: `[a,b,c]` . Wszystkie wartości klucza o typach danych, które nie pasują do typu danych filtru, są ignorowane.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF key ENDS_WITH filter
            FAIL_MATCH
```

## <a name="stringin"></a>Ciąg w
Operator **String** sprawdza, czy wartość **klucza** **dokładnie pasuje** do jednej z określonych wartości **filtru** . W poniższym przykładzie sprawdza, czy wartość `key1` atrybutu w `data` sekcji jest `contoso` lub `fabrikam` lub `factory` . 

```json
"advancedFilters": [{
    "operatorType": "StringIn",
    "key": "data.key1",
    "values": [
        "contoso", 
        "fabrikam", 
        "factory"
    ]
}]
```

Jeśli klucz jest tablicą, wszystkie wartości w tablicy są sprawdzane względem tablicy wartości filtru. Oto pseudo kod z kluczem: `[v1, v2, v3]` i filtr: `[a,b,c]` . Wszystkie wartości klucza o typach danych, które nie pasują do typu danych filtru, są ignorowane.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            MATCH
```

## <a name="stringnotin"></a>StringNotIn
Operator **StringNotIn** sprawdza, czy wartość **klucza** nie jest **zgodna** z żadną z określonych wartości **filtru** . W poniższym przykładzie sprawdza, czy wartość `key1` atrybutu w `data` sekcji nie jest `aws` i `bridge` . 

```json
"advancedFilters": [{
    "operatorType": "StringNotIn",
    "key": "data.key1",
    "values": [
        "aws", 
        "bridge"
    ]
}]
```

Jeśli klucz jest tablicą, wszystkie wartości w tablicy są sprawdzane względem tablicy wartości filtru. Oto pseudo kod z kluczem: `[v1, v2, v3]` i filtr: `[a,b,c]` . Wszystkie wartości klucza o typach danych, które nie pasują do typu danych filtru, są ignorowane.

```
FOR_EACH filter IN (a, b, c)
    FOR_EACH key IN (v1, v2, v3)
        IF filter == key
            FAIL_MATCH
```


W przypadku wszystkich porównań ciągów nie jest rozróżniana wielkość liter.

> [!NOTE]
> Jeśli kod JSON zdarzenia nie zawiera zaawansowanego klucza filtru, filtr jest evaulated jako **niezgodny** dla następujących operatorów: NumberGreaterThan, NumberGreaterThanOrEquals, NumberLessThan, NumberLessThanOrEquals, Number, BoolEquals, StringContains, StringNotContains, StringBeginsWith, StringNotBeginsWith, StringEndsWith, StringNotEndsWith, String.
> 
>Filtr jest evaulated jako **dopasowany** dla następujących operatorów: NumberNotIn, StringNotIn.


## <a name="isnullorundefined"></a>IsNullOrUndefined
Operator IsNullOrUndefined ma wartość true, jeśli wartość klucza jest RÓWNa NULL lub niezdefiniowana. 

```json
{
    "operatorType": "IsNullOrUndefined",
    "key": "data.key1"
}
```

W poniższym przykładzie brakuje Klucz1, więc operator zwróci wartość true. 

```json
{ 
    "data": 
    { 
        "key2": 5 
    } 
}
```

W poniższym przykładzie Klucz1 ma wartość null, więc operator zwróci wartość true.

```json
{
    "data": 
    { 
        "key1": null
    }
}
```

Jeśli w tych przykładach Klucz1 ma inną wartość, operator zwróci wartość false. 

## <a name="isnotnull"></a>IsNotNull
Operator IsNotNull ma wartość true, jeśli wartość klucza nie ma wartości NULL lub nie jest zdefiniowana. 

```json
{
    "operatorType": "IsNotNull",
    "key": "data.key1"
}
```

## <a name="or-and-and"></a>LUB i
Jeśli określisz jeden filtr z wieloma wartościami, wykonywana jest operacja **lub** , więc wartość pola klucza musi być jedną z tych wartości. Oto przykład:

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/",
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

Jeśli określisz wiele różnych filtrów, operacja **i** zostanie wykonana, więc należy spełnić każdy warunek filtru. Oto przykład: 

```json
"advancedFilters": [
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/microsoft.devtestlab/"
        ]
    },
    {
        "operatorType": "StringContains",
        "key": "Subject",
        "values": [
            "/providers/Microsoft.Compute/virtualMachines/"
        ]
    }
]
```

## <a name="cloudevents"></a>CloudEvents 
Dla zdarzeń w **schemacie CloudEvents** należy użyć następujących wartości dla klucza: `eventid` ,,, `source` `eventtype` `eventtypeversion` lub danych zdarzenia (na przykład `data.key1` ). 

Można również użyć [atrybutów kontekstu rozszerzenia w CloudEvents 1,0](https://github.com/cloudevents/spec/blob/v1.0.1/spec.md#extension-context-attributes). W poniższym przykładzie `comexampleextension1` i `comexampleothervalue` są atrybutami kontekstu rozszerzenia. 

```json
{
    "specversion" : "1.0",
    "type" : "com.example.someevent",
    "source" : "/mycontext",
    "id" : "C234-1234-1234",
    "time" : "2018-04-05T17:31:00Z",
    "subject": null,
    "comexampleextension1" : "value",
    "comexampleothervalue" : 5,
    "datacontenttype" : "application/json",
    "data" : {
        "appinfoA" : "abc",
        "appinfoB" : 123,
        "appinfoC" : true
    }
}
```

Oto przykład użycia atrybutu kontekstu rozszerzenia w filtrze.

```json
"advancedFilters": [{
    "operatorType": "StringBeginsWith",
    "key": "comexampleothervalue",
    "values": [
        "5", 
        "1"
    ]
}]
```


## <a name="limitations"></a>Ograniczenia

Filtrowanie zaawansowane ma następujące ograniczenia:

* 5 filtry zaawansowane i 25 wartości filtru dla wszystkich filtrów dla każdej subskrypcji usługi Event Grid
* 512 znaków na wartość ciągu
* Pięć wartości dla operatora **in** i **Not in**
* `StringNotContains`Operator nie jest obecnie dostępny w portalu.
* Klucze ze znakiem **`.` (kropką)** . Na przykład: `http://schemas.microsoft.com/claims/authnclassreference` lub `john.doe@contoso.com` . Obecnie nie ma obsługi znaków ucieczki w kluczach. 

Ten sam klucz może być używany w więcej niż jednym filtrze.





## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się więcej o filtrowaniu zdarzeń przy użyciu programu PowerShell i interfejsu wiersza polecenia platformy Azure, zobacz [filtrowanie zdarzeń dla Event Grid](how-to-filter-events.md).
* Aby szybko rozpocząć korzystanie z Event Grid, zobacz [Tworzenie i kierowanie zdarzeń niestandardowych z Azure Event Grid](custom-event-quickstart.md).
