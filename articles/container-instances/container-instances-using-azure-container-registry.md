---
title: Wdróż obraz kontenera z Azure Container Registry
description: Dowiedz się, jak wdrażać kontenery w Azure Container Instances przez ściąganie obrazów kontenerów z usługi Azure Container Registry.
services: container-instances
ms.topic: article
ms.date: 07/02/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: cca1001f0f84f4e4fc87df233f872fc1efdb3267
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92736733"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Wdrażanie do usługi Azure Container Instances z usługi Azure Container Registry

[Azure Container Registry](../container-registry/container-registry-intro.md) to oparta na platformie Azure, zarządzana usługa rejestru kontenerów służąca do przechowywania prywatnych obrazów kontenerów platformy Docker. W tym artykule opisano sposób ściągania obrazów kontenerów przechowywanych w usłudze Azure Container Registry podczas wdrażania programu w celu Azure Container Instances. Zalecaną metodą skonfigurowania dostępu do rejestru jest utworzenie nazwy głównej usługi Azure Active Directory i hasła oraz zapisanie poświadczeń logowania w magazynie kluczy platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

**Rejestr kontenerów platformy Azure**: potrzebny jest rejestr kontenerów platformy Azure — i co najmniej jeden obraz kontenera w rejestrze — aby wykonać kroki opisane w tym artykule. Jeśli potrzebujesz rejestru, zobacz [Tworzenie rejestru kontenerów za pomocą interfejsu wiersza polecenia platformy Azure](../container-registry/container-registry-get-started-azure-cli.md).

**Interfejs wiersza polecenia platformy Azure**: przykładowe przykłady w tym artykule służą do korzystania z [interfejsu CLI platformy Azure](/cli/azure/) i są sformatowane dla powłoki bash. [Interfejs wiersza polecenia platformy Azure można zainstalować](/cli/azure/install-azure-cli) lokalnie lub użyć [Azure Cloud Shell][cloud-shell-bash].

## <a name="limitations"></a>Ograniczenia

* Nie można uwierzytelnić się w celu Azure Container Registry ściągania obrazów podczas wdrażania grupy kontenerów przy użyciu [tożsamości zarządzanej](container-instances-managed-identity.md) skonfigurowanej w tej samej grupie kontenerów.
* W tej chwili nie można ściągnąć obrazów z [Azure Container Registry](../container-registry/container-registry-vnet.md) wdrożonych na platformie Azure Virtual Network.

## <a name="configure-registry-authentication"></a>Konfigurowanie uwierzytelniania rejestru

W scenariuszu produkcyjnym, w którym zapewniasz dostęp do "bezobsługowego" usług i aplikacji, zaleca się skonfigurowanie dostępu do rejestru przy użyciu [nazwy głównej usługi](../container-registry/container-registry-auth-service-principal.md). Jednostka usługi umożliwia udostępnianie obrazów kontenerów za pomocą [kontroli dostępu opartej na rolach (Azure RBAC)](../container-registry/container-registry-roles.md) . Na przykład można skonfigurować jednostkę usługi z dostępem tylko do ściągania do rejestru.

Azure Container Registry zapewnia dodatkowe [Opcje uwierzytelniania](../container-registry/container-registry-authentication.md).

W poniższej sekcji utworzysz Magazyn kluczy Azure i nazwę główną usługi, a następnie przechowujesz poświadczenia jednostki usługi w magazynie.

### <a name="create-key-vault"></a>Tworzenie magazynu kluczy

Jeśli nie masz jeszcze magazynu w usłudze [Azure Key Vault](../key-vault/general/overview.md), utwórz go przy użyciu interfejsu wiersza polecenia platformy Azure przy użyciu poniższych poleceń.

Zaktualizuj `RES_GROUP` zmienną o nazwę istniejącej grupy zasobów, w której ma zostać utworzony magazyn kluczy, oraz `ACR_NAME` nazwę rejestru kontenerów. W przypadku zwięzłości w poleceniach w tym artykule przyjęto założenie, że rejestr, Magazyn kluczy i wystąpienia kontenera zostały utworzone w tej samej grupie zasobów.

 Określ nazwę nowego magazynu kluczy w `AKV_NAME` . Nazwa magazynu musi być unikatowa w ramach platformy Azure i musi mieć 3-24 znaków alfanumerycznych, zaczynać się od litery, kończyć się literą lub cyfrą i nie może zawierać kolejnych łączników.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>Tworzenie jednostki usługi i poświadczenia magazynu

Teraz Utwórz nazwę główną usługi i Zapisz jej poświadczenia w magazynie kluczy.

Następujące polecenie używa polecenia [AZ AD Sp Create-for-RBAC][az-ad-sp-create-for-rbac] , aby utworzyć jednostkę usługi, i [AZ klucz tajny Set][az-keyvault-secret-set] do przechowywania **hasła** jednostki usługi w magazynie.

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

