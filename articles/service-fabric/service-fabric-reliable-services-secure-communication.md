---
title: 'Zabezpieczanie komunikacji zdalnej usługi przy użyciu języka C #'
description: Dowiedz się, jak zabezpieczyć komunikację zdalną usługi na podstawie komunikacji zdalnej dla niezawodnych usług w języku C#, które działają w klastrze Service Fabric platformy Azure.
author: suchiagicha
ms.topic: conceptual
ms.date: 04/20/2017
ms.author: pepogors
ms.openlocfilehash: ba68df53f1f21b9ff360772fe1a60c93c8df74d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86252219"
---
# <a name="secure-service-remoting-communications-in-a-c-service"></a>Bezpieczna komunikacja zdalna usługi w usłudze C#
> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java w systemie Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

Bezpieczeństwo jest jednym z najważniejszych aspektów komunikacji. Platforma aplikacji Reliable Services zawiera kilka wstępnie utworzonych stosów i narzędzi do komunikacji, których można użyć w celu zwiększenia bezpieczeństwa. W tym artykule omówiono sposób ulepszania zabezpieczeń w przypadku korzystania z komunikacji zdalnej usługi w usłudze C#. Jest ona oparta na istniejącym [przykładzie](service-fabric-reliable-services-communication-remoting.md) , który wyjaśnia, jak skonfigurować obsługę zdalną dla niezawodnych usług pisanych w języku C#. 

Aby pomóc w zabezpieczeniu usługi w przypadku korzystania z usług zdalnych usługi w języku C#, wykonaj następujące kroki:

1. Utwórz interfejs, `IHelloWorldStateful` który definiuje metody, które będą dostępne dla zdalnego wywołania procedury w usłudze. Zostanie użyta usługa `FabricTransportServiceRemotingListener` , która jest zadeklarowana w `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` przestrzeni nazw. Jest to `ICommunicationListener` implementacja, która udostępnia możliwości komunikacji zdalnej.

    ```csharp
    public interface IHelloWorldStateful : IService
    {
        Task<string> GetHelloWorld();
    }

    internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
    {
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]{
                    new ServiceReplicaListener(
                        (context) => new FabricTransportServiceRemotingListener(context,this))};
        }

        public Task<string> GetHelloWorld()
        {
            return Task.FromResult("Hello World!");
        }
    }
    ```
2. Dodaj ustawienia odbiornika i poświadczenia zabezpieczeń.

    Upewnij się, że certyfikat, którego chcesz użyć, aby zabezpieczyć komunikację usługi, został zainstalowany na wszystkich węzłach w klastrze. 
    
    > [!NOTE]
    > W węzłach systemu Linux certyfikat musi być obecny jako plik w formacie PEM w katalogu */var/lib/sfcerts* . Aby dowiedzieć się więcej, zobacz temat [lokalizowanie i formatowanie certyfikatów X. 509 w węzłach systemu Linux](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes). 

    Istnieją dwa sposoby zapewnienia ustawień odbiornika i poświadczeń zabezpieczeń:

   1. Podaj je bezpośrednio w kodzie usługi:

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           FabricTransportRemotingListenerSettings  listenerSettings = new FabricTransportRemotingListenerSettings
           {
               MaxMessageSize = 10000000,
               SecurityCredentials = GetSecurityCredentials()
           };
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(context,this,listenerSettings))
           };
       }

       private static SecurityCredentials GetSecurityCredentials()
       {
           // Provide certificate details.
           var x509Credentials = new X509Credentials
           {
               FindType = X509FindType.FindByThumbprint,
               FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
               StoreLocation = StoreLocation.LocalMachine,
               StoreName = "My",
               ProtectionLevel = ProtectionLevel.EncryptAndSign
           };
           x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
           x509Credentials.RemoteCertThumbprints.Add("9FEF3950642138446CC364A396E1E881DB76B483");
           return x509Credentials;
       }
       ```
   2. Podaj je przy użyciu [pakietu konfiguracji](service-fabric-application-and-service-manifests.md):

       Dodaj nazwaną `TransportSettings` sekcję w pliku settings.xml.

       ```xml
       <Section Name="HelloWorldStatefulTransportSettings">
           <Parameter Name="MaxMessageSize" Value="10000000" />
           <Parameter Name="SecurityCredentialsType" Value="X509" />
           <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
           <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
           <Parameter Name="CertificateRemoteThumbprints" Value="9FEF3950642138446CC364A396E1E881DB76B483" />
           <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
           <Parameter Name="CertificateStoreName" Value="My" />
           <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
           <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
       </Section>
       ```

       W takim przypadku `CreateServiceReplicaListeners` Metoda będzie wyglądać następująco:

       ```csharp
       protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
       {
           return new[]
           {
               new ServiceReplicaListener(
                   (context) => new FabricTransportServiceRemotingListener(
                       context,this,FabricTransportRemotingListenerSettings .LoadFrom("HelloWorldStatefulTransportSettings")))
           };
       }
       ```

        W przypadku dodania `TransportSettings` sekcji w pliku settings.xml program `FabricTransportRemotingListenerSettings` Domyślnie załaduje wszystkie ustawienia z tej sekcji.

        ```xml
        <!--"TransportSettings" section .-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        W takim przypadku `CreateServiceReplicaListeners` Metoda będzie wyglądać następująco:

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]
            {
                return new[]{
                        new ServiceReplicaListener(
                            (context) => new FabricTransportServiceRemotingListener(context,this))};
            };
        }
        ```
3. Gdy wywołujesz metody w zabezpieczonej usłudze przy użyciu stosu zdalnego, zamiast używać `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` klasy do tworzenia serwera proxy usługi, użyj polecenia `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory` . Przekaż `FabricTransportRemotingSettings` , który zawiera `SecurityCredentials` .

    ```csharp

    var x509Credentials = new X509Credentials
    {
        FindType = X509FindType.FindByThumbprint,
        FindValue = "9FEF3950642138446CC364A396E1E881DB76B483",
        StoreLocation = StoreLocation.LocalMachine,
        StoreName = "My",
        ProtectionLevel = ProtectionLevel.EncryptAndSign
    };
    x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
    x509Credentials.RemoteCertThumbprints.Add("4FEF3950642138446CC364A396E1E881DB76B48C");

    FabricTransportRemotingSettings transportSettings = new FabricTransportRemotingSettings
    {
        SecurityCredentials = x509Credentials,
    };

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(transportSettings));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Jeśli kod klienta jest uruchomiony w ramach usługi, można załadować `FabricTransportRemotingSettings` z pliku settings.xml. Utwórz sekcję HelloWorldClientTransportSettings podobną do kodu usługi, jak pokazano wcześniej. Wprowadź następujące zmiany w kodzie klienta:

    ```csharp
    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.LoadFrom("HelloWorldClientTransportSettings")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Jeśli klient nie działa w ramach usługi, można utworzyć plik client_name.settings.xml w tej samej lokalizacji, w której znajduje się client_name.exe. Następnie utwórz sekcję TransportSettings w tym pliku.

    Podobnie jak w przypadku dodania `TransportSettings` sekcji w settings.xmlklienta/client_name.settings.xml, program `FabricTransportRemotingSettings` Domyślnie ładuje wszystkie ustawienia z tej sekcji.

    W takim przypadku poprzedni kod jest jeszcze bardziej uproszczony:  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```


W następnym kroku Przeczytaj [internetowy interfejs API z Owin w Reliable Services](./service-fabric-reliable-services-communication-aspnetcore.md).
