---
title: Komunikacja zdalna usługi przy użyciu języka C# w Service Fabric
description: Service Fabric komunikacja zdalna umożliwia klientom i usługom komunikowanie się z usługami C# przy użyciu zdalnego wywołania procedury.
author: vturecek
ms.topic: conceptual
ms.date: 09/20/2017
ms.author: vturecek
ms.custom: devx-track-csharp
ms.openlocfilehash: 246b1456c05605c4015c19e1a139e9ad65f6eaba
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89022160"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>Komunikacja zdalna usługi w języku C# z Reliable Services

> [!div class="op_single_selector"]
> * [C# w systemie Windows](service-fabric-reliable-services-communication-remoting.md)
> * [Java w systemie Linux](service-fabric-reliable-services-communication-remoting-java.md)
>
>

W przypadku usług, które nie są powiązane z określonym protokołem lub stosem komunikacyjnym, takim jak internetowy interfejs API, Windows Communication Foundation lub inne, środowisko Reliable Services Framework udostępnia mechanizm komunikacji zdalnej w celu szybkiego i łatwego konfigurowania zdalnych wywołań procedur dla usług. W tym artykule omówiono sposób konfigurowania wywołań procedur zdalnych dla usług pisanych przy użyciu języka C#.

## <a name="set-up-remoting-on-a-service"></a>Konfigurowanie komunikacji zdalnej w usłudze

Obsługę zdalną usługi można skonfigurować w dwóch prostych krokach:

1. Utwórz interfejs do wdrożenia usługi. Ten interfejs definiuje metody, które są dostępne dla zdalnego wywołania procedury w usłudze. Metody muszą być zwracanymi metodami asynchronicznymi. Interfejs musi implementować `Microsoft.ServiceFabric.Services.Remoting.IService` , aby sygnalizować, że usługa ma interfejs komunikacji zdalnej.
2. Użyj odbiornika komunikacji zdalnej w usłudze. Odbiornik komunikacji zdalnej jest `ICommunicationListener` implementacją, która zapewnia obsługę zdalną. `Microsoft.ServiceFabric.Services.Remoting.Runtime`Przestrzeń nazw zawiera metodę rozszerzenia `CreateServiceRemotingInstanceListeners` dla usług bezstanowych i stanowych, które mogą być używane do tworzenia odbiornika usług zdalnych przy użyciu domyślnego protokołu transportowego komunikacji zdalnej.

>[!NOTE]
>`Remoting`Przestrzeń nazw jest dostępna jako oddzielny pakiet NuGet o nazwie `Microsoft.ServiceFabric.Services.Remoting` .

Na przykład następująca usługa bezstanowa udostępnia pojedynczą metodę, aby uzyskać "Hello world" za pośrednictwem zdalnego wywołania procedury.

```csharp
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Remoting;
using Microsoft.ServiceFabric.Services.Remoting.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

public interface IMyService : IService
{
    Task<string> HelloWorldAsync();
}

class MyService : StatelessService, IMyService
{
    public MyService(StatelessServiceContext context)
        : base (context)
    {
    }

    public Task<string> HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
     return this.CreateServiceRemotingInstanceListeners();
    }
}
```

> [!NOTE]
> Argumenty i zwracane typy w interfejsie usługi mogą być dowolnego typu prostego, złożonego lub niestandardowego, ale muszą być w stanie serializować przez obiekt [DataContractSerializer](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer)platformy .NET.
>
>

## <a name="call-remote-service-methods"></a>Wywoływanie metod usługi zdalnej

Wywoływanie metod w ramach usługi przy użyciu stosu zdalnego jest realizowane przy użyciu lokalnego serwera proxy do usługi za pośrednictwem `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` klasy. `ServiceProxy`Metoda tworzy lokalny serwer proxy przy użyciu tego samego interfejsu, który implementuje usługa. Za pomocą tego serwera proxy można wywoływać metody w interfejsie zdalnie.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

Struktura komunikacji zdalnej propaguje wyjątki zgłoszone przez usługę do klienta programu. W związku z tym, gdy `ServiceProxy` jest używany, klient jest odpowiedzialny za obsługę wyjątków zgłoszonych przez usługę.

## <a name="service-proxy-lifetime"></a>Okres istnienia serwera proxy usługi

Tworzenie serwera proxy usługi jest operacją uproszczoną, więc można utworzyć dowolną liczbę potrzebną. Wystąpienia serwera proxy usługi mogą być ponownie używane do czasu, gdy są one zbędne. Jeśli zdalne wywołanie procedury zgłasza wyjątek, nadal można użyć tego samego wystąpienia serwera proxy. Każdy serwer proxy usługi zawiera klienta komunikacyjnego służącego do wysyłania komunikatów przez sieć. Podczas wywoływania wywołań zdalnych testy wewnętrzne są wykonywane w celu ustalenia, czy klient komunikacyjny jest prawidłowy. W zależności od wyników tych sprawdzeń klient komunikacyjny zostanie utworzony w razie potrzeby. W związku z tym, jeśli wystąpi wyjątek, nie trzeba ponownie tworzyć `ServiceProxy` .

### <a name="service-proxy-factory-lifetime"></a>Okres istnienia fabryki serwerów proxy usługi

[ServiceProxyFactory](/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) to fabryka, która tworzy wystąpienia serwera proxy dla różnych interfejsów komunikacji zdalnej. Jeśli używasz interfejsu API `ServiceProxyFactory.CreateServiceProxy` do tworzenia serwera proxy, struktura tworzy serwer proxy usługi pojedynczej.
Warto utworzyć je ręcznie, gdy zachodzi potrzeba zastąpienia właściwości [IServiceRemotingClientFactory](/dotnet/api/microsoft.servicefabric.services.remoting.v1.client.iserviceremotingclientfactory) .
Tworzenie fabryki jest kosztowną operacją. Fabryka serwerów proxy usługi utrzymuje wewnętrzną pamięć podręczną klienta komunikacyjnego.
Najlepszym rozwiązaniem jest buforowanie fabryki proxy usługi tak długo, jak to możliwe.

## <a name="remoting-exception-handling"></a>Obsługa wyjątków zdalnych

Wszystkie wyjątki zdalne zgłoszone przez interfejs API usługi są odsyłane do klienta jako AggregateException. Wyjątki zdalne powinny być możliwe do serializacji za pomocą schematu DataContract. Jeśli nie, interfejs API proxy zgłasza wyjątek [ServiceException](/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) z błędem serializacji w tym elemencie.

Serwer proxy usługi obsługuje wszystkie wyjątki trybu failover dla partycji usługi, dla której jest tworzona. Powoduje to ponowne rozpoznanie punktów końcowych, jeśli występują wyjątki trybu failover (wyjątki nieprzejściowe) i ponawianie próby wywołania z prawidłowym punktem końcowym. Liczba ponownych prób w przypadku wyjątków trybu failover jest nieokreślona.
W przypadku wystąpienia wyjątków przejściowych serwer proxy ponawia próbę wywołania.

Domyślne parametry ponawiania prób są udostępniane przez [OperationRetrySettings](/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings).

Użytkownik może skonfigurować te wartości, przekazując obiekt OperationRetrySettings do konstruktora ServiceProxyFactory.

## <a name="use-the-remoting-v2-stack"></a>Korzystanie ze stosu usług zdalnych w wersji 2

Począwszy od wersji 2,8 pakietu funkcji komunikacji zdalnej NuGet, można użyć stosu zdalnego systemu v2. Stos usług zdalnych w wersji 2 wykonuje lepsze. Udostępnia również funkcje takie jak Serializacja niestandardowa i więcej interfejsów API do podłączania.
Kod szablonu nadal używa stosu zdalnego v1.
Komunikacja zdalna v2 nie jest zgodna z V1 (poprzedni stos komunikacji zdalnej). Postępuj zgodnie z instrukcjami zawartymi w artykule  [uaktualnienie z wersji od 1 do v2](#upgrade-from-remoting-v1-to-remoting-v2) , aby uniknąć wpływu na dostępność usługi.

Dostępne są następujące podejścia umożliwiające włączenie stosu v2.

### <a name="use-an-assembly-attribute-to-use-the-v2-stack"></a>Użyj atrybutu zestawu, aby użyć stosu v2

Te kroki zmieniają kod szablonu, aby używać stosu v2 przy użyciu atrybutu zestawu.

1. Zmień zasób punktu końcowego z `"ServiceEndpoint"` na na `"ServiceEndpointV2"` w manifeście usługi.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. Użyj `Microsoft.ServiceFabric.Services.Remoting.Runtime.CreateServiceRemotingInstanceListeners`  metody rozszerzającej, aby utworzyć odbiorniki komunikacji zdalnej (równe zarówno V1, jak i v2).

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. Oznacz zestaw zawierający interfejsy komunikacji zdalnej z `FabricTransportServiceRemotingProvider` atrybutem.

   ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
   ```

W projekcie klienta nie są wymagane żadne zmiany w kodzie.
Utwórz zestaw klienta z zestawem interfejsów, aby upewnić się, że jest używany atrybut zestawu poprzednio wyświetlany.

### <a name="use-explicit-v2-classes-to-use-the-v2-stack"></a>Użyj jawnych klas v2 do korzystania ze stosu v2

Alternatywnie przy użyciu atrybutu zestawu, można również włączyć stos v2 przy użyciu jawnych klas w wersji 2.

Te kroki zmieniają kod szablonu, aby używać stosu v2 przy użyciu jawnych klas w wersji 2.

1. Zmień zasób punktu końcowego z `"ServiceEndpoint"` na na `"ServiceEndpointV2"` w manifeście usługi.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. Użyj [FabricTransportServiceRemotingListener](/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener?view=azure-dotnet) z `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Runtime` przestrzeni nazw.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                return new FabricTransportServiceRemotingListener(c, this);

            })
        };
    }
   ```

3. Użyj [FabricTransportServiceRemotingClientFactory](/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet) z `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client` przestrzeni nazw, aby utworzyć klientów.

   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2"></a>Uaktualnianie z usług zdalnych w wersji 1 do wersji 2

Aby uaktualnić program z wersji 1 do wersji 2, wymagane są uaktualnienia dwuetapowe. Wykonaj kroki opisane w tej sekwencji.

1. Uaktualnij usługę V1 do wersji 2 przy użyciu tego atrybutu.
Ta zmiana gwarantuje, że usługa nasłuchuje na odbiorniku V1 i v2.

    a. Dodaj zasób punktu końcowego o nazwie "ServiceEndpointV2" w manifeście usługi.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b. Użyj następującej metody rozszerzenia, aby utworzyć odbiornik komunikacji zdalnej.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Dodaj atrybut zestawu do interfejsów zdalnych, aby używać odbiornika V1 i v2 oraz klienta v2.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2|RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2)]

      ```
2. Uaktualnij klienta V1 do klienta v2 przy użyciu atrybutu klienta v2.
Ten krok zapewnia, że klient używa stosu v2.
Brak zmian w projekcie/usłudze klienta nie jest wymagana. Wystarcza tworzenie projektów klientów ze zaktualizowanym zestawem interfejsów.

3. Ta czynność jest opcjonalna. Użyj atrybutu odbiornika v2, a następnie Uaktualnij usługę v2.
Ten krok zapewnia, że usługa nasłuchuje tylko na odbiorniku v2.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
    ```


