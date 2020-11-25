---
title: Lista kontrolna wydajności i skalowalności w usłudze Table Storage — Azure Storage
description: Lista kontrolna sprawdzonych praktyk do użycia z magazynem tabel podczas opracowywania aplikacji o wysokiej wydajności.
services: storage
author: tamram
ms.author: tamram
ms.service: storage
ms.topic: overview
ms.date: 10/10/2019
ms.subservice: tables
ms.custom: devx-track-csharp
ms.openlocfilehash: 71b1f3cfa1df86b417c468d56f67cd7fe8d71d73
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96004708"
---
# <a name="performance-and-scalability-checklist-for-table-storage"></a>Lista kontrolna wydajności i skalowalności dla usługi Table Storage

Firma Microsoft opracowała wiele sprawdzonych rozwiązań związanych z tworzeniem aplikacji o wysokiej wydajności za pomocą usługi Table Storage. Ta lista kontrolna zawiera najważniejsze rozwiązania, które deweloperzy mogą wykonać w celu zoptymalizowania wydajności. Należy pamiętać o tych praktykach podczas projektowania aplikacji i w trakcie całego procesu.

Usługa Azure Storage oferuje cele skalowalności i wydajności dla pojemności, szybkości transakcji i przepustowości. Aby uzyskać więcej informacji na temat celów skalowalności usługi Azure Storage, zobacz [cele skalowalności i wydajności dla standardowych kont magazynu](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json) i [skalowalności i wydajności docelowych dla magazynu tabel](scalability-targets.md).

## <a name="checklist"></a>Lista kontrolna

Ten artykuł organizuje sprawdzone rozwiązania dotyczące wydajności w ramach listy kontrolnej, którą można wykonać podczas tworzenia aplikacji magazynu tabel.

