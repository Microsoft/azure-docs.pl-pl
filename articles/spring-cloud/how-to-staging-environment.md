---
title: Konfigurowanie środowiska przejściowego w chmurze Azure wiosennej | Microsoft Docs
description: Dowiedz się, jak używać wdrożenia Blue-Green z chmurą Azure wiosennej
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 0a8536deac0103215cf362c07eb54bbf84701a6b
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878285"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Konfigurowanie środowiska przejściowego w chmurze Azure wiosennej

**Ten artykuł ma zastosowanie do:** ✔️ Java

W tym artykule wyjaśniono, jak skonfigurować wdrożenie przejściowe przy użyciu wzorca wdrażania Blue-Green w chmurze Azure wiosennej. Wdrożenie Blue-zielony jest wzorcem ciągłego dostarczania usługi Azure DevOps, która polega na zachowywaniu istniejącej (niebieskiej) wersji na żywo podczas wdrażania nowego (zielonego). W tym artykule opisano sposób umieszczania wdrożenia przemieszczania w środowisku produkcyjnym bez zmiany wdrożenia produkcyjnego.

## <a name="prerequisites"></a>Wymagania wstępne

* Wystąpienie chmury Azure wiosennej w standardowej warstwie cenowej
* [Rozszerzenie chmury Azure wiosny](/cli/azure/azure-cli-extensions-overview) dla interfejsu wiersza polecenia platformy Azure

W tym artykule jest stosowana aplikacja skompilowana ze sprężyny Initializr. Jeśli chcesz użyć innej aplikacji do tego przykładu, musisz wprowadzić prostą zmianę w publicznej części aplikacji w celu odróżnienia wdrożenia przemieszczania od środowiska produkcyjnego.

