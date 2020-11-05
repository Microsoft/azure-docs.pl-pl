---
title: Wykonywanie zapytań o graf bliźniaczych reprezentacji
titleSuffix: Azure Digital Twins
description: Aby uzyskać więcej informacji, zobacz Jak wykonać zapytanie dotyczące grafu bliźniaczych reprezentacjiów cyfrowych platformy Azure.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 57b6bac49f0142b008a21accfffb614453cc6aec
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93358154"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Tworzenie zapytań dotyczących grafu bliźniaczych reprezentacjiów cyfrowych platformy Azure

W tym artykule przedstawiono przykłady i więcej szczegółów dotyczących używania [języka zapytań Digital bliźniaczych reprezentacji](concepts-query-language.md) na potrzeby wykonywania zapytań dotyczących [grafu bliźniaczyego](concepts-twins-graph.md) w celu uzyskania informacji. Zapytania można uruchamiać na grafie przy użyciu [**interfejsów API**](/rest/api/digital-twins/dataplane/query)cyfrowych bliźniaczych reprezentacji kwerend platformy Azure.

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

Pozostała część tego artykułu zawiera przykłady sposobu korzystania z tych operacji.

## <a name="query-syntax"></a>Składnia zapytań

Ta sekcja zawiera przykładowe zapytania, które ilustrują strukturę języka zapytań i wykonują możliwe operacje zapytań na [bliźniaczych reprezentacji cyfrowym](concepts-twins-graph.md).

### <a name="show-all-existing-digital-twins"></a>Pokaż wszystkie istniejące bliźniaczych reprezentacji cyfrowe

Oto podstawowe zapytanie, które zwróci listę wszystkich bliźniaczych reprezentacji cyfrowych w wystąpieniu:

```sql
SELECT *
FROM DIGITALTWINS
```

### <a name="select-top-items"></a>Wybierz pierwsze elementy

Można wybrać kilka elementów "Top" w zapytaniu przy użyciu `Select TOP` klauzuli.

```sql
SELECT TOP (5)
FROM DIGITALTWINS
WHERE ...
```

### <a name="count-items"></a>Liczba elementów

Liczbę elementów w zestawie wyników można obliczyć przy użyciu `Select COUNT` klauzuli:

```sql
SELECT COUNT()
FROM DIGITALTWINS
```

