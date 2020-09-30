---
title: Konfigurowanie Jenkins dla aplikacji Java na Service Fabric na platformie Azure
description: W tym samouczku przedstawiono sposób konfigurowania ciągłej integracji przy użyciu narzędzia Jenkins w celu wdrożenia aplikacji Java usługi Service Fabric.
ms.topic: tutorial
ms.date: 08/27/2018
ms.custom: mvc, devx-track-java
ms.openlocfilehash: 4e29104f0586a8e18afb7a640903c57ce988b692
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91531976"
---
# <a name="tutorial-configure-a-jenkins-environment-to-enable-cicd-for-a-java-application-on-service-fabric"></a>Samouczek: konfigurowanie środowiska Jenkins w celu włączenia ciągłej integracji/ciągłego wdrażania dla aplikacji Java w usłudze Service Fabric

Niniejszy samouczek jest piątą częścią serii. Zawiera on informacje dotyczące sposobu wdrażania uaktualnień aplikacji za pomocą usługi Jenkins. W tym samouczku wtyczka usługi Jenkins w usłudze Service Fabric jest używana w połączeniu z repozytorium usługi GitHub hostującym aplikację do głosowania w celu wdrożenia aplikacji w klastrze.

Część piąta serii zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Wdrażanie kontenera narzędzia Jenkins usługi Service Fabric na maszynie
> * Konfigurowanie środowiska narzędzia Jenkins na potrzeby wdrożenia w usłudze Service Fabric
> * Uaktualnianie aplikacji

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * [Kompilowanie aplikacji Java usług Reliable Services w usłudze Service Fabric](service-fabric-tutorial-create-java-app.md)
> * [Wdrażanie i debugowanie aplikacji w klastrze lokalnym](service-fabric-tutorial-debug-log-local-cluster.md)
> * [Wdrażanie aplikacji w klastrze platformy Azure](service-fabric-tutorial-java-deploy-azure.md)
> * [Konfigurowanie monitorowania i diagnostyki dla aplikacji](service-fabric-tutorial-java-elk.md)
> * Konfigurowanie ciągłej integracji/ciągłego dostarczania

## <a name="prerequisites"></a>Wymagania wstępne

