---
title: Trwałe jednostki — Azure Functions
description: Dowiedz się, co to są trwałe jednostki i jak ich używać w rozszerzeniu Durable Functions Azure Functions.
author: cgillum
ms.topic: overview
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: c898444659c2ce071163e9ab774a4534f8c51a9c
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/11/2021
ms.locfileid: "102632054"
---
# <a name="entity-functions"></a>Funkcje jednostki

Funkcje Entity definiują operacje umożliwiające odczytywanie i aktualizowanie małych fragmentów stanu, znanych jako *jednostek trwałych*. Podobnie jak funkcje programu Orchestrator, funkcje jednostki są funkcjami o specjalnym typie wyzwalacza, *wyzwalaczem jednostki*. W przeciwieństwie do funkcji programu Orchestrator, funkcja Entity Functions zarządza stanem jednostki jawnie, a nie niejawnie reprezentującą stan za pośrednictwem przepływu sterowania.
Jednostki zapewniają metodę skalowania aplikacji przez dystrybuowanie pracy między wieloma jednostkami, z których każdy ma stan o nieokreślonym rozmiarze.

> [!NOTE]
> Funkcje jednostki i powiązane funkcje są dostępne tylko w [Durable Functions 2,0](durable-functions-versions.md#migrate-from-1x-to-2x) i nowszych. Są one obecnie obsługiwane w językach .NET, JavaScript i Python.

## <a name="general-concepts"></a>Pojęcia ogólne

Jednostki zachowują się jak małe usługi, które komunikują się za pośrednictwem komunikatów. Każda jednostka ma unikatową tożsamość i stan wewnętrzny (jeśli istnieje). Podobnie jak w przypadku usług lub obiektów, jednostki wykonują operacje po wyświetleniu monitu. Gdy operacja jest wykonywana, może ona zaktualizować stan wewnętrzny jednostki. Może również wywołać usługi zewnętrzne i poczekać na odpowiedź. Jednostki komunikują się z innymi jednostkami, aranżacjami i klientami przy użyciu komunikatów, które są niejawnie wysyłane przez niezawodne kolejki. 

Aby zapobiec konfliktom, wszystkie operacje na pojedynczej jednostce są gwarantowane do wykonania szeregowego, czyli jeden po drugim.

> [!NOTE]
> Gdy wywoływana jest jednostka, przetwarza jej ładunek do ukończenia, a następnie planuje nowe wykonywanie do aktywacji po nadejściu przyszłych wejść. W związku z tym dzienniki wykonywania jednostek mogą pokazać dodatkowe wykonanie po każdym wywołaniu jednostki; jest to oczekiwane.

### <a name="entity-id"></a>Identyfikator jednostki
Do jednostek uzyskuje się dostęp za pośrednictwem unikatowego identyfikatora, *identyfikatora jednostki*. Identyfikator jednostki to po prostu para ciągów, które jednoznacznie identyfikują wystąpienie jednostki. Składa się z:

* **Nazwa jednostki**, która jest nazwą identyfikującą typ jednostki. Przykładem jest "licznik". Ta nazwa musi być zgodna z nazwą funkcji jednostki implementującej jednostkę. Wielkość liter nie jest uwzględniana.
* **Klucz jednostki**, który jest ciągiem, który jednoznacznie identyfikuje jednostkę między wszystkimi innymi jednostkami o tej samej nazwie. Przykładem jest identyfikator GUID.

Na przykład `Counter` Funkcja Entity może być używana do przechowywania wyników w grze online. Każde wystąpienie gry ma unikatowy identyfikator jednostki, na przykład `@Counter@Game1` i `@Counter@Game2` . Wszystkie operacje przeznaczone dla określonej jednostki wymagają określenia identyfikatora jednostki jako parametru.

### <a name="entity-operations"></a>Operacje na jednostkach ###

Aby wywołać operację na jednostce, określ:

* **Identyfikator jednostki** docelowej.
* **Nazwa operacji**, która jest ciągiem, który określa operację do wykonania. Na przykład `Counter` Jednostka może obsługiwać `add` , `get` , lub `reset` operacje.
* **Wejście operacji**, który jest opcjonalnym parametrem wejściowym dla operacji. Na przykład operacja dodawania może przyjmować liczbę całkowitą jako dane wejściowe.
* **Zaplanowany czas**, który jest opcjonalnym parametrem służącym do określania czasu dostarczania operacji. Na przykład operacja może być niezawodnie zaplanowana do uruchomienia kilku dni w przyszłości.

Operacje mogą zwracać wartość wyniku lub wynik błędu, takie jak błąd JavaScript lub wyjątek programu .NET. Ten wynik lub błąd może być zauważalny przez aranżacje, które wywołały operację.

Operacja jednostki może również tworzyć, odczytywać, aktualizować i usuwać stan jednostki. Stan jednostki jest zawsze trwale trwały w magazynie.

## <a name="define-entities"></a>Definiowanie jednostek

Obecnie są dostępne dwa różne interfejsy API służące do definiowania jednostek:

**Składnia oparta na funkcjach**, gdzie jednostki są reprezentowane jako funkcje i operacje są jawnie wysyłane przez aplikację. Ta składnia działa dobrze w przypadku jednostek z prostym stanem, kilkoma operacjami lub dynamicznym zestawem operacji takich jak w strukturach aplikacji. Ta składnia może być żmudnym, ponieważ nie przechwytuje błędów typu w czasie kompilacji.

**Składnia oparta na klasie (tylko platforma .NET)**, gdzie jednostki i operacje są reprezentowane przez klasy i metody. Ta składnia daje łatwiejszy do odczytu kod i umożliwia wywoływanie operacji w sposób bezpieczny dla typu. Składnia oparta na klasie jest cienką warstwą na podstawie składni opartej na funkcjach, dlatego można używać obu wariantów zamiennie w tej samej aplikacji.

# <a name="c"></a>[C#](#tab/csharp)

### <a name="example-function-based-syntax---c"></a>Przykład: Składnia oparta na funkcjach-C #

Poniższy kod jest przykładem prostej `Counter` jednostki wdrożonej jako funkcja trwała. Ta funkcja definiuje trzy operacje, `add` , `reset` , i `get` , z których każda działa na stanie liczby całkowitej.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>());
            break;
    }
}
```

Aby uzyskać więcej informacji na temat składni opartej na funkcjach i korzystania z niej, zobacz [składnia oparta na funkcjach](durable-functions-dotnet-entities.md#function-based-syntax).

### <a name="example-class-based-syntax---c"></a>Przykład: Składnia oparta na klasie-C #

Poniższy przykład jest równoważną implementacją `Counter` jednostki przy użyciu klas i metod.

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;

    public void Reset() => this.CurrentValue = 0;

    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

Stan tej jednostki jest obiektem typu `Counter` , który zawiera pole przechowujące bieżącą wartość licznika. Aby zachować ten obiekt w magazynie, jest on serializowany i deserializowany przez bibliotekę [JSON.NET](https://www.newtonsoft.com/json) . 

Aby uzyskać więcej informacji na temat składni opartej na klasie i korzystania z niej, zobacz [Definiowanie klas jednostek](durable-functions-dotnet-entities.md#defining-entity-classes).

# <a name="javascript"></a>[JavaScript](#tab/javascript)

### <a name="example-javascript-entity"></a>Przykład: obiekt JavaScript

Trwałe jednostki są dostępne w języku JavaScript, począwszy  od wersji 1.3.0 `durable-functions` pakietu npm. Poniższy kod jest `Counter` jednostką zaimplementowaną jako funkcja trwała zapisywana w języku JavaScript.

**Licznik/function.jsna**
```json
{
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

**Licznik/index.js**
```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```
# <a name="python"></a>[Python](#tab/python)

### <a name="example-python-entity"></a>Przykład: jednostka języka Python

Poniższy kod jest `Counter` jednostką zaimplementowaną jako funkcja trwała zapisywana w języku Python.

**Licznik/function.jsna**
```json
{
  "scriptFile": "__init__.py",
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ]
}
```

**Licznik/__init__. PR**
```Python
import azure.functions as func
import azure.durable_functions as df


def entity_function(context: df.DurableEntityContext):
    current_value = context.get_state(lambda: 0)
    operation = context.operation_name
    if operation == "add":
        amount = context.get_input()
        current_value += amount
    elif operation == "reset":
        current_value = 0
    elif operation == "get":
        context.set_result(current_value)
    context.set_state(current_value)


main = df.Entity.create(entity_function)
```
---

## <a name="access-entities"></a>Jednostki dostępu

Dostęp do jednostek można uzyskać przy użyciu jednej lub dwukierunkowej komunikacji. Poniższe terminologia odróżnia dwie formy komunikacji: 

* **Wywołanie** jednostki używa komunikacji dwukierunkowej (rundy). Wysyłasz komunikat operacji do jednostki, a następnie poczekaj na komunikat odpowiedzi przed kontynuowaniem. Komunikat odpowiedzi może podawać wartość wyniku lub wynik błędu, taki jak błąd JavaScript lub wyjątek programu .NET. Ten wynik lub błąd jest następnie zauważalny przez obiekt wywołujący.
* **Sygnalizowanie** jednostki używa komunikacji jednokierunkowej (Fire i zapomnij). Wysyłasz komunikat operacji, ale nie czekaj na odpowiedź. Gdy komunikat jest gwarantowany w końcu, nadawca nie wie, kiedy i nie może obserwować żadnej wartości wyniku ani błędów.

Dostęp do jednostek można uzyskać z poziomu funkcji klienta, z poziomu funkcji programu Orchestrator lub z poziomu funkcji Entity. Nie wszystkie formy komunikacji są obsługiwane przez wszystkie konteksty:

* Z poziomu klientów można sygnalizować jednostki i odczytać stan jednostki.
* Z poziomu aranżacji można sygnalizować jednostki i można wywoływać jednostki.
* Z poziomu jednostek można sygnalizować jednostki.

W poniższych przykładach przedstawiono różne sposoby uzyskiwania dostępu do jednostek.

### <a name="example-client-signals-an-entity"></a>Przykład: klient sygnalizuje jednostkę

Aby uzyskać dostęp do jednostek z zwykłej funkcji platformy Azure, która jest również znana jako funkcja klienta, należy użyć [powiązania klienta jednostki](durable-functions-bindings.md#entity-client). Poniższy przykład pokazuje funkcję wyzwalaną przez kolejkę przy użyciu tego powiązania.

# <a name="c"></a>[C#](#tab/csharp)

> [!NOTE]
> Dla uproszczenia w poniższych przykładach przedstawiono składnię o jednoznacznie określonym typie na potrzeby uzyskiwania dostępu do jednostek. Ogólnie rzecz biorąc, zalecamy [dostęp do jednostek za za poorednictwem interfejsów](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) , ponieważ zapewnia ona większą kontrolę typów.

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await client.signalEntity(entityId, "add", 1);
};
```

# <a name="python"></a>[Python](#tab/python)

```Python
from azure.durable_functions import DurableOrchestrationClient
import azure.functions as func


async def main(req: func.HttpRequest, starter: str, message):
    client = DurableOrchestrationClient(starter)
    entityId = df.EntityId("Counter", "myCounter")
    await client.signal_entity(entityId, "add", 1)
```

---

Termin " *sygnał* " oznacza, że wywołanie interfejsu API jednostki jest jednokierunkowe i asynchroniczne. Nie jest możliwe, aby funkcja klienta wiedziała, kiedy jednostka przetworzyła operację. Ponadto funkcja klienta nie może obserwować żadnych wartości wyników ani wyjątków. 

### <a name="example-client-reads-an-entity-state"></a>Przykład: klient odczytuje stan jednostki

Funkcje klienta programu mogą również wysyłać zapytania o stan jednostki, jak pokazano w następującym przykładzie:

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("QueryCounter")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function)] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");
    EntityStateResponse<JObject> stateResponse = await client.ReadEntityStateAsync<JObject>(entityId);
    return req.CreateResponse(HttpStatusCode.OK, stateResponse.EntityState);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    const stateResponse = await client.readEntityState(entityId);
    return stateResponse.entityState;
};
```

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> Język Python nie obsługuje obecnie odczytywania Stanów jednostek z klienta. Zamiast tego użyj programu Orchestrator `callEntity` .

---

Zapytania o stan jednostki są wysyłane do magazynu trwałego śledzenia i zwracają ostatnio utrwalony stan jednostki. Ten stan jest zawsze "przydzielony", oznacza to, że nigdy nie jest tymczasowy stan pośredni przyjęty w trakcie wykonywania operacji. Jednak jest możliwe, że ten stan jest nieodświeżony w porównaniu do stanu w pamięci jednostki. Tylko aranżacje mogą odczytywać stan w pamięci jednostki, zgodnie z opisem w następnej sekcji.

### <a name="example-orchestration-signals-and-calls-an-entity"></a>Przykład: sygnały aranżacji i wywołania jednostki

Funkcje programu Orchestrator mogą uzyskiwać dostęp do jednostek przy użyciu interfejsów API w ramach [powiązania wyzwalacza aranżacji](durable-functions-bindings.md#orchestration-trigger). Poniższy przykładowy kod pokazuje funkcję programu Orchestrator wywołującą i sygnalizującą `Counter` jednostkę.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // One-way signal to the entity which updates the value - does not await a response
        context.SignalEntity(entityId, "Add", 1);
    }
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const entityId = new df.EntityId("Counter", "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    currentValue = yield context.df.callEntity(entityId, "get");
});
```

> [!NOTE]
> Język JavaScript nie obsługuje obecnie sygnalizowania jednostką od koordynatora. Zamiast tego użyj polecenia cmdlet `callEntity`.

# <a name="python"></a>[Python](#tab/python)

```Python
import azure.functions as func
import azure.durable_functions as df


