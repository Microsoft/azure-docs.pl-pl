---
title: Azure Monitor rejestruje dedykowane klastry
description: Klienci, którzy zajmują więcej niż 1 TB danych monitorowania, mogą korzystać z dedykowanych klastrów, a nie udostępnionych.
ms.subservice: logs
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 09/16/2020
ms.openlocfilehash: adcc894db630bba11e84e2f277705d2f31caf7dc
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98920227"
---
# <a name="azure-monitor-logs-dedicated-clusters"></a>Azure Monitor rejestruje dedykowane klastry

Azure Monitor dzienników dedykowanych są opcjami wdrażania, które umożliwiają zaawansowaną obsługę Azure Monitor dzienników klientów. Klienci z dedykowanymi klastrami mogą wybrać obszary robocze, które mają być hostowane w tych klastrach.

Możliwości, które wymagają dedykowanych klastrów, to:

- **[Klucze zarządzane przez klienta](../platform/customer-managed-keys.md)** — Szyfruj dane klastra przy użyciu kluczy dostarczanych i kontrolowanych przez klienta.
- **[Skrytka](../platform/customer-managed-keys.md#customer-lockbox-preview)** — klienci mogą kontrolować żądania dostępu inżynierów pomocy technicznej firmy Microsoft dotyczące danych.
- **[Szyfrowanie podwójne](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)** chroni przed scenariuszem, w którym można złamać jeden z algorytmów szyfrowania lub kluczy. W takim przypadku dodatkowa warstwa szyfrowania nadal chroni dane.
- **[Wiele obszarów roboczych](../log-query/cross-workspace-query.md)** — Jeśli klient korzysta z więcej niż jednego obszaru roboczego dla środowiska produkcyjnego, warto mieć sens, aby użyć dedykowanego klastra. Zapytania między obszarami roboczymi będą działać szybciej, jeśli wszystkie obszary robocze znajdują się w tym samym klastrze. Korzystanie z dedykowanego klastra może być również tańsze, ponieważ przypisana warstwa rezerwacji pojemności uwzględnia całe pozyskiwanie klastra i ma zastosowanie do wszystkich obszarów roboczych, nawet jeśli niektóre z nich są małe i nie kwalifikują się do rabatu rezerwacji pojemności.

Dedykowane klastry wymagają od klientów zatwierdzeń przy użyciu pojemności co najmniej 1 TB pozyskiwania danych dziennie. Migracja do dedykowanego klastra jest prosta. Brak utraty danych lub przerw w świadczeniu usług. 

## <a name="management"></a>Zarządzanie 

Dedykowane klastry są zarządzane za pośrednictwem zasobu platformy Azure, który reprezentuje Azure Monitor klastrów dzienników. Wszystkie operacje są wykonywane na tym zasobie przy użyciu programu PowerShell lub interfejsu API REST.

Po utworzeniu klastra można go skonfigurować i połączyć z nim obszary robocze. Gdy obszar roboczy jest połączony z klastrem, nowe dane wysyłane do obszaru roboczego znajdują się w klastrze. Tylko obszary robocze, które znajdują się w tym samym regionie, co klaster, mogą być połączone z klastrem. Obszary robocze mogą odróżniać się od klastra z pewnymi ograniczeniami. Więcej szczegółów na temat tych ograniczeń znajduje się w tym artykule. 

Dane pozyskane do dedykowanych klastrów są szyfrowane dwa razy — raz na poziomie usługi przy użyciu kluczy zarządzanych przez firmę Microsoft lub [klucza zarządzanego przez klienta](../platform/customer-managed-keys.md), a raz na poziomie infrastruktury przy użyciu dwóch różnych algorytmów szyfrowania i dwóch różnych kluczy. [Szyfrowanie podwójne](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) chroni przed scenariuszem, w którym można złamać jeden z algorytmów szyfrowania lub kluczy. W takim przypadku dodatkowa warstwa szyfrowania nadal chroni dane. Dedykowany klaster umożliwia również ochronę danych za pomocą kontrolki [skrytki](../platform/customer-managed-keys.md#customer-lockbox-preview) .

Wszystkie operacje na poziomie klastra wymagają `Microsoft.OperationalInsights/clusters/write` uprawnienia akcja w klastrze. To uprawnienie można udzielić za pośrednictwem właściciela lub współautora, który zawiera `*/write` akcję, lub za pośrednictwem roli współautor log Analytics, która zawiera `Microsoft.OperationalInsights/*` akcję. Aby uzyskać więcej informacji na temat uprawnień Log Analytics, zobacz [Zarządzanie dostępem do danych dziennika i obszarów roboczych w programie Azure monitor](../platform/manage-access.md). 


## <a name="cluster-pricing-model"></a>Model cenowy klastra

Log Analytics dedykowane klastry używają modelu cenowego rezerwacji pojemności, który wynosi co najmniej 1000 GB/dzień. Każde użycie powyżej poziomu rezerwacji będzie rozliczane według stawki płatności zgodnie z rzeczywistym użyciem.  Informacje o cenach rezerwacji zdolności produkcyjnych są dostępne na [stronie cennika Azure monitor]( https://azure.microsoft.com/pricing/details/monitor/).  

Poziom rezerwacji pojemności klastra jest konfigurowany za pomocą programu programistycznego za pomocą Azure Resource Manager przy użyciu `Capacity` parametru w obszarze `Sku` . Wartość `Capacity` jest określona w jednostkach GB i może mieć wartości 1000 GB/dzień lub więcej w przyrostach wynoszących 100 GB/dzień.

Istnieją dwa tryby rozliczania użycia w klastrze. Można je określić przy użyciu `billingType` parametru podczas konfigurowania klastra. 

1. **Klaster**: w tym przypadku (co jest ustawieniem domyślnym) rozliczanie danych pozyskiwanych odbywa się na poziomie klastra. Pobrane ilości danych z każdego obszaru roboczego skojarzonego z klastrem są agregowane w celu obliczenia dziennego rachunku dla klastra. 

2. **Obszary robocze**: koszty rezerwacji pojemności dla klastra są przydzielone proporcjonalnie do obszarów roboczych w klastrze (po rozpoczęciu obsługi alokacji dla każdego węzła z [Azure Security Center](../../security-center/index.yml) dla każdego obszaru roboczego).

Jeśli obszar roboczy korzysta ze starszej warstwy cenowej na węzeł, gdy jest ona połączona z klastrem, będzie rozliczany na podstawie danych pozyskanych w ramach rezerwacji pojemności klastra i nie jest już na węzeł. Alokacje danych na węzeł z Azure Security Center będą nadal stosowane.

Więcej szczegółów zawiera rozliczenia dla Log Analytics dedykowanych klastrów są dostępne [tutaj]( https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#log-analytics-dedicated-clusters).

## <a name="asynchronous-operations-and-status-check"></a>Operacje asynchroniczne i sprawdzanie stanu

Niektóre kroki konfiguracji działają asynchronicznie, ponieważ nie mogą być szybko wykonywane. Stan w odpowiedzi zawiera może być jedną z następujących wartości: "InProgress", "Aktualizacja", "Usuwanie", "powodzenie" lub "zakończonych niepowodzeniem", w tym kod błędu. Gdy używana jest funkcja REST, odpowiedź początkowo zwraca kod stanu HTTP 202 (zaakceptowane) i nagłówek z właściwością Azure-AsyncOperation:

```JSON
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2020-08-01"
```

Stan operacji asynchronicznej można sprawdzić, wysyłając żądanie GET do Azure-AsyncOperation wartości nagłówka:

```rst
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2020-08-01
Authorization: Bearer <token>
```

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
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
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

Powinno być 202 (zaakceptowane) i nagłówek.

### <a name="check-cluster-provisioning-status"></a>Sprawdź stan aprowizacji klastra

Inicjowanie obsługi klastra Log Analytics trwa dłużej. Stan aprowizacji można sprawdzić na kilka sposobów:

- Uruchom Get-AzOperationalInsightsCluster polecenie programu PowerShell z nazwą grupy zasobów i sprawdź Właściwość ProvisioningState. Wartość jest *ProvisioningAccount* podczas aprowizacji i zakończyła *się pomyślnie* .
  ```powershell
  New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} 
  ```

- Skopiuj wartość Azure-AsyncOperation adresu URL z odpowiedzi i postępuj zgodnie z testem stanu operacji asynchronicznych.

- Wyślij żądanie GET do zasobu *klastra* i sprawdź wartość *provisioningState* . Wartość jest *ProvisioningAccount* podczas aprowizacji i zakończyła *się pomyślnie* .

   ```rst
   GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
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

## <a name="link-a-workspace-to-cluster"></a>Łączenie obszaru roboczego z klastrem

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
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2020-08-01 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

*Odpowiedź*

202 (zaakceptowane) i nagłówek.

### <a name="check-workspace-link-status"></a>Sprawdź stan linku obszaru roboczego
  
W przypadku korzystania z kluczy zarządzanych przez klienta dane pozyskiwane są przechowywane w postaci zaszyfrowanej przy użyciu klucza zarządzanego po operacji skojarzenia, co może potrwać do 90 minut. 

Stan skojarzenia obszaru roboczego można sprawdzić na dwa sposoby:

- Skopiuj wartość Azure-AsyncOperation adresu URL z odpowiedzi i postępuj zgodnie z testem stanu operacji asynchronicznych.

- Wykonaj operację get w obszarze roboczym i sprawdź, czy właściwość *clusterResourceId* jest obecna w odpowiedzi w obszarze *funkcje*.

**Interfejs wiersza polecenia**

```azurecli
az monitor log-analytics cluster show --resource-group "resource-group-name" --name "cluster-name"
```

**Program PowerShell**

```powershell
Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

**REST**

*Call*

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>?api-version=2020-08-01
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

## <a name="change-cluster-properties"></a>Zmień właściwości klastra

Po utworzeniu zasobu *klastra* , który jest w pełni zainicjowany, można edytować dodatkowe właściwości na poziomie klastra przy użyciu programu PowerShell lub interfejsu API REST. Oprócz właściwości, które są dostępne podczas tworzenia klastra, dodatkowe właściwości można ustawić tylko po zainicjowaniu obsługi klastra:

- **keyVaultProperties** — aktualizuje klucz w Azure Key Vault. Zobacz temat [Aktualizowanie klastra przy użyciu identyfikatora klucza](../platform/customer-managed-keys.md#update-cluster-with-key-identifier-details). Zawiera następujące parametry: *KeyVaultUri*, *KeyName*, *wersja* klucza. 
- **rozliczenia** — Właściwość *rozliczenia* określa przypisanie rozliczeń dla zasobu *klastra* i jego danych:
  - **Klaster** (domyślnie) — koszty rezerwacji pojemności dla klastra są przypisywane do zasobu *klastra* .
  - **Obszary robocze** — koszty rezerwacji pojemności dla klastra są przypisywane proporcjonalnie do obszarów roboczych w klastrze, a zasób *klastra* jest rozliczany jako część użycia, jeśli łączna ilość danych pobieranych przez dzień jest objęta rezerwacją pojemności. Zobacz [log Analytics dedykowanych klastrów](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters) , aby dowiedzieć się więcej na temat modelu cen klastra. 

> [!NOTE]
> Właściwość *rozliczeniatype* nie jest obsługiwana w programie PowerShell.

### <a name="get-all-clusters-in-resource-group"></a>Pobierz wszystkie klastry w grupie zasobów
  
**Interfejs wiersza polecenia**

```azurecli
az monitor log-analytics cluster list --resource-group "resource-group-name"
```

**Program PowerShell**

```powershell
Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name"
```

**REST**

*Call*

  ```rst
  GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
  Authorization: Bearer <token>
  ```

*Odpowiedź*
  
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

### <a name="get-all-clusters-in-subscription"></a>Pobierz wszystkie klastry w subskrypcji

**Interfejs wiersza polecenia**

```azurecli
az monitor log-analytics cluster list
```

**Program PowerShell**

```powershell
Get-AzOperationalInsightsCluster
```

**REST**

*Call*

```rst
GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2020-08-01
Authorization: Bearer <token>
```
    
*Odpowiedź*
    
Taka sama jak w przypadku klastrów w grupie zasobów, ale w zakresie subskrypcji.



### <a name="update-capacity-reservation-in-cluster"></a>Aktualizowanie rezerwacji pojemności w klastrze

Gdy ilość danych w połączonych obszarach roboczych zmienia się wraz z upływem czasu i chcesz odpowiednio zaktualizować poziom rezerwacji. Pojemność jest określona w jednostkach GB i może mieć wartości 1000 GB/dzień lub więcej w przyrostach wynoszących 100 GB/dzień. Należy pamiętać, że nie musisz podawać całej treści żądania REST, ale powinna ona zawierać jednostkę SKU.

**Interfejs wiersza polecenia**

```azurecli
az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --sku-capacity 1000
```

**Program PowerShell**

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity 1000
```

**REST**

*Call*

  ```rst
  PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2020-08-01
  Authorization: Bearer <token>
  Content-type: application/json

  {
    "sku": {
      "name": "capacityReservation",
      "Capacity": 2000
    }
  }
  ```

### <a name="update-billingtype-in-cluster"></a>Aktualizowanie w klastrze

Właściwość *rozliczenia* określa przypisanie rozliczeń dla klastra i jego danych:
- *klaster* (domyślnie) — rozliczenia są przypisywane do subskrypcji hostingowej zasobu klastra
- *obszary robocze* — rozliczenia są przypisane do subskrypcji obsługujących obszary robocze proporcjonalnie

**REST**

*Call*

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

### <a name="unlink-a-workspace-from-cluster"></a>Odłączanie obszaru roboczego od klastra

Możesz odłączyć obszar roboczy z klastra. Po odłączeniu obszaru roboczego od klastra nowe dane skojarzone z tym obszarem roboczym nie są wysyłane do dedykowanego klastra. Ponadto rozliczanie obszaru roboczego nie odbywa się już za pośrednictwem klastra. Stare dane niepołączonego obszaru roboczego mogą pozostać w klastrze. Jeśli te dane są szyfrowane przy użyciu kluczy zarządzanych przez klienta (CMK), zostaną zachowane wpisy tajne Key Vault. System jest abstrakcyjny dla tej zmiany od Log Analytics użytkowników. Użytkownicy mogą jedynie wysyłać zapytania dotyczące obszaru roboczego w zwykły sposób. System wykonuje zapytania między klastrami w razie potrzeby bez wskazania użytkownikom.  

> [!WARNING] 
> Istnieje ograniczenie dwóch operacji łączenia określonego obszaru roboczego w ciągu miesiąca. Należy wziąć pod uwagę czas i odpowiednio zaplanować akcje odłączenia.

**Interfejs wiersza polecenia**

```azurecli
az monitor log-analytics workspace linked-service delete --resource-group "resource-group-name" --workspace-name "MyWorkspace" --name cluster
```

**Program PowerShell**

Aby odłączyć obszar roboczy z klastra, użyj następującego polecenia programu PowerShell:

```powershell
# Unlink a workspace from cluster
Remove-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster
```

### <a name="delete-cluster"></a>Usuwanie klastra

Można usunąć dedykowany zasób klastra. Przed usunięciem należy odłączyć wszystkie obszary robocze z klastra. Aby wykonać tę operację, musisz mieć uprawnienia "Write" dotyczące zasobu *klastra* . 

Po usunięciu zasobu klastra fizycznego klaster przechodzi do procesu przeczyszczania i usuwania. Usunięcie klastra powoduje usunięcie wszystkich danych, które były przechowywane w klastrze. Dane mogą pochodzić z obszarów roboczych, które były połączone z klastrem w przeszłości.

Zasób *klastra* , który został usunięty w ciągu ostatnich 14 dni, jest w stanie usuwania nietrwałego i można go odzyskać z danymi. Ponieważ wszystkie obszary robocze zostały usunięte z zasobu *klastra* z usunięciem zasobów *klastra* , po odzyskaniu należy ponownie skojarzyć obszary robocze. Nie można wykonać operacji odzyskiwania przez użytkownika, skontaktuj się z kanałem firmy Microsoft lub pomocą techniczną, aby uzyskać żądania odzyskiwania.

W ciągu 14 dni od usunięcia nazwa zasobu klastra jest zarezerwowana i nie może być używana przez inne zasoby.

> [!WARNING] 
> Istnieje limit trzech klastrów na subskrypcję. Wszystkie aktywne i nietrwałe klastry są zliczane jako część tego elementu. Klienci nie powinni tworzyć cyklicznych procedur, które tworzą i usuwają klastry. Ma znaczący wpływ na Log Analytics systemy zaplecza.

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

## <a name="limits-and-constraints"></a>Limity i ograniczenia

- Maksymalna liczba klastrów na region i subskrypcja wynosi 2

- Maksymalna liczba połączonych obszarów roboczych do klastra to 1000

- Możesz połączyć obszar roboczy z klastrem, a następnie odłączyć go od siebie. Liczba operacji linku obszaru roboczego w określonym obszarze roboczym jest ograniczona do 2 w okresie 30 dni.

- Klaster przeniesiony do innej grupy zasobów lub subskrypcji nie jest obecnie obsługiwany.

- Skrytka nie jest obecnie dostępna w Chinach. 

- [Podwójne szyfrowanie](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) jest konfigurowane automatycznie w przypadku klastrów utworzonych z października 2020 w obsługiwanych regionach. Można sprawdzić, czy klaster jest skonfigurowany do podwójnego szyfrowania, wysyłając żądanie GET w klastrze i obserwując, że `isDoubleEncryptionEnabled` wartość jest `true` w przypadku klastrów z włączonym podwójnym szyfrowaniem. 
  - Jeśli utworzysz klaster i wystąpi błąd "<regionu-Name> nie obsługuje podwójnego szyfrowania klastrów" ", można nadal utworzyć klaster bez podwójnego szyfrowania, dodając `"properties": {"isDoubleEncryptionEnabled": false}` w treści żądania Rest.
  - Ustawienia podwójnego szyfrowania nie można zmienić po utworzeniu klastra.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

- W przypadku wystąpienia błędu konfliktu podczas tworzenia klastra — może to być, że klaster został usunięty w ciągu ostatnich 14 dni i jest w stanie usuwania nietrwałego. Nazwa klastra pozostaje zarezerwowana w okresie usuwania nietrwałego i nie można utworzyć nowego klastra o takiej nazwie. Nazwa jest wydawana po okresie usuwania nietrwałego, gdy klaster zostanie trwale usunięty.

- Jeśli klaster zostanie zaktualizowany w trakcie aprowizacji lub aktualizowania stanu, aktualizacja zakończy się niepowodzeniem.

- Niektóre operacje są długie i mogą chwilę potrwać — są to między innymi tworzenie klastra, Aktualizacja klucza klastra i usuwanie klastra. Stan operacji można sprawdzić na dwa sposoby:
  - W przypadku korzystania z usługi REST skopiuj wartość Azure-AsyncOperation adresu URL z odpowiedzi i postępuj zgodnie ze [sprawdzaniem stanu operacji asynchronicznych](#asynchronous-operations-and-status-check).
  - Wyślij żądanie GET do klastra lub obszaru roboczego i obserwuj odpowiedź. Na przykład niepołączony obszar roboczy nie będzie miał *clusterResourceId* w obszarze *funkcje*.

- Łącze obszaru roboczego do klastra zakończy się niepowodzeniem, jeśli jest ono połączone z innym klastrem.

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
  -  400 — KeyVaultProperties nie jest pusty, ale ma zły format. Zobacz [aktualizacja identyfikatora klucza](../platform/customer-managed-keys.md#update-cluster-with-key-identifier-details).
  -  400 — nie można zweryfikować klucza w Key Vault. Może być spowodowany brakiem uprawnień lub gdy klucz nie istnieje. Upewnij się, że [ustawisz Zasady kluczy i dostępu](../platform/customer-managed-keys.md#grant-key-vault-permissions) w Key Vault.
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

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [log Analytics rozliczania dedykowanego klastra](../platform/manage-cost-storage.md#log-analytics-dedicated-clusters)
- Dowiedz się więcej o [odpowiednim projekcie log Analytics obszarów roboczych](../platform/design-logs-deployment.md)
