---
title: Szybki start — wdrażanie pierwszej aplikacji w usłudze Azure Spring Cloud
description: W tym przewodniku szybki start wdrożono aplikację w chmurze ze sprężyną w chmurze sieci platformy Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/23/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 416f997475e35f8e784679ca0826f7af38d756c7
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880308"
---
# <a name="quickstart-deploy-your-first-azure-spring-cloud-application"></a>Szybki Start: wdrażanie pierwszej aplikacji w chmurze Azure wiosny

::: zone pivot="programming-language-csharp"
Ten przewodnik Szybki Start wyjaśnia, jak wdrożyć prostą aplikację mikrousług w chmurze Azure wiosną do uruchamiania na platformie Azure.

>[!NOTE]
> Obsługa steeltoe w chmurze z systemem Azure jest obecnie oferowana jako publiczna wersja zapoznawcza. Oferty publicznej wersji zapoznawczej umożliwiają klientom eksperymentowanie z nowymi funkcjami przed ich oficjalną wersją.  Funkcje i usługi publicznej wersji zapoznawczej nie są przeznaczone do użytku produkcyjnego.  Aby uzyskać więcej informacji o obsłudze wersji zapoznawczych, zapoznaj się z tematem [często zadawanych pytań](https://azure.microsoft.com/support/faq/) lub pliku [support Request](../azure-portal/supportability/how-to-create-azure-support-request.md).

Postępując zgodnie z tym przewodnikiem Szybki Start, dowiesz się, jak:

> [!div class="checklist"]
> * Generowanie podstawowego projektu steeltoe .NET Core
> * Inicjowanie obsługi administracyjnej wystąpienia usługi w chmurze ze sprężyną Azure
> * Kompilowanie i wdrażanie aplikacji za pomocą publicznego punktu końcowego
> * Przesyłanie strumieniowe dzienników w czasie rzeczywistym

Kod aplikacji używany w tym przewodniku szybki start to prosta aplikacja skompilowana przy użyciu szablonu projektu interfejsu API sieci Web platformy .NET Core. Po ukończeniu tego przykładu aplikacja będzie dostępna w trybie online i może być zarządzana za pośrednictwem Azure Portal i interfejsu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Zestaw SDK platformy .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1). Usługa w chmurze Azure wiosennej obsługuje program .NET Core 3,1 i jego nowsze wersje.
* [Interfejs wiersza polecenia platformy Azure w wersji 2.0.67 lub nowszej](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest).
* Usługi [git](https://git-scm.com/).

## <a name="install-azure-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia platformy Azure

Sprawdź, czy wersja interfejsu wiersza polecenia platformy Azure to 2.0.67 lub nowszego:

```azurecli
az --version
```

Zainstaluj rozszerzenie chmury wiosennej platformy Azure dla interfejsu wiersza polecenia platformy Azure przy użyciu następującego polecenia:

```azurecli
az extension add --name spring-cloud
```

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

1. Logowanie do interfejsu wiersza polecenia platformy Azure

    ```azurecli
    az login
    ```

1. Jeśli masz więcej niż jedną subskrypcję, wybierz tę, która ma być używana w tym przewodniku Szybki Start.

   ```azurecli
   az account list -o table
   ```

   ```azurecli
   az account set --subscription <Name or ID of a subscription from the last step>
   ```

## <a name="generate-a-steeltoe-net-core-project"></a>Generowanie projektu steeltoe .NET Core

W programie Visual Studio Utwórz ASP.NET Core aplikację sieci Web o nazwie "Hello-World" przy użyciu szablonu projektu interfejsu API. Zwróć uwagę na to, że zostanie wygenerowany automatycznie WeatherForecastController, który będzie później używany przez nasz punkt końcowy testu.

1. Utwórz folder dla kodu źródłowego projektu i Wygeneruj projekt.
 
   ```console
   mkdir source-code
   ```

   ```console
   cd source-code
   ```

   ```dotnetcli
   dotnet new webapi -n hello-world --framework netcoreapp3.1
   ```

1. Przejdź do katalogu projektu.

   ```console
   cd hello-world
   ```

1. Edytuj *appSettings.jsw* pliku, aby dodać następujące ustawienia:

   ```json
   "spring": {
     "application": {
       "name": "hello-world"
     }
   },
   "eureka": {
     "client": {
       "shouldFetchRegistry": true,
       "shouldRegisterWithEureka": true
     }
   }
   ```

1. Również w *appsettings.jsna*, Zmień poziom rejestrowania dla `Microsoft` kategorii z `Warning` na `Information` . Ta zmiana gwarantuje, że dzienniki będą generowane podczas wyświetlania dzienników przesyłania strumieniowego w późniejszym kroku.

   *appsettings.jsw* pliku wygląda teraz podobnie do poniższego przykładu:

   ```json
   {
     "Logging": {
       "LogLevel": {
         "Default": "Information",
         "Microsoft": "Information",
         "Microsoft.Hosting.Lifetime": "Information"
       }
     },
     "AllowedHosts": "*",
     "spring": {
       "application": {
         "name": "hello-world"
       }
     },
     "eureka": {
       "client": {
         "shouldFetchRegistry": true,
         "shouldRegisterWithEureka": true
       }
     }
   }
   ```
   
1. Dodaj zależności i `Zip` zadania do pliku *. csproj* :

   ```xml
   <ItemGroup>
     <PackageReference Include="Steeltoe.Discovery.ClientCore" Version="3.0.0" />
     <PackageReference Include="Microsoft.Azure.SpringCloud.Client" Version="2.0.0-preview.1" />
   </ItemGroup>
   <Target Name="Publish-Zip" AfterTargets="Publish">
       <ZipDirectory SourceDirectory="$(PublishDir)" DestinationFile="$(MSBuildProjectDirectory)/deploy.zip" Overwrite="true" />
   </Target>
   ```

   Pakiety są przeznaczone do odnajdowania usług steeltoe i biblioteki klienta chmury wiosennej na platformie Azure. `Zip`Zadanie jest przeznaczone do wdrożenia na platformie Azure. Po uruchomieniu `dotnet publish` polecenia generuje pliki binarne w folderze *publikowania* , a to zadanie Zips folder *publikacji* do pliku *zip* , który jest przekazywany do platformy Azure.

3. W pliku *program.cs* Dodaj `using` dyrektywę i kod, który używa biblioteki klienckiej chmury wiosnowej platformy Azure:

   ```csharp
   using Microsoft.Azure.SpringCloud.Client;
   ```

   ```csharp
   public static IHostBuilder CreateHostBuilder(string[] args) =>
               Host.CreateDefaultBuilder(args)
                   .UseAzureSpringCloudService()
                   .ConfigureWebHostDefaults(webBuilder =>
                   {
                       webBuilder.UseStartup<Startup>();
                   });
   ```

4. W pliku *Startup.cs* Dodaj `using` dyrektywę i kod, który korzysta z wykrywania usługi steeltoe na końcu `ConfigureServices` `Configure` metod i:

   ```csharp
   using Steeltoe.Discovery.Client;
   ```

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       // Template code not shown.

       services.AddDiscoveryClient(Configuration);
   }
   ```

   ```csharp
   public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
   {
       // Template code not shown.

       app.UseDiscoveryClient();
   }
   ```

1. Skompiluj projekt, aby upewnić się, że nie występują błędy kompilacji.

   ```dotnetcli
   dotnet build
   ```
 
## <a name="provision-a-service-instance"></a>Inicjowanie obsługi administracyjnej wystąpienia usługi

Poniższa procedura umożliwia utworzenie wystąpienia chmury wiosennej platformy Azure przy użyciu Azure Portal.

1. Otwórz witrynę [Azure Portal](https://ms.portal.azure.com/). 

1. W górnym polu wyszukiwania Wyszukaj *chmurę Azure wiosenną*.

1. Z wyników wybierz *chmurę ze sprężyną Azure* .

   ![Początek ikony ASC](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

1. Na stronie chmura ze sprężyną Azure wybierz pozycję **+ Dodaj**.

   ![Dodawanie ikony ASC](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

1. Wypełnij formularz na stronie **Tworzenie** chmury Azure wiosennej.  Należy uwzględnić następujące wytyczne:

   * **Subskrypcja**: wybierz subskrypcję, dla której chcesz naliczać opłaty za ten zasób.
   * **Grupa zasobów**: Utwórz nową grupę zasobów. Wprowadzona nazwa zostanie użyta w kolejnych krokach **\<resource group name\>** .
   * **Szczegóły/nazwa usługi**: Określ **\<service instance name\>** .  Nazwa musi mieć długość od 4 do 32 znaków i może zawierać tylko małe litery, cyfry i łączniki.  Pierwszy znak nazwy usługi musi być literą, a ostatni znak musi być literą lub cyfrą.
   * **Region**: Wybierz region wystąpienia usługi.

   ![Początek portalu ASC](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Wybierz pozycję **Przejrzyj i Utwórz**.

## <a name="build-and-deploy-the-app"></a>Kompilowanie i wdrażanie aplikacji

Poniższa procedura kompiluje i wdraża utworzony wcześniej projekt.

1. Upewnij się, że wiersz polecenia znajduje się w folderze projektu.

1. Uruchom następujące polecenie w celu skompilowania projektu, opublikowania plików binarnych i zapisania plików binarnych w pliku *zip* w folderze projektu.

   ```dotnetcorecli
   dotnet publish -c release -o ./publish
   ```

1. Utwórz aplikację w wystąpieniu chmury Azure wiosennej z przypisanym publicznym punktem końcowym. Użyj tej samej nazwy aplikacji "Hello-World", która została określona w *appsettings.jsna*.

   ```console
   az spring-cloud app create -n hello-world -s <service instance name> -g <resource group name> --is-public --runtime-version NetCore_31
   ```

1. Wdróż plik *. zip* w aplikacji.

   ```azurecli
   az spring-cloud app deploy -n hello-world -s <service instance name> -g <resource group name> --runtime-version NetCore_31 --main-entry hello-world.dll --artifact-path ./deploy.zip
   ```

   `--main-entry`Opcja identyfikuje plik *dll* , który zawiera punkt wejścia aplikacji. Po przekazanie pliku *zip* przez usługę wyodrębnia wszystkie pliki i foldery i próbuje wykonać punkt wejścia w pliku *dll* określonym przez `--main-entry` .

   Wdrożenie aplikacji może zająć kilka minut. Aby upewnić się, że został wdrożony, przejdź do bloku **aplikacje** w Azure Portal.

## <a name="test-the-app"></a>Testowanie aplikacji

Po zakończeniu wdrażania uzyskaj dostęp do aplikacji pod następującym adresem URL:

```http
https://<service instance name>-hello-world.azuremicroservices.io/weatherforecast
```

Aplikacja zwraca dane JSON podobne do poniższego przykładu:

```json
[{"date":"2020-09-08T21:01:50.0198835+00:00","temperatureC":14,"temperatureF":57,"summary":"Bracing"},{"date":"2020-09-09T21:01:50.0200697+00:00","temperatureC":-14,"temperatureF":7,"summary":"Bracing"},{"date":"2020-09-10T21:01:50.0200715+00:00","temperatureC":27,"temperatureF":80,"summary":"Freezing"},{"date":"2020-09-11T21:01:50.0200717+00:00","temperatureC":18,"temperatureF":64,"summary":"Chilly"},{"date":"2020-09-12T21:01:50.0200719+00:00","temperatureC":16,"temperatureF":60,"summary":"Chilly"}]
```

## <a name="stream-logs-in-real-time"></a>Przesyłanie strumieniowe dzienników w czasie rzeczywistym

Użyj poniższego polecenia, aby uzyskać dzienniki czasu rzeczywistego z aplikacji.

```azurecli
az spring-cloud app logs -n hello-world -s <service instance name> -g <resource group name> --lines 100 -f
```

Dzienniki są wyświetlane w danych wyjściowych:

```output
[Azure Spring Cloud] The following environment variables are loaded:
2020-09-08 20:58:42,432 INFO supervisord started with pid 1
2020-09-08 20:58:43,435 INFO spawned: 'event-gather_00' with pid 9
2020-09-08 20:58:43,436 INFO spawned: 'dotnet-app_00' with pid 10
2020-09-08 20:58:43 [Warning] No managed processes are running. Wait for 30 seconds...
2020-09-08 20:58:44,843 INFO success: event-gather_00 entered RUNNING state, process has stayed up for > than 1 seconds (startsecs)
2020-09-08 20:58:44,843 INFO success: dotnet-app_00 entered RUNNING state, process has stayed up for > than 1 seconds (startsecs)
←[40m←[32minfo←[39m←[22m←[49m: Steeltoe.Discovery.Eureka.DiscoveryClient[0]
      Starting HeartBeat
info: Microsoft.Hosting.Lifetime[0]
      Now listening on: http://[::]:1025
info: Microsoft.Hosting.Lifetime[0]
      Application started. Press Ctrl+C to shut down.
info: Microsoft.Hosting.Lifetime[0]
      Hosting environment: Production
info: Microsoft.Hosting.Lifetime[0]
      Content root path: /netcorepublish/6e4db42a-b160-4b83-a771-c91adec18c60
2020-09-08 21:00:13 [Information] [10] Start listening...
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/1.1 GET http://asc-svc-hello-world.azuremicroservices.io/weatherforecast
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'hello_world.Controllers.WeatherForecastController.Get (hello-world)'
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[3]
      Route matched with {action = "Get", controller = "WeatherForecast"}. Executing controller action with signature System.Collections.Generic.IEnumerable`1[hello_world.WeatherForecast] Get() on controller hello_world.Controllers.WeatherForecastController (hello-world).
info: Microsoft.AspNetCore.Mvc.Infrastructure.ObjectResultExecutor[1]
      Executing ObjectResult, writing value of type 'hello_world.WeatherForecast[]'.
info: Microsoft.AspNetCore.Mvc.Infrastructure.ControllerActionInvoker[2]
      Executed action hello_world.Controllers.WeatherForecastController.Get (hello-world) in 1.8902ms
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'hello_world.Controllers.WeatherForecastController.Get (hello-world)'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 4.2591ms 200 application/json; charset=utf-8
```

> [!TIP]
> Użyj `az spring-cloud app logs -h` , aby poznać dodatkowe parametry i funkcje strumienia dzienników.

Aby uzyskać zaawansowane funkcje usługi log Analytics, odwiedź kartę **dzienniki** w menu na [Azure Portal](https://portal.azure.com/). Dzienniki mają opóźnienie kilka minut.
[![Dzienniki analizy ](media/spring-cloud-quickstart-java/logs-analytics.png)](media/spring-cloud-quickstart-java/logs-analytics.png#lightbox)
::: zone-end

::: zone pivot="programming-language-java"
Ten przewodnik Szybki Start wyjaśnia, jak wdrożyć prostą aplikację mikrousług w chmurze Azure wiosną do uruchamiania na platformie Azure. 

Kod aplikacji używany w tym samouczku to prosta aplikacja skompilowana ze sprężyną Initializr. Po ukończeniu tego przykładu aplikacja będzie dostępna w trybie online i może być zarządzana za pośrednictwem Azure Portal.

Ten przewodnik Szybki Start wyjaśnia, jak:

> [!div class="checklist"]
> * Generowanie podstawowego projektu chmurowego
> * Inicjowanie obsługi administracyjnej wystąpienia usługi
> * Kompilowanie i wdrażanie aplikacji za pomocą publicznego punktu końcowego
> * Przesyłanie strumieniowe dzienników w czasie rzeczywistym

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start:

* [Zainstaluj program JDK 8](/java/azure/jdk/?preserve-view=true&view=azure-java-stable)
* [Zarejestruj się w celu uzyskania subskrypcji platformy Azure](https://azure.microsoft.com/free/)
* Obowiązkowe [Zainstaluj interfejs wiersza polecenia platformy Azure w wersji 2.0.67 lub nowszej](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest) oraz rozszerzenie chmury wiosennej platformy Azure z poleceniem: `az extension add --name spring-cloud`
* Obowiązkowe [Zainstaluj Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) i [Zaloguj się](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="generate-a-spring-cloud-project"></a>Generowanie projektu wiosennej chmury

Zacznij od [Initializr sprężyny](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client) , aby wygenerować przykładowy projekt z zalecanymi zależnościami dla chmury wiosennej platformy Azure. Na poniższej ilustracji przedstawiono Initializr skonfigurowany dla tego przykładowego projektu.
```url
https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client
```

  ![Strona Initializr](media/spring-cloud-quickstart-java/initializr-page.png)

1. Po ustawieniu wszystkich zależności kliknij przycisk **Generuj** . Pobierz i Rozpakuj pakiet, a następnie utwórz Kontroler sieci Web dla prostej aplikacji sieci Web, dodając `src/main/java/com/example/hellospring/HelloController.java` następujący sposób:

    ```java
    package com.example.hellospring;
    
    import org.springframework.web.bind.annotation.RestController;
    import org.springframework.web.bind.annotation.RequestMapping;
    
    @RestController
    public class HelloController {
    
        @RequestMapping("/")
        public String index() {
            return "Greetings from Azure Spring Cloud!";
        }
    
    }
    ```
## <a name="provision-an-instance-of-azure-spring-cloud"></a>Udostępnianie wystąpienia chmury wiosennej platformy Azure

Poniższa procedura umożliwia utworzenie wystąpienia chmury wiosennej platformy Azure przy użyciu Azure Portal.

1. Na nowej karcie Otwórz [Azure Portal](https://ms.portal.azure.com/). 

2. W górnym polu wyszukiwania Wyszukaj *chmurę Azure wiosenną*.

3. Z wyników wybierz *chmurę ze sprężyną Azure* .

    ![Początek ikony ASC](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Na stronie chmura Wiosenna Azure kliknij pozycję **+ Dodaj**.

    ![Dodawanie ikony ASC](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Wypełnij formularz na stronie **Tworzenie** chmury Azure wiosennej.  Należy uwzględnić następujące wytyczne:
    - **Subskrypcja**: wybierz subskrypcję, dla której chcesz naliczać opłaty za ten zasób.
    - **Grupa zasobów**: najlepszym rozwiązaniem jest utworzenie nowych grup zasobów dla nowych zasobów. Ta procedura zostanie użyta w kolejnych krokach **\<resource group name\>** .
    - **Szczegóły/nazwa usługi**: Określ **\<service instance name\>** .  Nazwa musi mieć długość od 4 do 32 znaków i może zawierać tylko małe litery, cyfry i łączniki.  Pierwszy znak nazwy usługi musi być literą, a ostatni znak musi być literą lub cyfrą.
    - **Lokalizacja**: Wybierz region wystąpienia usługi.

    ![Początek portalu ASC](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Kliknij pozycję **Przejrzyj i utwórz**.

## <a name="build-and-deploy-the-app"></a>Kompilowanie i wdrażanie aplikacji
    
#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/Azure-CLI)
Poniższa procedura umożliwia kompilację i wdrożenie aplikacji przy użyciu interfejsu wiersza polecenia platformy Azure. Wykonaj następujące polecenie w katalogu głównym projektu.

1. Kompiluj projekt przy użyciu Maven:

    ```console
    mvn clean package -DskipTests
    ```

1. (Jeśli nie została jeszcze zainstalowana) Zainstaluj rozszerzenie chmury wiosennej platformy Azure dla interfejsu wiersza polecenia platformy Azure:

    ```azurecli
    az extension add --name spring-cloud
    ```
    
1. Utwórz aplikację z przypisanym publicznym punktem końcowym:

    ```azurecli
    az spring-cloud app create -n hellospring -s <service instance name> -g <resource group name> --is-public
    ```

1. Wdróż plik JAR dla aplikacji ( `target\hellospring-0.0.1-SNAPSHOT.jar` w systemie Windows):

    ```azurecli
    az spring-cloud app deploy -n hellospring -s <service instance name> -g <resource group name> --jar-path <jar file path>
    ```
    
1. Wdrożenie aplikacji może zająć kilka minut. Aby upewnić się, że został wdrożony, przejdź do bloku **aplikacje** w Azure Portal. Powinien zostać wyświetlony stan aplikacji.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

Poniższa procedura używa wtyczki IntelliJ dla chmury wiosennej platformy Azure do wdrożenia przykładowej aplikacji na POMYSŁie IntelliJ.  

### <a name="import-project"></a>Importowanie projektu

1. Otwórz okno dialogowe **powitalne** IntelliJ, a następnie wybierz pozycję **Importuj projekt** , aby otworzyć Kreatora importu.
1. Wybierz `hellospring` folder.

    ![Importuj projekt](media/spring-cloud-quickstart-java/intellij-new-project.png)

### <a name="deploy-the-app"></a>Wdrażanie aplikacji
Aby wdrożyć platformę Azure, musisz zalogować się przy użyciu konta platformy Azure i wybrać subskrypcję.  Aby uzyskać szczegółowe informacje dotyczące logowania, zobacz [Instalowanie i logowanie](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. Kliknij prawym przyciskiem myszy projekt w Eksploratorze projektów IntelliJ, a następnie wybierz pozycję **Azure**  ->  **Deploy w chmurze Azure wiosennej**.

    [![Wdrażanie na platformie Azure 1 ](media/spring-cloud-quickstart-java/intellij-deploy-azure-1.png)](media/spring-cloud-quickstart-java/intellij-deploy-azure-1.png#lightbox)

1. Zaakceptuj nazwę aplikacji w polu **Nazwa** . **Nazwa** odwołuje się do konfiguracji, a nie nazwy aplikacji. Użytkownicy zazwyczaj nie muszą go zmieniać.
1. W polu tekstowym **artefaktu** wybierz pozycję *hellospring-0.0.1-Snapshot. jar*.
1. W polu tekstowym **subskrypcja** Sprawdź swoją subskrypcję.
1. W polu tekstowym **chmura Wiosenna** wybierz wystąpienie chmury Azure wiosennej, która została utworzona w ramach aprowizacji [wystąpienia chmury wiosennej platformy Azure](./spring-cloud-quickstart-provision-service-instance.md).
1. Ustaw **publiczny punkt końcowy** , aby *włączyć*.
1. W polu tekstowym **aplikacja:** wybierz pozycję **Utwórz aplikację..**..
1. Wprowadź *hellospring*, a następnie kliknij przycisk **OK**.

    [![Wdróż na platformie Azure ](media/spring-cloud-quickstart-java/intellij-deploy-to-azure.png)](media/spring-cloud-quickstart-java/intellij-deploy-to-azure.png#lightbox)

1. Rozpocznij wdrażanie, klikając przycisk **Uruchom** w dolnej części okna dialogowego **wdrażanie aplikacji w chmurze Azure ze sprężyną** . Wtyczka uruchomi polecenie `mvn package` w `hellospring` aplikacji i Wdróż plik JAR wygenerowany przez `package` polecenie.
---

Po zakończeniu wdrażania możesz uzyskać dostęp do aplikacji w witrynie `https://<service instance name>-hellospring.azuremicroservices.io/` .

  [![Dostęp do aplikacji z przeglądarki ](media/spring-cloud-quickstart-java/access-app-browser.png)](media/spring-cloud-quickstart-java/access-app-browser.png#lightbox)

## <a name="streaming-logs-in-real-time"></a>Dzienniki przesyłania strumieniowego w czasie rzeczywistym

#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/Azure-CLI)

Użyj poniższego polecenia, aby uzyskać dzienniki czasu rzeczywistego z aplikacji.

```azurecli
az spring-cloud app logs -n hellospring -s <service instance name> -g <resource group name> --lines 100 -f

```
Dzienniki są wyświetlane w wynikach:

[![Dzienniki ](media/spring-cloud-quickstart-java/streaming-logs.png) przesyłania strumieniowego](media/spring-cloud-quickstart-java/streaming-logs.png#lightbox)

>[!TIP]
> Użyj `az spring-cloud app logs -h` , aby poznać dodatkowe parametry i funkcje strumienia dzienników.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

1. Wybierz opcję **Eksplorator platformy Azure**, a następnie pozycję **chmura Wiosenna**.
1. Kliknij prawym przyciskiem myszy działającą aplikację.
1. Z listy rozwijanej wybierz pozycję **dzienniki przesyłania strumieniowego** .
1. Wybierz wystąpienie.

    [![Wybieranie dzienników ](media/spring-cloud-quickstart-java/intellij-get-streaming-logs.png) przesyłania strumieniowego](media/spring-cloud-quickstart-java/intellij-get-streaming-logs.png)

1. Dziennik przesyłania strumieniowego będzie widoczny w oknie danych wyjściowych.

    [![Dane wyjściowe ](media/spring-cloud-quickstart-java/intellij-streaming-logs-output.png) dziennika przesyłania strumieniowego](media/spring-cloud-quickstart-java/intellij-streaming-logs-output.png)
---

Aby uzyskać zaawansowane funkcje analizy dzienników, odwiedź kartę **dzienniki** w menu na [Azure Portal](https://portal.azure.com/). Dzienniki mają opóźnienie kilka minut.

[![Dzienniki analizy ](media/spring-cloud-quickstart-java/logs-analytics.png)](media/spring-cloud-quickstart-java/logs-analytics.png#lightbox)
::: zone-end

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W poprzednich krokach utworzono zasoby platformy Azure, które będą nadal naliczane opłaty, gdy pozostaną w ramach subskrypcji. Jeśli nie chcesz potrzebować tych zasobów w przyszłości, Usuń grupę zasobów z portalu lub uruchamiając następujące polecenie w interfejsie wiersza polecenia platformy Azure:

```azurecli
az group delete --name <your resource group name; for example: hellospring-1558400876966-rg> --yes
```

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Generowanie podstawowego projektu Azure wiosny Cloud
> * Inicjowanie obsługi administracyjnej wystąpienia usługi
> * Kompilowanie i wdrażanie aplikacji za pomocą publicznego punktu końcowego
> * Przesyłanie strumieniowe dzienników w czasie rzeczywistym

Aby dowiedzieć się, jak korzystać z większej liczby funkcji sprężyny platformy Azure, przejdź do serii szybkiego startu, która wdraża przykładową aplikację w chmurze Azure wiosną:

> [!div class="nextstepaction"]
> [Kompiluj i uruchamiaj mikrousługi](spring-cloud-quickstart-sample-app-introduction.md)

Więcej przykładów można znaleźć w witrynie GitHub: [przykłady chmur usługi Azure wiosennej](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples).