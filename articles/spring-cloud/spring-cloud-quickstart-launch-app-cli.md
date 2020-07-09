---
title: 'Szybki Start: uruchamianie aplikacji ze sprężyną Java przy użyciu interfejsu wiersza polecenia platformy Azure'
description: W tym przewodniku szybki start wdrożono przykładową aplikację w chmurze Azure wiosennej w interfejsie wiersza polecenia platformy Azure.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 02/15/2020
ms.author: brendm
ms.openlocfilehash: ab6eb9b516ca30d92437bdc358e3f1f56cfe8e71
ms.sourcegitcommit: ba8df8424d73c8c4ac43602678dae4273af8b336
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/05/2020
ms.locfileid: "84457222"
---
# <a name="quickstart-launch-a-java-spring-application-using-the-azure-cli"></a>Szybki Start: uruchamianie aplikacji ze sprężyną Java przy użyciu interfejsu wiersza polecenia platformy Azure

Chmura sprężynowa platformy Azure umożliwia łatwe uruchamianie aplikacji mikrousług opartych na rozruchu wiosny na platformie Azure.

W tym przewodniku szybki start przedstawiono sposób wdrażania istniejącej aplikacji w chmurze ze sprężyną Java na platformie Azure. Po zakończeniu można nadal zarządzać aplikacją za pośrednictwem interfejsu wiersza polecenia platformy Azure lub przełączać się do korzystania z Azure Portal.

Postępując zgodnie z tym przewodnikiem Szybki Start, dowiesz się, jak:

> [!div class="checklist"]
> * Inicjowanie obsługi administracyjnej wystąpienia usługi
> * Ustawianie serwera konfiguracji dla wystąpienia
> * Lokalne Tworzenie aplikacji mikrousług
> * Wdróż każdą mikrousługę
> * Przypisywanie publicznego punktu końcowego dla aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

