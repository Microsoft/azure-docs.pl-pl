---
title: Niestandardowe programy obsługi Azure Functions (wersja zapoznawcza)
description: Dowiedz się, jak używać Azure Functions z dowolnym językiem lub wersją środowiska uruchomieniowego.
author: craigshoemaker
ms.author: cshoe
ms.date: 3/18/2020
ms.topic: article
ms.openlocfilehash: cdbb5bbde1e5efef9bef992a62a54f1525a16df7
ms.sourcegitcommit: ff19f4ecaff33a414c0fa2d4c92542d6e91332f8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/18/2020
ms.locfileid: "85052580"
---
# <a name="azure-functions-custom-handlers-preview"></a>Niestandardowe programy obsługi Azure Functions (wersja zapoznawcza)

Każda aplikacja funkcji jest wykonywana przez program obsługi specyficzny dla języka. Mimo że Azure Functions obsługuje wiele [programów obsługi języka](./supported-languages.md) domyślnie, istnieją przypadki, w których można chcieć dodatkowej kontroli nad środowiskiem wykonywania aplikacji. Niestandardowe programy obsługi zapewniają tę dodatkową kontrolę.

Niestandardowe programy obsługi to lekkie serwery sieci Web, które odbierają zdarzenia z hosta usługi Functions. Każdy język, który obsługuje elementy podstawowe HTTP, może zaimplementować obsługę niestandardową.

Niestandardowe programy obsługi są najlepiej dostosowane do sytuacji, w których chcesz:

- Zaimplementuj aplikację funkcji w języku, który nie jest oficjalnie obsługiwany.
- Zaimplementuj aplikację funkcji w wersji językowej lub środowisko uruchomieniowe nie jest obsługiwane domyślnie.
- Zapewnienie bardziej szczegółowej kontroli nad środowiskiem wykonywania aplikacji funkcji.

W przypadku obsługi niestandardowych wszystkie [wyzwalacze i powiązania wejściowe i wyjściowe](./functions-triggers-bindings.md) są obsługiwane przez [zbiory rozszerzeń](./functions-bindings-register.md).

## <a name="overview"></a>Omówienie

Na poniższym diagramie przedstawiono relację między hostem funkcji a serwerem sieci Web zaimplementowaną jako procedura obsługi niestandardowej.

![Przegląd niestandardowego programu obsługi Azure Functions](./media/functions-custom-handlers/azure-functions-custom-handlers-overview.png)

- Zdarzenia wyzwalają żądanie wysyłane do hosta funkcji. Zdarzenie obejmuje nieprzetworzony ładunek HTTP (dla funkcji wyzwalanych przez protokół HTTP bez powiązań) lub ładunek, który przechowuje dane wejściowe powiązania dla funkcji.
- Usługa Functions następnie wysyła żądanie do serwera sieci Web, wysyłając [ładunek żądania](#request-payload).
- Serwer sieci Web wykonuje pojedynczą funkcję i zwraca [ładunek odpowiedzi](#response-payload) do hosta funkcji.
- Serwer proxy usługi Functions jest odpowiedzią jako ładunek powiązania wyjściowego z elementem docelowym.

Aplikacja Azure Functions zaimplementowana jako procedura obsługi niestandardowej musi skonfigurować *host.jsna* plikach i *function.jsdla* plików zgodnie z kilkoma konwencjami.

## <a name="application-structure"></a>Struktura aplikacji

Aby zaimplementować obsługę niestandardową, potrzebne są następujące aspekty aplikacji:

- *host.jsw* pliku w katalogu głównym aplikacji
- *function.jsw* pliku dla każdej funkcji (wewnątrz folderu, który jest zgodny z nazwą funkcji)
- Polecenie, skrypt lub plik wykonywalny, który uruchamia serwer sieci Web

Na poniższym diagramie przedstawiono, jak te pliki wyglądają w systemie plików dla funkcji o nazwie "Order".

```bash
| /order
|   function.json
|
| host.json
```

### <a name="configuration"></a>Konfiguracja

Aplikacja jest konfigurowana za pośrednictwem *host.js* pliku. Ten plik informuje hosta funkcji, gdzie wysyłać żądania, wskazując na serwer sieci Web, który może przetwarzać zdarzenia HTTP.

Niestandardowa procedura obsługi jest definiowana przez skonfigurowanie *host.jsw* pliku ze szczegółowymi informacjami na temat sposobu uruchamiania serwera sieci Web za pośrednictwem `httpWorker` sekcji.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "server.exe"
        }
    }
}
```

`httpWorker`Sekcja wskazuje element docelowy określony przez `defaultExecutablePath` . Obiektem docelowym wykonywania może być polecenie, plik wykonywalny lub plik, w którym jest zaimplementowany serwer sieci Web.

W przypadku aplikacji ze skryptami `defaultExecutablePath` wskazuje środowisko uruchomieniowe języka skryptu i `defaultWorkerPath` wskazuje lokalizację pliku skryptu. Poniższy przykład pokazuje, jak aplikacja JavaScript w Node.js jest skonfigurowana jako procedura obsługi niestandardowej.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

Można również przekazywać argumenty przy użyciu `arguments` tablicy:

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js",
            "arguments": [ "--argument1", "--argument2" ]
        }
    }
}
```

