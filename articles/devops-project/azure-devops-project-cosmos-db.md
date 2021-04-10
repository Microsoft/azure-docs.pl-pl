---
title: 'Samouczek: wdrażanie aplikacji Node.js obsługiwanych przez Azure Cosmos DB za pomocą usługi Azure DevOps Starter'
description: Usługa Azure DevOps Starter ułatwia rozpoczęcie pracy z platformą Azure. Dzięki DevOps Starter możesz wdrożyć aplikację Node.js, która jest oparta na Azure Cosmos DB do aplikacji sieci Web systemu Windows w kilku prostych krokach.
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.custom: devx-track-js
ms.openlocfilehash: d1bdae976d5e5b7d98311d33bb38b5035f2557ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102548703"
---
# <a name="deploy-nodejs-apps-powered-by-azure-cosmos-db-with-devops-starter"></a>Wdróż Node.js aplikacje obsługiwane przez Azure Cosmos DB z DevOps Starter

Usługa Azure DevOps Starter oferuje usprawnione środowisko, w którym można utworzyć potok ciągłej integracji i ciągłego wdrażania (CD) na platformie Azure. Można to zrobić przy użyciu istniejącego kodu i repozytorium git (repozytorium) lub wybierając przykładową aplikację.

DevOps Starter również:

* Automatycznie tworzy zasoby platformy Azure, takie jak Azure Cosmos DB, Application Insights platformy Azure, Azure App Service i plany App Service

* Tworzy i konfiguruje potok zwolnienia ciągłej integracji/ciągłego wdrażania w usłudze Azure DevOps

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Użyj DevOps Starter do wdrożenia aplikacji Node.js obsługiwanej przez Azure Cosmos DB
> * Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure
> * Sprawdzanie Azure Cosmos DB
> * Badanie potoku ciągłej integracji
> * Badanie potoku ciągłego wdrażania
> * Zatwierdź zmiany w usłudze git i automatycznie wdróż je na platformie Azure
> * Oczyszczanie zasobów

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebna jest subskrypcja platformy Azure, za pomocą której możesz bezpłatnie uzyskać dostęp [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) .

## <a name="use-devops-starter-to-deploy-nodejs-app"></a>Wdrażanie aplikacji Node.js przy użyciu programu DevOps Starter

DevOps Starter tworzy potok ciągłej integracji/ciągłego wdrażania w Azure Pipelines. Możesz utworzyć nową organizację usługi Azure DevOps lub użyć istniejącej organizacji. DevOps Starter tworzy również zasoby platformy Azure, takie jak Azure Cosmos DB, Application Insights, App Service i plany App Service, w wybranej subskrypcji platformy Azure.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W polu wyszukiwania wpisz **DevOps Starter**, a następnie wybierz opcję. Kliknij przycisk **Dodaj** , aby utworzyć nowy.

    ![Pulpit nawigacyjny DevOps Starter](_img/azure-devops-starter-aks/search-devops-starter.png)

1. Wybierz **Node.js** jako środowisko uruchomieniowe, a następnie wybierz przycisk **dalej**. W obszarze **Wybierz platformę aplikacji** wybierz pozycję **Express.js**.

1. Włącz sekcję **Dodawanie bazy danych** dla **Cosmos DB**, a następnie wybierz pozycję **dalej**.

    ![Dodawanie bazy danych](_img/azure-devops-project-cosmos-db/add-database.png)

    Usługa Azure DevOps Starter obsługuje różne struktury aplikacji, takie jak **Express.js**, **przykładowa aplikacja Node.js** i **Sail.js**. W tym samouczku użyjemy **Express.js**.

1. Wybierz usługę platformy Azure, aby wdrożyć aplikację, a następnie wybierz pozycję **dalej**. Dostępne opcje to: Web App, Azure Kubernetes Service i Azure Web App for Containers. W tym samouczku używamy **aplikacji sieci Web systemu Windows**.

## <a name="configure-azure-devops-and-azure-subscription"></a>Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure

1. Wprowadź nazwę projektu usługi Azure DevOps.

1. Utwórz nową organizację usługi Azure DevOps lub wybierz istniejącą organizację.

1. Wybierz swoją subskrypcję platformy Azure.

1. Aby wyświetlić dodatkowe ustawienia konfiguracji platformy Azure lub określić warstwę cenową i lokalizację, wybierz pozycję **dodatkowe ustawienia**. W tym okienku przedstawiono różne opcje konfigurowania warstwy cenowej i lokalizacji usług platformy Azure.

1. Opuść obszar konfiguracji platformy Azure i wybierz pozycję **Gotowe**.

