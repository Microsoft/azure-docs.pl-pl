---
title: Reliable Actors na Service Fabric
description: Opisuje, w jaki sposób Reliable Actors są nakładane na Reliable Services i korzystać z funkcji platformy Service Fabric.
ms.topic: conceptual
ms.date: 3/9/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: e204b47b7fa9f528341507c315c926159d524e13
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98789586"
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Jak Reliable Actors używać platformy Service Fabric
W tym artykule wyjaśniono, jak Reliable Actors pracy na platformie Azure Service Fabric. Reliable Actors działać w strukturze, która jest hostowana w implementacji niezawodnej usługi stanowej o nazwie *Usługa aktora*. Usługa aktora zawiera wszystkie składniki niezbędne do zarządzania cyklem życia i wysyłaniem komunikatów dla uczestników:

* Środowisko uruchomieniowe aktora zarządza cyklem życia, wyrzucaniem elementów bezużytecznych i wymusza dostęp jednowątkowy.
* Odbiornik komunikacji zdalnej usługi aktora akceptuje wywołania dostępu zdalnego do aktorów i wysyła je do dyspozytora w celu skierowania do odpowiedniego wystąpienia aktora.
* Dostawca stanu aktora zawija dostawców stanu (na przykład dostawcę stanu niezawodnych kolekcji) i udostępnia adapter do zarządzania stanem aktora.

Te składniki wspólnie tworzą niezawodną strukturę aktora.

## <a name="service-layering"></a>Obsługa warstw usług
Ponieważ sama usługa aktora to niezawodna usługa, wszystkie pojęcia związane z [modelem aplikacji](service-fabric-application-model.md), cyklem życia, [pakowanie](service-fabric-package-apps.md), [wdrażanie](service-fabric-deploy-remove-applications.md), uaktualnianie i skalowanie Reliable Services mają taki sam sposób, jak w przypadku usług aktora.

![Obsługa warstw usługi aktora][1]

Na powyższym diagramie przedstawiono relację między strukturami aplikacji Service Fabric i kodem użytkownika. Niebieskie elementy reprezentują strukturę aplikacji Reliable Services, pomarańczowy reprezentuje niezawodną strukturę aktora, a zielony reprezentuje kod użytkownika.

W Reliable Services usługa dziedziczy `StatefulService` klasę. Ta klasa jest sama pochodna `StatefulServiceBase` (lub `StatelessService` dla usług bezstanowych). W Reliable Actors używasz usługi aktora. Usługa aktora to inna implementacja `StatefulServiceBase` klasy implementującej wzorzec aktora, w którym działają aktory. Ponieważ sama usługa aktora jest tylko implementacją programu `StatefulServiceBase` , można napisać własną usługę, która pochodzi z `ActorService` i Implementuj funkcje na poziomie usług w taki sam sposób, jak w przypadku dziedziczenia, na przykład `StatefulService` :

* Kopia zapasowa i przywracanie usługi.
* Funkcje udostępnione dla wszystkich aktorów, na przykład wyłącznika.
* Zdalne wywołania procedury dla samej usługi aktora i poszczególnych aktorów.

Aby uzyskać więcej informacji, zobacz [Implementowanie funkcji poziomu usług w usłudze aktora](service-fabric-reliable-actors-using.md).

## <a name="application-model"></a>Model aplikacji
Usługi aktora są Reliable Services, więc model aplikacji jest taki sam. Jednak narzędzia do tworzenia struktury aktora generują niektóre pliki modelu aplikacji.

### <a name="service-manifest"></a>Manifest usługi
Narzędzia do kompilowania struktury aktora automatycznie generują zawartość pliku ServiceManifest.xml usługi aktora. Ten plik zawiera:

* Typ usługi aktora. Nazwa typu jest generowana na podstawie nazwy projektu aktora. W oparciu o atrybut trwałości aktora flaga HasPersistedState jest również ustawiana odpowiednio.
* Pakiet kodu.
* Pakiet konfiguracji.
* Zasoby i punkty końcowe.

### <a name="application-manifest"></a>Manifest aplikacji
Narzędzia do tworzenia struktury aktora automatycznie tworzą domyślną definicję usługi dla Twojej usługi aktora. Narzędzia kompilacji wypełniają domyślne właściwości usługi:

* Liczba zestawów replik jest określana przez atrybut trwałości aktora. Za każdym razem, gdy atrybut trwałości aktora zostanie zmieniony, liczba zestawów replik w domyślnej definicji usługi jest odpowiednio resetowana.
* Schemat partycji i zakres są ustawiane jako jednorodne wartości Int64 z pełnym zakresem wartości Int64.

## <a name="service-fabric-partition-concepts-for-actors"></a>Pojęcia dotyczące partycji Service Fabric dla uczestników
Usługa aktora jest podzielona na partycje usługi stanowe. Każda partycja usługi aktora zawiera zestaw aktorów. Partycje usługi są automatycznie dystrybuowane na wiele węzłów w Service Fabric. Wystąpienia aktora są dystrybuowane w wyniku.

![Partycjonowanie aktora i dystrybucja][5]

Reliable Services można tworzyć z różnymi schematami partycji i zakresami kluczy partycji. Usługa aktor używa schematu partycjonowania Int64 z pełnym zakresem wartości Int64 do mapowania aktorów na partycje.

### <a name="actor-id"></a>Identyfikator aktora
Każdy aktor, który jest tworzony w usłudze, ma przypisany unikatowy identyfikator, reprezentowany przez `ActorId` klasę. `ActorId` to nieprzezroczysta wartość identyfikatora, która może być używana do jednorodnej dystrybucji aktorów w ramach partycji usługi przez generowanie losowych identyfikatorów:

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


Każde `ActorId` jest skrótem do wartości Int64. Dlatego usługa aktora musi używać schematu partycjonowania Int64 z pełnym zakresem wartości Int64. Jednak niestandardowe wartości identyfikatora mogą być używane dla elementu `ActorID` , w tym identyfikatory GUID/UUID, ciągi i wartości Int64.

```csharp
ActorProxy.Create<IMyActor>(new ActorId(Guid.NewGuid()));
ActorProxy.Create<IMyActor>(new ActorId("myActorId"));
ActorProxy.Create<IMyActor>(new ActorId(1234));
```
```Java
ActorProxyBase.create(MyActor.class, new ActorId(UUID.randomUUID()));
ActorProxyBase.create(MyActor.class, new ActorId("myActorId"));
ActorProxyBase.create(MyActor.class, new ActorId(1234));
```

W przypadku używania identyfikatorów GUID/UUID i ciągów wartości są zmieszane z wartością Int64. Jeśli jednak jawnie podajesz wartość Int64 do `ActorId` , to Int64 będzie mapować bezpośrednio na partycję bez dalszej mieszania. Za pomocą tej metody można kontrolować, w której partycji umieszczane są aktory.


## <a name="next-steps"></a>Następne kroki
* [Zarządzanie stanem aktora](service-fabric-reliable-actors-state-management.md)
* [Cykl życia aktora i odzyskiwanie pamięci](service-fabric-reliable-actors-lifecycle.md)
* [Dokumentacja referencyjna interfejsu API aktorów](/dotnet/api/microsoft.servicefabric.actors)
* [Przykładowy kod platformy .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Przykładowy kod w języku Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
