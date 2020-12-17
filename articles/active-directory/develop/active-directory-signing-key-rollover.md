---
title: Przerzucanie klucza podpisywania na platformie tożsamości firmy Microsoft
description: W tym artykule omówiono najlepsze rozwiązania związane z przerzucaniem klucza podpisywania dla Azure Active Directory
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 8/11/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.openlocfilehash: c9fbf6990f789bdb0edb1cf45885003569d4f6a8
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/17/2020
ms.locfileid: "97653235"
---
# <a name="signing-key-rollover-in-microsoft-identity-platform"></a>Przerzucanie klucza podpisywania na platformie tożsamości firmy Microsoft
W tym artykule omówiono, co należy wiedzieć o kluczach publicznych używanych przez platformę tożsamości firmy Microsoft do podpisywania tokenów zabezpieczających. Należy pamiętać, że te klucze są okresowo przenoszone i w nagłych przypadkach mogą być natychmiast rzutowane. Wszystkie aplikacje korzystające z platformy tożsamości firmy Microsoft powinny być w stanie programowo obsłużyć proces przerzucania kluczy. Kontynuuj odczytywanie, aby zrozumieć, jak działają klucze, jak oceniać wpływ przerzucania do aplikacji oraz jak aktualizować aplikację lub ustanawiać okresowe ręczne Przerzucanie w razie potrzeby.

