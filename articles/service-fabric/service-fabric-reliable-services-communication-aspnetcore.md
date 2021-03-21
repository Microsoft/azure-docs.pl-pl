---
title: Komunikacja usługi z ASP.NET Core
description: Dowiedz się, jak korzystać z ASP.NET Core w aplikacjach bezstanowych i stanowych platformy Azure Service Fabric Reliable Services.
ms.topic: conceptual
ms.date: 10/12/2018
ms.custom: devx-track-csharp
ms.openlocfilehash: a125c6a1972b51f518175a4c69248119f71ada7c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791598"
---
# <a name="aspnet-core-in-azure-service-fabric-reliable-services"></a>ASP.NET Core na platformie Azure Service Fabric Reliable Services

ASP.NET Core to platforma o postaci Open Source i dla wielu platform. Ta struktura jest przeznaczona do tworzenia aplikacji internetowych opartych na chmurze, takich jak aplikacje internetowe, aplikacje IoT i zaplecza mobilne.

Ten artykuł zawiera szczegółowy przewodnik dotyczący hostingu usług ASP.NET Core w Service Fabric Reliable Services przy użyciu elementu **Microsoft. servicefabric. AspNetCore.** Zestaw pakietów NuGet.

Aby zapoznać się z samouczkiem wprowadzającym na ASP.NET Core w Service Fabric i instrukcje dotyczące uzyskiwania konfiguracji środowiska programistycznego, zobacz [Samouczek: Tworzenie i wdrażanie aplikacji przy ASP.NET Core użyciu usługi frontonu sieci Web API i usługi stanowej](service-fabric-tutorial-create-dotnet-app.md)zaplecza.

W dalszej części tego artykułu założono, że znasz już ASP.NET Core. Jeśli nie, zapoznaj się z tematem [podstawy ASP.NET Core](/aspnet/core/fundamentals/index).

## <a name="aspnet-core-in-the-service-fabric-environment"></a>ASP.NET Core w środowisku Service Fabric

Zarówno ASP.NET Core, jak i aplikacje Service Fabric mogą działać na platformie .NET Core lub w trybie Full .NET Framework. ASP.NET Core można użyć na dwa różne sposoby w Service Fabric:
 - **Hostowane jako plik wykonywalny gościa**. Ten sposób jest używany głównie do uruchamiania istniejących aplikacji ASP.NET Core na Service Fabric bez zmian w kodzie.
 - **Uruchom wewnątrz niezawodnej usługi**. W ten sposób można zapewnić lepszą integrację z Service Fabric środowiska uruchomieniowego i zezwala na stanowe usługi ASP.NET Core.

W dalszej części tego artykułu wyjaśniono, jak używać ASP.NET Core wewnątrz niezawodnej usługi, za pomocą składników integracji ASP.NET Core dostarczanych z zestawem SDK Service Fabric.

## <a name="service-fabric-service-hosting"></a>Service Fabric hosting usług

W Service Fabric co najmniej jedno wystąpienie i/lub repliki usługi są uruchamiane w *procesie hosta usługi*: plik wykonywalny, który uruchamia kod usługi. Ty, jako autor usługi, jest to proces hosta usługi, a Service Fabric aktywuje i monitoruje go.

Tradycyjny ASP.NET (do MVC 5) jest ściśle połączony z usługami IIS przez System.Web.dll. ASP.NET Core zapewnia rozdzielenie między serwerem sieci Web a aplikacją sieci Web. Ta separacja pozwala na przenośne aplikacje sieci Web między różnymi serwerami sieci Web. Pozwala również na *samodzielne obsługiwanie* serwerów sieci Web. Oznacza to, że można uruchomić serwer sieci Web we własnym procesie, w przeciwieństwie do procesu, którego właścicielem jest dedykowane oprogramowanie serwera sieci Web, takie jak usługi IIS.

Aby połączyć usługę Service Fabric i ASP.NET jako plik wykonywalny gościa lub w niezawodnej usłudze, musisz mieć możliwość uruchomienia ASP.NET w ramach procesu hosta usługi. W tym celu ASP.NET Core samoobsługowego udostępniania.

## <a name="hosting-aspnet-core-in-a-reliable-service"></a>Hosting ASP.NET Core w niezawodnej usłudze
Najczęściej hostowane aplikacje ASP.NET Core tworzą element WebHost w punkcie wejścia aplikacji, na przykład `static void Main()` metodę w `Program.cs` . W takim przypadku cykl życia elementu WebHost jest związany z cyklem życia procesu.

![Hosting ASP.NET Core w procesie][0]

Jednak punkt wejścia aplikacji nie jest właściwym miejscem do utworzenia elementu WebHost w niezawodnej usłudze. Wynika to z faktu, że punkt wejścia aplikacji jest używany tylko do rejestrowania typu usługi w środowisku uruchomieniowym Service Fabric, dzięki czemu może tworzyć wystąpienia tego typu usługi. Element WebHost należy utworzyć w niezawodnej usłudze. W ramach procesu hosta usługi wystąpienia usługi i/lub repliki mogą przechodzić przez wiele cykli życia. 

