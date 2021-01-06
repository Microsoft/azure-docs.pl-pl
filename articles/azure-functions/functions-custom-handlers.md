---
title: Azure Functions niestandardowe programy obsługi
description: Dowiedz się, jak używać Azure Functions z dowolnym językiem lub wersją środowiska uruchomieniowego.
author: anthonychu
ms.author: antchu
ms.date: 12/1/2020
ms.topic: article
ms.openlocfilehash: f527b387afc01eb60bd582adc13a4ad3d516055b
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/06/2021
ms.locfileid: "97936995"
---
# <a name="azure-functions-custom-handlers"></a>Azure Functions niestandardowe programy obsługi

Każda aplikacja funkcji jest wykonywana przez program obsługi specyficzny dla języka. Mimo że Azure Functions obsługuje wiele [programów obsługi języka](./supported-languages.md) domyślnie, istnieją przypadki, w których można chcieć użyć innych języków lub środowisk uruchomieniowych.

Niestandardowe programy obsługi to lekkie serwery sieci Web, które odbierają zdarzenia z hosta usługi Functions. Każdy język, który obsługuje elementy podstawowe HTTP, może zaimplementować obsługę niestandardową.

Niestandardowe programy obsługi są najlepiej dostosowane do sytuacji, w których chcesz:

- Zaimplementuj aplikację funkcji w języku, który nie jest obecnie obsługiwany, na przykład go lub Rust.
- Zaimplementuj aplikację funkcji w środowisku uruchomieniowym, która nie jest obecnie obsługiwana, na przykład DENO.

W przypadku obsługi niestandardowych można używać [wyzwalaczy i powiązań wejściowych i wyjściowych](./functions-triggers-bindings.md) za pośrednictwem [pakietów rozszerzeń](./functions-bindings-register.md).

Rozpocznij pracę z Azure Functions obsługami niestandardowymi, korzystając z [przewodników Szybki Start i Rust](create-first-function-vs-code-other.md).

## <a name="overview"></a>Omówienie

Na poniższym diagramie przedstawiono relację między hostem funkcji a serwerem sieci Web zaimplementowaną jako procedura obsługi niestandardowej.

