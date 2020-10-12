---
title: Reliable Actors uwagi dotyczące serializacji typu aktora
description: Omawia podstawowe wymagania dotyczące definiowania klas możliwych do serializacji, których można użyć do zdefiniowania Service Fabric Reliable Actors stanów i interfejsów
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.custom: devx-track-csharp
ms.openlocfilehash: 33a15b3c6f570d3b4d36a205f91bce7b042f3bb4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89016584"
---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Uwagi dotyczące serializacji typu Service Fabric Reliable Actors
Argumenty wszystkich metod, typy wyników zadań zwracanych przez każdą metodę w interfejsie aktora, a obiekty przechowywane w Menedżerze stanu aktora muszą być [serializowane kontraktu danych](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer). Dotyczy to również argumentów metod zdefiniowanych w [interfejsie zdarzeń aktora](service-fabric-reliable-actors-events.md). (Aktora metody interfejsu zawsze zwracają wartość void.)

## <a name="custom-data-types"></a>Niestandardowe typy danych
W tym przykładzie następujący interfejs aktora definiuje metodę, która zwraca niestandardowy typ danych o nazwie `VoicemailBox` :

```csharp
public interface IVoiceMailBoxActor : IActor
{
    Task<VoicemailBox> GetMailBoxAsync();
}
```

```Java
public interface VoiceMailBoxActor extends Actor
{
    CompletableFuture<VoicemailBox> getMailBoxAsync();
}
```

Interfejs jest implementowany przez aktora, który używa menedżera stanu do przechowywania `VoicemailBox` obiektu:

```csharp
[StatePersistence(StatePersistence.Persisted)]
public class VoiceMailBoxActor : Actor, IVoicemailBoxActor
{
    public VoiceMailBoxActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<VoicemailBox> GetMailboxAsync()
    {
        return this.StateManager.GetStateAsync<VoicemailBox>("Mailbox");
    }
}

```

```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class VoiceMailBoxActorImpl extends FabricActor implements VoicemailBoxActor
{
    public VoiceMailBoxActorImpl(ActorService actorService, ActorId actorId)
    {
         super(actorService, actorId);
    }

    public CompletableFuture<VoicemailBox> getMailBoxAsync()
    {
         return this.stateManager().getStateAsync("Mailbox");
    }
}

```

W tym przykładzie `VoicemailBox` obiekt jest serializowany w następujących przypadkach:

* Obiekt jest przesyłany między wystąpieniem aktora a obiektem wywołującym.
* Obiekt jest zapisywany w Menedżerze stanu, w którym jest utrwalany na dysku i replikowany do innych węzłów.

Niezawodna struktura aktora używa serializacji DataContract. W związku z tym obiekty danych niestandardowych i ich elementy członkowskie muszą mieć adnotację odpowiednio z atrybutami **DataContract** i **DataMember** .

```csharp
[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}
```
```Java
public class Voicemail implements Serializable
{
    private static final long serialVersionUID = 42L;

    private UUID id;                    //getUUID() and setUUID()

    private String message;             //getMessage() and setMessage()

    private GregorianCalendar receivedAt; //getReceivedAt() and setReceivedAt()
}
```


```csharp
[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}
```
```Java
public class VoicemailBox implements Serializable
{
    static final long serialVersionUID = 42L;
    
    public VoicemailBox()
    {
        this.messageList = new ArrayList<Voicemail>();
    }

    private List<Voicemail> messageList;   //getMessageList() and setMessageList()

    private String greeting;               //getGreeting() and setGreeting()
}
```


## <a name="next-steps"></a>Następne kroki
* [Cykl życia aktora i odzyskiwanie pamięci](service-fabric-reliable-actors-lifecycle.md)
* [Czasomierze aktora i przypomnienia](service-fabric-reliable-actors-timers-reminders.md)
* [Zdarzenia aktora](service-fabric-reliable-actors-events.md)
* [Współużytkowania wątkowości aktora](service-fabric-reliable-actors-reentrancy.md)
* [Polimorfizm aktora i wzorce projektowe zorientowane obiektowo](service-fabric-reliable-actors-polymorphism.md)
* [Diagnostyka aktora i monitorowanie wydajności](service-fabric-reliable-actors-diagnostics.md)