def orchestrator_function(context: df.DurableOrchestrationContext):
    entityId = df.EntityId("Counter", "myCounter")
    current_value = yield context.call_entity(entityId, "get")
    if current_value < 10:
        context.signal_entity(entityId, "add", 1)
    return state
```

---

Tylko aranżacje mogą wywołać jednostki i uzyskać odpowiedź, co może być wartością zwracaną lub wyjątkiem. Funkcje klienta, które używają [powiązania klienta](durable-functions-bindings.md#entity-client) , mogą jedynie sygnalizować jednostki.

> [!NOTE]
> Wywołanie jednostki z funkcji programu Orchestrator jest podobne do wywołania [funkcji działania](durable-functions-types-features-overview.md#activity-functions) z funkcji programu Orchestrator. Główną różnicą jest to, że funkcje jednostki są trwałymi obiektami o adresie, który jest IDENTYFIKATORem jednostki. Funkcja Entity Functions obsługuje określanie nazwy operacji. Funkcje działania, z drugiej strony, są bezstanowe i nie mają koncepcji operacji.

### <a name="example-entity-signals-an-entity"></a>Przykład: jednostka sygnalizuje jednostkę

Funkcja Entity może wysyłać sygnały do innych jednostek, a nawet do siebie, podczas gdy wykonuje operację.
Na przykład można zmodyfikować poprzedni `Counter` przykład jednostki, aby wysyłał sygnał "punkt kontrolny" do pewnej jednostki monitora, gdy licznik osiągnie wartość 100.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
   case "add":
        var currentValue = ctx.GetState<int>();
        var amount = ctx.GetInput<int>();
        if (currentValue < 100 && currentValue + amount >= 100)
        {
            ctx.SignalEntity(new EntityId("MonitorEntity", ""), "milestone-reached", ctx.EntityKey);
        }

        ctx.SetState(currentValue + amount);
        break;
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
    case "add":
        const amount = context.df.getInput();
        if (currentValue < 100 && currentValue + amount >= 100) {
            const entityId = new df.EntityId("MonitorEntity", "");
            context.df.signalEntity(entityId, "milestone-reached", context.df.instanceId);
        }
        context.df.setState(currentValue + amount);
        break;
```

