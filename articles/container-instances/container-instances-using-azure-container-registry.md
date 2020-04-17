---
title: Wdrażanie obrazu kontenera z rejestru kontenerów platformy Azure
description: Dowiedz się, jak wdrożyć kontenery w wystąpieniach kontenerów platformy Azure, pobierając obrazy kontenerów z rejestru kontenerów platformy Azure.
services: container-instances
ms.topic: article
ms.date: 02/18/2020
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 212624b857d65297830995018603c2627f83369b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453527"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Wdrażanie w wystąpieniach kontenerów platformy Azure z rejestru kontenerów platformy Azure

[Usługa Azure Container Registry](../container-registry/container-registry-intro.md) to usługa rejestru kontenerów oparta na platformie Azure, używana do przechowywania prywatnych obrazów kontenerów platformy Docker. W tym artykule opisano sposób ściągania obrazów kontenerów przechowywanych w rejestrze kontenerów platformy Azure podczas wdrażania w wystąpieniach kontenera platformy Azure. Zalecanym sposobem konfigurowania dostępu do rejestru jest utworzenie jednostki usługi Azure Active Directory i hasła oraz przechowywanie poświadczeń logowania w magazynie kluczy platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

**Rejestru kontenerów platformy Azure:** Potrzebujesz rejestru kontenera platformy Azure — i co najmniej jeden obraz kontenera w rejestrze — aby wykonać kroki opisane w tym artykule. Jeśli potrzebujesz rejestru, zobacz [Tworzenie rejestru kontenerów przy użyciu interfejsu wiersza polecenia platformy Azure](../container-registry/container-registry-get-started-azure-cli.md).

**Interfejsu wiersza polecenia platformy Azure:** Przykłady wiersza polecenia w tym artykule używają [interfejsu wiersza polecenia platformy Azure](/cli/azure/) i są sformatowane dla powłoki Bash. Narzędzie [platformy Azure CLI](/cli/azure/install-azure-cli) można zainstalować lokalnie lub użyć usługi [Azure Cloud Shell][cloud-shell-bash].

## <a name="configure-registry-authentication"></a>Konfigurowanie uwierzytelniania rejestru

W scenariuszu produkcyjnym, w którym zapewniasz dostęp do usług i aplikacji bez głowy, zaleca się skonfigurowanie dostępu do rejestru przy użyciu [jednostki usługi.](../container-registry/container-registry-auth-service-principal.md) Podmiot usługi umożliwia zapewnienie [kontroli dostępu opartej na rolach](../container-registry/container-registry-roles.md) do obrazów kontenerów. Na przykład można skonfigurować jednostkę usługi z dostępem tylko do ściągania do rejestru.

Usługa Azure Container Registry udostępnia dodatkowe [opcje uwierzytelniania.](../container-registry/container-registry-authentication.md)

> [!NOTE]
> Nie można uwierzytelnić w rejestrze kontenerów platformy Azure, aby pobierać obrazy podczas wdrażania grupy kontenerów przy użyciu [tożsamości zarządzanej](container-instances-managed-identity.md) skonfigurowanej w tej samej grupie kontenerów.

W poniższej sekcji utworzysz magazyn kluczy platformy Azure i jednostkę usługi i przechowujesz poświadczenia jednostki usługi w magazynie. 

### <a name="create-key-vault"></a>Tworzenie magazynu kluczy

Jeśli nie masz jeszcze magazynu w usłudze [Azure Key Vault](../key-vault/general/overview.md), utwórz go przy użyciu interfejsu wiersza polecenia platformy Azure przy użyciu poniższych poleceń.

Zaktualizuj `RES_GROUP` zmienną o nazwę istniejącej grupy zasobów, `ACR_NAME` w której ma być utworzony magazyn kluczy, oraz o nazwie rejestru kontenerów. W przypadku zwięzłości polecenia w tym artykule zakładają, że wszystkie wystąpienia rejestru, magazynu kluczy i kontenera są tworzone w tej samej grupie zasobów.

 Określ nazwę nowego magazynu `AKV_NAME`kluczy w pliku . Nazwa magazynu musi być unikatowa na platformie Azure i musi mieć długość od 3 do 24 znaków alfanumerycznej, zaczynać się od litery, kończyć literą lub cyfrą i nie może zawierać kolejnych łączników.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>Tworzenie jednostki usługi i poświadczenia magazynu

Teraz utwórz jednostkę usługi i przechowuj jego poświadczenia w magazynie kluczy.

Następujące polecenie używa [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] do utworzenia jednostki usługi, a [az keyvault secret zestaw][az-keyvault-secret-set] do przechowywania **hasła** jednostki usługi w przechowalni.

```azurecli
# Create service principal, store its password in vault (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name http://$ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role acrpull \
                --query password \
                --output tsv)
```

Argument `--role` w poprzednim poleceniu konfiguruje jednostkę usługi z rolą *acrpull*, co spowoduje przyznanie dostępu tylko do ściągania do rejestru. Aby przyznać prawa dostępu do wypychania i ściągania, należy zmienić argument `--role` na wartość *acrpush*.

Następnie należy przechowywać *appId* jednostki usługi w przechowalni, która jest **nazwą użytkownika,** która jest przekazywane do rejestru kontenerów platformy Azure do uwierzytelniania.

