---
title: 'Szybki Start: Tworzenie aplikacji Java na Azure App Service'
description: Wdróż swoje pierwsze Hello world Java do Azure App Service w ciągu kilku minut. Wtyczka aplikacji sieci Web platformy Azure dla usługi Maven umożliwia wdrażanie aplikacji języka Java.
keywords: Azure, App Service, Web App, Windows, Linux, Java, Maven, szybki start
author: jasonfreeberg
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 08/01/2020
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
zone_pivot_groups: app-service-platform-windows-linux
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./quickstart-java-uiex
ms.openlocfilehash: ebd189e1cf6e053f5400b8217fc1c2fc385cdac9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101701726"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>Szybki Start: Tworzenie aplikacji Java na Azure App Service

[Azure App Service](overview.md) zapewnia wysoce skalowalną, samoobsługową usługę hostingu w sieci Web.  W tym przewodniku szybki start pokazano, jak używać [interfejsu wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli) z [wtyczką aplikacji sieci Web platformy Azure dla usługi Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) w celu wdrożenia pliku JAR lub pliku War. Użyj kart, aby przełączać się między instrukcjami Java SE i tomcat.


> [!NOTE]
> Można to zrobić również przy użyciu popularnych środowisk IDE, takich jak IntelliJ i zaćmienie. Zapoznaj się z naszymi dokumentami w [Azure Toolkit for IntelliJ przewodniku szybki start](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app) lub [Azure Toolkit for Eclipse przewodnika Szybki Start](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app).


