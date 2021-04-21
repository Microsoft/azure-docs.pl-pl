---
title: Wdrażanie obrazu kontenera z Azure Container Registry
description: Dowiedz się, jak wdrażać kontenery w Azure Container Instances, ściągając obrazy kontenerów z rejestru kontenerów platformy Azure.
services: container-instances
ms.topic: article
ms.date: 07/02/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 7aba107ac58538245c16f581afa2c493f546ca01
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786949"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Wdrażanie do usługi Azure Container Instances z usługi Azure Container Registry

[Azure Container Registry](../container-registry/container-registry-intro.md) to oparta na platformie Azure usługa zarządzanego rejestru kontenerów używana do przechowywania prywatnych obrazów kontenerów platformy Docker. W tym artykule opisano sposób ściągania obrazów kontenerów przechowywanych w rejestrze kontenerów platformy Azure podczas wdrażania w Azure Container Instances. Zalecanym sposobem skonfigurowania dostępu do rejestru jest utworzenie jednostki usługi Azure Active Directory i hasła oraz przechowywanie poświadczeń logowania w usłudze Azure Key Vault.

## <a name="prerequisites"></a>Wymagania wstępne

**Rejestr kontenerów platformy Azure:** aby wykonać kroki opisane w tym artykule, potrzebny jest rejestr kontenerów platformy Azure i co najmniej jeden obraz kontenera w rejestrze. Jeśli potrzebujesz rejestru, zobacz [Create a container registry using the Azure CLI (Tworzenie rejestru kontenerów przy użyciu interfejsu wiersza polecenia platformy Azure).](../container-registry/container-registry-get-started-azure-cli.md)

**Interfejs wiersza polecenia platformy Azure:** przykłady wiersza polecenia w tym artykule korzystają z interfejsu wiersza polecenia platformy [Azure](/cli/azure/) i są sformatowane dla powłoki Bash. Interfejs wiersza [polecenia platformy Azure można zainstalować](/cli/azure/install-azure-cli) lokalnie lub użyć [Azure Cloud Shell][cloud-shell-bash].

## <a name="limitations"></a>Ograniczenia

* Nie można uwierzytelniać się w Azure Container Registry do ściągania obrazów podczas wdrażania grupy kontenerów przy użyciu tożsamości zarządzanej skonfigurowanej w tej samej grupie kontenerów. [](container-instances-managed-identity.md)
* Obecnie nie można ściągać [obrazów z Azure Container Registry](../container-registry/container-registry-vnet.md) wdrożonych w Virtual Network Azure.

## <a name="configure-registry-authentication"></a>Konfigurowanie uwierzytelniania rejestru

W scenariuszu produkcyjnym, w którym zapewniasz dostęp do "bezgłowych" usług i aplikacji, zaleca się skonfigurowanie dostępu do rejestru przy użyciu [jednostki usługi](../container-registry/container-registry-auth-service-principal.md). Jednostkę usługi można zapewnić kontroli dostępu na podstawie ról [(RBAC)](../container-registry/container-registry-roles.md) platformy Azure dla obrazów kontenerów. Na przykład można skonfigurować jednostkę usługi z dostępem tylko do ściągania do rejestru.

Azure Container Registry dodatkowe opcje [uwierzytelniania.](../container-registry/container-registry-authentication.md)

W poniższej sekcji utworzysz magazyn kluczy platformy Azure i jednostkę usługi oraz przechowasz poświadczenia jednostki usługi w magazynie.

### <a name="create-key-vault"></a>Tworzenie magazynu kluczy

Jeśli nie masz jeszcze magazynu w usłudze [Azure Key Vault](../key-vault/general/overview.md), utwórz go przy użyciu interfejsu wiersza polecenia platformy Azure przy użyciu poniższych poleceń.

Zaktualizuj zmienną przy użyciu nazwy istniejącej grupy zasobów, w której chcesz utworzyć magazyn kluczy, oraz nazwy `RES_GROUP` `ACR_NAME` rejestru kontenerów. W celu zwięzłości polecenia w tym artykule zakładają, że rejestr, magazyn kluczy i wystąpienia kontenerów są tworzone w tej samej grupie zasobów.

 Określ nazwę nowego magazynu kluczy w pliku `AKV_NAME` . Nazwa magazynu musi być unikatowa w obrębie platformy Azure i musi mieć długość od 3 do 24 znaków alfanumerycznych, zaczynać się od litery, kończyć się literą lub cyfrą i nie może zawierać kolejnych łączników.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>Tworzenie jednostki usługi i poświadczenia magazynu

Teraz utwórz jednostkę usługi i przechowuj jej poświadczenia w magazynie kluczy.

Następujące polecenie używa [polecenia az ad sp create-for-rbac][az-ad-sp-create-for-rbac] do utworzenia jednostki usługi i polecenia  [az keyvault secret set][az-keyvault-secret-set] do przechowywania hasła jednostki usługi w magazynie.

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

