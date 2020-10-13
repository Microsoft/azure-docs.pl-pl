---
title: Zarządzanie danymi w usłudze Azure Blob Storage i znajdowanie ich przy użyciu indeksu obiektów BLOB (wersja zapoznawcza)
description: Dowiedz się, jak używać tagów indeksu obiektów BLOB do kategoryzowania i wykonywania zapytań dotyczących obiektów blob oraz zarządzania nimi.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/17/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: hux
ms.custom: references_regions
ms.openlocfilehash: db23d3b5c532a1539936b51222345c98679c554c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91817540"
---
# <a name="manage-and-find-azure-blob-data-with-blob-index-preview"></a>Zarządzanie danymi obiektów blob platformy Azure i znajdowanie ich przy użyciu indeksu obiektów BLOB (wersja zapoznawcza)

W miarę jak zestawy danych są większe i większe, znalezienie określonego obiektu na morzu może być trudne i frustrującee. Indeks obiektów BLOB umożliwia zarządzanie danymi i ich odnajdywanie przy użyciu atrybutów tagów indeksu wartości klucz-wartość, które umożliwiają kategoryzowanie i znajdowanie obiektów w ramach jednego kontenera lub wszystkich kontenerów na koncie magazynu. Później, zgodnie z wymaganiami zmian danych, obiekty mogą być dynamicznie kategoryzowane przez aktualizację ich tagów indeksu, a pozostałe w miejscu wraz z bieżącą organizacją kontenera. Używanie indeksu obiektów BLOB może uprościć programowanie przez skonsolidowanie danych obiektów blob i skojarzonych z nimi atrybutów indeksu w tej samej usłudze; umożliwienie tworzenia wydajnych i skalowalnych aplikacji przy użyciu funkcji natywnych.

Indeks obiektów BLOB umożliwia:

- Dynamiczne kategoryzowanie obiektów BLOB przy użyciu tagów indeksu wartości kluczowych dla zarządzania danymi
- Szybkie znajdowanie określonych otagowanych obiektów BLOB w ramach jednego kontenera lub całego konta magazynu
- Określ zachowania warunkowe dla interfejsów API obiektów BLOB na podstawie oceny tagów indeksu
- Korzystanie z tagów indeksu dla zaawansowanych kontrolek funkcji platformy obiektów blob, takich jak [Zarządzanie cyklem życia](storage-lifecycle-management-concepts.md)

Rozważmy scenariusz, w którym masz miliony obiektów BLOB na koncie magazynu, które są zapisywane i dostępne dla wielu różnych aplikacji. Chcesz znaleźć wszystkie powiązane dane z pojedynczego projektu, ale nie masz pewności, co znajduje się w zakresie, ponieważ dane mogą być rozłożone między wiele kontenerów z różnymi konwencjami nazewnictwa obiektów BLOB. Należy jednak pamiętać, że aplikacje przekazują wszystkie dane za pomocą tagów w oparciu o odpowiedni projekt i identyfikujący opis. Zamiast wyszukiwania przez miliony obiektów blob i porównywania nazw i właściwości, można po prostu użyć `Project = Contoso` jako kryterium odnajdywania. Indeks obiektów BLOB będzie filtrować wszystkie kontenery na całym koncie magazynu, aby szybko znajdować i zwracać tylko zestaw 50 obiektów blob z `Project = Contoso` .

Aby rozpocząć pracę z przykładami dotyczącymi używania indeksu obiektów blob, zobacz temat [Korzystanie z indeksu obiektów BLOB w celu zarządzania danymi i znajdowania](storage-blob-index-how-to.md)ich.

## <a name="blob-index-tags-and-data-management"></a>Tagi indeksu obiektów blob i zarządzanie danymi

Prefiksy nazw kontenerów i obiektów BLOB to Jednowymiarowa Kategoryzacja przechowywanych danych. Indeks obiektów BLOB umożliwia teraz kategoryzację wielowymiarową dla wszystkich [typów danych obiektów BLOB (blok, dołączenie lub strona)](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) z zastosowaniem tagów atrybutów. Ta wielowymiarowa Kategoryzacja jest natywnie indeksowana i udostępniana do szybkiego wykonywania zapytań i znajdowania danych.

