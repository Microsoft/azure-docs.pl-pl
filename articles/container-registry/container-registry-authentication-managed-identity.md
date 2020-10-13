---
title: Uwierzytelnianie przy użyciu tożsamości zarządzanej
description: Zapewnianie dostępu do obrazów w prywatnym rejestrze kontenera przy użyciu przypisanej do użytkownika lub zarządzanej tożsamości platformy Azure przypisanej do systemu.
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: 9a144f0e865cfc9bf857752eed65dbe5cda88bd9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91253466"
---
# <a name="use-an-azure-managed-identity-to-authenticate-to-an-azure-container-registry"></a>Uwierzytelnianie w usłudze Azure Container Registry za pomocą tożsamości zarządzanej platformy Azure 

Za pomocą [zarządzanej tożsamości zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) można uwierzytelniać się w usłudze Azure Container Registry z innego zasobu platformy Azure bez konieczności udostępniania poświadczeń rejestru ani zarządzania nimi. Można na przykład skonfigurować tożsamość zarządzaną przez użytkownika lub przypisanej do systemu na maszynie wirtualnej z systemem Linux, aby uzyskać dostęp do obrazów kontenerów z rejestru kontenerów, podobnie jak w przypadku korzystania z rejestru publicznego.

W tym artykule dowiesz się więcej na temat tożsamości zarządzanych i instrukcje:

> [!div class="checklist"]
> * Włączanie tożsamości przypisanej do użytkownika lub przypisanej do systemu na maszynie wirtualnej platformy Azure
> * Udzielanie tożsamości dostępu do usługi Azure Container Registry
> * Korzystanie z tożsamości zarządzanej w celu uzyskania dostępu do rejestru i ściągania obrazu kontenera 

Aby utworzyć zasoby platformy Azure, ten artykuł wymaga uruchomienia interfejsu wiersza polecenia platformy Azure w wersji 2.0.55 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

Aby skonfigurować rejestr kontenerów i wypchnąć do niego obraz kontenera, należy również zainstalować lokalnie platformę Docker. Środowisko Docker zawiera pakiety, które umożliwiają łatwe konfigurowanie platformy Docker w systemie [macOS][docker-mac], [Windows][docker-windows] lub [Linux][docker-linux].

## <a name="why-use-a-managed-identity"></a>Dlaczego warto używać tożsamości zarządzanej?

Zarządzana tożsamość zasobów platformy Azure zapewnia usługi platformy Azure z automatycznie zarządzaną tożsamością w Azure Active Directory (Azure AD). Istnieje możliwość skonfigurowania [niektórych zasobów platformy Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md), w tym maszyn wirtualnych, z zarządzaną tożsamością. Następnie użyj tożsamości, aby uzyskać dostęp do innych zasobów platformy Azure, bez przekazywania poświadczeń w kodzie lub w skryptach.

Zarządzane tożsamości są dwa typy:

* *Tożsamości przypisane przez użytkownika*, które można przypisać do wielu zasobów i zachowują się tak długo, jak chcesz. Tożsamości przypisane przez użytkownika są obecnie w wersji zapoznawczej.

* *Tożsamość zarządzana przez system*, która jest unikatowa dla określonego zasobu, takiego jak Pojedyncza maszyna wirtualna i trwa okres istnienia tego zasobu.

Po skonfigurowaniu zasobu platformy Azure przy użyciu tożsamości zarządzanej nadaj tożsamości dostęp do innego zasobu, podobnie jak w przypadku każdego podmiotu zabezpieczeń. Na przykład Przypisz zarządzaną tożsamość rolę z ściąganiem, wypychaniem i ściąganiem lub innymi uprawnieniami do rejestru prywatnego na platformie Azure. Aby uzyskać pełną listę ról rejestru, zobacz [Azure Container Registry role i uprawnienia](container-registry-roles.md).) Możesz nadać tożsamości dostęp do jednego lub większej liczby zasobów.

