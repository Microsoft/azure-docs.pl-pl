---
title: Konfigurowanie kluczy zarządzanych przez klienta dla konta usługi Azure Cosmos DB
description: Dowiedz się, jak skonfigurować klucze zarządzane przez klienta dla konta usługi Azure Cosmos DB za pomocą usługi Azure Key Vault
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: thweiss
ROBOTS: noindex, nofollow
ms.openlocfilehash: 8f58887a056c8ca0cd175a44127556562338de38
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450036"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>Konfigurowanie kluczy zarządzanych przez klienta dla konta usługi Azure Cosmos za pomocą usługi Azure Key Vault

> [!NOTE]
> W tej chwili należy zażądać dostępu do korzystania z tej funkcji. W tym celu [azurecosmosdbcmk@service.microsoft.com](mailto:azurecosmosdbcmk@service.microsoft.com)należy skontaktować się z firmą .

Dane przechowywane na koncie usługi Azure Cosmos są automatycznie i bezproblemowo szyfrowane za pomocą kluczy zarządzanych przez firmę Microsoft (**klucze zarządzane przez usługę).** Opcjonalnie można dodać drugą warstwę szyfrowania za pomocą zarządzanych kluczy **(kluczy zarządzanych przez klienta).**

![Warstwy szyfrowania wokół danych klienta](./media/how-to-setup-cmk/cmk-intro.png)

Klucze zarządzane przez klienta należy przechowywać w [usłudze Azure Key Vault](../key-vault/general/overview.md) i zapewniać klucz dla każdego konta usługi Azure Cosmos, które jest włączone za pomocą kluczy zarządzanych przez klienta. Ten klucz służy do szyfrowania wszystkich danych przechowywanych na tym koncie.

> [!NOTE]
> Obecnie klucze zarządzane przez klienta są dostępne tylko dla nowych kont usługi Azure Cosmos. Należy skonfigurować je podczas tworzenia konta.

## <a name="register-the-azure-cosmos-db-resource-provider-for-your-azure-subscription"></a><a id="register-resource-provider"></a>Zarejestruj dostawcę zasobów usługi Azure Cosmos DB dla subskrypcji platformy Azure

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/), przejdź do subskrypcji platformy Azure i wybierz **dostawców zasobów** na karcie **Ustawienia:**

   ![Wpis "Dostawcy zasobów" z lewego menu](./media/how-to-setup-cmk/portal-rp.png)

1. Wyszukaj dostawcę zasobów **microsoft.DocumentDB.** Sprawdź, czy dostawca zasobów jest już oznaczony jako zarejestrowany. Jeśli nie, wybierz dostawcę zasobów i wybierz pozycję **Zarejestruj:**

   ![Rejestrowanie dostawcy zasobów usługi Microsoft.DocumentDB](./media/how-to-setup-cmk/portal-rp-register.png)

## <a name="configure-your-azure-key-vault-instance"></a>Konfigurowanie wystąpienia usługi Azure Key Vault

Przy użyciu kluczy zarządzanych przez klienta z usługi Azure Cosmos DB wymaga, aby ustawić dwie właściwości w wystąpieniu usługi Azure Key Vault, które mają być używane do hosta kluczy szyfrowania. Właściwości te obejmują **usuwanie nietrwałe** i **nie czyścić**. Te właściwości nie są domyślnie włączone. Można je włączyć przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure.

Aby dowiedzieć się, jak włączyć te właściwości w istniejącym wystąpieniu usługi Azure Key Vault, zobacz sekcje "Włączanie usuwania nietrwałego" i "Włączanie ochrony przed przeczyszczaniem" w jednym z następujących artykułów:

- [Jak używać usuwania nietrwałego w programie PowerShell](../key-vault/general/soft-delete-powershell.md)
- [Jak używać usuwania nietrwałego za pomocą interfejsu wiersza polecenia platformy Azure](../key-vault/general/soft-delete-cli.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Dodawanie zasad dostępu do wystąpienia usługi Azure Key Vault

1. W witrynie Azure Portal przejdź do wystąpienia usługi Azure Key Vault, którego zamierzasz używać do obsługi kluczy szyfrowania. Z lewego menu wybierz **pozycję Zasady dostępu:**

   !["Zasady dostępu" z lewego menu](./media/how-to-setup-cmk/portal-akv-ap.png)

1. Wybierz **+ Dodaj zasady dostępu**.

1. W menu rozwijanym **Uprawnienia klucz** wybierz pozycję Pobierz , **Rozpakuj klawisz**i **Zawijanie klucza** uprawnienia: **Get**

   ![Wybieranie odpowiednich uprawnień](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

1. W obszarze **Wybierz głównego**wybierz pozycję **Brak zaznaczone**. Następnie wyszukaj podmiot usługi **Azure Cosmos DB** i wybierz go (aby ułatwić znajdowanie, możesz również wyszukiwać według identyfikatora głównego: `a232010e-820c-4083-83bb-3ace5fc29d0b` dla dowolnego regionu platformy Azure z wyjątkiem regionów platformy Azure dla instytucji rządowych, w których znajduje `57506a73-e302-42a9-b869-6f12d9ec29e9`się główny identyfikator). Na koniec wybierz pozycję **Wybierz** u dołu. Jeśli podmiot **usługi Azure Cosmos DB** nie znajduje się na liście, może być konieczne ponowne zarejestrowanie dostawcy zasobów **microsoft.DocumentDB** zgodnie z opisem w sekcji [Zarejestruj dostawcę zasobów](#register-resource-provider) w tym artykule.

   ![Wybierz głównego administratora usługi Azure Cosmos DB](./media/how-to-setup-cmk/portal-akv-add-ap.png)

1. Wybierz **pozycję Dodaj,** aby dodać nową zasadę dostępu.

## <a name="generate-a-key-in-azure-key-vault"></a>Generowanie klucza w usłudze Azure Key Vault

1. W witrynie Azure Portal przejdź do wystąpienia usługi Azure Key Vault, którego zamierzasz używać do obsługi kluczy szyfrowania. Następnie wybierz **klawisze** z lewego menu:

   ![Wpis "Klawisze" z lewego menu](./media/how-to-setup-cmk/portal-akv-keys.png)

1. Wybierz **pozycję Generuj/Zaimportuj**, podaj nazwę nowego klucza i wybierz rozmiar klucza RSA. Dla najlepszego zabezpieczenia zaleca się co najmniej 3072. Następnie wybierz **pozycję Utwórz**:

   ![Tworzenie nowego klucza](./media/how-to-setup-cmk/portal-akv-gen.png)

1. Po utworzeniu klucza wybierz nowo utworzony klucz, a następnie jego bieżącą wersję.

1. Skopiuj **identyfikator klucza**klucza , z wyjątkiem części po ostatnim ukośniku do przodu:

   ![Kopiowanie identyfikatora klucza](./media/how-to-setup-cmk/portal-akv-keyid.png)

## <a name="create-a-new-azure-cosmos-account"></a>Tworzenie nowego konta usługi Azure Cosmos

### <a name="using-the-azure-portal"></a>Korzystanie z witryny Azure Portal

Podczas tworzenia nowego konta usługi Azure Cosmos DB z witryny Azure portal, wybierz **klucz zarządzany przez klienta** w kroku **Szyfrowanie.** W polu **Identyfikator URI klucza** wklej identyfikator URI/klucz klucza usługi Azure Key Vault skopiowany z poprzedniego kroku:

![Ustawianie parametrów cmk w witrynie Azure portal](./media/how-to-setup-cmk/portal-cosmos-enc.png)

### <a name="using-azure-powershell"></a>Korzystanie z programu Azure PowerShell

Podczas tworzenia nowego konta usługi Azure Cosmos DB z programem PowerShell:

- Przekaż identyfikator URI klucza usługi Azure Key Vault skopiowany wcześniej w ramach właściwości **keyVaultKeyUri** w **PropertyObject**.

- Użyj **wersji interfejsu API 2019-12-12.**

> [!IMPORTANT]
> Należy ustawić `Location` parametr jawnie, aby konto było pomyślnie utworzone za pomocą kluczy zarządzanych przez klienta.

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

### <a name="using-an-azure-resource-manager-template"></a>Korzystanie z szablonu usługi Azure Resource Manager

Podczas tworzenia nowego konta usługi Azure Cosmos za pomocą szablonu usługi Azure Resource Manager:

- Przekaż identyfikator URI klucza usługi Azure Key Vault, który został skopiowany wcześniej pod właściwość **keyVaultKeyUri** w obiekcie **właściwości.**

- Użyj **wersji interfejsu API 2019-12-12.**

> [!IMPORTANT]
> Należy ustawić `Location` parametr jawnie, aby konto było pomyślnie utworzone za pomocą kluczy zarządzanych przez klienta.

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

Wdrażanie szablonu za pomocą następującego skryptu programu PowerShell:

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

### <a name="using-the-azure-cli"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure

Podczas tworzenia nowego konta usługi Azure Cosmos za pośrednictwem interfejsu wiersza polecenia platformy Azure należy przekazać identyfikator URI klucza usługi Azure Key Vault, który został skopiowany wcześniej w ramach parametru **--key-uri.**

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

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="is-there-any-additional-charge-for-using-customer-managed-keys"></a>Czy istnieje dodatkowa opłata za korzystanie z kluczy zarządzanych przez klienta?

Tak. Aby uwzględnić dodatkowe obciążenie obliczeniowe wymagane do zarządzania szyfrowaniem danych i odszyfrowywaniem za pomocą kluczy zarządzanych przez klienta, wszystkie operacje wykonywane na koncie usługi Azure Cosmos zużywają 25-procentowy wzrost [jednostek żądań.](./request-units.md)

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>Jakie dane są szyfrowane za pomocą kluczy zarządzanych przez klienta?

Wszystkie dane przechowywane na koncie usługi Azure Cosmos są szyfrowane przy użyciu kluczy zarządzanych przez klienta, z wyjątkiem następujących metadanych:

- Nazwy [kont, baz danych i kontenerów](./account-overview.md#elements-in-an-azure-cosmos-account) usługi Azure Cosmos DB

- Nazwy procedur [przechowywanych](./stored-procedures-triggers-udfs.md)

- Ścieżki właściwości zadeklarowane w [zasadach indeksowania](./index-policy.md)

- Wartości [kluczy partycji kontenerów](./partitioning-overview.md)

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>Czy klucze zarządzane przez klienta są obsługiwane dla istniejących kont usługi Azure Cosmos?

Ta funkcja jest obecnie dostępna tylko dla nowych kont.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>Czy istnieje plan obsługi drobniejszej szczegółowości niż klucze na poziomie konta?

Obecnie nie jest brane pod uwagę klucze na poziomie kontenera.

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>Jak klucze zarządzane przez klienta wpływają na kopię zapasową?

Usługa Azure Cosmos DB wykonuje [regularne i automatyczne kopie zapasowe](./online-backup-and-restore.md) danych przechowywanych na koncie. Ta operacja powoduje utworzenie kopii zapasowej zaszyfrowanych danych. Aby użyć przywróconej kopii zapasowej, wymagany jest klucz szyfrowania używany w czasie wykonywania kopii zapasowej. Oznacza to, że nie dokonano odwołania, a wersja klucza, który był używany w czasie tworzenia kopii zapasowej, będzie nadal włączona.

### <a name="how-do-i-revoke-an-encryption-key"></a>Jak odwołać klucz szyfrowania?

Odwołanie klucza odbywa się przez wyłączenie najnowszej wersji klucza:

![Wyłączanie wersji klucza](./media/how-to-setup-cmk/portal-akv-rev2.png)

Alternatywnie, aby odwołać wszystkie klucze z wystąpienia usługi Azure Key Vault, można usunąć zasady dostępu przyznane podmiotowi usługi Azure Cosmos DB:

![Usuwanie zasad dostępu dla podmiotu zabezpieczeń usługi Azure Cosmos DB](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>Jakie operacje są dostępne po odwołaniu klucza zarządzanego przez klienta?

Jedyną możliwą operacją po odwołaniu klucza szyfrowania jest usunięcie konta.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [szyfrowaniu danych w usłudze Azure Cosmos DB](./database-encryption-at-rest.md).
- Uzyskaj przegląd [bezpiecznego dostępu do danych w usłudze Cosmos DB](secure-access-to-data.md).
