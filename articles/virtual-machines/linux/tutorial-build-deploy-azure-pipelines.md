---
title: Samouczek — CI/CD do maszyn wirtualnych platformy Azure przy użyciu Azure Pipelines
description: W tym samouczku dowiesz się, jak skonfigurować ciągłą integrację (CI) i ciągłe wdrażanie aplikacji Node.js na maszynach wirtualnych platformy Azure przy użyciu potoku platformy Azure opartej na YAML.
author: ushan
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/3/2020
ms.author: ushan
ms.custom: devops, devx-track-js
ms.openlocfilehash: 4ca431290fe168f8fc073908bb3735ecb5992562
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98731254"
---
# <a name="tutorial-deploy-your-app-to-linux-virtual-machines-in-azure-using-azure-devops-services-and-azure-pipelines"></a>Samouczek: wdrażanie aplikacji na maszynach wirtualnych z systemem Linux na platformie Azure przy użyciu Azure DevOps Services i Azure Pipelines

Ciągłej integracji (CI) i ciągłego wdrażania (CD) tworzą potok, za pomocą którego można kompilować, wydawania i wdrażać kod po każdym zatwierdzeniu kodu. Ten dokument zawiera kroki związane z konfigurowaniem potoku ciągłej integracji/ciągłego wdrażania na potrzeby wdrożeń obejmujących wiele maszyn przy użyciu Azure Pipelines.

Azure Pipelines zawiera kompletny, w pełni funkcjonalny zestaw narzędzi do automatyzacji ciągłej integracji i ciągłego wdrażania na maszynach wirtualnych, zarówno w Premium, jak i w dowolnej chmurze.

W tym samouczku zostanie skonfigurowany potok ciągłej integracji/ciągłego wdrażania (YAML) umożliwiający wdrażanie aplikacji w [środowisku](/azure/devops/pipelines/process/environments) Azure Pipelinesym z maszynami wirtualnymi z systemem Linux jako zasobami, z których każdy służy jako serwery sieci Web do uruchamiania aplikacji.

Omawiane kwestie:

> [!div class="checklist"]
> * Pobierz przykładową aplikację.
> * Utwórz potok YAML na podstawie Azure Pipelines na potrzeby tworzenia przykładowej aplikacji.
> * Tworzenie środowiska Azure Pipelines dla maszyn wirtualnych platformy Azure
> * Utwórz potok Azure Pipelines CD.
> * Przeprowadzanie wdrożeń wyzwalanych ręcznie i za pomocą CI.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