Następnie przechowaj w magazynie nazwę główną usługi  *appId,* czyli nazwę użytkownika, która jest Azure Container Registry na Azure Container Registry uwierzytelniania.

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

Teraz, gdy poświadczenia jednostki usługi są przechowywane w Azure Key Vault tajnych, aplikacje i usługi mogą używać ich do uzyskiwania dostępu do rejestru prywatnego.

Najpierw pobierz nazwę serwera logowania rejestru za pomocą [polecenia az acr show.][az-acr-show] Nazwa serwera logowania jest mała i podobna do `myregistry.azurecr.io` .

```azurecli
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --resource-group $RES_GROUP --query "loginServer" --output tsv)
```

Wykonaj polecenie [az container create][az-container-create] w celu wdrożenia wystąpienia kontenera. Polecenie używa poświadczeń jednostki usługi przechowywanych w usłudze Azure Key Vault do uwierzytelniania w rejestrze kontenerów i zakłada, że wcześniej wypchnięliśmy obraz [aci-helloworld](container-instances-quickstart.md) do rejestru. Zaktualizuj wartość , jeśli chcesz użyć innego obrazu `--image` z rejestru.

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

Wartość musi być unikatowa w obrębie platformy Azure, dlatego poprzednie polecenie dołącza losową liczbę do `--dns-name-label` etykiety nazwy DNS kontenera. Dane wyjściowe polecenia wyświetlają w pełni kwalifikowaną nazwę domeny (FQDN) kontenera, na przykład:

```output
"aci-demo-25007.eastus.azurecontainer.io"
```

Po pomyślnym uruchomieniu kontenera możesz przejść do jego WQDN w przeglądarce, aby sprawdzić, czy aplikacja działa pomyślnie.

## <a name="deploy-with-azure-resource-manager-template"></a>Wdrażanie przy użyciu Azure Resource Manager szablonu

Właściwości rejestru kontenerów platformy Azure można określić w szablonie usługi Azure Resource Manager, uwzględniając `imageRegistryCredentials` właściwość w definicji grupy kontenerów. Na przykład możesz określić poświadczenia rejestru bezpośrednio:

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

Pełne ustawienia grupy kontenerów można znaleźć w [Resource Manager szablonu](/azure/templates/Microsoft.ContainerInstance/2019-12-01/containerGroups).    

Aby uzyskać szczegółowe informacje na temat odwoływania Azure Key Vault wpisów tajnych w szablonie usługi Resource Manager, zobacz Use Azure Key Vault to pass secure parameter value during deployment (Używanie Azure Key Vault do przekazania wartości bezpiecznego parametru [podczas wdrażania).](../azure-resource-manager/templates/key-vault-parameter.md)

## <a name="deploy-with-azure-portal"></a>Wdrażanie za pomocą Azure Portal

Jeśli utrzymujesz obrazy kontenerów w rejestrze kontenerów platformy Azure, możesz łatwo utworzyć kontener w usłudze Azure Container Instances użyciu Azure Portal. W przypadku wdrażania wystąpienia kontenera z rejestru kontenerów przy użyciu portalu należy włączyć konto [administratora rejestru](../container-registry/container-registry-authentication.md#admin-account). Konto administratora jest przeznaczone dla jednego użytkownika w celu uzyskania dostępu do rejestru, głównie do celów testowych. 

1. W Azure Portal przejdź do rejestru kontenerów.

1. Aby potwierdzić, że konto administratora jest włączone, wybierz pozycję **Klucze dostępu,** a następnie w obszarze **Administrator wybierz** pozycję **Włącz.**

1. Wybierz **pozycję Repozytoria,** wybierz repozytorium, z którego chcesz wdrożyć, kliknij prawym przyciskiem myszy tag obrazu kontenera, który chcesz wdrożyć, a następnie wybierz **polecenie Uruchom wystąpienie**.

    !["Uruchom wystąpienie" w Azure Container Registry w Azure Portal][acr-runinstance-contextmenu]

1. Wprowadź nazwę kontenera i nazwę grupy zasobów. Jeśli chcesz, możesz również zmienić wartości domyślne.

    ![Menu Tworzenie dla Azure Container Instances][acr-create-deeplink]

1. Po zakończeniu wdrażania możesz przejść do grupy kontenerów w okienku powiadomień, aby znaleźć jej adres IP i inne właściwości.

    ![Widok szczegółów dla Azure Container Instances kontenerów][aci-detailsview]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat uwierzytelniania Azure Container Registry, zobacz [Uwierzytelnianie za pomocą rejestru kontenerów platformy Azure.](../container-registry/container-registry-authentication.md)

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - External -->
[cloud-shell-bash]: https://shell.azure.com/bash
[cloud-shell-try-it]: https://shell.azure.com/powershell

<!-- LINKS - Internal -->
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-container-create]: /cli/azure/container#az_container_create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az_keyvault_secret_set
