---
title: Zarządzanie cyklem życia usługi Azure Storage
description: Dowiedz się, jak utworzyć reguły zasad cyklu życia, aby przenieść dane przedawniania z warstwy gorąca do chłodna i archiwum.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/15/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.custom: devx-track-azurepowershell, references_regions
ms.openlocfilehash: 49e82467cd5e9cef8100aa56016f778df3445f12
ms.sourcegitcommit: d2222681e14700bdd65baef97de223fa91c22c55
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/07/2020
ms.locfileid: "91822391"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>Zarządzanie cyklem życia magazynu usługi Azure Blob Storage

Zestawy danych mają unikatowe cykle życia. Wczesne w cyklu życia, ludzie często uzyskują dostęp do niektórych danych. Jednak konieczność uzyskania dostępu spadnie drastycznie jako wiek danych. Niektóre dane pozostają w stanie bezczynności w chmurze i są rzadko dostępne po ich zapisaniu. Niektóre dane wygasają w dniach lub miesiącach po utworzeniu, podczas gdy inne zestawy danych są aktywnie odczytywane i modyfikowane przez cały okres istnienia. Zarządzanie cyklem życia usługi Azure Blob Storage oferuje zaawansowane zasady oparte na regułach dla kont GPv2 i BLOB Storage. Użyj zasad, aby przenieść dane do odpowiednich warstw dostępu lub wygasnąć po zakończeniu cyklu życia danych.

Zasady zarządzania cyklem życia umożliwiają:

- Przechodzenie obiektów BLOB do warstwy magazynowania chłodnicy (gorąca do chłodna, gorąca do archiwum lub chłodna do archiwum) w celu optymalizacji pod kątem wydajności i kosztów
- Usuwanie obiektów BLOB na końcu ich cykli życia
- Zdefiniuj reguły, które mają być uruchamiane raz dziennie na poziomie konta magazynu
- Stosowanie reguł do kontenerów lub podzbioru obiektów BLOB (przy użyciu prefiksów nazw lub [tagów indeksów obiektów BLOB](storage-manage-find-blobs.md) jako filtrów)

Rozważmy scenariusz, w którym dane są często dostępne podczas wczesnych etapów cyklu życia, ale tylko sporadycznie po dwóch tygodniach. Po upływie pierwszego miesiąca zestaw danych jest rzadko używany. W tym scenariuszu w przypadku wczesnych etapów jest najlepszym miejscem do magazynowania. Chłodny magazyn jest najbardziej odpowiedni dla okazjonalnego dostępu. Magazyn archiwalny to opcja najlepszej warstwy po okresie ważności danych w miesiącu. Przez dostosowanie warstw magazynowania w odniesieniu do wieku danych można zaprojektować najtańsze opcje magazynu dla Twoich potrzeb. W celu osiągnięcia tego przejścia reguły zasad zarządzania cyklem życia są dostępne do przenoszenia danych przedawnienia do warstwy z chłodnicy.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="availability-and-pricing"></a>Dostępność i Cennik

Funkcja zarządzania cyklem życia jest dostępna we wszystkich regionach platformy Azure dla kont Ogólnego przeznaczenia v2 (GPv2), kont usługi BLOB Storage i bloków Premium BLOB Storage. W Azure Portal można uaktualnić istniejące konto Ogólnego przeznaczenia (GPv1) do konta GPv2. Aby uzyskać więcej informacji na temat kont magazynu, zobacz [Omówienie konta usługi Azure Storage](../common/storage-account-overview.md).

