---
title: Debugowanie aplikacji Java w lokalnym klastrze Service Fabric
description: W tym samouczku przedstawiono sposób debugowania i pobierania dzienników z aplikacji Java usługi Service Fabric działającej w klastrze lokalnym.
ms.topic: tutorial
ms.date: 02/26/2018
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 801702d43bae6d925fa4f7fbc37cf44bf585fa6d
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91531994"
---
# <a name="tutorial-debug-a-java-application-deployed-on-a-local-service-fabric-cluster"></a>Samouczek: debugowanie aplikacji Java wdrożonej w lokalnym klasterze usługi Service Fabric

Ten samouczek jest drugą częścią serii. Dowiesz się, jak dołączyć debuger zdalny przy użyciu programu Eclipse dla aplikacji usługi Service Fabric. Ponadto zostanie przedstawiony sposób przekierowywania dzienników z uruchomionych aplikacji do lokalizacji wygodnej dla dewelopera.

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * [Kompilowanie aplikacji Java usług Reliable Services w usłudze Service Fabric](service-fabric-tutorial-create-java-app.md)
> * Wdrażanie i debugowanie aplikacji w klastrze lokalnym
> * [Wdrażanie aplikacji w klastrze platformy Azure](service-fabric-tutorial-java-deploy-azure.md)
> * [Konfigurowanie monitorowania i diagnostyki dla aplikacji](service-fabric-tutorial-java-elk.md)
> * [Konfigurowanie ciągłej integracji/ciągłego dostarczania](service-fabric-tutorial-java-jenkins.md)


Część druga serii zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Debugowanie aplikacji Java przy użyciu programu Eclipse
> * Przekierowywanie dzienników do lokalizacji możliwej do skonfigurowania


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Skonfiguruj środowisko projektowe dla komputera [Mac](service-fabric-get-started-mac.md) lub systemu [Linux](service-fabric-get-started-linux.md). Postępuj zgodnie z instrukcjami, aby zainstalować wtyczkę programu Eclipse, program Gradle, zestaw Service Fabric SDK i interfejs wiersza polecenia usługi Service Fabric (sfctl).

## <a name="download-the-voting-sample-application"></a>Pobieranie przykładowej aplikacji do głosowania

Jeśli nie skompilowano przykładowej aplikacji do głosowania w [pierwszej części tej serii samouczków](service-fabric-tutorial-create-java-app.md), można ją pobrać. W oknie polecenia uruchom następujące polecenie, aby sklonować przykładowe repozytorium aplikacji na komputer lokalny.

```bash
git clone https://github.com/Azure-Samples/service-fabric-java-quickstart
```

