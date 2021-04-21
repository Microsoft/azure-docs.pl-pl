---
title: Niestandardowa cisza/cd kontenera z GitHub Actions
description: Dowiedz się, jak używać GitHub Actions do wdrażania niestandardowego kontenera systemu Linux do App Service z potoku ci/CD.
ms.devlang: na
ms.topic: article
ms.date: 12/04/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: github-actions-azure
ms.openlocfilehash: bf9fba9142de82c6e8518198d54b5e74f1807838
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107789433"
---
# <a name="deploy-a-custom-container-to-app-service-using-github-actions"></a>Wdrażanie niestandardowego kontenera w App Service użyciu GitHub Actions

[GitHub Actions](https://docs.github.com/en/actions) zapewnia elastyczność tworzenia zautomatyzowanego przepływu pracy tworzenia oprogramowania. Akcja [Azure Web Deploy umożliwia](https://github.com/Azure/webapps-deploy)zautomatyzowanie przepływu pracy w celu wdrożenia niestandardowych kontenerów w App Service [przy](overview.md) użyciu GitHub Actions.

Przepływ pracy jest definiowany przez plik YAML (yml) w `/.github/workflows/` ścieżce w repozytorium. Ta definicja zawiera różne kroki i parametry, które znajdują się w przepływie pracy.

W przypadku Azure App Service przepływu pracy kontenera plik zawiera trzy sekcje:

|Sekcja  |Zadania  |
|---------|---------|
|**Authentication** | 1. Pobieranie jednostki usługi lub profilu publikowania. <br /> 2. Utwórz klucz tajny usługi GitHub. |
|**Kompilacja** | 1. Utwórz środowisko. <br /> 2. Skompilowanie obrazu kontenera. |
|**Wdrażanie** | 1. Wdrażanie obrazu kontenera. |

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Bezpłatne tworzenie konta](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Konto usługi GitHub. Jeśli go nie masz, zarejestruj się [bezpłatnie.](https://github.com/join) Musisz mieć kod w repozytorium GitHub, aby wdrożyć go w Azure App Service. 
- Roboczy rejestr kontenerów i Azure App Service dla kontenerów. W tym przykładzie użyto Azure Container Registry. Pamiętaj, aby ukończyć pełne wdrożenie, aby Azure App Service kontenerów. W przeciwieństwie do zwykłych aplikacji internetowych, aplikacje internetowe dla kontenerów nie mają domyślnej strony docelowej. Opublikuj kontener, aby mieć roboczy przykład.
    - [Dowiedz się, jak utworzyć konteneryzowany konteneryzowany Node.js przy użyciu platformy Docker, wypchnąć obraz kontenera do rejestru, a następnie wdrożyć obraz w Azure App Service](/azure/developer/javascript/tutorial-vscode-docker-node-01)
        
## <a name="generate-deployment-credentials"></a>Generowanie poświadczeń wdrożenia

Zalecanym sposobem uwierzytelniania w usłudze Azure App Services for GitHub Actions jest użycie profilu publikowania. Można również uwierzytelnić się za pomocą jednostki usługi, ale proces wymaga więcej kroków. 

Zapisz poświadczenia profilu publikowania lub jednostkę usługi jako klucz tajny [usługi GitHub do](https://docs.github.com/en/actions/reference/encrypted-secrets) uwierzytelniania na platformie Azure. Będziesz mieć dostęp do tego tajnego w przepływie pracy. 

# <a name="publish-profile"></a>[Profil publikowania](#tab/publish-profile)

Profil publikowania to poświadczenie na poziomie aplikacji. Skonfiguruj profil publikowania jako klucz tajny usługi GitHub. 

1. Przejdź do usługi App Service w Azure Portal. 

1. Na stronie **Przegląd** wybierz pozycję **Pobierz profil publikowania.**

    > [!NOTE]
    > Od października 2020 r. aplikacje internetowe systemu Linux będą potrzebować ustawienia aplikacji ustawionego na `WEBSITE_WEBDEPLOY_USE_SCM` wartość `true` **przed pobraniem pliku**. To wymaganie zostanie usunięte w przyszłości. Zobacz [konfigurowanie App Service aplikacji internetowej](./configure-common.md)w Azure Portal , aby dowiedzieć się, jak skonfigurować typowe ustawienia aplikacji internetowej.  

1. Zapisz pobrany plik. Za pomocą zawartości pliku utworzysz klucz tajny usługi GitHub.

# <a name="service-principal"></a>[Jednostka usługi](#tab/service-principal)

Jednostkę usługi można utworzyć [za](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) pomocą polecenia [az ad sp create-for-rbac w](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) interfejsie wiersza polecenia platformy [Azure.](/cli/azure/) Uruchom to [polecenie, Azure Cloud Shell](https://shell.azure.com/) w Azure Portal lub wybierając **przycisk Wypróbuj.**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

W tym przykładzie zastąp symbole zastępcze identyfikatorem subskrypcji, nazwą grupy zasobów i nazwą aplikacji. Dane wyjściowe to obiekt JSON z poświadczeniami przypisania roli, które zapewniają dostęp do App Service aplikacji. Skopiuj ten obiekt JSON do późniejszego przetwarzania.

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
> Zawsze dobrym rozwiązaniem jest przyznanie minimalnego dostępu. Zakres w poprzednim przykładzie jest ograniczony do określonej App Service, a nie całej grupy zasobów.

---
## <a name="configure-the-github-secret-for-authentication"></a>Konfigurowanie tajnego hasła usługi GitHub na celu uwierzytelniania

# <a name="publish-profile"></a>[Profil publikowania](#tab/publish-profile)

W [usłudze GitHub](https://github.com/)przejrzyj repozytorium i wybierz pozycję Ustawienia > wpisy tajne **> Dodaj nowy wpis tajny.**

Aby użyć [poświadczeń na poziomie aplikacji,](#generate-deployment-credentials)wklej zawartość pobranego pliku profilu publikowania w polu wartości tego tajnego. Nadaj tajnej nazwie `AZURE_WEBAPP_PUBLISH_PROFILE` .

Podczas konfigurowania przepływu pracy usługi GitHub użyjesz polecenia w akcji Deploy Azure Web App (Wdrażanie `AZURE_WEBAPP_PUBLISH_PROFILE` aplikacji internetowej platformy Azure). Na przykład:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[Jednostka usługi](#tab/service-principal)

W [usłudze GitHub](https://github.com/)przejrzyj repozytorium i wybierz pozycję Ustawienia > wpisy tajne **> Dodaj nowy wpis tajny.**

Aby użyć [poświadczeń na poziomie użytkownika,](#generate-deployment-credentials)wklej całe dane wyjściowe JSON z polecenia interfejsu wiersza polecenia platformy Azure do pola wartości we kluczu tajnym. Nadaj kluczowi tajnego nazwę, na przykład `AZURE_CREDENTIALS` .

Podczas późniejszego konfigurowania pliku przepływu pracy użyjesz tajnego hasła dla danych `creds` wejściowych akcji logowania platformy Azure. Na przykład:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="configure-github-secrets-for-your-registry"></a>Konfigurowanie wpisów tajnych usługi GitHub dla rejestru

Zdefiniuj wpisy tajne do użycia z akcją logowania platformy Docker. W przykładzie w tym dokumencie użyto Azure Container Registry dla rejestru kontenerów. 

1. Przejdź do kontenera na stronie Azure Portal platformy Docker i skopiuj nazwę użytkownika i hasło. Nazwę użytkownika i Azure Container Registry można znaleźć w Azure Portal w obszarze **Klucze** dostępu  >  **ustawień** dla rejestru. 

2. Zdefiniuj nowy wpis tajny dla nazwy użytkownika rejestru o nazwie `REGISTRY_USERNAME` . 

3. Zdefiniuj nowy wpis tajny dla hasła rejestru o nazwie `REGISTRY_PASSWORD` . 

## <a name="build-the-container-image"></a>Kompilowanie obrazu kontenera

W poniższym przykładzie przedstawiono część przepływu pracy, która tworzy Node.JS platformy Docker. Użyj [logowania platformy Docker,](https://github.com/azure/docker-login) aby zalogować się do prywatnego rejestru kontenerów. W tym przykładzie Azure Container Registry, ale ta sama akcja działa w przypadku innych rejestrów. 


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

Możesz również użyć logowania [platformy Docker,](https://github.com/azure/docker-login) aby zalogować się do wielu rejestrów kontenerów w tym samym czasie. Ten przykład zawiera dwa nowe wpisy tajne usługi GitHub do uwierzytelniania za pomocą docker.io. W przykładzie założono, że na poziomie głównym rejestru znajduje się plik Dockerfile. 

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

## <a name="deploy-to-an-app-service-container"></a>Wdrażanie w kontenerze App Service kontenerze

Aby wdrożyć obraz w kontenerze niestandardowym w App Service, użyj `azure/webapps-deploy@v2` akcji . Ta akcja ma siedem parametrów:

| **Parametr**  | **Wyjaśnienie**  |
|---------|---------|
| **nazwa aplikacji** | (Wymagane) Nazwa aplikacji App Service aplikacji | 
| **publish-profile** | (Opcjonalnie) Dotyczy systemów Web Apps (Windows i Linux) i kontenerów aplikacji internetowych (linux). Scenariusz z wieloma kontenerami nie jest obsługiwany. Publikowanie zawartości pliku profilu \* (publishsettings) z wpisami Web Deploy tajnymi | 
| **nazwa-miejsca** | (Opcjonalnie) Wprowadź istniejące miejsce inne niż miejsce produkcyjne |
| **Pakiet** | (Opcjonalnie) Dotyczy tylko aplikacji internetowej: ścieżka do pakietu lub folderu. \*zip, \* war, \* jar lub folder do wdrożenia |
| **Obrazów** | (Wymagane) Dotyczy tylko kontenerów aplikacji internetowych: określ w pełni kwalifikowaną nazwę obrazów kontenera. Na przykład "myregistry.azurecr.io/nginx:latest" lub "python:3.7.2-alpine/". W przypadku aplikacji z wieloma kontenerami można określić wiele nazw obrazów kontenerów (rozdzielonych wieloma wierszami) |
| **plik konfiguracji** | (Opcjonalnie) Dotyczy tylko kontenerów aplikacji internetowych: ścieżka Docker-Compose pliku. Powinna być w pełni kwalifikowaną ścieżką lub względna względem domyślnego katalogu roboczego. Wymagane w przypadku aplikacji z wieloma kontenerami. |
| **polecenie uruchamiania** | (Opcjonalnie) Wprowadź polecenie uruchamiania. Na przykład dotnet run lub dotnet filename.dll |

# <a name="publish-profile"></a>[Profil publikowania](#tab/publish-profile)

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
# <a name="service-principal"></a>[Jednostka usługi](#tab/service-principal)

```yaml
on: [push]

name: Linux_Container_Node_Workflow

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@main
    
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

Nasz zestaw akcji pogrupowanych w różne repozytoria można znaleźć w witrynie GitHub, z których każde zawiera dokumentację i przykłady, które ułatwiają korzystanie z usługi GitHub na użytek funkcji ci/CD i wdrażanie aplikacji na platformie Azure.

- [Przepływy pracy akcji do wdrożenia na platformie Azure](https://github.com/Azure/actions-workflow-samples)

- [Logowanie w usłudze Azure](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Logowanie/wyloguj się z platformy Docker](https://github.com/Azure/docker-login)

- [Zdarzenia wyzwalające przepływy pracy](https://docs.github.com/en/actions/reference/events-that-trigger-workflows)

- [Wdrażanie k8s](https://github.com/Azure/k8s-deploy)

- [Początkowe przepływy pracy](https://github.com/actions/starter-workflows)