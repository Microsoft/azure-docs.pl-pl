---
title: 'Samouczek: wdrażanie aplikacji ASP.NET Core na platformie Azure Service Fabric przy użyciu usługi Azure DevOps Starter'
description: Usługa Azure DevOps Starter ułatwia rozpoczęcie pracy z platformą Azure. Usługa DevOps Projects pozwala wdrożyć aplikację platformy ASP.NET Core w usłudze Azure Service Fabric w kilku prostych krokach.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.openlocfilehash: 078331c35c76a3cda97398b1a487933a3f0f5ab8
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102559311"
---
# <a name="tutorial-deploy-your-aspnet-core-app-to-azure-service-fabric-by-using-azure-devops-starter"></a>Samouczek: wdrażanie aplikacji ASP.NET Core na platformie Azure Service Fabric przy użyciu usługi Azure DevOps Starter

Usługa Azure DevOps Starter przedstawia uproszczone środowisko, w którym można przenieść istniejący kod i repozytorium Git lub wybrać przykładową aplikację w celu utworzenia potoku ciągłej integracji i ciągłego dostarczania na platformę Azure. 

DevOps Starter również:

* Automatycznie tworzy zasoby platformy Azure, takie jak Azure Service Fabric.
* Tworzy i konfiguruje potok wydania w usłudze Azure DevOps, który konfiguruje potok ciągłej integracji/ciągłego wdrażania.
* Tworzy zasób usługi Azure Application Insights na potrzeby monitorowania.

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Użyj DevOps Starter, aby utworzyć aplikację ASP.NET Core i wdrożyć ją w Service Fabric
> * Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure 
> * Badanie potoku ciągłej integracji
> * Badanie potoku ciągłego wdrażania
> * Zatwierdzanie zmian w usłudze Git i automatyczne wdrażanie na platformie Azure
> * Czyszczenie zasobów

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Możesz uzyskać ją bezpłatnie za pośrednictwem programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

## <a name="use-devops-starter-to-create-an-aspnet-core-app-and-deploy-it-to-service-fabric"></a>Użyj DevOps Starter, aby utworzyć aplikację ASP.NET Core i wdrożyć ją w Service Fabric

DevOps Starter tworzy potok ciągłej integracji/ciągłego wdrażania w Azure Pipelines. Możesz utworzyć nową organizację usługi Azure DevOps lub użyć istniejącej organizacji. DevOps Starter tworzy również zasoby platformy Azure, takie jak klaster Service Fabric, w wybranej subskrypcji platformy Azure.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W polu wyszukiwania wpisz **DevOps Starter**, a następnie wybierz opcję. Kliknij przycisk **Dodaj** , aby utworzyć nowy.

    ![Pulpit nawigacyjny DevOps Starter](_img/azure-devops-starter-aks/search-devops-starter.png) 

1. Wybierz pozycję **.NET**, a następnie wybierz pozycję **Dalej**.

1. W obszarze **Wybierz strukturę aplikacji** wybierz pozycję **ASP.NET Core**, a następnie wybierz pozycję **Dalej**.

1. Wybierz pozycję **Klaster usługi Service Fabric**, a następnie wybierz pozycję **Dalej**. 

## <a name="configure-azure-devops-and-an-azure-subscription"></a>Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure

1. Utwórz nową organizację usługi Azure DevOps lub wybierz istniejącą organizację. 

1. Wprowadź nazwę projektu usługi Azure DevOps. 

1. Wybierz swoją subskrypcję platformy Azure.

1. Aby wyświetlić dodatkowe ustawienia konfiguracji platformy Azure oraz określić rozmiar maszyny wirtualnej węzła i system operacyjny dla klastra usługi Service Fabric, wybierz pozycję **Zmień**. W tym okienku są wyświetlane różne opcje konfigurowania typu i lokalizacji usług platformy Azure.
 
1. Opuść obszar konfiguracji platformy Azure i wybierz pozycję **Gotowe**.  
    Proces zakończy się po kilku minutach. Nastąpi skonfigurowanie przykładowej aplikacji platformy ASP.NET Core w repozytorium Git w organizacji usługi Azure DevOps, utworzenie klastra usługi Service Fabric, uruchomienie potoku ciągłej integracji/ciągłego wdrażania oraz wdrożenie aplikacji na platformie Azure. 

    Po zakończeniu wszystkich tych operacji DevOps początkowy pulpit nawigacyjny zostanie wyświetlony w Azure Portal. Możesz również przejść do pulpitu nawigacyjnego DevOps Starter bezpośrednio ze **wszystkich zasobów** w Azure Portal. 

    Ten pulpit nawigacyjny zapewnia wgląd do repozytorium kodu usługi Azure DevOps, potoku ciągłej integracji/ciągłego wdrażania i klastra usługi Service Fabric. Możesz skonfigurować dodatkowe opcje dla potoku ciągłej integracji/ciągłego wdrażania w usłudze Azure Repos. Po prawej stronie wybierz pozycję **Przeglądaj**, aby wyświetlić uruchomioną aplikację.

