---
title: Automatyczne publikowanie na potrzeby ciągłej integracji i dostarczania
description: Dowiedz się, jak publikować na potrzeby ciągłej integracji i dostarczania automatycznie.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 496d2b6b3d669013c8174e9bc961d0a2f640bed3
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462086"
---
# <a name="automated-publishing-for-continuous-integration-and-delivery"></a>Automatyczne publikowanie na potrzeby ciągłej integracji i dostarczania

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Omówienie

Ciągła integracja to metoda automatycznego testowania każdej zmiany wprowadzonej w bazie kodu. Jak najszybciej, ciągłe dostarczanie korzysta z testów, które odbywa się podczas ciągłej integracji i wypycha zmiany do systemu przejściowego lub produkcyjnego.

W Azure Data Factory, ciągła integracja i ciągłe dostarczanie (CI/CD) oznacza przemieszczenie Data Factory potoków z jednego środowiska, takiego jak programowanie, testowanie i produkcja, na inne. Data Factory używa [szablonów Azure Resource Manager (szablony ARM)](../azure-resource-manager/templates/overview.md) do przechowywania konfiguracji różnych Data Factory jednostek, takich jak potoki, zestawy danych i przepływy.

Istnieją dwie sugerowane metody podwyższania poziomu fabryki danych do innego środowiska:

- Automatyczne wdrażanie przy użyciu integracji Data Factory z [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines).
- Ręczne przekazywanie szablonu ARM przy użyciu Data Factory integracji środowiska użytkownika z Azure Resource Manager.

Aby uzyskać więcej informacji, zobacz [ciągła integracja i dostarczanie w Azure Data Factory](continuous-integration-deployment.md).

Ten artykuł koncentruje się na ulepszonych wdrożeniach i funkcji automatycznego publikowania dla ciągłej integracji/ciągłego wdrażania.

## <a name="continuous-deployment-improvements"></a>Udoskonalenia ciągłego wdrażania

Funkcja automatycznego publikowania przyjmuje funkcje " **Weryfikuj wszystkie** i **Eksportuj" w szablonie ARM** ze środowiska użytkownika Data Factory i umożliwia używanie logiki za pośrednictwem publicznie dostępnego pakietu npm [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) . Z tego powodu można programowo wyzwolić te akcje zamiast przejść do interfejsu użytkownika Data Factory i ręcznie wybrać przycisk. Ta funkcja umożliwi potoku ciągłej integracji w ramach tej funkcji.

### <a name="current-cicd-flow"></a>Bieżący przepływ CI/CD

1. Każdy użytkownik wprowadza zmiany w gałęziach prywatnych.
1. Wypychanie do wzorca nie jest dozwolone. Aby wprowadzić zmiany, użytkownicy muszą utworzyć żądanie ściągnięcia.
1. Użytkownicy muszą załadować interfejs użytkownika Data Factory i wybrać pozycję **Publikuj** , aby wdrożyć zmiany Data Factory i wygenerować szablony ARM w rozgałęzieniu publikacji.
1. Potok wersji DevOps został skonfigurowany tak, aby utworzyć nową wersję i wdrożyć szablon ARM za każdym razem, gdy Nowa zmiana jest wypychana do gałęzi publikowania.

![Diagram przedstawiający bieżący przepływ CI/CD.](media/continuous-integration-deployment-improvements/current-ci-cd-flow.png)

### <a name="manual-step"></a>Krok ręczny

W bieżącym przepływie ciągłej integracji/ciągłego wdrażania środowisko użytkownika jest pośrednią, aby utworzyć szablon ARM. W związku z tym użytkownik musi przejść do interfejsu użytkownika Data Factory i ręcznie wybrać pozycję **Publikuj** , aby rozpocząć generowanie szablonu ARM i usunąć go w rozgałęzieniu publikacji.

### <a name="the-new-cicd-flow"></a>Nowy przepływ CI/CD

1. Każdy użytkownik wprowadza zmiany w gałęziach prywatnych.
1. Wypychanie do wzorca nie jest dozwolone. Aby wprowadzić zmiany, użytkownicy muszą utworzyć żądanie ściągnięcia.
1. Kompilacja potoku platformy Azure DevOps jest wyzwalana za każdym razem, gdy zostanie wykonane nowe zatwierdzenie do serwera głównego. Sprawdza poprawność zasobów i generuje szablon ARM jako artefakt, jeśli Walidacja powiedzie się.
1. Potok wersji DevOps został skonfigurowany tak, aby utworzyć nową wersję i wdrożyć szablon ARM za każdym razem, gdy nowa kompilacja jest dostępna.

![Diagram przedstawiający nowy przepływ CI/CD.](media/continuous-integration-deployment-improvements/new-ci-cd-flow.png)

### <a name="what-changed"></a>Co się zmieniło?

- Mamy teraz proces kompilacji, który używa potoku kompilacji DevOps.
- Potok kompilacji używa pakietu NPM ADFUtilities, który będzie sprawdzać poprawność wszystkich zasobów i generować szablony ARM. Te szablony mogą być pojedyncze i połączone.
- Potok kompilacji jest odpowiedzialny za sprawdzanie poprawności zasobów Data Factory i generowanie szablonu ARM zamiast Data Factory interfejsu użytkownika (przycisk **Publikuj** ).
- Definicja wydania DevOps będzie teraz korzystać z tego nowego potoku kompilacji zamiast artefaktu git.

> [!NOTE]
> Można nadal korzystać z istniejącego mechanizmu, który jest `adf_publish` gałęzią, lub użyć nowego przepływu. Oba są obsługiwane.

