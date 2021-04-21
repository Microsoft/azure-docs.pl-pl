---
title: Włączanie tożsamości zarządzanej w grupie kontenerów
description: Dowiedz się, jak włączyć tożsamość zarządzaną w usłudze Azure Container Instances, która może uwierzytelniać się w innych usługach platformy Azure
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: f8f3c646487d86f4e1bce13ccbf28992b8b1497a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764013"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Jak używać tożsamości zarządzanych z usługą Azure Container Instances

Użyj [tożsamości zarządzanych dla zasobów platformy Azure,](../active-directory/managed-identities-azure-resources/overview.md) aby uruchamiać kod w usłudze Azure Container Instances, która współdziała z innymi usługami platformy Azure — bez konieczności utrzymywania wpisów tajnych ani poświadczeń w kodzie. Ta funkcja zapewnia wdrożenie Azure Container Instances z automatycznie zarządzaną tożsamością w Azure Active Directory.

Z tego artykułu dowiesz się więcej na temat tożsamości zarządzanych w Azure Container Instances i:

> [!div class="checklist"]
> * Włączanie tożsamości przypisanej przez użytkownika lub przypisanej przez system w grupie kontenerów
> * Udzielanie tożsamości dostępu do usługi Azure Key Vault
> * Uzyskiwanie dostępu do magazynu kluczy z uruchomionego kontenera przy użyciu tożsamości zarządzanej

Dostosuj przykłady, aby umożliwić korzystanie z tożsamości w usłudze Azure Container Instances w celu uzyskiwania dostępu do innych usług platformy Azure. Te przykłady są interaktywne. Jednak w praktyce obrazy kontenerów uruchamiałyby kod w celu uzyskania dostępu do usług platformy Azure.
 
> [!IMPORTANT]
> Ta funkcja jest obecnie w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna. Obecnie tożsamości zarządzane na komputerze Azure Container Instances są obsługiwane tylko z kontenerami systemu Linux, a jeszcze nie z kontenerami systemu Windows.

## <a name="why-use-a-managed-identity"></a>Dlaczego warto używać tożsamości zarządzanej?

Użyj tożsamości zarządzanej w uruchomionym kontenerze, aby uwierzytelnić się w dowolnej usłudze obsługującej uwierzytelnianie usługi [Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) bez zarządzania poświadczeniami w kodzie kontenera. W przypadku usług, które nie obsługują uwierzytelniania usługi AD, wpisy tajne można przechowywać w magazynie kluczy platformy Azure i używać tożsamości zarządzanej do uzyskiwania dostępu do magazynu kluczy w celu pobrania poświadczeń. Aby uzyskać więcej informacji na temat korzystania z tożsamości zarządzanej, zobacz [Co to są tożsamości zarządzane dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md)

### <a name="enable-a-managed-identity"></a>Włączanie tożsamości zarządzanej

 Podczas tworzenia grupy kontenerów włącz co najmniej jedną tożsamość zarządzaną, ustawiając właściwość [ContainerGroupIdentity.](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity) Tożsamości zarządzane można również włączyć lub zaktualizować po uruchomieniu grupy kontenerów — każda z tych akcji powoduje ponowne uruchomienie grupy kontenerów. Aby ustawić tożsamości w nowej lub istniejącej grupie kontenerów, użyj interfejsu wiersza polecenia platformy Azure, Resource Manager szablonu, pliku YAML lub innego narzędzia platformy Azure. 