Rozważ następujące pięć obiektów BLOB na koncie magazynu:

- *container1/transaction.csv*
- *container2/campaign.docx*
- *fotografie/bannerphoto.png*
- *Archiwa/ukończone/2019review.pdf*
- *Dzienniki/2020/01/01/logfile.txt*


Te obiekty blob są obecnie oddzielone przy użyciu prefiksu *kontenera/nazwy obiektu BLOB*. Za pomocą indeksu obiektów BLOB można ustawić atrybut znacznika indeksu dla `Project = Contoso` tych pięciu obiektów blob, aby wspólnie klasyfikować je przy zachowaniu ich bieżącej organizacji. Eliminuje to konieczność przenoszenia danych przez ujawnienie możliwości filtrowania i znajdowania danych przy użyciu wielowymiarowego indeksu platformy magazynu.

## <a name="setting-blob-index-tags"></a>Ustawianie tagów indeksów obiektów BLOB

Tagi indeksów obiektów BLOB są atrybutami wartości klucz-wartość, które mogą być stosowane do nowych lub istniejących obiektów na koncie magazynu. Tagi indeksów można określić podczas procesu przekazywania przy użyciu operacji PutBlob, PutBlockList lub CopyBlob oraz opcjonalnego nagłówka x-MS-Tags. Jeśli masz już obiekty blob na koncie magazynu, możesz wywołać SetBlobTags z sformatowanym dokumentem XML określającym atrybuty znacznika indeksu obiektu BLOB w treści żądania.

Rozważ następujące przykłady tagów, które można ustawić

Możesz zastosować pojedynczy tag w obiekcie blob, aby opisać, kiedy dane zostały zakończone.

> "processedDate" = "2020-01-01"

Możesz zastosować wiele tagów w obiekcie blob, aby uzyskać bardziej opisowe dane.

> "Projekt" = "contoso"  
> "Klasyfikowane" = "true"  
> "Status" = "nieprzetworzony"  
> "Priorytet" = "01"

Aby zmodyfikować istniejące atrybuty tagu indeksu, należy najpierw pobrać istniejące atrybuty tagu, zmodyfikować atrybuty tagu i zastąpić operacją SetBlobTags. Aby usunąć wszystkie Tagi indeksu z obiektu BLOB, wywołaj operację SetBlobTags bez określonych atrybutów tagu. Jako że Tagi indeksów obiektów BLOB są podzbiorem zawartości danych obiektów blob, SetBlobTags nie modyfikuje żadnej źródłowej zawartości i nie zmienia obiektu BLOB Last-Modified-Time ani eTag (tag jednostki). Możesz tworzyć lub modyfikować Tagi indeksów dla wszystkich bieżących podstawowych obiektów blob i poprzednich wersji; nie można jednak modyfikować tagów migawek ani nietrwałych usuniętych obiektów BLOB.

Do tagów indeksów obiektów BLOB mają zastosowanie następujące ograniczenia:
- Każdy obiekt BLOB może mieć do 10 tagów indeksów obiektów BLOB
- Klucze tagów muszą zawierać od 1 do 128 znaków
- Wartości tagów muszą zawierać się w przedziale od 0 do 256 znaków
- Klucze i wartości tagów są rozróżniane wielkości liter
- Klucze i wartości tagów obsługują tylko typy danych String; wszystkie liczby, daty, godziny lub znaki specjalne zostaną zapisane jako ciągi
- Klucze i wartości tagów muszą być zgodne z następującymi regułami nazewnictwa:
  - Znaki alfanumeryczne:
    - **od a** do **z** (małe litery)
    - **Od A** do **z** (wielkie litery)
    - **od 0** do **9** (liczba)
  - Prawidłowe znaki specjalne: Space, plus, minus, kropka, dwukropek, Equals, podkreślenie, ukośnik ( ` +-.:=_/` )

## <a name="getting-and-listing-blob-index-tags"></a>Pobieranie i wyświetlanie listy tagów indeksów obiektów BLOB

