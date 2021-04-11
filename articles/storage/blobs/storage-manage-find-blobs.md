---
title: Zarządzanie danymi obiektów blob platformy Azure i znajdowanie ich przy użyciu tagów indeksu obiektów BLOB (wersja zapoznawcza)
description: Dowiedz się, jak używać tagów indeksu obiektów BLOB do kategoryzowania i wykonywania zapytań dotyczących obiektów blob oraz zarządzania nimi.
author: twooley
ms.author: twooley
ms.date: 03/18/2021
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: klaasl
ms.custom: references_regions
ms.openlocfilehash: 2188aaea0cf5a4616291d3fdad839aefb2dbc413
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280704"
---
# <a name="manage-and-find-azure-blob-data-with-blob-index-tags-preview"></a>Zarządzanie danymi obiektów blob platformy Azure i znajdowanie ich przy użyciu tagów indeksu obiektów BLOB (wersja zapoznawcza)

Zestawy danych są większe, co może być trudne, ponieważ znalezienie określonego obiektu na morzu. Tagi indeksu obiektów BLOB zapewniają możliwości zarządzania danymi i ich odnajdywania przy użyciu atrybutów znacznika indeksu wartości klucz-wartość. Można kategoryzowanie i znajdować obiekty w ramach jednego kontenera lub dla wszystkich kontenerów na koncie magazynu. Zmiany wymagań dotyczących danych umożliwiają dynamiczne kategoryzowanie obiektów przez zaktualizowanie ich tagów indeksu. Obiekty mogą pozostać w miejscu wraz z bieżącą organizacją kontenera.

Tagi indeksu obiektów BLOB pozwalają:

- Dynamicznie Kategoryzuj obiekty blob przy użyciu tagów indeksu wartości klucz-wartość
- Szybkie znajdowanie określonych otagowanych obiektów BLOB na całym koncie magazynu
- Określ zachowania warunkowe dla interfejsów API obiektów BLOB na podstawie oceny tagów indeksu
- Używanie tagów indeksu do zaawansowanych kontrolek dotyczących funkcji, takich jak [Zarządzanie cyklem życia obiektów BLOB](storage-lifecycle-management-concepts.md)

Rozważmy scenariusz, w którym masz miliony obiektów BLOB na koncie magazynu, do których uzyskuje się dostęp w wielu różnych aplikacjach. Chcesz znaleźć wszystkie powiązane dane z pojedynczego projektu. Nie wiesz, co znajduje się w zakresie, ponieważ dane mogą być rozłożone między wiele kontenerów z różnymi konwencjami nazewnictwa. Jednak aplikacje przekazują wszystkie dane ze znacznikami opartymi na ich projekcie. Zamiast wyszukiwania przez miliony obiektów blob i porównywania nazw i właściwości można użyć `Project = Contoso` jako kryterium odnajdywania. Indeks obiektów BLOB będzie filtrować wszystkie kontenery na całym koncie magazynu, aby szybko znajdować i zwracać tylko zestaw 50 obiektów blob z `Project = Contoso` .

> [!IMPORTANT]
> Tagi indeksów obiektów BLOB są obecnie w **wersji zapoznawczej** i są dostępne we wszystkich regionach publicznych. Zapoznaj się z [dodatkowymi postanowieniami dotyczącymi](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) wersji zapoznawczych w Microsoft Azure wersjach zapoznawczych, które mają zastosowanie do funkcji platformy Azure w wersjach beta, Preview lub innych, które nie zostały jeszcze ogólnie udostępnione.

Aby rozpocząć pracę z przykładami dotyczącymi używania indeksu obiektów blob, zobacz [Używanie tagów indeksu obiektów BLOB do zarządzania danymi i ich znajdowania](storage-blob-index-how-to.md).

## <a name="blob-index-tags-and-data-management"></a>Tagi indeksu obiektów blob i zarządzanie danymi

Prefiksy nazw kontenerów i obiektów BLOB są kategoryzacją jednowymiarową. Tagi indeksu obiektów BLOB umożliwiają użycie kategoryzacji wielowymiarowej dla [typów danych obiektów BLOB (blok, dołączenie lub strona)](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs). Kategoryzacja wielowymiarowa jest natywnie indeksowana przez usługę Azure Blob Storage, dzięki czemu możesz szybko znaleźć swoje dane.

