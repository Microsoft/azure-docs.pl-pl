---
title: Metodyka DevOps dla potoku pozyskiwania danych
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak stosować praktyki DevOps w celu utworzenia potoku pozyskiwania danych w celu przygotowania danych przy użyciu Azure Data Factory i Azure Databricks.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 06/23/2020
ms.openlocfilehash: 8f229c52b62c740c9d955f745a6922e59163b907
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93348563"
---
# <a name="devops-for-a-data-ingestion-pipeline"></a>Metodyka DevOps dla potoku pozyskiwania danych

W większości scenariuszy rozwiązanie do pozyskiwania danych to kompozycja skryptów, wywołań usługi i potoku, który organizuje wszystkie działania. W tym artykule dowiesz się, jak stosować praktyki DevOps do cyklu życia wspólnego potoku pozyskiwania danych, który przygotowuje dane do szkolenia modelu uczenia maszynowego. Potok jest tworzony przy użyciu następujących usług platformy Azure:

* __Azure Data Factory__ : odczytuje dane pierwotne i organizuje przygotowanie danych.
* __Azure Databricks__ : uruchamia Notes języka Python, który przekształca dane.
* __Azure Pipelines__ : automatyzuje ciągłą integrację i proces tworzenia.

## <a name="data-ingestion-pipeline-workflow"></a>Przepływ pracy potoku pozyskiwania danych

Potok pozyskiwania danych implementuje następujący przepływ pracy:

1. Nieprzetworzone dane są odczytywane w potoku Azure Data Factory (ADF).
1. Potok ADF wysyła dane do klastra Azure Databricks, w którym jest uruchamiany Notes Python służący do przekształcania danych.
1. Dane są przechowywane w kontenerze obiektów blob, gdzie mogą być używane przez Azure Machine Learning do uczenia modelu.

![przepływ pracy potoku pozyskiwania danych](media/how-to-cicd-data-ingestion/data-ingestion-pipeline.png)

## <a name="continuous-integration-and-delivery-overview"></a>Przegląd ciągłej integracji i dostarczania

Podobnie jak w przypadku wielu rozwiązań programowych, istnieje zespół (na przykład inżynierowie danych) pracujący nad nim. Współpracują i udostępniają te same zasoby platformy Azure, takie jak Azure Data Factory, Azure Databricks i konta usługi Azure Storage. Zbieranie tych zasobów to środowisko programistyczne. Inżynierowie danych składają się na tę samą bazę kodu źródłowego.

Ciągły system integracji i dostarczania automatyzuje proces kompilowania, testowania i dostarczania (wdrażania) rozwiązania. Proces ciągłej integracji (CI) wykonuje następujące zadania:

* Składa kod
* Sprawdza przy użyciu testów jakości kodu
* Uruchamia testy jednostkowe
* Tworzy artefakty, takie jak testowany kod i szablony Azure Resource Manager

Proces ciągłego dostarczania (CD) wdraża artefakty w środowiskach podrzędnych.

![Diagram pozyskiwania danych cicd](media/how-to-cicd-data-ingestion/cicd-data-ingestion.png)

W tym artykule przedstawiono sposób automatyzacji procesów CI i dysków CD przy użyciu [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/).

## <a name="source-control-management"></a>Zarządzanie kontrolą źródła

