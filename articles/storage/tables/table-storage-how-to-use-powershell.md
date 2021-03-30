---
title: Wykonywanie operacji usługi Azure Table Storage za pomocą programu PowerShell | Microsoft Docs
description: Dowiedz się, jak uruchamiać typowe zadania, takie jak tworzenie, wykonywanie zapytań, usuwanie danych z konta usługi Azure Table Storage za pomocą programu PowerShell.
author: roygara
ms.service: storage
ms.topic: article
ms.date: 04/05/2019
ms.author: rogarana
ms.subservice: tables
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9a7502d48095fe18b983c1971d5145f51e766c95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93306902"
---
# <a name="perform-azure-table-storage-operations-with-azure-powershell"></a>Wykonywanie operacji usługi Azure Table Storage za pomocą Azure PowerShell 
[!INCLUDE [storage-table-cosmos-db-tip-include](../../../includes/storage-table-cosmos-db-langsoon-tip-include.md)]

Azure Table Storage to magazyn danych NoSQL, który służy do przechowywania i wykonywania zapytań dotyczących dużych zestawów strukturalnych danych nierelacyjnych. Głównymi składnikami usługi są tabele, jednostki i właściwości. Tabela jest kolekcją jednostek. Jednostka jest zestawem właściwości. Każda jednostka może mieć do 252 właściwości (wszystkie pary nazwa-wartość). W tym artykule przyjęto założenie, że znasz już pojęcia dotyczące usługi Azure Table Storage. Aby uzyskać szczegółowe informacje, zobacz [Omówienie modelu danych usługi Table Service](/rest/api/storageservices/Understanding-the-Table-Service-Data-Model) i rozpoczynanie [pracy z usługą Azure Table Storage przy użyciu platformy .NET](../../cosmos-db/tutorial-develop-table-dotnet.md).

W tym artykule opisano typowe operacje usługi Azure Table Storage. Omawiane kwestie: 

> [!div class="checklist"]
> * Tworzenie tabeli
> * Pobierz tabelę
> * Dodaj jednostki tabeli
> * Tworzenie zapytań względem tabeli
> * Usuń jednostki tabeli
> * Usuwanie tabeli

W tym artykule opisano sposób tworzenia nowego konta usługi Azure Storage w nowej grupie zasobów, dzięki czemu można łatwo usunąć go po zakończeniu. Jeśli wolisz korzystać z istniejącego konta magazynu, możesz to zrobić.

