---
title: Zautomatyzowane publikowanie na rzecz ciągłej integracji i ciągłego dostarczania
description: Dowiedz się, jak publikować w celu automatycznego ciągłej integracji i ciągłego dostarczania.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 5730b0c7e522f7496f578ffebf716957fcaa56b0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107788937"
---
# <a name="automated-publishing-for-continuous-integration-and-delivery"></a>Zautomatyzowane publikowanie na rzecz ciągłej integracji i ciągłego dostarczania

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Omówienie

Ciągła integracja to metoda automatycznego testowania każdej zmiany wprowadzonej w bazie kodu. Tak wcześnie, jak to możliwe, ciągłe dostarczanie odbywa się zgodnie z testowaniem podczas ciągłej integracji i wypycha zmiany do systemu przejściowego lub produkcyjnego.

W Azure Data Factory ciągłej integracji i ciągłego dostarczania (CI/CD) oznacza Data Factory potoków z jednego środowiska, takiego jak tworzenie, testowanie i produkcja, do innego. Data Factory używa [Azure Resource Manager (szablonów ARM)](../azure-resource-manager/templates/overview.md) do przechowywania konfiguracji różnych jednostek Data Factory, takich jak potoki, zestawy danych i przepływy danych.

Istnieją dwie sugerowane metody promowania fabryki danych do innego środowiska:

- Wdrożenie automatyczne przy użyciu integracji Data Factory z [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines).
- Ręczne przekazywanie szablonu usługi ARM przy użyciu Data Factory środowiska użytkownika z Azure Resource Manager.

Aby uzyskać więcej informacji, zobacz [Ciągła integracja i ciągłe dostarczanie w Azure Data Factory](continuous-integration-deployment.md).

Ten artykuł koncentruje się na ulepszeniach ciągłego wdrażania i funkcji automatycznego publikowania dla ciągłej integracji/ciągłego wdrażania.

## <a name="continuous-deployment-improvements"></a>Ulepszenia ciągłego wdrażania

Funkcja automatycznego publikowania  pobiera funkcje Weryfikuj wszystkie i Eksportuj szablon **usługi ARM** z interfejsu użytkownika usługi Data Factory i udostępnia logikę za pośrednictwem publicznie dostępnego pakietu [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) npm. Z tego powodu można programowo wyzwolić te akcje, zamiast przejść do interfejsu Data Factory użytkownika i wybrać przycisk ręcznie. Ta funkcja zapewni potokom ciągłej integracji/ciągłego ciągłego integracji prawdziwe środowisko.

### <a name="current-cicd-flow"></a>Bieżący przepływ ci/CD

1. Każdy użytkownik wprowadza zmiany w swoich prywatnych gałęziach.
1. Wypychanie do wzorca jest niedozwolone. Użytkownicy muszą utworzyć żądanie ściągnięć, aby wprowadzić zmiany.
1. Użytkownicy muszą załadować interfejs Data Factory  użytkownika i wybrać pozycję Publikuj, aby wdrożyć zmiany w Data Factory i wygenerować szablony usługi ARM w gałęzi publikowania.
1. Potok wydania DevOps jest skonfigurowany do tworzenia nowego wydania i wdrażania szablonu usługi ARM za każdym razem, gdy nowa zmiana jest wypychana do gałęzi publikowania.

![Diagram przedstawiający bieżący przepływ ci/CD.](media/continuous-integration-deployment-improvements/current-ci-cd-flow.png)

### <a name="manual-step"></a>Krok ręczny

W bieżącym przepływie CI/CD środowisko użytkownika jest pośrednikiem podczas tworzenia szablonu usługi ARM. W związku z tym użytkownik musi przejść do interfejsu  użytkownika usługi Data Factory i ręcznie wybrać pozycję Publikuj, aby rozpocząć generowanie szablonu usługi ARM i upuścić go w gałęzi publikowania.

### <a name="the-new-cicd-flow"></a>Nowy przepływ ci/CD