Zarządzanie kontrolą źródła jest konieczne do śledzenia zmian i zapewnienia współpracy między członkami zespołu.
Na przykład kod będzie przechowywany w repozytorium Azure DevOps, GitHub lub GitLab. Przepływ pracy współpracy jest oparty na modelu rozgałęziania. Na przykład [GitFlow](https://datasift.github.io/gitflow/IntroducingGitFlow.html).

### <a name="python-notebook-source-code"></a>Kod źródłowy notesu Python

Inżynierowie danych współpracują z kodem źródłowym notesu języka Python lokalnie w środowisku IDE (na przykład [Visual Studio Code](https://code.visualstudio.com)) lub bezpośrednio w obszarze roboczym datakosteks. Po zakończeniu wprowadzania zmian w kodzie zostaną one scalone z repozytorium po rozgałęzieniu zasad.

> [!TIP] 
> Zalecamy przechowywanie kodu w plikach, `.py` a nie w `.ipynb` formacie Jupyter Notebook. Zwiększa czytelność kodu i umożliwia automatyczne sprawdzanie jakości kodu w procesie CI.

### <a name="azure-data-factory-source-code"></a>Azure Data Factory kod źródłowy

Kod źródłowy potoków Azure Data Factory jest kolekcją plików JSON generowanych przez Azure Data Factory obszar roboczy. Zwykle inżynierowie danych pracują z projektantem wizualnym w obszarze roboczym Azure Data Factory, a nie bezpośrednio z plikami kodu źródłowego. 

Aby skonfigurować obszar roboczy do korzystania z repozytorium kontroli źródła, zobacz [Tworzenie z Azure Repos integrację git](../data-factory/source-control.md#author-with-azure-repos-git-integration).   

## <a name="continuous-integration-ci"></a>Ciągła integracja (CI)

Ostatecznym celem procesu ciągłej integracji jest zebranie współpracującego zespołu z kodu źródłowego i przygotowanie go do wdrożenia w środowiskach podrzędnych. Podobnie jak w przypadku zarządzania kodem źródłowym ten proces jest różny dla notesów Python i potoków Azure Data Factory. 

### <a name="python-notebook-ci"></a>Element konfiguracji notesu języka Python

Proces CI dla notesów języka Python Pobiera kod z gałęzi współpracy (na przykład * **Master** _ lub _*_opracowywać_*_ ) i wykonuje następujące działania: _ Code Zaznaczanie błędów
* Testowanie jednostek
* Zapisywanie kodu jako artefaktu

Poniższy fragment kodu ilustruje implementację tych kroków w potoku DevOps * **YAML** _:

```yaml
steps:
- script: |
   flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
  workingDirectory: '$(Build.SourcesDirectory)'
  displayName: 'Run flake8 (code style analysis)'  
  
- script: |
   python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
  displayName: 'Run unit tests'

- task: PublishTestResults@2
  condition: succeededOrFailed()
  inputs:
    testResultsFiles: '$(Build.BinariesDirectory)/_-testresults.xml'
    testRunTitle: 'Linting & Unit tests'
    failTaskOnFailedTests: true
  displayName: 'Publish linting and unit test results'

- publish: $(Build.SourcesDirectory)
    artifact: di-notebooks
```

Potok używa [flake8](https://pypi.org/project/flake8/) do wykonania kodu w języku Python Zaznaczanie błędów. Uruchamia testy jednostkowe zdefiniowane w kodzie źródłowym i publikuje wyniki Zaznaczanie błędów i testy, aby były dostępne na ekranie wykonywania potoku platformy Azure:

![testy jednostkowe Zaznaczanie błędów](media/how-to-cicd-data-ingestion/linting-unit-tests.png)

Jeśli Zaznaczanie błędów i testy jednostkowe zakończyły się powodzeniem, potok skopiuje kod źródłowy do repozytorium artefaktów, który będzie używany przez kolejne kroki wdrażania.

### <a name="azure-data-factory-ci"></a>Azure Data Factory CI

Proces CI dla potoku Azure Data Factory to wąskie gardło dla potoku pozyskiwania danych. Brak ciągłej integracji. Artefaktem wdrożonym dla Azure Data Factory jest kolekcja Azure Resource Manager szablonów. Jedynym sposobem tworzenia tych szablonów jest kliknięcie przycisku * **Publish** _ w obszarze roboczym Azure Data Factory.

1. Inżynierowie danych scalają kod źródłowy z gałęzi funkcji do gałęzi współpracy, na przykład _*_Master_*_ lub _*_opracowywać_*_. 
1. Ktoś z przyznanymi uprawnieniami klika przycisk _*_Publikuj_*_ , aby generować Azure Resource Manager szablony z kodu źródłowego w gałęzi współpracy. 
1. Obszar roboczy weryfikuje potoki (Pomyśl o tym, jak zaznaczanie błędów i testy jednostkowe), generuje szablony Azure Resource Manager (Zastanów się nad kompilacją) i zapisuje wygenerowane szablony do gałęzi technicznej _*_adf_publish_*_ w tym samym repozytorium kodu (należy wziąć pod uwagę publikowanie artefaktów). Ta gałąź jest tworzona automatycznie przez obszar roboczy Azure Data Factory. 

Aby uzyskać więcej informacji na temat tego procesu, zobacz [ciągła integracja i dostarczanie w Azure Data Factory](../data-factory/continuous-integration-deployment.md).

Ważne jest, aby upewnić się, że wygenerowane szablony Azure Resource Manager są środowiskiem niezależny od. Oznacza to, że wszystkie wartości, które mogą się różnić między środowiskami, są parametryczne. Azure Data Factory jest wystarczająco inteligentny, aby uwidocznić większość takich wartości jako parametry. Na przykład w poniższym szablonie właściwości połączenia z obszarem roboczym Azure Machine Learning są ujawniane jako parametry:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        "AzureMLService_servicePrincipalKey": {
            "value": ""
        },
        "AzureMLService_properties_typeProperties_subscriptionId": {
            "value": "0fe1c235-5cfa-4152-17d7-5dff45a8d4ba"
        },
        "AzureMLService_properties_typeProperties_resourceGroupName": {
            "value": "devops-ds-rg"
        },
        "AzureMLService_properties_typeProperties_servicePrincipalId": {
            "value": "6e35e589-3b22-4edb-89d0-2ab7fc08d488"
        },
        "AzureMLService_properties_typeProperties_tenant": {
            "value": "72f988bf-86f1-41af-912b-2d7cd611db47"
        }
    }
}
```

Jednak możesz chcieć uwidocznić niestandardowe właściwości, które nie są obsługiwane domyślnie przez obszar roboczy Azure Data Factory. W scenariuszu tego artykułu potok Azure Data Factory wywołuje Notes języka Python przetwarzający dane. Notes akceptuje parametr o nazwie pliku danych wejściowych.

```Python
import pandas as pd
import numpy as np

data_file_name = getArgument("data_file_name")
data = pd.read_csv(data_file_name)

labels = np.array(data['target'])
...
```

Ta nazwa jest inna dla środowisk _*_dev_*_ , _*_pytań i odpowiedzi_*_ , _*_przeprowadzających_*_ i _*_produkcyjnego_*_ . W złożonym potoku z wieloma działaniami może istnieć kilka właściwości niestandardowych. Dobrym sposobem jest zebranie wszystkich wartości w jednym miejscu i Definiowanie ich jako _*_zmiennych_*_ potoku:

![Zrzut ekranu przedstawia Notes o nazwie PrepareData i M L Uruchom potok o nazwie M L Uruchom potok na górze przy użyciu karty zmienne wybrane poniżej z opcją dodania nowych zmiennych, z których każda ma nazwę, typ i wartość domyślną.](media/how-to-cicd-data-ingestion/adf-variables.png)

Działania potoku mogą odwoływać się do zmiennych potoku podczas ich rzeczywistego używania:

![Zrzut ekranu przedstawia Notes o nazwie PrepareData i M L uruchomienia potoku o nazwie M L w górnej części z kartą ustawień wybraną poniżej.](media/how-to-cicd-data-ingestion/adf-notebook-parameters.png)

W obszarze roboczym Azure Data Factory _*_nie_*_ są domyślnie uwidaczniane zmienne potoku jako parametry szablonów Azure Resource Manager. W obszarze roboczym jest stosowany [domyślny szablon parametryzacja](../data-factory/continuous-integration-deployment.md#default-parameterization-template) , który określa, jakie właściwości potoku powinny być uwidocznione jako Azure Resource Manager parametry szablonu. Aby dodać zmienne potoku do listy, zaktualizuj `"Microsoft.DataFactory/factories/pipelines"` sekcję [domyślnego szablonu parametryzacja](../data-factory/continuous-integration-deployment.md#default-parameterization-template) z następującym fragmentem kodu i umieść plik JSON wynik w katalogu głównym folderu źródłowego:

```json
"Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "variables": {
                "_": {
                    "defaultValue": "="
                }
            }
        }
    }
