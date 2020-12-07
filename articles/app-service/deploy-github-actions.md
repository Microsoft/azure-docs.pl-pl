---
title: Konfigurowanie ciągłej integracji/ciągłego wdrażania za pomocą akcji GitHub
description: Dowiedz się, jak wdrożyć kod w celu Azure App Service z potoku ciągłej integracji/ciągłego wdrażania za pomocą akcji GitHub. Dostosuj zadania kompilacji i wykonuj złożone wdrożenia.
ms.devlang: na
ms.topic: article
ms.date: 09/14/2020
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: devx-track-python, github-actions-azure, devx-track-azurecli
ms.openlocfilehash: 16a21acabfd199ba16068e507919b564f01a76d5
ms.sourcegitcommit: d6e92295e1f161a547da33999ad66c94cf334563
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/07/2020
ms.locfileid: "96763914"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Wdrażanie do App Service przy użyciu akcji usługi GitHub

Rozpocznij pracę z [akcjami usługi GitHub](https://help.github.com/en/articles/about-github-actions) , aby zautomatyzować przepływ pracy i wdrożyć go w [Azure App Service](overview.md) z usługi GitHub. 

## <a name="prerequisites"></a>Wymagania wstępne 

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Konto usługi GitHub. Jeśli nie masz takiego konta, zarejestruj się [bezpłatnie](https://github.com/join).  
- Działająca aplikacja Azure App Service. 
    - .NET: [Tworzenie aplikacji internetowej ASP.NET Core na platformie Azure](quickstart-dotnetcore.md)
    - ASP.NET: [Tworzenie aplikacji sieci Web platformy ASP.NET Framework na platformie Azure](quickstart-dotnet-framework.md)
    - JavaScript: [Tworzenie aplikacji internetowej Node.js w programie Azure App Service](quickstart-nodejs.md)  
    - Java: [Tworzenie aplikacji Java na Azure App Service](quickstart-java.md)
    - Python: [Tworzenie aplikacji w języku Python w Azure App Service](quickstart-python.md)

## <a name="workflow-file-overview"></a>Omówienie pliku przepływu pracy

Przepływ pracy jest definiowany przez plik YAML (. yml) w `/.github/workflows/` ścieżce w repozytorium. Ta definicja zawiera różne kroki i parametry wchodzące w skład przepływu pracy.

Plik ma trzy sekcje:

|Sekcja  |Zadania  |
|---------|---------|
|**Authentication** | 1. Zdefiniuj nazwę główną usługi lub profil publikacji. <br /> 2. Utwórz wpis tajny usługi GitHub. |
|**Kompilacja** | 1. Skonfiguruj środowisko. <br /> 2. skompiluj aplikację sieci Web. |
|**Wdrażanie** | 1. Wdróż aplikację internetową. |

## <a name="use-the-deployment-center"></a>Korzystanie z centrum wdrażania

Możesz szybko rozpocząć pracę z akcjami usługi GitHub za pomocą centrum wdrażania App Service. Spowoduje to automatyczne wygenerowanie pliku przepływu pracy na podstawie stosu aplikacji i zatwierdzenie go do repozytorium GitHub w prawidłowym katalogu.

1. Przejdź do webapp w Azure Portal
1. Po lewej stronie kliknij pozycję **centrum wdrażania** .
1. W obszarze **ciągłe wdrażanie (Ci/CD)** wybierz pozycję **GitHub**
1. Następnie wybierz pozycję **Akcje GitHub**
1. Użyj listy rozwijanej, aby wybrać repozytorium GitHub, gałąź i stos aplikacji
    - Jeśli wybrana gałąź jest chroniona, nadal możesz dodać plik przepływu pracy. Przed kontynuowaniem należy zapoznać się z ochroną oddziałów.
1. Na ekranie końcowym można przejrzeć wybrane opcje i wyświetlić podgląd pliku przepływu pracy, który zostanie przekazany do repozytorium. Jeśli wybrane opcje są poprawne, kliknij przycisk **Zakończ** .

Spowoduje to zatwierdzenie pliku przepływu pracy do repozytorium. Przepływ pracy do kompilowania i wdrażania aplikacji rozpocznie się natychmiast.

## <a name="set-up-a-workflow-manually"></a>Ręczne konfigurowanie przepływu pracy

Możesz również wdrożyć przepływ pracy bez korzystania z centrum wdrażania. W tym celu należy najpierw wygenerować poświadczenia wdrożenia. 

## <a name="generate-deployment-credentials"></a>Generuj poświadczenia wdrożenia

Zalecanym sposobem uwierzytelniania przy użyciu usługi Azure App Services na potrzeby akcji GitHub jest profil publikowania. Można także uwierzytelnić się za pomocą nazwy głównej usługi, ale proces wymaga większej liczby kroków. 

Zapisz poświadczenia profilu publikowania lub nazwę główną usługi jako [wpis tajny serwisu GitHub](https://docs.github.com/en/actions/reference/encrypted-secrets) , aby uwierzytelnić się na platformie Azure. Będziesz uzyskiwać dostęp do wpisu tajnego w ramach przepływu pracy. 

# <a name="publish-profile"></a>[Publikuj profil](#tab/applevel)

Profil publikowania to poświadczenie na poziomie aplikacji. Skonfiguruj swój profil publikowania jako wpis tajny usługi GitHub. 

1. Przejdź do usługi App Service w Azure Portal. 

1. Na stronie **Przegląd** wybierz pozycję **Pobierz profil publikowania**.

1. Zapisz pobrany plik. Zawartość pliku zostanie użyta do utworzenia wpisu tajnego usługi GitHub.

> [!NOTE]
> Od 2020 października aplikacje sieci Web dla systemu Linux będą potrzebować ustawienia aplikacji `WEBSITE_WEBDEPLOY_USE_SCM` ustawionego na `true` **przed pobraniem profilu publikowania**. To wymaganie zostanie usunięte w przyszłości.

# <a name="service-principal"></a>[Jednostka usługi](#tab/userlevel)

Za pomocą polecenia [AZ AD Sp Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac&preserve-view=true) można utworzyć jednostkę [usługi](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) [.](/cli/azure/) Uruchom to polecenie z [Azure Cloud Shell](https://shell.azure.com/) w Azure Portal lub wybierając przycisk **Wypróbuj** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name>/providers/Microsoft.Web/sites/<app-name> \
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

> [!IMPORTANT]
> Zawsze dobrym sposobem jest przyznanie minimalnego dostępu. Zakres w poprzednim przykładzie jest ograniczony do konkretnej aplikacji App Service, a nie całej grupy zasobów.

---

## <a name="configure-the-github-secret"></a>Konfigurowanie wpisu tajnego usługi GitHub


# <a name="publish-profile"></a>[Publikuj profil](#tab/applevel)

W witrynie [GitHub](https://github.com/)Przejrzyj repozytorium, wybierz pozycję **Ustawienia > wpisy tajne > Dodaj nowe hasło**.

Aby użyć [poświadczeń na poziomie aplikacji](#generate-deployment-credentials), wklej zawartość pobranego pliku profilu publikowania w polu wartość klucza tajnego. Nazwij klucz tajny `AZURE_WEBAPP_PUBLISH_PROFILE` .

Podczas konfigurowania przepływu pracy w usłudze GitHub należy użyć `AZURE_WEBAPP_PUBLISH_PROFILE` akcji w obszarze Wdróż aplikację sieci Web platformy Azure. Na przykład:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```

# <a name="service-principal"></a>[Jednostka usługi](#tab/userlevel)

W witrynie [GitHub](https://github.com/)Przejrzyj repozytorium, wybierz pozycję **Ustawienia > wpisy tajne > Dodaj nowe hasło**.

Aby użyć [poświadczeń na poziomie użytkownika](#generate-deployment-credentials), Wklej wszystkie dane wyjściowe JSON z polecenia platformy Azure w polu wartość klucza tajnego. Podaj klucz tajny jako nazwę `AZURE_CREDENTIALS` .

Podczas późniejszej konfiguracji pliku przepływu pracy należy użyć wpisu tajnego dla danych wejściowych `creds` akcji logowania platformy Azure. Na przykład:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

---

## <a name="set-up-the-environment"></a>Konfigurowanie środowiska

Konfigurowanie środowiska można wykonać przy użyciu jednej z akcji instalacji.

|**Język**  |**Akcja konfiguracji**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**ASP.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

W poniższych przykładach pokazano, jak skonfigurować środowisko dla różnych obsługiwanych języków:

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
    - uses: actions/checkout@master
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

## <a name="build-the-web-app"></a>Kompilowanie aplikacji sieci Web

Proces tworzenia aplikacji sieci Web i wdrażania jej w celu Azure App Service zmian w zależności od języka. 

W poniższych przykładach przedstawiono część przepływu pracy, który kompiluje aplikację sieci Web, w różnych obsługiwanych językach.

Dla wszystkich języków można ustawić katalog główny aplikacji sieci Web za pomocą `working-directory` . 

**.NET**

Zmienna środowiskowa `AZURE_WEBAPP_PACKAGE_PATH` ustawia ścieżkę do projektu aplikacji sieci Web. 

```yaml
- name: dotnet build and publish
  run: |
    dotnet restore
    dotnet build --configuration Release
    dotnet publish -c Release -o '${{ env.AZURE_WEBAPP_PACKAGE_PATH }}/myapp' 
```
**ASP.NET**

Można przywrócić zależności NuGet i uruchomić MSBuild z `run` . 

```yaml
- name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
  run: nuget restore

- name: Add msbuild to PATH
  uses: microsoft/setup-msbuild@v1.0.0

- name: Run msbuild
  run: msbuild .\SampleWebApplication.sln
```

**Java**

```yaml
- name: Build with Maven
  run: mvn package --file pom.xml
```

**JavaScript**

W przypadku Node.js można ustawić `working-directory` lub zmienić dla katalogu npm w `pushd` . 

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

Aby wdrożyć kod w aplikacji App Service, użyj `azure/webapps-deploy@v2` akcji. Ta akcja ma cztery parametry:

| **Parametr**  | **Wyjaśnienie**  |
|---------|---------|
| **Nazwa aplikacji** | Potrzeb Nazwa aplikacji App Service | 
| **Publikuj — profil** | Obowiązkowe Publikuj zawartość pliku profilu za pomocą wpisów tajnych Web Deploy |
| **package** | Obowiązkowe Ścieżka do pakietu lub folderu. Ścieżka może zawierać plik *. zip, *. War, *. jar lub folder do wdrożenia |
| **Nazwa gniazda** | Obowiązkowe Wprowadź istniejące miejsce poza [miejscem](deploy-staging-slots.md) produkcyjnym |


# <a name="publish-profile"></a>[Publikuj profil](#tab/applevel)

### <a name="net-core"></a>.NET Core

Kompilowanie i wdrażanie aplikacji .NET Core na platformie Azure przy użyciu profilu publikowania platformy Azure. `publish-profile`Dane wejściowe odwołują się do `AZURE_WEBAPP_PUBLISH_PROFILE` wpisu tajnego, który został utworzony wcześniej.

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
      - uses: actions/checkout@master
      
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

Kompilowanie i wdrażanie aplikacji ASP.NET MVC korzystającej z NuGet i `publish-profile` uwierzytelniania. 


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

    - uses: actions/checkout@master  
    
    - name: Install Nuget
      uses: nuget/setup-nuget@v1
      with:
        nuget-version: ${{ env.NUGET_VERSION}}
    - name: NuGet to restore dependencies as well as project-specific tools that are specified in the project file
      run: nuget restore
  
    - name: Add msbuild to PATH
      uses: microsoft/setup-msbuild@v1.0.0

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

Kompiluj i Wdrażaj aplikację ze sprężyną Java na platformie Azure przy użyciu profilu publikowania platformy Azure. `publish-profile`Dane wejściowe odwołują się do `AZURE_WEBAPP_PUBLISH_PROFILE` wpisu tajnego, który został utworzony wcześniej.

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

Aby wdrożyć a `war` zamiast `jar` , należy zmienić `package` wartość. 


```yaml
    - name: Azure WebApp
      uses: Azure/webapps-deploy@v2
      with:
        app-name: my-app-name
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
        package: my/target/*.war
```

### <a name="javascript"></a>JavaScript 

Kompilowanie i wdrażanie aplikacji Node.js na platformie Azure przy użyciu profilu publikowania aplikacji. `publish-profile`Dane wejściowe odwołują się do `AZURE_WEBAPP_PUBLISH_PROFILE` wpisu tajnego, który został utworzony wcześniej.

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
    - uses: actions/checkout@master
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

Kompiluj i Wdrażaj aplikację w języku Python na platformie Azure przy użyciu profilu publikowania aplikacji. Zwróć uwagę na to, jak `publish-profile` dane wejściowe odwołują się do `AZURE_WEBAPP_PUBLISH_PROFILE` wpisu tajnego, który został utworzony wcześniej.

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

Kompilowanie i wdrażanie aplikacji .NET Core na platformie Azure przy użyciu jednostki usługi platformy Azure. Zwróć uwagę na to, jak `creds` dane wejściowe odwołują się do `AZURE_CREDENTIALS` wpisu tajnego, który został utworzony wcześniej.


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
      - uses: actions/checkout@master
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

Kompiluj i Wdróż aplikację ASP.NET MVC na platformie Azure przy użyciu nazwy głównej usługi platformy Azure. Zwróć uwagę na to, jak `creds` dane wejściowe odwołują się do `AZURE_CREDENTIALS` wpisu tajnego, który został utworzony wcześniej.

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
    - uses: actions/checkout@master  
    
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
      uses: microsoft/setup-msbuild@v1.0.0

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

Kompiluj i Wdrażaj aplikację ze sprężyną Java na platformie Azure przy użyciu nazwy głównej usługi platformy Azure. Zwróć uwagę na to, jak `creds` dane wejściowe odwołują się do `AZURE_CREDENTIALS` wpisu tajnego, który został utworzony wcześniej.

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

Kompilowanie i wdrażanie aplikacji Node.js na platformie Azure przy użyciu nazwy głównej usługi platformy Azure. Zwróć uwagę na to, jak `creds` dane wejściowe odwołują się do `AZURE_CREDENTIALS` wpisu tajnego, który został utworzony wcześniej.

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
      uses: actions/checkout@master
   
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

Kompiluj i Wdrażaj aplikację w języku Python na platformie Azure przy użyciu nazwy głównej usługi platformy Azure. Zwróć uwagę na to, jak `creds` dane wejściowe odwołują się do `AZURE_CREDENTIALS` wpisu tajnego, który został utworzony wcześniej.

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

Zestaw akcji można znaleźć w różnych repozytoriach w usłudze GitHub, z których każda zawiera dokumentację i przykłady ułatwiające korzystanie z usługi GitHub w przypadku ciągłej integracji/ciągłego wdrażania oraz wdrażanie aplikacji na platformie Azure.

- [Przepływy pracy akcji do wdrożenia na platformie Azure](https://github.com/Azure/actions-workflow-samples)

- [Logowanie w usłudze Azure](https://github.com/Azure/login)

- [Azure WebApp](https://github.com/Azure/webapps-deploy)

- [Usługa Azure WebApp dla kontenerów](https://github.com/Azure/webapps-container-deploy)

- [Logowanie/wylogowywanie platformy Docker](https://github.com/Azure/docker-login)

- [Zdarzenia wyzwalające przepływy pracy](https://help.github.com/en/articles/events-that-trigger-workflows)

- [K8s wdrażanie](https://github.com/Azure/k8s-deploy)

- [Początkowe przepływy pracy](https://github.com/actions/starter-workflows)
