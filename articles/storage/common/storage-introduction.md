---
title: Wprowadzenie do usługi Azure Storage — magazynu w chmurze na platformie Azure | Microsoft Docs
description: Podstawową platformą usługi Azure Storage jest rozwiązanie magazynu w chmurze firmy Microsoft. Usługa Azure Storage oferuje magazyn dla obiektów danych cechujący się wysoką dostępnością, bezpieczeństwem, trwałością, wysoką skalowalnością i nadmiarowością.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 7bb4cca6f58cb4ad0722c1407d2ef3062c3747e2
ms.sourcegitcommit: 400f473e8aa6301539179d4b320ffbe7dfae42fe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/28/2020
ms.locfileid: "92781961"
---
# <a name="introduction-to-the-core-azure-storage-services"></a>Wprowadzenie do podstawowych usług Azure Storage

Platforma Azure Storage to rozwiązanie magazynu w chmurze firmy Microsoft dla nowoczesnych scenariuszy magazynowania danych. Podstawowe usługi magazynu oferują wysoce skalowalny magazyn obiektów dla obiektów danych, magazynu dyskowego dla maszyn wirtualnych platformy Azure, usługi systemu plików dla chmury, magazynu komunikatów dla niezawodnej obsługi komunikatów i magazynu NoSQL. Usługi to:

- **Niezawodność i wysoka dostępność.** Nadmiarowość zapewnia bezpieczeństwo danych w przypadku przejściowych awarii sprzętu. Dla dodatkowej ochrony przed lokalnymi katastrofami lub klęskami żywiołowymi można również zdecydować się na replikowanie danych między centrami danych lub regionami geograficznymi. Dane replikowane w ten sposób pozostają wysoce dostępne w przypadku awarii.
- **Zapewnienia.** Wszystkie dane zapisywane na koncie usługi Azure Storage są szyfrowane przez usługę. Usługa Azure Storage zapewnia precyzyjną kontrolę nad tym, kto ma dostęp do danych.
- **Strategia.** Usługa Azure Storage została zaprojektowana jako wysoce skalowalne rozwiązanie spełniające potrzeby związane z magazynowaniem danych i wydajnością współczesnych aplikacji.
- **Zarządzanych.** Platforma Azure obsługuje konserwację sprzętu, aktualizacje i problemy krytyczne.
- **Dostępność.** Dane w usłudze Azure Storage są dostępne z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Firma Microsoft udostępnia biblioteki klienckie usługi Azure Storage w różnych językach, w tym .NET, Java, Node.js, Python, PHP, Ruby, go i innych, a także interfejs API REST. Usługa Azure Storage obsługuje skrypty programu Azure PowerShell lub interfejsu wiersza polecenia platformy Azure. Witryna Azure Portal oraz Eksplorator usługi Azure Storage oferują proste rozwiązania wizualne do pracy z danymi.  

## <a name="core-storage-services"></a>Podstawowe usługi magazynu

Platforma Azure Storage obejmuje następujące usługi danych:

- [Azure Blobs](../blobs/storage-blobs-introduction.md): wysoce skalowalny magazyn obiektów dla danych tekstowych i binarnych. Obejmuje również obsługę analizy danych Big Data za pomocą Data Lake Storage Gen2.
- [Azure Files](../files/storage-files-introduction.md): zarządzane udziały plików dla wdrożeń w chmurze lub lokalnych.
- [Azure Queues](../queues/storage-queues-introduction.md): magazyn obsługi komunikatów zapewniający niezawodną obsługę komunikatów między składnikami aplikacji.
- [Azure Tables](../tables/table-storage-overview.md): magazyn obiektów NoSQL do magazynowania ustrukturyzowanych danych bez użycia schematu.
- [Azure disks](../../virtual-machines/managed-disks-overview.md): woluminy magazynu na poziomie bloku dla maszyn wirtualnych platformy Azure.

Dostęp do każdej usługi uzyskuje się za pośrednictwem konta magazynu. Aby rozpocząć, zobacz artykuł [Tworzenie konta magazynu](storage-account-create.md).

## <a name="example-scenarios"></a>Przykładowe scenariusze

