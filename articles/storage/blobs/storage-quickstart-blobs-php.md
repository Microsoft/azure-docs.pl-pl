---
title: Przewodnik Szybki start platformy Azure — tworzenie obiektu blob w magazynie obiektów przy użyciu języka PHP | Microsoft Docs
description: Szybka nauka przesyłania obiektów do i z usługi Azure Blob Storage za pomocą języka PHP. Przekazywanie, pobieranie i wyświetlanie listy blokowych obiektów BLOB w kontenerze w usłudze Azure Blob Storage.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/14/2018
ms.service: storage
ms.subservice: blobs
ms.topic: quickstart
ms.openlocfilehash: af35927abfa491891dffe2c2397d6daf8c1ccde2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "95543123"
---
#  <a name="transfer-objects-tofrom-azure-blob-storage-using-php"></a>Transferowanie obiektów do i z usługi Azure Blob Storage za pomocą języka PHP
Dzięki tej skróconej instrukcji dowiesz się, w jaki sposób za pomocą języka PHP przekazywać, pobierać i wyświetlać listę blokowych obiektów blob w kontenerze usługi Azure Blob Storage. 

## <a name="prerequisites"></a>Wymagania wstępne

[!INCLUDE [storage-quickstart-prereq-include](../../../includes/storage-quickstart-prereq-include.md)]

Upewnij się, że masz zainstalowane następujące dodatkowe elementy wymagane wstępnie:

