---
title: Zdarzenia w aktorach Service Fabric platformy Azure opartych na aktorach
description: Informacje o zdarzeniach dotyczących Reliable Actors Service Fabric, efektywny sposób komunikowania się między aktorem a klientem.
ms.topic: conceptual
ms.date: 10/06/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 591e3539715ba0873e44f56a93d05df6552bb1ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96571372"
---
# <a name="actor-events"></a>Zdarzenia aktora
Zdarzenia aktora umożliwiają wysyłanie powiadomień o najlepszych nakładów od aktora do klientów. Zdarzenia aktora są przeznaczone do komunikacji między komputerami i nie powinny być używane do komunikacji między aktorami.

Poniższe fragmenty kodu pokazują, jak używać zdarzeń aktora w aplikacji.

Zdefiniuj interfejs, który opisuje zdarzenia opublikowane przez aktora. Ten interfejs musi być pochodny `IActorEvents` interfejsu. Argumenty metod muszą być [serializowane kontraktu danych](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). Metody muszą zwracać typ void, ponieważ powiadomienia o zdarzeniach są jednym sposobem i najlepszym nakładem pracy.

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```
```Java
public interface GameEvents implements ActorEvents
{
    void gameScoreUpdated(UUID gameId, String currentScore);
}
```
Zadeklaruj zdarzenia opublikowane przez aktora w interfejsie aktora.

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    Task<string> GetGameScore();
}
```
```Java
public interface GameActor extends Actor, ActorEventPublisherE<GameEvents>
{
    CompletableFuture<?> updateGameStatus(GameStatus status);

    CompletableFuture<String> getGameScore();
}
```
Po stronie klienta Zaimplementuj procedurę obsługi zdarzeń.

```csharp
class GameEventsHandler : IGameEvents
{
    public void GameScoreUpdated(Guid gameId, string currentScore)
    {
        Console.WriteLine(@"Updates: Game: {0}, Score: {1}", gameId, currentScore);
    }
}
```

```Java
class GameEventsHandler implements GameEvents {
    public void gameScoreUpdated(UUID gameId, String currentScore)
    {
        System.out.println("Updates: Game: "+gameId+" ,Score: "+currentScore);
    }
}
```

Na kliencie Utwórz serwer proxy do aktora, który publikuje zdarzenie i subskrybuje jego zdarzenia.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

```Java
GameActor actorProxy = ActorProxyBase.create<GameActor>(GameActor.class, new ActorId(UUID.fromString(args)));

return ActorProxyEventUtility.subscribeAsync(actorProxy, new GameEventsHandler());
```

W przypadku pracy awaryjnej aktor może przechodzić w tryb failover do innego procesu lub węzła. Serwer proxy aktora zarządza aktywnymi subskrypcjami i automatycznie subskrybuje je. Interwał ponownej subskrypcji można kontrolować za pomocą `ActorProxyEventExtensions.SubscribeAsync<TEvent>` interfejsu API. Aby anulować subskrypcję, użyj `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` interfejsu API.

Na aktora Opublikuj zdarzenia w miarę ich występowania. Jeśli istnieją Subskrybenci dla zdarzenia, środowisko uruchomieniowe aktorów wysyła je do powiadomienia.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```
```Java
GameEvents event = getEvent<GameEvents>(GameEvents.class);
event.gameScoreUpdated(Id.getUUIDId(), score);
```


## <a name="next-steps"></a>Następne kroki
* [Współużytkowania wątkowości aktora](service-fabric-reliable-actors-reentrancy.md)
* [Diagnostyka aktora i monitorowanie wydajności](service-fabric-reliable-actors-diagnostics.md)
* [Dokumentacja interfejsu API aktora](/previous-versions/azure/dn971626(v=azure.100))
* [Przykładowy kod w języku C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Przykładowy kod w języku C# .NET Core](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)
* [Przykładowy kod w języku Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)