Rozważ następujące pięć obiektów BLOB na koncie magazynu:

- *container1/transaction.csv*
- *container2/campaign.docx*
- *fotografie/bannerphoto.png*
- *Archiwa/ukończone/2019review.pdf*
- *Dzienniki/2020/01/01/logfile.txt*

Te obiekty blob są oddzielane przy użyciu prefiksu *kontenera/nazwy obiektu BLOB*. Można ustawić atrybut znacznika indeksu dla `Project = Contoso` tych pięciu obiektów blob, aby wspólnie klasyfikować je jednocześnie przy zachowaniu ich bieżącej organizacji prefiksu. Dodawanie tagów indeksu eliminuje konieczność przenoszenia danych przez udostępnienie możliwości filtrowania i znajdowania danych przy użyciu indeksu.

## <a name="setting-blob-index-tags"></a>Ustawianie tagów indeksów obiektów BLOB

Tagi indeksów obiektów BLOB są atrybutami wartości klucz-wartość, które mogą być stosowane do nowych lub istniejących obiektów na koncie magazynu. Tagi indeksów można określić podczas procesu przekazywania przy użyciu operacji [Put BLOB](/rest/api/storageservices/put-blob), [Put Block list](/rest/api/storageservices/put-block-list)lub [copy BLOB](/rest/api/storageservices/copy-blob) i opcjonalnego `x-ms-tags` nagłówka. Jeśli masz już obiekty blob na koncie magazynu, wywołaj opcję [Ustaw Tagi obiektów BLOB](/rest/api/storageservices/set-blob-tags) przekazującą sformatowany dokument XML z tagami indeksu w treści żądania.

> [!IMPORTANT]
> Ustawienie tagów indeksów obiektów BLOB może być wykonywane przez [właściciela danych obiektów blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) i przez każdą osobę z sygnaturą dostępu współdzielonego, która ma uprawnienia dostępu do tagów obiektu BLOB ( `t` uprawnienie SAS).
>
> Ponadto użytkownicy RBAC z `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` uprawnieniem mogą wykonać tę operację.

Możesz zastosować pojedynczy tag w obiekcie blob, aby opisać, kiedy dane zostały zakończone.

> "processedDate" = "2020-01-01"

Możesz zastosować wiele tagów w obiekcie blob, aby uzyskać bardziej opisowe dane.

> "Projekt" = "contoso"  
> "Klasyfikowane" = "true"  
> "Status" = "nieprzetworzony"  
> "Priorytet" = "01"

Aby zmodyfikować istniejące atrybuty tagu indeksu, Pobierz istniejące atrybuty tagu, zmodyfikuj atrybuty tagu i Zamień na operację [Ustaw Tagi obiektów BLOB](/rest/api/storageservices/set-blob-tags) . Aby usunąć wszystkie Tagi indeksu z obiektu BLOB, wywołaj `Set Blob Tags` operację bez określonych atrybutów tagu. Ponieważ Tagi indeksów obiektów BLOB są podzasobami do zawartości danych obiektów blob, `Set Blob Tags` nie modyfikują żadnej źródłowej zawartości i nie zmieniają wartości Last-Time ani ETag obiektu BLOB. Możesz tworzyć lub modyfikować Tagi indeksów dla wszystkich bieżących podstawowych obiektów blob i poprzednich wersji. Nie można jednak modyfikować tagów migawek ani nietrwałych usuniętych obiektów BLOB.

Do tagów indeksów obiektów BLOB mają zastosowanie następujące ograniczenia:

- Każdy obiekt BLOB może mieć do 10 tagów indeksów obiektów BLOB
- Klucze tagów muszą zawierać od 1 do 128 znaków
- Wartości tagów muszą zawierać od 0 do 256 znaków
- Klucze i wartości tagów są rozróżniane wielkości liter
- Klucze i wartości tagów obsługują tylko typy danych String. Wszystkie liczby, daty, godziny lub znaki specjalne są zapisywane jako ciągi
- Klucze i wartości tagów muszą być zgodne z następującymi regułami nazewnictwa:
  - Znaki alfanumeryczne:
    - **od a** do **z** (małe litery)
    - **Od A** do **z** (wielkie litery)
    - **od 0** do **9** (liczba)
  - Prawidłowe znaki specjalne: Space, plus, minus, kropka, dwukropek, Equals, podkreślenie, ukośnik ( ` +-.:=_/` )

## <a name="getting-and-listing-blob-index-tags"></a>Pobieranie i wyświetlanie listy tagów indeksów obiektów BLOB

Tagi indeksów obiektów BLOB są przechowywane jako zasoby podrzędne wraz z danymi obiektów blob i mogą być pobierane niezależnie od źródłowej zawartości danych obiektów BLOB. Tagi indeksów obiektów BLOB dla pojedynczego obiektu BLOB można pobrać przy użyciu operacji [pobierania tagów obiektów BLOB](/rest/api/storageservices/get-blob-tags) . Operacja [listy obiektów BLOB](/rest/api/storageservices/list-blobs) z `include:tags` parametrem zwróci również wszystkie obiekty blob w kontenerze wraz z ich tagami indeksu obiektów BLOB.

> [!IMPORTANT]
> Pobieranie i wyświetlanie znaczników indeksów obiektów BLOB może być wykonywane przez [właściciela danych obiektów blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) i przez każdą osobę z sygnaturą dostępu współdzielonego, która ma uprawnienia dostępu do tagów obiektu BLOB ( `t` uprawnieniem sygnatury typu SAS).
>
> Ponadto użytkownicy RBAC z `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` uprawnieniem mogą wykonać tę operację.

Dla wszystkich obiektów blob z co najmniej jednym tagiem indeksu obiektów BLOB `x-ms-tag-count` jest zwracany w postaci [listy obiektów BLOB](/rest/api/storageservices/list-blobs), [Get BLOB](/rest/api/storageservices/get-blob)i [Get właściwości obiektu BLOB](/rest/api/storageservices/get-blob-properties) wskazujących liczbę tagów indeksu w obiekcie blob.

## <a name="finding-data-using-blob-index-tags"></a>Znajdowanie danych przy użyciu tagów indeksu obiektów BLOB

Aparat indeksowania uwidacznia atrybuty klucz-wartość w indeksie wielowymiarowym. Po ustawieniu tagów indeksu istnieją one w obiekcie blob i można je pobrać od razu. Aktualizacja indeksu obiektów BLOB może zająć trochę czasu. Po aktualizacji indeksu obiektów BLOB można używać natywnych możliwości zapytań i odnajdywania oferowanych przez Blob Storage.

Operacja [Znajdź obiekty blob według tagów](/rest/api/storageservices/find-blobs-by-tags) umożliwia uzyskanie odfiltrowanego zestawu obiektów blob, których Tagi indeksów są zgodne z danym wyrażeniem zapytania. `Find Blobs by Tags` obsługuje filtrowanie między wszystkimi kontenerami w ramach konta magazynu lub można ograniczyć filtrowanie do tylko jednego kontenera. Ponieważ wszystkie klucze i wartości tagów indeksów są ciągami, operatory relacyjne używają sortowania leksykograficznych.

> [!IMPORTANT]
> Znajdowanie danych przy użyciu tagów indeksów obiektów BLOB może być wykonywane przez [właściciela danych obiektów blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) i przez każdą osobę z sygnaturą dostępu współdzielonego, która ma uprawnienia do znajdowania obiektów BLOB według tagów ( `f` uprawnienie sygnatury typu SAS).
>
> Ponadto użytkownicy RBAC z `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action` uprawnieniem mogą wykonać tę operację.

Następujące kryteria dotyczą filtrowania indeksów obiektów blob:

- Klucze tagów muszą być ujęte w podwójne cudzysłowy (")
- Wartości tagów i nazwy kontenerów powinny być ujęte w apostrofy (')
- @ Znak jest dozwolony tylko w przypadku filtrowania dla określonej nazwy kontenera (na przykład `@container = 'ContainerName'` )
- Filtry są stosowane z sortowaniem leksykograficznych dla ciągów
- Te same operacje dotyczące zakresu drukowania na tym samym kluczu są nieprawidłowe (na przykład `"Rank" > '10' AND "Rank" >= '15'` )
- W przypadku użycia opcji REST do utworzenia wyrażenia filtru znaki powinny być kodowane według identyfikatora URI

Poniższa tabela zawiera wszystkie prawidłowe operatory dla `Find Blobs by Tags` :

|  Operator  |  Opis  | Przykład |
|------------|---------------|---------|
|     =      |     Równe     | `"Status" = 'In Progress'` |
|     >      |  Większe niż | `"Date" > '2018-06-18'` |
|     >=     |  Większe niż lub równe | `"Priority" >= '5'` |
|     <      |  Mniejsze niż   | `"Age" < '32'` |
|     <=     |  Mniejsze niż lub równe  | `"Company" <= 'Contoso'` |
|    AND     |  Koniunkcja logiczna i  | `"Rank" >= '010' AND "Rank" < '100'` |
| @container | Zakres do określonego kontenera | `@container = 'videofiles' AND "status" = 'done'` |

> [!NOTE]
> Zapoznaj się z kolejnością lexicographical podczas ustawiania i wykonywania zapytań dotyczących tagów.
>
> - Liczby są sortowane przed literami. Liczby są sortowane na podstawie pierwszej cyfry.
> - Wielkie litery są sortowane przed małymi literami.
> - Symbole nie są standardami. Niektóre symbole są sortowane przed wartościami liczbowymi. Inne symbole są sortowane przed lub po literach.

## <a name="conditional-blob-operations-with-blob-index-tags"></a>Warunkowe operacje obiektów blob z tagami indeksów obiektów BLOB

W systemach REST 2019-10-10 i nowszych większość [interfejsów API usługi BLOB Service](/rest/api/storageservices/operations-on-blobs) obsługuje teraz nagłówek warunkowy, `x-ms-if-tags` w taki sposób, że operacja zakończy się powodzeniem tylko w przypadku spełnienia określonego warunku indeksu obiektów BLOB. Jeśli warunek nie jest spełniony, uzyskasz `error 412: The condition specified using HTTP conditional header(s) is not met` .

`x-ms-if-tags`Nagłówek może być połączony z innymi istniejącymi nagłówkami warunkowymi http (jeśli-Match, If-None-Match itd.). Jeśli w żądaniu dostarczono wiele nagłówków warunkowych, wszystkie muszą oszacować wartość true, aby operacja zakończyła się pomyślnie. Wszystkie nagłówki warunkowe są efektywnie połączone z logicznymi i.

W poniższej tabeli przedstawiono prawidłowe operatory operacji warunkowych:

|  Operator  |  Opis  | Przykład |
|------------|---------------|---------|
|     =      |     Równe     | `"Status" = 'In Progress'` |
|     <>     |   Nie równa się   | `"Status" <> 'Done'` |
|     >      |  Większe niż | `"Date" > '2018-06-18'` |
|     >=     |  Większe niż lub równe | `"Priority" >= '5'` |
|     <      |  Mniejsze niż   | `"Age" < '32'` |
|     <=     |  Mniejsze niż lub równe  | `"Company" <= 'Contoso'` |
|    AND     |  Koniunkcja logiczna i  | `"Rank" >= '010' AND "Rank" < '100'` |
|     LUB     | Logiczne lub   | `"Status" = 'Done' OR "Priority" >= '05'` |

> [!NOTE]
> Istnieją dwa operatory dodatkowe, nie równe i logiczne lub, które są dozwolone w `x-ms-if-tags` nagłówku warunkowym dla operacji obiektu BLOB, ale nie istnieją w tej `Find Blobs by Tags` operacji.

## <a name="platform-integrations-with-blob-index-tags"></a>Integracje platform z tagami indeksów obiektów BLOB

Tagi indeksu obiektów BLOB nie tylko ułatwiają kategoryzowanie, zarządzanie i wyszukiwanie danych obiektów blob, ale również zapewniają integrację z innymi funkcjami Blob Storage, takimi jak [Zarządzanie cyklem życia](storage-lifecycle-management-concepts.md).

### <a name="lifecycle-management"></a>Zarządzanie cyklem życia

Za pomocą `blobIndexMatch` filtru reguł w ramach zarządzania cyklem życia można przenieść dane do warstwy usługi chłodnicy lub usunąć dane na podstawie tagów indeksu zastosowanych do obiektów BLOB. Możesz być bardziej szczegółowymi regułami i przenosić lub usuwać obiekty blob, jeśli są one zgodne z określonymi kryteriami znaczników.

Można ustawić dopasowanie indeksu obiektów BLOB jako autonomicznego zestawu filtrów w regule cyklu życia, aby zastosować akcje dla oznakowanych danych. Można też połączyć prefiks i indeks obiektów BLOB w celu dopasowania do bardziej szczegółowych zestawów danych. Określenie wielu filtrów w regule cyklu życia dotyczy operacji logicznej i. Akcja będzie stosowana tylko w przypadku, gdy *wszystkie* kryteria filtrowania pasują do siebie.

Następująca przykładowa reguła zarządzania cyklem życia ma zastosowanie do blokowych obiektów BLOB w kontenerze o nazwie *videofiles*. Warstwy reguły obiektów BLOB do archiwizowania magazynu tylko wtedy, gdy dane pasują do kryteriów znacznika indeksu obiektów BLOB `"Status" == 'Processed' AND "Source" == 'RAW'` .

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

Można autoryzować dostęp do tagów indeksów obiektów BLOB przy użyciu jednej z następujących metod:

- Za pomocą kontroli dostępu opartej na rolach (Azure RBAC) w celu udzielenia uprawnień do podmiotu zabezpieczeń Azure Active Directory (Azure AD). Korzystaj z usługi Azure AD w celu zapewnienia bezpieczeństwa i łatwość użycia. Aby uzyskać więcej informacji na temat korzystania z usługi Azure AD z operacjami obiektów blob, zobacz [Autoryzuj dostęp do obiektów blob i kolejek przy użyciu Azure Active Directory](../common/storage-auth-aad.md).
- Używanie sygnatury dostępu współdzielonego w celu delegowania dostępu do indeksu obiektów BLOB. Aby uzyskać więcej informacji na temat sygnatur dostępu współdzielonego, zobacz [udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](../common/storage-sas-overview.md).
- Użycie kluczy dostępu do konta w celu autoryzacji operacji za pomocą klucza współużytkowanego. Aby uzyskać więcej informacji, zobacz [Autoryzuj przy użyciu klucza współużytkowanego](/rest/api/storageservices/authorize-with-shared-key).

Tagi indeksów obiektów BLOB to podzasób do danych obiektów BLOB. Użytkownik z uprawnieniami lub tokenem sygnatury dostępu współdzielonego w celu odczytu lub zapisu obiektów BLOB może nie mieć dostępu do tagów indeksów obiektów BLOB.

### <a name="role-based-access-control"></a>Kontrola dostępu oparta na rolach

Obiekty wywołujące korzystające z [tożsamości usługi Azure AD](../common/storage-auth-aad.md) mogą mieć przyznane następujące uprawnienia do działania w tagach indeksu obiektów BLOB.

| Operacje tagów indeksów obiektów BLOB                                          | Akcja RBAC platformy Azure                                                             |
|--------------------------------------------------------------------|-------------------------------------------------------------------------------|
| [Ustaw Tagi obiektów BLOB](/rest/api/storageservices/set-blob-tags)           | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/Tagi/zapis    |
| [Pobierz Tagi obiektów BLOB](/rest/api/storageservices/get-blob-tags)           | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/Tagi/odczyt     |
| [Znajdź obiekty blob według tagów](/rest/api/storageservices/find-blobs-by-tags) | Microsoft. Storage/storageAccounts/blobServices/Containers/Blobs/Filter/Action |

Do operacji tagów indeksów są wymagane dodatkowe uprawnienia, oddzielone od źródłowych danych obiektów BLOB. Rola [właściciela danych obiektów blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) ma przyznane uprawnienia dla wszystkich trzech operacji znacznika indeksu obiektów BLOB. [Czytnik danych obiektów blob magazynu](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) ma przyznane tylko uprawnienia do `Find Blobs by Tags` `Get Blob Tags` operacji i.

### <a name="sas-permissions"></a>Uprawnienia SAS

Obiekty wywołujące używające [sygnatury dostępu współdzielonego (SAS)](../common/storage-sas-overview.md) mogą mieć przyznane uprawnienia w zakresie do działania w tagach indeksu obiektów BLOB.

#### <a name="blob-sas"></a>SAS BLOB

W celu umożliwienia dostępu do tagów indeksów obiektów BLOB mogą być przyznawane następujące uprawnienia. Uprawnienia do odczytu i zapisu obiektów BLOB same nie są wystarczające, aby umożliwić odczytywanie i zapisywanie tagów indeksu.

| Uprawnienie | Symbol URI | Dozwolone operacje                |
|------------|------------|-----------------------------------|
| Tagi indeksu |     t      | Pobieranie i Ustawianie tagów indeksu dla obiektu BLOB |

#### <a name="container-sas"></a>Kontener SAS kontenera

W sygnaturze SAS kontenera można udzielić następujących uprawnień umożliwiających filtrowanie tagów obiektów BLOB. `Blob List`Uprawnienie nie jest wystarczające, aby zezwalać na filtrowanie obiektów BLOB według ich tagów indeksu.

| Uprawnienie | Symbol URI | Dozwolone operacje         |
|------------|------------|----------------------------|
| Tagi indeksu |     f      | Znajdowanie obiektów BLOB ze znacznikami indeksu |

## <a name="choosing-between-metadata-and-blob-index-tags"></a>Wybór między metadanymi a tagami indeksów obiektów BLOB

Zarówno Tagi indeksu obiektów blob, jak i metadane umożliwiają przechowywanie dowolnych właściwości wartości klucz-wartość zdefiniowanych przez użytkownika obok zasobu obiektu BLOB. Oba elementy można pobrać i ustawić bezpośrednio, bez zwracania lub zmiany zawartości obiektu BLOB. Możliwe jest użycie zarówno znaczników metadanych, jak i indeksów.

Tylko Tagi indeksów są automatycznie indeksowane i przeszukiwane przez natywną usługę Blob Storage. Metadane nie mogą być w sposób natywny indeksowane ani przeszukiwane. Należy użyć oddzielnej usługi, takiej jak [Azure Search](../../search/search-blob-ai-integration.md). Tagi indeksów obiektów BLOB mają dodatkowe uprawnienia do odczytu, filtrowania i pisania, które są niezależne od źródłowych danych obiektów BLOB. Metadane używają tych samych uprawnień co obiekt BLOB i są zwracane jako nagłówki HTTP przy użyciu operacji [Get BLOB](/rest/api/storageservices/get-blob) i [Get BLOB Properties](/rest/api/storageservices/get-blob-properties) . Tagi indeksów obiektów BLOB są szyfrowane w stanie spoczynku przy użyciu [klucza zarządzanego przez firmę Microsoft](../common/storage-service-encryption.md). Metadane są szyfrowane w stanie spoczynku przy użyciu tego samego klucza szyfrowania określonego dla danych obiektów BLOB.

W poniższej tabeli zestawiono różnice między metadanymi i tagami indeksów obiektów blob:

|              |   Metadane   |   Tagi indeksu obiektów BLOB  |
|--------------|--------------|--------------------|
| **Limity**      | Bez limitu liczbowego, suma 8 KB, bez uwzględniania wielkości liter | 10 tagów na obiekt BLOB Max, 768 bajtów na tag, z uwzględnieniem wielkości liter |
| **Aktualizacje**    | Niedozwolone w warstwie archiwum, `Set Blob Metadata` zastępuje wszystkie istniejące metadane, `Set Blob Metadata` zmienia czas ostatniej modyfikacji obiektu BLOB | Dozwolone dla wszystkich warstw dostępu, `Set Blob Tags` zastępuje wszystkie istniejące Tagi, `Set Blob Tags` nie powoduje zmiany czasu ostatniej modyfikacji obiektu BLOB |
| **Storage**     | Przechowywane z danymi obiektów BLOB | Podzasób danych obiektu BLOB |
| **Indeksowanie & indeksowania** | Należy użyć oddzielnej usługi, takiej jak Azure Search | Funkcje indeksowania i wykonywania zapytań wbudowanych w Blob Storage |
| **Szyfrowanie** | Szyfrowane przy użyciu tego samego klucza szyfrowania, który jest używany przez dane obiektów BLOB | Szyfrowane przy użyciu klucza szyfrowania zarządzanego przez firmę Microsoft |
| **Cennik** | Rozmiar metadanych jest uwzględniany w kosztach magazynu dla obiektu BLOB | Stały koszt na tag indeksu |
| **Odpowiedź nagłówka** | Metadane zwracane jako nagłówki `Get Blob` i `Get Blob Properties` | Liczba tagów zwracana przez `Get Blob` lub `Get Blob Properties` , Tagi zwrócone tylko przez `Get Blob Tags` i `List Blobs` |
| **Uprawnienia**  | Uprawnienia do odczytu lub zapisu danych obiektu BLOB rozszerzają się do metadanych | Do odczytywania, filtrowania lub zapisywania tagów indeksu są wymagane dodatkowe uprawnienia |
| **Nazewnictwo** | Nazwy metadanych muszą być zgodne z regułami nazewnictwa dla identyfikatorów C# | Tagi indeksu obiektów BLOB obsługują szerszego zakresu znaków alfanumerycznych |

## <a name="pricing"></a>Ceny

Cennik indeksów obiektów BLOB jest dostępny w publicznej wersji zapoznawczej i może ulec zmianie w zakresie ogólnej dostępności. Opłata jest naliczana za miesięczną średnią liczbę tagów indeksu w ramach konta magazynu. Aparat indeksowania nie ma kosztu. Żądania do `Set Blob Tags` , `Get Blob Tags` i `Find Blobs by Tags` są naliczone zgodnie z ich odpowiednimi typami operacji. [Aby dowiedzieć się więcej, zobacz temat Zablokuj ceny obiektów BLOB](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="regional-availability-and-storage-account-support"></a>Obsługa regionalnej dostępności i konta magazynu

Tagi indeksu obiektów BLOB są dostępne tylko na kontach Ogólnego przeznaczenia v2 (GPv2) z wyłączoną hierarchiczną przestrzenią nazw (SNS). Konta Ogólnego przeznaczenia (GPV1) nie są obsługiwane, ale można uaktualnić dowolne konto GPv1 do konta GPv2.

Tagi indeksu nie są obsługiwane na kontach magazynu w warstwie Premium. Aby uzyskać więcej informacji na temat kont magazynu, zobacz [Omówienie konta usługi Azure Storage](../common/storage-account-overview.md).

Tagi indeksów obiektów BLOB są obecnie dostępne we wszystkich regionach publicznych.

Aby rozpocząć, zobacz [Używanie tagów indeksu obiektów BLOB do zarządzania danymi i ich znajdowania](storage-blob-index-how-to.md).

> [!IMPORTANT]
> Musisz zarejestrować swoją subskrypcję, aby można było korzystać z podglądu indeksu obiektów BLOB na kontach magazynu. Zobacz sekcję [warunki i znane problemy](#conditions-and-known-issues) w tym artykule.

### <a name="register-your-subscription-preview"></a>Zarejestruj swoją subskrypcję (wersja zapoznawcza)

Ponieważ Tagi indeksów obiektów BLOB są tylko w publicznej wersji zapoznawczej, należy zarejestrować swoją subskrypcję, aby móc korzystać z tej funkcji. Aby przesłać żądanie, uruchom następujące polecenia programu PowerShell lub interfejsu CLI.

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

## <a name="conditions-and-known-issues"></a>Warunki i znane problemy

W tej sekcji opisano znane problemy i warunki w publicznej wersji zapoznawczej tagów indeksów obiektów BLOB. Ta funkcja nie powinna być używana do obciążeń produkcyjnych do momentu osiągnięcia ogólnej dostępności (GA), ponieważ zachowanie może się zmienić.

- W przypadku wersji zapoznawczej musisz najpierw zarejestrować swoją subskrypcję, aby można było użyć indeksu obiektów BLOB dla konta magazynu w regionach w wersji zapoznawczej.
- Tylko konta GPv2 są obsługiwane w wersji zapoznawczej. Konta obiektów blob, BlockBlobStorage i SNS włączone nie są obsługiwane. Konta GPv1 nie będą obsługiwane.
- Przekazywanie stronicowych obiektów BLOB za pomocą tagów indeksu nie zachowuje tagów. Po przekazaniu obiektu BLOB na stronie należy ustawić Tagi.
- Gdy filtrowanie jest ograniczone do jednego kontenera, `@container` można je przekazywać tylko wtedy, gdy wszystkie Tagi indeksu w wyrażeniu filtru mają kontrolę równości (klucz = wartość).
- W przypadku używania operatora Range z `AND` warunkiem można określić tylko tę samą nazwę klucza znacznika indeksu ( `"Age" > '013' AND "Age" < '100'` ).
- Przechowywanie wersji i indeks obiektów BLOB nie są obsługiwane. Tagi indeksu obiektów BLOB są zachowywane dla wersji, ale nie są przenoszone do aparatu indeksu obiektów BLOB.
- Nie ma interfejsu API, aby określić, czy Tagi indeksów są indeksowane.
- Tryb failover konta nie jest obsługiwany. Indeks obiektów BLOB może nie zostać poprawnie zaktualizowany po przejściu do trybu failover.
- Zarządzanie cyklem życia obsługuje tylko sprawdzanie równości przy użyciu dopasowania indeksu obiektów BLOB.
- `Copy Blob` nie kopiuje tagów indeksu obiektów BLOB ze źródłowego obiektu BLOB do nowego docelowego obiektu BLOB. Można określić Tagi, które mają być stosowane do docelowego obiektu BLOB podczas operacji kopiowania.
- `Copy Blob` (Kopia asynchroniczna) z innego konta magazynu z zastosowanymi tagami w docelowym obiekcie blob sprawia, że aparat indeksu obiektów BLOB nie zwraca obiektu BLOB i jego tagów w zestawie filtrów. Użyj `Copy Blob` adresu URL (kopia synchroniczna).
- Tagi są utrwalane podczas tworzenia migawki. Jednak promowanie migawek nie jest obsługiwane i może spowodować powstanie pustego zestawu tagów.

## <a name="faq"></a>Często zadawane pytania

**Czy indeks obiektów BLOB ułatwia filtrowanie i wykonywanie zapytań dotyczących zawartości wewnątrz obiektów BLOB?**

Nie, jeśli chcesz wyszukiwać dane obiektów blob, użyj przyspieszenia zapytania lub usługi Azure Search.

**Czy istnieją jakieś wymagania dotyczące wartości tagów indeksu?**

Tagi indeksu obiektów BLOB obsługują tylko typy danych String i zapytania zwraca wyniki z kolejnością lexicographical. Dla liczb, zero konsoli numer. W przypadku dat i godzin przechowywanie w formacie zgodnym ze standardem ISO 8601.

**Czy Tagi indeksów obiektów blob i Azure Resource Manager są powiązane?**

Nie, Menedżer zasobów Tagi ułatwiają organizowanie zasobów płaszczyzny kontroli, takich jak subskrypcje, grupy zasobów i konta magazynu. Tagi indeksów zapewniają zarządzanie obiektami BLOB i odnajdywanie na płaszczyźnie danych.

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z przykładem użycia indeksu obiektów blob, zobacz [używanie indeksu obiektów BLOB do zarządzania danymi i ich znajdowania](storage-blob-index-how-to.md).

Dowiedz się więcej o [zarządzaniu cyklem życia](storage-lifecycle-management-concepts.md) i ustaw regułę z dopasowywaniem indeksu obiektów BLOB.
