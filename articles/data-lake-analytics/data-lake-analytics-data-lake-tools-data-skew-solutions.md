---
title: Rozwiązywanie problemów z pochyleniem Azure Data Lake Tools for Visual Studio
description: Rozwiązywanie potencjalnych rozwiązań dotyczących problemów z pochyleniem danych przy użyciu Azure Data Lake Tools for Visual Studio.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 12/16/2016
ms.openlocfilehash: 0b82ce187c06afa69e54ea93931e1745f0d52674
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92219194"
---
# <a name="resolve-data-skew-problems-by-using-azure-data-lake-tools-for-visual-studio"></a>Resolve data-skew problems by using Azure Data Lake Tools for Visual Studio (Rozwiązywanie problemów z niesymetrycznością danych przy użyciu narzędzi Azure Data Lake Tools for Visual Studio)

## <a name="what-is-data-skew"></a>Co to jest odchylone dane?

Krótko podano, skośność danych jest wartością przestawioną przez nadmierną reprezentację. Załóżmy, że przypisano 50ych ekspertów podatkowych do inspekcji podatków, jeden ekspert dla każdego stanu Stanów Zjednoczonych. Test Wyoming, ponieważ populacja jest mała, ma niewiele do zrobienia. W Kalifornii, jednak ten test jest bardzo zajęty z powodu dużej populacji stanu.
    ![Przykład problemu z pochyleniem danych](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/data-skew-problem.png)

W naszym scenariuszu dane są nierównomiernie dystrybuowane we wszystkich testach podatkowych, co oznacza, że niektóre badania muszą współpracować więcej niż inne. We własnym zadaniu często występują sytuacje, takie jak przykład obtestowania podatku. W celu uzyskania więcej informacji technicznych jeden wierzchołek otrzymuje znacznie więcej danych niż jego elementy równorzędne. sytuacja, w której wierzchołk działa więcej niż pozostałe, a ostatecznie spowalnia całe zadanie. Co się nie dzieje, zadanie może się nie powieść, ponieważ wierzchołki mogą mieć na przykład 5-godzinne ograniczenie środowiska uruchomieniowego i ograniczenie pamięci 6 GB.

## <a name="resolving-data-skew-problems"></a>Rozwiązywanie problemów z pochyleniem danych

Azure Data Lake Tools for Visual Studio może pomóc wykryć, czy zadanie ma problem z pochyleniem danych. Jeśli problem istnieje, możesz go rozwiązać, próbując rozwiązania w tej sekcji.

## <a name="solution-1-improve-table-partitioning"></a>Rozwiązanie 1: ulepszanie partycjonowania tabel

### <a name="option-1-filter-the-skewed-key-value-in-advance"></a>Opcja 1: Filtruj z góry wartość pochylonego klucza

Jeśli nie ma to wpływu na logikę biznesową, można odfiltrować wartości wyższych częstotliwości z góry. Na przykład jeśli w identyfikatorze GUID kolumny istnieje wiele 000-000-000, możesz nie chcieć agregować tej wartości. Przed agregacją można napisać "WHERE GUID! =" 000-000-000 "", aby odfiltrować wartość o wysokiej częstotliwości.

### <a name="option-2-pick-a-different-partition-or-distribution-key"></a>Opcja 2: wybranie innej partycji lub klucza dystrybucji

W poprzednim przykładzie, jeśli chcesz tylko sprawdzić obciążenie związane z inspekcją podatkową w całym kraju/regionie, możesz poprawić dystrybucję danych, wybierając numer IDENTYFIKACYJNy jako klucz. Wybranie innej partycji lub klucza dystrybucji może czasami jednocześnie dystrybuować dane, ale należy upewnić się, że wybór nie ma wpływu na logikę biznesową. Na przykład aby obliczyć sumę podatków dla każdego stanu, możesz określić _stan_ jako klucz partycji. Jeśli ten problem będzie nadal występować, spróbuj użyć opcji 3.

### <a name="option-3-add-more-partition-or-distribution-keys"></a>Opcja 3: Dodaj więcej partycji lub kluczy dystrybucji

Zamiast używać tylko _stanu_ jako klucza partycji, można użyć więcej niż jednego klucza do partycjonowania. Przykładowo rozważ dodanie _kodu pocztowego_ jako dodatkowego klucza partycji, aby zmniejszyć rozmiar partycji danych i równomiernie rozesłać dane.

### <a name="option-4-use-round-robin-distribution"></a>Opcja 4. Korzystanie z rozkładu okrężnego

