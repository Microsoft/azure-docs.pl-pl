---
title: Metryki w Azure Monitor — Event Hubs platformy Azure | Microsoft Docs
description: Ten artykuł zawiera informacje dotyczące monitorowania platformy Azure Event Hubs przy użyciu usługi Azure Monitoring
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 7ad570a41fd9dfff01e3a1da6b2d309a7a8464cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88931152"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Metryki usługi Azure Event Hubs na platformie Azure Monitor

Metryki Event Hubs zapewniają stan Event Hubs zasobów w ramach subskrypcji platformy Azure. Dzięki bogatemu zestawowi danych metryk można ocenić ogólną kondycję centrów zdarzeń nie tylko na poziomie przestrzeni nazw, ale również na poziomie jednostki. Te dane statystyczne mogą być ważne, ponieważ ułatwiają monitorowanie stanu centrów zdarzeń. Metryki mogą również pomóc w rozwiązywaniu problemów dotyczących głównych przyczyn, bez konieczności kontaktowania się z pomocą techniczną platformy Azure.

Azure Monitor zapewnia ujednolicone interfejsy użytkownika do monitorowania różnych usług platformy Azure. Aby uzyskać więcej informacji, zobacz [monitorowanie w Microsoft Azure](../azure-monitor/overview.md) i [pobieranie metryk Azure monitor z przykładem platformy .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) w witrynie GitHub.

## <a name="access-metrics"></a>Metryki dostępu

