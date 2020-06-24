---
title: Azure Monitor klucz zarządzany przez klienta
description: Informacje i kroki służące do konfigurowania klucza zarządzanego przez klienta (CMK) w celu szyfrowania danych w obszarach roboczych Log Analytics przy użyciu klucza Azure Key Vault.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 06/11/2020
ms.openlocfilehash: 6e3a4b61c86d476a9e5c5a0392c51a72f06f048d
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/14/2020
ms.locfileid: "84761335"
---
# <a name="azure-monitor-customer-managed-key"></a>Azure Monitor klucz zarządzany przez klienta 

Ten artykuł zawiera ogólne informacje i instrukcje dotyczące konfigurowania kluczy zarządzanych przez klienta (CMK) dla obszarów roboczych Log Analytics. Po skonfigurowaniu wszystkie dane wysyłane do obszarów roboczych są szyfrowane za pomocą klucza Azure Key Vault.

Zalecamy przejrzenie [ograniczeń i ograniczeń](#limitationsandconstraints) poniżej przed rozpoczęciem konfiguracji.

## <a name="customer-managed-key-cmk-overview"></a>Klucz zarządzany przez klienta (CMK) — Omówienie

[Szyfrowanie w spoczynku](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)   to typowe wymagania w zakresie ochrony prywatności i bezpieczeństwa w organizacjach.Możesz pozwolić, aby platforma Azure całkowicie zarządzała szyfrowaniem w stanie spoczynku, podczas gdy masz różne opcje, aby dokładnie zarządzać szyfrowaniem lub kluczami szyfrowania.

Azure Monitor gwarantuje, że wszystkie dane są szyfrowane przy użyciu kluczy zarządzanych przez platformę Azure.Azure Monitor udostępnia również opcję szyfrowania danych przy użyciu własnego klucza przechowywanego w [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)   i dostępnego przez magazyn przy użyciu uwierzytelniania [tożsamości zarządzanej](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)przypisanego przez system   .Ten klucz może być [chroniony przez oprogramowanie lub sprzęt-moduł HSM](https://docs.microsoft.com/azure/key-vault/key-vault-overview).

Azure Monitor korzystania z szyfrowania jest taka sama jak w sposobie działania [szyfrowania usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption)   .

CMK umożliwia kontrolowanie dostępu do danych i odwoływanie go w dowolnym momencie. Azure Monitor Storage zawsze uwzględnia zmiany w uprawnieniach klucza w ciągu godziny. Dane pozyskane w ciągu ostatnich 14 dni również są przechowywane w pamięci podręcznej (dysk SSD) w celu wydajnej operacji aparatu zapytań. Te dane pozostają zaszyfrowane przy użyciu kluczy firmy Microsoft bez względu na konfigurację CMK, ale kontrola nad danymi SSD jest zgodna z [odwołaniem klucza](#cmk-kek-revocation). Pracujemy nad zaszyfrowaniem danych SSD z CMK w drugiej połowie 2020.

Funkcja CMK jest dostarczana w dedykowanych klastrach Log Analytics. Aby sprawdzić, czy w Twoim regionie jest wymagana pojemność, musisz wcześniej zezwolić na subskrypcję. Skontaktuj się z firmą Microsoft, aby uzyskać dostęp do subskrypcji przed rozpoczęciem konfigurowania usługi CMK.

 [Model cenowy klastrów log Analytics](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-dedicated-clusters)   używa rezerwacji pojemności, rozpoczynając od 1000 GB/dzień.

## <a name="how-cmk-works-in-azure-monitor"></a>Jak działa CMK w Azure Monitor

Azure Monitor korzysta z zarządzanej tożsamości przypisanej do systemu, aby udzielić dostępu do Azure Key Vault. Tożsamość zarządzana przypisana przez system może być skojarzona tylko z pojedynczym zasobem platformy Azure, podczas gdy tożsamość klastra Log Analytics jest obsługiwana na poziomie klastra — to oznacza, że możliwość CMK jest dostarczana w dedykowanym Log Analytics klastrze. Aby obsługiwać CMK w wielu obszarach roboczych, nowy zasób *klastra* log Analytics pełni rolę pośredniego połączenia tożsamości między Key Vault i obszarami roboczymi log Analytics. Magazyn klastra Log Analytics używa tożsamości zarządzanej \' skojarzonej z zasobem *klastra* do uwierzytelniania Azure Key Vault za pośrednictwem Azure Active Directory. 

Po CMK konfiguracji wszystkie dane pozyskiwane w obszarach roboczych skojarzonych z zasobem *klastra* są szyfrowane za pomocą klucza w Key Vault. W dowolnym momencie możesz usunąć skojarzenie obszarów roboczych z zasobem *klastra* . Nowe dane są pobierane do magazynu Log Analytics i szyfrowane za pomocą klucza firmy Microsoft, podczas gdy można bezproblemowo badać nowe i stare dane.


![CMK — Omówienie](media/customer-managed-keys/cmk-overview.png)

1. Key Vault
2. Log Analytics zasobu *klastra* mającego zarządzaną tożsamość z uprawnieniami do Key Vault — tożsamość jest propagowana do underlay dedykowanego log Analytics magazynu klastra
3. Dedykowany klaster Log Analytics
4. Obszary robocze skojarzone z zasobem *klastra* na potrzeby szyfrowania CMK

## <a name="encryption-keys-operation"></a>Operacja kluczy szyfrowania

Istnieją trzy typy kluczy, które wiążą się z szyfrowaniem danych magazynu:

- Klucz szyfrowania klucza ( **KEK** ) (CMK)
- Klucz szyfrowania konta **AEK**
- **DEK** Klucz szyfrowania danych

Mają zastosowanie następujące zasady:

- Konta magazynu klastra Log Analytics generują unikatowy klucz szyfrowania dla każdego konta magazynu, który jest znany jako AEK.

- AEK jest używany do wyprowadzania DEKs, które są kluczami, które są używane do szyfrowania poszczególnych bloków danych zapisywana na dysku.

- Podczas konfigurowania klucza w Key Vault i odwoływania się do niego w ramach zasobu *klastra* usługa Azure Storage wysyła żądania do Azure Key Vault, aby zawijać i deAEKać dane w celu szyfrowania i odszyfrowywania danych.

- KEK nigdy nie opuści Key Vault i w przypadku klucza HSM nigdy nie opuszcza sprzętu.

- Usługa Azure Storage korzysta z zarządzanej tożsamości skojarzonej z zasobem *klastra* w celu uwierzytelniania i dostępu do Azure Key Vault za pośrednictwem Azure Active Directory.

## <a name="cmk-provisioning-procedure"></a>Procedura inicjowania obsługi CMK

1. Zezwalanie na subskrypcję — funkcja CMK jest dostarczana w dedykowanych klastrach Log Analytics. Aby sprawdzić, czy w Twoim regionie jest wymagana pojemność, musisz wcześniej zezwolić na subskrypcję. Skontaktuj się z pomocą techniczną firmy Microsoft, aby uzyskać dostęp do subskrypcji.
2. Tworzenie Azure Key Vault i przechowywanie klucza
3. Tworzenie zasobu *klastra*
4. Przyznawanie uprawnień do Key Vault
5. Kojarzenie Log Analytics obszarów roboczych

Procedura nie jest obsługiwana w Azure Portal i Inicjowanie obsługi administracyjnej odbywa się za pośrednictwem programu PowerShell lub żądań REST.

> [!IMPORTANT]
> Wszystkie żądania REST muszą zawierać Token autoryzacji okaziciela w nagłówku żądania.

Przykład:

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer eyJ0eXAiO....
```

Gdzie *eyJ0eXAiO....* reprezentuje token pełnej autoryzacji. 

Token można uzyskać, korzystając z jednej z następujących metod:

1. Użyj metody [rejestracje aplikacji](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens) .
2. W witrynie Azure Portal
    1. Przejdź do Azure Portal w "narzędziu deweloperskim" (F12)
    1. Wyszukaj ciąg autoryzacji w obszarze "nagłówki żądania" w jednym z wystąpień "Batch? API-Version". Wygląda na to, że: "Authorization: Bearer eyJ0eXAiO...". 
    1. Skopiuj i dodaj go do wywołania interfejsu API zgodnie z poniższymi przykładami.
3. Przejdź do witryny dokumentacji REST platformy Azure. Naciśnij pozycję "Wypróbuj" w dowolnym interfejsie API i skopiuj token okaziciela.

### <a name="asynchronous-operations-and-status-check"></a>Operacje asynchroniczne i sprawdzanie stanu

Niektóre operacje w tej procedurze konfiguracji są uruchamiane asynchronicznie, ponieważ nie mogą być szybko wykonywane. W przypadku korzystania z żądań REST w konfiguracji, odpowiedź początkowo zwraca kod stanu HTTP 200 (OK) i nagłówek z właściwością *Azure-AsyncOperation* po zaakceptowaniu:
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-03-01-preview"
```

Następnie można sprawdzić stan operacji asynchronicznej, wysyłając żądanie GET do wartości nagłówka *Azure-AsyncOperation* :
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

Odpowiedź zawiera informacje o operacji i jej *stanie*. Może to być jedna z następujących czynności:

Operacja jest w toku
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "InProgress", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
}
```

Operacja aktualizacji identyfikatora klucza jest w toku
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Updating", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

Usuwanie zasobu *klastra* jest w toku — po usunięciu zasobu *klastra* z obszarami roboczymi skojarzonymi obszarami roboczymi jest wykonywana operacja usuwania skojarzenia dla każdego obszaru roboczego w operacjach asynchronicznych, które mogą chwilę potrwać.
Nie dotyczy to sytuacji, gdy usuwasz *klaster* bez skojarzonego obszaru roboczego — w tym przypadku zasób *klastra* zostanie natychmiast usunięty.
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Deleting", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

Operacja została ukończona
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Succeeded", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
}
```

Operacja nie powiodła się
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "Failed", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
    "endTime": "2017-01-06T20:56:56.002812+00:00",
    "error" : { 
        "code": "error-code",  
        "message": "error-message" 
    }
}
```

### <a name="allowing-subscription-for-cmk-deployment"></a>Zezwalanie na subskrypcję wdrożenia CMK

Funkcja CMK jest dostarczana w dedykowanych klastrach Log Analytics.Aby sprawdzić, czy w Twoim regionie jest wymagana pojemność, musisz wcześniej zezwolić na subskrypcję. Użyj swoich kontaktów do firmy Microsoft, aby podać identyfikatory subskrypcji.

> [!IMPORTANT]
> Funkcja CMK jest regionalna. Azure Key Vault, zasób *klastra* i powiązane obszary robocze log Analytics muszą znajdować się w tym samym regionie, ale mogą znajdować się w różnych subskrypcjach.

### <a name="storing-encryption-key-kek"></a>Przechowywanie klucza szyfrowania (KEK)

Utwórz lub użyj Azure Key Vault już do wygenerowania lub zaimportuj klucz, który ma być używany do szyfrowania danych. Azure Key Vault należy skonfigurować jako możliwy do odzyskania, aby chronić klucz i dostęp do danych w Azure Monitor. Możesz sprawdzić tę konfigurację w obszarze właściwości w Key Vault, należy włączyć zarówno funkcję *usuwania nietrwałego* , jak i *przeczyszczania* .

![Ustawienia ochrony usuwania nietrwałego i przeczyszczania](media/customer-managed-keys/soft-purge-protection.png)

Te ustawienia są dostępne za pośrednictwem interfejsu wiersza polecenia i programu PowerShell:
- [Usuwanie nietrwałe](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- [Przeczyść zabezpieczenia ochrony](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) przed wymuszeniem usunięcia wpisu tajnego/magazynu nawet po usunięciu nietrwałego

### <a name="create-cluster-resource"></a>Utwórz zasób *klastra*

Ten zasób jest używany jako połączenie tożsamości pośredniej między Key Vault i obszarami roboczymi Log Analytics. Po otrzymaniu potwierdzenia, że subskrypcje były dozwolone, utwórz zasób *klastra* log Analytics w regionie, w którym znajdują się obszary robocze.

Podczas tworzenia zasobu *klastra* należy określić poziom *rezerwacji pojemności* (SKU). Poziom *rezerwacji pojemności* może należeć do zakresu od 1 000 do 2 000 GB dziennie i można go zaktualizować w krokach 100 w późniejszym czasie. Jeśli potrzebujesz poziomu rezerwacji pojemności większej niż 2 000 GB dziennie, skontaktuj się z nami pod adresem LAIngestionRate@microsoft.com . [Dowiedz się więcej](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-clusters)

Właściwość *rozliczenia* określa przypisanie rozliczeń dla zasobu *klastra* i jego danych:
- *Klaster* (wartość domyślna) — koszty rezerwacji pojemności dla klastra są przypisywane do zasobu *klastra* .
- *Obszary robocze* — koszty rezerwacji pojemności dla klastra są przypisywane proporcjonalnie do obszarów roboczych w klastrze, a zasób *klastra* jest rozliczany jako część użycia, jeśli łączna ilość danych pobieranych przez dzień jest objęta rezerwacją pojemności. Zobacz [log Analytics dedykowanych klastrów](manage-cost-storage.md#log-analytics-dedicated-clusters) , aby dowiedzieć się więcej na temat modelu cen klastra. 

> [!NOTE]
> * Po utworzeniu zasobu *klastra* można go zaktualizować przy użyciu *jednostki SKU*, *keyVaultProperties* lub *rozliczeń* za pomocą żądania poprawek Rest.
> * W programie PowerShell można obecnie aktualizować opcję *rozliczeń* przy użyciu żądania Rest. nie jest to obsługiwane

Ta operacja jest asynchroniczna i może zostać ukończona.

> [!IMPORTANT]
> Skopiuj i Zapisz odpowiedź, ponieważ będziesz potrzebować szczegółowych informacji w następnych krokach.
> 
**PowerShell**

```powershell
New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -Location {region-name} -SkuCapacity {daily-ingestion-gigabyte} 
```

**REST**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
  "identity": {
    "type": "systemAssigned"
    },
  "sku": {
    "name": "capacityReservation",
    "Capacity": 1000
    },
  "properties": {
    "billingType": "cluster",
    },
  "location": "<region-name>",
}
```

Tożsamość jest przypisywana do zasobu *klastra* podczas tworzenia.

**Reakcji**

200 OK i nagłówek.

Mimo że trwa inicjowanie obsługi klastra Log Analytics przez pewien czas, można sprawdzić stan aprowizacji na dwa sposoby:

1. Skopiuj wartość adresu URL platformy Azure-AsyncOperation z odpowiedzi i postępuj zgodnie ze [sprawdzaniem stanu operacji asynchronicznych](#asynchronous-operations-and-status-check).
2. Wyślij żądanie GET do zasobu *klastra* i sprawdź wartość *provisioningState* . Jest on *ProvisioningAccount* podczas inicjowania obsługi administracyjnej i zakończył *się pomyślnie* .

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

**Reakcji**

```json
{
  "identity": {
    "type": "SystemAssigned",
    "tenantId": "tenant-id",
    "principalId": "principal-id"
    },
  "sku": {
    "name": "capacityReservation",
    "capacity": 1000,
    "lastSkuUpdate": "Sun, 22 Mar 2020 15:39:29 GMT"
    },
  "properties": {
    "provisioningState": "ProvisioningAccount",
    "billingType": "cluster",
    "clusterId": "cluster-id"
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

Identyfikator GUID "principalId" jest generowany przez zarządzaną usługę tożsamości dla zasobu *klastra* .

### <a name="grant-key-vault-permissions"></a>Przyznawanie uprawnień Key Vault

Zaktualizuj Key Vault przy użyciu nowych zasad dostępu w celu przyznania uprawnień do zasobu *klastra* . Te uprawnienia są używane przez underlay Azure Monitor Storage na potrzeby szyfrowania danych. Otwórz Key Vault w Azure Portal, a następnie kliknij pozycję "zasady dostępu" i "+ Dodaj zasady dostępu", aby utworzyć zasady z następującymi ustawieniami:

- Uprawnienia klucza: Wybierz uprawnienia "Pobierz", "Zawijanie klucza" i "Cofnij Zawijanie klucza".
- Wybierz podmiot zabezpieczeń: Wprowadź nazwę zasobu *klastra* lub wartość identyfikatora podmiotu zabezpieczeń, która zwróciła odpowiedź w poprzednim kroku.

![Przyznawanie uprawnień Key Vault](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

Uprawnienie *Get* jest wymagane do sprawdzenia, czy Key Vault jest skonfigurowany jako możliwy do odzyskania w celu ochrony klucza i dostępu do danych Azure monitor.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Aktualizowanie zasobu klastra przy użyciu szczegółów identyfikatora klucza

Ten krok jest wykonywany podczas początkowej i w przyszłych wersjach kluczowych aktualizacji w Key Vault. Informuje Magazyn Azure Monitor o wersji klucza, który ma być używany do szyfrowania danych. Po zaktualizowaniu nowy klucz jest używany do zawijania i depakowania do klucza magazynu (AEK).

Aby zaktualizować zasób *klastra* przy użyciu Key Vault szczegóły *identyfikatora klucza* , wybierz bieżącą wersję klucza w Azure Key Vault, aby uzyskać szczegóły identyfikatora klucza.

![Przyznawanie uprawnień Key Vault](media/customer-managed-keys/key-identifier-8bit.png)

Zaktualizuj KeyVaultProperties zasobów *klastra* przy użyciu szczegółów identyfikatora klucza.

Ta operacja jest asynchroniczna podczas aktualizowania szczegółów identyfikatora klucza i może zająć trochę czasu. Jest ona synchroniczna podczas aktualizowania wartości wydajności.

**PowerShell**

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -KeyVaultUri {key-uri} -KeyName {key-name} -KeyVersion {key-version}
```

**REST**

> [!NOTE]
> Możesz zaktualizować *jednostkę SKU*zasobu *klastra* , *keyVaultProperties* lub *rozliczeń* przy użyciu poprawki.

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "identity": { 
     "type": "systemAssigned" 
     },
   "sku": {
     "name": "capacityReservation",
     "capacity": 1000
     },
   "properties": {
    "billingType": "cluster",
     "KeyVaultProperties": {
       "KeyVaultUri": "https://<key-vault-name>.vault.azure.net",
       "KeyName": "<key-name>",
       "KeyVersion": "<current-version>"
       }
   },
   "location":"<region-name>"
}
```

"KeyVaultProperties" zawiera szczegóły identyfikatora klucza Key Vault.

**Reakcji**

200 OK i nagłówek.
Wykonanie propagacji identyfikatora klucza trwa kilka minut. Stan aktualizacji można sprawdzić na dwa sposoby:
1. Skopiuj wartość adresu URL platformy Azure-AsyncOperation z odpowiedzi i postępuj zgodnie ze [sprawdzaniem stanu operacji asynchronicznych](#asynchronous-operations-and-status-check).
2. Wyślij żądanie GET do zasobu *klastra* i sprawdź właściwości *KeyVaultProperties* . Ostatnio zaktualizowane szczegóły identyfikatora klucza powinny zwrócić odpowiedź.

Odpowiedź na uzyskanie żądania dotyczącego zasobu *klastra* powinna wyglądać następująco po zakończeniu aktualizacji identyfikatora klucza:

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

### <a name="workspace-association-to-cluster-resource"></a>Skojarzenie obszaru roboczego z zasobem *klastra*

Aby wykonać tę operację, musisz mieć uprawnienia "Write" do obszaru roboczego i zasobu *klastra* , co obejmuje następujące akcje:

- W obszarze roboczym: Microsoft. OperationalInsights/Workspaces/Write
- W obszarze zasób *klastra* : Microsoft. OperationalInsights/klastrów/Write

> [!IMPORTANT]
> Ten krok należy wykonać dopiero po zakończeniu aprowizacji klastra Log Analytics. W przypadku kojarzenia obszarów roboczych i pozyskiwania danych przed rozpoczęciem aprowizacji pozyskiwane dane zostaną usunięte i nie będzie można ich odzyskać.

Ta operacja jest asynchroniczna i można ją ukończyć.

**PowerShell**

```powershell
$clusterResourceId = (Get-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name}).id
Set-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster -WriteAccessResourceId $clusterResourceId
```

**REST**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

**Reakcji**

200 OK i nagłówek.

Pozyskiwane dane są przechowywane w postaci zaszyfrowanej przy użyciu klucza zarządzanego po operacji skojarzenia, co może potrwać do 90 minut. Stan skojarzenia obszaru roboczego można sprawdzić na dwa sposoby:

1. Skopiuj wartość adresu URL platformy Azure-AsyncOperation z odpowiedzi i postępuj zgodnie ze [sprawdzaniem stanu operacji asynchronicznych](#asynchronous-operations-and-status-check).
2. Wyślij [obszary robocze — Pobierz](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) żądanie i obserwuj odpowiedź, skojarzony obszar roboczy będzie miał clusterResourceId w obszarze "funkcje".

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

**Reakcji**

```json
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-name",
    "provisioningState": "Succeeded",
    "sku": {
      "name": "pricing-tier-name",
      "lastSkuUpdate": "Tue, 28 Jan 2020 12:26:30 GMT"
    },
    "retentionInDays": 31,
    "features": {
      "legacy": 0,
      "searchVersion": 1,
      "enableLogAccessUsingOnlyResourcePermissions": true,
      "clusterResourceId": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name"
    },
    "workspaceCapping": {
      "dailyQuotaGb": -1.0,
      "quotaNextResetTime": "Tue, 28 Jan 2020 14:00:00 GMT",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region-name"
}
```

## <a name="cmk-kek-revocation"></a>CMK (KEK) — odwołanie

Dostęp do danych można odwołać, wyłączając klucz lub usuwając zasady dostępu do zasobów *klastra* w Key Vault. Magazyn klastra Log Analytics będzie zawsze respektują zmiany w uprawnieniach klucza w ciągu godziny lub wcześniej, a magazyn stanie się niedostępny. Wszelkie nowe dane pozyskiwane w obszarach roboczych skojarzonych z *Cluster*   zasobem klastra zostaną porzucone i nie będzie można ich odzyskać, dane są niedostępne i zapytania do tych obszarów roboczych zakończą się niepowodzeniem. Poprzednio pozyskiwane dane pozostają w magazynie, dopóki nie zostaną usunięte zasoby *klastra* i obszary robocze. Dane niedostępne podlegają zasadom przechowywania danych i zostaną usunięte po osiągnięciu okresu przechowywania. 

Dane odebrane w ciągu ostatnich 14 dni również są przechowywane w pamięci podręcznej (dysk SSD) w celu wydajnej operacji aparatu zapytań. Spowoduje to usunięcie operacji odwoływania klucza i stanie się niedostępna.

Magazyn okresowo sonduje Key Vault, aby próbować odszyfrować klucz szyfrowania, a następnie uzyskać dostęp do pozyskiwania danych i wznowienia zapytania w ciągu 30 minut.

## <a name="cmk-kek-rotation"></a>CMK (KEK)

Obrót CMK wymaga jawnej aktualizacji zasobu *klastra* przy użyciu nowej wersji klucza w Azure Key Vault. Postępuj zgodnie z instrukcjami w kroku "Aktualizowanie zasobu *klastra* z identyfikatorem klucza szczegóły". Jeśli nie zaktualizujesz nowego identyfikatora klucza w zasobie *klastra* , magazyn klastra log Analytics będzie nadal korzystać z poprzedniego klucza do szyfrowania. W przypadku wyłączenia lub usunięcia starego klucza przed zaktualizowaniem nowego klucza w zasobie *klastra* zostanie wyświetlony stan [odwoływania klucza](#cmk-kek-revocation) .

Wszystkie dane pozostają dostępne po operacji rotacji kluczy, ponieważ dane zawsze są szyfrowane przy użyciu klucza szyfrowania konta (AEK), podczas gdy AEK jest teraz szyfrowany przy użyciu nowej wersji klucza szyfrowania kluczy (KEK) w Key Vault.

## <a name="cmk-manage"></a>CMK Zarządzaj

- **Pobierz wszystkie zasoby *klastra* dla grupy zasobów**
  
  **PowerShell**

  ```powershell
  Get-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name}
  ```

  **REST**

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Reakcji**
  
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

- **Pobierz wszystkie zasoby *klastra* dla subskrypcji**
  
  **PowerShell**

  ```powershell
  Get-AzOperationalInsightsCluster
  ```

  **REST**

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **Reakcji**
    
  Taka sama odpowiedź jak dla "zasobów*klastra* dla grupy zasobów", ale w zakresie subskrypcji.

- **Aktualizowanie *rezerwacji pojemności* w zasobie *klastra***

  Gdy ilość danych ze skojarzonych obszarów roboczych zmienia się wraz z upływem czasu i chcesz odpowiednio zaktualizować poziom rezerwacji. Postępuj zgodnie z [aktualizacją zasobu *klastra* ](#update-cluster-resource-with-key-identifier-details) i podaj nową wartość pojemności. Może należeć do zakresu od 1 000 do 2 000 GB dziennie i kroków z 100. Na poziomie wyższym niż 2 000 GB dziennie skontaktuj się z osobą kontaktową firmy Microsoft, aby ją włączyć. Należy pamiętać, że nie musisz podawać treści żądania REST i zawierać jednostki SKU:

  **PowerShell**

  ```powershell
  Update-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -SkuCapacity {daily-ingestion-gigabyte}
  ```

  **REST**
   
  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": 1000
    }
  }
  ```

