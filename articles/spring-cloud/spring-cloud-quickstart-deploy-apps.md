---
title: Szybki Start — Kompilowanie i wdrażanie aplikacji w chmurze Azure wiosennej
description: Opisuje wdrażanie aplikacji w chmurze Azure wiosennej.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: d368c938e805164b70c7d3a22f9229b5efd89dde
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/14/2020
ms.locfileid: "92045405"
---
# <a name="quickstart-build-and-deploy-apps-to-azure-spring-cloud"></a>Szybki Start: kompilowanie i wdrażanie aplikacji w chmurze Azure wiosennej

::: zone pivot="programming-language-csharp"
W tym przewodniku szybki start utworzysz i wdrażasz aplikacje mikrousług w chmurze Azure sprężynowej przy użyciu interfejsu wiersza polecenia platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

* Wykonaj poprzednie Przewodniki Szybki Start w tej serii:

  * [Zainicjuj obsługę usługi w chmurze Azure wiosennej](spring-cloud-quickstart-provision-service-instance.md).
  * [Skonfiguruj serwer konfiguracji chmury z platformą Azure](spring-cloud-quickstart-setup-config-server.md).

## <a name="download-the-sample-app"></a>Pobieranie przykładowej aplikacji

Jeśli używasz Azure Cloud Shell do tego momentu, przełącz się do lokalnego wiersza polecenia, aby wykonać następujące czynności.

1. Utwórz nowy folder i Sklonuj repozytorium przykładowej aplikacji.

   ```console
   mkdir source-code
   ```

   ```console
   cd source-code
   ```

   ```console
   git clone https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples
   ```

1. Przejdź do katalogu repozytorium.

   ```console
   cd Azure-Spring-Cloud-Samples
   ```

## <a name="deploy-planetweatherprovider"></a>Wdróż PlanetWeatherProvider

1. Utwórz aplikację dla projektu PlanetWeatherProvider w wystąpieniu chmury wiosennej platformy Azure.

   ```azurecli
   az spring-cloud app create --name planet-weather-provider --runtime-version NetCore_31
   ```

   Aby włączyć automatyczną rejestrację usługi, dana aplikacja ma taką samą nazwę jak wartość `spring.application.name` w *appsettings.js* projektu w pliku:

   ```json
   "spring": {
     "application": {
       "name": "planet-weather-provider"
     }
   }
   ```

   Uruchomienie tego polecenia może potrwać kilka minut.

1. Zmień katalog na `PlanetWeatherProvider` folder projektu.

   ```console
   cd steeltoe-sample/src/planet-weather-provider
   ```

1. Utwórz pliki binarne i plik *. zip* do wdrożenia.

   ```console
   dotnet publish -c release -o ./publish
   ```

   > [!TIP]
   > Plik projektu zawiera następujący kod XML do spakowania plików binarnych w pliku *zip* po zapisaniu ich w folderze *./publish* :
   >
   > ```xml
   > <Target Name="Publish-Zip" AfterTargets="Publish">
   >   <ZipDirectory SourceDirectory="$(PublishDir)" DestinationFile="$(MSBuildProjectDirectory)/publish-deploy-planet.zip" Overwrite="true" />
   > </Target>
   > ```

1. Wdróż na platformie Azure.

   Upewnij się, że wiersz polecenia znajduje się w folderze projektu przed uruchomieniem następującego polecenia.

   ```console
   az spring-cloud app deploy -n planet-weather-provider --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll --artifact-path ./publish-deploy-planet.zip
   ```

   `--main-entry`Opcja określa ścieżkę względną z folderu głównego pliku *. zip* do pliku *dll* , który zawiera punkt wejścia aplikacji. Gdy usługa przekaże plik *zip* , wyodrębnia wszystkie pliki i foldery i próbuje wykonać punkt wejścia w określonym pliku *dll* .

   Uruchomienie tego polecenia może potrwać kilka minut.

## <a name="deploy-solarsystemweather"></a>Wdróż SolarSystemWeather

1. Utwórz kolejną aplikację w wystąpieniu usługi Azure wiosennej chmury, tym razem dla projektu SolarSystemWeather:

   ```azurecli
   az spring-cloud app create --name solar-system-weather --runtime-version NetCore_31
   ```

   `solar-system-weather` jest nazwą określoną w `SolarSystemWeather` *appsettings.jsprojektu na* pliku.

   Uruchomienie tego polecenia może potrwać kilka minut.

