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
ms.openlocfilehash: 5070d1dc19995b635ebff6b98fec07e2b7d9fc70
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97663666"
---
Azure Functions umożliwia tworzenie zadań replikacji przeznaczonych tylko do konfiguracji, które są pochylenie w przedbudowanym punkcie wejścia. [Przykłady replikacji opartej na konfiguracji dla Azure Functions](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config) ilustrują sposób korzystania z [prebudowanych pomocników](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) w własnym kodzie lub uniknąć całkowitego obsługi kodu i samego użycia konfiguracji.

## <a name="create-a-replication-task"></a>Tworzenie zadania replikacji
Aby utworzyć takie zadanie replikacji, najpierw utwórz nowy folder pod folderem projektu. Nazwa nowego folderu to nazwa funkcji, na przykład `europeToAsia` lub `telemetry` . Nazwa nie ma bezpośredniej korelacji z używanymi jednostkami komunikatów i służy tylko do identyfikowania ich. Można utworzyć dziesiątki funkcji w tym samym projekcie.

Następnie Utwórz `function.json` plik w folderze. Plik konfiguruje funkcję. Zacznij od następującej zawartości:

``` JSON
{
    "configurationSource": "config",
    "bindings" : [
        {
            "direction": "in",
            "name": "input" 
        },
        {
            "direction": "out",
            "name": "output"
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": -1,
        "minimumInterval": "00:00:05",
        "maximumInterval": "00:05:00"
    },
    "disabled": false,
    "scriptFile": "../bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.*"
}
```

W tym pliku należy wykonać trzy czynności konfiguracyjne, które zależą od jednostek, które chcesz połączyć:

1. [Skonfiguruj kierunek wprowadzania](#configure-the-input-direction)
2. [Skonfiguruj kierunek danych wyjściowych](#configure-the-output-direction)
3. [Konfigurowanie punktu wejścia](#configure-the-entry-point)

### <a name="configure-the-input-direction"></a>Skonfiguruj kierunek wprowadzania

#### <a name="event-hub-input"></a>Dane wejściowe centrum zdarzeń

Jeśli chcesz otrzymywać zdarzenia z centrum zdarzeń, Dodaj informacje o konfiguracji do górnej sekcji w obszarze "powiązania", które ustawia

* **Typ** — typ "eventHubTrigger".
* **połączenie** — Nazwa wartości ustawień aplikacji dla parametrów połączenia centrum zdarzeń. 
* **eventHubName** — nazwa centrum zdarzeń w przestrzeni nazw identyfikowanego przez parametry połączenia.
* Grupa **odbiorców** — nazwa grupy odbiorców. Należy pamiętać, że nazwa jest ujęta w znaki procentu i w związku z tym odnosi się również do wartości ustawień aplikacji.

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "eventHubTrigger",
            "connection": "functionname-source-connection",
            "eventHubName": "eventHubA",
            "consumerGroup" : "%functionname-source-consumergroup%",
            "name": "input" 
        }
    ...
```

#### <a name="service-bus-queue-input"></a>Dane wejściowe kolejki Service Bus

Jeśli chcesz otrzymywać zdarzenia z kolejki Service Bus, Dodaj informacje o konfiguracji do górnej sekcji w obszarze "powiązania", które ustawia

* **Typ** — typ "serviceBusTrigger".
* **połączenie** — Nazwa wartości ustawień aplikacji dla parametrów połączenia Service Bus.
* **QueueName** — nazwa kolejki Service Bus w przestrzeni nazw identyfikowanej przez parametry połączenia.

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "serviceBusTrigger",
            "connection": "functionname-source-connection",
            "queueName": "queue-a",
            "name": "input" 
        }
    ...
```

#### <a name="service-bus-topic-input"></a>Dane wejściowe tematu Service Bus

Jeśli chcesz otrzymywać zdarzenia z tematu Service Bus, Dodaj informacje o konfiguracji do górnej sekcji w obszarze "powiązania", które ustawia

* **Typ** — typ "serviceBusTrigger".
* **połączenie** — Nazwa wartości ustawień aplikacji dla parametrów połączenia Service Bus. Ta wartość musi być wymagana `{FunctionName}-source-connection` , jeśli chcesz użyć dostarczonych skryptów.
* Nazwa **tematu** -Service Bus tematu w przestrzeni nazw identyfikowanego przez parametry połączenia.
* **subscriptionname** — nazwa subskrypcji Service Bus w danym temacie w przestrzeni nazw identyfikowanej przez parametry połączenia.

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "serviceBusTrigger",
            "connection": "functionname-source-connection",
            "topicName": "topic-x",
            "subscriptionName" : "sub-y",
            "name": "input" 
        }
    ...
```

### <a name="configure-the-output-direction"></a>Skonfiguruj kierunek danych wyjściowych

#### <a name="event-hub-output"></a>Dane wyjściowe centrum zdarzeń

Jeśli chcesz przesłać zdarzenia do centrum zdarzeń, Dodaj informacje o konfiguracji do dolnej sekcji w obszarze "powiązania", które ustawia

* **Typ** — typ "eventHub".
* **połączenie** — Nazwa wartości ustawień aplikacji dla parametrów połączenia centrum zdarzeń. 
* **eventHubName** — nazwa centrum zdarzeń w przestrzeni nazw identyfikowanego przez parametry połączenia.

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "eventHub",
            "connection": "functionname-target-connection",
            "eventHubName": "eventHubB",
            "name": "output" 
        }
    ...
```

#### <a name="service-bus-queue-output"></a>Dane wyjściowe kolejki Service Bus

Jeśli chcesz przesłać zdarzenia do kolejki Service Bus, Dodaj informacje o konfiguracji do dolnej sekcji w obszarze "powiązania", które ustawia

* **Typ** — typ "serviceBus".
* **połączenie** — Nazwa wartości ustawień aplikacji dla parametrów połączenia Service Bus. 
* **QueueName** — nazwa kolejki Service Bus w przestrzeni nazw identyfikowanej przez parametry połączenia.

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "serviceBus",
            "connection": "functionname-target-connection",
            "eventHubName": "queue-b",
            "name": "output" 
        }
    ...
