---
title: Skonfiguruj klucze zarządzane przez klienta dla konta Azure Batch przy użyciu tożsamości Azure Key Vault i zarządzanej
description: Dowiedz się, jak szyfrować dane wsadowe przy użyciu kluczy zarządzanych przez klienta.
author: pkshultz
ms.topic: how-to
ms.date: 01/25/2021
ms.author: peshultz
ms.openlocfilehash: 01dc21f067b03ad8e07a05a18aa6312ed7f7189e
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98789417"
---
# <a name="configure-customer-managed-keys-for-your-azure-batch-account-with-azure-key-vault-and-managed-identity"></a>Skonfiguruj klucze zarządzane przez klienta dla konta Azure Batch przy użyciu tożsamości Azure Key Vault i zarządzanej

Domyślnie Azure Batch używa kluczy zarządzanych przez platformę do szyfrowania wszystkich danych klienta przechowywanych w usłudze Azure Batch, takich jak certyfikaty, zadania/metadane zadania. Opcjonalnie możesz użyć własnych kluczy, takich jak klucze zarządzane przez klienta, aby szyfrować dane przechowywane w Azure Batch.

Udostępniane klucze muszą być generowane w [Azure Key Vault](../key-vault/general/basic-concepts.md)i muszą być dostępne z [tożsamościami zarządzanymi dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).

Istnieją dwa typy tożsamości zarządzanych: [ *przypisane do systemu* i *przypisane przez użytkownika*](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types).

