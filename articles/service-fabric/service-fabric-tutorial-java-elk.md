---
title: Monitoruj aplikacje w Service Fabric przy użyciu ELK na platformie Azure
description: W tym samouczku przedstawiono informacje na temat konfigurowania rozwiązania ELK i monitorowania aplikacji usługi Service Fabric.
ms.topic: tutorial
ms.date: 02/26/2018
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 5faf26230618161a5b908c9a544a43ec5c33b807
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91532026"
---
# <a name="tutorial-monitor-your-service-fabric-applications-using-elk"></a>Samouczek: monitorowanie aplikacji usługi Service Fabric przy użyciu rozwiązania ELK

Niniejszy samouczek jest czwartą częścią serii. Przedstawiono w nim sposób używania rozwiązania ELK (Elasticsearch Logstash i Kibana) do monitorowania aplikacji usługi Service Fabric działających na platformie Azure.

Część czwarta serii zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Konfigurowanie serwera ELK na platformie Azure
> * Konfigurowanie usługi Logstash w celu odbierania dzienników z usługi Event Hubs
> * Wizualizowanie dzienników platformy i aplikacji w rozwiązaniu Kibana

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * [Kompilowanie aplikacji Java usług Reliable Services w usłudze Service Fabric](service-fabric-tutorial-create-java-app.md)
> * [Wdrażanie i debugowanie aplikacji w klastrze lokalnym](service-fabric-tutorial-debug-log-local-cluster.md)
> * [Wdrażanie aplikacji w klastrze platformy Azure](service-fabric-tutorial-java-deploy-azure.md)
> * Konfigurowanie monitorowania i diagnostyki dla aplikacji
> * [Konfigurowanie ciągłej integracji/ciągłego dostarczania](service-fabric-tutorial-java-jenkins.md)

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Konfigurowanie aplikacji do emitowania dzienników do lokalizacji określonej w [części drugiej](service-fabric-tutorial-debug-log-local-cluster.md).
* Wykonaj czynności z [części trzeciej](service-fabric-tutorial-java-deploy-azure.md), aby skonfigurować uruchomiony klaster usługi Service Fabric do wysyłania dzienników do usługi Event Hubs.
* Zasady w usłudze Event Hubs mają uprawnienia do nasłuchiwania oraz skojarzony klucz podstawowy z trzeciej części serii.

## <a name="download-the-voting-sample-application"></a>Pobieranie przykładowej aplikacji do głosowania

