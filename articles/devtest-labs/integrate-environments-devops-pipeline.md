---
title: Integruj środowiska w Azure Pipelines w Azure DevTest Labs
description: Dowiedz się, jak zintegrować środowiska Azure DevTest Labs z potokiem ciągłej integracji (CI) DevOps (CI) i ciągłe dostarczanie (CD).
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: be726b2a3f67fd3dada4fdc3cf794922a3c18d06
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85483027"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>Integruj środowiska z potokami CI/DevOps na platformie Azure
Możesz użyć rozszerzenia Azure DevTest Labs zadania, które jest zainstalowane w Azure DevOps Services (wcześniej znane jako Visual Studio Team Services), aby łatwo zintegrować potok "Kompilacja ciągła (CI)"/ciągłe dostarczanie (CD-and-Release) z Azure DevTest Labs. Te rozszerzenia ułatwiają szybkie wdrażanie [środowiska](devtest-lab-test-env.md) dla określonego zadania testowego, a następnie usuwanie go po zakończeniu testu. 

W tym artykule pokazano, jak utworzyć i wdrożyć środowisko, a następnie usunąć środowisko, wszystko w jednym kompletnym potoku. Zwykle wykonujesz każde z tych zadań osobno we własnym niestandardowym potoku kompilacji-test-Deploy. Rozszerzenia używane w tym artykule stanowią uzupełnienie następujących [zadań tworzenia/usuwania DTL](devtest-lab-integrate-ci-cd.md):

- Tworzenie środowiska
- Usuwanie środowiska

## <a name="before-you-begin"></a>Przed rozpoczęciem
Aby można było zintegrować potok ciągłej integracji/ciągłego wdrażania z Azure DevTest Labs, zainstaluj rozszerzenie [zadania Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) z Visual Studio Marketplace. 

## <a name="create-and-configure-the-lab-for-environments"></a>Tworzenie i Konfigurowanie laboratorium dla środowisk
W tej sekcji opisano sposób tworzenia i konfigurowania laboratorium, w którym zostanie wdrożone środowisko platformy Azure.

1. [Utwórz laboratorium](devtest-lab-create-lab.md) , jeśli jeszcze go nie masz. 
2. Skonfiguruj laboratorium i Utwórz szablon środowiska, wykonując instrukcje opisane w tym artykule: [Tworzenie środowisk wielu maszyn wirtualnych i zasobów PaaS za pomocą szablonów Azure Resource Manager](devtest-lab-create-environment-from-arm.md).
3. Na potrzeby tego przykładu Użyj istniejącego szablonu szybkiego startu platformy Azure [https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/](https://azure.microsoft.com/resources/templates/201-web-app-redis-cache-sql-database/) .
4. Skopiuj folder **201-Web-App-Redis-Cache-SQL-Database** do folderu **ArmTemplate** w repozytorium skonfigurowanym w kroku 2.

## <a name="create-a-release-definition"></a>Tworzenie definicji wydania
Aby utworzyć definicję wydania, wykonaj następujące czynności:

1.  Na karcie **wersje** w **centrum kompilacja & wydania**wybierz przycisk **znak plus (+)** .
2.  W oknie **Tworzenie definicji wydania** wybierz **pusty** szablon, a następnie wybierz przycisk **dalej**.
3.  Wybierz pozycję **Wybierz później**, a następnie wybierz pozycję **Utwórz** , aby utworzyć nową definicję wydania z jednym środowiskiem domyślnym i bez połączonych artefaktów.
4.  Aby otworzyć menu skrótów, w nowej definicji wydania, wybierz **wielokropek (...)** obok nazwy środowiska, a następnie wybierz pozycję **Konfiguruj zmienne**.
5.  W oknie **Konfigurowanie środowiska** , dla zmiennych, które są używane w zadania definicji wersji, wprowadź następujące wartości:
1.  W polu **administratorLogin**wprowadź nazwę logowania administratora SQL.
2.  W polu **administratorLoginPassword**wprowadź hasło, które ma być używane przez dane logowania administratora SQL. Aby ukryć i zabezpieczyć hasło, użyj ikony "kłódki".
3.  W polu **DatabaseName**wpisz nazwę SQL Database.
4.  Te zmienne są specyficzne dla przykładowych środowisk, ale różne środowiska mogą mieć różne zmienne.

## <a name="create-an-environment"></a>Tworzenie środowiska
Następnym etapem wdrożenia jest utworzenie środowiska, które ma być używane na potrzeby tworzenia i testowania.

1. W definicji wersji wybierz pozycję **Dodaj zadania**.
2. Na karcie **zadania** dodaj zadanie Azure DevTest Labs tworzenia środowiska. Skonfiguruj zadanie w następujący sposób:
    1. W przypadku **subskrypcji platformy Azure RM**wybierz połączenie na liście **dostępne połączenia usługi platformy Azure** lub Utwórz bardziej ograniczone uprawnienia do subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure Resource Manager punkt końcowy usługi](/azure/devops/pipelines/library/service-endpoints).
