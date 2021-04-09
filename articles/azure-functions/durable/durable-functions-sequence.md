---
title: Łańcuch funkcji w Durable Functions — Azure
description: Dowiedz się, jak uruchomić próbkę Durable Functions, która wykonuje sekwencję funkcji.
author: cgillum
ms.topic: conceptual
ms.date: 11/29/2019
ms.author: azfuncdf
ms.openlocfilehash: f8223b1273c2a487e15e3c10d7c6852a119e4cdc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98028254"
---
# <a name="function-chaining-in-durable-functions---hello-sequence-sample"></a>Łańcuch funkcji w Durable Functions-Hello Sequence przykład

Łańcuch funkcji odnosi się do wzorca wykonywania sekwencji funkcji w określonej kolejności. Często dane wyjściowe jednej funkcji muszą zostać zastosowane do danych wejściowych innej funkcji. W tym artykule opisano sekwencję łańcucha utworzoną podczas wykonywania Durable Functions przewodnika Szybki Start ([C#](durable-functions-create-first-csharp.md),  [JavaScript](quickstart-js-vscode.md)lub [Python](quickstart-python-vscode.md)). Aby uzyskać więcej informacji na temat Durable Functions, zobacz [Durable Functions Omówienie](durable-functions-overview.md).

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="the-functions"></a>Funkcje

W tym artykule wyjaśniono następujące funkcje w przykładowej aplikacji:

* `E1_HelloSequence`: [Funkcja programu Orchestrator](durable-functions-bindings.md#orchestration-trigger) , która wywołuje `E1_SayHello` wiele razy w sekwencji. Zapisuje dane wyjściowe `E1_SayHello` wywołań i rejestruje wyniki.
* `E1_SayHello`: [Funkcja działania](durable-functions-bindings.md#activity-trigger) , która dołącza ciąg do "Hello".
* `HttpStart`: Wyzwolono funkcję [klienta trwałego](durable-functions-bindings.md#orchestration-client) http, która uruchamia wystąpienie programu Orchestrator.

### <a name="e1_hellosequence-orchestrator-function"></a>E1_HelloSequence funkcja programu Orchestrator

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=13-25)]

Wszystkie funkcje aranżacji języka C# muszą mieć parametr typu `DurableOrchestrationContext` , który istnieje w `Microsoft.Azure.WebJobs.Extensions.DurableTask` zestawie. Ten obiekt kontekstu umożliwia wywoływanie innych funkcji *działania* i przekazywanie parametrów wejściowych przy użyciu `CallActivityAsync` metody.

Kod wywołuje `E1_SayHello` trzy razy sekwencję z różnymi wartościami parametrów. Wartość zwracana każdego wywołania jest dodawana do `outputs` listy, która jest zwracana na końcu funkcji.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

> [!NOTE]
> Durable Functions JavaScript są dostępne tylko dla funkcji środowiska uruchomieniowego 3,0.

#### <a name="functionjson"></a>function.json

Jeśli używasz Visual Studio Code lub Azure Portal do tworzenia aplikacji, poniżej przedstawiono zawartość *function.jsw* pliku dla funkcji programu Orchestrator. Większośćfunction.jsprogramu Orchestrator *na* plikach wygląda niemal dokładnie tak samo.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/function.json)]

Istotną kwestią jest `orchestrationTrigger` Typ powiązania. Wszystkie funkcje programu Orchestrator muszą używać tego typu wyzwalacza.

> [!WARNING]
> Aby przestrzegać reguły "No we/wy" funkcji programu Orchestrator, nie używaj żadnych powiązań wejściowych ani wyjściowych w przypadku używania `orchestrationTrigger` powiązania wyzwalacza.  Jeśli wymagane są inne powiązania wejściowe lub wyjściowe, powinny one być używane w kontekście `activityTrigger` funkcji, które są wywoływane przez program Orchestrator. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [ograniczeń kodu funkcji programu Orchestrator](durable-functions-code-constraints.md) .

#### <a name="indexjs"></a>index.js


Oto funkcja programu Orchestrator:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_HelloSequence/index.js)]

