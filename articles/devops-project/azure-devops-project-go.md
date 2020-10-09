---
title: 'Szybki Start: Tworzenie potoku ciągłej integracji/ciągłego wdrażania za pomocą usługi Azure DevOps Starter'
description: DevOps Starter ułatwia rozpoczęcie pracy z platformą Azure. Umożliwia uruchomienie aplikacji języka programowania Go w usłudze platformy Azure w kilku prostych krokach.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: mlearned
manager: gwallace
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 07/09/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: ad62b59587f5ef7823e601cd8b6b526b75b8fd03
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91854617"
---
# <a name="create-a-cicd-pipeline-for-go-using-azure-devops-starter"></a>Tworzenie potoku ciągłej integracji/ciągłego wdrażania za pomocą usługi Azure DevOps Starter

Skonfiguruj ciągłą integrację (CI) i ciągłe dostarczanie (CD) dla aplikacji go przy użyciu usługi Azure DevOps Starter. DevOps Starter upraszcza konfigurację początkową kompilacji i potoku wersji platformy Azure DevOps.

Jeśli nie masz subskrypcji platformy Azure, możesz uzyskać ją bezpłatnie za pośrednictwem programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

DevOps Starter tworzy potok ciągłej integracji/ciągłego wdrażania w Azure Pipelines. Możesz utworzyć nową organizację usługi Azure DevOps lub użyć istniejącej organizacji. DevOps Starter tworzy również zasoby platformy Azure w wybranej subskrypcji platformy Azure.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W polu wyszukiwania wpisz **DevOps Starter**, a następnie wybierz opcję. Kliknij przycisk **Dodaj** , aby utworzyć nowy.

    ![Pulpit nawigacyjny DevOps Starter](_img/azure-devops-starter-aks/search-devops-starter.png)

## <a name="select-a-sample-app-and-azure-service"></a>Wybieranie przykładowej aplikacji i usługi platformy Azure

1. Wybierz przykładową aplikację języka **Go**, a następnie wybierz pozycję **Dalej**.  
    
1. Domyślną strukturą jest struktura **Prosta aplikacja języka Go**. Wybierz opcję **Dalej**.  Wybrana poprzednio struktura aplikacji decyduje o dostępnym typie celów wdrożenia usługi platformy Azure. 
    
1. Pozostaw domyślną usługę platformy Azure i wybierz pozycję **Dalej**.

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure 

1. Utwórz bezpłatnie nową organizację usługi Azure DevOps lub wybierz istniejącą organizację. 

1. Wprowadź nazwę projektu usługi Azure DevOps. 

1. Wybierz swoją subskrypcję platformy Azure i lokalizację, wprowadź nazwę aplikacji, a następnie wybierz pozycję **Gotowe**. Po kilku minutach DevOps początkowy pulpit nawigacyjny zostanie wyświetlony w Azure Portal. Aplikacja przykładowa zostanie skonfigurowana w repozytorium w organizacji usługi Azure DevOps, skompilowana i wdrożona na platformie Azure. 

    Pulpit nawigacyjny zapewnia wgląd do repozytorium kodu, potoku ciągłej integracji/ciągłego wdrażania i aplikacji na platformie Azure. Po prawej stronie wybierz pozycję **Przeglądaj**, aby wyświetlić uruchomioną aplikację.

    ![Widok pulpitu nawigacyjnego](_img/azure-devops-project-go/dashboardnopreview.png) 

## <a name="commit-your-code-changes-and-execute-the-cicd"></a>Zatwierdzanie zmian kodu i uruchamianie ciągłej integracji/ciągłego wdrażania

DevOps Starter tworzy repozytorium Git w Azure Repos lub GitHub. Aby wyświetlić repozytorium i wprowadzić zmiany kodu aplikacji, wykonaj następujące czynności:

1. Na DevOps Starter, po lewej stronie, wybierz link do gałęzi głównej. Link otwiera widok nowo utworzonego repozytorium Git.

1. Aby wyświetlić adres URL klonowania repozytorium, wybierz pozycję **Klonuj** w prawym górnym rogu. Możesz sklonować repozytorium Git w wybranym środowisku IDE. W kolejnych kilku krokach użyjesz przeglądarki internetowej, aby dokonać zmian w kodzie i zatwierdzić je bezpośrednio w gałęzi master.

1. Po lewej stronie przejdź do pliku *views/index.html*, a następnie wybierz pozycję **Edytuj**.

1. Wprowadź zmianę w pliku. Na przykład zmodyfikuj tekst w jednym z tagów div.

1. Wybierz pozycję **Zatwierdź**, a następnie zapisz zmiany.

1. W przeglądarce przejdź do pulpitu nawigacyjnego usługi DevOps Projects. Powinna trwać kompilacja. Wprowadzone zmiany są automatycznie kompilowane i wdrażane za pośrednictwem potoku ciągłej integracji/ciągłego wdrażania.

