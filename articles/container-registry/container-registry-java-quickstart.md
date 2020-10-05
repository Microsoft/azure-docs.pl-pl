---
title: Szybki Start — tworzenie i wypychanie obrazów kontenerów Java do Azure Container Registry przy użyciu Maven i wysięgnik
description: Kompiluj aplikację w języku Java, a następnie Wypchnij ją do Azure Container Registry przy użyciu wtyczki Maven.
author: KarlErickson
ms.custom: devx-track-java, devx-track-azurecli
ms.author: karler
ms.topic: quickstart
ms.date: 02/26/2020
ms.openlocfilehash: 42200fe61b0ac73a63b9f8a3317687fac60c5c02
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88078584"
---
# <a name="quickstart-build-and-push-java-container-images-to-azure-container-registry"></a>Szybki Start: Tworzenie i wypychanie obrazów kontenera Java do Azure Container Registry

Ten przewodnik Szybki Start przedstawia sposób tworzenia aplikacji w języku Java i wypychania jej do Azure Container Registry przy użyciu wtyczki Maven. Korzystanie z Maven i wysięgnika to jeden przykład użycia narzędzi deweloperskich do współdziałania z usługą Azure Container Registry.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) lub utworzyć [bezpłatne konto platformy Azure](https://azure.microsoft.com/pricing/free-trial).
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/overview).
* Obsługiwany zestaw Java Development Kit (JDK). Aby uzyskać więcej informacji na temat zestawów JDK dostępnych do użycia podczas tworzenia aplikacji na platformie Azure, zobacz <https://aka.ms/azure-jdks>.
* Narzędzie kompilacji [Maven](http://maven.apache.org) firmy Apache (wersja 3 lub nowsza).
* Klient usługi [Git](https://git-scm.com).
* Klient platformy [Docker](https://www.docker.com).
* [Pomocnika poświadczeń platformy Docker ACR](https://github.com/Azure/acr-docker-credential-helper).

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Tworzenie aplikacji internetowej Spring Boot on Docker Getting Started

Poniższa procedura przeprowadzi Cię przez proces kompilowania aplikacji internetowej Spring Boot i testowania jej lokalnie.

1. W wierszu polecenia Użyj następującego polecenia, aby sklonować [sprężynę rozruchu na platformie docker wprowadzenie](https://github.com/spring-guides/gs-spring-boot-docker) przykładowym projekcie.

   ```bash
   git clone https://github.com/spring-guides/gs-spring-boot-docker.git
   ```

1. Przejdź do katalogu ukończonego projektu.

   ```bash
   cd gs-spring-boot-docker/complete
   ```

1. Za pomocą narzędzia Maven skompiluj i uruchom przykładową aplikację.

   ```bash
   mvn package spring-boot:run
   ```

1. Przetestuj aplikację internetową, przechodząc do witryny `http://localhost:8080` lub używając następującego polecenia `curl`:

   ```bash
   curl http://localhost:8080
   ```

Powinien zostać wyświetlony następujący komunikat: **Hello Docker World**

## <a name="create-an-azure-container-registry-using-the-azure-cli"></a>Tworzenie usługi Azure Container Registry za pomocą interfejsu wiersza polecenia platformy Azure

Następnie utworzysz grupę zasobów platformy Azure i ACR, wykonując następujące czynności:

1. Zaloguj się do konta platformy Azure przy użyciu następującego polecenia:

   ```azurecli
   az login
   ```

1. Określ subskrypcję platformy Azure do użycia:

   ```azurecli
   az account set -s <subscription ID>
   ```

1. Utwórz grupę zasobów na potrzeby zasobów platformy Azure używanych w tym samouczku. W poniższym poleceniu Pamiętaj, aby zastąpić symbole zastępcze własną nazwą zasobu i lokalizacją, taką jak `eastus` .

   ```azurecli
   az group create \
       --name=<your resource group name> \
       --location=<location>
   ```

1. Utwórz prywatny rejestr kontenerów platformy Azure w grupie zasobów przy użyciu następującego polecenia. Pamiętaj, aby zastąpić symbole zastępcze wartościami rzeczywistymi. Samouczek wypchnie później przykładową aplikację jako obraz platformy Docker do tego rejestru.

   ```azurecli
   az acr create \
       --resource-group <your resource group name> \
       --location <location> \
       --name <your registry name> \
       --sku Basic
   ```

## <a name="push-your-app-to-the-container-registry-via-jib"></a>Wypychanie aplikacji do rejestru kontenerów za pośrednictwem narzędzia Jib

Na koniec zaktualizujesz konfigurację projektu i użyjesz wiersza polecenia, aby skompilować i wdrożyć obraz.

> [!NOTE]
> Aby zalogować się do usługi Azure Container Registry, która została właśnie utworzona, należy uruchomić demona Docker. Aby zainstalować platformę Docker na maszynie, należy [zapoznać się z oficjalną dokumentacją platformy Docker](https://docs.docker.com/install/).

1. Zaloguj się do Azure Container Registry w interfejsie wiersza polecenia platformy Azure przy użyciu następującego polecenia. Pamiętaj, aby zastąpić symbol zastępczy własną nazwą rejestru.

   ```azurecli
   az configure --defaults acr=<your registry name>
   az acr login
   ```

   `az configure`Polecenie ustawia domyślną nazwę rejestru do użycia z `az acr` poleceniami.

1. Przejdź do katalogu ukończonego projektu aplikacji Spring Boot (na przykład „*C:\SpringBoot\gs-spring-boot-docker\complete*” lub „*/users/robert/SpringBoot/gs-spring-boot-docker/complete*”) i otwórz plik *pom.xml* w edytorze tekstu.

1. Zaktualizuj `<properties>` kolekcję w pliku *pom.xml* przy użyciu poniższego kodu XML. Zastąp symbol zastępczy nazwą rejestru, a następnie Dodaj `<jib-maven-plugin.version>` Właściwość z wartością `2.2.0` lub nowszą wersję programu [wysięgnik-Maven-plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin).

   ```xml
   <properties>
      <docker.image.prefix><your registry name>.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
      <jib-maven-plugin.version>2.2.0</jib-maven-plugin.version>
   </properties>
   ```

1. Zaktualizuj `<plugins>` kolekcję w pliku *pom.xml* , tak aby `<plugin>` element zawierał i wpis dla `jib-maven-plugin` , jak pokazano w poniższym przykładzie. Należy pamiętać, że korzystamy z obrazu podstawowego z Container Registry firmy Microsoft (MCR): `mcr.microsoft.com/java/jdk:8-zulu-alpine` , który zawiera oficjalnie obsługiwane JDK dla platformy Azure. W przypadku innych obrazów podstawowych MCR z oficjalnie obsługiwanymi zestawy JDK, zobacz [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk), [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre), [Java SE bezobsługowe środowisko JRE](https://hub.docker.com/_/microsoft-java-jre-headless)i [Java SE JDK i Maven](https://hub.docker.com/_/microsoft-java-maven).

   ```xml
   <plugin>
     <artifactId>jib-maven-plugin</artifactId>
     <groupId>com.google.cloud.tools</groupId>
     <version>${jib-maven-plugin.version}</version>
     <configuration>
        <from>
            <image>mcr.microsoft.com/java/jdk:8-zulu-alpine</image>
        </from>
        <to>
            <image>${docker.image.prefix}/${project.artifactId}</image>
        </to>
     </configuration>
   </plugin>
   ```

1. Przejdź do katalogu ukończonego projektu aplikacji Spring Boot i uruchom następujące polecenie, aby skompilować obraz i wypchnąć go do rejestru:

   ```bash
   az acr login && mvn compile jib:build
   ```

> [!NOTE]
>
> Ze względów bezpieczeństwa poświadczenie utworzone przez program `az acr login` jest ważne tylko przez 1 godzinę. Jeśli wystąpi błąd *401* , możesz `az acr login -n <your registry name>` ponownie uruchomić polecenie w celu ponownego uwierzytelnienia.

## <a name="verify-your-container-image"></a>Weryfikowanie obrazu kontenera

Gratulacje! Teraz masz wbudowaną kompilację aplikacji Java na platformie Azure obsługiwaną przez JDK do ACR. Teraz można testować obraz, wdrażając go do Azure App Service lub pobierając go do lokalnego z poleceniem (zastępując symbol zastępczy):

```bash
docker pull <your registry name>.azurecr.io/gs-spring-boot-docker
```

## <a name="next-steps"></a>Następne kroki

W przypadku innych wersji oficjalnych obrazów podstawowych obsługiwanych przez firmę Microsoft należy zapoznać się z tematem:

* [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk)
* [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre)
* [Java SE — bezobsługowy JRE](https://hub.docker.com/_/microsoft-java-jre-headless)
* [Java SE JDK i Maven](https://hub.docker.com/_/microsoft-java-maven)

Aby dowiedzieć się więcej na temat oprogramowania Spring i platformy Azure, przejdź do centrum dokumentacji dotyczącej oprogramowania Spring na platformie Azure.

> [!div class="nextstepaction"]
> [Oprogramowanie Spring na platformie Azure](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>Dodatkowe zasoby

Więcej informacji można znaleźć w następujących zasobach:

* [Platforma Azure dla deweloperów języka Java](/azure/java)
* [Praca z narzędziami Azure DevOps i językiem Java](/azure/devops/java)
* [Spring Boot na platformie Docker — Wprowadzenie](https://spring.io/guides/gs/spring-boot-docker)
* [Spring Initializr](https://start.spring.io)
* [Deploy a Spring Boot Application to the Azure App Service](/azure/developer/java/spring-framework/deploy-spring-boot-java-app-from-container-registry-using-maven-plugin) (Wdrażanie aplikacji Spring Boot w usłudze Azure App Service)
* [Używanie niestandardowego obrazu Docker dla usługi Azure Web App w systemie Linux](../app-service/tutorial-custom-container.md)
