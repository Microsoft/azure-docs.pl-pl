---
title: Jak wykonywać zapytania dotyczące dzienników z Start/Stop VMs during off-hours
description: W tym artykule opisano sposób wykonywania zapytań dotyczących danych dzienników generowanych przez Start/Stop VMs during off-hours rozwiązanie z Azure Monitor.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 472baa3f4b3cbb970a8f365ccc94929ad565c421
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864236"
---
# <a name="how-to-query-logs-from-startstop-vms-during-off-hours"></a>Jak wykonywać zapytania dotyczące dzienników z Start/Stop VMs during off-hours

Azure Automation przekazuje dwa typy rekordów do połączonego Log Analytics obszaru roboczego: dzienniki zadań i strumienie zadań. Ten artykuł przegląda dane dostępne dla [zapytania](../azure-monitor/log-query/log-query-overview.md) w Azure monitor.

## <a name="job-logs"></a>Dzienniki zadań

|Właściwość | Opis|
|----------|----------|
|Obiekt wywołujący |  Użytkownik, który zainicjował operację. Możliwe wartości to adres e-mail lub system w przypadku zaplanowanych zadań.|
|Kategoria | Klasyfikacja typu danych. W przypadku usługi Automation wartością jest JobLogs.|
|CorrelationId | GUID, który jest IDENTYFIKATORem korelacji zadania elementu Runbook.|
|JobId | GUID, który jest IDENTYFIKATORem zadania elementu Runbook.|
|operationName | Określa typ operacji wykonywanej na platformie Azure. W przypadku usługi Automation wartością jest zadanie.|
|resourceId | Określa typ zasobu na platformie Azure. W przypadku usługi Automation wartością jest konto usługi Automation skojarzone z elementem Runbook.|
|ResourceGroup | Określa nazwę grupy zasobów zadania elementu Runbook.|
|ResourceProvider | Określa nazwę usługi platformy Azure, która zapewnia zasoby do wdrożenia i zarządzania. W przypadku usługi Automation wartością jest Azure Automation.|
|ResourceType | Określa typ zasobu na platformie Azure. W przypadku usługi Automation wartością jest konto usługi Automation skojarzone z elementem Runbook.|
|resultType | Stan zadania elementu Runbook. Możliwe wartości:<br>— Uruchomione<br>— Zatrzymane<br>— Wstrzymane<br>— Nie powiodło się<br>— Powiodło się|
|resultDescription | Opisuje stan wyniku zadania elementu Runbook. Możliwe wartości:<br>— Zadanie jest uruchomione<br>— Zadanie nie powiodło się<br>— Zadanie zostało ukończone|
|RunbookName | Określa nazwę elementu Runbook.|
|SourceSystem | Określa system źródłowy dla przesłanych danych. W przypadku usługi Automation wartością jest OpsManager|
|StreamType | Określa typ zdarzenia. Możliwe wartości:<br>— Pełne<br>— Dane wyjściowe<br>— Błąd<br>— Ostrzeżenie|
|SubscriptionId | Określa identyfikator subskrypcji zadania.
|Time | Data i godzina dla wykonania zadania elementu Runbook.|

## <a name="job-streams"></a>Strumienie zadania

|Właściwość | Opis|
|----------|----------|
|Obiekt wywołujący |  Użytkownik, który zainicjował operację. Możliwe wartości to adres e-mail lub system w przypadku zaplanowanych zadań.|
|Kategoria | Klasyfikacja typu danych. W przypadku usługi Automation wartością jest JobStreams.|
|JobId | GUID, który jest IDENTYFIKATORem zadania elementu Runbook.|
|operationName | Określa typ operacji wykonywanej na platformie Azure. W przypadku usługi Automation wartością jest zadanie.|
|ResourceGroup | Określa nazwę grupy zasobów zadania elementu Runbook.|
|resourceId | Określa identyfikator zasobu na platformie Azure. W przypadku usługi Automation wartością jest konto usługi Automation skojarzone z elementem Runbook.|
|ResourceProvider | Określa nazwę usługi platformy Azure, która zapewnia zasoby do wdrożenia i zarządzania. W przypadku usługi Automation wartością jest Azure Automation.|
|ResourceType | Określa typ zasobu na platformie Azure. W przypadku usługi Automation wartością jest konto usługi Automation skojarzone z elementem Runbook.|
|resultType | Wynik zadania elementu Runbook w czasie wygenerowania zdarzenia. Możliwe wartości to:<br>— W toku|
|resultDescription | Obejmuje strumień wyjściowy z elementu Runbook.|
|RunbookName | Nazwa elementu Runbook.|
|SourceSystem | Określa system źródłowy dla przesłanych danych. W przypadku usługi Automation wartością jest OpsManager.|
|StreamType | Typ strumienia zadania. Możliwe wartości:<br>— Postęp<br>— Dane wyjściowe<br>— Ostrzeżenie<br>— Błąd<br>— Debugowanie<br>— Pełne|
|Time | Data i godzina dla wykonania zadania elementu Runbook.|

Gdy wykonujesz każde wyszukiwanie w dzienniku, które zwraca rekordy kategorii **JobLogs** lub **JobStreams**, możesz wybrać widok **JobLogs** lub **JobStreams** , który wyświetla zestaw kafelków podsumowujących aktualizacje zwrócone przez wyszukiwanie.

## <a name="sample-log-searches"></a>Przykładowe wyszukiwania dzienników

Poniższa tabela zawiera przykładowe wyszukiwania dzienników dla rekordów dzienników zbieranych przez to rozwiązanie.

|Zapytanie | Opis|
|----------|----------|
|Znajdź zadania dla ScheduledStartStop_Parent elementu Runbook, które zakończyły się pomyślnie | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Znajdź zadania dla ScheduledStartStop_Parent elementu Runbook, które nie zostały pomyślnie zakończone | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Znajdź zadania dla SequencedStartStop_Parent elementu Runbook, które zakończyły się pomyślnie | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Znajdź zadania dla SequencedStartStop_Parent elementu Runbook, które nie zostały pomyślnie zakończone | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="next-steps"></a>Następne kroki

Rozwiązanie **Start/Stop VMS during off-hours** nie obejmuje wstępnie zdefiniowanego zestawu alertów. Zapoznaj się z tematem [tworzenie alertów dziennika](../azure-monitor/platform/alerts-log.md) przy użyciu Azure monitor, aby dowiedzieć się, jak utworzyć alerty dotyczące nieudanych zadań i procedur.