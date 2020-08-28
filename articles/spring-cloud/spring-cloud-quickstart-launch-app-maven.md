---
title: Szybki Start — uruchamianie aplikacji przy użyciu usługi Maven z chmurą Azure wiosną
description: W tym przewodniku Szybki Start Uruchom przykładową aplikację przy użyciu Maven
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 11/04/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 95ae9c596742b075b5d78d22b925b3adf9285a7d
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89048061"
---
# <a name="quickstart-launch-an-azure-spring-cloud-app-using-the-maven-plug-in"></a>Szybki Start: uruchamianie aplikacji w chmurze ze sprężyną na platformie Azure przy użyciu wtyczki Maven

Korzystając z wtyczki usługi Azure wiosennej w chmurze Maven, możesz łatwo tworzyć i aktualizować aplikacje w chmurze platformy Azure. Dzięki wstępnemu definiowaniu konfiguracji można wdrażać aplikacje w istniejącej usłudze w chmurze platformy Azure. W tym artykule przedstawiono tę funkcję za pomocą przykładowej aplikacji o nazwie PiggyMetrics.

Przed uruchomieniem tego przykładu możesz skorzystać z [podstawowego przewodnika Szybki Start](spring-cloud-quickstart.md).

Postępując zgodnie z tym przewodnikiem Szybki Start, dowiesz się, jak:

> [!div class="checklist"]
> * Inicjowanie obsługi administracyjnej wystąpienia usługi
> * Konfigurowanie serwera konfiguracji dla wystąpienia
> * Lokalne klonowanie i kompilowanie aplikacji mikrousług
> * Wdróż każdą mikrousługę
> * Przypisywanie publicznego punktu końcowego aplikacji

