---
title: Automatyczne publikowanie na potrzeby ciągłej integracji i dostarczania
description: Dowiedz się, jak publikować na potrzeby ciągłej integracji i dostarczania automatycznie.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: b5becd8ddaf74ab2acd059054a095ce9d21c178f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100366844"
---
# <a name="automated-publishing-for-continuous-integration-and-delivery"></a>Automatyczne publikowanie na potrzeby ciągłej integracji i dostarczania

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Omówienie

Ciągła integracja to metoda testowania każdej zmiany dokonanej w bazie kodu automatycznie, jak najszybciej, jak to możliwe, ciągłe dostarczanie odbywa się przy testowaniu, które odbywa się podczas ciągłej integracji i wypycha zmiany w systemie przejściowym lub produkcyjnym.

W usłudze Azure Data Factory ciągła integracja i ciągłe dostarczanie (CI/CD) oznacza przenoszenie potoków usługi Data Factory z jednego środowiska (programistycznego, testowego, produkcyjnego) do innego. Azure Data Factory korzysta z [szablonów Azure Resource Manager](../azure-resource-manager/templates/overview.md) do przechowywania konfiguracji różnych jednostek ADF (potoków, zestawów danych, przepływów i itp.). Istnieją dwie sugerowane metody podwyższania poziomu fabryki danych do innego środowiska:

- Automatyczne wdrażanie przy użyciu integracji Data Factory z [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines).
- Ręcznie Przekaż szablon Menedżer zasobów przy użyciu integracji środowiska UX Data Factory z Azure Resource Manager.

Aby uzyskać więcej informacji, zobacz [ciągła integracja i dostarczanie w Azure Data Factory](continuous-integration-deployment.md).

W tym artykule koncentrujemy się na ulepszonych ulepszeniach wdrażania i zautomatyzowanej funkcji publikowania dla ciągłej integracji/ciągłego dostarczania.

## <a name="continuous-deployment-improvements"></a>Udoskonalenia ciągłego wdrażania

Funkcja "Automatyczne publikowanie" przyjmuje funkcje szablonu *Weryfikuj wszystkie* i Eksportuj *Azure Resource Manager (ARM)* z poziomu środowiska ADF i umożliwia korzystanie z logiki za pośrednictwem publicznie dostępnego pakietu npm [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) . Pozwala to na programowe wyzwalanie tych akcji zamiast konieczności przechodzenia do interfejsu użytkownika funkcji ADF i kliknięcia przycisku. Zapewni to potoku ciągłej integracji w ramach tej funkcji.

### <a name="current-cicd-flow"></a>Bieżący przepływ CI/CD

1. Każdy użytkownik wprowadza zmiany w gałęziach prywatnych.
2. Wypychanie do elementu głównego jest zabronione, aby wprowadzić zmiany, użytkownicy muszą utworzyć żądanie ściągnięcia.
3. Użytkownicy muszą załadować interfejs użytkownika funkcji ADF i kliknąć przycisk Publikuj, aby wdrożyć zmiany do Data Factory i wygenerować szablony ARM w rozgałęzieniu publikacji.
4. Potok wersji DevOps został skonfigurowany tak, aby utworzyć nową wersję i wdrożyć szablon ARM za każdym razem, gdy Nowa zmiana jest wypychana do gałęzi publikowania.

![Bieżący przepływ CI/CD](media/continuous-integration-deployment-improvements/current-ci-cd-flow.png)

### <a name="manual-step"></a>Krok ręczny

W bieżącym przepływie ciągłej integracji/ciągłego wdrażania środowisko użytkownika jest pośrednią, aby utworzyć szablon ARM, w związku z czym użytkownik musi przejść do interfejsu użytkownika funkcji ADF i ręcznie kliknąć przycisk Publikuj, aby rozpocząć generowanie szablonu ARM i usunąć go w rozgałęzieniu publikowania, który jest bitem hakerów.

### <a name="the-new-cicd-flow"></a>Nowy przepływ CI/CD

