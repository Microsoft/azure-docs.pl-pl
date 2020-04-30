---
title: 'Szybki Start: Tworzenie aplikacji Java dla systemu Linux'
description: Zacznij korzystać z aplikacji systemu Linux na Azure App Service, wdrażając pierwszą aplikację Java do kontenera systemu Linux w programie App Service.
keywords: Azure, App Service, Web App, Linux, Java, Maven, szybki start
author: msangapu-msft
ms.assetid: 582bb3c2-164b-42f5-b081-95bfcb7a502a
ms.devlang: Java
ms.topic: quickstart
ms.date: 03/27/2019
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 8f2e99ffc9f9ee5c5553e8d933d82f83999c8ab2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81732895"
---
# <a name="quickstart-create-a-java-app-on-azure-app-service-on-linux"></a>Szybki Start: Tworzenie aplikacji Java na Azure App Service w systemie Linux

Usługa [App Service w systemie Linux](app-service-linux-intro.md) oferuje wysoce skalowalną i samonaprawialną usługę hostingu w Internecie przy użyciu systemu operacyjnego Linux. W tym przewodniku szybki start pokazano, jak używać [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) z [wtyczką aplikacji sieci Web platformy Azure dla programu Maven](https://github.com/Microsoft/azure-maven-plugins/tree/develop/azure-webapp-maven-plugin) w celu wdrożenia pliku archiwum sieci Web (War) języka Java w systemie operacyjnym Linux.

> [!NOTE]
>
> Ten sam element można także wykonać przy użyciu popularnych środowisk IDE, takich jak IntelliJ, zaćmienie i VS Code. Zapoznaj się z naszymi dokumentami na [Azure Toolkit for IntelliJ przewodniku szybki start](/java/azure/intellij/azure-toolkit-for-intellij-create-hello-world-web-app), [Azure Toolkit for Eclipse przewodniku szybki start](/java/azure/eclipse/azure-toolkit-for-eclipse-create-hello-world-web-app) [vs Code](https://code.visualstudio.com/docs/java/java-webapp)lub przewodniku Szybki Start.
>
![Przykładowa aplikacja działająca w Azure App Service](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="create-a-java-app"></a>Tworzenie aplikacji w języku Java

Wykonaj następujące polecenie narzędzia Maven w wierszu polecenia usługi Cloud Shell, aby utworzyć nową aplikację o nazwie `helloworld`:

```bash
mvn archetype:generate "-DgroupId=example.demo" "-DartifactId=helloworld" "-DarchetypeArtifactId=maven-archetype-webapp"
```
Następnie zmień katalog roboczy na folder projektu:

```bash
cd helloworld
```

## <a name="configure-the-maven-plugin"></a>Konfigurowanie wtyczki Maven

Proces wdrażania do Azure App Service używa poświadczeń konta z interfejsu wiersza polecenia platformy Azure. Przed kontynuowaniem [Zaloguj się przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli?view=azure-cli-latest) .

```azurecli
az login
```

Następnie możesz skonfigurować wdrożenie, uruchomić polecenie Maven w wierszu polecenia i użyć konfiguracji domyślnych, naciskając klawisz **Enter** do momentu uzyskania monitu o **potwierdzenie (t/N)** , a następnie naciśnij klawisz **"Y"** , a konfiguracja zostanie ukończona. 
```cmd
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
Enter index to use:
Define value for javaVersion(Default: jre8):
1. Java 11
2. Java 8 [*]
Enter index to use:
Define value for runtimeStack(Default: TOMCAT 8.5):
1. TOMCAT 9.0
2. TOMCAT 8.5 [*]
Enter index to use:
Please confirm webapp properties
AppName : helloworld-1558400876966
ResourceGroup : helloworld-1558400876966-rg
Region : westeurope
PricingTier : Premium_P1V2
OS : Linux
RuntimeStack : TOMCAT 8.5-jre8
Deploy to slot : false
Confirm (Y/N)? : Y
```

> [!NOTE]
> W tym artykule pracujemy tylko z aplikacjami w języku Java umieszczonych w pakietach w postaci plików WAR. Wtyczka obsługuje również aplikacje internetowe JAR. Odwiedź stronę [Deploy a Java SE JAR file to App Service on Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) (Wdrażanie pliku JAR języka Java SE do usługi App Service w systemie Linux), aby wypróbować tę funkcję.

Przejdź `pom.xml` ponownie, aby zobaczyć, że konfiguracja wtyczki została zaktualizowana, w razie potrzeby można zmodyfikować inne konfiguracje dla App Service bezpośrednio w pliku pliku pom:

 Właściwość | Wymagany | Opis | Wersja
---|---|---|---
`<schemaVersion>` | fałsz | Określ wersję schematu konfiguracji. Obsługiwane są następujące wartości `v1`: `v2`,. | 1.5.2
`<resourceGroup>` | true | Grupa zasobów platformy Azure dla aplikacji sieci Web. | 0.1.0 +
`<appName>` | true | Nazwa aplikacji sieci Web. | 0.1.0 +
`<region>` | true | Określa region, w którym będzie hostowana aplikacja sieci Web; wartość domyślna to **westeurope**. Wszystkie prawidłowe regiony w sekcji [Obsługiwane regiony](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) . | 0.1.0 +
`<pricingTier>` | fałsz | Warstwa cenowa aplikacji sieci Web. Wartość domyślna to **P1V2**.| 0.1.0 +
`<runtime>` | true | Konfiguracja środowiska uruchomieniowego, w [tym miejscu](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme)można zobaczyć szczegóły. | 0.1.0 +
`<deployment>` | true | W konfiguracji wdrożenia można zobaczyć szczegóły [tutaj](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme). | 0.1.0 +

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=config)

## <a name="deploy-the-app"></a>Wdrażanie aplikacji

Wdróż aplikację Java na platformie Azure za pomocą następującego polecenia:

```bash
mvn package azure-webapp:deploy
```

Po zakończeniu wdrażania w przeglądarce internetowej przejdź do wdrożonej aplikacji, używając następującego adresu URL, na przykład `http://<webapp>.azurewebsites.net`. 

![Przykładowa aplikacja działająca w Azure App Service](media/quickstart-java/java-hello-world-in-browser-azure-app-service.png)

**Gratulacje!** Udało Ci się wdrożyć pierwszą własną aplikację w języku Java w usłudze App Service w systemie Linux.

> [!div class="nextstepaction"]
> [Wystąpił problem](https://www.research.net/r/javae2e?tutorial=app-service-linux-quickstart&step=deploy)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

W poprzednich krokach utworzono zasoby platformy Azure w grupie zasobów. Jeśli nie chcesz potrzebować tych zasobów w przyszłości, Usuń grupę zasobów z portalu lub uruchamiając następujące polecenie w Cloud Shell:

```azurecli-interactive
az group delete --name <your resource group name; for example: helloworld-1558400876966-rg> --yes
```

Wykonanie tego polecenia może potrwać około minutę.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z usługą Azure SQL Database przy użyciu języka Java](/azure/sql-database/sql-database-connect-query-java?toc=%2Fazure%2Fjava%2Ftoc.json)

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z usługą Azure DB for MySQL za pomocą języka Java](/azure/mysql/connect-java)

> [!div class="nextstepaction"]
> [Nawiązywanie połączenia z usługą Azure DB for PostgreSQL za pomocą języka Java](/azure/postgresql/connect-java)

> [!div class="nextstepaction"]
> [Konfigurowanie aplikacji Java](configure-custom-container.md)

> [!div class="nextstepaction"]
> [Ciągła integracja/ciągłe wdrażanie przy użyciu narzędzia Jenkins](/azure/jenkins/deploy-jenkins-app-service-plugin)

> [!div class="nextstepaction"]
> [Inne zasoby platformy Azure dla deweloperów języka Java](/java/azure/)

> [!div class="nextstepaction"]
> [Dowiedz się więcej o wtyczkach Maven dla platformy Azure](https://github.com/microsoft/azure-maven-plugins)