Niezawodne wystąpienie usługi jest reprezentowane przez klasę usługi pochodną `StatelessService` lub `StatefulService` . Stos komunikacyjny usługi jest zawarty w `ICommunicationListener` implementacji w klasie usługi. `Microsoft.ServiceFabric.AspNetCore.*`Pakiety NuGet zawierają implementacje programu `ICommunicationListener` i zarządzają ASP.NET Core webhost dla Kestrel lub HTTP.sys w niezawodnej usłudze.

![Diagram służący do hostowania ASP.NET Core w niezawodnej usłudze][1]

## <a name="aspnet-core-icommunicationlisteners"></a>ASP.NET Core ICommunicationListeners
`ICommunicationListener`Implementacje dla Kestrel i HTTP.sys w `Microsoft.ServiceFabric.AspNetCore.*` pakietach NuGet mają podobne wzorce użycia. Jednak wykonują nieco inne akcje specyficzne dla każdego serwera sieci Web. 

Oba odbiorniki komunikacji zapewniają konstruktora, który przyjmuje następujące argumenty:
 - **`ServiceContext serviceContext`**: Jest to `ServiceContext` obiekt, który zawiera informacje o działającej usłudze.
 - **`string endpointName`**: Jest to nazwa `Endpoint` konfiguracji w ServiceManifest.xml. Jest to głównie miejsce, w którym dwa odbiorniki komunikacji różnią się. HTTP.sys *wymaga* `Endpoint` konfiguracji, podczas gdy nie Kestrel.
 - **`Func<string, AspNetCoreCommunicationListener, IWebHost> build`**: Jest to obiekt lambda, który jest implementowany, w którym tworzysz i zwracasz `IWebHost` . Pozwala to skonfigurować `IWebHost` sposób zwykle w aplikacji ASP.NET Core. Lambda zawiera adres URL, który jest generowany dla Ciebie, w zależności od używanej opcji integracji Service Fabric i `Endpoint` konfiguracji. Następnie można zmodyfikować ten adres URL lub użyć go do uruchomienia serwera sieci Web.

## <a name="service-fabric-integration-middleware"></a>Oprogramowanie pośredniczące Service Fabric Integration
`Microsoft.ServiceFabric.AspNetCore`Pakiet NuGet obejmuje `UseServiceFabricIntegration` metodę rozszerzenia `IWebHostBuilder` , która dodaje oprogramowanie pośredniczące Service Fabric-aware. To oprogramowanie pośredniczące konfiguruje Kestrel lub HTTP.sys, `ICommunicationListener` Aby zarejestrować unikatowy adres URL usługi za pomocą Usługa nazewnictwa Service Fabric. Następnie sprawdza poprawność żądań klientów, aby upewnić się, że klienci nawiązują połączenie z odpowiednią usługą. 

Ten krok jest niezbędny, aby uniemożliwić klientom błędne nawiązywanie połączenia z niewłaściwą usługą. Dzieje się tak, ponieważ w środowisku udostępnionego hosta, takim jak Service Fabric, wiele aplikacji sieci Web może działać na tej samej maszynie fizycznej lub wirtualnej, ale nie używać unikatowych nazw hostów. Ten scenariusz jest opisany bardziej szczegółowo w następnej sekcji.

### <a name="a-case-of-mistaken-identity"></a>Przypadek pomylony tożsamość
Repliki usług, niezależnie od protokołu, nasłuchiwanie na unikatowym adresie IP: kombinacja portu. Gdy replika usługi rozpoczęła nasłuchiwanie na adresie IP: Port punktu końcowego, Raport ten jest raportowany do Usługa nazewnictwa Service Fabric. Tam znajdują się klienci lub inne usługi. Jeśli usługi korzystają z dynamicznie przypisanych portów aplikacji, replika usługi może w nieprawidłowy sposób korzystać z tego samego adresu IP: Port punktu końcowego innej usługi na tej samej maszynie fizycznej lub wirtualnej. Może to spowodować, że klient nie nawiąże połączenia z złą usługą. Ten scenariusz może wystąpić w przypadku wystąpienia następującej sekwencji zdarzeń:

 1. Usługa nasłuchuje w dniu 10.0.0.1:30000 za pośrednictwem protokołu HTTP. 
 2. Klient rozpoznaje usługę A i Pobiera adres 10.0.0.1:30000.
 3. Usługa A przechodzi do innego węzła.
 4. Usługa B jest umieszczana w 10.0.0.1 i nie używa tego samego portu 30000.
 5. Klient próbuje nawiązać połączenie z usługą A z buforowanym adresem 10.0.0.1:30000.
 6. Klient pomyślnie nawiązał połączenie z usługą B, a nie jest on połączony z niewłaściwą usługą.

Może to spowodować błędy w losowych porach, które mogą być trudne do zdiagnozowania.