Następnie użyj tożsamości do uwierzytelniania w dowolnej [usłudze, która obsługuje uwierzytelnianie w usłudze Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication), bez żadnych poświadczeń w kodzie. Aby użyć tożsamości w celu uzyskania dostępu do usługi Azure Container Registry z maszyny wirtualnej, należy uwierzytelnić się za pomocą Azure Resource Manager. Wybierz sposób uwierzytelniania przy użyciu tożsamości zarządzanej, w zależności od scenariusza:

* [Uzyskiwanie tokenu dostępu usługi Azure AD](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) programowo przy użyciu wywołań http lub REST

* Korzystanie z [zestawów SDK platformy Azure](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md)

* [Zaloguj się do interfejsu wiersza polecenia platformy Azure lub programu PowerShell](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md) przy użyciu tożsamości. 

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów

Jeśli nie masz jeszcze usługi Azure Container Registry, Utwórz rejestr i wypchnij przykładowy obraz kontenera do niego. Aby uzyskać instrukcje, zobacz [Szybki Start: Tworzenie prywatnego rejestru kontenerów za pomocą interfejsu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md).

W tym artykule przyjęto założenie, że `aci-helloworld:v1` obraz kontenera jest przechowywany w rejestrze. W przykładach użyto nazwy rejestru *myContainerRegistry*. Zastąp własnymi nazwami rejestru i obrazów w dalszych krokach.

## <a name="create-a-docker-enabled-vm"></a>Tworzenie maszyny wirtualnej z obsługą platformy Docker

Utwórz maszynę wirtualną z obsługą platformy Docker Ubuntu. Trzeba również zainstalować [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) na maszynie wirtualnej. Jeśli masz już maszynę wirtualną platformy Azure, Pomiń ten krok, aby utworzyć maszynę wirtualną.

Wdróż domyślną Ubuntu maszynę wirtualną platformy Azure za pomocą [AZ VM Create][az-vm-create]. Poniższy przykład tworzy maszynę wirtualną o nazwie *myDockerVM* w istniejącej grupie zasobów o nazwie Moja *resourceName*:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Utworzenie maszyny wirtualnej może potrwać kilka minut. Po zakończeniu wykonywania polecenia Zanotuj wyświetlane wartości w wierszu polecenia `publicIpAddress` platformy Azure. Użyj tego adresu, aby nawiązać połączenia SSH z maszyną wirtualną.

### <a name="install-docker-on-the-vm"></a>Zainstaluj platformę Docker na maszynie wirtualnej

Po uruchomieniu maszyny wirtualnej należy nawiązać połączenie SSH z maszyną wirtualną. Zastąp *publicIpAddress* publicznym adresem IP maszyny wirtualnej.

```bash
ssh azureuser@publicIpAddress
```

Uruchom następujące polecenie, aby zainstalować platformę Docker na maszynie wirtualnej:

```bash
sudo apt update
sudo apt install docker.io -y
```

Po zakończeniu instalacji uruchom następujące polecenie, aby sprawdzić, czy program Docker działa prawidłowo na maszynie wirtualnej:

```bash
sudo docker run -it hello-world
```

Dane wyjściowe:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Instalowanie interfejsu wiersza polecenia platformy Azure

Wykonaj kroki opisane w temacie [Instalowanie interfejsu wiersza polecenia platformy Azure z programem apt](/cli/azure/install-azure-cli-apt?view=azure-cli-latest) , aby zainstalować interfejs wiersza polecenia platformy Azure na maszynie wirtualnej Ubuntu. W tym artykule upewnij się, że instalujesz wersję 2.0.55 lub nowszą.

Zakończ sesję SSH.

## <a name="example-1-access-with-a-user-assigned-identity"></a>Przykład 1: dostęp z tożsamością przypisaną przez użytkownika

### <a name="create-an-identity"></a>Tworzenie tożsamości