Azure Container Instances obsługuje oba typy zarządzanych tożsamości platformy Azure: przypisane przez użytkownika i przypisane przez system. W grupie kontenerów można włączyć tożsamość przypisaną przez system, co najmniej jedną tożsamość przypisaną przez użytkownika lub oba typy tożsamości. Jeśli nie masz informacji o tożsamościach zarządzanych dla zasobów platformy Azure, zobacz [omówienie](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="use-a-managed-identity"></a>Korzystanie z tożsamości zarządzanej

Aby użyć tożsamości zarządzanej, należy przyznać tożsamości dostęp do co najmniej jednego zasobów usługi platformy Azure (takich jak aplikacja internetowa, magazyn kluczy lub konto magazynu) w subskrypcji. Używanie tożsamości zarządzanej w uruchomionym kontenerze jest podobne do korzystania z tożsamości na maszynie wirtualnej platformy Azure. Zobacz wskazówki dotyczące używania tokenu , [interfejsu wiersza](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) [Azure PowerShell interfejsu](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md)wiersza polecenia platformy Azure lub zestawów [Azure SDK.](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md)

### <a name="limitations"></a>Ograniczenia

* Obecnie nie można użyć tożsamości zarządzanej w grupie kontenerów wdrożonej w sieci wirtualnej.
* Nie można użyć tożsamości zarządzanej do ściągnięcie obrazu z Azure Container Registry podczas tworzenia grupy kontenerów. Tożsamość jest dostępna tylko w uruchomionym kontenerze.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.49 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="create-an-azure-key-vault"></a>Tworzenie magazynu kluczy platformy Azure

W przykładach w tym artykule tożsamość zarządzana jest Azure Container Instances do uzyskiwania dostępu do klucza tajnego usługi Azure Key Vault. 

Najpierw utwórz grupę zasobów o nazwie *myResourceGroup* w lokalizacji *eastus* za pomocą następującego polecenia [az group create](/cli/azure/group#az_group_create):

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Użyj polecenia [az keyvault create,](/cli/azure/keyvault#az_keyvault_create) aby utworzyć magazyn kluczy. Pamiętaj, aby określić unikatową nazwę magazynu kluczy. 

```azurecli-interactive
az keyvault create \
  --name mykeyvault \
  --resource-group myResourceGroup \ 
  --location eastus
```

Przechowaj przykładowy klucz tajny w magazynie kluczy za pomocą [polecenia az keyvault secret set:](/cli/azure/keyvault/secret#az_keyvault_secret_set)

```azurecli-interactive
az keyvault secret set \
  --name SampleSecret \
  --value "Hello Container Instances" \
  --description ACIsecret --vault-name mykeyvault
```

Kontynuuj pracę z poniższymi przykładami, aby uzyskać dostęp do magazynu kluczy przy użyciu przypisanej przez użytkownika lub przypisanej przez system tożsamości zarządzanej w Azure Container Instances.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>Przykład 1: uzyskiwanie dostępu do usługi Azure Key Vault przy użyciu tożsamości przypisanej przez użytkownika

### <a name="create-an-identity"></a>Tworzenie tożsamości

Najpierw utwórz tożsamość w subskrypcji przy użyciu [polecenia az identity create.](/cli/azure/identity#az_identity_create) Możesz użyć tej samej grupy zasobów, która jest używana do utworzenia magazynu kluczy, lub innej.

```azurecli-interactive
az identity create \
  --resource-group myResourceGroup \
  --name myACIId
```

Aby użyć tożsamości w poniższych krokach, użyj [polecenia az identity show](/cli/azure/identity#az_identity_show) do przechowywania identyfikatora jednostki usługi i identyfikatora zasobu tożsamości w zmiennych.

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACIId \
  --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACIId \
  --query id --output tsv)
```

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Udzielanie tożsamości przypisanej przez użytkownika dostępu do magazynu kluczy

Uruchom następujące polecenie [az keyvault set-policy,](/cli/azure/keyvault) aby ustawić zasady dostępu w magazynie kluczy. Poniższy przykład umożliwia tożsamości przypisanej przez użytkownika uzyskiwanie wpisów tajnych z magazynu kluczy:

```azurecli-interactive
 az keyvault set-policy \
    --name mykeyvault \
    --resource-group myResourceGroup \
    --object-id $spID \
    --secret-permissions get
```

### <a name="enable-user-assigned-identity-on-a-container-group"></a>Włączanie tożsamości przypisanej przez użytkownika w grupie kontenerów

Uruchom następujące polecenie [az container create,](/cli/azure/container#az_container_create) aby utworzyć wystąpienie kontenera na podstawie obrazu `azure-cli` firmy Microsoft. Ten przykład zawiera grupę z jednym kontenerem, za pomocą których można interaktywnie uruchamiać interfejs wiersza polecenia platformy Azure w celu uzyskania dostępu do innych usług platformy Azure. W tej sekcji używany jest tylko podstawowy system operacyjny. Aby uzyskać przykład użycia interfejsu wiersza polecenia platformy Azure w kontenerze, zobacz Włączanie tożsamości przypisanej przez [system w grupie kontenerów.](#enable-system-assigned-identity-on-a-container-group) 

Parametr `--assign-identity` przekazuje tożsamość zarządzaną przypisaną przez użytkownika do grupy. Długotrwałe polecenie utrzymuje działanie kontenera. W tym przykładzie użyto tej samej grupy zasobów, która jest używana do utworzenia magazynu kluczy, ale można określić inną.

```azurecli-interactive
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity $resourceID \
  --command-line "tail -f /dev/null"
```

W ciągu kilku sekund powinna pojawić się odpowiedź z interfejsu wiersza polecenia platformy Azure, wskazująca ukończenie wdrażania. Sprawdź jego stan za pomocą [polecenia az container show.](/cli/azure/container#az_container_show)

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

Sekcja `identity` w danych wyjściowych wygląda podobnie do poniższej, pokazując, że tożsamość jest ustawiona w grupie kontenerów. W `principalID` obszarze `userAssignedIdentities` jest jednostką usługi tożsamości utworzonej w Azure Active Directory:

```console
[...]
"identity": {
    "principalId": "null",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-0903-4b79-a55a-xxxxxxxxxxxx/resourcegroups/danlep1018/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACIId": {
        "clientId": "xxxxxxxx-5523-45fc-9f49-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-f25b-4895-b828-xxxxxxxxxxxx"
      }
    }
  },
[...]
```

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>Uzyskiwanie klucza tajnego z magazynu kluczy przy użyciu tożsamości przypisanej przez użytkownika

Teraz możesz użyć tożsamości zarządzanej w uruchomionym wystąpieniu kontenera, aby uzyskać dostęp do magazynu kluczy. Najpierw uruchom powłokę Bash w kontenerze:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Uruchom następujące polecenia w powłoce bash w kontenerze. Aby uzyskać token dostępu w celu użycia Azure Active Directory do uwierzytelniania w magazynie kluczy, uruchom następujące polecenie:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true -s
```

Dane wyjściowe:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Aby zapisać token dostępu w zmiennej do użycia w kolejnych poleceniach do uwierzytelniania, uruchom następujące polecenie:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Teraz użyj tokenu dostępu, aby uwierzytelnić się w magazynie kluczy i odczytać klucz tajny. Pamiętaj, aby zastąpić nazwę magazynu kluczy w adresie URL *(https: \/ /mykeyvault.vault.azure.net/...*):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

Odpowiedź wygląda podobnie do poniższej, pokazując klucz tajny. W kodzie należy analizujeć te dane wyjściowe, aby uzyskać klucz tajny. Następnie użyj tego tajnego w kolejnej operacji, aby uzyskać dostęp do innego zasobu platformy Azure.

```bash
{"value":"Hello Container Instances","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>Przykład 2: używanie tożsamości przypisanej przez system do uzyskiwania dostępu do usługi Azure Key Vault

### <a name="enable-system-assigned-identity-on-a-container-group"></a>Włączanie tożsamości przypisanej przez system w grupie kontenerów

Uruchom następujące polecenie [az container create,](/cli/azure/container#az_container_create) aby utworzyć wystąpienie kontenera na podstawie obrazu firmy `azure-cli` Microsoft. Ten przykład zawiera grupę z jednym kontenerem, za pomocą których można interaktywnie uruchamiać interfejs wiersza polecenia platformy Azure w celu uzyskania dostępu do innych usług platformy Azure. 

Parametr bez dodatkowej wartości umożliwia przypisaną przez system tożsamość `--assign-identity` zarządzaną w grupie. Zakres tożsamości jest ograniczony do grupy zasobów grupy kontenerów. Długotrwałe polecenie utrzymuje działanie kontenera. W tym przykładzie użyto tej samej grupy zasobów, która jest używana do utworzenia magazynu kluczy, który znajduje się w zakresie tożsamości.

```azurecli-interactive
# Get the resource ID of the resource group
rgID=$(az group show --name myResourceGroup --query id --output tsv)

# Create container group with system-managed identity
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity --scope $rgID \
  --command-line "tail -f /dev/null"
```

W ciągu kilku sekund powinna pojawić się odpowiedź z interfejsu wiersza polecenia platformy Azure, wskazująca ukończenie wdrażania. Sprawdź jego stan za pomocą [polecenia az container show.](/cli/azure/container#az_container_show)

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

Sekcja w danych wyjściowych wygląda podobnie do poniższej, co pokazuje, że tożsamość przypisana przez system jest tworzona w `identity` Azure Active Directory:

```console
[...]
"identity": {
    "principalId": "xxxxxxxx-528d-7083-b74c-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
[...]
```

Ustaw zmienną na wartość `principalId` (identyfikator jednostki usługi) tożsamości do użycia w kolejnych krokach.

```azurecli-interactive
spID=$(az container show \
  --resource-group myResourceGroup \
  --name mycontainer \
  --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Udzielanie grupie kontenerów dostępu do magazynu kluczy

Uruchom następujące polecenie [az keyvault set-policy,](/cli/azure/keyvault) aby ustawić zasady dostępu w magazynie kluczy. Poniższy przykład umożliwia tożsamości zarządzanej przez system uzyskiwanie wpisów tajnych z magazynu kluczy:

```azurecli-interactive
 az keyvault set-policy \
   --name mykeyvault \
   --resource-group myResourceGroup \
   --object-id $spID \
   --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>Uzyskiwanie klucza tajnego z magazynu kluczy przy użyciu tożsamości grupy kontenerów

Teraz możesz użyć tożsamości zarządzanej, aby uzyskać dostęp do magazynu kluczy w uruchomionym wystąpieniu kontenera. Najpierw uruchom powłokę Bash w kontenerze:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Uruchom następujące polecenia w powłoce bash w kontenerze. Najpierw zaloguj się do interfejsu wiersza polecenia platformy Azure przy użyciu tożsamości zarządzanej:

```bash
az login --identity
```

Z uruchomionego kontenera pobierz klucz tajny z magazynu kluczy:

```bash
az keyvault secret show \
  --name SampleSecret \
  --vault-name mykeyvault --query value
```

Zostanie pobrana wartość tajnego:

```bash
"Hello Container Instances"
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Włączanie tożsamości zarządzanej przy użyciu Resource Manager szablonu

Aby włączyć tożsamość zarządzaną w grupie kontenerów przy użyciu Resource Manager [szablonu](container-instances-multi-container-group.md), ustaw właściwość `identity` obiektu za pomocą obiektu `Microsoft.ContainerInstance/containerGroups` `ContainerGroupIdentity` . Poniższe fragmenty kodu pokazują właściwość `identity` skonfigurowaną dla różnych scenariuszy. Zobacz informacje [Resource Manager szablonie.](/azure/templates/microsoft.containerinstance/containergroups) Określ wartość `apiVersion` minimalną `2018-10-01` .

### <a name="user-assigned-identity"></a>Tożsamość przypisana przez użytkownika

Tożsamość przypisana przez użytkownika jest identyfikatorem zasobu formularza:

```
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"
``` 

Możesz włączyć co najmniej jedną tożsamość przypisaną przez użytkownika.

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
```

### <a name="system-assigned-identity"></a>Tożsamość przypisana przez system

```json
"identity": {
    "type": "SystemAssigned"
    }
```

### <a name="system--and-user-assigned-identities"></a>Tożsamości przypisane przez system i użytkownika

W grupie kontenerów można włączyć zarówno tożsamość przypisaną przez system, jak i co najmniej jedną tożsamość przypisaną przez użytkownika.

```json
"identity": {
    "type": "System Assigned, UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
...
```

## <a name="enable-managed-identity-using-yaml-file"></a>Włączanie tożsamości zarządzanej przy użyciu pliku YAML

Aby włączyć tożsamość zarządzaną w grupie kontenerów wdrożonej przy użyciu pliku [YAML,](container-instances-multi-container-yaml.md)uwzględnij następujący kod YAML.
Określ wartość `apiVersion` minimalną `2018-10-01` .

### <a name="user-assigned-identity"></a>Tożsamość przypisana przez użytkownika

Tożsamość przypisana przez użytkownika jest identyfikatorem zasobu formularza 

```
'/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'
```

Możesz włączyć co najmniej jedną tożsamość przypisaną przez użytkownika.

```YAML
identity:
  type: UserAssigned
  userAssignedIdentities:
    {'myResourceID1':{}}
```

### <a name="system-assigned-identity"></a>Tożsamość przypisana przez system

```YAML
identity:
  type: SystemAssigned
```

### <a name="system--and-user-assigned-identities"></a>Tożsamości przypisane przez system i użytkownika

W grupie kontenerów można włączyć zarówno tożsamość przypisaną przez system, jak i co najmniej jedną tożsamość przypisaną przez użytkownika.

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>Następne kroki

W tym artykule opisano tożsamości zarządzane w Azure Container Instances oraz sposób:

> [!div class="checklist"]
> * Włączanie tożsamości przypisanej przez użytkownika lub przypisaną przez system w grupie kontenerów
> * Udzielanie dostępu tożsamości do usługi Azure Key Vault
> * Uzyskiwanie dostępu do magazynu kluczy z uruchomionego kontenera przy użyciu tożsamości zarządzanej

* Dowiedz się więcej o [tożsamościach zarządzanych dla zasobów platformy Azure.](../active-directory/managed-identities-azure-resources/index.yml)

* Zobacz przykład użycia [tożsamości zarządzanej w zestawie SDK języka Azure Go](https://medium.com/@samkreter/c98911206328) do uzyskiwania dostępu do magazynu kluczy z Azure Container Instances.