### <a name="using-unique-service-urls"></a>Korzystanie z unikatowych adresów URL usługi
Aby uniknąć tych usterek, usługi mogą opublikować punkt końcowy w Usługa nazewnictwa z unikatowym identyfikatorem, a następnie sprawdzić, czy unikatowy identyfikator w trakcie żądań klientów. Jest to wspólna akcja między usługami w zaufanym środowisku niebędącym niebezpiecznym dzierżawcą. Nie zapewnia bezpiecznego uwierzytelniania usługi w środowisku z dzierżawcą.

W zaufanym środowisku oprogramowanie pośredniczące dodawane przez `UseServiceFabricIntegration` metodę automatycznie dołącza unikatowy identyfikator do adresu opublikowanego w usługa nazewnictwa. Sprawdza ten identyfikator dla każdego żądania. Jeśli identyfikator nie jest zgodny, oprogramowanie pośredniczące natychmiast zwróci odpowiedź HTTP 410.

Usługi korzystające z dynamicznie przypisanego portu powinny korzystać z tego oprogramowania pośredniczącego.

Usługi korzystające ze stałego unikatowego portu nie mają tego problemu w środowisku wspólnym. Stały unikatowy port jest zwykle używany w przypadku usług zewnętrznych, które potrzebują dobrze znanego portu dla aplikacji klienckich, z którymi można nawiązać połączenie. Na przykład większość internetowych aplikacji sieci Web będzie używać portu 80 lub 443 dla połączeń przeglądarki sieci Web. W takim przypadku nie należy włączać unikatowego identyfikatora.

Na poniższym diagramie przedstawiono przepływ żądań z włączonym programem pośredniczącym:

![Service Fabric ASP.NET Core integrację][2]

Zarówno implementacje Kestrel, jak i HTTP.sys `ICommunicationListener` używają tego mechanizmu w taki sam sposób. Chociaż HTTP.sys mogą wewnętrznie odróżnić żądania oparte na unikatowych ścieżkach URL przy użyciu podstawowej funkcji udostępniania portów **HTTP.sys** , ta funkcja *nie* jest używana przez `ICommunicationListener` implementację HTTP.sys. Wynika to z faktu, że kody stanu błędów HTTP 503 i HTTP 404 w scenariuszu opisanym wcześniej. Dzięki temu klienci mogą określić zamiar błędu, ponieważ HTTP 503 i HTTP 404 są często używane do wskazywania innych błędów. 

W ten sposób zarówno implementacje Kestrel, jak i HTTP.sys są `ICommunicationListener` ustandaryzowane na oprogramowanie pośredniczące dostarczone przez `UseServiceFabricIntegration` metodę rozszerzenia. W związku z tym klienci muszą jedynie wykonać akcję ponownego rozpoznania punktu końcowego w odpowiedzi HTTP 410.

## <a name="httpsys-in-reliable-services"></a>HTTP.sys w Reliable Services
HTTP.sys w Reliable Services można użyć, importując pakiet NuGet **Microsoft. servicefabric. AspNetCore. HttpSys** . Ten pakiet zawiera `HttpSysCommunicationListener` implementację programu `ICommunicationListener` . `HttpSysCommunicationListener` umożliwia utworzenie ASP.NET Core WebHost wewnątrz niezawodnej usługi za pomocą HTTP.sys jako serwer sieci Web.

HTTP.sys jest oparty na [interfejsie API serwera HTTP systemu Windows](/windows/win32/http/http-api-start-page). Ten interfejs API używa **HTTP.sys** sterownika jądra do przetwarzania żądań HTTP i kierowania ich do procesów uruchamiających aplikacje sieci Web. Pozwala to na wiele procesów na tej samej maszynie fizycznej lub wirtualnej do hostowania aplikacji sieci Web na tym samym porcie, niezależnie od unikatowej ścieżki URL lub nazwy hosta. Te funkcje są przydatne w Service Fabric do hostowania wielu witryn sieci Web w tym samym klastrze.

>[!NOTE]
>Implementacja HTTP.sys działa tylko na platformie Windows.

Na poniższym diagramie przedstawiono sposób, w jaki HTTP.sys używa sterownika jądra **HTTP.sys** w systemie Windows do udostępniania portów:

![Diagram HTTP.sys][3]