![Przegląd niestandardowego programu obsługi Azure Functions](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

1. Każde zdarzenie wyzwala żądanie wysyłane do hosta funkcji. Zdarzenie jest dowolnym wyzwalaczem, który jest obsługiwany przez Azure Functions.
1. Host funkcji wysyła następnie [ładunek żądania](#request-payload) do serwera sieci Web. Ładunek przechowuje wyzwalacz i dane wejściowe powiązania oraz inne metadane dla funkcji.
1. Serwer sieci Web wykonuje pojedynczą funkcję i zwraca [ładunek odpowiedzi](#response-payload) do hosta funkcji.
1. Host usługi Functions przekazuje dane z odpowiedzi na powiązania wyjściowe funkcji w celu przetworzenia.

Aplikacja Azure Functions zaimplementowana jako procedura obsługi niestandardowej musi skonfigurować *host.js*, *local.settings.jsna* i *function.js* plików zgodnie z kilkoma konwencjami.

## <a name="application-structure"></a>Struktura aplikacji

Aby zaimplementować obsługę niestandardową, potrzebne są następujące aspekty aplikacji:

- *host.jsw* pliku w katalogu głównym aplikacji
- *local.settings.jsw* pliku w katalogu głównym aplikacji
- *function.jsw* pliku dla każdej funkcji (wewnątrz folderu, który jest zgodny z nazwą funkcji)
- Polecenie, skrypt lub plik wykonywalny, który uruchamia serwer sieci Web

Na poniższym diagramie przedstawiono, jak te pliki wyglądają w systemie plików dla funkcji o nazwie "MyQueueFunction" i pliku wykonywalnego programu obsługi o nazwie *handler.exe*.

```bash
| /MyQueueFunction
|   function.json
|
| host.json
| local.settings.json
| handler.exe
```

### <a name="configuration"></a>Konfigurowanie

Aplikacja jest konfigurowana za pomocą *host.js* i *local.settings.jsna* plikach.

#### <a name="hostjson"></a>host.jsna

*host.json* informuje hosta funkcji, gdzie wysyłać żądania, wskazując na serwer sieci Web, który może przetwarzać zdarzenia http.

Niestandardowa procedura obsługi jest definiowana przez skonfigurowanie *host.jsw* pliku ze szczegółowymi informacjami na temat sposobu uruchamiania serwera sieci Web za pośrednictwem `customHandler` sekcji.

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  }
}
```

`customHandler`Sekcja wskazuje element docelowy określony przez `defaultExecutablePath` . Obiektem docelowym wykonywania może być polecenie, plik wykonywalny lub plik, w którym jest zaimplementowany serwer sieci Web.

Użyj `arguments` tablicy, aby przekazać wszelkie argumenty do pliku wykonywalnego. Argumenty obsługują rozszerzanie zmiennych środowiskowych (ustawień aplikacji) przy użyciu `%%` notacji.

Możesz również zmienić katalog roboczy używany przez plik wykonywalny za pomocą programu `workingDirectory` .

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "app/handler.exe",
      "arguments": [
        "--database-connection-string",
        "%DATABASE_CONNECTION_STRING%"
      ],
      "workingDirectory": "app"
    }
  }
}
```

##### <a name="bindings-support"></a>Obsługa powiązań

Standardowe wyzwalacze wraz z powiązaniami wejściowymi i wyjściowymi są dostępne przez odwoływanie się do [pakietów rozszerzeń](./functions-bindings-register.md) w *host.js* pliku.

#### <a name="localsettingsjson"></a>local.settings.json

*local.settings.json* definiuje ustawienia aplikacji używane podczas lokalnego uruchamiania aplikacji funkcji. Ponieważ może zawierać wpisy tajne, *local.settings.json* powinien być wykluczony z kontroli źródła. Na platformie Azure zamiast tego użyj ustawień aplikacji.

W przypadku niestandardowych programów obsługi Ustaw `FUNCTIONS_WORKER_RUNTIME` wartość `Custom` w *local.settings.jsna*.

```json
{
  "IsEncrypted": false,
  "Values": {
    "FUNCTIONS_WORKER_RUNTIME": "Custom"
  }
}
```

> [!NOTE]
> `Custom` mogą nie zostać rozpoznane jako prawidłowe środowisko uruchomieniowe w planach Premium lub App Service systemu Linux. Jeśli jest to element docelowy wdrożenia, ustaw `FUNCTIONS_WORKER_RUNTIME` na pusty ciąg.

### <a name="function-metadata"></a>Metadane funkcji

W przypadku użycia z niestandardową obsługą *function.jsna* zawartości nie różnią się od sposobu definiowania funkcji w dowolnym innym kontekście. Jedyny wymóg polega na tym, że *function.js* plików musi znajdować się w folderze o nazwie, aby odpowiadał nazwie funkcji.

Poniższy *function.json* konfiguruje funkcję, która ma wyzwalacz kolejki i powiązanie danych wyjściowych kolejki. Ponieważ znajduje się w folderze o nazwie *MyQueueFunction*, definiuje funkcję o nazwie *MyQueueFunction*.

**MyQueueFunction/function.jswłączone**

```json
{
  "bindings": [
    {
      "name": "myQueueItem",
      "type": "queueTrigger",
      "direction": "in",
      "queueName": "messages-incoming",
      "connection": "AzureWebJobsStorage"
    },
    {
      "name": "$return",
      "type": "queue",
      "direction": "out",
      "queueName": "messages-outgoing",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

### <a name="request-payload"></a>Ładunek żądania

Po odebraniu komunikatu w kolejce host funkcji wysyła żądanie HTTP POST do niestandardowego programu obsługi z ładunkiem w treści.

Poniższy kod przedstawia przykładowy ładunek żądania. Ładunek zawiera strukturę JSON z dwoma elementami członkowskimi: `Data` i `Metadata` .

`Data`Element członkowski zawiera klucze, które pasują do nazw wejściowych i wyzwalaczy, zgodnie z definicją w tablicy powiązań w *function.jsna* pliku.

`Metadata`Element członkowski zawiera [metadane wygenerowane ze źródła zdarzeń](./functions-bindings-expressions-patterns.md#trigger-metadata).

```json
{
  "Data": {
    "myQueueItem": "{ message: \"Message sent\" }"
  },
  "Metadata": {
    "DequeueCount": 1,
    "ExpirationTime": "2019-10-16T17:58:31+00:00",
    "Id": "800ae4b3-bdd2-4c08-badd-f08e5a34b865",
    "InsertionTime": "2019-10-09T17:58:31+00:00",
    "NextVisibleTime": "2019-10-09T18:08:32+00:00",
    "PopReceipt": "AgAAAAMAAAAAAAAAAgtnj8x+1QE=",
    "sys": {
      "MethodName": "QueueTrigger",
      "UtcNow": "2019-10-09T17:58:32.2205399Z",
      "RandGuid": "24ad4c06-24ad-4e5b-8294-3da9714877e9"
    }
  }
}
```

### <a name="response-payload"></a>Ładunek odpowiedzi

Zgodnie z Konwencją, odpowiedzi funkcji są formatowane jako pary klucz/wartość. Obsługiwane są następujące klucze:

| <nobr>Klucz ładunku</nobr>   | Typ danych | Uwagi                                                      |
| ------------- | --------- | ------------------------------------------------------------ |
| `Outputs`     | object    | Przechowuje wartości odpowiedzi zdefiniowane przez `bindings` tablicę w *function.js*.<br /><br />Na przykład jeśli funkcja jest skonfigurowana za pomocą powiązania danych wyjściowych kolejki o nazwie "myQueueOutput", następnie `Outputs` zawiera klucz o nazwie `myQueueOutput` , który jest ustawiany przez niestandardową obsługę komunikatów wysyłanych do kolejki. |
| `Logs`        | array     | Komunikaty są wyświetlane w dziennikach wywołania funkcji.<br /><br />W przypadku uruchamiania na platformie Azure komunikaty są wyświetlane w Application Insights. |
| `ReturnValue` | ciąg    | Służy do dostarczania odpowiedzi w przypadku skonfigurowania danych wyjściowych, jak `$return` w *function.js* pliku. |

Jest to przykład ładunku odpowiedzi.

```json
{
  "Outputs": {
    "res": {
      "body": "Message enqueued"
    },
    "myQueueOutput": [
      "queue message 1",
      "queue message 2"
    ]
  },
  "Logs": [
    "Log message 1",
    "Log message 2"
  ],
  "ReturnValue": "{\"hello\":\"world\"}"
}
```

## <a name="examples"></a>Przykłady

Niestandardowe programy obsługi można zaimplementować w dowolnym języku, który obsługuje otrzymywanie zdarzeń HTTP. W poniższych przykładach pokazano, jak zaimplementować procedurę obsługi niestandardowej przy użyciu języka programowania go.

### <a name="function-with-bindings"></a>Funkcja z powiązaniami

Scenariusz zaimplementowany w tym przykładzie cechuje funkcję o nazwie `order` , która akceptuje element `POST` z ładunkiem reprezentującym zamówienie produktu. W przypadku opublikowania zamówienia w funkcji zostanie utworzony komunikat Queue Storage i zostanie zwrócona odpowiedź HTTP.

<a id="bindings-implementation" name="bindings-implementation"></a>

#### <a name="implementation"></a>Implementacja

W folderze o nazwie *order* *function.jsw* pliku konfiguruje funkcję wyzwalaną przez protokół http.

**kolejność/function.jsna**

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": ["post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    },
    {
      "type": "queue",
      "name": "message",
      "direction": "out",
      "queueName": "orders",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```

Ta funkcja jest definiowana jako [Funkcja wyzwalana przez protokół http](./functions-bindings-http-webhook-trigger.md) , która zwraca [odpowiedź HTTP](./functions-bindings-http-webhook-output.md) i wyprowadza komunikat [magazynu kolejki](./functions-bindings-storage-queue-output.md) .

W katalogu głównym aplikacji *host.jsw* pliku jest skonfigurowany do uruchamiania pliku wykonywalnego o nazwie `handler.exe` ( `handler` w systemie Linux lub macOS).

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  },
  "extensionBundle": {
    "id": "Microsoft.Azure.Functions.ExtensionBundle",
    "version": "[1.*, 2.0.0)"
  }
}
```

Jest to żądanie HTTP wysyłane do środowiska uruchomieniowego Functions.

```http
POST http://127.0.0.1:7071/api/order HTTP/1.1
Content-Type: application/json

