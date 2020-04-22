---
title: Program Azure Data Lake Storage Gen2 PowerShell dla plików & list ACL
description: Polecenia cmdlet programu PowerShell umożliwiają zarządzanie katalogami oraz listami kontroli dostępu do plików i katalogów (ACL) na kontach magazynu z włączoną hierarchiczną przestrzenią nazw (HNS).
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 68ffe40f93be3d10666ebad2eaa153fc9dc9687f
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768022"
---
# <a name="use-powershell-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Zarządzanie katalogami, plikami i listami ALs w usłudze Azure Data Lake Storage Gen2 za pomocą programu PowerShell

W tym artykule pokazano, jak używać programu PowerShell do tworzenia katalogów, plików i uprawnień oraz zarządzania nimi na kontach magazynu z włączoną hierarchiczną przestrzenią nazw (HNS). 

[Mapowanie](#gen1-gen2-map) | gen1 do gen2[Przekaż opinię](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Wymagania wstępne

> [!div class="checklist"]
> * Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Konto magazynu z włączoną hierarchiczną przestrzenią nazw (HNS). Postępuj zgodnie z [tymi](data-lake-storage-quickstart-create-account.md) instrukcjami, aby je utworzyć.
> * .NET Framework jest zainstalowany w 4.7.2 lub wyższym. Zobacz [Pobieranie programu .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
> * powershell `5.1` lub nowsza.

## <a name="install-the-powershell-module"></a>Instalowanie modułu programu PowerShell

1. Sprawdź, czy zainstalowana wersja programu `5.1` PowerShell jest lub wyższa za pomocą następującego polecenia.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Aby uaktualnić wersję programu PowerShell, zobacz [Uaktualnianie istniejącego programu Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
2. Zainstaluj moduł **Az.Storage.**

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   Aby uzyskać więcej informacji na temat instalowania modułów programu PowerShell, zobacz [Instalowanie modułu programu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)

## <a name="connect-to-the-account"></a>Łączenie się z kontem

Otwórz okno polecenia programu Windows PowerShell, a następnie `Connect-AzAccount` zaloguj się do subskrypcji platformy Azure za pomocą polecenia i postępuj zgodnie ze wskazówkami wyświetlanymi na ekranie.

```powershell
Connect-AzAccount
```

Jeśli twoja tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję na subskrypcję konta magazynu, w której chcesz utworzyć katalogi i w której chcesz zarządzać katalogami. W tym przykładzie `<subscription-id>` zastąp wartość symbolu zastępczego identyfikatorem subskrypcji.

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

Następnie wybierz sposób uzyskiwania autoryzacji do konta magazynu. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Opcja 1: Uzyskiwanie autoryzacji przy użyciu usługi Azure Active Directory (AD)

Dzięki takiemu podejściu system zapewnia, że konto użytkownika ma odpowiednie przypisania kontroli dostępu oparte na rolach (RBAC) i uprawnienia ACL. 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Opcja 2: Uzyskiwanie autoryzacji przy użyciu klucza konta magazynu

Dzięki takiemu podejściu system nie sprawdza uprawnień RBAC lub ACL.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

## <a name="create-a-file-system"></a>Tworzenie systemu plików

System plików działa jako kontener dla plików. Można go utworzyć za `New-AzDatalakeGen2FileSystem` pomocą polecenia cmdlet. 

W tym przykładzie `my-file-system`tworzy system plików o nazwie .

```powershell
$filesystemName = "my-file-system"
New-AzDatalakeGen2FileSystem -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Tworzenie katalogu

Utwórz odwołanie do katalogu `New-AzDataLakeGen2Item` przy użyciu polecenia cmdlet. 

W tym przykładzie `my-directory` dodaje katalog o nazwie do systemu plików.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

W tym przykładzie dodaje ten sam katalog, ale także ustawia uprawnienia, maskę, wartości właściwości i wartości metadanych. 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>Pokaż właściwości katalogu

W tym przykładzie pobiera `Get-AzDataLakeGen2Item` katalog przy użyciu polecenia cmdlet, a następnie drukuje wartości właściwości do konsoli.

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

## <a name="rename-or-move-a-directory"></a>Zmienianie nazwy lub przenoszenie katalogu

Zmień nazwę lub przenieś katalog `Move-AzDataLakeGen2Item` przy użyciu polecenia cmdlet.

W tym przykładzie zmienia nazwę `my-directory` katalogu `my-new-directory`z nazwy na nazwę .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

> [!NOTE]
> Użyj `-Force` parametru, jeśli chcesz zastąpić bez monitów.

W tym przykładzie `my-directory` przenosi katalog o `my-directory-2` nazwie `my-subdirectory`do podkatalogu o nazwie . 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2
```

## <a name="delete-a-directory"></a>Usuwanie katalogu

Usuń katalog za pomocą `Remove-AzDataLakeGen2Item` polecenia cmdlet.

W tym przykładzie usuwa `my-directory`katalog o nazwie . 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

Za pomocą `-Force` tego parametru można usunąć plik bez monitu.

## <a name="download-from-a-directory"></a>Pobieranie z katalogu

Pobierz plik z katalogu przy `Get-AzDataLakeGen2ItemContent` użyciu polecenia cmdlet.

W tym przykładzie `upload.txt` pobrano plik `my-directory`o nazwie z katalogu o nazwie . 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Wyświetlanie zawartości katalogu

Wyświetl listę zawartości katalogu przy `Get-AzDataLakeGen2ChildItem` użyciu polecenia cmdlet. Parametr opcjonalny służy `-OutputUserPrincipalName` do uzyskania nazwy (zamiast identyfikatora obiektu) użytkowników.

W tym przykładzie wymieniono `my-directory`zawartość katalogu o nazwie .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -OutputUserPrincipalName
```

Poniższy przykład `ACL`zawiera `Permissions` `Group`listę `Owner` , , i właściwości każdego elementu w katalogu. Parametr `-FetchProperty` jest wymagany do uzyskania `ACL` wartości dla właściwości. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchProperty
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

Aby wyświetlić listę zawartości systemu plików, należy pominąć `-Path` parametr z polecenia.

## <a name="upload-a-file-to-a-directory"></a>Przekazywanie pliku do katalogu

Przekaż plik do katalogu za `New-AzDataLakeGen2Item` pomocą polecenia cmdlet.

W tym przykładzie `upload.txt` przekazuje plik o `my-directory`nazwie do katalogu o nazwie . 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

W tym przykładzie przekazuje ten sam plik, ale następnie ustawia uprawnienia, maskę umask, wartości właściwości i wartości metadanych pliku docelowego. W tym przykładzie również drukuje te wartości do konsoli.

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.Properties
$file1.Properties.Metadata

```

## <a name="show-file-properties"></a>Pokaż właściwości pliku

W tym przykładzie pobiera `Get-AzDataLakeGen2Item` plik przy użyciu polecenia cmdlet, a następnie drukuje wartości właściwości do konsoli.

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

Usuń plik za `Remove-AzDataLakeGen2Item` pomocą polecenia cmdlet.

W tym przykładzie `upload.txt`usuwa plik o nazwie . 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

Za pomocą `-Force` tego parametru można usunąć plik bez monitu.

## <a name="manage-access-permissions"></a>Zarządzanie uprawnieniami dostępu

Możesz uzyskać, ustawić i zaktualizować uprawnienia dostępu do systemów plików, katalogów i plików. Te uprawnienia są przechwytywane na listach kontroli dostępu (Listy kontroli dostępu).

> [!NOTE]
> Jeśli do autoryzowania poleceń używasz usługi Azure Active Directory (Azure AD), upewnij się, że podmiot zabezpieczeń został przypisany [do roli Właściciel danych obiektu Blob magazynu.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Aby dowiedzieć się więcej o sposobie stosowania uprawnień listy ACL i skutkach ich zmiany, zobacz [Kontrola dostępu w usłudze Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

### <a name="get-an-acl"></a>Uzyskaj acl

Pobierz acl katalogu lub pliku za `Get-AzDataLakeGen2Item`pomocą polecenia cmdlet.

W tym przykładzie pobiera ACL **systemu plików,** a następnie drukuje ACL do konsoli.

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

W tym przykładzie pobiera ACL **katalogu**, a następnie drukuje ACL do konsoli.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

W tym przykładzie pobiera ACL **pliku,** a następnie drukuje ACL do konsoli.

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

Na poniższej ilustracji przedstawiono dane wyjściowe po uzyskaniu listy ACL katalogu.

![Uzyskaj wyjście ACL](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

W tym przykładzie użytkownik posiadający uprawnienia do odczytu, zapisu i wykonywania. Grupa właścicielka ma tylko uprawnienia do odczytu i wykonywania. Aby uzyskać więcej informacji na temat list kontroli dostępu, zobacz [Kontrola dostępu w usłudze Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-an-acl"></a>Ustawianie listy ACL

Polecenie `set-AzDataLakeGen2ItemAclObject` cmdlet służy do tworzenia listy ACL dla użytkownika posiadającego, grupy właścicielki lub innych użytkowników. Następnie użyj `Update-AzDataLakeGen2Item` polecenia cmdlet, aby zatwierdzić listy ACL.

W tym przykładzie ustawia ACL w **systemie plików** dla użytkownika posiadającego, grupy lub innych użytkowników, a następnie drukuje listy ACL do konsoli.

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Acl $acl
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

W tym przykładzie ustawia listy ACL w **katalogu** dla użytkownika posiadającego, grupy właścicielowej lub innych użytkowników, a następnie drukuje listy ACL do konsoli.

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
W tym przykładzie ustawia ACL w **pliku** dla użytkownika posiadającego, grupy lub innych użytkowników, a następnie drukuje listy ACL do konsoli.

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

![Uzyskaj wyjście ACL](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

W tym przykładzie użytkownik-właściciel i grupa posiadająca mają tylko uprawnienia do odczytu i zapisu. Wszyscy inni użytkownicy mają uprawnienia do zapisu i wykonywania. Aby uzyskać więcej informacji na temat list kontroli dostępu, zobacz [Kontrola dostępu w usłudze Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).


### <a name="set-acls-on-all-items-in-a-file-system"></a>Ustawianie list ACL dla wszystkich elementów w systemie plików

Można użyć `Get-AzDataLakeGen2Item` parametru `-Recurse` i parametru wraz z poleceniem `Update-AzDataLakeGen2Item` cmdlet, aby rekursywnie ustawić listy ACL wszystkich katalogów i plików w systemie plików. 

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Recurse | Update-AzDataLakeGen2Item -Acl $acl
```
### <a name="add-or-update-an-acl-entry"></a>Dodawanie lub aktualizowanie wpisu listy ACL

Po pierwsze, uzyskać ACL. Następnie użyj `set-AzDataLakeGen2ItemAclObject` polecenia cmdlet, aby dodać lub zaktualizować wpis listy ACL. Użyj `Update-AzDataLakeGen2Item` polecenia cmdlet, aby zatwierdzić listy ACL.

W tym przykładzie tworzy lub aktualizuje listy ACL w **katalogu** dla użytkownika.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -InputObject $acl 
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```

### <a name="remove-an-acl-entry"></a>Usuwanie wpisu listy ACL

W tym przykładzie usuwa wpis z istniejącej listy ACL.

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

<a id="gen1-gen2-map" />

## <a name="gen1-to-gen2-mapping"></a>Mapowanie gen1 do gen2

W poniższej tabeli przedstawiono, jak polecenia cmdlet używane do mapowania gen1 magazynu danych lake na polecenia cmdlet dla usługi Data Lake Storage Gen2.

|Polecenie cmdlet Data Lake Storage Gen1| Polecenie cmdlet Data Lake Storage Gen2| Uwagi |
|--------|---------|-----|
|Get-AzDataLakeStoreChildItem|Get-AzDataLakeGen2ChildItem|Domyślnie polecenie cmdlet Get-AzDataLakeGen2ChildItemitem zawiera tylko listę elementów podrzędnych pierwszego poziomu. Parametr -Recurse wyświetla elementy podrzędne rekursywnie. |
|Get-AzDataLakeStoreItem<br>Get-AzDataLakeStoreItemAclEntry<br>Właściciel get-azdatalakestoreitemowner<br>Get-AzDataLakeStoreItemPermission|Get-AzDataLakeGen2Item|Elementy wyjściowe polecenia cmdlet Get-AzDataLakeGen2Item ma następujące właściwości: Acl, Owner, Group, Permission.|
|Get-AzDataLakeStoreItemContent|Get-AzDataLakeGen2FileContent|Pobierz zawartość pliku cmdlet Get-AzDataLakeGen2FileContent do pliku lokalnego.|
|Move-AzDataLakeStoreItem|Move-AzDataLakeGen2Item||
|Nowy-AzDataLakeStoreItem|Nowy-AzDataLakeGen2Item|To polecenie cmdlet przekazuje nową zawartość pliku z pliku lokalnego.|
|Usuń-AzDataLakeStoreItem|Usuń-AzDataLakeGen2Item||
|Set-AzDataLakeStoreItemOwner<br>Set-AzDataLakeStoreItemPermission<br>Zestaw-AzDataLakeStoreItemAcl|Aktualizacja-AzDataLakeGen2Item|Polecenie cmdlet Update-AzDataLakeGen2Item aktualizuje tylko pojedynczy element, a nie cyklicznie. Jeśli chcesz zaktualizować cyklicznie, lista elementów przy użyciu polecenia cmdlet Get-AzDataLakeStoreChildItem, a następnie potok do polecenia cmdlet Update-AzDataLakeGen2Item.|
|Test-AzDataLakeStoreItem|Get-AzDataLakeGen2Item|Polecenie cmdlet Get-AzDataLakeGen2Item zgłosi błąd, jeśli element nie istnieje.|



## <a name="see-also"></a>Zobacz też

* [Znane problemy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Korzystanie z programu Azure PowerShell z usługą Azure Storage](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
* [Polecenia cmdlet programu PowerShell magazynu .](/powershell/module/az.storage)