### <a name="httpsys-in-a-stateless-service"></a>HTTP.sys w usłudze bezstanowej
Aby użyć `HttpSys` w usłudze bezstanowej, przesłonić `CreateServiceInstanceListeners` metodę i zwrócić `HttpSysCommunicationListener` wystąpienie:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseHttpSys()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build()))
    };
}
```

### <a name="httpsys-in-a-stateful-service"></a>HTTP.sys w usłudze stanowej

`HttpSysCommunicationListener` obecnie nie jest przeznaczona do użycia w usługach stanowych z powodu komplikacji z podstawową funkcją udostępniania portów **HTTP.sys** . Aby uzyskać więcej informacji, zobacz poniższą sekcję dotyczącą dynamicznego przydzielania portów przy użyciu HTTP.sys. W przypadku usług stanowych Kestrel jest sugerowanym serwerem sieci Web.

### <a name="endpoint-configuration"></a>Konfiguracja punktu końcowego

`Endpoint`Konfiguracja jest wymagana dla serwerów sieci Web, które używają interfejsu API serwera HTTP systemu Windows, w tym HTTP.sys. Serwery sieci Web, które używają interfejsu API serwera HTTP systemu Windows, muszą najpierw zarezerwować adresy URL za pomocą HTTP.sys (zazwyczaj jest to realizowane za pomocą narzędzia [netsh](/windows/win32/http/netsh-commands-for-http) ). 

Ta akcja wymaga podwyższonego poziomu uprawnień, których usługi nie mają domyślnie. Opcje "http" lub "https" `Protocol` właściwości `Endpoint` konfiguracji w ServiceManifest.xml są używane w specjalny sposób, aby nakazać Service Fabric środowiska uruchomieniowego w celu zarejestrowania adresu URL w Twoim imieniu w usłudze HTTP.sys. Robi to przy użyciu prefiksu [*silnego*](/windows/win32/http/urlprefix-strings) adresu URL.

Na przykład aby zarezerwować `http://+:80` dla usługi, należy użyć następującej konfiguracji w ServiceManifest.xml:

```xml
<ServiceManifest ... >
    ...
    <Resources>
        <Endpoints>
            <Endpoint Name="ServiceEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>

</ServiceManifest>
```

I nazwa punktu końcowego musi być przeniesiona do `HttpSysCommunicationListener` konstruktora:

```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     return new WebHostBuilder()
         .UseHttpSys()
         .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
         .UseUrls(url)
         .Build();
 })
```

#### <a name="use-httpsys-with-a-static-port"></a>Używanie HTTP.sys z portem statycznym
Aby użyć portu statycznego z HTTP.sys, podaj numer portu w `Endpoint` konfiguracji:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

#### <a name="use-httpsys-with-a-dynamic-port"></a>Używanie HTTP.sys z portem dynamicznym
Aby użyć dynamicznie przypisanego portu z HTTP.sys, Pomiń `Port` Właściwość w `Endpoint` konfiguracji:

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
```

Port dynamiczny przydzielony przez `Endpoint` konfigurację zapewnia tylko jeden port *na proces hosta*. Bieżący model hostingu Service Fabric umożliwia hostowanie wielu wystąpień usługi i/lub replik w tym samym procesie. Oznacza to, że każda z nich będzie współużytkować ten sam port po przydzieleniu przez `Endpoint` konfigurację. Wiele wystąpień **HTTP.sys** może współużytkować port przy użyciu podstawowej funkcji udostępniania portów **HTTP.sys** . Nie jest to jednak obsługiwane `HttpSysCommunicationListener` ze względu na komplikacje, które wprowadza dla żądań klientów. W przypadku używania portów dynamicznych Kestrel jest sugerowanym serwerem sieci Web.

## <a name="kestrel-in-reliable-services"></a>Kestrel w Reliable Services
Kestrel można użyć w Reliable Services przez zaimportowanie pakietu NuGet **Microsoft. servicefabric. AspNetCore. Kestrel** . Ten pakiet zawiera `KestrelCommunicationListener` implementację programu `ICommunicationListener` . `KestrelCommunicationListener` umożliwia utworzenie ASP.NET Core WebHost wewnątrz niezawodnej usługi za pomocą Kestrel jako serwera sieci Web.

Kestrel to Międzyplatformowy serwer sieci Web dla ASP.NET Core. W przeciwieństwie do HTTP.sys, Kestrel nie używa scentralizowanego Menedżera punktów końcowych. Również w przeciwieństwie do HTTP.sys, Kestrel nie obsługuje udostępniania portów między wieloma procesami. Każde wystąpienie elementu Kestrel musi używać unikatowego portu. Aby uzyskać więcej informacji na temat Kestrel, zobacz [szczegóły implementacji](/aspnet/core/fundamentals/servers/kestrel).

![Diagram Kestrel][4]

### <a name="kestrel-in-a-stateless-service"></a>Kestrel w usłudze bezstanowej
Aby użyć `Kestrel` w usłudze bezstanowej, przesłonić `CreateServiceInstanceListeners` metodę i zwrócić `KestrelCommunicationListener` wystąpienie:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                        services => services
                            .AddSingleton<StatelessServiceContext>(serviceContext))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

### <a name="kestrel-in-a-stateful-service"></a>Kestrel w usłudze stanowej
Aby użyć `Kestrel` w usłudze stanowej, przesłonić `CreateServiceReplicaListeners` metodę i zwrócić `KestrelCommunicationListener` wystąpienie:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new ServiceReplicaListener[]
    {
        new ServiceReplicaListener(serviceContext =>
            new KestrelCommunicationListener(serviceContext, (url, listener) =>
                new WebHostBuilder()
                    .UseKestrel()
                    .ConfigureServices(
                         services => services
                             .AddSingleton<StatefulServiceContext>(serviceContext)
                             .AddSingleton<IReliableStateManager>(this.StateManager))
                    .UseContentRoot(Directory.GetCurrentDirectory())
                    .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.UseUniqueServiceUrl)
                    .UseStartup<Startup>()
                    .UseUrls(url)
                    .Build();
            ))
    };
}
```