## <a name="use-the-remoting-v2-interface-compatible-stack"></a>Używanie stosu zdalnego v2 (zgodnego z interfejsem)

 Stos usług zdalnych w wersji 2 (zgodny z interfejsem, znany jako V2_1) ma wszystkie funkcje stosu komunikacji zdalnej w wersji 2. Jego stos interfejsu jest zgodny ze stosem zdalnego V1, ale nie jest zgodny z wersjami 2 i v1. Aby uaktualnić program z wersji 1 do V2_1 bez wpływu na dostępność usługi, wykonaj kroki opisane w artykule uaktualnienie z wersji od 1 do v2 (zgodne z interfejsem).


### <a name="use-an-assembly-attribute-to-use-the-remoting-v2-interface-compatible-stack"></a>Użyj atrybutu zestawu, aby użyć stosu zdalnego v2 (zgodnego z interfejsem)

Wykonaj następujące kroki, aby zmienić stos V2_1.

1. Dodaj zasób punktu końcowego o nazwie "ServiceEndpointV2_1" w manifeście usługi.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Użyj metody rozszerzenia komunikacji zdalnej, aby utworzyć odbiornik komunikacji zdalnej.

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. Dodaj [atrybut zestawu](/dotnet/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportserviceremotingproviderattribute?view=azure-dotnet) do interfejsów zdalnych.

   ```csharp
    [assembly:  FabricTransportServiceRemotingProvider(RemotingListenerVersion=  RemotingListenerVersion.V2_1, RemotingClientVersion= RemotingClientVersion.V2_1)]

   ```

