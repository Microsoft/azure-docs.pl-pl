---
title: Polimorfizm w Reliable Actors Framework
description: Kompiluj hierarchie interfejsów i typów .NET w Reliable Actors Framework, aby ponownie użyć funkcji i definicji interfejsu API.
ms.topic: conceptual
ms.date: 11/02/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 568e306979e862c325264a4e12a64d95e6d13c5f
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575996"
---
# <a name="polymorphism-in-the-reliable-actors-framework"></a>Polimorfizm w Reliable Actors Framework
Struktura Reliable Actors umożliwia kompilowanie aktorów przy użyciu wielu z tych samych technik, których można użyć w projekcie zorientowanym obiektowo. Jedną z tych technik jest polimorfizm, który umożliwia typom i interfejsom dziedziczenie z bardziej uogólnionych obiektów nadrzędnych. Dziedziczenie w strukturze Reliable Actors jest ogólnie zgodne z modelem platformy .NET z kilkoma dodatkowymi ograniczeniami. W przypadku środowiska Java/Linux następuje po modelu Java.

## <a name="interfaces"></a>Interfejsy
Struktura Reliable Actors wymaga zdefiniowania co najmniej jednego interfejsu, który ma być zaimplementowany przez typ aktora. Ten interfejs jest używany do generowania klasy proxy, która może być używana przez klientów do komunikowania się z aktorami. Interfejsy mogą dziedziczyć z innych interfejsów, o ile każdy interfejs, który jest zaimplementowany przez typ aktora i wszystkie jego obiekty nadrzędne, ostatecznie pochodzi od IActor (C#) lub aktora (Java). IActor (C#) i aktor (Java) są interfejsami podstawowymi zdefiniowanymi na platformie dla aktorów odpowiednio w strukturach .NET i Java. W ten sposób klasyczny przykład polimorfizmu używający kształtów może wyglądać następująco:

![Hierarchia interfejsów dla aktorów kształtów][shapes-interface-hierarchy]

## <a name="types"></a>Typy
Można również utworzyć hierarchię typów aktorów, które pochodzą z podstawowej klasy aktora dostarczonej przez platformę. W przypadku kształtów może istnieć `Shape` Typ podstawowy (C#) lub `ShapeImpl` (Java):

```csharp
public abstract class Shape : Actor, IShape
{
    public abstract Task<int> GetVerticeCount();

    public abstract Task<double> GetAreaAsync();
}
```
```Java
public abstract class ShapeImpl extends FabricActor implements Shape
{
    public abstract CompletableFuture<int> getVerticeCount();

    public abstract CompletableFuture<double> getAreaAsync();
}
```

Podtypy `Shape` (C#) lub `ShapeImpl` (Java) mogą przesłonić metody z bazy.

```csharp
[ActorService(Name = "Circle")]
[StatePersistence(StatePersistence.Persisted)]
public class Circle : Shape, ICircle
{
    public override Task<int> GetVerticeCount()
    {
        return Task.FromResult(0);
    }

    public override async Task<double> GetAreaAsync()
    {
        CircleState state = await this.StateManager.GetStateAsync<CircleState>("circle");

        return Math.PI *
            state.Radius *
            state.Radius;
    }
}
```
```Java
@ActorServiceAttribute(name = "Circle")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class Circle extends ShapeImpl implements Circle
{
    @Override
    public CompletableFuture<Integer> getVerticeCount()
    {
        return CompletableFuture.completedFuture(0);
    }

    @Override
    public CompletableFuture<Double> getAreaAsync()
    {
        return (this.stateManager().getStateAsync<CircleState>("circle").thenApply(state->{
          return Math.PI * state.radius * state.radius;
        }));
    }
}
```

Zwróć uwagę na `ActorService` atrybut typu aktora. Ten atrybut informuje niezawodną strukturę aktora, że powinna automatycznie utworzyć usługę do hostingu aktorów tego typu. W niektórych przypadkach można utworzyć typ podstawowy, który jest przeznaczony wyłącznie do udostępniania funkcjonalności z podtypem i nigdy nie będzie używany do wystąpienia konkretnych aktorów. W takich przypadkach należy użyć `abstract` słowa kluczowego, aby wskazać, że nigdy nie utworzysz aktora na podstawie tego typu.

## <a name="next-steps"></a>Następne kroki
* Zobacz, [jak struktura Reliable Actors wykorzystuje platformę Service Fabric](service-fabric-reliable-actors-platform.md) , aby zapewnić niezawodność, skalowalność i spójny stan.
* Dowiedz się więcej o [cyklu życia aktora](service-fabric-reliable-actors-lifecycle.md).

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png