W tym przykładzie pojedyncze wystąpienie `IReliableStateManager` jest dostarczane do kontenera iniekcji zależności webhost. Nie jest to bezwzględnie konieczne, ale umożliwia korzystanie z `IReliableStateManager` i niezawodne kolekcje w metodach akcji kontrolera MVC.

`Endpoint` *Nie* podano nazwy konfiguracji `KestrelCommunicationListener` w usłudze stanowej. Opisano to szczegółowo w poniższej sekcji.

### <a name="configure-kestrel-to-use-https"></a>Konfigurowanie usługi Kestrel do używania protokołu HTTPS
W przypadku włączania protokołu HTTPS z Kestrel w usłudze należy określić kilka opcji nasłuchiwania. Zaktualizuj `ServiceInstanceListener` program, aby używał punktu końcowego *EndpointHttps* i nasłuchiwanie na określonym porcie (na przykład port 443). Podczas konfigurowania hosta sieci Web do korzystania z serwera sieci Web Kestrel należy skonfigurować Kestrel do nasłuchiwania adresów IPv6 na wszystkich interfejsach sieciowych: 

```csharp
new ServiceInstanceListener(
serviceContext =>
    new KestrelCommunicationListener(
        serviceContext,
        "EndpointHttps",
        (url, listener) =>
        {
            ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

            return new WebHostBuilder()
                .UseKestrel(opt =>
                {
                    int port = serviceContext.CodePackageActivationContext.GetEndpoint("EndpointHttps").Port;
                    opt.Listen(IPAddress.IPv6Any, port, listenOptions =>
                    {
                        listenOptions.UseHttps(GetCertificateFromStore());
                        listenOptions.NoDelay = true;
                    });
                })
                .ConfigureAppConfiguration((builderContext, config) =>
                {
                    config.AddJsonFile("appsettings.json", optional: false, reloadOnChange: true);
                })

                .ConfigureServices(
                    services => services
                        .AddSingleton<HttpClient>(new HttpClient())
                        .AddSingleton<FabricClient>(new FabricClient())
                        .AddSingleton<StatelessServiceContext>(serviceContext))
                .UseContentRoot(Directory.GetCurrentDirectory())
                .UseStartup<Startup>()
                .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                .UseUrls(url)
                .Build();
        }))
```