>[!TIP]
> [Azure Cloud Shell](https://shell.azure.com) to bezpłatna interaktywna powłoka, której można użyć do uruchomienia instrukcji przedstawionych w tym artykule.  Zawiera ona wspólne, wstępnie zainstalowane narzędzia platformy Azure, w tym najnowsze wersje usług git, JDK, Maven i interfejsu wiersza polecenia platformy Azure. Jeśli zalogowano się do subskrypcji platformy Azure, uruchom wystąpienie Cloud Shell. Aby dowiedzieć się więcej, zobacz [omówienie Azure Cloud Shell](../cloud-shell/overview.md).

Aby skonfigurować wdrożenie Blue-Green w chmurze Azure wiosennej, postępuj zgodnie z instrukcjami w następnych sekcjach.

## <a name="install-the-azure-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia platformy Azure

Zainstaluj rozszerzenie chmury wiosennej platformy Azure dla interfejsu wiersza polecenia platformy Azure przy użyciu następującego polecenia:

```azurecli
az extension add --name spring-cloud
```
## <a name="prepare-the-app-and-deployments"></a>Przygotowywanie aplikacji i wdrożeń
Aby skompilować aplikację, wykonaj następujące kroki:

1. Wygeneruj kod dla przykładowej aplikacji przy użyciu sprężyny Initializr z [tą konfiguracją](https://start.spring.io/#!type=maven-project&language=java&platformVersion=2.3.4.RELEASE&packaging=jar&jvmVersion=1.8&groupId=com.example&artifactId=hellospring&name=hellospring&description=Demo%20project%20for%20Spring%20Boot&packageName=com.example.hellospring&dependencies=web,cloud-eureka,actuator,cloud-starter-sleuth,cloud-starter-zipkin,cloud-config-client).

2. Pobierz kod.
3. Dodaj następujący plik źródłowy HelloController. Java do folderu `\src\main\java\com\example\hellospring\` :

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

Wyświetlanie wdrożonych aplikacji przy użyciu następującej procedury:

1. Przejdź do wystąpienia chmury Azure wiosny w Azure Portal.

1. W okienku po lewej stronie Otwórz okienko **aplikacje** , aby wyświetlić aplikacje dla Twojego wystąpienia usługi.

   ![Zrzut ekranu okienka Otwieranie aplikacji.](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. Możesz wybrać aplikację i wyświetlić szczegóły.

   ![Zrzut ekranu przedstawiający szczegóły aplikacji.](media/spring-cloud-blue-green-staging/app-overview.png)

1. Otwórz **wdrożenia** , aby wyświetlić wszystkie wdrożenia aplikacji. Siatka pokazuje wdrożenia produkcyjne i przejściowe.

   ![Zrzut ekranu pokazujący wdrożenia aplikacji na liście.](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. Wybierz adres URL, aby otworzyć aktualnie wdrożoną aplikację.
    
   ![Zrzut ekranu pokazujący U R L dla wdrożonej aplikacji.](media/spring-cloud-blue-green-staging/running-blue-app.png)

1. Wybierz pozycję **produkcja** w kolumnie **stan** , aby wyświetlić domyślną aplikację.
    
   ![Zrzut ekranu pokazujący, że dla aplikacji domyślnej znajduje się U R L.](media/spring-cloud-blue-green-staging/running-default-app.png)

1. Wybierz pozycję **przemieszczanie** w kolumnie **stan** , aby wyświetlić aplikację przemieszczania.
    
   ![Zrzut ekranu pokazujący U R L dla aplikacji tymczasowej.](media/spring-cloud-blue-green-staging/running-staging-app.png)

>[!TIP]
> * Upewnij się, że punkt końcowy testu jest zakończony ukośnikiem (/), aby upewnić się, że plik CSS został załadowany poprawnie.  
> * Jeśli przeglądarka wymaga wprowadzenia poświadczeń logowania w celu wyświetlenia strony, użyj [dekodowania adresu URL](https://www.urldecoder.org/) , aby zdekodować punkt końcowy testu. Dekodowanie adresu URL zwraca adres URL w formacie *https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.IO/Gateway/Green*. Użyj tego formatu, aby uzyskać dostęp do punktu końcowego.

>[!NOTE]    
> Ustawienia serwera konfiguracji dotyczą zarówno środowiska tymczasowego, jak i środowiska produkcyjnego. Na przykład jeśli ustawisz ścieżkę kontekstu (*Server. serwletu. Context-Path*) dla bramy App Gateway na serwerze konfiguracji jako *somepath*, ścieżka do zielonego wdrożenia zmieni się na *https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.IO/Gateway/Green/somepath/...*.
 
Jeśli w tym momencie odwiedzasz swoją publiczną bramę aplikacji, powinna zostać wyświetlona stara strona bez nowej zmiany.

## <a name="set-the-green-deployment-as-the-production-environment"></a>Ustawianie zielonego wdrożenia jako środowiska produkcyjnego

1. Po zweryfikowaniu zmiany w środowisku tymczasowym można wypchnąć je do środowiska produkcyjnego. Na stronie   >  **wdrożenia** aplikacji wybierz aplikację aktualnie w **środowisku produkcyjnym**.

1. Wybierz wielokropek po **zarejestrowaniu** zielonego wdrożenia, a następnie wybierz pozycję **Ustaw jako produkcyjny**. 

   ![Zrzut ekranu pokazujący opcje ustawiania kompilacji przejściowej w środowisku produkcyjnym.](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. Upewnij się, że w adresie URL aplikacji są wyświetlane zmiany.

   ![Zrzut ekranu, na którym jest teraz wyświetlana U R L aplikacji w środowisku produkcyjnym.](media/spring-cloud-blue-green-staging/new-production-deployment.png)

>[!NOTE]
> Po ustawieniu zielonego wdrożenia jako środowiska produkcyjnego, poprzednie wdrożenie przejdzie do wdrożenia przejściowego.

## <a name="modify-the-staging-deployment"></a>Modyfikowanie wdrożenia przemieszczania

Jeśli nie masz zadowalającej zmiany, możesz zmodyfikować kod aplikacji, utworzyć nowy pakiet jar i przekazać go do zielonego wdrożenia przy użyciu interfejsu wiersza polecenia platformy Azure:

```azurecli
az spring-cloud app deploy  -g <resource-group-name> -s <service-instance-name> -n gateway -d green --jar-path gateway.jar
```

## <a name="delete-the-staging-deployment"></a>Usuwanie wdrożenia przemieszczania

Aby usunąć wdrożenie przemieszczania z Azure Portal, przejdź do strony wdrożenia przemieszczania i wybierz przycisk **Usuń** .

Alternatywnie możesz usunąć wdrożenie przejściowe z interfejsu wiersza polecenia platformy Azure, uruchamiając następujące polecenie:

```azurecli
az spring-cloud app deployment delete -n <staging-deployment-name> -g <resource-group-name> -s <service-instance-name> --app gateway
```

## <a name="next-steps"></a>Następne kroki

* [Ciągłej integracji/ciągłego wdrażania w chmurze platformy Azure](/azure/spring-cloud/spring-cloud-howto-cicd?pivots=programming-language-java)