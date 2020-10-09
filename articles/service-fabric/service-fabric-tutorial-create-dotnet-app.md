---
title: Tworzenie aplikacji .NET na Service Fabric na platformie Azure
description: W tym samouczku znajdziesz informacje o sposobie tworzenia aplikacji za pomocą frontonu platformy ASP.NET Core i niezawodnej stanowej usługi zaplecza, a także o sposobie wdrażania aplikacji w klastrze.
ms.topic: tutorial
ms.date: 07/10/2019
ms.custom: mvc, devx-track-js, devx-track-csharp
ms.openlocfilehash: e9014f2f5e4d0149d1b0c37c9dbf007358242189
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91326375"
---
# <a name="tutorial-create-and-deploy-an-application-with-an-aspnet-core-web-api-front-end-service-and-a-stateful-back-end-service"></a>Samouczek: tworzenie i wdrażanie aplikacji przy użyciu usługi frontonu platformy ASP.NET Core z internetowym interfejsem API oraz stanowej usługi zaplecza

Niniejszy samouczek jest pierwszą częścią serii.  Zostanie tutaj przedstawiony sposób tworzenia aplikacji usługi Azure Service Fabric za pomocą frontonu internetowego interfejsu API platformy ASP.NET Core i stanowej usługi zaplecza umożliwiającej przechowywanie danych. Po zakończeniu będziesz mieć aplikację do głosowania z usługą internetową frontonu ASP.NET Core, która zapisuje wyniki głosowania w stanowej usłudze zaplecza w klastrze. Ta seria samouczków wymaga komputera dewelopera systemu Windows. Jeśli nie chcesz ręcznie tworzyć aplikacji do głosowania, możesz [pobrać kod źródłowy](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) gotowej aplikacji i przejść od razu do sekcji [Szczegółowe omówienie przykładowej aplikacji do głosowania](#walkthrough_anchor).  Jeśli wolisz, możesz też obejrzeć [przewodnik wideo](https://channel9.msdn.com/Events/Connect/2017/E100) dla tego samouczka.

![AngularJS + ASP. NET API frontonu, nawiązywanie połączenia z usługą stanowej zaplecza na Service Fabric](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

Część pierwsza serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie usługi internetowego interfejsu API platformy ASP.NET Core jako niezawodnej stanowej usługi
> * Tworzenie usługi aplikacji internetowej platformy ASP.NET Core jako bezstanowej usługi internetowej
> * Używanie zwrotnego serwera proxy do komunikacji z usługą stanową

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Tworzenie aplikacji platformy .NET w usłudze Service Fabric
> * [Wdrażanie aplikacji w klastrze zdalnym](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Dodawanie punktu końcowego protokołu HTTPS do usługi frontonu platformy ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [Konfigurowanie ciągłej integracji/ciągłego wdrażania za pomocą usługi Azure Pipelines](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Konfigurowanie monitorowania i diagnostyki dla aplikacji](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:
* Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Zainstaluj program Visual Studio 2019](https://www.visualstudio.com/) w wersji 15,5 lub nowszej przy użyciu obciążeń deweloperskich i **ASP.NET** na **platformie Azure** .
* [Instalowanie zestawu SDK usługi Service Fabric](service-fabric-get-started.md)

## <a name="create-an-aspnet-web-api-service-as-a-reliable-service"></a>Tworzenie usługi internetowego interfejsu API platformy ASP.NET jako niezawodnej usługi

Najpierw utwórz fronton internetowy aplikacji do głosowania za pomocą platformy ASP.NET Core. ASP.NET Core to uproszczone międzyplatformowe środowisko programistyczne, którego można używać do tworzenia nowoczesnych internetowych interfejsów użytkownika i interfejsów API. Aby w pełni zrozumieć, w jaki sposób platforma ASP.NET Core integruje się z usługą Service Fabric, zdecydowanie zalecamy zapoznanie się z artykułem [ASP.NET Core in Service Fabric Reliable Services](service-fabric-reliable-services-communication-aspnetcore.md) (Platforma ASP.NET Core w niezawodnych usługach Service Fabric). Na tym etapie wystarczy wykonać czynności opisane w tym samouczku, aby szybko rozpocząć pracę. Aby uzyskać więcej informacji na temat platformy ASP.NET Core, przejrzyj [dokumentację platformy ASP.NET Core](/aspnet/core/).

1. Uruchom program Visual Studio jako **administrator**.

2. Utwórz projekt z **plikiem** -> **Nowy** -> **projekt**.

3. W oknie dialogowym **Nowy projekt** wybierz kolejno pozycje **Chmura > Aplikacja usługi Service Fabric**.

4. Nazwij aplikację **Voting** i kliknij przycisk **OK**.

   ![Okno dialogowe nowego projektu w programie Visual Studio](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog.png)

5. Na stronie **Nowa usługa Service Fabric** wybierz pozycję **Bezstanowa usługa programu ASP.NET Core**, nadaj swojej usłudze nazwę **VotingWeb**, a następnie kliknij przycisk **OK**.
   
   ![Wybieranie usługi internetowej programu ASP.NET w oknie dialogowym nowej usługi](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog-2.png) 

6. Na następnej stronie znajduje się zestaw szablonów projektów programu ASP.NET Core. Na potrzeby tego samouczka wybierz pozycję **Aplikacja internetowa (Model-View-Controller)**, a następnie kliknij przycisk **OK**.
   
   ![Wybieranie typu projektu programu ASP.NET](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog.png)

   Program Visual Studio utworzy projekt aplikacji i projekt usługi, a następnie wyświetli je w Eksploratorze rozwiązań.

   ![Eksplorator rozwiązań po utworzeniu aplikacji z usługą internetowego interfejsu API platformy ASP.NET Core]( ./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="update-the-sitejs-file"></a>Aktualizowanie pliku site.js
Otwórz plik **wwwroot/js/site.js**.  Zastąp jego zawartość poniższym kodem JavaScript używanym przez widoki z folderu Home, a następnie zapisz zmiany.

```javascript
var app = angular.module('VotingApp', ['ui.bootstrap']);
app.run(function () { });

app.controller('VotingAppController', ['$rootScope', '$scope', '$http', '$timeout', function ($rootScope, $scope, $http, $timeout) {

    $scope.refresh = function () {
        $http.get('api/Votes?c=' + new Date().getTime())
            .then(function (data, status) {
                $scope.votes = data;
            }, function (data, status) {
                $scope.votes = undefined;
            });
    };

    $scope.remove = function (item) {
        $http.delete('api/Votes/' + item)
            .then(function (data, status) {
                $scope.refresh();
            })
    };

    $scope.add = function (item) {
        var fd = new FormData();
        fd.append('item', item);
        $http.put('api/Votes/' + item, fd, {
            transformRequest: angular.identity,
            headers: { 'Content-Type': undefined }
        })
            .then(function (data, status) {
                $scope.refresh();
                $scope.item = undefined;
            })
    };
}]);
```

### <a name="update-the-indexcshtml-file"></a>Aktualizowanie pliku Index.cshtml

Otwórz plik **Views/Home/Index.cshtml** dotyczący kontrolera głównego.  Zastąp zawartość tego pliku następującym kodem i zapisz zmiany.

```html
@{
    ViewData["Title"] = "Service Fabric Voting Sample";
}

<div ng-controller="VotingAppController" ng-init="refresh()">
    <div class="container-fluid">
        <div class="row">
            <div class="col-xs-8 col-xs-offset-2 text-center">
                <h2>Service Fabric Voting Sample</h2>
            </div>
        </div>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <form class="col-xs-12 center-block">
                    <div class="col-xs-6 form-group">
                        <input id="txtAdd" type="text" class="form-control" placeholder="Add voting option" ng-model="item"/>
                    </div>
                    <button id="btnAdd" class="btn btn-default" ng-click="add(item)">
                        <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                        Add
                    </button>
                </form>
            </div>
        </div>

        <hr/>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <div class="row">
                    <div class="col-xs-4">
                        Click to vote
                    </div>
                </div>
                <div class="row top-buffer" ng-repeat="vote in votes.data">
                    <div class="col-xs-8">
                        <button class="btn btn-success text-left btn-block" ng-click="add(vote.key)">
                            <span class="pull-left">
                                {{vote.key}}
                            </span>
                            <span class="badge pull-right">
                                {{vote.value}} Votes
                            </span>
                        </button>
                    </div>
                    <div class="col-xs-4">
                        <button class="btn btn-danger pull-right btn-block" ng-click="remove(vote.key)">
                            <span class="glyphicon glyphicon-remove" aria-hidden="true"></span>
                            Remove
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
```

### <a name="update-the-_layoutcshtml-file"></a>Aktualizowanie pliku _Layout.cshtml

Otwórz plik **Views/Shared/_Layout.cshtml**, który jest widokiem domyślnym aplikacji ASP.NET.  Zastąp zawartość tego pliku następującym kodem i zapisz zmiany.


```html
<!DOCTYPE html>
<html ng-app="VotingApp" xmlns:ng="https://angularjs.org">
<head>
    <meta charset="utf-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>@ViewData["Title"]</title>

    <link href="~/lib/bootstrap/dist/css/bootstrap.css" rel="stylesheet"/>
    <link href="~/css/site.css" rel="stylesheet"/>

</head>
<body>
<div class="container body-content">
    @RenderBody()
</div>

<script src="~/lib/jquery/dist/jquery.js"></script>
<script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.7.2/angular.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/angular-ui-bootstrap/2.5.0/ui-bootstrap-tpls.js"></script>
<script src="~/js/site.js"></script>

@RenderSection("Scripts", required: false)
</body>
</html>
```

### <a name="update-the-votingwebcs-file"></a>Aktualizowanie pliku VotingWeb.cs

Otwórz plik *VotingWeb.cs*, który tworzy obiekt WebHost platformy ASP.NET Core wewnątrz usługi bezstanowej przy użyciu serwera internetowego WebListener.

Na początku tego pliku dodaj dyrektywę `using System.Net.Http;`.

Zastąp funkcję `CreateServiceInstanceListeners()` następującym kodem i zapisz zmiany.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(
            serviceContext =>
                new KestrelCommunicationListener(
                    serviceContext,
                    "ServiceEndpoint",
                    (url, listener) =>
                    {
                        ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

                        return new WebHostBuilder()
                            .UseKestrel()
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
    };
}
```

Dodaj również następującą metodę `GetVotingDataServiceName` poniżej `CreateServiceInstanceListeners()`, a następnie zapisz zmiany. Metoda `GetVotingDataServiceName` zwraca nazwę usługi w przypadku sondowania.

```csharp
internal static Uri GetVotingDataServiceName(ServiceContext context)
{
    return new Uri($"{context.CodePackageActivationContext.ApplicationName}/VotingData");
}
```

### <a name="add-the-votescontrollercs-file"></a>Dodawanie pliku VotesController.cs

Dodaj kontroler, który definiuje akcje głosowania. Kliknij prawym przyciskiem myszy folder **Controllers**, a następnie wybierz kolejno pozycje **Dodaj->Nowy element->Visual C#->ASP.NET Core->Klasa**. Nadaj plikowi nazwę **VotesController.cs** i kliknij pozycję **Dodaj**.  

Zastąp zawartość tego pliku *VotesController.cs* następującym kodem i zapisz zmiany.  Dalej, w sekcji [Aktualizowanie pliku VotesController.cs](#updatevotecontroller_anchor), ten plik zostanie zmodyfikowany na potrzeby odczytywania danych głosowania z usługi zaplecza i ich zapisywania.  Na razie ten kontroler zwraca do widoku dane w postaci statycznego ciągu.

```csharp
namespace VotingWeb.Controllers
{
    using System;
    using System.Collections.Generic;
    using System.Fabric;
    using System.Fabric.Query;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Newtonsoft.Json;

    [Produces("application/json")]
    [Route("api/Votes")]
    public class VotesController : Controller
    {
        private readonly HttpClient httpClient;

        public VotesController(HttpClient httpClient)
        {
            this.httpClient = httpClient;
        }

        // GET: api/Votes
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            List<KeyValuePair<string, int>> votes= new List<KeyValuePair<string, int>>();
            votes.Add(new KeyValuePair<string, int>("Pizza", 3));
            votes.Add(new KeyValuePair<string, int>("Ice cream", 4));

            return Json(votes);
        }
     }
}
```

### <a name="configure-the-listening-port"></a>Konfigurowanie portu nasłuchującego

Po utworzeniu usługi frontonu VotingWeb program Visual Studio losowo wybiera port, na którym usługa będzie nasłuchiwała.  Usługa VotingWeb działa jako fronton dla tej aplikacji i akceptuje ruch zewnętrzny, dlatego warto powiązać ją z ustalonym i dobrze znanym portem.  [Manifest usługi](service-fabric-application-and-service-manifests.md) deklaruje punkty końcowe usługi.

W Eksplorator rozwiązań Otwórz  *VotingWeb/PackageRoot/ServiceManifest.xml*.  W sekcji **Resources** znajdź element **Endpoint** i zmień wartość elementu **Port** na **8080**. Aby wdrożyć i uruchomić aplikację lokalnie, port nasłuchujący aplikacji musi być otwarty i dostępny na komputerze.

```xml
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
    </Endpoints>
  </Resources>
```

Zaktualizuj również właściwość Adres URL aplikacji w projekcie Voting, aby w przeglądarce internetowej został otwarty właściwy port podczas debugowania aplikacji.  W Eksploratorze rozwiązań wybierz projekt **Voting** i zaktualizuj właściwość **Adres URL aplikacji** na wartość **8080**.

### <a name="deploy-and-run-the-voting-application-locally"></a>Wdrażanie i uruchamianie aplikacji Voting lokalnie
Teraz możesz przystąpić do uruchomienia aplikacji Voting na potrzeby debugowania. W programie Visual Studio naciśnij klawisz **F5**, aby wdrożyć aplikację do lokalnego klastra usługi Service Fabric w trybie debugowania. Aplikacja zakończy działanie niepowodzeniem, jeśli program Visual Studio nie został wcześniej otwarty za pomocą konta z uprawnieniami **administratora**.

> [!NOTE]
> Przy pierwszym uruchamianiu i wdrażaniu aplikacji w środowisku lokalnym program Visual Studio tworzy lokalny klaster usługi Service Fabric na potrzeby debugowania.  Tworzenie klastra może trochę potrwać. Stan tworzenia klastra jest wyświetlany w oknie danych wyjściowych programu Visual Studio.

Po wdrożeniu aplikacji Voting do lokalnego klastra usługi Service Fabric aplikacja internetowa zostanie automatycznie otwarta na karcie przeglądarki. Powinna wyglądać następująco:

![Fronton platformy ASP.NET Core](./media/service-fabric-tutorial-create-dotnet-app/debug-front-end.png)

Aby zatrzymać debugowanie aplikacji, wróć do programu Visual Studio i naciśnij klawisze **Shift+F5**.

## <a name="add-a-stateful-back-end-service-to-your-application"></a>Dodawanie stanowej usługi zaplecza do aplikacji

Teraz, gdy usługa internetowego interfejsu API platformy ASP.NET działa w aplikacji, przejdź dalej i dodaj niezawodną usługę stanową na potrzeby przechowywania danych w aplikacji.

Usługa Service Fabric umożliwia spójne i niezawodne przechowywanie danych bezpośrednio w usłudze przy użyciu niezawodnych kolekcji. Niezawodne kolekcje to zestaw niezawodnych klas kolekcji wysokiej dostępności, które są znane wszystkim, którzy korzystali z kolekcji języka C#.

W tym samouczku utworzysz usługę, która w niezawodnej kolekcji przechowuje wartość licznika.

1. W Eksploratorze rozwiązań kliknij prawym przyciskiem myszy węzeł **Usługi** w projekcie aplikacji Voting i wybierz pozycję **Dodaj -> Nowa usługa Service Fabric...**.
    
2. W oknie dialogowym **Nowa usługa Service Fabric** wybierz usługę **Stateful ASP.NET Core**, nadaj jej nazwę **VotingData** i naciśnij przycisk **OK**.

    Po utworzeniu projektu usługi w aplikacji będą dostępne dwie usługi. W miarę postępu tworzenia aplikacji możesz w ten sam sposób dodać więcej usług. Każda z nich może być niezależnie wersjonowana i uaktualniana.

3. Na następnej stronie znajduje się zestaw szablonów projektów programu ASP.NET Core. Na potrzeby tego samouczka wybierz szablon **Interfejs API**.

    Program Visual Studio utworzy projekt usługi VotingData, a następnie wyświetli go w Eksploratorze rozwiązań.

    ![Eksplorator rozwiązań](./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-webapi-service.png)

### <a name="add-the-votedatacontrollercs-file"></a>Dodawanie pliku VoteDataController.cs

W projekcie **VotingData** kliknij prawym przyciskiem myszy folder **Controllers**, a następnie wybierz kolejno pozycje **Dodaj->Nowy element->Klasa**. Nazwij plik **VoteDataController.cs** i kliknij przycisk **Dodaj**. Zastąp zawartość tego pliku następującym kodem i zapisz zmiany.

```csharp
namespace VotingData.Controllers
{
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.ServiceFabric.Data;
    using Microsoft.ServiceFabric.Data.Collections;

    [Route("api/[controller]")]
    public class VoteDataController : Controller
    {
        private readonly IReliableStateManager stateManager;

        public VoteDataController(IReliableStateManager stateManager)
        {
            this.stateManager = stateManager;
        }

        // GET api/VoteData
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            CancellationToken ct = new CancellationToken();

            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                Microsoft.ServiceFabric.Data.IAsyncEnumerable<KeyValuePair<string, int>> list = await votesDictionary.CreateEnumerableAsync(tx);

                Microsoft.ServiceFabric.Data.IAsyncEnumerator<KeyValuePair<string, int>> enumerator = list.GetAsyncEnumerator();

                List<KeyValuePair<string, int>> result = new List<KeyValuePair<string, int>>();

                while (await enumerator.MoveNextAsync(ct))
                {
                    result.Add(enumerator.Current);
                }

                return this.Json(result);
            }
        }

        // PUT api/VoteData/name
        [HttpPut("{name}")]
        public async Task<IActionResult> Put(string name)
        {
            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
                await tx.CommitAsync();
            }

            return new OkResult();
        }

        // DELETE api/VoteData/name
        [HttpDelete("{name}")]
        public async Task<IActionResult> Delete(string name)
        {
            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                if (await votesDictionary.ContainsKeyAsync(tx, name))
                {
                    await votesDictionary.TryRemoveAsync(tx, name);
                    await tx.CommitAsync();
                    return new OkResult();
                }
                else
                {
                    return new NotFoundResult();
                }
            }
        }
    }
}
```

## <a name="connect-the-services"></a>Łączenie usług

W następnym kroku połączysz te dwie usługi i sprawisz, że aplikacja internetowa frontonu będzie pobierała z usługi zaplecza informacje o głosowaniu i ustawiała je.

Usługa Service Fabric zapewnia pełną elastyczność w sposobie komunikowania się z niezawodnymi usługami. W obrębie pojedynczej aplikacji mogą istnieć usługi, które są dostępne za pośrednictwem protokołu TCP. Inne usługi mogą być dostępne za pośrednictwem interfejsu API REST protokołu HTTP, a jeszcze inne za pośrednictwem gniazd internetowych. Aby zapoznać się z opisem dostępnych opcji i zastosowanych technologii, zobacz [Communicating with services](service-fabric-connect-and-communicate-with-services.md) (Komunikacja z usługami).

W tym samouczku użyj [internetowego interfejsu API platformy ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md) oraz [zwrotnego serwera proxy platformy Service Fabric](service-fabric-reverseproxy.md), aby internetowa usługa frontonu VotingWeb mogła komunikować się z usługą zaplecza VotingData. Zwrotny serwer proxy został skonfigurowany tak, aby domyślnie używać portu 19081, i powinien działać w tym samouczku. Port zwrotnego serwera proxy jest skonfigurowany w szablonie usługi Azure Resource Manager użytym do skonfigurowania klastra. Aby sprawdzić, który port jest używany, należy poszukać go w szablonie klastra w zasobie **Microsoft.ServiceFabric/clusters**: 

```json
"nodeTypes": [
          {
            ...
            "httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
            "isPrimary": true,
            "vmInstanceCount": "[parameters('nt0InstanceCount')]",
            "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]"
          }
        ],
```
Aby odnaleźć port zwrotnego serwera proxy użyty w lokalnym klastrze programowania, wyświetl element **HttpApplicationGatewayEndpoint** w manifeście klastra lokalnej usługi Service Fabric:
1. Otwórz okno przeglądarki i przejdź do protokołu http: \/ /localhost: 19080, aby otworzyć narzędzie Service Fabric Explorer.
2. Wybierz kolejno pozycje **Klaster > Manifest**.
3. Zanotuj port elementu HttpApplicationGatewayEndpoint. Domyślnie port to 19081. Jeśli port jest inny niż 19081, musisz zmienić go w metodzie GetProxyAddress następującego kodu VotesController.cs.

<a id="updatevotecontroller" name="updatevotecontroller_anchor"></a>

### <a name="update-the-votescontrollercs-file"></a>Aktualizowanie pliku VotesController.cs

W projekcie **VotingWeb** otwórz plik *Controllers/VotesController.cs*.  Zastąp zawartość definicji klasy `VotesController` następującym kodem i zapisz zmiany. Jeśli port zwrotnego serwera proxy odnaleziony w poprzednim kroku jest inny niż 19081, zmień port używany w metodzie GetProxyAddress z 19081 na odnaleziony port.

```csharp
public class VotesController : Controller
{
    private readonly HttpClient httpClient;
    private readonly FabricClient fabricClient;
    private readonly StatelessServiceContext serviceContext;

    public VotesController(HttpClient httpClient, StatelessServiceContext context, FabricClient fabricClient)
    {
        this.fabricClient = fabricClient;
        this.httpClient = httpClient;
        this.serviceContext = context;
    }

    // GET: api/Votes
    [HttpGet("")]
    public async Task<IActionResult> Get()
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);

        ServicePartitionList partitions = await this.fabricClient.QueryManager.GetPartitionListAsync(serviceName);

        List<KeyValuePair<string, int>> result = new List<KeyValuePair<string, int>>();

        foreach (Partition partition in partitions)
        {
            string proxyUrl =
                $"{proxyAddress}/api/VoteData?PartitionKey={((Int64RangePartitionInformation) partition.PartitionInformation).LowKey}&PartitionKind=Int64Range";

            using (HttpResponseMessage response = await this.httpClient.GetAsync(proxyUrl))
            {
                if (response.StatusCode != System.Net.HttpStatusCode.OK)
                {
                    continue;
                }

                result.AddRange(JsonConvert.DeserializeObject<List<KeyValuePair<string, int>>>(await response.Content.ReadAsStringAsync()));
            }
        }

        return this.Json(result);
    }

    // PUT: api/Votes/name
    [HttpPut("{name}")]
    public async Task<IActionResult> Put(string name)
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);
        long partitionKey = this.GetPartitionKey(name);
        string proxyUrl = $"{proxyAddress}/api/VoteData/{name}?PartitionKey={partitionKey}&PartitionKind=Int64Range";

        StringContent putContent = new StringContent($"{{ 'name' : '{name}' }}", Encoding.UTF8, "application/json");
        putContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");

        using (HttpResponseMessage response = await this.httpClient.PutAsync(proxyUrl, putContent))
        {
            return new ContentResult()
            {
                StatusCode = (int) response.StatusCode,
                Content = await response.Content.ReadAsStringAsync()
            };
        }
    }

    // DELETE: api/Votes/name
    [HttpDelete("{name}")]
    public async Task<IActionResult> Delete(string name)
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);
        long partitionKey = this.GetPartitionKey(name);
        string proxyUrl = $"{proxyAddress}/api/VoteData/{name}?PartitionKey={partitionKey}&PartitionKind=Int64Range";

        using (HttpResponseMessage response = await this.httpClient.DeleteAsync(proxyUrl))
        {
            if (response.StatusCode != System.Net.HttpStatusCode.OK)
            {
                return this.StatusCode((int) response.StatusCode);
            }
        }

        return new OkResult();
    }


    /// <summary>
    /// Constructs a reverse proxy URL for a given service.
    /// Example: http://localhost:19081/VotingApplication/VotingData/
    /// </summary>
    /// <param name="serviceName"></param>
    /// <returns></returns>
    private Uri GetProxyAddress(Uri serviceName)
    {
        return new Uri($"http://localhost:19081{serviceName.AbsolutePath}");
    }

    /// <summary>
    /// Creates a partition key from the given name.
    /// Uses the zero-based numeric position in the alphabet of the first letter of the name (0-25).
    /// </summary>
    /// <param name="name"></param>
    /// <returns></returns>
    private long GetPartitionKey(string name)
    {
        return Char.ToUpper(name.First()) - 'A';
    }
}
```

<a id="walkthrough" name="walkthrough_anchor"></a>

## <a name="walk-through-the-voting-sample-application"></a>Szczegółowe omówienie przykładowej aplikacji do głosowania

Aplikacja do głosowania składa się z dwóch usług:

* Usługa internetowa frontonu (VotingWeb) — usługa internetowa frontonu platformy ASP.NET Core, obsługująca stronę internetową i ujawniająca interfejsy API sieci Web na potrzeby komunikacji z usługą zaplecza.
* Usługi zaplecza (VotingData) — usługa internetowa platformy ASP.NET Core, która uwidacznia interfejs API do przechowywania wyników głosowania w niezawodnym słowniku utrwalonym na dysku.

![Diagram aplikacji](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

Podczas głosowania w aplikacji występują następujące zdarzenia:

1. Plik JavaScript wysyła żądanie głosowania do internetowego interfejsu API w usłudze internetowej frontonu jako żądanie HTTP PUT.

2. Usługa internetowa frontonu używa serwera proxy w celu zlokalizowania i przesłania żądania HTTP PUT do usługi zaplecza.

3. Usługa zaplecza przyjmuje żądanie przychodzące i przechowuje zaktualizowany wynik w niezawodnym słowniku, który jest replikowany do wielu węzłów w klastrze i utrwalany na dysku. Wszystkie dane aplikacji są przechowywane w klastrze, więc baza danych nie jest wymagana.

## <a name="debug-in-visual-studio"></a>Debugowanie w programie Visual Studio

Podczas debugowania aplikacji w programie Visual Studio używany jest lokalny klaster projektowy usługi Service Fabric. Możesz opcjonalnie dostosować środowisko debugowania do danego scenariusza. W tej aplikacji dane są przechowywane w usłudze zaplecza przy użyciu niezawodnego słownika. Program Visual Studio domyślnie usuwa aplikację po zatrzymaniu debugera. Usunięcie aplikacji spowoduje, że dane w usłudze zaplecza także zostaną usunięte. Aby zachować dane między sesjami debugowania, możesz zmienić **Tryb debugowania aplikacji** jako właściwość w projekcie **Voting (Głosowanie)** w programie Visual Studio.

Aby zobaczyć, co się stanie w kodzie, wykonaj następujące kroki:

1. Otwórz plik **VotingWeb\VotesController.cs** i ustaw punkt przerwania w metodzie **Put** tego internetowego interfejsu API (wiersz 72).

2. Otwórz plik **VotingData\VoteDataController.cs** i ustaw punkt przerwania w metodzie **Put** tego internetowego interfejsu API (wiersz 54).

3. Naciśnij klawisz **F5**, aby uruchomić aplikację w trybie debugowania.

4. Wróć do przeglądarki i kliknij opcję głosowania lub dodaj nową opcję głosowania. Zostanie trafiony pierwszy punkt przerwania w kontrolerze interfejsu API frontonu internetowego.
    

   1. Jest to punkt, w którym skrypt JavaScript w przeglądarce wysyła żądanie do kontrolera internetowego interfejsu API w usłudze frontonu.

      ![Dodawanie usługi frontonu Vote](./media/service-fabric-tutorial-create-dotnet-app/addvote-frontend.png)

   2. Najpierw skonstruuj adres URL do elementu ReverseProxy dla usługi zaplecza **(1)**.
   3. Następnie Wyślij żądanie HTTP PUT do elementu reverseproxy **(2)**.
   4. Na koniec zwróć odpowiedź z usługi zaplecza do klienta **(3)**.

5. Naciśnij klawisz **F5**, aby kontynuować.
   1. Jesteś teraz w punkcie przerwania w usłudze zaplecza.

      ![Dodawanie usługi zaplecza Vote](./media/service-fabric-tutorial-create-dotnet-app/addvote-backend.png)

   2. W pierwszym wierszu metody **(1)** użyj elementu `StateManager` do pobrania lub dodania niezawodnego słownika o nazwie `counts`.
   3. Wszystkie interakcje z wartościami w niezawodnym słowniku wymagają transakcji — ta instrukcja using **(2)** tworzy tę transakcję.
   4. W transakcji zaktualizuj wartość odpowiedniego klucza dla opcji głosowania i zatwierdź operację **(3)**. Po powrocie z metody zatwierdzania dane są aktualizowane w słowniku i replikowane do innych węzłów w klastrze. Dane są bezpiecznie przechowywane w klastrze, a usługa zaplecza może zostać przełączona w tryb failover do innych węzłów, które nadal mają dostępne dane.
6. Naciśnij klawisz **F5**, aby kontynuować.

Aby zatrzymać sesję debugowania, naciśnij klawisze **Shift+F5**.

## <a name="next-steps"></a>Następne kroki

W tej części samouczka zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie usługi internetowego interfejsu API platformy ASP.NET Core jako niezawodnej stanowej usługi
> * Tworzenie usługi aplikacji internetowej platformy ASP.NET Core jako bezstanowej usługi internetowej
> * Używanie zwrotnego serwera proxy do komunikacji z usługą stanową

Przejdź do następnego samouczka:
> [!div class="nextstepaction"]
> [Wdrożenie aplikacji na platformie Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md)
