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
ms.openlocfilehash: 6681b2688c7e8884a197ebe27fb784b1a195f4b5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81732162"
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
mvn archetype:generate -DgroupId=example.demo -DartifactId=helloworld -DarchetypeArtifactId=maven-archetype-webapp
```

## <a name="configure-the-maven-plugin"></a>Konfigurowanie wtyczki Maven

Aby dokonać wdrożenia z systemu Maven, użyj edytora kodu w usłudze Cloud Shell, aby otworzyć plik `pom.xml` projektu w katalogu `helloworld`. 

```bash
code pom.xml
```

Następnie dodaj następującą definicję wtyczki w elemencie `<build>` pliku `pom.xml`.

```xml
<plugins>
    <!--*************************************************-->
    <!-- Deploy to Tomcat in App Service Windows         -->
    <!--*************************************************-->
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.9.0</version>
        <configuration>
            <!-- Specify v2 schema -->
            <schemaVersion>v2</schemaVersion>
            <!-- App information -->
            <subscriptionId>SUBSCRIPTION_ID</subscriptionId>
            <resourceGroup>RESOURCEGROUP_NAME</resourceGroup>
            <appName>WEBAPP_NAME</appName>
            <region>REGION</region>
            <!-- Java Runtime Stack for App Service on Windows-->
            <runtime>
                <os>windows</os>
                <javaVersion>1.8</javaVersion>
                <webContainer>tomcat 9.0</webContainer>
            </runtime>
            <deployment>
                <resources>
                    <resource>
                        <directory>${project.basedir}/target</directory>
                        <includes>
                            <include>*.war</include>
                        </includes>
                    </resource>
                </resources>
            </deployment>
        </configuration>
    </plugin>
</plugins>
```

> [!NOTE]
> W tym artykule pracujemy tylko z aplikacjami w języku Java umieszczonych w pakietach w postaci plików WAR. Wtyczka obsługuje również aplikacje internetowe JAR. Odwiedź stronę [Deploy a Java SE JAR file to App Service on Linux](https://docs.microsoft.com/java/azure/spring-framework/deploy-spring-boot-java-app-with-maven-plugin?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) (Wdrażanie pliku JAR języka Java SE do usługi App Service w systemie Linux), aby wypróbować tę funkcję.


Zaktualizuj następujące symbole zastępcze w konfiguracji wtyczki:

| Symbol zastępczy | Opis |
| ----------- | ----------- |
| `SUBSCRIPTION_ID` | Unikatowy identyfikator subskrypcji, w której ma zostać wdrożona aplikacja. Identyfikator domyślnej subskrypcji można znaleźć w Cloud Shell lub interfejsie wiersza `az account show` polecenia przy użyciu poleceń. Dla wszystkich dostępnych subskrypcji Użyj `az account list` polecenia.|
| `RESOURCEGROUP_NAME` | Nazwa nowej grupy zasobów, w której ma zostać utworzona aplikacja. Dzięki wprowadzeniu wszystkich zasobów dla aplikacji do grupy można nimi zarządzać jednocześnie. Na przykład usunięcie grupy zasobów spowodowałoby usunięcie wszystkich zasobów skojarzonych z aplikacją. Zaktualizuj tę wartość przy użyciu unikatowej nazwy nowej grupy zasobów, na przykład grupa *zasobów*. Za pomocą tej nazwy grupy zasobów wyczyścisz wszystkie zasoby platformy Azure w późniejszej sekcji. |
| `WEBAPP_NAME` | Nazwa aplikacji będzie częścią nazwy hosta aplikacji po wdrożeniu na platformie Azure (WEBAPP_NAME. azurewebsites. NET). Zaktualizuj tę wartość przy użyciu unikatowej nazwy nowej aplikacji usługi App Service, która będzie hostem aplikacji Java, na przykład *contoso*. |
| `REGION` | Region świadczenia usługi Azure, w którym aplikacja jest hostowana, na przykład *westus2*. Listę regionów można uzyskać z usługi Cloud Shell lub interfejsu wiersza polecenia przy użyciu polecenia `az account list-locations`. |

## <a name="deploy-the-app"></a>Wdrażanie aplikacji

Wdróż aplikację Java na platformie Azure za pomocą następującego polecenia:

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
