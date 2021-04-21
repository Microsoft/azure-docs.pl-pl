---
title: Konfigurowanie konfiguracji ci/CD za pomocą GitHub Actions
description: Dowiedz się, jak wdrożyć kod w Azure App Service potoku ci/CD za pomocą GitHub Actions. Dostosowywanie zadań kompilacji i wykonywanie złożonych wdrożeń.
ms.devlang: na
ms.topic: article
ms.date: 09/14/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: devx-track-python, github-actions-azure, devx-track-azurecli
ms.openlocfilehash: aa4475ccedfb19ece540337f493bcc5ed64af035
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832468"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Wdrażanie w usłudze App Service przy użyciu funkcji GitHub Actions

Rozpoczynanie pracy z [GitHub Actions](https://docs.github.com/en/actions/learn-github-actions) w celu zautomatyzowania przepływu pracy i wdrożenia w Azure App Service [z](overview.md) usługi GitHub. 

## <a name="prerequisites"></a>Wymagania wstępne 

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Konto usługi GitHub. Jeśli go nie masz, zarejestruj się [bezpłatnie.](https://github.com/join)  
- Robocza Azure App Service aplikacji. 
    - .NET: [tworzenie aplikacji ASP.NET Core na platformie Azure](quickstart-dotnetcore.md)
    - ASP.NET: [tworzenie aplikacji internetowej ASP.NET Framework na platformie Azure](quickstart-dotnet-framework.md)
    - JavaScript: [tworzenie Node.js internetowej w Azure App Service](quickstart-nodejs.md)  
    - Java: [tworzenie aplikacji Java na platformie Azure App Service](quickstart-java.md)
    - Python: [tworzenie aplikacji w języku Python w Azure App Service](quickstart-python.md)

## <a name="workflow-file-overview"></a>Omówienie pliku przepływu pracy

Przepływ pracy jest definiowany przez plik YAML (yml) w `/.github/workflows/` ścieżce w repozytorium. Ta definicja zawiera różne kroki i parametry, które składa się na przepływ pracy.

Plik zawiera trzy sekcje:

|Sekcja  |Zadania  |
|---------|---------|
|**Authentication** | 1. Zdefiniuj jednostkę usługi lub profil publikowania. <br /> 2. Utwórz klucz tajny usługi GitHub. |
|**Kompilacja** | 1. Skonfiguruj środowisko. <br /> 2. Skompilowanie aplikacji internetowej. |
|**Wdrażanie** | 1. Wdrażanie aplikacji internetowej. |

## <a name="use-the-deployment-center"></a>Korzystanie z Centrum wdrażania

Możesz szybko rozpocząć pracę z GitHub Actions przy użyciu centrum App Service wdrażania. Spowoduje to automatyczne wygenerowanie pliku przepływu pracy na podstawie stosu aplikacji i zatwierdzenie go w repozytorium GitHub we właściwym katalogu.

1. Przejdź do swojej aplikacji internetowej w Azure Portal
1. Po lewej stronie kliknij pozycję **Centrum wdrażania.**
1. W **obszarze Ciągłe wdrażanie (CI/CD)** wybierz pozycję **GitHub**
1. Następnie wybierz **pozycję GitHub Actions**
1. Użyj listy rozwijanej, aby wybrać repozytorium, gałąź i stos aplikacji GitHub
    - Jeśli wybrana gałąź jest chroniona, nadal możesz dodać plik przepływu pracy. Przed kontynuowaniem zapoznaj się z zabezpieczeniami gałęzi.
1. Na ekranie końcowym możesz przejrzeć wybrane opcje i wyświetlić podgląd pliku przepływu pracy, który zostanie zatwierdzone w repozytorium. Jeśli wybrane opcje są poprawne, kliknij przycisk **Zakończ**

Spowoduje to zatwierdzenie pliku przepływu pracy w repozytorium. Przepływ pracy kompilowania i wdrażania aplikacji rozpocznie się natychmiast.

## <a name="set-up-a-workflow-manually"></a>Ręczne konfigurowanie przepływu pracy

Możesz również wdrożyć przepływ pracy bez korzystania z Centrum wdrażania. W tym celu należy najpierw wygenerować poświadczenia wdrożenia. 

## <a name="generate-deployment-credentials"></a>Generowanie poświadczeń wdrożenia

Zalecanym sposobem uwierzytelniania za pomocą usługi Azure App Services for GitHub Actions jest użycie profilu publikowania. Można również uwierzytelnić się za pomocą jednostki usługi, ale proces wymaga większej liczby kroków. 

Zapisz poświadczenia profilu publikowania lub jednostkę usługi jako klucz tajny [usługi GitHub w](https://docs.github.com/en/actions/reference/encrypted-secrets) celu uwierzytelnienia na platformie Azure. Będziesz mieć dostęp do tego tajnego w przepływie pracy. 

# <a name="publish-profile"></a>[Publikowanie profilu](#tab/applevel)

Profil publikowania to poświadczenie na poziomie aplikacji. Skonfiguruj profil publikowania jako klucz tajny usługi GitHub. 

1. Przejdź do usługi App Service w Azure Portal. 

1. Na stronie **Przegląd** wybierz pozycję **Pobierz profil publikowania.**

1. Zapisz pobrany plik. Użyjesz zawartości pliku, aby utworzyć klucz tajny usługi GitHub.

> [!NOTE]
> Od października 2020 r. aplikacje internetowe systemu Linux będą potrzebować ustawienia aplikacji ustawionego na `WEBSITE_WEBDEPLOY_USE_SCM` wartość `true` **przed pobraniem profilu publikowania.** To wymaganie zostanie usunięte w przyszłości.

# <a name="service-principal"></a>[Jednostka usługi](#tab/userlevel)

Jednostkę usługi można utworzyć [za](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) pomocą polecenia [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) w interfejsie wiersza polecenia [platformy Azure.](/cli/azure/) Uruchom to polecenie, [Azure Cloud Shell](https://shell.azure.com/) w Azure Portal lub wybierając **przycisk Wypróbuj.**

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
                            --sdk-auth
```

W powyższym przykładzie zastąp symbole zastępcze identyfikatorem subskrypcji, nazwą grupy zasobów i nazwą aplikacji. Dane wyjściowe to obiekt JSON z poświadczeniami przypisania roli, które zapewniają dostęp do aplikacji App Service podobnej do poniższej. Skopiuj ten obiekt JSON na później.

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

## <a name="configure-the-github-secret"></a>Konfigurowanie tajnego repozytorium GitHub


# <a name="publish-profile"></a>[Profil publikowania](#tab/applevel)

W [usłudze GitHub](https://github.com/)przejrzyj repozytorium i wybierz pozycję Ustawienia > wpisy tajne **> Dodaj nowy wpis tajny.**

Aby użyć [poświadczeń na poziomie aplikacji,](#generate-deployment-credentials)wklej zawartość pobranego pliku profilu publikowania w polu wartości tego tajnego. Nadaj tajnej nazwie `AZURE_WEBAPP_PUBLISH_PROFILE` .

Podczas konfigurowania przepływu pracy usługi GitHub użyjesz polecenia w akcji Deploy Azure Web App (Wdrażanie `AZURE_WEBAPP_PUBLISH_PROFILE` aplikacji internetowej platformy Azure). Na przykład:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[Jednostka usługi](#tab/userlevel)

W [usłudze GitHub](https://github.com/)przejrzyj repozytorium i wybierz pozycję Ustawienia > wpisy tajne **> Dodaj nowy wpis tajny.**

Aby użyć [poświadczeń na poziomie użytkownika,](#generate-deployment-credentials)wklej całe dane wyjściowe JSON z polecenia interfejsu wiersza polecenia platformy Azure do pola wartości we kluczu tajnym. Nadaj kluczowi tajnego nazwę `AZURE_CREDENTIALS` .

Podczas późniejszego konfigurowania pliku przepływu pracy użyjesz tajnego hasła dla danych `creds` wejściowych akcji logowania platformy Azure. Na przykład:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="set-up-the-environment"></a>Konfigurowanie środowiska

Konfigurowanie środowiska można wykonać przy użyciu jednej z akcji konfiguracji.

|**Język**  |**Akcja konfiguracji**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**ASP.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

Poniższe przykłady pokazują, jak skonfigurować środowisko dla różnych obsługiwanych języków:

**.NET**

```yaml
    - name: Setup Dotnet 3.3.x
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '3.3.x'
```

**ASP.NET**

```yaml
    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
```

**Java**

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x,
        # change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

**JavaScript**

```yaml
env:
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    name: Build and Deploy
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    - name: Use Node.js ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
```
**Python**

```yaml
    - name: Setup Python 3.x 
      uses: actions/setup-python@v1
      with:
        python-version: 3.x
```

## <a name="build-the-web-app"></a>Tworzenie aplikacji internetowej

Proces budowania aplikacji internetowej i wdrażania jej w celu Azure App Service zmian w zależności od języka. 

W poniższych przykładach przedstawiono część przepływu pracy, która tworzy aplikację internetową, w różnych obsługiwanych językach.

Dla wszystkich języków katalog główny aplikacji internetowej można ustawić za `working-directory` pomocą . 

**.NET**

Zmienna `AZURE_WEBAPP_PACKAGE_PATH` środowiskowa ustawia ścieżkę do projektu aplikacji internetowej. 

```yaml
- name: dotnet build and publish
  run: |
    dotnet restore
    dotnet build --configuration Release
    dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
```
**ASP.NET**

Możesz przywrócić zależności NuGet i uruchomić msbuild za pomocą programu `run` . 

```yaml
- name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
  run: nuget restore

- name: Add msbuild to PATH
  uses: microsoft/setup-msbuild@v1.0.2

- name: Run msbuild
  run: msbuild .\SampleWebApplication.sln
```

**Java**

```yaml
- name: Build with Maven
  run: mvn package --file pom.xml
```

**JavaScript**

Na Node.js można ustawić lub `working-directory` zmienić katalog npm w katalogu `pushd` . 

```yaml
- name: npm install, build, and test
  run: |
    npm install
    npm run build --if-present
    npm run test --if-present
  working-directory: my-app-folder # set to the folder with your app if it is not the root directory
```

**Python**

```yaml
- name: Install dependencies
  run: |
    python -m pip install --upgrade pip
    pip install -r requirements.txt
```


## <a name="deploy-to-app-service"></a>Wdrażanie w usłudze App Service

Aby wdrożyć kod w App Service aplikacji, użyj `azure/webapps-deploy@v2` akcji . Ta akcja ma cztery parametry:

| **Parametr**  | **Wyjaśnienie**  |
|---------|---------|
| **nazwa aplikacji** | (Wymagane) Nazwa aplikacji App Service aplikacji | 
| **publish-profile** | (Opcjonalnie) Publikowanie zawartości pliku profilu przy użyciu Web Deploy tajnych |
| **Pakiet** | (Opcjonalnie) Ścieżka do pakietu lub folderu. Ścieżka może zawierać pliki *.zip, *.war, *.jar lub folder do wdrożenia |
| **nazwa-miejsca** | (Opcjonalnie) Wprowadź istniejące miejsce inne niż miejsce [produkcyjne](deploy-staging-slots.md) |


# <a name="publish-profile"></a>[Publikowanie profilu](#tab/applevel)

### <a name="net-core"></a>.NET Core

Kompilowanie i wdrażanie aplikacji platformy .NET Core na platformie Azure przy użyciu profilu publikowania platformy Azure. Dane `publish-profile` wejściowe odwołuje się do `AZURE_WEBAPP_PUBLISH_PROFILE` utworzonego wcześniej tajnego.

```yaml
name: .NET Core CI

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app-name    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '3.1.x'           # set this to the dot net version to use

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # Checkout the repo
      - uses: actions/checkout@main
      
      # Setup .NET Core SDK
      - name: Setup .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }} 
      
      # Run dotnet build and publish
      - name: dotnet build and publish
        run: |
          dotnet restore
          dotnet build --configuration Release
          dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
          
      # Deploy to Azure Web apps
      - name: 'Run Azure webapp deploy action using publish profile credentials'
        uses: azure/webapps-deploy@v2
        with: 
          app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
          publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE  }} # Define secret variable in repository settings as per action documentation
          package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp'
