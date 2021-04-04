---
title: 'Samouczek: wdrażanie aplikacji Node.js w usłudze Azure Web App przy użyciu DevOps Starter dla akcji GitHub'
description: DevOps Starter ułatwia rozpoczęcie pracy z platformą Azure i wdrażanie aplikacji Node.js w usłudze Azure Web App w kilku prostych krokach.
ms.author: ninallam
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 08/25/2020
author: ninallam
ms.openlocfilehash: 4caac943df33177728997c74c9ec812105efd62c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97588781"
---
# <a name="tutorial-deploy-nodejs-app-to-azure-web-app-using-devops-starter-for-github-actions"></a>Samouczek: wdrażanie aplikacji Node.js w usłudze Azure Web App przy użyciu DevOps Starter dla akcji GitHub

DevOps Starter for GitHub oferuje uproszczone środowisko, w którym można wybrać przykładową aplikację do tworzenia przepływu pracy ciągłej integracji i ciągłego dostarczania (CD) w celu wdrożenia na platformie Azure. 

DevOps Starter również:
* Program automatycznie tworzy zasoby platformy Azure, takie jak nowa aplikacja internetowa platformy Azure.
* Tworzy i konfiguruje przepływ pracy w usłudze GitHub, który obejmuje zadanie kompilacji dla elementu konfiguracji.
* Przepływ pracy zawiera również zadanie wdrażania dla dysku CD. 
* Tworzy zasób usługi Azure Application Insights na potrzeby monitorowania.

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Wdrażanie aplikacji Node.js przy użyciu programu DevOps Starter
> * Konfigurowanie usługi GitHub i subskrypcji platformy Azure 
> * Badanie przepływu pracy w usłudze GitHub
> * Zatwierdzanie zmian w usłudze GitHub i automatyczne wdrażanie ich na platformie Azure
> * Konfigurowanie monitorowania usługi Azure Application Insights
> * Czyszczenie zasobów

## <a name="prerequisites"></a>Wymagania wstępne

* Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/).

## <a name="use-devops-starter-to-deploy-a-nodejs-app"></a>Wdrażanie aplikacji Node.js przy użyciu programu DevOps Starter

