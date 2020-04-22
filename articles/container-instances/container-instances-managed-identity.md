---
title: Włączanie tożsamości zarządzanej w grupie kontenerów
description: Dowiedz się, jak włączyć tożsamość zarządzaną w wystąpieniach kontenera platformy Azure, które można uwierzytelnić za pomocą innych usług platformy Azure
ms.topic: article
ms.date: 01/29/2020
ms.openlocfilehash: 19d2ab22eea15278c7753046f9222c7856fbf5ef
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685641"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Jak używać tożsamości zarządzanych za pomocą wystąpień kontenerów platformy Azure

Użyj [tożsamości zarządzanych dla zasobów platformy Azure,](../active-directory/managed-identities-azure-resources/overview.md) aby uruchomić kod w wystąpieniach kontenera platformy Azure, które współdziałają z innymi usługami platformy Azure — bez zachowywania żadnych wpisów tajnych lub poświadczeń w kodzie. Ta funkcja zapewnia wdrożenie wystąpień kontenerów platformy Azure z automatycznie zarządzaną tożsamością w usłudze Azure Active Directory.

W tym artykule dowiesz się więcej o tożsamościach zarządzanych w wystąpieniach kontenerów platformy Azure i:

> [!div class="checklist"]
> * Włączanie tożsamości przypisanej przez użytkownika lub przypisanej do systemu w grupie kontenerów
> * Udzielanie dostępu do tożsamości do magazynu kluczy platformy Azure
> * Użyj tożsamości zarządzanej, aby uzyskać dostęp do magazynu kluczy z uruchomionego kontenera

Dostosuj przykłady, aby włączyć i używać tożsamości w wystąpieniach kontenera platformy Azure, aby uzyskać dostęp do innych usług platformy Azure. Te przykłady są interaktywne. Jednak w praktyce obrazy kontenera będzie uruchamiać kod, aby uzyskać dostęp do usług platformy Azure.

> [!NOTE]
> Obecnie nie można użyć tożsamości zarządzanej w grupie kontenerów wdrożonej w sieci wirtualnej.

## <a name="why-use-a-managed-identity"></a>Dlaczego warto używać tożsamości zarządzanej?

Użyj tożsamości zarządzanej w uruchomionym kontenerze, aby uwierzytelnić się w dowolnej [usłudze obsługującej uwierzytelnianie usługi Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) bez zarządzania poświadczeniami w kodzie kontenera. W przypadku usług, które nie obsługują uwierzytelniania usługi AD, można przechowywać wpisy tajne w magazynie kluczy platformy Azure i używać tożsamości zarządzanej, aby uzyskać dostęp do magazynu kluczy w celu pobrania poświadczeń. Aby uzyskać więcej informacji na temat korzystania z tożsamości zarządzanej, zobacz [Co to są tożsamości zarządzane dla zasobów platformy Azure?](../active-directory/managed-identities-azure-resources/overview.md)

> [!IMPORTANT]
> Ta funkcja jest obecnie dostępna w wersji zapoznawczej. Wersje zapoznawcze są udostępniane pod warunkiem udzielenia zgody na [dodatkowe warunki użytkowania](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Niektóre cechy funkcji mogą ulec zmianie, zanim stanie się ona ogólnie dostępna. Obecnie tożsamości zarządzane w wystąpieniach kontenerów platformy Azure są obsługiwane tylko w kontenerach systemu Linux, a nie jeszcze z kontenerami systemu Windows.
>  

### <a name="enable-a-managed-identity"></a>Włączanie tożsamości zarządzanej

 W przypadku wystąpień kontenerów platformy Azure tożsamości zarządzane dla zasobów platformy Azure są obsługiwane od interfejsu REST API w wersji 2018-10-01 i odpowiednich zestawów SDK i narzędzi. Podczas tworzenia grupy kontenerów należy włączyć jedną lub więcej tożsamości zarządzanych, ustawiając właściwość [ContainerGroupIdentity.](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity) Można również włączyć lub zaktualizować tożsamości zarządzane po uruchomieniu grupy kontenerów — każda akcja powoduje ponowne uruchomienie grupy kontenerów. Aby ustawić tożsamości w nowej lub istniejącej grupie kontenerów, użyj interfejsu wiersza polecenia platformy Azure, szablonu Menedżera zasobów lub pliku YAML. 

Wystąpienia kontenera platformy Azure obsługuje oba typy zarządzanych tożsamości platformy Azure: przypisane przez użytkownika i przypisane do systemu. W grupie kontenerów można włączyć tożsamość przypisaną do systemu, jedną lub więcej tożsamości przypisanych przez użytkownika lub oba typy tożsamości. 

* Tożsamość **zarządzana przypisana przez użytkownika** jest tworzona jako autonomiczny zasób platformy Azure w dzierżawie usługi Azure AD, która jest zaufana przez używaną subskrypcję. Po utworzeniu tożsamości tożsamość można przypisać do jednego lub więcej zasobów platformy Azure (w wystąpieniach kontenera platformy Azure lub innych usługach platformy Azure). Cykl życia tożsamości przypisanej przez użytkownika jest zarządzany oddzielnie od cyklu życia grup kontenerów lub innych zasobów usługi, do których jest przypisany. To zachowanie jest szczególnie przydatne w przypadku wystąpień kontenera platformy Azure. Ponieważ tożsamość wykracza poza okres istnienia grupy kontenerów, można użyć go ponownie wraz z innymi ustawieniami standardowymi, aby wdrożenia grupy kontenerów były wysoce powtarzalne.

* Tożsamość **zarządzana przypisana do systemu** jest włączona bezpośrednio w grupie kontenerów w wystąpieniach kontenera platformy Azure. Gdy jest włączona, platforma Azure tworzy tożsamość dla grupy w dzierżawie usługi Azure AD, która jest zaufana przez subskrypcję wystąpienia. Po utworzeniu tożsamości poświadczenia są aprowizowane w każdym kontenerze w grupie kontenerów. Cykl życia tożsamości przypisanej do systemu jest bezpośrednio powiązany z grupą kontenerów, na których jest włączona. Po usunięciu grupy platforma Azure automatycznie czyści poświadczenia i tożsamość w usłudze Azure AD.

### <a name="use-a-managed-identity"></a>Korzystanie z tożsamości zarządzanej

Aby użyć tożsamości zarządzanej, tożsamość musi początkowo uzyskać dostęp do co najmniej jednego zasobu usługi platformy Azure (takiego jak aplikacja sieci web, magazyn kluczy lub konto magazynu) w ramach subskrypcji. Aby uzyskać dostęp do zasobów platformy Azure z uruchomionego kontenera, kod musi uzyskać *token dostępu* z punktu końcowego usługi Azure AD. Następnie kod wysyła token dostępu na wywołanie do usługi, która obsługuje uwierzytelnianie usługi Azure AD. 

Przy użyciu tożsamości zarządzanej w uruchomionym kontenerze jest zasadniczo taka sama jak przy użyciu tożsamości w maszynie Wirtualnej platformy Azure. Zobacz wskazówki dotyczące korzystania z [tokenu,](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) [narzędzia Azure PowerShell lub interfejsu wiersza polecenia platformy Azure](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md)lub [zestawów SDK platformy Azure.](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md)

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Jeśli zdecydujesz się zainstalować i używać interfejsu wiersza polecenia lokalnie, ten artykuł wymaga, aby uruchomić interfejsu wiersza polecenia platformy Azure w wersji 2.0.49 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli).

## <a name="create-an-azure-key-vault"></a>Tworzenie magazynu kluczy platformy Azure

Przykłady w tym artykule używają tożsamości zarządzanej w wystąpieniach kontenera platformy Azure, aby uzyskać dostęp do klucza tajnego magazynu kluczy platformy Azure. 

