---
title: Omówienie komunikacji Reliable Services
description: Omówienie modelu komunikacji Reliable Services, w tym otwierania detektorów na usługach, rozpoznawania punktów końcowych i komunikacji między usługami.
author: vturecek
ms.topic: conceptual
ms.date: 11/01/2017
ms.author: vturecek
ms.custom: devx-track-csharp
ms.openlocfilehash: 9b45ceaed9f0d3d64a0fc6890549542acc6b1c21
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89018641"
---
# <a name="how-to-use-the-reliable-services-communication-apis"></a>Jak używać interfejsów API komunikacji Reliable Services
Usługa Azure Service Fabric jako platforma jest całkowicie niezależny od o komunikację między usługami. Wszystkie protokoły i stosy są akceptowalne z protokołu UDP do protokołu HTTP. Aby wybrać sposób komunikacji usług, należy do deweloperów usług. Reliable Services Application Framework zawiera wbudowane stosy komunikacji oraz interfejsy API, których można używać do tworzenia niestandardowych składników komunikacji.

## <a name="set-up-service-communication"></a>Konfigurowanie komunikacji z usługą
Interfejs API Reliable Services używa prostego interfejsu do komunikacji z usługą. Aby otworzyć punkt końcowy usługi, po prostu Zaimplementuj ten interfejs:

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

```java
public interface CommunicationListener {
    CompletableFuture<String> openAsync(CancellationToken cancellationToken);

    CompletableFuture<?> closeAsync(CancellationToken cancellationToken);

    void abort();
}
```

Następnie można dodać implementację odbiornika komunikacji, zwracając ją w przesłonięciu metody klasy opartej na usłudze.

W przypadku usług bezstanowych:

```csharp
public class MyStatelessService : StatelessService
{
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        ...
    }
    ...
}
```
```java
public class MyStatelessService extends StatelessService {

    @Override
    protected List<ServiceInstanceListener> createServiceInstanceListeners() {
        ...
    }
    ...
}
```

Dla usług stanowych:

```java
    @Override
    protected List<ServiceReplicaListener> createServiceReplicaListeners() {
        ...
    }
    ...
```

```csharp
public class MyStatefulService : StatefulService
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        ...
    }
    ...
}
```

W obu przypadkach zwracasz kolekcję odbiorników. Dzięki temu usługa może nasłuchiwać wielu punktów końcowych, które mogą korzystać z różnych protokołów, przy użyciu wielu odbiorników. Na przykład może istnieć odbiornik HTTP i oddzielny odbiornik WebSocket. Każdy odbiornik Pobiera nazwę, a wynikiem zbierania wartości *name: Address* par są reprezentowane jako obiekt JSON, gdy klient żąda adresów nasłuchujących dla wystąpienia usługi lub partycji.

W usłudze bezstanowej przesłonięcie zwraca kolekcję ServiceInstanceListeners. A `ServiceInstanceListener` zawiera funkcję do utworzenia `ICommunicationListener(C#) / CommunicationListener(Java)` i nadaje mu nazwę. W przypadku usług stanowych przesłonięcie zwraca kolekcję ServiceReplicaListeners. Jest to nieco inne niż jego odpowiednik bezstanowy, ponieważ `ServiceReplicaListener` ma opcję otwarcia `ICommunicationListener` w replikach pomocniczych. Można nie tylko używać wielu odbiorników komunikacji w usłudze, ale można również określić, które odbiorniki akceptują żądania w replikach pomocniczych, a które nasłuchują tylko w replikach podstawowych.