[Kompilowanie i wdrażanie](service-fabric-tutorial-create-java-app.md#deploy-application-to-local-cluster) aplikacji w lokalnym klastrze projektowym.

## <a name="debug-java-application-using-eclipse"></a>Debugowanie aplikacji Java przy użyciu programu Eclipse

1. Otwórz środowisko Eclipse IDE na maszynie i kliknij pozycję **Plik -> Import...**

2. W oknie podręcznym wybierz opcję **Ogólne -> Istniejące projekty do obszaru roboczego** i kliknij przycisk Dalej.

3. W oknie Importowanie projektów wybierz pozycję **Wybierz katalog główny**, a następnie wybierz katalog **Voting**. Jeśli kroki z pierwszego samouczka zostały wykonane, katalog **Voting** znajduje się w katalogu **Eclipse-workspace**.

4. Zaktualizuj plik entryPoint.sh usługi do debugowania, tak aby uruchamiał proces języka Java z parametrami debugowania zdalnego. W tym samouczku jest używany fronton bezstanowy: *głosujący/VotingApplication/VotingWebPkg/Code/EntryPoint. sh*. Port 8001 jest ustawiany na potrzeby debugowania w tym przykładzie.

    ```bash
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -jar VotingWeb.jar
    ```

5. Zaktualizuj manifest aplikacji, ustawiając liczbę wystąpień lub liczbę replik dla debugowanej usługi na jeden. To ustawienie pozwala uniknąć konfliktów z portem używanym podczas debugowania. Na przykład w przypadku usług bezstanowych ustaw ``InstanceCount="1"``, a dla usług stanowych ustaw docelowy i minimalny rozmiar zestawu replik na 1 w następujący sposób: ``TargetReplicaSetSize="1" MinReplicaSetSize="1"``.

6. W środowisku IDE programu Eclipse wybierz pozycje **Uruchom -> Debuguj konfiguracje -> Zdalna aplikacja Java**, naciśnij przycisk **Nowy**, ustaw poniższe właściwości i kliknij pozycję **Zastosuj**.

    ```
    Name: Voting
    Project: Voting
    Connection Type: Standard
    Host: localhost
    Port: 8001
    ```

7. Umieść punkt przerwania w wierszu 109 pliku *Voting/VotingWeb/src/statelessservice/HttpCommunicationListener.java*.

8. W eksploratorze pakietu kliknij prawym przyciskiem myszy projekt **Voting** i kliknij pozycję **Service Fabric -> Publikuj aplikację...**

9. W oknie **Publikowanie aplikacji** wybierz pozycję **Local.json** z listy rozwijanej, a następnie kliknij pozycję **Publikuj**.

10. W środowisku IDE programu Eclipse wybierz pozycje **Uruchom -> Debuguj konfiguracje -> Zdalna aplikacja Java**, kliknij utworzoną konfigurację **Voting** i kliknij pozycję **Debuguj**.

11. Przejdź do przeglądarki sieci Web i uzyskaj dostęp do **hosta localhost: 8080**. Spowoduje to automatyczne trafienie punktu przerwania, a przekroczenie spowoduje wprowadzenie **perspektywy debugowania**.

Teraz możesz zastosować te same kroki, aby debugować dowolne aplikacje Service Fabric w programie zaćmienie.

## <a name="redirect-application-logs-to-custom-location"></a>Przekierowywanie dzienników aplikacji do lokalizacji niestandardowej

Poniższe kroki przedstawiają sposób przekierowywania dzienników aplikacji z domyślnej lokalizacji */var/log/syslog* do lokalizacji niestandardowej.

1. Obecnie aplikacje działające w klastrach Service Fabric Linux obsługują tylko pobieranie jednego pliku dziennika. Aby skonfigurować aplikację w taki sposób, aby dzienniki były zawsze przechodzą do */tmp/mysfapp0.0.log*, Utwórz plik o nazwie Logging. Properties w następującej lokalizacji: *głosu/VotingApplication/VotingWebPkg/Code/Logging. Properties* i Dodaj poniższą zawartość.

    ```
    handlers = java.util.logging.FileHandler

    java.util.logging.FileHandler.level = ALL
    java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter

    # This value specifies your custom location.
    # You will have to ensure this path has read and write access by the process running the SF Application
    java.util.logging.FileHandler.pattern = /tmp/mysfapp0.0.log
    ```

2. Dodaj następujący parametr w pliku *Voting/VotingApplication/VotingWebPkg/Code/entryPoint.sh* na potrzeby polecenia wykonywania kodu Java:

    ```bash
    -Djava.util.logging.config.file=logging.properties
    ```

    W poniższym przykładzie pokazano przykładowe wykonywanie z dołączonym debugerem, podobnie jak w poprzedniej sekcji.

    ```bash
    java -Xdebug -Xrunjdwp:transport=dt_socket,address=8001,server=y,suspend=n -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=logging.properties -jar VotingWeb.jar
    ```

Na tym etapie zapoznano się ze sposobem debugowania i uzyskiwać dostęp do dzienników aplikacji podczas tworzenia aplikacji Java w usłudze Service Fabric.

## <a name="next-steps"></a>Następne kroki

W tej części samouczka zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Debugowanie aplikacji Java przy użyciu programu Eclipse
> * Przekierowywanie dzienników do lokalizacji możliwej do skonfigurowania

Przejdź do następnego samouczka:
> [!div class="nextstepaction"]
> [Wdrażanie aplikacji na platformie Azure](service-fabric-tutorial-java-deploy-azure.md)