---
title: Korzystanie z akcji usługi GitHub w celu wprowadzania aktualizacji kodu w Azure Functions
description: Dowiedz się, jak używać akcji usługi GitHub do definiowania przepływu pracy do kompilowania i wdrażania projektów Azure Functions w usłudze GitHub.
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: cshoe
ms.openlocfilehash: 54010269e5b61ebf28a29dd3165c4310f3472817
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80878208"
---
# <a name="continuous-delivery-by-using-github-action"></a>Ciągłe dostarczanie za pomocą akcji GitHub

[Akcje GitHub](https://github.com/features/actions) umożliwiają zdefiniowanie przepływu pracy w celu automatycznego kompilowania i wdrażania kodu funkcji w aplikacji funkcji na platformie Azure. 

W akcjach usługi GitHub [przepływ pracy](https://help.github.com/articles/about-github-actions#workflow) to zautomatyzowany proces zdefiniowany w repozytorium GitHub. Ten proces zawiera informacje dotyczące kompilowania i wdrażania projektu aplikacji usługi Functions w witrynie GitHub. 

Przepływ pracy jest definiowany przez plik YAML (. yml) w `/.github/workflows/` ścieżce w repozytorium. Ta definicja zawiera różne kroki i parametry wchodzące w skład przepływu pracy. 

W przypadku przepływu pracy Azure Functions plik ma trzy sekcje: 

| Sekcja | Zadania |
| ------- | ----- |
| **Uwierzytelnianie** | <ol><li>Zdefiniuj nazwę główną usługi.</li><li>Pobierz profil publikowania.</li><li>Utwórz wpis tajny usługi GitHub.</li></ol>|
| **Kompilacja** | <ol><li>Skonfiguruj środowisko.</li><li>Kompiluj aplikację funkcji.</li></ol> |
| **Wdróż** | <ol><li>Wdróż aplikację funkcji.</li></ol>|

> [!NOTE]
> Nie trzeba tworzyć jednostki usługi, jeśli zdecydujesz się na użycie profilu publikowania na potrzeby uwierzytelniania.

## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

[Nazwę główną usługi](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object) można utworzyć przy użyciu polecenia [AZ AD Sp Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) w [interfejsie użytkownika platformy Azure](/cli/azure/). Można uruchomić to polecenie przy użyciu [Azure Cloud Shell](https://shell.azure.com) w Azure Portal lub wybierając przycisk **Wypróbuj** .

```azurecli-interactive
az ad sp create-for-rbac --name "myApp" --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP>/providers/Microsoft.Web/sites/<APP_NAME> --sdk-auth
```

W tym przykładzie Zastąp symbole zastępcze w zasobie IDENTYFIKATORem subskrypcji, grupą zasobów i nazwą aplikacji funkcji. Dane wyjściowe to poświadczenia przypisania roli, które zapewniają dostęp do aplikacji funkcji. Skopiuj ten obiekt JSON, którego możesz użyć do uwierzytelniania z usługi GitHub.

> [!IMPORTANT]
> Zawsze dobrym sposobem jest przyznanie minimalnego dostępu. Dlatego zakres w poprzednim przykładzie jest ograniczony do określonej aplikacji funkcji, a nie całej grupy zasobów.

## <a name="download-the-publishing-profile"></a>Pobieranie profilu publikowania

Profil publikowania aplikacji funkcji można pobrać, przechodząc do strony **przeglądu** aplikacji i klikając pozycję **Pobierz profil publikowania**.

   ![Pobierz profil publikowania](media/functions-how-to-github-actions/get-publish-profile.png)

Skopiuj zawartość pliku.

## <a name="configure-the-github-secret"></a>Konfigurowanie wpisu tajnego usługi GitHub

1. W witrynie [GitHub](https://github.com)przejdź do repozytorium, wybierz pozycję **Ustawienia** > wpisy**tajne** > **Dodaj nowy wpis tajny**.

   ![Dodaj klucz tajny](media/functions-how-to-github-actions/add-secret.png)

1. Dodaj nowy wpis tajny.

   * Jeśli używasz jednostki usługi, która została utworzona przy użyciu interfejsu wiersza polecenia platformy Azure, użyj `AZURE_CREDENTIALS` jako **nazwy**. Następnie wklej skopiowany dane wyjściowe obiektu JSON dla **wartości**, a następnie wybierz pozycję **Dodaj klucz tajny**.
   * Jeśli używasz profilu publikowania, użyj `SCM_CREDENTIALS` jako **nazwy**. Następnie użyj zawartości pliku profilu publikowania dla **wartości**, a następnie wybierz pozycję **Dodaj klucz tajny**.

Usługa GitHub umożliwia teraz uwierzytelnianie w aplikacji funkcji na platformie Azure.

## <a name="set-up-the-environment"></a>Konfigurowanie środowiska 

Konfigurowanie środowiska odbywa się przy użyciu akcji konfiguracji publikowania specyficznego dla języka.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Poniższy przykład przedstawia część przepływu pracy, który używa `actions/setup-node` akcji do skonfigurowania środowiska:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Node 10.x
      uses: actions/setup-node@v1
      with:
        node-version: '10.x'
```

# <a name="python"></a>[Python](#tab/python)

Poniższy przykład przedstawia część przepływu pracy, który używa `actions/setup-python` akcji do skonfigurowania środowiska:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Python 3.6
      uses: actions/setup-python@v1
      with:
        python-version: 3.6
```

# <a name="c"></a>[S #](#tab/csharp)

Poniższy przykład przedstawia część przepływu pracy, który używa `actions/setup-dotnet` akcji do skonfigurowania środowiska:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Dotnet 2.2.300
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: '2.2.300'
```

# <a name="java"></a>[Java](#tab/java)

Poniższy przykład przedstawia część przepływu pracy, który używa `actions/setup-java` akcji do skonfigurowania środowiska:

```yaml
    - name: 'Login via Azure CLI'
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```
---

## <a name="build-the-function-app"></a>Tworzenie aplikacji funkcji

Jest to zależne od języka i języków obsługiwanych przez Azure Functions Ta sekcja powinna być standardowym etapem kompilacji każdego języka.

Poniższy przykład przedstawia część przepływu pracy, który kompiluje aplikację funkcji, która jest specyficzna dla języka:

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```yaml
    - name: 'Run npm'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for npm in pushd
        pushd .
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

# <a name="python"></a>[Python](#tab/python)

```yaml
    - name: 'Run pip'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for pip in pushd
        pushd .
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/python3.6/site-packages"
        popd
```

# <a name="c"></a>[S #](#tab/csharp)

```yaml
    - name: 'Run dotnet build'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please consider using pushd to change your path
        pushd .
        dotnet build --configuration Release --output ./output
        popd
```

# <a name="java"></a>[Java](#tab/java)

```yaml
    - name: 'Run mvn'
      shell: bash
      run: |
        # If your function app project is not located in your repository's root
        # Please change your directory for maven build in pushd
        pushd . ./POM_ARTIFACT_ID
        mvn clean package
        mvn azure-functions:package
        popd
```
---

## <a name="deploy-the-function-app"></a>Wdrażanie aplikacji funkcji

Aby wdrożyć kod w aplikacji funkcji, musisz użyć `Azure/functions-action` akcji. Ta akcja ma dwa parametry:

|Parametr |Wyjaśnienie  |
|---------|---------|
|**_Nazwa aplikacji_** | Wypełnione Nazwa aplikacji funkcji. |
|_**Nazwa gniazda**_ | Obowiązkowe Nazwa [miejsca wdrożenia](functions-deployment-slots.md) , które ma zostać wdrożone. Gniazdo musi być już zdefiniowane w aplikacji funkcji. |


W poniższym przykładzie jest `functions-action`używane wersja 1:

```yaml
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: PLEASE_REPLACE_THIS_WITH_YOUR_FUNCTION_APP_NAME
```

## <a name="next-steps"></a>Następne kroki

Aby wyświetlić kompletny przepływ pracy. YAML, zobacz jeden z plików w [repozytorium przykładów przepływu pracy akcji usługi Azure GitHub](https://aka.ms/functions-actions-samples) , który `functionapp` ma nazwę. Możesz użyć tych przykładów jako punktu wyjścia dla przepływu pracy.

> [!div class="nextstepaction"]
> [Dowiedz się więcej o akcjach usługi GitHub](https://help.github.com/en/articles/about-github-actions)