Jeśli nie możesz znaleźć odpowiedniego klucza dla partycji i dystrybucji, możesz spróbować użyć rozkładu okrężnego. Rozkład Round-Robin traktuje wszystkie wiersze równo i losowo umieszcza je w odpowiednich zasobnikach. Dane są dystrybuowane równomiernie, ale utraci informacje o lokalizacji lokalnej, a wadą, która może również zmniejszyć wydajność zadań dla niektórych operacji. Dodatkowo, jeśli w tym przypadku w tym przypadku jest wykonywane agregowanie pochylonego klucza, problem z pochyleniem danych będzie trwały. Aby dowiedzieć się więcej o dystrybucji okrężnej, zobacz sekcję rozkłady tabeli U-SQL w [CREATE TABLE (U-SQL): Tworzenie tabeli ze schematem](/u-sql/ddl/tables/create/managed/create-table-u-sql-creating-a-table-with-schema#dis_sch).

## <a name="solution-2-improve-the-query-plan"></a>Rozwiązanie 2: ulepszanie planu zapytania

### <a name="option-1-use-the-create-statistics-statement"></a>Opcja 1. Korzystanie z instrukcji CREATE STATISTICs

Język U-SQL zawiera instrukcję CREATE STATISTICs w tabelach. Ta instrukcja zawiera więcej informacji o cechach danych, takich jak dystrybucja wartości, które są przechowywane w tabeli. W przypadku większości zapytań optymalizator zapytań generuje już niezbędne dane statystyczne dla planu zapytania o wysokiej jakości. Czasami może być konieczne zwiększenie wydajności zapytania przez utworzenie dodatkowych statystyk przy użyciu programu CREATE STATISTICs lub zmodyfikowanie projektu zapytania. Aby uzyskać więcej informacji, zobacz stronę [Tworzenie statystyk (U-SQL)](/u-sql/ddl/statistics/create-statistics) .

Przykład kodu:

```usql
CREATE STATISTICS IF NOT EXISTS stats_SampleTable_date ON SampleDB.dbo.SampleTable(date) WITH FULLSCAN;
```

>[!NOTE]
>Informacje statystyczne nie są aktualizowane automatycznie. Jeśli zaktualizujesz dane w tabeli bez ponownego tworzenia statystyk, wydajność zapytań może się nie powieść.

### <a name="option-2-use-skewfactor"></a>Opcja 2: korzystanie z SKEWFACTOR

Jeśli chcesz zsumować podatek dla każdego stanu, musisz użyć grupowania według stanu, metody, która nie pozwala uniknąć problemu związanego z odchyleniami danych. Można jednak podać wskazówkę dotyczącą danych w zapytaniu, aby identyfikować pochylenie danych w kluczach, aby Optymalizator mógł przygotować plan wykonania.

Zazwyczaj można ustawić parametr jako 0,5 i 1, z 0,5 oznacza to, że nie ma dużo pochylenia i 1 oznacza duże pochylenie. Ponieważ Wskazówka ma wpływ na optymalizację planu wykonania dla bieżącej instrukcji i wszystkich instrukcji podrzędnych, należy pamiętać, aby dodać wskazówkę przed potencjalną skośną agregacją klucza.

```usql
SKEWFACTOR (columns) = x
```

Zapewnia wskazówkę, że dana kolumna ma Współczynnik skośności x od 0 (bez pochylenia) do 1 (bardzo ciężki pochylenie).

Przykład kodu:

```usql
//Add a SKEWFACTOR hint.
@Impressions =
    SELECT * FROM
    searchDM.SML.PageView(@start, @end) AS PageView
    OPTION(SKEWFACTOR(Query)=0.5)
    ;
//Query 1 for key: Query, ClientId
@Sessions =
    SELECT
        ClientId,
        Query,
        SUM(PageClicks) AS Clicks
    FROM
        @Impressions
    GROUP BY
        Query, ClientId
    ;
//Query 2 for Key: Query
@Display =
    SELECT * FROM @Sessions
        INNER JOIN @Campaigns
            ON @Sessions.Query == @Campaigns.Query
    ;
```

### <a name="option-3-use-rowcount"></a>Opcja 3: Użyj liczby wierszy
Oprócz SKEWFACTOR, dla konkretnych przypadków sprzężenia z kluczami skośnymi, Jeśli wiesz, że inny połączony zestaw wierszy jest mały, możesz powiedzieć Optymalizatorowi, dodając wskazówkę w instrukcji "U-SQL" przed przyłączeniem. Dzięki temu optymalizator może wybrać strategię sprzężenia emisji, aby zwiększyć wydajność. Należy pamiętać, że liczba wierszy nie rozwiązuje problemu związanego z odchyleniami danych, ale może oferować dodatkową pomoc.

```usql
OPTION(ROWCOUNT = n)
```

Przed przystąpieniem Zidentyfikuj mały zestaw wierszy przed przypisaniem, podając szacowaną liczbę wierszy Integer.

Przykład kodu:

```usql
//Unstructured (24-hour daily log impressions)
@Huge   = EXTRACT ClientId int, ...
            FROM @"wasb://ads@wcentralus/2015/10/30/{*}.nif"
            ;
//Small subset (that is, ForgetMe opt out)
@Small  = SELECT * FROM @Huge
            WHERE Bing.ForgetMe(x,y,z)
            OPTION(ROWCOUNT=500)
            ;
//Result (not enough information to determine simple broadcast JOIN)
@Remove = SELECT * FROM Bing.Sessions
            INNER JOIN @Small ON Sessions.Client == @Small.Client
            ;
```

## <a name="solution-3-improve-the-user-defined-reducer-and-combiner"></a>Rozwiązanie 3: poprawa zdefiniowanego przez użytkownika narzędzia do ograniczania i łączenia

Czasami można napisać operatora zdefiniowanego przez użytkownika, aby zaradzić sobie z skomplikowaną logiką procesów, a dobrze napisanym środkiem ograniczającym i łączenia w niektórych przypadkach zmniejszał problem z pochyleniem danych.

### <a name="option-1-use-a-recursive-reducer-if-possible"></a>Opcja 1: Użyj zmniejszenia cyklicznego, jeśli to możliwe

Domyślnie, zdefiniowane przez użytkownika ograniczenie działa w trybie niecyklicznym, co oznacza, że zmniejszenie pracy dla klucza jest dystrybuowane do jednego wierzchołka. Ale jeśli dane są skośne, ogromne zestawy danych mogą być przetwarzane w jednym wierzchołku i uruchamiane przez dłuższy czas.

Aby zwiększyć wydajność, można dodać atrybut w kodzie, aby zdefiniować zmniejszenie do uruchomienia w trybie cyklicznym. Następnie duże zestawy danych mogą być dystrybuowane do wielu wierzchołków i uruchamiane równolegle, co przyspiesza pracę.

Aby zmienić ograniczenie niecykliczne na cykliczne, należy się upewnić, że algorytm jest asocjacyjny. Na przykład suma jest asocjacyjna, a mediana nie jest. Należy również upewnić się, że dane wejściowe i wyjściowe dla elementu zmniejszającą ten sam schemat.

Atrybut zredukowania cyklicznego:

```usql
[SqlUserDefinedReducer(IsRecursive = true)]
```

Przykład kodu:

```usql
[SqlUserDefinedReducer(IsRecursive = true)]
public class TopNReducer : IReducer
{
    public override IEnumerable<IRow>
        Reduce(IRowset input, IUpdatableRow output)
    {
        //Your reducer code goes here.
    }
}
```

### <a name="option-2-use-row-level-combiner-mode-if-possible"></a>Opcja 2: Użyj trybu łączenia na poziomie wiersza, jeśli jest to możliwe

Podobnie jak w przypadku określonych skośnych przypadków sprzężenia, tryb połączenia próbuje rozesłać ogromny zestaw wartości klucza skośnego do wielu wierzchołków, aby można było wykonywać zadania współbieżnie. Tryb łączenia nie może rozwiązywać problemów z odchyleniami danych, ale może oferować dodatkową pomoc dla bardzo dużych zestawów wartości z kluczami skośnymi.

Domyślnie tryb łączenia jest pełny, co oznacza, że nie można oddzielić lewego zestawu wierszy i prawego zestawu wierszy. Ustawienie trybu jako Left/Right/Inner włącza przyłączanie na poziomie wiersza. System oddziela odpowiednie zestawy wierszy i dystrybuuje je do wielu wierzchołków, które są uruchamiane równolegle. Jednak przed skonfigurowaniem trybu łączenia należy zachować ostrożność, aby upewnić się, że odpowiednie zestawy wierszy mogą być oddzielone.

Poniższy przykład pokazuje rozdzielony zestaw wierszy z lewej strony. Każdy wiersz danych wyjściowych zależy od jednego wiersza wejściowego z lewej strony i może zależeć od wszystkich wierszy z prawej strony z tą samą wartością klucza. Jeśli ustawisz tryb łączenia z lewej strony, system oddziela ogromnie zdefiniowany lewy wiersz do małych i przypisze do wielu wierzchołków.

![Ilustracja przedstawiająca tryb łączenia](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/combiner-mode-illustration.png)

>[!NOTE]
>Jeśli ustawisz niewłaściwy tryb łączenia, kombinacja jest mniej wydajna i wyniki mogą być błędne.

Atrybuty trybu łączenia:

- SqlUserDefinedCombiner (Mode = CombinerMode. Full): każdy wiersz danych wyjściowych może być zależny od wszystkich wierszy danych wejściowych od lewej i prawej z tą samą wartością klucza.

- SqlUserDefinedCombiner (Mode = CombinerMode. Left): każdy wiersz danych wyjściowych zależy od jednego wiersza wejściowego z lewej strony (i potencjalnie wszystkie wiersze z prawej strony z tą samą wartością klucza).

- qlUserDefinedCombiner (Mode = CombinerMode. Right): każdy wiersz danych wyjściowych zależy od jednego wiersza wejściowego z prawej strony (i potencjalnie wszystkie wiersze z lewej strony z tą samą wartością klucza).

- SqlUserDefinedCombiner (Mode = CombinerMode. Inner): każdy wiersz danych wyjściowych zależy od pojedynczego wiersza wejściowego z lewej strony i z prawej strony z tą samą wartością.

Przykład kodu:

```usql
[SqlUserDefinedCombiner(Mode = CombinerMode.Right)]
public class WatsonDedupCombiner : ICombiner
{
    public override IEnumerable<IRow>
        Combine(IRowset left, IRowset right, IUpdatableRow output)
    {
    //Your combiner code goes here.
    }
}
```