1. Każdy użytkownik wprowadza zmiany w gałęziach prywatnych.
2. Wypychanie do elementu głównego jest zabronione, aby wprowadzić zmiany, użytkownicy muszą utworzyć żądanie ściągnięcia.
3. **Kompilacja potoku usługi Azure DevOps jest wyzwalana za każdym razem, gdy zostanie wykonane nowe zatwierdzenie do serwera głównego, sprawdza poprawność zasobów i generuje szablon ARM jako artefakt, jeśli Walidacja powiedzie się.**
4. Potok wersji DevOps został skonfigurowany tak, aby utworzyć nową wersję i wdrożyć szablon ARM za każdym razem, gdy nowa kompilacja jest dostępna. 

![Nowy przepływ CI/CD](media/continuous-integration-deployment-improvements/new-ci-cd-flow.png)

### <a name="what-changed"></a>Co się zmieniło?

- Mamy teraz proces kompilacji korzystający z potoku kompilacji DevOps.
- Potok kompilacji używa pakietu ADFUtilities NPM, który będzie sprawdzać poprawność wszystkich zasobów i generować szablony ARM (szablony pojedyncze i połączone).
- Potok kompilacji będzie odpowiedzialny za Weryfikowanie zasobów ADF i generowanie szablonu ARM zamiast interfejsu użytkownika funkcji ADF (przycisk Publikuj).
- Definicja wydania DevOps będzie teraz korzystać z tego nowego potoku kompilacji zamiast artefaktu git.

> [!NOTE]
> Można nadal używać istniejącego mechanizmu (adf_publish Branch) lub użyć nowego przepływu. Oba są obsługiwane. 

## <a name="package-overview"></a>Przegląd pakietu

W pakiecie są obecnie dostępne dwa polecenia:
- Eksportowanie szablonu usługi Resource Manager
- Walidacja

### <a name="export-arm-template"></a>Eksportowanie szablonu usługi Resource Manager

Uruchom npm Uruchom eksport <rootFolder> <factoryId> [outputFolder], aby wyeksportować szablon ARM przy użyciu zasobów danego folderu. To polecenie uruchamia sprawdzanie poprawności, a także przed wygenerowaniem szablonu ARM. Poniżej znajduje się przykład:

```
npm run start export C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory ArmTemplateOutput
```

- RootFolder to pole wymagane, które reprezentuje miejsce, w którym znajdują się zasoby Data Factory.
- FactoryId to obowiązkowe pole, które reprezentuje identyfikator zasobu fabryki danych w formacie: "/subscriptions/ <subId> /ResourceGroups/ <rgName> /providers/Microsoft.DataFactory/Factories/ <dfName> ".
- OutputFolder jest opcjonalnym parametrem, który określa ścieżkę względną do zapisania wygenerowanego szablonu ARM.
 
> [!NOTE]
> Wygenerowany szablon ARM nie jest publikowany w `Live` wersji fabryki. Wdrożenie należy wykonać przy użyciu potoku ciągłej integracji/ciągłego wdrażania. 
 

### <a name="validate"></a>Walidacja

Uruchom sprawdzanie poprawności uruchomienia npm <rootFolder> <factoryId> , aby sprawdzić poprawność wszystkich zasobów danego folderu. Poniżej znajduje się przykład:
    
```
npm run start validate C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory
```

- RootFolder to pole wymagane, które reprezentuje miejsce, w którym znajdują się zasoby Data Factory.
- FactoryId to obowiązkowe pole, które reprezentuje identyfikator zasobu fabryki danych w formacie: "/subscriptions/ <subId> /ResourceGroups/ <rgName> /providers/Microsoft.DataFactory/Factories/ <dfName> ".


## <a name="create-an-azure-pipeline"></a>Tworzenie potoku platformy Azure