Poniższa tabela zawiera porównanie plików, obiektów blob, dysków, kolejek i tabel oraz przykładowe scenariusze dla każdego z nich.

| Cechy | Opis | Kiedy stosować |
|--------------|-------------|-------------|
| **Azure Files** |Oferuje w pełni zarządzane udziały plików w chmurze, do których można uzyskać dostęp z dowolnego miejsca za pośrednictwem protokołu SMB (Industry Standard Server Message Block).<br><br>Udziały plików platformy Azure można instalować z lokalnych wdrożeń systemu Windows, Linux i macOS. | Chcesz "podnieść i przenieść" aplikację do chmury, która korzysta już z natywnych interfejsów API systemu plików do udostępniania danych między nimi a innymi aplikacjami uruchomionymi na platformie Azure.<br/><br/>Chcesz zastąpić lub uzupełnić lokalne serwery plików lub urządzenia NAS.<br><br> Chcesz przechowywać narzędzia do tworzenia i debugowania, które muszą być dostępne z wielu maszyn wirtualnych. |
| **Obiekty blob platformy Azure** | Umożliwia przechowywanie danych bez struktury i uzyskiwanie do nich dostępu w dużej skali w blokowych obiektach Blob.<br/><br/>Program obsługuje również [Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) w przypadku rozwiązań do analizy danych Big Data w przedsiębiorstwie. | Chcesz, aby aplikacja obsługiwała scenariusze przesyłania strumieniowego i dostępu losowego.<br/><br/>Chcesz mieć możliwość dostępu do danych aplikacji z dowolnego miejsca.<br/><br/>Chcesz utworzyć przedsiębiorstwo Data Lake na platformie Azure i przeprowadzić analizę danych Big Data. |
| **Azure Disks** | Umożliwia trwałe przechowywanie danych i uzyskiwanie do nich dostępu z dołączonego wirtualnego dysku twardego. | Chcesz "podnieść i przenieść" aplikacje, które używają natywnych interfejsów API systemu plików do odczytu i zapisu danych na dyskach trwałych.<br/><br/>Chcesz przechowywać dane, które nie są wymagane, aby można było uzyskać do nich dostęp spoza maszyny wirtualnej, do której dysk jest podłączony. |
| **Kolejki platformy Azure** | Umożliwia asynchroniczne kolejkowanie komunikatów między składnikami aplikacji. | Chcesz oddzielić składniki aplikacji i używać komunikatów asynchronicznych do komunikacji między nimi.<br><br>Wskazówki dotyczące korzystania z usługi queue storage i kolejek Service Bus można znaleźć w temacie [kolejki magazynu i kolejki Service Bus — w porównaniu z kontrastami](../../service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted.md). |
| **Tabele platformy Azure** | Umożliwiają przechowywanie strukturalnych danych NoSQL w chmurze, zapewniając Magazyn kluczy/atrybutów z projektem bez schematu. | Chcesz przechowywać elastyczne zestawy danych, takie jak dane użytkowników dla aplikacji sieci Web, książki adresowe, informacje o urządzeniach lub inne typy metadanych, których potrzebuje Twoja usługa. <br/><br/>Aby uzyskać wskazówki dotyczące korzystania z usługi Table Storage i interfejs API tabel Azure Cosmos DB, zobacz [Tworzenie aplikacji za pomocą Azure Cosmos DB interfejs API tabel i usługi Azure Table Storage](../../cosmos-db/table-support.md). |

## <a name="blob-storage"></a>Blob Storage

Azure Blob Storage to rozwiązanie do magazynowania obiektów w chmurze firmy Microsoft. Usługa Blob Storage jest zoptymalizowana pod kątem przechowywania olbrzymich ilości danych bez struktury, takich jak dane tekstowe lub binarne.

Usługa Blob Storage to idealne rozwiązanie w następujących przypadkach:

- Obsługiwanie obrazów i dokumentów bezpośrednio w przeglądarce.
- Przechowywanie plików do dostępu rozproszonego.
- Przesyłanie strumieniowe audio i wideo.
- Zapisywanie danych w celu tworzenia kopii zapasowych, przywracania, odzyskiwania po awarii i archiwizowania.
- Przechowywanie danych w celu analizy w usłudze lokalnej lub hostowanej na platformie Azure.