* [PHP](https://php.net/downloads.php)
* [Zestaw SDK usługi Azure Storage dla języka PHP](https://github.com/Azure/azure-storage-php)

## <a name="download-the-sample-application"></a>Pobieranie przykładowej aplikacji
[Przykładowa aplikacja](https://github.com/Azure-Samples/storage-blobs-php-quickstart.git) używana w tym przewodniku Szybki start to podstawowa aplikacja w języku PHP.  

Użyj narzędzia [git](https://git-scm.com/), aby pobrać kopię tej aplikacji do swojego środowiska projektowego. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-php-quickstart.git
```

To polecenie klonuje repozytorium do lokalnego folderu git. Aby otworzyć przykładową aplikację w języku PHP, wyszukaj folder storage-blobs-php-quickstart, a następnie otwórz plik phpqs.php.  

[!INCLUDE [storage-copy-account-key-portal](../../../includes/storage-copy-account-key-portal.md)]

## <a name="configure-your-storage-connection-string"></a>Konfigurowanie parametrów połączenia magazynu
W aplikacji należy podać nazwę konta magazynu i klucz konta, aby utworzyć wystąpienie obiektu **BlobRestProxy** na potrzeby aplikacji. Zaleca się do przechowywanie tych identyfikatorów w zmiennej środowiskowej na maszynie lokalnej, na której uruchomiona jest aplikacja. Użyj jednego z poniższych przykładów w zależności od używanego systemu operacyjnego, aby utworzyć zmienną środowiskową. Zastąp wartości **youraccountname** i **youraccountkey** własną nazwą konta i własnym kluczem.

# <a name="linux"></a>[Linux](#tab/linux)

```bash
export ACCOUNT_NAME=<youraccountname>
export ACCOUNT_KEY=<youraccountkey>
```

# <a name="windows"></a>[Windows](#tab/windows)

```cmd
setx ACCOUNT_NAME=<youraccountname>
setx ACCOUNT_KEY=<youraccountkey>
```
---

## <a name="configure-your-environment"></a>Konfigurowanie środowiska
Umieść lokalny folder git w katalogu udostępnionym przez serwer PHP. Następnie w tym samym katalogu otwórz wiersz polecenia i wprowadź: `php composer.phar install`

## <a name="run-the-sample"></a>Uruchamianie aplikacji przykładowej
Ta aplikacja przykładowa tworzy plik testowy w folderze „.”. Aplikacja przykładowa przesyła plik testowy do usługi Blob Storage, wyświetla listę obiektów blob w kontenerze i pobiera plik z nową nazwą. 

Uruchom przykład. Poniższej przedstawiono przykładowe dane wyjściowe zwracane po uruchomieniu aplikacji:
  
```
Uploading BlockBlob: HelloWorld.txt
These are the blobs present in the container: HelloWorld.txt: https://myexamplesacct.blob.core.windows.net/blockblobsleqvxd/HelloWorld.txt

This is the content of the blob uploaded: Hello Azure!
```
Po naciśnięciu wyświetlonego przycisku aplikacja przykładowa usunie kontener magazynu i pliki. Przed kontynuowaniem sprawdź, czy w folderze na serwerze znajdują się te dwa pliki. Możesz je otworzyć i sprawdzić, czy są identyczne.

Możesz również wyświetlić pliki w usłudze Blob Storage za pomocą narzędzia takiego jak [Eksplorator usługi Azure Storage](https://storageexplorer.com). Eksplorator usługi Azure Storage to darmowe narzędzie międzyplatformowe, które umożliwia dostęp do informacji na koncie magazynu. 

Po zweryfikowaniu plików naciśnij dowolny klawisz, aby zakończyć demonstrację i usunąć pliki testowe. Teraz, gdy wiesz już, jak działa aplikacja przykładowa, otwórz plik example.rb i przyjrzyj się kodowi. 

## <a name="understand-the-sample-code"></a>Omówienie przykładowego kodu

W kolejnej części omówimy przykładowy kod, aby wyjaśnić, w jaki sposób działa.

### <a name="get-references-to-the-storage-objects"></a>Pobieranie odwołań do obiektów magazynu
Najpierw należy utworzyć odwołania do obiektów używane w celu uzyskania dostępu do usługi Blob Storage i zarządzania nią. Te obiekty są powiązane i każdy obiekt jest używany przez kolejny na liście.

* Utwórz wystąpienie obiektu **BlobRestProxy** usługi Azure Storage, aby skonfigurować poświadczenia połączenia. 
* Utwórz obiekt **BlobService**, który wskazuje usługę Blob w ramach konta magazynu. 
* Utwórz obiekt **Container** reprezentujący kontener, do którego uzyskujesz dostęp. Kontenery są używane do porządkowania obiektów blob w ten sam sposób, w jaki foldery na komputerze są używane do porządkowania plików.

Gdy istnieje już obiekt kontenera **blobClient**, możesz utworzyć obiekt blob **Block** wskazujący konkretny obiekt blob, który Cię interesuje. Następnie możesz wykonać operacje, takie jak przekazywanie, pobieranie i kopiowanie.

> [!IMPORTANT]
> Nazwy kontenerów muszą być zapisane małymi literami. Aby uzyskać dodatkowe informacje o regułach nazewnictwa kontenerów i obiektów blob, zobacz [Nazewnictwo i odwoływanie się do kontenerów, obiektów blob i metadanych](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Ta sekcja poświęcona jest konfigurowaniu wystąpienia klienta usługi Azure Storage, tworzeniu wystąpienia obiektu usługi Blob, tworzeniu nowego kontenera i ustawianiu uprawnień w kontenerze, aby obiekty blob były publiczne. Kontener nazywa się **quickstartblobs**. 

```PHP
    # Setup a specific instance of an Azure::Storage::Client
    $connectionString = "DefaultEndpointsProtocol=https;AccountName=".getenv('account_name').";AccountKey=".getenv('account_key');
    
    // Create blob client.
    $blobClient = BlobRestProxy::createBlobService($connectionString);
    
    # Create the BlobService that represents the Blob service for the storage account
    $createContainerOptions = new CreateContainerOptions();
    
    $createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);
    
    // Set container metadata.
    $createContainerOptions->addMetaData("key1", "value1");
    $createContainerOptions->addMetaData("key2", "value2");

    $containerName = "blockblobs".generateRandomString();

    try    {
        // Create container.
        $blobClient->createContainer($containerName, $createContainerOptions);
```

### <a name="upload-blobs-to-the-container"></a>Przekazywanie obiektów blob do kontenera

Usługa Blob Storage obsługuje blokowe, uzupełnialne i stronicowe obiekty blob. Blokowe obiekty blob są używane najczęściej i dlatego zostały użyte w tym przewodniku Szybki start.  

Aby przekazać plik do obiektu blob, uzyskaj pełną ścieżkę pliku, łącząc nazwę katalogu i nazwę pliku na dysku lokalnym. Następnie możesz przekazać plik do określonej ścieżki przy użyciu metody **createBlockBlob()**. 

Przykładowy kod pobiera lokalny plik i przekazuje go do platformy Azure. W tym kodzie plik jest przechowywany pod nazwą **myfile**, a nazwa obiektu blob to **fileToUpload**. Następujący kod przykładowy przekazuje plik do kontenera o nazwie **quickstartblobs**.

```PHP
    $myfile = fopen("HelloWorld.txt", "w") or die("Unable to open file!");
    fclose($myfile);

    # Upload file as a block blob
    echo "Uploading BlockBlob: ".PHP_EOL;
    echo $fileToUpload;
    echo "<br />";
    
    $content = fopen($fileToUpload, "r");

    //Upload blob
    $blobClient->createBlockBlob($containerName, $fileToUpload, $content);
```

Aby wykonać częściową aktualizację zawartości blokowego obiektu blob, użyj metody **createblocklist()**. Blokowe obiekty blob mogą mieć rozmiar nawet do 4,7 TB i mogą to być dowolne pliki, od arkuszy kalkulacyjnych programu Excel po duże pliki wideo. Stronicowe obiekty blob są używane głównie do tworzenia plików VHD służących do obsługi maszyn wirtualnych IaaS. Uzupełnialne obiekty blob są używane do rejestrowania, na przykład w sytuacji, w której konieczny jest zapis do pliku, a następnie dodawanie kolejnych informacji. Uzupełnianego obiektu blob należy używać w ramach pojedynczego modelu zapisywania. Większość obiektów przechowywanych w usłudze Blob Storage to blokowe obiekty blob.

### <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Pobierz listę plików w kontenerze, używając metody **listBlobs()**. Poniższy kod umożliwia pobranie listy obiektów blob, a następnie przetwarza je w pętli, wyświetlając nazwy obiektów blob odnalezionych w kontenerze.  

```PHP
    $listBlobsOptions = new ListBlobsOptions();
    $listBlobsOptions->setPrefix("HelloWorld");
    
    echo "These are the blobs present in the container: ";
    
    do{
        $result = $blobClient->listBlobs($containerName, $listBlobsOptions);
        foreach ($result->getBlobs() as $blob)
        {
            echo $blob->getName().": ".$blob->getUrl()."<br />";
        }
        
        $listBlobsOptions->setContinuationToken($result->getContinuationToken());
    } while($result->getContinuationToken());
```

### <a name="get-the-content-of-your-blobs"></a>Pobieranie zawartości obiektów blob

Metoda **getBlob()** umożliwia pobranie zawartości obiektów blob. Poniższy kod pozwala wyświetlić zawartość obiektu blob przekazanego w poprzedniej sekcji.

```PHP
    $blob = $blobClient->getBlob($containerName, $fileToUpload);
    fpassthru($blob->getContentStream());
```

### <a name="clean-up-resources"></a>Czyszczenie zasobów
Jeśli nie potrzebujesz już obiektów blob przekazanych podczas pracy z tym przewodnikiem Szybki start, możesz usunąć cały kontener, korzystając z metody **deleteContainer()**. Jeśli utworzone pliki nie są już potrzebne, możesz użyć metody **deleteBlob()**, aby je usunąć.

```PHP
    // Delete blob.
    echo "Deleting Blob".PHP_EOL;
    echo $fileToUpload;
    echo "<br />";
    $blobClient->deleteBlob($_GET["containerName"], $fileToUpload);

    // Delete container.
    echo "Deleting Container".PHP_EOL;
    echo $_GET["containerName"].PHP_EOL;
    echo "<br />";
    $blobClient->deleteContainer($_GET["containerName"]);

    //Deleting local file
    echo "Deleting file".PHP_EOL;
    echo "<br />";
    unlink($fileToUpload);   
```

## <a name="resources-for-developing-php-applications-with-blobs"></a>Zasoby używane do tworzenia aplikacji PHP z obiektami blob

Zobacz dodatkowe zasoby używane podczas tworzenia aplikacji PHP z magazynem obiektów blob:

- W witrynie GitHub wyświetl [kod źródłowy biblioteki klienta PHP](https://github.com/Azure/azure-storage-php) dla usługi Azure Storage, a następnie pobierz i zainstaluj go.
- Zapoznaj się z [przykładami magazynu Blob Storage](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=php&term=blob) napisanymi przy użyciu biblioteki klienta PHP.

## <a name="next-steps"></a>Następne kroki
 
W tym przewodniku Szybki start przedstawiono metodę transferowania plików między dyskiem lokalnym i usługą Azure Blob Storage przy użyciu języka PHP. Aby dowiedzieć się więcej o pracy z językiem PHP, przejdź do naszego Centrum deweloperów języka PHP.

> [!div class="nextstepaction"]
> [Centrum deweloperów języka PHP](https://azure.microsoft.com/develop/php/)


Aby uzyskać więcej informacji na temat Eksploratora usługi Storage i obiektów blob, zapoznaj się artykułem [Manage Azure Blob storage resources with Storage Explorer](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) (Zarządzanie zasobami usługi Azure Blob Storage za pomocą Eksploratora usługi Storage).