1. Zmień katalog na `SolarSystemWeather` projekt.

   ```console
   cd ../solar-system-weather
   ```

1. Utwórz pliki binarne i pliki *zip* , które mają zostać wdrożone.

   ```console
   dotnet publish -c release -o ./publish
   ```

1. Wdróż na platformie Azure.

   ```console
   az spring-cloud app deploy -n solar-system-weather --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll --artifact-path ./publish-deploy-solar.zip
   ```
   
   Uruchomienie tego polecenia może potrwać kilka minut.

## <a name="assign-public-endpoint"></a>Przypisz publiczny punkt końcowy

Aby przetestować aplikację, Wyślij żądanie HTTP GET do `solar-system-weather` aplikacji z przeglądarki.  Aby to zrobić, potrzebny jest publiczny punkt końcowy dla żądania.

1. Aby przypisać punkt końcowy, uruchom następujące polecenie.

   ```azurecli
   az spring-cloud app update -n solar-system-weather --is-public true
   ```

1. Aby uzyskać adres URL punktu końcowego, uruchom następujące polecenie.

   W systemie Windows:

   ```azurecli
   az spring-cloud app show -n solar-system-weather -o table
   ```

   W systemie Linux:

   ```azurecli
   az spring-cloud app show --name solar-system-weather | grep url
   ```

## <a name="test-the-application"></a>Testowanie aplikacji

Wyślij żądanie GET do `solar-system-weather` aplikacji. W przeglądarce przejdź do publicznego adresu URL z `/weatherforecast` dołączonym do końca. Na przykład:

```
https://servicename-solar-system-weather.azuremicroservices.io/weatherforecast
```

Dane wyjściowe są w formacie JSON:

```json
[{"Key":"Mercury","Value":"very warm"},{"Key":"Venus","Value":"quite unpleasant"},{"Key":"Mars","Value":"very cool"},{"Key":"Saturn","Value":"a little bit sandy"}]
```

Ta odpowiedź pokazuje, że obie aplikacje mikrousług działają. `SolarSystemWeather`Aplikacja zwraca dane pobierane z `PlanetWeatherProvider` aplikacji.
::: zone-end

::: zone pivot="programming-language-java"
W tym dokumencie wyjaśniono, jak tworzyć i wdrażać aplikacje mikrousług w chmurze Azure sprężynowej przy użyciu:
* Interfejs wiersza polecenia platformy Azure
* Wtyczka dla programu Maven
* IntelliJ

Przed wdrożeniem przy użyciu interfejsu wiersza polecenia platformy Azure lub Maven należy wykonać przykłady, które [inicjują obsługę wystąpienia chmury wiosennej platformy Azure](spring-cloud-quickstart-provision-service-instance.md) i [skonfigurować serwer konfiguracji](spring-cloud-quickstart-setup-config-server.md).

## <a name="prerequisites"></a>Wymagania wstępne

