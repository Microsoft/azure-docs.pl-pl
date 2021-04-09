---
title: Szybkie uruchamianie zadań z szablonem
description: Zakolejkowanie uruchomienia zadania ACR w celu skompilowania obrazu przy użyciu szablonu Azure Resource Manager
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: 6e8023c088ac328c2b6e95fccd0230c4d40325c1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98916069"
---
# <a name="run-acr-tasks-using-resource-manager-templates"></a>Uruchamianie zadań ACR przy użyciu szablonów Menedżer zasobów

[ACR Tasks](container-registry-tasks-overview.md) to zestaw funkcji w ramach Azure Container Registry, które ułatwiają zarządzanie obrazami kontenerów w ramach cyklu życia kontenera i ich modyfikowanie. 

W tym artykule przedstawiono przykłady szablonów Azure Resource Manager do kolejki przebiegu szybkiego zadania, podobnie jak w przypadku, można utworzyć ręcznie przy użyciu polecenia [AZ ACR Build][az-acr-build] .

Szablon Menedżer zasobów do zakolejkowania uruchomienia zadania jest użyteczny w scenariuszach automatyzacji i rozszerza funkcje programu `az acr build` . Na przykład:

* Użyj szablonu, aby utworzyć rejestr kontenerów i natychmiast kolejkować uruchomione zadanie w celu kompilowania i wypychania obrazu kontenera
* Tworzenie lub Włączanie dodatkowych zasobów, których można użyć w szybkim przebiegu zadania, takich jak zarządzana tożsamość zasobów platformy Azure

## <a name="limitations"></a>Ograniczenia