Tagi indeksów obiektów BLOB są przechowywane jako zasób podrzędny obok danych obiektów blob i mogą być pobierane niezależnie od źródłowej zawartości danych obiektów BLOB. Po ustawieniu Tagi indeksów obiektów BLOB dla pojedynczego obiektu BLOB można pobrać i przejrzeć natychmiast przy użyciu operacji GetBlobTags. Operacja ListBlobs z `include:tags` parametrem zwróci również wszystkie obiekty blob w kontenerze wraz z zastosowanymi tagami indeksu obiektów BLOB.

Dla każdego obiektu BLOB z co najmniej 1 tagiem indeksu obiektów blob, wartość x-MS-tag-Count jest zwracana w operacjach ListBlobs, GetBlob i GetBlobProperties wskazujących liczbę tagów indeksów obiektów BLOB istniejących w obiekcie blob.

## <a name="finding-data-using-blob-index-tags"></a>Znajdowanie danych przy użyciu tagów indeksu obiektów BLOB

W przypadku znaczników indeksu obiektów BLOB ustawionych w obiektach Blob aparat indeksowania uwidacznia te atrybuty klucz/wartość w indeksie wielowymiarowym. Chociaż Tagi indeksu istnieją w obiekcie blob i mogą być pobierane natychmiast, może upłynąć trochę czasu, zanim indeks obiektu BLOB zostanie zaktualizowany o odświeżone atrybuty tagu indeksu. Po zaktualizowaniu indeksu obiektów BLOB można korzystać z natywnych możliwości zapytań i odnajdywania oferowanych przez magazyn obiektów BLOB.

Operacja FindBlobsByTags umożliwia uzyskanie filtrowanego zwracanego zestawu obiektów blob, których Tagi indeksu pasują do danego wyrażenia zapytania indeksu obiektów BLOB. Indeks obiektów BLOB obsługuje filtrowanie między wszystkimi kontenerami w ramach konta magazynu lub można ograniczyć filtrowanie do tylko jednego kontenera. Ponieważ wszystkie klucze i wartości tagów indeksu obiektów BLOB są ciągami, obsługiwane operatory relacyjne używają sortowania leksykograficznych na wartości tagów indeksu.

