---
title: Azure Monitor rejestruje dedykowane klastry
description: Klienci, którzy zajmują więcej niż 1 TB danych monitorowania, mogą korzystać z dedykowanych klastrów, a nie udostępnionych.
ms.subservice: logs
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 09/16/2020
ms.openlocfilehash: 714a43ec197ac150488d4443c1eb6fe1be1da232
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91575524"
---
# <a name="azure-monitor-logs-dedicated-clusters"></a>Azure Monitor rejestruje dedykowane klastry

Azure Monitor dzienniki dedykowane są opcją wdrożenia, która jest dostępna do lepszego obsłużenia klientom o dużej pojemności. Klienci korzystający z ponad 4 TB danych dziennie będą korzystać z dedykowanych klastrów. Klienci z dedykowanymi klastrami mogą wybrać obszary robocze, które mają być hostowane w tych klastrach.

Oprócz obsługi dużych ilości, istnieją inne korzyści wynikające z używania dedykowanych klastrów:

- **Limit szybkości** — klient może mieć wyższe [limity szybkości](../service-limits.md#data-ingestion-volume-rate) pozyskiwania tylko w dedykowanym klastrze.
- **Funkcje** — niektóre funkcje przedsiębiorstwa są dostępne tylko w dedykowanych klastrach — w odniesieniu do kluczy zarządzanych przez klienta (CMK) i obsługi skrytki. 
- **Spójność** — klienci mają własne dedykowane zasoby i nie mają wpływu na innych klientów korzystających z tej samej infrastruktury udostępnionej.
- **Opłacalność** — może być tańsze używanie dedykowanego klastra jako przypisanej do nich warstwy rezerwacji zdolności produkcyjnych uwzględniają cały pozyskiwanie klastra i mają zastosowanie do wszystkich obszarów roboczych, nawet jeśli niektóre z nich są małe i nie kwalifikują się do rabatu za rezerwację zdolności produkcyjnych.
- Zapytania **między obszarami roboczymi** działają szybciej, jeśli wszystkie obszary robocze znajdują się w tym samym klastrze.

Dedykowane klastry wymagają od klientów zatwierdzeń przy użyciu pojemności co najmniej 1 TB pozyskiwania danych dziennie. Migracja do dedykowanego klastra jest prosta. Brak utraty danych lub przerw w świadczeniu usług. 

> [!IMPORTANT]
> Dedykowane klastry są zatwierdzane i w pełni obsługiwane w przypadku wdrożeń produkcyjnych. Jednak ze względu na tymczasowe ograniczenia pojemności do korzystania z tej funkcji wymagane jest wstępne zarejestrowanie. Użyj swoich kontaktów do firmy Microsoft, aby podać identyfikatory subskrypcji.

## <a name="management"></a>Zarządzanie 

Dedykowane klastry są zarządzane za pośrednictwem zasobu platformy Azure, który reprezentuje Azure Monitor klastrów dzienników. Wszystkie operacje są wykonywane na tym zasobie przy użyciu programu PowerShell lub interfejsu API REST.

Po utworzeniu klastra można go skonfigurować i połączyć z nim obszary robocze. Gdy obszar roboczy jest połączony z klastrem, nowe dane wysyłane do obszaru roboczego znajdują się w klastrze. Tylko obszary robocze, które znajdują się w tym samym regionie, co klaster, mogą być połączone z klastrem. Obszary robocze mogą odróżniać się od klastra z pewnymi ograniczeniami. Więcej szczegółów na temat tych ograniczeń znajduje się w tym artykule. 

Wszystkie operacje na poziomie klastra wymagają `Microsoft.OperationalInsights/clusters/write` uprawnienia akcja w klastrze. To uprawnienie można udzielić za pośrednictwem właściciela lub współautora, który zawiera `*/write` akcję, lub za pośrednictwem roli współautor log Analytics, która zawiera `Microsoft.OperationalInsights/*` akcję. Aby uzyskać więcej informacji na temat uprawnień Log Analytics, zobacz [Zarządzanie dostępem do danych dziennika i obszarów roboczych w programie Azure monitor](../platform/manage-access.md). 


## <a name="cluster-pricing-model"></a>Model cenowy klastra

Log Analytics dedykowane klastry używają modelu cenowego rezerwacji pojemności, który wynosi co najmniej 1000 GB/dzień. Każde użycie powyżej poziomu rezerwacji będzie rozliczane według stawki płatności zgodnie z rzeczywistym użyciem.  Informacje o cenach rezerwacji zdolności produkcyjnych są dostępne na [stronie cennika Azure monitor]( https://azure.microsoft.com/pricing/details/monitor/).  

Poziom rezerwacji pojemności klastra jest konfigurowany za pomocą programu programistycznego za pomocą Azure Resource Manager przy użyciu `Capacity` parametru w obszarze `Sku` . Wartość `Capacity` jest określona w jednostkach GB i może mieć wartości 1000 GB/dzień lub więcej w przyrostach wynoszących 100 GB/dzień.

Istnieją dwa tryby rozliczania użycia w klastrze. Można je określić przy użyciu `billingType` parametru podczas konfigurowania klastra. 

1. **Klaster**: w tym przypadku (co jest ustawieniem domyślnym) rozliczanie danych pozyskiwanych odbywa się na poziomie klastra. Pobrane ilości danych z każdego obszaru roboczego skojarzonego z klastrem są agregowane w celu obliczenia dziennego rachunku dla klastra. 

2. **Obszary robocze**: koszty rezerwacji pojemności dla klastra są przydzielone proporcjonalnie do obszarów roboczych w klastrze (po rozpoczęciu obsługi alokacji dla każdego węzła z [Azure Security Center](https://docs.microsoft.com/azure/security-center/) dla każdego obszaru roboczego).

Należy pamiętać, że jeśli obszar roboczy korzysta ze starszej warstwy cenowej na węzeł, gdy jest on połączony z klastrem, będzie rozliczany na podstawie danych pozyskanych w ramach rezerwacji pojemności klastra i nie jest już na węzeł. Alokacje danych na węzeł z Azure Security Center będą nadal stosowane.

Więcej szczegółów zawiera rozliczenia dla Log Analytics dedykowanych klastrów są dostępne [tutaj]( https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-dedicated-clusters).


## <a name="creating-a-cluster"></a>Tworzenie klastra

Najpierw utwórz zasoby klastra, aby rozpocząć tworzenie dedykowanego klastra.

Należy określić następujące właściwości:

- **ClusterName**: służy do celów administracyjnych. Użytkownicy nie są narażeni na tę nazwę.
- **ResourceGroupName**: podobnie jak w przypadku dowolnego zasobu platformy Azure, klastry należą do grupy zasobów. Zalecamy korzystanie z centralnej grupy zasobów IT, ponieważ klastry są zwykle udostępniane przez wiele zespołów w organizacji. Aby uzyskać więcej informacji dotyczących projektowania, zapoznaj się z tematem [projektowanie wdrożenia dzienników Azure monitor](../platform/design-logs-deployment.md)
- **Lokalizacja**: klaster znajduje się w określonym regionie platformy Azure. Tylko obszary robocze znajdujące się w tym regionie mogą być połączone z tym klastrem.
- **SkuCapacity**: należy określić poziom *rezerwacji zdolności produkcyjnych* (SKU) podczas tworzenia zasobu *klastra* . Poziom *rezerwacji pojemności* może należeć do zakresu od 1 000 gb do 3 000 GB dziennie. W razie konieczności można ją zaktualizować w krokach 100 w dalszej części. Jeśli potrzebujesz poziomu rezerwacji pojemności większej niż 3 000 GB dziennie, skontaktuj się z nami pod adresem LAIngestionRate@microsoft.com . Aby uzyskać więcej informacji na temat kosztów klastrów, zobacz [Zarządzanie kosztami klastrów log Analytics](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)

Po utworzeniu zasobu *klastra* można edytować dodatkowe właściwości, takie jak *SKU*, * keyVaultProperties lub *rozliczeń*. Zobacz więcej szczegółów poniżej.

> [!WARNING]
> Tworzenie klastra wyzwala alokację zasobów i Inicjowanie obsługi administracyjnej. Wykonanie tej operacji może potrwać do godziny. Zalecane jest, aby uruchomić go asynchronicznie.

Konto użytkownika, które tworzy klastry, musi mieć standardowe uprawnienie do tworzenia zasobów platformy Azure: `Microsoft.Resources/deployments/*` i uprawnienia do zapisu w klastrze `(Microsoft.OperationalInsights/clusters/write)` .

### <a name="create"></a>Utwórz 

**Program PowerShell**

```powershell
New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -Location {region-name} -SkuCapacity {daily-ingestion-gigabyte} -AsJob

# Check when the job is done
Get-Job -Command "New-AzOperationalInsightsCluster*" | Format-List -Property *
```

**REST**

*Call* 
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

*Odpowiedź*

Powinna być 200 OK i nagłówek.

### <a name="check-provisioning-status"></a>Sprawdzanie stanu aprowizacji

Inicjowanie obsługi klastra Log Analytics trwa dłużej. Stan aprowizacji można sprawdzić na kilka sposobów:

- Uruchom Get-AzOperationalInsightsCluster polecenie programu PowerShell z nazwą grupy zasobów i sprawdź Właściwość ProvisioningState. Wartość jest *ProvisioningAccount* podczas aprowizacji i zakończyła *się pomyślnie* .
  ```powershell
  New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} 
  ```

- Skopiuj wartość Azure-AsyncOperation adresu URL z odpowiedzi i postępuj zgodnie z testem stanu operacji asynchronicznych.

- Wyślij żądanie GET do zasobu *klastra* i sprawdź wartość *provisioningState* . Wartość jest *ProvisioningAccount* podczas aprowizacji i zakończyła *się pomyślnie* .

   ```rst
   GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
   Authorization: Bearer <token>
   ```

   **Odpowiedź**

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

Identyfikator GUID *principalId* jest generowany przez zarządzaną usługę tożsamości dla zasobu *klastra* .

## <a name="change-cluster-properties"></a>Zmień właściwości klastra

Po utworzeniu zasobu *klastra* , który jest w pełni zainicjowany, można edytować dodatkowe właściwości na poziomie klastra przy użyciu programu PowerShell lub interfejsu API REST. Oprócz właściwości, które są dostępne podczas tworzenia klastra, dodatkowe właściwości można ustawić tylko po zainicjowaniu obsługi klastra:

- **keyVaultProperties**: służy do konfigurowania Azure Key Vault używany do aprowizacji [Azure monitor kluczem zarządzanym przez klienta](../platform/customer-managed-keys.md#cmk-provisioning-procedure). Zawiera następujące parametry:  *KeyVaultUri*, *KeyName*, *wersja*klucza. 
- **rozliczenia** — Właściwość *rozliczenia* określa przypisanie rozliczeń dla zasobu *klastra* i jego danych:
  - **Klaster** (domyślnie) — koszty rezerwacji pojemności dla klastra są przypisywane do zasobu *klastra* .
  - **Obszary robocze** — koszty rezerwacji pojemności dla klastra są przypisywane proporcjonalnie do obszarów roboczych w klastrze, a zasób *klastra* jest rozliczany jako część użycia, jeśli łączna ilość danych pobieranych przez dzień jest objęta rezerwacją pojemności. Zobacz [log Analytics dedykowanych klastrów](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters) , aby dowiedzieć się więcej na temat modelu cen klastra. 

> [!NOTE]
> Właściwość *rozliczeniatype* nie jest obsługiwana w programie PowerShell.
> Aktualizacje właściwości klastra mogą być wykonywane asynchronicznie i może upłynąć trochę czasu.

**Program PowerShell**

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -KeyVaultUri {key-uri} -KeyName {key-name} -KeyVersion {key-version}
```

**REST**

> [!NOTE]
> Możesz zaktualizować *jednostkę SKU*zasobu *klastra* , *keyVaultProperties* lub *rozliczeń* przy użyciu poprawki.

Na przykład: 

*Call*

```rst
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
Content-type: application/json

{
   "sku": {
     "name": "capacityReservation",
     "capacity": <capacity-reservation-amount-in-GB>
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

*Odpowiedź*

200 OK i nagłówek

### <a name="check-cluster-update-status"></a>Sprawdź stan aktualizacji klastra

Propagacja identyfikatora klucza może potrwać kilka minut. Stan aktualizacji można sprawdzić na dwa sposoby:

- Skopiuj wartość Azure-AsyncOperation adresu URL z odpowiedzi i postępuj zgodnie z testem stanu operacji asynchronicznych. 

   LUB

- Wyślij żądanie GET do zasobu *klastra* i sprawdź właściwości *KeyVaultProperties* . Ostatnio zaktualizowane szczegóły identyfikatora klucza powinny zwrócić odpowiedź.

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

## <a name="link-a-workspace-to-the-cluster"></a>Łączenie obszaru roboczego z klastrem

Gdy obszar roboczy jest połączony z dedykowanym klastrem, nowe dane, które są wprowadzane do obszaru roboczego, są przekierowywane do nowego klastra, podczas gdy istniejące dane pozostają w istniejącym klastrze. Jeśli dedykowany klaster jest szyfrowany przy użyciu kluczy zarządzanych przez klienta (CMK), tylko nowe dane są szyfrowane za pomocą klucza. System jest abstrakcją tej różnicy od użytkowników, a użytkownicy po prostu wysyłają zapytania do obszaru roboczego w zwykły sposób, gdy system wykonuje zapytania między klastrami w zapleczu.

Klaster może być połączony z do 100 obszarów roboczych. Połączone obszary robocze znajdują się w tym samym regionie co klaster. Aby chronić zaplecze systemu i uniknąć fragmentacji danych, obszar roboczy nie może być połączony z klastrem więcej niż dwa razy w miesiącu.

Aby wykonać operację łączenia, musisz mieć uprawnienia do zapisu zarówno dla obszaru roboczego, jak i zasobu *klastra* :

- W obszarze roboczym: *Microsoft. OperationalInsights/Workspaces/Write*
- W zasobie *klastra* : *Microsoft. OperationalInsights/klastrów/Write*

Oprócz aspektów rozliczeń połączony obszar roboczy zachowuje własne ustawienia, takie jak długość przechowywania danych.
Obszar roboczy i klaster mogą znajdować się w różnych subskrypcjach. Istnieje możliwość, że obszar roboczy i klaster będą znajdować się w różnych dzierżawach, jeśli usługa Azure Lighthouse jest używana do mapowania obu tych elementów do pojedynczej dzierżawy.

W ramach dowolnej operacji klastra łączenie obszaru roboczego można wykonać dopiero po zakończeniu aprowizacji klastra Log Analytics.

> [!WARNING]
> Łączenie obszaru roboczego z klastrem wymaga synchronizacji wielu składników zaplecza i zapewnienia, że pamięć podręczna jest renderowana. Wykonanie tej operacji może potrwać do dwóch godzin. Zalecamy uruchomienie go asynchronicznie.


**Program PowerShell**

Użyj następującego polecenia programu PowerShell, aby połączyć się z klastrem:

```powershell
# Find cluster resource ID
$clusterResourceId = (Get-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name}).id

# Link the workspace to the cluster
Set-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster -WriteAccessResourceId $clusterResourceId -AsJob

# Check when the job is done
Get-Job -Command "Set-AzOperationalInsightsLinkedService" | Format-List -Property *
```


**REST**

Użyj następującego wywołania REST, aby połączyć się z klastrem:

*Wysyłanie*

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

*Odpowiedź*

200 OK i nagłówek.

### <a name="using-customer-managed-keys-with-linking"></a>Korzystanie z kluczy zarządzanych przez klienta z łączeniem

W przypadku korzystania z kluczy zarządzanych przez klienta dane pozyskiwane są przechowywane w postaci zaszyfrowanej przy użyciu klucza zarządzanego po operacji skojarzenia, co może potrwać do 90 minut. 

Stan skojarzenia obszaru roboczego można sprawdzić na dwa sposoby:

- Skopiuj wartość Azure-AsyncOperation adresu URL z odpowiedzi i postępuj zgodnie z testem stanu operacji asynchronicznych.

- Wyślij [obszary robocze — Pobierz](https://docs.microsoft.com/rest/api/loganalytics/workspaces/get) żądanie i obserwuj odpowiedź. Skojarzony obszar roboczy ma clusterResourceId w obszarze "funkcje".

Żądanie wysłania wygląda następująco:

*Wysyłanie*

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalInsights/workspaces/<workspace-name>?api-version=2020-03-01-preview
Authorization: Bearer <token>
```

*Odpowiedź*

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

## <a name="unlink-a-workspace-from-a-dedicated-cluster"></a>Odłączanie obszaru roboczego z dedykowanego klastra

Możesz odłączyć obszar roboczy z klastra. Po odłączeniu obszaru roboczego od klastra nowe dane skojarzone z tym obszarem roboczym nie są wysyłane do dedykowanego klastra. Ponadto rozliczanie obszaru roboczego nie odbywa się już za pośrednictwem klastra. Stare dane niepołączonego obszaru roboczego mogą pozostać w klastrze. Jeśli te dane są szyfrowane przy użyciu kluczy zarządzanych przez klienta (CMK), zostaną zachowane wpisy tajne Key Vault. System jest abstrakcyjny dla tej zmiany od Log Analytics użytkowników. Użytkownicy mogą jedynie wysyłać zapytania dotyczące obszaru roboczego w zwykły sposób. System wykonuje zapytania między klastrami w razie potrzeby bez wskazania użytkownikom.  

> [!WARNING] 
> Istnieje ograniczenie dwóch operacji łączenia na obszar roboczy w ciągu miesiąca. Należy wziąć pod uwagę czas i odpowiednio zaplanować akcje odłączenia. 

## <a name="delete-a-dedicated-cluster"></a>Usuwanie dedykowanego klastra

Można usunąć dedykowany zasób klastra. Przed usunięciem należy odłączyć wszystkie obszary robocze z klastra. Aby wykonać tę operację, musisz mieć uprawnienia "Write" dotyczące zasobu *klastra* . 

Po usunięciu zasobu klastra fizycznego klaster przechodzi do procesu przeczyszczania i usuwania. Usunięcie klastra powoduje usunięcie wszystkich danych, które były przechowywane w klastrze. Dane mogą pochodzić z obszarów roboczych, które były połączone z klastrem w przeszłości.

Zasób *klastra* , który został usunięty w ciągu ostatnich 14 dni, jest w stanie usuwania nietrwałego i można go odzyskać z danymi. Ponieważ wszystkie obszary robocze zostały usunięte z zasobu *klastra* z usunięciem zasobów *klastra* , po odzyskaniu należy ponownie skojarzyć obszary robocze. Nie można wykonać operacji odzyskiwania przez użytkownika, skontaktuj się z kanałem firmy Microsoft lub pomocą techniczną, aby uzyskać żądania odzyskiwania.

W ciągu 14 dni od usunięcia nazwa zasobu klastra jest zarezerwowana i nie może być używana przez inne zasoby.

**Program PowerShell**

Aby usunąć klaster, użyj następującego polecenia programu PowerShell:

  ```powershell
  Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
  ```

**REST**

Aby usunąć klaster, użyj następującego wywołania REST:

  ```rst
  DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

  **Odpowiedź**

  200 OK



## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [log Analytics rozliczania dedykowanego klastra](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)
- Dowiedz się więcej o [odpowiednim projekcie log Analytics obszarów roboczych](../platform/design-logs-deployment.md)
