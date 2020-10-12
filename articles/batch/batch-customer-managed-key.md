---
title: Skonfiguruj klucze zarządzane przez klienta dla konta Azure Batch przy użyciu tożsamości Azure Key Vault i zarządzanej
description: Dowiedz się, jak szyfrować dane wsadowe przy użyciu kluczy
author: pkshultz
ms.topic: how-to
ms.date: 07/17/2020
ms.author: peshultz
ms.openlocfilehash: 35780f915247e88a5de093594b653ddcebdfb06b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89008883"
---
# <a name="configure-customer-managed-keys-for-your-azure-batch-account-with-azure-key-vault-and-managed-identity"></a>Skonfiguruj klucze zarządzane przez klienta dla konta Azure Batch przy użyciu tożsamości Azure Key Vault i zarządzanej

Domyślnie Azure Batch używa kluczy zarządzanych przez platformę do szyfrowania wszystkich danych klienta przechowywanych w usłudze Azure Batch, takich jak certyfikaty, zadania/metadane zadania. Opcjonalnie możesz użyć własnych kluczy, takich jak klucze zarządzane przez klienta, aby szyfrować dane przechowywane w Azure Batch.

Klucze, które należy podać, muszą być generowane w [Azure Key Vault](../key-vault/general/basic-concepts.md), a konta usługi Batch, które chcesz skonfigurować przy użyciu kluczy zarządzanych przez klienta, muszą być włączone przy użyciu [tożsamości zarządzanej platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).

