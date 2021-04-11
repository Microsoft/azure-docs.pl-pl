---
title: Warstwy dostępu dla platformy Azure Blob Storage — gorąca, chłodna i archiwalna
description: Przeczytaj o warstwach dostępu gorąca, chłodna i archiwalna dla usługi Azure Blob Storage. Przejrzyj konta magazynu obsługujące obsługę warstw.
author: twooley
ms.author: twooley
ms.date: 03/18/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: klaasl
ms.openlocfilehash: 957973cc4f53dba10ed9d635c8e3f69fd66ee33b
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278426"
---
# <a name="access-tiers-for-azure-blob-storage---hot-cool-and-archive"></a>Warstwy dostępu dla platformy Azure Blob Storage — gorąca, chłodna i archiwalna

Usługa Azure Storage oferuje różne warstwy dostępu, dzięki czemu można przechowywać dane obiektów BLOB w najbardziej opłacalny sposób. Dostępne są następujące warstwy dostępu:

-  Zoptymalizowany pod kątem przechowywania danych, które są często dostępne.
- **Chłodnie** zoptymalizowane pod kątem przechowywania danych, które są rzadko używane i są przechowywane przez co najmniej 30 dni.
- Funkcja **archiwizowania** zoptymalizowana pod kątem przechowywania danych, które są rzadko używane i są przechowywane przez co najmniej 180 dni z elastycznymi wymaganiami dotyczącymi opóźnień, w kolejności godzin.

Poniższe uwagi dotyczą różnych warstw dostępu:

- Warstwę dostępu można ustawić na obiekcie blob podczas przekazywania lub po nim.
- Na poziomie konta można ustawić tylko warstwy dostępu Chłodna i Gorąca. Warstwę dostępu do archiwum można ustawić tylko na poziomie obiektu BLOB.
- Dane w warstwie dostępu chłodnego mają nieco niższą dostępność, ale nadal mają wysoką trwałość, opóźnienia pobierania i właściwości przepływności podobne do danych gorąca. W przypadku danych chłodnych nieznacznie niższa dostępność i wyższe koszty dostępu to akceptowalne koszty związane z niższymi ogólnymi kosztami magazynowania w porównaniu z gorącymi danymi. Aby uzyskać więcej informacji, zobacz [Magazyn — umowa SLA](https://azure.microsoft.com/support/legal/sla/storage/v1_5/).
- Dane w warstwie dostępu archiwizowania są przechowywane w trybie offline. Warstwa archiwum zapewnia najniższe koszty magazynowania, ale również najwyższy koszt dostępu i opóźnienia.
- Warstwy gorąca i chłodna obsługują wszystkie opcje nadmiarowości. Warstwa archiwum obsługuje tylko LRS, GRS i RA-GRS.
- Limity magazynu danych są ustawiane na poziomie konta, a nie na warstwie dostępu. Możesz użyć wszystkich limitów w jednej warstwie lub we wszystkich trzech warstwach.

Dane przechowywane w chmurze zwiększają się w tempie wykładniczym. Aby zarządzać kosztami zwiększających się potrzeb dotyczących magazynowania, warto zorganizować dane na podstawie atrybutów, takich jak częstotliwość dostępu i planowany okres przechowywania. Pozwoli to na optymalizację kosztów. Dane przechowywane w chmurze mogą być różne w zależności od tego, jak są generowane, przetwarzane i jak uzyskuje się do nich dostęp w okresie ich istnienia. Do niektórych danych często uzyskuje się dostęp. Są one również często modyfikowane w trakcie całego okresu istnienia. Do niektórych danych często uzyskuje się dostęp na początkowym etapie istnienia, a z czasem już zdecydowanie rzadziej. Niektóre dane pozostają w stanie bezczynności w chmurze i rzadko, jeśli kiedykolwiek, uzyskuje się do nich dostęp po rozpoczęciu ich przechowywania.

Każdy z tych scenariuszy dostępu do danych korzysta z innej warstwy dostępu, która jest zoptymalizowana pod kątem określonego wzorca dostępu. Dzięki warstwom dostępu gorąca, chłodna i archiwalna platforma Azure Blob Storage eliminuje konieczność stosowania zróżnicowanych warstw dostępu z oddzielnymi modelami cenowymi.

Poniższe narzędzia i biblioteki klienta obsługują obsługę warstw na poziomie obiektów blob i magazyn archiwum.

- Azure Portal
- PowerShell
- Narzędzia interfejsu wiersza polecenia platformy Azure
- Biblioteka klienta platformy .NET
- Biblioteka klienta języka Java
- Biblioteka klienta języka Python
- Node.js Biblioteka kliencka

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Konta magazynu z obsługą warstw

Obsługa warstw danych magazynu obiektów między gorącą, chłodną i archiwum jest obsługiwana w ramach kont Blob Storage i Ogólnego przeznaczenia v2 (GPv2). Konta Ogólnego przeznaczenia V1 (GPv1) nie obsługują warstw. Istniejące konta GPv1 lub Blob Storage można łatwo przekonwertować na konta GPv2 przy użyciu Azure Portal. GPv2 udostępnia nowe ceny i funkcje dla obiektów blob, plików i kolejek. Niektóre funkcje i obniżki cen są oferowane tylko w ramach kont GPv2. Niektóre obciążenia mogą być droższe w GPv2 niż GPv1. Aby uzyskać więcej informacji, zobacz [Omówienie konta magazynu platformy Azure](../common/storage-account-overview.md).

Konta Blob Storage i GPv2 uwidaczniają atrybut **Warstwa dostępu** na poziomie konta. Ten atrybut umożliwia określenie domyślnej warstwy dostępu dla dowolnego obiektu BLOB, który nie został jawnie ustawiony na poziomie obiektu. W przypadku obiektów z jawnie ustawioną warstwą nie będą stosowane warstwy konta. Warstwę archiwum można stosować tylko na poziomie obiektu. W każdej chwili można przełączać się między warstwami dostępu.

Używaj GPv2 zamiast kont Blob Storage do obsługi warstw. Program GPv2 obsługuje wszystkie funkcje, które obsługują obsługę kont Blob Storage, a także wiele innych. Ceny między Blob Storage i GPv2 są niemal identyczne, ale niektóre nowe funkcje i ceny są dostępne tylko na kontach GPv2.

Struktury cen kont GPv1 i GPv2 są różne, a klienci powinni dokładnie je sprawdzić przed podjęciem decyzji o użyciu kont GPv2. Istniejące konto usługi Blob Storage lub GPv1 można łatwo przekonwertować na konto GPv2 w ramach prostego procesu uruchamianego jednym kliknięciem. Aby uzyskać więcej informacji, zobacz [Omówienie konta magazynu platformy Azure](../common/storage-account-overview.md).

## <a name="hot-access-tier"></a>Warstwa dostępu Gorąca

Warstwa dostępu gorąca ma wyższe koszty magazynowania niż warstwy chłodna i archiwum, ale najniższe koszty dostępu. Przykładowe scenariusze użycia dla warstwy dostępu gorąca obejmują:

- Dane, które są aktywnie używane lub powinny być często odczytywane i zapisywane.
- Dane przygotowane do przetworzenia i ostatecznej migracji do warstwy dostępu chłodnego

## <a name="cool-access-tier"></a>Warstwa dostępu Chłodna

Warstwa dostępu chłodna ma niższe koszty magazynowania i wyższe koszty dostępu w porównaniu do warstwy magazynowania gorąca. Ta warstwa jest przeznaczona dla danych, które pozostaną w warstwie Chłodna przez co najmniej 30 dni. Przykładowe scenariusze użycia dla warstwy dostępu chłodna obejmują:

- Krótkoterminowe wykonywanie kopii zapasowych i odzyskiwanie po awarii
- Starsze dane nie są często używane, ale oczekiwano, że są one dostępne natychmiast po uzyskaniu dostępu
- Duże zestawy danych, które muszą być przechowywane ekonomicznie, podczas gdy więcej danych jest zbieranych do przyszłego przetwarzania

## <a name="archive-access-tier"></a>Warstwa dostępu Archiwum

Warstwa dostępu archiwalnego ma najniższy koszt magazynowania, ale wyższe koszty pobierania danych są porównywane z warstwami gorąca i chłodna. Dane muszą pozostać w warstwie archiwum przez co najmniej 180 dni lub podlegają opłacie za wczesne usunięcie. Pobieranie danych w warstwie archiwum może potrwać kilka godzin w zależności od określonego priorytetu odzyskania. W przypadku małych obiektów dehydratacji o wysokim priorytecie może pobrać obiekt z archiwum w ciągu godziny. Aby dowiedzieć się więcej, zobacz informacje dotyczące [danych obiektów BLOB w warstwie archiwum](storage-blob-rehydration.md) .

Gdy obiekt BLOB znajduje się w magazynie archiwum, dane obiektów BLOB są w trybie offline i nie można ich odczytać ani modyfikować. Aby odczytać lub pobrać obiekt BLOB w archiwum, należy najpierw go przetworzyć w warstwie online. Nie można wykonać migawek obiektu BLOB w magazynie archiwum. Jednak metadane obiektów BLOB pozostają w trybie online i są dostępne, co umożliwia wyświetlenie listy obiektów blob, jego właściwości, metadanych i znaczników indeksów obiektów BLOB. Ustawianie lub modyfikowanie metadanych obiektów BLOB w archiwum jest niedozwolone. Można jednak ustawiać i modyfikować Tagi indeksów obiektów BLOB. W przypadku obiektów BLOB w archiwum jedynymi prawidłowymi operacjami są [pobieranie właściwości obiektów BLOB](/rest/api/storageservices/get-blob-properties), [Pobieranie metadanych obiektów](/rest/api/storageservices/get-blob-metadata)BLOB, [Ustawianie tagów obiektów](/rest/api/storageservices/set-blob-tags)BLOB, [pobieranie tagów obiektów BLOB](/rest/api/storageservices/get-blob-tags), [Znajdowanie obiektów BLOB według tagów](/rest/api/storageservices/find-blobs-by-tags), [Wyświetlanie listy obiektów](/rest/api/storageservices/list-blobs)BLOB, [Ustawianie warstwy obiektów BLOB](/rest/api/storageservices/set-blob-tier), [Kopiowanie obiektu](/rest/api/storageservices/copy-blob)BLOB i [usuwanie obiektu BLOB](/rest/api/storageservices/delete-blob).

Przykładowe scenariusze użycia dla warstwy dostępu archiwalnego obejmują:

- Długoterminowe kopie zapasowe, dodatkowe kopie zapasowej i archiwalne zestawy danych
- Oryginalne (nieprzetworzone) dane, które muszą zostać zachowane, nawet po przetworzeniu ich w ostatecznej formie do użycia
- Dane dotyczące zgodności i archiwizowania, które muszą być przechowywane przez długi czas i są trudne do użycia

> [!NOTE]
> Warstwa archiwum nie jest obsługiwana w przypadku kont ZRS, GZRS i RA-GZRS. Migrowanie z LRS do GRS jest obsługiwane, o ile nie zostały przeniesione obiekty blob do warstwy archiwum, gdy konto zostało ustawione na LRS. Konto można przenieść z powrotem do GRS, jeśli aktualizacja zostanie wykonana krócej niż 30 dni od momentu, gdy konto stało się LRS, a obiekty blob nie zostały przeniesione do warstwy archiwum, gdy konto zostało ustawione na LRS.

## <a name="account-level-tiering"></a>Obsługa warstw na poziomie konta

Obiekty blob we wszystkich trzech warstwach dostępu mogą współistnieć w ramach tego samego konta. Wszystkie obiekty blob, które nie mają jawnie przypisanej warstwy, są uwzględniane w ustawieniach warstwy dostępu konta. Jeśli warstwa dostępu pochodzi z konta, zostanie wyświetlona właściwość obiektu BLOB **wywnioskowanej warstwy dostępu** ustawiona na wartość "true", a właściwość obiektu BLOB **warstwy dostępu** jest zgodna z warstwą konta. W Azure Portal Właściwość _wywnioskowana Warstwa dostępu_ jest wyświetlana z warstwą dostępu obiektu BLOB jako **gorąca (wywnioskowana)** lub **chłodna (wywnioskowana)**.

Zmiana warstwy dostępu do konta ma zastosowanie do wszystkich obiektów _odroczonych w warstwie dostępu_ przechowywanych na koncie, które nie mają jawnie ustawionej warstwy. Jeśli przełączysz warstwę konta z gorąca na chłodna, nastąpi naliczanie opłat za operacje zapisu (za 10 000) dla wszystkich obiektów Blob bez ustawionej warstwy tylko na kontach GPv2. Ta zmiana nie jest naliczana w ramach kont Blob Storage. Zostanie naliczona opłata za operacje odczytu (za 10 000) i pobieranie danych (za GB) w przypadku przełączenia z chłodnej na gorącą Blob Storage lub kont GPv2.

Tylko warstwy dostępu gorąca i chłodna można ustawić jako domyślną warstwę dostępu do konta. Archiwum można ustawić tylko na poziomie obiektu. Przy przekazywaniu obiektów BLOB możesz określić wybraną warstwę dostępu jako gorąca, chłodna lub archiwalna niezależnie od domyślnej warstwy konta. Ta funkcja umożliwia zapisanie danych bezpośrednio w warstwie archiwum, co pozwala na wykorzystanie oszczędności kosztów od momentu utworzenia danych w magazynie obiektów BLOB.

## <a name="blob-level-tiering"></a>Obsługa warstw na poziomie obiektów blob

Obsługa warstw na poziomie obiektów BLOB umożliwia przekazywanie danych do wybranej warstwy dostępu przy użyciu operacji [Put BLOB](/rest/api/storageservices/put-blob) lub [Put Block list](/rest/api/storageservices/put-block-list) oraz zmianę warstwy danych na poziomie obiektu za pomocą funkcji [Ustaw warstwę obiektów BLOB](/rest/api/storageservices/set-blob-tier) lub funkcję [zarządzania cyklem życia](#blob-lifecycle-management) . Dane można przekazywać do wymaganej warstwy dostępu, a następnie łatwo zmienić warstwę dostępu do obiektów BLOB w warstwach gorąca, chłodna lub archiwalna w miarę zmiany wzorców użycia, bez konieczności przenoszenia danych między kontami. Wszystkie żądania zmiany warstwy są wykonywane natychmiast, a zmiany warstwy między gorącą i chłodną są chwilowo. Ponownego wypełniania obiektu BLOB z warstwy archiwum może potrwać kilka godzin.

Czas ostatniej zmiany warstwy obiektu blob jest uwidaczniany za pomocą właściwości obiektu blob **Czas zmiany warstwy dostępu**. Ustawienie **czas zmiany warstwy dostępu** jest właściwością na poziomie obiektu BLOB i nie jest aktualizowane, gdy zostanie zmieniona domyślna warstwa konta. Właściwości konta i właściwości obiektu BLOB są osobne. Za każdym razem, gdy domyślna Warstwa dostępu do konta jest zmieniana, niezwykle się od **czasu zmiany warstwy dostępu** na każdym obiekcie BLOB na koncie magazynu.

Podczas zastępowania obiektu BLOB w warstwie gorąca lub chłodna nowo utworzony obiekt BLOB dziedziczy warstwę obiektu BLOB, który został zastąpiony, chyba że nowa warstwa dostępu do obiektów BLOB jest jawnie ustawiona podczas tworzenia. Jeśli obiekt BLOB znajduje się w warstwie archiwum, nie można go zastąpić, więc przekazywanie tego samego obiektu BLOB nie jest dozwolone w tym scenariuszu.

> [!NOTE]
> Magazyn Archiwum i funkcja obsługi warstw na poziomie obiektów blob obsługują tylko blokowe obiekty blob.

### <a name="blob-lifecycle-management"></a>Zarządzanie cyklem życia obiektów BLOB

Funkcja zarządzania cyklem życia magazynu obiektów BLOB oferuje rozbudowane zasady oparte na regułach, które umożliwiają przechodzenie danych do najlepszej warstwy dostępu i wygasanie danych na koniec cyklu życia. Aby dowiedzieć się więcej [, zobacz Optymalizacja kosztów dzięki automatyzowaniu warstw dostępu BLOB Storage platformy Azure](storage-lifecycle-management-concepts.md) .

> [!NOTE]
> Dane przechowywane na koncie magazynu blokowych obiektów BLOB (wydajność Premium) nie mogą być obecnie warstwami gorąca, chłodna lub archiwalna przy użyciu [warstwy obiektu BLOB](/rest/api/storageservices/set-blob-tier) lub usługi Azure Blob Storage.
> Aby przenieść dane, należy synchronicznie skopiować obiekty blob z konta blokowego usługi BLOB Storage do warstwy dostępu gorąca na innym koncie przy użyciu [funkcji Put blok z adresu URL](/rest/api/storageservices/put-block-from-url) lub wersji AzCopy, która obsługuje ten interfejs API.
> **Blok Put z adresu URL** synchronicznie kopiuje dane na serwerze, co oznacza, że wywołanie kończy się tylko wtedy, gdy wszystkie dane są przenoszone z oryginalnej lokalizacji serwera do lokalizacji docelowej.

### <a name="blob-level-tiering-billing"></a>Rozliczanie obsługi warstw na poziomie obiektów blob

Gdy obiekt BLOB jest przekazywany lub przenoszony między warstwami, jest naliczana według odpowiedniej stawki natychmiast po przekazaniu lub zmianie warstwy.

Gdy obiekt BLOB jest przenoszony do warstwy chłodnicy (gorąca >chłodna, archiwum gorąca >lub archiwum chłodnych >), operacja jest rozliczana jako operacja zapisu w warstwie docelowej, w przypadku której stosuje się opłaty za operacje zapisu (za 10 000) i zapis danych (za GB) dla warstwy docelowej.

Gdy obiekt BLOB jest przenoszony do warstwy gorąca (archiwum->chłodna, >archiwalna lub chłodna >gorąca), operacja jest rozliczana jako odczyt z warstwy źródłowej, gdzie stosowane są opłaty za operacje odczytu (za 10 000) i pobieranie danych (za GB) dla warstwy źródłowej. Można również zastosować opłaty za [wczesne usunięcie](#cool-and-archive-early-deletion) dla dowolnego obiektu BLOB z warstwy chłodna lub archiwalna. [Dane ponownego wypełniania z warstwy archiwum](storage-blob-rehydration.md) zajmują czas, a dane będą obciążane cenami, dopóki dane nie zostaną przywrócone w trybie online, a warstwa obiektu BLOB zmieni się na gorąca lub chłodna.

Poniższa tabela zawiera podsumowanie sposobu rozliczania zmian warstwy.

| | **Opłaty za zapis (operacja i dostęp)** | **Opłaty za odczyt (operacja i dostęp)** |
| ---- | ----- | ----- |
| **Ustawianie warstwy obiektu blob** | Chłodna > gorąca<br> Archiwum > gorąca<br> Archiwum > chłodnych | Archiwum — > chłodna<br> Archiwizuj — > gorąca<br> Chłodna > gorąca

### <a name="cool-and-archive-early-deletion"></a>Opłaty za wcześniejsze usunięcie w warstwach Chłodna i Archiwum

Każdy obiekt BLOB, który jest przenoszony do warstwy chłodna (tylko konta GPv2), jest objęty okresem wcześniejszego usunięcia wynoszącym 30 dni. Każdy obiekt BLOB, który jest przenoszony do warstwy archiwum, podlega okresowi wcześniejszego usunięcia z archiwum 180 dni. Ta opłata jest naliczana proporcjonalnie. Jeśli na przykład obiekt BLOB zostanie przeniesiony do archiwum, a następnie usunięty lub przeniesiony do warstwy gorąca po 45 dniach, zostanie naliczona opłata za wczesne usunięcie, równą 135 (180 minus 45) dni przechowywania tego obiektu BLOB w archiwum.

Istnieją pewne szczegóły dotyczące przechodzenia między warstwami chłodna i archiwalna:

1. Jeśli obiekt BLOB jest wywnioskowany jako chłodny na podstawie domyślnej warstwy dostępu konta magazynu, a obiekt BLOB zostanie przeniesiony do archiwum, nie ma opłaty za wczesne usunięcie.
1. Jeśli obiekt BLOB zostanie jawnie przeniesiony do warstwy chłodna, a następnie przeniesiony do archiwum, obowiązuje opłata za wczesne usunięcie.

Oblicz czas wczesnego usunięcia przy użyciu **ostatniej zmodyfikowanej** właściwości obiektu BLOB, jeśli nie ma żadnych zmian w warstwie dostępu. W przeciwnym razie użyj, gdy warstwa dostępu była ostatnio modyfikowana do chłodna lub archiwalna, wyświetlając właściwość obiektu BLOB: **Warstwa dostępu-zmiana-czas**. Aby uzyskać więcej informacji na temat właściwości obiektów blob, zobacz [pobieranie właściwości obiektów BLOB](/rest/api/storageservices/get-blob-properties).

## <a name="comparing-block-blob-storage-options"></a>Porównywanie opcji magazynu blokowych obiektów BLOB

W poniższej tabeli przedstawiono porównanie magazynu obiektów BLOB wydajności w warstwie Premium oraz warstw dostępu gorąca, chłodna i archiwalna.

|                                           | **Wydajność warstwy Premium**   | **Warstwa gorąca** | **Warstwa chłodna**       | **Warstwa Archiwum**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **Dostępność**                          | 99,9%                     | 99,9%        | 99%                 | Tryb offline           |
| **Dostępność** <br> **(odczyty RA-GRS)**  | Nie dotyczy                       | 99,99%       | 99,9%               | Tryb offline           |
| **Opłaty za użycie**                         | Wyższe koszty magazynowania, niższy dostęp i koszt transakcji | Wyższe koszty magazynowania, niższy dostęp i koszty transakcji | Niższe koszty magazynowania, wyższego poziomu dostępu i kosztów transakcji | Najniższe koszty magazynowania, najwyższy poziom dostępu i koszty transakcji |
| **Minimalny czas magazynowania**              | NIE DOTYCZY                       | NIE DOTYCZY          | 30 dni<sup>1</sup> | 180 dni
| **Opóźnienie** <br> **(czas do pierwszego bajtu)** | Jednocyfrowe milisekundy | milisekundy | milisekundy        | godz.<sup>2</sup> |

<sup>1</sup> obiekty w warstwie chłodna na kontach GPv2 mają minimalny okres przechowywania równy 30 dni. Konta Blob Storage nie mają minimalnego czasu przechowywania dla warstwy chłodna.

<sup>2</sup> Archive Storage obecnie obsługuje dwa priorytety uzupełniania, wysoki i standardowy, oferując różne opóźnienia i koszty pobierania. Aby uzyskać więcej informacji, zobacz informacje o [rehydratacji danych obiektów blob z warstwy archiwum](storage-blob-rehydration.md).

> [!NOTE]
> Konta Blob Storage obsługują te same cele dotyczące wydajności i skalowalności co konta magazynu ogólnego przeznaczenia w wersji 2. Aby uzyskać więcej informacji, zobacz [elementy docelowe skalowalności i wydajności dla BLOB Storage](scalability-targets.md).

## <a name="pricing-and-billing"></a>Cennik i rozliczenia

Wszystkie konta magazynu używają modelu cen dla magazynu blokowych obiektów BLOB na podstawie warstwy każdego obiektu BLOB. Należy pamiętać o następujących kwestiach dotyczących rozliczeń:

- **Koszty usługi Storage**: oprócz ilości przechowywanych danych koszt przechowywania danych różni się w zależności od warstwy dostępu. Koszt za gigabajt zmniejsza się w miarę, jak warstwa staje się chłodniejsza.
- **Koszty dostępu do danych**: opłaty za dostęp do danych wzrastają w miarę, jak warstwa staje się chłodniejsza. W przypadku danych w warstwie dostępu chłodna i archiwalna naliczana jest opłata za dostęp do danych za każdy gigabajt dla operacji odczytu.
- **Koszty transakcji**: jest naliczana opłata za transakcję dla wszystkich warstw, które zwiększają się, gdy warstwa uzyskuje chłodnicę.
- **Koszty transferu danych replikacji geograficznej**: Ta opłata dotyczy tylko kont ze skonfigurowaną replikacją geograficzną, w tym GRS i RA-GRS. Transfer danych w ramach replikacji geograficznej powoduje naliczanie opłaty za każdy gigabajt.
- **Koszty transferu danych wychodzących**: transfery danych wychodzących (dane przesyłane poza region platformy Azure) powodują naliczanie opłat za zużycie przepustowości za każdy gigabajt, co jest spójne z kontami magazynu ogólnego przeznaczenia.
- **Zmiana warstwy dostępu**: zmiana warstwy dostępu do konta spowoduje naliczanie opłat za zmianę warstwy dla wszystkich obiektów blob, które nie mają jawnie ustawionej warstwy. Aby uzyskać informacje na temat zmiany warstwy dostępu dla pojedynczego obiektu BLOB, zobacz [rozliczenia warstwowe na poziomie obiektów BLOB](#blob-level-tiering-billing).

    Zmiana warstwy dostępu dla obiektu BLOB, gdy jest włączona obsługa wersji, lub jeśli obiekt BLOB zawiera migawki, może spowodować naliczenie dodatkowych opłat. Aby uzyskać informacje na temat obiektów blob z włączoną obsługą wersji, zobacz [Cennik i rozliczenia](versioning-overview.md#pricing-and-billing) w dokumentacji dotyczącej wersji obiektów BLOB. Aby uzyskać informacje na temat obiektów blob z migawkami, zobacz [Cennik i rozliczenia](snapshots-overview.md#pricing-and-billing) w dokumentacji migawek obiektów BLOB.

> [!NOTE]
> Aby uzyskać więcej informacji na temat cen blokowych obiektów blob, zobacz temat [Zablokuj ceny obiektów BLOB](https://azure.microsoft.com/pricing/details/storage/blobs/). Aby uzyskać więcej informacji na temat opłat za transfer danych wychodzących, zobacz stronę [szczegółów cennika przepustowości](https://azure.microsoft.com/pricing/details/bandwidth/) .

## <a name="availability"></a>Dostępność

Różne warstwy dostępu wraz z warstwami na poziomie obiektów BLOB są dostępne w wybranych regionach. Pełną listę można znaleźć w temacie [Dostępność produktów platformy Azure według regionów](https://azure.microsoft.com/global-infrastructure/services/?products=storage).

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak zarządzać obiektami BLOB i kontami w warstwach dostępu.

- [Jak zarządzać warstwą obiektu BLOB na koncie usługi Azure Storage](manage-access-tier.md)
- [Jak zarządzać domyślną warstwą dostępu do konta usługi Azure Storage](../common/manage-account-default-access-tier.md)
- [Optymalizowanie kosztów dzięki automatyzowaniu warstw dostępu Blob Storage platformy Azure](storage-lifecycle-management-concepts.md)
