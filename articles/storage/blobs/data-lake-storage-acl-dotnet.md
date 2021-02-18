---
title: Korzystanie z platformy .NET do ustawiania list ACL w Azure Data Lake Storage Gen2
description: Użyj programu .NET do zarządzania listami kontroli dostępu (ACL) na kontach magazynu, które mają włączoną hierarchiczną przestrzeń nazw (SNS).
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-csharp
ms.openlocfilehash: 626e89d8d758d5fe31ef6c913076a9154274bb61
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654231"
---
# <a name="use-net-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Zarządzanie listami ACL w Azure Data Lake Storage Gen2 przy użyciu platformy .NET

W tym artykule pokazano, jak używać programu .NET do pobierania, ustawiania i aktualizowania list kontroli dostępu do katalogów i plików.

Dziedziczenie listy ACL jest już dostępne dla nowych elementów podrzędnych, które są tworzone w katalogu nadrzędnym. Można również dodawać, aktualizować i usuwać listy ACL cyklicznie na istniejących elementach podrzędnych katalogu nadrzędnego bez konieczności wprowadzania tych zmian indywidualnie dla każdego elementu podrzędnego.

[Pakiet (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)  |  [Przykłady](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)  |  Przykład cyklicznej [listy ACL](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)  |  [Dokumentacja](/dotnet/api/azure.storage.files.datalake)  |  interfejsu API Mapowanie Gen1 do [Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)  |  [Przekaż opinię](https://github.com/Azure/azure-sdk-for-net/issues)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

- Konto magazynu z włączoną hierarchiczną przestrzenią nazw (SNS). Postępuj zgodnie z [tymi](create-data-lake-storage-account.md) instrukcjami, aby je utworzyć.

- Interfejs wiersza polecenia platformy Azure w wersji `2.6.0` lub nowszej.

- Jedno z następujących uprawnień zabezpieczeń:

  - Przydzielona [podmiot zabezpieczeń](../../role-based-access-control/overview.md#security-principal) Azure Active Directory (AD), do którego przypisano rolę [właściciela danych obiektów blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) w zakresie kontenera docelowego, nadrzędnej grupy zasobów lub subskrypcji.  

  - Użytkownik będący właścicielem docelowego kontenera lub katalogu, do którego planujesz zastosować ustawienia listy ACL. Aby ustawić listy ACL cyklicznie, obejmuje to wszystkie elementy podrzędne w kontenerze lub katalogu docelowym.
  
  - Klucz konta magazynu.

## <a name="set-up-your-project"></a>konfigurowanie projektu

Aby rozpocząć, zainstaluj pakiet NuGet [Azure. Storage. Files. datalake](https://www.nuget.org/packages/Azure.Storage.Files.DataLake/) .

1. Otwórz okno polecenia (na przykład: Windows PowerShell).

2. W katalogu projektu Zainstaluj pakiet Azure. Storage. Files. datalake Preview za pomocą `dotnet add package` polecenia.

   ```console
   dotnet add package Azure.Storage.Files.DataLake -v 12.6.0 -s https://pkgs.dev.azure.com/azure-sdk/public/_packaging/azure-sdk-for-net/nuget/v3/index.json
   ```

   Następnie Dodaj te instrukcje using na początku pliku kodu.

   ```csharp
   using Azure;
   using Azure.Core;
   using Azure.Storage;
   using Azure.Storage.Files.DataLake;
   using Azure.Storage.Files.DataLake.Models;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   ```

## <a name="connect-to-the-account"></a>Połącz z kontem

Aby użyć fragmentów kodu w tym artykule, należy utworzyć wystąpienie [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) reprezentujące konto magazynu. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Łączenie przy użyciu Azure Active Directory (AD)

> [!NOTE]
> Jeśli używasz usługi Azure Active Directory (Azure AD) do autoryzacji dostępu, upewnij się, że podmiot zabezpieczeń ma przypisaną [rolę właściciela danych obiektu blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Aby dowiedzieć się więcej na temat sposobu stosowania uprawnień ACL i skutków ich zmiany, zobacz  [model kontroli dostępu w Azure Data Lake Storage Gen2](./data-lake-storage-access-control-model.md).

Za pomocą [biblioteki klienta tożsamości platformy Azure dla platformy .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) można uwierzytelniać aplikację w usłudze Azure AD.

Po zainstalowaniu pakietu Dodaj tę instrukcję using na początku pliku kodu.

```csharp
using Azure.Identity;
```

Pobierz identyfikator klienta, klucz tajny klienta i identyfikator dzierżawy. Aby to zrobić, zobacz [pozyskiwanie tokenu z usługi Azure AD w celu autoryzowania żądań z aplikacji klienckiej](../common/storage-auth-aad-app.md). W ramach tego procesu należy przypisać jedną z następujących ról [kontroli dostępu opartej na rolach (Azure RBAC)](../../role-based-access-control/overview.md) do podmiotu zabezpieczeń. 

|Rola|Możliwość ustawienia listy ACL|
|--|--|
|[Właściciel danych obiektu blob usługi Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Wszystkie katalogi i pliki na koncie.|
|[Współautor danych obiektu blob usługi Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Tylko katalogi i pliki należące do podmiotu zabezpieczeń.|

Ten przykład tworzy wystąpienie [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) przy użyciu identyfikatora klienta, klucza tajnego klienta i identyfikatora dzierżawy.  

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Aby uzyskać więcej przykładów, zobacz dokumentację [biblioteki klienta tożsamości platformy Azure dla programu .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity) .

### <a name="connect-by-using-an-account-key"></a>Nawiązywanie połączenia przy użyciu klucza konta

Jest to najprostszy sposób nawiązywania połączenia z kontem.

Ten przykład tworzy wystąpienie [DataLakeServiceClient](/dotnet/api/azure.storage.files.datalake.datalakeserviceclient) przy użyciu klucza konta.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Authorize_DataLake.cs" id="Snippet_AuthorizeWithKey":::

## <a name="set-acls"></a>Ustawianie list ACL

Po *ustawieniu* listy ACL **zastąpi** całą listę kontroli dostępu, w tym wszystkie jej wpisy. Jeśli chcesz zmienić poziom uprawnień podmiotu zabezpieczeń lub dodać nowego podmiot zabezpieczeń do listy kontroli dostępu bez wpływu na inne istniejące wpisy, należy zamiast tego *zaktualizować* listę kontroli dostępu. Aby zaktualizować listę ACL zamiast zastępować, zobacz sekcję [listy ACL aktualizacji](#update-acls) w tym artykule.  

Jeśli zdecydujesz się *ustawić* listę kontroli dostępu, musisz dodać wpis dla użytkownika będącego właścicielem, wpis dla grupy będącej właścicielem i wpis dla wszystkich innych użytkowników. Aby dowiedzieć się więcej na temat użytkownika będącego właścicielem, grupy będącej właścicielem i wszystkich innych użytkowników, zobacz [Użytkownicy i tożsamości](data-lake-storage-access-control.md#users-and-identities).

W tej sekcji pokazano, jak:

- Ustawianie listy ACL katalogu
- Ustawianie listy ACL pliku
- Cykliczne ustawianie list kontroli dostępu

### <a name="set-the-acl-of-a-directory"></a>Ustawianie listy ACL katalogu

Pobierz listę kontroli dostępu (ACL) katalogu, wywołując metodę [DataLakeDirectoryClient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) i ustawiając listę ACL przez wywołanie metody [DataLakeDirectoryClient. SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) .

Ten przykład pobiera i ustawia listę ACL katalogu o nazwie `my-directory` . Ciąg `user::rwx,group::r-x,other::rw-` przyznaje uprawnienia Odczyt, zapis i wykonywanie przez użytkownika, który ma grupę będącą właścicielem, ma uprawnienia tylko do odczytu i wykonania, i daje wszystkim innym uprawnienie do odczytu i zapisu.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ACLDirectory":::

Możesz również uzyskać i ustawić listę kontroli dostępu dla katalogu głównego kontenera. Aby uzyskać katalog główny, należy przekazać pusty ciąg ( `""` ) do metody [DataLakeFileSystemClient. GetDirectoryClient](/dotnet/api/azure.storage.files.datalake.datalakefilesystemclient.getdirectoryclient) .

### <a name="set-the-acl-of-a-file"></a>Ustawianie listy ACL pliku

Pobieranie listy kontroli dostępu (ACL) pliku przez wywołanie metody [DataLakeFileClient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakefileclient.getaccesscontrolasync) i ustawienie listy ACL przez wywołanie metody [DataLakeFileClient. SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakefileclient.setaccesscontrollist) .

Ten przykład pobiera i ustawia listę ACL pliku o nazwie `my-file.txt` . Ciąg `user::rwx,group::r-x,other::rw-` przyznaje uprawnienia Odczyt, zapis i wykonywanie przez użytkownika, który ma grupę będącą właścicielem, ma uprawnienia tylko do odczytu i wykonania, i daje wszystkim innym uprawnienie do odczytu i zapisu.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_FileACL":::

### <a name="set-acls-recursively"></a>Cykliczne ustawianie list kontroli dostępu

Ustawianie list ACL cyklicznie przez wywołanie metody **DataLakeDirectoryClient. SetAccessControlRecursiveAsync** . Przekaż tę metodę z [listy](/dotnet/api/system.collections.generic.list-1) [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Każdy [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) definiuje wpis listy ACL.

Jeśli chcesz ustawić **domyślny** wpis listy ACL, możesz ustawić właściwość [PathAccessControlItem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) elementu [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) na **wartość true**. 

Ten przykład ustawia listę ACL katalogu o nazwie `my-parent-directory` . Ta metoda akceptuje parametr logiczny o nazwie `isDefaultScope` , który określa, czy ma zostać ustawiona domyślna lista ACL. Ten parametr jest używany w konstruktorze [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Wpisy listy ACL nadają użytkownikowi właściciela uprawnienia do odczytu, zapisu i wykonania, dają grupie będącej właścicielem tylko uprawnienia do odczytu i wykonywania, a inni nie mają dostępu. Ostatni wpis listy ACL w tym przykładzie daje określonemu użytkownikowi identyfikator "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "uprawnień do odczytu i wykonywania.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_SetACLRecursively":::

Aby zobaczyć przykład, który ustawia listy ACL rekursywnie w partiach przez określenie rozmiaru partii, zobacz [przykład](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)platformy .NET.

## <a name="update-acls"></a>Aktualizowanie list ACL

Podczas *aktualizowania* listy kontroli dostępu należy zmodyfikować listę ACL zamiast zastępować listę kontroli dostępu. Na przykład można dodać nowego podmiot zabezpieczeń do listy kontroli dostępu bez wpływu na inne podmioty zabezpieczeń wymienione na liście ACL.  Aby zastąpić listę ACL zamiast aktualizacji, zobacz sekcję [Ustawianie list ACL](#set-acls) w tym artykule.

W tej sekcji pokazano, jak:

- Aktualizowanie listy ACL
- Aktualizuje rekursywnie listy ACL

### <a name="update-an-acl"></a>Aktualizowanie listy ACL

Najpierw Pobierz listę kontroli dostępu do katalogu, wywołując metodę [DataLakeDirectoryClient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) . Skopiuj listę wpisów listy ACL do nowej [listy](/dotnet/api/system.collections.generic.list-1) obiektów [PathAccessControl](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrol) . Następnie odszukaj wpis, który chcesz zaktualizować, i zastąp go na liście. Ustaw listę ACL, wywołując metodę [DataLakeDirectoryClient. SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) .

Ten przykład aktualizuje główną listę kontroli dostępu kontenera, zastępując wpis listy ACL wszystkim innym użytkownikom. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_UpdateACL":::

### <a name="update-acls-recursively"></a>Aktualizuje rekursywnie listy ACL

Aby zaktualizować listę ACL rekursywnie, Utwórz nowy obiekt ACL z wpisem listy kontroli dostępu, który chcesz zaktualizować, a następnie użyj tego obiektu w operacji Update ACL. Nie pobieraj istniejącej listy kontroli dostępu, po prostu podaj wpisy listy ACL do zaktualizowania.

Aktualizowanie listy ACL rekursywnie przez wywołanie metody **DataLakeDirectoryClient. UpdateAccessControlRecursiveAsync** .  Przekaż tę metodę z [listy](/dotnet/api/system.collections.generic.list-1) [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Każdy [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) definiuje wpis listy ACL.

Jeśli chcesz zaktualizować **domyślny** wpis listy kontroli dostępu, możesz ustawić właściwość [PathAccessControlItem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) elementu [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) na **wartość true**.

Ten przykład aktualizuje wpis listy ACL z uprawnieniami do zapisu. Ta metoda akceptuje parametr logiczny o nazwie `isDefaultScope` , który określa, czy zaktualizować domyślną listę ACL. Ten parametr jest używany w konstruktorze [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_UpdateACLsRecursively":::

Aby zobaczyć przykład, który aktualizuje listy ACL rekursywnie w partiach przez określenie rozmiaru partii, zobacz [przykład](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)platformy .NET.

## <a name="remove-acl-entries"></a>Usuń wpisy listy ACL

Można usunąć co najmniej jeden wpis listy ACL. W tej sekcji pokazano, jak:

- Usuwanie wpisu listy ACL
- Usuń rekursywnie wpisów listy ACL

### <a name="remove-an-acl-entry"></a>Usuwanie wpisu listy ACL

Najpierw Pobierz listę kontroli dostępu do katalogu, wywołując metodę [DataLakeDirectoryClient. GetAccessControlAsync](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.getaccesscontrolasync) . Skopiuj listę wpisów listy ACL do nowej [listy](/dotnet/api/system.collections.generic.list-1) obiektów [PathAccessControl](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrol) . Następnie Znajdź wpis, który chcesz usunąć, i Wywołaj metodę [Remove](/dotnet/api/system.collections.ilist.remove) kolekcji. Ustaw zaktualizowaną listę ACL, wywołując metodę [DataLakeDirectoryClient. SetAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) .

Ten przykład aktualizuje główną listę kontroli dostępu kontenera, zastępując wpis listy ACL wszystkim innym użytkownikom. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_RemoveACLEntry":::

### <a name="remove-acl-entries-recursively"></a>Usuń rekursywnie wpisów listy ACL

Aby usunąć pozycje listy ACL cyklicznie, Utwórz nowy obiekt ACL dla wpisu listy ACL, który ma zostać usunięty, a następnie użyj tego obiektu w operacji usuwania listy ACL. Nie pobieraj istniejącej listy kontroli dostępu, po prostu podaj wpisy listy ACL do usunięcia. 

Usuń wpisy listy ACL, wywołując metodę **DataLakeDirectoryClient. RemoveAccessControlRecursiveAsync** . Przekaż tę metodę z [listy](/dotnet/api/system.collections.generic.list-1) [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem). Każdy [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) definiuje wpis listy ACL. 

Jeśli chcesz usunąć **domyślny** wpis listy kontroli dostępu, możesz ustawić właściwość [PathAccessControlItem. DefaultScope](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem.defaultscope#Azure_Storage_Files_DataLake_Models_PathAccessControlItem_DefaultScope) elementu [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem) na **wartość true**. 

Ten przykład usuwa wpis listy ACL z listy ACL katalogu o nazwie `my-parent-directory` . Ta metoda akceptuje parametr logiczny o nazwie `isDefaultScope` , który określa, czy usunąć wpis z domyślnej listy ACL. Ten parametr jest używany w konstruktorze [PathAccessControlItem](/dotnet/api/azure.storage.files.datalake.models.pathaccesscontrolitem).

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_RemoveACLRecursively":::

Aby zobaczyć przykład, który usuwa listy ACL cyklicznie w partiach przez określenie rozmiaru partii, zobacz [przykład](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)platformy .NET.

## <a name="recover-from-failures"></a>Odzyskiwanie po awarii

Podczas cyklicznego modyfikowania list ACL mogą wystąpić błędy środowiska uruchomieniowego lub uprawnień. W przypadku błędów środowiska uruchomieniowego należy ponownie uruchomić proces od początku. Błędy uprawnień mogą wystąpić, jeśli podmiot zabezpieczeń nie ma wystarczających uprawnień do modyfikowania listy ACL katalogu lub pliku znajdującego się w modyfikowanej hierarchii katalogów. Rozwiąż problem z uprawnieniami, a następnie wybierz wznowienie procesu od punktu awarii przy użyciu tokenu kontynuacji lub ponownie uruchom proces od początku. Nie musisz używać tokenu kontynuacji, jeśli wolisz uruchomić ponownie od początku. Możesz ponownie zastosować wpisy ACL bez żadnego negatywnego wpływu.

Ten przykład zwraca token kontynuacji w przypadku wystąpienia błędu. Aplikacja może wywołać tę przykładową metodę ponownie po rozpoczęciu błędu i przekazać token kontynuacji. Jeśli ta Przykładowa Metoda jest wywoływana po raz pierwszy, aplikacja może przekazać wartość `null` dla parametru tokenu kontynuacji. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ResumeContinuationToken":::

Aby zobaczyć przykład, który ustawia listy ACL rekursywnie w partiach przez określenie rozmiaru partii, zobacz [przykład](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)platformy .NET.

Jeśli chcesz, aby proces został zakończony nieprzerwanie przez błędy uprawnień, możesz określić, czy.

Aby upewnić się, że proces kończy się nieprzerwanie, przekaż obiekt **AccessControlChangedOptions** i ustaw właściwość **ContinueOnFailure** tego obiektu na ``true`` .

Ten przykład ustawia cyklicznie wpisy listy ACL. Jeśli ten kod napotyka błąd uprawnień, rejestruje ten błąd i kontynuuje wykonywanie. Ten przykład drukuje liczbę błędów w konsoli programu.

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/ACL_DataLake.cs" id="Snippet_ContinueOnFailure":::

Aby zobaczyć przykład, który ustawia listy ACL rekursywnie w partiach przez określenie rozmiaru partii, zobacz [przykład](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Frecursiveaclpr.blob.core.windows.net%2Fprivatedrop%2FRecursive-Acl-Sample-Net.zip%3Fsv%3D2019-02-02%26st%3D2020-08-24T07%253A45%253A28Z%26se%3D2021-09-25T07%253A45%253A00Z%26sr%3Db%26sp%3Dr%26sig%3D2GI3f0KaKMZbTi89AgtyGg%252BJePgNSsHKCL68V6I5W3s%253D&data=02%7C01%7Cnormesta%40microsoft.com%7C6eae76c57d224fb6de8908d848525330%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C637338865714571853&sdata=%2FWom8iI3DSDMSw%2FfYvAaQ69zbAoqXNTQ39Q9yVMnASA%3D&reserved=0)platformy .NET.

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Zobacz też

- [Dokumentacja referencyjna interfejsu API](/dotnet/api/azure.storage.files.datalake)
- [Pakiet (NuGet)](https://www.nuget.org/packages/Azure.Storage.Files.DataLake)
- [Samples](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake)
- [Mapowanie Gen1 do Gen2](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/storage/Azure.Storage.Files.DataLake/GEN1_GEN2_MAPPING.md)
- [Znane problemy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Przekaż opinię](https://github.com/Azure/azure-sdk-for-net/issues)
- [Model kontroli dostępu w Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Listy kontroli dostępu (ACL) w Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)