```

Spowoduje to wymuszenie, aby obszar roboczy Azure Data Factory dodać zmienne do listy parametrów po kliknięciu przycisku * **Publish** _:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        ...
        "data-ingestion-pipeline_properties_variables_data_file_name_defaultValue": {
            "value": "driver_prediction_train.csv"
        }        
    }
}
```

Wartości w pliku JSON są wartościami domyślnymi skonfigurowanymi w definicji potoku. Po wdrożeniu szablonu Azure Resource Manager powinny one zostać zastąpione wartościami środowiska docelowego.

## <a name="continuous-delivery-cd"></a>Ciągłe dostarczanie (CD)

Proces ciągłego dostarczania wykonuje artefakty i wdraża je w pierwszym środowisku docelowym. Upewnij się, że rozwiązanie działa, uruchamiając testy. Jeśli to się powiedzie, przechodzi do następnego środowiska. 

Potok na platformie Azure obejmuje wiele etapów przedstawiających środowiska. Każdy etap zawiera [wdrożenia](/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) i [zadania](/azure/devops/pipelines/process/phases?tabs=yaml&view=azure-devops) , które wykonują następujące czynności:

_ Wdróż Notes w języku Python w obszarze roboczym Azure Databricks
* Wdróż potok Azure Data Factory 
* Uruchamianie potoku
* Sprawdzanie wyniku pozyskiwania danych