Dostęp do obiektów w usłudze Blob Storage można uzyskać z dowolnego miejsca na świecie za pośrednictwem protokołu HTTP lub HTTPS. Użytkownicy lub aplikacje klienckie mogą uzyskiwać dostęp do obiektów blob za pomocą adresów URL, [interfejsu API REST usługi Azure Storage](/rest/api/storageservices/blob-service-rest-api), [programu Azure PowerShell](/powershell/module/azure.storage), [interfejsu wiersza polecenia platformy Azure](/cli/azure/storage) lub biblioteki klienta usługi Azure Storage. Biblioteki klienta magazynu są dostępne dla wielu języków, w tym [.NET](/dotnet/api/overview/azure/storage), [Java](/java/api/overview/azure/storage), [Node.js](https://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/), [PHP](https://azure.github.io/azure-storage-php/) i [Ruby](https://azure.github.io/azure-storage-ruby).

Aby uzyskać więcej informacji dotyczących usługi Blob Storage, zobacz [Introduction to Blob storage (Wprowadzenie do usługi Blob Storage)](../blobs/storage-blobs-introduction.md).

## <a name="azure-files"></a>Azure Files

[Azure Files](../files/storage-files-introduction.md) umożliwia skonfigurowanie udziałów plików o wysokiej dostępności, do których można uzyskać dostęp przy użyciu standardowego protokołu bloku komunikatów serwera (SMB). Oznacza to, że wiele maszyn wirtualnych może współdzielić te same pliki z dostępem zarówno do odczytu, jak i do zapisu. Pliki można także odczytywać przy użyciu interfejsu REST lub bibliotek klienckich magazynu.

Jedną z różnic między usługą Azure Files i plikami w firmowym udziale plików jest możliwość dostępu do plików z dowolnego miejsca na świecie przy użyciu adresu URL, który wskazuje plik i zawiera token sygnatury dostępu współdzielonego. Tokeny sygnatury dostępu współdzielonego można generować. Umożliwiają one uzyskanie określonego dostępu do prywatnego zasobu przez określony czas.

Udziałów plików można używać w wielu typowych scenariuszach:

- Wiele aplikacji lokalnych korzysta z udziałów plików. Dzięki temu migracja tych aplikacji współdzielących dane na platformę Azure jest łatwiejsza. Jeśli zainstalujesz udział plików przy użyciu tej samej litery dysku, która jest używana przez aplikację lokalną, część aplikacji uzyskująca dostęp do udziału plików powinna działać bez konieczności wprowadzania zmian lub jedynie z minimalnymi zmianami.

- Pliki konfiguracji można przechowywać w udziale plików i uzyskiwać do nich dostęp z wielu maszyn wirtualnych. W udziale plików można przechowywać narzędzia i programy narzędziowe używane przez wielu deweloperów w grupie, dzięki czemu wszyscy będą mogli je znaleźć oraz będą używać tych samych wersji.

- Dzienniki zasobów, metryki i Zrzuty awaryjne to trzy przykładowe dane, które można zapisać w udziale plików i przetworzyć lub przeanalizować później.

Aby uzyskać więcej informacji na temat usługi Azure Files, zobacz [Wprowadzenie do usługi Azure Files](../files/storage-files-introduction.md).

Niektóre funkcje protokołu SMB nie mają zastosowania do chmury. Aby uzyskać więcej informacji, zobacz [funkcje nieobsługiwane przez usługę plików platformy Azure](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).

## <a name="queue-storage"></a>Queue Storage

Usługa Azure Queue jest używana do przechowywania i pobierania komunikatów. Komunikaty kolejek mogą mieć rozmiar do 64 KB, a jedna kolejka może zawierać miliony komunikatów. Kolejki są zazwyczaj używane do przechowywania list komunikatów, które mają zostać przetworzone asynchronicznie.

Przykładowo załóżmy, że chcesz, aby klienci mogli przekazywać obrazy, i chcesz utworzyć miniatury dla każdego obrazu. Klient może poczekać, aż utworzysz miniatury podczas przekazywania zdjęcia. Alternatywą jest zastosowanie kolejki. Po zakończeniu przekazywania przez klienta Napisz komunikat do kolejki. Następnie funkcja usługi Azure Functions pobierze ten komunikat z kolejki i utworzy miniatury. Każdą część tego przetwarzania można skalować oddzielnie, co daje większą kontrolę podczas dostosowywania.

Aby uzyskać więcej informacji na temat usługi Azure Queues, zobacz [Wprowadzenie do usługi Azure Queues](../queues/storage-queues-introduction.md).

## <a name="table-storage"></a>Table Storage

Usługa Azure Table Storage jest teraz częścią usługi Azure Cosmos DB. Aby zapoznać się z dokumentacją usługi Azure Table Storage, przejdź do artykułu [Omówienie usługi Azure Table Storage](../tables/table-storage-overview.md). Oprócz istniejącej usługi Azure Table Storage dostępna jest nowa oferta interfejsu API tabel usługi Azure Cosmos DB, który zapewnia tabele zoptymalizowane pod kątem przepływności, globalną dystrybucję i automatyczne indeksy pomocnicze. Aby dowiedzieć się więcej i wypróbować nowe środowisko Premium, zobacz [Azure Cosmos DB interfejs API tabel](../../cosmos-db/table-introduction.md).

Aby uzyskać więcej informacji na temat usługi Table Storage, zobacz [Omówienie usługi Azure Table Storage](../tables/table-storage-overview.md).

## <a name="disk-storage"></a>Przechowywanie na dysku

Dysk zarządzany przez platformę Azure to wirtualny dysk twardy (VHD). Można traktować go jako dysk fizyczny na serwerze lokalnym, ale zwirtualizowany. Dyski zarządzane przez platformę Azure są przechowywane jako stronicowe obiekty blob, które są losowym obiektem magazynu we/wy na platformie Azure. Nazywamy zarządzany dysk zarządzany, ponieważ jest to Abstrakcja dla stronicowych obiektów blob, kontenerów obiektów blob i kont usługi Azure Storage. W przypadku dysków zarządzanych wszystkie czynności, które należy wykonać, udostępniają dysk, a platforma Azure zajmie się resztą.

Aby uzyskać więcej informacji o dyskach zarządzanych, zobacz [wprowadzenie do usługi Azure Managed disks](../../virtual-machines/managed-disks-overview.md).

## <a name="types-of-storage-accounts"></a>Typy kont magazynu

Usługa Azure Storage oferuje kilka typów kont magazynu. Każdy typ obsługuje różne funkcje i ma własny model cen. Aby uzyskać więcej informacji dotyczących typów kont magazynu, zobacz temat [Przegląd konta usługi Azure Storage](storage-account-overview.md).

## <a name="secure-access-to-storage-accounts"></a>Bezpieczny dostęp do kont magazynu

Każde żądanie do usługi Azure Storage musi być autoryzowane. Usługa Azure Storage obsługuje następujące metody autoryzacji:

- **Azure Active Directory (Azure AD) integracja dla danych obiektów blob i kolejek.** Usługa Azure Storage obsługuje uwierzytelnianie i autoryzację w usłudze Azure AD dla usług obiektów blob i kolejek za pośrednictwem kontroli dostępu opartej na rolach (Azure RBAC). Autoryzacja żądań za pomocą usługi Azure AD jest zalecana w celu zapewnienia bezpieczeństwa i łatwość użycia. Aby uzyskać więcej informacji, zobacz [Autoryzuj dostęp do obiektów blob i kolejek platformy Azure przy użyciu Azure Active Directory](storage-auth-aad.md).
- **Autoryzacja usługi Azure AD za pośrednictwem protokołu SMB dla Azure Files.** Azure Files obsługuje autoryzację opartą na tożsamościach za pośrednictwem protokołu SMB (Server Message Block) za pośrednictwem Azure Active Directory Domain Services (Azure AD DS) lub lokalnego Active Directory Domain Services (wersja zapoznawcza). Przyłączone do domeny maszyny wirtualne z systemem Windows mogą uzyskiwać dostęp do udziałów plików platformy Azure przy użyciu poświadczeń usługi Azure AD. Aby uzyskać więcej informacji, zobacz [omówienie Azure Files obsługi uwierzytelniania opartego na tożsamościach na potrzeby dostępu do protokołu SMB](../files/storage-files-active-directory-overview.md) i [planowania wdrożenia Azure Files](../files/storage-files-planning.md#identity).
- **Autoryzacja za pomocą klucza współużytkowanego.** Usługi Azure Storage Blob, plików, kolejek i tabel obsługują autoryzację za pomocą klucza współużytkowanego. Klient korzystający z autoryzacji klucza wspólnego przekazuje nagłówek z każdym żądaniem podpisanym przy użyciu klucza dostępu konta magazynu. Aby uzyskać więcej informacji, zobacz [Autoryzuj przy użyciu klucza współużytkowanego](/rest/api/storageservices/authorize-with-shared-key).
- **Autoryzacja przy użyciu sygnatur dostępu współdzielonego (SAS).** Sygnatura dostępu współdzielonego (SAS) jest ciągiem zawierającym token zabezpieczający, który może być dołączany do identyfikatora URI zasobu magazynu. Token zabezpieczający hermetyzuje ograniczenia, takie jak uprawnienia i interwał dostępu. Aby uzyskać więcej informacji, zobacz [Używanie sygnatur dostępu współdzielonego (SAS)](storage-sas-overview.md).
- **Anonimowy dostęp do kontenerów i obiektów BLOB.** Kontener i jego obiekty blob mogą być publicznie dostępne. Po określeniu, że kontener lub obiekt BLOB jest publiczny, każda osoba może ją odczytać anonimowo; nie jest wymagane żadne uwierzytelnianie. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem anonimowym do odczytu do kontenerów i obiektów BLOB](../blobs/anonymous-read-access-configure.md).

## <a name="encryption"></a>Szyfrowanie

Istnieją dwa podstawowe rodzaje szyfrowania dostępne dla podstawowych usług magazynu. Aby uzyskać więcej informacji na temat bezpieczeństwa i szyfrowania, zobacz [Azure Storage security guide (Przewodnik po zabezpieczeniach usługi Azure Storage)](../blobs/security-recommendations.md).

### <a name="encryption-at-rest"></a>Szyfrowanie w spoczynku

Szyfrowanie usługi Azure Storage chroni i zabezpiecza dane zgodnie ze zobowiązaniami dotyczącymi zabezpieczeń i zgodności w organizacji. Usługa Azure Storage automatycznie szyfruje wszystkie dane przed utrwaleniem do konta magazynu i odszyfrowuje je przed pobraniem. Procesy szyfrowania, odszyfrowywania i zarządzania kluczami są niewidoczne dla użytkowników. Klienci mogą również zdecydować się na zarządzanie własnymi kluczami przy użyciu Azure Key Vault. Aby uzyskać więcej informacji, zobacz [szyfrowanie usługi Azure Storage dla danych magazynowanych](storage-service-encryption.md).

### <a name="client-side-encryption"></a>Szyfrowania po stronie klienta

Biblioteki klienta usługi Azure Storage zapewniają metody szyfrowania danych z biblioteki klienta przed wysłaniem ich przez sieć i Odszyfrowanie odpowiedzi. Dane szyfrowane za pomocą szyfrowania po stronie klienta są również szyfrowane przez usługę Azure Storage. Aby uzyskać więcej informacji o szyfrowaniu po stronie klienta, zobacz [szyfrowanie po stronie klienta za pomocą platformy .NET dla usługi Azure Storage](storage-client-side-encryption.md).

## <a name="redundancy"></a>Nadmiarowość

Aby mieć pewność, że dane są trwałe, usługa Azure Storage przechowuje wiele kopii danych. Podczas konfigurowania konta magazynu należy wybrać opcję nadmiarowości. Aby uzyskać więcej informacji, zobacz [Nadmiarowość usługi Azure Storage](./storage-redundancy.md?toc=%252fazure%252fstorage%252fblobs%252ftoc.json).

## <a name="transfer-data-to-and-from-azure-storage"></a>Transferowanie danych do i z usługi Azure Storage

Jest dostępnych kilka opcji umożliwiających przenoszenie danych do usługi Azure Storage lub poza nią. Wybór opcji zależy od rozmiaru zestawu danych i przepustowości sieci. Aby uzyskać więcej informacji, zobacz [Wybieranie rozwiązania platformy Azure do transferu danych](storage-choose-data-transfer-solution.md).

## <a name="pricing"></a>Cennik

Przy podejmowaniu decyzji o sposobie przechowywania danych i uzyskiwania do nich dostępu należy również wziąć pod uwagę koszty związane z nimi. Aby uzyskać więcej informacji, zobacz [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

## <a name="storage-apis-libraries-and-tools"></a>Narzędzia, biblioteki oraz interfejsy API usługi Storage

Możesz uzyskać dostęp do zasobów na koncie magazynu za pomocą dowolnego języka, który może wykonywać żądania HTTP/HTTPS. Ponadto podstawowe usługi Azure Storage oferują biblioteki programistyczne dla kilku popularnych języków. Te biblioteki upraszczają wiele aspektów pracy z usługą Azure Storage dzięki obsłudze szczegółów, takich jak wywołania synchroniczne i asynchroniczne, przetwarzanie wsadowe operacji, zarządzanie wyjątkami, automatyczne ponawianie, zachowania podczas działania itd. Biblioteki są obecnie dostępne dla następujących języków i platform, a kolejne są planowane:

### <a name="azure-storage-data-api-and-library-references"></a>Dokumentacja bibliotek i interfejsów API danych usługi Azure Storage

- [Usługa Azure Storage — interfejs API REST](/rest/api/storageservices/)
- [Biblioteka klienta usługi Azure Storage dla platformy .NET](/dotnet/api/overview/azure/storage)
- [Biblioteka klienta usługi Azure Storage dla środowiska Java/Android](/java/api/overview/azure/storage)
- [Biblioteka klienta usługi Azure Storage dla Node.js](/javascript/api/overview/azure/storage-overview)
- [Biblioteka klienta usługi Azure Storage dla języka Python](https://github.com/Azure/azure-storage-python)
- [Biblioteka klienta usługi Azure Storage dla języka PHP](https://github.com/Azure/azure-storage-php)
- [Biblioteka klienta usługi Azure Storage dla języka Ruby](https://github.com/Azure/azure-storage-ruby)
- [Biblioteka klienta usługi Azure Storage dla języka C++](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>Dokumentacja bibliotek i interfejsów API zarządzania usługi Azure Storage

- [Interfejs API REST dostawcy zasobów usługi Storage](/rest/api/storagerp/)
- [Biblioteka klienta dostawcy zasobów usługi Storage dla platformy .NET](/dotnet/api/overview/azure/storage/management)
- [Interfejs API REST zarządzania usługą Storage (klasyczny)](/previous-versions/azure/reference/ee460790(v=azure.100))

### <a name="azure-storage-data-movement-api-and-library-references"></a>Dokumentacja bibliotek i interfejsów API ruchu danych usługi Azure Storage

- [Interfejs API REST usługi Import/Eksport usługi Storage](/rest/api/storageimportexport/)
- [Biblioteka klienta przenoszenia danych usługi Storage dla platformy .NET](/dotnet/api/microsoft.azure.storage.datamovement)

### <a name="tools-and-utilities"></a>Narzędzia i programy narzędziowe

- [Polecenia cmdlet programu Azure PowerShell dla usługi Storage](/powershell/module/az.storage)
- [Polecenia cmdlet interfejsu wiersza polecenia platformy Azure dla usługi Storage](/cli/azure/storage)
- [Narzędzie AzCopy Command-Line](https://aka.ms/downloadazcopy)
- [Eksplorator usługi Azure Storage](https://azure.microsoft.com/features/storage-explorer/) jest bezpłatną, autonomiczną aplikacją oferowaną przez firmę Microsoft, która umożliwia wizualną pracę z danymi w usłudze Azure Storage w systemach Windows, macOS i Linux.
- [Szablony Azure Resource Manager dla usługi Azure Storage](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage)

## <a name="next-steps"></a>Następne kroki

Aby zacząć korzystać z podstawowych usług Azure Storage, zobacz [Tworzenie konta magazynu](storage-account-create.md).