{
  "id": 1005,
  "quantity": 2,
  "color": "black"
}
```

Środowisko uruchomieniowe funkcji wyśle następnie następujące żądanie HTTP do niestandardowej procedury obsługi:

```http
POST http://127.0.0.1:<FUNCTIONS_CUSTOMHANDLER_PORT>/order HTTP/1.1
Content-Type: application/json

{
  "Data": {
    "req": {
      "Url": "http://localhost:7071/api/order",
      "Method": "POST",
      "Query": "{}",
      "Headers": {
        "Content-Type": [
          "application/json"
        ]
      },
      "Params": {},
      "Body": "{\"id\":1005,\"quantity\":2,\"color\":\"black\"}"
    }
  },
  "Metadata": {
  }
}
```

> [!NOTE]
> Niektóre fragmenty ładunku zostały usunięte z zwięzłości.

*handler.exe* to skompilowany program obsługi niestandardowej języka go, który uruchamia serwer sieci Web i reaguje na żądania wywołania funkcji z hosta funkcji.

```go
package main

import (
    "encoding/json"
    "fmt"
    "log"
    "net/http"
    "os"
)

type InvokeRequest struct {
    Data     map[string]json.RawMessage
    Metadata map[string]interface{}
}

type InvokeResponse struct {
    Outputs     map[string]interface{}
    Logs        []string
    ReturnValue interface{}
}

