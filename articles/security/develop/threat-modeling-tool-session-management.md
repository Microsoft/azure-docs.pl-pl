---
title: Zarządzanie sesją — Microsoft Threat Modeling Tool na platformie Azure | Microsoft Docs
description: Dowiedz się więcej o rozwiązywaniu problemów z zarządzaniem sesją dla zagrożeń ujawnionych w Threat Modeling Tool. Zobacz informacje o ograniczeniach i Wyświetl przykłady kodu.
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
ms.custom: has-adal-ref, devx-track-js, devx-track-csharp
ms.openlocfilehash: a1f4d4a3bb78da82753d651e1a73cf244096d5df
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94518105"
---
# <a name="security-frame-session-management"></a>Ramka zabezpieczeń: zarządzanie sesjami
| Produkt/usługa | Artykuł |
| --------------- | ------- |
| **Azure AD**    | <ul><li>[Implementowanie prawidłowego logowania przy użyciu metod ADAL w przypadku korzystania z usługi Azure AD](#logout-adal)</li></ul> |
| **Urządzenie IoT** | <ul><li>[Używanie skończonego okresu istnienia dla wygenerowanych tokenów SaS](#finite-tokens)</li></ul> |
| **Baza danych dokumentów platformy Azure** | <ul><li>[Użyj minimalnych okresów istnienia tokenu dla wygenerowanych tokenów zasobów](#resource-tokens)</li></ul> |
| **ADFS** | <ul><li>[Zaimplementuj odpowiednie wylogowanie przy użyciu metod WsFederation w przypadku korzystania z usług AD FS](#wsfederation-logout)</li></ul> |
| **Serwer tożsamości** | <ul><li>[Zaimplementuj właściwe wylogowanie w przypadku korzystania z serwera tożsamości](#proper-logout)</li></ul> |
| **Aplikacja sieci Web** | <ul><li>[Aplikacje dostępne za pośrednictwem protokołu HTTPS muszą używać bezpiecznych plików cookie](#https-secure-cookies)</li><li>[Cała aplikacja oparta na protokole HTTP powinna określać protokół http tylko dla definicji pliku cookie](#cookie-definition)</li><li>[Rozwiązywanie problemów związanych z fałszerstwem żądań między witrynami (CSRF) na stronach sieci Web ASP.NET](#csrf-asp)</li><li>[Konfigurowanie sesji dla okresu istnienia braku aktywności](#inactivity-lifetime)</li><li>[Implementowanie prawidłowego wylogowywania z aplikacji](#proper-app-logout)</li></ul> |
| **Interfejs API sieci Web** | <ul><li>[Rozwiązywanie problemów związanych z fałszerstwem żądań między witrynami (CSRF) na ASP.NETch interfejsów API sieci Web](#csrf-api)</li></ul> |

## <a name="implement-proper-logout-using-adal-methods-when-using-azure-ad"></a><a id="logout-adal"></a>Implementowanie prawidłowego logowania przy użyciu metod ADAL w przypadku korzystania z usługi Azure AD

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure AD | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Jeśli aplikacja korzysta z tokenu dostępu wystawionego przez usługę Azure AD, procedura obsługi zdarzeń wylogowywania powinna wywołać |

### <a name="example"></a>Przykład
```csharp
HttpContext.GetOwinContext().Authentication.SignOut(OpenIdConnectAuthenticationDefaults.AuthenticationType, CookieAuthenticationDefaults.AuthenticationType)
```

### <a name="example"></a>Przykład
Powinien również zniszczyć sesję użytkownika przez wywołanie metody Session. Abandon (). W poniższej metodzie przedstawiono bezpieczną implementację wylogowywania użytkownika:
```csharp
    [HttpPost]
        [ValidateAntiForgeryToken]
        public void LogOff()
        {
            string userObjectID = ClaimsPrincipal.Current.FindFirst("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
            AuthenticationContext authContext = new AuthenticationContext(Authority + TenantId, new NaiveSessionCache(userObjectID));
            authContext.TokenCache.Clear();
            Session.Clear();
            Session.Abandon();
            Response.SetCookie(new HttpCookie("ASP.NET_SessionId", string.Empty));
            HttpContext.GetOwinContext().Authentication.SignOut(
                OpenIdConnectAuthenticationDefaults.AuthenticationType,
                CookieAuthenticationDefaults.AuthenticationType);
        } 
```

## <a name="use-finite-lifetimes-for-generated-sas-tokens"></a><a id="finite-tokens"></a>Używanie skończonego okresu istnienia dla wygenerowanych tokenów SaS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Urządzenie IoT | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Tokeny sygnatury dostępu współdzielonego wygenerowane na potrzeby uwierzytelniania na platformie Azure IoT Hub powinny mieć ograniczony okres ważności. Należy zachować okres istnienia tokenu sygnatury dostępu współdzielonego jako minimum, aby ograniczyć czas, w którym można je odtworzyć w przypadku naruszenia zabezpieczeń tokenów.|

## <a name="use-minimum-token-lifetimes-for-generated-resource-tokens"></a><a id="resource-tokens"></a>Użyj minimalnych okresów istnienia tokenu dla wygenerowanych tokenów zasobów

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Baza danych dokumentów platformy Azure | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Zmniejsz wartość przedziału czasu tokenu zasobu do wymaganej wartości minimalnej. Tokeny zasobów mają domyślny czas przedziału czasu wynoszący 1 godzinę.|

## <a name="implement-proper-logout-using-wsfederation-methods-when-using-adfs"></a><a id="wsfederation-logout"></a>Zaimplementuj odpowiednie wylogowanie przy użyciu metod WsFederation w przypadku korzystania z usług AD FS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | ADFS | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Jeśli aplikacja korzysta z tokenu usługi STS wystawionego przez usługi AD FS, procedura obsługi zdarzeń wylogowywania powinna wywołać metodę WSFederationAuthenticationModule. FederatedSignOut () w celu wylogowania użytkownika. Należy również zniszczyć bieżącą sesję, a wartość tokenu sesji powinna być resetowana i nullified.|

### <a name="example"></a>Przykład
```csharp
        [HttpPost, ValidateAntiForgeryToken]
        [Authorization]
        public ActionResult SignOut(string redirectUrl)
        {
            if (!this.User.Identity.IsAuthenticated)
            {
                return this.View("LogOff", null);
            }

            // Removes the user profile.
            this.Session.Clear();
            this.Session.Abandon();
            HttpContext.Current.Response.Cookies.Add(new System.Web.HttpCookie("ASP.NET_SessionId", string.Empty)
                {
                    Expires = DateTime.Now.AddDays(-1D),
                    Secure = true,
                    HttpOnly = true
                });

            // Signs out at the specified security token service (STS) by using the WS-Federation protocol.
            Uri signOutUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Issuer);
            Uri replyUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Realm);
            if (!string.IsNullOrEmpty(redirectUrl))
            {
                replyUrl = new Uri(FederatedAuthentication.WSFederationAuthenticationModule.Realm + redirectUrl);
            }
           //     Signs out of the current session and raises the appropriate events.
            var authModule = FederatedAuthentication.WSFederationAuthenticationModule;
            authModule.SignOut(false);
        //     Signs out at the specified security token service (STS) by using the WS-Federation
        //     protocol.            
            WSFederationAuthenticationModule.FederatedSignOut(signOutUrl, replyUrl);
            return new RedirectResult(redirectUrl);
        }
```

## <a name="implement-proper-logout-when-using-identity-server"></a><a id="proper-logout"></a>Zaimplementuj właściwe wylogowanie w przypadku korzystania z serwera tożsamości

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Serwer tożsamości | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [IdentityServer3 — wylogowywanie federacyjne](https://identityserver.github.io/Documentation/docsv2/advanced/federated-signout.html) |
| **Kroki** | IdentityServer obsługuje możliwość sfederować z zewnętrznymi dostawcami tożsamości. Gdy użytkownik wyloguje się z nadrzędnego dostawcy tożsamości, w zależności od używanego protokołu może być możliwe otrzymywanie powiadomienia po wylogowaniu się użytkownika. Umożliwia IdentityServer do powiadamiania klientów, aby mogli również podpisać użytkownika. Zapoznaj się z dokumentacją w sekcji References, aby poznać szczegóły implementacji.|

## <a name="applications-available-over-https-must-use-secure-cookies"></a><a id="https-secure-cookies"></a>Aplikacje dostępne za pośrednictwem protokołu HTTPS muszą używać bezpiecznych plików cookie

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | EnvironmentType-lokalnego |
| **Odwołania**              | [httpCookies, element (Schemat ustawień ASP.NET)](/previous-versions/dotnet/netframework-4.0/ms228262(v=vs.100)), [Właściwość HttpCookie. Secure](/dotnet/api/system.web.httpcookie.secure) |
| **Kroki** | Pliki cookie są zwykle dostępne tylko dla domeny, dla której zostały objęte zakresem. Niestety definicja "domena" nie zawiera protokołu, więc pliki cookie, które są tworzone za pośrednictwem protokołu HTTPS, są dostępne za pośrednictwem protokołu HTTP. Atrybut "Secure" wskazuje przeglądarki, że plik cookie powinien być udostępniany tylko za pośrednictwem protokołu HTTPS. Upewnij się, że wszystkie pliki cookie ustawione za pośrednictwem protokołu HTTPS używają **bezpiecznego** atrybutu. Wymagania można wymusić w pliku web.config, ustawiając atrybut requireSSL na true. Jest to preferowane podejście, ponieważ będzie wymuszać **bezpieczny** atrybut dla wszystkich bieżących i przyszłych plików cookie bez konieczności wprowadzania jakichkolwiek dodatkowych zmian w kodzie.|

### <a name="example"></a>Przykład
```csharp
<configuration>
  <system.web>
    <httpCookies requireSSL="true"/>
  </system.web>
</configuration>
```
Ustawienie jest wymuszane nawet wtedy, gdy do uzyskiwania dostępu do aplikacji jest używany protokół HTTP. Jeśli protokół HTTP jest używany do uzyskiwania dostępu do aplikacji, to ustawienie powoduje przerwanie aplikacji, ponieważ pliki cookie są ustawiane z bezpiecznym atrybutem, a przeglądarka nie wyśle ich z powrotem do aplikacji.

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Formularze sieci Web, MVC5 |
| **Atrybuty**              | EnvironmentType-lokalnego |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Gdy aplikacja sieci Web jest jednostką uzależnioną, a dostawcy tożsamości to serwer ADFS, bezpieczny atrybut tokenu FedAuth można skonfigurować przez ustawienie requireSSL na true w `system.identityModel.services` sekcji web.config:|

### <a name="example"></a>Przykład
```csharp
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:20:0" />
    ....  
    </federationConfiguration>
  </system.identityModel.services>
```

## <a name="all-http-based-application-should-specify-http-only-for-cookie-definition"></a><a id="cookie-definition"></a>Cała aplikacja oparta na protokole HTTP powinna określać protokół http tylko dla definicji pliku cookie

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Atrybut bezpiecznego pliku cookie](https://en.wikipedia.org/wiki/HTTP_cookie#Secure_cookie) |
| **Kroki** | W celu ograniczenia ryzyka ujawnienia informacji za pomocą ataku z wykorzystaniem skryptów między lokacjami (XSS) nowy atrybut-httpOnly-został wprowadzony do plików cookie i jest obsługiwany przez wszystkie główne przeglądarki. Ten atrybut określa, że plik cookie nie jest dostępny za pomocą skryptu. Dzięki użyciu plików cookie HttpOnly aplikacja sieci Web zmniejsza możliwość kradzieży poufnych informacji zawartych w pliku cookie za pośrednictwem skryptu i wysyłanych do witryny sieci Web osoby atakującej. |

### <a name="example"></a>Przykład
Wszystkie aplikacje oparte na protokole HTTP, które używają plików cookie, powinny określać HttpOnly w definicji pliku cookie przez implementację następującej konfiguracji w web.config:
```XML
<system.web>
.
.
   <httpCookies requireSSL="false" httpOnlyCookies="true"/>
.
.
</system.web>
```

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Formularze sieci Web |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [FormsAuthentication. RequireSSL — Właściwość](/dotnet/api/system.web.security.formsauthentication.requiressl) |
| **Kroki** | Wartość właściwości RequireSSL jest ustawiana w pliku konfiguracji dla aplikacji ASP.NET przy użyciu atrybutu requireSSL elementu Configuration. Można określić w pliku Web.config dla aplikacji ASP.NET, czy Transport Layer Security (TLS), wcześniej znana jako SSL (Secure Sockets Layer), musi zwrócić plik cookie uwierzytelniania formularzy do serwera przez ustawienie atrybutu requireSSL.|

### <a name="example"></a>Przykład 
Poniższy przykład kodu ustawia atrybut requireSSL w pliku Web.config.
```XML
<authentication mode="Forms">
  <forms loginUrl="member_login.aspx" cookieless="UseCookies" requireSSL="true"/>
</authentication>
```

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | MVC5 |
| **Atrybuty**              | EnvironmentType-lokalnego |
| **Odwołania**              | [Konfiguracja Windows Identity Foundation (WIF) — część II](/archive/blogs/alikl/windows-identity-foundation-wif-configuration-part-ii-cookiehandler-chunkedcookiehandler-customcookiehandler) |
| **Kroki** | Aby ustawić atrybut httpOnly dla plików cookie FedAuth, wartość atrybutu hideFromCsript powinna być ustawiona na wartość true. |

### <a name="example"></a>Przykład
Poniższa konfiguracja przedstawia poprawną konfigurację:
```XML
<federatedAuthentication>
<cookieHandler mode="Custom"
                       hideFromScript="true"
                       name="FedAuth"
                       path="/"
                       requireSsl="true"
                       persistentSessionLifetime="25">
</cookieHandler>
</federatedAuthentication>
```

## <a name="mitigate-against-cross-site-request-forgery-csrf-attacks-on-aspnet-web-pages"></a><a id="csrf-asp"></a>Rozwiązywanie problemów związanych z fałszerstwem żądań między witrynami (CSRF) na stronach sieci Web ASP.NET

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Fałszerstwo żądania między lokacjami (CSRF lub XSRF) jest typem ataku, w którym osoba atakująca może wykonywać działania w kontekście zabezpieczeń sesji określonej przez inną użytkownika w witrynie sieci Web. Celem jest zmodyfikowanie lub usunięcie zawartości, jeśli docelowa witryna sieci Web opiera się wyłącznie na plikach cookie sesji w celu uwierzytelnienia odebranego żądania. Osoba atakująca może wykorzystać tę lukę w zabezpieczeniach, korzystając z przeglądarki innego użytkownika w celu załadowania adresu URL za pomocą polecenia z zagrożonej witryny, na której użytkownik jest już zalogowany. Istnieje wiele sposobów na wykonanie tej czynności przez osobę atakującą, na przykład przez hostowanie innej witryny sieci Web, która ładuje zasób z zagrożonego serwera, lub uzyskanie użytkownika w celu kliknięcia linku. Atak może być niemożliwy, jeśli serwer wysyła dodatkowy token do klienta programu, program wymaga, aby klient dołączył ten token we wszystkich przyszłych żądaniach i sprawdza, czy wszystkie przyszłe żądania zawierają token odnoszący się do bieżącej sesji, na przykład przy użyciu ASP.NET AntiForgeryToken lub ViewState. |

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | MVC5, MVC6 |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Zapobieganie atakom XSRF/CSRF we wzorcach ASP.NET MVC i Web Pages](https://www.asp.net/mvc/overview/security/xsrfcsrf-prevention-in-aspnet-mvc-and-web-pages) |
| **Kroki** | Formularze CSRF i ASP.NET MVC — Użyj `AntiForgeryToken` metody pomocnika w widokach; Umieść `Html.AntiForgeryToken()` w formularzu, na przykład|

### <a name="example"></a>Przykład
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
```

### <a name="example"></a>Przykład
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Przykład
W tym samym czasie plik HTML. AntiForgeryToken () zapewnia odwiedzającemu plik cookie o nazwie __RequestVerificationToken, z taką samą wartością jak losowo Ukryta wartość pokazana powyżej. Następnie, aby sprawdzić poprawność post formularzy przychodzących, Dodaj filtr [ValidateAntiForgeryToken] do docelowej metody akcji. Na przykład:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Filtr autoryzacji, który sprawdza, czy:
* Żądanie przychodzące ma plik cookie o nazwie __RequestVerificationToken
* Żądanie przychodzące zawiera `Request.Form` wpis o nazwie __RequestVerificationToken
* Te pliki cookie i `Request.Form` wartości są zgodne z założeniem, że wszystkie są prawidłowe. W przeciwnym razie błąd autoryzacji z komunikatem "wymagany token chroniący przed fałszerstwem nie został dostarczony lub był nieprawidłowy". 

### <a name="example"></a>Przykład
Anti-CSRF i AJAX: token formy może być problemem w przypadku żądań AJAX, ponieważ żądanie AJAX może wysyłać dane JSON, a nie dane formularza HTML. Jednym z rozwiązań jest wysłanie tokenów w niestandardowym nagłówku HTTP. Poniższy kod używa składnia Razor do wygenerowania tokenów, a następnie dodaje tokeny do żądania AJAX. 
```csharp
<script>
    @functions{
        public string TokenHeaderValue()
        {
            string cookieToken, formToken;
            AntiForgery.GetTokens(null, out cookieToken, out formToken);
            return cookieToken + ":" + formToken;                
        }
    }

    $.ajax("api/values", {
        type: "post",
        contentType: "application/json",
        data: {  }, // JSON data goes here
        dataType: "json",
        headers: {
            'RequestVerificationToken': '@TokenHeaderValue()'
        }
    });
</script>
```

### <a name="example"></a>Przykład
Podczas przetwarzania żądania Wyodrębnij tokeny z nagłówka żądania. Następnie Wywołaj metodę unfałszerstwa. Validate, aby sprawdzić poprawność tokenów. Metoda Validate zgłasza wyjątek, jeśli tokeny są nieprawidłowe.
```csharp
void ValidateRequestHeader(HttpRequestMessage request)
{
    string cookieToken = "";
    string formToken = "";

    IEnumerable<string> tokenHeaders;
    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
    {
        string[] tokens = tokenHeaders.First().Split(':');
        if (tokens.Length == 2)
        {
            cookieToken = tokens[0].Trim();
            formToken = tokens[1].Trim();
        }
    }
    AntiForgery.Validate(cookieToken, formToken);
}
```

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Formularze sieci Web |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Korzystanie z wbudowanych funkcji ASP.NET w celu Fend ataków z sieci Web](/previous-versions/dotnet/articles/ms972969(v=msdn.10)#securitybarriers_topic2) |
| **Kroki** | Ataki CSRF w aplikacjach opartych na webformach można wyeliminować, ustawiając ViewStateUserKey na losowy ciąg, który zmienia się dla każdego użytkownika — identyfikatora użytkownika lub, jeszcze lepiej, identyfikatora sesji. W przypadku wielu przyczyn technicznych i społecznościowych identyfikator sesji jest znacznie lepszy, ponieważ identyfikator sesji jest nieprzewidywalny, przekracza limit czasu i zależy od poszczególnych użytkowników.|

### <a name="example"></a>Przykład
Oto kod, który musisz mieć na wszystkich stronach:
```csharp
void Page_Init (object sender, EventArgs e) {
   ViewStateUserKey = Session.SessionID;
   :
}
```

## <a name="set-up-session-for-inactivity-lifetime"></a><a id="inactivity-lifetime"></a>Konfigurowanie sesji dla okresu istnienia braku aktywności

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [HttpSessionState. Timeout — właściwość](/dotnet/api/system.web.sessionstate.httpsessionstate.timeout) |
| **Kroki** | Limit czasu sesji reprezentuje zdarzenie występujące, gdy użytkownik nie wykonuje żadnych akcji w witrynie sieci Web w ciągu interwału (zdefiniowanego przez serwer sieci Web). Po stronie serwera Zmień stan sesji użytkownika na "nieprawidłowy" (na przykład "nieużywane już") i poinstruuj serwer sieci Web, aby go zniszczyć (usuwając wszystkie zawarte w nim dane). Poniższy przykład kodu ustawia wartość atrybutu Session Timeout na 15 minut w pliku Web.config.|

### <a name="example"></a>Przykład
```XML 
<configuration>
  <system.web>
    <sessionState mode="InProc" cookieless="true" timeout="15" />
  </system.web>
</configuration>
```

## <a name="enable-threat-detection-on-azure-sql"></a><a id="threat-detection"></a>Włączanie wykrywania zagrożeń w usłudze Azure SQL

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Formularze sieci Web |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Element formularzy do uwierzytelniania (Schemat ustawień ASP.NET)](/previous-versions/dotnet/netframework-4.0/1d3t3c61(v=vs.100)) |
| **Kroki** | Ustaw limit czasu plików cookie biletu uwierzytelniania formularzy na 15 minut|

### <a name="example"></a>Przykład
```XML
<forms  name=".ASPXAUTH" loginUrl="login.aspx"  defaultUrl="default.aspx" protection="All" timeout="15" path="/" requireSSL="true" slidingExpiration="true"/>
</forms>
```

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Formularze sieci Web, MVC5 |
| **Atrybuty**              | EnvironmentType-lokalnego |
| **Odwołania**              | [asdeqa](https://skf.azurewebsites.net/Mitigations/Details/wefr) |
| **Kroki** | Gdy aplikacja sieci Web jest jednostką uzależnioną, a usługi AD FS to usługa STS, okres istnienia FedAuth tokenów plików cookie uwierzytelniania — można ustawić za pomocą następującej konfiguracji w web.config:|

### <a name="example"></a>Przykład
```XML
  <system.identityModel.services>
    <federationConfiguration>
      <!-- Set requireSsl=true; domain=application domain name used by FedAuth cookies (Ex: .gdinfra.com); -->
      <cookieHandler requireSsl="true" persistentSessionLifetime="0.0:15:0" />
      <!-- Set requireHttps=true; -->
      <wsFederation passiveRedirectEnabled="true" issuer="http://localhost:39529/" realm="https://localhost:44302/" reply="https://localhost:44302/" requireHttps="true"/>
      <!--
      Use the code below to enable encryption-decryption of claims received from ADFS. Thumbprint value varies based on the certificate being used.
      <serviceCertificate>
        <certificateReference findValue="4FBBBA33A1D11A9022A5BF3492FF83320007686A" storeLocation="LocalMachine" storeName="My" x509FindType="FindByThumbprint" />
      </serviceCertificate>
      -->
    </federationConfiguration>
  </system.identityModel.services>
```

### <a name="example"></a>Przykład
Ponadto okres istnienia tokenu wystawionego przez usługi AD FS powinien mieć wartość 15 minut, wykonując następujące polecenie programu PowerShell na serwerze usług AD FS:
```csharp
Set-ADFSRelyingPartyTrust -TargetName "<RelyingPartyWebApp>" -ClaimsProviderName @("Active Directory") -TokenLifetime 15 -AlwaysRequireAuthentication $true
```

## <a name="implement-proper-logout-from-the-application"></a><a id="proper-app-logout"></a>Implementowanie prawidłowego wylogowywania z aplikacji

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Wykonaj odpowiednie Wylogowywanie z aplikacji, gdy użytkownik naciśnie przycisk Wyloguj. Po wylogowaniu aplikacja powinna zniszczyć sesję użytkownika, a także zresetować i zniesienia wartość pliku cookie sesji oraz resetować i nullifying wartość cookie uwierzytelniania. Ponadto, jeśli wiele sesji jest powiązanych z tożsamością pojedynczego użytkownika, muszą one być rozłączone zbiorczo po stronie serwera po upływie limitu czasu lub wylogowaniu. Na koniec upewnij się, że na każdej stronie jest dostępna funkcja wylogowywania. |

## <a name="mitigate-against-cross-site-request-forgery-csrf-attacks-on-aspnet-web-apis"></a><a id="csrf-api"></a>Rozwiązywanie problemów związanych z fałszerstwem żądań między witrynami (CSRF) na ASP.NETch interfejsów API sieci Web

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Fałszerstwo żądania między lokacjami (CSRF lub XSRF) jest typem ataku, w którym osoba atakująca może wykonywać działania w kontekście zabezpieczeń sesji określonej przez inną użytkownika w witrynie sieci Web. Celem jest zmodyfikowanie lub usunięcie zawartości, jeśli docelowa witryna sieci Web opiera się wyłącznie na plikach cookie sesji w celu uwierzytelnienia odebranego żądania. Osoba atakująca może wykorzystać tę lukę w zabezpieczeniach, korzystając z przeglądarki innego użytkownika w celu załadowania adresu URL za pomocą polecenia z zagrożonej witryny, na której użytkownik jest już zalogowany. Istnieje wiele sposobów na wykonanie tej czynności przez osobę atakującą, na przykład przez hostowanie innej witryny sieci Web, która ładuje zasób z zagrożonego serwera, lub uzyskanie użytkownika w celu kliknięcia linku. Atak może być niemożliwy, jeśli serwer wysyła dodatkowy token do klienta programu, program wymaga, aby klient dołączył ten token we wszystkich przyszłych żądaniach i sprawdza, czy wszystkie przyszłe żądania zawierają token odnoszący się do bieżącej sesji, na przykład przy użyciu ASP.NET AntiForgeryToken lub ViewState. |

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | MVC5, MVC6 |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Zapobieganie atakom CSRF (cross-site) w interfejsie API sieci Web ASP.NET](https://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks) |
| **Kroki** | Anti-CSRF i AJAX: token formy może być problemem w przypadku żądań AJAX, ponieważ żądanie AJAX może wysyłać dane JSON, a nie dane formularza HTML. Jednym z rozwiązań jest wysłanie tokenów w niestandardowym nagłówku HTTP. Poniższy kod używa składnia Razor do wygenerowania tokenów, a następnie dodaje tokeny do żądania AJAX. |

### <a name="example"></a>Przykład
```Javascript
<script>
    @functions{
        public string TokenHeaderValue()
        {
            string cookieToken, formToken;
            AntiForgery.GetTokens(null, out cookieToken, out formToken);
            return cookieToken + ":" + formToken;                
        }
    }
    $.ajax("api/values", {
        type: "post",
        contentType: "application/json",
        data: {  }, // JSON data goes here
        dataType: "json",
        headers: {
            'RequestVerificationToken': '@TokenHeaderValue()'
        }
    });
</script>
```

### <a name="example"></a>Przykład
Podczas przetwarzania żądania Wyodrębnij tokeny z nagłówka żądania. Następnie Wywołaj metodę unfałszerstwa. Validate, aby sprawdzić poprawność tokenów. Metoda Validate zgłasza wyjątek, jeśli tokeny są nieprawidłowe.
```csharp
void ValidateRequestHeader(HttpRequestMessage request)
{
    string cookieToken = "";
    string formToken = "";

    IEnumerable<string> tokenHeaders;
    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
    {
        string[] tokens = tokenHeaders.First().Split(':');
        if (tokens.Length == 2)
        {
            cookieToken = tokens[0].Trim();
            formToken = tokens[1].Trim();
        }
    }
    AntiForgery.Validate(cookieToken, formToken);
}
```

### <a name="example"></a>Przykład
Formularze CSRF i ASP.NET MVC — Użyj metody pomocnika AntiForgeryToken w widokach; Umieść plik HTML. AntiForgeryToken () w formularzu, na przykład
```csharp
@using (Html.BeginForm("UserProfile", "SubmitUpdate")) { 
    @Html.ValidationSummary(true) 
    @Html.AntiForgeryToken()
    <fieldset> 
}
```

### <a name="example"></a>Przykład
W powyższym przykładzie przedstawiono dane wyjściowe podobne do następujących:
```csharp
<form action="/UserProfile/SubmitUpdate" method="post">
    <input name="__RequestVerificationToken" type="hidden" value="saTFWpkKN0BYazFtN6c4YbZAmsEwG0srqlUqqloi/fVgeV2ciIFVmelvzwRZpArs" />
    <!-- rest of form goes here -->
</form>
```

### <a name="example"></a>Przykład
W tym samym czasie plik HTML. AntiForgeryToken () zapewnia odwiedzającemu plik cookie o nazwie __RequestVerificationToken, z taką samą wartością jak losowo Ukryta wartość pokazana powyżej. Następnie, aby sprawdzić poprawność post formularzy przychodzących, Dodaj filtr [ValidateAntiForgeryToken] do docelowej metody akcji. Na przykład:
```
[ValidateAntiForgeryToken]
public ViewResult SubmitUpdate()
{
// ... etc.
}
```
Filtr autoryzacji, który sprawdza, czy:
* Żądanie przychodzące ma plik cookie o nazwie __RequestVerificationToken
* Żądanie przychodzące zawiera `Request.Form` wpis o nazwie __RequestVerificationToken
* Te pliki cookie i `Request.Form` wartości są zgodne z założeniem, że wszystkie są prawidłowe. W przeciwnym razie błąd autoryzacji z komunikatem "wymagany token chroniący przed fałszerstwem nie został dostarczony lub był nieprawidłowy".

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | MVC5, MVC6 |
| **Atrybuty**              | Dostawca tożsamości — ADFS, dostawca tożsamości — Azure AD |
| **Odwołania**              | [Zabezpieczanie interfejsu API sieci Web przy użyciu pojedynczych kont i logowanie lokalne w ASP.NET Web API 2,2](https://www.asp.net/web-api/overview/security/individual-accounts-in-web-api) |
| **Kroki** | Jeśli internetowy interfejs API jest zabezpieczony przy użyciu protokołu OAuth 2,0, oczekuje tokenu okaziciela w nagłówku żądania autoryzacji i przyznaje dostęp do żądania tylko wtedy, gdy token jest prawidłowy. W przeciwieństwie do uwierzytelniania na podstawie plików cookie przeglądarki nie dołączają tokenów okaziciela do żądań. Klient żądający musi jawnie dołączyć token okaziciela do nagłówka żądania. W związku z tym w przypadku internetowych interfejsów API ASP.NET chronionych przy użyciu protokołu OAuth 2,0 tokeny okaziciela są traktowane jako ochrona przed atakami CSRF. Należy pamiętać, że jeśli część MVC aplikacji używa uwierzytelniania formularzy (tj. używa plików cookie), tokeny zabezpieczające muszą być używane przez aplikację sieci Web MVC. |

### <a name="example"></a>Przykład
Internetowy interfejs API musi być informowany w celu zagwarantowania tylko tokenów okaziciela, a nie plików cookie. Można to zrobić przez następującą konfigurację w `WebApiConfig.Register` metodzie:

```csharp
config.SuppressDefaultHostAuthentication();
config.Filters.Add(new HostAuthenticationFilter(OAuthDefaults.AuthenticationType));
```

Metoda SuppressDefaultHostAuthentication informuje internetowy interfejs API, aby ignorował wszelkie uwierzytelnienia, które są wykonywane przed przekroczeniem przez żądanie potoku interfejsu API sieci Web przez usługi IIS lub przez oprogramowanie pośredniczące OWIN. Dzięki temu można ograniczyć interfejs API sieci Web do uwierzytelniania tylko przy użyciu tokenów okaziciela.