* Zaloguj się do swojej organizacji Azure DevOps Services ( **https://dev.azure.com/** ). 
  Możesz uzyskać [bezpłatną organizację Azure DevOps Services](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > Aby uzyskać więcej informacji, zobacz [Nawiązywanie połączenia z usługami Azure DevOps Services](/azure/devops/organizations/projects/connect-to-projects).

*  Maszyna wirtualna systemu Linux jest potrzebna jako cel wdrożenia.  Aby uzyskać więcej informacji, zobacz [Create and manage Linux VMs with the Azure CLI (Tworzenie maszyn wirtualnych systemu Linux i zarządzanie nimi za pomocą interfejsu wiersza polecenia platformy Azure)](./tutorial-manage-vm.md).

*  Otwórz port wejściowy 80 dla maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Create network security groups using the Azure portal (Tworzenie sieciowych grup zabezpieczeń przy użyciu witryny Azure Portal)](../../virtual-network/tutorial-filter-network-traffic.md).

## <a name="get-your-sample-app-code"></a>Pobieranie kodu przykładowej aplikacji

Jeśli masz już aplikację w usłudze GitHub, którą chcesz wdrożyć, możesz spróbować utworzyć potok dla tego kodu.

Jeśli jednak jesteś nowym użytkownikiem, możesz lepiej zacząć korzystać z naszego przykładowego kodu. W takim przypadku rozwidlenie tego repozytorium w serwisie GitHub:

#### <a name="java"></a>[Java](#tab/java)

```
https://github.com/spring-projects/spring-petclinic
```

> [!NOTE]
> Petclinic to aplikacja [rozruchu ze sprężyną języka Java](https://spring.io/guides/gs/spring-boot) skompilowana przy użyciu [Maven](https://spring.io/guides/gs/maven/).

#### <a name="javascript"></a>[JavaScript](#tab/java-script)

```
https://github.com/azure-devops/fabrikam-node
```

> [!NOTE]
> Ta aplikacja Node.js była skompilowana przez [Narzędzia Yeoman](https://yeoman.io/learning/index.html). Używa ona narzędzi Express, Bower i Grunt. I ma niektóre pakiety npm jako zależności.
> Przykład zawiera także skrypt, który konfiguruje serwer Nginx, a następnie wdraża aplikację. Jest on wykonywany na maszynach wirtualnych. W szczególności skrypt:
> 1. Instaluje rozwiązania Node, Nginx i PM2.
> 2. Konfiguruje rozwiązania Nginx i PM2.
> 3. Uruchamia aplikację Node.

* * * 

## <a name="prerequisites-for-the-linux-vm"></a>Wymagania wstępne dla maszyny wirtualnej z systemem Linux

Przykładowe aplikacje wymienione powyżej zostały przetestowane w witrynie Ubuntu 16,04 i zalecamy użycie tej samej wersji maszyny wirtualnej systemu Linux na potrzeby tego przewodnika Szybki Start.
Postępuj zgodnie z dodatkowymi krokami opisanymi poniżej w zależności od stosu środowiska uruchomieniowego używanego dla aplikacji.

#### <a name="java"></a>[Java](#tab/java)

- Aby wdrażać aplikacje oparte na chmurze pod kątem rozruchowego i sprężyny chmurowej, należy utworzyć maszynę wirtualną z systemem Linux na platformie Azure przy użyciu [tego](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu13-ubuntu-2004) szablonu, który zapewnia w pełni obsługiwaną obsługę środowiska uruchomieniowego
- Aby wdrożyć środowisko Java serwletów na serwerze Tomcat, należy utworzyć maszynę wirtualną z systemem Linux za pomocą języka Java 8 przy użyciu [tego](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu13-ubuntu-2004) szablonu platformy Azure i [skonfigurować Tomcat 9. x jako usługę](https://tomcat.apache.org/tomcat-9.0-doc/setup.html).
- Aby wdrożyć aplikację opartą na języku Java EE, użyj szablonu platformy Azure, aby utworzyć [maszynę wirtualną z systemem Linux + Java + WebSphere 9. x](https://azuremarketplace.microsoft.com/marketplace/apps/midvision.websphere-application-server-nde-90) lub [maszynę wirtualną z systemem Linux + Java + WebLogic 12. x](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.20191009-arm-oraclelinux-wls-admin) lub [maszynę wirtualną z systemem Linux + Java](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu13-ubuntu-2004) + WildFly/JBoss 14 


#### <a name="javascript"></a>[JavaScript](#tab/java-script)

Aby można było zainstalować aplikację JavaScript lub aplikację Node.js, do wdrożenia aplikacji potrzebna jest maszyna wirtualna z systemem Linux z serwerem sieci Web Nginx.
Jeśli nie masz jeszcze maszyny wirtualnej z systemem Linux z usługą Nginx, utwórz ją teraz na platformie Azure, korzystając z kroków przedstawionych w [tym przykładzie](./quick-create-cli.md).

* * * 

## <a name="create-an-azure-pipelines-environment-with-azure-virtual-machines"></a>Tworzenie środowiska Azure Pipelines za pomocą usługi Azure Virtual Machines

Maszyny wirtualne można dodawać jako zasoby w [środowiskach](/azure/devops/pipelines/process/environments) i mogą być przeznaczone do wdrożeń wielu maszyn. Widoki historii wdrożenia w środowisku zapewniają możliwość śledzenia z maszyny wirtualnej do potoku, a następnie do zatwierdzenia.

Środowisko można utworzyć w centrum "**środowiska**" w sekcji "**potoki**".
1.  Zaloguj się do organizacji usługi Azure DevOps i przejdź do projektu.
2.  W projekcie przejdź do strony **potoki** . Następnie wybierz opcję **środowiska** , a następnie kliknij pozycję **Utwórz środowisko**. Określ **nazwę** (wymaganą) dla środowiska i **Opis**.
3.  Wybierz **Virtual Machines** jako **zasób** do dodania do środowiska, a następnie kliknij przycisk **dalej**.
4.  Wybierz system operacyjny (Windows/Linux) i **Skopiuj skrypt rejestracji PS**. 
5.  Teraz można uruchomić skopiowany skrypt z poziomu wiersza polecenia programu PowerShell administratora na wszystkich docelowych maszynach wirtualnych, które mają być zarejestrowane w tym środowisku.
    > [!NOTE]
    > - Osobisty token dostępu zalogowanego użytkownika został wstępnie wstawiony do skryptu, który wygasa w tym samym dniu, w którym skopiowany skrypt będzie bezużyteczny.
    > - Jeśli na maszynie wirtualnej jest już uruchomiony agent, podaj unikatową nazwę "Agent", aby zarejestrować się w środowisku.
6.  Po zarejestrowaniu maszyny wirtualnej zostanie ona uruchomiona jako zasób środowiska na karcie "zasoby" w środowisku.

    ![VMcreation](media/tutorial-deploy-vms-azure-pipelines/vm-creation.png)

7.  Aby dodać więcej maszyn wirtualnych, można wyświetlić i skopiować skrypt ponownie, klikając pozycję "Dodaj zasób" i wybierając pozycję "Virtual Machines" jako zasób. Ten skrypt pozostanie taki sam dla wszystkich maszyn wirtualnych, które mają zostać dodane do tego środowiska. 
8.  Każdy komputer współdziała z Azure Pipelines, aby koordynować wdrażanie aplikacji.

    ![VMresource_view](media/tutorial-deploy-vms-azure-pipelines/vm-resourceview.png)

9. Możesz dodać tagi do maszyny wirtualnej w ramach interakcyjnego skryptu rejestracji programu PowerShell (lub) można również dodać/usunąć ten sam z widoku zasobów, klikając trzykrotne kropki na końcu każdego zasobu maszyny wirtualnej w widoku zasoby.

   Przypisane znaczniki umożliwiają ograniczenie wdrożenia do określonych maszyn wirtualnych, gdy środowisko jest używane w zadaniu wdrażania. Tagi są ograniczone do 256 znaków, ale nie ma żadnego limitu liczby tagów, których można użyć.

   ![VMtags](media/tutorial-deploy-vms-azure-pipelines/vm-tags.png)

* * * 

## <a name="define-your-ci-build-pipeline"></a>Zdefiniuj potok kompilacji CI

Będziesz potrzebować potoku kompilacji ciągłej integracji (CI), który publikuje aplikację sieci Web, a także skrypt wdrażania, który można uruchomić lokalnie na serwerze Ubuntu. Skonfiguruj potok kompilacji elementu konfiguracji w oparciu o środowisko uruchomieniowe, którego chcesz użyć. 

1. Zaloguj się do organizacji usługi Azure DevOps i przejdź do projektu.

1. W projekcie przejdź do strony **potoki** . Następnie wybierz akcję, aby utworzyć nowy potok.

1. Wykonaj kroki kreatora, wybierając najpierw witrynę **GitHub** jako lokalizację kodu źródłowego.

1. Być może nastąpi przekierowanie do usługi GitHub w celu zalogowania się. W takim przypadku wprowadź swoje poświadczenia usługi GitHub.

1. Po wyświetleniu listy repozytoriów wybierz odpowiednie przykładowe repozytorium aplikacji.

1. Azure Pipelines przeanalizować repozytorium i zalecamy odpowiedni szablon potoku.

#### <a name="java"></a>[Java](#tab/java)

Wybierz szablon **startowy** i Skopiuj poniższy fragment kodu YAML, który kompiluje projekt Java i uruchamia testy za pomocą platformy Apache Maven:

```YAML
jobs:
- job: Build
  displayName: Build Maven Project
  steps:
  - task: Maven@3
    displayName: 'Maven Package'
    inputs:
      mavenPomFile: 'pom.xml'
  - task: CopyFiles@2
    displayName: 'Copy Files to artifact staging directory'
    inputs:
      SourceFolder: '$(System.DefaultWorkingDirectory)'
      Contents: '**/target/*.?(war|jar)'
      TargetFolder: $(Build.ArtifactStagingDirectory)
  - upload: $(Build.ArtifactStagingDirectory)
    artifact: drop
```

Aby uzyskać więcej wskazówek, wykonaj kroki opisane w sekcji [Kompilowanie aplikacji Java za pomocą Maven](/azure/devops/pipelines/ecosystems/java).

#### <a name="javascript"></a>[JavaScript](#tab/java-script)

Wybierz **początkowy** szablon i Skopiuj poniższy fragment kodu YAML, który kompiluje ogólny projekt Node.js z npm.

```YAML
- stage: Build
  displayName: Build stage
  jobs:  
  - job: Build
    displayName: Build
    - task: NodeTool@0
      inputs:
        versionSpec: '10.x'
      displayName: 'Install Node.js'
    - script: |
        npm install
        npm run build --if-present
        npm run test --if-present
      displayName: 'npm install, build and test'
    - task: ArchiveFiles@2
      displayName: 'Archive files'
      inputs:
        rootFolderOrFile: '$(System.DefaultWorkingDirectory)'
        includeRootFolder: false
        archiveType: zip
        archiveFile: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
        replaceExistingArchive: true
    - upload: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
      artifact: drop
```

Aby uzyskać więcej wskazówek, postępuj zgodnie z instrukcjami w sekcji [Kompilowanie aplikacji Node.js za pomocą Gulp](/azure/devops/pipelines/ecosystems/javascript).

- Obejrzyj potok, aby zobaczyć, co robi. Upewnij się, że wszystkie domyślne dane wejściowe są odpowiednie dla kodu.

- Wybierz pozycję **Zapisz i uruchom**, a następnie wybierz pozycję **Zatwierdź bezpośrednio w gałęzi głównej**, a następnie wybierz pozycję **Zapisz i uruchom** ponownie.

- Uruchomiono nowe uruchomienie. Poczekaj na zakończenie przebiegu.

* * * 

## <a name="define-cd-steps-to-deploy-to-the-linux-vm"></a>Definiowanie kroków z dysku CD do wdrożenia na maszynie wirtualnej z systemem Linux

1. Zmień plik YAML dla powyższego potoku, aby uwzględnić [zadanie wdrażania](/azure/devops/pipelines/process/deployment-jobs) , odwołując się do środowiska i zasobów maszyny wirtualnej, których wcześniej używasz składni YAML poniżej:

   ```YAML
   jobs:  
   - deployment: VMDeploy
     displayName: web
     environment:
       name:  <environment name>
       resourceType: VirtualMachine
       tags: web
   ```
2. Można wybrać określone zestawy maszyn wirtualnych ze środowiska w celu uzyskania wdrożenia, określając **Tagi** zdefiniowane dla każdej maszyny wirtualnej w środowisku.
[Oto](/azure/devops/pipelines/yaml-schema?tabs=schema#deployment-job) kompletny schemat YAML dla zadania wdrażania.

3. Można określić albo `runOnce` `rolling` jako strategię wdrażania. 

   `runOnce` jest najprostszą strategią wdrażania, która powoduje, że wszystkie punkty zaczepienia cyklu życia, mianowicie `preDeploy` `deploy` , `routeTraffic` i `postRouteTraffic` , są wykonywane raz. Następnie albo `on:` `success` lub `on:` `failure` jest wykonywane.

   Poniżej znajduje się przykładowy fragment kodu YAML dla `runOnce` :
   ```YAML
   jobs:
   - deployment: VMDeploy
     displayName: web
     pool:
       vmImage: 'Ubuntu-16.04'
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         runOnce:
           deploy:
             steps:
             - script: echo my first deployment
   ```

4. Poniżej znajduje się przykład fragmentu kodu YAML, którego można użyć do zdefiniowania strategii stopniowej aktualizacji maszyn wirtualnych do 5 elementów docelowych w każdej iteracji. `maxParallel` określi liczbę elementów docelowych, do których można wdrożyć równolegle. Wybór konta dla liczby bezwzględnej lub procentowej wartości docelowych, które muszą pozostać dostępne w dowolnym momencie, z wyjątkiem obiektów docelowych, które są wdrażane w. Jest on również używany do określania warunków sukcesu i niepowodzenia podczas wdrażania.

   ```YAML
   jobs: 
   - deployment: VMDeploy
     displayName: web
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         rolling:
           maxParallel: 5  #for percentages, mention as x%
           preDeploy:
             steps:
             - download: current
               artifact: drop
             - script: echo initialize, cleanup, backup, install certs
           deploy:
             steps:
             - task: Bash@3
               inputs:
                 targetType: 'inline'
                 script: |
                   # Modify deployment script based on the app type
                   echo "Starting deployment script run"
                   sudo java -jar '$(Pipeline.Workspace)/drop/**/target/*.jar'
           routeTraffic:
             steps:
             - script: echo routing traffic
           postRouteTraffic:
             steps:
             - script: echo health check post-route traffic
           on:
             failure:
               steps:
               - script: echo Restore from backup! This is on failure
             success:
               steps:
               - script: echo Notify! This is on success
   ```

   W każdym uruchomieniu tego zadania historia wdrożenia jest rejestrowana w `<environment name>` środowisku, w którym utworzono i zarejestrowano maszyny wirtualne.

## <a name="run-your-pipeline-and-get-traceability-views-in-environment"></a>Uruchamianie potoku i pobieranie widoków śledzenia w środowisku
Widok wdrożenia środowiska zapewnia pełną możliwość śledzenia zatwierdzeń i elementów roboczych oraz historię wdrożenia między potokami dla środowiska/zasobu.

![VMDeployments_view](media/tutorial-deploy-vms-azure-pipelines/vm-deployments.png)
  
![VMjobs_view](media/tutorial-deploy-vms-azure-pipelines/vm-jobsview.png)

## <a name="next-steps"></a>Następne kroki
- Możesz przystępować do [dostosowywania utworzonego przez siebie potoku](/azure/devops/pipelines/customize-pipeline) .
- Aby dowiedzieć się, co jeszcze można zrobić w potokach YAML, zobacz [YAML Schema Reference](/azure/devops/pipelines/yaml-schema).
- Aby dowiedzieć się więcej o sposobie wdrażania stosu LAMP (Linux, Apache MySQL i PHP), przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Wdrażanie stosu LAMP](tutorial-lamp-stack.md)