## <a name="package-overview"></a>Przegląd pakietu

Dwa polecenia są obecnie dostępne w pakiecie:

- Eksportowanie szablonu usługi Resource Manager
- Walidacja

### <a name="export-arm-template"></a>Eksportowanie szablonu usługi Resource Manager

Uruchom `npm run start export <rootFolder> <factoryId> [outputFolder]` , aby wyeksportować szablon ARM przy użyciu zasobów danego folderu. To polecenie uruchamia również sprawdzanie poprawności przed wygenerowaniem szablonu ARM. Oto przykład:

```
npm run start export C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory ArmTemplateOutput
```

- `RootFolder` to pole obowiązkowe, które reprezentuje miejsce, w którym znajdują się Data Factory zasoby.
- `FactoryId` to pole obowiązkowe, które reprezentuje Data Factory identyfikator zasobu w formacie `/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>` .
- `OutputFolder` jest opcjonalnym parametrem, który określa ścieżkę względną do zapisania wygenerowanego szablonu ARM.
 
> [!NOTE]
> Wygenerowany szablon ARM nie jest publikowany w działającej wersji fabryki. Wdrożenie powinno odbywać się przy użyciu potoku ciągłej integracji/ciągłego wdrażania.
 
### <a name="validate"></a>Walidacja

Uruchom `npm run start validate <rootFolder> <factoryId>` , aby sprawdzić poprawność wszystkich zasobów danego folderu. Oto przykład:

```
npm run start validate C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory
```

- `RootFolder` to pole obowiązkowe, które reprezentuje miejsce, w którym znajdują się Data Factory zasoby.
- `FactoryId` to pole obowiązkowe, które reprezentuje Data Factory identyfikator zasobu w formacie `/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>` .

## <a name="create-an-azure-pipeline"></a>Tworzenie potoku platformy Azure

Pakiety npm mogą być używane na różne sposoby, ale jedna z głównych korzyści jest używana za pośrednictwem [potoku platformy Azure](https://nam06.safelinks.protection.outlook.com/?url=https:%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fget-started%2Fwhat-is-azure-pipelines%3Fview%3Dazure-devops%23:~:text%3DAzure%2520Pipelines%2520is%2520a%2520cloud%2Cit%2520available%2520to%2520other%2520users.%26text%3DAzure%2520Pipelines%2520combines%2520continuous%2520integration%2Cship%2520it%2520to%2520any%2520target.&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000268277%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=jo%2BkIvSBiz6f%2B7kmgqDN27TUWc6YoDanOxL9oraAbmA%3D&reserved=0). Na każdym scalaniu do gałęzi współpracy można wyzwolić potok, który najpierw sprawdza wszystkie kod, a następnie eksportuje szablon ARM do [artefaktu kompilacji](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fartifacts%2Fbuild-artifacts%3Fview%3Dazure-devops%26tabs%3Dyaml%23how-do-i-consume-artifacts&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000278113%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=dN3t%2BF%2Fzbec4F28hJqigGANvvedQoQ6npzegTAwTp1A%3D&reserved=0) , który może być używany przez potok wersji. Różnice między bieżącym procesem ciągłej integracji/ciągłego wdrażania polega na tym, że *potoku wydania w tym artefaktie zamiast istniejącej `adf_publish` gałęzi*.

Wykonaj następujące kroki, aby rozpocząć proces:

1.  Otwórz projekt usługi Azure DevOps, a następnie przejdź do pozycji **potoki**. Wybierz pozycję **Nowy potok**.

    ![Zrzut ekranu pokazujący przycisk nowe potoku.](media/continuous-integration-deployment-improvements/new-pipeline.png)
    
1.  Wybierz repozytorium, w którym chcesz zapisać skrypt YAML potoku. Zalecamy zapisanie go w folderze kompilacji w tym samym repozytorium zasobów Data Factory. Upewnij się, że w repozytorium zawierającym nazwę pakietu znajduje się *package.js* w pliku, jak pokazano w następującym przykładzie:

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
    
1.  Wybierz pozycję **potok początkowy**. Jeśli plik YAML został przekazany lub scalony, jak pokazano w poniższym przykładzie, można również bezpośrednio wskazać go i edytować.

    ![Zrzut ekranu przedstawiający początkowy potok.](media/continuous-integration-deployment-improvements/starter-pipeline.png)

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
        verbose: true
      displayName: 'Install npm package'
    
    # Validates all of the Data Factory resources in the repository. You'll get the same validation errors as when "Validate All" is selected.
    # Enter the appropriate subscription and name for the source factory.
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
      displayName: 'Validate'
    
    # Validate and then generate the ARM template into the destination folder, which is the same as selecting "Publish" from the UX.
    # The ARM template generated isn't published to the live version of the factory. Deployment should be done by using a CI/CD pipeline. 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build export $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName "ArmTemplate"'
      displayName: 'Validate and Generate ARM template'
    
    # Publish the artifact to be used as a source for a release pipeline.
    
    - task: PublishPipelineArtifact@1
      inputs:
        targetPath: '$(Build.Repository.LocalPath)/ArmTemplate'
        artifact: 'ArmTemplates'
        publishLocation: 'pipeline'
    ```

1.  Wprowadź kod YAML. Zalecamy używanie pliku YAML jako punktu początkowego.
1.  Zapisz i Uruchom. Jeśli użyto YAML, jest on wyzwalany za każdym razem, gdy główna gałąź zostanie zaktualizowana.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o ciągłej integracji i dostarczaniu w Data Factory:

- [Ciągła integracja i dostarczanie w Azure Data Factory](continuous-integration-deployment.md).