Aby zapoznać się z pełnym przykładem w samouczku, zobacz [Konfigurowanie Kestrel do używania protokołu HTTPS](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#configure-kestrel-to-use-https).


### <a name="endpoint-configuration"></a>Konfiguracja punktu końcowego
`Endpoint`Konfiguracja nie jest wymagana do korzystania z Kestrel. 

Kestrel to prosty autonomiczny serwer sieci Web. W przeciwieństwie do HTTP.sys (lub odbiornika HttpListener) nie wymaga `Endpoint` konfiguracji w ServiceManifest.xml, ponieważ nie wymaga rejestracji adresów URL przed rozpoczęciem. 

#### <a name="use-kestrel-with-a-static-port"></a>Używanie Kestrel z portem statycznym
Można skonfigurować port statyczny w `Endpoint` konfiguracji ServiceManifest.xml do użycia z Kestrel. Chociaż nie jest to bezwzględnie konieczne, oferuje dwie potencjalne korzyści:
 - Jeśli port nie jest objęty zakresem portów aplikacji, zostanie otwarty za pomocą zapory systemu operacyjnego przez Service Fabric.
 - Ten port będzie używany przez adres URL podany przez użytkownika `KestrelCommunicationListener` .

```xml
  <Resources>
    <Endpoints>
      <Endpoint Protocol="http" Name="ServiceEndpoint" Port="80" />
    </Endpoints>
  </Resources>
```

Jeśli `Endpoint` skonfigurowano, jego nazwę należy przesłać do `KestrelCommunicationListener` konstruktora: 

```csharp
new KestrelCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) => ...
```

Jeśli ServiceManifest.xml nie używa `Endpoint` konfiguracji, Pomiń nazwę w `KestrelCommunicationListener` konstruktorze. W takim przypadku zostanie użyty port dynamiczny. Aby uzyskać więcej informacji na ten temat, zobacz następną sekcję.

#### <a name="use-kestrel-with-a-dynamic-port"></a>Używanie Kestrel z portem dynamicznym
Kestrel nie może użyć automatycznego przypisania portu z `Endpoint` konfiguracji w ServiceManifest.xml. Jest to spowodowane tym, że automatyczne przypisywanie portów z `Endpoint` konfiguracji przypisuje unikatowy port na *proces hosta*, a pojedynczy proces hosta może zawierać wiele wystąpień Kestrel. To nie działa z Kestrel, ponieważ nie obsługuje udostępniania portów. W związku z tym każde wystąpienie Kestrel musi być otwarte na unikatowym porcie.

Aby użyć dynamicznego przypisywania portów z Kestrel, Pomiń `Endpoint` konfigurację w ServiceManifest.xml całkowicie i nie przekazuj nazwy punktu końcowego do `KestrelCommunicationListener` konstruktora w następujący sposób:

```csharp
new KestrelCommunicationListener(serviceContext, (url, listener) => ...
```

W tej konfiguracji program `KestrelCommunicationListener` automatycznie wybierze nieużywany port z zakresu portów aplikacji.

W przypadku protokołu HTTPS powinien mieć punkt końcowy skonfigurowany przy użyciu protokołu HTTPS bez portu określonego w ServiceManifest.xml i przekazać nazwę punktu końcowego do konstruktora KestrelCommunicationListener.


## <a name="service-fabric-configuration-provider"></a>Dostawca konfiguracji Service Fabric
Konfiguracja aplikacji w ASP.NET Core jest oparta na parach klucz-wartość ustanowionych przez dostawcę konfiguracji. Zapoznaj się z [konfiguracją w ASP.NET Core](/aspnet/core/fundamentals/configuration/) , aby dowiedzieć się więcej na temat ogólnej obsługi konfiguracji ASP.NET Core.

W tej sekcji opisano, w jaki sposób dostawca konfiguracji Service Fabric integruje się z konfiguracją ASP.NET Core, importując `Microsoft.ServiceFabric.AspNetCore.Configuration` pakiet NuGet.

### <a name="addservicefabricconfiguration-startup-extensions"></a>AddServiceFabricConfiguration rozszerzenia uruchomieniowe
Po zaimportowaniu `Microsoft.ServiceFabric.AspNetCore.Configuration` pakietu NuGet należy zarejestrować Źródło konfiguracji Service Fabric za pomocą interfejsu API konfiguracji ASP.NET Core. W tym celu należy sprawdzić rozszerzenia **AddServiceFabricConfiguration** w `Microsoft.ServiceFabric.AspNetCore.Configuration` przestrzeni nazw względem `IConfigurationBuilder` .

```csharp
using Microsoft.ServiceFabric.AspNetCore.Configuration;

public Startup(IHostingEnvironment env)
{
    var builder = new ConfigurationBuilder()
        .SetBasePath(env.ContentRootPath)
        .AddJsonFile("appsettings.json", optional: false, reloadOnChange: true)
        .AddJsonFile($"appsettings.{env.EnvironmentName}.json", optional: true)
        .AddServiceFabricConfiguration() // Add Service Fabric configuration settings.
        .AddEnvironmentVariables();
    Configuration = builder.Build();
}

public IConfigurationRoot Configuration { get; }
```

Teraz usługa ASP.NET Core może uzyskać dostęp do ustawień konfiguracji Service Fabric, podobnie jak w przypadku innych ustawień aplikacji. Można na przykład użyć wzorca opcji do załadowania ustawień do obiektów o jednoznacznie określonym typie.

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.Configure<MyOptions>(Configuration);  // Strongly typed configuration object.
    services.AddMvc();
}
```
### <a name="default-key-mapping"></a>Domyślne mapowanie kluczy
Domyślnie dostawca konfiguracji Service Fabric obejmuje nazwę pakietu, nazwę sekcji i nazwę właściwości. Razem z tymi formularzami ASP.NET Core klucz konfiguracji:
```csharp
$"{this.PackageName}{ConfigurationPath.KeyDelimiter}{section.Name}{ConfigurationPath.KeyDelimiter}{property.Name}"
```

Na przykład jeśli masz pakiet konfiguracyjny o nazwie `MyConfigPackage` z następującą zawartością, wartość konfiguracji będzie dostępna dla ASP.NET Core `IConfiguration` za pomocą *MyConfigPackage: MyConfigSection: Parameter*.
```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">  
  <Section Name="MyConfigSection">
    <Parameter Name="MyParameter" Value="Value1" />
  </Section>  
