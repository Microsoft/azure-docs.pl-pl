---
title: Współużytkowania wątkowości na platformie Azure Service Fabric
description: Wprowadzenie do współużytkowania wątkowości Service Fabric Reliable Actors, sposób logicznego unikania blokowania na podstawie kontekstu wywołania.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 46ce91e607341e2fbdc0b6a3018e74cb24e76839
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75645535"
---
# <a name="reliable-actors-reentrancy"></a>Reliable Actors współużytkowania wątkowości
Domyślnie środowisko uruchomieniowe Reliable Actors zezwala na współużytkowania wątkowości na kontekst wywołania logicznego. Pozwala to na współużytkowanie aktorów, jeśli znajdują się w tym samym łańcuchu kontekstu wywołania. Na przykład aktor A wysyła komunikat do aktora B, który wysyła komunikat do aktora C. W ramach przetwarzania wiadomości, jeśli aktor w języku C wywołuje aktora A, komunikat jest współużytkowany, więc będzie dozwolony. Wszystkie inne komunikaty, które są częścią innego kontekstu wywołania, będą blokowane na aktora A do momentu zakończenia przetwarzania.

Dostępne są dwie opcje aktora współużytkowania wątkowości zdefiniowane w `ActorReentrancyMode` wyliczeniu:

* `LogicalCallContext`(zachowanie domyślne)
* `Disallowed`-wyłącza współużytkowania wątkowości

```csharp
public enum ActorReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```
```Java
public enum ActorReentrancyMode
{
    LogicalCallContext(1),
    Disallowed(2)
}
```
Współużytkowania wątkowości można skonfigurować w `ActorService`ustawieniach ustawień podczas rejestracji. To ustawienie ma zastosowanie do wszystkich wystąpień aktora utworzonych w usłudze aktora.

W poniższym przykładzie przedstawiono usługę aktora, która ustawia tryb współużytkowania wątkowości na `ActorReentrancyMode.Disallowed`. W takim przypadku, jeśli aktor wysyła komunikat Współużytkowany do innego aktora, zostanie zgłoszony wyjątek typu `FabricException` .

```csharp
static class Program
{
    static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<Actor1>(
                (context, actorType) => new ActorService(
                    context,
                    actorType, () => new Actor1(),
                    settings: new ActorServiceSettings()
                    {
                        ActorConcurrencySettings = new ActorConcurrencySettings()
                        {
                            ReentrancyMode = ActorReentrancyMode.Disallowed
                        }
                    }))
                .GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}
```
```Java
static class Program
{
    static void Main()
    {
        try
        {
            ActorConcurrencySettings actorConcurrencySettings = new ActorConcurrencySettings();
            actorConcurrencySettings.setReentrancyMode(ActorReentrancyMode.Disallowed);

            ActorServiceSettings actorServiceSettings = new ActorServiceSettings();
            actorServiceSettings.setActorConcurrencySettings(actorConcurrencySettings);

            ActorRuntime.registerActorAsync(
                Actor1.getClass(),
                (context, actorType) -> new FabricActorService(
                    context,
                    actorType, () -> new Actor1(),
                    null,
                    stateProvider,
                    actorServiceSettings, timeout);

            Thread.sleep(Long.MAX_VALUE);
        }
        catch (Exception e)
        {
            throw e;
        }
    }
}
```


## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o współużytkowania wątkowości w dokumentacji dotyczącej [interfejsu API aktora](https://msdn.microsoft.com/library/azure/dn971626.aspx)
