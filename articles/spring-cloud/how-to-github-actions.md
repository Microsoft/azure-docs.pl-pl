---
title: Azure Spring Cloud ci/CD za pomocą GitHub Actions
description: Jak utworzyć przepływ pracy ci/CD na Azure Spring Cloud za pomocą GitHub Actions
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: c52279108a8fd8d5a7ac8bbd7c8eb215097b21b0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791359"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>Azure Spring Cloud ci/CD za pomocą GitHub Actions

GitHub Actions obsługuje zautomatyzowany przepływ pracy cyklu życia tworzenia oprogramowania. Dzięki GitHub Actions dla Azure Spring Cloud można tworzyć przepływy pracy w repozytorium w celu kompilowania, testowania, testowania, tworzenia pakietów, wydania i wdrażania na platformie Azure. 

## <a name="prerequisites"></a>Wymagania wstępne
Ten przykład wymaga interfejsu wiersza [polecenia platformy Azure.](/cli/azure/install-azure-cli)

::: zone pivot="programming-language-csharp"
## <a name="set-up-github-repository-and-authenticate"></a>Konfigurowanie repozytorium GitHub i uwierzytelnianie
Do autoryzowania akcji logowania platformy Azure potrzebne jest poświadczenie jednostki usługi platformy Azure. Aby uzyskać poświadczenia platformy Azure, wykonaj następujące polecenia na maszynie lokalnej:

```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```

Aby uzyskać dostęp do określonej grupy zasobów, można zmniejszyć zakres:

```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

Polecenie powinno zwrócić obiekt JSON:

```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

W tym przykładzie użyto [próbki ze zębowiska w usłudze GitHub.](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample)  Zakłoń repozytorium, otwórz stronę repozytorium GitHub dla tego widelecu, a następnie wybierz **kartę** Ustawienia. Otwórz menu **Wpisy tajne** i wybierz pozycję Nowy **wpis tajny:**

 ![Dodawanie nowego tajnego](./media/github-actions/actions1.png)

Ustaw nazwę tajnego na , a jego wartość na ciąg JSON znaleziony pod nagłówkiem Konfigurowanie repozytorium `AZURE_CREDENTIALS` *GitHub i uwierzytelnianie*.

 ![Ustawianie danych tajnych](./media/github-actions/actions2.png)

