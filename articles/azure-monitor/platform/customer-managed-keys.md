---
title: Azure Monitor konfigurację klucza zarządzanego przez klienta
description: Informacje i kroki służące do konfigurowania klucza zarządzanego przez klienta (CMK) w celu szyfrowania danych w obszarach roboczych Log Analytics przy użyciu klucza Azure Key Vault.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 04/12/2020
ms.openlocfilehash: 25fdb0aefacbdd9c2630a69981a67821ac155786
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81758807"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>Azure Monitor konfigurację klucza zarządzanego przez klienta 

Ten artykuł zawiera ogólne informacje i instrukcje dotyczące konfigurowania kluczy zarządzanych przez klienta (CMK) dla obszarów roboczych Log Analytics i składników Application Insights. Po skonfigurowaniu wszystkie dane wysyłane do obszarów roboczych lub składników są szyfrowane za pomocą klucza Azure Key Vault.

Zalecamy przejrzenie [ograniczeń i ograniczeń](#limitations-and-constraints) poniżej przed rozpoczęciem konfiguracji.

## <a name="disclaimers"></a>Zastrzeżenia

- Azure Monitor CMK to funkcja wczesnego dostępu i włączona dla zarejestrowanej subskrypcji.

- Wdrożenie CMK opisane w tym artykule jest dostarczane w jakości produkcyjnej i obsługiwane w taki sposób, że jest to funkcja wczesnego dostępu.

- Funkcja CMK jest dostarczana w dedykowanym klastrze magazynów danych, który jest klastrem usługi Azure Eksplorator danych (ADX) i jest przeznaczony dla klientów wysyłających 1 TB dziennie. 

- Model cen CMK jest obecnie niedostępny i nie został uwzględniony w tym artykule. Model cenowy dedykowanego klastra ADX jest oczekiwany w drugim kwartale roku kalendarzowego (CY) 2020 i będzie miał zastosowanie do wszystkich istniejących wdrożeń CMK.

- W tym artykule opisano konfigurację CMK dla obszarów roboczych Log Analytics. Program CMK for Application Insights Components jest również obsługiwany przy użyciu tego artykułu, podczas gdy różnice są wymienione w dodatku.

> [!NOTE]
> Log Analytics i Application Insights używają tej samej platformy do przechowywania danych i aparatu zapytań.
> Te dwa magazyny są przełączane przez integrację Application Insights w Log Analytics, aby utworzyć pojedynczy magazyn ujednoliconych dzienników w Azure Monitor. Ta zmiana jest planowana w drugim kwartale roku kalendarzowego 2020. Jeśli nie musisz wdrażać CMK dla danych Application Insights, zalecamy oczekiwanie na zakończenie konsolidacji, ponieważ takie wdrożenia zostaną zakłócone przez konsolidację i konieczne będzie ponowne skonfigurowanie CMK po zakończeniu migracji do Log Analytics obszaru roboczego. Wartość 1 TB na dzień jest stosowana na poziomie klastra i do momentu ukończenia konsolidacji w drugim kwartale Application Insights i Log Analytics wymagają oddzielnych klastrów.

## <a name="customer-managed-key-cmk-overview"></a>Klucz zarządzany przez klienta (CMK) — Omówienie

[Szyfrowanie w spoczynku](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) to wspólne wymagania w zakresie ochrony prywatności i bezpieczeństwa w organizacjach. Możesz pozwolić, aby platforma Azure całkowicie zarządzała szyfrowaniem w stanie spoczynku, podczas gdy masz różne opcje, aby dokładnie zarządzać szyfrowaniem lub kluczami szyfrowania.

Magazyn danych Azure Monitor zapewnia, że wszystkie dane zaszyfrowane przy użyciu kluczy zarządzanych przez platformę Azure są przechowywane w usłudze Azure Storage. Azure Monitor udostępnia również opcję szyfrowania danych przy użyciu własnego klucza przechowywanego w [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview), do którego uzyskuje się dostęp przy użyciu uwierzytelniania [tożsamości zarządzanej](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) przypisanego przez system. Ten klucz może być [chroniony przez oprogramowanie lub sprzęt-moduł HSM](https://docs.microsoft.com/azure/key-vault/key-vault-overview).
Azure Monitor korzystania z szyfrowania jest taka sama jak w sposobie działania [szyfrowania usługi Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption) .

Częstotliwość, z jaką Azure Monitor dostęp do magazynu Key Vault dla operacji zawijania i rozwinięcia, wynosi od 6 do 60 sekund.Azure Monitor Storage zawsze uwzględnia zmiany w uprawnieniach klucza w ciągu godziny.

Dane odebrane w ciągu ostatnich 14 dni również są przechowywane w pamięci podręcznej (dysk SSD) w celu wydajnej operacji aparatu zapytań. Te dane pozostają zaszyfrowane przy użyciu kluczy firmy Microsoft bez względu na konfigurację CMK, ale pracujemy nad szyfrowaniem SSD przy użyciu CMK w pierwszej połowie 2020.

## <a name="how-cmk-works-in-azure-monitor"></a>Jak działa CMK w Azure Monitor

Azure Monitor korzysta z zarządzanej tożsamości przypisanej do systemu, aby udzielić dostępu do Azure Key Vault.Tożsamość zarządzana przypisana przez system może być skojarzona tylko z pojedynczym zasobem platformy Azure. Tożsamość Azure Monitorgo magazynu danych (klastra ADX) jest obsługiwana na poziomie klastra, co oznacza, że funkcja CMK jest dostarczana w dedykowanym klastrze ADX. Aby obsługiwać CMK w wielu obszarach roboczych, nowy zasób Log Analytics (*klaster*) pełni rolę pośredniego połączenia tożsamości między Key Vault i obszarami roboczymi log Analytics. Pojęcie to jest zgodne z ograniczeniem tożsamości przypisanym do systemu, a tożsamość jest utrzymywana między klastrem ADX i zasobem *klastra* log Analytics, podczas gdy dane wszystkich skojarzonych obszarów roboczych są chronione za pomocą klucza Key Vault. Magazyn klastra underlay ADX korzysta z tożsamości\'zarządzanej skojarzonej z zasobem *klastra* w celu uwierzytelniania i uzyskiwania dostępu do Azure Key Vault za pośrednictwem Azure Active Directory.

![CMK — Omówienie](media/customer-managed-keys/cmk-overview-8bit.png)
1.    Key Vault klienta.
2.    Zasób *klastra* log Analytics klienta mającego zarządzaną tożsamość z uprawnieniami do Key Vault — tożsamość jest obsługiwana na poziomie magazynu danych (ADX klaster).
3.    Azure Monitor dedykowany klaster ADX.
4.    Obszary robocze klientów skojarzone z zasobem *klastra* na potrzeby szyfrowania CMK.

## <a name="encryption-keys-management"></a>Zarządzanie kluczami szyfrowania

Istnieją trzy typy kluczy, które wiążą się z szyfrowaniem danych magazynu:

- Klucz szyfrowania klucza ( **KEK** ) (CMK)
- Klucz szyfrowania konta **AEK**
- **DEK** Klucz szyfrowania danych

Mają zastosowanie następujące zasady:

- Konta magazynu ADX generują unikatowy klucz szyfrowania dla każdego konta magazynu, który jest znany jako AEK.

- AEK jest używany do wyprowadzania DEKs, które są kluczami, które są używane do szyfrowania poszczególnych bloków danych zapisywana na dysku.

- Podczas konfigurowania klucza w Key Vault i odwoływania się do niego w ramach zasobu *klastra* usługa Azure Storage wysyła żądania do Azure Key Vault, aby zawijać i deAEKać dane w celu szyfrowania i odszyfrowywania danych.

- KEK nigdy nie opuści Key Vault i w przypadku klucza HSM nigdy nie opuszcza sprzętu.

- Usługa Azure Storage korzysta z zarządzanej tożsamości skojarzonej z zasobem *klastra* w celu uwierzytelniania i dostępu do Azure Key Vault za pośrednictwem Azure Active Directory.

## <a name="cmk-provisioning-procedure"></a>Procedura inicjowania obsługi CMK

Aby uzyskać Application Insights konfigurację CMK, postępuj zgodnie z zawartością dodatku dla kroków 3 i 6.

1. Listy dozwolonych subskrypcji — jest to wymagane w przypadku tej funkcji wczesnego dostępu
2. Tworzenie Azure Key Vault i przechowywanie klucza
3. Tworzenie zasobu *klastra*
4. Inicjowanie obsługi Azure Monitor magazynu danych (klastra ADX)
5. Przyznawanie uprawnień do Key Vault
6. Kojarzenie Log Analytics obszarów roboczych

Procedura nie jest obecnie obsługiwana w interfejsie użytkownika, a proces aprowizacji jest wykonywany za pośrednictwem interfejsu API REST.

> [!IMPORTANT]
> Wszystkie żądania interfejsu API muszą zawierać Token autoryzacji okaziciela w nagłówku żądania.

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

Niektóre operacje w tej procedurze konfiguracji są uruchamiane asynchronicznie, ponieważ nie mogą być szybko wykonywane. Odpowiedź na operację asynchroniczną początkowo zwraca kod stanu HTTP 200 (OK) i nagłówek z właściwością *Azure-AsyncOperation* po zaakceptowaniu:
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-03-01-preview"
```

Stan operacji asynchronicznej można sprawdzić, wysyłając żądanie GET do wartości nagłówka *Azure-AsyncOperation* :
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

### <a name="subscription-whitelisting"></a>Listy dozwolonych subskrypcji

CMK to funkcja wczesnego dostępu. Subskrypcje, w których planujesz utworzyć zasoby *klastra* , muszą być listy dozwolonych wcześniej przez grupę produktów platformy Azure. Użyj swoich kontaktów do firmy Microsoft, aby podać identyfikatory subskrypcji.

> [!IMPORTANT]
> Funkcja CMK jest regionalna. Azure Key Vault, zasób *klastra* i powiązane obszary robocze log Analytics muszą znajdować się w tym samym regionie, ale mogą znajdować się w różnych subskrypcjach.

### <a name="storing-encryption-key-kek"></a>Przechowywanie klucza szyfrowania (KEK)

Utwórz lub użyj Azure Key Vault już do wygenerowania lub zaimportuj klucz, który ma być używany do szyfrowania danych. Azure Key Vault należy skonfigurować jako możliwy do odzyskania, aby chronić klucz i dostęp do danych w Azure Monitor. Możesz sprawdzić tę konfigurację w obszarze właściwości w Key Vault, należy włączyć zarówno funkcję *usuwania nietrwałego* , jak i *przeczyszczania* .

![Ustawienia ochrony usuwania nietrwałego i przeczyszczania](media/customer-managed-keys/soft-purge-protection.png)

Te ustawienia są dostępne za pośrednictwem interfejsu wiersza polecenia i programu PowerShell:
- [Usuwanie nietrwałe](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- [Przeczyść zabezpieczenia ochrony](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) przed wymuszeniem usunięcia wpisu tajnego/magazynu nawet po usunięciu nietrwałego

### <a name="create-cluster-resource"></a>Utwórz zasób *klastra*

Ten zasób jest używany jako połączenie tożsamości pośredniej między Key Vault i obszarami roboczymi Log Analytics. Po otrzymaniu potwierdzenia, że subskrypcje zostały listy dozwolonyche, utwórz zasób *klastra* log Analytics w regionie, w którym znajdują się obszary robocze. Application Insights i Log Analytics wymagają oddzielnych typów zasobów *klastra* . Typ zasobu *klastra* jest definiowany w czasie tworzenia przez ustawienie właściwości *clustertype* na wartość *LogAnalytics*lub *ApplicationInsights*. Nie można zmienić typu zasobu klastra po.

Aby uzyskać Application Insights konfigurację CMK, postępuj zgodnie z zawartością dodatku.

Podczas tworzenia zasobu *klastra* należy określić poziom rezerwacji pojemności (SKU). Poziom rezerwacji pojemności może należeć do zakresu od 1 000 do 2 000 GB dziennie i można go zaktualizować w krokach 100 w późniejszym czasie. Jeśli potrzebujesz poziomu rezerwacji pojemności większej niż 2 000 GB dziennie, skontaktuj się z osobą kontaktową firmy Microsoft, aby ją włączyć. Ta właściwość nie ma wpływu na rozliczenia, gdy zostanie wprowadzony model cenowy dedykowanego klastra, rozliczenie będzie miało zastosowanie do wszystkich istniejących wdrożeń CMK.

**Utwórz**

To żądanie Menedżer zasobów jest operacją asynchroniczną.

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
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
    "clusterType": "LogAnalytics",
    },
  "location": "<region-name>",
}
```
Tożsamość jest przypisywana do zasobu *klastra* podczas tworzenia.

**Reakcji**

200 OK i nagłówek.
W okresie wczesnego dostępu do funkcji klaster ADX jest inicjowany ręcznie. Mimo że trwa inicjowanie obsługi klastra ADX przez cały czas, można sprawdzić stan aprowizacji na dwa sposoby:
1. Skopiuj wartość adresu URL platformy Azure-AsyncOperation z odpowiedzi i postępuj zgodnie ze [sprawdzaniem stanu operacji asynchronicznych](#asynchronous-operations-and-status-check).
2. Wyślij żądanie GET do zasobu *klastra* i sprawdź wartość *provisioningState* . Jest on *ProvisioningAccount* podczas inicjowania obsługi administracyjnej i zakończył *się pomyślnie* .

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Inicjowanie obsługi Azure Monitor magazynu danych (klastra ADX)

W okresie wczesnego dostępu do funkcji klaster ADX jest inicjowany ręcznie przez zespół produktu po zakończeniu poprzednich kroków. Skorzystaj z kanału firmy Microsoft w tym kroku i podaj odpowiedź zasobu *klastra* . 

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
```

> [!IMPORTANT]
> Skopiuj i Zapisz odpowiedź, ponieważ będziesz potrzebować szczegółowych informacji w następnych krokach.

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
    "clusterType": "LogAnalytics", 
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

Zaktualizuj Key Vault przy użyciu nowych zasad dostępu, które przyznają uprawnienia do zasobu *klastra* . Te uprawnienia są używane przez underlay Azure Monitor Storage na potrzeby szyfrowania danych. Otwórz Key Vault w Azure Portal, a następnie kliknij pozycję "zasady dostępu" i "+ Dodaj zasady dostępu", aby utworzyć zasady z następującymi ustawieniami:

- Uprawnienia klucza: Wybierz uprawnienia "Pobierz", "Zawijanie klucza" i "Cofnij Zawijanie klucza".
- Wybierz podmiot zabezpieczeń: wprowadź wartość identyfikatora podmiotu zabezpieczeń, która została zwrócona w odpowiedzi w poprzednim kroku.

![Przyznawanie uprawnień Key Vault](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

Uprawnienie *Get* jest wymagane do sprawdzenia, czy Key Vault jest skonfigurowany jako możliwy do odzyskania w celu ochrony klucza i dostępu do danych Azure monitor.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Aktualizowanie zasobu klastra przy użyciu szczegółów identyfikatora klucza

Ten krok jest wykonywany podczas początkowej i w przyszłych wersjach kluczowych aktualizacji w Key Vault. Informuje Magazyn Azure Monitor o wersji klucza, który ma być używany do szyfrowania danych. Po zaktualizowaniu nowy klucz jest używany do zawijania i depakowania do klucza magazynu (AEK).

Aby zaktualizować zasób *klastra* przy użyciu Key Vault szczegóły *identyfikatora klucza* , wybierz bieżącą wersję klucza w Azure Key Vault, aby uzyskać szczegóły identyfikatora klucza.

![Przyznawanie uprawnień Key Vault](media/customer-managed-keys/key-identifier-8bit.png)

Zaktualizuj KeyVaultProperties zasobów *klastra* przy użyciu szczegółów identyfikatora klucza.

**Aktualizacja**

To żądanie Menedżer zasobów jest operacją asynchroniczną podczas aktualizowania szczegółów identyfikatora klucza, podczas gdy jest ona synchroniczna podczas aktualizowania wartości wydajności.

> [!Warning]
> Należy podać pełną treść w aktualizacji zasobów *klastra* , która obejmuje *tożsamość*, *jednostkę SKU*, *KeyVaultProperties* i *lokalizację*. Brak *KeyVaultProperties* szczegóły usunie identyfikator klucza z zasobu *klastra* i spowoduje [odwołanie klucza](#cmk-kek-revocation).

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
     "capacity": 1000
     },
   "properties": {
     "KeyVaultProperties": {
       KeyVaultUri: "https://<key-vault-name>.vault.azure.net",
       KeyName: "<key-name>",
       KeyVersion: "<current-version>"
       },
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
    "KeyVaultProperties": {
      KeyVaultUri: "https://key-vault-name.vault.azure.net",
      KeyName: "key-name",
      KeyVersion: "current-version"
      },
    "provisioningState": "Succeeded",
    "clusterType": "LogAnalytics", 
    "clusterId": "cluster-id"
  },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
}
```

### <a name="workspace-association-to-cluster-resource"></a>Skojarzenie obszaru roboczego z zasobem *klastra*
Aby uzyskać Application Insights konfigurację CMK, postępuj zgodnie z zawartością dodatku dla tego kroku.

Aby wykonać tę operację, musisz mieć uprawnienia "Write" do obszaru roboczego i zasobu *klastra* , co obejmuje następujące akcje:

- W obszarze roboczym: Microsoft. OperationalInsights/Workspaces/Write
- W obszarze zasób *klastra* : Microsoft. OperationalInsights/klastrów/Write

> [!IMPORTANT]
> Ten krok należy wykonać tylko po zainicjowaniu obsługi klastra ADX. W przypadku kojarzenia obszarów roboczych i pozyskiwania danych przed rozpoczęciem aprowizacji pozyskiwane dane zostaną usunięte i nie będzie można ich odzyskać.

**Kojarzenie obszaru roboczego**

To żądanie Menedżer zasobów jest operacją asynchroniczną.

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
    "retentionInDays": days,
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

Możesz odwołać dostęp do swoich danych, wyłączając klucz lub usuwając zasady dostępu do zasobów *klastra* w Key Vault. Azure Monitor Storage zawsze będzie uwzględniać zmiany w uprawnieniach klucza w ciągu godziny, zwykle wcześniej, a magazyn stanie się niedostępny. Wszelkie dane pozyskane do obszarów roboczych skojarzonych z zasobem *klastra* zostaną usunięte, a zapytania zakończą się niepowodzeniem. Wcześniej pozyskiwane dane pozostają niedostępne w magazynie Azure Monitor, o ile jesteś zasobem *klastra* , a obszary robocze nie zostaną usunięte. Dane niedostępne podlegają zasadom przechowywania danych i zostaną usunięte po osiągnięciu okresu przechowywania.

Magazyn będzie okresowo sondował Key Vault, aby próbować odszyfrować klucz szyfrowania, a następnie uzyskać dostęp do pozyskiwania danych i wznowienia zapytania w ciągu 30 minut.

## <a name="cmk-kek-rotation"></a>CMK (KEK)

Obrót CMK wymaga jawnej aktualizacji zasobu *klastra* z nową wersją klucza w Azure Key Vault. Aby zaktualizować Azure Monitor przy użyciu nowej wersji klucza, postępuj zgodnie z instrukcjami w kroku "Aktualizowanie zasobu *klastra* z identyfikatorem klucza szczegóły". Jeśli zaktualizujesz wersję klucza w Key Vault i nie zaktualizujesz nowego identyfikatora klucza w zasobie *klastra* , Azure monitor magazyn będzie nadal korzystać z poprzedniego klucza.
Wszystkie dane są dostępne po operacji rotacji kluczy, w tym danych pozyskanych przed rotacją i po niej, ponieważ wszystkie dane pozostają zaszyfrowane za pomocą klucza szyfrowania konta (AEK), podczas gdy AEK jest teraz szyfrowany przy użyciu nowej wersji klucza szyfrowania kluczy (KEK).

## <a name="limitations-and-constraints"></a>Ograniczenia i ograniczenia

- Funkcja CMK jest obsługiwana na poziomie klastra ADX i wymaga dedykowanego klastra Azure Monitor ADX z wymaganiem wysyłania 1 TB na dzień lub dłużej.

- Maksymalna liczba zasobów *klastra* na subskrypcję jest ograniczona do 2

- Skojarzenie zasobów *klastra* z obszarem roboczym należy przeprowadzić dopiero po sprawdzeniu, czy zakończono Inicjowanie obsługi klastra ADX. Dane wysyłane do obszaru roboczego przed ukończeniem aprowizacji zostaną usunięte i nie będzie można ich odzyskać.

- Szyfrowanie CMK ma zastosowanie do nowo wprowadzonych danych po konfiguracji CMK. Dane pozyskiwane przed konfiguracją CMK pozostają zaszyfrowane za pomocą klucza firmy Microsoft. Możesz wykonywać zapytania dotyczące danych pozyskiwanych przed bezproblemową konfiguracją CMK i po niej.

- Można usunąć skojarzenie obszaru roboczego z zasobu *klastra* podczas decydowania, że CMK nie jest wymagany dla określonego obszaru roboczego. Nowe dane pozyskiwane po operacji usuwania skojarzenia są przechowywane w magazynie udostępnionym Log Analytics, ponieważ wcześniej były skojarzone z zasobem *klastra* . Można wysyłać zapytania o dane pozyskiwane przed bezproblemowym i po usunięciu skojarzenia, jeśli zasób *klastra* zostanie zainicjowany i skonfigurowany z prawidłowym kluczem Key Vault.

- Azure Key Vault musi być skonfigurowany jako możliwy do odzyskania. Te właściwości nie są domyślnie włączone i należy je skonfigurować przy użyciu interfejsu wiersza polecenia lub programu PowerShell:

  - [Usuwanie nietrwałe](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) musi być włączone
  - [Ochrona przed przeczyszczeniem](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) powinna być włączona, aby zabezpieczyć przed wymuszeniem usunięcia wpisu tajnego lub magazynu nawet po usunięciu nietrwałego

- Application Insights i Log Analytics wymagają oddzielnych zasobów *klastra* . Typ zasobu *klastra* jest definiowany w czasie tworzenia przez ustawienie właściwości "clustertype" na wartość "LogAnalytics" lub "ApplicationInsights". Nie można zmienić typu zasobu *klastra* .

- Zasób *klastra* przeniesiony do innej grupy zasobów lub subskrypcji nie jest obecnie obsługiwany.

- Azure Key Vault, zasób *klastra* i powiązane obszary robocze muszą znajdować się w tym samym regionie i w tej samej dzierżawie Azure Active Directory (Azure AD), ale mogą znajdować się w różnych subskrypcjach.

- Skojarzenie obszaru roboczego z zasobem *klastra* zakończy się niepowodzeniem, jeśli jest ono skojarzone z innym zasobem *klastra*

## <a name="troubleshooting-and-management"></a>Rozwiązywanie problemów i zarządzanie

- Dostępność Key Vault
    - W normalnej operacji — pamięć podręczna magazynu AEK przez krótki okresy i powraca do Key Vault, aby okresowo wycofać.
    
    - Błędy połączeń przejściowych — usługa Storage obsługuje błędy przejściowe (przekroczenia limitu czasu, błędy połączeń, problemy z usługą DNS), dzięki czemu klucze mogą pozostać w pamięci podręcznej przez krótki czas i jest to zbyt małe Blips w dostępności. Możliwości zapytań i pozyskiwania kontynuują działanie bez przeszkód.
    
    - Lokacja na żywo — niedostępność przez około 30 minut spowoduje, że konto magazynu stanie się niedostępne. Funkcja zapytania jest niedostępna, a dane pozyskiwane są buforowane przez kilka godzin przy użyciu klawisza Microsoft, aby uniknąć utraty danych. Po przywróceniu dostępu do Key Vault jest on dostępny, a tymczasowe dane przechowywane w pamięci podręcznej są przechowywane w magazynie danych i szyfrowane za pomocą CMK.

- Jeśli utworzysz zasób *klastra* i natychmiast określisz KeyVaultProperties, operacja może zakończyć się niepowodzeniem, ponieważ nie można zdefiniować zasad dostępu do momentu przypisania tożsamości systemu do zasobu *klastra* .

- W przypadku zaktualizowania istniejącego zasobu *klastra* przy użyciu KeyVaultProperties i braku zasad dostępu do klucza "Get" w Key Vault operacja zakończy się niepowodzeniem.

- Próba usunięcia zasobu *klastra* skojarzonego z obszarem roboczym spowoduje niepowodzenie operacji usuwania.

- Jeśli wystąpi błąd konfliktu podczas tworzenia zasobu *klastra* — może to spowodować, że zasób *klastra* został usunięty w ciągu ostatnich 14 dni i jest w okresie usuwania nietrwałego. Nazwa zasobu *klastra* pozostaje zarezerwowana w okresie usuwania nietrwałego i nie można utworzyć nowego klastra o takiej nazwie. Nazwa jest wydawana po okresie usuwania nietrwałego, gdy zasób *klastra* zostanie trwale usunięty.

- Pobierz wszystkie zasoby *klastra* dla grupy zasobów:

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
           "KeyVaultProperties": {
              KeyVaultUri: "https://key-vault-name.vault.azure.net",
              KeyName: "key-name",
              KeyVersion: "current-version"
              },
          "provisioningState": "Succeeded",
          "clusterType": "LogAnalytics", 
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

- Pobierz wszystkie zasoby *klastra* dla subskrypcji:

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **Reakcji**
    
  Taka sama odpowiedź jak dla "zasobów*klastra* dla grupy zasobów", ale w zakresie subskrypcji.

- Aktualizowanie *rezerwacji pojemności* w zasobie *klastra* — w przypadku zmiany ilości danych na skojarzone obszary robocze, a chcesz zaktualizować poziom rezerwacji pojemności dla zagadnień związanych z rozliczeniami, postępuj zgodnie z [aktualizacją zasobu *klastra* ](#update-cluster-resource-with-key-identifier-details) i podaj nową wartość pojemności. Poziom rezerwacji pojemności może należeć do zakresu od 1 000 do 2 000 GB dziennie i kroków z 100. Na poziomie wyższym niż 2 000 GB dziennie skontaktuj się z osobą kontaktową firmy Microsoft, aby ją włączyć.

- Usuwanie zasobu *klastra* — operacja usuwania nietrwałego jest wykonywana w celu zezwalania na odzyskiwanie zasobu *klastra* , w tym jego danych w ciągu 14 dni, bez względu na to, czy usunięcie było przypadkowe czy celowe. Nazwa zasobu *klastra* pozostaje zarezerwowana w okresie usuwania nietrwałego i nie można utworzyć nowego klastra o takiej nazwie. Po okresie usuwania nietrwałego nazwa zasobu *klastra* zostanie wydana, a zasoby *klastra* i dane są trwale usuwane i nie można ich odzyskać. Wszystkie skojarzone obszary robocze są usuwane z zasobu *klastra* podczas operacji usuwania. Nowe pozyskiwane dane są przechowywane w magazynie udostępnionym Log Analytics i szyfrowane za pomocą klucza firmy Microsoft. Operacja usuwania skojarzonych obszarów roboczych jest asynchroniczna.

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Reakcji**

  200 OK

- Odzyskaj zasób *klastra* i dane — zasób *klastra* , który został usunięty w ciągu ostatnich 14 dni, jest w stanie usuwania nietrwałego i można go odzyskać. Jest to wykonywane ręcznie przez grupę produktów. Użyj Twojego kanału firmy Microsoft na potrzeby żądań odzyskiwania.

## <a name="appendix"></a>Dodatek

Application Insights jest również obsługiwany klucz zarządzany przez klienta (CMK), jednak należy wziąć pod uwagę następujące zmiany, aby ułatwić Planowanie wdrożenia CMK dla składników usługi Application Insights.

Log Analytics i Application Insights używają tej samej platformy do przechowywania danych i aparatu zapytań. Te dwa magazyny są przełączane za pośrednictwem integracji Application Insights w Log Analytics, aby zapewnić jeden ujednolicony magazyn dzienników w Azure Monitor w drugim kwartale
2020. Ta zmiana spowoduje przełączenie danych usługi Application Insights do obszarów roboczych Log Analytics i przeprowadzenie zapytań, szczegółowych informacji i innych ulepszeń w czasie, gdy konfiguracja CMK w obszarze roboczym zostanie również zastosowana do Application Insights danych.

> [!NOTE]
> Jeśli nie musisz wdrażać CMK na potrzeby danych usługi Application Insights przed integracją, zalecamy oczekiwanie na Application Insights CMK, ponieważ takie wdrożenia zostaną zakłócone przez integrację i konieczne będzie ponowne skonfigurowanie CMK po migracji do Log Analytics obszaru roboczego. Wartość 1 TB na dzień jest stosowana na poziomie klastra i do momentu ukończenia konsolidacji w drugim kwartale Application Insights i Log Analytics wymagają oddzielnych klastrów.

## <a name="application-insights-cmk-configuration"></a>Application Insights konfiguracja CMK

Konfiguracja Application Insights CMK jest identyczna z procesem przedstawionym w tym artykule, w tym ograniczenia i rozwiązywanie problemów, z wyjątkiem następujących kroków:

- Tworzenie zasobu *klastra*
- Kojarzenie składnika z zasobem *klastra*

Podczas konfigurowania CMK dla Application Insights należy wykonać te czynności zamiast wymienionych powyżej.

### <a name="create-a-cluster-resource"></a>Tworzenie zasobu *klastra*

Ten zasób jest używany jako połączenie tożsamości pośredniej między Key Vault i składnikami. Po otrzymaniu potwierdzenia, że subskrypcje zostały listy dozwolonyche, utwórz zasób *klastra* log Analytics w regionie, w którym znajdują się składniki. Typ zasobu *klastra* jest definiowany w czasie tworzenia przez ustawienie właściwości *clustertype* na wartość *LogAnalytics*lub *ApplicationInsights*. Powinien być *ApplicationInsights* dla Application Insights CMK. Nie można zmienić ustawienia *klastratype* po konfiguracji.

**Utwórz**

To żądanie Menedżer zasobów jest operacją asynchroniczną.

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
    "clusterType":"ApplicationInsights"
    },
  "location": "<region-name>"
}
```

**Reakcji**

200 OK i nagłówek.
W okresie wczesnego dostępu do funkcji klaster ADX jest inicjowany ręcznie. Mimo że trwa inicjowanie obsługi klastra ADX przez cały czas, można sprawdzić stan aprowizacji na dwa sposoby:
1. Skopiuj wartość adresu URL platformy Azure-AsyncOperation z odpowiedzi i postępuj zgodnie ze [sprawdzaniem stanu operacji asynchronicznych](#asynchronous-operations-and-status-check).
2. Wyślij żądanie GET do zasobu *klastra* i sprawdź wartość *provisioningState* . Jest on *ProvisioningAccount* podczas inicjowania obsługi administracyjnej i zakończył *się pomyślnie* .

### <a name="associate-a-component-to-a-cluster-resource-using-components---create-or-update-api"></a>Kojarzenie składnika z zasobem *klastra* przy użyciu [składników — Tworzenie lub aktualizowanie](https://docs.microsoft.com/rest/api/application-insights/components/createorupdate) interfejsu API

Aby można było wykonać tę operację, musisz mieć uprawnienia "Write" zarówno dla składnika, jak i zasobu *klastra* , co obejmuje następujące akcje:

- W składniku: Microsoft. Insights/Component/Write
- W obszarze zasób *klastra* : Microsoft. OperationalInsights/klastrów/Write

> [!IMPORTANT]
> Ten krok należy wykonać tylko po zainicjowaniu obsługi klastra ADX. W przypadku kojarzenia składników i pozyskiwania danych przed zainicjowaniem obsługi pozyskiwane dane zostaną usunięte i nie będzie można ich odzyskać.
> Aby sprawdzić, czy klaster ADX jest zainicjowany, wykonaj zasób *klastra* Pobierz interfejs API REST i sprawdź, czy wartość *ProvisioningState* została *zakończona pomyślnie*.

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
    "KeyVaultProperties": {
      KeyVaultUri: "https://key-vault-name.vault.azure.net",
      KeyName: "key-name",
      KeyVersion: "current-version"
      },
    "provisioningState": "Succeeded",
    "clusterType": "ApplicationInsights", 
    "clusterId": "cluster-id"
    },
  "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
  "name": "cluster-name",
  "type": "Microsoft.OperationalInsights/clusters",
  "location": "region-name"
  }
```

> [!IMPORTANT]
> Skopiuj i Zachowaj odpowiedź, ponieważ będzie ona potrzebna w następnych krokach.

**Skojarz składnik**

```rst
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.Insights/components/<component-name>?api-version=2015-05-01
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "clusterDefinitionId": "cluster-id"
  },
  "location": "<region-name>",
  "kind": "<component-type>"
}
```
"clusterDefinitionId" jest wartością "clusterId" podaną w odpowiedzi z poprzedniego kroku.
przykład "Kind" to "Web".

**Reakcji**

```json
{
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.insights/components/component-name",
  "name": "component-name",
  "type": "Microsoft.Insights/components",
  "location": "region-name",
  "tags": "",
  "kind": "",
  "properties": {
    "clusterDefinitionId": "cluster-id"
    "ApplicationId": "",
    "AppId": "",
    "Application_Type": "",
    "Flow_Type": "",
    "Request_Source": "",
    "InstrumentationKey": "",
    "CreationDate": "",
    "TenantId": "",
    "HockeyAppId": "",
    "HockeyAppToken": "",
    "provisioningState": "",
    "SamplingPercentage":,
    "RetentionInDays":,
    "ConnectionString": "",
    "DisableIpMasking":,
    "ImmediatePurgeDataOn30Days": 
  }
}
```
"clusterDefinitionId" jest IDENTYFIKATORem zasobu *klastra* skojarzonym z tym składnikiem.

Po skojarzeniu dane wysyłane do składników są przechowywane w postaci zaszyfrowanej przy użyciu klucza zarządzanego.
