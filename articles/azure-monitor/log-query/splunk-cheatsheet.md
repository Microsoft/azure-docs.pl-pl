---
title: Splunk Azure Monitor kwerendy dziennika | Microsoft Docs
description: Pomoc dla użytkowników zaznajomionych z usługą Splunk w celu uczenia się zapytań dzienników Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/21/2018
ms.openlocfilehash: c59b5646e011afa6b8487e8145a1cb07e6e2a8ff
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95015587"
---
# <a name="splunk-to-azure-monitor-log-query"></a>Splunk Azure Monitor zapytanie dziennika

Ten artykuł ma na celu ułatwienie użytkownikom, którzy znają Splunk, aby poznać język zapytań Kusto w celu pisania zapytań dzienników w Azure Monitor. Bezpośrednie porównania są wykonywane między tymi dwoma, aby zrozumieć kluczowe różnice, a także podobieństwa, w których można korzystać z istniejącej wiedzy.

## <a name="structure-and-concepts"></a>Struktura i pojęcia

W poniższej tabeli porównano koncepcje i struktury danych między Splunk i dziennikami Azure Monitor.

 | Pojęcie  | Splunk | Azure Monitor |  Komentarz
 | --- | --- | --- | ---
 | Jednostka wdrożenia  | cluster |  cluster |  Azure Monitor zezwala na dowolne zapytania między klastrami. Splunk nie. |
 | Pamięć podręczna danych |  zasobników  |  Zasady pamięci podręcznej i przechowywania |  Kontroluje okres i poziom buforowania danych. To ustawienie ma bezpośredni wpływ na wydajność zapytań i koszt wdrożenia. |
 | Logiczna partycja danych  |  index  |  database  |  Umożliwia logiczne rozdzielenie danych. Obie implementacje zezwalają na złożenie i łączenie między tymi partycjami. |
 | Metadane zdarzeń strukturalnych | Nie dotyczy | table (stolik) |  Splunk nie ma koncepcji uwidocznionej w języku wyszukiwania metadanych zdarzeń. Dzienniki Azure Monitor mają koncepcję tabeli, która zawiera kolumny. Każde wystąpienie zdarzenia jest zamapowane na wiersz. |
 | Rekord danych | event | wiersz |  Tylko zmiana terminologii. |
 | Atrybut rekordu danych | pole |  kolumna |  W Azure Monitor jest to wstępnie zdefiniowane jako część struktury tabeli. W Splunk każde zdarzenie ma swój własny zestaw pól. |
 | Types | typu |  typu |  Azure Monitor typy danych są bardziej jawne, ponieważ są ustawione w kolumnach. Obie funkcje umożliwiają dynamiczne działanie z typami danych i w przybliżeniu równoważnym zestawem elementów DataType, w tym obsługi JSON. |
 | Zapytanie i wyszukiwanie  | search | query |  Pojęcia są zasadniczo takie same między Azure Monitor i Splunk. |
 | Czas pozyskiwania zdarzeń | Czas systemowy | ingestion_time() |  W Splunk każde zdarzenie pobiera sygnaturę czasową systemową, która jest indeksowana przez zdarzenie. W Azure Monitor można zdefiniować zasady o nazwie ingestion_time, które ujawniają kolumnę systemową, do której można odwoływać się za pomocą funkcji ingestion_time (). |

## <a name="functions"></a>Funkcje

Poniższa tabela zawiera funkcje w Azure Monitor, które są równoważne z funkcjami Splunk.