* Usługa Git zainstalowana na komputerze lokalnym ze [strony plików do pobrania usługi Git](https://git-scm.com/downloads). Aby uzyskać więcej informacji na temat usługi Git, zapoznaj się z [dokumentacją usługi Git](https://git-scm.com/docs).
* Praktyczna wiedza na temat narzędzia [Jenkins](https://jenkins.io/).
* Utworzone konto w serwisie [GitHub](https://github.com/) i wiedza dotycząca korzystania z serwisu GitHub.
* Platforma [Docker](https://www.docker.com/community-edition) zainstalowana na komputerze.

## <a name="pull-and-deploy-service-fabric-jenkins-container-image"></a>Ściąganie i wdrażanie obrazu kontenera narzędzia Jenkins usługi Service Fabric

Narzędzie Jenkins możesz skonfigurować wewnątrz klastra usługi Service Fabric lub poza nim. Poniższe instrukcje przedstawiają sposób konfigurowania go poza klastrem przy użyciu udostępnionego obrazu platformy Docker. Można jednak użyć wstępnie skonfigurowanego środowiska narzędzia Jenkins. Poniższy obraz kontenera jest instalowany z wtyczką usługi Service Fabric i jest natychmiast gotowy do użycia z usługą Service Fabric.

1. Ściągnij obraz kontenera narzędzia Jenkins usługi Service Fabric: ``docker pull rapatchi/jenkins:v10``. Ten obraz jest dostarczany ze wstępnie zainstalowaną wtyczką narzędzia Jenkins usługi Service Fabric.

1. Uruchom obraz kontenera z lokalizacji, w której przechowywane są certyfikaty platformy Azure na zainstalowanej maszynie lokalnej.

    ```bash
    docker run -itd -p 8080:8080 -v /service-fabric-java-quickstart/AzureCluster rapatchi/jenkins:v10
    ```

1. Uzyskaj identyfikator wystąpienia obrazu kontenera. Listę wszystkich kontenerów platformy Docker można wyświetlić za pomocą polecenia ``docker ps –a``

1. Pobierz hasło wystąpienia narzędzia Jenkins, uruchamiając następujące polecenie:

    ```sh
    docker exec [first-four-digits-of-container-ID] cat /var/jenkins_home/secrets/initialAdminPassword
    ```

    Jeśli identyfikator kontenera to 2d24a73b5964, użyj wartości 2d24.
    * To hasło będzie wymagane do zalogowania się do pulpitu nawigacyjnego narzędzia Jenkins z poziomu portalu, którego adres to ``http://<HOST-IP>:8080``
    * Po zalogowaniu się po raz pierwszy możesz utworzyć konto użytkownika lub użyć konta administratora.

1. Skonfiguruj usługę GitHub do pracy z narzędziem Jenkins, wykonując kroki opisane w temacie [Generating a new SSH key and adding it to the SSH agent (Generowanie nowego klucza SSH i dodawanie go do agenta SSH)](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/). Ponieważ polecenia są uruchamiane w z kontenera Docker, postępuj zgodnie z instrukcjami dla środowiska systemu Linux.
   * Użyj instrukcji z usługi GitHub do wygenerowania klucza SSH. Następnie dodaj klucz SSH do konta usługi GitHub, które hostuje repozytorium.
   * Uruchom polecenia wymienione pod powyższym linkiem w powłoce platformy Docker narzędzia Jenkins (a nie na hoście).
   * Aby zalogować się do powłoki narzędzia Jenkins z poziomu hosta, użyj następujących poleceń:

     ```sh
     docker exec -t -i [first-four-digits-of-container-ID] /bin/bash
     ```

     Upewnij się, że klaster lub maszyna hostujące obraz kontenera narzędzia Jenkins mają publiczny adres IP. Dzięki publicznemu adresowi IP wystąpienie narzędzia Jenkins może otrzymywać powiadomienia z usługi GitHub.

## <a name="create-and-configure-a-jenkins-job"></a>Tworzenie i konfigurowanie zadania narzędzia Jenkins

1. Najpierw — jeśli nie masz repozytorium, którego można użyć w celu hostowania projektu głosowania w usłudze GitHub — utwórz je. W pozostałej części tego samouczka repozytorium nosi nazwę **dev_test**.

1. Utwórz **nowy element** na pulpicie nawigacyjnym narzędzia Jenkins w ``http://<HOST-IP>:8080``.

1. Wprowadź nazwę elementu (na przykład **MyJob**). Wybierz pozycję **free-style project** (projekt w stylu dowolnym) i kliknij przycisk **OK**.

1. Przejdź na stronę zadania i kliknij pozycję **Configure** (Konfiguruj).

   a. W sekcji Ogólne zaznacz pole wyboru **projektu GitHub** i określ adres URL projektu usługi GitHub. Pod tym adresem URL jest hostowana aplikacja Java usługi Service Fabric, którą chcesz zintegrować z przepływem ciągłej integracji/ciągłego wdrażania narzędzia Jenkins (na przykład ``https://github.com/testaccount/dev_test``).

   b. W obszarze **Source Code Management** (Zarządzanie kodem źródłowym) wybierz pozycję **Git**. Określ adres URL repozytorium, który obsługuje aplikację Java Service Fabric, którą chcesz zintegrować z przepływem ciągłej integracji/ciągłego wdrażania Jenkins (na przykład *`https://github.com/testaccount/dev_test.git`* ). W tym miejscu możesz również określić gałąź do skompilowania (na przykład **/master**).

1. Skonfiguruj usługę *GitHub* (która hostuje repozytorium), aby mogła komunikować się z narzędziem Jenkins. Wykonaj następujące kroki:

   a. Przejdź do strony repozytorium serwisu GitHub. Przejdź do pozycji **Ustawienia**  >  **integracje i usługi**.

   b. Wybierz pozycję **Add Service** (Dodaj usługę), wpisz ciąg **Jenkins** i wybierz pozycję **Jenkins-Github plugin** (Wtyczka Jenkins-Github).

   c. Wprowadź adres URL elementu webhook narzędzia Jenkins (domyślnie adres URL powinien być następujący: ``http://<PublicIPorFQDN>:8081/github-webhook/``). Kliknij pozycję **add/update service** (dodaj/aktualizuj usługę).

   d. Do wystąpienia narzędzia Jenkins zostanie wysłane zdarzenie testowe. W usłudze GitHub powinien zostać wyświetlony zielony znacznik wyboru obok elementu webhook i projekt jest kompilowany.

   ![Konfiguracja narzędzia Jenkins usługi Service Fabric](./media/service-fabric-tutorial-java-jenkins/jenkinsconfiguration.png)

1. W sekcji **Build Triggers** (Wyzwalacze kompilacji) wybierz odpowiednią opcję kompilacji. W tym przykładzie kompilacja ma być wyzwalana po każdej operacji wypychania do repozytorium. Dlatego wybierz pozycję **GitHub hook trigger for GITScm polling** (Wyzwalacz punktu zaczepienia GitHub na potrzeby sondowania GITScm).

1. W sekcji **Build** (Kompilacja) z listy rozwijanej **Add build step** (Dodaj krok kompilacji) wybierz opcję **Invoke Gradle Script** (Wywołaj skrypt Gradle). W wyświetlonym widżecie otwórz zaawansowane menu i określ ścieżkę do lokalizacji **Root build script** (Główny skrypt kompilacji) dla aplikacji. Spowoduje to wybranie elementu build.gradle z określonej ścieżki i wykonanie odpowiedniego działania.

    ![Akcja kompilacji narzędzia Jenkins w usłudze Service Fabric](./media/service-fabric-tutorial-java-jenkins/jenkinsbuildscreenshot.png)

1. Z listy rozwijanej **Post-Build Actions** (Akcje po kompilacji) wybierz pozycję **Deploy Service Fabric Project** (Wdróż projekt usługi Service Fabric). W tym miejscu należy podać szczegóły dotyczące klastra, w ramach którego zostanie wdrożona skompilowana aplikacja usługi Service Fabric narzędzia Jenkins. Ścieżka do certyfikatu to lokalizacja, w której zainstalowano wolumin (/tmp/myCerts).

    Możesz także podać dodatkowe szczegóły dotyczące używane podczas wdrażania aplikacji. Na poniższym zrzucie ekranu przedstawiono przykład szczegółów aplikacji:

    ![Okno dialogowe akcje po kompilacji z dodatkowymi opcjami wdrażania projektu Service Fabric](./media/service-fabric-tutorial-java-jenkins/sfjenkins.png)

    > [!NOTE]
    > Określany w tym miejscu klaster może być tym samym klastrem, który hostuje aplikację kontenera narzędzia Jenkins, jeśli usługa Service Fabric jest używana do wdrożenia obrazu kontenera narzędzia Jenkins.
    >

1. Kliknij pozycję **Zapisz**.

## <a name="update-your-existing-application"></a>Aktualizowanie istniejącej aplikacji

1. Zaktualizuj tytuł pliku HTML w pliku *VotingApplication/VotingWebPkg/Code/wwwroot/index.html* na **Service Fabric Voting Sample V2**.

    ```html
    <div ng-app="VotingApp" ng-controller="VotingAppController" ng-init="refresh()">
        <div class="container-fluid">
            <div class="row">
                <div class="col-xs-8 col-xs-offset-2 text-center">
                    <h2>Service Fabric Voting Sample V2</h2>
                </div>
            </div>
        </div>
    </div>
    ```

1. Zaktualizuj wersje **ApplicationTypeVersion** i **ServiceManifestVersion** na **2.0.0** w pliku *Voting/VotingApplication/ApplicationManifest.xml*.

    ```xml
    <?xml version="1.0" encoding="utf-8" standalone="no"?>
    <ApplicationManifest xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="VotingApplicationType" ApplicationTypeVersion="2.0.0">
      <Description>Voting Application</Description>
      <ServiceManifestImport>
        <ServiceManifestRef ServiceManifestName="VotingWebPkg" ServiceManifestVersion="2.0.0"/>
      </ServiceManifestImport>
      <ServiceManifestImport>
            <ServiceManifestRef ServiceManifestName="VotingDataServicePkg" ServiceManifestVersion="1.0.0"/>
        </ServiceManifestImport>
        <DefaultServices>
          <Service Name="VotingWeb">
             <StatelessService InstanceCount="1" ServiceTypeName="VotingWebType">
                <SingletonPartition/>
             </StatelessService>
          </Service>
       <Service Name="VotingDataService">
                <StatefulService MinReplicaSetSize="3" ServiceTypeName="VotingDataServiceType" TargetReplicaSetSize="3">
                    <UniformInt64Partition HighKey="9223372036854775807" LowKey="-9223372036854775808" PartitionCount="1"/>
                </StatefulService>
            </Service>
        </DefaultServices>
    </ApplicationManifest>
    ```

1. Zaktualizuj pole **Wersja** w tagu **ServiceManifest** i pole **Wersja** w tagu **CodePackage** w pliku *Voting/VotingApplication/VotingWebPkg/ServiceManifest.xml* na **2.0.0**.

    ```xml
    <CodePackage Name="Code" Version="2.0.0">
    <EntryPoint>
        <ExeHost>
        <Program>entryPoint.sh</Program>
        </ExeHost>
    </EntryPoint>
    </CodePackage>
    ```

1. Aby zainicjować zadanie narzędzia Jenkins, które wykonuje uaktualnienie aplikacji, wypchnij nowe zmiany do repozytorium GitHub.

1. W narzędziu Service Fabric Explorer kliknij listę rozwijaną **Aplikacje**. Aby sprawdzić stan uaktualnienia, kliknij kartę **Uaktualnienia w toku**.

    ![Uaktualnianie w toku](./media/service-fabric-tutorial-create-java-app/upgradejava.png)

1. Jeśli masz dostęp do **http:// \<Host-IP> : 8080**  , aplikacja do głosowania z pełną funkcjonalnością jest teraz w pełni uruchomiona.

    ![Lokalna aplikacja do głosowania](./media/service-fabric-tutorial-java-jenkins/votingv2.png)

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wdrażanie kontenera narzędzia Jenkins usługi Service Fabric na maszynie
> * Konfigurowanie środowiska narzędzia Jenkins na potrzeby wdrożenia w usłudze Service Fabric
> * Uaktualnianie aplikacji

* Zapoznaj się z innymi [przykładami w języku Java](https://github.com/Azure-Samples/service-fabric-java-getting-started)
