---
title: Testowanie usługi Azure Functions
description: Utwórz testy automatyczne dla funkcji języka C# w programie Visual Studio i funkcji JavaScript w VS Code
author: craigshoemaker
ms.topic: conceptual
ms.custom: devx-track-csharp, devx-track-js
ms.date: 03/25/2019
ms.author: cshoe
ms.openlocfilehash: ff64d5c17174f8e1e67111ebca9ccf050deb2f26
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94409658"
---
# <a name="strategies-for-testing-your-code-in-azure-functions"></a>Strategie testowania kodu w usłudze Azure Functions

W tym artykule przedstawiono sposób tworzenia zautomatyzowanych testów dla Azure Functions.

Zalecane jest przetestowanie całego kodu, jednak uzyskanie najlepszych wyników przez zapakowanie logiki funkcji i utworzenie testów poza funkcją może być możliwe. Logika abstrakcji ogranicza wiersze kodu funkcji i pozwala, aby funkcja była odpowiedzialna wyłącznie za wywoływanie innych klas lub modułów. W tym artykule przedstawiono jednak sposób tworzenia zautomatyzowanych testów względem funkcji wyzwalanych przez protokół HTTP i czasomierz.

Następująca zawartość jest podzielona na dwie różne sekcje przeznaczone dla różnych języków i środowisk. Możesz dowiedzieć się, jak kompilować testy w:

