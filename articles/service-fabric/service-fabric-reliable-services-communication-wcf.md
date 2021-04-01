---
title: Reliable Services stos komunikacji WCF
description: Wbudowany stos komunikacji WCF w Service Fabric zapewnia komunikację usługi klienta WCF dla Reliable Services.
author: BharatNarasimman
ms.topic: conceptual
ms.date: 06/07/2017
ms.author: bharatn
ms.custom: devx-track-csharp
ms.openlocfilehash: c5b5d413eee2528e2d5c7d04d06f1607949beaae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89012708"
---
# <a name="wcf-based-communication-stack-for-reliable-services"></a>Oparty na WCF stos komunikacji dla Reliable Services
Struktura Reliable Services umożliwia autorom usługi Wybieranie stosu komunikacyjnego, który ma być używany dla swojej usługi. Mogą podłączyć stos komunikacji za pomocą **ICommunicationListener** zwróconych z metod [CreateServiceReplicaListeners lub CreateServiceInstanceListeners](service-fabric-reliable-services-communication.md) . Struktura zapewnia implementację stosu komunikacji na podstawie Windows Communication Foundation (WCF) dla autorów usług, którzy chcą korzystać z komunikacji opartej na platformie WCF.

## <a name="wcf-communication-listener"></a>Odbiornik komunikacji WCF
Implementacja **ICommunicationListener** specyficzna dla programu WCF jest udostępniana przez klasę **Microsoft. servicefabric. Services. Communications. WCF. Runtime. WcfCommunicationListener** .

Lest Załóżmy, że mamy umowę usługi typu `ICalculator`

```csharp
[ServiceContract]
public interface ICalculator
{
    [OperationContract]
    Task<int> Add(int value1, int value2);
}
```

W następujący sposób możemy utworzyć odbiornik komunikacji WCF w usłudze.

```csharp

protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[] { new ServiceReplicaListener((context) =>
        new WcfCommunicationListener<ICalculator>(
            wcfServiceObject:this,
            serviceContext:context,
            //
            // The name of the endpoint configured in the ServiceManifest under the Endpoints section
            // that identifies the endpoint that the WCF ServiceHost should listen on.
            //
            endpointResourceName: "WcfServiceEndpoint",

            //
            // Populate the binding information that you want the service to use.
            //
            listenerBinding: WcfUtility.CreateTcpListenerBinding()
        )
    )};
}

```

## <a name="writing-clients-for-the-wcf-communication-stack"></a>Pisanie klientów dla stosu komunikacji WCF
W przypadku pisania klientów w celu komunikowania się z usługami przy użyciu programu WCF struktura zawiera **WcfClientCommunicationFactory**, która jest implementacją [ClientCommunicationFactoryBase](service-fabric-reliable-services-communication.md)specyficzną dla programu WCF.

```csharp

public WcfCommunicationClientFactory(
    Binding clientBinding = null,
    IEnumerable<IExceptionHandler> exceptionHandlers = null,
    IServicePartitionResolver servicePartitionResolver = null,
    string traceId = null,
    object callback = null);
```

Dostęp do kanału komunikacyjnego WCF można uzyskać z **WcfCommunicationClient** utworzonego przez **WcfCommunicationClientFactory**.

```csharp

public class WcfCommunicationClient : ServicePartitionClient<WcfCommunicationClient<ICalculator>>
   {
       public WcfCommunicationClient(ICommunicationClientFactory<WcfCommunicationClient<ICalculator>> communicationClientFactory, Uri serviceUri, ServicePartitionKey partitionKey = null, TargetReplicaSelector targetReplicaSelector = TargetReplicaSelector.Default, string listenerName = null, OperationRetrySettings retrySettings = null)
           : base(communicationClientFactory, serviceUri, partitionKey, targetReplicaSelector, listenerName, retrySettings)
       {
       }
   }

```

Kod klienta może używać **WcfCommunicationClientFactory** wraz z **WcfCommunicationClient** , który implementuje **ServicePartitionClient** , aby określić punkt końcowy usługi i komunikować się z usługą.

```csharp
// Create binding
Binding binding = WcfUtility.CreateTcpClientBinding();
// Create a partition resolver
IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();
// create a  WcfCommunicationClientFactory object.
var wcfClientFactory = new WcfCommunicationClientFactory<ICalculator>
    (clientBinding: binding, servicePartitionResolver: partitionResolver);

//
// Create a client for communicating with the ICalculator service that has been created with the
// Singleton partition scheme.
//
var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
                wcfClientFactory,
                ServiceUri,
                ServicePartitionKey.Singleton);

//
// Call the service to perform the operation.
//
var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
                client => client.Channel.Add(2, 3)).Result;

```
> [!NOTE]
> Domyślna ServicePartitionResolver zakłada, że klient jest uruchomiony w tym samym klastrze co usługa. Jeśli tak się nie dzieje, Utwórz obiekt ServicePartitionResolver i przekaż punkty końcowe połączenia klastra.
> 
> 

## <a name="next-steps"></a>Następne kroki
* [Zdalne wywołanie procedury z Reliable Services komunikacji zdalnej](service-fabric-reliable-services-communication-remoting.md)
* [Interfejs API sieci Web z OWIN w Reliable Services](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Zabezpieczanie komunikacji dla Reliable Services](service-fabric-reliable-services-secure-communication-wcf.md)
