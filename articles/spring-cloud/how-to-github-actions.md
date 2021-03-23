---
title: Ciągłej integracji/ciągłego wdrażania w chmurze platformy Azure za pomocą akcji GitHub
description: Jak utworzyć przepływ pracy ciągłej integracji/ciągłego wdrażania w chmurze Azure z akcjami GitHub
author: MikeDodaro
ms.author: barbkess
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/08/2020
ms.custom: devx-track-java, devx-track-azurecli
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: 8400fcacbfa4c76aceb079b788255e3d3b83ce33
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104878295"
---
# <a name="azure-spring-cloud-cicd-with-github-actions"></a>Ciągłej integracji/ciągłego wdrażania w chmurze platformy Azure za pomocą akcji GitHub

Akcje usługi GitHub obsługują zautomatyzowany przepływ pracy tworzenia oprogramowania. Dzięki akcjom GitHub dla chmury Azure wiosny możesz tworzyć przepływy pracy w repozytorium, aby kompilować, testować, wdrażać, wydawania i wdrożyć je na platformie Azure. 

## <a name="prerequisites"></a>Wymagania wstępne
Ten przykład wymaga [interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

::: zone pivot="programming-language-csharp"
## <a name="set-up-github-repository-and-authenticate"></a>Skonfiguruj repozytorium GitHub i uwierzytelniaj
Potrzebujesz poświadczenia jednostki usługi platformy Azure, aby autoryzować akcję logowania platformy Azure. Aby uzyskać poświadczenia platformy Azure, wykonaj następujące polecenia na komputerze lokalnym:

```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```

Aby uzyskać dostęp do określonej grupy zasobów, można zmniejszyć zakres:

```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```

Polecenie powinno wykonać wyjście obiektu JSON:

```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

Ten przykład używa [przykładu steeltoe w witrynie GitHub](https://github.com/Azure-Samples/Azure-Spring-Cloud-Samples/tree/master/steeltoe-sample).  Utwórz rozwidlenie repozytorium, Otwórz stronę repozytorium GitHub dla rozwidlenia, a następnie wybierz kartę **Ustawienia** . Otwórz menu wpisy **tajne** i wybierz pozycję **Nowy wpis tajny**:

 ![Dodaj nowy wpis tajny](./media/github-actions/actions1.png)

W polu Nazwa klucza tajnego Ustaw `AZURE_CREDENTIALS` wartość i jej wartości na ciąg JSON znaleziony w nagłówku *Skonfiguruj repozytorium GitHub i Uwierzytelnij*.

 ![Ustawianie danych tajnych](./media/github-actions/actions2.png)

Możesz również uzyskać poświadczenia logowania platformy Azure z Key Vault w ramach akcji usługi GitHub, jak wyjaśniono w temacie [uwierzytelnianie Azure wiosna z Key Vault w akcjach GitHub](./spring-cloud-github-actions-key-vault.md).

## <a name="provision-service-instance"></a>Inicjowanie obsługi administracyjnej wystąpienia usługi
Aby zainicjować obsługę administracyjną wystąpienia usługi w chmurze ze sprężyną Azure, uruchom następujące polecenia przy użyciu interfejsu wiersza polecenia platformy Azure.

```azurecli
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/Azure-Spring-Cloud-Samples --label main --search-paths steeltoe-sample/config
```

## <a name="build-the-workflow"></a>Kompilowanie przepływu pracy
Przepływ pracy jest definiowany przy użyciu następujących opcji.

### <a name="prepare-for-deployment-with-azure-cli"></a>Przygotowanie do wdrożenia za pomocą interfejsu wiersza polecenia platformy Azure

Polecenie `az spring-cloud app create` nie jest obecnie idempotentne. Po uruchomieniu tego polecenia zostanie wyświetlony komunikat o błędzie, jeśli ponownie uruchomisz to samo polecenie. Zalecamy korzystanie z tego przepływu pracy w istniejących aplikacjach i wystąpieniach w chmurze usługi Azure wiosennej.

Użyj następujących poleceń interfejsu wiersza polecenia platformy Azure na potrzeby przygotowania:
```
az configure --defaults group=<service group name>
az configure --defaults spring-cloud=<service instance name>
az spring-cloud app create --name planet-weather-provider
az spring-cloud app create --name solar-system-weather
```

### <a name="deploy-with-azure-cli-directly"></a>Bezpośrednie wdrażanie za pomocą interfejsu wiersza polecenia platformy Azure

Utwórz `.github/workflows/main.yml` plik w repozytorium z następującą zawartością. Zamień `<your resource group name>` i `<your service name>` z prawidłowymi wartościami.

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
## <a name="set-up-github-repository-and-authenticate"></a>Skonfiguruj repozytorium GitHub i uwierzytelniaj
Potrzebujesz poświadczenia jednostki usługi platformy Azure, aby autoryzować akcję logowania platformy Azure. Aby uzyskać poświadczenia platformy Azure, wykonaj następujące polecenia na komputerze lokalnym:
```
az login
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID> --sdk-auth 
```
Aby uzyskać dostęp do określonej grupy zasobów, można zmniejszyć zakres:
```
az ad sp create-for-rbac --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP> --sdk-auth
```
Polecenie powinno wykonać wyjście obiektu JSON:
```JSON
{
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    ...
}
```

Ten przykład używa przykładu [PiggyMetrics](https://github.com/Azure-Samples/piggymetrics) w witrynie GitHub.  Rozwidlenie przykładu, Otwórz stronę repozytorium GitHub, a następnie kliknij kartę **Ustawienia** . Otwórz menu **klucze tajne** i kliknij pozycję **Dodaj nowy wpis tajny**:

 ![Dodaj nowy wpis tajny](./media/github-actions/actions1.png)

W polu Nazwa klucza tajnego Ustaw `AZURE_CREDENTIALS` wartość i jej wartości na ciąg JSON znaleziony w nagłówku *Skonfiguruj repozytorium GitHub i Uwierzytelnij*.

 ![Ustawianie danych tajnych](./media/github-actions/actions2.png)

Możesz również uzyskać poświadczenia logowania platformy Azure z Key Vault w ramach akcji usługi GitHub, jak wyjaśniono w temacie [uwierzytelnianie Azure wiosna z Key Vault w akcjach GitHub](./spring-cloud-github-actions-key-vault.md).

## <a name="provision-service-instance"></a>Inicjowanie obsługi administracyjnej wystąpienia usługi
Aby zainicjować obsługę administracyjną wystąpienia usługi w chmurze ze sprężyną Azure, uruchom następujące polecenia przy użyciu interfejsu wiersza polecenia platformy Azure.
```
az extension add --name spring-cloud
az group create --location eastus --name <resource group name>
az spring-cloud create -n <service instance name> -g <resource group name>
az spring-cloud config-server git set -n <service instance name> --uri https://github.com/xxx/piggymetrics --label config
```
## <a name="build-the-workflow"></a>Kompilowanie przepływu pracy
Przepływ pracy jest definiowany przy użyciu następujących opcji.

### <a name="prepare-for-deployment-with-azure-cli"></a>Przygotowanie do wdrożenia za pomocą interfejsu wiersza polecenia platformy Azure
Polecenie `az spring-cloud app create` nie jest obecnie idempotentne.  Zalecamy korzystanie z tego przepływu pracy w istniejących aplikacjach i wystąpieniach w chmurze usługi Azure wiosennej.

Użyj następujących poleceń interfejsu wiersza polecenia platformy Azure na potrzeby przygotowania:
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
### <a name="deploy-with-azure-cli-action"></a>Wdrażanie za pomocą akcji interfejsu wiersza polecenia platformy Azure
AZ `run` polecenie będzie używać najnowszej wersji interfejsu wiersza polecenia platformy Azure. Jeśli istnieją istotne zmiany, możesz również użyć określonej wersji interfejsu wiersza polecenia platformy Azure z platformą Azure/CLI `action` . 

> [!Note] 
> To polecenie zostanie uruchomione w nowym kontenerze, więc `env` nie będzie działać, a dostęp do plików między akcjami może mieć dodatkowe ograniczenia.

Utwórz plik GitHub/Workflow/Main. yml w repozytorium:
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

## <a name="deploy-with-maven-plugin"></a>Wdrażanie przy użyciu wtyczki Maven
Innym rozwiązaniem jest użycie [wtyczki Maven](./spring-cloud-quickstart.md) do wdrożenia ustawień aplikacji jar i aktualizującej. Polecenie `mvn azure-spring-cloud:deploy` jest idempotentne i w razie konieczności automatycznie utworzy aplikacje. Nie musisz tworzyć odpowiednich aplikacji z wyprzedzeniem.

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

**Akcje** usługi GitHub powinny być włączane automatycznie po wypchnięciu `.github/workflow/main.yml` do serwisu GitHub. Akcja zostanie wyzwolona po wypchnięciu nowego zatwierdzenia. Jeśli utworzysz ten plik w przeglądarce, Twoja akcja powinna być już uruchomiona.

Aby sprawdzić, czy akcja została włączona, kliknij kartę **Akcje** na stronie repozytorium GitHub:

![Sprawdź, czy włączono akcję](./media/github-actions/actions3.png)

Jeśli akcja zostanie uruchomiona z błędem, na przykład jeśli nie ustawiono poświadczeń platformy Azure, można ponownie uruchomić testy po rozwiązaniu błędu. Na stronie repozytorium GitHub kliknij pozycję **Akcje**, wybierz określone zadanie przepływu pracy, a następnie kliknij przycisk **Uruchom ponownie testy** , aby ponownie uruchomić testy:

![Uruchom ponownie sprawdzenia](./media/github-actions/actions4.png)

## <a name="next-steps"></a>Następne kroki

* [Key Vault dla akcji GitHub w chmurze](./spring-cloud-github-actions-key-vault.md)
* [Azure Active Directory jednostek usługi](/cli/azure/ad/sp#az-ad-sp-create-for-rbac)
* [Akcje GitHub Actions dla platformy Azure](https://github.com/Azure/actions/)