```azurecli
# Store service principal ID in vault (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Utworzono magazyn kluczy platformy Azure, w którym zapisano dwa wpisy tajne:

* `$ACR_NAME-pull-usr`: identyfikator jednostki usługi do użycia jako **nazwa użytkownika** rejestru kontenerów.
* `$ACR_NAME-pull-pwd`: hasło jednostki usługi do użycia jako **hasło** rejestru kontenerów.

Teraz możesz odwoływać się do tych wpisów tajnych według nazwy, gdy Ty lub Twoje aplikacje i usługi ściągają obrazy z rejestru.

## <a name="deploy-container-with-azure-cli"></a>Wdrażanie kontenera przy użyciu interfejsu wiersza polecenia platformy Azure

Teraz, gdy poświadczenia jednostki usługi są przechowywane w nieuwzowych wpisach tajnych usługi Azure Key Vault, aplikacje i usługi mogą używać ich do uzyskiwania dostępu do rejestru prywatnego.

Najpierw pobierz nazwę serwera logowania rejestru za pomocą [polecenia az acr show.][az-acr-show] Nazwa serwera logowania jest mała i `myregistry.azurecr.io`podobna do .

```azurecli
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --resource-group $RES_GROUP --query "loginServer" --output tsv)
```

Wykonaj polecenie [az container create][az-container-create] w celu wdrożenia wystąpienia kontenera. Polecenie używa poświadczeń jednostki usługi przechowywanych w usłudze Azure Key Vault do uwierzytelniania w rejestrze kontenerów i zakłada, że wcześniej wypchnął obraz [aci-helloworld](container-instances-quickstart.md) do rejestru. Zaktualizuj `--image` wartość, jeśli chcesz użyć innego obrazu niż rejestr.

```azurecli
az container create \
    --name aci-demo \
    --resource-group $RES_GROUP \
    --image $ACR_LOGIN_SERVER/aci-helloworld:v1 \
    --registry-login-server $ACR_LOGIN_SERVER \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label aci-demo-$RANDOM \
    --query ipAddress.fqdn
```

Wartość `--dns-name-label` musi być unikatowa na platformie Azure, więc poprzednie polecenie dołącza losową liczbę do etykiety nazwy DNS kontenera. Dane wyjściowe polecenia wyświetlają w pełni kwalifikowaną nazwę domeny (FQDN) kontenera, na przykład:

```output
"aci-demo-25007.eastus.azurecontainer.io"
```

Po pomyślnym uruchomieniu kontenera można przejść do jego nazwy FQDN w przeglądarce, aby sprawdzić, czy aplikacja działa pomyślnie.

## <a name="deploy-with-azure-resource-manager-template"></a>Wdrażanie za pomocą szablonu usługi Azure Resource Manager

Można określić właściwości rejestru kontenerów platformy Azure w szablonie `imageRegistryCredentials` usługi Azure Resource Manager, dołączając właściwość w definicji grupy kontenerów. Na przykład można określić poświadczenia rejestru bezpośrednio:

```JSON
[...]
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
[...]
```

Aby zapoznać się z pełnymi ustawieniami grupy [kontenerów,](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups)zobacz odwołanie do szablonu Menedżera zasobów .    

Aby uzyskać szczegółowe informacje na temat odwoływania się do wpisów tajnych usługi Azure Key Vault w szablonie Usługi Resource Manager, zobacz [Przekazywanie wartości bezpiecznego parametru podczas wdrażania za pomocą usługi Azure Key Vault.](../azure-resource-manager/templates/key-vault-parameter.md)

## <a name="deploy-with-azure-portal"></a>Wdrażanie za pomocą witryny Azure portal

Jeśli przechowujesz obrazy kontenerów w rejestrze kontenerów platformy Azure, można łatwo utworzyć kontener w wystąpieniach kontenera platformy Azure przy użyciu witryny Azure portal. Podczas korzystania z portalu do wdrażania wystąpienia kontenera z rejestru kontenerów należy włączyć [konto administratora](../container-registry/container-registry-authentication.md#admin-account)rejestru . Konto administratora jest przeznaczone dla pojedynczego użytkownika, aby uzyskać dostęp do rejestru, głównie do celów testowych. 

1. W witrynie Azure portal przejdź do rejestru kontenerów.

1. Aby potwierdzić, że konto administratora jest włączone, wybierz pozycję **Klawisze programu Access**, a następnie w obszarze **Użytkownik administratora** wybierz pozycję **Włącz**.

1. Wybierz **repozytoria**, a następnie wybierz repozytorium, z którego chcesz wdrożyć, kliknij prawym przyciskiem myszy znacznik obrazu kontenera, który chcesz wdrożyć, a następnie wybierz pozycję **Uruchom wystąpienie**.

    !["Uruchom wystąpienie" w rejestrze kontenerów platformy Azure w witrynie Azure portal][acr-runinstance-contextmenu]

1. Wprowadź nazwę kontenera i nazwę grupy zasobów. Można również zmienić wartości domyślne, jeśli chcesz.

    ![Tworzenie menu dla wystąpień kontenerów platformy Azure][acr-create-deeplink]

1. Po zakończeniu wdrażania można przejść do grupy kontenerów z okienka powiadomień, aby znaleźć jego adres IP i inne właściwości.

    ![Widok szczegółów dla grupy kontenerów wystąpień kontenerów platformy Azure][aci-detailsview]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat uwierzytelniania rejestru kontenerów platformy Azure, zobacz [Uwierzytelnianie przy użyciu rejestru kontenerów platformy Azure](../container-registry/container-registry-authentication.md).

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - External -->
[cloud-shell-bash]: https://shell.azure.com/bash
[cloud-shell-try-it]: https://shell.azure.com/powershell

<!-- LINKS - Internal -->
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-create]: /cli/azure/container#az-container-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
