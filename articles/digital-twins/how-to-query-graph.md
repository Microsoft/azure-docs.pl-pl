---
title: Wykonywanie zapytań o graf bliźniaczych reprezentacji
titleSuffix: Azure Digital Twins
description: Aby uzyskać więcej informacji, zobacz Jak wykonać zapytanie dotyczące grafu bliźniaczych reprezentacjiów cyfrowych platformy Azure.
author: baanders
ms.author: baanders
ms.date: 11/19/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: 3fd504ec36abae3f00cd2a7eb4e1f7b639be0cea
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462681"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Tworzenie zapytań dotyczących grafu bliźniaczych reprezentacjiów cyfrowych platformy Azure

W tym artykule przedstawiono przykłady zapytań i bardziej szczegółowe instrukcje dotyczące korzystania z **języka zapytań Digital bliźniaczych reprezentacji na platformie Azure** w celu wykonywania zapytań dotyczących [grafu bliźniaczyego](concepts-twins-graph.md) w celu uzyskania informacji. (Aby zapoznać się z wprowadzeniem do języka zapytań i pełną listą jego funkcji, zobacz [*pojęcia: język zapytań*](concepts-query-language.md)).

Ten artykuł rozpoczyna się od przykładowych zapytań, które ilustrują strukturę języka zapytań i typowe operacje zapytań na potrzeby cyfrowego bliźniaczych reprezentacji. Następnie opisano sposób uruchamiania zapytań po ich zapisaniu przy użyciu [interfejsu API](/rest/api/digital-twins/dataplane/query) Digital bliźniaczych reprezentacji Query lub [zestawu SDK](how-to-use-apis-sdks.md#overview-data-plane-apis).

> [!NOTE]
> Jeśli używasz przykładowych zapytań poniżej z wywołaniem interfejsu API lub zestawu SDK, musisz zastąpić tekst zapytania w jednym wierszu.

## <a name="show-all-digital-twins"></a>Pokaż wszystkie bliźniaczych reprezentacji cyfrowe

Oto podstawowe zapytanie, które zwróci listę wszystkich bliźniaczych reprezentacji cyfrowych w wystąpieniu:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="GetAllTwins":::

## <a name="query-by-property"></a>Zapytanie według właściwości

Pobieranie bliźniaczych reprezentacji cyfrowych według **Właściwości** (łącznie z identyfikatorami i metadanymi):

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty1":::

Jak pokazano w powyższym zapytaniu, identyfikator dwucyfrowego przędzy jest wysyłany przy użyciu pola metadanych `$dtId` .

>[!TIP]
> Jeśli używasz Cloud Shell do uruchamiania zapytania z polami metadanych, które zaczynają się od `$` , należy wypróbować, `$` aby umożliwić Cloud Shell wiedzieć, że nie jest to zmienna i powinna być używana jako literał w tekście zapytania.

Możesz również uzyskać bliźniaczych reprezentacji na podstawie tego, **czy określona właściwość jest zdefiniowana**. Oto zapytanie, które pobiera bliźniaczych reprezentacji, które mają zdefiniowaną Właściwość *Location* :

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty2":::

Może to ułatwić uzyskanie bliźniaczych reprezentacji przez ich właściwości *tagów* , zgodnie z opisem w temacie [Dodawanie tagów do Digital bliźniaczych reprezentacji](how-to-use-tags.md). Oto zapytanie, które pobiera wszystkie bliźniaczych reprezentacji oznaczone *czerwonymi*:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

Możesz również uzyskać bliźniaczych reprezentacji na podstawie **typu właściwości**. Oto zapytanie, które pobiera bliźniaczych reprezentacji, którego właściwość *temperatury* jest liczbą:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty3":::

>[!TIP]
> Jeśli właściwość jest typu `Map` , można użyć kluczy i wartości map bezpośrednio w zapytaniu, takich jak:
> :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty4":::

## <a name="query-by-model"></a>Zapytanie według modelu

`IS_OF_MODEL`Operatora można użyć do filtrowania na podstawie [**modelu**](concepts-models.md)sznurka.

Jest to zgodne z [dziedziczeniem](concepts-models.md#model-inheritance) i [przechowywaniem wersji](how-to-manage-model.md#update-models), a wynikiem jest **wartość true** dla danej przędzy, jeśli dwuosiowa spełnia jeden z następujących warunków:

* Dwuosiowy realizuje bezpośrednio model dostarczony do `IS_OF_MODEL()` , a numer wersji modelu na przędze jest *większy lub równy* numerowi wersji podanego modelu
* Przędza implementuje model, który *rozszerza* model, który jest dostarczany do `IS_OF_MODEL()` , a rozszerzony numer wersji modelu przędzy jest *większy lub równy* numerowi wersji podanego modelu

Jeśli na przykład kwerenda dotyczy bliźniaczych reprezentacji modelu `dtmi:example:widget;4` , zapytanie zwróci wszystkie bliźniaczych reprezentacji na podstawie **wersji 4 lub nowszej** modelu **widżetu** , a także bliźniaczych reprezentacji w oparciu o wersję **4 lub większą** z **modeli, które dziedziczą z widżetu**.

`IS_OF_MODEL` może przyjmować kilka różnych parametrów, a pozostała część tej sekcji jest przeznaczona dla różnych opcji przeciążenia.

Najprostszym zastosowaniem jest `IS_OF_MODEL` tylko `twinTypeName` parametr: `IS_OF_MODEL(twinTypeName)` .
Oto przykład zapytania, który przekazuje wartość w tym parametrze:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel1":::

Aby określić kolekcję przędzy do przeszukania, gdy istnieje więcej niż jeden (jak w przypadku `JOIN` użycia), Dodaj `twinCollection` parametr: `IS_OF_MODEL(twinCollection, twinTypeName)` .
Oto przykład zapytania, które dodaje wartość dla tego parametru:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel2":::

Aby wykonać dokładne dopasowanie, Dodaj `exact` parametr: `IS_OF_MODEL(twinTypeName, exact)` .
Oto przykład zapytania, które dodaje wartość dla tego parametru:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel3":::

Można również przekazać wszystkie trzy argumenty jednocześnie: `IS_OF_MODEL(twinCollection, twinTypeName, exact)` .
Oto przykład zapytania określającego wartość dla wszystkich trzech parametrów:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel4":::

## <a name="query-by-relationship"></a>Zapytanie według relacji

Podczas wykonywania zapytań na podstawie **relacji** cyfrowych bliźniaczych reprezentacji ' język zapytań Digital bliźniaczych reprezentacji platformy Azure ma specjalną składnię.

Relacje są ściągane do zakresu zapytania w klauzuli `FROM`. Istotną różnicą od "klasycznych" języków typu SQL jest to, że każde wyrażenie w tej `FROM` klauzuli nie jest tabelą, a `FROM` klauzula wskazuje przechodzenie między różnymi jednostkami i jest zapisywana przy użyciu Digital bliźniaczych reprezentacji wersja systemu Azure `JOIN` .

Należy przypomnieć, że dzięki możliwościom [modelu](concepts-models.md) Digital bliźniaczych reprezentacji na platformie Azure relacje nie istnieją niezależnie od bliźniaczych reprezentacji. Oznacza to, że klauzula `JOIN` w języku zapytań usługi Azure Digital Twins jest nieco inna niż klauzula `JOIN` w zwykłym języku SQL, ponieważ tutaj relacje nie mogą otrzymywać zapytań niezależnie i muszą być powiązane z reprezentacją bliźniaczą.
W celu uwzględnienia tej różnicy słowo kluczowe `RELATED` jest używane w klauzuli `JOIN` do odwoływania się do zestawu relacji reprezentacji bliźniaczej.

W poniższej sekcji przedstawiono kilka przykładów tego wyglądu.

> [!TIP]
> Koncepcyjnie funkcja ta naśladuje funkcję CosmosDB skoncentrowaną na dokumentach, gdzie `JOIN` można ją wykonać na obiektach podrzędnych w dokumencie. CosmosDB używa `IN` słowa kluczowego, aby wskazać, że `JOIN` jest przeznaczony do iteracji względem elementów tablicy w bieżącym dokumencie kontekstowym.

### <a name="relationship-based-query-examples"></a>Przykłady zapytań opartych na relacji

Aby uzyskać zestaw danych, który zawiera relacje, należy użyć pojedynczej `FROM` instrukcji, a następnie N `JOIN` instrukcji, gdzie `JOIN` instrukcja Express Relationships w wyniku `FROM` instrukcji or `JOIN` .

Oto przykład zapytania opartego na relacji. Ten fragment kodu wybiera wszystkie cyfrowe bliźniaczych reprezentacji z właściwością *ID* "ABC" i wszystkie cyfrowe bliźniaczych reprezentacji powiązane z tymi cyfrowymi bliźniaczych reprezentacji za pośrednictwem relacji *zawiera* .

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByRelationship1":::

> [!NOTE]
> Deweloper nie musi skorelować tego `JOIN` z wartością klucza w `WHERE` klauzuli (lub określić wartości klucza wbudowanej z `JOIN` definicją). Ta korelacja jest obliczana automatycznie przez system, ponieważ właściwości relacji same identyfikują jednostkę docelową.

### <a name="query-the-properties-of-a-relationship"></a>Zapytanie o właściwości relacji

Relacje mogą mieć właściwości podobnie do sposobu, w jaki cyfrowe reprezentacje bliźniacze mają właściwości opisywane za pośrednictwem języka DTDL. Możesz badać bliźniaczych reprezentacji **na podstawie właściwości ich relacji**.
Język zapytań usługi Azure Digital bliźniaczych reprezentacji umożliwia filtrowanie i projekcję relacji przez przypisanie aliasu do relacji w obrębie `JOIN` klauzuli.

Na przykład rozważmy relację *servicedBy* , która ma właściwość *reportedCondition* . W poniższym zapytaniu ta relacja ma alias "R", aby można było odwołać się do jego właściwości.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByRelationship2":::

W powyższym przykładzie Zwróć uwagę na to, jak *reportedCondition* jest właściwością relacji *SERVICEDBY* (a nie z nieruchomą dwuosiową z relacją *servicedBy* ).

### <a name="query-with-multiple-joins"></a>Zapytanie z wieloma sprzężeniami

`JOIN`Pojedyncze zapytanie obsługuje maksymalnie pięć s. Umożliwia to jednoczesne przechodzenie między różnymi poziomami relacji.

Oto przykład zapytania z wielosprzężeniem, które pobiera wszystkie żarówki zawarte w panelach lekkich w pokojach 1 i 2.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByRelationship3":::

## <a name="count-items"></a>Liczba elementów

Liczbę elementów w zestawie wyników można obliczyć przy użyciu `Select COUNT` klauzuli:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectCount1":::

Dodaj `WHERE` klauzulę, aby policzyć liczbę elementów spełniających określone kryteria. Oto kilka przykładów zliczania z zastosowanym filtrem na podstawie typu modelu sznurka (Aby uzyskać więcej informacji na temat tej składni, zobacz [*zapytania według modelu*](#query-by-model) poniżej):

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectCount2":::

Można również użyć `COUNT` razem z `JOIN` klauzulą. Oto zapytanie, które zlicza wszystkie żarówki zawarte w panelach lekkich pokojów 1 i 2:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectCount3":::

## <a name="filter-results-select-top-items"></a>Wyniki filtrowania: wybierz pierwsze elementy

Można wybrać kilka elementów "Top" w zapytaniu przy użyciu `Select TOP` klauzuli.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectTop":::

## <a name="filter-results-specify-return-set-with-projections"></a>Wyniki filtrowania: Określ zestaw zwrotny z projekcjami

Korzystając z projekcji w `SELECT` instrukcji, można wybrać kolumny, które będą zwracane przez zapytanie.

>[!NOTE]
>W tej chwili złożone właściwości nie są obsługiwane. Aby upewnić się, że właściwości projekcji są prawidłowe, Połącz projekcje ze `IS_PRIMITIVE` sprawdzaniem.

Oto przykład zapytania korzystającego z projekcji do zwracania bliźniaczych reprezentacji i relacji. Następujące zapytanie bada *odbiorcę*, *fabrykę* i *krawędź* w scenariuszu, w którym *fabryka* z identyfikatorem *ABC* jest związana z *konsumentem* za pośrednictwem relacji *fabryki. Klient*, a ta relacja jest prezentowana jako *krawędź*.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections1":::

Można również użyć projekcji, aby zwrócić Właściwość sznurka. Następujące zapytanie bada Właściwość *name* *odbiorców* , którzy są powiązani z *fabryką* z identyfikatorem *ABC* za pomocą relacji *fabryki. Klient*.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections2":::

Można również użyć projekcji do zwrócenia właściwości relacji. Podobnie jak w poprzednim przykładzie, następujące zapytanie projektuje Właściwość *name* *odbiorców* związanych z *fabryką* z identyfikatorem *ABC* przez relację *fabryki. Klient*; ale teraz zwraca również dwie właściwości tej relacji, *Prop1* i *prop2*. Robi to poprzez nazwę *krawędzi* relacji i gromadzenie jej właściwości.  

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections3":::

Aliasów można również używać do uproszczenia zapytań z projekcją.

Następujące zapytanie wykonuje te same operacje co w poprzednim przykładzie, ale aliasuje nazwy właściwości do `consumerName` , `first` , `second` , i `factoryArea` .

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections4":::

Poniżej znajduje się podobne zapytanie, które wysyła zapytania do tego samego zestawu, co powyżej, ale zawiera projekty tylko właściwość *Consumer.Name* jako `consumerName` i  tworzy jako dwuosiowe wszystkie projekty.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections5":::

## <a name="build-efficient-queries-with-the-in-operator"></a>Tworzenie wydajnych zapytań za pomocą operatora IN

Można znacznie zmniejszyć liczbę potrzebnych zapytań, tworząc tablicę bliźniaczych reprezentacji i wykonując zapytania z `IN` operatorem. 

Rozważmy na przykład scenariusz, w którym *budynki* zawierają *piętra* i *piętra* *.* Aby wyszukać pokoje w budynku, które są gorącą, należy wykonać następujące czynności.

1. Znajdź piętra w budynku na podstawie `contains` relacji.

    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="INOperatorWithout":::

2. Aby znaleźć pokoje, zamiast rozważać piętra jeden-do-jednego i uruchamiać `JOIN` zapytanie, aby znaleźć pokoje dla każdej z nich, można wykonać zapytanie z kolekcją podłóg w budynku (nazwane *piętro* w zapytaniu poniżej).

    W aplikacji klienckiej:
    
    ```csharp
    var floors = "['floor1','floor2', ..'floorn']"; 
    ```
    
    W zapytaniu:
    
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="INOperatorWith":::

## <a name="other-compound-query-examples"></a>Inne przykładowe zapytania złożone

Można **połączyć** dowolne z powyższych typów zapytań za pomocą operatorów kombinacji, aby dołączać więcej szczegółów w pojedynczym zapytaniu. Oto kilka dodatkowych przykładów zapytania złożonego, które wykonują zapytania dla więcej niż jednego typu dwuosiowego deskryptora.

* Z urządzeń, które pozostało *123* , zwróć urządzenia zestawu deweloperskiego, które obsługują rolę operatora
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="OtherExamples1":::
* Pobierz bliźniaczych reprezentacji, które mają relację o nazwie *Contains* z innym przędzą o identyfikatorze *ID1*
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="OtherExamples2":::
* Pobierz wszystkie pokoje tego modelu pokojowego, które są zawarte w *floor11*
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="OtherExamples3":::

## <a name="run-queries-with-the-api"></a>Uruchamianie zapytań za pomocą interfejsu API

Po określeniu ciągu zapytania należy wykonać operację, wykonując wywołanie do [**interfejsu API zapytania**](/rest/api/digital-twins/dataplane/query).

Interfejs API można wywołać bezpośrednio lub użyć jednego z [zestawów SDK](how-to-use-apis-sdks.md#overview-data-plane-apis) dostępnych dla usługi Azure Digital bliźniaczych reprezentacji.

Poniższy fragment kodu ilustruje wywołanie [zestawu SDK .NET (C#)](/dotnet/api/overview/azure/digitaltwins/client) z aplikacji klienckiej:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="RunQuery":::

Zapytanie użyte w tym wywołaniu zwraca listę bliźniaczych reprezentacji cyfrowych, które powyższy przykład reprezentuje z obiektami [BasicDigitalTwin](/dotnet/api/azure.digitaltwins.core.basicdigitaltwin) . Zwracany typ danych dla każdego zapytania będzie zależeć od tego, co określono w `SELECT` instrukcji:
* Zapytania, które zaczynają się od, `SELECT * FROM ...` zwracają listę bliźniaczych reprezentacji cyfrowych (które mogą być serializowane jako `BasicDigitalTwin` obiekty lub inne niestandardowe typy wieloosiowe, które mogły zostać utworzone).
* Zapytania, które zaczynają się w formacie `SELECT <A>, <B>, <C> FROM ...` , zwróci słownika z kluczami `<A>` , `<B>` i `<C>` .
* Inne formaty `SELECT` instrukcji można przystąpić, aby zwracały dane niestandardowe. Warto rozważyć utworzenie własnych klas do obsługi bardzo dostosowanych zestawów wyników. 

### <a name="query-with-paging"></a>Zapytanie ze stronicowaniem

Wywołania zapytań obsługują stronicowanie. Oto kompletny przykład użycia `BasicDigitalTwin` jako typ wyniku zapytania z obsługą błędów i stronicowaniem:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="FullQuerySample":::

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [interfejsów API i zestawów SDK Digital bliźniaczych reprezentacji na platformie Azure](how-to-use-apis-sdks.md), w tym interfejsu API zapytania używanego do uruchamiania zapytań z tego artykułu.