# <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> Język Python nie obsługuje jeszcze sygnałów Entity-Entity. Zamiast tego należy użyć programu Orchestrator do sygnalizowania jednostek.

---

## <a name="entity-coordination-currently-net-only"></a><a name="entity-coordination"></a>Koordynacja jednostek (obecnie tylko platforma .NET)

Mogą wystąpić sytuacje, w których trzeba skoordynować operacje w wielu jednostkach. Na przykład w aplikacji bankowej mogą istnieć jednostki reprezentujące poszczególne konta bankowe. W przypadku przenoszenia funduszy z jednego konta do innego należy upewnić się, że konto źródłowe ma wystarczające środki. Należy również upewnić się, że aktualizacje zarówno dla konta źródłowego, jak i docelowego są wykonywane w sposób spójny transakcyjnie.

### <a name="example-transfer-funds-c"></a>Przykład: transfer środków (C#)

Poniższy przykładowy kod transferuje fundusze między dwiema jednostkami kont przy użyciu funkcji programu Orchestrator. Koordynacja aktualizacji jednostek wymaga użycia `LockAsync` metody do utworzenia _sekcji krytycznej_ w aranżacji.

> [!NOTE]
> Dla uproszczenia w tym przykładzie użyto `Counter` wcześniej zdefiniowanej jednostki. W rzeczywistej aplikacji lepiej jest zdefiniować bardziej szczegółową `BankAccount` jednostkę.