W projekcie klienta nie są wymagane żadne zmiany.
Skompiluj zestaw klienta z zestawem interfejsów, aby upewnić się, że jest używany poprzedni atrybut zestawu.

### <a name="use-explicit-remoting-classes-to-create-a-listenerclient-factory-for-the-v2-interface-compatible-version"></a>Używanie jawnych klas zdalnych w celu utworzenia fabryki odbiornika/klienta dla wersji 2 (zgodnej z interfejsem)

Wykonaj następujące kroki:

1. Dodaj zasób punktu końcowego o nazwie "ServiceEndpointV2_1" w manifeście usługi.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Użyj [odbiornika usług zdalnych w wersji 2](/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener?view=azure-dotnet). Użyta nazwa domyślnego zasobu punktu końcowego usługi to "ServiceEndpointV2_1". Musi być zdefiniowany w manifeście usługi.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                var settings = new FabricTransportRemotingListenerSettings();
                settings.UseWrappedMessage = true;
                return new FabricTransportServiceRemotingListener(c, this,settings);

            })
        };
    }
   ```

3. Użyj [fabryki klienckiej](/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory?view=azure-dotnet)v2.
   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
            var settings = new FabricTransportRemotingSettings();
            settings.UseWrappedMessage = true;
            return new FabricTransportServiceRemotingClientFactory(settings);
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2-interface-compatible"></a>Uaktualnianie z usług zdalnych w wersji 1 do wersji 2 usług zdalnych (zgodne z interfejsem)

W celu uaktualnienia z wersji 1 do wersji 2 (zgodnej z interfejsem, znanej jako V2_1) wymagane są uaktualnienia dwuetapowe. Wykonaj kroki opisane w tej sekwencji.

> [!NOTE]
> W przypadku uaktualniania z wersji 1 do wersji 2 Upewnij się, że `Remoting` przestrzeń nazw została zaktualizowana do korzystania z wersji 2. Przykład: "Microsoft. servicefabric. Services. zdalne. v2. FabricTransport. Client"
>
>

1. Uaktualnij usługę V1 do usługi V2_1 przy użyciu następującego atrybutu.
Ta zmiana gwarantuje, że usługa nasłuchuje w punkcie 1 i odbiorniku V2_1.

    a. Dodaj zasób punktu końcowego o nazwie "ServiceEndpointV2_1" w manifeście usługi.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2_1" />  
        </Endpoints>
      </Resources>
      ```

    b. Użyj następującej metody rozszerzenia, aby utworzyć odbiornik komunikacji zdalnej.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Dodaj atrybut Assembly do interfejsów zdalnych, aby używać odbiornika V1, V2_1 i V2_1 Client.
    ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1 | RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2_1)]

      ```
2. Uaktualnij klienta V1 do klienta V2_1 przy użyciu atrybutu V2_1 Client.
Ten krok zapewnia, że klient używa stosu V2_1.
Brak zmian w projekcie/usłudze klienta nie jest wymagana. Wystarcza tworzenie projektów klientów ze zaktualizowanym zestawem interfejsów.

3. Ta czynność jest opcjonalna. Usuń wersję odbiornika V1 z atrybutu, a następnie Uaktualnij usługę v2.
Ten krok zapewnia, że usługa nasłuchuje tylko na odbiorniku v2.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1, RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```
  
