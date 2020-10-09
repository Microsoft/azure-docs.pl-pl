---
title: 'Samouczek: wdrażanie aplikacji ASP.NET na Azure Functions za pomocą usługi Azure DevOps Starter'
description: Usługa Azure DevOps Starter ułatwia rozpoczęcie pracy z platformą Azure. Dzięki DevOps Starter możesz wdrożyć aplikację ASP.NET, aby Azure Functions w kilku prostych krokach.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.custom: devx-track-csharp
ms.openlocfilehash: 3277fefb5615fd492b2859b741ef8e6f991eaf23
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91355478"
---
# <a name="deploy-to-azure-functions-with-devops-starter"></a>Wdrażanie do Azure Functions za pomocą DevOps Starter

Usługa Azure DevOps Starter przedstawia uproszczone środowisko, w którym można przenieść istniejący kod i repozytorium Git lub wybrać przykładową aplikację w celu utworzenia potoku ciągłej integracji i ciągłego dostarczania na platformę Azure.

DevOps Starter również:

* Automatycznie tworzy zasoby platformy Azure, takie jak Azure Functions

* Tworzy i konfiguruje potok wydania na platformie Azure DevOps dla ciągłej integracji/ciągłego wdrażania

Ten samouczek obejmuje następujące kroki:

> [!div class="checklist"]
>* Używanie narzędzia DevOps Starter do wdrażania aplikacji ASP.NET w usłudze Azure Function
>* Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure
>* Sprawdzanie funkcji platformy Azure
>* Badanie potoku ciągłej integracji
>* Badanie potoku ciągłego wdrażania
>* Zatwierdzanie zmian w usłudze Git i automatyczne wdrażanie ich na platformie Azure
>* Czyszczenie zasobów

Obecnie obsługiwane środowiska uruchomieniowe dla funkcji to **.NET** i **Node.js**. Używamy. Środowisko uruchomieniowe sieci dla tego samouczka do wdrożenia w Azure Functions. 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Możesz skorzystać z jednej bezpłatnej [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)

## <a name="use-devops-starter-to-deploy-an-aspnet-app-to-azure-functions"></a>Użyj DevOps Starter, aby wdrożyć aplikację ASP.NET do Azure Functions

DevOps Starter tworzy potok ciągłej integracji/ciągłego wdrażania w Azure Pipelines. Możesz utworzyć nową organizację usługi Azure DevOps lub użyć istniejącej organizacji. DevOps Projects również tworzy zasoby platformy Azure, takie jak IoTHub, w wybranej subskrypcji platformy Azure.

