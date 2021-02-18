---
title: Czas pozyskiwania danych dziennika w Azure Monitor | Microsoft Docs
description: Wyjaśnia różne czynniki wpływające na opóźnienie w zbieraniu danych dzienników w Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/18/2019
ms.openlocfilehash: 17d89414a762dd6bf68176b5044787179eb80250
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100614070"
---
# <a name="log-data-ingestion-time-in-azure-monitor"></a>Czas pozyskiwania danych dziennika w usłudze Azure Monitor
Azure Monitor to usługa danych o dużej skali, która umożliwia tysiącom klientów wysyłanie terabajtów danych co miesiąc w coraz większej tempie. Często zadawane pytania dotyczące czasu potrzebnego do uzyskania danych dziennika stają się dostępne po ich zebraniu. W tym artykule wyjaśniono różne czynniki wpływające na to opóźnienie.

## <a name="typical-latency"></a>Typowe opóźnienia
Opóźnienie dotyczy czasu, w którym dane są tworzone w monitorowanym systemie, oraz czasu, który jest dostępny do analizy w Azure Monitor. Typowy czas oczekiwania na pozyskiwanie danych dziennika wynosi od 2 do 5 minut. Określone opóźnienie dla konkretnych danych będzie się różnić w zależności od różnych czynników opisanych poniżej.


## <a name="factors-affecting-latency"></a>Czynniki wpływające na opóźnienie
Łączny czas pozyskiwania dla określonego zestawu danych można podzielić na następujące obszary wysokiego poziomu. 

- Czas agenta — czas na odnalezienie zdarzenia, zebranie go, a następnie wysłanie go do Azure Monitor punktu pozyskiwania jako rekordu dziennika. W większości przypadków ten proces jest obsługiwany przez agenta.
- Czas potoku — czas przetwarzania rekordu dziennika przez potok pozyskiwania. Obejmuje to analizowanie właściwości zdarzenia i potencjalnie Dodawanie informacji obliczeniowych.
- Czas indeksowania — czas poświęcony na pozyskiwanie rekordu dziennika do Azure Monitor magazynu danych Big Data.

Szczegółowe informacje o różnych opóźnieniach wprowadzonych w tym procesie zostały opisane poniżej.

### <a name="agent-collection-latency"></a>Opóźnienie zbierania agenta
Agenci i rozwiązania do zarządzania wykorzystują różne strategie do zbierania danych z maszyny wirtualnej, co może mieć wpływ na opóźnienia. Poniżej wymieniono kilka konkretnych przykładów:

- Zdarzenia systemu Windows, zdarzenia dziennika systemowego i metryki wydajności są zbierane natychmiast. Liczniki wydajności systemu Linux są sondowane w 30-sekundowych odstępach czasu.
- Dzienniki usług IIS i dzienniki niestandardowe są zbierane po zmianie sygnatury czasowej. W przypadku dzienników usług IIS ma to wpływ na [harmonogram przerzucania skonfigurowany w usługach IIS](../agents/data-sources-iis-logs.md). 
- Active Directory rozwiązanie replikacji wykonuje swoją ocenę co pięć dni, podczas gdy rozwiązanie Active Directory Assessment wykonuje cotygodniową ocenę infrastruktury Active Directory. Agent będzie zbierać te dzienniki tylko wtedy, gdy ocena zostanie zakończona.

### <a name="agent-upload-frequency"></a>Częstotliwość przekazywania agenta
Aby zapewnić, że Agent Log Analytics jest lekki, Agent buforuje dzienniki i okresowo przekazuje je do Azure Monitor. Częstotliwość przekazywania różni się od 30 sekund do 2 minut w zależności od typu danych. Większość danych jest przekazywanych w ciągu 1 minuty. Warunki dotyczące sieci mogą mieć negatywny wpływ na opóźnienie tych danych w celu osiągnięcia Azure Monitor punktu pozyskiwania.

### <a name="azure-activity-logs-resource-logs-and-metrics"></a>Dzienniki aktywności platformy Azure, dzienniki zasobów i metryki
Dane platformy Azure są dodawane do Log Analytics punktu pozyskiwania na potrzeby przetwarzania:

- Dane z dzienników zasobów trwają 2-15 minut, w zależności od usługi platformy Azure. Zobacz [poniższe zapytanie](#checking-ingestion-time) , aby sprawdzić to opóźnienie w danym środowisku
- Metryki platformy Azure mogą być wysyłane do punktu pozyskiwania Log Analytics w ciągu 3 minut.
- Dane dziennika aktywności będą wysyłane o około 10-15 minut do punktu pozyskiwania Log Analytics.

Po udostępnieniu w punkcie pozyskiwania danych do wykonywania zapytań zajmują się dodatkowe 2-5 minut.

### <a name="management-solutions-collection"></a>Kolekcja rozwiązań do zarządzania
Niektóre rozwiązania nie zbierają danych od agenta i mogą korzystać z metody kolekcji, która wprowadza dodatkowe opóźnienia. Niektóre rozwiązania zbierają dane w regularnych odstępach czasu bez próby zbierania danych w czasie niemal rzeczywistym. Określone przykłady obejmują następujące elementy:

- Microsoft 365 rozwiązanie sonduje dzienniki aktywności przy użyciu interfejsu API działania zarządzania, który obecnie nie zapewnia żadnych gwarancji opóźnienia w czasie niemal rzeczywistym.
- Rozwiązania Windows Analytics (Update Compliance na przykład) dane są zbierane przez rozwiązanie z częstotliwością dzienną.

Zapoznaj się z dokumentacją każdego rozwiązania, aby określić jego częstotliwość zbierania danych.

### <a name="pipeline-process-time"></a>Potok — czas procesu
Po pobraniu rekordów dziennika do potoku Azure Monitor (zgodnie z definicją we właściwości [_TimeReceived](../platform/log-standard-columns.md#_timereceived) ) są one zapisywane w magazynie tymczasowym w celu zapewnienia izolacji dzierżawy i upewnienia się, że dane nie zostaną utracone. Ten proces zazwyczaj dodaje 5-15 sekund. Niektóre rozwiązania do zarządzania implementują algorytmy cięższe w celu agregowania danych i uzyskiwania szczegółowych informacji, jak dane przesyłane strumieniowo. Na przykład monitorowanie wydajności sieci agreguje dane przychodzące przez 3-minutowy interwały, co skutecznie dodaje 3 minuty opóźnienia. Innym procesem, który dodaje opóźnienie, jest proces obsługujący dzienniki niestandardowe. W niektórych przypadkach ten proces może dodawać kilka minut opóźnienia do dzienników zbieranych z plików przez agenta.

### <a name="new-custom-data-types-provisioning"></a>Nowe niestandardowe typy danych — Inicjowanie obsługi
Po utworzeniu nowego typu danych niestandardowych z [dziennika niestandardowego](../agents/data-sources-custom-logs.md) lub [interfejsu API modułu zbierającego dane](../logs/data-collector-api.md)system tworzy dedykowany kontener magazynu. Jest to jednorazowe obciążenie, które występuje tylko przy pierwszym pojawieniu się tego typu danych.

### <a name="surge-protection"></a>Ochrona przed przepięciem
Najważniejszym priorytetem Azure Monitor jest upewnienie się, że żadne dane klienta nie zostaną utracone, dlatego system ma wbudowaną ochronę przed gwałtownym przepięciem danych. Obejmuje to bufory, aby zapewnić, że nawet pod obciążeniem ogromną system będzie działać. W przypadku normalnego obciążenia te kontrolki dodają mniej niż minutę, ale w ekstremalnych warunkach i błędach, które mogą zwiększyć znaczący czas, przy jednoczesnym zapewnieniu bezpieczeństwa danych.

### <a name="indexing-time"></a>Czas indeksowania
Istnieje wbudowana równowaga dla każdej platformy danych Big Data między udostępnianiem analiz i zaawansowanych możliwości wyszukiwania, a nie w celu zapewnienia natychmiastowego dostępu do danych. Azure Monitor pozwala uruchamiać zaawansowane zapytania dotyczące miliardów rekordów i uzyskiwać wyniki w ciągu kilku sekund. Jest to możliwe, ponieważ infrastruktura przekształca dane znacznie podczas jego pozyskiwania i zapisuje je w unikatowych, kompaktowych strukturach. System buforuje dane do momentu uzyskania wystarczającej ilości pamięci do utworzenia tych struktur. Należy to zrobić, aby rekord dziennika pojawił się w wynikach wyszukiwania.

Ten proces trwa około 5 minut, gdy istnieje niska ilość danych, ale mniej czasu przy wyższych stawkach danych. To wydaje się nielogiczna, ale ten proces umożliwia optymalizację opóźnienia dla obciążeń produkcyjnych o dużej pojemności.



## <a name="checking-ingestion-time"></a>Sprawdzanie czasu pozyskiwania
Czas pozyskiwania może różnić się w zależności od różnych zasobów w różnych warunkach. Zapytania dzienników służą do identyfikowania określonego zachowania środowiska. W poniższej tabeli opisano, jak można określić różne godziny dla rekordu, które są tworzone i wysyłane do Azure Monitor.

| Krok | Właściwość lub funkcja | Komentarze |
|:---|:---|:---|
| Rekord utworzony w źródle danych | [TimeGenerated](../platform/log-standard-columns.md#timegenerated-and-timestamp) <br>Jeśli źródło danych nie ustawi tej wartości, zostanie ona ustawiona na ten sam czas co _TimeReceived. |
| Rekord otrzymany przez Azure Monitor punkt końcowy pozyskiwania | [_TimeReceived](../platform/log-standard-columns.md#_timereceived) | |
| Rekord przechowywany w obszarze roboczym i dostępny dla zapytań | [ingestion_time ()](/azure/kusto/query/ingestiontimefunction) | |

### <a name="ingestion-latency-delays"></a>Opóźnienia w czasie przyjmowania
Można mierzyć opóźnienie określonego rekordu, porównując wynik funkcji [ingestion_time ()](/azure/kusto/query/ingestiontimefunction) z właściwością _TimeGenerated_ . Te dane mogą być używane z różnymi agregacjami, aby dowiedzieć się, jak działa opóźnienie pozyskiwania. Zapoznaj się z informacjami o percentylu czasu pozyskiwania, aby uzyskać szczegółowe informacje dotyczące dużej ilości danych. 

Na przykład następujące zapytanie pokazuje, które komputery mają największy czas pozyskiwania w ciągu poprzednich 8 godzin: 

``` Kusto
Heartbeat
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by Computer 
| top 20 by percentile_E2EIngestionLatency_95 desc
```

Poprzednie sprawdzenia percentylu są przydatne do znajdowania ogólnych trendów w czasie oczekiwania. Aby zidentyfikować krótkoterminowe gwałtowne opóźnienia, użycie wartości Maximum ( `max()` ) może być bardziej efektywne.

Jeśli chcesz przejść do szczegółów czasu pozyskiwania dla określonego komputera w danym okresie, użyj następującego zapytania, które również wizualizuje dane z ostatniego dnia na wykresie: 


``` Kusto
Heartbeat 
| where TimeGenerated > ago(24h) //and Computer == "ContosoWeb2-Linux"  
| extend E2EIngestionLatencyMin = todouble(datetime_diff("Second",ingestion_time(),TimeGenerated))/60 
| extend AgentLatencyMin = todouble(datetime_diff("Second",_TimeReceived,TimeGenerated))/60 
| summarize percentiles(E2EIngestionLatencyMin,50,95), percentiles(AgentLatencyMin,50,95) by bin(TimeGenerated,30m) 
| render timechart
```
 
Użyj następującego zapytania, aby wyświetlić czas pozyskiwania komputera według kraju/regionu, w którym się znajdują, w oparciu o adres IP: 

``` Kusto
Heartbeat 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95),percentiles(AgentLatency,50,95) by RemoteIPCountry 
```
 
Różne typy danych pochodzące od agenta mogą mieć inny czas opóźnienia pozyskiwania, dlatego poprzednie zapytania mogą być używane z innymi typami. Użyj następującego zapytania, aby zbadać czas pozyskiwania różnych usług platformy Azure: 

``` Kusto
AzureDiagnostics 
| where TimeGenerated > ago(8h) 
| extend E2EIngestionLatency = ingestion_time() - TimeGenerated 
| extend AgentLatency = _TimeReceived - TimeGenerated 
| summarize percentiles(E2EIngestionLatency,50,95), percentiles(AgentLatency,50,95) by ResourceProvider
```

### <a name="resources-that-stop-responding"></a>Zasoby, które przestają odpowiadać 
W niektórych przypadkach zasób może zatrzymać wysyłanie danych. Aby zrozumieć, czy zasób wysyła dane, zapoznaj się z najnowszym rekordem, który może być identyfikowany przez standardowe pole _TimeGenerated_ .  

Użyj tabeli _pulsu_ , aby sprawdzić dostępność maszyny wirtualnej, ponieważ puls jest wysyłany raz na minutę przez agenta. Użyj następującego zapytania, aby wyświetlić listę aktywnych komputerów, które ostatnio nie zgłosiły pulsu: 

``` Kusto
Heartbeat  
| where TimeGenerated > ago(1d) //show only VMs that were active in the last day 
| summarize NoHeartbeatPeriod = now() - max(TimeGenerated) by Computer  
| top 20 by NoHeartbeatPeriod desc 
```

## <a name="next-steps"></a>Następne kroki
* Przeczytaj [Umowa dotycząca poziomu usług (SLA)](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_1/) dla Azure monitor.