```

#### <a name="service-bus-topic-output"></a>Dane wyjściowe tematu Service Bus

Jeśli chcesz przesłać zdarzenia do Service Bus tematu, Dodaj informacje o konfiguracji do dolnej sekcji w obszarze "powiązania", które ustawia

* **Typ** — typ "serviceBus".
* **połączenie** — Nazwa wartości ustawień aplikacji dla parametrów połączenia Service Bus. 
* Nazwa **tematu** -Service Bus tematu w przestrzeni nazw identyfikowanego przez parametry połączenia.

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "serviceBus",
            "connection": "functionname-target-connection",
            "eventHubName": "queue-b",
            "name": "output" 
        }
    ...
```

### <a name="configure-the-entry-point"></a>Konfigurowanie punktu wejścia

Konfiguracja punktu wejścia umożliwia wybór jednej z zadań replikacji standardowej. Jeśli modyfikujesz `Azure.Messaging.Replication` projekt, możesz również dodać zadania i odwoływać się do nich w tym miejscu. Przykład:

```JSON
    ...
    "scriptFile": "../dotnet/bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub"
    ...
```

Poniższa tabela zawiera poprawne wartości kombinacji źródeł i elementów docelowych:

| Element źródłowy      | Cel      | Punkt wejścia 
|-------------|-------------|------------------------------------------------------------------------
| Centrum zdarzeń   | Centrum zdarzeń   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| Centrum zdarzeń   | Service Bus | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| Service Bus | Centrum zdarzeń   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| Service Bus | Service Bus | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`

### <a name="retry-policy"></a>Zasady ponawiania

Aby skonfigurować zasady ponawiania prób, zapoznaj się z [dokumentacją Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-bindings-error-pages.md) . Ustawienia zasad wybrane w ramach projektów w tym repozytorium konfigurują wykładniczą strategię wycofywania z interwałami ponawiania prób od 5 sekund do 5 minut z nieskończoną ponowną próbą, aby uniknąć utraty danych.

Aby uzyskać Service Bus, zapoznaj się z sekcją ["Używanie obsługi ponowień na poziomie odporności na wyzwalacze"](https://docs.microsoft.com/azure/azure-functions/functions-bindings-error-pages.md#using-retry-support-on-top-of-trigger-resilience) , aby zrozumieć interakcję wyzwalaczy i maksymalną liczbę dostaw zdefiniowaną dla kolejki.

### <a name="build-deploy-and-configure"></a>Kompiluj, wdrażaj i Konfiguruj

Podczas skoncentrowania się na konfiguracji zadania nadal wymagają skompilowania aplikacji możliwej do wdrożenia i skonfigurowania hostów Azure Functions, tak że mają wszystkie wymagane informacje, aby połączyć się z poszczególnymi punktami końcowymi. 

Jest to zilustrowane razem ze skryptami wielokrotnego użytku w przykładach [replikacji opartych na konfiguracji dla Azure Functions](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config).

