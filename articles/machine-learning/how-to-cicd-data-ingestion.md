---
title: DevOps dla potoku pozyskiwania danych
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak stosować praktyki DevOps do implementacji potoku pozyskiwania danych, która służy do przygotowywania danych do szkolenia modeli.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 01/30/2020
ms.custom: tracking-python
ms.openlocfilehash: 864c7f2fd16a935d1df740b7d64d068bf7214d18
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84552285"
---
# <a name="devops-for-a-data-ingestion-pipeline"></a>DevOps dla potoku pozyskiwania danych

W większości scenariuszy rozwiązanie do pozyskiwania danych to kompozycja skryptów, wywołań usługi i potoku, który organizuje wszystkie działania. W tym artykule dowiesz się, jak stosować praktyki DevOps do cyklu życia opracowywania wspólnego potoku pozyskiwania danych. Potok przygotowuje dane do szkolenia modelu Machine Learning.

## <a name="the-solution"></a>Rozwiązanie

Weź pod uwagę następujący przepływ pracy pozyskiwania danych:

![Pozyskiwanie danych — potok](media/how-to-cicd-data-ingestion/data-ingestion-pipeline.png)

W tym podejściu dane szkoleniowe są przechowywane w usłudze Azure Blob Storage. Potok Azure Data Factory pobiera dane z wejściowego kontenera obiektów blob, przekształca je i zapisuje dane w wyjściowym kontenerze obiektów BLOB. Ten kontener służy jako [Magazyn danych](concept-data.md) dla usługi Azure Machine Learning. Po przygotowaniu danych potok Data Factory wywołuje potok Machine Learning szkolenia, aby szkolić model. W tym konkretnym przykładzie transformacja danych jest wykonywana przez Notes języka Python, uruchomiony w klastrze Azure Databricks. 

## <a name="what-we-are-building"></a>Co tworzysz

Podobnie jak w przypadku dowolnego rozwiązania oprogramowania, istnieje zespół (na przykład inżynierowie danych) pracujący nad nim. 

![cicd — pozyskiwanie danych](media/how-to-cicd-data-ingestion/cicd-data-ingestion.png)

Współpracują i udostępniają te same zasoby platformy Azure, takie jak Azure Data Factory, Azure Databricks, konto usługi Azure Storage i takie. Zbieranie tych zasobów to środowisko programistyczne. Inżynierowie danych składają się na tę samą bazę kodu źródłowego. Proces ciągłej integracji składa kod, sprawdza go za pomocą testów jakości kodu, testów jednostkowych i tworzy artefakty, takie jak testowany kod i szablony Azure Resource Manager. Proces ciągłego dostarczania wdraża artefakty w środowiskach podrzędnych. W tym artykule przedstawiono sposób automatyzacji procesów CI i dysków CD przy użyciu [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/).

## <a name="source-control-management"></a>Zarządzanie kontrolą źródła

