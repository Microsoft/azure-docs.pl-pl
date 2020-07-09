---
title: Niestandardowe ciągłej integracji/ciągłego kontenera z akcji GitHub
description: Dowiedz się, jak za pomocą akcji usługi GitHub wdrożyć niestandardowy kontener systemu Linux do App Service z potoku ciągłej integracji/ciągłego dostarczania.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: d5f175d887cec1d5b5e567d3f716e6492f4516dd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "78246969"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Wdrażanie niestandardowego kontenera do App Service przy użyciu akcji GitHub

Dzięki [akcjom GitHub](https://help.github.com/en/articles/about-github-actions) można tworzyć zautomatyzowane przepływy pracy tworzenia oprogramowania. Za pomocą [akcji Azure App Service dla kontenerów](https://github.com/Azure/webapps-container-deploy)można zautomatyzować przepływ pracy w celu wdrożenia aplikacji jako [kontenerów niestandardowych do App Service](https://azure.microsoft.com/services/app-service/containers/) przy użyciu akcji usługi GitHub.

> [!IMPORTANT]
> Akcje usługi GitHub są obecnie dostępne w wersji beta. Musisz najpierw [utworzyć konto, aby dołączyć do wersji zapoznawczej](https://github.com/features/actions) przy użyciu konta usługi GitHub.
> 

Przepływ pracy jest definiowany przez plik YAML (. yml) w `/.github/workflows/` ścieżce w repozytorium. Ta definicja zawiera różne kroki i parametry wchodzące w skład przepływu pracy.

W przypadku przepływu pracy kontenera Azure App Service plik ma trzy sekcje:

|Sekcja  |Zadania  |
|---------|---------|
|**Authentication** | 1. Zdefiniuj nazwę główną usługi. <br /> 2. Utwórz wpis tajny usługi GitHub. |
|**Kompilacja** | 1. Skonfiguruj środowisko. <br /> 2. Skompiluj obraz kontenera. |
|**Wdrażanie** | 1. Wdróż obraz kontenera. |

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

[Nazwę główną usługi](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object) można utworzyć przy użyciu polecenia [AZ AD Sp Create-for-RBAC](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) w [interfejsie użytkownika platformy Azure](https://docs.microsoft.com/cli/azure/). Można uruchomić to polecenie przy użyciu [Azure Cloud Shell](https://shell.azure.com/) w Azure Portal lub wybierając przycisk **Wypróbuj** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                            --sdk-auth
                            
# Replace {subscription-id}, {resource-group} with the subscription, resource group details of the WebApp
```

Dane wyjściowe są obiektem JSON z poświadczeniami przypisywania roli, które zapewniają dostęp do aplikacji App Service podobnej do poniższego. Skopiuj ten obiekt JSON w celu uwierzytelnienia z usługi GitHub.

 ```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

> [!IMPORTANT]
> Zawsze dobrym sposobem jest przyznanie minimalnego dostępu. Można ograniczyć zakres w powyższym poleceniach AZ CLI do konkretnej aplikacji App Service i Azure Container Registry, do której są wypychane obrazy kontenerów.

## <a name="configure-the-github-secret"></a>Konfigurowanie wpisu tajnego usługi GitHub

Poniższy przykład używa poświadczeń na poziomie użytkownika, np. nazwy głównej usługi platformy Azure do wdrożenia. Postępuj zgodnie z instrukcjami, aby skonfigurować klucz tajny:

1. W witrynie [GitHub](https://github.com/)Przejrzyj repozytorium, wybierz pozycję **Ustawienia > wpisy tajne > Dodaj nowe hasło**

2. Wklej zawartość poniższego `az cli` polecenia jako wartość zmiennej tajnej. Na przykład `AZURE_CREDENTIALS`.

    
    ```azurecli
    az ad sp create-for-rbac --name "myApp" --role contributor \
                                --scopes /subscriptions/{subscription-id}/resourceGroups/{resource-group} \
                                --sdk-auth
                                
    # Replace {subscription-id}, {resource-group} with the subscription, resource group details
    ```

3. Teraz w pliku przepływu pracy w gałęzi: `.github/workflows/workflow.yml` Zastąp wpis tajny w akcji logowania platformy Azure przy użyciu klucza tajnego.

4. Podobnie należy zdefiniować następujące dodatkowe wpisy tajne dla poświadczeń rejestru kontenera i ustawić je w akcji logowania Docker. 

    - REGISTRY_USERNAME
    - REGISTRY_PASSWORD

5. Zobaczysz wpisy tajne, jak pokazano poniżej.

    ![klucze tajne kontenera](../media/app-service-github-actions/app-service-secrets-container.png)

## <a name="build-the-container-image"></a>Tworzenie obrazu kontenera

Poniższy przykład przedstawia część przepływu pracy, który kompiluje obraz platformy Docker.

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
```

## <a name="deploy-to-an-app-service-container"></a>Wdrażanie do kontenera App Service

Aby wdrożyć obraz do niestandardowego kontenera w App Service, użyj `azure/webapps-container-deploy@v1` akcji. Ta akcja ma pięć parametrów:

| **Parametr**  | **Objaśnienie**  |
|---------|---------|
| **Nazwa aplikacji** | Potrzeb Nazwa aplikacji App Service | 
| **Nazwa gniazda** | Obowiązkowe Wprowadź istniejące miejsce poza miejscem produkcyjnym |
| **rastrow** | Potrzeb Określ w pełni kwalifikowaną nazwę obrazu kontenera. Na przykład "myregistry.azurecr.io/nginx:latest" lub "Python: 3.7.2-Alpine/". W przypadku aplikacji z wieloma kontenerami można podać wiele nazw obrazów kontenerów (rozdzielonych w wielu wierszach) |
| **plik konfiguracji** | Obowiązkowe Ścieżka pliku z systemem Docker — tworzenie. Powinna być w pełni kwalifikowana ścieżka lub odnosząca się do domyślnego katalogu roboczego. Wymagane dla aplikacji wielokontenerowych. |
| **Container — polecenie** | Obowiązkowe Wprowadź polecenie uruchamiania. Na przykład filename.dll uruchomienia dotnet lub dotnet |

Poniżej znajduje się przykładowy przepływ pracy do kompilowania i wdrażania aplikacji Node.js w kontenerze niestandardowym w App Service.

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    - uses: azure/docker-login@v1
      with:
        login-server: contoso.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    
    - run: |
        docker build . -t contoso.azurecr.io/nodejssampleapp:${{ github.sha }}
        docker push contoso.azurecr.io/nodejssampleapp:${{ github.sha }} 
      
    - uses: azure/webapps-container-deploy@v1
      with:
        app-name: 'node-rnc'
        images: 'contoso.azurecr.io/nodejssampleapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

## <a name="next-steps"></a>Następne kroki

Zestaw akcji można znaleźć w różnych repozytoriach w usłudze GitHub, z których każda zawiera dokumentację i przykłady ułatwiające korzystanie z usługi GitHub w przypadku ciągłej integracji/ciągłego wdrażania oraz wdrażanie aplikacji na platformie Azure.

- [Logowanie w usłudze Azure](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Usługa Azure WebApp dla kontenerów](https://github.com/Azure/webapps-container-deploy)

- [Logowanie/wylogowywanie platformy Docker](https://github.com/Azure/docker-login)

- [Zdarzenia wyzwalające przepływy pracy](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s wdrażanie](https://github.com/Azure/k8s-deploy)

- [Początkowe przepływy pracy CI](https://github.com/actions/starter-workflows)

- [Początkowe przepływy pracy do wdrożenia na platformie Azure](https://github.com/Azure/actions-workflow-samples)