| Gotowe | Kategoria | Zagadnienie dotyczące projektowania |
| --- | --- | --- |
| &nbsp; |Tarcze skalowalności |[Czy można zaprojektować aplikację tak, aby korzystała z nie więcej niż maksymalna liczba kont magazynu?](#maximum-number-of-storage-accounts) |
| &nbsp; |Tarcze skalowalności |[Czy unikasz zbliżania się limitów pojemności i transakcji?](#capacity-and-transaction-targets) |
| &nbsp; |Tarcze skalowalności |[Czy zbliżasz się do elementów docelowych skalowalności dla jednostek na sekundę?](#targets-for-data-operations) |
| &nbsp; |Sieć |[Czy urządzenia po stronie klienta mają dostatecznie wysoką przepustowość i małe opóźnienia w celu osiągnięcia wymaganej wydajności?](#throughput) |
| &nbsp; |Sieć |[Czy urządzenia po stronie klienta mają link do sieci o wysokiej jakości?](#link-quality) |
| &nbsp; |Sieć |[Czy aplikacja kliencka znajduje się w tym samym regionie co konto magazynu?](#location) |
| &nbsp; |Bezpośredni dostęp klienta |[Czy używasz sygnatur dostępu współdzielonego (SAS) i udostępniania zasobów między źródłami (CORS), aby umożliwić bezpośredni dostęp do usługi Azure Storage?](#sas-and-cors) |
| &nbsp; |Przetwarzanie wsadowe |[Czy aplikacja wsadowa aktualizuje aktualizacje przy użyciu transakcji grup jednostek?](#batch-transactions) |
| &nbsp; |Konfiguracja platformy .NET |[Czy używasz platformy .NET Core 2,1 lub nowszej w celu uzyskania optymalnej wydajności?](#use-net-core) |
| &nbsp; |Konfiguracja platformy .NET |[Czy skonfigurowano klienta tak, aby używał wystarczającej liczby jednoczesnych połączeń?](#increase-default-connection-limit) |
| &nbsp; |Konfiguracja platformy .NET |[Czy w przypadku aplikacji .NET skonfigurowano platformę .NET do używania wystarczającej liczby wątków?](#increase-minimum-number-of-threads) |
| &nbsp; |Równoległości |[Czy istnieje pewność, że równoległość jest odpowiednio ograniczona, aby nie można było przeciążać możliwości klienta ani podejścia do celów skalowalności?](#unbounded-parallelism) |
| &nbsp; |Narzędzia |[Czy używasz najnowszych wersji bibliotek i narzędzi klienta dostarczonych przez firmę Microsoft?](#client-libraries-and-tools) |
| &nbsp; |Ponowne próby |[Czy zasady ponawiania są używane z wykładniczą wycofywaniaą do ograniczania błędów i przekroczeń limitu czasu?](#timeout-and-server-busy-errors) |
| &nbsp; |Ponowne próby |[Czy aplikacja unika ponawiania prób w przypadku błędów, które nie są ponawiane?](#non-retryable-errors) |
| &nbsp; |Konfigurowanie |[Czy używasz formatu JSON dla żądań tabeli?](#use-json) |
| &nbsp; |Konfigurowanie |[Czy wyłączono algorytm nagle, aby zwiększyć wydajność małych żądań?](#disable-nagle) |
| &nbsp; |Tabele i partycje |[Czy masz poprawnie partycjonowane dane?](#schema) |
| &nbsp; |Partycje aktywne |[Czy unikasz wzorców tylko do dołączania i tylko do prefiksu?](#append-only-and-prepend-only-patterns) |
| &nbsp; |Partycje aktywne |[Czy operacje wstawiania/aktualizowania są rozłożone na wiele partycji?](#high-traffic-data) |
| &nbsp; |Zakres zapytania |[Czy schemat został zaprojektowany w taki sposób, aby zezwalał na używanie zapytań do punktów w większości przypadków, a zapytania tabeli, które mają być używane oszczędnie?](#query-scope) |
| &nbsp; |Gęstość zapytania |[Czy zapytania zwykle skanują i zwracają wiersze, które będą używane przez aplikację?](#query-density) |
| &nbsp; |Ograniczanie zwracanych danych |[Czy używasz filtrowania, aby uniknąć zwracania jednostek, które nie są potrzebne?](#limiting-the-amount-of-data-returned) |
| &nbsp; |Ograniczanie zwracanych danych |[Czy używasz projekcji, aby uniknąć zwracania właściwości, które nie są potrzebne?](#limiting-the-amount-of-data-returned) |
| &nbsp; |Denormalizacja |[Czy masz rozznormalizowane dane, aby uniknąć nieefektywnych zapytań lub wielu żądań odczytu podczas próby pobrania danych?](#denormalization) |
| &nbsp; |Wstawianie, aktualizowanie i usuwanie |[Czy są przetwarzane zbiorczo żądania, które muszą być transakcyjne lub mogą być wykonywane w tym samym czasie w celu zmniejszenia liczby operacji rundy?](#batching) |
| &nbsp; |Wstawianie, aktualizowanie i usuwanie |[Czy unikasz pobierania jednostki tylko w celu ustalenia, czy chcesz wywołać Wstawianie czy aktualizowanie?](#upsert) |
| &nbsp; |Wstawianie, aktualizowanie i usuwanie |[Czy należy wziąć pod uwagę przechowywanie serii danych, które często będą pobierane razem w jednej jednostce jako właściwości zamiast wielu jednostek?](#storing-data-series-in-a-single-entity) |
| &nbsp; |Wstawianie, aktualizowanie i usuwanie |[W przypadku jednostek, które będą zawsze pobierane razem i mogą być zapisywane w partiach (na przykład dane szeregów czasowych), należy wziąć pod uwagę użycie obiektów BLOB zamiast tabel?](#storing-structured-data-in-blobs) |

## <a name="scalability-targets"></a>Tarcze skalowalności

Jeśli aplikacja zbliża się lub przekroczy elementy docelowe skalowalności, może wystąpić zwiększenie opóźnień transakcji lub ograniczenie przepustowości. Gdy usługa Azure Storage ogranicza swoją aplikację, rozpocznie się zwracanie kodów błędów 503 (serwer zajęty) lub 500 (limit czasu operacji). Unikanie tych błędów przez Przekroczenie limitów celów skalowalności jest ważną częścią zwiększania wydajności aplikacji.

Aby uzyskać więcej informacji dotyczących skalowalności Table service, zobacz [cele skalowalności i wydajności dla usługi Table Storage](scalability-targets.md).

### <a name="maximum-number-of-storage-accounts"></a>Maksymalna liczba kont magazynu

Jeśli zbliżasz się do maksymalnej liczby kont magazynu dozwolonych dla danej kombinacji subskrypcji/regionu, używasz wielu kont magazynu do fragmentu w celu zwiększenia ruchu przychodzącego, wychodzącego, operacji we/wy na sekundę (IOPS) lub pojemności? W tym scenariuszu firma Microsoft zaleca użycie zwiększonych limitów dla kont magazynu w celu zmniejszenia liczby kont magazynu wymaganych do obciążenia, jeśli jest to możliwe. Skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/options/) , aby zażądać zwiększonych limitów dla konta magazynu. Aby uzyskać więcej informacji, zobacz temat [ogłaszanie większych kont magazynu](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)o większej skali.

### <a name="capacity-and-transaction-targets"></a>Pojemność i cele transakcji

Jeśli aplikacja zbliża się do celów skalowalności dla jednego konta magazynu, należy rozważyć przyjęcie jednej z następujących metod:  

- Zapoznaj się z obciążeniem, które powoduje, że aplikacja może obsłużyć lub przekroczyć obiekt docelowy skalowalności. Czy można projektować inaczej, aby używać mniejszej przepustowości lub pojemności lub mniejszej liczby transakcji?
- Jeśli aplikacja musi przekroczyć jeden z celów skalowalności, należy utworzyć wiele kont magazynu i podzielić na partycje dane aplikacji na te wiele kont magazynu. Jeśli używasz tego wzorca, pamiętaj, aby zaprojektować aplikację tak, aby w przyszłości można było dodać więcej kont magazynu do równoważenia obciążenia. Same konta magazynu nie mają kosztu innego niż użycie w odniesieniu do danych przechowywanych, wykonanych transakcji lub przesłanych danych.
- Jeśli aplikacja zbliża się do docelowych przepustowości, rozważ skompresowanie danych po stronie klienta, aby zmniejszyć przepustowość wymaganą do wysłania danych do usługi Azure Storage.
    Chociaż kompresowanie danych może zaoszczędzić przepustowość i zwiększyć wydajność sieci, może mieć także negatywny wpływ na wydajność. Oceń wpływ dodatkowych wymagań związanych z przetwarzaniem na kompresję danych i dekompresję po stronie klienta. Należy pamiętać, że przechowywanie skompresowanych danych może utrudnić rozwiązywanie problemów, ponieważ może być trudniejsze do wyświetlania danych przy użyciu standardowych narzędzi.
- Jeśli aplikacja zbliża się do elementów docelowych skalowalności, upewnij się, że używasz wykładniczej wycofywania do ponawiania prób. Najlepszym rozwiązaniem jest uniknięcie osiągnięcia celów skalowalności przez implementację zaleceń opisanych w tym artykule. Jednak użycie wykładniczej wycofywania na potrzeby ponownych prób uniemożliwi szybkiej próby aplikacji, co może spowodować, że ograniczanie wydajności będzie gorsze. Aby uzyskać więcej informacji, zobacz sekcję zatytułowaną [limity czasu i błędy zajęte serwera](#timeout-and-server-busy-errors).

### <a name="targets-for-data-operations"></a>Elementy docelowe dla operacji na danych

Równoważenie obciążenia usługi Azure Storage w miarę wzrostu ruchu na koncie magazynu zwiększa się, ale jeśli ruch wykazuje nagłe rozerwania, nie można natychmiast uzyskać tej ilości przepływności. W miarę jak usługa Azure Storage automatycznie równoważy obciążenie z tabelą, należy się spodziewać, że są wyświetlane ograniczenia przepustowości i/lub przekroczenia limitu czasu. Powolne spowolnienie zapewnia lepsze wyniki, ponieważ system ma czas odpowiednio do równoważenia obciążenia.

#### <a name="entities-per-second-storage-account"></a>Jednostki na sekundę (konto magazynu)

Limit skalowalności dla dostępu do tabel to 20 000 jednostek (1 KB każdej) na sekundę dla konta. Ogólnie rzecz biorąc, każda jednostka, która jest wstawiana, aktualizowana, usuwana lub skanowana, jest uwzględniana w tym miejscu docelowym. Dzięki temu wstawka wsadowa zawierająca jednostki 100 będzie liczyć jako jednostki 100. Zapytanie, które skanuje jednostki 1000 i zwraca wartość 5, będzie liczyć jako jednostki 1000.

#### <a name="entities-per-second-partition"></a>Jednostki na sekundę (partycja)

W ramach jednej partycji obiekt docelowy skalowalności do uzyskiwania dostępu do tabel to 2 000 jednostek (1 KB każdej) na sekundę przy użyciu tego samego zliczania, jak opisano w poprzedniej sekcji.

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

## <a name="sas-and-cors"></a>SAS i CORS

Załóżmy, że musisz autoryzować kod, taki jak JavaScript, który jest uruchomiony w przeglądarce sieci Web użytkownika lub w aplikacji mobilnej dla telefonu, aby uzyskać dostęp do danych w usłudze Azure Storage. Jednym z rozwiązań jest skompilowanie aplikacji usługi, która działa jako serwer proxy. Urządzenie użytkownika uwierzytelnia się za pomocą usługi, która z kolei autoryzuje dostęp do zasobów usługi Azure Storage. W ten sposób można uniknąć ujawniania kluczy konta magazynu na niezabezpieczonych urządzeniach. Jednak takie podejście powoduje znaczne obciążenie aplikacji usługi, ponieważ wszystkie dane przesyłane między urządzeniem użytkownika a usługą Azure Storage muszą przechodzić przez aplikację usługi.

Za pomocą sygnatur dostępu współdzielonego można uniknąć używania aplikacji usługi jako serwera proxy dla usługi Azure Storage. Za pomocą SYGNATURy dostępu współdzielonego można umożliwić urządzeniu użytkownika wykonywanie żądań bezpośrednio do usługi Azure Storage przy użyciu ograniczonego tokenu. Na przykład jeśli użytkownik chce przekazać zdjęcie do aplikacji, aplikacja usługi może wygenerować sygnaturę dostępu współdzielonego i wysłać ją do urządzenia użytkownika. Token sygnatury dostępu współdzielonego może udzielić uprawnienia do zapisu w zasobie usługi Azure Storage przez określony przedział czasu, po upływie którego wygasa token sygnatury dostępu współdzielonego. Aby uzyskać więcej informacji na temat SAS, zobacz [udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](../common/storage-sas-overview.md).  

Zwykle przeglądarka sieci Web nie zezwala na używanie języka JavaScript na stronie hostowanej przez witrynę sieci Web w jednej domenie w celu wykonywania niektórych operacji, takich jak operacje zapisu, do innej domeny. Te zasady są nazywane zasadami tego samego źródła, co uniemożliwia złośliwemu skryptowi na jednej stronie uzyskanie dostępu do danych na innej stronie sieci Web. Jednak zasady tego samego źródła mogą stanowić ograniczenie podczas kompilowania rozwiązania w chmurze. Udostępnianie zasobów między źródłami (CORS) to funkcja przeglądarki, która umożliwia domenie docelowej komunikowanie się z przeglądarką, która ufa żądaniami pochodzącymi z domeny źródłowej.

Załóżmy na przykład, że aplikacja sieci Web działająca na platformie Azure wysyła żądanie do konta usługi Azure Storage. Aplikacja sieci Web jest domeną źródłową, a konto magazynu jest domeną docelową. Można skonfigurować mechanizm CORS dla dowolnej usługi Azure Storage, aby komunikować się z przeglądarką sieci Web, którą żądania z domeny źródłowej są zaufane przez usługę Azure Storage. Aby uzyskać więcej informacji na temat mechanizmu CORS, zobacz [Obsługa zasobów między źródłami (CORS) dla usługi Azure Storage](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).  
  
Zarówno sygnatury dostępu współdzielonego, jak i CORS mogą pomóc uniknąć niepotrzebnego obciążenia aplikacji sieci Web.  

## <a name="batch-transactions"></a>Transakcje wsadowe

Table service obsługuje transakcje wsadowe w jednostkach, które znajdują się w tej samej tabeli, i należą do tej samej grupy partycji. Aby uzyskać więcej informacji, zobacz [wykonywanie transakcji grupy jednostek](/rest/api/storageservices/performing-entity-group-transactions).

## <a name="net-configuration"></a>Konfiguracja platformy .NET

W przypadku korzystania z .NET Framework Ta sekcja zawiera kilka ustawień szybkiego konfigurowania, których można użyć w celu zwiększenia wydajności.  Jeśli używasz innych języków, sprawdź, czy podobne koncepcje dotyczą wybranego języka.  

### <a name="use-net-core"></a>Korzystanie z platformy .NET Core

Utwórz aplikacje usługi Azure Storage za pomocą platformy .NET Core 2,1 lub nowszej, aby skorzystać z ulepszeń wydajności. Jeśli jest to możliwe, zalecamy korzystanie z programu .NET Core 3. x.

Aby uzyskać więcej informacji na temat ulepszeń wydajności w programie .NET Core, zobacz następujące wpisy w blogu:

- [Ulepszenia wydajności w programie .NET Core 3,0](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-3-0/)
- [Ulepszenia wydajności w programie .NET Core 2,1](https://devblogs.microsoft.com/dotnet/performance-improvements-in-net-core-2-1/)

### <a name="increase-default-connection-limit"></a>Zwiększ domyślny limit połączeń

W programie .NET Poniższy kod zwiększa domyślny limit połączeń (zwykle 2 w środowisku klienta lub 10 w środowisku serwera) do 100. Zazwyczaj należy ustawić wartość na około liczbę wątków używanych przez aplikację.  

```csharp
ServicePointManager.DefaultConnectionLimit = 100; //(Or More)  
```

Ustaw limit połączeń przed otwarciem wszystkich połączeń.  

W przypadku innych języków programowania zapoznaj się z dokumentacją tego języka, aby określić, jak ustawić limit połączeń.  

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

## <a name="configuration"></a>Konfigurowanie

Ta sekcja zawiera kilka ustawień szybkiego konfigurowania, których można użyć w celu zwiększenia wydajności Table service:

### <a name="use-json"></a>Używanie danych JSON

Począwszy od usługi Storage w wersji 2013-08-15, Table service obsługuje używanie kodu JSON zamiast formatu AtomPub opartego na języku XML do przesyłania danych tabeli. Użycie formatu JSON może zmniejszyć rozmiar ładunku o nawet do 75% i znacząco poprawić wydajność aplikacji.

Aby uzyskać więcej informacji, zobacz [tabele Microsoft Azure publikowania: wprowadzenie formatu JSON](/archive/blogs/windowsazurestorage/windows-azure-tables-introducing-json) i [ładunku dla operacji usługi Table Service](/rest/api/storageservices/Payload-Format-for-Table-Service-Operations).

### <a name="disable-nagle"></a>Wyłącz nagle

Algorytm nagle jest szeroko implementowany w sieciach TCP/IP jako środek w celu zwiększenia wydajności sieci. Nie jest to jednak optymalne we wszystkich sytuacjach (takich jak wysoce interaktywne środowiska). Algorytm nagle ma negatywny wpływ na wydajność żądań do Table service platformy Azure i należy go wyłączyć, jeśli jest to możliwe.

## <a name="schema"></a>Schemat

Sposób reprezentowania danych i wykonywania w nim zapytań jest największym czynnikiem wpływającym na wydajność Table service. Chociaż każda aplikacja jest różna, w tej sekcji opisano niektóre ogólne sprawdzone rozwiązania, które odnoszą się do:

- Projektowanie tabel
- Wydajne zapytania
- Wydajne aktualizacje danych

### <a name="tables-and-partitions"></a>Tabele i partycje

Tabele są podzielone na partycje. Każda jednostka przechowywana na partycji ma ten sam klucz partycji i ma unikatowy klucz wiersza, aby zidentyfikować ją w ramach tej partycji. Partycje zapewniają korzyści, ale również wprowadzają limity skalowalności.

- Zalety: można aktualizować jednostki w jednej partycji w pojedynczej, niepodzielnej transakcji wsadowej zawierającej do 100 oddzielnych operacji magazynowania (limit rozmiaru 4 MB). Przy założeniu, że ta sama liczba jednostek ma być pobierana, można również wykonywać zapytania dotyczące danych w pojedynczej partycji bardziej wydajniej niż dane, które obejmują partycje (należy przeczytać, aby uzyskać dalsze zalecenia dotyczące wykonywania zapytań dotyczących danych tabeli).
- Limit skalowalności: dostęp do jednostek przechowywanych w jednej partycji nie może być zrównoważony pod względem obciążenia, ponieważ partycje obsługują niepodzielną transakcję wsadową. Z tego powodu obiekt docelowy skalowalności dla pojedynczej partycji tabeli jest niższy niż dla Table service jako całości.

Ze względu na te cechy tabel i partycji należy przyjąć następujące zasady dotyczące projektowania:

- Zlokalizuj dane, które są często aktualizowane przez aplikację kliencką lub zapytania w tej samej jednostce logicznej pracy w tej samej partycji. Na przykład zlokalizuj dane w tej samej partycji, jeśli aplikacja agreguje zapisy lub wykonujesz niepodzielną operację wsadową. Ponadto dane w pojedynczej partycji mogą być bardziej wydajne w pojedynczej kwerendzie niż dane między partycjami.
- Znajdź dane, które aplikacja kliencka nie wstawia, nie aktualizuje ani nie bada zapytania w tej samej jednostce logicznej pracy (czyli w pojedynczym zapytaniu lub aktualizacji wsadowej) w osobnych partycjach. Należy pamiętać, że nie ma żadnego limitu liczby kluczy partycji w pojedynczej tabeli, tak aby miliony kluczy partycji nie było problemem i nie wpłyną na wydajność. Na przykład jeśli aplikacja jest popularną witryną sieci Web z logowaniem użytkownika, użycie identyfikatora użytkownika jako klucza partycji może być dobrym wyborem.

#### <a name="hot-partitions"></a>Partycje aktywne

Partycja gorąca to taka, która otrzymuje nieproporcjonalny odsetek ruchu do konta i nie może być zrównoważona pod względem obciążenia, ponieważ jest jedną partycją. Ogólnie rzecz biorąc, partycje gorące są tworzone jeden z dwóch sposobów:

#### <a name="append-only-and-prepend-only-patterns"></a>Tylko Dołącz wzorce i tylko poprzedź

Wzorzec "Append Only" to jeden, gdzie wszystkie (lub niemal wszystkie) ruchu do danego klucza partycji rosną i zmniejszają się w zależności od bieżącego czasu. Załóżmy na przykład, że aplikacja używa bieżącej daty jako klucza partycji dla danych dziennika. Ten projekt powoduje wszystkie operacje wstawiania do ostatniej partycji w tabeli, a system nie może prawidłowo zrównoważyć równoważenia obciążenia. Jeśli ilość ruchu do tej partycji przekracza miejsce docelowe skalowalności na poziomie partycji, spowoduje to ograniczenie przepustowości. Lepiej jest zapewnić, że ruch jest wysyłany do wielu partycji, aby umożliwić Równoważenie obciążenia żądaniami w tabeli.

#### <a name="high-traffic-data"></a>Dane o dużym natężeniu ruchu

Jeśli schemat partycjonowania składa się z pojedynczej partycji, która ma tylko dane, które są znacznie bardziej używane niż inne partycje, można także sprawdzić ograniczenia, ponieważ ta partycja zbliża się do celu skalowalności dla jednej partycji. Lepszym rozwiązaniem jest upewnienie się, że schemat partycji nie ma żadnej pojedynczej partycji, która zbliża się do celów skalowalności.

### <a name="querying"></a>Wykonywanie zapytania

W tej sekcji opisano sprawdzone praktyki dotyczące wykonywania zapytań dotyczących Table service.

#### <a name="query-scope"></a>Zakres zapytania

Istnieje kilka sposobów określania zakresu jednostek do zapytania. Na poniższej liście opisano każdą opcję zakresu zapytania.

- **Zapytania dotyczące punktów:**— zapytanie o punkt pobiera dokładnie jedną jednostkę przez określenie klucza partycji i klucza wiersza jednostki do pobrania. Te zapytania są wydajne i należy ich używać wszędzie tam, gdzie jest to możliwe.
- **Zapytania dotyczące partycji:** Zapytanie partycji to zapytanie, które pobiera zestaw danych, które współużytkują wspólny klucz partycji. Zwykle zapytanie określa zakres wartości klucza wiersza lub zakres wartości dla niektórych właściwości jednostki oprócz klucza partycji. Te zapytania są mniej wydajne niż zapytania punktowe i powinny być używane oszczędnie.
- **Zapytania tabeli:** Zapytanie tabeli to zapytanie, które pobiera zestaw jednostek, które nie korzystają ze wspólnego klucza partycji. Te zapytania są niewydajne i należy je unikać, jeśli jest to możliwe.

Ogólnie rzecz biorąc, unikaj skanowania (zapytania większe niż pojedyncza jednostka), ale jeśli musisz skanować, spróbuj zorganizować dane tak, aby skany pobierają potrzebne dane bez skanowania lub zwracania znaczących niepotrzebnych jednostek.

#### <a name="query-density"></a>Gęstość zapytania

Drugim czynnikiem wydajności zapytania jest liczba jednostek zwróconych w porównaniu do liczby przeskanowanych jednostek w celu znalezienia zwracanego zestawu. Jeśli aplikacja wykonuje zapytanie tabeli z filtrem dla wartości właściwości, która ma tylko 1% udziałów danych, zapytanie przeskanuje jednostki 100 dla każdej jednostki, która zwraca. Obiekty docelowe skalowalności tabeli omawiane poprzednio odnoszą się do liczby przeskanowanych jednostek, a nie liczby zwróconych jednostek: niska gęstość zapytań może łatwo spowodować, że Table service ograniczające działanie aplikacji, ponieważ musi ona skanować tak wiele jednostek, aby pobrać szukaną jednostkę. Aby uzyskać więcej informacji o tym, jak uniknąć ograniczania przepustowości, zapoznaj się z sekcją [denormalizacji](#denormalization).

#### <a name="limiting-the-amount-of-data-returned"></a>Ograniczanie ilości zwracanych danych

Gdy wiadomo, że zapytanie zwróci jednostki, które nie są potrzebne w aplikacji klienckiej, należy rozważyć użycie filtru, aby zmniejszyć rozmiar zwracanego zestawu. Mimo że jednostki, które nie są zwracane do klienta, są nadal wliczane do ograniczeń skalowalności, wydajność aplikacji poprawi się z powodu zmniejszonego rozmiaru ładunku sieciowego i zredukowanej liczby jednostek, które aplikacja kliencka musi przetworzyć. Należy pamiętać, że elementy docelowe skalowalności odnoszą się do liczby przeskanowanych jednostek, dlatego zapytanie filtrujące wiele jednostek może nadal powodować ograniczenia, nawet jeśli zwracane są kilka jednostek. Aby uzyskać więcej informacji na temat wydajnego tworzenia zapytań, zobacz sekcję [gęstość zapytań](#query-density).

Jeśli aplikacja kliencka wymaga tylko ograniczonego zestawu właściwości z jednostek w tabeli, można użyć projekcji, aby ograniczyć rozmiar zwracanego zestawu danych. Podobnie jak w przypadku filtrowania, projekcja pomaga zmniejszyć obciążenie sieci i przetwarzanie klienta.

#### <a name="denormalization"></a>Denormalizacja

W przeciwieństwie do pracy z relacyjnymi bazami danych, sprawdzone rozwiązania do wydajnego wykonywania zapytań o dane tabeli prowadzą do denormalizacji danych. Oznacza to, że duplikowanie tych samych danych w wielu jednostkach (po jednym dla każdego klucza, którego można użyć do znajdowania danych), aby zminimalizować liczbę jednostek, które muszą zostać przeskanowane przez zapytanie w celu znalezienia danych potrzebnych przez klienta, a nie konieczności skanowania dużej liczby jednostek w celu znalezienia danych potrzebnych przez aplikację. Na przykład w witrynie internetowej handlu elektronicznego możesz chcieć znaleźć zamówienie zarówno według identyfikatora klienta, jak i daty (podać zamówienia tego klienta), a w dniu W Table Storage najlepiej przechowywać jednostkę (lub odwołanie do niej) dwa razy — raz z nazwą tabeli, PK i RK, aby ułatwić znajdowanie według identyfikatora klienta, co ułatwia znalezienie go w dniu.  

### <a name="insert-update-and-delete"></a>Wstawianie, aktualizowanie i usuwanie

W tej sekcji opisano sprawdzone praktyki modyfikowania jednostek przechowywanych w Table service.  

#### <a name="batching"></a>Przetwarzanie wsadowe

Transakcje usługi Batch są nazywane transakcjami grupy jednostek w usłudze Azure Storage. Wszystkie operacje w ramach transakcji grupy jednostek muszą znajdować się na jednej partycji w pojedynczej tabeli. Jeśli to możliwe, użyj transakcji grupy jednostek do wykonywania operacji wstawiania, aktualizacji i usuwania w partiach. Użycie transakcji grupy jednostek zmniejsza liczbę podróży z aplikacji klienckiej do serwera, zmniejsza liczbę transakcji rozliczanych (liczba transakcji grupy jednostek jako pojedynczą transakcję na potrzeby rozliczania i może zawierać do 100 operacji magazynu) i włącza aktualizacje niepodzielne (wszystkie operacje kończą się powodzeniem lub wszystkie w ramach transakcji grupy jednostek). Środowiska z dużymi opóźnieniami, takimi jak urządzenia przenośne, będą znacznie korzystać z transakcji grupy jednostek.  

#### <a name="upsert"></a>Upsert

W miarę możliwości używaj tabeli **upsert** . Istnieją dwa typy **upsert**, które mogą być bardziej wydajne niż tradycyjne operacje **wstawiania** i **aktualizacji** :  

- **InsertOrMerge**: Użyj tej operacji, gdy chcesz przekazać podzestaw właściwości jednostki, ale nie ma pewności, czy jednostka już istnieje. Jeśli jednostka istnieje, to wywołanie aktualizuje właściwości zawarte w operacji **upsert** i pozostawia wszystkie istniejące właściwości, jeśli są one, jeśli jednostka nie istnieje, wstawia nową jednostkę. Jest to podobne do użycia projekcji w zapytaniu, w którym należy jedynie przekazać właściwości, które są zmieniane.
- **InsertOrReplace**: Użyj tej operacji, jeśli chcesz przekazać całkowicie nową jednostkę, ale nie masz pewności, czy już istnieje. Użyj tej operacji, Jeśli wiesz, że nowo przekazana jednostka jest całkowicie poprawna, ponieważ całkowicie zastępuje starą jednostkę. Na przykład, chcesz zaktualizować jednostkę, która przechowuje bieżącą lokalizację użytkownika, niezależnie od tego, czy aplikacja ma poprzednio przechowywane dane lokalizacji dla użytkownika; Nowa jednostka lokalizacji została ukończona i nie są potrzebne żadne informacje z poprzedniej jednostki.

#### <a name="storing-data-series-in-a-single-entity"></a>Przechowywanie serii danych w jednej jednostce

Czasami aplikacja przechowuje serie danych, które często mają być pobierane jednocześnie: na przykład aplikacja może śledzić użycie procesora w czasie w celu wykreślania przenoszonego wykresu danych z ostatnich 24 godzin. Jednym z metod jest posiadanie jednej jednostki tabeli na godzinę, dla każdej jednostki reprezentującej określoną godzinę i przechowywanie użycia procesora CPU przez daną godzinę. Aby wykreślić te dane, aplikacja musi pobrać jednostki przechowujące dane z 24 ostatnich godzin.  

Alternatywnie, aplikacja może przechowywać użycie procesora CPU przez każdą godzinę jako oddzielną Właściwość pojedynczej jednostki: Aby zaktualizować każdą godzinę, aplikacja może użyć pojedynczego wywołania **InsertOrMerge upsert** , aby zaktualizować wartość dla ostatniej godziny. Aby wykreślić dane, aplikacja musi pobrać tylko jedną jednostkę zamiast 24, co pozwala na wydajne wykonywanie zapytań. Aby uzyskać więcej informacji o wydajności zapytań, zobacz sekcję zatytułowaną [zakres zapytania](#query-scope)).

#### <a name="storing-structured-data-in-blobs"></a>Przechowywanie danych strukturalnych w obiektach Blob

Jeśli wykonujesz operacje wstawiania wsadowego, a następnie pobierasz zakresy jednostek, rozważ użycie obiektów BLOB zamiast tabel. Dobrym przykładem jest plik dziennika. Można wykonać wsadowe kilka minut dzienników i wstawić je, a następnie pobrać kilka minut dzienników jednocześnie. W takim przypadku wydajność jest lepsza, jeśli używasz obiektów BLOB zamiast tabel, ponieważ można znacząco ograniczyć liczbę obiektów zapisaną lub odczytaną, a także liczbę żądań, które wymagają.  

## <a name="next-steps"></a>Następne kroki

- [Cele skalowalności i wydajności dla usługi Table Storage](scalability-targets.md)
- [Cele skalowalności i wydajności dla kont magazynu w warstwie Standardowa](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)
- [Kody stanu i błędów](/rest/api/storageservices/Status-and-Error-Codes2)