Wszystkie funkcje aranżacji języka JavaScript muszą zawierać [ `durable-functions` moduł](https://www.npmjs.com/package/durable-functions). Jest to biblioteka, która umożliwia pisanie Durable Functions w języku JavaScript. Istnieją trzy znaczące różnice między funkcją programu Orchestrator i innymi funkcjami języka JavaScript:

1. Funkcja programu Orchestrator jest [funkcją generatora](/scripting/javascript/advanced/iterators-and-generators-javascript).
2. Funkcja jest opakowana w wywołaniu `durable-functions` `orchestrator` metody modułu (tutaj `df` ).
3. Funkcja musi być synchroniczna. Ponieważ metoda "Orchestrator" obsługuje wywoływanie elementu "Context. Done", funkcja powinna po prostu zwracać.

`context`Obiekt zawiera `df` trwały obiekt kontekstu aranżacji, który umożliwia wywoływanie innych funkcji *działania* i przekazywanie parametrów wejściowych przy użyciu `callActivity` metody. Kod wywołuje `E1_SayHello` trzy razy sekwencję z różnymi wartościami parametrów, przy użyciu `yield` do wskazania, że wykonanie powinno oczekiwać na zwrócenie wywołania funkcji działania asynchronicznego. Wartość zwracana każdego wywołania jest dodawana do `outputs` tablicy, która jest zwracana na końcu funkcji.

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> Durable Functions języka Python są dostępne tylko dla funkcji środowiska uruchomieniowego 3,0.


#### <a name="functionjson"></a>function.json

Jeśli używasz Visual Studio Code lub Azure Portal do tworzenia aplikacji, poniżej przedstawiono zawartość *function.jsw* pliku dla funkcji programu Orchestrator. Większośćfunction.jsprogramu Orchestrator *na* plikach wygląda niemal dokładnie tak samo.

[!code-json[Main](~/samples-durable-functions-python/samples/function_chaining/E1_HelloSequence/function.json)]

Istotną kwestią jest `orchestrationTrigger` Typ powiązania. Wszystkie funkcje programu Orchestrator muszą używać tego typu wyzwalacza.

> [!WARNING]
> Aby przestrzegać reguły "No we/wy" funkcji programu Orchestrator, nie używaj żadnych powiązań wejściowych ani wyjściowych w przypadku używania `orchestrationTrigger` powiązania wyzwalacza.  Jeśli wymagane są inne powiązania wejściowe lub wyjściowe, powinny one być używane w kontekście `activityTrigger` funkcji, które są wywoływane przez program Orchestrator. Aby uzyskać więcej informacji, zobacz artykuł dotyczący [ograniczeń kodu funkcji programu Orchestrator](durable-functions-code-constraints.md) .

#### <a name="__init__py"></a>\_\_init \_ \_ . PR

Oto funkcja programu Orchestrator:

[!code-python[Main](~/samples-durable-functions-python/samples/function_chaining/E1_HelloSequence/\_\_init\_\_.py)]

Wszystkie funkcje aranżacji języka Python muszą zawierać [ `durable-functions` pakiet](https://pypi.org/project/azure-functions-durable). Jest to biblioteka, która umożliwia pisanie Durable Functions w języku Python. Istnieją dwie znaczące różnice między funkcją programu Orchestrator i innymi funkcjami języka Python:

1. Funkcja programu Orchestrator jest [funkcją generatora](https://wiki.python.org/moin/Generators).
2. _Plik_ powinien zarejestrować funkcję programu Orchestrator jako koordynator, podając `main = df.Orchestrator.create(<orchestrator function name>)` na końcu pliku. Ułatwia to odróżnienie go od innych, pomocników, funkcji zadeklarowanych w pliku.

`context`Obiekt umożliwia wywoływanie innych funkcji *działania* i przekazywanie parametrów wejściowych przy użyciu `call_activity` metody. Kod wywołuje `E1_SayHello` trzy razy sekwencję z różnymi wartościami parametrów, przy użyciu `yield` do wskazania, że wykonanie powinno oczekiwać na zwrócenie wywołania funkcji działania asynchronicznego. Wartość zwracana każdego wywołania jest zwracana na końcu funkcji.

---

### <a name="e1_sayhello-activity-function"></a>Funkcja działania E1_SayHello

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=27-32)]

Działania korzystają z `ActivityTrigger` atrybutu. Użyj podanej `IDurableActivityContext` do wykonania akcji związanych z działaniami, takich jak uzyskiwanie dostępu do wartości wejściowej przy użyciu `GetInput<T>` .

Implementacja `E1_SayHello` jest stosunkowo prostej operacji formatowania ciągu.

Zamiast tworzyć powiązania z `IDurableActivityContext` , można powiązać bezpośrednio z typem, który jest przesyłany do funkcji działania. Na przykład:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HelloSequence.cs?range=34-38)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/function.jswłączone

