---
title: Szyfrowanie po stronie klienta za pomocą platformy .NET dla usługi Microsoft Azure Storage | Dokumenty firmy Microsoft
description: Biblioteka klienta usługi Azure Storage dla platformy .NET obsługuje szyfrowanie po stronie klienta i integrację z usługą Azure Key Vault w celu uzyskania maksymalnego bezpieczeństwa aplikacji usługi Azure Storage.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 10/20/2017
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c07167a9f3a9194b7c45932ac749324429943ea9
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81450126"
---
# <a name="client-side-encryption-and-azure-key-vault-for-microsoft-azure-storage"></a>Szyfrowanie po stronie klienta i usługa Azure Key Vault dla usługi Microsoft Azure Storage
[!INCLUDE [storage-selector-client-side-encryption-include](../../../includes/storage-selector-client-side-encryption-include.md)]

## <a name="overview"></a>Omówienie
[Biblioteka klienta usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage?view=azure-dotnet) obsługuje szyfrowanie danych w aplikacjach klienckich przed przekazaniem do usługi Azure Storage i odszyfrowywanie danych podczas pobierania do klienta. Biblioteka obsługuje również integrację z [usługą Azure Key Vault](https://azure.microsoft.com/services/key-vault/) do zarządzania kluczami do magazynu.

Aby uzyskać samouczek krok po kroku, który prowadzi cię przez proces szyfrowania obiektów blob przy użyciu szyfrowania po stronie klienta i usługi Azure Key Vault, zobacz [Szyfrowanie i odszyfrowywanie obiektów blob w usłudze Microsoft Azure Storage przy użyciu usługi Azure Key Vault](../blobs/storage-encrypt-decrypt-blobs-key-vault.md).

Aby uzyskać informacje o szyfrowaniu po stronie klienta w języku Java, zobacz [Szyfrowanie po stronie klienta za pomocą oprogramowania Java dla usługi Microsoft Azure Storage](storage-client-side-encryption-java.md).

## <a name="encryption-and-decryption-via-the-envelope-technique"></a>Szyfrowanie i odszyfrowywanie za pomocą techniki koperty
Procesy szyfrowania i odszyfrowywania są zgodne z techniką koperty.

### <a name="encryption-via-the-envelope-technique"></a>Szyfrowanie za pomocą techniki koperty
Szyfrowanie za pomocą techniki koperty działa w następujący sposób:

1. Biblioteka klienta magazynu platformy Azure generuje klucz szyfrowania zawartości (CEK), który jest kluczem symetrycznym jednorazowego użycia.
2. Dane użytkownika są szyfrowane przy użyciu tego CEK.
3. CEK jest następnie zawijany (zaszyfrowany) przy użyciu klucza szyfrowania klucza (KEK). KEK jest identyfikowany przez identyfikator klucza i może być asymetryczną parą kluczy lub kluczem symetrycznym i może być zarządzany lokalnie lub przechowywany w magazynach kluczy azure.
   
    Sama biblioteka klienta magazynu nigdy nie ma dostępu do KEK. Biblioteka wywołuje algorytm zawijania kluczy, który jest dostarczany przez magazyn klucza. Użytkownicy mogą wybrać użycie niestandardowych dostawców do zawijania/rozpakowywania kluczy w razie potrzeby.

4. Zaszyfrowane dane są następnie przekazywane do usługi Azure Storage. Zawinięty klucz wraz z niektórymi dodatkowymi metadanymi szyfrowania jest przechowywany jako metadane (na obiektach blob) lub interpolowany z zaszyfrowanymi danymi (wiadomości kolejki i jednostki tabeli).

### <a name="decryption-via-the-envelope-technique"></a>Odszyfrowywanie za pomocą techniki koperty
Odszyfrowywanie za pomocą techniki koperty działa w następujący sposób:

1. Biblioteka klienta zakłada, że użytkownik zarządza kluczem szyfrowania klucza (KEK) lokalnie lub w magazynach kluczy azure. Użytkownik nie musi znać określonego klucza, który był używany do szyfrowania. Zamiast tego można skonfigurować i używać rozpoznawania kluczy, który rozpoznaje różne identyfikatory kluczy do kluczy.
2. Biblioteka klienta pobiera zaszyfrowane dane wraz z dowolnym materiałem szyfrowania, który jest przechowywany w usłudze.
3. Klucz szyfrowania opakowanej zawartości (CEK) jest następnie rozpakowany (odszyfrowany) przy użyciu klucza szyfrowania klucza (KEK). W tym miejscu ponownie biblioteka klienta nie ma dostępu do KEK. Po prostu wywołuje algorytm rozpakowywania niestandardowego lub dostawcy usługi Key Vault.
4. Klucz szyfrowania zawartości (CEK) jest następnie używany do odszyfrowywania zaszyfrowanych danych użytkownika.

## <a name="encryption-mechanism"></a>Mechanizm szyfrowania
Biblioteka klienta magazynu używa [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) w celu szyfrowania danych użytkownika. W szczególności [tryb łańcucha bloków szyfrowania (CBC)](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#Cipher-block_chaining_.28CBC.29) z AES. Każda usługa działa nieco inaczej, więc omówimy każdy z nich tutaj.

### <a name="blobs"></a>Obiekty blob
Biblioteka klienta obsługuje obecnie szyfrowanie tylko całych obiektów blob. W szczególności szyfrowanie jest obsługiwane, gdy użytkownicy używają **Metody UploadFrom** lub **OpenWrite** metody. W przypadku pobierania obsługiwane są zarówno pobieranie kompletne, jak i zakresowe.

Podczas szyfrowania biblioteka klienta wygeneruje losowy wektor inicjowania (IV) o 16 bajtach, wraz z kluczem szyfrowania zawartości losowej (CEK) 32 bajtów i wykona szyfrowanie kopert danych obiektów blob przy użyciu tych informacji. Opakowane CEK i niektóre dodatkowe metadane szyfrowania są następnie przechowywane jako metadane obiektów blob wraz z zaszyfrowanym obiektem blob w usłudze.

> [!WARNING]
> Jeśli edytujesz lub przekazujesz własne metadane obiektu blob, musisz upewnić się, że te metadane są zachowywane. Jeśli przekażesz nowe metadane bez tych metadanych, opakowane CEK, IV i inne metadane zostaną utracone, a zawartość obiektu blob nigdy nie będzie można pobrać ponownie.
> 
> 

Pobieranie zaszyfrowanego obiektu blob polega na pobraniu zawartości całego obiektu blob przy użyciu metody wygody **DownloadTo**/**BlobReadStream.** Opakowane CEK jest rozpakowany i używane razem z IV (przechowywane jako metadane obiektu blob w tym przypadku) do zwracania odszyfrowanych danych do użytkowników.

Pobieranie dowolnego zakresu **(Metody DownloadRange)** w zaszyfrowanym obiekcie blob polega na dostosowaniu zakresu dostarczonego przez użytkowników w celu uzyskania niewielkiej ilości dodatkowych danych, które mogą być używane do pomyślnego odszyfrowania żądanego zakresu.

Wszystkie typy obiektów blob (blokowe obiekty BLOB, obiekty blob stron i obiekty blob dołączania) mogą być szyfrowane/odszyfrowywane przy użyciu tego schematu.

### <a name="queues"></a>Kolejki
Ponieważ wiadomości kolejki mogą mieć dowolny format, biblioteka klienta definiuje format niestandardowy, który zawiera wektor inicjowania (IV) i zaszyfrowany klucz szyfrowania zawartości (CEK) w tekście wiadomości.

Podczas szyfrowania biblioteka klienta generuje losowe IV 16 bajtów wraz z losowym CEK 32 bajtów i wykonuje szyfrowanie koperty tekstu wiadomości kolejki przy użyciu tych informacji. Zawinięty CEK i niektóre dodatkowe metadane szyfrowania są następnie dodawane do zaszyfrowanego komunikatu kolejki. Ten zmodyfikowany komunikat (pokazany poniżej) jest przechowywany w usłudze.

    <MessageText>{"EncryptedMessageContents":"6kOu8Rq1C3+M1QO4alKLmWthWXSmHV3mEfxBAgP9QGTU++MKn2uPq3t2UjF1DO6w","EncryptionData":{…}}</MessageText>

Podczas odszyfrowywania opakowany klucz jest wyodrębniany z komunikatu kolejki i rozpakowany. IV jest również wyodrębniany z wiadomości kolejki i używany wraz z kluczem rozpakowanym do odszyfrowania danych wiadomości kolejki. Należy zauważyć, że metadane szyfrowania jest mały (poniżej 500 bajtów), więc podczas gdy nie wlicza się do limitu 64 KB dla wiadomości kolejki, wpływ powinien być w zarządzaniu.

### <a name="tables"></a>Tabele
Biblioteka klienta obsługuje szyfrowanie właściwości jednostki dla operacji wstawiania i zamieniania.

> [!NOTE]
> Scalanie nie jest obecnie obsługiwane. Ponieważ podzbiór właściwości może być zaszyfrowany wcześniej przy użyciu innego klucza, po prostu scalanie nowych właściwości i aktualizowanie metadanych spowoduje utratę danych. Scalanie wymaga wykonywania dodatkowych wywołań usługi, aby odczytać wcześniej istniejącej jednostki z usługi lub przy użyciu nowego klucza na właściwość, z których oba nie są odpowiednie ze względu na wydajność.
> 
> 

Szyfrowanie danych tabeli działa w następujący sposób:  

1. Użytkownicy określają właściwości, które mają być szyfrowane.
2. Biblioteka klienta generuje losowe Inicjowanie Vector (IV) z 16 bajtów wraz z kluczem szyfrowania zawartości losowej (CEK) 32 bajtów dla każdej jednostki i wykonuje szyfrowanie koperty na poszczególnych właściwości, które mają być zaszyfrowane przez wyprowadzanie nowego IV na właściwość. Szyfrowana właściwość jest przechowywana jako dane binarne.
3. Opakowane CEK i niektóre dodatkowe metadane szyfrowania są następnie przechowywane jako dwie dodatkowe właściwości zarezerwowane. Pierwsza właściwość zarezerwowana (_ClientEncryptionMetadata1) jest właściwość string, która przechowuje informacje o IV, version, i opakowane klucz. Druga właściwość zarezerwowana (_ClientEncryptionMetadata2) jest właściwością binarną, która przechowuje informacje o właściwościach, które są szyfrowane. Informacje w tej drugiej właściwości (_ClientEncryptionMetadata2) jest sama zaszyfrowana.
4. Ze względu na te dodatkowe właściwości zarezerwowane wymagane do szyfrowania użytkownicy mogą mieć teraz tylko 250 właściwości niestandardowych zamiast 252. Całkowity rozmiar jednostki musi być mniejszy niż 1 MB.

Należy zauważyć, że tylko właściwości ciągu mogą być szyfrowane. Jeśli inne typy właściwości mają być szyfrowane, muszą być konwertowane na ciągi. Zaszyfrowane ciągi są przechowywane w usłudze jako właściwości binarne i są konwertowane z powrotem na ciągi po odszyfrowaniu.

W przypadku tabel, oprócz zasad szyfrowania, użytkownicy muszą określić właściwości, które mają być szyfrowane. Można to zrobić, określając atrybut [EncryptProperty] (dla jednostek POCO, które pochodzą z TableEntity) lub program rozpoznawania szyfrowania w opcjach żądań. Program rozpoznawania nazw szyfrowania jest pełnomocnikiem, który przyjmuje klucz partycji, klucz wiersza i nazwę właściwości i zwraca wartość logiczną, która wskazuje, czy ta właściwość powinna być szyfrowana. Podczas szyfrowania biblioteka klienta użyje tych informacji, aby zdecydować, czy właściwość powinna być szyfrowana podczas zapisywania do sieci. Delegat zapewnia również możliwość logiki wokół jak właściwości są szyfrowane. (Na przykład, jeśli X, a następnie zaszyfrować właściwość A; w przeciwnym razie szyfrować właściwości A i B.) Należy zauważyć, że nie jest konieczne podanie tych informacji podczas odczytywania lub wykonywania zapytań jednostek.

### <a name="batch-operations"></a>Operacje wsadowe
W operacjach wsadowych ten sam KEK będzie używany we wszystkich wierszach w tej operacji wsadowej, ponieważ biblioteka klienta zezwala tylko na jeden obiekt opcji (a zatem jeden zasadę/KEK) na operację wsadową. Jednak biblioteka klienta wewnętrznie wygeneruje nowy losowy IV i losowy CEK na wiersz w partii. Użytkownicy mogą również szyfrować różne właściwości dla każdej operacji w partii, definiując to zachowanie w programze rozpoznawania szyfrowania.

### <a name="queries"></a>Zapytania
> [!NOTE]
> Ponieważ jednostki są szyfrowane, nie można uruchamiać kwerend, które filtrują na właściwości zaszyfrowanej.  Jeśli spróbujesz, wyniki będą niepoprawne, ponieważ usługa będzie próbować porównać zaszyfrowane dane z niezaszyfrowanymi danymi.
> 
> 
> Aby wykonać operacje kwerendy, należy określić program rozpoznawania kluczy, który jest w stanie rozpoznać wszystkie klucze w zestawie wyników. Jeśli jednostka zawarta w wyniku kwerendy nie może zostać rozpoznana dla dostawcy, biblioteka klienta zrzuci błąd. W przypadku każdej kwerendy wykonującej projekcje po stronie serwera biblioteka klienta domyślnie doda do wybranych kolumn specjalne właściwości metadanych szyfrowania (_ClientEncryptionMetadata1 i _ClientEncryptionMetadata2).

## <a name="azure-key-vault"></a>W usłudze Azure Key Vault
Usługa Azure Key Vault ułatwia ochronę kluczy kryptograficznych i kluczy tajnych używanych przez aplikacje i usługi w chmurze. Korzystając z usługi Azure Key Vault, użytkownicy mogą szyfrować klucze i wpisy tajne (takie jak klucze uwierzytelniania, klucze kont magazynu, klucze szyfrowania danych, . PFX i hasła) przy użyciu kluczy chronionych przez sprzętowe moduły zabezpieczeń (HSM). Aby uzyskać więcej informacji, zobacz [Co to jest usługa Azure Key Vault?](../../key-vault/general/overview.md).

Biblioteka klienta magazynu używa biblioteki podstawowej usługi Key Vault w celu zapewnienia wspólnej struktury na platformie Azure do zarządzania kluczami. Użytkownicy otrzymują również dodatkowe korzyści wynikające z korzystania z biblioteki rozszerzeń usługi Key Vault. Biblioteka rozszerzeń zapewnia przydatne funkcje wokół prostych i bezproblemowych dostawców kluczy lokalnych i bezpieczeństwa RSA, a także agregacji i buforowania.

### <a name="interface-and-dependencies"></a>Interfejs i zależności
Istnieją trzy pakiety Usługi Key Vault:

* Microsoft.Azure.KeyVault.Core zawiera iKey i IKeyResolver. Jest to mały pakiet bez zależności. Biblioteka klienta magazynu dla platformy .NET definiuje ją jako zależność.
* Usługa Microsoft.Azure.KeyVault zawiera klienta REST usługi Key Vault.
* Microsoft.Azure.KeyVault.Extensions zawiera kod rozszerzenia, który zawiera implementacje algorytmów kryptograficznych i RSAKey i SymmetricKey. To zależy od obszarów nazw Core i KeyVault i zapewnia funkcje do definiowania agregacji rozpoznawania nazw (gdy użytkownicy chcą używać wielu dostawców kluczy) i buforowania klucza rozpoznawania nazw. Chociaż biblioteka klienta magazynu nie zależy bezpośrednio od tego pakietu, jeśli użytkownicy chcą używać usługi Azure Key Vault do przechowywania kluczy lub do korzystania z rozszerzeń usługi Key Vault do korzystania z dostawców kryptograficznych lokalnych i chmurowych, będą potrzebować tego pakietu.

Usługa Key Vault jest przeznaczona do kluczy głównych o wysokiej wartości, a limity ograniczania przepustowości na magazyn kluczy są zaprojektowane z myślą o tym. Podczas szyfrowania po stronie klienta za pomocą usługi Key Vault preferowanym modelem jest użycie symetrycznych kluczy głównych przechowywanych jako klucze tajne w magazynie kluczy kluczowych i buforowanych lokalnie. Użytkownicy muszą wykonać następujące czynności:

1. Utwórz klucz tajny w trybie offline i przekaż go do usługi Key Vault.
2. Użyj identyfikatora podstawowego klucza jako parametru, aby rozwiązać bieżącą wersję klucza tajnego do szyfrowania i buforować te informacje lokalnie. Użyj CachingKeyResolver do buforowania; użytkownicy nie powinni implementować własnej logiki buforowania.
3. Użyj programu rozpoznawania nazw buforowania jako danych wejściowych podczas tworzenia zasad szyfrowania.

Więcej informacji na temat użycia usługi Key Vault można znaleźć w [przykładach kodu szyfrowania](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples).

## <a name="best-practices"></a>Najlepsze rozwiązania
Obsługa szyfrowania jest dostępna tylko w bibliotece klienta magazynu dla platformy .NET. Systemy Windows Phone i Windows Runtime obecnie nie obsługują szyfrowania.

> [!IMPORTANT]
> Należy pamiętać o tych ważnych punktach podczas korzystania z szyfrowania po stronie klienta:
> 
> * Podczas odczytywania lub zapisywania do zaszyfrowanego obiektu blob należy używać poleceń przekazywania całych obiektów blob i poleceń pobierania zakresu/całego obiektu blob. Unikaj zapisywania do zaszyfrowanego obiektu blob przy użyciu operacji protokołu, takich jak Put Block, Put Block List, Write Pages, Clear Pages lub Dołącz blok; w przeciwnym razie możesz uszkodzić zaszyfrowany obiekt blob i uczynić go nieczytelnym.
> * W przypadku tabel istnieje podobne ograniczenie. Należy uważać, aby nie aktualizować zaszyfrowanych właściwości bez aktualizowania metadanych szyfrowania.
> * Jeśli ustawisz metadane na zaszyfrowanym obiekcie blob, możesz zastąpić metadane związane z szyfrowaniem wymagane do odszyfrowywania, ponieważ ustawienie metadanych nie jest addytywne. Dotyczy to również migawek; uniknąć określania metadanych podczas tworzenia migawki zaszyfrowanego obiektu blob. Jeśli metadane muszą być ustawione, należy najpierw wywołać **FetchAttributes** metody, aby uzyskać bieżące metadane szyfrowania i uniknąć równoczesnych zapisów podczas ustawiania metadanych.
> * Włącz **właściwość RequireEncryption** w domyślnych opcjach żądań dla użytkowników, którzy powinni pracować tylko z zaszyfrowanymi danymi. Zobacz poniżej, aby uzyskać więcej informacji.
> 
> 

## <a name="client-api--interface"></a>Interfejs / interfejs klienta
Podczas tworzenia obiektu SzyfrowaniePolicy, użytkownicy mogą podać tylko klucz (implementowanie IKey), tylko program rozpoznawania nazw (implementowanie IKeyResolver) lub obu. IKey jest podstawowym typem klucza, który jest identyfikowany przy użyciu identyfikatora klucza i który zapewnia logikę zawijania/rozpakowywania. IKeyResolver jest używany do rozpoznawania klucza podczas procesu odszyfrowywania. Definiuje ResolveKey metoda, która zwraca IKey nadany identyfikator klucza. Dzięki temu użytkownicy mogą wybierać między wieloma kluczami, które są zarządzane w wielu lokalizacjach.

* W przypadku szyfrowania klucz jest zawsze używany, a brak klucza spowoduje błąd.
* Do odszyfrowywania:
  * Program rozpoznawania kluczy jest wywoływany, jeśli określono, aby uzyskać klucz. Jeśli program rozpoznawania nazw jest określony, ale nie ma mapowania dla identyfikatora klucza, zgłaszany jest błąd.
  * Jeśli program rozpoznawania nazw nie jest określony, ale klucz jest określony, klucz jest używany, jeśli jego identyfikator jest zgodny z wymaganym identyfikatorem klucza. Jeśli identyfikator nie jest zgodny, zgłaszany jest błąd.

Przykłady kodu w tym artykule pokazują ustawienie zasad szyfrowania i pracy z zaszyfrowanymi danymi, ale nie wykazują pracy z usługą Azure Key Vault. [Przykłady szyfrowania](https://github.com/Azure/azure-storage-net/tree/master/Samples/GettingStarted/EncryptionSamples) w usłudze GitHub pokazują bardziej szczegółowy scenariusz end-to-end dla obiektów blob, kolejek i tabel wraz z integracją usługi Key Vault.

### <a name="requireencryption-mode"></a>Tryb szyfrowania requireEncryption
Użytkownicy mogą opcjonalnie włączyć tryb działania, w którym wszystkie przesyłanie i pobieranie musi być zaszyfrowane. W tym trybie próby przekazania danych bez zasad szyfrowania lub danych pobierania, które nie są szyfrowane w usłudze, zakończy się niepowodzeniem na kliencie. **Właściwość RequireEncryption** obiektu opcji żądania kontroluje to zachowanie. Jeśli aplikacja będzie szyfrować wszystkie obiekty przechowywane w usłudze Azure Storage, a następnie można ustawić **RequireEncryption** właściwość dla domyślnych opcji żądania dla obiektu klienta usługi. Na przykład ustaw **CloudBlobClient.DefaultRequestOptions.RequireEncryption** **true** wymaga szyfrowania dla wszystkich operacji obiektów blob wykonywanych za pośrednictwem tego obiektu klienta.


### <a name="blob-service-encryption"></a>Szyfrowanie usługi obiektów Blob
Utwórz obiekt **BlobEncryptionPolicy** i ustaw go w opcjach żądania (na interfejs API lub na poziomie klienta przy użyciu **defaultrequestoptions**). Wszystko inne będzie obsługiwane przez bibliotekę klienta wewnętrznie.

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

### <a name="queue-service-encryption"></a>Szyfrowanie usługi kolejek
Utwórz obiekt **QueueEncryptionPolicy** i ustaw go w opcjach żądania (na interfejs API lub na poziomie klienta przy użyciu **defaultrequestoptions**). Wszystko inne będzie obsługiwane przez bibliotekę klienta wewnętrznie.

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

### <a name="table-service-encryption"></a>Szyfrowanie usługi tabel
Oprócz tworzenia zasad szyfrowania i ustawiania jej na opcje żądania, należy określić **EncryptionResolver** w **TableRequestOptions**lub ustawić atrybut [EncryptProperty] dla jednostki.

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

#### <a name="using-attributes"></a>Korzystanie z atrybutów
Jak wspomniano powyżej, jeśli jednostka implementuje TableEntity, właściwości mogą być ozdobione atrybutem [EncryptProperty] zamiast określania **EncryptionResolver**.

```csharp
[EncryptProperty]
 public string EncryptedProperty1 { get; set; }
```

## <a name="encryption-and-performance"></a>Szyfrowanie i wydajność
Należy zauważyć, że szyfrowanie danych magazynu powoduje dodatkowe obciążenie wydajności. Klucz zawartości i IV muszą być generowane, sama zawartość musi być zaszyfrowana, a dodatkowe meta-dane muszą być sformatowane i przesłane. To obciążenie będzie się różnić w zależności od ilości zaszyfrowanych danych. Zaleca się, aby klienci zawsze testowali swoje aplikacje pod kątem wydajności podczas tworzenia.

## <a name="next-steps"></a>Następne kroki
* [Samouczek: Szyfrowanie i odszyfrowywanie obiektów blob w usłudze Microsoft Azure Storage przy użyciu usługi Azure Key Vault](../blobs/storage-encrypt-decrypt-blobs-key-vault.md)
* Pobierz [bibliotekę klienta usługi Azure Storage dla pakietu NuGet .NET](https://www.nuget.org/packages/WindowsAzure.Storage)
* Pobierz pakiety Azure Key Vault NuGet [Core](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core/), [Client](https://www.nuget.org/packages/Microsoft.Azure.KeyVault/)i [Extensions](https://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions/)  
* Odwiedź [dokumentację usługi Azure Key Vault](../../key-vault/general/overview.md)
