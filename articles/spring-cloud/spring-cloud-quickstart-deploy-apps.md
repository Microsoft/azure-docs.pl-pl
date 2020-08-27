---
title: Szybki Start — Kompilowanie i wdrażanie aplikacji w chmurze Azure wiosennej
description: Opisuje wdrażanie aplikacji w chmurze Azure wiosennej.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: 770dd021a09e3ba0b1c2c6742ded3a73424b042f
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951750"
---
# <a name="quickstart-build-and-deploy-apps-to-azure-spring-cloud"></a>Szybki Start: kompilowanie i wdrażanie aplikacji w chmurze Azure wiosennej

W tym dokumencie wyjaśniono, jak tworzyć i wdrażać aplikacje mikrousług w chmurze Azure sprężynowej przy użyciu:
* Interfejs wiersza polecenia platformy Azure
* Wtyczka dla programu Maven
* IntelliJ

Przed wdrożeniem przy użyciu interfejsu wiersza polecenia platformy Azure lub Maven należy wykonać przykłady, które [inicjują obsługę wystąpienia chmury wiosennej platformy Azure](spring-cloud-quickstart-provision-service-instance.md) i [skonfigurować serwer konfiguracji](spring-cloud-quickstart-setup-config-server.md).

## <a name="prerequisites"></a>Wymagania wstępne

* [Zainstaluj program JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Zarejestruj się w celu uzyskania subskrypcji platformy Azure](https://azure.microsoft.com/free/)
* Obowiązkowe [Zainstaluj interfejs wiersza polecenia platformy Azure w wersji 2.0.67 lub nowszej](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) i zainstaluj rozszerzenie chmury wiosennej Azure z poleceniem: `az extension add --name spring-cloud`
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

1. Musimy faktycznie wdrożyć nasze aplikacje na platformie Azure. Użyj następujących poleceń, aby wdrożyć wszystkie trzy aplikacje:

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
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Zmień katalog i skompiluj projekt, uruchamiając następujące polecenie:

    ```
    cd piggymetrics
    mvn clean package -DskipTests
    ```

### <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>Generuj konfiguracje i wdrażaj je w chmurze Azure wiosennej

1. Wygeneruj konfiguracje, uruchamiając następujące polecenie w folderze głównym PiggyMetrics zawierającym pliku pom nadrzędny. Jeśli użytkownik jest już zalogowany za pomocą interfejsu wiersza polecenia platformy Azure, polecenie automatycznie pobierze poświadczenia. W przeciwnym razie zostanie zalogowanie się przy użyciu instrukcji monitu. Aby uzyskać więcej informacji, zobacz naszą [stronę typu wiki](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication) .

    ```
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.1.0:config
    ```
    
    Zostanie wyświetlony monit o wybranie:
    * **Moduły:** wybierz `gateway` , `auth-service` i "konto-usługa" "
    * **Subskrypcja:** utworzono subskrypcję ze swoim wystąpieniem usługi Azure wiosny Cloud
    * **Wystąpienie usługi:** nazwa utworzonego wystąpienia chmury Azure wiosennej
    * **Publiczny punkt końcowy:** Na liście podanych projektów wprowadź numer, który odpowiada w `gateway` celu uzyskania dostępu publicznego.

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

1. W polu **Nazwa** Dołącz wartość *: Brama* do istniejącej **nazwy** odwołuje się do konfiguracji.
1. W polu tekstowym **artefaktu** wybierz pozycję *com. piggymetrics: Gateway: 1.0-Snapshot*.
1. W polu tekstowym **subskrypcja** Sprawdź swoją subskrypcję.
1. W polu tekstowym **chmura Wiosenna** wybierz wystąpienie chmury Azure wiosennej, która została utworzona w ramach aprowizacji [wystąpienia chmury wiosennej platformy Azure](https://docs.microsoft.com/azure/spring-cloud/spring-cloud-quickstart-provision-service-instance).
1. Ustaw **publiczny punkt końcowy** , aby *włączyć*.
1. W polu tekstowym **aplikacja:** wybierz pozycję **Utwórz aplikację..**..
1. Wprowadź *bramę*, a następnie kliknij przycisk **OK**.

    ![Wdróż na platformie Azure](media/spring-cloud-intellij-howto/revision-deploy-to-azure-2.png)

1. W sekcji **przed uruchomieniem** okna dialogowego kliknij dwukrotnie pozycję * Run Maven cel * *.
1. W polu tekstowym **katalog roboczy** przejdź do folderu *piggymetrics/Gateway* .
1. W polu tekstowym **wiersz polecenia** wprowadź polecenie *Package-DskipTests*. Kliknij przycisk **OK**.
1. Rozpocznij wdrażanie, klikając przycisk **Uruchom** w dolnej części okna dialogowego **wdrażanie aplikacji w chmurze Azure ze sprężyną** . Wtyczka uruchomi polecenie `mvn package` w `gateway` aplikacji i Wdróż plik JAR wygenerowany przez `package` polecenie.

### <a name="deploy-auth-service-and-account-service-apps-to-azure-spring-cloud"></a>Wdrażanie aplikacji uwierzytelniania-usługi i konta usługi w chmurze platformy Azure
Możesz powtórzyć powyższe kroki, aby wdrożyć `auth-service` usługi i `account-service` aplikacje w chmurze Azure wiosennej. Upewnij się:

1. Zmodyfikuj **nazwę** i **artefakt** , aby zidentyfikować `auth-service` aplikację.
1. W polu tekstowym **aplikacja:** wybierz pozycję **Utwórz aplikację..** ., aby utworzyć `auth-service` aplikacje.
1. Sprawdź, czy opcja **publiczny punkt końcowy** jest ustawiona na wartość *Disabled*.
1. W sekcji **przed uruchomieniem** okna dialogowego Przełącz **katalog roboczy** do folderu *piggymetrics/auth-Service* .
1. Rozpocznij wdrażanie, klikając przycisk **Uruchom** w dolnej części okna dialogowego **wdrażanie aplikacji w chmurze Azure ze sprężyną** . 
1. Powtórz te procedury, aby skonfigurować i wdrożyć `account-service` .
---

Przejdź do adresu URL podanego w danych wyjściowych, aby uzyskać dostęp do aplikacji PiggyMetrics. tj. `https://<service instance name>-gateway.azuremicroservices.io`

![Dostęp do PiggyMetrics](media/spring-cloud-quickstart-launch-app-cli/launch-app.png)

Możesz również przejść do Azure Portal, aby znaleźć adres URL. 
1. Przejdź do usługi
2. Wybierz **aplikacje**
3. Wybierz **bramę**

    ![Przejdź do aplikacji](media/spring-cloud-quickstart-launch-app-cli/navigate-app1.png)
    
4. Znajdź adres URL w **bramie | Strona przeglądu**

    ![Nawigacja po drugiej aplikacji](media/spring-cloud-quickstart-launch-app-cli/navigate-app2-url.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów
W poprzednich krokach utworzono zasoby platformy Azure w grupie zasobów. Jeśli nie chcesz potrzebować tych zasobów w przyszłości, Usuń grupę zasobów z portalu lub uruchamiając następujące polecenie w Cloud Shell:
```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```
W poprzednich krokach należy również ustawić domyślną nazwę grupy zasobów. Aby wyczyścić to ustawienie domyślne, uruchom następujące polecenie w Cloud Shell:
```azurecli
az configure --defaults group=
```
## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Dzienniki, metryki i śledzenie](spring-cloud-quickstart-logs-metrics-tracing.md)