```

### <a name="aspnet"></a>ASP.NET

Skompilować i wdrożyć ASP.NET MVC, która używa programu NuGet i `publish-profile` do uwierzytelniania. 


```yaml
name: Deploy ASP.NET MVC App deploy to Azure Web App

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NUGET_VERSION: '5.3.x'           # set this to the dot net version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:

    - uses: actions/checkout@main  
    
    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
    - name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
      run: nuget restore
  
    - name: Add msbuild to PATH
      uses: microsoft/setup-msbuild@v1.0.2

    - name: Run MSBuild
      run: msbuild .\SampleWebApplication.sln
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE  }} # Define secret variable in repository settings as per action documentation
        package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/SampleWebApplication/'
```

### <a name="java"></a>Java

Tworzenie i wdrażanie aplikacji Java Spring na platformie Azure przy użyciu profilu publikowania platformy Azure. Dane `publish-profile` wejściowe odwołuje się do `AZURE_WEBAPP_PUBLISH_PROFILE` utworzonego wcześniej tajnego.

```yaml
name: Java CI with Maven

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
      working-directory: my-app-path
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.jar
```

Aby wdrożyć `war` zamiast , zmień wartość `jar` `package` . 


```yaml
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.war
```

### <a name="javascript"></a>JavaScript 

Tworzenie i wdrażanie aplikacji Node.js na platformie Azure przy użyciu profilu publikowania aplikacji. Dane `publish-profile` wejściowe odwołuje się do `AZURE_WEBAPP_PUBLISH_PROFILE` utworzonego wcześniej tajnego.

```yaml
# File: .github/workflows/workflow.yml
name: JavaScript CI

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app-name   # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: 'my-app-path'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    name: Build and Deploy
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@main
    - name: Use Node.js ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
    - name: npm install, build, and test
      run: |
        # Build and test the project, then
        # deploy to Azure Web App.
        npm install
        npm run build --if-present
        npm run test --if-present
      working-directory: my-app-path
    - name: 'Deploy to Azure WebApp'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
