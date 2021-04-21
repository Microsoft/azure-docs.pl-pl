---
title: Uwierzytelnianie zewnętrzne z zadania usługi ACR
description: Skonfiguruj zadanie Azure Container Registry (ACR Task) do odczytywania poświadczeń usługi Docker Hub przechowywanych w usłudze Azure Key Vault przy użyciu tożsamości zarządzanej dla zasobów platformy Azure.
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: 16404f9244818d91c5333eb5eec5944bfdd9df98
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781203"
---
# <a name="external-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Uwierzytelnianie zewnętrzne w zadaniu usługi ACR przy użyciu tożsamości zarządzanej przez platformę Azure 

W [zadaniu usługi ACR](container-registry-tasks-overview.md)można włączyć [tożsamość zarządzaną dla zasobów platformy Azure.](container-registry-tasks-authentication-managed-identity.md) Zadanie może używać tożsamości do uzyskiwania dostępu do innych zasobów platformy Azure bez konieczności poświadczeń ani zarządzania nimi. 

Z tego artykułu dowiesz się, jak włączyć tożsamość zarządzaną w zadaniu, które uzyskuje dostęp do wpisów tajnych przechowywanych w usłudze Azure Key Vault. 

Aby utworzyć zasoby platformy Azure, ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.68 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

## <a name="scenario-overview"></a>Omówienie scenariusza

Przykładowe zadanie odczytuje poświadczenia Docker Hub przechowywane w usłudze Azure Key Vault. Poświadczenia są używane dla konta Docker Hub z uprawnieniami do zapisu (wypychania) do prywatnego Docker Hub prywatnego. Aby odczytać poświadczenia, należy skonfigurować zadanie przy użyciu tożsamości zarządzanej i przypisać do niego odpowiednie uprawnienia. Zadanie skojarzone z tożsamością tworzy obraz i Docker Hub, aby wypchnąć obraz do prywatnego repo. 

W tym przykładzie przedstawiono kroki przy użyciu tożsamości zarządzanej przypisanej przez użytkownika lub przez system. Wybór tożsamości zależy od potrzeb organizacji.

W rzeczywistym scenariuszu firma może publikować obrazy w prywatnym Docker Hub w ramach procesu kompilacji. 

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebujesz rejestru kontenerów platformy Azure, w którym uruchamiasz zadanie. W tym artykule ten rejestr nosi nazwę *myregistry*. W kolejnych krokach zastąp własną nazwą rejestru.

Jeśli nie masz jeszcze rejestru kontenerów platformy Azure, zobacz Szybki start: tworzenie prywatnego rejestru kontenerów przy [użyciu interfejsu wiersza polecenia platformy Azure.](container-registry-get-started-azure-cli.md) Nie musisz jeszcze wypychać obrazów do rejestru.

Potrzebujesz również prywatnego repozytorium w Docker Hub oraz konta Docker Hub z uprawnieniami do zapisu w repozytorium. W tym przykładzie to repo nosi nazwę *hubuser/hubrepo*. 

## <a name="create-a-key-vault-and-store-secrets"></a>Tworzenie magazynu kluczy i przechowywanie wpisów tajnych

Najpierw, jeśli to konieczne, utwórz grupę zasobów o nazwie *myResourceGroup* w lokalizacji *eastus* za pomocą następującego [polecenia az group create:][az-group-create]

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Użyj polecenia [az keyvault create,][az-keyvault-create] aby utworzyć magazyn kluczy. Pamiętaj, aby określić unikatową nazwę magazynu kluczy. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Przechowuj wymagane poświadczenia Docker Hub klucza w magazynie kluczy przy użyciu [polecenia az keyvault secret set.][az-keyvault-secret-set] W tych poleceniach wartości są przekazywane w zmiennych środowiskowych:

```azurecli
# Store Docker Hub user name
az keyvault secret set \
  --name UserName \
  --value $USERNAME \
  --vault-name mykeyvault

# Store Docker Hub password
az keyvault secret set \
  --name Password \
  --value $PASSWORD \
  --vault-name mykeyvault
```

W rzeczywistym scenariuszu wpisy tajne byłyby prawdopodobnie ustawiane i utrzymywane w osobnym procesie.

