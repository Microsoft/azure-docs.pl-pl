---
title: Konfigurowanie klastra usługi Azure Service Fabric Linux w systemie Windows
description: W tym artykule opisano sposób konfigurowania klastrów Service Fabric Linux działających na maszynach deweloperskich systemu Windows. Takie podejście jest przydatne do tworzenia aplikacji międzyplatformowych.
ms.topic: conceptual
ms.date: 10/16/2020
ms.openlocfilehash: 7b25a84e76773baea9f17430df1b7ba13aa661aa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93087081"
---
# <a name="set-up-a-linux-service-fabric-cluster-on-your-windows-developer-machine"></a>Konfigurowanie klastra Service Fabric z systemem Linux na komputerze dewelopera systemu Windows

W tym dokumencie opisano sposób konfigurowania lokalnego klastra Service Fabric systemu Linux na komputerze deweloperskim z systemem Windows. Skonfigurowanie lokalnego klastra z systemem Linux jest przydatne do szybkiego testowania aplikacji przeznaczonych dla klastrów systemu Linux, które są opracowywane na komputerze z systemem Windows.

## <a name="prerequisites"></a>Wymagania wstępne
Klastry Service Fabric oparte na systemie Linux nie działają w systemach Windows, ale aby umożliwić tworzenie prototypów dla wielu platform, podano Service Fabric kontener platformy Docker z systemem Linux, który można wdrożyć za pośrednictwem Docker for Windows.

Przed rozpoczęciem potrzebne są następujące elementy:

* Co najmniej 4 GB pamięci RAM
* Najnowsza wersja [Docker for Windows](https://store.docker.com/editions/community/docker-ce-desktop-windows)
* Platforma Docker musi być uruchomiona w trybie kontenerów systemu Linux

>[!TIP]
> Aby zainstalować platformę Docker na komputerze z systemem Windows, wykonaj kroki opisane w [dokumentacji platformy Docker](https://store.docker.com/editions/community/docker-ce-desktop-windows/plans/docker-ce-desktop-windows-tier?tab=instructions). Po zainstalowaniu platformy [zweryfikuj instalację](https://docs.docker.com/docker-for-windows/#check-versions-of-docker-engine-compose-and-machine).
>

## <a name="create-a-local-container-and-setup-service-fabric"></a>Tworzenie kontenera lokalnego i konfigurowanie usługi Service Fabric
Aby skonfigurować lokalny kontener platformy Docker i uruchomić na nim klaster Service Fabric, uruchom następujące kroki:


1. Zaktualizuj konfigurację demona platformy Docker na swoim hoście za pomocą następujących ustawień i ponownie uruchom demona platformy Docker: 

    ```json
    {
      "ipv6": true,
      "fixed-cidr-v6": "2001:db8:1::/64"
    }
    ```
    Zalecaną metodą aktualizacji jest przejście do: 

    * Ikona platformy Docker > ustawienia > aparat platformy Docker
    * Dodaj nowe pola wymienione powyżej
    * Zastosuj & ponownie uruchom ponownie demona platformy Docker, aby zmiany zaczęły obowiązywać.

2. Uruchom klaster za pośrednictwem programu PowerShell.<br/>
    <b>Ubuntu 18,04 LTS:</b>
    ```powershell
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u18
    ```

    <b>Ubuntu 16,04 LTS:</b>
    ```powershell
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mcr.microsoft.com/service-fabric/onebox:u16
    ```

    >[!TIP]
    > Domyślnie zostanie ściągnięty obraz z najnowszą wersją usługi Service Fabric. W przypadku poszczególnych poprawek Zobacz stronę [Service Fabric jednopunktowy](https://hub.docker.com/_/microsoft-service-fabric-onebox) w usłudze Docker Hub.



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

    ```powershell 
    docker build -t mysfcluster .
    ```
    
    >[!NOTE]
    >Ta operacja może zająć pewien czas, ale trzeba ją wykonać tylko raz.

    Teraz można szybko uruchomić lokalną kopię Service Fabric, gdy będzie potrzebna, uruchamiając:

    ```powershell 
    docker run --name sftestcluster -d -v /var/run/docker.sock:/var/run/docker.sock -p 19080:19080 -p 19000:19000 -p 25100-25200:25100-25200 mysfcluster
    ```

    >[!TIP]
    >Określ nazwę wystąpienia kontenera, aby usprawnić jego obsługę. 
    >
    >Jeśli Twoja aplikacja nasłuchuje na określonych portach, należy je określić za pomocą dodatkowych tagów `-p`. Jeśli na przykład aplikacja nasłuchuje na porcie 8080, dodaj następujący tag `-p`:
    >
    >`docker run -itd -p 19000:19000 -p 19080:19080 -p 8080:8080 --name sfonebox mcr.microsoft.com/service-fabric/onebox:u18`
    >


4. Klaster zostanie uruchomiony po krótkim czasie. Możesz wyświetlić dzienniki przy użyciu poniższego polecenia lub przejść do pulpitu nawigacyjnego, aby wyświetlić kondycję klastrów `http://localhost:19080`:

    ```powershell 
    docker logs sftestcluster
    ```

5. Po pomyślnym wdrożeniu klastra zgodnie z opisem w kroku 4 możesz przejść do pozycji ``http://localhost:19080`` z komputera z systemem Windows, aby znaleźć pulpit nawigacyjny Service Fabric Explorer. W tym momencie można nawiązać połączenie z tym klastrem przy użyciu narzędzi z komputera dewelopera systemu Windows i wdrożyć aplikacje przeznaczone dla klastrów Service Fabric Linux. 

    > [!NOTE]
    > Wtyczka Eclipse obecnie nie jest obsługiwana w systemie Windows. 

6. Gdy wszystko będzie gotowe, Zatrzymaj i wyczyść kontener za pomocą tego polecenia:

    ```powershell 
    docker rm -f sftestcluster
    ```

### <a name="known-limitations"></a>Znane ograniczenia 
 
 Poniżej przedstawiono znane ograniczenia dotyczące klastrów lokalnych działających w kontenerze na komputerach Mac: 
 
 * Usługa DNS nie działa i nie jest obecnie obsługiwana w kontenerze. [#132 problemu](https://github.com/Microsoft/service-fabric/issues/132)
 * Uruchomione aplikacje oparte na kontenerach wymagają uruchomienia SF na hoście z systemem Linux. Zagnieżdżone aplikacje kontenera nie są obecnie obsługiwane.

## <a name="next-steps"></a>Następne kroki
* [Create and deploy your first Service Fabric Java application on Linux using Yeoman](service-fabric-create-your-first-linux-application-with-java.md) (Tworzenie i wdrażanie pierwszej aplikacji Java usługi Service Fabric w systemie Linux przy użyciu programu Yeoman)
* Wprowadzenie do [Przezaćmienia](./service-fabric-get-started-eclipse.md)
* Zapoznaj się z innymi [przykładami języka Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)
* Uzyskaj informacje o [opcjach pomocy technicznej usługi Service Fabric](service-fabric-support.md)


<!-- Image references -->

[publishdialog]: ./media/service-fabric-manage-multiple-environment-app-configuration/publish-dialog-choose-app-config.png
[app-parameters-solution-explorer]:./media/service-fabric-manage-multiple-environment-app-configuration/app-parameters-in-solution-explorer.png
