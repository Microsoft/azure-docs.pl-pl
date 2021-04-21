---
title: Uwierzytelnianie między rejestrami z zadania usługi ACR
description: Konfigurowanie zadania Azure Container Registry (ACR Task) w celu uzyskania dostępu do innego prywatnego rejestru kontenerów platformy Azure przy użyciu tożsamości zarządzanej dla zasobów platformy Azure
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: a9b70a44de0cfccb9a61bc24575281e440db6e32
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781128"
---
# <a name="cross-registry-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Uwierzytelnianie między rejestrami w zadaniu usługi ACR przy użyciu tożsamości zarządzanej przez platformę Azure 

W [zadaniu usługi ACR](container-registry-tasks-overview.md)możesz włączyć [tożsamość zarządzaną dla zasobów platformy Azure.](container-registry-tasks-authentication-managed-identity.md) Zadanie może używać tożsamości do uzyskiwania dostępu do innych zasobów platformy Azure bez konieczności poświadczeń ani zarządzania nimi. 

Z tego artykułu dowiesz się, jak włączyć tożsamość zarządzaną w zadaniu, aby ściągnąć obraz z rejestru innego niż używany do uruchomienia zadania.

Aby utworzyć zasoby platformy Azure, ten artykuł wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0.68 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

## <a name="scenario-overview"></a>Omówienie scenariusza

Przykładowe zadanie ściąga obraz podstawowy z innego rejestru kontenerów platformy Azure w celu skompilowania i wypchnięcia obrazu aplikacji. Aby ściągnąć obraz podstawowy, należy skonfigurować zadanie przy użyciu tożsamości zarządzanej i przypisać do niego odpowiednie uprawnienia. 

W tym przykładzie przedstawiono kroki przy użyciu tożsamości zarządzanej przypisanej przez użytkownika lub przypisanej przez system. Wybór tożsamości zależy od potrzeb organizacji.

W rzeczywistym scenariuszu organizacja może utrzymywać zestaw podstawowych obrazów używanych przez wszystkie zespoły programowe do tworzenia aplikacji. Te obrazy podstawowe są przechowywane w rejestrze firmowym, a każdy zespół programistów ma tylko prawa do ściągania. 

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule potrzebne są dwa rejestry kontenerów platformy Azure:

* Pierwszy rejestr umożliwia tworzenie i wykonywanie zadań ACR. W tym artykule ten rejestr nosi nazwę *myregistry*. 
* Drugi rejestr hostuje obraz podstawowy używany do tworzenia obrazu przez zadanie. W tym artykule drugi rejestr nosi nazwę *mybaseregistry*. 

W kolejnych krokach zastąp własnymi nazwami rejestru.

Jeśli nie masz jeszcze potrzebnych rejestrów kontenerów platformy Azure, zobacz Szybki start: tworzenie prywatnego rejestru kontenerów przy [użyciu interfejsu wiersza polecenia platformy Azure.](container-registry-get-started-azure-cli.md) Nie musisz jeszcze wypychać obrazów do rejestru.

## <a name="prepare-base-registry"></a>Przygotowywanie rejestru podstawowego

W celach demonstracyjnych jako operację raz uruchom [az acr import][az-acr-import], aby zaimportować publiczny obraz Node.js z Docker Hub do rejestru podstawowego. W praktyce inny zespół lub proces w organizacji może utrzymywać obrazy w rejestrze bazowym.

```azurecli
az acr import --name mybaseregistry \
  --source docker.io/library/node:15-alpine \
  --image baseimages/node:15-alpine 
```

## <a name="define-task-steps-in-yaml-file"></a>Definiowanie kroków zadań w pliku YAML

Kroki tego przykładowego [wieloetapowego zadania są](container-registry-tasks-multi-step.md) zdefiniowane w pliku [YAML](container-registry-tasks-reference-yaml.md). Utwórz plik o nazwie `helloworldtask.yaml` w lokalnym katalogu pracy i wklej następującą zawartość. Zaktualizuj wartość w `REGISTRY_NAME` kroku kompilacji przy użyciu nazwy serwera rejestru podstawowego.

```yml
version: v1.1.0
steps:
# Replace mybaseregistry with the name of your registry containing the base image
  - build: -t $Registry/hello-world:$ID  https://github.com/Azure-Samples/acr-build-helloworld-node.git#main -f Dockerfile-app --build-arg REGISTRY_NAME=mybaseregistry.azurecr.io
  - push: ["$Registry/hello-world:$ID"]
```

