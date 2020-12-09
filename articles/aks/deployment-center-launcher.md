---
title: Centrum wdrażania dla usługi Azure Kubernetes
description: Centrum wdrażania w usłudze Azure DevOps upraszcza Konfigurowanie niezawodnego potoku usługi Azure DevOps dla aplikacji
ms.author: puagarw
ms.topic: tutorial
ms.date: 07/12/2019
author: pulkitaggarwl
ms.openlocfilehash: 15b0413eabcfae7e3a4b28243caf2a708260ccae
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932221"
---
# <a name="deployment-center-for-azure-kubernetes"></a>Centrum wdrażania dla usługi Azure Kubernetes

Centrum wdrażania w usłudze Azure DevOps upraszcza Konfigurowanie niezawodnego potoku usługi Azure DevOps dla aplikacji. Domyślnie centrum wdrażania konfiguruje potok usługi Azure DevOps w celu wdrożenia aktualizacji aplikacji w klastrze Kubernetes. Można rozszerzyć domyślny skonfigurowany potok usługi Azure DevOps, a także dodać bogatsze możliwości: możliwość uzyskania zatwierdzenia przed wdrożeniem, aprowizacji dodatkowych zasobów platformy Azure, uruchamiania skryptów, uaktualniania aplikacji, a nawet wykonywania dodatkowych testów weryfikacyjnych.

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Skonfiguruj potok Azure DevOps, aby wdrożyć aktualizacje aplikacji w klastrze Kubernetes.
> * Zapoznaj się z potokiem ciągłej integracji (CI).
> * Zapoznaj się z potokiem ciągłego dostarczania (CD).
> * Wyczyść zasoby.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Możesz uzyskać ją bezpłatnie za pośrednictwem programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

* Klaster usługi Azure Kubernetes Service (AKS).

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).

1. Wybierz opcję [Cloud Shell](../cloud-shell/overview.md) po prawej stronie paska menu w Azure Portal.

1. Aby utworzyć klaster AKS, uruchom następujące polecenia:

    ```azurecli
    # Create a resource group in the South India location:

    az group create --name azooaks --location southindia

    # Create a cluster named azookubectl with one node.

    az aks create --resource-group azooaks --name azookubectl --node-count 1 --enable-addons monitoring --generate-ssh-keys
    ```

## <a name="deploy-application-updates-to-a-kubernetes-cluster"></a>Wdrażanie aktualizacji aplikacji w klastrze Kubernetes

1. Przejdź do grupy zasobów utworzonej w poprzedniej sekcji.

1. Wybierz klaster AKS, a następnie wybierz pozycję **centrum wdrażania (wersja zapoznawcza)** w lewym bloku. Wybierz pozycję **Rozpocznij**.

   ![Zrzut ekranu przedstawia Azure Portal ze strzałką wskazującą centrum wdrażania.](media/deployment-center-launcher/settings.png)

1. Wybierz lokalizację kodu i wybierz pozycję **dalej**. Następnie wybierz jedną z obecnie obsługiwanych repozytoriów: **[Azure Repos](/azure/devops/repos/index)** lub **GitHub**.

    Azure Repos to zestaw narzędzi kontroli wersji, które ułatwiają zarządzanie kodem. Bez względu na to, czy projekt oprogramowania jest duży, czy mały, z użyciem kontroli wersji jak najszybciej jest dobrym pomysłem.

    - **Azure Repos**: Wybierz repozytorium z istniejącego projektu i organizacji.

        ![Azure Repos](media/deployment-center-launcher/azure-repos.gif)

    - **GitHub**: Autoryzuj i wybieraj repozytorium dla konta usługi GitHub.

        ![Animacja przedstawia proces w serwisie GitHub wybierania usługi GitHub jako źródła, a następnie wybraniu repozytorium.](media/deployment-center-launcher/github.gif)


1. Centrum wdrażania analizuje repozytorium i wykrywa pliku dockerfile. Jeśli chcesz zaktualizować pliku dockerfile, możesz edytować określony numer portu.

    ![Ustawienia aplikacji](media/deployment-center-launcher/application-settings.png)

    Jeśli repozytorium nie zawiera pliku dockerfile, system wyświetli komunikat, aby go zatwierdzić.

    ![Zrzut ekranu przedstawia centrum wdrażania z komunikatem nie można odnaleźć pliku dockerfile w repozytorium.](media/deployment-center-launcher/dockerfile.png)

1. Wybierz istniejący rejestr kontenerów lub utwórz go, a następnie wybierz pozycję **Zakończ**. Potok jest tworzony automatycznie i kolejkuje kompilację w [Azure Pipelines](/azure/devops/pipelines/index).

    Azure Pipelines to usługa w chmurze, która umożliwia automatyczne Kompilowanie i testowanie projektu kodu i udostępnianie go innym użytkownikom. Azure Pipelines łączy ciągłą integrację i ciągłe dostarczanie, aby stale i spójnie testować i kompilować kod oraz dostarczać go do dowolnego celu.

    ![Container Registry](media/deployment-center-launcher/container-registry.png)

