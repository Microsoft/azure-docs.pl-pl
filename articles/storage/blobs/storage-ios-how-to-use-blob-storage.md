---
title: Jak używać magazynu obiektów (BLOB) z systemu iOS — Azure | Microsoft Docs
description: Przechowuj dane niestrukturalne w chmurze za pomocą Magazynu obiektów blob Azure.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 11/20/2018
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: 7fc1b2638c2ab17c4cd58ca8d4508d2e6d244cfa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95996823"
---
# <a name="how-to-use-blob-storage-from-ios"></a>Jak korzystać z usługi BLOB Storage z systemu iOS

W tym artykule pokazano, jak wykonywać typowe scenariusze przy użyciu Microsoft Azure magazynu obiektów BLOB. Przykłady są zapisywane w zamierzeniu C i korzystają z [biblioteki klienta usługi Azure Storage dla systemu iOS](https://github.com/Azure/azure-storage-ios). Omówione scenariusze obejmują przekazywanie, wyświetlanie, pobieranie i usuwanie obiektów BLOB. Aby uzyskać więcej informacji na temat obiektów blob, zobacz sekcję [następne kroki](#next-steps) . Możesz również pobrać [przykładową aplikację](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) , aby szybko zobaczyć, jak korzystać z usługi Azure Storage w aplikacji systemu iOS.

Aby dowiedzieć się więcej o usłudze BLOB Storage, zobacz [wprowadzenie do usługi Azure Blob Storage](storage-blobs-introduction.md).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Importowanie biblioteki systemu iOS usługi Azure Storage do aplikacji

Bibliotekę systemu iOS w usłudze Azure Storage można zaimportować do aplikacji przy użyciu [usługi Azure Storage CocoaPod](https://cocoapods.org/pods/AZSClient) lub importując plik **struktury** . CocoaPod jest zalecanym sposobem, w jaki ułatwia integrację biblioteki, jednak Importowanie z pliku struktury jest mniej inwazyjne dla istniejącego projektu.

Aby można było korzystać z tej biblioteki, potrzebne są następujące elementy:

- System iOS 8 +
- Xcode 7 +

## <a name="cocoapod"></a>CocoaPod

1. Jeśli jeszcze tego nie zrobiono, [Zainstaluj program CocoaPods](https://guides.cocoapods.org/using/getting-started.html#toc_3) na komputerze, otwierając okno terminalu i uruchamiając następujące polecenie

    ```shell
    sudo gem install cocoapods
    ```

2. Następnie w katalogu projektu (katalog zawierający plik. XCODEPROJ) Utwórz nowy plik o nazwie _plik podfile_(bez rozszerzenia pliku). Dodaj następujące elementy do _plik podfile_ i Save.

    ```ruby
    platform :ios, '8.0'

    target 'TargetName' do
      pod 'AZSClient'
    end
    ```

3. W oknie terminalu przejdź do katalogu projektu i uruchom następujące polecenie

    ```shell
    pod install
    ```

4. Jeśli plik. XCODEPROJ jest otwarty w Xcode, zamknij go. W katalogu projektu Otwórz nowo utworzony plik projektu, który będzie miał rozszerzenie. xcworkspace. Jest to plik, od którego będziesz teraz korzystać.

## <a name="framework"></a>Framework

Innym sposobem korzystania z biblioteki jest ręczne skompilowanie struktury:

1. Najpierw pobierz lub Sklonuj [repozytorium Azure-Storage-iOS](https://github.com/azure/azure-storage-ios).
2. Przejdź do biblioteki klienta usługi Azure Storage dla *systemu iOS*  ->    ->  i otwórz ją `AZSClient.xcodeproj` w Xcode.
3. W lewym górnym rogu Xcode Zmień aktywny schemat z "Biblioteka klienta usługi Azure Storage" na "Framework".
4. Kompiluj projekt (⌘ + B). Spowoduje to utworzenie `AZSClient.framework` pliku na pulpicie.

Następnie można zaimportować plik struktury do aplikacji, wykonując następujące czynności:

1. Utwórz nowy projekt lub Otwórz istniejący projekt w Xcode.
2. Przeciągnij i upuść `AZSClient.framework` do swojego nawigatora projektu Xcode.
3. W *razie konieczności wybierz opcję Kopiuj elementy*, a następnie kliknij przycisk *Zakończ*.
4. Kliknij projekt w okienku nawigacji po lewej stronie i kliknij kartę *Ogólne* w górnej części edytora projektu.
5. W sekcji *połączone struktury i biblioteki* kliknij przycisk Dodaj (+).
6. Na liście już podanych bibliotek Wyszukaj `libxml2.2.tbd` i Dodaj ją do projektu.

## <a name="import-the-library"></a>Importuj bibliotekę

```objc
// Include the following import statement to use blob APIs.
#import <AZSClient/AZSClient.h>
```

Jeśli używasz SWIFT, musisz utworzyć nagłówek mostkowania i zaimportować go \<AZSClient/AZSClient.h> :

1. Utwórz plik nagłówka `Bridging-Header.h` i Dodaj powyższą instrukcję importu.
2. Przejdź do karty *Ustawienia kompilacji* i Wyszukaj *nagłówek "z mostkiem C"*.
3. Kliknij dwukrotnie pole *nagłówka "cel-C"* , a następnie dodaj ścieżkę do pliku nagłówkowego: `ProjectName/Bridging-Header.h`
4. Skompiluj projekt (⌘ + B), aby sprawdzić, czy nagłówek mostkowania został pobrany przez Xcode.
5. Zacznij korzystać z biblioteki bezpośrednio w dowolnym pliku SWIFT, nie ma potrzeby stosowania instrukcji import.

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>Operacje asynchroniczne

> [!NOTE]
> Wszystkie metody, które wykonują żądanie w odniesieniu do usługi, są operacjami asynchronicznymi. W przykładach kodu znajdziesz, że te metody mają procedurę obsługi uzupełniania. Kod wewnątrz procedury obsługi uzupełniania zostanie uruchomiony **po** zakończeniu żądania. Kod po zakończeniu procedury obsługi zostanie uruchomiony **podczas** wykonywania żądania.

## <a name="create-a-container"></a>Tworzenie kontenera

Każdy obiekt BLOB w usłudze Azure Storage musi znajdować się w kontenerze. Poniższy przykład pokazuje, jak utworzyć kontener o nazwie *newcontainer* w swoim koncie magazynu, jeśli jeszcze nie istnieje. Po wybraniu nazwy kontenera należy zanotować powyższe zasady nazewnictwa.

```objc
-(void)createContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

Możesz potwierdzić, że to działa, przeglądając [Eksplorator usługi Microsoft Azure Storage](https://storageexplorer.com) i sprawdzając, czy *newcontainer* znajduje się na liście kontenerów dla konta magazynu.

## <a name="set-container-permissions"></a>Ustawianie uprawnień kontenera

Uprawnienia kontenera są domyślnie skonfigurowane na potrzeby dostępu **prywatnego** . Kontenery zapewniają jednak kilka różnych opcji dostępu do kontenera:

- **Prywatne**: dane kontenera i obiektu BLOB mogą być odczytywane tylko przez właściciela konta.
- **Obiekt BLOB**: dane obiektów BLOB w tym kontenerze można odczytać za pośrednictwem anonimowego żądania, ale dane kontenera nie są dostępne. Klienci nie mogą wyliczyć obiektów BLOB w kontenerze za pośrednictwem anonimowego żądania.
- **Kontener**: dane kontenera i obiektu BLOB mogą być odczytywane za pośrednictwem anonimowego żądania. Klienci mogą wyliczać obiekty blob w kontenerze za pośrednictwem żądania anonimowego, ale nie mogą wyliczać kontenerów na koncie magazynu.

Poniższy przykład pokazuje, jak utworzyć kontener z uprawnieniami dostępu do **kontenera** , które umożliwią publiczny dostęp tylko do odczytu dla wszystkich użytkowników w Internecie:

```objc
-(void)createContainerWithPublicAccess{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

## <a name="upload-a-blob-into-a-container"></a>Przekazywanie obiektu blob do kontenera

Jak wspomniano w sekcji pojęcia Blob service, Blob Storage oferuje trzy różne typy obiektów blob: blokowe obiekty blob, dołączanie obiektów blob i stronicowe obiekty blob. Biblioteka systemu iOS w usłudze Azure Storage obsługuje wszystkie trzy typy obiektów BLOB. W większości przypadków zalecane jest użycie blokowych obiektów blob.

Poniższy przykład pokazuje, jak przekazać blokowy obiekt BLOB z NSString. Jeśli obiekt BLOB o takiej samej nazwie już istnieje w tym kontenerze, zawartość tego obiektu BLOB zostanie zastąpiona.

```objc
-(void)uploadBlobToContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
        {
            if (error){
                NSLog(@"Error in creating container.");
            }
            else{
                // Create a local blob object
                AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

                // Upload blob to Storage
                [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                    if (error){
                        NSLog(@"Error in creating blob.");
                    }
                }];
            }
        }];
}
```

Można potwierdzić, że to działa, przeglądając [Eksplorator usługi Microsoft Azure Storage](https://storageexplorer.com) i sprawdzając, czy kontener, *containerpublic*, zawiera obiekt BLOB, *sampleblob*. W tym przykładzie użyto publicznego kontenera, więc można także sprawdzić, czy aplikacja działa, przechodząc do identyfikatora URI obiektów blob:

```http
https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob
```

Oprócz przekazywania blokowych obiektów blob z NSString, podobne metody istnieją dla NSData, NSInputStream lub pliku lokalnego.

## <a name="list-the-blobs-in-a-container"></a>Wyświetlanie listy obiektów blob w kontenerze

Poniższy przykład pokazuje, jak wyświetlić listę wszystkich obiektów BLOB w kontenerze. Podczas wykonywania tej operacji należy mieć na uwadze następujące parametry:

- **continuationToken** — token kontynuacji reprezentuje miejsce, w którym powinna zostać uruchomiona operacja tworzenia listy. Jeśli nie podano żadnego tokenu, wyświetli obiekty blob od początku. Dowolna liczba obiektów BLOB może zostać wyświetlona, od zera do zestawu. Nawet jeśli ta metoda zwróci wynik zero, jeśli nie jest równa `results.continuationToken` Nil, może istnieć więcej obiektów BLOB w usłudze, które nie zostały wymienione na liście.
- **prefiks** — można określić prefiks, który ma być używany na potrzeby listy obiektów BLOB. Zostaną wyświetlone tylko obiekty blob zaczynające się od tego prefiksu.
- **useFlatBlobListing** — jak wspomniano w sekcji [nazywanie i kontenery odwołań oraz obiektów blob](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) , chociaż BLOB Service jest schematem magazynu prostego, można utworzyć hierarchię wirtualną przez nadanie nazw obiektów blob z informacjami o ścieżce. Jednak niepłaska lista nie jest obecnie obsługiwana. Ta funkcja jest dostępna wkrótce. Na razie ta wartość powinna być równa **tak**.
- **blobListingDetails** — możesz określić, które elementy mają być uwzględniane podczas wyświetlania listy obiektów BLOB
  - _AZSBlobListingDetailsNone_: Wyświetl tylko przekazane obiekty blob i nie zwracaj metadanych obiektów BLOB.
  - _AZSBlobListingDetailsSnapshots_: wyświetlenie listy zatwierdzonych obiektów blob i migawek obiektów BLOB.
  - _AZSBlobListingDetailsMetadata_: Pobierz metadane obiektu BLOB dla każdego obiektu BLOB zwróconego na liście.
  - _AZSBlobListingDetailsUncommittedBlobs_: wyświetlenie listy zatwierdzonych i niezatwierdzonych obiektów BLOB.
  - _AZSBlobListingDetailsCopy_: Dołącz właściwości kopiowania na liście.
  - _AZSBlobListingDetailsAll_: Wyświetla listę wszystkich dostępnych obiektów blob, niezatwierdzonych obiektów blob i migawek oraz zwraca wszystkie metadane i stan kopiowania dla tych obiektów BLOB.
- **maxResults** — Maksymalna liczba wyników do zwrócenia dla tej operacji. Użyj wartości-1, aby nie ustawić limitu.
- **completionHandler** — blok kodu do wykonania z wynikami operacji tworzenia listy.

W tym przykładzie metoda pomocnika jest używana do rekursywnego wywoływania metody list obiektów BLOB za każdym razem, gdy zwracany jest token kontynuacji.

```objc
-(void)listBlobsInContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    //List all blobs in container
    [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
        if (error != nil){
            NSLog(@"Error in creating container.");
        }
    }];
}

//List blobs helper method
-(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
{
    [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
        if (error)
        {
            completionHandler(error);
        }
        else
        {
            for (int i = 0; i < results.blobs.count; i++) {
                NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
            }
            if (results.continuationToken)
            {
                [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
            }
            else
            {
                completionHandler(nil);
            }
        }
    }];
}
```

## <a name="download-a-blob"></a>Pobieranie obiektu blob

Poniższy przykład pokazuje, jak pobrać obiekt BLOB do obiektu NSString.

```objc
-(void)downloadBlobToString{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

    // Download blob
    [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
        if (error) {
            NSLog(@"Error in downloading blob");
        }
        else{
            NSLog(@"%@",text);
        }
    }];
}
```

## <a name="delete-a-blob"></a>Usuwanie obiektu blob

Poniższy przykład pokazuje, jak usunąć obiekt BLOB.

```objc
-(void)deleteBlob{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

    // Delete blob
    [blockBlob deleteWithCompletionHandler:^(NSError *error) {
        if (error) {
            NSLog(@"Error in deleting blob.");
        }
    }];
}
```

## <a name="delete-a-blob-container"></a>Usuwanie kontenera obiektów BLOB

Poniższy przykład pokazuje, jak usunąć kontener.

```objc
-(void)deleteContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Delete container
    [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
        if(error){
            NSLog(@"Error in deleting container");
        }
    }];
}
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak korzystać z Blob Storage z systemu iOS, Skorzystaj z poniższych linków, aby dowiedzieć się więcej na temat biblioteki systemu iOS i usługi magazynu.

- [Biblioteka klienta usługi Azure Storage dla systemu iOS](https://github.com/azure/azure-storage-ios)
- [Dokumentacja dotycząca systemu iOS w usłudze Azure Storage](https://azure.github.io/azure-storage-ios/)
- [Interfejs API REST usług Azure Storage](/rest/api/storageservices/)
- [Blog zespołu usługi Azure Storage](/archive/blogs/windowsazurestorage/)

Jeśli masz pytania dotyczące tej biblioteki, możesz bezpłatnie ogłosić na naszej stronie pytania i [Stack Overflow](https://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files)w [firmie Microsoft Q&](/answers/topics/azure-blob-storage.html) .
Jeśli masz sugestie dotyczące funkcji usługi Azure Storage, Opublikuj je w usłudze [Azure Storage](https://feedback.azure.com/forums/217298-storage/).