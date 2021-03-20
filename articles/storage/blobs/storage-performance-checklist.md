---
title: Lista kontrolna wydajności i skalowalności usługi BLOB Storage — Azure Storage
description: Lista kontrolna sprawdzonych praktyk do użycia z usługą BLOB Storage w przypadku tworzenia aplikacji o wysokiej wydajności.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: 14da8b6cb695703f1881b6b0b9858772bde386c5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95544755"
---
# <a name="performance-and-scalability-checklist-for-blob-storage"></a>Lista kontrolna wydajności i skalowalności usługi BLOB Storage

Firma Microsoft opracowała kilka sprawdzonych praktyk tworzenia aplikacji o wysokiej wydajności za pomocą usługi BLOB Storage. Ta lista kontrolna zawiera najważniejsze rozwiązania, które deweloperzy mogą wykonać w celu zoptymalizowania wydajności. Należy pamiętać o tych praktykach podczas projektowania aplikacji i w trakcie całego procesu.

Usługa Azure Storage oferuje cele skalowalności i wydajności dla pojemności, szybkości transakcji i przepustowości. Aby uzyskać więcej informacji o obiektach docelowych skalowalności usługi Azure Storage, zobacz [cele skalowalności i wydajności dla standardowych kont magazynu](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) i [skalowalności i wydajności docelowych dla usługi BLOB Storage](scalability-targets.md).

## <a name="checklist"></a>Lista kontrolna

Ten artykuł organizuje sprawdzone rozwiązania dotyczące wydajności w ramach listy kontrolnej, którą można wykonać podczas tworzenia aplikacji usługi BLOB Storage.

