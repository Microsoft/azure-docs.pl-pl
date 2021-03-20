---
title: Komunikacja zdalna usługi przy użyciu języka Java na platformie Azure Service Fabric
description: Service Fabric komunikacja zdalna umożliwia klientom i usługom komunikowanie się z usługami Java przy użyciu zdalnego wywołania procedury.
author: PavanKunapareddyMSFT
ms.topic: conceptual
ms.date: 06/30/2017
ms.custom: devx-track-java
ms.author: pakunapa
ms.openlocfilehash: d53d20510db70d81aab796efab48de40c880bb3a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87316128"
---
# <a name="service-remoting-in-java-with-reliable-services"></a>Komunikacja zdalna usługi w języku Java z Reliable Services
> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java w systemie Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

W przypadku usług, które nie są powiązane z określonym protokołem lub stosem komunikacyjnym, takim jak WebAPI, Windows Communication Foundation (WCF) lub inne, struktura Reliable Services udostępnia mechanizm komunikacji zdalnej, który umożliwia szybkie i łatwe konfigurowanie zdalnych wywołań procedur dla usług.  W tym artykule omówiono sposób konfigurowania wywołań procedur zdalnych dla usług zapisanych w języku Java.

## <a name="set-up-remoting-on-a-service"></a>Konfigurowanie komunikacji zdalnej w usłudze
Konfigurowanie komunikacji zdalnej dla usługi odbywa się w dwóch prostych krokach:

1. Utwórz interfejs do wdrożenia usługi. Ten interfejs definiuje metody, które są dostępne dla zdalnego wywołania procedury w usłudze. Metody muszą być zwracanymi metodami asynchronicznymi. Interfejs musi implementować `microsoft.serviceFabric.services.remoting.Service` , aby sygnalizować, że usługa ma interfejs komunikacji zdalnej.
2. Użyj odbiornika komunikacji zdalnej w usłudze. Jest to `CommunicationListener` implementacja, która udostępnia możliwości komunikacji zdalnej. `FabricTransportServiceRemotingListener` może służyć do tworzenia odbiornika usług zdalnych przy użyciu domyślnego protokołu transportowego komunikacji zdalnej.

Na przykład następująca usługa bezstanowa udostępnia pojedynczą metodę, aby uzyskać "Hello world" za pośrednictwem zdalnego wywołania procedury.

```java
import java.util.ArrayList;
import java.util.concurrent.CompletableFuture;
import java.util.List;
import microsoft.servicefabric.services.communication.runtime.ServiceInstanceListener;
import microsoft.servicefabric.services.remoting.Service;
import microsoft.servicefabric.services.runtime.StatelessService;

public interface MyService extends Service {
    CompletableFuture<String> helloWorldAsync();
}

class MyServiceImpl extends StatelessService implements MyService {
    public MyServiceImpl(StatelessServiceContext context) {
       super(context);
    }

    public CompletableFuture<String> helloWorldAsync() {
        return CompletableFuture.completedFuture("Hello!");
    }

    @Override
    protected List<ServiceInstanceListener> createServiceInstanceListeners() {
        ArrayList<ServiceInstanceListener> listeners = new ArrayList<>();
        listeners.add(new ServiceInstanceListener((context) -> {
            return new FabricTransportServiceRemotingListener(context,this);
        }));
        return listeners;
    }
}
```

> [!NOTE]
> Argumenty i zwracane typy w interfejsie usługi mogą być dowolnego typu prostego, złożonego lub niestandardowego, ale muszą być możliwe do serializacji.
>
>

## <a name="call-remote-service-methods"></a>Wywoływanie metod usługi zdalnej
Wywoływanie metod w ramach usługi przy użyciu stosu zdalnego jest realizowane przy użyciu lokalnego serwera proxy do usługi za pośrednictwem `microsoft.serviceFabric.services.remoting.client.ServiceProxyBase` klasy. `ServiceProxyBase`Metoda tworzy lokalny serwer proxy przy użyciu tego samego interfejsu, który implementuje usługa. Za pomocą tego serwera proxy można po prostu wywołać metody w interfejsie.

```java

MyService helloWorldClient = ServiceProxyBase.create(MyService.class, new URI("fabric:/MyApplication/MyHelloWorldService"));

CompletableFuture<String> message = helloWorldClient.helloWorldAsync();

```

Struktura komunikacji zdalnej propaguje wyjątki zgłoszone przez usługę do klienta programu. Logika obsługi wyjątków na kliencie przy użyciu programu `ServiceProxyBase` może bezpośrednio obsługiwać wyjątki zgłaszane przez usługę.

## <a name="service-proxy-lifetime"></a>Okres istnienia serwera proxy usługi
Tworzenie ServiceProxy jest operacją uproszczoną, więc można utworzyć dowolną liczbę potrzebną. Wystąpienia serwera proxy usługi mogą być ponownie używane, o ile są one zbędne. Jeśli zdalne wywołanie procedury zgłasza wyjątek, nadal można użyć tego samego wystąpienia serwera proxy. Każdy serwer ServiceProxy zawiera klienta komunikacyjnego służącego do wysyłania komunikatów przez sieć. Podczas wywoływania wywołań zdalnych testy wewnętrzne są wykonywane w celu ustalenia, czy klient komunikacyjny jest prawidłowy. W zależności od wyników tych testów klient komunikacyjny zostanie odtworzony w razie konieczności. W związku z tym, jeśli wystąpi wyjątek, nie trzeba ponownie tworzyć `ServiceProxy` .

### <a name="serviceproxyfactory-lifetime"></a>ServiceProxyFactory okres istnienia
[FabricServiceProxyFactory](/java/api/microsoft.servicefabric.services.remoting.client.fabricserviceproxyfactory) to fabryka, która tworzy serwer proxy dla różnych interfejsów komunikacji zdalnej. Jeśli używasz interfejsu API `ServiceProxyBase.create` do tworzenia serwera proxy, platforma utworzy `FabricServiceProxyFactory` .
Warto utworzyć je ręcznie, gdy zachodzi potrzeba zastąpienia właściwości [ServiceRemotingClientFactory](/java/api/microsoft.servicefabric.services.remoting.client.serviceremotingclientfactory) .
Fabryka jest kosztowną operacją. `FabricServiceProxyFactory` obsługuje pamięć podręczną klientów komunikacyjnych.
Najlepszym rozwiązaniem jest buforowanie `FabricServiceProxyFactory` tak długo, jak to możliwe.

## <a name="remoting-exception-handling"></a>Obsługa wyjątków zdalnych
Wszystkie wyjątki zdalne zgłoszone przez interfejs API usługi są odsyłane do klienta jako RuntimeException lub Fabricexception.

Usługa ServiceProxy obsługuje wszystkie wyjątki trybu failover dla partycji usługi, dla której została utworzona. Powoduje to ponowne rozpoznanie punktów końcowych, jeśli występują wyjątki trybu failover (wyjątki nieprzejściowe) i ponawianie próby wywołania z prawidłowym punktem końcowym. Liczba ponownych prób dla wyjątku trybu failover jest nieokreślona.
W przypadku TransientExceptions jest to tylko ponowna próba wywołania.

Domyślne parametry ponowień są dostarczają przez [OperationRetrySettings](/java/api/microsoft.servicefabric.services.communication.client.operationretrysettings).
Można skonfigurować te wartości, przekazując obiekt OperationRetrySettings do konstruktora ServiceProxyFactory.

## <a name="next-steps"></a>Następne kroki
* [Zabezpieczanie komunikacji dla Reliable Services](service-fabric-reliable-services-secure-communication-java.md)
