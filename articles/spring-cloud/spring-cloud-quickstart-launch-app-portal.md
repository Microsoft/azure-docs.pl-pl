---
title: Szybki Start — uruchamianie istniejącej aplikacji w chmurze platformy Azure przy użyciu Azure Portal
description: W tym przewodniku szybki start Wdróż aplikację w chmurze ze sprężyną w chmurze Azure wiosennej przy użyciu Azure Portal.
author: bmitchell287
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 02/15/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 09820da3fcebe3614852f36180a29f35505039b8
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/31/2020
ms.locfileid: "87496852"
---
# <a name="quickstart-launch-an-existing-azure-spring-cloud-application-using-the-azure-portal"></a>Szybki Start: uruchamianie istniejącej aplikacji w chmurze platformy Azure przy użyciu Azure Portal

W tym przewodniku szybki start przedstawiono sposób wdrażania istniejącej aplikacji ze sprężyną w chmurze na platformie Azure. Chmura sprężynowa platformy Azure umożliwia łatwe uruchamianie aplikacji mikrousług opartych na chmurze na platformie Azure. 

Kod przykładowej aplikacji użyty w tym samouczku można znaleźć w naszym [repozytorium przykładów usługi GitHub](https://github.com/Azure-Samples/PiggyMetrics). Po zakończeniu udostępniona Przykładowa aplikacja będzie dostępna w trybie online i będzie gotowa do zarządzania za pośrednictwem Azure Portal.

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

## <a name="provision-a-service-instance-on-the-azure-portal"></a>Inicjowanie obsługi administracyjnej wystąpienia usługi na Azure Portal

1. Na nowej karcie Otwórz [Azure Portal](https://ms.portal.azure.com/). 

2. W górnym polu wyszukiwania Wyszukaj **chmurę Azure wiosenną**.

3. Z wyników wybierz **chmurę ze sprężyną Azure** .

 ![Ikona ASC](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Na stronie chmura Wiosenna Azure kliknij pozycję **+ Dodaj**.

 ![Ikona ASC](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Wypełnij formularz na stronie **Tworzenie** chmury Azure wiosennej.  Należy wziąć pod uwagę następujące wytyczne:
    - **Subskrypcja**: wybierz subskrypcję, dla której chcesz naliczać opłaty za ten zasób.  Upewnij się, że ta subskrypcja została dodana do naszej listy dozwolonych chmur Azure wiosennej.
    - **Grupa zasobów**: najlepszym rozwiązaniem jest utworzenie nowych grup zasobów dla nowych zasobów.
    - **Szczegóły/nazwa usługi**: Określ nazwę wystąpienia usługi.  Nazwa musi mieć długość od 4 do 32 znaków i może zawierać tylko małe litery, cyfry i łączniki.  Pierwszy znak nazwy usługi musi być literą, a ostatni znak musi być literą lub cyfrą.
    - **Lokalizacja**: Wybierz lokalizację wystąpienia usługi. Obecnie obsługiwane lokalizacje to Wschodnie stany USA, zachodnie stany USA 2, Europa Zachodnia i Azja Południowo-Wschodnia.

    ![Początek portalu ASC](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Kliknij kartę **Ustawienia diagnostyczne** , aby otworzyć następujące okno dialogowe.

7. Możesz ustawić **opcję Włącz dzienniki** na *wartość tak* lub *nie* , zgodnie z wymaganiami.

    ![Włączanie dzienników](media/spring-cloud-quickstart-launch-app-portal/diagnostic-setting.png)

8. Kliknij kartę **śledzenie** .

9. Możesz ustawić **opcję Włącz śledzenie** na *wartość tak* lub *nie* zgodnie z wymaganiami.  Jeśli ustawisz opcję **Włącz śledzenie** na wartość tak, zaznacz również pozycję istniejąca aplikacja usługi Application Insights lub Utwórz nową. Bez specyfikacji **Application Insights** wystąpi błąd walidacji.


    ![Śledzenie](media/spring-cloud-quickstart-launch-app-portal/tracing.png)

10. Kliknij pozycję **Przejrzyj i utwórz**.

11. Sprawdź specyfikacje i kliknij przycisk **Utwórz**.

Wdrożenie usługi wymaga około 5 minut.  Po jego wdrożeniu zostanie wyświetlona strona **przeglądu** wystąpienia usługi.

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=provision)


## <a name="set-up-your-configuration-server"></a>Konfigurowanie serwera konfiguracji

1. Przejdź do strony **Przegląd** usługi i wybierz pozycję **serwer konfiguracji**.

2. W sekcji **repozytorium domyślne** Ustaw **Identyfikator URI** na " https://github.com/Azure-Samples/piggymetrics-config ".

3. Wybierz pozycję **Zastosuj**, aby zapisać zmiany.

    ![Zrzut ekranu przedstawiający Portal ASC](media/spring-cloud-quickstart-launch-app-portal/portal-config.png)

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=config-server)

## <a name="build-and-deploy-microservice-applications"></a>Kompilowanie i wdrażanie aplikacji mikrousług

1. Otwórz [Azure Cloud Shell](https://shell.azure.com) lub lokalną powłokę z zainstalowanym interfejsem wiersza polecenia platformy Azure. W tym miejscu najpierw tworzymy tymczasowy katalog o nazwie `source-code` przed sklonowaniem przykładowej aplikacji.

    ```console
    mkdir source-code
    cd source-code
    git clone https://github.com/Azure-Samples/piggymetrics
    ```

2. Kompiluj sklonowany pakiet.

    ```console
    cd piggymetrics
    mvn clean package -DskipTests
    ```

3. Zainstaluj rozszerzenie chmury wiosennej platformy Azure dla interfejsu wiersza polecenia platformy Azure przy użyciu następującego polecenia

    ```azurecli
    az extension add --name spring-cloud
    ```

4. Przypisywanie nazw do grupy zasobów i usługi. Pamiętaj, aby zastąpić symbole zastępcze poniżej nazwą grupy zasobów i nazwą usługi, która została zainicjowana wcześniej w tym samouczku.

    ```azurecli
    az configure --defaults group=<resource group name>
    az configure --defaults spring-cloud=<service instance name>
    ```

5. Utwórz `gateway` aplikację i Wdróż plik JAR.

    Utwórz aplikację przy użyciu rozszerzenia chmury Wiosnowej:

    ```azurecli
    az spring-cloud app create -n gateway
    az spring-cloud app deploy -n gateway --jar-path ./gateway/target/gateway.jar
    ```

6. Wykonując te same wzorce, Utwórz `account-service` aplikacje i `auth-service` Wdróż ich pliki jar.

    ```azurecli
    az spring-cloud app create -n account-service
    az spring-cloud app deploy -n account-service --jar-path ./account-service/target/account-service.jar
    az spring-cloud app create -n auth-service
    az spring-cloud app deploy -n auth-service --jar-path ./auth-service/target/auth-service.jar
    ```

7. Wdrożenie aplikacji może zająć kilka minut. Aby upewnić się, że zostały wdrożone, przejdź do bloku **aplikacje** w Azure Portal. Powinna zostać wyświetlona linia każdej z trzech aplikacji.

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=deploy)

## <a name="assign-a-public-endpoint-to-gateway"></a>Przypisywanie publicznego punktu końcowego do bramy

1. Otwórz kartę **aplikacje** w menu po lewej stronie.

2. Wybierz `gateway` aplikację, aby wyświetlić stronę **Przegląd** .

3. Wybierz pozycję **Przypisz punkt końcowy** , aby przypisać publiczny punkt końcowy do bramy. Może to potrwać kilka minut.

    ![Zrzut ekranu przedstawiający Portal ASC](media/spring-cloud-quickstart-launch-app-portal/portal-endpoint.png)

4. Wprowadź przypisany publiczny punkt końcowy (oznaczony jako **adres URL**) do przeglądarki, aby wyświetlić uruchomioną aplikację.

    ![Zrzut ekranu przedstawiający Portal ASC](media/spring-cloud-quickstart-launch-app-portal/sample-app.png)

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=asc-portal-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Inicjowanie obsługi administracyjnej wystąpienia usługi
> * Ustawianie serwera konfiguracji dla wystąpienia
> * Lokalne Tworzenie aplikacji mikrousług
> * Wdróż każdą mikrousługę
> * Przypisywanie publicznego punktu końcowego dla bramy aplikacji

> [!div class="nextstepaction"]
> [Przygotowywanie aplikacji w chmurze platformy Azure pod kątem wdrożenia](spring-cloud-tutorial-prepare-app-deployment.md)

Więcej przykładów można znaleźć w witrynie GitHub: [przykłady chmur usługi Azure wiosennej](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).
