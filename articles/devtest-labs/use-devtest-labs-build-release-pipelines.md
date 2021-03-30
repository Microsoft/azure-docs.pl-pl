---
title: Używanie usługi DevTest Labs w potokach kompilacji i wydań usługi Azure Pipelines
description: Dowiedz się, jak używać Azure DevTest Labs w Azure Pipelines kompilacjach i wydaniach.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: d04ed5dd7bebac0c8f24deb9145c3d2e4b77122e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "88080338"
---
# <a name="use-devtest-labs-in-azure-pipelines-build-and-release-pipelines"></a>Używanie usługi DevTest Labs w potokach kompilacji i wydań usługi Azure Pipelines
Ten artykuł zawiera informacje dotyczące sposobu używania DevTest Labs w Azure Pipelines potoków kompilowania i wydawania. 

## <a name="overall-flow"></a>Przepływ ogólny
Podstawowym przepływem jest utworzenie **potoku kompilacji** , który wykonuje następujące zadania:

1. Skompiluj kod aplikacji.
1. Utwórz środowisko podstawowe w DevTest Labs.
1. Zaktualizuj środowisko przy użyciu informacji niestandardowych.
1. Wdrażanie aplikacji w środowisku DevTest Labs
1. Przetestuj kod. 

Po pomyślnym zakończeniu kompilacji **potok wersji** użyje artefaktów kompilacji do wdrożenia przemieszczania lub produkcji. 

Jednym z niezbędnych miejsc jest to, że wszystkie informacje potrzebne do odtworzenia testowanego ekosystemu są dostępne w ramach artefaktów kompilacji, w tym konfiguracji zasobów platformy Azure. Ponieważ zasoby platformy Azure ponoszą koszt w przypadku użycia, firmy chcą kontrolować lub śledzić użycie tych zasobów. W niektórych sytuacjach Azure Resource Manager szablony służące do tworzenia i konfigurowania zasobów mogą być zarządzane przez inny dział, taki jak. Szablony te mogą być przechowywane w innym repozytorium. Prowadzi do interesującej sytuacji, w której kompilacja zostanie utworzona i przetestowana, a zarówno kod, jak i konfiguracja muszą być przechowywane w artefaktach kompilacji, aby prawidłowo odtworzyć system w środowisku produkcyjnym. 

Korzystając z DevTest Labs w fazie kompilowania/testowania, można dodać szablony Azure Resource Manager i pliki pomocnicze do źródeł kompilacji, aby w fazie wydania dokładna konfiguracja użyta do przetestowania została wdrożona w środowisku produkcyjnym. Zadanie **tworzenia środowiska Azure DevTest Labs** z odpowiednią konfiguracją spowoduje zapisanie Menedżer zasobów szablonów w ramach artefaktów kompilacji. Na potrzeby tego przykładu będziesz używać kodu z [samouczka: Tworzenie aplikacji sieci Web platformy .NET Core i SQL Database w usłudze Azure App Service](../app-service/tutorial-dotnetcore-sqldb-app.md), aby wdrożyć i przetestować aplikację sieci Web na platformie Azure.

![Przepływ ogólny](./media/use-devtest-labs-build-release-pipelines/overall-flow.png)

## <a name="set-up-azure-resources"></a>Konfigurowanie zasobów platformy Azure
Istnieje kilka elementów, które muszą zostać utworzone wcześniej:

- Dwa repozytoria. Pierwszym z nich jest kod z samouczka i szablon Menedżer zasobów z dwoma dodatkowymi maszynami wirtualnymi. Druga z nich będzie zawierać podstawowy szablon Azure Resource Manager (istniejąca konfiguracja).
- Grupa zasobów służąca do wdrażania kodu produkcyjnego i konfiguracji.
- Laboratorium należy skonfigurować przy użyciu [połączenia z repozytorium konfiguracji](devtest-lab-create-environment-from-arm.md) dla potoku kompilacji. Szablon Menedżer zasobów należy zaewidencjonować do repozytorium konfiguracji jako azuredeploy.jsna metadata.jsw celu umożliwienia DevTest laboratoriom rozpoznawania i wdrażania szablonu.

Potok kompilacji utworzy środowisko DevTest Labs i Wdróż kod do testowania.

## <a name="set-up-a-build-pipeline"></a>Konfigurowanie potoku kompilacji
W Azure Pipelines Utwórz potok kompilacji przy użyciu kodu z [samouczka: Tworzenie aplikacji internetowej platformy .NET Core i SQL Database w Azure App Service](../app-service/tutorial-dotnetcore-sqldb-app.md). Użyj szablonu **ASP.NET Core** , który wypełni zadanie niezbędne do skompilowania, przetestowania i opublikowania kodu.

