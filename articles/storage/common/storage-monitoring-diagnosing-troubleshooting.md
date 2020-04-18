---
title: Monitorowanie, diagnozowanie i rozwiązywanie problemów z usługą Azure Storage | Dokumenty firmy Microsoft
description: Użyj funkcji, takich jak analiza magazynu, rejestrowanie po stronie klienta i inne narzędzia innych firm, aby identyfikować, diagnozować i rozwiązywać problemy związane z usługą Azure Storage.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: normesta
ms.reviewer: fryu
ms.subservice: common
ms.openlocfilehash: 0bbffacc0a8c47950b8637e826d1d5db9fbdb234
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81605064"
---
# <a name="monitor-diagnose-and-troubleshoot-microsoft-azure-storage"></a>Monitorowanie, diagnozowanie i rozwiązywanie problemów z usługą Microsoft Azure Storage
[!INCLUDE [storage-selector-portal-monitoring-diagnosing-troubleshooting](../../../includes/storage-selector-portal-monitoring-diagnosing-troubleshooting.md)]

## <a name="overview"></a>Omówienie
Diagnozowanie i rozwiązywanie problemów w rozproszonej aplikacji hostowane w środowisku chmury może być bardziej skomplikowane niż w tradycyjnych środowiskach. Aplikacje można wdrażać w infrastrukturze PaaS lub IaaS, lokalnie, na urządzeniu przenośnym lub w niektórych kombinacjach tych środowisk. Zazwyczaj ruch sieciowy aplikacji może przechodzić przez sieci publiczne i prywatne, a aplikacja może korzystać z wielu technologii magazynu, takich jak Tabele magazynu Microsoft Azure, obiekty Blobs, Kolejki lub Pliki oprócz innych magazynów danych, takich jak relacyjne i dokumenty baz danych.

Aby pomyślnie zarządzać takimi aplikacjami, należy monitorować je proaktywnie i zrozumieć, jak diagnozować i rozwiązywać wszystkie aspekty z nich i ich zależne technologie. Jako użytkownik usług Azure Storage należy stale monitorować usługi magazynu używane przez aplikację pod kątem nieoczekiwanych zmian w zachowaniu (takich jak wolniejsze niż zwykle czasy odpowiedzi) i używać rejestrowania do zbierania bardziej szczegółowych danych i dogłębnego analizowania problemu. Informacje diagnostyczne, które można uzyskać zarówno z monitorowania i rejestrowania pomoże ci ustalić główną przyczynę problemu, który napotkała aplikacja. Następnie można rozwiązać problem i określić odpowiednie kroki, które można podjąć, aby go rozwiązać. Usługa Azure Storage jest podstawową usługą platformy Azure i stanowi ważną część większości rozwiązań wdrażanych przez klientów w infrastrukturze platformy Azure. Usługa Azure Storage zawiera funkcje upraszczające monitorowanie, diagnozowanie i rozwiązywanie problemów z magazynem w aplikacjach chmurowych.

> [!NOTE]
> Usługa Azure Files nie obsługuje rejestrowania w tej chwili.
>

Aby uzyskać praktyczny przewodnik dotyczący kompleksowego rozwiązywania problemów w aplikacjach usługi Azure Storage, zobacz [Kompleksowe rozwiązywanie problemów przy użyciu metryk i rejestrowania usługi Azure Storage, AzCopy i analizatora komunikatów.](../storage-e2e-troubleshooting.md)

* [Wprowadzenie]
  * [Jak ten przewodnik jest zorganizowany]
* [Monitorowanie usługi pamięci masowej]
  * [Monitorowanie kondycji usługi]
  * [Zdolność monitorowania]
  * [Monitorowanie dostępności]
  * [Monitorowanie wydajności]
* [Diagnozowanie problemów z pamięcią masową]
  * [Problemy ze zdrowiem usług]
  * [Problemy z wydajnością]
  * [Diagnozowanie błędów]
  * [Problemy z emulatorem pamięci masowej]
  * [Narzędzia do rejestrowania pamięci masowej]
  * [Korzystanie z narzędzi do rejestrowania sieci]
* [Śledzenie end-to-end]
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
  * [Metryki pokazują niskie procentysukces lub wpisy dziennika analizy mają operacje ze stanem transakcji ClientOtherErrors]
  * [Metryki pojemności pokazują nieoczekiwany wzrost zużycia pojemności magazynu]
  * [Problem wynika z używania emulatora pamięci masowej do tworzenia lub testowania]
  * [Występują problemy z instalacją zestawu Azure SDK dla platformy .NET]
  * [Masz inny problem z usługą magazynu]
  * [Rozwiązywanie problemów z dyskami VHD na maszynach wirtualnych z systemem Windows](../../virtual-machines/windows/troubleshoot-vhds.md)   
  * [Rozwiązywanie problemów z dyskami VHD na maszynach wirtualnych systemu Linux](../../virtual-machines/linux/troubleshoot-vhds.md)
  * [Rozwiązywanie problemów z usługą Azure Files w systemie Windows](../files/storage-troubleshoot-windows-file-connection-problems.md)   
  * [Rozwiązywanie problemów z usługą Azure Files w systemie Linux](../files/storage-troubleshoot-linux-file-connection-problems.md)
* [Dodatki]
  * [Dodatek 1: Przechwytywanie ruchu HTTP i HTTPS za pomocą fiddlera]
  * [Dodatek 2: Korzystanie z Wireshark do przechwytywania ruchu sieciowego]
  * [Dodatek 3: Przechwytywanie ruchu sieciowego za pomocą analizatora komunikatów firmy Microsoft]
  * [Dodatek 4: Wyświetlanie danych i rejestrowania danych za pomocą programu Excel]
  * [Dodatek 5: Monitorowanie za pomocą usługi Application Insights dla usługi Azure DevOps]

## <a name="introduction"></a><a name="introduction"></a>Wprowadzenie
W tym przewodniku pokazano, jak używać funkcji, takich jak usługa Azure Storage Analytics, rejestrowanie po stronie klienta w bibliotece klienta usługi Azure Storage i inne narzędzia innych firm do identyfikowania, diagnozowania i rozwiązywania problemów związanych z usługą Azure Storage.

![][1]

Ten przewodnik jest przeznaczony do odczytywania przede wszystkim przez deweloperów usług online, które korzystają z usług Azure Storage Services i IT Pros odpowiedzialnych za zarządzanie takimi usługami online. Cele tego przewodnika to:

* Aby pomóc Ci utrzymać kondycję i wydajność kont usługi Azure Storage.
* Aby zapewnić Ci niezbędne procesy i narzędzia ułatwiające podjęcie decyzji, czy problem lub problem w aplikacji dotyczy usługi Azure Storage.
* Aby zapewnić ci wskazówki dotyczące rozwiązywania problemów związanych z usługą Azure Storage.

### <a name="how-this-guide-is-organized"></a><a name="how-this-guide-is-organized"></a>Jak ten przewodnik jest zorganizowany
W sekcji["Monitorowanie usługi magazynu"]opisano sposób monitorowania kondycji i wydajności usług usługi Azure Storage przy użyciu metryk usługi Azure Storage Analytics (Metryki magazynu).

W sekcji["Diagnozowanie problemów z magazynem"]opisano sposób diagnozowania problemów przy użyciu rejestrowania usługi Azure Storage Analytics (rejestrowanie magazynu). Opisano w nim również sposób włączania rejestrowania po stronie klienta przy użyciu obiektów w jednej z bibliotek klienckich, takich jak biblioteka klienta magazynu dla platformy .NET lub zestaw SDK platformy Azure dla środowiska Java.

W sekcji["Śledzenie end-to-end"]opisano, jak można skorelować informacje zawarte w różnych plikach dziennika i danych metryk.

Sekcja["Wskazówki dotyczące rozwiązywania problemów"]zawiera wskazówki dotyczące rozwiązywania problemów z niektórymi typowymi problemami związanymi z magazynem, które mogą wystąpić.

"Dodatki" zawierają informacje o użyciu innych narzędzi, takich jak Wireshark i Netmon do analizowania danych[pakietów]sieciowych, Fiddler do analizowania wiadomości HTTP/HTTPS i Microsoft Message Analyzer do korelowania danych dziennika.