```csharp
// This is a method called by an orchestrator function
public static async Task<bool> TransferFundsAsync(
    string sourceId,
    string destinationId,
    int transferAmount,
    IDurableOrchestrationContext context)
{
    var sourceEntity = new EntityId(nameof(Counter), sourceId);
    var destinationEntity = new EntityId(nameof(Counter), destinationId);

    // Create a critical section to avoid race conditions.
    // No operations can be performed on either the source or
    // destination accounts until the locks are released.
    using (await context.LockAsync(sourceEntity, destinationEntity))
    {
        ICounter sourceProxy = 
            context.CreateEntityProxy<ICounter>(sourceEntity);
        ICounter destinationProxy =
            context.CreateEntityProxy<ICounter>(destinationEntity);

        int sourceBalance = await sourceProxy.Get();

        if (sourceBalance >= transferAmount)
        {
            await sourceProxy.Add(-transferAmount);
            await destinationProxy.Add(transferAmount);

            // the transfer succeeded
            return true;
        }
        else
        {
            // the transfer failed due to insufficient funds
            return false;
        }
    }
}
```

W programie .NET program `LockAsync` zwraca `IDisposable` , co spowoduje zakończenie sekcji krytycznej po jej zlikwidowaniu. Tego `IDisposable` wyniku można używać razem z `using` blokiem, aby uzyskać składniową reprezentację sekcji krytycznej.

