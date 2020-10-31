---
title: Konfigurowanie środowiska deweloperskiego w systemie macOS
description: Zainstaluj środowisko uruchomieniowe, zestaw SDK i narzędzia oraz utwórz lokalny klaster projektowy. Po ukończeniu tej konfiguracji będziesz gotowy do kompilowania aplikacji w witrynie macOS.
ms.topic: conceptual
ms.date: 10/16/2020
ms.custom: devx-track-js
ms.openlocfilehash: d08046c8f29901dd9650a1edc886efa2ff226e00
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93086781"
---
# <a name="set-up-your-development-environment-on-mac-os-x"></a>Konfigurowanie środowiska projektowego w systemie Mac OS X
> [!div class="op_single_selector"]
> * [Windows](service-fabric-get-started.md)
> * [Linux](service-fabric-get-started-linux.md)
> * [Mac OS X](service-fabric-get-started-mac.md)

Przy użyciu systemu Mac OS X można kompilować aplikacje usługi Azure Service Fabric, aby działały w klastrach systemu Linux. W tym dokumencie opisano, jak skonfigurować komputer Mac na potrzeby projektowania.

## <a name="prerequisites"></a>Wymagania wstępne
Usługa Azure Service Fabric nie działa natywnie w systemie Mac OS X. Aby można było uruchomić lokalny klaster usługi Service Fabric, udostępniono wstępnie skonfigurowany obraz kontenera platformy Docker. Przed rozpoczęciem potrzebne są następujące elementy:

* Co najmniej 4 GB pamięci RAM.
* Najnowsza wersja platformy [Docker](https://www.docker.com/).

>[!TIP]
>
>Aby zainstalować platformę Docker na komputerze Mac, wykonaj czynności opisane w [dokumentacji platformy Docker](https://docs.docker.com/docker-for-mac/install/#what-to-know-before-you-install). Po zainstalowaniu platformy [zweryfikuj instalację](https://docs.docker.com/docker-for-mac/#check-versions-of-docker-engine-compose-and-machine).
>

## <a name="create-a-local-container-and-set-up-service-fabric"></a>Tworzenie kontenera lokalnego i konfigurowanie usługi Service Fabric
Aby skonfigurować lokalny kontener platformy Docker i uruchomić w nim klaster usługi Service Fabric, wykonaj następujące czynności:

1. Zaktualizuj konfigurację demona platformy Docker na swoim hoście za pomocą następujących ustawień i ponownie uruchom demona platformy Docker: 

    ```json
    {
        "ipv6": true,
        "fixed-cidr-v6": "fd00::/64"
    }
    ```
    Możesz zaktualizować te ustawienia bezpośrednio w pliku daemon.json w ścieżce instalacji platformy Docker. Ustawienia konfiguracji demona można modyfikować bezpośrednio w programie Docker. Wybierz **ikonę platformy Docker** , a następnie wybierz pozycje **Preferencje** > **Demon** > **Zaawansowane** .
    
    >[!NOTE]
    >
    >Zaleca się modyfikowanie demona bezpośrednio w programie Docker, ponieważ lokalizacja daemon.jsw pliku może się różnić od maszyny do komputera. Przykład: ~/Library/Containers/com.docker.docker/Data/database/com.docker.driver.amd64-linux/etc/docker/daemon.json.
    >

    >[!TIP]
    >Zalecamy zwiększenie zasobów przydzielonych do platformy Docker podczas testowania dużych aplikacji. W tym celu można wybrać **ikonę platformy Docker** , a następnie wybrać pozycję **Zaawansowane** , aby dostosować liczbę rdzeni i ilość pamięci.

2. Uruchom klaster.<br/>
    <b>Ubuntu 18,04 LTS:</b>
    ```bash
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u18
    ```

    <b>Ubuntu 16,04 LTS:</b>
    ```bash
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u16
    ```

    >[!TIP]
    > Domyślnie zostanie ściągnięty obraz z najnowszą wersją usługi Service Fabric. W przypadku poszczególnych poprawek odwiedź stronę [Service Fabric jednopunktowy](https://hub.docker.com/_/microsoft-service-fabric-onebox) w usłudze Docker Hub.



3. Opcjonalne: Tworzenie obrazu rozszerzonego Service Fabric.

    W nowym katalogu Utwórz plik o nazwie `Dockerfile` do skompilowania niestandardowego obrazu:

    >[!NOTE]
    >Możesz dostosować obraz powyżej za pomocą pliku dockerfile, aby dodać dodatkowe programy lub zależności do kontenera.
    >Na przykład dodanie elementu `RUN apt-get install nodejs -y` umożliwi obsługę aplikacji `nodejs` jako plików wykonywalnych gościa.
    ```Dockerfile
    FROM mcr.microsoft.com/service-fabric/onebox:u18
    RUN apt-get install nodejs -y
    EXPOSE 19080 19000 80 443
    WORKDIR /home/ClusterDeployer
    CMD ["./ClusterDeployer.sh"]
    ```
    
    >[!TIP]
    > Domyślnie zostanie ściągnięty obraz z najnowszą wersją usługi Service Fabric. W przypadku poszczególnych poprawek odwiedź stronę usługi [Docker Hub](https://hub.docker.com/r/microsoft/service-fabric-onebox/) .

    Aby skompilować obraz do wielokrotnego użytku z programu `Dockerfile` , Otwórz Terminal i `cd` bezpośrednio przytrzymując `Dockerfile` następnie uruchom następujące polecenie:

    ```bash 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >Ta operacja może zająć pewien czas, ale trzeba ją wykonać tylko raz.

    Teraz można szybko uruchomić lokalną kopię Service Fabric, gdy będzie potrzebna, uruchamiając:

    ```bash 
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >Określ nazwę wystąpienia kontenera, aby usprawnić jego obsługę. 
    >
    >Jeśli Twoja aplikacja nasłuchuje na określonych portach, należy je określić za pomocą dodatkowych tagów `-p`. Jeśli na przykład aplikacja nasłuchuje na porcie 8080, dodaj następujący tag `-p`:
    >
    >`docker run -itd -p 19000:19000 -p 19080:19080 -p 8080:8080 --name sfonebox mcr.microsoft.com/service-fabric/onebox:u18`
    >

4. Uruchomienie klastra zajmie trochę czasu. Gdy jest uruchomiony, można wyświetlić dzienniki przy użyciu poniższego polecenia lub przejść do pulpitu nawigacyjnego, aby wyświetlić kondycję klastrów: `http://localhost:19080`

    ```bash 
    docker logs sftestcluster
    ```


5. Aby zatrzymać i oczyścić kontener, użyj następującego polecenia. Jednak będziemy używać tego kontenera w następnym kroku.

    ```bash 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Znane ograniczenia 
 
 Poniżej przedstawiono znane ograniczenia dotyczące klastrów lokalnych działających w kontenerze na komputerach Mac: 
 
 * Usługa DNS nie działa i nie jest obecnie obsługiwana w kontenerze. [#132 problemu](https://github.com/Microsoft/service-fabric/issues/132)
 * Uruchomione aplikacje oparte na kontenerach wymagają uruchomienia SF na hoście z systemem Linux. Zagnieżdżone aplikacje kontenera nie są obecnie obsługiwane.

## <a name="set-up-the-service-fabric-cli-sfctl-on-your-mac"></a>Konfigurowanie interfejsu wiersza polecenia usługi Service Fabric (sfctl) na komputerze Mac

Postępuj zgodnie z instrukcjami w temacie [Interfejs wiersza polecenia usługi Service Fabric](service-fabric-cli.md#cli-mac), aby zainstalować interfejs wiersza polecenia usługi Service Fabric (`sfctl`) na komputerze Mac.
Polecenia interfejsu wiersza polecenia obsługują interakcję z jednostkami usługi Service Fabric, w tym klastrami, aplikacjami i usługami.

1. Aby połączyć się z klastrem przed wdrożeniem aplikacji, uruchom poniższe polecenie. 

```bash
sfctl cluster select --endpoint http://localhost:19080
```

## <a name="create-your-application-on-your-mac-by-using-yeoman"></a>Tworzenie aplikacji na komputerze Mac przy użyciu narzędzia Yeoman

Usługa Service Fabric udostępnia narzędzia do tworzenia szkieletów, które ułatwiają tworzenie aplikacji usługi Service Fabric z poziomu terminalu przy użyciu generatora szablonów narzędzia Yeoman. Wykonaj poniższe czynności, aby upewnić się, że generator szablonów narzędzia Yeoman usługi Service Fabric działa na Twojej maszynie:

1. Na komputerze Mac muszą być zainstalowane środowisko Node.js i narzędzie Node Package Manager (NPM). Oprogramowanie można zainstalować za pomocą rozwiązania [HomeBrew](https://brew.sh/) w następujący sposób:

    ```bash
    brew install node
    node -v
    npm -v
    ```
2. Zainstaluj generator szablonów narzędzia [Yeoman](https://yeoman.io/) na swojej maszynie z poziomu narzędzia NPM:

    ```bash
    npm install -g yo
    ```
3. Zainstaluj generator Yeoman, którego chcesz używać, zgodnie z instrukcjami podanymi w [dokumentacji](service-fabric-get-started-linux.md#set-up-yeoman-generators-for-containers-and-guest-executables) wprowadzającej. Aby utworzyć aplikacje usługi Service Fabric za pomocą narzędzia Yeoman, wykonaj następujące czynności:

    ```bash
    npm install -g generator-azuresfjava       # for Service Fabric Java Applications
    npm install -g generator-azuresfguest      # for Service Fabric Guest executables
    npm install -g generator-azuresfcontainer  # for Service Fabric Container Applications
    ```
4. Po zainstalowaniu generatorów utwórz plik wykonywalny gościa lub usługi kontenera, uruchamiając odpowiednio polecenie `yo azuresfguest` lub `yo azuresfcontainer`.

5. Aby można było skompilować aplikację Java usługi Service Fabric na komputerze Mac, na maszynie hosta muszą być zainstalowane zestaw JDK 1.8 i narzędzie Gradle. Oprogramowanie można zainstalować za pomocą rozwiązania [HomeBrew](https://brew.sh/) w następujący sposób: 

    ```bash
    brew update
    brew cask install java
    brew install gradle
    ```

    > [!IMPORTANT]
    > Bieżące wersje programu `brew cask install java` mogą instalować nowszą wersję programu JDK.
    > Pamiętaj, aby zainstalować program JDK 8.

## <a name="deploy-your-application-on-your-mac-from-the-terminal"></a>Wdrażanie aplikacji na komputerze Mac z poziomu terminalu

Po utworzenia i skompilowaniu aplikacji usługi Service Fabric możesz wdrożyć ją przy użyciu [interfejsu wiersza polecenia usługi Service Fabric](service-fabric-cli.md#cli-mac):

1. Połącz się z klastrem usługi Service Fabric działającym wewnątrz wystąpienia kontenera na komputerze Mac:

    ```bash
    sfctl cluster select --endpoint http://localhost:19080
    ```

2. Uruchom skrypt instalacji z poziomu katalogu projektu:

    ```bash
    cd MyProject
    bash install.sh
    ```

## <a name="set-up-net-core-31-development"></a>Skonfiguruj Programowanie na platformie .NET Core 3,1

Zainstaluj [zestaw SDK platformy .NET Core 3,1 dla komputerów Mac](https://www.microsoft.com/net/core#macos) , aby rozpocząć [Tworzenie aplikacji w języku C# Service Fabric](service-fabric-create-your-first-linux-application-with-csharp.md). Pakiety dla aplikacji .NET Core Service Fabric są hostowane w witrynie NuGet.org.

## <a name="install-the-service-fabric-plug-in-for-eclipse-on-your-mac"></a>Instalowanie wtyczki usługi Service Fabric dla środowiska Eclipse na komputerze Mac

Usługa Azure Service Fabric udostępnia wtyczkę środowiska Eclipse Neon (lub nowszą) dla środowiska IDE Java. Wtyczka upraszcza proces tworzenia, kompilowania i wdrażania usług Java. Aby zainstalować wtyczkę usługi Service Fabric dla środowiska Eclipse lub zaktualizować ją do najnowszej wersji, wykonaj [te czynności](service-fabric-get-started-eclipse.md#install-or-update-the-service-fabric-plug-in-in-eclipse). Możesz też wykonać inne procedury przedstawione w [dokumentacji usługi Service Fabric dla środowiska Eclipse](service-fabric-get-started-eclipse.md): kompilowanie aplikacji, dodawanie usługi do aplikacji, odinstalowywanie aplikacji itp.

Ostatnim krokiem jest utworzenie wystąpienia kontenera ze ścieżką współużytkowaną z hostem. Wtyczka wymaga tego typu wystąpienia na potrzeby pracy z kontenerem platformy Docker na komputerze Mac. Przykład:

```bash
docker run -itd -p 19080:19080 -v /Users/sayantan/work/workspaces/mySFWorkspace:/tmp/mySFWorkspace --name sfonebox mcr.microsoft.com/service-fabric/onebox:latest
```

Zdefiniowane są następujące atrybuty:
* `/Users/sayantan/work/workspaces/mySFWorkspace` to w pełni kwalifikowana ścieżka obszaru roboczego na komputerze Mac.
* `/tmp/mySFWorkspace` to ścieżka wewnątrz kontenera, na którą ma zostać zamapowana ścieżka obszaru roboczego.

>[!NOTE]
> 
>Jeśli obszar roboczy ma inną nazwę/ścieżkę, zaktualizuj te wartości w poleceniu `docker run`.
> 
>Jeśli uruchamiasz kontener o innej nazwie niż `sfonebox`, zaktualizuj wartość nazwy w pliku testclient.sh w aplikacji Java usługi Service Fabric aktora.
>

## <a name="next-steps"></a>Następne kroki
<!-- Links -->
* [Create and deploy your first Service Fabric Java application on Linux using Yeoman](service-fabric-create-your-first-linux-application-with-java.md) (Tworzenie i wdrażanie pierwszej aplikacji Java usługi Service Fabric w systemie Linux przy użyciu programu Yeoman)
* [Create and deploy your first Service Fabric Java application on Linux using Service Fabric Plugin for Eclipse (Tworzenie i wdrażanie pierwszej aplikacji Java usługi Service Fabric w systemie Linux przy użyciu wtyczki usługi Service Fabric dla środowiska Eclipse)](service-fabric-get-started-eclipse.md)
* [Tworzenie klastra usługi Service Fabric w witrynie Azure Portal](service-fabric-cluster-creation-via-portal.md)
* [Tworzenie klastra usługi Service Fabric przy użyciu usługi Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
* [Informacje o modelu aplikacji usługi Service Fabric](service-fabric-application-model.md)
* [Use the Service Fabric CLI to manage your applications](service-fabric-application-lifecycle-sfctl.md) (Zarządzanie aplikacjami przy użyciu interfejsu wiersza polecenia usługi Service Fabric)
* [Przygotowywanie środowiska projektowego systemu Linux w systemie Windows](service-fabric-local-linux-cluster-windows.md)

<!-- Images -->
[cluster-setup-script]: ./media/service-fabric-get-started-mac/cluster-setup-mac.png
[sfx-mac]: ./media/service-fabric-get-started-mac/sfx-mac.png
[sf-eclipse-plugin-install]: ./media/service-fabric-get-started-mac/sf-eclipse-plugin-install.png
[buildship-update]: https://projects.eclipse.org/projects/tools.buildship
