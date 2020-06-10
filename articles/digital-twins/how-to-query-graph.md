---
title: Zapytanie o wykres bliźniaczy
titleSuffix: Azure Digital Twins
description: Aby uzyskać więcej informacji, zobacz Jak wykonać zapytanie dotyczące grafu bliźniaczych reprezentacjiów cyfrowych platformy Azure.
author: baanders
ms.author: baanders
ms.date: 3/26/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 93043874db6076b26d0fefe447db7acd83547442
ms.sourcegitcommit: 1de57529ab349341447d77a0717f6ced5335074e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84612880"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Tworzenie zapytań dotyczących grafu bliźniaczych reprezentacjiów cyfrowych platformy Azure

W tym artykule szczegółowo opisano użycie [języka magazynu zapytań Digital bliźniaczych reprezentacji na platformie Azure](concepts-query-language.md) w celu zbadania [grafu bliźniaczyego](concepts-twins-graph.md) w celu uzyskania informacji. Zapytania można uruchamiać na grafie przy użyciu [**interfejsów API**](how-to-use-apis-sdks.md)cyfrowych bliźniaczych reprezentacji kwerend platformy Azure.

## <a name="query-syntax"></a>Składnia zapytań

Oto przykładowe zapytania, które ilustrują strukturę języka zapytań i wykonują możliwe operacje zapytań.

Pobieranie [bliźniaczych reprezentacji cyfrowych](concepts-twins-graph.md) według właściwości (łącznie z identyfikatorami i metadanymi):
```sql
SELECT  * 
FROM DigitalTwins T  
WHERE T.firmwareVersion = '1.1'
AND T.$dtId in ['123', '456']
AND T.Temperature = 70
```

Pobierz bliźniaczych reprezentacji cyfrowy według [modelu](concepts-models.md)
```sql
SELECT  * 
FROM DigitalTwins T  
WHERE IS_OF_MODEL(T , 'dtmi:com:contoso:Space;3')
AND T.roomSize > 50
```

> [!TIP]
> Identyfikator dwucyfrowego podpisu jest wysyłany przy użyciu pola metadanych `$dtId` .

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

## <a name="query-based-on-relationships"></a>Zapytanie w oparciu o relacje

W przypadku wykonywania zapytań na podstawie relacji cyfrowych bliźniaczych reprezentacji ' język magazynu zapytań Digital bliźniaczych reprezentacji platformy Azure ma specjalną składnię.

Relacje są ściągane do zakresu zapytania w `FROM` klauzuli. Istotną różnicą od "klasycznych" języków typu SQL jest to, że każde wyrażenie w tej `FROM` klauzuli nie jest tabelą, a `FROM` klauzula wskazuje przechodzenie między różnymi jednostkami i jest zapisywana przy użyciu Digital bliźniaczych reprezentacji wersja systemu Azure `JOIN` . 

Należy przypomnieć, że dzięki możliwościom [modelu](concepts-models.md) Digital bliźniaczych reprezentacji na platformie Azure relacje nie istnieją niezależnie od bliźniaczych reprezentacji. Oznacza to, że język magazynu zapytań Digital bliźniaczych reprezentacji na platformie Azure `JOIN` jest nieco inny niż ogólny kod SQL `JOIN` , ponieważ w tym miejscu nie można wykonywać zapytań niezależnie i muszą one być powiązane z sznurem.
Aby uwzględnić tę różnicę, słowo kluczowe `RELATED` jest używane w `JOIN` klauzuli do odwoływania się do zestawu relacji typu bliźniaczy. 

W poniższej sekcji przedstawiono kilka przykładów tego wyglądu.

> [!TIP]
> Koncepcyjnie funkcja ta naśladuje funkcję CosmosDB skoncentrowaną na dokumentach, gdzie `JOIN` można ją wykonać na obiektach podrzędnych w dokumencie. CosmosDB używa `IN` słowa kluczowego, aby wskazać, że `JOIN` jest przeznaczony do iteracji względem elementów tablicy w bieżącym dokumencie kontekstowym.

### <a name="relationship-based-query-examples"></a>Przykłady zapytań opartych na relacji

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

### <a name="query-the-properties-of-a-relationship"></a>Zapytanie o właściwości relacji

Podobnie jak w przypadku bliźniaczych reprezentacji Digital ma właściwości opisane za pośrednictwem DTDL, relacje mogą również mieć właściwości. Język magazynu zapytań Digital bliźniaczych reprezentacji na platformie Azure umożliwia filtrowanie i projekcję relacji przez przypisanie aliasu do relacji w obrębie `JOIN` klauzuli. 

Na przykład rozważmy relację *servicedBy* , która ma właściwość *reportedCondition* . W poniższym zapytaniu ta relacja ma alias "R", aby można było odwołać się do jego właściwości.

```sql
SELECT T, SBT, R
FROM DIGITALTWINS T
JOIN SBT RELATED T.servicedBy R
WHERE T.$dtId = 'ABC' 
AND R.reportedCondition = 'clean'
```

W powyższym przykładzie Zwróć uwagę na to, jak *reportedCondition* jest właściwością relacji *SERVICEDBY* (a nie z nieruchomą dwuosiową z relacją *servicedBy* ).

### <a name="query-limitations"></a>Ograniczenia zapytania

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