- **Aktualizowanie *billingType* postawtype w zasobie *klastra***

  Właściwość *rozliczenia* określa przypisanie rozliczeń dla zasobu *klastra* i jego danych:
  - *klaster* (domyślnie) — rozliczenia są przypisywane do subskrypcji hostingowej zasobu klastra
  - *obszary robocze* — rozliczenia są przypisane do subskrypcji obsługujących obszary robocze proporcjonalnie
  
  Postępuj zgodnie z [aktualizacją zasobu *klastra* ](#update-cluster-resource-with-key-identifier-details) i podaj nową wartość rozliczenia. Należy *pamiętać, że*nie musisz podawać treści żądania w trybie REST i powinny być uwzględniane:

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "properties": {
      "billingType": "cluster",
      }  
  }
  ``` 

- **Usuń skojarzenie obszaru roboczego**

  Aby wykonać tę operację, musisz mieć uprawnienia "Write" dotyczące obszaru roboczego i zasobu *klastra* . W dowolnym momencie możesz usunąć skojarzenie obszaru roboczego z zasobem *klastra* . Nowe dane pozyskiwane po operacji usuwania skojarzenia są przechowywane w magazynie Log Analytics i szyfrowane za pomocą klucza firmy Microsoft. Możesz wykonywać zapytania dotyczące danych, które zostały pozyskane do obszaru roboczego przed bezproblemowym usunięciem skojarzenia, o ile zasób *klastra* został zainicjowany i skonfigurowany z prawidłowym kluczem Key Vault.

  Ta operacja jest asynchroniczna i można ją ukończyć.

  **PowerShell**

  ```powershell
  Remove-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -Name {workspace-name} -LinkedServiceName cluster
  ```

  **REST**

  ```rest
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Reakcji**

  200 OK i nagłówek.

  Dane pozyskane po operacji usunięcia skojarzenia są przechowywane w magazynie Log Analytics, co może potrwać 90 minut. Stan nieskojarzenia obszaru roboczego można sprawdzić na dwa sposoby:

  1. Skopiuj wartość adresu URL platformy Azure-AsyncOperation z odpowiedzi i postępuj zgodnie ze [sprawdzaniem stanu operacji asynchronicznych](#asynchronous-operations-and-status-check).
  2. Wyślij [obszary robocze — Pobierz](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) żądanie i obserwuj odpowiedź, nieskojarzone obszary robocze nie będą miały *clusterResourceId* w obszarze *funkcje*.

- **Sprawdź stan powiązania obszaru roboczego** Wykonaj operację get w obszarze roboczym i sprawdź, czy *clusterId* jest obecny w odpowiedzi. Skojarzony obszar roboczy będzie miał Właściwość *clusterId* .

  **PowerShell**

  ```powershell
  Get-AzOperationalInsightsWorkspace -ResourceGroupName {resource-group-name} -Name {workspace-name}
  ```

- **Usuwanie zasobu *klastra***

  Aby wykonać tę operację, musisz mieć uprawnienia "Write" dotyczące zasobu *klastra* . Operacja usuwania nietrwałego jest wykonywana w celu zezwalania na odzyskiwanie zasobu *klastra* , w tym jego danych w ciągu 14 dni, bez względu na to, czy usunięcie było przypadkowe czy celowe. Nazwa zasobu *klastra* pozostaje zarezerwowana w okresie usuwania nietrwałego i nie można utworzyć nowego klastra o takiej nazwie. Po okresie usuwania nietrwałego nazwa zasobu *klastra* zostanie wydana, a zasoby *klastra* i dane są trwale usuwane i nie można ich odzyskać. Wszystkie skojarzone obszary robocze nie są usuwane z zasobów *klastra* podczas operacji usuwania. Nowe pozyskiwane dane są przechowywane w magazynie Log Analytics i szyfrowane za pomocą klucza firmy Microsoft. 
  
  Operacje obszarów roboczych, które nie są skojarzone, są asynchroniczne i może upłynąć do 90 minut.

  **PowerShell**

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name}
  ```

  **REST**
  
  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Reakcji**

  200 OK

- **Odzyskiwanie zasobu *klastra* i danych** 
  
  Zasób *klastra* , który został usunięty w ciągu ostatnich 14 dni, jest w stanie usuwania nietrwałego i można go odzyskać z danymi. Ponieważ wszystkie obszary robocze zostały usunięte z zasobu *klastra* z usunięciem zasobów *klastra* , należy ponownie skojarzyć obszary robocze po odzyskaniu do szyfrowania CMK. Operacja odzyskiwania jest wykonywana ręcznie przez grupę produktów. Użyj Twojego kanału firmy Microsoft na potrzeby żądań odzyskiwania.

## <a name="limitationsandconstraints"></a>Ograniczenia i ograniczenia

-CMK jest obsługiwana w dedykowanym klastrze Log Analytics i jest odpowiednia dla klientów wysyłających 1 TB dziennie lub dłużej.

— Maksymalna liczba zasobów *klastra*   na region i subskrypcja wynosi 2.

— Możesz skojarzyć obszar roboczy z zasobem *klastra*   , a następnie usunąć jego skojarzenie, jeśli CMK nie jest wymagany dla obszaru roboczego.Liczba skojarzeń obszaru roboczego w określonym obszarze roboczym w okresie 30 dni jest ograniczona do 2

— Skojarzenie obszaru *Cluster*roboczego z   zasobem klastra powinno być przeprowadzane dopiero po sprawdzeniu, czy zakończono inicjowanie obsługi klastra log Analytics.Dane wysyłane do obszaru roboczego przed ukończeniem zostaną usunięte i nie będzie można ich odzyskać.

-CMK szyfrowanie ma zastosowanie do nowo wprowadzonych danych po      konfiguracji CMK.Dane pozyskiwane przed      konfiguracją CMK pozostają zaszyfrowane za pomocą klucza firmy Microsoft.Możesz wykonywać zapytania dotyczące danych pozyskiwanych      przed bezproblemową konfiguracją CMK i po niej.

-Azure Key Vault musi być skonfigurowany jako możliwy do odzyskania.Te właściwości nie są domyślnie włączone i należy je skonfigurować przy użyciu interfejsu wiersza polecenia lub programu PowerShell:

  - [Soft Delete](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) 
 Usuwanie      nietrwałe musi być włączona    -  [Ochrona przed przeczyszczeniem](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection)   powinna być włączona, aby chronić przed usunięciem wpisu tajnego lub magazynu nawet po usunięciu nietrwałego.

- *Klaster*   przeniesienie zasobu do innej grupy zasobów lub subskrypcji      nie jest obecnie obsługiwane.

-Azure Key Vault, zasób *klastra*   i powiązane obszary robocze muszą znajdować się w tym samym regionie i w tej samej dzierżawie Azure Active Directory (Azure AD), ale mogą znajdować się w różnych subskrypcjach.

— Skojarzenie obszaru roboczego z zasobem *klastra*zakończy   się niepowodzeniem, jeśli jest ono      skojarzone z innym *Cluster*   zasobem klastra

## <a name="troubleshooting"></a>Rozwiązywanie problemów

- Zachowanie z dostępnością Key Vault
  - W normalnej operacji — pamięć podręczna magazynu AEK przez krótki okresy i powraca do Key Vault, aby okresowo wycofać.
    
  - Błędy połączeń przejściowych — usługa Storage obsługuje błędy przejściowe (przekroczenia limitu czasu, błędy połączeń, problemy z usługą DNS), dzięki czemu klucze mogą pozostać w pamięci podręcznej przez krótki czas i jest to zbyt małe Blips w dostępności. Możliwości zapytań i pozyskiwania kontynuują działanie bez przeszkód.
    
  - Lokacja na żywo — niedostępność przez około 30 minut spowoduje, że konto magazynu stanie się niedostępne. Funkcja zapytania jest niedostępna, a dane pozyskiwane są buforowane przez kilka godzin przy użyciu klawisza Microsoft, aby uniknąć utraty danych. Po przywróceniu dostępu do Key Vault jest on dostępny, a tymczasowe dane przechowywane w pamięci podręcznej są przechowywane w magazynie danych i szyfrowane za pomocą CMK.

  - Key Vault szybkość dostępu — częstotliwość, z jaką Azure Monitor dostęp do magazynu Key Vault dla operacji zawijania i rozwinięcia, wynosi od 6 do 60 sekund.

- Jeśli utworzysz zasób *klastra* i natychmiast określisz KeyVaultProperties, operacja może zakończyć się niepowodzeniem, ponieważ nie można zdefiniować zasad dostępu do momentu przypisania tożsamości systemu do zasobu *klastra* .

- W przypadku zaktualizowania istniejącego zasobu *klastra* przy użyciu KeyVaultProperties i braku zasad dostępu do klucza "Get" w Key Vault operacja zakończy się niepowodzeniem.

- Próba usunięcia zasobu *klastra* skojarzonego z obszarem roboczym spowoduje niepowodzenie operacji usuwania.

- Jeśli wystąpi błąd konfliktu podczas tworzenia zasobu *klastra* — może to spowodować, że zasób *klastra* został usunięty w ciągu ostatnich 14 dni i jest w okresie usuwania nietrwałego. Nazwa zasobu *klastra* pozostaje zarezerwowana w okresie usuwania nietrwałego i nie można utworzyć nowego klastra o takiej nazwie. Nazwa jest wydawana po okresie usuwania nietrwałego, gdy zasób *klastra* zostanie trwale usunięty.

- W przypadku aktualizowania zasobu *klastra* , gdy operacja jest w toku, operacja zakończy się niepowodzeniem.

- Jeśli nie można wdrożyć zasobu *klastra* , sprawdź, czy Azure Key Vault, zasób *klastra*   i skojarzone obszary robocze log Analytics znajdują się w tym samym regionie. Mogą znajdować się w różnych subskrypcjach.

- Jeśli zaktualizujesz wersję klucza w Key Vault i nie zaktualizujesz nowego identyfikatora klucza w zasobie *klastra* , klaster log Analytics będzie nadal korzystać z poprzedniego klucza i Twoje dane staną się niedostępne. Zaktualizuj szczegóły nowego identyfikatora klucza w zasobie *klastra* w celu wznowienia pozyskiwania danych i umożliwienia wykonywania zapytań dotyczących danych.

- Aby uzyskać pomoc techniczną i powiązana z kluczem zarządzanym przez klienta, Użyj kontaktów do firmy Microsoft.
