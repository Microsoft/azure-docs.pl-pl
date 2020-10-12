---
title: Uwierzytelnianie między rejestrami z zadania ACR
description: Skonfiguruj zadanie Azure Container Registry (zadanie ACR), aby uzyskać dostęp do innego prywatnego rejestru kontenerów platformy Azure przy użyciu tożsamości zarządzanej dla zasobów platformy Azure
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: 8b961a2ff6a795f03798cc6f6a7d303391036ef8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86057363"
---
# <a name="cross-registry-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Uwierzytelnianie między rejestrami w ACR zadania przy użyciu tożsamości zarządzanej przez platformę Azure 

W [zadaniu ACR](container-registry-tasks-overview.md)można [włączyć zarządzaną tożsamość dla zasobów platformy Azure](container-registry-tasks-authentication-managed-identity.md). Zadanie może korzystać z tożsamości w celu uzyskania dostępu do innych zasobów platformy Azure bez konieczności podania poświadczeń ani zarządzania nimi. 

W tym artykule dowiesz się, jak włączyć zarządzaną tożsamość w ramach zadania, aby ściągnąć obraz z rejestru innego niż użyty do uruchomienia zadania.

Aby utworzyć zasoby platformy Azure, ten artykuł wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0.68 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

## <a name="scenario-overview"></a>Omówienie scenariusza

Przykładowe zadanie pobiera obraz podstawowy z innego rejestru kontenerów platformy Azure w celu kompilowania i wypychania obrazu aplikacji. Aby ściągnąć podstawowy obraz, należy skonfigurować zadanie przy użyciu tożsamości zarządzanej i przypisać do niego odpowiednie uprawnienia. 

W tym przykładzie przedstawiono kroki korzystające z tożsamości zarządzanej przypisanej przez użytkownika lub przypisanej do systemu. Wybór tożsamości zależy od potrzeb organizacji.

W rzeczywistym scenariuszu Organizacja może zachować zestaw obrazów podstawowych używanych przez wszystkie zespoły programistyczne do kompilowania aplikacji. Te obrazy podstawowe są przechowywane w rejestrze firmowym, a każdy zespół programistyczny ma tylko prawa do ściągania. 

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule potrzebne są dwa rejestry kontenerów platformy Azure:

* Pierwszy rejestr służy do tworzenia i wykonywania zadań ACR. W tym artykule rejestr nazywa się *rejestrem*. 
* Drugi rejestr hostuje obraz podstawowy używany do zadania tworzenia obrazu. W tym artykule drugi rejestr nosi nazwę *mybaseregistry*. 

Zastąp ciąg własnymi nazwami rejestru w dalszych krokach.

Jeśli nie masz jeszcze wymaganych rejestrów kontenerów platformy Azure, zobacz [Szybki Start: Tworzenie prywatnego rejestru kontenerów za pomocą interfejsu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md). Nie jest jeszcze konieczne wypychanie obrazów do rejestru.

## <a name="prepare-base-registry"></a>Przygotuj rejestr podstawowy

Najpierw Utwórz katalog roboczy, a następnie utwórz plik o nazwie pliku dockerfile z następującą zawartością. Ten prosty przykład kompiluje Node.js obraz podstawowy z publicznego obrazu w usłudze Docker Hub.
    
```bash
echo FROM node:9-alpine > Dockerfile
```

W bieżącym katalogu Uruchom polecenie [AZ ACR Build][az-acr-build] , aby skompilować i wypchnąć obraz podstawowy do rejestru podstawowego. W tym przypadku inny zespół lub proces w organizacji może zachować rejestr podstawowy.
    
```azurecli
az acr build --image baseimages/node:9-alpine --registry mybaseregistry --file Dockerfile .
```

## <a name="define-task-steps-in-yaml-file"></a>Zdefiniuj kroki zadania w pliku YAML

Kroki tego przykładowego [zadania wieloetapowego](container-registry-tasks-multi-step.md) są zdefiniowane w [pliku YAML](container-registry-tasks-reference-yaml.md). Utwórz plik o nazwie `helloworldtask.yaml` w lokalnym katalogu roboczym i wklej poniższą zawartość. Zaktualizuj wartość `REGISTRY_NAME` w kroku kompilacji z nazwą serwera podstawowego rejestru.

```yml
version: v1.1.0
steps:
# Replace mybaseregistry with the name of your registry containing the base image
  - build: -t $Registry/hello-world:$ID  https://github.com/Azure-Samples/acr-build-helloworld-node.git -f Dockerfile-app --build-arg REGISTRY_NAME=mybaseregistry.azurecr.io
  - push: ["$Registry/hello-world:$ID"]
```

