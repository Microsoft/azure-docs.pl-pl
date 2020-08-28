---
title: Szybki Start — wdrażanie pierwszej aplikacji w chmurze Azure wiosny
description: W tym przewodniku szybki start wdrożono aplikację Hello w chmurze ze sprężyną w chmurze sieci platformy Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: e4813f5c8c156fdf381a55ae450cf0ee35cedfa7
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89048209"
---
# <a name="quickstart-deploy-your-first-azure-spring-cloud-application"></a>Szybki Start: wdrażanie pierwszej aplikacji w chmurze Azure wiosny

Ten przewodnik Szybki Start wyjaśnia, jak wdrożyć prostą aplikację mikrousług w chmurze Azure wiosną do uruchamiania na platformie Azure. 

Kod aplikacji używany w tym samouczku to prosta aplikacja skompilowana ze sprężyną Initializr. Po ukończeniu tego przykładu aplikacja będzie dostępna w trybie online i może być zarządzana za pośrednictwem Azure Portal.

Ten przewodnik Szybki Start wyjaśnia, jak:

> [!div class="checklist"]
> * Generowanie podstawowego projektu chmurowego
> * Inicjowanie obsługi administracyjnej wystąpienia usługi
> * Kompilowanie i wdrażanie aplikacji za pomocą publicznego punktu końcowego
> * Rejestrowanie strumieni w czasie rzeczywistym

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki start:

* [Zainstaluj program JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Zarejestruj się w celu uzyskania subskrypcji platformy Azure](https://azure.microsoft.com/free/)
* Obowiązkowe [Zainstaluj interfejs wiersza polecenia platformy Azure w wersji 2.0.67 lub nowszej](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) oraz rozszerzenie chmury wiosennej platformy Azure z poleceniem: `az extension add --name spring-cloud`
* Obowiązkowe [Zainstaluj Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) i [Zaloguj się](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="generate-a-spring-cloud-hello-project"></a>Generowanie projektu Hello w chmurze wiosny

Zacznij od [Initializr sprężyny](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.3.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin) , aby wygenerować przykładowy projekt z zalecanymi zależnościami dla chmury wiosennej platformy Azure. Na poniższej ilustracji przedstawiono Initializr skonfigurowany dla tego przykładowego projektu.
```url
https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.3.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin
```

  ![Strona Initializr](media/spring-cloud-quickstart-java/initializr-page.png)

1. Po ustawieniu wszystkich zależności kliknij przycisk **Generuj** . Pobierz i Rozpakuj pakiet, a następnie Dodaj następujący zależność do `pom.xml` pliku aplikacji.

    ```xml
        <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>spring-cloud-starter-azure-spring-cloud-client</artifactId>
            <version>2.3.0</version>
        </dependency>
    ```

1. Utwórz Kontroler sieci Web dla prostej aplikacji sieci Web, dodając `src/main/java/com/example/hellospring/HelloController.java` następujący sposób:

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

5. Wypełnij formularz na stronie **Tworzenie** chmury Azure wiosennej.  Należy wziąć pod uwagę następujące wytyczne:
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

1. Wdróż plik JAR dla aplikacji:

    ```azurecli
    az spring-cloud app deploy -n hellospring -s <service instance name> -g <resource group name> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar
    ```
    
1. Wdrożenie aplikacji może zająć kilka minut. Aby upewnić się, że został wdrożony, przejdź do bloku **aplikacje** w Azure Portal. Powinien zostać wyświetlony stan aplikacji.

#### <a name="intellij"></a>[IntelliJ](#tab/IntelliJ)

Poniższa procedura używa wtyczki IntelliJ dla chmury wiosennej platformy Azure do wdrożenia przykładowej aplikacji na POMYSŁie IntelliJ.  

### <a name="import-hello-project"></a>Importuj projekt Hello

1. Otwórz okno dialogowe **powitalne** IntelliJ, a następnie wybierz pozycję **Importuj projekt** , aby otworzyć Kreatora importu.
1. Wybierz `hellospring` folder.

    ![Importuj projekt](media/spring-cloud-quickstart-java/intellij-new-project.png)

### <a name="deploy-the-app"></a>Wdrażanie aplikacji
Aby wdrożyć platformę Azure, musisz zalogować się przy użyciu konta platformy Azure i wybrać subskrypcję.  Aby uzyskać szczegółowe informacje dotyczące logowania, zobacz [Instalowanie i logowanie](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in).

1. Kliknij prawym przyciskiem myszy projekt w Eksploratorze projektów IntelliJ, a następnie wybierz pozycję **Azure**  ->  **Deploy w chmurze Azure wiosennej**.

    ![Wdrażanie na platformie Azure 1](media/spring-cloud-quickstart-java/intellij-deploy-azure.png)

1. Zaakceptuj nazwę aplikacji w polu **Nazwa** . **Nazwa** odwołuje się do konfiguracji, a nie nazwy aplikacji. Użytkownicy zazwyczaj nie muszą go zmieniać.
1. W polu tekstowym **artefaktu** wybierz pozycję *hellospring-0.0.1-Snapshot. jar*.
1. W polu tekstowym **subskrypcja** Sprawdź swoją subskrypcję.
1. W polu tekstowym **chmura Wiosenna** wybierz wystąpienie chmury Azure wiosennej, która została utworzona w ramach aprowizacji [wystąpienia chmury wiosennej platformy Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-provision-service-instance).
1. W polu tekstowym **aplikacja:** wybierz pozycję **Utwórz aplikację..**..
1. Wprowadź *hellospring*, a następnie kliknij przycisk **OK**.

    ![Wdróż na platformie Azure](media/spring-cloud-quickstart-java/intellij-deploy-to-azure.png)

1. Rozpocznij wdrażanie, klikając przycisk **Uruchom** w dolnej części okna dialogowego **wdrażanie aplikacji w chmurze Azure ze sprężyną** . Wtyczka uruchomi polecenie `mvn package` w `hellospring` aplikacji i Wdróż plik JAR wygenerowany przez `package` polecenie.
---

Po zakończeniu wdrażania możesz uzyskać dostęp do aplikacji w witrynie `https://<service instance name>-hellospring.azuremicroservices.io/` .

  ![Dostęp do aplikacji z przeglądarki](media/spring-cloud-quickstart-java/access-app-browser.png)

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

    ![Wybieranie dzienników przesyłania strumieniowego](media/spring-cloud-intellij-howto/streaming-logs.png)

1. Wybierz wystąpienie.

    ![Wybierz wystąpienie](media/spring-cloud-intellij-howto/select-instance.png)

1. Dziennik przesyłania strumieniowego będzie widoczny w oknie danych wyjściowych.

    ![Dane wyjściowe dziennika przesyłania strumieniowego](media/spring-cloud-intellij-howto/streaming-log-output.png)
---

Aby uzyskać zaawansowane funkcje analizy dzienników, odwiedź kartę **dzienniki** w menu na [Azure Portal](https://portal.azure.com/). Dzienniki mają opóźnienie kilka minut.

[![Dzienniki analizy ](media/spring-cloud-quickstart-java/logs-analytics.png)](media/spring-cloud-quickstart-java/logs-analytics.png#lightbox)

## <a name="clean-up-resources"></a>Czyszczenie zasobów
W poprzednich krokach utworzono zasoby platformy Azure w grupie zasobów. Jeśli nie chcesz potrzebować tych zasobów w przyszłości, Usuń grupę zasobów z portalu lub uruchamiając następujące polecenie w Cloud Shell:
```azurecli
az group delete --name <your resource group name; for example: hellospring-1558400876966-rg> --yes
```

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Generowanie podstawowego projektu Azure wiosny Cloud
> * Inicjowanie obsługi administracyjnej wystąpienia usługi
> * Kompilowanie i wdrażanie aplikacji za pomocą publicznego punktu końcowego
> * Dzienniki przesyłania strumieniowego w czasie rzeczywistym
## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Kompiluj i uruchamiaj mikrousługi](spring-cloud-quickstart-sample-app-introduction.md)

Więcej przykładów można znaleźć w witrynie GitHub: [przykłady chmur usługi Azure wiosennej](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
