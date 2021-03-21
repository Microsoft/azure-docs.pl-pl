---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: service-bus-messaging, event-hubs
author: spelluru
ms.service: service-bus-messaging, event-hubs
ms.topic: include
ms.date: 12/12/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 1ce983ee739a4a124a93c7913f092b23dfec3cbd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98901101"
---
## <a name="what-is-a-replication-task"></a>Co to jest zadanie replikacji?

Zadanie replikacji odbiera zdarzenia ze źródła i przekazuje je do obiektu docelowego.
Większość zadań replikacji spowoduje przekazanie zdarzeń niezmienionych i przeprowadzenie mapowania między strukturami metadanych, jeśli protokoły źródłowe i docelowe różnią się od siebie. 

Zadania replikacji są zwykle bezstanowe, co oznacza, że nie udostępniają stanu lub innych efektów ubocznych w wyniku wykonywania zadań sekwencyjnych lub równoległych. Ma również wartość true w przypadku przetwarzania wsadowego i tworzenia łańcucha, który można zaimplementować na podstawie istniejącego stanu strumienia. 

Powoduje to, że zadania replikacji różnią się od zadań agregacji, które są ogólnie stanowe, a są domeną platform i usług analitycznych, takich jak [Azure Stream Analytics](../articles/stream-analytics/stream-analytics-introduction.md).

## <a name="replication-applications-and-tasks-in-azure-functions"></a>Replikacja aplikacji i zadań w Azure Functions

