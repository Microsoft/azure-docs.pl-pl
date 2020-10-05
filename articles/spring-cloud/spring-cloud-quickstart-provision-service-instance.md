---
title: Szybki Start — udostępnianie usługi Azure wiosennej w chmurze
description: Opisuje tworzenie wystąpienia usługi w chmurze Azure wiosny dla wdrożenia aplikacji.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 09/08/2020
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: f44ebc3885fe57d68756d908c4b392cd43b0366f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91326205"
---
# <a name="quickstart-provision-azure-spring-cloud-service"></a>Szybki Start: udostępnianie usługi Azure wiosennej w chmurze

::: zone pivot="programming-language-csharp"
W tym przewodniku szybki start użyjesz interfejsu wiersza polecenia platformy Azure, aby zainicjować wystąpienie usługi w chmurze Azure wiosennej.

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Zestaw SDK platformy .NET Core 3,1](https://dotnet.microsoft.com/download/dotnet-core/3.1). Usługa w chmurze Azure wiosennej obsługuje program .NET Core 3,1 i jego nowsze wersje.
* [Interfejs wiersza polecenia platformy Azure w wersji 2.0.67 lub nowszej](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true).
* Usługi [git](https://git-scm.com/).

## <a name="install-azure-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia platformy Azure

Sprawdź, czy wersja interfejsu wiersza polecenia platformy Azure to 2.0.67 lub nowszego:

```azurecli
az --version
```

Zainstaluj rozszerzenie chmury wiosennej platformy Azure dla interfejsu wiersza polecenia platformy Azure przy użyciu następującego polecenia:

```azurecli
az extension add --name spring-cloud
```

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

1. Zaloguj się do interfejsu wiersza polecenia platformy Azure.

    ```azurecli
    az login
    ```

1. Jeśli masz więcej niż jedną subskrypcję, wybierz tę, która ma być używana w tym przewodniku Szybki Start.

   ```azurecli
   az account list -o table
   ```

   ```azurecli
   az account set --subscription <Name or ID of a subscription from the last step>
   ```

## <a name="provision-an-instance-of-azure-spring-cloud"></a>Udostępnianie wystąpienia chmury wiosennej platformy Azure

1. Utwórz [grupę zasobów](../azure-resource-manager/management/overview.md) , która będzie zawierać swoją usługę w chmurze platformy Azure. Nazwa grupy zasobów może zawierać znaki alfanumeryczne, podkreślenia, nawiasy, łączniki, kropki (z wyjątkiem końca) i znaków Unicode.

   ```azurecli
   az group create --location eastus --name <resource group name>
   ```

1. Zainicjuj obsługę wystąpienia usługi w chmurze Azure wiosennej. Nazwa wystąpienia usługi musi mieć unikatową długość od 4 do 32 znaków i może zawierać tylko małe litery, cyfry i łączniki. Pierwszy znak nazwy usługi musi być literą, a ostatni znak musi być literą lub cyfrą.

    ```azurecli
    az spring-cloud create -n <service instance name> -g <resource group name>
    ```

    Wykonanie tego polecenia może potrwać kilka minut.

1. Ustaw domyślną nazwę grupy zasobów i nazwę wystąpienia usługi, aby nie trzeba było wielokrotnie określać tych wartości w kolejnych poleceniach.

   ```azurecli
   az configure --defaults group=<resource group name>
   ```

   ```azurecli
   az configure --defaults spring-cloud=<service instance name>
   ```
::: zone-end

::: zone pivot="programming-language-java"
Możesz utworzyć wystąpienie chmury Azure sprężynowej przy użyciu Azure Portal lub interfejsu wiersza polecenia platformy Azure.  Obie metody są wyjaśnione w poniższych procedurach.
## <a name="prerequisites"></a>Wymagania wstępne

* [Zainstaluj program JDK 8](https://docs.microsoft.com/java/azure/jdk/?view=azure-java-stable&preserve-view=true)
* [Zarejestruj się w celu uzyskania subskrypcji platformy Azure](https://azure.microsoft.com/free/)
* Obowiązkowe [Zainstaluj interfejs wiersza polecenia platformy Azure w wersji 2.0.67 lub nowszej](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) i zainstaluj rozszerzenie chmury wiosennej Azure z poleceniem: `az extension add --name spring-cloud`
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

1. Zaloguj się do interfejsu wiersza polecenia platformy Azure i wybierz aktywną subskrypcję.

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
::: zone-end

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono zasoby platformy Azure, które będą nadal naliczane opłaty, jeśli pozostaną w ramach subskrypcji. Jeśli nie planujesz kontynuować pracy z następnym przewodnikiem Szybki Start, zobacz [Oczyszczanie zasobów](spring-cloud-quickstart-logs-metrics-tracing.md#clean-up-resources). W przeciwnym razie przejdź do następnego przewodnika Szybki Start:

> [!div class="nextstepaction"]
> [Konfigurowanie serwera konfiguracji](spring-cloud-quickstart-setup-config-server.md)