Na przykład można mieć ServiceRemotingListener, który wykonuje wywołania RPC tylko w replikach podstawowych, i drugi odbiornik niestandardowy, który pobiera żądania odczytu w replikach pomocniczych za pośrednictwem protokołu HTTP:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(context =>
            new MyCustomHttpListener(context),
            "HTTPReadonlyEndpoint",
            true),

        new ServiceReplicaListener(context =>
            this.CreateServiceRemotingListener(context),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

> [!NOTE]
> Podczas tworzenia wielu odbiorników dla usługi każdy odbiornik **musi** mieć unikatową nazwę.
>
>

Na koniec opisz punkty końcowe, które są wymagane dla usługi w [manifeście usługi](service-fabric-application-and-service-manifests.md) , w sekcji w punktach końcowych.

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

Odbiornik komunikacji może uzyskać dostęp do zasobów punktu końcowego przyznanych do niego z `CodePackageActivationContext` w `ServiceContext` . Odbiornik może rozpocząć nasłuchiwanie żądań, gdy zostanie otwarte.

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```
```java
CodePackageActivationContext codePackageActivationContext = serviceContext.getCodePackageActivationContext();
int port = codePackageActivationContext.getEndpoint("ServiceEndpoint").getPort();

```

> [!NOTE]
> Zasoby punktu końcowego są wspólne dla całego pakietu usługi i są przydzielone przez Service Fabric, gdy pakiet usługi jest aktywowany. Wiele replik usługi hostowanych w tym samym elemencie ServiceHost może współużytkować ten sam port. Oznacza to, że odbiornik komunikacji powinien obsługiwać Udostępnianie portów. Zalecanym sposobem tego jest, aby odbiornik komunikacji używał identyfikatora partycji i repliki/identyfikatora wystąpienia podczas generowania adresu nasłuchiwania.
>
>

### <a name="service-address-registration"></a>Rejestracja adresu usługi
Usługa systemowa o nazwie *Usługa nazewnictwa* działa w klastrach Service Fabric. Usługa nazewnictwa jest rejestratorem dla usług i ich adresów, na których nasłuchuje każde wystąpienie lub replika usługi. Gdy `OpenAsync(C#) / openAsync(Java)` Metoda zostanie `ICommunicationListener(C#) / CommunicationListener(Java)` ukończona, jej wartość zwracana zostanie zarejestrowana w usługa nazewnictwa. Ta wartość zwracana, która jest publikowana w Usługa nazewnictwa jest ciągiem, którego wartość może być dowolna. Ta wartość ciągu jest wyświetlana przez klientów po poproszeniu o adres usługi z Usługa nazewnictwa.

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = string.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/",
                port);

    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

    // the string returned here will be published in the Naming Service.
    return Task.FromResult(this.publishAddress);
}
```
```java
public CompletableFuture<String> openAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.getCodePackageActivationContext.getEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.getPort();

    this.publishAddress = String.format("http://%s:%d/", FabricRuntime.getNodeContext().getIpAddressOrFQDN(), port);

    this.webApp = new WebApp(port);
    this.webApp.start();

    /* the string returned here will be published in the Naming Service.
     */
    return CompletableFuture.completedFuture(this.publishAddress);
}
```

Service Fabric udostępnia interfejsy API, które umożliwiają klientom i innym usługom następnie poproszenie o ten adres według nazwy usługi. Jest to ważne, ponieważ adres usługi nie jest statyczny. Usługi są przenoszone w klastrze w celu zrównoważenia zasobów i dostępności. Jest to mechanizm, który umożliwia klientom rozpoznawanie adresu nasłuchiwania dla usługi.

> [!NOTE]
> Aby zapoznać się z kompletnymi krokami dotyczącymi pisania odbiornika komunikacji, zobacz [Service Fabric usług interfejsu API sieci Web za pomocą samoobsługowego udostępniania](./service-fabric-reliable-services-communication-aspnetcore.md) w języku C#, a w przypadku języka Java można napisać własną implementację serwera http, zobacz przykład aplikacji EchoServer w lokalizacji https://github.com/Azure-Samples/service-fabric-java-getting-started .
>
>

## <a name="communicating-with-a-service"></a>Komunikacja z usługą
Interfejs API Reliable Services udostępnia następujące biblioteki do pisania klientów komunikujących się z usługami.

### <a name="service-endpoint-resolution"></a>Rozpoznawanie punktu końcowego usługi
Pierwszym krokiem komunikacji z usługą jest rozpoznanie adresu punktu końcowego partycji lub wystąpienia usługi, do której chcesz się skontaktować. `ServicePartitionResolver(C#) / FabricServicePartitionResolver(Java)`Klasa narzędzi jest podstawowym elementem pierwotnym, który pomaga klientom ustalić punkt końcowy usługi w czasie wykonywania. W Service Fabric terminologii proces określania punktu końcowego usługi jest określany jako *rozwiązanie punktu końcowego usługi*.

Aby połączyć się z usługami w klastrze, ServicePartitionResolver można utworzyć przy użyciu ustawień domyślnych. Jest to zalecane użycie w większości sytuacji:

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();
```

Aby połączyć się z usługami w innym klastrze, ServicePartitionResolver można utworzyć za pomocą zestawu punktów końcowych bramy klastra. Należy pamiętać, że punkty końcowe bramy są tylko różnymi punktami końcowymi do łączenia się z tym samym klastrem. Na przykład:

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

Alternatywnie, `ServicePartitionResolver` można otrzymać funkcję `FabricClient` do tworzenia do użycia wewnętrznie:

```csharp
public delegate FabricClient CreateFabricClientDelegate();
```
```java
public FabricServicePartitionResolver(CreateFabricClient createFabricClient) {
...
}

public interface CreateFabricClient {
    public FabricClient getFabricClient();
}
```

`FabricClient` jest obiektem, który jest używany do komunikowania się z klastrem Service Fabric dla różnych operacji zarządzania w klastrze. Jest to przydatne w przypadku, gdy potrzebna jest większa kontrola nad sposobem interakcji programu rozpoznawania partycji usług z klastrem. `FabricClient` wykonuje buforowanie wewnętrznie i ogólnie jest kosztowna, dlatego ważne jest, aby w miarę `FabricClient` możliwości ponownie używać wystąpień.

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver(() -> new CreateFabricClientImpl());
```

Metoda Rozwiązuj służy następnie do pobierania adresu usługi lub partycji usługi dla usług podzielonych na partycje.

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();

ResolvedServicePartition partition =
    await resolver.ResolveAsync(new Uri("fabric:/MyApp/MyService"), new ServicePartitionKey(), cancellationToken);
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();

CompletableFuture<ResolvedServicePartition> partition =
    resolver.resolveAsync(new URI("fabric:/MyApp/MyService"), new ServicePartitionKey());
```

Adres usługi można łatwo rozpoznać przy użyciu ServicePartitionResolver, ale więcej pracy jest wymagane, aby zapewnić poprawne użycie rozwiązanego adresu. Klient musi wykryć, czy próba nawiązania połączenia nie powiodła się z powodu przejściowego błędu i może zostać ponowiona (np. usługa została przeniesiona lub jest tymczasowo niedostępna) albo trwały błąd (np. usługa została usunięta lub żądany zasób już nie istnieje). Wystąpienia usługi lub repliki mogą poruszać się między węzłami i węzłami w dowolnym momencie z wielu powodów. Adres usługi rozpoznany za pomocą ServicePartitionResolver może być przestarzały przez czas, przez jaki kod klienta próbuje nawiązać połączenie. W takim przypadku klient musi ponownie rozpoznać adres. Podanie poprzedniej `ResolvedServicePartition` wskazuje, że program rozpoznawania nazw musi ponownie spróbować, zamiast po prostu pobrać buforowany adres.

Zazwyczaj kod klienta nie musi bezpośrednio współpracować z ServicePartitionResolver. Jest ona tworzona i przenoszona do fabryk klientów komunikacyjnych w interfejsie API Reliable Services. Fabryki używają wewnętrznie rozpoznawania nazw do generowania obiektu klienta, którego można użyć do komunikowania się z usługami.

### <a name="communication-clients-and-factories"></a>Klienci i fabryki komunikacyjne
Biblioteka fabryki komunikacji implementuje typowy wzorzec ponawiania obsługi błędów, który podejmuje dalsze próby połączeń w celu ułatwienia rozwiązywania punktów końcowych usługi. Biblioteka fabryki zapewnia mechanizm ponawiania prób podczas dostarczania programów obsługi błędów.

`ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)` definiuje interfejs podstawowy zaimplementowany przez fabrykę klienta komunikacji, który tworzy klientów, którzy mogą komunikować się z usługą Service Fabric. Implementacja CommunicationClientFactory zależy od stosu komunikacji używanego przez usługę Service Fabric, do której klient chce się skomunikować. Interfejs API Reliable Services zapewnia `CommunicationClientFactoryBase<TCommunicationClient>` . Zapewnia to podstawową implementację interfejsu CommunicationClientFactory oraz wykonuje zadania wspólne dla wszystkich stosów komunikacji. (Te zadania obejmują użycie ServicePartitionResolver do określenia punktu końcowego usługi). Klienci zazwyczaj implementują abstrakcyjną klasę CommunicationClientFactoryBase, aby obsługiwała logikę specyficzną dla stosu komunikacji.

Klient komunikacyjny odbiera tylko adres i używa go do nawiązywania połączenia z usługą. Klient może korzystać z dowolnego protokołu, który chce.

```csharp
public class MyCommunicationClient : ICommunicationClient
{
    public ResolvedServiceEndpoint Endpoint { get; set; }

