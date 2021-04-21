---
title: Kompilowanie, testowanie i wdrażanie kontenerów w Azure Kubernetes Service użyciu GitHub Actions
description: Dowiedz się, jak używać GitHub Actions do wdrażania kontenera na kubernetes
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/06/2020
ms.author: atulmal
ms.custom: github-actions-azure
ms.openlocfilehash: 3a8e91f74fe3c862a814d7660e64748df9553f1d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779763"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>GitHub Actions wdrażania w usłudze Kubernetes

[GitHub Actions](https://docs.github.com/en/actions) elastyczność tworzenia zautomatyzowanego przepływu pracy cyklu życia tworzenia oprogramowania. Można użyć wielu akcji rozwiązania Kubernetes do wdrożenia w kontenerach od Azure Container Registry do Azure Kubernetes Service z GitHub Actions. 

## <a name="prerequisites"></a>Wymagania wstępne 

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Konto usługi GitHub. Jeśli go nie masz, zarejestruj się [bezpłatnie.](https://github.com/join)  
- Roboczy klaster Kubernetes
    - [Samouczek: przygotowywanie aplikacji do Azure Kubernetes Service](tutorial-kubernetes-prepare-app.md)

## <a name="workflow-file-overview"></a>Omówienie pliku przepływu pracy

Przepływ pracy jest definiowany przez plik YAML (yml) w `/.github/workflows/` ścieżce w repozytorium. Ta definicja zawiera różne kroki i parametry, które składa się na przepływ pracy.

W przypadku przepływu pracy przeznaczonego dla usługi AKS plik zawiera trzy sekcje:

|Sekcja  |Zadania  |
|---------|---------|
|**Authentication** | Logowanie do prywatnego rejestru kontenerów (ACR) |
|**Kompilacja** | Kompilowanie & wypychanie obrazu kontenera  |
|**Wdrażanie** | 1. Ustawianie docelowego klastra usługi AKS |
| |2. Tworzenie klucza tajnego ogólnego/docker-registry w klastrze Kubernetes  |
||3. Wdrażanie w klastrze Kubernetes|

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Jednostkę usługi można [utworzyć za](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) pomocą polecenia az ad [sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) w interfejsie wiersza polecenia [platformy Azure.](/cli/azure/) To polecenie można uruchomić przy [użyciu Azure Cloud Shell](https://shell.azure.com/) w Azure Portal lub wybierając **przycisk Wypróbuj.**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

W powyższym poleceniu zastąp symbole zastępcze identyfikatorem subskrypcji i grupą zasobów. Dane wyjściowe to poświadczenia przypisania roli, które zapewniają dostęp do zasobu. Polecenie powinno zwrócić obiekt JSON podobny do tego.

```json
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```
Skopiuj ten obiekt JSON, którego możesz użyć do uwierzytelniania z usługi GitHub.

## <a name="configure-the-github-secrets"></a>Konfigurowanie wpisów tajnych usługi GitHub

Wykonaj kroki konfigurowania wpisów tajnych:

1. W [usłudze GitHub](https://github.com/)przejdź do repozytorium, wybierz pozycję Ustawienia > wpisy tajne **> Dodaj nowy wpis tajny.**

    ![Zrzut ekranu przedstawia link Dodaj nowy klucz tajny dla repozytorium.](media/kubernetes-action/secrets.png)

2. Wklej zawartość powyższego polecenia `az cli` jako wartość zmiennej tajnej. Na przykład `AZURE_CREDENTIALS`.

3. Podobnie zdefiniuj następujące dodatkowe wpisy tajne dla poświadczeń rejestru kontenerów i ustaw je w akcji logowania platformy Docker. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. Po zdefiniowanym wpisie tajnym zostaną wyświetlone wpisy tajne, jak pokazano poniżej.

    ![Zrzut ekranu przedstawia istniejące wpisy tajne dla repozytorium.](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Kompilowanie obrazu kontenera i wdrażanie go w Azure Kubernetes Service klastra

Kompilowanie i wypychanie obrazów kontenerów odbywa się przy użyciu `Azure/docker-login@v1` akcji . 


```yml
env:
  REGISTRY_NAME: {registry-name}
  CLUSTER_NAME: {cluster-name}
  CLUSTER_RESOURCE_GROUP: {resource-group-name}
  NAMESPACE: {namespace-name}
  APP_NAME: {app-name}
  
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    
    # Connect to Azure Container registry (ACR)
    - uses: azure/docker-login@v1
      with:
        login-server: ${{ env.REGISTRY_NAME }}.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }} 
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    # Container build and push to a Azure Container registry (ACR)
    - run: |
        docker build . -t ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        docker push ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}

```

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Wdrażanie w Azure Kubernetes Service klastra

Aby wdrożyć obraz kontenera w u usługi AKS, należy użyć `Azure/k8s-deploy@v1` akcji . Ta akcja ma pięć parametrów:

| **Parametr**  | **Wyjaśnienie**  |
|---------|---------|
| **Obszaru nazw** | (Opcjonalnie) Wybierz docelową przestrzeń nazw Kubernetes. Jeśli przestrzeń nazw nie zostanie podany, polecenia będą uruchamiane w domyślnej przestrzeni nazw | 
| **Manifesty** |  (Wymagane) Ścieżka do plików manifestu, które będą używane do wdrożenia |
| **Obrazów** | (Opcjonalnie) W pełni kwalifikowany adres URL zasobów obrazów do użycia na podstawienia w plikach manifestu |
| **imagepullsecrets** | (Opcjonalnie) Nazwa klucza tajnego rejestru platformy Docker, który został już ustawiony w klastrze. Każda z tych nazw tajnych jest dodawana w polu imagePullSecrets dla obciążeń znalezionych w wejściowych plikach manifestu |
| **kubectl-version** | (Opcjonalnie) Instaluje określoną wersję pliku binarnego kubectl |


Przed wdrożeniem do usługi AKS należy ustawić docelową przestrzeń nazw kubernetes i utworzyć klucz tajny ściągania obrazu. Zobacz Ściąganie obrazów z rejestru kontenerów platformy Azure do klastra [Kubernetes,](../container-registry/container-registry-auth-kubernetes.md)aby dowiedzieć się więcej o tym, jak działa ściąganie obrazów. 

```yaml
  # Create namespace if doesn't exist
  - run: |
      kubectl create namespace ${{ env.NAMESPACE }} --dry-run -o json | kubectl apply -f -
  
  # Create image pull secret for ACR
  - uses: azure/k8s-create-secret@v1
    with:
      container-registry-url: ${{ env.REGISTRY_NAME }}.azurecr.io
      container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
      container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
      secret-name: ${{ env.SECRET }}
      namespace: ${{ env.NAMESPACE }}
      force: true
```


Ukończ wdrożenie za pomocą `k8s-deploy` akcji . Zastąp zmienne środowiskowe wartościami aplikacji. 

```yaml

on: [push]

# Environment variables available to all jobs and steps in this workflow
env:
  REGISTRY_NAME: {registry-name}
  CLUSTER_NAME: {cluster-name}
  CLUSTER_RESOURCE_GROUP: {resource-group-name}
  NAMESPACE: {namespace-name}
  SECRET: {secret-name}
  APP_NAME: {app-name}
  
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    
    # Connect to Azure Container registry (ACR)
    - uses: azure/docker-login@v1
      with:
        login-server: ${{ env.REGISTRY_NAME }}.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }} 
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    # Container build and push to a Azure Container registry (ACR)
    - run: |
        docker build . -t ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        docker push ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
    
    # Set the target Azure Kubernetes Service (AKS) cluster. 
    - uses: azure/aks-set-context@v1
      with:
        creds: '${{ secrets.AZURE_CREDENTIALS }}'
        cluster-name: ${{ env.CLUSTER_NAME }}
        resource-group: ${{ env.CLUSTER_RESOURCE_GROUP }}
    
    # Create namespace if doesn't exist
    - run: |
        kubectl create namespace ${{ env.NAMESPACE }} --dry-run -o json | kubectl apply -f -
    
    # Create image pull secret for ACR
    - uses: azure/k8s-create-secret@v1
      with:
        container-registry-url: ${{ env.REGISTRY_NAME }}.azurecr.io
        container-registry-username: ${{ secrets.REGISTRY_USERNAME }}
        container-registry-password: ${{ secrets.REGISTRY_PASSWORD }}
        secret-name: ${{ env.SECRET }}
        namespace: ${{ env.NAMESPACE }}
        force: true
    
    # Deploy app to AKS
    - uses: azure/k8s-deploy@v1
      with:
        manifests: |
          manifests/deployment.yml
          manifests/service.yml
        images: |
          ${{ env.REGISTRY_NAME }}.azurecr.io/${{ env.APP_NAME }}:${{ github.sha }}
        imagepullsecrets: |
          ${{ env.SECRET }}
        namespace: ${{ env.NAMESPACE }}
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Gdy klaster Kubernetes, rejestr kontenerów i repozytorium nie będą już potrzebne, wyczyść wdrożone zasoby, usuwając grupę zasobów i repozytorium GitHub. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o Azure Kubernetes Service](/azure/architecture/reference-architectures/containers/aks-start-here)

### <a name="more-kubernetes-github-actions"></a>Więcej informacji na temat GitHub Actions Kubernetes

* [Instalator narzędzia Kubectl](https://github.com/Azure/setup-kubectl) `azure/setup-kubectl` (): instaluje określoną wersję narzędzia kubectl w programie runner.
* [Kontekst zestawu kubernetes](https://github.com/Azure/k8s-set-context) ( ): ustaw kontekst docelowego klastra Kubernetes, który będzie używany przez inne akcje lub uruchamia `azure/k8s-set-context` polecenia kubectl.
* [Kontekst zestawu usługi AKS](https://github.com/Azure/aks-set-context) ( `azure/aks-set-context` ): ustaw docelowy Azure Kubernetes Service kontekstu klastra.
* [Kubernetes create secret](https://github.com/Azure/k8s-create-secret) ( ): utwórz ogólny wpis tajny lub wpis tajny `azure/k8s-create-secret` docker-registry w klastrze Kubernetes.
* [Wdrażanie rozwiązania Kubernetes](https://github.com/Azure/k8s-deploy) ( `azure/k8s-deploy` ): tąb i wdrażanie manifestów w klastrach Kubernetes.
* [Skonfiguruj program Helm](https://github.com/Azure/setup-helm) ( `azure/setup-helm` ): zainstaluj określoną wersję pliku binarnego programu Helm w programie runner.
* [Kubernetes bake](https://github.com/Azure/k8s-bake) ( ): plik manifestu bake używany do wdrożeń przy użyciu `azure/k8s-bake` helm2, kustomize lub kompose.
* [Lint kubernetes](https://github.com/azure/k8s-lint) ( `azure/k8s-lint` ): weryfikuj/lint plików manifestu.