## <a name="examine-the-cicd-pipeline"></a>Badanie potoku ciągłej integracji/ciągłego wdrażania

DevOps Starter automatycznie konfiguruje pełny potok ciągłej integracji/ciągłego wdrażania w Azure Repos. Możesz przeglądać i dostosowywać potok według potrzeb. Aby zapoznać się z potokami kompilacji i wydania usługi Azure DevOps, wykonaj następujące czynności:

1. Przejdź do pulpitu nawigacyjnego DevOps Starter.

1. U góry wybierz pozycję **Potoki kompilacji**. Zostanie wyświetlona karta przeglądarki z potokiem kompilacji dla nowego projektu.

1. Wskaż pole **stan** , a następnie wybierz przycisk wielokropka (...). Menu wyświetla kilka opcji, takich jak kolejkowanie nowej kompilacji, wstrzymywanie kompilacji i edytowanie potoku kompilacji.

1. Kliknij pozycję **Edytuj**.

1. W tym okienku możesz zapoznać się z różnymi zadaniami w potoku kompilacji. W ramach kompilacji są wykonywane różne zadania, takie jak pobieranie kodu źródłowego z repozytorium Git, przywracanie zależności i publikowanie danych wyjściowych używanych na potrzeby wdrażania.

1. W górnej części potoku kompilacji wybierz jego nazwę.

1. Zmień nazwę potoku kompilacji na bardziej opisową, wybierz pozycję **Zapisz i dodaj do kolejki**, a następnie wybierz pozycję **Zapisz**.

1. W obszarze nazwy potoku kompilacji wybierz pozycję **Historia**. W tym okienku jest wyświetlany dziennik inspekcji ostatnio wprowadzonych zmian w kompilacji. Usługa Azure DevOps śledzi wszelkie zmiany wprowadzone do potoku kompilacji i pozwala na porównanie wersji.

1. Wybierz pozycję **Wyzwalacze**. DevOps Starter automatycznie tworzy wyzwalacz CI i każde zatwierdzenie repozytorium uruchamia nową kompilację. Opcjonalnie możesz zdecydować się dołączyć gałęzie do procesu ciągłej integracji lub wykluczyć je z niego.

1. Wybierz pozycję **Przechowywanie**. W zależności od scenariusza możesz określić zasady przechowywania lub usuwania pewnej liczby kompilacji.

1. Wybierz pozycję **Kompilacja i wydanie**, a następnie wybierz pozycję **Wydania**.  DevOps Starter tworzy potok wydania do zarządzania wdrożeniami na platformie Azure.

1. Wybierz symbol wielokropka (...) obok potoku wydania, a następnie wybierz pozycję **Edytuj**. Potok wydania zawiera *potok*, który definiuje proces tworzenia wydania.

1. W obszarze **Artefakty** wybierz polecenie **Porzuć**. Przedstawiony poprzednio potok kompilacji generuje dane wyjściowe używane na potrzeby artefaktu. 

1. Z prawej strony ikony **Porzuć** wybierz pozycję **Wyzwalacz ciągłego wdrażania**. Ten potok wydania ma włączony wyzwalacz ciągłego wdrażania, który przeprowadza wdrożenie za każdym razem, gdy jest dostępny nowy artefakt kompilacji. Opcjonalnie możesz wyłączyć wyzwalacz. Wtedy wdrożenia będą wymagać ręcznego wykonania. 

1. Po lewej stronie wybierz pozycję **Zadania**. Zadania to czynności wykonywane w procesie wdrażania. W tym przykładzie zostało utworzone zadanie w celu wdrożenia w usłudze Azure App Service.

1. Po prawej stronie wybierz pozycję **Wyświetl wydania**, aby wyświetlić historię wydań.

1. Wybierz symbol wielokropka (...) obok wydania, a następnie wybierz pozycję **Otwórz**. Dostępnych jest kilka menu, z którymi możesz się zapoznać, na przykład podsumowanie wydań, skojarzone elementy robocze i testy.

1. Wybierz pozycję **Zatwierdzenia**. Ten widok przedstawia zatwierdzenia kodu skojarzone z danym wdrożeniem. 

1. Wybierz pozycję **dzienniki**. Dzienniki zawierają przydatne informacje na temat procesu wdrażania. Możesz je wyświetlać zarówno podczas wdrażania, jak i po jego zakończeniu.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy wystąpienie usługi Azure App Service i powiązane zasoby utworzone w tym przewodniku Szybki start nie będą już potrzebne, możesz je usunąć. Aby to zrobić, użyj funkcji **usuwania** na pulpicie nawigacyjnym DevOps Starter.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat modyfikowania potoków kompilacji i wydania w celu dopasowania ich do potrzeb Twojego zespołu, zobacz:

> [!div class="nextstepaction"]
> [Definiowanie wieloetapowego potoku ciągłego wdrażania](/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)