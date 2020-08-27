---
title: Szybki Start — udostępnianie usługi Azure wiosennej w chmurze
description: Opisuje tworzenie wystąpienia usługi w chmurze Azure wiosny dla wdrożenia aplikacji.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 08/03/2020
ms.custom: devx-track-java
ms.openlocfilehash: c91237e3a14c60e477f58be0bf62f634b462960b
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2020
ms.locfileid: "88951918"
---
# <a name="quickstart-provision-azure-spring-cloud-service"></a>Szybki Start: udostępnianie usługi Azure wiosennej w chmurze

Możesz utworzyć wystąpienie chmury Azure sprężynowej przy użyciu Azure Portal lub interfejsu wiersza polecenia platformy Azure.  Obie metody są wyjaśnione w poniższych procedurach.
## <a name="prerequisites"></a>Wymagania wstępne

* [Zainstaluj program JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable)
* [Zarejestruj się w celu uzyskania subskrypcji platformy Azure](https://azure.microsoft.com/free/)
* Obowiązkowe [Zainstaluj interfejs wiersza polecenia platformy Azure w wersji 2.0.67 lub nowszej](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) i zainstaluj rozszerzenie chmury wiosennej Azure z poleceniem: `az extension add --name spring-cloud`
* Obowiązkowe [Zainstaluj Azure Toolkit for IntelliJ](https://plugins.jetbrains.com/plugin/8053-azure-toolkit-for-intellij/) i [Zaloguj się](https://docs.microsoft.com/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app#installation-and-sign-in)

## <a name="provision-an-instance-of-azure-spring-cloud"></a>Udostępnianie wystąpienia chmury wiosennej platformy Azure

#### <a name="portal"></a>[Portal](#tab/Azure-portal)

Poniższa procedura umożliwia utworzenie wystąpienia chmury wiosennej platformy Azure przy użyciu Azure Portal.

1. Na nowej karcie Otwórz [Azure Portal](https://ms.portal.azure.com/). 

2. W górnym polu wyszukiwania Wyszukaj **chmurę Azure wiosenną**.

3. Z wyników wybierz **chmurę ze sprężyną Azure** .

    ![Początek ikony ASC](media/spring-cloud-quickstart-launch-app-portal/find-spring-cloud-start.png)

4. Na stronie chmura Wiosenna Azure kliknij pozycję **+ Dodaj**.

    ![Dodawanie ikony ASC](media/spring-cloud-quickstart-launch-app-portal/spring-cloud-add.png)

5. Wypełnij formularz na stronie **Tworzenie** chmury Azure wiosennej.  Należy wziąć pod uwagę następujące wytyczne:
    - **Subskrypcja**: wybierz subskrypcję, dla której chcesz naliczać opłaty za ten zasób.
    - **Grupa zasobów**: najlepszym rozwiązaniem jest utworzenie nowych grup zasobów dla nowych zasobów. Należy zauważyć, że ta procedura zostanie użyta w dalszych krokach **\<resource group name\>** .
    - **Szczegóły/nazwa usługi**: Określ **\<service instance name\>** .  Nazwa musi mieć długość od 4 do 32 znaków i może zawierać tylko małe litery, cyfry i łączniki.  Pierwszy znak nazwy usługi musi być literą, a ostatni znak musi być literą lub cyfrą.
    - **Lokalizacja**: Wybierz lokalizację wystąpienia usługi.

    ![Początek portalu ASC](media/spring-cloud-quickstart-launch-app-portal/portal-start.png)

6. Kliknij pozycję **Przejrzyj i utwórz**.

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=asc-cli-quickstart&step=public-endpoint)

#### <a name="cli"></a>[Interfejs wiersza polecenia](#tab/Azure-CLI)

Poniższa procedura używa rozszerzenia interfejsu wiersza polecenia platformy Azure w celu aprowizacji wystąpienia chmury wiosennej platformy Azure.

1. Zaloguj się do interfejsu wiersza polecenia platformy Azure i wybierz aktywną subskrypcję. Pamiętaj, aby wybrać aktywną subskrypcję, która jest listy dozwolonych dla chmury wiosennej platformy Azure

    ```azurecli
    az login
    az account list -o table
    az account set --subscription <Name or ID of subscription, skip if you only have 1 subscription>
    ```

1. Przygotuj nazwę usługi w chmurze Azure wiosennej.  Nazwa musi mieć długość od 4 do 32 znaków i może zawierać tylko małe litery, cyfry i łączniki.  Pierwszy znak nazwy usługi musi być literą, a ostatni znak musi być literą lub cyfrą.

1. Utwórz grupę zasobów, która będzie zawierać swoją usługę w chmurze platformy Azure.

    ```azurecli
    az group create --location eastus --name <resource group name>
    ```

    Dowiedz się więcej o [grupach zasobów platformy Azure](../azure-resource-manager/management/overview.md).

1. Otwórz okno interfejsu wiersza polecenia platformy Azure i uruchom następujące polecenia, aby zainicjować obsługę chmury wiosennej platformy Azure.

    ```azurecli
    az spring-cloud create -n <service instance name> -g <resource group name>
    ```

    Wdrożenie wystąpienia usługi potrwa około 5 minut.
---

## <a name="next-steps"></a>Kolejne kroki
> [!div class="nextstepaction"]
> [Konfigurowanie serwera konfiguracji](spring-cloud-quickstart-setup-config-server.md)