Krok kompilacji używa `Dockerfile-app` pliku w repozytorium [Azure-Samples/ACR-Build-HelloWorld-Node](https://github.com/Azure-Samples/acr-build-helloworld-node.git) do tworzenia obrazu. Odwołuje się do `--build-arg` rejestru podstawowego w celu ściągnięcia obrazu podstawowego. Po pomyślnym skompilowaniu obraz jest wypychany do rejestru używanego do uruchomienia zadania.

## <a name="option-1-create-task-with-user-assigned-identity"></a>Opcja 1. Tworzenie zadania z tożsamością przypisaną przez użytkownika

Kroki opisane w tej sekcji umożliwiają utworzenie zadania i włączenie tożsamości przypisanej do użytkownika. Jeśli zamiast tego chcesz włączyć tożsamość przypisaną do systemu, zobacz [opcję 2. Tworzenie zadania z tożsamością przypisaną do systemu](#option-2-create-task-with-system-assigned-identity). 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Tworzenie zadania

Utwórz zadanie *helloworldtask* , wykonując następujące polecenie [AZ ACR Task Create][az-acr-task-create] . Zadanie jest uruchamiane bez kontekstu kodu źródłowego, a polecenie odwołuje się do pliku `helloworldtask.yaml` w katalogu roboczym. `--assign-identity`Parametr przekazuje identyfikator zasobu tożsamości przypisanej do użytkownika. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Przyznaj uprawnienia do ściągania tożsamości do rejestru podstawowego

W tej sekcji nadaj zarządzanej tożsamości uprawnienia do ściągania z rejestru podstawowego, *mybaseregistry*.

Użyj polecenia [AZ ACR show][az-acr-show] , aby uzyskać identyfikator zasobu podstawowego rejestru i zapisać go w zmiennej:

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

Użyj polecenia [AZ role Assign Create][az-role-assignment-create] , aby przypisać tożsamości `acrpull` rolę do rejestru podstawowego. Ta rola ma uprawnienia tylko do ściągania obrazów z rejestru.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

Dodaj do [zadania poświadczenia rejestru docelowego](#add-target-registry-credentials-to-task).

## <a name="option-2-create-task-with-system-assigned-identity"></a>Opcja 2: Tworzenie zadania przy użyciu tożsamości przypisanej do systemu

Kroki opisane w tej sekcji umożliwiają utworzenie zadania i włączenie tożsamości przypisanej do systemu. Jeśli zamiast tego chcesz włączyć tożsamość przypisaną przez użytkownika, zobacz [Opcja 1. Tworzenie zadania z tożsamością przypisaną przez użytkownika](#option-1-create-task-with-user-assigned-identity). 

### <a name="create-task"></a>Tworzenie zadania

Utwórz zadanie *helloworldtask* , wykonując następujące polecenie [AZ ACR Task Create][az-acr-task-create] . Zadanie jest uruchamiane bez kontekstu kodu źródłowego, a polecenie odwołuje się do pliku `helloworldtask.yaml` w katalogu roboczym. `--assign-identity`Parametr bez wartości umożliwia włączenie do zadania tożsamości przypisanej do systemu. 

```azurecli
az acr task create \
  --registry myregistry \
  --name helloworldtask \
  --context /dev/null \
  --file helloworldtask.yaml \
  --assign-identity 
```
[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

### <a name="give-identity-pull-permissions-to-the-base-registry"></a>Przyznaj uprawnienia do ściągania tożsamości do rejestru podstawowego

W tej sekcji nadaj zarządzanej tożsamości uprawnienia do ściągania z rejestru podstawowego, *mybaseregistry*.

Użyj polecenia [AZ ACR show][az-acr-show] , aby uzyskać identyfikator zasobu podstawowego rejestru i zapisać go w zmiennej:

```azurecli
baseregID=$(az acr show --name mybaseregistry --query id --output tsv)
```

Użyj polecenia [AZ role Assign Create][az-role-assignment-create] , aby przypisać tożsamości `acrpull` rolę do rejestru podstawowego. Ta rola ma uprawnienia tylko do ściągania obrazów z rejestru.

```azurecli
az role assignment create \
  --assignee $principalID \
  --scope $baseregID \
  --role acrpull
```

## <a name="add-target-registry-credentials-to-task"></a>Dodaj docelowe poświadczenia rejestru do zadania

Teraz użyj polecenia [AZ ACR Task Credential Add][az-acr-task-credential-add] , aby umożliwić uwierzytelnienie zadania w rejestrze podstawowym przy użyciu poświadczeń tożsamości. Uruchom polecenie odpowiadające typowi tożsamości zarządzanej włączonej w zadaniu. Jeśli włączono tożsamość przypisaną przez użytkownika, Przekaż `--use-identity` jej identyfikator klienta. W przypadku włączenia tożsamości przypisanej do systemu należy przekazać polecenie `--use-identity [system]` .

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

Aby sprawdzić, czy zadanie, w którym włączono tożsamość zarządzaną, zostało pomyślnie uruchomione, ręcznie Wyzwól zadanie przy użyciu polecenia [AZ ACR Task Run][az-acr-task-run] . 

```azurecli
az acr task run \
  --name helloworldtask \
  --registry myregistry
```

Jeśli zadanie zostanie wykonane pomyślnie, dane wyjściowe są podobne do:

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
Step 2/6 : FROM ${REGISTRY_NAME}/baseimages/node:9-alpine
9-alpine: Pulling from baseimages/node
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
    tag: 9-alpine
    digest: sha256:e8e92cffd464fce3be9a3eefd1b65dc9cbe2484da31c11e813a4effc6105c00f
  git:
    git-head-revision: 0f988779c97fe0bfc7f2f74b88531617f4421643

Run ID: cf10 was successful after 32s
```

Uruchom polecenie [AZ ACR Repository show-Tags][az-acr-repository-show-tags] , aby sprawdzić, czy obraz został skompilowany i został pomyślnie wypychany do *rejestru*:

```azurecli
az acr repository show-tags --name myregistry --repository hello-world --output tsv
```

Przykładowe dane wyjściowe:

```console
cf10
```

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej [na temat włączania tożsamości zarządzanej w zadaniu ACR](container-registry-tasks-authentication-managed-identity.md).
* Zobacz [odwołanie ACR Tasks YAML](container-registry-tasks-reference-yaml.md)

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