Przykłady wymagają AZ PowerShell modules `Az.Storage (1.1.0 or greater)` i `Az.Resources (1.2.0 or greater)` . W oknie programu PowerShell uruchom polecenie, `Get-Module -ListAvailable Az*` Aby znaleźć wersję. Jeśli nic nie jest wyświetlane lub musisz przeprowadzić uaktualnienie, zobacz [Install Azure PowerShell module](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Korzystanie z tej funkcji platformy Azure z poziomu programu PowerShell wymaga `Az` zainstalowania modułu. Bieżąca wersja programu `AzTable` nie jest zgodna ze starszym modułem AzureRM.
> Postępuj zgodnie z [najnowszymi instrukcjami dotyczącymi instalacji polecenia AZ module w](/powershell/azure/install-az-ps) razie potrzeby.

Po zainstalowaniu lub zaktualizowaniu Azure PowerShell należy zainstalować moduł **AzTable**, który zawiera polecenia służące do zarządzania jednostkami. Aby zainstalować ten moduł, uruchom program PowerShell jako administrator i użyj polecenia **Install-module** .

> [!IMPORTANT]
> Ze względów zgodności nazw modułów nadal publikujemy ten sam moduł pod starą nazwą `AzureRmStorageTables` w Galeria programu PowerShell. Ten dokument odwołuje się tylko do nowej nazwy.

```powershell
Install-Module AzTable
```

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do subskrypcji platformy Azure za pomocą polecenia `Add-AzAccount` i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Add-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Pobierz listę lokalizacji

Jeśli nie wiesz, której lokalizacji użyć, możesz wyświetlić listę dostępnych lokalizacji. Po wyświetleniu listy znajdź lokalizację, której chcesz użyć. W tych przykładach użyto **wschodniego**. Zapisz tę wartość w zmiennej **lokalizacji** do użycia w przyszłości.

```powershell
Get-AzLocation | select Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów za pomocą polecenia [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

Grupa zasobów platformy Azure to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Zapisz nazwę grupy zasobów w zmiennej do użycia w przyszłości. W tym przykładzie grupa zasobów o nazwie *pshtablesrg* jest tworzona w regionie *wschodnim* .

```powershell
$resourceGroup = "pshtablesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Tworzenie konta magazynu

Utwórz standardowe konto magazynu ogólnego przeznaczenia z magazynem lokalnie nadmiarowy (LRS) przy użyciu polecenia [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Pamiętaj, aby określić unikatową nazwę konta magazynu. Następnie Pobierz kontekst reprezentujący konto magazynu. Gdy działa na koncie magazynu, można odwoływać się do kontekstu zamiast powtarzania podawania poświadczeń.

```powershell
$storageAccountName = "pshtablestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
```

## <a name="create-a-new-table"></a>Tworzenie nowej tabeli

Aby utworzyć tabelę, użyj polecenia cmdlet [New-AzStorageTable](/powershell/module/az.storage/New-AzStorageTable) . W tym przykładzie tabela jest wywoływana `pshtesttable` .

```powershell
$tableName = "pshtesttable"
New-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="retrieve-a-list-of-tables-in-the-storage-account"></a>Pobieranie listy tabel na koncie magazynu

Pobierz listę tabel na koncie magazynu za pomocą polecenia [Get-AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
Get-AzStorageTable –Context $ctx | select Name
```

## <a name="retrieve-a-reference-to-a-specific-table"></a>Pobierz odwołanie do określonej tabeli

Aby wykonać operacje na tabeli, należy odwołać się do konkretnej tabeli. Pobierz odwołanie za pomocą polecenia [Get-AzStorageTable](/powershell/module/azure.storage/Get-AzureStorageTable).

```powershell
$storageTable = Get-AzStorageTable –Name $tableName –Context $ctx
```

## <a name="reference-cloudtable-property-of-a-specific-table"></a>Odwołanie do właściwości chmury określonej tabeli

> [!IMPORTANT]
> Użycie w chmurze jest obowiązkowe podczas pracy z modułem **AzTable** PowerShell. Wywołaj polecenie **Get-AzStorageTable** , aby uzyskać odwołanie do tego obiektu. To polecenie tworzy również tabelę, jeśli jeszcze nie istnieje.

Aby wykonać operacje na tabeli przy użyciu **AzTable**, należy odwołać się do właściwości w chmurze określonej tabeli.

```powershell
$cloudTable = (Get-AzStorageTable –Name $tableName –Context $ctx).CloudTable
```

[!INCLUDE [storage-table-entities-powershell-include](../../../includes/storage-table-entities-powershell-include.md)]

## <a name="delete-a-table"></a>Usuwanie tabeli

Aby usunąć tabelę, użyj polecenie [Remove-AzStorageTable](/powershell/module/az.storage/Remove-AzStorageTable). To polecenie cmdlet usuwa tabelę, w tym wszystkie jej dane.

```powershell
Remove-AzStorageTable –Name $tableName –Context $ctx

# Retrieve the list of tables to verify the table has been removed.
Get-AzStorageTable –Context $Ctx | select Name
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli utworzono nową grupę zasobów i konto magazynu na początku tej procedury, możesz usunąć wszystkie zasoby utworzone w tym ćwiczeniu, usuwając grupę zasobów. To polecenie usuwa wszystkie zasoby zawarte w grupie, a także samą grupę zasobów.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym artykule poznasz sposób wykonywania typowych operacji w usłudze Azure Table Storage za pomocą programu PowerShell, w tym: 

> [!div class="checklist"]
> * Tworzenie tabeli
> * Pobierz tabelę
> * Dodaj jednostki tabeli
> * Tworzenie zapytań względem tabeli
> * Usuń jednostki tabeli
> * Usuwanie tabeli

Aby uzyskać więcej informacji, zobacz następujące artykuły

* [Polecenia cmdlet programu PowerShell usługi Storage](/powershell/module/az.storage#storage)

* [Praca z tabelami platformy Azure z poziomu programu PowerShell — AzureRmStorageTable/AzTable PS module v 2.0](https://paulomarquesc.github.io/working-with-azure-storage-tables-from-powershell)

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) jest bezpłatną aplikacją autonomiczną oferowaną przez firmę Microsoft, która umożliwia wizualną pracę z danymi w usłudze Azure Storage w systemach Windows, macOS i Linux.