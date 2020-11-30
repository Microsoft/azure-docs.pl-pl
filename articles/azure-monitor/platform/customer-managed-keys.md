---
title: Klucz zarządzany przez klienta usługi Azure Monitor
description: Informacje i kroki konfigurowania Customer-Managed klucza do szyfrowania danych w obszarach roboczych Log Analytics za pomocą klucza Azure Key Vault.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 11/18/2020
ms.openlocfilehash: c2a9dbb5fa821d408835cd1bcbf3e6afdade36e1
ms.sourcegitcommit: e5f9126c1b04ffe55a2e0eb04b043e2c9e895e48
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96317490"
---
# <a name="azure-monitor-customer-managed-key"></a>Klucz zarządzany przez klienta usługi Azure Monitor 

Ten artykuł zawiera ogólne informacje i instrukcje dotyczące konfigurowania kluczy zarządzanych przez klienta dla obszarów roboczych Log Analytics. Po skonfigurowaniu wszystkie dane wysyłane do obszarów roboczych są szyfrowane za pomocą klucza Azure Key Vault.

Zalecamy przejrzenie [ograniczeń i ograniczeń](#limitationsandconstraints) poniżej przed rozpoczęciem konfiguracji.

## <a name="customer-managed-key-overview"></a>Informacje o kluczu zarządzanym przez klienta

[Szyfrowanie w spoczynku](../../security/fundamentals/encryption-atrest.md) to wspólne wymagania w zakresie ochrony prywatności i bezpieczeństwa w organizacjach. Możesz pozwolić, aby platforma Azure całkowicie zarządzała szyfrowaniem w stanie spoczynku, podczas gdy masz różne opcje, aby dokładnie zarządzać szyfrowaniem i kluczami szyfrowania.

Azure Monitor gwarantuje, że wszystkie dane i zapisane zapytania są szyfrowane przy użyciu kluczy zarządzanych przez firmę Microsoft (MMK). Azure Monitor udostępnia również opcję szyfrowania przy użyciu własnego klucza, który jest przechowywany w [Azure Key Vault](../../key-vault/general/overview.md) i daje formantowi możliwość odwołania dostępu do danych w dowolnym momencie. Azure Monitor korzystania z szyfrowania jest taka sama jak w sposobie działania [szyfrowania usługi Azure Storage](../../storage/common/storage-service-encryption.md#about-azure-storage-encryption) .

Klucz Customer-Managed jest dostarczany w dedykowanych klastrach Log Analytics zapewniających wyższy poziom ochrony i kontrolę. Dane pozyskane do dedykowanych klastrów są szyfrowane dwa razy — raz na poziomie usługi przy użyciu kluczy zarządzanych przez firmę Microsoft lub kluczy zarządzanych przez klienta, a raz na poziomie infrastruktury przy użyciu dwóch różnych algorytmów szyfrowania i dwóch różnych kluczy. [Szyfrowanie podwójne](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) chroni przed scenariuszem, w którym można złamać jeden z algorytmów szyfrowania lub kluczy. W takim przypadku dodatkowa warstwa szyfrowania nadal chroni dane. Dedykowany klaster umożliwia również ochronę danych za pomocą kontrolki [skrytki](#customer-lockbox-preview) .

Dane pozyskane w ciągu ostatnich 14 dni również są przechowywane w pamięci podręcznej (dysk SSD) w celu wydajnej operacji aparatu zapytań. Te dane pozostają zaszyfrowane przy użyciu kluczy firmy Microsoft niezależnie od konfiguracji klucza zarządzanego przez klienta, ale kontrola nad danymi SSD jest zgodna z [odwołaniem klucza](#key-revocation). Pracujemy nad zaszyfrowaniem danych SSD z kluczem Customer-Managed w pierwszej połowie 2021.

[Model cenowy klastrów log Analytics](./manage-cost-storage.md#log-analytics-dedicated-clusters) używa rezerwacji pojemności, rozpoczynając od 1000 GB/dzień.

> [!IMPORTANT]
> Ze względu na tymczasowe ograniczenia pojemności firma Microsoft wymaga wstępnego zarejestrowania się przed utworzeniem klastra. Użyj swoich kontaktów do firmy Microsoft lub Otwórz żądanie pomocy technicznej, aby zarejestrować identyfikatory subskrypcji.

## <a name="how-customer-managed-key-works-in-azure-monitor"></a>Jak działa klucz Customer-Managed w Azure Monitor

Azure Monitor korzysta z zarządzanej tożsamości przypisanej do systemu, aby udzielić dostępu do Azure Key Vault. Tożsamość zarządzana przypisana przez system może być skojarzona tylko z pojedynczym zasobem platformy Azure, podczas gdy tożsamość klastra Log Analytics jest obsługiwana na poziomie klastra — to oznacza, że ta funkcja jest dostarczana w dedykowanym Log Analytics klastrze. Aby można było obsługiwać klucz Customer-Managed w wielu obszarach roboczych, nowy zasób *klastra* log Analytics pełni rolę pośredniego połączenia tożsamości między Key Vault i obszarami roboczymi log Analytics. Magazyn klastra Log Analytics używa tożsamości zarządzanej \' skojarzonej z zasobem *klastra* do uwierzytelniania Azure Key Vault za pośrednictwem Azure Active Directory. 

Po zakończeniu konfiguracji wszystkie dane pozyskiwane w obszarach roboczych połączonych z dedykowanym klastrem są szyfrowane przy użyciu klucza w Key Vault. W każdej chwili można odłączyć obszary robocze z klastra. Nowe dane są następnie pobierane do magazynu Log Analytics i szyfrowane za pomocą klucza firmy Microsoft, podczas gdy można bezproblemowo badać nowe i stare dane.


![Przegląd klucza Customer-Managed](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Log Analytics zasobu *klastra* mającego zarządzaną tożsamość z uprawnieniami do Key Vault — tożsamość jest propagowana do underlay dedykowanego log Analytics magazynu klastra
3. Dedykowany klaster Log Analytics
4. Obszary robocze połączone z zasobem *klastra* 

## <a name="encryption-keys-operation"></a>Operacja kluczy szyfrowania

Istnieją trzy typy kluczy, które wiążą się z szyfrowaniem danych magazynu:

- Klucz szyfrowania klucza **KEK** (klucz Customer-Managed)
- Klucz szyfrowania konta **AEK**
- **DEK** Klucz szyfrowania danych

Mają zastosowanie następujące zasady:

- Konta magazynu klastra Log Analytics generują unikatowy klucz szyfrowania dla każdego konta magazynu, który jest znany jako AEK.
- AEK jest używany do wyprowadzania DEKs, które są kluczami, które są używane do szyfrowania poszczególnych bloków danych zapisywana na dysku.
- W przypadku konfigurowania klucza w Key Vault i odwoływania się do niego w klastrze usługa Azure Storage wysyła żądania do Azure Key Vault, aby zawijać i deAEKać dane w celu szyfrowania i odszyfrowywania danych.
- KEK nigdy nie opuści Key Vault i w przypadku klucza HSM nigdy nie opuszcza sprzętu.
- Usługa Azure Storage korzysta z zarządzanej tożsamości skojarzonej z zasobem *klastra* w celu uwierzytelniania i dostępu do Azure Key Vault za pośrednictwem Azure Active Directory.

## <a name="customer-managed-key-provisioning-procedure"></a>Procedura inicjowania obsługi klucza Customer-Managed

1. Zarejestruj swoją subskrypcję, aby umożliwić tworzenie klastra
1. Tworzenie Azure Key Vault i przechowywanie klucza
1. Tworzenie klastra
1. Przyznawanie uprawnień do Key Vault
1. Łączenie Log Analytics obszarów roboczych

Konfiguracja klucza Customer-Managed nie jest obsługiwana w Azure Portal i Inicjowanie obsługi administracyjnej odbywa się za pośrednictwem [programu PowerShell](/powershell/module/az.operationalinsights/), [interfejsu wiersza polecenia](/cli/azure/monitor/log-analytics) lub żądań [rest](/rest/api/loganalytics/) .

### <a name="asynchronous-operations-and-status-check"></a>Operacje asynchroniczne i sprawdzanie stanu

Niektóre kroki konfiguracji działają asynchronicznie, ponieważ nie mogą być szybko wykonywane. `status`Odpowiedź w odpowiedzi zawiera może być jedną z następujących wartości: "InProgress", "Aktualizacja", "Usuwanie", "powodzenie" lub "zakończone niepowodzeniem", w tym kod błędu.

# <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Nie dotyczy

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Nie dotyczy

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Nie dotyczy

# <a name="rest"></a>[REST](#tab/rest)

W przypadku użycia opcji REST odpowiedź początkowo zwraca kod stanu HTTP 200 (OK) i nagłówek z właściwością *Azure-AsyncOperation* po zaakceptowaniu:
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

Stan operacji asynchronicznej można sprawdzić, wysyłając żądanie GET do wartości nagłówka *Azure-AsyncOperation* :
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

---

### <a name="allowing-subscription"></a>Zezwalanie na subskrypcję

> [!IMPORTANT]
> Funkcja klucza Customer-Managed jest regionalna. Azure Key Vault, klaster i połączone Log Analytics obszary robocze muszą znajdować się w tym samym regionie, ale mogą znajdować się w różnych subskrypcjach.

### <a name="storing-encryption-key-kek"></a>Przechowywanie klucza szyfrowania (KEK)

Utwórz lub użyj Azure Key Vault już do wygenerowania lub zaimportuj klucz, który ma być używany do szyfrowania danych. Azure Key Vault należy skonfigurować jako możliwy do odzyskania, aby chronić klucz i dostęp do danych w Azure Monitor. Możesz sprawdzić tę konfigurację w obszarze właściwości w Key Vault, należy włączyć zarówno funkcję *usuwania nietrwałego* , jak i *przeczyszczania* .

![Ustawienia ochrony usuwania nietrwałego i przeczyszczania](media/customer-managed-keys/soft-purge-protection.png)

Te ustawienia można aktualizować w Key Vault za pomocą interfejsu wiersza polecenia i programu PowerShell:

- [Usuwanie nietrwałe](../../key-vault/general/soft-delete-overview.md)
- [Przeczyść zabezpieczenia ochrony](../../key-vault/general/soft-delete-overview.md#purge-protection) przed wymuszeniem usunięcia wpisu tajnego/magazynu nawet po usunięciu nietrwałego

### <a name="create-cluster"></a>Tworzenie klastra

Postępuj zgodnie z procedurą przedstawioną w [artykule dedykowane klastry](../log-query/logs-dedicated-clusters.md#creating-a-cluster). 

> [!IMPORTANT]
> Skopiuj i Zapisz odpowiedź, ponieważ będziesz potrzebować szczegółowych informacji w następnych krokach.

### <a name="grant-key-vault-permissions"></a>Przyznawanie uprawnień Key Vault

Utwórz zasady dostępu w Key Vault, aby udzielać uprawnień do klastra. Te uprawnienia są używane przez underlay Azure Monitor Storage na potrzeby szyfrowania danych. Otwórz Key Vault w Azure Portal, a następnie kliknij pozycję "zasady dostępu" i "+ Dodaj zasady dostępu", aby utworzyć zasady z następującymi ustawieniami:

- Uprawnienia klucza: Wybierz uprawnienia "Pobierz", "Zawijanie klucza" i "Cofnij Zawijanie klucza".
- Wybierz podmiot zabezpieczeń: Wprowadź nazwę klastra lub wartość identyfikatora podmiotu zabezpieczeń, która zwróciła odpowiedź w poprzednim kroku.

![Przyznawanie uprawnień Key Vault](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

Uprawnienie *Get* jest wymagane do sprawdzenia, czy Key Vault jest skonfigurowany jako możliwy do odzyskania w celu ochrony klucza i dostępu do danych Azure monitor.

### <a name="update-cluster-with-key-identifier-details"></a>Aktualizuj klaster przy użyciu szczegółów identyfikatora klucza

Wszystkie operacje w klastrze wymagają uprawnienia Microsoft. OperationalInsights/klastrów/akcja zapisu. To uprawnienie można udzielić za pośrednictwem właściciela lub współautora, który zawiera *akcję/Write lub za pośrednictwem roli współautor log Analytics, która zawiera akcję Microsoft. OperationalInsights/* Action.

Ten krok umożliwia zaktualizowanie magazynu Azure Monitor przy użyciu klucza i wersji, który ma być używany do szyfrowania danych. Po zaktualizowaniu nowy klucz jest używany do zawijania i rozwinięcia klucza magazynu (AEK).

Wybierz bieżącą wersję klucza w Azure Key Vault, aby uzyskać szczegółowe informacje o identyfikatorze klucza.

![Przyznawanie uprawnień Key Vault](media/customer-managed-keys/key-identifier-8bit.png)

Zaktualizuj KeyVaultProperties w klastrze z informacjami o identyfikatorze klucza.

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

Wykonanie propagacji identyfikatora klucza trwa kilka minut. Stan aktualizacji można sprawdzić na dwa sposoby:
1. Skopiuj wartość Azure-AsyncOperation adresu URL z odpowiedzi i postępuj zgodnie z [testem stanu operacji asynchronicznych](#asynchronous-operations-and-status-check).
2. Wyślij żądanie GET w klastrze i sprawdź właściwości *KeyVaultProperties* . Ostatnio zaktualizowane szczegóły identyfikatora klucza powinny zwrócić odpowiedź.

Odpowiedź na żądanie pobrania powinna wyglądać następująco po zakończeniu aktualizacji identyfikatora klucza: 200 OK i nagłówek
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

### <a name="link-workspace-to-cluster"></a>Połącz obszar roboczy z klastrem

Aby można było wykonać tę operację, musisz mieć uprawnienia "Write" do obszaru roboczego i klastra, co obejmuje następujące akcje:

- W obszarze roboczym: Microsoft. OperationalInsights/Workspaces/Write
- W klastrze: Microsoft. OperationalInsights/klastrów/Write

> [!IMPORTANT]
> Ten krok należy wykonać dopiero po zakończeniu aprowizacji klastra Log Analytics. Jeśli połączysz obszary robocze i pozyskasz dane przed zainicjowaniem obsługi, pozyskiwane dane zostaną usunięte i nie będzie można ich odzyskać.

Ta operacja jest asynchroniczna i może zostać ukończona.

Postępuj zgodnie z procedurą przedstawioną w [artykule dedykowane klastry](../log-query/logs-dedicated-clusters.md#link-a-workspace-to-the-cluster).

## <a name="key-revocation"></a>Odwoływanie klucza

Dostęp do danych można odwołać, wyłączając klucz lub usuwając zasady dostępu klastra w Key Vault. Magazyn klastra Log Analytics będzie zawsze respektują zmiany w uprawnieniach klucza w ciągu godziny lub wcześniej, a magazyn stanie się niedostępny. Wszelkie nowe dane pozyskiwane w obszarach roboczych połączonych z klastrem zostaną usunięte i nie będzie można ich odzyskać, dane są niedostępne i zapytania do tych obszarów roboczych zakończą się niepowodzeniem. Wcześniej pozyskiwane dane pozostają w magazynie, dopóki klaster i Twoje obszary robocze nie zostaną usunięte. Dane niedostępne podlegają zasadom przechowywania danych i zostaną usunięte po osiągnięciu okresu przechowywania. 

Dane odebrane w ciągu ostatnich 14 dni również są przechowywane w pamięci podręcznej (dysk SSD) w celu wydajnej operacji aparatu zapytań. Spowoduje to usunięcie operacji odwoływania klucza i stanie się niedostępna.

Magazyn okresowo sonduje Key Vault, aby próbować odszyfrować klucz szyfrowania, a następnie uzyskać dostęp do pozyskiwania danych i wznowienia zapytania w ciągu 30 minut.

## <a name="key-rotation"></a>Wymiana kluczy

Rotacja kluczy Customer-Managed wymaga jawnej aktualizacji klastra z nową wersją klucza w Azure Key Vault. Postępuj zgodnie z instrukcjami w kroku "Aktualizowanie klastra z informacjami o identyfikatorze klucza". Jeśli nie zaktualizujesz nowego identyfikatora klucza w klastrze, magazyn klastra Log Analytics będzie nadal korzystać z poprzedniego klucza do szyfrowania. W przypadku wyłączenia lub usunięcia starego klucza przed zaktualizowaniem nowego klucza w klastrze zostanie wyświetlony stan [odwołania klucza](#key-revocation) .

Wszystkie dane pozostają dostępne po operacji rotacji kluczy, ponieważ dane zawsze są szyfrowane przy użyciu klucza szyfrowania konta (AEK), podczas gdy AEK jest teraz szyfrowany przy użyciu nowej wersji klucza szyfrowania kluczy (KEK) w Key Vault.

## <a name="customer-managed-key-for-queries"></a>Klucz Customer-Managed dla zapytań

Język zapytań używany w Log Analytics jest wyraźny i może zawierać poufne informacje w komentarzach, które można dodać do zapytań lub w składni zapytania. Niektóre organizacje wymagają, aby te informacje były chronione w ramach zasad kluczy Customer-Managed, a wymagane jest zapisanie zaszyfrowanej kwerendy przy użyciu klucza. Azure Monitor umożliwia przechowywanie *zapisywanych* i *zarejestrowań zapytań dotyczących alertów* w postaci zaszyfrowanej przy użyciu klucza na własnym koncie magazynu po nawiązaniu połączenia z obszarem roboczym. 

> [!NOTE]
> Zapytania Log Analytics można zapisywać w różnych magazynach w zależności od użytego scenariusza. Zapytania pozostają zaszyfrowane za pomocą usługi Microsoft Key (MMK) w następujących scenariuszach, niezależnie od konfiguracji klucza Customer-Managed: skoroszyty w Azure Monitor, pulpity nawigacyjne platformy Azure, aplikacja logiki Azure, Azure Notebooks i elementy Runbook automatyzacji.

Gdy przeniesiesz własny magazyn (BYOS) i połączysz go z obszarem roboczym, usługa przekaże *zapisane zapytania wyszukiwania* i *alerty dziennika* do konta magazynu. Oznacza to, że można kontrolować konto magazynu i [zasady szyfrowania w trybie REST](../../storage/common/customer-managed-keys-overview.md) przy użyciu tego samego klucza, który jest używany do szyfrowania danych w klastrze log Analytics lub innego klucza. Użytkownik będzie jednak odpowiedzialny za koszty związane z tym kontem magazynu. 

**Zagadnienia przed ustawieniem Customer-Managed klucza dla zapytań**
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

- **Pobieranie wszystkich klastrów w grupie zasobów**
  
  # <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

  Nie dotyczy

  # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

  ```azurecli
  az monitor log-analytics cluster list --resource-group "resource-group-name"
  ```

  # <a name="powershell"></a>[Program PowerShell](#tab/powershell)

  ```powershell
  Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name"
  ```

  # <a name="rest"></a>[REST](#tab/rest)

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Odpowiedź**
  
  ```json
  {
    "value": [
      {
        "identity": {
          "type": "SystemAssigned",
          "tenantId": "tenant-id",
          "principalId": "principal-Id"
        },
        "sku": {
          "name": "capacityReservation",
          "capacity": 1000,
          "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
          },
        "properties": {
           "keyVaultProperties": {
              "keyVaultUri": "https://key-vault-name.vault.azure.net",
              "keyName": "key-name",
              "keyVersion": "current-version"
              },
          "provisioningState": "Succeeded",
          "billingType": "cluster",
          "clusterId": "cluster-id"
        },
        "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
        "name": "cluster-name",
        "type": "Microsoft.OperationalInsights/clusters",
        "location": "region-name"
      }
    ]
  }
  ```

  ---

- **Pobierz wszystkie klastry w ramach subskrypcji**

  # <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

  Nie dotyczy

  # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

  ```azurecli
  az monitor log-analytics cluster list
  ```

  # <a name="powershell"></a>[Program PowerShell](#tab/powershell)

  ```powershell
  Get-AzOperationalInsightsCluster
  ```

  # <a name="rest"></a>[REST](#tab/rest)

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```
    
  **Odpowiedź**
    
  Taka sama odpowiedź jak dla klastra w grupie zasobów, ale w zakresie subskrypcji.

  ---

- **Aktualizowanie *rezerwacji pojemności* w klastrze**

  Gdy ilość danych w połączonych obszarach roboczych zmienia się wraz z upływem czasu i chcesz odpowiednio zaktualizować poziom rezerwacji. Wykonaj czynności opisane w obszarze [Aktualizuj klaster](#update-cluster-with-key-identifier-details) i podaj nową wartość pojemności. Może należeć do zakresu od 1000 do 3000 GB dziennie i kroków z 100. Na poziomie wyższym niż 3000 GB dziennie skontaktuj się z osobą kontaktową firmy Microsoft, aby ją włączyć. Należy pamiętać, że nie musisz podawać całej treści żądania REST, ale powinna ona zawierać jednostkę SKU:

  # <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

  Nie dotyczy

  # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

  ```azurecli
  az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --sku-capacity daily-ingestion-gigabyte
  ```

  # <a name="powershell"></a>[Program PowerShell](#tab/powershell)

  ```powershell
  Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity daily-ingestion-gigabyte
  ```

  # <a name="rest"></a>[REST](#tab/rest)

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": daily-ingestion-gigabyte
    }
  }
  ```

  ---

- **Aktualizowanie *w* klastrze**

  Właściwość *rozliczenia* określa przypisanie rozliczeń dla klastra i jego danych:
  - *klaster* (domyślnie) — rozliczenia są przypisywane do subskrypcji hostingowej zasobu klastra
  - *obszary robocze* — rozliczenia są przypisane do subskrypcji obsługujących obszary robocze proporcjonalnie
  
  Wykonaj czynności opisane w obszarze [Aktualizuj klaster](#update-cluster-with-key-identifier-details) i podaj nową wartość rozliczenia. Należy *pamiętać, że* nie musisz podawać treści żądania w trybie REST i powinny być uwzględniane:

  # <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

  Nie dotyczy

  # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

  Nie dotyczy

  # <a name="powershell"></a>[Program PowerShell](#tab/powershell)

  Nie dotyczy

  # <a name="rest"></a>[REST](#tab/rest)

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ``` 

  ---

- **Unlink workspace** (Odłączanie obszaru roboczego)

  Aby można było wykonać tę operację, musisz mieć uprawnienia "Write" w obszarze roboczym i klastrze. W dowolnym momencie możesz odłączyć obszar roboczy z klastra. Nowe dane pozyskiwane po wykonaniu operacji odłączenia są przechowywane w magazynie Log Analytics i szyfrowane za pomocą klucza firmy Microsoft. Możesz wykonywać zapytania dotyczące danych, które zostały pozyskane do obszaru roboczego przed bezproblemowym rozłączeniem, o ile klaster jest zainicjowany i skonfigurowany z prawidłowym kluczem Key Vault.

  Ta operacja jest asynchroniczna i można ją ukończyć.

  # <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

  Nie dotyczy

  # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

  ```azurecli
  az monitor log-analytics workspace linked-service delete --resource-group "resource-group-name" --name "cluster-name" --workspace-name "workspace-name"
  ```

  # <a name="powershell"></a>[Program PowerShell](#tab/powershell)

  ```powershell
  Remove-AzOperationalInsightsLinkedService -ResourceGroupName "resource-group-name" -Name "workspace-name" -LinkedServiceName cluster
  ```

  # <a name="rest"></a>[REST](#tab/rest)

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  ---

- **Sprawdź stan linku obszaru roboczego**
  
  Wykonaj operację get w obszarze roboczym i sprawdź, czy właściwość *clusterResourceId* jest obecna w odpowiedzi w obszarze *funkcje*. Połączony obszar roboczy będzie miał Właściwość *clusterResourceId* .

  # <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

  Nie dotyczy

  # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

  ```azurecli
  az monitor log-analytics cluster show --resource-group "resource-group-name" --name "cluster-name"
  ```

  # <a name="powershell"></a>[Program PowerShell](#tab/powershell)

  ```powershell
  Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
  ```

  # <a name="rest"></a>[REST](#tab/rest)

   ```rest
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  ---

- **Usuwanie klastra**

  Aby można było wykonać tę operację, musisz mieć uprawnienia "Write" w klastrze. Operacja usuwania nietrwałego jest wykonywana w celu zezwolenia na odzyskiwanie klastra, w tym jego danych w ciągu 14 dni, bez względu na to, czy usunięcie było przypadkowe czy celowe. Nazwa klastra pozostaje zarezerwowana w okresie usuwania nietrwałego i nie można utworzyć nowego klastra o takiej nazwie. Po okresie usuwania nietrwałego Nazwa klastra zostanie wydana, a klaster i jego dane są trwale usuwane i nie można ich odzyskać. Wszystkie połączone obszary robocze są odłączone od klastra podczas operacji usuwania. Nowe pozyskiwane dane są przechowywane w magazynie Log Analytics i szyfrowane za pomocą klucza firmy Microsoft. 
  
  Operacja odłączenia jest asynchroniczna i może trwać do 90 minut.

  # <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

  Nie dotyczy

  # <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

  ```azurecli
  az monitor log-analytics cluster delete --resource-group "resource-group-name" --name "cluster-name"
  ```

  # <a name="powershell"></a>[Program PowerShell](#tab/powershell)

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

  # <a name="rest"></a>[REST](#tab/rest)

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  ---
  
- **Odzyskiwanie klastra i danych** 
  
  Klaster, który został usunięty w ciągu ostatnich 14 dni, jest w stanie usuwania nietrwałego i może zostać odzyskany ze swoimi danymi. Ponieważ wszystkie obszary robocze zostały odłączone od usunięcia klastra, należy ponownie połączyć obszary robocze po odzyskaniu klastra. Operacja odzyskiwania jest obecnie wykonywana ręcznie przez grupę produktów. Skorzystaj z kanału firmy Microsoft lub Otwórz żądanie pomocy technicznej w celu odzyskania usuniętego klastra.

## <a name="limitations-and-constraints"></a>Ograniczenia i ograniczenia

- Klucz Customer-Managed jest obsługiwany w dedykowanym klastrze Log Analytics i jest przeznaczony dla klientów wysyłających 1 TB dziennie lub dłużej.

- Maksymalna liczba klastrów na region i subskrypcja wynosi 2

- Maksymalna liczba połączonych obszarów roboczych do klastra to 1000

- Możesz połączyć obszar roboczy z klastrem, a następnie odłączyć go od siebie. Liczba operacji linku obszaru roboczego w określonym obszarze roboczym jest ograniczona do 2 w okresie 30 dni.

- Link obszaru roboczego do klastra należy przeprowadzić dopiero po sprawdzeniu, czy zakończono Inicjowanie obsługi klastra Log Analytics. Dane wysyłane do obszaru roboczego przed ukończeniem zostaną usunięte i nie będzie można ich odzyskać.

- Customer-Managed szyfrowanie klucza ma zastosowanie do nowo wprowadzonych danych po upływie czasu konfiguracji. Dane, które zostały wprowadzone przed rozpoczęciem konfiguracji, pozostają zaszyfrowane za pomocą klucza firmy Microsoft. Możesz wykonywać zapytania dotyczące danych pozyskiwanych przed bezproblemową konfiguracją Customer-Managed i po niej.

- Azure Key Vault musi być skonfigurowany jako możliwy do odzyskania. Te właściwości nie są domyślnie włączone i należy je skonfigurować przy użyciu interfejsu wiersza polecenia lub programu PowerShell:<br>
  - [Usuwanie nietrwałe](../../key-vault/general/soft-delete-overview.md)
  - [Ochrona przed przeczyszczeniem](../../key-vault/general/soft-delete-overview.md#purge-protection) powinna być włączona, aby zabezpieczyć przed wymuszeniem usunięcia wpisu tajnego lub magazynu nawet po usunięciu nietrwałego.

- Klaster przeniesiony do innej grupy zasobów lub subskrypcji nie jest obecnie obsługiwany.

- Azure Key Vault, klaster i połączone obszary robocze muszą znajdować się w tym samym regionie i w tej samej dzierżawie Azure Active Directory (Azure AD), ale mogą znajdować się w różnych subskrypcjach.

- Łącze obszaru roboczego do klastra zakończy się niepowodzeniem, jeśli jest ono połączone z innym klastrem.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

- Zachowanie z dostępnością Key Vault
  - W normalnej operacji — pamięć podręczna magazynu AEK przez krótki okresy i powraca do Key Vault, aby okresowo wycofać.
    
  - Błędy połączeń przejściowych — usługa Storage obsługuje błędy przejściowe (przekroczenia limitu czasu, błędy połączeń, problemy z usługą DNS), dzięki czemu klucze mogą pozostać w pamięci podręcznej przez krótki czas i jest to zbyt małe Blips w dostępności. Możliwości zapytań i pozyskiwania kontynuują działanie bez przeszkód.
    
  - Lokacja na żywo — niedostępność przez około 30 minut spowoduje, że konto magazynu stanie się niedostępne. Funkcja zapytania jest niedostępna, a dane pozyskiwane są buforowane przez kilka godzin przy użyciu klawisza Microsoft, aby uniknąć utraty danych. Po przywróceniu dostępu do Key Vault jest on dostępny, a tymczasowe dane przechowywane w pamięci podręcznej są przechowywane w magazynie danych i szyfrowane za pomocą klucza Customer-Managed.

  - Key Vault szybkość dostępu — częstotliwość, z jaką Azure Monitor dostęp do magazynu Key Vault dla operacji zawijania i rozwinięcia, wynosi od 6 do 60 sekund.

- Jeśli utworzysz klaster i KeyVaultProperties od razu, operacja może się nie powieść, ponieważ nie można zdefiniować zasad dostępu do momentu przypisania tożsamości systemu do klastra.

- W przypadku aktualizacji istniejącego klastra z KeyVaultProperties i braku zasad dostępu do klucza "Get" w Key Vault operacja zakończy się niepowodzeniem.

- W przypadku wystąpienia błędu konfliktu podczas tworzenia klastra — może być to, że klaster został usunięty w ciągu ostatnich 14 dni i jest w okresie usuwania nietrwałego. Nazwa klastra pozostaje zarezerwowana w okresie usuwania nietrwałego i nie można utworzyć nowego klastra o takiej nazwie. Nazwa jest wydawana po okresie usuwania nietrwałego, gdy klaster zostanie trwale usunięty.

- Jeśli aktualizujesz klaster, gdy operacja jest w toku, operacja zakończy się niepowodzeniem.

- Jeśli klaster nie zostanie wdrożony, sprawdź, czy Azure Key Vault, klaster i połączone Log Analytics obszary robocze znajdują się w tym samym regionie. Mogą znajdować się w różnych subskrypcjach.

- Jeśli zaktualizujesz wersję klucza w Key Vault i nie zaktualizujesz nowego identyfikatora klucza w klastrze, klaster Log Analytics będzie nadal korzystać z poprzedniego klucza, a Twoje dane staną się niedostępne. Zaktualizuj szczegóły nowego identyfikatora klucza w klastrze w celu wznowienia pozyskiwania danych i wykonywania zapytań dotyczących danych.

- Niektóre operacje są długie i mogą chwilę potrwać — są to między innymi tworzenie klastra, Aktualizacja klucza klastra i usuwanie klastra. Stan operacji można sprawdzić na dwa sposoby:
  1. w przypadku korzystania z usługi REST skopiuj wartość Azure-AsyncOperation adresu URL z odpowiedzi i postępuj zgodnie ze [sprawdzaniem stanu operacji asynchronicznych](#asynchronous-operations-and-status-check).
  2. Wyślij żądanie GET do klastra lub obszaru roboczego i obserwuj odpowiedź. Na przykład niepołączony obszar roboczy nie będzie miał *clusterResourceId* w obszarze *funkcje*.

- [Podwójne szyfrowanie](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) jest konfigurowane automatycznie w przypadku klastrów utworzonych z października 2020 w obsługiwanych regionach. Możesz sprawdzić, czy klaster jest skonfigurowany do podwójnego szyfrowania przez żądanie GET w klastrze i obserwując `"isDoubleEncryptionEnabled"` wartość właściwości — `true` dla klastrów z włączonym podwójnym szyfrowaniem. 
  - Jeśli utworzysz klaster i wystąpi błąd "<regionu-Name> nie obsługuje podwójnego szyfrowania dla klastrów" ", można nadal utworzyć klaster bez podwójnego szyfrowania. Dodaj `"properties": {"isDoubleEncryptionEnabled": false}` w treści żądania Rest.
  - Ustawienia podwójnego szyfrowania nie można zmienić po utworzeniu klastra.

- Komunikaty o błędach
  
  Tworzenie klastra:
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

  Aktualizacja klastra
  -  400 — klaster jest w stanie usuwania. Operacja asynchroniczna jest w toku. Klaster musi zakończyć swoją operację przed wykonaniem jakiejkolwiek operacji aktualizacji.
  -  400 — KeyVaultProperties nie jest pusty, ale ma zły format. Zobacz [aktualizacja identyfikatora klucza](#update-cluster-with-key-identifier-details).
  -  400 — nie można zweryfikować klucza w Key Vault. Może być spowodowany brakiem uprawnień lub gdy klucz nie istnieje. Upewnij się, że [ustawisz Zasady kluczy i dostępu](#grant-key-vault-permissions) w Key Vault.
  -  400 — klucz nie jest możliwy do odzyskania. Key Vault musi być ustawiona na wartość unsoft-DELETE i przeczyszczania ochrony. Zobacz [dokumentację Key Vault](../../key-vault/general/soft-delete-overview.md)
  -  400 — nie można wykonać operacji teraz. Poczekaj na zakończenie operacji asynchronicznej i spróbuj ponownie.
  -  400 — klaster jest w stanie usuwania. Poczekaj na zakończenie operacji asynchronicznej i spróbuj ponownie.

  Pobierz klaster:
    -  404 — nie odnaleziono klastra, klaster mógł zostać usunięty. Jeśli spróbujesz utworzyć klaster o tej nazwie i uzyskać konflikt, klaster jest w trakcie usuwania nietrwałego przez 14 dni. Można skontaktować się z pomocą techniczną, aby go odzyskać, lub użyć innej nazwy do utworzenia nowego klastra. 

  Usuwanie klastra
    -  409 — nie można usunąć klastra w stanie aprowizacji. Poczekaj na zakończenie operacji asynchronicznej i spróbuj ponownie.

  Link obszaru roboczego:
  -  404 — nie znaleziono obszaru roboczego. Wybrany obszar roboczy nie istnieje lub został usunięty.
  -  409--link do obszaru roboczego lub rozłączanie w procesie.
  -  400 — nie znaleziono klastra, określony klaster nie istnieje lub został usunięty. Jeśli spróbujesz utworzyć klaster o tej nazwie i uzyskać konflikt, klaster jest w trakcie usuwania nietrwałego przez 14 dni. Możesz skontaktować się z pomocą techniczną, aby ją odzyskać.

  Odłączenie obszaru roboczego:
  -  404 — nie znaleziono obszaru roboczego. Wybrany obszar roboczy nie istnieje lub został usunięty.
  -  409--link do obszaru roboczego lub rozłączanie w procesie.