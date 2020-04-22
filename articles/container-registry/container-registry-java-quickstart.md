---
title: Szybki start — tworzenie i wypychanie obrazów kontenerów Java do rejestru kontenerów platformy Azure przy użyciu maven i jib
description: Tworzenie konteneryzowanej aplikacji Java i wypychanie jej do rejestru kontenerów platformy Azure przy użyciu wtyczki Maven Jib.
author: KarlErickson
ms.author: karler
ms.topic: quickstart
ms.date: 02/26/2020
ms.openlocfilehash: ef933ae9a6b0a34529c7ec145b13c023728a3457
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81731856"
---
# <a name="quickstart-build-and-push-java-container-images-to-azure-container-registry"></a>Szybki start: tworzenie i wypychanie obrazów kontenerów Java do rejestru kontenerów platformy Azure

Ten przewodnik Szybki start pokazuje, jak utworzyć konteneryzowaną aplikację Java i wypchnąć ją do rejestru kontenerów platformy Azure przy użyciu wtyczki Maven Jib. Użycie Maven i Jib jest jednym z przykładów korzystania z narzędzi deweloperskich do interakcji z rejestrem kontenerów platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz jeszcze subskrypcji platformy Azure, możesz aktywować [korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) lub utworzyć [bezpłatne konto platformy Azure](https://azure.microsoft.com/pricing/free-trial).
* [Interfejs wiersza polecenia platformy Azure](/cli/azure/overview).
* Obsługiwany zestaw Java Development Kit (JDK). Aby uzyskać więcej informacji na temat zestawów JDK dostępnych do użycia podczas tworzenia aplikacji na platformie Azure, zobacz <https://aka.ms/azure-jdks>.
* Narzędzie do budowania [Maven](http://maven.apache.org) firmy Apache (wersja 3 lub wyższa).
* Klient usługi [Git](https://git-scm.com).
* Klient platformy [Docker](https://www.docker.com).
* [Pomocnik poświadczeń platformy ACR Docker](https://github.com/Azure/acr-docker-credential-helper).

## <a name="create-the-spring-boot-on-docker-getting-started-web-app"></a>Tworzenie aplikacji internetowej Spring Boot on Docker Getting Started

Poniższa procedura przeprowadzi Cię przez proces kompilowania aplikacji internetowej Spring Boot i testowania jej lokalnie.

1. W wierszu polecenia użyj następującego polecenia, aby sklonować [wiosenny rozruch w przykładowym projekcie docker wprowadzenie.](https://github.com/spring-guides/gs-spring-boot-docker)

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

Następnie utworzysz grupę zasobów platformy Azure i usługę ACR, wykonując następujące kroki:

1. Zaloguj się do konta platformy Azure przy użyciu następującego polecenia:

   ```azurecli
   az login
   ```

1. Określ subskrypcję platformy Azure do użycia:

   ```azurecli
   az account set -s <subscription ID>
   ```

1. Utwórz grupę zasobów na potrzeby zasobów platformy Azure używanych w tym samouczku. W poniższym poleceniu należy zastąpić symbole zastępcze własną nazwą `eastus`zasobu i lokalizacją, taką jak .

   ```azurecli
   az group create \
       --name=<your resource group name> \
       --location=<location>
   ```

1. Utwórz prywatny rejestr kontenerów platformy Azure w grupie zasobów, używając następującego polecenia. Pamiętaj, aby zastąpić symbole zastępcze wartościami rzeczywistymi. Samouczek wypchnie później przykładową aplikację jako obraz platformy Docker do tego rejestru.

   ```azurecli
   az acr create \
       --resource-group <your resource group name> \
       --location <location> \
       --name <your registry name> \
       --sku Basic
   ```

## <a name="push-your-app-to-the-container-registry-via-jib"></a>Wypychanie aplikacji do rejestru kontenerów za pośrednictwem narzędzia Jib

Na koniec zaktualizujesz konfigurację projektu i użyjesz wiersza polecenia do tworzenia i wdrażania obrazu.

1. Zaloguj się do rejestru kontenerów platformy Azure z interfejsu wiersza polecenia platformy Azure przy użyciu następującego polecenia. Pamiętaj, aby zastąpić symbol zastępczy własną nazwą rejestru.

   ```azurecli
   az configure --defaults acr=<your registry name>
   az acr login
   ```

   Polecenie `az configure` ustawia domyślną nazwę rejestru `az acr` do użycia z poleceniami.

1. Przejdź do katalogu ukończonego projektu aplikacji Spring Boot (na przykład „*C:\SpringBoot\gs-spring-boot-docker\complete*” lub „*/users/robert/SpringBoot/gs-spring-boot-docker/complete*”) i otwórz plik *pom.xml* w edytorze tekstu.

1. Zaktualizuj kolekcję `<properties>` w pliku *pom.xml* za pomocą następującego kodu XML. Zastąp symbol zastępczy nazwą `<jib-maven-plugin.version>` rejestru i zaktualizuj wartość najnowszą wersją [wtyczki wysięgnika.](https://github.com/GoogleContainerTools/jib/tree/master/jib-maven-plugin)

   ```xml
   <properties>
      <docker.image.prefix><your registry name>.azurecr.io</docker.image.prefix>
      <jib-maven-plugin.version>1.8.0</jib-maven-plugin.version>
      <java.version>1.8</java.version>
   </properties>
   ```

1. Zaktualizuj kolekcję `<plugins>` w pliku *pom.xml,* tak aby `<plugin>` element zawierał i wpisowywał `jib-maven-plugin`dla , jak pokazano w poniższym przykładzie. Należy zauważyć, że używamy obrazu podstawowego z rejestru `mcr.microsoft.com/java/jdk:8-zulu-alpine`kontenerów firmy Microsoft (MCR): , który zawiera oficjalnie obsługiwany zestaw JDK dla platformy Azure. Zobacz też: [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk), [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre), Java SE JRE , [Java SE Headless JRE](https://hub.docker.com/_/microsoft-java-jre-headless)oraz [Java SE JDK i Maven](https://hub.docker.com/_/microsoft-java-maven).

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
   mvn compile jib:build
   ```

> [!NOTE]
>
> Ze względów bezpieczeństwa `az acr login` poświadczenia utworzone przez jest prawidłowy tylko dla 1 godziny. Jeśli zostanie wyświetlony błąd *401 Unauthorized,* możesz ponownie uruchomić `az acr login -n <your registry name>` polecenie, aby ponownie uwierzytelnić.

## <a name="verify-your-container-image"></a>Weryfikowanie obrazu kontenera

Gratulacje! Teraz masz konteneryzowany aplikacji Java kompilacji na platformie Azure obsługiwane JDK wypchnięte do ACR. Teraz można przetestować obraz, wdrażając go w usłudze Azure App Service lub ściągając go do lokalnego za pomocą polecenia (zastępując symbol zastępczy):

```bash
docker pull <your registry name>.azurecr.io/gs-spring-boot-docker:v1
```

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z innymi wersjami oficjalnych obrazów podstawowych java obsługiwanych przez firmę Microsoft, zobacz:

* [Java SE JDK](https://hub.docker.com/_/microsoft-java-jdk)
* [Java SE JRE](https://hub.docker.com/_/microsoft-java-jre)
* [Java SE bezgłowy JRE](https://hub.docker.com/_/microsoft-java-jre-headless)
* [Java SE JDK i Maven](https://hub.docker.com/_/microsoft-java-maven)

Aby dowiedzieć się więcej na temat oprogramowania Spring i platformy Azure, przejdź do centrum dokumentacji dotyczącej oprogramowania Spring na platformie Azure.

> [!div class="nextstepaction"]
> [Oprogramowanie Spring na platformie Azure](/azure/developer/java/spring-framework)

### <a name="additional-resources"></a>Dodatkowe zasoby

Więcej informacji zawierają następujące zasoby:

* [Platforma Azure dla deweloperów języka Java](/azure/java)
* [Praca z narzędziami Azure DevOps i językiem Java](/azure/devops/java)
* [Spring Boot na platformie Docker — Wprowadzenie](https://spring.io/guides/gs/spring-boot-docker)
* [Spring Initializr](https://start.spring.io)
* [Deploy a Spring Boot Application to the Azure App Service](/azure/developer/java/spring-framework/deploy-spring-boot-java-app-from-container-registry-using-maven-plugin) (Wdrażanie aplikacji Spring Boot w usłudze Azure App Service)
* [Używanie niestandardowego obrazu Docker dla usługi Azure Web App w systemie Linux](/azure/app-service-web/app-service-linux-using-custom-docker-image)