| Gotowe | Kategoria | Zagadnienie dotyczące projektowania |
| --- | --- | --- |
| &nbsp; |Tarcze skalowalności |[Czy można zaprojektować aplikację tak, aby korzystała z nie więcej niż maksymalna liczba kont magazynu?](#maximum-number-of-storage-accounts) |
| &nbsp; |Tarcze skalowalności |[Czy unikasz zbliżania się limitów pojemności i transakcji?](#capacity-and-transaction-targets) |
| &nbsp; |Tarcze skalowalności |[Czy duża liczba klientów uzyskuje dostęp do pojedynczego obiektu BLOB współbieżnie?](#multiple-clients-accessing-a-single-blob-concurrently) |
| &nbsp; |Tarcze skalowalności |[Czy Twoja aplikacja mieści się w celach skalowalności dla pojedynczego obiektu BLOB?](#bandwidth-and-operations-per-blob) |
| &nbsp; |Partycjonowanie |[Czy Twoja Konwencja nazewnictwa została zaprojektowana w celu umożliwienia lepszego równoważenia obciążenia?](#partitioning) |
| &nbsp; |Sieć |[Czy urządzenia po stronie klienta mają dostatecznie wysoką przepustowość i małe opóźnienia w celu osiągnięcia wymaganej wydajności?](#throughput) |
| &nbsp; |Sieć |[Czy urządzenia po stronie klienta mają link do sieci o wysokiej jakości?](#link-quality) |
| &nbsp; |Sieć |[Czy aplikacja kliencka znajduje się w tym samym regionie co konto magazynu?](#location) |
| &nbsp; |Bezpośredni dostęp klienta |[Czy używasz sygnatur dostępu współdzielonego (SAS) i udostępniania zasobów między źródłami (CORS), aby umożliwić bezpośredni dostęp do usługi Azure Storage?](#sas-and-cors) |
| &nbsp; |Buforowanie |[Czy aplikacja buforuje dane, które są często używane i rzadko zmieniane?](#reading-data) |
| &nbsp; |Buforowanie |[Czy aplikacja wsadowa aktualizuje aktualizacje przez buforowanie ich na kliencie, a następnie przekazywanie ich w większych zestawach?](#uploading-data-in-batches) |
| &nbsp; |Konfiguracja platformy .NET |[Czy używasz platformy .NET Core 2,1 lub nowszej w celu uzyskania optymalnej wydajności?](#use-net-core) |
| &nbsp; |Konfiguracja platformy .NET |[Czy skonfigurowano klienta tak, aby używał wystarczającej liczby jednoczesnych połączeń?](#increase-default-connection-limit) |
| &nbsp; |Konfiguracja platformy .NET |[Czy w przypadku aplikacji .NET skonfigurowano platformę .NET do używania wystarczającej liczby wątków?](#increase-minimum-number-of-threads) |
| &nbsp; |Równoległości |[Czy istnieje pewność, że równoległość jest odpowiednio ograniczona, aby nie można było przeciążać możliwości klienta ani podejścia do celów skalowalności?](#unbounded-parallelism) |
| &nbsp; |Narzędzia |[Czy używasz najnowszych wersji bibliotek i narzędzi klienta dostarczonych przez firmę Microsoft?](#client-libraries-and-tools) |
| &nbsp; |Ponowne próby |[Czy zasady ponawiania są używane z wykładniczą wycofywaniaą do ograniczania błędów i przekroczeń limitu czasu?](#timeout-and-server-busy-errors) |
| &nbsp; |Ponowne próby |[Czy aplikacja unika ponawiania prób w przypadku błędów, które nie są ponawiane?](#non-retryable-errors) |
| &nbsp; |Kopiowanie obiektów BLOB |[Czy są kopiowane obiekty blob w sposób najbardziej wydajny?](#blob-copy-apis) |
| &nbsp; |Kopiowanie obiektów BLOB |[Czy używasz najnowszej wersji programu AzCopy na potrzeby operacji kopiowania zbiorczego?](#use-azcopy) |
| &nbsp; |Kopiowanie obiektów BLOB |[Czy używasz rodziny Azure Data Box do importowania dużych ilości danych?](#use-azure-data-box) |
| &nbsp; |Dystrybucja zawartości |[Czy używasz sieci CDN do dystrybucji zawartości?](#content-distribution) |
| &nbsp; |Użyj metadanych |[Czy są przechowywane często używane metadane dotyczące obiektów BLOB w swoich metadanych?](#use-metadata) |
| &nbsp; |Szybkie przekazywanie |[Czy podczas próby przekazania jednego obiektu BLOB szybko przekazująsz bloki?](#upload-one-large-blob-quickly) |
| &nbsp; |Szybkie przekazywanie |[Czy podczas próby przeładowania wielu obiektów BLOB szybko przekazujesz obiekty blob?](#upload-many-blobs-quickly) |
| &nbsp; |Typ obiektów blob |[Czy używasz stronicowych obiektów blob lub blokowych obiektów BLOB w razie potrzeby?](#choose-the-correct-type-of-blob) |

## <a name="scalability-targets"></a>Tarcze skalowalności

Jeśli aplikacja zbliża się lub przekroczy elementy docelowe skalowalności, może wystąpić zwiększenie opóźnień transakcji lub ograniczenie przepustowości. Gdy usługa Azure Storage ogranicza swoją aplikację, rozpocznie się zwracanie kodów błędów 503 (serwer zajęty) lub 500 (limit czasu operacji). Unikanie tych błędów przez Przekroczenie limitów celów skalowalności jest ważną częścią zwiększania wydajności aplikacji.

Aby uzyskać więcej informacji dotyczących skalowalności usługa kolejki, zobacz [cele dotyczące skalowalności i wydajności usługi Azure Storage](../queues/scalability-targets.md#scale-targets-for-queue-storage).

### <a name="maximum-number-of-storage-accounts"></a>Maksymalna liczba kont magazynu

Jeśli zbliżasz się do maksymalnej liczby kont magazynu dozwolonych dla konkretnej kombinacji subskrypcji/regionu, Oceń swój scenariusz i ustal, czy są spełnione następujące warunki:

- Czy używasz kont magazynu do przechowywania dysków niezarządzanych i dodawania tych dysków do maszyn wirtualnych? W tym scenariuszu firma Microsoft zaleca używanie dysków zarządzanych. Skalowanie dysków zarządzanych automatycznie i bez konieczności tworzenia poszczególnych kont magazynu i zarządzania nimi. Aby uzyskać więcej informacji, zobacz [wprowadzenie do usługi Azure Managed disks](../../virtual-machines/managed-disks-overview.md)
- Czy używasz jednego konta magazynu dla każdego klienta na potrzeby izolacji danych? W tym scenariuszu firma Microsoft zaleca używanie kontenera obiektów BLOB dla każdego klienta, a nie całego konta magazynu. Usługa Azure Storage umożliwia teraz Przypisywanie ról platformy Azure dla poszczególnych kontenerów. Aby uzyskać więcej informacji, zobacz [używanie Azure Portal do przypisywania roli platformy Azure na potrzeby dostępu do danych obiektów blob i kolejek](../common/storage-auth-aad-rbac-portal.md).
- Czy używasz wielu kont magazynu do fragmentu, aby zwiększyć ruch przychodzący, ruch wychodzący, operacje we/wy na sekundę (IOPS) lub pojemność? W tym scenariuszu firma Microsoft zaleca użycie zwiększonych limitów dla kont magazynu w celu zmniejszenia liczby kont magazynu wymaganych do obciążenia, jeśli jest to możliwe. Skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/options/) , aby zażądać zwiększonych limitów dla konta magazynu. Aby uzyskać więcej informacji, zobacz temat [ogłaszanie większych kont magazynu](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)o większej skali.

### <a name="capacity-and-transaction-targets"></a>Pojemność i cele transakcji

Jeśli aplikacja zbliża się do celów skalowalności dla jednego konta magazynu, należy rozważyć przyjęcie jednej z następujących metod:  

- Jeśli aplikacja trafi na cel transakcji, należy rozważyć użycie kont blokowych usługi BLOB Storage, które są zoptymalizowane pod kątem wysokich stawek transakcji i niskich i spójnych opóźnień. Aby uzyskać więcej informacji, zobacz [Omówienie konta magazynu platformy Azure](../common/storage-account-overview.md).
- Zapoznaj się z obciążeniem, które powoduje, że aplikacja może obsłużyć lub przekroczyć obiekt docelowy skalowalności. Czy można projektować inaczej, aby używać mniejszej przepustowości lub pojemności lub mniejszej liczby transakcji?
- Jeśli aplikacja musi przekroczyć jeden z celów skalowalności, należy utworzyć wiele kont magazynu i podzielić na partycje dane aplikacji na te wiele kont magazynu. Jeśli używasz tego wzorca, pamiętaj, aby zaprojektować aplikację tak, aby w przyszłości można było dodać więcej kont magazynu do równoważenia obciążenia. Same konta magazynu nie mają kosztu innego niż użycie w odniesieniu do danych przechowywanych, wykonanych transakcji lub przesłanych danych.
- Jeśli aplikacja zbliża się do docelowych przepustowości, rozważ skompresowanie danych po stronie klienta, aby zmniejszyć przepustowość wymaganą do wysłania danych do usługi Azure Storage.
    Chociaż kompresowanie danych może zaoszczędzić przepustowość i zwiększyć wydajność sieci, może mieć także negatywny wpływ na wydajność. Oceń wpływ dodatkowych wymagań związanych z przetwarzaniem na kompresję danych i dekompresję po stronie klienta. Należy pamiętać, że przechowywanie skompresowanych danych może utrudnić rozwiązywanie problemów, ponieważ może być trudniejsze do wyświetlania danych przy użyciu standardowych narzędzi.
- Jeśli aplikacja zbliża się do elementów docelowych skalowalności, upewnij się, że używasz wykładniczej wycofywania do ponawiania prób. Najlepszym rozwiązaniem jest uniknięcie osiągnięcia celów skalowalności przez implementację zaleceń opisanych w tym artykule. Jednak użycie wykładniczej wycofywania na potrzeby ponownych prób uniemożliwi szybkiej próby aplikacji, co może spowodować, że ograniczanie wydajności będzie gorsze. Aby uzyskać więcej informacji, zobacz sekcję zatytułowaną [limity czasu i błędy zajęte serwera](#timeout-and-server-busy-errors).

### <a name="multiple-clients-accessing-a-single-blob-concurrently"></a>Wielu klientów uzyskujących dostęp do pojedynczego obiektu BLOB współbieżnie

Jeśli masz dużą liczbę klientów uzyskujących dostęp do pojedynczego obiektu BLOB współbieżnie, musisz wziąć pod uwagę zarówno dla obiektów blob, jak i dla elementów docelowych skalowalności konta magazynu. Dokładna liczba klientów, którzy mogą uzyskać dostęp do pojedynczego obiektu BLOB, różni się w zależności od czynników, takich jak liczba klientów żądających obiektu BLOB jednocześnie, rozmiar obiektu BLOB i warunki sieciowe.

Jeśli obiekt BLOB może być dystrybuowany za pośrednictwem sieci CDN, takiego jak obrazy lub wideo obsługiwane przez witrynę sieci Web, można użyć sieci CDN. Aby uzyskać więcej informacji, zapoznaj się z sekcją [Dystrybucja zawartości](#content-distribution).

W innych scenariuszach, takich jak symulacje naukowe, gdzie dane są poufne, dostępne są dwie opcje. Pierwszy polega na rozdzieleniu dostępu do obciążenia, tak aby obiekt BLOB był dostępny w danym okresie czasu, a jednocześnie uzyskać do niego dostęp. Alternatywnie można tymczasowo skopiować obiekt BLOB do wielu kont magazynu, aby zwiększyć łączną liczbę operacji we/wy na obiekty blob i na kontach magazynu. Wyniki będą się różnić w zależności od zachowania aplikacji, dlatego należy przetestować wzorce współbieżności podczas projektowania.

### <a name="bandwidth-and-operations-per-blob"></a>Przepustowość i operacje na obiekt BLOB

Pojedynczy obiekt BLOB obsługuje do 500 żądań na sekundę. Jeśli istnieje wielu klientów, którzy muszą odczytać ten sam obiekt BLOB i można przekroczyć ten limit, rozważ użycie konta blokowego magazynu obiektów BLOB. Konto magazynu blokowych obiektów BLOB zapewnia wyższą liczbę żądań lub operacje we/wy na sekundę (IOPS).

Możesz również użyć usługi Content Delivery Network (CDN), takiej jak Azure CDN, aby dystrybuować operacje na obiekcie blob. Aby uzyskać więcej informacji na temat Azure CDN, zobacz [Azure CDN omówienie](../../cdn/cdn-overview.md).  

## <a name="partitioning"></a>Partycjonowanie

Zrozumienie, jak usługa Azure Storage Partitions dane obiektów BLOB są przydatne do zwiększenia wydajności. Usługa Azure Storage może szybciej obsłużyć dane z pojedynczej partycji niż dane obejmujące wiele partycji. Wprowadzając odpowiednie nazwy obiektów blob, można poprawić wydajność żądań odczytu.

Usługa BLOB Storage używa schematu partycjonowania opartego na zakresie na potrzeby skalowania i równoważenia obciążenia. Każdy obiekt BLOB ma klucz partycji składający się z pełnej nazwy obiektu BLOB (account + Container + BLOB). Klucz partycji służy do partycjonowania danych obiektów BLOB w ramach zakresów. Zakresy są następnie zrównoważone obciążenie między magazynem obiektów BLOB.

Partycjonowanie oparte na zakresie oznacza, że konwencje nazewnictwa, które korzystają z porządkowania leksykalnego (na przykład :*log20160101*, *log20160102*, *log20160102* itp. *), są* bardziej podobne do tych, które znajdują się na tym samym serwerze partycji. , do momentu zwiększenia obciążenia wymaga, aby zostały podzielone na mniejsze zakresy. Wspólne lokalizowanie obiektów BLOB na tym samym serwerze partycji zwiększa wydajność, dzięki czemu istotna część rozszerzania wydajności polega na nazewnictwie obiektów BLOB w sposób, który organizuje je najbardziej efektywnie.

Na przykład wszystkie obiekty blob w kontenerze mogą być obsługiwane przez jeden serwer, dopóki obciążenie tych obiektów BLOB nie wymaga dodatkowego ponownego zrównoważenia zakresów partycji. Analogicznie, Grupa jasno załadowanych kont z ich nazwami uporządkowanymi w kolejności leksykalnej może być obsługiwana przez jeden serwer do momentu, gdy obciążenie jednego lub wszystkich tych kont nie będzie wymagało ich podziału na wiele serwerów partycji.

Każda operacja równoważenia obciążenia może mieć wpływ na opóźnienie wywołań magazynu podczas operacji. Zdolność usługi do obsługi nagłego rozłożenia ruchu do partycji jest ograniczona przez skalowalność serwera pojedynczej partycji, dopóki operacja równoważenia obciążenia nie zostanie rozpoczęta i ponownie zrównoważy zakres kluczy partycji.

Aby zmniejszyć częstotliwość takich operacji, można wykonać kilka najlepszych rozwiązań.  

- Jeśli to możliwe, Użyj obiektów blob lub bloków o rozmiarze większym niż 4 MiB dla kont magazynu w warstwie Standardowa i więcej niż 256 KiB dla kont magazynu w warstwie Premium. Większe rozmiary obiektów blob i bloków automatycznie uaktywniają blokowe obiekty blob o wysokiej przepływności. Blokowe obiekty blob o wysokiej przepływności umożliwiają pozyskiwanie wysokiej wydajności, które nie mają wpływ na nazewnictwo partycji.
- Należy zapoznać się z konwencją nazewnictwa używaną dla kont, kontenerów, obiektów blob, tabel i kolejek. Należy rozważyć prefiksowanie nazw kont, kontenerów lub obiektów blob z trzema cyframi skrótu przy użyciu funkcji tworzenia skrótów, która najlepiej odpowiada Twoim potrzebom.
- Jeśli organizujesz dane przy użyciu sygnatur czasowych lub identyfikatorów liczbowych, upewnij się, że nie używasz wzorca ruchu tylko do dołączania (lub tylko do odczytu). Wzorce te nie są odpowiednie dla systemu partycjonowania opartego na zakresie. Wzorce te mogą prowadzić do całego ruchu kierowanego do jednej partycji i ograniczania systemu do efektywnego równoważenia obciążenia.

    Na przykład jeśli masz codzienne operacje, które używają obiektu BLOB z sygnaturą czasową, taką jak *RRRRMMDD*, cały ruch dla tej codziennej operacji jest kierowany do pojedynczego obiektu BLOB, który jest obsługiwany przez serwer z jedną partycją. Należy wziąć pod uwagę, czy limity dla obiektów blob i dla partycji spełniają Twoje potrzeby, i rozważ przerwanie tej operacji do wielu obiektów BLOB w razie potrzeby. Podobnie, Jeśli przechowujesz dane szeregów czasowych w tabelach, cały ruch może być kierowany do ostatniej części przestrzeni nazw klucza. Jeśli używasz identyfikatorów liczbowych, poprzedź identyfikator prefiksem 3-cyfrowym. Jeśli używasz sygnatur czasowych, poprzedź sygnaturę czasową wartością sekund, na przykład *ssyyyymmdd*. Jeśli aplikacja rutynowo wykonuje operacje tworzenia listy i wykonywania zapytań, wybierz funkcję tworzenia skrótów, która będzie ograniczać liczbę zapytań. W niektórych przypadkach losowy prefiks może być wystarczający.
  
- Aby uzyskać więcej informacji o schemacie partycjonowania używanym w usłudze Azure Storage, zobacz [Azure Storage: usługa magazynu w chmurze o wysokiej dostępności z silną spójnością](https://sigops.org/sosp/sosp11/current/2011-Cascais/printable/11-calder.pdf).

## <a name="networking"></a>Sieć

Ograniczenia sieci fizycznej aplikacji mogą mieć znaczący wpływ na wydajność. W poniższych sekcjach opisano niektóre ograniczenia, które mogą napotkać użytkownicy.  

### <a name="client-network-capability"></a>Możliwość sieci klienta

Przepustowość i jakość łącza sieciowego odgrywają ważne role w działaniu aplikacji, zgodnie z opisem w poniższych sekcjach.

#### <a name="throughput"></a>Przepływność

W przypadku przepustowości problem jest często możliwością klienta programu. Większe wystąpienia platformy Azure mają karty sieciowe o większej pojemności, dlatego należy rozważyć użycie większego wystąpienia lub większej liczby maszyn wirtualnych, jeśli potrzebne są wyższe limity sieci z jednej maszyny. Jeśli uzyskujesz dostęp do usługi Azure Storage z poziomu aplikacji lokalnej, ta sama reguła ma zastosowanie: Poznaj możliwości sieciowe urządzenia klienckiego i połączenia sieciowego z lokalizacją usługi Azure Storage, a następnie popraw je w razie potrzeby lub Zaprojektuj aplikację do pracy w ramach swoich możliwości.

#### <a name="link-quality"></a>Jakość łącza

Podobnie jak w przypadku dowolnego użycia sieci należy pamiętać, że warunki sieci powodujące błędy i utrata pakietów spowodują spowolnienie przepływności.  Korzystanie z programu WireShark lub NetMon może pomóc w zdiagnozowaniu tego problemu.  

### <a name="location"></a>Lokalizacja

W każdym środowisku rozproszonym, umieszczenie klienta w sąsiedztwie z serwerem zapewnia najlepszą wydajność. Aby uzyskać dostęp do usługi Azure Storage z najniższym opóźnieniem, Najlepsza lokalizacja klienta jest w tym samym regionie świadczenia usługi Azure. Na przykład jeśli masz aplikację sieci Web platformy Azure, która korzysta z usługi Azure Storage, zlokalizuj je zarówno w jednym regionie, jak zachodnie stany USA, Azja Południowo-Wschodnia. Kolokacja zasobów zmniejsza czas oczekiwania i koszt, ponieważ użycie przepustowości w jednym regionie jest bezpłatne.  

Jeśli aplikacje klienckie będą uzyskiwać dostęp do usługi Azure Storage, ale nie są hostowane na platformie Azure, np. w przypadku aplikacji urządzeń przenośnych lub lokalnych usług przedsiębiorstwa, lokalizowanie konta magazynu w regionie blisko tych klientów może skrócić czas oczekiwania. Jeśli klienci są rozległie dystrybuowani (na przykład niektóre w Ameryka Północna, a niektóre w Europie), należy rozważyć użycie jednego konta magazynu dla każdego regionu. To podejście jest łatwiejsze do wdrożenia, jeśli dane przechowywane przez aplikacje są specyficzne dla poszczególnych użytkowników i nie wymagają replikowania danych między kontami magazynu.

Aby uzyskać szeroką dystrybucję zawartości obiektów blob, Użyj sieci dostarczania zawartości, takiej jak Azure CDN. Aby uzyskać więcej informacji na temat Azure CDN, zobacz [Azure CDN](../../cdn/cdn-overview.md).  

## <a name="sas-and-cors"></a>SAS i CORS

Załóżmy, że musisz autoryzować kod, taki jak JavaScript, który jest uruchomiony w przeglądarce sieci Web użytkownika lub w aplikacji mobilnej dla telefonu, aby uzyskać dostęp do danych w usłudze Azure Storage. Jednym z rozwiązań jest skompilowanie aplikacji usługi, która działa jako serwer proxy. Urządzenie użytkownika uwierzytelnia się za pomocą usługi, która z kolei autoryzuje dostęp do zasobów usługi Azure Storage. W ten sposób można uniknąć ujawniania kluczy konta magazynu na niezabezpieczonych urządzeniach. Jednak takie podejście powoduje znaczne obciążenie aplikacji usługi, ponieważ wszystkie dane przesyłane między urządzeniem użytkownika a usługą Azure Storage muszą przechodzić przez aplikację usługi.

Za pomocą sygnatur dostępu współdzielonego można uniknąć używania aplikacji usługi jako serwera proxy dla usługi Azure Storage. Za pomocą SYGNATURy dostępu współdzielonego można umożliwić urządzeniu użytkownika wykonywanie żądań bezpośrednio do usługi Azure Storage przy użyciu ograniczonego tokenu. Na przykład jeśli użytkownik chce przekazać zdjęcie do aplikacji, aplikacja usługi może wygenerować sygnaturę dostępu współdzielonego i wysłać ją do urządzenia użytkownika. Token sygnatury dostępu współdzielonego może udzielić uprawnienia do zapisu w zasobie usługi Azure Storage przez określony przedział czasu, po upływie którego wygasa token sygnatury dostępu współdzielonego. Aby uzyskać więcej informacji na temat SAS, zobacz [udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](../common/storage-sas-overview.md).  

Zwykle przeglądarka sieci Web nie zezwala na używanie języka JavaScript na stronie hostowanej przez witrynę sieci Web w jednej domenie w celu wykonywania niektórych operacji, takich jak operacje zapisu, do innej domeny. Te zasady są nazywane zasadami tego samego źródła, co uniemożliwia złośliwemu skryptowi na jednej stronie uzyskanie dostępu do danych na innej stronie sieci Web. Jednak zasady tego samego źródła mogą stanowić ograniczenie podczas kompilowania rozwiązania w chmurze. Udostępnianie zasobów między źródłami (CORS) to funkcja przeglądarki, która umożliwia domenie docelowej komunikowanie się z przeglądarką, która ufa żądaniami pochodzącymi z domeny źródłowej.

Załóżmy na przykład, że aplikacja sieci Web działająca na platformie Azure wysyła żądanie do konta usługi Azure Storage. Aplikacja sieci Web jest domeną źródłową, a konto magazynu jest domeną docelową. Można skonfigurować mechanizm CORS dla dowolnej usługi Azure Storage, aby komunikować się z przeglądarką sieci Web, którą żądania z domeny źródłowej są zaufane przez usługę Azure Storage. Aby uzyskać więcej informacji na temat mechanizmu CORS, zobacz [Obsługa zasobów między źródłami (CORS) dla usługi Azure Storage](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).  
  
Zarówno sygnatury dostępu współdzielonego, jak i CORS mogą pomóc uniknąć niepotrzebnego obciążenia aplikacji sieci Web.  

## <a name="caching"></a>Buforowanie

Buforowanie odgrywa ważną rolę w wydajności. W poniższych sekcjach omówiono najlepsze rozwiązania dotyczące buforowania.

### <a name="reading-data"></a>Odczytywanie danych

Ogólnie rzecz biorąc, odczyt danych jest preferowany do odczytu. Rozważmy przykład aplikacji sieci Web, która pobrała obiekt BLOB 50 MiB z usługi Azure Storage, aby zapewnić użytkownikowi zawartość. W idealnym przypadku aplikacja buforuje obiekt BLOB lokalnie na dysku, a następnie pobiera buforowaną wersję dla kolejnych żądań użytkownika.

Jednym ze sposobów, aby uniknąć pobierania obiektu BLOB, jeśli nie został on zmodyfikowany od momentu jego zbuforowania, jest zakwalifikowanie operacji GET do nagłówka warunkowego w celu zmodyfikowania czasu. Jeśli godzina ostatniej modyfikacji jest późniejsza niż czas buforowania obiektu BLOB, obiekt BLOB zostanie pobrany i ponownie zbuforowany. W przeciwnym razie buforowany obiekt BLOB zostanie pobrany w celu uzyskania optymalnej wydajności.

Możesz również zdecydować się na zaprojektowanie aplikacji, aby założyć, że obiekt BLOB pozostaje niezmieniony przez krótki czas po jego pobraniu. W takim przypadku aplikacja nie musi sprawdzać, czy obiekt BLOB został zmodyfikowany w tym interwale.

Dane konfiguracji, dane wyszukiwania i inne dane, które są często używane przez aplikację, są dobrymi kandydatami do buforowania.  

Aby uzyskać więcej informacji na temat używania nagłówków warunkowych, zobacz [Określanie nagłówków warunkowych dla operacji BLOB Service](/rest/api/storageservices/specifying-conditional-headers-for-blob-service-operations).  

### <a name="uploading-data-in-batches"></a>Przekazywanie danych w partiach

W niektórych scenariuszach dane można agregować lokalnie, a następnie okresowo przekazać je do partii, zamiast bezpośrednio przekazywać dane. Załóżmy na przykład, że aplikacja sieci Web przechowuje plik dziennika działań. Aplikacja może przekazać szczegółowe informacje o każdym działaniu z tabeli (która wymaga wielu operacji magazynowania) lub zapisać szczegóły działania w lokalnym pliku dziennika, a następnie okresowo przekazać wszystkie szczegóły działania jako rozdzielany plik do obiektu BLOB. Jeśli każdy wpis dziennika ma rozmiar 1 KB, można przekazać tysiące wpisów w jednej transakcji. Pojedyncza transakcja obsługuje przekazywanie obiektu BLOB o rozmiarze do 64 MiB. Deweloper aplikacji musi projektować pod kątem możliwości niepowodzeń urządzeń klienckich lub przeładowania. Jeśli dane dotyczące aktywności wymagają pobrania przez przedział czasu, a nie dla pojedynczego działania, zaleca się użycie magazynu obiektów BLOB w ramach usługi Table Storage.

## <a name="net-configuration"></a>Konfiguracja platformy .NET

W przypadku korzystania z .NET Framework Ta sekcja zawiera kilka ustawień szybkiego konfigurowania, których można użyć w celu zwiększenia wydajności.  Jeśli używasz innych języków, sprawdź, czy podobne koncepcje dotyczą wybranego języka.  

### <a name="use-net-core"></a>Korzystanie z platformy .NET Core

Utwórz aplikacje usługi Azure Storage za pomocą platformy .NET Core 2,1 lub nowszej, aby skorzystać z ulepszeń wydajności. Jeśli jest to możliwe, zalecamy korzystanie z programu .NET Core 3. x.

Aby uzyskać więcej informacji na temat ulepszeń wydajności w programie .NET Core, zobacz następujące wpisy w blogu:

- [Ulepszenia wydajności w programie .NET Core 3,0](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Ulepszenia wydajności w programie .NET Core 2,1](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Zwiększ domyślny limit połączeń

W programie .NET Poniższy kod zwiększa domyślny limit połączeń (zazwyczaj dwa w środowisku klienta lub dziesięć w środowisku serwera) do 100. Zazwyczaj należy ustawić wartość na około liczbę wątków używanych przez aplikację. Ustaw limit połączeń przed otwarciem wszystkich połączeń.

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

W przypadku innych języków programowania zapoznaj się z dokumentacją, aby określić, jak ustawić limit połączeń.  

Aby uzyskać więcej informacji, zobacz blog [usługi sieci Web w blogu: połączenia współbieżne](/archive/blogs/darrenj/web-services-concurrent-connections).  

### <a name="increase-minimum-number-of-threads"></a>Zwiększ minimalną liczbę wątków

Jeśli używasz wywołań synchronicznych razem z zadaniami asynchronicznymi, możesz chcieć zwiększyć liczbę wątków w puli wątków:

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Aby uzyskać więcej informacji, zobacz [SetMinThreads —](/dotnet/api/system.threading.threadpool.setminthreads) .  

## <a name="unbounded-parallelism"></a>Nieograniczona równoległość

Chociaż równoległość może być świetna dla wydajności, należy zachować ostrożność przy użyciu nieograniczonej równoległości, co oznacza, że nie ma żadnego ograniczenia dotyczącego liczby wątków lub żądań równoległych. Należy pamiętać, aby ograniczyć liczbę żądań równoległych do przekazywania lub pobierania danych, uzyskać dostęp do wielu partycji na tym samym koncie magazynu lub uzyskać dostęp do wielu elementów w tej samej partycji. Jeśli równoległość jest nieograniczona, aplikacja może przekroczyć możliwości urządzenia klienckiego lub skalowalności konta magazynu, co powoduje dłuższe opóźnienia i ograniczanie przepustowości.  

## <a name="client-libraries-and-tools"></a>Biblioteki i narzędzia klienta

Aby uzyskać najlepszą wydajność, zawsze używaj najnowszych bibliotek i narzędzi klienta dostarczonych przez firmę Microsoft. Biblioteki klienckie usługi Azure Storage są dostępne dla różnych języków. Usługa Azure Storage obsługuje również program PowerShell i interfejs wiersza polecenia platformy Azure. Firma Microsoft aktywnie opracowuje te biblioteki i narzędzia klienckie z uwzględnieniem wydajności, a następnie zapewnia ich aktualność przy użyciu najnowszych wersji usługi i gwarantuje, że obsługa wielu sprawdzonych rozwiązań dotyczących wydajności jest wewnętrznie.

## <a name="handle-service-errors"></a>Obsługa błędów usługi

Usługa Azure Storage zwraca błąd, jeśli nie można przetworzyć żądania przez usługę. Informacje o błędach, które mogą zostać zwrócone przez usługę Azure Storage w danym scenariuszu, są przydatne w celu optymalizacji wydajności.

### <a name="timeout-and-server-busy-errors"></a>Błędy i czas zajętości serwera

Usługa Azure Storage może ograniczać swoją aplikację, jeśli zbliża się ona do ograniczeń skalowalności. W niektórych przypadkach usługa Azure Storage może nie być w stanie obsłużyć żądania ze względu na przejściowy warunek. W obu przypadkach usługa może zwrócić błąd 503 (serwer zajęty) lub 500 (limit czasu). Te błędy mogą również wystąpić, jeśli usługa przestawia partycje danych w celu zapewnienia większej przepływności. Aplikacja kliencka powinna zwykle ponowić próbę wykonania operacji, która powoduje wystąpienie jednego z tych błędów. Jeśli jednak usługa Azure Storage ogranicza swoją aplikację, ponieważ przekracza ona elementy docelowe skalowalności, a nawet jeśli nie jest w stanie obsłużyć żądania z innego powodu, agresywne ponawianie prób może spowodować, że problem będzie gorszy. Zalecane jest użycie wykładniczej zasady ponawiania prób, a biblioteki klienckie domyślnie to zachowanie. Na przykład aplikacja może ponowić próbę po upływie 2 sekund, następnie 4 sekund, następnie 10 sekund, a następnie 30 sekundach, a następnie zadawać całkowicie. W ten sposób aplikacja znacznie zmniejsza obciążenie usługi, a nie zachowanie, które może prowadzić do ograniczenia.  

Błędy łączności mogą być podejmowane natychmiast, ponieważ nie są one wynikiem ograniczenia przepustowości i powinny być przejściowe.  

### <a name="non-retryable-errors"></a>Błędy nieponowień

Biblioteki klienta obsługują ponawianie prób z świadomością, które błędy mogą być ponawiane i które nie mogą. Jeśli jednak wywołujesz interfejs API REST usługi Azure Storage bezpośrednio, istnieją pewne błędy, które nie powinny być ponawiane. Na przykład błąd 400 (złe żądanie) wskazuje, że aplikacja kliencka wysłała żądanie, którego nie można było przetworzyć, ponieważ nie ma oczekiwanego formularza. Ponowne wysłanie tego żądania spowoduje takie samo odpowiedzi, więc nie ma żadnego punktu na ponawianie próby. Jeśli wywołujesz interfejs API REST usługi Azure Storage bezpośrednio, weź pod uwagę potencjalne błędy i czy należy ponowić próbę.

Aby uzyskać więcej informacji na temat kodów błędów usługi Azure Storage, zobacz informacje o [stanie i kodach błędów](/rest/api/storageservices/status-and-error-codes2).

## <a name="copying-and-moving-blobs"></a>Kopiowanie i przeniesienie obiektów BLOB

Usługa Azure Storage udostępnia wiele rozwiązań do kopiowania i przemieszczania obiektów BLOB w ramach konta magazynu, między kontami magazynu i między systemami lokalnymi a chmurą. W tej sekcji opisano niektóre z tych opcji w zależności od ich wpływu na wydajność. Aby uzyskać informacje na temat wydajnego transferu danych do lub z magazynu obiektów blob, zobacz [Wybieranie rozwiązania platformy Azure do transferu danych](../common/storage-choose-data-transfer-solution.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="blob-copy-apis"></a>Interfejsy API kopiowania obiektów BLOB

Aby skopiować obiekty blob na kontach magazynu, użyj operacji [Put bloku z adresu URL](/rest/api/storageservices/put-block-from-url) . Ta operacja kopiuje dane synchronicznie z dowolnego źródła adresu URL do blokowego obiektu BLOB. Użycie `Put Block from URL` operacji może znacznie zmniejszyć wymaganą przepustowość podczas migrowania danych między kontami magazynu. Ponieważ operacja kopiowania odbywa się po stronie usługi, nie trzeba pobierać i ponownie ładować danych.

Aby skopiować dane w ramach tego samego konta magazynu, użyj operacji [kopiowania obiektu BLOB](/rest/api/storageservices/Copy-Blob) . Kopiowanie danych w ramach tego samego konta magazynu jest zwykle wykonywane szybko.  

### <a name="use-azcopy"></a>Korzystanie z narzędzia AzCopy

Narzędzie wiersza polecenia AzCopy jest prostą i wydajną opcją transferu zbiorczego obiektów BLOB do, z i między kontami magazynu. AzCopy jest zoptymalizowany pod kątem tego scenariusza i może osiągać wysokie szybkości transferu. AzCopy wersja 10 używa `Put Block From URL` operacji do kopiowania danych obiektów BLOB na kontach magazynu. Aby uzyskać więcej informacji, zobacz [kopiowanie lub przenoszenie danych do usługi Azure Storage za pomocą AzCopy v10](../common/storage-use-azcopy-v10.md).  

### <a name="use-azure-data-box"></a>Użyj Azure Data Box

Aby zaimportować duże ilości danych do usługi BLOB Storage, należy rozważyć użycie Azure Data Box rodziny do transferów w trybie offline. Urządzenia urządzenie Data Box dostarczone przez firmę Microsoft są dobrym rozwiązaniem w przypadku przeniesienia dużych ilości danych na platformę Azure, gdy jest to ograniczone przez czas, dostępność sieci lub koszty. Aby uzyskać więcej informacji, zobacz [dokumentację usługi Azure DataBox](../../databox/index.yml).

## <a name="content-distribution"></a>Dystrybucja zawartości

Czasami aplikacja musi obsłużyć tę samą zawartość dla wielu użytkowników (na przykład klip wideo z pokazem produktu używany na stronie głównej witryny sieci Web) znajdujący się w jednym lub wielu regionach. W tym scenariuszu należy użyć Content Delivery Network (CDN), takiego jak Azure CDN, aby dystrybuować zawartość obiektów BLOB geograficznie. W przeciwieństwie do konta usługi Azure Storage, które istnieje w jednym regionie i który nie może dostarczać zawartości o małym opóźnieniu do innych regionów, Azure CDN używa serwerów w wielu centrach danych na całym świecie. Ponadto sieć CDN może zazwyczaj obsługiwać znacznie wyższe limity ruchu wychodzącego niż pojedyncze konto magazynu.  

Aby uzyskać więcej informacji na temat Azure CDN, zobacz [Azure CDN](../../cdn/cdn-overview.md).

## <a name="use-metadata"></a>Użyj metadanych

Blob service obsługuje żądania główne, które mogą obejmować właściwości obiektów blob lub metadane. Na przykład, jeśli aplikacja wymaga danych EXIF (format obrazu exchangable) ze zdjęcia, może pobrać zdjęcie i wyodrębnić ją. Aby zaoszczędzić przepustowość i zwiększyć wydajność, aplikacja może przechowywać dane EXIF w metadanych obiektu BLOB, gdy aplikacja przekaże zdjęcie. Następnie można pobrać dane EXIF w metadanych przy użyciu tylko żądania PODRZĘDNEgo. Pobieranie tylko metadanych, a nie całej zawartości obiektu BLOB, oszczędza przepustowość i skraca czas przetwarzania wymagany do wyodrębnienia danych EXIF. Należy pamiętać, że 8 KiB metadanych można przechowywać na obiekt BLOB.  

## <a name="upload-blobs-quickly"></a>Szybkie przekazywanie obiektów BLOB

Aby szybko przekazać obiekty blob, należy najpierw określić, czy będzie przekazywany jeden obiekt BLOB czy wiele. Skorzystaj z poniższych wskazówek, aby określić poprawną metodę do użycia w zależności od danego scenariusza.  

### <a name="upload-one-large-blob-quickly"></a>Szybko przekazuj jeden duży obiekt BLOB

Aby szybko przekazać pojedynczy duży obiekt BLOB, aplikacja kliencka może jednocześnie przekazać swoje bloki lub strony, co jest świadome obiektów docelowych skalowalności dla poszczególnych obiektów blob i konta magazynu jako całości. Biblioteki klienta usługi Azure Storage obsługują przekazywanie równolegle. Na przykład można użyć następujących właściwości, aby określić liczbę równoczesnych żądań dozwolonych w programie .NET lub Java. Biblioteki klienckie dla innych obsługiwanych języków zapewniają podobne opcje.

- Dla platformy .NET ustaw właściwość [BlobRequestOptions. ParallelOperationThreadCount](/dotnet/api/microsoft.azure.storage.blob.blobrequestoptions.paralleloperationthreadcount) .
- W przypadku języka Java/Android Wywołaj metodę [BlobRequestOptions. setConcurrentRequestCount (końcową liczbę całkowitą concurrentRequestCount)](/java/api/com.microsoft.azure.storage.blob.blobrequestoptions.setconcurrentrequestcount) .

### <a name="upload-many-blobs-quickly"></a>Szybkie przekazywanie wielu obiektów BLOB

Aby szybko przekazać wiele obiektów blob, należy przekazać obiekty blob równolegle. Przekazywanie równolegle jest szybsze niż przekazywanie pojedynczych obiektów BLOB jednocześnie przy użyciu równoległych bloków przekazywania, ponieważ rozkłada je na wiele partycji usługi Storage. AzCopy domyślnie wykonuje operacje przekazywania równolegle i jest to zalecane w tym scenariuszu. Aby uzyskać więcej informacji, zobacz Rozpoczynanie [pracy z usługą AzCopy](../common/storage-use-azcopy-v10.md).  

## <a name="choose-the-correct-type-of-blob"></a>Wybierz poprawny typ obiektu BLOB

Usługa Azure Storage obsługuje blokowe obiekty blob, Dodawanie obiektów blob i stronicowe obiekty blob. Dla danego scenariusza użycia wybór typu obiektu BLOB będzie miał wpływ na wydajność i skalowalność rozwiązania.

Blokowe obiekty blob są odpowiednie, gdy chcesz efektywnie przekazywać duże ilości danych. Na przykład aplikacja kliencka, która przekazuje zdjęcia lub wideo do magazynu obiektów blob, będzie ukierunkowana na blokowe obiekty blob.

Dołączane obiekty blob są podobne do bloków obiektów blob, które składają się z bloków. Gdy modyfikujesz obiekt BLOB dołączania, bloki są dodawane do końca obiektu BLOB. Dołączanie obiektów BLOB jest przydatne w scenariuszach takich jak rejestrowanie, gdy aplikacja musi dodać dane do istniejącego obiektu BLOB.

Stronicowe obiekty blob są odpowiednie, jeśli aplikacja musi wykonywać losowe operacje zapisu na danych. Na przykład dyski maszyny wirtualnej platformy Azure są przechowywane jako stronicowe obiekty blob. Aby uzyskać więcej informacji, zobacz [Omówienie blokowych obiektów blob, dołączanie obiektów blob i stronicowych obiektów BLOB](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs).  

## <a name="next-steps"></a>Następne kroki

- [Cele skalowalności i wydajności dla usługi BLOB Storage](scalability-targets.md)
- [Cele skalowalności i wydajności dla kont magazynu w warstwie Standardowa](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
- [Kody stanu i błędów](/rest/api/storageservices/Status-and-Error-Codes2)