Najpierw utwórz grupę zasobów o nazwie *myResourceGroup* w lokalizacji *eastus* za pomocą następującego polecenia [az group create](/cli/azure/group?view=azure-cli-latest#az-group-create):

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Polecenie [az keyvault create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) służy do tworzenia magazynu kluczy. Pamiętaj, aby określić unikatową nazwę magazynu kluczy. 

```azurecli-interactive
az keyvault create \
  --name mykeyvault \
  --resource-group myResourceGroup \ 
  --location eastus
```

Przechowuj przykładowy klucz tajny w magazynie kluczy przy użyciu polecenia [az keyvault secret set:](/cli/azure/keyvault/secret?view=azure-cli-latest#az-keyvault-secret-set)

```azurecli-interactive
az keyvault secret set \
  --name SampleSecret \
  --value "Hello Container Instances" \
  --description ACIsecret --vault-name mykeyvault
```

Przejdź do poniższych przykładów, aby uzyskać dostęp do magazynu kluczy przy użyciu tożsamości zarządzanej przypisanej przez użytkownika lub przypisanej do systemu w wystąpieniach kontenera platformy Azure.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>Przykład 1: Dostęp do magazynu kluczy platformy Azure za pomocą tożsamości przypisanej przez użytkownika

### <a name="create-an-identity"></a>Tworzenie tożsamości

Najpierw utwórz tożsamość w subskrypcji przy użyciu polecenia [az identity create.](/cli/azure/identity?view=azure-cli-latest#az-identity-create) Można użyć tej samej grupy zasobów, która została użyta do utworzenia magazynu kluczy lub użyć innej.

```azurecli-interactive
az identity create \
  --resource-group myResourceGroup \
  --name myACIId
```

Aby użyć tożsamości w poniższych krokach, użyj polecenia [az identity show](/cli/azure/identity?view=azure-cli-latest#az-identity-show) do przechowywania identyfikatora jednostki usługi tożsamości i identyfikatora zasobu w zmiennych.

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show --resource-group myResourceGroup --name myACIId --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show --resource-group myResourceGroup --name myACIId --query id --output tsv)
```

### <a name="enable-a-user-assigned-identity-on-a-container-group"></a>Włączanie tożsamości przypisanej przez użytkownika w grupie kontenerów

Uruchom następujące polecenie [tworzenia kontenera az,](/cli/azure/container?view=azure-cli-latest#az-container-create) aby utworzyć `azure-cli` wystąpienie kontenera na podstawie obrazu firmy Microsoft. W tym przykładzie zawiera grupę jednego kontenera, której można użyć interaktywnie do uruchomienia interfejsu wiersza polecenia platformy Azure w celu uzyskania dostępu do innych usług platformy Azure. W tej sekcji używany jest tylko podstawowy system operacyjny Ubuntu. 

Parametr `--assign-identity` przekazuje przypisaną przez użytkownika tożsamość zarządzaną do grupy. Długotrwałe polecenie utrzymuje kontener działa. W tym przykładzie użyto tej samej grupy zasobów używanej do tworzenia magazynu kluczy, ale można określić inną.

```azurecli-interactive
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity $resourceID \
  --command-line "tail -f /dev/null"
```

W ciągu kilku sekund powinna pojawić się odpowiedź z interfejsu wiersza polecenia platformy Azure, wskazująca ukończenie wdrażania. Sprawdź jego stan za pomocą polecenia [az container show.](/cli/azure/container?view=azure-cli-latest#az-container-show)

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

Sekcja `identity` w danych wyjściowych wygląda podobnie do następującego, pokazując tożsamość jest ustawiona w grupie kontenerów. W `principalID` `userAssignedIdentities` obszarze jest podmiotem usługi tożsamości utworzonej w usłudze Azure Active Directory:

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

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Udzielanie dostępu do tożsamości przypisanej przez użytkownika do magazynu kluczy

Uruchom następujące polecenie [az keyvault set-policy,](/cli/azure/keyvault?view=azure-cli-latest) aby ustawić zasady dostępu w magazynie kluczy. Poniższy przykład umożliwia tożsamości przypisanej przez użytkownika, aby uzyskać wpisy tajne z magazynu kluczy:

```azurecli-interactive
 az keyvault set-policy \
    --name mykeyvault \
    --resource-group myResourceGroup \
    --object-id $spID \
    --secret-permissions get
```

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>Użyj tożsamości przypisanej przez użytkownika, aby uzyskać klucz tajny z magazynu kluczy

Teraz można użyć tożsamości zarządzanej w wystąpieniu uruchomionego kontenera, aby uzyskać dostęp do magazynu kluczy. Najpierw uruchom powłokę bash w kontenerze:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Uruchom następujące polecenia w powłoce bash w kontenerze. Aby uzyskać token dostępu do uwierzytelniania usługi Azure Active Directory za pomocą usługi Azure Active Directory, uruchom następujące polecenie:

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

Teraz użyj tokenu dostępu do uwierzytelniania do magazynu kluczy i odczytu klucza tajnego. Pamiętaj, aby zastąpić nazwę magazynu kluczy w adresie URL (*https:\//mykeyvault.vault.azure.net/...*):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

Odpowiedź wygląda podobnie do następującego, pokazując tajemnicę. W kodzie należy przeanalizować to dane wyjściowe, aby uzyskać klucz tajny. Następnie użyj klucza tajnego w kolejnej operacji, aby uzyskać dostęp do innego zasobu platformy Azure.

```bash
{"value":"Hello Container Instances","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>Przykład 2: Dostęp do magazynu kluczy platformy Azure za pomocą tożsamości przypisanej do systemu

### <a name="enable-a-system-assigned-identity-on-a-container-group"></a>Włączanie tożsamości przypisanej do systemu w grupie kontenerów

Uruchom następujące polecenie [tworzenia kontenera az,](/cli/azure/container?view=azure-cli-latest#az-container-create) aby utworzyć `azure-cli` wystąpienie kontenera na podstawie obrazu firmy Microsoft. W tym przykładzie zawiera grupę jednego kontenera, której można użyć interaktywnie do uruchomienia interfejsu wiersza polecenia platformy Azure w celu uzyskania dostępu do innych usług platformy Azure. 

Parametr `--assign-identity` bez dodatkowej wartości umożliwia przypisaną do systemu tożsamość zarządzaną w grupie. Tożsamość jest ograniczone do grupy zasobów grupy kontenerów. Długotrwałe polecenie utrzymuje kontener działa. W tym przykładzie użyto tej samej grupy zasobów używanej do tworzenia magazynu kluczy, ale można określić inną.

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

W ciągu kilku sekund powinna pojawić się odpowiedź z interfejsu wiersza polecenia platformy Azure, wskazująca ukończenie wdrażania. Sprawdź jego stan za pomocą polecenia [az container show.](/cli/azure/container?view=azure-cli-latest#az-container-show)

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

Sekcja `identity` w danych wyjściowych wygląda podobnie do następującego, pokazując, że tożsamość przypisana do systemu jest tworzona w usłudze Azure Active Directory:

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

Ustaw zmienną na `principalId` wartość (identyfikator jednostki usługi) tożsamości, aby użyć w późniejszych krokach.

```azurecli-interactive
spID=$(az container show --resource-group myResourceGroup --name mycontainer --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Udziel dostępu grupy kontenerów do magazynu kluczy

Uruchom następujące polecenie [az keyvault set-policy,](/cli/azure/keyvault?view=azure-cli-latest) aby ustawić zasady dostępu w magazynie kluczy. Poniższy przykład umożliwia tożsamości zarządzanej przez system, aby uzyskać wpisy tajne z magazynu kluczy:

```azurecli-interactive
 az keyvault set-policy \
   --name mykeyvault \
   --resource-group myResourceGroup \
   --object-id $spID \
   --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>Udostępnianie klucza z magazynu kluczy za pomocą tożsamości grupy kontenerów

Teraz można użyć tożsamości zarządzanej, aby uzyskać dostęp do magazynu kluczy w wystąpieniu uruchomionego kontenera. Najpierw uruchom powłokę bash w kontenerze:

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

Wartość klucza tajnego jest pobierana:

```bash
"Hello Container Instances"
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Włączanie tożsamości zarządzanej przy użyciu szablonu Menedżera zasobów

Aby włączyć tożsamość zarządzaną w grupie kontenerów `identity` przy użyciu `Microsoft.ContainerInstance/containerGroups` [szablonu Menedżera zasobów,](container-instances-multi-container-group.md)ustaw właściwość obiektu za `ContainerGroupIdentity` pomocą obiektu. Poniższe fragmenty kodu `identity` pokazują właściwość skonfigurowaną dla różnych scenariuszy. Zobacz [odwołanie do szablonu Menedżera zasobów](/azure/templates/microsoft.containerinstance/containergroups). Określ `apiVersion` minimum `2018-10-01`.

### <a name="user-assigned-identity"></a>Tożsamość przypisana przez użytkownika

Tożsamość przypisana przez użytkownika jest identyfikatorem zasobu formularza:

```
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"
``` 

Można włączyć jedną lub więcej tożsamości przypisanych przez użytkownika.

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
```

### <a name="system-assigned-identity"></a>Tożsamość przypisana systemowi

```json
"identity": {
    "type": "SystemAssigned"
    }
```

### <a name="system--and-user-assigned-identities"></a>Tożsamości przypisane przez system i użytkownika

W grupie kontenerów można włączyć zarówno tożsamość przypisaną do systemu, jak i jedną lub więcej tożsamości przypisanych przez użytkownika.

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

Aby włączyć tożsamość zarządzaną w grupie kontenerów wdrożonych przy użyciu [pliku YAML,](container-instances-multi-container-yaml.md)należy dołączyć następujący plik YAML.
Określ `apiVersion` minimum `2018-10-01`.

### <a name="user-assigned-identity"></a>Tożsamość przypisana przez użytkownika

Tożsamość przypisana przez użytkownika jest identyfikatorem zasobu formularza 

```
'/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'
```

Można włączyć jedną lub więcej tożsamości przypisanych przez użytkownika.

```YAML
identity:
  type: UserAssigned
  userAssignedIdentities:
    {'myResourceID1':{}}
```

### <a name="system-assigned-identity"></a>Tożsamość przypisana systemowi

```YAML
identity:
  type: SystemAssigned
```

### <a name="system--and-user-assigned-identities"></a>Tożsamości przypisane przez system i użytkownika

W grupie kontenerów można włączyć zarówno tożsamość przypisaną do systemu, jak i jedną lub więcej tożsamości przypisanych przez użytkownika.

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>Następne kroki

W tym artykule dowiesz się o tożsamościach zarządzanych w wystąpieniach kontenera platformy Azure i jak to zrobić:

> [!div class="checklist"]
> * Włączanie tożsamości przypisanej przez użytkownika lub przypisanej do systemu w grupie kontenerów
> * Udzielanie dostępu do tożsamości do magazynu kluczy platformy Azure
> * Użyj tożsamości zarządzanej, aby uzyskać dostęp do magazynu kluczy z uruchomionego kontenera

* Dowiedz się więcej o [tożsamościach zarządzanych dla zasobów platformy Azure](/azure/active-directory/managed-identities-azure-resources/).

* Zobacz [przykład zestawu SDK usługi Azure Go](https://medium.com/@samkreter/c98911206328) przy użyciu tożsamości zarządzanej w celu uzyskania dostępu do magazynu kluczy z wystąpień kontenerów platformy Azure.