Azure Monitor zapewnia wiele sposobów uzyskiwania dostępu do metryk. Możesz uzyskać dostęp do metryk przy użyciu [Azure Portal](https://portal.azure.com)lub użyć interfejsów API Azure monitor (REST i .NET) oraz rozwiązań do analizy, takich jak Log Analytics i Event Hubs. Aby uzyskać więcej informacji, zobacz [monitorowanie danych zebranych przez Azure monitor](../azure-monitor/platform/data-platform.md).

Metryki są domyślnie włączone i można uzyskać dostęp do najnowszych danych z ostatnich 30 dni. Jeśli chcesz zachować dane przez dłuższy czas, możesz zarchiwizować dane metryk na koncie usługi Azure Storage. Ta konfiguracja jest skonfigurowana w [ustawieniach diagnostycznych](../azure-monitor/platform/diagnostic-settings.md) w Azure monitor.


## <a name="access-metrics-in-the-portal"></a>Dostęp do metryk w portalu

Można monitorować metryki w czasie w [Azure Portal](https://portal.azure.com). Poniższy przykład pokazuje, jak wyświetlić pomyślne żądania i żądania przychodzące na poziomie konta:

![Wyświetl metryki pomyślne][1]

Możesz również uzyskać dostęp do metryk bezpośrednio za pośrednictwem przestrzeni nazw. Aby to zrobić, wybierz przestrzeń nazw, a następnie kliknij pozycję **metryki**. Aby wyświetlić metryki odfiltrowane do zakresu centrum zdarzeń, wybierz centrum zdarzeń, a następnie kliknij pozycję **metryki**.

W przypadku metryk obsługujących wymiary należy filtrować według żądanej wartości wymiaru, jak pokazano w następującym przykładzie:

![Filtruj z wartością wymiaru][2]

## <a name="billing"></a>Rozliczenia

Korzystanie z metryk w Azure Monitor jest obecnie bezpłatne. Jeśli jednak korzystasz z dodatkowych rozwiązań, które pozyskają dane metryk, możesz obciążyć te rozwiązania. Na przykład w przypadku archiwizowania danych metryk na koncie usługi Azure Storage są naliczane opłaty za usługę Azure Storage. Opłaty są naliczane także przez platformę Azure, jeśli dane metryk są przesyłane strumieniowo do dzienników Azure Monitor w celu przeprowadzenia zaawansowanej analizy.

Poniższe metryki zapewniają przegląd kondycji usługi. 

> [!NOTE]
> Gdy są one przenoszone pod inną nazwę, jest przestarzałe kilka metryk. Może to wymagać aktualizacji odwołań. Metryki oznaczone słowem kluczowym "przestarzałe" nie będą obsługiwane do przodu.

Wszystkie wartości metryk są wysyłane do Azure Monitor co minutę. Stopień szczegółowości czasu określa przedział czasu, w którym są prezentowane wartości metryk. Obsługiwany przedział czasu dla wszystkich metryk Event Hubs wynosi 1 minutę.

## <a name="request-metrics"></a>Metryki żądań

Zlicza żądania operacji na danych i zarządzania.

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
| Żądania przychodzące  | Liczba żądań wysyłanych do usługi Azure Event Hubs w określonym przedziale czasu. <br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Wymiar: EntityName |
| Żądania pomyślne    | Liczba pomyślnych żądań skierowanych do usługi Azure Event Hubs w określonym przedziale czasu. <br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Wymiar: EntityName |
| Błędy serwera  | Liczba żądań, które nie zostały przetworzone z powodu błędu w usłudze Azure Event Hubs w określonym przedziale czasu. <br/><br/>Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Wymiar: EntityName |
|Błędy użytkownika |Liczba żądań, które nie zostały przetworzone z powodu błędów użytkowników w określonym przedziale czasu.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Wymiar: EntityName|
|Błędy przekroczenia limitu przydziału |Liczba żądań przekroczyła dostępny limit przydziału. Aby uzyskać więcej informacji na temat przydziałów Event Hubs, zobacz [ten artykuł](event-hubs-quotas.md) .<br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Wymiar: EntityName|

## <a name="throughput-metrics"></a>Metryki przepływności

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|Ograniczone żądania |liczba żądań, które zostały ograniczone, ponieważ przekroczono użycie jednostek przepływności.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Wymiar: EntityName|

## <a name="message-metrics"></a>Metryki komunikatów

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|Komunikaty przychodzące |Liczba zdarzeń lub komunikatów wysłanych do Event Hubs w określonym przedziale czasu.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Wymiar: EntityName|
|Komunikaty wychodzące |Liczba zdarzeń lub komunikatów pobranych z Event Hubs w określonym przedziale czasu.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Wymiar: EntityName|
|Przychodzące bajty |Liczba bajtów wysłanych do usługi Azure Event Hubs w określonym przedziale czasu.<br/><br/> Jednostka: bajty <br/> Typ agregacji: łącznie <br/> Wymiar: EntityName|
|Bajty wychodzące |Liczba bajtów pobranych z usługi Event Hubs platformy Azure w określonym przedziale czasu.<br/><br/> Jednostka: bajty <br/> Typ agregacji: łącznie <br/> Wymiar: EntityName|

## <a name="connection-metrics"></a>Metryki połączeń

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|Aktywne połączenia |Liczba aktywnych połączeń w przestrzeni nazw, jak również w jednostce.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Wymiar: EntityName|
|Otwarte połączenia |Liczba otwartych połączeń.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Wymiar: EntityName|
|Połączenia zamknięte |Liczba zamkniętych połączeń.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Wymiar: EntityName|

## <a name="event-hubs-capture-metrics"></a>Metryki przechwytywania Event Hubs

Po włączeniu funkcji przechwytywania dla centrów zdarzeń można monitorować metryki przechwytywania Event Hubs. Poniższe metryki opisują możliwości monitorowania z włączoną funkcją przechwytywania.

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|Lista prac przechwytywania |Liczba bajtów, które nie są jeszcze przechwytywane do wybranego miejsca docelowego.<br/><br/> Jednostka: bajty <br/> Typ agregacji: łącznie <br/> Wymiar: EntityName|
|Przechwycone komunikaty |Liczba komunikatów lub zdarzeń, które są przechwytywane do wybranego miejsca docelowego w określonym przedziale czasu.<br/><br/> Jednostka: liczba <br/> Typ agregacji: łącznie <br/> Wymiar: EntityName|
|Przechwycone bajty |Liczba bajtów przechwytywanych do wybranego miejsca docelowego w określonym przedziale czasu.<br/><br/> Jednostka: bajty <br/> Typ agregacji: łącznie <br/> Wymiar: EntityName|

## <a name="metrics-dimensions"></a>Wymiary metryk

Usługa Azure Event Hubs obsługuje następujące wymiary dla metryk w Azure Monitor. Dodawanie wymiarów do metryk jest opcjonalne. Jeśli nie dodasz wymiarów, metryki są określane na poziomie przestrzeni nazw. 

| Nazwa metryki | Opis |
| ------------------- | ----------------- |
|EntityName| Event Hubs obsługuje jednostki centrum zdarzeń w przestrzeni nazw.|

## <a name="azure-monitor-integration-with-siem-tools"></a>Integracja Azure Monitor z narzędziami SIEM
Kierowanie danych monitorowania (dzienników aktywności, dzienników diagnostycznych itp.) do centrum zdarzeń z Azure Monitor pozwala łatwo zintegrować z narzędziami do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM). Aby uzyskać więcej informacji, zobacz następujące artykuły/wpisy w blogu:

- [Przesyłanie strumieniowe danych monitorowania platformy Azure do centrum zdarzeń w celu użycia przez narzędzie zewnętrzne](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)
- [Wprowadzenie do Azure Log Integration](/previous-versions/azure/security/fundamentals/azure-log-integration-overview)
- [Integrowanie platformy Azure Monitor z narzędziami SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

W scenariuszu, w którym narzędzie SIEM korzysta z danych dziennika z centrum zdarzeń, jeśli nie są wyświetlane żadne komunikaty przychodzące lub komunikaty przychodzące nie są wyświetlane na wykresie metryk, wykonaj następujące kroki:

- Jeśli nie ma **komunikatów przychodzących**, oznacza to, że usługa Azure monitor nie przenosi dzienników inspekcji/diagnostyki do centrum zdarzeń. W tym scenariuszu Otwórz bilet pomocy technicznej z zespołem Azure Monitor. 
- Jeśli istnieją komunikaty przychodzące, ale **nie wychodzące wiadomości**, oznacza to, że aplikacja Siem nie odczytuje komunikatów. Skontaktuj się z dostawcą SIEM, aby określić, czy konfiguracja centrum zdarzeń jest poprawna.


## <a name="next-steps"></a>Następne kroki

* Zobacz [Omówienie monitorowania platformy Azure](../azure-monitor/overview.md).
* [Pobierz metryki Azure monitor z przykładem platformy .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) w witrynie GitHub. 

Aby uzyskać więcej informacji na temat usługi Event Hubs, skorzystaj z następujących linków:

- Wprowadzenie do samouczka Event Hubs
    - [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
    - [Java](event-hubs-java-get-started-send.md)
    - [Python](event-hubs-python-get-started-send.md)
    - [JavaScript](event-hubs-java-get-started-send.md)
* [Event Hubs — często zadawane pytania](event-hubs-faq.md)
* [Przykładowe aplikacje korzystające z usługi Event Hubs](https://github.com/Azure/azure-event-hubs/tree/master/samples)

[1]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor1.png
[2]: ./media/event-hubs-metrics-azure-monitor/event-hubs-monitor2.png
