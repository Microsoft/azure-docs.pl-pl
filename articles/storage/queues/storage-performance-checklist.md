---
title: Lista kontrolna wydajności i skalowalności dla Queue Storage — Azure Storage
description: Lista kontrolna sprawdzonych praktyk do użycia z Queue Storage tworzenia aplikacji o wysokiej wydajności.
author: tamram
services: storage
ms.author: tamram
ms.date: 10/10/2019
ms.topic: overview
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-csharp
ms.openlocfilehash: 4040a81d5b509ddbdd355953e28721a7c9fccfb8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97585670"
---
<!-- docutune:casing "Timeout and Server Busy errors" -->

# <a name="performance-and-scalability-checklist-for-queue-storage"></a>Lista kontrolna wydajności i skalowalności dla Queue Storage

Firma Microsoft opracowała kilka sprawdzonych praktyk tworzenia aplikacji o wysokiej wydajności z Queue Storage. Ta lista kontrolna zawiera najważniejsze rozwiązania, które deweloperzy mogą wykonać w celu zoptymalizowania wydajności. Należy pamiętać o tych praktykach podczas projektowania aplikacji i w trakcie całego procesu.

Usługa Azure Storage oferuje cele skalowalności i wydajności dla pojemności, szybkości transakcji i przepustowości. Aby uzyskać więcej informacji o obiektach docelowych skalowalności usługi Azure Storage, zobacz [cele skalowalności i wydajności dla standardowych kont magazynu](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) i [skalowalności i wydajności dla queue storage](scalability-targets.md).

## <a name="checklist"></a>Lista kontrolna

Ten artykuł organizuje sprawdzone rozwiązania dotyczące wydajności w ramach listy kontrolnej, którą można wykonać podczas tworzenia aplikacji Queue Storage.