1. Proces kończy się po kilku minutach. Przykładowa aplikacja Node.js została skonfigurowana w repozytorium Git w organizacji usługi Azure DevOps. Następnie tworzone są Azure Cosmos DB, App Service, plan App Service i zasoby Application Insights, a także potok ciągłej integracji/ciągłego wdrażania. Aplikacja zostanie następnie wdrożona na platformie Azure.

   Po zakończeniu wszystkich tych procesów pulpit nawigacyjny Azure DevOps Starter zostanie wyświetlony w Azure Portal. Możesz również przejść do pulpitu nawigacyjnego DevOps Starter bezpośrednio ze **wszystkich zasobów** w Azure Portal.

   Ten pulpit nawigacyjny zapewnia wgląd w repozytorium kodu usługi Azure DevOps, potok CI/CD i bazę danych Azure Cosmos DB. Dodatkowe opcje ciągłej integracji/ciągłego wdrażania możesz skonfigurować w potoku usługi Azure DevOps. Po prawej stronie pulpitu nawigacyjnego wybierz pozycję **Azure Cosmos DB** , aby wyświetlić te opcje.

## <a name="examine-azure-cosmos-db"></a>Sprawdzanie Azure Cosmos DB

DevOps Starter automatycznie konfiguruje Azure Cosmos DB, które można eksplorować i dostosowywać. Aby zaznajomić się z Azure Cosmos DB, wykonaj następujące czynności:

1. Przejdź do pulpitu nawigacyjnego DevOps Starter.

    ![Pulpit nawigacyjny DevOps Projects](_img/azure-devops-project-cosmos-db/devops-starter-dashboard.png)

1. Po prawej stronie wybierz pozycję Azure Cosmos DB. Zostanie otwarte okienko dla Azure Cosmos DB. Z tego widoku można wykonywać różne akcje, takie jak operacje monitorowania i wyszukiwanie dzienników.

    ![Okienko Azure Cosmos DB](_img/azure-devops-project-cosmos-db/cosmos-db.png)

## <a name="examine-the-ci-pipeline"></a>Badanie potoku ciągłej integracji

DevOps Starter automatycznie konfiguruje potok ciągłej integracji/ciągłego wdrażania w organizacji usługi Azure DevOps. Możesz przeglądać i dostosowywać potok. Aby zapoznać się z nim, wykonaj następujące czynności:

1. Przejdź do pulpitu nawigacyjnego DevOps Starter.

1. Wybierz hiperlink w obszarze **kompilacja**. Zostanie wyświetlona karta przeglądarki z potokiem kompilacji dla nowego projektu.

    ![Okienko kompilacji](_img/azure-devops-project-cosmos-db/build.png)

1. Wybierz pozycję **Edytuj**. W tym okienku możesz zapoznać się z różnymi zadaniami w potoku kompilacji. Kompilacja wykonuje różne zadania, takie jak pobieranie kodu źródłowego z repozytorium git, tworzenie aplikacji, uruchamianie testów jednostkowych i publikowanie danych wyjściowych, które są używane do wdrożeń.

1. Wybierz pozycję **Wyzwalacze**. DevOps Starter automatycznie tworzy wyzwalacz CI i każde zatwierdzenie repozytorium uruchamia nową kompilację. Możesz dołączać lub wykluczać gałęzie z procesu CI elementu konfiguracji.

1. Wybierz pozycję **Przechowywanie**. W zależności od scenariusza możesz określić zasady przechowywania lub usuwania pewnej liczby kompilacji.

1. W górnej części potoku kompilacji wybierz jego nazwę.

1. Zmień nazwę potoku kompilacji na coś bardziej opisowego, a następnie wybierz pozycję **Zapisz** na liście rozwijanej **Zapisz & kolejki** .

1. W obszarze nazwy potoku kompilacji wybierz pozycję **Historia**. W tym okienku jest wyświetlany dziennik inspekcji ostatnio wprowadzonych zmian w kompilacji. Usługa Azure DevOps śledzi wszelkie zmiany wprowadzone do potoku kompilacji i pozwala na porównanie wersji.

## <a name="examine-the-cd-release-pipeline"></a>Badanie potoku wydania ciągłego wdrażania

DevOps Starter automatycznie tworzy i konfiguruje kroki niezbędne do wdrożenia z organizacji usługi Azure DevOps w ramach subskrypcji platformy Azure. Te kroki obejmują konfigurowanie połączenia usługi platformy Azure w celu uwierzytelniania usługi Azure DevOps w subskrypcji platformy Azure. W ramach automatyzacji jest też tworzony potok wydania, który zapewnia ciągłe wdrażanie na platformie Azure. Aby dowiedzieć się więcej o potoku wydania, wykonaj następujące czynności:

1. Przejdź do pozycji **potoki** i wybierz pozycję **wersje**.