</Settings>
```
### <a name="service-fabric-configuration-options"></a>Opcje konfiguracji Service Fabric
Dostawca konfiguracji Service Fabric obsługuje także `ServiceFabricConfigurationOptions` zmianę domyślnego zachowania mapowania kluczy.

#### <a name="encrypted-settings"></a>Ustawienia zaszyfrowane
Service Fabric obsługuje zaszyfrowane ustawienia, tak jak dostawca konfiguracji Service Fabric. Szyfrowane ustawienia nie są domyślnie odszyfrowywane do ASP.NET Core `IConfiguration` . W zamian zaszyfrowane wartości są przechowywane w tym miejscu. Jeśli jednak chcesz odszyfrować wartość do magazynu w ASP.NET Core IConfiguration, możesz ustawić flagę *DecryptValue* na false w `AddServiceFabricConfiguration` rozszerzeniu w następujący sposób:

```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        .AddServiceFabricConfiguration(activationContext, (options) => options.DecryptValue = false); // set flag to decrypt the value
    Configuration = builder.Build();
}
```
#### <a name="multiple-configuration-packages"></a>Wiele pakietów konfiguracyjnych
Service Fabric obsługuje wiele pakietów konfiguracyjnych. Domyślnie nazwa pakietu jest uwzględniona w kluczu konfiguracji. Ale flagę można ustawić `IncludePackageName` na wartość false w następujący sposób:
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    var builder = new ConfigurationBuilder()        
        // exclude package name from key.
        .AddServiceFabricConfiguration(activationContext, (options) => options.IncludePackageName = false); 
    Configuration = builder.Build();
}
```
#### <a name="custom-key-mapping-value-extraction-and-data-population"></a>Niestandardowe mapowanie kluczy, wyodrębnianie wartości i wypełnianie danych
Dostawca konfiguracji Service Fabric obsługuje również bardziej zaawansowane scenariusze umożliwiające dostosowanie mapowania kluczy przy użyciu programu `ExtractKeyFunc` i wyodrębnienie wartości przy użyciu opcji `ExtractValueFunc` . Można nawet zmienić cały proces wypełniania danych z konfiguracji Service Fabric, aby ASP.NET Core konfigurację przy użyciu programu `ConfigAction` .

Poniższe przykłady ilustrują sposób korzystania `ConfigAction` z programu w celu dostosowania populacji danych:
```csharp
public Startup()
{
    ICodePackageActivationContext activationContext = FabricRuntime.GetActivationContext();
    
    this.valueCount = 0;
    this.sectionCount = 0;
    var builder = new ConfigurationBuilder();
    builder.AddServiceFabricConfiguration(activationContext, (options) =>
        {
            options.ConfigAction = (package, configData) =>
            {
                ILogger logger = new ConsoleLogger("Test", null, false);
                logger.LogInformation($"Config Update for package {package.Path} started");

                foreach (var section in package.Settings.Sections)
                {
                    this.sectionCount++;

                    foreach (var param in section.Parameters)
                    {
                        configData[options.ExtractKeyFunc(section, param)] = options.ExtractValueFunc(section, param);
                        this.valueCount++;
                    }
                }

                logger.LogInformation($"Config Update for package {package.Path} finished");
            };
        });
  Configuration = builder.Build();
}
```

### <a name="configuration-updates"></a>Aktualizacje konfiguracji
Dostawca konfiguracji Service Fabric obsługuje również aktualizacje konfiguracji. Za pomocą ASP.NET Core można `IOptionsMonitor` odbierać powiadomienia o zmianach, a następnie używać `IOptionsSnapshot` go do ponownego ładowania danych konfiguracyjnych. Aby uzyskać więcej informacji, zobacz [opcje ASP.NET Core](/aspnet/core/fundamentals/configuration/options).

Te opcje są domyślnie obsługiwane. W celu włączenia aktualizacji konfiguracji nie jest konieczne dalsze kodowanie.

## <a name="scenarios-and-configurations"></a>Scenariusze i konfiguracje
Ta sekcja zawiera kombinację opcji serwer sieci Web, konfiguracja portów, Service Fabric opcje integracji i różne ustawienia, które zalecamy, aby rozwiązać problemy z następującymi scenariuszami:
 - Narażone na zewnątrz ASP.NET Core usługi bezstanowe
 - Tylko wewnętrzne ASP.NET Core usługi bezstanowe
 - Tylko wewnętrzne ASP.NET Core usługi stanowe

**Zewnętrznie uwidoczniona usługa** to ta, która udostępnia punkt końcowy, który jest wywoływany z spoza klastra, zazwyczaj za pośrednictwem modułu równoważenia obciążenia.

Usługa **tylko wewnętrzna** jest tą, której punkt końcowy jest wywoływany tylko z poziomu klastra.

> [!NOTE]
> Punkty końcowe usługi stanowej zazwyczaj nie powinny być ujawnione w Internecie. Klastry z modułami równoważenia obciążenia, które nie znają rozwiązań usługi Service Fabric, takich jak Azure Load Balancer, nie będą mogły ujawniać usług stanowych. Wynika to z faktu, że moduł równoważenia obciążenia nie będzie w stanie zlokalizować i skierować ruchu do odpowiedniej repliki stanowej usługi. 

### <a name="externally-exposed-aspnet-core-stateless-services"></a>Narażone na zewnątrz ASP.NET Core usługi bezstanowe
Kestrel to sugerowany serwer sieci Web dla usług frontonu, które uwidaczniają zewnętrzne, dostępne w Internecie punkty końcowe HTTP. W systemie Windows HTTP.sys może zapewnić możliwość udostępniania portów, która umożliwia hostowanie wielu usług sieci Web w tym samym zestawie węzłów przy użyciu tego samego portu. W tym scenariuszu usługi sieci Web są rozróżniane przez nazwę hosta lub ścieżkę, bez polegania na serwerze proxy frontonu lub bramie do udostępniania routingu HTTP.
 