Pakiety npm mogą być używane na różne sposoby, ale jedna z głównych korzyści jest używana za pośrednictwem [potoku platformy Azure](https://nam06.safelinks.protection.outlook.com/?url=https:%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fget-started%2Fwhat-is-azure-pipelines%3Fview%3Dazure-devops%23:~:text%3DAzure%2520Pipelines%2520is%2520a%2520cloud%2Cit%2520available%2520to%2520other%2520users.%26text%3DAzure%2520Pipelines%2520combines%2520continuous%2520integration%2Cship%2520it%2520to%2520any%2520target.&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000268277%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=jo%2BkIvSBiz6f%2B7kmgqDN27TUWc6YoDanOxL9oraAbmA%3D&reserved=0). Na każdym scalaniu do gałęzi współpracy można wyzwolić potok, który najpierw sprawdza wszystkie kod, a następnie eksportuje szablon ARM do [artefaktu kompilacji](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fartifacts%2Fbuild-artifacts%3Fview%3Dazure-devops%26tabs%3Dyaml%23how-do-i-consume-artifacts&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000278113%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=dN3t%2BF%2Fzbec4F28hJqigGANvvedQoQ6npzegTAwTp1A%3D&reserved=0) , który może być używany przez potok wersji. **Różnice między bieżącym procesem ciągłej integracji/ciągłego wdrażania polega na tym, że potoku wydania w tym artefaktie zamiast istniejącej `adf_publish` gałęzi.**

Wykonaj poniższe czynności, aby rozpocząć pracę:

1.  Otwórz projekt usługi Azure DevOps i przejdź do pozycji "potoki". Wybierz pozycję "nowy potok".

    ![Nowy potok](media/continuous-integration-deployment-improvements/new-pipeline.png)
    
2.  Wybierz repozytorium, w którym chcesz zapisać skrypt YAML potoku. Zalecamy zapisanie go w folderze *kompilacji* w ramach tego samego REPOZYTORIUM zasobów ADF. Upewnij się, że istnieje **package.jsw** pliku w repozytorium, a także zawiera nazwę pakietu (jak pokazano w poniższym przykładzie).

    ```json
    {
        "scripts":{
            "build":"node node_modules/@microsoft/azure-data-factory-utilities/lib/index"
        },
        "dependencies":{
            "@microsoft/azure-data-factory-utilities":"^0.1.2"
        }
    } 
    ```
    
3.  Wybierz pozycję *potok początkowy*. Jeśli plik YAML został przekazany lub scalony (jak pokazano w poniższym przykładzie), możesz również bezpośrednio wskazać go i edytować. 

    ![Potok początkowy](media/continuous-integration-deployment-improvements/starter-pipeline.png) 

    ```yaml
    # Sample YAML file to validate and export an ARM template into a Build Artifact
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
    
    # Validates all of the ADF resources in the repository. You will get the same validation errors as when "Validate All" is clicked
    # Enter the appropriate subscription and name for the source factory 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
      displayName: 'Validate'
    
    # Validate and then generate the ARM template into the destination folder. Same as clicking "Publish" from UX
    # The ARM template generated is not published to the ‘Live’ version of the factory. Deployment should be done using a CI/CD pipeline. 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build export $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName "ArmTemplate"'
      displayName: 'Validate and Generate ARM template'
    
    # Publish the Artifact to be used as a source for a release pipeline
    
    - task: PublishPipelineArtifact@1
      inputs:
        targetPath: '$(Build.Repository.LocalPath)/ArmTemplate'
        artifact: 'ArmTemplates'
        publishLocation: 'pipeline'
    ```

4.  Wprowadź kod YAML. Zalecamy pobranie pliku YAML i użycie go jako punktu początkowego.
5.  Zapisz i Uruchom. Jeśli jest używany YAML, będzie on wyzwalany za każdym razem, gdy gałąź "główna" zostanie zaktualizowana.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o ciągłej integracji i dostarczaniu w Data Factory: 

- [Ciągła integracja i dostarczanie w Azure Data Factory](continuous-integration-deployment.md).
