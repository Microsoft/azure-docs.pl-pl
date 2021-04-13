---
title: Klucz zarządzany przez klienta usługi Azure Monitor
description: Informacje i kroki służące do konfigurowania klucza zarządzanego przez klienta w celu szyfrowania danych w obszarach roboczych Log Analytics przy użyciu klucza Azure Key Vault.
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 01/10/2021
ms.openlocfilehash: 4033421095ead47e2bd1e97c4f2f42672644d7df
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364859"
---
# <a name="azure-monitor-customer-managed-key"></a>Klucz zarządzany przez klienta usługi Azure Monitor 

Dane w Azure Monitor są szyfrowane za pomocą kluczy zarządzanych przez firmę Microsoft. Możesz użyć własnego klucza szyfrowania do ochrony danych i zapisanych zapytań w Twoich obszarach roboczych. W przypadku określenia klucza zarządzanego przez klienta ten klucz jest używany do ochrony i kontroli dostępu do danych, a po skonfigurowaniu wszystkie dane wysyłane do obszarów roboczych są szyfrowane za pomocą klucza Azure Key Vault. Klucze zarządzane przez klienta zapewniają większą elastyczność zarządzania kontrolą dostępu.

Zalecamy przejrzenie [ograniczeń i ograniczeń](#limitationsandconstraints) poniżej przed rozpoczęciem konfiguracji.

## <a name="customer-managed-key-overview"></a>Informacje o kluczu zarządzanym przez klienta

[Szyfrowanie w spoczynku](../../security/fundamentals/encryption-atrest.md) to wspólne wymagania w zakresie ochrony prywatności i bezpieczeństwa w organizacjach. Możesz pozwolić, aby platforma Azure całkowicie zarządzała szyfrowaniem w stanie spoczynku, podczas gdy masz różne opcje, aby dokładnie zarządzać szyfrowaniem i kluczami szyfrowania.

Azure Monitor gwarantuje, że wszystkie dane i zapisane zapytania są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft (MMK). Azure Monitor udostępnia również opcję szyfrowania przy użyciu własnego klucza, który jest przechowywany w [Azure Key Vault](../../key-vault/general/overview.md), co umożliwia kontrolowanie dostępu do danych w dowolnym momencie. Azure Monitor korzystania z szyfrowania jest taka sama jak w sposobie działania [szyfrowania usługi Azure Storage](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption) .

Klucz zarządzany przez klienta jest dostarczany w [dedykowanych klastrach](./logs-dedicated-clusters.md) , zapewniając wyższy poziom ochrony i kontrolę. Dane pozyskane do dedykowanych klastrów są szyfrowane dwa razy — raz na poziomie usługi przy użyciu kluczy zarządzanych przez firmę Microsoft lub kluczy zarządzanych przez klienta, a raz na poziomie infrastruktury przy użyciu dwóch różnych algorytmów szyfrowania i dwóch różnych kluczy. [Szyfrowanie podwójne](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) chroni przed scenariuszem, w którym można złamać jeden z algorytmów szyfrowania lub kluczy. W takim przypadku dodatkowa warstwa szyfrowania nadal chroni dane. Dedykowany klaster umożliwia również ochronę danych za pomocą kontrolki [skrytki](#customer-lockbox-preview) .

Dane pozyskane w ciągu ostatnich 14 dni również są przechowywane w pamięci podręcznej (dysk SSD) w celu wydajnej operacji aparatu zapytań. Te dane pozostają zaszyfrowane przy użyciu kluczy firmy Microsoft niezależnie od konfiguracji klucza zarządzanego przez klienta, ale kontrola nad danymi SSD jest zgodna z [odwołaniem klucza](#key-revocation). Pracujemy nad zaszyfrowaniem danych SSD z kluczem zarządzanym przez klienta w pierwszej połowie 2021.

Log Analytics dedykowane klastry używają [modelu cenowego](./logs-dedicated-clusters.md#cluster-pricing-model) rezerwacji pojemności, rozpoczynając od 1000 GB/dzień.

## <a name="how-customer-managed-key-works-in-azure-monitor"></a>Jak działa klucz zarządzany przez klienta w Azure Monitor

Azure Monitor używa tożsamości zarządzanej w celu udzielenia dostępu do Azure Key Vault. Tożsamość klastra Log Analytics jest obsługiwana na poziomie klastra. Aby umożliwić ochronę klucza zarządzanego przez klienta w wielu obszarach roboczych, nowy zasób *klastra* log Analytics pełni rolę pośredniego połączenia tożsamości między Key Vault i obszarami roboczymi log Analytics. Magazyn klastra używa zarządzanej tożsamości \' skojarzonej z zasobem *klastra* w celu uwierzytelnienia w Azure Key Vault za pośrednictwem Azure Active Directory. 

Po skonfigurowaniu klucza zarządzanego przez klienta nowe pozyskiwane dane do obszarów roboczych połączonych z dedykowanym klastrem są szyfrowane przy użyciu klucza. W każdej chwili można odłączyć obszary robocze z klastra. Nowe dane są następnie pobierane do magazynu Log Analytics i szyfrowane za pomocą klucza firmy Microsoft, podczas gdy można bezproblemowo badać nowe i stare dane.

> [!IMPORTANT]
> Możliwość zarządzania kluczem zarządzanym przez klienta jest regionalna. Azure Key Vault, klaster i połączone Log Analytics obszary robocze muszą znajdować się w tym samym regionie, ale mogą znajdować się w różnych subskrypcjach.

![Informacje o kluczu zarządzanym przez klienta](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Log Analytics zasobu *klastra* mającego zarządzaną tożsamość z uprawnieniami do Key Vault — tożsamość jest propagowana do underlay dedykowanego log Analytics magazynu klastra
3. Dedykowany klaster Log Analytics
4. Obszary robocze połączone z zasobem *klastra* 

### <a name="encryption-keys-operation"></a>Operacja kluczy szyfrowania

Istnieją trzy typy kluczy, które wiążą się z szyfrowaniem danych magazynu:

- Klucz szyfrowania klucza **KEK** (klucz zarządzany przez klienta)
- Klucz szyfrowania konta **AEK**
-  Klucz szyfrowania danych

Mają zastosowanie następujące zasady:

- Konta magazynu klastra Log Analytics generują unikatowy klucz szyfrowania dla każdego konta magazynu, który jest znany jako AEK.
- AEK jest używany do wyprowadzania DEKs, które są kluczami, które są używane do szyfrowania poszczególnych bloków danych zapisywana na dysku.
- W przypadku konfigurowania klucza w Key Vault i odwoływania się do niego w klastrze usługa Azure Storage wysyła żądania do Azure Key Vault, aby zawijać i deAEKać dane w celu szyfrowania i odszyfrowywania danych.
- Twoje KEK nigdy nie opuściją Key Vault.
- Usługa Azure Storage korzysta z zarządzanej tożsamości skojarzonej z zasobem *klastra* w celu uwierzytelniania i dostępu do Azure Key Vault za pośrednictwem Azure Active Directory.

### <a name="customer-managed-key-provisioning-steps"></a>Procedura inicjowania obsługi klucza Customer-Managed

1. Tworzenie Azure Key Vault i przechowywanie klucza
1. Tworzenie klastra
1. Przyznawanie uprawnień do Key Vault
1. Aktualizowanie klastra z informacjami o identyfikatorze klucza
1. Łączenie Log Analytics obszarów roboczych

Konfiguracja klucza zarządzanego przez klienta nie jest obsługiwana w Azure Portal obecnie Inicjowanie obsługi administracyjnej może odbywać się za pośrednictwem [programu PowerShell](/powershell/module/az.operationalinsights/), [interfejsu wiersza polecenia](/cli/azure/monitor/log-analytics) lub żądań [rest](/rest/api/loganalytics/) .

### <a name="asynchronous-operations-and-status-check"></a>Operacje asynchroniczne i sprawdzanie stanu

Niektóre kroki konfiguracji działają asynchronicznie, ponieważ nie mogą być szybko wykonywane. `status`Odpowiedź w odpowiedzi może być jedną z następujących wartości: "InProgress", "aktualizując", "Usuwanie", "powodzenie" lub "zakończony niepowodzeniem" z kodem błędu.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Nie dotyczy

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Nie dotyczy

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Nie dotyczy

# <a name="rest"></a>[REST](#tab/rest)

W przypadku korzystania z funkcji REST odpowiedź początkowo zwraca kod stanu HTTP 202 (zaakceptowane) i nagłówek przy użyciu właściwości *Azure-AsyncOperation* :
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

Stan operacji asynchronicznej można sprawdzić, wysyłając żądanie GET do punktu końcowego w nagłówku *Azure-AsyncOperation* :
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

---

## <a name="storing-encryption-key-kek"></a>Przechowywanie klucza szyfrowania (KEK)

Utwórz lub użyj Azure Key Vault już do wygenerowania lub zaimportuj klucz, który ma być używany do szyfrowania danych. Azure Key Vault należy skonfigurować jako możliwy do odzyskania, aby chronić klucz i dostęp do danych w Azure Monitor. Możesz sprawdzić tę konfigurację w obszarze właściwości w Key Vault, należy włączyć zarówno funkcję *usuwania nietrwałego* , jak i *przeczyszczania* .

![Ustawienia ochrony usuwania nietrwałego i przeczyszczania](media/customer-managed-keys/soft-purge-protection.png)

Te ustawienia można aktualizować w Key Vault za pomocą interfejsu wiersza polecenia i programu PowerShell:

- [Usuwanie nietrwałe](../../key-vault/general/soft-delete-overview.md)
- [Przeczyść zabezpieczenia ochrony](../../key-vault/general/soft-delete-overview.md#purge-protection) przed wymuszeniem usunięcia wpisu tajnego/magazynu nawet po usunięciu nietrwałego

## <a name="create-cluster"></a>Tworzenie klastra

Klastry obsługują dwa [zarządzane typy tożsamości](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types): przypisane do systemu i przypisane przez użytkownika, podczas gdy jedna tożsamość może być zdefiniowana w klastrze w zależności od danego scenariusza. 
- Tożsamość zarządzana przypisana przez system jest prostsza i generowana automatycznie przy użyciu tworzenia klastra, gdy tożsamość `type` jest ustawiona na wartość "*SystemAssigned*". Tej tożsamości można użyć później w celu udzielenia dostępu do magazynu Key Vault na potrzeby operacji zawijania i rozwinięcia. 
  
  Ustawienia tożsamości w klastrze dla tożsamości zarządzanej przypisanej do systemu
  ```json
  {
    "identity": {
      "type": "SystemAssigned"
      }
  }
  ```

- Jeśli chcesz skonfigurować klucz zarządzany przez klienta podczas tworzenia klastra, musisz wcześniej uzyskać Key Vault tożsamość przypisanej do klucza i użytkownika, a następnie utworzyć klaster z tymi ustawieniami: tożsamość `type` jako "*UserAssigned*" `UserAssignedIdentities` z *identyfikatorem zasobu* tożsamości.

  Ustawienia tożsamości w klastrze dla tożsamości zarządzanej przypisanej przez użytkownika
  ```json
  {
  "identity": {
  "type": "UserAssigned",
    "userAssignedIdentities": {
      "subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.ManagedIdentity/UserAssignedIdentities/<cluster-assigned-managed-identity>"
      }
  }
  ```

> [!IMPORTANT]
> Nie można użyć tożsamości zarządzanej przypisanej przez użytkownika, jeśli Key Vault znajduje się w Private-Link (vNet). W tym scenariuszu można użyć zarządzanej tożsamości przypisanej do systemu.

Postępuj zgodnie z procedurą przedstawioną w [artykule dedykowane klastry](./logs-dedicated-clusters.md#creating-a-cluster). 

## <a name="grant-key-vault-permissions"></a>Przyznawanie uprawnień Key Vault

Utwórz zasady dostępu w Key Vault, aby udzielać uprawnień do klastra. Te uprawnienia są używane przez magazyn underlay Azure Monitor. Otwórz Key Vault w Azure Portal, a następnie kliknij pozycję *"zasady dostępu"* i *"+ Dodaj zasady dostępu"* , aby utworzyć zasady z następującymi ustawieniami:

- Uprawnienia klucza: wybierz opcję *"Pobierz"*, *"Zawijanie klucza"* i *"Cofnij Zawijanie klucza"*.
- Wybierz podmiot zabezpieczeń: w zależności od typu tożsamości używanego w klastrze (tożsamość zarządzana przez system lub użytkownika) wprowadź nazwę klastra lub Identyfikator podmiotu zabezpieczeń klastra dla tożsamości zarządzanej przypisanej do systemu lub nazwę tożsamości zarządzanej przypisanej przez użytkownika.

![Przyznawanie uprawnień Key Vault](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

Uprawnienie *Get* jest wymagane do sprawdzenia, czy Key Vault jest skonfigurowany jako możliwy do odzyskania w celu ochrony klucza i dostępu do danych Azure monitor.

## <a name="update-cluster-with-key-identifier-details"></a>Aktualizuj klaster przy użyciu szczegółów identyfikatora klucza

Wszystkie operacje w klastrze wymagają uprawnienia do `Microsoft.OperationalInsights/clusters/write` akcji. To uprawnienie można udzielić za pośrednictwem właściciela lub współautora, który zawiera `*/write` akcję, lub za pośrednictwem roli współautor log Analytics, która zawiera `Microsoft.OperationalInsights/*` akcję.

Ten krok umożliwia zaktualizowanie magazynu Azure Monitor przy użyciu klucza i wersji, który ma być używany do szyfrowania danych. Po zaktualizowaniu nowy klucz jest używany do zawijania i rozwinięcia klucza magazynu (AEK).

Wybierz bieżącą wersję klucza w Azure Key Vault, aby uzyskać szczegółowe informacje o identyfikatorze klucza.

![Przyznawanie uprawnień Key Vault](media/customer-managed-keys/key-identifier-8bit.png)

Zaktualizuj KeyVaultProperties w klastrze z informacjami o identyfikatorze klucza.

>[!NOTE]
>Rotacja kluczy obsługuje dwa tryby: funkcja autorotacji lub jawnej wersji klucza. Aby określić najlepsze rozwiązanie, zobacz [rotacja kluczy](#key-rotation) .

Operacja jest asynchroniczna i może chwilę potrwać.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Nie dotyczy

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --key-name "key-name" --key-vault-uri "key-uri" --key-version "key-version"
```
# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -KeyVaultUri "key-uri" -KeyName "key-name" -KeyVersion "key-version"
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/cluster-name?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
  },
  "sku": {
    "name": "CapacityReservation",
    "capacity": 1000
  }
}
```

**Odpowiedź**

Wykonanie propagacji klucza trwa kilka minut. Stan aktualizacji można sprawdzić na dwa sposoby:
1. Skopiuj wartość Azure-AsyncOperation adresu URL z odpowiedzi i postępuj zgodnie z [testem stanu operacji asynchronicznych](#asynchronous-operations-and-status-check).
2. Wyślij żądanie GET w klastrze i sprawdź właściwości *KeyVaultProperties* . Ostatnio zaktualizowany klucz powinien zwrócić odpowiedź.

Odpowiedź na żądanie uzyskania żądania powinna wyglądać następująco po zakończeniu aktualizacji klucza: 202 (zaakceptowane) i nagłówek
```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principle-id"
    },
  "sku": {
    "name": "capacityReservation",
    "capacity": 1000,
    "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
    },
  "properties": {
    "keyVaultProperties": {
      "keyVaultUri": "https://key-vault-name.vault.azure.net",
      "kyName": "key-name",
      "keyVersion": "current-version"
      },
    "provisioningState": "Succeeded",
    "billingType": "cluster",
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

---

## <a name="link-workspace-to-cluster"></a>Połącz obszar roboczy z klastrem

> [!IMPORTANT]
> Ten krok należy wykonać dopiero po zakończeniu aprowizacji klastra Log Analytics. Jeśli połączysz obszary robocze i pozyskasz dane przed zainicjowaniem obsługi, pozyskiwane dane zostaną usunięte i nie będzie można ich odzyskać.

Aby można było wykonać tę operację, musisz mieć uprawnienia "Write" do obszaru roboczego i klastra, co obejmuje `Microsoft.OperationalInsights/workspaces/write` i `Microsoft.OperationalInsights/clusters/write` .

Postępuj zgodnie z procedurą przedstawioną w [artykule dedykowane klastry](./logs-dedicated-clusters.md#link-a-workspace-to-cluster).

## <a name="key-revocation"></a>Odwoływanie klucza

> [!IMPORTANT]
> - Zalecanym sposobem odwołania dostępu do danych jest wyłączenie klucza lub usunięcie zasad dostępu w Key Vault.
> - Ustawienie klastra na wartość `identity` `type` "none" spowoduje również odwołanie dostępu do danych, ale takie podejście nie jest zalecane, ponieważ nie można cofnąć odwołania podczas ponownego wystawiania `identity` w klastrze bez otwierania żądania obsługi.

Magazyn klastra zawsze będzie uwzględniał zmiany w uprawnieniach klucza w ciągu godziny lub wcześniej, a magazyn stanie się niedostępny. Wszelkie nowe dane pozyskiwane w obszarach roboczych połączonych z klastrem zostaną usunięte i nie będą odzyskiwalne. dane staną się niedostępne i zapytania w tych obszarach roboczych zakończą się niepowodzeniem. Wcześniej pozyskiwane dane pozostają w magazynie, dopóki klaster i Twoje obszary robocze nie zostaną usunięte. Dane niedostępne podlegają zasadom przechowywania danych i zostaną usunięte po osiągnięciu okresu przechowywania. Dane odebrane w ciągu ostatnich 14 dni również są przechowywane w pamięci podręcznej (dysk SSD) w celu wydajnej operacji aparatu zapytań. Spowoduje to usunięcie operacji odwoływania klucza i stanie się niedostępna.

Magazyn klastra okresowo sprawdza Key Vault, aby próbować odszyfrować klucz szyfrowania, a po uzyskaniu dostępu do pozyskiwania danych i wykonywania zapytań w ciągu 30 minut.

## <a name="key-rotation"></a>Wymiana kluczy

Rotacja kluczy ma dwa tryby: 
- Funkcja autorotacji — w przypadku aktualizowania klastra przy użyciu ```"keyVaultProperties"``` Właściwości pomijania ```"keyVersion"``` lub ustawienia dla ```""``` magazynu autoamatically będzie używać najnowszych wersji.
- Aktualizacja jawnej wersji klucza — po zaktualizowaniu klastra i udostępnieniu wersji klucza we ```"keyVersion"``` właściwościach wszystkie nowe kluczowe wersje wymagają jawnej ```"keyVaultProperties"``` aktualizacji w klastrze, zobacz temat [Aktualizowanie klastra z identyfikatorem klucza szczegóły](#update-cluster-with-key-identifier-details). Jeśli wygenerujesz nową wersję klucza w Key Vault ale nie zaktualizujesz jej w klastrze, Log Analytics magazynu klastra będzie nadal korzystać z poprzedniego klucza. W przypadku wyłączenia lub usunięcia starego klucza przed zaktualizowaniem nowego klucza w klastrze zostanie wyświetlony stan [odwołania klucza](#key-revocation) .

Wszystkie dane pozostają dostępne po operacji rotacji kluczy, ponieważ dane zawsze są szyfrowane przy użyciu klucza szyfrowania konta (AEK), podczas gdy AEK jest teraz szyfrowany przy użyciu nowej wersji klucza szyfrowania kluczy (KEK) w Key Vault.

## <a name="customer-managed-key-for-saved-queries"></a>Klucz zarządzany przez klienta dla zapisanych zapytań

Język zapytań używany w Log Analytics jest wyraźny i może zawierać poufne informacje w komentarzach, które można dodać do zapytań lub w składni zapytania. Niektóre organizacje wymagają, aby te informacje były chronione w ramach zasad kluczy zarządzanych przez klienta, a wymagane jest zapisanie zaszyfrowanej kwerendy przy użyciu klucza. Azure Monitor umożliwia przechowywanie *zapisywanych* i *zarejestrowań zapytań dotyczących alertów* w postaci zaszyfrowanej przy użyciu klucza na własnym koncie magazynu po nawiązaniu połączenia z obszarem roboczym. 

> [!NOTE]
> Zapytania Log Analytics można zapisywać w różnych magazynach w zależności od użytego scenariusza. Zapytania pozostają zaszyfrowane za pomocą usługi Microsoft Key (MMK) w następujących scenariuszach niezależnie od konfiguracji klucza zarządzanego przez klienta: skoroszyty w Azure Monitor, pulpity nawigacyjne platformy Azure, aplikacja logiki platformy Azure, Azure Notebooks i elementy Runbook automatyzacji.

Gdy przeniesiesz własny magazyn (BYOS) i połączysz go z obszarem roboczym, usługa przekaże *zapisane zapytania wyszukiwania* i *alerty dziennika* do konta magazynu. Oznacza to, że można kontrolować konto magazynu i [zasady szyfrowania w trybie REST](../../storage/common/customer-managed-keys-overview.md) przy użyciu tego samego klucza, który jest używany do szyfrowania danych w klastrze log Analytics lub innego klucza. Użytkownik będzie jednak odpowiedzialny za koszty związane z tym kontem magazynu. 

**Zagadnienia przed ustawieniem klucza zarządzanego przez klienta dla zapytań**
* Musisz mieć uprawnienia do zapisu zarówno w obszarze roboczym, jak i koncie magazynu
* Upewnij się, że utworzono konto magazynu w tym samym regionie, w którym znajduje się obszar roboczy Log Analytics.
* *Zapisywanie wyszukiwań* w magazynie jest traktowane jako artefakty usługi i ich format może ulec zmianie
* Istniejące *wyszukiwania zapisu* zostaną usunięte z obszaru roboczego. Skopiuj i *Zapisz wymagane wyszukiwania* przed konfiguracją. *Zapisane wyniki wyszukiwania* można wyświetlić przy użyciu [programu PowerShell](/powershell/module/az.operationalinsights/get-azoperationalinsightssavedsearch) .
* Historia zapytania nie jest obsługiwana i nie będzie można zobaczyć uruchomionych zapytań
* Można połączyć jedno konto magazynu z obszarem roboczym na potrzeby zapisywania zapytań, ale można go użyć z zapytaniami *zapisane wyszukiwania* i *alerty dziennika* .
* Przypinanie do pulpitu nawigacyjnego nie jest obsługiwane

**Konfigurowanie BYOS na potrzeby zapytań zapisanych wyszukiwań**

Połącz konto magazynu w celu *wysłania zapytania* do obszaru roboczego — *zapisane zapytania wyszukiwania* są zapisywane na koncie magazynu. 

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Nie dotyczy

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type Query --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Query -StorageAccountIds $storageAccount.Id
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Query?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Query", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

---

Po zakończeniu konfiguracji wszystkie nowe *zapisane zapytania wyszukiwania* zostaną zapisane w magazynie.

**Konfigurowanie BYOS na potrzeby zapytań dotyczących alertów dziennika**

Łączenie konta magazynu w celu uzyskania *alertów* w obszarze roboczym — zapytania dotyczące *alertów dziennika* są zapisywane na koncie magazynu. 

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Nie dotyczy

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

```azurecli
$storageAccountId = '/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage name>'
az monitor log-analytics workspace linked-storage create --type ALerts --resource-group "resource-group-name" --workspace-name "workspace-name" --storage-accounts $storageAccountId
```

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

```powershell
$storageAccount.Id = Get-AzStorageAccount -ResourceGroupName "resource-group-name" -Name "storage-account-name"
New-AzOperationalInsightsLinkedStorageAccount -ResourceGroupName "resource-group-name" -WorkspaceName "workspace-name" -DataSourceType Alerts -StorageAccountIds $storageAccount.Id
```

# <a name="rest"></a>[REST](#tab/rest)

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>/linkedStorageAccounts/Alerts?api-version=2020-08-01
Authorization: Bearer <token> 
Content-type: application/json
 
{
  "properties": {
    "dataSourceType": "Alerts", 
    "storageAccountIds": 
    [
      "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"
    ]
  }
}
```

---

Po zakończeniu konfiguracji wszystkie nowe zapytania o alerty zostaną zapisane w magazynie.

## <a name="customer-lockbox-preview"></a>Skrytka klienta (wersja zapoznawcza)

Skrytka daje kontrolę, aby zatwierdzić lub odrzucić żądanie inżyniera firmy Microsoft w celu uzyskania dostępu do danych w trakcie żądania obsługi.

W Azure Monitor jest to formant dotyczący danych w obszarach roboczych połączonych z Log Analytics dedykowanym klastrem. Kontrolka skrytki ma zastosowanie do danych przechowywanych w Log Analytics dedykowanym klastrze, w którym są one izolowane na kontach magazynu klastra w ramach chronionej subskrypcji skrytki.  

Dowiedz się więcej [na temat Skrytka klienta Microsoft Azure](../../security/fundamentals/customer-lockbox-overview.md)

## <a name="customer-managed-key-operations"></a>Operacje na kluczu Customer-Managed

Klucz Customer-Managed jest udostępniany w dedykowanym klastrze i te operacje są określane w [dedykowanym artykule klastra](./logs-dedicated-clusters.md#change-cluster-properties)

- Pobierz wszystkie klastry w grupie zasobów  
- Pobierz wszystkie klastry w subskrypcji
- Aktualizowanie *rezerwacji pojemności* w klastrze
- Aktualizowanie *w* klastrze
- Odłączanie obszaru roboczego od klastra
- Usuwanie klastra

## <a name="limitations-and-constraints"></a>Limity i ograniczenia

- Maksymalna liczba klastrów na region i subskrypcja wynosi 2

- Maksymalna liczba obszarów roboczych, które mogą być połączone z klastrem, to 1000

- Możesz połączyć obszar roboczy z klastrem, a następnie odłączyć go od siebie. Liczba operacji linku obszaru roboczego w określonym obszarze roboczym jest ograniczona do 2 w okresie 30 dni.

- Szyfrowanie klucza zarządzanego przez klienta ma zastosowanie do nowo wprowadzonych danych po upływie czasu konfiguracji. Dane, które zostały wprowadzone przed rozpoczęciem konfiguracji, pozostają zaszyfrowane za pomocą klucza firmy Microsoft. Można wykonywać zapytania dotyczące danych pozyskiwanych przed bezproblemową konfiguracją klucza zarządzanego przez klienta i po nim.

- Azure Key Vault musi być skonfigurowany jako możliwy do odzyskania. Te właściwości nie są domyślnie włączone i należy je skonfigurować przy użyciu interfejsu wiersza polecenia lub programu PowerShell:<br>
  - [Usuwanie nietrwałe](../../key-vault/general/soft-delete-overview.md)
  - [Ochrona przed przeczyszczeniem](../../key-vault/general/soft-delete-overview.md#purge-protection) powinna być włączona, aby zabezpieczyć przed wymuszeniem usunięcia wpisu tajnego lub magazynu nawet po usunięciu nietrwałego.

- Klaster przeniesiony do innej grupy zasobów lub subskrypcji nie jest obecnie obsługiwany.

- Azure Key Vault, klaster i obszary robocze muszą znajdować się w tym samym regionie i w tej samej dzierżawie Azure Active Directory (Azure AD), ale mogą znajdować się w różnych subskrypcjach.

- Skrytka nie jest obecnie dostępna w Chinach. 

- [Podwójne szyfrowanie](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) jest konfigurowane automatycznie w przypadku klastrów utworzonych z października 2020 w obsługiwanych regionach. Można sprawdzić, czy klaster jest skonfigurowany do podwójnego szyfrowania, wysyłając żądanie GET w klastrze i obserwując, że `isDoubleEncryptionEnabled` wartość jest `true` w przypadku klastrów z włączonym podwójnym szyfrowaniem. 
  - Jeśli utworzysz klaster i wystąpi błąd "<regionu-Name> nie obsługuje podwójnego szyfrowania klastrów" ", można nadal utworzyć klaster bez podwójnego szyfrowania, dodając `"properties": {"isDoubleEncryptionEnabled": false}` w treści żądania Rest.
  - Ustawienia podwójnego szyfrowania nie można zmienić po utworzeniu klastra.

  - Jeśli klaster jest ustawiony przy użyciu tożsamości zarządzanej przypisanej przez użytkownika, ustawienie `UserAssignedIdentities` with `None` zawiesza klaster i uniemożliwia dostęp do danych, ale nie można cofnąć odwołania i aktywować klastra bez otwierania żądania obsługi. To ograniczenie jest ' zostało zastosowane do zarządzanej tożsamości przypisanej do systemu.

  - Nie można używać klucza zarządzanego przez klienta z tożsamością zarządzaną przez użytkownika, jeśli Key Vault znajduje się w Private-Link (vNet). W tym scenariuszu można użyć zarządzanej tożsamości przypisanej do systemu.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

- Zachowanie z dostępnością Key Vault
  - W normalnej operacji — pamięć podręczna magazynu AEK przez krótki okresy i powraca do Key Vault, aby okresowo wycofać.
    
  - Błędy połączeń przejściowych — usługa Storage obsługuje błędy przejściowe (przekroczenia limitu czasu, błędy połączeń, problemy z usługą DNS), dzięki czemu klucze mogą pozostać w pamięci podręcznej przez krótki czas i jest to zbyt małe Blips w dostępności. Możliwości zapytań i pozyskiwania kontynuują działanie bez przeszkód.
    
  - Lokacja na żywo — niedostępność przez około 30 minut spowoduje, że konto magazynu stanie się niedostępne. Funkcja zapytania jest niedostępna, a dane pozyskiwane są buforowane przez kilka godzin przy użyciu klawisza Microsoft, aby uniknąć utraty danych. Po przywróceniu dostępu do Key Vault jest on dostępny, a tymczasowe dane przechowywane w pamięci podręcznej są przechowywane w magazynie danych i szyfrowane za pomocą klucza zarządzanego przez klienta.

  - Key Vault szybkość dostępu — częstotliwość, z jaką Azure Monitor dostęp do magazynu Key Vault dla operacji zawijania i rozwinięcia, wynosi od 6 do 60 sekund.

- Jeśli klaster zostanie zaktualizowany w trakcie aprowizacji lub aktualizowania stanu, aktualizacja zakończy się niepowodzeniem.

- W przypadku wystąpienia błędu konfliktu podczas tworzenia klastra — może być to, że klaster został usunięty w ciągu ostatnich 14 dni i jest w okresie usuwania nietrwałego. Nazwa klastra pozostaje zarezerwowana w okresie usuwania nietrwałego i nie można utworzyć nowego klastra o takiej nazwie. Nazwa jest wydawana po okresie usuwania nietrwałego, gdy klaster zostanie trwale usunięty.

- Łącze obszaru roboczego do klastra zakończy się niepowodzeniem, jeśli jest ono połączone z innym klastrem.

- Jeśli utworzysz klaster i KeyVaultProperties od razu, operacja może się nie powieść, ponieważ nie można zdefiniować zasad dostępu do momentu przypisania tożsamości systemu do klastra.

- W przypadku aktualizacji istniejącego klastra z KeyVaultProperties i braku zasad dostępu do klucza "Get" w Key Vault operacja zakończy się niepowodzeniem.

- Jeśli klaster nie zostanie wdrożony, sprawdź, czy Azure Key Vault, klaster i połączone Log Analytics obszary robocze znajdują się w tym samym regionie. Mogą znajdować się w różnych subskrypcjach.

- Jeśli zaktualizujesz wersję klucza w Key Vault i nie zaktualizujesz nowego identyfikatora klucza w klastrze, klaster Log Analytics będzie nadal korzystać z poprzedniego klucza, a Twoje dane staną się niedostępne. Zaktualizuj szczegóły nowego identyfikatora klucza w klastrze w celu wznowienia pozyskiwania danych i wykonywania zapytań dotyczących danych.

- Niektóre operacje są długie i mogą chwilę potrwać — są to między innymi tworzenie klastra, Aktualizacja klucza klastra i usuwanie klastra. Stan operacji można sprawdzić na dwa sposoby:
  1. w przypadku korzystania z usługi REST skopiuj wartość Azure-AsyncOperation adresu URL z odpowiedzi i postępuj zgodnie ze [sprawdzaniem stanu operacji asynchronicznych](#asynchronous-operations-and-status-check).
  2. Wyślij żądanie GET do klastra lub obszaru roboczego i obserwuj odpowiedź. Na przykład niepołączony obszar roboczy nie będzie miał *clusterResourceId* w obszarze *funkcje*.

- Komunikaty o błędach
  
  **Tworzenie klastra**
  -  400 — Nazwa klastra jest nieprawidłowa. Nazwa klastra może zawierać znaki a-z, A-Z, 0-9 i długość 3-63.
  -  400 — treść żądania ma wartość null lub ma zły format.
  -  400 — Nazwa jednostki SKU jest nieprawidłowa. Ustaw nazwę jednostki SKU na capacityReservation.
  -  400 — dostarczono pojemność, ale jednostka SKU nie jest capacityReservation. Ustaw nazwę jednostki SKU na capacityReservation.
  -  400 — brak pojemności w jednostce SKU. Ustaw wartość pojemności na 1000 lub wyższą w krokach 100 (GB).
  -  400 — pojemność jednostki SKU nie należy do zakresu. Należy ustawić minimalną 1000 i maksymalnie maksymalną dozwoloną pojemność, która jest dostępna w obszarze roboczym "użycie i szacowany koszt".
  -  400 — pojemność jest zablokowana przez 30 dni. Zmniejszenie wydajności jest dozwolone przez 30 dni po aktualizacji.
  -  400 — nie ustawiono jednostki SKU. Ustaw wartość w polu Nazwa jednostki SKU na capacityReservation i pojemność na 1000 lub wyższą w krokach 100 (GB).
  -  400 — tożsamość ma wartość null lub jest pusta. Ustaw tożsamość z typem systemAssigned.
  -  400 — KeyVaultProperties są ustawiane podczas tworzenia. Zaktualizuj KeyVaultProperties po utworzeniu klastra.
  -  400 — nie można wykonać operacji teraz. Operacja asynchroniczna jest w stanie innym niż powodzenie. Klaster musi zakończyć swoją operację przed wykonaniem jakiejkolwiek operacji aktualizacji.

  **Aktualizacja klastra**
  -  400 — klaster jest w stanie usuwania. Operacja asynchroniczna jest w toku. Klaster musi zakończyć swoją operację przed wykonaniem jakiejkolwiek operacji aktualizacji.
  -  400 — KeyVaultProperties nie jest pusty, ale ma zły format. Zobacz [aktualizacja identyfikatora klucza](#update-cluster-with-key-identifier-details).
  -  400 — nie można zweryfikować klucza w Key Vault. Może być spowodowany brakiem uprawnień lub gdy klucz nie istnieje. Upewnij się, że [ustawisz Zasady kluczy i dostępu](#grant-key-vault-permissions) w Key Vault.
  -  400 — klucz nie jest możliwy do odzyskania. Key Vault musi być ustawiona na wartość unsoft-DELETE i przeczyszczania ochrony. Zobacz [dokumentację Key Vault](../../key-vault/general/soft-delete-overview.md)
  -  400 — nie można wykonać operacji teraz. Poczekaj na zakończenie operacji asynchronicznej i spróbuj ponownie.
  -  400 — klaster jest w stanie usuwania. Poczekaj na zakończenie operacji asynchronicznej i spróbuj ponownie.

  **Pobieranie klastra**
    -  404 — nie odnaleziono klastra, klaster mógł zostać usunięty. Jeśli spróbujesz utworzyć klaster o tej nazwie i uzyskać konflikt, klaster jest w trakcie usuwania nietrwałego przez 14 dni. Można skontaktować się z pomocą techniczną, aby go odzyskać, lub użyć innej nazwy do utworzenia nowego klastra. 

  **Usuwanie klastra**
    -  409 — nie można usunąć klastra w stanie aprowizacji. Poczekaj na zakończenie operacji asynchronicznej i spróbuj ponownie.

  **Link obszaru roboczego**
  -  404 — nie znaleziono obszaru roboczego. Wybrany obszar roboczy nie istnieje lub został usunięty.
  -  409--link do obszaru roboczego lub rozłączanie w procesie.
  -  400 — nie znaleziono klastra, określony klaster nie istnieje lub został usunięty. Jeśli spróbujesz utworzyć klaster o tej nazwie i uzyskać konflikt, klaster jest w trakcie usuwania nietrwałego przez 14 dni. Możesz skontaktować się z pomocą techniczną, aby ją odzyskać.

  **Odłącz obszar roboczy**
  -  404 — nie znaleziono obszaru roboczego. Wybrany obszar roboczy nie istnieje lub został usunięty.
  -  409--link do obszaru roboczego lub rozłączanie w procesie.
## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [log Analytics rozliczania dedykowanego klastra](./manage-cost-storage.md#log-analytics-dedicated-clusters)
- Dowiedz się więcej o [odpowiednim projekcie log Analytics obszarów roboczych](./design-logs-deployment.md)