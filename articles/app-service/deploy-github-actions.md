---
title: Konfigurowanie ciągłej integracji/ciągłego wdrażania za pomocą akcji GitHub
description: Dowiedz się, jak wdrożyć kod w celu Azure App Service z potoku ciągłej integracji/ciągłego wdrażania za pomocą akcji GitHub. Dostosuj zadania kompilacji i wykonuj złożone wdrożenia.
ms.devlang: na
ms.topic: article
ms.date: 10/25/2019
ms.author: jafreebe
ms.reviewer: ushan
ms.custom: devx-track-python
ms.openlocfilehash: 54e4ce409eb9f2a6bedd7861b3e268311f886b49
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91273249"
---
# <a name="deploy-to-app-service-using-github-actions"></a>Wdrażanie do App Service przy użyciu akcji usługi GitHub

Dzięki [akcjom GitHub](https://help.github.com/en/articles/about-github-actions) można tworzyć zautomatyzowane przepływy pracy tworzenia oprogramowania. Za pomocą Azure App Service działań w witrynie GitHub można zautomatyzować przepływ pracy w celu wdrożenia w [Azure App Service](overview.md) za pomocą akcji usługi GitHub.

> [!IMPORTANT]
> Akcje usługi GitHub są obecnie dostępne w wersji beta. Musisz najpierw [utworzyć konto, aby dołączyć do wersji zapoznawczej](https://github.com/features/actions) przy użyciu konta usługi GitHub.
> 

Przepływ pracy jest definiowany przez plik YAML (. yml) w `/.github/workflows/` ścieżce w repozytorium. Ta definicja zawiera różne kroki i parametry wchodzące w skład przepływu pracy.

## <a name="use-the-deployment-center"></a>Korzystanie z centrum wdrażania

Możesz szybko rozpocząć pracę z akcjami usługi GitHub za pomocą centrum wdrażania App Service. Spowoduje to automatyczne wygenerowanie pliku przepływu pracy na podstawie stosu aplikacji i zatwierdzenie go do repozytorium GitHub w prawidłowym katalogu.

1. Przejdź do webapp w witrynie Azure Portal
1. Po lewej stronie kliknij pozycję **centrum wdrażania** .
1. W obszarze **ciągłe wdrażanie (Ci/CD)** wybierz pozycję **GitHub**
1. Następnie wybierz pozycję **Akcje GitHub**
1. Użyj listy rozwijanej, aby wybrać repozytorium GitHub, gałąź i stos aplikacji
    - Jeśli wybrana gałąź jest chroniona, nadal możesz dodać plik przepływu pracy. Przed kontynuowaniem należy zapoznać się z ochroną oddziałów.
1. Na ekranie końcowym można przejrzeć wybrane opcje i wyświetlić podgląd pliku przepływu pracy, który zostanie przekazany do repozytorium. Jeśli wybrane opcje są poprawne, kliknij przycisk **Zakończ** .

Spowoduje to zatwierdzenie pliku przepływu pracy do repozytorium. Przepływ pracy do kompilowania i wdrażania aplikacji rozpocznie się natychmiast.

## <a name="add-the-workflow-manually"></a>Ręczne dodawanie przepływu pracy

W przypadku przepływu pracy Azure App Service plik ma trzy sekcje:

|Sekcja  |Zadania  |
|---------|---------|
|**Authentication** | 1. Zdefiniuj nazwę główną usługi. <br /> 2. Utwórz wpis tajny usługi GitHub. |
|**Kompilacja** | 1. Skonfiguruj środowisko. <br /> 2. skompiluj aplikację sieci Web. |
|**Wdrażanie** | 1. Wdróż aplikację internetową. |

## <a name="generate-deployment-credentials"></a>Generuj poświadczenia wdrożenia

# <a name="user-level-credentials"></a>[Poświadczenia na poziomie użytkownika](#tab/userlevel)

[Nazwę główną usługi](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) można utworzyć przy użyciu polecenia [AZ AD Sp Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) w [interfejsie użytkownika platformy Azure](/cli/azure/). Można uruchomić to polecenie przy użyciu [Azure Cloud Shell](https://shell.azure.com/) w Azure Portal lub wybierając przycisk **Wypróbuj** .

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

# <a name="app-level-credentials"></a>[Poświadczenia na poziomie aplikacji](#tab/applevel)

Poświadczeń na poziomie aplikacji można używać przy użyciu profilu publikowania aplikacji. Przejdź do strony zarządzania aplikacji w portalu. Na stronie **Przegląd** kliknij opcję **Pobierz profil publikowania** .

Zawartość pliku będzie potrzebna później.

---

## <a name="configure-the-github-secret"></a>Konfigurowanie wpisu tajnego usługi GitHub

# <a name="user-level-credentials"></a>[Poświadczenia na poziomie użytkownika](#tab/userlevel)

W witrynie [GitHub](https://github.com/)Przejrzyj repozytorium, wybierz pozycję **Ustawienia > wpisy tajne > Dodaj nowe hasło**.

Aby użyć [poświadczeń na poziomie użytkownika](#generate-deployment-credentials), Wklej wszystkie dane wyjściowe JSON z polecenia platformy Azure w polu wartość klucza tajnego. Podaj tajną nazwę, taką jak `AZURE_CREDENTIALS` .

Podczas późniejszej konfiguracji pliku przepływu pracy należy użyć wpisu tajnego dla danych wejściowych `creds` akcji logowania platformy Azure. Na przykład:

```yaml
- uses: azure/login@v1
  with:
    creds: ${{ secrets.AZURE_CREDENTIALS }}
```

# <a name="app-level-credentials"></a>[Poświadczenia na poziomie aplikacji](#tab/applevel)

W witrynie [GitHub](https://github.com/)Przejrzyj repozytorium, wybierz pozycję **Ustawienia > wpisy tajne > Dodaj nowe hasło**.

Aby użyć [poświadczeń na poziomie aplikacji](#generate-deployment-credentials), wklej zawartość pobranego pliku profilu publikowania w polu wartość klucza tajnego. Podaj tajną nazwę, taką jak `azureWebAppPublishProfile` .

Podczas późniejszej konfiguracji pliku przepływu pracy należy użyć wpisu tajnego dla danych wejściowych `publish-profile` akcji Wdróż aplikację sieci Web platformy Azure. Na przykład:
    
```yaml
- uses: azure/webapps-deploy@v2
  with:
    publish-profile: ${{ secrets.azureWebAppPublishProfile }}
```

---

## <a name="set-up-the-environment"></a>Konfigurowanie środowiska

Konfigurowanie środowiska można wykonać przy użyciu jednej z akcji instalacji.

|**Język**  |**Akcja konfiguracji**  |
|---------|---------|
|**.NET**     | `actions/setup-dotnet` |
|**Java**     | `actions/setup-java` |
|**JavaScript** | `actions/setup-node` |
|**Python**     | `actions/setup-python` |

W poniższych przykładach przedstawiono część przepływu pracy, który konfiguruje środowisko dla różnych obsługiwanych języków:

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

## <a name="build-the-web-app"></a>Kompilowanie aplikacji sieci Web

Jest to zależne od języka i języków obsługiwanych przez Azure App Service Ta sekcja powinna być standardowym etapem kompilacji każdego języka.

W poniższych przykładach przedstawiono część przepływu pracy, który kompiluje aplikację sieci Web, w różnych obsługiwanych językach.

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

Aby wdrożyć kod w aplikacji App Service, użyj `azure/webapps-deploy@v2` akcji. Ta akcja ma cztery parametry:

| **Parametr**  | **Objaśnienie**  |
|---------|---------|
| **Nazwa aplikacji** | Potrzeb Nazwa aplikacji App Service | 
| **Publikuj — profil** | Obowiązkowe Publikuj zawartość pliku profilu za pomocą wpisów tajnych Web Deploy |
| **Package** | Obowiązkowe Ścieżka do pakietu lub folderu. *. zip, *. War, *. jar lub folder do wdrożenia |
| **Nazwa gniazda** | Obowiązkowe Wprowadź istniejące miejsce poza miejscem produkcyjnym |

# <a name="user-level-credentials"></a>[Poświadczenia na poziomie użytkownika](#tab/userlevel)

Poniżej znajduje się przykładowy przepływ pracy do kompilowania i wdrażania aplikacji Node.js na platformie Azure przy użyciu nazwy głównej usługi platformy Azure. Zwróć uwagę na to, jak `creds` dane wejściowe odwołują się do `AZURE_CREDENTIALS` wpisu tajnego, który został utworzony wcześniej.

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

# <a name="app-level-credentials"></a>[Poświadczenia na poziomie aplikacji](#tab/applevel)

Poniżej znajduje się przykładowy przepływ pracy do kompilowania i wdrażania aplikacji Node.js na platformie Azure przy użyciu profilu publikowania aplikacji. Zwróć uwagę na to, jak `publish-profile` dane wejściowe odwołują się do `azureWebAppPublishProfile` wpisu tajnego, który został utworzony wcześniej.

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