* [Zainstaluj program JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true)
* [Zarejestruj się w celu uzyskania subskrypcji platformy Azure](https://azure.microsoft.com/free/)
* Obowiązkowe [Zainstaluj interfejs wiersza polecenia platformy Azure w wersji 2.0.67 lub nowszej](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) i zainstaluj rozszerzenie chmury wiosennej Azure z poleceniem: `az extension add --name spring-cloud`
* Obowiązkowe [Zainstaluj Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) i [Zaloguj się](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="deployment-procedures"></a>Procedury wdrażania

#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/Azure-CLI)

### <a name="build-the-microservices-applications-locally"></a>Lokalne Tworzenie aplikacji mikrousług

1. Sklonuj repozytorium przykładowej aplikacji na koncie w chmurze platformy Azure.  

    ```azurecli
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Zmień katalog i skompiluj projekt.

    ```azurecli
    cd piggymetrics
    mvn clean package -DskipTests
    ```

Kompilowanie projektu trwa około 5 minut. Po ukończeniu należy mieć pojedyncze pliki JAR dla każdej usługi w odpowiednich folderach.

### <a name="create-and-deploy-the-apps"></a>Tworzenie i wdrażanie aplikacji

1. Ustaw domyślną nazwę grupy zasobów i nazwę klastra przy użyciu następujących poleceń:

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

1. Tworzenie mikrousług chmurowych w chmurze platformy Azure przy użyciu plików JAR utworzonych w poprzednim kroku. Utworzysz trzy aplikacje: **brama**, **uwierzytelnianie-usługa**i **konto usługi**.

    ```azurecli
    az spring-cloud app create --name gateway
    az spring-cloud app create --name auth-service
    az spring-cloud app create --name account-service
    ```

1. Musimy wdrożyć aplikacje utworzone w poprzednim kroku na platformie Azure. Użyj następujących poleceń, aby wdrożyć wszystkie trzy aplikacje:

    ```azurecli
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

### <a name="assign-public-endpoint-to-gateway"></a>Przypisz publiczny punkt końcowy do bramy

Potrzebujemy sposobu na dostęp do aplikacji za pośrednictwem przeglądarki sieci Web. Nasza aplikacja bramy wymaga publicznego punktu końcowego.

1. Przypisz punkt końcowy przy użyciu następującego polecenia:

    ```azurecli
    az spring-cloud app update -n gateway --is-public true
    ```

2. Zbadaj swój publiczny adres IP aplikacji **bramy** , aby można było sprawdzić, czy aplikacja jest uruchomiona:

    ```azurecli
    az spring-cloud app show --name gateway --query properties.url
    ```

#### <a name="maven"></a>[Maven](#tab/Maven)

### <a name="clone-and-build-the-sample-application-repository"></a>Klonowanie i kompilowanie przykładowego repozytorium aplikacji

1. Sklonuj repozytorium git, uruchamiając następujące polecenie:

    ```
    git clone https://github.com/Azure-Samples/piggymetrics
    ```
  
1. Zmień katalog i skompiluj projekt, uruchamiając następujące polecenie:

    ```
    cd piggymetrics
    mvn clean package -DskipTests
    ```

### <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>Generuj konfiguracje i wdrażaj je w chmurze Azure wiosennej

1. Wygeneruj konfiguracje, uruchamiając następujące polecenie w folderze głównym PiggyMetrics zawierającym pliku pom nadrzędny. Jeśli użytkownik jest już zalogowany za pomocą interfejsu wiersza polecenia platformy Azure, polecenie automatycznie pobierze poświadczenia. W przeciwnym razie zostanie zalogowanie się przy użyciu instrukcji monitu. Aby uzyskać więcej informacji, zobacz naszą [stronę typu wiki](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication).

    ```
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.3.0:config
    ```
    
    Zostanie wyświetlony monit o wybranie:
    * **Moduły:** Wybierz opcję `gateway` , `auth-service` , i `account-service` .
    * **Subskrypcja:** To jest subskrypcja użyta do utworzenia wystąpienia chmury wiosennej na platformie Azure.
    * **Wystąpienie usługi:** To jest nazwa Twojego wystąpienia chmury Azure wiosennej.
    * **Publiczny punkt końcowy:** Na liście podanych projektów wprowadź liczbę, która odpowiada `gateway` .  Daje to dostęp publiczny.

1. PLIKU pom zawiera teraz zależności i konfiguracje wtyczki. Wdróż aplikacje za pomocą następującego polecenia. 

    ```
    mvn azure-spring-cloud:deploy
    ```

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

### <a name="import-sample-project-in-intellij"></a>Importowanie przykładowego projektu w IntelliJ

1. Pobierz i rozpakuj repozytorium źródłowe dla tego samouczka lub Sklonuj je za pomocą narzędzia Git: `git clone https://github.com/Azure-Samples/piggymetrics` 

1. Otwórz okno dialogowe **Witamy** w IntelliJ, wybierz pozycję **Importuj projekt** , aby otworzyć Kreatora importu.

1. Wybierz `piggymetric` folder.

    ![Importuj projekt](media/spring-cloud-intellij-howto/revision-import-project-1.png)

### <a name="deploy-gateway-app-to-azure-spring-cloud"></a>Wdrażanie aplikacji bramy w chmurze Azure wiosennej
Aby można było wdrożyć platformę Azure, musisz zalogować się przy użyciu konta platformy Azure przy użyciu Azure Toolkit for IntelliJ i wybrać subskrypcję. Aby uzyskać szczegółowe informacje dotyczące logowania, zobacz [Instalowanie i logowanie](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. Kliknij prawym przyciskiem myszy projekt w Eksploratorze projektów IntelliJ, a następnie wybierz pozycję **Azure**  ->  **Deploy w chmurze Azure wiosennej**.

    ![Wdrażanie na platformie Azure 1](media/spring-cloud-intellij-howto/revision-deploy-to-azure-1.png)

1. W polu **Nazwa** Dołącz *: Brama* do istniejącej **nazwy**.
1. W polu tekstowym **artefaktu** wybierz pozycję *com. piggymetrics: Gateway: 1.0-Snapshot*.
1. W polu tekstowym **subskrypcja** Sprawdź swoją subskrypcję.
1. W polu tekstowym **chmura Wiosenna** wybierz wystąpienie chmury Azure wiosennej, która została utworzona w ramach aprowizacji [wystąpienia chmury wiosennej platformy Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-provision-service-instance).
1. Ustaw **publiczny punkt końcowy** , aby *włączyć*.
1. W polu tekstowym **aplikacja:** wybierz pozycję **Utwórz aplikację..**..
1. Wprowadź *bramę*, a następnie kliknij przycisk **OK**.

    ![Wdróż na platformie Azure](media/spring-cloud-intellij-howto/revision-deploy-to-azure-2.png)

1. W sekcji **przed uruchomieniem** okna dialogowego kliknij dwukrotnie pozycję *Uruchom Maven cel*.
1. W polu tekstowym **katalog roboczy** przejdź do folderu *piggymetrics/Gateway* .
1. W polu tekstowym **wiersz polecenia** wprowadź polecenie *Package-DskipTests*. Kliknij przycisk **OK**.
1. Rozpocznij wdrażanie, klikając przycisk **Uruchom** w dolnej części okna dialogowego **wdrażanie aplikacji w chmurze Azure ze sprężyną** . Wtyczka uruchomi polecenie `mvn package` w `gateway` aplikacji i Wdróż plik JAR wygenerowany przez `package` polecenie.

### <a name="deploy-auth-service-and-account-service-apps-to-azure-spring-cloud"></a>Wdrażanie aplikacji uwierzytelniania-usługi i konta usługi w chmurze platformy Azure
Możesz powtórzyć powyższe kroki, aby wdrożyć `auth-service` usługi i `account-service` aplikacje w chmurze Azure wiosną:

1. Zmodyfikuj **nazwę** i **artefakt** , aby zidentyfikować `auth-service` aplikację.
1. W polu tekstowym **aplikacja:** wybierz pozycję **Utwórz aplikację..** ., aby utworzyć `auth-service` aplikacje.
1. Sprawdź, czy opcja **publiczny punkt końcowy** jest ustawiona na wartość *Disabled*.
1. W sekcji **przed uruchomieniem** okna dialogowego Przełącz **katalog roboczy** do folderu *piggymetrics/auth-Service* .
1. Rozpocznij wdrażanie, klikając przycisk **Uruchom** w dolnej części okna dialogowego **wdrażanie aplikacji w chmurze Azure ze sprężyną** . 
1. Powtórz te procedury, aby skonfigurować i wdrożyć `account-service` .
---

Przejdź do adresu URL podanego w danych wyjściowych, aby uzyskać dostęp do aplikacji PiggyMetrics. Na przykład: `https://<service instance name>-gateway.azuremicroservices.io`

![Dostęp do PiggyMetrics](media/spring-cloud-quickstart-launch-app-cli/launch-app.png)

Możesz również przejść do Azure Portal, aby znaleźć adres URL. 
1. Przejdź do usługi
2. Wybierz **aplikacje**
3. Wybierz **bramę**

    ![Przejdź do aplikacji](media/spring-cloud-quickstart-launch-app-cli/navigate-app1.png)
    
4. Znajdź adres URL w **bramie | Strona przeglądu**

    ![Nawigacja po drugiej aplikacji](media/spring-cloud-quickstart-launch-app-cli/navigate-app2-url.png)

::: zone-end

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono zasoby platformy Azure, które będą nadal naliczane opłaty, jeśli pozostaną w ramach subskrypcji. Jeśli nie planujesz kontynuować pracy z następnym przewodnikiem Szybki Start, zobacz [Oczyszczanie zasobów](spring-cloud-quickstart-logs-metrics-tracing.md#clean-up-resources). W przeciwnym razie przejdź do następnego przewodnika Szybki Start:

> [!div class="nextstepaction"]
> [Dzienniki, metryki i śledzenie](spring-cloud-quickstart-logs-metrics-tracing.md)