|Splunk | Azure Monitor |Komentarz
|---|---|---
|strcat | strcat()| jedno |
|split  | split() | jedno |
|if     | Jeśli ()   | jedno |
|tonumber | todouble()<br>tolong()<br>toint() | jedno |
|prawym górnym<br>dołu |toupper()<br>tolower()|jedno |
| Zastąp | replace() | jedno<br> Należy również zauważyć, że chociaż `replace()` w obu produktach są używane trzy parametry, parametry są różne. |
| substr — | substring() | jedno<br>Należy również zauważyć, że Splunk używa indeksów jednostronicowych. Azure Monitor notatki dotyczące indeksów liczonych od zera. |
| ToLower |  tolower() | jedno |
| ToUpper | toupper() | jedno |
| match | dopasowuje wyrażenie regularne |  (2)  |
| wyrażeń | dopasowuje wyrażenie regularne | W Splunk, `regex` jest operatorem. W Azure Monitor jest operatorem relacyjnym. |
| searchmatch | == | W programie Splunk `searchmatch` umożliwia wyszukiwanie dokładnego ciągu.
| losowo | rand()<br>Rand (n) | Funkcja Splunk zwraca liczbę z przestawu od zera do 2<sup>31</sup>-1. Azure Monitor "zwraca liczbę z zakresu od 0,0 do 1,0 lub w przypadku podanego parametru, między 0 a n-1.
| teraz | now() | jedno
| relative_time | totimespan() | jedno<br>W Azure Monitor Splunk odpowiednik relative_time (datetimeVal, offsetVal) to datetimeVal + ToTimeSpan (offsetVal).<br>Na przykład, <code>search &#124; eval n=relative_time(now(), "-1d@d")</code> staje się <code>...  &#124; extend myTime = now() - totimespan("1d")</code>.

(1) w Splunk funkcja jest wywoływana z `eval` operatorem. W Azure Monitor jest używany jako część `extend` lub `project` .<br>(2) w Splunk, funkcja jest wywoływana z `eval` operatorem. W Azure Monitor może być używana z `where` operatorem.


## <a name="operators"></a>Operatory

Poniższe sekcje zawierają przykłady użycia różnych operatorów między Splunk i Azure Monitor.

> [!NOTE]
> Na potrzeby poniższego przykładu _reguła_ pola Splunk jest mapowana na tabelę w Azure monitor i domyślna sygnatura czasowa Splunk jest mapowana do kolumny Logs Analytics _ingestion_time ()_ .

### <a name="search"></a>Wyszukiwanie
W Splunk można pominąć `search` słowo kluczowe i określić ciąg nieujęty w cudzysłów. W Azure Monitor należy uruchomić każde zapytanie z `find` , ciąg bez cudzysłowu jest nazwą kolumny, a wartość wyszukiwania musi być ciągiem ujętym w cudzysłów. 

| | Operator | Przykład |
|:---|:---|:---|
| **Splunk** | **search** | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| **Azure Monitor** | **wyświetlić** | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |


### <a name="filter"></a>Filtr
Zapytania dziennika Azure Monitor rozpoczynają się od tabelarycznego zestawu wyników, w którym filtr. W Splunk filtrowanie jest operacją domyślną w bieżącym indeksie. Możesz również użyć `where` operatora w Splunk, ale nie jest to zalecane.

| | Operator | Przykład |
|:---|:---|:---|
| **Splunk** | **search** | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| **Azure Monitor** | **where** | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |

### <a name="getting-n-eventsrows-for-inspection"></a>Pobieranie n zdarzeń/wierszy do inspekcji 
Zapytania dziennika Azure Monitor obsługują również `take` jako alias do `limit` . W Splunk, jeśli wyniki są uporządkowane, `head` zwróci pierwsze n wyników. W Azure Monitor, limit nie jest uporządkowany, ale zwraca pierwsze n znalezionych wierszy.

| | Operator | Przykład |
|:---|:---|:---|
| **Splunk** | **MTP** | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| **Azure Monitor** | **granice** | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |

### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>Pobieranie pierwszych n zdarzeń/wierszy uporządkowanych według pola/kolumny
W przypadku dolnych wyników w Splunk używany `tail` . W Azure Monitor można określić kierunek porządkowania przy użyciu `asc` .

| | Operator | Przykład |
|:---|:---|:---|
| **Splunk** | **MTP** |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| **Azure Monitor** | **Do góry** | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |

### <a name="extending-the-result-set-with-new-fieldscolumns"></a>Rozszerzanie zestawu wyników przy użyciu nowych pól/kolumn
Splunk ma również `eval` funkcję, która nie jest porównywalna z `eval` operatorem. `eval`Operator w Splunk i `extend` operator w Azure monitor obsługują tylko funkcje skalarne i operatory arytmetyczne.

| | Operator | Przykład |
|:---|:---|:---|
| **Splunk** | **powiadomienie** |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| **Azure Monitor** | **sunąć** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |

### <a name="rename"></a>Zmień nazwę 
Azure Monitor używa `project-rename` operatora, aby zmienić nazwę pola. `project-rename` zezwala, aby zapytanie korzystało ze wszystkich indeksów wstępnie skompilowanych dla pola. Splunk ma `rename` operator do wykonania tego samego.

| | Operator | Przykład |
|:---|:---|:---|
| **Splunk** | **ZmieńNazwę** |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| **Azure Monitor** | **projekt — zmiana nazwy** | <code>Office_Hub_OHubBGTaskError<br>&#124; project-rename exception = Date_Exception</code> |

### <a name="format-resultsprojection"></a>Formatuj wyniki/projekcję
Splunk prawdopodobnie nie ma operatora podobnego do `project-away` . Za pomocą interfejsu użytkownika można odfiltrować pola.

| | Operator | Przykład |
|:---|:---|:---|
| **Splunk** | **table** |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| **Azure Monitor** | **projektu**<br>**projekt — poza** | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |

### <a name="aggregation"></a>Agregacja
Zobacz [agregacje w dzienniku Azure monitor kwerendy](/azure/data-explorer/kusto/query/samples?&pivots=azuremonitor#aggregations) dla różnych funkcji agregacji.

| | Operator | Przykład |
|:---|:---|:---|
| **Splunk** | **stats** |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| **Azure Monitor** | **Podsumuj** | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |


### <a name="join"></a>Join
Sprzężenie w Splunk ma istotne ograniczenia. Podzapytanie ma limit 10000 wyników (ustawiony w pliku konfiguracyjnym wdrożenia) i ograniczoną liczbę typów sprzężenia.

| | Operator | Przykład |
|:---|:---|:---|
| **Splunk** | **Złącza** |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias \| join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]</code> |
| **Azure Monitor** | **Złącza** | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |

### <a name="sort"></a>Sortowanie
W Splunk, aby sortować w kolejności rosnącej, należy użyć `reverse` operatora. Azure Monitor obsługuje także Definiowanie miejsca, w którym należy umieścić wartości null, na początku lub na końcu.

| | Operator | Przykład |
|:---|:---|:---|
| **Splunk** | **porządku** |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| **Azure Monitor** | **Porządkuj według** | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |

### <a name="multivalue-expand"></a>Rozszerzanie z wieloma wartościami
Jest to podobny operator w Splunk i Azure Monitor.

| | Operator | Przykład |
|:---|:---|:---|
| **Splunk** | **mvexpand** |  `mvexpand foo` |
| **Azure Monitor** | **mvexpand** | `mvexpand foo` |

### <a name="results-facets-interesting-fields"></a>Aspekty wyników, interesujące pola
W Log Analytics w Azure Portal tylko pierwsza kolumna jest widoczna. Wszystkie kolumny są dostępne za pomocą interfejsu API.

| | Operator | Przykład |
|:---|:---|:---|
| **Splunk** | **pola** |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| **Azure Monitor** | **Facets** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |

### <a name="de-duplicate"></a>Cofnij duplikat
Zamiast tego można użyć `summarize arg_min()` do odwrócenia kolejności wybranego rekordu.

| | Operator | Przykład |
|:---|:---|:---|
| **Splunk** | **deduplikacji** |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| **Azure Monitor** | **Podsumuj arg_max ()** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |

## <a name="next-steps"></a>Następne kroki

- Zapoznaj się z lekcjami dotyczącymi [pisania zapytań dzienników w Azure monitor](get-started-queries.md).
