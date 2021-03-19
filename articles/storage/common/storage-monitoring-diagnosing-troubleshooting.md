---
title: Monitorowanie, diagnozowanie i rozwiązywanie problemów z usługą Azure Storage | Microsoft Docs
description: Korzystaj z funkcji, takich jak analiza magazynu, rejestrowanie po stronie klienta i inne narzędzia innych firm, aby identyfikować, diagnozować i rozwiązywać problemy związane z usługą Azure Storage.
author: normesta
ms.service: storage
ms.topic: troubleshooting
ms.date: 10/08/2020
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.custom: monitoring, devx-track-csharp
ms.openlocfilehash: 5943a65d61dee4fcf610b8cc45139674362995cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104600721"
---
# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>Monitorowanie, diagnozowanie i rozwiązywanie problemów z usługą Microsoft Azure Storage
[!INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>Omówienie
Diagnozowanie i rozwiązywanie problemów w aplikacji rozproszonej hostowanej w środowisku chmury może być bardziej skomplikowane niż w tradycyjnych środowiskach. Aplikacje można wdrażać w infrastrukturze PaaS lub IaaS, lokalnie, na urządzeniu przenośnym lub w niektórych kombinacjach tych środowisk. Zwykle ruch sieciowy aplikacji może przechodzić między sieciami publicznymi i prywatnymi, a aplikacja może korzystać z wielu technologii magazynowania, takich jak tabele Microsoft Azure Storage, obiekty blob, kolejki lub pliki, a także do innych magazynów danych, takich jak relacyjne bazy danych i dokumenty.

Aby zarządzać takimi aplikacjami, należy monitorować je aktywnie i zrozumieć, jak diagnozować i rozwiązywać problemy ze wszystkimi aspektami i technologiami zależnymi. Jako użytkownik usługi Azure Storage należy stale monitorować usługi magazynu używane przez aplikację w przypadku wszelkich nieoczekiwanych zmian w zachowaniu (na przykład wolniejszych od zwykłych czasów odpowiedzi), a także używać funkcji rejestrowania do zbierania bardziej szczegółowych danych i analizowania problemów. Informacje diagnostyczne uzyskane z monitorowania i rejestrowania ułatwią ustalenie głównej przyczyny problemu występującego przez aplikację. Następnie można rozwiązać problem i określić odpowiednie kroki, które należy wykonać w celu rozwiązania tego problemu. Azure Storage to podstawowa usługa platformy Azure, która stanowi ważną część większości rozwiązań wdrażanych przez klientów w infrastrukturze platformy Azure. Usługa Azure Storage obejmuje funkcje upraszczające monitorowanie, diagnozowanie i rozwiązywanie problemów z magazynem w aplikacjach opartych na chmurze.

* [Wprowadzenie]
  * [Jak zorganizowany jest ten przewodnik]
* [Monitorowanie usługi magazynu]
  * [Kondycja usługi monitorowania]
  * [Monitorowanie wydajności]
  * [Monitorowanie dostępności]
  * [Monitorowanie wydajności]
* [Diagnozowanie problemów z magazynem]
  * [Problemy z kondycją usługi]
  * [Problemy z wydajnością]
  * [Diagnozowanie błędów]
  * [Problemy emulatora magazynu]
  * [Narzędzia rejestrowania magazynu]
  * [Korzystanie z narzędzi do rejestrowania w sieci]
* [Kompleksowe śledzenie]
  * [Skorelowanie danych dziennika]
  * [Identyfikator żądania klienta]
  * [Identyfikator żądania serwera]
  * [Znaczniki czasu]
* [Wskazówki dotyczące rozwiązywania problemów]
  * [Metryki wskazują wysoką wartość AverageE2ELatency i niską wartość AverageServerLatency]
  * [Metryki wskazują niską wartość AverageE2ELatency i niską wartość AverageServerLatency, ale na kliencie występuje duże opóźnienie]
  * [Metryki wskazują wysoką wartość AverageServerLatency]
  * [Występują nieoczekiwane opóźnienia w dostarczaniu komunikatów w kolejce]
  * [Metryki wskazują wzrost wartości PercentThrottlingError]
  * [Metryki wskazują wzrost wartości PercentTimeoutError]
  * [Metryki wskazują wzrost wartości PercentNetworkError]
  * [Klient odbiera komunikaty HTTP 403 (zabronione)]
  * [Klient odbiera komunikaty HTTP 404 (nie znaleziono)]
  * [Klient odbiera komunikaty HTTP 409 (konflikt)]
  * [Metryki pokazujące niskie PercentSuccess lub wpisy dziennika analizy zawierają operacje ze stanem transakcji ClientOtherErrors]
  * [Metryki pojemności pokazują nieoczekiwany wzrost użycia pojemności magazynu]
  * [Problem wynika z używania emulatora magazynu na potrzeby tworzenia i testowania]
  * [Występują problemy z instalacją zestawu Azure SDK dla platformy .NET]
  * [Istnieje inny problem dotyczący usługi magazynu]
  * [Rozwiązywanie problemów dotyczących dysków VHD na maszynach wirtualnych z systemem Windows](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)   
  * [Rozwiązywanie problemów dotyczących dysków VHD na maszynach wirtualnych z systemem Linux](/troubleshoot/azure/virtual-machines/welcome-virtual-machines)
  * [Rozwiązywanie problemów z Azure Filesmi w systemie Windows](../files/storage-troubleshoot-windows-file-connection-problems.md)   
  * [Rozwiązywanie problemów z Azure Filesymi przy użyciu systemu Linux](../files/storage-troubleshoot-linux-file-connection-problems.md)
* [Dodatki]
  * [Dodatek 1: używanie programu Fiddler do przechwytywania ruchu HTTP i HTTPS]
  * [Dodatek 2. Korzystanie z programu Wireshark do przechwytywania ruchu sieciowego]
  * [Dodatek 4: używanie programu Excel do wyświetlania metryk i danych dzienników]
  * [Dodatek 5: monitorowanie za pomocą Application Insights platformy Azure DevOps]

## <a name="introduction"></a><a name="introduction"></a>Wprowadzenie
W tym przewodniku pokazano, jak za pomocą funkcji, takich jak Azure Storage Analytics, rejestrowanie po stronie klienta w bibliotece klienta usługi Azure Storage oraz inne narzędzia innych firm do identyfikowania, diagnozowania i rozwiązywania problemów związanych z usługą Azure Storage.

![Diagram przedstawiający przepływ informacji między aplikacjami klienckimi i usługami Azure Storage.][1]

Ten przewodnik jest przeznaczony głównie dla deweloperów Usługi online korzystających z usług Azure Storage i informatyków odpowiedzialnych za zarządzanie takimi Usługi online. Celem tego przewodnika są:

* Aby ułatwić Ci zachowanie kondycji i wydajności kont usługi Azure Storage.
* Aby zapewnić niezbędne procesy i narzędzia ułatwiające podjęcie decyzji o tym, czy problem lub problem w aplikacji odnosi się do usługi Azure Storage.
* Aby zapewnić odpowiednie wskazówki dotyczące rozwiązywania problemów związanych z usługą Azure Storage.

### <a name="how-this-guide-is-organized"></a><a name="how-this-guide-is-organized"></a>Jak zorganizowany jest ten przewodnik
Sekcja "[monitorowanie usługi magazynu]" opisuje sposób monitorowania kondycji i wydajności usług Azure Storage przy użyciu metryk Azure Storage Analytics (metryki magazynu).

Sekcja "[diagnozowanie problemów z magazynem]" opisuje, jak zdiagnozować problemy przy użyciu rejestrowania Azure Storage Analytics (Rejestrowanie magazynu). Opisano w nim również, jak włączyć rejestrowanie po stronie klienta przy użyciu funkcji w jednej z bibliotek klienckich, takich jak Biblioteka klienta usługi Storage dla platformy .NET lub zestawu Azure SDK dla języka Java.

Sekcja "[kompleksowe śledzenie]" opisuje sposób skorelowania informacji zawartych w różnych plikach dziennika i danych metryk.

Sekcja "[wskazówki dotyczące rozwiązywania problemów]" zawiera wskazówki dotyczące rozwiązywania niektórych typowych problemów związanych z magazynem, które mogą wystąpić.

"[Dodatki]" zawierają informacje dotyczące korzystania z innych narzędzi, takich jak Wireshark i NetMon do analizowania danych pakietów sieciowych oraz programu Fiddler do analizowania komunikatów http/https.

## <a name="monitoring-your-storage-service"></a><a name="monitoring-your-storage-service"></a>Monitorowanie usługi magazynu
Jeśli wiesz już, jak działa program Windows Performance Monitoring, możesz traktować metryki magazynu jako odpowiednik usługi Azure Storage w zakresie liczników wydajności systemu Windows. W metrykach magazynu znajduje się kompleksowy zestaw metryk (liczniki w terminologii monitora wydajności systemu Windows), takie jak dostępność usługi, Łączna liczba żądań do obsługi lub procent pomyślnych żądań do usługi. Aby uzyskać pełną listę dostępnych metryk, zobacz [analityka magazynu metryk tabeli schematu](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema). Można określić, czy usługa magazynu ma zbierać i agregowanie metryk co godzinę, czy co minutę. Aby uzyskać więcej informacji o sposobie włączania metryk i monitorowania kont magazynu, zobacz [Włączanie metryk magazynu i wyświetlanie danych metryk](../blobs/monitor-blob-storage.md).

Możesz wybrać metryki godzinowe, które mają być wyświetlane w [Azure Portal](https://portal.azure.com) i skonfigurować reguły, które powiadamiają administratorów pocztą e-mail za każdym razem, gdy Metryka godzinowa przekroczy określony próg. Aby uzyskać więcej informacji, zobacz [Odbieranie powiadomień o alertach](../../azure-monitor/alerts/alerts-overview.md).

Zalecamy zapoznanie się [Azure monitor z magazynem](../../azure-monitor/insights/storage-insights-overview.md) (wersja zapoznawcza). Jest to funkcja Azure Monitor, która oferuje kompleksowe monitorowanie kont usługi Azure Storage, zapewniając ujednolicony wgląd w wydajność, wydajność i dostępność usług Azure Storage. Nie wymaga to włączania ani konfigurowania niczego i można od razu wyświetlać te metryki ze wstępnie zdefiniowanych wykresów interaktywnych i innych wizualizacji.

Usługa Storage zbiera metryki przy użyciu najlepszego nakładu pracy, ale nie może rejestrować każdej operacji magazynu.

W Azure Portal można wyświetlić metryki, takie jak dostępność, Łączna liczba żądań i średnie opóźnienia dla konta magazynu. Reguła powiadomienia została również skonfigurowana w taki sposób, aby ostrzegał administratora o tym, że dostępność spadnie poniżej określonego poziomu. W przypadku wyświetlania tych danych jeden z możliwych obszarów do zbadania to procent sukcesu usługi tabeli poniżej 100% (Aby uzyskać więcej informacji, zobacz sekcję "[metryki Pokaż wpisy dziennika o niskiej PercentSuccess lub analizie zawierają operacje ze stanem transakcji ClientOtherErrors]").

Należy stale monitorować aplikacje platformy Azure, aby upewnić się, że są w dobrej kondycji i są wykonywane zgodnie z oczekiwaniami:

* Ustanowienie niektórych metryk linii bazowej dla aplikacji, które umożliwią porównanie bieżących danych i zidentyfikowanie wszelkich znaczących zmian w zachowaniu usługi Azure Storage i aplikacji. Wartości metryki linii bazowej w wielu przypadkach są specyficzne dla aplikacji i należy je ustalić w przypadku testowania wydajności aplikacji.
* Rejestrowanie metryk minut i używanie ich do aktywnego monitorowania pod kątem nieoczekiwanych błędów i anomalii, takich jak maksymalne liczby błędów lub stawki żądania.
* Rejestrowanie metryk godzinowych i używanie ich do monitorowania średnich wartości, takich jak średnia liczba błędów i szybkość żądania.
* Badanie potencjalnych problemów przy użyciu narzędzi diagnostycznych opisanych w dalszej części artykułu "[diagnozowanie problemów z magazynem]".

Wykresy na poniższej ilustracji ilustrują sposób, w jaki przeciętny wpływ na metryki godzinowe może ukryć skoki w działaniu. Metryki godzinowe pojawiają się, aby pokazać stałą częstotliwość żądań, natomiast metryki minut ujawniają wahania, które naprawdę odbywają się.

![Wykresy pokazujące, jak przeciętne występujące w przypadku metryk co godzinę mogą ukrywać skoki w działaniu.][3]

W pozostałej części tej sekcji opisano metryki, które należy monitorować i dlaczego.

### <a name="monitoring-service-health"></a><a name="monitoring-service-health"></a>Kondycja usługi monitorowania
Możesz użyć [Azure Portal](https://portal.azure.com) , aby wyświetlić kondycję usługi magazynu (i innych usług platformy Azure) we wszystkich regionach platformy Azure na całym świecie. Monitorowanie umożliwia natychmiastowe sprawdzenie, czy problem poza formantem ma wpływ na usługę magazynu w regionie używanym przez aplikację.

[Azure Portal](https://portal.azure.com) może również udostępniać powiadomienia o zdarzeniach, które mają wpływ na różne usługi platformy Azure.
Uwaga: te informacje były wcześniej dostępne wraz z danymi historycznymi na [pulpicie nawigacyjnym usług systemu Azure](https://status.azure.com).
Aby uzyskać więcej informacji na temat Application Insights usługi Azure DevOps, zobacz dodatek "[dodatek 5: monitorowanie za pomocą Application Insights dla platformy Azure DevOps](#appendix-5)".

### <a name="monitoring-capacity"></a><a name="monitoring-capacity"></a>Monitorowanie wydajności
Metryki magazynu przechowują tylko metryki pojemności dla usługi BLOB Service, ponieważ obiekty blob zwykle uwzględniają największą część przechowywanych danych (w czasie pisania nie można używać metryk magazynu do monitorowania pojemności tabel i kolejek). Te dane można znaleźć w tabeli **$MetricsCapacityBlob** , jeśli włączono monitorowanie BLOB Service. Metryki magazynu zapisują te dane raz dziennie i można użyć wartości **RowKey** , aby określić, czy wiersz zawiera jednostkę, która odnosi się do danych użytkownika ( **dane** wartości) czy danych analitycznych ( **Analiza** wartości). Każda składowana jednostka zawiera informacje o ilości używanej pamięci (**pojemność** mierzona w bajtach) oraz o bieżącej liczbie kontenerów (**ContainerCount**) i obiektów BLOB (**ObjectCount**) używanych na koncie magazynu. Aby uzyskać więcej informacji na temat metryk pojemności przechowywanych w tabeli **$MetricsCapacityBlob** , zobacz [analityka magazynu metryk tabeli schematu](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema).

> [!NOTE]
> Należy monitorować te wartości, aby uzyskać wczesne ostrzeżenie, że zbliżasz się do limitów pojemności konta magazynu. W Azure Portal można dodać reguły alertów w celu powiadomienia, jeśli zagregowane użycie magazynu przekracza lub spadnie poniżej progów określonych przez użytkownika.
>
>

Aby uzyskać pomoc w szacowaniu rozmiaru różnych obiektów magazynu, takich jak obiekty blob, zobacz wpis w blogu [Informacje o rozliczeniach usługi Azure Storage — przepustowości, transakcjach i pojemności](/archive/blogs/patrick_butler_monterde/azure-storage-understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity).

### <a name="monitoring-availability"></a><a name="monitoring-availability"></a>Monitorowanie dostępności
Dostępność usług magazynu na koncie magazynu należy monitorować przez monitorowanie wartości w kolumnie **dostępność** w tabelach metryk godzinowych lub minutowych — **$MetricsHourPrimaryTransactionsBlob**, **$MetricsHourPrimaryTransactionsTable**, **$MetricsHourPrimaryTransactionsQueue**, **$MetricsMinutePrimaryTransactionsBlob**, **$MetricsMinutePrimaryTransactionsTable**, **$MetricsMinutePrimaryTransactionsQueue**, **$MetricsCapacityBlob**. Kolumna **dostępności** zawiera wartość procentową wskazującą dostępność usługi lub operacji interfejsu API reprezentowanej przez wiersz ( **RowKey** pokazuje, czy wiersz zawiera metryki dla usługi jako całość lub dla konkretnej operacji interfejsu API).

Każda wartość mniejsza niż 100% wskazuje, że niektóre żądania magazynu kończą się niepowodzeniem. Możesz sprawdzić, dlaczego są one zakończone niepowodzeniem, badając inne kolumny w danych metryk, które pokazują liczbę żądań z różnymi typami błędów, takimi jak **ServerTimeoutError**. Należy oczekiwać, że **dostępność** przekroczy mniej niż 100% z powodów, takich jak przejściowe limity czasu serwera, podczas gdy usługa przenosi partycje w celu lepszego żądania równoważenia obciążenia; Logika ponowień w aplikacji klienckiej powinna obsługiwać takie sporadyczne warunki. Artykuł [analityka magazynu zarejestrowanymi operacjami i komunikatami o stanie](/rest/api/storageservices/Storage-Analytics-Logged-Operations-and-Status-Messages) zawiera listę typów transakcji, które metryki magazynu obejmują w ich obliczeniach **dostępności** .

W [Azure Portal](https://portal.azure.com)można dodać reguły alertów w celu powiadomienia, jeśli **dostępność** usługi spadnie poniżej progu określonego przez użytkownika.

W sekcji "[wskazówki dotyczące rozwiązywania problemów]" w tym przewodniku opisano niektóre typowe problemy dotyczące usługi Storage związane z dostępnością.

### <a name="monitoring-performance"></a><a name="monitoring-performance"></a>Monitorowanie wydajności
Aby monitorować wydajność usług magazynu, można użyć następujących metryk z tabeli metryk godzinowych i minutowych.

* Wartości w kolumnach **niską averagee2elatency** i **wartość averageserverlatency** przedstawiają średni czas przetwarzania żądań usługi magazynu lub operacji interfejsu API. **Niską averagee2elatency** to miara kompleksowego opóźnienia, która obejmuje czas potrzebny na odczytanie żądania i wysłanie odpowiedzi oprócz czasu na przetworzenie żądania (w związku z tym opóźnienia sieci, gdy żądanie osiągnie usługę magazynu); **Wartość averageserverlatency** to miara tylko czasu przetwarzania i w związku z tym wyklucza wszelkie opóźnienia sieci związane z komunikowaniem się z klientem. Zapoznaj się z sekcją "[metryki Pokaż duże niską averagee2elatency i Low wartość averageserverlatency]" w dalszej części tego przewodnika, aby zapoznać się z omówieniem przyczyny znaczącej różnicy między tymi dwiema wartościami.
* Wartości w kolumnach **TotalIngress** i **TotalEgress** przedstawiają łączną ilość danych (w bajtach) i wychodzącą z usługi magazynu lub przez określony typ operacji interfejsu API.
* Wartości w kolumnie **TotalRequests** przedstawiają łączną liczbę żądań otrzymywanych przez usługę magazynu operacji interfejsu API. **TotalRequests** to całkowita liczba żądań odbieranych przez usługę magazynu.

Zwykle monitorujesz pod kątem nieoczekiwanych zmian w którejkolwiek z tych wartości jako wskaźnik problemu, który wymaga badania.

W [Azure Portal](https://portal.azure.com)można dodać reguły alertów w celu powiadomienia użytkownika, Jeśli którakolwiek z metryk wydajności dla tej usługi spadnie poniżej lub przekroczy określony próg.

W sekcji "[wskazówki dotyczące rozwiązywania problemów]" w tym przewodniku opisano niektóre typowe problemy dotyczące usługi Storage związane z wydajnością.

## <a name="diagnosing-storage-issues"></a><a name="diagnosing-storage-issues"></a>Diagnozowanie problemów z magazynem
Istnieje kilka sposobów, na które można mieć świadomość problemu lub problemu w aplikacji, w tym:

* Poważny błąd, który powoduje awarię aplikacji lub zaprzestanie jej działania.
* Znaczące zmiany wartości linii bazowej w monitorowanych metrykach zgodnie z opisem w poprzedniej sekcji "[monitorowanie usługi magazynu]".
* Raporty od użytkowników aplikacji, że niektóre konkretne operacje nie zostały wykonane zgodnie z oczekiwaniami lub niektóre funkcje nie działają.
* Błędy wygenerowane w aplikacji, które pojawiają się w plikach dziennika lub w innej metodzie powiadamiania.

Zazwyczaj problemy związane z usługami Azure Storage należą do jednej z czterech ogólnych kategorii:

* Aplikacja ma problem z wydajnością, zgłaszany przez użytkowników lub ujawnił zmiany w metrykach wydajności.
* Wystąpił problem z infrastrukturą usługi Azure Storage w co najmniej jednym regionie.
* Aplikacja napotyka błąd, zgłoszone przez użytkowników lub wykazało, że występuje wzrost jednej z metryk licznika błędów, które monitorują.
* Podczas tworzenia i testowania może być używany lokalny emulator magazynu; mogą wystąpić problemy, które odnoszą się głównie do użycia emulatora magazynu.

W poniższych sekcjach opisano kroki, które należy wykonać, aby zdiagnozować i rozwiązać problemy w każdej z tych czterech kategorii. Sekcja "[wskazówki dotyczące rozwiązywania problemów]" w dalszej części tego przewodnika zawiera więcej szczegółów dotyczących niektórych typowych problemów, które mogą wystąpić.

### <a name="service-health-issues"></a><a name="service-health-issues"></a>Problemy z kondycją usługi
Problemy z kondycją usługi są zwykle poza kontrolką. [Azure Portal](https://portal.azure.com) zawiera informacje o ewentualnych problemach z usługami platformy Azure, w tym usługami magazynu. Jeśli podczas tworzenia konta magazynu wybrano opcję Read-Access Geo-Redundant Storage, jeśli dane staną się niedostępne w lokalizacji głównej, aplikacja może przełączać się tymczasowo do kopii tylko do odczytu w lokalizacji dodatkowej. Aby można było odczytywać dane z pomocniczego programu, aplikacja musi być w stanie przełączać się między podstawowymi i pomocniczymi lokalizacjami magazynu i mieć możliwość pracy w trybie zmniejszonej funkcjonalności z danymi tylko do odczytu. Biblioteki klienta usługi Azure Storage umożliwiają zdefiniowanie zasad ponawiania, które mogą odczytywać z magazynu pomocniczego w przypadku niepowodzenia odczytu z magazynu podstawowego. Aplikacja musi również mieć świadomość, że dane w lokalizacji pomocniczej są ostatecznie spójne. Aby uzyskać więcej informacji, zobacz wpis w blogu [Opcje nadmiarowości usługi Azure Storage i dostęp do odczytu Geograficznie nadmiarowy magazyn](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

### <a name="performance-issues"></a><a name="performance-issues"></a>Problemy z wydajnością
Wydajność aplikacji może być wartością subiektywną, zwłaszcza z punktu widzenia użytkownika. Dlatego należy mieć dostępne metryki linii bazowej, które ułatwiają wykrywanie problemów z wydajnością. Wiele czynników może mieć wpływ na wydajność usługi Azure Storage z perspektywy aplikacji klienckiej. Te czynniki mogą działać w usłudze Storage, na kliencie lub w infrastrukturze sieci. w związku z tym ważne jest, aby mieć strategię do identyfikowania pochodzenia problemu z wydajnością.

Po zidentyfikowaniu najprawdopodobniej lokalizacji przyczyny problemu z wydajnością z metryk można użyć plików dziennika, aby znaleźć szczegółowe informacje umożliwiające diagnozowanie i rozwiązywanie problemów.

Sekcja "[wskazówki dotyczące rozwiązywania problemów]" w dalszej części tego przewodnika zawiera więcej informacji na temat niektórych typowych problemów związanych z wydajnością, które mogą wystąpić.

### <a name="diagnosing-errors"></a><a name="diagnosing-errors"></a>Diagnozowanie błędów
Użytkownicy Twojej aplikacji mogą powiadomić użytkownika o błędach raportowanych przez aplikację kliencką. Metryki magazynu rejestrują także liczbę różnych typów błędów z usług magazynu, takich jak **NetworkError**, **ClientTimeoutError** lub **AuthorizationError**. Chociaż metryki magazynu rejestrują tylko liczbę różnych typów błędów, można uzyskać więcej szczegółowych informacji o poszczególnych żądaniach, przeglądając dzienniki po stronie serwera, klienta i sieci. Zazwyczaj kod stanu HTTP zwracany przez usługę Storage wskazuje dlaczego żądanie nie powiodło się.

> [!NOTE]
> Należy pamiętać, że powinny być widoczne błędy sporadyczne: na przykład błędy z powodu przejściowych warunków sieciowych lub błędy aplikacji.
>
>

Przydatne dla zrozumienia kodów stanu i błędów związanych z magazynem są następujące zasoby:

* [Typowe kody błędów interfejsu API REST](/rest/api/storageservices/Common-REST-API-Error-Codes)
* [Kody błędów usługi BLOB](/rest/api/storageservices/Blob-Service-Error-Codes)
* [Kody błędów usługi kolejki](/rest/api/storageservices/Queue-Service-Error-Codes)
* [Kody błędów usługi Table Service](/rest/api/storageservices/Table-Service-Error-Codes)
* [Kody błędów usługi plików](/rest/api/storageservices/File-Service-Error-Codes)

### <a name="storage-emulator-issues"></a><a name="storage-emulator-issues"></a>Problemy emulatora magazynu
Zestaw Azure SDK zawiera emulator magazynu, który można uruchomić na stacji roboczej deweloperskiej. Ten emulator symuluje większość zachowań usług Azure Storage i jest przydatny podczas tworzenia i testowania, umożliwiając uruchamianie aplikacji korzystających z usług Azure Storage bez potrzeby subskrypcji platformy Azure i konta usługi Azure Storage.

W sekcji "[wskazówki dotyczące rozwiązywania problemów]" w tym przewodniku opisano niektóre typowe problemy występujące podczas korzystania z emulatora magazynu.

### <a name="storage-logging-tools"></a><a name="storage-logging-tools"></a>Narzędzia rejestrowania magazynu
Rejestrowanie magazynu umożliwia rejestrowanie żądań magazynu po stronie serwera na koncie usługi Azure Storage. Aby uzyskać więcej informacji na temat włączania rejestrowania po stronie serwera i uzyskiwania dostępu do danych dziennika, zobacz [Włączanie rejestrowania magazynu i uzyskiwanie dostępu do danych dziennika](./storage-analytics-logging.md).

Biblioteka klienta usługi Storage dla platformy .NET umożliwia zbieranie danych dziennika po stronie klienta, które odnoszą się do operacji magazynu wykonywanych przez aplikację. Aby uzyskać więcej informacji, zobacz [Client-side Logging with the .NET Storage Client Library](/rest/api/storageservices/Client-side-Logging-with-the-.NET-Storage-Client-Library) (Logowanie po stronie klienta przy użyciu biblioteki klienckiej usługi .NET Storage).

> [!NOTE]
> W niektórych sytuacjach (takich jak błędy autoryzacji sygnatury dostępu współdzielonego) użytkownik może zgłosić błąd, dla którego nie można znaleźć danych żądania w dziennikach magazynu po stronie serwera. Możesz użyć funkcji rejestrowania w bibliotece klienta magazynu, aby sprawdzić, czy przyczyną problemu jest klient, czy też użyć narzędzi do monitorowania sieci do zbadania sieci.
>
>

### <a name="using-network-logging-tools"></a><a name="using-network-logging-tools"></a>Korzystanie z narzędzi do rejestrowania w sieci
Możesz przechwytywać ruch między klientem a serwerem, aby podać szczegółowe informacje o danych, które klient i serwer wymieniają, i podstawowych warunkach sieciowych. Przydatne narzędzia do rejestrowania w sieci obejmują:

* [Programu Fiddler](https://www.telerik.com/fiddler) to bezpłatny serwer proxy debugowania sieci Web, który umożliwia badanie nagłówków i danych ładunku komunikatów i żądań HTTP i https. Aby uzyskać więcej informacji, zobacz [dodatek 1: używanie programu Fiddler do przechwytywania ruchu HTTP i https](#appendix-1).
* [Microsoft Network Monitor (netmon)](https://download.cnet.com/s/network-monitor/) i [Wireshark](https://www.wireshark.org/) to bezpłatne analizatory protokołów sieciowych, które umożliwiają wyświetlanie szczegółowych informacji o pakiecie dla szerokiego zakresu protokołów sieciowych. Aby uzyskać więcej informacji na temat programu Wireshark, zobacz "[dodatek 2: korzystanie z programu Wireshark do przechwytywania ruchu sieciowego](#appendix-2)".
* Jeśli chcesz przeprowadzić podstawowy test łączności, aby sprawdzić, czy komputer kliencki może połączyć się z usługą Azure Storage za pośrednictwem sieci, nie możesz wykonać tego czynności przy użyciu standardowego narzędzia **ping** na kliencie. Można jednak sprawdzić łączność przy użyciu [narzędzia **tcping**](https://www.elifulkerson.com/projects/tcping.php) .

W wielu przypadkach dane dziennika z rejestrowania magazynu i biblioteki klienta usługi Storage będą wystarczające do zdiagnozowania problemu, ale w niektórych scenariuszach może być konieczne wprowadzenie bardziej szczegółowych informacji, które mogą być używane przez te narzędzia rejestrowania sieciowego. Na przykład za pomocą programu Fiddler do wyświetlania komunikatów HTTP i HTTPS można wyświetlać dane nagłówka i ładunku wysyłane do i z usług magazynu, co umożliwi sprawdzenie, jak aplikacja kliencka ponawia operacje magazynu. Analizatory protokołu, takie jak program Wireshark, działają na poziomie pakietu, umożliwiając wyświetlanie danych TCP, co umożliwia rozwiązywanie problemów z utraconymi pakietami i problemami z łącznością. 

## <a name="end-to-end-tracing"></a><a name="end-to-end-tracing"></a>Kompleksowe śledzenie
Kompleksowe śledzenie przy użyciu różnych plików dziennika jest przydatną techniką do badania potencjalnych problemów. Możesz użyć informacji o dacie/godzinie z danych metryk jako wskazanie lokalizacji, w której należy rozpocząć wyszukiwanie w plikach dziennika, aby uzyskać szczegółowe informacje, które pomogą w rozwiązaniu problemu.

### <a name="correlating-log-data"></a><a name="correlating-log-data"></a>Skorelowanie danych dziennika
Podczas wyświetlania dzienników z aplikacji klienckich, śladów sieci i rejestrowania magazynu po stronie serwera, krytyczne jest, aby można było skorelować żądania w różnych plikach dziennika. Pliki dziennika zawierają wiele różnych pól, które są przydatne jako identyfikatory korelacji. Identyfikator żądania klienta jest najbardziej przydatnym polem służącym do skorelowania wpisów w różnych dziennikach. Jednak czasami może być przydatne użycie identyfikatora lub sygnatury czasowej żądania serwera. Poniższe sekcje zawierają więcej informacji na temat tych opcji.

### <a name="client-request-id"></a><a name="client-request-id"></a>Identyfikator żądania klienta
Biblioteka klienta usługi Storage automatycznie generuje unikatowy identyfikator żądania klienta dla każdego żądania.

* W dzienniku po stronie klienta, który tworzy Biblioteka klienta magazynu, identyfikator żądania klienta jest wyświetlany w polu **Identyfikator żądania klienta** każdego wpisu dziennika odnoszącego się do żądania.
* W wyniku śledzenia sieci, takiego jak jeden przechwycony przez programu Fiddler, identyfikator żądania klienta jest widoczny w komunikatach żądania jako wartość nagłówka HTTP **-MS-Client-Request-ID** .
* W dzienniku rejestrowania magazynu po stronie serwera identyfikator żądania klienta jest wyświetlany w kolumnie Identyfikator żądania klienta.

> [!NOTE]
> Istnieje możliwość, że wiele żądań współużytkuje ten sam identyfikator żądania klienta, ponieważ klient może przypisać tę wartość (mimo że Biblioteka klienta magazynu automatycznie przypisuje nową wartość). Po ponownym próbie klienta wszystkie próby współdzielą ten sam identyfikator żądania klienta. W przypadku partii wysyłanej z klienta partia zawiera jeden identyfikator żądania klienta.
>
>

### <a name="server-request-id"></a><a name="server-request-id"></a>Identyfikator żądania serwera
Usługa magazynu automatycznie generuje identyfikatory żądań serwera.

* W dzienniku rejestrowania magazynu po stronie serwera identyfikator żądania serwera jest wyświetlany w kolumnie **nagłówek żądania** .
* W wyniku śledzenia sieci, takiego jak jeden przechwytywany przez programu Fiddler, identyfikator żądania serwera pojawia się w komunikatach odpowiedzi jako wartość nagłówka HTTP **-MS-Request-ID** .
* W dzienniku po stronie klienta, który tworzy Biblioteka klienta usługi Storage, w kolumnie **Tekst operacji** dla wpisu dziennika zostanie wyświetlony Identyfikator żądania serwera zawierający szczegóły odpowiedzi serwera.

> [!NOTE]
> Usługa Storage zawsze przypisuje unikatowy identyfikator żądania serwera do każdego odebranego żądania, więc każda próba ponowienia próby od klienta, a każda operacja uwzględniona w partii ma unikatowy identyfikator żądania serwera.
>
>

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

Poniższy przykład kodu pokazuje, jak używać niestandardowego identyfikatora żądania klienta. 

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Monitoring.cs" id="Snippet_UseCustomRequestID":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

Jeśli Biblioteka klienta magazynu zgłasza wyjątek **magazynu** na kliencie, właściwość **RequestInformation** zawiera obiekt **RequestResult** , który zawiera właściwość **ServiceRequestID** . Możesz również uzyskać dostęp do obiektu **RequestResult** z wystąpienia elementu **OperationContext** .

Poniższy przykład kodu ilustruje sposób ustawienia niestandardowej wartości **identyfikatorem żądania klienta** przez dołączenie obiektu **OperationContext** do żądania usługi magazynu. Pokazano również, jak pobrać wartość **ServerRequestId** z komunikatu odpowiedzi.

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Create an Operation Context that includes custom ClientRequestId string based on constants defined within the application along with a Guid.
OperationContext oc = new OperationContext();
oc.ClientRequestID = String.Format("{0} {1} {2} {3}", HOSTNAME, APPNAME, USERID, Guid.NewGuid().ToString());

try
{
    CloudBlobContainer container = blobClient.GetContainerReference("democontainer");
    ICloudBlob blob = container.GetBlobReferenceFromServer("testImage.jpg", null, null, oc);  
    var downloadToPath = string.Format("./{0}", blob.Name);
    using (var fs = File.OpenWrite(downloadToPath))
    {
        blob.DownloadToStream(fs, null, null, oc);
        Console.WriteLine("\t Blob downloaded to file: {0}", downloadToPath);
    }
}
catch (StorageException storageException)
{
    Console.WriteLine("Storage exception {0} occurred", storageException.Message);
    // Multiple results may exist due to client side retry logic - each retried operation will have a unique ServiceRequestId
    foreach (var result in oc.RequestResults)
    {
            Console.WriteLine("HttpStatus: {0}, ServiceRequestId {1}", result.HttpStatusCode, result.ServiceRequestID);
    }
}
```

---

### <a name="timestamps"></a><a name="timestamps"></a>Znaczniki czasu
Sygnatury czasowe można także użyć do lokalizowania powiązanych wpisów dziennika, ale należy zachować ostrożność podczas pochylenia zegara między klientem a serwerem, który może istnieć. Wyszukaj i minus 15 minut dla pasujących wpisów po stronie serwera na podstawie sygnatury czasowej klienta. Należy pamiętać, że metadane obiektów BLOB dla obiektów BLOB zawierających metryki wskazują zakres czasu dla metryk przechowywanych w obiekcie blob. Ten zakres czasu jest przydatny, jeśli masz wiele obiektów BLOB metryk przez tę samą minutę lub godzinę.

## <a name="troubleshooting-guidance"></a><a name="troubleshooting-guidance"></a>Wskazówki dotyczące rozwiązywania problemów
Ta sekcja pomoże Ci w diagnozowaniu i rozwiązywaniu problemów z typowymi problemami, które może napotkać aplikacja podczas korzystania z usług Azure Storage. Skorzystaj z poniższej listy, aby znaleźć informacje istotne dla danego problemu.

**Drzewo decyzyjne**

---
Czy problem jest związany z wydajnością jednego z usług magazynu?

* [Metryki wskazują wysoką wartość AverageE2ELatency i niską wartość AverageServerLatency]
* [Metryki wskazują niską wartość AverageE2ELatency i niską wartość AverageServerLatency, ale na kliencie występuje duże opóźnienie]
* [Metryki wskazują wysoką wartość AverageServerLatency]
* [Występują nieoczekiwane opóźnienia w dostarczaniu komunikatów w kolejce]

---
Czy problem odnosi się do dostępności jednego z usług magazynu?

* [Metryki wskazują wzrost wartości PercentThrottlingError]
* [Metryki wskazują wzrost wartości PercentTimeoutError]
* [Metryki wskazują wzrost wartości PercentNetworkError]

---
 Czy aplikacja kliencka otrzymuje odpowiedź HTTP 4XX (na przykład 404) z usługi magazynu?

* [Klient odbiera komunikaty HTTP 403 (zabronione)]
* [Klient odbiera komunikaty HTTP 404 (nie znaleziono)]
* [Klient odbiera komunikaty HTTP 409 (konflikt)]

---
[Metryki pokazujące niskie PercentSuccess lub wpisy dziennika analizy zawierają operacje ze stanem transakcji ClientOtherErrors]

---
[Metryki pojemności pokazują nieoczekiwany wzrost użycia pojemności magazynu]

---
[Występują nieoczekiwane ponowne uruchomienia Virtual Machines, które mają dużą liczbę dołączonych wirtualnych dysków twardych]

---
[Problem wynika z używania emulatora magazynu na potrzeby tworzenia i testowania]

---
[Występują problemy z instalacją zestawu Azure SDK dla platformy .NET]

---
[Istnieje inny problem dotyczący usługi magazynu]

---
### <a name="metrics-show-high-averagee2elatency-and-low-averageserverlatency"></a><a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>Metryki wskazują wysoką wartość AverageE2ELatency i niską wartość AverageServerLatency
Na poniższej ilustracji [Azure Portal](https://portal.azure.com) narzędzia do monitorowania przedstawiono przykład, w którym **niską averagee2elatency** jest znacznie większa niż **wartość averageserverlatency**.

![Ilustracja z Azure Portal, który pokazuje przykład, gdzie niską averagee2elatency jest znacznie wyższy niż wartość averageserverlatency.][4]

Usługa magazynu oblicza tylko **niską averagee2elatency** metryk dla pomyślnych żądań i, w przeciwieństwie do **wartość averageserverlatency**, obejmuje czas wymagany przez klienta do wysłania danych i otrzymania potwierdzenia z usługi magazynu. W związku z tym różnica między **niską averagee2elatency** i **wartość averageserverlatency** może być spowodowana powolnej reakcji aplikacji klienckiej lub ze względu na warunki w sieci.

> [!NOTE]
> Można również wyświetlać **E2ELatency** i **ServerLatency** dla poszczególnych operacji magazynu w danych dziennika rejestrowania magazynu.
>
>

#### <a name="investigating-client-performance-issues"></a>Badanie problemów z wydajnością klienta
Możliwe przyczyny, dla których klient odpowie wolno, obejmują ograniczoną liczbę dostępnych połączeń lub wątków lub niską ilość zasobów, takich jak procesor CPU, pamięć lub przepustowość sieci. Można rozwiązać ten problem, modyfikując kod klienta, aby był bardziej wydajny (na przykład za pomocą wywołań asynchronicznych usługi Storage) lub przy użyciu większej maszyny wirtualnej (z większą liczbą rdzeni i więcej pamięci).

W przypadku usług tabel i kolejek algorytm nagle może również spowodować duże **niską averagee2elatency** w porównaniu z **wartość averageserverlatency**: Aby uzyskać więcej informacji, zobacz Algorytm post [nagle nie jest przyjazny do małych żądań](/archive/blogs/windowsazurestorage/nagles-algorithm-is-not-friendly-towards-small-requests). Algorytm nagle można wyłączyć w kodzie przy użyciu klasy **ServicePointManager** w przestrzeni nazw **System.NET** . Należy to zrobić przed wprowadzeniem jakichkolwiek wywołań do usług Table lub Queue w aplikacji, ponieważ nie ma to wpływu na połączenia, które są już otwarte. Poniższy przykład pochodzi z metody **Application_Start** w roli procesu roboczego.

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Monitoring.cs" id="Snippet_DisableNagle":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);
ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
queueServicePoint.UseNagleAlgorithm = false;
```

---

Należy sprawdzić dzienniki po stronie klienta, aby zobaczyć liczbę żądań przesyłanych przez aplikację kliencką, a także sprawdzić ogólne wąskie gardła wydajności związane z platformą .NET na kliencie, takie jak procesor CPU, wyrzucanie elementów bezużytecznych platformy .NET, wykorzystanie sieci lub pamięć. Jako punkt wyjścia do rozwiązywania problemów z aplikacjami klienckimi platformy .NET, zobacz [debugowanie, śledzenie i profilowanie](/dotnet/framework/debug-trace-profile/).

#### <a name="investigating-network-latency-issues"></a>Badanie problemów z opóźnieniem sieci
Zwykle wysokie opóźnienie spowodowane przez sieć wynika z przejściowych warunków. Można zbadać zarówno przejściowe, jak i trwałe problemy z siecią, takie jak pakiety porzucone, przy użyciu narzędzi takich jak Wireshark.

Aby uzyskać więcej informacji na temat rozwiązywania problemów z siecią przy użyciu programu Wireshark, zobacz "[dodatek 2: korzystanie z programu Wireshark do przechwytywania ruchu sieciowego]".

### <a name="metrics-show-low-averagee2elatency-and-low-averageserverlatency-but-the-client-is-experiencing-high-latency"></a><a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>Metryki wskazują niską wartość AverageE2ELatency i niską wartość AverageServerLatency, ale na kliencie występuje duże opóźnienie
W tym scenariuszu najbardziej prawdopodobną przyczyną jest opóźnienie żądań magazynu, które docierają do usługi Storage. Należy sprawdzić, dlaczego żądania klienta nie są wysyłane do usługi BLOB Service.

Jedną z możliwych przyczyn opóźnienia wysyłania żądań przez klienta jest to, że istnieje ograniczona liczba dostępnych połączeń lub wątków.

Sprawdź również, czy klient wykonuje wiele ponownych prób i zbadaj przyczynę, jeśli jest. Aby określić, czy klient wykonuje wiele ponownych prób, możesz wykonać następujące czynności:

* Zapoznaj się z dziennikami analityka magazynu. Jeśli wykonywane jest wiele ponownych prób, zobaczysz wiele operacji z tym samym IDENTYFIKATORem żądania klienta, ale z różnymi identyfikatorami żądań serwera.
* Przejrzyj dzienniki klienta. Pełne rejestrowanie będzie oznaczało, że nastąpiło ponowienie próby.
* Debuguj kod i sprawdź właściwości obiektu **OperationContext** skojarzonego z żądaniem. Jeśli operacja została ponowiona, właściwość **RequestResults** będzie zawierać wiele unikatowych identyfikatorów żądań serwera. Możesz również sprawdzić godziny rozpoczęcia i zakończenia każdego żądania. Aby uzyskać więcej informacji, zobacz przykładowy kod w sekcji [Identyfikator żądania serwera].

Jeśli klient nie ma żadnych problemów, należy zbadać potencjalne problemy z siecią, takie jak utrata pakietów. Aby zbadać problemy z siecią, możesz skorzystać z narzędzi takich jak Wireshark.

Aby uzyskać więcej informacji na temat rozwiązywania problemów z siecią przy użyciu programu Wireshark, zobacz "[dodatek 2: korzystanie z programu Wireshark do przechwytywania ruchu sieciowego]".

### <a name="metrics-show-high-averageserverlatency"></a><a name="metrics-show-high-AverageServerLatency"></a>Metryki wskazują wysoką wartość AverageServerLatency
W przypadku **wartość averageserverlatency** żądań pobrania obiektów BLOB należy użyć dzienników rejestrowania magazynu, aby sprawdzić, czy istnieją powtórzone żądania dla tego samego obiektu BLOB (lub zestawu obiektów BLOB). W przypadku żądań przekazywania obiektów BLOB należy zbadać, jaki rozmiar bloku jest używany przez klienta (na przykład bloki o rozmiarze mniejszym niż 64 K) mogą spowodować nadmiarowe, chyba że odczyty są również w mniej niż 64 K. Należy również sprawdzić metryki dla minut dla liczby żądań, które powodują przekroczenie na sekundę elementów docelowych skalowalności: Zobacz też "[metryki pokazują wzrost w wzrost percenttimeouterror]".

Jeśli widzisz wysokie **wartość averageserverlatency** żądań pobrania obiektów blob, gdy istnieją powtórzone żądania tego samego obiektu BLOB lub zestawu obiektów blob, należy rozważyć buforowanie tych obiektów BLOB za pomocą usługi Azure cache lub Azure Content Delivery Network (CDN). W przypadku żądań przekazywania można zwiększyć przepływność przy użyciu większego rozmiaru bloku. W przypadku zapytań dotyczących tabel można również zaimplementować buforowanie po stronie klienta na klientach wykonujących te same operacje zapytania i gdzie dane nie ulegają częstym zmianom.

Wysokie **wartość averageserverlatency** wartości mogą również być objawami niewłaściwie zaprojektowanych tabel lub zapytań, które powodują operacje skanowania lub które są zgodne ze standardem dołączania/dopasowywania. Aby uzyskać więcej informacji, zobacz "[metryki pokazują wzrost w wzrost percentthrottlingerror]".

> [!NOTE]
> Wyczerpującą listę kontrolną wydajności można znaleźć w tym miejscu: [Microsoft Azure Storage listy kontrolnej wydajności i skalowalności](../blobs/storage-performance-checklist.md).
>
>

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery-on-a-queue"></a><a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>Występują nieoczekiwane opóźnienia w dostarczaniu komunikatów w kolejce
Jeśli występują opóźnienia między czasem, gdy aplikacja dodaje komunikat do kolejki i czas, w którym będzie ona dostępna do odczytu z kolejki, należy wykonać następujące czynności, aby zdiagnozować problem:

* Sprawdź, czy aplikacja pomyślnie dodaje komunikaty do kolejki. Sprawdź, czy aplikacja nie ponawia próbę wykonania metody **AddMessage** kilka razy przed sukcesem. W dziennikach biblioteki klienta magazynu zostaną wyświetlone wszystkie powtórzone próby operacji magazynu.
* Upewnij się, że nie ma żadnych pochylenia zegara między rolą procesu roboczego, która dodaje komunikat do kolejki, i rolę procesu roboczego, która odczytuje komunikat z kolejki, co sprawia, że w przypadku opóźnienia przetwarzania.
* Sprawdź, czy rola procesu roboczego, która odczytuje komunikaty z kolejki, kończy się niepowodzeniem. Jeśli klient kolejki wywoła metodę **GetMessage** , ale nie odpowie na potwierdzenie, komunikat pozostanie niewidoczny w kolejce do czasu wygaśnięcia okresu **invisibilityTimeout** . W tym momencie komunikat zostaje ponownie udostępniony do przetworzenia.
* Sprawdź, czy długość kolejki rośnie z upływem czasu. Taka sytuacja może wystąpić, jeśli nie masz wystarczającej liczby pracowników dostępnych do przetworzenia wszystkich komunikatów umieszczanych w kolejce przez innych pracowników. Sprawdź również metryki, aby sprawdzić, czy żądania usunięcia kończą się niepowodzeniem, a w przypadku komunikatów, które mogą wskazywać na powtarzanie nieudanych prób usunięcia komunikatu.
* Zapoznaj się z dziennikami rejestrowania magazynu dla operacji w kolejce, które mają więcej niż oczekiwane wartości **E2ELatency** i **ServerLatency** w dłuższym okresie niż zwykle.

### <a name="metrics-show-an-increase-in-percentthrottlingerror"></a><a name="metrics-show-an-increase-in-PercentThrottlingError"></a>Metryki wskazują wzrost wartości PercentThrottlingError
Błędy ograniczania są wykonywane, gdy przekroczy się tarcze skalowalności usługi magazynu. Ograniczenie usługi magazynu zapewnia, że żaden klient lub dzierżawca nie może korzystać z usługi w kosztach innych. Aby uzyskać więcej informacji, zobacz [cele skalowalności i wydajności dla kont magazynu w warstwie Standardowa](scalability-targets-standard-account.md) , aby uzyskać szczegółowe informacje na temat elementów docelowych skalowalności dla kont magazynu i docelowych wydajności dla partycji w ramach kont magazynu.

Jeśli Metryka **wzrost percentthrottlingerror** wykazuje wzrost procentu liczby żądań, które kończą się niepowodzeniem z powodu błędu ograniczania, należy zbadać jeden z dwóch scenariuszy:

* [Przejściowy wzrost w wzrost percentthrottlingerror]
* [Trwały wzrost błędu wzrost percentthrottlingerror]

Zwiększenie **wzrost percentthrottlingerror** często odbywa się w tym samym czasie, co zwiększa liczbę żądań magazynu, lub podczas początkowego ładowania aplikacji. Może to również zostać zgłoszone przez klienta jako "503 serwer zajęty" lub 500 "limit czasu operacji na żądania HTTP" z operacji magazynu.

#### <a name="transient-increase-in-percentthrottlingerror"></a><a name="transient-increase-in-PercentThrottlingError"></a>Przejściowy wzrost w wzrost percentthrottlingerror
Jeśli widzisz skoki w wartości **wzrost percentthrottlingerror** , które pokrywają się z okresami dużego działania aplikacji, zaimplementujmy strategię odwrotną (nieliniową) dla ponownych prób w kliencie. Ponowne próby wycofania zmniejszają natychmiastowe obciążenie partycji i pomagają aplikacji w obciążeniu skoków ruchu. Aby uzyskać więcej informacji na temat implementowania zasad ponawiania przy użyciu biblioteki klienta usługi Storage, zobacz [przestrzeń nazw Microsoft. Azure. Storage. RetryPolicies](/dotnet/api/microsoft.azure.storage.retrypolicies).

> [!NOTE]
> Możesz również zobaczyć, że w wartości **wzrost percentthrottlingerror** nie nastąpiły niezbieżność z okresami wysokiego działania aplikacji: najbardziej prawdopodobną przyczyną jest przeniesienie partycji przez usługę magazynu w celu usprawnienia równoważenia obciążenia.
>
>

#### <a name="permanent-increase-in-percentthrottlingerror-error"></a><a name="permanent-increase-in-PercentThrottlingError"></a>Trwały wzrost błędu wzrost percentthrottlingerror
Jeśli widzisz spójną dużą wartość dla **wzrost percentthrottlingerror** po trwałym zwiększeniu liczby woluminów transakcji lub podczas wykonywania początkowych testów obciążenia w aplikacji, musisz sprawdzić, w jaki sposób aplikacja korzysta z partycji magazynu i czy zbliża się do elementów docelowych skalowalności dla konta magazynu. Na przykład jeśli widzisz błędy ograniczania w kolejce (które liczą jako pojedynczą partycję), należy rozważyć użycie dodatkowych kolejek do rozłożenia transakcji na wielu partycjach. Jeśli widzisz błędy ograniczania w tabeli, należy rozważyć użycie innego schematu partycjonowania do rozłożenia transakcji na wielu partycjach przy użyciu szerszego zakresu wartości klucza partycji. Jedną z typowych przyczyn tego problemu jest dołączenie/dołączanie antywzorców, w której można wybrać datę jako klucz partycji, a następnie wszystkie dane w określonym dniu są zapisywane do jednej partycji: w przypadku obciążenia może to spowodować wąskie gardło zapisu. Należy wziąć pod uwagę inny projekt partycjonowania lub sprawdzić, czy korzystanie z usługi BLOB Storage może być lepszym rozwiązaniem. Sprawdź również, czy w wyniku skoków w ruchu występuje ograniczenie, i zbadaj metody wygładzania wzorca żądań.

Jeśli transakcje są dystrybuowane między wieloma partycjami, nadal trzeba mieć świadomość ograniczeń skalowalności ustawionych dla konta magazynu. Na przykład jeśli użyto dziesięciu kolejek, każdy przetwarza maksymalnie 2 000 komunikatów rozmiarze 1 KB na sekundę, zostanie osiągnięty całkowity limit 20 000 komunikatów na sekundę dla konta magazynu. Jeśli zachodzi potrzeba przetworzenia ponad 20 000 jednostek na sekundę, należy rozważyć użycie wielu kont magazynu. Należy również pamiętać, że rozmiar żądań i jednostek ma wpływ na to, kiedy usługa magazynu ogranicza klientów: Jeśli masz większe żądania i jednostki, może to być ograniczone.

Niewydajny projekt zapytań może również spowodować osiągnięcie ograniczeń skalowalności dla partycji tabeli. Na przykład zapytanie z filtrem, który wybiera tylko jeden procent jednostek w partycji, ale skanowanie wszystkich jednostek w partycji będzie wymagało dostępu do każdej jednostki. Każda odczytana jednostka będzie liczyć do łącznej liczby transakcji w tej partycji; w związku z tym można łatwo uzyskać dostęp do obiektów docelowych skalowalności.

> [!NOTE]
> Testy wydajności powinny ujawniać wszelkie niewydajne projekty zapytań w aplikacji.
>
>

### <a name="metrics-show-an-increase-in-percenttimeouterror"></a><a name="metrics-show-an-increase-in-PercentTimeoutError"></a>Metryki wskazują wzrost wartości PercentTimeoutError
Twoje metryki pokazują wzrost **wzrost percenttimeouterror** dla jednej z usług magazynu. W tym samym czasie klient otrzymuje dużą liczbę komunikatów o stanie HTTP "limit czasu operacji 500" z operacji magazynu.

> [!NOTE]
> Błędy przekroczenia limitu czasu można zobaczyć tymczasowo, ponieważ usługa magazynu równoważy żądanie, przenosząc partycję na nowy serwer.
>
>

Metryka **wzrost percenttimeouterror** jest agregacją następujących metryk: **ClientTimeoutError**, **AnonymousClientTimeoutError**, **SASClientTimeoutError**, **ServerTimeoutError**, **AnonymousServerTimeoutError** i **SASServerTimeoutError**.

Limity czasu serwera są spowodowane błędem na serwerze. Limity czasu klienta zachodzą, ponieważ operacja na serwerze przekroczyła limit czasu określony przez klienta; na przykład klient korzystający z biblioteki klienta magazynu może ustawić limit czasu dla operacji za pomocą właściwości **ServerTimeout** klasy **QueueRequestOptions** .

Limity czasu serwera wskazują na problem z usługą magazynu, która wymaga dalszych badań. Metryk można użyć, aby sprawdzić, czy obowiązują limity skalowalności dla usługi i zidentyfikować wszelkie skoki w ruchu, który może powodować występowanie tego problemu. Jeśli problem występuje sporadycznie, może to być spowodowane działaniem równoważenia obciążenia w usłudze. Jeśli problem jest trwały i nie jest spowodowany przez aplikację, która osiąga limity skalowalności usługi, należy zgłosić problem z pomocą techniczną. W przypadku przekroczenia limitu czasu klienta należy zdecydować, czy limit czasu jest ustawiony na odpowiednią wartość w kliencie, i zmienić wartość limitu czasu ustawioną w kliencie lub zbadać, jak można poprawić wydajność operacji w usłudze magazynu, na przykład przez optymalizację zapytań tabel lub zmniejszenie rozmiaru komunikatów.

### <a name="metrics-show-an-increase-in-percentnetworkerror"></a><a name="metrics-show-an-increase-in-PercentNetworkError"></a>Metryki wskazują wzrost wartości PercentNetworkError
Twoje metryki pokazują wzrost **wzrost percentnetworkerror** dla jednej z usług magazynu. Metryka **wzrost percentnetworkerror** jest agregacją następujących metryk: **NetworkError**, **AnonymousNetworkError** i **SASNetworkError**. Są one wykonywane, gdy usługa magazynu wykryje błąd sieciowy, gdy klient wysyła żądanie magazynu.

Najbardziej typową przyczyną tego błędu jest odłączenie klienta przed upływem limitu czasu w usłudze Storage. Zbadaj kod w kliencie, aby zrozumieć, dlaczego i kiedy klient rozłącza się z usługą magazynu. Aby zbadać problemy z łącznością sieciową z poziomu klienta, można również użyć programu Wireshark lub Tcping. Te narzędzia są opisane w [dodatkach].

### <a name="the-client-is-receiving-http-403-forbidden-messages"></a><a name="the-client-is-receiving-403-messages"></a>Klient odbiera komunikaty HTTP 403 (zabronione)
Jeśli aplikacja kliencka zgłasza błędy HTTP 403 (zabronione), prawdopodobną przyczyną jest to, że klient używa wygasłej sygnatury dostępu współdzielonego podczas wysyłania żądania magazynu (chociaż inne możliwe przyczyny to niedokładność zegara, nieprawidłowe klucze i puste nagłówki). Jeśli przyczyną jest wygasły klucz sygnatury dostępu współdzielonego, nie będą widoczne żadne wpisy w danych dziennika rejestrowania danych magazynu po stronie serwera. W poniższej tabeli przedstawiono przykład z dziennika po stronie klienta wygenerowanego przez bibliotekę klienta usługi Storage, która ilustruje ten problem:

| Element źródłowy | Szczegółowość | Szczegółowość | Identyfikator żądania klienta | Tekst operacji |
| --- | --- | --- | --- | --- |
| Microsoft. Azure. Storage |Informacje |3 |85d077ab-... |Rozpoczynanie operacji przy użyciu lokalizacji podstawowej dla trybu lokalizacji PrimaryOnly. |
| Microsoft. Azure. Storage |Informacje |3 |85d077ab-... |Uruchamianie żądania synchronicznego do <https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/Synchronous_and_Asynchronous_Requests#Synchronous_request> |
| Microsoft. Azure. Storage |Informacje |3 |85d077ab-... |Oczekiwanie na odpowiedź. |
| Microsoft. Azure. Storage |Ostrzeżenie |2 |85d077ab-... |Zgłoszono wyjątek podczas oczekiwania na odpowiedź: serwer zdalny zwrócił błąd: (403) zabronione. |
| Microsoft. Azure. Storage |Informacje |3 |85d077ab-... |Odebrano odpowiedź. Kod stanu = 403, identyfikator żądania = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, Content-MD5 =, ETag =. |
| Microsoft. Azure. Storage |Ostrzeżenie |2 |85d077ab-... |Zgłoszono wyjątek podczas operacji: serwer zdalny zwrócił błąd: (403) zabronione.. |
| Microsoft. Azure. Storage |Informacje |3 |85d077ab-... |Sprawdzanie, czy operacja powinna być ponowiona. Liczba ponownych prób = 0, kod stanu HTTP = 403, wyjątek = serwer zdalny zwrócił błąd: (403) jest zabroniony. |
| Microsoft. Azure. Storage |Informacje |3 |85d077ab-... |W następnej lokalizacji ustawiono wartość podstawowa, na podstawie trybu lokalizacji. |
| Microsoft. Azure. Storage |Błąd |1 |85d077ab-... |Zasady ponawiania nie umożliwiały ponowienia próby. Niepowodzenie z serwerem zdalnym zwróciło błąd: (403) zabronione. |

W tym scenariuszu należy zbadać, dlaczego token sygnatury dostępu współdzielonego upływa przed wysłaniem przez klienta tokenu do serwera:

* Zazwyczaj nie należy ustawiać czasu rozpoczęcia, tworząc sygnaturę dostępu współdzielonego dla klienta przeznaczoną do natychmiastowego użycia. Jeśli istnieją małe różnice zegara między hostem generującym sygnaturę dostępu współdzielonego przy użyciu bieżącej godziny a usługą magazynu jest możliwe, że usługa magazynu otrzyma sygnaturę dostępu współdzielonego, która nie jest jeszcze ważna.
* Nie należy ustawiać bardzo krótkiego czasu wygaśnięcia w sygnaturze dostępu współdzielonego. W tym przypadku niewielkie różnice zegara między hostem generującym sygnaturę dostępu współdzielonego a usługą magazynu mogą prowadzić do tego, że sygnatura dostępu współdzielonego wygasa wcześniej, niż zakładano.
* Czy parametr Version w kluczu sygnatury dostępu współdzielonego (na przykład **OHR = 2015-04-05**) jest zgodny z wersją używanej biblioteki klienta magazynu? Zalecamy, aby zawsze używać najnowszej wersji [biblioteki klienta magazynu](https://www.nuget.org/packages/WindowsAzure.Storage/).
* Jeśli ponownie wygenerujesz klucze dostępu do magazynu, wszelkie istniejące tokeny SAS mogą zostać unieważnione. Ten problem może wystąpić, jeśli generujesz tokeny SAS z długim czasem wygaśnięcia dla aplikacji klienckich do przechowywania w pamięci podręcznej.

Jeśli używasz biblioteki klienta usługi magazynu do generowania tokenów SAS, utworzenie prawidłowego tokenu jest łatwe. Jeśli jednak korzystasz z interfejsu API REST magazynu i ręcznie konstruujesz tokeny SAS, zobacz [delegowanie dostępu przy użyciu sygnatury dostępu współdzielonego](/rest/api/storageservices/delegate-access-with-shared-access-signature).

### <a name="the-client-is-receiving-http-404-not-found-messages"></a><a name="the-client-is-receiving-404-messages"></a>Klient odbiera komunikaty HTTP 404 (nie znaleziono)
Jeśli aplikacja kliencka odbiera komunikat HTTP 404 (nie znaleziono) z serwera, oznacza to, że obiekt, którego klient próbował użyć (np. jednostka, tabela, obiekt blob, kontener lub kolejka) nie istnieje w usłudze magazynu. Istnieje kilka możliwych przyczyn tej sytuacji, takich jak:

* [Klient lub inny proces wcześniej usunął obiekt]
* [Problem z autoryzacją sygnatury dostępu współdzielonego]
* [Kod JavaScript po stronie klienta nie ma uprawnień dostępu do obiektu]
* [Błąd sieci]

#### <a name="the-client-or-another-process-previously-deleted-the-object"></a><a name="client-previously-deleted-the-object"></a>Klient lub inny proces usunął wcześniej obiekt
W scenariuszach, w których klient próbuje odczytywać, aktualizować lub usuwać dane w usłudze Storage, zwykle jest to łatwe do zidentyfikowania w dziennikach po stronie serwera poprzedniej operacji, która usunęła dany obiekt z usługi magazynu. Często dane dziennika pokazują, że inny użytkownik lub proces usunął obiekt. W dzienniku rejestrowania magazynu po stronie serwera, kolumny Typ operacji i żądany-obiekt-klucz są wyświetlane, gdy klient usunął obiekt.

W scenariuszu, w którym klient próbuje wstawić obiekt, może nie być od razu oczywisty powód, dla którego wynikiem jest odpowiedź HTTP 404 (nie znaleziono), ponieważ klient tworzy nowy obiekt. Jeśli jednak klient tworzy obiekt BLOB, musi mieć możliwość znalezienia kontenera obiektów blob, jeśli klient tworzy komunikat, musi mieć możliwość znalezienia kolejki, a jeśli klient dodaje wiersz, musi być w stanie znaleźć tabelę.

Można użyć dziennika po stronie klienta z biblioteki klienta usługi Storage, aby uzyskać bardziej szczegółowe informacje o tym, kiedy klient wysyła określone żądania do usługi Storage.

Następujący dziennik po stronie klienta wygenerowany przez bibliotekę klienta magazynu ilustruje problem, gdy klient nie może znaleźć kontenera dla tworzonego obiektu BLOB. Ten dziennik zawiera szczegółowe informacje o następujących operacjach magazynu:

| Identyfikator żądania | Operacja |
| --- | --- |
| 07b26a5d-... |**DeleteIfExists** metoda usuwania kontenera obiektów BLOB. Należy zauważyć, że ta operacja obejmuje żądanie **Główne** do sprawdzenia istnienia kontenera. |
| e2d06d78... |**Metodę createifnotexists** metoda tworzenia kontenera obiektów BLOB. Należy zauważyć, że ta operacja obejmuje żądanie **Główne** , które sprawdza obecność kontenera. **Nagłówek** zwraca komunikat 404, ale kontynuuje działanie. |
| de8b1c3c-... |**UploadFromStream** metoda tworzenia obiektu BLOB. Żądanie **Put** kończy się niepowodzeniem z komunikatem 404 |

Wpisy dziennika:

| Identyfikator żądania | Tekst operacji |
| --- | --- |
| 07b26a5d-... |Uruchamianie żądania synchronicznego do `https://domemaildist.blob.core.windows.net/azuremmblobcontainer` . |
| 07b26a5d-... |StringToSign = główna................ x-MS-Client-Request-ID: 07b26a5d-.... x-MS-Date: WT, 03 Jun 2014 10:33:11 GMT. x-MS-Version: 2014-02-14./domemaildist/azuremmblobcontainer. restype: Container. |
| 07b26a5d-... |Oczekiwanie na odpowiedź. |
| 07b26a5d-... |Odebrano odpowiedź. Kod stanu = 200, identyfikator żądania = eeead849-... Content-MD5 =, ETag =    &quot; 0x8D14D2DC63D059B &quot; . |
| 07b26a5d-... |Nagłówki odpowiedzi zostały pomyślnie przetworzone, podczas gdy pozostała część operacji. |
| 07b26a5d-... |Pobieranie treści odpowiedzi. |
| 07b26a5d-... |Operacja została ukończona pomyślnie. |
| 07b26a5d-... |Uruchamianie żądania synchronicznego do `https://domemaildist.blob.core.windows.net/azuremmblobcontainer` . |
| 07b26a5d-... |StringToSign = Usuń............. x-MS-Client-Request-ID: 07b26a5d-.... x-MS-Date: WT, 03 Jun 2014 10:33:12 GMT. x-MS-Version: 2014-02-14./domemaildist/azuremmblobcontainer. restype: Container. |
| 07b26a5d-... |Oczekiwanie na odpowiedź. |
| 07b26a5d-... |Odebrano odpowiedź. Kod stanu = 202, identyfikator żądania = 6ab2a4cf-..., Content-MD5 =, ETag =. |
| 07b26a5d-... |Nagłówki odpowiedzi zostały pomyślnie przetworzone, podczas gdy pozostała część operacji. |
| 07b26a5d-... |Pobieranie treści odpowiedzi. |
| 07b26a5d-... |Operacja została ukończona pomyślnie. |
| e2d06d78-... |Uruchamianie żądania asynchronicznego do `https://domemaildist.blob.core.windows.net/azuremmblobcontainer` .</td> |
| e2d06d78-... |StringToSign = główna................ x-MS-Client-Request-ID: e2d06d78-.... x-MS-Date: WT, 03 Jun 2014 10:33:12 GMT. x-MS-Version: 2014-02-14./domemaildist/azuremmblobcontainer. restype: Container. |
| e2d06d78-... |Oczekiwanie na odpowiedź. |
| de8b1c3c-... |Uruchamianie żądania synchronicznego do `https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt` . |
| de8b1c3c-... |StringToSign = PUT... 64. qCmF + TQLPhq/YYK50mP9ZQ = =......... x-MS-BLOB-Type: BlockBlob. x-MS-Client-Request-ID: de8b1c3c-.... x-MS-Date: WT, 03 Jun 2014 10:33:12 GMT. x-MS-Version: 2014-02-14./domemaildist/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |Przygotowywanie do zapisu danych żądania. |
| e2d06d78-... |Zgłoszono wyjątek podczas oczekiwania na odpowiedź: serwer zdalny zwrócił błąd: nie znaleziono (404). |
| e2d06d78-... |Odebrano odpowiedź. Kod stanu = 404, identyfikator żądania = 353ae3bc-..., Content-MD5 =, ETag =. |
| e2d06d78-... |Nagłówki odpowiedzi zostały pomyślnie przetworzone, podczas gdy pozostała część operacji. |
| e2d06d78-... |Pobieranie treści odpowiedzi. |
| e2d06d78-... |Operacja została ukończona pomyślnie. |
| e2d06d78-... |Uruchamianie żądania asynchronicznego do `https://domemaildist.blob.core.windows.net/azuremmblobcontainer` . |
| e2d06d78-... |StringToSign = PUT... 0....... x-MS-Client-Request-ID: e2d06d78-.... x-MS-Date: WT, 03 Jun 2014 10:33:12 GMT. x-MS-Version: 2014-02-14./domemaildist/azuremmblobcontainer. restype: Container. |
| e2d06d78-... |Oczekiwanie na odpowiedź. |
| de8b1c3c-... |Zapisywanie danych żądania. |
| de8b1c3c-... |Oczekiwanie na odpowiedź. |
| e2d06d78-... |Zgłoszono wyjątek podczas oczekiwania na odpowiedź: serwer zdalny zwrócił błąd: (409) konflikt.. |
| e2d06d78-... |Odebrano odpowiedź. Kod stanu = 409, identyfikator żądania = c27da20e-..., Content-MD5 =, ETag =. |
| e2d06d78-... |Pobieranie treści odpowiedzi na błędy. |
| de8b1c3c-... |Zgłoszono wyjątek podczas oczekiwania na odpowiedź: serwer zdalny zwrócił błąd: nie znaleziono (404). |
| de8b1c3c-... |Odebrano odpowiedź. Kod stanu = 404, identyfikator żądania = 0eaeab3e-..., Content-MD5 =, ETag =. |
| de8b1c3c-... |Zgłoszono wyjątek podczas operacji: serwer zdalny zwrócił błąd: nie znaleziono (404). |
| de8b1c3c-... |Zasady ponawiania nie umożliwiały ponowienia próby. Niepowodzenie z serwerem zdalnym zwróciło błąd: nie znaleziono (404). |
| e2d06d78-... |Zasady ponawiania nie umożliwiały ponowienia próby. Niepowodzenie z serwerem zdalnym zwróciło błąd: (409) konflikt.. |

W tym przykładzie dziennik pokazuje, że klient nie opuszcza żądań z metody **metodę createifnotexists** (Identyfikator żądania e2d06d78...) z żądaniami z metody **UploadFromStream** (de8b1c3c-...). Ten przeplot jest spowodowany tym, że aplikacja kliencka wywołuje te metody asynchronicznie. Zmodyfikuj kod asynchroniczny w kliencie, aby upewnić się, że tworzy kontener przed podjęciem próby przekazania jakichkolwiek danych do obiektu BLOB w tym kontenerze. Najlepiej utworzyć wszystkie kontenery z wyprzedzeniem.

#### <a name="a-shared-access-signature-sas-authorization-issue"></a><a name="SAS-authorization-issue"></a>Problem z autoryzacją sygnatury dostępu współdzielonego (SAS)
Jeśli aplikacja kliencka próbuje użyć klucza sygnatury dostępu współdzielonego, który nie zawiera wystarczających uprawnień do operacji, usługa magazynu zwróci komunikat HTTP 404 (nie znaleziono) do klienta. W tym samym czasie zostanie również wyświetlona wartość różna od zera dla **SASAuthorizationError** w metrykach.

W poniższej tabeli przedstawiono przykładowy komunikat dziennika po stronie serwera z pliku dziennika rejestrowania magazynu:

| Nazwa | Wartość |
| --- | --- |
| Czas rozpoczęcia żądania | 2014-05-30T06:17:48.4473697 Z |
| Typ operacji     | GetBlobProperties            |
| Stan żądania     | SASAuthorizationError        |
| Kod stanu HTTP   | 404                            |
| Typ uwierzytelniania| Sygnatur                          |
| Typ usługi       | Obiekt blob                         |
| Adres URL żądania         | `https://domemaildist.blob.core.windows.net/azureimblobcontainer/blobCreatedViaSAS.txt` |
| &nbsp;                 |   ? OHR = 2014-02-14&SR = c&si = moje zasady&SIG = XXXXX &; API-Version = 2014-02-14 |
| Nagłówek identyfikatora żądania  | a1f348d5-8032-4912-93ef-b393e5252a3b |
| Identyfikator żądania klienta  | 2d064953-8436-4ee0-aa0c-65cb874f7929 |


Sprawdź, Dlaczego aplikacja kliencka próbuje wykonać operację, do której nie udzielono uprawnień.

#### <a name="client-side-javascript-code-does-not-have-permission-to-access-the-object"></a><a name="JavaScript-code-does-not-have-permission"></a>Kod JavaScript po stronie klienta nie ma uprawnień dostępu do obiektu
Jeśli używasz klienta JavaScript, a usługa magazynu zwraca komunikaty HTTP 404, sprawdź następujące błędy języka JavaScript w przeglądarce:

```
SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.
```

> [!NOTE]
> Możesz użyć Narzędzia deweloperskie F12 w programie Internet Explorer, aby śledzić komunikaty wymieniane między przeglądarką a usługą magazynu w przypadku rozwiązywania problemów z JavaScript po stronie klienta.
>
>

Te błędy występują, ponieważ przeglądarka sieci Web implementuje te same ograniczenia zabezpieczeń [zasad pochodzenia](https://www.w3.org/Security/wiki/Same_Origin_Policy) , które uniemożliwiają wywoływanie przez stronę sieci Web interfejsu API w innej domenie z domeny, z której pochodzi strona.

Aby obejść problem z JavaScript, można skonfigurować udostępnianie zasobów między źródłami (CORS) dla usługi magazynu, do której uzyskuje dostęp klient. Aby uzyskać więcej informacji, zobacz [Obsługa udostępniania zasobów między źródłami (CORS) dla usług Azure Storage](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services).

Poniższy przykład kodu pokazuje, jak skonfigurować usługę BLOB Service, aby zezwolić JavaScript działaniu w domenie contoso na dostęp do obiektu BLOB w usłudze BLOB Storage:

# <a name="net-v12"></a>[V12 .NET](#tab/dotnet)

:::code language="csharp" source="~/azure-storage-snippets/blobs/howto/dotnet/dotnet-v12/Monitoring.cs" id="Snippet_ConfigureCORS":::

# <a name="net-v11"></a>[V11 .NET](#tab/dotnet11)

```csharp
CloudBlobClient client = new CloudBlobClient(blobEndpoint, new StorageCredentials(accountName, accountKey));
// Set the service properties.
ServiceProperties sp = client.GetServiceProperties();
sp.DefaultServiceVersion = "2013-08-15";
CorsRule cr = new CorsRule();
cr.AllowedHeaders.Add("*");
cr.AllowedMethods = CorsHttpMethods.Get | CorsHttpMethods.Put;
cr.AllowedOrigins.Add("http://www.contoso.com");
cr.ExposedHeaders.Add("x-ms-*");
cr.MaxAgeInSeconds = 5;
sp.Cors.CorsRules.Clear();
sp.Cors.CorsRules.Add(cr);
client.SetServiceProperties(sp);
```

---

#### <a name="network-failure"></a><a name="network-failure"></a>Awaria sieci
W pewnych okolicznościach utracone pakiety sieciowe mogą prowadzić do usługi magazynu zwracającej komunikaty HTTP 404 do klienta. Na przykład jeśli aplikacja kliencka usuwa jednostkę z usługi Table Service, zobaczysz komunikat o stanie "HTTP 404 (nie znaleziono)" w usłudze Table Service. Gdy przebadasz tabelę w usłudze Table Storage, zobaczysz, że usługa usunąła jednostkę zgodnie z żądaniem.

Szczegóły wyjątku w kliencie obejmują identyfikator żądania (7e84f12d...) przypisany przez usługę tabeli dla żądania: można użyć tych informacji, aby zlokalizować szczegóły żądania w dziennikach magazynu po stronie serwera, wyszukując w kolumnie **żądanie-ID-nagłówka** w pliku dziennika. Można również użyć metryk, aby określić, kiedy wystąpią błędy, a następnie przeszukać pliki dziennika na podstawie czasu, w którym metryki zarejestrowali ten błąd. Ten wpis dziennika pokazuje, że usunięcie nie powiodło się z komunikatem o stanie "HTTP (404) inny błąd". Ten sam wpis dziennika zawiera również identyfikator żądania wygenerowany przez klienta w kolumnie **Client-Request-ID** (813ea74f...).

Dziennik po stronie serwera zawiera również inny wpis z tą samą wartością **Client-Request-ID** (813ea74f...) dla pomyślnego usunięcia operacji usuwania dla tej samej jednostki oraz z tego samego klienta. Ta operacja usuwania została wykonana bardzo krótko przed żądaniem usunięcia zakończonego niepowodzeniem.

Najbardziej prawdopodobną przyczyną tego scenariusza jest to, że klient wysłał żądanie usunięcia dla jednostki do usługi Table Service, która zakończyła się powodzeniem, ale nie otrzymała potwierdzenia z serwera (prawdopodobnie z powodu tymczasowego problemu z siecią). Klient automatycznie ponowi próbę wykonania operacji (przy użyciu tego samego **identyfikatora Client-Request-ID**) i nie powiodła się, ponieważ jednostka została już usunięta.

Jeśli ten problem występuje często, należy zbadać, dlaczego klient nie otrzymuje potwierdzeń z usługi Table Service. Jeśli problem występuje sporadycznie, należy zastosować pułapki "HTTP (404) nie znaleziono" i zalogować się na kliencie, ale zezwolić na kontynuowanie działania klienta.

### <a name="the-client-is-receiving-http-409-conflict-messages"></a><a name="the-client-is-receiving-409-messages"></a>Klient otrzymuje komunikaty HTTP 409 (konflikt)
W poniższej tabeli przedstawiono wyodrębnienie z dziennika po stronie serwera dla dwóch operacji klienta: **DeleteIfExists** , a następnie bezpośrednio przez **metodę createifnotexists** przy użyciu tej samej nazwy kontenera obiektów BLOB. Każda operacja klienta powoduje wysłanie dwóch żądań wysyłanych do serwera, najpierw żądania **GetContainerProperties** , aby sprawdzić, czy kontener istnieje, a następnie żądania DeleteContainer **lub** . 

| Znacznik czasu | Operacja | Wynik | Nazwa kontenera | Identyfikator żądania klienta |
| --- | --- | --- | --- | --- |
| 05:13.7167225 |GetContainerProperties |200 |mmcont |c9f52c89-... |
| 05:13.8167325 |DeleteContainer |202 |mmcont |c9f52c89-... |
| 05:13.8987407 |GetContainerProperties |404 |mmcont |bc881924-... |
| 05:14.2147723 |CreateContainer |409 |mmcont |bc881924-... |

Kod w aplikacji klienckiej usuwa, a następnie natychmiast ponownie tworzy kontener obiektów BLOB przy użyciu tej samej nazwy: Metoda **metodę createifnotexists** (Identyfikator żądania klienta bc881924-...) kończy się niepowodzeniem z powodu błędu HTTP 409 (konflikt). Kiedy klient usuwa kontenery obiektów blob, tabele lub kolejki, musi upłynąć pewien czas, zanim nazwa stanie się ponownie dostępna.

Aplikacja kliencka powinna używać unikatowych nazw kontenerów za każdym razem, gdy tworzy nowe kontenery, jeśli wzorzec usuń/utwórz ponownie jest typowy.

### <a name="metrics-show-low-percentsuccess-or-analytics-log-entries-have-operations-with-transaction-status-of-clientothererrors"></a><a name="metrics-show-low-percent-success"></a>Metryki pokazujące niskie PercentSuccess lub wpisy dziennika analizy zawierają operacje ze stanem transakcji ClientOtherErrors
Metryka **PercentSuccess** przechwytuje procent operacji zakończonych powodzeniem na podstawie ich kodu stanu HTTP. Operacje o kodach stanu 2XX są jak pomyślne, natomiast operacje o kodach stanu w 3XX, 4XX i 5XX zakresy są liczone jako niepowodzenie i niższe wartości metryki **PercentSuccess** . W plikach dziennika magazynu po stronie serwera te operacje są rejestrowane ze stanem transakcji **ClientOtherErrors**.

Należy pamiętać, że te operacje zostały wykonane pomyślnie i dlatego nie wpływają na inne metryki, takie jak dostępność. Niektóre przykłady operacji, które zostały wykonane pomyślnie, ale mogą spowodować niepowodzenie kodów stanu HTTP:

* **ResourceNotFound** (nie znaleziono 404), na przykład z żądania GET do obiektu BLOB, który nie istnieje.
* **ResourceAlreadyExists** (konflikt 409), na przykład z operacji **CreateIfNotExist** , w której zasób już istnieje.
* **ConditionNotMet** (niezmodyfikowana 304), na przykład z operacji warunkowej, takiej jak gdy klient wysyła wartość **ETag** i nagłówek HTTP **If-None-Match** , aby zażądać obrazu tylko wtedy, gdy został on zaktualizowany od czasu ostatniej operacji.

Można znaleźć listę typowych kodów błędów interfejsu API REST, które zwracają usługi magazynu na stronie [kody błędów interfejsu API REST](/rest/api/storageservices/Common-REST-API-Error-Codes).

### <a name="capacity-metrics-show-an-unexpected-increase-in-storage-capacity-usage"></a><a name="capacity-metrics-show-an-unexpected-increase"></a>Metryki pojemności pokazują nieoczekiwany wzrost użycia pojemności magazynu
Jeśli widzisz nagłe, nieoczekiwane zmiany zużycia pojemności na koncie magazynu, możesz zbadać przyczyny, najpierw sprawdzając metryki dostępności. na przykład wzrost liczby żądań usunięcia zakończonych niepowodzeniem może prowadzić do zwiększenia ilości magazynu obiektów blob, który jest używany jako operacje oczyszczania specyficzne dla aplikacji, może się zdarzyć, że nie będzie działać w oczekiwany sposób (na przykład ponieważ tokeny sygnatury dostępu współdzielonego użyte do zwolnienia miejsca wygasły).

### <a name="your-issue-arises-from-using-the-storage-emulator-for-development-or-test"></a><a name="your-issue-arises-from-using-the-storage-emulator"></a>Problem wynika z używania emulatora magazynu na potrzeby tworzenia i testowania
Emulator magazynu jest zazwyczaj używany podczas tworzenia i testowania, aby uniknąć konieczności korzystania z konta usługi Azure Storage. Typowe problemy, które mogą wystąpić podczas korzystania z emulatora magazynu, to:

* [Funkcja "X" nie działa w emulatorze magazynu]
* [Błąd "wartość jednego z nagłówków HTTP jest w niepoprawnym formacie" podczas korzystania z emulatora magazynu]
* [Uruchamianie emulatora magazynu wymaga uprawnień administracyjnych]

#### <a name="feature-x-is-not-working-in-the-storage-emulator"></a><a name="feature-X-is-not-working"></a>Funkcja "X" nie działa w emulatorze magazynu
Emulator magazynu nie obsługuje wszystkich funkcji usług Azure Storage, takich jak usługa plików. Więcej informacji można znaleźć w temacie [Use the Azure Storage Emulator for Development and Testing](storage-use-emulator.md) (Używanie emulatora usługi Azure Storage do programowania i testowania).

Dla tych funkcji, które nie są obsługiwane przez emulator magazynu, Użyj usługi Azure Storage w chmurze.

#### <a name="error-the-value-for-one-of-the-http-headers-is-not-in-the-correct-format-when-using-the-storage-emulator"></a><a name="error-HTTP-header-not-correct-format"></a>Błąd "wartość jednego z nagłówków HTTP jest w niepoprawnym formacie" podczas korzystania z emulatora magazynu
Testujesz aplikację, która korzysta z biblioteki klienta usługi Storage w odniesieniu do lokalnego emulatora magazynu i wywołań metod, takich jak **metodę createifnotexists** , nie powiedzie się z komunikatem o błędzie "wartość dla jednego z nagłówków HTTP ma niepoprawny format". Oznacza to, że używana wersja emulatora magazynu nie obsługuje używanej wersji biblioteki klienta magazynu. Biblioteka klienta magazynu dodaje nagłówek **x-MS-Version** do wszystkich żądań, które wprowadza. Jeśli emulator magazynu nie rozpoznaje wartości w nagłówku **x-MS-Version** , odrzuca żądanie.

Możesz użyć dzienników klienta biblioteki magazynów, aby wyświetlić wartość **nagłówka x-MS-Version** , który jest wysyłany. Możesz również zobaczyć wartość **nagłówka x-MS-Version** , jeśli używasz programu Fiddler do śledzenia żądań z aplikacji klienckiej.

Ten scenariusz zwykle występuje w przypadku instalowania i używania najnowszej wersji biblioteki klienta magazynu bez aktualizowania emulatora magazynu. Należy zainstalować najnowszą wersję emulatora magazynu lub użyć magazynu w chmurze, a nie emulatora do tworzenia i testowania.

#### <a name="running-the-storage-emulator-requires-administrative-privileges"></a><a name="storage-emulator-requires-administrative-privileges"></a>Uruchamianie emulatora magazynu wymaga uprawnień administracyjnych
Po uruchomieniu emulatora magazynu zostanie wyświetlony monit o podanie poświadczeń administratora. Dzieje się to tylko w przypadku inicjowania emulatora magazynu po raz pierwszy. Po zainicjowaniu emulatora magazynu nie trzeba mieć uprawnień administracyjnych, aby uruchomić je ponownie.

Więcej informacji można znaleźć w temacie [Use the Azure Storage Emulator for Development and Testing](storage-use-emulator.md) (Używanie emulatora usługi Azure Storage do programowania i testowania). Możesz również zainicjować emulator magazynu w programie Visual Studio, który również będzie wymagał uprawnień administracyjnych.

### <a name="you-are-encountering-problems-installing-the-azure-sdk-for-net"></a><a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>Występują problemy z instalacją zestawu Azure SDK dla platformy .NET
Podczas próby zainstalowania zestawu SDK próba instalacji emulatora magazynu na komputerze lokalnym kończy się niepowodzeniem. Dziennik instalacji zawiera jeden z następujących komunikatów:

* CAQuietExec: błąd: nie można uzyskać dostępu do wystąpienia SQL
* CAQuietExec: błąd: nie można utworzyć bazy danych

Przyczyną jest problem z istniejącą instalacją LocalDB. Domyślnie emulator magazynu używa LocalDB do utrwalania danych, gdy symuluje usługi Azure Storage. Możesz zresetować wystąpienie LocalDB, uruchamiając następujące polecenia w oknie wiersza polecenia przed podjęciem próby zainstalowania zestawu SDK.

```
sqllocaldb stop v11.0
sqllocaldb delete v11.0
delete %USERPROFILE%\WAStorageEmulatorDb3*.*
sqllocaldb create v11.0
```

Polecenie **Usuń** usuwa wszystkie stare pliki bazy danych z poprzednich instalacji emulatora magazynu.

### <a name="you-have-a-different-issue-with-a-storage-service"></a><a name="you-have-a-different-issue-with-a-storage-service"></a>Istnieje inny problem dotyczący usługi magazynu
Jeśli poprzednie sekcje rozwiązywania problemów nie obejmują problemu związanego z usługą magazynu, należy zastosować następujące podejście do diagnozowania i rozwiązywania problemów.

* Sprawdź metryki, aby sprawdzić, czy istnieją zmiany od oczekiwanego zachowania w wierszu podstawowym. Z poziomu metryk można określić, czy problem ma charakter przejściowy, czy trwały, oraz na które operacje magazynu wpływają na ten problem.
* Korzystając z informacji o metrykach, można przeszukiwać dane dziennika po stronie serwera, aby uzyskać bardziej szczegółowe informacje o błędach, które występują. Te informacje mogą pomóc w rozwiązywaniu problemów i rozwiązaniu problemu.
* Jeśli informacje w dziennikach po stronie serwera nie wystarczają do pomyślnego rozwiązania problemu, można użyć dzienników po stronie klienta biblioteki klienta magazynu, aby zbadać zachowanie aplikacji klienckiej, a także narzędzia, takie jak programu Fiddler, Wireshark, aby zbadać sieć.

Aby uzyskać więcej informacji na temat korzystania z programu programu Fiddler, zobacz "[dodatek 1: używanie programu Fiddler do przechwytywania ruchu HTTP i https]".

Aby uzyskać więcej informacji na temat korzystania z programu Wireshark, zobacz "[dodatek 2: korzystanie z programu Wireshark do przechwytywania ruchu sieciowego]".

## <a name="appendices"></a><a name="appendices"></a>Dodatki
W dodatkach opisano kilka narzędzi, które mogą być przydatne podczas diagnozowania i rozwiązywania problemów z usługą Azure Storage (i innymi usługami). Te narzędzia nie są częścią usługi Azure Storage, a niektóre z nich są produktami innych firm. W związku z tym narzędzia omówione w tych dodatkach nie są objęte żadną umową pomocy technicznej, którą może mieć usługa Microsoft Azure lub Azure Storage, a tym samym w ramach procesu oceny należy zapoznać się z opcjami licencjonowania i pomocy technicznej dostępnymi u dostawców tych narzędzi.

### <a name="appendix-1-using-fiddler-to-capture-http-and-https-traffic"></a><a name="appendix-1"></a>Dodatek 1: używanie programu Fiddler do przechwytywania ruchu HTTP i HTTPS
[Programu Fiddler](https://www.telerik.com/fiddler) to przydatne narzędzie do analizowania ruchu HTTP i HTTPS między aplikacją kliencką a usługą Azure Storage, z której korzystasz.

> [!NOTE]
> Programu Fiddler może zdekodować ruch HTTPS; należy uważnie zapoznać się z dokumentacją programu Fiddler, aby zrozumieć, jak to robi, i poznać implikacje zabezpieczeń.
>
>

Ten dodatek zawiera krótki przewodnik konfigurowania programu Fiddler do przechwytywania ruchu między komputerem lokalnym, na którym zainstalowano programu Fiddler i usługi Azure Storage.

Po uruchomieniu programu programu Fiddler rozpocznie się przechwytywanie ruchu HTTP i HTTPS na komputerze lokalnym. Oto kilka przydatnych poleceń do kontrolowania programu Fiddler:

* Zatrzymywanie i uruchamianie przechwytywania ruchu. W menu głównym przejdź do **pliku** , a następnie kliknij pozycję **Przechwyć ruch** , aby włączyć i wyłączyć przechwytywanie.
* Zapisz przechwycone dane ruchu. W menu głównym przejdź do **pliku**, kliknij pozycję **Zapisz**, a następnie kliknij pozycję **wszystkie sesje**: umożliwia to zapisanie ruchu w pliku archiwum sesji. Można ponownie załadować archiwum sesji w celu przeprowadzenia analizy lub wysłać je w razie potrzeby do pomocy technicznej firmy Microsoft.

Aby ograniczyć ilość ruchu przechwytywanego przez programu Fiddler, można użyć filtrów skonfigurowanych na karcie **filtry** . Poniższy zrzut ekranu przedstawia filtr, który przechwytuje tylko ruch wysyłany do punktu końcowego magazynu **contosoemaildist.Table.Core.Windows.NET** :

![Zrzut ekranu pokazujący filtr, który przechwytuje tylko ruch wysyłany do punktu końcowego magazynu contosoemaildist.table.core.windows.net.][5]

### <a name="appendix-2-using-wireshark-to-capture-network-traffic"></a><a name="appendix-2"></a>Dodatek 2. Korzystanie z programu Wireshark do przechwytywania ruchu sieciowego
[Wireshark](https://www.wireshark.org/) to Analizator protokołów sieciowych, który umożliwia wyświetlanie szczegółowych informacji o pakiecie dla szerokiego zakresu protokołów sieciowych.

Poniższa procedura przedstawia sposób przechwytywania szczegółowych informacji o pakiecie dla ruchu z komputera lokalnego, na którym zainstalowano serwis Wireshark, do usługi Table Service na koncie magazynu platformy Azure.

1. Uruchom aplikację Wireshark na komputerze lokalnym.
2. W sekcji **Start** wybierz lokalny interfejs sieciowy lub interfejsy, które są połączone z Internetem.
3. Kliknij pozycję **Opcje przechwytywania**.
4. Dodaj filtr do pola tekstowego **Filtr przechwytywania** . Na przykład **host contosoemaildist.Table.Core.Windows.NET** skonfiguruje program Wireshark do przechwytywania tylko pakietów wysyłanych do lub z punktu końcowego usługi Table Service na koncie magazynu **contosoemaildist** . Zapoznaj się z [pełną listą filtrów przechwytywania](https://wiki.wireshark.org/CaptureFilters).

   ![Zrzut ekranu, który pokazuje, jak dodać filtr do pola tekstowego filtr przechwytywania.][6]
5. Kliknij przycisk **Uruchom**. Program Wireshark przechwytuje teraz wszystkie pakiety wysyłane do lub z punktu końcowego usługi Table Service podczas korzystania z aplikacji klienckiej na komputerze lokalnym.
6. Po zakończeniu w menu głównym kliknij polecenie **Przechwytuj** , a następnie **Zatrzymaj**.
7. Aby zapisać przechwycone dane w pliku przechwytywania programu Wireshark, w menu głównym kliknij **plik** , a następnie **Zapisz**.

W programie WireShark zostaną wyróżnione wszystkie błędy, które istnieją w oknie **packetlist** . Możesz również użyć okna **informacje specjalistyczne** ( **Analizuj**, a następnie uzyskać **Informacje o ekspertu**), aby wyświetlić podsumowanie błędów i ostrzeżeń.

![Zrzut ekranu pokazujący okno informacji o ekspertach, w którym można wyświetlić podsumowanie błędów i ostrzeżeń.][7]

Możesz również wyświetlić dane TCP, gdy warstwa aplikacji widzi ją, klikając prawym przyciskiem myszy dane TCP i wybierając pozycję **Śledź strumień TCP**. Jest to przydatne w przypadku przechwycenia zrzutu bez filtru przechwytywania. Aby uzyskać więcej informacji, zobacz [następujące strumienie TCP](https://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html).

![Zrzut ekranu pokazujący sposób wyświetlania danych TCP jako warstwy aplikacji.][8]

> [!NOTE]
> Więcej informacji o korzystaniu z programu Wireshark można znaleźć w [podręczniku użytkownicy programu Wireshark](https://www.wireshark.org/docs/wsug_html_chunked).
>
>

### <a name="appendix-4-using-excel-to-view-metrics-and-log-data"></a><a name="appendix-4"></a>Dodatek 4: używanie programu Excel do wyświetlania metryk i danych dzienników
Wiele narzędzi umożliwia pobieranie danych metryk magazynu z usługi Azure Table Storage w formacie rozdzielanym, który ułatwia ładowanie danych do programu Excel na potrzeby przeglądania i analizowania. Dane rejestrowania magazynu z usługi Azure Blob Storage są już w formacie rozdzielanym, który można załadować do programu Excel. Należy jednak dodać odpowiednie nagłówki kolumn na podstawie informacji w [formacie dziennika analityka magazynu](/rest/api/storageservices/Storage-Analytics-Log-Format) i [schematu tabeli metryk analityka magazynu](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema).

Aby zaimportować dane rejestrowania magazynu do programu Excel po ich pobraniu z magazynu obiektów blob:

* W menu **dane** kliknij **tekst z tekstu**.
* Przejdź do pliku dziennika, który chcesz wyświetlić, a następnie kliknij przycisk **Importuj**.
* W kroku 1 **Kreatora importu tekstu** wybierz opcję **rozdzielone**.

W kroku 1 **Kreatora importu tekstu** wybierz **średnik** jako jedyny ogranicznik i wybierz podwójne cudzysłowy jako **kwalifikator tekstu**. Następnie kliknij przycisk **Zakończ** i wybierz miejsce umieszczenia danych w skoroszycie.

### <a name="appendix-5-monitoring-with-application-insights-for-azure-devops"></a><a name="appendix-5"></a>Dodatek 5: monitorowanie za pomocą Application Insights platformy Azure DevOps
Możesz również użyć funkcji Application Insights dla usługi Azure DevOps w ramach monitorowania wydajności i dostępności. To narzędzie może:

* Upewnij się, że usługa sieci Web jest dostępna i odpowiada. Bez względu na to, czy aplikacja jest witryną sieci Web, czy z aplikacją urządzenia korzystającą z usługi sieci Web, może testować adres URL co kilka minut w lokalizacjach na całym świecie i poinformować o tym, czy wystąpił problem.
* Szybko Diagnozuj wszelkie problemy z wydajnością lub wyjątki w usłudze sieci Web. Dowiedz się, czy można rozciągnąć procesor lub inne zasoby, uzyskać ślady stosu z wyjątków oraz łatwo przeszukiwać dane śledzenia dzienników. Jeśli wydajność aplikacji spadnie poniżej akceptowalnych limitów, firma Microsoft może wysłać wiadomość e-mail. Można monitorować zarówno usługi sieci Web platformy .NET, jak i Java.

Więcej informacji można znaleźć na [Application Insights](../../azure-monitor/app/app-insights-overview.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat analiz w usłudze Azure Storage, zobacz następujące zasoby:

* [Monitorowanie konta magazynu w witrynie Azure Portal](./manage-storage-analytics-logs.md)
* [Analiza magazynu](storage-analytics.md)
* [Metryki analizy magazynu](storage-analytics-metrics.md)
* [Schemat tabeli metryk usługi Storage Analytics](/rest/api/storageservices/storage-analytics-metrics-table-schema)
* [Dzienniki analityki magazynu](storage-analytics-logging.md)
* [Format dziennika analizy magazynu](/rest/api/storageservices/storage-analytics-log-format)

<!--Anchors-->
[Wprowadzenie]: #introduction
[Jak zorganizowany jest ten przewodnik]: #how-this-guide-is-organized

[Monitorowanie usługi magazynu]: #monitoring-your-storage-service
[Kondycja usługi monitorowania]: #monitoring-service-health
[Monitorowanie wydajności]: #monitoring-capacity
[Monitorowanie dostępności]: #monitoring-availability
[Monitorowanie wydajności]: #monitoring-performance

[Diagnozowanie problemów z magazynem]: #diagnosing-storage-issues
[Problemy z kondycją usługi]: #service-health-issues
[Problemy z wydajnością]: #performance-issues
[Diagnozowanie błędów]: #diagnosing-errors
[Problemy emulatora magazynu]: #storage-emulator-issues
[Narzędzia rejestrowania magazynu]: #storage-logging-tools
[Korzystanie z narzędzi do rejestrowania w sieci]: #using-network-logging-tools

[Kompleksowe śledzenie]: #end-to-end-tracing
[Skorelowanie danych dziennika]: #correlating-log-data
[Identyfikator żądania klienta]: #client-request-id
[Identyfikator żądania serwera]: #server-request-id
[Znaczniki czasu]: #timestamps

[Wskazówki dotyczące rozwiązywania problemów]: #troubleshooting-guidance
[Metryki wskazują wysoką wartość AverageE2ELatency i niską wartość AverageServerLatency]: #metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency
[Metryki wskazują niską wartość AverageE2ELatency i niską wartość AverageServerLatency, ale na kliencie występuje duże opóźnienie]: #metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency
[Metryki wskazują wysoką wartość AverageServerLatency]: #metrics-show-high-AverageServerLatency
[Występują nieoczekiwane opóźnienia w dostarczaniu komunikatów w kolejce]: #you-are-experiencing-unexpected-delays-in-message-delivery

[Metryki wskazują wzrost wartości PercentThrottlingError]: #metrics-show-an-increase-in-PercentThrottlingError
[Przejściowy wzrost w wzrost percentthrottlingerror]: #transient-increase-in-PercentThrottlingError
[Trwały wzrost błędu wzrost percentthrottlingerror]: #permanent-increase-in-PercentThrottlingError
[Metryki wskazują wzrost wartości PercentTimeoutError]: #metrics-show-an-increase-in-PercentTimeoutError
[Metryki wskazują wzrost wartości PercentNetworkError]: #metrics-show-an-increase-in-PercentNetworkError

[Klient odbiera komunikaty HTTP 403 (zabronione)]: #the-client-is-receiving-403-messages
[Klient odbiera komunikaty HTTP 404 (nie znaleziono)]: #the-client-is-receiving-404-messages
[Klient lub inny proces wcześniej usunął obiekt]: #client-previously-deleted-the-object
[Problem z autoryzacją sygnatury dostępu współdzielonego]: #SAS-authorization-issue
[Kod JavaScript po stronie klienta nie ma uprawnień dostępu do obiektu]: #JavaScript-code-does-not-have-permission
[Błąd sieci]: #network-failure
[Klient odbiera komunikaty HTTP 409 (konflikt)]: #the-client-is-receiving-409-messages

[Metryki pokazujące niskie PercentSuccess lub wpisy dziennika analizy zawierają operacje ze stanem transakcji ClientOtherErrors]: #metrics-show-low-percent-success
[Metryki pojemności pokazują nieoczekiwany wzrost użycia pojemności magazynu]: #capacity-metrics-show-an-unexpected-increase
[Problem wynika z używania emulatora magazynu na potrzeby tworzenia i testowania]: #your-issue-arises-from-using-the-storage-emulator
[Funkcja "X" nie działa w emulatorze magazynu]: #feature-X-is-not-working
[Błąd "wartość jednego z nagłówków HTTP jest w niepoprawnym formacie" podczas korzystania z emulatora magazynu]: #error-HTTP-header-not-correct-format
[Uruchamianie emulatora magazynu wymaga uprawnień administracyjnych]: #storage-emulator-requires-administrative-privileges
[Występują problemy z instalacją zestawu Azure SDK dla platformy .NET]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Istnieje inny problem dotyczący usługi magazynu]: #you-have-a-different-issue-with-a-storage-service

[Dodatki]: #appendices
[Dodatek 1: używanie programu Fiddler do przechwytywania ruchu HTTP i HTTPS]: #appendix-1
[Dodatek 2. Korzystanie z programu Wireshark do przechwytywania ruchu sieciowego]: #appendix-2
[Dodatek 4: używanie programu Excel do wyświetlania metryk i danych dzienników]: #appendix-4
[Dodatek 5: monitorowanie za pomocą Application Insights platformy Azure DevOps]: #appendix-5

<!--Image references-->
[1]: ./media/storage-monitoring-diagnosing-troubleshooting/overview.png
[3]: ./media/storage-monitoring-diagnosing-troubleshooting/hour-minute-metrics.png
[4]: ./media/storage-monitoring-diagnosing-troubleshooting/high-e2e-latency.png
[5]: ./media/storage-monitoring-diagnosing-troubleshooting/fiddler-screenshot.png
[6]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-1.png
[7]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-2.png
[8]: ./media/storage-monitoring-diagnosing-troubleshooting/wireshark-screenshot-3.png
[9]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-1.png
[10]: ./media/storage-monitoring-diagnosing-troubleshooting/mma-screenshot-2.png