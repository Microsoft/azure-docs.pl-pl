---
title: 'Szybki Start: Tworzenie potoku ciągłej integracji/ciągłego wdrażania dla środowiska Java — Azure DevOps Starter'
description: Dowiedz się, jak korzystać z uproszczonego środowiska Azure DevOps Starter, aby skonfigurować potok ciągłej integracji (CI) i ciągłe dostarczanie dla aplikacji Java w Azure Pipelines.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: gwallace
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: mlearned
ms.custom: mvc, seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: d76c4206ca39518c3a09d27ee470c05db108ae6d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102551885"
---
# <a name="set-up-a-cicd-pipeline-for-a-java-app-with-azure-devops-starter"></a>Konfigurowanie potoku ciągłej integracji/ciągłego wdrażania dla aplikacji Java za pomocą usługi Azure DevOps Starter

W tym przewodniku szybki start użyjesz uproszczonego środowiska Azure DevOps Starter, aby skonfigurować potok ciągłej integracji (CI) i ciągłe dostarczanie dla aplikacji Java w Azure Pipelines. Możesz użyć platformy Azure DevOps Starter, aby skonfigurować wszystko, czego potrzebujesz do tworzenia, wdrażania i monitorowania aplikacji. 

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- Konto i organizacja [usługi Azure DevOps](https://azure.microsoft.com/services/devops/) .

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

DevOps Starter tworzy potok ciągłej integracji/ciągłego wdrażania w Azure Pipelines. Możesz utworzyć nową organizację usługi Azure DevOps lub użyć istniejącej organizacji. DevOps Starter tworzy również zasoby platformy Azure w wybranej subskrypcji platformy Azure.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W polu wyszukiwania wpisz **DevOps Starter**, a następnie wybierz opcję. Kliknij przycisk **Dodaj** , aby utworzyć nowy.

    ![Pulpit nawigacyjny DevOps Starter](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-application-and-azure-service"></a>Wybieranie przykładowej aplikacji i usługi platformy Azure

1. Wybierz aplikację przykładową w języku Java. Przykłady w języku Java obejmują kilka struktur aplikacji do wyboru.

1. Domyślna struktura w przykładzie to Spring. Pozostaw ustawienie domyślne, a następnie wybierz pozycję **Dalej**.  Domyślnym celem wdrożenia jest funkcja Web App For Containers. Wybrana poprzednio struktura aplikacji decyduje o dostępnym w tym miejscu typie celu wdrożenia usługi platformy Azure. 

2. Pozostaw usługę domyślną, a następnie wybierz pozycję **Dalej**.
 
## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure 

1. Utwórz nową organizację usługi Azure DevOps lub wybierz istniejącą organizację. 
   
   1. Wybierz nazwę projektu. 
   
   1. Wybierz lokalizację i subskrypcję platformy Azure, wybierz nazwę aplikacji, a następnie wybierz przycisk **Gotowe**.  
   Po kilku minutach DevOps początkowy pulpit nawigacyjny zostanie wyświetlony w Azure Portal. Aplikacja przykładowa zostanie skonfigurowana w repozytorium w organizacji usługi Azure DevOps, skompilowana i wdrożona na platformie Azure. Ten pulpit nawigacyjny zapewnia wgląd w repozytorium kodu, potok ciągłej integracji/ciągłego wdrażania i aplikację na platformie Azure.
   
2. Wybierz pozycję **Przeglądaj**, aby wyświetlić uruchomioną aplikację.
   
   ![Wyświetlanie pulpitu nawigacyjnego aplikacji w Azure Portal](_img/azure-devops-project-java/azure-devops-application-dashboard.png) 

DevOps Starter automatycznie skonfigurował wyzwalacz tworzenia i zwalniania elementu konfiguracji.  Możesz teraz rozpocząć pracę w zespole nad aplikacją w języku Java w ramach procesu ciągłej integracji/ciągłego wdrażania, który umożliwia automatyczne wdrożenie najnowszej wersji w witrynie internetowej.

## <a name="commit-code-changes-and-execute-cicd"></a>Zatwierdzanie zmian kodu i wykonywanie ciągłej integracji/ciągłego wdrażania

DevOps Starter tworzy repozytorium Git w Azure Repos lub GitHub. Aby wyświetlić repozytorium i wprowadzić zmiany w kodzie aplikacji, wykonaj poniższe kroki:

1. Po lewej stronie pulpitu nawigacyjnego DevOps Starter wybierz link do gałęzi głównej. Ten link otwiera widok nowo utworzonego repozytorium Git.

1. Aby wyświetlić adres URL klonowania repozytorium, wybierz pozycję **Klonuj** w prawym górnym rogu przeglądarki. Możesz sklonować repozytorium Git w wybranym środowisku IDE. W następnych kilku krokach można użyć przeglądarki sieci Web, aby wprowadzić zmiany w kodzie i zatwierdzić je bezpośrednio w gałęzi głównej.

1. Po lewej stronie przeglądarki wybierz plik **src/main/webapp/index.html**.

1. Wybierz pozycję **Edytuj** i wprowadź zmianę w tekście.
    Na przykład zmień fragment tekstu dla jednego z tagów div.

1. Wybierz pozycję **Zatwierdź**, a następnie zapisz zmiany.

1. W przeglądarce przejdź do pulpitu nawigacyjnego DevOps Starter.   
W tym momencie powinna być widoczna trwająca kompilacja. Wprowadzone zmiany są automatycznie kompilowane i wdrażane za pośrednictwem potoku ciągłej integracji/ciągłego wdrażania.

## <a name="examine-the-cicd-pipeline"></a>Badanie potoku ciągłej integracji/ciągłego wdrażania

 W poprzednim kroku DevOps Starter automatycznie skonfigurował potok ciągłej integracji/ciągłego dostarczania. Możesz przeglądać i dostosowywać potok według potrzeb. Wykonaj poniższe kroki, aby zapoznać się z potokami kompilacji i wydania.

1. W górnej części pulpitu nawigacyjnego DevOps początkowy wybierz pozycję **potoki kompilacji**. Ten link otwiera kartę przeglądarki i potok kompilacji dla nowego projektu.

1. Wskaż pole **stan** , a następnie wybierz przycisk wielokropka (...). Ta akcja powoduje otwarcie menu, w którym można rozpocząć kilka działań, takich jak kolejkowanie nowej kompilacji, wstrzymywanie kompilacji i edytowanie potoku kompilacji.

1. Wybierz pozycję **Edytuj**.

1. W tym okienku możesz zapoznać się z różnymi zadaniami w potoku kompilacji. W ramach kompilacji są wykonywane różne zadania, takie jak pobieranie kodu źródłowego z repozytorium Git, przywracanie zależności i publikowanie danych wyjściowych używanych do wdrożenia.

1. W górnej części potoku kompilacji wybierz jego nazwę.

1. Zmień nazwę potoku kompilacji na bardziej opisową, wybierz pozycję **Zapisz i dodaj do kolejki**, a następnie wybierz pozycję **Zapisz**.

1. W obszarze nazwy potoku kompilacji wybierz pozycję **Historia**.   
W okienku **Historia** zostanie wyświetlony dziennik inspekcji zmian ostatnio wprowadzonych w kompilacji.  Usługa Azure Pipelines śledzi wszelkie zmiany wprowadzone do potoku kompilacji i pozwala na porównanie wersji.

1. Wybierz pozycję **Wyzwalacze**.  DevOps Starter automatycznie utworzył wyzwalacz CI i każde zatwierdzenie do repozytorium uruchamia nową kompilację.  Opcjonalnie możesz zdecydować się dołączyć gałęzie do procesu ciągłej integracji lub je wykluczyć.

1. Wybierz pozycję **Przechowywanie**. W zależności od scenariusza możesz określić zasady przechowywania lub usuwania pewnej liczby kompilacji.

1. Wybierz pozycję **Kompilacja i wydanie**, a następnie wybierz pozycję **Wydania**.  
 DevOps Starter tworzy potok wydania do zarządzania wdrożeniami na platformie Azure.

1. Po lewej stronie wybierz symbol wielokropka (...) obok potoku wydania, a następnie wybierz pozycję **Edytuj**. Potok wydania zawiera potok, który definiuje proces tworzenia wydania.  
    
12. W obszarze **Artefakty** wybierz polecenie **Porzuć**. Potok kompilacji przedstawiony w poprzednich krokach generuje dane wyjściowe używane na potrzeby artefaktu. 

1. Obok ikony **Porzuć** wybierz pozycję **Wyzwalacz ciągłego wdrażania**. Ten potok wydania ma włączony wyzwalacz ciągłego wdrażania, który przeprowadza wdrożenie za każdym razem, gdy jest dostępny nowy artefakt kompilacji. Opcjonalnie możesz wyłączyć wyzwalacz. Wtedy wdrożenia będą wymagać ręcznego wykonania. 

1. Po lewej stronie wybierz pozycję **Zadania**. Zadania to działania wykonywane w procesie wdrażania. W tym przykładzie zostało utworzone zadanie w celu wdrożenia w usłudze Azure App Service.

1. Po prawej stronie wybierz pozycję **Wyświetl wydania**. Ten widok przedstawia historię wersji.

1. Wybierz symbol wielokropka (...) obok jednego z wydań i wybierz pozycję **Otwórz**. Dostępnych jest kilka menu, z którymi możesz się zapoznać, na przykład podsumowanie wydań, skojarzone elementy robocze i testy.

1. Wybierz pozycję **Zatwierdzenia**. Ten widok przedstawia zatwierdzenia kodu skojarzone z konkretnym wdrożeniem. 

1. Wybierz pozycję **dzienniki**. Dzienniki zawierają przydatne informacje na temat procesu wdrażania. Mogą być wyświetlane zarówno podczas wdrożeń, jak i po nich.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy usługa Azure App Service i inne powiązane zasoby nie będą już potrzebne, możesz je usunąć. Użyj funkcji **usuwania** na pulpicie nawigacyjnym DevOps Starter.

## <a name="next-steps"></a>Następne kroki

Podczas konfigurowania procesu ciągłej integracji/ciągłego wdrażania zostały automatycznie utworzone potoki kompilacji i wydania. Możesz zmodyfikować potoki kompilacji i wydania, aby dopasować je do potrzeb swojego zespołu. Aby dowiedzieć się więcej na temat potoku ciągłej integracji/ciągłego wdrażania, zobacz:

> [!div class="nextstepaction"]
> [Dostosowywanie procesu ciągłego wdrażania](/azure/devops/pipelines/release/define-multistage-release-process)