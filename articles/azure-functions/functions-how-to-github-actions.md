---
title: Korzystanie z akcji usługi GitHub w celu wprowadzania aktualizacji kodu w Azure Functions
description: Dowiedz się, jak używać akcji usługi GitHub do definiowania przepływu pracy do kompilowania i wdrażania projektów Azure Functions w usłudze GitHub.
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python, github-actions-azure
ms.openlocfilehash: ae3ffb9dc387129153b684a849ba4e58274ad80c
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97563283"
---
# <a name="continuous-delivery-by-using-github-action"></a>Ciągłe dostarczanie za pomocą akcji GitHub

Użyj [akcji usługi GitHub](https://github.com/features/actions) , aby zdefiniować przepływ pracy umożliwiający automatyczne Kompilowanie i wdrażanie kodu w aplikacji funkcji platformy Azure. 

W akcjach usługi GitHub [przepływ pracy](https://help.github.com/articles/about-github-actions#workflow) to zautomatyzowany proces zdefiniowany w repozytorium GitHub. Ten proces zawiera informacje dotyczące kompilowania i wdrażania projektu aplikacji usługi Functions w witrynie GitHub. 

Przepływ pracy jest definiowany przez plik YAML (. yml) w `/.github/workflows/` ścieżce w repozytorium. Ta definicja zawiera różne kroki i parametry wchodzące w skład przepływu pracy. 

W przypadku przepływu pracy Azure Functions plik ma trzy sekcje: 

| Sekcja | Zadania |
| ------- | ----- |
| **Authentication** | Pobierz profil publikowania.<br/>Utwórz wpis tajny usługi GitHub.|
| **Kompilacja** | Skonfiguruj środowisko.<br/>Kompiluj aplikację funkcji.|
| **Wdrażanie** | Wdróż aplikację funkcji.|

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Konto usługi GitHub. Jeśli nie masz takiego konta, zarejestruj się [bezpłatnie](https://github.com/join).  
- Aplikacja funkcji działającej hostowana na platformie Azure z repozytorium GitHub.   
    - [Szybki start: Tworzenie funkcji na platformie Azure przy użyciu programu Visual Studio Code](./create-first-function-vs-code-csharp.md)

## <a name="generate-deployment-credentials"></a>Generuj poświadczenia wdrożenia

Zalecanym sposobem uwierzytelniania przy użyciu Azure Functions na potrzeby akcji usługi GitHub jest użycie profilu publikowania. Można także uwierzytelnić się za pomocą nazwy głównej usługi. Aby dowiedzieć się więcej, zobacz [to repozytorium akcji](https://github.com/Azure/functions-action)w witrynie GitHub. 

Po zapisaniu poświadczenia profilu publikowania jako [wpisu tajnego usługi GitHub](https://docs.github.com/en/actions/reference/encrypted-secrets)będziesz używać tego klucza tajnego w ramach przepływu pracy do uwierzytelniania za pomocą platformy Azure. 

#### <a name="download-your-publish-profile"></a>Pobieranie profilu publikowania

Aby pobrać profil publikowania aplikacji funkcji:

1. Wybierz stronę **Przegląd** aplikacji funkcji, a następnie wybierz pozycję **Pobierz profil publikowania**.

   :::image type="content" source="media/functions-how-to-github-actions/get-publish-profile.png" alt-text="Pobierz profil publikowania":::

1. Zapisz i skopiuj zawartość pliku.


### <a name="add-the-github-secret"></a>Dodawanie wpisu tajnego usługi GitHub

1. W witrynie [GitHub](https://github.com)przejdź do repozytorium, wybierz pozycję **Ustawienia** wpisy  >  **tajne**  >  **Dodaj nowy wpis tajny**.

   :::image type="content" source="media/functions-how-to-github-actions/add-secret.png" alt-text="Dodaj klucz tajny":::

1. Dodaj nowy wpis tajny przy użyciu `AZURE_FUNCTIONAPP_PUBLISH_PROFILE` **nazwy**, zawartości pliku profilu publikowania dla **wartości**, a następnie wybierz pozycję **Dodaj klucz tajny**.

Usługa GitHub umożliwia teraz uwierzytelnianie w aplikacji funkcji na platformie Azure.

## <a name="create-the-environment"></a>Tworzenie środowiska 

Konfigurowanie środowiska odbywa się przy użyciu akcji konfiguracji publikowania specyficznego dla języka.

# <a name="net"></a>[.NET](#tab/dotnet)

Program .NET (w tym ASP.NET) używa tej `actions/setup-dotnet` akcji.  
Poniższy przykład przedstawia część przepływu pracy, który konfiguruje środowisko:

```yaml
    - name: Setup DotNet 2.2.402 Environment
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: 2.2.402
```

# <a name="java"></a>[Java](#tab/java)

Środowisko Java używa tej  `actions/setup-java` akcji.  
Poniższy przykład przedstawia część przepływu pracy, który konfiguruje środowisko:

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

W języku JavaScript (Node.js) jest stosowana `actions/setup-node` Akcja.  
Poniższy przykład przedstawia część przepływu pracy, który konfiguruje środowisko:

```yaml

    - name: Setup Node 12.x Environment
      uses: actions/setup-node@v1
      with:
        node-version: 12.x
```

# <a name="python"></a>[Python](#tab/python)

Środowisko Python używa tej `actions/setup-python` akcji.  
Poniższy przykład przedstawia część przepływu pracy, który konfiguruje środowisko:

```yaml
    - name: Setup Python 3.7 Environment
      uses: actions/setup-python@v1
      with:
        python-version: 3.7
```
---

## <a name="build-the-function-app"></a>Tworzenie aplikacji funkcji

Jest to zależne od języka i języków obsługiwanych przez Azure Functions Ta sekcja powinna być standardowym etapem kompilacji każdego języka.

Poniższy przykład przedstawia część przepływu pracy, który kompiluje aplikację funkcji, która jest specyficzna dla języka:

# <a name="net"></a>[.NET](#tab/dotnet)

```yaml
    env:
      AZURE_FUNCTIONAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

    - name: 'Resolve Project Dependencies Using Dotnet'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        dotnet build --configuration Release --output ./output
        popd
```

# <a name="java"></a>[Java](#tab/java)

```yaml
    env:
      POM_XML_DIRECTORY: '.'  # set this to the directory which contains pom.xml file

    - name: 'Restore Project Dependencies Using Mvn'
      shell: bash
      run: |
        pushd './${{ env.POM_XML_DIRECTORY }}'
        mvn clean package
        mvn azure-functions:package
        popd
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```yaml
    env:
      AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'  # set this to the path to your web app project, defaults to the repository root

    - name: 'Resolve Project Dependencies Using Npm'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

# <a name="python"></a>[Python](#tab/python)

```yaml
    env:
      AZURE_FUNCTIONAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

    - name: 'Resolve Project Dependencies Using Pip'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/site-packages"
        popd
```
---

## <a name="deploy-the-function-app"></a>Wdrażanie aplikacji funkcji
Użyj `Azure/functions-action` akcji, aby wdrożyć kod w aplikacji funkcji. Ta akcja ma trzy parametry:

|Parametr |Wyjaśnienie  |
|---------|---------|
|_**Nazwa aplikacji**_ | Wypełnione Nazwa aplikacji funkcji. |
|_**Nazwa gniazda**_ | Obowiązkowe Nazwa [miejsca wdrożenia](functions-deployment-slots.md) , które ma zostać wdrożone. Gniazdo musi być już zdefiniowane w aplikacji funkcji. |
|_**Publikuj — profil**_ | Obowiązkowe Nazwa wpisu tajnego usługi GitHub dla profilu publikowania. |

W poniższym przykładzie użyta zostanie wersja 1 programu `functions-action` i `publish profile` na potrzeby uwierzytelniania 

# <a name="net"></a>[.NET](#tab/dotnet)

Skonfiguruj przepływ pracy programu .NET Linux, który używa profilu publikowania.

```yaml
name: Deploy DotNet project to Azure function app with a Linux environment

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name  # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'    # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '2.2.402'              # set this to the dotnet version to use

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup DotNet ${{ env.DOTNET_VERSION }} Environment
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: ${{ env.DOTNET_VERSION }}

    - name: 'Resolve Project Dependencies Using Dotnet'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        dotnet build --configuration Release --output ./output
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: '${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}/output'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```
Skonfiguruj przepływ pracy programu .NET dla systemu Windows, który używa profilu publikowania.

```yaml
name: Deploy DotNet project to Azure function app with a Windows environment

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name  # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'    # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '2.2.402'              # set this to the dotnet version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup DotNet ${{ env.DOTNET_VERSION }} Environment
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: ${{ env.DOTNET_VERSION }}

    - name: 'Resolve Project Dependencies Using Dotnet'
      shell: pwsh
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        dotnet build --configuration Release --output ./output
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: '${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}/output'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

# <a name="java"></a>[Java](#tab/java)

Skonfiguruj przepływ pracy środowiska Java Linux, który używa profilu publikowania.

```yaml
name: Deploy Java project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name      # set this to your function app name on Azure
  POM_XML_DIRECTORY: '.'                     # set this to the directory which contains pom.xml file
  POM_FUNCTIONAPP_NAME: your-app-name        # set this to the function app name in your local development environment
  JAVA_VERSION: '1.8.x'                      # set this to the dotnet version to use

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup Java Sdk ${{ env.JAVA_VERSION }}
      uses: actions/setup-java@v1
      with:
        java-version: ${{ env.JAVA_VERSION }}

    - name: 'Restore Project Dependencies Using Mvn'
      shell: bash
      run: |
        pushd './${{ env.POM_XML_DIRECTORY }}'
        mvn clean package
        mvn azure-functions:package
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: './${{ env.POM_XML_DIRECTORY }}/target/azure-functions/${{ env.POM_FUNCTIONAPP_NAME }}'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

Skonfiguruj przepływ pracy systemu Windows w języku Java, który używa profilu publikowania.

```yaml
name: Deploy Java project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name      # set this to your function app name on Azure
  POM_XML_DIRECTORY: '.'                     # set this to the directory which contains pom.xml file
  POM_FUNCTIONAPP_NAME: your-app-name        # set this to the function app name in your local development environment
  JAVA_VERSION: '1.8.x'                      # set this to the java version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup Java Sdk ${{ env.JAVA_VERSION }}
      uses: actions/setup-java@v1
      with:
        java-version: ${{ env.JAVA_VERSION }}

    - name: 'Restore Project Dependencies Using Mvn'
      shell: pwsh
      run: |
        pushd './${{ env.POM_XML_DIRECTORY }}'
        mvn clean package
        mvn azure-functions:package
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: './${{ env.POM_XML_DIRECTORY }}/target/azure-functions/${{ env.POM_FUNCTIONAPP_NAME }}'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Skonfiguruj przepływ pracy Node.JS systemu Linux, który używa profilu publikowania.

```yaml
name: Deploy Node.js project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name    # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '12.x'                     # set this to the node version to use (supports 8.x, 10.x, 12.x)

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup Node ${{ env.NODE_VERSION }} Environment
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}

    - name: 'Resolve Project Dependencies Using Npm'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: ${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

Skonfiguruj Node.JS przepływ pracy systemu Windows, który używa profilu publikowania.

```yaml
name: Deploy Node.js project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name    # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '10.x'                     # set this to the node version to use (supports 8.x, 10.x, 12.x)

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup Node ${{ env.NODE_VERSION }} Environment
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}

    - name: 'Resolve Project Dependencies Using Npm'
      shell: pwsh
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: ${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}

```
# <a name="python"></a>[Python](#tab/python)

Skonfiguruj przepływ pracy w języku Python systemu Linux, który używa profilu publikowania.

```yaml
name: Deploy Python project to Azure Function App

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'   # set this to the path to your web app project, defaults to the repository root
  PYTHON_VERSION: '3.7'                 # set this to the python version to use (supports 3.6, 3.7, 3.8)

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@main

    - name: Setup Python ${{ env.PYTHON_VERSION }} Environment
      uses: actions/setup-python@v1
      with:
        python-version: ${{ env.PYTHON_VERSION }}

    - name: 'Resolve Project Dependencies Using Pip'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/site-packages"
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: ${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

---

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o integracji z platformą Azure i usługą GitHub](/azure/developer/github/)