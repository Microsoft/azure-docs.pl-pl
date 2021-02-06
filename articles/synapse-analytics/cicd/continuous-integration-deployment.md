---
title: Ciągła integracja i dostarczanie dla obszaru roboczego Synapse
description: Dowiedz się, jak korzystać z ciągłej integracji i dostarczania, aby wdrażać zmiany w obszarze roboczym z jednego środowiska (opracowywanie, testowanie i produkcja).
services: synapse-analytics
author: liud
ms.service: synapse-analytics
ms.topic: conceptual
ms.date: 11/20/2020
ms.author: liud
ms.reviewer: pimorano
ms.openlocfilehash: 5f82e8b7359b90d5127e2c20a2b89cc5ad739a56
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2021
ms.locfileid: "99624764"
---
# <a name="continuous-integration-and-delivery-for-azure-synapse-workspace"></a>Ciągła integracja i dostarczanie dla obszaru roboczego usługi Azure Synapse

## <a name="overview"></a>Omówienie

Ciągła integracja (CI) to proces automatyzacji kompilowania i testowania kodu za każdym razem, gdy członek zespołu zatwierdza zmiany w kontroli wersji. Ciągłe wdrażanie (CD) to proces kompilowania, testowania, konfigurowania i wdrażania wielu środowisk testowych lub przejściowych w środowisku produkcyjnym.

W przypadku usługi Azure Synapse Workspace, ciągłej integracji i dostarczania (CI/CD) przenosi wszystkie jednostki z jednego środowiska (Programowanie, testowanie, produkcja) do innego. Aby podwyższyć poziom obszaru roboczego do innego obszaru roboczego, istnieją dwie części: Użyj [szablonów Azure Resource Manager](../../azure-resource-manager/templates/overview.md) do tworzenia lub aktualizowania zasobów obszaru roboczego (pul i obszarów roboczych); Migrowanie artefaktów (skrypty SQL, Notes, definicja zadań platformy Spark, potoki, zestawy danych, przepływy, dane itp.) dzięki narzędziom CI/CD Synapse na platformie Azure DevOps. 

W tym artykule zawarto informacje na temat korzystania z potoku wersji platformy Azure w celu zautomatyzowania wdrożenia obszaru roboczego Synapse w wielu środowiskach.

## <a name="prerequisites"></a>Wymagania wstępne

-   Obszar roboczy używany do tworzenia aplikacji został skonfigurowany z repozytorium Git w programie Studio, patrz [Kontrola źródła w programie Synapse Studio](source-control.md).
-   Projekt usługi Azure DevOps został przygotowany do uruchomienia potoku wydania.

## <a name="set-up-a-release-pipelines"></a>Konfigurowanie potoków wydania