![Przykładowa aplikacja działająca w Azure App Service](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Tworzenie aplikacji w języku Java

# <a name="java-se"></a>[Java SE](#tab/javase)

Sklonuj przykładowy projekt [wprowadzenie rozruchowego sprężyny](https://github.com/spring-guides/gs-spring-boot) .

```bash
git clone https://github.com/spring-guides/gs-spring-boot
```

Przejdź do katalogu ukończonego projektu.

```bash
cd gs-spring-boot/complete
```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

Wykonaj następujące polecenie narzędzia Maven w wierszu polecenia usługi Cloud Shell, aby utworzyć nową aplikację o nazwie `helloworld`:

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp" "-Dversion=1.0-SNAPSHOT"
```

Następnie zmień katalog roboczy na folder projektu:

```bash
cd helloworld
```

---

## <a name="configure-the-maven-plugin"></a>Konfigurowanie wtyczki Maven

Proces wdrażania w usłudze Azure App Service będzie używać automatycznie poświadczeń platformy Azure z poziomu interfejsu wiersza polecenia platformy Azure. Jeśli interfejs wiersza polecenia platformy Azure nie jest zainstalowany lokalnie, wtyczka Maven będzie uwierzytelniana przy użyciu uwierzytelniania OAuth lub urządzenia. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie przy użyciu wtyczek Maven](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication).

Uruchom poniższe polecenie programu Maven w celu skonfigurowania wdrożenia. To polecenie pomoże Ci skonfigurować App Service system operacyjny, wersję Java i wersję tomcat.

```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.12.0:config
```

::: zone pivot="platform-windows"

# <a name="java-se"></a>[Java SE](#tab/javase)

1. Po wyświetleniu monitu z opcją **Subskrypcja** wybierz odpowiedni element `Subscription`, wprowadzając wartość liczbową na początku wiersza.
1. Po wyświetleniu monitu z opcją **aplikacji sieci Web** Zaakceptuj opcję defaut, `<create>` naciskając klawisz ENTER lub wybierając istniejącą aplikację.
1. Po wyświetleniu monitu z opcją systemu **operacyjnego** wybierz pozycję **Windows** , wprowadzając polecenie `3` .
1. Po wyświetleniu monitu z opcją **warstwy cenowej** wybierz pozycję **B2** , wprowadzając polecenie `2` .
1. Użyj domyślnej wersji języka Java, **Java 8**, naciskając klawisz ENTER.
1. Na koniec naciśnij klawisz Enter w ostatnim monicie, aby potwierdzić wybrane opcje.

    Podsumowanie danych wyjściowych będzie wyglądać podobnie do przedstawionego poniżej fragmentu kodu.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007390755
    ResourceGroup : spring-boot-1599007390755-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Windows
    Java : 1.8
    WebContainer : java 8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 41.118 s
    [INFO] Finished at: 2020-09-01T17:43:45-07:00
    [INFO] ------------------------------------------------------------------------
    ```

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. Po wyświetleniu monitu z opcją **Subskrypcja** wybierz odpowiedni element `Subscription`, wprowadzając wartość liczbową na początku wiersza.
1. Po wyświetleniu monitu z opcją **aplikacji sieci Web** Zaakceptuj opcję defaut, `<create>` naciskając klawisz ENTER lub wybierając istniejącą aplikację.
1. Po wyświetleniu monitu z opcją systemu **operacyjnego** wybierz pozycję **Windows** , wprowadzając polecenie `3` .
1. Po wyświetleniu monitu z opcją **warstwy cenowej** wybierz pozycję **B2** , wprowadzając polecenie `2` .
1. Użyj domyślnej wersji języka Java, **Java 8**, naciskając klawisz ENTER.
1. Użyj domyślnego kontenera sieci Web, **Tomcat 8,5**, naciskając klawisz ENTER.
1. Na koniec naciśnij klawisz Enter w ostatnim monicie, aby potwierdzić wybrane opcje.

    Podsumowanie danych wyjściowych będzie wyglądać podobnie do przedstawionego poniżej fragmentu kodu.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003152123
    ResourceGroup : helloworld-1599003152123-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Windows
    Java : 1.8
    WebContainer : tomcat 8.5
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 03:03 min
    [INFO] Finished at: 2020-09-01T16:35:30-07:00
    [INFO] ------------------------------------------------------------------------
    ```

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="java-se"></a>[Java SE](#tab/javase)

1. Po wyświetleniu monitu z opcją **Subskrypcja** wybierz odpowiedni element `Subscription`, wprowadzając wartość liczbową na początku wiersza.
1. Po wyświetleniu monitu z opcją **aplikacji sieci Web** Zaakceptuj opcję defaut, `<create>` naciskając klawisz ENTER lub wybierając istniejącą aplikację.
1. Po wyświetleniu monitu z opcją **systemu operacyjnego** wybierz pozycję **Linux** , naciskając klawisz ENTER.
1. Po wyświetleniu monitu z opcją **warstwy cenowej** wybierz pozycję **B2** , wprowadzając polecenie `2` .
1. Użyj domyślnej wersji języka Java, **Java 8**, naciskając klawisz ENTER.
1. Na koniec naciśnij klawisz Enter w ostatnim monicie, aby potwierdzić wybrane opcje.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : spring-boot-1599007116351
    ResourceGroup : spring-boot-1599007116351-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Linux
    RuntimeStack : JAVA 8-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 20.925 s
    [INFO] Finished at: 2020-09-01T17:38:51-07:00
    [INFO] ------------------------------------------------------------------------
    ```

### <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. Po wyświetleniu monitu z opcją **Subskrypcja** wybierz odpowiedni element `Subscription`, wprowadzając wartość liczbową na początku wiersza.
1. Po wyświetleniu monitu z opcją **aplikacji sieci Web** Zaakceptuj opcję defaut, `<create>` naciskając klawisz ENTER lub wybierając istniejącą aplikację.
1. Po wyświetleniu monitu z opcją **systemu operacyjnego** wybierz pozycję **Linux** , naciskając klawisz ENTER.
1. Po wyświetleniu monitu z opcją **warstwy cenowej** wybierz pozycję **B2** , wprowadzając polecenie `2` .
1. Użyj domyślnej wersji języka Java, **Java 8**, naciskając klawisz ENTER.
1. Użyj domyślnego kontenera sieci Web, **Tomcat 8,5**, naciskając klawisz ENTER.
1. Na koniec naciśnij klawisz Enter w ostatnim monicie, aby potwierdzić wybrane opcje.

    ```
    Please confirm webapp properties
    Subscription Id : ********-****-****-****-************
    AppName : helloworld-1599003744223
    ResourceGroup : helloworld-1599003744223-rg
    Region : westeurope
    PricingTier : Basic_B2
    OS : Linux
    RuntimeStack : TOMCAT 8.5-jre8
    Deploy to slot : false
    Confirm (Y/N)? : Y
    [INFO] Saving configuration to pom.
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time: 50.785 s
    [INFO] Finished at: 2020-09-01T16:43:09-07:00
    [INFO] ------------------------------------------------------------------------
    ```

---

::: zone-end

Konfiguracje dla App Service można modyfikować bezpośrednio w `pom.xml` razie potrzeby. Poniżej wymieniono niektóre typowe:

Właściwość | Wymagane | Opis | Wersja
---|---|---|---
`<schemaVersion>` | fałsz | Określ wersję schematu konfiguracji. Obsługiwane są następujące wartości: `v1` , `v2` . | 1.5.2
`<subscriptionId>` | fałsz | Określ Identyfikator subskrypcji. | 0.1.0 +
`<resourceGroup>` | true | Grupa zasobów platformy Azure dla aplikacji sieci Web. | 0.1.0 +
`<appName>` | true | Nazwa aplikacji sieci Web. | 0.1.0 +
`<region>` | true | Określa region, w którym będzie hostowana aplikacja sieci Web; wartość domyślna to **westeurope**. Wszystkie prawidłowe regiony w sekcji [Obsługiwane regiony](https://azure.microsoft.com/global-infrastructure/services/?products=app-service) . | 0.1.0 +
`<pricingTier>` | fałsz | Warstwa cenowa aplikacji sieci Web. Wartość domyślna to **P1V2** dla obciążenia produkcyjnego, a **B2** jest zalecanym minimum dla środowiska Java Dev/Test. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/app-service/linux/)| 0.1.0 +
`<runtime>` | true | Konfiguracja środowiska uruchomieniowego, w [tym miejscu](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details)można zobaczyć szczegóły. | 0.1.0 +
`<deployment>` | true | W konfiguracji wdrożenia można zobaczyć szczegóły [tutaj](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details). | 0.1.0 +

Należy zachować ostrożność w przypadku wartości `<appName>` i `<resourceGroup>` ( `helloworld-1590394316693` i `helloworld-1590394316693-rg` odpowiednio w demonstracji), które będą używane później.

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

## <a name="deploy-the-app"></a>Wdrażanie aplikacji

Wtyczka Maven używa poświadczeń konta z interfejsu wiersza polecenia platformy Azure do wdrożenia w App Services. Przed kontynuowaniem [Zaloguj się przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli) .

```azurecli
az login
```

Następnie możesz wdrożyć aplikację Java na platformie Azure przy użyciu następującego polecenia.

```bash
mvn package azure-webapp:deploy
```

Po zakończeniu wdrażania aplikacja będzie gotowa pod adresem `http://<appName>.azurewebsites.net/`(`http://helloworld-1590394316693.azurewebsites.net` w pokazie). Otwórz adres URL z lokalną przeglądarką internetową, aby zobaczyć

![Przykładowa aplikacja działająca w Azure App Service](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Gratulacje!** Twoja pierwsza aplikacja Java została wdrożona w celu App Service.

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W poprzednich krokach utworzono zasoby platformy Azure w grupie zasobów. Jeśli prawdopodobnie nie będziesz potrzebować tych zasobów w przyszłości, usuń grupę zasobów z portalu lub uruchamiając następujące polecenie w usłudze Cloud Shell:

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Wykonanie tego polecenia może potrwać około minutę.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z usługą Azure DB for PostgreSQL za pomocą języka Java](../postgresql/connect-java.md)

> [!div class="nextstepaction"]
> [Konfigurowanie ciągłej integracji/ciągłego dostarczania](deploy-continuous-deployment.md)

> [!div class="nextstepaction"]
> [Informacje o cenach](https://azure.microsoft.com/pricing/details/app-service/linux/)

> [!div class="nextstepaction"]
> [Agregowanie dzienników i metryk](troubleshoot-diagnostic-logs.md)

> [!div class="nextstepaction"]
> [Skalowanie w górę](manage-scale-up.md)

> [!div class="nextstepaction"]
> [Zasoby dla deweloperów platformy Azure dla języka Java](/java/azure/)

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji Java](configure-language-java.md)
