---
title: Tworzenie usługi opartej na aktorze na platformie Azure Service Fabric
description: Dowiedz się, jak tworzyć, debugować i wdrażać pierwszą usługę opartą na aktorze w języku C# przy użyciu Service Fabric Reliable Actors.
author: vturecek
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: vturecek
ms.custom: devx-track-csharp
ms.openlocfilehash: f775608c6c349c93a43ef4aef26ad50707cd39b7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96023622"
---
# <a name="getting-started-with-reliable-actors"></a>Wprowadzenie do Reliable Actors
> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-actors-get-started.md)
> * [Java w systemie Linux](./service-fabric-create-your-first-linux-application-with-java.md)

W tym artykule omówiono tworzenie i debugowanie prostej niezawodnej aplikacji aktora w programie Visual Studio. Aby uzyskać więcej informacji na temat Reliable Actors, zobacz [wprowadzenie do Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md).

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz Service Fabric środowisko programistyczne, w tym program Visual Studio, a następnie skonfiguruj go na komputerze. Aby uzyskać szczegółowe informacje, zobacz [jak skonfigurować środowisko deweloperskie](service-fabric-get-started.md).

## <a name="create-a-new-project-in-visual-studio"></a>Utworzenie nowego projektu w Visual Studio

Uruchom program Visual Studio 2019 lub nowszy jako administrator, a następnie utwórz nowy projekt **aplikacji Service Fabric** :

![Service Fabric Tools for Visual Studio — nowy projekt][1]

W następnym oknie dialogowym wybierz pozycję **Usługa aktora** w obszarze **.NET Core 2,0** , a następnie wprowadź nazwę usługi.

![Service Fabric szablonów projektu][5]

Utworzony projekt pokazuje następującą strukturę:

![Struktura projektu Service Fabric][2]

## <a name="examine-the-solution"></a>Sprawdzanie rozwiązania

Rozwiązanie zawiera trzy projekty:

* **Projekt aplikacji (aplikacje)**. Ten projekt pakuje wszystkie usługi do wdrożenia. Zawiera ona skrypty *ApplicationManifest.xml* i PowerShell służące do zarządzania aplikacją.

* **Projekt interfejsu (HelloWorld. Interfaces)**. Ten projekt zawiera definicję interfejsu aktora. Interfejsy aktora można definiować w dowolnym projekcie z dowolną nazwą.  Interfejs definiuje kontrakt aktora, który jest współużytkowany przez implementację aktora i klientów wywołujących aktora.  Ze względu na to, że projekty klienta mogą być od niego zależne, zazwyczaj warto zdefiniować je w zestawie, który jest oddzielony od implementacji aktora.

* **Projekt usługi aktora (HelloWorld)**. Ten projekt definiuje usługę Service Fabric, która będzie hostować aktora. Zawiera implementację aktora, *HelloWorld.cs*. Implementacja aktora to Klasa, która pochodzi od typu podstawowego `Actor` i implementuje interfejsy zdefiniowane w projekcie *webaktor. Interfaces* . Klasa aktora musi również implementować konstruktora, który akceptuje `ActorService` wystąpienie i `ActorId` i przekazuje je do `Actor` klasy bazowej.
    
    Ten projekt zawiera również *program.cs*, który rejestruje klasy aktora w środowisku uruchomieniowym Service Fabric przy użyciu `ActorRuntime.RegisterActorAsync<T>()` . `HelloWorld`Klasa jest już zarejestrowana. Wszystkie dodatkowe implementacje aktora dodane do projektu również muszą być zarejestrowane w `Main()` metodzie.

## <a name="customize-the-helloworld-actor"></a>Dostosowywanie aktora HelloWorld

Szablon projektu definiuje niektóre metody w `IHelloWorld` interfejsie i implementuje je w `HelloWorld` implementacji aktora.  Zastąp te metody, aby usługa aktora zwracała prosty ciąg "Hello world".

W projekcie *HelloWorld. Interfaces* w pliku *IHelloWorld.cs* Zastąp definicję interfejsu w następujący sposób:

```csharp
public interface IHelloWorld : IActor
{
    Task<string> GetHelloWorldAsync();
}
```

W projekcie **HelloWorld** w **HelloWorld.cs** Zastąp całą definicję klasy w następujący sposób:

```csharp
[StatePersistence(StatePersistence.Persisted)]
internal class HelloWorld : Actor, IHelloWorld
{
    public HelloWorld(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<string> GetHelloWorldAsync()
    {
        return Task.FromResult("Hello from my reliable actor!");
    }
}
```

Naciśnij **kombinację klawiszy Ctrl-Shift-B** , aby skompilować projekt i upewnić się, że wszystko jest kompilowane.

## <a name="add-a-client"></a>Dodawanie klienta

Utwórz prostą aplikację konsolową, która wywoła usługę aktora.