| Gotowe | Kategoria | Zagadnienie dotyczące projektowania |
|--|--|--|
| &nbsp; | Tarcze skalowalności | [Czy można zaprojektować aplikację tak, aby korzystała z nie więcej niż maksymalna liczba kont magazynu?](#maximum-number-of-storage-accounts) |
| &nbsp; | Tarcze skalowalności | [Czy unikasz zbliżania się limitów pojemności i transakcji?](#capacity-and-transaction-targets) |
| &nbsp; | Sieć | [Czy urządzenia po stronie klienta mają dostatecznie wysoką przepustowość i małe opóźnienia w celu osiągnięcia wymaganej wydajności?](#throughput) |
| &nbsp; | Sieć | [Czy urządzenia po stronie klienta mają link do sieci o wysokiej jakości?](#link-quality) |
| &nbsp; | Sieć | [Czy aplikacja kliencka znajduje się w tym samym regionie co konto magazynu?](#location) |
| &nbsp; | Bezpośredni dostęp klienta | [Czy używasz sygnatur dostępu współdzielonego (SAS) i udostępniania zasobów między źródłami (CORS), aby umożliwić bezpośredni dostęp do usługi Azure Storage?](#sas-and-cors) |
| &nbsp; | Konfiguracja platformy .NET | [Czy używasz platformy .NET Core 2,1 lub nowszej w celu uzyskania optymalnej wydajności?](#use-net-core) |
| &nbsp; | Konfiguracja platformy .NET | [Czy skonfigurowano klienta tak, aby używał wystarczającej liczby jednoczesnych połączeń?](#increase-default-connection-limit) |
| &nbsp; | Konfiguracja platformy .NET | [Czy w przypadku aplikacji .NET skonfigurowano platformę .NET do używania wystarczającej liczby wątków?](#increase-the-minimum-number-of-threads) |
| &nbsp; | Równoległości | [Czy istnieje pewność, że równoległość jest odpowiednio ograniczona, aby nie można było przeciążać możliwości klienta ani podejścia do celów skalowalności?](#unbounded-parallelism) |
| &nbsp; | Narzędzia | [Czy używasz najnowszych wersji bibliotek i narzędzi klienta dostarczonych przez firmę Microsoft?](#client-libraries-and-tools) |
| &nbsp; | Ponowne próby | [Czy zasady ponawiania są używane z wykładniczą wycofywaniaą do ograniczania błędów i przekroczeń limitu czasu?](#timeout-and-server-busy-errors) |
| &nbsp; | Ponowne próby | [Czy aplikacja unika ponawiania prób w przypadku błędów, które nie są ponawiane?](#non-retryable-errors) |
| &nbsp; | Konfigurowanie | [Czy wyłączono algorytm nagle, aby zwiększyć wydajność małych żądań?](#disable-nagles-algorithm) |
| &nbsp; | Rozmiar komunikatu | [Czy komunikaty są kompaktowe, aby zwiększyć wydajność kolejki?](#message-size) |
| &nbsp; | Pobieranie zbiorcze | [Czy pobierasz wiele komunikatów w ramach jednej operacji get?](#batch-retrieval) |
| &nbsp; | Częstotliwość sondowania | [Czy trwa sondowanie na tyle często, aby zmniejszyć postrzegane opóźnienie aplikacji?](#queue-polling-interval) |
| &nbsp; | Komunikat aktualizacji | [Czy wykonywana jest operacja aktualizacji komunikatu w celu przechowywania postępu w przetwarzaniu komunikatów, dzięki czemu można uniknąć konieczności ponownego przetwarzania całego komunikatu w przypadku wystąpienia błędu?](#perform-an-update-message-operation) |
| &nbsp; | Architektura | [Czy używasz kolejek, aby zwiększyć skalowalność całej aplikacji przez utrzymywanie długotrwałych obciążeń z ścieżki krytycznej i skalowalność niezależnie?](#application-architecture) |

## <a name="scalability-targets"></a>Tarcze skalowalności

Jeśli aplikacja zbliża się lub przekroczy elementy docelowe skalowalności, może wystąpić zwiększenie opóźnień transakcji lub ograniczenie przepustowości. Gdy usługa Azure Storage ogranicza swoją aplikację, rozpocznie się zwracanie 503 ( `Server Busy` ) lub 500 ( `Operation Timeout` ) kodów błędów. Unikanie tych błędów przez Przekroczenie limitów celów skalowalności jest ważną częścią zwiększania wydajności aplikacji.

Aby uzyskać więcej informacji dotyczących skalowalności Queue Storage, zobacz [cele dotyczące skalowalności i wydajności usługi Azure Storage](./scalability-targets.md#scale-targets-for-queue-storage).

### <a name="maximum-number-of-storage-accounts"></a>Maksymalna liczba kont magazynu

Jeśli zbliżasz się do maksymalnej liczby kont magazynu dozwolonych dla danej kombinacji subskrypcji/regionu, używasz wielu kont magazynu do fragmentu w celu zwiększenia ruchu przychodzącego, wychodzącego, operacji we/wy na sekundę (IOPS) lub pojemności? W tym scenariuszu firma Microsoft zaleca użycie zwiększonych limitów dla kont magazynu w celu zmniejszenia liczby kont magazynu wymaganych do obciążenia, jeśli jest to możliwe. Skontaktuj się z [pomocą techniczną platformy Azure](https://azure.microsoft.com/support/options/) , aby zażądać zwiększonych limitów dla konta magazynu. Aby uzyskać więcej informacji, zobacz temat [ogłaszanie większych kont magazynu](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)o większej skali.

### <a name="capacity-and-transaction-targets"></a>Pojemność i cele transakcji

Jeśli aplikacja zbliża się do celów skalowalności dla jednego konta magazynu, należy rozważyć przyjęcie jednej z następujących metod:

- Jeśli elementy docelowe skalowalności dla kolejek są niewystarczające dla aplikacji, Użyj wielu kolejek i Dystrybuuj wiadomości między nimi.
- Zapoznaj się z obciążeniem, które powoduje, że aplikacja może obsłużyć lub przekroczyć obiekt docelowy skalowalności. Czy można projektować inaczej, aby używać mniejszej przepustowości lub pojemności lub mniejszej liczby transakcji?
- Jeśli aplikacja musi przekroczyć jeden z celów skalowalności, należy utworzyć wiele kont magazynu i podzielić na partycje dane aplikacji na te wiele kont magazynu. Jeśli używasz tego wzorca, pamiętaj, aby zaprojektować aplikację tak, aby w przyszłości można było dodać więcej kont magazynu do równoważenia obciążenia. Same konta magazynu nie mają kosztu innego niż użycie w odniesieniu do danych przechowywanych, wykonanych transakcji lub przesłanych danych.
- Jeśli aplikacja zbliża się do docelowych przepustowości, rozważ skompresowanie danych po stronie klienta, aby zmniejszyć przepustowość wymaganą do wysłania danych do usługi Azure Storage. Chociaż kompresowanie danych może zaoszczędzić przepustowość i zwiększyć wydajność sieci, może mieć także negatywny wpływ na wydajność. Oceń wpływ dodatkowych wymagań związanych z przetwarzaniem na kompresję danych i dekompresję po stronie klienta. Należy pamiętać, że przechowywanie skompresowanych danych może utrudnić rozwiązywanie problemów, ponieważ może być trudniejsze do wyświetlania danych przy użyciu standardowych narzędzi.
- Jeśli aplikacja zbliża się do elementów docelowych skalowalności, upewnij się, że używasz wykładniczej wycofywania do ponawiania prób. Najlepszym rozwiązaniem jest uniknięcie osiągnięcia celów skalowalności przez implementację zaleceń opisanych w tym artykule. Jednak użycie wykładniczej wycofywania na potrzeby ponownych prób uniemożliwi szybkiej próby aplikacji, co może spowodować, że ograniczanie wydajności będzie gorsze. Aby uzyskać więcej informacji, zobacz sekcję [limity czasu i błędy zajętości serwera](#timeout-and-server-busy-errors) .

## <a name="networking"></a>Sieć

Ograniczenia sieci fizycznej aplikacji mogą mieć znaczący wpływ na wydajność. W poniższych sekcjach opisano niektóre ograniczenia, które mogą napotkać użytkownicy.

### <a name="client-network-capability"></a>Możliwość sieci klienta

Przepustowość i jakość łącza sieciowego odgrywają ważne role w działaniu aplikacji, zgodnie z opisem w poniższych sekcjach.

#### <a name="throughput"></a>Przepływność

W przypadku przepustowości problem jest często możliwością klienta programu. Większe wystąpienia platformy Azure mają karty sieciowe o większej pojemności, dlatego należy rozważyć użycie większego wystąpienia lub większej liczby maszyn wirtualnych, jeśli potrzebne są wyższe limity sieci z jednej maszyny. Jeśli uzyskujesz dostęp do usługi Azure Storage z aplikacji lokalnej, ta sama reguła ma zastosowanie: omówienie możliwości sieciowych urządzenia klienckiego i połączenia sieciowego z lokalizacją usługi Azure Storage, a następnie popraw je w razie potrzeby lub Zaprojektuj aplikację do pracy w ramach swoich możliwości.

#### <a name="link-quality"></a>Jakość łącza

Podobnie jak w przypadku dowolnego użycia sieci należy pamiętać, że warunki sieci powodujące błędy i utrata pakietów spowodują spowolnienie przepływności. Korzystanie z programu Wireshark lub Monitor sieci może pomóc w zdiagnozowaniu tego problemu.

### <a name="location"></a>Lokalizacja

W każdym środowisku rozproszonym, umieszczenie klienta w sąsiedztwie z serwerem zapewnia najlepszą wydajność. Aby uzyskać dostęp do usługi Azure Storage z najniższym opóźnieniem, Najlepsza lokalizacja klienta jest w tym samym regionie świadczenia usługi Azure. Na przykład jeśli masz aplikację sieci Web platformy Azure, która korzysta z usługi Azure Storage, zlokalizuj je zarówno w jednym regionie, jak zachodnie stany USA czy Azja Południowo-Wschodnia. Kolokacja zasobów zmniejsza czas oczekiwania i koszt, ponieważ użycie przepustowości w jednym regionie jest bezpłatne.

Jeśli aplikacje klienckie będą uzyskiwać dostęp do usługi Azure Storage, ale nie są hostowane na platformie Azure, np. w przypadku aplikacji urządzeń przenośnych lub lokalnych usług przedsiębiorstwa, lokalizowanie konta magazynu w regionie blisko tych klientów może skrócić czas oczekiwania. Jeśli klienci są rozległie dystrybuowani (na przykład niektóre w Ameryka Północna, a niektóre w Europie), należy rozważyć użycie jednego konta magazynu dla każdego regionu. To podejście jest łatwiejsze do wdrożenia, jeśli dane przechowywane przez aplikacje są specyficzne dla poszczególnych użytkowników i nie wymagają replikowania danych między kontami magazynu.

## <a name="sas-and-cors"></a>SAS i CORS

Załóżmy, że musisz autoryzować kod, taki jak JavaScript, który jest uruchomiony w przeglądarce sieci Web użytkownika lub w aplikacji mobilnej dla telefonu, aby uzyskać dostęp do danych w usłudze Azure Storage. Jednym z rozwiązań jest skompilowanie aplikacji usługi, która działa jako serwer proxy. Urządzenie użytkownika uwierzytelnia się za pomocą usługi, która z kolei autoryzuje dostęp do zasobów usługi Azure Storage. W ten sposób można uniknąć ujawniania kluczy konta magazynu na niezabezpieczonych urządzeniach. Jednak takie podejście powoduje znaczne obciążenie aplikacji usługi, ponieważ wszystkie dane przesyłane między urządzeniem użytkownika a usługą Azure Storage muszą przechodzić przez aplikację usługi.

Za pomocą sygnatur dostępu współdzielonego można uniknąć używania aplikacji usługi jako serwera proxy dla usługi Azure Storage. Za pomocą SYGNATURy dostępu współdzielonego można umożliwić urządzeniu użytkownika wykonywanie żądań bezpośrednio do usługi Azure Storage przy użyciu ograniczonego tokenu. Na przykład jeśli użytkownik chce przekazać zdjęcie do aplikacji, aplikacja usługi może wygenerować sygnaturę dostępu współdzielonego i wysłać ją do urządzenia użytkownika. Token sygnatury dostępu współdzielonego może udzielić uprawnienia do zapisu w zasobie usługi Azure Storage przez określony przedział czasu, po upływie którego wygasa token sygnatury dostępu współdzielonego. Aby uzyskać więcej informacji na temat SAS, zobacz [udzielanie ograniczonego dostępu do zasobów usługi Azure Storage za pomocą sygnatur dostępu współdzielonego (SAS)](../common/storage-sas-overview.md).

Zwykle przeglądarka sieci Web nie zezwala na używanie języka JavaScript na stronie hostowanej przez witrynę sieci Web w jednej domenie w celu wykonywania niektórych operacji, takich jak operacje zapisu, do innej domeny. Te zasady są nazywane zasadami tego samego źródła, co uniemożliwia złośliwemu skryptowi na jednej stronie uzyskanie dostępu do danych na innej stronie sieci Web. Jednak zasady tego samego źródła mogą stanowić ograniczenie podczas kompilowania rozwiązania w chmurze. Udostępnianie zasobów między źródłami (CORS) to funkcja przeglądarki, która umożliwia domenie docelowej komunikowanie się z przeglądarką, która ufa żądaniami pochodzącymi z domeny źródłowej.

Załóżmy na przykład, że aplikacja sieci Web działająca na platformie Azure wysyła żądanie do konta usługi Azure Storage. Aplikacja sieci Web jest domeną źródłową, a konto magazynu jest domeną docelową. Można skonfigurować mechanizm CORS dla dowolnej usługi Azure Storage, aby komunikować się z przeglądarką sieci Web, którą żądania z domeny źródłowej są zaufane przez usługę Azure Storage. Aby uzyskać więcej informacji na temat mechanizmu CORS, zobacz [Obsługa zasobów między źródłami (CORS) dla usługi Azure Storage](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).

Zarówno sygnatury dostępu współdzielonego, jak i CORS mogą pomóc uniknąć niepotrzebnego obciążenia aplikacji sieci Web.

## <a name="net-configuration"></a>Konfiguracja platformy .NET

W przypadku korzystania z .NET Framework Ta sekcja zawiera kilka ustawień szybkiego konfigurowania, których można użyć w celu zwiększenia wydajności. Jeśli używasz innych języków, sprawdź, czy podobne koncepcje dotyczą wybranego języka.

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

### <a name="increase-the-minimum-number-of-threads"></a>Zwiększ minimalną liczbę wątków

Jeśli używasz wywołań synchronicznych razem z zadaniami asynchronicznymi, możesz chcieć zwiększyć liczbę wątków w puli wątków:

```csharp
ThreadPool.SetMinThreads(100,100); //(Determine the right number for your application)  
```

Aby uzyskać więcej informacji, zobacz [`ThreadPool.SetMinThreads`](/dotnet/api/system.threading.threadpool.setminthreads) metodę.

## <a name="unbounded-parallelism"></a>Nieograniczona równoległość

Chociaż równoległość może być świetna dla wydajności, należy zachować ostrożność przy użyciu nieograniczonej równoległości, co oznacza, że nie ma żadnego ograniczenia dotyczącego liczby wątków lub żądań równoległych. Należy pamiętać, aby ograniczyć liczbę żądań równoległych do przekazywania lub pobierania danych, uzyskać dostęp do wielu partycji na tym samym koncie magazynu lub uzyskać dostęp do wielu elementów w tej samej partycji. Jeśli równoległość jest nieograniczona, aplikacja może przekroczyć możliwości urządzenia klienckiego lub skalowalności konta magazynu, co powoduje dłuższe opóźnienia i ograniczanie przepustowości.

## <a name="client-libraries-and-tools"></a>Biblioteki i narzędzia klienta

Aby uzyskać najlepszą wydajność, zawsze używaj najnowszych bibliotek i narzędzi klienta dostarczonych przez firmę Microsoft. Biblioteki klienckie usługi Azure Storage są dostępne dla różnych języków. Usługa Azure Storage obsługuje również program PowerShell i interfejs wiersza polecenia platformy Azure. Firma Microsoft aktywnie opracowuje te biblioteki i narzędzia klienckie z uwzględnieniem wydajności, a następnie zapewnia ich aktualność przy użyciu najnowszych wersji usługi i gwarantuje, że obsługa wielu sprawdzonych rozwiązań dotyczących wydajności jest wewnętrznie. Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą usługi Azure Storage](./reference.md).

## <a name="handle-service-errors"></a>Obsługa błędów usługi

Usługa Azure Storage zwraca błąd, jeśli nie można przetworzyć żądania przez usługę. Informacje o błędach, które mogą zostać zwrócone przez usługę Azure Storage w danym scenariuszu, są przydatne w celu optymalizacji wydajności.

### <a name="timeout-and-server-busy-errors"></a>Błędy i czas zajętości serwera

Usługa Azure Storage może ograniczać swoją aplikację, jeśli zbliża się ona do ograniczeń skalowalności. W niektórych przypadkach usługa Azure Storage może nie być w stanie obsłużyć żądania ze względu na przejściowy warunek. W obu przypadkach usługa może zwrócić błąd 503 ( `Server Busy` ) lub 500 ( `Timeout` ). Te błędy mogą również wystąpić, jeśli usługa przestawia partycje danych w celu zapewnienia większej przepływności. Aplikacja kliencka powinna zwykle ponowić próbę wykonania operacji, która powoduje wystąpienie jednego z tych błędów. Jeśli jednak usługa Azure Storage ogranicza swoją aplikację, ponieważ przekracza ona elementy docelowe skalowalności, a nawet jeśli nie jest w stanie obsłużyć żądania z innego powodu, agresywne ponawianie prób może spowodować, że problem będzie gorszy. Zalecane jest użycie wykładniczej zasady ponawiania prób, a biblioteki klienckie domyślnie to zachowanie. Na przykład aplikacja może ponowić próbę po upływie 2 sekund, następnie 4 sekund, następnie 10 sekund, a następnie 30 sekundach, a następnie zadawać całkowicie. W ten sposób aplikacja znacznie zmniejsza obciążenie usługi, a nie zachowanie, które może prowadzić do ograniczenia.

Błędy łączności mogą być podejmowane natychmiast, ponieważ nie są one wynikiem ograniczenia przepustowości i powinny być przejściowe.

### <a name="non-retryable-errors"></a>Błędy nieponowień

Biblioteki klienta obsługują ponawianie prób z świadomością, które błędy mogą być ponawiane i które nie mogą. Jeśli jednak wywołujesz interfejs API REST usługi Azure Storage bezpośrednio, istnieją pewne błędy, które nie powinny być ponawiane. Na przykład błąd 400 ( `Bad Request` ) wskazuje, że aplikacja kliencka wysłała żądanie, którego nie można było przetworzyć, ponieważ nie ma oczekiwanego formularza. Ponowne wysłanie tego żądania spowoduje takie samo odpowiedzi, więc nie ma żadnego punktu na ponawianie próby. Jeśli wywołujesz interfejs API REST usługi Azure Storage bezpośrednio, weź pod uwagę potencjalne błędy i czy należy ponowić próbę.

Aby uzyskać więcej informacji na temat kodów błędów usługi Azure Storage, zobacz informacje o [stanie i kodach błędów](/rest/api/storageservices/status-and-error-codes2).

## <a name="disable-nagles-algorithm"></a>Wyłącz algorytm nagle

Algorytm nagle jest szeroko implementowany w sieciach TCP/IP jako środek w celu zwiększenia wydajności sieci. Nie jest to jednak optymalne we wszystkich sytuacjach (takich jak wysoce interaktywne środowiska). Algorytm nagle ma negatywny wpływ na wydajność żądań na platformie Azure Table Storage i należy go wyłączyć, jeśli jest to możliwe.

## <a name="message-size"></a>Rozmiar komunikatu

Wydajność i skalowalność kolejki zmniejsza się w miarę wzrostu rozmiaru wiadomości. W komunikacie należy umieścić tylko te informacje, których potrzebuje odbiornik.

## <a name="batch-retrieval"></a>Pobieranie wsadowe

Można pobrać maksymalnie 32 komunikatów z kolejki w ramach jednej operacji. Pobieranie wsadowe może zmniejszyć liczbę rejsów z aplikacji klienckiej, która jest szczególnie przydatna w środowiskach, takich jak urządzenia przenośne, z dużym opóźnieniem.

## <a name="queue-polling-interval"></a>Interwał sondowania kolejki

Większość aplikacji sonduje komunikaty z kolejki, co może być jednym z największych źródeł transakcji dla danej aplikacji. Wielokrotnie wybieraj interwał sondowania: sondowanie zbyt często może spowodować, że aplikacja zbliża się do celów skalowalności dla kolejki. Jednak w przypadku 200 000 transakcji dla $0,01 (w momencie pisania) pojedynczy procesor sondowanie co sekundę przez miesiąc ma koszt mniejszy niż 15 centów, dzięki czemu koszt nie jest zwykle czynnikiem wpływającym na wybór interwału sondowania.

Aby uzyskać aktualne informacje o kosztach, zobacz [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

## <a name="perform-an-update-message-operation"></a>Wykonaj operację aktualizacji komunikatu

Można wykonać operację aktualizacji komunikatu, aby zwiększyć limit czasu niewidoczności lub zaktualizować informacje o stanie komunikatu. Takie podejście może być wydajniejsze niż posiadanie przepływu pracy, który przekazuje zadanie z jednej kolejki do następnego, ponieważ każdy krok zadania zostanie ukończony. Aplikacja może zapisać stan zadania w komunikacie, a następnie kontynuować pracę, zamiast ponownie kolejkować komunikat dla kolejnego kroku zadania za każdym razem, gdy krok zostanie ukończony. Należy pamiętać, że każda operacja aktualizacji komunikatów jest liczona względem celu skalowalności.

## <a name="application-architecture"></a>Architektura aplikacji

Użyj kolejek, aby zapewnić skalowalność architektury aplikacji. Poniżej wymieniono niektóre sposoby używania kolejek w celu zapewnienia bardziej skalowalności aplikacji:

- Za pomocą kolejek można tworzyć zaległości służbowe do przetwarzania i wygładzania obciążeń w aplikacji. Na przykład można kolejkować żądania od użytkowników w celu wykonywania dużej ilości pracy procesora, takich jak zmienianie rozmiarów przekazanych obrazów.
- Za pomocą kolejek można rozdzielić części aplikacji, aby umożliwić ich niezależne skalowanie. Na przykład fronton sieci Web może umieścić wyniki ankiety od użytkowników w kolejce na potrzeby późniejszej analizy i magazynu. Można dodać więcej wystąpień roli procesu roboczego, aby przetworzyć dane kolejki zgodnie z wymaganiami.

## <a name="next-steps"></a>Następne kroki

- [Elementy docelowe skalowalności i wydajności dla Queue Storage](scalability-targets.md)
- [Cele skalowalności i wydajności dla kont magazynu w warstwie Standardowa](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)
- [Kody stanu i błędów](/rest/api/storageservices/Status-and-Error-Codes2)
