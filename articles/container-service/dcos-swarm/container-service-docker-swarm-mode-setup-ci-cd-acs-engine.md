---
title: PRZESTARZAŁE Ciągłej integracji/ciągłego wdrażania za pomocą aparatu Azure Container Service i trybu Swarm
description: Używanie aparatu Azure Container Service z trybem Docker Swarm, Azure Container Registry i Azure DevOps do ciągłego dostarczania wielokontenerowej aplikacji platformy .NET Core
author: diegomrtnzg
ms.service: container-service
ms.topic: conceptual
ms.date: 05/27/2017
ms.author: dimart
ms.custom: mvc
ms.openlocfilehash: 1ec7ece6f5afd1bbd2613ae08af04b82e8a156b2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76277917"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-acs-engine-and-docker-swarm-mode-using-azure-devops"></a>PRZESTARZAŁE Pełny potok ciągłej integracji/ciągłego wdrażania, aby wdrożyć wielokontenerową aplikację na Azure Container Service z aparatem ACS i trybem Docker Swarm przy użyciu usługi Azure DevOps

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

*Ten artykuł jest oparty na [pełnym potoku ciągłej integracji/ciągłego wdrażania, aby wdrożyć wielokontenerową aplikację na Azure Container Service za pomocą narzędzia Docker Swarm przy użyciu dokumentacji usługi Azure DevOps](container-service-docker-swarm-setup-ci-cd.md)*

Obecnie, jednym z największych wyzwań podczas tworzenia nowoczesnych aplikacji dla chmury jest możliwość ciągłego dostarczania tych aplikacji. W tym artykule dowiesz się, jak zaimplementować potok ciągłej integracji i ciągłego wdrażania (CI/CD) przy użyciu: 
* Aparat Azure Container Service z trybem Docker Swarm
* Azure Container Registry
* Azure DevOps


![Przykładowa aplikacja dla sklepu](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/myshop-application.png)

Celem jest ciągłe dostarczanie tej aplikacji w klastrze trybu Docker Swarm przy użyciu usługi Azure DevOps. Poniższy rysunek zawiera szczegółowe informacje o tym potoku ciągłego dostarczania:

![Przykładowa aplikacja dla sklepu](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/full-ci-cd-pipeline.png)

Oto krótkie objaśnienie kroków:

1. Zmiany kodu są zatwierdzane w repozytorium kodu źródłowego (tutaj: GitHub) 
2. Usługa GitHub wyzwala kompilację w usłudze Azure DevOps 
3. Usługa Azure DevOps Pobiera najnowszą wersję źródeł i kompiluje wszystkie obrazy tworzące aplikację. 
4. Usługa Azure DevOps wypychanie każdego obrazu do rejestru platformy Docker utworzonego przy użyciu usługi Azure Container Registry 
5. Usługa Azure DevOps wyzwala nową wersję 
6. Wydanie uruchamia niektóre polecenia przy użyciu protokołu SSH w węźle głównym klastra usługi Azure Container Service 
7. Tryb Docker Swarm w klastrze ściąga najnowszą wersję obrazów 
8. Nowa wersja aplikacji jest wdrażana przy użyciu stosu platformy Docker 

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka należy wykonać następujące zadania:

- [Tworzenie klastra trybu Swarm w Azure Container Service z aparatem ACS](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acsengine-swarmmode)
- [Połączenie z klastrem Swarm w usłudze kontenera platformy Azure](../container-service-connect.md)
- [Tworzenie usługi Azure Container Registry](../../container-registry/container-registry-get-started-portal.md)
- [Utworzenie organizacji i projektu usługi Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Rozwidlenie repozytorium GitHub do konta usługi GitHub](https://github.com/jcorioland/MyShop/tree/docker-linux)

>[!NOTE]
> Koordynator rozwiązania Docker Swarm w usłudze Azure Container Service korzysta ze starszego autonomicznego rozwiązania Swarm. Obecnie zintegrowany [tryb Swarm](https://docs.docker.com/engine/swarm/) (na platformie Docker 1.12 lub nowszej) nie jest obsługiwanym koordynatorem w usłudze Azure Container Service. Z tego powodu korzystamy z [aparatu usług ACS](https://github.com/Azure/acs-engine/blob/master/docs/swarmmode.md), [szablonu szybkiego startu](https://azure.microsoft.com/resources/templates/101-acsengine-swarmmode/)z wkładem do społeczności lub rozwiązania Docker w [witrynie Azure Marketplace](https://azuremarketplace.microsoft.com).
>

## <a name="step-1-configure-your-azure-devops-organization"></a>Krok 1. Konfigurowanie organizacji usługi Azure DevOps 

W tej sekcji skonfigurujesz organizację usługi Azure DevOps. Aby skonfigurować Azure DevOps Services punkty końcowe, w projekcie usługi Azure DevOps kliknij ikonę **Ustawienia** na pasku narzędzi, a następnie wybierz pozycję **usługi**.

![Otwórz punkt końcowy usług](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/services-vsts.PNG)

### <a name="connect-azure-devops-and-azure-account"></a>Połącz usługę Azure DevOps i konto platformy Azure

Skonfiguruj połączenie między projektem usługi Azure DevOps i kontem platformy Azure.

1. Po lewej stronie kliknij pozycję **nowy punkt końcowy** > usługi**Azure Resource Manager**.
2. Aby autoryzować usługę Azure DevOps do pracy z kontem platformy Azure, wybierz **subskrypcję** i kliknij przycisk **OK**.

    ![Azure DevOps — Autoryzuj platformę Azure](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-azure.PNG)

### <a name="connect-azure-devops-and-github"></a>Łączenie usługi Azure DevOps i serwisu GitHub

Skonfiguruj połączenie między projektem usługi Azure DevOps i kontem w serwisie GitHub.

1. Po lewej stronie kliknij pozycję **nowy punkt końcowy** > usługi**GitHub**.
2. Aby autoryzować usługę Azure DevOps do pracy z kontem w serwisie GitHub, kliknij przycisk **Autoryzuj** i postępuj zgodnie z procedurą w otwartym oknie.

    ![Azure DevOps — Autoryzuj witrynę GitHub](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github.png)

### <a name="connect-azure-devops-to-your-azure-container-service-cluster"></a>Łączenie usługi Azure DevOps z klastrem Azure Container Service

Ostatnie kroki przed przystąpieniem do potoku ciągłej integracji/ciągłego konfigurowania połączeń zewnętrznych z klastrem Docker Swarm na platformie Azure. 

1. W przypadku klastra Docker Swarm Dodaj punkt końcowy typu **SSH**. Następnie wprowadź informacje o połączeniu SSH klastra Swarm (węzeł główny).

    ![Azure DevOps — SSH](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-ssh.png)

Cała konfiguracja jest teraz gotowa. W następnych krokach utworzysz potok ciągłej integracji/ciągłego wdrażania, który kompiluje i wdraża aplikację w klastrze Docker Swarm. 

## <a name="step-2-create-the-build-pipeline"></a>Krok 2. Tworzenie potoku kompilacji

W tym kroku należy skonfigurować potok kompilacji dla projektu usługi Azure DevOps i zdefiniować przepływ pracy kompilacji dla obrazów kontenerów

### <a name="initial-pipeline-setup"></a>Początkowa konfiguracja potoku

1. Aby utworzyć potok kompilacji, Połącz się z projektem usługi Azure DevOps i kliknij pozycję **kompiluj & wydania**. W sekcji **definicje kompilacji** kliknij pozycję **+ Nowy**. 

    ![Azure DevOps — nowy potok kompilacji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-build-vsts.PNG)

2. Wybierz **pusty proces**.

    ![Azure DevOps — nowy pusty potok kompilacji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/create-empty-build-vsts.PNG)

4. Następnie kliknij kartę **zmienne** i Utwórz dwie nowe zmienne: **RegistryURL** i **AgentURL**. Wklej wartości rejestru i agentów klastra DNS.

    ![Azure DevOps — konfiguracja zmiennych kompilacji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-variables.png)

5. Na stronie **definicje kompilacji** Otwórz kartę **wyzwalacze** i skonfiguruj kompilację do korzystania z ciągłej integracji z rozwidleniem projektu dla elementu WebShop utworzonego w sekcji wymagania wstępne. Następnie wybierz pozycję **zmiany**w usłudze Batch. Upewnij się, że wybrano opcję *Docker-Linux* jako **specyfikację gałęzi**.

    ![Azure DevOps — konfiguracja repozytorium kompilacji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-github-repo-conf.PNG)


6. Na koniec kliknij kartę **Opcje** i Skonfiguruj domyślną kolejkę agentów w **wersji zapoznawczej systemu Linux**.

    ![Azure DevOps — Konfiguracja agenta hosta](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-agent.png)

### <a name="define-the-build-workflow"></a>Definiowanie przepływu pracy kompilacji
W następnych krokach zdefiniujemy przepływ pracy kompilacji. Najpierw należy skonfigurować źródło kodu. W tym celu wybierz pozycję **GitHub** i **repozytorium** i **gałąź** (Docker-Linux).

![Azure DevOps — Konfigurowanie źródła kodu](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-source-code.png)

Istnieje pięć obrazów kontenerów do skompilowania dla aplikacji dla *sklepu* . Każdy obraz jest tworzony przy użyciu pliku dockerfile znajdującego się w folderach projektu:

* ProductsApi
* Serwer proxy
* RatingsApi
* RecommendationsApi
* ShopFront

Wymagane są dwa kroki platformy Docker dla każdego obrazu, jeden do skompilowania obrazu, a drugi do wypychania obrazu w usłudze Azure Container Registry. 

1. Aby dodać krok w przepływie pracy kompilacji, kliknij pozycję **+ Dodaj krok kompilacji** i wybierz pozycję **Docker**.

    ![Azure DevOps — Dodawanie kroków kompilacji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-add-task.png)

2. Dla każdego obrazu skonfiguruj jeden krok, który używa `docker build` polecenia.

    ![Azure DevOps — kompilacja platformy Docker](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-build.png)

    Dla operacji kompilowania wybierz Azure Container Registry, akcję Kompiluj a **Image** i pliku dockerfile, która definiuje każdy obraz. Ustaw **katalog roboczy** jako katalog główny pliku dockerfile, zdefiniuj **nazwę obrazu**i wybierz pozycję **Dołącz najnowszy tag**.
    
    Nazwa obrazu musi mieć następujący format: ```$(RegistryURL)/[NAME]:$(Build.BuildId)```. Zastąp wartość **[name]** nazwą obrazu:
    - ```proxy```
    - ```products-api```
    - ```ratings-api```
    - ```recommendations-api```
    - ```shopfront```

3. Dla każdego obrazu Skonfiguruj drugi krok, który używa `docker push` polecenia.

    ![Azure DevOps — wypychanie Docker](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-docker-push.png)

    W przypadku operacji wypychania wybierz usługę Azure Container Registry, akcję **push a image** , wprowadź **nazwę obrazu** skompilowaną w poprzednim kroku, a następnie wybierz pozycję **Uwzględnij najnowszy tag**.

4. Po skonfigurowaniu kroków kompilacji i wypychania dla każdego z pięciu obrazów Dodaj trzy więcej kroków w przepływie pracy kompilacji.

   ![Azure DevOps — Dodawanie zadania wiersza polecenia](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-command-task.png)

   1. Zadanie wiersza polecenia, które używa skryptu bash do zastąpienia wystąpienia *RegistryURL* w pliku Docker-Compose. yml za pomocą zmiennej RegistryURL. 
    
       ```-c "sed -i 's/RegistryUrl/$(RegistryURL)/g' src/docker-compose-v3.yml"```

       ![Azure DevOps — Zaktualizuj plik redagowania przy użyciu adresu URL rejestru](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-replace-registry.png)

   2. Zadanie wiersza polecenia, które używa skryptu bash do zastąpienia wystąpienia *AgentURL* w pliku Docker-Compose. yml za pomocą zmiennej AgentURL.
  
       ```-c "sed -i 's/AgentUrl/$(AgentURL)/g' src/docker-compose-v3.yml"```

      1. Zadanie, które porzuca zaktualizowany plik redagowania jako artefakt kompilacji, aby można go było użyć w wydaniu. Aby uzyskać szczegółowe informacje, zobacz następujący ekran.

      ![Azure DevOps — publikowanie artefaktu](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish.png) 

      ![Azure DevOps — publikowanie pliku redagowania](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-publish-compose.png) 

5. Kliknij pozycję **zapisz & kolejkę** , aby przetestować potok kompilacji.

   ![Azure DevOps — zapisywanie i kolejkowanie](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-save.png) 

   ![Azure DevOps — Nowa kolejka](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-queue.png) 

6. Jeśli **kompilacja** jest poprawna, musisz zobaczyć ten ekran:

   ![Azure DevOps — kompilacja powiodła się](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-build-succeeded.png) 

## <a name="step-3-create-the-release-pipeline"></a>Krok 3. Tworzenie potoku wydania

Usługa Azure DevOps umożliwia [Zarządzanie wersjami w różnych środowiskach](https://www.visualstudio.com/team-services/release-management/). Możesz włączyć ciągłe wdrażanie, aby upewnić się, że aplikacja jest wdrożona w różnych środowiskach (takich jak programowanie, testowanie, przedprodukcyjne i produkcyjne) w sposób płynny. Można utworzyć środowisko, które reprezentuje Azure Container Service klaster trybu Docker Swarm.

![Azure DevOps — wydanie do usługi ACS](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Początkowa konfiguracja wydania

1. Aby utworzyć potok wersji, kliknij pozycje **wersje** > **+ wydanie** .

2. Aby skonfigurować źródło artefaktów, kliknij pozycję **artefakty** > **Połącz Źródło artefaktu**. W tym miejscu Połącz ten nowy potok wydania z kompilacją zdefiniowaną w poprzednim kroku. Po tym pliku Docker-Compose. yml jest dostępny w procesie zwalniania.

    ![Azure DevOps — artefakty wersji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-artefacts.png) 

3. Aby skonfigurować wyzwalacz wydania, kliknij przycisk **wyzwalacze** i wybierz pozycję **ciągłe wdrażanie**. Ustaw wyzwalacz dla tego samego źródła artefaktu. To ustawienie gwarantuje, że nowa wersja zostanie uruchomiona, gdy kompilacja zakończy się pomyślnie.

    ![Azure DevOps — wyzwalacze wydania](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-trigger.png) 

4. Aby skonfigurować zmienne wersji, kliknij przycisk **zmienne** i wybierz pozycję **+ zmienna** , aby utworzyć trzy nowe zmienne z informacjami rejestru: **Docker. username**, **Docker. Password**i **Docker. Registry**. Wklej wartości rejestru i agentów klastra DNS.

    ![Azure DevOps — konfiguracja repozytorium kompilacji](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-variables.png)

    >[!IMPORTANT]
    > Jak pokazano na poprzednim ekranie, kliknij pole wyboru **Zablokuj** w polu Docker. Password. To ustawienie jest ważne, aby ograniczyć hasło.
    >

### <a name="define-the-release-workflow"></a>Definiowanie przepływu pracy wydania

Przepływ pracy wydania składa się z dwóch zadań, które zostały dodane.

1. Skonfiguruj zadanie, aby bezpiecznie skopiować plik redagowania do folderu *Deploy* w węźle głównym Docker Swarm, używając wcześniej skonfigurowanego połączenia SSH. Aby uzyskać szczegółowe informacje, zobacz następujący ekran.
    
    Folder źródłowy:```$(System.DefaultWorkingDirectory)/MyShop-CI/drop```

    ![Azure DevOps — punkt połączenia usługi](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-scp.png)

2. Skonfiguruj drugie zadanie w celu wykonania polecenia bash w celu uruchomienia `docker` polecenia `docker stack deploy` i poleceń w węźle głównym. Aby uzyskać szczegółowe informacje, zobacz następujący ekran.

    ```
    docker login -u $(docker.username) -p $(docker.password) $(docker.registry) && export DOCKER_HOST=:2375 && cd deploy && docker stack deploy --compose-file docker-compose-v3.yml myshop --with-registry-auth
    ```

    ![Azure DevOps — wydanie bash](./media/container-service-docker-swarm-mode-setup-ci-cd-acs-engine/vsts-release-bash.png)

    Polecenie wykonywane na wzorcu korzysta z interfejsu wiersza polecenia platformy Docker i interfejsu CLI platformy Docker, aby wykonać następujące zadania:

   - Zaloguj się do usługi Azure Container Registry (używa trzech zmiennych kompilacji, które są zdefiniowane na karcie **zmienne** )
   - Zdefiniuj zmienną **DOCKER_HOST** do pracy z punktem końcowym Swarm (: 2375)
   - Przejdź do folderu *Wdróż* , który został utworzony przez poprzednie zadanie bezpiecznego kopiowania i który zawiera plik Docker-Compose. yml 
   - Wykonaj `docker stack deploy` polecenia, które pobierają nowe obrazy i tworzą kontenery.

     >[!IMPORTANT]
     > Jak pokazano na poprzednim ekranie, nie zaznaczaj pola wyboru **Niepowodzenie w przypadku stderr** . To ustawienie umożliwia ukończenie procesu wydania z powodu `docker-compose` drukowania kilku komunikatów diagnostycznych, takich jak kontenery są zatrzymywane lub usuwane, w standardowym wyjściu błędu. Jeśli zaznaczono pole wyboru, usługa Azure DevOps raportuje, że wystąpiły błędy podczas tej wersji, nawet jeśli wszystko przebiega prawidłowo.
     >
3. Zapisz ten nowy potok wydania.

## <a name="step-4-test-the-cicd-pipeline"></a>Krok 4. Testowanie potoku ciągłej integracji/ciągłego wdrażania

Teraz, po zakończeniu konfiguracji, czas na przetestowanie nowego potoku ciągłej integracji/ciągłego wdrażania. Najprostszym sposobem przetestowania jest zaktualizowanie kodu źródłowego i zatwierdzenie zmian w repozytorium GitHub. Kilka sekund po wypchnięciu kodu zostanie wyświetlona nowa kompilacja działająca na platformie Azure DevOps. Po pomyślnym zakończeniu nowe wydanie zostanie wyzwolone i wdrożone Nowa wersja aplikacji w klastrze Azure Container Service.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat ciągłej integracji/ciągłego wdrażania za pomocą usługi Azure DevOps, zobacz artykuł z [dokumentacją Azure Pipelines](/azure/devops/pipelines/?view=azure-devops) .
* Aby uzyskać więcej informacji o aparacie usługi ACS, zobacz [repozytorium usługi ACS Engine](https://github.com/Azure/acs-engine)w witrynie GitHub.
* Aby uzyskać więcej informacji o trybie Docker Swarm, zobacz [Omówienie trybu Docker Swarm](https://docs.docker.com/engine/swarm/).