1. Każdy użytkownik wprowadza zmiany w swoich prywatnych gałęziach.
1. Wypychanie do wzorca jest niedozwolone. Użytkownicy muszą utworzyć żądanie ściągnięć, aby wprowadzić zmiany.
1. Kompilacja Azure DevOps potoku jest wyzwalana za każdym razem, gdy do wzorca jest dokonywane nowe zatwierdzenie. Weryfikuje ona zasoby i generuje szablon usługi ARM jako artefakt, jeśli weryfikacja zakończy się pomyślnie.
1. Potok wydania DevOps jest skonfigurowany do tworzenia nowego wydania i wdrażania szablonu usługi ARM za każdym razem, gdy jest dostępna nowa kompilacja.

![Diagram przedstawiający nowy przepływ ci/CD.](media/continuous-integration-deployment-improvements/new-ci-cd-flow.png)

### <a name="what-changed"></a>Co się zmieniło?

- Mamy teraz proces kompilacji, który używa potoku kompilacji DevOps.
- Potok kompilacji używa pakietu NPM ADFUtilities, który zweryfikuje wszystkie zasoby i wygeneruje szablony usługi ARM. Te szablony mogą być pojedyncze i połączone.
- Potok kompilacji jest odpowiedzialny za Data Factory zasobów i generowanie szablonu usługi ARM zamiast interfejsu Data Factory użytkownika **(przycisk Publikuj).**
- Definicja wydania DevOps będzie teraz korzystać z tego nowego potoku kompilacji zamiast artefaktu usługi Git.

> [!NOTE]
> Możesz nadal używać istniejącego mechanizmu, który jest gałęzią, lub nowego `adf_publish` przepływu. Oba są obsługiwane.

## <a name="package-overview"></a>Przegląd pakietu

W pakiecie są obecnie dostępne dwa polecenia:

- Eksportowanie szablonu usługi Resource Manager
- Walidacja

### <a name="export-arm-template"></a>Eksportowanie szablonu usługi Resource Manager

Uruchom `npm run start export <rootFolder> <factoryId> [outputFolder]` plik , aby wyeksportować szablon usługi ARM przy użyciu zasobów danego folderu. To polecenie uruchamia również sprawdzanie poprawności przed wygenerowaniem szablonu usługi ARM. Oto przykład:

```
npm run start export C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory ArmTemplateOutput
```

- `RootFolder` jest polem obowiązkowym, które reprezentuje miejsce Data Factory zasobów.
- `FactoryId` to pole obowiązkowe reprezentujące identyfikator Data Factory w formacie `/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>` .
- `OutputFolder` jest opcjonalnym parametrem określającym ścieżkę względną do zapisania wygenerowanego szablonu usługi ARM.
 
> [!NOTE]
> Wygenerowany szablon ARM nie jest publikowany w wersji na żywo fabryki. Wdrażanie powinno odbywać się przy użyciu potoku ci/CD.
 
### <a name="validate"></a>Walidacja

Uruchom `npm run start validate <rootFolder> <factoryId>` , aby zweryfikować wszystkie zasoby danego folderu. Oto przykład:

```
npm run start validate C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory
```

- `RootFolder` jest polem obowiązkowym, które reprezentuje miejsce Data Factory zasobów.
- `FactoryId` to pole obowiązkowe reprezentujące identyfikator Data Factory w formacie `/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>` .

## <a name="create-an-azure-pipeline"></a>Tworzenie potoku platformy Azure

