---
title: Niestandardowe ciągłej integracji/ciągłego kontenera z akcji GitHub
description: Dowiedz się, jak za pomocą akcji usługi GitHub wdrożyć niestandardowy kontener systemu Linux do App Service z potoku ciągłej integracji/ciągłego dostarczania.
ms.devlang: na
ms.topic: article
ms.date: 10/03/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: github-actions-azure
ms.openlocfilehash: dc8b5e75b4feed886f843e7a516cc18429afec11
ms.sourcegitcommit: 638f326d02d108cf7e62e996adef32f2b2896fd5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91728492"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Wdrażanie niestandardowego kontenera do App Service przy użyciu akcji GitHub

Dzięki [akcjom GitHub](https://help.github.com/en/articles/about-github-actions) można utworzyć zautomatyzowany przepływ pracy tworzenia oprogramowania. Za pomocą [akcji Web Deploy platformy Azure](https://github.com/Azure/webapps-deploy)można zautomatyzować przepływ pracy w celu wdrożenia niestandardowych kontenerów do [App Service](overview.md) przy użyciu akcji usługi GitHub.

Przepływ pracy jest definiowany przez plik YAML (. yml) w `/.github/workflows/` ścieżce w repozytorium. Ta definicja zawiera różne kroki i parametry, które znajdują się w przepływie pracy.

W przypadku przepływu pracy kontenera Azure App Service plik ma trzy sekcje:

|Sekcja  |Zadania  |
|---------|---------|
|**Authentication** | 1. Pobierz nazwę główną usługi lub profil publikacji. <br /> 2. Utwórz wpis tajny usługi GitHub. |
|**Kompilacja** | 1. Utwórz środowisko. <br /> 2. Skompiluj obraz kontenera. |
|**Wdrażanie** | 1. Wdróż obraz kontenera. |

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Konto usługi GitHub. Jeśli nie masz takiego konta, zarejestruj się [bezpłatnie](https://github.com/join).  
- Rejestr kontenerów roboczych i Azure App Service App for Containers. Ten przykład używa Azure Container Registry. 
    - [Dowiedz się, jak utworzyć kontener Node.js aplikacji przy użyciu platformy Docker, wypchnąć obraz kontenera do rejestru, a następnie wdrożyć obraz w Azure App Service](https://docs.microsoft.com/azure/developer/javascript/tutorial-vscode-docker-node-01)

## <a name="generate-deployment-credentials"></a>Generuj poświadczenia wdrożenia

Zalecanym sposobem uwierzytelniania przy użyciu usługi Azure App Services na potrzeby akcji GitHub jest profil publikowania. Można także uwierzytelnić się za pomocą nazwy głównej usługi, ale proces wymaga większej liczby kroków. 

Zapisz poświadczenia profilu publikowania lub nazwę główną usługi jako [wpis tajny serwisu GitHub](https://docs.github.com/en/actions/reference/encrypted-secrets) , aby uwierzytelnić się na platformie Azure. Będziesz uzyskiwać dostęp do wpisu tajnego w ramach przepływu pracy. 

# <a name="publish-profile"></a>[Publikuj profil](#tab/publish-profile)

Profil publikowania to poświadczenie na poziomie aplikacji. Skonfiguruj swój profil publikowania jako wpis tajny usługi GitHub. 

1. Przejdź do usługi App Service w Azure Portal. 

1. Na stronie **Przegląd** wybierz pozycję **Pobierz profil publikowania**.

1. Zapisz pobrany plik. Zawartość pliku zostanie użyta do utworzenia wpisu tajnego usługi GitHub.

# <a name="service-principal"></a>[Nazwa główna usługi](#tab/service-principal)

Za pomocą polecenia [AZ AD Sp Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) można utworzyć jednostkę [usługi](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) [.](/cli/azure/) Uruchom to polecenie z [Azure Cloud Shell](https://shell.azure.com/) w Azure Portal lub wybierając przycisk **Wypróbuj** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

W przykładzie Zastąp symbole zastępcze IDENTYFIKATORem subskrypcji, nazwą grupy zasobów i nazwą aplikacji. Dane wyjściowe są obiektem JSON z poświadczeniami przypisania roli, które zapewniają dostęp do aplikacji App Service. Skopiuj ten obiekt JSON do nowszej wersji.

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
> Zawsze dobrym sposobem jest przyznanie minimalnego dostępu. Zakres w poprzednim przykładzie jest ograniczony do konkretnej aplikacji App Service, a nie całej grupy zasobów.

---

## <a name="configure-the-github-secret"></a>Konfigurowanie wpisu tajnego usługi GitHub

W witrynie [GitHub](https://github.com/)Przejrzyj repozytorium, wybierz pozycję **Ustawienia > wpisy tajne > Dodaj nowe hasło**.

Wklej zawartość danych wyjściowych JSON jako wartość zmiennej tajnej. Podaj tajną nazwę, taką jak `AZURE_CREDENTIALS` .

Podczas późniejszej konfiguracji pliku przepływu pracy należy użyć wpisu tajnego dla danych wejściowych `creds` akcji logowania platformy Azure. Na przykład:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

## <a name="configure-the-github-secret-for-authentication"></a>Konfigurowanie wpisu tajnego usługi GitHub na potrzeby uwierzytelniania

# <a name="publish-profile"></a>[Publikuj profil](#tab/publish-profile)

W witrynie [GitHub](https://github.com/)Przejrzyj repozytorium, wybierz pozycję **Ustawienia > wpisy tajne > Dodaj nowe hasło**.

Aby użyć [poświadczeń na poziomie aplikacji](#generate-deployment-credentials), wklej zawartość pobranego pliku profilu publikowania w polu wartość klucza tajnego. Nazwij klucz tajny `AZURE_WEBAPP_PUBLISH_PROFILE` .

Podczas konfigurowania przepływu pracy w usłudze GitHub należy użyć `AZURE_WEBAPP_PUBLISH_PROFILE` akcji w obszarze Wdróż aplikację sieci Web platformy Azure. Na przykład:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[Nazwa główna usługi](#tab/service-principal)

W witrynie [GitHub](https://github.com/)Przejrzyj repozytorium, wybierz pozycję **Ustawienia > wpisy tajne > Dodaj nowe hasło**.

Aby użyć [poświadczeń na poziomie użytkownika](#generate-deployment-credentials), Wklej wszystkie dane wyjściowe JSON z polecenia platformy Azure w polu wartość klucza tajnego. Podaj tajną nazwę, taką jak `AZURE_CREDENTIALS` .

Podczas późniejszej konfiguracji pliku przepływu pracy należy użyć wpisu tajnego dla danych wejściowych `creds` akcji logowania platformy Azure. Na przykład:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="configure-github-secrets-for-your-registry"></a>Konfigurowanie wpisów tajnych usługi GitHub dla rejestru

Zdefiniuj wpisy tajne do użycia z akcją logowania platformy Docker. 

1. Przejdź do kontenera w Azure Portal lub Docker i skopiuj nazwę użytkownika i hasło. 

2. Zdefiniuj nowy wpis tajny dla nazwy użytkownika rejestru o nazwie `REGISTRY_USERNAME` . 

3. Zdefiniuj nowy wpis tajny dla hasła rejestru o nazwie `REGISTRY_PASSWORD` . 

## <a name="build-the-container-image"></a>Tworzenie obrazu kontenera

Poniższy przykład przedstawia część przepływu pracy, który kompiluje Node.JS obrazu platformy Docker. Zaloguj się do prywatnego rejestru kontenerów przy użyciu [logowania Docker](https://github.com/azure/docker-login) . W tym przykładzie używa Azure Container Registry, ale ta sama akcja działa w przypadku innych rejestrów. 

# <a name="publish-profile"></a>[Publikuj profil](#tab/publish-profile)

Ten przykład pokazuje, jak utworzyć obraz Node.JS Docker przy użyciu profilu publikowania na potrzeby uwierzytelniania.


```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```

Możesz również użyć [logowania do platformy Docker](https://github.com/azure/docker-login) , aby zalogować się do wielu rejestrów kontenerów w tym samym czasie. Ten przykład zawiera dwa nowe wpisy tajne usługi GitHub do uwierzytelniania za pomocą usługi docker.io.

```yml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - uses: azure/docker-login@v1
      with:
        login-server: index.docker.io
        username: ${{ secrets.DOCKERIO_USERNAME }}
        password: ${{ secrets.DOCKERIO_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
```
# <a name="service-principal"></a>[Nazwa główna usługi](#tab/service-principal)

Ten przykład pokazuje, jak utworzyć obraz Node.JS Docker przy użyciu nazwy głównej usługi do uwierzytelnienia. 

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
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}  
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}      
    - name: Azure logout
      run: |
        az logout
```

---

## <a name="deploy-to-an-app-service-container"></a>Wdrażanie do kontenera App Service

Aby wdrożyć obraz do niestandardowego kontenera w App Service, użyj `azure/webapps-deploy@v2` akcji. Ta akcja ma pięć parametrów:

| **Parametr**  | **Objaśnienie**  |
|---------|---------|
| **Nazwa aplikacji** | Potrzeb Nazwa aplikacji App Service | 
| **Publikuj — profil** | Obowiązkowe Publikuj zawartość pliku profilu za pomocą wpisów tajnych Web Deploy |
| **rastrow** | W pełni kwalifikowana nazwa obrazu kontenera. Na przykład "myregistry.azurecr.io/nginx:latest" lub "Python: 3.7.2-Alpine/". W przypadku scenariusza z wieloma kontenerami można podać wiele nazw obrazów kontenerów (rozdzielonych w wielu wierszach) |
| **Nazwa gniazda** | Obowiązkowe Wprowadź istniejące miejsce poza miejscem produkcyjnym |
| **plik konfiguracji** | Obowiązkowe Ścieżka pliku z systemem Docker — redagowanie |

# <a name="publish-profile"></a>[Publikuj profil](#tab/publish-profile)

```yaml
name: Linux Container Node Workflow

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2

    - uses: azure/docker-login@v1
      with:
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}

    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     

    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
```
# <a name="service-principal"></a>[Nazwa główna usługi](#tab/service-principal)

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
        login-server: mycontainer.azurecr.io
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
    - run: |
        docker build . -t mycontainer.azurecr.io/myapp:${{ github.sha }}
        docker push mycontainer.azurecr.io/myapp:${{ github.sha }}     
      
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'myapp'
        images: 'mycontainer.azurecr.io/myapp:${{ github.sha }}'
    
    - name: Azure logout
      run: |
        az logout
```

---

## <a name="next-steps"></a>Następne kroki

Zestaw akcji można znaleźć w różnych repozytoriach w usłudze GitHub, z których każda zawiera dokumentację i przykłady ułatwiające korzystanie z usługi GitHub w przypadku ciągłej integracji/ciągłego wdrażania oraz wdrażanie aplikacji na platformie Azure.

- [Przepływy pracy akcji do wdrożenia na platformie Azure](https://github.com/Azure/actions-workflow-samples)

- [Logowanie w usłudze Azure](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Logowanie/wylogowywanie platformy Docker](https://github.com/Azure/docker-login)

- [Zdarzenia wyzwalające przepływy pracy](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s wdrażanie](https://github.com/Azure/k8s-deploy)

- [Początkowe przepływy pracy](https://github.com/actions/starter-workflows)