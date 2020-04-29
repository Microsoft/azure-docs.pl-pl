---
title: PRZESTARZAŁE Ciągłej integracji/ciągłego wdrażania za pomocą Azure Container Service i Swarm
description: Używanie Azure Container Service z rozwiązaniami Docker Swarm, Azure Container Registry i Azure DevOps do ciągłego dostarczania aplikacji platformy .NET Core z obsługą kontenera
author: jcorioland
ms.service: container-service
ms.topic: conceptual
ms.date: 12/08/2016
ms.author: jucoriol
ms.custom: mvc
ms.openlocfilehash: 11a6debe735459b617f6f93c3f67a32350dd4623
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76549057"
---
# <a name="deprecated-full-cicd-pipeline-to-deploy-a-multi-container-application-on-azure-container-service-with-docker-swarm-using-azure-devops-services"></a>PRZESTARZAŁE Pełny potok ciągłej integracji/ciągłego wdrażania, który umożliwia wdrażanie aplikacji wielokontenerowych na Azure Container Service przy użyciu rozwiązania Docker Swarm z Azure DevOps Services

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Jednym z największych wyzwań podczas tworzenia nowoczesnych aplikacji dla chmury jest możliwość ciągłego dostarczania tych aplikacji. W tym artykule dowiesz się, jak zaimplementować potok ciągłej integracji i ciągłego wdrażania (CI/CD) przy użyciu Azure Container Service z funkcją Docker Swarm, Azure Container Registry i zarządzaniem Azure Pipelines.


![Przykładowa aplikacja dla sklepu](./media/container-service-docker-swarm-setup-ci-cd/myshop-application.png)

Celem jest ciągłe dostarczanie tej aplikacji w klastrze Docker Swarm przy użyciu Azure DevOps Services. Poniższy rysunek zawiera szczegółowe informacje o tym potoku ciągłego dostarczania:

![Przykładowa aplikacja dla sklepu](./media/container-service-docker-swarm-setup-ci-cd/full-ci-cd-pipeline.png)

Oto krótkie objaśnienie kroków:

1. Zmiany kodu są zatwierdzane w repozytorium kodu źródłowego (tutaj: GitHub) 
1. GitHub wyzwala kompilację w Azure DevOps Services 
1. Azure DevOps Services Pobiera najnowszą wersję źródeł i kompiluje wszystkie obrazy tworzące aplikację 
1. Azure DevOps Services wypychania każdego obrazu do rejestru platformy Docker utworzonego przy użyciu usługi Azure Container Registry 
1. Azure DevOps Services wyzwala nową wersję 
1. Wydanie uruchamia niektóre polecenia przy użyciu protokołu SSH w węźle głównym klastra usługi Azure Container Service 
1. Platforma Docker Swarm w klastrze Pobiera najnowszą wersję obrazów 
1. Nowa wersja aplikacji jest wdrażana przy użyciu Docker Compose 

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka należy wykonać następujące zadania:

- [Utworzenie klastra Swarm usługi kontenera platformy Azure](container-service-deployment.md)
- [Połączenie z klastrem Swarm w usłudze kontenera platformy Azure](../container-service-connect.md)
- [Tworzenie usługi Azure Container Registry](../../container-registry/container-registry-get-started-portal.md)
- [Utworzenie Azure DevOps Services organizacji i projektu](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student)
- [Rozwidlenie repozytorium GitHub do konta usługi GitHub](https://github.com/jcorioland/MyShop/)

[!INCLUDE [container-service-swarm-mode-note](../../../includes/container-service-swarm-mode-note.md)]

Potrzebna jest również maszyna z systemem Ubuntu (14,04 lub 16,04) z zainstalowanym platformą Docker. Ta maszyna jest używana przez Azure DevOps Services w trakcie procesów Azure Pipelines. Jednym ze sposobów tworzenia tego komputera jest użycie obrazu dostępnego w portalu Azure Marketplace. 

## <a name="step-1-configure-your-azure-devops-services-organization"></a>Krok 1. Konfigurowanie organizacji Azure DevOps Services 

W tej sekcji skonfigurujesz organizację Azure DevOps Services.

### <a name="configure-an-azure-devops-services-linux-build-agent"></a>Skonfiguruj Azure DevOps Services agenta kompilacji systemu Linux

Aby utworzyć obrazy platformy Docker i wypchnąć te obrazy do usługi Azure Container Registry z kompilacji Azure DevOps Services, należy zarejestrować agenta systemu Linux. Dostępne są następujące opcje instalacji:

* [Wdrażanie agenta w systemie Linux](https://www.visualstudio.com/docs/build/admin/agents/v2-linux)

* [Uruchamianie agenta Azure DevOps Services przy użyciu platformy Docker](https://hub.docker.com/r/microsoft/vsts-agent)

### <a name="install-the-docker-integration-azure-devops-services-extension"></a>Instalowanie rozszerzenia Azure DevOps Services integracji platformy Docker

Firma Microsoft udostępnia rozszerzenie Azure DevOps Services do pracy z platformą Docker w procesach Azure Pipelines. To rozszerzenie jest dostępne w [witrynie Azure DevOps Services Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-vscs-rm.docker). Kliknij przycisk **Instaluj** , aby dodać to rozszerzenie do organizacji Azure DevOps Services:

![Instalowanie integracji platformy Docker](./media/container-service-docker-swarm-setup-ci-cd/install-docker-vsts.png)

Zostanie wyświetlony monit o połączenie się z organizacją usługi Azure DevOps Services przy użyciu swoich poświadczeń. 

### <a name="connect-azure-devops-services-and-github"></a>Łączenie Azure DevOps Services i GitHub

Skonfiguruj połączenie między projektem Azure DevOps Services i kontem usługi GitHub.

1. W projekcie Azure DevOps Services kliknij ikonę **Ustawienia** na pasku narzędzi, a następnie wybierz pozycję **usługi**.

    ![Azure DevOps Services — połączenie zewnętrzne](./media/container-service-docker-swarm-setup-ci-cd/vsts-services-menu.png)

1. Po lewej stronie kliknij pozycję **nowy punkt końcowy** > usługi**GitHub**.

    ![Azure DevOps Services — GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github.png)

1. Aby autoryzować Azure DevOps Services do pracy z kontem usługi GitHub, kliknij przycisk **Autoryzuj** i postępuj zgodnie z procedurą w otwartym oknie.

    ![Azure DevOps Services — Autoryzuj witrynę GitHub](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-authorize.png)

### <a name="connect-azure-devops-services-to-your-azure-container-registry-and-azure-container-service-cluster"></a>Łączenie Azure DevOps Services z usługą Azure Container Registry i klastrem Azure Container Service

Ostatnie kroki przed przystąpieniem do potoku ciągłej integracji/ciągłego konfigurowania połączeń zewnętrznych z rejestrem kontenerów i klastrem Docker Swarm na platformie Azure. 

1. W ustawieniach **usług** projektu Azure DevOps Services należy dodać punkt końcowy usługi typu **Docker Registry**. 

1. W otwartym oknie podręcznym wprowadź adres URL i poświadczenia usługi Azure Container Registry.

    ![Azure DevOps Services — rejestr platformy Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-registry.png)

1. W przypadku klastra Docker Swarm Dodaj punkt końcowy typu **SSH**. Następnie wprowadź informacje o połączeniu SSH klastra Swarm.

    ![Azure DevOps Services — SSH](./media/container-service-docker-swarm-setup-ci-cd/vsts-ssh.png)

Cała konfiguracja jest teraz gotowa. W następnych krokach utworzysz potok ciągłej integracji/ciągłego wdrażania, który kompiluje i wdraża aplikację w klastrze Docker Swarm. 

## <a name="step-2-create-the-build-pipeline"></a>Krok 2. Tworzenie potoku kompilacji

W tym kroku skonfigurujesz potok kompilacji dla projektu Azure DevOps Services i zdefiniujesz przepływ pracy kompilacji dla obrazów kontenerów

### <a name="initial-pipeline-setup"></a>Początkowa konfiguracja potoku

1. Aby utworzyć potok kompilacji, Połącz się z projektem Azure DevOps Services i kliknij pozycję **kompilacja & wersja**. 

1. W sekcji **definicje kompilacji** kliknij pozycję **+ Nowy**. Wybierz **pusty** szablon.

    ![Azure DevOps — nowy potok kompilacji](./media/container-service-docker-swarm-setup-ci-cd/create-build-vsts.png)

1. Skonfiguruj nową kompilację przy użyciu źródła repozytorium GitHub, sprawdź **ciągłą integrację**i wybierz kolejkę agentów, w której został zarejestrowany agent systemu Linux. Kliknij przycisk **Utwórz** , aby utworzyć potok kompilacji.

    ![Azure DevOps Services — tworzenie potoku kompilacji](./media/container-service-docker-swarm-setup-ci-cd/vsts-create-build-github.png)

1. Na stronie **definicje kompilacji** najpierw otwórz kartę **repozytorium** i skonfiguruj kompilację tak, aby korzystała z rozwidlenia projektu, który został utworzony w wymaganiach wstępnych. Upewnij się, że wybrano opcję *ACS-docs* jako **gałąź domyślną**.

    ![Azure DevOps Services — Konfiguracja repozytorium kompilacji](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-repo-conf.png)

1. Na karcie **wyzwalacze** Skonfiguruj kompilację do wyzwolenia po każdym zatwierdzeniu. Wybierz pozycję **ciągła integracja** i zmiany w usłudze **Batch**.

    ![Azure DevOps Services — Konfiguracja wyzwalacza kompilacji](./media/container-service-docker-swarm-setup-ci-cd/vsts-github-trigger-conf.png)

### <a name="define-the-build-workflow"></a>Definiowanie przepływu pracy kompilacji
W następnych krokach zdefiniujemy przepływ pracy kompilacji. Istnieje pięć obrazów kontenerów do skompilowania dla aplikacji dla *sklepu* . Każdy obraz jest tworzony przy użyciu pliku dockerfile znajdującego się w folderach projektu:

* ProductsApi
* Serwer proxy
* RatingsApi
* RecommendationsApi
* ShopFront

Należy dodać dwa kroki platformy Docker dla każdego obrazu, jeden do skompilowania obrazu, a drugi do wypchnięcia obrazu w usłudze Azure Container Registry. 

1. Aby dodać krok w przepływie pracy kompilacji, kliknij pozycję **+ Dodaj krok kompilacji** i wybierz pozycję **Docker**.

    ![Azure DevOps Services — Dodawanie kroków kompilacji](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-add-task.png)

1. Dla każdego obrazu skonfiguruj jeden krok, który używa `docker build` polecenia.

    ![Azure DevOps Services — kompilacja platformy Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-build.png)

    Dla operacji kompilowania wybierz usługę Azure Container Registry, akcję **kompilacja obrazu** i pliku dockerfile, która definiuje każdy obraz. Ustaw **kontekst kompilacji** jako katalog główny pliku dockerfile i zdefiniuj **nazwę obrazu**. 
    
    Jak pokazano na powyższym ekranie, należy uruchomić nazwę obrazu z identyfikatorem URI usługi Azure Container Registry. (Można również użyć zmiennej kompilacji do Sparametryzuj znacznika obrazu, takiego jak identyfikator kompilacji w tym przykładzie).

1. Dla każdego obrazu Skonfiguruj drugi krok, który używa `docker push` polecenia.

    ![Azure DevOps Services — wypychanie Docker](./media/container-service-docker-swarm-setup-ci-cd/vsts-docker-push.png)

    W przypadku operacji wypychania wybierz usługę Azure Container Registry, akcję **push a image** , a następnie wprowadź **nazwę obrazu** skompilowaną w poprzednim kroku.

1. Po skonfigurowaniu kroków kompilacji i wypychania dla każdego z pięciu obrazów Dodaj dwa więcej kroków w przepływie pracy kompilacji.

    a. Zadanie wiersza polecenia, które używa skryptu bash do zastąpienia wystąpienia *BuildNumber* w pliku Docker-Compose. yml przy użyciu bieżącego identyfikatora kompilacji. Aby uzyskać szczegółowe informacje, zobacz następujący ekran.

    ![Azure DevOps Services — Zaktualizuj plik redagowania](./media/container-service-docker-swarm-setup-ci-cd/vsts-build-replace-build-number.png)

    b. Zadanie, które porzuca zaktualizowany plik redagowania jako artefakt kompilacji, aby można go było użyć w wydaniu. Aby uzyskać szczegółowe informacje, zobacz następujący ekran.

    ![Azure DevOps Services — publikowanie pliku redagowania](./media/container-service-docker-swarm-setup-ci-cd/vsts-publish-compose.png) 

1. Kliknij przycisk **Zapisz** i nadaj nazwę potoku kompilacji.

## <a name="step-3-create-the-release-pipeline"></a>Krok 3. Tworzenie potoku wydania

Azure DevOps Services umożliwia [Zarządzanie wersjami w różnych środowiskach](https://www.visualstudio.com/team-services/release-management/). Możesz włączyć ciągłe wdrażanie, aby upewnić się, że aplikacja jest wdrożona w różnych środowiskach (takich jak programowanie, testowanie, przedprodukcyjne i produkcyjne) w sposób płynny. Można utworzyć nowe środowisko, które reprezentuje klaster Azure Container Service Docker Swarm.

![Azure DevOps Services — wydanie do usługi ACS](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-acs.png) 

### <a name="initial-release-setup"></a>Początkowa konfiguracja wydania

1. Aby utworzyć potok wersji, kliknij pozycje **wersje** > **+ wydanie** .

1. Aby skonfigurować źródło artefaktów, kliknij pozycję **artefakty** > **Połącz Źródło artefaktu**. W tym miejscu Połącz ten nowy potok wydania z kompilacją zdefiniowaną w poprzednim kroku. W ten sposób plik Docker-Compose. yml jest dostępny w procesie zwalniania.

    ![Artefakty wersji Azure DevOps Services](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-artefacts.png) 

1. Aby skonfigurować wyzwalacz wydania, kliknij przycisk **wyzwalacze** i wybierz pozycję **ciągłe wdrażanie**. Ustaw wyzwalacz dla tego samego źródła artefaktu. To ustawienie gwarantuje, że nowe wydanie zostanie uruchomione zaraz po pomyślnym zakończeniu kompilacji.

    ![Wyzwalacze Azure DevOps Services — wydawanie](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-trigger.png) 

### <a name="define-the-release-workflow"></a>Definiowanie przepływu pracy wydania

Przepływ pracy wydania składa się z dwóch zadań, które zostały dodane.

1. Skonfiguruj zadanie, aby bezpiecznie skopiować plik redagowania do folderu *Deploy* w węźle głównym Docker Swarm, używając wcześniej skonfigurowanego połączenia SSH. Aby uzyskać szczegółowe informacje, zobacz następujący ekran.

    ![Azure DevOps Services — punkt połączenia usługi](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-scp.png)

1. Skonfiguruj drugie zadanie w celu wykonania polecenia bash w celu uruchomienia `docker` polecenia `docker-compose` i poleceń w węźle głównym. Aby uzyskać szczegółowe informacje, zobacz następujący ekran.

    ![Azure DevOps Services — Release bash](./media/container-service-docker-swarm-setup-ci-cd/vsts-release-bash.png)

    Polecenie wykonane na wzorcu używa interfejsu wiersza polecenia platformy Docker i interfejsu wiersza poleceń platformy Docker, aby wykonać następujące zadania:

   - Zaloguj się do usługi Azure Container Registry (używa trzech variab'les kompilacji, które są zdefiniowane na karcie **zmienne** )
   - Zdefiniuj zmienną **DOCKER_HOST** do pracy z punktem końcowym Swarm (: 2375)
   - Przejdź do folderu *Wdróż* , który został utworzony przez poprzednie zadanie bezpiecznego kopiowania i który zawiera plik Docker-Compose. yml 
   - Wykonaj `docker-compose` polecenia ściągające nowe obrazy, Zatrzymaj usługi, Usuń usługi i Utwórz kontenery.

     >[!IMPORTANT]
     > Jak pokazano na powyższym ekranie, nie zaznaczaj pola wyboru **Niepowodzenie w przypadku stderr** . Jest to ważne ustawienie, ponieważ `docker-compose` program drukuje kilka komunikatów diagnostycznych, takich jak kontenery są zatrzymywane lub usuwane, przy użyciu standardowego błędu wyjściowego. Jeśli pole wyboru jest zaznaczone, Azure DevOps Services raporty, że wystąpiły błędy w trakcie wydania, nawet jeśli wszystko przebiega prawidłowo.
     >
1. Zapisz ten nowy potok wydania.


>[!NOTE]
>To wdrożenie obejmuje pewien przestój, ponieważ zatrzymujemy stare usługi i uruchamiamy nowe. Aby tego uniknąć, należy wykonać wdrożenie Blue-Green.
>

## <a name="step-4-test-the-cicd-pipeline"></a>Krok 4. Testowanie potoku ciągłej integracji/ciągłego wdrażania

Teraz, po zakończeniu konfiguracji, czas na przetestowanie nowego potoku ciągłej integracji/ciągłego wdrażania. Najprostszym sposobem przetestowania jest zaktualizowanie kodu źródłowego i zatwierdzenie zmian w repozytorium GitHub. Kilka sekund po wypchnięciu kodu zostanie wyświetlona nowa kompilacja działająca w Azure DevOps Services. Po pomyślnym zakończeniu zostanie wyzwolone nowe wydanie i zostanie wdrożona nowa wersja aplikacji w klastrze Azure Container Service.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat ciągłej integracji/ciągłego wdrażania Azure DevOps Services, zobacz artykuł z [dokumentacją Azure Pipelines](/azure/devops/pipelines/?view=azure-devops) .
