---
title: Włącz zarządzaną tożsamość w grupie kontenerów
description: Dowiedz się, jak włączyć zarządzaną tożsamość w Azure Container Instances, która może być uwierzytelniana za pomocą innych usług platformy Azure
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: 23f2347593137a4846c8fd22e3b90f22db39bda3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86259619"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Jak używać tożsamości zarządzanych z usługą Azure Container Instances

Użyj [zarządzanych tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md) , aby uruchomić kod w Azure Container Instances, który współdziała z innymi usługami platformy Azure — bez utrzymywania żadnych wpisów tajnych lub poświadczeń w kodzie. Ta funkcja udostępnia Azure Container Instances wdrożenie z automatycznie zarządzaną tożsamością w programie Azure Active Directory.

W tym artykule dowiesz się więcej na temat tożsamości zarządzanych w Azure Container Instances i:

> [!div class="checklist"]
> * Włączanie przypisanej do użytkownika lub przypisanej do systemu tożsamości w grupie kontenerów
> * Udzielanie tożsamości dostępu do magazynu kluczy platformy Azure
> * Korzystanie z tożsamości zarządzanej w celu uzyskiwania dostępu do magazynu kluczy z działającego kontenera

Dostosuj przykłady, aby włączyć i użyć tożsamości w Azure Container Instances, aby uzyskać dostęp do innych usług platformy Azure. Te przykłady są interaktywne. Jednak w ramach tej metody obrazy kontenerów będą uruchamiały kod w celu uzyskania dostępu do usług platformy Azure.
 
> [!IMPORTANT]
> Ta funkcja jest obecnie w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna. Obecnie zarządzane tożsamości w Azure Container Instances są obsługiwane tylko w kontenerach systemu Linux i nie są jeszcze dostępne w kontenerach Windows.

## <a name="why-use-a-managed-identity"></a>Dlaczego warto używać tożsamości zarządzanej?

Użyj zarządzanej tożsamości w działającym kontenerze do uwierzytelniania w dowolnej [usłudze obsługującej uwierzytelnianie w usłudze Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) bez konieczności zarządzania poświadczeniami w kodzie kontenera. W przypadku usług, które nie obsługują uwierzytelniania usługi AD, można przechowywać wpisy tajne w magazynie kluczy platformy Azure i używać tożsamości zarządzanej do uzyskiwania dostępu do magazynu kluczy w celu pobrania poświadczeń. Aby uzyskać więcej informacji o korzystaniu z tożsamości zarządzanej, zobacz [co to jest tożsamość zarządzana dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md)

### <a name="enable-a-managed-identity"></a>Włącz zarządzaną tożsamość

 Podczas tworzenia grupy kontenerów należy włączyć co najmniej jedną zarządzaną tożsamość przez ustawienie właściwości [ContainerGroupIdentity](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity) . Można również włączyć lub zaktualizować zarządzane tożsamości po uruchomieniu grupy kontenerów — każda akcja powoduje ponowne uruchomienie grupy kontenerów. Aby ustawić tożsamości w nowej lub istniejącej grupie kontenerów, użyj interfejsu wiersza polecenia platformy Azure, szablonu Menedżer zasobów, pliku YAML lub innego narzędzia platformy Azure. 