## <a name="examine-the-ci-pipeline"></a>Badanie potoku ciągłej integracji

DevOps Starter automatycznie konfiguruje potok ciągłej integracji/ciągłego wdrażania w Azure Pipelines. Możesz przeglądać i dostosowywać potok. Aby zapoznać się z nim, wykonaj następujące czynności:

1. Przejdź do pulpitu nawigacyjnego DevOps Starter.

1. W górnej części pulpitu nawigacyjnego DevOps początkowy wybierz pozycję **potoki kompilacji**. Zostanie wyświetlona karta przeglądarki z potokiem kompilacji dla nowego projektu.

1. Wskaż pole **stan** , a następnie wybierz przycisk wielokropka (...). Menu wyświetla kilka opcji, takich jak kolejkowanie nowej kompilacji, wstrzymywanie kompilacji i edytowanie potoku kompilacji.

1. Wybierz pozycję **Edytuj**.

1. W tym okienku możesz zapoznać się z różnymi zadaniami w potoku kompilacji. W ramach kompilacji są wykonywane różne zadania, takie jak pobieranie kodu źródłowego z repozytorium Git, przywracanie zależności i publikowanie danych wyjściowych używanych na potrzeby wdrażania.

1. W górnej części potoku kompilacji wybierz jego nazwę. 

1. W obszarze nazwy potoku kompilacji wybierz pozycję **Historia**. W tym okienku jest wyświetlany dziennik inspekcji ostatnio wprowadzonych zmian w kompilacji. Usługa Azure DevOps śledzi wszelkie zmiany wprowadzone do potoku kompilacji i pozwala na porównanie wersji.

1. Wybierz pozycję **Wyzwalacze**. DevOps Starter automatycznie tworzy wyzwalacz CI i każde zatwierdzenie repozytorium uruchamia nową kompilację. Opcjonalnie możesz zdecydować się dołączyć gałęzie do procesu ciągłej integracji lub wykluczyć je z niego.

1. Wybierz pozycję **Przechowywanie**. W zależności od scenariusza możesz określić zasady przechowywania lub usuwania pewnej liczby kompilacji.

## <a name="examine-the-cd-pipeline"></a>Badanie potoku ciągłego wdrażania

DevOps Starter automatycznie tworzy i konfiguruje kroki niezbędne do wdrożenia z organizacji usługi Azure DevOps w ramach subskrypcji platformy Azure. Te kroki obejmują konfigurowanie połączenia usługi platformy Azure w celu uwierzytelniania usługi Azure DevOps w subskrypcji platformy Azure. W ramach automatyzacji jest też tworzony potok wydania, który zapewnia ciągłe wdrażanie na platformie Azure. Aby dowiedzieć się więcej o potoku wydania, wykonaj następujące czynności:

1. Wybierz pozycję **Kompilacja i wydanie**, a następnie wybierz pozycję **Wydania**. DevOps Starter tworzy potok wydania do zarządzania wdrożeniami na platformie Azure.

1. Wybierz symbol wielokropka (...) obok potoku wydania, a następnie wybierz pozycję **Edytuj**. Potok wydania zawiera *potok*, który definiuje proces tworzenia wydania.

1. W obszarze **Artefakty** wybierz polecenie **Porzuć**. Przedstawiony poprzednio potok kompilacji generuje dane wyjściowe używane na potrzeby artefaktu. 

1. Z prawej strony ikony **Porzuć** wybierz pozycję **Wyzwalacz ciągłego wdrażania**. Ten potok wydania ma włączony wyzwalacz ciągłego wdrażania, który przeprowadza wdrożenie za każdym razem, gdy jest dostępny nowy artefakt kompilacji. Opcjonalnie możesz wyłączyć wyzwalacz. Wtedy wdrożenia będą wymagać ręcznego wykonania. 

1. Po prawej stronie wybierz pozycję **Wyświetl wydania**, aby wyświetlić historię wydań.

1. Wybierz symbol wielokropka (...) obok wydania, a następnie wybierz pozycję **Otwórz**. Dostępnych jest kilka menu, z którymi możesz się zapoznać, na przykład podsumowanie wydań, skojarzone elementy robocze i testy.

