---
title: Przykłady zapytań w dzienniku Azure Monitor | Microsoft Docs
description: Przykłady zapytań dzienników w Azure Monitor przy użyciu języka zapytań Kusto.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/16/2020
ms.openlocfilehash: 736daa8a09a8f08721c7b7d9c20f012f274b384a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87045523"
---
# <a name="azure-monitor-log-query-examples"></a>Przykłady zapytań w dzienniku Azure Monitor
Ten artykuł zawiera różne przykłady [zapytań](log-query-overview.md) używających [języka zapytań Kusto](/azure/kusto/query/) do pobierania różnych typów danych dziennika z Azure monitor. Różne metody służą do konsolidowania i analizowania danych, dzięki czemu można używać tych przykładów do identyfikowania różnych strategii, których można użyć do własnych wymagań.  

Aby uzyskać szczegółowe informacje o różnych słowach kluczowych używanych w tych przykładach, zobacz [Dokumentacja języka Kusto](/azure/kusto/query/) . Zapoznaj się z lekcji, aby [utworzyć zapytania](get-started-queries.md) , jeśli jesteś nowym Azure monitor.

## <a name="events"></a>Zdarzenia

### <a name="search-application-level-events-described-as-cryptographic"></a>Wyszukaj zdarzenia na poziomie aplikacji opisane jako "kryptograficzne"
Ten przykład przeszukuje tabelę **Events** pod kątem rekordów, w których **EventLog** jest _Application_ i **RenderedDescription** zawiera dane _kryptograficzne_. Zawiera rekordy z ostatnich 24 godzin.

```Kusto
Event
| where EventLog == "Application" 
| where TimeGenerated > ago(24h) 
| where RenderedDescription contains "cryptographic"
```

### <a name="search-events-related-to-unmarshaling"></a>Wyszukaj zdarzenia związane z rozorganizowaniem
Wyszukaj w tabelach **zdarzenia** i **SecurityEvents** dla rekordów, które wymieniają informacje o _rozkierowaniu_.

```Kusto
search in (Event, SecurityEvent) "unmarshaling"
```

## <a name="heartbeat"></a>Puls

### <a name="chart-a-week-over-week-view-of-the-number-of-computers-sending-data"></a>Wykres przedstawiający tygodniowy widok liczby komputerów wysyłających dane

W poniższym przykładzie przedstawiono liczbę różnych komputerów, które wysyłają pulsy, co tydzień.

```Kusto
Heartbeat
| where TimeGenerated >= startofweek(ago(21d))
| summarize dcount(Computer) by endofweek(TimeGenerated) | render barchart kind=default
```

### <a name="find-stale-computers"></a>Znajdź stare komputery

W poniższym przykładzie znaleziono komputery, które były aktywne w ciągu ostatniego dnia, ale nie wysłano pulsów w ciągu ostatniej godziny.

```Kusto
Heartbeat
| where TimeGenerated > ago(1d)
| summarize LastHeartbeat = max(TimeGenerated) by Computer
| where isnotempty(Computer)
| where LastHeartbeat < ago(1h)
```

### <a name="get-the-latest-heartbeat-record-per-computer-ip"></a>Pobierz najnowszy rekord pulsu dla adresu IP komputera

Ten przykład zwraca ostatni rekord pulsu dla każdego adresu IP komputera.
```Kusto
Heartbeat
| summarize arg_max(TimeGenerated, *) by ComputerIP
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Dopasuj rekordy stanu chronionego przy użyciu rekordów pulsu

Ten przykład wyszukuje powiązane rekordy stanu ochrony i rekordy pulsu, dopasowane zarówno na komputerze, jak i w czasie.
Zwróć uwagę, że pole Time jest zaokrąglane do najbliższej minuty. Użyto obliczeń bin w czasie wykonywania, aby to zrobić: `round_time=bin(TimeGenerated, 1m)` .

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```

### <a name="server-availability-rate"></a>Szybkość dostępności serwera

Oblicz szybkość dostępności serwera na podstawie rekordów pulsu. Dostępność jest definiowana jako "co najmniej 1 puls na godzinę".
Tak więc, jeśli serwer był dostępny 98 z 100 godz., stawka dostępności to 98%.

```Kusto
let start_time=startofday(datetime("2018-03-01"));
let end_time=now();
Heartbeat
| where TimeGenerated > start_time and TimeGenerated < end_time
| summarize heartbeat_per_hour=count() by bin_at(TimeGenerated, 1h, start_time), Computer
| extend available_per_hour=iff(heartbeat_per_hour>0, true, false)
| summarize total_available_hours=countif(available_per_hour==true) by Computer 
| extend total_number_of_buckets=round((end_time-start_time)/1h)+1
| extend availability_rate=total_available_hours*100/total_number_of_buckets
```