W poprzednim przykładzie funkcja programu Orchestrator przesłała fundusze z jednostki źródłowej do jednostki docelowej. `LockAsync`Metoda została zablokowana zarówno dla jednostki konta źródłowego, jak i docelowego. To blokowanie zapewnia, że żaden inny klient nie może wykonać zapytania lub zmodyfikować stanu jednego z kont, dopóki logika aranżacji nie zakończyła sekcji krytycznej na końcu `using` instrukcji. Takie zachowanie zapobiega możliwości przekroczenia liczby projektów z konta źródłowego.

> [!NOTE] 
> Gdy aranżacja kończy się, zwykle lub z powodu błędu, wszelkie krytyczne sekcje w toku są niejawnie zakończone i wszystkie blokady są zwalniane.

### <a name="critical-section-behavior"></a>Zachowanie sekcji krytycznej

`LockAsync`Metoda tworzy sekcję krytyczną w aranżacji. Te krytyczne sekcje uniemożliwiają innym aranżacjom wprowadzanie nakładających się zmian do określonego zestawu jednostek. Wewnętrznie `LockAsync` interfejs API wysyła do jednostek operacje "Lock" i zwraca, gdy odbierze komunikat odpowiedzi "Zablokuj pobrany" z każdej z tych samych jednostek. Blokady i odblokowywanie to wbudowane operacje obsługiwane przez wszystkie jednostki.

Żadna operacja z innych klientów nie jest dozwolona w jednostce, gdy jest w stanie zablokowanym. Takie zachowanie zapewnia, że tylko jedno wystąpienie aranżacji może blokować jednostkę jednocześnie. Jeśli obiekt wywołujący podejmie próbę wywołania operacji na jednostce, gdy jest ona zablokowana przez aranżację, ta operacja zostanie umieszczona w kolejce oczekujących operacji. Żadne oczekujące operacje nie są przetwarzane do momentu, gdy organizacja holdingowa zwolni blokadę.

> [!NOTE] 
> To zachowanie różni się nieco od elementów pierwotnych synchronizacji używanych w większości języków programowania, takich jak `lock` instrukcja w języku C#. Na przykład w języku C# `lock` instrukcja musi być używana przez wszystkie wątki, aby zapewnić poprawną synchronizację w wielu wątkach. Jednostki nie wymagają jednak, aby wszyscy wywołujący jawnie blokowały jednostkę. Jeśli jakikolwiek obiekt wywołujący blokuje jednostkę, wszystkie pozostałe operacje na tej jednostce są blokowane i umieszczane w kolejce w tle.

Blokady jednostek są trwałe, więc zachowują się nawet wtedy, gdy proces wykonywany jest odtwarzany. Blokady są wewnętrznie utrwalane jako część trwałego stanu jednostki.

