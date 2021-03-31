---
title: Implementowanie funkcji w usłudze Azure Service Fabric aktorzy
description: Opisuje sposób pisania własnej usługi aktora, która implementuje funkcje na poziomie usług w taki sam sposób, jak w przypadku dziedziczenia klasy statefulservice.
ms.topic: conceptual
ms.date: 03/19/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: d39ec93e0ad03d6c860bae9d0790e860c95457a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96575571"
---
# <a name="implement-service-level-features-in-your-actor-service"></a>Implementowanie funkcji na poziomie usług w usłudze aktora

Jak opisano w [warstwach usług](service-fabric-reliable-actors-platform.md#service-layering), sama usługa aktora jest niezawodna. Można napisać własną usługę, która pochodzi od `ActorService` . Funkcje poziomu usługi można również zaimplementować w taki sam sposób, jak w przypadku dziedziczenia usługi stanowej, na przykład:

- Kopia zapasowa i przywracanie usługi.
- Funkcje udostępnione dla wszystkich aktorów, na przykład wyłącznika.
- Zdalne wywołania procedury dla samej usługi aktora i poszczególnych aktorów.

## <a name="use-the-actor-service"></a>Korzystanie z usługi aktora

Wystąpienia aktora mają dostęp do usługi aktora, w której są uruchomione. Za pomocą usługi aktora wystąpienia aktora mogą programowo uzyskać kontekst usługi. Kontekst usługi zawiera identyfikator partycji, nazwę usługi, nazwę aplikacji oraz inne informacje dotyczące platformy Service Fabric platformy Azure.

```csharp
Task MyActorMethod()
{
    Guid partitionId = this.ActorService.Context.PartitionId;
    string serviceTypeName = this.ActorService.Context.ServiceTypeName;
    Uri serviceInstanceName = this.ActorService.Context.ServiceName;
    string applicationInstanceName = this.ActorService.Context.CodePackageActivationContext.ApplicationName;
}
```
```Java
CompletableFuture<?> MyActorMethod()
{
    UUID partitionId = this.getActorService().getServiceContext().getPartitionId();
    String serviceTypeName = this.getActorService().getServiceContext().getServiceTypeName();
    URI serviceInstanceName = this.getActorService().getServiceContext().getServiceName();
    String applicationInstanceName = this.getActorService().getServiceContext().getCodePackageActivationContext().getApplicationName();
}
```

Podobnie jak w przypadku wszystkich Reliable Services usługa aktora musi być zarejestrowana w ramach typu usługi w środowisku uruchomieniowym Service Fabric. Aby usługa aktora mogła uruchamiać wystąpienia aktora, typ aktora musi być zarejestrowany w usłudze aktora. Metoda rejestracji `ActorRuntime` wykonuje tę pracę dla aktorów. W najprostszym przypadku można zarejestrować typ aktora, a usługa aktora korzysta z domyślnych ustawień.

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>().GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```

Alternatywnie można użyć wyrażenia lambda dostarczonego przez metodę rejestracji do samodzielnego skonstruowania usługi aktora. Następnie można skonfigurować usługę aktora i jawnie skonstruować wystąpienia aktora. Można wstrzyknąć zależności do aktora za jego konstruktorem.

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new ActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
static class Program
{
    private static void Main()
    {
      ActorRuntime.registerActorAsync(
              MyActor.class,
              (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
              timeout);

        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="actor-service-methods"></a>Metody usługi aktora

Usługa aktora implementuje `IActorService` (c#) lub `ActorService` (Java), które z kolei implementują `IService` (c#) lub `Service` (Java). Ten interfejs jest używany przez Reliable Services komunikacji zdalnej, co umożliwia zdalne wywoływanie procedur w metodach usługi. Zawiera metody poziomu usług, które można wywołać zdalnie za pośrednictwem komunikacji zdalnej usługi. Można jej użyć do [wyliczania](service-fabric-reliable-actors-enumerate.md) i [usuwania](service-fabric-reliable-actors-delete-actors.md) aktorów.


## <a name="custom-actor-service"></a>Niestandardowa usługa aktora

Za pomocą wyrażenia lambda rejestracji aktora można zarejestrować własną niestandardową usługę aktora, która pochodzi z `ActorService` (C#) i `FabricActorService` (Java). Następnie można zaimplementować własne funkcje poziomu usług, pisząc klasę usługi, która dziedziczy `ActorService` (C#) lub `FabricActorService` (Java). Niestandardowa usługa aktora dziedziczy wszystkie funkcje środowiska uruchomieniowego aktora z `ActorService` (C#) lub `FabricActorService` (Java). Może służyć do implementowania własnych metod usług.

```csharp
class MyActorService : ActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }
}
```
```Java
class MyActorService extends FabricActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, BiFunction<FabricActorService, ActorId, ActorBase> newActor)
    {
         super(context, typeInfo, newActor);
    }
}
```

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new MyActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="implement-actor-backup-and-restore"></a>Implementowanie tworzenia i przywracania kopii zapasowych aktora

Niestandardowa usługa aktora może uwidaczniać metodę tworzenia kopii zapasowych danych aktora, wykorzystując odbiornik komunikacji zdalnej już obecny w `ActorService` . Aby zapoznać się z przykładem, zobacz [Tworzenie kopii zapasowych i przywracanie aktorów](service-fabric-reliable-actors-backup-and-restore.md).

## <a name="actor-that-uses-a-remoting-v2-interface-compatible-stack"></a>Aktor używający stosu z obsługą zdalną v2 (zgodnego z interfejsem)

Stos usług zdalnych w wersji 2 (zgodny z interfejsem, znany jako V2_1) ma wszystkie funkcje stosu komunikacji zdalnej w wersji 2. Jego interfejs jest zgodny ze stosem usług zdalnych w wersji 1, ale nie jest zgodny z wersjami 2 i v1. Aby uaktualnić program z wersji 1 do V2_1 bez wpływu na dostępność usługi, wykonaj kroki opisane w następnej sekcji.

Następujące zmiany są wymagane do używania stosu V2_1 zdalnych:

1. Dodaj następujący atrybut zestawu do interfejsów aktora.
  
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Kompiluj i uaktualniaj usługę aktora i projekty klientów aktorów, aby rozpocząć korzystanie ze stosu v2.

### <a name="actor-service-upgrade-to-remoting-v2-interface-compatible-stack-without-affecting-service-availability"></a>Uaktualnianie usługi aktora do funkcji komunikacji zdalnej v2 (zgodnej z interfejsem) bez wpływu na dostępność usługi

Ta zmiana jest uaktualnieniem dwuetapowym. Wykonaj kroki opisane w tej sekwencji.

1. Dodaj następujący atrybut zestawu do interfejsów aktora. Ten atrybut uruchamia dwa odbiorniki dla usługi aktora, v1 (istniejący) i odbiornik V2_1. Uaktualnij usługę aktora przy użyciu tej zmiany.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Uaktualnij klientów aktorów po zakończeniu poprzedniego uaktualnienia.
   Ten krok zapewnia, że serwer proxy aktora korzysta ze stosu V2_1 zdalnych.

3. Ta czynność jest opcjonalna. Zmień poprzedni atrybut, aby usunąć odbiornik v1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```

