---
title: 'Samouczek: Tworzenie potoku ciągłej integracji/ciągłego wdrażania dla istniejącego kodu przy użyciu usługi Azure DevOps Starter'
description: Usługa Azure DevOps Starter ułatwia rozpoczęcie pracy z platformą Azure. W kilku prostych krokach DevOps Projects pomóc Ci w użyciu własnego kodu i repozytorium GitHub do uruchamiania aplikacji w usłudze platformy Azure.
services: vsts
documentationcenter: vs-devops-build
ms.author: mlearned
ms.manager: gwallace
ms.workload: web
ms.prod: devops
ms.technology: devops-cicd
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.custom: mvc
ms.openlocfilehash: c03ba657ac264b72d035f28956354398421ed2f9
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102566604"
---
# <a name="create-a-cicd-pipeline-for-github-repo-using-azure-devops-starter"></a>Tworzenie potoku ciągłej integracji/ciągłego wdrażania za pomocą usługi Azure DevOps Starter

Usługa Azure DevOps Starter przedstawia uproszczony proces tworzenia potoku ciągłej integracji i ciągłego dostarczania na platformę Azure. Możesz przenieść istniejący kod i repozytorium Git lub wybrać przykładową aplikację.

Wykonasz następujące zadania:

> [!div class="checklist"]
> * Użyj DevOps Starter, aby utworzyć potok ciągłej integracji/ciągłego wdrażania
> * Konfigurowanie dostępu do repozytorium GitHub i wybieranie platformy
> * Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure 
> * Zatwierdzanie zmian w usłudze GitHub i automatyczne wdrażanie ich na platformie Azure
> * Badanie potoku ciągłej integracji/ciągłego wdrażania usługi Azure Pipelines
> * Czyszczenie zasobów

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Możesz uzyskać ją bezpłatnie za pośrednictwem programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).
* Dostęp do usługi GitHub lub zewnętrznego repozytorium git, które zawiera .NET, Java, PHP, Node.js, Python lub static Web Code.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Usługa Azure DevOps Starter tworzy potok ciągłej integracji/ciągłego wdrażania w Azure Pipelines. Możesz utworzyć nową organizację usługi Azure DevOps lub użyć istniejącej organizacji. Usługa Azure DevOps Starter tworzy również zasoby platformy Azure w wybranej subskrypcji platformy Azure.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W polu wyszukiwania wpisz **DevOps Starter**, a następnie wybierz opcję. Kliknij przycisk **Dodaj** , aby utworzyć nowy.

    ![Pulpit nawigacyjny DevOps Starter](_img/azure-devops-starter-aks/search-devops-starter.png)
    
1. Wybierz pozycję **Dodaj własny kod**, a następnie wybierz pozycję **Dalej**.

## <a name="configure-access-to-your-github-repo-and-select-a-framework"></a>Konfigurowanie dostępu do repozytorium GitHub i wybieranie struktury

1. Wybierz pozycję **GitHub** lub zewnętrzne repozytorium kodu **git** . Na potrzeby tego samouczka wybierz pozycję **GitHub**. Może być wymagane uwierzytelnienie za pomocą usługi GitHub po raz pierwszy, aby umożliwić systemowi Azure dostęp do repozytorium GitHub.

1. Wybierz **repozytorium** i **gałąź**, a następnie wybierz przycisk **dalej**.

1. Jeśli używasz kontenerów platformy Docker, Zmień **wartość na Dockerized aplikacji** na **tak**. Na potrzeby tego samouczka **nie** zaznaczaj żadnych opcji, a następnie wybierz przycisk **dalej**. Aby uzyskać więcej informacji o używaniu kontenerów platformy Docker, umieść kursor nad ikoną **i** .

   ![Wybór struktury aplikacji w menu rozwijanym](_img/azure-devops-project-github/appframework.png)

1. Z menu rozwijanego wybierz **środowisko uruchomieniowe aplikacji** i **strukturę aplikacji**, a następnie wybierz przycisk **dalej**. Platforma aplikacji określa typ dostępnego miejsca docelowego wdrożenia usługi platformy Azure.

1. Wybierz **usługę platformy Azure** , aby wdrożyć aplikację, a następnie wybierz pozycję **dalej**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure

1. Wprowadź nazwę dla **nazwy projektu**.

1. Utwórz nową bezpłatną organizację w **usłudze Azure DevOps Organization** lub wybierz istniejącą organizację z menu rozwijanego.

1. Wybierz swoją subskrypcję w **subskrypcji platformy Azure**, a następnie wprowadź nazwę w **aplikacji sieci Web** lub użyj wartości domyślnej. Wybierz **lokalizację**, a następnie wybierz pozycję **gotowe**. Po kilku minutach w Azure Portal zostanie wyświetlona wartość przegląd wdrożenia DevOps Starter.

1. Wybierz pozycję **Przejdź do zasobu** , aby wyświetlić pulpit nawigacyjny DevOps Starter. W prawym górnym rogu Przypnij **projekt** do pulpitu nawigacyjnego, aby uzyskać szybki dostęp. Usługa Azure DevOps Starter automatycznie konfiguruje wyzwalacz tworzenia i zwalniania elementu konfiguracji. Twój kod pozostaje w repozytorium GitHub lub innym repozytorium zewnętrznym, a Przykładowa aplikacja jest skonfigurowana w repozytorium w **organizacji usługi Azure DevOps**. Usługa Azure DevOps Starter uruchamia kompilację i wdraża aplikację na platformie Azure.

   ![Widok pulpitu nawigacyjnego Azure DevOps Projects](_img/azure-devops-project-github/projectsdashboard.png)

