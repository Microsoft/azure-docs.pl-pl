---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 03/27/2019
ms.author: tamram
ms.openlocfilehash: 9a60c624b181a1efd2f6deebd349daa82214a8a4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "67182906"
---
<!--created by Robin Shahan to go in the articles for table storage w/powershell.
    There is one for Azure Table Storage and one for Azure Cosmos DB Table API -->

## <a name="managing-table-entities"></a>Zarządzanie jednostkami tabeli

Teraz, gdy masz tabelę, przyjrzyjmy się sposobom zarządzania jednostkami lub wierszami w tabeli. 

Jednostki mogą mieć do 255 właściwości, w tym trzy właściwości systemowe: **PartitionKey**, **RowKey** i **timestamp**. Użytkownik jest odpowiedzialny za Wstawianie i aktualizowanie wartości **PartitionKey** i **RowKey**. Serwer zarządza wartością **sygnatury czasowej**, której nie można modyfikować. **PartitionKey** i **RowKey** jednoznacznie identyfikują każdą jednostkę w tabeli.

* **PartitionKey**: Określa partycję, w której jest przechowywana jednostka.
* **RowKey**: unikatowy identyfikator jednostki w ramach partycji.

Można zdefiniować maksymalnie 252 właściwości niestandardowych dla jednostki. 

### <a name="add-table-entities"></a>Dodaj jednostki tabeli

Dodawanie jednostek do tabeli przy użyciu polecenia **Add-AzTableRow**. W tych przykładach użyto kluczy partycji z wartościami `partition1` i `partition2` , a klucze wierszy są równe skrótom stanu. Właściwości w każdej jednostce są `username` i `userid` . 

```powershell
$partitionKey1 = "partition1"
$partitionKey2 = "partition2"

# add four rows 
Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey1 `
    -rowKey ("CA") -property @{"username"="Chris";"userid"=1}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey2 `
    -rowKey ("NM") -property @{"username"="Jessie";"userid"=2}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey1 `
    -rowKey ("WA") -property @{"username"="Christine";"userid"=3}

Add-AzTableRow `
    -table $cloudTable `
    -partitionKey $partitionKey2 `
    -rowKey ("TX") -property @{"username"="Steven";"userid"=4}
```

### <a name="query-the-table-entities"></a>Zapytanie dotyczące jednostek tabeli

Można wysyłać zapytania o jednostki w tabeli za pomocą polecenia **Get-AzTableRow** .

> [!NOTE]
> Polecenia cmdlet **Get-AzureStorageTableRowAll**, **Get-AzureStorageTableRowByPartitionKey**, **Get-AzureStorageTableRowByColumnName** i **Get-AzureStorageTableRowByCustomFilter** są przestarzałe i zostaną usunięte w przyszłej aktualizacji.

#### <a name="retrieve-all-entities"></a>Pobierz wszystkie jednostki

```powershell
Get-AzTableRow -table $cloudTable | ft
```

To polecenie daje wyniki podobne do poniższej tabeli:

| userid | nazwa użytkownika | partition | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |
| 2 | Jessie | partition2 | NM |
| 4 | Steven | partition2 | TX |

#### <a name="retrieve-entities-for-a-specific-partition"></a>Pobieranie jednostek dla określonej partycji

```powershell
Get-AzTableRow -table $cloudTable -partitionKey $partitionKey1 | ft
```

Wyniki wyglądają podobnie jak w poniższej tabeli:

| userid | nazwa użytkownika | partition | rowkey |
|----|---------|---------------|----|
| 1 | Chris | partition1 | CA |
| 3 | Christine | partition1 | WA |

#### <a name="retrieve-entities-for-a-specific-value-in-a-specific-column"></a>Pobierz jednostki dla określonej wartości w określonej kolumnie

```powershell
Get-AzTableRow -table $cloudTable `
    -columnName "username" `
    -value "Chris" `
    -operator Equal
```

To zapytanie pobiera jeden rekord.

|pole|wartość|
|----|----|
| userid | 1 |
| nazwa użytkownika | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

#### <a name="retrieve-entities-using-a-custom-filter"></a>Pobieranie jednostek przy użyciu filtru niestandardowego 

```powershell
Get-AzTableRow `
    -table $cloudTable `
    -customFilter "(userid eq 1)"
```

To zapytanie pobiera jeden rekord.

|pole|wartość|
|----|----|
| userid | 1 |
| nazwa użytkownika | Chris |
| PartitionKey | partition1 |
| RowKey      | CA |

### <a name="updating-entities"></a>Aktualizowanie jednostek 

Aby zaktualizować jednostki, należy wykonać trzy czynności. Najpierw pobierz jednostkę do zmiany. Następnie wprowadź zmianę. Po trzecie Zatwierdź zmianę przy użyciu polecenia **Update-AzTableRow**.

Zaktualizuj jednostkę o nazwie username = "Jessie" na wartość username = "Jessie2". Ten przykład pokazuje również inny sposób tworzenia niestandardowego filtru przy użyciu typów .NET.

```powershell
# Create a filter and get the entity to be updated.
[string]$filter = `
    [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("username",`
    [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Jessie")
$user = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter

# Change the entity.
$user.username = "Jessie2"

# To commit the change, pipe the updated record into the update cmdlet.
$user | Update-AzTableRow -table $cloudTable

# To see the new record, query the table.
Get-AzTableRow -table $cloudTable `
    -customFilter "(username eq 'Jessie2')"
```

Wyniki przedstawiają rekord Jessie2.

|pole|wartość|
|----|----|
| userid | 2 |
| nazwa użytkownika | Jessie2 |
| PartitionKey | partition2 |
| RowKey      | NM |

### <a name="deleting-table-entities"></a>Usuwanie jednostek tabeli

Można usunąć jedną jednostkę lub wszystkie jednostki z tabeli.

#### <a name="deleting-one-entity"></a>Usuwanie jednej jednostki

Aby usunąć pojedynczą jednostkę, Uzyskaj odwołanie do tej jednostki i umieść ją w potoku do elementu **Remove-AzTableRow**.

```powershell
# Set filter.
[string]$filter = `
  [Microsoft.Azure.Cosmos.Table.TableQuery]::GenerateFilterCondition("username",`
  [Microsoft.Azure.Cosmos.Table.QueryComparisons]::Equal,"Jessie2")

# Retrieve entity to be deleted, then pipe it into the remove cmdlet.
$userToDelete = Get-AzTableRow `
    -table $cloudTable `
    -customFilter $filter
$userToDelete | Remove-AzTableRow -table $cloudTable

# Retrieve entities from table and see that Jessie2 has been deleted.
Get-AzTableRow -table $cloudTable | ft
```

#### <a name="delete-all-entities-in-the-table"></a>Usuń wszystkie jednostki z tabeli

Aby usunąć wszystkie jednostki w tabeli, należy je pobrać i powołać wyniki do polecenia cmdlet Remove. 

```powershell
# Get all rows and pipe the result into the remove cmdlet.
Get-AzTableRow `
    -table $cloudTable | Remove-AzTableRow -table $cloudTable 

# List entities in the table (there won't be any).
Get-AzTableRow -table $cloudTable | ft
```
