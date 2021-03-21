---
title: Metryki Azure Relay w Azure Monitor | Microsoft Docs
description: Ten artykuł zawiera informacje dotyczące monitorowania stanu Azure Relay za pomocą Azure Monitor.
services: service-bus-relay
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 7f91ac0aec46d0fc24901585156dceabb18640c1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100586826"
---
# <a name="azure-relay-metrics-in-azure-monitor"></a>Metryki Azure Relay w Azure Monitor 
Metryki Azure Relay umożliwiają udostępnienie stanu zasobów w ramach subskrypcji platformy Azure. Dzięki bogatemu zestawowi danych metryk można ocenić ogólną kondycję zasobów przekazywania, nie tylko na poziomie przestrzeni nazw, ale również na poziomie jednostki. Te dane statystyczne mogą być ważne, ponieważ ułatwiają monitorowanie stanu Azure Relay. Metryki mogą również pomóc w rozwiązywaniu problemów dotyczących głównych przyczyn, bez konieczności kontaktowania się z pomocą techniczną platformy Azure.

Azure Monitor zapewnia ujednolicone interfejsy użytkownika do monitorowania różnych usług platformy Azure. Aby uzyskać więcej informacji, zobacz [monitorowanie w Microsoft Azure](../azure-monitor/overview.md) i [pobieranie metryk Azure monitor z przykładem platformy .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) w witrynie GitHub.

> [!IMPORTANT]
> Ten artykuł ma zastosowanie tylko do Połączenia hybrydowe funkcji Azure Relay, a nie do WCF Relay. 

## <a name="access-metrics"></a>Metryki dostępu

Azure Monitor zapewnia wiele sposobów uzyskiwania dostępu do metryk. Możesz uzyskać dostęp do metryk przy użyciu [Azure Portal](https://portal.azure.com)lub użyć interfejsów API Azure monitor (REST i .NET) oraz rozwiązań do analizy, takich jak pakiet Operations Management Suite i Event Hubs. Aby uzyskać więcej informacji, zobacz [monitorowanie danych zebranych przez Azure monitor](../azure-monitor/data-platform.md).

Metryki są domyślnie włączone i można uzyskać dostęp do najnowszych danych z ostatnich 30 dni. Jeśli chcesz zachować dane przez dłuższy czas, możesz zarchiwizować dane metryk na koncie usługi Azure Storage. Ta konfiguracja jest skonfigurowana w [ustawieniach diagnostycznych](../azure-monitor/essentials/diagnostic-settings.md) w Azure monitor.

## <a name="access-metrics-in-the-portal"></a>Dostęp do metryk w portalu

Można monitorować metryki w czasie w [Azure Portal](https://portal.azure.com). Poniższy przykład pokazuje, jak wyświetlić pomyślne żądania i żądania przychodzące na poziomie konta:

![Na stronie zatytułowanej "Monitor-Metrics (wersja zapoznawcza)" przedstawiono wykres liniowy użycia pamięci w ciągu ostatnich 30 dni.][1]

Możesz również uzyskać dostęp do metryk bezpośrednio za pośrednictwem przestrzeni nazw. Aby to zrobić, wybierz przestrzeń nazw, a następnie kliknij pozycję **metryki**. 

W przypadku metryk obsługujących wymiary należy filtrować według żądanej wartości wymiaru.

## <a name="billing"></a>Rozliczenia

Korzystanie z metryk w Azure Monitor jest obecnie bezpłatne w wersji zapoznawczej. Jeśli jednak korzystasz z dodatkowych rozwiązań, które pozyskają dane metryk, możesz obciążyć te rozwiązania. Na przykład w przypadku archiwizowania danych metryk na koncie usługi Azure Storage są naliczane opłaty za usługę Azure Storage. Opłaty są naliczane również za pomocą dzienników Azure Monitor, jeśli przesyłasz strumieniowo dane metryk do dzienników Azure Monitor w celu przeprowadzenia zaawansowanej analizy.

Poniższe metryki zapewniają przegląd kondycji usługi. 

> [!NOTE]
> Gdy są one przenoszone pod inną nazwę, jest przestarzałe kilka metryk. Może to wymagać aktualizacji odwołań. Metryki oznaczone słowem kluczowym "przestarzałe" nie będą obsługiwane do przodu.

Wszystkie wartości metryk są wysyłane do Azure Monitor co minutę. Stopień szczegółowości czasu określa przedział czasu, w którym są prezentowane wartości metryk. Obsługiwany przedział czasu dla wszystkich metryk Azure Relay wynosi 1 minutę.

## <a name="connection-metrics"></a>Metryki połączeń

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| ListenerConnections-Success  | Liczba pomyślnych połączeń odbiornika wykonanych w celu Azure Relay w określonym przedziale czasu. <br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Wymiar: EntityName|
|ListenerConnections-ClientError |Liczba błędów klienta w połączeniach odbiornika w określonym przedziale czasu.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Wymiar: EntityName|
|ListenerConnections-ServerError |Liczba błędów serwera w połączeniach odbiornika w określonym przedziale czasu.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Wymiar: EntityName|
|SenderConnections-Success |Liczba pomyślnych połączeń nadawcy wykonanych w określonym przedziale czasu.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Wymiar: EntityName|
|SenderConnections-ClientError |Liczba błędów klienta w połączeniach nadawcy w określonym przedziale czasu.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Wymiar: EntityName|
|SenderConnections-ServerError |Liczba błędów serwera w połączeniach nadawcy w określonym przedziale czasu.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Wymiar: EntityName|
|ListenerConnections-TotalRequests |Całkowita liczba połączeń odbiornika w określonym przedziale czasu.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Wymiar: EntityName|
|SenderConnections-TotalRequests |Żądania połączenia wykonywane przez nadawców w określonym przedziale czasu.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Wymiar: EntityName|
|Aktywne połączenia |Liczba aktywnych połączeń. Ta wartość jest wartością punktu w czasie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Wymiar: EntityName|
|ActiveListeners |Liczba aktywnych odbiorników. Ta wartość jest wartością punktu w czasie.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Wymiar: EntityName|
|ListenerDisconnects |Liczba odłączonych odbiorników w określonym przedziale czasu.<br/><br/> Jednostka: bajty <br/> Typ agregacji: łącznie <br/> Wymiar: EntityName|
|SenderDisconnects |Liczba odłączonych nadawców w określonym przedziale czasu.<br/><br/> Jednostka: bajty <br/> Typ agregacji: łącznie <br/> Wymiar: EntityName|

## <a name="memory-usage-metrics"></a>Metryki użycia pamięci

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|BytesTransferred |Liczba bajtów transferowanych w określonym przedziale czasu.<br/><br/> Jednostka: bajty <br/> Typ agregacji: łącznie <br/> Wymiar: EntityName|

## <a name="metrics-dimensions"></a>Wymiary metryk

Azure Relay obsługuje następujące wymiary dla metryk w Azure Monitor. Dodawanie wymiarów do metryk jest opcjonalne. Jeśli nie dodasz wymiarów, metryki są określane na poziomie przestrzeni nazw. 

|Nazwa wymiaru|Opis|
| ------------------- | ----------------- |
|EntityName| Azure Relay obsługuje jednostki obsługi komunikatów w przestrzeni nazw.|

## <a name="next-steps"></a>Następne kroki

Zobacz [Omówienie monitorowania platformy Azure](../azure-monitor/overview.md).

[1]: ./media/relay-metrics-azure-monitor/relay-monitor1.png