* Należy określić kontekst zdalny, taki jak repozytorium GitHub, jako [lokalizację źródłową](container-registry-tasks-overview.md#context-locations) do uruchomienia zadania. Nie można użyć lokalnego kontekstu źródłowego.
* W przypadku uruchamiania zadań przy użyciu tożsamości zarządzanej jest dozwolona tylko tożsamość zarządzana *przypisana przez użytkownika* .

## <a name="prerequisites"></a>Wymagania wstępne

* **Konto usługi GitHub** — Utwórz konto https://github.com , jeśli jeszcze go nie masz. 
* **Przykładowe repozytorium rozwidlenia** — aby zobaczyć przykłady zadań, użyj interfejsu użytkownika usługi GitHub, aby utworzyć rozwidlenie następującego przykładowego repozytorium do konta usługi GitHub: https://github.com/Azure-Samples/acr-build-helloworld-node . To repozytorium zawiera przykładowy wieloetapowe dockerfile i kod źródłowy do tworzenia obrazów małych kontenerów.

## <a name="example-create-registry-and-queue-task-run"></a>Przykład: tworzenie rejestru i uruchomienie zadania w kolejce

W tym przykładzie używamy [przykładowego szablonu](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuild) do utworzenia rejestru kontenera i uruchomienia zadania w kolejce, które kompiluje i wypycha obraz. 

### <a name="template-parameters"></a>Parametry szablonu

Na potrzeby tego przykładu podaj wartości dla następujących parametrów szablonu:

|Parametr  |Wartość  |
|---------|---------|
|registryname     |Unikatowa nazwa utworzonego rejestru         |
|repozytorium     |Repozytorium docelowe dla zadania kompilacji        |
|taskRunName     |Nazwa uruchomienia zadania, która określa tag obrazu |
|sourceLocation     |Kontekst zdalny dla zadania kompilacji, na przykład https://github.com/Azure-Samples/acr-build-helloworld-node . Pliku dockerfile w katalogu głównym repozytorium tworzy obraz kontenera dla małej Node.js aplikacji sieci Web. W razie potrzeby użyj rozwidlenia repozytorium jako kontekstu kompilacji.         |

### <a name="deploy-the-template"></a>Wdrożenie szablonu

Wdróż szablon za pomocą polecenia [AZ Deployment Group Create][az-deployment-group-create] . Ten przykład kompiluje i wypycha plik *HelloWorld-Node: przebiegu testowego* do rejestru o nazwie *mycontainerregistry*.

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/acr/master/docs/tasks/run-as-deployment/quickdockerbuild/azuredeploy.json \
  --parameters \
    registryName=mycontainerregistry \
    repository=helloworld-node \
    taskRunName=testrun \
    sourceLocation=https://github.com/Azure-Samples/acr-build-helloworld-node.git#main
 ```

Poprzednie polecenie przekazuje parametry w wierszu polecenia. W razie potrzeby Przekaż je do [pliku parametrów](../azure-resource-manager/templates/parameter-files.md).

### <a name="verify-deployment"></a>Weryfikowanie wdrożenia

Po pomyślnym zakończeniu wdrożenia Sprawdź, czy obraz został skompilowany przez uruchomienie [AZ ACR Repository show-Tags][az-acr-repository-show-tags]:

```azurecli
az acr repository show-tags \
  --name mycontainerregistry \
  --repository helloworld-node --output table
```

Dane wyjściowe:

```console
Result
--------
testrun
```

### <a name="view-run-log"></a>Wyświetl dziennik uruchamiania

Aby wyświetlić szczegółowe informacje o przebiegu zadania, Wyświetl dziennik przebiegu.

Najpierw Pobierz identyfikator uruchomienia za pomocą [AZ ACR Task List-][az-acr-task-list-runs] Runs
```azurecli
az acr task list-runs \
  --registry mycontainerregistry --output table
```

Dane wyjściowe są podobne do następujących:

```console
RUN ID    TASK    PLATFORM    STATUS     TRIGGER    STARTED               DURATION
--------  ------  ----------  ---------  ---------  --------------------  ----------
ca1               linux       Succeeded  Manual     2020-03-23T17:54:28Z  00:00:48
```

Uruchom [AZ ACR Task Logs][az-acr-task-logs] , aby wyświetlić dzienniki uruchamiania zadań dla identyfikatora uruchomienia, w tym przypadku *CA1*:

```azurecli
az acr task logs \
  --registry mycontainerregistry \
  --run-id ca1
```

Dane wyjściowe przedstawiają dziennik uruchomienia zadania.

Możesz również wyświetlić dziennik przebiegu zadania w Azure Portal. 

1. Przejdź do rejestru kontenerów
2. W obszarze **usługi** wybierz pozycję **zadania**  >  **uruchomienia**.
3. Wybierz identyfikator przebiegu, w tym przypadku *CA1*. 

W portalu jest wyświetlany dziennik uruchamiania zadań.

## <a name="example-task-run-with-managed-identity"></a>Przykład: zadanie uruchamiane z tożsamością zarządzaną

Użyj [przykładowego szablonu](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity) do kolejki uruchomienia zadania, które umożliwia tożsamość zarządzaną przez użytkownika. Podczas wykonywania zadania tożsamość uwierzytelnia się w celu ściągnięcia obrazu z innego rejestru kontenerów platformy Azure. 

Ten scenariusz jest podobny do [uwierzytelniania między rejestrami w ACR zadania przy użyciu tożsamości zarządzanej przez platformę Azure](container-registry-tasks-cross-registry-authentication.md). Na przykład organizacja może zachować scentralizowany rejestr z podstawowymi obrazami dostępnymi dla wielu zespołów programistycznych.

### <a name="prepare-base-registry"></a>Przygotuj rejestr podstawowy

W celach demonstracyjnych Utwórz oddzielny rejestr kontenerów jako rejestr podstawowy i wypchnij Node.js obraz podstawowy pobrany z usługi Docker Hub.

1. Utwórz drugi rejestr kontenerów, na przykład *mybaseregistry*, do przechowywania obrazów podstawowych.
1. Pobierz `node:9-alpine` obraz z usługi Docker Hub, oznacz go jako rejestr podstawowy i wypchnij do rejestru podstawowego:

  ```azurecli
  docker pull node:9-alpine
  docker tag node:9-alpine mybaseregistry.azurecr.io/baseimages/node:9-alpine
  az acr login -n mybaseregistry
  docker push mybaseregistry.azurecr.io/baseimages/node:9-alpine
  ```

### <a name="create-new-dockerfile"></a>Utwórz nowy pliku dockerfile

Utwórz element pliku dockerfile, który pobiera podstawowy obraz z rejestru podstawowego. Wykonaj następujące kroki w lokalnym rozwidleniu repozytorium GitHub, na przykład `https://github.com/myGitHubID/acr-build-helloworld-node.git` .

1. W interfejsie użytkownika usługi GitHub wybierz pozycję **Utwórz nowy plik**.
1. Nazwij plik *pliku dockerfile-test* i wklej poniższą zawartość. Zastąp nazwę rejestru *mybaseregistry*.
    ```
    FROM mybaseregistry.azurecr.io/baseimages/node:9-alpine
    COPY . /src
    RUN cd /src && npm install
    EXPOSE 80
    CMD ["node", "/src/server.js"]
    ```
 1. Wybierz pozycję **Zatwierdź nowy plik**.

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Przyznaj uprawnienia do ściągania tożsamości do rejestru podstawowego

Nadaj zarządzanej tożsamości uprawnienia do ściągania z rejestru podstawowego, *mybaseregistry*.

Użyj polecenia [AZ ACR show][az-acr-show] , aby uzyskać identyfikator zasobu podstawowego rejestru i zapisać go w zmiennej:

```azurecli
baseregID=$(az acr show \
  --name mybaseregistry \
  --query id --output tsv)
```

Użyj polecenia [AZ role Assign Create][az-role-assignment-create] , aby przypisać tożsamości rolę Acrpull do rejestru podstawowego. Ta rola ma uprawnienia tylko do ściągania obrazów z rejestru.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

### <a name="template-parameters"></a>Parametry szablonu

Na potrzeby tego przykładu podaj wartości dla następujących parametrów szablonu:

|Parametr  |Wartość  |
|---------|---------|
|registryname     |Nazwa rejestru, w którym utworzono obraz  |
|repozytorium     |Repozytorium docelowe dla zadania kompilacji        |
|taskRunName     |Nazwa uruchomienia zadania, która określa tag obrazu |
|userAssignedIdentity |Identyfikator zasobu tożsamości przypisanej przez użytkownika w zadaniu|
|customRegistryIdentity | Identyfikator klienta dla tożsamości przypisanej przez użytkownika w zadaniu, używany do uwierzytelniania za pomocą rejestru niestandardowego |
|customRegistry |Nazwa serwera logowania rejestru niestandardowego, do którego można uzyskać dostęp w zadaniu, na przykład *mybaseregistry.azurecr.IO*|
|sourceLocation     |Kontekst zdalny dla zadania kompilacji, na przykład *https://github.com/ \<your-GitHub-ID\> /ACR-Build-HelloWorld-Node.* |
|dockerFilePath | Ścieżka do pliku dockerfile w kontekście zdalnym, użyta do utworzenia obrazu. |

### <a name="deploy-the-template"></a>Wdrożenie szablonu

Wdróż szablon za pomocą polecenia [AZ Deployment Group Create][az-deployment-group-create] . Ten przykład kompiluje i wypycha plik *HelloWorld-Node: przebiegu testowego* do rejestru o nazwie *mycontainerregistry*. Obraz podstawowy jest pobierany z *mybaseregistry.azurecr.IO*.

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/acr/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity/azuredeploy.json \
  --parameters \
    registryName=mycontainerregistry \
    repository=helloworld-node \
    taskRunName=basetask \
    userAssignedIdentity=$resourceID \
    customRegistryIdentity=$clientID \
    sourceLocation=https://github.com/<your-GitHub-ID>/acr-build-helloworld-node.git#main \
    dockerFilePath=Dockerfile-test \
    customRegistry=mybaseregistry.azurecr.io
```

Poprzednie polecenie przekazuje parametry w wierszu polecenia. W razie potrzeby Przekaż je do [pliku parametrów](../azure-resource-manager/templates/parameter-files.md).

### <a name="verify-deployment"></a>Weryfikowanie wdrożenia

Po pomyślnym zakończeniu wdrożenia Sprawdź, czy obraz został skompilowany przez uruchomienie [AZ ACR Repository show-Tags][az-acr-repository-show-tags]:

```azurecli
az acr repository show-tags \
  --name mycontainerregistry \
  --repository helloworld-node --output table
```

Dane wyjściowe:

```console
Result
--------
basetask
```

### <a name="view-run-log"></a>Wyświetl dziennik uruchamiania

Aby wyświetlić dziennik uruchamiania, zobacz kroki opisane w [poprzedniej sekcji](#view-run-log).

## <a name="next-steps"></a>Następne kroki

 * Zobacz więcej przykładów szablonów w [repozytorium GitHub ACR](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment).
 * Aby uzyskać szczegółowe informacje na temat właściwości szablonu, zobacz Dokumentacja szablonu dotyczące [uruchamiania zadań](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/taskruns) i [zadań](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/tasks).


<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-logs]: /cli/azure/acr/task#az-acr-task-logs
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