- [Język C# w programie Visual Studio z xUnit](#c-in-visual-studio)
- [Język JavaScript w VS Code za pomocą](#javascript-in-vs-code)

Przykładowe repozytorium jest dostępne w witrynie [GitHub](https://github.com/Azure-Samples/azure-functions-tests).

## <a name="c-in-visual-studio"></a>Język C# w programie Visual Studio

W poniższym przykładzie opisano sposób tworzenia aplikacji funkcji języka C# w programie Visual Studio i uruchamiania i testowania przy użyciu [xUnit](https://xunit.github.io).

![Testowanie Azure Functions przy użyciu języka C# w programie Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="setup"></a>Konfigurowanie

Aby skonfigurować środowisko, Utwórz funkcję i aplikację testową. Poniższe kroki ułatwiają tworzenie aplikacji i funkcji wymaganych do obsługi testów:

1. [Utwórz nową aplikację funkcji](./functions-create-first-azure-function.md) i nadaj jej nazwę **Functions**
2. [Utwórz funkcję http z szablonu](./functions-create-first-azure-function.md) i nadaj jej nazwę **MyHttpTrigger**.
3. [Utwórz funkcję timer na podstawie szablonu](./functions-create-scheduled-function.md) i nadaj jej nazwę **MyTimerTrigger**.
4. [Utwórz aplikację testową xUnit](https://xunit.github.io/docs/getting-started-dotnet-core) w rozwiązaniu i nadaj jej nazwę **Functions. Tests**.
5. Użyj NuGet, aby dodać odwołanie z aplikacji testowej do [Microsoft. AspNetCore. MVC](https://www.nuget.org/packages/Microsoft.AspNetCore.Mvc/)
6. [Odwołuje się do aplikacji *Functions*](/visualstudio/ide/managing-references-in-a-project?view=vs-2017) z poziomu aplikacji *Functions. Tests* .

### <a name="create-test-classes"></a>Utwórz klasy testowe

Teraz, gdy projekty są tworzone, można utworzyć klasy używane do uruchamiania testów automatycznych.

Każda funkcja przyjmuje wystąpienie [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger) do obsługi rejestrowania komunikatów. Niektóre testy nie rejestrują komunikatów lub nie mają żadnych obaw dotyczących sposobu zaimplementowania rejestrowania. Inne testy muszą mieć na celu sprawdzenie, czy test kończy się powodzeniem.

Utworzysz nową klasę o nazwie `ListLogger` , która zawiera wewnętrzną listę komunikatów do oceny podczas testowania. Do zaimplementowania wymaganego `ILogger` interfejsu Klasa musi mieć zakres. W poniższej klasie przedstawiono zakres dla przypadków testowych do przekazania do `ListLogger` klasy.

Utwórz nową klasę w *Functions. Tests* projektu o nazwie **NullScope.cs** i wprowadź następujący kod:

```csharp
using System;

namespace Functions.Tests
{
    public class NullScope : IDisposable
    {
        public static NullScope Instance { get; } = new NullScope();

        private NullScope() { }

        public void Dispose() { }
    }
}
```

Następnie utwórz nową klasę w *Functions. Tests* projektu o nazwie **ListLogger.cs** i wprowadź następujący kod:

```csharp
using Microsoft.Extensions.Logging;
using System;
using System.Collections.Generic;
using System.Text;

namespace Functions.Tests
{
    public class ListLogger : ILogger
    {
        public IList<string> Logs;

        public IDisposable BeginScope<TState>(TState state) => NullScope.Instance;

        public bool IsEnabled(LogLevel logLevel) => false;

        public ListLogger()
        {
            this.Logs = new List<string>();
        }

        public void Log<TState>(LogLevel logLevel,
                                EventId eventId,
                                TState state,
                                Exception exception,
                                Func<TState, Exception, string> formatter)
        {
            string message = formatter(state, exception);
            this.Logs.Add(message);
        }
    }
}
```

`ListLogger`Klasa implementuje następujących członków zgodnie z umową `ILogger` Interface:

- **BeginScope** : zakresy dodają kontekst do rejestrowania. W takim przypadku test tylko wskazuje na wystąpienie statyczne w `NullScope` klasie, aby zezwolić na działanie testu.

- **IsEnabled** : podano wartość domyślną `false` .

- **Dziennik** : Ta metoda używa podanej `formatter` funkcji do sformatowania wiadomości, a następnie dodania otrzymanego tekstu do `Logs` kolekcji.

`Logs`Kolekcja jest wystąpieniem `List<string>` i jest inicjowana w konstruktorze.

Następnie utwórz nowy plik w usłudze *Functions. Tests* o nazwie **LoggerTypes.cs** i wprowadź następujący kod:

```csharp
namespace Functions.Tests
{
    public enum LoggerTypes
    {
        Null,
        List
    }
}
```

To Wyliczenie określa typ rejestratora używany przez testy.

Teraz Utwórz nową klasę w *funkcji Functions. Tests* projektu o nazwie **TestFactory.cs** i wprowadź następujący kod:

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Http.Internal;
using Microsoft.Extensions.Logging;
using Microsoft.Extensions.Logging.Abstractions;
using Microsoft.Extensions.Primitives;
using System.Collections.Generic;

namespace Functions.Tests
{
    public class TestFactory
    {
        public static IEnumerable<object[]> Data()
        {
            return new List<object[]>
            {
                new object[] { "name", "Bill" },
                new object[] { "name", "Paul" },
                new object[] { "name", "Steve" }

            };
        }

        private static Dictionary<string, StringValues> CreateDictionary(string key, string value)
        {
            var qs = new Dictionary<string, StringValues>
            {
                { key, value }
            };
            return qs;
        }

        public static HttpRequest CreateHttpRequest(string queryStringKey, string queryStringValue)
        {
            var context = new DefaultHttpContext();
            var request = context.Request;
            request.Query = new QueryCollection(CreateDictionary(queryStringKey, queryStringValue));
            return request;
        }

        public static ILogger CreateLogger(LoggerTypes type = LoggerTypes.Null)
        {
            ILogger logger;

            if (type == LoggerTypes.List)
            {
                logger = new ListLogger();
            }
            else
            {
                logger = NullLoggerFactory.Instance.CreateLogger("Null Logger");
            }

            return logger;
        }
    }
}
```

`TestFactory`Klasa implementuje następujące elementy członkowskie:

- **Dane** : Ta właściwość zwraca kolekcję [IEnumerable](/dotnet/api/system.collections.ienumerable) przykładowych danych. Pary klucz wartość reprezentują wartości, które są przesyłane do ciągu zapytania.

- **IsDictionary** : Ta metoda akceptuje parę klucz/wartość jako argumenty i zwraca nowy `Dictionary` użyty do utworzenia `QueryCollection` do reprezentowania wartości ciągu zapytania.

- Zdarzenie **: Ta** Metoda tworzy żądanie HTTP zainicjowane przy użyciu podanym parametrów ciągu zapytania.

- **Wyrejestrowania** : na podstawie typu rejestratora ta metoda zwraca klasę rejestratora używaną do testowania. `ListLogger`Śledzi zarejestrowane komunikaty dostępne do oceny w testach.

Na koniec Utwórz nową klasę w *Functions. Tests* projektu o nazwie **FunctionsTests.cs** i wprowadź następujący kod:

```csharp
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using Xunit;

namespace Functions.Tests
{
    public class FunctionsTests
    {
        private readonly ILogger logger = TestFactory.CreateLogger();

        [Fact]
        public async void Http_trigger_should_return_known_string()
        {
            var request = TestFactory.CreateHttpRequest("name", "Bill");
            var response = (OkObjectResult)await MyHttpTrigger.Run(request, logger);
            Assert.Equal("Hello, Bill. This HTTP triggered function executed successfully.", response.Value);
        }

        [Theory]
        [MemberData(nameof(TestFactory.Data), MemberType = typeof(TestFactory))]
        public async void Http_trigger_should_return_known_string_from_member_data(string queryStringKey, string queryStringValue)
        {
            var request = TestFactory.CreateHttpRequest(queryStringKey, queryStringValue);
            var response = (OkObjectResult)await MyHttpTrigger.Run(request, logger);
            Assert.Equal($"Hello, {queryStringValue}. This HTTP triggered function executed successfully.", response.Value);
        }

        [Fact]
        public void Timer_should_log_message()
        {
            var logger = (ListLogger)TestFactory.CreateLogger(LoggerTypes.List);
            MyTimerTrigger.Run(null, logger);
            var msg = logger.Logs[0];
            Assert.Contains("C# Timer trigger function executed at", msg);
        }
    }
}
```

Członkowie zaimplementowani w tej klasie są:

- **Http_trigger_should_return_known_string** : ten test tworzy żądanie z wartościami ciągu zapytania dla `name=Bill` funkcji http i sprawdza, czy jest zwracana oczekiwana odpowiedź.

- **Http_trigger_should_return_string_from_member_data** : ten test używa atrybutów xUnit, aby zapewnić dane przykładowe do funkcji http.

- **Timer_should_log_message** : ten test tworzy wystąpienie `ListLogger` i przekazuje je do funkcji timer. Po uruchomieniu funkcji, Dziennik zostanie sprawdzony w celu upewnienia się, że oczekiwany komunikat jest obecny.

Jeśli chcesz uzyskać dostęp do ustawień aplikacji w testach, można [wstrzyknąć](./functions-dotnet-dependency-injection.md) wystąpienie z nieprawidłowymi `IConfiguration` wartościami zmiennych środowiskowych do funkcji.

### <a name="run-tests"></a>Uruchom testy

Aby uruchomić testy, przejdź do **Eksploratora testów** i kliknij przycisk **Uruchom wszystkie**.

![Testowanie Azure Functions przy użyciu języka C# w programie Visual Studio](./media/functions-test-a-function/azure-functions-test-visual-studio-xunit.png)

### <a name="debug-tests"></a>Debuguj testy

Aby debugować testy, ustaw punkt przerwania w teście, przejdź do **Eksploratora testów** i kliknij przycisk **Uruchom > Debuguj ostatnie uruchomienie**.

## <a name="javascript-in-vs-code"></a>Język JavaScript w VS Code

W poniższym przykładzie opisano sposób tworzenia aplikacji funkcji JavaScript w VS Code i uruchamiania oraz [testów za pomocą](https://jestjs.io). Ta procedura powoduje utworzenie Azure Functions za pomocą [rozszerzenia funkcji vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) .

![Testowanie Azure Functions przy użyciu języka JavaScript w programie VS Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="setup"></a>Konfigurowanie

Aby skonfigurować środowisko, zainicjuj nową aplikację Node.js w pustym folderze, uruchamiając polecenie `npm init` .

```bash
npm init -y
```

Następnie zainstaluj program za pomocą następującego polecenia:

```bash
npm i jest
```

Teraz zaktualizuj _package.jsna_ , aby zastąpić istniejące polecenie testowe następującym poleceniem:

```bash
"scripts": {
    "test": "jest"
}
```

### <a name="create-test-modules"></a>Tworzenie modułów testowych

Po zainicjowaniu projektu można utworzyć moduły używane do uruchamiania testów automatycznych. Zacznij od utworzenia nowego folderu o nazwie *test* w celu przechowywania modułów obsługi.

W folderze *testowym* Dodaj nowy plik, nadaj mu nazwę **defaultContext.js** i Dodaj następujący kod:

```javascript
module.exports = {
    log: jest.fn()
};
```

Ten moduł służy do makietowania funkcji *log* w celu reprezentowania domyślnego kontekstu wykonania.

Następnie Dodaj nowy plik, nadaj mu nazwę **defaultTimer.js** i Dodaj następujący kod:

```javascript
module.exports = {
    IsPastDue: false
};
```

Ten moduł implementuje `IsPastDue` Właściwość, która ma stanowić wystąpienie fałszywego czasomierza. Konfiguracje czasomierza, takie jak wyrażenia NCRONTAB, nie są wymagane w tym miejscu, ponieważ zespół testów ma po prostu wywołanie funkcji bezpośrednio w celu przetestowania wyniku.

Następnie użyj rozszerzenia funkcji VS Code, aby [utworzyć nową funkcję http języka JavaScript](/azure/developer/javascript/tutorial-vscode-serverless-node-01) i nadaj jej nazwę *HttpTrigger*. Po utworzeniu funkcji Dodaj nowy plik w tym samym folderze o nazwie **index.test.js** i Dodaj następujący kod:

```javascript
const httpFunction = require('./index');
const context = require('../testing/defaultContext')

test('Http trigger should return known text', async () => {

    const request = {
        query: { name: 'Bill' }
    };

    await httpFunction(context, request);

    expect(context.log.mock.calls.length).toBe(1);
    expect(context.res.body).toEqual('Hello Bill');
});
```

Funkcja HTTP z szablonu zwraca ciąg "Hello" połączonego z nazwą podaną w ciągu zapytania. Ten test tworzy fałszywe wystąpienie żądania i przekazuje je do funkcji HTTP. Test sprawdza, czy metoda *log* jest wywoływana jednokrotnie, a zwracany tekst jest równy "Hello Bill".

Następnie użyj rozszerzenia funkcji VS Code, aby utworzyć nową funkcję czasomierza JavaScript i nadaj jej nazwę *TimerTrigger*. Po utworzeniu funkcji Dodaj nowy plik w tym samym folderze o nazwie **index.test.js** i Dodaj następujący kod:

```javascript
const timerFunction = require('./index');
const context = require('../testing/defaultContext');
const timer = require('../testing/defaultTimer');

test('Timer trigger should log message', () => {
    timerFunction(context, timer);
    expect(context.log.mock.calls.length).toBe(1);
});
```

Funkcja Timer z szablonu rejestruje komunikat na końcu treści funkcji. Ten test zapewnia jednokrotne wywołanie funkcji *log* .

### <a name="run-tests"></a>Uruchom testy

Aby uruchomić testy, naciśnij **klawisze CTRL + ~** , aby otworzyć okno wiersza polecenia, a następnie uruchom polecenie `npm test` :

```bash
npm test
```

![Testowanie Azure Functions przy użyciu języka JavaScript w programie VS Code](./media/functions-test-a-function/azure-functions-test-vs-code-jest.png)

### <a name="debug-tests"></a>Debuguj testy

Aby debugować testy, Dodaj następującą konfigurację do *launch.jsw* pliku:

```json
{
  "type": "node",
  "request": "launch",
  "name": "Jest Tests",
  "disableOptimisticBPs": true,
  "program": "${workspaceRoot}/node_modules/jest/bin/jest.js",
  "args": [
      "-i"
  ],
  "internalConsoleOptions": "openOnSessionStart"
}
```

Następnie ustaw punkt przerwania w teście i naciśnij klawisz **F5**.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak pisać zautomatyzowane testy dla swoich funkcji, przejdź do następujących zasobów:

- [Ręczne uruchamianie funkcji niewyzwalanej przez protokół HTTP](./functions-manually-run-non-http.md)
- [Obsługa błędów Azure Functions](./functions-bindings-error-pages.md)
- [Debugowanie lokalnego wyzwalacza siatki zdarzeń funkcji platformy Azure](./functions-debug-event-grid-trigger-local.md)
