---
title: Uwierzytelnianie przy użyciu tożsamości zarządzanej
description: Zapewnianie dostępu do obrazów w prywatnym rejestrze kontenerów przy użyciu przypisanej przez użytkownika lub przypisanej przez system tożsamości zarządzanej platformy Azure.
ms.topic: article
ms.date: 01/16/2019
ms.openlocfilehash: 213f49356fdc2444f8bc2cb4635e96015aff0a61
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781545"
---
# <a name="use-an-azure-managed-identity-to-authenticate-to-an-azure-container-registry"></a>Używanie tożsamości zarządzanej platformy Azure do uwierzytelniania w rejestrze kontenerów platformy Azure 

Użyj tożsamości [zarządzanej dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) do uwierzytelniania w rejestrze kontenerów platformy Azure z innego zasobu platformy Azure bez konieczności poświadczeń rejestru ani zarządzania nimi. Na przykład skonfiguruj tożsamość zarządzaną przypisaną przez użytkownika lub przypisaną przez system na maszynie wirtualnej z systemem Linux, aby uzyskać dostęp do obrazów kontenerów z rejestru kontenerów tak łatwo, jak w przypadku korzystania z rejestru publicznego. Możesz też skonfigurować klaster Azure Kubernetes Service do [](../aks/use-managed-identity.md) używania jego tożsamości zarządzanej do ściągania obrazów kontenerów z Azure Container Registry wdrożeń zasobników.

Z tego artykułu dowiesz się więcej na temat tożsamości zarządzanych i sposobu:

> [!div class="checklist"]
> * Włączanie tożsamości przypisanej przez użytkownika lub przypisanej przez system na maszynie wirtualnej platformy Azure
> * Udzielanie tożsamości dostępu do rejestru kontenerów platformy Azure
> * Uzyskiwanie dostępu do rejestru i ściąganie obrazu kontenera przy użyciu tożsamości zarządzanej 

Aby utworzyć zasoby platformy Azure, ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.55 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

Aby skonfigurować rejestr kontenerów i wypchnąć do niego obraz kontenera, musisz mieć również lokalnie zainstalowaną platformę Docker. Środowisko Docker zawiera pakiety, które umożliwiają łatwe konfigurowanie platformy Docker w systemie [macOS][docker-mac], [Windows][docker-windows] lub [Linux][docker-linux].

## <a name="why-use-a-managed-identity"></a>Dlaczego warto używać tożsamości zarządzanej?

Jeśli nie znasz funkcji tożsamości zarządzanych dla zasobów platformy Azure, zobacz to [omówienie](../active-directory/managed-identities-azure-resources/overview.md).

Po skonfigurowaniu wybranych zasobów platformy Azure przy użyciu tożsamości zarządzanej nadaj tożsamości dostęp do innego zasobu, podobnie jak w przypadku każdego podmiotu zabezpieczeń. Na przykład przypisz tożsamości zarządzanej rolę z uprawnieniami ściągania, wypychania i ściągania lub innymi uprawnieniami do rejestru prywatnego na platformie Azure. (Aby uzyskać pełną listę ról rejestru, zobacz [Azure Container Registry i uprawnienia).](container-registry-roles.md) Możesz udzielić tożsamości dostępu do jednego lub większej liczby zasobów.

Następnie użyj tożsamości, aby uwierzytelnić się w dowolnej usłudze obsługującej uwierzytelnianie usługi [Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)bez żadnych poświadczeń w kodzie. Wybierz sposób uwierzytelniania przy użyciu tożsamości zarządzanej, w zależności od scenariusza. Aby użyć tożsamości w celu uzyskania dostępu do rejestru kontenerów platformy Azure z maszyny wirtualnej, należy uwierzytelnić się przy użyciu Azure Resource Manager. 

