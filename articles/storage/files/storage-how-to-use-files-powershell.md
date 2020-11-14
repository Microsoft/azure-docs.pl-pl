---
title: Przewodnik Szybki start dotyczący zarządzania udziałami plików platformy Azure przy użyciu programu Azure PowerShell
description: W tym przewodniku Szybki start dowiesz się, jak zarządzać udziałami plików platformy Azure przy użyciu programu Azure PowerShell.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 8b4bd9ece5f010f1294356ad4673543834e5076a
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/14/2020
ms.locfileid: "94626915"
---
# <a name="quickstart-create-and-manage-an-azure-file-share-with-azure-powershell"></a>Szybki start: tworzenie udziałów plików platformy Azure i zarządzanie nimi za pomocą programu Azure PowerShell 
W tym przewodniku przedstawiono podstawowe informacje dotyczące pracy z [udziałami plików platformy Azure](storage-files-introduction.md) przy użyciu programu PowerShell. Udziały plików platformy Azure są podobne do innych udziałów plików, ale są przechowywane w chmurze i obsługiwane przez platformę Azure. Udziały plików platformy Azure obsługują protokół SMB (Server Message Block), Protokół NFS (Network File System) (wersja zapoznawcza) i umożliwia udostępnianie plików na wielu komputerach, aplikacjach i wystąpieniach. 

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Jeśli chcesz zainstalować program PowerShell i używać go lokalnie, ten przewodnik wymaga modułu Az programu Azure PowerShell w wersji 0.7 lub nowszej. Aby dowiedzieć się, jaka wersja modułu Azure PowerShell jest uruchomiona, wykonaj polecenie `Get-Module -ListAvailable Az`. Jeśli konieczne będzie uaktualnienie, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-Az-ps). Jeśli używasz programu PowerShell lokalnie, musisz też uruchomić polecenie `Login-AzAccount`, aby zalogować się na swoje konto platformy Azure.

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów
Grupa zasobów to logiczny kontener przeznaczony do wdrażania zasobów platformy Azure i zarządzania nimi. Jeśli nie masz jeszcze grupy zasobów platformy Azure, możesz utworzyć nową grupę za pomocą polecenia cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

Poniższy przykład tworzy grupę zasobów o nazwie Moja *zasobów* w regionie zachodnie stany USA 2:

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$region = "westus2"

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $region | Out-Null
```

## <a name="create-a-storage-account"></a>Tworzenie konta magazynu
Konto magazynu to udostępniona pula magazynu, za pomocą której można wdrażać udziały plików platformy Azure. Konto magazynu może zawierać nieograniczoną liczbę udziałów, a udział może obejmować nieograniczoną liczbę plików, nieprzekraczającą limitów pojemności konta magazynu. W tym przykładzie tworzony jest program ogólnego przeznaczenia w wersji 2 (GPv2 Storage), który może magazynować standardowe udziały plików platformy Azure lub inne zasoby magazynu, takie jak obiekty blob lub kolejki, na nośniku rotacyjnym dysku twardego. Azure Files obsługuje również dyski półprzewodnikowe w warstwie Premium (dysków SSD); udziały plików platformy Azure w warstwie Premium można tworzyć na kontach magazynu FileStorage.

W tym przykładzie tworzone jest konto magazynu przy użyciu polecenia cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). Konto magazynu ma nazwę *mystorageaccount\<random number>* , a odwołanie do tego konta magazynu jest przechowywane w zmiennej **$storageAcct**. Nazwy kont magazynów muszą być unikatowe, użyj zatem polecenia `Get-Random` w celu dołączenia liczby do nazwy, aby była unikatowa. 

```azurepowershell-interactive 
$storageAccountName = "mystorageacct$(Get-Random)"

$storageAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $region `
    -Kind StorageV2 `
    -SkuName Standard_ZRS `
    -EnableLargeFileShare
```

> [!Note]  
> Udziały większe niż 5 TiB (maksymalnie 100 TiB na udział) są dostępne tylko na kontach magazynu lokalnie nadmiarowy (LRS) i strefowo nadmiarowe (ZRS). Aby utworzyć konto magazynu geograficznie nadmiarowego (GRS) lub strefy geograficznie nadmiarowe (GZRS), Usuń `-EnableLargeFileShare` parametr.

## <a name="create-an-azure-file-share"></a>Tworzenie udziału plików platformy Azure
Teraz możesz utworzyć swój pierwszy udział plików platformy Azure. Udział plików można utworzyć przy użyciu polecenia cmdlet [New-AzRmStorageShare](/powershell/module/az.storage/New-AzRmStorageShare) . W tym przykładzie tworzony jest udział o nazwie `myshare`.

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $shareName `
    -EnabledProtocol SMB `
    -QuotaGiB 1024 | Out-Null
