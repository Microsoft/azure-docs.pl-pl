---
title: 'Szybki start: tworzenie potoku ci/CD dla języka Python przy użyciu Azure DevOps Starter'
description: Usługa DevOps Starter ułatwia rozpoczynanie pracy z platformą Azure. Umożliwia uruchomienie aplikacji w wybranej usłudze platformy Azure w kilku prostych krokach.
services: vsts
documentationcenter: vs-devops-build
author: mlearned
ms.author: mlearned
manager: gwallace
ms.date: 03/24/2020
ms.prod: devops
ms.technology: devops-cicd
ms.topic: quickstart
ms.workload: web
ms.tgt_pltfrm: na
ms.custom:
- mvc
- devx-track-python
- mode-api
ms.openlocfilehash: d236eadd4c1c3bbda5a781d00de43e4ed3dc88b9
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533264"
---
# <a name="create-a-cicd-pipeline-for-python-with-azure-devops-starter"></a>Tworzenie potoku cicha/cd dla języka Python przy użyciu Azure DevOps Starter

W tym przewodniku Szybki start użyjemy uproszczonego środowiska Azure DevOps Starter do skonfigurowania potoku ciągłej integracji/ciągłego dostarczania dla aplikacji w języku Python w Azure Pipelines. Możesz użyć aplikacji Azure DevOps Starter, aby skonfigurować wszystko, czego potrzebujesz do tworzenia, wdrażania i monitorowania aplikacji. 

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- Konto [Azure DevOps](https://azure.microsoft.com/services/devops/) organizacji.

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

DevOps Starter tworzy potok ci/CD w Azure Pipelines. Możesz utworzyć nową organizację usługi Azure DevOps lub użyć istniejącej organizacji. Usługa DevOps Starter tworzy również zasoby platformy Azure w wybranej subskrypcji platformy Azure.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 

1. W polu wyszukiwania wpisz **DevOps Starter**, a następnie wybierz pozycję. Kliknij pozycję **Dodaj,** aby utworzyć nową.

    ![Pulpit nawigacyjny usługi DevOps Starter](_img/azure-devops-starter-aks/search-devops-starter.png) 

## <a name="select-a-sample-application-and-azure-service"></a>Wybieranie przykładowej aplikacji i usługi platformy Azure

1. Wybierz aplikację przykładową w języku Python. Przykłady w języku Python obejmują kilka struktur aplikacji do wyboru.

1. Domyślna struktura w przykładzie to Django. Pozostaw ustawienie domyślne, a następnie wybierz pozycję **Dalej**. Domyślnym celem wdrożenia jest funkcja Web App For Containers. Wybrana poprzednio struktura aplikacji decyduje o dostępnym w tym miejscu typie celu wdrożenia usługi platformy Azure. 

3. Pozostaw usługę domyślną, a następnie wybierz pozycję **Dalej**.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure 

1. Utwórz nową organizację usługi Azure DevOps lub wybierz istniejącą organizację. 

    1. Wprowadź nazwę projektu w usłudze Azure DevOps.  

    1. Wybierz swoją subskrypcję platformy Azure i lokalizację, wprowadź nazwę aplikacji, a następnie wybierz pozycję **Gotowe**.  
    
     Po kilku minutach pulpit nawigacyjny startowy zostanie wyświetlony w Azure Portal. Aplikacja przykładowa zostanie skonfigurowana w repozytorium w organizacji usługi Azure DevOps, skompilowana i wdrożona na platformie Azure. Ten pulpit nawigacyjny zapewnia wgląd w repozytorium kodu, potok ciągłej integracji/ciągłego wdrażania i aplikację na platformie Azure.  
    
2. Wybierz pozycję **Przeglądaj**, aby wyświetlić uruchomioną aplikację.

    ![Widok pulpitu nawigacyjnego](_img/azure-devops-project-python/dashboardnopreview.png) 
    
   Usługa DevOps Projects automatycznie konfiguruje wyzwalacz kompilacji i wydania ciągłej integracji. Teraz możesz rozpocząć współpracę z zespołem nad aplikacją w języku Python w ramach procesu ciągłej integracji/ciągłego wdrażania, który automatycznie wdraża najnowszy kod w witrynie internetowej.

## <a name="commit-code-changes-and-execute-cicd"></a>Zatwierdzanie zmian kodu i wykonywanie ciągłej integracji/ciągłego wdrażania

DevOps Starter tworzy repozytorium Git w Azure Repos lub GitHub. Aby wyświetlić repozytorium i wprowadzić zmiany w kodzie aplikacji, wykonaj poniższe kroki: 

1. Po lewej stronie pulpitu nawigacyjnego usługi DevOps Starter wybierz link dla gałęzi main. Ten link otwiera widok nowo utworzonego repozytorium Git.

1. Aby wyświetlić adres URL klonowania repozytorium, wybierz pozycję **Klonuj** w prawym górnym rogu przeglądarki. Możesz sklonować repozytorium Git w wybranym środowisku IDE. W następnych kilku krokach za pomocą przeglądarki internetowej można wprowadzać i zatwierdzać zmiany kodu bezpośrednio w gałęzi main.

1. Po lewej stronie wybierz plik **app/templates/app/index.html**.

1. Wybierz pozycję **Edytuj** i wprowadź zmianę w tekście. Na przykład zmień fragment tekstu dla jednego z tagów div.

1. Wybierz pozycję **Zatwierdź**, a następnie zapisz zmiany.

1. W przeglądarce przejdź do pulpitu nawigacyjnego usługi DevOps Starter. W tym momencie powinna być widoczna trwająca kompilacja. Wprowadzone zmiany są automatycznie kompilowane i wdrażane za pośrednictwem potoku ciągłej integracji/ciągłego wdrażania.

## <a name="examine-the-cicd-pipeline"></a>Badanie potoku ciągłej integracji/ciągłego wdrażania

W poprzednim kroku program DevOps Starter automatycznie skonfigurował pełny potok cicha/cd. Możesz przeglądać i dostosowywać potok według potrzeb. Aby zapoznać się z potokami kompilacji i wydania, wykonaj następujące czynności:

1. W górnej części pulpitu nawigacyjnego usługi DevOps Starter wybierz pozycję **Build Pipelines (Potoki kompilacji).** Zostanie wyświetlona karta przeglądarki z potokiem kompilacji dla nowego projektu.

1. Wskaż pole **Stan,** a następnie wybierz **wielokropek** (...). W menu zostanie wyświetlonych kilka opcji, takich jak kolejkowanie nowej kompilacji, wstrzymanie kompilacji i edytowanie potoku kompilacji.

1. Wybierz pozycję **Edytuj**.

1. W tym okienku możesz zapoznać się z różnymi zadaniami w potoku kompilacji. W ramach kompilacji są wykonywane różne zadania, takie jak pobieranie kodu źródłowego z repozytorium Git, przywracanie zależności i publikowanie danych wyjściowych na potrzeby wdrażania.

1. W górnej części potoku kompilacji wybierz jego nazwę.

1. Zmień nazwę potoku kompilacji na bardziej opisową, wybierz pozycję **Zapisz i dodaj do kolejki**, a następnie wybierz pozycję **Zapisz**.

1. W obszarze nazwy potoku kompilacji wybierz pozycję **Historia**. Zostanie wyświetlony dziennik inspekcji ostatnio wprowadzonych zmian w kompilacji. Usługa Azure DevOps śledzi wszelkie zmiany wprowadzane w potoku kompilacji i pozwala na porównywanie wersji.

1. Wybierz pozycję **Wyzwalacze**. DevOps Starter automatycznie tworzy wyzwalacz ci, a każde zatwierdzenie w repozytorium uruchamia nową kompilację. Opcjonalnie możesz zdecydować się dołączyć gałęzie do procesu ciągłej integracji lub je wykluczyć.

1. Wybierz pozycję **Przechowywanie**. W zależności od scenariusza możesz określić zasady przechowywania lub usuwania pewnej liczby kompilacji.

1. Wybierz pozycję **Kompilacja i wydanie**, a następnie wybierz pozycję **Wydania**.   
 Usługa DevOps Projects tworzy potok wydania w celu zarządzania wdrożeniami na platformie Azure.

1. Wybierz symbol wielokropka obok potoku wydania, a następnie wybierz pozycję **Edytuj**. Potok wydania definiuje proces tworzenia wydania.  
        
12. W obszarze **Artefakty** wybierz polecenie **Porzuć**. Potok kompilacji przedstawiony w poprzednich krokach generuje dane wyjściowe używane na potrzeby artefaktu. 

1. Obok ikony **Porzuć** wybierz pozycję **Wyzwalacz ciągłego wdrażania**. Potok wydania ma włączony wyzwalacz ciągłego wdrażania, który przeprowadza wdrożenie za każdym razem, gdy jest dostępny nowy artefakt kompilacji. Opcjonalnie możesz wyłączyć wyzwalacz. Wtedy wdrożenia będą wymagać ręcznego wykonania. 

1. Po lewej stronie wybierz pozycję **Zadania**. Zadania to działania wykonywane w procesie wdrażania. W tym przykładzie zostało utworzone zadanie w celu wdrożenia w usłudze Azure App Service.

1. Po prawej stronie wybierz pozycję **Wyświetl wydania**, aby wyświetlić historię wydań.  
        
1. Wybierz symbol wielokropka (...) obok jednego z wydań i wybierz pozycję **Otwórz**. W tym widoku jest kilka menu, z którymi możesz się zapoznać, na przykład podsumowanie wydań, skojarzone elementy robocze i testy.

1. Wybierz pozycję **Zatwierdzenia**. Ten widok przedstawia zatwierdzenia kodu skojarzone z konkretnym wdrożeniem. 

1. Wybierz pozycję **Dzienniki.** Dzienniki zawierają przydatne informacje na temat procesu wdrażania. Możesz je wyświetlać zarówno podczas wdrażania, jak i po jego zakończeniu.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy usługa Azure App Service i powiązane zasoby nie będą już potrzebne, możesz je usunąć. Użyj funkcji **Usuń na** pulpicie nawigacyjnym DevOps Starter.

## <a name="next-steps"></a>Następne kroki

Podczas konfigurowania procesu ciągłej integracji/ciągłego wdrażania zostały automatycznie utworzone potoki kompilacji i wydania. Możesz zmodyfikować potoki kompilacji i wydania, aby dopasować je do potrzeb swojego zespołu. Aby dowiedzieć się więcej na temat potoku ciągłej integracji/ciągłego wdrażania, zobacz:

> [!div class="nextstepaction"]
> [Dostosowywanie procesu ciągłego wdrażania](/azure/devops/pipelines/release/define-multistage-release-process)