> [!NOTE]
> Obecnie usługi takie jak Azure Web App for Containers lub Azure Container Instances nie mogą używać tożsamości zarządzanej do uwierzytelniania w usłudze Azure Container Registry podczas ściągania obrazu kontenera w celu wdrożenia samego zasobu kontenera. Tożsamość jest dostępna tylko po uruchomieniu kontenera. Aby wdrożyć te zasoby przy użyciu obrazów Azure Container Registry, zaleca się użycie innej metody uwierzytelniania, takiej jak [jednostki](container-registry-auth-service-principal.md) usługi.

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów

Jeśli nie masz jeszcze rejestru kontenerów platformy Azure, utwórz rejestr i wypchń do niego przykładowy obraz kontenera. Aby uzyskać instrukcje, zobacz [Szybki start: tworzenie prywatnego rejestru kontenerów przy użyciu interfejsu wiersza polecenia platformy Azure.](container-registry-get-started-azure-cli.md)

W tym artykule przyjęto założenie, że `aci-helloworld:v1` obraz kontenera jest przechowywany w rejestrze. W przykładach jest używana nazwa rejestru *myContainerRegistry*. W kolejnych krokach zastąp własnymi nazwami rejestru i obrazów.

## <a name="create-a-docker-enabled-vm"></a>Tworzenie maszyny wirtualnej z obsługą platformy Docker

Utwórz maszynę wirtualną z systemem Ubuntu z obsługą platformy Docker. Musisz również zainstalować interfejs wiersza [polecenia platformy Azure](/cli/azure/install-azure-cli) na maszynie wirtualnej. Jeśli masz już maszynę wirtualną platformy Azure, pomiń ten krok, aby utworzyć maszynę wirtualną.

Wd wdrażaj domyślną maszynę wirtualną z systemem Ubuntu Azure za [pomocą narzędzia az vm create][az-vm-create]. Poniższy przykład umożliwia utworzenie maszyny wirtualnej *o nazwie myDockerVM* w istniejącej grupie zasobów *o nazwie myResourceGroup:*

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Utworzenie maszyny wirtualnej może potrwać kilka minut. Po zakończeniu działania polecenia zanotuj dane `publicIpAddress` wyświetlane przez interfejs wiersza polecenia platformy Azure. Ten adres pozwala na połączenia SSH z maszyną wirtualną.

### <a name="install-docker-on-the-vm"></a>Instalowanie platformy Docker na maszynie wirtualnej

Po uruchomieniu maszyny wirtualnej nawiązaniu z maszyną wirtualną połączenia SSH. Zastąp *publicIpAddress* publicznym adresem IP maszyny wirtualnej.

```bash
ssh azureuser@publicIpAddress
```

Uruchom następujące polecenie, aby zainstalować aplikację Docker na maszynie wirtualnej:

```bash
sudo apt update
sudo apt install docker.io -y
```

Po zakończeniu instalacji uruchom następujące polecenie, aby sprawdzić, czy na maszynie wirtualnej działa prawidłowo system Docker:

```bash
sudo docker run -it mcr.microsoft.com/hello-world
```

Dane wyjściowe:

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
[...]
```

### <a name="install-the-azure-cli"></a>Instalowanie interfejsu wiersza polecenia platformy Azure

Wykonaj kroki opisane w temacie [Instalowanie interfejsu wiersza polecenia platformy Azure za pomocą narzędzia apt,](/cli/azure/install-azure-cli-apt) aby zainstalować interfejs wiersza polecenia platformy Azure na maszynie wirtualnej z systemem Ubuntu. W tym artykule upewnij się, że instalujesz wersję 2.0.55 lub nowszą.

Zamknij sesję SSH.

## <a name="example-1-access-with-a-user-assigned-identity"></a>Przykład 1: dostęp przy użyciu tożsamości przypisanej przez użytkownika

### <a name="create-an-identity"></a>Tworzenie tożsamości

Utwórz tożsamość w subskrypcji za pomocą [polecenia az identity create.](/cli/azure/identity#az_identity_create) Możesz użyć tej samej grupy zasobów, która została wcześniej użyta do utworzenia rejestru kontenerów, maszyny wirtualnej lub innej.

```azurecli-interactive
az identity create --resource-group myResourceGroup --name myACRId
```

Aby skonfigurować tożsamość w poniższych krokach, użyj polecenia [az identity show][az_identity_show] do przechowywania identyfikatora zasobu tożsamości i identyfikatora jednostki usługi w zmiennych.

```azurecli
# Get resource ID of the user-assigned identity
userID=$(az identity show --resource-group myResourceGroup --name myACRId --query id --output tsv)

# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACRId --query principalId --output tsv)
```

Ponieważ identyfikator tożsamości będzie potrzebny w późniejszym kroku podczas logowania się do interfejsu wiersza polecenia z maszyny wirtualnej, wyświetl wartość:

```bash
echo $userID
```

Identyfikator ma postać:

```
/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACRId
```

### <a name="configure-the-vm-with-the-identity"></a>Konfigurowanie maszyny wirtualnej przy użyciu tożsamości

Następujące polecenie [az vm identity assign][az-vm-identity-assign] konfiguruje maszynę wirtualną platformy Docker przy użyciu tożsamości przypisanej przez użytkownika:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM --identities $userID
```

### <a name="grant-identity-access-to-the-container-registry"></a>Udzielanie dostępu tożsamości do rejestru kontenerów

Teraz skonfiguruj tożsamość, aby uzyskać dostęp do rejestru kontenerów. Najpierw użyj [polecenia az acr show,][az-acr-show] aby uzyskać identyfikator zasobu rejestru:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Użyj polecenia [az role assignment create,][az-role-assignment-create] aby przypisać rolę AcrPull do rejestru. Ta rola zapewnia [uprawnienia do ściągania](container-registry-roles.md) do rejestru. Aby zapewnić uprawnienia do ściągania i wypychania, przypisz rolę ACRPush.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Uzyskiwanie dostępu do rejestru przy użyciu tożsamości

Na maszynie wirtualnej platformy Docker skonfigurowanej przy użyciu tożsamości nasyć SSH. Uruchom następujące polecenia interfejsu wiersza polecenia platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure zainstalowanego na maszynie wirtualnej.

Najpierw uwierzytelnij się w interfejsie wiersza polecenia platformy Azure za pomocą [polecenia az login][az-login]przy użyciu tożsamości skonfigurowanej na maszynie wirtualnej. Zastąp identyfikator tożsamości pobranej w `<userID>` poprzednim kroku. 

```azurecli
az login --identity --username <userID>
```

Następnie uwierzytelnij się w rejestrze za pomocą [az acr login][az-acr-login]. W przypadku użycia tego polecenia interfejs wiersza polecenia używa tokenu usługi Active Directory utworzonego po jego użyciu w celu bezproblemowego uwierzytelnienia sesji `az login` w rejestrze kontenerów. (W zależności od konfiguracji maszyny wirtualnej może być konieczne uruchomienie tego polecenia i poleceń platformy Docker za pomocą polecenia `sudo` ).

```azurecli
az acr login --name myContainerRegistry
```

Powinien zostać wyświetlony `Login succeeded` komunikat. Następnie można uruchamiać `docker` polecenia bez poświadczeń. Na przykład uruchom [docker pull,][docker-pull] aby ściągnąć obraz, określając nazwę serwera `aci-helloworld:v1` logowania rejestru. Nazwa serwera logowania składa się z nazwy rejestru kontenerów (wszystkie małe litery), po której następuje `.azurecr.io` na przykład `mycontainerregistry.azurecr.io` .

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="example-2-access-with-a-system-assigned-identity"></a>Przykład 2: dostęp przy użyciu tożsamości przypisanej przez system

### <a name="configure-the-vm-with-a-system-managed-identity"></a>Konfigurowanie maszyny wirtualnej przy użyciu tożsamości zarządzanej przez system

Następujące polecenie [az vm identity assign][az-vm-identity-assign] konfiguruje maszynę wirtualną platformy Docker przy użyciu tożsamości przypisanej przez system:

```azurecli
az vm identity assign --resource-group myResourceGroup --name myDockerVM 
```