    public string ListenerName { get; set; }

    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}
```
```java
public class MyCommunicationClient implements CommunicationClient {

    private ResolvedServicePartition resolvedServicePartition;
    private String listenerName;
    private ResolvedServiceEndpoint endPoint;

    /*
     * Getters and Setters
     */
}
```

Fabryka klienta jest przede wszystkim odpowiedzialna za tworzenie klientów komunikacyjnych. W przypadku klientów, którzy nie utrzymują połączenia trwałego, takiego jak klient HTTP, fabryka musi jedynie utworzyć i zwrócić klienta. Inne protokoły, które utrzymują trwałe połączenie, takie jak niektóre protokoły binarne, powinny być również weryfikowane przez fabrykę w celu ustalenia, czy połączenie musi zostać utworzone.  

```csharp
public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient client)
    {
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
    }
}
```
```java
public class MyCommunicationClientFactory extends CommunicationClientFactoryBase<MyCommunicationClient> {

    @Override
    protected boolean validateClient(MyCommunicationClient clientChannel) {
    }

    @Override
    protected boolean validateClient(String endpoint, MyCommunicationClient client) {
    }

    @Override
    protected CompletableFuture<MyCommunicationClient> createClientAsync(String endpoint) {
    }

    @Override
    protected void abortClient(MyCommunicationClient client) {
    }
}
```

Na koniec program obsługi wyjątków jest odpowiedzialny za określenie działania, które należy podjąć w przypadku wystąpienia wyjątku. Wyjątki są podzielone na **ponowienie** i **nie**powtarzają się.

* Wyjątki niepowtarzające się po prostu **ponownie** zgłaszają się do obiektu wywołującego.
* wyjątki **powtarzające** się są dalsze w kategorii **przejściowe** i **nieprzejściowe**.
  * **Przejściowe** wyjątki to te, które można po prostu ponowić próbę, bez konieczności ponownego rozpoznawania adresu punktu końcowego usługi. Będą to między innymi przejściowe problemy z siecią lub odpowiedzi na błędy usług inne niż te, które wskazują, że adres punktu końcowego usługi nie istnieje.
  * Wyjątki **nieprzejściowe** to te, które wymagają ponownego rozpoznania adresu punktu końcowego usługi. Należą do nich wyjątki wskazujące, że nie można osiągnąć punktu końcowego usługi, wskazując, że usługa została przeniesiona do innego węzła.

`TryHandleException`Podejmuje decyzję dotyczącą danego wyjątku. Jeśli nie **wie** , jakie decyzje należy podjąć o wyjątku, powinien zwrócić **wartość false**. Jeśli **wiadomo** , jakie decyzje należy podjąć, powinien odpowiednio ustawić wynik i zwrócić **wartość true**.

```csharp
class MyExceptionHandler : IExceptionHandler
{
    public bool TryHandleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings, out ExceptionHandlingResult result)
    {
        // if exceptionInformation.Exception is known and is transient (can be retried without re-resolving)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, true, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;


        // if exceptionInformation.Exception is known and is not transient (indicates a new service endpoint address must be resolved)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, false, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;

        // if exceptionInformation.Exception is unknown (let the next IExceptionHandler attempt to handle it)
        result = null;
        return false;
    }
}
```
```java
public class MyExceptionHandler implements ExceptionHandler {