Członkowie zespołu pracują na nieco różnych sposobach współpracy w kodzie źródłowym notesu Python i kodzie źródłowym Azure Data Factory. Jednak w obu przypadkach kod jest przechowywany w repozytorium kontroli źródła (na przykład Azure DevOps, GitHub, GitLab), a współpraca jest zwykle oparta na modelu rozgałęziania (na przykład [GitFlow](https://datasift.github.io/gitflow/IntroducingGitFlow.html)).

### <a name="python-notebook-source-code"></a>Kod źródłowy notesu Python

Inżynierowie danych współpracują z kodem źródłowym notesu języka Python lokalnie w środowisku IDE (na przykład [Visual Studio Code](https://code.visualstudio.com)) lub bezpośrednio w obszarze roboczym datakosteks. Druga z nich umożliwia debugowanie kodu w środowisku deweloperskim. W każdym przypadku kod zostanie scalony z repozytorium po rozgałęzieniu zasad. Zdecydowanie zaleca się przechowywanie kodu w `.py` plikach, a nie w `.ipynb` formacie notesu Jupyter. Zwiększa czytelność kodu i umożliwia automatyczne sprawdzanie jakości kodu w procesie CI.

### <a name="azure-data-factory-source-code"></a>Azure Data Factory kod źródłowy

Kod źródłowy potoków Azure Data Factory jest kolekcją plików JSON generowanych przez obszar roboczy. Zwykle inżynierowie danych pracują z projektantem wizualnym w obszarze roboczym Azure Data Factory, a nie bezpośrednio z plikami kodu źródłowego. Skonfiguruj obszar roboczy z repozytorium kontroli źródła zgodnie z opisem w [dokumentacji Azure Data Factory](https://docs.microsoft.com/azure/data-factory/source-control#author-with-azure-repos-git-integration). W przypadku tej konfiguracji inżynierowie danych mogą współpracować nad kodem źródłowym, korzystając z preferowanego przepływu pracy rozgałęziania.    

## <a name="continuous-integration-ci"></a>Ciągła integracja (CI)

Ostatecznym celem procesu ciągłej integracji jest zebranie współpracującego zespołu z kodu źródłowego i przygotowanie go do wdrożenia w środowiskach podrzędnych. Podobnie jak w przypadku zarządzania kodem źródłowym ten proces jest różny dla notesów Python i potoków Azure Data Factory. 

### <a name="python-notebook-ci"></a>Element konfiguracji notesu języka Python

Proces CI dla notesów języka Python Pobiera kod z gałęzi współpracy (na przykład ***Master*** lub ***opracowywać***) i wykonuje następujące działania:
* Zaznaczanie błędów kodu
* Testowanie jednostek
* Zapisywanie kodu jako artefaktu

Poniższy fragment kodu ilustruje implementację tych kroków w potoku usługi Azure DevOps ***YAML*** :

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
    testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
    testRunTitle: 'Linting & Unit tests'
    failTaskOnFailedTests: true
  displayName: 'Publish linting and unit test results'

- publish: $(Build.SourcesDirectory)
    artifact: di-notebooks

```

Potok używa ***flake8*** do wykonania kodu w języku Python Zaznaczanie błędów. Uruchamia testy jednostkowe zdefiniowane w kodzie źródłowym i publikuje wyniki Zaznaczanie błędów i testy, aby były dostępne na ekranie wykonywania potoku platformy Azure:

![Zaznaczanie błędów-Unit-Tests](media/how-to-cicd-data-ingestion/linting-unit-tests.png)

Jeśli Zaznaczanie błędów i testy jednostkowe zakończyły się powodzeniem, potok skopiuje kod źródłowy do repozytorium artefaktów, który będzie używany przez kolejne kroki wdrażania.

### <a name="azure-data-factory-ci"></a>Azure Data Factory CI

Proces CI dla potoku Azure Data Factory jest wąskim gardłem w całym wątku ciągłej integracji i ciągłego wdrażania danych. Brak ***ciągłej*** integracji. Artefaktem wdrożonym dla Azure Data Factory jest kolekcja Azure Resource Manager szablonów. Jedynym sposobem tworzenia tych szablonów jest kliknięcie przycisku ***Publikuj*** w obszarze roboczym Azure Data Factory. Nie ma tutaj automatyzacji.
Inżynierowie danych scalają kod źródłowy z gałęzi funkcji do gałęzi współpracy, na przykład ***Master*** lub ***opracowywać***. Następnie ktoś z przyznanymi uprawnieniami klika przycisk ***Publikuj*** , aby generować Azure Resource Manager szablony z kodu źródłowego w gałęzi współpracy. Gdy przycisk zostanie kliknięty, obszar roboczy sprawdza poprawność potoków (Pomyśl o tym, jak zaznaczanie błędów i testy jednostkowe), generuje szablony Azure Resource Manager (Zastanów się nad kompilacją) i zapisuje wygenerowane szablony do gałęzi technicznej ***adf_publish*** w tym samym repozytorium kodu (należy wziąć pod uwagę publikowanie artefaktów). Ta gałąź jest tworzona automatycznie przez obszar roboczy Azure Data Factory. Ten proces został opisany szczegółowo w [dokumentacji Azure Data Factory](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment).

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

Ta nazwa jest inna dla środowisk ***dev***, ***pytań i odpowiedzi***, ***przeprowadzających***i ***produkcyjnego*** . W złożonym potoku z wieloma działaniami może istnieć kilka właściwości niestandardowych. Dobrym sposobem jest zebranie wszystkich wartości w jednym miejscu i Definiowanie ich jako ***zmiennych***potoku:

![ADF — zmienne](media/how-to-cicd-data-ingestion/adf-variables.png)

Działania potoku mogą odwoływać się do zmiennych potoku podczas ich rzeczywistego używania:

![ADF — parametry notesu](media/how-to-cicd-data-ingestion/adf-notebook-parameters.png)

W obszarze roboczym Azure Data Factory ***nie*** są domyślnie uwidaczniane zmienne potoku jako parametry szablonów Azure Resource Manager. W obszarze roboczym jest stosowany [domyślny szablon parametryzacja](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) , który określa, jakie właściwości potoku powinny być uwidocznione jako Azure Resource Manager parametry szablonu. Aby dodać do listy zmienne potoku, zaktualizuj sekcję "Microsoft. DataFactory/factorers/Pipelines" [domyślnego szablonu parametryzacja](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) z następującym fragmentem kodu i umieść plik JSON z wynikami w katalogu głównym folderu źródłowego:

```json
"Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "variables": {
                "*": {
                    "defaultValue": "="
                }
            }
        }
    }
```

Spowoduje to wymuszenie, aby obszar roboczy Azure Data Factory dodać zmienne do listy parametrów po kliknięciu przycisku ***Publikuj*** :

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

Proces ciągłego dostarczania wykonuje artefakty i wdraża je w pierwszym środowisku docelowym. Upewnij się, że rozwiązanie działa, uruchamiając testy. Jeśli to się powiedzie, przechodzi do następnego środowiska. Potok na platformie Azure obejmuje wiele etapów przedstawiających środowiska. Każdy etap zawiera [wdrożenia](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) i [zadania](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) , które wykonują następujące czynności:
* Wdróż Notes w języku Python w Azure Databricks obszarze roboczym
* Wdróż potok Azure Data Factory 
* Uruchamianie potoku
* Sprawdzanie wyniku pozyskiwania danych

Etapy potoku można skonfigurować przy użyciu [zatwierdzeń](https://docs.microsoft.com/azure/devops/pipelines/process/approvals?view=azure-devops&tabs=check-pass) i [bram](https://docs.microsoft.com/azure/devops/pipelines/release/approvals/gates?view=azure-devops) , które zapewniają dodatkową kontrolę nad sposobem rozwoju procesu wdrażania przez łańcuch środowisk.

### <a name="deploy-a-python-notebook"></a>Wdrażanie notesu w języku Python

Poniższy fragment kodu definiuje [wdrożenie](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) potoku platformy Azure, które kopiuje Notes w języku Python do klastra datakostki:

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

Artefakty utworzone przez CI są automatycznie kopiowane do agenta wdrażania i są dostępne w folderze ***$ (Pipeline. Workspace)*** . W takim przypadku zadanie wdrażania odwołuje się do artefaktu ***"di-Notess"*** zawierającego Notes języka Python. To [wdrożenie](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) używa [rozszerzenia Azure DevOps](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks) w celu skopiowania plików notesu do obszaru roboczego datakosteks.
Etap ***Deploy_to_QA*** zawiera odwołanie do grupy zmiennych ***DevOps-ds-pytania-VG*** zdefiniowanej w projekcie DevOps platformy Azure. Kroki w tym etapie odwołują się do zmiennych z tej grupy zmiennych (na przykład $ (DATABRICKS_URL), $ (DATABRICKS_TOKEN)). Pomysłem jest to, że kolejny etap (na przykład ***Deploy_to_UAT***) będzie działać z tymi samymi nazwami zmiennych zdefiniowanymi w jego własnej grupie zmiennych o zakresie przeprowadzających.

### <a name="deploy-an-azure-data-factory-pipeline"></a>Wdróż potok Azure Data Factory

Artefaktem do wdrożenia Azure Data Factory jest szablon Azure Resource Manager. W związku z tym zostanie on wdrożony przy użyciu zadania ***wdrażania grupy zasobów platformy Azure*** , jak pokazano w poniższym fragmencie kodu:

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
Wartość parametru Data filename pochodzi ze zmiennej $ (DATA_FILE_NAME) zdefiniowanej w grupie zmiennych etapów kontroli jakości. Podobnie wszystkie parametry zdefiniowane w ***ARMTemplateForFactory. JSON*** mogą zostać zastąpione. Jeśli tak nie jest, są używane wartości domyślne.

### <a name="run-the-pipeline-and-check-the-data-ingestion-result"></a>Uruchamianie potoku i sprawdzanie wyniku pozyskiwania danych

Następnym krokiem jest upewnienie się, że wdrożone rozwiązanie działa. Poniższa Definicja zadania uruchamia potok Azure Data Factory za pomocą [skryptu programu PowerShell](https://github.com/microsoft/DataOps/tree/master/adf/utils) i wykonuje Notes w języku Python w klastrze Azure Databricks. Notes sprawdza, czy dane zostały pozyskane prawidłowo, i sprawdza poprawność pliku danych wynikowych o nazwie $ (bin_FILE_NAME).

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

Wynikiem tego artykułu jest potok platformy Azure ciągłej integracji i ciągłego wdrażania, który składa się z następujących etapów:
* CI
* Wdróż w usłudze pytań i odpowiedzi
    * Wdróż w usłudze datakostki + Wdróż w usłudze ADF
    * Test integracji

Zawiera kilka etapów ***wdrażania*** równych liczbie środowisk docelowych. Każdy etap ***wdrażania*** zawiera dwa [wdrożenia](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) , które są uruchamiane równolegle, a [zadanie](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) uruchamiane po wdrożeniach w celu przetestowania rozwiązania w środowisku.

Przykładowa implementacja potoku jest przedłożono w następującym fragmencie kodu ***YAML*** :

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
        testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
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

* [Kontrola źródła w usłudze Azure Data Factory](https://docs.microsoft.com/azure/data-factory/source-control)
* [Ciągła integracja i dostarczanie w Azure Data Factory](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment)
* [DevOps Azure Databricks](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)
