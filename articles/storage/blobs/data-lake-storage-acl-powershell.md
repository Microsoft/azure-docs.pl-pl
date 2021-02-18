---
title: Zarządzanie listami ACL w Azure Data Lake Storage Gen2 przy użyciu programu PowerShell
description: Polecenia cmdlet programu PowerShell umożliwiają zarządzanie listami kontroli dostępu (ACL) na kontach magazynu, które mają włączoną hierarchiczną przestrzeń nazw (SNS).
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 02/17/2021
ms.author: normesta
ms.reviewer: prishet
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b606e69baec8d159a6a3fa7373500176260ef0d7
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654351"
---
# <a name="use-powershell-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Zarządzanie listami ACL w Azure Data Lake Storage Gen2 przy użyciu programu PowerShell

W tym artykule pokazano, jak używać programu PowerShell do pobierania, ustawiania i aktualizowania list kontroli dostępu do katalogów i plików. 

Dziedziczenie listy ACL jest już dostępne dla nowych elementów podrzędnych, które są tworzone w katalogu nadrzędnym. Można również dodawać, aktualizować i usuwać listy ACL cyklicznie na istniejących elementach podrzędnych katalogu nadrzędnego bez konieczności wprowadzania tych zmian indywidualnie dla każdego elementu podrzędnego. 

