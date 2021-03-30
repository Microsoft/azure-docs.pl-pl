---
title: Uwierzytelnianie zewnętrzne z zadania ACR
description: Skonfiguruj zadanie Azure Container Registry (zadanie ACR) w celu odczytania poświadczeń usługi Docker Hub przechowywanych w magazynie kluczy platformy Azure przy użyciu tożsamości zarządzanej dla zasobów platformy Azure.
ms.topic: article
ms.date: 07/06/2020
ms.openlocfilehash: 0bc43f958a14016146160a06372af0b36a9fff75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86058133"
---
# <a name="external-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Uwierzytelnianie zewnętrzne w ACR zadania przy użyciu tożsamości zarządzanej przez platformę Azure 

W [zadaniu ACR](container-registry-tasks-overview.md)można [włączyć zarządzaną tożsamość dla zasobów platformy Azure](container-registry-tasks-authentication-managed-identity.md). Zadanie może korzystać z tożsamości w celu uzyskania dostępu do innych zasobów platformy Azure bez konieczności podania poświadczeń ani zarządzania nimi. 

W tym artykule dowiesz się, jak włączyć zarządzaną tożsamość w zadaniu, które uzyskuje dostęp do wpisów tajnych przechowywanych w magazynie kluczy platformy Azure. 

Aby utworzyć zasoby platformy Azure, ten artykuł wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0.68 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

## <a name="scenario-overview"></a>Omówienie scenariusza

Przykładowe zadanie odczytuje poświadczenia usługi Docker Hub przechowywane w magazynie kluczy Azure. Poświadczenia są przeznaczone dla konta usługi Docker Hub z uprawnieniami do zapisu (wypychania) do prywatnego repozytorium usługi Docker Hub. Aby odczytać poświadczenia, należy skonfigurować zadanie przy użyciu tożsamości zarządzanej i przypisać do niego odpowiednie uprawnienia. Zadanie skojarzone z tożsamością kompiluje obraz i loguje się do centrum Docker w celu wypchnięcia obrazu do repozytorium prywatnego. 

W tym przykładzie przedstawiono kroki korzystające z tożsamości zarządzanej przypisanej przez użytkownika lub przypisanej do systemu. Wybór tożsamości zależy od potrzeb organizacji.

W rzeczywistym scenariuszu firma może publikować obrazy w repozytorium prywatnym w usłudze Docker Hub jako część procesu kompilacji. 

## <a name="prerequisites"></a>Wymagania wstępne

Potrzebna jest usługa Azure Container Registry, w której uruchomiono zadanie. W tym artykule rejestr nazywa się *rejestrem*. Zamień na własną nazwę rejestru w dalszych krokach.

Jeśli nie masz jeszcze usługi Azure Container Registry, zobacz [Szybki Start: Tworzenie prywatnego rejestru kontenerów za pomocą interfejsu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md). Nie jest jeszcze konieczne wypychanie obrazów do rejestru.

Potrzebne jest również prywatne repozytorium w usłudze Docker Hub oraz konto centrum Docker z uprawnieniami do zapisu w repozytorium. W tym przykładzie repozytorium nosi nazwę *hubuser/hubrepo*. 

## <a name="create-a-key-vault-and-store-secrets"></a>Tworzenie magazynu kluczy i przechowywanie wpisów tajnych

Po pierwsze, jeśli chcesz, Utwórz grupę zasobów o nazwie Moja *zasobów* w lokalizacji *Wschodnie* przy użyciu następującego polecenia [AZ Group Create][az-group-create] :

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Użyj polecenia [AZ Key magazynu Create][az-keyvault-create] , aby utworzyć magazyn kluczy. Pamiętaj, aby określić unikatową nazwę magazynu kluczy. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

W magazynie kluczy należy przechowywać wymagane poświadczenia usługi Docker Hub przy użyciu polecenia [AZ Key magazynu tajnego Set][az-keyvault-secret-set] . W tych poleceniach wartości są przesyłane w zmiennych środowiskowych:

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

W rzeczywistym scenariuszu wpisy tajne byłyby prawdopodobnie ustawione i utrzymywane w osobnym procesie.

## <a name="define-task-steps-in-yaml-file"></a>Zdefiniuj kroki zadania w pliku YAML

Kroki tego przykładowego zadania są zdefiniowane w [pliku YAML](container-registry-tasks-reference-yaml.md). Utwórz plik o nazwie `dockerhubtask.yaml` w lokalnym katalogu roboczym i wklej poniższą zawartość. Pamiętaj, aby zastąpić nazwę magazynu kluczy w pliku nazwą magazynu kluczy.

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

