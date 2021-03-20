---
title: Przewodnik dewelopera dotyczący trwałych jednostek w programie .NET — Azure Functions
description: Jak korzystać z jednostek trwałych w programie .NET przy użyciu rozszerzenia Durable Functions Azure Functions.
author: sebastianburckhardt
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: azfuncdf
ms.openlocfilehash: 88d2a23104b67dae8fd480406eb9171e9f3d5652
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92740012"
---
# <a name="developers-guide-to-durable-entities-in-net"></a>Przewodnik dewelopera dotyczący trwałych jednostek w programie .NET

W tym artykule opisano interfejsy dostępne do tworzenia trwałych jednostek z platformą .NET, w tym przykłady i ogólne porady. 

Funkcje jednostki udostępniają deweloperom aplikacji bezserwerowych wygodny sposób organizowania stanu aplikacji jako kolekcji szczegółowych jednostek. Aby uzyskać szczegółowe informacje na temat podstawowych pojęć, zobacz artykuł [trwałe jednostki: pojęcia](durable-functions-entities.md) .

Obecnie oferujemy dwa interfejsy API do definiowania jednostek:

- **Składnia oparta na klasie** reprezentuje jednostki i operacje jako klasy i metody. Ta składnia daje łatwy do odczytu kod i umożliwia wywoływanie operacji w sposób sprawdzany przez interfejsy. 

- **Składnia oparta na funkcjach** jest interfejsem niższego poziomu, który reprezentuje jednostki jako funkcje. Zapewnia precyzyjną kontrolę nad sposobem wysyłania operacji jednostki oraz sposobie zarządzania stanem jednostki.  

