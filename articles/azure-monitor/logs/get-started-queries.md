---
title: Rozpoczynanie pracy z zapytaniami dzienników w Azure Monitor | Microsoft Docs
description: Ten artykuł zawiera samouczek dotyczący rozpoczynania pisania zapytań dzienników w Azure Monitor.
ms.topic: tutorial
author: bwren
ms.author: bwren
ms.date: 10/24/2019
ms.openlocfilehash: c87b70c74a5de9cc4b6c48935b93fdd13295f0f8
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102052334"
---
# <a name="get-started-with-log-queries-in-azure-monitor"></a>Wprowadzenie do zapytań dotyczących dzienników w usłudze Azure Monitor

> [!NOTE]
> Można to zrobić w Twoim środowisku, jeśli zbierasz dane z co najmniej jednej maszyny wirtualnej. Jeśli nie, użyj naszego [środowiska demonstracyjnego](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring_Logs/DemoLogsBlade), co obejmuje wiele przykładowych danych.  Jeśli wiesz już, jak wykonywać zapytania w usłudze KQL, ale wystarczy szybko utworzyć przydatne zapytania w oparciu o typy zasobów, zobacz [okienko zapisane przykładowe zapytania](../logs/example-queries.md).

W tym samouczku dowiesz się, jak pisać zapytania dzienników w Azure Monitor. Pouczysz się, jak:

- Opis struktury zapytania
- Sortuj wyniki zapytania
- Filtruj wyniki zapytania
- Określ zakres czasu
- Wybierz pola do uwzględnienia w wynikach
- Definiowanie i używanie pól niestandardowych
- Agregowanie i grupowanie wyników

Aby zapoznać się z samouczkiem dotyczącym używania Log Analytics w Azure Portal, zobacz [wprowadzenie do Azure Monitor Log Analytics](./log-analytics-tutorial.md).<br>
Aby uzyskać więcej informacji na temat zapytań dzienników w Azure Monitor, zobacz [Omówienie zapytań dzienników w programie Azure monitor](../logs/log-query-overview.md).

Wykonaj poniższe czynności, korzystając z wersji wideo z tego samouczka:

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE42pGX]

## <a name="writing-a-new-query"></a>Pisanie nowego zapytania

Zapytania mogą rozpoczynać się od nazwy tabeli lub polecenia *wyszukiwania* . Należy zacząć od nazwy tabeli, ponieważ definiuje jasno zakres zapytania i poprawia wydajność zapytań oraz przydatność wyników.

> [!NOTE]
> W języku zapytań Kusto używanym przez usługę Azure Monitor jest uwzględniana wielkość liter. Słowa kluczowe języka są zwykle zapisywane przy użyciu małych liter. W przypadku używania nazw tabel lub kolumn w zapytaniu upewnij się, że użyto poprawnej wielkości liter, jak pokazano w okienku schematu.

### <a name="table-based-queries"></a>Zapytania oparte na tabelach

Usługa Azure Monitor organizuje dane dzienników w postaci tabel, z których każda składa się z wielu kolumn. Wszystkie tabele i kolumny są wyświetlane w okienku schematu w Log Analytics w portalu analizy. Zidentyfikuj tabelę, która Cię interesuje, a następnie zapoznaj się z bitem danych:

```Kusto
SecurityEvent
| take 10
```

Pokazane powyżej zapytanie zwraca 10 wyników z tabeli *SecurityEvent* w określonej kolejności. Jest to bardzo typowy sposób, aby skrócić tabelę i zrozumieć jej strukturę i zawartość. Sprawdźmy, jak to zostało skompilowane:

* Zapytanie rozpoczyna się od nazwy tabeli *SecurityEvent* — ta część definiuje zakres zapytania.
* Znak potoku (|) oddziela polecenia, więc dane wyjściowe pierwszego z nich w danych wejściowych z następującego polecenia. Można dodać dowolną liczbę elementów potokowych.
* Po potoku jest polecenie **Take** , które zwraca określoną liczbę dowolnych rekordów z tabeli.

W rzeczywistości można uruchomić zapytanie nawet bez dodawania, `| take 10` które nadal będzie prawidłowe, ale może to spowodować zwrócenie do 10 000 wyników.

### <a name="search-queries"></a>Zapytania wyszukiwania

Zapytania wyszukiwania są mniej strukturalne i zwykle bardziej dopasowane do znajdowania rekordów zawierających określoną wartość w dowolnej z ich kolumn:

```Kusto
search in (SecurityEvent) "Cryptographic"
| take 10
```

To zapytanie przeszukuje tabelę *SecurityEvent* pod kątem rekordów zawierających frazę "Cryptographic". Z tych rekordów zostaną zwrócone i wyświetlone 10 rekordów. W przypadku pominięcia `in (SecurityEvent)` części i uruchomienia `search "Cryptographic"` , wyszukiwanie przejdzie nad *wszystkie* tabele, co zajmie więcej czasu i jest mniej wydajne.