Dodaj `WHERE` klauzulę, aby policzyć liczbę elementów spełniających określone kryteria. Oto kilka przykładów zliczania z zastosowanym filtrem na podstawie typu modelu sznurka (Aby uzyskać więcej informacji na temat tej składni, zobacz [*zapytania według modelu*](#query-by-model) poniżej):

```sql
SELECT COUNT()
FROM DIGITALTWINS
WHERE IS_OF_MODEL('dtmi:sample:Room;1')

SELECT COUNT()
FROM DIGITALTWINS c
WHERE IS_OF_MODEL('dtmi:sample:Room;1') AND c.Capacity > 20
```

Można również użyć `COUNT` razem z `JOIN` klauzulą. Oto zapytanie, które zlicza wszystkie żarówki zawarte w panelach lekkich pokojów 1 i 2:

```sql
SELECT COUNT()  
FROM DIGITALTWINS Room  
JOIN LightPanel RELATED Room.contains  
JOIN LightBulb RELATED LightPanel.contains  
WHERE IS_OF_MODEL(LightPanel, 'dtmi:contoso:com:lightpanel;1')  
AND IS_OF_MODEL(LightBulb, 'dtmi:contoso:com:lightbulb ;1')  
AND Room.$dtId IN ['room1', 'room2']
```

### <a name="specify-return-set-with-projections"></a>Określanie zestawu powrotu z projekcjami

Korzystając z projekcji, można wybrać kolumny, które będą zwracane przez zapytanie.

>[!NOTE]
>W tej chwili złożone właściwości nie są obsługiwane. Aby upewnić się, że właściwości projekcji są prawidłowe, Połącz projekcje ze `IS_PRIMITIVE` sprawdzaniem.

Oto przykład zapytania korzystającego z projekcji do zwracania bliźniaczych reprezentacji i relacji. Następujące zapytanie bada *odbiorcę* , *fabrykę* i *krawędź* w scenariuszu, w którym *fabryka* z identyfikatorem *ABC* jest związana z *konsumentem* za pośrednictwem relacji *fabryki. Klient* , a ta relacja jest prezentowana jako *krawędź*.

```sql
SELECT Consumer, Factory, Edge
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
```

Można również użyć projekcji, aby zwrócić Właściwość sznurka. Następujące zapytanie bada Właściwość *name* *odbiorców* , którzy są powiązani z *fabryką* z identyfikatorem *ABC* za pomocą relacji *fabryki. Klient*.

```sql
SELECT Consumer.name
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Consumer.name)
```

Można również użyć projekcji do zwrócenia właściwości relacji. Podobnie jak w poprzednim przykładzie, następujące zapytanie projektuje Właściwość *name* *odbiorców* związanych z *fabryką* z identyfikatorem *ABC* przez relację *fabryki. Klient* ; ale teraz zwraca również dwie właściwości tej relacji, *Prop1* i *prop2*. Robi to poprzez nazwę *krawędzi* relacji i gromadzenie jej właściwości.  

```sql
SELECT Consumer.name, Edge.prop1, Edge.prop2, Factory.area
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name) AND IS_PRIMITIVE(Edge.prop1) AND IS_PRIMITIVE(Edge.prop2)
```

Aliasów można również używać do uproszczenia zapytań z projekcją.

Następujące zapytanie wykonuje te same operacje co w poprzednim przykładzie, ale aliasuje nazwy właściwości do `consumerName` , `first` , `second` i `factoryArea` .

```sql
SELECT Consumer.name AS consumerName, Edge.prop1 AS first, Edge.prop2 AS second, Factory.area AS factoryArea
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name) AND IS_PRIMITIVE(Edge.prop1) AND IS_PRIMITIVE(Edge.prop2)"
```

Poniżej znajduje się podobne zapytanie, które wysyła zapytania do tego samego zestawu, co powyżej, ale zawiera projekty tylko właściwość *Consumer.Name* jako `consumerName` i *Factory* tworzy jako dwuosiowe wszystkie projekty.

```sql
SELECT Consumer.name AS consumerName, Factory
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name)
```

### <a name="query-by-property"></a>Zapytanie według właściwości

Pobieranie bliźniaczych reprezentacji cyfrowych według **Właściwości** (łącznie z identyfikatorami i metadanymi):

```sql
SELECT  *
FROM DigitalTwins T  
WHERE T.firmwareVersion = '1.1'
AND T.$dtId in ['123', '456']
AND T.Temperature = 70
```

> [!TIP]
> Identyfikator dwucyfrowego podpisu jest wysyłany przy użyciu pola metadanych `$dtId` .

Możesz również uzyskać bliźniaczych reprezentacji na podstawie tego, **czy określona właściwość jest zdefiniowana**. Oto zapytanie, które pobiera bliźniaczych reprezentacji, które mają zdefiniowaną Właściwość *Location* :

```sql
SELECT * FROM DIGITALTWINS WHERE IS_DEFINED(Location)
```

Może to ułatwić uzyskanie bliźniaczych reprezentacji przez ich właściwości *tagów* , zgodnie z opisem w temacie [Dodawanie tagów do Digital bliźniaczych reprezentacji](how-to-use-tags.md). Oto zapytanie, które pobiera wszystkie bliźniaczych reprezentacji oznaczone *czerwonymi* :

```sql
SELECT * FROM DIGITALTWINS WHERE IS_DEFINED(tags.red)
```

Możesz również uzyskać bliźniaczych reprezentacji na podstawie **typu właściwości**. Oto zapytanie, które pobiera bliźniaczych reprezentacji, którego właściwość *temperatury* jest liczbą:

```sql
SELECT * FROM DIGITALTWINS T WHERE IS_NUMBER(T.Temperature)
```

### <a name="query-by-model"></a>Zapytanie według modelu

`IS_OF_MODEL`Operatora można użyć do filtrowania na podstawie [**modelu**](concepts-models.md)sznurka.

Jest to zgodne z [dziedziczeniem](concepts-models.md#model-inheritance) i [przechowywaniem wersji](how-to-manage-model.md#update-models), a wynikiem jest **wartość true** dla danej przędzy, jeśli dwuosiowa spełnia jeden z następujących warunków:

* Dwuosiowy realizuje bezpośrednio model dostarczony do `IS_OF_MODEL()` , a numer wersji modelu na przędze jest *większy lub równy* numerowi wersji podanego modelu
* Przędza implementuje model, który *rozszerza* model, który jest dostarczany do `IS_OF_MODEL()` , a rozszerzony numer wersji modelu przędzy jest *większy lub równy* numerowi wersji podanego modelu

Jeśli na przykład kwerenda dotyczy bliźniaczych reprezentacji modelu `dtmi:example:widget;4` , zapytanie zwróci wszystkie bliźniaczych reprezentacji na podstawie **wersji 4 lub nowszej** modelu **widżetu** , a także bliźniaczych reprezentacji w oparciu o wersję **4 lub większą** z **modeli, które dziedziczą z widżetu**.

`IS_OF_MODEL` może przyjmować kilka różnych parametrów, a pozostała część tej sekcji jest przeznaczona dla różnych opcji przeciążenia.

Najprostszym zastosowaniem jest `IS_OF_MODEL` tylko `twinTypeName` parametr: `IS_OF_MODEL(twinTypeName)` .
Oto przykład zapytania, który przekazuje wartość w tym parametrze:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:example:thing;1')
```

Aby określić kolekcję przędzy do przeszukania, gdy istnieje więcej niż jeden (jak w przypadku `JOIN` użycia), Dodaj `twinCollection` parametr: `IS_OF_MODEL(twinCollection, twinTypeName)` .
Oto przykład zapytania, które dodaje wartość dla tego parametru:

```sql
SELECT * FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:example:thing;1')
```

Aby wykonać dokładne dopasowanie, Dodaj `exact` parametr: `IS_OF_MODEL(twinTypeName, exact)` .
Oto przykład zapytania, które dodaje wartość dla tego parametru:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:example:thing;1', exact)
```

Można również przekazać wszystkie trzy argumenty jednocześnie: `IS_OF_MODEL(twinCollection, twinTypeName, exact)` .
Oto przykład zapytania określającego wartość dla wszystkich trzech parametrów:

```sql
SELECT ROOM FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:example:thing;1', exact)
```

### <a name="query-based-on-relationships"></a>Zapytanie w oparciu o relacje

Podczas wykonywania zapytań na podstawie **relacji** cyfrowych bliźniaczych reprezentacji ' język zapytań Digital bliźniaczych reprezentacji platformy Azure ma specjalną składnię.

Relacje są ściągane do zakresu zapytania w `FROM` klauzuli. Istotną różnicą od "klasycznych" języków typu SQL jest to, że każde wyrażenie w tej `FROM` klauzuli nie jest tabelą, a `FROM` klauzula wskazuje przechodzenie między różnymi jednostkami i jest zapisywana przy użyciu Digital bliźniaczych reprezentacji wersja systemu Azure `JOIN` .

Należy przypomnieć, że dzięki możliwościom [modelu](concepts-models.md) Digital bliźniaczych reprezentacji na platformie Azure relacje nie istnieją niezależnie od bliźniaczych reprezentacji. Oznacza to, że język zapytań usługi Azure Digital bliźniaczych reprezentacji `JOIN` jest nieco inny niż ogólny kod SQL `JOIN` , ponieważ w tym miejscu nie można wykonywać zapytań niezależnie od siebie i muszą one być powiązane z sznurem.
Aby uwzględnić tę różnicę, słowo kluczowe `RELATED` jest używane w `JOIN` klauzuli do odwoływania się do zestawu relacji typu bliźniaczy.

W poniższej sekcji przedstawiono kilka przykładów tego wyglądu.

> [!TIP]
> Koncepcyjnie funkcja ta naśladuje funkcję CosmosDB skoncentrowaną na dokumentach, gdzie `JOIN` można ją wykonać na obiektach podrzędnych w dokumencie. CosmosDB używa `IN` słowa kluczowego, aby wskazać, że `JOIN` jest przeznaczony do iteracji względem elementów tablicy w bieżącym dokumencie kontekstowym.

#### <a name="relationship-based-query-examples"></a>Przykłady zapytań opartych na relacji

Aby uzyskać zestaw danych, który zawiera relacje, należy użyć pojedynczej `FROM` instrukcji, a następnie N `JOIN` instrukcji, gdzie `JOIN` instrukcja Express Relationships w wyniku `FROM` instrukcji or `JOIN` .

Oto przykład zapytania opartego na relacji. Ten fragment kodu wybiera wszystkie cyfrowe bliźniaczych reprezentacji z właściwością *ID* "ABC" i wszystkie cyfrowe bliźniaczych reprezentacji powiązane z tymi cyfrowymi bliźniaczych reprezentacji za pośrednictwem relacji *zawiera* .

```sql
SELECT T, CT
FROM DIGITALTWINS T
JOIN CT RELATED T.contains
WHERE T.$dtId = 'ABC'
```

>[!NOTE]
> Deweloper nie musi skorelować tego `JOIN` z wartością klucza w `WHERE` klauzuli (lub określić wartości klucza wbudowanej z `JOIN` definicją). Ta korelacja jest obliczana automatycznie przez system, ponieważ właściwości relacji same identyfikują jednostkę docelową.

#### <a name="query-the-properties-of-a-relationship"></a>Zapytanie o właściwości relacji

Podobnie jak w przypadku bliźniaczych reprezentacji Digital ma właściwości opisane za pośrednictwem DTDL, relacje mogą również mieć właściwości. Możesz badać bliźniaczych reprezentacji **na podstawie właściwości ich relacji**.
Język zapytań usługi Azure Digital bliźniaczych reprezentacji umożliwia filtrowanie i projekcję relacji przez przypisanie aliasu do relacji w obrębie `JOIN` klauzuli.

Na przykład rozważmy relację *servicedBy* , która ma właściwość *reportedCondition* . W poniższym zapytaniu ta relacja ma alias "R", aby można było odwołać się do jego właściwości.

```sql
SELECT T, SBT, R
FROM DIGITALTWINS T
JOIN SBT RELATED T.servicedBy R
WHERE T.$dtId = 'ABC'
AND R.reportedCondition = 'clean'
```

W powyższym przykładzie Zwróć uwagę na to, jak *reportedCondition* jest właściwością relacji *SERVICEDBY* (a nie z nieruchomą dwuosiową z relacją *servicedBy* ).

### <a name="query-with-multiple-joins"></a>Zapytanie z wieloma sprzężeniami

`JOIN`Pojedyncze zapytanie obsługuje maksymalnie pięć s. Umożliwia to jednoczesne przechodzenie między różnymi poziomami relacji.

Oto przykład zapytania z wielosprzężeniem, które pobiera wszystkie żarówki zawarte w panelach lekkich w pokojach 1 i 2.

```sql
SELECT LightBulb
FROM DIGITALTWINS Room
JOIN LightPanel RELATED Room.contains
JOIN LightBulb RELATED LightPanel.contains
WHERE IS_OF_MODEL(LightPanel, 'dtmi:contoso:com:lightpanel;1')
AND IS_OF_MODEL(LightBulb, 'dtmi:contoso:com:lightbulb ;1')
AND Room.$dtId IN ['room1', 'room2']
```

### <a name="other-compound-query-examples"></a>Inne przykładowe zapytania złożone

Można **połączyć** dowolne z powyższych typów zapytań za pomocą operatorów kombinacji, aby dołączać więcej szczegółów w pojedynczym zapytaniu. Oto kilka dodatkowych przykładów zapytania złożonego, które wykonują zapytania dla więcej niż jednego typu dwuosiowego deskryptora.

| Opis | Zapytanie |
| --- | --- |
| Z urządzeń, które pozostało *123* , zwróć urządzenia zestawu deweloperskiego, które obsługują rolę operatora | `SELECT device`<br>`FROM DigitalTwins space`<br>`JOIN device RELATED space.has`<br>`WHERE space.$dtid = 'Room 123'`<br>`AND device.$metadata.model = 'dtmi:contosocom:DigitalTwins:MxChip:3'`<br>`AND has.role = 'Operator'` |
| Pobierz bliźniaczych reprezentacji, które mają relację o nazwie *Contains* z innym przędzą o identyfikatorze *ID1* | `SELECT Room`<br>`FROM DIGITALTWINS Room`<br>`JOIN Thermostat RELATED Room.Contains`<br>`WHERE Thermostat.$dtId = 'id1'` |
| Pobierz wszystkie pokoje tego modelu pokojowego, które są zawarte w *floor11* | `SELECT Room`<br>`FROM DIGITALTWINS Floor`<br>`JOIN Room RELATED Floor.Contains`<br>`WHERE Floor.$dtId = 'floor11'`<br>`AND IS_OF_MODEL(Room, 'dtmi:contosocom:DigitalTwins:Room;1')` |

## <a name="reference-expressions-and-conditions"></a>Odwołanie: wyrażenia i warunki

Ta sekcja zawiera informacje dotyczące operatorów i funkcji dostępnych podczas pisania zapytań usługi Azure Digital bliźniaczych reprezentacji.

### <a name="operators"></a>Operatory

Obsługiwane są następujące operatory:

| Family | Operatory |
| --- | --- |
| Logiczny |AND, OR, NOT |
| Porównanie |=,! =, <, >, <=, >= |
| Contains | W, NZA |

### <a name="functions"></a>Funkcje

Obsługiwane są następujące funkcje sprawdzania typu i rzutowania:

| Funkcja | Opis |
| -------- | ----------- |
| IS_DEFINED | Zwraca wartość logiczną wskazującą, do właściwości przypisano wartość. Jest to obsługiwane tylko wtedy, gdy wartość jest typem pierwotnym. Typy pierwotne obejmują ciąg, wartość logiczną, numeryczną lub `null` . DateTime, typy obiektów i tablice nie są obsługiwane. |
| IS_OF_MODEL | Zwraca wartość logiczną wskazującą, czy określony sznurek pasuje do określonego typu modelu |
| IS_BOOL | Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia jest wartością logiczną. |
| IS_NUMBER | Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia jest liczbą. |
| IS_STRING | Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia jest ciągiem. |
| IS_NULL | Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia ma wartość null. |
| IS_PRIMITIVE | Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia jest typem pierwotnym (String, Boolean, numeric lub `null` ). |
| IS_OBJECT | Zwraca wartość logiczną wskazującą, czy typ określonego wyrażenia jest obiektem JSON. |

Obsługiwane są następujące funkcje ciągów:

| Funkcja | Opis |
| -------- | ----------- |
| STARTSWITH (x, y) | Zwraca wartość Boolean wskazującą, czy pierwsze wyrażenie ciągu zaczyna się od drugiego. |
| ENDSWITH (x, y) | Zwraca wartość Boolean wskazującą, czy pierwsze wyrażenie ciągu ma kończyć się drugim. |

## <a name="run-queries-with-an-api-call"></a>Uruchom zapytania z wywołaniem interfejsu API

Po określeniu ciągu zapytania należy wykonać operację, wykonując wywołanie do **interfejsu API zapytania**.
Poniższy fragment kodu ilustruje to wywołanie z aplikacji klienckiej:

```csharp

var adtInstanceEndpoint = new Uri(your-Azure-Digital-Twins-instance-URL>);
var tokenCredential = new DefaultAzureCredential();

var client = new DigitalTwinsClient(adtInstanceEndpoint, tokenCredential);

string query = "SELECT * FROM digitaltwins";
AsyncPageable<string> result = await client.QueryAsync<string>(query);
```

To wywołanie zwraca wyniki zapytania w postaci obiektu ciągu.

Wywołania zapytań obsługują stronicowanie. Oto kompletny przykład użycia `BasicDigitalTwin` jako typ wyniku zapytania z obsługą błędów i stronicowaniem:

```csharp
string query = "SELECT * FROM digitaltwins";
try
{
    AsyncPageable<BasicDigitalTwin> qresult = client.QueryAsync<BasicDigitalTwin>(query);
    await foreach (BasicDigitalTwin item in qresult)
    {
        // Do something with each result
    }
}
catch (RequestFailedException e)
{
    Log.Error($"Error {e.Status}: {e.Message}");
    throw;
}
```

## <a name="query-limitations"></a>Ograniczenia zapytania

Może istnieć opóźnienie do 10 sekund, po upływie którego zmiany w wystąpieniu zostaną odzwierciedlone w zapytaniach. Na przykład jeśli zostanie wykonana operacja, taka jak tworzenie lub usuwanie bliźniaczych reprezentacji za pomocą interfejsu API DigitalTwins, wynik może nie być natychmiast widoczny w żądaniach interfejsu API zapytań. Oczekiwanie na krótki okres powinno być wystarczające do rozwiązania.

Istnieją dodatkowe ograniczenia dotyczące korzystania z programu `JOIN` .

* W instrukcji nie są obsługiwane żadne podzapytania `FROM` .
* `OUTER JOIN` Semantyka nie jest obsługiwana, znaczenie, jeśli relacja ma rangę zero, cały "wiersz" jest eliminowany z wyjściowego zestawu wyników.
* Głębokość przechodzenia wykresu jest ograniczona do pięciu `JOIN` poziomów na zapytanie.
* Źródło `JOIN` operacji jest ograniczone: zapytanie musi deklarować bliźniaczych reprezentacji, w którym rozpoczyna się zapytanie.

## <a name="query-best-practices"></a>Najlepsze rozwiązania dotyczące zapytań

Poniżej znajdują się porady dotyczące wykonywania zapytań w usłudze Azure Digital bliźniaczych reprezentacji.

* Rozważmy wzorzec zapytania w fazie projektowania modelu. Spróbuj upewnić się, że relacje, które muszą mieć odpowiedzi w jednym zapytaniu, są modelowane jako relacja jednego poziomu.
* Właściwości projektu w sposób, który pozwoli uniknąć dużych zestawów wyników z przechodzenia wykresu.
* Można znacznie zmniejszyć liczbę potrzebnych zapytań, tworząc tablicę bliźniaczych reprezentacji i wykonując zapytania z `IN` operatorem. Rozważmy na przykład scenariusz, w którym *budynki* zawierają *piętra* i *piętra* *.* Aby wyszukać pokoje w budynku, które są gorącą, możesz:

    1. Znajdź piętra w budynku na podstawie `contains` relacji

        ```sql
        SELECT Floor
        FROM DIGITALTWINS Building
        JOIN Floor RELATED Building.contains
        WHERE Building.$dtId = @buildingId
        ```

    2. Aby znaleźć pokoje, zamiast rozważać piętra jeden-do-jednego i uruchamiać `JOIN` zapytanie, aby znaleźć pokoje dla każdej z nich, można wykonać zapytanie z kolekcją podłóg w budynku (nazwane *piętro* w zapytaniu poniżej).

        W aplikacji klienckiej:

        ```csharp
        var floors = "['floor1','floor2', ..'floorn']"; 
        ```

        W zapytaniu:

        ```sql

        SELECT Room
        FROM DIGITALTWINS Floor
        JOIN Room RELATED Floor.contains
        WHERE Floor.$dtId IN ['floor1','floor2', ..'floorn']
        AND Room. Temperature > 72
        AND IS_OF_MODEL(Room, 'dtmi:com:contoso:Room;1')

        ```

* Nazwy właściwości i wartości są rozróżniana wielkość liter, dlatego należy zadbać o użycie dokładnych nazw zdefiniowanych w modelach. Jeśli nazwy właściwości są błędnie napisane lub nieprawidłowo, zestaw wyników jest pusty i nie są zwracane żadne błędy.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [interfejsów API i zestawów SDK Digital bliźniaczych reprezentacji na platformie Azure](how-to-use-apis-sdks.md), w tym interfejsu API zapytania używanego do uruchamiania zapytań z tego artykułu.