## <a name="monitoring-your-storage-service"></a><a name="monitoring-your-storage-service"></a>Monitorowanie usługi pamięci masowej
Jeśli znasz monitorowanie wydajności systemu Windows, możesz myśleć o metryki magazynu jako odpowiednik usługi Azure Storage liczników Monitora wydajności systemu Windows. W metryki magazynu znajdziesz kompleksowy zestaw metryk (liczniki w terminologii Monitora wydajności systemu Windows), takich jak dostępność usługi, całkowita liczba żądań do usługi lub procent pomyślnych żądań do usługi. Aby uzyskać pełną listę dostępnych danych, zobacz [Schemat tabeli metryk analizy magazynu](https://msdn.microsoft.com/library/azure/hh343264.aspx). Można określić, czy usługa magazynu ma zbierać i agregować metryki co godzinę lub co minutę. Aby uzyskać więcej informacji na temat włączania metryk i monitorowania kont magazynu, zobacz [Włączanie danych magazynu i wyświetlanie danych metryk](https://go.microsoft.com/fwlink/?LinkId=510865).

Można wybrać, które metryki godzinowe mają być wyświetlane w [witrynie Azure portal](https://portal.azure.com) i skonfigurować reguły, które powiadamiają administratorów pocztą e-mail, gdy metryka godzinowa przekracza określony próg. Aby uzyskać więcej informacji, zobacz [Otrzymywanie powiadomień o alertach](/azure/monitoring-and-diagnostics/monitoring-overview-alerts).

Zalecamy przejrzenie [usługi Azure Monitor for Storage](../../azure-monitor/insights/storage-insights-overview.md) (wersja zapoznawcza). Jest to funkcja usługi Azure Monitor, która oferuje kompleksowe monitorowanie kont usługi Azure Storage, zapewniając ujednolicony widok wydajności, pojemności i dostępności usług Azure Storage. Nie wymaga włączania ani konfigurowania czegokolwiek i można natychmiast wyświetlić te metryki ze wstępnie zdefiniowanych interaktywnych wykresów i innych uwzględnionych wizualizacji.

Usługa magazynu zbiera metryki przy użyciu najlepszego wysiłku, ale może nie rejestrować każdej operacji magazynu.

W witrynie Azure portal można wyświetlić metryki, takie jak dostępność, całkowita liczba żądań i średnie liczby opóźnień dla konta magazynu. Ustanowiono również regułę powiadomień, aby ostrzegać administratora, jeśli dostępność spadnie poniżej pewnego poziomu. Od przeglądania tych danych, jednym z możliwych obszarów do badania jest procent sukcesu usługi tabeli jest poniżej 100% (aby uzyskać więcej informacji, zobacz sekcję "[Metryki pokazują niskie PercentageSuccess lub zapisy dziennika analizy mają operacje ze stanem transakcji ClientOtherErrors]").

Należy stale monitorować aplikacje platformy Azure, aby upewnić się, że są one w dobrej kondycji i działa zgodnie z oczekiwaniami przez:

* Ustanawianie niektórych metryk planu bazowego dla aplikacji, które umożliwią porównanie bieżących danych i zidentyfikowanie wszelkich istotnych zmian w zachowaniu usługi Azure Storage i aplikacji. Wartości metryki linii bazowej będzie w wielu przypadkach specyficzne dla aplikacji i należy je ustanowić podczas testowania wydajności aplikacji.
* Rejestrowanie metryk minut i używanie ich do aktywnego monitorowania nieoczekiwanych błędów i anomalii, takich jak skoki liczby błędów lub szybkości żądań.
* Rejestrowanie metryk godzinowych i używanie ich do monitorowania średnich wartości, takich jak średnia liczba błędów i szybkości żądań.
* Badanie potencjalnych problemów przy użyciu narzędzi diagnostycznych, o których wspomniano w dalszej części sekcji["Diagnozowanie problemów z pamięcią masową".]

Wykresy na poniższej ilustracji ilustrują, jak uśrednianie, które występuje dla metryk godzinowych, może ukryć skoki aktywności. Metryki godzinowe wydają się wykazywać stałą liczbę żądań, podczas gdy dane minutowe ujawniają wahania, które naprawdę mają miejsce.

![][3]

W dalszej części tej sekcji opisano, jakie metryki należy monitorować i dlaczego.

### <a name="monitoring-service-health"></a><a name="monitoring-service-health"></a>Monitorowanie kondycji usługi
Za pomocą [witryny Azure Portal](https://portal.azure.com) można wyświetlić kondycję usługi Storage (i innych usług platformy Azure) we wszystkich regionach platformy Azure na całym świecie. Monitorowanie umożliwia natychmiastowe sprawdzenie, czy problem poza formantem ma wpływ na usługę magazynu w regionie używanym dla aplikacji.

Witryna [Azure portal](https://portal.azure.com) może również dostarczać powiadomienia o zdarzeniach, które mają wpływ na różne usługi platformy Azure.
Uwaga: Te informacje były wcześniej dostępne wraz z danymi historycznymi na [pulpicie nawigacyjnym usługi Azure](https://status.azure.com).

Podczas gdy [portal Azure](https://portal.azure.com) zbiera informacje o kondycji z wewnętrznych centrów danych platformy Azure (monitorowanie wewnętrzne), można również rozważyć przyjęcie podejścia zewnętrznego do generowania transakcji syntetycznych, które okresowo uzyskują dostęp do aplikacji sieci web hostowanych przez platformę Azure z wielu lokalizacji. Usługi oferowane przez [Dynatrace](https://www.dynatrace.com/en/synthetic-monitoring) i application insights dla usługi Azure DevOps są przykładami tego podejścia. Aby uzyskać więcej informacji na temat usługi Application Insights for Azure DevOps, zobacz dodatek "[Dodatek 5: Monitorowanie za pomocą usługi Application Insights for Azure DevOps."](#appendix-5)

### <a name="monitoring-capacity"></a><a name="monitoring-capacity"></a>Zdolność monitorowania
Metryki magazynu przechowuje tylko metryki pojemności dla usługi obiektów blob, ponieważ obiekty BLOB zazwyczaj stanowią największą część przechowywanych danych (w momencie pisania nie jest możliwe użycie metryk magazynu do monitorowania pojemności tabel i kolejek). Te dane można znaleźć w tabeli **$MetricsCapacityBlob,** jeśli włączono monitorowanie usługi obiektów Blob. Metryki magazynu rejestruje te dane raz dziennie i można użyć wartości **RowKey,** aby ustalić, czy wiersz zawiera jednostkę, która odnosi się do danych użytkownika **(dane**wartości) lub danych analitycznych **(analiza**wartości ). Każda przechowywana encja zawiera informacje o ilości używanego magazynu **(Pojemność** mierzona w bajtach) oraz bieżącej liczbie kontenerów **(ContainerCount)** i obiektów blob (**ObjectCount**) używanych na koncie magazynu. Aby uzyskać więcej informacji na temat metryk pojemności przechowywanych w tabeli **$MetricsCapacityBlob,** zobacz [Schemat tabeli metryk analizy magazynu](https://msdn.microsoft.com/library/azure/hh343264.aspx).

> [!NOTE]
> Należy monitorować te wartości dla wczesnego ostrzegania, że zbliżasz się do limitów pojemności konta magazynu. W witrynie Azure portal można dodać reguły alertów, aby powiadomić cię, jeśli użycie magazynu zbiorczego przekracza lub spada poniżej progów, które określisz.
>
>

Aby uzyskać pomoc w szacowaniu rozmiaru różnych obiektów magazynu, takich jak obiekty blob, zobacz wpis w blogu [Opis rozliczeń usługi Azure Storage — przepustowość, transakcje i pojemność.](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/07/09/understanding-windows-azure-storage-billing-bandwidth-transactions-and-capacity.aspx)

### <a name="monitoring-availability"></a><a name="monitoring-availability"></a>Monitorowanie dostępności
Dostępność usług magazynu na koncie magazynu należy monitorować, monitorując wartość w kolumnie **Dostępność** w tabelach metryk godzinowych lub minutowych — **$MetricsHourPrimaryTransactionsBlob**, **$MetricsHourPrimaryTransactionsTable**, **$MetricsHourPrimaryTransactionsQueue**, **$MetricsMinutePrimaryTransactionsBlob**, **$MetricsMinutePrimaryTransactionsTable**, **$MetricsMinutePrimaryTransactionsQueue**, **$MetricsCapacityBlob**. **Kolumna Dostępność** zawiera wartość procentową, która wskazuje dostępność usługi lub operacji interfejsu API reprezentowanej przez wiersz **(klawisz RowKey** pokazuje, czy wiersz zawiera metryki dla usługi jako całości lub dla określonej operacji interfejsu API).

Każda wartość mniejsza niż 100% wskazuje, że niektóre żądania magazynu są wadowe. Można zobaczyć, dlaczego nie są one wada, badając inne kolumny w danych metryk, które pokazują liczbę żądań z różnych typów błędów, takich jak **ServerTimeoutError**. Należy oczekiwać, aby zobaczyć **dostępność** spadnie tymczasowo poniżej 100% z powodów, takich jak przejściowe limity czasu serwera, podczas gdy usługa przenosi partycje do lepszego żądania równoważenia obciążenia; logika ponawiania w aplikacji klienckiej powinna obsługiwać takie warunki przerywane. Artykuł [Usługa Storage Analytics Logged Operations and Status Messages](https://msdn.microsoft.com/library/azure/hh343260.aspx) zawiera listę typów transakcji, które metryki magazynu zawierają w obliczeniach **dostępności.**

W [witrynie Azure portal](https://portal.azure.com)można dodać reguły alertów, aby powiadomić Cię, jeśli **dostępność** usługi spadnie poniżej określonego progu.

W sekcji["Wskazówki dotyczące rozwiązywania problemów"]w tym przewodniku opisano niektóre typowe problemy z usługą magazynu związane z dostępnością.

### <a name="monitoring-performance"></a><a name="monitoring-performance"></a>Monitorowanie wydajności
Aby monitorować wydajność usług magazynu, można użyć następujących metryk z tabel metryk godzinowych i minut.

* Wartości w **kolumnach AverageE2ELatency** i **AverageServerLatency** pokazują średni czas, przez jaki usługa magazynu lub typ operacji interfejsu API jest przyjmowany do przetwarzania żądań. **AverageE2ELatency** jest miarą opóźnienia end-to-end, która obejmuje czas przeznaczony na odczyt żądania i wysłanie odpowiedzi oprócz czasu poświęconego na przetworzenie żądania (w związku z tym obejmuje opóźnienie sieci, gdy żądanie dotrze do usługi magazynu); **AverageServerLatency** jest miarą tylko czas przetwarzania i dlatego wyklucza wszelkie opóźnienia sieci związane z komunikowaniem się z klientem. Zobacz sekcję "[Metryki pokazują wysoką średniąE2elatency i niską średniąserverlatency]" w dalszej części tego przewodnika, aby o tym, dlaczego może istnieć znacząca różnica między tymi dwiema wartościami.
* Wartości w **Kolumnach TotalIngress** i **TotalEgress** pokazują całkowitą ilość danych w bajtach, które przychodzą i wychodzą z usługi magazynu lub za pośrednictwem określonego typu operacji interfejsu API.
* Wartości w **kolumnie TotalRequests** pokazują całkowitą liczbę żądań odbieranych przez usługę magazynowania operacji interfejsu API. **TotalRequests** to całkowita liczba żądań odbieranych przez usługę magazynu.

Zazwyczaj będzie monitorować nieoczekiwane zmiany w dowolnej z tych wartości jako wskaźnik, że masz problem, który wymaga badania.

W [witrynie Azure portal](https://portal.azure.com)można dodać reguły alertów, aby powiadomić Cię, jeśli którakolwiek z metryk wydajności dla tej usługi spadnie poniżej lub przekroczy próg, który określisz.

W sekcji["Wskazówki dotyczące rozwiązywania problemów"]w tym przewodniku opisano niektóre typowe problemy z usługą magazynu związane z wydajnością.

## <a name="diagnosing-storage-issues"></a><a name="diagnosing-storage-issues"></a>Diagnozowanie problemów z pamięcią masową
Istnieje kilka sposobów, które mogą być świadome problemu lub problemu w aplikacji, w tym:

* Poważny błąd, który powoduje awarię aplikacji lub do zaprzestania pracy.
* Znaczące zmiany wartości bazowych w monitorowaniu metryk, zgodnie z opisem w poprzedniej sekcji["Monitorowanie usługi magazynowania".]
* Raporty od użytkowników aplikacji, że niektóre określone działanie nie zostało zakończone zgodnie z oczekiwaniami lub że niektóre funkcje nie działa.
* Błędy generowane w aplikacji, które pojawiają się w plikach dziennika lub za pomocą innej metody powiadamiania.

Zazwyczaj problemy związane z usługami magazynu platformy Azure należą do jednej z czterech ogólnych kategorii:

* Aplikacja ma problem z wydajnością, zgłoszony przez użytkowników lub ujawniony przez zmiany w metrykach wydajności.
* Wystąpił problem z infrastrukturą usługi Azure Storage w co najmniej jednym regionie.
* Aplikacja napotyka błąd zgłoszony przez użytkowników lub ujawniony przez zwiększenie jednej z metryk liczby błędów, które monitorujesz.
* Podczas opracowywania i testowania, może być przy użyciu emulatora magazynu lokalnego; mogą wystąpić pewne problemy, które odnoszą się specjalnie do użycia emulatora magazynu.

W poniższych sekcjach opisano kroki, które należy wykonać w celu zdiagnozowania i rozwiązania problemów w każdej z tych czterech kategorii. Sekcja "[Wskazówki dotyczące rozwiązywania problemów]" w dalszej części tego przewodnika zawiera więcej szczegółów na temat niektórych typowych problemów, które mogą wystąpić.

### <a name="service-health-issues"></a><a name="service-health-issues"></a>Problemy ze zdrowiem usług
Problemy ze zdrowiem usługi są zazwyczaj poza kontrolą. Portal [Azure](https://portal.azure.com) zawiera informacje o bieżących problemach z usługami platformy Azure, w tym usługami magazynu. Jeśli podczas tworzenia konta magazynu w systemie Read-Access magazyn geograficznie nadmiarowy została wydzielona, jeśli dane staną się niedostępne w lokalizacji podstawowej, aplikacja może tymczasowo przełączyć się na kopię tylko do odczytu w lokalizacji dodatkowej. Aby odczytywać z pomocniczego, aplikacja musi być w stanie przełączać się między przy użyciu podstawowych i dodatkowych lokalizacji magazynu i być w stanie pracować w trybie zmniejszonej funkcjonalności z danymi tylko do odczytu. Biblioteki klienta usługi Azure Storage umożliwiają definiowanie zasad ponawiania prób, które można odczytać z magazynu pomocniczego w przypadku niepowodzenia odczytu z magazynu podstawowego. Aplikacja musi również mieć świadomość, że dane w lokalizacji pomocniczej jest po pewnym czasie spójne. Aby uzyskać więcej informacji, zobacz wpis w blogu [Opcje nadmiarowości usługi Azure Storage i Dostęp do odczytu Geo nadmiarowy magazyn](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

### <a name="performance-issues"></a><a name="performance-issues"></a>Problemy z wydajnością
Wydajność aplikacji może być wartością subiektywną, zwłaszcza z punktu widzenia użytkownika. Dlatego należy mieć dostępne metryki linii bazowej, które ułatwiają wykrywanie problemów z wydajnością. Wiele czynników może mieć wpływ na wydajność usługi magazynu platformy Azure z punktu widzenia aplikacji klienckiej. Te czynniki mogą działać w usłudze magazynu, na kliencie lub w infrastrukturze sieciowej; w związku z tym ważne jest, aby mieć strategię identyfikacji pochodzenia problemu wydajności.

Po zidentyfikowaniu prawdopodobnej lokalizacji przyczyny problemu z wydajnością z metryk, można następnie użyć plików dziennika, aby znaleźć szczegółowe informacje, aby zdiagnozować i rozwiązać problem dalej.

Sekcja "[Wskazówki dotyczące rozwiązywania problemów]" w dalszej części tego przewodnika zawiera więcej informacji na temat niektórych typowych problemów związanych z wydajnością, które mogą wystąpić.

### <a name="diagnosing-errors"></a><a name="diagnosing-errors"></a>Diagnozowanie błędów
Użytkownicy aplikacji mogą powiadamiać użytkownika o błędach zgłoszonych przez aplikację kliencką. Metryki magazynu rejestruje również liczbę różnych typów błędów z usług magazynu, takich jak **NetworkError**, **ClientTimeoutError**lub **AuthorizationError**. Podczas gdy metryki magazynu rejestruje tylko liczby różnych typów błędów, można uzyskać więcej szczegółów na temat poszczególnych żądań, sprawdzając dzienniki po stronie serwera, klienta i sieci. Zazwyczaj kod stanu HTTP zwracany przez usługę magazynu daje wskazanie, dlaczego żądanie nie powiodło się.

> [!NOTE]
> Należy pamiętać, że należy spodziewać się niektórych sporadyczne błędy: na przykład błędy z powodu przejściowych warunków sieciowych lub błędów aplikacji.
>
>

Przydatne dla zrozumienia kodów stanu i błędów związanych z magazynem są następujące zasoby:

* [Typowe kody błędów interfejsu API REST](https://msdn.microsoft.com/library/azure/dd179357.aspx)
* [Kody błędów usługi Blob Service](https://msdn.microsoft.com/library/azure/dd179439.aspx)
* [Kody błędów usługi kolejki](https://msdn.microsoft.com/library/azure/dd179446.aspx)
* [Kody błędów usługi tabeli](https://msdn.microsoft.com/library/azure/dd179438.aspx)
* [Kody błędów usługi plików](https://msdn.microsoft.com/library/azure/dn690119.aspx)

### <a name="storage-emulator-issues"></a><a name="storage-emulator-issues"></a>Problemy z emulatorem pamięci masowej
Zestaw Azure SDK zawiera emulator magazynu, który można uruchomić na deweloperskiej stacji roboczej. Ten emulator symuluje większość zachowania usług magazynu platformy Azure i jest przydatny podczas tworzenia i testowania, umożliwiając uruchamianie aplikacji korzystających z usług magazynu platformy Azure bez konieczności subskrypcji platformy Azure i konta magazynu platformy Azure.

W sekcji["Wskazówki dotyczące rozwiązywania problemów"]w tym przewodniku opisano niektóre typowe problemy napotkane przy użyciu emulatora magazynu.

### <a name="storage-logging-tools"></a><a name="storage-logging-tools"></a>Narzędzia do rejestrowania pamięci masowej
Rejestrowanie magazynu zapewnia rejestrowanie po stronie serwera żądań magazynu na koncie magazynu platformy Azure. Aby uzyskać więcej informacji na temat włączania rejestrowania po stronie serwera i uzyskiwania dostępu do danych dziennika, zobacz [Włączanie rejestrowania magazynu i uzyskiwania dostępu do danych dziennika](https://go.microsoft.com/fwlink/?LinkId=510867).

Biblioteka klienta magazynu dla platformy .NET umożliwia zbieranie danych dziennika po stronie klienta, które odnoszą się do operacji magazynu wykonywanych przez aplikację. Aby uzyskać więcej informacji, zobacz [Client-side Logging with the .NET Storage Client Library](https://go.microsoft.com/fwlink/?LinkId=510868) (Logowanie po stronie klienta przy użyciu biblioteki klienckiej usługi .NET Storage).

> [!NOTE]
> W pewnych okolicznościach (takich jak błędy autoryzacji sygnatury dostępu Współdzielonego), użytkownik może zgłosić błąd, dla którego nie można znaleźć żadnych danych żądania w dziennikach magazynu po stronie serwera. Można użyć możliwości rejestrowania biblioteki klienta magazynu, aby zbadać, czy przyczyną problemu jest na kliencie, czy użyć narzędzi do monitorowania sieci w celu zbadania sieci.
>
>

### <a name="using-network-logging-tools"></a><a name="using-network-logging-tools"></a>Korzystanie z narzędzi do rejestrowania sieci
Można przechwycić ruch między klientem a serwerem, aby zapewnić szczegółowe informacje na temat danych wymienianych przez klienta i serwer oraz podstawowych warunków sieciowych. Przydatne narzędzia do rejestrowania sieci obejmują:

* [Fiddler](https://www.telerik.com/fiddler) to darmowy serwer proxy do debugowania sieci Web, który umożliwia sprawdzanie nagłówków i danych ładunku komunikatów żądania i odpowiedzi HTTP i HTTPS. Aby uzyskać więcej informacji, zobacz [Dodatek 1: Przechwytywanie ruchu HTTP i HTTPS za pomocą fiddlera.](#appendix-1)
* [Microsoft Network Monitor (Netmon)](https://www.microsoft.com/download/details.aspx?id=4865) i [Wireshark](https://www.wireshark.org/) to bezpłatne analizatory protokołów sieciowych, które umożliwiają wyświetlanie szczegółowych informacji o pakietach dla szerokiego zakresu protokołów sieciowych. Aby uzyskać więcej informacji na temat Wireshark, zobacz "[Dodatek 2: Używanie Wireshark do przechwytywania ruchu sieciowego](#appendix-2)".
* Microsoft Message Analyzer to narzędzie firmy Microsoft, które zastępuje netmon i które oprócz przechwytywania danych pakietów sieciowych pomaga wyświetlać i analizować dane dziennika przechwycone z innych narzędzi. Aby uzyskać więcej informacji, zobacz "[Dodatek 3: Przechwytywanie ruchu sieciowego za pomocą analizatora komunikatów firmy Microsoft".](#appendix-3)
* Jeśli chcesz wykonać test podstawowej łączności, aby sprawdzić, czy komputer kliencki może łączyć się z usługą magazynu platformy Azure za pośrednictwem sieci, nie można tego zrobić przy użyciu standardowego narzędzia **ping** na kliencie. Można jednak użyć [narzędzia **tcping** ](https://www.elifulkerson.com/projects/tcping.php) do sprawdzenia łączności.

W wielu przypadkach dane dziennika z rejestrowania magazynu i biblioteki klienta magazynu będą wystarczające do zdiagnozowania problemu, ale w niektórych scenariuszach może być konieczne bardziej szczegółowe informacje, które te narzędzia do rejestrowania sieci mogą dostarczyć. Na przykład za pomocą Fiddler do wyświetlania wiadomości HTTP i HTTPS umożliwia wyświetlanie danych nagłówka i ładunku wysyłane do i z usług magazynu, co umożliwi sprawdzenie, jak aplikacja kliencka ponawia operacje magazynu. Analizatory protokołów, takie jak Wireshark, działają na poziomie pakietów, umożliwiając wyświetlanie danych TCP, co umożliwia rozwiązywanie problemów z utraconymi pakietami i łącznością. Analizator wiadomości może działać zarówno na warstwach HTTP, jak i TCP.

## <a name="end-to-end-tracing"></a><a name="end-to-end-tracing"></a>Śledzenie end-to-end
Śledzenie end-to-end przy użyciu różnych plików dziennika jest użyteczną techniką do badania potencjalnych problemów. Informacje o dacie/godzinie z danych metryk można użyć jako wskazania, od czego zacząć szukać w plikach dziennika szczegółowych informacji, które pomogą Ci rozwiązać problem.

### <a name="correlating-log-data"></a><a name="correlating-log-data"></a>Skorelowanie danych dziennika
Podczas przeglądania dzienników z aplikacji klienckich, śledzenia sieci i rejestrowania magazynu po stronie serwera jest bardzo ważne, aby móc skorelować żądania w różnych plikach dziennika. Pliki dziennika zawierają wiele różnych pól, które są przydatne jako identyfikatory korelacji. Identyfikator żądania klienta jest najbardziej przydatne pole do użycia do skorelowania wpisów w różnych dziennikach. Czasami jednak może być przydatne użycie identyfikatora żądania serwera lub sygnatur czasowych. Poniższe sekcje zawierają więcej szczegółów na temat tych opcji.

### <a name="client-request-id"></a><a name="client-request-id"></a>Identyfikator żądania klienta
Biblioteka klienta magazynu automatycznie generuje unikatowy identyfikator żądania klienta dla każdego żądania.

* W dzienniku po stronie klienta, który tworzy biblioteka klienta magazynu, identyfikator żądania klienta pojawia się w polu **Identyfikator żądania klienta** każdego wpisu dziennika odnoszącego się do żądania.
* W śledzenia sieci, takich jak jeden przechwycone przez Fiddler, identyfikator żądania klienta jest widoczny w komunikatach żądania jako **x-ms-client-request-id** wartość nagłówka HTTP.
* W dzienniku rejestrowania magazynu po stronie serwera identyfikator żądania klienta pojawia się w kolumnie Identyfikator żądania klienta.

> [!NOTE]
> Istnieje możliwość wielu żądań do współużytkowania tego samego identyfikatora żądania klienta, ponieważ klient może przypisać tę wartość (chociaż biblioteka klienta magazynu przypisuje nową wartość automatycznie). Gdy klient ponawia próbę, wszystkie próby współużytkują ten sam identyfikator żądania klienta. W przypadku partii wysłanej z klienta partia ma identyfikator pojedynczego żądania klienta.
>
>

### <a name="server-request-id"></a><a name="server-request-id"></a>Identyfikator żądania serwera
Usługa magazynu automatycznie generuje identyfikatory żądań serwera.

* W dzienniku rejestrowania magazynu po stronie serwera identyfikator żądania serwera jest wyświetlany kolumna **nagłówka identyfikator żądania.**
* W śledzenia sieci, takich jak jeden przechwycone przez Fiddler, identyfikator żądania serwera pojawia się w wiadomościach odpowiedzi jako **x-ms-request-id** wartość nagłówka HTTP.
* W dzienniku po stronie klienta, który tworzy biblioteka klienta magazynu, identyfikator żądania serwera pojawia się w kolumnie **Tekst operacji** dla wpisu dziennika zawierającego szczegóły odpowiedzi serwera.

> [!NOTE]
> Usługa magazynu zawsze przypisuje unikatowy identyfikator żądania serwera do każdego żądania, które otrzymuje, więc każda próba ponawiania próby od klienta i każdej operacji zawartej w partii ma unikatowy identyfikator żądania serwera.
>
>

Jeśli biblioteka klienta magazynu zgłasza **StorageException** w kliencie, **RequestInformation** właściwość zawiera **RequestResult** obiektu, który zawiera **ServiceRequestID** właściwości. Można również uzyskać dostęp **do RequestResult** obiektu z **OperationContext** wystąpienia.

Przykładowy kod poniżej pokazuje, jak ustawić niestandardową **clientRequestId** wartość dołączając **OperationContext** obiektu żądania do usługi magazynu. Pokazano również, jak pobrać **ServerRequestId** wartość z komunikatu odpowiedzi.

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

### <a name="timestamps"></a><a name="timestamps"></a>Znaczniki czasu
Można również użyć sygnatur czasowych, aby zlokalizować powiązane wpisy dziennika, ale należy uważać na wszelkie pochylenia zegara między klientem a serwerem, który może istnieć. Wyszukaj plus lub minus 15 minut w celu dopasowania wpisów po stronie serwera na podstawie sygnatury czasowej na kliencie. Należy pamiętać, że metadane obiektów blob dla obiektów blob zawierających metryki wskazuje zakres czasu dla metryk przechowywanych w obiekcie blob. Ten zakres czasu jest przydatne, jeśli masz wiele metryk obiektów blob dla tej samej minuty lub godziny.

## <a name="troubleshooting-guidance"></a><a name="troubleshooting-guidance"></a>Wskazówki dotyczące rozwiązywania problemów
Ta sekcja pomoże Ci w diagnostyce i rozwiązywaniu niektórych typowych problemów, które aplikacja może napotkać podczas korzystania z usług magazynu platformy Azure. Skorzystaj z poniższej listy, aby znaleźć informacje istotne dla danego problemu.

**Drzewo decyzji dotyczące rozwiązywania problemów**

---
Czy problem dotyczy wydajności jednej z usług magazynu?

* [Metryki wskazują wysoką wartość AverageE2ELatency i niską wartość AverageServerLatency]
* [Metryki wskazują niską wartość AverageE2ELatency i niską wartość AverageServerLatency, ale na kliencie występuje duże opóźnienie]
* [Metryki wskazują wysoką wartość AverageServerLatency]
* [Występują nieoczekiwane opóźnienia w dostarczaniu komunikatów w kolejce]

---
Czy problem dotyczy dostępności jednej z usług magazynu?

* [Metryki wskazują wzrost wartości PercentThrottlingError]
* [Metryki wskazują wzrost wartości PercentTimeoutError]
* [Metryki wskazują wzrost wartości PercentNetworkError]

---
 Czy aplikacja kliencka odbiera odpowiedź HTTP 4XX (na przykład 404) z usługi magazynu?

* [Klient odbiera komunikaty HTTP 403 (zabronione)]
* [Klient odbiera komunikaty HTTP 404 (nie znaleziono)]
* [Klient odbiera komunikaty HTTP 409 (konflikt)]

---
[Metryki pokazują niskie procentysukces lub wpisy dziennika analizy mają operacje ze stanem transakcji ClientOtherErrors]

---
[Metryki pojemności pokazują nieoczekiwany wzrost zużycia pojemności magazynu]

---
[Występują nieoczekiwane ponowne uruchomienie maszyn wirtualnych, które mają dużą liczbę dołączonych dysków VHD]

---
[Problem wynika z używania emulatora pamięci masowej do tworzenia lub testowania]

---
[Występują problemy z instalacją zestawu Azure SDK dla platformy .NET]

---
[Masz inny problem z usługą magazynu]

---
### <a name="metrics-show-high-averagee2elatency-and-low-averageserverlatency"></a><a name="metrics-show-high-AverageE2ELatency-and-low-AverageServerLatency"></a>Metryki wskazują wysoką wartość AverageE2ELatency i niską wartość AverageServerLatency
Poniższa ilustracja z narzędzia do monitorowania [portalu Azure](https://portal.azure.com) pokazuje przykład, w którym Średnia **Wartość 2Elatency** jest znacznie wyższa niż **ŚredniaServerLatency**.

![][4]

Usługa magazynu oblicza tylko metrykę **AverageE2ELatency** dla pomyślnych żądań i, w przeciwieństwie do **AverageServerLatency,** zawiera czas potrzebny klientowi na wysłanie danych i odebranie potwierdzenia z usługi magazynu. W związku z tym różnica między **AverageE2ELatency** i **AverageServerLatency** może być albo ze względu na aplikację kliencką jest wolno odpowiadać lub ze względu na warunki w sieci.

> [!NOTE]
> Można również wyświetlić **E2ELatency** i **ServerLatency** dla poszczególnych operacji magazynu w danych dziennika rejestrowania magazynu.
>
>

#### <a name="investigating-client-performance-issues"></a>Badanie problemów z wydajnością klienta
Możliwe przyczyny, dla których klient odpowiada powoli, obejmują ograniczoną liczbę dostępnych połączeń lub wątków lub brak zasobów, takich jak procesor, pamięć lub przepustowość sieci. Można rozwiązać ten problem, modyfikując kod klienta, aby był bardziej wydajny (na przykład przy użyciu wywołań asynchronicznych do usługi magazynu) lub przy użyciu większej maszyny wirtualnej (z większą liczą rdzeni i większą pamięcią).

W przypadku usług tabeli i kolejek algorytm Nagle'a może również powodować wysoką **średniąE2ELatency** w porównaniu do **AverageServerLatency**: aby uzyskać więcej informacji, zobacz [post Algorytm Nagle'a nie jest przyjazny dla małych żądań.](https://blogs.msdn.com/b/windowsazurestorage/archive/2010/06/25/nagle-s-algorithm-is-not-friendly-towards-small-requests.aspx) Algorytm Nagle można wyłączyć w kodzie przy użyciu **servicepointmanager** klasy w **System.Net** przestrzeni nazw. Należy to zrobić przed wykonywaniem jakichkolwiek wywołań do usług tabeli lub kolejek w aplikacji, ponieważ nie ma to wpływu na połączenia, które są już otwarte. Poniższy przykład pochodzi z **metody Application_Start** w roli procesu roboczego.

```csharp
var storageAccount = CloudStorageAccount.Parse(connStr);
ServicePoint tableServicePoint = ServicePointManager.FindServicePoint(storageAccount.TableEndpoint);
tableServicePoint.UseNagleAlgorithm = false;
ServicePoint queueServicePoint = ServicePointManager.FindServicePoint(storageAccount.QueueEndpoint);
queueServicePoint.UseNagleAlgorithm = false;
```

Należy sprawdzić dzienniki po stronie klienta, aby zobaczyć, ile żądań aplikacja kliencka jest przesyłana i sprawdzić ogólne wąskie gardła wydajności związane z platformy .NET w kliencie, takie jak CPU, .NET wyrzucania elementów bezużytecznych, wykorzystanie sieci lub pamięci. Aby rozwiązać problem z aplikacjami klienckimi platformy .NET, zobacz [Debugowanie, Śledzenie i profilowanie](https://msdn.microsoft.com/library/7fe0dd2y).

#### <a name="investigating-network-latency-issues"></a>Badanie problemów z opóźnieniem sieci
Zazwyczaj wysokie opóźnienia end-to-end spowodowane przez sieć jest ze względu na stany przejściowe. Można zbadać zarówno przejściowe, jak i trwałe problemy z siecią, takie jak porzucone pakiety, za pomocą narzędzi, takich jak Wireshark lub Microsoft Message Analyzer.

Aby uzyskać więcej informacji na temat używania wireshark do rozwiązywania problemów z siecią, zobacz "[Dodatek 2: Przechwytywanie ruchu sieciowego za pomocą Wireshark."]

Aby uzyskać więcej informacji na temat używania analizatora komunikatów firmy Microsoft do rozwiązywania problemów z siecią, zobacz "[Dodatek 3: Przechwytywanie ruchu sieciowego za pomocą analizatora wiadomości firmy Microsoft."]

### <a name="metrics-show-low-averagee2elatency-and-low-averageserverlatency-but-the-client-is-experiencing-high-latency"></a><a name="metrics-show-low-AverageE2ELatency-and-low-AverageServerLatency"></a>Metryki wskazują niską wartość AverageE2ELatency i niską wartość AverageServerLatency, ale na kliencie występuje duże opóźnienie
W tym scenariuszu najbardziej prawdopodobną przyczyną jest opóźnienie w żądaniach magazynu docierających do usługi magazynu. Należy zbadać, dlaczego żądania od klienta nie są co do usługi obiektu blob.

Jedną z możliwych przyczyn opóźnienia żądania wysyłania przez klienta jest ograniczona liczba dostępnych połączeń lub wątków.

Sprawdź również, czy klient wykonuje wiele ponownych prób i zbadać przyczynę, jeśli tak jest. Aby ustalić, czy klient wykonuje wiele ponownych prób, można:

* Sprawdź dzienniki analizy magazynu. Jeśli dzieje się wiele ponownych prób, zobaczysz wiele operacji z tym samym identyfikatorem żądania klienta, ale z różnymi identyfikatorami żądań serwera.
* Sprawdź dzienniki klienta. Pełne rejestrowanie wskazuje, że nastąpiła ponowna próba.
* Debuguj kod i sprawdź właściwości **obiektu OperationContext** skojarzonego z żądaniem. Jeśli operacja została ponowiona, **RequestResults** właściwość będzie zawierać wiele unikatowych identyfikatorów żądań serwera. Można również sprawdzić godziny rozpoczęcia i zakończenia dla każdego żądania. Aby uzyskać więcej informacji, zobacz przykładowy kod w sekcji [Identyfikator żądania serwera].

Jeśli nie ma żadnych problemów w kliencie, należy zbadać potencjalne problemy z siecią, takie jak utrata pakietów. Za pomocą narzędzi, takich jak Wireshark lub Microsoft Message Analyzer, można zbadać problemy z siecią.

Aby uzyskać więcej informacji na temat używania wireshark do rozwiązywania problemów z siecią, zobacz "[Dodatek 2: Przechwytywanie ruchu sieciowego za pomocą Wireshark."]

Aby uzyskać więcej informacji na temat używania analizatora komunikatów firmy Microsoft do rozwiązywania problemów z siecią, zobacz "[Dodatek 3: Przechwytywanie ruchu sieciowego za pomocą analizatora wiadomości firmy Microsoft."]

### <a name="metrics-show-high-averageserverlatency"></a><a name="metrics-show-high-AverageServerLatency"></a>Metryki wskazują wysoką wartość AverageServerLatency
W przypadku wysokiej **AverageServerLatency** dla żądań pobierania obiektów blob, należy użyć dzienników rejestrowania magazynu, aby sprawdzić, czy istnieją powtarzające się żądania dla tego samego obiektu blob (lub zestaw obiektów blob). W przypadku żądań przekazywania obiektów blob należy zbadać, jaki rozmiar bloku używa klient (na przykład bloki o rozmiarze mniejszym niż 64 K mogą spowodować obciążenie, chyba że odczyty są również w mniej niż 64 K fragmentów), a jeśli wielu klientów przekazuje bloki do tego samego obiektu blob równolegle. Należy również sprawdzić metryki na minutę pod kątem skoków liczby żądań, które powodują przekroczenie celów skalowalności na sekundę: zobacz również "[Metryki pokazują wzrost PercentTimeoutError]."

Jeśli widzisz wysokiej **AverageServerLatency** dla żądań pobierania obiektów blob, gdy istnieją powtarzające się żądania tego samego obiektu blob lub zestaw obiektów blob, należy rozważyć buforowanie tych obiektów blob przy użyciu pamięci podręcznej azure lub sieci dostarczania zawartości platformy Azure (CDN). W przypadku żądań przekazywania można poprawić przepływność przy użyciu większego rozmiaru bloku. W przypadku kwerend do tabel jest również możliwe do zaimplementowania buforowania po stronie klienta na klientach, którzy wykonują te same operacje kwerendy i gdzie dane nie zmieniają się często.

High **AverageServerLatency** wartości mogą być również objawem źle zaprojektowane tabel lub kwerend, które powodują operacje skanowania lub które wykonaj dołączyć/prepend anty-wzorzec. Aby uzyskać więcej informacji, zobacz "[Metryki pokazują wzrost PercentThrottlingError]".

> [!NOTE]
> Obszerną listę kontrolną wydajności listy kontrolnej można znaleźć tutaj: [Lista kontrolna wydajności i skalowalności magazynu platformy Microsoft Azure.](storage-performance-checklist.md)
>
>

### <a name="you-are-experiencing-unexpected-delays-in-message-delivery-on-a-queue"></a><a name="you-are-experiencing-unexpected-delays-in-message-delivery"></a>Występują nieoczekiwane opóźnienia w dostarczaniu komunikatów w kolejce
Jeśli występują opóźnienia między czasem aplikacja dodaje wiadomość do kolejki i czas staje się dostępny do odczytu z kolejki, następnie należy wykonać następujące kroki, aby zdiagnozować problem:

* Sprawdź, czy aplikacja pomyślnie dodaje wiadomości do kolejki. Sprawdź, czy aplikacja nie ponawia próby **AddMessage** metody kilka razy przed pomyślnym. Dzienniki biblioteki klienta magazynu będą wyświetlane wszelkie powtarzające się ponownych prób operacji magazynu.
* Sprawdź, czy nie ma pochylenia zegara między rolą procesu roboczego, która dodaje wiadomość do kolejki, a rolą procesu roboczego, która odczytuje wiadomość z kolejki, która sprawia, że wygląda tak, jakby wystąpiło opóźnienie w przetwarzaniu.
* Sprawdź, czy rola procesu roboczego, która odczytuje wiadomości z kolejki, nie działa. Jeśli klient kolejki wywołuje **GetMessage** metody, ale nie można odpowiedzieć za pomocą potwierdzenia, wiadomość pozostanie niewidoczny w kolejce, aż do wygaśnięcia okresu **niewidokności.** W tym momencie komunikat staje się dostępny do przetwarzania ponownie.
* Sprawdź, czy długość kolejki rośnie w czasie. Taka możliwość może wystąpić, jeśli nie masz wystarczającej liczby pracowników dostępnych do przetwarzania wszystkich wiadomości, które inni pracownicy umieszczają w kolejce. Sprawdź również metryki, aby sprawdzić, czy żądania usuwania nie powiodły się, a liczba dequeue na wiadomościach, co może wskazywać na powtarzające się nieudane próby usunięcia wiadomości.
* Sprawdź dzienniki rejestrowania magazynu dla wszystkich operacji kolejki, które mają wyższe niż **oczekiwano Wartości E2ELatency** i **ServerLatency** przez dłuższy okres czasu niż zwykle.

### <a name="metrics-show-an-increase-in-percentthrottlingerror"></a><a name="metrics-show-an-increase-in-PercentThrottlingError"></a>Metryki wskazują wzrost wartości PercentThrottlingError
Błędy ograniczania występują po przekroczeniu docelowych skalowalności usługi magazynu. Ograniczanie usługi magazynu, aby upewnić się, że żaden klient lub dzierżawca nie może korzystać z usługi kosztem innych. Aby uzyskać więcej informacji, zobacz [Cele skalowalności i wydajności dla standardowych kont magazynu,](scalability-targets-standard-account.md) aby uzyskać szczegółowe informacje na temat celów skalowalności dla kont magazynu i celów wydajności dla partycji na kontach magazynu.

Jeśli **metryka PercentThrottlingError** wykazują wzrost odsetka żądań, które nie mają czasu na błąd ograniczania przepustowości, należy zbadać jeden z dwóch scenariuszy:

* [Przejściowy wzrost PercentThrottlingError]
* [Stały wzrost błędu PercentThrottlingError]

Wzrost **PercentThrottlingError** często występuje w tym samym czasie, co wzrost liczby żądań magazynu lub gdy są początkowo ładowania testowania aplikacji. Może to również objawiać się w kliencie jako "503 Server Busy" lub "500 Operation Timeout" komunikaty stanu HTTP z operacji magazynu.

#### <a name="transient-increase-in-percentthrottlingerror"></a><a name="transient-increase-in-PercentThrottlingError"></a>Przejściowy wzrost PercentThrottlingError
Jeśli widzisz skoki wartości **PercentThrottlingError,** które pokrywają się z okresami wysokiej aktywności dla aplikacji, należy zaimplementować wykładniczą (nie liniową) strategię wycofywania ponownych prób w kliencie. Wycofywanie ponownych prób zmniejsza natychmiastowe obciążenie partycji i pomaga aplikacji wygładzić skoki ruchu. Aby uzyskać więcej informacji na temat sposobu implementowania zasad ponawiania prób przy użyciu biblioteki klienta magazynu, zobacz [obszar nazw Microsoft.Azure.Storage.RetryPolicies](/dotnet/api/microsoft.azure.storage.retrypolicies).

> [!NOTE]
> Można również zobaczyć skoki wartości **PercentThrottlingError,** które nie pokrywają się z okresami wysokiej aktywności dla aplikacji: najbardziej prawdopodobną przyczyną w tym miejscu jest przenoszenie partycji usługi magazynu w celu poprawy równoważenia obciążenia.
>
>

#### <a name="permanent-increase-in-percentthrottlingerror-error"></a><a name="permanent-increase-in-PercentThrottlingError"></a>Stały wzrost błędu PercentThrottlingError
Jeśli widzisz stale wysoką wartość **percentthrottlingError** po stałym wzroście wolumenów transakcji lub podczas wykonywania testów początkowego obciążenia w aplikacji, następnie należy ocenić, jak aplikacja używa partycji magazynu i czy zbliża się do celów skalowalności dla konta magazynu. Na przykład jeśli widzisz błędy ograniczania przepustowości w kolejce (która liczy się jako pojedyncza partycja), należy rozważyć użycie dodatkowych kolejek do rozłąki transakcji na wielu partycjach. Jeśli widzisz błędy ograniczania przepustowości w tabeli, należy rozważyć użycie innego schematu partycjonowania do rozłożenia transakcji na wiele partycji przy użyciu szerszego zakresu wartości klucza partycji. Jedną z typowych przyczyn tego problemu jest prepend/dołącz anti-pattern, gdzie można wybrać datę jako klucz partycji, a następnie wszystkie dane w danym dniu jest zapisywany na jednej partycji: pod obciążeniem, może to spowodować wąskie gardło zapisu. Należy wziąć pod uwagę inny projekt partycjonowania lub ocenić, czy przy użyciu magazynu obiektów blob może być lepszym rozwiązaniem. Sprawdź również, czy ograniczanie jest występujące w wyniku skoków w ruchu i zbadać sposoby wygładzania wzorzec żądań.

Jeśli rozesłać transakcje na wielu partycjach, nadal należy pamiętać o limity skalowalności ustawione dla konta magazynu. Na przykład jeśli użyto dziesięć kolejek każdego przetwarzania maksymalnie 2000 komunikatów 1KB na sekundę, będzie na ogólny limit 20 000 wiadomości na sekundę dla konta magazynu. Jeśli musisz przetworzyć więcej niż 20 000 jednostek na sekundę, należy rozważyć użycie wielu kont magazynu. Należy również pamiętać, że rozmiar żądań i jednostek ma wpływ na kiedy usługa magazynu ogranicza klientów: jeśli masz większe żądania i jednostki, może być ograniczona wcześniej.

Nieefektywny projekt kwerendy może również spowodować trafienie limitów skalowalności dla partycji tabeli. Na przykład kwerenda z filtrem, który wybiera tylko jeden procent jednostek na partycji, ale skanuje wszystkie jednostki na partycji będzie musiał uzyskać dostęp do każdej jednostki. Każdy odczyt jednostki będzie wliczany do całkowitej liczby transakcji w tej partycji; w związku z tym można łatwo osiągnąć cele skalowalności.

> [!NOTE]
> Testy wydajności powinny ujawnić wszelkie nieefektywne projekty zapytań w aplikacji.
>
>

### <a name="metrics-show-an-increase-in-percenttimeouterror"></a><a name="metrics-show-an-increase-in-PercentTimeoutError"></a>Metryki wskazują wzrost wartości PercentTimeoutError
Metryki pokazują wzrost **PercentTimeoutError** dla jednej z usług magazynu. W tym samym czasie klient odbiera dużą ilość komunikatów o stanie HTTP "500 Operation Timeout" z operacji magazynu.

> [!NOTE]
> Mogą być widoczne błędy limitu czasu tymczasowo jako obciążenia usługi magazynu równoważe żądania, przenosząc partycję do nowego serwera.
>
>

Metryka **PercentTimeoutError** jest agregacją następujących metryk: **ClientTimeoutError**, **AnonymousClientTimeoutError**, **SASClientTimeoutError**, **ServerTimeoutError**, **AnonymousServerTimeoutError**i **SASServerTimeoutError**.

Limity czasu serwera są spowodowane przez błąd na serwerze. Limity czasu klienta się zdarzyć, ponieważ operacja na serwerze przekroczyła limit czasu określony przez klienta; na przykład klient korzystający z biblioteki klienta magazynu można ustawić limit czasu dla operacji przy użyciu **ServerTimeout** właściwości **QueueRequestOptions** klasy.

Limity czasu serwera wskazują na problem z usługą magazynu, który wymaga dalszego badania. Można użyć metryki, aby sprawdzić, czy są trafienia limity skalowalności dla usługi i zidentyfikować wszelkie skoki ruchu, które mogą być przyczyną tego problemu. Jeśli problem jest przerywany, może to być spowodowane aktywnością równoważenia obciążenia w usłudze. Jeśli problem jest trwały i nie jest spowodowany przez aplikację trafienie limitów skalowalności usługi, należy zgłosić problem pomocy technicznej. W przypadku limitów czasu klienta należy zdecydować, czy limit czasu jest ustawiony na odpowiednią wartość w kliencie i albo zmienić wartość limitu czasu ustawioną w kliencie lub zbadać, jak można poprawić wydajność operacji w usłudze magazynu, na przykład przez optymalizację zapytań tabeli lub zmniejszenie rozmiaru wiadomości.

### <a name="metrics-show-an-increase-in-percentnetworkerror"></a><a name="metrics-show-an-increase-in-PercentNetworkError"></a>Metryki wskazują wzrost wartości PercentNetworkError
Metryki pokazują wzrost **PercentNetworkError** dla jednej z usług magazynu. Metryka **PercentNetworkError** jest agregacją następujących metryk: **NetworkError**, **AnonymousNetworkError**i **SASNetworkError**. Te występują, gdy usługa magazynu wykrywa błąd sieci, gdy klient złoży żądanie magazynu.

Najczęstszą przyczyną tego błędu jest odłączenie klienta przed upływem limitu czasu w usłudze magazynu. Zbadaj kod w kliencie, aby zrozumieć, dlaczego i kiedy klient rozłącza się z usługą magazynu. Można również użyć Wireshark, Microsoft Message Analyzer lub Tcping do zbadania problemów z łącznością sieciową z klienta. Narzędzia te są opisane w [dodatkach].

### <a name="the-client-is-receiving-http-403-forbidden-messages"></a><a name="the-client-is-receiving-403-messages"></a>Klient odbiera komunikaty HTTP 403 (zabronione)
Jeśli aplikacja kliencka zgłasza błędy HTTP 403 (zabronione), prawdopodobną przyczyną jest to, że klient używa wygasłej sygnatury dostępu współdzielonego podczas wysyłania żądania magazynu (chociaż inne możliwe przyczyny to niedokładność zegara, nieprawidłowe klucze i puste nagłówki). Jeśli przyczyną jest wygasły klucz sygnatury dostępu współdzielonego, nie będą widoczne żadne wpisy w danych dziennika rejestrowania danych magazynu po stronie serwera. W poniższej tabeli przedstawiono przykład z dziennika po stronie klienta wygenerowanego przez bibliotekę klienta magazynu, który ilustruje występujący ten problem:

| Element źródłowy | Szczegółowość | Szczegółowość | Identyfikator żądania klienta | Tekst operacji |
| --- | --- | --- | --- | --- |
| Microsoft.Azure.Storage |Informacje |3 |85d077ab-... |Uruchamianie operacji z lokalizacją Podstawową dla trybu lokalizacji Tylko w trybie. |
| Microsoft.Azure.Storage |Informacje |3 |85d077ab -... |Uruchamianie synchronicznego żądania<https://domemaildist.blob.core.windows.netazureimblobcontainer/blobCreatedViaSAS.txt?sv=2014-02-14&sr=c&si=mypolicy&sig=OFnd4Rd7z01fIvh%2BmcR6zbudIH2F5Ikm%2FyhNYZEmJNQ%3D&api-version=2014-02-14> |
| Microsoft.Azure.Storage |Informacje |3 |85d077ab -... |Oczekiwanie na odpowiedź. |
| Microsoft.Azure.Storage |Ostrzeżenie |2 |85d077ab -... |Wyjątek podczas oczekiwania na odpowiedź: Serwer zdalny zwrócił błąd: (403) Zabronione. |
| Microsoft.Azure.Storage |Informacje |3 |85d077ab -... |Otrzymano odpowiedź. Kod stanu = 403, Identyfikator żądania = 9d67c64a-64ed-4b0d-9515-3b14bbcdc63d, Content-MD5 = , ETag = . |
| Microsoft.Azure.Storage |Ostrzeżenie |2 |85d077ab -... |Wyjątek zgłoszony podczas operacji: Serwer zdalny zwrócił błąd: (403) Zabronione.. |
| Microsoft.Azure.Storage |Informacje |3 |85d077ab -... |Sprawdzanie, czy operacja powinna zostać ponowiona. Liczba ponownych prób = 0, kod stanu HTTP = 403, Wyjątek = Serwer zdalny zwrócił błąd: (403) Zabronione.. |
| Microsoft.Azure.Storage |Informacje |3 |85d077ab -... |Następna lokalizacja została ustawiona na Podstawowa, na podstawie trybu lokalizacji. |
| Microsoft.Azure.Storage |Błąd |1 |85d077ab -... |Zasady ponawiania próby nie pozwalają na ponowną próbę. Niepowodzenie z serwerem zdalnym zwrócił błąd: (403) Zabronione. |

W tym scenariuszu należy zbadać, dlaczego token sygnatury dostępu Współdzielonego wygasa, zanim klient wyśle token do serwera:

* Zazwyczaj nie należy ustawiać czasu rozpoczęcia, tworząc sygnaturę dostępu współdzielonego dla klienta przeznaczoną do natychmiastowego użycia. Jeśli istnieją małe różnice zegara między hostem generującym sygnaturę dostępu współdzielonego przy użyciu bieżącej godziny a usługą magazynu jest możliwe, że usługa magazynu otrzyma sygnaturę dostępu współdzielonego, która nie jest jeszcze ważna.
* Nie należy ustawiać bardzo krótkiego czasu wygaśnięcia w sygnaturze dostępu współdzielonego. W tym przypadku niewielkie różnice zegara między hostem generującym sygnaturę dostępu współdzielonego a usługą magazynu mogą prowadzić do tego, że sygnatura dostępu współdzielonego wygasa wcześniej, niż zakładano.
* Czy parametr wersji w kluczu Sygnatury dostępu Współdzielonego (na przykład **sv=2015-04-05)** jest zgodny z wersją używanej biblioteki klienta magazynu? Zaleca się, aby zawsze korzystać z najnowszej wersji [biblioteki klienta magazynu](https://www.nuget.org/packages/WindowsAzure.Storage/).
* Jeśli ponownie wygenerujesz klucze dostępu do magazynu, wszelkie istniejące tokeny SAS mogą zostać unieważnione. Ten problem może wystąpić, jeśli generujesz tokeny SAS z długim czasem wygaśnięcia dla aplikacji klienckich do przechowywania w pamięci podręcznej.

Jeśli używasz biblioteki klienta usługi magazynu do generowania tokenów SAS, utworzenie prawidłowego tokenu jest łatwe. Jeśli jednak używasz interfejsu API REST magazynu i konstruujesz tokeny sygnatury dostępu współdzielonego ręcznie, zobacz [Delegowanie dostępu za pomocą sygnatury dostępu współdzielonego.](https://msdn.microsoft.com/library/azure/ee395415.aspx)

### <a name="the-client-is-receiving-http-404-not-found-messages"></a><a name="the-client-is-receiving-404-messages"></a>Klient odbiera komunikaty HTTP 404 (nie znaleziono)
Jeśli aplikacja kliencka odbiera komunikat HTTP 404 (nie znaleziono) z serwera, oznacza to, że obiekt, którego klient próbował użyć (np. jednostka, tabela, obiekt blob, kontener lub kolejka) nie istnieje w usłudze magazynu. Istnieje kilka możliwych przyczyn tej sytuacji, takich jak:

* [Klient lub inny proces wcześniej usunął obiekt]
* [Problem z autoryzacją sygnatury dostępu współdzielonego]
* [Kod JavaScript po stronie klienta nie ma uprawnień dostępu do obiektu]
* [Błąd sieci]

#### <a name="the-client-or-another-process-previously-deleted-the-object"></a><a name="client-previously-deleted-the-object"></a>Klient lub inny proces wcześniej usunął obiekt
W scenariuszach, w których klient próbuje odczytać, zaktualizować lub usunąć dane w usłudze magazynu jest zwykle łatwe do zidentyfikowania w dziennikach po stronie serwera poprzedniej operacji, która usunęła dany obiekt z usługi magazynu. Często dane dziennika pokazuje, że inny użytkownik lub proces usunięte obiektu. W dzienniku rejestrowania magazynu po stronie serwera kolumny typu operacji i żądanego klucza obiektowego są wyświetlane, gdy klient usunął obiekt.

W scenariuszu, w którym klient próbuje wstawić obiekt, może nie być od razu oczywiste, dlaczego powoduje to odpowiedź HTTP 404 (Nie znaleziono), biorąc pod uwagę, że klient tworzy nowy obiekt. Jednak jeśli klient tworzy obiekt blob musi być w stanie znaleźć kontener obiektu blob, jeśli klient tworzy komunikat musi być w stanie znaleźć kolejkę, a jeśli klient jest dodanie wiersza musi być w stanie znaleźć tabelę.

Można użyć dziennika po stronie klienta z biblioteki klienta magazynu, aby uzyskać bardziej szczegółowe zrozumienie, kiedy klient wysyła określone żądania do usługi magazynu.

Następujący dziennik po stronie klienta wygenerowany przez bibliotekę klienta magazynu ilustruje problem, gdy klient nie może znaleźć kontenera dla obiektu blob, który tworzy. Ten dziennik zawiera szczegóły następujących operacji przechowywania:

| Identyfikator żądania | Operacja |
| --- | --- |
| 07b26a5d-... |**DeleteIfExists** metoda, aby usunąć kontener obiektu blob. Należy zauważyć, że ta operacja zawiera żądanie **HEAD,** aby sprawdzić istnienie kontenera. |
| e2d06d78... |**CreateIfNotExists** metoda do tworzenia kontenera obiektów blob. Należy zauważyć, że ta operacja zawiera żądanie **HEAD,** który sprawdza istnienie kontenera. **Head** zwraca komunikat 404, ale nadal. |
| de8b1c3c-... |**UploadFromStream** metoda do tworzenia obiektu blob. Żądanie **PUT** kończy się niepowodzeniem z komunikatem 404 |

Wpisy dziennika:

| Identyfikator żądania | Tekst operacji |
| --- | --- |
| 07b26a5d-... |Uruchamianie synchronicznego żądania do `https://domemaildist.blob.core.windows.net/azuremmblobcontainer`. |
| 07b26a5d-... |StringToSign = HEAD............ x-ms-client-request-id:07b26a5d-.... x-ms-date:Wt, 03 cze 2014 10:33:11 GMT.x-ms-version:2014-02-14./domuaildist/azuremmblobtainer.restype:container. |
| 07b26a5d-... |Oczekiwanie na odpowiedź. |
| 07b26a5d-... |Otrzymano odpowiedź. Kod stanu = 200, Identyfikator żądania = eeead849-... Content-MD5 = , &quot;ETag = 0x8D14D2DC63D059B&quot;. |
| 07b26a5d-... |Nagłówki odpowiedzi zostały pomyślnie przetworzone, przechodząc do pozostałej części operacji. |
| 07b26a5d-... |Pobieranie treści odpowiedzi. |
| 07b26a5d-... |Operacja została zakończona pomyślnie. |
| 07b26a5d-... |Uruchamianie synchronicznego żądania do `https://domemaildist.blob.core.windows.net/azuremmblobcontainer`. |
| 07b26a5d-... |StringToSign = DELETE............ x-ms-client-request-id:07b26a5d-.... x-ms-date:Wt, 03 cze 2014 10:33:12 GMT.x-ms-version:2014-02-14./domuaildist/azuremmblobtainer.restype:container. |
| 07b26a5d-... |Oczekiwanie na odpowiedź. |
| 07b26a5d-... |Otrzymano odpowiedź. Kod stanu = 202, Identyfikator żądania = 6ab2a4cf-..., Content-MD5 = , ETag = . |
| 07b26a5d-... |Nagłówki odpowiedzi zostały pomyślnie przetworzone, przechodząc do pozostałej części operacji. |
| 07b26a5d-... |Pobieranie treści odpowiedzi. |
| 07b26a5d-... |Operacja została zakończona pomyślnie. |
| e2d06d78-... |Uruchamianie asynchronii `https://domemaildist.blob.core.windows.net/azuremmblobcontainer`żądania do .</td> |
| e2d06d78-... |StringToSign = HEAD............ x-ms-client-request-id:e2d06d78-.... x-ms-date:Wt, 03 cze 2014 10:33:12 GMT.x-ms-version:2014-02-14./domuaildist/azuremmblobtainer.restype:container. |
| e2d06d78-... |Oczekiwanie na odpowiedź. |
| de8b1c3c-... |Uruchamianie synchronicznego żądania do `https://domemaildist.blob.core.windows.net/azuremmblobcontainer/blobCreated.txt`. |
| de8b1c3c-... |StringToSign = PUT... 64.qCmF+TQLPhq/YYK50mP9ZQ=........ x-ms-blob-type:BlockBlob.x-ms-client-request-id:de8b1c3c-.... x-ms-date:Wt, 03 cze 2014 10:33:12 GMT.x-ms-version:2014-02-14./domuaildist/azuremmblobcontainer/blobCreated.txt. |
| de8b1c3c-... |Przygotowanie do zapisu danych żądania. |
| e2d06d78-... |Wyjątek zgłoszony podczas oczekiwania na odpowiedź: Serwer zdalny zwrócił błąd: (404) Nie znaleziono.. |
| e2d06d78-... |Otrzymano odpowiedź. Kod stanu = 404, Identyfikator żądania = 353ae3bc-..., Content-MD5 = , ETag = . |
| e2d06d78-... |Nagłówki odpowiedzi zostały pomyślnie przetworzone, przechodząc do pozostałej części operacji. |
| e2d06d78-... |Pobieranie treści odpowiedzi. |
| e2d06d78-... |Operacja została zakończona pomyślnie. |
| e2d06d78-... |Uruchamianie asynchronii `https://domemaildist.blob.core.windows.net/azuremmblobcontainer`żądania do . |
| e2d06d78-... |StringToSign = PUT... 0.........x-ms-client-request-id:e2d06d78-.... x-ms-date:Wt, 03 cze 2014 10:33:12 GMT.x-ms-version:2014-02-14./domuaildist/azuremmblobtainer.restype:container. |
| e2d06d78-... |Oczekiwanie na odpowiedź. |
| de8b1c3c-... |Zapisywanie danych żądania. |
| de8b1c3c-... |Oczekiwanie na odpowiedź. |
| e2d06d78-... |Wyjątek zgłoszony podczas oczekiwania na odpowiedź: Serwer zdalny zwrócił błąd: (409) Konflikt.. |
| e2d06d78-... |Otrzymano odpowiedź. Kod stanu = 409, Identyfikator żądania = c27da20e-..., Content-MD5 = , ETag = . |
| e2d06d78-... |Pobieranie treści odpowiedzi na błędy. |
| de8b1c3c-... |Wyjątek zgłoszony podczas oczekiwania na odpowiedź: Serwer zdalny zwrócił błąd: (404) Nie znaleziono.. |
| de8b1c3c-... |Otrzymano odpowiedź. Kod stanu = 404, Identyfikator żądania = 0eaeab3e-..., Content-MD5 = , ETag = . |
| de8b1c3c-... |Wyjątek zgłoszony podczas operacji: Serwer zdalny zwrócił błąd: (404) Nie znaleziono.. |
| de8b1c3c-... |Zasady ponawiania próby nie pozwalają na ponowną próbę. Niepowodzenie z serwerem zdalnym zwrócił błąd: (404) Nie znaleziono.. |
| e2d06d78-... |Zasady ponawiania próby nie pozwalają na ponowną próbę. Niepowodzenie z serwerem zdalnym zwrócił błąd: (409) Konflikt.. |

W tym przykładzie dziennik pokazuje, że klient jest przeplatanie żądań z **CreateIfNotExists** metody (identyfikator żądania e2d06d78...) z żądaniami z **UploadFromStream** metody (de8b1c3c-...). To przeplot dzieje się, ponieważ aplikacja kliencka wywołuje te metody asynchronicznie. Zmodyfikuj kod asynchroniczne w kliencie, aby upewnić się, że tworzy kontener przed próbą przekazania danych do obiektu blob w tym kontenerze. Najlepiej, jeśli należy utworzyć wszystkie pojemniki z wyprzedzeniem.

#### <a name="a-shared-access-signature-sas-authorization-issue"></a><a name="SAS-authorization-issue"></a>Problem z autoryzacją sygnatury dostępu współdzielonego
Jeśli aplikacja kliencka próbuje użyć klucza sygnatury dostępu Współdzielonego, który nie zawiera niezbędnych uprawnień dla operacji, usługa magazynu zwraca do klienta komunikat HTTP 404 (Nie znaleziono). W tym samym czasie zostanie również wyświetlona wartość niezerowa dla **SASAuthorizationError** w metrykach.

W poniższej tabeli przedstawiono przykładowy komunikat dziennika po stronie serwera z pliku dziennika rejestrowania magazynu:

| Nazwa | Wartość |
| --- | --- |
| Czas rozpoczęcia żądania | 2014-05-30T06:17:48.4473697Z |
| Typ operacji     | Właściwości GetBlobProperties            |
| Stan żądania     | SasAutoryzacjaError        |
| Kod stanu HTTP   | 404                            |
| Typ uwierzytelniania| Sas                          |
| Typ usługi       | Obiekt blob                         |
| Adres URL żądania         | `https://domemaildist.blob.core.windows.net/azureimblobcontainer/blobCreatedViaSAS.txt` |
| &nbsp;                 |   ?sv=2014-02-14&sr=c&si=mypolicy&sig=XXXXX&;api-version=2014-02-14 |
| Nagłówek identyfikatora żądania  | a1f348d5-8032-4912-93ef-b393e5252a3b |
| Identyfikator żądania klienta  | 2d064953-8436-4ee0-aa0c-65cb874f7929 |


Sprawdź, dlaczego aplikacja kliencka próbuje wykonać operację, do której nie przyznano uprawnień.

#### <a name="client-side-javascript-code-does-not-have-permission-to-access-the-object"></a><a name="JavaScript-code-does-not-have-permission"></a>Kod JavaScript po stronie klienta nie ma uprawnień dostępu do obiektu
Jeśli korzystasz z klienta JavaScript, a usługa przechowywania zwraca komunikaty HTTP 404, sprawdź, czy w przeglądarce nie ma następujących błędów JavaScript:

```
SEC7120: Origin http://localhost:56309 not found in Access-Control-Allow-Origin header.
SCRIPT7002: XMLHttpRequest: Network Error 0x80070005, Access is denied.
```

> [!NOTE]
> Za pomocą narzędzi deweloperskich F12 w programie Internet Explorer można śledzić wiadomości wymieniane między przeglądarką a usługą magazynu podczas rozwiązywania problemów z javascriptem po stronie klienta.
>
>

Te błędy występują, ponieważ przeglądarka sieci web implementuje to samo ograniczenie zabezpieczeń [zasad pochodzenia,](https://www.w3.org/Security/wiki/Same_Origin_Policy) które uniemożliwia stronie sieci web wywoływanie interfejsu API w innej domenie niż domena, z których pochodzi strona.

Aby obejść problem javascript, można skonfigurować udostępnianie zasobów cross origin (CORS) dla usługi magazynu, do której klient uzyskuje dostęp. Aby uzyskać więcej informacji, zobacz [Obsługa udostępniania zasobów między źródłami (CORS) dla usług Azure Storage Services](https://msdn.microsoft.com/library/azure/dn535601.aspx).

Poniższy przykładowy kod pokazuje, jak skonfigurować usługę obiektu blob, aby zezwolić na uruchamianie języka JavaScript w domenie Contoso w celu uzyskania dostępu do obiektu blob w usłudze magazynu obiektów blob:

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

#### <a name="network-failure"></a><a name="network-failure"></a>Awaria sieci
W niektórych okolicznościach utracone pakiety sieciowe mogą prowadzić do zwrócenia klientowi usługi magazynu zwracającej wiadomości HTTP 404. Na przykład gdy aplikacja kliencka jest usuwanie jednostki z usługi tabel widać klienta zgłosić wyjątek magazynu raportowania "HTTP 404 (Nie znaleziono)" komunikat stanu z usługi tabel. Podczas badania tabeli w usłudze magazynu tabel, widać, że usługa nie usunąć jednostki zgodnie z żądaniem.

Szczegóły wyjątku w kliencie obejmują identyfikator żądania (7e84f12d...) przypisany przez usługę tabel dla żądania: można użyć tych informacji, aby zlokalizować szczegóły żądania w dziennikach magazynu po stronie serwera, wyszukując w kolumnie **nagłówka identyfikatora żądania** w pliku dziennika. Można również użyć metryki, aby zidentyfikować, kiedy wystąpią błędy, takie jak ten, a następnie przeszukać pliki dziennika na podstawie czasu metryki rejestrowane ten błąd. Ten wpis dziennika pokazuje, że usunięcie nie powiodło się z komunikatem stanu "HTTP (404) Client Other Error". Ten sam wpis dziennika zawiera również identyfikator żądania wygenerowany przez klienta w kolumnie **identyfikator żądania klienta** (813ea74f...).

Dziennik po stronie serwera zawiera również inny wpis o tej samej wartości **identyfikatora żądania klienta** (813ea74f...) dla pomyślnej operacji usuwania dla tej samej jednostki i od tego samego klienta. Ta pomyślna operacja usuwania miała miejsce bardzo krótko przed żądaniem usunięcia nie powiodło się.

Najbardziej prawdopodobną przyczyną tego scenariusza jest to, że klient wysłał żądanie usunięcia jednostki do usługi tabel, która powiodła się, ale nie otrzymała potwierdzenia z serwera (być może z powodu tymczasowego problemu z siecią). Następnie klient automatycznie ponowił próbę operacji (przy użyciu tego samego **identyfikatora żądania klienta),** a ta ponowna próba nie powiodła się, ponieważ jednostka została już usunięta.

Jeśli ten problem występuje często, należy zbadać, dlaczego klient nie odbiera potwierdzeń z usługi tabel. Jeśli problem jest przerywany, należy podeszwać do pułapki "HTTP (404) Nie znaleziono" błąd i zalogować go w kliencie, ale zezwolić klientowi na kontynuowanie.

### <a name="the-client-is-receiving-http-409-conflict-messages"></a><a name="the-client-is-receiving-409-messages"></a>Klient odbiera komunikaty HTTP 409 (konflikt)
W poniższej tabeli przedstawiono wyciąg z dziennika po stronie serwera dla dwóch operacji klienta: **DeleteIfExists** następuje natychmiast **CreateIfNotExists** przy użyciu tej samej nazwy kontenera obiektu blob. Każda operacja klienta powoduje dwa żądania wysyłane do serwera, najpierw **GetContainerProperties** żądanie, aby sprawdzić, czy kontener istnieje, a następnie **DeleteContainer** lub **CreateContainer** żądania.

| Znacznik czasu | Operacja | Wynik | Nazwa kontenera | Identyfikator żądania klienta |
| --- | --- | --- | --- | --- |
| 05:10:13.7167225 |Właściwości GetContainerProperties |200 |mmcont (własówce) |c9f52c89-... |
| 05:10:13.8167325 |Usuń kontener |202 |mmcont (własówce) |c9f52c89-... |
| 05:10:13.8987407 |Właściwości GetContainerProperties |404 |mmcont (własówce) |bc881924-... |
| 05:10:14.2147723 |Utwórz Kontener |409 |mmcont (własówce) |bc881924-... |

Kod w aplikacji klienckiej usuwa, a następnie natychmiast odtwarza kontener obiektów blob przy użyciu tej samej nazwy: **CreateIfNotExists** metody (identyfikator żądania klienta bc881924-...) po pewnym czasie kończy się niepowodzeniem z błędem HTTP 409 (Konflikt). Kiedy klient usuwa kontenery obiektów blob, tabele lub kolejki, musi upłynąć pewien czas, zanim nazwa stanie się ponownie dostępna.

Aplikacja kliencka powinna używać unikatowych nazw kontenerów za każdym razem, gdy tworzy nowe kontenery, jeśli wzorzec usuń/utwórz ponownie jest typowy.

### <a name="metrics-show-low-percentsuccess-or-analytics-log-entries-have-operations-with-transaction-status-of-clientothererrors"></a><a name="metrics-show-low-percent-success"></a>Metryki pokazują niskie procentysukces lub wpisy dziennika analizy mają operacje ze stanem transakcji ClientOtherErrors
Metryka **PercentSuccess** przechwytuje procent operacji, które zakończyły się pomyślnie na podstawie ich kodu stanu HTTP. Operacje z kodami stanu 2XX są liczone jako udane, podczas gdy operacje z kodami stanu w zakresach 3XX, 4XX i 5XX są liczone jako nieudane i obniżają wartość metryki **PercentSuccess.** W plikach dziennika magazynu po stronie serwera operacje te są rejestrowane ze stanem transakcji **ClientOtherErrors**.

Należy pamiętać, że te operacje zostały zakończone pomyślnie i dlatego nie mają wpływu na inne metryki, takie jak dostępność. Oto kilka przykładów operacji, które zostały pomyślnie wykonane, ale mogą spowodować niepowodzenie kodów stanu HTTP:

* **ResourceNotFound** (nie znaleziono 404), na przykład z żądania GET do obiektu blob, który nie istnieje.
* **ResourceAlreadyExists** (Konflikt 409), na przykład z **CreateIfNotExist** operacji, gdzie zasób już istnieje.
* **ConditionNotMet** (Not Modified 304), na przykład z operacji warunkowej, takich jak gdy klient wysyła wartość **ETag** i nagłówek HTTP **If-None-Match,** aby zażądać obrazu tylko wtedy, gdy został zaktualizowany od ostatniej operacji.

Listę typowych kodów błędów interfejsu API REST zwracanych przez usługi magazynowania można znaleźć na stronie [Typowe kody błędów interfejsu API REST](https://msdn.microsoft.com/library/azure/dd179357.aspx).

### <a name="capacity-metrics-show-an-unexpected-increase-in-storage-capacity-usage"></a><a name="capacity-metrics-show-an-unexpected-increase"></a>Metryki pojemności pokazują nieoczekiwany wzrost zużycia pojemności magazynu
Jeśli widzisz nagłe, nieoczekiwane zmiany w pojemność użytkowania na koncie magazynu, można zbadać przyczyny, najpierw patrząc na metryki dostępności; na przykład zwiększenie liczby żądań usunięcia nie powiodło się może prowadzić do zwiększenia ilości magazynu obiektów blob, który jest używany jako operacje oczyszczania specyficzne dla aplikacji, które mogą być oczekiwane, zwalniając miejsce może nie działać zgodnie z oczekiwaniami (na przykład, ponieważ tokeny sygnatury dostępu Współdzielonego używane do zwalniania miejsca wygasły).

### <a name="your-issue-arises-from-using-the-storage-emulator-for-development-or-test"></a><a name="your-issue-arises-from-using-the-storage-emulator"></a>Problem wynika z używania emulatora pamięci masowej do tworzenia lub testowania
Zazwyczaj używasz emulatora magazynu podczas tworzenia i testowania, aby uniknąć wymagań dotyczących konta magazynu platformy Azure. Typowe problemy, które mogą wystąpić podczas korzystania z emulatora magazynu są:

* [Funkcja "X" nie działa w emulatorze pamięci masowej]
* [Błąd "Wartość jednego z nagłówków HTTP nie jest w prawidłowym formacie" podczas korzystania z emulatora magazynu]
* [Uruchamianie emulatora magazynu wymaga uprawnień administracyjnych]

#### <a name="feature-x-is-not-working-in-the-storage-emulator"></a><a name="feature-X-is-not-working"></a>Funkcja "X" nie działa w emulatorze pamięci masowej
Emulator magazynu nie obsługuje wszystkich funkcji usług magazynu platformy Azure, takich jak usługa plików. Więcej informacji można znaleźć w temacie [Use the Azure Storage Emulator for Development and Testing](storage-use-emulator.md) (Używanie emulatora usługi Azure Storage do programowania i testowania).

Dla tych funkcji, które emulator magazynu nie obsługuje, należy użyć usługi magazynu platformy Azure w chmurze.

#### <a name="error-the-value-for-one-of-the-http-headers-is-not-in-the-correct-format-when-using-the-storage-emulator"></a><a name="error-HTTP-header-not-correct-format"></a>Błąd "Wartość jednego z nagłówków HTTP nie jest w prawidłowym formacie" podczas korzystania z emulatora magazynu
Testujesz aplikację, która używa biblioteki klienta magazynu przed emulatorem magazynu lokalnego i wywołania metody, takie jak **CreateIfNotExists** zakończyć się niepowodzeniem z komunikatem o błędzie "Wartość jednego z nagłówków HTTP nie jest w poprawnym formacie." Oznacza to, że wersja emulatora magazynu, którego używasz, nie obsługuje wersji biblioteki klienta magazynu, której używasz. Biblioteka klienta magazynu dodaje nagłówek **x-ms-version** do wszystkich żądań, które wykonuje. Jeśli emulator magazynu nie rozpoznaje wartości w nagłówku **x-ms-version,** odrzuca żądanie.

Można użyć dzienników klienta biblioteki magazynu, aby zobaczyć wartość **nagłówka x-ms-version, który** wysyła. Można również zobaczyć wartość **nagłówka x-ms-version,** jeśli używasz Fiddler do śledzenia żądań z aplikacji klienckiej.

Ten scenariusz zazwyczaj występuje, jeśli zainstalować i używać najnowszej wersji biblioteki klienta magazynu bez aktualizowania emulatora magazynu. Należy zainstalować najnowszą wersję emulatora magazynu lub użyć magazynu w chmurze zamiast emulatora do tworzenia i testowania.

#### <a name="running-the-storage-emulator-requires-administrative-privileges"></a><a name="storage-emulator-requires-administrative-privileges"></a>Uruchamianie emulatora magazynu wymaga uprawnień administracyjnych
Po uruchomieniu emulatora magazynu zostanie wyświetlony monit o podanie poświadczeń administratora. Dzieje się tak tylko podczas inicjowania emulatora magazynu po raz pierwszy. Po zainicjowaniu emulatora magazynu nie są potrzebne uprawnienia administracyjne, aby uruchomić go ponownie.

Więcej informacji można znaleźć w temacie [Use the Azure Storage Emulator for Development and Testing](storage-use-emulator.md) (Używanie emulatora usługi Azure Storage do programowania i testowania). Można również zainicjować emulator magazynu w programie Visual Studio, który będzie również wymagać uprawnień administracyjnych.

### <a name="you-are-encountering-problems-installing-the-azure-sdk-for-net"></a><a name="you-are-encountering-problems-installing-the-Windows-Azure-SDK"></a>Występują problemy z instalacją zestawu Azure SDK dla platformy .NET
Podczas próby zainstalowania sdk, nie można zainstalować emulator magazynu na komputerze lokalnym. Dziennik instalacji zawiera jeden z następujących komunikatów:

* CAQuietExec: Błąd: nie można uzyskać dostępu do wystąpienia SQL
* CAQuietExec: Błąd: nie można utworzyć bazy danych

Przyczyną jest problem z istniejącą instalacją LocalDB. Domyślnie emulator magazynu używa LocalDB do utrwalania danych, gdy symuluje usługi magazynu platformy Azure. Wystąpienie localdb można zresetować, uruchamiając następujące polecenia w oknie wiersza polecenia przed próbą zainstalowania sdk.

```
sqllocaldb stop v11.0
sqllocaldb delete v11.0
delete %USERPROFILE%\WAStorageEmulatorDb3*.*
sqllocaldb create v11.0
```

Polecenie **delete** usuwa wszystkie stare pliki bazy danych z poprzednich instalacji emulatora magazynu.

### <a name="you-have-a-different-issue-with-a-storage-service"></a><a name="you-have-a-different-issue-with-a-storage-service"></a>Masz inny problem z usługą magazynu
Jeśli poprzednie sekcje rozwiązywania problemów nie zawierają problemu z usługą magazynu, należy przyjąć następujące podejście do diagnozowania i rozwiązywania problemu.

* Sprawdź swoje dane, aby sprawdzić, czy nie ma żadnych zmian w oczekiwanym zachowaniu linii bazowej. Na podstawie metryk można określić, czy problem jest przejściowy czy stały i na które operacje magazynowania dotyczy problem.
* Informacje o metrykach ułatwiają przeszukiwanie danych dziennika po stronie serwera w poszukiwaniu bardziej szczegółowych informacji o błędach, które występują. Te informacje mogą pomóc w rozwiązaniu problemu i rozwiązaniu go.
* Jeśli informacje w dziennikach po stronie serwera nie są wystarczające do pomyślnego rozwiązania problemu, można użyć dzienników po stronie klienta biblioteki klienta magazynu w celu zbadania zachowania aplikacji klienckiej i narzędzi, takich jak Fiddler, Wireshark i Microsoft Message Analyzer do zbadania sieci.

Aby uzyskać więcej informacji na temat używania fiddler, zobacz "[Dodatek 1: Używanie fiddlera do przechwytywania ruchu HTTP i HTTPS."]

Aby uzyskać więcej informacji na temat używania Wireshark, zobacz "[Dodatek 2: Używanie Wireshark do przechwytywania ruchu sieciowego."]

Aby uzyskać więcej informacji na temat korzystania z analizatora komunikatów firmy Microsoft, zobacz "[Dodatek 3: Przechwytywanie ruchu sieciowego za pomocą analizatora wiadomości firmy Microsoft."]

## <a name="appendices"></a><a name="appendices"></a>Dodatki
W dodatkach opisano kilka narzędzi, które mogą okazać się przydatne podczas diagnozowania i rozwiązywania problemów z usługą Azure Storage (i innymi usługami). Te narzędzia nie są częścią usługi Azure Storage, a niektóre są produktami innych firm. W związku z tym narzędzia omówione w tych dodatkach nie są objęte żadną umową pomocy technicznej, którą możesz zawrzeć z usługą Microsoft Azure lub usługa Azure Storage, dlatego w ramach procesu oceny należy zbadać opcje licencjonowania i pomocy technicznej dostępne od dostawców tych narzędzi.

### <a name="appendix-1-using-fiddler-to-capture-http-and-https-traffic"></a><a name="appendix-1"></a>Dodatek 1: Przechwytywanie ruchu HTTP i HTTPS za pomocą fiddlera
[Fiddler](https://www.telerik.com/fiddler) jest przydatnym narzędziem do analizowania ruchu HTTP i HTTPS między aplikacją kliencką a usługą magazynu platformy Azure, której używasz.

> [!NOTE]
> Fiddler może dekodować ruch HTTPS; należy uważnie przeczytać dokumentację Fiddler, aby zrozumieć, jak to robi, i zrozumieć implikacje dla bezpieczeństwa.
>
>

Ten dodatek zawiera krótki przewodnik jak skonfigurować Fiddler do przechwytywania ruchu między komputerem lokalnym, gdzie zainstalowano Fiddler i usługi magazynu platformy Azure.

Po uruchomieniu Fiddler rozpocznie przechwytywanie ruchu HTTP i HTTPS na komputerze lokalnym. Oto kilka przydatnych poleceń do sterowania Fiddler:

* Zatrzymaj i zacznij przechwytywać ruch. W menu głównym przejdź do **menu Plik,** a następnie kliknij pozycję **Przechwyć ruch,** aby włączyć i wyłączyć przechwytywanie.
* Zapisz przechwycone dane o ruchu drogowym. W menu głównym przejdź do **menu Plik**, kliknij pozycję **Zapisz**, a następnie kliknij pozycję **Wszystkie sesje**, co umożliwia zapisanie ruchu w pliku archiwum sesji. Można ponownie załadować archiwum sesji później do analizy lub wysłać je, jeśli zażąda się go do pomocy technicznej firmy Microsoft.

Aby ograniczyć ilość ruchu przechwytywania fiddler, można użyć filtrów skonfigurowanych na karcie **Filtry.** Poniższy zrzut ekranu przedstawia filtr, który przechwytuje tylko ruch wysyłany do punktu końcowego magazynu **contosoemaildist.table.core.windows.net:**

![][5]

### <a name="appendix-2-using-wireshark-to-capture-network-traffic"></a><a name="appendix-2"></a>Dodatek 2: Korzystanie z Wireshark do przechwytywania ruchu sieciowego
[Wireshark](https://www.wireshark.org/) to analizator protokołów sieciowych, który umożliwia wyświetlanie szczegółowych informacji o pakietach dla szerokiego zakresu protokołów sieciowych.

Poniższa procedura pokazuje, jak przechwycić szczegółowe informacje o pakietach dla ruchu z komputera lokalnego, na którym zainstalowano wireshark do usługi tabel na koncie usługi Azure storage.

1. Uruchom Wireshark na lokalnym komputerze.
2. W sekcji **Start** wybierz lokalny interfejs sieciowy lub interfejsy połączone z Internetem.
3. Kliknij pozycję **Opcje przechwytywania**.
4. Dodaj filtr do pola tekstowego **Filtr przechwytywania.** Na przykład **contosoemaildist.table.core.windows.net hosta** skonfiguruje Wireshark do przechwytywania tylko pakietów wysyłanych do lub z punktu końcowego usługi tabeli na koncie magazynu **contosoemaildist.** Zapoznaj się z [pełną listą filtrów przechwytywania](https://wiki.wireshark.org/CaptureFilters).

   ![][6]
5. Kliknij przycisk **Uruchom**. Wireshark będzie teraz przechwytywać wszystkie pakiety wysyłane do lub z punktu końcowego usługi tabeli podczas korzystania z aplikacji klienckiej na komputerze lokalnym.
6. Po zakończeniu w menu głównym kliknij polecenie **Przechwyć,** a następnie **zatrzymaj**.
7. Aby zapisać przechwycone dane w pliku przechwytywania Wireshark, w menu głównym kliknij polecenie **Plik,** a następnie **zapisz**.

WireShark podświetli wszelkie błędy, które istnieją w oknie **listy pakietów.** Można również użyć okna **Informacje eksperta** (kliknij przycisk **Analizuj,** a następnie **informacji eksperta),** aby wyświetlić podsumowanie błędów i ostrzeżeń.

![][7]

Można również wyświetlić dane TCP, ponieważ warstwa aplikacji widzi je, klikając prawym przyciskiem myszy dane TCP i wybierając **polecenie Obserwuj strumień TCP**. Jest to przydatne, jeśli zrzut został przechwycony bez filtru przechwytywania. Aby uzyskać więcej informacji, [zobacz: Obserwowanie strumieni TCP](https://www.wireshark.org/docs/wsug_html_chunked/ChAdvFollowTCPSection.html).

![][8]

> [!NOTE]
> Aby uzyskać więcej informacji na temat korzystania z wireshark, zobacz [Przewodnik użytkowników Wireshark](https://www.wireshark.org/docs/wsug_html_chunked).
>
>

### <a name="appendix-3-using-microsoft-message-analyzer-to-capture-network-traffic"></a><a name="appendix-3"></a>Dodatek 3: Przechwytywanie ruchu sieciowego za pomocą analizatora komunikatów firmy Microsoft
Za pomocą microsoft message analyzer można przechwytywać ruch HTTP i HTTPS w podobny sposób do Fiddler i przechwytywać ruch sieciowy w podobny sposób jak Wireshark.

#### <a name="configure-a-web-tracing-session-using-microsoft-message-analyzer"></a>Konfigurowanie sesji śledzenia sieci Web przy użyciu analizatora wiadomości firmy Microsoft
Aby skonfigurować sesję śledzenia sieci Web dla ruchu HTTP i HTTPS przy użyciu analizatora wiadomości firmy Microsoft, uruchom aplikację Microsoft Message Analyzer, a następnie w menu **Plik** kliknij polecenie **Przechwytywanie/śledzenie**. Na liście dostępnych scenariuszy śledzenia wybierz pozycję **Web Proxy**. Następnie w panelu **Konfiguracja śledzenia scenariusza** w pola tekstowym **HostnameFilter** dodaj nazwy punktów końcowych magazynu (możesz wyszukać te nazwy w [witrynie Azure portal).](https://portal.azure.com) Na przykład jeśli nazwa konta magazynu platformy Azure jest **contosodata**, należy dodać następujące do pola tekstowego **HostnameFilter:**

```
contosodata.blob.core.windows.net contosodata.table.core.windows.net contosodata.queue.core.windows.net
```

> [!NOTE]
> Znak spacji oddziela nazwy hostów.
>
>

Gdy będziesz gotowy do rozpoczęcia zbierania danych śledzenia, kliknij przycisk **Rozpocznij od.**

Aby uzyskać więcej informacji na temat śledzenia **serwera proxy sieci Web** analizatora wiadomości firmy Microsoft, zobacz Dostawca [microsoft-PEF-WebProxy](https://technet.microsoft.com/library/jj674814.aspx).

Wbudowany śledzenia **serwera proxy sieci Web** w analizatorze wiadomości firmy Microsoft jest oparty na Fiddler; może przechwytywać ruch HTTPS po stronie klienta i wyświetlać niezaszyfrowane wiadomości HTTPS. Śledzenie **serwera proxy sieci Web** działa przez skonfigurowanie lokalnego serwera proxy dla całego ruchu HTTP i HTTPS, który daje mu dostęp do niezaszyfrowanych wiadomości.

#### <a name="diagnosing-network-issues-using-microsoft-message-analyzer"></a>Diagnozowanie problemów z siecią przy użyciu analizatora wiadomości firmy Microsoft
Oprócz używania śledzenia serwera **proxy sieci Web** analizatora wiadomości firmy Microsoft do przechwytywania szczegółów ruchu HTTP/HTTPs między aplikacją kliencką a usługą magazynowania, można również użyć wbudowanego śledzenia **warstwy łącza lokalnego** do przechwytywania informacji o pakietach sieciowych. Dzięki temu można przechwytywać dane podobne do tych, które można przechwycić za pomocą Wireshark i diagnozować problemy z siecią, takie jak porzucone pakiety.

Na poniższym zrzucie ekranu przedstawiono przykładową warstwę **łącza lokalnego** śledzenia z niektórych komunikatów **informacyjnych** w **DiagnosisTypes** kolumny. Kliknięcie ikony w **diagnosistypes** kolumny pokazuje szczegóły wiadomości. W tym przykładzie komunikat retransmitowany serwera #305, ponieważ nie otrzymał potwierdzenia od klienta:

![][9]

Podczas tworzenia sesji śledzenia w microsoft message analyzer, można określić filtry, aby zmniejszyć ilość szumu w śledzenia. Na stronie **Przechwytywanie / Śledzenie,** gdzie można zdefiniować ślad, kliknij łącze **Konfiguruj** obok **microsoft-Windows-NDIS-PacketCapture**. Poniższy zrzut ekranu przedstawia konfigurację, która filtruje ruch TCP dla adresów IP trzech usług magazynu:

![][10]

Aby uzyskać więcej informacji na temat śledzenia warstwy łącza lokalnego analizatora wiadomości firmy Microsoft, zobacz [Dostawca pakietu Microsoft-PEF-NDIS-PacketCapture](https://technet.microsoft.com/library/jj659264.aspx).

### <a name="appendix-4-using-excel-to-view-metrics-and-log-data"></a><a name="appendix-4"></a>Dodatek 4: Wyświetlanie danych i rejestrowania danych za pomocą programu Excel
Wiele narzędzi umożliwia pobieranie danych metryk magazynu z magazynu tabel platformy Azure w formacie rozdzielanym, który ułatwia ładowanie danych do programu Excel w celu przeglądania i analizy. Dane rejestrowania magazynu z magazynu obiektów blob platformy Azure są już w formacie rozdzielanym, który można załadować do programu Excel. Należy jednak dodać odpowiednie nagłówki kolumn oparte na informacjach w [schemacie tabeli metryk analizy](https://msdn.microsoft.com/library/azure/hh343264.aspx) [danych magazynu](https://msdn.microsoft.com/library/azure/hh343259.aspx) i analizy magazynu .

Aby zaimportować dane rejestrowania magazynu do programu Excel po pobraniu ich z magazynu obiektów blob:

* W menu **Dane** kliknij polecenie **Od tekstu**.
* Przejdź do pliku dziennika, który chcesz wyświetlić, a następnie kliknij przycisk **Importuj**.
* W kroku 1 **Kreatora importu tekstu**wybierz pozycję **Rozdzielone**.

W kroku 1 **Kreatora importu tekstu**wybierz opcję **Średnik** jako jedyny ogranicznik i wybierz double-quote jako **kwalifikator tekstu**. Następnie kliknij przycisk **Zakończ** i wybierz miejsce umieszczenia danych w skoroszycie.

### <a name="appendix-5-monitoring-with-application-insights-for-azure-devops"></a><a name="appendix-5"></a>Dodatek 5: Monitorowanie za pomocą usługi Application Insights dla usługi Azure DevOps
Można również użyć funkcji usługi Application Insights dla usługi Azure DevOps jako część monitorowania wydajności i dostępności. To narzędzie może:

* Upewnij się, że usługa internetowa jest dostępna i responsywna. Niezależnie od tego, czy aplikacja jest witryną sieci Web, czy aplikacją urządzenia korzystającą z usługi sieci Web, może co kilka minut testować adres URL z lokalizacji na całym świecie i poinformować cię, czy wystąpił problem.
* Szybko diagnozuj wszelkie problemy z wydajnością lub wyjątki w usłudze sieci web. Dowiedz się, czy procesor CPU lub inne zasoby są rozciągnięte, uzyskać ślady stosu z wyjątków i łatwo przeszukiwać ślady dziennika. Jeśli wydajność aplikacji spadnie poniżej dopuszczalnych limitów, firma Microsoft może wysłać wiadomość e-mail. Można monitorować zarówno usługi sieci web .NET, jak i Java.

Więcej informacji można znaleźć na [temat Co to jest usługa Application Insights](../../azure-monitor/app/app-insights-overview.md).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat analizy w usłudze Azure Storage, zobacz następujące zasoby:

* [Monitorowanie konta magazynu w witrynie Azure Portal](storage-monitor-storage-account.md)
* [Analityka magazynu](storage-analytics.md)
* [Dane dotyczące analizy magazynu](storage-analytics-metrics.md)
* [Schemat tabeli metryk analizy magazynu](/rest/api/storageservices/storage-analytics-metrics-table-schema)
* [Dzienniki analityki magazynu](storage-analytics-logging.md)
* [Format dziennika analizy magazynu](/rest/api/storageservices/storage-analytics-log-format)

<!--Anchors-->
[Wprowadzenie]: #introduction
[Jak ten przewodnik jest zorganizowany]: #how-this-guide-is-organized

[Monitorowanie usługi pamięci masowej]: #monitoring-your-storage-service
[Monitorowanie kondycji usługi]: #monitoring-service-health
[Zdolność monitorowania]: #monitoring-capacity
[Monitorowanie dostępności]: #monitoring-availability
[Monitorowanie wydajności]: #monitoring-performance

[Diagnozowanie problemów z pamięcią masową]: #diagnosing-storage-issues
[Problemy ze zdrowiem usług]: #service-health-issues
[Problemy z wydajnością]: #performance-issues
[Diagnozowanie błędów]: #diagnosing-errors
[Problemy z emulatorem pamięci masowej]: #storage-emulator-issues
[Narzędzia do rejestrowania pamięci masowej]: #storage-logging-tools
[Korzystanie z narzędzi do rejestrowania sieci]: #using-network-logging-tools

[Śledzenie end-to-end]: #end-to-end-tracing
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
[Przejściowy wzrost PercentThrottlingError]: #transient-increase-in-PercentThrottlingError
[Stały wzrost błędu PercentThrottlingError]: #permanent-increase-in-PercentThrottlingError
[Metryki wskazują wzrost wartości PercentTimeoutError]: #metrics-show-an-increase-in-PercentTimeoutError
[Metryki wskazują wzrost wartości PercentNetworkError]: #metrics-show-an-increase-in-PercentNetworkError

[Klient odbiera komunikaty HTTP 403 (zabronione)]: #the-client-is-receiving-403-messages
[Klient odbiera komunikaty HTTP 404 (nie znaleziono)]: #the-client-is-receiving-404-messages
[Klient lub inny proces wcześniej usunął obiekt]: #client-previously-deleted-the-object
[Problem z autoryzacją sygnatury dostępu współdzielonego]: #SAS-authorization-issue
[Kod JavaScript po stronie klienta nie ma uprawnień dostępu do obiektu]: #JavaScript-code-does-not-have-permission
[Błąd sieci]: #network-failure
[Klient odbiera komunikaty HTTP 409 (konflikt)]: #the-client-is-receiving-409-messages

[Metryki pokazują niskie procentysukces lub wpisy dziennika analizy mają operacje ze stanem transakcji ClientOtherErrors]: #metrics-show-low-percent-success
[Metryki pojemności pokazują nieoczekiwany wzrost zużycia pojemności magazynu]: #capacity-metrics-show-an-unexpected-increase
[Problem wynika z używania emulatora pamięci masowej do tworzenia lub testowania]: #your-issue-arises-from-using-the-storage-emulator
[Funkcja "X" nie działa w emulatorze pamięci masowej]: #feature-X-is-not-working
[Błąd "Wartość jednego z nagłówków HTTP nie jest w prawidłowym formacie" podczas korzystania z emulatora magazynu]: #error-HTTP-header-not-correct-format
[Uruchamianie emulatora magazynu wymaga uprawnień administracyjnych]: #storage-emulator-requires-administrative-privileges
[Występują problemy z instalacją zestawu Azure SDK dla platformy .NET]: #you-are-encountering-problems-installing-the-Windows-Azure-SDK
[Masz inny problem z usługą magazynu]: #you-have-a-different-issue-with-a-storage-service

[Dodatki]: #appendices
[Dodatek 1: Przechwytywanie ruchu HTTP i HTTPS za pomocą fiddlera]: #appendix-1
[Dodatek 2: Korzystanie z Wireshark do przechwytywania ruchu sieciowego]: #appendix-2
[Dodatek 3: Przechwytywanie ruchu sieciowego za pomocą analizatora komunikatów firmy Microsoft]: #appendix-3
[Dodatek 4: Wyświetlanie danych i rejestrowania danych za pomocą programu Excel]: #appendix-4
[Dodatek 5: Monitorowanie za pomocą usługi Application Insights dla usługi Azure DevOps]: #appendix-5

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
