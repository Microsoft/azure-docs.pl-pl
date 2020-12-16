---
title: 'Szybki Start: tworzenie przepływu pracy CI/CD dla Node.js-DevOps Starter dla usługi GitHub w celu wdrożenia na platformie Azure'
description: DevOps Starter ułatwia rozpoczęcie pracy z platformą Azure przy użyciu akcji usługi GitHub.
author: ninallam
editor: ninallam
ms.assetid: ''
ms.devlang: na
ms.service: az-devops-project
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 08/24/2020
ms.author: ninallam
ms.openlocfilehash: e3c72ce6a15f90f0dbe08bbff10db0ca5f6b5c8f
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97588815"
---
# <a name="set-up-cicd-for-a-nodejs-app-with-devops-starter-using-github-actions"></a>Konfigurowanie ciągłej integracji/ciągłego wdrażania dla aplikacji Node.js przy użyciu akcji usługi GitHub DevOps Starter

W tym przewodniku szybki start użyjesz uproszczonego środowiska DevOps Starter, aby skonfigurować przepływ pracy ciągłej integracji i ciągłego dostarczania dla aplikacji Node.js przy użyciu akcji usługi GitHub. Możesz użyć DevOps Starter, aby skonfigurować wszystko, czego potrzebujesz do tworzenia, wdrażania i monitorowania aplikacji. 

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- Konto usługi [GitHub](https://github.com/).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

DevOps Starter tworzy przepływ pracy ciągłej integracji/ciągłego wdrażania przy użyciu akcji usługi GitHub. DevOps Starter tworzy również zasoby platformy Azure w wybranej subskrypcji platformy Azure.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W polu wyszukiwania wpisz **DevOps Starter**, a następnie wybierz opcję. Kliknij przycisk **Dodaj** , aby utworzyć nowy.

    ![Pulpit nawigacyjny DevOps Starter](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-application-and-azure-service"></a>Wybieranie przykładowej aplikacji i usługi platformy Azure

1. Kliknij pozycję **Konfiguruj DevOps Starter przy użyciu usługi GitHub** na banerze z prawej strony.

    ![landing_page](_img/azure-devops-project-nodejs/landing-page.png)

1. Upewnij się, że dostawca CI/CD został wybrany jako **Akcje usługi GitHub**.

    ![CICD_provider](_img/azure-devops-project-nodejs/provider-selection.png)

1. Wybierz **Node.js** przykładową aplikację. Przykłady środowiska Node.js obejmują kilka struktur aplikacji do wyboru.

1. Domyślną strukturą przykładu jest **Express.js**. Pozostaw ustawienie domyślne, a następnie wybierz pozycję **Dalej**.   

2. Aplikacja sieci Web systemu Windows jest domyślnym miejscem docelowym wdrożenia. Wybrana poprzednio struktura aplikacji decyduje o dostępnym w tym miejscu typie celu wdrożenia usługi platformy Azure. Pozostaw usługę domyślną, a następnie wybierz pozycję **Dalej**.
 
## <a name="configure-github-account-and-an-azure-subscription"></a>Konfigurowanie konta GitHub i subskrypcji platformy Azure 

1. Uwierzytelnianie za pomocą usługi GitHub.

   1. Kliknij przycisk **Autoryzuj** . 
   
   1. Zaloguj się do usługi GitHub. Jeśli nie masz konta w usłudze GitHub, możesz również zarejestrować się tutaj.

2. Wybierz istniejącą **organizację usługi GitHub**. 
   
   1. Wybierz nazwę repozytorium GitHub. 
   
   1. Wybierz lokalizację i subskrypcję platformy Azure, wybierz nazwę aplikacji, a następnie wybierz przycisk **Gotowe**.
    
       ![Enter_GH_details](_img/azure-devops-project-nodejs/gh-details.png)


    Po kilku minutach DevOps początkowy pulpit nawigacyjny zostanie wyświetlony w Azure Portal. Przykładowa aplikacja jest skonfigurowana w repozytorium w organizacji usługi Azure DevOps, zostanie wyzwolony przepływ pracy w usłudze GitHub, a aplikacja zostanie wdrożona na platformie Azure. Ten pulpit nawigacyjny zapewnia wgląd w repozytorium kodu, przepływ pracy w usłudze GitHub i aplikację na platformie Azure.
   
3. Wybierz pozycję **Przeglądaj**, aby wyświetlić uruchomioną aplikację.
    
    Pulpit nawigacyjny zawiera szczegółowe informacje o przepływie pracy w usłudze GitHub i zasobach platformy Azure. Aby wyświetlić szczegółowe informacje o przepływie pracy usługi GitHub, takie jak Najnowsze uruchomienia, zatwierdzenia i stan zadania, należy **autoryzować do serwisu GitHub**.
   
   ![Authorize_dashboard](_img/azure-devops-project-nodejs/authenticate-dashboard.png)

DevOps Starter automatycznie skonfigurował przepływ pracy usługi GitHub z zadaniami kompilowania i wdrażania przy użyciu akcji usługi GitHub. Możesz teraz rozpocząć pracę w zespole nad aplikacją Node.js w ramach procesu ciągłej integracji/ciągłego wdrażania, który umożliwia automatyczne wdrożenie najnowszej wersji w witrynie internetowej.

   ![dashooard_view](_img/azure-devops-project-nodejs/full-dashboard.png)

## <a name="commit-code-changes-and-execute-cicd"></a>Zatwierdzanie zmian kodu i wykonywanie ciągłej integracji/ciągłego wdrażania

DevOps Starter tworzy repozytorium w serwisie GitHub. Aby wyświetlić repozytorium i wprowadzić zmiany w kodzie aplikacji, wykonaj poniższe kroki:

1. Po lewej stronie pulpitu nawigacyjnego DevOps Starter wybierz link do gałęzi głównej. To łącze powoduje otwarcie widoku nowo utworzonego repozytorium GitHub.

1. Aby wyświetlić adres URL klonowania repozytorium, wybierz pozycję **Klonuj** w prawym górnym rogu przeglądarki. Możesz sklonować repozytorium Git w wybranym środowisku IDE. W następnych kilku krokach można użyć przeglądarki sieci Web, aby wprowadzić zmiany w kodzie i zatwierdzić je bezpośrednio w gałęzi głównej.

1. Po lewej stronie przeglądarki przejdź do pliku **/Application/views/index.Pug** .

1. Wybierz pozycję **Edytuj** i wprowadź zmianę w tekście.
    Na przykład zmień fragment tekstu dla jednego z tagów.

1. Wybierz pozycję **Zatwierdź**, a następnie zapisz zmiany.

1. W przeglądarce przejdź do pulpitu nawigacyjnego DevOps Starter.   
Powinno zostać wyświetlone zadanie kompilacji przepływu pracy w usłudze GitHub w toku. Wprowadzone zmiany są automatycznie kompilowane i wdrażane za pośrednictwem przepływu pracy usługi GitHub.

## <a name="view-the-github-workflow"></a>Wyświetlanie przepływu pracy w usłudze GitHub

W poprzednim kroku DevOps Starter automatycznie skonfigurował pełny przepływ pracy w usłudze GitHub. Przejrzyj i dostosuj przepływ pracy zgodnie z wymaganiami. Wykonaj następujące kroki, aby zaznajomić się z przepływem pracy.

1. Po lewej stronie pulpitu nawigacyjnego DevOps Starter wybierz pozycję **przepływ pracy usługi GitHub**. To łącze powoduje otwarcie karty przeglądarki i przepływu pracy usługi GitHub dla nowego projektu.
    > [!NOTE]
    > Nie zmieniaj nazwy pliku przepływu pracy. Nazwa pliku przepływu pracy powinna mieć wartość **DevOps-Starter-Workflow. yml** dla pulpitu nawigacyjnego, aby odzwierciedlić zmiany

1. Plik YAML przepływu pracy zawiera wszystkie akcje usługi GitHub wymagane do kompilowania i wdrażania aplikacji. Kliknij opcję **Edytuj plik** , aby dostosować plik przepływu pracy.

1. Na karcie **kod** repozytorium kliknij pozycję **Zatwierdź**. Ten widok przedstawia zatwierdzenia kodu skojarzone z konkretnym wdrożeniem.

1. Na karcie **Akcje** repozytorium można wyświetlić historię wszystkich przebiegów przepływu pracy repozytorium.

1. Wybierz **najnowszy przebieg** , aby wyświetlić wszystkie zadania uruchomione w przepływie pracy.

1. Kliknij **zadania** , aby wyświetlić szczegółowe dzienniki przebiegu przepływu pracy. Dzienniki zawierają przydatne informacje na temat procesu wdrażania. Mogą być wyświetlane zarówno podczas wdrożeń, jak i po nich.

1. Kliknij kartę **żądanie ściągnięcia** , aby wyświetlić wszystkie żądania ściągnięcia w repozytorium

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy usługa Azure App Service i inne powiązane zasoby nie będą już potrzebne, możesz je usunąć. Użyj funkcji **usuwania** na pulpicie nawigacyjnym DevOps Starter.

## <a name="next-steps"></a>Następne kroki

W przypadku skonfigurowania procesu ciągłej integracji/ciągłego tworzenia przepływu pracy usługi GitHub został on automatycznie utworzony. Możesz zmodyfikować ten przepływ pracy, aby zaspokoić potrzeby zespołu. Aby dowiedzieć się więcej o akcjach i przepływie pracy usługi GitHub, zobacz:

> [!div class="nextstepaction"]
> [Dostosowywanie przepływu pracy w usłudze GitHub](https://docs.github.com/actions/configuring-and-managing-workflows/configuring-and-managing-workflow-files-and-runs)