Następnie należy zapisać *identyfikator appid* jednostki usługi w magazynie, który jest **nazwą użytkownika** , która zostanie przekazana do Azure Container Registry na potrzeby uwierzytelniania.

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

Teraz, gdy poświadczenia jednostki usługi są przechowywane w kluczach tajnych Azure Key Vault, Twoje aplikacje i usługi mogą korzystać z nich w celu uzyskania dostępu do rejestru prywatnego.

Najpierw Pobierz nazwę serwera logowania rejestru przy użyciu polecenia [AZ ACR show][az-acr-show] . Nazwa serwera logowania jest mała i podobna do `myregistry.azurecr.io` .

```azurecli
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --resource-group $RES_GROUP --query "loginServer" --output tsv)
```

Wykonaj polecenie [az container create][az-container-create] w celu wdrożenia wystąpienia kontenera. Polecenie używa poświadczeń jednostki usługi przechowywanych w Azure Key Vault do uwierzytelniania w rejestrze kontenerów i zakłada, że wcześniej wypychał obraz [ACI-HelloWorld](container-instances-quickstart.md) do rejestru. Zaktualizuj `--image` wartość, jeśli chcesz użyć innego obrazu z rejestru.

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

`--dns-name-label`Wartość musi być unikatowa w ramach platformy Azure, dlatego poprzednie polecenie dołącza liczbę losową do etykiety nazwy DNS kontenera. Dane wyjściowe polecenia wyświetlają w pełni kwalifikowaną nazwę domeny (FQDN) kontenera, na przykład:

```output
"aci-demo-25007.eastus.azurecontainer.io"
```

Po pomyślnym rozpoczęciu pracy kontenera możesz przejść do jego nazwy FQDN w przeglądarce, aby sprawdzić, czy aplikacja działa prawidłowo.

## <a name="deploy-with-azure-resource-manager-template"></a>Wdrażanie przy użyciu szablonu Azure Resource Manager

Właściwości usługi Azure Container Registry można określić w szablonie Azure Resource Manager, dołączając `imageRegistryCredentials` Właściwość w definicji grupy kontenerów. Na przykład można określić bezpośrednio poświadczenia rejestru:

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

Aby uzyskać pełne ustawienia grupy kontenerów, zobacz [odwołanie do szablonu Menedżer zasobów](/azure/templates/Microsoft.ContainerInstance/2019-12-01/containerGroups).    

Aby uzyskać szczegółowe informacje dotyczące odwoływania się do Azure Key Vault wpisów tajnych w szablonie Menedżer zasobów, zobacz [używanie Azure Key Vault do przekazywania bezpiecznej wartości parametru podczas wdrażania](../azure-resource-manager/templates/key-vault-parameter.md).

## <a name="deploy-with-azure-portal"></a>Wdrażanie za pomocą Azure Portal

Jeśli przechowujesz obrazy kontenerów w usłudze Azure Container Registry, możesz łatwo utworzyć kontener w Azure Container Instances przy użyciu Azure Portal. W przypadku wdrażania wystąpienia kontenera z rejestru kontenerów przy użyciu portalu należy włączyć [konto administratora](../container-registry/container-registry-authentication.md#admin-account)rejestru. Konto administratora jest przeznaczone dla jednego użytkownika, aby uzyskać dostęp do rejestru, głównie do celów testowych. 

1. W Azure Portal przejdź do rejestru kontenerów.

1. Aby upewnić się, że konto administratora jest włączone, wybierz pozycję **klucze dostępu**, a w obszarze **administrator** wybierz pozycję **Włącz**.

1. Wybierz pozycję **repozytoria**, a następnie wybierz repozytorium, z którego chcesz wykonać wdrożenie, kliknij prawym przyciskiem myszy tag obrazu kontenera, który chcesz wdrożyć, a następnie wybierz polecenie **Uruchom wystąpienie**.

    !["Uruchom wystąpienie" w Azure Container Registry w Azure Portal][acr-runinstance-contextmenu]

1. Wprowadź nazwę dla kontenera i nazwę grupy zasobów. Możesz również zmienić wartości domyślne, jeśli chcesz.

    ![Utwórz menu dla Azure Container Instances][acr-create-deeplink]

1. Po zakończeniu wdrażania możesz przejść do grupy kontenerów z okienka powiadomienia, aby znaleźć jego adres IP i inne właściwości.

    ![Widok szczegółów dla Azure Container Instances grupy kontenerów][aci-detailsview]

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat uwierzytelniania Azure Container Registry, zobacz [uwierzytelnianie przy użyciu usługi Azure Container Registry](../container-registry/container-registry-authentication.md).

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
