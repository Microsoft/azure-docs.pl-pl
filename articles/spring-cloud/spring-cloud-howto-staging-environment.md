---
title: Konfigurowanie środowiska przejściowego w chmurze Azure wiosennej | Microsoft Docs
description: Dowiedz się, jak używać wdrożenia Blue-Green z chmurą Azure wiosennej
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 403cfe72a4c5b6dbaea7e4eb93c37f687970443c
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/08/2021
ms.locfileid: "102451935"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Konfigurowanie środowiska przejściowego w chmurze Azure wiosennej

**Ten artykuł ma zastosowanie do:** ✔️ Java

W tym artykule wyjaśniono, jak skonfigurować wdrożenie przejściowe przy użyciu wzorca wdrażania Blue-Green w chmurze Azure wiosennej. Wdrożenie Blue-zielony jest wzorcem ciągłego dostarczania usługi Azure DevOps, która opiera się na zachowaniu istniejącej (niebieskiej) wersji na żywo, podczas gdy jest wdrażany nowy (zielony). W tym artykule opisano sposób umieszczania wdrożenia przemieszczania w środowisku produkcyjnym bez zmiany wdrożenia produkcyjnego.

## <a name="prerequisites"></a>Wymagania wstępne

* Wystąpienie chmury Azure wiosny w **warstwie cenowej** *standardowa* .
* [Rozszerzenie Cloud wiosny](/cli/azure/azure-cli-extensions-overview) Azure dla interfejsu wiersza polecenia platformy Azure

W tym artykule jest stosowana aplikacja skompilowana z inicjatora sprężyny. Jeśli chcesz użyć innej aplikacji do tego przykładu, musisz wprowadzić prostą zmianę w publicznej części aplikacji w celu odróżnienia wdrożenia przemieszczania od środowiska produkcyjnego.