W przeciwieństwie do transakcji sekcje krytyczne nie są automatycznie wycofywane w przypadku błędów. Zamiast tego, wszelkie obsłudze błędów, takie jak wycofywanie lub ponawianie próby, muszą być jawnie kodowane, na przykład przez przechwytywanie błędów lub wyjątków. Ten wybór projektu jest zamierzony. Automatyczne wycofywanie wszystkich efektów aranżacji jest trudne lub niemożliwe, ponieważ aranżacje mogą uruchamiać działania i wykonywać wywołania do zewnętrznych usług, których nie można cofnąć. Ponadto próby wycofania mogą zakończyć się niepowodzeniem i wymagać dalszej obsługi błędów.

### <a name="critical-section-rules"></a>Reguły sekcji krytycznej

W przeciwieństwie do elementów podstawowych blokowania niskiego poziomu w większości języków programowania, sekcje krytyczne *nie są zagwarantowane*. Aby zapobiec zakleszczeniom, Wymuś następujące ograniczenia: 

* Sekcje krytyczne nie mogą być zagnieżdżane.
* Sekcje krytyczne nie mogą tworzyć podaranżacji.
* Sekcje krytyczne mogą wywoływać tylko jednostki, które zostały zablokowane.
* Sekcje krytyczne nie mogą wywoływać tej samej jednostki przy użyciu wielu wywołań równoległych.
* Sekcje krytyczne mogą sygnalizować tylko jednostki, które nie zostały zablokowane.

Wszelkie naruszenia tych reguł powodują wystąpienie błędu czasu wykonywania, na przykład `LockingRulesViolationException` w programie .NET, który zawiera komunikat objaśniający, która reguła została przerwana.

## <a name="comparison-with-virtual-actors"></a>Porównanie z aktorami wirtualnymi

Wiele funkcji jednostek trwałych jest inspirowany [modelem aktora](https://en.wikipedia.org/wiki/Actor_model). Jeśli znasz już uczestników, możesz rozpoznać wiele pojęć opisanych w tym artykule. Trwałe jednostki są szczególnie podobne do [aktorów wirtualnych](https://research.microsoft.com/projects/orleans/)lub ziaren, które są popularne przez [projekt Orleans](http://dotnet.github.io/orleans/). Na przykład:

* Jednostki trwałe są adresowane za pośrednictwem identyfikatora jednostki.
* Trwałe operacje jednostki wykonują szeregowo, pojedynczo, aby uniknąć sytuacji wyścigu.
* Trwałe jednostki są tworzone niejawnie po wywołaniu lub zasygnalizowaniu.
* W przypadku braku wykonywania operacji trwałe jednostki są dyskretnie zwalniane z pamięci.

Istnieją pewne istotne różnice, które warto zwrócić uwagę:

* Trwałe jednostki ustalają priorytety trwałości przed opóźnieniami i dlatego mogą nie być odpowiednie dla aplikacji z rygorystycznymi wymaganiami opóźnienia.
* Jednostki trwałe nie mają wbudowanych limitów czasu dla komunikatów. W programie Orleans wszystkie komunikaty przekroczyły limit czasu, gdy zostanie skonfigurowany czas. Wartość domyślna to 30 sekund.
* Komunikaty wysyłane między jednostkami są dostarczane w sposób niezawodny i w kolejności. W Orleans, niezawodne lub uporządkowane dostarczanie jest obsługiwane w przypadku zawartości wysyłanej za poorednictwem strumieni, ale nie jest gwarantowane dla wszystkich komunikatów między ziarnami.
* Wzorce żądania-odpowiedzi w jednostkach są ograniczone do aranżacji. W obrębie jednostek dozwolony jest tylko jednokierunkowa obsługa komunikatów (nazywanych również sygnalizowaniem), jak w oryginalnym modelu aktora i w przeciwieństwie do ziaren w Orleans. 
* Niezakleszczenie jednostek trwałych. W Orleans, zakleszczenie mogą wystąpić i nie są rozwiązywane, dopóki komunikaty przekroczą limit czasu.
* Jednostki trwałe mogą być używane w połączeniu z trwałymi aranżacjami i obsługują mechanizmy blokowania rozproszonego. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zapoznaj się z przewodnikiem dewelopera w przypadku trwałych jednostek w programie .NET](durable-functions-dotnet-entities.md)

> [!div class="nextstepaction"]
> [Informacje o centrach zadań](durable-functions-task-hubs.md)
