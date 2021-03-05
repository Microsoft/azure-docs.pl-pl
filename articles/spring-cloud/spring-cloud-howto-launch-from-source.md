---
title: Jak uruchomić aplikację w chmurze wiosennej z kodu źródłowego
description: W tym przewodniku szybki start dowiesz się, jak uruchomić aplikację w chmurze Azure wiosennej bezpośrednio z kodu źródłowego
author: MikeDodaro
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/03/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: a6710a15bd0637eead0051ebb70a7cdd8bb8aa58
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102210329"
---
# <a name="how-to-launch-your-spring-cloud-application-from-source-code"></a>Jak uruchomić aplikację w chmurze wiosennej z kodu źródłowego

**Ten artykuł ma zastosowanie do:** ✔️ Java

Chmura sprężynowa platformy Azure umożliwia korzystanie z aplikacji mikrousług opartych na chmurze na platformie Azure.

Można uruchamiać aplikacje bezpośrednio z kodu źródłowego Java lub z wstępnie skompilowanego JAR. W tym artykule opisano procedury wdrażania.

Ten przewodnik Szybki Start wyjaśnia, jak:

> [!div class="checklist"]
> * Inicjowanie obsługi administracyjnej wystąpienia usługi
> * Ustawianie serwera konfiguracji dla wystąpienia
> * Lokalne Tworzenie aplikacji mikrousług
> * Wdróż każdą mikrousługę
> * Przypisywanie publicznego punktu końcowego dla aplikacji

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem upewnij się, że Twoja subskrypcja platformy Azure ma wymagane zależności:

1. [Zainstaluj oprogramowanie Git](https://git-scm.com/)
2. [Zainstaluj program JDK 8](https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
3. [Zainstaluj Maven 3,0 lub nowszy](https://maven.apache.org/download.cgi)
4. [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)
5. [Zarejestruj się w celu uzyskania subskrypcji platformy Azure](https://azure.microsoft.com/free/)

> [!TIP]
> Usługa Azure Cloud Shell to bezpłatna interaktywna powłoka, której możesz używać do wykonywania kroków opisanych w tym artykule.  Zawiera ona popularne narzędzia platformy Azure preinstalowane, w tym najnowsze wersje usług git, JDK, Maven i interfejsu wiersza polecenia platformy Azure. Jeśli logujesz się do subskrypcji platformy Azure, uruchom [Azure Cloud Shell](https://shell.azure.com) z Shell.Azure.com.  Więcej informacji na temat Azure Cloud Shell można znaleźć w [dokumentacji](../cloud-shell/overview.md) .

## <a name="install-the-azure-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia platformy Azure

Zainstaluj rozszerzenie chmury wiosennej platformy Azure dla interfejsu wiersza polecenia platformy Azure przy użyciu następującego polecenia

```azurecli
az extension add --name spring-cloud
```

## <a name="provision-a-service-instance-using-the-azure-cli"></a>Inicjowanie obsługi administracyjnej wystąpienia usługi za pomocą interfejsu wiersza polecenia platformy Azure

Zaloguj się do interfejsu wiersza polecenia platformy Azure i wybierz aktywną subskrypcję. 

```azurecli
az login
az account list -o table
az account set --subscription
```

Utwórz grupę zasobów, która będzie zawierać swoją usługę w chmurze platformy Azure. Możesz dowiedzieć się więcej na temat [grup zasobów platformy Azure](../azure-resource-manager/management/overview.md).

```azurecli
az group create --location eastus --name <resource group name>
```

Uruchom następujące polecenia, aby zainicjować obsługę chmury z platformą Azure wiosną. Przygotuj nazwę usługi w chmurze Azure wiosennej. Nazwa musi mieć długość od 4 do 32 znaków i może zawierać tylko małe litery, cyfry i łączniki. Pierwszy znak nazwy usługi musi być literą, a ostatni znak musi być literą lub cyfrą.

```azurecli
az spring-cloud create -n <resource name> -g <resource group name>
```

Wdrożenie wystąpienia usługi zajmie około pięciu minut.

Ustaw nazwę domyślnej grupy zasobów i nazwę wystąpienia chmury Azure sprężynowej przy użyciu następujących poleceń:

```azurecli
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
```

## <a name="create-the-azure-spring-cloud-application"></a>Tworzenie aplikacji w chmurze Azure wiosennej

Następujące polecenie tworzy aplikację w chmurze Azure wiosną w Twojej subskrypcji.  Spowoduje to utworzenie pustej usługi, która będzie mogła przekazać aplikację.

```azurecli
az spring-cloud app create -n <app-name>
```

## <a name="deploy-your-spring-cloud-application"></a>Wdróż aplikację w chmurze wiosny

Aplikację można wdrożyć z poziomu wstępnie skompilowanego JAR lub z repozytorium Gradle lub Maven.  Znajdź instrukcje dla każdego przypadku poniżej.

### <a name="deploy-a-built-jar"></a>Wdrażanie wbudowanego JAR

Aby wdrożyć aplikację z poziomu systemu JAR skompilowanego na komputerze lokalnym, upewnij się, że Twoja kompilacja produkuje plik [jar](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-build.html#howto-create-an-executable-jar-with-maven).

Aby wdrożyć plik FAT-JAR w aktywnym wdrożeniu

```azurecli
az spring-cloud app deploy -n <app-name> --jar-path <path-to-fat-JAR e.g. "target\hellospring-0.0.1-SNAPSHOT.jar">
```

Aby wdrożyć plik FAT-JAR w określonym wdrożeniu

```azurecli
az spring-cloud app deployment create --app <app-name> -n <deployment-name> --jar-path <path-to-fat-JAR e.g. "target\hellospring-0.0.1-SNAPSHOT.jar">
```

### <a name="deploy-from-source-code"></a>Wdróż z kodu źródłowego

Chmura sprężynowa platformy Azure używa [kpack](https://github.com/pivotal/kpack) do kompilowania projektu.  Możesz użyć interfejsu wiersza polecenia platformy Azure, aby przekazać kod źródłowy, skompilować projekt przy użyciu kpack i wdrożyć go w aplikacji docelowej.

> [!WARNING]
> Projekt musi generować tylko jeden plik JAR z `main-class` wpisem w `MANIFEST.MF` programie `target` (dla wdrożeń Maven) lub `build/libs` (dla wdrożeń Gradle).  Wiele plików JAR z `main-class` wpisami spowoduje niepowodzenie wdrożenia.

W przypadku projektów Maven/Gradle o pojedynczym module:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name>
```

W przypadku projektów Maven/Gradle z wieloma modułami Powtórz te czynności dla każdego modułu:

```azurecli
cd <path-to-maven-or-gradle-source-root>
az spring-cloud app deploy -n <app-name> --target-module <relative-path-to-module>
```

### <a name="show-deployment-logs"></a>Pokaż dzienniki wdrożenia

Przejrzyj dzienniki kompilacji kpack za pomocą następującego polecenia:

```azurecli
az spring-cloud app show-deploy-log -n <app-name> [-d <deployment-name>]
```

> [!NOTE]
> W dziennikach kpack będzie wyświetlane tylko najnowsze wdrożenie, jeśli wdrożenie zostało skompilowane ze źródła przy użyciu kpack.

## <a name="assign-a-public-endpoint-to-gateway"></a>Przypisywanie publicznego punktu końcowego do bramy

1. Otwórz stronę **pulpitu nawigacyjnego aplikacji** .
2. Wybierz `gateway` aplikację, aby wyświetlić stronę **Szczegóły aplikacji** .
3. Wybierz pozycję **Przypisz punkt końcowy** , aby przypisać publiczny punkt końcowy do bramy. Może to potrwać kilka minut. 
4. Wprowadź przypisany publiczny adres IP do przeglądarki, aby wyświetlić uruchomioną aplikację.

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=asc-source-quickstart&step=public-endpoint)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Inicjowanie obsługi administracyjnej wystąpienia usługi
> * Ustawianie serwera konfiguracji dla wystąpienia
> * Lokalne Tworzenie aplikacji mikrousług
> * Wdróż każdą mikrousługę
> * Edytuj zmienne środowiskowe dla aplikacji
> * Przypisywanie publicznego adresu IP do bramy aplikacji

> [!div class="nextstepaction"]
> [Sprężynowe dzienniki, metryki, śledzenie](spring-cloud-quickstart-logs-metrics-tracing.md)

Więcej przykładów można znaleźć w witrynie GitHub: [przykłady chmur usługi Azure wiosennej](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/service-binding-cosmosdb-sql).