Następujące kryteria dotyczą filtrowania indeksów obiektów blob:
- Klucze tagów muszą być ujęte w podwójne cudzysłowy (")
- Wartości tagów i nazwy kontenerów powinny być ujęte w apostrofy (')
- @ Znak jest dozwolony tylko w przypadku filtrowania dla określonej nazwy kontenera (tj. @container = "ContainerName")
- Filtry są stosowane z sortowaniem leksykograficznych dla ciągów
- Te same operacje dotyczące zakresu drukowania na tym samym kluczu są nieprawidłowe (tj. "ranga" > "10" i "ranga" >= "15")
- W przypadku użycia opcji REST do utworzenia wyrażenia filtru znaki powinny być kodowane według identyfikatora URI

Poniższa tabela zawiera wszystkie prawidłowe operatory dla FindBlobsByTags:

|  Operator  |  Opis  | Przykład |
|------------|---------------|---------|
|     =      |     Równe     | "Stan" = "w toku" |
|     >      |  Większe niż | "Date" > "2018-06-18" |
|     >=     |  Większe niż lub równe | "Priorytet" >= "5" |
|     <      |  Mniejsze niż   | "Wiek" < "32" |
|     <=     |  Mniejsze niż lub równe  | "Firma" <= "contoso" |
|    AND     |  Koniunkcja logiczna i  | "Ranga" >= "010" i "ranga" < "100" |
| @container | Zakres do określonego kontenera | @container = "videofiles" i "status" = "gotowe" |

> [!NOTE]
> Zapoznaj się z kolejnością lexicographical podczas ustawiania i wykonywania zapytań dotyczących tagów.
> - Liczby są sortowane przed literami. Liczby są sortowane na podstawie pierwszej cyfry.
> - Wielkie litery są sortowane przed małymi literami.
> - Symbole nie są standardami. Niektóre symbole są sortowane przed wartościami liczbowymi. Inne symbole są sortowane przed lub po literach.

## <a name="conditional-blob-operations-with-blob-index-tags"></a>Warunkowe operacje obiektów blob z tagami indeksów obiektów BLOB
W systemach REST 2019-10-10 i nowszych większość [interfejsów API usługi BLOB Service](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs) obsługuje teraz nagłówki warunkowe, x-MS-if-Tags, tak że operacja zakończy się powodzeniem tylko w przypadku spełnienia określonego warunku indeksu obiektów BLOB. Jeśli warunek nie jest spełniony, otrzymasz `error 412: The condition specified using HTTP conditional header(s) is not met` .

Nagłówek x-MS-if-Tags może być połączony z innymi istniejącymi nagłówkami warunkowymi HTTP (jeśli-Match, If-None-Match itp.).  Jeśli w żądaniu dostarczono wiele nagłówków warunkowych, wszystkie muszą oszacować wartość true, aby operacja zakończyła się pomyślnie.  Wszystkie nagłówki warunkowe są efektywnie połączone z logicznymi i.

W poniższej tabeli przedstawiono wszystkie prawidłowe operatory operacji warunkowych:

|  Operator  |  Opis  | Przykład |
|------------|---------------|---------|
|     =      |     Równe     | "Stan" = "w toku" |
|     <>     |   Nie równa się   | "Status"  <>  "gotowe"  |
|     >      |  Większe niż | "Date" > "2018-06-18" |
|     >=     |  Większe niż lub równe | "Priorytet" >= "5" |
|     <      |  Mniejsze niż   | "Wiek" < "32" |
|     <=     |  Mniejsze niż lub równe  | "Firma" <= "contoso" |
|    AND     |  Koniunkcja logiczna i  | "Ranga" >= "010" i "ranga" < "100" |
|     LUB     | Logiczne lub   | "Status" = "gotowe" lub "priorytet" >= "05" |

> [!NOTE]
> Istnieją dwa operatory dodatkowe, nie równe i logiczne lub, które są dozwolone w przypadku warunkowego nagłówka x-MS-if-Tags dla operacji obiektu BLOB, ale nie istnieją w operacji FindBlobsByTags.

## <a name="platform-integrations-with-blob-index-tags"></a>Integracje platform z tagami indeksów obiektów BLOB

Tagi indeksu obiektów BLOB nie tylko ułatwiają kategoryzowanie, zarządzanie i wyszukiwanie danych obiektów blob, ale również zapewniają integrację z innymi funkcjami Blob service, takimi jak [Zarządzanie cyklem życia](storage-lifecycle-management-concepts.md).

### <a name="lifecycle-management"></a>Zarządzanie cyklem życia

Za pomocą nowego blobIndexMatch jako filtru reguł w ramach zarządzania cyklem życia można przenieść dane do warstwy chłodziarki lub usunąć dane na podstawie tagów indeksu zastosowanych do obiektów BLOB. Umożliwia to bardziej szczegółowe zasady i przenoszenie lub usuwanie danych tylko wtedy, gdy są one zgodne z określonymi kryteriami tagów.

Można ustawić dopasowanie indeksu obiektów BLOB jako autonomicznego zestawu filtrów w regule cyklu życia, aby zastosować akcje dla oznakowanych danych. Można też połączyć dopasowanie prefiksu i indeks obiektów BLOB w celu dopasowania do bardziej szczegółowych zestawów danych. Stosowanie wielu filtrów do reguły cyklu życia traktuje się jako logiczne i operacje w taki sposób, aby akcja dotyczyła tylko wtedy, gdy wszystkie kryteria filtrowania pasują do siebie.

Następująca przykładowa reguła zarządzania cyklem życia ma zastosowanie do blokowych obiektów BLOB w kontenerze "videofiles" i "warstwowych obiektów BLOB do przechowywania tylko w przypadku, gdy dane są zgodne z kryteriami znacznika indeksu obiektu BLOB ```"Status" = 'Processed' AND "Source" == 'RAW'``` .

# <a name="portal"></a>[Portal](#tab/azure-portal)
![Przykład reguły dopasowania indeksu obiektów BLOB dla zarządzania cyklem życia w Azure Portal](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)

# <a name="json"></a>[JSON](#tab/json)
```json
{
    "rules": [
        {
            "enabled": true,
            "name": "ArchiveProcessedSourceVideos",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "tierToArchive": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Status",
                            "op": "==",
                            "value": "Processed"
                        },
                        {
                            "name": "Source",
                            "op": "==",
                            "value": "RAW"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ],
                    "prefixMatch": [
                        "videofiles/"
                    ]
                }
            }
        }
    ]
}
```
---

## <a name="permissions-and-authorization"></a>Uprawnienia i autoryzacja

Można autoryzować dostęp do indeksu obiektów BLOB przy użyciu jednej z następujących metod:

- Za pomocą kontroli dostępu opartej na rolach (Azure RBAC) w celu udzielenia uprawnień do podmiotu zabezpieczeń Azure Active Directory (Azure AD). Firma Microsoft zaleca korzystanie z usługi Azure AD w celu zapewnienia bezpieczeństwa i łatwość użycia. Aby uzyskać więcej informacji na temat korzystania z usługi Azure AD z operacjami obiektów blob, zobacz [Autoryzuj dostęp do obiektów blob i kolejek przy użyciu Azure Active Directory](../common/storage-auth-aad.md).
- Za pomocą sygnatury dostępu współdzielonego (SAS) do delegowania dostępu do indeksu obiektów BLOB. Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego, zobacz [udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](../common/storage-sas-overview.md).
- Przy użyciu kluczy dostępu do konta w celu autoryzacji operacji za pomocą klucza współużytkowanego. Aby uzyskać więcej informacji, zobacz [Autoryzuj przy użyciu klucza współużytkowanego](/rest/api/storageservices/authorize-with-shared-key).

Tagi indeksów obiektów BLOB to zasób podrzędny do danych obiektów BLOB. Użytkownik z uprawnieniami lub tokenem sygnatury dostępu współdzielonego w celu odczytu lub zapisu obiektów BLOB może nie mieć dostępu do tagów indeksów obiektów BLOB.

### <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach
Obiekty wywołujące korzystające z [tożsamości usługi Azure AD](../common/storage-auth-aad.md) mogą mieć przyznane następujące uprawnienia do działania w tagach indeksu obiektów BLOB.

|   Operacje obiektu BLOB  |  Akcja RBAC platformy Azure   |
|--------------------|----------------|
| Znajdź obiekty blob według tagów | Microsoft. Storage/storageAccounts/blobServices/Containers/Blobs/Filter/Action |
| Ustaw Tagi obiektów BLOB      | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/Tagi/zapis |
| Pobierz Tagi obiektów BLOB      | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/Tagi/odczyt |

Dodatkowe uprawnienia oddzielone od źródłowych danych obiektów BLOB są wymagane do obsługi tagów. Rola właściciela danych obiektów blob magazynu zostanie przyznany wszystkie trzy z tych uprawnień. Czytnik danych obiektów blob magazynu uzyska uprawnienia tylko znajdowanie obiektów BLOB według tagów i pobieranie tagów obiektów BLOB.

### <a name="sas-permissions"></a>Uprawnienia SAS
Obiekty wywołujące używające [sygnatury dostępu współdzielonego (SAS)](../common/storage-sas-overview.md) mogą mieć przyznane uprawnienia w zakresie do działania w tagach obiektów BLOB.

#### <a name="blob-sas"></a>SAS BLOB
W Blob service SAS można udzielić następujących uprawnień, aby umożliwić dostęp do tagów indeksów obiektów BLOB. Uprawnienia do odczytu i zapisu obiektów BLOB są niewystarczające, aby można było odczytać lub zapisać Tagi indeksu.

|  Uprawnienie  |  Symbol URI  | Dozwolone operacje |
|--------------|--------------|--------------------|
|  Tagi indeksu  |      t      | Pobieranie i Ustawianie tagów indeksów obiektów BLOB dla obiektu BLOB |

#### <a name="container-sas"></a>Kontener SAS kontenera
Do obsługi filtrowania tagów obiektów BLOB mogą być przyznawane następujące uprawnienia.  Uprawnienie do listy obiektów BLOB nie jest wystarczające, aby można było filtrować obiekty blob według ich tagów indeksu.

|  Uprawnienie  |  Symbol URI  | Dozwolone operacje |
|--------------|--------------|--------------------|
| Tagi indeksu   |      f      | Znajdowanie obiektów blob z tagami indeksów obiektów BLOB |

## <a name="choosing-between-metadata-and-blob-index-tags"></a>Wybór między metadanymi a tagami indeksów obiektów BLOB
Zarówno Tagi indeksu obiektów blob, jak i metadane umożliwiają przechowywanie dowolnych właściwości klucza/wartości zdefiniowanych przez użytkownika obok zasobu obiektu BLOB. Obie te funkcje mogą być pobierane i ustawiane bezpośrednio, bez zwracania ani modyfikowania zawartości obiektu BLOB. Istnieje możliwość użycia zarówno znaczników metadanych, jak i indeksów.

Jednak Tagi indeksu obiektów BLOB są automatycznie indeksowane i Queryable przez natywną usługę BLOB Service. Metadane nie mogą być indeksowane natywnie i zapytania, chyba że zostanie wykorzystana oddzielna usługa, taka jak [Azure Search](../../search/search-blob-ai-integration.md). Tagi indeksu obiektów BLOB mają również dodatkowe uprawnienia do odczytu/filtrowania i pisania, które są niezależne od źródłowych danych obiektów BLOB. Metadane wykorzystują te same uprawnienia co obiekt BLOB i są zwracane jako nagłówki HTTP w operacjach GetBlob lub GetBlobProperties. Tagi indeksu obiektów BLOB są szyfrowane w stanie spoczynku przy użyciu [klucza zarządzanego przez firmę Microsoft](../common/storage-service-encryption.md) , a metadane są szyfrowane przy użyciu tego samego klucza szyfrowania określonego dla danych obiektów BLOB.

W poniższej tabeli zestawiono różnice między metadanymi i tagami indeksów obiektów blob:

|              |   Metadane   |   Tagi indeksu obiektów BLOB  |
|--------------|--------------|--------------------|
| **Limity**      | Bez limitu liczbowego; łącznie 8 KB; bez uwzględniania wielkości liter | 10 tagów na obiekt BLOB Max; 768 bajtów na tag; Uwzględnianie wielkości liter |
| **Aktualizacje**    | Niedozwolone w warstwie Archiwum; SetBlobMetadata zastępuje wszystkie istniejące metadane; SetBlobMetadata zmienia czas ostatniej modyfikacji obiektu BLOB | Dozwolone dla wszystkich warstw dostępu; SetBlobTags zastępuje wszystkie istniejące Tagi; SetBlobTags nie zmienia czasu ostatniego modyfikowania obiektu BLOB |
| **Storage**     | Przechowywane z danymi obiektów BLOB | Zasób podrzędny do danych obiektu BLOB |
| **Indeksowanie & indeksowania** | Nie dotyczy natywnie; należy użyć oddzielnej usługi, takiej jak Azure Search | Tak, natywne funkcje indeksowania i wykonywania zapytań wbudowane w magazyn obiektów BLOB |
| **Szyfrowanie** | Szyfrowane przy użyciu tego samego klucza szyfrowania, który jest używany przez dane obiektów BLOB | Szyfrowane przy użyciu klucza szyfrowania zarządzanego przez firmę Microsoft |
| **Cennik** | Rozmiar metadanych jest uwzględniany w kosztach magazynu dla obiektu BLOB | Stały koszt na tag indeksu |
| **Odpowiedź nagłówka** | Metadane zwracane jako nagłówki w GetBlob i GetBlobProperties | TagCount zwrócony w GetBlob lub GetBlobProperties; Tagi zwracane tylko w GetBlobTags i ListBlobs |
| **Uprawnienia**  | Uprawnienia do odczytu lub zapisu danych obiektu BLOB rozszerzają się do metadanych | Do odczytu/filtrowania lub zapisu tagów są wymagane dodatkowe uprawnienia |
| **Nazewnictwo** | Nazwy metadanych muszą być zgodne z regułami nazewnictwa dla identyfikatorów C# | Tagi indeksu obiektów BLOB obsługują szerszego zakresu znaków alfanumerycznych |

## <a name="pricing"></a>Cennik
Cennik indeksów obiektów BLOB jest obecnie dostępny w publicznej wersji zapoznawczej i może ulec zmianie w zakresie ogólnej dostępności. Klienci są obciążani opłatami za łączną liczbę tagów indeksu obiektów BLOB w ramach konta magazynu, obliczoną w ciągu miesiąca. Aparat indeksowania nie ma kosztu. Żądania do SetBlobTags, GetBlobTags i FindBlobsByTags są rozliczone zgodnie z ich odpowiednimi typami operacji. [Aby dowiedzieć się więcej, zobacz temat Zablokuj ceny obiektów BLOB](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="regional-availability-and-storage-account-support"></a>Obsługa regionalnej dostępności i konta magazynu

Indeks obiektów BLOB jest obecnie dostępny tylko na kontach Ogólnego przeznaczenia v2 (GPv2) z wyłączoną hierarchiczną przestrzenią nazw (SNS). Konta Ogólnego przeznaczenia (GPV1) nie są obsługiwane, ale można uaktualnić dowolne konto GPv1 do konta GPv2. Aby uzyskać więcej informacji na temat kont magazynu, zobacz [Omówienie konta usługi Azure Storage](../common/storage-account-overview.md).

W publicznej wersji zapoznawczej indeks obiektów BLOB jest obecnie dostępny tylko w następujących regionach:
- Kanada Środkowa
- Kanada Wschodnia
- Francja Środkowa
- Francja Południowa

Aby rozpocząć, zobacz temat [Korzystanie z indeksu obiektów BLOB do zarządzania danymi i ich znajdowania](storage-blob-index-how-to.md).

> [!IMPORTANT]
> Zobacz sekcję warunki w tym artykule. Aby zarejestrować się w wersji zapoznawczej, zarejestruj swoją subskrypcję, aby móc używać indeksu obiektów BLOB na kontach magazynu.

### <a name="register-your-subscription-preview"></a>Zarejestruj swoją subskrypcję (wersja zapoznawcza)
Ponieważ indeks obiektów BLOB jest tylko w publicznej wersji zapoznawczej, należy zarejestrować swoją subskrypcję, aby móc korzystać z tej funkcji. Aby przesłać żądanie, uruchom następujące polecenia programu PowerShell lub interfejsu CLI.

#### <a name="register-by-using-powershell"></a>Rejestrowanie przy użyciu programu PowerShell
```powershell
Register-AzProviderFeature -FeatureName BlobIndex -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

#### <a name="register-by-using-azure-cli"></a>Rejestrowanie przy użyciu interfejsu wiersza polecenia platformy Azure
```azurecli
az feature register --namespace Microsoft.Storage --name BlobIndex
az provider register --namespace 'Microsoft.Storage'
```

## <a name="conditions-and-known-issues-preview"></a>Warunki i znane problemy (wersja zapoznawcza)
W tej sekcji opisano znane problemy i warunki w bieżącej publicznej wersji zapoznawczej indeksu obiektów BLOB. Tak jak w przypadku większości wersji zapoznawczych, ta funkcja nie powinna być używana do obsługi obciążeń produkcyjnych, dopóki nie osiągnie jej w miarę możliwości zmiany.

- W przypadku wersji zapoznawczej musisz najpierw zarejestrować swoją subskrypcję, aby można było użyć indeksu obiektów BLOB dla konta magazynu w regionach w wersji zapoznawczej.
- Tylko konta GPv2 są obecnie obsługiwane w wersji zapoznawczej. Konta obiektów blob, BlockBlobStorage i SNS włączone nie są obecnie obsługiwane przez indeks obiektów BLOB. Konta GPv1 nie będą obsługiwane.
- Przekazywanie stronicowych obiektów BLOB za pomocą tagów indeksu obecnie nie zachowuje tagów. Po przekazaniu obiektu BLOB stronicowania należy ustawić Tagi.
- Gdy filtrowanie jest ograniczone do jednego kontenera, @container można je przekazywać tylko wtedy, gdy wszystkie Tagi indeksu w wyrażeniu filtru mają kontrolę równości (klucz = wartość).
- W przypadku używania operatora Range z warunkiem i można określić tylko tę samą nazwę klucza znacznika indeksu (wiek > "013" i wiek < "100").
- Przechowywanie wersji i indeks obiektów BLOB nie są obecnie obsługiwane. Tagi indeksu obiektów BLOB są zachowywane dla wersji, ale nie są obecnie przesyłane do aparatu indeksu obiektów BLOB.
- Tryb failover konta nie jest obecnie obsługiwany. Indeks obiektów BLOB może nie zostać poprawnie zaktualizowany po przejściu do trybu failover.
- Zarządzanie cyklem życia obecnie obsługuje tylko sprawdzanie równości przy użyciu dopasowania indeksu obiektów BLOB.
- CopyBlob nie kopiuje tagów indeksów obiektów BLOB ze źródłowego obiektu BLOB do nowego docelowego obiektu BLOB. Można określić Tagi, które mają być stosowane do docelowego obiektu BLOB podczas operacji kopiowania.
- CopyBlob (kopia asynchroniczna) z innego konta magazynu z zastosowanymi tagami w docelowym obiekcie blob aktualnie powoduje, że aparat indeksu obiektów BLOB nie zwraca obiektu BLOB i jego tagów w zestawie filtrów. Zalecane jest użycie CopyBlob z adresu URL (kopia synchroniczna) w tymczasowym.
- Tagi są utrwalane podczas tworzenia migawki; jednak promowanie migawek nie jest obecnie obsługiwane i może spowodować powstanie pustego zestawu tagów.

## <a name="faq"></a>Najczęściej zadawane pytania

### <a name="can-blob-index-help-me-filter-and-query-content-inside-my-blobs"></a>Czy indeks obiektów BLOB ułatwia filtrowanie i wykonywanie zapytań dotyczących zawartości wewnątrz obiektów BLOB?
Nie, Tagi indeksów obiektów BLOB mogą pomóc w znalezieniu obiektów blob, których szukasz. Jeśli chcesz wyszukać w obiektach Blob, użyj przyspieszenia zapytania lub usługi Azure Search.

### <a name="are-there-any-special-considerations-regarding-blob-index-tag-values"></a>Czy istnieją jakieś specjalne uwagi dotyczące wartości tagów indeksu obiektów BLOB?
Tagi indeksu obiektów BLOB obsługują tylko typy danych String i zapytania zwraca wyniki z kolejnością lexicographical. W przypadku liczb zaleca się, aby nie przypadać na konsolę. W przypadku daty i godziny zaleca się przechowywanie w formacie zgodnym ze standardem ISO 8601.

### <a name="are-blob-index-tags-and-azure-resource-manager-tags-related"></a>Czy Tagi indeksów obiektów blob i Azure Resource Manager są powiązane?
Nie, Menedżer zasobów Tagi ułatwiają organizowanie zasobów płaszczyzny kontroli, takich jak subskrypcje, grupy zasobów i konta magazynu. Tagi indeksu obiektów BLOB zapewniają zarządzanie obiektami i odnajdywanie zasobów płaszczyzny danych, takich jak obiekty blob na koncie magazynu.

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z przykładem użycia indeksu obiektów blob, zobacz temat [Korzystanie z indeksu obiektów BLOB do zarządzania danymi i ich znajdowania](storage-blob-index-how-to.md).

Więcej informacji na temat [zarządzania cyklem życia](storage-lifecycle-management-concepts.md) i ustawiania reguły z dopasowaniem indeksu obiektów BLOB.