```

### <a name="python"></a>Python 

Skompilowanie i wdrożenie aplikacji w języku Python na platformie Azure przy użyciu profilu publikowania aplikacji. Zwróć uwagę, jak `publish-profile` dane wejściowe odwołuje się do `AZURE_WEBAPP_PUBLISH_PROFILE` utworzonego wcześniej tajnego.

```yaml
name: Python CI

on:
  [push]

env:
  AZURE_WEBAPP_NAME: my-web-app # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Set up Python 3.x
      uses: actions/setup-python@v2
      with:
        python-version: 3.x
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Building web app
      uses: azure/appservice-build@v2
    - name: Deploy web App using GH Action azure/webapps-deploy
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
```

# <a name="service-principal"></a>[Jednostka usługi](#tab/userlevel)

### <a name="net-core"></a>.NET Core 

Kompilowanie i wdrażanie aplikacji platformy .NET Core na platformie Azure przy użyciu jednostki usługi platformy Azure. Zwróć uwagę, jak `creds` dane wejściowe odwołuje się do `AZURE_CREDENTIALS` utworzonego wcześniej tajnego.


```yaml
name: .NET Core

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '3.1.x'           # set this to the dot net version to use

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      # Checkout the repo
      - uses: actions/checkout@main
      - uses: azure/login@v1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      
      # Setup .NET Core SDK
      - name: Setup .NET Core
        uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ env.DOTNET_VERSION }} 
      
      # Run dotnet build and publish
      - name: dotnet build and publish
        run: |
          dotnet restore
          dotnet build --configuration Release
          dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
          
      # Deploy to Azure Web apps
      - name: 'Run Azure webapp deploy action using publish profile credentials'
        uses: azure/webapps-deploy@v2
        with: 
          app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
          package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp'
      
      - name: logout
        run: |
          az logout