W przypadku udostępnienia Internetu usługa bezstanowa powinna używać dobrze znanego i stabilnego punktu końcowego, który jest dostępny za pomocą modułu równoważenia obciążenia. Ten adres URL należy podać użytkownikom aplikacji. Zalecamy wykonanie następującej konfiguracji:

| Typ | Zalecenie | Uwagi |
| ---- | -------------- | ----- |
| Serwer sieci Web | Kestrel | Kestrel to preferowany serwer sieci Web, który jest obsługiwany w systemach Windows i Linux. |
| Konfiguracja portu | static | Dobrze znany port statyczny powinien zostać skonfigurowany w `Endpoints` konfiguracji ServiceManifest.xml, na przykład 80 dla http lub 443 dla protokołu HTTPS. |
| ServiceFabricIntegrationOptions | Brak | Użyj `ServiceFabricIntegrationOptions.None` opcji podczas konfigurowania oprogramowania pośredniczącego integracji Service Fabric, aby usługa nie podjęła próby zweryfikowania żądań przychodzących dla unikatowego identyfikatora. Użytkownicy zewnętrzni Twojej aplikacji nie będą znać unikatowych informacji identyfikacyjnych używanych przez oprogramowanie pośredniczące. |
| Liczba wystąpień | -1 | W typowych przypadkach użycia ustawienie liczba wystąpień powinna mieć wartość *-1*. Dzieje się tak, aby wystąpienie było dostępne we wszystkich węzłach, które odbierają ruch z modułu równoważenia obciążenia. |

Jeśli wiele usług narażonych na zewnątrz współużytkują ten sam zestaw węzłów, można użyć HTTP.sys z unikatowym, ale stabilną ścieżką URL. Można to osiągnąć przez zmodyfikowanie adresu URL podanego podczas konfigurowania IWebHost. Należy zauważyć, że dotyczy to tylko HTTP.sys.

 ```csharp
 new HttpSysCommunicationListener(serviceContext, "ServiceEndpoint", (url, listener) =>
 {
     url += "/MyUniqueServicePath";
 
     return new WebHostBuilder()
         .UseHttpSys()
         ...
         .UseUrls(url)
         .Build();
 })
 ```

### <a name="internal-only-stateless-aspnet-core-service"></a>Usługa bezstanowa ASP.NET Core tylko w ramach wewnętrznego
W przypadku usług bezstanowych, które są wywoływane tylko z klastra, należy używać unikatowych adresów URL i dynamicznie przypisywanych portów w celu zapewnienia współpracy między wieloma usługami. Zalecamy wykonanie następującej konfiguracji:

| Typ | Zalecenie | Uwagi |
| ---- | -------------- | ----- |
| Serwer sieci Web | Kestrel | Chociaż można używać HTTP.sys dla wewnętrznych usług bezstanowych, Kestrel jest najlepszym serwerem, aby umożliwić wielu wystąpieniem usługi Udostępnianie hosta.  |
| Konfiguracja portu | przypisane dynamicznie | Wielokrotne repliki usługi stanowej mogą współużytkować proces hosta lub system operacyjny hosta i w ten sposób potrzebować unikatowych portów. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | W przypadku dynamicznego przypisywania portów to ustawienie uniemożliwia opisywany wcześniej problem dotyczący tożsamości. |
| InstanceCount | dowolny | Ustawienie liczby wystąpień można ustawić na dowolną wartość potrzebną do obsługi usługi. |

### <a name="internal-only-stateful-aspnet-core-service"></a>Usługa bezstanowa ASP.NET Core tylko wewnętrznie
Usługi stanowe, które są wywoływane tylko z poziomu klastra, powinny używać dynamicznie przydzielonych portów w celu zapewnienia współpracy między wieloma usługami. Zalecamy wykonanie następującej konfiguracji:

| Typ | Zalecenie | Uwagi |
| ---- | -------------- | ----- |
| Serwer sieci Web | Kestrel | `HttpSysCommunicationListener`Nie jest przeznaczony do użycia przez usługi stanowe, w których repliki współużytkują proces hosta. |
| Konfiguracja portu | przypisane dynamicznie | Wielokrotne repliki usługi stanowej mogą współużytkować proces hosta lub system operacyjny hosta i w ten sposób potrzebować unikatowych portów. |
| ServiceFabricIntegrationOptions | UseUniqueServiceUrl | W przypadku dynamicznego przypisywania portów to ustawienie uniemożliwia opisywany wcześniej problem dotyczący tożsamości. |

## <a name="next-steps"></a>Następne kroki
[Debug your Service Fabric application by using Visual Studio (Debugowanie aplikacji usługi Service Fabric przy użyciu programu Visual Studio)](service-fabric-debugging-your-application.md)


<!--Image references-->
[0]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-standalone.png
[1]:./media/service-fabric-reliable-services-communication-aspnetcore/webhost-servicefabric.png
[2]:./media/service-fabric-reliable-services-communication-aspnetcore/integration.png
[3]:./media/service-fabric-reliable-services-communication-aspnetcore/httpsys.png
[4]:./media/service-fabric-reliable-services-communication-aspnetcore/kestrel.png
