---
title: Dokumentacja dla deweloperów języka JavaScript dla Azure Functions
description: Dowiedz się, jak opracowywać funkcje przy użyciu języka JavaScript.
ms.assetid: 45dedd78-3ff9-411f-bb4b-16d29a11384c
ms.topic: conceptual
ms.date: 03/07/2021
ms.custom: devx-track-js
ms.openlocfilehash: 971fb2a3239614a708e14c109e567081f1ec9ff6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102614908"
---
# <a name="azure-functions-javascript-developer-guide"></a>Przewodnik dla deweloperów Azure Functions JavaScript

Ten przewodnik zawiera szczegółowe informacje ułatwiające pomyślne rozwinięcie Azure Functions przy użyciu języka JavaScript.

Jako Express.js, Node.js lub programista JavaScript, jeśli dopiero zaczynasz Azure Functions, rozważ pierwsze czytanie jednego z następujących artykułów:

| Wprowadzenie | Pojęcia| Nauka z przewodnikiem |
| -- | -- | -- | 
| <ul><li>[ FunkcjaNode.js przy użyciu Visual Studio Code](./create-first-function-vs-code-node.md)</li><li>[ FunkcjaNode.js z terminalem/wierszem polecenia](./create-first-function-cli-node.md)</li><li>[ FunkcjaNode.js przy użyciu Azure Portal](functions-create-function-app-portal.md)</li></ul> | <ul><li>[Przewodnik dla deweloperów](functions-reference.md)</li><li>[Opcje hostingu](functions-scale.md)</li><li>[Funkcje języka TypeScript](#typescript)</li><li>[&nbsp;Zagadnienia dotyczące wydajności](functions-best-practices.md)</li></ul> | <ul><li>[Tworzenie aplikacji bezserwerowych](/learn/paths/create-serverless-applications/)</li><li>[Refaktoryzacja Node.js i Express API do bezserwerowych interfejsów API](/learn/modules/shift-nodejs-express-apis-serverless/)</li></ul> |

## <a name="javascript-function-basics"></a>Podstawowe funkcje języka JavaScript

Funkcja JavaScript (Node.js) jest wyeksportowana `function` , która jest wykonywana po wyzwoleniu ([wyzwalacze są konfigurowane w function.jsna](functions-triggers-bindings.md)). Pierwszym argumentem przekazywanym do każdej funkcji jest `context` obiekt, który jest używany do otrzymywania i wysyłania powiązań danych, rejestrowania i komunikacji z środowiskiem uruchomieniowym.

## <a name="folder-structure"></a>Struktura folderów

Wymagana struktura folderów dla projektu JavaScript wygląda następująco. Tę wartość domyślną można zmienić. Aby uzyskać więcej informacji, zobacz sekcję [scriptFile](#using-scriptfile) poniżej.

```
FunctionsProject
 | - MyFirstFunction
 | | - index.js
 | | - function.json
 | - MySecondFunction
 | | - index.js
 | | - function.json
 | - SharedCode
 | | - myFirstHelperFunction.js
 | | - mySecondHelperFunction.js
 | - node_modules
 | - host.json
 | - package.json
 | - extensions.csproj
```

W katalogu głównym projektu znajduje się [host.jsudostępnione w](functions-host-json.md) pliku, którego można użyć do skonfigurowania aplikacji funkcji. Każda funkcja ma folder z własnym plikiem kodu (. js) i plikiem konfiguracji powiązania (function.json). Nazwa `function.json` katalogu nadrzędnego jest zawsze nazwą funkcji.

Rozszerzenia powiązań wymagane w [wersji 2. x](functions-versions.md) środowiska uruchomieniowego funkcji są zdefiniowane w `extensions.csproj` pliku z rzeczywistymi plikami biblioteki w `bin` folderze. Podczas programowania lokalnego należy [zarejestrować rozszerzenia powiązań](./functions-bindings-register.md#extension-bundles). Podczas tworzenia funkcji w Azure Portal Rejestracja jest wykonywana.

## <a name="exporting-a-function"></a>Eksportowanie funkcji

Funkcje JavaScript muszą zostać wyeksportowane za pośrednictwem [`module.exports`](https://nodejs.org/api/modules.html#modules_module_exports) (lub [`exports`](https://nodejs.org/api/modules.html#modules_exports) ). Wyeksportowana funkcja powinna być funkcją języka JavaScript, która jest wykonywana po wyzwoleniu.

Domyślnie środowisko uruchomieniowe funkcji wyszukuje funkcję w programie `index.js` , gdzie jest w niej `index.js` udostępniony ten sam katalog nadrzędny `function.json` . W przypadku domyślnego przypadku eksportowany funkcja powinna być jedynym eksportem z jego pliku lub eksportu o nazwie `run` lub `index` . Aby skonfigurować lokalizację pliku i nazwę eksportu funkcji, Przeczytaj o [konfigurowaniu poniższego punktu wejścia funkcji](functions-reference-node.md#configure-function-entry-point) .

Wyeksportowana funkcja przekazała wiele argumentów w wykonaniu. Pierwszym argumentem, który przyjmuje, jest zawsze `context` obiekt. Jeśli funkcja jest synchroniczna (nie zwraca obietnicy), należy przekazać `context` obiekt, ponieważ wywoływanie `context.done` jest wymagane do poprawnego użycia.

```javascript
// You should include context, other arguments are optional
module.exports = function(context, myTrigger, myInput, myOtherInput) {
    // function logic goes here :)
    context.done();
};
```

### <a name="exporting-an-async-function"></a>Eksportowanie funkcji asynchronicznej
W przypadku korzystania z [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) deklaracji lub zwykłego języka JavaScript [niesie obietnice zwiększenia](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise) w wersji 2. x środowiska uruchomieniowego Functions nie trzeba jawnie wywoływać [`context.done`](#contextdone-method) wywołania zwrotnego, aby sygnalizować, że funkcja została ukończona. Funkcja kończy pracę po zakończeniu wyeksportowanej funkcji/obietnicy asynchronicznej. W przypadku funkcji przeznaczonych dla środowiska uruchomieniowego w wersji 1. x należy nadal wywołać, [`context.done`](#contextdone-method) gdy kod jest wykonywany.

Poniższy przykład to prosta funkcja, która rejestruje, że została wyzwolona, i natychmiast kończy wykonywanie.

```javascript
module.exports = async function (context) {
    context.log('JavaScript trigger function processed a request.');
};
```

Podczas eksportowania funkcji asynchronicznej można również skonfigurować powiązanie danych wyjściowych, aby przyjąć `return` wartość. Jest to zalecane, jeśli masz tylko jedno powiązanie danych wyjściowych.

Aby przypisać dane wyjściowe przy użyciu polecenia `return` , Zmień `name` Właściwość na wartość `$return` w `function.json` .

```json
{
  "type": "http",
  "direction": "out",
  "name": "$return"
}
```

W takim przypadku funkcja powinna wyglądać podobnie do poniższego przykładu:

```javascript
module.exports = async function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');
    // You can call and await an async method here
    return {
        body: "Hello, world!"
    };
}
```

## <a name="bindings"></a>Powiązania 
W języku JavaScript [powiązania](functions-triggers-bindings.md) są konfigurowane i definiowane w function.jsfunkcji w programie. Funkcje współdziałają z powiązaniami na wiele sposobów.

### <a name="inputs"></a>Dane wejściowe
Dane wejściowe są podzielone na dwie kategorie w Azure Functions: jeden to dane wejściowe wyzwalacza, a drugi to dodatkowe dane wejściowe. Wyzwalacz i inne powiązania wejściowe (powiązania `direction === "in"` ) mogą być odczytywane przez funkcję na trzy sposoby:
 - **_[Zalecane]_ Jako parametry przesłane do funkcji.** Są one przenoszone do funkcji w takiej samej kolejności, w jakiej są zdefiniowane w *function.jsna*. `name`Właściwość zdefiniowana w *function.jsna* nie musi być zgodna z nazwą parametru, chociaż powinna być.
 
   ```javascript
   module.exports = async function(context, myTrigger, myInput, myOtherInput) { ... };
   ```
   
 - **Jako elementy członkowskie [`context.bindings`](#contextbindings-property) obiektu.** Każdy element członkowski jest nazwany przez `name` Właściwość zdefiniowaną w *function.jsna*.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + context.bindings.myTrigger);
       context.log("This is myInput: " + context.bindings.myInput);
       context.log("This is myOtherInput: " + context.bindings.myOtherInput);
   };
   ```
   
 - **Jako dane wejściowe przy użyciu [`arguments`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/arguments) obiektu JavaScript.** Jest to zasadniczo takie samo jak przekazywanie danych wejściowych jako parametrów, ale umożliwia dynamiczne obsługiwanie danych wejściowych.
 
   ```javascript
   module.exports = async function(context) { 
       context.log("This is myTrigger: " + arguments[1]);
       context.log("This is myInput: " + arguments[2]);
       context.log("This is myOtherInput: " + arguments[3]);
   };
   ```

### <a name="outputs"></a>Dane wyjściowe
Dane wyjściowe (powiązania `direction === "out"` ) mogą być zapisywane przez funkcję na wiele sposobów. We wszystkich przypadkach `name` właściwość powiązania zgodnie z definicją w *function.json* odpowiada nazwie elementu członkowskiego obiektu, do którego jest zapisywana w funkcji. 

Dane można przypisywać do powiązań wyjściowych w jeden z następujących sposobów (nie łącz tych metod):

- **_[Zalecane dla wielu wyjść]_ Zwracanie obiektu.** Jeśli używana jest funkcja zwracająca Asynchroniczność/Promise, można zwrócić obiekt z przypisanymi danymi wyjściowymi. W poniższym przykładzie powiązania wyjściowe mają nazwę "httpResponse" i "queueOutput" w *function.json*.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      return {
          httpResponse: {
              body: retMsg
          },
          queueOutput: retMsg
      };
  };
  ```

  Jeśli używasz funkcji synchronicznej, możesz zwrócić ten obiekt przy użyciu [`context.done`](#contextdone-method) (Zobacz przykład).
- **_[Zalecane dla pojedynczego wyjścia]_ Zwracanie wartości bezpośrednio i przy użyciu nazwy powiązania $return.** Działa to tylko w przypadku funkcji zwracających dane asynchroniczne/Promise. Zobacz przykład [eksportowania funkcji asynchronicznej](#exporting-an-async-function). 
- **Przypisywanie wartości do `context.bindings`** Możesz przypisywać wartości bezpośrednio do kontekstu Context. bindings.

  ```javascript
  module.exports = async function(context) {
      let retMsg = 'Hello, world!';
      context.bindings.httpResponse = {
          body: retMsg
      };
      context.bindings.queueOutput = retMsg;
      return;
  };
  ```

### <a name="bindings-data-type"></a>Typ danych powiązań

Aby zdefiniować typ danych dla powiązania wejściowego, należy użyć `dataType` właściwości w definicji powiązania. Aby na przykład odczytać zawartość żądania HTTP w formacie binarnym, użyj typu `binary` :

```json
{
    "type": "httpTrigger",
    "name": "req",
    "direction": "in",
    "dataType": "binary"
}
```

Opcje dla `dataType` : `binary` , `stream` , i `string` .

## <a name="context-object"></a>obiekt kontekstu

Środowisko uruchomieniowe używa `context` obiektu do przekazywania danych do i z funkcji oraz środowiska uruchomieniowego. Używany do odczytywania i ustawiania danych z powiązań i zapisywania w dziennikach, `context` obiekt jest zawsze pierwszym parametrem przekazaną do funkcji.

W przypadku funkcji z kodem synchronicznym obiekt kontekstu zawiera `done` wywołanie zwrotne, które jest wywoływane po zakończeniu przetwarzania funkcji. Jawne wywołanie `done` jest niepotrzebne podczas pisania kodu asynchronicznego; `done` wywołanie zwrotne jest wywoływana niejawnie.

```javascript
module.exports = (context) => {

    // function logic goes here

    context.log("The function has executed.");

    context.done();
};
```

Kontekst przekazaną do funkcji uwidacznia `executionContext` Właściwość, która jest obiektem o następujących właściwościach:

| Nazwa właściwości  | Typ  | Opis |
|---------|---------|---------|
| `invocationId` | Ciąg | Zapewnia unikatowy identyfikator dla konkretnego wywołania funkcji. |
| `functionName` | Ciąg | Zawiera nazwę działającej funkcji |
| `functionDirectory` | Ciąg | Udostępnia katalog aplikacji usługi Functions. |

Poniższy przykład pokazuje, jak zwrócić `invocationId` .

```javascript
module.exports = (context, req) => {
    context.res = {
        body: context.executionContext.invocationId
    };
    context.done();
};
```

### <a name="contextbindings-property"></a>Context. bindings — Właściwość

```js
context.bindings
```

Zwraca nazwany obiekt, który jest używany do odczytywania lub przypisywania danych powiązań. Dostęp do danych wejściowych i wyzwalaczy można uzyskać, odczytując właściwości w `context.bindings` . Dane wyjściowe powiązania mogą być przypisywane przez dodanie danych do `context.bindings`

Na przykład następujące definicje powiązań w function.jsumożliwiają dostęp do zawartości kolejki z `context.bindings.myInput` i przypisywanie danych wyjściowych do kolejki przy użyciu `context.bindings.myOutput` .

```json
{
    "type":"queue",
    "direction":"in",
    "name":"myInput"
    ...
},
{
    "type":"queue",
    "direction":"out",
    "name":"myOutput"
    ...
}
```

```javascript
// myInput contains the input data, which may have properties such as "name"
var author = context.bindings.myInput.name;
// Similarly, you can set your output data
context.bindings.myOutput = { 
        some_text: 'hello world', 
        a_number: 1 };
```

Możesz zdefiniować dane wyjściowe powiązania przy użyciu `context.done` metody zamiast `context.binding` obiektu (patrz poniżej).

### <a name="contextbindingdata-property"></a>Context. bindingData, właściwość

```js
context.bindingData
```

Zwraca nazwany obiekt, który zawiera metadane wyzwalacza i dane wywołania funkcji ( `invocationId` , `sys.methodName` , `sys.utcNow` , `sys.randGuid` ). Przykład metadanych wyzwalacza zawiera przykład tego centrum [zdarzeń](functions-bindings-event-hubs-trigger.md).

### <a name="contextdone-method"></a>Context. done — Metoda

```js
context.done([err],[propertyBag])
```

Umożliwia, że środowisko uruchomieniowe wie, że Twój kod został ukończony. Gdy funkcja używa [`async function`](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Statements/async_function) deklaracji, nie musisz używać `context.done()` . `context.done`Wywołanie zwrotne jest wywoływane niejawnie. Funkcje asynchroniczne są dostępne w węźle 8 lub w nowszej wersji, co wymaga wersji 2. x środowiska uruchomieniowego funkcji.

Jeśli funkcja nie jest funkcją asynchroniczną, **należy wywołać** , `context.done` aby poinformować środowisko uruchomieniowe, że funkcja została ukończona. Trwa limit czasu wykonywania, jeśli go nie ma.

`context.done`Metoda umożliwia przekazanie z powrotem błędu zdefiniowanego przez użytkownika do środowiska uruchomieniowego i obiektu JSON zawierającego dane powiązania danych wyjściowych. Właściwości przesłane w celu `context.done` zastąpienia dowolnego zestawu dla `context.bindings` obiektu.

```javascript
// Even though we set myOutput to have:
//  -> text: 'hello world', number: 123
context.bindings.myOutput = { text: 'hello world', number: 123 };
// If we pass an object to the done function...
context.done(null, { myOutput: { text: 'hello there, world', noNumber: true }});
// the done method overwrites the myOutput binding to be: 
//  -> text: 'hello there, world', noNumber: true
```

### <a name="contextlog-method"></a>Context. log — Metoda  

```js
context.log(message)
```

Umożliwia zapis do dzienników funkcji przesyłania strumieniowego na domyślnym poziomie śledzenia przy użyciu innych dostępnych poziomów rejestrowania. Rejestrowanie śledzenia zostało szczegółowo opisane w następnej sekcji. 

## <a name="write-trace-output-to-logs"></a>Zapisz dane wyjściowe śledzenia do dzienników

W obszarze Funkcje używasz `context.log` metod do zapisywania danych wyjściowych śledzenia do dzienników i konsoli programu. Po wywołaniu `context.log()` , wiadomość jest zapisywana w dziennikach na domyślnym poziomie śledzenia, który jest poziomem śledzenia _informacji_ . Funkcje integrują się z usługą Azure Application Insights w celu lepszego przechwytywania dzienników aplikacji funkcji. Application Insights, część Azure Monitor, oferuje funkcje do zbierania, renderowania wizualnego i analizowania danych telemetrycznych aplikacji i wyników śledzenia. Aby dowiedzieć się więcej, zobacz [monitorowanie Azure Functions](functions-monitoring.md).

Poniższy przykład zapisuje dziennik na poziomie śledzenia informacji, łącznie z IDENTYFIKATORem wywołania:

```javascript
context.log("Something has happened. " + context.invocationId); 
```

Wszystkie `context.log` metody obsługują ten sam format parametru, który jest obsługiwany przez [metodę Node.js util. format](https://nodejs.org/api/util.html#util_util_format_format). Rozważmy następujący kod, który zapisuje dzienniki funkcji przy użyciu domyślnego poziomu śledzenia:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=' + req.originalUrl);
context.log('Request Headers = ' + JSON.stringify(req.headers));
```

Możesz również napisać ten sam kod w następującym formacie:

```javascript
context.log('Node.js HTTP trigger function processed a request. RequestUri=%s', req.originalUrl);
context.log('Request Headers = ', JSON.stringify(req.headers));
```

> [!NOTE]  
> Nie używaj `console.log` do zapisywania wyników śledzenia. Ponieważ dane wyjściowe `console.log` są przechwytywane na poziomie aplikacji funkcji, nie są powiązane z konkretnym wywołaniem funkcji i nie są wyświetlane w dziennikach określonej funkcji. Ponadto wersja 1. x środowiska uruchomieniowego funkcji nie obsługuje zapisywania w `console.log` konsoli programu.

### <a name="trace-levels"></a>Poziomy śledzenia

Oprócz poziomu domyślnego dostępne są następujące metody rejestrowania, które umożliwiają pisanie dzienników funkcji na określonych poziomach śledzenia.

| Metoda                 | Opis                                |
| ---------------------- | ------------------------------------------ |
| **błąd (_komunikat_)**   | Zapisuje zdarzenie poziomu błędu w dziennikach.   |
| **warn (_komunikat_)**    | Zapisuje zdarzenie poziomu ostrzeżeń w dziennikach. |
| **informacje (_komunikat_)**    | Zapisuje dane w dzienniku lub niższym poziomie informacji.    |
| **verbose (_komunikat_)** | Zapisuje dane w celu pełnego rejestrowania na poziomie.           |

Poniższy przykład zapisuje ten sam dziennik na poziomie śledzenia ostrzeżeń, a nie na poziomie informacji:

```javascript
context.log.warn("Something has happened. " + context.invocationId); 
```

Ponieważ _błąd_ to najwyższy poziom śledzenia, ten ślad jest zapisywana w danych wyjściowych na wszystkich poziomach śledzenia, o ile rejestrowanie jest włączone.

### <a name="configure-the-trace-level-for-logging"></a>Konfigurowanie poziomu śledzenia na potrzeby rejestrowania

Funkcje umożliwiają zdefiniowanie poziomu śledzenia progu do zapisu w dziennikach lub konsoli programu. Ustawienia określonego progu zależą od używanej wersji środowiska uruchomieniowego funkcji.

# <a name="v2x"></a>[v2. x +](#tab/v2)

Aby ustawić wartość progową dla śladów, które są zapisywane w dziennikach, użyj `logging.logLevel` właściwości w host.jspliku. Ten obiekt JSON pozwala zdefiniować domyślny próg dla wszystkich funkcji w aplikacji funkcji, a także zdefiniować określone progi dla poszczególnych funkcji. Aby dowiedzieć się więcej, zobacz [jak skonfigurować monitorowanie dla Azure Functions](configure-monitoring.md).

# <a name="v1x"></a>[V1. x](#tab/v1)

Aby ustawić wartość progową dla wszystkich śladów, które są zapisywane w dziennikach i konsoli programu, należy użyć `tracing.consoleLevel` właściwości w host.jspliku. To ustawienie ma zastosowanie do wszystkich funkcji w aplikacji funkcji. W poniższym przykładzie ustawiono próg śledzenia w celu włączenia pełnego rejestrowania:

```json
{
    "tracing": {
        "consoleLevel": "verbose"
    }
}  
```

Wartości **consoleLevel** odpowiadają nazwom `context.log` metod. Aby wyłączyć wszystkie rejestrowanie śledzenia w konsoli programu, należy ustawić **consoleLevel** na _wyłączone_. Aby uzyskać więcej informacji, zobacz [host.jsinformacje o wersji 1. x](functions-host-json-v1.md).

---

### <a name="log-custom-telemetry"></a>Rejestruj niestandardową telemetrię

Domyślnie funkcje zapisują dane wyjściowe jako ślady do Application Insights. Aby uzyskać większą kontrolę, zamiast tego możesz użyć [zestawu SDK Application Insights Node.js](https://github.com/microsoft/applicationinsights-node.js) do wysyłania niestandardowych danych telemetrycznych do wystąpienia Application Insights. 

# <a name="v2x"></a>[v2. x +](#tab/v2)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.traceContext.traceparent};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

# <a name="v1x"></a>[V1. x](#tab/v1)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup();
const client = appInsights.defaultClient;

module.exports = function (context, req) {
    context.log('JavaScript HTTP trigger function processed a request.');

    // Use this with 'tagOverrides' to correlate custom telemetry to the parent function invocation.
    var operationIdOverride = {"ai.operation.id":context.operationId};

    client.trackEvent({name: "my custom event", tagOverrides:operationIdOverride, properties: {customProperty2: "custom property value"}});
    client.trackException({exception: new Error("handled exceptions can be logged with this method"), tagOverrides:operationIdOverride});
    client.trackMetric({name: "custom metric", value: 3, tagOverrides:operationIdOverride});
    client.trackTrace({message: "trace message", tagOverrides:operationIdOverride});
    client.trackDependency({target:"http://dbname", name:"select customers proc", data:"SELECT * FROM Customers", duration:231, resultCode:0, success: true, dependencyTypeName: "ZSQL", tagOverrides:operationIdOverride});
    client.trackRequest({name:"GET /customers", url:"http://myserver/customers", duration:309, resultCode:200, success:true, tagOverrides:operationIdOverride});

    context.done();
};
```

---

`tagOverrides`Parametr ustawia wartość `operation_Id` na identyfikator wywołania funkcji. To ustawienie pozwala skorelować wszystkie automatycznie generowane i niestandardową telemetrię dla danego wywołania funkcji.

## <a name="http-triggers-and-bindings"></a>Wyzwalacze i powiązania HTTP

Wyzwalacze HTTP i webhook oraz powiązania wyjściowe HTTP używają obiektów Request i Response do reprezentowania komunikatów HTTP.  

### <a name="request-object"></a>Obiekt żądania

`context.req`Obiekt (żądanie) ma następujące właściwości:

| Właściwość      | Opis                                                    |
| ------------- | -------------------------------------------------------------- |
| _jednostce_        | Obiekt, który zawiera treść żądania.               |
| _nagłówka_     | Obiekt, który zawiera nagłówki żądania.                   |
| _Method_      | Metoda HTTP żądania.                                |
| _originalUrl_ | Adres URL żądania.                                        |
| _params_      | Obiekt, który zawiera parametry routingu żądania. |
| _dotyczących_       | Obiekt, który zawiera parametry zapytania.                  |
| _rawBody_     | Treść komunikatu jako ciąg.                           |


### <a name="response-object"></a>Obiekt odpowiedzi

`context.res`Obiekt (odpowiedź) ma następujące właściwości:

| Właściwość  | Opis                                               |
| --------- | --------------------------------------------------------- |
| _jednostce_    | Obiekt, który zawiera treść odpowiedzi.         |
| _nagłówka_ | Obiekt, który zawiera nagłówki odpowiedzi.             |
| _isRaw_   | Wskazuje, że formatowanie dla odpowiedzi jest pomijane.    |
| _Stany_  | Kod stanu HTTP odpowiedzi.                     |
| _cookie_ | Tablica obiektów cookie HTTP, które są ustawiane w odpowiedzi. Obiekt cookie HTTP ma `name` `value` Właściwość, i inne właściwości pliku cookie, takie jak `maxAge` lub `sameSite` . |

### <a name="accessing-the-request-and-response"></a>Uzyskiwanie dostępu do żądania i odpowiedzi 

Podczas pracy z wyzwalaczami HTTP można uzyskiwać dostęp do obiektów żądania HTTP i odpowiedzi na wiele sposobów:

+ **Z `req` i `res` właściwości `context` obiektu.** W ten sposób można użyć wzorca konwencjonalnego do uzyskiwania dostępu do danych HTTP z obiektu kontekstu, zamiast używać pełnego `context.bindings.name` wzorca. Poniższy przykład pokazuje, jak uzyskać dostęp do `req` `res` obiektów i w `context` :

    ```javascript
    // You can access your HTTP request off the context ...
    if(context.req.body.emoji === ':pizza:') context.log('Yay!');
    // and also set your HTTP response
    context.res = { status: 202, body: 'You successfully ordered more coffee!' }; 
    ```

+ **Z powiązań nazwanych wejściowych i wyjściowych.** W ten sposób wyzwalacz HTTP i powiązania działają tak samo jak inne powiązania. Poniższy przykład ustawia obiekt odpowiedzi przy użyciu nazwanego `response` powiązania: 

    ```json
    {
        "type": "http",
        "direction": "out",
        "name": "response"
    }
    ```
    ```javascript
    context.bindings.response = { status: 201, body: "Insert succeeded." };
    ```
+ **_[Tylko odpowiedź]_ Przez wywołanie metody `context.res.send(body?: any)` .** Odpowiedź HTTP jest tworzona z danymi wejściowymi `body` jako treść odpowiedzi. `context.done()` jest wywoływana niejawnie.

+ **_[Tylko odpowiedź]_ Przez wywołanie metody `context.done()` .** Specjalny typ powiązania HTTP zwraca odpowiedź, która jest przesyłana do `context.done()` metody. Następujące powiązanie wyjściowe HTTP definiuje `$return` parametr wyjściowy:

    ```json
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    }
    ``` 
    ```javascript
     // Define a valid response object.
    res = { status: 201, body: "Insert succeeded." };
    context.done(null, res);   
    ```  

## <a name="scaling-and-concurrency"></a>Skalowanie i współbieżność

Domyślnie Azure Functions automatycznie monitoruje obciążenie aplikacji i tworzy dodatkowe wystąpienia hosta dla Node.js, zgodnie z potrzebami. Funkcja używa wbudowanych wartości progowych (nie można skonfigurować użytkownika) dla różnych typów wyzwalaczy, aby określić, kiedy należy dodać wystąpienia, takie jak wiek komunikatów i rozmiar kolejki dla QueueTrigger. Aby uzyskać więcej informacji, zobacz [jak działają plany zużycia i Premium](event-driven-scaling.md).

To zachowanie skalowania jest wystarczające dla wielu aplikacji Node.js. W przypadku aplikacji powiązanych z PROCESORem można dodatkowo poprawić wydajność przy użyciu wielu procesów roboczych języka.

Domyślnie każde wystąpienie hosta funkcji ma proces roboczy o pojedynczym języku. Liczbę procesów roboczych można zwiększyć na hosta (do 10) przy użyciu ustawienia aplikacji [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) . Azure Functions następnie próbuje równomiernie rozpowszechnić jednoczesne wywołania funkcji przez tych pracowników. 

FUNCTIONS_WORKER_PROCESS_COUNT ma zastosowanie do każdego hosta, który tworzy funkcje podczas skalowania aplikacji w celu spełnienia wymagań. 

## <a name="node-version"></a>Wersja węzła

W poniższej tabeli przedstawiono bieżące obsługiwane wersje Node.js dla każdej wersji głównej środowiska uruchomieniowego funkcji, według systemu operacyjnego:

| Wersja funkcji | Wersja węzła (system Windows) | Wersja węzła (Linux) |
|---|---| --- |
| 3. x (zalecane) | `~14` Rekomendowane<br/>`~12`<br/>`~10` | `node|14` Rekomendowane<br/>`node|12`<br/>`node|10` |
| 2.x  | `~12`<br/>`~10`<br/>`~8` | `node|10`<br/>`node|8`  |
| 1.x | 6.11.2 (zablokowany przez środowisko uruchomieniowe) | n/d |

Bieżącą wersję, która jest używana przez środowisko uruchomieniowe, można zobaczyć, logując się `process.version` z dowolnej funkcji.

### <a name="setting-the-node-version"></a>Ustawianie wersji węzła

W przypadku aplikacji funkcji systemu Windows należy określić wersję na platformie Azure, ustawiając dla `WEBSITE_NODE_DEFAULT_VERSION` [Ustawienia aplikacji](functions-how-to-use-azure-function-app-settings.md#settings) obsługiwaną wersję LTS, na przykład `~14` .

W przypadku aplikacji funkcji systemu Linux Uruchom następujące polecenie interfejsu wiersza polecenia platformy Azure, aby zaktualizować wersję węzła.

```bash
az functionapp config set --linux-fx-version "node|14" --name "<MY_APP_NAME>" --resource-group "<MY_RESOURCE_GROUP_NAME>"
```

## <a name="dependency-management"></a>Zarządzanie zależnościami
Aby można było używać bibliotek społeczności w kodzie JavaScript, jak pokazano w poniższym przykładzie, należy się upewnić, że wszystkie zależności są zainstalowane na aplikacja funkcji na platformie Azure.

```javascript
// Import the underscore.js library
var _ = require('underscore');
var version = process.version; // version === 'v6.5.0'

module.exports = function(context) {
    // Using our imported underscore.js library
    var matched_names = _
        .where(context.bindings.myInput.names, {first: 'Carla'});
```

> [!NOTE]
> Należy zdefiniować `package.json` plik w katalogu głównym aplikacja funkcji. Zdefiniowanie pliku pozwala wszystkim funkcjom w aplikacji współdzielić te same pakiety w pamięci podręcznej, co zapewnia najlepszą wydajność. Jeśli występuje konflikt wersji, można go rozwiązać, dodając `package.json` plik w folderze określonej funkcji.  

Podczas wdrażania aplikacji funkcji z kontroli źródła, każdy `package.json` plik obecny w repozytorium spowoduje wyzwolenie `npm install` w folderze podczas wdrażania. Ale podczas wdrażania za pośrednictwem portalu lub interfejsu wiersza polecenia trzeba będzie ręcznie zainstalować pakiety.

Istnieją dwa sposoby instalowania pakietów na aplikacja funkcji: 

### <a name="deploying-with-dependencies"></a>Wdrażanie z zależnościami
1. Zainstaluj wszystkie wymagane pakiety lokalnie, uruchamiając program `npm install` .

2. Wdróż swój kod i upewnij się, że `node_modules` folder jest uwzględniony we wdrożeniu. 


### <a name="using-kudu"></a>Korzystanie z kudu
1. Przejdź do witryny `https://<function_app_name>.scm.azurewebsites.net`.

2. Kliknij pozycję **Debuguj konsolę**  >  **cmd**.

3. Przejdź do `D:\home\site\wwwroot` , a następnie przeciągnij package.jsw pliku do folderu **wwwroot** w górnej połowie strony.  
    Możesz również przekazywać pliki do aplikacji funkcji w inny sposób. Aby uzyskać więcej informacji, zobacz [jak zaktualizować pliki aplikacji funkcji](functions-reference.md#fileupdate). 

4. Po przekazaniu package.jsw pliku Uruchom `npm install` polecenie w **konsoli wykonywania zdalnego kudu**.  
    Ta akcja spowoduje pobranie pakietów wskazanych w package.jsw pliku i ponowne uruchomienie aplikacji funkcji.

## <a name="environment-variables"></a>Zmienne środowiskowe

Dodaj własne zmienne środowiskowe do aplikacji funkcji, zarówno w środowiskach lokalnych, jak i w chmurze, takich jak wpisy tajne działania (parametry połączenia, klucze i punkty końcowe) lub ustawienia środowiska (takie jak zmienne profilowania). Dostęp do tych ustawień przy użyciu `process.env` kodu funkcji.

### <a name="in-local-development-environment"></a>W lokalnym środowisku programistycznym

W przypadku uruchamiania lokalnego projekt funkcji zawiera [ `local.settings.json` plik](./functions-run-local.md), w którym przechowywane są zmienne środowiskowe w `Values` obiekcie. 

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "node",
    "translatorTextEndPoint": "https://api.cognitive.microsofttranslator.com/",
    "translatorTextKey": "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
    "languageWorkers__node__arguments": "--prof"
  }
}
```

### <a name="in-azure-cloud-environment"></a>W środowisku chmury platformy Azure

W przypadku uruchamiania na platformie Azure aplikacja funkcji pozwala ustawić używanie [ustawień aplikacji](functions-app-settings.md), takich jak parametry połączenia z usługą, i uwidacznia te ustawienia jako zmienne środowiskowe podczas wykonywania. 

[!INCLUDE [Function app settings](../../includes/functions-app-settings.md)]

### <a name="access-environment-variables-in-code"></a>Dostęp do zmiennych środowiskowych w kodzie

Dostęp do ustawień aplikacji jako zmiennych środowiskowych za pomocą programu `process.env` , jak pokazano poniżej w drugim i trzecim wywołaniach, `context.log()` gdzie rejestrujemy `AzureWebJobsStorage` `WEBSITE_SITE_NAME` zmienne środowiskowe i:

```javascript
module.exports = async function (context, myTimer) {

    context.log("AzureWebJobsStorage: " + process.env["AzureWebJobsStorage"]);
    context.log("WEBSITE_SITE_NAME: " + process.env["WEBSITE_SITE_NAME"]);
};
```

## <a name="ecmascript-modules-preview"></a><a name="ecmascript-modules"></a>Moduły języka ECMAScript (wersja zapoznawcza)

> [!NOTE]
> Ponieważ moduły ECMAScript są aktualnie oznaczone jako *eksperymentalne* w Node.js 14, są one dostępne jako funkcja w wersji zapoznawczej w Node.js 14 Azure Functions. Do momentu, aż Node.js 14 dla modułów ECMAScript staną się *stabilne*, oczekiwano możliwych zmian w jego interfejsie API lub zachowaniu.

[Moduły języka ECMAScript](https://nodejs.org/docs/latest-v14.x/api/esm.html#esm_modules_ecmascript_modules) (moduły) to nowy oficjalny system standardowego modułu do Node.js. Do tej pory przykłady kodu w tym artykule używają składni CommonJS. Podczas uruchamiania Azure Functions w Node.js 14 można wybrać opcję pisania funkcji przy użyciu składni modułów ES.

Aby użyć modułów ES w funkcji, Zmień nazwę pliku, aby użyć `.mjs` rozszerzenia. Poniższy przykład pliku *index. MJS* to funkcja wyzwalana przez protokół http, która używa SKŁADNI modułów es do importowania `uuid` biblioteki i zwracania wartości.

```js
import { v4 as uuidv4 } from 'uuid';

export default async function (context, req) {
    context.res.body = uuidv4();
};
```

## <a name="configure-function-entry-point"></a>Konfiguruj punkt wejścia funkcji

`function.json`Właściwości `scriptFile` i `entryPoint` można użyć do skonfigurowania lokalizacji i nazwy wyeksportowanej funkcji. Te właściwości mogą być ważne w przypadku transsterty języka JavaScript.

### <a name="using-scriptfile"></a>Korzystanie z akcji `scriptFile`

Domyślnie funkcja języka JavaScript jest wykonywana z `index.js` , plik, który współużytkuje ten sam katalog nadrzędny co odpowiadający mu `function.json` .

`scriptFile` można go użyć, aby uzyskać strukturę folderów, która wygląda podobnie do poniższego przykładu:

```
FunctionApp
 | - host.json
 | - myNodeFunction
 | | - function.json
 | - lib
 | | - sayHello.js
 | - node_modules
 | | - ... packages ...
 | - package.json
```

`function.json`Element for `myNodeFunction` powinien zawierać `scriptFile` Właściwość wskazującą plik z wyeksportowaną funkcją do uruchomienia.

```json
{
  "scriptFile": "../lib/sayHello.js",
  "bindings": [
    ...
  ]
}
```

### <a name="using-entrypoint"></a>Korzystanie z akcji `entryPoint`

W `scriptFile` (lub `index.js` ) funkcja musi zostać wyeksportowana przy użyciu `module.exports` programu w celu znalezienia i uruchomienia. Domyślnie funkcja, która jest wykonywana, gdy wyzwalane jest jedynym eksportem z tego pliku, eksportem o nazwie `run` lub eksportem o nazwie `index` .

Tę konfigurację można skonfigurować przy użyciu `entryPoint` programu w programie `function.json` , jak w poniższym przykładzie:

```json
{
  "entryPoint": "logFoo",
  "bindings": [
    ...
  ]
}
```

W funkcjach w wersji 2. x, która obsługuje `this` parametr w funkcjach użytkownika, kod funkcji może być następnie tak jak w poniższym przykładzie:

```javascript
class MyObj {
    constructor() {
        this.foo = 1;
    };

    logFoo(context) { 
        context.log("Foo is " + this.foo); 
        context.done(); 
    }
}

const myObj = new MyObj();
module.exports = myObj;
```

W tym przykładzie należy zauważyć, że mimo że obiekt jest eksportowany, nie ma gwarancji, aby zachować stan między wykonaniami.

## <a name="local-debugging"></a>Debugowanie lokalne

Po uruchomieniu z `--inspect` parametrem proces Node.js nasłuchuje klienta debugowania na określonym porcie. W Azure Functions 2. x można określić argumenty do przekazania do procesu Node.js, który uruchamia kod przez dodanie zmiennej środowiskowej lub ustawienia aplikacji `languageWorkers:node:arguments = <args>` . 

Aby debugować lokalnie, Dodaj w `"languageWorkers:node:arguments": "--inspect=5858"` obszarze `Values` [local.settings.js](./functions-run-local.md#local-settings-file) do pliku i dołącz debuger do portu 5858.

Podczas debugowania przy użyciu VS Code `--inspect` parametr jest dodawany automatycznie przy użyciu `port` wartości w launch.jsprojektu na pliku.

W wersji 1. x ustawienie `languageWorkers:node:arguments` nie będzie działało. Port debugowania można wybrać z [`--nodeDebugPort`](./functions-run-local.md#start) parametrem na Azure Functions Core Tools.

## <a name="typescript"></a>TypeScript

Gdy element docelowy jest w wersji 2. x środowiska uruchomieniowego Functions, obie [Azure Functions dla Visual Studio Code](./create-first-function-cli-typescript.md) i [Azure Functions Core Tools](functions-run-local.md) umożliwiają tworzenie aplikacji funkcji przy użyciu szablonu, który obsługuje projekty aplikacji funkcji TypeScript. Szablon generuje i tworzy pliki projektu, które ułatwiają transkodowanie `package.json` `tsconfig.json` , uruchamianie i publikowanie funkcji języka JavaScript w kodzie TypeScript za pomocą tych narzędzi.

Wygenerowany `.funcignore` plik służy do wskazywania, które pliki są wykluczone, gdy projekt jest publikowany na platformie Azure.  

Pliki TypeScript (. TS) są transsterty do plików JavaScript (js) w `dist` katalogu wyjściowym. Szablony języka TypeScript używają [ `scriptFile` parametru](#using-scriptfile) w `function.json` , aby wskazać lokalizację odpowiedniego pliku js w `dist` folderze. Lokalizacja wyjściowa jest ustawiana przez szablon przy użyciu `outDir` parametru w `tsconfig.json` pliku. Jeśli zmienisz to ustawienie lub nazwę folderu, środowisko uruchomieniowe nie będzie w stanie znaleźć kodu do uruchomienia.

Sposób, w jaki użytkownik jest opracowywany i wdrażany z projektu TypeScript, zależy od narzędzia deweloperskiego.

### <a name="visual-studio-code"></a>Visual Studio Code

[Azure Functions rozszerzenia Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) umożliwia tworzenie funkcji przy użyciu języka TypeScript. Podstawowe narzędzia są wymaganiem rozszerzenia Azure Functions.

Aby utworzyć aplikację funkcji języka TypeScript w Visual Studio Code, wybierz `TypeScript` jako język podczas tworzenia aplikacji funkcji.

Po naciśnięciu klawisza **F5** w celu uruchomienia aplikacji lokalnie transpilation jest wykonywana przed zainicjowaniem hosta (func.exe). 

Po wdrożeniu aplikacji funkcji na platformie Azure przy użyciu przycisku **Wdróż do funkcji App...** rozszerzenie Azure Functions najpierw generuje kompilację do użycia w środowisku produkcyjnym na podstawie plików źródłowych języka TypeScript.

### <a name="azure-functions-core-tools"></a>Azure Functions Core Tools

Istnieje kilka sposobów, w których projekt TypeScript różni się od projektu JavaScript podczas korzystania z podstawowych narzędzi.

#### <a name="create-project"></a>Tworzenie projektu

Aby utworzyć projekt aplikacji funkcji TypeScript przy użyciu podstawowych narzędzi, należy określić opcję języka TypeScript podczas tworzenia aplikacji funkcji. Można to zrobić na jeden z następujących sposobów:

- Uruchom `func init` polecenie, wybierz `node` jako stos języka, a następnie wybierz opcję `typescript` .

- Uruchom polecenie `func init --worker-runtime typescript`.

#### <a name="run-local"></a>Uruchom lokalnie

Aby uruchomić kod aplikacji funkcji lokalnie za pomocą podstawowych narzędzi, użyj następujących poleceń zamiast `func host start` : 

```command
npm install
npm start
```

`npm start`Polecenie jest równoważne z następującymi poleceniami:

- `npm run build`
- `func extensions install`
- `tsc`
- `func start`

#### <a name="publish-to-azure"></a>Publikowanie na platformie Azure

Przed użyciem [`func azure functionapp publish`] polecenia do wdrożenia na platformie Azure utworzysz gotowe do produkcji kompilację plików JavaScript z plików źródłowych TypeScript. 

Następujące polecenia przygotują i opublikują projekt TypeScript przy użyciu podstawowych narzędzi: 

```command
npm run build:production 
func azure functionapp publish <APP_NAME>
```

W tym poleceniu Zastąp `<APP_NAME>` wartość nazwą aplikacji funkcji.

## <a name="considerations-for-javascript-functions"></a>Zagadnienia dotyczące funkcji języka JavaScript

Podczas pracy z funkcjami JavaScript należy pamiętać o zagadnieniach w poniższych sekcjach.

### <a name="choose-single-vcpu-app-service-plans"></a>Wybierz plany App Service pojedynczego vCPU

Po utworzeniu aplikacji funkcji, która korzysta z planu App Service, zalecamy wybranie planu pojedynczego vCPU zamiast planu z wieloma procesorów wirtualnych vCPU. Obecnie funkcje języka JavaScript działają wydajniej na maszynach wirtualnych z systemem Single-vCPU, a użycie większych maszyn wirtualnych nie daje oczekiwanych ulepszeń wydajności. W razie potrzeby można ręcznie skalować w poziomie, dodając więcej wystąpień maszyn wirtualnych o pojedynczej vCPU, lub można włączyć automatyczne skalowanie. Aby uzyskać więcej informacji, zobacz [Ręczne lub automatyczne skalowanie liczby wystąpień](../azure-monitor/autoscale/autoscale-get-started.md?toc=/azure/app-service/toc.json).

### <a name="cold-start"></a>Zimne uruchomienie

Podczas opracowywania Azure Functions w modelu hostingu bezserwerowego zimny start jest rzeczywistości. *Zimny start* odnosi się do faktu, że gdy aplikacja funkcji jest uruchamiana po raz pierwszy od czasu nieaktywności, trwa dłużej. W przypadku funkcji języka JavaScript z dużymi drzewami zależności w szczególności zimny start może być znaczący. Aby przyspieszyć zimny proces uruchamiania, [Uruchom funkcje jako plik pakietu](run-functions-from-deployment-package.md) , gdy jest to możliwe. Wiele metod wdrażania domyślnie korzysta z modelu "Uruchom z modelem", ale w przypadku wystąpienia dużej zimnego startu i nie działają w ten sposób, jednak ta zmiana może zapewnić znaczną poprawę.

### <a name="connection-limits"></a>Limity połączeń

W przypadku korzystania z klienta specyficznego dla usługi w aplikacji Azure Functions nie należy tworzyć nowego klienta przy każdym wywołaniu funkcji. Zamiast tego należy utworzyć pojedynczego, statycznego klienta w zakresie globalnym. Aby uzyskać więcej informacji, zobacz [Zarządzanie połączeniami w Azure Functions](manage-connections.md).

### <a name="use-async-and-await"></a>Użyj `async` i `await`

Podczas pisania Azure Functions w języku JavaScript, należy napisać kod przy użyciu `async` `await` słów kluczowych i. Pisanie kodu przy użyciu `async` i `await` zamiast wywołań zwrotnych lub `.then` i `.catch` z niesie obietnice zwiększeniaą pomaga uniknąć dwóch typowych problemów:
 - Zgłaszanie nieprzechwyconych wyjątków, które [uległy awarii procesu Node.js](https://nodejs.org/api/process.html#process_warning_using_uncaughtexception_correctly), mogą mieć wpływ na wykonywanie innych funkcji.
 - Nieoczekiwane zachowanie, takie jak brakujące dzienniki z kontekstu. log, spowodowane przez wywołania asynchroniczne, które nie zostały prawidłowo oczekiwane.

W poniższym przykładzie Metoda asynchroniczna `fs.readFile` jest wywoływana z funkcją wywołania zwrotnego pierwszego błędu jako drugi parametr. Ten kod powoduje wystąpienie obu problemów. Wyjątek, który nie został jawnie przechwycony w prawidłowym zakresie, uległ awarii cały proces (problem #1). Wywołanie `context.done()` poza zakresem funkcji wywołania zwrotnego oznacza, że wywołanie funkcji może kończyć się przed odczytaniem pliku (problem #2). W tym przykładzie wywoływanie `context.done()` zbyt wczesnych wyników w przypadku brakujących wpisów dziennika rozpoczyna się od `Data from file:` .

```javascript
// NOT RECOMMENDED PATTERN
const fs = require('fs');

module.exports = function (context) {
    fs.readFile('./hello.txt', (err, data) => {
        if (err) {
            context.log.error('ERROR', err);
            // BUG #1: This will result in an uncaught exception that crashes the entire process
            throw err;
        }
        context.log(`Data from file: ${data}`);
        // context.done() should be called here
    });
    // BUG #2: Data is not guaranteed to be read before the Azure Function's invocation ends
    context.done();
}
```

Używanie `async` `await` słów kluczowych i pomaga uniknąć obydwu tych błędów. Należy użyć funkcji narzędzia Node.js, [`util.promisify`](https://nodejs.org/api/util.html#util_util_promisify_original) Aby przekształcić funkcje pierwszego stylu wywołania zwrotnego w funkcje w oczekiwany sposób.

W poniższym przykładzie wszystkie Nieobsłużone wyjątki zgłoszone podczas wykonywania funkcji kończą się niepowodzeniem tylko przez pojedyncze wywołanie, które zgłosiło wyjątek. `await`Słowo kluczowe oznacza, że kroki `readFileAsync` wykonywane tylko po zakończeniu `readFile` zostały wykonane. W przypadku `async` i `await` , nie trzeba również wywoływać `context.done()` wywołania zwrotnego.

```javascript
// Recommended pattern
const fs = require('fs');
const util = require('util');
const readFileAsync = util.promisify(fs.readFile);

module.exports = async function (context) {
    let data;
    try {
        data = await readFileAsync('./hello.txt');
    } catch (err) {
        context.log.error('ERROR', err);
        // This rethrown exception will be handled by the Functions Runtime and will only fail the individual invocation
        throw err;
    }
    context.log(`Data from file: ${data}`);
}
```

## <a name="next-steps"></a>Następne kroki

Więcej informacji można znaleźć w następujących zasobach:

+ [Najlepsze rozwiązania dotyczące usługi Azure Functions](functions-best-practices.md)
+ [Dokumentacja usługi Azure Functions dla deweloperów](functions-reference.md)
+ [Azure Functions wyzwalacze i powiązania](functions-triggers-bindings.md)

["Func Azure functionapp Publish"]: functions-run-local.md#project-file-deployment
