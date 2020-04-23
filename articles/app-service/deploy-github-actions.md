---
title: Konfigurowanie ciągłej integracji/ciągłego wdrażania za pomocą akcji usługi GitHub
description: Dowiedz się, jak wdrożyć kod w usłudze Azure App Service z potoku ciągłej integracji/ciągłego wdrażania za pomocą akcji GitHub. Dostosuj zadania kompilacji i wykonaj złożone wdrożenia.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.openlocfilehash: 57ca5b0880d4b027e33bc0d01fc6225eb886029b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084995"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Wdrażanie w usłudze app service przy użyciu akcji usługi GitHub

[GitHub Actions](https://help.github.com/en/articles/about-github-actions) zapewnia elastyczność tworzenia zautomatyzowanego przepływu pracy cyklu życia tworzenia oprogramowania. Dzięki akcji usługi Azure App Service dla usługi GitHub można zautomatyzować przepływ pracy do wdrożenia w [usłudze Azure App Service](overview.md) przy użyciu akcji Usługi GitHub.

> [!IMPORTANT]
> GitHub Actions jest obecnie w wersji beta. Musisz najpierw [zarejestrować się, aby dołączyć do wersji zapoznawczej](https://github.com/features/actions) przy użyciu konta GitHub.
> 

Przepływ pracy jest definiowany przez plik YAML (.yml) w `/.github/workflows/` ścieżce w repozytorium. Ta definicja zawiera różne kroki i parametry, które składają się na przepływ pracy.

W przypadku przepływu pracy usługi Azure App Service plik składa się z trzech sekcji:

|Sekcja  |Zadania  |
|---------|---------|
|**Authentication** | 1. Zdefiniuj jednostkę usługi <br /> 2. Tworzenie tajemnicy GitHub |
|**Kompilacja** | 1. Konfigurowanie środowiska <br /> 2. Zbuduj aplikację internetową |
|**Wdróż** | 1. Wdrażanie aplikacji internetowej |

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

[Jednostkę usługi](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) można utworzyć za pomocą polecenia [az ad sp create-for-rbac](https://docs.microsoft.com/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) w [usłudze Azure CLI](https://docs.microsoft.com/cli/azure/). To polecenie można uruchomić przy użyciu [usługi Azure Cloud Shell](https://shell.azure.com/) w witrynie Azure portal lub wybierając przycisk **Wypróbuj.**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> --sdk-auth
```

W tym przykładzie zastąp symbole zastępcze w zasobie identyfikatorem subskrypcji, nazwą grupy zasobów i nazwą aplikacji. Dane wyjściowe to poświadczenia przypisania roli, które zapewniają dostęp do aplikacji usługi App Service. Skopiuj ten obiekt JSON, którego można użyć do uwierzytelniania z usługi GitHub.

> [!NOTE]
> Nie trzeba tworzyć jednostki usługi, jeśli zdecydujesz się użyć profilu publikowania do uwierzytelniania.

> [!IMPORTANT]
> Zawsze dobrą praktyką jest przyznanie minimalnego dostępu. Dlatego zakres w poprzednim przykładzie jest ograniczony do określonej aplikacji usługi App Service, a nie do całej grupy zasobów.

## <a name="configure-the-github-secret"></a>Konfigurowanie klucza tajnego usługi GitHub

Można również użyć poświadczeń na poziomie aplikacji, czyli profilu publikowania do wdrożenia. Wykonaj kroki, aby skonfigurować klucz tajny:

1. Pobierz profil publikowania aplikacji Usługi app service z portalu przy użyciu opcji **Pobierz profil publikowania.**

2. W [usłudze GitHub](https://github.com/)przeglądaj repozytorium, wybierz **ustawienia > wpisowe > Dodaj nowy klucz tajny**

    ![wpisy tajne](media/app-service-github-actions/secrets.png)

3. Wklej zawartość pobranego pliku profilu publikowania do pola wartości klucza tajnego.

4. Teraz w pliku przepływu pracy `.github/workflows/workflow.yml` w gałęzi: zastąp klucz tajny dla wprowadzania akcji `publish-profile` wdrażania aplikacji Azure Web App.
    
    ```yaml
        - uses: azure/webapps-deploy@v2
          with:
            creds: ${{ secrets.azureWebAppPublishProfile }}
    ```

5. Widzisz klucz tajny, jak pokazano poniżej po zdefiniowaniu.

    ![wpisy tajne](media/app-service-github-actions/app-service-secrets.png)

## <a name="set-up-the-environment"></a>Konfigurowanie środowiska

Konfigurowanie środowiska można wykonać za pomocą jednej z akcji konfiguracji.

|**Język**  |**Akcja instalacji**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

Poniższe przykłady przedstawiają część przepływu pracy, która konfiguruje środowisko dla różnych obsługiwanych języków:

**JavaScript**

```yaml
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```
**Python**

```yaml
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

**.NET**

```yaml
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

**Java**

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

## <a name="build-the-web-app"></a>Tworzenie aplikacji sieci Web

To zależy od języka i dla języków obsługiwanych przez usługę Azure App Service, ta sekcja powinna być standardowe kroki kompilacji każdego języka.

Poniższe przykłady pokazują część przepływu pracy, która tworzy aplikację sieci web w różnych obsługiwanych językach.

**JavaScript**

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please change your directory for npm in pushd
        pushd .
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

**Python**

```yaml
    - name: 'Run pip'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please change your directory for pip in pushd
        pushd .
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/python3.6/site-packages"
        popd
```

**.NET**

```yaml
    - name: 'Run dotnet build'
      shell: bash
      run: |
        # If your web app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

**Java**

```yaml
    - uses: actions/checkout@v1
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
```
## <a name="deploy-to-app-service"></a>Wdrażanie w usłudze App Service

Aby wdrożyć kod w aplikacji usługi `azure/webapps-deploy@v2` App Service, użyj akcji. Ta akcja ma cztery parametry:

| **Parametr**  | **Wyjaśnienie**  |
|---------|---------|
| **nazwa aplikacji** | (Wymagane) Nazwa aplikacji Usługi aplikacji | 
| **publikuj profil** | (Opcjonalnie) Publikowanie zawartości pliku profilu za pomocą wpisów tajnych wdrażania sieci Web |
| **Pakiet** | (Opcjonalnie) Ścieżka do pakietu lub folderu. *.zip, *.war, *.jar lub folder do wdrożenia |
| **nazwa gniazda** | (Opcjonalnie) Wprowadź istniejące gniazdo inne niż gniazdo produkcyjne |

### <a name="deploy-using-publish-profile"></a>Wdrażanie przy użyciu profilu publikowania

Poniżej znajduje się przykładowy przepływ pracy do tworzenia i wdrażania aplikacji Node.js na platformie Azure przy użyciu profilu publikowania.

```yaml
# File: .github/workflows/workflow.yml

on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
    
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
          uses: azure/webapps-deploy@v2
          with: 
            app-name: node-rn
            publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

### <a name="deploy-using-azure-service-principal"></a>Wdrażanie przy użyciu jednostki usługi platformy Azure

Poniżej znajduje się przykładowy przepływ pracy do tworzenia i wdrażania aplikacji Node.js na platformie Azure przy użyciu jednostki usługi platformy Azure.

```yaml
on: [push]

name: Node.js

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@master
   
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
        
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
    
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
               
    # deploy web app using Azure credentials
    - uses: azure/webapps-deploy@v2
      with:
        app-name: 'node-rn'

    # Azure logout 
    - name: logout
      run: |
        az logout
```

## <a name="next-steps"></a>Następne kroki

Możesz znaleźć nasz zestaw akcji pogrupowanych w różne repozytoria w usłudze GitHub, z których każdy zawiera dokumentację i przykłady ułatwiające korzystanie z usługi GitHub dla ciągłej integracji/ciągłego wdrażania i wdrażanie aplikacji na platformie Azure.

- [Przepływ pracy akcji do wdrożenia na platformie Azure](https://github.com/Azure/actions-workflow-samples)

- [Logowanie w usłudze Azure](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Usługa Azure WebApp dla kontenerów](https://github.com/Azure/webapps-container-deploy)

- [Logowanie/wylogowywki platformy Docker](https://github.com/Azure/docker-login)

- [Zdarzenia wyzwalające przepływy pracy](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s wdrożyć](https://github.com/Azure/k8s-deploy)

- [Przepływy pracy rozrusznika](https://github.com/actions/starter-workflows)
