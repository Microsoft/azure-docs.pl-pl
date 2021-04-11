---
title: 'Szybki Start: Azure Blob Storage Client Library — Ruby'
description: Utwórz konto magazynu i kontener w usłudze Azure Blob Storage. Użyj biblioteki klienta usługi Storage dla języka Ruby, aby utworzyć obiekt BLOB, pobrać obiekt BLOB i wyświetlić listę obiektów BLOB w kontenerze.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 12/04/2020
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: ec3fc490466f5fce36b67b2f3744e4ee5cc0ae79
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96781100"
---
# <a name="quickstart-azure-blob-storage-client-library-for-ruby"></a>Szybki Start: Biblioteka kliencka Blob Storage platformy Azure dla języka Ruby

Dowiedz się, jak używać języka Ruby do tworzenia, pobierania i wyświetlania listy obiektów BLOB w kontenerze w Microsoft Azure Blob Storage.

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Upewnij się, że masz zainstalowane następujące dodatkowe elementy wymagane wstępnie:

- [Ruby](https://www.ruby-lang.org/en/downloads/)
- [Biblioteka usługi Azure Storage dla języka Ruby](https://github.com/azure/azure-storage-ruby)przy użyciu [pakietu RubyGem](https://rubygems.org/gems/azure-storage-blob):

    ```console
    gem install azure-storage-blob
    ```

## <a name="download-the-sample-application"></a>Pobieranie przykładowej aplikacji

[Przykładowa aplikacja](https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git) używana w tym przewodniku Szybki start to podstawowa aplikacja w języku Ruby.

Użyj [narzędzia Git](https://git-scm.com/) , aby pobrać kopię aplikacji do środowiska deweloperskiego. To polecenie klonuje repozytorium na komputerze lokalnym:

```console
git clone https://github.com/Azure-Samples/storage-blobs-ruby-quickstart.git
```

Przejdź do folderu *Storage-Blobs-Ruby-Start* i Otwórz plik *example. RB* w edytorze kodu.

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurowanie parametrów połączenia magazynu

Podaj nazwę konta magazynu i klucz konta, aby utworzyć wystąpienie [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) dla swojej aplikacji.

Poniższy kod w *przykładzie. plik RB* tworzy wystąpienie nowego obiektu [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) . Zastąp wartości *accountname* i *accountkey* własną nazwą konta i własnym kluczem.

```ruby
# Create a BlobService object
account_name = "accountname"
account_key = "accountkey"

blob_client = Azure::Storage::Blob::BlobService.create(
    storage_account_name: account_name,
    storage_access_key: account_key
)
```

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej

Przykład tworzy kontener w Blob Storage, tworzy nowy obiekt BLOB w kontenerze, wyświetla obiekty blob w kontenerze i pobiera obiekt BLOB do pliku lokalnego.

Uruchom przykład. Oto przykład danych wyjściowych z uruchamiania aplikacji:

```console
C:\azure-samples\storage-blobs-ruby-quickstart> ruby example.rb

Creating a container: quickstartblobs18cd9ec0-f4ac-4688-a979-75c31a70503e

Creating blob: QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

List blobs in the container following continuation token
        Blob name: QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

Downloading blob to C:/Users/azureuser/Documents/QuickStart_6f8f29a8-879a-41fb-9db2-0b8595180728.txt

Paused, press the Enter key to delete resources created by the sample and exit the application
```

Po naciśnięciu klawisza ENTER w celu kontynuowania program przykładu usunie kontener magazynu i plik lokalny. Przed kontynuowaniem sprawdź pobrany plik w folderze *dokumenty* .

Możesz również użyć [Eksplorator usługi Azure Storage](https://storageexplorer.com) , aby wyświetlić pliki na koncie magazynu. Eksplorator usługi Azure Storage to darmowe narzędzie międzyplatformowe, które umożliwia dostęp do informacji na koncie magazynu.

Po zweryfikowaniu plików naciśnij klawisz ENTER, aby usunąć pliki testowe i zakończyć demonstrację. Otwórz plik *example. RB* i przyjrzyj się kodowi.

## <a name="understand-the-sample-code"></a>Omówienie przykładowego kodu

Następnie przeglądamy przykładowy kod, aby zrozumieć, jak to działa.

### <a name="get-references-to-the-storage-objects"></a>Pobieranie odwołań do obiektów magazynu

Najpierw należy utworzyć wystąpienia obiektów używanych do uzyskiwania dostępu do Blob Storage i zarządzania nimi. Te obiekty bazują na siebie nawzajem. Każdy obiekt jest używany przez kolejny obiekt na liście.

- Utwórz wystąpienie obiektu [BlobService](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/BlobService) usługi Azure Storage, aby skonfigurować poświadczenia połączenia.
- Utwórz obiekt [kontenera](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container/Container) , który reprezentuje kontener, do którego uzyskujesz dostęp. Kontenery są używane do porządkowania obiektów blob w ten sam sposób, w jaki foldery na komputerze są używane do porządkowania plików.

Po utworzeniu obiektu kontenera można utworzyć [blokowy](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Block) obiekt BLOB, który wskazuje na konkretny obiekt BLOB, który Cię interesuje. Obiekt [bloku](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Block) służy do tworzenia, pobierania i kopiowania obiektów BLOB.

> [!IMPORTANT]
> Nazwy kontenerów muszą być zapisane małymi literami. Aby uzyskać więcej informacji na temat nazw kontenerów i obiektów blob, zobacz [nazywanie i odwoływanie się do kontenerów, obiektów blob i metadanych](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Następujący przykładowy kod:

- Tworzy nowy kontener
- Ustawia uprawnienia w kontenerze, aby obiekty blob były publiczne. Kontener ma nazwę *quickstartblobs* z dołączonym UNIKATOWYm identyfikatorem.

```ruby
# Create a container
container_name = "quickstartblobs" + SecureRandom.uuid
puts "\nCreating a container: " + container_name
container = blob_client.create_container(container_name)

# Set the permission so the blobs are public
blob_client.set_container_acl(container_name, "container")
```

### <a name="create-a-blob-in-the-container"></a>Tworzenie obiektu BLOB w kontenerze

Blob Storage obsługuje blokowe obiekty blob, Dodawanie obiektów blob i stronicowe obiekty blob. Aby utworzyć obiekt BLOB, wywołaj metodę [create_block_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#create_block_blob-instance_method) przekazującą dane dla obiektu BLOB.

Poniższy przykład tworzy obiekt BLOB o nazwie *QuickStart_* z UNIKATOWYm identyfikatorem i rozszerzeniem *txt* w utworzonym wcześniej kontenerze.

```ruby
# Create a new block blob containing 'Hello, World!'
blob_name = "QuickStart_" + SecureRandom.uuid + ".txt"
blob_data = "Hello, World!"
puts "\nCreating blob: " + blob_name
blob_client.create_block_blob(container.name, blob_name, blob_data)
```

Blokowe obiekty blob mogą mieć rozmiar do 4,7 TB i mogą być dowolne od arkuszy kalkulacyjnych do dużych plików wideo. Stronicowe obiekty blob są używane głównie dla plików VHD, które IaaSą maszyny wirtualne. Dołącz obiekty blob są często używane do rejestrowania, na przykład podczas zapisywania do pliku, a następnie Dodaj więcej informacji.

### <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Pobierz listę plików w kontenerze, używając metody [list_blobs](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container#list_blobs-instance_method). Poniższy kod pobiera listę obiektów blob, a następnie wyświetla ich nazwy.

```ruby
# List the blobs in the container
puts "\nList blobs in the container following continuation token"
nextMarker = nil
loop do
    blobs = blob_client.list_blobs(container_name, { marker: nextMarker })
    blobs.each do |blob|
        puts "\tBlob name: #{blob.name}"
    end
    nextMarker = blobs.continuation_token
    break unless nextMarker && !nextMarker.empty?
end
```

### <a name="download-a-blob"></a>Pobieranie obiektu blob

Pobierz obiekt BLOB do dysku lokalnego przy użyciu metody [get_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#get_blob-instance_method) . Poniższy kod pobiera obiekt BLOB utworzony w poprzedniej sekcji.

```ruby
# Download the blob

# Set the path to the local folder for downloading
if(is_windows)
    local_path = File.expand_path("~/Documents")
else 
    local_path = File.expand_path("~/")
end

# Create the full path to the downloaded file
full_path_to_file = File.join(local_path, blob_name)

puts "\nDownloading blob to " + full_path_to_file
blob, content = blob_client.get_blob(container_name, blob_name)
File.open(full_path_to_file,"wb") {|f| f.write(content)}
```

### <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli obiekt BLOB nie jest już wymagany, użyj [delete_blob](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob#delete_blob-instance_method) , aby go usunąć. Usuń cały kontener przy użyciu metody [delete_container](https://www.rubydoc.info/gems/azure-storage-blob/2.0.1/Azure/Storage/Blob/Container#delete_container-instance_method) . Usunięcie kontenera spowoduje również usunięcie wszystkich obiektów BLOB przechowywanych w kontenerze.

```ruby
# Clean up resources, including the container and the downloaded file
blob_client.delete_container(container_name)
File.delete(full_path_to_file)
```

## <a name="resources-for-developing-ruby-applications-with-blobs"></a>Zasoby używane do tworzenia aplikacji Ruby z obiektami blob

Zapoznaj się z tymi dodatkowymi zasobami na potrzeby programowania w języku Ruby:

- W witrynie GitHub wyświetl [kod źródłowy biblioteki klienta Ruby](https://github.com/Azure/azure-storage-ruby) dla usługi Azure Storage i pobierz go.
- Zapoznaj się z [przykładami platformy Azure](/samples/browse/?products=azure&languages=ruby) zapisanymi przy użyciu biblioteki klienta Ruby.
- [Przykład: Wprowadzenie z usługą Azure Storage w języku Ruby](https://github.com/Azure-Samples/storage-blob-ruby-getting-started)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób transferu plików między usługą Azure Blob Storage i dyskiem lokalnym przy użyciu języka Ruby. Aby dowiedzieć się więcej na temat pracy z Blob Storage, przejdź do omówienia konta magazynu.

> [!div class="nextstepaction"]
> [Omówienie kont magazynu](../common/storage-account-overview.md)

Aby uzyskać więcej informacji na temat Eksplorator usługi Storage i obiektów blob, zobacz [Zarządzanie zasobami BLOB Storage platformy Azure za pomocą Eksplorator usługi Storage](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
