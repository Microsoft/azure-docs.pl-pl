---
title: Analizowanie danych tekstowych w dziennikach Azure Monitor | Microsoft Docs
description: W tym artykule opisano różne opcje analizowania danych dziennika w Azure Monitor rekordy, gdy dane są pozyskiwane i pobierane w kwerendzie, porównując zalety poszczególnych.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/04/2018
ms.openlocfilehash: 2e2babd39dc9dadf06c9a3ca3292e021e0c1deaa
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102037187"
---
# <a name="parse-text-data-in-azure-monitor-logs"></a>Analizowanie danych tekstowych w dziennikach Azure Monitor
Niektóre dane dziennika zbierane przez Azure Monitor będą zawierać wiele informacji w jednej właściwości. Analizowanie tych danych w wielu właściwościach ułatwia korzystanie z nich w zapytaniach. Typowym przykładem jest [Dziennik niestandardowy](../agents/data-sources-custom-logs.md) , który zbiera cały wpis dziennika z wieloma wartościami w jednej właściwości. Tworząc osobne właściwości różnych wartości, można wyszukiwać i agregowania na każdej z nich.

W tym artykule opisano różne opcje analizowania danych dziennika w Azure Monitor podczas pozyskiwania danych i pobierania ich w zapytaniu, porównując zalety każdej z nich.


## <a name="parsing-methods"></a>Metody analizy
Dane można analizować w czasie pozyskiwania, gdy dane są zbierane lub w czasie wykonywania zapytania podczas analizowania danych za pomocą zapytania. Każda strategia ma unikatowe zalety, zgodnie z poniższym opisem.

### <a name="parse-data-at-collection-time"></a>Analizowanie danych w czasie zbierania
Podczas analizowania danych w czasie zbierania można skonfigurować [niestandardowe pola](../logs/custom-fields.md) , które tworzą nowe właściwości w tabeli. Zapytania nie muszą zawierać żadnej logiki analizy i po prostu używają tych właściwości jako innych pól w tabeli.

Zalety tej metody:

- Łatwiejsza kwerenda zebranych danych, ponieważ nie trzeba dołączać poleceń analizy do zapytania.
- Lepsza wydajność zapytań, ponieważ zapytanie nie musi wykonywać analizy.
 
Wadą tej metody są następujące:

- Musi być zdefiniowana z góry. Nie może zawierać danych, które zostały już zebrane.
- W przypadku zmiany logiki analizy zostanie ona zastosowana tylko do nowych danych.
- Mniej opcji analizy dostępnych w zapytaniach.
- Zwiększa czas oczekiwania na zbieranie danych.
- Błędy mogą być trudne do obsłużenia.


### <a name="parse-data-at-query-time"></a>Analizowanie danych w czasie wykonywania zapytania
Podczas analizowania danych w czasie wykonywania zapytania należy uwzględnić w zapytaniu logikę umożliwiającą analizowanie danych w wielu polach. Sama sama tabela nie jest modyfikowana.

Zalety tej metody:

- Dotyczy dowolnych danych, w tym danych, które zostały już zebrane.
- Zmiany w logice mogą być stosowane natychmiast do wszystkich danych.
- Elastyczne opcje analizy, w tym wstępnie zdefiniowana logika dla określonych struktur danych.
 
Wadą tej metody są następujące:

- Wymaga bardziej złożonych zapytań. Można to ograniczyć przy użyciu funkcji, [Aby symulować tabelę](#use-function-to-simulate-a-table).
- Należy replikować logikę analizy w wielu zapytaniach. Może udostępniać pewne logiki za poorednictwem funkcji.
- Może tworzyć koszty w przypadku uruchamiania złożonych logiki dla bardzo dużych zestawów rekordów (miliardów rekordów).

## <a name="parse-data-as-its-collected"></a>Analizowanie danych w miarę ich zbierania
Zobacz [Tworzenie pól niestandardowych w Azure monitor](../logs/custom-fields.md) , aby uzyskać szczegółowe informacje na temat analizowania danych w miarę ich zbierania. Spowoduje to utworzenie właściwości niestandardowych w tabeli, które mogą być używane przez zapytania tak samo jak każda inna właściwość.

## <a name="parse-data-in-query-using-patterns"></a>Analizowanie danych w kwerendzie przy użyciu wzorców
Gdy dane, które mają zostać poddane analizie, mogą być identyfikowane przez wzorzec powtarzany w wielu rekordach, można użyć różnych operatorów w [języku zapytania Kusto](/azure/kusto/query/) , aby wyodrębnić określony fragment danych do jednej lub kilku nowych właściwości.

### <a name="simple-text-patterns"></a>Proste wzorce tekstu

Użyj operatora [Parse](/azure/kusto/query/parseoperator) w zapytaniu, aby utworzyć co najmniej jedną właściwość niestandardową, którą można wyodrębnić z wyrażenia ciągu. Należy określić wzorzec do zidentyfikowania i nazwy właściwości do utworzenia. Jest to szczególnie przydatne w przypadku danych z ciągami klucz-wartość o postaci podobnej do _klucz = wartość_.

Należy rozważyć użycie dziennika niestandardowego z danymi w następującym formacie.

```
Time=2018-03-10 01:34:36 Event Code=207 Status=Success Message=Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
Time=2018-03-10 01:33:33 Event Code=208 Status=Warning Message=Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
Time=2018-03-10 01:35:44 Event Code=209 Status=Success Message=Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
Time=2018-03-10 01:38:22 Event Code=302 Status=Error Message=Application could not connect to database
Time=2018-03-10 01:31:34 Event Code=303 Status=Error Message=Application lost connection to database
```

Następujące zapytanie przeanalizuje te dane do poszczególnych właściwości. Linia z _projektem_ jest dodawana, aby zwracać tylko obliczone właściwości, a nie _rawData_, która jest pojedynczą właściwością przechowującą cały wpis z dziennika niestandardowego.

```Kusto
MyCustomLog_CL
| parse RawData with * "Time=" EventTime " Event Code=" Code " Status=" Status " Message=" Message
| project EventTime, Code, Status, Message
```

Poniżej znajduje się kolejny przykład, który dzieli nazwę użytkownika nazwy UPN w tabeli _usługi Azure_ .

```Kusto
AzureActivity
| parse  Caller with UPNUserPart "@" * 
| where UPNUserPart != "" //Remove non UPN callers (apps, SPNs, etc)
| distinct UPNUserPart, Caller
```


### <a name="regular-expressions"></a>Wyrażenia regularne
Jeśli dane można zidentyfikować za pomocą wyrażenia regularnego, można użyć [funkcji, które używają wyrażeń regularnych](/azure/kusto/query/re2) do wyodrębniania pojedynczych wartości. Poniższy przykład używa [wyodrębnienia](/azure/kusto/query/extractfunction) w celu rozdzielenia pola _nazwy UPN_ z rekordów _usługi Azure_ , a następnie zwrócenia odrębnych użytkowników.

```Kusto
AzureActivity
| extend UPNUserPart = extract("([a-z.]*)@", 1, Caller) 
| distinct UPNUserPart, Caller
```

Aby umożliwić wydajne analizowanie w dużej skali, Azure Monitor używa wersji RE2 wyrażeń regularnych, która jest podobna, ale nie jest identyczna z niektórymi innymi wariantami wyrażeń regularnych. Aby uzyskać szczegółowe informacje, zapoznaj się ze [składnią wyrażenia RE2](https://aka.ms/kql_re2syntax) .


## <a name="parse-delimited-data-in-a-query"></a>Analizowanie danych z ograniczeniami w zapytaniu
Rozdzielane dane oddziela pola ze wspólnym znakiem, takim jak przecinek w pliku CSV. Użyj funkcji [Split](/azure/kusto/query/splitfunction) , aby przeanalizować rozdzielone dane przy użyciu określonego ogranicznika. Za pomocą operatora [rozszerzając](/azure/kusto/query/extendoperator) można zwrócić wszystkie pola w danych lub określić pojedyncze pola do uwzględnienia w danych wyjściowych.

> [!NOTE]
> Ponieważ funkcja Split zwraca obiekt dynamiczny, wyniki mogą być jawnie rzutowane na typy danych, takie jak ciąg, który ma być używany w operatorach i filtrach.

Należy wziąć pod uwagę dziennik niestandardowy z danymi w następującym formacie CSV.

```
2018-03-10 01:34:36, 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2018-03-10 01:33:33, 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2018-03-10 01:35:44, 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2018-03-10 01:38:22, 302,Error,Application could not connect to database
2018-03-10 01:31:34, 303,Error,Application lost connection to database
```

Poniższe zapytanie przeanalizuje te dane i podsumowuje je według dwóch właściwości obliczeniowych. Pierwszy wiersz dzieli Właściwość  _rawData_ na tablicę ciągów. Każdy z następnych wierszy daje nazwę poszczególnych właściwości i dodaje je do danych wyjściowych przy użyciu funkcji, aby przekonwertować je na odpowiedni typ danych.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields  = split(RawData, ',')
| extend EventTime  = todatetime(CSVFields[0])
| extend Code       = toint(CSVFields[1]) 
| extend Status     = tostring(CSVFields[2]) 
| extend Message    = tostring(CSVFields[3]) 
| where getyear(EventTime) == 2018
| summarize count() by Status,Code
```

## <a name="parse-predefined-structures-in-a-query"></a>Analizowanie wstępnie zdefiniowanych struktur w zapytaniu
Jeśli dane są sformatowane w znanej strukturze, może być możliwe użycie jednej z funkcji w [języku zapytań Kusto](/azure/kusto/query/) do analizowania wstępnie zdefiniowanych struktur:

- [JSON](/azure/kusto/query/parsejsonfunction)
- [XML](/azure/kusto/query/parse-xmlfunction)
- [Protokół IPv4](/azure/kusto/query/parse-ipv4function)
- [Adres URL](/azure/kusto/query/parseurlfunction)
- [Zapytanie URL](/azure/kusto/query/parseurlqueryfunction)
- [Ścieżka pliku](/azure/kusto/query/parsepathfunction)
- [Agent użytkownika](/azure/kusto/query/parse-useragentfunction)
- [Ciąg wersji](/azure/kusto/query/parse-versionfunction)

Poniższe przykładowe zapytanie analizuje pole _Właściwości_ tabeli _Azure_ , która jest strukturą w formacie JSON. Zapisuje wyniki do właściwości dynamicznej o nazwie _parsedProp_, która zawiera pojedynczą wartość nazwaną w kodzie JSON. Te wartości służą do filtrowania i podsumowywania wyników zapytania.

```Kusto
AzureActivity
| extend parsedProp = parse_json(Properties) 
| where parsedProp.isComplianceCheck == "True" 
| summarize count() by ResourceGroup, tostring(parsedProp.tags.businessowner)
```

Te funkcje analizy mogą być intensywnym procesorem, dlatego powinny być używane tylko wtedy, gdy zapytanie używa wielu właściwości z sformatowanych danych. W przeciwnym razie proste przetwarzanie zgodne ze wzorcem będzie szybsze.

Poniższy przykład przedstawia podział typu uwierzytelniania wstępnego TGT kontrolera domeny. Typ istnieje tylko w polu EventData, który jest ciągiem XML, ale żadne inne dane z tego pola nie są zbędne. W takim przypadku [Analiza](/azure/kusto/query/parseoperator) służy do wybierania wymaganego elementu danych.

```Kusto
SecurityEvent
| where EventID == 4768
| parse EventData with * 'PreAuthType">' PreAuthType '</Data>' * 
| summarize count() by PreAuthType
```

## <a name="use-function-to-simulate-a-table"></a>Używanie funkcji do symulowania tabeli
Może istnieć wiele zapytań, które wykonują takie samo analizowanie określonej tabeli. W takim przypadku należy utworzyć funkcję, która zwraca dane przeanalizowane, zamiast [replikować](../logs/functions.md) logikę analizy w każdym zapytaniu. Następnie można użyć aliasu funkcji zamiast oryginalnej tabeli w innych zapytaniach.

Zapoznaj się z rozdzielonym przykładem dziennika niestandardowego z rozdzieloną przecinkami. Aby użyć przeanalizowanych danych w wielu zapytaniach, Utwórz funkcję przy użyciu następującego zapytania i Zapisz ją z aliasem _MyCustomCSVLog_.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields = split(RawData, ',')
| extend DateTime  = tostring(CSVFields[0])
| extend Code      = toint(CSVFields[1]) 
| extend Status    = tostring(CSVFields[2]) 
| extend Message   = tostring(CSVFields[3]) 
```

Teraz można użyć aliasu _MyCustomCSVLog_ zamiast rzeczywistej nazwy tabeli w zapytaniach, takich jak następujące.

```Kusto
MyCustomCSVLog
| summarize count() by Status,Code
```


## <a name="next-steps"></a>Następne kroki
* Informacje na temat [zapytań dzienników](./log-query-overview.md) w celu analizowania danych zebranych ze źródeł danych i rozwiązań.