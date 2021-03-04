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
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 64e4c05e9439c164329dede5d714bec160bc5ae2
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050379"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service"></a>Szybki Start: Tworzenie aplikacji Java na Azure App Service

[Azure App Service](overview.md) zapewnia wysoce skalowalną, samoobsługową usługę hostingu w sieci Web.  W tym przewodniku szybki start pokazano, jak używać [interfejsu wiersza polecenia platformy Azure](/cli/azure/get-started-with-azure-cli) z [wtyczką aplikacji sieci Web platformy Azure dla usługi Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) w celu wdrożenia pliku JAR lub pliku War. 

Istnieją także wersje IDE tego artykułu. Zapoznaj się z [Azure Toolkit for IntelliJ szybki start](/azure/developer/java/toolkit-for-intellij/create-hello-world-web-app) lub [Azure Toolkit for Eclipse przewodnika Szybki Start](/azure/developer/java/toolkit-for-eclipse/create-hello-world-web-app).

## <a name="1-prepare-your-environment"></a>1. Przygotuj środowisko

Przed rozpoczęciem należy wykonać następujące czynności:

+ przykład <abbr title="Profil, który przechowuje informacje o rozliczeniach dotyczące użycia platformy Azure.">Konto platformy Azure</abbr> z aktywnym <abbr title="Podstawowa struktura organizacyjna, w której zarządzane są zasoby na platformie Azure, zwykle skojarzone z osobą lub działem w organizacji.">subskrypcja</abbr>. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

+ [Zestaw Java developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), wersja 8 lub 11.