Azure Container Instances obsługuje oba typy zarządzanych tożsamości platformy Azure: przypisane przez użytkownika i przypisane do systemu. W grupie kontenerów można włączyć tożsamość przypisaną do systemu, jedną lub więcej tożsamości przypisanych do użytkownika lub oba typy tożsamości. Jeśli nie znasz tożsamości zarządzanych dla zasobów platformy Azure, zapoznaj się z [omówieniem](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="use-a-managed-identity"></a>Korzystanie z tożsamości zarządzanej

Aby można było korzystać z tożsamości zarządzanej, tożsamość musi mieć udzielony dostęp do co najmniej jednego zasobu usługi platformy Azure (na przykład aplikacji sieci Web, magazynu kluczy lub konta magazynu) w ramach subskrypcji. Używanie tożsamości zarządzanej w działającym kontenerze jest podobne do użycia tożsamości na maszynie wirtualnej platformy Azure. Zobacz wskazówki dotyczące maszyn wirtualnych, aby korzystać z [tokenu](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md), [Azure PowerShell lub interfejsu wiersza polecenia platformy Azure](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md)lub [zestawów SDK platformy Azure](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md).

### <a name="limitations"></a>Ograniczenia

* Obecnie nie można używać tożsamości zarządzanej w grupie kontenerów wdrożonej w sieci wirtualnej.
* Nie można użyć tożsamości zarządzanej do ściągania obrazu z Azure Container Registry podczas tworzenia grupy kontenerów. Tożsamość jest dostępna tylko w działającym kontenerze.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten artykuł będzie wymagał interfejsu wiersza polecenia platformy Azure w wersji 2.0.49 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="create-an-azure-key-vault"></a>Tworzenie magazynu kluczy platformy Azure

W przykładach w tym artykule używana jest tożsamość zarządzana w Azure Container Instances, aby uzyskać dostęp do wpisu tajnego magazynu kluczy platformy Azure. 

Najpierw utwórz grupę zasobów o nazwie *myResourceGroup* w lokalizacji *eastus* za pomocą następującego polecenia [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create):

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Użyj polecenia [AZ Key magazynu Create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) , aby utworzyć magazyn kluczy. Pamiętaj, aby określić unikatową nazwę magazynu kluczy. 

```azurecli-interactive
az keyvault create \
  --name mykeyvault \
  --resource-group myResourceGroup \ 
  --location eastus
```

Zapisz przykładowy klucz tajny w magazynie kluczy za pomocą polecenia AZ Key Key [Secret Set](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set) :

```azurecli-interactive
az keyvault secret set \
  --name SampleSecret \
  --value "Hello Container Instances" \
  --description ACIsecret --vault-name mykeyvault
```

Aby uzyskać dostęp do magazynu kluczy przy użyciu tożsamości zarządzanej przypisanej przez użytkownika lub przypisanej do systemu w Azure Container Instances, przejdź do następujących przykładów.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>Przykład 1: użycie tożsamości przypisanej do użytkownika w celu uzyskania dostępu do magazynu kluczy Azure

### <a name="create-an-identity"></a>Tworzenie tożsamości

Najpierw utwórz tożsamość w subskrypcji za pomocą polecenia [AZ Identity Create](/cli/azure/identity?view=azure-cli-latest#az-identity-create) . Możesz użyć tej samej grupy zasobów, która została użyta do utworzenia magazynu kluczy lub użyć innego.

```azurecli-interactive
az identity create \
  --resource-group myResourceGroup \
  --name myACIId
```

Aby użyć tożsamości w poniższych krokach, użyj polecenia [AZ Identity show](/cli/azure/identity?view=azure-cli-latest#az-identity-show) do przechowywania identyfikatora jednostki usługi tożsamości i identyfikatora zasobu w zmiennych.

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

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Udzielanie tożsamości przypisanej użytkownikowi dostępu do magazynu kluczy

Uruchom następujące polecenie [AZ KeyBinding Set-Policy](/cli/azure/keyvault?view=azure-cli-latest) , aby ustawić zasady dostępu w magazynie kluczy. Poniższy przykład umożliwia tożsamości przypisanej przez użytkownika uzyskanie wpisów tajnych z magazynu kluczy:

```azurecli-interactive
 az keyvault set-policy \
    --name mykeyvault \
    --resource-group myResourceGroup \
    --object-id $spID \
    --secret-permissions get
```

### <a name="enable-user-assigned-identity-on-a-container-group"></a>Włączanie tożsamości przypisanej przez użytkownika w grupie kontenerów

Uruchom następujące polecenie [AZ Container Create](/cli/azure/container?view=azure-cli-latest#az-container-create) , aby utworzyć wystąpienie kontenera na podstawie obrazu firmy Microsoft `azure-cli` . Ten przykład zawiera grupę z pojedynczym kontenerem, która może być używana interaktywnie do uruchamiania interfejsu wiersza polecenia platformy Azure w celu uzyskania dostępu do innych usług platformy Azure. W tej sekcji używany jest tylko podstawowy system operacyjny. Przykład użycia interfejsu wiersza polecenia platformy Azure w kontenerze znajduje się w temacie [Włączanie tożsamości przypisanej do systemu w grupie kontenerów](#enable-system-assigned-identity-on-a-container-group). 

`--assign-identity`Parametr przekazuje do grupy zarządzaną tożsamość przypisaną przez użytkownika. Długotrwałe polecenie utrzymuje uruchomiony kontener. W tym przykładzie użyto tej samej grupy zasobów, która została użyta do utworzenia magazynu kluczy, ale można określić inny.

```azurecli-interactive
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity $resourceID \
  --command-line "tail -f /dev/null"
```

W ciągu kilku sekund powinna pojawić się odpowiedź z interfejsu wiersza polecenia platformy Azure, wskazująca ukończenie wdrażania. Sprawdź swój stan za pomocą polecenia [AZ Container show](/cli/azure/container?view=azure-cli-latest#az-container-show) .

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

`identity`Sekcja w danych wyjściowych wygląda podobnie do poniższego, pokazując tożsamość jest ustawiona w grupie kontenerów. `principalID`Poniżej `userAssignedIdentities` znajduje się nazwa główna usługi tożsamości utworzonej w Azure Active Directory:

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

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>Korzystanie z tożsamości przypisanej do użytkownika w celu pobrania klucza tajnego z magazynu kluczy

Teraz można użyć tożsamości zarządzanej w ramach uruchomionego wystąpienia kontenera w celu uzyskania dostępu do magazynu kluczy. Najpierw Uruchom powłokę bash w kontenerze:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Uruchom następujące polecenia w powłoce bash w kontenerze. Aby uzyskać token dostępu do uwierzytelniania w magazynie kluczy przy użyciu Azure Active Directory, uruchom następujące polecenie:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true -s
```

Dane wyjściowe:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Aby zapisać token dostępu w zmiennej, która ma być używana w kolejnych poleceniach do uwierzytelniania, uruchom następujące polecenie:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Teraz Użyj tokenu dostępu do uwierzytelniania w magazynie kluczy i odczytywania wpisu tajnego. Pamiętaj, aby zastąpić nazwę magazynu kluczy adresem URL (*https: \/ /mykeyvault.Vault.Azure.NET/...*):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

Odpowiedź wygląda podobnie do poniższego, pokazując wpis tajny. W kodzie można analizować te dane wyjściowe, aby uzyskać klucz tajny. Następnie użyj wpisu tajnego w kolejnej operacji, aby uzyskać dostęp do innego zasobu platformy Azure.

```bash
{"value":"Hello Container Instances","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>Przykład 2: użycie przypisanej do systemu tożsamości do uzyskiwania dostępu do magazynu kluczy Azure

### <a name="enable-system-assigned-identity-on-a-container-group"></a>Włączanie tożsamości przypisanej do systemu w grupie kontenerów

Uruchom następujące polecenie [AZ Container Create](/cli/azure/container?view=azure-cli-latest#az-container-create) , aby utworzyć wystąpienie kontenera na podstawie obrazu firmy Microsoft `azure-cli` . Ten przykład zawiera grupę z pojedynczym kontenerem, która może być używana interaktywnie do uruchamiania interfejsu wiersza polecenia platformy Azure w celu uzyskania dostępu do innych usług platformy Azure. 

`--assign-identity`Parametr bez dodatkowych wartości włącza do grupy tożsamość zarządzaną przez system. Tożsamość jest objęta zakresem grupy zasobów grupy kontenerów. Długotrwałe polecenie utrzymuje uruchomiony kontener. W tym przykładzie użyto tej samej grupy zasobów, która została użyta do utworzenia magazynu kluczy, który znajduje się w zakresie tożsamości.

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

W ciągu kilku sekund powinna pojawić się odpowiedź z interfejsu wiersza polecenia platformy Azure, wskazująca ukończenie wdrażania. Sprawdź swój stan za pomocą polecenia [AZ Container show](/cli/azure/container#az-container-show) .

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

`identity`Sekcja w danych wyjściowych wygląda podobnie do poniższego, pokazując, że tożsamość przypisana przez system jest tworzona w Azure Active Directory:

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

Ustaw zmienną na wartość `principalId` (identyfikator jednostki usługi) tożsamości, która będzie używana w dalszych krokach.

```azurecli-interactive
spID=$(az container show \
  --resource-group myResourceGroup \
  --name mycontainer \
  --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Przyznaj grupie kontenerów dostęp do magazynu kluczy

Uruchom następujące polecenie [AZ KeyBinding Set-Policy](/cli/azure/keyvault?view=azure-cli-latest) , aby ustawić zasady dostępu w magazynie kluczy. Poniższy przykład umożliwia tożsamości zarządzanej przez system otrzymywanie wpisów tajnych z magazynu kluczy:

```azurecli-interactive
 az keyvault set-policy \
   --name mykeyvault \
   --resource-group myResourceGroup \
   --object-id $spID \
   --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>Korzystanie z tożsamości grupy kontenerów w celu pobrania klucza tajnego z magazynu kluczy

Teraz można użyć tożsamości zarządzanej w celu uzyskania dostępu do magazynu kluczy w uruchomionym wystąpieniu kontenera. Najpierw Uruchom powłokę bash w kontenerze:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Uruchom następujące polecenia w powłoce bash w kontenerze. Najpierw Zaloguj się do interfejsu wiersza polecenia platformy Azure przy użyciu tożsamości zarządzanej:

```bash
az login --identity
```

Z działającego kontenera Pobierz klucz tajny z magazynu kluczy:

```bash
az keyvault secret show \
  --name SampleSecret \
  --vault-name mykeyvault --query value
```

Wartość wpisu tajnego jest pobierana:

```bash
"Hello Container Instances"
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Włączanie tożsamości zarządzanej przy użyciu szablonu Menedżer zasobów

Aby włączyć zarządzaną tożsamość w grupie kontenerów przy użyciu [szablonu Menedżer zasobów](container-instances-multi-container-group.md), ustaw `identity` Właściwość `Microsoft.ContainerInstance/containerGroups` obiektu na `ContainerGroupIdentity` obiekt. Poniższe fragmenty kodu pokazują `identity` Właściwość skonfigurowaną dla różnych scenariuszy. Zobacz [odwołanie do szablonu Menedżer zasobów](/azure/templates/microsoft.containerinstance/containergroups). Określ wartość minimalną `apiVersion` `2018-10-01` .

### <a name="user-assigned-identity"></a>Tożsamość przypisana przez użytkownika

Tożsamość przypisana przez użytkownika jest IDENTYFIKATORem zasobu w postaci:

```
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"
``` 

Można włączyć co najmniej jedną tożsamość przypisaną do użytkownika.

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

### <a name="system--and-user-assigned-identities"></a>Tożsamości przypisane do systemu i użytkownika

W grupie kontenerów można włączyć zarówno tożsamość przypisana do systemu, jak i jedną lub więcej tożsamości przypisanych do użytkownika.

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

## <a name="enable-managed-identity-using-yaml-file"></a>Włącz zarządzaną tożsamość przy użyciu pliku YAML

Aby włączyć zarządzaną tożsamość w grupie kontenerów wdrożonej przy użyciu [pliku YAML](container-instances-multi-container-yaml.md), należy uwzględnić następujące YAML.
Określ wartość minimalną `apiVersion` `2018-10-01` .

### <a name="user-assigned-identity"></a>Tożsamość przypisana przez użytkownika

Tożsamość przypisana przez użytkownika to identyfikator zasobu formularza 

```
'/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'
```

Można włączyć co najmniej jedną tożsamość przypisaną do użytkownika.

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

### <a name="system--and-user-assigned-identities"></a>Tożsamości przypisane do systemu i użytkownika

W grupie kontenerów można włączyć zarówno tożsamość przypisana do systemu, jak i jedną lub więcej tożsamości przypisanych do użytkownika.

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>Następne kroki

W tym artykule przedstawiono informacje o zarządzanych tożsamościach w Azure Container Instances i instrukcje:

> [!div class="checklist"]
> * Włączanie przypisanej do użytkownika lub przypisanej do systemu tożsamości w grupie kontenerów
> * Udzielanie tożsamości dostępu do magazynu kluczy platformy Azure
> * Korzystanie z tożsamości zarządzanej w celu uzyskiwania dostępu do magazynu kluczy z działającego kontenera

* Dowiedz się więcej o [zarządzanych tożsamościach dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/index.yml).

* Zobacz [przykład zestawu SDK platformy Azure](https://medium.com/@samkreter/c98911206328) , aby uzyskać dostęp do magazynu kluczy z Azure Container Instances przy użyciu tożsamości zarządzanej.