func orderHandler(w http.ResponseWriter, r *http.Request) {
    var invokeRequest InvokeRequest

    d := json.NewDecoder(r.Body)
    d.Decode(&invokeRequest)

    var reqData map[string]interface{}
    json.Unmarshal(invokeRequest.Data["req"], &reqData)

    outputs := make(map[string]interface{})
    outputs["message"] = reqData["Body"]

    resData := make(map[string]interface{})
    resData["body"] = "Order enqueued"
    outputs["res"] = resData
    invokeResponse := InvokeResponse{outputs, nil, nil}

    responseJson, _ := json.Marshal(invokeResponse)

    w.Header().Set("Content-Type", "application/json")
    w.Write(responseJson)
}

func main() {
    customHandlerPort, exists := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT")
    if !exists {
        customHandlerPort = "8080"
    }
    mux := http.NewServeMux()
    mux.HandleFunc("/order", orderHandler)
    fmt.Println("Go server Listening on: ", customHandlerPort)
    log.Fatal(http.ListenAndServe(":"+customHandlerPort, mux))
}
```

W tym przykładzie niestandardowa procedura obsługi uruchamia serwer sieci Web do obsługi zdarzeń HTTP i jest skonfigurowany do nasłuchiwania żądań za pośrednictwem `FUNCTIONS_CUSTOMHANDLER_PORT` .

Mimo że host usługi Functions otrzymał oryginalne żądanie HTTP o `/api/order` , wywołuje procedurę obsługi niestandardowej przy użyciu nazwy funkcji (nazwy folderu). W tym przykładzie funkcja jest zdefiniowana w ścieżce `/order` . Host wysyła żądanie HTTP w ścieżce do programu obsługi niestandardowej `/order` .

Jak `POST` żądania są wysyłane do tej funkcji, metadane danych i funkcji wyzwalacza są dostępne za pośrednictwem treści żądania HTTP. Do oryginalnej treści żądania HTTP można uzyskać dostęp w ładunku `Data.req.Body` .

Odpowiedź funkcji jest formatowana jako pary klucz/wartość, gdzie `Outputs` składowa zawiera wartość JSON, gdzie klucze pasują do danych wyjściowych, zgodnie z definicją w *function.js* pliku.

Jest to przykładowy ładunek, który jest zwracany przez tę procedurę obsługi do hosta funkcji.

```json
{
  "Outputs": {
    "message": "{\"id\":1005,\"quantity\":2,\"color\":\"black\"}",
    "res": {
      "body": "Order enqueued"
    }
  },
  "Logs": null,
  "ReturnValue": null
}
```

Przez ustawienie danych `message` wyjściowych równych dane zamówienia, które pochodzą z żądania, funkcja wyprowadza dane zamówienia do skonfigurowanej kolejki. Host funkcji zwraca również odpowiedź HTTP skonfigurowaną w obiekcie `res` wywołującym.

### <a name="http-only-function"></a>Funkcja tylko HTTP

W przypadku funkcji wyzwalanych przez protokół HTTP bez dodatkowych powiązań i danych wyjściowych może być konieczne, aby program obsługi pracował bezpośrednio z żądaniem HTTP i odpowiedzią, a nie z niestandardowymi [żądaniami](#request-payload) obsługi i ładunkiem [odpowiedzi](#response-payload) . To zachowanie można skonfigurować w *host.jsna* korzystanie z tego `enableForwardingHttpRequest` Ustawienia.

> [!IMPORTANT]
> Głównym celem funkcji obsługi niestandardowych jest włączenie języków i środowisk uruchomieniowych, które nie mają obecnie wsparcia dla pierwszej klasy w Azure Functions. Chociaż może być możliwe uruchamianie aplikacji sieci Web przy użyciu niestandardowych programów obsługi, Azure Functions nie jest standardowym zwrotnym serwerem proxy. Niektóre funkcje, takie jak przesyłanie strumieniowe odpowiedzi, HTTP/2 i WebSockets, są niedostępne. Niektóre składniki żądania HTTP, takie jak określone nagłówki i trasy, mogą być ograniczone. Aplikacja może również napotkać nadmierne [zimne uruchomienie](event-driven-scaling.md#cold-start).
>
> Aby rozwiązać te okoliczności, Rozważ uruchomienie aplikacji sieci Web na [Azure App Service](../app-service/overview.md).

Poniższy przykład ilustruje sposób konfigurowania funkcji wyzwalanej przez protokół HTTP bez dodatkowych powiązań ani danych wyjściowych. Scenariusz zaimplementowany w tym przykładzie cechuje funkcję o nazwie `hello` , która akceptuje `GET` lub `POST` .

<a id="hello-implementation" name="hello-implementation"></a>

#### <a name="implementation"></a>Implementacja

W folderze o nazwie *hello* *function.jsw* pliku konfiguruje funkcję wyzwalaną przez protokół http.

**Witaj/function.jsna**

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "anonymous",
      "direction": "in",
      "name": "req",
      "methods": ["get", "post"]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "res"
    }
  ]
}
```