Użyj polecenia [az vm show,][az-vm-show] aby ustawić zmienną na wartość (identyfikator jednostki usługi) tożsamości maszyny wirtualnej do użycia `principalId` w kolejnych krokach.

```azurecli-interactive
spID=$(az vm show --resource-group myResourceGroup --name myDockerVM --query identity.principalId --out tsv)
```

### <a name="grant-identity-access-to-the-container-registry"></a>Udzielanie dostępu tożsamości do rejestru kontenerów

Teraz skonfiguruj tożsamość, aby uzyskać dostęp do rejestru kontenerów. Najpierw użyj [polecenia az acr show,][az-acr-show] aby uzyskać identyfikator zasobu rejestru:

```azurecli
resourceID=$(az acr show --resource-group myResourceGroup --name myContainerRegistry --query id --output tsv)
```

Użyj polecenia [az role assignment create,][az-role-assignment-create] aby przypisać rolę AcrPull do tożsamości. Ta rola zapewnia [uprawnienia do ściągania](container-registry-roles.md) do rejestru. Aby zapewnić uprawnienia do ściągania i wypychania, przypisz rolę ACRPush.

```azurecli
az role assignment create --assignee $spID --scope $resourceID --role acrpull
```

### <a name="use-the-identity-to-access-the-registry"></a>Uzyskiwanie dostępu do rejestru przy użyciu tożsamości

Na maszynie wirtualnej platformy Docker skonfigurowanej przy użyciu tożsamości nasyć SSH. Uruchom następujące polecenia interfejsu wiersza polecenia platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure zainstalowanego na maszynie wirtualnej.

Najpierw uwierzytelnij interfejs wiersza polecenia platformy Azure za pomocą [polecenia az login][az-login]przy użyciu tożsamości przypisanej przez system na maszynie wirtualnej.

```azurecli
az login --identity
```

Następnie uwierzytelnij się w rejestrze za pomocą [az acr login][az-acr-login]. W przypadku użycia tego polecenia interfejs wiersza polecenia używa tokenu usługi Active Directory utworzonego po jego użyciu w celu bezproblemowego uwierzytelnienia sesji `az login` w rejestrze kontenerów. (W zależności od konfiguracji maszyny wirtualnej może być konieczne uruchomienie tego polecenia i poleceń platformy Docker za pomocą polecenia `sudo` ).

```azurecli
az acr login --name myContainerRegistry
```

Powinien zostać wyświetlony `Login succeeded` komunikat. Następnie można uruchamiać `docker` polecenia bez poświadczeń. Na przykład uruchom [docker pull,][docker-pull] aby ściągnąć obraz, określając nazwę serwera `aci-helloworld:v1` logowania rejestru. Nazwa serwera logowania składa się z nazwy rejestru kontenerów (wszystkie małe litery), po której następuje `.azurecr.io` na przykład `mycontainerregistry.azurecr.io` .

```
docker pull mycontainerregistry.azurecr.io/aci-helloworld:v1
```

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano używanie tożsamości zarządzanych z Azure Container Registry oraz sposób:

> [!div class="checklist"]
> * Włączanie tożsamości przypisanej przez użytkownika lub przypisanej przez system na maszynie wirtualnej platformy Azure
> * Udzielanie tożsamości dostępu do rejestru kontenerów platformy Azure
> * Uzyskiwanie dostępu do rejestru i ściąganie obrazu kontenera przy użyciu tożsamości zarządzanej

* Dowiedz się więcej o [tożsamościach zarządzanych dla zasobów platformy Azure.](../active-directory/managed-identities-azure-resources/index.yml)


<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az_login
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-vm-create]: /cli/azure/vm#az_vm_create
[az-vm-show]: /cli/azure/vm#az_vm_show
[az-vm-identity-assign]: /cli/azure/vm/identity#az_vm_identity_assign
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-identity-show]: /cli/azure/identity#az_identity_show
[azure-cli]: /cli/azure/install-azure-cli
