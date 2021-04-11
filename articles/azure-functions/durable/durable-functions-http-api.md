---
title: Interfejsy API protokołu HTTP w Durable Functions — Azure Functions
description: Dowiedz się, jak zaimplementować interfejsy API protokołu HTTP w rozszerzeniu Durable Functions, aby Azure Functions.
author: cgillum
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 0ab9f33616547c073e8e3a2128a441238bf3a17d
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2021
ms.locfileid: "106220457"
---
# <a name="http-api-reference"></a>Dokumentacja interfejsu API protokołu HTTP

Rozszerzenie Durable Functions uwidacznia zestaw wbudowanych interfejsów API protokołu HTTP, które mogą służyć do wykonywania zadań zarządzania w ramach [aranżacji](durable-functions-types-features-overview.md#orchestrator-functions), [jednostek](durable-functions-types-features-overview.md#entity-functions)i [centrów zadań](durable-functions-task-hubs.md). Te interfejsy API HTTP to rozszerzalne elementy webhook autoryzowane przez hosta Azure Functions, ale obsługiwane bezpośrednio przez rozszerzenie Durable Functions.

Wszystkie interfejsy API HTTP implementowane przez rozszerzenie wymagają następujących parametrów. Typem danych wszystkich parametrów jest `string` .

| Parametr        | Typ parametru  | Opis |
|------------------|-----------------|-------------|
| **`taskHub`**    | Ciąg zapytania    | Nazwa [centrum zadań](durable-functions-task-hubs.md). Jeśli nie zostanie określony, założono nazwę centrum zadań bieżącej aplikacji funkcji. |
| **`connection`** | Ciąg zapytania    | **Nazwa** parametrów połączenia dla konta magazynu. Jeśli nie zostanie określony, przyjmuje się domyślne parametry połączenia dla aplikacji funkcji. |
| **`systemKey`**  | Ciąg zapytania    | Klucz autoryzacji wymagany do wywołania interfejsu API. |

`systemKey` jest kluczem autoryzacji automatycznie generowanym przez hosta Azure Functions. Zapewnia ona specjalny dostęp do interfejsów API rozszerzenia zadania trwałego i można nimi zarządzać w taki sam sposób jak [inne klucze autoryzacji](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Key-management-API). Można generować adresy URL, które zawierają prawidłowe `taskHub` `connection` wartości ciągu zapytania,, i, `systemKey` przy użyciu interfejsów API [powiązania klienta aranżacji](durable-functions-bindings.md#orchestration-client) , takie jak `CreateCheckStatusResponse` `CreateHttpManagementPayload` interfejsy API i interfejsy `createCheckStatusResponse` i interfejsów API `createHttpManagementPayload` w języku JavaScript.

W następnych sekcjach znajdują się określone interfejsy API protokołu HTTP obsługiwane przez rozszerzenie i przedstawiono przykłady ich użycia.

## <a name="start-orchestration"></a>Rozpocznij aranżację

Uruchamia nowe wystąpienie określonej funkcji programu Orchestrator.

### <a name="request"></a>Żądanie

W przypadku wersji 1. x środowiska uruchomieniowego usługi Functions żądanie jest sformatowane w następujący sposób (wiele wierszy jest widocznych dla przejrzystości):

```http
POST /admin/extensions/DurableTaskExtension/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

W wersji 2. x środowiska uruchomieniowego funkcji format adresu URL ma wszystkie te same parametry, ale z nieco innym prefiksem:

```http
POST /runtime/webhooks/durabletask/orchestrators/{functionName}/{instanceId?}
     ?taskHub={taskHub}
     &connection={connectionName}
     &code={systemKey}
```

Parametry żądania dla tego interfejsu API obejmują domyślnie wymieniony wcześniej zestaw, a także następujące unikatowe parametry:

| Pole              | Typ parametru  | Opis |
|--------------------|-----------------|-------------|
| **`functionName`** | Adres URL             | Nazwa funkcji programu Orchestrator do uruchomienia. |
| **`instanceId`**   | Adres URL             | Parametr opcjonalny. Identyfikator wystąpienia aranżacji. Jeśli nie zostanie określony, funkcja programu Orchestrator rozpocznie się od losowego identyfikatora wystąpienia. |
| **`{content}`**    | Żądaj zawartości | Opcjonalny. Dane wejściowe funkcji programu Orchestrator w formacie JSON. |

### <a name="response"></a>Reakcja

Można zwrócić kilka możliwych wartości kodu stanu.

* **HTTP 202 (zaakceptowane)**: zaplanowano uruchomienie określonej funkcji programu Orchestrator. `Location`Nagłówek odpowiedzi zawiera adres URL służący do sondowania stanu aranżacji.
* **HTTP 400 (złe żądanie)**: określona funkcja programu Orchestrator nie istnieje, określony identyfikator wystąpienia jest nieprawidłowy lub zawartość żądania nie była prawidłowym kodem JSON.

Poniżej przedstawiono przykładowe żądanie, które uruchamia funkcję programu `RestartVMs` Orchestrator i zawiera ładunek obiektu JSON:

```http
POST /runtime/webhooks/durabletask/orchestrators/RestartVMs?code=XXX
Content-Type: application/json
Content-Length: 83

{
    "resourceGroup": "myRG",
    "subscriptionId": "111deb5d-09df-4604-992e-a968345530a9"
}
```

Ładunek odpowiedzi dla przypadków **HTTP 202** jest obiektem JSON z następującymi polami:

| Pole                       | Opis                          |
|-----------------------------|--------------------------------------|
| **`id`**                    |Identyfikator wystąpienia aranżacji. |
| **`statusQueryGetUri`**     |Adres URL stanu wystąpienia aranżacji. |
| **`sendEventPostUri`**      |Adres URL "zgłoś zdarzenie" wystąpienia aranżacji. |
| **`terminatePostUri`**      |Adres URL "Przerwij" wystąpienia aranżacji. |
| **`purgeHistoryDeleteUri`** |Adres URL "Przeczyść historię" wystąpienia aranżacji. |
| **`rewindPostUri`**         |przeglądania Adres URL "przewijania do tyłu" wystąpienia aranżacji. |

Typ danych wszystkich pól to `string` .

Oto przykładowy ładunek odpowiedzi dla wystąpienia aranżacji z `abc123` identyfikatorem (sformatowanym pod kątem czytelności):

```http
{
    "id": "abc123",
    "purgeHistoryDeleteUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "sendEventPostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/raiseEvent/{eventName}?code=XXX",
    "statusQueryGetUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123?code=XXX",
    "terminatePostUri": "http://localhost:7071/runtime/webhooks/durabletask/instances/abc123/terminate?reason={text}&code=XXX"
}
```

Odpowiedź HTTP ma być zgodna ze *wzorcem dla odbiorców sondowania*. Zawiera również następujące istotne nagłówki odpowiedzi:

* **Lokalizacja**: adres URL punktu końcowego stanu. Ten adres URL zawiera tę samą wartość, co `statusQueryGetUri` pole.
* **Retry-After**: liczba sekund oczekiwania między operacjami sondowania. Wartość domyślna to `10`.

Aby uzyskać więcej informacji na temat asynchronicznego wzorca sondowania HTTP, zobacz dokumentację [śledzenia asynchronicznych operacji http](durable-functions-http-features.md#async-operation-tracking) .

## <a name="get-instance-status"></a>Pobierz stan wystąpienia

Pobiera stan określonego wystąpienia aranżacji.

### <a name="request"></a>Żądanie

W przypadku wersji 1. x środowiska uruchomieniowego usługi Functions żądanie jest sformatowane w następujący sposób (wiele wierszy jest widocznych dla przejrzystości):

```http
GET /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
    &returnInternalServerErrorOnFailure=[true|false]
```

W wersji 2. x środowiska uruchomieniowego funkcji format adresu URL ma wszystkie te same parametry, ale z nieco innym prefiksem:

```http
GET /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &showHistory=[true|false]
    &showHistoryOutput=[true|false]
    &showInput=[true|false]
    &returnInternalServerErrorOnFailure=[true|false]
```

Parametry żądania dla tego interfejsu API obejmują domyślnie wymieniony wcześniej zestaw, a także następujące unikatowe parametry:

| Pole                   | Typ parametru  | Opis |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | Adres URL             | Identyfikator wystąpienia aranżacji. |
| **`showInput`**         | Ciąg zapytania    | Parametr opcjonalny. W przypadku ustawienia wartości `false` dane wejściowe funkcji nie zostaną uwzględnione w ładunku odpowiedzi.|
| **`showHistory`**       | Ciąg zapytania    | Parametr opcjonalny. W przypadku wybrania `true` tej opcji historia wykonywania aranżacji zostanie uwzględniona w ładunku odpowiedzi.|
| **`showHistoryOutput`** | Ciąg zapytania    | Parametr opcjonalny. W przypadku ustawienia wartości `true` dane wyjściowe funkcji zostaną uwzględnione w historii wykonywania aranżacji.|
| **`createdTimeFrom`**   | Ciąg zapytania    | Parametr opcjonalny. Jeśli ta wartość jest określona, filtruje listę zwracanych wystąpień, które zostały utworzone w lub po danej sygnaturze czasowej ISO8601.|
| **`createdTimeTo`**     | Ciąg zapytania    | Parametr opcjonalny. Jeśli ta wartość jest określona, filtruje listę zwracanych wystąpień, które zostały utworzone w czasie lub przed daną sygnaturą czasową ISO8601.|
| **`runtimeStatus`**     | Ciąg zapytania    | Parametr opcjonalny. Jeśli ta wartość jest określona, filtruje listę zwracanych wystąpień na podstawie ich stanu czasu wykonywania. Aby wyświetlić listę możliwych wartości stanu środowiska uruchomieniowego, zobacz artykuł o [wystąpieniach zapytań](durable-functions-instance-management.md) . |
| **`returnInternalServerErrorOnFailure`**  | Ciąg zapytania    | Parametr opcjonalny. Jeśli ta wartość `true` jest ustawiona na, ten interfejs API zwróci odpowiedź HTTP 500 zamiast 200, jeśli wystąpienie jest w stanie niepowodzenia. Ten parametr jest przeznaczony dla scenariuszy automatycznej sondowania stanu. |

### <a name="response"></a>Reakcja

Można zwrócić kilka możliwych wartości kodu stanu.

* **HTTP 200 (ok)**: określone wystąpienie jest w stanie ukończone lub niepowodzenie.
* **HTTP 202 (zaakceptowane)**: określone wystąpienie jest w toku.
* **HTTP 400 (złe żądanie)**: określone wystąpienie nie powiodło się lub zostało przerwane.
* **HTTP 404 (nie znaleziono)**: określone wystąpienie nie istnieje lub nie zostało uruchomione.
* **HTTP 500 (wewnętrzny błąd serwera)**: zwracany tylko wtedy, gdy `returnInternalServerErrorOnFailure` jest ustawiona na `true` , a określone wystąpienie nie powiodło się z powodu nieobsługiwanego wyjątku.

Ładunek odpowiedzi dla przypadków **http 200** i **http 202** jest obiektem JSON z następującymi polami:

| Pole                 | Typ danych | Opis |
|-----------------------|-----------|-------------|
| **`runtimeStatus`**   | ciąg    | Stan środowiska uruchomieniowego wystąpienia. Wartości obejmują *Uruchamianie*, *oczekiwanie*, *Niepowodzenie*, *anulowane*, *zakończone*, *zakończone.* |
| **`input`**           | JSON      | Dane JSON używane do inicjowania wystąpienia. To pole jest `null` `showInput` ustawione na wartość `false` .|
| **`customStatus`**    | JSON      | Dane JSON używane dla niestandardowego stanu aranżacji. To pole jest `null` Nieskonfigurowane. |
| **`output`**          | JSON      | Dane wyjściowe JSON wystąpienia. To pole jest `null` , jeśli wystąpienie nie jest w stanie ukończone. |
| **`createdTime`**     | ciąg    | Godzina utworzenia wystąpienia. Używa rozszerzonej notacji ISO 8601. |
| **`lastUpdatedTime`** | ciąg    | Godzina, o której wystąpienie zostało ostatnio utrwalone. Używa rozszerzonej notacji ISO 8601. |
| **`historyEvents`**   | JSON      | Tablica JSON zawierająca historię wykonywania aranżacji. To pole jest `null` , chyba że `showHistory` parametr ciągu zapytania jest ustawiony na `true` . |

Oto przykładowy ładunek odpowiedzi, w tym historia wykonywania aranżacji i dane wyjściowe działań (sformatowane pod kątem czytelności):

```json
{
  "createdTime": "2018-02-28T05:18:49Z",
  "historyEvents": [
      {
          "EventType": "ExecutionStarted",
          "FunctionName": "E1_HelloSequence",
          "Timestamp": "2018-02-28T05:18:49.3452372Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Tokyo!",
          "ScheduledTime": "2018-02-28T05:18:51.3939873Z",
          "Timestamp": "2018-02-28T05:18:52.2895622Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello Seattle!",
          "ScheduledTime": "2018-02-28T05:18:52.8755705Z",
          "Timestamp": "2018-02-28T05:18:53.1765771Z"
      },
      {
          "EventType": "TaskCompleted",
          "FunctionName": "E1_SayHello",
          "Result": "Hello London!",
          "ScheduledTime": "2018-02-28T05:18:53.5170791Z",
          "Timestamp": "2018-02-28T05:18:53.891081Z"
      },
      {
          "EventType": "ExecutionCompleted",
          "OrchestrationStatus": "Completed",
          "Result": [
              "Hello Tokyo!",
              "Hello Seattle!",
              "Hello London!"
          ],
          "Timestamp": "2018-02-28T05:18:54.3660895Z"
      }
  ],
  "input": null,
  "customStatus": { "nextActions": ["A", "B", "C"], "foo": 2 },
  "lastUpdatedTime": "2018-02-28T05:18:54Z",
  "output": [
      "Hello Tokyo!",
      "Hello Seattle!",
      "Hello London!"
  ],
  "runtimeStatus": "Completed"
}
```

Odpowiedź **HTTP 202** zawiera również nagłówek odpowiedzi **lokalizacji** , który odwołuje się do tego samego adresu URL, co `statusQueryGetUri` pole wymienione wcześniej.

## <a name="get-all-instances-status"></a>Pobierz stan wszystkich wystąpień

Można także zbadać stan wszystkich wystąpień, usuwając `instanceId` z żądania "Pobierz stan wystąpienia". W takim przypadku parametry podstawowe są takie same, jak "Pobierz stan wystąpienia". Parametry ciągu zapytania do filtrowania są również obsługiwane.

Należy pamiętać, że są one `connection` `code` opcjonalne. Jeśli masz anonimowe uwierzytelnianie funkcji, `code` nie jest to wymagane.
Jeśli nie chcesz używać innych parametrów połączenia magazynu innego niż zdefiniowane w ustawieniu aplikacji AzureWebJobsStorage, możesz bezpiecznie zignorować parametr ciągu zapytania połączenia.

### <a name="request"></a>Żądanie

W przypadku wersji 1. x środowiska uruchomieniowego usługi Functions żądanie jest sformatowane w następujący sposób (wiele wierszy jest widocznych dla przejrzystości):

```http
GET /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

W wersji 2. x środowiska uruchomieniowego funkcji format adresu URL ma wszystkie te same parametry, ale z nieco innym prefiksem:

```http
GET /runtime/webhooks/durableTask/instances?
    taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
    &showInput=[true|false]
    &top={integer}
```

Parametry żądania dla tego interfejsu API obejmują domyślnie wymieniony wcześniej zestaw, a także następujące unikatowe parametry:

| Pole                   | Typ parametru  | Opis |
|-------------------------|-----------------|-------------|
| **`instanceId`**        | Adres URL             | Identyfikator wystąpienia aranżacji. |
| **`showInput`**         | Ciąg zapytania    | Parametr opcjonalny. W przypadku ustawienia wartości `false` dane wejściowe funkcji nie zostaną uwzględnione w ładunku odpowiedzi.|
| **`showHistory`**       | Ciąg zapytania    | Parametr opcjonalny. W przypadku wybrania `true` tej opcji historia wykonywania aranżacji zostanie uwzględniona w ładunku odpowiedzi.|
| **`showHistoryOutput`** | Ciąg zapytania    | Parametr opcjonalny. W przypadku ustawienia wartości `true` dane wyjściowe funkcji zostaną uwzględnione w historii wykonywania aranżacji.|
| **`createdTimeFrom`**   | Ciąg zapytania    | Parametr opcjonalny. Jeśli ta wartość jest określona, filtruje listę zwracanych wystąpień, które zostały utworzone w lub po danej sygnaturze czasowej ISO8601.|
| **`createdTimeTo`**     | Ciąg zapytania    | Parametr opcjonalny. Jeśli ta wartość jest określona, filtruje listę zwracanych wystąpień, które zostały utworzone w czasie lub przed daną sygnaturą czasową ISO8601.|
| **`runtimeStatus`**     | Ciąg zapytania    | Parametr opcjonalny. Jeśli ta wartość jest określona, filtruje listę zwracanych wystąpień na podstawie ich stanu czasu wykonywania. Aby wyświetlić listę możliwych wartości stanu środowiska uruchomieniowego, zobacz artykuł o [wystąpieniach zapytań](durable-functions-instance-management.md) . |
| **`top`**               | Ciąg zapytania    | Parametr opcjonalny. Gdy ta wartość jest określona, ogranicza liczbę wystąpień zwracanych przez zapytanie. |

### <a name="response"></a>Reakcja

Oto przykład ładunków odpowiedzi, w tym stanu aranżacji (sformatowany do czytelności):

```json
[
    {
        "instanceId": "7af46ff000564c65aafbfe99d07c32a5",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:39Z",
        "lastUpdatedTime": "2018-06-04T10:46:47Z"
    },
    {
        "instanceId": "80eb7dd5c22f4eeba9f42b062794321e",
        "runtimeStatus": "Running",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:28Z",
        "lastUpdatedTime": "2018-06-04T15:18:38Z"
    },
    {
        "instanceId": "9124518926db408ab8dfe84822aba2b1",
        "runtimeStatus": "Completed",
        "input": null,
        "customStatus": null,
        "output": [
            "Hello Tokyo!",
            "Hello Seattle!",
            "Hello London!"
        ],
        "createdTime": "2018-06-04T10:46:54Z",
        "lastUpdatedTime": "2018-06-04T10:47:03Z"
    },
    {
        "instanceId": "d100b90b903c4009ba1a90868331b11b",
        "runtimeStatus": "Pending",
        "input": null,
        "customStatus": null,
        "output": null,
        "createdTime": "2018-06-04T15:18:39Z",
        "lastUpdatedTime": "2018-06-04T15:18:39Z"
    }
]
```

> [!NOTE]
> Ta operacja może być bardzo kosztowna w odniesieniu do operacji we/wy usługi Azure Storage, jeśli w tabeli wystąpień istnieje wiele wierszy. Więcej szczegółów dotyczących tabeli wystąpień można znaleźć w dokumentacji dotyczącej [wydajności i skalowania w Durable Functions (Azure Functions)](durable-functions-perf-and-scale.md#instances-table) .
>

Jeśli istnieje więcej wyników, w nagłówku odpowiedzi zwracany jest token kontynuacji.  Nazwa nagłówka to `x-ms-continuation-token` .

Jeśli ustawisz wartość tokenu kontynuacji w następnym nagłówku żądania, możesz uzyskać następną stronę wyników. Ta nazwa nagłówka żądania jest również `x-ms-continuation-token` .

## <a name="purge-single-instance-history"></a>Przeczyść historię pojedynczego wystąpienia

Usuwa historię i powiązane artefakty dla określonego wystąpienia aranżacji.

### <a name="request"></a>Żądanie

W przypadku wersji 1. x środowiska uruchomieniowego usługi Functions żądanie jest sformatowane w następujący sposób (wiele wierszy jest widocznych dla przejrzystości):

```http
DELETE /admin/extensions/DurableTaskExtension/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

W wersji 2. x środowiska uruchomieniowego funkcji format adresu URL ma wszystkie te same parametry, ale z nieco innym prefiksem:

```http
DELETE /runtime/webhooks/durabletask/instances/{instanceId}
    ?taskHub={taskHub}
    &connection={connection}
    &code={systemKey}
```

Parametry żądania dla tego interfejsu API obejmują domyślnie wymieniony wcześniej zestaw, a także następujące unikatowe parametry:

| Pole             | Typ parametru  | Opis |
|-------------------|-----------------|-------------|
| **`instanceId`**  | Adres URL             | Identyfikator wystąpienia aranżacji. |

### <a name="response"></a>Reakcja

Można zwrócić następujące wartości kodów stanu HTTP.

* **HTTP 200 (ok)**: historia wystąpień została pomyślnie przeczyszczona.
* **HTTP 404 (nie znaleziono)**: określone wystąpienie nie istnieje.

Ładunek odpowiedzi dla przypadku **HTTP 200** jest obiektem JSON z następującym polem:

| Pole                  | Typ danych | Opis |
|------------------------|-----------|-------------|
| **`instancesDeleted`** | liczba całkowita   | Liczba usuniętych wystąpień. W przypadku pojedynczego wystąpienia, ta wartość powinna zawsze być `1` . |

Oto przykładowy ładunek odpowiedzi (sformatowany na potrzeby czytelności):

```json
{
    "instancesDeleted": 1
}
```

## <a name="purge-multiple-instance-histories"></a>Przeczyść historie z wieloma wystąpieniami

Można również usunąć historię i powiązane artefakty dla wielu wystąpień w centrum zadań, usuwając `{instanceId}` z żądania "Przeczyść historię pojedynczego wystąpienia". Aby selektywnie przeczyścić historię wystąpień, Użyj tych samych filtrów opisanych w żądaniu "Pobierz wszystkie wystąpienia stanu".

### <a name="request"></a>Żądanie

W przypadku wersji 1. x środowiska uruchomieniowego usługi Functions żądanie jest sformatowane w następujący sposób (wiele wierszy jest widocznych dla przejrzystości):

```http
DELETE /admin/extensions/DurableTaskExtension/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

W wersji 2. x środowiska uruchomieniowego funkcji format adresu URL ma wszystkie te same parametry, ale z nieco innym prefiksem:

```http
DELETE /runtime/webhooks/durabletask/instances
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &createdTimeFrom={timestamp}
    &createdTimeTo={timestamp}
    &runtimeStatus={runtimeStatus1,runtimeStatus2,...}
```

Parametry żądania dla tego interfejsu API obejmują domyślnie wymieniony wcześniej zestaw, a także następujące unikatowe parametry:

| Pole                 | Typ parametru  | Opis |
|-----------------------|-----------------|-------------|
| **`createdTimeFrom`** | Ciąg zapytania    | Filtruje listę przeczyszczonych wystąpień, które zostały utworzone w czasie lub po danej sygnaturze czasowej ISO8601.|
| **`createdTimeTo`**   | Ciąg zapytania    | Parametr opcjonalny. Jeśli ta wartość jest określona, filtruje listę przeczyszczonych wystąpień, które zostały utworzone w czasie lub przed daną sygnaturą czasową ISO8601.|
| **`runtimeStatus`**   | Ciąg zapytania    | Parametr opcjonalny. Jeśli ta wartość jest określona, program filtruje listę przeczyszczonych wystąpień na podstawie ich stanu czasu wykonywania. Aby wyświetlić listę możliwych wartości stanu środowiska uruchomieniowego, zobacz artykuł o [wystąpieniach zapytań](durable-functions-instance-management.md) . |

> [!NOTE]
> Ta operacja może być bardzo kosztowna w odniesieniu do operacji we/wy usługi Azure Storage, jeśli w tabelach wystąpień i/lub historia występuje wiele wierszy. Więcej informacji o tych tabelach można znaleźć w dokumentacji dotyczącej [wydajności i skalowania w Durable Functions (Azure Functions)](durable-functions-perf-and-scale.md#instances-table) .

### <a name="response"></a>Reakcja

Można zwrócić następujące wartości kodów stanu HTTP.

* **HTTP 200 (ok)**: historia wystąpień została pomyślnie przeczyszczona.
* **HTTP 404 (nie znaleziono)**: nie znaleziono wystąpień pasujących do wyrażenia filtru.

Ładunek odpowiedzi dla przypadku **HTTP 200** jest obiektem JSON z następującym polem:

| Pole                   | Typ danych | Opis |
|-------------------------|-----------|-------------|
| **`instancesDeleted`**  | liczba całkowita   | Liczba usuniętych wystąpień. |

Oto przykładowy ładunek odpowiedzi (sformatowany na potrzeby czytelności):

```json
{
    "instancesDeleted": 250
}
```

## <a name="raise-event"></a>Zgłoś zdarzenie

Wysyła komunikat z powiadomieniem o zdarzeniu do uruchomionego wystąpienia aranżacji.

### <a name="request"></a>Żądanie

W przypadku wersji 1. x środowiska uruchomieniowego usługi Functions żądanie jest sformatowane w następujący sposób (wiele wierszy jest widocznych dla przejrzystości):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

W wersji 2. x środowiska uruchomieniowego funkcji format adresu URL ma wszystkie te same parametry, ale z nieco innym prefiksem:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/{eventName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

Parametry żądania dla tego interfejsu API obejmują domyślnie wymieniony wcześniej zestaw, a także następujące unikatowe parametry:

| Pole             | Typ parametru  | Opis |
|-------------------|-----------------|-------------|
| **`instanceId`**  | Adres URL             | Identyfikator wystąpienia aranżacji. |
| **`eventName`**   | Adres URL             | Nazwa zdarzenia, na które oczekuje wystąpienie aranżacji docelowej. |
| **`{content}`**   | Żądaj zawartości | Ładunek zdarzenia w formacie JSON. |

### <a name="response"></a>Reakcja

Można zwrócić kilka możliwych wartości kodu stanu.

* **HTTP 202 (zaakceptowane)**: zgłoszone zdarzenie zostało zaakceptowane do przetwarzania.
* **HTTP 400 (Nieprawidłowe żądanie)**: zawartość żądania nie jest typu `application/json` lub nie jest prawidłową notacją JSON.
* **HTTP 404 (nie znaleziono)**: określone wystąpienie nie zostało odnalezione.
* **HTTP 410 (usunięty)**: określone wystąpienie zostało ukończone lub zakończyło się niepowodzeniem i nie może przetworzyć żadnych zgłoszonych zdarzeń.

Oto przykładowe żądanie, które wysyła ciąg JSON `"incr"` do wystąpienia oczekującego na zdarzenie o nazwie **Operation**:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/raiseEvent/operation?taskHub=DurableFunctionsHub&connection=Storage&code=XXX
Content-Type: application/json
Content-Length: 6

"incr"
```

Odpowiedzi dla tego interfejsu API nie zawierają żadnej zawartości.

## <a name="terminate-instance"></a>Przerwij wystąpienie

Kończy uruchomione wystąpienie aranżacji.

### <a name="request"></a>Żądanie

W przypadku wersji 1. x środowiska uruchomieniowego usługi Functions żądanie jest sformatowane w następujący sposób (wiele wierszy jest widocznych dla przejrzystości):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

W wersji 2. x środowiska uruchomieniowego funkcji format adresu URL ma wszystkie te same parametry, ale z nieco innym prefiksem:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/terminate
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Parametry żądania dla tego interfejsu API zawierają zestaw domyślny opisany wcześniej, a także następujący unikatowy parametr.

| Pole             | Typ parametru  | Opis |
|-------------------|-----------------|-------------|
| **`instanceId`**  | Adres URL             | Identyfikator wystąpienia aranżacji. |
| **`reason`**      | Ciąg zapytania    | Opcjonalny. Przyczyna zakończenia wystąpienia aranżacji. |

### <a name="response"></a>Reakcja

Można zwrócić kilka możliwych wartości kodu stanu.

* **HTTP 202 (zaakceptowane)**: żądanie zakończenia zostało zaakceptowane do przetwarzania.
* **HTTP 404 (nie znaleziono)**: określone wystąpienie nie zostało odnalezione.
* **HTTP 410 (usunięty)**: określone wystąpienie zostało ukończone lub zakończyło się niepowodzeniem.

Oto przykładowe żądanie kończące uruchomione wystąpienie i określające powód **debugowania**:

```
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/terminate?reason=buggy&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Odpowiedzi dla tego interfejsu API nie zawierają żadnej zawartości.

## <a name="rewind-instance-preview"></a>Przewiń do tyłu (wersja zapoznawcza)

Przywraca niepowodzenie wystąpienia aranżacji do stanu uruchomienia przez odtwarzanie najnowszych operacji zakończonych niepowodzeniem.

### <a name="request"></a>Żądanie

W przypadku wersji 1. x środowiska uruchomieniowego usługi Functions żądanie jest sformatowane w następujący sposób (wiele wierszy jest widocznych dla przejrzystości):

```http
POST /admin/extensions/DurableTaskExtension/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

W wersji 2. x środowiska uruchomieniowego funkcji format adresu URL ma wszystkie te same parametry, ale z nieco innym prefiksem:

```http
POST /runtime/webhooks/durabletask/instances/{instanceId}/rewind
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &reason={text}
```

Parametry żądania dla tego interfejsu API zawierają zestaw domyślny opisany wcześniej, a także następujący unikatowy parametr.

| Pole             | Typ parametru  | Opis |
|-------------------|-----------------|-------------|
| **`instanceId`**  | Adres URL             | Identyfikator wystąpienia aranżacji. |
| **`reason`**      | Ciąg zapytania    | Opcjonalny. Przyczyna odwinięcia wystąpienia aranżacji. |

### <a name="response"></a>Reakcja

Można zwrócić kilka możliwych wartości kodu stanu.

* **HTTP 202 (zaakceptowane)**: żądanie przewijania do tyłu zostało zaakceptowane do przetwarzania.
* **HTTP 404 (nie znaleziono)**: określone wystąpienie nie zostało odnalezione.
* **HTTP 410 (usunięty)**: określone wystąpienie zostało zakończone lub zostało zakończone.

Oto przykładowe żądanie, które powoduje odwracanie wystąpienia zakończonego niepowodzeniem i określa przyczynę **naprawienia**:

```http
POST /admin/extensions/DurableTaskExtension/instances/bcf6fb5067b046fbb021b52ba7deae5a/rewind?reason=fixed&taskHub=DurableFunctionsHub&connection=Storage&code=XXX
```

Odpowiedzi dla tego interfejsu API nie zawierają żadnej zawartości.

## <a name="signal-entity"></a>Jednostka sygnału

Wysyła jednokierunkową wiadomość operacji do [jednostki trwałej](durable-functions-types-features-overview.md#entity-functions). Jeśli jednostka nie istnieje, zostanie utworzona automatycznie.

> [!NOTE]
> Trwałe jednostki są dostępne w Durable Functions 2,0.

### <a name="request"></a>Żądanie

Żądanie HTTP jest sformatowane w następujący sposób (wiele wierszy jest widocznych dla przejrzystości):

```http
POST /runtime/webhooks/durabletask/entities/{entityName}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &op={operationName}
```

Parametry żądania dla tego interfejsu API obejmują domyślnie wymieniony wcześniej zestaw, a także następujące unikatowe parametry:

| Pole             | Typ parametru  | Opis |
|-------------------|-----------------|-------------|
| **`entityName`**  | Adres URL             | Nazwa (typ) jednostki. |
| **`entityKey`**   | Adres URL             | Klucz (unikatowy identyfikator) jednostki. |
| **`op`**          | Ciąg zapytania    | Opcjonalny. Nazwa operacji zdefiniowanej przez użytkownika do wywołania. |
| **`{content}`**   | Żądaj zawartości | Ładunek zdarzenia w formacie JSON. |

Oto przykładowe żądanie, które wysyła zdefiniowany przez użytkownika komunikat "Dodaj" do `Counter` jednostki o nazwie `steps` . Zawartość wiadomości jest wartością `5` . Jeśli jednostka jeszcze nie istnieje, zostanie utworzona przez to żądanie:

```http
POST /runtime/webhooks/durabletask/entities/Counter/steps?op=Add
Content-Type: application/json

5
```

> [!NOTE]
> Domyślnie w przypadku [jednostek opartych na klasie w programie .NET](durable-functions-dotnet-entities.md#defining-entity-classes)określenie `op` wartości `delete` spowoduje usunięcie stanu jednostki. Jeśli jednostka definiuje operację o nazwie `delete` , jednak zostanie wywołana operacja zdefiniowana przez użytkownika.

### <a name="response"></a>Reakcja

Ta operacja ma kilka możliwych odpowiedzi:

* **HTTP 202 (zaakceptowane)**: operacja sygnału została zaakceptowana do przetwarzania asynchronicznego.
* **HTTP 400 (Nieprawidłowe żądanie)**: zawartość żądania nie jest typu, nie `application/json` jest prawidłowym kodem JSON lub ma nieprawidłową `entityKey` wartość.
* **HTTP 404 (nie znaleziono)**: `entityName` nie znaleziono określonego.

Pomyślne żądanie HTTP nie zawiera żadnej zawartości odpowiedzi. Żądanie HTTP, które nie powiodło się, może zawierać informacje o błędzie w formacie JSON w treści odpowiedzi.

## <a name="get-entity"></a>Pobierz jednostkę

Pobiera stan określonej jednostki.

### <a name="request"></a>Żądanie

Żądanie HTTP jest sformatowane w następujący sposób (wiele wierszy jest widocznych dla przejrzystości):

```http
GET /runtime/webhooks/durabletask/entities/{entityName}/{entityKey}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
```

### <a name="response"></a>Reakcja

Ta operacja ma dwie możliwe odpowiedzi:

* **HTTP 200 (ok)**: określona jednostka istnieje.
* **HTTP 404 (nie znaleziono)**: nie znaleziono określonej jednostki.

Pomyślna odpowiedź zawiera stan Zserializowany przez kod JSON jednostki jako jego zawartość.

### <a name="example"></a>Przykład
Następujące przykładowe żądanie HTTP Pobiera stan istniejącej `Counter` jednostki o nazwie `steps` :

```http
GET /runtime/webhooks/durabletask/entities/Counter/steps
```

Jeśli `Counter` Jednostka zawiera po prostu kilka kroków zapisanych w `currentValue` polu, zawartość odpowiedzi może wyglądać podobnie do następującej (sformatowanej pod kątem czytelności):

```json
{
    "currentValue": 5
}
```

## <a name="list-entities"></a>Wyświetlanie listy jednostek

Można wykonywać zapytania dotyczące wielu jednostek według nazwy jednostki lub daty ostatniej operacji.

### <a name="request"></a>Żądanie

Żądanie HTTP jest sformatowane w następujący sposób (wiele wierszy jest widocznych dla przejrzystości):

```http
GET /runtime/webhooks/durabletask/entities/{entityName}
    ?taskHub={taskHub}
    &connection={connectionName}
    &code={systemKey}
    &lastOperationTimeFrom={timestamp}
    &lastOperationTimeTo={timestamp}
    &fetchState=[true|false]
    &top={integer}
```

Parametry żądania dla tego interfejsu API obejmują domyślnie wymieniony wcześniej zestaw, a także następujące unikatowe parametry:

| Pole                       | Typ parametru  | Opis |
|-----------------------------|-----------------|-------------|
| **`entityName`**            | Adres URL             | Opcjonalny. Jeśli ta wartość jest określona, filtruje listę zwracanych jednostek według nazwy jednostki (bez uwzględniania wielkości liter). |
| **`fetchState`**            | Ciąg zapytania    | Parametr opcjonalny. Jeśli jest ustawiona na `true` , stan jednostki zostanie uwzględniony w ładunku odpowiedzi. |
| **`lastOperationTimeFrom`** | Ciąg zapytania    | Parametr opcjonalny. Jeśli ta wartość jest określona, filtruje listę zwracanych jednostek, które przetworzyły operacje po podanej sygnaturze czasowej ISO8601. |
| **`lastOperationTimeTo`**   | Ciąg zapytania    | Parametr opcjonalny. Jeśli ta wartość jest określona, filtruje listę zwracanych jednostek, które przetworzyły operacje przed daną sygnaturą czasową ISO8601. |
| **`top`**                   | Ciąg zapytania    | Parametr opcjonalny. Gdy ta wartość jest określona, ogranicza liczbę jednostek zwracanych przez zapytanie. |


### <a name="response"></a>Reakcja

Pomyślnie odpowiedź HTTP 200 zawiera serializowaną w notacji JSON tablicę jednostek i opcjonalnie stan każdej jednostki.

Domyślnie operacja zwraca pierwsze 100 jednostek, które pasują do kryteriów zapytania. Obiekt wywołujący może określić wartość parametru ciągu zapytania, `top` aby zwracać inną maksymalną liczbę wyników. Jeśli więcej wyników istnieje poza zwracanymi informacjami, w nagłówku odpowiedzi jest również zwracany token kontynuacji. Nazwa nagłówka to `x-ms-continuation-token` .

Jeśli ustawisz wartość tokenu kontynuacji w następnym nagłówku żądania, możesz uzyskać następną stronę wyników. Ta nazwa nagłówka żądania jest również `x-ms-continuation-token` .

### <a name="example---list-all-entities"></a>Przykład — Wyświetl listę wszystkich jednostek

Następujące przykładowe żądanie HTTP wyświetla listę wszystkich jednostek w centrum zadań:

```http
GET /runtime/webhooks/durabletask/entities
```

Plik JSON odpowiedzi może wyglądać podobnie do poniższego (sformatowanego pod kątem czytelności):

```json
[
    {
        "entityId": { "key": "cats", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:45:44.6326361Z",
    },
    {
        "entityId": { "key": "dogs", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:01.9477382Z"
    },
    {
        "entityId": { "key": "mice", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:15.4626159Z"
    },
    {
        "entityId": { "key": "radio", "name": "device" },
        "lastOperationTime": "2019-12-18T21:46:18.2616154Z"
    },
]
```

### <a name="example---filtering-the-list-of-entities"></a>Przykład — filtrowanie listy jednostek

Poniższe przykładowe żądanie HTTP wyświetla tylko dwie pierwsze jednostki typu `counter` , a także pobiera ich stan:

```http
GET /runtime/webhooks/durabletask/entities/counter?top=2&fetchState=true
```

Plik JSON odpowiedzi może wyglądać podobnie do poniższego (sformatowanego pod kątem czytelności):

```json
[
    {
        "entityId": { "key": "cats", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:45:44.6326361Z",
        "state": { "value": 9 }
    },
    {
        "entityId": { "key": "dogs", "name": "counter" },
        "lastOperationTime": "2019-12-18T21:46:01.9477382Z",
        "state": { "value": 10 }
    }
]
```

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się, jak używać Application Insights do monitorowania funkcji trwałych](durable-functions-diagnostics.md)