>[!TIP]
> Azure Cloud Shell to bezpłatna interaktywna powłoka, której można użyć do uruchomienia instrukcji przedstawionych w tym artykule.  Zawiera ona wspólne, wstępnie zainstalowane narzędzia platformy Azure, w tym najnowsze wersje usług git, JDK, Maven i interfejsu wiersza polecenia platformy Azure. Jeśli zalogowano się do subskrypcji platformy Azure, uruchom [Azure Cloud Shell](https://shell.azure.com).  Aby dowiedzieć się więcej, zobacz [omówienie Azure Cloud Shell](../cloud-shell/overview.md).

Aby skonfigurować wdrożenia Blue-Green w chmurze Azure wiosennej, postępuj zgodnie z instrukcjami podanymi w następnych sekcjach.

## <a name="install-the-azure-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia platformy Azure

Zainstaluj rozszerzenie chmury wiosennej platformy Azure dla interfejsu wiersza polecenia platformy Azure przy użyciu następującego polecenia:

```azurecli
az extension add --name spring-cloud
```
## <a name="prepare-app-and-deployments"></a>Przygotowywanie aplikacji i wdrożeń
Aby skompilować aplikację, wykonaj następujące kroki:
1. Generuj kod przykładowej aplikacji przy użyciu inicjatora sprężyny z [tą konfiguracją](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client).

2. Pobierz kod.
3. Dodaj następujący plik źródłowy HelloController. Java do folderu `\src\main\java\com\example\hellospring\` .
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
4. Kompilowanie pliku JAR:
```azurecli
mvn clean packge -DskipTests
```
5. Utwórz aplikację w wystąpieniu usługi Azure wiosny Cloud:
```azurecli
az spring-cloud app create -n demo -g <resourceGroup> -s <Azure Spring Cloud instance> --assign-endpoint
```
6. Wdróż aplikację w chmurze Azure wiosną:
```azurecli
az spring-cloud app deploy -n demo -g <resourceGroup> -s <Azure Spring Cloud instance> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar
```
7. Zmodyfikuj kod wdrożenia przemieszczania:
```java
package com.example.hellospring; 
import org.springframework.web.bind.annotation.RestController; 
import org.springframework.web.bind.annotation.RequestMapping; 

@RestController 

public class HelloController { 

@RequestMapping("/") 

  public String index() { 

      return "Greetings from Azure Spring Cloud! THIS IS THE GREEN DEPLOYMENT"; 
  } 

} 
```
8. Odbuduj plik JAR:
```azurecli
mvn clean packge -DskipTests
```
9. Utwórz zielone wdrożenie: 
```azurecli
az spring-cloud app deployment create -n green --app demo -g <resourceGroup> -s <Azure Spring Cloud instance> --jar-path target\hellospring-0.0.1-SNAPSHOT.jar 
```

## <a name="view-apps-and-deployments"></a>Wyświetlanie aplikacji i wdrożeń

Przejrzyj wdrożone aplikacje przy użyciu poniższych procedur.

1. Przejdź do wystąpienia chmury Azure wiosny w Azure Portal.

1. W okienku nawigacji po lewej stronie Otwórz blok "aplikacje", aby wyświetlić aplikacje dla Twojego wystąpienia usługi.

    [![Aplikacje — pulpit nawigacyjny](media/spring-cloud-blue-green-staging/app-dashboard.png)](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. Możesz kliknąć aplikację i wyświetlić szczegóły.

    [![Aplikacje — Omówienie](media/spring-cloud-blue-green-staging/app-overview.png)](media/spring-cloud-blue-green-staging/app-overview.png)

1. Otwórz **wdrożenia** , aby wyświetlić wszystkie wdrożenia aplikacji. Siatka pokazuje wdrożenia produkcyjne i przejściowe.

    [![Pulpit nawigacyjny aplikacji/wdrożeń](media/spring-cloud-blue-green-staging/deployments-dashboard.png)](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. Kliknij adres URL, aby otworzyć aktualnie wdrożoną aplikację.
    ![Wdrożony adres URL](media/spring-cloud-blue-green-staging/running-blue-app.png)
1. Kliknij pozycję **produkcja** w kolumnie **stan** , aby wyświetlić domyślną aplikację.
    ![Domyślnie uruchomione](media/spring-cloud-blue-green-staging/running-default-app.png)
1. Kliknij pozycję **przemieszczanie** w kolumnie **stan** , aby wyświetlić aplikację przemieszczania.
    ![Przemieszczanie uruchomione](media/spring-cloud-blue-green-staging/running-staging-app.png)

>[!TIP]
> * Upewnij się, że punkt końcowy testu jest zakończony ukośnikiem (/), aby upewnić się, że plik CSS został załadowany poprawnie.  
> * Jeśli przeglądarka wymaga wprowadzenia poświadczeń logowania w celu wyświetlenia strony, użyj [dekodowania adresu URL](https://www.urldecoder.org/) , aby zdekodować punkt końcowy testu. Dekodowanie adresu URL zwraca adres URL w postaci "https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.IO/Gateway/Green".  Ten formularz służy do uzyskiwania dostępu do punktu końcowego.

>[!NOTE]    
> Ustawienia serwera konfiguracji dotyczą zarówno środowiska przejściowego, jak i produkcyjnego. Na przykład jeśli ustawisz ścieżkę kontekstu ( `server.servlet.context-path` ) dla bramy aplikacji na serwerze konfiguracji jako *somepath*, ścieżka do zielonego wdrożenia zmieni się na "https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.IO/Gateway/Green/somepath/...".
 
 Jeśli w tym momencie odwiedzasz swoją publiczną bramę aplikacji, powinna zostać wyświetlona stara strona bez nowej zmiany.

## <a name="set-the-green-deployment-as-the-production-environment"></a>Ustawianie zielonego wdrożenia jako środowiska produkcyjnego

1. Po zweryfikowaniu zmiany w środowisku tymczasowym można wypchnąć je do środowiska produkcyjnego. Na stronie  / **wdrożenia** aplikacji wybierz aplikację, która jest obecnie dostępna `Production` .

1. Kliknij wielokropek po **statusie rejestracji** zielonego wdrożenia i ustaw kompilację przemieszczania w środowisku produkcyjnym. 

   [![Ustaw produkcję na przejściową](media/spring-cloud-blue-green-staging/set-staging-deployment.png)](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. Teraz adres URL aplikacji powinien zawierać zmiany.

   ![Przygotowywanie teraz do wdrożenia](media/spring-cloud-blue-green-staging/new-production-deployment.png)

>[!NOTE]
> Po ustawieniu zielonego wdrożenia jako środowiska produkcyjnego, poprzednie wdrożenie przejdzie do wdrożenia przejściowego.

## <a name="modify-the-staging-deployment"></a>Modyfikowanie wdrożenia przemieszczania

Jeśli nie masz zadowalającej zmiany, możesz zmodyfikować kod aplikacji, utworzyć nowy pakiet jar i przekazać go do zielonego wdrożenia przy użyciu interfejsu wiersza polecenia platformy Azure.

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Usuwanie wdrożenia przemieszczania

Aby usunąć wdrożenie przejściowe z portu platformy Azure, przejdź do strony wdrożenia przejściowego, a następnie wybierz przycisk **Usuń** .

Alternatywnie możesz usunąć wdrożenie przejściowe z interfejsu wiersza polecenia platformy Azure, uruchamiając następujące polecenie:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

## <a name="next-steps"></a>Następne kroki

* [Ciągłej integracji/ciągłego wdrażania w chmurze platformy Azure](/azure/spring-cloud/spring-cloud-howto-cicd?pivots=programming-language-java)