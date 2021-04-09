---
title: Tożsamość zarządzana w zadaniu ACR
description: Włącz zarządzaną tożsamość zasobów platformy Azure w zadaniu Azure Container Registry, aby umożliwić zadanie dostępu do innych zasobów platformy Azure, w tym innych prywatnych rejestrów kontenerów.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/14/2020
ms.author: danlep
ms.openlocfilehash: 8f2749a18a5ac6aed0822553d59beaacc9060228
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98915951"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Korzystanie z tożsamości zarządzanej przez platformę Azure w zadaniach ACR 

Włącz [zarządzaną tożsamość zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) w [zadaniu ACR](container-registry-tasks-overview.md), aby zadanie miało dostęp do innych zasobów platformy Azure bez konieczności udostępniania poświadczeń ani zarządzania nimi. Na przykład użyj tożsamości zarządzanej, aby umożliwić krokowi zadania ściąganie lub wypychanie obrazów kontenera do innego rejestru.

W tym artykule dowiesz się, jak korzystać z interfejsu wiersza polecenia platformy Azure w celu włączenia zarządzanej tożsamości przypisanej przez użytkownika lub przypisanej do systemu w ramach zadania ACR. Możesz użyć Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure. Jeśli chcesz używać go lokalnie, wymagana jest wersja 2.0.68 lub nowsza. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli-install].

Przykładowe polecenia w tym [artykule służą do tworzenia][az-acr-task-create] podstawowego zadania tworzenia obrazu, które umożliwia tożsamość zarządzaną. Przykładowe scenariusze umożliwiające dostęp do zabezpieczonych zasobów z zadania ACR przy użyciu tożsamości zarządzanej można znaleźć w temacie:

* [Uwierzytelnianie między rejestrami](container-registry-tasks-cross-registry-authentication.md)
* [Dostęp do zasobów zewnętrznych przy użyciu wpisów tajnych przechowywanych w Azure Key Vault](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>Dlaczego warto używać tożsamości zarządzanej?

Zarządzana tożsamość zasobów platformy Azure umożliwia wybranie usług platformy Azure z automatyczną tożsamością zarządzaną w Azure Active Directory. Zadanie ACR można skonfigurować przy użyciu tożsamości zarządzanej, aby zadanie miało dostęp do innych zabezpieczonych zasobów platformy Azure bez przekazywania poświadczeń w krokach zadania.

Zarządzane tożsamości są dwa typy:

* *Tożsamości przypisane przez użytkownika*, które można przypisać do wielu zasobów i utrzymywać je tak długo, jak chcesz. Tożsamości przypisane przez użytkownika są obecnie w wersji zapoznawczej.

* *Tożsamość przypisana przez system*, która jest unikatowa dla określonego zasobu, takiego jak zadanie ACR i obowiązuje w okresie istnienia tego zasobu.

W zadaniu ACR można włączyć albo oba typy tożsamości. Przyznaj tożsamości dostęp do innego zasobu, podobnie jak każdy podmiot zabezpieczeń. Gdy zadanie zostanie uruchomione, używa tożsamości w celu uzyskania dostępu do zasobu w dowolnym kroku zadania, który wymaga dostępu.

## <a name="steps-to-use-a-managed-identity"></a>Procedura używania tożsamości zarządzanej

Wykonaj te czynności wyższego poziomu, aby użyć zarządzanej tożsamości z zadaniem ACR.

### <a name="1-optional-create-a-user-assigned-identity"></a>1. (opcjonalnie) Utwórz tożsamość przypisaną przez użytkownika

Jeśli planujesz użycie tożsamości przypisanej do użytkownika, Użyj istniejącej tożsamości lub Utwórz tożsamość przy użyciu interfejsu wiersza polecenia platformy Azure lub innych narzędzi platformy Azure. Na przykład użyj polecenia [AZ Identity Create][az-identity-create] . 

Jeśli planujesz używać tylko tożsamości przypisanej do systemu, Pomiń ten krok. Podczas tworzenia zadania ACR można utworzyć tożsamość przypisaną przez system.

### <a name="2-enable-identity-on-an-acr-task"></a>2. Włącz tożsamość dla zadania ACR

Podczas tworzenia zadania ACR opcjonalnie należy włączyć tożsamość przypisaną przez użytkownika, tożsamość przypisaną do systemu lub obie te funkcje. Na przykład, należy przekazać `--assign-identity` parametr, gdy uruchamiasz polecenie [AZ ACR Task Create][az-acr-task-create] w interfejsie wiersza polecenia platformy Azure.

Aby włączyć tożsamość przypisaną do systemu, należy przekazać `--assign-identity` bez wartości lub `assign-identity [system]` . Poniższe przykładowe polecenie tworzy zadanie systemu Linux na podstawie publicznego repozytorium GitHub, które kompiluje `hello-world` obraz i umożliwia tożsamość zarządzaną przypisaną przez system:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --file Dockerfile \
    --commit-trigger-enabled false \
    --assign-identity
```

Aby włączyć tożsamość przypisaną przez użytkownika, należy przekazać `--assign-identity` wartość *identyfikatora zasobu* tożsamości. Poniższe przykładowe polecenie tworzy zadanie systemu Linux na podstawie publicznego repozytorium GitHub, które kompiluje `hello-world` obraz i umożliwia tożsamość zarządzaną przez użytkownika:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git#main \
    --file Dockerfile \
    --commit-trigger-enabled false
    --assign-identity <resourceID>
```

Identyfikator zasobu tożsamości można uzyskać, uruchamiając polecenie [AZ Identity show][az-identity-show] . Identyfikator zasobu dla identyfikatora *myUserAssignedIdentity* *w grupie zasobów resourceName* ma postać: 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. Przyznaj tożsamości uprawnienia dostępu do innych zasobów platformy Azure

W zależności od wymagań zadania Przyznaj tożsamości uprawnienia dostępu do innych zasobów platformy Azure. Przykłady obejmują:

* Przypisywanie tożsamości zarządzanej roli za pomocą ściągania, wypychania i ściągania lub innych uprawnień do rejestru kontenerów docelowych na platformie Azure. Pełną listę ról rejestru można znaleźć w temacie [Azure Container Registry role i uprawnienia](container-registry-roles.md). 
* Przypisanie tożsamości zarządzanej roli do odczytu wpisów tajnych w magazynie kluczy platformy Azure.

Użyj [interfejsu wiersza polecenia platformy Azure](../role-based-access-control/role-assignments-cli.md) lub innych narzędzi platformy Azure, aby zarządzać dostępem opartym na rolach do zasobów. Na przykład uruchom polecenie [AZ role Assign Create][az-role-assignment-create] , aby przypisać tożsamość roli do zasobu. 

Poniższy przykład przypisuje zarządzanej tożsamości uprawnienia do ściągania z rejestru kontenerów. Polecenie określa *Identyfikator podmiotu zabezpieczeń* tożsamości zadania i *Identyfikator zasobu* docelowego rejestru.


```azurecli
az role assignment create \
  --assignee <principalID> \
  --scope <registryID> \
  --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. (opcjonalnie) Dodaj poświadczenia do zadania

Jeśli zadanie wymaga poświadczeń do ściągania lub wypychania obrazów do innego niestandardowego rejestru lub do uzyskiwania dostępu do innych zasobów, Dodaj poświadczenia do zadania. Uruchom polecenie [AZ ACR Task Credential Add][az-acr-task-credential-add] , aby dodać poświadczenia, i przekaż `--use-identity` parametr, aby wskazać, że tożsamość może uzyskać dostęp do poświadczeń. 

Na przykład aby dodać poświadczenia dla tożsamości przypisanej do systemu w celu uwierzytelnienia w usłudze Azure Container Registry *targetregistry*, Przekaż `use-identity [system]` :

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

Aby dodać poświadczenia dla tożsamości przypisanej do użytkownika w celu uwierzytelnienia za pomocą rejestru *targetregistry*, Przekaż `use-identity` wartość *identyfikatora klienta* tożsamości. Na przykład:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

Identyfikator klienta tożsamości można uzyskać, uruchamiając polecenie [AZ Identity show][az-identity-show] . Identyfikator klienta jest identyfikatorem GUID formularza `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` .

### <a name="5-run-the-task"></a>5. Uruchom zadanie

Po skonfigurowaniu zadania z tożsamością zarządzaną Uruchom zadanie. Na przykład w celu przetestowania jednego z zadań utworzonych w tym artykule ręcznie Wyzwól je za pomocą polecenia [AZ ACR Task Run][az-acr-task-run] . Jeśli skonfigurowano dodatkowe, zautomatyzowane Wyzwalacze zadań, zadanie zostanie uruchomione, gdy zostanie wyzwolone automatycznie.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono sposób włączania i używania tożsamości zarządzanej przypisanej przez użytkownika lub przypisanej do systemu w ramach zadania ACR. Scenariusze umożliwiające dostęp do zabezpieczonych zasobów z zadania ACR przy użyciu tożsamości zarządzanej można znaleźć w temacie:

* [Uwierzytelnianie między rejestrami](container-registry-tasks-cross-registry-authentication.md)
* [Dostęp do zasobów zewnętrznych przy użyciu wpisów tajnych przechowywanych w Azure Key Vault](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[azure-cli-install]: /cli/azure/install-azure-cli
