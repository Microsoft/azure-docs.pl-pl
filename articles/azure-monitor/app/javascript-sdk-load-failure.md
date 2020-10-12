---
title: Rozwiązywanie problemów z niepowodzeniem ładowania zestawu SDK dla aplikacji sieci Web w języku JavaScript — Azure Application Insights
description: Jak rozwiązywać problemy z niepowodzeniem ładowania zestawu SDK dla aplikacji sieci Web w języku JavaScript
ms.topic: conceptual
author: MSNev
ms.author: newylie
ms.date: 06/05/2020
ms.custom: devx-track-js
ms.openlocfilehash: 30c7caef4143b1a7cdba959971ff7689f986cb9e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91333260"
---
# <a name="troubleshooting-sdk-load-failure-for-javascript-web-apps"></a>Rozwiązywanie problemów z błędem ładowania zestawu SDK dla aplikacji sieci Web JavaScript

<!-- 
Editor Note: This link name above "SDK Load Failure" has a direct references by https://go.microsoft.com/fwlink/?linkid=2128109 which is embedded in the snippet and from the JavaScript Page. If you change this text you MUST also update these references.
-->

Wyjątek niepowodzenia ładowania zestawu SDK jest tworzony i raportowany przez fragment kodu JavaScript (V3 lub nowszy) w przypadku wykrycia, że pobranie lub zainicjowanie skryptu zestawu SDK nie powiodło się. Simplistically klient końcowy (przeglądarka) nie mógł pobrać zestawu SDK Application Insights ani zainicjować go ze wskazanej strony hostingu i w związku z tym nie będą raportowane dane telemetryczne ani zdarzenia.

![Przegląd błędów przeglądarki Azure Portal](./media/javascript-sdk-load-failure/overview.png)

> [!NOTE]
> Ten wyjątek jest obsługiwany we wszystkich głównych przeglądarkach obsługujących interfejs API pobierania () lub XMLHttpRequest. Spowoduje to wykluczenie programu IE 8 i poniżej, dlatego ten typ wyjątku raportowany przez te przeglądarki (chyba że środowisko zawiera wypełnienie).

![Szczegóły wyjątku przeglądarki](./media/javascript-sdk-load-failure/exception.png)

Szczegóły stosu obejmują podstawowe informacje o adresach URL używanych przez użytkownika końcowego.

| Nazwa                      | Opis                                                                                                  |
|---------------------------|--------------------------------------------------------------------------------------------------------------|
| &lt;&nbsp;Punkt końcowy usługi CDN&gt; | Adres URL, który został użyty (i nie powiódł się) do pobrania zestawu SDK.                                                      |
| &lt;&nbsp;Link pomocy&gt;    | Adres URL, który łączy się z dokumentacją dotyczącą rozwiązywania problemów (Ta strona).                                              |
| &lt;&nbsp;Adres URL hosta&gt;     | Pełny adres URL strony, z której korzysta użytkownik końcowy.                                                    |
| &lt;&nbsp;Adres URL punktu końcowego&gt; | Adres URL, który został użyty do raportowania wyjątku, ta wartość może być przydatna w identyfikacji, czy dostęp do strony hostingu z publicznej sieci Internet czy z chmury prywatnej.

Najczęstsze przyczyny tego wyjątku:

- Sporadyczny błąd łączności sieciowej.
- Application Insights awaria usługi CDN.
- Nie można zainicjować zestawu SDK po załadowaniu skryptu.
- Application Insights sieci CDN JavaScript została zablokowana.

