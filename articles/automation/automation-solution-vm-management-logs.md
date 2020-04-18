---
title: Jak wysyłać zapytania do dzienników z rozwiązania Start/Stop VMs
description: W tym artykule opisano sposób wykonywania zapytań o dane dziennika wygenerowane przez rozwiązanie Start/Stop VMs z usługi Azure Monitor.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 472f3762ca18f71ba95053576daf025d8477fee9
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604722"
---
# <a name="how-to-query-logs-from-startstop-vms-solution"></a>Jak wysyłać zapytania do dzienników z rozwiązania Start/Stop VMs

Usługa Azure Automation przekazuje dwa typy rekordów do połączonego obszaru roboczego usługi Log Analytics: dzienniki zadań i strumienie zadań. Te dane są dostępne dla [kwerendy](../azure-monitor/log-query/log-query-overview.md) w usłudze Azure Monitor.

## <a name="job-logs"></a>Dzienniki zadań

|Właściwość | Opis|
|----------|----------|
|Obiekt wywołujący |  Użytkownik, który zainicjował operację. Możliwe wartości to adres e-mail lub system w przypadku zaplanowanych zadań.|
|Kategoria | Klasyfikacja typu danych. W przypadku usługi Automation wartością jest JobLogs.|
|CorrelationId | Identyfikator GUID, który jest identyfikatorem korelacji zadania runbook.|
|JobId | Identyfikator GUID, który jest identyfikatorem zadania runbook.|
|operationName | Określa typ operacji wykonywanej na platformie Azure. W przypadku automatyzacji wartością jest Zadanie.|
|resourceId | Określa typ zasobu na platformie Azure. W przypadku usługi Automation wartością jest konto usługi Automation skojarzone z elementem Runbook.|
|ResourceGroup | Określa nazwę grupy zasobów zadania elementu Runbook.|
|ResourceProvider | Określa nazwę usługi platformy Azure, która zapewnia zasoby do wdrożenia i zarządzania. W przypadku usługi Automation wartością jest Azure Automation.|
|ResourceType | Określa typ zasobu na platformie Azure. W przypadku usługi Automation wartością jest konto usługi Automation skojarzone z elementem Runbook.|
|resultType | Stan zadania elementu Runbook. Możliwe wartości:<br>— Uruchomione<br>— Zatrzymane<br>— Wstrzymane<br>— Nie powiodło się<br>— Powiodło się|
|resultDescription | Opisuje stan wyniku zadania elementu Runbook. Możliwe wartości:<br>— Zadanie jest uruchomione<br>— Zadanie nie powiodło się<br>— Zadanie zostało ukończone|
|RunbookName | Określa nazwę elementu Runbook.|
|SourceSystem | Określa system źródłowy dla przesłanych danych. W przypadku automatyzacji wartością jest OpsManager|
|StreamType | Określa typ zdarzenia. Możliwe wartości:<br>— Pełne<br>— Dane wyjściowe<br>— Błąd<br>— Ostrzeżenie|
|SubscriptionId | Określa identyfikator subskrypcji zadania.
|Time | Data i godzina dla wykonania zadania elementu Runbook.|

## <a name="job-streams"></a>Strumienie zadania

|Właściwość | Opis|
|----------|----------|
|Obiekt wywołujący |  Użytkownik, który zainicjował operację. Możliwe wartości to adres e-mail lub system w przypadku zaplanowanych zadań.|
|Kategoria | Klasyfikacja typu danych. W przypadku usługi Automation wartością jest JobStreams.|
|JobId | Identyfikator GUID, który jest identyfikatorem zadania runbook.|
|operationName | Określa typ operacji wykonywanej na platformie Azure. W przypadku automatyzacji wartością jest Zadanie.|
|ResourceGroup | Określa nazwę grupy zasobów zadania elementu Runbook.|
|resourceId | Określa identyfikator zasobu na platformie Azure. W przypadku usługi Automation wartością jest konto usługi Automation skojarzone z elementem Runbook.|
|ResourceProvider | Określa nazwę usługi platformy Azure, która zapewnia zasoby do wdrożenia i zarządzania. W przypadku usługi Automation wartością jest Azure Automation.|
|ResourceType | Określa typ zasobu na platformie Azure. W przypadku usługi Automation wartością jest konto usługi Automation skojarzone z elementem Runbook.|
|resultType | Wynik zadania elementu Runbook w czasie wygenerowania zdarzenia. Możliwa wartość to:<br>— W toku|
|resultDescription | Obejmuje strumień wyjściowy z elementu Runbook.|
|RunbookName | Nazwa elementu Runbook.|
|SourceSystem | Określa system źródłowy dla przesłanych danych. W przypadku automatyzacji wartością jest OpsManager.|
|StreamType | Typ strumienia zadania. Możliwe wartości:<br>- Postępy<br>— Dane wyjściowe<br>— Ostrzeżenie<br>— Błąd<br>— Debugowanie<br>— Pełne|
|Time | Data i godzina dla wykonania zadania elementu Runbook.|

Podczas wyszukiwania dzienników, które zwraca rekordy kategorii **JobLogs** lub **JobStreams**, można wybrać **JobLogs** lub **JobStreams** widoku, który wyświetla zestaw kafelków podsumowujących aktualizacje zwrócone przez wyszukiwanie.

## <a name="sample-log-searches"></a>Przykładowe wyszukiwania dzienników

Poniższa tabela zawiera przykładowe wyszukiwania dzienników dla rekordów dzienników zbieranych przez to rozwiązanie.

|Zapytanie | Opis|
|----------|----------|
|Znajdowanie zadań dla ScheduledStartStop_Parent y śmięty, które zakończyły się pomyślnie | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Znajdowanie zadań dla ScheduledStartStop_Parent y śmięty, które nie zostały pomyślnie ukończone | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "ScheduledStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" )  <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Znajdowanie zadań dla SequencedStartStop_Parent y śmięty, które zakończyły się pomyślnie | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Completed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|
|Znajdowanie zadań dla SequencedStartStop_Parent eksletów, które nie zostały pomyślnie ukończone | <code>search Category == "JobLogs" <br>&#124;  where ( RunbookName_s == "SequencedStartStop_Parent" ) <br>&#124;  where ( ResultType == "Failed" ) <br>&#124;  summarize AggregatedValue = count() by ResultType, bin(TimeGenerated, 1h) <br>&#124;  sort by TimeGenerated desc</code>|

## <a name="next-steps"></a>Następne kroki

**Maszyny wirtualne start/stop podczas** rozwiązania poza godzinami pracy nie zawiera wstępnie zdefiniowanego zestawu alertów. Przejrzyj [tworzenie alertów dziennika](../azure-monitor/platform/alerts-log.md) za pomocą usługi Azure Monitor, aby dowiedzieć się, jak tworzyć alerty nieudane zadania do obsługi devops lub procesów operacyjnych i procedur.