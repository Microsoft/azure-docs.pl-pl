---
title: Opracowywanie testów jednostkowych dla usług stanowych
description: Dowiedz się więcej na temat testów jednostkowych w usłudze Azure Service Fabric dla usług stanowych i szczególnych zagadnień, które należy wziąć pod uwagę podczas opracowywania.
ms.topic: conceptual
ms.date: 09/04/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: 287c0544daa3c44d91fd336b502c496b9b4bb266
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89011399"
---
# <a name="create-unit-tests-for-stateful-services"></a>Tworzenie testów jednostkowych dla usług stanowych
Testy jednostkowe Service Fabric usługi stanowe nie obejmują częstych pomyłek, które nie muszą być przechwytywane przez konwencjonalne aplikacje lub testy jednostkowe specyficzne dla domeny. Opracowując testy jednostkowe dla usług stanowych, należy pamiętać o pewnych szczególnych kwestiach.

1. Każda replika wykonuje kod aplikacji, ale w innym kontekście. Jeśli usługa używa trzech replik, kod usługi jest wykonywany na trzech węzłach równolegle pod innym kontekstem/rolą.
2. Stan przechowywany w usłudze stanowej powinien być spójny między wszystkimi replikami. Menedżer stanu i niezawodne kolekcje zapewniają tę spójność. Jednak stan w pamięci będzie musiał być zarządzany przez kod aplikacji.
3. Każda replika zmieni role w pewnym momencie podczas uruchamiania w klastrze. Replika pomocnicza stanie się podstawowym, w przypadku gdy węzeł hostujący podstawowy staje się niedostępny lub przeciążony. Jest to naturalne zachowanie dla Service Fabric dlatego usługi muszą zaplanować ostatecznie wykonywanie w ramach innej roli.

W tym artykule przyjęto założenie, że [testy jednostkowe usług stanowych w Service Fabric](service-fabric-concepts-unit-testing.md) zostały odczytane.