W Azure Functions zadanie replikacji jest implementowane przy użyciu [wyzwalacza](../articles/azure-functions/functions-triggers-bindings.md) , który uzyskuje co najmniej jeden komunikat wejściowy ze skonfigurowanego źródła i [powiązanie danych wyjściowych](../articles/azure-functions/functions-triggers-bindings.md#binding-direction) , które przesyła wiadomości skopiowane ze źródła do skonfigurowanego obiektu docelowego. 

| Wyzwalacz  | Dane wyjściowe |
|----------|--------|
| [Wyzwalacz usługi Azure Event Hubs](../articles/azure-functions/functions-bindings-event-hubs-trigger.md?tabs=csharp) | [Powiązanie danych wyjściowych usługi Azure Event Hub](../articles/azure-functions/functions-bindings-event-hubs-output.md?tabs=csharp) |
| [Wyzwalacz Azure Service Bus](../articles/azure-functions/functions-bindings-service-bus-trigger.md?tabs=csharp) | [Azure Service Bus powiązanie danych wyjściowych](../articles/azure-functions/functions-bindings-service-bus-output.md?tabs=csharp)
| [Wyzwalacz usługi Azure IoT Hub](../articles/azure-functions/functions-bindings-event-iot-trigger.md?tabs=csharp) | [Powiązanie danych wyjściowych usługi Azure IoT Hub](../articles/azure-functions/functions-bindings-event-iot-output.md?tabs=csharp)
| [Wyzwalacz Azure Event Grid](../articles/azure-functions/functions-bindings-event-grid-trigger.md?tabs=csharp) | [Azure Event Grid powiązanie danych wyjściowych](../articles/azure-functions/functions-bindings-event-grid-output.md?tabs=csharp)
| [Wyzwalacz usługi Azure Queue Storage](../articles/azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp) | [Powiązanie danych wyjściowych usługi Azure Queue Storage](../articles/azure-functions/functions-bindings-storage-queue-output.md?tabs=csharp)
| [Wyzwalacz Apache Kafka](https://github.com/azure/azure-functions-kafka-extension) | [Apache Kafka powiązanie danych wyjściowych](https://github.com/azure/azure-functions-kafka-extension)
| [Wyzwalacz RabbitMQ](https://github.com/azure/azure-functions-rabbitmq-extension) | [RabbitMQ wyjściowe powiązania](https://github.com/azure/azure-functions-rabbitmq-extension) 
| | [Powiązanie danych wyjściowych usługi Azure Notification Hubs](../articles/azure-functions/functions-bindings-notification-hubs.md)
||[Powiązanie danych wyjściowych usługi sygnałów platformy Azure](../articles/azure-functions/functions-bindings-signalr-service-output.md?tabs=csharp)
||[Powiązanie danych wyjściowych Twilio SendGrid](../articles/azure-functions/functions-bindings-sendgrid.md?tabs=csharp)

Zadania replikacji są wdrażane w ramach aplikacji replikacji za pomocą tych samych metod wdrażania, jak każda inna aplikacja Azure Functions. Można skonfigurować wiele zadań w tej samej aplikacji. 

W przypadku Azure Functions Premium wiele aplikacji do replikacji może współużytkować tę samą źródłową pulę zasobów, nazywaną planem App Service. Oznacza to, że można łatwo rozstawić zadania replikacji w programie .NET przy użyciu zadań replikacji, które są zapisywane w języku Java, na przykład. Jest to przydatne, jeśli chcesz skorzystać z określonych bibliotek, takich jak Apache notacji CamelCase, które są dostępne tylko dla języka Java, i jeśli są one najlepszą opcją dla konkretnej ścieżki integracji, mimo że zwykle preferujesz inny język i środowisko uruchomieniowe dla innych zadań replikacji. 

Zawsze, gdy jest to możliwe, należy preferować wyzwalacze zorientowane na partie przez wyzwalacze, które dostarczają pojedyncze zdarzenia lub komunikaty, a następnie zawsze uzyskują kompletną strukturę zdarzeń lub komunikatów, zamiast korzystać z [wyrażeń powiązań parametrów](../articles/azure-functions/functions-bindings-expressions-patterns.md)funkcji platformy Azure.

Nazwa funkcji powinna odzwierciedlać parę źródłową i docelową, którą nawiązujesz połączenie, i należy prefiksować odwołania do parametrów połączenia lub innych elementów konfiguracji w plikach konfiguracji aplikacji o tej nazwie. 

### <a name="data-and-metadata-mapping"></a>Mapowanie danych i metadanych

Po podaniu pary wyzwalacza wejściowego i powiązania danych wyjściowych trzeba będzie wykonać pewne mapowanie między różnymi typami zdarzeń lub komunikatów, chyba że typ wyzwalacza i dane wyjściowe są takie same.

W przypadku prostych zadań replikacji, które kopiują komunikaty między Event Hubs i Service Bus, nie trzeba pisać własnego kodu, ale mogą być pochylenie w [bibliotece narzędzi dostarczonej](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) z próbkami replikacji.

### <a name="retry-policy"></a>Zasady ponawiania

Aby uniknąć utraty danych podczas zdarzenia dostępności po obu stronach funkcji replikacji, należy skonfigurować zasady ponawiania, aby były niezawodne. Aby skonfigurować zasady ponawiania prób, zapoznaj się z [dokumentacją Azure Functions](../articles/azure-functions/functions-bindings-error-pages.md) . 

Ustawienia zasad wybrane dla przykładowych projektów w [repozytorium przykładowym](https://github.com/Azure-Samples/azure-messaging-replication-dotnet) konfigurują wykładniczą strategię wycofywania z interwałami ponawiania prób od 5 sekund do 15 minut z nieskończoną ponowną próbą, aby uniknąć utraty danych. 

Aby uzyskać Service Bus, zapoznaj się z sekcją ["Używanie obsługi ponowień na poziomie odporności na wyzwalacze"](../articles/azure-functions/functions-bindings-error-pages.md#using-retry-support-on-top-of-trigger-resilience) , aby zrozumieć interakcję wyzwalaczy i maksymalną liczbę dostaw zdefiniowaną dla kolejki.

### <a name="setting-up-a-replication-application-host"></a>Konfigurowanie hosta aplikacji do replikacji

Aplikacja replikacji to host wykonywania dla co najmniej jednego zadania replikacji. 

Jest to aplikacja Azure Functions, która jest skonfigurowana do uruchamiania w planie zużycia lub (zalecane) w planie Azure Functions Premium. Wszystkie aplikacje replikacji muszą działać w ramach [tożsamości zarządzanej przypisanej do systemu lub przez użytkownika](../articles/app-service/overview-managed-identity.md). 

Połączone Azure Resource Manager (ARM) szablony tworzą i konfigurują aplikację replikacji przy użyciu:

* konto usługi Azure Storage do śledzenia postępu replikacji i dzienników,
* tożsamość zarządzana przypisana przez system i 
* Monitorowanie platformy Azure i integracja Application Insights na potrzeby monitorowania.

Aplikacje do replikacji, które muszą mieć dostęp Event Hubs powiązane z siecią wirtualną platformy Azure, muszą używać planu Azure Functions Premium i być skonfigurowane do dołączania do tej samej sieci wirtualnej, która jest również jedną z dostępnych opcji.


|       | Wdróż | Wizualizacja  
|-------|------------------|--------------|---------------|
| **Azure Functions plan zużycia** | [![Wdróż na platformie Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2FAconsumption%2Fazuredeploy.json)|[![Wizualizacja](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fconsumption%2Fazuredeploy.json)
| **Plan Azure Functions Premium** |[![Wdróż na platformie Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json) | [![Wizualizacja](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json)
| **Azure Functions plan Premium z siecią wirtualną** | [![Wdróż na platformie Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)|[![Wizualizacja](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)


### <a name="examples"></a>Przykłady

[Repozytorium przykładów](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/) zawiera kilka przykładów zadań replikacji, które kopiują zdarzenia między Event Hubs i/lub między jednostkami Service Bus.

Do kopiowania zdarzeń między Event Hubs należy użyć wyzwalacza centrum zdarzeń z powiązaniem wyjściowym centrum zdarzeń:

```csharp
[FunctionName("telemetry")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task Telemetry(
    [EventHubTrigger("telemetry", ConsumerGroup = "$USER_FUNCTIONS_APP_NAME.telemetry", Connection = "telemetry-source-connection")] EventData[] input,
    [EventHub("telemetry-copy", Connection = "telemetry-target-connection")] EventHubClient outputClient,
    ILogger log)
{
    return EventHubReplicationTasks.ForwardToEventHub(input, outputClient, log);
}
```

W przypadku kopiowania komunikatów między jednostkami Service Bus Użyj wyzwalacza Service Bus i powiązania danych wyjściowych:

```csharp
[FunctionName("jobs-transfer")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task JobsTransfer(
    [ServiceBusTrigger("jobs-transfer", Connection = "jobs-transfer-source-connection")] Message[] input,
    [ServiceBus("jobs", Connection = "jobs-target-connection")] IAsyncCollector<Message> output,
    ILogger log)
{
    return ServiceBusReplicationTasks.ForwardToServiceBus(input, output, log);
}
```

Metody pomocnika mogą ułatwić replikację między Event Hubs i Service Bus:

| Element źródłowy      | Cel      | Punkt wejścia 
|-------------|-------------|------------------------------------------------------------------------
| Centrum zdarzeń   | Centrum zdarzeń   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| Centrum zdarzeń   | Service Bus | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| Service Bus | Centrum zdarzeń   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| Service Bus | Service Bus | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`


### <a name="monitoring"></a>Monitorowanie

Aby dowiedzieć się, jak można monitorować aplikację replikacji, zapoznaj się z [sekcją monitorowanie](../articles/azure-functions/configure-monitoring.md) w dokumentacji Azure Functions.

Szczególnie przydatne narzędzie wizualne do monitorowania zadań replikacji to Application Insights [Mapa aplikacji](../articles/azure-monitor/app/app-map.md), która jest generowana automatycznie na podstawie przechwyconych informacji o monitorowaniu i umożliwia Eksplorowanie niezawodności i wydajności źródła zadań replikacji i transferów docelowych.

W celu natychmiastowego wglądu w dane diagnostyczne możesz korzystać z narzędzia Portal [metryk na żywo](../articles/azure-monitor/app/live-stream.md) , które zapewnia wizualizację o małym opóźnieniu dotyczącej szczegółów dziennika.

## <a name="next-steps"></a>Następne kroki

* [Wdrożenia Azure Functions](../articles/azure-functions/functions-deployment-technologies.md)
* [Diagnostyka Azure Functions](../articles/azure-functions/functions-diagnostics.md)
* [Opcje sieci Azure Functions](../articles/azure-functions/functions-networking-options.md)
* [Application Insights platformy Azure](../articles/azure-monitor/app/app-insights-overview.md)