## <a name="overview-of-signing-keys-in-microsoft-identity-platform"></a>Przegląd kluczy podpisywania na platformie tożsamości firmy Microsoft
Platforma tożsamości firmy Microsoft korzysta z kryptografii klucza publicznego opartej na standardach branżowych, aby ustanowić relację zaufania między nią a aplikacjami, które go używają. W praktyce te działania działają w następujący sposób: platforma tożsamości firmy Microsoft używa klucza podpisywania, który składa się z pary kluczy publiczny i prywatny. Gdy użytkownik loguje się do aplikacji korzystającej z platformy tożsamości firmy Microsoft na potrzeby uwierzytelniania, firma Microsoft Identity platform tworzy token zabezpieczający zawierający informacje o użytkowniku. Ten token jest podpisany przez platformę tożsamości firmy Microsoft przy użyciu swojego klucza prywatnego przed wysłaniem ich z powrotem do aplikacji. Aby sprawdzić, czy token jest prawidłowy i pochodzi z platformy tożsamości firmy Microsoft, aplikacja musi sprawdzić poprawność podpisu tokenu przy użyciu kluczy publicznych udostępnianych przez platformę tożsamości firmy Microsoft, która jest zawarta w [dokumencie OpenID Connect Connect](https://openid.net/specs/openid-connect-discovery-1_0.html) lub [metadanych Federacji](../azuread-dev/azure-ad-federation-metadata.md)protokołu SAML/WS-karmionego.

Ze względów bezpieczeństwa klucz podpisywania platformy tożsamości firmy Microsoft jest okresowo przenoszone i w przypadku awarii może zostać natychmiast przeniesiona. Między tymi kluczami nie jest ustawiony ani gwarantowany czas — wszystkie aplikacje, które integrują się z platformą tożsamości firmy Microsoft, powinny być przygotowane do obsługi zdarzenia przerzucania klucza niezależnie od tego, jak często mogą wystąpić. Jeśli tak nie jest, a aplikacja próbuje użyć wygasłego klucza do zweryfikowania podpisu w tokenie, żądanie logowania zakończy się niepowodzeniem.  Sprawdzanie, co 24 godziny dla aktualizacji, to najlepsze rozwiązanie z ograniczeniami (co pięć minut maksymalnie) natychmiastowe odświeżenie dokumentu klucza w przypadku napotkania tokenu z nieznanym identyfikatorem klucza. 

W dokumencie OpenID Connect Connect Discovery i dokumencie metadanych Federacji zawsze jest dostępny więcej niż jeden prawidłowy klucz. Twoja aplikacja powinna być przygotowana do korzystania z dowolnej i wszystkich kluczy określonych w dokumencie, ponieważ jeden klucz może zostać przetworzony wkrótce, drugi może być jego zastąpieniem i tak dalej.  Liczba kluczy, które mogą ulec zmianie w zależności od wewnętrznej architektury platformy tożsamości firmy Microsoft w miarę obsługi nowych platform, nowych chmur lub nowych protokołów uwierzytelniania. Kolejność kluczy w odpowiedzi JSON ani kolejność, w jakiej zostały uwidocznione, powinna być traktowana jako meaninful do aplikacji. 

Aplikacje obsługujące tylko jeden klucz podpisywania lub te, które wymagają ręcznej aktualizacji kluczy podpisywania, są z natury mniej bezpieczne i niezawodne.  Należy je zaktualizować do korzystania z [bibliotek standardowych](reference-v2-libraries.md) , aby upewnić się, że zawsze korzystają z aktualnych kluczy podpisywania, między innymi najlepszymi rozwiązaniami. 

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Jak ocenić, czy będzie to miało wpływ na aplikację i co z nią zrobić
Sposób, w jaki aplikacja obsługuje Przerzucanie kluczy, zależy od zmiennych, takich jak typ aplikacji lub używany protokół tożsamości i Biblioteka. Poniższe sekcje oceniają, czy Najczęstsze typy aplikacji mają wpływ na Przerzucanie kluczy i zapewniają wskazówki dotyczące aktualizowania aplikacji w celu obsługi automatycznego przerzucania lub ręcznego aktualizowania klucza.

* [Natywne aplikacje klienckie uzyskujące dostęp do zasobów](#nativeclient)
* [Aplikacje sieci Web/interfejsy API uzyskujący dostęp do zasobów](#webclient)
* [Aplikacje sieci Web/interfejsy API chroniące zasoby i skompilowane przy użyciu usługi Azure App Services](#appservices)
* [Aplikacje sieci Web/interfejsy API chroniące zasoby przy użyciu oprogramowania .NET OWIN OpenID Connect Connect, WS-Fed lub WindowsAzureActiveDirectoryBearerAuthentication](#owin)
* [Aplikacje sieci Web/interfejsy API chroniące zasoby przy użyciu oprogramowania .NET Core OpenID Connect Connect lub JwtBearerAuthentication](#owincore)
* [Aplikacje sieci Web/interfejsy API chroniące zasoby przy użyciu modułu Node.js Passport-Azure-AD](#passport)
* [Aplikacje sieci Web/interfejsy API chroniące zasoby i tworzone za pomocą programu Visual Studio 2015 lub nowszego](#vs2015)
* [Aplikacje sieci Web chroniące zasoby i tworzone za pomocą Visual Studio 2013](#vs2013)
* Interfejsy API sieci Web chroniące zasoby i tworzone za pomocą Visual Studio 2013
* [Aplikacje sieci Web chroniące zasoby i utworzone za pomocą programu Visual Studio 2012](#vs2012)
* [Aplikacje sieci Web chroniące zasoby i utworzone za pomocą programu Visual Studio 2010, 2008 o korzystaniu z programu Windows Identity Foundation](#vs2010)
* [Aplikacje sieci Web/interfejsy API chroniące zasoby przy użyciu innych bibliotek lub ręcznie implementujących dowolne z obsługiwanych protokołów](#other)

Wskazówki te **nie** dotyczą:

* Aplikacje dodane z galerii aplikacji usługi Azure AD (w tym niestandardowe) mają oddzielne wskazówki dotyczące kluczy podpisywania. [Więcej informacji.](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
* Aplikacje lokalne opublikowane za pośrednictwem serwera proxy aplikacji nie muszą martwić się o klucze podpisywania.

### <a name="native-client-applications-accessing-resources"></a><a name="nativeclient"></a>Natywne aplikacje klienckie uzyskujące dostęp do zasobów
Aplikacje, które uzyskują dostęp tylko do zasobów (tj. Microsoft Graph, Magazyn kluczy, interfejs API programu Outlook i inne interfejsy API firmy Microsoft) Generalnie uzyskują jedynie token i przekazują je do właściciela zasobu. Ponieważ nie chronią żadnych zasobów, nie sprawdzają one tokenu i w związku z tym nie trzeba upewnić się, że jest on prawidłowo podpisany.

Natywne aplikacje klienckie, zarówno stacjonarne, jak i mobilne, należą do tej kategorii i nie mają wpływu na Przerzucanie.

### <a name="web-applications--apis-accessing-resources"></a><a name="webclient"></a>Aplikacje sieci Web/interfejsy API uzyskujący dostęp do zasobów
Aplikacje, które uzyskują dostęp tylko do zasobów (tj. Microsoft Graph, Magazyn kluczy, interfejs API programu Outlook i inne interfejsy API firmy Microsoft) Generalnie uzyskują jedynie token i przekazują je do właściciela zasobu. Ponieważ nie chronią żadnych zasobów, nie sprawdzają one tokenu i w związku z tym nie trzeba upewnić się, że jest on prawidłowo podpisany.

Aplikacje sieci Web i interfejsy API sieci Web, które używają przepływu tylko aplikacji (poświadczenia klienta/certyfikat klienta) do żądania tokenów do tej kategorii i nie mają wpływu na Przerzucanie.

### <a name="web-applications--apis-protecting-resources-and-built-using-azure-app-services"></a><a name="appservices"></a>Aplikacje sieci Web/interfejsy API chroniące zasoby i skompilowane przy użyciu usługi Azure App Services
Funkcja uwierzytelniania/autoryzacji App Services platformy Azure ma już niezbędną logikę do automatycznego obsługi przerzucania kluczy.

### <a name="web-applications--apis-protecting-resources-using-net-owin-openid-connect-ws-fed-or-windowsazureactivedirectorybearerauthentication-middleware"></a><a name="owin"></a>Aplikacje sieci Web/interfejsy API chroniące zasoby przy użyciu oprogramowania .NET OWIN OpenID Connect Connect, WS-Fed lub WindowsAzureActiveDirectoryBearerAuthentication
Jeśli aplikacja korzysta z oprogramowania .NET OWIN OpenID Connect Connect, WS-Fed lub WindowsAzureActiveDirectoryBearerAuthentication, ma już niezbędną logikę do automatycznego obsługi przerzucania kluczy.

Możesz potwierdzić, że aplikacja korzysta z dowolnego z poniższych fragmentów kodu w Startup.cs lub Startup.Auth.cs aplikacji

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseWsFederationAuthentication(
    new WsFederationAuthenticationOptions
    {
     // ...
     });
```
```
app.UseWindowsAzureActiveDirectoryBearerAuthentication(
     new WindowsAzureActiveDirectoryBearerAuthenticationOptions
     {
     // ...
     });
```

### <a name="web-applications--apis-protecting-resources-using-net-core-openid-connect-or--jwtbearerauthentication-middleware"></a><a name="owincore"></a>Aplikacje sieci Web/interfejsy API chroniące zasoby przy użyciu oprogramowania .NET Core OpenID Connect Connect lub JwtBearerAuthentication
Jeśli aplikacja korzysta z programu .NET Core OWIN OpenID Connect Connect lub JwtBearerAuthentication, ma już niezbędną logikę do automatycznego obsługi przerzucania kluczy.

Możesz potwierdzić, że aplikacja korzysta z dowolnego z poniższych fragmentów kodu w Startup.cs lub Startup.Auth.cs aplikacji

```
app.UseOpenIdConnectAuthentication(
     new OpenIdConnectAuthenticationOptions
     {
         // ...
     });
```
```
app.UseJwtBearerAuthentication(
    new JwtBearerAuthenticationOptions
    {
     // ...
     });
```

### <a name="web-applications--apis-protecting-resources-using-nodejs-passport-azure-ad-module"></a><a name="passport"></a>Aplikacje sieci Web/interfejsy API chroniące zasoby przy użyciu modułu Node.js Passport-Azure-AD
Jeśli aplikacja korzysta z modułu Node.js Passport-AD, ma już niezbędną logikę do automatycznego obsługi przerzucania kluczy.

Możesz potwierdzić, że Twoja aplikacja jest zadana przez wyszukiwanie następującego fragmentu kodu w app.js aplikacji

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="web-applications--apis-protecting-resources-and-created-with-visual-studio-2015-or-later"></a><a name="vs2015"></a>Aplikacje sieci Web/interfejsy API chroniące zasoby i tworzone za pomocą programu Visual Studio 2015 lub nowszego
Jeśli aplikacja została skompilowana przy użyciu szablonu aplikacji sieci Web w programie Visual Studio 2015 lub nowszym, a wybrano **konta służbowe** z menu **Zmień uwierzytelnianie** , ma już ona niezbędną logikę umożliwiającą automatyczne Przerzucanie kluczy. Ta logika osadzona w OWIN OpenID Connect łączy oprogramowanie pośredniczące, pobiera i buforuje klucze z dokumentu OpenID Connect Connect Discovery i okresowo odświeża je.

W przypadku ręcznego dodania uwierzytelniania do rozwiązania aplikacja może nie mieć niezbędnej logiki przerzucania kluczy. Należy napisać go samodzielnie lub wykonać kroki opisane w temacie [aplikacje sieci Web/interfejsy API przy użyciu innych bibliotek lub ręcznie wdrażać dowolne z obsługiwanych protokołów](#other).

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>Aplikacje sieci Web chroniące zasoby i tworzone za pomocą Visual Studio 2013
Jeśli aplikacja została skompilowana przy użyciu szablonu aplikacji sieci Web w Visual Studio 2013 i wybrano opcję **konta organizacji** z menu **Zmień uwierzytelnianie** , istnieje już niezbędna logika do automatycznego obsłużenia przerzucania kluczy. Ta logika przechowuje unikatowy identyfikator organizacji i informacje o kluczu podpisywania w dwóch tabelach bazy danych skojarzonych z projektem. Parametry połączenia dla bazy danych można znaleźć w pliku Web.config projektu.

W przypadku ręcznego dodania uwierzytelniania do rozwiązania aplikacja może nie mieć niezbędnej logiki przerzucania kluczy. Należy napisać go samodzielnie lub wykonać kroki opisane w temacie [aplikacje sieci Web/interfejsy API przy użyciu innych bibliotek lub ręcznie wdrażać dowolne z obsługiwanych protokołów.](#other)

Poniższe kroki pomogą sprawdzić, czy logika działa prawidłowo w aplikacji.

1. W Visual Studio 2013 Otwórz rozwiązanie, a następnie kliknij kartę **Eksplorator serwera** w oknie po prawej stronie.
2. Rozwiń węzeł **połączenia danych**, **DefaultConnection**, a następnie **tabele**. Znajdź tabelę **IssuingAuthorityKeys** , kliknij ją prawym przyciskiem myszy, a następnie kliknij polecenie **Pokaż dane tabeli**.
3. W tabeli **IssuingAuthorityKeys** będzie zawierać co najmniej jeden wiersz, który odnosi się do wartości odcisku palca klucza. Usuń wszystkie wiersze z tabeli.
4. Kliknij prawym przyciskiem myszy tabelę **dzierżawców** , a następnie kliknij polecenie **Pokaż dane tabeli**.
5. W tabeli **dzierżawców** będzie istnieć co najmniej jeden wiersz, który odnosi się do unikatowego identyfikatora dzierżawy katalogu. Usuń wszystkie wiersze z tabeli. Jeśli nie usuniesz wierszy w tabeli **dzierżawców** i tabeli **IssuingAuthorityKeys** , wystąpi błąd w czasie wykonywania.
6. Skompiluj i uruchom aplikację. Po zalogowaniu się do konta możesz zatrzymać aplikację.
7. Wróć do **Eksplorator serwera** i sprawdź wartości w tabeli **IssuingAuthorityKeys** i **dzierżawców** . Należy zauważyć, że zostały one automatycznie ponownie wypełniane odpowiednimi informacjami z dokumentu metadanych Federacji.

### <a name="web-apis-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>Interfejsy API sieci Web chroniące zasoby i tworzone za pomocą Visual Studio 2013
Jeśli aplikacja internetowego interfejsu API została utworzona w Visual Studio 2013 przy użyciu szablonu internetowego interfejsu API, a następnie wybrane **konta organizacji** z menu **Zmień uwierzytelnianie** , masz już niezbędną logikę w aplikacji.

W przypadku ręcznego skonfigurowania uwierzytelniania postępuj zgodnie z poniższymi instrukcjami, aby dowiedzieć się, jak skonfigurować internetowy interfejs API do automatycznego aktualizowania informacji o kluczu.

Poniższy fragment kodu pokazuje, jak pobrać najnowsze klucze z dokumentu metadanych Federacji, a następnie użyć [programu obsługi tokenów JWT](/previous-versions/dotnet/framework/security/json-web-token-handler) do walidacji tokenu. W fragmencie kodu założono, że będzie używany własny mechanizm buforowania w celu utrwalenia klucza w celu zweryfikowania przyszłych tokenów z platformy tożsamości firmy Microsoft, niezależnie od tego, czy znajduje się on w bazie danych, pliku konfiguracji czy w innym miejscu.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IdentityModel.Tokens;
using System.Configuration;
using System.Security.Cryptography.X509Certificates;
using System.Xml;
using System.IdentityModel.Metadata;
using System.ServiceModel.Security;
using System.Threading;

namespace JWTValidation
{
    public class JWTValidator
    {
        private string MetadataAddress = "[Your Federation Metadata document address goes here]";

        // Validates the JWT Token that's part of the Authorization header in an HTTP request.
        public void ValidateJwtToken(string token)
        {
            JwtSecurityTokenHandler tokenHandler = new JwtSecurityTokenHandler()
            {
                // Do not disable for production code
                CertificateValidator = X509CertificateValidator.None
            };

            TokenValidationParameters validationParams = new TokenValidationParameters()
            {
                AllowedAudience = "[Your App ID URI goes here, as registered in the Azure Portal]",
                ValidIssuer = "[The issuer for the token goes here, such as https://sts.windows.net/68b98905-130e-4d7c-b6e1-a158a9ed8449/]",
                SigningTokens = GetSigningCertificates(MetadataAddress)

                // Cache the signing tokens by your desired mechanism
            };

            Thread.CurrentPrincipal = tokenHandler.ValidateToken(token, validationParams);
        }

        // Returns a list of certificates from the specified metadata document.
        public List<X509SecurityToken> GetSigningCertificates(string metadataAddress)
        {
            List<X509SecurityToken> tokens = new List<X509SecurityToken>();

            if (metadataAddress == null)
            {
                throw new ArgumentNullException(metadataAddress);
            }

            using (XmlReader metadataReader = XmlReader.Create(metadataAddress))
            {
                MetadataSerializer serializer = new MetadataSerializer()
                {
                    // Do not disable for production code
                    CertificateValidationMode = X509CertificateValidationMode.None
                };

                EntityDescriptor metadata = serializer.ReadMetadata(metadataReader) as EntityDescriptor;

                if (metadata != null)
                {
                    SecurityTokenServiceDescriptor stsd = metadata.RoleDescriptors.OfType<SecurityTokenServiceDescriptor>().First();

                    if (stsd != null)
                    {
                        IEnumerable<X509RawDataKeyIdentifierClause> x509DataClauses = stsd.Keys.Where(key => key.KeyInfo != null && (key.Use == KeyType.Signing || key.Use == KeyType.Unspecified)).
                                                             Select(key => key.KeyInfo.OfType<X509RawDataKeyIdentifierClause>().First());

                        tokens.AddRange(x509DataClauses.Select(token => new X509SecurityToken(new X509Certificate2(token.GetX509RawData()))));
                    }
                    else
                    {
                        throw new InvalidOperationException("There is no RoleDescriptor of type SecurityTokenServiceType in the metadata");
                    }
                }
                else
                {
                    throw new Exception("Invalid Federation Metadata document");
                }
            }
            return tokens;
        }
    }
}
```

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2012"></a><a name="vs2012"></a>Aplikacje sieci Web chroniące zasoby i utworzone za pomocą programu Visual Studio 2012
Jeśli aplikacja została skompilowana w programie Visual Studio 2012, prawdopodobnie używasz narzędzia do tworzenia tożsamości i dostępu do konfigurowania aplikacji. Jest również prawdopodobnie używany do [sprawdzania poprawności rejestru Nazwa wystawcy (VINR)](/previous-versions/dotnet/framework/security/validating-issuer-name-registry). VINR jest odpowiedzialny za utrzymywanie informacji o zaufanych dostawcach tożsamości (Microsoft Identity platform) oraz kluczy używanych do weryfikacji tokenów wystawionych przez nich. VINR ułatwia również automatyczne aktualizowanie informacji o kluczach przechowywanych w pliku Web.config przez pobranie najnowszego dokumentu metadanych Federacji skojarzonego z Twoim katalogiem, sprawdzenie, czy konfiguracja jest nieaktualna przy użyciu najnowszego dokumentu, i zaktualizowanie aplikacji tak, aby korzystała z nowego klucza w razie potrzeby.

Jeśli aplikacja została utworzona przy użyciu którejkolwiek z przykładów kodu lub dokumentacji instruktażowej dostarczonej przez firmę Microsoft, kluczowa logika przerzucania jest już dołączona do projektu. Zobaczysz, że Poniższy kod już istnieje w projekcie. Jeśli aplikacja nie ma jeszcze tej logiki, wykonaj poniższe czynności, aby je dodać, i sprawdź, czy działa poprawnie.

1. W **Eksplorator rozwiązań** Dodaj odwołanie do zestawu **System. IdentityModel** dla odpowiedniego projektu.
2. Otwórz plik **Global.asax.cs** i Dodaj następujące dyrektywy using:
   ```
   using System.Configuration;
   using System.IdentityModel.Tokens;
   ```
3. Dodaj następującą metodę do pliku **Global.asax.cs** :
   ```
   protected void RefreshValidationSettings()
   {
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
   }
   ```
4. Wywołaj metodę **RefreshValidationSettings ()** w metodzie **Application_Start ()** w **Global.asax.cs** , jak pokazano poniżej:
   ```
   protected void Application_Start()
   {
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
   }
   ```

Po wykonaniu tych kroków Web.config aplikacji zostanie zaktualizowana o najnowsze informacje z dokumentu metadanych Federacji, łącznie z najnowszymi kluczami. Ta aktualizacja będzie odbywać się za każdym razem, gdy pula aplikacji jest odtwarzana w usługach IIS. Domyślnie usługi IIS są skonfigurowane do odtwarzania aplikacji co 29 godzin.

Wykonaj poniższe kroki, aby sprawdzić, czy logika przerzucania kluczy działa.

1. Po zweryfikowaniu, że aplikacja korzysta z powyższego kodu, Otwórz plik **Web.config** i przejdź do **\<issuerNameRegistry>** bloku, w którym szukasz następujących kilku wierszy:
   ```
   <issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
   ```
2. W **\<add thumbprint="">** ustawieniu Zmień wartość odcisku palca, zastępując dowolny znak innym. Zapisz plik **Web.config** .
3. Skompiluj aplikację, a następnie uruchom ją. Jeśli możesz zakończyć proces logowania, aplikacja pomyślnie zaktualizuje klucz, pobierając wymagane informacje z dokumentu metadanych Federacji katalogu. Jeśli masz problemy z logowaniem się, upewnij się, że zmiany w aplikacji są poprawne, odczytując [dodawanie Sign-On do aplikacji sieci Web przy użyciu artykułu platformy tożsamości firmy Microsoft](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) lub pobierając i sprawdzając następujący przykład kodu: [wielodostępna aplikacja w chmurze dla Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b).

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2008-or-2010-and-windows-identity-foundation-wif-v10-for-net-35"></a><a name="vs2010"></a>Aplikacje sieci Web chroniące zasoby i utworzone za pomocą programu Visual Studio 2008 lub 2010 oraz Windows Identity Foundation (WIF) v 1.0 dla programu .NET 3,5
Jeśli aplikacja została utworzona w systemie WIF v 1.0, nie ma żadnego mechanizmu, aby automatycznie odświeżyć konfigurację aplikacji w celu użycia nowego klucza.

* *Najprostszy sposób* Użyj narzędzi FedUtil zawartych w zestawie SDK WIF, które mogą pobrać najnowszy dokument metadanych i zaktualizować konfigurację.
* Zaktualizuj aplikację do wersji .NET 4,5, która zawiera najnowszą wersję WIF znajdującą się w przestrzeni nazw System. Następnie można użyć [weryfikacji rejestru Nazwa wystawcy (VINR)](/previous-versions/dotnet/framework/security/validating-issuer-name-registry) w celu przeprowadzenia automatycznych aktualizacji konfiguracji aplikacji.
* Wykonaj ręczne Przerzucanie zgodnie z instrukcjami znajdującymi się na końcu niniejszego dokumentu wskazówek.

Instrukcje dotyczące aktualizowania konfiguracji przy użyciu FedUtil:

1. Sprawdź, czy na komputerze deweloperskim jest zainstalowany zestaw SDK WIF v 1.0 dla programu Visual Studio 2008 lub 2010. Możesz [pobrać go z tego miejsca](https://www.microsoft.com/en-us/download/details.aspx?id=4451) , jeśli jeszcze go nie zainstalowano.
2. W programie Visual Studio Otwórz rozwiązanie, a następnie kliknij prawym przyciskiem myszy odpowiedni projekt i wybierz polecenie **Aktualizuj metadane federacji**. Jeśli ta opcja jest niedostępna, FedUtil i/lub zestaw SDK WIF v 1.0 nie został zainstalowany.
3. W wierszu polecenia wybierz pozycję **Aktualizuj** , aby rozpocząć aktualizowanie metadanych Federacji. Jeśli masz dostęp do środowiska serwera, w którym jest hostowana aplikacja, możesz opcjonalnie użyć [automatycznego harmonogramu aktualizacji metadanych](/previous-versions/windows-identity-foundation/ee517272(v=msdn.10))FedUtil.
4. Kliknij przycisk **Zakończ** , aby ukończyć proces aktualizacji.

### <a name="web-applications--apis-protecting-resources-using-any-other-libraries-or-manually-implementing-any-of-the-supported-protocols"></a><a name="other"></a>Aplikacje sieci Web/interfejsy API chroniące zasoby przy użyciu innych bibliotek lub ręcznie implementujących dowolne z obsługiwanych protokołów
W przypadku korzystania z innej biblioteki lub ręcznie zaimplementowania któregokolwiek z obsługiwanych protokołów należy przejrzeć bibliotekę lub implementację, aby upewnić się, że klucz jest pobierany z pliku OpenID Connect Connect Discovery lub dokumentu metadanych Federacji. Jednym ze sposobów, aby sprawdzić, czy jest to wyszukiwanie w kodzie lub kod biblioteki dla wszelkich wywołań do dokumentu odnajdywania OpenID Connect lub dokumentu metadanych Federacji.

Jeśli klucz jest przechowywany gdzieś lub stałe w aplikacji, można ręcznie pobrać klucz i zaktualizować go odpowiednio przez ręczne Przerzucanie zgodnie z instrukcjami znajdującymi się na końcu niniejszego dokumentu wskazówek. **Zdecydowanie zachęcamy do ulepszania aplikacji, aby obsługiwała automatyczne Przerzucanie** przy użyciu dowolnych z koncepcji przedstawionych w tym artykule, aby uniknąć przyszłego zakłócenia i nakładu pracy, jeśli platforma tożsamości firmy Microsoft zwiększy swój Przerzucanie erze lub ma awaryjne Przerzucanie poza pasmem.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Testowanie aplikacji w celu ustalenia, czy będzie to miało wpływ
Możesz sprawdzić, czy aplikacja obsługuje automatyczne Przerzucanie kluczy przez pobranie skryptów i postępując zgodnie z instrukcjami w [tym repozytorium GitHub.](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-your-application-does-not-support-automatic-rollover"></a>Jak przeprowadzić ręczne Przerzucanie, jeśli aplikacja nie obsługuje automatycznego przerzucania
Jeśli aplikacja **nie obsługuje automatycznego** przerzucania, należy ustalić proces, który okresowo monitoruje klucze podpisywania platformy tożsamości firmy Microsoft i wykonuje odpowiednie Przerzucanie ręczne. [To repozytorium GitHub](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) zawiera skrypty i instrukcje dotyczące sposobu wykonania tej czynności.