Jeśli nie skompilowano przykładowej aplikacji do głosowania w [pierwszej części tej serii samouczków](service-fabric-tutorial-create-java-app.md), można ją pobrać. W oknie polecenia uruchom następujące polecenie, aby sklonować przykładowe repozytorium aplikacji na komputer lokalny.

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart
```

## <a name="create-an-elk-server-in-azure"></a>Tworzenie serwera ELK na platformie Azure

W tym samouczku możesz używać wstępnie skonfigurowanego środowiska ELK. Jeśli już takie masz, przejdź do sekcji **Konfigurowanie rozwiązania Logstash**. W przeciwnym razie wykonaj poniższe kroki w celu utworzenia go na platformie Azure.

1. Utwórz serwer ELK certyfikowany przez bibliotekę [Bitnami](https://ms.portal.azure.com/#create/bitnami.elk4-6) na platformie Azure. W przypadku tego samouczka nie ma żadnych szczególnych specyfikacji wymaganych do utworzenia tego serwera.

2. Przejdź do zasobu w witrynie Azure Portal, a następnie przejdź na kartę **Diagnostyka rozruchu** w sekcji **Pomoc techniczna i rozwiązywanie problemów**. Następnie kliknij kartę **Dziennik magistrali szeregowej**.

    ![Diagnostyka rozruchu](./media/service-fabric-tutorial-java-elk/bootdiagnostics.png)
3. Wyszukaj w dziennikach hasło wymagane do uzyskania dostępu do wystąpienia rozwiązania Kibana. Jest on podobny do następującego fragmentu kodu:

    ```bash
    [   25.932766] bitnami[1496]: #########################################################################
    [   25.948656] bitnami[1496]: #                                                                       #
    [   25.962448] bitnami[1496]: #        Setting Bitnami application password to '[PASSWORD]'           #
    [   25.978137] bitnami[1496]: #        (the default application username is 'user')                   #
    [   26.004770] bitnami[1496]: #                                                                       #
    [   26.029413] bitnami[1496]: #########################################################################
    ```

4. Naciśnij przycisk Połącz na stronie przeglądu serwera w witrynie Azure Portal w celu uzyskania szczegółowych danych logowania.

    ![Połączenie maszyny wirtualnej](./media/service-fabric-tutorial-java-elk/vmconnection.png)

5. Użyj protokołu SSH, aby połączyć się z serwerem obsługującym obraz ELK za pomocą poniższego polecenia

    ```bash
    ssh [USERNAME]@[CONNECTION-IP-OF-SERVER]

    Example: ssh testaccount@104.40.63.157
    ```

## <a name="set-up-elk"></a>Konfigurowanie serwera ELK

1. Pierwszy krok to załadowanie środowiska ELK

    ```bash
    sudo /opt/bitnami/use_elk
    ```

2. Jeśli używasz istniejącego środowiska, musisz uruchomić poniższe polecenie, aby zatrzymać usługę Logstash

    ```bash
    sudo /opt/bitnami/ctlscript.sh stop logstash
    ```

3. Uruchom poniższe polecenie, aby zainstalować wtyczkę Logstash dla usługi Event Hubs.

    ```bash
    logstash-plugin install logstash-input-azureeventhub
    ```

4. Utwórz lub zmodyfikuj istniejący plik konfiguracji Logstash z następującą zawartością: w przypadku korzystania z obrazu ELK Bitnami na platformie Azure plik należy utworzyć w lokalizacji ```/opt/bitnami/logstash/conf/access-log.conf```.

    ```json
    input
    {
        azureeventhub
        {
            key => "[RECEIVER-POLICY-KEY-FOR-EVENT-HUB]"
            username => "[RECEIVER-POLICY-NAME]"
            namespace => "[EVENTHUB-NAMESPACENAME]"
            eventhub => "[EVENTHUB-NAME]"
            partitions => 4
        }
    }

    output {
         elasticsearch {
             hosts => [ "127.0.0.1:9200" ]
         }
     }
    ```

5. Aby zweryfikować konfigurację, uruchom następujące polecenie:

    ```bash
    /opt/bitnami/logstash/bin/logstash -f /opt/bitnami/logstash/conf/ --config.test_and_exit
    ```

6. Uruchom usługę Logstash

    ```bash
    sudo /opt/bitnami/ctlscript.sh start logstash
    ```

7. Sprawdź rozwiązanie Elasticsearch, aby upewnić się, że odbierasz dane

    ```bash
    curl 'localhost:9200/_cat/indices?v'
    ```

8. Uzyskaj dostęp do pulpitu nawigacyjnego usługi Kibana przy użyciu **protokołu http: \/ /Server-IP** i wprowadź nazwę użytkownika i hasło dla Kibana. Jeśli używasz obrazu ELK na platformie Azure, domyślna nazwa użytkownika to „user”, a hasło jest uzyskiwane z **diagnostyki rozruchu**.

    ![Zrzut ekranu przedstawia pulpit nawigacyjny Kibana do wizualizacji dzienników platformy i aplikacji.](./media/service-fabric-tutorial-java-elk/kibana.png)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Uruchamianie serwera ELK na platformie Azure
> * Konfigurowanie serwera do odbierania informacji diagnostycznych z klastra usługi Service Fabric

Przejdź do następnego samouczka:
> [!div class="nextstepaction"]
> [Konfigurowanie ciągłej integracji/ciągłego wdrażania przy użyciu narzędzia Jenkins](service-fabric-tutorial-java-jenkins.md)
