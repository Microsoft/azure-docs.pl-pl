---
title: Kompiluj, Testuj i wdrażaj kontenery w usłudze Azure Kubernetes Service przy użyciu akcji GitHub
description: Dowiedz się, jak wdrożyć kontener w usłudze Kubernetes przy użyciu akcji usługi GitHub
services: container-service
author: azooinmyluggage
ms.topic: article
ms.date: 11/06/2020
ms.author: atulmal
ms.custom: github-actions-azure
ms.openlocfilehash: 94134360de49a066f825cbb0c85712995d90b37f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98761461"
---
# <a name="github-actions-for-deploying-to-kubernetes-service"></a>Akcje GitHub dotyczące wdrażania w usłudze Kubernetes Service

Dzięki [akcjom GitHub](https://docs.github.com/en/actions) można tworzyć zautomatyzowane przepływy pracy tworzenia oprogramowania. Można użyć wielu akcji Kubernetes do wdrożenia do kontenerów z Azure Container Registry do usługi Azure Kubernetes przy użyciu akcji GitHub. 

## <a name="prerequisites"></a>Wymagania wstępne 

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Konto usługi GitHub. Jeśli nie masz takiego konta, zarejestruj się [bezpłatnie](https://github.com/join).  
- Działający klaster Kubernetes
    - [Samouczek: przygotowywanie aplikacji dla usługi Azure Kubernetes Service](tutorial-kubernetes-prepare-app.md)

## <a name="workflow-file-overview"></a>Omówienie pliku przepływu pracy

Przepływ pracy jest definiowany przez plik YAML (. yml) w `/.github/workflows/` ścieżce w repozytorium. Ta definicja zawiera różne kroki i parametry wchodzące w skład przepływu pracy.

Dla przepływu pracy AKS, plik ma trzy sekcje:

|Sekcja  |Zadania  |
|---------|---------|
|**Authentication** | Zaloguj się do prywatnego rejestru kontenerów (ACR) |
|**Kompilacja** | Kompilowanie & wypychanie obrazu kontenera  |
|**Wdrażanie** | 1. Ustaw docelowy klaster AKS |
| |2. tworzenie wpisu tajnego rejestru ogólnego/Docker w klastrze Kubernetes  |
||3. Wdróż w klastrze Kubernetes|

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

[Nazwę główną usługi](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) można utworzyć przy użyciu polecenia [AZ AD Sp Create-for-RBAC](/cli/azure/ad/sp#az-ad-sp-create-for-rbac) w [interfejsie użytkownika platformy Azure](/cli/azure/). Można uruchomić to polecenie przy użyciu [Azure Cloud Shell](https://shell.azure.com/) w Azure Portal lub wybierając przycisk **Wypróbuj** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

W powyższym poleceniu Zastąp symbole zastępcze IDENTYFIKATORem subskrypcji i grupą zasobów. Dane wyjściowe to poświadczenia przypisania roli, które zapewniają dostęp do zasobu. Polecenie powinno spowodować wyjście obiektu JSON podobnego do tego.

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

Postępuj zgodnie z instrukcjami, aby skonfigurować wpisy tajne:

1. W witrynie [GitHub](https://github.com/)przejdź do repozytorium, wybierz pozycję **Ustawienia > wpisy tajne > Dodaj nowe hasło**.

    ![Zrzut ekranu przedstawia link Dodaj nowy wpis tajny dla repozytorium.](media/kubernetes-action/secrets.png)

2. Wklej zawartość powyższego `az cli` polecenia jako wartość zmiennej tajnej. Na przykład `AZURE_CREDENTIALS`.

3. Podobnie należy zdefiniować następujące dodatkowe wpisy tajne dla poświadczeń rejestru kontenera i ustawić je w akcji logowania Docker. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

4. Zostaną wyświetlone wpisy tajne, jak pokazano poniżej.

    ![Zrzut ekranu przedstawia istniejące wpisy tajne dla repozytorium.](media/kubernetes-action/kubernetes-secrets.png)

##  <a name="build-a-container-image-and-deploy-to-azure-kubernetes-service-cluster"></a>Tworzenie obrazu kontenera i wdrażanie go w klastrze usługi Azure Kubernetes Service

Kompilacja i wypychanie obrazów kontenera odbywa się przy użyciu `Azure/docker-login@v1` akcji. 


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

### <a name="deploy-to-azure-kubernetes-service-cluster"></a>Wdróż w klastrze usługi Azure Kubernetes Service

Aby wdrożyć obraz kontenera w AKS, musisz użyć `Azure/k8s-deploy@v1` akcji. Ta akcja ma pięć parametrów:

| **Parametr**  | **Wyjaśnienie**  |
|---------|---------|
| **obszaru** | Obowiązkowe Wybierz docelową przestrzeń nazw Kubernetes. Jeśli przestrzeń nazw nie zostanie podana, polecenia zostaną uruchomione w domyślnej przestrzeni nazw | 
| **manifesty** |  Potrzeb Ścieżka do plików manifestu, która będzie używana do wdrażania |
| **rastrow** | Obowiązkowe W pełni kwalifikowany adres URL zasobów przeznaczonych do podstawiania plików manifestu |
| **imagepullsecrets** | Obowiązkowe Nazwa wpisu tajnego rejestru platformy Docker, który został już skonfigurowany w klastrze. Każda z tych nazw tajnych jest dodawana w polu imagePullSecrets dla obciążeń znalezionych w wejściowych plikach manifestu |
| **polecenia kubectl — wersja** | Obowiązkowe Instaluje określoną wersję pliku binarnego polecenia kubectl |


Przed wdrożeniem programu do AKS należy ustawić przestrzeń nazw Target Kubernetes i utworzyć wpis tajny obrazu. Zobacz [ściąganie obrazów z usługi Azure Container Registry do klastra Kubernetes](../container-registry/container-registry-auth-kubernetes.md), aby dowiedzieć się więcej na temat sposobu działania obrazów ściągających. 

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


Ukończ wdrożenie z `k8s-deploy` akcją. Zamień zmienne środowiskowe na wartości dla aplikacji. 

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

Gdy klaster Kubernetes, rejestr kontenerów i repozytorium nie są już potrzebne, Oczyść wdrożone zasoby, usuwając grupę zasobów i repozytorium GitHub. 

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o usłudze Azure Kubernetes Service](/azure/architecture/reference-architectures/containers/aks-start-here)

### <a name="more-kubernetes-github-actions"></a>Więcej Kubernetesych akcji GitHub

* [Instalator narzędzia polecenia kubectl](https://github.com/Azure/setup-kubectl) ( `azure/setup-kubectl` ): instaluje określoną wersję programu polecenia kubectl w module uruchamiającego.
* [Kontekst zestawu Kubernetes](https://github.com/Azure/k8s-set-context) ( `azure/k8s-set-context` ): Ustaw docelowy kontekst klastra Kubernetes, który będzie używany przez inne akcje, lub Uruchom dowolne polecenia polecenia kubectl.
* [Kontekst zestawu AKS](https://github.com/Azure/aks-set-context) ( `azure/aks-set-context` ): Ustaw docelowy kontekst klastra usługi Azure Kubernetes.
* [Kubernetes Utwórz klucz tajny](https://github.com/Azure/k8s-create-secret) ( `azure/k8s-create-secret` ): Utwórz ogólny wpis tajny lub element Docker-Registry w klastrze Kubernetes.
* [Kubernetes Deploy](https://github.com/Azure/k8s-deploy) ( `azure/k8s-deploy` ): Tworzenie i Wdróż manifesty w klastrach Kubernetes.
* [Setup Helm](https://github.com/Azure/setup-helm) ( `azure/setup-helm` ): Zainstaluj określoną wersję pliku binarnego Helm w module uruchamiającego.
* [Kubernetes tworzenie](https://github.com/Azure/k8s-bake) ( `azure/k8s-bake` ): Tworzenie plik manifestu, który ma być używany na potrzeby wdrożeń przy użyciu helm2, kustomize lub kompose.
* [Kubernetes lint](https://github.com/azure/k8s-lint) ( `azure/k8s-lint` ): Weryfikuj/lint plików manifestu.