## <a name="multiple-data-types"></a>Wiele typów danych

### <a name="chart-the-record-count-per-table"></a>Tworzenie wykresów według liczby rekordów na tabelę
Poniższy przykład zbiera wszystkie rekordy ze wszystkich tabel z ostatnich pięciu godzin i liczy liczbę rekordów w każdej tabeli. Wyniki są wyświetlane w timechart.

```Kusto
union withsource=sourceTable *
| where TimeGenerated > ago(5h) 
| summarize count() by bin(TimeGenerated,10m), sourceTable
| render timechart
```

### <a name="count-all-logs-collected-over-the-last-hour-by-type"></a>Liczba wszystkich dzienników zebranych w ciągu ostatniej godziny według typu
Poniższy przykład wyszukuje wszystkie zgłoszone w ciągu ostatniej godziny i zlicza rekordy każdej tabeli według **typu**. Wyniki są wyświetlane na wykresie słupkowym.

```Kusto
search *
| where TimeGenerated > ago(1h) 
| summarize CountOfRecords = count() by Type
| render barchart
```

## <a name="azurediagnostics"></a>AzureDiagnostics

### <a name="count-azure-diagnostics-records-per-category"></a>Liczba rekordów diagnostyki platformy Azure na kategorię
Ten przykład zlicza wszystkie rekordy diagnostyki Azure dla każdej unikatowej kategorii.

```Kusto
AzureDiagnostics 
| where TimeGenerated > ago(1d)
| summarize count() by Category
```

### <a name="get-a-random-record-for-each-unique-category"></a>Pobierz losowy rekord dla każdej unikatowej kategorii
Ten przykład pobiera pojedynczy losowy rekord diagnostyki platformy Azure dla każdej unikatowej kategorii.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize any(*) by Category
```

### <a name="get-the-latest-record-per-category"></a>Pobierz najnowszy rekord na kategorię
Ten przykład pobiera najnowszy rekord diagnostyki platformy Azure w każdej unikatowej kategorii.

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize arg_max(TimeGenerated, *) by Category
```

## <a name="network-monitoring"></a>Monitorowanie sieci

### <a name="computers-with-unhealthy-latency"></a>Komputery z opóźnieniem złej kondycji
Ten przykład tworzy listę komputerów z nieprawidłowym opóźnieniem.

```Kusto
NetworkMonitoring 
| where LatencyHealthState <> "Healthy" 
| where Computer != "" 
| distinct Computer
```

## <a name="performance"></a>Wydajność

### <a name="join-computer-perf-records-to-correlate-memory-and-cpu"></a>Dołącz rekordy wydajności komputera do skorelowania pamięci i procesora CPU
Ten przykład służy do skorelowania rekordów **wydajności** określonego komputera i tworzenia dwóch wykresów czasu, średniego procesora CPU i maksymalnej pamięci.

```Kusto
let StartTime = now()-5d;
let EndTime = now()-4d;
Perf
| where CounterName == "% Processor Time"  
| where TimeGenerated > StartTime and TimeGenerated < EndTime
| project TimeGenerated, Computer, cpu=CounterValue 
| join kind= inner (
   Perf
    | where CounterName == "% Used Memory"  
    | where TimeGenerated > StartTime and TimeGenerated < EndTime
    | project TimeGenerated , Computer, mem=CounterValue 
) on TimeGenerated, Computer
| summarize avgCpu=avg(cpu), maxMem=max(mem) by TimeGenerated bin=30m  
| render timechart
```

### <a name="perf-cpu-utilization-graph-per-computer"></a>Wykres wykorzystania procesora CPU wydajności na komputer
Ten przykład oblicza i wykresówuje użycie procesora CPU przez komputery, które zaczynają się od firmy _contoso_.

```Kusto
Perf
| where TimeGenerated > ago(4h)
| where Computer startswith "Contoso" 
| where CounterName == @"% Processor Time"
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 15m) 
| render timechart
```

## <a name="protection-status"></a>Stan ochrony

### <a name="computers-with-non-reporting-protection-status-duration"></a>Komputery z niezgodnym okresem ważności ochrony
Ten przykład zawiera listę komputerów, które mają stan ochrony _bez raportowania_  i czas trwania tego stanu.

```Kusto
ProtectionStatus
| where ProtectionStatus == "Not Reporting"
| summarize count(), startNotReporting = min(TimeGenerated), endNotReporting = max(TimeGenerated) by Computer, ProtectionStatusDetails
| join ProtectionStatus on Computer
| summarize lastReporting = max(TimeGenerated), startNotReporting = any(startNotReporting), endNotReporting = any(endNotReporting) by Computer
| extend durationNotReporting = endNotReporting - startNotReporting
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>Dopasuj rekordy stanu chronionego przy użyciu rekordów pulsu
Ten przykład wyszukuje powiązane rekordy stanu ochrony i rekordy pulsu dopasowane zarówno do komputera, jak i czasu.
Wartość pola czas jest zaokrąglana do najbliższej minuty przy użyciu **bin**.

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```