+ [Apache Maven](https://maven.apache.org), wersja 3,0 lub nowsza.

Wtyczka Maven używa poświadczeń konta z interfejsu wiersza polecenia platformy Azure do wdrożenia w App Services. Przed kontynuowaniem [Zaloguj się przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli) . Aby uzyskać więcej informacji, zobacz [uwierzytelnianie przy użyciu wtyczek Maven](https://github.com/microsoft/azure-maven-plugins/wiki/Authentication).

```azurecli
az login
```

<br>
<hr/>

## <a name="2-create-a-java-app"></a>2. Tworzenie aplikacji Java

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

<br>
<hr/>

## <a name="3-configure-the-maven-plugin"></a>3. skonfiguruj wtyczkę Maven

Uruchom poniższe polecenie programu Maven w celu skonfigurowania wdrożenia. To polecenie pomoże Ci skonfigurować App Service system operacyjny, wersję Java i wersję tomcat.

```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.12.0:config
```

::: zone pivot="platform-windows"

# <a name="java-se"></a>[Java SE](#tab/javase)

1. Po wyświetleniu monitu z opcją **Subskrypcja** wybierz odpowiedni element `Subscription`, wprowadzając wartość liczbową na początku wiersza.
1. Po wyświetleniu monitu z opcją **aplikacji sieci Web** Zaakceptuj opcję domyślną, `<create>` naciskając klawisz ENTER lub wybierając istniejącą aplikację.
1. Po wyświetleniu monitu z opcją systemu **operacyjnego** wybierz pozycję **Windows** , wprowadzając polecenie `3` .
1. Po wyświetleniu monitu z opcją **warstwy cenowej** wybierz pozycję **B2** , wprowadzając polecenie `2` .
1. Użyj domyślnej wersji języka Java, **Java 8**, naciskając klawisz ENTER.
1. Na koniec naciśnij klawisz Enter w ostatnim monicie, aby potwierdzić wybrane opcje.

    Podsumowanie danych wyjściowych będzie wyglądać podobnie do przedstawionego poniżej fragmentu kodu.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
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
    </pre>

# <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. Po wyświetleniu monitu z opcją **Subskrypcja** wybierz odpowiedni element `Subscription`, wprowadzając wartość liczbową na początku wiersza.
1. Po wyświetleniu monitu z opcją **aplikacji sieci Web** Zaakceptuj opcję domyślną, `<create>` naciskając klawisz ENTER lub wybierając istniejącą aplikację.
1. Po wyświetleniu monitu z opcją systemu **operacyjnego** wybierz pozycję **Windows** , wprowadzając polecenie `3` .
1. Po wyświetleniu monitu z opcją **warstwy cenowej** wybierz pozycję **B2** , wprowadzając polecenie `2` .
1. Użyj domyślnej wersji języka Java, **Java 8**, naciskając klawisz ENTER.
1. Użyj domyślnego kontenera sieci Web, **Tomcat 8,5**, naciskając klawisz ENTER.
1. Na koniec naciśnij klawisz Enter w ostatnim monicie, aby potwierdzić wybrane opcje.

    Podsumowanie danych wyjściowych będzie wyglądać podobnie do przedstawionego poniżej fragmentu kodu.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
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
    </pre>

---

::: zone-end
::: zone pivot="platform-linux"

### <a name="java-se"></a>[Java SE](#tab/javase)

1. Po wyświetleniu monitu z opcją **Subskrypcja** wybierz odpowiedni element `Subscription`, wprowadzając wartość liczbową na początku wiersza.
1. Po wyświetleniu monitu z opcją **aplikacji sieci Web** Zaakceptuj opcję domyślną, `<create>` naciskając klawisz ENTER lub wybierając istniejącą aplikację.
1. Po wyświetleniu monitu z opcją **systemu operacyjnego** wybierz pozycję **Linux** , naciskając klawisz ENTER.
1. Po wyświetleniu monitu z opcją **warstwy cenowej** wybierz pozycję **B2** , wprowadzając polecenie `2` .
1. Użyj domyślnej wersji języka Java, **Java 8**, naciskając klawisz ENTER.
1. Na koniec naciśnij klawisz Enter w ostatnim monicie, aby potwierdzić wybrane opcje.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
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
    </pre>

### <a name="tomcat"></a>[Tomcat](#tab/tomcat)

1. Po wyświetleniu monitu z opcją **Subskrypcja** wybierz odpowiedni element `Subscription`, wprowadzając wartość liczbową na początku wiersza.
1. Po wyświetleniu monitu z opcją **aplikacji sieci Web** Zaakceptuj opcję domyślną, `<create>` naciskając klawisz ENTER lub wybierając istniejącą aplikację.
1. Po wyświetleniu monitu z opcją **systemu operacyjnego** wybierz pozycję **Linux** , naciskając klawisz ENTER.
1. Po wyświetleniu monitu z opcją **warstwy cenowej** wybierz pozycję **B2** , wprowadzając polecenie `2` .
1. Użyj domyślnej wersji języka Java, **Java 8**, naciskając klawisz ENTER.
1. Użyj domyślnego kontenera sieci Web, **Tomcat 8,5**, naciskając klawisz ENTER.
1. Na koniec naciśnij klawisz Enter w ostatnim monicie, aby potwierdzić wybrane opcje.

    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
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
    </pre>

---

::: zone-end

Konfiguracje dla App Service można modyfikować bezpośrednio w `pom.xml` razie potrzeby. Poniżej wymieniono niektóre typowe:

Właściwość | Wymagane | Opis | Wersja
---|---|---|---
`<schemaVersion>` | fałsz | Określ wersję schematu konfiguracji. Obsługiwane są następujące wartości: `v1` , `v2` . | 1.5.2
`<subscriptionId>` | fałsz | Określ Identyfikator subskrypcji. | 0.1.0 +
`<resourceGroup>` | true | Azure <abbr title="Logiczny kontener dla powiązanych zasobów platformy Azure, którymi można zarządzać jako jednostką.">grupa zasobów</abbr> dla aplikacji sieci Web. | 0.1.0 +
`<appName>` | true | Nazwa aplikacji sieci Web. | 0.1.0 +
`<region>` | true | Określa region, w którym będzie hostowana aplikacja sieci Web; wartość domyślna to **westeurope**. Wszystkie prawidłowe regiony w sekcji [Obsługiwane regiony](https://azure.microsoft.com/global-infrastructure/services/?products=app-service) . | 0.1.0 +
`<pricingTier>` | fałsz | Warstwa cenowa aplikacji sieci Web. Wartość domyślna to **P1V2** dla obciążenia produkcyjnego, a **B2** jest zalecanym minimum dla środowiska Java Dev/Test. [Dowiedz się więcej](https://azure.microsoft.com/pricing/details/app-service/linux/)| 0.1.0 +
`<runtime>` | true | Konfiguracja środowiska uruchomieniowego, w [tym miejscu](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details)można zobaczyć szczegóły. | 0.1.0 +
`<deployment>` | true | W konfiguracji wdrożenia można zobaczyć szczegóły [tutaj](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Web-App:-Configuration-Details). | 0.1.0 +

Należy zachować ostrożność w przypadku wartości `<appName>` i `<resourceGroup>` ( `helloworld-1590394316693` i `helloworld-1590394316693-rg` odpowiednio w przykładowych danych wyjściowych), które będą używane później.

[zgłaszanie problemu](https://www.research.net/r/javae2e?tutorial=quickstart-java&step=config)

<br>
<hr/>

## <a name="4-deploy-the-app"></a>4. Wdróż aplikację

Wdróż swoją aplikację w języku Java na platformie Azure przy użyciu następującego polecenia.

```bash
mvn package azure-webapp:deploy
```

Po zakończeniu wdrażania aplikacja będzie gotowa `http://<appName>.azurewebsites.net/` . Otwórz adres URL za pomocą lokalnej przeglądarki sieci Web. Powinien zostać wyświetlony następujący ekran:

![Przykładowa aplikacja działająca w Azure App Service](./media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Gratulacje!** Twoja pierwsza aplikacja Java została wdrożona w celu App Service.

[zgłaszanie problemu](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

<br>
<hr/>

## <a name="5-clean-up-resources"></a>5. Wyczyść zasoby

Usuń aplikację Java i powiązane z nią zasoby, aby uniknąć ponoszenia dalszych kosztów.

```azurecli
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Wykonanie tego polecenia może potrwać około minutę.

<br>
<hr/>

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