Chociaż pakiety npm mogą być używane na różne sposoby, jedną z podstawowych korzyści jest korzystanie z usługi [Azure Pipeline.](https://nam06.safelinks.protection.outlook.com/?url=https:%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fget-started%2Fwhat-is-azure-pipelines%3Fview%3Dazure-devops%23:~:text%3DAzure%2520Pipelines%2520is%2520a%2520cloud%2Cit%2520available%2520to%2520other%2520users.%26text%3DAzure%2520Pipelines%2520combines%2520continuous%2520integration%2Cship%2520it%2520to%2520any%2520target.&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000268277%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=jo%2BkIvSBiz6f%2B7kmgqDN27TUWc6YoDanOxL9oraAbmA%3D&reserved=0) Przy każdym scaleniu z gałęzią współpracy można wyzwolić potok, który najpierw weryfikuje cały kod, [a](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fartifacts%2Fbuild-artifacts%3Fview%3Dazure-devops%26tabs%3Dyaml%23how-do-i-consume-artifacts&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000278113%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=dN3t%2BF%2Fzbec4F28hJqigGANvvedQoQ6npzegTAwTp1A%3D&reserved=0) następnie eksportuje szablon arm do artefaktu kompilacji, który może być zużyty przez potok wydania. Różnice między bieżącym procesem ciasnych/cd a bieżącym procesem są takie, że potok wydania będzie wskazać ten artefakt zamiast *na istniejącą `adf_publish` gałąź*.

Wykonaj następujące kroki, aby rozpocząć proces:

1.  Otwórz projekt Azure DevOps i przejdź do **potoków**. Wybierz pozycję **Nowy potok**.

    ![Zrzut ekranu przedstawiający przycisk Nowy potok.](media/continuous-integration-deployment-improvements/new-pipeline.png)
    
1.  Wybierz repozytorium, w którym chcesz zapisać skrypt YAML potoku. Zalecamy zapisanie go w folderze kompilacji w tym samym repozytorium zasobów Data Factory kompilacji. Upewnij się, że *package.jsw repozytorium* zawiera nazwę pakietu, jak pokazano w poniższym przykładzie:

    ```json
    {
        "scripts":{
            "build":"node node_modules/@microsoft/azure-data-factory-utilities/lib/index"
        },
        "dependencies":{
            "@microsoft/azure-data-factory-utilities":"^0.1.3"
        }
    } 
    ```
    
1.  Wybierz **pozycję Potok początkowy.** Jeśli plik YAML został przekazany lub scalony, jak pokazano w poniższym przykładzie, możesz również wskazać go bezpośrednio i edytować.

    ![Zrzut ekranu przedstawiający potok początkowy.](media/continuous-integration-deployment-improvements/starter-pipeline.png)

    ```yaml
    # Sample YAML file to validate and export an ARM template into a build artifact
    # Requires a package.json file located in the target repository
    
    trigger:
    - main #collaboration branch
    
    pool:
      vmImage: 'ubuntu-latest'
    
    steps:
    
    # Installs Node and the npm packages saved in your package.json file in the build
    
    - task: NodeTool@0
      inputs:
        versionSpec: '10.x'
      displayName: 'Install Node.js'
    
    - task: Npm@1
      inputs:
        command: 'install'
        workingDir: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>' #replace with the package.json folder
        verbose: true
      displayName: 'Install npm package'
    
    # Validates all of the Data Factory resources in the repository. You'll get the same validation errors as when "Validate All" is selected.
    # Enter the appropriate subscription and name for the source factory.
    
    - task: Npm@1
      inputs:
        command: 'custom'
        workingDir: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>' #replace with the package.json folder
        customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
      displayName: 'Validate'
    
    # Validate and then generate the ARM template into the destination folder, which is the same as selecting "Publish" from the UX.
    # The ARM template generated isn't published to the live version of the factory. Deployment should be done by using a CI/CD pipeline. 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        workingDir: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>' #replace with the package.json folder
        customCommand: 'run build export $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName "ArmTemplate"'
      displayName: 'Validate and Generate ARM template'
    
    # Publish the artifact to be used as a source for a release pipeline.
    
    - task: PublishPipelineArtifact@1
      inputs:
        targetPath: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>/ArmTemplate' #replace with the package.json folder
        artifact: 'ArmTemplates'
        publishLocation: 'pipeline'
    ```

1.  Wprowadź kod YAML. Zalecamy użycie pliku YAML jako punktu wyjścia.
1.  Zapisz i uruchom. Jeśli używasz pliku YAML, jest on wyzwalany za każdym razem, gdy gałąź główna jest aktualizowana.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat ciągłej integracji i ciągłego dostarczania w Data Factory:

- [Ciągła integracja i ciągłe dostarczanie w Azure Data Factory](continuous-integration-deployment.md).