> [!IMPORTANT]
> Obsługa kluczy zarządzanych przez klienta w Azure Batch jest obecnie dostępna w publicznej wersji zapoznawczej dla Europy Zachodniej, Europa Północna, Szwajcaria Północna, środkowe stany USA, Południowo-środkowe stany USA, zachodnie stany USA, Wschodnie stany USA, Wschodnie stany USA 2, zachodnie stany USA 2, US Gov Wirginia i US Gov Arizona.
> Ta wersja zapoznawcza nie jest objęta umową dotyczącą poziomu usług i nie zalecamy korzystania z niej w przypadku obciążeń produkcyjnych. Niektóre funkcje mogą być nieobsługiwane lub ograniczone.
> Aby uzyskać więcej informacji, zobacz [Uzupełniające warunki korzystania z wersji zapoznawczych platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-batch-account-with-system-assigned-managed-identity"></a>Tworzenie konta w usłudze Batch z tożsamością zarządzaną przypisaną przez system

### <a name="azure-portal"></a>Azure Portal

W [Azure Portal](https://portal.azure.com/)podczas tworzenia kont wsadowych wybierz pozycję **system przypisany** w typie tożsamości na karcie **Zaawansowane** .

![Nowe konto wsadowe z typem tożsamości przypisanym do systemu](./media/batch-customer-managed-key/create-batch-account.png)

Po utworzeniu konta można znaleźć unikatowy identyfikator GUID w polu **Identyfikator podmiotu zabezpieczeń** w sekcji **Właściwości** . Zostanie wyświetlony **Typ tożsamości** `SystemAssigned` .

![Unikatowy identyfikator GUID w polu identyfikatora podmiotu zabezpieczeń tożsamości](./media/batch-customer-managed-key/linked-batch-principal.png)
 
### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Podczas tworzenia nowego konta usługi Batch należy określić `SystemAssigned` wartość `--identity` parametru.

```powershell
resourceGroupName='myResourceGroup'
accountName='mybatchaccount'

az batch account create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' \
    --identity 'SystemAssigned'
```

Po utworzeniu konta możesz sprawdzić, czy na tym koncie jest włączona tożsamość zarządzana przypisana przez system. Należy pamiętać, że należy zwrócić uwagę `PrincipalId` , ponieważ ta wartość będzie konieczna do udzielenia temu kontu wsadowemu dostępu do Key Vault.

```powershell
az batch account show \
    -n $accountName \
    -g $resourceGroupName \
    --query identity
```

> [!NOTE]
> Tożsamość zarządzana przypisana przez system utworzona na koncie usługi Batch jest używana tylko do pobierania kluczy zarządzanych przez klienta z Key Vault. Ta tożsamość nie jest dostępna w pulach partii.

## <a name="configure-your-azure-key-vault-instance"></a>Skonfiguruj wystąpienie Azure Key Vault

### <a name="create-an-azure-key-vault"></a>Tworzenie usługi Azure Key Vault

Podczas tworzenia wystąpienia Azure Key Vault z kluczami zarządzanymi przez klienta dla Azure Batch upewnij się, że włączono ochronę **nietrwałego usuwania** i **przeczyszczania** .

![Ekran tworzenia Key Vault](./media/batch-customer-managed-key/create-key-vault.png)

### <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Dodawanie zasad dostępu do wystąpienia Azure Key Vault

W Azure Portal po utworzeniu Key Vault w obszarze **zasady dostępu** w obszarze **ustawienie**Dodaj dostęp do konta usługi Batch przy użyciu tożsamości zarządzanej. W obszarze **uprawnienia klucza**wybierz pozycję **Pobierz**, **Zawijaj klucz** i **Odpakuj klucz**. 

![Dodawanie zasad dostępu](./media/batch-customer-managed-key/key-permissions.png)

W polu **Wybierz** w obszarze **podmiot zabezpieczeń**Wypełnij `principalId` wcześniej pobrane lub nazwę konta zadania wsadowego.

![Wprowadź principalId](./media/batch-customer-managed-key/principal-id.png)

### <a name="generate-a-key-in-azure-key-vault"></a>Wygeneruj klucz w Azure Key Vault

W Azure Portal przejdź do wystąpienia Key Vault w sekcji **klucz** , wybierz pozycję **Generuj/Importuj**. Wybierz **Typ klucza** , który ma być `RSA` i **Rozmiar klucza RSA** , aby mieć co najmniej `2048` bity. `EC` typy kluczy nie są obecnie obsługiwane jako klucz zarządzany przez klienta na koncie wsadowym.

![Utwórz klucz](./media/batch-customer-managed-key/create-key.png)

Po utworzeniu klucza kliknij nowo utworzony klucz i bieżącą wersję, skopiuj **Identyfikator klucza** w sekcji **Właściwości** .  Upewnij się, że w obszarze **dozwolone operacje**jest zaznaczone pole **Zawijanie klucza** i **odwinięcie klucza** .

## <a name="enable-customer-managed-keys-on-azure-batch-account"></a>Włącz klucze zarządzane przez klienta na koncie Azure Batch

### <a name="azure-portal"></a>Azure Portal

W [Azure Portal](https://portal.azure.com/)przejdź do strony konto w usłudze Batch. W sekcji **szyfrowanie** Włącz **klucz zarządzany przez klienta**. Można bezpośrednio użyć identyfikatora klucza lub wybrać Magazyn kluczy, a następnie kliknąć pozycję **Wybierz magazyn kluczy i klucz**.

![W obszarze Szyfrowanie Włącz klucz zarządzany przez klienta](./media/batch-customer-managed-key/encryption-page.png)

### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

Po utworzeniu konta w usłudze Batch przy użyciu tożsamości zarządzanej przypisanej do systemu i udzieleniu dostępu do Key Vault, zaktualizuj konto wsadowe przy użyciu `{Key Identifier}` adresu URL w obszarze `keyVaultProperties` parametr. Ustaw również **encryption_key_source** jako `Microsoft.KeyVault` .

```powershell
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_source Microsoft.KeyVault \
    --encryption_key_identifier {YourKeyIdentifier} 
```

## <a name="update-the-customer-managed-key-version"></a>Aktualizowanie wersji klucza zarządzanego przez klienta

Podczas tworzenia nowej wersji klucza należy zaktualizować konto w usłudze Batch, aby używało nowej wersji. Wykonaj następujące kroki:

1. Przejdź do konta w usłudze Batch w Azure Portal i Wyświetl ustawienia szyfrowania.
2. Wprowadź identyfikator URI dla nowej wersji klucza. Alternatywnie można wybrać Magazyn kluczy i ponownie klucz, aby zaktualizować wersję.
3. Zapisz zmiany.

Możesz również użyć interfejsu wiersza polecenia platformy Azure, aby zaktualizować wersję.

```powershell
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourKeyIdentifierWithNewVersion} 
```
## <a name="use-a-different-key-for-batch-encryption"></a>Użyj innego klucza do szyfrowania wsadowego

Aby zmienić klucz używany do szyfrowania wsadowego, wykonaj następujące czynności:

1. Przejdź do konta w usłudze Batch i Wyświetl ustawienia szyfrowania.
2. Wprowadź identyfikator URI dla nowego klucza. Alternatywnie można wybrać Magazyn kluczy i wybrać nowy klucz.
3. Zapisz zmiany.

Możesz również użyć interfejsu wiersza polecenia platformy Azure, aby użyć innego klucza.

```powershell
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourNewKeyIdentifier} 
```
## <a name="frequently-asked-questions"></a>Często zadawane pytania
  * **Czy dla istniejących kont usługi Batch są obsługiwane klucze zarządzane przez klienta?** Nie. Klucze zarządzane przez klienta są obsługiwane tylko w przypadku nowych kont usługi Batch.
  * **Czy mogę wybrać rozmiary kluczy RSA większe niż 2048 bitów?** Tak, `3072` obsługiwane są również rozmiary kluczy RSA i `4096` bity.
  * **Jakie operacje są dostępne po odwołaniu klucza zarządzanego przez klienta?** Jedyną dozwoloną operacją jest usunięcie konta, jeśli partia utraci dostęp do klucza zarządzanego przez klienta.
  * **Jak przywrócić dostęp do konta w usłudze Batch w przypadku przypadkowego usunięcia klucza Key Vault?** Ponieważ ochrona przed przeczyszczeniem i usuwanie trwałe są włączone, można przywrócić istniejące klucze. Aby uzyskać więcej informacji, zobacz [odzyskiwanie Azure Key Vault](../key-vault/general/soft-delete-cli.md#recovering-a-key-vault).
  * **Czy można wyłączyć klucze zarządzane przez klienta?** W dowolnym momencie możesz ustawić typ szyfrowania konta wsadowego z powrotem na "klucz zarządzany przez firmę Microsoft". Następnie możesz usunąć lub zmienić klucz.
  * **Jak mogę obrócić moje klucze?** Klucze zarządzane przez klienta nie są automatycznie obracane. Aby obrócić klucz, zaktualizuj identyfikator klucza, z którym jest skojarzone konto.
  * **Czy po przywróceniu dostępu przez program do czasu ponownego działania konta usługi Batch?** Ponowne udostępnienie konta po przywróceniu dostępu może potrwać do 10 minut.
  * **Czy konto usługa Batch jest niedostępna, co się dzieje z zasobami?** Wszystkie pule uruchomione w przypadku utraty dostępu do kluczy zarządzanych przez klienta będą nadal działać. Jednak węzły przechodzą w stan niedostępności, a zadania przestaną działać (i będą ponownie kolejkowane). Po przywróceniu dostępu węzły staną się znów dostępne i zadania zostaną ponownie uruchomione.
  * **Czy ten mechanizm szyfrowania ma zastosowanie do dysków maszyn wirtualnych w puli wsadowej?** Nie. W przypadku pul konfiguracji usługi w chmurze nie jest stosowane szyfrowanie dla systemu operacyjnego i dysku tymczasowego. W przypadku pul konfiguracji maszyny wirtualnej system operacyjny i wszystkie określone dyski danych zostaną domyślnie zaszyfrowane za pomocą klucza zarządzanego platformy firmy Microsoft. Obecnie nie można określić własnego klucza dla tych dysków. Aby zaszyfrować tymczasowy dysk maszyn wirtualnych dla puli usługi Batch przy użyciu klucza zarządzanego przez platformę Microsoft, należy włączyć właściwość [diskEncryptionConfiguration](/rest/api/batchservice/pool/add#diskencryptionconfiguration) w puli [konfiguracji maszyny wirtualnej](/rest/api/batchservice/pool/add#virtualmachineconfiguration) . W przypadku środowisk o wysokim poziomie dostępności zalecamy włączenie tymczasowego szyfrowania dysków i uniknięcie przechowywania poufnych danych na dyskach systemu operacyjnego i danych. Aby uzyskać więcej informacji, zobacz [Tworzenie puli z włączonym szyfrowaniem dysku](./disk-encryption.md)
  * **Czy tożsamość zarządzana przypisana przez system na koncie w usłudze Batch jest dostępna w węzłach obliczeniowych?** Nie. Ta tożsamość zarządzana jest obecnie używana tylko do uzyskiwania dostępu do Azure Key Vault dla klucza zarządzanego przez klienta.
  
