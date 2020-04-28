---
title: Zabezpieczanie komunikacji zdalnej usługi przy użyciu języka Java
description: Dowiedz się, jak zabezpieczyć komunikację zdalną usługi na podstawie komunikacji zdalnej dla niezawodnych usług Java, które działają w klastrze Service Fabric platformy Azure.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.author: pakunapa
ms.openlocfilehash: adefeadf939d398268624343d82c18cbf5ec87cd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75609642"
---
# <a name="secure-service-remoting-communications-in-a-java-service"></a>Bezpieczna komunikacja zdalna usługi w usłudze Java
> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-services-secure-communication.md)
> * [Java w systemie Linux](service-fabric-reliable-services-secure-communication-java.md)
>
>

Bezpieczeństwo jest jednym z najważniejszych aspektów komunikacji. Platforma aplikacji Reliable Services zawiera kilka wstępnie utworzonych stosów i narzędzi do komunikacji, których można użyć w celu zwiększenia bezpieczeństwa. W tym artykule omówiono sposób ulepszania zabezpieczeń w przypadku korzystania z komunikacji zdalnej usługi w usłudze Java. Na istniejącym [przykładzie](service-fabric-reliable-services-communication-remoting-java.md) przedstawiono sposób konfigurowania komunikacji zdalnej dla niezawodnych usług pisanych w języku Java. 

Aby zabezpieczyć usługę w przypadku korzystania z usług zdalnych w usłudze Java, wykonaj następujące kroki:

1. Utwórz interfejs `HelloWorldStateless`, który definiuje metody, które będą dostępne dla zdalnego wywołania procedury w usłudze. Zostanie użyta `FabricTransportServiceRemotingListener`usługa, która jest zadeklarowana w `microsoft.serviceFabric.services.remoting.fabricTransport.runtime` pakiecie. Jest to `CommunicationListener` implementacja, która udostępnia możliwości komunikacji zdalnej.

    ```java
    public interface HelloWorldStateless extends Service {
        CompletableFuture<String> getHelloWorld();
    }

    class HelloWorldStatelessImpl extends StatelessService implements HelloWorldStateless {
        @Override
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
        return listeners;
        }

        public CompletableFuture<String> getHelloWorld() {
            return CompletableFuture.completedFuture("Hello World!");
        }
    }
    ```
2. Dodaj ustawienia odbiornika i poświadczenia zabezpieczeń.

    Upewnij się, że certyfikat, którego chcesz użyć, aby zabezpieczyć komunikację usługi, został zainstalowany na wszystkich węzłach w klastrze. W przypadku usług działających w systemie Linux certyfikat musi być dostępny jako plik PEM-formmatted. `.pem` plik zawierający certyfikat i klucz prywatny lub `.crt` plik zawierający certyfikat oraz `.key` plik, który zawiera klucz prywatny. Aby dowiedzieć się więcej, zobacz temat [lokalizowanie i formatowanie certyfikatów X. 509 w węzłach systemu Linux](./service-fabric-configure-certificates-linux.md#location-and-format-of-x509-certificates-on-linux-nodes).
    
    Istnieją dwa sposoby zapewnienia ustawień odbiornika i poświadczeń zabezpieczeń:

   1. Podaj je przy użyciu [pakietu konfiguracji](service-fabric-application-and-service-manifests.md):

       Dodaj nazwaną `TransportSettings` sekcję w pliku Settings. XML.

       ```xml
       <!--Section name should always end with "TransportSettings".-->
       <!--Here we are using a prefix "HelloWorldStateless".-->
        <Section Name="HelloWorldStatelessTransportSettings">
            <Parameter Name="MaxMessageSize" Value="10000000" />
            <Parameter Name="SecurityCredentialsType" Value="X509_2" />
            <Parameter Name="CertificatePath" Value="/path/to/cert/BD1C71E248B8C6834C151174DECDBDC02DE1D954.crt" />
            <Parameter Name="CertificateProtectionLevel" Value="EncryptandSign" />
            <Parameter Name="CertificateRemoteThumbprints" Value="BD1C71E248B8C6834C151174DECDBDC02DE1D954" />
        </Section>

       ```

       W takim przypadku `createServiceInstanceListeners` Metoda będzie wyglądać następująco:

       ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this, FabricTransportRemotingListenerSettings.loadFrom(HelloWorldStatelessTransportSettings));
            }));
            return listeners;
        }
       ```

        Jeśli dodasz `TransportSettings` sekcję w pliku Settings. XML bez żadnego prefiksu, `FabricTransportListenerSettings` program załaduje domyślnie wszystkie ustawienia z tej sekcji.

        ```xml
        <!--"TransportSettings" section without any prefix.-->
        <Section Name="TransportSettings">
            ...
        </Section>
        ```
        W takim przypadku `CreateServiceInstanceListeners` Metoda będzie wyglądać następująco:

        ```java
        protected List<ServiceInstanceListener> createServiceInstanceListeners() {
            ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
            listeners.add(new ServiceInstanceListener((context) -> {
                return new FabricTransportServiceRemotingListener(context,this);
            }));
            return listeners;
        }
       ```
3. Gdy wywołujesz metody w zabezpieczonej usłudze przy użyciu stosu zdalnego, zamiast używać `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` klasy do tworzenia serwera proxy usługi, użyj polecenia. `microsoft.serviceFabric.services.remoting.client.FabricServiceProxyFactory`

    Jeśli kod klienta jest uruchomiony w ramach usługi, można załadować `FabricTransportSettings` z pliku Settings. XML. Utwórz sekcję TransportSettings podobną do kodu usługi, jak pokazano wcześniej. Wprowadź następujące zmiany w kodzie klienta:

    ```java

    FabricServiceProxyFactory serviceProxyFactory = new FabricServiceProxyFactory(c -> {
            return new FabricTransportServiceRemotingClientFactory(FabricTransportRemotingSettings.loadFrom("TransportPrefixTransportSettings"), null, null, null, null);
        }, null)

    HelloWorldStateless client = serviceProxyFactory.createServiceProxy(HelloWorldStateless.class,
        new URI("fabric:/MyApplication/MyHelloWorldService"));

    CompletableFuture<String> message = client.getHelloWorld();

    ```
