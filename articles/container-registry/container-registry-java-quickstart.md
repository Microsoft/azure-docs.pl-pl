---
title: Szybki start — kompilowanie i wypychanie obrazów kontenerów języka Java do Azure Container Registry przy użyciu narzędzi Maven i Jib
description: Skompilowanie konteneryzowanej aplikacji Java i wypchnie ją do Azure Container Registry za pomocą wtyczki Jib narzędzia Maven.
author: KarlErickson
ms.author: karler
ms.date: 02/26/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
- devx-track-azurecli
- mode-api
ms.openlocfilehash: 4d805458d90c73de879a9b87d5b08c98a8f1a250
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107537299"
---
# <a name="quickstart-build-and-push-java-container-images-to-azure-container-registry"></a>Szybki start: kompilowanie i wypychanie obrazów kontenerów języka Java do Azure Container Registry

W tym przewodniku Szybki start przedstawiono sposób kompilowania konteneryzowanej aplikacji Java i wypychania jej do środowiska Azure Container Registry za pomocą wtyczki Jib narzędzia Maven. Użycie narzędzi Maven i Jib jest jednym z przykładów użycia narzędzi dewelopera do interakcji z rejestrem kontenerów platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) lub utworzyć [bezpłatne konto platformy Azure](https://azure.microsoft.com/pricing/free-trial).
* Interfejs [wiersza polecenia Command-Line Azure.](/cli/azure/overview)
* Obsługiwany zestaw Java Development Kit (JDK). Aby uzyskać więcej informacji na temat zestawów JDK dostępnych do użycia podczas tworzenia aplikacji na platformie Azure, zobacz <https://aka.ms/azure-jdks>.
* Narzędzie kompilacji [Maven platformy](http://maven.apache.org) Apache (wersja 3 lub nowsza).
* Klient usługi [Git](https://git-scm.com).
* Klient platformy [Docker](https://www.docker.com).
* Pomocnik [poświadczeń platformy Docker usługi ACR](https://github.com/Azure/acr-docker-credential-helper).

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Tworzenie aplikacji internetowej Spring Boot on Docker Getting Started

Poniższa procedura przeprowadzi Cię przez proces kompilowania aplikacji internetowej Spring Boot i testowania jej lokalnie.

1. W wierszu polecenia użyj następującego polecenia, aby sklonować Spring Boot [na Wprowadzenie do platformy Docker.](https://github.com/spring-guides/gs-spring-boot-docker)

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

Następnie utworzysz grupę zasobów platformy Azure i usługę ACR, korzystając z następujących kroków:

1. Zaloguj się do konta platformy Azure przy użyciu następującego polecenia:

   ```azurecli
   az login
   ```

1. Określ subskrypcję platformy Azure do użycia:

   ```azurecli
   az account set -s <subscription ID>
   ```

1. Utwórz grupę zasobów na potrzeby zasobów platformy Azure używanych w tym samouczku. W poniższym poleceniu zastąp symbole zastępcze własną nazwą zasobu i lokalizacją, taką jak `eastus` .

   ```azurecli
   az group create \
       --name=<your resource group name> \
       --location=<location>
   ```

1. Utwórz prywatny rejestr kontenerów platformy Azure w grupie zasobów przy użyciu następującego polecenia. Pamiętaj, aby zastąpić symbole zastępcze rzeczywistymi wartościami. Samouczek wypchnie później przykładową aplikację jako obraz platformy Docker do tego rejestru.

   ```azurecli
   az acr create \
       --resource-group <your resource group name> \
       --location <location> \
       --name <your registry name> \
       --sku Basic
   ```

## <a name="push-your-app-to-the-container-registry-via-jib"></a>Wypychanie aplikacji do rejestru kontenerów za pośrednictwem narzędzia Jib

Na koniec zaktualizujesz konfigurację projektu i użyjesz wiersza polecenia do skompilowania i wdrożenia obrazu.

> [!NOTE]
> Aby zalogować się do utworzonego rejestru kontenerów platformy Azure, musisz mieć uruchomiony demon platformy Docker. Aby zainstalować aplikację Docker na swojej maszynie, [oto oficjalna dokumentacja platformy Docker.](https://docs.docker.com/install/)

1. Zaloguj się do swojego konta Azure Container Registry interfejsie wiersza polecenia platformy Azure przy użyciu następującego polecenia. Pamiętaj, aby zastąpić symbol zastępczy własną nazwą rejestru.

   ```azurecli
   az configure --defaults acr=<your registry name>
   az acr login
   ```

   Polecenie `az configure` ustawia domyślną nazwę rejestru do użycia z `az acr` poleceniami .

1. Przejdź do katalogu ukończonego projektu aplikacji Spring Boot (na przykład „*C:\SpringBoot\gs-spring-boot-docker\complete*” lub „*/users/robert/SpringBoot/gs-spring-boot-docker/complete*”) i otwórz plik *pom.xml* w edytorze tekstu.

1. Zaktualizuj `<properties>` kolekcję w pliku *pom.xml* następującym kodem XML. Zastąp symbol zastępczy nazwą rejestru i dodaj właściwość z wartością lub nowszą wersją `<jib-maven-plugin.version>` `2.2.0` wtyczki [jib-maven-plugin](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin).

   ```xml
   <properties>
      <docker.image.prefix><your registry name>.azurecr.io</docker.image.prefix>
      <java.version>1.8</java.version>
      <jib-maven-plugin.version>2.2.0</jib-maven-plugin.version>
   </properties>
   ```

1. Zaktualizuj kolekcję w plikupom.xmltak, aby element zawierał wpis i `<plugins>` dla elementu , jak  `<plugin>` `jib-maven-plugin` pokazano w poniższym przykładzie. Należy pamiętać, że używamy obrazu podstawowego z zestawu Microsoft Container Registry (MCR): , który zawiera oficjalnie obsługiwany zestaw `mcr.microsoft.com/java/jdk:8-zulu-alpine` JDK dla platformy Azure. Inne obrazy podstawowe MCR z oficjalnie obsługiwanymi kodami JDK można znaleźć w tematach [Java SE JDK,](https://hub.docker.com/_/microsoft-java-jdk) [Java SE JRE,](https://hub.docker.com/_/microsoft-java-jre) [Java SE Headless JRE](https://hub.docker.com/_/microsoft-java-jre-headless)oraz [Java SE JDK i Maven.](https://hub.docker.com/_/microsoft-java-maven)

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
> Ze względów bezpieczeństwa poświadczenie utworzone przez `az acr login` program jest ważne tylko przez 1 godzinę. Jeśli zostanie wyświetlony błąd *401 Brak* autoryzacji, możesz ponownie uruchomić polecenie w `az acr login -n <your registry name>` celu ponownego uwierzytelnienia.

## <a name="verify-your-container-image"></a>Weryfikowanie obrazu kontenera

Gratulacje! Teraz masz skompilowanie konteneryzowanej aplikacji Java na obsługiwanym przez platformę Azure pliku JDK wypchniętą do usługi ACR. Teraz możesz przetestować obraz, wdrażając go w Azure App Service lub ściągając go do lokalizacji lokalnej za pomocą polecenia (zastępując symbol zastępczy):

```bash
docker pull <your registry name>.azurecr.io/gs-spring-boot-docker
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać inne wersje oficjalnych obrazów podstawowych Java obsługiwanych przez firmę Microsoft, zobacz:

* [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk)
* [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre)
* [Java SE Headless JRE](https://hub.docker.com/_/microsoft-java-jre-headless)
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
* [Deploy a Spring Boot Application to the Azure App Service](/azure/developer/java/spring-framework/deploy-spring-boot-java-app-on-linux#configure-maven-to-build-image-to-your-azure-container-registry) (Wdrażanie aplikacji Spring Boot w usłudze Azure App Service)
* [Używanie niestandardowego obrazu Docker dla usługi Azure Web App w systemie Linux](../app-service/tutorial-custom-container.md)
