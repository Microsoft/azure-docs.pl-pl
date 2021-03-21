---
title: 'Zarządzanie danymi za pomocą programu PowerShell: Azure Data Lake Storage Gen2'
description: Polecenia cmdlet programu PowerShell umożliwiają zarządzanie katalogami i plikami na kontach magazynu, które mają włączoną hierarchiczną przestrzeń nazw.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 552d53ff0257105ff61397e281504c5270512319
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103573867"
---
# <a name="use-powershell-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Zarządzanie katalogami i plikami w Azure Data Lake Storage Gen2 przy użyciu programu PowerShell

W tym artykule przedstawiono sposób użycia programu PowerShell do tworzenia katalogów i plików oraz zarządzania nimi na kontach magazynu, które mają hierarchiczną przestrzeń nazw.

Aby dowiedzieć się, jak pobierać, ustawiać i aktualizować listy kontroli dostępu (ACL) katalogów i plików, zobacz [Używanie programu PowerShell do zarządzania listami ACL w Azure Data Lake Storage Gen2](data-lake-storage-acl-powershell.md).

[Dokumentacja](/powershell/module/Az.Storage/)  |  Mapowanie Gen1 do [Gen2](#gen1-gen2-map)  |  [Przekaż opinię](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

- Konto magazynu, dla którego włączono hierarchiczną przestrzeń nazw. Postępuj zgodnie z [tymi](create-data-lake-storage-account.md) instrukcjami, aby je utworzyć.

- .NET Framework jest 4.7.2 lub większa. Zobacz [pobieranie .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).

- Program PowerShell w wersji `5.1` lub nowszej.

## <a name="install-the-powershell-module"></a>Zainstaluj moduł programu PowerShell

1. Sprawdź, czy zainstalowana wersja programu PowerShell jest `5.1` lub nowsza przy użyciu następującego polecenia.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```

   Aby uaktualnić wersję programu PowerShell, zobacz [uaktualnianie istniejącego środowiska Windows PowerShell](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell)

2. Zainstaluj **AZ. Storage** module.

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   Aby uzyskać więcej informacji na temat sposobu instalowania modułów programu PowerShell, zobacz [Instalowanie modułu Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="connect-to-the-account"></a>Połącz z kontem

Wybierz, w jaki sposób polecenia mają uzyskać autoryzację na konto magazynu. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-azure-ad"></a>Opcja 1: uzyskiwanie autoryzacji za pomocą Azure Active Directory (Azure AD)

W ramach tego podejścia system gwarantuje, że konto użytkownika ma odpowiednie przypisania kontroli dostępu opartej na rolach (Azure RBAC) i listy ACL.

1. Otwórz okno polecenia programu Windows PowerShell, a następnie zaloguj się do subskrypcji platformy Azure za pomocą `Connect-AzAccount` polecenia i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie.

   ```powershell
   Connect-AzAccount
   ```

2. Jeśli Twoja tożsamość jest skojarzona z więcej niż jedną subskrypcją, ustaw aktywną subskrypcję na konto magazynu, w którym chcesz utworzyć katalogi i zarządzać nimi. W tym przykładzie Zastąp `<subscription-id>` wartość symbolu zastępczego identyfikatorem subskrypcji.

   ```powershell
   Select-AzSubscription -SubscriptionId <subscription-id>
   ``` 

3. Pobierz kontekst konta magazynu.

   ```powershell
   $ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
   ```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Opcja 2: uzyskiwanie autoryzacji przy użyciu klucza konta magazynu

W tym podejściu system nie sprawdza uprawnień usługi Azure RBAC ani listy ACL. Pobierz kontekst konta magazynu przy użyciu klucza konta.

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -StorageAccountKey '<storage-account-key>'
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
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
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

<a id="gen1-gen2-map"></a>

## <a name="gen1-to-gen2-mapping"></a>Mapowanie Gen1 do Gen2

W poniższej tabeli przedstawiono sposób używania poleceń cmdlet do Data Lake Storage Gen1 mapowania poleceń cmdlet dla Data Lake Storage Gen2.

|Data Lake Storage Gen1 polecenie cmdlet| Data Lake Storage Gen2 polecenie cmdlet| Uwagi |
|--------|---------|-----|
|Get-AzDataLakeStoreChildItem|Get-AzDataLakeGen2ChildItem|Domyślnie polecenie cmdlet Get-AzDataLakeGen2ChildItem wyświetla tylko elementy podrzędne pierwszego poziomu. Parametr-rekursywnie wyświetla listę elementów podrzędnych rekursywnie. |
|Get-AzDataLakeStoreItem<br>Get-AzDataLakeStoreItemAclEntry<br>Get-AzDataLakeStoreItemOwner<br>Get-AzDataLakeStoreItemPermission|Get-AzDataLakeGen2Item|Elementy wyjściowe polecenia cmdlet Get-AzDataLakeGen2Item mają następujące właściwości: list ACL, właściciel, Grupa, uprawnienie.|
|Get-AzDataLakeStoreItemContent|Get-AzDataLakeGen2FileContent|Get-AzDataLakeGen2FileContent polecenie cmdlet pobiera zawartość pliku do pliku lokalnego.|
|Move-AzDataLakeStoreItem|Move-AzDataLakeGen2Item||
|New-AzDataLakeStoreItem|New-AzDataLakeGen2Item|To polecenie cmdlet przekazuje nową zawartość pliku z pliku lokalnego.|
|Remove-AzDataLakeStoreItem|Remove-AzDataLakeGen2Item||
|Set-AzDataLakeStoreItemOwner<br>Set-AzDataLakeStoreItemPermission<br>Set-AzDataLakeStoreItemAcl|Update-AzDataLakeGen2Item|Update-AzDataLakeGen2Item polecenie cmdlet aktualizuje tylko jeden element i nie rekursywnie. Jeśli chcesz zaktualizować cyklicznie, Wyświetl listę elementów za pomocą polecenia cmdlet Get-AzDataLakeStoreChildItem, a następnie potoku do Update-AzDataLakeGen2Item polecenia cmdlet.|
|Test-AzDataLakeStoreItem|Get-AzDataLakeGen2Item|Get-AzDataLakeGen2Item polecenie cmdlet zgłosi błąd, jeśli element nie istnieje.|

## <a name="see-also"></a>Zobacz też

- [Znane problemy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Polecenia cmdlet programu PowerShell usługi Storage](/powershell/module/az.storage)
