---
title: Utwórz potok ciągłej integracji/ciągłego wdrażania dla programu PWA z GatsbyJS i Azure DevOps Starter
description: Dowiedz się, jak utworzyć NodeJS progresywną aplikację sieci Web (PWA) przy użyciu GatsbyJS i uproszczonego środowiska tworzenia platformy Azure DevOps Starter.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: arob98
manager: angrobe
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: angrobe
ms.custom: mvc, devx-track-js
ms.openlocfilehash: fa6d0f78f984c5e71ccdbcada45ea1bc50be9e95
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91318623"
---
# <a name="create-a-cicd-pipeline-in-azure-pipelines-for-nodejs-with-azure-devops-starter"></a>Tworzenie potoku ciągłej integracji/ciągłego wdrażania w Azure Pipelines dla Node.js za pomocą usługi Azure DevOps Starter

W tym przewodniku szybki start utworzysz NodeJS progresywną aplikację sieci Web (PWA) przy użyciu [GatsbyJS](https://www.gatsbyjs.org/) i uproszczonego środowiska tworzenia usługi Azure DevOps Starter. Po zakończeniu masz potok ciągłej integracji (CI) i ciągłe dostarczanie (CD) dla programu PWA w Azure Pipelines. Usługa Azure DevOps Starter konfiguruje, co jest potrzebne do tworzenia, wdrażania i monitorowania.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- Organizacja [usługi Azure DevOps](https://azure.microsoft.com/services/devops/) .

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

DevOps Starter tworzy potok ciągłej integracji/ciągłego wdrażania w Azure Pipelines. Możesz utworzyć nową organizację usługi Azure DevOps lub użyć istniejącej organizacji. DevOps Starter tworzy również zasoby platformy Azure w wybranej subskrypcji platformy Azure.

1. Zaloguj się do [Azure Portal](https://portal.azure.com), a następnie w okienku po lewej stronie wybierz pozycję **Utwórz zasób**. 

   ![Tworzenie zasobu platformy Azure w Azure Portal](_img/azure-devops-project-nodejs/create-azure-resource.png)

1. W polu wyszukiwania wpisz **DevOps Starter**, a następnie wybierz opcję. Kliknij przycisk **Dodaj** , aby utworzyć nowy.

    ![Pulpit nawigacyjny DevOps Starter](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-application-and-azure-service"></a>Wybieranie przykładowej aplikacji i usługi platformy Azure

1. Wybierz przykładową aplikację środowiska Node.js.   

    ![Wybierz przykład Node.js](_img/azure-devops-project-nodejs/select-nodejs.png) 

1. Domyślną strukturą przykładu jest **Express.js**. Zmień zaznaczenie na **prostą aplikację Node.js** , a następnie wybierz przycisk **dalej**. 

    ![Wybierz prostą aplikację Node.js](_img/azure-devops-project-nodejs/select-nodejs-framework.png) 

1. Cele wdrożenia dostępne w tym kroku są podyktowane przez strukturę aplikacji wybraną w kroku 2. W tym przykładzie **aplikacja sieci Web systemu Windows** jest domyślnym miejscem docelowym wdrożenia. Pozostaw zestaw **Web App for Containers** i wybierz pozycję **dalej**.

    ![Wybierz cel wdrożenia](_img/azure-devops-project-nodejs/select-web-server.png)

## <a name="configure-a-project-name-and-an-azure-subscription"></a>Skonfiguruj nazwę projektu i subskrypcję platformy Azure

1. W ostatnim kroku przepływu pracy DevOps Starter — Przypisz nazwę projektu, wybierz subskrypcję platformy Azure, a następnie wybierz pozycję **gotowe**.  

    ![Przypisz nazwę projektu i wybierz subskrypcję](_img/azure-devops-project-nodejs/assign-project-name.png)

1. Podczas kompilowania projektu zostanie wyświetlona strona podsumowania, a aplikacja jest wdrażana na platformie Azure. Po krótkim czasie projekt zostanie utworzony w [organizacji usługi Azure DevOps](https://dev.azure.com/) , która obejmuje repozytorium git, tablicę Kanban, potok wdrożenia, plany testów i artefakty wymagane przez aplikację.  

## <a name="managing-your-project"></a>Zarządzanie projektem

1. Przejdź do **wszystkich zasobów** i Znajdź swój DevOps Starter. Wybierz **DevOps Starter**.

    ![Pulpit nawigacyjny usługi Azure DevOps na liście zasobów](_img/azure-devops-project-nodejs/devops-starter-list.png)

1. Nastąpi przekierowanie do pulpitu nawigacyjnego, który zapewnia wgląd w stronę główną projektu, repozytorium kodu, potok ciągłej integracji/ciągłego wdrażania oraz link do uruchomionej aplikacji. Wybierz **stronę główną projektu** , aby wyświetlić aplikację w **usłudze Azure DevOps** , a następnie w innej karcie przeglądarki wybierz **punkt końcowy aplikacji** , aby wyświetlić przykładową aplikację na żywo. Zmienimy ten przykład później, aby użyć GatsbyJS wygenerowanego PWA.

    ![Pulpit nawigacyjny usługi Azure DevOps](_img/azure-devops-project-nodejs/devops-projects-dashboard.png) 

1. W projekcie usługi Azure DevOps możesz zapraszać członków zespołu do współpracy i nawiązywać tablicę Kanban, aby zacząć śledzić swoją pracę. Aby uzyskać więcej informacji, zobacz [tutaj](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops?view=azure-devops).

![Azure DevOps — Omówienie](_img/azure-devops-project-nodejs/azure-devops-overview.png)

## <a name="clone-the-repo-and-install-your-gatsby-pwa"></a>Klonowanie repozytorium i Instalowanie programu Gatsby PWA

DevOps Starter tworzy repozytorium Git w Azure Repos lub GitHub. Ten przykład utworzył repozytorium platformy Azure. Następnym krokiem jest klonowanie repozytorium i wprowadzanie zmian.

1. Wybierz pozycję **repozytoria** z **projektu DevOps** , a następnie kliknij pozycję **Klonuj**.  Istnieją różne mechanizmy klonowania repozytorium Git na pulpicie.  Wybierz ten, który pasuje do Twojego środowiska programistycznego.  

    ![Klonowanie repozytorium](_img/azure-devops-project-nodejs/clone-the-repo.png)

1. Po sklonowaniu repozytorium do pulpitu wprowadź pewne zmiany w szablonie początkowym. Zacznij od zainstalowania interfejsu wiersza polecenia GatsbyJS z poziomu terminalu.

   ```powershell
    npm install -g gatsby
   ```

1. Z poziomu terminalu przejdź do katalogu głównego repozytorium. Powinien on zawierać trzy foldery, które wyglądają następująco:

    ```powershell
    Mode                LastWriteTime         Length Name
    ----                -------------         ------ ----
    d-----        2/23/2020  10:42 PM                Application
    d-----        2/23/2020   3:05 PM                ArmTemplates
    d-----        2/23/2020   3:05 PM                Tests
    ```
    
1. Nie chcemy, aby wszystkie pliki w folderze aplikacji zostały zamienione na Gatsby Starter. Uruchom następujące polecenia w kolejności, aby przyciąć je w dół.
    
    ```powershell
    cp .\Application\Dockerfile .
    rmdir Application
    ```

1. Użyj interfejsu wiersza polecenia Gatsby, aby wygenerować przykład PWA. Uruchom `gatsby new` polecenie w terminalu, aby rozpocząć pracę z kreatorem programu PWA i wybrać `gatsby-starter-blog` dla początkowego szablonu. Powinien wyglądać podobnie do tego przykładu:

    ```powershell
    c:\myproject> gatsby new
    √ What is your project called? ... my-gatsby-project
    ? What starter would you like to use? » - Use arrow-keys. Return to submit.
        gatsby-starter-default
        gatsby-starter-hello-world
    >   gatsby-starter-blog
        (Use a different starter)
    ```
    
1. Masz teraz folder o nazwie `my-gatsby-project` . Zmień jego nazwę na `Application` i skopiuj do `Dockerfile` niego.
    
    ```powershell
    mv my-gatsby-project Application
    mv Dockerfile Application
    ```
    
1. W ulubionym edytorze Otwórz pliku dockerfile i Zmień pierwszy wiersz z `FROM node:8` na `FROM node:12` . Ta zmiana gwarantuje, że kontener używa Node.js wersja 12. x zamiast wersji 8. x. GatsbyJS wymaga więcej nowoczesnych wersji Node.js.

1. Następnie otwórz package.jsw pliku w folderze aplikacji i edytuj [pole skrypty](https://docs.npmjs.com/files/package.json#scripts) , aby upewnić się, że serwery deweloperskie i produkcyjne nasłuchują wszystkich dostępnych interfejsów sieciowych (na przykład 0.0.0.0) i port 80. Bez tych ustawień usługa App Service nie może kierować ruchu do aplikacji Node.js działającej w kontenerze. `scripts`Pole powinno wyglądać podobnie do poniższego. W tym celu należy zmienić `develop` `serve` wartości domyślne,, i `start` .

    ```json
      "scripts": {
        "build": "gatsby build",
        "develop": "gatsby develop  -H 0.0.0.0 -p 80",
        "format": "prettier --write \"**/*.{js,jsx,json,md}\"",
        "start": "npm run serve",
        "serve": "npm run build && gatsby serve -H 0.0.0.0 -p 80",
        "clean": "gatsby clean",
        "test": "echo \"Write tests! -> https://gatsby.dev/unit-testing\" && exit 1"
      }
    ```
    
## <a name="edit-your-cicd-pipelines"></a>Edytowanie potoków ciągłej integracji/ciągłego wdrażania

1. Przed zatwierdzeniem kodu w poprzedniej sekcji wprowadź pewne zmiany w kompilacjach i potokach wydań. Edytuj "potok kompilacji" i zaktualizuj zadanie węzła, aby używało Node.js w wersji 12. x. W polu **wersja zadania** ustaw wartość 1. x i pole **wersja** na 12. x.

    ![Aktualizacja Node.js do 12. x](_img/azure-devops-project-nodejs/build-pipeline-update-node.png)

1. W tym przewodniku Szybki Start nie są tworzone testy jednostkowe i wyłączamy te kroki w naszym potoku kompilacji. Podczas pisania testów można ponownie włączyć te kroki. Kliknij prawym przyciskiem myszy, aby wybrać zadania z etykietą **Zainstaluj zależności testów** i **uruchomić testy jednostkowe** i wyłączyć je.

    ![Wyłącz testy kompilacji](_img/azure-devops-project-nodejs/disable-build-unittests.png)

1. Edytuj potoku wydania.

    ![Edytowanie potoku wydania](_img/azure-devops-project-nodejs/edit-release-pipeline.png)

1. Podobnie jak w przypadku potoku kompilacji, należy zmienić zadanie węzła, aby użyć 12. x, i wyłączyć dwa zadania testowe. Twoja wersja powinna wyglądać podobnie do tego zrzutu ekranu.

    ![Ukończony potok wydania](_img/azure-devops-project-nodejs/release-pipeline-complete.png)

1. Przejdź do pliku **views/index.pug** z lewej strony okna przeglądarki.

1. Wybierz pozycję **Edytuj** i wprowadź zmianę w nagłówku h2.  Na przykład wprowadź od razu rozpocząć **pracę z platformą Azure DevOps Starter** lub wprowadź inne zmiany.

1. Wybierz pozycję **Zatwierdź**, a następnie zapisz zmiany.

1. W przeglądarce przejdź do pulpitu nawigacyjnego DevOps Starter.   
W tym momencie powinna być widoczna trwająca kompilacja. Wprowadzone zmiany są automatycznie kompilowane i wdrażane za pomocą potoku ciągłej integracji/ciągłego wdrażania.

## <a name="commit-your-changes-and-examine-the-azure-cicd-pipeline"></a>Zatwierdź zmiany i sprawdź potok ciągłej integracji/ciągłego wdrażania platformy Azure

W poprzednich dwóch krokach dodano Gatsby wygenerowany PWA do repozytorium git i edytowano potoki w celu skompilowania i wdrożenia kodu. Możemy zatwierdzić kod i obserwować postępy w potoku kompilowania i wydawania.

1. Z poziomu katalogu głównego repozytorium git projektu w terminalu uruchom następujące polecenia, aby wypchnąć kod do projektu usługi Azure DevOps:

    ```powershell
    git add .
    git commit -m "My first Gatsby PWA"
    git push
    ```
    
1. Kompilacja została rozpoczęta zaraz po `git push` zakończeniu. Postęp można wykonać przy użyciu **pulpitu nawigacyjnego usługi Azure DevOps**.

3. Po kilku minutach kompilacje i potoki wydań powinny zostać zakończone, a Twoje PWA należy wdrożyć do kontenera. Kliknij link do **punktu końcowego aplikacji** z poziomu pulpitu nawigacyjnego powyżej i powinien zostać wyświetlony projekt Gatsby Start dla blogów.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Można usuwać Azure App Service i inne powiązane zasoby, które zostały utworzone, gdy nie są już potrzebne zasoby. Użyj funkcji **usuwania** na pulpicie nawigacyjnym DevOps Starter.

## <a name="next-steps"></a>Następne kroki

Podczas konfigurowania procesu ciągłej integracji/ciągłego tworzenia potoki kompilacji i wydania są tworzone automatycznie. Możesz zmienić te potoki kompilacji i wydań, aby zaspokoić potrzeby zespołu. Aby dowiedzieć się więcej na temat potoku ciągłej integracji/ciągłego wdrażania, zobacz:

> [!div class="nextstepaction"]
> [Dostosowywanie procesu ciągłego wdrażania](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

