---
title: Szybkie uruchamianie zadania przy użyciu szablonu
description: Kolejkowanie uruchomienia zadania usługi ACR w celu skompilowania obrazu przy użyciu Azure Resource Manager szablonu
ms.topic: article
ms.date: 04/22/2020
ms.openlocfilehash: af7bebc311f81bb489fcc8be419f167ff6f9460a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781239"
---
# <a name="run-acr-tasks-using-resource-manager-templates"></a>Uruchamianie zadania usługi ACR przy użyciu Resource Manager szablonów

[zadania usługi ACR](container-registry-tasks-overview.md) to pakiet funkcji w ramach usługi Azure Container Registry ułatwiający zarządzanie obrazami kontenerów i modyfikowanie ich w całym cyklu życia kontenera. 

W tym artykule Azure Resource Manager przykłady szablonów służące do kolejkowania szybkiego uruchamiania zadań, podobnie jak te, które można utworzyć ręcznie za pomocą [polecenia az acr build.][az-acr-build]

Szablon Resource Manager do kolejki uruchamiania zadania jest przydatny w scenariuszach automatyzacji i rozszerza funkcjonalność programu `az acr build` . Na przykład:

* Tworzenie rejestru kontenerów za pomocą szablonu i natychmiastowe kolejkowanie uruchomienia zadania w celu skompilowania i wypchania obrazu kontenera
* Tworzenie lub włączanie dodatkowych zasobów, których można użyć w szybkim uruchomieniu zadania, takich jak tożsamość zarządzana dla zasobów platformy Azure

## <a name="limitations"></a>Ograniczenia

