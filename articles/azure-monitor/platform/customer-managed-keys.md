---
title: Konfiguracja klucza zarządzanego przez klienta usługi Azure Monitor
description: Informacje i kroki konfigurowania klucza zarządzanego przez klienta (CMK) do szyfrowania danych w obszarach roboczych usługi Log Analytics przy użyciu klucza usługi Azure Key Vault.
ms.subservice: logs
ms.topic: conceptual
author: yossi-y
ms.author: yossiy
ms.date: 04/12/2020
ms.openlocfilehash: 25fdb0aefacbdd9c2630a69981a67821ac155786
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758807"
---
# <a name="azure-monitor-customer-managed-key-configuration"></a>Konfiguracja klucza zarządzanego przez klienta usługi Azure Monitor 

Ten artykuł zawiera podstawowe informacje i kroki konfigurowania kluczy zarządzanych przez klienta (CMK) dla obszarów roboczych usługi Log Analytics i składników usługi Application Insights. Po skonfigurowaniu wszystkie dane wysyłane do obszarów roboczych lub składników są szyfrowane za pomocą klucza usługi Azure Key Vault.

Zalecamy [zapoznanie się z ograniczeniami i ograniczeniami](#limitations-and-constraints) poniżej przed konfiguracją.

## <a name="disclaimers"></a>Zastrzeżenia

- Usługa Azure Monitor CMK jest funkcją wczesnego dostępu i jest włączona dla zarejestrowanych subskrypcji.

- Wdrożenie cmk opisane w tym artykule jest dostarczane w jakości produkcji i obsługiwane jako takie, chociaż jest to funkcja wczesnego dostępu.

- Funkcja CMK jest dostarczana w dedykowanym klastrze magazynu danych, który jest klastrem usługi Azure Data Explorer (ADX) i jest odpowiedni dla klientów wysyłających 1 TB dziennie lub więcej. 

- Model cenowy CMK nie jest obecnie dostępny i nie jest uwzględniony w tym artykule. Model cenowy dedykowanego klastra ADX jest oczekiwany w drugim kwartale roku kalendarzowego (CY) 2020 i będzie miał zastosowanie do wszystkich istniejących wdrożeń CMK.

- W tym artykule opisano konfigurację cmk dla obszarów roboczych usługi Log Analytics. Składniki CMK for Application Insights są również obsługiwane przy użyciu tego artykułu, podczas gdy różnice są wymienione w dodatku.

> [!NOTE]
> Usługa Log Analytics i usługa Application Insights korzystają z tej samej platformy magazynu danych i aparatu zapytań.
> Łączymy te dwa sklepy za pośrednictwem integracji usługi Application Insights z usługą Log Analytics w celu utworzenia pojedynczego magazynu dzienników ujednoliconych w usłudze Azure Monitor. Zmiana ta planowana jest na drugi kwartał roku kalendarzowego 2020. Jeśli nie trzeba wdrożyć CMK dla danych usługi Application Insights do tego czasu, zaleca się oczekiwanie na zakończenie konsolidacji, ponieważ takie wdrożenia zostaną zakłócone przez konsolidację i trzeba będzie ponownie skonfigurować CMK po migracji do obszaru roboczego usługi Log Analytics. Minimum 1 TB dziennie ma zastosowanie na poziomie klastra i do czasu zakończenia konsolidacji w drugim kwartale usługa Application Insights i usługa Log Analytics wymagają oddzielnych klastrów.

## <a name="customer-managed-key-cmk-overview"></a>Omówienie klucza zarządzanego przez klienta (CMK)

[Szyfrowanie w spoczynku](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest) jest powszechnym wymogiem prywatności i bezpieczeństwa w organizacjach. Możesz pozwolić platformie Azure całkowicie zarządzać szyfrowaniem w spoczynku, podczas gdy masz różne opcje ścisłego zarządzania kluczami szyfrowania lub szyfrowania.

Magazyn danych usługi Azure Monitor zapewnia, że wszystkie dane zaszyfrowane w spoczynku przy użyciu kluczy zarządzanych przez platformę Azure podczas przechowywania w usłudze Azure Storage. Usługa Azure Monitor udostępnia również opcję szyfrowania danych przy użyciu własnego klucza, który jest przechowywany w magazynie [azure key vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview), który jest dostępny przy użyciu uwierzytelniania tożsamości [zarządzanej](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) przypisanego przez system. Ten klucz może być [chroniony przez oprogramowanie lub sprzętowy moduł HSM.](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
Użycie szyfrowania w usłudze Azure Monitor jest identyczne ze sposobem obsługi [szyfrowania usługi Azure Storage.](https://docs.microsoft.com/azure/storage/common/storage-service-encryption#about-azure-storage-encryption)

Częstotliwość, że usługa Azure Monitor Storage uzyskuje dostęp do magazynu kluczy dla operacji zawijania i odwijania wynosi od 6 do 60 sekund.Usługa Azure Monitor Storage zawsze uwzględnia zmiany w uprawnieniach kluczy w ciągu godziny.

Połkniętych danych w ciągu ostatnich 14 dni jest również przechowywany w gorącej pamięci podręcznej (SSD-backed) dla wydajnej pracy aparatu zapytań. Te dane pozostają zaszyfrowane za pomocą kluczy firmy Microsoft niezależnie od konfiguracji CMK, ale pracujemy nad zaszyfrowaniem dysków SSD za pomocą cmk w pierwszej połowie 2020 roku.

## <a name="how-cmk-works-in-azure-monitor"></a>Jak działa cmk w usłudze Azure Monitor

Usługa Azure Monitor wykorzystuje przypisaną do systemu tożsamość zarządzaną, aby udzielić dostępu do usługi Azure Key Vault.Tożsamości zarządzanej przypisanej do systemu można skojarzyć tylko z jednym zasobem platformy Azure. Tożsamość magazynu danych usługi Azure Monitor (klaster ADX) jest obsługiwana na poziomie klastra i nakazuje to, że funkcja CMK jest dostarczana w dedykowanym klastrze ADX. Aby obsługiwać cmk w wielu obszarach roboczych, nowy zasób usługi Log Analytics *(Klaster)* wykonuje jako pośrednie połączenie tożsamości między magazynem kluczy a obszarami roboczymi usługi Log Analytics. Ta koncepcja jest zgodna z ograniczeniem tożsamości przypisanym przez system, a tożsamość jest zachowywana między klastrem ADX a zasobem *klastra analizy* dzienników, podczas gdy dane wszystkich skojarzonych obszarów roboczych są chronione za pomocą klucza Usługi Key Vault. Podkładanie magazynu klastra ADX używa\'tożsamości zarządzanej skojarzonej z zasobem *klastra* do uwierzytelniania usługi Azure Key Vault za pośrednictwem usługi Azure Active Directory i uzyskiwania do niego dostępu.

![Przegląd CMK](media/customer-managed-keys/cmk-overview-8bit.png)
1.    Magazyn kluczy klienta.
2.    Zasób *klastra* analizy dzienników klienta, który zarządzał tożsamością z uprawnieniami do usługi Key Vault — tożsamość jest obsługiwana na poziomie magazynu danych (klaster ADX).
3.    Dedykowany klaster ADX usługi Azure Monitor.
4.    Obszary robocze klienta skojarzone z zasobem *klastra* do szyfrowania CMK.

## <a name="encryption-keys-management"></a>Zarządzanie kluczami szyfrowania

Istnieją 3 typy kluczy zaangażowanych w szyfrowanie danych magazynu:

- **KEK** - Klucz szyfrowania klucza (CMK)
- **AEK** - Klucz szyfrowania konta
- **DEK** — klucz szyfrowania danych

Mają zastosowanie następujące zasady:

- Konta magazynu ADX generują unikatowy klucz szyfrowania dla każdego konta magazynu, który jest znany jako AEK.

- AEK służy do wyprowadzania deks, które są klucze, które są używane do szyfrowania każdego bloku danych zapisanych na dysku.

- Po skonfigurowaniu klucza w magazynie kluczy i odwoływaniu się do niego w zasobie *klastra* usługa Azure Storage wysyła żądania do usługi Azure Key Vault w celu zawijania i odwijania AEK w celu wykonania operacji szyfrowania i odszyfrowywania danych.

- KEK nigdy nie opuszcza magazynu kluczy, a w przypadku klucza HSM nigdy nie opuszcza sprzętu.

- Usługa Azure Storage używa tożsamości zarządzanej skojarzonej z zasobem *klastra* do uwierzytelniania i dostępu do usługi Azure Key Vault za pośrednictwem usługi Azure Active Directory.

## <a name="cmk-provisioning-procedure"></a>Procedura tworzenia inicjowania obsługi administracyjnej cmk

W przypadku konfiguracji CMK usługi Application Insights postępuj zgodnie z zawartością dodatku dla kroków 3 i 6.

1. Biała lista subskrypcji — jest to wymagane dla tej funkcji wczesnego dostępu
2. Tworzenie usługi Azure Key Vault i przechowywanie klucza
3. Tworzenie zasobu *klastra*
4. Inicjowanie obsługi administracyjnej magazynu danych usługi Azure Monitor (klaster ADX)
5. Udzielanie uprawnień do magazynu kluczy
6. Kojarzenie obszarów roboczych usługi Log Analytics

Procedura nie jest obsługiwana w interfejsie użytkownika obecnie i proces inicjowania obsługi administracyjnej jest wykonywany za pośrednictwem interfejsu API REST.

> [!IMPORTANT]
> Każde żądanie interfejsu API musi zawierać token autoryzacji na okaziciela w nagłówku żądania.

Przykład:

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer eyJ0eXAiO....
```

Gdzie *eyJ0eXAiO....* reprezentuje pełny token autoryzacji. 

Token można uzyskać przy użyciu jednej z następujących metod:

1. Użyj metody [rejestracji aplikacji.](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)
2. W witrynie Azure Portal
    1. Przejdź do witryny Azure portal w "narzędziu deweloperskim" (F12)
    1. Poszukaj ciągu autoryzacji w obszarze "Nagłówki żądań" w jednym z wystąpień "batch?api-version". Wygląda na to: "autoryzacja: Okaziciela eyJ0eXAiO....". 
    1. Skopiuj i dodaj go do wywołania interfejsu API według poniższych przykładów.
3. Przejdź do witryny dokumentacji usługi Azure REST. Naciśnij przycisk "Wypróbuj" na dowolnym interfejsie API i skopiuj token na okaziciela.

### <a name="asynchronous-operations-and-status-check"></a>Operacje asynchroniczne i sprawdzanie stanu

Niektóre operacje w tej procedurze konfiguracji są uruchamiane asynchronicznie, ponieważ nie można ich szybko wykonać. Odpowiedź dla operacji asynchroniiowej początkowo zwraca kod stanu HTTP 200 (OK) i nagłówek z *azure-AsyncOperation* właściwości po zaakceptowaniu:
```json
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-03-01-preview"
```

Można sprawdzić stan operacji asynchroniiowej, wysyłając żądanie GET do wartości nagłówka *Azure-AsyncOperation:*
```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

Odpowiedź zawiera informacje o operacji i jej *statusie*. Może to być jedna z następujących czynności:

Operacja jest w toku
```json
{
    "id": "Azure-AsyncOperation URL value from the GET operation",
    "name": "operation-id", 
    "status" : "InProgress", 
    "startTime": "2017-01-06T20:56:36.002812+00:00",
}
```

Operacja została zakończona
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

### <a name="subscription-whitelisting"></a>Biała lista subskrypcji

Funkcja CMK jest funkcją wczesnego dostępu. Subskrypcje, w których planujesz utworzyć zasoby *klastra,* muszą być wcześniej wpisane na białą listę przez grupę produktów platformy Azure. Użyj kontaktów w firmie Microsoft, aby podać identyfikatory subskrypcji.

> [!IMPORTANT]
> Możliwości CMK mają regionalny rozwój. Obszary robocze usługi Azure Key Vault, *cluster* resource i associated Log Analytics muszą znajdować się w tym samym regionie, ale mogą znajdować się w różnych subskrypcjach.

### <a name="storing-encryption-key-kek"></a>Przechowywanie klucza szyfrowania (KEK)

Utwórz lub użyj usługi Azure Key Vault, który już musisz wygenerować, lub zaimportuj klucz do szyfrowania danych. Usługa Azure Key Vault musi być skonfigurowana jako generowana w celu ochrony klucza i dostępu do danych w usłudze Azure Monitor. Tę konfigurację można zweryfikować w obszarze właściwości w magazynie kluczy, należy włączyć *zarówno ochronę* *usuwania nietrwałego,* jak i oczyszczania.

![Ustawienia ochrony przed usuwaniem i czyszczeniem trwoniącej](media/customer-managed-keys/soft-purge-protection.png)

Te ustawienia są dostępne za pośrednictwem interfejsu WIERSZA i programu PowerShell:
- [Usuwanie nietrwałe](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete)
- [Przeczyścić osłony ochronne](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) przed usunięciem siły z sekret / skarbiec nawet po miękkim usunięciu

### <a name="create-cluster-resource"></a>Tworzenie *zasobu klastra*

Ten zasób jest używany jako pośrednie połączenie tożsamości między magazynem kluczy a obszarami roboczymi usługi Log Analytics. Po otrzymaniu potwierdzenia, że subskrypcje zostały umieszczone na białej liście, utwórz zasób *klastra analizy* dzienników w regionie, w którym znajdują się obszary robocze. Usługa Application Insights i usługa Log Analytics wymagają oddzielnych typów zasobów *klastra.* Typ zasobu *klastra* jest definiowany w czasie tworzenia przez ustawienie *właściwości clusterType* na *LogAnalytics*lub *ApplicationInsights*. Nie można zmienić typu zasobu klastra.

W przypadku konfiguracji CMK usługi Application Insights należy postępować zgodnie z zawartością dodatku.

Podczas tworzenia zasobu *klastra* należy określić poziom rezerwacji pojemności (sku). Poziom rezerwacji pojemności może być w zakresie od 1000 do 2000 GB dziennie i można go zaktualizować w krokach 100 później. Jeśli potrzebujesz poziomu rezerwacji pojemności wyższego niż 2000 GB dziennie, skontaktuj się z kontaktem firmy Microsoft, aby go włączyć. Ta właściwość nie ma obecnie wpływu na rozliczenia — po wprowadzeniu modelu cenowego dla dedykowanego klastra rozliczenia będą miały zastosowanie do wszystkich istniejących wdrożeń CMK.

**Utwórz**

To żądanie Menedżera zasobów jest operacją asynchronizacyjną.

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
Tożsamość jest przypisywana do zasobu *klastra* w czasie tworzenia.

**Odpowiedzi**

200 OK i nagłówek.
W okresie wczesnego dostępu funkcji klaster ADX jest aprowizowany ręcznie. Chociaż trwa inicjowanie obsługi administracyjnej klastra underly ADX chwilę, aby zakończyć, można sprawdzić stan inicjowania obsługi administracyjnej na dwa sposoby:
1. Skopiuj wartość adresu URL Azure-AsyncOperation z odpowiedzi i postępuj zgodnie z [sprawdzaniem stanu operacji asynchronicznych](#asynchronous-operations-and-status-check).
2. Wyślij żądanie GET w zasobie *klastra* i przyjrzyj się *wartością provisioningState.* Jest *inicjowanie obsługi administracyjnejKontowanie* podczas inicjowania obsługi administracyjnej i *po pomyślnym* zakończeniu.

### <a name="azure-monitor-data-store-adx-cluster-provisioning"></a>Inicjowanie obsługi administracyjnej magazynu danych usługi Azure Monitor (klaster ADX)

W okresie wczesnego dostępu funkcji klaster ADX jest aprowizowany ręcznie przez zespół produktu po wykonaniu poprzednich kroków. Użyj kanału firmy Microsoft w tym kroku i podaj odpowiedź zasobów *klastra.* 

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2019-08-01-preview
Authorization: Bearer <token>
```

> [!IMPORTANT]
> Skopiuj i zapisz odpowiedź, ponieważ będziesz potrzebować szczegółów w następnych krokach.

**Odpowiedzi**

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

Identyfikator GUID "principalId" jest generowany przez usługę tożsamości zarządzanej dla zasobu *klastra.*

### <a name="grant-key-vault-permissions"></a>Udziel uprawnień Magazynu kluczy

Zaktualizuj magazyn kluczy o nową zasadę dostępu, która przyznaje uprawnienia do zasobu *klastra.* Te uprawnienia są używane przez podkładanie usługi Azure Monitor Storage do szyfrowania danych. Otwórz magazyn kluczy w witrynie Azure portal i kliknij przycisk "Zasady dostępu", a następnie "+ Dodaj zasady dostępu", aby utworzyć zasady z tymi ustawieniami:

- Uprawnienia klucza: wybierz uprawnienia "Get", "Wrap Key" i "Unwrap Key".
- Wybierz jednostkę: wprowadź wartość identyfikatora głównego, który zwrócił w odpowiedzi w poprzednim kroku.

![udzielanie uprawnień Usługi Magazynu kluczy](media/customer-managed-keys/grant-key-vault-permissions-8bit.png)

Uprawnienie *Pobierz* jest wymagane, aby sprawdzić, czy magazyn kluczy jest skonfigurowany jako możliwe do odzyskania w celu ochrony klucza i dostępu do danych usługi Azure Monitor.

### <a name="update-cluster-resource-with-key-identifier-details"></a>Aktualizowanie zasobu klastra za pomocą szczegółów identyfikatora klucza

Ten krok jest wykonywany podczas początkowych i przyszłych aktualizacji wersji klucza w magazynie kluczy. Informuje usługę Azure Monitor Storage o wersji klucza, która ma być używana do szyfrowania danych. Po zaktualizowaniu nowy klucz jest używany do zawijania i odwijania do klucza magazynu (AEK).

Aby zaktualizować zasób *klastra* o szczegóły *identyfikatora klucza przechowalni* kluczy, wybierz bieżącą wersję klucza w usłudze Azure Key Vault, aby uzyskać szczegółowe informacje o identyfikatorze klucza.

![Udziel uprawnień Magazynu kluczy](media/customer-managed-keys/key-identifier-8bit.png)

Zaktualizuj właściwości keyvaultproperties *zasobu klastra* o szczegóły identyfikatora klucza.

**Aktualizacja**

To żądanie Menedżera zasobów jest operacją asynchronizacyjną podczas aktualizowania szczegółów identyfikatora klucza, podczas gdy jest synchroniczne podczas aktualizowania wartości capacity.

> [!Warning]
> W aktualizacji zasobów *klastra* należy podać pełną treść, która zawiera *tożsamość,* *sku,* *KeyVaultProperties* i *lokalizację.* Brak szczegółów *KeyVaultProperties* spowoduje usunięcie identyfikatora klucza z zasobu *klastra* i [spowodowanie odwołania klucza](#cmk-kek-revocation).

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
"KeyVaultProperties" zawiera szczegóły identyfikatora klucza magazynu kluczy.

**Odpowiedzi**

200 OK i nagłówek.
Zajmuje propagacji key identyfikatora kilka minut, aby zakończyć. Stan aktualizacji można sprawdzić na dwa sposoby:
1. Skopiuj wartość adresu URL Azure-AsyncOperation z odpowiedzi i postępuj zgodnie z [sprawdzaniem stanu operacji asynchronicznych](#asynchronous-operations-and-status-check).
2. Wyślij żądanie GET w zasobie *klastra* i spójrz na *właściwości KeyVaultProperties.* Ostatnio zaktualizowane szczegóły identyfikatora klucza powinny zostać wrócone w odpowiedzi.

Odpowiedź na żądanie GET w zasobie *klastra* powinna wyglądać następująco po zakończeniu aktualizacji identyfikatora klucza:

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
W przypadku konfiguracji CMK usługi Application Insights postępuj zgodnie z zawartością dodatku dla tego kroku.

Aby wykonać tę operację, musisz mieć uprawnienia "zapisu" zarówno do obszaru roboczego, jak i *zasobu klastra,* które obejmują następujące akcje:

- W obszarze roboczym: Microsoft.OperationalInsights/workspaces/write
- W *zasobie klastra:* Microsoft.OperationalInsights/clusters/write

> [!IMPORTANT]
> Ten krok należy wykonać tylko po inicjowaniu obsługi administracyjnej klastra ADX. Jeśli skojarzone obszary robocze i pozyskiwania danych przed inicjowania obsługi administracyjnej, pochłonięte dane zostaną usunięte i nie będzie można odzyskać.

**Kojarzenie obszaru roboczego**

To żądanie Menedżera zasobów jest operacją asynchronizacyjną.

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

**Odpowiedzi**

200 OK i nagłówek.
Pojmowane dane są przechowywane szyfrowane za pomocą zarządzanego klucza po operacji skojarzenia, co może potrwać do 90 minut. Stan skojarzenia obszaru roboczego można sprawdzić na dwa sposoby:
1. Skopiuj wartość adresu URL Azure-AsyncOperation z odpowiedzi i postępuj zgodnie z [sprawdzaniem stanu operacji asynchronicznych](#asynchronous-operations-and-status-check).
2. Wyślij [obszary robocze — Pobierz](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) żądanie i obserwować odpowiedź, skojarzony obszar roboczy będzie miał clusterResourceId w obszarze "funkcje".

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
```

**Odpowiedzi**

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

## <a name="cmk-kek-revocation"></a>Odwołanie CMK (KEK)

Dostęp do danych można odwołać, wyłączając klucz lub usuwając zasady dostępu do zasobów *klastra* w magazynie kluczy. Usługa Azure Monitor Storage zawsze będzie respektować zmiany w uprawnieniach kluczy w ciągu godziny, zwykle wcześniej, a magazyn magazynowy stanie się niedostępny. Wszystkie dane przynajmowane do obszarów roboczych skojarzonych z zasobem *klastra* są odrzucane, a kwerendy nie powiodą się. Wcześniej pojęte dane pozostają niedostępne w usłudze Azure Monitor Storage, o ile jesteś zasobem *klastra,* a obszary robocze nie są usuwane. Niedostępne dane podlegają zasadom przechowywania danych i zostaną usunięte po osiągnięciu zasad przechowywania.

Magazyn będzie okresowo sondować magazyn kluczy, aby spróbować odwinąć klucz szyfrowania i po uzyskaniu dostępu, wznawianie pozyskiwania danych i zapytań w ciągu 30 minut.

## <a name="cmk-kek-rotation"></a>Obrót CMK (KEK)

Obrót cmk wymaga jawnej aktualizacji zasobu *klastra* z nową wersją klucza w usłudze Azure Key Vault. Aby zaktualizować usługę Azure Monitor za pomocą nowej wersji klucza, postępuj zgodnie z instrukcjami w kroku "Aktualizuj zasób *klastra* ze szczegółami identyfikatora klucza". Jeśli zaktualizujesz wersję klucza w magazynie kluczy i nie zaktualizujesz nowych szczegółów identyfikatora klucza w zasobie *klastra,* usługa Azure Monitor Storage będzie nadal używać poprzedniego klucza.
Wszystkie dane są dostępne po operacji rotacji kluczy, w tym dane ponawione przed rotacją i po nim, ponieważ wszystkie dane pozostają zaszyfrowane przez klucz szyfrowania konta (AEK), podczas gdy AEK jest teraz szyfrowany przez nową wersję klucza szyfrowania klucza (KEK).

## <a name="limitations-and-constraints"></a>Ograniczenia i ograniczenia

- Funkcja CMK jest obsługiwana na poziomie klastra ADX i wymaga dedykowanego klastra ADX usługi Azure Monitor z wymaganiem wysyłania 1 TB dziennie lub więcej.

- Maksymalna liczba zasobów *klastra* na subskrypcję jest ograniczona do 2

- *Skojarzenie* zasobów klastra z obszarem roboczym powinno być przenoszone TYLKO po sprawdzeniu, że aprowizacji klastra ADX została ukończona. Dane wysyłane do obszaru roboczego przed zakończeniem inicjowania obsługi administracyjnej zostaną usunięte i nie będą możliwe do odzyskania.

- Szyfrowanie CMK ma zastosowanie do nowo pogoń za danymi po konfiguracji CMK. Dane, które zostały pojętone przed konfiguracją CMK, pozostają zaszyfrowane za pomocą klucza firmy Microsoft. Można bezproblemowo wysyłać zapytania do danych przygonanych przed i po konfiguracji cmk.

- Obszar roboczy można odstąrzyć z zasobu *klastra* przy podejmowaniu decyzji, że cmk nie jest wymagany dla określonego obszaru roboczego. Nowe pogoń za operacja de-skojarzenia jest przechowywana w magazynie udostępnionej usługi Log Analytics, jak to było przed skojarzeniem z zasobem *klastra.* Można bezproblemowo wysyłać zapytania do danych przynaudowanych przed i po usunięciu skojarzenia, jeśli zasób *klastra* jest aprowizowany i skonfigurowany przy pomocą prawidłowego klucza usługi Key Vault.

- Usługa Azure Key Vault musi być skonfigurowana jako możliwe do odzyskania. Te właściwości nie są domyślnie włączone i powinny być konfigurowane przy użyciu interfejsu wiersza polecenia lub programu PowerShell:

  - [Usuwanie nietrwałe](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete) musi być włączone
  - [Ochrona przed przeczyszczaniem](https://docs.microsoft.com/azure/key-vault/key-vault-ovw-soft-delete#purge-protection) powinna być włączona, aby chronić przed usunięciem siły tajnego / skarbca nawet po usunięciu

- Usługa Application Insights i log Analytics wymagają oddzielnych zasobów *klastra.* Typ zasobu *klastra* jest definiowany w czasie tworzenia przez ustawienie właściwości "clusterType" na "LogAnalytics" lub "ApplicationInsights". Nie można zmienić typu zasobu *klastra.*

- *Przenoszenie* zasobów klastra do innej grupy zasobów lub subskrypcja nie jest obecnie obsługiwana.

- Usługa Azure Key Vault, zasób *klastra* i skojarzone obszary robocze muszą znajdować się w tym samym regionie i w tej samej dzierżawie usługi Azure Active Directory (Azure AD), ale mogą znajdować się w różnych subskrypcjach.

- Skojarzenie obszaru roboczego z zasobem *klastra* zakończy się niepowodzeniem, jeśli jest skojarzone z innym zasobem *klastra*

## <a name="troubleshooting-and-management"></a>Rozwiązywanie problemów i zarządzanie nimi

- Dostępność magazynu kluczy
    - W normalnej pracy — pamięć masowa buforuje AEK przez krótki czas i wraca do magazynu kluczy, aby okresowo odwożć.
    
    - Przejściowe błędy połączenia — magazyn obsługuje błędy przejściowe (limity czasu, awarie połączeń, problemy z systemem DNS), umożliwiając kluczom pozostanie w pamięci podręcznej przez krótki czas dłużej, co pokonuje wszelkie małe błędy w dostępności. Funkcje zapytania i pozyskiwania są kontynuowane bez przerw.
    
    - Niedostępność witryny na żywo — niedostępność około 30 minut spowoduje, że konto Magazynu stanie się niedostępne. Funkcja kwerendy jest niedostępna, a pochłonięte dane są buforowane przez kilka godzin przy użyciu klucza firmy Microsoft, aby uniknąć utraty danych. Po przywróceniu dostępu do usługi Key Vault kwerenda staje się dostępna, a tymczasowe dane w pamięci podręcznej są pozyskiwania do magazynu danych i szyfrowane za pomocą cmk.

- Jeśli utworzysz zasób *klastra* i natychmiast określisz właściwości KeyVaultProperties, operacja może zakończyć się niepowodzeniem, ponieważ zasady dostępu nie można zdefiniować, dopóki tożsamość systemu nie zostanie przypisana do zasobu *klastra.*

- Jeśli w magazynie kluczy klucza Kluczy zostanie *zaktualizowany istniejący* zasób klastra za pomocą właściwości KeyVaultProperties i "Get" klucz Access Policy, operacja zakończy się niepowodzeniem.

- Jeśli spróbujesz usunąć zasób *klastra* skojarzony z obszarem roboczym, operacja usuwania zakończy się niepowodzeniem.

- Jeśli podczas tworzenia zasobu *klastra* pojawia się błąd powodujący konflikt — może to oznaczać, że zasób *klastra* został usunięty w ciągu ostatnich 14 dni i jest w okresie usuwania nietrwałego. Nazwa zasobu *klastra* pozostaje zarezerwowana w okresie usuwania nietrwałego i nie można utworzyć nowego klastra o tej nazwie. Nazwa jest zwalniana po okresie usuwania nietrwałego, gdy zasób *klastra* jest trwale usuwany.

- Pobierz wszystkie zasoby *klastra* dla grupy zasobów:

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```
    
  **Odpowiedzi**
  
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
    
  **Odpowiedzi**
    
  Ta sama odpowiedź, co w przypadku "zasobów*klastra* dla grupy zasobów", ale w zakresie subskrypcji.

- Aktualizowanie *rezerwacji pojemności* w *zasobie klastra* — gdy zmienia się wolumin danych do skojarzonych obszarów roboczych i chcesz zaktualizować poziom rezerwacji pojemności dla zagadnień rozliczeniowych, postępuj zgodnie z [aktualizacją *zasobu klastra* ](#update-cluster-resource-with-key-identifier-details) i podaj nową wartość pojemności. Poziom rezerwacji pojemności może wynosić od 1000 do 2000 GB dziennie i w krokach 100. Aby uzyskać poziom wyższy niż 2000 GB dziennie, skontaktuj się z kontaktem firmy Microsoft, aby go włączyć.

- Usuń zasób *klastra* — operacja usuwania nietrwałego jest wykonywana w celu umożliwienia odzyskania zasobu *klastra* wraz z jego danymi w ciągu 14 dni, niezależnie od tego, czy usunięcie było przypadkowe, czy zamierzone. Nazwa zasobu *klastra* pozostaje zarezerwowana w okresie usuwania nietrwałego i nie można utworzyć nowego klastra o tej nazwie. Po okresie usuwania nietrwałego nazwa zasobu *klastra* jest zwalniana, zasób i dane *klastra* są trwale usuwane i nie można ich odzyskać. Każdy skojarzony obszar roboczy zostanie usunięty z zasobu *klastra* podczas operacji usuwania. Nowe pogoń za pomocą klucza Microsoft jest przechowywana w udostępnionym magazynie usługi Log Analytics. Operacja dezłączniona obszarów roboczych jest asynchroniza.

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
  Authorization: Bearer <token>
  ```

  **Odpowiedzi**

  200 ok.

- Odzyskiwanie *zasobu klastra* i danych — zasób *klastra,* który został usunięty w ciągu ostatnich 14 dni, jest w stanie nietrwałego usuwania i można go odzyskać. Jest to wykonywane ręcznie przez grupę produktów obecnie. Użyj swojego kanału firmy Microsoft do żądania odzyskiwania.

## <a name="appendix"></a>Dodatek

Usługa Application Insights Customer Managed Key (CMK) jest również obsługiwana, chociaż należy rozważyć następującą zmianę, aby ułatwić planowanie wdrażania cmk dla składników usługi Application Insight.

Usługa Log Analytics i usługa Application Insights korzystają z tej samej platformy magazynu danych i aparatu zapytań. Łączymy te dwa sklepy poprzez integrację usługi Application Insights z usługą Log Analytics, aby zapewnić pojedynczy ujednolicony magazyn dzienników w usłudze Azure Monitor w drugim kwartale
2020. Ta zmiana spowoduje, że dane usługi Application Insight zostaną wprowadzone do obszarów roboczych usługi Log Analytics i umożliwią kwerendy, szczegółowe informacje i inne ulepszenia, podczas gdy konfiguracja cmk w obszarze roboczym będzie również stosowana do danych usługi Application Insights.

> [!NOTE]
> Jeśli nie trzeba wdrożyć CMK dla danych usługi Application Insight przed integracją, zaleca się oczekiwanie z application insights CMK, ponieważ takie wdrożenia zostaną zakłócone przez integrację i trzeba będzie ponownie skonfigurować CMK po migracji do obszaru roboczego usługi Log Analytics. Minimum 1 TB dziennie ma zastosowanie na poziomie klastra i do czasu zakończenia konsolidacji w drugim kwartale usługa Application Insights i usługa Log Analytics wymagają oddzielnych klastrów.

## <a name="application-insights-cmk-configuration"></a>Konfiguracja cmk usługi Application Insights

Konfiguracja usługi CMK usługi Application Insights jest identyczna z procesem zilustrowanym w tym artykule, w tym ograniczeniami i rozwiązywaniem problemów, z wyjątkiem następujących kroków:

- Tworzenie zasobu *klastra*
- Kojarzenie składnika z zasobem *klastra*

Podczas konfigurowania cmk dla usługi Application Insights, należy użyć tych kroków zamiast tych wymienionych powyżej.

### <a name="create-a-cluster-resource"></a>Tworzenie zasobu *klastra*

Ten zasób jest używany jako pośrednie połączenie tożsamości między magazynem kluczy a składnikami. Po otrzymaniu potwierdzenia, że subskrypcje zostały umieszczone na białej liście, utwórz zasób *klastra analizy* dzienników w regionie, w którym znajdują się składniki. Typ zasobu *klastra* jest definiowany w czasie tworzenia przez ustawienie *właściwości clusterType* na *LogAnalytics*lub *ApplicationInsights*. Powinien to być *ApplicationInsights* dla usługi Application Insights CMK. Nie można zmienić ustawienia *clusterType* po zakończeniu konfiguracji.

**Utwórz**

To żądanie Menedżera zasobów jest operacją asynchronizacyjną.

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

**Odpowiedzi**

200 OK i nagłówek.
W okresie wczesnego dostępu funkcji klaster ADX jest aprowizowany ręcznie. Chociaż trwa inicjowanie obsługi administracyjnej klastra underly ADX chwilę, aby zakończyć, można sprawdzić stan inicjowania obsługi administracyjnej na dwa sposoby:
1. Skopiuj wartość adresu URL Azure-AsyncOperation z odpowiedzi i postępuj zgodnie z [sprawdzaniem stanu operacji asynchronicznych](#asynchronous-operations-and-status-check).
2. Wyślij żądanie GET w zasobie *klastra* i przyjrzyj się *wartością provisioningState.* Jest *inicjowanie obsługi administracyjnejKontowanie* podczas inicjowania obsługi administracyjnej i *po pomyślnym* zakończeniu.

### <a name="associate-a-component-to-a-cluster-resource-using-components---create-or-update-api"></a>Kojarzenie składnika z zasobem *klastra* przy użyciu [interfejsu Components — tworzenie lub aktualizowanie](https://docs.microsoft.com/rest/api/application-insights/components/createorupdate) interfejsu API

Aby wykonać tę operację, musisz mieć uprawnienia "zapisu" zarówno do składnika, jak i *zasobu klastra,* które obejmują następujące akcje:

- W składniku: Microsoft.Insights/component/write
- W *zasobie klastra:* Microsoft.OperationalInsights/clusters/write

> [!IMPORTANT]
> Ten krok należy wykonać tylko po inicjowaniu obsługi administracyjnej klastra ADX. Jeśli skojarzysz składniki i pozyskujesz dane przed inicjowania obsługi administracyjnej, pogoń danych zostanie porzucona i nie będzie można odzyskać.
> Aby sprawdzić, czy klaster ADX jest aprowidyfikowany, wykonaj interfejs API Pobierz rest *zasobu klastra* i sprawdź, czy wartość *aprowizowaniaState* *powiodła się*.

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

**Odpowiedzi**
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
> Skopiuj i zachowaj odpowiedź, ponieważ będzie ona potrzebna w następnych krokach.

**Kojarzenie komponentu**

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
"clusterDefinitionId" jest wartością "clusterId" podana w odpowiedzi z poprzedniego kroku.
"miły" przykład to "web".

**Odpowiedzi**

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
"clusterDefinitionId" to identyfikator zasobu *klastra* skojarzony z tym składnikiem.

Po skojarzeniu dane wysyłane do składników są przechowywane szyfrowane za pomocą klucza zarządzanego.