> [!WARNING]
> Zapytania wyszukiwania są zwykle wolniejsze niż zapytania oparte na tabelach, ponieważ muszą przetwarzać więcej danych. 

## <a name="sort-and-top"></a>Sortuj i Top
Mimo **że warto pobrać** kilka rekordów, wyniki są wybierane i wyświetlane w określonej kolejności. Aby uzyskać widok uporządkowany, można **posortować** według preferowanej kolumny:

```Kusto
SecurityEvent   
| sort by TimeGenerated desc
```

Może to spowodować zwrócenie zbyt wielu wyników, a także może zająć trochę czasu. Powyższe zapytanie sortuje *całą* tabelę SecurityEvent według kolumny TimeGenerated. Następnie Portal analizy ograniczy wyświetlanie do wyświetlania tylko 10 000 rekordów. Ta metoda nie jest optymalna.

Najlepszym sposobem uzyskania tylko 10 ostatnich rekordów jest użycie **górnej**, która sortuje całą tabelę po stronie serwera, a następnie zwraca górne rekordy:

```Kusto
SecurityEvent
| top 10 by TimeGenerated
```

Malejąco jest domyślną kolejności sortowania, więc zwykle pomijamy argument **DESC** . Dane wyjściowe będą wyglądać następująco:

![10 najważniejszych](media/get-started-queries/top10.png)


## <a name="where-filtering-on-a-condition"></a>WHERE: filtrowanie dla warunku
Filtry, zgodnie z ich nazwą, filtrują dane według określonego warunku. Jest to najbardziej typowy sposób ograniczania wyników zapytania do istotnych informacji.

Aby dodać filtr do zapytania, użyj operatora **WHERE** , po którym następuje co najmniej jeden warunek. Na przykład następujące zapytanie zwraca tylko rekordy *SecurityEvent* , w których _poziom_ jest równy _8_:

```Kusto
SecurityEvent
| where Level == 8
```

Podczas pisania warunków filtrowania można użyć następujących wyrażeń:

| Wyrażenie | Opis | Przykład |
|:---|:---|:---|
| == | Sprawdź równość<br>(z uwzględnieniem wielkości liter) | `Level == 8` |
| =~ | Sprawdź równość<br>(bez uwzględniania wielkości liter) | `EventSourceName =~ "microsoft-windows-security-auditing"` |
| !=, <> | Sprawdzanie nierówności<br>(oba wyrażenia są identyczne) | `Level != 4` |
| *oraz*, *lub* | Wymagane między warunkami| `Level == 16 or CommandLine != ""` |

Aby filtrować według wielu warunków, można użyć **i**:

```Kusto
SecurityEvent
| where Level == 8 and EventID == 4672
```

lub wiele potoków, **gdzie** jeden po drugim:

```Kusto
SecurityEvent
| where Level == 8 
| where EventID == 4672
```
    
> [!NOTE]
> Wartości mogą mieć różne typy, więc może być konieczne rzutowanie ich w celu przeprowadzenia porównania w poprawnym typie. Na przykład kolumna *poziomu* SecurityEvent jest typu String, więc musisz rzutować ją na typ liczbowy, na przykład *int* lub *Long*, zanim będzie można używać operatorów numerycznych: `SecurityEvent | where toint(Level) >= 10`

## <a name="specify-a-time-range"></a>Określ zakres czasu

### <a name="time-picker"></a>Wybór godziny

Selektor godziny znajduje się obok przycisku Uruchom i wskazuje, że wysyłamy zapytania tylko do rekordów z ostatnich 24 godzin. Jest to domyślny zakres czasu stosowany do wszystkich zapytań. Aby uzyskać tylko rekordy z ostatniej godziny, wybierz pozycję _Ostatnia godzina_ i ponownie uruchom zapytanie.

![Selektor czasu](media/get-started-queries/timepicker.png)


### <a name="time-filter-in-query"></a>Filtr czasu w zapytaniu

