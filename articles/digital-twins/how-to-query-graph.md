---
title: Wykonywanie zapytań o graf bliźniaczych reprezentacji
titleSuffix: Azure Digital Twins
description: Aby uzyskać więcej informacji, zobacz Jak wykonać zapytanie dotyczące grafu bliźniaczych reprezentacjiów cyfrowych platformy Azure.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 5d9d7c59244474c76e63271c0be92f835c2ed087
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/03/2020
ms.locfileid: "87543354"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Tworzenie zapytań dotyczących grafu bliźniaczych reprezentacjiów cyfrowych platformy Azure

W tym artykule przedstawiono przykłady i więcej szczegółów dotyczących używania [języka zapytań Digital bliźniaczych reprezentacji](concepts-query-language.md) na potrzeby wykonywania zapytań dotyczących [grafu bliźniaczyego](concepts-twins-graph.md) w celu uzyskania informacji. Zapytania można uruchamiać na grafie przy użyciu [**interfejsów API**](how-to-use-apis-sdks.md)cyfrowych bliźniaczych reprezentacji kwerend platformy Azure.

[!INCLUDE [digital-twins-query-operations.md](../../includes/digital-twins-query-operations.md)]

Pozostała część tego artykułu zawiera przykłady sposobu korzystania z tych operacji.

## <a name="query-syntax"></a>Składnia zapytań

Ta sekcja zawiera przykładowe zapytania, które ilustrują strukturę języka zapytań i wykonują możliwe operacje zapytań.

Pobieranie [bliźniaczych reprezentacji cyfrowych](concepts-twins-graph.md) według właściwości (łącznie z identyfikatorami i metadanymi):
```sql
SELECT  * 
FROM DigitalTwins T  
WHERE T.firmwareVersion = '1.1'
AND T.$dtId in ['123', '456']
AND T.Temperature = 70
```

> [!TIP]
> Identyfikator dwucyfrowego podpisu jest wysyłany przy użyciu pola metadanych `$dtId` .

Możesz również uzyskać bliźniaczych reprezentacji według ich właściwości *tagów* , zgodnie z opisem w temacie [Dodawanie tagów do Digital bliźniaczych reprezentacji](how-to-use-tags.md):
```sql
select * from digitaltwins where is_defined(tags.red) 
```

### <a name="select-top-items"></a>Wybierz pierwsze elementy

Można wybrać kilka elementów "Top" w zapytaniu przy użyciu `Select TOP` klauzuli.

```sql
SELECT TOP (5)
FROM DIGITALTWINS
WHERE property = 42
```

### <a name="query-by-model"></a>Zapytanie według modelu

`IS_OF_MODEL`Operatora można użyć do filtrowania na podstawie [modelu](concepts-models.md)sznurka. Obsługuje dziedziczenie i ma kilka opcji przeciążenia.

Najprostszym zastosowaniem jest `IS_OF_MODEL` tylko `twinTypeName` parametr: `IS_OF_MODEL(twinTypeName)` .
Oto przykład zapytania, który przekazuje wartość w tym parametrze:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:sample:thing;1')
```

Aby określić kolekcję przędzy do przeszukania, gdy istnieje więcej niż jeden (jak w przypadku `JOIN` użycia), Dodaj `twinCollection` parametr: `IS_OF_MODEL(twinCollection, twinTypeName)` .
Oto przykład zapytania, które dodaje wartość dla tego parametru:

```sql
SELECT * FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:sample:thing;1')
```

Aby wykonać dokładne dopasowanie, Dodaj `exact` parametr: `IS_OF_MODEL(twinTypeName, exact)` .
Oto przykład zapytania, które dodaje wartość dla tego parametru:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:sample:thing;1', exact)
```

Można również przekazać wszystkie trzy argumenty jednocześnie: `IS_OF_MODEL(twinCollection, twinTypeName, exact)` .
Oto przykład zapytania określającego wartość dla wszystkich trzech parametrów:

```sql
SELECT ROOM FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:sample:thing;1', exact)
```

### <a name="query-based-on-relationships"></a>Zapytanie w oparciu o relacje

Podczas wykonywania zapytań na podstawie relacji cyfrowych bliźniaczych reprezentacji ' język zapytań Digital bliźniaczych reprezentacji platformy Azure ma specjalną składnię.

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

Podobnie jak w przypadku bliźniaczych reprezentacji Digital ma właściwości opisane za pośrednictwem DTDL, relacje mogą również mieć właściwości. Język zapytań usługi Azure Digital bliźniaczych reprezentacji umożliwia filtrowanie i projekcję relacji przez przypisanie aliasu do relacji w obrębie `JOIN` klauzuli. 

Na przykład rozważmy relację *servicedBy* , która ma właściwość *reportedCondition* . W poniższym zapytaniu ta relacja ma alias "R", aby można było odwołać się do jego właściwości.

```sql
SELECT T, SBT, R
FROM DIGITALTWINS T
JOIN SBT RELATED T.servicedBy R
WHERE T.$dtId = 'ABC' 
AND R.reportedCondition = 'clean'
```

W powyższym przykładzie Zwróć uwagę na to, jak *reportedCondition* jest właściwością relacji *SERVICEDBY* (a nie z nieruchomą dwuosiową z relacją *servicedBy* ).

## <a name="run-queries-with-an-api-call"></a>Uruchom zapytania z wywołaniem interfejsu API

Po określeniu ciągu zapytania należy wykonać operację, wykonując wywołanie do **interfejsu API zapytania**.
Poniższy fragment kodu ilustruje to wywołanie z aplikacji klienckiej:

```csharp
var client = new AzureDigitalTwinsAPIClient(<your-credentials>);
client.BaseUri = new Uri(<your-Azure-Digital-Twins-instance-URL>);

QuerySpecification spec = new QuerySpecification("SELECT * FROM digitaltwins");
QueryResult result = await client.Query.QueryTwinsAsync(spec);
```

To wywołanie zwraca wyniki zapytania w postaci obiektu QueryResult. 

Wywołania zapytań obsługują stronicowanie. Oto kompletny przykład z obsługą błędów i stronicowaniem:

```csharp
string query = "SELECT * FROM digitaltwins";
try
{
    AsyncPageable<string> qresult = client.QueryAsync(query);
    await foreach (string item in qresult) 
    {
        // Do something with each result
    }
}
catch (RequestFailedException e)
{
    Log.Error($"Error {e.Status}: {e.Message}");
    return null;
}
```

## <a name="query-limitations"></a>Ograniczenia zapytania

Może istnieć opóźnienie do 10 sekund, po upływie którego zmiany w wystąpieniu zostaną odzwierciedlone w zapytaniach. Na przykład jeśli zostanie wykonana operacja, taka jak tworzenie lub usuwanie bliźniaczych reprezentacji za pomocą interfejsu API DigitalTwins, wynik może nie być natychmiast widoczny w żądaniach interfejsu API zapytań. Oczekiwanie na krótki okres powinno być wystarczające do rozwiązania.

W trakcie korzystania z `JOIN` wersji zapoznawczej obowiązują dodatkowe ograniczenia.
* W instrukcji nie są obsługiwane żadne podzapytania `FROM` .
* `OUTER JOIN`Semantyka nie jest obsługiwana, znaczenie, jeśli relacja ma rangę zero, cały "wiersz" jest eliminowany z wyjściowego zestawu wyników.
* W publicznej wersji zapoznawczej głębokość przechodzenia wykresu jest ograniczona: dozwolony jest tylko jeden z nich `JOIN` dla każdego zapytania.
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
