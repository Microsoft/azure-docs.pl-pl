---
title: Akcje GitHub Actions dla ciągłej integracji/ciągłego wdrażania
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak utworzyć przepływ pracy akcji usługi GitHub w celu uczenia modelu na Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: juliakm
ms.author: jukullam
ms.date: 10/19/2020
ms.topic: conceptual
ms.custom: github-actions-azure
ms.openlocfilehash: 8bce95b65d9b944505d7f9153159b342e49427cd
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98762918"
---
# <a name="use-github-actions-with-azure-machine-learning"></a>Korzystanie z akcji usługi GitHub z Azure Machine Learning

Rozpocznij pracę z [akcjami usługi GitHub](https://docs.github.com/en/actions) , aby nauczyć model na Azure Machine Learning. 

> [!NOTE]
> Akcje usługi GitHub dla Azure Machine Learning są udostępniane w postaci, w jakiej są i nie są w pełni obsługiwane przez firmę Microsoft. Jeśli wystąpią problemy z konkretną akcją, Otwórz problem w repozytorium dla tej akcji. Na przykład jeśli wystąpi problem z akcją AML-Deploy, zgłoś problem w [https://github.com/Azure/aml-deploy]( https://github.com/Azure/aml-deploy) repozytorium.

## <a name="prerequisites"></a>Wymagania wstępne 

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Konto usługi GitHub. Jeśli nie masz takiego konta, zarejestruj się [bezpłatnie](https://github.com/join).  

## <a name="workflow-file-overview"></a>Omówienie pliku przepływu pracy

Przepływ pracy jest definiowany przez plik YAML (. yml) w `/.github/workflows/` ścieżce w repozytorium. Ta definicja zawiera różne kroki i parametry wchodzące w skład przepływu pracy.

Plik ma cztery sekcje:

|Sekcja  |Zadania  |
|---------|---------|
|**Authentication** | 1. Zdefiniuj nazwę główną usługi. <br /> 2. Utwórz wpis tajny usługi GitHub. |
|**Połącz** | 1. Połącz się z obszarem roboczym uczenia maszynowego. <br /> 2. Połącz się z obiektem docelowym obliczeń. |
|**Uruchom** | 1. Prześlij przebieg szkoleniowy. |
|**Wdrażanie** | 1. Zarejestruj model w rejestrze Azure Machine Learning. 1. Wdrażanie modelu. |

## <a name="create-repository"></a>Utwórz repozytorium

Utwórz nowe repozytorium [za pomocą akcji usługi GitHub i szablonu Azure Machine Learning](https://github.com/machine-learning-apps/ml-template-azure). 

1. Otwórz [szablon](https://github.com/machine-learning-apps/ml-template-azure) w serwisie GitHub. 
2. Wybierz pozycję **Użyj tego szablonu**. 

    :::image type="content" source="media/how-to-github-actions-machine-learning/gh-actions-use-template.png" alt-text="Wybierz pozycję Użyj tego szablonu":::
3. Utwórz nowe repozytorium na podstawie szablonu. Ustaw nazwę repozytorium na `ml-learning` lub nazwę wybranego elementu. 


## <a name="generate-deployment-credentials"></a>Generuj poświadczenia wdrożenia

Za pomocą polecenia [AZ AD Sp Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) można utworzyć jednostkę [usługi](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) [.](/cli/azure/) Uruchom to polecenie z [Azure Cloud Shell](https://shell.azure.com/) w Azure Portal lub wybierając przycisk **Wypróbuj** .

```azurecli-interactive
az ad sp create-for-rbac --name "myML" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name> \
                            --sdk-auth
```

W powyższym przykładzie Zastąp symbole zastępcze IDENTYFIKATORem subskrypcji, nazwą grupy zasobów i nazwą aplikacji. Dane wyjściowe są obiektem JSON z poświadczeniami przypisywania roli, które zapewniają dostęp do aplikacji App Service podobnej do poniższego. Skopiuj ten obiekt JSON do nowszej wersji.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

## <a name="configure-the-github-secret"></a>Konfigurowanie wpisu tajnego usługi GitHub

1. W witrynie [GitHub](https://github.com/)Przejrzyj repozytorium, wybierz pozycję **Ustawienia > wpisy tajne > Dodaj nowe hasło**.

2. Wklej wszystkie dane wyjściowe JSON z polecenia platformy Azure w polu wartość klucza tajnego. Podaj klucz tajny jako nazwę `AZURE_CREDENTIALS` .

## <a name="connect-to-the-workspace"></a>Łączenie z obszarem roboczym

Użyj [akcji obszar roboczy usługi Azure Machine Learning](https://github.com/marketplace/actions/azure-machine-learning-workspace) , aby nawiązać połączenie z obszarem roboczym Azure Machine Learning. 

```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

Domyślnie akcja oczekuje `workspace.json` pliku. Jeśli plik JSON ma inną nazwę, można go określić za pomocą `parameters_file` parametru wejściowego. Jeśli plik nie istnieje, zostanie utworzony nowy z nazwą repozytorium.


```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          parameters_file: "alternate_workspace.json"
```
Akcja spowoduje zapisanie właściwości Azure Resource Manager obszaru roboczego (ARM) w pliku konfiguracji, który zostanie wybrany przez wszystkie przyszłe Azure Machine Learning akcji GitHub. Plik jest zapisywany w `GITHUB_WORKSPACE/aml_arm_config.json` . 

## <a name="connect-to-a-compute-target-in-azure-machine-learning"></a>Nawiązywanie połączenia z elementem docelowym obliczeń w Azure Machine Learning

Użyj [akcji compute Azure Machine Learning](https://github.com/Azure/aml-compute) , aby nawiązać połączenie z elementem docelowym obliczeń w Azure Machine Learning.  Jeśli obiekt docelowy obliczeń istnieje, akcja spowoduje nawiązanie z nim połączenia. W przeciwnym razie akcja spowoduje utworzenie nowego elementu docelowego obliczeń. [Akcja COMPUTE AML](https://github.com/Azure/aml-compute) obsługuje tylko klaster obliczeniowy Azure ml i usługę Azure Kubernetes Service (AKS). 

```yaml
    - name: Connect/Create Azure Machine Learning Compute Target
      id: aml_compute_training
      uses: Azure/aml-compute@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```
## <a name="submit-training-run"></a>Przesyłanie przebiegu trenowania

Użyj [akcji szkolenia Azure Machine Learning](https://github.com/Azure/aml-run) , aby przesłać element ScriptRun, szacowania lub potoku do Azure Machine Learning. 

```yaml
    - name: Submit training run
      id: aml_run
      uses: Azure/aml-run@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

## <a name="register-model-in-registry"></a>Zarejestruj model w rejestrze

Użyj [akcji Zarejestruj model Azure Machine Learning](https://github.com/Azure/aml-registermodel) , aby zarejestrować model do Azure Machine Learning.

```yaml
    - name: Register model
      id: aml_registermodel
      uses: Azure/aml-registermodel@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          run_id:  ${{ steps.aml_run.outputs.run_id }}
          experiment_name: ${{ steps.aml_run.outputs.experiment_name }}
```

## <a name="deploy-model-to-azure-machine-learning-to-aci"></a>Wdróż model do Azure Machine Learning ACI

Użyj [akcji wdróż Azure Machine Learning](https://github.com/Azure/aml-deploy) , aby wdrożyć model i utworzyć punkt końcowy dla modelu. Do wdrożenia usługi Azure Kubernetes można także użyć wdrażania Azure Machine Learning. [Ten przykładowy przepływ pracy](https://github.com/Azure-Samples/mlops-enterprise-template) zawiera model, który jest wdrażany w usłudze Azure Kubernetes Service.

```yaml
    - name: Deploy model
      id: aml_deploy
      uses: Azure/aml-deploy@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          model_name:  ${{ steps.aml_registermodel.outputs.model_name }}
          model_version: ${{ steps.aml_registermodel.outputs.model_version }}

```

## <a name="complete-example"></a>Pełny przykład

Przeszkol model i Wdróż go w Azure Machine Learning. 

```yaml
# Actions train a model on Azure Machine Learning
name: Azure Machine Learning training and deployment
on:
  push:
    branches:
      - master
    # paths:
    #   - 'code/*'
jobs:
  train:
    runs-on: ubuntu-latest
    steps:
    # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
    - name: Check Out Repository
      id: checkout_repository
      uses: actions/checkout@v2
        
    # Connect or Create the Azure Machine Learning Workspace
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Connect or Create a Compute Target in Azure Machine Learning
    - name: Connect/Create Azure Machine Learning Compute Target
      id: aml_compute_training
      uses: Azure/aml-compute@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Submit a training run to the Azure Machine Learning
    - name: Submit training run
      id: aml_run
      uses: Azure/aml-run@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}

    # Register model in Azure Machine Learning model registry
    - name: Register model
      id: aml_registermodel
      uses: Azure/aml-registermodel@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          run_id:  ${{ steps.aml_run.outputs.run_id }}
          experiment_name: ${{ steps.aml_run.outputs.experiment_name }}

    # Deploy model in Azure Machine Learning to ACI
    - name: Deploy model
      id: aml_deploy
      uses: Azure/aml-deploy@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          model_name:  ${{ steps.aml_registermodel.outputs.model_name }}
          model_version: ${{ steps.aml_registermodel.outputs.model_version }}

```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli grupa zasobów i repozytorium nie są już potrzebne, Oczyść wdrożone zasoby, usuwając grupę zasobów i repozytorium GitHub. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie i uruchamianie potoków uczenia maszynowego za pomocą zestawu SDK Azure Machine Learning](how-to-create-your-first-pipeline.md)
