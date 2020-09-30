---
title: Konfigurowanie klastra usługi Azure Service Fabric Linux w systemie Windows
description: W tym artykule opisano sposób konfigurowania klastrów Service Fabric Linux działających na maszynach deweloperskich systemu Windows. Jest to szczególnie przydatne w przypadku tworzenia aplikacji międzyplatformowych.
ms.topic: conceptual
ms.date: 11/20/2017
ms.openlocfilehash: 83d494d777a4a1e1586707c8848056ca8fe9780a
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91537076"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Konfigurowanie klastra Service Fabric z systemem Linux na komputerze dewelopera systemu Windows

W tym dokumencie opisano sposób konfigurowania lokalnego Service Fabric systemu Linux na maszynach deweloperskich z systemem Windows. Skonfigurowanie lokalnego klastra z systemem Linux jest przydatne do szybkiego testowania aplikacji przeznaczonych dla klastrów systemu Linux, które są opracowywane na komputerze z systemem Windows.

## <a name="prerequisites"></a>Wymagania wstępne
Klastry Service Fabric oparte na systemie Linux nie działają w sposób natywny dla systemu Windows. Aby uruchomić lokalny klaster Service Fabric, zostanie udostępniony wstępnie skonfigurowany obraz kontenera Docker. Przed rozpoczęciem potrzebne są następujące elementy:

* Co najmniej 4 GB pamięci RAM
* Najnowsza wersja platformy [Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* Platforma Docker musi być uruchomiona w trybie systemu Linux

>[!TIP]
> * Aby zainstalować platformę Docker w systemie Windows, można wykonać kroki opisane w oficjalnej [dokumentacji](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions) platformy Docker. 
> * Po zakończeniu instalacji upewnij się, że została ona przeprowadzona pomyślnie, wykonując kroki opisane [tutaj](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine).


## <a name="create-a-local-container-and-setup-service-fabric"></a>Tworzenie kontenera lokalnego i konfigurowanie usługi Service Fabric
Aby skonfigurować lokalny kontener platformy Docker i uruchomić na nim klaster usługi Service Fabric, wykonaj następujące kroki w programie PowerShell:


1. Zaktualizuj konfigurację demona platformy Docker na swoim hoście za pomocą następujących ustawień i ponownie uruchom demona platformy Docker: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    Zalecaną metodą aktualizacji jest przejście do ikony platformy Docker > ustawienia > demona > Advanced i aktualizowanie jej w tym miejscu. Następnie ponownie uruchom demona platformy Docker, aby zmiany zaczęły obowiązywać. 

2. W nowym katalogu utwórz plik o nazwie `Dockerfile` w celu skompilowania obrazu usługi Service Fabric:

    ```Dockerfile
    FROM mcr.microsoft.com/service-fabric/onebox:latest
    WORKDIR /home/ClusterDeployer
    RUN ./setup.sh
    #Generate the local
    RUN locale-gen en_US.UTF-8
    #Set environment variables
    ENV LANG=en_US.UTF-8
    ENV LANGUAGE=en_US:en
    ENV LC_ALL=en_US.UTF-8
    EXPOSE 19080 19000 80 443
    #Start SSH before running the cluster
    CMD /etc/init.d/ssh start && ./run.sh
    ```

    >[!NOTE]
    >Możesz dostosować ten plik, aby dodać dodatkowe programy lub zależności do kontenera.
    >Na przykład dodanie elementu `RUN apt-get install nodejs -y` umożliwi obsługę aplikacji `nodejs` jako plików wykonywalnych gościa.
    
    >[!TIP]
    > Domyślnie zostanie ściągnięty obraz z najnowszą wersją usługi Service Fabric. Poszczególne wersje są dostępne na stronie [usługi Docker Hub](https://hub.docker.com/r/microsoft/service-fabric-onebox/)

3. Aby skompilować obraz do wielokrotnego użytku na podstawie pliku `Dockerfile`, otwórz terminal i element `cd` w lokalizacji bezpośrednio przechowującej plik `Dockerfile`, a następnie uruchom kod:

    ```powershell 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >Ta operacja może zająć pewien czas, ale trzeba ją wykonać tylko raz.

4. Teraz możesz szybko w razie potrzeby uruchomić kopię lokalną usługi Service Fabric, uruchamiając kod:

    ```powershell 
    docker run --name sftestcluster -d -v //var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >Określ nazwę wystąpienia kontenera, aby usprawnić jego obsługę. 
    >
    >Jeśli Twoja aplikacja nasłuchuje na określonych portach, należy je określić za pomocą dodatkowych tagów `-p`. Jeśli na przykład aplikacja nasłuchuje na porcie 8080, dodaj następujący tag `-p`:
    >
    >`docker run -itd -p 19080:19080 -p 8080:8080 --name sfonebox mcr.microsoft.com/service-fabric/onebox:latest`
    >

5. Klaster zostanie uruchomiony po krótkim czasie. Możesz wyświetlić dzienniki przy użyciu poniższego polecenia lub przejść do pulpitu nawigacyjnego, aby wyświetlić kondycję klastrów `http://localhost:19080`:

    ```powershell 
    docker logs sftestcluster
    ```

6. Po pomyślnym ukończeniu kroku 5 możesz przejść do ``http://localhost:19080`` okna z systemem Windows, aby zobaczyć eksploratora Service Fabric. W tym momencie można nawiązać połączenie z tym klastrem za pomocą dowolnych narzędzi z komputera dewelopera systemu Windows i wdrożyć aplikacje przeznaczone dla klastrów Service Fabric Linux. 

    > [!NOTE]
    > Wtyczka Eclipse obecnie nie jest obsługiwana w systemie Windows. 

7. Gdy skończysz, Zatrzymaj i oczyść kontener za pomocą tego polecenia:

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Znane ograniczenia 
 
 Poniżej przedstawiono znane ograniczenia dotyczące klastrów lokalnych działających w kontenerze na komputerach Mac: 
 
 * Usługa DNS nie działa i nie jest obsługiwana: [problem #132](https://github.com/Microsoft/service-fabric/issues/132)

## <a name="next-steps"></a>Następne kroki
* Wprowadzenie do [Przezaćmienia](./service-fabric-get-started-eclipse.md)
* Zapoznaj się z innymi [przykładami języka Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
