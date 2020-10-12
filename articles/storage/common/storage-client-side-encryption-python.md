---
title: Szyfrowanie po stronie klienta przy użyciu języka Python
titleSuffix: Azure Storage
description: Biblioteka klienta usługi Azure Storage dla języka Python obsługuje szyfrowanie po stronie klienta w celu zapewnienia maksymalnego poziomu zabezpieczeń aplikacji usługi Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.devlang: python
ms.topic: how-to
ms.date: 12/04/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 511166e156591562b2120b58cc420f3fccd1d8c4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84804901"
---
# <a name="client-side-encryption-with-python"></a>Szyfrowanie po stronie klienta przy użyciu języka Python

[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Omówienie
[Biblioteka klienta usługi Azure Storage dla języka Python](https://pypi.python.org/pypi/azure-storage) obsługuje szyfrowanie danych w aplikacjach klienckich przed przekazaniem ich do usługi Azure Storage i odszyfrowywanie danych podczas pobierania ich do klienta.

> [!NOTE]
> Biblioteka języka Python usługi Azure Storage jest w wersji zapoznawczej.
> 
> 

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Szyfrowanie i odszyfrowywanie za pomocą techniki Envelope
Procesy szyfrowania i odszyfrowywania są zgodne z techniką formy.

### <a name="encryption-via-the-envelope-technique"></a>Szyfrowanie za pośrednictwem techniki Envelope
Szyfrowanie za pomocą techniki Envelope działa w następujący sposób:

1. Biblioteka klienta usługi Azure Storage generuje klucz szyfrowania zawartości (CEK), który jest jednorazowym użyciem klucza symetrycznego.
2. Dane użytkownika są szyfrowane przy użyciu tego CEK.
3. CEK jest następnie opakowany (zaszyfrowany) przy użyciu klucza szyfrowania klucza (KEK). KEK jest identyfikowany przez identyfikator klucza i może być parę kluczy asymetrycznych lub kluczem symetrycznym, który jest zarządzany lokalnie.
   Sama Biblioteka klienta magazynu nigdy nie ma dostępu do KEK. Biblioteka wywołuje algorytm zawijania kluczy dostarczany przez KEK. Użytkownicy mogą zdecydować się na użycie niestandardowych dostawców do zawijania kluczy/rozpakowywania w razie potrzeby.
4. Zaszyfrowane dane są następnie przekazywane do usługi Azure Storage. Opakowany klucz wraz z dodatkowymi metadanymi szyfrowania jest przechowywany jako metadane (na obiekcie BLOB) lub interpolowany z zaszyfrowanymi danymi (kolejkowanie komunikatów i obiekty tabeli).

### <a name="decryption-via-the-envelope-technique"></a>Odszyfrowywanie za pomocą techniki Envelope
Odszyfrowywanie za pomocą techniki Envelope działa w następujący sposób:

1. W bibliotece klienta założono, że użytkownik zarządza kluczem szyfrowania kluczy (KEK) lokalnie. Użytkownik nie musi znać określonego klucza, który był używany do szyfrowania. Zamiast tego program rozpoznawania kluczy, który rozwiązuje różne identyfikatory kluczy do kluczy, można skonfigurować i użyć.
2. Biblioteka klienta pobiera zaszyfrowane dane wraz z dowolnym materiałem szyfrowania, który jest przechowywany w usłudze.
3. Klucz szyfrowania zawartości opakowanej (CEK) jest następnie odpakowany (odszyfrowany) przy użyciu klucza szyfrowania klucza (KEK). W tym miejscu Biblioteka kliencka nie ma dostępu do KEK. Po prostu wywołuje algorytm unotoką niestandardowego dostawcy.
4. Klucz szyfrowania zawartości (CEK) jest następnie używany do odszyfrowywania zaszyfrowanych danych użytkownika.

## <a name="encryption-mechanism"></a>Mechanizm szyfrowania
Biblioteka klienta magazynu używa [algorytmu AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) w celu szyfrowania danych użytkownika. W przypadku trybu [szyfrowania bloku blokowego (CBC)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) z algorytmem AES. Każda usługa działa nieco inaczej, więc omawiamy każdą z nich w tym miejscu.

### <a name="blobs"></a>Obiekty blob
Biblioteka klienta obsługuje obecnie szyfrowanie tylko całych obiektów BLOB. Szyfrowanie jest obsługiwane, gdy użytkownicy korzystają z metod **Create***. W przypadku plików do pobrania obsługiwane są zarówno pobieranie w całości, jak i zakres, a ponadto dostępne są przetwarzanie równoległe przekazywania i pobierania.

Podczas szyfrowania Biblioteka klienta generuje losowy wektor inicjacji (IV) z 16 bajtów wraz z losowym kluczem szyfrowania zawartości (CEK) wynoszącym 32 bajtów i przeprowadź szyfrowanie koperty danych obiektów BLOB przy użyciu tych informacji. Opakowany CEK i niektóre dodatkowe metadane szyfrowania są następnie przechowywane jako metadane obiektów BLOB wraz z zaszyfrowanego obiektu BLOB w usłudze.

> [!WARNING]
> Jeśli edytujesz lub przekazujesz własne metadane dla obiektu BLOB, musisz upewnić się, że metadane są zachowywane. W przypadku przekazania nowych metadanych bez tych metadanych zawinięte CEK, IV i inne metadane zostaną utracone i nie będzie można ponownie pobrać zawartości obiektu BLOB.
> 
> 

Pobieranie zaszyfrowanego obiektu BLOB polega na pobieraniu zawartości całego obiektu BLOB przy użyciu metod **Get*** wygodnych. Opakowany CEK jest rozpakowany i używany razem z IV (przechowywane jako metadane obiektu BLOB w tym przypadku) w celu zwrócenia odszyfrowanych danych do użytkowników.

Pobieranie dowolnego zakresu (metody**Get*** z parametrami zakresu przekazane) w zaszyfrowanym obiekcie blob obejmuje dostosowanie zakresu zapewnianego przez użytkowników w celu uzyskania niewielkiej ilości dodatkowych danych, których można użyć do pomyślnego odszyfrowania żądanego zakresu.

Blokowe obiekty blob i stronicowe obiekty blob mogą być szyfrowane/odszyfrowywane przy użyciu tego schematu. Obecnie nie jest obsługiwane szyfrowanie dołączanych obiektów BLOB.

### <a name="queues"></a>Kolejki
Ponieważ komunikaty kolejki mogą mieć dowolny format, Biblioteka klienta definiuje format niestandardowy, który zawiera wektor inicjujący (IV) i zaszyfrowany klucz szyfrowania zawartości (CEK) w tekście komunikatu.

Podczas szyfrowania Biblioteka klienta generuje losową część IV 16 bajtów wraz z losową CEKą 32 bajtów i wykonuje szyfrowanie koperty tekstu komunikatu w kolejce przy użyciu tych informacji. Opakowany CEK i dodatkowe metadane szyfrowania są następnie dodawane do komunikatu zaszyfrowanej kolejki. Ten zmodyfikowany komunikat (przedstawiony poniżej) jest przechowywany w usłudze.

```
<MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>
```

Podczas odszyfrowywania, opakowany klucz jest wyodrębniany z komunikatu kolejki i nieopakowany. IV jest również wyodrębniany z komunikatu kolejki i używany razem z nieopakowanym kluczem do odszyfrowania danych komunikatu w kolejce. Należy pamiętać, że metadane szyfrowania są małe (poniżej 500 bajtów), więc w miarę jak licznik KB dla komunikatu kolejki, wpływ powinien być zarządzany.

### <a name="tables"></a>Tabele
Biblioteka klienta obsługuje szyfrowanie właściwości jednostki dla operacji wstawiania i zamiany.

> [!NOTE]
> Scalanie nie jest obecnie obsługiwane. Ponieważ podzbiór właściwości mógł zostać zaszyfrowany wcześniej przy użyciu innego klucza, po prostu scalanie nowych właściwości i aktualizowanie metadanych spowoduje utratę danych. Scalanie wymaga wykonania dodatkowych wywołań usługi do odczytu istniejącej jednostki z usługi lub użycia nowego klucza dla każdej właściwości, co nie jest odpowiednie ze względu na wydajność.
> 
> 

Szyfrowanie danych tabeli działa w następujący sposób:

1. Użytkownicy określają właściwości, które mają być szyfrowane.
2. Biblioteka klienta generuje losowy wektor inicjacji (IV) z 16 bajtów wraz z losowym kluczem szyfrowania zawartości (CEK) wynoszącym 32 bajtów dla każdej jednostki i wykonuje szyfrowanie kopert dla poszczególnych właściwości, aby były szyfrowane przez wygenerowanie nowej wartości IV na właściwość. Zaszyfrowana właściwość jest przechowywana jako dane binarne.
3. Opakowany CEK i niektóre dodatkowe metadane szyfrowania są następnie przechowywane jako dwie dodatkowe zastrzeżone właściwości. Pierwsza zastrzeżona Właściwość ( \_ ClientEncryptionMetadata1) jest właściwością ciągu, która zawiera informacje o IV, wersji i kluczu opakowanym. Druga Właściwość zastrzeżona ( \_ ClientEncryptionMetadata2) jest właściwością binarną, która zawiera informacje o zaszyfrowanych właściwościach. Informacje zawarte w tej drugiej właściwości ( \_ ClientEncryptionMetadata2) są zaszyfrowane.
4. Ze względu na te dodatkowe właściwości zastrzeżone wymagane do szyfrowania, użytkownicy mogą teraz mieć tylko 250 właściwości niestandardowe zamiast 252. Łączny rozmiar jednostki musi być mniejszy niż 1 MB.

   Należy pamiętać, że tylko właściwości ciągu mogą być szyfrowane. Jeśli inne typy właściwości mają być szyfrowane, muszą być konwertowane na ciągi. Zaszyfrowane ciągi są przechowywane w usłudze jako właściwości binarne i są konwertowane z powrotem do ciągów (nieprzetworzone ciągi, nie EntityProperties z typem obiektu EdmType. STRING) Po odszyfrowaniu.

   W przypadku tabel oprócz zasad szyfrowania użytkownicy muszą określić właściwości, które mają być szyfrowane. Można to zrobić przez zapisanie tych właściwości w klasy tableentity obiektów z typem ustawionym na obiektu EdmType. STRING i szyfrowaniem ustawionym na wartość true lub ustawieniem encryption_resolver_function w obiekcie tableservice. Program rozpoznawania szyfrowania jest funkcją, która przyjmuje klucz partycji, klucz wiersza i nazwę właściwości i zwraca wartość logiczną wskazującą, czy ta właściwość powinna być szyfrowana. Podczas szyfrowania Biblioteka kliencka będzie używać tych informacji do decydowania, czy właściwość powinna być szyfrowana podczas zapisywania w sieci. Delegat udostępnia również możliwość logiki dotyczącej sposobu szyfrowania właściwości. (Na przykład, jeśli X, wówczas Zaszyfruj Właściwość A; w przeciwnym razie Szyfruj właściwości a i B). Należy pamiętać, że nie trzeba podawać tych informacji podczas odczytywania lub wysyłania zapytań do jednostek.

### <a name="batch-operations"></a>Operacje wsadowe
Jedna zasada szyfrowania ma zastosowanie do wszystkich wierszy w partii. Biblioteka kliencka będzie wewnętrznie generować nowe losowe CEK IV i losowe na wiersz w partii. Użytkownicy mogą również zaszyfrować różne właściwości dla każdej operacji w partii przez zdefiniowanie tego zachowania w programie rozpoznawania nazw.
Jeśli partia jest tworzona jako Menedżer kontekstu za pomocą metody tableservice Batch (), zasady szyfrowania tableservice zostaną automatycznie zastosowane do zadania wsadowego. Jeśli partia jest tworzona jawnie przez wywołanie konstruktora, zasady szyfrowania muszą zostać przesłane jako parametr i pozostawione niemodyfikowane przez okres istnienia partii.
Należy pamiętać, że jednostki są szyfrowane w miarę ich wstawiania do partii przy użyciu zasad szyfrowania partii (jednostki nie są szyfrowane w chwili zatwierdzania partii przy użyciu zasad szyfrowania tableservice).

### <a name="queries"></a>Zapytania
> [!NOTE]
> Ponieważ jednostki są zaszyfrowane, nie można uruchamiać zapytań, które filtrują zaszyfrowaną właściwość.  Jeśli spróbujesz, wyniki będą nieprawidłowe, ponieważ usługa próbuje porównać zaszyfrowane dane z niezaszyfrowanymi danymi.
> 
> 
> Aby wykonać operacje zapytania, należy określić program rozpoznawania kluczy, który może rozpoznać wszystkie klucze w zestawie wyników. Jeśli nie można rozpoznać jednostki zawartej w wyniku zapytania jako dostawcy, Biblioteka klienta zgłosi błąd. Dla każdego zapytania, które wykonuje projekcje po stronie serwera, Biblioteka klienta doda do wybranych kolumn specjalne właściwości specjalnych metadanych ( \_ ClientEncryptionMetadata1 i \_ ClientEncryptionMetadata2).
> 
> [!IMPORTANT]
> Należy pamiętać o tych ważnych kwestiach podczas korzystania z szyfrowania po stronie klienta:
> 
> * Podczas odczytywania lub zapisywania do zaszyfrowanego obiektu BLOB używaj pełnych poleceń przekazywania obiektów blob oraz poleceń pobierania zakresu/całego obiektu BLOB. Unikaj zapisywania do zaszyfrowanego obiektu BLOB przy użyciu operacji protokołu, takich jak Put Block, Put Block list, Write Pages lub Clear Pages; w przeciwnym razie może dojść do uszkodzenia zaszyfrowanego obiektu BLOB i uniemożliwić jego odczytanie.
> * W przypadku tabel istnieje podobne ograniczenie. Należy zachować ostrożność, aby nie aktualizować zaszyfrowanych właściwości bez aktualizowania metadanych szyfrowania.
> * Jeśli ustawisz metadane dla zaszyfrowanego obiektu BLOB, możesz zastąpić metadane związane z szyfrowaniem wymagane do odszyfrowania, ponieważ metadane ustawień nie są addytywne. Dotyczy to również migawek; należy unikać określania metadanych podczas tworzenia migawki zaszyfrowanego obiektu BLOB. Jeśli metadane muszą być ustawione, należy najpierw wywołać metodę **get_blob_metadata** , aby pobrać bieżące metadane szyfrowania i uniknąć jednoczesnego zapisu podczas ustawiania metadanych.
> * Włącz flagę **require_encryption** w obiekcie usługi dla użytkowników, którzy powinni korzystać tylko z zaszyfrowanych danych. Aby uzyskać więcej informacji, zobacz poniżej.

Biblioteka klienta magazynu oczekuje podanej KEK i rozpoznawania kluczy, aby zaimplementować następujący interfejs. Obsługa [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) dla zarządzania KEK języka Python jest w stanie oczekiwania i zostanie zintegrowana z tą biblioteką po zakończeniu.

## <a name="client-api--interface"></a>API/interfejs klienta
Po utworzeniu obiektu usługi magazynu (tj. blockblobservice) użytkownik może przypisać wartości do pól wchodzących w skład zasad szyfrowania: key_encryption_key, key_resolver_function i require_encryption. Użytkownicy mogą udostępniać tylko KEK, tylko program rozpoznawania nazw lub oba te elementy. key_encryption_key to podstawowy typ klucza, który jest identyfikowany przy użyciu identyfikatora klucza i który udostępnia logikę zawijania/rozpakowywania. key_resolver_function jest używany do rozpoznawania klucza podczas procesu odszyfrowywania. Zwraca prawidłowy KEK podanym identyfikatorem klucza. Dzięki temu użytkownicy mogą wybierać między wieloma kluczami, które są zarządzane w wielu lokalizacjach.

Aby pomyślnie zaszyfrować dane, KEK musi implementować następujące metody:

* wrap_key (CEK): zawija określone CEK (bajty) przy użyciu algorytmu wybranego przez użytkownika. Zwraca opakowany klucz.
* get_key_wrap_algorithm (): zwraca algorytm używany do zawijania kluczy.
* get_kid (): zwraca identyfikator klucza ciągu dla tego KEK.
  Aby można było pomyślnie odszyfrować dane, KEK musi implementować następujące metody:
* unwrap_key (CEK, algorithm): zwraca nieopakowaną postać określonego CEK przy użyciu algorytmu określonego przez ciąg.
* get_kid (): zwraca identyfikator klucza ciągu dla tego KEK.

Program rozpoznawania kluczy musi mieć co najmniej implementację metody, która w danym identyfikatorze klucza zwraca odpowiedni KEK implementujący interfejs powyżej. Tylko ta metoda ma być przypisana do właściwości key_resolver_function obiektu usługi.

* W przypadku szyfrowania klucz jest używany zawsze, a brak klucza spowoduje wystąpienie błędu.
* W przypadku odszyfrowywania:

  * Program rozpoznawania kluczy jest wywoływany, jeśli jest określony w celu pobrania klucza. Jeśli jest określony mechanizm rozwiązywania konfliktów, ale nie ma mapowania dla identyfikatora klucza, zostanie zgłoszony błąd.
  * Jeśli nie określono mechanizmu rozwiązywania konfliktów, ale określono klucz, zostanie użyty klucz, jeśli jego identyfikator pasuje do wymaganego identyfikatora klucza. Jeśli identyfikator nie jest zgodny, zostanie zgłoszony błąd.

    Przykłady szyfrowania na platformie Azure. Storage. przykłady przedstawiają bardziej szczegółowy scenariusz kompleksowego dla obiektów blob, kolejek i tabel.
      Przykładowe implementacje programu KEK i rozpoznawania kluczy są dostępne w przykładowych plikach jako otoka klucza i rozwiązanie.

### <a name="requireencryption-mode"></a>Tryb RequireEncryption
Użytkownicy mogą opcjonalnie włączyć tryb operacji, w której wszystkie operacje przekazywania i pobierania muszą być szyfrowane. W tym trybie program próbuje przekazać dane bez zasad szyfrowania lub pobrać dane, które nie są zaszyfrowane w usłudze, zakończą się niepowodzeniem na kliencie. Flaga **require_encryption** obiektu usługi steruje tym zachowaniem.

### <a name="blob-service-encryption"></a>Szyfrowanie Blob service
Ustaw pola zasad szyfrowania w obiekcie blockblobservice. Wszystkie inne elementy będą obsługiwane przez bibliotekę kliencką wewnętrznie.

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_block_blob_service.key_encryption_key = kek
my_block_blob_service.key_resolver_funcion = key_resolver.resolve_key

# Upload the encrypted contents to the blob.
my_block_blob_service.create_blob_from_stream(
    container_name, blob_name, stream)

# Download and decrypt the encrypted contents from the blob.
blob = my_block_blob_service.get_blob_to_bytes(container_name, blob_name)
```

### <a name="queue-service-encryption"></a>Szyfrowanie usługa kolejki
Ustaw pola zasad szyfrowania w obiekcie QueueService. Wszystkie inne elementy będą obsługiwane przez bibliotekę kliencką wewnętrznie.

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Set the KEK and key resolver on the service object.
my_queue_service.key_encryption_key = kek
my_queue_service.key_resolver_funcion = key_resolver.resolve_key

# Add message
my_queue_service.put_message(queue_name, content)

# Retrieve message
retrieved_message_list = my_queue_service.get_messages(queue_name)
```

### <a name="table-service-encryption"></a>Szyfrowanie Table service
Oprócz tworzenia zasad szyfrowania i ustawiania ich dla opcji żądania należy określić **encryption_resolver_function** w **tableservice**lub ustawić atrybut szyfrowania w EntityProperty.

### <a name="using-the-resolver"></a>Korzystanie z programu rozpoznawania nazw

```python
# Create the KEK used for encryption.
# KeyWrapper is the provided sample implementation, but the user may use their own object as long as it implements the interface above.
kek = KeyWrapper('local:key1')  # Key identifier

# Create the key resolver used for decryption.
# KeyResolver is the provided sample implementation, but the user may use whatever implementation they choose so long as the function set on the service object behaves appropriately.
key_resolver = KeyResolver()
key_resolver.put_key(kek)

# Define the encryption resolver_function.


def my_encryption_resolver(pk, rk, property_name):
    if property_name == 'foo':
        return True
    return False


# Set the KEK and key resolver on the service object.
my_table_service.key_encryption_key = kek
my_table_service.key_resolver_funcion = key_resolver.resolve_key
my_table_service.encryption_resolver_function = my_encryption_resolver

# Insert Entity
my_table_service.insert_entity(table_name, entity)

# Retrieve Entity
# Note: No need to specify an encryption resolver for retrieve, but it is harmless to leave the property set.
my_table_service.get_entity(
    table_name, entity['PartitionKey'], entity['RowKey'])
```

### <a name="using-attributes"></a>Używanie atrybutów
Jak wspomniano powyżej, właściwość może być oznaczona do szyfrowania przez zapisanie jej w obiekcie EntityProperty i ustawienie pola Szyfruj.

```python
encrypted_property_1 = EntityProperty(EdmType.STRING, value, encrypt=True)
```

## <a name="encryption-and-performance"></a>Szyfrowanie i wydajność
Należy pamiętać, że szyfrowanie danych magazynu skutkuje dodatkowym obciążeniem wydajności. Należy wygenerować klucz zawartości i IV, zawartość musi być szyfrowana, a dodatkowe metadane muszą być sformatowane i przekazane. To obciążenie będzie się różnić w zależności od ilości szyfrowanych danych. Zalecamy, aby klienci zawsze testować swoje aplikacje pod kątem wydajności podczas opracowywania.

## <a name="next-steps"></a>Następne kroki
* Pobierz [bibliotekę klienta usługi Azure Storage dla pakietu Java PyPi](https://pypi.python.org/pypi/azure-storage)
* Pobierz [bibliotekę klienta usługi Azure Storage dla kodu źródłowego języka Python z witryny GitHub](https://github.com/Azure/azure-storage-python)