2. W polu **Nazwa laboratorium**wybierz nazwę wystąpienia, które zostało utworzone wcześniej *.
3. W polu **Nazwa repozytorium**wybierz repozytorium, w którym został wypchnięte szablon Menedżer zasobów (201) do *.
4. W polu **Nazwa szablonu**wybierz nazwę środowiska zapisanego w repozytorium kodu źródłowego *. 
5. **Nazwa laboratorium**, **Nazwa repozytorium**i **Nazwa szablonu** są przyjaznymi reprezentacjami identyfikatorów zasobów platformy Azure. Ręczne wprowadzenie przyjaznej nazwy spowoduje wystąpienie błędów, użyj list rozwijanych, aby wybrać informacje.
6. W polu **Nazwa środowiska**wprowadź nazwę, aby jednoznacznie zidentyfikować wystąpienie środowiska w laboratorium.  Musi być unikatowa w ramach laboratorium.
7. **Plik parametrów** i **Parametry**umożliwiają przekazywanie parametrów niestandardowych do środowiska. W celu ustawienia wartości parametrów można użyć obu tych metod. W tym przykładzie zostanie użyta sekcja Parameters. Użyj nazw zmiennych zdefiniowanych w środowisku, na przykład: `-administratorLogin "$(administratorLogin)" -administratorLoginPassword "$(administratorLoginPassword)" -databaseName "$(databaseName)" -cacheSKUCapacity 1`
8. Informacje w szablonie środowiska mogą być przekazywane przez sekcję wyjściową szablonu. Zaznacz opcję **Utwórz zmienne wyjściowe w oparciu o dane wyjściowe szablonu środowiska** , aby inne zadania mogły korzystać z danych. `$(Reference name.Output Name)` jest wzorcem do wykonania. Na przykład jeśli nazwa odwołania to DTL, a nazwa wyjściowa w szablonie była lokalizacją, zmienna byłaby `$(DTL.location)` .

## <a name="delete-the-environment"></a>Usuwanie środowiska
Ostatnim etapem jest usunięcie środowiska wdrożonego w wystąpieniu Azure DevTest Labs. Zwykle należy usunąć środowisko po wykonaniu zadań deweloperskich lub przeprowadzeniu testów, które są potrzebne w wdrożonych zasobach.

W definicji wersji wybierz pozycję **Dodaj zadania**, a następnie na karcie **wdrażanie** Dodaj zadanie **Azure DevTest Labs usuwanie środowiska** . Skonfiguruj go w następujący sposób:

1. Aby usunąć maszynę wirtualną, zobacz [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks):
    1. W przypadku **subskrypcji platformy Azure RM**wybierz połączenie na liście **dostępne połączenia usługi platformy Azure** lub Utwórz bardziej ograniczone uprawnienia do subskrypcji platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure Resource Manager punkt końcowy usługi](/azure/devops/pipelines/library/service-endpoints).
    2. W polu **Nazwa laboratorium**wybierz laboratorium, w którym istnieje środowisko.
    3. W polu **Nazwa środowiska**wprowadź nazwę środowiska, które ma zostać usunięte.
2. Wprowadź nazwę definicji wersji, a następnie zapisz ją.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły: 
- [Twórz środowiska z obsługą wielu maszyn wirtualnych za pomocą szablonów Menedżer zasobów](devtest-lab-create-environment-from-arm.md).
- Przewodnik Szybki Start dotyczący Menedżer zasobów szablonów dla automatyzacji DevTest Labs z [repozytorium DevTest Labs](https://github.com/Azure/azure-quickstart-templates)w witrynie GitHub.
- [Strona rozwiązywania problemów z VSTS](/azure/devops/pipelines/troubleshooting)