Funkcja jest skonfigurowana tak, aby akceptowała zarówno `GET` `POST` żądania, jak i wartość wynik, jest podawana za pośrednictwem argumentu o nazwie `res` .

W katalogu głównym aplikacji *host.jsw* pliku jest skonfigurowany do uruchamiania `handler.exe` i `enableForwardingHttpRequest` ma ustawioną wartość `true` .

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    },
    "enableForwardingHttpRequest": true
  }
}
```

Gdy `enableForwardingHttpRequest` tak jest `true` , zachowanie funkcji tylko http różni się od domyślnego zachowania niestandardowych programów obsługi w następujący sposób:

* Żądanie HTTP nie zawiera ładunku [żądania](#request-payload) obsługi niestandardowej. Zamiast tego host funkcji wywołuje procedurę obsługi z kopią oryginalnego żądania HTTP.
* Host funkcji wywołuje procedurę obsługi z taką samą ścieżką jak oryginalne żądanie, łącznie z parametrami ciągu zapytania.
* Host funkcji zwraca kopię odpowiedzi HTTP programu obsługi jako odpowiedź na oryginalne żądanie.

Poniżej znajduje się żądanie POST dotyczące hosta funkcji. Host funkcji wysyła następnie kopię żądania do niestandardowego programu obsługi w tej samej ścieżce.

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
Content-Type: application/json

{
  "message": "Hello World!"
}
```