>[!Note]
> Chmura Wiosenna platformy Azure jest obecnie oferowana jako publiczna wersja zapoznawcza. Oferty publicznej wersji zapoznawczej umożliwiają klientom eksperymentowanie z nowymi funkcjami przed ich oficjalną wersją.  Funkcje i usługi publicznej wersji zapoznawczej nie są przeznaczone do użytku produkcyjnego.  Aby uzyskać więcej informacji na temat pomocy technicznej w przypadku wersji zapoznawczych, zapoznaj się z naszymi [często zadawanymi pytaniami](https://azure.microsoft.com/support/faq/) lub zapoznaj się z [support requestą](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)


>[!TIP]
> Azure Cloud Shell to bezpłatna interaktywna powłoka, której można użyć do uruchomienia poleceń z tego artykułu. Ma ona popularne narzędzia platformy Azure preinstalowane, w tym najnowsze wersje narzędzia Git, zestawu Java Development Kit (JDK), Maven i interfejsu wiersza polecenia platformy Azure. Jeśli logujesz się do subskrypcji platformy Azure, uruchom [Azure Cloud Shell](https://shell.azure.com). Aby uzyskać więcej informacji, zobacz [omówienie Azure Cloud Shell](../cloud-shell/overview.md).

Aby ukończyć ten przewodnik Szybki start:

1. [Zainstaluj narzędzie git](https://git-scm.com/).
2. [Zainstaluj program JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable).
3. [Zainstaluj program Maven 3,0 lub nowszy](https://maven.apache.org/download.cgi).
4. [Zarejestruj się, aby skorzystać z bezpłatnej subskrypcji platformy Azure](https://azure.microsoft.com/free/).

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Inicjowanie obsługi administracyjnej wystąpienia usługi na Azure Portal

1. W przeglądarce sieci Web Otwórz [ten link do chmury Azure wiosny w Azure Portal](https://ms.portal.azure.com/#create/Microsoft.AppPlatform)i zaloguj się na swoim koncie.

1. Podaj **szczegóły projektu** dla przykładowej aplikacji w następujący sposób:

    1. Wybierz **subskrypcję** , z którą aplikacja zostanie skojarzona.
    1. Wybierz lub Utwórz grupę zasobów dla aplikacji. Zalecamy utworzenie nowej grupy zasobów.  W poniższym przykładzie pokazano nową grupę zasobów o nazwie `myspringservice` .
    1. Podaj nazwę nowej usługi w chmurze Azure wiosennej.  Nazwa musi mieć długość od 4 do 32 znaków i może zawierać tylko małe litery, cyfry i łączniki. Pierwszy znak nazwy usługi musi być literą, a ostatni znak musi być literą lub cyfrą.  Usługa w poniższym przykładzie ma nazwę `contosospringcloud` .
    1. Wybierz lokalizację aplikacji z dostępnych opcji.  W tym przykładzie wybieramy `East US` .
    1. Wybierz pozycję **Przegląd + Utwórz** , aby przejrzeć Podsumowanie nowej usługi.  Jeśli wszystko wygląda poprawnie, wybierz pozycję **Utwórz**.

    > [!div class="mx-imgBorder"]
    > ![Wybieranie opcji Recenzja + tworzenie](media/maven-qs-review-create.jpg)

Wdrożenie usługi wymaga około 5 minut. Po wdrożeniu usługi wybierz pozycję **Przejdź do zasobu** , a zostanie wyświetlona strona **Omówienie** wystąpienia usługi.

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=provision)

## <a name="set-up-your-configuration-server"></a>Konfigurowanie serwera konfiguracji

1. Na stronie **Przegląd** usługi wybierz opcję **serwer konfiguracji**.
1. W sekcji **repozytorium domyślne** ustaw wartość **Identyfikator URI** na **https://github.com/Azure-Samples/piggymetrics-config** , a następnie wybierz pozycję **Zastosuj** , aby zapisać zmiany.

    > [!div class="mx-imgBorder"]
    > ![Definiowanie i stosowanie ustawień konfiguracji](media/maven-qs-apply-config.jpg)

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=config-server)

## <a name="clone-and-build-the-sample-application-repository"></a>Klonowanie i kompilowanie przykładowego repozytorium aplikacji

1. Uruchom [Azure Cloud Shell](https://shell.azure.com).

1. Sklonuj repozytorium git, uruchamiając następujące polecenie:

    ```console
    git clone https://github.com/Azure-Samples/piggymetrics
    ```
  
1. Zmień katalog i skompiluj projekt, uruchamiając następujące polecenie:

    ```console
    cd piggymetrics
    mvn clean package -DskipTests
    ```

## <a name="generate-configurations-and-deploy-to-the-azure-spring-cloud"></a>Generuj konfiguracje i wdrażaj je w chmurze Azure wiosennej

1. Wygeneruj konfiguracje, uruchamiając następujące polecenie w folderze głównym PiggyMetrics zawierającym pliku pom nadrzędny:

    ```console
    mvn com.microsoft.azure:azure-spring-cloud-maven-plugin:1.1.0:config
    ```

    a. Wybierz moduły `gateway` , `auth-service` i `account-service` .

    b. Wybierz subskrypcję i klaster usługi w chmurze Azure wiosennej.

    c. Na liście podanych projektów wprowadź numer, który odpowiada w `gateway` celu uzyskania dostępu publicznego.
    
    d. Potwierdź konfigurację.

1. PLIKU pom zawiera teraz zależności i konfiguracje wtyczki. Wdróż aplikacje za pomocą następującego polecenia:

   ```console
   mvn azure-spring-cloud:deploy
   ```

1. Po zakończeniu wdrażania możesz uzyskać dostęp do PiggyMetrics przy użyciu adresu URL podanego w danych wyjściowych z poprzedniego polecenia.

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=asc-maven-quickstart&step=deploy)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start wdrożono aplikację w chmurze ze sprężyną z repozytorium Maven. Aby dowiedzieć się więcej na temat chmury Azure wiosennej, przejdź do samouczka dotyczącego przygotowywania aplikacji do wdrożenia.

> [!div class="nextstepaction"]
> [Przygotowywanie aplikacji w chmurze platformy Azure pod kątem wdrożenia](spring-cloud-tutorial-prepare-app-deployment.md) 
>  [Dowiedz się więcej o wtyczkach Maven dla platformy Azure](https://github.com/microsoft/azure-maven-plugins)

Więcej przykładów można znaleźć w witrynie GitHub: [przykłady chmur usługi Azure wiosennej](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