>[!Note]
> Chmura Wiosenna platformy Azure jest obecnie oferowana jako publiczna wersja zapoznawcza. Oferty publicznej wersji zapoznawczej umożliwiają klientom eksperymentowanie z nowymi funkcjami przed ich oficjalną wersją.  Funkcje i usługi publicznej wersji zapoznawczej nie są przeznaczone do użytku produkcyjnego.  Aby uzyskać więcej informacji na temat pomocy technicznej w przypadku wersji zapoznawczych, zapoznaj się z naszymi [często zadawanymi pytaniami](https://azure.microsoft.com/support/faq/) lub zapoznaj się z [support requestą](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)

>[!TIP]
> Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule.  Zawiera ona popularne narzędzia platformy Azure preinstalowane, w tym najnowsze wersje usług git, JDK, Maven i interfejsu wiersza polecenia platformy Azure. Jeśli logujesz się do subskrypcji platformy Azure, uruchom [Azure Cloud Shell](https://shell.azure.com) z Shell.Azure.com.  Więcej informacji na temat Azure Cloud Shell można znaleźć w [dokumentacji](../cloud-shell/overview.md) .

Aby ukończyć ten przewodnik Szybki start:

1. [Zainstaluj oprogramowanie Git](https://git-scm.com/)
2. [Zainstaluj program JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
3. [Zainstaluj Maven 3,0 lub nowszy](https://maven.apache.org/download.cgi)
4. [Instalowanie interfejsu wiersza polecenia platformy Azure w wersji 2.0.67 lub nowszej](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
5. [Zarejestruj się w celu uzyskania subskrypcji platformy Azure](https://azure.microsoft.com/free/)

## <a name="install-the-azure-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia platformy Azure

Zainstaluj rozszerzenie chmury wiosennej platformy Azure dla interfejsu wiersza polecenia platformy Azure przy użyciu następującego polecenia

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-on-the-azure-cli"></a>Inicjowanie obsługi administracyjnej wystąpienia usługi w interfejsie wiersza polecenia platformy Azure

1. Zaloguj się do interfejsu wiersza polecenia platformy Azure i wybierz aktywną subskrypcję. Pamiętaj, aby wybrać aktywną subskrypcję, która jest listy dozwolonych dla chmury wiosennej platformy Azure

    ```azurecli
        az login
        az account list -o table
        az account set --subscription <Name or ID of subscription from the last step>
    ```

2. Przygotuj nazwę usługi w chmurze Azure wiosennej.  Nazwa musi mieć długość od 4 do 32 znaków i może zawierać tylko małe litery, cyfry i łączniki.  Pierwszy znak nazwy usługi musi być literą, a ostatni znak musi być literą lub cyfrą.

3. Utwórz grupę zasobów, która będzie zawierać swoją usługę w chmurze platformy Azure.

    ```azurecli
        az group create --location eastus --name <resource group name>
    ```

    Dowiedz się więcej o [grupach zasobów platformy Azure](../azure-resource-manager/management/overview.md).

4. Otwórz okno interfejsu wiersza polecenia platformy Azure i uruchom następujące polecenia, aby zainicjować obsługę chmury wiosennej platformy Azure.

    ```azurecli
        az spring-cloud create -n <service instance name> -g <resource group name>
    ```

    Wdrożenie wystąpienia usługi potrwa około 5 minut.

5. Ustaw domyślną nazwę grupy zasobów i nazwę klastra przy użyciu następujących poleceń:

    ```azurecli
        az configure --defaults group=<resource group name>
        az configure --defaults spring-cloud=<service instance name>
    ```

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=provision)

## <a name="setup-your-configuration-server"></a>Skonfiguruj serwer konfiguracji

Zaktualizuj konfigurację serwera, używając lokalizacji repozytorium git dla naszego projektu:

```azurecli
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/Azure-Samples/piggymetrics-config
```

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=config-server)

## <a name="build-the-microservices-applications-locally"></a>Lokalne Tworzenie aplikacji mikrousług

1. Utwórz nowy folder i Sklonuj repozytorium przykładowej aplikacji na koncie w chmurze platformy Azure.  

    ```console
        mkdir source-code
        git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Zmień katalog i skompiluj projekt.

    ```console
        cd piggymetrics
        mvn clean package -D skipTests
    ```

Kompilowanie projektu trwa około 5 minut.  Po ukończeniu należy mieć pojedyncze pliki JAR dla każdej usługi w odpowiednich folderach.

## <a name="create-the-microservices"></a>Tworzenie mikrousług

Twórz mikrousługi w chmurze wiosny przy użyciu plików JAR utworzonych w poprzednim kroku. Utworzysz trzy mikrousługi: **brama**, **uwierzytelnianie-usługa**i **konto usługi**.

```azurecli
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

## <a name="deploy-applications-and-set-environment-variables"></a>Wdrażanie aplikacji i Ustawianie zmiennych środowiskowych

Musimy faktycznie wdrożyć nasze aplikacje na platformie Azure. Użyj następujących poleceń, aby wdrożyć wszystkie trzy aplikacje:

```azurecli
az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
```

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=deploy)

## <a name="assign-public-endpoint-to-gateway"></a>Przypisz publiczny punkt końcowy do bramy

Potrzebujemy sposobu na dostęp do aplikacji za pośrednictwem przeglądarki sieci Web. Nasza aplikacja bramy wymaga publicznego punktu końcowego.

1. Przypisz punkt końcowy przy użyciu następującego polecenia:

```azurecli
az spring-cloud app update -n gateway --is-public true
```

2. Zbadaj swój publiczny adres IP aplikacji **bramy** , aby można było sprawdzić, czy aplikacja jest uruchomiona:

```azurecli
az spring-cloud app show --name gateway --query properties.url
```

3. Przejdź do adresu URL podanego przez poprzednie polecenie, aby uruchomić aplikację PiggyMetrics.
    ![Zrzut ekranu przedstawiający PiggyMetrics uruchomiony](media/spring-cloud-quickstart-launch-app-cli/launch-app.png)

Możesz również przejść do Azure Portal, aby znaleźć adres URL. 
1. Przejdź do usługi
2. Wybierz **aplikacje**
3. Wybierz **bramę**

    ![Zrzut ekranu przedstawiający PiggyMetrics uruchomiony](media/spring-cloud-quickstart-launch-app-cli/navigate-app1.png)
    
4. Znajdź adres URL na stronie **Przegląd bramy** ![ zrzut ekranu przedstawiający PiggyMetrics uruchomione](media/spring-cloud-quickstart-launch-app-cli/navigate-app2-url.png)

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono aplikację w chmurze ze sprężyną z poziomu interfejsu wiersza polecenia platformy Azure.  Aby dowiedzieć się więcej na temat chmury Azure wiosennej, przejdź do samouczka dotyczącego przygotowywania aplikacji do wdrożenia.

> [!div class="nextstepaction"]
> [Przygotowywanie aplikacji w chmurze platformy Azure pod kątem wdrożenia](spring-cloud-tutorial-prepare-app-deployment.md)

Więcej przykładów można znaleźć w witrynie GitHub: [przykłady chmur usługi Azure wiosennej](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
