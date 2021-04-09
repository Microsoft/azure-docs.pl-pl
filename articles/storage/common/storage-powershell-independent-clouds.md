---
title: Zarządzanie danymi w chmurach niezależnych platformy Azure za pomocą programu PowerShell
titleSuffix: Azure Storage
description: Zarządzanie magazynem w chmurze chińskiej, w chmurze dla instytucji rządowych i w niemieckiej chmurze przy użyciu Azure PowerShell.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e924a5f6c765b5b964fe3b1492393b063d9d23b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92783576"
---
# <a name="managing-storage-in-the-azure-independent-clouds-using-powershell"></a>Zarządzanie magazynem w chmurach niezależnych platformy Azure przy użyciu programu PowerShell

Większość osób korzysta z chmury publicznej platformy Azure na potrzeby globalnego wdrożenia platformy Azure. Istnieją także niezależne wdrożenia Microsoft Azure z przyczyn suwerenności i tak dalej. Te niezależne wdrożenia są określane jako "środowiska". Na poniższej liście znajdują się obecnie dostępne niezależne chmury.

* [Azure Government Cloud](https://azure.microsoft.com/features/gov/)
* [Azure — Chiny, Chmura firmy 21Vianet obsługiwana przez firmę 21Vianet w Chinach](http://www.windowsazure.cn/)
* [Chmura w języku niemieckim platformy Azure](../../germany/germany-welcome.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="using-an-independent-cloud"></a>Korzystanie z niezależnej chmury

Aby korzystać z usługi Azure Storage w jednej z chmur niezależnych, możesz połączyć się z tą chmurą zamiast z publicznej platformy Azure. Aby użyć jednej z niezależnych chmur zamiast publicznej platformy Azure:

* Należy określić *środowisko* , z którym ma zostać nawiązane połączenie.
* Można określić i użyć dostępnych regionów.
* Używasz poprawnego sufiksu punktu końcowego, który jest inny niż publiczny na platformie Azure.

Przykłady wymagają modułu Azure PowerShell AZ w wersji 0,7 lub nowszej. W oknie programu PowerShell uruchom polecenie, `Get-Module -ListAvailable Az` Aby znaleźć wersję. Jeśli niczego nie ma na liście lub musisz przeprowadzić uaktualnienie, zobacz [Install Azure PowerShell module](/powershell/azure/install-Az-ps).

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Uruchom polecenie cmdlet [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment) , aby wyświetlić dostępne środowiska platformy Azure:

```powershell
Get-AzEnvironment
```

Zaloguj się do swojego konta, które ma dostęp do chmury, z którą chcesz nawiązać połączenie, i ustaw środowisko. Ten przykład pokazuje, jak zalogować się do konta, które korzysta z chmury Azure Governmentej.   

```powershell
Connect-AzAccount –Environment AzureUSGovernment
```

Aby uzyskać dostęp do chmury chińskiej, użyj środowiska **AzureChinaCloud**. Aby uzyskać dostęp do chmury niemieckiej, użyj **AzureGermanCloud**.

W tym momencie, jeśli potrzebujesz listy lokalizacji do utworzenia konta magazynu lub innego zasobu, możesz wysyłać zapytania o lokalizacje dostępne dla wybranej chmury przy użyciu polecenia [Get-AzLocation](/powershell/module/az.resources/get-azlocation).

```powershell
Get-AzLocation | select Location, DisplayName
```

W poniższej tabeli przedstawiono lokalizacje zwrócone dla chmury niemieckiej.

|Lokalizacja | Nazwa wyświetlana |
|----|----|
| `germanycentral` | Niemcy Środkowe|
| `germanynortheast` | Niemcy Północno-Wschodnie |


## <a name="endpoint-suffix"></a>Sufiks punktu końcowego

Sufiks punktu końcowego dla każdego z tych środowisk różni się od publicznego punktu końcowego platformy Azure. Na przykład sufiks punktu końcowego obiektu BLOB dla publicznej platformy Azure to **BLOB.Core.Windows.NET**. W przypadku chmury rządowej sufiks punktu końcowego obiektu BLOB to **BLOB.Core.usgovcloudapi.NET**.

### <a name="get-endpoint-using-get-azenvironment"></a>Pobierz punkt końcowy przy użyciu Get-AzEnvironment

Pobierz sufiks punktu końcowego za pomocą polecenia [Get-AzEnvironment](/powershell/module/az.accounts/get-azenvironment). Punkt końcowy jest właściwością *StorageEndpointSuffix* środowiska.

Poniższe fragmenty kodu pokazują, jak pobrać sufiks punktu końcowego. Wszystkie te polecenia zwracają coś takiego jak "core.cloudapp.net" lub "core.cloudapi.de" itp. Dołącz sufiks do usługi Storage, aby uzyskać dostęp do tej usługi. Na przykład "queue.core.cloudapi.de" będzie uzyskiwać dostęp do usługi kolejki w chmurze niemieckiej.

Ten fragment kodu pobiera wszystkie środowiska i sufiks punktu końcowego dla każdej z nich.

```powershell
Get-AzEnvironment | select Name, StorageEndpointSuffix 
```

To polecenie zwraca następujące wyniki.

| Nazwa| StorageEndpointSuffix|
|----|----|
| AzureChinaCloud | core.chinacloudapi.cn|
| AzureCloud | core.windows.net |
| AzureGermanCloud | core.cloudapi.de|
| AzureUSGovernment | core.usgovcloudapi.net |

Aby pobrać wszystkie właściwości dla określonego środowiska, wywołaj polecenie **Get-AzEnvironment** i podaj nazwę chmury. Ten fragment kodu zwraca listę właściwości; Poszukaj **StorageEndpointSuffix** na liście. Poniższy przykład dotyczy chmury niemieckiej.

```powershell
Get-AzEnvironment -Name AzureGermanCloud
```

Wyniki są podobne do następujących:

|Nazwa właściwości|Wartość|
|----|----|
| Nazwa | `AzureGermanCloud` |
| EnableAdfsAuthentication | `False` |
| ActiveDirectoryServiceEndpointResourceI | `http://management.core.cloudapi.de/` |
| GalleryURL | `https://gallery.cloudapi.de/` |
| ManagementPortalUrl | `https://portal.microsoftazure.de/` |
| ServiceManagementUrl | `https://manage.core.cloudapi.de/` |
| PublishSettingsFileUrl| `https://manage.microsoftazure.de/publishsettings/index` |
| ResourceManagerUrl | `http://management.microsoftazure.de/` |
| SqlDatabaseDnsSuffix | `.database.cloudapi.de` |
| **StorageEndpointSuffix** | `core.cloudapi.de` |
| ... | ... |

Aby pobrać tylko Właściwość sufiksu punktu końcowego magazynu, Pobierz określoną chmurę i podawaj tylko jedną właściwość.

```powershell
$environment = Get-AzEnvironment -Name AzureGermanCloud
Write-Host "Storage EndPoint Suffix = " $environment.StorageEndpointSuffix
```

To polecenie zwraca następujące informacje:

`Storage Endpoint Suffix = core.cloudapi.de`

### <a name="get-endpoint-from-a-storage-account"></a>Pobierz punkt końcowy z konta magazynu

Możesz również przejrzeć właściwości konta magazynu, aby pobrać punkty końcowe:

```powershell
# Get a reference to the storage account.
$resourceGroup = "myexistingresourcegroup"
$storageAccountName = "myexistingstorageaccount"
$storageAccount = Get-AzStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName 
  # Output the endpoints.
Write-Host "blob endpoint = " $storageAccount.PrimaryEndPoints.Blob 
Write-Host "file endpoint = " $storageAccount.PrimaryEndPoints.File
Write-Host "queue endpoint = " $storageAccount.PrimaryEndPoints.Queue
Write-Host "table endpoint = " $storageAccount.PrimaryEndPoints.Table
```

W przypadku konta magazynu w chmurze dla instytucji rządowych to polecenie zwraca następujące dane wyjściowe:

```
blob endpoint = http://myexistingstorageaccount.blob.core.usgovcloudapi.net/
file endpoint = http://myexistingstorageaccount.file.core.usgovcloudapi.net/
queue endpoint = http://myexistingstorageaccount.queue.core.usgovcloudapi.net/
table endpoint = http://myexistingstorageaccount.table.core.usgovcloudapi.net/
```

## <a name="after-setting-the-environment"></a>Po ustawieniu środowiska

Teraz można używać programu PowerShell do zarządzania kontami magazynu i dostępu do danych obiektów blob, kolejek, plików i tabel. Aby uzyskać więcej informacji, zobacz [AZ. Storage](/powershell/module/az.storage).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli utworzono nową grupę zasobów i konto magazynu dla tego ćwiczenia, można usunąć oba elementy zawartości, usuwając grupę zasobów. Usunięcie grupy zasobów spowoduje usunięcie wszystkich zasobów w tej grupie.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Następne kroki

* [Zachowywanie danych logowania użytkownika między sesjami programu PowerShell](/powershell/azure/context-persistence)
* [Magazyn Azure Government](../../azure-government/compare-azure-government-global-azure.md)
* [Przewodnik dla deweloperów Microsoft Azure Government](../../azure-government/documentation-government-developer-guide.md)
* [Uwagi dla deweloperów dotyczące aplikacji 21Vianet platformy Azure w Chinach](https://msdn.microsoft.com/library/azure/dn578439.aspx)
* [Dokumentacja platformy Azure (Niemcy)](../../germany/germany-welcome.md)