Plik *programu obsługi plików. Przejdź* implementuje serwer sieci Web i funkcję http.

```go
package main

import (
    "fmt"
    "io/ioutil"
    "log"
    "net/http"
    "os"
)

func helloHandler(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-Type", "application/json")
    if r.Method == "GET" {
        w.Write([]byte("hello world"))
    } else {
        body, _ := ioutil.ReadAll(r.Body)
        w.Write(body)
    }
}

func main() {
    customHandlerPort, exists := os.LookupEnv("FUNCTIONS_CUSTOMHANDLER_PORT")
    if !exists {
        customHandlerPort = "8080"
    }
    mux := http.NewServeMux()
    mux.HandleFunc("/api/hello", helloHandler)
    fmt.Println("Go server Listening on: ", customHandlerPort)
    log.Fatal(http.ListenAndServe(":"+customHandlerPort, mux))
}
```

W tym przykładzie niestandardowa procedura obsługi tworzy serwer sieci Web do obsługi zdarzeń HTTP i jest skonfigurowany do nasłuchiwania żądań za pośrednictwem `FUNCTIONS_CUSTOMHANDLER_PORT` .

`GET` żądania są obsługiwane przez zwrócenie ciągu, a `POST` żądania mają dostęp do treści żądania.

Trasa dla funkcji Order w tym miejscu jest `/api/hello` taka sama jak pierwotne żądanie.

>[!NOTE]
>`FUNCTIONS_CUSTOMHANDLER_PORT`Nie jest to port publiczny używany do wywołania funkcji. Ten port jest używany przez hosta funkcji do wywoływania niestandardowej procedury obsługi.

## <a name="deploying"></a>Wdrażanie

Niestandardową obsługę można wdrożyć do każdej Azure Functions opcji hostingu. Jeśli program obsługi wymaga systemu operacyjnego lub zależności platformy (takich jak środowisko uruchomieniowe języka), może być konieczne użycie [niestandardowego kontenera](./functions-create-function-linux-custom-image.md).

W przypadku tworzenia aplikacji funkcji na platformie Azure dla obsługi niestandardowych zalecamy wybranie platformy .NET Core jako stosu. W przyszłości zostanie dodany stos "niestandardowy" dla obsługi niestandardowych.

Aby wdrożyć aplikację obsługi niestandardowej przy użyciu Azure Functions Core Tools, uruchom następujące polecenie.

```bash
func azure functionapp publish $functionAppName
```

> [!NOTE]
> Upewnij się, że wszystkie pliki wymagane do uruchomienia niestandardowej procedury obsługi znajdują się w folderze i są uwzględnione we wdrożeniu. Jeśli niestandardowa procedura obsługi jest plikiem wykonywalnym binarnym lub ma zależności specyficzne dla platformy, upewnij się, że te pliki są zgodne z docelową platformą wdrożenia.

## <a name="restrictions"></a>Ograniczenia

- Serwer sieci Web obsługi niestandardowej musi rozpoczynać się w ciągu 60 sekund.

## <a name="samples"></a>Samples