## <a name="security-records"></a>Rekordy zabezpieczeń

### <a name="count-security-events-by-activity-id"></a>Liczba zdarzeń zabezpieczeń według identyfikatora działania


Ten przykład opiera się na stałej strukturze kolumny **aktywności** : \<ID\> - \<Name\> .
Analizuje ona wartość **działania** w dwie nowe kolumny i zlicza wystąpienia każdego **activityIDu**.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| project Activity 
| parse Activity with activityID " - " activityDesc
| summarize count() by activityID
```

### <a name="count-security-events-related-to-permissions"></a>Liczba zdarzeń zabezpieczeń związanych z uprawnieniami
W tym przykładzie przedstawiono liczbę rekordów **securityEvent** , w których kolumna **Activity** zawiera _uprawnienia_całodzienne. Zapytanie dotyczy rekordów utworzonych w ciągu ostatnich 30 minut.

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m)
| summarize EventCount = countif(Activity has "Permissions")
```

### <a name="find-accounts-that-failed-to-log-in-from-computers-with-a-security-detection"></a>Znajdź konta, których nie udało się zalogować z komputerów z wykrywaniem zabezpieczeń
Ten przykład umożliwia znalezienie i liczenie kont, których logowanie nie powiodło się z komputerów, na których zidentyfikowano wykrywanie zabezpieczeń.

```Kusto
let detections = toscalar(SecurityDetection
| summarize makeset(Computer));
SecurityEvent
| where Computer in (detections) and EventID == 4624
| summarize count() by Account
```

### <a name="is-my-security-data-available"></a>Czy moje dane zabezpieczeń są dostępne?
Uruchamianie eksploracji danych często rozpoczyna się od sprawdzenia dostępności danych. Ten przykład pokazuje liczbę rekordów **SecurityEvent** w ciągu ostatnich 30 minut.

```Kusto
SecurityEvent 
| where TimeGenerated  > ago(30m) 
| count
```

### <a name="parse-activity-name-and-id"></a>Nazwa i identyfikator działania analizy
Dwa poniższe przykłady polegają na stałej strukturze kolumny **aktywności** : \<ID\> - \<Name\> . W pierwszym przykładzie używa operatora **Parse** do przypisywania wartości do dwóch nowych kolumn: **ActivityId** i **activityDesc**.

```Kusto
SecurityEvent
| take 100
| project Activity 
| parse Activity with activityID " - " activityDesc
```

W tym przykładzie używa operatora **Split** do tworzenia tablicy oddzielnych wartości
```Kusto
SecurityEvent
| take 100
| project Activity 
| extend activityArr=split(Activity, " - ") 
| project Activity , activityArr, activityId=activityArr[0]
```

### <a name="explicit-credentials-processes"></a>Procesy jawnych poświadczeń
Poniższy przykład przedstawia wykres kołowy procesów, które używały jawnych poświadczeń w ostatnim tygodniu

```Kusto
SecurityEvent
| where TimeGenerated > ago(7d)
// filter by id 4648 ("A logon was attempted using explicit credentials")
| where EventID == 4648
| summarize count() by Process
| render piechart 
```

### <a name="top-running-processes"></a>Najczęstsze uruchomione procesy

Poniższy przykład pokazuje wiersz czasu aktywności dla pięciu najpopularniejszych procesów w ciągu ostatnich trzech dni.

```Kusto
// Find all processes that started in the last three days. ID 4688: A new process has been created.
let RunProcesses = 
    SecurityEvent
    | where TimeGenerated > ago(3d)
    | where EventID == "4688";
// Find the 5 processes that were run the most
let Top5Processes =
RunProcesses
| summarize count() by Process
| top 5 by count_;
// Create a time chart of these 5 processes - hour by hour
RunProcesses 
| where Process in (Top5Processes) 
| summarize count() by bin (TimeGenerated, 1h), Process
| render timechart
```


### <a name="find-repeating-failed-login-attempts-by-the-same-account-from-different-ips"></a>Znajdź powtarzające się nieudane próby logowania według tego samego konta z różnych adresów IP

Poniższy przykład wyszukuje nieudane próby zalogowania przy użyciu tego samego konta z więcej niż pięciu różnych adresów IP w ciągu ostatnich sześciu godzin.

```Kusto
SecurityEvent 
| where AccountType == "User" and EventID == 4625 and TimeGenerated > ago(6h) 
| summarize IPCount = dcount(IpAddress), makeset(IpAddress)  by Account
| where IPCount > 5
| sort by IPCount desc
```