Dla wielu konfiguracji debugowania są wymagane argumenty. Zobacz sekcję [debugowanie](#debugging) , aby uzyskać więcej szczegółów.

> [!NOTE]
> *host.jsna* pliku musi znajdować się na tym samym poziomie w strukturze katalogów co uruchomiony serwer sieci Web. Niektóre języki i łańcuchy narzędzi nie mogą domyślnie umieścić tego pliku w katalogu głównym aplikacji.

#### <a name="bindings-support"></a>Obsługa powiązań

Standardowe wyzwalacze wraz z powiązaniami wejściowymi i wyjściowymi są dostępne przez odwoływanie się do [pakietów rozszerzeń](./functions-bindings-register.md) w *host.js* pliku.

### <a name="function-metadata"></a>Metadane funkcji

W przypadku użycia z niestandardową obsługą *function.jsna* zawartości nie różnią się od sposobu definiowania funkcji w dowolnym innym kontekście. Jedyny wymóg polega na tym, że *function.js* plików musi znajdować się w folderze o nazwie, aby odpowiadał nazwie funkcji.

### <a name="request-payload"></a>Ładunek żądania

Ładunek żądania dla czystych funkcji HTTP to nieprzetworzony ładunek żądania HTTP. Czyste funkcje HTTP są definiowane jako funkcje bez powiązań wejściowych i wyjściowych, które zwracają odpowiedź HTTP.

Każdy inny typ funkcji, który zawiera dane wejściowe, powiązania wyjściowe lub wyzwalane za pośrednictwem źródła zdarzeń innego niż HTTP ma niestandardowy ładunek żądania.

Poniższy kod przedstawia przykładowy ładunek żądania. Ładunek zawiera strukturę JSON z dwoma elementami członkowskimi: `Data` i `Metadata` .

`Data`Element członkowski zawiera klucze, które pasują do nazw wejściowych i wyzwalaczy, zgodnie z definicją w tablicy powiązań w *function.jsna* pliku.

`Metadata`Element członkowski zawiera [metadane wygenerowane ze źródła zdarzeń](./functions-bindings-expressions-patterns.md#trigger-metadata).

Uwzględniając powiązania zdefiniowane w następującym *function.jsw* pliku:

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

Zwracany jest ładunek żądania podobny do tego przykładu:

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
| `Outputs`     | JSON      | Przechowuje wartości odpowiedzi zdefiniowane przez `bindings` tablicę *function.jsna* pliku.<br /><br />Na przykład jeśli funkcja jest skonfigurowana za pomocą powiązania danych wyjściowych usługi BLOB Storage o nazwie "BLOB", a następnie `Outputs` zawiera klucz o nazwie `blob` , który jest ustawiony na wartość obiektu BLOB. |
| `Logs`        | tablica     | Komunikaty są wyświetlane w dziennikach wywołania funkcji.<br /><br />W przypadku uruchamiania na platformie Azure komunikaty są wyświetlane w Application Insights. |
| `ReturnValue` | ciąg    | Służy do dostarczania odpowiedzi w przypadku skonfigurowania danych wyjściowych, jak `$return` w *function.js* pliku. |

Zapoznaj się z [przykładowym ładunkiem](#bindings-implementation).

## <a name="examples"></a>Przykłady

Niestandardowe programy obsługi można zaimplementować w dowolnym języku, który obsługuje zdarzenia HTTP. Mimo że Azure Functions w [pełni obsługuje język JavaScript i Node.js](./functions-reference-node.md), w poniższych przykładach pokazano, jak zaimplementować procedurę obsługi niestandardowej przy użyciu języka JavaScript w Node.js na potrzeby instrukcji.

> [!TIP]
> Przewodnik dotyczący uczenia się, jak zaimplementować obsługę niestandardową w innych językach, przykładowe przykłady oparte na Node.js mogą być przydatne, jeśli chcesz uruchomić aplikację Functions w nieobsługiwanej wersji Node.js.

## <a name="http-only-function"></a>Funkcja tylko HTTP

Poniższy przykład ilustruje sposób konfigurowania funkcji wyzwalanej przez protokół HTTP bez dodatkowych powiązań ani danych wyjściowych. Scenariusz zaimplementowany w tym przykładzie cechuje funkcję o nazwie `http` , która akceptuje `GET` lub `POST` .

Poniższy fragment kodu przedstawia sposób tworzenia żądania do funkcji.

```http
POST http://127.0.0.1:7071/api/hello HTTP/1.1
content-type: application/json

{
  "message": "Hello World!"
}
```

<a id="hello-implementation" name="hello-implementation"></a>

### <a name="implementation"></a>Implementacja

W folderze o nazwie *http* *function.jsw* pliku konfiguruje funkcję wyzwalaną przez protokół http.

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
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

W katalogu głównym aplikacji *host.jsw* pliku jest skonfigurowany do uruchamiania Node.js i wskazywania `server.js` pliku.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

Plik *server.js* pliku implementuje serwer sieci Web i funkcję http.

```javascript
const express = require("express");
const app = express();

app.use(express.json());

const PORT = process.env.FUNCTIONS_HTTPWORKER_PORT;

const server = app.listen(PORT, "localhost", () => {
  console.log(`Your port is ${PORT}`);
  const { address: host, port } = server.address();
  console.log(`Example app listening at http://${host}:${port}`);
});

app.get("/hello", (req, res) => {
  res.json("Hello World!");
});

app.post("/hello", (req, res) => {
  res.json({ value: req.body });
});
```

W tym przykładzie program Express służy do tworzenia serwera sieci Web do obsługi zdarzeń HTTP i jest skonfigurowany do nasłuchiwania żądań za pośrednictwem `FUNCTIONS_HTTPWORKER_PORT` .

Funkcja jest definiowana w ścieżce `/hello` . `GET`żądania są obsługiwane przez zwrócenie prostego obiektu JSON, a `POST` żądania mają dostęp do treści żądania za pośrednictwem `req.body` .

Trasa dla funkcji Order w tym miejscu to `/hello` , a nie `/api/hello` ponieważ Host usługi Functions jest serwerem proxy żądania do obsługi niestandardowej.

>[!NOTE]
>`FUNCTIONS_HTTPWORKER_PORT`Nie jest to port publiczny używany do wywołania funkcji. Ten port jest używany przez hosta funkcji do wywoływania niestandardowej procedury obsługi.

## <a name="function-with-bindings"></a>Funkcja z powiązaniami

Scenariusz zaimplementowany w tym przykładzie cechuje funkcję o nazwie `order` , która akceptuje element `POST` z ładunkiem reprezentującym zamówienie produktu. W przypadku opublikowania zamówienia w funkcji zostanie utworzony komunikat Queue Storage i zostanie zwrócona odpowiedź HTTP.

```http
POST http://127.0.0.1:7071/api/order HTTP/1.1
content-type: application/json

{
  "id": 1005,
  "quantity": 2,
  "color": "black"
}
```

<a id="bindings-implementation" name="bindings-implementation"></a>

### <a name="implementation"></a>Implementacja

W folderze o nazwie *order* *function.jsw* pliku konfiguruje funkcję wyzwalaną przez protokół http.

```json
{
  "bindings": [
    {
      "type": "httpTrigger",
      "authLevel": "function",
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

W katalogu głównym aplikacji *host.jsw* pliku jest skonfigurowany do uruchamiania Node.js i wskazywania `server.js` pliku.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js"
        }
    }
}
```

Plik *server.js* pliku implementuje serwer sieci Web i funkcję http.

```javascript
const express = require("express");
const app = express();

app.use(express.json());

const PORT = process.env.FUNCTIONS_HTTPWORKER_PORT;

const server = app.listen(PORT, "localhost", () => {
  console.log(`Your port is ${PORT}`);
  const { address: host, port } = server.address();
  console.log(`Example app listening at http://${host}:${port}`);
});

app.post("/order", (req, res) => {
  const message = req.body.Data.req.Body;
  const response = {
    Outputs: {
      message: message,
      res: {
        statusCode: 200,
        body: "Order complete"
      }
    },
    Logs: ["order processed"]
  };
  res.json(response);
});
```

W tym przykładzie program Express służy do tworzenia serwera sieci Web do obsługi zdarzeń HTTP i jest skonfigurowany do nasłuchiwania żądań za pośrednictwem `FUNCTIONS_HTTPWORKER_PORT` .

Funkcja jest definiowana w ścieżce `/order` .  Trasa dla funkcji Order w tym miejscu to `/order` , a nie `/api/order` ponieważ Host usługi Functions jest serwerem proxy żądania do obsługi niestandardowej.

Po `POST` wysłaniu żądań do tej funkcji dane są udostępniane za pomocą kilku punktów:

- Treść żądania jest dostępna za pośrednictwem`req.body`
- Dane ogłoszone do funkcji są dostępne za pośrednictwem`req.body.Data.req.Body`

Odpowiedź funkcji jest formatowana do pary klucz/wartość, gdzie `Outputs` składowa zawiera wartość JSON, gdzie klucze pasują do danych wyjściowych, zgodnie z definicją w *function.js* pliku.

Zgodnie z ustawieniem `message` równym komunikatowi, który został dostarczony z żądania, i `res` do oczekiwanej odpowiedzi HTTP, ta funkcja wyprowadza komunikat do queue storage i zwraca odpowiedź HTTP.

## <a name="debugging"></a>Debugowanie

Aby debugować aplikację obsługi niestandardowej funkcji, musisz dodać argumenty odpowiednie dla języka i środowiska uruchomieniowego, aby włączyć debugowanie.

Na przykład w celu debugowania aplikacji Node.js `--inspect` flaga jest przenoszona jako argument w *host.js* pliku.

```json
{
    "version": "2.0",
    "httpWorker": {
        "description": {
            "defaultExecutablePath": "node",
            "defaultWorkerPath": "server.js",
            "arguments": [ "--inspect" ]
        }
    }
}
```

> [!NOTE]
> Konfiguracja debugowania jest częścią *host.jsw* pliku, co oznacza, że może być konieczne usunięcie niektórych argumentów przed wdrożeniem w środowisku produkcyjnym.

Za pomocą tej konfiguracji można uruchomić proces hosta funkcji przy użyciu następującego polecenia:

```bash
func host start
```

Po rozpoczęciu procesu możesz dołączyć debuger i trafień punktów przerwania.

### <a name="visual-studio-code"></a>Visual Studio Code

W poniższym przykładzie przedstawiono przykładową konfigurację, która pokazuje, jak można skonfigurować *launch.jsw* pliku, aby połączyć aplikację z debugerem Visual Studio Code.

Ten przykład dotyczy Node.js, więc może być konieczne zmodyfikowanie tego przykładu dla innych języków lub środowisk uruchomieniowych.

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Attach to Node Functions",
      "type": "node",
      "request": "attach",
      "port": 9229,
      "preLaunchTask": "func: host start"
    }
  ]
}
```

## <a name="deploying"></a>Wdrażanie

Niestandardową obsługę można wdrożyć do niemal każdej Azure Functions opcji hostingu (zobacz [ograniczenia](#restrictions)). Jeśli program obsługi wymaga niestandardowych zależności (takich jak środowisko uruchomieniowe języka), może być konieczne użycie [niestandardowego kontenera](./functions-create-function-linux-custom-image.md).

Aby wdrożyć aplikację obsługi niestandardowej przy użyciu Azure Functions Core Tools, uruchom następujące polecenie.

```bash
func azure functionapp publish $functionAppName --no-build --force
```

## <a name="restrictions"></a>Ograniczenia

- Serwer sieci Web musi zostać uruchomiony w ciągu 60 sekund.

## <a name="samples"></a>Samples

Zapoznaj się z artykułem [Obsługa niestandardowa przykłady dla repozytorium GitHub](https://github.com/Azure-Samples/functions-custom-handlers) , aby zapoznać się z przykładami dotyczącymi implementowania funkcji w różnych różnych językach.