![Wybierz szablon ASP.NET](./media/use-devtest-labs-build-release-pipelines/select-asp-net.png)

Musisz dodać trzy dodatkowe zadania, aby utworzyć środowisko w DevTest Labs i wdrożyć je w środowisku.

![Potok z trzema zadaniami](./media/use-devtest-labs-build-release-pipelines/pipeline-tasks.png)

### <a name="create-environment-task"></a>Tworzenie zadania środowiska
W zadanie tworzenia środowiska (**Azure DevTest Labs tworzenia środowiska** ) Użyj list rozwijanych, aby wybrać następujące wartości:

- Subskrypcja platformy Azure
- Nazwa laboratorium
- Nazwa repozytorium
- Nazwa szablonu (który pokazuje folder, w którym jest przechowywane środowisko). 

Zalecamy używanie list rozwijanych na stronie zamiast ręcznego wprowadzania informacji. Jeśli ręcznie wprowadzisz informacje, wprowadź w pełni kwalifikowane identyfikatory zasobów platformy Azure. Zadanie wyświetla przyjazne nazwy zamiast identyfikatorów zasobów. 

Nazwa środowiska to wyświetlana nazwa wyświetlana w DevTest Labs. Powinna to być unikatowa nazwa dla każdej kompilacji. Na przykład: **TestEnv $ (Build. buildID)**. 

Możesz określić plik lub parametry parametrów, aby przekazać informacje do szablonu Menedżer zasobów. 

Wybierz opcję **Utwórz zmienne wyjściowe na podstawie szablonu środowiska wyjście** i wprowadź nazwę odwołania. W tym przykładzie wprowadź **BaseEnv** dla nazwy odwołania. Ta BaseEnv będzie używana podczas konfigurowania następnego zadania. 

![Utwórz zadanie środowiska Azure DevTest Labs](./media/use-devtest-labs-build-release-pipelines/create-environment.png)

### <a name="populate-environment-task"></a>Wypełnij zadanie środowiska
Drugie zadanie (**Azure DevTest Labs wypełnienie środowiska** ) polega na aktualizacji istniejącego środowiska DevTest Labs. Zadanie tworzenia środowiska wyprowadza **BaseEnv. environmentResourceId** , które służy do konfigurowania nazwy środowiska dla tego zadania. Szablon Menedżer zasobów w tym przykładzie ma dwa parametry- **adminUserName** i **adminPassword**. 

![Wypełnij zadanie środowiska Azure DevTest Labs](./media/use-devtest-labs-build-release-pipelines/populate-environment.png)

## <a name="app-service-deploy-task"></a>Zadanie wdrażania App Service
Trzecim zadaniem jest zadanie **Azure App Service Deploy** . Typ aplikacji to **aplikacja sieci Web** , a nazwa App Service jest ustawiona na wartość **$ (witryna sieci Web)**.

![Zadanie wdrażania App Service](./media/use-devtest-labs-build-release-pipelines/app-service-deploy.png)

## <a name="set-up-release-pipeline"></a>Konfigurowanie potoku wydania
Tworzysz potok wersji z dwoma zadaniami: **Azure Deployment: Utwórz lub Zaktualizuj grupę zasobów** i **Wdróż Azure App Service**. 

Dla pierwszego zadania Określ nazwę i lokalizację grupy zasobów. Lokalizacja szablonu to połączony artefakt. Jeśli szablon Menedżer zasobów obejmuje połączone szablony, należy zaimplementować wdrożenie niestandardowej grupy zasobów. Szablon znajduje się w opublikowanym artefaktie drop. Przesłoń parametry szablonu dla szablonu Menedżer zasobów. Pozostałe ustawienia można pozostawić z wartościami domyślnymi. 

W przypadku drugiego zadania **wdrażania Azure App Service** Określ subskrypcję platformy Azure, wybierz pozycję **aplikacja sieci Web** dla **typu aplikacji**, a dla **nazwy App Service** wartość **$ (witryna internetowa)** . Pozostałe ustawienia można pozostawić z wartościami domyślnymi. 

## <a name="test-run"></a>Przebieg testowy
Teraz, gdy oba potoki zostały skonfigurowane, należy ręcznie utworzyć w kolejce kompilację i zobaczyć jej działanie. Następnym krokiem jest ustawienie odpowiedniego wyzwalacza dla kompilacji i połączenie kompilacji z potokiem wersji.

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [Integracja Azure DevTest Labs z Azure Pipelines ciągłą integracją i potoku dostarczania](devtest-lab-integrate-ci-cd.md)
- [Integruj środowiska w Azure Pipelines potoki ciągłej integracji/ciągłego wdrażania](integrate-environments-devops-pipeline.md)
