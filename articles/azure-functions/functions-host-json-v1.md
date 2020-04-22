---
title: odwołanie host.json dla usługi Azure Functions 1.x
description: Dokumentacja referencyjna dla pliku host.json usługi Azure functions ze środowiska wykonawczego w wersji 1.
ms.topic: conceptual
ms.date: 10/19/2018
ms.openlocfilehash: 36d028d09c94ae28e77404297bd576f5e20404c6
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81757519"
---
# <a name="hostjson-reference-for-azure-functions-1x"></a>odwołanie host.json dla usługi Azure Functions 1.x

> [!div class="op_single_selector" title1="Wybierz wersję używanego środowiska wykonawczego usługi Azure Functions: "]
> * [Wersja 1](functions-host-json-v1.md)
> * [Wersja 2](functions-host-json.md)

Plik metadanych *host.json* zawiera globalne opcje konfiguracji, które wpływają na wszystkie funkcje aplikacji funkcji. W tym artykule wymieniono ustawienia, które są dostępne dla środowiska wykonawczego w wersji 1. Schemat JSON jest http://json.schemastore.org/hostw .

> [!NOTE]
> Ten artykuł dotyczy usługi Azure Functions 1.x.  Aby uzyskać odwołanie do pliku host.json w usługach 2.x i nowszych, zobacz [odwołanie do host.json dla usługi Azure Functions 2.x](functions-host-json.md).

Inne opcje konfiguracji aplikacji funkcji są zarządzane w [ustawieniach aplikacji](functions-app-settings.md).