Etapy potoku można skonfigurować przy użyciu [zatwierdzeń](/azure/devops/pipelines/process/approvals?tabs=check-pass&view=azure-devops) i [bram](/azure/devops/pipelines/release/approvals/gates?view=azure-devops) , które zapewniają dodatkową kontrolę nad sposobem rozwoju procesu wdrażania przez łańcuch środowisk.

### <a name="deploy-a-python-notebook"></a>Wdrażanie notesu w języku Python

Poniższy fragment kodu definiuje [wdrożenie](/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) potoku platformy Azure, które kopiuje Notes w języku Python do klastra datakostki:

```yaml
- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'       
```            

Artefakty produkowane przez CI są automatycznie kopiowane do agenta wdrażania i są dostępne w `$(Pipeline.Workspace)` folderze. W takim przypadku zadanie wdrażania odwołuje się do `di-notebooks` artefaktu zawierającego Notes języka Python. To [wdrożenie](/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) używa [rozszerzenia Azure DevOps](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks) w celu skopiowania plików notesu do obszaru roboczego datakosteks.

`Deploy_to_QA`Etap zawiera odwołanie do `devops-ds-qa-vg` grupy zmiennych zdefiniowanej w projekcie usługi Azure DevOps. Kroki w tym etapie odwołują się do zmiennych z tej grupy zmiennych (na przykład `$(DATABRICKS_URL)` i `$(DATABRICKS_TOKEN)` ). Pomysłem jest to, że następny etap (na przykład `Deploy_to_UAT` ) będzie działać z tymi samymi nazwami zmiennych zdefiniowanymi we własnej grupie zmiennych o zakresie przeprowadzających.

### <a name="deploy-an-azure-data-factory-pipeline"></a>Wdróż potok Azure Data Factory

