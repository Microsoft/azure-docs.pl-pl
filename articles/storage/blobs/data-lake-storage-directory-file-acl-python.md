---
title: Zarządzanie danymi w Azure Data Lake Storage Gen2 przy użyciu języka Python
description: Użyj języka Python do zarządzania katalogami i plikami na kontach magazynu, które mają włączoną hierarchiczną przestrzeń nazw.
author: normesta
ms.service: storage
ms.date: 02/17/2021
ms.author: normesta
ms.topic: how-to
ms.subservice: data-lake-storage-gen2
ms.reviewer: prishet
ms.custom: devx-track-python
ms.openlocfilehash: a143c0aa19241b532cabff95fe6bf85679e4007c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100652296"
---
# <a name="use-python-to-manage-directories-and-files-in-azure-data-lake-storage-gen2"></a>Zarządzanie katalogami i plikami w Azure Data Lake Storage Gen2 przy użyciu języka Python

W tym artykule pokazano, jak używać języka Python do tworzenia katalogów i plików oraz zarządzania nimi na kontach magazynu, które mają hierarchiczną przestrzeń nazw.

Aby dowiedzieć się, jak pobierać, ustawiać i aktualizować listy kontroli dostępu (ACL) katalogów i plików, zobacz Używanie języka [Python do zarządzania listami ACL w Azure Data Lake Storage Gen2](data-lake-storage-acl-python.md).

[Pakiet (indeks pakietu języka Python)](https://pypi.org/project/azure-storage-file-datalake/)  |  [Przykłady](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)  |  [Dokumentacja](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)  |  interfejsu API Mapowanie Gen1 do [Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)  |  [Przekaż opinię](https://github.com/Azure/azure-sdk-for-python/issues)

## <a name="prerequisites"></a>Wymagania wstępne

- Subskrypcja platformy Azure. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

- Konto magazynu, dla którego włączono hierarchiczną przestrzeń nazw. Postępuj zgodnie z [tymi](create-data-lake-storage-account.md) instrukcjami, aby je utworzyć.

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

### <a name="connect-by-using-an-account-key"></a>Nawiązywanie połączenia przy użyciu klucza konta

Jest to najprostszy sposób nawiązywania połączenia z kontem. 

Ten przykład tworzy wystąpienie **DataLakeServiceClient** przy użyciu klucza konta.

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithKey":::

- Zastąp wartość symbolu zastępczego `storage_account_name` nazwą konta magazynu.

- Zastąp `storage_account_key` wartość symbolu zastępczego kluczem dostępu do konta magazynu.

### <a name="connect-by-using-azure-active-directory-azure-ad"></a>Nawiązywanie połączenia przy użyciu Azure Active Directory (Azure AD)

Aby uwierzytelnić aplikację w usłudze Azure AD, możesz użyć [biblioteki klienta tożsamości platformy Azure dla języka Python](https://pypi.org/project/azure-identity/) .

Ten przykład tworzy wystąpienie **DataLakeServiceClient** przy użyciu identyfikatora klienta, klucza tajnego klienta i identyfikatora dzierżawy.  Aby uzyskać te wartości, zobacz [pozyskiwanie tokenu z usługi Azure AD w celu autoryzowania żądań z aplikacji klienckiej](../common/storage-auth-aad-app.md).

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_AuthorizeWithAAD":::

> [!NOTE]
> Aby uzyskać więcej przykładów, zobacz [Biblioteka klienta tożsamości platformy Azure dla](https://pypi.org/project/azure-identity/) dokumentacji języka Python.

## <a name="create-a-container"></a>Tworzenie kontenera

Kontener działa jako system plików dla plików. Można utworzyć jeden, wywołując metodę **FileSystemDataLakeServiceClient.create_file_system** .

Ten przykład tworzy kontener o nazwie `my-file-system` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_CreateContainer":::

## <a name="create-a-directory"></a>Tworzenie katalogu

Utwórz odwołanie do katalogu, wywołując metodę **FileSystemClient.create_directory** .

Ten przykład dodaje katalog o nazwie `my-directory` do kontenera. 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_CreateDirectory":::

## <a name="rename-or-move-a-directory"></a>Zmienianie nazwy lub przenoszenie katalogu

Zmień nazwę lub Przenieś katalog, wywołując metodę **DataLakeDirectoryClient.rename_directory** . Przekaż ścieżkę do żądanego katalogu jako parametr. 

Ten przykład zmienia nazwę podkatalogu na nazwę `my-subdirectory-renamed` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_RenameDirectory":::

## <a name="delete-a-directory"></a>Usuwanie katalogu

Usuń katalog, wywołując metodę **DataLakeDirectoryClient.delete_directory** .

Ten przykład usuwa katalog o nazwie `my-directory` .  

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_DeleteDirectory":::

## <a name="upload-a-file-to-a-directory"></a>Przekaż plik do katalogu

Najpierw Utwórz odwołanie do pliku w katalogu docelowym, tworząc wystąpienie klasy **DataLakeFileClient** . Przekaż plik, wywołując metodę **DataLakeFileClient.append_data** . Upewnij się, że ukończono przekazywanie, wywołując metodę **DataLakeFileClient.flush_data** .

Ten przykład przekazuje plik tekstowy do katalogu o nazwie `my-directory` .   

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_UploadFile":::

> [!TIP]
> Jeśli rozmiar pliku jest duży, kod będzie musiał wykonać wiele wywołań metody **DataLakeFileClient.append_data** . Zamiast tego Rozważ użycie metody **DataLakeFileClient.upload_data** . Dzięki temu można przekazać cały plik w jednym wywołaniu. 

## <a name="upload-a-large-file-to-a-directory"></a>Przekaż duży plik do katalogu

Użyj metody **DataLakeFileClient.upload_data** , aby przekazać duże pliki bez konieczności wykonywania wielu wywołań do metody **DataLakeFileClient.append_data** .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_UploadFileBulk":::

## <a name="download-from-a-directory"></a>Pobierz z katalogu 

Otwórz plik lokalny do zapisu. Następnie Utwórz wystąpienie **DataLakeFileClient** , które reprezentuje plik, który chcesz pobrać. Wywołaj **DataLakeFileClient.read_file** , aby odczytać bajty z pliku, a następnie zapisać te bajty do pliku lokalnego. 

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_DownloadFromDirectory":::

## <a name="list-directory-contents"></a>Wyświetlanie zawartości katalogu

Wyświetlanie zawartości katalogu przez wywołanie metody **FileSystemClient.get_paths** , a następnie Wyliczenie przez wyniki.

W tym przykładzie program drukuje ścieżkę każdego podkatalogu i pliku, który znajduje się w katalogu o nazwie `my-directory` .

:::code language="python" source="~/azure-storage-snippets/blobs/howto/python/python-v12/crud_datalake.py" id="Snippet_ListFilesInDirectory":::

## <a name="see-also"></a>Zobacz też

- [Dokumentacja referencyjna interfejsu API](/python/api/azure-storage-file-datalake/azure.storage.filedatalake)
- [Pakiet (indeks pakietu języka Python)](https://pypi.org/project/azure-storage-file-datalake/)
- [Samples](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/samples)
- [Mapowanie Gen1 do Gen2](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-file-datalake/GEN1_GEN2_MAPPING.md)
- [Znane problemy](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
- [Prześlij opinię](https://github.com/Azure/azure-sdk-for-python/issues)