1. Wybierz pozycję **Zatwierdzenia**. Ten widok przedstawia zatwierdzenia kodu skojarzone z danym wdrożeniem. Porównaj wydania, aby wyświetlić różnice w zatwierdzeniach pomiędzy wdrożeniami.

1. Wybierz pozycję **dzienniki**. Dzienniki zawierają przydatne informacje na temat procesu wdrażania. Możesz je wyświetlać zarówno podczas wdrażania, jak i po jego zakończeniu.

## <a name="commit-changes-to-git-and-automatically-deploy-them-to-azure"></a>Zatwierdzanie zmian w usłudze Git i automatyczne wdrażanie ich na platformie Azure 

 > [!NOTE]
 > Poniższa procedura testuje potok ciągłej integracji/ciągłego wdrażania, wprowadzając prostą zmianę tekstu.

Teraz możesz rozpocząć współpracę z zespołem nad aplikacją w języku Python w ramach procesu ciągłej integracji/ciągłego wdrażania, który automatycznie wdraża najnowszy kod w witrynie internetowej. Każda zmiana repozytorium usługi Git powoduje uruchomienie kompilacji, a wydanie wdraża zmiany na platformie Azure. Wykonaj procedurę opisaną w tej sekcji lub użyj innej techniki, aby zatwierdzić zmiany w repozytorium. Możesz na przykład sklonować repozytorium Git za pomocą ulubionego narzędzia lub środowiska IDE, a następnie wypchnąć zmiany do tego repozytorium.

1. W menu Azure DevOps wybierz pozycję pliki **kodu**  >  , a następnie przejdź do repozytorium.

1. Przejdź do katalogu *Views\Home*, wybierz symbol wielokropka (...) obok pliku *Index.cshtml* i wybierz pozycję **Edytuj**.

1. Wprowadź zmianę w pliku, na przykład dodaj tekst w jednym z tagów div. 

1. W prawym górnym rogu wybierz pozycję **Zatwierdź**, a następnie ponownie wybierz pozycję **Zatwierdź**, aby wypchnąć zmianę.  
    Po chwili kompilacja zostanie uruchomiona, a następnie zostanie wykonane wydanie w celu wdrożenia zmian. Stan kompilacji można monitorować na pulpicie nawigacyjnym DevOps Starter lub w przeglądarce za pomocą rejestrowania w czasie rzeczywistym usługi Azure DevOps.

1. Po zakończeniu tworzenia wydania odśwież aplikację, aby zweryfikować zmiany.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli przeprowadzasz testowanie, możesz uniknąć naliczania opłat, oczyszczając zasoby. Gdy klaster usługi Azure Service Fabric i powiązane zasoby utworzone w tym samouczku nie będą już potrzebne, możesz je usunąć. Aby to zrobić, użyj funkcji **usuwania** na pulpicie nawigacyjnym DevOps Starter.

> [!IMPORTANT]
> Poniższa procedura powoduje trwałe usunięcie zasobów. Funkcja *usuwania* niszczy dane, które są tworzone przez projekt w DevOps Starter w systemie Azure i Azure DevOps, i nie będzie można go pobrać. Użyj tej procedury dopiero po uważnym przeczytaniu monitów.

1. W Azure Portal przejdź do pulpitu nawigacyjnego DevOps Starter.
1. W prawym górnym rogu wybierz pozycję **Usuń**. 
1. Po wyświetleniu monitu wybierz pozycję **Tak**, aby *trwale usunąć* zasoby.

## <a name="next-steps"></a>Następne kroki

Opcjonalnie możesz zmodyfikować potok ciągłej integracji/ciągłego wdrażania platformy Azure w celu zaspokojenia potrzeb zespołu. Możesz także użyć wzorca ciągłej integracji/ciągłego wdrażania jako szablonu podczas pracy z innymi potokami. W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Użyj DevOps Starter, aby utworzyć aplikację ASP.NET Core i wdrożyć ją w Service Fabric
> * Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure 
> * Badanie potoku ciągłej integracji
> * Badanie potoku ciągłego wdrażania
> * Zatwierdzanie zmian w usłudze Git i automatyczne wdrażanie ich na platformie Azure
> * Czyszczenie zasobów

Aby dowiedzieć się więcej na temat usługi Service Fabric i mikrousług, zobacz:

> [!div class="nextstepaction"]
> [Use a microservices approach for building applications (Korzystanie z mikrousług podczas tworzenia aplikacji)](/azure/devops/pipelines/release/define-multistage-release-process)