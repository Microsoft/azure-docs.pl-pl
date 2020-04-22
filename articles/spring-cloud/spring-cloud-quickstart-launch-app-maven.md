---
title: Szybki start — uruchamianie aplikacji przy użyciu maven z usługą Azure Spring Cloud
description: W tym przewodniku Szybki start uruchom przykładową aplikację przy użyciu maven
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 4eddf34f4887f0e2bad08eb5700d83eaeae0ef7e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687089"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-using-the-maven-plug-in"></a>Szybki start: uruchamianie aplikacji usługi Azure Spring Cloud przy użyciu wtyczki Maven

Korzystając z wtyczki Azure Spring Cloud Maven, można łatwo tworzyć i aktualizować aplikacje usługi Azure Spring Cloud. Predefiniowanie konfiguracji można wdrożyć aplikacje w istniejącej usłudze Azure Spring Cloud. W tym artykule należy użyć przykładowej aplikacji o nazwie PiggyMetrics, aby zademonstrować tę funkcję.

Po tym przewodniku Szybki start dowiesz się, jak:

> [!div class="checklist"]
> * Uarowuj instancję usługi
> * Konfigurowanie serwera konfiguracji dla wystąpienia
> * Klonowanie i tworzenie aplikacji mikrousług lokalnie
> * Wdrażanie każdej mikrousług
> * Przypisywanie publicznego punktu końcowego dla aplikacji

>[!Note]
> Usługa Azure Spring Cloud jest obecnie oferowana jako publiczna wersja zapoznawcza. Publiczne oferty w wersji zapoznawczej umożliwiają klientom eksperymentowanie z nowymi funkcjami przed ich oficjalną premierą.  Publiczne funkcje i usługi w wersji zapoznawczej nie są przeznaczone do użytku w wersji produkcyjnej.  Aby uzyskać więcej informacji na temat pomocy technicznej podczas wersji zapoznawców, zapoznaj się z [często zadawanymi pytaniami](https://azure.microsoft.com/support/faq/) lub złóż [wniosek o pomoc techniczną,](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) aby dowiedzieć się więcej.


>[!TIP]
> Usługa Azure Cloud Shell to bezpłatna powłoka interaktywna, której można użyć do uruchomienia poleceń w tym artykule. Ma wstępnie zainstalowane typowe narzędzia platformy Azure, w tym najnowsze wersje gita, zestawu Java Development Kit (JDK), Maven i interfejsu wiersza polecenia platformy Azure. Jeśli zalogujesz się do subskrypcji platformy Azure, uruchom usługę [Azure Cloud Shell](https://shell.azure.com). Aby uzyskać więcej informacji, zobacz [Omówienie usługi Azure Cloud Shell](../cloud-shell/overview.md).

Aby ukończyć ten przewodnik Szybki start:

1. [Zainstaluj Git](https://git-scm.com/).
2. [Zainstaluj JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable).
3. [Zainstaluj Maven 3.0 lub nowsze](https://maven.apache.org/download.cgi).
4. [Zarejestruj się, aby uzyskać bezpłatną subskrypcję platformy Azure](https://azure.microsoft.com/free/).

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Aprowizuj wystąpienie usługi w witrynie Azure portal

1. W przeglądarce sieci Web otwórz [to łącze do usługi Azure Spring Cloud w witrynie Azure portal](https://ms.portal.azure.com/#create/Microsoft.AppPlatform)i zaloguj się do swojego konta.

1. Podaj **szczegóły projektu** dla przykładowej aplikacji w następujący sposób:

    1. Wybierz **subskrypcję,** z którą aplikacja będzie skojarzona.
    1. Wybierz lub utwórz grupę zasobów dla aplikacji. Zalecamy utworzenie nowej grupy zasobów.  W poniższym przykładzie przedstawiono nową grupę zasobów o nazwie `myspringservice`.
    1. Podaj nazwę nowej usługi Azure Spring Cloud.  Nazwa musi zawierać od 4 do 32 znaków i może zawierać tylko małe litery, cyfry i łączniki. Pierwszy znak nazwy usługi musi być literą, a ostatni znak musi być literą lub cyfrą.  Usługa w poniższym przykładzie `contosospringcloud`ma nazwę .
    1. Wybierz lokalizację aplikacji z podanych opcji.  W tym przykładzie `East US`wybieramy opcję .
    1. Wybierz **opcję Przejrzyj + utwórz,** aby przejrzeć podsumowanie nowej usługi.  Jeśli wszystko wygląda poprawnie, wybierz pozycję **Utwórz**.

    > [!div class="mx-imgBorder"]
    > ![Wybierz pozycję Recenzja + utwórz](media/maven-qs-review-create.jpg)

Wdrożenie usługi zajmuje około 5 minut. Po wdrożeniu usługi wybierz pozycję **Przejdź do zasobu** i zostanie **wyświetlona** strona Przegląd wystąpienia usługi.

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=provision)

## <a name="set-up-your-configuration-server"></a>Konfigurowanie serwera konfiguracji

1. Na stronie **Przegląd** usługi wybierz pozycję **Serwer konfiguracji**.
1. W sekcji **Domyślne repozytorium** ustaw **https://github.com/Azure-Samples/piggymetrics-config**identyfikator **URI** na , a następnie wybierz pozycję **Zastosuj,** aby zapisać zmiany.

    > [!div class="mx-imgBorder"]
    > ![Definiowanie i stosowanie ustawień konfiguracji](media/maven-qs-apply-config.jpg)

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=config-server)

## <a name="clone-and-build-the-sample-application-repository"></a>Klonowanie i tworzenie przykładowego repozytorium aplikacji

1. Uruchom usługę [Azure Cloud Shell](https://shell.azure.com).

1. Sklonuj repozytorium Git, uruchamiając następujące polecenie:

    ```console
    git clone https://github.com/Azure-Samples/PiggyMetrics
    ```
  
1. Zmień katalog i skompiluj projekt, uruchamiając następujące polecenie:

    ```console
    cd piggymetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>Generowanie konfiguracji i wdrażanie w chmurze azure spring cloud

1. Generowanie konfiguracji przez uruchomienie następującego polecenia w folderze głównym PiggyMetrics zawierającego nadrzędny pom:

    ```console
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.1.0:config
    ```

    a. Wybierz moduły `gateway``auth-service`, `account-service`i .

    b. Wybierz subskrypcję i klaster usług Azure Spring Cloud.

    d. Na liście dostarczonych projektów wprowadź numer, `gateway` który odpowiada, aby nadać mu publiczny dostęp.
    
    d. Potwierdź konfigurację.

1. Pom zawiera teraz zależności i konfiguracje wtyczki. Wdrażanie aplikacji przy użyciu następującego polecenia:

   ```console
   mvn azure-spring-cloud:deploy
   ```

1. Po zakończeniu wdrażania można uzyskać dostęp do piggymetrics przy użyciu adresu URL podanego w danych wyjściowych z poprzedniego polecenia.

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=deploy)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start wdrożono aplikację Spring Cloud z repozytorium Maven. Aby dowiedzieć się więcej o usłudze Azure Spring Cloud, przejdź do samouczka na temat przygotowywania aplikacji do wdrożenia.

> [!div class="nextstepaction"]
> [Przygotowanie aplikacji usługi Azure Spring Cloud do wdrożenia](spring-cloud-tutorial-prepare-app-deployment.md)
> [Dowiedz się więcej o wtyczkach Maven dla platformy Azure](https://github.com/microsoft/azure-maven-plugin)

Więcej przykładów jest dostępnych w usłudze GitHub: [Samples w chmurze w chmurze Azure Spring.](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql)