Artefaktem do wdrożenia Azure Data Factory jest szablon Azure Resource Manager. Zostanie ona wdrożona przy użyciu zadania * **wdrożenie grupy zasobów platformy Azure** _, tak jak pokazano w poniższym fragmencie kodu:

```yaml
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
```
Wartość parametru Data filename pochodzi ze `$(DATA_FILE_NAME)` zmiennej zdefiniowanej w grupie zmiennych etapów kontroli jakości. Analogicznie, wszystkie parametry zdefiniowane w _*_ARMTemplateForFactory.jsna_*_ mogą zostać zastąpione. Jeśli tak nie jest, są używane wartości domyślne.

### <a name="run-the-pipeline-and-check-the-data-ingestion-result"></a>Uruchamianie potoku i sprawdzanie wyniku pozyskiwania danych

Następnym krokiem jest upewnienie się, że wdrożone rozwiązanie działa. Poniższa Definicja zadania uruchamia potok Azure Data Factory za pomocą [skryptu programu PowerShell](https://github.com/microsoft/DataOps/tree/master/adf/utils) i wykonuje Notes w języku Python w klastrze Azure Databricks. Notes sprawdza, czy dane zostały pozyskane prawidłowo, i sprawdza poprawność pliku danych wynikowych o `$(bin_FILE_NAME)` nazwie.

```yaml
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'
```

Końcowe zadanie w zadaniu sprawdza wynik wykonania notesu. Jeśli zwróci błąd, ustawia stan wykonywania potoku na niepowodzenie.

## <a name="putting-pieces-together"></a>Umieszczanie fragmentów

Kompletny potok ciągłej integracji/ciągłego wdrażania składa się z następujących etapów: _ CI
* Wdróż w usłudze pytań i odpowiedzi
    * Wdróż w usłudze datakostki + Wdróż w usłudze ADF
    * Test integracji

Zawiera kilka * etapów **wdrażania** _ równych liczbie środowisk docelowych. Każdy etap _*_wdrażania_*_ zawiera dwa [wdrożenia](/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) , które są uruchamiane równolegle, a [zadanie](/azure/devops/pipelines/process/phases?tabs=yaml&view=azure-devops) uruchamiane po wdrożeniach w celu przetestowania rozwiązania w środowisku.

Przykładowa implementacja potoku jest przedłożono w następującym fragmencie kodu _*_YAML_*_ :

```yaml
variables:
- group: devops-ds-vg

stages:
- stage: 'CI'
  displayName: 'CI'
  jobs:
  - job: "CI_Job"
    displayName: "CI Job"
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'
    - script: pip install --upgrade flake8 flake8_formatter_junit_xml
      displayName: 'Install flake8'
    - checkout: self
    - script: |
       flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
    workingDirectory: '$(Build.SourcesDirectory)'
    displayName: 'Run flake8 (code style analysis)'  
    - script: |
       python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
    displayName: 'Run unit tests'
    - task: PublishTestResults@2
    condition: succeededOrFailed()
    inputs:
        testResultsFiles: '$(Build.BinariesDirectory)/_-testresults.xml'
        testRunTitle: 'Linting & Unit tests'
        failTaskOnFailedTests: true
    displayName: 'Publish linting and unit test results'    

    # The CI stage produces two artifacts (notebooks and ADF pipelines).
    # The pipelines Azure Resource Manager templates are stored in a technical branch "adf_publish"
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/code/dataingestion
      artifact: di-notebooks
    - checkout: git://${{variables['System.TeamProject']}}@adf_publish    
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/devops-ds-adf
      artifact: adf-pipelines

- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'             
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'                

```

## <a name="next-steps"></a>Następne kroki

* [Kontrola źródła w usłudze Azure Data Factory](../data-factory/source-control.md)
* [Ciągła integracja i dostarczanie w Azure Data Factory](../data-factory/continuous-integration-deployment.md)
* [DevOps Azure Databricks](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)