1. Na pulpicie nawigacyjnym zostanie wyświetlone repozytorium kodu, potok ciągłej integracji/ciągłego wdrażania oraz Twoja aplikacja na platformie Azure. Po prawej stronie w obszarze zasoby platformy Azure wybierz pozycję **Przeglądaj** , aby wyświetlić uruchomioną aplikację.

## <a name="commit-changes-to-github-and-automatically-deploy-them-to-azure"></a>Zatwierdzanie zmian w usłudze GitHub i automatyczne wdrażanie ich na platformie Azure

Teraz możesz przystąpić do współpracy nad swoją aplikacją z zespołem. Proces ciągłej integracji/ciągłego wdrażania automatycznie wdraża najnowszą swoją służbę w witrynie sieci Web. Każda zmiana repozytorium GitHub uruchamia kompilację w usłudze Azure DevOps, a potok CD uruchamia wdrożenie na platformie Azure.

1. Na pulpicie nawigacyjnym DevOps wybierz pozycję **repozytoria**. Repozytorium GitHub zostanie otwarte na nowej karcie przeglądarki. Wprowadź zmiany w aplikacji, a następnie wybierz pozycję **Zatwierdź zmiany**.

1. Po kilku chwilach rozpocznie się kompilacja w usłudze Azure Pipelines. Stan kompilacji można monitorować na pulpicie nawigacyjnym DevOps Starter. Możesz również monitorować ją w organizacji usługi Azure DevOps, wybierając kartę **kompilacja potoków** na pulpicie nawigacyjnym DevOps Starter.

## <a name="examine-the-azure-pipelines-cicd-pipeline"></a>Badanie potoku ciągłej integracji/ciągłego wdrażania usługi Azure Pipelines

Usługa Azure DevOps Starter automatycznie konfiguruje potok ciągłej integracji/ciągłego wdrażania w Azure Pipelines. Możesz przeglądać i dostosowywać potok według potrzeb. Aby zapoznać się z potokami kompilacji i wydania, wykonaj następujące czynności:

1. Na pulpicie nawigacyjnym DevOps wybierz pozycję **potoki kompilacji**.

1. Po otwarciu strony **Azure Pipelines** zobaczysz historię najnowszych kompilacji i stan dla każdej kompilacji.

   ![Strona kompilacje Azure Pipelines](_img/azure-devops-project-github/pipelinesbuildpage.png)

1. W prawym górnym rogu strony **kompilacje** możesz wybrać pozycję **Edytuj** , aby zmienić bieżącą kompilację, **kolejkę** , dodać nową kompilację, lub przycisk wielokropka pionowy (**&#8942;**), aby otworzyć menu z więcej opcji. Wybierz pozycję **Edytuj**.

1. Kompilacja wykonuje różne zadania, takie jak pobieranie źródeł z repozytorium, przywracanie zależności i publikowanie danych wyjściowych dla wdrożeń. Po prawej stronie w polu **Nazwa** Zmień nazwę potoku kompilacji na coś bardziej opisowego. Wybierz pozycję **zapisz & kolejkę**, a następnie wybierz pozycję **Zapisz**. Wprowadź komentarz, a następnie wybierz pozycję **Zapisz** ponownie.

   ![Strona kompilacji usługi Azure DevOps](_img/azure-devops-project-github/buildpage.png)

1. Aby wyświetlić dziennik inspekcji ostatnich zmian dla kompilacji, wybierz kartę **historia** .  Usługa Azure DevOps śledzi wszelkie zmiany wprowadzone w potoku kompilacji i umożliwia porównanie wersji.

1. Wybierz kartę **wyzwalacze** . Azure DevOps projects automatycznie tworzy wyzwalacz elementu konfiguracji z niektórymi ustawieniami domyślnymi. Można ustawić wyzwalacze, takie jak **włączenie ciągłej integracji** , aby uruchamiać kompilację za każdym razem, gdy zatwierdzisz zmianę kodu. Możesz również ustawić wyzwalacze, aby zaplanować uruchamianie kompilacji o określonych godzinach.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy Azure App Service i powiązane zasoby, które zostały utworzone w tym samouczku, nie są już potrzebne, można je usunąć. Użyj funkcji **Usuń** na pulpicie nawigacyjnym usługi DevOps Projects.

## <a name="next-steps"></a>Następne kroki

Po skonfigurowaniu procesu ciągłej integracji/ciągłego wdrażania w ramach tego samouczka automatycznie utworzysz potok kompilacja i wydanie w Azure DevOps Projects. Możesz zmodyfikować potoki kompilacji i wydania, aby dopasować je do potrzeb swojego zespołu.

Aby dowiedzieć się więcej na temat potoku ciągłej integracji/ciągłego wdrażania, zobacz:

> [!div class="nextstepaction"]
> [Definiowanie wieloetapowego potoku ciągłego wdrażania](/azure/devops/pipelines/release/define-multistage-release-process)

Aby dowiedzieć się więcej o monitorowaniu aplikacji, zobacz:
  
 > [!div class="nextstepaction"]
 > [Co to jest usługa Azure monitor?](../azure-monitor/overview.md)