1.  W [usłudze Azure DevOps](https://dev.azure.com/)Otwórz projekt utworzony dla tego wydania.

1.  W lewej części strony wybierz pozycję **potoki**, a następnie wybierz pozycję **wersje**.

    ![Wybieranie potoków, wydań](media/create-release-1.png)

1.  Wybierz pozycję **Nowy potok** lub, jeśli masz istniejące potoki, wybierz pozycję **Nowy** , a następnie **nowe potoku wydania**.

1.  Wybierz **pusty szablon zadania** .

    ![Wybierz puste zadanie](media/create-release-select-empty.png)

1.  W polu **Nazwa etapu** wprowadź nazwę środowiska.

1.  Wybierz pozycję **Dodaj artefakt**, a następnie wybierz repozytorium git skonfigurowane przy użyciu programu Development Synapse Studio. Wybierz repozytorium git, które zostało użyte do zarządzania szablonami ARM pul i obszaru roboczego. Jeśli używasz usługi GitHub jako źródła, musisz utworzyć połączenie z usługą dla konta w usłudze GitHub i pobrać repozytoria. Aby uzyskać więcej informacji na temat [połączenia z usługą](/azure/devops/pipelines/library/service-endpoints) 

    ![Dodaj gałąź publikowania](media/release-creation-github.png)

1.  Wybierz gałąź szablonu usługi ARM dla aktualizacji zasobów. W polu **wersja domyślna** wybierz pozycję **Najnowsza z gałęzi domyślnej**.

    ![Dodaj szablon ARM](media/release-creation-arm-branch.png)

1.  Wybierz [gałąź Publikuj](source-control.md#configure-publishing-settings) repozytorium dla **gałęzi domyślnej**. Domyślnie ta gałąź publikowania to `workspace_publish` . W polu **wersja domyślna** wybierz pozycję **Najnowsza z gałęzi domyślnej**.

    ![Dodawanie artefaktu](media/release-creation-publish-branch.png)

## <a name="set-up-a-stage-task-for-arm-resource-create-and-update"></a>Skonfiguruj zadanie etapowe tworzenia i aktualizowania zasobów usługi ARM 

Dodaj zadanie wdrażania Azure Resource Manager, aby tworzyć lub aktualizować zasoby, w tym obszary robocze i pule:

1. W widoku etap wybierz pozycję **Wyświetl zadania etapowe**.

    ![Widok etapu](media/release-creation-stage-view.png)

1. Utwórz nowe zadanie. Wyszukaj **wdrożenie szablonu ARM**, a następnie wybierz pozycję **Dodaj**.

1. W zadaniu wdrażania wybierz subskrypcję, grupę zasobów i lokalizację docelowego obszaru roboczego. W razie potrzeby podaj poświadczenia.

1. Na liście **Akcja** wybierz pozycję **Utwórz lub Zaktualizuj grupę zasobów**.

1. Wybierz przycisk wielokropka (**...**) obok pola **szablon** . Przeglądaj w poszukiwaniu szablonu Azure Resource Manager docelowego obszaru roboczego

1. Wybierz **...** obok pola **Parametry szablonu** , aby wybrać plik parametrów.

1. Wybierz **...** obok pola **Przesłoń parametry szablonu** wprowadź odpowiednie wartości parametrów dla docelowego obszaru roboczego. 

1. Wybierz opcję **przyrostowy** dla **trybu wdrożenia**.
    
    ![Wdrażanie obszarów roboczych i pul](media/pools-resource-deploy.png)

1. Obowiązkowe Dodaj **Azure PowerShell** do przypisania roli obszaru roboczego Grant i Update. Jeśli używasz potoku wydania do tworzenia obszaru roboczego Synapse, jednostka usługi potoku zostanie dodana jako domyślny administrator obszaru roboczego. Możesz uruchomić program PowerShell, aby przyznać innym kontom dostęp do obszaru roboczego. 
    
    ![Udziel uprawnienia](media/release-creation-grant-permission.png)

 > [!WARNING]
> W trybie ukończenia wdrożenia zasoby istniejące w grupie zasobów, ale nie są określone w nowym szablonie Menedżer zasobów, zostaną **usunięte**. Aby uzyskać więcej informacji, zobacz [tryby wdrażania Azure Resource Manager](../../azure-resource-manager/templates/deployment-modes.md)

## <a name="set-up-a-stage-task-for-artifacts-deployment"></a>Konfigurowanie zadania etapowego dla wdrożenia artefaktów 

Użyj rozszerzenia [wdrożenia obszaru roboczego Synapse](https://marketplace.visualstudio.com/items?itemName=AzureSynapseWorkspace.synapsecicd-deploy) , aby wdrożyć inne elementy w obszarze roboczym usługi Synapse, takie jak zestaw danych, skrypt SQL, Notes, definicja zadania platformy Spark, przepływu danych, potok, połączona usługa, poświadczenia i IR (Integration Runtime).  

1. Wyszukaj i Pobierz rozszerzenie z **witryny Azure DevOps Marketplace**(https://marketplace.visualstudio.com/azuredevops) 

     ![Pobierz rozszerzenie](media/get-extension-from-market.png)

1. Wybierz organizację, w której ma zostać zainstalowane rozszerzenie. 

     ![Instalowanie rozszerzenia](media/install-extension.png)

1. Upewnij się, że jednostka usługi potoku platformy Azure DevOps otrzymała uprawnienia do subskrypcji, a także została przypisana jako administrator obszaru roboczego dla docelowego miejsca pracy. 

1. Utwórz nowe zadanie. Wyszukaj **wdrożenie obszaru roboczego Synapse**, a następnie wybierz pozycję **Dodaj**.

     ![Dodaj rozszerzenie](media/add-extension-task.png)

1.  W zadaniu wybierz pozycję **...** obok pola **szablon** wybierz plik szablonu.

1. Wybierz **...** obok pola **Parametry szablonu** , aby wybrać plik parametrów.

1. Wybierz połączenie, grupę zasobów i nazwę docelowego obszaru roboczego. 

1. Wybierz **...** obok pola **Przesłoń parametry szablonu** wprowadź odpowiednie wartości parametrów dla docelowego obszaru roboczego. 

    ![Wdrażanie obszaru roboczego Synapse](media/create-release-artifacts-deployment.png)

> [!IMPORTANT]
> W scenariuszach ciągłej integracji/ciągłego wdrażania typ środowiska Integration Runtime (IR) w różnych środowiskach musi być taki sam. Na przykład jeśli masz własne środowisko IR w środowisku deweloperskim, ten sam IR musi być również typu samodzielnego w innych środowiskach, na przykład test i środowisko produkcyjne. Podobnie, jeśli udostępniasz środowiska Integration Runtime na wielu etapach, musisz skonfigurować środowisko Integration Runtime jako połączone samodzielnie hostowane we wszystkich środowiskach, takich jak programowanie, testowanie i środowisko produkcyjne.

## <a name="create-release-for-deployment"></a>Utwórz wydanie do wdrożenia 

Po zapisaniu wszystkich zmian możesz wybrać pozycję **Utwórz wydanie** , aby ręcznie utworzyć wydanie. Aby zautomatyzować tworzenie wydań, zobacz [Azure DevOps Release Triggers](/azure/devops/pipelines/release/triggers)

   ![Wybierz pozycję Utwórz wydanie](media/release-creation-manually.png)

## <a name="best-practices-for-cicd"></a>Najlepsze rozwiązania dotyczące ciągłej integracji/ciągłego wdrażania

Jeśli korzystasz z integracji narzędzia Git z obszarem roboczym usługi Synapse i masz potoku CI/CD, który przenosi zmiany z programowania na test, a następnie do środowiska produkcyjnego, zalecamy następujące najlepsze rozwiązania:

-   **Integracja** z usługą git. Skonfiguruj tylko obszar roboczy programu Development Synapse z integracją narzędzia Git. Zmiany dotyczące testów i produkcyjnych obszarów roboczych są wdrażane za pośrednictwem ciągłej integracji/ciągłego wdrażania, a nie potrzebna jest integracja z usługą git.
-   **Przygotuj pule przed migracją artefaktów**. Jeśli masz skrypt SQL lub Notes dołączony do pul w obszarze roboczym programowanie, oczekiwana jest taka sama nazwa pul w różnych środowiskach. 
-   **Infrastruktura jako kod (IaC)**. Zarządzanie infrastrukturą (sieciami, maszynami wirtualnymi, modułami równoważenia obciążenia i topologią połączenia) w modelu opisowym przy użyciu tej samej wersji, której zespół DevOps używa w przypadku kodu źródłowego. 
-   **Inne osoby**. Zapoznaj się z [najlepszymi rozwiązaniami dotyczącymi artefaktów ADF](../../data-factory/continuous-integration-deployment.md#best-practices-for-cicd)

## <a name="troubleshooting-artifacts-deployment"></a>Rozwiązywanie problemów z wdrażaniem artefaktów 

### <a name="use-the-synapse-workspace-deployment-task"></a>Korzystanie z zadania wdrażania obszaru roboczego Synapse

W programie Synapse istnieje wiele artefaktów, które nie są zasobami ARM. Różni się to od Azure Data Factory. Zadanie wdrażania szablonu ARM nie będzie działało prawidłowo w celu wdrożenia artefaktów Synapse
 
### <a name="unexpected-token-error-in-release"></a>Nieoczekiwany błąd tokenu w wydaniu

Gdy plik parametrów zawiera wartości parametrów, które nie są wyprowadzane, potok wersji nie będzie mógł przeanalizować pliku i wygeneruje błąd "Nieoczekiwany token". Sugerujemy przesłonięcie parametrów lub użycie usługi Azure kluczy do pobrania wartości parametrów. Można również użyć podwójnego znaku ucieczki jako obejścia.