    @Override
    public ExceptionHandlingResult handleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings) {

        /* if exceptionInformation.getException() is known and is transient (can be retried without re-resolving)
         */
        result = new ExceptionHandlingRetryResult(exceptionInformation.getException(), true, retrySettings, retrySettings.getDefaultMaxRetryCount());
        return true;


        /* if exceptionInformation.getException() is known and is not transient (indicates a new service endpoint address must be resolved)
         */
        result = new ExceptionHandlingRetryResult(exceptionInformation.getException(), false, retrySettings, retrySettings.getDefaultMaxRetryCount());
        return true;

        /* if exceptionInformation.getException() is unknown (let the next ExceptionHandler attempt to handle it)
         */
        result = null;
        return false;

    }
}
```
### <a name="putting-it-all-together"></a>Zebranie wszystkich elementów
W przypadku programu `ICommunicationClient(C#) / CommunicationClient(Java)` , `ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)` , i `IExceptionHandler(C#) / ExceptionHandler(Java)` skompilowane wokół protokołu komunikacyjnego, obiekt `ServicePartitionClient(C#) / FabricServicePartitionClient(Java)` otacza wszystkie te elementy i zapewnia pętlę rozpoznawania błędów i adresów partycji usługi wokół tych składników.

```csharp
private MyCommunicationClientFactory myCommunicationClientFactory;
private Uri myServiceUri;

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

var result = await myServicePartitionClient.InvokeWithRetryAsync(async (client) =>
   {
      // Communicate with the service using the client.
   },
   CancellationToken.None);

```
```java
private MyCommunicationClientFactory myCommunicationClientFactory;
private URI myServiceUri;

FabricServicePartitionClient myServicePartitionClient = new FabricServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

CompletableFuture<?> result = myServicePartitionClient.invokeWithRetryAsync(client -> {
      /* Communicate with the service using the client.
       */
   });

```

## <a name="next-steps"></a>Następne kroki
* [ASP.NET Core z Reliable Services](service-fabric-reliable-services-communication-aspnetcore.md)
* [Zdalne wywołania procedur z Reliable Services komunikacji zdalnej](service-fabric-reliable-services-communication-remoting.md)
* [Komunikacja WCF przy użyciu Reliable Services](service-fabric-reliable-services-communication-wcf.md)