[Sporadyczny błąd łączności sieciowej](#intermittent-network-connectivity-failure) to najbardziej typowy powód dla tego wyjątku, szczególnie w scenariuszach mobilnych mobilnej, w których użytkownicy sporadycznie tracą łączność sieciową.

W poniższych sekcjach opisano sposób rozwiązywania problemów z każdą potencjalną główną przyczyną tego błędu.

> [!NOTE]
> Kilka kroków rozwiązywania problemów zakłada, że aplikacja ma bezpośrednią kontrolę nad &lt; skryptem/ &gt; tagiem wstawki i konfiguracją, która jest zwracana w ramach hostingu strony html. Jeśli tego nie zrobisz, te zidentyfikowane kroki nie będą miały zastosowania w danym scenariuszu.

## <a name="intermittent-network-connectivity-failure"></a>Sporadyczny błąd łączności sieciowej

**Jeśli wystąpią problemy z nieprzerwaną łącznością sieciową, istnieje mniej możliwych rozwiązań i ogólnie rozwiążą się one w krótkim czasie.** Na przykład, jeśli użytkownik ponownie ładuje witrynę (Odświeża stronę), pliki będą (ostatecznie) pobierane i buforowane lokalnie do momentu wydania zaktualizowanej wersji.

> [!NOTE]
> Jeśli ten wyjątek jest trwały i występuje na wielu użytkownikach (zdiagnozowany przez szybki i trwały poziom tego wyjątku) wraz z zmniejszeniem normalnej telemetrii klienta, sporadyczne problemy z łącznością sieciową _nie mogą_ być przyczyną wystąpienia problemu i należy kontynuować diagnozowanie przy użyciu innych znanych problemów.

W tej sytuacji hostowanie zestawu SDK w ramach własnej sieci CDN jest mało prawdopodobne, aby zapewnić lub zmniejszyć liczbę wystąpień tego wyjątku, ponieważ ten sam problem ma wpływ na własną sieć CDN.

Ta sama wartość ma również zastosowanie w przypadku korzystania z zestawu SDK za pośrednictwem rozwiązania NPM Packages. Jednakże z punktu widzenia użytkowników końcowych w przypadku wystąpienia awarii cała aplikacja nie zostanie załadowana/zainicjowana (a nie _tylko_ zestaw danych telemetrycznych, który nie widzi wizualizacji), więc najprawdopodobniej odświeżenie witryny do momentu całkowitego załadowania.

Możesz również użyć [pakietów npm](#use-npm-packages-to-embed-the-application-insight-sdk) do osadzenia zestawu SDK Application Insights.

Aby zminimalizować Sporadyczne awarie łączności sieciowej, wdrożono Cache-Control nagłówki we wszystkich plikach usługi CDN, dzięki czemu gdy przeglądarka użytkownika końcowego pobrała bieżącą wersję zestawu SDK, nie będzie musiała być pobierana ponownie, a przeglądarka użyje wcześniej uzyskanej kopii (zobacz [jak działa buforowanie](../../cdn/cdn-how-caching-works.md)). Jeśli sprawdzanie pamięci podręcznej nie powiedzie się lub wystąpiło nowe wydanie, przeglądarka użytkownika końcowego będzie musiała pobrać zaktualizowaną wersję. W scenariuszu sprawdzania awarii może być widoczny poziom tła _"hałas"_ lub tymczasowy wzrost, gdy występuje Nowa wersja i jest on ogólnie dostępny (wdrożony w sieci CDN).
 
## <a name="application-insights-cdn-outage"></a>Application Insights awaria usługi CDN

Można potwierdzić, czy występuje Application Insights awaria usługi CDN, próbując uzyskać dostęp do punktu końcowego usługi CDN bezpośrednio z przeglądarki (na przykład https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js) z innej lokalizacji niż użytkownicy końcowi prawdopodobnie z własnej maszyny deweloperskiej (przy założeniu, że Twoja organizacja nie zablokował tej domeny).

Jeśli potwierdzisz awarię, możesz [utworzyć nowy bilet pomocy technicznej](https://azure.microsoft.com/support/create-ticket/) lub spróbować zmienić adres URL używany do pobrania zestawu SDK.

### <a name="change-the-cdn-endpoint"></a>Zmień punkt końcowy usługi CDN
  
Ponieważ fragment kodu i jego konfiguracja są zwracane przez aplikację jako część każdej wygenerowanej strony, można zmienić konfigurację fragmentu tak, `src` aby używała innego adresu URL dla zestawu SDK. Korzystając z tej metody, można ominąć zablokowany problem z siecią CDN, ponieważ nowy adres URL nie powinien być zablokowany.

Bieżąca Application Insights punkty końcowe usługi CDN zestawu SDK języka JavaScript
- `https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js`
- `https://js.monitor.azure.com/scripts/b/ai.2.min.js`

> [!NOTE]
> `https://js.monitor.azure.com/`Punkt końcowy jest aliasem umożliwiającym nam przełączanie się między dostawcami sieci CDN w ciągu około 5 minut, bez konieczności zmiany konfiguracji. Ma to na celu szybsze rozwiązywanie wykrytych problemów związanych z usługą CDN, jeśli dostawca sieci CDN ma problemy regionalne lub globalne bez konieczności dostosowywania ustawień przez wszystkich użytkowników.

## <a name="sdk-failed-to-initialize-after-loading-the-script"></a>Nie można zainicjować zestawu SDK po załadowaniu skryptu

Po nieudanym zainicjowaniu zestawu SDK &lt; skrypt/ &gt; został pomyślnie pobrany z sieci CDN, ale jego nie można zainicjować. Może to być spowodowane brakiem lub nieprawidłowymi zależnościami lub nieprawidłową formą wyjątku JavaScript.

Pierwszym krokiem, aby sprawdzić, czy zestaw SDK został pobrany pomyślnie, jeśli skrypt nie został pobrany, ten scenariusz __nie__ jest przyczyną wyjątku błędu ładowania zestawu SDK.

Szybkie sprawdzanie: korzystanie z przeglądarki, która obsługuje narzędzia deweloperskie (F12), zweryfikuj na karcie Sieć, czy skrypt zdefiniowany w ```src``` konfiguracji fragmentu został pobrany z kodem odpowiedzi 200 (powodzenie) lub 304 (nie zmieniono). Możesz również użyć narzędzia, takiego jak programu Fiddler, aby przejrzeć ruch sieciowy.

Poniższe sekcje zawierają różne opcje raportowania, dlatego zalecamy utworzenie biletu pomocy technicznej lub zgłoszenie problemu w serwisie GitHub.

 Podstawowe reguły raportowania:

- Jeśli problem ma wpływ tylko na niewielką liczbę użytkowników i konkretne lub podzbiór wersji przeglądarki (Sprawdź szczegóły zgłoszonego wyjątku), prawdopodobnie tylko użytkownik końcowy lub środowisko nie będzie miało problemu, co prawdopodobnie wymaga, aby aplikacja zapewniała dodatkowe `polyfill` implementacje. W tym celu należy [rozwiązać problem w witrynie GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues).
- Jeśli ten problem ma wpływ na całą aplikację, a wszyscy użytkownicy mają wpływ, prawdopodobnie jest to problem związany z wydaniem i w związku z tym należy [utworzyć nowy bilet pomocy technicznej](https://azure.microsoft.com/support/create-ticket/).

### <a name="javascript-exceptions"></a>Wyjątki języka JavaScript

Najpierw można sprawdzić obecność wyjątków JavaScript przy użyciu przeglądarki, która obsługuje narzędzia deweloperskie (F12), Załaduj stronę i sprawdź, czy wystąpiły wyjątki.

W przypadku zgłaszania wyjątków w skrypcie zestawu SDK (na przykład ai.2.min.js) może to wskazywać, że konfiguracja przekazana do zestawu SDK zawiera nieoczekiwaną lub brakującą konfigurację lub w usłudze CDN wdrożono wadliwą wersję.

Aby sprawdzić, czy konfiguracja jest błędna, należy zmienić konfigurację przekazaną do fragmentu (jeśli nie jest jeszcze), tak aby zawierała tylko klucz Instrumentacji jako wartość ciągu.

> src: " https://az416426.vo.msecnd.net/scripts/b/ai.2.min.js ",<br />
> cfg: {<br />
> instrumentationKey: "INSTRUMENTATION_KEY"<br />
> }});<br />

Jeśli podczas korzystania z tej minimalnej konfiguracji nadal widzisz wyjątek JavaScript w skrypcie zestawu SDK, [Utwórz nowy bilet pomocy technicznej](https://azure.microsoft.com/support/create-ticket/) , ponieważ będzie to wymagało wycofania nieprawidłowej kompilacji, ponieważ prawdopodobnie wystąpił problem z nowo wdrożoną wersją.

Jeśli wyjątek zniknie, przyczyną problemu jest prawdopodobnie niezgodność typów lub Nieoczekiwana wartość. Zacznij dodawać do siebie opcje konfiguracji pojedynczo i test, dopóki wyjątek nie wystąpi ponownie. Następnie zapoznaj się z dokumentacją dla elementu powodującego problem. Jeśli dokumentacja jest niejasne lub potrzebujesz pomocy, Zaproś o [problem w serwisie GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues).

Jeśli Twoja konfiguracja została wcześniej wdrożona i działa, ale właśnie została uruchomiona, to w przypadku zgłoszenia tego wyjątku może wystąpić problem z nowo wdrożoną wersją, sprawdzić, czy ma to wpływ tylko na niewielki zestaw użytkowników/przeglądarki, a także podać [problem w usłudze GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues) lub  [utworzyć nowy bilet pomocy technicznej](https://azure.microsoft.com/support/create-ticket/).

### <a name="enable-console-debugging"></a>Włącz debugowanie konsoli

Przy założeniu, że żadne wyjątki nie są zgłaszane, następnym krokiem jest włączenie debugowania konsoli przez dodanie `loggingLevelConsole` Ustawienia do konfiguracji. spowoduje to wysłanie wszystkich błędów inicjalizacji i ostrzeżeń do konsoli przeglądarki (zwykle dostępnej za pośrednictwem narzędzi programistycznych (F12)). Wszelkie zgłoszone błędy nie powinny mieć wyjaśnień i jeśli potrzebujesz dalszej pomocy, aby [zgłosić problem w usłudze GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues).

> cfg: {<br />
> instrumentationKey: "INSTRUMENTATION_KEY",<br />
> loggingLevelConsole: 2<br />
> }});<br />

> [!NOTE]
> Podczas inicjowania zestaw SDK wykonuje podstawowe testy dla znanych głównych zależności. Jeśli nie są one dostarczane przez bieżące środowisko uruchomieniowe, będzie zgłaszać błędy jako komunikaty ostrzegawcze do konsoli, ale tylko wtedy, gdy wartość `loggingLevelConsole` jest większa od zera.

Jeśli nadal nie można zainicjować, spróbuj włączyć ```enableDebug``` ustawienie konfiguracji. Spowoduje to, że wszystkie błędy wewnętrzne będą zgłaszane jako wyjątek (co spowoduje utratę danych telemetrycznych). Ponieważ jest to ustawienie tylko dla deweloperów, prawdopodobnie otrzymasz zakłócenia z wyjątkami, które są zgłaszane w ramach niektórych testów wewnętrznych, dlatego należy przejrzeć każdy wyjątek, aby ustalić, który problem powoduje niepowodzenie działania zestawu SDK. Użyj niezminimalizowanegoj wersji skryptu (Zwróć uwagę na rozszerzenie poniżej ". js", a nie ".min.js"), w przeciwnym razie wyjątki nie będą czytelne.

> [!WARNING]
> Jest to ustawienie tylko dla deweloperów i nigdy nie powinno być włączone w pełnym środowisku produkcyjnym, ponieważ spowoduje to utratę danych telemetrycznych.

> src: " https://az416426.vo.msecnd.net/scripts/b/ai.2.js ",<br />
> cfg: {<br />
> instrumentationKey: "INSTRUMENTATION_KEY",<br />
> enableDebug: true<br />
> }});<br />