Funkcja zarządzania cyklem życia jest bezpłatna. Klienci są obciążani kosztami zwykłych operacji dla wywołań interfejsu API [zestawu warstwy obiektów BLOB](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) . Operacja usuwania jest bezpłatna. Aby uzyskać więcej informacji na temat cen, zobacz temat [Block BLOB — Cennik](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="add-or-remove-a-policy"></a>Dodawanie lub usuwanie zasad

Można dodawać, edytować lub usuwać zasady przy użyciu dowolnej z następujących metod:

* [Azure Portal](https://portal.azure.com)
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [Interfejsy API REST](https://docs.microsoft.com/rest/api/storagerp/managementpolicies)

Zasady mogą być odczytywane lub zapisywane w całości. Aktualizacje częściowe nie są obsługiwane. 

> [!NOTE]
> Jeśli włączysz reguły zapory dla konta magazynu, żądania zarządzania cyklem życia mogą zostać zablokowane. Można odblokować te żądania, dostarczając wyjątki dla zaufanych usług firmy Microsoft. Aby uzyskać więcej informacji, zobacz sekcję wyjątki w artykule [Konfigurowanie zapór i sieci wirtualnych](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

W tym artykule pokazano, jak zarządzać zasadami przy użyciu portalu i metod programu PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Istnieją dwa sposoby dodawania zasad za pomocą Azure Portal. 

* [Widok listy Azure Portal](#azure-portal-list-view)
* [Widok kodu Azure Portal](#azure-portal-code-view)

#### <a name="azure-portal-list-view"></a>Widok listy Azure Portal

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W Azure Portal Wyszukaj i wybierz konto magazynu. 

1. W obszarze **BLOB Service**wybierz pozycję **Zarządzanie cyklem życia** , aby wyświetlić lub zmienić reguły.

1. Wybierz kartę **Widok listy** .

1. Wybierz pozycję **Dodaj regułę** i nadaj jej nazwę w formularzu **szczegóły** . Można również ustawić **Zakres reguły**, **Typ obiektu BLOB**i wartości **podtypu obiektu BLOB** . W poniższym przykładzie ustawiono zakres filtrowania obiektów BLOB. Powoduje to dodanie karty **zestawu filtrów** .

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-details.png" alt-text="Zarządzanie cyklem życia Dodawanie strony szczegółów reguły w Azure Portal":::

1. Wybierz **podstawowe obiekty blob** , aby ustawić warunki dla reguły. W poniższym przykładzie obiekty blob są przenoszone do magazynu chłodnego, jeśli nie zostały zmodyfikowane przez 30 dni.

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-base-blobs.png" alt-text="Zarządzanie cyklem życia Dodawanie strony szczegółów reguły w Azure Portal":::

   **Ostatnia** dostępna opcja jest dostępna w wersji zapoznawczej w następujących regionach:

    - Francja Środkowa
    - Kanada Wschodnia
    - Kanada Środkowa

   > [!IMPORTANT]
   > Ostatnia wersja zapoznawcza śledzenia czasu dostępu jest używana tylko w przypadku nieprodukcji. Umowy dotyczące poziomu usług produkcyjnych (umowy SLA) nie są obecnie dostępne.
   
   Aby można było użyć **ostatnio używanej** opcji, wybierz opcję **Śledzenie dostępu włączone** na stronie **zarządzania cyklem życia** w Azure Portal. Aby uzyskać więcej informacji na temat **ostatnio używanej** opcji, zobacz [przenoszenie danych na podstawie daty ostatniego dostępu (wersja zapoznawcza)](#move-data-based-on-last-accessed-date-preview).

1. W przypadku wybrania opcji **Ogranicz obiekty blob z filtrami** na stronie **szczegółów** wybierz opcję **Filtr zestaw** , aby dodać opcjonalny filtr. Poniższy przykład filtruje obiekty blob w kontenerze *mylifecyclecontainer* , który rozpoczyna się od "log".

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-filter-set.png" alt-text="Zarządzanie cyklem życia Dodawanie strony szczegółów reguły w Azure Portal":::

1. Wybierz pozycję **Dodaj** , aby dodać nowe zasady.

#### <a name="azure-portal-code-view"></a>Widok kodu Azure Portal
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W Azure Portal Wyszukaj i wybierz konto magazynu.

1. W obszarze **BLOB Service**wybierz pozycję **Zarządzanie cyklem życia** , aby wyświetlić lub zmienić zasady.

1. Poniższy kod JSON jest przykładem zasad, które można wkleić do karty **Widok kodu** .

   ```json
   {
     "rules": [
       {
         "enabled": true,
         "name": "move-to-cool",
         "type": "Lifecycle",
         "definition": {
           "actions": {
             "baseBlob": {
               "tierToCool": {
                 "daysAfterModificationGreaterThan": 30
               }
             }
           },
           "filters": {
             "blobTypes": [
               "blockBlob"
             ],
             "prefixMatch": [
               "mylifecyclecontainer/log"
             ]
           }
         }
       }
     ]
   }
   ```

1. Wybierz pozycję **Zapisz**.

1. Aby uzyskać więcej informacji na temat tego przykładu JSON, zobacz sekcję [zasad](#policy) i [reguł](#rules) .

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Poniższy skrypt programu PowerShell może służyć do dodawania zasad do konta magazynu. `$rgname`Zmienna musi być zainicjowana przy użyciu nazwy grupy zasobów. `$accountName`Zmienna musi zostać zainicjowana przy użyciu nazwy konta magazynu.

```powershell
#Install the latest module
Install-Module -Name Az -Repository PSGallery

#Initialize the following with your resource group and storage account names
$rgname = ""
$accountName = ""

#Create a new action object
$action = Add-AzStorageAccountManagementPolicyAction -BaseBlobAction Delete -daysAfterModificationGreaterThan 2555
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToArchive -daysAfterModificationGreaterThan 90
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToCool -daysAfterModificationGreaterThan 30
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -SnapshotAction Delete -daysAfterCreationGreaterThan 90

# Create a new filter object
# PowerShell automatically sets BlobType as “blockblob” because it is the only available option currently
$filter = New-AzStorageAccountManagementPolicyFilter -PrefixMatch ab,cd

#Create a new rule object
#PowerShell automatically sets Type as “Lifecycle” because it is the only available option currently
$rule1 = New-AzStorageAccountManagementPolicyRule -Name Test -Action $action -Filter $filter

#Set the policy
Set-AzStorageAccountManagementPolicy -ResourceGroupName $rgname -StorageAccountName $accountName -Rule $rule1
```

# <a name="template"></a>[Szablon](#tab/template)

Zarządzanie cyklem życia można definiować za pomocą szablonów Azure Resource Manager. Oto przykładowy szablon służący do wdrożenia konta magazynu RA-GRS GPv2 z zasadami zarządzania cyklem życia.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "storageAccountName": "[uniqueString(resourceGroup().id)]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-04-01",
      "sku": {
        "name": "Standard_RAGRS"
      },
      "kind": "StorageV2",
      "properties": {
        "networkAcls": {}
      }
    },
    {
      "name": "[concat(variables('storageAccountName'), '/default')]",
      "type": "Microsoft.Storage/storageAccounts/managementPolicies",
      "apiVersion": "2019-04-01",
      "dependsOn": [
        "[variables('storageAccountName')]"
      ],
      "properties": {
        "policy": {...}
      }
    }
  ],
  "outputs": {}
}
```

---

## <a name="policy"></a>Zasady

Zasady zarządzania cyklem życia to zbiór reguł w dokumencie JSON:

```json
{
  "rules": [
    {
      "name": "rule1",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {...}
    },
    {
      "name": "rule2",
      "type": "Lifecycle",
      "definition": {...}
    }
  ]
}
```

Zasady są kolekcją reguł:

| Nazwa parametru | Typ parametru | Uwagi |
|----------------|----------------|-------|
| `rules`        | Tablica obiektów reguł | W zasadach jest wymagana co najmniej jedna reguła. W zasadach można zdefiniować maksymalnie 100 reguł.|

Każda reguła w ramach zasad ma kilka parametrów:

| Nazwa parametru | Typ parametru | Uwagi | Wymagane |
|----------------|----------------|-------|----------|
| `name`         | String |Nazwa reguły może zawierać do 256 znaków alfanumerycznych. W nazwie reguły jest rozróżniana wielkość liter. Musi być unikatowa w ramach zasad. | Prawda |
| `enabled`      | Boolean | Opcjonalna wartość logiczna zezwalająca na tymczasowe wyłączenie reguły. Wartość domyślna to true, jeśli nie została ustawiona. | Fałsz | 
| `type`         | Wartość wyliczenia | Bieżący prawidłowy typ to `Lifecycle` . | Prawda |
| `definition`   | Obiekt, który definiuje regułę cyklu życia | Każda definicja składa się z zestawu filtrów i zestawu akcji. | Prawda |

## <a name="rules"></a>Reguły

Każda definicja reguły zawiera zestaw filtrów i ustawioną akcję. [Ustawienia filtru ustawiają](#rule-filters) ograniczenia akcji do określonego zestawu obiektów w nazwach kontenerów lub obiektów. [Zestaw akcji](#rule-actions) stosuje akcje warstwy lub usuwania do filtrowanego zestawu obiektów.

### <a name="sample-rule"></a>Przykładowa reguła

Następująca przykładowa reguła filtruje konto, aby uruchomić akcje na obiektach, które istnieją w `container1` programie, i zacznij od `foo` .

>[!NOTE]
>- Zarządzanie cyklem życia obsługuje blokowe obiekty blob i dołączanie typów obiektów BLOB.<br>
>- Zarządzanie cyklem życia nie wpływa na kontenery systemowe, takie jak $logs i $web.

- Warstwa BLOB warstwy do warstwy chłodna 30 dni po ostatniej modyfikacji
- Warstwa BLOB warstwy do archiwum 90 dni po ostatniej modyfikacji
- Usuń obiekt BLOB 2 555 dni (siedem lat) po ostatniej modyfikacji
- Usuwanie migawek obiektów BLOB 90 dni po utworzeniu migawki

```json
{
  "rules": [
    {
      "name": "ruleFoo",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
            "delete": { "daysAfterModificationGreaterThan": 2555 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="rule-filters"></a>Filtry reguł

Filtry ograniczają akcje reguły do podzbioru obiektów BLOB w ramach konta magazynu. Jeśli zdefiniowano więcej niż jeden filtr, funkcja logiczna `AND` jest uruchamiana na wszystkich filtrach.

Dostępne są następujące filtry:

| Nazwa filtru | Typ filtru | Uwagi | Jest wymagana |
|-------------|-------------|-------|-------------|
| blobTypes   | Tablica wstępnie zdefiniowanych wartości wyliczeniowych. | Bieżąca wersja obsługuje `blockBlob` i `appendBlob` . Tylko usuwanie jest obsługiwane dla programu `appendBlob` , warstwa zestawu nie jest obsługiwana. | Tak |
| prefixMatch | Tablica ciągów dla prefiksów, które mają zostać dopasowane. Każda reguła może definiować do 10 prefiksów. Ciąg prefiksu musi rozpoczynać się od nazwy kontenera. Na przykład jeśli chcesz dopasować wszystkie obiekty blob w ramach `https://myaccount.blob.core.windows.net/container1/foo/...` reguły, prefixMatch jest `container1/foo` . | Jeśli nie zdefiniujesz prefixMatch, reguła będzie stosowana do wszystkich obiektów BLOB w ramach konta magazynu. | Nie |
| blobIndexMatch | Tablica wartości słownika składająca się z klucza znacznika indeksu obiektów blob i warunków wartości do dopasowania. Każda reguła może definiować do 10 warunek tagu indeksu obiektów BLOB. Na przykład, jeśli chcesz dopasować wszystkie obiekty blob w `Project = Contoso` ramach `https://myaccount.blob.core.windows.net/` reguły, blobIndexMatch to `{"name": "Project","op": "==","value": "Contoso"}` . | Jeśli nie zdefiniujesz blobIndexMatch, reguła będzie stosowana do wszystkich obiektów BLOB w ramach konta magazynu. | Nie |

> [!NOTE]
> Indeks obiektów BLOB jest w publicznej wersji zapoznawczej i jest dostępny w regionach **Kanada środkowa**, **Kanada Wschodnia**, **Francja środkowa**i **Francja Południowa** . Aby dowiedzieć się więcej na temat tej funkcji wraz z znanymi problemami i ograniczeniami, zobacz artykuł [Zarządzanie danymi na platformie Azure Blob Storage przy użyciu indeksu obiektów BLOB (wersja zapoznawcza)](storage-manage-find-blobs.md).

### <a name="rule-actions"></a>Akcje reguły

Akcje są stosowane do filtrowanych obiektów blob, gdy spełniony jest warunek uruchomienia.

Zarządzanie cyklem życia obsługuje warstwowe i usuwanie obiektów blob oraz usuwanie migawek obiektów BLOB. Zdefiniuj co najmniej jedną akcję dla każdej reguły dla obiektów blob lub migawek obiektów BLOB.

| Akcja                      | Podstawowy obiekt BLOB                                   | Snapshot      |
|-----------------------------|---------------------------------------------|---------------|
| tierToCool                  | Obsługa obiektów BLOB obecnie w warstwie gorąca         | Nieobsługiwane |
| enableAutoTierToHotFromCool | Obsługa obiektów BLOB obecnie w warstwie chłodna        | Nieobsługiwane |
| tierToArchive               | Obsługa obiektów BLOB obecnie w warstwie gorąca lub chłodna | Nieobsługiwane |
| delete                      | Obsługiwane przez `blockBlob` i `appendBlob`  | Obsługiwane     |

>[!NOTE]
>W przypadku zdefiniowania więcej niż jednej akcji w tym samym obiekcie blob Zarządzanie cyklem życia stosuje najtańszą akcję do obiektu BLOB. Na przykład akcja `delete` jest tańsza niż Akcja `tierToArchive` . Akcja `tierToArchive` jest tańsza niż Akcja `tierToCool` .

Warunki uruchamiania są oparte na wieku. Podstawowe obiekty blob używają czasu ostatniej modyfikacji do śledzenia wieku, a migawki obiektów BLOB używają czasu utworzenia migawki do śledzenia wieku.

| Warunek uruchomienia akcji               | Wartość warunku                          | Opis                                                                      |
|------------------------------------|------------------------------------------|----------------------------------------------------------------------------------|
| daysAfterModificationGreaterThan   | Wartość całkowita wskazująca wiek w dniach | Warunek dla podstawowych akcji obiektu BLOB                                              |
| daysAfterCreationGreaterThan       | Wartość całkowita wskazująca wiek w dniach | Warunek akcji migawek obiektów BLOB                                          |
| daysAfterLastAccessTimeGreaterThan | Wartość całkowita wskazująca wiek w dniach | przeglądania Warunek dla akcji podstawowego obiektu BLOB, gdy czas ostatniego dostępu jest włączony |

## <a name="examples"></a>Przykłady

W poniższych przykładach pokazano, jak rozwiązywać typowe scenariusze z regułami zasad cyklu życia.

### <a name="move-aging-data-to-a-cooler-tier"></a>Przenoszenie danych przedawnienia do warstwy chłodnicy

Ten przykład pokazuje, jak przejść blokowe obiekty blob poprzedzone prefiksem `container1/foo` lub `container2/bar` . Zasady przeniesieją obiekty blob, które nie zostały zmodyfikowane w ciągu 30 dni do magazynu chłodnego, i nie są modyfikowane przez 90 dni w warstwie archiwum:

```json
{
  "rules": [
    {
      "name": "agingRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo", "container2/bar" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="move-data-based-on-last-accessed-date-preview"></a>Przenieś dane na podstawie daty ostatniego dostępu (wersja zapoznawcza)

Możesz włączyć śledzenie czasu ostatniego dostępu, aby zachować rekord, kiedy obiekt BLOB jest ostatnio odczytywany lub zapisywana. Aby zarządzać warstwami i przechowywaniem danych obiektów blob, można użyć czasu ostatniego dostępu jako filtru.

**Ostatnia** dostępna opcja jest dostępna w wersji zapoznawczej w następujących regionach:

 - Francja Środkowa
 - Kanada Wschodnia
 - Kanada Środkowa

> [!IMPORTANT]
> Ostatnia wersja zapoznawcza śledzenia czasu dostępu jest używana tylko w przypadku nieprodukcji. Umowy dotyczące poziomu usług produkcyjnych (umowy SLA) nie są obecnie dostępne.

Aby można było użyć **ostatnio używanej** opcji, wybierz opcję **Śledzenie dostępu włączone** na stronie **zarządzania cyklem życia** w Azure Portal.

#### <a name="how-last-access-time-tracking-works"></a>Jak działa śledzenie czasu ostatniego dostępu

Gdy włączone jest śledzenie czasu ostatniego dostępu, wywoływana właściwość obiektu BLOB `LastAccessTime` jest aktualizowana, gdy obiekt BLOB jest odczytywany lub zapisywana. Operacja [pobierania obiektu BLOB](/rest/api/storageservices/get-blob) jest uznawana za operację dostępu. [Pobieranie właściwości obiektów BLOB](/rest/api/storageservices/get-blob-properties), [Pobieranie metadanych obiektów BLOB](/rest/api/storageservices/get-blob-metadata)i [pobieranie tagów obiektów BLOB](/rest/api/storageservices/get-blob-tags) nie ma dostępu i w związku z tym nie należy aktualizować czasu ostatniego dostępu.

Aby zminimalizować wpływ na opóźnienie dostępu do odczytu, tylko pierwszy odczyt ostatnich 24 godzin aktualizuje czas ostatniego dostępu. Kolejne odczyty w tym samym okresie 24-godzinnym nie aktualizują czasu ostatniego dostępu. Jeśli obiekt BLOB jest modyfikowany między operacjami odczytu, czas ostatniego dostępu jest większy od dwóch wartości.

W poniższym przykładzie obiekty blob są przenoszone do magazynu chłodnego, jeśli nie były dostępne przez 30 dni. `enableAutoTierToHotFromCool`Właściwość jest wartością logiczną, która wskazuje, czy obiekt BLOB powinien być automatycznie warstwowy od ochłodzenia z powrotem do gorącego, jeśli jest dostępny ponownie po warstwach.

```json
{
  "enabled": true,
  "name": "last-accessed-thirty-days-ago",
  "type": "Lifecycle",
  "definition": {
    "actions": {
      "baseBlob": {
        "enableAutoTierToHotFromCool": true,
        "tierToCool": {
          "daysAfterLastAccessTimeGreaterThan": 30
        }
      }
    },
    "filters": {
      "blobTypes": [
        "blockBlob"
      ],
      "prefixMatch": [
        "mylifecyclecontainer/log"
      ]
    }
  }
}
```

#### <a name="storage-account-support"></a>Obsługa kont magazynu

Funkcja śledzenia czasu ostatniego dostępu jest dostępna dla następujących typów kont magazynu:

 - Konta magazynu ogólnego przeznaczenia w wersji 2
 - Blokuj konta magazynu obiektów BLOB
 - Konta usługi Blob Storage

Jeśli konto magazynu jest kontem ogólnego przeznaczenia w wersji 1, użyj Azure Portal, aby przeprowadzić uaktualnienie do konta ogólnego przeznaczenia w wersji 2.

Konta magazynu z hierarchiczną przestrzenią nazw włączone do użycia z Azure Data Lake Storage Gen2 nie są jeszcze obsługiwane.

#### <a name="pricing-and-billing"></a>Cennik i rozliczenia

Każda aktualizacja czasu ostatniego dostępu jest traktowana jako [inna operacja](https://azure.microsoft.com/pricing/details/storage/blobs/).

### <a name="archive-data-after-ingest"></a>Archiwizuj dane po pozyskaniu

Niektóre dane pozostają w stanie bezczynności w chmurze i są rzadko, jeśli kiedykolwiek są dostępne. Następujące zasady cyklu życia są skonfigurowane tak, aby dane były archiwizowane wkrótce po ich pozyskaniu. Ten przykład przechodzi do blokowych obiektów BLOB na koncie magazynu w ramach kontenera `archivecontainer` do warstwy archiwum. Przejście jest realizowane przez działanie w przypadku obiektów BLOB 0 dni od czasu ostatniej modyfikacji:

> [!NOTE] 
> Zaleca się przekazywanie obiektów BLOB bezpośrednio do warstwy archiwum, aby zwiększyć efektywność. Możesz użyć nagłówka x-MS-Access-Version dla [PutBlob](https://docs.microsoft.com/rest/api/storageservices/put-blob) lub [PUTBLOCKLIST](https://docs.microsoft.com/rest/api/storageservices/put-block-list) z wersją REST 2018-11-09 lub nowszą lub najnowszymi bibliotekami klienta usługi BLOB Storage. 

```json
{
  "rules": [
    {
      "name": "archiveRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "archivecontainer" ]
        },
        "actions": {
          "baseBlob": {
              "tierToArchive": { "daysAfterModificationGreaterThan": 0 }
          }
        }
      }
    }
  ]
}

```

### <a name="expire-data-based-on-age"></a>Wygaśnie dane na podstawie wieku

Niektóre dane oczekują na wygaśnięcie dni lub miesięcy po utworzeniu. Zasady zarządzania cyklem życia można skonfigurować tak, aby wygaśnie dane przez usunięcie na podstawie wieku danych. W poniższym przykładzie przedstawiono zasady, które usuwają wszystkie blokowe obiekty blob starsze niż 365 dni.

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 365 }
          }
        }
      }
    }
  ]
}
```

### <a name="delete-data-with-blob-index-tags"></a>Usuwanie danych za pomocą tagów indeksu obiektów BLOB
Niektóre dane powinny być wygasłe tylko wtedy, gdy są jawnie oznaczone do usunięcia. Zasady zarządzania cyklem życia można skonfigurować tak, aby wygaśnie dane oznaczone przy użyciu atrybutów klucz/wartość indeksu obiektów BLOB. W poniższym przykładzie przedstawiono zasady, które usuwa wszystkie blokowe obiekty blob otagowane przy użyciu `Project = Contoso` . Aby dowiedzieć się więcej na temat indeksu obiektów blob, zobacz temat [Zarządzanie danymi i znajdowanie ich w usłudze Azure Blob Storage przy użyciu indeksu obiektów BLOB (wersja zapoznawcza)](storage-manage-find-blobs.md).

```json
{
    "rules": [
        {
            "enabled": true,
            "name": "DeleteContosoData",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "delete": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Project",
                            "op": "==",
                            "value": "Contoso"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ]
                }
            }
        }
    ]
}
```

### <a name="delete-old-snapshots"></a>Usuń stare migawki

W przypadku danych, które są regularnie modyfikowane i dostępne przez cały okres istnienia, migawki są często używane do śledzenia starszych wersji danych. Można utworzyć zasady, które usuwa stare migawki na podstawie wieku migawki. Wiek migawki jest określany przez ocenę czasu utworzenia migawki. Ta zasada zasad usuwa migawki blokowych obiektów BLOB w kontenerze `activedata` o 90 dni lub starszej wersji po utworzeniu migawki.

```json
{
  "rules": [
    {
      "name": "snapshotRule",
      "enabled": true,
      "type": "Lifecycle",
    "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "activedata" ]
        },
        "actions": {
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

## <a name="faq"></a>Często zadawane pytania

**Po utworzeniu nowych zasad nie są one uruchamiane natychmiast?**

Na platformie są uruchamiane zasady cyklu życia raz dziennie. Po skonfigurowaniu zasad może upłynąć nawet 24 godziny, aby niektóre akcje działały po raz pierwszy.

**Kiedy aktualizujesz istniejące zasady, jak długo trwa wykonywanie akcji?**

Zastosowanie zaktualizowanych zasad może zająć do 24 godzin. Gdy zasady będą obowiązywać, wykonanie akcji może potrwać do 24 godzin. W związku z tym akcje zasad mogą potrwać do 48 godzin.

**Ręcznie ponownie odwodniono zarchiwizowany obiekt BLOB, jak zapobiegać przenoszeniu go z powrotem do warstwy Archiwum?**

Gdy obiekt BLOB jest przenoszony z jednej warstwy dostępu do innej, jego Ostatnia modyfikacja nie zmienia się. W przypadku ręcznego ponownego przełączenia zarchiwizowanego obiektu BLOB do warstwy gorąca można go przenieść z powrotem do warstwy archiwum przez aparat zarządzania cyklem życia. Wyłącz regułę, która ma wpływ na ten obiekt BLOB tymczasowo, aby uniemożliwić jego ponowne zarchiwizowanie. Włącz ponownie regułę, gdy obiekt BLOB będzie można bezpiecznie przenieść z powrotem do warstwy archiwum. Możesz również skopiować obiekt BLOB do innej lokalizacji, jeśli musi on pozostać w warstwie gorąca lub chłodna.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak odzyskiwać dane po przypadkowym usunięciu:

- [Soft delete for Azure Storage blobs](../blobs/storage-blob-soft-delete.md) (Usuwanie nietrwałe dla obiektów blob usługi Azure Storage)

Dowiedz się, jak zarządzać danymi i znajdować je za pomocą indeksu obiektów blob:

- [Zarządzanie danymi w usłudze Azure Blob Storage i znajdowanie ich przy użyciu indeksu obiektów BLOB](storage-manage-find-blobs.md)