* Zarządzaj poświadczeniami tajnymi w celu uwierzytelniania za pomocą narzędzia Docker Hub.
* Uwierzytelnianie za pomocą narzędzia Docker Hub przez przekazanie wpisów tajnych do `docker login` polecenia.
* Kompilowanie obrazu przy użyciu przykładowej pliku dockerfile w repozytorium [Azure-Samples/ACR-Tasks](https://github.com/Azure-Samples/acr-tasks.git) .
* Wypchnij obraz do prywatnego repozytorium centrum platformy Docker.


## <a name="option-1-create-task-with-user-assigned-identity"></a>Opcja 1. Tworzenie zadania z tożsamością przypisaną przez użytkownika

Kroki opisane w tej sekcji umożliwiają utworzenie zadania i włączenie tożsamości przypisanej do użytkownika. Jeśli zamiast tego chcesz włączyć tożsamość przypisaną do systemu, zobacz [opcję 2. Tworzenie zadania z tożsamością przypisaną do systemu](#option-2-create-task-with-system-assigned-identity). 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Tworzenie zadania

Utwórz zadanie *dockerhubtask* , wykonując następujące polecenie [AZ ACR Task Create][az-acr-task-create] . Zadanie jest uruchamiane bez kontekstu kodu źródłowego, a polecenie odwołuje się do pliku `dockerhubtask.yaml` w katalogu roboczym. `--assign-identity`Parametr przekazuje identyfikator zasobu tożsamości przypisanej do użytkownika. 

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]


### <a name="grant-identity-access-to-key-vault"></a>Udzielanie tożsamości dostępu do magazynu kluczy

Uruchom następujące polecenie [AZ KeyBinding Set-Policy][az-keyvault-set-policy] , aby ustawić zasady dostępu w magazynie kluczy. Poniższy przykład umożliwia tożsamości odczytywanie wpisów tajnych z magazynu kluczy. 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

W tym celu należy wykonać [zadanie ręcznie](#manually-run-the-task).

## <a name="option-2-create-task-with-system-assigned-identity"></a>Opcja 2: Tworzenie zadania przy użyciu tożsamości przypisanej do systemu

Kroki opisane w tej sekcji umożliwiają utworzenie zadania i włączenie tożsamości przypisanej do systemu. Jeśli zamiast tego chcesz włączyć tożsamość przypisaną przez użytkownika, zobacz [Opcja 1. Tworzenie zadania z tożsamością przypisaną przez użytkownika](#option-1-create-task-with-user-assigned-identity). 

### <a name="create-task"></a>Tworzenie zadania

Utwórz zadanie *dockerhubtask* , wykonując następujące polecenie [AZ ACR Task Create][az-acr-task-create] . Zadanie jest uruchamiane bez kontekstu kodu źródłowego, a polecenie odwołuje się do pliku `dockerhubtask.yaml` w katalogu roboczym. `--assign-identity`Parametr bez wartości umożliwia włączenie do zadania tożsamości przypisanej do systemu.  

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity 
```

[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

### <a name="grant-identity-access-to-key-vault"></a>Udzielanie tożsamości dostępu do magazynu kluczy

Uruchom następujące polecenie [AZ KeyBinding Set-Policy][az-keyvault-set-policy] , aby ustawić zasady dostępu w magazynie kluczy. Poniższy przykład umożliwia tożsamości odczytywanie wpisów tajnych z magazynu kluczy. 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

## <a name="manually-run-the-task"></a>Ręczne uruchamianie zadania

Aby sprawdzić, czy zadanie, w którym włączono tożsamość zarządzaną, zostało pomyślnie uruchomione, ręcznie Wyzwól zadanie przy użyciu polecenia [AZ ACR Task Run][az-acr-task-run] . `--set`Parametr jest używany do przekazywania nazwy repozytorium prywatnego do zadania. W tym przykładzie nazwa repozytorium zastępczego to *hubuser/hubrepo*.

```azurecli
az acr task run --name dockerhubtask --registry myregistry --set PrivateRepo=hubuser/hubrepo
```

Po pomyślnym uruchomieniu zadania dane wyjściowe pokazują pomyślne uwierzytelnienie w usłudze Docker Hub, a obraz został pomyślnie skompilowany i wypychany do repozytorium prywatnego:

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

Aby potwierdzić, że obraz jest wypychany, sprawdź tag ( `cf24` w tym przykładzie) w prywatnym repozytorium usługi Docker Hub.

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
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
[az-keyvault-create]: /cli/azure/keyvault?#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
