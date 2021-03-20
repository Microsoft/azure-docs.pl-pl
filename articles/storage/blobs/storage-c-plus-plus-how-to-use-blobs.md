---
title: Jak używać magazynu obiektów (BLOB) w języku C++ — Azure | Microsoft Docs
description: Dowiedz się, jak przechowywać dane niestrukturalne (BLOB) w chmurze za pomocą magazynu obiektów blob platformy Azure przy użyciu języka C++.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 07/16/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: 64069292ea0059216d06bfc41316c2aed7484dd0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011102"
---
# <a name="how-to-use-blob-storage-from-c"></a>Jak używać usługi BLOB Storage w języku C++

W tym przewodniku pokazano, jak wykonywać typowe scenariusze za pomocą usługi Azure Blob Storage. Przykłady pokazują, jak przekazywać, wyświetlać, pobierać i usuwać obiekty blob. Przykłady są napisane w języku C++ i korzystają z [biblioteki klienta usługi Azure Table Storage dla języka C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md).
Aby dowiedzieć się więcej o usłudze BLOB Storage, zobacz [wprowadzenie do usługi Azure Blob Storage](storage-blobs-introduction.md).

> [!NOTE]
> Ten przewodnik jest przeznaczony do użycia z biblioteką klienta usługi Azure Storage dla języka C++ w wersji 1.0.0 lub wyższej. Firma Microsoft zaleca korzystanie z najnowszej wersji biblioteki klienta usługi Storage dla języka C++, dostępnej za pośrednictwem programu [NuGet](https://www.nuget.org/packages/wastorage) lub [GitHub](https://github.com/Azure/azure-storage-cpp).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>Tworzenie aplikacji języka C++
W tym przewodniku będziesz używać funkcji magazynu, które można uruchomić w aplikacji C++.

W tym celu należy zainstalować bibliotekę klienta usługi Azure Storage dla języka C++ i utworzyć konto usługi Azure Storage w ramach subskrypcji platformy Azure.

Możesz zainstalować bibliotekę klienta usługi Azure Storage dla języka C++, korzystając z następujących metod:

- System **Linux:** Postępuj zgodnie z instrukcjami podanymi w [bibliotece klienta usługi Azure Storage dla pliku Readme języka C++: wprowadzenie na](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux) stronie systemu Linux.
- **System Windows:** W systemie Windows Użyj [vcpkg](https://github.com/microsoft/vcpkg) jako Menedżera zależności. Postępuj zgodnie z [przewodnikiem Szybki Start](https://github.com/microsoft/vcpkg#quick-start) , aby zainicjować vcpkg. Następnie użyj następującego polecenia, aby zainstalować bibliotekę:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

Przewodnik dotyczący sposobu tworzenia kodu źródłowego i eksportowania go do programu NuGet można znaleźć w pliku [README](https://github.com/Azure/azure-storage-cpp#download--install) .

## <a name="configure-your-application-to-access-blob-storage"></a>Konfigurowanie aplikacji w celu uzyskiwania dostępu do magazynu obiektów BLOB
Dodaj następujące instrukcje include na początku pliku języka C++, w którym chcesz używać interfejsów API usługi Azure Storage do uzyskiwania dostępu do obiektów blob:

```cpp
#include <was/storage_account.h>
#include <was/blob.h>
#include <cpprest/filestream.h>  
#include <cpprest/containerstream.h> 
```

## <a name="setup-an-azure-storage-connection-string"></a>Konfigurowanie parametrów połączenia usługi Azure Storage
W kliencie usługi Azure Storage punkty końcowe i poświadczenia wymagane do uzyskania dostępu do usług zarządzania danymi są przechowywane w parametrach połączenia magazynu. W przypadku uruchamiania w aplikacji klienckiej należy podać parametry połączenia magazynu w następującym formacie, używając nazwy konta magazynu i klucza dostępu do magazynu dla konta magazynu wymienionego w [Azure Portal](https://portal.azure.com) wartości *AccountName* i *AccountKey* . Aby uzyskać informacje na temat kont magazynu i kluczy dostępu, zobacz [Informacje o kontach usługi Azure Storage](../common/storage-account-create.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). W tym przykładzie pokazano, jak można zadeklarować pole statyczne w celu przechowywania parametrów połączenia:

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

Aby przetestować aplikację na komputerze lokalnym z systemem Windows, można użyć [emulatora magazynu azurite](../common/storage-use-azurite.md). Azurite to narzędzie, które symuluje usługi obiektów blob i Queue dostępne na platformie Azure na lokalnym komputerze deweloperskim. W tym przykładzie pokazano, jak można zadeklarować pole statyczne w celu przechowywania parametrów połączenia z lokalnym emulatorem magazynu:

```cpp
// Define the connection-string with Azurite.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));
```

Aby rozpocząć azurite, zobacz [Używanie emulatora azurite na potrzeby tworzenia lokalnych magazynów platformy Azure](../common/storage-use-azurite.md).

W poniższych przykładach założono, że uzyskano parametry połączenia za pomocą jednej z tych dwóch metod.

## <a name="retrieve-your-storage-account"></a>Pobierz konto magazynu
Możesz użyć klasy **cloud_storage_account** do reprezentowania informacji o koncie magazynu. Aby pobrać informacje o koncie magazynu z parametrów połączenia usługi Storage, użyj metody **parse**.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Następnie Pobierz odwołanie do klasy **cloud_blob_client** , ponieważ umożliwia to pobieranie obiektów reprezentujących kontenery i obiekty blob przechowywane w magazynie obiektów BLOB. Poniższy kod tworzy obiekt **cloud_blob_client** przy użyciu obiektu konta magazynu pobranego powyżej:

```cpp
// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  
```

## <a name="how-to-create-a-container"></a>Instrukcje: Tworzenie kontenera
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

W tym przykładzie pokazano, jak utworzyć kontener, jeśli jeszcze nie istnieje:

```cpp
try
{
    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Create the container if it doesn't already exist.
    container.create_if_not_exists();
}
catch (const std::exception& e)
{
    std::wcout << U("Error: ") << e.what() << std::endl;
}  
```

Domyślnie nowy kontener jest prywatny i należy określić klucz dostępu do magazynu, aby pobrać obiekty blob z tego kontenera. Jeśli chcesz, aby pliki (obiekty blob) w kontenerze były dostępne dla wszystkich użytkowników, możesz ustawić kontener jako publiczny przy użyciu następującego kodu:

```cpp
// Make the blob container publicly accessible.
azure::storage::blob_container_permissions permissions;
permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
container.upload_permissions(permissions);  
```

Każda osoba w Internecie może wyświetlać obiekty blob w kontenerze publicznym, ale można je modyfikować lub usuwać tylko wtedy, gdy dysponujesz odpowiednim kluczem dostępu.

## <a name="how-to-upload-a-blob-into-a-container"></a>Instrukcje: przekazywanie obiektu BLOB do kontenera
Usługa Azure Blob Storage obsługuje blokowe obiekty blob i stronicowe obiekty blob. W większości przypadków zalecane jest użycie blokowych obiektów blob.

Aby przekazać plik do blokowego obiektu blob, pobierz odwołanie do kontenera i uzyskaj za jego pomocą odwołanie do blokowego obiektu blob. Po uzyskaniu odwołania do obiektu BLOB możesz przekazać do niego dowolny strumień danych, wywołując metodę **upload_from_stream** . Ta operacja spowoduje utworzenie obiektu blob, jeśli jeszcze nie istnieje, lub jego zastąpienie, jeśli już istnieje. W poniższym przykładzie przedstawiono, jak przekazać obiekt blob do kontenera, zakładając, że kontener został już utworzony.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Create or overwrite the "my-blob-1" blob with contents from a local file.
concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
blockBlob.upload_from_stream(input_stream);
input_stream.close().wait();

// Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
// Retrieve a reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
blob2.upload_text(U("more text"));

// Retrieve a reference to a blob named "my-blob-3".
azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
blob3.upload_text(U("other text"));  
```

Alternatywnie możesz użyć metody **upload_from_file** , aby przekazać plik do blokowego obiektu BLOB.

## <a name="how-to-list-the-blobs-in-a-container"></a>Instrukcje: Wyświetlanie listy obiektów BLOB w kontenerze
Aby wyświetlić listę obiektów blob w kontenerze, należy najpierw uzyskać odwołanie do kontenera. Następnie można użyć metody **list_blobs** kontenera do pobrania obiektów blob i/lub znajdujących się w niej katalogów. Aby uzyskać dostęp do bogatego zestawu właściwości i metod dla zwracanych **list_blob_item**, należy wywołać metodę **list_blob_item. as _blob** , aby uzyskać obiekt  **cloud_blob** lub metodę **list_blob. as _directory** , aby uzyskać obiekt cloud_blob_directory. Poniższy kod ilustruje sposób pobierania i wyprowadzania identyfikatorów URI poszczególnych elementów w kontenerze **My-Sample-Container** :

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Output URI of each item.
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        std::wcout << U("Blob: ") << it->as_blob().uri().primary_uri().to_string() << std::endl;
    }
    else
    {
        std::wcout << U("Directory: ") << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
}
```

Aby uzyskać więcej informacji o operacjach związanych z wyświetlaniem, zobacz [Lista zasobów usługi Azure Storage w języku C++](../common/storage-c-plus-plus-enumeration.md).

## <a name="how-to-download-blobs"></a>Instrukcje: pobieranie obiektów BLOB
Aby pobrać obiekty blob, najpierw Pobierz odwołanie do obiektu BLOB, a następnie Wywołaj metodę **download_to_stream** . W poniższym przykładzie zastosowano metodę **download_to_stream** , aby przesłać zawartość obiektu BLOB do obiektu strumienia, który można następnie zachować do pliku lokalnego.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Save blob contents to a file.
concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
blockBlob.download_to_stream(output_stream);

std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();

outfile.write((char *)&data[0], buffer.size());
outfile.close();  
```

Alternatywnie możesz użyć metody **download_to_file** , aby pobrać zawartość obiektu BLOB do pliku.
Ponadto można również użyć metody **download_text** , aby pobrać zawartość obiektu BLOB jako ciąg tekstowy.

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));

// Download the contents of a blog as a text string.
utility::string_t text = text_blob.download_text();
```

## <a name="how-to-delete-blobs"></a>Instrukcje: usuwanie obiektów BLOB
Aby usunąć obiekt BLOB, należy najpierw pobrać odwołanie do obiektu BLOB, a następnie wywołać metodę **delete_blob** .

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Delete the blob.
blockBlob.delete_blob();
```

## <a name="next-steps"></a>Następne kroki
Teraz, gdy znasz już podstawy usługi BLOB Storage, Skorzystaj z poniższych linków, aby dowiedzieć się więcej o usłudze Azure Storage.

- [Jak używać usługi Queue Storage z poziomu języka C++](../queues/storage-c-plus-plus-how-to-use-queues.md)
- [Jak używać Table Storage z C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
- [Wyświetlanie listy zasobów usługi Azure Storage w języku C++](../common/storage-c-plus-plus-enumeration.md)
- [Dokumentacja biblioteki klienta usługi Storage dla języka C++](https://azure.github.io/azure-storage-cpp)
- [Dokumentacja usługi Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
- [Transferowanie danych za pomocą narzędzia wiersza polecenia AzCopy](../common/storage-use-azcopy-v10.md)