1. Wybierz łącze, aby wyświetlić bieżący potok.

1. Po zakończeniu wdrażania zostaną wyświetlone pomyślne dzienniki.

    ![Zrzut ekranu przedstawia centrum wdrażania z wydaniami 1, które jest oznaczone zielonym znacznikiem wyboru.](media/deployment-center-launcher/logs.png)

## <a name="examine-the-ci-pipeline"></a>Badanie potoku ciągłej integracji

Centrum wdrażania automatycznie skonfiguruje potok ciągłej integracji/ciągłego wdrażania usługi Azure DevOps. Potok można eksplorować i dostosowywać.

1. Przejdź do pulpitu nawigacyjnego Centrum wdrażania.  

1. Wybierz numer kompilacji z listy pomyślnych dzienników, aby wyświetlić potok kompilacji dla projektu.

1. Wybierz wielokropek (...) w prawym górnym rogu. Menu zawiera kilka opcji, takich jak kolejkowanie nowej kompilacji, zachowywanie kompilacji i edytowanie potoku kompilacji. Wybierz pozycję **Edytuj potok**. 

1. W tym okienku można przeglądać różne zadania dla potoku kompilacji. Kompilacja wykonuje różne zadania, takie jak zbieranie źródeł z repozytorium git, tworzenie obrazu, wypychanie obrazu do rejestru kontenerów i publikowanie danych wyjściowych, które są używane do wdrożeń.

1. Wybierz nazwę potoku kompilacji u góry potoku.

1. Zmień nazwę potoku kompilacji na bardziej opisową, wybierz pozycję **zapisz & kolejki**, a następnie wybierz pozycję **Zapisz**.

1. W obszarze potoku kompilacji wybierz pozycję **historia**. To okienko pokazuje dziennik inspekcji ostatnich zmian kompilacji. Usługa Azure DevOps monitoruje wszelkie zmiany wprowadzone w potoku kompilacji i umożliwia porównanie wersji.

1. Wybierz pozycję **Wyzwalacze**. Można dołączać lub wykluczać gałęzie z procesu CI elementu konfiguracji.

1. Wybierz pozycję **Przechowywanie**. Możesz określić zasady, aby zachować lub usunąć liczbę kompilacji, w zależności od danego scenariusza.

## <a name="examine-the-cd-pipeline"></a>Badanie potoku ciągłego wdrażania

Centrum wdrażania automatycznie tworzy i konfiguruje relację między organizacją usługi Azure DevOps i subskrypcją platformy Azure. Te kroki obejmują skonfigurowanie połączenia usługi platformy Azure w celu uwierzytelnienia subskrypcji platformy Azure za pomocą usługi Azure DevOps. Zautomatyzowany proces tworzy również potok wydania, który zapewnia ciągłe dostarczanie na platformę Azure.

1. Wybierz pozycję **potoki**, a następnie wybierz pozycję **wersje**.

1. Aby edytować potok wersji, wybierz pozycję **Edytuj**.

1. Wybierz pozycję **Porzuć** z listy **artefaktów** . W poprzednich krokach sprawdzona potoku konstruowania generuje dane wyjściowe używane dla artefaktu. 

1. Wybierz wyzwalacz **ciągłego wdrażania** po prawej stronie opcji **Drop** . Ten potok wersji ma włączony wyzwalacz CD, który uruchamia wdrożenie przy każdym udostępnieniu nowego artefaktu kompilacji. Możesz również wyłączyć wyzwalacz, aby wymagać ręcznego wykonania dla wdrożeń.

1. Aby przejrzeć wszystkie zadania dla potoku, wybierz pozycję **zadania**. Wydanie ustawia środowisko przyrzeczer, konfiguruje `imagePullSecrets` parametr, instaluje narzędzia Helm i wdraża wykresy Helm w klastrze Kubernetes.

1. Aby wyświetlić historię wersji, wybierz pozycję **Wyświetl wersje**.

1. Aby wyświetlić podsumowanie, wybierz pozycję **Zwolnij**. Wybierz dowolny etap, aby eksplorować wiele menu, takich jak podsumowanie wydania, skojarzone elementy robocze i testy. 

1. Wybierz pozycję **Zatwierdzenia**. Ten widok przedstawia zatwierdzenia kodu powiązane z tym wdrożeniem. Porównaj wydania, aby zobaczyć różnice zatwierdzeń między wdrożeniami.

1. Wybierz pozycję **dzienniki**. Dzienniki zawierają przydatne informacje dotyczące wdrażania, które można wyświetlić w trakcie wdrażania i po nim.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Możesz usunąć powiązane zasoby, które zostały utworzone, gdy nie są już potrzebne. Użyj funkcji usuwania na pulpicie nawigacyjnym DevOps Projects.

## <a name="next-steps"></a>Następne kroki

Możesz zmodyfikować potoki kompilacji i wydania, aby dopasować je do potrzeb swojego zespołu. Możesz też użyć tego modelu CI/CD jako szablonu dla innych potoków.