## <a name="actor-that-uses-the-remoting-v2-stack"></a>Aktor korzystający ze stosu zdalnego v2

Korzystając z pakietu NuGet w wersji 2,8, użytkownicy mogą teraz używać stosu zdalnego v2, który wykonuje lepsze i udostępnia funkcje takie jak Serializacja niestandardowa. Komunikacja zdalna v2 nie jest zgodna z poprzednimi wersjami z istniejącym stosem usług zdalnych (nazywanym teraz stosem komunikacji zdalnej w wersji 1).

Poniższe zmiany są wymagane do korzystania ze stosu usług zdalnych w wersji 2.

1. Dodaj następujący atrybut zestawu do interfejsów aktora.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Kompiluj i uaktualniaj usługę aktora i projekty klientów aktorów, aby rozpocząć korzystanie ze stosu v2.

### <a name="upgrade-the-actor-service-to-the-remoting-v2-stack-without-affecting-service-availability"></a>Uaktualnij usługę aktora do stosu zdalnego systemu v2 bez wpływu na dostępność usługi

Ta zmiana jest uaktualnieniem dwuetapowym. Wykonaj kroki opisane w tej sekwencji.

1. Dodaj następujący atrybut zestawu do interfejsów aktora. Ten atrybut uruchamia dwa odbiorniki dla usługi aktora, v1 (istniejący) i odbiornik v2. Uaktualnij usługę aktora przy użyciu tej zmiany.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Uaktualnij klientów aktorów po zakończeniu poprzedniego uaktualnienia.
   Ten krok zapewnia, że serwer proxy aktora korzysta ze stosu usług zdalnych w wersji 2.

3. Ta czynność jest opcjonalna. Zmień poprzedni atrybut, aby usunąć odbiornik v1.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
    ```

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie stanem aktora](service-fabric-reliable-actors-state-management.md)
* [Cykl życia aktora i odzyskiwanie pamięci](service-fabric-reliable-actors-lifecycle.md)
* [Dokumentacja referencyjna interfejsu API aktorów](/previous-versions/azure/dn971626(v=azure.100))
* [Przykładowy kod platformy .NET](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Przykładowy kod w języku Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
