---
title: Przechowywanie i przechowywanie danych w usłudze Azure Application Insights | Microsoft Docs
description: Zasady przechowywania i zasad zachowania poufności informacji
ms.topic: conceptual
ms.date: 06/30/2020
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 6a7545d4d927418e6153ce3bf439fc8c3e2dc702
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104774919"
---
# <a name="data-collection-retention-and-storage-in-application-insights"></a>Zbieranie, przechowywanie i magazynowanie danych w usłudze Application Insights

Po zainstalowaniu zestawu [Azure Application Insights][start] SDK w aplikacji wysyła ona dane telemetryczne dotyczące aplikacji do chmury. W naturalny sposób zainteresowani deweloperzy chcą dokładnie wiedzieć, jakie dane są wysyłane, co się dzieje z danymi i jak mogą one zachować kontrolę nad nimi. W szczególności mogą być wysyłane poufne dane, gdzie są przechowywane i jak są bezpieczne? 

Najpierw krótka odpowiedź:

* Standardowe moduły telemetrii, które działają "poza Box", prawdopodobnie nie będą wysyłać poufnych danych do usługi. Dane telemetryczne są powiązane z metrykami obciążenia, wydajności i użycia, raportami o wyjątkach i innymi danymi diagnostycznymi. Główne dane użytkownika widoczne w raportach diagnostycznych są adresami URL; Jednak aplikacja nie powinna w żadnym przypadku umieszczać poufnych danych w postaci zwykłego tekstu w adresie URL.
* Można napisać kod, który wysyła dodatkowe niestandardowe dane telemetryczne, aby ułatwić diagnostykę i użycie monitorowania. (To rozszerzalność to świetna funkcja Application Insights.) Można to zrobić przez pomyłkę, aby napisać ten kod w taki sposób, aby zawierał dane osobowe i inne poufne. Jeśli aplikacja współpracuje z takimi danymi, należy zastosować proces dokładnego przeglądu do całego kodu, który napiszesz.
* Podczas tworzenia i testowania aplikacji można łatwo sprawdzić, co jest wysyłane przez zestaw SDK. Dane są wyświetlane w oknach wyjściowych debugowania środowiska IDE i przeglądarki.
* Lokalizację można wybrać podczas tworzenia nowego zasobu Application Insights. Dowiedz się więcej o dostępności Application Insights [na region.](https://azure.microsoft.com/global-infrastructure/services/?products=all)
*   Przejrzyj zebrane dane, ponieważ mogą one obejmować dane, które są dozwolone w pewnych okolicznościach, ale nie w innych.  Dobrym przykładem jest nazwa urządzenia. Nazwa urządzenia z serwera nie ma wpływu na prywatność i jest przydatna, ale nazwa urządzenia z telefonu lub laptopu może mieć wpływ na prywatność i być mniej przydatna. Zestaw SDK opracowany głównie dla serwerów docelowych, domyślnie zbiera nazwę urządzenia i może być zastąpiony w normalnych zdarzeniach i wyjątkach.

W dalszej części tego artykułu szczegółowo opracowano te odpowiedzi. Jest ona zaprojektowana jako samodzielna, aby można było ją pokazać współpracownikom, którzy nie są częścią swojego bezpośredniego zespołu.

## <a name="what-is-application-insights"></a>Co to jest usługa Application Insights?
[Azure Application Insights][start] to usługa świadczona przez firmę Microsoft, która pomaga w ulepszaniu wydajności i użyteczności działającej aplikacji. Monitoruje aplikację przez cały czas jej działania, zarówno podczas testowania, jak i po jej opublikowaniu lub wdrożeniu. Application Insights tworzy wykresy i tabele, które pokazują Ci, na przykład pory dnia, w jaki każdy dzień otrzymuje większość użytkowników, jak reaguje na aplikację i jak jest obsługiwany przez wszystkie zewnętrzne usługi, od których zależy. W przypadku awarii, błędów lub problemów z wydajnością można wyszukać szczegółowe dane telemetryczne, aby zdiagnozować przyczynę. A usługa wyśle Ci wiadomości e-mail w przypadku zmiany dostępności i wydajności aplikacji.

Aby można było skorzystać z tej funkcji, należy zainstalować zestaw SDK Application Insights w aplikacji, który będzie częścią kodu. Gdy aplikacja jest uruchomiona, zestaw SDK monitoruje swoją operację i wysyła dane telemetryczne do usługi Application Insights. Jest to usługa w chmurze hostowana przez [Microsoft Azure](https://azure.com). (Ale Application Insights działa dla wszystkich aplikacji, a nie tylko aplikacji hostowanych na platformie Azure).

Usługa Application Insights przechowuje i analizuje dane telemetryczne. Aby wyświetlić analizę lub przeszukać dane telemetryczne przechowywane, zaloguj się do konta platformy Azure i Otwórz zasób Application Insights aplikacji. Dostęp do danych można również udostępnić innym członkom zespołu lub określonym subskrybentom platformy Azure.

Dane można eksportować z usługi Application Insights, na przykład do bazy danych lub narzędzi zewnętrznych. Każdemu narzędziu można udostępnić klucz specjalny uzyskany od usługi. Klucz można odwołać w razie potrzeby. 

Zestawy SDK Application Insights są dostępne dla różnych typów aplikacji: usług sieci Web hostowanych we własnych serwerach Java EE lub ASP.NET lub na platformie Azure; Klienci sieci Web — to oznacza kod uruchomiony na stronie sieci Web; aplikacje i usługi pulpitu; aplikacje urządzenia, takie jak Windows Phone, iOS i Android. Wszystkie dane telemetryczne są wysyłane do tej samej usługi.

## <a name="what-data-does-it-collect"></a>Jakie dane są zbierane?
Istnieją trzy źródła danych:

* Zestaw SDK, który można zintegrować z aplikacją [w trakcie programowania](./asp-net.md) lub w [czasie wykonywania](./monitor-performance-live-website-now.md). Istnieją różne zestawy SDK dla różnych typów aplikacji. Istnieje również [zestaw SDK dla stron sieci Web](./javascript.md), które są ładowane do przeglądarki użytkownika końcowego wraz ze stroną.
  
  * Każdy zestaw SDK ma wiele [modułów](./configuration-with-applicationinsights-config.md), które używają różnych technik do zbierania różnych typów danych telemetrycznych.
  * Jeśli instalujesz zestaw SDK w programie Development, możesz użyć jego interfejsu API do wysyłania własnej telemetrii (oprócz modułów standardowych). Ta niestandardowa Telemetria może zawierać dowolne dane, które chcesz wysłać.
* Na niektórych serwerach sieci Web są również agenci, którzy uruchamiają się wraz z aplikacją i wysyłają dane telemetryczne dotyczące użycia procesora CPU, pamięci i sieci. Na przykład serwery Azure Virtual Machines, Docker Hosts i [Java EE](./java-agent.md) mogą mieć takich agentów.
* [Testy dostępności](./monitor-web-app-availability.md) to procesy uruchamiane przez firmę Microsoft, które wysyłają żądania do aplikacji sieci Web w regularnych odstępach czasu. Wyniki są wysyłane do usługi Application Insights.

### <a name="what-kinds-of-data-are-collected"></a>Jakie rodzaje danych są zbierane?
Główne kategorie to:

* Dane [telemetryczne serwera sieci Web](./asp-net.md) — żądania HTTP.  Identyfikator URI, czas trwania przetwarzania żądania, kod odpowiedzi, adres IP klienta. `Session id`.
* [Strony sieci Web](./javascript.md) — liczba stron, użytkowników i sesji. Czasy ładowania strony. Wyłączenia. Wywołania AJAX.
* Liczniki wydajności — pamięć, procesor CPU, we/wy, zajętość sieci.
* Kontekst klienta i serwera — system operacyjny, ustawienia regionalne, typ urządzenia, przeglądarka, rozdzielczość ekranu.
* [Wyjątki](./asp-net-exceptions.md) i awarie — **zrzuty stosu**, `build id` Typ procesora CPU. 
* [Zależności](./asp-net-dependencies.md) — wywołania usług zewnętrznych, takich jak REST, SQL i AJAX. Identyfikator URI lub parametry połączenia, czas trwania, powodzenie, polecenie.
* [Testy dostępności](./monitor-web-app-availability.md) — czas trwania testu i kroków, odpowiedzi.
* [Dzienniki śledzenia](./asp-net-trace-logs.md) i [niestandardowa Telemetria](./api-custom-events-metrics.md)—  -  **wszystko, co jest używane w dziennikach lub danych telemetrycznych**.

[Więcej szczegółów](#data-sent-by-application-insights).

## <a name="how-can-i-verify-whats-being-collected"></a>Jak mogę sprawdzić, co jest zbierane?
Jeśli tworzysz aplikację przy użyciu programu Visual Studio, uruchom aplikację w trybie debugowania (F5). Dane telemetryczne pojawiają się w oknie danych wyjściowych. Z tego miejsca możesz skopiować go i sformatować jako kod JSON w celu zapewnienia łatwej inspekcji. 

![Zrzut ekranu pokazujący uruchomioną aplikację w trybie debugowania w programie Visual Studio.](./media/data-retention-privacy/06-vs.png)

W oknie Diagnostyka znajduje się również bardziej czytelny widok.

W przypadku stron sieci Web otwórz okno debugowanie przeglądarki.

![Naciśnij klawisz F12 i Otwórz kartę Sieć.](./media/data-retention-privacy/08-browser.png)

### <a name="can-i-write-code-to-filter-the-telemetry-before-it-is-sent"></a>Czy mogę napisać kod, aby odfiltrować dane telemetryczne przed wysłaniem?
Jest to możliwe dzięki napisaniu [wtyczki procesora telemetrii](./api-filtering-sampling.md).

## <a name="how-long-is-the-data-kept"></a>Jak długo są przechowywane dane?
Pierwotne punkty danych (czyli elementy, które można badać w analizie i inspekcji wyszukiwania) są przechowywane przez maksymalnie 730 dni. Można [wybrać okres przechowywania](./pricing.md#change-the-data-retention-period) równy 30, 60, 90, 120, 180, 270, 365, 550 lub 730. Jeśli chcesz przechowywać dane dłużej niż 730 dni, możesz użyć [eksportu ciągłego](./export-telemetry.md) , aby skopiować go do konta magazynu podczas pozyskiwania danych. 

Dane przechowywane dłużej niż 90 dni spowodują naliczenie opłat za dodawanie. Dowiedz się więcej o cenach Application Insights na [stronie cennika Azure monitor](https://azure.microsoft.com/pricing/details/monitor/).

Zagregowane dane (czyli liczniki, średnie i inne dane statystyczne widoczne w Eksploratorze metryk) są zachowywane w ciągu 1 minuty przez 90 dni.

[Migawki debugowania](./snapshot-debugger.md) są przechowywane przez 15 dni. Te zasady przechowywania są ustawiane dla poszczególnych aplikacji. Jeśli musisz zwiększyć tę wartość, możesz poprosić o zwiększenie, otwierając przypadek pomocy technicznej w Azure Portal.

## <a name="who-can-access-the-data"></a>Kto może uzyskać dostęp do danych?
Dane są widoczne dla Ciebie i, jeśli masz konto organizacji, członków zespołu. 

Mogą być eksportowane przez Ciebie i członków zespołu i mogą być kopiowane do innych lokalizacji i przesyłane do innych osób.

#### <a name="what-does-microsoft-do-with-the-information-my-app-sends-to-application-insights"></a>Do czego służy firma Microsoft z informacjami wysyłanymi przez aplikację do Application Insights?
Firma Microsoft używa danych tylko w celu udostępnienia usługi.

## <a name="where-is-the-data-held"></a>Gdzie są przechowywane dane?
* Lokalizację można wybrać podczas tworzenia nowego zasobu Application Insights. Dowiedz się więcej o dostępności Application Insights [na region.](https://azure.microsoft.com/global-infrastructure/services/?products=all)

## <a name="how-secure-is-my-data"></a>Jak bezpieczne są moje dane?
Application Insights to usługa platformy Azure. Zasady zabezpieczeń są opisane w [oficjalny dokument dotyczący zabezpieczeń, ochrony prywatności i zgodności z platformą Azure](https://go.microsoft.com/fwlink/?linkid=392408).

Dane są przechowywane na serwerach Microsoft Azure. W przypadku kont w Azure Portal ograniczenia konta są opisane w [dokumencie zabezpieczenia, prywatność i zgodność platformy Azure](https://go.microsoft.com/fwlink/?linkid=392408).

Dostęp do danych przez personel firmy Microsoft jest ograniczony. Dostęp do danych odbywa się tylko przy użyciu uprawnień użytkownika, a jeśli jest to konieczne, aby obsługiwać korzystanie z Application Insights. 

Dane zagregowane dla wszystkich naszych aplikacji klientów (takie jak stawki danych i średni rozmiar śladów) są używane do ulepszania Application Insights.

#### <a name="could-someone-elses-telemetry-interfere-with-my-application-insights-data"></a>Czy dane telemetryczne innej osoby mogą przeszkadzać w danych Application Insights?
Mogą oni wysyłać dodatkowe dane telemetryczne do konta za pomocą klucza instrumentacji, który można znaleźć w kodzie stron sieci Web. Dzięki wystarczającej ilości dodatkowych danych metryki nie będą poprawnie przedstawiać wydajności i użycia aplikacji.

Jeśli współużytkujesz kod z innymi projektami, pamiętaj o usunięciu klucza Instrumentacji.

## <a name="is-the-data-encrypted"></a>Czy dane są szyfrowane?
Wszystkie dane są szyfrowane w czasie spoczynku i przenoszone między centrami danych.

#### <a name="is-the-data-encrypted-in-transit-from-my-application-to-application-insights-servers"></a>Czy dane są szyfrowane podczas przesyłania z mojej aplikacji do Application Insights serwerów?
Tak, protokół HTTPS jest używany do wysyłania danych do portalu z niemal wszystkich zestawów SDK, w tym serwerów sieci Web, urządzeń i stron sieci Web HTTPS. 

## <a name="does-the-sdk-create-temporary-local-storage"></a>Czy zestaw SDK tworzy tymczasowy magazyn lokalny?

Tak, niektóre kanały telemetrii będą utrzymywać dane lokalnie, jeśli nie można nawiązać połączenia z punktem końcowym. Zapoznaj się z poniższymi tematami, aby zobaczyć, które platformy i kanały telemetrii mają oddziaływać.

Kanały telemetrii używające lokalnego magazynu tworzą pliki tymczasowe w katalogach TEMP lub APPDATA, które są ograniczone do określonego konta, na którym działa aplikacja. Może się tak zdarzyć, gdy punkt końcowy był tymczasowo niedostępny lub osiągnięto limit ograniczania. Po rozwiązaniu tego problemu kanał telemetrii zostanie wznowiony, wysyłając wszystkie nowe i utrwalone dane.

Te utrwalone dane nie są szyfrowane lokalnie. Jeśli jest to problem, Przejrzyj dane i Ogranicz zbieranie danych prywatnych. (Aby uzyskać więcej informacji, zobacz [Jak eksportować i usuwać dane prywatne](../logs/personal-data-mgmt.md#how-to-export-and-delete-private-data)).

Jeśli klient musi skonfigurować ten katalog z określonymi wymaganiami dotyczącymi zabezpieczeń, można go skonfigurować na platformę. Upewnij się, że proces z uruchomioną aplikacją ma dostęp do zapisu w tym katalogu, ale upewnij się również, że ten katalog jest chroniony, aby uniknąć odczytania danych telemetrycznych przez niezamierzonych użytkowników.

### <a name="java"></a>Java

`C:\Users\username\AppData\Local\Temp` służy do utrwalania danych. Ta lokalizacja nie jest konfigurowalna z katalogu konfiguracji, a uprawnienia dostępu do tego folderu są ograniczone do określonego użytkownika z wymaganymi poświadczeniami. (Aby uzyskać więcej informacji, zobacz [implementacja](https://github.com/Microsoft/ApplicationInsights-Java/blob/40809cb6857231e572309a5901e1227305c27c1a/core/src/main/java/com/microsoft/applicationinsights/internal/util/LocalFileSystemUtils.java#L48-L72)).

###  <a name="net"></a>.NET

Domyślnie `ServerTelemetryChannel` używa folderu danych lokalnej aplikacji `%localAppData%\Microsoft\ApplicationInsights` lub folderu tymczasowego bieżącego użytkownika `%TMP%` . (Zobacz tutaj [implementację](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) ).


Plik konfiguracji:
```xml
<TelemetryChannel Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel,   Microsoft.AI.ServerTelemetryChannel">
    <StorageFolder>D:\NewTestFolder</StorageFolder>
</TelemetryChannel>
```

Za pośrednictwem kodu:

- Usuń ServerTelemetryChannel z pliku konfiguracji
- Dodaj następujący fragment kodu do konfiguracji:
  ```csharp
  ServerTelemetryChannel channel = new ServerTelemetryChannel();
  channel.StorageFolder = @"D:\NewTestFolder";
  channel.Initialize(TelemetryConfiguration.Active);
  TelemetryConfiguration.Active.TelemetryChannel = channel;
  ```

### <a name="netcore"></a>NetCore

Domyślnie `ServerTelemetryChannel` używa folderu danych lokalnej aplikacji `%localAppData%\Microsoft\ApplicationInsights` lub folderu tymczasowego bieżącego użytkownika `%TMP%` . (Zobacz tutaj [implementację](https://github.com/Microsoft/ApplicationInsights-dotnet/blob/91e9c91fcea979b1eec4e31ba8e0fc683bf86802/src/ServerTelemetryChannel/Implementation/ApplicationFolderProvider.cs#L54-L84) ). 

W środowisku systemu Linux magazyn lokalny zostanie wyłączony, chyba że zostanie określony folder magazynu.

> [!NOTE]
> W przypadku wersji 2.15.0-beta3 i nowszej magazyn lokalny jest teraz automatycznie tworzony dla systemów Linux, Mac i Windows. W przypadku systemów innych niż Windows zestaw SDK automatycznie utworzy lokalny folder magazynu na podstawie następującej logiki:
> - `${TMPDIR}` -Jeśli `${TMPDIR}` zmienna środowiskowa jest ustawiona, Ta lokalizacja jest używana.
> - `/var/tmp` — Jeśli poprzednia lokalizacja nie istnieje, spróbuj ponownie `/var/tmp` .
> - `/tmp` — Jeśli obie poprzednie lokalizacje nie istnieją, spróbujmy `tmp` . 
> - Jeśli żadna z tych lokalizacji nie istnieje, Magazyn lokalny nie jest tworzony, a Konfiguracja ręczna jest nadal wymagana. [Aby uzyskać pełne szczegóły implementacji](https://github.com/microsoft/ApplicationInsights-dotnet/pull/1860).

Poniższy fragment kodu pokazuje, jak ustawić `ServerTelemetryChannel.StorageFolder` w `ConfigureServices()` metodzie `Startup.cs` klasy:

```csharp
services.AddSingleton(typeof(ITelemetryChannel), new ServerTelemetryChannel () {StorageFolder = "/tmp/myfolder"});
```

(Aby uzyskać więcej informacji, zobacz [Konfiguracja niestandardowa AspNetCore](https://github.com/Microsoft/ApplicationInsights-aspnetcore/wiki/Custom-Configuration)).

### <a name="nodejs"></a>Node.js

Domyślnie `%TEMP%/appInsights-node{INSTRUMENTATION KEY}` jest używany do utrwalania danych. Uprawnienia dostępu do tego folderu są ograniczone do bieżącego użytkownika i administratorów. (Zobacz tutaj [implementację](https://github.com/Microsoft/ApplicationInsights-node.js/blob/develop/Library/Sender.ts) ).

Prefiks folderu `appInsights-node` można przesłonić, zmieniając wartość w czasie wykonywania zmiennej statycznej `Sender.TEMPDIR_PREFIX` znalezionej w elemencie [Sender. TS](https://github.com/Microsoft/ApplicationInsights-node.js/blob/7a1ecb91da5ea0febf5ceab13d6a4bf01a63933d/Library/Sender.ts#L384).

### <a name="javascript-browser"></a>JavaScript (przeglądarka)

[Magazyn sesji HTML5](https://developer.mozilla.org/en-US/docs/Web/API/Window/sessionStorage) służy do utrwalania danych. Używane są dwa oddzielne bufory: `AI_buffer` i `AI_sent_buffer` . Dane telemetryczne, które są przetwarzane w partii i oczekujące na wysłanie, są przechowywane w `AI_buffer` . Dane telemetryczne, które zostały właśnie wysłane, są umieszczane `AI_sent_buffer` do momentu, gdy serwer pozyskiwania odpowie, że został pomyślnie odebrany. Gdy dane telemetryczne zostały pomyślnie odebrane, zostaną usunięte ze wszystkich buforów. W przypadku błędów przejściowych (na przykład jeśli użytkownik utraci łączność z siecią), dane telemetryczne pozostają w programie `AI_buffer` do momentu jego pomyślnego odebrania lub serwer pozyskiwania odpowie, że dane telemetryczne są nieprawidłowe (na przykład zły schemat lub zbyt stary).

Bufory telemetrii można wyłączyć, ustawiając wartość [`enableSessionStorageBuffer`](https://github.com/microsoft/ApplicationInsights-JS/blob/17ef50442f73fd02a758fbd74134933d92607ecf/legacy/JavaScript/JavaScriptSDK.Interfaces/IConfig.ts#L31) na `false` . Gdy magazyn sesji jest wyłączony, tablica lokalna jest używana jako magazyn trwały. Ponieważ zestaw JavaScript SDK działa na urządzeniu klienckim, użytkownik ma dostęp do tej lokalizacji magazynu za pomocą narzędzi deweloperskich w przeglądarce.

### <a name="opencensus-python"></a>Zestaw OpenCensus Python

Domyślnie OpenCensus Python SDK używa bieżącego folderu użytkownika `%username%/.opencensus/.azure/` . Uprawnienia dostępu do tego folderu są ograniczone do bieżącego użytkownika i administratorów. (Zobacz tutaj [implementację](https://github.com/census-instrumentation/opencensus-python/blob/master/contrib/opencensus-ext-azure/opencensus/ext/azure/common/storage.py) ). Folder z danymi utrwalanymi zostanie nazwany po pliku języka Python, który wygenerował dane telemetryczne.

Lokalizację pliku magazynu można zmienić, przekazując `storage_path` parametr w konstruktorze używanego przez eksportera.

```python
AzureLogHandler(
  connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000',
  storage_path='<your-path-here>',
)
```

## <a name="how-do-i-send-data-to-application-insights-using-tls-12"></a>Jak mogę wysyłać dane do Application Insights przy użyciu protokołu TLS 1,2?

Aby zapewnić bezpieczeństwo danych przesyłanych do Application Insightsych punktów końcowych, zdecydowanie zachęcamy klientów do konfigurowania aplikacji do korzystania z co najmniej Transport Layer Security (TLS) 1,2. Starsza wersja protokołu TLS/Secure Sockets Layer (SSL) została uznana za narażoną, a mimo to nadal pracują w celu zapewnienia zgodności z poprzednimi wersjami, nie jest to **zalecane**, a branża szybko przenosi się do porzucenia, aby uzyskać pomoc techniczną dla tych starszych protokołów. 

[Rada normy zabezpieczeń PCI](https://www.pcisecuritystandards.org/) ustawił [termin ostateczny 30 czerwca 2018,](https://www.pcisecuritystandards.org/pdfs/PCI_SSC_Migrating_from_SSL_and_Early_TLS_Resource_Guide.pdf) aby wyłączyć starsze wersje protokołu TLS/SSL i uaktualnić je do bezpieczniejsze protokoły. Gdy platforma Azure pozostanie w starszej wersji, jeśli aplikacja/klienci nie mogą komunikować się za pomocą co najmniej protokołu TLS 1,2, nie będzie możliwe wysyłanie danych do Application Insights. Podejście wykonywane do testowania i weryfikowania obsługi protokołu TLS aplikacji będzie się różnić w zależności od systemu operacyjnego/platformy, a także od języka/platformy używanej przez aplikację.

Firma Microsoft nie zaleca jawnie ustawienia aplikacji do używania protokołu TLS 1,2, chyba że jest to konieczne, ponieważ może to spowodować przerwanie funkcji zabezpieczeń na poziomie platformy, która umożliwia automatyczne wykrywanie i korzystanie z nowszych bezpieczniejsze protokołów, które staną się dostępne, takich jak TLS 1,3. Zalecamy przeprowadzenie szczegółowej inspekcji kodu aplikacji w celu sprawdzenia zakodowana określonych wersji protokołu TLS/SSL.

### <a name="platformlanguage-specific-guidance"></a>Wskazówki dotyczące platformy/języka

|Platforma/język | Pomoc techniczna | Więcej informacji |
| --- | --- | --- |
| Azure App Services  | Obsługiwane, może być wymagana konfiguracja. | Obsługa została ogłoszona w kwietniu 2018. Zapoznaj się z ogłoszeniem, aby uzyskać [szczegółowe informacje o konfiguracji](https://azure.github.io/AppService/2018/04/17/App-Service-and-Functions-hosted-apps-can-now-update-TLS-versions!).  |
| Aplikacje funkcji platformy Azure | Obsługiwane, może być wymagana konfiguracja. | Obsługa została ogłoszona w kwietniu 2018. Zapoznaj się z ogłoszeniem, aby uzyskać [szczegółowe informacje o konfiguracji](https://azure.github.io/AppService/2018/04/17/App-Service-and-Functions-hosted-apps-can-now-update-TLS-versions!). |
|.NET | Obsługiwane, konfiguracja zależy od wersji. | Aby uzyskać szczegółowe informacje o konfiguracji dla programu .NET 4,7 i jego wcześniejszych wersji, zobacz [te instrukcje](/dotnet/framework/network-programming/tls#support-for-tls-12).  |
|Monitor stanu | Obsługiwane, wymagana konfiguracja | Monitor stanu korzysta z [](/windows-server/security/tls/tls-registry-settings)  +  [konfiguracji platformy .NET](/dotnet/framework/network-programming/tls#support-for-tls-12) konfiguracji systemu operacyjnego w celu zapewnienia obsługi protokołu TLS 1,2.
|Node.js |  Obsługiwane w programie v 10.5.0 może być wymagana konfiguracja. | Użyj [oficjalnej Node.js protokołu TLS/SSL](https://nodejs.org/api/tls.html) dla każdej konfiguracji specyficznej dla aplikacji. |
|Java | Obsługiwane, JDK support for TLS 1,2 zostało dodane w [JDK 6 update 121](https://www.oracle.com/technetwork/java/javase/overview-156328.html#R160_121) i [JDK 7](https://www.oracle.com/technetwork/java/javase/7u131-relnotes-3338543.html). | JDK 8 [domyślnie używa protokołu TLS 1,2](https://blogs.oracle.com/java-platform-group/jdk-8-will-use-tls-12-as-default).  |
|Linux | Dystrybucje systemu Linux zależą od [OpenSSL](https://www.openssl.org) obsługi TLS 1,2.  | Sprawdź [Dziennik zmian OpenSSL](https://www.openssl.org/news/changelog.html) , aby potwierdzić, że wersja OpenSSL jest obsługiwana.|
| Windows 8,0 — 10 | Obsługiwane i domyślnie włączone. | , Aby upewnić się, że nadal używasz [ustawień domyślnych](/windows-server/security/tls/tls-registry-settings).  |
| Windows Server 2012 — 2016 | Obsługiwane i domyślnie włączone. | Aby potwierdzić, że nadal używasz [ustawień domyślnych](/windows-server/security/tls/tls-registry-settings) |
| Windows 7 z dodatkiem SP1 i Windows Server 2008 R2 z dodatkiem SP1 | Obsługiwane, ale nie włączone domyślnie. | Aby uzyskać szczegółowe informacje na temat włączania, zobacz stronę [Ustawienia rejestru Transport Layer Security (TLS)](/windows-server/security/tls/tls-registry-settings) .  |
| Windows Server 2008 SP2 | Obsługa protokołu TLS 1,2 wymaga aktualizacji. | Zobacz [Aktualizacja, aby dodać obsługę protokołu TLS 1,2](https://support.microsoft.com/help/4019276/update-to-add-support-for-tls-1-1-and-tls-1-2-in-windows-server-2008-s) w systemie Windows Server 2008 z dodatkiem SP2. |
|Windows Vista | Nieobsługiwane. | Nie dotyczy

### <a name="check-what-version-of-openssl-your-linux-distribution-is-running"></a>Sprawdź wersję OpenSSL, w której działa dystrybucja systemu Linux

Aby sprawdzić zainstalowaną wersję programu OpenSSL, Otwórz Terminal i uruchom polecenie:

```terminal
openssl version -a
```

### <a name="run-a-test-tls-12-transaction-on-linux"></a>Uruchamianie testowej transakcji TLS 1,2 w systemie Linux

Aby uruchomić wstępny test, aby sprawdzić, czy system Linux może komunikować się za pośrednictwem protokołu TLS 1,2. Otwórz Terminal i uruchom polecenie:

```terminal
openssl s_client -connect bing.com:443 -tls1_2
```

## <a name="personal-data-stored-in-application-insights"></a>Dane osobowe przechowywane w Application Insights

Nasz [artykuł dotyczący danych osobowych w Application Insights](../logs/personal-data-mgmt.md) zawiera szczegółowe omówienie tego problemu.

#### <a name="can-my-users-turn-off-application-insights"></a>Czy moi użytkownicy mogą wyłączyć Application Insights?
Nie bezpośrednio. Nie udostępniamy przełącznika, który użytkownicy mogą wykonywać, aby wyłączyć Application Insights.

Można jednak zaimplementować takie funkcje w aplikacji. Wszystkie zestawy SDK zawierają ustawienie interfejsu API, które powoduje wyłączenie zbierania danych telemetrycznych. 

## <a name="data-sent-by-application-insights"></a>Dane wysyłane przez Application Insights
Zestawy SDK różnią się między platformami i istnieje kilka składników, które można zainstalować. (Zobacz [Application Insights — przegląd][start]). Każdy składnik wysyła różne dane.

#### <a name="classes-of-data-sent-in-different-scenarios"></a>Klasy danych wysyłanych w różnych scenariuszach

| Twoja akcja | Zebrane klasy danych (zobacz następną tabelę) |
| --- | --- |
| [Dodawanie Application Insights SDK do projektu sieci Web platformy .NET][greenbrown] |ServerContext<br/>Wywnioskować<br/>Liczniki wydajności<br/>Żądania<br/>**Wyjątki**<br/>Sesja<br/>users |
| [Instalowanie monitor stanu w usługach IIS][redfield] |Zależności<br/>ServerContext<br/>Wywnioskować<br/>Liczniki wydajności |
| [Dodawanie Application Insights SDK do aplikacji sieci Web Java][java] |ServerContext<br/>Wywnioskować<br/>Żądanie<br/>Sesja<br/>users |
| [Dodaj zestaw SDK JavaScript do strony sieci Web][client] |Klasa ClientContext <br/>Wywnioskować<br/>Strona<br/>ClientPerf<br/>AJAX |
| [Definiowanie właściwości domyślnych][apiproperties] |**Właściwości** wszystkich zdarzeń standardowych i niestandardowych |
| [TrackMetric wywołań][api] |Wartości liczbowe<br/>**Właściwości** |
| [Śledzenie wywołań *][api] |Nazwa zdarzenia<br/>**Właściwości** |
| [Wywołanie metody Trackexception][api] |**Wyjątki**<br/>Zrzut stosu<br/>**Właściwości** |
| Zestaw SDK nie może zbierać danych. Na przykład: <br/> -nie można uzyskać dostępu do liczników wydajności<br/> -wyjątek w inicjatorze telemetrii |Diagnostyka zestawu SDK |

W przypadku [zestawów SDK dla innych platform][platforms]Zobacz dokumenty.

#### <a name="the-classes-of-collected-data"></a>Klasy zebranych danych

| Klasa zebranych danych | Zawiera (nie jest to pełna lista) |
| --- | --- |
| **Właściwości** |**Wszystkie dane — określone przez kod** |
| DeviceContext |`Id`, IP, ustawienia regionalne, model urządzenia, Sieć, typ sieci, nazwa OEM, rozdzielczość ekranu, wystąpienie roli, nazwa roli, typ urządzenia |
| Klasa ClientContext |System operacyjny, ustawienia regionalne, język, Sieć, rozdzielczość okna |
| Sesja |`session id` |
| ServerContext |Nazwa komputera, ustawienia regionalne, system operacyjny, urządzenie, sesja użytkownika, kontekst użytkownika, operacja |
| Wywnioskować |Lokalizacja geograficzna z adresu IP, sygnatury czasowej, systemu operacyjnego, przeglądarki |
| Metryki |Nazwa i wartość metryki |
| Zdarzenia |Nazwa i wartość zdarzenia |
| PageViews |Adres URL i nazwa strony lub nazwa ekranu |
| Wydajność klienta |Nazwa adresu URL/strony, czas ładowania przeglądarki |
| AJAX |Wywołania HTTP ze strony sieci Web na serwer |
| Żądania |Adres URL, czas trwania, kod odpowiedzi |
| Zależności |Typ (SQL, HTTP,...), parametry połączenia lub identyfikator URI, synchronizacja/Async, czas trwania, powodzenie, instrukcja SQL (z monitor stanu) |
| **Wyjątki** |Typ, **komunikat**, stosy wywołań, plik źródłowy, numer wiersza, `thread id` |
| Stąp |`Process id`, `parent process id` , `crash thread id` ; poprawka aplikacji, `id` , kompilacja;  Typ wyjątku, adres, Przyczyna; zasłonięte symbole i rejestry, binarne adresy startowe i końcowe, nazwa binarna i ścieżka, typ procesora |
| Ślad |**Komunikat** i poziom ważności |
| Liczniki wydajności |Czas procesora, dostępna pamięć, szybkość żądania, szybkość wyjątku, bajty prywatne procesu, szybkość operacji we/wy, czas żądania, Długość kolejki żądań |
| Dostępność |Kod odpowiedzi testu sieci Web, czas trwania każdego kroku testu, nazwa testu, sygnatura czasowa, sukces, czas odpowiedzi, lokalizacja testu |
| Diagnostyka zestawu SDK |Komunikat lub wyjątek śledzenia |

[Niektóre dane można wyłączyć, edytując ApplicationInsights.config][config]

> [!NOTE]
> Adres IP klienta jest używany do wywnioskowania lokalizacji geograficznej, ale domyślnie dane IP nie są już przechowywane i wszystkie zera są zapisywane w skojarzonym polu. Aby dowiedzieć się więcej na temat obsługi danych osobowych, zalecamy korzystanie z tego [artykułu](../logs/personal-data-mgmt.md#application-data). Jeśli zachodzi potrzeba zapisania danych adresów IP w [artykule dotyczącym zbierania adresów IP](./ip-collection.md) , przeprowadzisz Cię przez opcje.

## <a name="can-i-modify-or-update-data-after-it-has-been-collected"></a>Czy mogę modyfikować lub aktualizować dane po ich zebraniu?

Nie, dane są tylko do odczytu i można je usunąć tylko za pomocą funkcji przeczyszczania. Aby dowiedzieć się więcej [, odwiedź wskazówki dotyczące danych osobowych przechowywanych w log Analytics i Application Insights](../logs/personal-data-mgmt.md#delete).

## <a name="credits"></a>Środki
Ten produkt zawiera dane GeoLite2 utworzone przez MaxMind, dostępne z [https://www.maxmind.com](https://www.maxmind.com) .



<!--Link references-->

[api]: ./api-custom-events-metrics.md
[apiproperties]: ./api-custom-events-metrics.md#properties
[client]: ./javascript.md
[config]: ./configuration-with-applicationinsights-config.md
[greenbrown]: ./asp-net.md
[java]: ./java-get-started.md
[platforms]: ./platforms.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
[redfield]: ./monitor-performance-live-website-now.md
[start]: ./app-insights-overview.md