1. Kliknij prawym przyciskiem myszy rozwiązanie w Eksplorator rozwiązań > **Dodaj**  >  **Nowy projekt..**..

2. W obszarze typy projektów **platformy .NET Core** wybierz pozycję **Aplikacja konsolowa (.NET Core)**.  Nazwij projekt *ActorClient*.
    
    ![Okno dialogowe Dodawanie nowego projektu][6]    
    
    > [!NOTE]
    > Aplikacja konsolowa nie jest typem aplikacji, która zwykle jest używana jako klient w Service Fabric, ale stanowi wygodny przykład dla debugowania i testowania przy użyciu lokalnego klastra Service Fabric.

3. Aplikacja konsolowa musi być aplikacją 64-bitową, aby zachować zgodność z projektem interfejsu i innymi zależnościami.  W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt **ActorClient** , a następnie kliknij polecenie **Właściwości**.  Na karcie **kompilacja** ustaw wartość **docelowy platformy** na **x64**.
    
    ![Właściwości kompilacji][8]

4. Projekt klienta wymaga pakietu NuGet niezawodnych aktorów.  Kliknij kolejno pozycje **Narzędzia** Menedżer  >  **pakietów NuGet**  >  **konsola Menedżera pakietów**.  W konsoli Menedżera pakietów wprowadź następujące polecenie:
    
    ```powershell
    Install-Package Microsoft.ServiceFabric.Actors -IncludePrerelease -ProjectName ActorClient
    ```

    Pakiet NuGet i wszystkie jego zależności są instalowane w projekcie ActorClient.

5. Projekt klienta wymaga również odwołania do projektu interfejsów.  W projekcie ActorClient kliknij prawym przyciskiem myszy pozycję **zależności** , a następnie kliknij pozycję **Dodaj odwołanie do projektu..**..  Wybierz pozycję **projekty > rozwiązanie** (jeśli nie zostało to jeszcze zaznaczone), a następnie zaznacz pole wyboru obok pozycji **HelloWorld. Interfaces**.  Kliknij przycisk **OK**.
    
    ![Okno dialogowe Dodawanie odwołania][7]

6. W projekcie ActorClient Zastąp całą zawartość *program.cs* następującym kodem:
    
    ```csharp
    using System;
    using System.Threading.Tasks;
    using Microsoft.ServiceFabric.Actors;
    using Microsoft.ServiceFabric.Actors.Client;
    using HelloWorld.Interfaces;
    
    namespace ActorClient
    {
        class Program
        {
            static void Main(string[] args)
            {
                IHelloWorld actor = ActorProxy.Create<IHelloWorld>(ActorId.CreateRandom(), new Uri("fabric:/MyApplication/HelloWorldActorService"));
                Task<string> retval = actor.GetHelloWorldAsync();
                Console.Write(retval.Result);
                Console.ReadLine();
            }
        }
    }
    ```

## <a name="running-and-debugging"></a>Uruchamianie i debugowanie

Naciśnij klawisz **F5** , aby skompilować, wdrożyć i uruchomić aplikację lokalnie w klastrze tworzenia Service Fabric.  W trakcie procesu wdrażania postęp jest wyświetlany w oknie **danych wyjściowych** .

![Okno danych wyjściowych debugowania Service Fabric][3]

Gdy dane wyjściowe zawierają tekst, *aplikacja jest gotowa*, możliwe jest przetestowanie usługi przy użyciu aplikacji ActorClient.  W Eksplorator rozwiązań kliknij prawym przyciskiem myszy projekt **ActorClient** , a następnie kliknij pozycję **Debuguj**  >  **Uruchom nowe wystąpienie**.  Aplikacja wiersza polecenia powinna wyświetlać dane wyjściowe usługi aktora.

![Dane wyjściowe aplikacji][9]

> [!TIP]
> Środowisko uruchomieniowe aktorów Service Fabric emituje niektóre [zdarzenia i liczniki wydajności związane z metodami aktora](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). Są one przydatne w przypadku diagnostyki i monitorowania wydajności.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [tym, jak Reliable Actors używać platformy Service Fabric](service-fabric-reliable-actors-platform.md).


[1]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject.PNG
[2]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-projectstructure.PNG
[3]: ./media/service-fabric-reliable-actors-get-started/debugging-output.PNG
[4]: ./media/service-fabric-reliable-actors-get-started/vs-context-menu.png
[5]: ./media/service-fabric-reliable-actors-get-started/reliable-actors-newproject1.PNG
[6]: ./media/service-fabric-reliable-actors-get-started/new-console-app.png
[7]: ./media/service-fabric-reliable-actors-get-started/add-reference.png
[8]: ./media/service-fabric-reliable-actors-get-started/build-props.png
[9]: ./media/service-fabric-reliable-actors-get-started/app-output.png
