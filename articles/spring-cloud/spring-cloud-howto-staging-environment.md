---
title: Konfigurowanie środowiska przejściowego w chmurze Azure wiosennej | Microsoft Docs
description: Dowiedz się, jak używać wdrożenia Blue-Green z chmurą Azure wiosennej
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: brendm
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: d36c185272b7b67ffb8a21a77cc3a64f22d0df5a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90888480"
---
# <a name="set-up-a-staging-environment-in-azure-spring-cloud"></a>Konfigurowanie środowiska przejściowego w chmurze Azure wiosennej

**Ten artykuł ma zastosowanie do:** ✔️ Java

W tym artykule omówiono sposób konfigurowania wdrożenia tymczasowego przy użyciu wzorca wdrażania Blue-Green w chmurze Azure wiosennej. Wdrażanie niebieskie/zielone to wzorzec ciągłego dostarczania usługi Azure DevOps, który polega na utrzymywaniu działającej istniejącej wersji (niebieska) podczas wdrażania nowej (zielona). W tym artykule opisano sposób umieszczania wdrożenia przemieszczania w środowisku produkcyjnym bez konieczności bezpośredniego zmiany wdrożenia produkcyjnego.

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że aplikacja PiggyMetrics została już wdrożona z naszego [samouczka dotyczącego uruchamiania aplikacji w chmurze ze sprężyną na platformie Azure](spring-cloud-quickstart-launch-app-portal.md). PiggyMetrics składa się z trzech aplikacji: "brama", "konto-usługa" i "uwierzytelnianie-usługa".  

Jeśli chcesz użyć innej aplikacji do tego przykładu, musisz wprowadzić prostą zmianę w publicznej części aplikacji.  Ta zmiana odróżnia wdrożenie przejściowe od środowiska produkcyjnego.

>[!TIP]
> Azure Cloud Shell to bezpłatna interaktywna powłoka, której można użyć do uruchomienia instrukcji przedstawionych w tym artykule.  Zawiera ona wspólne, wstępnie zainstalowane narzędzia platformy Azure, w tym najnowsze wersje usług git, JDK, Maven i interfejsu wiersza polecenia platformy Azure. Jeśli zalogowano się do subskrypcji platformy Azure, uruchom [Azure Cloud Shell](https://shell.azure.com).  Aby dowiedzieć się więcej, zobacz [omówienie Azure Cloud Shell](../cloud-shell/overview.md).

Aby skonfigurować środowisko przejściowe w chmurze Azure wiosennej, postępuj zgodnie z instrukcjami podanymi w następnych sekcjach.

## <a name="install-the-azure-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia platformy Azure

Zainstaluj rozszerzenie chmury wiosennej platformy Azure dla interfejsu wiersza polecenia platformy Azure przy użyciu następującego polecenia:

```azurecli
az extension add --name spring-cloud
```
    
## <a name="view-all-deployments"></a>Wyświetl wszystkie wdrożenia

Przejdź do wystąpienia usługi w Azure Portal i wybierz pozycję **Zarządzanie wdrażaniem** , aby wyświetlić wszystkie wdrożenia. Aby wyświetlić więcej szczegółów, można wybrać każde wdrożenie.

## <a name="create-a-staging-deployment"></a>Tworzenie wdrożenia przemieszczania

1. W lokalnym środowisku programistycznym wprowadź małą modyfikację aplikacji bramy PiggyMetrics. Na przykład zmień kolor w pliku *Gateway/src/Main/sources/static/CSS/Launch. css* . Dzięki temu można łatwo rozróżnić te dwa wdrożenia. Aby skompilować pakiet jar, uruchom następujące polecenie: 

    ```console
    mvn clean package
    ```

1. W interfejsie wiersza polecenia platformy Azure Utwórz nowe wdrożenie i nadaj mu nazwę wdrożenia przejściowego "zielony".

    ```azurecli
    az spring-cloud app deployment create -g <resource-group-name> -s <service-instance-name> --app gateway -n green --jar-path gateway/target/gateway.jar
    ```

1. Po pomyślnym zakończeniu wdrożenia uzyskaj dostęp do strony bramy z poziomu **pulpitu nawigacyjnego aplikacji**i Wyświetl wszystkie wystąpienia na karcie **wystąpienia aplikacji** po lewej stronie.
  
> [!NOTE]
> Stan odnajdywania to *OUT_OF_SERVICE* , dzięki czemu ruch nie zostanie rozesłany do tego wdrożenia przed ukończeniem weryfikacji.

## <a name="verify-the-staging-deployment"></a>Weryfikowanie wdrożenia przemieszczania

1. Wróć do strony **zarządzania wdrożeniem** i wybierz nowe wdrożenie. Stan wdrożenia powinien zawierać wartość *uruchomiony*. Przycisk **Przypisz/Cofnij przypisanie domeny** powinien być wyszarzony, ponieważ środowisko jest środowiskiem przejściowym.

1. W okienku **Przegląd** powinien zostać wyświetlony **punkt końcowy testu**. Skopiuj i wklej go do nowego okna przeglądarki, a nowa strona PiggyMetrics powinna zostać wyświetlona.

>[!TIP]
> * Upewnij się, że punkt końcowy testu jest zakończony ukośnikiem (/), aby upewnić się, że plik CSS został załadowany poprawnie.  
> * Jeśli przeglądarka wymaga wprowadzenia poświadczeń logowania w celu wyświetlenia strony, użyj [dekodowania adresu URL](https://www.urldecoder.org/) , aby zdekodować punkt końcowy testu. Dekodowanie adresu URL zwraca adres URL w postaci "https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.IO/Gateway/Green".  Ten formularz służy do uzyskiwania dostępu do punktu końcowego.

>[!NOTE]    
> Ustawienia serwera konfiguracji dotyczą zarówno środowiska przejściowego, jak i produkcyjnego. Na przykład jeśli ustawisz ścieżkę kontekstu ( `server.servlet.context-path` ) dla bramy aplikacji na serwerze konfiguracji jako *somepath*, ścieżka do zielonego wdrożenia zmieni się na "https:// \<username> : \<password> @ \<cluster-name> . test.azureapps.IO/Gateway/Green/somepath/...".
 
 Jeśli w tym momencie odwiedzasz swoją publiczną bramę aplikacji, powinna zostać wyświetlona stara strona bez nowej zmiany.
    
## <a name="set-the-green-deployment-as-the-production-environment"></a>Ustawianie zielonego wdrożenia jako środowiska produkcyjnego

1. Po zweryfikowaniu zmiany w środowisku tymczasowym można wypchnąć je do środowiska produkcyjnego. Wróć do **zarządzania wdrożeniem**i zaznacz pole wyboru aplikacja **bramy** .

2. Wybierz pozycję **Ustaw wdrożenie**.
3. Na liście **wdrożenie produkcyjne** wybierz pozycję **zielony**, a następnie wybierz pozycję **Zastosuj**.
4. Przejdź do strony **przeglądu** aplikacji bramy. Jeśli masz już przypisaną domenę dla aplikacji bramy, adres URL zostanie wyświetlony w okienku **Przegląd** . Aby wyświetlić zmodyfikowaną stronę PiggyMetrics, wybierz adres URL i przejdź do witryny.

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

* [Szybki Start: wdrażanie pierwszej aplikacji w chmurze Azure wiosny](spring-cloud-quickstart.md)
