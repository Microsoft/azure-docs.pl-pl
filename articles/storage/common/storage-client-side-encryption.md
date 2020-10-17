---
title: Client-Side szyfrowanie przy użyciu platformy .NET dla Microsoft Azure Storage | Microsoft Docs
description: Biblioteka klienta usługi Azure Storage dla platformy .NET obsługuje szyfrowanie po stronie klienta i integrację z usługą Azure Key Vault, aby uzyskać maksymalne zabezpieczenia aplikacji usługi Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/20/2017
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 4e8623ecb351fa99a437de70a9b74a70fb6228cd
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92151145"
---
# <a name="client-side-encryption-and-azure-key-vault-for-microsoft-azure-storage"></a>Client-Side szyfrowanie i Azure Key Vault Microsoft Azure Storage
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Omówienie
[Biblioteka klienta usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage) obsługuje szyfrowanie danych w aplikacjach klienckich przed przekazaniem ich do usługi Azure Storage i odszyfrowywanie danych podczas pobierania ich do klienta. Biblioteka obsługuje także integrację z usługą [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dla zarządzania kluczami konta magazynu.

Aby zapoznać się z samouczkiem krok po kroku, który przeprowadzi Cię przez proces szyfrowania obiektów BLOB przy użyciu funkcji szyfrowania po stronie klienta i Azure Key Vault, zobacz artykuł [szyfrowanie i odszyfrowywanie obiektów BLOB w Microsoft Azure Storage przy użyciu Azure Key Vault](../blobs/storage-encrypt-decrypt-blobs-key-vault.md).

Aby uzyskać szyfrowanie po stronie klienta przy użyciu języka Java, zapoznaj się z tematem [szyfrowanie po stronie klienta za pomocą języka Java dla Microsoft Azure Storage](storage-client-side-encryption-java.md).

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Szyfrowanie i odszyfrowywanie za pomocą techniki Envelope
Procesy szyfrowania i odszyfrowywania są zgodne z techniką formy.

### <a name="encryption-via-the-envelope-technique"></a>Szyfrowanie za pośrednictwem techniki Envelope
Szyfrowanie za pomocą techniki Envelope działa w następujący sposób:

1. Biblioteka klienta usługi Azure Storage generuje klucz szyfrowania zawartości (CEK), który jest jednorazowym użyciem klucza symetrycznego.
2. Dane użytkownika są szyfrowane przy użyciu tego CEK.
3. CEK jest następnie opakowany (zaszyfrowany) przy użyciu klucza szyfrowania klucza (KEK). KEK jest identyfikowany przez identyfikator klucza i może być parę kluczy asymetrycznych lub kluczem symetrycznym i może być zarządzany lokalnie lub przechowywany w magazynach kluczy Azure.
   
    Sama Biblioteka klienta magazynu nigdy nie ma dostępu do KEK. Biblioteka wywołuje algorytm zawijania kluczy, który jest dostarczany przez Key Vault. Użytkownicy mogą zdecydować się na użycie niestandardowych dostawców do zawijania kluczy/rozpakowywania w razie potrzeby.

4. Zaszyfrowane dane są następnie przekazywane do usługi Azure Storage. Opakowany klucz wraz z dodatkowymi metadanymi szyfrowania jest przechowywany jako metadane (na obiekcie BLOB) lub interpolowany z zaszyfrowanymi danymi (kolejkowanie komunikatów i obiekty tabeli).

### <a name="decryption-via-the-envelope-technique"></a>Odszyfrowywanie za pomocą techniki Envelope
Odszyfrowywanie za pomocą techniki Envelope działa w następujący sposób:

1. W bibliotece klienta założono, że użytkownik zarządza kluczem szyfrowania kluczy (KEK) lokalnie lub w magazynach kluczy platformy Azure. Użytkownik nie musi znać określonego klucza, który był używany do szyfrowania. Zamiast tego można skonfigurować i użyć programu rozpoznawania kluczy, który rozwiązuje różne identyfikatory kluczy do kluczy.
2. Biblioteka klienta pobiera zaszyfrowane dane wraz z dowolnym materiałem szyfrowania, który jest przechowywany w usłudze.
3. Klucz szyfrowania zawartości opakowanej (CEK) jest następnie odpakowany (odszyfrowany) przy użyciu klucza szyfrowania klucza (KEK). W tym miejscu Biblioteka kliencka nie ma dostępu do KEK. Po prostu wywołuje algorytm unotoką niestandardowego lub Key Vault dostawcy.
4. Klucz szyfrowania zawartości (CEK) jest następnie używany do odszyfrowywania zaszyfrowanych danych użytkownika.

## <a name="encryption-mechanism"></a>Mechanizm szyfrowania
Biblioteka klienta magazynu używa [algorytmu AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) w celu szyfrowania danych użytkownika. W przypadku trybu [szyfrowania bloku blokowego (CBC)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) z algorytmem AES. Każda usługa działa nieco inaczej, więc omawiamy każdą z nich w tym miejscu.

### <a name="blobs"></a>Obiekty blob
Biblioteka klienta obsługuje obecnie szyfrowanie tylko całych obiektów BLOB. W przypadku plików do pobrania obsługiwane są zarówno pobieranie kompletne, jak i zakres.

Podczas szyfrowania Biblioteka klienta generuje losowy wektor inicjacji (IV) z 16 bajtów wraz z losowym kluczem szyfrowania zawartości (CEK) wynoszącym 32 bajtów i przeprowadź szyfrowanie koperty danych obiektów BLOB przy użyciu tych informacji. Opakowany CEK i niektóre dodatkowe metadane szyfrowania są następnie przechowywane jako metadane obiektów BLOB wraz z zaszyfrowanego obiektu BLOB w usłudze.

> [!WARNING]
> Jeśli edytujesz lub przekazujesz własne metadane dla obiektu BLOB, musisz upewnić się, że metadane są zachowywane. Jeśli załadujesz nowe metadane bez tych metadanych, opakowane CEK, IV i inne metadane zostaną utracone i nie będzie można ponownie pobrać zawartości obiektu BLOB.
> 
> 

Podczas pobierania całego obiektu BLOB opakowany CEK jest rozpakowany i używany razem z IV (przechowywane jako metadane obiektu BLOB w tym przypadku) w celu zwrócenia odszyfrowanych danych do użytkowników.

Pobieranie dowolnego zakresu w zaszyfrowanym obiekcie blob obejmuje dostosowanie zakresu zapewnianego przez użytkowników w celu uzyskania niewielkiej ilości dodatkowych danych, których można użyć do pomyślnego odszyfrowania żądanego zakresu.

Wszystkie typy obiektów BLOB (blokowe obiekty blob, stronicowe obiekty blob i dołączane obiekty blob) mogą być szyfrowane/odszyfrowywane przy użyciu tego schematu.

### <a name="queues"></a>Kolejki
Ponieważ komunikaty kolejki mogą mieć dowolny format, Biblioteka klienta definiuje format niestandardowy, który zawiera wektor inicjujący (IV) i zaszyfrowany klucz szyfrowania zawartości (CEK) w tekście komunikatu.

Podczas szyfrowania Biblioteka klienta generuje losową część IV 16 bajtów wraz z losową CEKą 32 bajtów i wykonuje szyfrowanie koperty tekstu komunikatu w kolejce przy użyciu tych informacji. Opakowany CEK i dodatkowe metadane szyfrowania są następnie dodawane do komunikatu zaszyfrowanej kolejki. Ten zmodyfikowany komunikat (przedstawiony poniżej) jest przechowywany w usłudze.

```xml
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

Podczas odszyfrowywania, opakowany klucz jest wyodrębniany z komunikatu kolejki i nieopakowany. IV jest również wyodrębniany z komunikatu kolejki i używany razem z nieopakowanym kluczem do odszyfrowania danych komunikatu w kolejce. Należy pamiętać, że metadane szyfrowania są małe (poniżej 500 bajtów), więc w miarę jak licznik KB dla komunikatu kolejki, wpływ powinien być zarządzany. Należy pamiętać, że zaszyfrowany komunikat będzie kodowany algorytmem Base64, jak pokazano w powyższym fragmencie kodu, co spowoduje również powiększenie rozmiaru wysyłanej wiadomości.

### <a name="tables"></a>Tabele
> [!NOTE]
> Table service jest obsługiwana w bibliotece klienta usługi Azure Storage tylko w wersji 9. x.
> 
> 

Biblioteka klienta obsługuje szyfrowanie właściwości jednostki dla operacji wstawiania i zamiany.

> [!NOTE]
> Scalanie nie jest obecnie obsługiwane. Ponieważ podzbiór właściwości mógł zostać zaszyfrowany wcześniej przy użyciu innego klucza, po prostu scalanie nowych właściwości i aktualizowanie metadanych spowoduje utratę danych. Scalanie wymaga wykonania dodatkowych wywołań usługi do odczytu istniejącej jednostki z usługi lub użycia nowego klucza dla każdej właściwości, co nie jest odpowiednie ze względu na wydajność.
> 
> 

Szyfrowanie danych tabeli działa w następujący sposób:  

1. Użytkownicy określają właściwości, które mają być szyfrowane.
2. Biblioteka klienta generuje losowy wektor inicjacji (IV) z 16 bajtów wraz z losowym kluczem szyfrowania zawartości (CEK) wynoszącym 32 bajtów dla każdej jednostki i wykonuje szyfrowanie kopert dla poszczególnych właściwości, aby były szyfrowane przez wygenerowanie nowej wartości IV na właściwość. Zaszyfrowana właściwość jest przechowywana jako dane binarne.
3. Opakowany CEK i niektóre dodatkowe metadane szyfrowania są następnie przechowywane jako dwie dodatkowe zastrzeżone właściwości. Pierwsza zastrzeżona Właściwość (_ClientEncryptionMetadata1) jest właściwością ciągu, która zawiera informacje o IV, wersji i kluczu opakowanym. Druga Właściwość zastrzeżona (_ClientEncryptionMetadata2) jest właściwością binarną, która zawiera informacje o zaszyfrowanych właściwościach. Informacje w tej drugiej właściwości (_ClientEncryptionMetadata2) są zaszyfrowane.
4. Ze względu na te dodatkowe właściwości zastrzeżone wymagane do szyfrowania, użytkownicy mogą teraz mieć tylko 250 właściwości niestandardowe zamiast 252. Łączny rozmiar jednostki musi być mniejszy niż 1 MB.

Należy pamiętać, że tylko właściwości ciągu mogą być szyfrowane. Jeśli inne typy właściwości mają być szyfrowane, muszą być konwertowane na ciągi. Zaszyfrowane ciągi są przechowywane w usłudze jako właściwości binarne i są konwertowane z powrotem do ciągów po odszyfrowaniu.

W przypadku tabel oprócz zasad szyfrowania użytkownicy muszą określić właściwości, które mają być szyfrowane. Można to zrobić przez określenie atrybutu [EncryptProperty] (dla jednostek POCO, które pochodzą z klasy tableentity) lub mechanizmu rozwiązywania konfliktów w opcjach żądania. Program rozpoznawania szyfrowania jest delegatem, który pobiera klucz partycji, klucz wiersza i nazwę właściwości i zwraca wartość logiczną wskazującą, czy ta właściwość powinna być szyfrowana. Podczas szyfrowania Biblioteka kliencka będzie używać tych informacji do decydowania, czy właściwość powinna być szyfrowana podczas zapisywania w sieci. Delegat udostępnia również możliwość logiki dotyczącej sposobu szyfrowania właściwości. (Na przykład, jeśli X, wówczas Zaszyfruj Właściwość A; w przeciwnym razie Szyfruj właściwości a i B). Należy pamiętać, że nie trzeba podawać tych informacji podczas odczytywania lub wysyłania zapytań do jednostek.

### <a name="batch-operations"></a>Operacje wsadowe
W operacjach wsadowych ta sama KEK będzie używana we wszystkich wierszach tej operacji wsadowej, ponieważ Biblioteka klienta zezwala tylko na jeden obiekt opcji (a tym samym zasady/KEK) na operację wsadową. Jednak Biblioteka klienta będzie wewnętrznie generować nowe losowe CEK IV i losowe na wiersz w partii. Użytkownicy mogą również zaszyfrować różne właściwości dla każdej operacji w partii przez zdefiniowanie tego zachowania w programie rozpoznawania nazw.

### <a name="queries"></a>Zapytania
> [!NOTE]
> Ponieważ jednostki są zaszyfrowane, nie można uruchamiać zapytań, które filtrują zaszyfrowaną właściwość.  Jeśli spróbujesz, wyniki będą nieprawidłowe, ponieważ usługa próbuje porównać zaszyfrowane dane z niezaszyfrowanymi danymi.
> 
> 
> Aby wykonać operacje zapytania, należy określić program rozpoznawania kluczy, który może rozpoznać wszystkie klucze w zestawie wyników. Jeśli nie można rozpoznać jednostki zawartej w wyniku zapytania jako dostawcy, Biblioteka klienta zgłosi błąd. Dla każdego zapytania, które wykonuje projekcje po stronie serwera, Biblioteka klienta doda specjalne właściwości metadanych szyfrowania (_ClientEncryptionMetadata1 i _ClientEncryptionMetadata2) do wybranych kolumn.

## <a name="azure-key-vault"></a>W usłudze Azure Key Vault
Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Za pomocą Azure Key Vault użytkownicy mogą szyfrować klucze i wpisy tajne (takie jak klucze uwierzytelniania, klucze konta magazynu, klucze szyfrowania danych). Pliki i hasła PFX) przy użyciu kluczy chronionych przez sprzętowe moduły zabezpieczeń (sprzętowych modułów zabezpieczeń). Aby uzyskać więcej informacji, zobacz [Co to jest usługa Azure Key Vault?](../../key-vault/general/overview.md).

Biblioteka klienta usługi Storage używa interfejsów Key Vault w bibliotece podstawowej, aby zapewnić wspólną platformę na platformie Azure na potrzeby zarządzania kluczami. Użytkownicy mogą korzystać z bibliotek Key Vault, aby uzyskać dodatkowe korzyści, które zapewnia, takich jak przydatne funkcje dotyczące prostych i bezproblemowych i niezawodnych, symetrycznych i zarówno lokalnych, jak i niewygodnych dostawców kluczy w chmurze, a także pomoc przy agregacji i

### <a name="interface-and-dependencies"></a>Interfejs i zależności

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

Istnieją dwa niezbędne pakiety do integracji Key Vault:

* Platforma Azure. Core zawiera `IKeyEncryptionKey` `IKeyEncryptionKeyResolver` interfejsy i. Biblioteka klienta usługi Storage dla platformy .NET definiuje już ją jako zależność.
* Azure. Security. Keys. klucze (v4. x) zawierają Key Vault klienta REST, a także klientów kryptograficznych używanych z szyfrowaniem po stronie klienta.

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

Istnieją trzy Key Vault pakiety:

* Microsoft. Azure. kluczowe magazyn. Core zawiera IKey i IKeyResolver. Jest to mały pakiet bez zależności. Biblioteka klienta usługi Storage dla programu .NET definiuje ją jako zależność.
* Magazyn Microsoft. Azure. kluczy (v3. x) zawiera Key Vault klienta REST.
* Microsoft. Azure. RSAKey. Extensions (v3. x) zawiera kod rozszerzenia zawierający implementacje algorytmów kryptograficznych i oraz SymmetricKey. Jest ona zależna od podstawowych i przestrzeni nazw magazynu kluczy i oferuje funkcje do definiowania zagregowanego programu rozpoznawania nazw (gdy użytkownicy chcą korzystać z wielu dostawców kluczy) i rozpoznawania klucza buforowania. Mimo że Biblioteka klienta usługi Storage nie zależy bezpośrednio od tego pakietu, jeśli użytkownicy chcą używać Azure Key Vault do przechowywania swoich kluczy lub używania rozszerzeń Key Vault do korzystania z dostawców usług kryptograficznych lokalnych i w chmurze, będą oni potrzebować tego pakietu.

Więcej informacji dotyczących użycia Key Vault w v11 można znaleźć w [przykładach kodu szyfrowania v11](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples).

---

Key Vault jest zaprojektowana dla kluczy głównych o wysokiej wartości, a limity ograniczania dla Key Vault są z tego względu zaprojektowane. Podczas wykonywania szyfrowania po stronie klienta przy użyciu Key Vault, preferowanym modelem jest użycie symetrycznego klucza głównego przechowywanych jako wpisy tajne w Key Vault i w pamięci podręcznej lokalnie. Użytkownicy muszą wykonać następujące czynności:

1. Utwórz klucz tajny w trybie offline i przekaż go do Key Vault.
2. Użyj identyfikatora podstawowego wpisu tajnego jako parametru, aby rozwiązać bieżącą wersję klucza tajnego w celu szyfrowania, a następnie Buforuj te informacje lokalnie. Użyj CachingKeyResolver do buforowania; Użytkownicy nie oczekują implementacji własnej logiki buforowania.
3. Użyj mechanizmu rozwiązywania konfliktów jako danych wejściowych podczas tworzenia zasad szyfrowania.

## <a name="best-practices"></a>Najlepsze rozwiązania
Obsługa szyfrowania jest dostępna tylko w bibliotece klienta usługi Storage dla platformy .NET. Windows Phone i środowisko wykonawcze systemu Windows nie obsługują obecnie szyfrowania.

> [!IMPORTANT]
> Należy pamiętać o tych ważnych kwestiach podczas korzystania z szyfrowania po stronie klienta:
> 
> * Podczas odczytywania lub zapisywania do zaszyfrowanego obiektu BLOB używaj pełnych poleceń przekazywania obiektów blob oraz poleceń pobierania zakresu/całego obiektu BLOB. Unikaj zapisywania do zaszyfrowanego obiektu BLOB przy użyciu operacji protokołu, takich jak Put Block, Put Block list, Write Pages, Clear Pages lub Append Block; w przeciwnym razie może dojść do uszkodzenia zaszyfrowanego obiektu BLOB i uniemożliwić jego odczytanie.
> * W przypadku tabel istnieje podobne ograniczenie. Należy zachować ostrożność, aby nie aktualizować zaszyfrowanych właściwości bez aktualizowania metadanych szyfrowania.
> * Jeśli ustawisz metadane dla zaszyfrowanego obiektu BLOB, możesz zastąpić metadane związane z szyfrowaniem wymagane do odszyfrowania, ponieważ metadane ustawień nie są addytywne. Dotyczy to również migawek; należy unikać określania metadanych podczas tworzenia migawki zaszyfrowanego obiektu BLOB. Jeśli metadane muszą być ustawione, należy najpierw wywołać metodę **FetchAttributes** , aby pobrać bieżące metadane szyfrowania i uniknąć jednoczesnego zapisu podczas ustawiania metadanych.
> * Włącz Właściwość **RequireEncryption** w domyślnych opcjach żądania dla użytkowników, którzy powinni korzystać tylko z szyfrowanych danych. Aby uzyskać więcej informacji, zobacz poniżej.
>
>

## <a name="client-api--interface"></a>API/interfejs klienta
Użytkownicy mogą udostępniać tylko klucz, tylko program rozpoznawania nazw lub oba te elementy. Klucze są identyfikowane przy użyciu identyfikatora klucza i udostępniają logikę zawijania/rozpakowywania. Podczas procesu odszyfrowywania są używane rozwiązania rozpoznawania kluczy. Definiuje metodę rozwiązywania, która zwraca klucz z danym identyfikatorem klucza. Dzięki temu użytkownicy mogą wybierać między wieloma kluczami, które są zarządzane w wielu lokalizacjach.

* W przypadku szyfrowania klucz jest używany zawsze, a brak klucza spowoduje wystąpienie błędu.
* W przypadku odszyfrowywania:
  * Jeśli klucz jest określony, a jego identyfikator jest zgodny z wymaganym identyfikatorem klucza, ten klucz jest używany do odszyfrowywania. W przeciwnym razie zostanie podjęta próba rozpoznawania. Jeśli dla tej próby nie ma żadnego mechanizmu rozwiązywania konfliktów, zostanie zgłoszony błąd.
  * Program rozpoznawania kluczy jest wywoływany, jeśli jest określony w celu pobrania klucza. Jeśli jest określony mechanizm rozwiązywania konfliktów, ale nie ma mapowania dla identyfikatora klucza, zostanie zgłoszony błąd.

### <a name="requireencryption-mode-v11-only"></a>Tryb RequireEncryption (tylko v11)
Użytkownicy mogą opcjonalnie włączyć tryb operacji, w której wszystkie operacje przekazywania i pobierania muszą być szyfrowane. W tym trybie program próbuje przekazać dane bez zasad szyfrowania lub pobrać dane, które nie są zaszyfrowane w usłudze, zakończą się niepowodzeniem na kliencie. Właściwość **RequireEncryption** obiektu Opcje żądania steruje tym zachowaniem. Jeśli aplikacja będzie szyfrować wszystkie obiekty przechowywane w usłudze Azure Storage, można ustawić właściwość **RequireEncryption** w domyślnej opcji żądania dla obiektu klienta usługi. Na przykład ustaw **CloudBlobClient. DefaultRequestOptions. RequireEncryption** na **true** , aby wymagać szyfrowania dla wszystkich operacji obiektu BLOB wykonywanych za poorednictwem tego obiektu klienta.


### <a name="blob-service-encryption"></a>Szyfrowanie Blob service


# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)
Utwórz obiekt **ClientSideEncryptionOptions** i ustaw go na tworzenie klienta z **SpecializedBlobClientOptions**. Nie można ustawić opcji szyfrowania dla poszczególnych interfejsów API. Wszystkie inne elementy będą obsługiwane przez bibliotekę kliencką wewnętrznie.

```csharp
// Your key and key resolver instances, either through KeyVault SDK or an external implementation
IKeyEncryptionKey key;
IKeyEncryptionKeyResolver keyResolver;

// Create the encryption options to be used for upload and download.
ClientSideEncryptionOptions encryptionOptions = new ClientSideEncryptionOptions(ClientSideEncryptionVersion.V1_0)
{
   KeyEncryptionKey = key,
   KeyResolver = keyResolver,
   // string the storage client will use when calling IKeyEncryptionKey.WrapKey()
   KeyWrapAlgorithm = "some algorithm name"
};

// Set the encryption options on the client options
BlobClientOptions options = new SpecializedBlobClientOptions() { ClientSideEncryption = encryptionOptions };

// Get your blob client with client-side encryption enabled.
// Client-side encryption options are passed from service to container clients, and container to blob clients.
// Attempting to construct a BlockBlobClient, PageBlobClient, or AppendBlobClient from a BlobContainerClient
// with client-side encryption options present will throw, as this functionality is only supported with BlobClient.
BlobClient blob = new BlobServiceClient(connectionString, options).GetBlobContainerClient("myContainer").GetBlobClient("myBlob");

// Upload the encrypted contents to the blob.
blob.Upload(stream);

// Download and decrypt the encrypted contents from the blob.
MemoryStream outputStream = new MemoryStream();
blob.DownloadTo(outputStream);
```

**BlobServiceClient** nie jest konieczna do zastosowania opcji szyfrowania. Mogą być również przesyłane do **BlobContainerClient** / konstruktorów**BlobClient** BlobContainerClient, które akceptują obiekty **BlobClientOptions** .

Jeśli żądany obiekt **BlobClient** już istnieje, ale bez opcji szyfrowania po stronie klienta, Metoda rozszerzenia istnieje, aby utworzyć kopię tego obiektu dla danego **ClientSideEncryptionOptions**. Ta metoda rozszerzenia pozwala uniknąć obciążenia tworzenia nowego obiektu **BlobClient** od zera.

```csharp
using Azure.Storage.Blobs.Specialized;

// Your existing BlobClient instance and encryption options
BlobClient plaintextBlob;
ClientSideEncryptionOptions encryptionOptions;

// Get a copy of plaintextBlob that uses client-side encryption
BlobClient clientSideEncryptionBlob = plaintextBlob.WithClientSideEncryptionOptions(encryptionOptions);
```

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)
Utwórz obiekt **BlobEncryptionPolicy** i ustaw go w opcjach żądania (na interfejsie API lub na poziomie klienta przy użyciu **DefaultRequestOptions**). Wszystkie inne elementy będą obsługiwane przez bibliotekę kliencką wewnętrznie.

```csharp
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

// Set the encryption policy on the request options.
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

// Upload the encrypted contents to the blob.
blob.UploadFromStream(stream, size, null, options, null);

// Download and decrypt the encrypted contents from the blob.
MemoryStream outputStream = new MemoryStream();
blob.DownloadToStream(outputStream, null, options, null);
```

---

### <a name="queue-service-encryption"></a>Szyfrowanie usługa kolejki
# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)
Utwórz obiekt **ClientSideEncryptionOptions** i ustaw go na tworzenie klienta z **SpecializedQueueClientOptions**. Nie można ustawić opcji szyfrowania dla poszczególnych interfejsów API. Wszystkie inne elementy będą obsługiwane przez bibliotekę kliencką wewnętrznie.

```csharp
// Your key and key resolver instances, either through KeyVault SDK or an external implementation
IKeyEncryptionKey key;
IKeyEncryptionKeyResolver keyResolver;

// Create the encryption options to be used for upload and download.
ClientSideEncryptionOptions encryptionOptions = new ClientSideEncryptionOptions(ClientSideEncryptionVersion.V1_0)
{
   KeyEncryptionKey = key,
   KeyResolver = keyResolver,
   // string the storage client will use when calling IKeyEncryptionKey.WrapKey()
   KeyWrapAlgorithm = "some algorithm name"
};

// Set the encryption options on the client options
QueueClientOptions options = new SpecializedQueueClientOptions() { ClientSideEncryption = encryptionOptions };

// Get your queue client with client-side encryption enabled.
// Client-side encryption options are passed from service to queue clients.
QueueClient queue = new QueueServiceClient(connectionString, options).GetQueueClient("myQueue");

// Send an encrypted queue message.
queue.SendMessage("Hello, World!");

// Download queue messages, decrypting ones that are detected to be encrypted
QueueMessage[] queue.ReceiveMessages(); 
```

**QueueServiceClient** nie jest konieczna do zastosowania opcji szyfrowania. Mogą być również przesyłane do konstruktorów **QueueClient** , które akceptują obiekty **QueueClientOptions** .

Jeśli żądany obiekt **QueueClient** już istnieje, ale bez opcji szyfrowania po stronie klienta, Metoda rozszerzenia istnieje, aby utworzyć kopię tego obiektu dla danego **ClientSideEncryptionOptions**. Ta metoda rozszerzenia pozwala uniknąć obciążenia tworzenia nowego obiektu **QueueClient** od zera.

```csharp
using Azure.Storage.Queues.Specialized;

// Your existing QueueClient instance and encryption options
QueueClient plaintextQueue;
ClientSideEncryptionOptions encryptionOptions;

// Get a copy of plaintextQueue that uses client-side encryption
QueueClient clientSideEncryptionQueue = plaintextQueue.WithClientSideEncryptionOptions(encryptionOptions);
```

Niektórzy użytkownicy mogą mieć kolejki, w przypadku których nie wszystkie odebrane komunikaty mogą zostać pomyślnie odszyfrowane, a klucz lub mechanizm rozwiązywania konfliktów musi zgłosić. Ostatni wiersz powyższego przykładu zostanie zgłoszony w tym przypadku, a żaden z odebranych komunikatów nie będzie dostępny. W tych scenariuszach podrzędna Klasa **QueueClientSideEncryptionOptions** może służyć do zapewnienia klientom opcji szyfrowania. Przedstawia on **DecryptionFailed** zdarzeń, który zostanie wyzwolony za każdym razem, gdy nie uda się odszyfrować komunikatu kolejki, tak długo, jak co najmniej jedno wywołanie zostało dodane do zdarzenia. W ten sposób można w ten sposób obsługiwać pojedyncze nieudane komunikaty i zostaną one odfiltrowane z końcowego **QueueMessage []** zwróconego przez **ReceiveMessages**.

```csharp
// Create your encryption options using the sub-class.
QueueClientSideEncryptionOptions encryptionOptions = new QueueClientSideEncryptionOptions(ClientSideEncryptionVersion.V1_0)
{
   KeyEncryptionKey = key,
   KeyResolver = keyResolver,
   // string the storage client will use when calling IKeyEncryptionKey.WrapKey()
   KeyWrapAlgorithm = "some algorithm name"
};

// Add a handler to the DecryptionFailed event.
encryptionOptions.DecryptionFailed += (source, args) => {
   QueueMessage failedMessage = (QueueMessage)source;
   Exception exceptionThrown = args.Exception;
   // do something
};

// Use these options with your client objects.
QueueClient queue = new QueueClient(connectionString, queueName, new SpecializedQueueClientOptions()
{
   ClientSideEncryption = encryptionOptions
});

// Retrieve 5 messages from the queue.
// Assume 5 messages come back and one throws during decryption.
QueueMessage[] messages = queue.ReceiveMessages(maxMessages: 5).Value;
Debug.Assert(messages.Length == 4)
```

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)
Utwórz obiekt **QueueEncryptionPolicy** i ustaw go w opcjach żądania (na interfejsie API lub na poziomie klienta przy użyciu **DefaultRequestOptions**). Wszystkie inne elementy będą obsługiwane przez bibliotekę kliencką wewnętrznie.

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

 // Add message
 QueueRequestOptions options = new QueueRequestOptions() { EncryptionPolicy = policy };
 queue.AddMessage(message, null, null, options, null);

 // Retrieve message
 CloudQueueMessage retrMessage = queue.GetMessage(null, options, null);
```

---

### <a name="table-service-encryption-v11-only"></a>Szyfrowanie Table service (tylko v11)
Oprócz tworzenia zasad szyfrowania i ustawiania ich dla opcji żądania należy określić wartość **EncryptionResolver** w **TableRequestOptions**lub ustawić atrybut [EncryptProperty] w jednostce.

#### <a name="using-the-resolver"></a>Korzystanie z programu rozpoznawania nazw

```csharp
// Create the IKey used for encryption.
 RsaKey key = new RsaKey("private:key1" /* key identifier */);

 // Create the encryption policy to be used for upload and download.
 TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

 TableRequestOptions options = new TableRequestOptions()
 {
    EncryptionResolver = (pk, rk, propName) =>
     {
        if (propName == "foo")
         {
            return true;
         }
         return false;
     },
     EncryptionPolicy = policy
 };

 // Insert Entity
 currentTable.Execute(TableOperation.Insert(ent), options, null);

 // Retrieve Entity
 // No need to specify an encryption resolver for retrieve
 TableRequestOptions retrieveOptions = new TableRequestOptions()
 {
    EncryptionPolicy = policy
 };

 TableOperation operation = TableOperation.Retrieve(ent.PartitionKey, ent.RowKey);
 TableResult result = currentTable.Execute(operation, retrieveOptions, null);
```

#### <a name="using-attributes"></a>Używanie atrybutów
Jak wspomniano powyżej, jeśli jednostka implementuje klasy tableentity, właściwości mogą mieć atrybut [EncryptProperty] zamiast określać **EncryptionResolver**.

```csharp
[EncryptProperty]
 public string EncryptedProperty1 { get; set; }
```

## <a name="encryption-and-performance"></a>Szyfrowanie i wydajność
Należy pamiętać, że szyfrowanie danych magazynu skutkuje dodatkowym obciążeniem wydajności. Należy wygenerować klucz zawartości i IV, zawartość musi być zaszyfrowana, a dodatkowe metadane muszą być sformatowane i przekazane. To obciążenie będzie się różnić w zależności od ilości szyfrowanych danych. Zalecamy, aby klienci zawsze testować swoje aplikacje pod kątem wydajności podczas opracowywania.

## <a name="next-steps"></a>Następne kroki
* [Samouczek: szyfrowanie i odszyfrowywanie obiektów BLOB w Microsoft Azure Storage przy użyciu Azure Key Vault](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)
* Pobierz [pakiet NuGet biblioteki klienta usługi Azure Storage dla platformy .NET](https://www.nuget.org/packages/WindowsAzure.Storage)
* Pobieranie Azure Key Vault pakietów programu NuGet [Core](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/), [klienta](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)i [rozszerzeń](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/)  
* Zapoznaj się z [dokumentacją Azure Key Vault](../../key-vault/general/overview.md)
