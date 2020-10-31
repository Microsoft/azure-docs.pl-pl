---
title: Konfigurowanie kluczy zarządzanych przez klienta dla konta Azure Cosmos DB
description: Dowiedz się, jak skonfigurować klucze zarządzane przez klienta dla konta Azure Cosmos DB przy użyciu Azure Key Vault
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/05/2020
ms.author: thweiss
ms.openlocfilehash: 21bb594f4e374d41cfc4184f3a72aea1717c85d8
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93086146"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>Skonfiguruj klucze zarządzane przez klienta na potrzeby konta usługi Azure Cosmos przy użyciu usługi Azure Key Vault
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Dane przechowywane na koncie usługi Azure Cosmos są automatycznie i bezproblemowo szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft ( **klucze zarządzane przez usługę** ). Opcjonalnie można dodać drugą warstwę szyfrowania z kluczami, którymi zarządzasz ( **klucze zarządzane przez klienta** ).

:::image type="content" source="./media/how-to-setup-cmk/cmk-intro.png" alt-text="Warstwy szyfrowania wokół danych klienta":::

Klucze zarządzane przez klienta muszą być przechowywane w [Azure Key Vault](../key-vault/general/overview.md) i zapewniać klucz dla każdego konta usługi Azure Cosmos, które jest włączone przy użyciu kluczy zarządzanych przez klienta. Ten klucz służy do szyfrowania wszystkich danych przechowywanych na tym koncie.

> [!NOTE]
> Obecnie klucze zarządzane przez klienta są dostępne tylko dla nowych kont usługi Azure Cosmos. Należy je skonfigurować podczas tworzenia konta.

## <a name="register-the-azure-cosmos-db-resource-provider-for-your-azure-subscription"></a><a id="register-resource-provider"></a> Zarejestruj dostawcę zasobów Azure Cosmos DB dla subskrypcji platformy Azure

