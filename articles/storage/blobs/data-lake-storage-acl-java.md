---
title: Używanie języka Java do ustawiania list ACL w Azure Data Lake Storage Gen2
description: Biblioteki usługi Azure Storage dla języka Java umożliwiają zarządzanie listami kontroli dostępu (ACL) na kontach magazynu, które mają włączoną hierarchiczną przestrzeń nazw (SNS).
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.custom: devx-track-java
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.openlocfilehash: e7d6156fe5cd8ab32ff159bda64e0c06cfbac406
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654228"
---
# <a name="use-java-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Używanie języka Java do zarządzania listami ACL w Azure Data Lake Storage Gen2

W tym artykule pokazano, jak używać języka Java do pobierania, ustawiania i aktualizowania list kontroli dostępu do katalogów i plików. 

Dziedziczenie listy ACL jest już dostępne dla nowych elementów podrzędnych, które są tworzone w katalogu nadrzędnym. Można również dodawać, aktualizować i usuwać listy ACL cyklicznie na istniejących elementach podrzędnych katalogu nadrzędnego bez konieczności wprowadzania tych zmian indywidualnie dla każdego elementu podrzędnego. 

[Pakiet (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)  |  [Przykłady](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)  |  [Dokumentacja](/java/api/overview/azure/storage-file-datalake-readme)  |  interfejsu API Mapowanie Gen1 do [Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Przekaż opinię](https://github.com/Azure/azure-sdk-for-java/issues)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

- Konto magazynu z włączoną hierarchiczną przestrzenią nazw (SNS). Postępuj zgodnie z [tymi](create-data-lake-storage-account.md) instrukcjami, aby je utworzyć.

- Interfejs wiersza polecenia platformy Azure w wersji `2.6.0` lub nowszej.

- Jedno z następujących uprawnień zabezpieczeń:

  - Przydzielona [podmiot zabezpieczeń](../../role-based-access-control/overview.md#security-principal) Azure Active Directory (AD), do którego przypisano rolę [właściciela danych obiektów blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) w zakresie kontenera docelowego, nadrzędnej grupy zasobów lub subskrypcji.  

  - Użytkownik będący właścicielem docelowego kontenera lub katalogu, do którego planujesz zastosować ustawienia listy ACL. Aby ustawić listy ACL cyklicznie, obejmuje to wszystkie elementy podrzędne w kontenerze lub katalogu docelowym.
  
  - Klucz konta magazynu.

## <a name="set-up-your-project"></a>konfigurowanie projektu

Aby rozpocząć, Otwórz [Tę stronę](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake) i Znajdź najnowszą wersję biblioteki języka Java. Następnie otwórz plik *pom.xml* w edytorze tekstów. Dodaj element zależności, który odwołuje się do tej wersji.

Jeśli planujesz uwierzytelnienie aplikacji klienckiej za pomocą Azure Active Directory (AD), a następnie Dodaj zależność do biblioteki klienta usługi Azure Secret. Zobacz  [Dodawanie pakietu biblioteki tajnego klienta do projektu](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity#adding-the-package-to-your-project).

Następnie Dodaj te instrukcje Imports do pliku kodu.

```java
import com.azure.storage.common.StorageSharedKeyCredential;
import com.azure.storage.file.datalake.DataLakeDirectoryClient;
import com.azure.storage.file.datalake.DataLakeFileClient;
import com.azure.storage.file.datalake.DataLakeFileSystemClient;
import com.azure.storage.file.datalake.DataLakeServiceClient;
import com.azure.storage.file.datalake.DataLakeServiceClientBuilder;
import com.azure.storage.file.datalake.models.ListPathsOptions;
import com.azure.storage.file.datalake.models.PathItem;
import com.azure.storage.file.datalake.models.AccessControlChangeCounters;
import com.azure.storage.file.datalake.models.AccessControlChangeResult;
import com.azure.storage.file.datalake.models.AccessControlType;
import com.azure.storage.file.datalake.models.PathAccessControl;
import com.azure.storage.file.datalake.models.PathAccessControlEntry;
import com.azure.storage.file.datalake.models.PathPermissions;
import com.azure.storage.file.datalake.models.PathRemoveAccessControlEntry;
import com.azure.storage.file.datalake.models.RolePermissions;
import com.azure.storage.file.datalake.options.PathSetAccessControlRecursiveOptions;
```

## <a name="connect-to-the-account"></a>Połącz z kontem

Aby użyć fragmentów kodu w tym artykule, należy utworzyć wystąpienie **DataLakeServiceClient** reprezentujące konto magazynu. 

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Nawiązywanie połączenia przy użyciu Azure Active Directory (Azure AD)

Aby uwierzytelnić aplikację w usłudze Azure AD, możesz użyć [biblioteki klienta tożsamości platformy Azure dla języka Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) .

Pobierz identyfikator klienta, klucz tajny klienta i identyfikator dzierżawy. Aby to zrobić, zobacz [pozyskiwanie tokenu z usługi Azure AD w celu autoryzowania żądań z aplikacji klienckiej](../common/storage-auth-aad-app.md). W ramach tego procesu należy przypisać jedną z następujących ról [kontroli dostępu opartej na rolach (Azure RBAC)](../../role-based-access-control/overview.md) do podmiotu zabezpieczeń. 

|Rola|Możliwość ustawienia listy ACL|
|--|--|
|[Właściciel danych obiektu blob usługi Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Wszystkie katalogi i pliki na koncie.|
|[Współautor danych obiektu blob usługi Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Tylko katalogi i pliki należące do podmiotu zabezpieczeń.|

Ten przykład tworzy wystąpienie **DataLakeServiceClient** przy użyciu identyfikatora klienta, klucza tajnego klienta i identyfikatora dzierżawy.  

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithAzureAD":::

> [!NOTE]
> Aby uzyskać więcej przykładów, zobacz dokumentację [usługi Azure Identity Client Library for Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/identity/azure-identity) .

### <a name="connect-by-using-an-account-key"></a>Nawiązywanie połączenia przy użyciu klucza konta

Jest to najprostszy sposób nawiązywania połączenia z kontem. 

Ten przykład tworzy wystąpienie **DataLakeServiceClient** przy użyciu klucza konta.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/Authorize_DataLake.java" id="Snippet_AuthorizeWithKey":::

## <a name="set-acls"></a>Ustawianie list ACL

Po *ustawieniu* listy ACL **zastąpi** całą listę kontroli dostępu, w tym wszystkie jej wpisy. Jeśli chcesz zmienić poziom uprawnień podmiotu zabezpieczeń lub dodać nowego podmiot zabezpieczeń do listy kontroli dostępu bez wpływu na inne istniejące wpisy, należy zamiast tego *zaktualizować* listę kontroli dostępu. Aby zaktualizować listę ACL zamiast zastępować, zobacz sekcję [listy ACL aktualizacji](#update-acls) w tym artykule.  

Jeśli zdecydujesz się *ustawić* listę kontroli dostępu, musisz dodać wpis dla użytkownika będącego właścicielem, wpis dla grupy będącej właścicielem i wpis dla wszystkich innych użytkowników. Aby dowiedzieć się więcej na temat użytkownika będącego właścicielem, grupy będącej właścicielem i wszystkich innych użytkowników, zobacz [Użytkownicy i tożsamości](data-lake-storage-access-control.md#users-and-identities).

W tej sekcji pokazano, jak:

- Ustawianie listy ACL katalogu
- Ustawianie listy ACL pliku
- Cykliczne ustawianie list kontroli dostępu 

### <a name="set-the-acl-of-a-directory"></a>Ustawianie listy ACL katalogu

Ten przykład pobiera i ustawia listę ACL katalogu o nazwie `my-directory` . Ten przykład daje właścicielowi uprawnień Odczyt, zapis i wykonywanie, daje grupie będącej właścicielem tylko uprawnienia do odczytu i wykonywania, a ponadto daje wszystkim innym osobom dostęp do odczytu.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageDirectoryACLs":::

Możesz również uzyskać i ustawić listę kontroli dostępu dla katalogu głównego kontenera. Aby uzyskać katalog główny, należy przekazać pusty ciąg ( `""` ) do metody **DataLakeFileSystemClient. getDirectoryClient** .

### <a name="set-the-acl-of-a-file"></a>Ustawianie listy ACL pliku

Ten przykład pobiera i ustawia listę ACL pliku o nazwie `upload-file.txt` . Ten przykład daje właścicielowi uprawnień Odczyt, zapis i wykonywanie, daje grupie będącej właścicielem tylko uprawnienia do odczytu i wykonywania, a ponadto daje wszystkim innym osobom dostęp do odczytu.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ManageFileACLs":::

### <a name="set-acls-recursively"></a>Cykliczne ustawianie list kontroli dostępu

Ustawianie list ACL cyklicznie przez wywołanie metody **DataLakeDirectoryClient. setAccessControlRecursive** . Przekaż tę metodę z [listy](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) obiektów [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) . Każdy [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) definiuje wpis listy ACL.

Jeśli chcesz ustawić **domyślny** wpis listy kontroli dostępu, możesz wywołać metodę **setDefaultScope** [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) i przekazać wartość **true**. 

Ten przykład ustawia listę ACL katalogu o nazwie `my-parent-directory` . Ta metoda akceptuje parametr logiczny o nazwie `isDefaultScope` , który określa, czy ma zostać ustawiona domyślna lista ACL. Ten parametr jest używany w każdym wywołaniu metody **SetDefaultScope** [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html). Wpisy listy ACL nadają użytkownikowi właściciela uprawnienia do odczytu, zapisu i wykonania, dają grupie będącej właścicielem tylko uprawnienia do odczytu i wykonywania, a inni nie mają dostępu. Ostatni wpis listy ACL w tym przykładzie daje określonemu użytkownikowi IDENTYFIKATORowi "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" uprawnień do odczytu i wykonywania.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_SetACLRecursively":::

## <a name="update-acls"></a>Aktualizowanie list ACL

Podczas *aktualizowania* listy kontroli dostępu należy zmodyfikować listę ACL zamiast zastępować listę kontroli dostępu. Na przykład można dodać nowego podmiot zabezpieczeń do listy kontroli dostępu bez wpływu na inne podmioty zabezpieczeń wymienione na liście ACL.  Aby zastąpić listę ACL zamiast aktualizacji, zobacz sekcję [Ustawianie list ACL](#set-acls) w tym artykule.

W tej sekcji pokazano, jak:

- Aktualizowanie listy ACL
- Aktualizuje rekursywnie listy ACL

### <a name="update-an-acl"></a>Aktualizowanie listy ACL

Najpierw Pobierz listę kontroli dostępu do katalogu, wywołując metodę [PathAccessControl. getAccessControlList](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrol.getaccesscontrollist) . Skopiuj listę wpisów listy ACL do nowego obiektu **listy** typu [PathAccessControlListEntry](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrolentry). Następnie odszukaj wpis, który chcesz zaktualizować, i zastąp go na liście. Ustaw listę ACL, wywołując metodę [DataLakeDirectoryClient. setAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) .

Ten przykład aktualizuje listę ACL katalogu o nazwie `my-parent-directory` przez zastąpienie wpisu dla wszystkich innych użytkowników. 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_UpdateACL":::

Możesz również uzyskać i ustawić listę kontroli dostępu dla katalogu głównego kontenera. Aby uzyskać katalog główny, należy przekazać pusty ciąg ( `""` ) do metody **DataLakeFileSystemClient. getDirectoryClient** .

### <a name="update-acls-recursively"></a>Aktualizuje rekursywnie listy ACL

Aby zaktualizować listę ACL rekursywnie, Utwórz nowy obiekt ACL z wpisem listy kontroli dostępu, który chcesz zaktualizować, a następnie użyj tego obiektu w operacji Update ACL. Nie pobieraj istniejącej listy kontroli dostępu, po prostu podaj wpisy listy ACL do zaktualizowania.

Aktualizowanie list ACL rekursywnie przez wywołanie metody **DataLakeDirectoryClient. updateAccessControlRecursive** .  Przekaż tę metodę z [listy](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) obiektów [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) . Każdy [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) definiuje wpis listy ACL. 

Jeśli chcesz zaktualizować **domyślny** wpis listy kontroli dostępu, możesz użyć metody **setDefaultScope** [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) i przekazać wartość **true**. 

Ten przykład aktualizuje wpis listy ACL z uprawnieniami do zapisu. Ta metoda akceptuje parametr logiczny o nazwie `isDefaultScope` , który określa, czy zaktualizować domyślną listę ACL. Ten parametr jest używany w wywołaniu metody **setDefaultScope** w [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html). 

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_UpdateACLRecursively":::

## <a name="remove-acl-entries"></a>Usuń wpisy listy ACL

Można usunąć co najmniej jeden wpis listy ACL. W tej sekcji pokazano, jak:

- Usuwanie wpisu listy ACL
- Usuń rekursywnie wpisów listy ACL

### <a name="remove-an-acl-entry"></a>Usuwanie wpisu listy ACL

Najpierw Pobierz listę kontroli dostępu do katalogu, wywołując metodę [PathAccessControl. getAccessControlList](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrol.getaccesscontrollist) . Skopiuj listę wpisów listy ACL do nowego obiektu **listy** typu [PathAccessControlListEntry](/java/api/com.azure.storage.file.datalake.models.pathaccesscontrolentry). Następnie zlokalizuj wpis, który chcesz usunąć, i Wywołaj metodę **Remove** obiektu **list** . Ustaw zaktualizowaną listę ACL, wywołując metodę [DataLakeDirectoryClient. setAccessControlList](/dotnet/api/azure.storage.files.datalake.datalakedirectoryclient.setaccesscontrollist) .

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_RemoveACLEntry":::

### <a name="remove-acl-entries-recursively"></a>Usuń rekursywnie wpisów listy ACL

Aby usunąć pozycje listy ACL cyklicznie, Utwórz nowy obiekt ACL dla wpisu listy ACL, który ma zostać usunięty, a następnie użyj tego obiektu w operacji usuwania listy ACL. Nie pobieraj istniejącej listy kontroli dostępu, po prostu podaj wpisy listy ACL do usunięcia.

Usuń wpisy listy ACL, wywołując metodę **DataLakeDirectoryClient. removeAccessControlRecursive** . Przekaż tę metodę z [listy](https://docs.oracle.com/javase/8/docs/api/java/util/List.html) obiektów [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) . Każdy [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) definiuje wpis listy ACL. 

Jeśli chcesz usunąć **domyślny** wpis listy kontroli dostępu, możesz użyć metody **setDefaultScope** [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) i przekazać wartość **true**.  

Ten przykład usuwa wpis listy ACL z listy ACL katalogu o nazwie `my-parent-directory` . Ta metoda akceptuje parametr logiczny o nazwie `isDefaultScope` , który określa, czy usunąć wpis z domyślnej listy ACL. Ten parametr jest używany w wywołaniu metody **setDefaultScope** w [PathAccessControlEntry](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html).

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_RemoveACLRecursively":::

## <a name="recover-from-failures"></a>Odzyskiwanie po awarii

Mogą wystąpić błędy środowiska uruchomieniowego lub uprawnień. W przypadku błędów środowiska uruchomieniowego należy ponownie uruchomić proces od początku. Błędy uprawnień mogą wystąpić, jeśli podmiot zabezpieczeń nie ma wystarczających uprawnień do modyfikowania listy ACL katalogu lub pliku znajdującego się w modyfikowanej hierarchii katalogów. Rozwiąż problem z uprawnieniami, a następnie wybierz wznowienie procesu od punktu awarii przy użyciu tokenu kontynuacji lub ponownie uruchom proces od początku. Nie musisz używać tokenu kontynuacji, jeśli wolisz uruchomić ponownie od początku. Możesz ponownie zastosować wpisy ACL bez żadnego negatywnego wpływu.

Ten przykład zwraca token kontynuacji w przypadku wystąpienia błędu. Aplikacja może wywołać tę przykładową metodę ponownie po rozpoczęciu błędu i przekazać token kontynuacji. Jeśli ta Przykładowa Metoda jest wywoływana po raz pierwszy, aplikacja może przekazać wartość `null` dla parametru tokenu kontynuacji.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ResumeSetACLRecursively":::

Jeśli chcesz, aby proces został zakończony nieprzerwanie przez błędy uprawnień, możesz określić, czy.

Aby upewnić się, że proces kończy się nieprzerwanie, wywołaj metodę **setContinueOnFailure** obiektu [PathSetAccessControlRecursiveOptions](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-storage-file-datalake/12.3.0-beta.1/index.html) i przekaż wartość **true**.

Ten przykład ustawia cyklicznie wpisy listy ACL. Jeśli ten kod napotyka błąd uprawnień, rejestruje ten błąd i kontynuuje wykonywanie. Ten przykład drukuje liczbę błędów w konsoli programu.

:::code language="java" source="~/azure-storage-snippets/blobs/howto/Java/Java-v12/src/main/java/com/datalake/manage/ACL_DataLake.java" id="Snippet_ContinueOnFailure":::

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Zobacz też

- [Dokumentacja referencyjna interfejsu API](/java/api/overview/azure/storage-file-datalake-readme)
- [Pakiet (Maven)](https://search.maven.org/artifact/com.azure/azure-storage-file-datalake)
- [Samples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake)
- [Mapowanie Gen1 do Gen2](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [Znane problemy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Przekaż opinię](https://github.com/Azure/azure-sdk-for-java/issues)
- [Model kontroli dostępu w Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Listy kontroli dostępu (ACL) w Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)