---
title: Client-Side szyfrowanie przy użyciu języka Java dla Microsoft Azure Storage | Microsoft Docs
description: Biblioteka klienta usługi Azure Storage dla języka Java obsługuje szyfrowanie po stronie klienta i integrację z usługą Azure Key Vault, aby uzyskać maksymalne zabezpieczenia aplikacji usługi Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.devlang: java
ms.topic: article
ms.date: 02/18/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-java
ms.openlocfilehash: 78baaa3f794bed870b40fb3975f6b80ff37e90f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102043732"
---
# <a name="client-side-encryption-and-azure-key-vault-with-java-for-microsoft-azure-storage"></a>Client-Side szyfrowanie i Azure Key Vault przy użyciu języka Java dla Microsoft Azure Storage
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Omówienie
[Biblioteka klienta usługi Azure Storage dla języka Java](https://search.maven.org/artifact/com.azure/azure-storage-blob-cryptography) obsługuje szyfrowanie danych w aplikacjach klienckich przed przekazaniem ich do usługi Azure Storage i odszyfrowywanie danych podczas pobierania ich do klienta. Biblioteka obsługuje także integrację z usługą [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dla zarządzania kluczami konta magazynu.

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
Biblioteka klienta obsługuje obecnie szyfrowanie tylko całych obiektów BLOB. Szyfrowanie jest obsługiwane, gdy użytkownicy korzystają z metod **upload** _ lub _ *openOutputStream**. W przypadku plików do pobrania obsługiwane są zarówno pobieranie kompletne, jak i zakres.  

Podczas szyfrowania Biblioteka klienta generuje losowy wektor inicjacji (IV) z 16 bajtów wraz z losowym kluczem szyfrowania zawartości (CEK) wynoszącym 32 bajtów i przeprowadź szyfrowanie koperty danych obiektów BLOB przy użyciu tych informacji. Opakowany CEK i niektóre dodatkowe metadane szyfrowania są następnie przechowywane jako metadane obiektów BLOB wraz z zaszyfrowanego obiektu BLOB w usłudze.

> [!WARNING]
> Jeśli edytujesz lub przekazujesz własne metadane dla obiektu BLOB, musisz upewnić się, że metadane są zachowywane. W przypadku przekazania nowych metadanych bez tych metadanych zawinięte CEK, IV i inne metadane zostaną utracone i nie będzie można ponownie pobrać zawartości obiektu BLOB.
> 
> 

Pobieranie zaszyfrowanego obiektu BLOB polega na pobieraniu zawartości całego obiektu BLOB przy użyciu metod **pobierania** / **openInputStream** . Opakowany CEK jest rozpakowany i używany razem z IV (przechowywane jako metadane obiektu BLOB w tym przypadku) w celu zwrócenia odszyfrowanych danych do użytkowników.

Pobieranie dowolnego zakresu (metody **downloadRange** ) w zaszyfrowanym obiekcie blob obejmuje dostosowanie zakresu zapewnianego przez użytkowników w celu uzyskania niewielkiej ilości dodatkowych danych, których można użyć do pomyślnego odszyfrowania żądanego zakresu.  

Wszystkie typy obiektów BLOB (blokowe obiekty blob, stronicowe obiekty blob i dołączane obiekty blob) mogą być szyfrowane/odszyfrowywane przy użyciu tego schematu.

### <a name="queues"></a>Kolejki
Ponieważ komunikaty kolejki mogą mieć dowolny format, Biblioteka klienta definiuje format niestandardowy, który zawiera wektor inicjujący (IV) i zaszyfrowany klucz szyfrowania zawartości (CEK) w tekście komunikatu.  

Podczas szyfrowania Biblioteka klienta generuje losową część IV 16 bajtów wraz z losową CEKą 32 bajtów i wykonuje szyfrowanie koperty tekstu komunikatu w kolejce przy użyciu tych informacji. Opakowany CEK i dodatkowe metadane szyfrowania są następnie dodawane do komunikatu zaszyfrowanej kolejki. Ten zmodyfikowany komunikat (przedstawiony poniżej) jest przechowywany w usłudze.

```
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

Podczas odszyfrowywania, opakowany klucz jest wyodrębniany z komunikatu kolejki i nieopakowany. IV jest również wyodrębniany z komunikatu kolejki i używany razem z nieopakowanym kluczem do odszyfrowania danych komunikatu w kolejce. Należy pamiętać, że metadane szyfrowania są małe (poniżej 500 bajtów), więc w miarę jak licznik KB dla komunikatu kolejki, wpływ powinien być zarządzany.

### <a name="tables"></a>tabelami
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
   
   W przypadku tabel oprócz zasad szyfrowania użytkownicy muszą określić właściwości, które mają być szyfrowane. Można to zrobić przez określenie atrybutu [Encryption] (dla jednostek POCO, które pochodzą z klasy tableentity) lub mechanizmu rozwiązywania konfliktów w opcjach żądania. Program rozpoznawania szyfrowania jest delegatem, który pobiera klucz partycji, klucz wiersza i nazwę właściwości i zwraca wartość logiczną wskazującą, czy ta właściwość powinna być szyfrowana. Podczas szyfrowania Biblioteka kliencka będzie używać tych informacji do decydowania, czy właściwość powinna być szyfrowana podczas zapisywania w sieci. Delegat udostępnia również możliwość logiki dotyczącej sposobu szyfrowania właściwości. (Na przykład, jeśli X, wówczas Zaszyfruj Właściwość A; w przeciwnym razie Szyfruj właściwości a i B). Należy pamiętać, że nie trzeba podawać tych informacji podczas odczytywania lub wysyłania zapytań do jednostek.

### <a name="batch-operations"></a>Operacje wsadowe
W operacjach wsadowych ta sama KEK będzie używana we wszystkich wierszach tej operacji wsadowej, ponieważ Biblioteka klienta zezwala tylko na jeden obiekt opcji (a tym samym zasady/KEK) na operację wsadową. Jednak Biblioteka klienta będzie wewnętrznie generować nowe losowe CEK IV i losowe na wiersz w partii. Użytkownicy mogą również zaszyfrować różne właściwości dla każdej operacji w partii przez zdefiniowanie tego zachowania w programie rozpoznawania nazw.

### <a name="queries"></a>Zapytania
> [!NOTE]
> Ponieważ jednostki są zaszyfrowane, nie można uruchamiać zapytań, które filtrują zaszyfrowaną właściwość.  Jeśli spróbujesz, wyniki będą nieprawidłowe, ponieważ usługa próbuje porównać zaszyfrowane dane z niezaszyfrowanymi danymi.
> 
> 
> Aby wykonać operacje zapytania, należy określić program rozpoznawania kluczy, który może rozpoznać wszystkie klucze w zestawie wyników. Jeśli nie można rozpoznać jednostki zawartej w wyniku zapytania jako dostawcy, Biblioteka klienta zgłosi błąd. Dla każdego zapytania, które wykonuje projekcje po stronie serwera, Biblioteka klienta doda specjalne właściwości metadanych szyfrowania (_ClientEncryptionMetadata1 i _ClientEncryptionMetadata2) domyślnie do wybranych kolumn.

## <a name="azure-key-vault"></a>Azure Key Vault
Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Za pomocą Azure Key Vault użytkownicy mogą szyfrować klucze i wpisy tajne (takie jak klucze uwierzytelniania, klucze konta magazynu, klucze szyfrowania danych). Pliki i hasła PFX) przy użyciu kluczy chronionych przez sprzętowe moduły zabezpieczeń (sprzętowych modułów zabezpieczeń). Aby uzyskać więcej informacji, zobacz [Co to jest usługa Azure Key Vault?](../../key-vault/general/overview.md).

Biblioteka klienta magazynu używa biblioteki podstawowej Key Vault, aby zapewnić wspólną platformę na platformie Azure do zarządzania kluczami. Użytkownicy mogą również skorzystać z dodatkowej korzyści wynikającej z używania biblioteki rozszerzeń Key Vault. Biblioteka rozszerzeń zapewnia użyteczną funkcjonalność dla prostych i bezproblemowych i niezawodnych dostawców kluczy symetrycznych i RSA oraz z agregacją i buforowaniem.

### <a name="interface-and-dependencies"></a>Interfejs i zależności
Istnieją trzy Key Vault pakiety:  

* Magazyn kluczy Azure — rdzeń zawiera IKey i IKeyResolver. Jest to mały pakiet bez zależności. Biblioteka klienta usługi Storage dla języka Java definiuje ją jako zależność.
* Magazyn kluczy Azure zawiera Key Vault klienta REST.  
* Magazyn kluczy Azure — rozszerzenia zawiera kod rozszerzenia, który obejmuje implementacje algorytmów kryptograficznych i RSAKey oraz SymmetricKey. Jest ona zależna od podstawowych i przestrzeni nazw magazynu kluczy i oferuje funkcje do definiowania zagregowanego programu rozpoznawania nazw (gdy użytkownicy chcą korzystać z wielu dostawców kluczy) i rozpoznawania klucza buforowania. Mimo że Biblioteka klienta usługi Storage nie zależy bezpośrednio od tego pakietu, jeśli użytkownicy chcą używać Azure Key Vault do przechowywania swoich kluczy lub używania rozszerzeń Key Vault do korzystania z dostawców usług kryptograficznych lokalnych i w chmurze, będą oni potrzebować tego pakietu.  
  
  Key Vault jest zaprojektowana dla kluczy głównych o wysokiej wartości, a limity ograniczania dla Key Vault są z tego względu zaprojektowane. Podczas wykonywania szyfrowania po stronie klienta przy użyciu Key Vault, preferowanym modelem jest użycie symetrycznego klucza głównego przechowywanych jako wpisy tajne w Key Vault i w pamięci podręcznej lokalnie. Użytkownicy muszą wykonać następujące czynności:  

1. Utwórz klucz tajny w trybie offline i przekaż go do Key Vault.  
2. Użyj identyfikatora podstawowego wpisu tajnego jako parametru, aby rozwiązać bieżącą wersję klucza tajnego w celu szyfrowania, a następnie Buforuj te informacje lokalnie. Użyj CachingKeyResolver do buforowania; Użytkownicy nie oczekują implementacji własnej logiki buforowania.  
3. Użyj mechanizmu rozwiązywania konfliktów jako danych wejściowych podczas tworzenia zasad szyfrowania.
   Więcej informacji dotyczących użycia Key Vault można znaleźć w przykładach kodu szyfrowania.

## <a name="best-practices"></a>Najlepsze rozwiązania
Obsługa szyfrowania jest dostępna tylko w bibliotece klienta usługi Storage dla języka Java.

> [!IMPORTANT]
> Należy pamiętać o tych ważnych kwestiach podczas korzystania z szyfrowania po stronie klienta:
> 
> * Podczas odczytywania lub zapisywania do zaszyfrowanego obiektu BLOB używaj pełnych poleceń przekazywania obiektów blob oraz poleceń pobierania zakresu/całego obiektu BLOB. Unikaj zapisywania do zaszyfrowanego obiektu BLOB przy użyciu operacji protokołu, takich jak Put Block, Put Block list, Write Pages, Clear Pages lub Append Block; w przeciwnym razie może dojść do uszkodzenia zaszyfrowanego obiektu BLOB i uniemożliwić jego odczytanie.
> * W przypadku tabel istnieje podobne ograniczenie. Należy zachować ostrożność, aby nie aktualizować zaszyfrowanych właściwości bez aktualizowania metadanych szyfrowania.  
> * Jeśli ustawisz metadane dla zaszyfrowanego obiektu BLOB, możesz zastąpić metadane związane z szyfrowaniem wymagane do odszyfrowania, ponieważ metadane ustawień nie są addytywne. Dotyczy to również migawek; należy unikać określania metadanych podczas tworzenia migawki zaszyfrowanego obiektu BLOB. Jeśli metadane muszą być ustawione, należy najpierw wywołać metodę **downloadAttributes** , aby pobrać bieżące metadane szyfrowania i uniknąć jednoczesnego zapisu podczas ustawiania metadanych.  
> * Włącz flagę **requireEncryption** w domyślnych opcjach żądania dla użytkowników, którzy powinni korzystać tylko z szyfrowanych danych. Aby uzyskać więcej informacji, zobacz poniżej.  
> 
> 

## <a name="client-api--interface"></a>API/interfejs klienta
Podczas tworzenia obiektu EncryptionPolicy użytkownicy mogą podawać tylko klucz (implementujący IKey), tylko program rozpoznawania nazw (implementując IKeyResolver) lub oba te elementy. IKey to podstawowy typ klucza, który jest identyfikowany przy użyciu identyfikatora klucza i który udostępnia logikę zawijania/rozpakowywania. IKeyResolver jest używany do rozpoznawania klucza podczas procesu odszyfrowywania. Definiuje metodę ResolveKey, która zwraca IKey z identyfikatorem klucza. Dzięki temu użytkownicy mogą wybierać między wieloma kluczami, które są zarządzane w wielu lokalizacjach.

* W przypadku szyfrowania klucz jest używany zawsze, a brak klucza spowoduje wystąpienie błędu.  
* W przypadku odszyfrowywania:  
  
  * Program rozpoznawania kluczy jest wywoływany, jeśli jest określony w celu pobrania klucza. Jeśli jest określony mechanizm rozwiązywania konfliktów, ale nie ma mapowania dla identyfikatora klucza, zostanie zgłoszony błąd.  
  * Jeśli nie określono mechanizmu rozwiązywania konfliktów, ale określono klucz, zostanie użyty klucz, jeśli jego identyfikator pasuje do wymaganego identyfikatora klucza. Jeśli identyfikator nie jest zgodny, zostanie zgłoszony błąd.  
    
    [Przykłady szyfrowania](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples) przedstawiają bardziej szczegółowy scenariusz kompleksowego dla obiektów blob, kolejek i tabel oraz Key Vault integrację.

### <a name="requireencryption-mode"></a>Tryb RequireEncryption
Użytkownicy mogą opcjonalnie włączyć tryb operacji, w której wszystkie operacje przekazywania i pobierania muszą być szyfrowane. W tym trybie program próbuje przekazać dane bez zasad szyfrowania lub pobrać dane, które nie są zaszyfrowane w usłudze, zakończą się niepowodzeniem na kliencie. Flaga **requireEncryption** obiektu Opcje żądania steruje tym zachowaniem. Jeśli aplikacja będzie szyfrować wszystkie obiekty przechowywane w usłudze Azure Storage, można ustawić właściwość **requireEncryption** w domyślnej opcji żądania dla obiektu klienta usługi.   

Na przykład użyj **CloudBlobClient. getDefaultRequestOptions (). setRequireEncryption (true)** , aby wymagać szyfrowania dla wszystkich operacji obiektu BLOB wykonywanych za pomocą tego obiektu klienta.

### <a name="blob-service-encryption"></a>Szyfrowanie Blob service
Utwórz obiekt **BlobEncryptionPolicy** i ustaw go w opcjach żądania (na interfejsie API lub na poziomie klienta przy użyciu **DefaultRequestOptions**). Wszystkie inne elementy będą obsługiwane przez bibliotekę kliencką wewnętrznie.

# <a name="java-v12"></a>[V12 Java](#tab/java)

Obecnie pracujemy nad utworzeniem fragmentów kodu odzwierciedlających wersję 12. x bibliotek klienckich usługi Azure Storage. Aby uzyskać więcej informacji, zobacz temat [ogłaszanie bibliotek klienckich usługi Azure Storage V12](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="java-v8"></a>[V8 Java](#tab/java8)

```java
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(key, null);

// Set the encryption policy on the request options.
BlobRequestOptions options = new BlobRequestOptions();
options.setEncryptionPolicy(policy);

// Upload the encrypted contents to the blob.
blob.upload(stream, size, null, options, null);

// Download and decrypt the encrypted contents from the blob.
ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
blob.download(outputStream, null, options, null);
```
---

### <a name="queue-service-encryption"></a>Szyfrowanie usługa kolejki
Utwórz obiekt **QueueEncryptionPolicy** i ustaw go w opcjach żądania (na interfejsie API lub na poziomie klienta przy użyciu **DefaultRequestOptions**). Wszystkie inne elementy będą obsługiwane przez bibliotekę kliencką wewnętrznie.

# <a name="java-v12"></a>[V12 Java](#tab/java)

Obecnie pracujemy nad utworzeniem fragmentów kodu odzwierciedlających wersję 12. x bibliotek klienckich usługi Azure Storage. Aby uzyskać więcej informacji, zobacz temat [ogłaszanie bibliotek klienckich usługi Azure Storage V12](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="java-v8"></a>[V8 Java](#tab/java8)

```java
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
QueueEncryptionPolicy policy = new QueueEncryptionPolicy(key, null);

// Add message
QueueRequestOptions options = new QueueRequestOptions();
options.setEncryptionPolicy(policy);

queue.addMessage(message, 0, 0, options, null);

// Retrieve message
CloudQueueMessage retrMessage = queue.retrieveMessage(30, options, null);
```
---

### <a name="table-service-encryption"></a>Szyfrowanie Table service
Oprócz tworzenia zasad szyfrowania i ustawiania ich dla opcji żądania należy określić wartość **EncryptionResolver** w **TableRequestOptions** lub ustawić atrybut [Encryption] dla metody pobierającej i ustawiającej jednostki.

### <a name="using-the-resolver"></a>Korzystanie z programu rozpoznawania nazw

# <a name="java-v12"></a>[V12 Java](#tab/java)

Obecnie pracujemy nad utworzeniem fragmentów kodu odzwierciedlających wersję 12. x bibliotek klienckich usługi Azure Storage. Aby uzyskać więcej informacji, zobacz temat [ogłaszanie bibliotek klienckich usługi Azure Storage V12](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="java-v8"></a>[V8 Java](#tab/java8)

```java
// Create the IKey used for encryption.
RsaKey key = new RsaKey("private:key1" /* key identifier */);

// Create the encryption policy to be used for upload and download.
TableEncryptionPolicy policy = new TableEncryptionPolicy(key, null);

TableRequestOptions options = new TableRequestOptions()
options.setEncryptionPolicy(policy);
options.setEncryptionResolver(new EncryptionResolver() {
    public boolean encryptionResolver(String pk, String rk, String key) {
        if (key == "foo")
        {
            return true;
        }
        return false;
    }
});

// Insert Entity
currentTable.execute(TableOperation.insert(ent), options, null);

// Retrieve Entity
// No need to specify an encryption resolver for retrieve
TableRequestOptions retrieveOptions = new TableRequestOptions()
retrieveOptions.setEncryptionPolicy(policy);

TableOperation operation = TableOperation.retrieve(ent.PartitionKey, ent.RowKey, DynamicTableEntity.class);
TableResult result = currentTable.execute(operation, retrieveOptions, null);
```
---

### <a name="using-attributes"></a>Używanie atrybutów
Jak wspomniano powyżej, jeśli jednostka implementuje klasy tableentity, wówczas właściwości getter i setter mogą być dekoracyjne przy użyciu atrybutu [Szyfruj] zamiast określać **EncryptionResolver**.

# <a name="java-v12"></a>[V12 Java](#tab/java)

Obecnie pracujemy nad utworzeniem fragmentów kodu odzwierciedlających wersję 12. x bibliotek klienckich usługi Azure Storage. Aby uzyskać więcej informacji, zobacz temat [ogłaszanie bibliotek klienckich usługi Azure Storage V12](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="java-v8"></a>[V8 Java](#tab/java8)

```java
private string encryptedProperty1;

@Encrypt
public String getEncryptedProperty1 () {
    return this.encryptedProperty1;
}

@Encrypt
public void setEncryptedProperty1(final String encryptedProperty1) {
    this.encryptedProperty1 = encryptedProperty1;
}
```
---

## <a name="encryption-and-performance"></a>Szyfrowanie i wydajność

Należy pamiętać, że szyfrowanie danych magazynu skutkuje dodatkowym obciążeniem wydajności. Należy wygenerować klucz zawartości i IV, zawartość musi być zaszyfrowana, a dodatkowe metadane muszą być sformatowane i przekazane. To obciążenie będzie się różnić w zależności od ilości szyfrowanych danych. Zalecamy, aby klienci zawsze testować swoje aplikacje pod kątem wydajności podczas opracowywania.

## <a name="next-steps"></a>Następne kroki

* Pobierz [bibliotekę klienta usługi Azure Storage dla pakietu Java Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-storage)  
* Pobierz [bibliotekę klienta usługi Azure Storage dla kodu źródłowego Java z usługi GitHub](https://github.com/Azure/azure-storage-java)
* Pobierz Azure Key Vault Maven Library for Java Maven Packages:
  * Pakiet [podstawowy](https://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault-core)
  * Pakiet [klienta](https://mvnrepository.com/artifact/com.microsoft.azure/azure-keyvault)
* Zapoznaj się z [dokumentacją Azure Key Vault](../../key-vault/general/overview.md)
