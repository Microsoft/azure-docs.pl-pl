---
title: Tożsamość zarządzana w zadaniu usługi ACR
description: Włącz tożsamość zarządzaną dla zasobów platformy Azure w zadaniu Azure Container Registry, aby umożliwić zadaniu dostęp do innych zasobów platformy Azure, w tym innych prywatnych rejestrów kontenerów.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/14/2020
ms.author: danlep
ms.openlocfilehash: 19d63861a98884ff2f5103946c19e2226c4b14b7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781167"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Używanie tożsamości zarządzanej platformy Azure w usłudze zadania usługi ACR 

Włącz tożsamość [zarządzaną dla zasobów](../active-directory/managed-identities-azure-resources/overview.md) platformy Azure w zadaniu [usługi ACR,](container-registry-tasks-overview.md)aby zadanie było w stanie uzyskać dostęp do innych zasobów platformy Azure bez konieczności poświadczeń ani zarządzania nimi. Na przykład użyj tożsamości zarządzanej, aby umożliwić krokowi zadania ściągnięcie lub wypchnięcie obrazów kontenera do innego rejestru.

Z tego artykułu dowiesz się, jak za pomocą interfejsu wiersza polecenia platformy Azure włączyć tożsamość zarządzaną przypisaną przez użytkownika lub przypisaną przez system w zadaniu usługi ACR. Możesz użyć interfejsu Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure. Jeśli chcesz używać go lokalnie, wymagana jest wersja 2.0.68 lub nowsza. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

W celach ilustracyjne przykładowe polecenia w tym artykule używają polecenia [az acr task create][az-acr-task-create] do utworzenia podstawowego zadania kompilacji obrazu, które włącza tożsamość zarządzaną. Aby uzyskać przykładowe scenariusze uzyskiwania dostępu do zabezpieczonych zasobów z zadania usługi ACR przy użyciu tożsamości zarządzanej, zobacz:

* [Uwierzytelnianie między rejestrami](container-registry-tasks-cross-registry-authentication.md)
* [Uzyskiwanie dostępu do zasobów zewnętrznych za pomocą wpisów tajnych przechowywanych w Azure Key Vault](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>Dlaczego warto używać tożsamości zarządzanej?

Tożsamość zarządzana dla zasobów platformy Azure zapewnia wybranym usługom platformy Azure automatycznie zarządzaną tożsamość w Azure Active Directory. Zadanie usługi ACR można skonfigurować za pomocą tożsamości zarządzanej, aby zadanie było w celu uzyskania dostępu do innych zabezpieczonych zasobów platformy Azure bez przekazywania poświadczeń w krokach zadania.

Tożsamości zarządzane są dwa typy:

* *Tożsamości przypisane przez użytkownika*, które można przypisać do wielu zasobów i utrwalać tak długo, jak chcesz. Tożsamości przypisane przez użytkownika są obecnie dostępne w wersji zapoznawczej.

* Tożsamość *przypisana przez system*, która jest unikatowa dla określonego zasobu, takiego jak zadanie ACR, i trwa przez okres istnienia tego zasobu.

W zadaniu ACR można włączyć jeden lub oba typy tożsamości. Przyznaj tożsamości dostęp do innego zasobu, podobnie jak do każdego podmiotu zabezpieczeń. Gdy zadanie jest uruchamiane, używa tożsamości w celu uzyskania dostępu do zasobu we wszystkich krokach zadania, które wymagają dostępu.

## <a name="steps-to-use-a-managed-identity"></a>Procedura używania tożsamości zarządzanej

Wykonaj następujące kroki wysokiego poziomu, aby użyć tożsamości zarządzanej z zadaniem usługi ACR.

### <a name="1-optional-create-a-user-assigned-identity"></a>1. (Opcjonalnie) Tworzenie tożsamości przypisanej przez użytkownika

Jeśli planujesz używać tożsamości przypisanej przez użytkownika, użyj istniejącej tożsamości lub utwórz tożsamość przy użyciu interfejsu wiersza polecenia platformy Azure lub innych narzędzi platformy Azure. Na przykład użyj [polecenia az identity create.][az-identity-create] 

Jeśli planujesz używać tylko tożsamości przypisanej przez system, pomiń ten krok. Tożsamość przypisana przez system jest tworzymy podczas tworzenia zadania ACR.

### <a name="2-enable-identity-on-an-acr-task"></a>2. Włączanie tożsamości w zadaniu ACR

Podczas tworzenia zadania ACR opcjonalnie włącz tożsamość przypisaną przez użytkownika, tożsamość przypisaną przez system lub obie te tożsamości. Na przykład przekaż parametr po uruchomieniu polecenia `--assign-identity` [az acr task create][az-acr-task-create] w interfejsie wiersza polecenia platformy Azure.

Aby włączyć tożsamość przypisaną przez system, przekaż wartość bez `--assign-identity` wartości lub `assign-identity [system]` . Następujące przykładowe polecenie tworzy zadanie systemu Linux z publicznego repozytorium GitHub, które tworzy obraz i włącza tożsamość zarządzaną `hello-world` przypisaną przez system:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --file Dockerfile \
    --commit-trigger-enabled false \
    --assign-identity
```

Aby włączyć tożsamość przypisaną przez użytkownika, przekaż wartość z `--assign-identity` *identyfikatorem zasobu* tożsamości. Następujące przykładowe polecenie tworzy zadanie systemu Linux z publicznego repozytorium GitHub, które tworzy obraz i włącza tożsamość zarządzaną `hello-world` przypisaną przez użytkownika:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --file Dockerfile \
    --commit-trigger-enabled false
    --assign-identity <resourceID>
```

Identyfikator zasobu tożsamości można uzyskać, uruchamiając [polecenie az identity show.][az-identity-show] Identyfikator zasobu o identyfikatorze *myUserAssignedIdentity* w grupie zasobów *myResourceGroup* ma postać: 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. Udzielanie tożsamości uprawnień dostępu do innych zasobów platformy Azure

W zależności od wymagań zadania przyznaj tożsamości uprawnienia dostępu do innych zasobów platformy Azure. Przykłady obejmują:

* Przypisz tożsamości zarządzanej rolę z uprawnieniami ściągania, wypychania i ściągania lub innymi uprawnieniami do docelowego rejestru kontenerów na platformie Azure. Aby uzyskać pełną listę ról rejestru, zobacz [Azure Container Registry ról i uprawnień.](container-registry-roles.md) 
* Przypisz tożsamości zarządzanej rolę do odczytywania wpisów tajnych w usłudze Azure Key Vault.

Użyj interfejsu [wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md) lub innych narzędzi platformy Azure, aby zarządzać dostępem opartym na rolach do zasobów. Na przykład uruchom polecenie [az role assignment create,][az-role-assignment-create] aby przypisać tożsamość do zasobu. 

W poniższym przykładzie przypisano tożsamości zarządzanej uprawnienia do ściągania z rejestru kontenerów. Polecenie określa *identyfikator podmiotu zabezpieczeń* tożsamości zadania i *identyfikator zasobu* rejestru docelowego.


```azurecli
az role assignment create \
  --assignee <principalID> \
  --scope <registryID> \
  --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. (Opcjonalnie) Dodawanie poświadczeń do zadania

Jeśli zadanie wymaga poświadczeń w celu ściągania lub wypychania obrazów do innego rejestru niestandardowego lub uzyskiwania dostępu do innych zasobów, dodaj poświadczenia do zadania. Uruchom polecenie [az acr task credential add,][az-acr-task-credential-add] aby dodać poświadczenia, i przekaż parametr , aby wskazać, że tożsamość może uzyskać `--use-identity` dostęp do poświadczeń. 

Aby na przykład dodać poświadczenia dla tożsamości przypisanej przez system w celu uwierzytelnienia w docelowym *rejestrze* kontenerów platformy Azure, przekaż wartość `use-identity [system]` :

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

Aby dodać poświadczenia dla tożsamości przypisanej przez użytkownika w celu uwierzytelnienia za pomocą rejestru *targetregistry,* przekaż wartość identyfikatora klienta `use-identity` tożsamości.  Na przykład:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

Identyfikator klienta tożsamości można uzyskać, uruchamiając [polecenie az identity show.][az-identity-show] Identyfikator klienta jest identyfikatorem GUID formularza `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` .

### <a name="5-run-the-task"></a>5. Uruchamianie zadania

Po skonfigurowaniu zadania przy użyciu tożsamości zarządzanej uruchom zadanie. Aby na przykład przetestować jedno z zadań utworzonych w tym artykule, wyzwolij je ręcznie za pomocą [polecenia az acr task run.][az-acr-task-run] Jeśli skonfigurowano dodatkowe, automatyczne wyzwalacze zadań, zadanie jest uruchamiane po jego automatycznym wyzwoleniu.

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano sposób włączania i używania tożsamości zarządzanej przypisanej przez użytkownika lub przypisanej przez system w zadaniu usługi ACR. Aby uzyskać scenariusze uzyskiwania dostępu do zabezpieczonych zasobów z zadania usługi ACR przy użyciu tożsamości zarządzanej, zobacz:

* [Uwierzytelnianie między rejestrami](container-registry-tasks-cross-registry-authentication.md)
* [Uzyskiwanie dostępu do zasobów zewnętrznych za pomocą wpisów tajnych przechowywanych w Azure Key Vault](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-show]: /cli/azure/identity#az_identity_show
[az-acr-task-create]: /cli/azure/acr/task#az_acr_task_create
[az-acr-task-run]: /cli/azure/acr/task#az_acr_task_run
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az_acr_task_credential_add
[azure-cli-install]: /cli/azure/install-azure-cli
