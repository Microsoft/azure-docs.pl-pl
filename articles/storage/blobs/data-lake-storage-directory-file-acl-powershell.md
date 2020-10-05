---
title: Azure Data Lake Storage Gen2 PowerShell dla plików & listy ACL
description: Polecenia cmdlet programu PowerShell umożliwiają zarządzanie katalogami oraz listami kontroli dostępu do plików i katalogów (ACL) na kontach magazynu, które mają włączoną hierarchiczną przestrzeń nazw (SNS).
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 08/26/2020
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 62a6bb807f01fd19a92c3dc4edf797171dd5ebc9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91713399"
---
# <a name="use-powershell-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Użyj programu PowerShell do zarządzania katalogami, plikami i listami ACL w Azure Data Lake Storage Gen2

W tym artykule przedstawiono sposób użycia programu PowerShell do tworzenia katalogów, plików i uprawnień w ramach kont magazynu, które mają włączoną hierarchiczną przestrzeń nazw (SNS), oraz do zarządzania nimi. 

[Dokumentacja](https://docs.microsoft.com/powershell/module/Az.Storage/?view=azps-4.5.0)  |  Mapowanie Gen1 do [Gen2](#gen1-gen2-map)  |  [Przekaż opinię](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Wymagania wstępne

> [!div class="checklist"]
> * Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Konto magazynu z włączoną hierarchiczną przestrzenią nazw (SNS). Postępuj zgodnie z [tymi](data-lake-storage-quickstart-create-account.md) instrukcjami, aby je utworzyć.
> * .NET Framework jest 4.7.2 lub większa. Zobacz [pobieranie .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
> * Program PowerShell w wersji `5.1` lub nowszej.

## <a name="install-the-powershell-module"></a>Instalowanie modułu programu PowerShell

1. Sprawdź, czy zainstalowana wersja programu PowerShell jest `5.1` lub nowsza przy użyciu następującego polecenia.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Aby uaktualnić wersję programu PowerShell, zobacz [uaktualnianie istniejącego środowiska Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
2. Zainstaluj **AZ. Storage** module.

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   Aby uzyskać więcej informacji na temat sposobu instalowania modułów programu PowerShell, zobacz [Instalowanie modułu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)

## <a name="connect-to-the-account"></a>Połącz z kontem

Otwórz okno polecenia programu Windows PowerShell, a następnie zaloguj się do subskrypcji platformy Azure za pomocą `Connect-AzAccount` polecenia i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

```powershell
Connect-AzAccount
```

Jeśli Twoja tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję na konto magazynu, w którym chcesz utworzyć katalogi i zarządzać nimi. W tym przykładzie Zastąp `<subscription-id>` wartość symbolu zastępczego identyfikatorem subskrypcji.

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

Następnie wybierz, w jaki sposób polecenia mają uzyskać autoryzację na konto magazynu. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Opcja 1: uzyskiwanie autoryzacji za pomocą Azure Active Directory (AD)

W ramach tego podejścia system gwarantuje, że konto użytkownika ma odpowiednie przypisania kontroli dostępu opartej na rolach (Azure RBAC) i listy ACL. 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Opcja 2: uzyskiwanie autoryzacji przy użyciu klucza konta magazynu

W tym podejściu system nie sprawdza uprawnień usługi Azure RBAC ani listy ACL.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

## <a name="create-a-container"></a>Tworzenie kontenera

Kontener działa jako system plików dla plików. Można go utworzyć przy użyciu `New-AzStorageContainer` polecenia cmdlet. 

Ten przykład tworzy kontener o nazwie `my-file-system` .

```powershell
$filesystemName = "my-file-system"
New-AzStorageContainer -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Tworzenie katalogu

Utwórz odwołanie do katalogu przy użyciu `New-AzDataLakeGen2Item` polecenia cmdlet. 

Ten przykład dodaje katalog o nazwie `my-directory` do kontenera.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

Ten przykład dodaje ten sam katalog, ale również ustawia uprawnienia, maska umask, wartości właściwości i wartości metadanych. 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>Pokaż właściwości katalogu

Ten przykład pobiera katalog przy użyciu `Get-AzDataLakeGen2Item` polecenia cmdlet, a następnie drukuje wartości właściwości w konsoli programu.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Group
$dir.Owner
$dir.Properties
$dir.Properties.Metadata
```
> [!NOTE]
> Aby uzyskać katalog główny kontenera, Pomiń `-Path` parametr.

## <a name="rename-or-move-a-directory"></a>Zmienianie nazwy lub przenoszenie katalogu

Zmień nazwę lub Przenieś katalog przy użyciu `Move-AzDataLakeGen2Item` polecenia cmdlet.

Ten przykład zmienia nazwę katalogu z nazwy `my-directory` na nazwę `my-new-directory` .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

> [!NOTE]
> Użyj `-Force` parametru, jeśli chcesz zastąpić bez wyświetlania wierszy.

Ten przykład przenosi katalog o nazwie `my-directory` do podkatalogu o `my-directory-2` nazwie `my-subdirectory` . 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2
```

## <a name="delete-a-directory"></a>Usuwanie katalogu

Usuń katalog przy użyciu `Remove-AzDataLakeGen2Item` polecenia cmdlet.

Ten przykład usuwa katalog o nazwie `my-directory` . 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

Możesz użyć parametru, `-Force` Aby usunąć plik bez monitu.

## <a name="download-from-a-directory"></a>Pobierz z katalogu

Pobierz plik z katalogu przy użyciu `Get-AzDataLakeGen2ItemContent` polecenia cmdlet.

Ten przykład pobiera plik o nazwie `upload.txt` z katalogu o nazwie `my-directory` . 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Wyświetlanie zawartości katalogu

Wyświetl listę zawartości katalogu przy użyciu `Get-AzDataLakeGen2ChildItem` polecenia cmdlet. Można użyć opcjonalnego parametru, `-OutputUserPrincipalName` Aby uzyskać nazwę (zamiast identyfikatora obiektu) użytkowników.

W tym przykładzie wymieniono zawartość katalogu o nazwie `my-directory` .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -OutputUserPrincipalName
```

Poniższy przykład zawiera listę `ACL` właściwości, `Permissions` , `Group` i `Owner` każdego elementu w katalogu. `-FetchProperty`Parametr jest wymagany do pobrania wartości `ACL` właściwości. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchProperty
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

> [!NOTE]
> Aby wyświetlić listę zawartości katalogu głównego kontenera, Pomiń `-Path` parametr.

## <a name="upload-a-file-to-a-directory"></a>Przekaż plik do katalogu

Przekaż plik do katalogu przy użyciu `New-AzDataLakeGen2Item` polecenia cmdlet.

Ten przykład przekazuje plik o nazwie `upload.txt` do katalogu o nazwie `my-directory` . 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

Ten przykład przekazuje ten sam plik, ale następnie ustawia uprawnienia, maska umask, wartości właściwości i wartości metadanych pliku docelowego. Ten przykład również drukuje te wartości w konsoli programu.

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.Properties
$file1.Properties.Metadata

```

> [!NOTE]
> Aby przekazać plik do katalogu głównego kontenera, Pomiń `-Path` parametr.

## <a name="show-file-properties"></a>Pokaż właściwości pliku

Ten przykład pobiera plik za pomocą `Get-AzDataLakeGen2Item` polecenia cmdlet, a następnie drukuje wartości właściwości w konsoli programu.

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.Group
$file.Owner
$file.Properties
$file.Properties.Metadata
```

## <a name="delete-a-file"></a>Usuwanie pliku

Usuń plik przy użyciu `Remove-AzDataLakeGen2Item` polecenia cmdlet.

Ten przykład usuwa plik o nazwie `upload.txt` . 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

Możesz użyć parametru, `-Force` Aby usunąć plik bez monitu.

## <a name="manage-access-permissions"></a>Zarządzanie uprawnieniami dostępu

Uprawnienia dostępu do katalogów i plików można uzyskiwać, ustawiać i aktualizować. Te uprawnienia są przechwytywane na listach kontroli dostępu (ACL).

> [!NOTE]
> Jeśli używasz Azure Active Directory (Azure AD) do autoryzacji poleceń, upewnij się, że podmiot zabezpieczeń ma przypisaną [rolę właściciela danych obiektu blob magazynu](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Aby dowiedzieć się więcej na temat sposobu stosowania uprawnień ACL i skutków ich zmiany, zobacz  [Kontrola dostępu w Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

### <a name="get-an-acl"></a>Pobieranie listy ACL

Pobierz listę kontroli dostępu do katalogu lub pliku przy użyciu `Get-AzDataLakeGen2Item` polecenia cmdlet.

Ten przykład pobiera listę ACL katalogu głównego **kontenera** , a następnie drukuje listę kontroli dostępu do konsoli programu.

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

Ten przykład pobiera listę ACL **katalogu**, a następnie drukuje listę kontroli dostępu do konsoli programu.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

Ten przykład pobiera listę ACL **pliku** , a następnie drukuje listę kontroli dostępu do konsoli programu.

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

Na poniższej ilustracji przedstawiono dane wyjściowe po uzyskaniu listy ACL katalogu.

![Pobierz dane wyjściowe listy ACL dla katalogu](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

W tym przykładzie użytkownik będący właścicielem ma uprawnienia do odczytu, zapisu i wykonania. Grupa będąca właścicielem ma tylko uprawnienia do odczytu i wykonywania. Aby uzyskać więcej informacji na temat list kontroli dostępu, zobacz [Kontrola dostępu w Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-an-acl"></a>Ustawianie listy ACL

Użyj `set-AzDataLakeGen2ItemAclObject` polecenia cmdlet, aby utworzyć listę ACL dla użytkownika będącego właścicielem, grupy będącej właścicielem lub innych użytkowników. Następnie użyj `Update-AzDataLakeGen2Item` polecenia cmdlet, aby zatwierdzić listę kontroli dostępu.

Ten przykład ustawia listę ACL w katalogu głównym **kontenera** dla użytkownika będącego właścicielem, grupy będącej właścicielem lub innych użytkowników, a następnie drukuje listę kontroli dostępu do konsoli programu.

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Acl $acl
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

Ten przykład ustawia listę ACL dla **katalogu** dla użytkownika będącego właścicielem, grupy będącej właścicielem lub innych użytkowników, a następnie drukuje listę kontroli dostępu do konsoli programu.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```
Ten przykład ustawia listę ACL dla **pliku** dla użytkownika będącego właścicielem, grupy będącej właścicielem lub innych użytkowników, a następnie drukuje listę kontroli dostępu do konsoli programu.

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

Na poniższej ilustracji przedstawiono dane wyjściowe po ustawieniu listy ACL pliku.

![Pobierz dane wyjściowe listy ACL dla pliku](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

W tym przykładzie użytkownik będący właścicielem i grupa będąca właścicielem mają tylko uprawnienia do odczytu i zapisu. Wszyscy inni użytkownicy mają uprawnienia do zapisu i wykonywania. Aby uzyskać więcej informacji na temat list kontroli dostępu, zobacz [Kontrola dostępu w Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="add-or-update-an-acl-entry"></a>Dodawanie lub aktualizowanie wpisu listy ACL

Najpierw Pobierz listę kontroli dostępu. Następnie użyj `set-AzDataLakeGen2ItemAclObject` polecenia cmdlet, aby dodać lub zaktualizować wpis listy ACL. Użyj `Update-AzDataLakeGen2Item` polecenia cmdlet, aby zatwierdzić listę kontroli dostępu.

Ten przykład umożliwia utworzenie lub zaktualizowanie listy ACL w **katalogu** dla użytkownika.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -InputObject $acl 
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```

### <a name="remove-an-acl-entry"></a>Usuwanie wpisu listy ACL

Ten przykład usuwa wpis z istniejącej listy ACL.

```powershell
$id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

# Create the new ACL object.
[Collections.Generic.List[System.Object]]$aclnew =$acl

foreach ($a in $aclnew)
{
    if ($a.AccessControlType -eq "User"-and $a.DefaultScope -eq $false -and $a.EntityId -eq $id)
    {
        $aclnew.Remove($a);
        break;
    }
}
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $aclnew
```

### <a name="set-an-acl-recursively-preview"></a>Ustawianie listy ACL rekursywnie (wersja zapoznawcza)

Można dodawać, aktualizować i usuwać listy ACL cyklicznie na istniejących elementach podrzędnych katalogu nadrzędnego bez konieczności wprowadzania tych zmian indywidualnie dla każdego elementu podrzędnego. Więcej informacji można znaleźć w sekcji [Ustawianie list kontroli dostępu (ACL) rekursywnie dla Azure Data Lake Storage Gen2](recursive-access-control-lists.md).

<a id="gen1-gen2-map"></a>

## <a name="gen1-to-gen2-mapping"></a>Mapowanie Gen1 do Gen2

W poniższej tabeli przedstawiono sposób używania poleceń cmdlet do Data Lake Storage Gen1 mapowania poleceń cmdlet dla Data Lake Storage Gen2.

|Data Lake Storage Gen1 polecenie cmdlet| Data Lake Storage Gen2 polecenie cmdlet| Uwagi |
|--------|---------|-----|
|Get-AzDataLakeStoreChildItem|Get-AzDataLakeGen2ChildItem|Domyślnie polecenie cmdlet Get-AzDataLakeGen2ChildItem wyświetla tylko elementy podrzędne pierwszego poziomu. Parametr-rekursywnie wyświetla listę elementów podrzędnych rekursywnie. |
|Get-AzDataLakeStoreItem<br>Get-AzDataLakeStoreItemAclEntry<br>Get-AzDataLakeStoreItemOwner<br>Get-AzDataLakeStoreItemPermission|Get-AzDataLakeGen2Item|Elementy wyjściowe polecenia cmdlet Get-AzDataLakeGen2Item mają następujące właściwości: list ACL, właściciel, Grupa, uprawnienie.|
|Get-AzDataLakeStoreItemContent|Get-AzDataLakeGen2FileContent|Polecenie cmdlet Get-AzDataLakeGen2FileContent pobiera zawartość pliku do pliku lokalnego.|
|Move-AzDataLakeStoreItem|Move-AzDataLakeGen2Item||
|New-AzDataLakeStoreItem|New-AzDataLakeGen2Item|To polecenie cmdlet przekazuje nową zawartość pliku z pliku lokalnego.|
|Remove-AzDataLakeStoreItem|Remove-AzDataLakeGen2Item||
|Set-AzDataLakeStoreItemOwner<br>Set-AzDataLakeStoreItemPermission<br>Set-AzDataLakeStoreItemAcl|Update-AzDataLakeGen2Item|Polecenie cmdlet Update-AzDataLakeGen2Item aktualizuje tylko jeden element i nie rekursywnie. Jeśli chcesz zaktualizować cyklicznie, Wyświetl listę elementów za pomocą polecenia cmdlet Get-AzDataLakeStoreChildItem, a następnie potoku do polecenia cmdlet Update-AzDataLakeGen2Item.|
|Test-AzDataLakeStoreItem|Get-AzDataLakeGen2Item|Polecenie cmdlet Get-AzDataLakeGen2Item zgłosi błąd, jeśli element nie istnieje.|

## <a name="see-also"></a>Zobacz też

* [Znane problemy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Polecenia cmdlet programu PowerShell usługi Storage](/powershell/module/az.storage)