### <a name="use-custom-serialization-with-a-remoting-wrapped-message"></a>Używanie serializacji niestandardowej z zawiniętym komunikatem obsługi zdalnej

W przypadku wiadomości zapakowanej zdalnie tworzymy pojedynczy opakowany obiekt ze wszystkimi parametrami w postaci pola.
Wykonaj następujące kroki:

1. Zaimplementuj `IServiceRemotingMessageSerializationProvider` interfejs, aby zapewnić implementację dla serializacji niestandardowej.
    Ten fragment kodu pokazuje, jak wygląda implementacja.

      ```csharp
      public class ServiceRemotingJsonSerializationProvider : IServiceRemotingMessageSerializationProvider
      {
        public IServiceRemotingMessageBodyFactory CreateMessageBodyFactory()
        {
          return new JsonMessageFactory();
        }

        public IServiceRemotingRequestMessageBodySerializer CreateRequestMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> requestWrappedType, IEnumerable<Type> requestBodyTypes = null)
        {
          return new ServiceRemotingRequestJsonMessageBodySerializer();
        }

        public IServiceRemotingResponseMessageBodySerializer CreateResponseMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> responseWrappedType, IEnumerable<Type> responseBodyTypes = null)
        {
          return new ServiceRemotingResponseJsonMessageBodySerializer();
        }
      }
      ```
      ```csharp
        class JsonMessageFactory : IServiceRemotingMessageBodyFactory
            {

              public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName, int numberOfParameters, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }

              public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }
            }
      ```
      ```csharp
      class ServiceRemotingRequestJsonMessageBodySerializer : IServiceRemotingRequestMessageBodySerializer
        {
            private JsonSerializer serializer;

            public ServiceRemotingRequestJsonMessageBodySerializer()
            {
              serializer = JsonSerializer.Create(new JsonSerializerSettings()
              {
                TypeNameHandling = TypeNameHandling.All
                });
              }

              public IOutgoingMessageBody Serialize(IServiceRemotingRequestMessageBody serviceRemotingRequestMessageBody)
             {
               if (serviceRemotingRequestMessageBody == null)
               {
                 return null;
               }          
               using (var writeStream = new MemoryStream())
               {
                 using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
                 {
                   serializer.Serialize(jsonWriter, serviceRemotingRequestMessageBody);
                   jsonWriter.Flush();
                   var bytes = writeStream.ToArray();
                   var segment = new ArraySegment<byte>(bytes);
                   var segments = new List<ArraySegment<byte>> { segment };
                   return new OutgoingMessageBody(segments);
                 }
               }
              }

              public IServiceRemotingRequestMessageBody Deserialize(IIncomingMessageBody messageBody)
             {
               using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
               {
                 using (JsonReader reader = new JsonTextReader(sr))
                 {
                   var ob = serializer.Deserialize<JsonBody>(reader);
                   return ob;
                 }
               }
             }
            }
      ```
      ```csharp
      class ServiceRemotingResponseJsonMessageBodySerializer : IServiceRemotingResponseMessageBodySerializer
       {
         private JsonSerializer serializer;

        public ServiceRemotingResponseJsonMessageBodySerializer()
        {
          serializer = JsonSerializer.Create(new JsonSerializerSettings()
          {
              TypeNameHandling = TypeNameHandling.All
            });
          }

          public IOutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
          {
            if (responseMessageBody == null)
            {
              return null;
            }

            using (var writeStream = new MemoryStream())
            {
              using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
              {
                serializer.Serialize(jsonWriter, responseMessageBody);
                jsonWriter.Flush();
                var bytes = writeStream.ToArray();
                var segment = new ArraySegment<byte>(bytes);
                var segments = new List<ArraySegment<byte>> { segment };
                return new OutgoingMessageBody(segments);
              }
            }
          }

          public IServiceRemotingResponseMessageBody Deserialize(IIncomingMessageBody messageBody)
          {

             using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
             {
               using (var reader = new JsonTextReader(sr))
               {
                 var obj = serializer.Deserialize<JsonBody>(reader);
                 return obj;
               }
             }
           }
       }
    ```
    ```csharp
    class JsonBody : WrappedMessage, IServiceRemotingRequestMessageBody, IServiceRemotingResponseMessageBody
    {
          public JsonBody(object wrapped)
          {
            this.Value = wrapped;
          }

          public void SetParameter(int position, string parameName, object parameter)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object GetParameter(int position, string parameName, Type paramType)
          {
            //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public void Set(object response)
          { //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object Get(Type paramType)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }
    }
    ```

2. Zastąp domyślny dostawcę serializacji `JsonSerializationProvider` dla odbiorniku komunikacji zdalnej.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
   {
       return new[]
       {
           new ServiceInstanceListener((c) =>
           {
               return new FabricTransportServiceRemotingListener(c, this,
                   new ServiceRemotingJsonSerializationProvider());
           })
       };
   }
   ```

3. Zastąp domyślny dostawcę serializacji usługą `JsonSerializationProvider` dla fabryki klienta usług zdalnych.

    ```csharp
    var proxyFactory = new ServiceProxyFactory((c) =>
    {
        return new FabricTransportServiceRemotingClientFactory(
        serializationProvider: new ServiceRemotingJsonSerializationProvider());
      });
      ```

## <a name="next-steps"></a>Następne kroki

* [Interfejs API sieci Web z OWIN w Reliable Services](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Windows Communication Foundation komunikacji z Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Bezpieczna komunikacja dla Reliable Services](service-fabric-reliable-services-secure-communication.md)