```

Nazwy udziałów muszą składać się z małych liter, cyfr i pojedynczych łączników, ale nie mogą zaczynać się od łącznika. Szczegółowe informacje o nazwach plików i udziałów plików można znaleźć w temacie [Naming and Referencing Shares, Directories, Files, and Metadata](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata) (Nazywanie i odwoływanie się do udziałów, katalogów, plików i metadanych).

## <a name="use-your-azure-file-share"></a>Korzystanie z udziału plików platformy Azure
Usługa Azure Files oferuje dwie metody pracy z plikami i folderami w obrębie udziału plików platformy Azure: zgodny ze standardem branżowym [protokół Server Message Block (SMB)](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) i [protokół REST usługi Files](/rest/api/storageservices/file-service-rest-api). 

Aby zainstalować udział plików za pomocą protokołu SMB, zobacz następujący dokument zgodny z używanym systemem operacyjnym:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Korzystanie z udziału plików platformy Azure za pomocą protokołu REST usługi Files 
Istnieje możliwość bezpośredniej pracy z protokołem REST plików (tj. wywołania HTTP REST handcrafting samodzielnie), ale najbardziej typowym sposobem korzystania z protokołu REST jest użycie modułu Azure PowerShell, [interfejsu wiersza polecenia platformy Azure](storage-how-to-use-files-cli.md)lub zestawu SDK usługi Azure Storage, z którego wszystkie zapewniają dodatkową otokę wokół protokołu REST plików w wybranym języku skryptów/programowania.  

W większości przypadków użyjesz udziału plików platformy Azure za pośrednictwem protokołu SMB, ponieważ umożliwi to skorzystanie ze znanych Ci istniejących aplikacji i narzędzi, ale istnieje kilka powodów, dla których użycie interfejsu API REST usługi Files wydaje się korzystniejsze niż użycie protokołu SMB:

- Udziały plików przeglądasz za pomocą programu PowerShell Cloud Shell (który nie może instalować udziałów plików za pośrednictwem protokołu SMB).
- Korzystasz z zasobów bezserwerowych, takich jak [usługa Azure Functions](../../azure-functions/functions-overview.md). 
- Tworzysz usługę wartości doprowadzającej, która będzie współdziałać z wieloma udziałami plików platformy Azure, takimi jak wykonywanie skanowania kopii zapasowej lub skanowanie antywirusowe.

W poniższych przykładach przedstawiono, jak używać modułu Azure PowerShell do obsługi udziałów plików platformy Azure przy użyciu protokołu REST usługi Files. Ten `-Context` parametr służy do pobierania klucza konta magazynu w celu wykonania wskazanych akcji względem udziału plików. Aby można było pobrać klucz konta magazynu, musisz mieć rolę platformy Azure dla `Owner` konta magazynu.

#### <a name="create-directory"></a>Tworzenie katalogu
Aby utworzyć nowy katalog o nazwie *myDirectory* w katalogu głównym udziału plików platformy Azure, użyj polecenia cmdlet [New-AzStorageDirectory](/powershell/module/az.storage/New-AzStorageDirectory).

```azurepowershell-interactive
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Path "myDirectory"
```

#### <a name="upload-a-file"></a>Przekazywanie pliku
Aby zademonstrować przekazywanie pliku przy użyciu polecenia cmdlet [Set-AzStorageFileContent](/powershell/module/az.storage/Set-AzStorageFileContent), najpierw należy utworzyć plik do przekazania w obrębie dysku tymczasowego usługi Cloud Shell w programie PowerShell. 

Ten przykład obejmuje umieszczenie bieżącej daty i godziny w nowym pliku na dysku tymczasowym, a następnie przekazanie tego pliku do udziału plików.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
cd "~/CloudDrive/"
Get-Date | Out-File -FilePath "SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Source "SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

Jeśli korzystasz z programu PowerShell lokalnie, zastąp ciąg `~/CloudDrive/` ścieżką, która istnieje na Twoim komputerze.

Po przekazaniu pliku możesz skorzystać z polecenia cmdlet [Get-AzStorageFile](/powershell/module/Az.Storage/Get-AzStorageFile), aby upewnić się, że plik został przekazany do udziału plików platformy Azure. 

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\" 
```

#### <a name="download-a-file"></a>Pobieranie pliku
Za pomocą polecenia cmdlet [Get-AzStorageFileContent](/powershell/module/az.storage/Get-AzStorageFilecontent) możesz pobrać kopię pliku, który właśnie został przekazany na dysk tymczasowy usługi Cloud Shell.

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item `
    -Path "SampleDownload.txt" `
    -Force `
    -ErrorAction SilentlyContinue

Get-AzStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt" `
    -Destination "SampleDownload.txt"
```

Po pobraniu pliku możesz sprawdzić, czy plik został pobrany na dysk tymczasowy programu PowerShell Cloud Shell, za pomocą polecenia `Get-ChildItem`.

```azurepowershell-interactive
Get-ChildItem | Where-Object { $_.Name -eq "SampleDownload.txt" }
``` 

#### <a name="copy-files"></a>Kopiowanie plików
Jednym z typowych zadań jest kopiowanie plików z jednego udziału plików do innego udziału plików. Aby zademonstrować tę funkcjonalność, możesz utworzyć nowy udział i skopiować właśnie przekazany plik do tego nowego udziału przy użyciu polecenia cmdlet [Start-AzStorageFileCopy](/powershell/module/az.storage/Start-AzStorageFileCopy). 

```azurepowershell-interactive
$otherShareName = "myshare2"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $otherShareName `
    -EnabledProtocol SMB `
    -QuotaGiB 1024 | Out-Null
  
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $otherShareName `
   -Path "myDirectory2"

Start-AzStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName $shareName `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName $otherShareName `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

Teraz, po wyświetleniu listy plików w nowym udziale, skopiowany plik powinien być widoczny.

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $otherShareName `
    -Path "myDirectory2" 
