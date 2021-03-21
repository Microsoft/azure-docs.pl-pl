---
title: Zarządzanie listami ACL w Azure Data Lake Storage Gen2 przy użyciu języka Python
description: Użyj języka Python zarządzanie listami kontroli dostępu (ACL) na kontach magazynu, które mają włączoną hierarchiczną przestrzeń nazw (SNS).
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: ba864aa1aa2462f21e05ab5e779c8e715d6bb973
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100654221"
---
# <a name="use-python-to-manage-acls-in-azure-data-lake-storage-gen2"></a>Zarządzanie listami ACL w Azure Data Lake Storage Gen2 przy użyciu języka Python

W tym artykule pokazano, jak używać języka Python do pobierania, ustawiania i aktualizowania list kontroli dostępu do katalogów i plików. 

Dziedziczenie listy ACL jest już dostępne dla nowych elementów podrzędnych, które są tworzone w katalogu nadrzędnym. Można również dodawać, aktualizować i usuwać listy ACL cyklicznie na istniejących elementach podrzędnych katalogu nadrzędnego bez konieczności wprowadzania tych zmian indywidualnie dla każdego elementu podrzędnego. 

[Pakiet (indeks pakietu języka Python)](https://pypi.org/project/azure-storage-file-datalake/)  |  [Przykłady](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)  |  [Rekursywne przykłady](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)  |  listy ACL [Dokumentacja](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)  |  interfejsu API Mapowanie Gen1 do [Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Przekaż opinię](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

- Konto magazynu z włączoną hierarchiczną przestrzenią nazw (SNS). Postępuj zgodnie z [tymi](create-data-lake-storage-account.md) instrukcjami, aby je utworzyć.

- Interfejs wiersza polecenia platformy Azure w wersji `2.6.0` lub nowszej.

- Jedno z następujących uprawnień zabezpieczeń:

  - Przydzielona [podmiot zabezpieczeń](../../role-based-access-control/overview.md#security-principal) Azure Active Directory (AD), do którego przypisano rolę [właściciela danych obiektów blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) w zakresie kontenera docelowego, nadrzędnej grupy zasobów lub subskrypcji.  

  - Użytkownik będący właścicielem docelowego kontenera lub katalogu, do którego planujesz zastosować ustawienia listy ACL. Aby ustawić listy ACL cyklicznie, obejmuje to wszystkie elementy podrzędne w kontenerze lub katalogu docelowym.
  
  - Klucz konta magazynu.

## <a name="set-up-your-project"></a>konfigurowanie projektu

Zainstaluj Azure Data Lake Storageą bibliotekę kliencką dla języka Python za pomocą narzędzia [PIP](https://pypi.org/project/pip/).

```
pip install azure-storage-file-datalake
```

Dodaj te instrukcje importu na początku pliku kodu.

```python
import os, uuid, sys
from azure.storage.filedatalake import DataLakeServiceClient
from azure.core._match_conditions import MatchConditions
from azure.storage.filedatalake._models import ContentSettings
```

## <a name="connect-to-the-account"></a>Połącz z kontem

Aby użyć fragmentów kodu w tym artykule, należy utworzyć wystąpienie **DataLakeServiceClient** reprezentujące konto magazynu. 

### <a name="connect-by-using-azure-active-directory-ad"></a>Łączenie przy użyciu Azure Active Directory (AD)

> [!NOTE]
> Jeśli używasz usługi Azure Active Directory (Azure AD) do autoryzacji dostępu, upewnij się, że podmiot zabezpieczeń ma przypisaną [rolę właściciela danych obiektu blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner). Aby dowiedzieć się więcej na temat sposobu stosowania uprawnień ACL i skutków ich zmiany, zobacz  [model kontroli dostępu w Azure Data Lake Storage Gen2](./data-lake-storage-access-control-model.md).

Aby uwierzytelnić aplikację w usłudze Azure AD, możesz użyć [biblioteki klienta tożsamości platformy Azure dla języka Python](https://pypi.org/project/azure-identity/) .

Pobierz identyfikator klienta, klucz tajny klienta i identyfikator dzierżawy. Aby to zrobić, zobacz [pozyskiwanie tokenu z usługi Azure AD w celu autoryzowania żądań z aplikacji klienckiej](../common/storage-auth-aad-app.md). W ramach tego procesu należy przypisać jedną z następujących ról [kontroli dostępu opartej na rolach (Azure RBAC)](../../role-based-access-control/overview.md) do podmiotu zabezpieczeń. 

|Rola|Możliwość ustawienia listy ACL|
|--|--|
|[Właściciel danych obiektu blob usługi Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner)|Wszystkie katalogi i pliki na koncie.|
|[Współautor danych obiektu blob usługi Storage](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)|Tylko katalogi i pliki należące do podmiotu zabezpieczeń.|

Ten przykład tworzy wystąpienie **DataLakeServiceClient** przy użyciu identyfikatora klienta, klucza tajnego klienta i identyfikatora dzierżawy.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Aby uzyskać więcej przykładów, zobacz [Biblioteka klienta tożsamości platformy Azure dla](https://pypi.org/project/azure-identity/) dokumentacji języka Python.

### <a name="connect-by-using-an-account-key"></a>Nawiązywanie połączenia przy użyciu klucza konta

Jest to najprostszy sposób nawiązywania połączenia z kontem.

Ten przykład tworzy wystąpienie **DataLakeServiceClient** przy użyciu klucza konta.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithKey":::
 
- Zastąp wartość symbolu zastępczego `storage_account_name` nazwą konta magazynu.

- Zastąp `storage_account_key` wartość symbolu zastępczego kluczem dostępu do konta magazynu.

## <a name="set-acls"></a>Ustawianie list ACL

Po *ustawieniu* listy ACL **zastąpi** całą listę kontroli dostępu, w tym wszystkie jej wpisy. Jeśli chcesz zmienić poziom uprawnień podmiotu zabezpieczeń lub dodać nowego podmiot zabezpieczeń do listy kontroli dostępu bez wpływu na inne istniejące wpisy, należy zamiast tego *zaktualizować* listę kontroli dostępu. Aby zaktualizować listę ACL zamiast zastępować, zobacz sekcję [listy ACL aktualizacji](#update-acls-recursively) w tym artykule.  

W tej sekcji pokazano, jak:

- Ustawianie listy ACL katalogu
- Ustawianie listy ACL pliku

### <a name="set-the-acl-of-a-directory"></a>Ustawianie listy ACL katalogu

Pobierz listę kontroli dostępu (ACL) katalogu, wywołując metodę **DataLakeDirectoryClient.get_access_control** i ustawiając listę ACL, wywołując metodę **DataLakeDirectoryClient.set_access_control** .

Ten przykład pobiera i ustawia listę ACL katalogu o nazwie `my-directory` . Ciąg `rwxr-xrw-` przyznaje uprawnienia Odczyt, zapis i wykonywanie przez użytkownika, który ma grupę będącą właścicielem, ma uprawnienia tylko do odczytu i wykonania, i daje wszystkim innym uprawnienie do odczytu i zapisu.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ACLDirectory":::

Możesz również uzyskać i ustawić listę kontroli dostępu dla katalogu głównego kontenera. Aby uzyskać katalog główny, wywołaj metodę **FileSystemClient._get_root_directory_client** .

### <a name="set-the-acl-of-a-file"></a>Ustawianie listy ACL pliku

Pobierz listę kontroli dostępu (ACL) pliku, wywołując metodę **DataLakeFileClient.get_access_control** i ustawiając listę ACL, wywołując metodę **DataLakeFileClient.set_access_control** .

Ten przykład pobiera i ustawia listę ACL pliku o nazwie `my-file.txt` . Ciąg `rwxr-xrw-` przyznaje uprawnienia Odczyt, zapis i wykonywanie przez użytkownika, który ma grupę będącą właścicielem, ma uprawnienia tylko do odczytu i wykonania, i daje wszystkim innym uprawnienie do odczytu i zapisu.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_FileACL":::

## <a name="set-acls-recursively"></a>Cykliczne ustawianie list kontroli dostępu

Po *ustawieniu* listy ACL **zastąpi** całą listę kontroli dostępu, w tym wszystkie jej wpisy. Jeśli chcesz zmienić poziom uprawnień podmiotu zabezpieczeń lub dodać nowego podmiot zabezpieczeń do listy kontroli dostępu bez wpływu na inne istniejące wpisy, należy zamiast tego *zaktualizować* listę kontroli dostępu. Aby zaktualizować listę kontroli dostępu, a nie zastępować, zobacz sekcję [Aktualizowanie list ACL rekursywnie](#update-acls-recursively) w tym artykule.

Ustawianie list ACL cyklicznie przez wywołanie metody **DataLakeDirectoryClient.set_access_control_recursive** .

Jeśli chcesz ustawić **domyślny** wpis listy kontroli dostępu, Dodaj ciąg `default:` do początku każdego ciągu wpisu listy ACL.

Ten przykład ustawia listę ACL katalogu o nazwie `my-parent-directory` .

Ta metoda akceptuje parametr logiczny o nazwie `is_default_scope` , który określa, czy ma zostać ustawiona domyślna lista ACL. Jeśli ten parametr ma wartość `True` , lista wpisów listy ACL jest poprzedzana ciągiem `default:` .

Wpisy listy ACL nadają użytkownikowi właściciela uprawnienia do odczytu, zapisu i wykonania, dają grupie będącej właścicielem tylko uprawnienia do odczytu i wykonywania, a inni nie mają dostępu. Ostatni wpis listy ACL w tym przykładzie daje określonemu użytkownikowi identyfikator "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "uprawnień do odczytu i wykonywania. Te wpisy dają użytkownikowi właściciela uprawnienia do odczytu, zapisu i wykonywania, dają członkom należącym do grupy uprawnienia Odczyt i wykonywanie, a inni nie mają dostępu. Ostatni wpis listy ACL w tym przykładzie daje określonemu użytkownikowi identyfikator "" xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx "uprawnień do odczytu i wykonywania.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_SetACLRecursively":::

Aby zobaczyć przykład, który przetwarza listy ACL cyklicznie w partiach przez określenie rozmiaru partii, zobacz [przykład](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)języka Python.

## <a name="update-acls-recursively"></a>Aktualizuje rekursywnie listy ACL

Podczas *aktualizowania* listy kontroli dostępu należy zmodyfikować listę ACL zamiast zastępować listę kontroli dostępu. Na przykład można dodać nowego podmiot zabezpieczeń do listy kontroli dostępu bez wpływu na inne podmioty zabezpieczeń wymienione na liście ACL.  Aby zastąpić listę ACL zamiast aktualizacji, zobacz sekcję [Ustawianie list ACL](#set-acls) w tym artykule.

Aby zaktualizować listę ACL rekursywnie, Utwórz nowy obiekt ACL z wpisem listy kontroli dostępu, który chcesz zaktualizować, a następnie użyj tego obiektu w operacji Update ACL. Nie pobieraj istniejącej listy kontroli dostępu, po prostu podaj wpisy listy ACL do zaktualizowania. Aktualizowanie listy ACL rekursywnie przez wywołanie metody **DataLakeDirectoryClient.update_access_control_recursive** . Jeśli chcesz zaktualizować **domyślny** wpis listy kontroli dostępu, Dodaj ciąg `default:` do początku każdego ciągu wpisu listy ACL.

Ten przykład aktualizuje wpis listy ACL z uprawnieniami do zapisu.

Ten przykład ustawia listę ACL katalogu o nazwie `my-parent-directory` . Ta metoda akceptuje parametr logiczny o nazwie `is_default_scope` , który określa, czy zaktualizować domyślną listę ACL. Jeśli ten parametr ma wartość `True` , zaktualizowany wpis ACL jest poprzedzony ciągiem `default:` .  

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_UpdateACLsRecursively":::

Aby zobaczyć przykład, który przetwarza listy ACL cyklicznie w partiach przez określenie rozmiaru partii, zobacz [przykład](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)języka Python.

## <a name="remove-acl-entries-recursively"></a>Usuń rekursywnie wpisów listy ACL

Można usunąć co najmniej jeden wpis listy ACL. Aby usunąć pozycje listy ACL cyklicznie, Utwórz nowy obiekt ACL dla wpisu listy ACL, który ma zostać usunięty, a następnie użyj tego obiektu w operacji usuwania listy ACL. Nie pobieraj istniejącej listy kontroli dostępu, po prostu podaj wpisy listy ACL do usunięcia. 

Usuń wpisy listy ACL, wywołując metodę **DataLakeDirectoryClient.remove_access_control_recursive** . Jeśli chcesz usunąć **domyślny** wpis listy kontroli dostępu, Dodaj ciąg `default:` do początku ciągu wpisu listy ACL. 

Ten przykład usuwa wpis listy ACL z listy ACL katalogu o nazwie `my-parent-directory` . Ta metoda akceptuje parametr logiczny o nazwie `is_default_scope` , który określa, czy usunąć wpis z domyślnej listy ACL. Jeśli ten parametr ma wartość `True` , zaktualizowany wpis ACL jest poprzedzony ciągiem `default:` . 

```python
def remove_permission_recursively(is_default_scope):
    
    try:
        file_system_client = service_client.get_file_system_client(file_system="my-container")

        directory_client = file_system_client.get_directory_client("my-parent-directory")
              
        acl = 'user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        if is_default_scope:
           acl = 'default:user:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

        directory_client.remove_access_control_recursive(acl=acl)

    except Exception as e:
     print(e)
```

Aby zobaczyć przykład, który przetwarza listy ACL cyklicznie w partiach przez określenie rozmiaru partii, zobacz [przykład](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)języka Python.

## <a name="recover-from-failures"></a>Odzyskiwanie po awarii

Mogą wystąpić błędy środowiska uruchomieniowego lub uprawnień. W przypadku błędów środowiska uruchomieniowego należy ponownie uruchomić proces od początku. Błędy uprawnień mogą wystąpić, jeśli podmiot zabezpieczeń nie ma wystarczających uprawnień do modyfikowania listy ACL katalogu lub pliku znajdującego się w modyfikowanej hierarchii katalogów. Rozwiąż problem z uprawnieniami, a następnie wybierz wznowienie procesu od punktu awarii przy użyciu tokenu kontynuacji lub ponownie uruchom proces od początku. Nie musisz używać tokenu kontynuacji, jeśli wolisz uruchomić ponownie od początku. Możesz ponownie zastosować wpisy ACL bez żadnego negatywnego wpływu.

Ten przykład zwraca token kontynuacji w przypadku wystąpienia błędu. Aplikacja może wywołać tę przykładową metodę ponownie po rozpoczęciu błędu i przekazać token kontynuacji. Jeśli ta Przykładowa Metoda jest wywoływana po raz pierwszy, aplikacja może przekazać wartość ``None`` dla parametru tokenu kontynuacji.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ResumeContinuationToken":::

Aby zobaczyć przykład, który przetwarza listy ACL cyklicznie w partiach przez określenie rozmiaru partii, zobacz [przykład](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)języka Python.

Jeśli chcesz, aby proces został zakończony nieprzerwanie przez błędy uprawnień, możesz określić, czy.

Aby upewnić się, że proces kończy się nieprzerwanie, nie przekazuj tokenu kontynuacji do metody **DataLakeDirectoryClient.set_access_control_recursive** .

Ten przykład ustawia cyklicznie wpisy listy ACL. Jeśli ten kod napotyka błąd uprawnień, rejestruje ten błąd i kontynuuje wykonywanie. Ten przykład drukuje liczbę błędów w konsoli programu.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/ACL_datalake.py" id="Snippet_ContinueOnFailure":::

Aby zobaczyć przykład, który przetwarza listy ACL cyklicznie w partiach przez określenie rozmiaru partii, zobacz [przykład](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/storage/azure-storage-file-datalake/samples/datalake_samples_access_control_recursive.py)języka Python.

[!INCLUDE [updated-for-az](../../../includes/recursive-acl-best-practices.md)]

## <a name="see-also"></a>Zobacz też

- [Dokumentacja referencyjna interfejsu API](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
- [Pakiet (indeks pakietu języka Python)](https://pypi.org/project/azure-storage-file-datalake/)
- [Samples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
- [Mapowanie Gen1 do Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [Znane problemy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Prześlij opinię](https://github.com/Azure/azure-sdk-for-python/issues)
- [Model kontroli dostępu w Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)
- [Listy kontroli dostępu (ACL) w Azure Data Lake Storage Gen2](data-lake-storage-access-control.md)