```

### <a name="aspnet"></a>ASP.NET

Kompilowanie i wdrażanie aplikacji ASP.NET MVC na platformie Azure przy użyciu jednostki usługi platformy Azure. Zwróć uwagę, jak `creds` dane wejściowe odwołuje się do `AZURE_CREDENTIALS` utworzonego wcześniej tajnego.

```yaml
name: Deploy ASP.NET MVC App deploy to Azure Web App

on: [push]

env:
  AZURE_WEBAPP_NAME: my-app    # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NUGET_VERSION: '5.3.x'           # set this to the dot net version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:

    # checkout the repo
    - uses: actions/checkout@main
    
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
    - name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
      run: nuget restore
  
    - name: Add msbuild to PATH
      uses: microsoft/setup-msbuild@v1.0.2

    - name: Run MSBuild
      run: msbuild .\SampleWebApplication.sln
       
    - name: 'Run Azure webapp deploy action using publish profile credentials'
      uses: azure/webapps-deploy@v2
      with: 
        app-name: ${{ env.AZURE_WEBAPP_NAME }} # Replace with your app name
        package: '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/SampleWebApplication/'
  
    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="java"></a>Java 

Kompilowanie i wdrażanie aplikacji Java Spring na platformie Azure przy użyciu jednostki usługi platformy Azure. Zwróć uwagę, jak `creds` dane wejściowe odwołuje się do `AZURE_CREDENTIALS` utworzonego wcześniej tajnego.

