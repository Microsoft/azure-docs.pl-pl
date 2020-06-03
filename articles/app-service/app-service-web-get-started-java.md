---
title: 'Szybki Start: Tworzenie aplikacji Java w systemie Windows'
description: Wdróż swoje pierwsze Hello world Java, aby Azure App Service w systemie Windows w ciągu kilku minut. Wtyczka aplikacji sieci Web platformy Azure dla usługi Maven umożliwia wdrażanie aplikacji języka Java.
keywords: Azure, App Service, Web App, Windows, Java, Maven, szybki start
author: msangapu-msft
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 05/29/2019
ms.author: jafreebe
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: b07ffe92a5dd0c105188fab55bc679c04f660ed2
ms.sourcegitcommit: d118ad4fb2b66c759b70d4d8a18e6368760da3ad
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/02/2020
ms.locfileid: "84300964"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-windows"></a>Szybki Start: Tworzenie aplikacji Java na Azure App Service w systemie Windows

> [!NOTE]
> W tym artykule opisano wdrażanie aplikacji w usłudze App Service w systemie Windows. Aby wdrożyć program w celu App Service w systemie _Linux_, zobacz [Tworzenie aplikacji sieci Web Java w systemie Linux](./containers/quickstart-java.md).
>

[Azure App Service](overview.md) zapewnia wysoce skalowalną, samoobsługową usługę hostingu w sieci Web.  W tym przewodniku szybki start pokazano, jak używać [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) z [wtyczką aplikacji sieci Web platformy Azure dla usługi Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) w celu wdrożenia pliku archiwum sieci Web (War) języka Java.