Zapoznaj się z artykułem [Obsługa niestandardowa przykłady dla repozytorium GitHub](https://github.com/Azure-Samples/functions-custom-handlers) , aby zapoznać się z przykładami dotyczącymi implementowania funkcji w różnych różnych językach.

## <a name="troubleshooting-and-support"></a>Rozwiązywanie problemów i pomoc techniczna

### <a name="trace-logging"></a>Rejestrowanie śledzenia

Jeśli uruchomienie niestandardowego procesu obsługi nie powiedzie się lub jeśli wystąpił problem z komunikacją z hostem funkcji, można zwiększyć poziom rejestrowania aplikacji funkcji do, `Trace` Aby zobaczyć więcej komunikatów diagnostycznych z hosta.

Aby zmienić domyślny poziom dziennika aplikacji funkcji, skonfiguruj `logLevel` ustawienie w `logging` sekcji *host.jsna*.

```json
{
  "version": "2.0",
  "customHandler": {
    "description": {
      "defaultExecutablePath": "handler.exe"
    }
  },
  "logging": {
    "logLevel": {
      "default": "Trace"
    }
  }
}
```

Na hoście funkcji są wyprowadzane dodatkowe komunikaty dziennika, w tym informacje związane z niestandardowym procesem obsługi. Użyj dzienników, aby zbadać problemy z uruchamianiem niestandardowego procesu obsługi lub wywoływaniem funkcji w niestandardowej procedurze obsługi.

Lokalnie dzienniki są drukowane w konsoli programu.

Na platformie Azure [Wykonaj zapytania dotyczące Application Insights śledzenia](analyze-telemetry-data.md#query-telemetry-data) , aby wyświetlić komunikaty dziennika. Jeśli aplikacja generuje dużą ilość dzienników, do Application Insights są wysyłane tylko podzbiór komunikatów dziennika. [Wyłącz próbkowanie](configure-monitoring.md#configure-sampling) , aby upewnić się, że wszystkie komunikaty są rejestrowane.

### <a name="test-custom-handler-in-isolation"></a>Niestandardowa obsługa testu w izolacji

Niestandardowe aplikacje obsługi są procesem serwera sieci Web, dlatego może być przydatne, aby można było je uruchomić na własnych i testowych wywołaniach funkcji, wysyłając [żądania HTTP](#request-payload) imitacji przy użyciu [Narzędzia, takiego](https://www.postman.com/)jak [zwinięcie](https://curl.haxx.se/) lub autor.

Możesz również użyć tej strategii w potokach ciągłej integracji/ciągłego dostarczania, aby uruchomić testy automatyczne na niestandardowej obsłudze.

### <a name="execution-environment"></a>Środowisko wykonawcze

Niestandardowe programy obsługi są uruchamiane w tym samym środowisku co Typowa aplikacja Azure Functions. Przetestuj procedurę obsługi, aby upewnić się, że środowisko zawiera wszystkie zależności wymagane do uruchomienia. W przypadku aplikacji, które wymagają dodatkowych zależności, może być konieczne ich uruchomienie przy użyciu [niestandardowego obrazu kontenera](functions-create-function-linux-custom-image.md) hostowanego w [planie Azure Functions Premium](functions-premium-plan.md).

### <a name="get-support"></a>Uzyskiwanie pomocy technicznej

Jeśli potrzebujesz pomocy dotyczącej aplikacji funkcji z obsługą niestandardową, możesz przesłać żądanie za pomocą zwykłych kanałów pomocy technicznej. Jednak ze względu na szeroką gamę możliwych języków używanych do tworzenia niestandardowych aplikacji obsługi, pomoc techniczna nie jest ograniczona.

Pomoc techniczna jest dostępna, Jeśli host funkcji ma problemy z uruchamianiem lub komunikowaniem się z niestandardowym procesem obsługi. W przypadku problemów związanych z wewnętrznymi działaniami niestandardowego procesu obsługi, takich jak problemy z wybranym językiem lub strukturą, zespół pomocy technicznej nie może zapewnić pomocy w tym kontekście.

## <a name="next-steps"></a>Następne kroki

Rozpocznij tworzenie aplikacji Azure Functions w instrukcji go lub Rust przy użyciu [niestandardowych przewodników szybki start](create-first-function-vs-code-other.md).