*function.jsw* pliku dla funkcji działania `E1_SayHello` jest podobny do tego, z wyjątkiem tego, `E1_HelloSequence` że używa `activityTrigger` typu powiązania zamiast `orchestrationTrigger` typu powiązania.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/function.json)]

> [!NOTE]
> Wszystkie funkcje działania wywoływane przez funkcję aranżacji muszą używać `activityTrigger` powiązania.

Implementacja `E1_SayHello` jest stosunkowo prostej operacji formatowania ciągu.

#### <a name="e1_sayhelloindexjs"></a>E1_SayHello/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E1_SayHello/index.js)]

W przeciwieństwie do funkcji aranżacji funkcja Activity nie wymaga żadnej specjalnej konfiguracji. Dane wejściowe przesłane przez funkcję programu Orchestrator znajdują się na `context.bindings` obiekcie pod nazwą `activityTrigger` powiązania — w tym przypadku `context.bindings.name` . Nazwę powiązania można ustawić jako parametr funkcji wyeksportowanej i uzyskać do niej dostęp bezpośrednio, czyli do czego służy przykładowy kod.

# <a name="python"></a>[Python](#tab/python)

#### <a name="e1_sayhellofunctionjson"></a>E1_SayHello/function.jswłączone

*function.jsw* pliku dla funkcji działania `E1_SayHello` jest podobny do tego, z wyjątkiem tego, `E1_HelloSequence` że używa `activityTrigger` typu powiązania zamiast `orchestrationTrigger` typu powiązania.

[!code-json[Main](~/samples-durable-functions-python/samples/function_chaining/E1_SayHello/function.json)]

> [!NOTE]
> Wszystkie funkcje działania wywoływane przez funkcję aranżacji muszą używać `activityTrigger` powiązania.

Implementacja `E1_SayHello` jest stosunkowo prostej operacji formatowania ciągu.

#### <a name="e1_sayhello__init__py"></a>E1_SayHello/ \_ \_ init \_ \_ . PR

[!code-python[Main](~/samples-durable-functions-python/samples/function_chaining/E1_SayHello/\_\_init\_\_.py)]

W przeciwieństwie do funkcji programu Orchestrator, funkcja Activity nie wymaga żadnej specjalnej konfiguracji. Dane wejściowe przesłane przez funkcję programu Orchestrator są bezpośrednio dostępne jako parametr funkcji.

---

### <a name="httpstart-client-function"></a>HttpStart — funkcja klienta

Wystąpienie funkcji programu Orchestrator można uruchomić przy użyciu funkcji klienta. `HttpStart`Funkcja wyzwalana przez protokół HTTP będzie używana do uruchamiania wystąpień `E1_HelloSequence` .

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/HttpStart.cs?range=13-30)]

Aby można było korzystać z koordynatorów, funkcja musi zawierać `DurableClient` powiązanie wejściowe. Używasz klienta do uruchomienia aranżacji. Może również pomóc zwrócić odpowiedź HTTP zawierającą adresy URL w celu sprawdzenia stanu nowej aranżacji.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

#### <a name="httpstartfunctionjson"></a>HttpStart/function.jswłączone

[!code-json[Main](~/samples-durable-functions/samples/javascript/HttpStart/function.json?highlight=16-20)]

Aby można było korzystać z koordynatorów, funkcja musi zawierać `durableClient` powiązanie wejściowe.