## <a name="the-servicefabricmocks-library"></a>Biblioteka servicefabric. imitacje
W wersji 3.3.0, [servicefabric. makiety](https://www.nuget.org/packages/ServiceFabric.Mocks/) udostępniają interfejs API do tworzenia aranżacji replik i zarządzania stanem. Ta wartość zostanie użyta w przykładach.

Pakiet [NuGet](https://www.nuget.org/packages/ServiceFabric.Mocks/) 
 [GitHub](https://github.com/loekd/ServiceFabric.Mocks)

*Webfabric. imitacje nie należą do firmy Microsoft ani nie są przez nią obsługiwane. Jest to jednak obecnie zalecana Biblioteka firmy Microsoft do testowania jednostkowego usług stanowych.*

## <a name="set-up-the-mock-orchestration-and-state"></a>Konfigurowanie aranżacji i stanu makiety
W ramach części porządkowanie testu zostanie utworzony zestaw replik i Menedżer stanu. Zestaw replik będzie tworzyć wystąpienie przetestowanej usługi dla każdej repliki. Będzie on również wykonywał wykonywanie zdarzeń cyklu życia, takich jak `OnChangeRole` i `RunAsync` . Menedżer stanu makiety zapewni, że wszystkie operacje wykonywane względem menedżera stanu zostaną uruchomione i zachowane jako rzeczywisty Menedżer stanu.

1. Utwórz delegata fabryki usługi, który będzie tworzyć wystąpienia testowanej usługi. Powinno to być podobne lub takie samo jak wywołanie zwrotne fabryki usługi zwykle dostępne w `Program.cs` usłudze dla Service Fabric lub aktora. Powinno to być zgodne z następującym podpisem:
   ```csharp
   MyStatefulService CreateMyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica2 stateManager)
   ```
2. Utwórz wystąpienie `MockReliableStateManager` klasy. Spowoduje to zasymulować wszystkie interakcje z menedżerem stanu.
3. Utwórz wystąpienie, `MockStatefulServiceReplicaSet<TStatefulService>` gdzie `TStatefulService` jest typem testowanej usługi. Spowoduje to wymaganie delegata utworzonego w kroku #1 i menedżera stanu uruchomionego w #2
4. Dodaj repliki do zestawu replik. Określ rolę (na przykład Primary, ActiveSecondary, IdleSecondary) i identyfikator repliki
   > Zaczekaj na identyfikatory replik! Te elementy będą prawdopodobnie używane podczas działania i potwierdzeń części testu jednostkowego.

```csharp
//service factory to instruct how to create the service instance
var serviceFactory = (StatefulServiceContext context, IReliableStateManagerReplica2 stateManager) => new MyStatefulService(context, stateManager);
//instantiate a new mock state manager
var stateManager = new MockReliableStateManager();
//instantiate a new replica set with the service factory and state manager
var replicaSet = new MockStatefulServiceReplicaSet<MyStatefulService>(CreateStatefulService, stateManager);
//add a new Primary replica with id 1
await replicaSet.AddReplicaAsync(ReplicaRole.Primary, 1);
//add a new ActiveSecondary replica with id 2
await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 2);
//add a second ActiveSecondary replica with id 3
await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 3);
```

## <a name="execute-service-requests"></a>Żądania wykonania usługi
Żądania obsługi mogą być wykonywane w określonej replice przy użyciu wygodnych właściwości i wyszukiwań.
```csharp
const string stateName = "test";
var payload = new Payload(StatePayload);

//execute a request on the primary replica using
await replicaSet.Primary.ServiceInstance.InsertAsync(stateName, payload);

//execute a request against replica with id 2
await replicaSet[2].ServiceInstance.InsertAsync(stateName, payload);

//execute a request against one of the active secondary replicas
await replicaSet.FirstActiveSecondary.InsertAsync(stateName, payload);
```

## <a name="execute-a-service-move"></a>Wykonywanie przenoszenia usługi
Zestaw replik makiety uwidacznia kilka wygodnych metod wyzwalania różnych typów przenoszonych usług.
```csharp
//promote the first active secondary to primary
replicaSet.PromoteNewReplicaToPrimaryAsync();
//promote the secondary with replica id 4 to primary
replicaSet.PromoteNewReplicaToPrimaryAsync(4);

//promote the first idle secondary to an active secondary
PromoteIdleSecondaryToActiveSecondaryAsync();
//promote idle secondary with replica id 4 to active secondary
PromoteIdleSecondaryToActiveSecondaryAsync(4);

//add a new replica with randomly assigned replica id and promote it to primary
PromoteNewReplicaToPrimaryAsync()
//add a new replica with replica id 4 and promote it to primary
PromoteNewReplicaToPrimaryAsync(4)
```

## <a name="putting-it-all-together"></a>Zebranie wszystkich elementów
Poniższy test pokazuje skonfigurowanie zestawu replik z trzema węzłami i sprawdzanie, czy dane są dostępne z poziomu pomocniczego po zmianie roli. Typowym problemem może być to, że dane dodane podczas `InsertAsync` zapisywania zostały zapisane w pamięci lub w niezawodnej kolekcji bez uruchamiania programu `CommitAsync` . W obu przypadkach pomocnicza nie jest zsynchronizowana z serwerem podstawowym. Mogłoby to spowodować niespójne odpowiedzi po przeniesieniu usługi.

```csharp
[TestMethod]
public async Task TestServiceState_InMemoryState_PromoteActiveSecondary()
{
    var stateManager = new MockReliableStateManager();
    var replicaSet = new MockStatefulServiceReplicaSet<MyStatefulService>(CreateStatefulService, stateManager);
    await replicaSet.AddReplicaAsync(ReplicaRole.Primary, 1);
    await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 2);
    await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 3);

    const string stateName = "test";
    var payload = new Payload(StatePayload);

    //insert data
    await replicaSet.Primary.ServiceInstance.InsertAsync(stateName, payload);
    //promote one of the secondaries to primary
    await replicaSet.PromoteActiveSecondaryToPrimaryAsync(2);
    //get data
    var payloads = (await replicaSet.Primary.ServiceInstance.GetPayloadsAsync()).ToList();

    //data should match what was inserted against the primary
    Assert.IsTrue(payloads.Count == 1);
    Assert.IsTrue(payloads[0].Content == payload.Content);

    //verify the data was saved against the reliable dictionary
    var dictionary = await StateManager.GetOrAddAsync<IReliableDictionary<string, Payload>>(MyStatefulService.StateManagerDictionaryKey);
    using(var tx = StateManager.CreateTransaction())
    {
        var payload = await dictionary.TryGetValue(stateName);
        Assert.IsTrue(payload.HasValue);
        Assert.IsTrue(payload.Value.Content == payload.Content);
    }
}
```

## <a name="next-steps"></a>Następne kroki
Informacje na temat testowania [komunikacji między](service-fabric-testability-scenarios-service-communication.md) usługami i [symulowania błędów przy użyciu kontrolowanego chaos](service-fabric-controlled-chaos.md).