### <a name="find-user-accounts-that-failed-to-log-in"></a>Znajdź konta użytkowników, których zalogowanie nie powiodło się 
W poniższym przykładzie zidentyfikowano konta użytkowników, których logowanie nie powiodło się więcej niż pięć razy w ciągu ostatniego dnia, a następnie podczas ostatniej próby logowania.

```Kusto
let timeframe = 1d;
SecurityEvent
| where TimeGenerated > ago(1d)
| where AccountType == 'User' and EventID == 4625 // 4625 - failed log in
| summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
| where failed_login_attempts > 5
| project-away Account1
```

Korzystając z funkcji **Join**, możesz **sprawdzić, czy** te same podejrzane konta były później mogły zostać pomyślnie zalogować.

```Kusto
let timeframe = 1d;
let suspicious_users = 
    SecurityEvent
    | where TimeGenerated > ago(timeframe)
    | where AccountType == 'User' and EventID == 4625 // 4625 - failed login
    | summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
    | where failed_login_attempts > 5
    | project-away Account1;
let suspicious_users_that_later_logged_in = 
    suspicious_users 
    | join kind=innerunique (
        SecurityEvent
        | where TimeGenerated > ago(timeframe)
        | where AccountType == 'User' and EventID == 4624 // 4624 - successful login,
        | summarize latest_successful_login=arg_max(TimeGenerated, Account) by Account
    ) on Account
    | extend was_login_after_failures = iif(latest_successful_login>latest_failed_login, 1, 0)
    | where was_login_after_failures == 1
;
suspicious_users_that_later_logged_in
```

## <a name="usage"></a>Użycie

`Usage`Typ danych może służyć do śledzenia ilości danych pozyskiwanych według rozwiązania lub typu danych. Istnieją inne techniki analizowania pozyskiwanych woluminów danych według [komputera](../platform/manage-cost-storage.md#data-volume-by-computer) lub [subskrypcji platformy Azure, grupy zasobów lub zasobu](../platform/manage-cost-storage.md#data-volume-by-azure-resource-resource-group-or-subscription).

#### <a name="data-volume-by-solution"></a>Ilość danych wg rozwiązania

Zapytanie używane do wyświetlania ilości danych do rozliczenia według rozwiązania w ciągu ostatniego miesiąca (z wyjątkiem ostatniego częściowego dnia) to:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution | render barchart
```

Należy zauważyć, że klauzula `where IsBillable = true` filtruje typy danych z niektórych rozwiązań, dla których nie jest naliczana opłata za pozyskiwanie.  Ponadto klauzula WITH `TimeGenerated` ma na celu upewnienie się, że obsługa zapytań w Azure Portal będzie wyglądać poza domyślnymi 24 godzinami. W przypadku użycia typu danych użycia i reprezentowania przedziałów `StartTime` `EndTime` czasu, dla których są wyświetlane wyniki. 

#### <a name="data-volume-by-type"></a>Ilość danych według typu

Możesz przejść do szczegółów, aby zobaczyć trendy dotyczące danych według typu danych:

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType | render barchart
```

Lub, aby wyświetlić tabelę według rozwiązania i typu w ciągu ostatniego miesiąca,

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by Solution, DataType
| sort by Solution asc, DataType asc
```

> [!NOTE]
> Niektóre pola typu danych użycia, ale nadal w schemacie, są przestarzałe i ich wartości nie będą już wypełnione. Są to **komputery** , a także pola związane z pozyskiwaniem (**TotalBatches**, **BatchesWithinSla**, **BatchesOutsideSla**, **BatchesCapped** i **AverageProcessingTimeMs**.

## <a name="updates"></a>Aktualizacje

### <a name="computers-still-missing-updates"></a>Komputery, na których nadal brakuje aktualizacji
Ten przykład przedstawia listę komputerów, na których brakuje co najmniej jednej aktualizacji krytycznej kilka dni temu i nadal nie ma aktualizacji.

```Kusto
let ComputersMissingUpdates3DaysAgo = Update
| where TimeGenerated between (ago(30d)..ago(1h))
| where Classification !has "Critical" and UpdateState =~ "Needed"
| summarize makeset(Computer);
Update
| where TimeGenerated > ago(1d)
| where Classification has "Critical" and UpdateState =~ "Needed"
| where Computer in (ComputersMissingUpdates3DaysAgo)
| summarize UniqueUpdatesCount = dcount(Product) by Computer, OSType
```


## <a name="next-steps"></a>Następne kroki

- Szczegółowe informacje na temat języka można znaleźć w [dokumentacji języka Kusto](/azure/kusto/query) .
- Zapoznaj się z [lekcjami dotyczącymi pisania zapytań dzienników w Azure monitor](get-started-queries.md).