Jeśli to nadal nie zapewnia żadnych szczegółowych informacji, należy [rozwiązać problem w serwisie GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues) , podając szczegóły i przykładową witrynę, jeśli ją masz. Zapoznaj się ze szczegółami dotyczącymi wersji przeglądarki, systemu operacyjnego i JS, aby ułatwić zidentyfikowanie problemu.

## <a name="the-application-insights-javascript-cdn-has-been-blocked"></a>Usługa CDN Application Insights JavaScript została zablokowana

Blokowanie usługi CDN jest możliwe, gdy zostanie zgłoszony i/lub określony jako niebezpieczny punkt końcowy usługi CDN Application Insights JavaScript SDK. Gdy tak się stanie, spowoduje to, że punkt końcowy będzie publiczny na liście, a użytkownicy tych list rozpoczną blokowanie całego dostępu.

Aby rozwiązać ten problem, wymaga, aby właściciel punktu końcowego usługi CDN pracował z jednostką z listą zablokowanych, która oznaczył punkt końcowy jako niebezpieczny, aby można go było usunąć z odpowiedniej listy.

Sprawdź, czy punkt końcowy usługi CDN został zidentyfikowany jako niebezpieczny.
- [Raport o przejrzystości Google](https://transparencyreport.google.com/safe-browsing/search)
- [VirusTotal](https://www.virustotal.com/gui/home/url)
- [Sucuri SiteCheck](https://sitecheck.sucuri.net/)

W zależności od częstotliwości, w której aplikacja, zapora lub środowisko aktualizuje lokalne kopie tych list, może zająć dużo czasu i/lub wymagać ręcznej interwencji przez użytkowników końcowych lub firmowych działów IT, aby wymusić aktualizację lub jawnie zezwolić na dostęp do punktów końcowych usługi CDN w celu rozwiązania problemu.

Jeśli punkt końcowy usługi CDN został zidentyfikowany jako niebezpieczny, [Utwórz bilet pomocy technicznej](https://azure.microsoft.com/support/create-ticket/) , aby upewnić się, że problem został rozwiązany tak szybko, jak to możliwe.

Aby *szybciej* ominąć ten problem, można [zmienić punkt końcowy usługi CDN zestawu SDK](#change-the-cdn-endpoint).

### <a name="application-insights-javascript-cdn-is-blocked-by-end-user---blocked-by-browser-installed-blocker-personal-firewall"></a>Application Insights sieć CDN JavaScript jest blokowana (przez użytkownika końcowego zablokowaną przez przeglądarkę; zainstalowany program blokujący; Zapora osobista)

Sprawdź, czy użytkownicy końcowi mają:

- Zainstalowano wtyczkę przeglądarki (zazwyczaj jest to pewna postać usługi AD, złośliwego oprogramowania lub blokowania wyskakujących okienek).
- Zablokowane (lub niedozwolone) Application Insights punkty końcowe usługi CDN w swojej przeglądarce lub na serwerze proxy.
- Skonfigurowano regułę zapory, która powoduje blokowanie domeny usługi CDN dla zestawu SDK (lub nie można rozpoznać wpisu DNS).

Jeśli skonfigurowano dowolne z tych opcji, trzeba będzie z nimi korzystać (lub podać dokumentację), aby umożliwić korzystanie z punktów końcowych usługi CDN.

Jest możliwe, że wtyczka, której zainstalowano, korzysta z listy blokowania publicznego. Jeśli tak się nie dzieje, jest to najprawdopodobniej inne ręcznie skonfigurowane rozwiązanie lub używa prywatnej domeny listy blokowania.

#### <a name="add-exceptions-for-cdn-endpoints"></a>Dodawanie wyjątków dla punktów końcowych usługi CDN

Pracuj z użytkownikami końcowymi lub dostarczaj dokumentację informującą o tym, że powinny zezwalać na pobieranie skryptów z punktów końcowych usługi CDN Application Insights przez uwzględnienie ich na liście wyjątków lub reguł zapory w przeglądarce (w zależności od środowiska użytkownika końcowego).

Oto przykład sposobu konfigurowania [programu Chrome w celu zezwalania na dostęp lub blokowania go w witrynach sieci Web.](https://support.google.com/chrome/a/answer/7532419?hl=en)

### <a name="application-insights-cdn-is-blocked-by-corporate-firewall"></a>Application Insights sieć CDN jest blokowana (przez zaporę firmową)

Jeśli użytkownicy końcowi znajdują się w sieci firmowej, najprawdopodobniej jest to rozwiązanie zapory i że dział IT zaimplementował pewną formę systemu filtrowania internetowego. W takim przypadku trzeba będzie z nimi korzystać, aby zezwolić na wymagane reguły dla użytkowników końcowych.

#### <a name="add-exceptions-for-cdn-endpoints-for-corporations"></a>Dodawanie wyjątków dla punktów końcowych usługi CDN dla firm

  Jest to podobne do [dodawania wyjątków dla użytkowników końcowych](#add-exceptions-for-cdn-endpoints), ale należy skontaktować się z DZIAŁem informatycznym firmy, aby skonfigurować Application Insights punkty końcowe usługi CDN do pobrania przez włączenie (lub usunięcie) ich z dowolnego bloku domeny lub usług list dozwolonych.

  > [!WARNING]
  > Jeśli użytkownik firmowy korzysta z [chmury prywatnej](https://azure.microsoft.com/overview/what-is-a-private-cloud/) i nie może włączyć żadnej formy wyjątku w celu zapewnienia użytkownikom wewnętrznym dostępu publicznego dla punktów końcowych usługi CDN, należy użyć [Application Insights pakietów npm](https://www.npmjs.com/package/applicationinsights) lub hostować zestaw Application INSIGHTS SDK w usłudze CDN.  

### <a name="additional-troubleshooting-for-blocked-cdn"></a>Dodatkowe Rozwiązywanie problemów dotyczących zablokowanej sieci CDN

> [!NOTE]
> Jeśli użytkownicy korzystają z [chmury prywatnej](https://azure.microsoft.com/overview/what-is-a-private-cloud/) i nie mają dostępu do publicznej sieci Internet, konieczne będzie HOSTOWANIE zestawu SDK we własnej sieci CDN lub korzystanie z pakietów npm.

#### <a name="host-the-sdk-on-your-own-cdn"></a>Hostowanie zestawu SDK w ramach własnej sieci CDN

 Użytkownicy końcowi nie pobierają zestawu SDK Application Insights z publicznej sieci CDN, który może być hostem Application Insights SDK z własnego punktu końcowego usługi CDN. Zaleca się użycie określonej wersji (AI. 2. #. # .min.js), aby ułatwić identyfikację używanej wersji. Należy również regularnie aktualizować je do bieżącej wersji (ai.2.min.js), aby można było korzystać z dowolnych poprawek błędów i nowych funkcji, które staną się dostępne.

#### <a name="use-npm-packages-to-embed-the-application-insight-sdk"></a>Osadź zestaw SDK usługi Application Insights za pomocą pakietów NPM

Zamiast używać fragmentów kodu i publicznych punktów końcowych usługi CDN, możesz użyć [pakietów npm](https://www.npmjs.com/package/applicationinsights) do dołączenia zestawu SDK w ramach własnych plików JavaScript. Zestaw SDK stanie się tylko innym pakietem w ramach własnych skryptów.

> [!NOTE]
> Zaleca się, aby w przypadku używania pakietów NPM należy również użyć pewnej formy [pakietu języka JavaScript](https://www.bing.com/search?q=javascript+bundler) do ułatwienia dzielenia kodu i minifikacja.

Podobnie jak w przypadku fragmentu kodu, istnieje również możliwość, że własne skrypty (z pakietami SDK NPM lub bez nich) mogą mieć wpływ te same problemy z blokowaniem, w zależności od aplikacji, użytkowników i platformy, dlatego warto rozważyć wdrożenie czegoś podobnego do logiki w fragmencie kodu, aby wykryć i zgłosić te problemy.


## <a name="next-steps"></a>Następne kroki 
- [Uzyskaj dodatkową pomoc, zgłaszając problem w usłudze GitHub](https://github.com/Microsoft/ApplicationInsights-JS/issues)
- [Monitorowanie użycia stron sieci Web](javascript.md)
