---
title: Zarządzanie stanem Service Fabric platformy Azure
description: Informacje o uzyskiwaniu dostępu, zapisywania i usuwania stanu dla niezawodnego aktora Service Fabric platformy Azure oraz zagadnienia dotyczące projektowania aplikacji.
ms.topic: conceptual
ms.date: 03/19/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 1e796232719342883efc2aa5dd377d586f3039ad
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96571321"
---
# <a name="access-save-and-remove-reliable-actors-state"></a>Dostęp, zapisywanie i usuwanie stanu Reliable Actors
[Reliable Actors](service-fabric-reliable-actors-introduction.md) są obiektami jednowątkowymi, które mogą w sposób niezawodny hermetyzować logikę i stan i zachować stan. Każde wystąpienie aktora ma swój własny [Menedżer stanu](service-fabric-reliable-actors-state-management.md): strukturę danych przypominającą słownik, która w niezawodny sposób przechowuje pary klucz/wartość. Menedżer stanu jest otoką wokół dostawcy stanu. Można jej użyć do przechowywania danych bez względu na to, które [ustawienie trwałości](service-fabric-reliable-actors-state-management.md#state-persistence-and-replication) jest używane.

Klucze menedżera stanu muszą być ciągami. Wartości są ogólne i mogą być dowolnego typu, w tym typów niestandardowych. Wartości przechowywane w Menedżerze Stanów muszą być serializowane kontraktu danych, ponieważ mogą być przesyłane przez sieć do innych węzłów podczas replikacji i mogą być zapisywane na dysku w zależności od ustawienia trwałości stanu aktora.

Menedżer stanu udostępnia typowe metody słownika do zarządzania stanem, podobnie jak te znajdujące się w niezawodnym słowniku.

Aby uzyskać więcej informacji, zobacz [najlepsze rozwiązania dotyczące zarządzania stanem aktora](service-fabric-reliable-actors-state-management.md#best-practices).

## <a name="access-state"></a>Stan dostępu
Dostęp do stanu jest uzyskiwany za pomocą menedżera stanu według klucza. Metody menedżera stanu są wszystkie asynchroniczne, ponieważ mogą wymagać wejścia/wyjścia dysku, gdy aktory mają stan trwały. Po pierwszym dostępie obiekty stanu są buforowane w pamięci. Powtarzaj dostęp do obiektów Access Objects bezpośrednio z pamięci i zwracają synchronicznie, bez ponoszenia obciążenia we/wy dysku lub asynchronicznego przełączenia kontekstu. Obiekt State zostaje usunięty z pamięci podręcznej w następujących przypadkach:

* Metoda aktor zgłasza nieobsługiwany wyjątek po pobraniu obiektu z menedżera stanu.
* Aktor jest ponownie uaktywniany, Po zdezaktywowaniu lub po wystąpieniu błędu.
* Stan strony dostawcy stanu na dysk. To zachowanie zależy od implementacji dostawcy stanu. Domyślnym dostawcą stanu dla tego `Persisted` Ustawienia jest to zachowanie.

Stan można pobrać przy użyciu standardowej operacji *pobierania* , która zgłasza `KeyNotFoundException` (C#) lub `NoSuchElementException` (Java), Jeśli wpis nie istnieje dla klucza:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<int> GetCountAsync()
    {
        return this.StateManager.GetStateAsync<int>("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().getStateAsync("MyState");
    }
}
```

Możesz również pobrać stan przy użyciu metody *TryGet* , która nie zgłasza, jeśli dla klucza nie istnieje wpis:

```csharp
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task<int> GetCountAsync()
    {
        ConditionalValue<int> result = await this.StateManager.TryGetStateAsync<int>("MyState");
        if (result.HasValue)
        {
            return result.Value;
        }

        return 0;
    }
}
```
```Java
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().<Integer>tryGetStateAsync("MyState").thenApply(result -> {
            if (result.hasValue()) {
                return result.getValue();
            } else {
                return 0;
            });
    }
}
```

## <a name="save-state"></a>Zapisz stan
Metody pobierania menedżera stanu zwracają odwołanie do obiektu w pamięci lokalnej. Zmodyfikowanie tego obiektu w lokalnej pamięci nie powoduje jego zapisania trwale. Gdy obiekt jest pobierany z menedżera stanu i modyfikowany, musi zostać ponownie wstawiony do menedżera stanu w celu zapisania trwale.

Stan można wstawić przy użyciu *zestawu* bezwarunkowego, który jest odpowiednikiem `dictionary["key"] = value` składni:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task SetCountAsync(int value)
    {
        return this.StateManager.SetStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture setCountAsync(int value)
    {
        return this.stateManager().setStateAsync("MyState", value);
    }
}
```

Stan można dodać za pomocą metody *Add* . Ta metoda zgłasza `InvalidOperationException` (C#) lub `IllegalStateException` (Java), gdy próbuje dodać klucz, który już istnieje.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task AddCountAsync(int value)
    {
        return this.StateManager.AddStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().addOrUpdateStateAsync("MyState", value, (key, old_value) -> old_value + value);
    }
}
```

Możesz również dodać stan przy użyciu metody *TryAdd* . Ta metoda nie jest zgłaszana, gdy próbuje dodać klucz, który już istnieje.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task AddCountAsync(int value)
    {
        bool result = await this.StateManager.TryAddStateAsync<int>("MyState", value);

        if (result)
        {
            // Added successfully!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().tryAddStateAsync("MyState", value).thenApply((result)->{
            if(result)
            {
                // Added successfully!
            }
        });
    }
}
```

Na końcu metody aktora Menedżer stanu automatycznie zapisuje wszystkie wartości, które zostały dodane lub zmodyfikowane przez operację wstawiania lub aktualizacji. "Save" może obejmować utrwalanie dysków i replikacji w zależności od użytych ustawień. Wartości, które nie zostały zmodyfikowane, nie są utrwalane ani replikowane. Jeśli żadne wartości nie zostały zmodyfikowane, operacja zapisywania nie robi nic. Jeśli zapisanie nie powiedzie się, zmodyfikowany stan zostanie odrzucony i zostanie ponownie załadowany pierwotny stan.

Można również zapisać stan ręcznie, wywołując `SaveStateAsync` metodę na podstawie aktora:

```csharp
async Task IMyActor.SetCountAsync(int count)
{
    await this.StateManager.AddOrUpdateStateAsync("count", count, (key, value) => count > value ? count : value);

    await this.SaveStateAsync();
}
```
```Java
interface MyActor {
    CompletableFuture setCountAsync(int count)
    {
        this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value).thenApply();

        this.stateManager().saveStateAsync().thenApply();
    }
}
```

## <a name="remove-state"></a>Usuń stan
Stan można trwale usunąć z menedżera stanu aktora, wywołując metodę *Remove* . Ta metoda zgłasza `KeyNotFoundException` (C#) lub `NoSuchElementException` (Java), gdy próbuje usunąć klucz, który nie istnieje.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task RemoveCountAsync()
    {
        return this.StateManager.RemoveStateAsync("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().removeStateAsync("MyState");
    }
}
```

Stan można również trwale usunąć za pomocą metody *TryRemove* . Ta metoda nie jest zgłaszana, gdy próbuje usunąć klucz, który nie istnieje.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task RemoveCountAsync()
    {
        bool result = await this.StateManager.TryRemoveStateAsync("MyState");

        if (result)
        {
            // State removed!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().tryRemoveStateAsync("MyState").thenApply((result)->{
            if(result)
            {
                // State removed!
            }
        });
    }
}
```

## <a name="next-steps"></a>Następne kroki

Stan, który jest przechowywany w Reliable Actors musi być serializowany przed zapisem na dysku i replikowany w celu zapewnienia wysokiej dostępności. Dowiedz się więcej o [serializacji typu aktora](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Następnie Dowiedz się więcej na temat [diagnostyki aktora i monitorowania wydajności](service-fabric-reliable-actors-diagnostics.md).