* Jako lokalizację źródłową uruchomienia zadania należy [](container-registry-tasks-overview.md#context-locations) określić kontekst zdalny, taki jak repozytorium GitHub. Nie można użyć lokalnego kontekstu źródłowego.
* W przypadku zadań uruchamianych przy użyciu tożsamości zarządzanej dozwolona *jest tylko* tożsamość zarządzana przypisana przez użytkownika.

## <a name="prerequisites"></a>Wymagania wstępne

* **Konto usługi GitHub** — utwórz konto w usłudze https://github.com , jeśli jeszcze go nie masz. 
* **Przykładowe repozytorium fork** — w przykładach zadań pokazanych w tym miejscu użyj interfejsu użytkownika usługi GitHub do wykonania forkowania następującego przykładowego repozytorium na koncie usługi GitHub: https://github.com/Azure-Samples/acr-build-helloworld-node . To repo zawiera przykładowe pliki Dockerfile i kod źródłowy do kompilowania małych obrazów kontenerów.

## <a name="example-create-registry-and-queue-task-run"></a>Przykład: Uruchamianie zadania rejestru i kolejki

W tym przykładzie [użyto przykładowego szablonu](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuild) do utworzenia rejestru kontenerów i do kolejki uruchomienia zadania, które skompilowało i wypchłoło obraz. 

### <a name="template-parameters"></a>Parametry szablonu

W tym przykładzie podaj wartości następujących parametrów szablonu:

|Parametr  |Wartość  |
|---------|---------|
|registryName (nazwa rejestru)     |Unikatowa nazwa utworzonego rejestru         |
|repozytorium     |Repozytorium docelowe dla zadania kompilacji        |
|taskRunName     |Nazwa uruchomienia zadania, które określa tag obrazu |
|Sourcelocation     |Zdalny kontekst zadania kompilacji, na przykład https://github.com/Azure-Samples/acr-build-helloworld-node . Plik Dockerfile w katalogu głównym repo tworzy obraz kontenera dla małej Node.js internetowej. W razie potrzeby użyj swojego widelec z repo jako kontekstu kompilacji.         |

### <a name="deploy-the-template"></a>Wdrożenie szablonu

Wd wdrażaj szablon za pomocą [polecenia az deployment group create.][az-deployment-group-create] W tym przykładzie jest kompilowany i wypychany obraz *helloworld-node:testrun* do rejestru *o nazwie mycontainerregistry.*

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

Poprzednie polecenie przekazuje parametry w wierszu polecenia. W razie potrzeby przekaż je w pliku [parametrów](../azure-resource-manager/templates/parameter-files.md).

### <a name="verify-deployment"></a>Weryfikowanie wdrożenia

Po pomyślnym zakończeniu wdrażania sprawdź, czy obraz został sbudowaną, uruchamiając [az acr repository show-tags:][az-acr-repository-show-tags]

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

### <a name="view-run-log"></a>Wyświetlanie dziennika uruchamiania

Aby wyświetlić szczegółowe informacje o uruchomieniu zadania, wyświetl dziennik uruchamiania.

Najpierw pobierz identyfikator uruchomienia za pomocą [az acr task list-runs][az-acr-task-list-runs]
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

Uruchom [az acr task logs,][az-acr-task-logs] aby wyświetlić dzienniki uruchamiania zadań dla identyfikatora uruchomienia, w tym przypadku *ca1:*

```azurecli
az acr task logs \
  --registry mycontainerregistry \
  --run-id ca1
```

Dane wyjściowe pokazują dziennik uruchamiania zadania.

Dziennik uruchamiania zadania można również wyświetlić w Azure Portal. 

1. Przejdź do rejestru kontenerów
2. W **obszarze Usługi** wybierz pozycję **Zadania**  >  **przebiegów.**
3. Wybierz identyfikator uruchomienia, w tym przypadku *ca1.* 

W portalu zostanie uruchomiony dziennik uruchamiania zadań.

## <a name="example-task-run-with-managed-identity"></a>Przykład: Zadanie uruchamiane przy użyciu tożsamości zarządzanej

Użyj [przykładowego szablonu,](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment/quickdockerbuildwithidentity) aby do kolejki kolejki uruchomić zadanie, które włącza tożsamość zarządzaną przypisaną przez użytkownika. Podczas uruchamiania zadania tożsamość jest uwierzytelniana w celu ściągnięcie obrazu z innego rejestru kontenerów platformy Azure. 

Ten scenariusz jest podobny do [uwierzytelniania między rejestrami w zadaniu usługi ACR przy użyciu tożsamości zarządzanej przez platformę Azure.](container-registry-tasks-cross-registry-authentication.md) Na przykład organizacja może zachować scentralizowany rejestr z obrazami bazowymi dostępnymi dla wielu zespołów programistów.

### <a name="prepare-base-registry"></a>Przygotowywanie rejestru podstawowego

W celach demonstracyjnych utwórz oddzielny rejestr kontenerów jako rejestr podstawowy i wypchnij obraz Node.js ściągnęty z Docker Hub.

1. Utwórz drugi rejestr kontenerów, na przykład *mybaseregistry,* do przechowywania obrazów podstawowych.
1. Ściągnij obraz z Docker Hub, otaguj go dla rejestru podstawowego `node:9-alpine` i wypchnij do rejestru podstawowego:

  ```azurecli
  docker pull node:9-alpine
  docker tag node:9-alpine mybaseregistry.azurecr.io/baseimages/node:9-alpine
  az acr login -n mybaseregistry
  docker push mybaseregistry.azurecr.io/baseimages/node:9-alpine
  ```

### <a name="create-new-dockerfile"></a>Tworzenie nowego pliku Dockerfile

Utwórz plik Dockerfile, który ściąga obraz podstawowy z rejestru podstawowego. Wykonaj następujące kroki w lokalnym forkcie repozytorium GitHub, na przykład `https://github.com/myGitHubID/acr-build-helloworld-node.git` .

1. W interfejsie użytkownika usługi GitHub wybierz **pozycję Utwórz nowy plik**.
1. Nadaj *plikowi nazwę Dockerfile-test* i wklej następującą zawartość. Zastąp wartość *mybaseregistry* nazwą rejestru.
    ```
    FROM mybaseregistry.azurecr.io/baseimages/node:9-alpine
    COPY . /src
    RUN cd /src && npm install
    EXPOSE 80
    CMD ["node", "/src/server.js"]
    ```
 1. Wybierz **pozycję Zat zatwierdzanie nowego pliku.**

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Nadaj tożsamości uprawnienia do ściągania do rejestru podstawowego

Nadaj tożsamości zarządzanej uprawnienia do ściągania z rejestru podstawowego *mybaseregistry*.

Użyj polecenia [az acr show,][az-acr-show] aby uzyskać identyfikator zasobu rejestru podstawowego i zapisać go w zmiennej:

```azurecli
baseregID=$(az acr show \
  --name mybaseregistry \
  --query id --output tsv)
```

Użyj polecenia [az role assignment create,][az-role-assignment-create] aby przypisać tożsamość roli Acrpull do rejestru podstawowego. Ta rola ma uprawnienia tylko do ściągania obrazów z rejestru.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

### <a name="template-parameters"></a>Parametry szablonu

W tym przykładzie podaj wartości następujących parametrów szablonu:

|Parametr  |Wartość  |
|---------|---------|
|registryName (nazwa rejestru)     |Nazwa rejestru, w którym jest budowy obraz  |
|repozytorium     |Repozytorium docelowe dla zadania kompilacji        |
|taskRunName     |Nazwa uruchomienia zadania, które określa tag obrazu |
|userAssignedIdentity |Identyfikator zasobu tożsamości przypisanej przez użytkownika włączony w zadaniu|
|customRegistryIdentity | Identyfikator klienta tożsamości przypisanej przez użytkownika włączony w zadaniu używany do uwierzytelniania za pomocą rejestru niestandardowego |
|customRegistry |Nazwa serwera logowania niestandardowego rejestru dostępnego w  zadaniu, na przykład mybaseregistry.azurecr.io|
|Sourcelocation     |Zdalny kontekst zadania kompilacji, na przykład *https://github.com/ \<your-GitHub-ID\> /acr-build-helloworld-node.* |
|dockerFilePath | Ścieżka do pliku Dockerfile w kontekście zdalnym używana do kompilowania obrazu. |

### <a name="deploy-the-template"></a>Wdrożenie szablonu

Wd wdrażaj szablon za pomocą [polecenia az deployment group create.][az-deployment-group-create] Ten przykład tworzy i wypycha obraz *helloworld-node:testrun* do rejestru *o nazwie mycontainerregistry.* Obraz podstawowy jest ściągany z *mybaseregistry.azurecr.io*.

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

Poprzednie polecenie przekazuje parametry w wierszu polecenia. W razie potrzeby przekaż je w pliku [parametrów](../azure-resource-manager/templates/parameter-files.md).

### <a name="verify-deployment"></a>Weryfikowanie wdrożenia

Po pomyślnym zakończeniu wdrażania sprawdź, czy obraz został sbudowaną, uruchamiając [repozytorium az acr show-tags:][az-acr-repository-show-tags]

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

### <a name="view-run-log"></a>Wyświetlanie dziennika uruchamiania

Aby wyświetlić dziennik uruchamiania, zobacz kroki opisane w [poprzedniej sekcji](#view-run-log).

## <a name="next-steps"></a>Następne kroki

 * Zobacz więcej przykładów szablonów w repozytorium GitHub usługi [ACR](https://github.com/Azure/acr/tree/master/docs/tasks/run-as-deployment).
 * Aby uzyskać szczegółowe informacje o właściwościach szablonu, zobacz informacje o szablonie [przebiegów zadań i](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/taskruns) [zadań.](/azure/templates/microsoft.containerregistry/2019-06-01-preview/registries/tasks)


<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-logs]: /cli/azure/acr/task#az_acr_task_logs
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az_acr_repository_show_tags
[az-acr-task-list-runs]: /cli/azure/acr/task#az_acr_task_list_runs
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-show]: /cli/azure/identity#az_identity_show
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