[Dokumentacja](/powershell/module/Az.Storage/)  |  [Rekursywne przykłady](https://recursiveaclpr.blob.core.windows.net/privatedrop/samplePS.ps1?sv=2019-02-02&st=2020-08-24T17%3A04%3A44Z&se=2021-08-25T17%3A04%3A00Z&sr=b&sp=r&sig=dNNKS%2BZcp%2F1gl6yOx6QLZ6OpmXkN88ZjBeBtym1Mejo%3D)  |  listy ACL [Przekaż opinię](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

- Konto magazynu z włączoną hierarchiczną przestrzenią nazw (SNS). Postępuj zgodnie z [tymi](create-data-lake-storage-account.md) instrukcjami, aby je utworzyć.

- Interfejs wiersza polecenia platformy Azure w wersji `2.6.0` lub nowszej.

- Jedno z następujących uprawnień zabezpieczeń:

  - Przydzielona [podmiot zabezpieczeń](../../role-based-access-control/overview.md#security-principal) Azure Active Directory (AD), do którego przypisano rolę [właściciela danych obiektów blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) w zakresie kontenera docelowego, nadrzędnej grupy zasobów lub subskrypcji.  

  - Użytkownik będący właścicielem docelowego kontenera lub katalogu, do którego planujesz zastosować ustawienia listy ACL. Aby ustawić listy ACL cyklicznie, obejmuje to wszystkie elementy podrzędne w kontenerze lub katalogu docelowym.
  
  - Klucz konta magazynu.

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

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Opcja 1: uzyskiwanie autoryzacji za pomocą Azure Active Directory (AD)

> [!NOTE]
> Jeśli używasz usługi Azure Active Directory (Azure AD) do autoryzacji dostępu, upewnij się, że podmiot zabezpieczeń ma przypisaną [rolę właściciela danych obiektu blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Aby dowiedzieć się więcej na temat sposobu stosowania uprawnień ACL i skutków ich zmiany, zobacz  [model kontroli dostępu w Azure Data Lake Storage Gen2](./data-lake-storage-access-control-model.md).

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

## <a name="get-acls"></a>Pobierz listy ACL

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

## <a name="set-acls"></a>Ustawianie list ACL

Po *ustawieniu* listy ACL **zastąpi** całą listę kontroli dostępu, w tym wszystkie jej wpisy. Jeśli chcesz zmienić poziom uprawnień podmiotu zabezpieczeń lub dodać nowego podmiot zabezpieczeń do listy kontroli dostępu bez wpływu na inne istniejące wpisy, należy zamiast tego *zaktualizować* listę kontroli dostępu. Aby zaktualizować listę ACL zamiast zastępować, zobacz sekcję [listy ACL aktualizacji](#update-acls) w tym artykule.  

Jeśli zdecydujesz się *ustawić* listę kontroli dostępu, musisz dodać wpis dla użytkownika będącego właścicielem, wpis dla grupy będącej właścicielem i wpis dla wszystkich innych użytkowników. Aby dowiedzieć się więcej na temat użytkownika będącego właścicielem, grupy będącej właścicielem i wszystkich innych użytkowników, zobacz [Użytkownicy i tożsamości](data-lake-storage-access-control.md#users-and-identities).

W tej sekcji pokazano, jak:

- Ustawianie listy ACL
- Cykliczne ustawianie list kontroli dostępu

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

> [!NOTE]
> Jeśli chcesz ustawić **domyślny** wpis listy ACL, użyj parametru **-DefaultScope** podczas uruchamiania polecenia **Set-AzDataLakeGen2ItemAclObject** . Na przykład: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`.

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

> [!NOTE]
> Jeśli chcesz ustawić **domyślny** wpis listy ACL, użyj parametru **-DefaultScope** podczas uruchamiania polecenia **Set-AzDataLakeGen2ItemAclObject** . Na przykład: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`.

Na poniższej ilustracji przedstawiono dane wyjściowe po ustawieniu listy ACL pliku.

![Pobierz dane wyjściowe listy ACL dla pliku](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

W tym przykładzie użytkownik będący właścicielem i grupa będąca właścicielem mają tylko uprawnienia do odczytu i zapisu. Wszyscy inni użytkownicy mają uprawnienia do zapisu i wykonywania. Aby uzyskać więcej informacji na temat list kontroli dostępu, zobacz [Kontrola dostępu w Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-acls-recursively"></a>Cykliczne ustawianie list kontroli dostępu

Ustawianie list ACL cyklicznie przy użyciu polecenia cmdlet **Set-AzDataLakeGen2AclRecursive** .

Ten przykład ustawia listę ACL katalogu o nazwie `my-parent-directory` . Te wpisy dają użytkownikowi właściciela uprawnienia do odczytu, zapisu i wykonywania, dają członkom należącym do grupy uprawnienia Odczyt i wykonywanie, a inni nie mają dostępu. Ostatni wpis listy ACL w tym przykładzie daje określonemu użytkownikowi IDENTYFIKATORowi "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" uprawnień do odczytu i wykonywania.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission r-x -InputObject $acl 
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "---" -InputObject $acl
$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission r-x -InputObject $acl 

Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Jeśli chcesz ustawić **domyślny** wpis listy ACL, użyj parametru **-DefaultScope** podczas uruchamiania polecenia **Set-AzDataLakeGen2ItemAclObject** . Na przykład: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rwx -DefaultScope`.

Aby zobaczyć przykład, który ustawia listy ACL rekursywnie w partiach przez określenie rozmiaru partii, zobacz artykuł dotyczący [zestawu AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) .

## <a name="update-acls"></a>Aktualizowanie list ACL

Podczas *aktualizowania* listy kontroli dostępu należy zmodyfikować listę ACL zamiast zastępować listę kontroli dostępu. Na przykład można dodać nowego podmiot zabezpieczeń do listy kontroli dostępu bez wpływu na inne podmioty zabezpieczeń wymienione na liście ACL.  Aby zastąpić listę ACL zamiast aktualizacji, zobacz sekcję [Ustawianie list ACL](#set-acls) w tym artykule.

Aby zaktualizować listę kontroli dostępu, Utwórz nowy obiekt listy ACL z wpisem listy ACL, który chcesz zaktualizować, a następnie użyj tego obiektu w operacji aktualizowania listy ACL. Nie pobieraj istniejącej listy kontroli dostępu, po prostu podaj wpisy listy ACL do zaktualizowania.

W tej sekcji pokazano, jak:

- Aktualizowanie listy ACL
- Aktualizuje rekursywnie listy ACL

### <a name="update-an-acl"></a>Aktualizowanie listy ACL

Najpierw Pobierz listę kontroli dostępu. Następnie użyj `set-AzDataLakeGen2ItemAclObject` polecenia cmdlet, aby dodać lub zaktualizować wpis listy ACL. Użyj `Update-AzDataLakeGen2Item` polecenia cmdlet, aby zatwierdzić listę kontroli dostępu.

Ten przykład umożliwia utworzenie lub zaktualizowanie listy ACL w **katalogu** dla użytkownika.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -InputObject $acl 
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```

> [!NOTE]
> Jeśli chcesz zaktualizować **domyślny** wpis listy ACL, użyj parametru **-DefaultScope** podczas uruchamiania polecenia **Set-AzDataLakeGen2ItemAclObject** . Na przykład: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -DefaultScope`.

### <a name="update-acls-recursively"></a>Aktualizuje rekursywnie listy ACL

Aktualizowanie list ACL rekursywnie za pomocą polecenia cmdlet  **Update-AzDataLakeGen2AclRecursive** .

Ten przykład aktualizuje wpis listy ACL z uprawnieniami do zapisu.

```powershell
$filesystemName = "my-container"
$dirname = "my-parent-directory/"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx";

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx

Update-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl

```

> [!NOTE]
> Jeśli chcesz zaktualizować **domyślny** wpis listy ACL, użyj parametru **-DefaultScope** podczas uruchamiania polecenia **Set-AzDataLakeGen2ItemAclObject** . Na przykład: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission rwx -DefaultScope`.

Aby zobaczyć przykład, który aktualizuje listy ACL rekursywnie w partiach przez określenie rozmiaru partii, zobacz artykuł dotyczący [aktualizacji AzDataLakeGen2AclRecursive](/powershell/module/az.storage/update-azdatalakegen2aclrecursive) .

## <a name="remove-acl-entries"></a>Usuń wpisy listy ACL

W tej sekcji pokazano, jak:

- Usuwanie wpisu listy ACL
- Usuń rekursywnie wpisów listy ACL

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

### <a name="remove-acl-entries-recursively"></a>Usuń rekursywnie wpisów listy ACL

Co najmniej jeden wpis listy ACL można usunąć cyklicznie. Aby usunąć wpis listy ACL, Utwórz nowy obiekt ACL dla wpisu listy ACL, który ma zostać usunięty, a następnie użyj tego obiektu w operacji usuwania listy ACL. Nie pobieraj istniejącej listy kontroli dostępu, po prostu podaj wpisy listy ACL do usunięcia.

Usuń wpisy listy ACL za pomocą polecenia cmdlet **Remove-AzDataLakeGen2AclRecursive** .

Ten przykład usuwa wpis listy ACL z katalogu głównego kontenera.  

```powershell
$filesystemName = "my-container"
$userID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

$acl = Set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" 

Remove-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName  -Acl $acl
```

> [!NOTE]
> Jeśli chcesz usunąć **domyślny** wpis listy ACL, użyj parametru **-DefaultScope** podczas uruchamiania polecenia **Set-AzDataLakeGen2ItemAclObject** . Na przykład: `$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $userID -Permission "---" -DefaultScope`.

Aby zobaczyć przykład, który usuwa listy ACL cyklicznie w partiach przez określenie rozmiaru partii, zobacz artykuł dotyczący [usuwania-AzDataLakeGen2AclRecursive](/powershell/module/az.storage/remove-azdatalakegen2aclrecursive) .

## <a name="recover-from-failures"></a>Odzyskiwanie po awarii

Podczas cyklicznego modyfikowania list ACL mogą wystąpić błędy środowiska uruchomieniowego lub uprawnień. 

W przypadku błędów środowiska uruchomieniowego należy ponownie uruchomić proces od początku. Błędy uprawnień mogą wystąpić, jeśli podmiot zabezpieczeń nie ma wystarczających uprawnień do modyfikowania listy ACL katalogu lub pliku znajdującego się w modyfikowanej hierarchii katalogów. Rozwiąż problem z uprawnieniami, a następnie wybierz wznowienie procesu od punktu awarii przy użyciu tokenu kontynuacji lub ponownie uruchom proces od początku. Nie musisz używać tokenu kontynuacji, jeśli wolisz uruchomić ponownie od początku. Możesz ponownie zastosować wpisy ACL bez żadnego negatywnego wpływu.

Ten przykład zwraca wyniki do zmiennej, a następnie potoki nie mogą wejść do sformatowanej tabeli.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$result
$result.FailedEntries | ft 
```

Na podstawie danych wyjściowych tabeli można naprawić wszelkie błędy uprawnień, a następnie wznowić wykonywanie przy użyciu tokenu kontynuacji.

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinuationToken $result.ContinuationToken
$result

```

Aby zobaczyć przykład, który ustawia listy ACL rekursywnie w partiach przez określenie rozmiaru partii, zobacz artykuł dotyczący [zestawu AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) .

Jeśli chcesz, aby proces został zakończony nieprzerwanie przez błędy uprawnień, możesz określić, czy.

Ten przykład używa `ContinueOnFailure` parametru, aby kontynuować wykonywanie nawet wtedy, gdy operacja napotka błąd uprawnień. 

```powershell
$result = Set-AzDataLakeGen2AclRecursive -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl -ContinueOnFailure

echo "[Result Summary]"
echo "TotalDirectoriesSuccessfulCount: `t$($result.TotalFilesSuccessfulCount)"
echo "TotalFilesSuccessfulCount: `t`t`t$($result.TotalDirectoriesSuccessfulCount)"
echo "TotalFailureCount: `t`t`t`t`t$($result.TotalFailureCount)"
echo "FailedEntries:"$($result.FailedEntries | ft) 
```

Aby zobaczyć przykład, który ustawia listy ACL rekursywnie w partiach przez określenie rozmiaru partii, zobacz artykuł dotyczący [zestawu AzDataLakeGen2AclRecursive](/powershell/module/az.storage/set-azdatalakegen2aclrecursive) .

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Zobacz też

- [Znane problemy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Polecenia cmdlet programu PowerShell usługi Storage](/powershell/module/az.storage)
- [Model kontroli dostępu w Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Listy kontroli dostępu (ACL) w Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
