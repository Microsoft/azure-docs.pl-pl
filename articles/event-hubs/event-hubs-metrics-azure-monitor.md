---
title: Metryki w Azure Monitor — Event Hubs platformy Azure | Microsoft Docs
description: Ten artykuł zawiera informacje dotyczące monitorowania platformy Azure Event Hubs przy użyciu usługi Azure Monitoring
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: 74830775a4f31e6f8e486b4d6cc434335b4ee723
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165896"
---
# <a name="azure-event-hubs-metrics-in-azure-monitor"></a>Metryki usługi Azure Event Hubs na platformie Azure Monitor

Metryki Event Hubs zapewniają stan Event Hubs zasobów w ramach subskrypcji platformy Azure. Dzięki bogatemu zestawowi danych metryk można ocenić ogólną kondycję centrów zdarzeń nie tylko na poziomie przestrzeni nazw, ale również na poziomie jednostki. Te dane statystyczne mogą być ważne, ponieważ ułatwiają monitorowanie stanu centrów zdarzeń. Metryki mogą również pomóc w rozwiązywaniu problemów dotyczących głównych przyczyn, bez konieczności kontaktowania się z pomocą techniczną platformy Azure.

Azure Monitor zapewnia ujednolicone interfejsy użytkownika do monitorowania różnych usług platformy Azure. Aby uzyskać więcej informacji, zobacz [monitorowanie w Microsoft Azure](../azure-monitor/overview.md) i [pobieranie metryk Azure monitor z przykładem platformy .NET](https://github.com/Azure-Samples/monitor-dotnet-metrics-api) w witrynie GitHub.

## <a name="access-metrics"></a>Metryki dostępu

Azure Monitor zapewnia wiele sposobów uzyskiwania dostępu do metryk. Możesz uzyskać dostęp do metryk przy użyciu [Azure Portal](https://portal.azure.com)lub użyć interfejsów API Azure monitor (REST i .NET) oraz rozwiązań do analizy, takich jak Log Analytics i Event Hubs. Aby uzyskać więcej informacji, zobacz [monitorowanie danych zebranych przez Azure monitor](../azure-monitor/platform/data-platform.md).

Metryki są domyślnie włączone i można uzyskać dostęp do najnowszych danych z ostatnich 30 dni. Jeśli chcesz przechowywać dane przez dłuższy czas, możesz zarchiwizować dane metryk na koncie usługi Azure Storage. To ustawienie można skonfigurować w [ustawieniach diagnostycznych](../azure-monitor/platform/diagnostic-settings.md) w Azure monitor.


## <a name="access-metrics-in-the-portal"></a>Dostęp do metryk w portalu

Można monitorować metryki w czasie w [Azure Portal](https://portal.azure.com). Poniższy przykład pokazuje, jak wyświetlić pomyślne żądania i żądania przychodzące na poziomie konta:

![Wyświetl metryki pomyślne][1]

Możesz również uzyskać dostęp do metryk bezpośrednio za pośrednictwem przestrzeni nazw. Aby to zrobić, wybierz przestrzeń nazw, a następnie wybierz pozycję **metryki**. Aby wyświetlić metryki odfiltrowane do zakresu centrum zdarzeń, wybierz centrum zdarzeń, a następnie wybierz pozycję **metryki**.

W przypadku metryk obsługujących wymiary należy filtrować według żądanej wartości wymiaru, jak pokazano w następującym przykładzie:

![Filtruj z wartością wymiaru][2]

## <a name="billing"></a>Rozliczenia

Korzystanie z metryk w Azure Monitor jest obecnie bezpłatne. Jeśli jednak używasz innych rozwiązań, które pozyskają dane metryk, możesz obciążyć te rozwiązania. Na przykład w przypadku archiwizowania danych metryk na koncie usługi Azure Storage są naliczane opłaty za usługę Azure Storage. Opłaty są naliczane także przez platformę Azure, jeśli dane metryk są przesyłane strumieniowo do dzienników Azure Monitor w celu przeprowadzenia zaawansowanej analizy.

Poniższe metryki zapewniają przegląd kondycji usługi. 

> [!NOTE]
> Gdy są one przenoszone pod inną nazwę, jest przestarzałe kilka metryk. Może to wymagać aktualizacji odwołań. Metryki oznaczone słowem kluczowym "przestarzałe" nie będą obsługiwane do przodu.

Wszystkie wartości metryk są wysyłane do Azure Monitor co minutę. Stopień szczegółowości czasu określa przedział czasu, w którym są prezentowane wartości metryk. Obsługiwany przedział czasu dla wszystkich metryk Event Hubs wynosi 1 minutę.

## <a name="azure-event-hubs-metrics"></a>Metryki usługi Azure Event Hubs
Listę metryk obsługiwanych przez usługę można znaleźć w temacie [Azure Event Hubs](../azure-monitor/platform/metrics-supported.md#microsofteventhubnamespaces)

> [!NOTE]
> Gdy wystąpi błąd użytkownika, usługa Azure Event Hubs aktualizuje metrykę **błędy użytkownika** , ale nie rejestruje żadnych innych informacji diagnostycznych. W związku z tym należy przechwycić szczegółowe informacje o błędach użytkowników w aplikacjach. Możesz również przekonwertować dane telemetryczne generowane, gdy wiadomości są wysyłane lub odbierane w usłudze Application Insights. Aby zapoznać się z przykładem, zobacz [śledzenie przy użyciu Application Insights](../service-bus-messaging/service-bus-end-to-end-tracing.md#tracking-with-azure-application-insights).

## <a name="azure-monitor-integration-with-siem-tools"></a>Integracja Azure Monitor z narzędziami SIEM
Kierowanie danych monitorowania (dzienników aktywności, dzienników diagnostycznych itp.) do centrum zdarzeń z Azure Monitor umożliwia łatwą integrację z narzędziami do zarządzania informacjami i zdarzeniami zabezpieczeń (SIEM). Aby uzyskać więcej informacji, zobacz następujące artykuły/wpisy w blogu:

- [Przesyłanie strumieniowe danych monitorowania platformy Azure do centrum zdarzeń w celu użycia przez narzędzie zewnętrzne](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)
- [Wprowadzenie do Azure Log Integration](/previous-versions/azure/security/fundamentals/azure-log-integration-overview)
- [Integrowanie platformy Azure Monitor z narzędziami SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/)

W scenariuszu, w którym narzędzie SIEM korzysta z danych dziennika z centrum zdarzeń, jeśli nie są wyświetlane żadne komunikaty przychodzące lub komunikaty przychodzące nie są wyświetlane na wykresie metryk, wykonaj następujące kroki:

- Jeśli nie ma **komunikatów przychodzących**, oznacza to, że usługa Azure monitor nie przenosi dzienników inspekcji/diagnostyki do centrum zdarzeń. W tym scenariuszu Otwórz bilet pomocy technicznej z zespołem Azure Monitor. 
- Jeśli istnieją komunikaty przychodzące, ale nie są wysyłane **komunikaty wychodzące**, oznacza to, że aplikacja Siem nie odczytuje komunikatów. Skontaktuj się z dostawcą SIEM, aby określić, czy konfiguracja centrum zdarzeń jest poprawna.


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