```yaml
name: Java CI with Maven

on: [push]

jobs:
  build:

    runs-on: ubuntu-latest

    steps:
    - uses: actions/checkout@v2
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    - name: Build with Maven
      run: mvn -B package --file pom.xml
      working-directory: complete
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        package: my/target/*.jar

    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="javascript"></a>JavaScript 

Kompilowanie i wdrażanie aplikacji Node.js na platformie Azure przy użyciu jednostki usługi platformy Azure. Zwróć uwagę, jak `creds` dane wejściowe odwołuje się do `AZURE_CREDENTIALS` utworzonego wcześniej tajnego.

```yaml
name: JavaScript CI

on: [push]

name: Node.js

env:
  AZURE_WEBAPP_NAME: my-app   # set this to your application's name
  NODE_VERSION: '14.x'                # set this to the node version to use

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    # checkout the repo
    - name: 'Checkout GitHub Action' 
      uses: actions/checkout@main
   
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
        
    - name: Setup Node ${{ env.NODE_VERSION }}
      uses: actions/setup-node@v1
      with:
        node-version: ${{ env.NODE_VERSION }}
    
    - name: 'npm install, build, and test'
      run: |
        npm install
        npm run build --if-present
        npm run test --if-present
      working-directory:  my-app-path
               
    # deploy web app using Azure credentials
    - uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}

    # Azure logout 
    - name: logout
      run: |
        az logout
```

### <a name="python"></a>Python 

Kompilowanie i wdrażanie aplikacji w języku Python na platformie Azure przy użyciu jednostki usługi platformy Azure. Zwróć uwagę, jak `creds` dane wejściowe odwołuje się do `AZURE_CREDENTIALS` utworzonego wcześniej tajnego.

```yaml
name: Python application

on:
  [push]

env:
  AZURE_WEBAPP_NAME: my-app # set this to your application's name
  AZURE_WEBAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    
    - uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}

    - name: Set up Python 3.x
      uses: actions/setup-python@v2
      with:
        python-version: 3.x
    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt
    - name: Deploy web App using GH Action azure/webapps-deploy
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ env.AZURE_WEBAPP_NAME }}
        package: ${{ env.AZURE_WEBAPP_PACKAGE_PATH }}
    - name: logout
      run: |
        az logout
```


---

## <a name="next-steps"></a>Następne kroki

Nasz zestaw akcji pogrupowanych w różne repozytoria można znaleźć w witrynie GitHub, z których każde zawiera dokumentację i przykłady, które ułatwiają korzystanie z usługi GitHub na użytek funkcji ci/CD i wdrażanie aplikacji na platformie Azure.

- [Przepływy pracy akcji do wdrożenia na platformie Azure](https://github.com/Azure/actions-workflow-samples)

- [Logowanie w usłudze Azure](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Usługa Azure WebApp dla kontenerów](https://github.com/Azure/webapps-container-deploy)

- [Logowanie/wyloguj się z platformy Docker](https://github.com/Azure/docker-login)

- [Zdarzenia wyzwalające przepływy pracy](https://docs.github.com/en/actions/reference/events-that-trigger-workflows)

- [Wdrażanie k8s](https://github.com/Azure/k8s-deploy)

- [Początkowe przepływy pracy](https://github.com/actions/starter-workflows)