## <a name="define-task-steps-in-yaml-file"></a>Definiowanie kroków zadań w pliku YAML

Kroki tego przykładowego zadania są zdefiniowane w pliku [YAML](container-registry-tasks-reference-yaml.md). Utwórz plik o nazwie `dockerhubtask.yaml` w lokalnym katalogu pracy i wklej następującą zawartość. Pamiętaj, aby zastąpić nazwę magazynu kluczy w pliku nazwą magazynu kluczy.

```yml
version: v1.1.0
# Replace mykeyvault with the name of your key vault
secrets:
  - id: username
    keyvault: https://mykeyvault.vault.azure.net/secrets/UserName
  - id: password
    keyvault: https://mykeyvault.vault.azure.net/secrets/Password
steps:
# Log in to Docker Hub
  - cmd: bash echo '{{.Secrets.password}}' | docker login --username '{{.Secrets.username}}' --password-stdin 
# Build image
  - build: -t {{.Values.PrivateRepo}}:$ID https://github.com/Azure-Samples/acr-tasks.git -f hello-world.dockerfile
# Push image to private repo in Docker Hub
  - push:
    - {{.Values.PrivateRepo}}:$ID
```

Kroki zadania wykonaj następujące czynności:

* Zarządzanie poświadczeniami tajnymi w celu uwierzytelniania za pomocą Docker Hub.
* Uwierzytelnij Docker Hub za pomocą polecenia , przekazując wpisy tajne do `docker login` polecenia .
* Skompilowanie obrazu przy użyciu przykładowego pliku Dockerfile w [repocie Azure-Samples/acr-tasks.](https://github.com/Azure-Samples/acr-tasks.git)
* Wypchnie obraz do prywatnego Docker Hub repozytorium.


## <a name="option-1-create-task-with-user-assigned-identity"></a>Opcja 1. Tworzenie zadania z tożsamością przypisaną przez użytkownika

Kroki opisane w tej sekcji umożliwiają utworzenie zadania i włączenie tożsamości przypisanej przez użytkownika. Jeśli zamiast tego chcesz włączyć tożsamość przypisaną przez system, zobacz Opcja 2. Tworzenie zadania z [tożsamością przypisaną przez system.](#option-2-create-task-with-system-assigned-identity) 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Tworzenie zadania

Utwórz zadanie *dockerhubtask,* wykonując następujące [polecenie az acr task create.][az-acr-task-create] Zadanie jest uruchamiane bez kontekstu kodu źródłowego, a polecenie odwołuje się do pliku `dockerhubtask.yaml` w katalogu pracy. Parametr `--assign-identity` przekazuje identyfikator zasobu tożsamości przypisanej przez użytkownika. 

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]


### <a name="grant-identity-access-to-key-vault"></a>Udzielanie dostępu tożsamości do magazynu kluczy

Uruchom następujące polecenie [az keyvault set-policy,][az-keyvault-set-policy] aby ustawić zasady dostępu w magazynie kluczy. Poniższy przykład umożliwia tożsamości odczytywanie wpisów tajnych z magazynu kluczy. 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

Przejdź do [ręcznie uruchomić zadanie](#manually-run-the-task).

## <a name="option-2-create-task-with-system-assigned-identity"></a>Opcja 2. Tworzenie zadania z tożsamością przypisaną przez system

Kroki opisane w tej sekcji umożliwiają utworzenie zadania i włączenie tożsamości przypisanej przez system. Jeśli zamiast tego chcesz włączyć tożsamość przypisaną przez użytkownika, zobacz Opcja 1. Tworzenie zadania z tożsamością [przypisaną przez użytkownika.](#option-1-create-task-with-user-assigned-identity) 

### <a name="create-task"></a>Tworzenie zadania

Utwórz zadanie *dockerhubtask,* wykonując następujące [polecenie az acr task create.][az-acr-task-create] Zadanie jest uruchamiane bez kontekstu kodu źródłowego, a polecenie odwołuje się do pliku `dockerhubtask.yaml` w katalogu pracy. Parametr `--assign-identity` bez wartości włącza tożsamość przypisaną przez system w zadaniu.  

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity 
```

[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

### <a name="grant-identity-access-to-key-vault"></a>Udzielanie dostępu tożsamości do magazynu kluczy

Uruchom następujące polecenie [az keyvault set-policy,][az-keyvault-set-policy] aby ustawić zasady dostępu w magazynie kluczy. Poniższy przykład umożliwia tożsamości odczytywanie wpisów tajnych z magazynu kluczy. 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

## <a name="manually-run-the-task"></a>Ręczne uruchamianie zadania

Aby sprawdzić, czy zadanie, w którym włączono tożsamość zarządzaną, działa pomyślnie, ręcznie wyzwolij zadanie za pomocą [polecenia az acr task run.][az-acr-task-run] Parametr `--set` służy do przekazania nazwy prywatnego repo do zadania. W tym przykładzie nazwa zastępczego repo to *hubuser/hubrepo*.

```azurecli
az acr task run --name dockerhubtask --registry myregistry --set PrivateRepo=hubuser/hubrepo
```

Gdy zadanie zostanie pomyślnie uruchomiony, dane wyjściowe pokazują pomyślne uwierzytelnienie w u Docker Hub, a obraz zostanie pomyślnie s skuowany i wypchnięty do prywatnego repo:

```console
Queued a run with ID: cf24
Waiting for an agent...
2019/06/20 18:05:55 Using acb_vol_b1edae11-30de-4f2b-a9c7-7d743e811101 as the home volume
2019/06/20 18:05:58 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/20 18:05:58 Successfully set up Docker network: acb_default_network
2019/06/20 18:05:58 Setting up Docker configuration...
2019/06/20 18:05:59 Successfully set up Docker configuration
2019/06/20 18:05:59 Logging in to registry: myregistry.azurecr.io
2019/06/20 18:06:00 Successfully logged into myregistry.azurecr.io
2019/06/20 18:06:00 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:00 Launching container with name: acb_step_0
[...]
Login Succeeded
2019/06/20 18:06:02 Successfully executed container: acb_step_0
2019/06/20 18:06:02 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:02 Scanning for dependencies...
2019/06/20 18:06:04 Successfully scanned dependencies
2019/06/20 18:06:04 Launching container with name: acb_step_1
Sending build context to Docker daemon    129kB
[...]
2019/06/20 18:06:07 Successfully pushed image: hubuser/hubrepo:cf24
2019/06/20 18:06:07 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 2.064353)
2019/06/20 18:06:07 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.594061)
2019/06/20 18:06:07 Populating digests for step ID: acb_step_1...
2019/06/20 18:06:09 Successfully populated digests for step ID: acb_step_1
2019/06/20 18:06:09 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.743923)
2019/06/20 18:06:09 The following dependencies were found:
2019/06/20 18:06:09
- image:
    registry: registry.hub.docker.com
    repository: hubuser/hubrepo
    tag: cf24
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0e11c388b664df8a27a901dce21eb89f11d8292f7fca1b3e3c4321bf7897bffe
  git:
    git-head-revision: b0ffa6043dd893a4c75644c5fed384c82ebb5f9e

Run ID: cf24 was successful after 15s
```

Aby potwierdzić wypchniętą ilustrację, sprawdź tag (w tym przykładzie) w prywatnym `cf24` Docker Hub repo.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [włączaniu tożsamości zarządzanej w zadaniu usługi ACR.](container-registry-tasks-authentication-managed-identity.md)
* Zobacz zadania usługi ACR [YAML](container-registry-tasks-reference-yaml.md)


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az_login
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-build]: /cli/azure/acr#az_acr_build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az_acr_repository_show_tags
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-show]: /cli/azure/identity#az_identity_show
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-show]: /cli/azure/acr/task#az_acr_task_show
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-list-runs]: /cli/azure/acr/task#az_acr_task_list_runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az_acr_task_credential_add
[az-group-create]: /cli/azure/group?#az_group_create
[az-keyvault-create]: /cli/azure/keyvault?#az_keyvault_create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az_keyvault_secret_set
[az-keyvault-set-policy]: /cli/azure/keyvault#az_keyvault_set_policy