Niektóre ustawienia host.json są używane tylko podczas uruchamiania lokalnie w pliku [local.settings.json.](functions-run-local.md#local-settings-file)

## <a name="sample-hostjson-file"></a>Przykładowy plik hosta.json

Następujące przykładowe pliki *host.json* mają wszystkie możliwe opcje określone.


```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    },
    "documentDB": {
        "connectionMode": "Gateway",
        "protocol": "Https",
        "leaseOptions": {
            "leasePrefix": "prefix"
        }
    },
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    },
    "id": "9f4ea53c5136457d883d685e57164f08",
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    },
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    },
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
    },
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    },
    "watchDirectories": [ "Shared" ],
}
```

Poniżej dalsza część artykułu Wyjaśnij każdą właściwość najwyższego poziomu. Wszystkie są opcjonalne, chyba że wskazano inaczej.

## <a name="aggregator"></a>Agregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>ApplicationInsights

[!INCLUDE [applicationInsights](../../includes/functions-host-json-applicationinsights.md)]

## <a name="documentdb"></a>DocumentDB

Ustawienia konfiguracji [wyzwalacza i powiązań usługi Azure Cosmos DB](functions-bindings-cosmosdb.md).

```json
{
    "documentDB": {
        "connectionMode": "Gateway",
        "protocol": "Https",
        "leaseOptions": {
            "leasePrefix": "prefix1"
        }
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------|
|Tryb bramy|Brama|Tryb połączenia używany przez funkcję podczas łączenia się z usługą Azure Cosmos DB. Dostępne `Direct` są i są dostępne pod`Gateway`|
|Protocol (Protokół)|Https (https)|Protokół połączenia używany przez tę funkcję podczas połączenia z usługą Azure Cosmos DB.  Przeczytaj [tutaj, aby uzyskać wyjaśnienie obu trybów](../cosmos-db/performance-tips.md#networking)|
|leasePrefix|Nie dotyczy|Prefiks dzierżawy do użycia we wszystkich funkcjach w aplikacji.|

## <a name="durabletask"></a>trwałeZadań

[!INCLUDE [durabletask](../../includes/functions-host-json-durabletask.md)]

## <a name="eventhub"></a>eventHub

Ustawienia konfiguracji [wyzwalaczy i powiązań Centrum zdarzeń](functions-bindings-event-hubs-trigger.md#functions-1x).

## <a name="functions"></a> — funkcje

Lista funkcji uruchamianych przez hosta zadań. Pusta tablica oznacza uruchomienie wszystkich funkcji. Przeznaczone do użytku tylko wtedy, gdy [działa lokalnie](functions-run-local.md). W aplikacjach funkcji na platformie Azure zamiast tego należy wykonać kroki w [jak wyłączyć funkcje w usłudze Azure Functions,](disable-function.md) aby wyłączyć określone funkcje, a nie przy użyciu tego ustawienia.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>funkcjaCzas

Wskazuje czas trwania limitu czasu dla wszystkich funkcji. W planie zużycia bez użycia serwera prawidłowy zakres wynosi od 1 sekundy do 10 minut, a wartość domyślna to 5 minut. W planie usługi app service nie ma ogólnego limitu, a wartość domyślna to _null_, co oznacza brak limitu czasu.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>monitor zdrowia

Ustawienia konfiguracji [monitora kondycji hosta](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------| 
|enabled|true|Określa, czy funkcja jest włączona. | 
|healthCheckInterval|10 sekund|Przedział czasu między okresowymi kontrolami kondycji w tle. | 
|healthCheckWindow|2 min.|Przesuwane okno czasu używane w `healthCheckThreshold` połączeniu z ustawieniem.| 
|zdrowieWykonańce|6|Maksymalna liczba razy sprawdzanie kondycji może zakończyć się niepowodzeniem przed rozpoczęciem odtwarzania hosta.| 
|licznikTrzymek|0,80|Próg, przy którym licznik wydajności będą uważane za niezdrowe.| 

## <a name="http"></a>http

Ustawienia konfiguracji [wyzwalaczy i powiązań http](functions-bindings-http-webhook.md).

```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 200,
        "maxConcurrentRequests": 100,
        "dynamicThrottlesEnabled": true
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------| 
|dynamicThrottlesEnabled|false|Po włączeniu to ustawienie powoduje, że potok przetwarzania żądań okresowo sprawdza liczniki wydajności systemu, takie jak połączenia/wątki/procesy/pamięć/cpu/etc., a jeśli którykolwiek z tych liczników przekracza wbudowany wysoki próg (80%), żądania zostaną odrzucone z odpowiedzią 429 "Zbyt zajęty", dopóki licznik(y) nie powróci do normalnego poziomu.|
|maxConcurrentRequests|bez ograniczeń`-1`( )|Maksymalna liczba funkcji HTTP, które będą wykonywane równolegle. Dzięki temu można kontrolować współbieżność, co może pomóc w zarządzaniu wykorzystaniem zasobów. Na przykład może mieć funkcję HTTP, która używa dużo zasobów systemowych (pamięci/cpu/sockets) w taki sposób, że powoduje problemy, gdy współbieżność jest zbyt wysoka. Lub może mieć funkcję, która sprawia, że wychodzące żądania do usługi innej firmy, a te wywołania muszą być ograniczone stawki. W takich przypadkach zastosowanie przepustnicy tutaj może pomóc.|
|maxOutstandingRequests|bez ograniczeń`-1`( )|Maksymalna liczba zaległych żądań, które są przechowywane w danym momencie. Ten limit obejmuje żądania, które są w kolejce, ale nie rozpoczęły wykonywania, a także wszelkie w toku wykonania. Wszystkie przychodzące żądania przekraczające ten limit są odrzucane z odpowiedzią 429 "Zbyt zajęty". Dzięki temu wywołujący można stosować strategie ponawiania na podstawie czasu, a także pomaga kontrolować maksymalne opóźnienia żądań. To tylko steruje kolejkowaniem, który występuje w ścieżce wykonywania hosta skryptu. Inne kolejki, takie jak kolejka żądań ASP.NET, będą nadal obowiązywać i nie będą objęte tym ustawieniem.|
|routePrefix|api|Prefiks trasy, który ma zastosowanie do wszystkich tras. Użyj pustego ciągu, aby usunąć domyślny prefiks. |

## <a name="id"></a>id

Unikatowy identyfikator dla hosta zadań. Może być mały identyfikator GUID z myślnikami usunięte. Wymagane podczas uruchamiania lokalnie. Podczas uruchamiania na platformie Azure zaleca się, aby nie ustawiać wartości identyfikatora. Identyfikator jest generowany automatycznie na `id` platformie Azure po pominięciu. 

Jeśli udostępniasz konto Magazynu w wielu aplikacjach funkcji, upewnij `id`się, że każda aplikacja funkcyjna ma inną . Można pominąć `id` właściwość lub ręcznie ustawić każdą `id` aplikację funkcji do innej wartości. Wyzwalacz czasomierza używa blokady magazynu, aby upewnić się, że będzie tylko jedno wystąpienie czasomierza, gdy aplikacja funkcji skaluje się w poziomie do wielu wystąpień. Jeśli dwie aplikacje `id` funkcji współużytkują to samo i każda z nich używa wyzwalacza czasomierza, zostanie uruchomiony tylko jeden czasomierz.

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>Rejestratora

Steruje filtrowaniem dzienników napisanych przez [obiekt ILogger](functions-monitoring.md#write-logs-in-c-functions) lub [przez context.log](functions-monitoring.md#write-logs-in-javascript-functions).

```json
{
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------| 
|categoryFilter|Nie dotyczy|Określa filtrowanie według kategorii| 
|domyślniePoziom|Informacje|Dla wszystkich kategorii, które `categoryLevels` nie są określone w tablicy, wyślij dzienniki na tym poziomie i powyżej do usługi Application Insights.| 
|kategoriaLevels|Nie dotyczy|Tablica kategorii określająca minimalny poziom dziennika do wysłania do usługi Application Insights dla każdej kategorii. Kategoria określona w tym miejscu kontroluje wszystkie kategorie, które zaczynają się od tej samej wartości, a dłuższe wartości mają pierwszeństwo. W poprzednim przykładowym pliku *host.json* wszystkie kategorie, które zaczynają się `Information` od dziennika "Host.Aggregator" na poziomie. Wszystkie inne kategorie, które zaczynają się od "Host", takie `Error` jak "Host.Executor", logują się na poziomie.| 

## <a name="queues"></a>Kolejek

Ustawienia konfiguracji [wyzwalaczy i powiązań kolejki magazynu](functions-bindings-storage-queue.md).

```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------| 
|maxPollingInterval (właso.|60000|Maksymalny interwał w milisekundach między ankietami kolejki.| 
|widocznośćCzas|0|Przedział czasu między ponownych prób podczas przetwarzania wiadomości kończy się niepowodzeniem.| 
|batchSize|16|Liczba komunikatów kolejki, które funkcje środowiska wykonawczego pobiera jednocześnie i procesów równolegle. Gdy liczba przetwarzana pobiera w `newBatchThreshold`dół do , środowisko wykonawcze pobiera inną partię i rozpoczyna przetwarzanie tych komunikatów. Tak więc maksymalna liczba równoczesnych komunikatów `batchSize` `newBatchThreshold`przetwarzanych na funkcję jest plus . Ten limit ma zastosowanie oddzielnie do każdej funkcji wyzwalaneją kolejki. <br><br>Jeśli chcesz uniknąć równoległego wykonywania wiadomości odebranych w `batchSize` jednej kolejce, można ustawić na 1. Jednak to ustawienie eliminuje współbieżność tylko tak długo, jak aplikacja funkcji działa na jednej maszynie wirtualnej (VM). Jeśli aplikacja funkcji jest skalowana w poziomie do wielu maszyn wirtualnych, każda maszyna wirtualna może uruchomić jedno wystąpienie każdej funkcji wyzwalanej kolejką.<br><br>Maksymalna `batchSize` wartość to 32. | 
|maksymalna liczba kolejek nieliczna|5|Liczba razy, aby spróbować przetworzyć wiadomość przed przeniesieniem go do kolejki trucizny.| 
|newBatchThreshold|batchSize/2|Za każdym razem, gdy liczba wiadomości przetwarzanych jednocześnie spada do tej liczby, środowisko wykonawcze pobiera inną partię.| 

## <a name="sendgrid"></a>SendGrid

Ustawienie konfiguracji [wiązania wyjściowego SendGrind](functions-bindings-sendgrid.md)

```json
{
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
    }
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------| 
|Z|Nie dotyczy|Adres e-mail nadawcy we wszystkich funkcjach.| 

## <a name="servicebus"></a>usługiBus

Ustawienie konfiguracji [wyzwalaczy i powiązań usługi Service Bus](functions-bindings-service-bus.md).

```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------| 
|Maxconcurrentcalls|16|Maksymalna liczba równoczesnych wywołań wywołania zwrotnego, które pompa wiadomości powinna zainicjować. Domyślnie środowisko wykonawcze Functions przetwarza wiele komunikatów jednocześnie. Aby skierować środowisko uruchomieniowe do przetwarzania tylko jednej `maxConcurrentCalls` kolejki lub komunikatu tematu w czasie, należy ustawić na 1. | 
|Konto prefetch|Nie dotyczy|Domyślny PrefetchCount, który będzie używany przez podstawowe MessageReceiver.| 
|autoRenewTimeout|00:05:00|Maksymalny czas trwania, w którym blokada wiadomości zostanie automatycznie odnowiona.| 

## <a name="singleton"></a>Singleton

Ustawienia konfiguracji zachowania blokady singleton. Aby uzyskać więcej informacji, zobacz [GitHub problem dotyczący obsługi singleton](https://github.com/Azure/azure-webjobs-sdk-script/issues/912).

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------| 
|lockPeriod|00:00:15|Okres, dla który blokuje poziom funkcji. Blokady automatycznie odnawiają.| 
|odbiornikLockPeriod|00:01:00|Okres, dla który są brane zablokowania odbiornika.| 
|odbiornikLockRecoveryPollingInterval|00:01:00|Przedział czasu używany do odzyskiwania blokady odbiornika, jeśli blokada odbiornika nie może zostać nabyta podczas uruchamiania.| 
|lockAcquisitionTimeout|00:01:00|Maksymalny czas, przez który środowisko wykonawcze spróbuje uzyskać blokadę.| 
|lockAcquisitionPollingInterval|Nie dotyczy|Interwał między próbami nabycia blokady.| 

## <a name="tracing"></a>Śledzenia

*Wersja 1.x*

Ustawienia konfiguracji dzienników utworzonych przy `TraceWriter` użyciu obiektu. Zobacz [C# Rejestrowanie](functions-reference-csharp.md#logging) i [Node.js Rejestrowanie](functions-reference-node.md#writing-trace-output-to-the-console).

```json
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|Właściwość  |Domyślne | Opis |
|---------|---------|---------| 
|konsolaPoziom|informacje o|Poziom śledzenia rejestrowania konsoli. Dostępne opcje `off` `error`to: `info`, `verbose`, `warning`, , i .|
|tryb rejestrowania plików|debugOnly|Poziom śledzenia rejestrowania plików. Dostępne `never`opcje `always` `debugOnly`to , , .| 

## <a name="watchdirectories"></a>watchKatary

Zestaw [katalogów kodu udostępnionego,](functions-reference-csharp.md#watched-directories) które powinny być monitorowane pod kątem zmian.  Zapewnia, że po zmianie kodu w tych katalogach zmiany są pobierane przez funkcje.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak zaktualizować plik host.json](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Zobacz ustawienia globalne w zmiennych środowiskowych](functions-app-settings.md)