1. Wybierz pozycję **Edytuj**.

1. W obszarze **Artefakty** wybierz polecenie **Porzuć**. Potok kompilacji przedstawiony w poprzednich krokach generuje dane wyjściowe używane na potrzeby artefaktu.

1. Z prawej strony ikony **rozwijanej** wybierz pozycję **wyzwalacz ciągłego wdrażania**. Ten potok wersji włączył wyzwalacz ciągłego wdrażania, który wykonuje wdrożenie za każdym razem, gdy jest dostępny nowy artefakt kompilacji. Można wyłączyć wyzwalacz, aby wdrożenia były wykonywane ręcznie.

1. Po prawej stronie wybierz pozycję **Wyświetl wersje** , aby wyświetlić historię wersji.

1. Wybierz wersję, która spowoduje wyświetlenie potoku. Wybierz dowolne środowisko, aby sprawdzić podsumowanie wydania, zatwierdzenia lub skojarzone elementy robocze.

1. Wybierz pozycję **Zatwierdzenia**. Ten widok przedstawia zatwierdzenia kodu skojarzone z danym wdrożeniem. Porównaj wydania, aby wyświetlić różnice w zatwierdzeniach pomiędzy wdrożeniami.

1. Wybierz pozycję **Wyświetl dzienniki**. Dzienniki zawierają przydatne informacje na temat procesu wdrażania. Możesz je wyświetlać zarówno podczas wdrażania, jak i po jego zakończeniu.

## <a name="commit-code-changes-and-execute-the-cicd-pipeline"></a>Zatwierdzanie zmian kodu i wykonywanie potoku ciągłej integracji/ciągłego wdrażania

> [!NOTE]
> Poniższa procedura testuje potok ciągłej integracji/ciągłego wdrażania, wprowadzając prostą zmianę tekstu.

Teraz możesz przystąpić do współpracy z zespołem w aplikacji przy użyciu procesu ciągłej integracji/ciągłego wdrażania, który wdraża najnowszą pracę na App Service. Każda zmiana w repozytorium Git rozpoczyna kompilację w usłudze Azure DevOps, a potok ciągłego wdrażania wykonuje wdrażanie na platformie Azure. Wykonaj procedurę opisaną w tej sekcji lub użyj innej techniki, aby zatwierdzić zmiany w repozytorium. Możesz na przykład sklonować repozytorium Git za pomocą ulubionego narzędzia lub środowiska IDE, a następnie wypchnąć zmiany do tego repozytorium.

1. W menu Azure DevOps wybierz kolejno pozycje **repozytoria** i **pliki**. Następnie przejdź do repozytorium.

1. Repozytorium zawiera już kod oparty na języku aplikacji wybranym w procesie tworzenia. Otwórz plik **Application/views/index. Pug** .

1. Wybierz pozycję **Edytuj**, a następnie wprowadź zmiany w **wierszu numer 15**. Można na przykład zmienić ją na "pierwsze wdrożenie, Azure App Service obsługiwane przez Azure Cosmos DB".

1. W prawym górnym rogu wybierz pozycję **Zatwierdź**, a następnie ponownie wybierz pozycję **Zatwierdź** , aby wypchnąć zmianę.

     Po kilku sekundach kompilacja rozpocznie się w usłudze Azure DevOps i zostanie wykonana wersja w celu wdrożenia zmian. Monitoruj stan kompilacji na pulpicie nawigacyjnym DevOps Starter lub w przeglądarce za pomocą swojej organizacji usługi Azure DevOps.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Usuń powiązane zasoby, które zostały utworzone, gdy nie są już potrzebne. Użyj funkcji **usuwania** na pulpicie nawigacyjnym DevOps Starter.

## <a name="next-steps"></a>Następne kroki

Możesz zmodyfikować potoki kompilacji i wydania, aby dopasować je do potrzeb swojego zespołu. Możesz także użyć wzorca ciągłej integracji/ciągłego wdrażania jako szablonu podczas pracy z innymi potokami. W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Użyj DevOps Starter do wdrożenia aplikacji Node.js obsługiwanej przez Azure Cosmos DB
> * Konfigurowanie usługi Azure DevOps i subskrypcji platformy Azure 
> * Sprawdzanie Azure Cosmos DB
> * Badanie potoku ciągłej integracji
> * Badanie potoku ciągłego wdrażania
> * Zatwierdzanie zmian w usłudze Git i automatyczne wdrażanie ich na platformie Azure
> * Czyszczenie zasobów

Aby uzyskać więcej informacji, zobacz sekcję [Definiowanie potoku ciągłego wdrażania wieloetapowego (CD)](/azure/devops/pipelines/release/define-multistage-release-process) i następnych kroków.