DevOps Starter tworzy przepływ pracy w usłudze GitHub. Możesz użyć istniejącej organizacji usługi GitHub. DevOps Starter tworzy również zasoby platformy Azure, takie jak aplikacja sieci Web w ramach wybranej subskrypcji platformy Azure.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W polu wyszukiwania wpisz **DevOps Starter**, a następnie wybierz opcję. Kliknij przycisk **Dodaj** , aby utworzyć nowy.

    ![Pulpit nawigacyjny DevOps Starter](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Upewnij się, że dostawca CI/CD został wybrany jako **Akcje usługi GitHub**.

    ![CICD_provider](_img/azure-devops-project-nodejs/provider-selection.png)

1. Wybierz pozycję **Node.js**, a następnie wybierz pozycję **dalej**.

1. W obszarze **Wybierz platformę aplikacji** wybierz pozycję **Express.js**, a następnie wybierz pozycję **dalej**. Wybrana w poprzednim kroku struktura aplikacji decyduje o dostępnym w tym miejscu typie celu wdrożenia usługi platformy Azure. 

1. Wybierz **aplikację sieci Web systemu Windows**, a następnie wybierz przycisk **dalej**.

## <a name="configure-github-and-an-azure-subscription"></a>Konfigurowanie usługi GitHub i subskrypcji platformy Azure

1. **Autoryzuj** GitHub i wybierz istniejącą organizację usługi GitHub. 

1. Wprowadź nazwę **repozytorium GitHub**. 

1. Wybierz usługi subskrypcji platformy Azure. Opcjonalnie możesz wybrać pozycję **Zmień**, a następnie wprowadzić więcej szczegółów konfiguracji, takich jak lokalizacja zasobów platformy Azure.
 
1. Wprowadź nazwę aplikacji sieci Web, a następnie wybierz pozycję **gotowe**. Po kilku minutach aplikacja internetowa platformy Azure będzie gotowa. Przykładowa aplikacja Node.js jest skonfigurowana w repozytorium w organizacji GitHub, zostanie wyzwolony przepływ pracy, a aplikacja zostanie wdrożona w nowo utworzonej aplikacji sieci Web platformy Azure.

   ![Enter_GH_details](_img/azure-devops-project-nodejs/gh-details.png)    

   Po zakończeniu DevOps początkowy pulpit nawigacyjny zostanie wyświetlony w Azure Portal. Możesz również przejść do pulpitu nawigacyjnego bezpośrednio z obszaru **Wszystkie zasoby** w witrynie Azure Portal. 

   Pulpit nawigacyjny zapewnia wgląd w repozytorium kodu GitHub, przepływ pracy CI/CD i działającą aplikację na platformie Azure.   

   ![Widok pulpitu nawigacyjnego](_img/azure-devops-project-nodejs/full-dashboard.png)

DevOps Starter automatycznie konfiguruje wyzwalacz, który wdraża zmiany kodu w repozytorium.
    
## <a name="examine-the-github-workflow"></a>Badanie przepływu pracy w usłudze GitHub

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

1. Po zakończeniu wdrażania Odśwież aplikację, aby zweryfikować zmiany.

## <a name="configure-azure-application-insights-monitoring"></a>Konfigurowanie monitorowania usługi Azure Application Insights

Usługa Azure Application Insights umożliwia łatwe monitorowanie wydajności i użycia aplikacji. DevOps Starter automatycznie konfiguruje zasób Application Insights dla aplikacji. Możesz dodatkowo skonfigurować różne alerty i możliwości monitorowania stosownie do potrzeb.

1. W Azure Portal przejdź do pulpitu nawigacyjnego DevOps Starter. 

1. W prawym dolnym rogu wybierz link **Application Insights** dla Twojej aplikacji. Zostanie otwarte okienko **Application Insights**. Ten widok zawiera informacje o monitorowaniu użycia, wydajności i dostępności aplikacji.

   ![Okienko Application Insights](_img/azure-devops-project-github/appinsights.png) 

1. Wybierz pozycję **Zakres czasu**, a następnie wybierz wartość **Ostatnia godzina**. Aby filtrować wyniki, wybierz pozycję **Aktualizuj**. Teraz możesz przejrzeć wszystkie działania z ostatnich 60 minut. 
    
1. Aby zamknąć zakres czasu, wybierz pozycję **x**

1. Wybierz pozycję **Alerty**, a następnie wybierz pozycję **Dodaj alert metryki**. 

1. Wprowadź nazwę alertu.

1. Na liście rozwijanej **Metryka** zapoznaj się z różnymi metrykami alertów. Domyślny alert dotyczy **czasu odpowiedzi serwera dłuższego niż 1 sekunda**. Możesz łatwo skonfigurować różne alerty, aby zwiększyć możliwości monitorowania aplikacji.

1. Zaznacz pole wyboru **Powiadom pocztą e-mail właścicieli, współautorów i czytelników**. Opcjonalnie możesz wykonać dodatkowe akcje po wyświetleniu alertu, wykonując aplikację logiki platformy Azure.

1. Wybierz pozycję **OK**, aby utworzyć alert. Po chwili alert będzie widoczny jako aktywny na pulpicie nawigacyjnym. 

1. Zamknij obszar **Alerty** i przejdź z powrotem do okienka **Application Insights**.

1. Wybierz pozycję **Dostępność**, a następnie wybierz pozycję **Dodaj test**. 

1. Wprowadź nazwę testu, a następnie wybierz pozycję **Utwórz**. Zostanie utworzony prosty test ping w celu sprawdzania dostępności aplikacji. Po kilku minutach wyniki testów będą dostępne, a na pulpicie nawigacyjnym usługi Application Insights zostanie wyświetlony stan dostępności.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli przeprowadzasz testowanie, możesz uniknąć naliczania opłat, oczyszczając zasoby. Gdy maszyna wirtualna platformy Azure i powiązane zasoby utworzone w tym samouczku nie będą już potrzebne, możesz je usunąć. Aby to zrobić, użyj funkcji **usuwania** na pulpicie nawigacyjnym DevOps Starter. 

> [!IMPORTANT]
> Poniższa procedura powoduje trwałe usunięcie zasobów. Funkcja *usuwania* niszczy dane, które są tworzone przez projekt w DevOps Starter na platformie Azure, i nie będzie można go pobrać. Użyj tej procedury dopiero po uważnym przeczytaniu monitów.

1. W Azure Portal przejdź do pulpitu nawigacyjnego DevOps Starter.
1. W prawym górnym rogu wybierz pozycję **Usuń**. 
1. Po wyświetleniu monitu wybierz pozycję **Tak**, aby *trwale usunąć* zasoby.

Opcjonalnie możesz zmodyfikować przepływ pracy, aby zaspokoić potrzeby zespołu. Tego wzorca CI/CD można także użyć jako szablonu dla innych repozytoriów. 

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wdrażanie aplikacji Node.js przy użyciu programu DevOps Starter
> * Konfigurowanie usługi GitHub i subskrypcji platformy Azure 
> * Badanie przepływu pracy w usłudze GitHub
> * Zatwierdzanie zmian w usłudze GitHub i automatyczne wdrażanie ich na platformie Azure
> * Konfigurowanie monitorowania usługi Azure Application Insights
> * Czyszczenie zasobów

Aby dowiedzieć się więcej o akcjach i przepływach pracy usługi GitHub, zobacz:

> [!div class="nextstepaction"]
> [Dostosowywanie przepływu pracy w usłudze GitHub](https://docs.github.com/actions/configuring-and-managing-workflows/configuring-and-managing-workflow-files-and-runs)