Utwórz tożsamość w subskrypcji za pomocą polecenia [AZ Identity Create](/cli/azure/identity?view=azure-cli-latest#az-identity-create) . Możesz użyć tej samej grupy zasobów, która została wcześniej użyta do utworzenia rejestru kontenerów lub maszyny wirtualnej, lub innej.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRId
```

Aby skonfigurować tożsamość w poniższych krokach, użyj polecenia [AZ Identity show][az-identity-show] , aby zapisać identyfikator zasobu tożsamości i identyfikator jednostki usługi w zmiennych.

```azurecli
# Get resource ID of the user-assigned identity
userID=$(az identity show --resource-group myResourceGroup --name myACRId --query id --output tsv)

# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACRId --query principalId --output tsv)
```

Ponieważ w późniejszym czasie zalogowanie się do interfejsu wiersza polecenia z poziomu maszyny wirtualnej wymaga identyfikatora tożsamości, należy wyświetlić wartość:

```bash
echo $userID
```

Identyfikator ma postać:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRId
```

### <a name="configure-the-vm-with-the-identity"></a>Konfigurowanie maszyny wirtualnej przy użyciu tożsamości

Następujące polecenie [AZ VM Identity Assign][az-vm-identity-assign] KONFIGURUJe maszynę wirtualną platformy Docker przy użyciu tożsamości przypisanej do użytkownika:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM --identities $userID
```

### <a name="grant-identity-access-to-the-container-registry"></a>Udzielanie tożsamości dostępu do rejestru kontenerów

Teraz Skonfiguruj tożsamość, aby uzyskać dostęp do rejestru kontenerów. Najpierw użyj polecenia [AZ ACR show][az-acr-show] , aby uzyskać identyfikator zasobu rejestru:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Użyj polecenia [AZ role Assign Create][az-role-assignment-create] , aby przypisać rolę AcrPull do rejestru. Ta rola zapewnia [uprawnienia do ściągania](container-registry-roles.md) rejestru. Aby zapewnić uprawnienia ściągania i wypychania, przypisz rolę ACRPush.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Korzystanie z tożsamości w celu uzyskania dostępu do rejestru

SSH do maszyny wirtualnej platformy Docker skonfigurowanej przy użyciu tożsamości. Uruchom następujące poleceń interfejsu wiersza polecenia platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure zainstalowanego na maszynie wirtualnej.

Najpierw należy przeprowadzić uwierzytelnienie w interfejsie wiersza polecenia platformy Azure za pomocą polecenia [AZ login][az-login]przy użyciu tożsamości skonfigurowanej na maszynie wirtualnej. W przypadku programu `<userID>` należy zastąpić identyfikator tożsamości, która została pobrana w poprzednim kroku. 

```azurecli
az login --identity --username <userID>
```

Następnie Uwierzytelnij się w rejestrze za pomocą [AZ ACR login][az-acr-login]. Korzystając z tego polecenia, interfejs CLI używa tokenu Active Directory utworzonego podczas `az login` uruchamiania, aby bezproblemowo uwierzytelniać sesję przy użyciu rejestru kontenerów. (W zależności od konfiguracji maszyny wirtualnej może być konieczne uruchomienie tego polecenia i poleceń platformy Docker z programem `sudo` ).

```azurecli
az acr login --name myContainerRegistry
```

Powinien pojawić się `Login succeeded` komunikat. Następnie można uruchamiać `docker` polecenia bez podawania poświadczeń. Na przykład uruchom [Docker pull][docker-pull] , aby ściągnąć `aci-helloworld:v1` obraz, określając nazwę serwera logowania rejestru. Nazwa serwera logowania składa się z nazwy rejestru kontenerów (wszystkie małe litery) `.azurecr.io` , a następnie — na przykład `mycontainerregistry.azurecr.io` .

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="example-2-access-with-a-system-assigned-identity"></a>Przykład 2: dostęp do tożsamości przypisanej do systemu

### <a name="configure-the-vm-with-a-system-managed-identity"></a>Konfigurowanie maszyny wirtualnej przy użyciu tożsamości zarządzanej przez system

Następujące polecenie [AZ VM Identity Assign][az-vm-identity-assign] KONFIGURUJe maszynę wirtualną platformy Docker przy użyciu tożsamości przypisanej do systemu:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM 
```

Użyj polecenia [AZ VM show][az-vm-show] , aby ustawić zmienną na wartość `principalId` (identyfikator jednostki usługi) tożsamości maszyny wirtualnej, która będzie używana w dalszych krokach.

```azurecli-interactive
spID=$(az vm show --resource-group myResourceGroup --name myDockerVM --query identity.principalId --out tsv)
```

### <a name="grant-identity-access-to-the-container-registry"></a>Udzielanie tożsamości dostępu do rejestru kontenerów

Teraz Skonfiguruj tożsamość, aby uzyskać dostęp do rejestru kontenerów. Najpierw użyj polecenia [AZ ACR show][az-acr-show] , aby uzyskać identyfikator zasobu rejestru:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Użyj polecenia [AZ role Assign Create][az-role-assignment-create] , aby przypisać rolę AcrPull do tożsamości. Ta rola zapewnia [uprawnienia do ściągania](container-registry-roles.md) rejestru. Aby zapewnić uprawnienia ściągania i wypychania, przypisz rolę ACRPush.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Korzystanie z tożsamości w celu uzyskania dostępu do rejestru

SSH do maszyny wirtualnej platformy Docker skonfigurowanej przy użyciu tożsamości. Uruchom następujące poleceń interfejsu wiersza polecenia platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure zainstalowanego na maszynie wirtualnej.

Najpierw należy przeprowadzić uwierzytelnienie interfejsu wiersza polecenia platformy Azure za pomocą polecenia [AZ login][az-login], używając tożsamości przypisanej do systemu na maszynie wirtualnej.

```azurecli
az login --identity
```

Następnie Uwierzytelnij się w rejestrze za pomocą [AZ ACR login][az-acr-login]. Korzystając z tego polecenia, interfejs CLI używa tokenu Active Directory utworzonego podczas `az login` uruchamiania, aby bezproblemowo uwierzytelniać sesję przy użyciu rejestru kontenerów. (W zależności od konfiguracji maszyny wirtualnej może być konieczne uruchomienie tego polecenia i poleceń platformy Docker z programem `sudo` ).

```azurecli
az acr login --name myContainerRegistry
```

Powinien pojawić się `Login succeeded` komunikat. Następnie można uruchamiać `docker` polecenia bez podawania poświadczeń. Na przykład uruchom [Docker pull][docker-pull] , aby ściągnąć `aci-helloworld:v1` obraz, określając nazwę serwera logowania rejestru. Nazwa serwera logowania składa się z nazwy rejestru kontenerów (wszystkie małe litery) `.azurecr.io` , a następnie — na przykład `mycontainerregistry.azurecr.io` .

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```
> [!NOTE]
> Tożsamości usługi zarządzanej przypisane do systemu mogą być używane do współpracy z rekordami ACR, a App Service mogą korzystać z tożsamości usługi zarządzanej przypisanej do systemu. Nie można jednak łączyć tych, ponieważ App Service nie można użyć pliku MSI do komunikowania się z ACR. Jedynym sposobem jest włączenie administratora w ACR i użycie nazwy użytkownika/hasła administratora.

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono informacje dotyczące korzystania z tożsamości zarządzanych z Azure Container Registry i instrukcje:

> [!div class="checklist"]
> * Włączanie tożsamości przypisanej do użytkownika lub przypisanej do systemu na maszynie wirtualnej platformy Azure
> * Udzielanie tożsamości dostępu do usługi Azure Container Registry
> * Korzystanie z tożsamości zarządzanej w celu uzyskania dostępu do rejestru i ściągania obrazu kontenera

* Dowiedz się więcej o [zarządzanych tożsamościach dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/index.yml).


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-vm-show]: /cli/azure/vm#az-vm-show
[az-vm-identity-assign]: /cli/azure/vm/identity#az-vm-identity-assign
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