Ten artykuł koncentruje się głównie na składni opartej na klasie, ponieważ oczekujemy, że będzie lepiej dostosowany do większości aplikacji. Jednak [składnia oparta na funkcjach](#function-based-syntax) może być odpowiednia dla aplikacji, które chcą definiować własne abstrakcje stanu jednostki i operacji oraz nimi zarządzać. Ponadto może być odpowiednie do zaimplementowania bibliotek, które wymagają generyczności, która nie jest obecnie obsługiwana przez składnię opartą na klasie. 

> [!NOTE]
> Składnia oparta na klasie jest po prostu warstwą na podstawie składni opartej na funkcjach, tak aby obie warianty mogły być używane zamiennie w tej samej aplikacji. 
 
## <a name="defining-entity-classes"></a>Definiowanie klas jednostek

Poniższy przykład jest implementacją `Counter` jednostki, która przechowuje pojedynczą wartość typu integer i oferuje cztery operacje `Add` , `Reset` , `Get` , i `Delete` .

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int Value { get; set; }

    public void Add(int amount) 
    {
        this.Value += amount;
    }

    public Task Reset() 
    {
        this.Value = 0;
        return Task.CompletedTask;
    }

    public Task<int> Get() 
    {
        return Task.FromResult(this.Value);
    }

    public void Delete() 
    {
        Entity.Current.DeleteState();
    }

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

`Run`Funkcja zawiera typowe wymagania dotyczące korzystania z składni opartej na klasie. Musi to być *statyczna* funkcja platformy Azure. Jest ono wykonywane raz dla każdego komunikatu operacji, który jest przetwarzany przez jednostkę. Gdy `DispatchAsync<T>` jest wywoływana i jednostka nie znajduje się już w pamięci, konstruuje obiekt typu `T` i wypełnia pola z ostatniego utrwalonego JSON znalezionego w magazynie (jeśli istnieje). Następnie wywołuje metodę o pasującej nazwie.

> [!NOTE]
> Stan jednostki opartej na klasie jest **tworzony niejawnie** przed przetwarzaniem operacji przez jednostkę i można ją **usunąć jawnie** w operacji, wywołując metodę `Entity.Current.DeleteState()` .

### <a name="class-requirements"></a>Wymagania dotyczące klas
 
Klasy jednostek to POCOs (zwykłe stare obiekty CLR), które nie wymagają specjalnych klas, interfejsów ani atrybutów. Ale

- Klasa musi być konstrukcyjną (zobacz [konstrukcja jednostki](#entity-construction)).
- Klasa musi być możliwa do serializacji JSON (zobacz [Serializacja jednostki](#entity-serialization)).

Ponadto wszelkie metody, które mają być wywoływane jako operacje, muszą spełniać dodatkowe wymagania:

- Operacja musi mieć co najwyżej jeden argument i nie może mieć żadnych przeciążeń lub argumentów typu ogólnego.
- Operacja przeznaczona do wywołania z aranżacji przy użyciu interfejsu musi zwracać `Task` lub `Task<T>` .
- Argumenty i zwracane wartości muszą być wartościami możliwymi do serializacji lub obiektami.

### <a name="what-can-operations-do"></a>Do czego służą operacje?

Wszystkie operacje jednostki mogą odczytywać i aktualizować stan jednostki, a zmiany stanu są automatycznie utrwalane w magazynie. Ponadto działania mogą wykonywać zewnętrzne operacje we/wy lub inne obliczenia w ramach ogólnych limitów wspólnych dla wszystkich Azure Functions.

Operacje mają również dostęp do funkcji zapewnianych przez `Entity.Current` kontekst:

* `EntityName`: nazwa aktualnie wykonywanej jednostki.
* `EntityKey`: klucz aktualnie wykonywanej jednostki.
* `EntityId`: Identyfikator aktualnie wykonywanej jednostki (w tym nazwa i klucz).
* `SignalEntity`: wysyła jednokierunkową wiadomość do jednostki.
* `CreateNewOrchestration`: uruchamia nową aranżację.
* `DeleteState`: usuwa stan tej jednostki.

Można na przykład zmodyfikować jednostkę licznika, aby rozpocząć aranżację, gdy licznik osiągnie wartość 100 i przekaże identyfikator jednostki jako argument wejściowy:

```csharp
    public void Add(int amount) 
    {
        if (this.Value < 100 && this.Value + amount >= 100)
        {
            Entity.Current.StartNewOrchestration("MilestoneReached", Entity.Current.EntityId);
        }
        this.Value += amount;      
    }
```

## <a name="accessing-entities-directly"></a>Bezpośredni dostęp do jednostek

Dostęp do jednostek opartych na klasie można uzyskać bezpośrednio przy użyciu jawnych nazw ciągów dla jednostki i jej operacji. Udostępniamy kilka przykładów poniżej. Aby uzyskać dokładniejsze wyjaśnienie podstawowych koncepcji (takich jak sygnały i wywołania), zobacz dyskusje w [jednostkach dostępu](durable-functions-entities.md#access-entities). 

> [!NOTE]
> Tam, gdzie to możliwe, zalecamy [dostęp do jednostek za poorednictwem interfejsów](#accessing-entities-through-interfaces), ponieważ zapewnia ona większą kontrolę typów.

### <a name="example-client-signals-entity"></a>Przykład: klient sygnalizuje jednostkę

Następująca funkcja http platformy Azure implementuje operację usuwania przy użyciu konwencji REST. Wysyła sygnał Delete do jednostki licznika, której klucz jest przesyłany w ścieżce adresu URL.

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync(entityId, "Delete");    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

### <a name="example-client-reads-entity-state"></a>Przykład: klient odczytuje stan jednostki

Następująca funkcja http platformy Azure implementuje operację GET przy użyciu konwencji REST. Odczytuje bieżący stan jednostki licznika, której klucz jest przesyłany w ścieżce adresu URL.

```csharp
[FunctionName("GetCounter")]
public static async Task<HttpResponseMessage> GetCounter(
    [HttpTrigger(AuthorizationLevel.Function, "get", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    var state = await client.ReadEntityStateAsync<Counter>(entityId); 
    return req.CreateResponse(state);
}
```

> [!NOTE]
> Obiekt zwrócony przez `ReadEntityStateAsync` jest tylko kopią lokalną, czyli migawką stanu jednostki od pewnego wcześniejszego punktu w czasie. W szczególności może być nieodświeżone i modyfikowanie tego obiektu nie ma wpływu na rzeczywistą jednostkę. 

### <a name="example-orchestration-first-signals-then-calls-entity"></a>Przykład: pierwsze sygnały aranżacji, a następnie wywołuje jednostkę

Następująca organizacja sygnalizuje jednostce licznika, aby ją zwiększyć, a następnie wywołuje tę samą jednostkę w celu odczytania jej najnowszej wartości.

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");

    // One-way signal to the entity - does not await a response
    context.SignalEntity(entityId, "Add", 1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");

    return currentValue;
}
```

## <a name="accessing-entities-through-interfaces"></a>Uzyskiwanie dostępu do jednostek za poorednictwem interfejsów

Interfejsy mogą służyć do uzyskiwania dostępu do jednostek za pośrednictwem wygenerowanych obiektów serwera proxy. Takie podejście zapewnia, że nazwa i typ argumentu operacji są zgodne z implementacją. Zalecamy używanie interfejsów do uzyskiwania dostępu do jednostek, gdy jest to możliwe.

Przykładowo można zmodyfikować przykład licznika w następujący sposób:

```csharp
public interface ICounter
{
    void Add(int amount);
    Task Reset();
    Task<int> Get();
    void Delete();
}

public class Counter : ICounter
{
    ...
}
```

Klasy jednostek i interfejsy jednostek są podobne do interfejsów ziaren i ziarna, które są popularne przez [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/). Aby uzyskać więcej informacji na temat podobieństw i różnic między trwałymi jednostkami i Orleans, zobacz [porównanie z aktorami wirtualnymi](durable-functions-entities.md#comparison-with-virtual-actors).

Poza zapewnieniem kontroli typów interfejsy są przydatne do lepszego oddzielenia problemów w aplikacji. Na przykład, ponieważ jednostka może zaimplementować wiele interfejsów, pojedyncza jednostka może obsłużyć wiele ról. Ponadto, ponieważ interfejs może być zaimplementowany przez wiele jednostek, wzorce komunikacji ogólnej można zaimplementować jako biblioteki wielokrotnego użytku.

### <a name="example-client-signals-entity-through-interface"></a>Przykład: klient sygnalizuje jednostkę za pośrednictwem interfejsu

Kod klienta może służyć `SignalEntityAsync<TEntityInterface>` do wysyłania sygnałów do jednostek, które implementują `TEntityInterface` . Na przykład:

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync<ICounter>(entityId, proxy => proxy.Delete());    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

W tym przykładzie `proxy` parametr jest dynamicznie generowanym wystąpieniem `ICounter` , które wewnętrznie tłumaczy wywołanie na `Delete` sygnał.

> [!NOTE]
> `SignalEntityAsync`Interfejsów API można używać tylko w przypadku operacji jednokierunkowych. Nawet w przypadku, gdy operacja zwraca `Task<T>` , wartość `T` parametru zawsze będzie równa null lub `default` nie jest rzeczywistym wynikiem.
Na przykład nie ma sensu sygnalizowanie `Get` operacji, ponieważ nie jest zwracana żadna wartość. Zamiast tego klienci mogą `ReadStateAsync` uzyskać dostęp do stanu licznika bezpośrednio lub uruchomić funkcję programu Orchestrator, która wywołuje `Get` operację. 

### <a name="example-orchestration-first-signals-then-calls-entity-through-proxy"></a>Przykład: pierwsze sygnały aranżacji, a następnie wywołuje jednostkę za pośrednictwem serwera proxy

Aby wywoływać lub sygnalizować jednostkę z poziomu aranżacji, `CreateEntityProxy` można jej użyć wraz z typem interfejsu, aby wygenerować serwer proxy dla jednostki. Ten serwer proxy może być następnie używany do wywoływania lub wykonywania sygnałów:

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");
    var proxy = context.CreateEntityProxy<ICounter>(entityId);

    // One-way signal to the entity - does not await a response
    proxy.Add(1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await proxy.Get();

    return currentValue;
}
```

Niejawnie wszystkie operacje, które zwracają, `void` są sygnalizowane i wszelkie operacje, które zwracają `Task` lub `Task<T>` są wywoływane. Jeden może zmienić to zachowanie domyślne i operacje sygnałów nawet wtedy, gdy zwracają zadanie, przy użyciu `SignalEntity<IInterfaceType>` metody jawnie.

### <a name="shorter-option-for-specifying-the-target"></a>Krótsza opcja określania celu

Podczas wywoływania lub sygnalizowania jednostki za pomocą interfejsu, pierwszy argument musi określać jednostkę docelową. Element docelowy można określić przez określenie identyfikatora jednostki lub, w przypadkach, gdy istnieje tylko jedna klasa implementująca jednostkę, po prostu klucz jednostki:

```csharp
context.SignalEntity<ICounter>(new EntityId(nameof(Counter), "myCounter"), ...);
context.SignalEntity<ICounter>("myCounter", ...);
```

Jeśli jest określony tylko klucz jednostki i w czasie wykonywania nie można odnaleźć unikatowej implementacji, `InvalidOperationException` jest generowany. 

### <a name="restrictions-on-entity-interfaces"></a>Ograniczenia dotyczące interfejsów jednostek

Jak zwykle, wszystkie typy parametrów i zwracanych muszą być serializowane w formacie JSON. W przeciwnym razie wyjątki serializacji są generowane w czasie wykonywania.

Wymuszamy również kilka dodatkowych reguł:
* Interfejsy jednostek mogą definiować tylko metody.
* Interfejsy jednostek nie mogą zawierać parametrów ogólnych.
* Metody interfejsu jednostki nie mogą mieć więcej niż jednego parametru.
* Metody interfejsu jednostki muszą zwracać `void` , `Task` lub `Task<T>` 

Jeśli którykolwiek z tych reguł zostanie naruszony, `InvalidOperationException` jest generowany w czasie wykonywania, gdy interfejs jest używany jako argument typu do `SignalEntity` lub `CreateProxy` . Komunikat o wyjątku wyjaśnia, która reguła została przerwana.

> [!NOTE]
> Metody interfejsu zwracające `void` można sygnalizować tylko (jednokierunkowe), niewywoływane (dwukierunkowe). Metody interfejsu zwracają `Task` lub `Task<T>` mogą być wywoływane lub sygnalizowane. Jeśli zostanie wywołana, zwracają wynik operacji lub ponownie generują wyjątki zgłoszone przez operację. Jednak w przypadku, gdy są sygnalizowane, nie zwracają rzeczywistego wyniku lub wyjątku z operacji, ale tylko wartości domyślnej.

## <a name="entity-serialization"></a>Serializacja jednostki

Ponieważ stan jednostki jest utrwalony jako trwale, Klasa jednostki musi być możliwa do serializacji. W tym celu środowisko uruchomieniowe Durable Functions używa biblioteki [JSON.NET](https://www.newtonsoft.com/json) , która obsługuje wiele zasad i atrybutów sterujących procesem serializacji i deserializacji. Najczęściej używane typy danych języka C# (w tym tablice i typy kolekcji) są już serializowane i mogą być łatwo używane do definiowania stanu trwałych jednostek.

Na przykład Json.NET może łatwo serializować i deserializować następujące klasy:

```csharp
[JsonObject(MemberSerialization = MemberSerialization.OptIn)]
public class User
{
    [JsonProperty("name")]
    public string Name { get; set; }

    [JsonProperty("yearOfBirth")]
    public int YearOfBirth { get; set; }

    [JsonProperty("timestamp")]
    public DateTime Timestamp { get; set; }

    [JsonProperty("contacts")]
    public Dictionary<Guid, Contact> Contacts { get; set; } = new Dictionary<Guid, Contact>();

    [JsonObject(MemberSerialization = MemberSerialization.OptOut)]
    public struct Contact
    {
        public string Name;
        public string Number;
    }

    ...
}
```

### <a name="serialization-attributes"></a>Atrybuty serializacji

W powyższym przykładzie wybieramy kilka atrybutów, aby zapewnić, że bazowa Serializacja jest bardziej widoczna:
- Dodajemy do klasy adnotację z, `[JsonObject(MemberSerialization.OptIn)]` aby przypominać nam, że klasa musi być serializowana i aby zachować tylko elementy członkowskie, które są jawnie oznaczone jako właściwości JSON.
-  Adnotuj pola, które mają być utrwalane `[JsonProperty("name")]` , aby przypominać, że pole jest częścią trwałego stanu jednostki i określić nazwę właściwości, która ma być używana w reprezentacji JSON.

Jednak te atrybuty nie są wymagane; inne konwencje lub atrybuty są dozwolone, o ile pracują z Json.NET. Na przykład jeden może używać `[DataContract]` atrybutów lub nie ma żadnych atrybutów w ogóle:

```csharp
[DataContract]
public class Counter
{
    [DataMember]
    public int Value { get; set; }
    ...
}

public class Counter
{
    public int Value;
    ...
}
```

Domyślnie nazwa klasy *nie* jest przechowywana jako część reprezentacji JSON: to jest, używane `TypeNameHandling.None` jako ustawienie domyślne. To zachowanie domyślne można przesłonić przy użyciu `JsonObject` `JsonProperty` atrybutów lub.

### <a name="making-changes-to-class-definitions"></a>Wprowadzanie zmian w definicjach klas

Przed wprowadzeniem zmian w definicji klasy po uruchomieniu aplikacji należy zwrócić uwagę, ponieważ zapisany obiekt JSON nie jest już zgodny z nową definicją klasy. W dalszym ciągu jest często możliwe poprawność w przypadku zmiany formatów danych, o ile jedna z nich rozumie proces deserializacji używany przez `JsonConvert.PopulateObject` .

Na przykład poniżej przedstawiono kilka przykładów zmian i ich wpływu:

1. Jeśli dodawana jest nowa właściwość, która nie jest obecna w przechowywanym formacie JSON, zakłada jej wartość domyślną.
1. Jeśli właściwość zostanie usunięta, która jest obecna w zapisanym formacie JSON, poprzednia zawartość zostanie utracona.
1. W przypadku zmiany nazwy właściwości efekt jest taki jak usunięcie starego i dodanie nowego.
1. Jeśli typ właściwości zostanie zmieniony, dlatego nie można już przeprowadzić deserializacji z zapisanego pliku JSON, zostanie zgłoszony wyjątek.
1. Jeśli typ właściwości zostanie zmieniony, ale nadal będzie można go zdeserializować z zapisanego kodu JSON, zostanie to zrobione.

Istnieje wiele opcji dostosowywania zachowania Json.NET. Na przykład, aby wymusić wyjątek, jeśli przechowywany kod JSON zawiera pole, którego nie ma w klasie, określ atrybut `JsonObject(MissingMemberHandling = MissingMemberHandling.Error)` . Istnieje również możliwość napisania niestandardowego kodu do deserializacji, który może odczytywać dane JSON przechowywane w dowolnych formatach.

## <a name="entity-construction"></a>Konstrukcja jednostki

Czasami chcemy mieć większą kontrolę nad sposobem konstruowania obiektów Entity. Teraz opisano kilka opcji zmiany domyślnego zachowania podczas konstruowania obiektów Entity. 

### <a name="custom-initialization-on-first-access"></a>Inicjowanie niestandardowe przy pierwszym dostępie

Czasami konieczne jest wykonanie pewnej inicjalizacji specjalnej przed wysłaniem operacji do jednostki, do której nigdy nie uzyskano dostępu lub który został usunięty. Aby określić takie zachowanie, jeden może dodać warunek warunkowy przed `DispatchAsync` :

```csharp
[FunctionName(nameof(Counter))]
public static Task Run([EntityTrigger] IDurableEntityContext ctx)
{
    if (!ctx.HasState)
    {
        ctx.SetState(...);
    }
    return ctx.DispatchAsync<Counter>();
}
```

### <a name="bindings-in-entity-classes"></a>Powiązania w klasach jednostek

W przeciwieństwie do funkcji regularnych, metody klasy jednostek nie mają bezpośredniego dostępu do powiązań wejściowych i wyjściowych. Zamiast tego, dane wiążące muszą być przechwytywane w deklaracji funkcji punktu wejścia, a następnie przekazywać do `DispatchAsync<T>` metody. Wszystkie obiekty, do których przechodzą, `DispatchAsync<T>` zostaną automatycznie przesłane do konstruktora klasy jednostki jako argument.

Poniższy przykład pokazuje, jak `CloudBlobContainer` odwołanie z [powiązania danych wejściowych obiektu BLOB](../functions-bindings-storage-blob-input.md) może zostać udostępnione jednostce opartej na klasie.

```csharp
public class BlobBackedEntity
{
    [JsonIgnore]
    private readonly CloudBlobContainer container;

    public BlobBackedEntity(CloudBlobContainer container)
    {
        this.container = container;
    }

    // ... entity methods can use this.container in their implementations ...
    
    [FunctionName(nameof(BlobBackedEntity))]
    public static Task Run(
        [EntityTrigger] IDurableEntityContext context,
        [Blob("my-container", FileAccess.Read)] CloudBlobContainer container)
    {
        // passing the binding object as a parameter makes it available to the
        // entity class constructor
        return context.DispatchAsync<BlobBackedEntity>(container);
    }
}
```

Aby uzyskać więcej informacji na temat powiązań w Azure Functions, zapoznaj się z dokumentacją [Azure Functions wyzwalacze i powiązania](../functions-triggers-bindings.md) .

### <a name="dependency-injection-in-entity-classes"></a>Iniekcja zależności w klasach jednostek

Klasy jednostek obsługują [iniekcję zależności Azure Functions](../functions-dotnet-dependency-injection.md). Poniższy przykład pokazuje, jak zarejestrować `IHttpClientFactory` usługę w jednostce opartej na klasie.

```csharp
[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
        }
    }
}
```

W poniższym fragmencie kodu pokazano, jak dołączyć wstrzykiwaną usługę do klasy Entity.

```csharp
public class HttpEntity
{
    [JsonIgnore]
    private readonly HttpClient client;

    public HttpEntity(IHttpClientFactory factory)
    {
        this.client = factory.CreateClient();
    }

    public Task<int> GetAsync(string url)
    {
        using (var response = await this.client.GetAsync(url))
        {
            return (int)response.StatusCode;
        }
    }

    [FunctionName(nameof(HttpEntity))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<HttpEntity>();
}
```

> [!NOTE]
> Aby uniknąć problemów z serializacją, upewnij się, że wykluczono pola przeznaczone do przechowywania wprowadzonych wartości z serializacji.

> [!NOTE]
> W przeciwieństwie do używania iniekcji konstruktora w zwykłych Azure Functions .NET, Metoda punktu wejścia funkcji dla jednostek opartych na klasie *musi* być zadeklarowana `static` . Deklarowanie niestatycznego punktu wejścia funkcji może spowodować konflikty między normalnym inicjatorem obiektu Azure Functions i inicjatorem obiektów trwałe jednostki.

## <a name="function-based-syntax"></a>Składnia oparta na funkcjach

Do tej pory koncentrujemy się na składni opartej na klasie, ponieważ oczekujemy, że będzie ona lepiej dopasowana do większości aplikacji. Jednak składnia oparta na funkcjach może być odpowiednia dla aplikacji, które chcą definiować własne abstrakcje stanu jednostki i operacji oraz nimi zarządzać. Ponadto może być odpowiednie w przypadku implementowania bibliotek, które wymagają generyczności, która nie jest obecnie obsługiwana przez składnię opartą na klasie. 

Przy użyciu składni opartej na funkcjach funkcja Entity jawnie obsługuje wysyłanie operacji i jawnie zarządza stanem jednostki. Na przykład poniższy kod pokazuje jednostkę *licznika* zaimplementowaną przy użyciu składni opartej na funkcji.  

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
        case "delete":
            ctx.DeleteState();
            break;
    }
}
```

### <a name="the-entity-context-object"></a>Obiekt kontekstu jednostki

Dostęp do funkcji specyficznych dla jednostki można uzyskać za pośrednictwem obiektu kontekstu typu `IDurableEntityContext` . Ten obiekt kontekstu jest dostępny jako parametr funkcji Entity i za pośrednictwem właściwości Async-Local `Entity.Current` .

Poniższe elementy członkowskie zawierają informacje o bieżącej operacji i umożliwiają określenie wartości zwracanej. 

* `EntityName`: nazwa aktualnie wykonywanej jednostki.
* `EntityKey`: klucz aktualnie wykonywanej jednostki.
* `EntityId`: Identyfikator aktualnie wykonywanej jednostki (w tym nazwa i klucz).
* `OperationName`: nazwa bieżącej operacji.
* `GetInput<TInput>()`: Pobiera dane wejściowe dla bieżącej operacji.
* `Return(arg)`: zwraca wartość aranżacji, która wywołała operację.

Następujący członkowie zarządzają stanem jednostki (tworzenie, Odczyt, aktualizowanie, usuwanie). 

* `HasState`: wskazuje, czy jednostka istnieje, czy ma jakiś stan. 
* `GetState<TState>()`: Pobiera bieżący stan jednostki. Jeśli jeszcze nie istnieje, zostanie utworzony.
* `SetState(arg)`: tworzy lub aktualizuje stan jednostki.
* `DeleteState()`: usuwa stan jednostki, jeśli istnieje. 

Jeśli stan zwrócony przez `GetState` to obiekt, może on być bezpośrednio modyfikowany przez kod aplikacji. Nie ma potrzeby `SetState` ponownego wywołania na końcu (ale również bez szkody). Jeśli `GetState<TState>` jest wywoływana wiele razy, należy użyć tego samego typu.

Na koniec następujące elementy członkowskie są używane do sygnalizowania innych jednostek lub uruchamiania nowych aranżacji:

* `SignalEntity(EntityId, operation, input)`: wysyła jednokierunkową wiadomość do jednostki.
* `CreateNewOrchestration(orchestratorFunctionName, input)`: uruchamia nową aranżację.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Informacje o pojęciach dotyczących jednostek](durable-functions-entities.md)