Krok kompilacji używa `Dockerfile-app` pliku w repocie [Azure-Samples/acr-build-helloworld-node](https://github.com/Azure-Samples/acr-build-helloworld-node.git) do skompilowania obrazu. Plik `--build-arg` odwołuje się do rejestru podstawowego w celu ściągnięcie obrazu podstawowego. Po pomyślnym s zbudowaniu obraz jest wypychany do rejestru używanego do uruchamiania zadania.

## <a name="option-1-create-task-with-user-assigned-identity"></a>Opcja 1. Tworzenie zadania z tożsamością przypisaną przez użytkownika

Kroki opisane w tej sekcji umożliwiają utworzenie zadania i włączenie tożsamości przypisanej przez użytkownika. Jeśli zamiast tego chcesz włączyć tożsamość przypisaną przez system, zobacz Option [2: Create task with system-assigned identity](#option-2-create-task-with-system-assigned-identity)(Opcja 2: tworzenie zadania z tożsamością przypisaną przez system). 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Tworzenie zadania

Utwórz zadanie *helloworldtask,* wykonując następujące [polecenie az acr task create.][az-acr-task-create] Zadanie jest uruchamiane bez kontekstu kodu źródłowego, a polecenie odwołuje się do pliku `helloworldtask.yaml` w katalogu pracy. Parametr `--assign-identity` przekazuje identyfikator zasobu tożsamości przypisanej przez użytkownika. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Nadaj tożsamości uprawnienia do ściągania do rejestru podstawowego

W tej sekcji nadaj tożsamości zarządzanej uprawnienia do ściągania z rejestru podstawowego *mybaseregistry*.

Użyj polecenia [az acr show,][az-acr-show] aby uzyskać identyfikator zasobu rejestru podstawowego i zapisać go w zmiennej:

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

Użyj polecenia [az role assignment create,][az-role-assignment-create] aby przypisać tożsamość `acrpull` roli do rejestru podstawowego. Ta rola ma uprawnienia tylko do ściągania obrazów z rejestru.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

Przejdź do [zadania Add target registry credentials to task](#add-target-registry-credentials-to-task)(Dodawanie poświadczeń rejestru docelowego do zadania ).

## <a name="option-2-create-task-with-system-assigned-identity"></a>Opcja 2. Tworzenie zadania z tożsamością przypisaną przez system

Kroki opisane w tej sekcji umożliwiają utworzenie zadania i włączenie tożsamości przypisanej przez system. Jeśli zamiast tego chcesz włączyć tożsamość przypisaną przez użytkownika, zobacz Opcja 1. Tworzenie zadania z [tożsamością przypisaną przez użytkownika.](#option-1-create-task-with-user-assigned-identity) 

### <a name="create-task"></a>Tworzenie zadania

Utwórz zadanie *helloworldtask,* wykonując następujące [polecenie az acr task create.][az-acr-task-create] Zadanie jest uruchamiane bez kontekstu kodu źródłowego, a polecenie odwołuje się do pliku `helloworldtask.yaml` w katalogu roboczy. Parametr bez wartości włącza tożsamość przypisaną przez `--assign-identity` system w zadaniu. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity 
```
[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Nadaj tożsamości uprawnienia do ściągania do rejestru podstawowego

W tej sekcji nadaj tożsamości zarządzanej uprawnienia do ściągania z rejestru podstawowego *mybaseregistry*.

Użyj polecenia [az acr show,][az-acr-show] aby uzyskać identyfikator zasobu rejestru podstawowego i zapisać go w zmiennej:

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

Użyj polecenia [az role assignment create,][az-role-assignment-create] aby przypisać tożsamość `acrpull` roli do rejestru podstawowego. Ta rola ma uprawnienia tylko do ściągania obrazów z rejestru.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

## <a name="add-target-registry-credentials-to-task"></a>Dodawanie poświadczeń rejestru docelowego do zadania

Teraz użyj polecenia [az acr task credential add,][az-acr-task-credential-add] aby umożliwić zadaniu uwierzytelnianie w rejestrze bazowym przy użyciu poświadczeń tożsamości. Uruchom polecenie odpowiadające typowi tożsamości zarządzanej włączonej w zadaniu. Jeśli włączono tożsamość przypisaną przez użytkownika, przekaż identyfikator `--use-identity` klienta tożsamości. Jeśli włączono tożsamość przypisaną przez system, `--use-identity [system]` przekaż .

```azurecli
# Add credentials for user-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity $clientID

# Add credentials for system-assigned identity to the task
az acr task credential add \
  --name helloworldtask \
  --registry myregistry \
  --login-server mybaseregistry.azurecr.io \
  --use-identity [system]
```

## <a name="manually-run-the-task"></a>Ręczne uruchamianie zadania

Aby sprawdzić, czy zadanie, w którym włączono tożsamość zarządzaną, działa pomyślnie, ręcznie wyzwolij zadanie za pomocą [polecenia az acr task run.][az-acr-task-run] 

```azurecli
az acr task run \
  --name helloworldtask \
  --registry myregistry
```

Jeśli zadanie zostanie pomyślnie uruchomiony, dane wyjściowe będą podobne do:

```
Queued a run with ID: cf10
Waiting for an agent...
2019/06/14 22:47:32 Using acb_vol_dbfbe232-fd76-4ca3-bd4a-687e84cb4ce2 as the home volume
2019/06/14 22:47:39 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/14 22:47:40 Successfully set up Docker network: acb_default_network
2019/06/14 22:47:40 Setting up Docker configuration...
2019/06/14 22:47:41 Successfully set up Docker configuration
2019/06/14 22:47:41 Logging in to registry: myregistry.azurecr.io
2019/06/14 22:47:42 Successfully logged into myregistry.azurecr.io
2019/06/14 22:47:42 Logging in to registry: mybaseregistry.azurecr.io
2019/06/14 22:47:43 Successfully logged into mybaseregistry.azurecr.io
2019/06/14 22:47:43 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:43 Scanning for dependencies...
2019/06/14 22:47:45 Successfully scanned dependencies
2019/06/14 22:47:45 Launching container with name: acb_step_0
Sending build context to Docker daemon   25.6kB
Step 1/6 : ARG REGISTRY_NAME
Step 2/6 : FROM ${REGISTRY_NAME}/baseimages/node:15-alpine
15-alpine: Pulling from baseimages/node
[...]
Successfully built 41b49a112663
Successfully tagged myregistry.azurecr.io/hello-world:cf10
2019/06/14 22:47:56 Successfully executed container: acb_step_0
2019/06/14 22:47:56 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/14 22:47:56 Pushing image: myregistry.azurecr.io/hello-world:cf10, attempt 1
The push refers to repository [myregistry.azurecr.io/hello-world]
[...]
2019/06/14 22:48:00 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.517011)
2019/06/14 22:48:00 The following dependencies were found:
2019/06/14 22:48:00
- image:
    registry: myregistry.azurecr.io
    repository: hello-world
    tag: cf10
    digest: sha256:611cf6e3ae3cb99b23fadcd89fa144e18aa1b1c9171ad4a0da4b62b31b4e38d1
  runtime-dependency:
    registry: mybaseregistry.azurecr.io
    repository: baseimages/node
    tag: 15-alpine
    digest: sha256:e8e92cffd464fce3be9a3eefd1b65dc9cbe2484da31c11e813a4effc6105c00f
  git:
    git-head-revision: 0f988779c97fe0bfc7f2f74b88531617f4421643

Run ID: cf10 was successful after 32s
```

Uruchom polecenie [az acr repository show-tags,][az-acr-repository-show-tags] aby sprawdzić, czy obraz został sbudowaną i pomyślnie wypchniętą do *myregistry:*

```azurecli
az acr repository show-tags --name myregistry --repository hello-world --output tsv
```

Przykładowe dane wyjściowe:

```console
cf10
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [włączaniu tożsamości zarządzanej w zadaniu usługi ACR.](container-registry-tasks-authentication-managed-identity.md)
* Zobacz informacje [zadania usługi ACR YAML](container-registry-tasks-reference-yaml.md)

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az_login
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az_acr_repository_show_tags
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-show]: /cli/azure/acr/task#az_acr_task_show
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-list-runs]: /cli/azure/acr/task#az_acr_task_list_runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az_acr_task_credential_add
[az-group-create]: /cli/azure/group?#az_group_create