Możesz również uzyskać poświadczenia logowania platformy Azure z witryny Key Vault w usłudze GitHub Actions, jak wyjaśniono w te tematu Authenticate Azure Spring with Key Vault in GitHub Actions (Uwierzytelnianie platformy Azure Spring za pomocą usługi [Key Vault w usłudze GitHub Actions.](./spring-cloud-github-actions-key-vault.md)

## <a name="provision-service-instance"></a>Aprowizuj wystąpienie usługi
Aby aprowizować Azure Spring Cloud usługi, uruchom następujące polecenia przy użyciu interfejsu wiersza polecenia platformy Azure.

```azurecli
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/Azure-Spring-Cloud-Samples --label main --search-paths steeltoe-sample/config
```

## <a name="build-the-workflow"></a>Tworzenie przepływu pracy
Przepływ pracy jest definiowany przy użyciu następujących opcji.

### <a name="prepare-for-deployment-with-azure-cli"></a>Przygotowywanie do wdrożenia przy użyciu interfejsu wiersza polecenia platformy Azure

Polecenie `az spring-cloud app create` nie jest obecnie idempotentne. Po uruchomieniu go raz wystąpi błąd, jeśli ponownie zostanie uruchomione to samo polecenie. Zalecamy ten przepływ pracy dla istniejących Azure Spring Cloud i wystąpień.

Do przygotowania użyj następujących poleceń interfejsu wiersza polecenia platformy Azure:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name planet-weather-provider
az spring-cloud app create --name solar-system-weather
```

### <a name="deploy-with-azure-cli-directly"></a>Bezpośrednie wdrażanie za pomocą interfejsu wiersza polecenia platformy Azure

Utwórz `.github/workflows/main.yml` plik w repozytorium z następującą zawartością. Zastąp `<your resource group name>` wartości i `<your service name>` prawidłowymi wartościami.

```yaml
name: Steeltoe-CD

# Controls when the action will run. Triggers the workflow on push or pull request
# events but only for the main branch
on:
  push:
    branches: [ main]

# A workflow run is made up of one or more jobs that can run sequentially or in parallel
jobs:
  # This workflow contains a single job called "build"
  build:
    # The type of runner that the job will run on
    runs-on: ubuntu-latest
    env:
      working-directory: ./steeltoe-sample
      resource-group-name: <your resource group name>
      service-name: <your service name>
    
    # Supported .NET Core version matrix.
    strategy:
      matrix:
        dotnet: [ '3.1.x' ]

    # Steps represent a sequence of tasks that will be executed as part of the job
    steps:
      # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
      - uses: actions/checkout@v2

      # Set up .NET Core 3.1 SDK
      - uses: actions/setup-dotnet@v1
        with:
          dotnet-version: ${{ matrix.dotnet }}
          
      # Set credential for az login
      - uses: azure/login@v1.1
        with:
          creds: ${{ secrets.AZURE_CREDENTIALS }}

      - name: install Azure CLI extension
        run: |
          az extension add --name spring-cloud --yes
      
      - name: Build and package planet-weather-provider app
        working-directory: ${{env.working-directory}}/src/planet-weather-provider
        run: |
          dotnet publish
          az spring-cloud app deploy -n planet-weather-provider --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll --artifact-path ./publish-deploy-planet.zip -s ${{ env.service-name }} -g ${{ env.resource-group-name }}
      - name: Build solar-system-weather app
        working-directory: ${{env.working-directory}}/src/solar-system-weather
        run: |
          dotnet publish
          az spring-cloud app deploy -n solar-system-weather --runtime-version NetCore_31 --main-entry Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll --artifact-path ./publish-deploy-solar.zip -s ${{ env.service-name }} -g ${{ env.resource-group-name }}
```
::: zone-end

::: zone pivot="programming-language-java"
## <a name="set-up-github-repository-and-authenticate"></a>Konfigurowanie repozytorium GitHub i uwierzytelnianie
Do autoryzowania akcji logowania platformy Azure potrzebne jest poświadczenie jednostki usługi platformy Azure. Aby uzyskać poświadczenia platformy Azure, wykonaj następujące polecenia na maszynie lokalnej:
```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```
Aby uzyskać dostęp do określonej grupy zasobów, można zmniejszyć zakres:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```
Polecenie powinno zwrócić obiekt JSON:
```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

W tym przykładzie użyto [przykładu PiggyMetrics](https://github.com/Azure-Samples/piggymetrics) w witrynie GitHub.  Zakłoń przykład, otwórz stronę repozytorium GitHub i kliknij **kartę** Ustawienia. Otwórz menu **Wpisy** tajne i kliknij **pozycję Dodaj nowy wpis tajny:**

 ![Dodawanie nowego tajnego](./media/github-actions/actions1.png)

Ustaw nazwę tajnego na , a jego wartość na ciąg JSON znaleziony pod nagłówkiem Konfigurowanie repozytorium `AZURE_CREDENTIALS` *GitHub i uwierzytelnianie*.

 ![Ustawianie danych tajnych](./media/github-actions/actions2.png)

Możesz również uzyskać poświadczenia logowania platformy Azure z witryny Key Vault w usłudze GitHub Actions, jak wyjaśniono w te tematu Authenticate Azure Spring with Key Vault in GitHub Actions (Uwierzytelnianie platformy Azure Spring za pomocą usługi [Key Vault w usłudze GitHub Actions.](./spring-cloud-github-actions-key-vault.md)

## <a name="provision-service-instance"></a>Aprowizuj wystąpienie usługi
Aby aprowizować Azure Spring Cloud usługi, uruchom następujące polecenia przy użyciu interfejsu wiersza polecenia platformy Azure.
```
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```
## <a name="build-the-workflow"></a>Kompilowanie przepływu pracy
Przepływ pracy jest definiowany przy użyciu następujących opcji.

### <a name="prepare-for-deployment-with-azure-cli"></a>Przygotowywanie do wdrożenia przy użyciu interfejsu wiersza polecenia platformy Azure
Polecenie `az spring-cloud app create` nie jest obecnie idempotentne.  Zalecamy ten przepływ pracy dla istniejących Azure Spring Cloud aplikacji i wystąpień.

Do przygotowania użyj następujących poleceń interfejsu wiersza polecenia platformy Azure:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name gateway
az spring-cloud app create --name auth-service
az spring-cloud app create --name account-service
```

### <a name="deploy-with-azure-cli-directly"></a>Bezpośrednie wdrażanie za pomocą interfejsu wiersza polecenia platformy Azure
Utwórz `.github/workflow/main.yml` plik w repozytorium:

```
name: AzureSpringCloud
on: push

env:
  GROUP: <resource group name>
  SERVICE_NAME: <service instance name>

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
    
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
      
    - name: Install ASC AZ extension
      run: az extension add --name spring-cloud
   
    - name: Deploy with AZ CLI commands
      run: |
        az configure --defaults group=$GROUP
        az configure --defaults spring-cloud=$SERVICE_NAME
        az spring-cloud app deploy -n gateway --jar-path ${{ github.workspace }}/gateway/target/gateway.jar
        az spring-cloud app deploy -n account-service --jar-path ${{ github.workspace }}/account-service/target/account-service.jar
        az spring-cloud app deploy -n auth-service --jar-path ${{ github.workspace }}/auth-service/target/auth-service.jar
```
### <a name="deploy-with-azure-cli-action"></a>Akcja Deploy with Azure CLI (Wdrażanie za pomocą interfejsu wiersza polecenia platformy Azure)
Polecenie az `run` będzie używać najnowszej wersji interfejsu wiersza polecenia platformy Azure. Jeśli występują istotne zmiany, możesz również użyć określonej wersji interfejsu wiersza polecenia platformy Azure za pomocą polecenia `action` azure/CLI. 

> [!Note] 
> To polecenie zostanie uruchomione w nowym kontenerze, więc nie będzie działać, a dostęp do plików między akcjami może `env` mieć dodatkowe ograniczenia.

Utwórz plik .github/workflow/main.yml w repozytorium:
```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
        
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
              
    - name: Azure CLI script
      uses: azure/CLI@v1
      with:
        azcliversion: 2.0.75
        inlineScript: |
          az extension add --name spring-cloud
          az configure --defaults group=<service group name>
          az configure --defaults spring-cloud=<service instance name>
          az spring-cloud app deploy -n gateway --jar-path $GITHUB_WORKSPACE/gateway/target/gateway.jar
          az spring-cloud app deploy -n account-service --jar-path $GITHUB_WORKSPACE/account-service/target/account-service.jar
          az spring-cloud app deploy -n auth-service --jar-path $GITHUB_WORKSPACE/auth-service/target/auth-service.jar
```

## <a name="deploy-with-maven-plugin"></a>Wdrażanie za pomocą wtyczki Maven
Inną opcją jest użycie wtyczki [Maven do](./spring-cloud-quickstart.md) wdrażania pliku Jar i aktualizowania ustawień aplikacji. Polecenie jest `mvn azure-spring-cloud:deploy` idempotentne i w razie potrzeby automatycznie utworzy aplikacje. Nie musisz tworzyć odpowiednich aplikacji z wyprzedzeniem.

```
name: AzureSpringCloud
on: push

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    
    - uses: actions/checkout@main
    
    - name: Set up JDK 1.8
      uses: actions/setup-java@v1
      with:
        java-version: 1.8
    
    - name: maven build, clean
      run: |
        mvn clean package -DskipTests
        
    # Maven plugin can cosume this authentication method automatically
    - name: Azure Login
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Maven deploy, make sure you have correct configurations in your pom.xml
    - name: deploy to Azure Spring Cloud using Maven
      run: |
        mvn azure-spring-cloud:deploy
```

::: zone-end

## <a name="run-the-workflow"></a>Uruchamianie przepływu pracy

Funkcja GitHub **Actions** powinna zostać włączona automatycznie po wypchnięciu `.github/workflow/main.yml` do usługi GitHub. Akcja zostanie wyzwolona po wypchnąć nowe zatwierdzenie. Jeśli utworzysz ten plik w przeglądarce, akcja powinna być już uruchamiana.

Aby sprawdzić, czy akcja została włączona, kliknij **kartę Akcje** na stronie repozytorium GitHub:

![Weryfikowanie włączonej akcji](./media/github-actions/actions3.png)

Jeśli akcja jest uruchamiana z błędem, na przykład jeśli nie ustawiono poświadczeń platformy Azure, możesz ponownie uruchomić kontrole po naprawieniu błędu. Na stronie repozytorium GitHub kliknij pozycję **Actions**(Akcje), wybierz określone zadanie przepływu pracy, a następnie kliknij przycisk Rerun checks (Ponownie wytniej **kontrole),** aby ponownie uruchomić kontrole:

![Testy ponownego przeprowadzania](./media/github-actions/actions4.png)

## <a name="next-steps"></a>Następne kroki

* [Key Vault dla Spring Cloud GitHub Actions](./spring-cloud-github-actions-key-vault.md)
* [Azure Active Directory jednostki usługi](/cli/azure/ad/sp#az_ad_sp_create_for_rbac)
* [Akcje GitHub Actions dla platformy Azure](https://github.com/Azure/actions/)