> [!NOTE]
> Ten sam element można także wykonać przy użyciu popularnych środowisk IDE, takich jak IntelliJ i zaćmienie. Zapoznaj się z naszymi dokumentami w [Azure Toolkit for IntelliJ przewodniku szybki start](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app) lub [Azure Toolkit for Eclipse przewodnika Szybki Start](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app).
>
![Przykładowa aplikacja działająca w Azure App Service](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Tworzenie aplikacji w języku Java

Wykonaj następujące polecenie narzędzia Maven w wierszu polecenia usługi Cloud Shell, aby utworzyć nową aplikację o nazwie `helloworld`:

```bash
mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp -Dversion=1.0-SNAPSHOT
```

Następnie zmień katalog roboczy na folder projektu:

```bash
cd helloworld
```

## <a name="configure-the-maven-plugin"></a>Konfigurowanie wtyczki Maven

Można uruchomić następujące polecenie Maven w wierszu polecenia, aby skonfigurować wdrożenie, wybrać **"2"** dla **systemu operacyjnego Windows** w pierwszym kroku, a następnie zaakceptować konfigurację domyślną, naciskając klawisz **Enter** do momentu uzyskania monitu o **potwierdzenie (t/N)** , a następnie nacisnąć klawisz **"Y"** i konfigurację. 

```bash
mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
```

Przykładowy proces wygląda następująco:

```cmd
~@Azure:~/helloworld$ mvn com.microsoft.azure:azure-webapp-maven-plugin:1.9.1:config
[INFO] Scanning for projects...
[INFO]
[INFO] ----------------------< example.demo:helloworld >-----------------------
[INFO] Building helloworld Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
[INFO] --- azure-webapp-maven-plugin:1.9.1:config (default-cli) @ helloworld ---
[WARNING] The plugin may not work if you change the os of an existing webapp.
Define value for OS(Default: Linux):
1. linux [*]
2. windows
3. docker
Enter index to use: 2
Define value for javaVersion(Default: 1.8): 
1. 1.7
2. 1.7.0_191_ZULU
3. 1.7.0_51
4. 1.7.0_71
5. 1.7.0_80
6. 1.8 [*]
7. 1.8.0_102
8. 1.8.0_111
9. 1.8.0_144
10. 1.8.0_172
11. 1.8.0_172_ZULU
12. 1.8.0_181
13. 1.8.0_181_ZULU
14. 1.8.0_202
15. 1.8.0_202_ZULU
16. 1.8.0_25
17. 1.8.0_60
18. 1.8.0_73
19. 1.8.0_92
20. 11
21. 11.0.2_ZULU
Enter index to use:
Define value for webContainer(Default: tomcat 8.5): 
1. jetty 9.1
2. jetty 9.1.0.20131115
3. jetty 9.3
4. jetty 9.3.13.20161014
5. tomcat 7.0
6. tomcat 7.0.50
7. tomcat 7.0.62
8. tomcat 8.0
9. tomcat 8.0.23
10. tomcat 8.5 [*]
11. tomcat 8.5.20
12. tomcat 8.5.31
13. tomcat 8.5.34
14. tomcat 8.5.37
15. tomcat 8.5.6
16. tomcat 9.0
17. tomcat 9.0.0
18. tomcat 9.0.12
19. tomcat 9.0.14
20. tomcat 9.0.8
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1590394316693
ResourceGroup : helloworld-1590394316693-rg
Region : westeurope
PricingTier : PremiumV2_P1v2
OS : Windows
Java : 1.8
WebContainer : tomcat 8.5
Deploy to slot : false
Confirm (Y/N)? :
[INFO] Saving configuration to pom.
```

> [!NOTE]
> W tym artykule pracujemy tylko z aplikacjami w języku Java umieszczonych w pakietach w postaci plików WAR. Wtyczka obsługuje również aplikacje internetowe JAR. Odwiedź stronę [Deploy a Java SE JAR file to App Service on Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) (Wdrażanie pliku JAR języka Java SE do usługi App Service w systemie Linux), aby wypróbować tę funkcję.

Otwórz do `pom.xml` , aby wyświetlić zaktualizowaną konfigurację.

```bash
code pom.xml
```

W razie potrzeby można modyfikować konfiguracje dla App Service bezpośrednio w pliku pliku pom:

 Właściwość | Wymagane | Opis | Wersja
---|---|---|---
`<schemaVersion>` | fałsz | Określ wersję schematu konfiguracji. Obsługiwane są następujące wartości: `v1` , `v2` . | 1.5.2
`<resourceGroup>` | true | Grupa zasobów platformy Azure dla aplikacji sieci Web. | 0.1.0 +
`<appName>` | true | Nazwa aplikacji sieci Web. | 0.1.0 +
`<region>` | true | Określa region, w którym będzie hostowana aplikacja sieci Web; wartość domyślna to **westeurope**. Wszystkie prawidłowe regiony w sekcji [Obsługiwane regiony](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) . | 0.1.0 +
`<pricingTier>` | fałsz | Warstwa cenowa aplikacji sieci Web. Wartość domyślna to **P1V2**.| 0.1.0 +
`<runtime>` | true | Konfiguracja środowiska uruchomieniowego, w [tym miejscu](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)można zobaczyć szczegóły. | 0.1.0 +
`<deployment>` | true | W konfiguracji wdrożenia można zobaczyć szczegóły [tutaj](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | 0.1.0 +

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=app-service-web-get-started-java&step=config)

## <a name="deploy-the-app"></a>Wdrażanie aplikacji

Proces wdrażania do Azure App Service używa poświadczeń konta z interfejsu wiersza polecenia platformy Azure. Przed kontynuowaniem [Zaloguj się przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) .

```azurecli
az login
```
Następnie możesz wdrożyć aplikację Java na platformie Azure przy użyciu następującego polecenia:

```bash
mvn package azure-webapp:deploy
```

Po zakończeniu wdrażania w przeglądarce internetowej przejdź do wdrożonej aplikacji, używając następującego adresu URL, na przykład `http://<webapp>.azurewebsites.net/`.

![Przykładowa aplikacja działająca w Azure App Service](./media/app-service-web-get-started-java/java-hello-world-in-browser-azure-app-service.png)

**Gratulacje!** Twoja pierwsza aplikacja Java została wdrożona w celu App Service w systemie Windows.

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z usługą Azure SQL Database przy użyciu języka Java](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z usługą Azure DB for MySQL za pomocą języka Java](/azure/mysql/connect-java)

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z usługą Azure DB for PostgreSQL za pomocą języka Java](/azure/postgresql/connect-java)

> [!div class="nextstepaction"]
> [Zasoby dla deweloperów platformy Azure dla języka Java](/java/azure/)

> [!div class="nextstepaction"]
> [Mapowanie domeny niestandardowej](app-service-web-tutorial-custom-domain.md)

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wtyczkach Maven dla platformy Azure](https://github.com/microsoft/azure-maven-plugins)