Możesz również zdefiniować własny zakres czasu, dodając do zapytania filtr czasu. Najlepiej umieścić filtr czasu bezpośrednio po nazwie tabeli: 

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| where toint(Level) >= 10
```

W filtrze powyżej "  `ago(30m)` 30 minut temu", więc to zapytanie zwraca tylko rekordy z ostatnich 30 minut. Inne jednostki czasu obejmują dni (2D), minuty (25m) i sekundy (dziesiątkach).


## <a name="project-and-extend-select-and-compute-columns"></a>Projekt i rozszerzona: Wybieranie i kolumny obliczeniowe

Użyj **projektu** , aby wybrać określone kolumny do uwzględnienia w wynikach:

```Kusto
SecurityEvent 
| top 10 by TimeGenerated 
| project TimeGenerated, Computer, Activity
```

Poprzedni przykład generuje te dane wyjściowe:

![Wyniki projektu zapytania](media/get-started-queries/project.png)

Można również użyć **projektu** , aby zmienić nazwy kolumn i zdefiniować nowe. Poniższy przykład używa programu Project, aby wykonać następujące czynności:

* Wybierz tylko *komputer* i *TimeGenerated* oryginalne kolumny.
* Wyświetla kolumnę *Activity* jako *EventDetails*.
* Utwórz nową kolumnę o nazwie *Kod zdarzenia*. Funkcja **substring ()** służy do pobierania tylko pierwszych czterech znaków z pola aktywności.


```Kusto
SecurityEvent
| top 10 by TimeGenerated 
| project Computer, TimeGenerated, EventDetails=Activity, EventCode=substring(Activity, 0, 4)
```

wartość **Rozwiń** zachowuje wszystkie oryginalne kolumny w zestawie wyników i definiuje dodatkowe. Poniższe zapytanie używa **rozszerzeń** , aby dodać kolumnę *Kod zdarzenia* . Zwróć uwagę, że ta kolumna nie może być wyświetlana na końcu tabeli. w takim przypadku należy rozwinąć szczegóły rekordu, aby go wyświetlić.

```Kusto
SecurityEvent
| top 10 by TimeGenerated
| extend EventCode=substring(Activity, 0, 4)
```

## <a name="summarize-aggregate-groups-of-rows"></a>Podsumowanie: agregowanie grup wierszy
Użyj **podsumowania** do identyfikowania grup rekordów, zgodnie z co najmniej jedną kolumną, i Zastosuj do nich agregacje. Najbardziej typowym zastosowaniem **podsumowania** jest *Liczba*, która zwraca liczbę wyników w każdej grupie.

Następujące zapytanie przegląda wszystkie rekordy *wydajności* z ostatniej godziny, grupuje je według obiektu *ObjectName* i zlicza rekordy w każdej grupie: 
```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName
```

Czasami warto zdefiniować grupy według wielu wymiarów. Każda unikatowa kombinacja tych wartości definiuje oddzielną grupę:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize count() by ObjectName, CounterName
```

Innym typowym zastosowaniem jest wykonywanie obliczeń matematycznych lub statystycznych dla każdej grupy. Na przykład poniższy kod oblicza średnią *CounterValue* dla każdego komputera:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer
```

Niestety, wyniki tego zapytania nie są bezwzględne, ponieważ mieszamy różne liczniki wydajności. Aby to bardziej zrozumiałe, należy obliczyć średnią osobno dla każdej kombinacji *CounterName* i *komputera*:

```Kusto
Perf
| where TimeGenerated > ago(1h)
| summarize avg(CounterValue) by Computer, CounterName
```

### <a name="summarize-by-a-time-column"></a>Podsumowanie według kolumny czasu
Grupowanie wyników może być również oparte na kolumnie czas lub innej ciągłej wartości. Po prostu Podsumowując `by TimeGenerated` , można utworzyć grupy dla każdej pojedynczej milisekundy w zakresie czasu, ponieważ są to unikatowe wartości. 

Aby utworzyć grupy na podstawie wartości ciągłych, najlepiej podzielić zakres na jednostki możliwe do zarządzania przy użyciu usługi **bin**. Poniższe zapytanie analizuje rekordy *wydajności* , które mierzą ilość wolnej pamięci (*dostępna pamięć (MB*) na określonym komputerze. Oblicza średnią wartość każdego ciągu 1 godziny w ciągu ostatnich 7 dni:

```Kusto
Perf 
| where TimeGenerated > ago(7d)
| where Computer == "ContosoAzADDS2" 
| where CounterName == "Available MBytes" 
| summarize avg(CounterValue) by bin(TimeGenerated, 1h)
```

Aby dane wyjściowe były wyraźniejsze, wybierz opcję wyświetlania go jako wykresu czasu, pokazując dostępną pamięć w czasie:

![Badaj pamięć w czasie](media/get-started-queries/chart.png)



## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o korzystaniu z danych ciągu w zapytaniu dziennika i [pracy z ciągami w zapytaniach dziennika Azure monitor](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#string-operations).
- Dowiedz się więcej na temat agregowania danych w zapytaniu dziennika z [zaawansowanymi agregacjami w zapytaniach dziennika Azure monitor](/azure/data-explorer/write-queries#advanced-aggregations).
- Dowiedz się, jak dołączać dane z wielu tabel przy użyciu [sprzężeń w kwerendach dzienników Azure monitor](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#joins).
- Zapoznaj się z dokumentacją w całym języku zapytań Kusto w dokumentacji [języka KQL](/azure/kusto/query/).