#### <a name="httpstartindexjs"></a>HttpStart/index.js

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/HttpStart/index.js)]

Użyj `df.getClient` , aby uzyskać `DurableOrchestrationClient` obiekt. Używasz klienta do uruchomienia aranżacji. Może również pomóc zwrócić odpowiedź HTTP zawierającą adresy URL w celu sprawdzenia stanu nowej aranżacji.

# <a name="python"></a>[Python](#tab/python)

#### <a name="httpstartfunctionjson"></a>HttpStart/function.jswłączone

[!code-json[Main](~/samples-durable-functions-python/samples/function_chaining/HttpStart/function.json)]

Aby można było korzystać z koordynatorów, funkcja musi zawierać `durableClient` powiązanie wejściowe.

#### <a name="httpstart__init__py"></a>HttpStart/ \_ \_ init \_ \_ . PR

[!code-python[Main](~/samples-durable-functions-python/samples/function_chaining/HttpStart/\_\_init\_\_.py)]

Użyj `DurableOrchestrationClient` konstruktora, aby uzyskać Durable Functions klienta. Używasz klienta do uruchomienia aranżacji. Może również pomóc zwrócić odpowiedź HTTP zawierającą adresy URL w celu sprawdzenia stanu nowej aranżacji.

---

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Aby wykonać `E1_HelloSequence` aranżację, Wyślij do funkcji następujące żądanie HTTP Post `HttpStart` .

```
POST http://{host}/orchestrators/E1_HelloSequence
```

> [!NOTE]
> W poprzednim fragmencie kodu HTTP założono, że w pliku znajduje się wpis, `host.json` który usuwa `api/` prefiks domyślny ze wszystkich adresów URL funkcji wyzwalacza http. Znaczniki tej konfiguracji można znaleźć w `host.json` pliku w przykładach.

Jeśli na przykład używasz przykładu w aplikacji funkcji o nazwie "myfunctionapp", Zastąp ciąg "{host}" opcją "myfunctionapp.azurewebsites.net".

Wynikiem jest odpowiedź HTTP 202, na przykład (przycięty do zwięzłości):

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

W tym momencie aranżacja została umieszczona w kolejce i rozpocznie się natychmiastowo. `Location`Aby sprawdzić stan wykonania, można użyć adresu URL w nagłówku.

```
GET http://{host}/runtime/webhooks/durabletask/instances/96924899c16d43b08a536de376ac786b?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

Wynikiem jest stan aranżacji. Jest ona uruchamiana i trwa szybko, więc zobaczysz ją w stanie *ukończenia* z odpowiedzią, która wygląda następująco (przycięty do zwięzłości):

```
HTTP/1.1 200 OK
Content-Length: 179
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":null,"output":["Hello Tokyo!","Hello Seattle!","Hello London!"],"createdTime":"2017-06-29T05:24:57Z","lastUpdatedTime":"2017-06-29T05:24:59Z"}
```

Jak widać, `runtimeStatus` wystąpienie jest *zakończone* i `output` zawiera wynik Zserializowany notacją JSON wykonywania funkcji programu Orchestrator.

> [!NOTE]
> Możesz zaimplementować podobną, rozglądającą logikę dla innych typów wyzwalaczy, takich jak `queueTrigger` , `eventHubTrigger` , lub `timerTrigger` .

Sprawdź dzienniki wykonywania funkcji. `E1_HelloSequence`Funkcja została uruchomiona i wykonana wiele razy ze względu na zachowanie odtwarzania opisane w temacie [niezawodność aranżacji](durable-functions-orchestrations.md#reliability) . Z drugiej strony istniały tylko trzy wykonania, `E1_SayHello` ponieważ te wykonania funkcji nie są odtwarzane.

## <a name="next-steps"></a>Następne kroki

Ten przykład pokazuje prostą aranżację łańcucha funkcji. Następny przykład pokazuje, jak zaimplementować wzorzec wentylatorów/wentylatorów.

> [!div class="nextstepaction"]
> [Uruchamianie przykładowego Wentylatoru/wentylatoru](durable-functions-cloud-backup.md)