Można utworzyć konto w usłudze Batch przy użyciu tożsamości zarządzanej przypisanej do systemu lub utworzyć oddzielną tożsamość zarządzaną przez użytkownika, która będzie miała dostęp do kluczy zarządzanych przez klienta. Zapoznaj się z [tabelą porównania](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) , aby poznać różnice, i rozważ, która opcja działa najlepiej dla danego rozwiązania. Jeśli na przykład chcesz używać tej samej tożsamości zarządzanej do uzyskiwania dostępu do wielu zasobów platformy Azure, będzie potrzebna tożsamość zarządzana przypisana przez użytkownika. W przeciwnym razie tożsamość zarządzana przypisana przez system skojarzona z kontem partii może być wystarczająca. Użycie tożsamości zarządzanej przypisanej przez użytkownika umożliwia również wymuszanie kluczy zarządzanych przez klienta podczas tworzenia konta usługi Batch, jak pokazano [w poniższym przykładzie](#create-a-batch-account-with-user-assigned-managed-identity-and-customer-managed-keys).

> [!IMPORTANT]
> Obsługa kluczy zarządzanych przez klienta w Azure Batch jest obecnie dostępna w publicznej wersji zapoznawczej dla Europy Zachodniej, Europa Północna, Szwajcaria Północna, środkowe stany USA, Południowo-środkowe stany USA, zachodnie stany USA, Wschodnie stany USA, Wschodnie stany USA 2, zachodnie stany USA 2, US Gov Wirginia i US Gov Arizona.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-batch-account-with-system-assigned-managed-identity"></a>Tworzenie konta w usłudze Batch z tożsamością zarządzaną przypisaną przez system

Jeśli nie potrzebujesz oddzielnej tożsamości zarządzanej przypisanej przez użytkownika, możesz włączyć tożsamość zarządzaną przypisaną przez system podczas tworzenia konta w usłudze Batch.

### <a name="azure-portal"></a>Witryna Azure Portal

W [Azure Portal](https://portal.azure.com/)podczas tworzenia kont wsadowych wybierz pozycję **system przypisany** w typie tożsamości na karcie **Zaawansowane** .

![Zrzut ekranu przedstawiający nowe konto w usłudze Batch z typem tożsamości przypisanym do systemu.](./media/batch-customer-managed-key/create-batch-account.png)

Po utworzeniu konta można znaleźć unikatowy identyfikator GUID w polu **Identyfikator podmiotu zabezpieczeń** w sekcji **Właściwości** . Zostanie wyświetlony **Typ tożsamości** `System assigned` .

![Zrzut ekranu przedstawiający unikatowy identyfikator GUID w polu identyfikatora podmiotu zabezpieczeń tożsamości.](./media/batch-customer-managed-key/linked-batch-principal.png)

Ta wartość będzie potrzebna w celu przyznania temu kontu partii dostępu do Key Vault.

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Podczas tworzenia nowego konta usługi Batch należy określić `SystemAssigned` wartość `--identity` parametru.

```azurecli
resourceGroupName='myResourceGroup'
accountName='mybatchaccount'

az batch account create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' \
    --identity 'SystemAssigned'
```

Po utworzeniu konta możesz sprawdzić, czy na tym koncie jest włączona tożsamość zarządzana przypisana przez system. Należy pamiętać, że należy zwrócić uwagę `PrincipalId` , ponieważ ta wartość będzie konieczna do udzielenia temu kontu wsadowemu dostępu do Key Vault.

```azurecli
az batch account show \
    -n $accountName \
    -g $resourceGroupName \
    --query identity
```

> [!NOTE]
> Tożsamość zarządzana przypisana przez system utworzona na koncie usługi Batch jest używana tylko do pobierania kluczy zarządzanych przez klienta z Key Vault. Ta tożsamość nie jest dostępna w pulach partii.

## <a name="create-a-user-assigned-managed-identity"></a>Tworzenie tożsamości zarządzanej przypisanej przez użytkownika

Jeśli wolisz, możesz [utworzyć tożsamość zarządzaną przypisaną przez użytkownika](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) , która może być używana do uzyskiwania dostępu do kluczy zarządzanych przez klienta.

Aby można było uzyskać dostęp do Key Vault, wymagana jest wartość **identyfikatora klienta** tej tożsamości.

## <a name="configure-your-azure-key-vault-instance"></a>Skonfiguruj wystąpienie Azure Key Vault

Azure Key Vault, w którym klucze będą generowane, muszą zostać utworzone w tej samej dzierżawie co konto w usłudze Batch. Nie musi znajdować się w tej samej grupie zasobów lub nawet w tej samej subskrypcji.

### <a name="create-an-azure-key-vault"></a>Tworzenie usługi Azure Key Vault

Podczas [tworzenia wystąpienia Azure Key Vault](../key-vault/general/quick-create-portal.md) z kluczami zarządzanymi przez klienta dla Azure Batch upewnij się, że włączono ochronę **nietrwałego usuwania** i **przeczyszczania** .

![Zrzut ekranu przedstawiający ekran tworzenia Key Vault.](./media/batch-customer-managed-key/create-key-vault.png)

### <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Dodawanie zasad dostępu do wystąpienia Azure Key Vault

W Azure Portal po utworzeniu Key Vault w obszarze **zasady dostępu** w obszarze **ustawienie** Dodaj dostęp do konta usługi Batch przy użyciu tożsamości zarządzanej. W obszarze **uprawnienia klucza** wybierz pozycję **Pobierz**, **Zawijaj klucz** i **Odpakuj klucz**.

![Screenshow wyświetlania ekranu Dodawanie zasad dostępu.](./media/batch-customer-managed-key/key-permissions.png)

W polu **Wybierz** w obszarze **podmiot zabezpieczeń** Wypełnij jedną z następujących opcji:

- W przypadku tożsamości zarządzanej przypisanej do systemu: wprowadź `principalId` wcześniej pobraną lub nazwę konta usługi Batch.
- W przypadku tożsamości zarządzanej przypisanej przez użytkownika: wprowadź wcześniej pobrany **Identyfikator klienta** lub nazwę zarządzanej tożsamości przypisanej do użytkownika.

![Zrzut ekranu przedstawiający ekran główny.](./media/batch-customer-managed-key/principal-id.png)

### <a name="generate-a-key-in-azure-key-vault"></a>Wygeneruj klucz w Azure Key Vault

W Azure Portal przejdź do wystąpienia Key Vault w sekcji **klucz** , wybierz pozycję **Generuj/Importuj**. Wybierz **Typ klucza** , który ma być `RSA` i **Rozmiar klucza RSA** , aby mieć co najmniej `2048` bity. `EC` typy kluczy nie są obecnie obsługiwane jako klucz zarządzany przez klienta na koncie wsadowym.

![Utwórz klucz](./media/batch-customer-managed-key/create-key.png)

Po utworzeniu klucza kliknij nowo utworzony klucz i bieżącą wersję, skopiuj **Identyfikator klucza** w sekcji **Właściwości** .  Upewnij się, że w obszarze **dozwolone operacje** jest zaznaczone pole **Zawijanie klucza** i **odwinięcie klucza** .

## <a name="enable-customer-managed-keys-on-a-batch-account"></a>Włączanie kluczy zarządzanych przez klienta na koncie wsadowym

Po wykonaniu powyższych kroków można włączyć klucze zarządzane przez klienta na koncie w usłudze Batch.

### <a name="azure-portal"></a>Witryna Azure Portal

W [Azure Portal](https://portal.azure.com/)przejdź do strony konto w usłudze Batch. W sekcji **szyfrowanie** Włącz **klucz zarządzany przez klienta**. Można bezpośrednio użyć identyfikatora klucza lub wybrać Magazyn kluczy, a następnie kliknąć pozycję **Wybierz magazyn kluczy i klucz**.

![Zrzut ekranu przedstawiający sekcję szyfrowanie i opcję umożliwiającą włączenie klucza zarządzanego przez klienta](./media/batch-customer-managed-key/encryption-page.png)

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Po utworzeniu konta w usłudze Batch przy użyciu tożsamości zarządzanej przypisanej do systemu i udzieleniu dostępu do Key Vault, zaktualizuj konto wsadowe przy użyciu `{Key Identifier}` adresu URL w obszarze `keyVaultProperties` parametr. Ustaw również **encryption_key_source** jako `Microsoft.KeyVault` .

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_source Microsoft.KeyVault \
    --encryption_key_identifier {YourKeyIdentifier} 
```

## <a name="create-a-batch-account-with-user-assigned-managed-identity-and-customer-managed-keys"></a>Tworzenie konta w usłudze Batch przy użyciu tożsamości zarządzanej przypisanej przez użytkownika i kluczy zarządzanych przez klienta

Za pomocą klienta usługi Batch Management można utworzyć konto usługi Batch, które będzie miało zarządzane przez użytkownika Zarządzanie tożsamościami i kluczami zarządzanymi przez klienta.

```c#
EncryptionProperties encryptionProperties = new EncryptionProperties()
{
    KeySource = KeySource.MicrosoftKeyVault,
    KeyVaultProperties = new KeyVaultProperties()
    {
        KeyIdentifier = "Your Key Azure Resource Manager Resource ID"
    }
};

BatchAccountIdentity identity = new BatchAccountIdentity()
{
    Type = ResourceIdentityType.UserAssigned,
    UserAssignedIdentities = new Dictionary<string, BatchAccountIdentityUserAssignedIdentitiesValue>
    {
            ["Your Identity Azure Resource Manager ResourceId"] = new BatchAccountIdentityUserAssignedIdentitiesValue()
    }
};
var parameters = new BatchAccountCreateParameters(TestConfiguration.ManagementRegion, encryption:encryptionProperties, identity: identity);

var account = await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount", parameters); 
```

## <a name="update-the-customer-managed-key-version"></a>Aktualizowanie wersji klucza zarządzanego przez klienta

Podczas tworzenia nowej wersji klucza należy zaktualizować konto w usłudze Batch, aby używało nowej wersji. Wykonaj następujące kroki:

1. Przejdź do konta w usłudze Batch w Azure Portal i Wyświetl ustawienia szyfrowania.
2. Wprowadź identyfikator URI dla nowej wersji klucza. Alternatywnie możesz wybrać Key Vault i ponownie klucz, aby zaktualizować wersję.
3. Zapisz zmiany.

Możesz również użyć interfejsu wiersza polecenia platformy Azure, aby zaktualizować wersję.

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourKeyIdentifierWithNewVersion} 
```

## <a name="use-a-different-key-for-batch-encryption"></a>Użyj innego klucza do szyfrowania wsadowego

Aby zmienić klucz używany do szyfrowania wsadowego, wykonaj następujące czynności:

1. Przejdź do konta w usłudze Batch i Wyświetl ustawienia szyfrowania.
2. Wprowadź identyfikator URI dla nowego klucza. Alternatywnie można wybrać Key Vault i wybrać nowy klucz.
3. Zapisz zmiany.

Możesz również użyć interfejsu wiersza polecenia platformy Azure, aby użyć innego klucza.

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourNewKeyIdentifier} 
```

## <a name="frequently-asked-questions"></a>Często zadawane pytania

- **Czy dla istniejących kont usługi Batch są obsługiwane klucze zarządzane przez klienta?** Nie. Klucze zarządzane przez klienta są obsługiwane tylko w przypadku nowych kont usługi Batch.
- **Czy mogę wybrać rozmiary kluczy RSA większe niż 2048 bitów?** Tak, `3072` obsługiwane są również rozmiary kluczy RSA i `4096` bity.
- **Jakie operacje są dostępne po odwołaniu klucza zarządzanego przez klienta?** Jedyną dozwoloną operacją jest usunięcie konta, jeśli partia utraci dostęp do klucza zarządzanego przez klienta.
- **Jak przywrócić dostęp do konta w usłudze Batch w przypadku przypadkowego usunięcia klucza Key Vault?** Ponieważ ochrona przed przeczyszczeniem i usuwanie trwałe są włączone, można przywrócić istniejące klucze. Aby uzyskać więcej informacji, zobacz [odzyskiwanie Azure Key Vault](../key-vault/general/key-vault-recovery.md).
- **Czy można wyłączyć klucze zarządzane przez klienta?** W dowolnym momencie możesz ustawić typ szyfrowania konta wsadowego z powrotem na "klucz zarządzany przez firmę Microsoft". Następnie możesz usunąć lub zmienić klucz.
- **Jak mogę obrócić moje klucze?** Klucze zarządzane przez klienta nie są automatycznie obracane. Aby obrócić klucz, zaktualizuj identyfikator klucza, z którym jest skojarzone konto.
- **Czy po przywróceniu dostępu przez program do czasu ponownego działania konta usługi Batch?** Ponowne udostępnienie konta po przywróceniu dostępu może potrwać do 10 minut.
- **Czy konto usługa Batch jest niedostępna, co się dzieje z zasobami?** Wszystkie pule uruchomione w przypadku utraty dostępu do kluczy zarządzanych przez klienta będą nadal działać. Jednak węzły przechodzą w stan niedostępności, a zadania przestaną działać (i będą ponownie kolejkowane). Po przywróceniu dostępu węzły staną się znów dostępne i zadania zostaną ponownie uruchomione.
- **Czy ten mechanizm szyfrowania ma zastosowanie do dysków maszyn wirtualnych w puli wsadowej?** Nie. W przypadku pul konfiguracji usługi w chmurze nie jest stosowane szyfrowanie dla systemu operacyjnego i dysku tymczasowego. W przypadku pul konfiguracji maszyny wirtualnej system operacyjny i wszystkie określone dyski danych zostaną domyślnie zaszyfrowane za pomocą klucza zarządzanego platformy firmy Microsoft. Obecnie nie można określić własnego klucza dla tych dysków. Aby zaszyfrować tymczasowy dysk maszyn wirtualnych dla puli usługi Batch przy użyciu klucza zarządzanego przez platformę Microsoft, należy włączyć właściwość [diskEncryptionConfiguration](/rest/api/batchservice/pool/add#diskencryptionconfiguration) w puli [konfiguracji maszyny wirtualnej](/rest/api/batchservice/pool/add#virtualmachineconfiguration) . W przypadku środowisk o wysokim poziomie dostępności zalecamy włączenie tymczasowego szyfrowania dysków i uniknięcie przechowywania poufnych danych na dyskach systemu operacyjnego i danych. Aby uzyskać więcej informacji, zobacz [Tworzenie puli z włączonym szyfrowaniem dysku](./disk-encryption.md)
- **Czy tożsamość zarządzana przypisana przez system na koncie w usłudze Batch jest dostępna w węzłach obliczeniowych?** Nie. Tożsamość zarządzana przypisana przez system jest obecnie używana tylko do uzyskiwania dostępu do Azure Key Vault dla klucza zarządzanego przez klienta.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [najlepszych rozwiązaniach dotyczących zabezpieczeń w Azure Batch](security-best-practices.md).
- Dowiedz się więcej o[Azure Key Vault](../key-vault/general/basic-concepts.md).
