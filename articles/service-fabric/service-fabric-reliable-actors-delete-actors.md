---
title: Usuwanie uczestników Service Fabric platformy Azure
description: Dowiedz się, jak ręcznie i w pełni usuwać Reliable Actors i ich stan w aplikacji Service Fabric platformy Azure.
author: amanbha
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: amanbha
ms.custom: devx-track-csharp
ms.openlocfilehash: 80192aef564317e36fba56025aa31c787676d974
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89006860"
---
# <a name="delete-reliable-actors-and-their-state"></a>Usuń usługi Reliable Actors i jej stanu
Wyrzucanie elementów bezużytecznych nieaktywowanych aktorów jedynie czyści obiekt aktora, ale nie usuwa danych przechowywanych w Menedżerze stanu aktora. Po ponownym uaktywnieniu aktora jego dane zostaną ponownie udostępnione za pomocą menedżera stanu. W przypadkach, gdy aktory przechowują dane w Menedżerze stanu i są dezaktywowane, ale nigdy nie są ponownie uaktywniane, może być konieczne wyczyszczenie danych.

[Usługa aktora](service-fabric-reliable-actors-platform.md) udostępnia funkcję usuwania aktorów ze zdalnego obiektu wywołującego:

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```
```Java
ActorId actorToDelete = new ActorId(id);

ActorService myActorServiceProxy = ActorServiceProxy.create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

myActorServiceProxy.deleteActorAsync(actorToDelete);
```

Usunięcie aktora ma następujące skutki, w zależności od tego, czy aktor jest obecnie aktywny:

* **Aktywny aktor**
  * Aktor został usunięty z listy aktywnych aktorów i zdezaktywowany.
  * Jego stan jest trwale usunięty.
* **Aktor nieaktywny**
  * Jego stan jest trwale usunięty.

Aktor nie może wywoływać operacji Delete dla samego siebie z jednej z metod aktora, ponieważ aktor nie może zostać usunięty podczas wykonywania w kontekście wywołania aktora, w którym środowisko uruchomieniowe uzyskało blokadę wywołania aktora w celu wymuszenia dostępu jednowątkowego.

Więcej informacji o Reliable Actors można znaleźć w następujących tematach:
* [Czasomierze aktora i przypomnienia](service-fabric-reliable-actors-timers-reminders.md)
* [Zdarzenia aktora](service-fabric-reliable-actors-events.md)
* [Współużytkowania wątkowości aktora](service-fabric-reliable-actors-reentrancy.md)
* [Diagnostyka aktora i monitorowanie wydajności](service-fabric-reliable-actors-diagnostics.md)
* [Dokumentacja interfejsu API aktora](/previous-versions/azure/dn971626(v=azure.100))
* [Przykładowy kod w języku C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Przykładowy kod w języku Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
