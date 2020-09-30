---
title: 'Samouczek: Tworzenie aplikacji Java na platformie Azure Service Fabric'
description: W tym samouczku znajdziesz informacje o sposobie tworzenia aplikacji usługi Reliable Service za pomocą frontonu, tworzenia bezstanowego zaplecza usług Reliable Services, a także o sposobie wdrażania aplikacji w klastrze.
ms.topic: tutorial
ms.date: 09/01/2018
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java, devx-track-js
ms.openlocfilehash: d1f66177b1939d612a5ee80c99109c397b56cb37
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91532197"
---
# <a name="tutorial-create-an-application-with-a-java-api-front-end-service-and-a-stateful-back-end-service-on-azure-service-fabric"></a>Samouczek: Tworzenie aplikacji przy użyciu usługi frontonu Java API i stanowej usługi zaplecza na platformie Azure Service Fabric

Niniejszy samouczek jest pierwszą częścią serii. Po zakończeniu będziesz mieć aplikację do głosowania z frontonem sieci Web w języku Java, która zapisuje wyniki głosowania w usłudze stanowej zaplecza na platformie Azure Service Fabric. Ta seria samouczków wymaga działającej maszyny dewelopera z systemem Mac OS X lub Linux. Jeśli nie chcesz ręcznie tworzyć aplikacji do głosowania, możesz [pobrać kod źródłowy ukończonej aplikacji](https://github.com/Azure-Samples/service-fabric-java-quickstart) i przejść od razu do [przewodnika po przykładowej aplikacji do głosowania](service-fabric-tutorial-create-java-app.md#walk-through-the-voting-sample-application). Rozważ również skorzystanie z [przewodnika Szybki Start dla niezawodnych usług Java.](service-fabric-quickstart-java-reliable-services.md)

![Service Fabric voting sample (Przykład głosowania usługi Service Fabric)](./media/service-fabric-tutorial-create-java-app/service-fabric-java-voting-app-sample.png)

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Kompilowanie aplikacji Java usług Reliable Services w usłudze Service Fabric
> * [Wdrażanie i debugowanie aplikacji w klastrze lokalnym](service-fabric-tutorial-debug-log-local-cluster.md)
> * [Wdrażanie aplikacji w klastrze platformy Azure](service-fabric-tutorial-java-deploy-azure.md)
> * [Konfigurowanie monitorowania i diagnostyki dla aplikacji](service-fabric-tutorial-java-elk.md)
> * [Konfigurowanie ciągłej integracji/ciągłego dostarczania](service-fabric-tutorial-java-jenkins.md)


Część pierwsza serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie stanowej usługi Reliable Service w języku Java
> * Tworzenie bezstanowej internetowej usługi aplikacji w języku Java
> * Używanie usług zdalnych do komunikacji z usługą stanową
> * Wdrażanie aplikacji w lokalnym klastrze usługi Service Fabric

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Skonfiguruj środowisko projektowe dla komputera [Mac](service-fabric-get-started-mac.md) lub systemu [Linux](service-fabric-get-started-linux.md). Postępuj zgodnie z instrukcjami, aby zainstalować wtyczkę programu Eclipse, program Gradle, zestaw Service Fabric SDK i interfejs wiersza polecenia usługi Service Fabric (sfctl).

## <a name="create-the-front-end-java-stateless-service"></a>Tworzenie bezstanowej usługi Java frontonu

Najpierw utwórz fronton internetowy aplikacji do głosowania. Internetowy interfejs użytkownika wspierany przez rozwiązanie AngularJS wysyła żądania do bezstanowej usługi Java, która uruchamia lekki serwer HTTP. Ta usługa przetwarza każde żądanie i wysyła zdalne wywołanie procedury do usługi stanowej w celu przechowywania głosów. 

1. Otwórz program Eclipse.

2. Utwórz projekt z **plikiem**  >  **New**  >  **other**  >  **Service Fabric**  >  **Service Fabric Project**.

    ![Nowy projekt Service Fabric w programie zaćmienie](./media/service-fabric-tutorial-create-java-app/service-fabric-project-wizard.png)

3. W oknie dialogowym **Kreator projektu dla sieci szkieletowej** Nazwij projekt **, a** następnie wybierz przycisk **dalej**.

    ![Wybieranie usługi bezstanowej Java w oknie dialogowym nowej usługi](./media/service-fabric-tutorial-create-java-app/name-service-fabric-project-wizard.png) 

4. Na stronie **Dodawanie usługi** wybierz pozycję **Usługa bezstanowa**i nadaj usłudze nazwę **VotingWeb**. Wybierz pozycję **Zakończ** , aby utworzyć projekt.

    ![Utwórz usługę bezstanową dla projektu Service Fabric]( ./media/service-fabric-tutorial-create-java-app/add-service-fabric-votingweb-service.png)

    Program Eclipse utworzy projekt aplikacji i projekt usługi, a następnie wyświetli je w narzędziu Package Explorer.

    ![Narzędzie Package Explorer programu Eclipse po utworzeniu aplikacji]( ./media/service-fabric-tutorial-create-java-app/eclipse-package-explorer.png)

W tabeli przedstawiono krótki opis każdego elementu narzędzia Package Explorer z poprzedniego zrzutu ekranu. 

| **Element narzędzia Package Explorer** | **Opis** |
| --- | --- |
| PublishProfiles | Zawiera pliki w formacie JSON opisujące szczegóły profilu lokalnego i klastrów usługi Azure Service Fabric. Zawartość tych plików jest używana przez wtyczkę podczas wdrażania aplikacji. |
| Skrypty | Zawiera skrypty pomocnika, których można użyć z poziomu wiersza polecenia do szybkiego zarządzania aplikacjami z klastrem. |
| VotingApplication | Zawiera aplikację usługi Service Fabric wypychaną do klastra usługi Service Fabric. |
| VotingWeb | Zawiera pliki źródłowe usługi bezstanowej frontonu oraz powiązany plik kompilacji narzędzia Gradle. |
| build.gradle | Plik narzędzia Gradle używany do zarządzania projektem. |
| settings.gradle | Zawiera nazwy projektów Gradle w tym folderze. |

### <a name="add-html-and-javascript-to-the-votingweb-service"></a>Dodawanie kodu HTML i Javascript do usługi VotingWeb

Aby dodać interfejs użytkownika, który może być renderowany przez usługę bezstanową, dodaj plik HTML. Ten plik HTML jest następnie renderowany przez lekki serwer HTTP osadzone w usłudze bezstanowej Java.

1. Rozwiń katalog *VotingApplication*, aby przejść do katalogu *VotingApplication/VotingWebPkg/Code*.

2. Kliknij prawym przyciskiem myszy katalog *kod* , a następnie wybierz pozycję **Nowy**  >  **folder**.

3. Nazwij folder *wwwroot* i wybierz pozycję **Zakończ**.

    ![Tworzenie folderu wwwroot w programie Eclipse](./media/service-fabric-tutorial-create-java-app/create-wwwroot-folder.png)

4. Do folderu **wwwroot** dodaj plik o nazwie **index.html** i wklej poniższą zawartość do tego pliku.

```html
<!DOCTYPE html>
<html>
<script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/angular-ui-bootstrap/0.13.4/ui-bootstrap-tpls.min.js"></script>
<link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css">
<body>

<script>
var app = angular.module('VotingApp', ['ui.bootstrap']);
app.controller("VotingAppController", ['$rootScope', '$scope', '$http', '$timeout', function ($rootScope, $scope, $http, $timeout) {
    $scope.votes = [];
    
    $scope.refresh = function () {
        $http.get('getStatelessList')
            .then(function successCallback(response) {
        $scope.votes = Object.assign(
            {},
            ...Object.keys(response.data) .
            map(key => ({[decodeURI(key)]: response.data[key]}))
        )
        },
        function errorCallback(response) {
            alert(response);
        });
    };

    $scope.remove = function (item) {
       $http.get("removeItem", {params: { item: encodeURI(item) }})
            .then(function successCallback(response) {
                $scope.refresh();
            },
            function errorCallback(response) {
                alert(response);
            });
    };

    $scope.add = function (item) {
        if (!item) {return;}
        $http.get("addItem", {params: { item: encodeURI(item) }})
            .then(function successCallback(response) {
                $scope.refresh();
            },
            function errorCallback(response) {
                alert(response);
            });
    };
}]);
</script>

<div ng-app="VotingApp" ng-controller="VotingAppController" ng-init="refresh()">
    <div class="container-fluid">
        <div class="row">
            <div class="col-xs-8 col-xs-offset-2 text-center">
                <h2>Service Fabric Voting Sample</h2>
            </div>
        </div>

        <div class="row">
            <div class="col-xs-offset-2">
                <form style="width:50% ! important;" class="center-block">
                    <div class="col-xs-6 form-group">
                        <input id="txtAdd" type="text" class="form-control" placeholder="Add voting option" ng-model="item" />
                    </div>
                    <button id="btnAdd" class="btn btn-default" ng-click="add(item)">
                        <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                        Add
                    </button>
                </form>
            </div>
        </div>

        <hr />

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <div class="row">
                    <div class="col-xs-4">
                        Click to vote
                    </div>
                </div>
                <div class="row top-buffer" ng-repeat="(key, value)  in votes">
                    <div class="col-xs-8">
                        <button class="btn btn-success text-left btn-block" ng-click="add(key)">
                            <span class="pull-left">
                                {{key}}
                            </span>
                            <span class="badge pull-right">
                                {{value}} Votes
                            </span>
                        </button>
                    </div>
                    <div class="col-xs-4">
                        <button class="btn btn-danger pull-right btn-block" ng-click="remove(key)">
                            <span class="glyphicon glyphicon-remove" aria-hidden="true"></span>
                            Remove
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>

</body>
</html>
```

### <a name="update-the-votingwebjava-file"></a>Aktualizowanie pliku VotingWeb.java

W projekcie podrzędnym **VotingWeb** otwórz plik *VotingWeb/src/statelessservice/VotingWeb.java*. Usługa **VotingWeb** to brama do usługi bezstanowej odpowiedzialna za konfigurowanie odbiornika komunikacji dla interfejsu API frontonu.

Zastąp istniejącą metodę **createServiceInstanceListeners** w pliku poniższym kodem, a następnie zapisz zmiany.

```java
@Override
protected List<ServiceInstanceListener> createServiceInstanceListeners() {

    EndpointResourceDescription endpoint = this.getServiceContext().getCodePackageActivationContext().getEndpoint(webEndpointName);
    int port = endpoint.getPort();

    List<ServiceInstanceListener> listeners = new ArrayList<ServiceInstanceListener>();
    listeners.add(new ServiceInstanceListener((context) -> new HttpCommunicationListener(context, port)));
    return listeners;
}
```

### <a name="add-the-httpcommunicationlistenerjava-file"></a>Dodawanie pliku HTTPCommunicationListener.java

Odbiornik komunikacji HTTP działa jako kontroler, który konfiguruje serwer HTTP i uwidacznia interfejsy API definiujące akcje głosowania. Kliknij prawym przyciskiem myszy pakiet *statelessservice* w folderze *VotingWeb/src/statelessservice* , a następnie wybierz pozycję **Nowy**  >  **plik**.  Nazwij plik *HttpCommunicationListener. Java* i wybierz pozycję **Zakończ**.

Zastąp zawartość tego pliku następującym kodem i zapisz zmiany.  Dalej w sekcji „Aktualizowanie pliku HttpCommunicationListener.java” ten plik zostanie zmodyfikowany na potrzeby renderowania i odczytywania danych głosowania z usługi zaplecza oraz ich zapisywania.  Obecnie odbiornik po prostu zwraca statyczny kod HTML dla aplikacji do głosowania.

```java
// ------------------------------------------------------------
//  Copyright (c) Microsoft Corporation.  All rights reserved.
//  Licensed under the MIT License (MIT). See License.txt in the repo root for license information.
// ------------------------------------------------------------

package statelessservice;

import com.google.gson.Gson;
import com.sun.net.httpserver.HttpExchange;
import com.sun.net.httpserver.HttpHandler;
import com.sun.net.httpserver.Headers;

import java.io.File;
import java.io.OutputStream;
import java.io.FileInputStream;

import java.net.InetSocketAddress;
import java.net.URI;

import java.util.HashMap;
import java.util.Map;
import java.util.concurrent.CompletableFuture;
import java.util.logging.Level;
import java.util.logging.Logger;

import microsoft.servicefabric.services.communication.runtime.CommunicationListener;
import microsoft.servicefabric.services.runtime.StatelessServiceContext;
import microsoft.servicefabric.services.client.ServicePartitionKey;
import microsoft.servicefabric.services.remoting.client.ServiceProxyBase;
import microsoft.servicefabric.services.communication.client.TargetReplicaSelector;
import system.fabric.CancellationToken;

public class HttpCommunicationListener implements CommunicationListener {

    private static final Logger logger = Logger.getLogger(HttpCommunicationListener.class.getName());

    private static final String HEADER_CONTENT_TYPE = "Content-Type";
    private static final int STATUS_OK = 200;
    private static final int STATUS_NOT_FOUND = 404;
    private static final int STATUS_ERROR = 500;
    private static final String RESPONSE_NOT_FOUND = "404 (Not Found) \n";
    private static final String MIME = "text/html";
    private static final String ENCODING = "UTF-8";

    private static final String ROOT = "wwwroot/";
    private static final String FILE_NAME = "index.html";
    private StatelessServiceContext context;
    private com.sun.net.httpserver.HttpServer server;
    private ServicePartitionKey partitionKey;
    private final int port;

    public HttpCommunicationListener(StatelessServiceContext context, int port) {
        this.partitionKey = new ServicePartitionKey(0);
        this.context = context;
        this.port = port;
    }

    // Called by openAsync when the class is instantiated
    public void start() {
        try {
            logger.log(Level.INFO, "Starting Server");
            server = com.sun.net.httpserver.HttpServer.create(new InetSocketAddress(this.port), 0);
        } catch (Exception ex) {
            logger.log(Level.SEVERE, null, ex);
            throw new RuntimeException(ex);
        }

        // Responsible for rendering the HTML layout described in the previous step
        server.createContext("/", new HttpHandler() {
            @Override
            public void handle(HttpExchange t) {
                try {
                    File file = new File(ROOT + FILE_NAME).getCanonicalFile();

                    if (!file.isFile()) {
                      // Object does not exist or is not a file: reject with 404 error.
                      t.sendResponseHeaders(STATUS_NOT_FOUND, RESPONSE_NOT_FOUND.length());
                      OutputStream os = t.getResponseBody();
                      os.write(RESPONSE_NOT_FOUND.getBytes());
                      os.close();
                    } else {
                      Headers h = t.getResponseHeaders();
                      h.set(HEADER_CONTENT_TYPE, MIME);
                      t.sendResponseHeaders(STATUS_OK, 0);
    
                      OutputStream os = t.getResponseBody();
                      FileInputStream fs = new FileInputStream(file);
                      final byte[] buffer = new byte[0x10000];
                      int count = 0;
                      while ((count = fs.read(buffer)) >= 0) {
                        os.write(buffer,0,count);
                      }

                      fs.close();
                      os.close();
                    }
                } catch (Exception e) {
                    logger.log(Level.WARNING, null, e);
                }
            }
        });

        /*
        [Replace this entire comment block in the 'Connect the services' section]
        */

        server.setExecutor(null);
        server.start();
    }

    //Helper method to parse raw HTTP requests
    private Map<String, String> queryToMap(String query){
        Map<String, String> result = new HashMap<String, String>();
        for (String param : query.split("&")) {
            String pair[] = param.split("=");
            if (pair.length>1) {
                result.put(pair[0], pair[1]);
            }else{
                result.put(pair[0], "");
            }
        }
        return result;
    }

    //Called closeAsync when the service is shut down
    private void stop() {
        if (null != server)
            server.stop(0);
    }

    //Called by the Service Fabric runtime when this service is created on a node
    @Override
    public CompletableFuture<String> openAsync(CancellationToken cancellationToken) {
        this.start();
                    logger.log(Level.INFO, "Opened Server");
        String publishUri = String.format("http://%s:%d/", this.context.getNodeContext().getIpAddressOrFQDN(), port);
        return CompletableFuture.completedFuture(publishUri);
    }

    //Called by the Service Fabric runtime when the service is shut down
    @Override
    public CompletableFuture<?> closeAsync(CancellationToken cancellationToken) {
        this.stop();
        return CompletableFuture.completedFuture(true);
    }

    //Called by the Service Fabric runtime to forcibly shut this listener down
    @Override
    public void abort() {
        this.stop();
    }
}
```

### <a name="configure-the-listening-port"></a>Konfigurowanie portu nasłuchującego

Po utworzeniu usługi frontonu usługi VotingWeb usługa Service Fabric wybiera port, na którym usługa będzie nasłuchiwała.  Usługa VotingWeb działa jako fronton dla tej aplikacji i akceptuje ruch zewnętrzny, dlatego warto powiązać ją z ustalonym i dobrze znanym portem. W narzędziu Package Explorer otwórz plik *VotingApplication/VotingWebPkg/ServiceManifest.xml*.  Znajdź zasób **Endpoint** w sekcji **Resources** i zmień wartość **Port** na 8080 (ten port będzie nadal używany w ramach tego samouczka). Aby wdrożyć i uruchomić aplikację lokalnie, port nasłuchujący aplikacji musi być otwarty i dostępny na komputerze. Wklej poniższy fragment kodu w obrębie elementu **ServiceManifest** (czyli tuż poniżej elementu ```<DataPackage>```).

```xml
<Resources>
    <Endpoints>
        <!-- This endpoint is used by the communication listener to obtain the port on which to
            listen. Please note that if your service is partitioned, this port is shared with
            replicas of different partitions that are placed in your code. -->
        <Endpoint Name="WebEndpoint" Protocol="http" Port="8080" />
    </Endpoints>
  </Resources>
```

## <a name="add-a-stateful-back-end-service-to-your-application"></a>Dodawanie stanowej usługi zaplecza do aplikacji

Po ukończeniu szkieletu internetowej usługi interfejsu API w języku Java spróbujmy utworzyć kompletną stanową usługę zaplecza.

Usługa Service Fabric umożliwia spójne i niezawodne przechowywanie danych bezpośrednio w usłudze przy użyciu niezawodnych kolekcji. Elementy Reliable Collections to zestaw wysoko dostępnych i niezawodnych klas kolekcji. Sposób użycia tych klas jest znany wszystkim osobom, które korzystały z kolekcji Java.

1. W Eksploratorze pakietów kliknij prawym przyciskiem myszy pozycję **głos** w projekcie aplikacji i wybierz pozycję **Service Fabric**  >  **Dodaj usługę Service Fabric**.

2. W oknie dialogowym **Dodawanie usługi** wybierz pozycję **Usługa stanowa** i nazwij usługę **VotingDataService** i wybierz pozycję **Dodaj usługę**.

    Po utworzeniu projektu usługi w aplikacji będą dostępne dwie usługi. W miarę postępu tworzenia aplikacji możesz w ten sam sposób dodać więcej usług. Każda z nich może być niezależnie wersjonowana i uaktualniana.

3. Program Eclipse utworzy projekt usługi, a następnie wyświetli go w narzędziu Package Explorer.

    ![Zaćmienie Eksploratora projektów](./media/service-fabric-tutorial-create-java-app/service-fabric-package-explorer-java.png)

### <a name="add-the-votingdataservicejava-file"></a>Dodawanie pliku VotingDataService.java

Plik *VotingDataService.java* zawiera metody obejmujące logikę pobierania, dodawania i usuwania głosów z elementów Reliable Collections. Dodaj następujące metody klasy **VotingDataService** do pliku *VotingDataService/src/statefulservice/VotingDataService.java*.

```java
package statefulservice;

import java.util.HashMap;
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.CompletableFuture;
import java.util.concurrent.atomic.AtomicInteger;

import microsoft.servicefabric.services.communication.runtime.ServiceReplicaListener;

import microsoft.servicefabric.services.runtime.StatefulService;

import microsoft.servicefabric.services.remoting.fabrictransport.runtime.FabricTransportServiceRemotingListener;

import microsoft.servicefabric.data.ReliableStateManager;
import microsoft.servicefabric.data.Transaction;
import microsoft.servicefabric.data.collections.ReliableHashMap;
import microsoft.servicefabric.data.utilities.AsyncEnumeration;
import microsoft.servicefabric.data.utilities.KeyValuePair;

import system.fabric.StatefulServiceContext;

import rpcmethods.VotingRPC;

class VotingDataService extends StatefulService implements VotingRPC {
    private static final String MAP_NAME = "votesMap";
    private ReliableStateManager stateManager;

    protected VotingDataService (StatefulServiceContext statefulServiceContext) {
        super (statefulServiceContext);
    }

    @Override
    protected List<ServiceReplicaListener> createServiceReplicaListeners() {
        this.stateManager = this.getReliableStateManager();
        ArrayList<ServiceReplicaListener> listeners = new ArrayList<>();

        listeners.add(new ServiceReplicaListener((context) -> {
            return new FabricTransportServiceRemotingListener(context,this);
        }));

        return listeners;
    }

    // Method that will be invoked via RPC from the front end to retrieve the complete set of votes in the map
    public CompletableFuture<HashMap<String,String>> getList() {
        HashMap<String, String> tempMap = new HashMap<String, String>();

        try {

            ReliableHashMap<String, String> votesMap = stateManager
                    .<String, String> getOrAddReliableHashMapAsync(MAP_NAME).get();

            Transaction tx = stateManager.createTransaction();
            AsyncEnumeration<KeyValuePair<String, String>> kv = votesMap.keyValuesAsync(tx).get();
            while (kv.hasMoreElementsAsync().get()) {
                KeyValuePair<String, String> k = kv.nextElementAsync().get();
                tempMap.put(k.getKey(), k.getValue());
            }

            tx.close();


        } catch (Exception e) {
            e.printStackTrace();
        }

        return CompletableFuture.completedFuture(tempMap);
    }

    // Method that will be invoked via RPC from the front end to add an item to the votes list or to increase the
    // vote count for a particular item
    public CompletableFuture<Integer> addItem(String itemToAdd) {
        AtomicInteger status = new AtomicInteger(-1);

        try {

            ReliableHashMap<String, String> votesMap = stateManager
                    .<String, String> getOrAddReliableHashMapAsync(MAP_NAME).get();

            Transaction tx = stateManager.createTransaction();
            votesMap.computeAsync(tx, itemToAdd, (k, v) -> {
                if (v == null) {
                    return "1";
                }
                else {
                    int numVotes = Integer.parseInt(v);
                    numVotes = numVotes + 1;
                    return Integer.toString(numVotes);
                }
            }).get();

            tx.commitAsync().get();
            tx.close();

            status.set(1);
        } catch (Exception e) {
            e.printStackTrace();
        }

        return CompletableFuture.completedFuture(new Integer(status.get()));
    }

    // Method that will be invoked via RPC from the front end to remove an item
    public CompletableFuture<Integer> removeItem(String itemToRemove) {
        AtomicInteger status = new AtomicInteger(-1);
        try {
            ReliableHashMap<String, String> votesMap = stateManager
                    .<String, String> getOrAddReliableHashMapAsync(MAP_NAME).get();

            Transaction tx = stateManager.createTransaction();
            votesMap.removeAsync(tx, itemToRemove).get();
            tx.commitAsync().get();
            tx.close();

            status.set(1);
        } catch (Exception e) {
            e.printStackTrace();
        }

        return CompletableFuture.completedFuture(new Integer(status.get()));
    }

}
```

Został utworzony szkielet usługi bezstanowej frontonu i usługi zaplecza.

## <a name="create-the-communication-interface-to-your-application"></a>Tworzenie interfejsu komunikacji z aplikacją

 Następnym krokiem jest połączenie usługi bezstanowej frontonu i usługi zaplecza. Usługi te wykorzystują interfejs o nazwie VotingRPC definiujący operacje aplikacji do głosowania. Ten interfejs jest implementowany przez usługi frontonu i zaplecza w celu umożliwienia zdalnych wywołań procedur (RPC) między dwoma usługami. Niestety, program Eclipse nie obsługuje dodawania projektów podrzędnych narzędzia Gradle, więc pakiet zawierający ten interfejs należy dodać ręcznie.

1. Kliknij prawym przyciskiem myszy projekt **głosowania** w Eksploratorze pakietów i wybierz pozycję **Nowy**  >  **folder**. Nadaj folderowi nazwę **VotingRPC/src/rpcmethods**.

    ![Utwórz pakiet VotingRPC w Eksploratorze pakietów w przezaćmieniu](./media/service-fabric-tutorial-create-java-app/create-voting-rpc-package-java.png)

3. W obszarze *Voting/VotingRPC/src/rpcmethods* utwórz plik o nazwie *VotingRPC.java* i wklej następujący kod wewnątrz pliku **VotingRPC.java**. 

    ```java
    package rpcmethods;
    
    import java.util.ArrayList;
    import java.util.concurrent.CompletableFuture;
    import java.util.List;
    import java.util.HashMap;
    
    import microsoft.servicefabric.services.remoting.Service;
    
    public interface VotingRPC extends Service {
        CompletableFuture<HashMap<String, String>> getList();
    
        CompletableFuture<Integer> addItem(String itemToAdd);
    
        CompletableFuture<Integer> removeItem(String itemToRemove);
    }
    ```

4. Utwórz pusty plik o nazwie *build.gradle* w katalogu *Voting/VotingRPC* i wklej w nim poniższy kod. Ten plik narzędzia Gradle jest używany do kompilowania i tworzenia pliku JAR importowanego przez inne usługi. 

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    sourceSets {
      main {
         java.srcDirs = ['src']
         output.classesDir = 'out/classes'
          resources {
           srcDirs = ['src']
         }
       }
    }
    
    clean.doFirst {
        delete "${projectDir}/out"
        delete "${projectDir}/VotingRPC.jar"
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        compile ('com.microsoft.servicefabric:sf-actors:1.0.0')
    }
    
    jar {
        from configurations.compile.collect {
            (it.isDirectory() && !it.getName().contains("native")) ? it : zipTree(it)}

        manifest {
                attributes(
                'Main-Class': 'rpcmethods.VotingRPC')
            baseName "VotingRPC"
            destinationDir = file('./')
        }
    
        exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA'
    }

    defaultTasks 'clean', 'jar'
    ```

5. W pliku *Voting/settings.gradle* dodaj wiersz w celu uwzględnienia nowo utworzonego projektu w kompilacji. 

    ```gradle
    include ':VotingRPC'
    ```

6. W pliku *Voting/VotingWeb/src/statelessservice/HttpCommunicationListener.java* zastąp blok komentarza poniższym kodem.  

    ```java
    server.createContext("/getStatelessList", new HttpHandler() {
        @Override
        public void handle(HttpExchange t) {
            try {
                t.sendResponseHeaders(STATUS_OK,0);
                OutputStream os = t.getResponseBody();
    
                HashMap<String,String> list = ServiceProxyBase.create(VotingRPC.class, new URI("fabric:/VotingApplication/VotingDataService"), partitionKey, TargetReplicaSelector.DEFAULT, "").getList().get();
                String json = new Gson().toJson(list);
                os.write(json.getBytes(ENCODING));
                os.close();
            } catch (Exception e) {
                logger.log(Level.WARNING, null, e);
            }
        }
    });
    
    server.createContext("/removeItem", new HttpHandler() {
        @Override
        public void handle(HttpExchange t) {
            try {
                OutputStream os = t.getResponseBody();
                URI r = t.getRequestURI();
    
                Map<String, String> params = queryToMap(r.getQuery());
                String itemToRemove = params.get("item");

                Integer num = ServiceProxyBase.create(VotingRPC.class, new URI("fabric:/VotingApplication/VotingDataService"), partitionKey, TargetReplicaSelector.DEFAULT, "").removeItem(itemToRemove).get();
    
                if (num != 1)
                {
                    t.sendResponseHeaders(STATUS_ERROR, 0);
                } else {
                    t.sendResponseHeaders(STATUS_OK,0);
                }
    
                String json = new Gson().toJson(num);
                os.write(json.getBytes(ENCODING));
                os.close();
            } catch (Exception e) {
                logger.log(Level.WARNING, null, e);
            }
    
        }
    });
    
    server.createContext("/addItem", new HttpHandler() {
        @Override
        public void handle(HttpExchange t) {
            try {
                URI r = t.getRequestURI();
                Map<String, String> params = queryToMap(r.getQuery());
                String itemToAdd = params.get("item");

                OutputStream os = t.getResponseBody();
                Integer num = ServiceProxyBase.create(VotingRPC.class, new URI("fabric:/VotingApplication/VotingDataService"), partitionKey, TargetReplicaSelector.DEFAULT, "").addItem(itemToAdd).get();
                if (num != 1)
                {
                    t.sendResponseHeaders(STATUS_ERROR, 0);
                } else {
                    t.sendResponseHeaders(STATUS_OK,0);
                }
    
                String json = new Gson().toJson(num);
                os.write(json.getBytes(ENCODING));
                os.close();
            } catch (Exception e) {
                logger.log(Level.WARNING, null, e);
            }
        }
    });
    ```
7. Dodaj odpowiednią instrukcję importu na początku pliku *Voting/VotingWeb/src/statelessservice/HttpCommunicationListener.java*. 

    ```java
    import rpcmethods.VotingRPC; 
    ```

Na tym etapie funkcjonalność frontonu, zaplecza i zdalnego wywołania procedury jest kompletna. Następny etap to odpowiednie skonfigurowanie skryptów Gradle przed wdrożeniem w klastrze usługi Service Fabric. 

## <a name="walk-through-the-voting-sample-application"></a>Szczegółowe omówienie przykładowej aplikacji do głosowania
Aplikacja do głosowania składa się z dwóch usług:
- Usługa internetowa frontonu (VotingWeb) — usługa internetowa frontonu środowiska Java obsługująca stronę internetową i uwidaczniająca interfejsy API na potrzeby komunikacji z usługą zaplecza.
- Usługa zaplecza (VotingDataService) — internetowa usługa Java, która definiuje metody wywoływane za pomocą zdalnych wywołań procedury w celu utrzymania głosów.

![Przykładowy diagram głosowania](./media/service-fabric-tutorial-create-java-app/walkthrough-java-voting.png)

Podczas wykonywania akcji w aplikacji (dodawanie elementu, głosowanie, usuwanie elementu) występują następujące zdarzenia:
1. Plik JavaScript wysyła odpowiednie żądanie do internetowego interfejsu API w usłudze internetowej frontonu jako żądanie HTTP.

2. Internetowa usługa frontonu używa wbudowanej funkcjonalności komunikacji zdalnej usługi Service Fabric w celu zlokalizowania i przesłania żądania do usługi zaplecza. 

3. Usługa zaplecza definiuje metody aktualizujące wynik w elemencie Reliable Dictionary. Zawartość tego elementu jest replikowana w wielu węzłach w klastrze i utrzymywana na dysku. Wszystkie dane aplikacji są przechowywane w klastrze. 

## <a name="configure-gradle-scripts"></a>Konfigurowanie skryptów narzędzia Gradle 

W tej sekcji konfigurowane są skrypty narzędzia Gradle dla projektu. 

1. Zastąp zawartość pliku *Voting/build.gradle* następującym kodem.

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    subprojects {
        apply plugin: 'java'
    }
    
    defaultTasks 'clean', 'jar', 'copyDeps'
    ```

2. Zastąp zawartość pliku *Voting/VotingWeb/build.gradle* poniższym kodem.

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    sourceSets {
      main {
         java.srcDirs = ['src']
         output.classesDir = 'out/classes'
          resources {
           srcDirs = ['src']
         }
       }
    }
    
    clean.doFirst {
        delete "${projectDir}/../lib"
        delete "${projectDir}/out"
        delete "${projectDir}/../VotingApplication/VotingWebPkg/Code/lib"
        delete "${projectDir}/../VotingApplication/VotingWebPkg/Code/VotingWeb.jar"
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        compile ('com.microsoft.servicefabric:sf-actors:1.0.0-preview1')
        compile project(':VotingRPC')
    }
    
    task explodeDeps(type: Copy, dependsOn:configurations.compile) { task ->
        configurations.compile.filter {!it.toString().contains("native")}.each{
            from it
        }
    
        configurations.compile.filter {it.toString().contains("native")}.each{
            from zipTree(it)
        }
        into "../lib/"
        include "lib*.so", "*.jar"
    }
    
    task copyDeps<< {
        copy {
            from("../lib/")
            into("../VotingApplication/VotingWebPkg/Code/lib")
            include('lib*.so')
        }
    }
    
    compileJava.dependsOn(explodeDeps)
    
    jar {
        from configurations.compile.collect {(it.isDirectory() && !it.getName().contains("native")) ? it : zipTree(it)}
    
        manifest {
            attributes(
                'Main-Class': 'statelessservice.VotingWebServiceHost')
            baseName "VotingWeb"
            destinationDir = file('../VotingApplication/VotingWebPkg/Code/')
        }
    
        exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA'
    }
    
    defaultTasks 'clean', 'jar', 'copyDeps'
    ```

3. Zastąp zawartość pliku *Voting/VotingDataService/build.gradle*. 

    ```gradle
    apply plugin: 'java'
    apply plugin: 'eclipse'
    
    sourceSets {
      main {
         java.srcDirs = ['src']
         output.classesDir = 'out/classes'
          resources {
           srcDirs = ['src']
         }
       }
    }
    
    clean.doFirst {
        delete "${projectDir}/../lib"
        delete "${projectDir}/out"
        delete "${projectDir}/../VotingApplication/VotingDataServicePkg/Code/lib"
        delete "${projectDir}/../VotingApplication/VotingDataServicePkg/Code/VotingDataService.jar"
    }
    
    repositories {
        mavenCentral()
    }
    
    dependencies {
        compile ('com.microsoft.servicefabric:sf-actors:1.0.0-preview1')
        compile project(':VotingRPC')
    }
    
    task explodeDeps(type: Copy, dependsOn:configurations.compile) { task ->
        configurations.compile.filter {!it.toString().contains("native")}.each{
            from it
        }
    
        configurations.compile.filter {it.toString().contains("native")}.each{
            from zipTree(it)
        }
        into "../lib/"
        include "lib*.so", "*.jar"
    }
    
    compileJava.dependsOn(explodeDeps)
    
    task copyDeps<< {
        copy {
            from("../lib/")
            into("../VotingApplication/VotingDataServicePkg/Code/lib")
            include('lib*.so')
        }
    }
    
    jar {
        from configurations.compile.collect {
            (it.isDirectory() && !it.getName().contains("native")) ? it : zipTree(it)}
    
        manifest {
            attributes('Main-Class': 'statefulservice.VotingDataServiceHost')
    
            baseName "VotingDataService"
            destinationDir = file('../VotingApplication/VotingDataServicePkg/Code/')
        }
    
        exclude 'META-INF/*.RSA', 'META-INF/*.SF','META-INF/*.DSA'
    }
    
    defaultTasks 'clean', 'jar', 'copyDeps'
    ```

## <a name="deploy-application-to-local-cluster"></a>Wdrażanie aplikacji w klastrze lokalnym

Na tym etapie aplikacja jest gotowa do wdrożenia w lokalnym klastrze usługi Service Fabric.

1. Kliknij prawym przyciskiem myszy projekt **głosowania** w Eksploratorze pakietów i wybierz polecenie **Service Fabric**  >  **kompilowania** aplikacji, aby skompilować aplikację.

2. Uruchom lokalny klaster usługi Service Fabric. Ten krok zależy od środowiska projektowego (Mac lub Linux).

    Jeśli używasz komputera Mac, uruchom lokalny klaster przy użyciu następującego polecenia: zastąp polecenie przekazane do parametru **-v** ścieżką do własnego obszaru roboczego.

    ```bash
    docker run -itd -p 19080:19080 -p 8080:8080 -p --name sfonebox mcr.microsoft.com/service-fabric/onebox:latest
    ```
    Zobacz szczegółowe instrukcje w [przewodniku konfiguracji dla systemu OS X.](service-fabric-get-started-mac.md)

    Jeśli używasz maszyny z systemem Linux, uruchom lokalny klaster przy użyciu następującego polecenia: 

    ```bash 
    sudo /opt/microsoft/sdk/servicefabric/common/clustersetup/devclustersetup.sh
    ```
    Zobacz szczegółowe instrukcje w [przewodniku konfiguracji dla systemu Linux.](service-fabric-get-started-linux.md)

4. W Eksploratorze pakietów do przepakowania kliknij prawym przyciskiem myszy projekt **głosowania** i wybierz polecenie **Service Fabric**  >  **publikowanie aplikacji** 
5. W oknie **publikowanie aplikacji** wybierz pozycję **Local.jsna** liście rozwijanej, a następnie wybierz pozycję **Publikuj**.
6. Przejdź do przeglądarki sieci Web i uzyskaj dostęp do \/ aplikacji http:/localhost: 8080, aby wyświetlić działającą aplikację w lokalnym klastrze Service Fabric. 

## <a name="next-steps"></a>Następne kroki

W tej części samouczka zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie usługi języka Java jako niezawodnej usługi stanowej
> * Tworzenie usługi języka Java jako bezstanowej usługi internetowej
> * Dodawanie interfejsu języka Java w celu obsługi zdalnych wywołań procedury między usługami
> * Konfigurowanie skryptów narzędzia Gradle
> * Kompilowanie i wdrażanie aplikacji w lokalnym klastrze usługi Service Fabric

Przejdź do następnego samouczka:
> [!div class="nextstepaction"]
> [Debugowanie i rejestrowanie aplikacji w klastrze lokalnym](service-fabric-tutorial-debug-log-local-cluster.md)
