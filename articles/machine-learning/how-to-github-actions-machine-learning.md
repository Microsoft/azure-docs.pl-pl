---
title: Akcje GitHub Actions dla ciągłej integracji/ciągłego wdrażania
titleSuffix: Azure Machine Learning
description: Dowiedz się, jak utworzyć przepływ pracy GitHub Actions trenowania modelu na Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: juliakm
ms.author: jukullam
ms.date: 10/19/2020
ms.topic: conceptual
ms.custom: github-actions-azure
ms.openlocfilehash: 6505523aa367eaf202ece81a4253429e864e169a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107780375"
---
# <a name="use-github-actions-with-azure-machine-learning"></a>Używanie GitHub Actions z Azure Machine Learning

Rozpoczynanie pracy z [GitHub Actions](https://docs.github.com/en/actions) trenowania modelu na Azure Machine Learning. 

> [!NOTE]
> GitHub Actions dla Azure Machine Learning są dostarczane bez ich obsługi i nie są w pełni obsługiwane przez firmę Microsoft. Jeśli wystąpią problemy z określoną akcją, otwórz problem w repozytorium dla akcji. Jeśli na przykład wystąpi problem z akcją aml-deploy, zgłoś problem w [https://github.com/Azure/aml-deploy]( https://github.com/Azure/aml-deploy) repo.

## <a name="prerequisites"></a>Wymagania wstępne 

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Konto usługi GitHub. Jeśli go nie masz, zarejestruj się [bezpłatnie.](https://github.com/join)  

## <a name="workflow-file-overview"></a>Omówienie pliku przepływu pracy

Przepływ pracy jest definiowany przez plik YAML (yml) w `/.github/workflows/` ścieżce w repozytorium. Ta definicja zawiera różne kroki i parametry, które składa się na przepływ pracy.

Plik zawiera cztery sekcje:

|Sekcja  |Zadania  |
|---------|---------|
|**Authentication** | 1. Definiowanie jednostki usługi. <br /> 2. Utwórz klucz tajny usługi GitHub. |
|**Połącz** | 1. Połącz się z obszarem roboczym uczenia maszynowego. <br /> 2. Połącz się z docelowym obiektem obliczeniowym. |
|**Uruchom** | 1. Prześlij przebieg trenowania. |
|**Wdrażanie** | 1. Rejestrowanie modelu w Azure Machine Learning rejestru. 1. Wdrażanie modelu. |

## <a name="create-repository"></a>Tworzenie repozytorium

Utwórz nowe repozytorium poza [platformą ML Ops przy użyciu GitHub Actions i Azure Machine Learning szablonu](https://github.com/machine-learning-apps/ml-template-azure). 

1. Otwórz szablon [w](https://github.com/machine-learning-apps/ml-template-azure) witrynie GitHub. 
2. Wybierz pozycję **Użyj tego szablonu**. 

    :::image type="content" source="media/how-to-github-actions-machine-learning/gh-actions-use-template.png" alt-text="Wybierz pozycję Użyj tego szablonu":::
3. Utwórz nowe repozytorium na podstawie szablonu. Ustaw nazwę repozytorium `ml-learning` na lub nazwę wybranej przez użytkownika. 


## <a name="generate-deployment-credentials"></a>Generowanie poświadczeń wdrożenia

Jednostkę usługi można utworzyć [za](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) pomocą polecenia [az ad sp create-for-rbac w](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) interfejsie wiersza polecenia platformy [Azure.](/cli/azure/) Uruchom to [polecenie, Azure Cloud Shell](https://shell.azure.com/) w Azure Portal lub wybierając **przycisk Wypróbuj.**

```azurecli-interactive
az ad sp create-for-rbac --name "myML" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name> \
                            --sdk-auth
```

W powyższym przykładzie zastąp symbole zastępcze identyfikatorem subskrypcji, nazwą grupy zasobów i nazwą aplikacji. Dane wyjściowe to obiekt JSON z poświadczeniami przypisania roli, które zapewniają dostęp do aplikacji App Service podobnej do poniższej. Skopiuj ten obiekt JSON do późniejszego przetwarzania.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

## <a name="configure-the-github-secret"></a>Konfigurowanie tajnego repozytorium GitHub

1. W [usłudze GitHub](https://github.com/)przejrzyj repozytorium i wybierz pozycję Ustawienia > wpisy tajne **> Dodaj nowy wpis tajny.**

2. Wklej całe dane wyjściowe JSON z polecenia interfejsu wiersza polecenia platformy Azure do pola wartości we kluczu tajnym. Nadaj kluczowi tajnego nazwę `AZURE_CREDENTIALS` .

## <a name="connect-to-the-workspace"></a>Łączenie z obszarem roboczym

Użyj akcji [Obszar roboczy usługi Azure Machine Learning, aby](https://github.com/marketplace/actions/azure-machine-learning-workspace) nawiązać połączenie z Azure Machine Learning roboczym. 

```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

Domyślnie akcja oczekuje `workspace.json` pliku. Jeśli plik JSON ma inną nazwę, możesz określić go za pomocą `parameters_file` parametru wejściowego. Jeśli plik nie istnieje, zostanie utworzony z nazwą repozytorium.


```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          parameters_file: "alternate_workspace.json"
```
Akcja zapisuje właściwości obszaru roboczego Azure Resource Manager (ARM) do pliku konfiguracji, który będzie wybierany przez wszystkie przyszłe Azure Machine Learning GitHub Actions. Plik jest zapisywany w pliku `GITHUB_WORKSPACE/aml_arm_config.json` . 

## <a name="connect-to-a-compute-target-in-azure-machine-learning"></a>Nawiązywanie połączenia z docelowym obiektem obliczeniowym w Azure Machine Learning

Użyj akcji [Azure Machine Learning Compute,](https://github.com/Azure/aml-compute) aby nawiązać połączenie z docelowym obiektem obliczeniowym w Azure Machine Learning.  Jeśli docelowy obiekt obliczeniowy istnieje, akcja połączy się z nim. W przeciwnym razie akcja spowoduje utworzenie nowego docelowego obiektu obliczeniowego. Akcja [AML Compute obsługuje](https://github.com/Azure/aml-compute) tylko klaster obliczeniowy usługi Azure ML i Azure Kubernetes Service (AKS). 

```yaml
    - name: Connect/Create Azure Machine Learning Compute Target
      id: aml_compute_training
      uses: Azure/aml-compute@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```
## <a name="submit-training-run"></a>Przesyłanie przebiegu trenowania

Użyj akcji [Azure Machine Learning Trenowania,](https://github.com/Azure/aml-run) aby przesłać element ScriptRun, estymator lub potok do Azure Machine Learning. 

```yaml
    - name: Submit training run
      id: aml_run
      uses: Azure/aml-run@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

## <a name="register-model-in-registry"></a>Rejestrowanie modelu w rejestrze

Użyj akcji [Azure Machine Learning Zarejestruj model,](https://github.com/Azure/aml-registermodel) aby zarejestrować model do Azure Machine Learning.

```yaml
    - name: Register model
      id: aml_registermodel
      uses: Azure/aml-registermodel@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          run_id:  ${{ steps.aml_run.outputs.run_id }}
          experiment_name: ${{ steps.aml_run.outputs.experiment_name }}
```

## <a name="deploy-model-to-azure-machine-learning-to-aci"></a>Wdrażanie modelu w u Azure Machine Learning ACI

Użyj akcji [Azure Machine Learning Deploy,](https://github.com/Azure/aml-deploy) aby wdrożyć model i utworzyć punkt końcowy dla modelu. Możesz również użyć funkcji wdrażania Azure Machine Learning, aby wdrożyć w Azure Kubernetes Service. Zobacz [ten przykładowy przepływ](https://github.com/Azure-Samples/mlops-enterprise-template) pracy dla modelu, który jest wdrażany w Azure Kubernetes Service.

```yaml
    - name: Deploy model
      id: aml_deploy
      uses: Azure/aml-deploy@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          model_name:  ${{ steps.aml_registermodel.outputs.model_name }}
          model_version: ${{ steps.aml_registermodel.outputs.model_version }}

```

## <a name="complete-example"></a>Kompletny przykład

Przeszkolić model i wdrożyć go w Azure Machine Learning. 

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

Gdy grupa zasobów i repozytorium nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów i repozytorium GitHub. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie i uruchamianie potoków uczenia maszynowego przy użyciu Azure Machine Learning SDK](./how-to-create-machine-learning-pipelines.md)