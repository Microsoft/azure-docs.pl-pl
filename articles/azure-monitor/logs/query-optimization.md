---
title: Optymalizowanie zapytań dzienników w Azure Monitor
description: Najlepsze rozwiązania dotyczące optymalizowania zapytań dzienników w Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2019
ms.openlocfilehash: 7b5412b2ca738f5d2099521062e37afcff90e938
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102047336"
---
# <a name="optimize-log-queries-in-azure-monitor"></a>Optymalizowanie zapytań dzienników w Azure Monitor
Dzienniki Azure Monitor korzystają z [usługi Azure Eksplorator danych (ADX)](/azure/data-explorer/) do przechowywania danych dziennika i uruchamiania zapytań w celu analizowania tych danych. Tworzy, zarządza i obsługuje klastry ADX oraz optymalizuje je do obciążeń analizy dzienników. Po uruchomieniu zapytania jest on zoptymalizowany i kierowany do odpowiedniego klastra ADX, który przechowuje dane obszaru roboczego. Zarówno dzienniki Azure Monitor, jak i Azure Eksplorator danych korzystają z wielu automatycznych mechanizmów optymalizacji zapytań. Chociaż Optymalizacja automatyczna zapewnia znaczący wzrost, istnieją przypadki, w których można znacznie poprawić wydajność zapytań. W tym artykule opisano zagadnienia dotyczące wydajności i kilka technik rozwiązywania tych problemów.

Większość technik jest wspólna dla zapytań, które są uruchamiane bezpośrednio na platformie Azure Eksplorator danych i w dziennikach Azure Monitor, chociaż kilka unikatowych zagadnień dotyczących dzienników Azure Monitor, które zostały omówione w tym miejscu. Aby uzyskać więcej porad dotyczących optymalizacji Eksplorator danych platformy Azure, zobacz [najlepsze rozwiązania dotyczące zapytań](/azure/kusto/query/best-practices).

Zoptymalizowane zapytania będą:

- Uruchamiaj szybciej, skracaj łączny czas wykonywania zapytania.
- Mniejsza szansa na ograniczenie lub odrzucanie.

Należy zwrócić szczególną uwagę na zapytania, które są używane do przekroczenia bieżącego i użycia na rozerwanie, takich jak pulpity nawigacyjne, alerty, Logic Apps i Power BI. W takich przypadkach wpływ nieskutecznego zapytania jest znaczny.

## <a name="query-performance-pane"></a>Okienko wydajności zapytań
Po uruchomieniu zapytania w Log Analytics kliknij strzałkę w dół powyżej wyników zapytania, aby wyświetlić okienko wydajności zapytania, które pokazuje wyniki kilku wskaźników wydajności dla zapytania. Te wskaźniki wydajności są opisane w następnej sekcji.

![Okienko wydajności zapytań](media/query-optimization/query-performance-pane.png)


## <a name="query-performance-indicators"></a>Wskaźniki wydajności zapytań

Następujące wskaźniki wydajności zapytań są dostępne dla każdego wykonywanego zapytania:

- [Łączny czas CPU](#total-cpu): ogólne obliczenia używane do przetwarzania zapytania we wszystkich węzłach obliczeniowych. Reprezentuje czas używany do przetwarzania, analizowania i pobierania danych. 

- [Dane używane do przetworzenia zapytania](#data-used-for-processed-query): dane ogólne, do których uzyskano dostęp do przetwarzania zapytania. Wpływ na rozmiar tabeli docelowej, użyty zakres czasu, zastosowane filtry i liczbę kolumn, do których istnieją odwołania.

- [Przedział czasu przetworzonego zapytania](#time-span-of-the-processed-query): różnica między najnowszymi i najstarszymi danymi, do których uzyskano dostęp do przetwarzania zapytania. Wpływ na jawny zakres czasu określony dla zapytania.

- [Wiek przetworzonych danych](#age-of-processed-data): przerwy między nimi i najstarszych danych, do których uzyskano dostęp do przetwarzania zapytania. Ma wysoce wpływ na wydajność pobierania danych.

- [Liczba obszarów roboczych](#number-of-workspaces): ile obszarów roboczych zostało uzyskanych podczas przetwarzania zapytania z powodu niejawnego lub jawnego wyboru.

- [Liczba regionów](#number-of-regions): ile regionów było dostępnych podczas przetwarzania zapytania na podstawie niejawnego lub jawnego wyboru obszarów roboczych. Zapytania w wielu regionach są znacznie mniej wydajne i wskaźniki wydajności stanowią część pokrycia.

- [Równoległość](#parallelism): wskazuje, ile systemu było w stanie wykonać to zapytanie w wielu węzłach. Dotyczy tylko zapytań, które mają duże użycie procesora CPU. Wpływ na użycie określonych funkcji i operatorów.


## <a name="total-cpu"></a>Łączny czas procesora
Rzeczywisty procesor obliczeniowy, który został zainwestowany w celu przetworzenia tego zapytania we wszystkich węzłach przetwarzania zapytań. Ponieważ większość zapytań jest wykonywanych na dużej liczbie węzłów, zwykle będzie znacznie większa niż czas trwania wykonywania zapytania. 

Zapytanie korzystające z ponad 100 sekund procesora CPU jest traktowane jako zapytanie, które zużywa nadmierne zasoby. Zapytanie, które wykorzystuje ponad 1 000 sekund procesora CPU, jest traktowane jako zapytanie nadstosowane i może być ograniczone.

Czas przetwarzania zapytania jest poświęcany na:
- Pobieranie danych — pobieranie starych danych zajmie więcej czasu niż pobranie ostatnich danych.
- Przetwarzanie danych — logika i ocena danych. 

Oprócz czasu spędzonego w węzłach przetwarzania zapytań istnieje dodatkowy czas, który jest poświęcany przez Azure Monitor dzienników: uwierzytelniaj użytkownika i sprawdź, czy mogą oni uzyskać dostęp do tych danych, zlokalizować magazyn danych, przeanalizować zapytanie i przydzielić węzły przetwarzania zapytań. Ten czas nie jest uwzględniany w łącznym czasie procesora CPU.

### <a name="early-filtering-of-records-prior-of-using-high-cpu-functions"></a>Wczesne Filtrowanie rekordów przed użyciem wysokich funkcji procesora CPU

Niektóre polecenia i funkcje zapytania są intensywnie używane do użycia procesora CPU. Jest to szczególnie prawdziwe w przypadku poleceń, które analizują dane JSON i XML lub wyodrębniają złożone wyrażenia regularne. Takie analizowanie może być wykonywane jawnie za pośrednictwem funkcji [parse_json ()](/azure/kusto/query/parsejsonfunction) lub [parse_xml ()](/azure/kusto/query/parse-xmlfunction) lub niejawnie w przypadku odwoływania się do kolumn dynamicznych.

Te funkcje zużywają procesor proporcjonalnie do liczby wierszy, które są przetwarzane. Najbardziej wydajna Optymalizacja polega na dodaniu tam przypadków, w których warunki w zapytaniu mogą odfiltrować dowolną liczbę rekordów, zanim funkcja intensywnie korzysta z procesora CPU.

Na przykład następujące zapytania generują dokładnie ten sam wynik, ale drugi jest najbardziej wydajny jako warunek [WHERE](/azure/kusto/query/whereoperator) przed analizą wyklucza wiele rekordów:

```Kusto
//less efficient
SecurityEvent
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| where FileHash != "" and FilePath !startswith "%SYSTEM32"  // Problem: irrelevant results are filtered after all processing and parsing is done
| summarize count() by FileHash, FilePath
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| where FileHash != "" and FilePath !startswith "%SYSTEM32"  // exact removal of results. Early filter is not accurate enough
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```

### <a name="avoid-using-evaluated-where-clauses"></a>Unikaj używania ocenionych klauzul WHERE

Zapytania zawierające klauzule [WHERE](/azure/kusto/query/whereoperator) w ocenianej kolumnie, a nie w kolumnach, które są fizycznie obecne w zestawie danych tracą wydajność. Filtrowanie dla ocenionych kolumn uniemożliwia pewne optymalizacje systemu, gdy są obsługiwane duże zestawy danych.
Na przykład następujące zapytania dają dokładnie ten sam wynik, ale druga jest bardziej wydajna, gdy warunek [WHERE](/azure/kusto/query/whereoperator) odwołuje się do kolumny wbudowanej

```Kusto
//less efficient
Syslog
| extend Msg = strcat("Syslog: ",SyslogMessage)
| where  Msg  has "Error"
| count 
```
```Kusto
//more efficient
Syslog
| where  SyslogMessage  has "Error"
| count 
```

W niektórych przypadkach Szacowana kolumna jest tworzona niejawnie przez aparat przetwarzania zapytań, ponieważ filtrowanie nie jest wykonywane tylko w polu:
```Kusto
//less efficient
SecurityEvent
| where tolower(Process) == "conhost.exe"
| count 
```
```Kusto
//more efficient
SecurityEvent
| where Process =~ "conhost.exe"
| count 
```




### <a name="use-effective-aggregation-commands-and-dimensions-in-summarize-and-join"></a>Użyj efektywnych poleceń agregacji i wymiarów w podsumowaniu i przyłączaniu

Chociaż niektóre polecenia agregujące, takie jak [Max ()](/azure/kusto/query/max-aggfunction), [sum ()](/azure/kusto/query/sum-aggfunction), [Count ()](/azure/kusto/query/count-aggfunction)i [AVG ()](/azure/kusto/query/avg-aggfunction) mają niski wpływ na procesor CPU ze względu na ich logikę, inne są bardziej skomplikowane i obejmują algorytmy heurystyczne i oszacowania, które umożliwiają ich wydajne wykonywanie. Na przykład, [DCount ()](/azure/kusto/query/dcount-aggfunction) używa algorytmu HyperLogLog, aby zapewnić ścisłe oszacowanie do odrębnej liczby dużych zestawów danych bez faktycznego zliczania każdej wartości; funkcje percentylu są podobne do przybliżania przy użyciu najbliższego algorytmu percentylu rangi. Kilka poleceń zawiera opcjonalne parametry w celu zmniejszenia ich wpływu. Na przykład funkcja [MakeSet ()](/azure/kusto/query/makeset-aggfunction) ma opcjonalny parametr definiujący maksymalny rozmiar zestawu, który znacząco wpływa na procesor i pamięć.

Polecenia [Join](/azure/kusto/query/joinoperator?pivots=azuremonitor) i [podsumowujące](/azure/kusto/query/summarizeoperator) mogą spowodować wysokie wykorzystanie procesora CPU podczas przetwarzania dużego zestawu danych. Ich złożoność jest bezpośrednio związana z liczbą możliwych wartości, które są określane jako *Kardynalność* kolumn, które są używane jako `by` podsumowujące lub jako atrybuty sprzężenia. Aby uzyskać wyjaśnienie i optymalizację przyłączania i podsumowywania, zobacz artykuły z dokumentacją i porady dotyczące optymalizacji.

Na przykład następujące zapytania dają dokładnie ten sam wynik, ponieważ **CounterPath** jest zawsze jeden do jednego zmapowany do **CounterName** i **ObjectName**. Druga z nich jest bardziej wydajna, ponieważ wymiar agregacji jest mniejszy:

```Kusto
//less efficient
Perf
| summarize avg(CounterValue) 
by CounterName, CounterPath, ObjectName
```
```Kusto
//make the group expression more compact improve the performance
Perf
| summarize avg(CounterValue), any(CounterName), any(ObjectName) 
by CounterPath
```

Użycie procesora CPU może również mieć wpływ na warunki lub kolumny rozszerzone, które wymagają intensywnego przetwarzania danych. Wszystkie proste porównania ciągów, takie jak [równości = =](/azure/kusto/query/datatypes-string-operators) i [StartsWith](/azure/kusto/query/datatypes-string-operators) , mają mniej więcej na ten sam wpływ na procesor, podczas gdy zaawansowane dopasowania tekstu mają większy wpływ. [W odróżnieniu od operatora](/azure/kusto/query/datatypes-string-operators) [Contains](/azure/kusto/query/datatypes-string-operators) jest bardziej wydajne. Ze względu na techniki obsługi ciągów bardziej wydajne jest wyszukiwanie ciągów, które są dłuższe niż cztery znaki niż krótkie ciągi.

Na przykład następujące zapytania dają podobne wyniki, w zależności od zasad nazewnictwa komputerów, ale druga z nich jest bardziej wydajna:

```Kusto
//less efficient – due to filter based on contains
Heartbeat
| where Computer contains "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//less efficient – due to filter based on extend
Heartbeat
| extend MyComputer = Computer
| where MyComputer startswith "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//more efficient
Heartbeat
| where Computer startswith "Production" 
| summarize count() by ComputerIP 
```

> [!NOTE]
> Ten wskaźnik przedstawia tylko procesor CPU z klastra bezpośredniego. W zapytaniu obejmującym wiele regionów reprezentuje tylko jeden z regionów. W zapytaniu obejmującym wiele obszarów roboczych może nie zawierać wszystkich obszarów roboczych.

### <a name="avoid-full-xml-and-json-parsing-when-string-parsing-works"></a>Unikaj pełnej analizy kodu XML i JSON, gdy analiza ciągu działa
Pełna analiza obiektu XML lub JSON może zużywać duże zasoby procesora CPU i pamięci. W wielu przypadkach, gdy potrzebne są tylko jeden lub dwa parametry, a obiekty XML lub JSON są proste, łatwiej jest analizować je jako ciągi przy użyciu [operatora analizy](/azure/kusto/query/parseoperator) lub innych [technik analizy tekstu](./parse-text.md). Zwiększenie wydajności będzie bardziej znaczące, ponieważ liczba rekordów w obiekcie XML lub JSON rośnie. Jest to istotne, gdy liczba rekordów osiągnie dziesiątki milionów.

Na przykład następujące zapytanie zwróci dokładnie te same wyniki, co zapytania powyżej, bez wykonywania pełnej analizy kodu XML. Należy zauważyć, że niektóre założenia w strukturze pliku XML, takie jak ten element FilePath, jest dostępny po FileHash i żadna z nich ma atrybuty. 

```Kusto
//even more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" *
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```


## <a name="data-used-for-processed-query"></a>Dane używane do przetworzenia zapytania

Krytycznym czynnikiem w przetwarzaniu zapytania jest ilość danych, które są skanowane i używane do przetwarzania zapytań. Usługa Azure Eksplorator danych korzysta z agresywnych optymalizacji, które znacząco zmniejszają ilość danych w porównaniu z innymi platformami danych. Nadal istnieją kluczowe czynniki w zapytaniu, które mogą mieć wpływ na używany wolumin danych.

Zapytanie, które przetwarza więcej niż 2 000KB danych jest traktowane jako zapytanie, które zużywa nadmierne zasoby. Kwerenda, która przetwarza więcej niż 20 000KB danych jest uważana za zapytanie nadstosowane i może być ograniczone.

W dziennikach Azure Monitor kolumna **TimeGenerated** służy jako sposób indeksowania danych. Ograniczanie wartości **TimeGenerated** do tak wąskiego zakresu, jak to możliwe, spowoduje znaczne zwiększenie wydajności zapytań przez znaczne ograniczenie ilości danych, które należy przetworzyć.

### <a name="avoid-unnecessary-use-of-search-and-union-operators"></a>Unikaj niepotrzebnego użycia operatorów wyszukiwania i złożenia

Innym czynnikiem, który zwiększa dane procesu, jest użycie dużej liczby tabel. Zwykle zdarza się to `search *` , gdy `union *` polecenia i są używane. Te polecenia wymuszają, aby system obliczał i skanował dane ze wszystkich tabel w obszarze roboczym. W niektórych przypadkach w obszarze roboczym mogą znajdować się setki tabel. Spróbuj uniknąć możliwie największej ilości miejsca przy użyciu funkcji wyszukiwania * lub dowolnego wyszukiwania bez określania zakresu dla konkretnej tabeli.

Na przykład następujące zapytania dają dokładnie ten sam wynik, ale ostatni z nich jest najbardziej wydajny:

```Kusto
// This version scans all tables though only Perf has this kind of data
search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This version scans all strings in Perf tables – much more efficient
Perf
| search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This is the most efficient version 
Perf 
| where CounterName == "% Processor Time"  
| summarize count(), avg(CounterValue)  by Computer
```

### <a name="add-early-filters-to-the-query"></a>Dodawanie wczesnych filtrów do zapytania

Inną metodą zredukowania ilości danych jest w [przypadku](/azure/kusto/query/whereoperator) wczesnych warunków w zapytaniu. Platforma Azure Eksplorator danych obejmuje pamięć podręczną, która pozwala na określenie, które partycje zawierają dane istotne dla określonego warunku WHERE. Na przykład, jeśli zapytanie zawiera, `where EventID == 4624` będzie ono dystrybuowane tylko do węzłów, które obsługują partycje ze zgodnymi zdarzeniami.

Poniższe przykładowe zapytania dają dokładnie ten sam wynik, ale druga jest bardziej wydajna:

```Kusto
//less efficient
SecurityEvent
| summarize LoginSessions = dcount(LogonGuid) by Account
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 4624 //Logon GUID is relevant only for logon event
| summarize LoginSessions = dcount(LogonGuid) by Account
```

### <a name="avoid-multiple-scans-of-same-source-data-using-conditional-aggregation-functions-and-materialize-function"></a>Unikaj wielu skanów tych samych danych źródłowych przy użyciu funkcji agregacji warunkowej i funkcji zmaterializowania
Gdy zapytanie zawiera kilka podzapytań, które są scalane za pomocą operatora Join lub Union, każde podzapytanie skanuje całe Źródło oddzielnie, a następnie scala wyniki. Ta wielokrotność zawiera wiele przypadków, w których dane są skanowane — czynnik krytyczny w bardzo dużych zestawach danych.

Technikę, aby uniknąć tej sytuacji, przy użyciu funkcji agregacji warunkowej. Większość [funkcji agregacji](/azure/data-explorer/kusto/query/summarizeoperator#list-of-aggregation-functions) , które są używane w operatorze podsumowania ma zainstalowaną wersję warunkową, która umożliwia użycie jednego operatora podsumowującego z wieloma warunkami. 

Na przykład następujące zapytania pokazują liczbę zdarzeń logowania i liczbę zdarzeń wykonania procesu dla każdego konta. Zwracają one te same wyniki, ale pierwsze skanuje dane dwa razy, drugi skanuje tylko raz:

```Kusto
//Scans the SecurityEvent table twice and perform expensive join
SecurityEvent
| where EventID == 4624 //Login event
| summarize LoginCount = count() by Account
| join 
(
    SecurityEvent
    | where EventID == 4688 //Process execution event
    | summarize ExecutionCount = count(), ExecutedProcesses = make_set(Process) by Account
) on Account
```

```Kusto
//Scan only once with no join
SecurityEvent
| where EventID == 4624 or EventID == 4688 //early filter
| summarize LoginCount = countif(EventID == 4624), ExecutionCount = countif(EventID == 4688), ExecutedProcesses = make_set_if(Process,EventID == 4688)  by Account
```

Inny przypadek, w którym podzapytania są niepotrzebne, to wstępne filtrowanie [operatora analizy](/azure/data-explorer/kusto/query/parseoperator?pivots=azuremonitor) , aby upewnić się, że przetwarza tylko rekordy pasujące do określonego wzorca. Nie jest to konieczne, ponieważ operator analizy i inne podobne operatory zwracają puste wyniki, gdy wzorzec nie jest zgodny. Poniżej przedstawiono dwie zapytania, które zwracają dokładnie te same wyniki, podczas gdy drugie zapytanie skanuje dane tylko raz. W drugim zapytaniu każde polecenie analizy dotyczy tylko jego zdarzeń. Następnie operator rozszerzający pokazuje, jak odwoływać się do pustej sytuacji danych.

```Kusto
//Scan SecurityEvent table twice
union(
SecurityEvent
| where EventID == 8002 
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" *
| distinct FilePath
),(
SecurityEvent
| where EventID == 4799
| parse EventData with * "CallerProcessName\">" CallerProcessName1 "</Data>" * 
| distinct CallerProcessName1
)
```

```Kusto
//Single scan of the SecurityEvent table
SecurityEvent
| where EventID == 8002 or EventID == 4799
| parse EventData with * "<FilePath>" FilePath "</FilePath>" * "<FileHash>" FileHash "</FileHash>" * //Relevant only for event 8002
| parse EventData with * "CallerProcessName\">" CallerProcessName1 "</Data>" *  //Relevant only for event 4799
| extend FilePath = iif(isempty(CallerProcessName1),FilePath,"")
| distinct FilePath, CallerProcessName1
```

Gdy powyższe nie zezwala na unikanie korzystania z podzapytań, inna technika jest wskazówką do aparatu zapytań, że w każdym z nich użyto [funkcji zmaterializowania ()](/azure/data-explorer/kusto/query/materializefunction?pivots=azuremonitor). Jest to przydatne, gdy dane źródłowe pochodzą z funkcji, która jest używana kilka razy w ramach zapytania. Zmaterializowania obowiązuje, gdy dane wyjściowe podzapytania są znacznie mniejsze niż dane wejściowe. Aparat zapytań będzie buforować i ponownie używać danych wyjściowych we wszystkich wystąpieniach.



### <a name="reduce-the-number-of-columns-that-is-retrieved"></a>Zmniejsz liczbę pobieranych kolumn

Ponieważ Eksplorator danych platformy Azure to kolumnowy magazyn danych, pobieranie każdej kolumny jest niezależne od innych. Liczba kolumn, które są pobierane bezpośrednio wpływa na cały rozmiar danych. Należy uwzględnić tylko kolumny w danych wyjściowych, które są potrzebne, [Podsumowując](/azure/kusto/query/summarizeoperator) wyniki lub [projekcję](/azure/kusto/query/projectoperator) określonych kolumn. Usługa Azure Eksplorator danych ma kilka optymalizacji, aby zmniejszyć liczbę pobranych kolumn. Jeśli określa, że kolumna nie jest wymagana, na przykład jeśli nie jest przywoływana w poleceniu [podsumowywania](/azure/kusto/query/summarizeoperator) , nie zostanie ona pobrana.

Na przykład drugie zapytanie może przetwarzać trzy razy więcej danych, ponieważ nie musi pobrać jednej kolumny, ale trzy:

```Kusto
//Less columns --> Less data
SecurityEvent
| summarize count() by Computer  
```
```Kusto
//More columns --> More data
SecurityEvent
| summarize count(), dcount(EventID), avg(Level) by Computer  
```

## <a name="time-span-of-the-processed-query"></a>Przedział czasu przetworzonego zapytania

Wszystkie dzienniki dzienników Azure Monitor są partycjonowane według kolumny **TimeGenerated** . Liczba dostępnych partycji jest bezpośrednio związana z przedziałem czasu. Skrócenie zakresu czasu jest najbardziej wydajnym sposobem zapewnienia wykonywania zapytania o monit.

Zapytanie z przedziałem czasu o więcej niż 15 dni jest traktowane jako zapytanie, które zużywa nadmierne zasoby. Kwerenda z przedziałem czasu przekraczającym 90 dni jest traktowana jako zapytanie nadużycia i może być ograniczone.

Zakres czasu można ustawić za pomocą selektora zakresu czasu na ekranie Log Analytics, zgodnie z opisem w temacie [zakres zapytania dziennika i zakres czasu w Log Analytics Azure monitor](./scope.md#time-range). Jest to zalecana metoda, ponieważ wybrany zakres czasu jest przesyłany do zaplecza przy użyciu metadanych zapytania. 

Alternatywną metodą jest jawne uwzględnienie warunku [WHERE](/azure/kusto/query/whereoperator) w **TimeGenerated** w zapytaniu. Tej metody należy użyć, ponieważ gwarantuje to, że przedział czasu jest stały, nawet gdy zapytanie jest używane z innego interfejsu.
Należy upewnić się, że wszystkie części zapytania mają filtry **TimeGenerated** . Gdy zapytanie zawiera podzapytania pobierające dane z różnych tabel lub tej samej tabeli, każda musi zawierać własny warunek [WHERE](/azure/kusto/query/whereoperator) .

### <a name="make-sure-all-sub-queries-have-timegenerated-filter"></a>Upewnij się, że wszystkie zapytania podrzędne mają filtr TimeGenerated

Na przykład w poniższym zapytaniu, gdy tabela **wydajności** będzie skanowana tylko przez ostatni dzień, tabela **pulsu** zostanie przeskanowana w celu uzyskania całej historii, co może być maksymalnie dwa lata:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize IPs = makeset(ComputerIP, 10) by  Computer
) on Computer
```

Typowy przypadek, w którym występuje błąd jest używany do znajdowania ostatniego wystąpienia [arg_max ()](/azure/kusto/query/arg-max-aggfunction) . Na przykład:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

Można to łatwo naprawić, dodając filtr czasu w wewnętrznej kwerendzie:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    | where TimeGenerated > ago(1d) //filter for this part
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

Innym przykładem tego błędu jest wykonywanie filtrowania zakresu czasu zaraz po [Unii](/azure/kusto/query/unionoperator?pivots=azuremonitor) w kilku tabelach. Podczas wykonywania Unii każde podzapytanie powinno być objęte zakresem. Można użyć instrukcji [Let](/azure/kusto/query/letstatement) , aby zapewnić spójność zakresu.

Na przykład następujące zapytanie przeskanuje wszystkie dane w tabelach *pulsu* i *wydajności* , a nie tylko na ostatni 1 dzień:

```Kusto
Heartbeat 
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | summarize arg_min(TimeGenerated,*) by Computer) 
| where TimeGenerated > ago(1d)
| summarize min(TimeGenerated) by Computer
```

To zapytanie powinno być ustalone w następujący sposób:

```Kusto
let MinTime = ago(1d);
Heartbeat 
| where TimeGenerated > MinTime
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | where TimeGenerated > MinTime
    | summarize arg_min(TimeGenerated,*) by Computer) 
| summarize min(TimeGenerated) by Computer
```

### <a name="time-span-measurement-limitations"></a>Ograniczenia pomiaru zakresu czasu

Pomiar jest zawsze większy niż określony czas rzeczywisty. Na przykład jeśli filtr zapytania wynosi 7 dni, system może skanować 7,5 lub 8,1 dni. Wynika to z faktu, że system dzieli dane na fragmenty w zmiennym rozmiarze. Aby upewnić się, że wszystkie odpowiednie rekordy są skanowane, skanuje całą partycję, która może obejmować kilka godzin, a nawet kilka dni.

Istnieje kilka przypadków, w których system nie może zapewnić dokładnego pomiaru zakresu czasu. Zdarza się to w większości przypadków, gdy zapytanie obejmuje mniej niż dzień lub zapytania obejmujące wiele obszarów roboczych.


> [!IMPORTANT]
> Ten wskaźnik przedstawia tylko dane przetworzone w bezpośrednim klastrze. W zapytaniu obejmującym wiele regionów reprezentuje tylko jeden z regionów. W zapytaniu obejmującym wiele obszarów roboczych może nie zawierać wszystkich obszarów roboczych.

## <a name="age-of-processed-data"></a>Wiek przetworzonych danych
Usługa Azure Eksplorator danych korzysta z kilku warstw magazynowania: lokalnych dysków SSD i znacznie wolniejszych obiektów blob platformy Azure. Im nowsze dane, tym wyższy jest szansa, że jest ona przechowywana w bardziej wydajnej warstwie z mniejszym opóźnieniem, co skraca czas trwania zapytania i procesor CPU. Oprócz samych danych system ma również pamięć podręczną dla metadanych. Im starsze dane, tym mniej szansa, że metadane będą znajdować się w pamięci podręcznej.

Zapytanie, które przetwarza dane niż jest ponad 14 dni, jest traktowane jako zapytanie, które zużywa nadmierne zasoby.


Niektóre zapytania wymagają użycia starych danych, ale zdarzają się sytuacje, w których stare dane są używane przez pomyłkę. Dzieje się tak, gdy zapytania są wykonywane bez podawania zakresu czasu w metadanych, a nie wszystkie odwołania do tabeli obejmują filtr w kolumnie **TimeGenerated** . W takich przypadkach system przeskanuje wszystkie dane, które są przechowywane w tej tabeli. Gdy przechowywanie danych jest długie, może obejmować wiele przedziałów czasu, a tym samym dane, które są tak stare jak okres przechowywania danych.

Takie przypadki mogą być na przykład następujące:

- Nie ustawiono zakresu czasu w Log Analytics z podzapytaniem, które nie jest ograniczone. Zobacz przykład powyżej.
- Korzystanie z interfejsu API bez parametrów opcjonalnych zakresu czasu.
- Korzystanie z klienta, który nie wymusza przedziału czasu, takiego jak łącznik Power BI.

Zapoznaj się z przykładami i uwagami w sekcji poprzedniej, ponieważ są one również odpowiednie w tym przypadku.

## <a name="number-of-regions"></a>Liczba regionów
Istnieje kilka sytuacji, w których pojedyncze zapytanie może być wykonywane w różnych regionach:

- Gdy kilka obszarów roboczych jest jawnie wymienionych i znajdują się w różnych regionach.
- Gdy zapytanie o zakres zasobów pobiera dane, a dane są przechowywane w wielu obszarach roboczych, które znajdują się w różnych regionach.

Wykonywanie zapytań między regionami wymaga, aby system mógł serializować i przesłać do dużych fragmentów danych pośrednich, które są zwykle znacznie większe niż końcowe wyniki zapytania. Pozwala również ograniczyć możliwość wykonywania optymalizacji, algorytmów heurystycznych i wykorzystania pamięci podręcznych przez system.
Jeśli nie ma żadnej prawdziwej przyczyny skanowania wszystkich tych regionów, należy dostosować zakres tak, aby obejmował mniejszą liczbę regionów. Jeśli zakres zasobów jest zminimalizowany, ale nadal są używane wiele regionów, może się to zdarzyć z powodu błędu konfiguracji. Na przykład dzienniki inspekcji i ustawienia diagnostyczne są wysyłane do różnych obszarów roboczych w różnych regionach lub wiele konfiguracji ustawień diagnostycznych. 

Kwerenda obejmująca więcej niż 3 regiony jest traktowana jako zapytanie, które zużywa nadmierne zasoby. Kwerenda obejmująca więcej niż 6 regionów jest traktowana jako nadużycie zapytania i może być ograniczona.

> [!IMPORTANT]
> Gdy zapytanie jest uruchamiane w kilku regionach, pomiary procesora i danych nie będą dokładne i będą przedstawiać pomiar tylko w jednym z regionów.

## <a name="number-of-workspaces"></a>Liczba obszarów roboczych
Obszary robocze są kontenerami logicznymi, które są używane do segregowania i administrowania danymi dzienników. Zaplecze optymalizuje umieszczanie obszarów roboczych w klastrach fizycznych w wybranym regionie.

Użycie wielu obszarów roboczych może wynikać z: 

- Gdzie kilka obszarów roboczych jest jawnie wymienionych.
- Gdy zapytanie o zakres zasobów pobiera dane, a dane są przechowywane w wielu obszarach roboczych.
 
Wykonywanie zapytań między regionami i między klastrami wymaga, aby system mógł serializować i przesłać do dużych fragmentów danych pośrednich, które są zwykle znacznie większe niż końcowe wyniki zapytania. Pozwala również ograniczyć możliwość wykonywania optymalizacji, algorytmów heurystycznych i wykorzystania pamięci podręcznych przez system.

Kwerenda obejmująca więcej niż 5 obszarów roboczych jest traktowana jako zapytanie, które zużywa nadmierne zasoby. Zapytania nie mogą obejmować więcej niż 100 obszarów roboczych.

> [!IMPORTANT]
> W niektórych scenariuszach obejmujących wiele obszarów roboczych pomiary procesora i danych nie będą dokładne i będą reprezentować tylko niektóre obszary robocze.

## <a name="parallelism"></a>Równoległości
Dzienniki Azure Monitor korzystają z dużych klastrów usługi Azure Eksplorator danych do uruchamiania zapytań. te klastry różnią się w zależności od liczby węzłów obliczeniowych. System automatycznie skaluje klastry zgodnie z logiką i pojemnością umieszczenia obszaru roboczego.

Aby efektywnie wykonać zapytanie, jest ono partycjonowane i dystrybuowane do węzłów obliczeniowych na podstawie danych, które są wymagane do przetwarzania. Istnieją sytuacje, w których system nie może wykonać tej czynności efektywnie. Może to prowadzić do długiego czasu trwania zapytania. 

Zachowania zapytań, które mogą obniżyć równoległość, obejmują:

- Użycie funkcji serializacji i okna, takich jak [operator serializacji](/azure/kusto/query/serializeoperator), [Next ()](/azure/kusto/query/nextfunction), [poprzedni ()](/azure/kusto/query/prevfunction)i funkcje [wiersza](/azure/kusto/query/rowcumsumfunction) . W niektórych z tych przypadków można używać szeregów czasowych i funkcji analitycznych użytkownika. Nieefektywna Serializacja może również wystąpić, jeśli następujące operatory nie znajdują się na końcu zapytania: [zakres](/azure/kusto/query/rangeoperator), [Sortowanie](/azure/kusto/query/sortoperator), [kolejność](/azure/kusto/query/orderoperator), [Top](/azure/kusto/query/topoperator), [Top-hitters](/azure/kusto/query/tophittersoperator), [GetSchema](/azure/kusto/query/getschemaoperator).
-    Użycie funkcji agregacji [DCount ()](/azure/kusto/query/dcount-aggfunction) wymusza, aby system miał centralną kopię różnych wartości. Gdy skala danych jest wysoka, rozważ użycie opcjonalnych parametrów funkcji DCount do zredukowania dokładności.
-    W wielu przypadkach operator [Join](/azure/kusto/query/joinoperator?pivots=azuremonitor) obniżyć ogólną wartość równoległości. Badaj rozłączenie losowe jako alternatywę w przypadku problemów z wydajnością.
-    W zapytaniach dotyczących zakresu zasobów, sprawdzanie przed wykonaniem Kubernetes RBAC lub kontrola RBAC platformy Azure może być pokutujące w sytuacjach, gdy istnieje bardzo duża liczba przypisań ról platformy Azure. Może to prowadzić do dłuższego sprawdzenia, które mogłoby spowodować zmniejszenie równoległości. Na przykład zapytanie jest wykonywane w ramach subskrypcji, w której istnieją tysiące zasobów, a każdy zasób ma wiele przypisań roli na poziomie zasobu, a nie w ramach subskrypcji lub grupy zasobów.
-    Jeśli zapytanie przetwarza małe fragmenty danych, jego równoległość będzie niska, ponieważ system nie rozwiąże go w wielu węzłach obliczeniowych.



## <a name="next-steps"></a>Następne kroki

- [Dokumentacja języka zapytań Kusto](/azure/kusto/query/).