---
title: Konfigurowanie środowiska przejściowego w chmurze Azure wiosennej | Microsoft Docs
description: Dowiedz się, jak używać wdrożenia Blue-Green z chmurą Azure wiosennej
author: MikeDodaro
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 8cae73e03fee0b59be0c7596f0783570ac14f6ee
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99053113"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Konfigurowanie środowiska przejściowego w chmurze Azure wiosennej

**Ten artykuł ma zastosowanie do:** ✔️ Java

W tym artykule omówiono sposób konfigurowania wdrożenia tymczasowego przy użyciu wzorca wdrażania Blue-Green w chmurze Azure wiosennej. Wdrażanie niebieskie/zielone to wzorzec ciągłego dostarczania usługi Azure DevOps, który polega na utrzymywaniu działającej istniejącej wersji (niebieska) podczas wdrażania nowej (zielona). W tym artykule opisano sposób umieszczania wdrożenia przemieszczania w środowisku produkcyjnym bez konieczności bezpośredniego zmiany wdrożenia produkcyjnego.

## <a name="prerequisites"></a>Wymagania wstępne

* Uruchomiona aplikacja.  Zobacz [Szybki Start: wdrażanie pierwszej aplikacji w chmurze Azure wiosennej](spring-cloud-quickstart.md).
* [Rozszerzenie ASC](https://docs.microsoft.com/cli/azure/azure-cli-extensions-overview) interfejsu wiersza polecenia platformy Azure

Jeśli chcesz użyć innej aplikacji do tego przykładu, musisz wprowadzić prostą zmianę w publicznej części aplikacji.  Ta zmiana odróżnia wdrożenie przejściowe od środowiska produkcyjnego.

>[!TIP]
> Azure Cloud Shell to bezpłatna interaktywna powłoka, której można użyć do uruchomienia instrukcji przedstawionych w tym artykule.  Zawiera ona wspólne, wstępnie zainstalowane narzędzia platformy Azure, w tym najnowsze wersje usług git, JDK, Maven i interfejsu wiersza polecenia platformy Azure. Jeśli zalogowano się do subskrypcji platformy Azure, uruchom [Azure Cloud Shell](https://shell.azure.com).  Aby dowiedzieć się więcej, zobacz [omówienie Azure Cloud Shell](../cloud-shell/overview.md).

Aby skonfigurować środowisko przejściowe w chmurze Azure wiosennej, postępuj zgodnie z instrukcjami podanymi w następnych sekcjach.

## <a name="install-the-azure-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia platformy Azure

Zainstaluj rozszerzenie chmury wiosennej platformy Azure dla interfejsu wiersza polecenia platformy Azure przy użyciu następującego polecenia:

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-apps-and-deployments"></a>Wyświetlanie aplikacji i wdrożeń

Przejrzyj wdrożone aplikacje przy użyciu poniższych procedur.

1. Przejdź do wystąpienia chmury Azure wiosny w Azure Portal.

1. W okienku nawigacji po lewej stronie Otwórz **wdrożenia**.

    [![Wdrożenie — przestarzałe](media/spring-cloud-blue-green-staging/deployments.png)](media/spring-cloud-blue-green-staging/deployments.png)

1. Otwórz blok "aplikacje", aby wyświetlić aplikacje dla swojego wystąpienia usługi.

    [![Aplikacje — pulpit nawigacyjny](media/spring-cloud-blue-green-staging/app-dashboard.png)](media/spring-cloud-blue-green-staging/app-dashboard.png)

1. Możesz kliknąć aplikację i wyświetlić szczegóły.

    [![Aplikacje — Omówienie](media/spring-cloud-blue-green-staging/app-overview.png)](media/spring-cloud-blue-green-staging/app-overview.png)

1. Otwórz blok **wdrożenia** , aby wyświetlić wszystkie wdrożenia aplikacji. W obszarze siatka wdrożenia zostanie wyświetlona wartość środowisko produkcyjne lub przejściowe.

    [![Pulpit nawigacyjny wdrożeń](media/spring-cloud-blue-green-staging/deployments-dashboard.png)](media/spring-cloud-blue-green-staging/deployments-dashboard.png)

1. Możesz kliknąć nazwę wdrożenia, aby wyświetlić przegląd wdrożenia. W takim przypadku jedynym wdrożeniem jest nazwa *Domyślna*.

    [![Przegląd wdrożeń](media/spring-cloud-blue-green-staging/deployments-overview.png)](media/spring-cloud-blue-green-staging/deployments-overview.png)
    

## <a name="create-a-staging-deployment"></a>Tworzenie wdrożenia przemieszczania

1. W lokalnym środowisku programistycznym wprowadź niewielką modyfikację aplikacji. Dzięki temu można łatwo rozróżnić te dwa wdrożenia. Aby skompilować pakiet jar, uruchom następujące polecenie: 

    ```console
    mvn clean package -DskipTests
    ```

1. W interfejsie wiersza polecenia platformy Azure Utwórz nowe wdrożenie i nadaj mu nazwę wdrożenia przejściowego "zielony".

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app default -n green --jar-path gateway/target/gateway.jar
    ```

1. Po pomyślnym zakończeniu wdrożenia interfejsu wiersza polecenia uzyskaj dostęp do strony aplikacji z poziomu **pulpitu nawigacyjnego aplikacji** i Wyświetl wszystkie wystąpienia na karcie **wdrożenia** po lewej stronie.

   [![Pulpit nawigacyjny wdrożenia po zielonym wdrożeniu](media/spring-cloud-blue-green-staging/deployments-dashboard-2.png)](media/spring-cloud-blue-green-staging/deployments-dashboard-2.png)

  
> [!NOTE]
> Stan odnajdywania to *OUT_OF_SERVICE* , dzięki czemu ruch nie zostanie rozesłany do tego wdrożenia przed ukończeniem weryfikacji.

## <a name="verify-the-staging-deployment"></a>Weryfikowanie wdrożenia przemieszczania

Aby sprawdzić, czy Zielona opracowywanie przemieszczania działa:
1. Przejdź do pozycji **wdrożenia** i kliknij `green` **wdrożenie przejściowe**.
1. Na stronie **Przegląd** kliknij **punkt końcowy testu**.
1. Spowoduje to otwarcie kompilacji etapowej, która pokazuje zmiany.

>[!TIP]
> * Upewnij się, że punkt końcowy testu jest zakończony ukośnikiem (/), aby upewnić się, że plik CSS został załadowany poprawnie.  
> * Jeśli przeglądarka wymaga wprowadzenia poświadczeń logowania w celu wyświetlenia strony, użyj [dekodowania adresu URL](https://www.urldecoder.org/) , aby zdekodować punkt końcowy testu. Dekodowanie adresu URL zwraca adres URL w postaci "https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.IO/Gateway/Green".  Ten formularz służy do uzyskiwania dostępu do punktu końcowego.

>[!NOTE]    
> Ustawienia serwera konfiguracji dotyczą zarówno środowiska przejściowego, jak i produkcyjnego. Na przykład jeśli ustawisz ścieżkę kontekstu ( `server.servlet.context-path` ) dla bramy aplikacji na serwerze konfiguracji jako *somepath*, ścieżka do zielonego wdrożenia zmieni się na "https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.IO/Gateway/Green/somepath/...".
 
 Jeśli w tym momencie odwiedzasz swoją publiczną bramę aplikacji, powinna zostać wyświetlona stara strona bez nowej zmiany.
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>Ustawianie zielonego wdrożenia jako środowiska produkcyjnego

1. Po zweryfikowaniu zmiany w środowisku tymczasowym można wypchnąć je do środowiska produkcyjnego. Wróć do **zarządzania wdrożeniem** i wybierz aplikację, która jest obecnie dostępna `Production` .

1. Kliknij wielokropek po **statusie rejestracji** i ustaw kompilację produkcyjną na `staging` .

   [![Wdrażanie wdrożenia zestawu wdrożeń](media/spring-cloud-blue-green-staging/set-staging-deployment.png)](media/spring-cloud-blue-green-staging/set-staging-deployment.png)

1. Wróć do strony **zarządzania wdrożeniem** .  `green`Stan wdrożenia wdrożenia powinien zostać wyświetlony . Jest to teraz uruchomiona kompilacja produkcyjna.

   [![Wynik wdrożenia zestawu wdrożeń](media/spring-cloud-blue-green-staging/set-staging-deployment-result.png)](media/spring-cloud-blue-green-staging/set-staging-deployment-result.png)

1. Skopiuj i wklej adres URL do nowego okna przeglądarki, a nowa strona aplikacji powinna być wyświetlana ze zmianami.

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

* [Ciągłej integracji/ciągłego wdrażania w chmurze platformy Azure](https://review.docs.microsoft.com/azure/spring-cloud/spring-cloud-howto-cicd?branch=pr-en-us-142929&pivots=programming-language-java)