```

Chociaż `Start-AzStorageFileCopy` polecenie cmdlet jest wygodne w przypadku przenoszenia plików między udziałami plików platformy Azure, w przypadku migracji i większych przepływów danych zalecamy korzystanie `robocopy` z systemów Windows i `rsync` macOS oraz Linux. `robocopy` i `rsync` Użyj protokołu SMB, aby wykonać przesunięcia danych zamiast interfejsu API FileREST.

## <a name="create-and-manage-share-snapshots"></a>Tworzenie migawek udziałów i zarządzanie nimi
Jedną z dodatkowych przydatnych czynności, które można wykonywać na udziałach plików platformy Azure, jest tworzenie migawek udziałów. Migawka zachowuje określony moment w czasie dla udziału plików platformy Azure. Migawki udziałów są podobne do technologii systemów operacyjnych, które być może już znasz, takich jak:

- [Usługa kopiowania woluminów w tle (VSS)](/windows/desktop/VSS/volume-shadow-copy-service-portal) dla systemów plików systemu Windows, takich jak NTFS i ReFS.
- Migawki [Menedżera woluminów logicznych (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) dla systemów Linux.
- Migawki [Systemu plików firmy Apple (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) dla systemu macOS 

Migawkę dla udziału można utworzyć za pomocą metody `Snapshot` na obiekcie programu PowerShell dla udziału plików, który jest pobierany za pomocą polecenia cmdlet [Get-AzStorageShare](/powershell/module/az.storage/Get-AzStorageShare). 

```azurepowershell-interactive
$share = Get-AzStorageShare -Context $storageAcct.Context -Name $shareName
$snapshot = $share.CloudFileShare.Snapshot()
```

### <a name="browse-share-snapshots"></a>Przeglądanie migawek udziałów
Aby przeglądać zawartość migawki udziału, należy przekazać odwołanie do migawki (`$snapshot`) do parametru `-Share` polecenia cmdlet `Get-AzStorageFile`.

```azurepowershell-interactive
Get-AzStorageFile -Share $snapshot
```

### <a name="list-share-snapshots"></a>Wyświetlanie listy migawek udziałów
Za pomocą następującego polecenia można wyświetlić listę migawek wykonanych dla udziału.

```azurepowershell-interactive
Get-AzStorageShare `
        -Context $storageAcct.Context | `
    Where-Object { $_.Name -eq $shareName -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>Przywracanie na podstawie migawki udziału
Aby przywrócić plik, użyj polecenia `Start-AzStorageFileCopy`, którego używaliśmy wcześniej. Na potrzeby tego przewodnika Szybki start najpierw usuniemy nasz plik `SampleUpload.txt`, który wcześniej przekazaliśmy, aby można go było przywrócić na podstawie migawki.

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt"

# Restore SampleUpload.txt from the share snapshot
Start-AzStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName $shareName `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Usuwanie migawki udziału
Aby usunąć migawkę udziału, możesz użyć polecenia cmdlet [Remove-AzStorageShare](/powershell/module/az.storage/Remove-AzStorageShare) ze zmienną zawierającą odwołanie `$snapshot` do parametru `-Share`.

```azurepowershell-interactive
Remove-AzStorageShare `
    -Share $snapshot `
    -Confirm:$false `
    -Force
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Gdy skończysz, możesz usunąć grupę zasobów i wszystkie pokrewne zasoby za pomocą polecenia cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). 

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

Zasoby możesz również usuwać pojedynczo:

- Aby usunąć udziały plików platformy Azure utworzone na potrzeby tego przewodnika Szybki start.

    ```azurepowershell-interactive
    Get-AzRmStorageShare -StorageAccount $storageAcct | Remove-AzRmStorageShare -Force
    ```

    > [!Note]  
    > Musisz usunąć wszystkie migawki udziałów dla utworzonych udziałów plików platformy Azure przed usunięciem udziału plików platformy Azure.

- Aby usunąć konto magazynu (spowoduje to niejawne usunięcie utworzonych udziałów plików platformy Azure oraz wszelkich innych zasobów magazynu, które mogły zostać utworzone, takich jak kontener usługi Azure Blob Storage).

    ```azurepowershell-interactive
    Remove-AzStorageAccount `
        -ResourceGroupName $storageAcct.ResourceGroupName `
        -Name $storageAcct.StorageAccountName
    ```

## <a name="next-steps"></a>Następne kroki
> [!div class="nextstepaction"]
> [Co to jest usługa Azure Files?](storage-files-introduction.md)