1. Zaloguj się do witryny [Azure Portal](https://portal.azure.com).

1. W polu wyszukiwania wpisz **DevOps Starter**, a następnie wybierz opcję. Kliknij przycisk **Dodaj** , aby utworzyć nowy.

    ![Pulpit nawigacyjny DevOps Starter](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Wybierz pozycję **.NET**, a następnie wybierz pozycję **Dalej**. W obszarze **Wybierz strukturę aplikacji**wybierz pozycję **ASP.NET** , a następnie kliknij przycisk **dalej**.

1. Wybierz **aplikacja funkcji** a następnie wybierz przycisk **dalej**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure

1. Wprowadź nazwę projektu usługi Azure DevOps.

1. Utwórz nową organizację usługi Azure DevOps lub wybierz istniejącą organizację.

1. Wybierz swoją subskrypcję platformy Azure.

1. Aby wyświetlić dodatkowe ustawienia konfiguracji platformy Azure i określić warstwę cenową i lokalizację, kliknij pozycję **Ustawienia dodatkowe**. W tym okienku są wyświetlane różne opcje konfigurowania warstwy cenowej i lokalizacji usług platformy Azure.

1. Opuść obszar konfiguracji platformy Azure i wybierz pozycję **Gotowe**.

1. Po kilku minutach proces zostanie ukończony. Przykładowa aplikacja ASP.NET jest skonfigurowana w repozytorium Git w organizacji usługi Azure DevOps, aplikacja funkcji i Application Insights zostanie utworzona potok ciągłej integracji/ciągłego wdrażania, a aplikacja zostanie wdrożona na platformie Azure.

   Po zakończeniu całego tego zadania pulpit nawigacyjny Azure DevOps Starter zostanie wyświetlony w Azure Portal. Możesz również przejść do pulpitu nawigacyjnego DevOps Starter bezpośrednio ze **wszystkich zasobów** w Azure Portal.

   Ten pulpit nawigacyjny zapewnia wgląd w repozytorium kodu usługi Azure DevOps, potok CI/CD i funkcję platformy Azure. Dodatkowe opcje ciągłej integracji/ciągłego wdrażania możesz skonfigurować w potoku usługi Azure DevOps. Po prawej stronie wybierz **aplikacja funkcji** do wyświetlenia.

## <a name="examine-the-function-app"></a>Sprawdzanie aplikacja funkcji

DevOps Starter automatycznie konfiguruje aplikację funkcji, którą można eksplorować i dostosowywać. Aby poznać aplikację funkcji, wykonaj następujące czynności:

1. Przejdź do pulpitu nawigacyjnego DevOps Starter.

    ![Pulpit nawigacyjny DevOps Projects](_img/azure-devops-project-functions/fapp-dashboard.png)

1. Po prawej stronie wybierz aplikację funkcji. Zostanie otwarte okienko dla aplikacji funkcji. Z tego widoku można wykonywać różne czynności, takie jak monitorowanie operacji, wyszukiwanie dzienników.

    ![Aplikacja funkcji](_img/azure-devops-project-functions/function-app.png)

## <a name="examine-the-ci-pipeline"></a>Badanie potoku ciągłej integracji

DevOps Starter automatycznie konfiguruje potok ciągłej integracji/ciągłego wdrażania w organizacji usługi Azure DevOps. Możesz przeglądać i dostosowywać potok. Aby zapoznać się z nim, wykonaj następujące czynności:

1. Przejdź do pulpitu nawigacyjnego DevOps Starter.

1. Kliknij hiperlink w obszarze **kompilacja**. Zostanie wyświetlona karta przeglądarki z potokiem kompilacji dla nowego projektu.

    ![Zrzut ekranu przedstawia pulpit nawigacyjny Azure Portal DevOps Starter ze strzałką wskazującą link w obszarze kompilacja w potoku C I/C D.](_img/azure-devops-project-functions/build.png)

1. Kliknij pozycję **Edytuj**. W tym okienku możesz zapoznać się z różnymi zadaniami w potoku kompilacji. Kompilacja wykonuje różne zadania, takie jak pobieranie kodu źródłowego z repozytorium git, tworzenie aplikacji, uruchamianie testów jednostkowych i publikowanie danych wyjściowych, które są używane do wdrożeń.

1. Wybierz pozycję **Wyzwalacze**. DevOps Starter automatycznie tworzy wyzwalacz CI i każde zatwierdzenie repozytorium uruchamia nową kompilację. Opcjonalnie możesz zdecydować się dołączyć gałęzie do procesu ciągłej integracji lub wykluczyć je z niego.

1. Wybierz pozycję **Przechowywanie**. W zależności od scenariusza możesz określić zasady przechowywania lub usuwania pewnej liczby kompilacji.

1. W górnej części potoku kompilacji wybierz jego nazwę.

1. Zmień nazwę potoku kompilacji na coś bardziej opisowego, a następnie wybierz pozycję **Zapisz** na liście rozwijanej **Zapisz & kolejki** .

1. W obszarze nazwy potoku kompilacji wybierz pozycję **Historia**. W tym okienku jest wyświetlany dziennik inspekcji ostatnio wprowadzonych zmian w kompilacji. Usługa Azure DevOps śledzi wszelkie zmiany wprowadzone w potoku kompilacji i umożliwia porównanie wersji.

## <a name="examine-the-cd-release-pipeline"></a>Badanie potoku wydania ciągłego wdrażania

DevOps Starter automatycznie tworzy i konfiguruje kroki niezbędne do wdrożenia z organizacji usługi Azure DevOps w ramach subskrypcji platformy Azure. Te kroki obejmują konfigurowanie połączenia usługi platformy Azure w celu uwierzytelniania usługi Azure DevOps w subskrypcji platformy Azure. W ramach automatyzacji jest też tworzony potok wydania, który zapewnia ciągłe wdrażanie na platformie Azure. Aby dowiedzieć się więcej o potoku wydania, wykonaj następujące czynności:

1. Przejdź do **potoków | Wersje**.

1. Kliknij pozycję **Edytuj**.

1. W obszarze **Artefakty** wybierz polecenie **Porzuć**. Potok kompilacji przedstawiony w poprzednich krokach generuje dane wyjściowe używane na potrzeby artefaktu.

1. Z prawej strony ikony **Porzuć** wybierz pozycję **Wyzwalacz ciągłego wdrażania**. Ten potok wydania ma włączony wyzwalacz ciągłego wdrażania, który przeprowadza wdrożenie za każdym razem, gdy jest dostępny nowy artefakt kompilacji. Opcjonalnie możesz wyłączyć wyzwalacz. Wtedy wdrożenia będą wymagać ręcznego wykonania.

1. Po prawej stronie wybierz pozycję **Wyświetl wydania**, aby wyświetlić historię wydań.

1. Kliknij wydanie, które spowoduje wyświetlenie potoku. Kliknij dowolne środowisko, aby sprawdzić podsumowanie wydania **, zatwierdzenia**, skojarzone **elementy robocze**.

1. Wybierz pozycję **Zatwierdzenia**. Ten widok przedstawia zatwierdzenia kodu skojarzone z danym wdrożeniem. Porównaj wydania, aby wyświetlić różnice w zatwierdzeniach pomiędzy wdrożeniami.

1. Wybierz pozycję **Wyświetl dzienniki**. Dzienniki zawierają przydatne informacje na temat procesu wdrażania. Możesz je wyświetlać zarówno podczas wdrażania, jak i po jego zakończeniu.

## <a name="commit-code-changes-and-execute-cicd"></a>Zatwierdzanie zmian kodu i wykonywanie ciągłej integracji/ciągłego wdrażania

> [!NOTE]
> Poniższa procedura testuje potok ciągłej integracji/ciągłego wdrażania, wprowadzając prostą zmianę tekstu.

Teraz możesz przystąpić do współpracy z zespołem w aplikacji przy użyciu procesu ciągłej integracji/ciągłego wdrażania, który automatycznie wdraża najnowszą pracę w funkcji platformy Azure. Każda zmiana w repozytorium Git rozpoczyna kompilację w usłudze Azure DevOps, a potok ciągłego wdrażania wykonuje wdrażanie na platformie Azure. Wykonaj procedurę opisaną w tej sekcji lub użyj innej techniki, aby zatwierdzić zmiany w repozytorium. Możesz na przykład sklonować repozytorium Git za pomocą ulubionego narzędzia lub środowiska IDE, a następnie wypchnąć zmiany do tego repozytorium.

1. W menu Azure DevOps wybierz kolejno pozycje **repozytoria | Pliki**, a następnie przejdź do repozytorium.

1. Repozytorium zawiera już kod o nazwie **SampleFunctionApp** na podstawie języka aplikacji wybranego w procesie tworzenia. Otwórz plik **Application/SampleFunctionApp/Function1. cs** .

1. Wybierz pozycję **Edytuj**, a następnie wprowadź zmiany w **wierszu numer 31** . Na przykład możesz ją zaktualizować, aby **powitać! Witamy w Azure Functions przy użyciu DevOps Starter**

1. W prawym górnym rogu wybierz pozycję **Zatwierdź**, a następnie ponownie wybierz pozycję **Zatwierdź**, aby wypchnąć zmianę.

1. Otwórz plik **Application/SampleFunctionApp. test/Function1TestRunner. cs** . 

1. Wybierz pozycję **Edytuj**, a następnie wprowadź zmiany w **wierszu numer 21**. Na przykład możesz ją zaktualizować, aby **powitać! Witamy w Azure Functions przy użyciu usługi Azure DevOps Starter**.

     Po chwili zostanie uruchomiona kompilacja w usłudze Azure DevOps, a następnie zostanie wykonane wydanie w celu wdrożenia zmian. Monitoruj stan kompilacji na pulpicie nawigacyjnym DevOps Starter lub w przeglądarce za pomocą swojej organizacji usługi Azure DevOps.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Możesz usunąć powiązane zasoby, które zostały utworzone, gdy nie są już potrzebne. Użyj funkcji **usuwania** na pulpicie nawigacyjnym DevOps Starter.

## <a name="next-steps"></a>Następne kroki

Opcjonalnie możesz zmodyfikować potoki kompilacji i wydania, aby zaspokoić potrzeby swojego zespołu. Możesz także użyć wzorca ciągłej integracji/ciągłego wdrażania jako szablonu podczas pracy z innymi potokami. W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Używanie narzędzia DevOps Starter do wdrażania aplikacji ASP.NET Core w usłudze Azure Function
> * Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure 
> * Sprawdzanie funkcji platformy Azure
> * Badanie potoku ciągłej integracji
> * Badanie potoku ciągłego wdrażania
> * Zatwierdzanie zmian w usłudze Git i automatyczne wdrażanie ich na platformie Azure
> * Czyszczenie zasobów