1. Zaloguj się do [Azure Portal](https://portal.azure.com/), przejdź do subskrypcji platformy Azure, a następnie wybierz pozycję **dostawcy zasobów** na karcie **Ustawienia** :

   :::image type="content" source="./media/how-to-setup-cmk/portal-rp.png" alt-text="Warstwy szyfrowania wokół danych klienta":::

1. Wyszukaj dostawcę zasobów usługi **Microsoft.DocumentDB** . Sprawdź, czy dostawca zasobów został już oznaczony jako zarejestrowany. W przeciwnym razie wybierz dostawcę zasobów i wybierz pozycję **zarejestruj** :

   :::image type="content" source="./media/how-to-setup-cmk/portal-rp-register.png" alt-text="Warstwy szyfrowania wokół danych klienta":::

## <a name="configure-your-azure-key-vault-instance"></a>Skonfiguruj wystąpienie Azure Key Vault

Użycie kluczy zarządzanych przez klienta z Azure Cosmos DB wymaga ustawienia dwóch właściwości w wystąpieniu Azure Key Vault, które ma być używane do hostowania kluczy szyfrowania: **trwałe usuwanie** i **przeczyszczanie** .

Jeśli tworzysz nowe wystąpienie Azure Key Vault, Włącz te właściwości podczas tworzenia:

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-prop.png" alt-text="Warstwy szyfrowania wokół danych klienta" w jednym z następujących artykułów:

- [Jak używać nietrwałego usuwania przy użyciu programu PowerShell](../key-vault/general/soft-delete-powershell.md)
- [Jak używać nietrwałego usuwania przy użyciu interfejsu wiersza polecenia platformy Azure](../key-vault/general/soft-delete-cli.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Dodawanie zasad dostępu do wystąpienia Azure Key Vault

1. W Azure Portal przejdź do wystąpienia Azure Key Vault, które ma być używane do hostowania kluczy szyfrowania. Wybierz pozycję **zasady dostępu** w menu po lewej stronie:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-ap.png" alt-text="Warstwy szyfrowania wokół danych klienta":::

1. Wybierz pozycję **+ Dodaj zasady dostępu** .

1. W menu rozwijanym **uprawnienia klucza** wybierz pozycję **Pobierz** , **Odpakuj klucz** i **Zawijaj uprawnienia klucza** :

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png" alt-text="Warstwy szyfrowania wokół danych klienta":::

1. W obszarze **Wybierz podmiot zabezpieczeń** wybierz pozycję **nie wybrano** . Następnie wyszukaj **Azure Cosmos DB** podmiot zabezpieczeń i wybierz go (aby ułatwić znalezienie, możesz również wyszukiwać według identyfikatora podmiotu zabezpieczeń `a232010e-820c-4083-83bb-3ace5fc29d0b` ) w dowolnym regionie świadczenia usługi Azure, Azure Government z wyjątkiem regionów, w których jest to identyfikator podmiotu zabezpieczeń `57506a73-e302-42a9-b869-6f12d9ec29e9` . Na koniec wybierz **pozycję Wybierz** u dołu. Jeśli podmiot zabezpieczeń **Azure Cosmos DB** nie znajduje się na liście, może być konieczne ponowne zarejestrowanie **Microsoft.Doc** dostawcy zasobów umentDB zgodnie z opisem w sekcji [Rejestrowanie dostawcy zasobów](#register-resource-provider) w tym artykule.

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-add-ap.png" alt-text="Warstwy szyfrowania wokół danych klienta":::

1. Wybierz pozycję **Dodaj** , aby dodać nowe zasady dostępu.

1. Wybierz pozycję **Zapisz** w wystąpieniu Key Vault, aby zapisać wszystkie zmiany.

## <a name="generate-a-key-in-azure-key-vault"></a>Wygeneruj klucz w Azure Key Vault

1. W Azure Portal przejdź do wystąpienia Azure Key Vault, które ma być używane do hostowania kluczy szyfrowania. Następnie wybierz pozycję **klucze** z menu po lewej stronie:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-keys.png" alt-text="Warstwy szyfrowania wokół danych klienta":::

1. Wybierz pozycję **Generuj/Importuj** , podaj nazwę nowego klucza, a następnie wybierz rozmiar klucza RSA. Aby zapewnić najlepsze zabezpieczenia, zalecamy co najmniej 3072. Następnie wybierz pozycję **Utwórz** :

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-gen.png" alt-text="Warstwy szyfrowania wokół danych klienta":::

1. Po utworzeniu klucza wybierz nowo utworzony klucz, a następnie jego bieżącą wersję.

1. Skopiuj **Identyfikator klucza** klucza, z wyjątkiem części po ostatnim ukośniku:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-keyid.png" alt-text="Warstwy szyfrowania wokół danych klienta":::

## <a name="create-a-new-azure-cosmos-account"></a>Utwórz nowe konto usługi Azure Cosmos

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Po utworzeniu nowego konta Azure Cosmos DB z Azure Portal wybierz opcję **klucz zarządzany przez klienta** w kroku **szyfrowanie** . W polu **Identyfikator URI klucza** wklej identyfikator URI/klucz Azure Key Vault klucza skopiowanego z poprzedniego kroku:

:::image type="content" source="./media/how-to-setup-cmk/portal-cosmos-enc.png" alt-text="Warstwy szyfrowania wokół danych klienta":::

### <a name="using-azure-powershell"></a><a id="using-powershell"></a> Używanie Azure PowerShell

Podczas tworzenia nowego konta Azure Cosmos DB przy użyciu programu PowerShell:

- Przekaż identyfikator URI klucza Azure Key Vault skopiowanego wcześniej we właściwości **keyVaultKeyUri** w **właściwościobject** .

- Użyj **2019-12-12** lub nowszej jako wersji interfejsu API.

> [!IMPORTANT]
> Należy ustawić `locations` właściwość jawnie dla konta, aby można je było pomyślnie utworzyć przy użyciu kluczy zarządzanych przez klienta.

```powershell
$resourceGroupName = "myResourceGroup"
$accountLocation = "West US 2"
$accountName = "mycosmosaccount"

$failoverLocations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 }
)

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$failoverLocations;
    "keyVaultKeyUri" = "https://<my-vault>.vault.azure.net/keys/<my-key>";
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2019-12-12" -ResourceGroupName $resourceGroupName `
    -Location $accountLocation -Name $accountName -PropertyObject $CosmosDBProperties
```

Po utworzeniu konta możesz sprawdzić, czy zostały włączone klucze zarządzane przez klienta, pobierając identyfikator URI klucza Azure Key Vault:

```powershell
Get-AzResource -ResourceGroupName $resourceGroupName -Name $accountName `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    | Select-Object -ExpandProperty Properties `
    | Select-Object -ExpandProperty keyVaultKeyUri
```

### <a name="using-an-azure-resource-manager-template"></a>Korzystanie z szablonu Azure Resource Manager

Podczas tworzenia nowego konta usługi Azure Cosmos za pomocą szablonu Azure Resource Manager:

- Przekaż identyfikator URI klucza Azure Key Vault, który został wcześniej skopiowany we właściwości **keyVaultKeyUri** w obiekcie **Properties** .

- Użyj **2019-12-12** lub nowszej jako wersji interfejsu API.

> [!IMPORTANT]
> Należy ustawić `locations` właściwość jawnie dla konta, aby można je było pomyślnie utworzyć przy użyciu kluczy zarządzanych przez klienta.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "keyVaultKeyUri": {
            "type": "string"
        }
    },
    "resources": 
    [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[parameters('accountName')]",
            "apiVersion": "2019-12-12",
            "kind": "GlobalDocumentDB",
            "location": "[parameters('location')]",
            "properties": {
                "locations": [ 
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    }
                ],
                "databaseAccountOfferType": "Standard",
                "keyVaultKeyUri": "[parameters('keyVaultKeyUri')]"
            }
        }
    ]
}
```

Wdróż szablon przy użyciu następującego skryptu programu PowerShell:

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

### <a name="using-the-azure-cli"></a><a id="using-azure-cli"></a> Korzystanie z interfejsu wiersza polecenia platformy Azure

Podczas tworzenia nowego konta usługi Azure Cosmos za pomocą interfejsu wiersza polecenia platformy Azure należy przekazać identyfikator URI Azure Key Vault klucza skopiowanego wcześniej pod `--key-uri` parametrem.

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
keyVaultKeyUri = 'https://<my-vault>.vault.azure.net/keys/<my-key>'

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --key-uri $keyVaultKeyUri
```

Po utworzeniu konta możesz sprawdzić, czy zostały włączone klucze zarządzane przez klienta, pobierając identyfikator URI klucza Azure Key Vault:

```azurecli-interactive
az cosmosdb show \
    -n $accountName \
    -g $resourceGroupName \
    --query keyVaultKeyUri
```

## <a name="key-rotation"></a>Wymiana kluczy

Rotacja klucza zarządzanego przez klienta używanego przez konto usługi Azure Cosmos można wykonać na dwa sposoby.

- Utwórz nową wersję klucza aktualnie używaną w Azure Key Vault:

  :::image type="content" source="./media/how-to-setup-cmk/portal-akv-rot.png" alt-text="Warstwy szyfrowania wokół danych klienta":::

- Zamień klucz aktualnie używany z całkowicie innym, aktualizując klucz URI na koncie. W Azure Portal przejdź do swojego konta usługi Azure Cosmos i wybierz pozycję **szyfrowanie danych** w menu po lewej stronie:

    :::image type="content" source="./media/how-to-setup-cmk/portal-data-encryption.png" alt-text="Warstwy szyfrowania wokół danych klienta":::

    Następnie zastąp **Identyfikator URI klucza** nowym kluczem, którego chcesz użyć, a następnie wybierz pozycję **Zapisz** :

    :::image type="content" source="./media/how-to-setup-cmk/portal-key-swap.png" alt-text="Warstwy szyfrowania wokół danych klienta":::

    Poniżej przedstawiono sposób osiągnięcia tego samego wyniku w programie PowerShell:

    ```powershell
    $resourceGroupName = "myResourceGroup"
    $accountName = "mycosmosaccount"
    $newKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-new-key>"
    
    $account = Get-AzResource -ResourceGroupName $resourceGroupName -Name $accountName `
        -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    
    $account.Properties.keyVaultKeyUri = $newKeyUri
    
    $account | Set-AzResource -Force
    ```

Poprzedni klucz lub wersję klucza można wyłączyć po 24 godzinach lub w [dziennikach inspekcji Azure Key Vault](../key-vault/general/logging.md) nie będą już wyświetlane działania Azure Cosmos DB w tym kluczu lub wersji klucza.
    
## <a name="error-handling"></a>Obsługa błędów

W przypadku korzystania z kluczy Customer-Managed (CMK) w Azure Cosmos DB, jeśli wystąpią jakieś błędy, Azure Cosmos DB zwróci szczegóły błędu oraz kod stanu podrzędnego HTTP w odpowiedzi. Tego kodu stanu podrzędnego można użyć do debugowania głównej przyczyny problemu. Zobacz [kody stanu HTTP dla Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) artykułu, aby uzyskać listę obsługiwanych kodów stanu podrzędnego http.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="is-there-an-additional-charge-to-enable-customer-managed-keys"></a>Czy istnieje dodatkowa opłata za włączenie kluczy zarządzanych przez klienta?

Nie, nie jest naliczana opłata za włączenie tej funkcji.

### <a name="how-do-customer-managed-keys-impact-capacity-planning"></a>Jak klucze zarządzane przez klienta wpływają na planowanie pojemności?

W przypadku korzystania z kluczy zarządzanych przez klienta [jednostki żądań](./request-units.md) używane przez operacje bazy danych widzą zwiększenie, aby odzwierciedlić dodatkowe przetwarzanie wymagane do szyfrowania i odszyfrowywania danych. Może to prowadzić do nieco wyższego wykorzystania zainicjowanej pojemności. Skorzystaj z poniższej tabeli, aby uzyskać wskazówki:

| Typ operacji | Zwiększenie jednostki żądania |
|---|---|
| Odczyty punktów (pobieranie elementów według ich identyfikatora) | + 5% na operację |
| Każda operacja zapisu | + 6% na operację<br/>około. + 0,06 RU na indeksowaną Właściwość |
| Zapytania, odczytywanie informacji o zmianach lub strumieniowe źródło danych | + 15% na operację |

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>Jakie dane są szyfrowane za pomocą kluczy zarządzanych przez klienta?

Wszystkie dane przechowywane na koncie usługi Azure Cosmos są szyfrowane przy użyciu kluczy zarządzanych przez klienta, z wyjątkiem następujących metadanych:

- Nazwy [kont Azure Cosmos DB, baz danych i kontenerów](./account-databases-containers-items.md#elements-in-an-azure-cosmos-account)

- Nazwy [procedur składowanych](./stored-procedures-triggers-udfs.md)

- Ścieżki właściwości zadeklarowane w [zasadach indeksowania](./index-policy.md)

- Wartości [kluczy partycji](./partitioning-overview.md) kontenera

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>Czy dla istniejących kont usługi Azure Cosmos są obsługiwane klucze zarządzane przez klienta?

Ta funkcja jest obecnie dostępna tylko dla nowych kont.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>Czy istnieje plan obsługi bardziej szczegółowego stopnia szczegółowości niż klucze poziomu konta?

Obecnie nie są uwzględniane klucze poziomu kontenera.

### <a name="how-can-i-tell-if-customer-managed-keys-are-enabled-on-my-azure-cosmos-account"></a>Jak mogę sprawdzić, czy na moim koncie usługi Azure Cosmos są włączone klucze zarządzane przez klienta?

W Azure Portal przejdź do konta usługi Azure Cosmos i obejrzyj wpis **szyfrowania danych** w menu po lewej stronie. Jeśli ten wpis istnieje, klucze zarządzane przez klienta są włączone na Twoim koncie:

:::image type="content" source="./media/how-to-setup-cmk/portal-data-encryption.png" alt-text="Warstwy szyfrowania wokół danych klienta":::

Możesz również programowo pobrać szczegóły konta usługi Azure Cosmos i wyszukać obecność `keyVaultKeyUri` właściwości. Zobacz powyżej, aby dowiedzieć się, jak to zrobić [w programie PowerShell](#using-powershell) i [przy użyciu interfejsu wiersza polecenia platformy Azure](#using-azure-cli).

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>Jak klucze zarządzane przez klienta wpływają na kopię zapasową?

Azure Cosmos DB [regularnie i automatycznie tworzy kopie zapasowe](./online-backup-and-restore.md) danych przechowywanych na koncie. Ta operacja tworzy kopię zapasową zaszyfrowanych danych. Aby można było użyć przywróconej kopii zapasowej, wymagany jest klucz szyfrowania używany w momencie tworzenia kopii zapasowej. Oznacza to, że nie wprowadzono odwołania, a wersja klucza, która była używana w momencie tworzenia kopii zapasowej, nadal będzie włączona.

### <a name="how-do-i-revoke-an-encryption-key"></a>Jak mogę odwołać klucz szyfrowania?

Odwoływanie klucza jest wykonywane przez wyłączenie najnowszej wersji klucza:

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-rev2.png" alt-text="Warstwy szyfrowania wokół danych klienta":::

Alternatywnie, aby odwołać wszystkie klucze z wystąpienia Azure Key Vault, można usunąć zasady dostępu przyznane do Azure Cosmos DB podmiotu zabezpieczeń:

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-rev.png" alt-text="Warstwy szyfrowania wokół danych klienta":::

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>Jakie operacje są dostępne po odwołaniu klucza zarządzanego przez klienta?

Jedyną operacją możliwą do odwołania klucza szyfrowania jest usunięcie konta.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej [na temat szyfrowania danych w Azure Cosmos DB](./database-encryption-at-rest.md).
- Zapoznaj się z omówieniem [bezpiecznego dostępu do danych w Cosmos DB](secure-access-to-data.md).