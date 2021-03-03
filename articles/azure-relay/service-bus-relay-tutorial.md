---
title: Uwidocznij Premium usługę WCF REST klientom przy użyciu Azure Relay
description: W tym samouczku opisano, jak uwidocznić lokalną usługę WCF REST na zewnętrznym kliencie przy użyciu usługi Azure WCF Relay.
ms.topic: tutorial
ms.custom: devx-track-dotnet
ms.date: 06/23/2020
ms.openlocfilehash: 7669bc07ad91933cd31bd2ccd10eaf830d98de7c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101710791"
---
# <a name="tutorial-expose-an-on-premises-wcf-rest-service-to-external-client-by-using-azure-wcf-relay"></a>Samouczek: Uwidacznianie lokalnej usługi WCF REST na zewnętrznym kliencie przy użyciu usługi Azure WCF Relay

W tym samouczku opisano sposób tworzenia WCF Relay aplikacji klienckiej i usługi przy użyciu Azure Relay. Aby zapoznać się z podobnym samouczkiem korzystającym z [Service Bus Messaging](../service-bus-messaging/service-bus-messaging-overview.md), zobacz [Rozpoczynanie pracy z kolejkami Service Bus](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md).

Praca w ramach tego samouczka zawiera opis kroków tworzenia WCF Relay klienta i aplikacji usługi. Podobnie jak w przypadku oryginalnych odpowiedników WCF, usługa jest konstrukcja, która uwidacznia jeden lub więcej punktów końcowych. Każdy punkt końcowy uwidacznia jedną lub więcej operacji usługi. Punkt końcowy usługi określa adres usługi, powiązanie zawierające informacje umożliwiające klientowi komunikowanie się z usługą i kontrakt definiujący funkcje zapewniane klientom przez usługę. Główną różnicą między WCF i WCF Relay jest to, że punkt końcowy jest ujawniany w chmurze, a nie lokalnie na komputerze.

Po przejściu przez sekwencję sekcji w tym samouczku będziesz mieć uruchomioną usługę. Będziesz również mieć klienta, który może wywołać operacje usługi. 

W tym samouczku wykonasz następujące zadania:

> [!div class="checklist"]
>
> * Zainstaluj wymagania wstępne dla tego samouczka.
> * Utwórz przestrzeń nazw przekaźnika.
> * Utwórz kontrakt usługi WCF.
> * Zaimplementuj kontrakt WCF.
> * Hostowanie i uruchamianie usługi WCF w celu zarejestrowania się w usłudze przekaźnika.
> * Utwórz klienta WCF dla kontraktu usługi.
> * Skonfiguruj klienta WCF.
> * Zaimplementuj klienta WCF.
> * Uruchom aplikacje.

## <a name="prerequisites"></a>Wymagania wstępne

Do wykonania kroków tego samouczka niezbędne jest spełnienie następujących wymagań wstępnych:

* Subskrypcja platformy Azure. Jeśli go nie masz, przed rozpoczęciem [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) .
* [Program Visual Studio 2015 lub nowszy](https://www.visualstudio.com). W przykładach w tym samouczku użyto programu Visual Studio 2019.
* Zestaw Azure SDK dla platformy .NET. Zainstaluj go ze [strony pobierania zestawu SDK](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Tworzenie przestrzeni nazw przekaźnika

Pierwszym krokiem jest utworzenie przestrzeni nazw i uzyskanie klucza [sygnatury dostępu współdzielonego (SAS, Shared Access Signature)](../service-bus-messaging/service-bus-sas.md). Przestrzeń nazw wyznacza granice każdej aplikacji uwidacznianej za pośrednictwem usługi przekaźnika. Klucz sygnatury dostępu współdzielonego jest generowany automatycznie przez system po utworzeniu przestrzeni nazw usługi. Kombinacja przestrzeni nazw i klucza sygnatury dostępu współdzielonego usługi dostarcza poświadczenia dla platformy Azure w celu uwierzytelnienia dostępu do aplikacji.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="define-a-wcf-service-contract"></a>Definiowanie kontraktu usługi WCF

Kontrakt usługi określa operacje obsługiwane przez usługę. Operacje to metody lub funkcje usługi sieci Web. Kontrakty są tworzone przez definiowanie interfejsu C++, C# lub Visual Basic. Każda metoda w interfejsie odpowiada określonej operacji usługi. W odniesieniu do każdego interfejsu należy zastosować atrybut [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute), a w odniesieniu do każdej operacji należy zastosować atrybut [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute). Jeśli metoda w interfejsie z atrybutem [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) nie ma atrybutu [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) , ta metoda nie jest ujawniana. Kod dla tych zadań podano w przykładzie zamieszczonym na końcu procedury. Więcej dyskusji na temat umów i usług można znaleźć w temacie [projektowanie i implementowanie usług](/dotnet/framework/wcf/designing-and-implementing-services).

### <a name="create-a-relay-contract-with-an-interface"></a>Tworzenie kontraktu przekaźnika z interfejsem

1. Uruchom Microsoft Visual Studio jako administrator. Aby to zrobić, kliknij prawym przyciskiem myszy ikonę programu Visual Studio, a następnie wybierz polecenie **Uruchom jako administrator**.
1. W programie Visual Studio wybierz pozycję **Utwórz nowy projekt**.
1. W obszarze **Utwórz nowy projekt** wybierz pozycję **aplikacja konsoli (.NET Framework)** dla języka C# i wybierz pozycję **dalej**.
1. Nadaj projektowi nazwę *EchoService* i wybierz pozycję **Utwórz**.

   ![tworzenie aplikacji konsoli][2]

1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Zarządzaj pakietami NuGet**. W **Menedżerze pakietów NuGet** wybierz pozycję **Przeglądaj**, a następnie wyszukaj i wybierz pozycję **windowsazure. ServiceBus**. Wybierz pozycję **Zainstaluj** i zaakceptuj warunki użytkowania.

    ![Pakiet Service Bus][3]

   Ten pakiet automatycznie dodaje odwołania do bibliotek Service Bus i programu WCF `System.ServiceModel` . [System.ServiceModel](/dotnet/api/system.servicemodel) jest przestrzenią nazw umożliwiającą programowy dostęp do podstawowych funkcji platformy WCF. Usługa Service Bus używa wielu obiektów i atrybutów usługi WCF do definiowania kontraktów usług.

1. Dodaj następujące `using` instrukcje w górnej części *program.cs*:

    ```csharp
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```

1. Zmień nazwę przestrzeni nazw z domyślnej nazwy `EchoService` na `Microsoft.ServiceBus.Samples`.

   > [!IMPORTANT]
   > W tym samouczku jest używana przestrzeń nazw języka C#, `Microsoft.ServiceBus.Samples` która jest przestrzenią nazw typu zarządzanego na podstawie kontraktu, który jest używany w pliku konfiguracji w sekcji [Konfigurowanie klienta WCF](#configure-the-wcf-client) . Podczas kompilowania tego przykładu można określić dowolną przestrzeń nazw. Jednak samouczek nie będzie działał, chyba że zmodyfikujesz odpowiednio przestrzenie nazw kontraktu i usługi w pliku konfiguracji aplikacji. Przestrzeń nazw określona w pliku *App.config* musi być taka sama jak przestrzeń nazw określona w plikach języka C#.
   >

1. Bezpośrednio po `Microsoft.ServiceBus.Samples` deklaracji przestrzeni nazw, ale w przestrzeni nazw, Zdefiniuj nowy interfejs o nazwie `IEchoContract` i Zastosuj `ServiceContractAttribute` atrybut do interfejsu z wartością przestrzeni nazw `https://samples.microsoft.com/ServiceModel/Relay/` . Wklej następujący kod po deklaracji przestrzeni nazw:

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

    Wartość przestrzeni nazw różni się od przestrzeni nazw używanej w kodzie. Zamiast tego wartość przestrzeni nazw jest używana jako unikatowy identyfikator dla tego kontraktu. Jawne określenie przestrzeni nazw zapobiega dodawaniu domyślnej wartości przestrzeni nazw do nazwy kontraktu.

   > [!NOTE]
   > Zazwyczaj przestrzeń nazw kontraktu usługi zawiera schemat nazewnictwa uwzględniający informacje o wersji. Uwzględnienie informacji o wersji w przestrzeni nazw kontraktu usługi umożliwia usługom izolowanie istotnych zmian przez zdefiniowanie nowego kontraktu usługi z nową przestrzenią nazw i ujawnienie go w nowym punkcie końcowym. W ten sposób klienci mogą nadal używać starego kontraktu usługi bez konieczności jego aktualizowania. Informacje o wersji mogą zawierać datę lub numer kompilacji. Aby uzyskać więcej informacji, zobacz [Service Versioning](/dotnet/framework/wcf/service-versioning) (Obsługa wersji usług). W tym samouczku schemat nazewnictwa przestrzeni nazw kontraktu usługi nie zawiera informacji o wersji.
   >

1. W `IEchoContract` interfejsie Zadeklaruj metodę dla pojedynczej operacji `IEchoContract` uwidacznianej przez kontrakt w interfejsie i Zastosuj `OperationContractAttribute` atrybut do metody, którą chcesz uwidocznić w ramach kontraktu WCF Relay publicznego, w następujący sposób:

    ```csharp
    [OperationContract]
    string Echo(string text);
    ```

1. Bezpośrednio po definicji interfejsu `IEchoContract` zadeklaruj kanał dziedziczący zarówno po `IEchoContract`, jak i interfejsie `IClientChannel`, w sposób pokazany poniżej:

    ```csharp
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    Kanał jest obiektem platformy WCF, za pomocą którego host i klient przekazują do siebie informacje. Później napiszesz kod w kanale w celu ECHA informacji między obiema aplikacjami.

1. Wybierz pozycję **Kompiluj**  >  **kompilację rozwiązania** lub wybierz kombinację klawiszy Ctrl + Shift + B, aby potwierdzić dokładność pracy wykonanej do tej pory.

### <a name="example-of-a-wcf-contract"></a>Przykład kontraktu WCF

Poniższy kod przedstawia podstawowy interfejs, który definiuje kontrakt WCF Relay.

```csharp
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Teraz, gdy interfejs został utworzony, możesz go zaimplementować.

## <a name="implement-the-wcf-contract"></a>Implementowanie kontraktu WCF

Tworzenie usługi Azure Relay wymaga, aby najpierw utworzyć kontrakt przy użyciu interfejsu. Aby uzyskać więcej informacji na temat tworzenia interfejsu, zobacz poprzednią sekcję. Kolejna procedura implementuje interfejs. To zadanie obejmuje utworzenie klasy o nazwie `EchoService` implementującej interfejs zdefiniowany przez użytkownika `IEchoContract` . Po zaimplementowaniu interfejsu należy skonfigurować interfejs przy użyciu pliku konfiguracji *App.config* . Plik konfiguracji zawiera niezbędne informacje dotyczące aplikacji. Te informacje obejmują nazwę usługi, nazwę kontraktu i typ protokołu, który jest używany do komunikacji z usługą przekaźnika. Kod używany do wykonywania tych zadań podano w przykładzie, który następuje po procedurze. Aby uzyskać bardziej ogólną dyskusję na temat implementowania kontraktu usługi, zobacz [implementowanie kontraktów usług](/dotnet/framework/wcf/implementing-service-contracts).

1. Utwórz nową klasę o nazwie `EchoService` bezpośrednio po definicji interfejsu `IEchoContract`. Klasa `EchoService` implementuje interfejs `IEchoContract`.

    ```csharp
    class EchoService : IEchoContract
    {
    }
    ```

    Podobnie jak w przypadku innych implementacji interfejsów, można zaimplementować definicję w innym pliku. Jednak w przypadku tego samouczka implementację umieszczono w tym samym pliku, w którym znajduje się definicja interfejsu i metoda `Main()`.

1. Zastosuj atrybut [ServiceBehaviorAttribute](/dotnet/api/system.servicemodel.servicebehaviorattribute) do interfejsu `IEchoContract`. Ten atrybut określa nazwę usługi i przestrzeń nazw. Po wykonaniu tych czynności klasa `EchoService` wygląda następująco:

    ```csharp
    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```

1. Zaimplementuj metodę `Echo` zdefiniowaną w interfejsie `IEchoContract` w klasie `EchoService`.

    ```csharp
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```

1. Wybierz opcję **Kompiluj**  >  **kompilację rozwiązania** lub wybierz kombinację klawiszy Ctrl + Shift + B.

### <a name="define-the-configuration-for-the-service-host"></a>Zdefiniuj konfigurację dla hosta usługi

Plik konfiguracji jest podobny do pliku konfiguracji WCF. Zawiera nazwę usługi, punkt końcowy i powiązanie. Punkt końcowy jest lokalizacją Azure Relay udostępnia klientom i hostom komunikację ze sobą. Powiązanie jest typem protokołu, który jest używany do komunikacji. Główną różnicą jest to, że ten skonfigurowany punkt końcowy usługi odwołuje się do powiązania [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) , które nie jest częścią .NET Framework. [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) jest jednym ze powiązań zdefiniowanych przez usługę.

1. W **Eksplorator rozwiązań** kliknij dwukrotnie pozycję **App.config** , aby otworzyć plik w edytorze programu Visual Studio.
1. W elemencie `<appSettings>` zastąp symbole zastępcze nazwą przestrzeni nazw usługi i kluczem sygnatury dostępu współdzielonego skopiowanym we wcześniejszym kroku.
1. W tagach `<system.serviceModel>` dodaj element `<services>`. W pojedynczym pliku konfiguracji można zdefiniować wiele aplikacji przekaźnika. W tym samouczku zdefiniowano jednak tylko jedną.

    ```xml
    <?xmlversion="1.0" encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```

1. W elemencie `<services>` dodaj element `<service>`, aby zdefiniować nazwę usługi.

    ```xml
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```

1. W elemencie `<service>` zdefiniuj lokalizację kontraktu punktu końcowego i wpisz powiązanie dla punktu końcowego.

    ```xml
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    Punkt końcowy określa, gdzie klient będzie szukać aplikacji hosta. W dalszej części tego samouczka zostanie użyty ten krok w celu utworzenia identyfikatora URI, który w pełni uwidacznia hosta za pośrednictwem Azure Relay. Powiązanie deklaruje, że protokół TCP jest używany do komunikowania się z usługą przekaźnika.

1. Wybierz pozycję **Kompiluj**  >  **kompilację rozwiązania** lub wybierz kombinację klawiszy Ctrl + Shift + B, aby potwierdzić dokładność pracy wykonanej do tej pory.

### <a name="example-of-implementation-of-a-service-contract"></a>Przykład wdrożenia kontraktu usługi

Poniższy kod przedstawia implementację kontraktu usługi.

```csharp
[ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

Poniższy kod przedstawia podstawowy format pliku *App.config* skojarzonego z hostem usługi.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="host-and-run-the-wcf-service-to-register-with-the-relay-service"></a>Hostowanie i uruchamianie usługi WCF w celu zarejestrowania się w usłudze przekaźnika

W tym kroku opisano sposób uruchamiania usługi Azure Relay.

### <a name="create-the-relay-credentials"></a>Tworzenie poświadczeń przekaźnika

1. W procedurze `Main()` utwórz dwie zmienne do przechowywania przestrzeni nazw i klucza sygnatury dostępu współdzielonego odczytanego z okna konsoli.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    Klucz SAS będzie później używany do uzyskiwania dostępu do projektu. Przestrzeń nazw jest przekazywana jako parametr do procedury `CreateServiceUri` w celu utworzenia identyfikatora URI.

1. Przy użyciu obiektu [TransportClientEndpointBehavior](/dotnet/api/microsoft.servicebus.transportclientendpointbehavior) należy zadeklarować, że jako typ poświadczenia będziesz używać klucza SAS. Dodaj poniższy kod bezpośrednio po kodzie dodanym w ostatnim kroku.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="create-a-base-address-for-the-service"></a>Utwórz adres podstawowy dla usługi

Po kodzie dodanym w poprzedniej sekcji Utwórz `Uri` wystąpienie dla adresu podstawowego usługi. Ten identyfikator URI określa schemat magistrali usług, przestrzeń nazw i ścieżkę interfejsu usługi.

```csharp
Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
```

Wartość "SB" jest skrótem dla schematu Service Bus. Wskazuje, że używamy protokołu TCP jako protokołu. Ten schemat został również wcześniej wskazany w pliku konfiguracji, jeśli [NetTcpRelayBinding](/dotnet/api/microsoft.servicebus.nettcprelaybinding) został określony jako powiązanie.

W tym samouczku użyto identyfikatora URI `sb://putServiceNamespaceHere.windows.net/EchoService`.

### <a name="create-and-configure-the-service-host"></a>Utwórz i skonfiguruj hosta usługi

1. Nadal Pracujesz w programie `Main()` , ustaw tryb łączności na `AutoDetect` .

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    Tryb łączności opisuje protokół, którego usługa używa do komunikacji z usługą przekazywania; HTTP lub TCP. Przy użyciu ustawienia domyślnego `AutoDetect` usługa próbuje nawiązać połączenie z Azure Relay za pośrednictwem protokołu TCP, jeśli jest dostępne, oraz protokołu HTTP, jeśli protokół TCP jest niedostępny. Ten wynik różni się od protokołu, który jest określany przez usługę do komunikacji z klientem. Ten protokół jest ustalany na podstawie używanego powiązania. Na przykład usługa może używać powiązania [BasicHttpRelayBinding](/dotnet/api/microsoft.servicebus.basichttprelaybinding) , które określa, że punkt końcowy komunikuje się z klientami za pośrednictwem protokołu HTTP. Ta sama usługa mogłaby określić, `ConnectivityMode.AutoDetect` że usługa komunikuje się z Azure Relay za pośrednictwem protokołu TCP.

1. Utwórz hosta usługi przy użyciu identyfikatora URI utworzonego wcześniej w tej sekcji.

    ```csharp
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    Host usługi jest obiektem platformy WCF tworzącym wystąpienie usługi. W tym miejscu możesz przekazać typ usługi, którą chcesz utworzyć, `EchoService` Typ, a także adres, pod którym ma zostać ujawniona usługa.

1. W górnej części pliku *program.cs* Dodaj odwołania do elementu [System. ServiceModel. Description](/dotnet/api/system.servicemodel.description) i [Microsoft. ServiceBus. Description](/dotnet/api/microsoft.servicebus.description).

    ```csharp
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```

1. Ponownie w procedurze `Main()` skonfiguruj punkt końcowy do obsługi dostępu publicznego.

    ```csharp
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    Ten krok informuje usługę przekaźnikową, że aplikacja może zostać znaleziona publicznie przez sprawdzenie źródła danych Atom dla projektu. W przypadku ustawienia `DiscoveryType` opcji `private` Klient może nadal uzyskać dostęp do usługi. Jednak usługa nie będzie wyświetlana podczas wyszukiwania w `Relay` przestrzeni nazw. Zamiast tego klient musiałby wcześniej znać ścieżkę punktu końcowego.

1. Zastosuj poświadczenia usługi do punktów końcowych usługi zdefiniowanych w pliku *App.config* :

    ```csharp
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Jak wspomniano wcześniej, można było zadeklarować wiele usług i punktów końcowych w pliku konfiguracji. W takim przypadku ten kod pomijałby plik konfiguracji i wyszukiwałby każdy punkt końcowy, do którego powinien zastosować Twoje poświadczenia. W tym samouczku plik konfiguracji ma tylko jeden punkt końcowy.

### <a name="open-the-service-host"></a>Otwieranie hosta usługi

1. W programie `Main()` Dodaj następujący wiersz, aby otworzyć usługę.

    ```csharp
    host.Open();
    ```

1. Poinformuj użytkownika, że usługa jest uruchomiona, i wyjaśnij, jak zamknąć usługę.

    ```csharp
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Po zakończeniu zamknij hosta usługi.

    ```csharp
    host.Close();
    ```

1. Wybierz kombinację klawiszy Ctrl + Shift + B, aby skompilować projekt.

### <a name="example-that-hosts-a-service-in-a-console-application"></a>Przykład, który hostuje usługę w aplikacji konsolowej

Ukończony kod usługi powinien wyglądać w następujący sposób. Kod zawiera kontrakt usługi i implementację z poprzednich kroków samouczka i hostuje usługę w aplikacji konsolowej.

```csharp
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         

            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Relay credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }

            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## <a name="create-a-wcf-client-for-the-service-contract"></a>Tworzenie klienta platformy WCF dla kontraktu usługi

Następnym zadaniem jest utworzenie aplikacji klienckiej i zdefiniowanie kontraktu usługi, który zostanie wdrożony później. Te kroki przypominają kroki używane do tworzenia usługi: Definiowanie kontraktu, edytowanie pliku *App.config* przy użyciu poświadczeń w celu nawiązania połączenia z usługą przekazywania itd. Kod używany do wykonywania tych zadań podano w przykładzie zamieszczonym po procedurze.

1. Utwórz nowy projekt w bieżącym rozwiązaniu programu Visual Studio dla klienta:

   1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy bieżące rozwiązanie (nie projekt) i wybierz polecenie **Dodaj**  >  **Nowy projekt**.
   1. W obszarze **Dodaj nowy projekt** wybierz pozycję **aplikacja konsoli (.NET Framework)** dla języka C#, a **następnie** wybierz pozycję Dalej.
   1. Nazwij projekt *EchoClient* i wybierz pozycję **Utwórz**.

1. W **Eksplorator rozwiązań** w projekcie **EchoClient** kliknij dwukrotnie pozycję **program.cs** , aby otworzyć plik w edytorze, jeśli nie jest jeszcze otwarty.
1. Zmień nazwę przestrzeni nazw z domyślnej nazwy `EchoClient` na `Microsoft.ServiceBus.Samples`.
1. Zainstaluj [pakiet NuGet Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus):

   1. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy pozycję **EchoClient** , a następnie wybierz pozycję **Zarządzaj pakietami NuGet**.
   1. Wybierz pozycję **Przeglądaj**, a następnie wyszukaj i wybierz pozycję **windowsazure. ServiceBus**. Wybierz pozycję **Zainstaluj** i zaakceptuj warunki użytkowania.

      ![Zainstaluj pakiet usługi Service Bus][4]

1. Dodaj `using` instrukcję dla przestrzeni nazw [System. ServiceModel](/dotnet/api/system.servicemodel) w pliku *program.cs* .

    ```csharp
    using System.ServiceModel;
    ```

1. Dodaj definicję kontraktu usługi do przestrzeni nazw jak pokazano w poniższym przykładzie. Ta definicja jest taka sama jak definicja użyta w projekcie **usługi** . Dodaj ten kod w górnej części `Microsoft.ServiceBus.Samples` przestrzeni nazw.

    ```csharp
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

1. Wybierz kombinację klawiszy Ctrl + Shift + B, aby skompilować klienta.

### <a name="example-of-the-echoclient-project"></a>Przykład projektu EchoClient

Poniższy kod przedstawia bieżący stan pliku *program.cs* w projekcie **EchoClient** .

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="configure-the-wcf-client"></a>Konfigurowanie klienta platformy WCF

W tym kroku utworzysz plik *App.config* dla podstawowej aplikacji klienckiej, która uzyskuje dostęp do usługi utworzonej wcześniej w tym samouczku. Ten plik *App.config* definiuje kontrakt, powiązanie i nazwę punktu końcowego. Kod używany do wykonywania tych zadań podano w przykładzie zamieszczonym po procedurze.

1. W **Eksplorator rozwiązań** w projekcie **EchoClient** kliknij dwukrotnie pozycję **App.config** , aby otworzyć plik w edytorze programu Visual Studio.
1. W elemencie `<appSettings>` zastąp symbole zastępcze nazwą przestrzeni nazw usługi i kluczem sygnatury dostępu współdzielonego skopiowanym we wcześniejszym kroku.
1. W `system.serviceModel` elemencie Dodaj `<client>` element.

    ```xml
    <?xmlversion="1.0" encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    Ten kod deklaruje, że definiujesz aplikację kliencką w stylu programu WCF.

1. W elemencie `client` zdefiniuj nazwę, kontrakt i typ powiązania punktu końcowego.

    ```xml
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    Ten kod definiuje nazwę punktu końcowego. Definiuje również kontrakt zdefiniowany w usłudze i fakt, że aplikacja kliencka używa protokołu TCP do komunikowania się z Azure Relay. Nazwa punktu końcowego jest używana w następnym kroku do powiązania tej konfiguracji pliku końcowego z identyfikatorem URI usługi.

1. Wybierz pozycję **plik**  >  **Zapisz wszystko**.

### <a name="example-of-the-appconfig-file"></a>Przykład pliku App.config

Poniższy kod przedstawia plik *App.config* dla klienta echo.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="implement-the-wcf-client"></a>Implementowanie klienta WCF

W tej sekcji zaimplementowano podstawową aplikację kliencką, która uzyskuje dostęp do usługi utworzonej wcześniej w tym samouczku. Podobnie jak w przypadku usługi, Klient wykonuje wiele operacji w celu uzyskania dostępu do Azure Relay:

* Ustawienie trybu łączności.
* Utworzenie identyfikatora URI, który lokalizuje usługę hosta.
* Uzyskanie poświadczeń zabezpieczeń.
* Zastosowanie poświadczeń do połączenia.
* Otwarcie połączenia.
* Wykonanie zadań specyficznych dla aplikacji.
* Zamknięcie połączenia.

Jednak jedna z głównych różnic polega na tym, że aplikacja kliencka korzysta z kanału w celu nawiązania połączenia z usługą przekazywania. Usługa używa wywołania do **ServiceHost**. Kod używany do wykonywania tych zadań podano w przykładzie zamieszczonym po procedurze.

### <a name="implement-a-client-application"></a>Implementowanie aplikacji klienckiej

1. Ustaw tryb łączności `AutoDetect`. Dodaj następujący kod w metodzie `Main()` aplikacji **EchoClient**.

    ```csharp
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

1. Zdefiniuj zmienne do przechowywania wartości przestrzeni nazw i klucza sygnatury dostępu współdzielonego odczytanego z okna konsoli.

    ```csharp
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```

1. Utwórz identyfikator URI, który definiuje lokalizację hosta w projekcie przekaźnika.

    ```csharp
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

1. Utwórz obiekt poświadczeń dla punktu końcowego przestrzeni nazw Twojej usługi.

    ```csharp
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

1. Utwórz fabrykę kanałów ładującą konfigurację opisaną w pliku *App.config* .

    ```csharp
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    Fabryka kanałów jest obiektem platformy WCF tworzącym kanał komunikacyjny dla usługi i aplikacji klienckich.

1. Zastosuj poświadczenia.

    ```csharp
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```

1. Utwórz i otwórz kanał dla usługi.

    ```csharp
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```

1. Zapisz podstawowy interfejs użytkownika i funkcje dla echa.

    ```csharp
    Console.WriteLine("Enter text to echo (or [Enter] to exit):");
    string input = Console.ReadLine();
    while (input != String.Empty)
    {
        try
        {
            Console.WriteLine("Server echoed: {0}", channel.Echo(input));
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: " + e.Message);
        }
        input = Console.ReadLine();
    }
    ```

    Kod używa wystąpienia obiektu kanału jako serwera proxy dla usługi.

1. Zamknij kanał i fabrykę.

    ```csharp
    channel.Close();
    channelFactory.Close();
    ```

### <a name="example-code-for-this-tutorial"></a>Przykładowy kod dla tego samouczka

Ukończony kod powinien wyglądać w następujący sposób. Ten kod pokazuje, jak utworzyć aplikację kliencką, sposób wywoływania operacji usługi i jak zamknąć klienta po zakończeniu wywołania operacji.

```csharp
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;


            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

## <a name="run-the-applications"></a>Uruchamianie aplikacji

1. Wybierz kombinację klawiszy Ctrl + Shift + B, aby skompilować rozwiązanie. Ta akcja kompiluje zarówno projekt klienta, jak i projekt usługi, który został utworzony w poprzednich krokach.
1. Przed uruchomieniem aplikacji klienckiej musisz upewnić się, że aplikacja usługi jest uruchomiona. W **Eksplorator rozwiązań** kliknij prawym przyciskiem myszy rozwiązanie **EchoService** , a następnie wybierz polecenie **Właściwości**.
1. Na **stronie właściwości**,   >  **projekt uruchomieniowy** wspólne właściwości, a następnie wybierz **wiele projektów startowych**. Upewnij się, że pozycja **EchoService** jest wyświetlana na początku listy.
1. Ustaw w polu **Akcja** zarówno dla projektu **EchoService**, jak i projektu **EchoClient** ustawienie **Uruchom**.

    ![Strony właściwości projektu][5]

1. Wybierz pozycję **zależności projektu**. W obszarze **projekty** wybierz pozycję **EchoClient**. W **zależności od**, upewnij się, że wybrano opcję **EchoService** .

    ![Zależności projektu][6]

1. Wybierz **przycisk OK** , aby zamknąć **strony właściwości**.
1. Wybierz klawisz F5, aby uruchomić oba projekty.
1. Oba okna konsoli zostaną otwarte z monitami o podanie nazwy przestrzeni nazw. Najpierw należy uruchomić usługę, a następnie w oknie konsoli **EchoService** wprowadź przestrzeń nazw, a następnie wybierz klawisz ENTER.
1. Następnie w konsoli zostanie wyświetlony komunikat z prośbą o klucz sygnatury dostępu współdzielonego. Wprowadź klucz sygnatury dostępu współdzielonego i wybierz klawisz ENTER.

    Oto przykładowe dane wyjściowe z okna konsoli. Poniżej znajdują się poniższe wartości.

    `Your Service Namespace: myNamespace`

    `Your SAS Key: <SAS key value>`

    Aplikacja usługi wyświetla w oknie konsoli adres, na którym nasłuchuje, jak w poniższym przykładzie.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`

    `Press [Enter] to exit`

1. W oknie konsoli **EchoClient** wprowadź te same informacje, które zostały wprowadzone uprzednio dla aplikacji usługi. Wprowadź tę samą przestrzeń nazw usługi i klucz sygnatury dostępu współdzielonego dla aplikacji klienckiej.
1. Po wprowadzeniu tych wartości klient otworzy kanał do usługi i będzie monitować o wprowadzenie tekstu, jak w poniższym przykładzie danych wyjściowych konsoli.

    `Enter text to echo (or [Enter] to exit):`

    Wprowadź tekst, który ma zostać wysłany do aplikacji usługi, a następnie wybierz klawisz ENTER. Ten tekst jest wysyłany do usługi za pośrednictwem operacji usługi Echo i pojawia się w oknie konsoli usługi, jak w poniższym przykładzie danych wyjściowych.

    `Echoing: My sample text`

    Aplikacja kliencka odbiera wartość zwracaną przez operację `Echo`, która jest oryginalnym tekstem, i wyświetla go w oknie swojej konsoli. Poniższy tekst zawiera przykładowe dane wyjściowe z okna konsoli klienta.

    `Server echoed: My sample text`

1. Możesz kontynuować wysyłanie wiadomości tekstowych z klienta do usługi w ten sposób. Po zakończeniu wybierz pozycję Wprowadź w oknach konsoli klienta i usługi, aby zakończyć obie aplikacje.

## <a name="next-steps"></a>Następne kroki

Przejdź do następującego samouczka:

> [!div class="nextstepaction"]
>[Uwidacznianie lokalnej usługi WCF dla klienta WCF spoza sieci](service-bus-relay-rest-tutorial.md)

[2]: ./media/service-bus-relay-tutorial/configure-echoservice-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget-service-bus.png
[4]: ./media/service-bus-relay-tutorial/install-nuget-service-bus-client.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
