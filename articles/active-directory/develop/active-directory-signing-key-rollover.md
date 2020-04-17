---
title: Podpisywanie przerzucenia kluczy w usłudze Azure AD
description: W tym artykule omówiono najważniejsze wskazówki dotyczące rolowania kluczy podpisywania usługi Azure Active Directory
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/20/2018
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin
ms.custom: aaddev
ms.openlocfilehash: e0a38eb03df3d1da64172842fb6eca3cd762f9cd
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537240"
---
# <a name="signing-key-rollover-in-azure-active-directory"></a>Podpisywanie narzutu kluczy w usłudze Azure Active Directory
W tym artykule omówiono, co należy wiedzieć o kluczach publicznych, które są używane w usłudze Azure Active Directory (Azure AD) do podpisywania tokenów zabezpieczających. Ważne jest, aby pamiętać, że klucze te przewracają się okresowo i w nagłych wypadkach mogą być natychmiast przerzucone. Wszystkie aplikacje korzystające z usługi Azure AD powinny mieć możliwość programowego obsługi procesu przerzucenia klucza lub ustanowienia okresowego procesu ręcznego przerzucenia. Kontynuuj czytanie, aby zrozumieć, jak działają klucze, jak ocenić wpływ przerzucenia na aplikację i jak zaktualizować aplikację lub ustanowić okresowy proces ręcznego przerzucenia do obsługi przerzucenia klucza, jeśli to konieczne.

## <a name="overview-of-signing-keys-in-azure-ad"></a>Omówienie kluczy podpisywania w usłudze Azure AD
Usługa Azure AD używa kryptografii klucza publicznego opartego na standardach branżowych w celu ustanowienia zaufania między nią a aplikacjami, które jej używają. W praktyce działa to w następujący sposób: usługa Azure AD używa klucza podpisywania, który składa się z pary kluczy publicznych i prywatnych. Gdy użytkownik loguje się do aplikacji, która używa usługi Azure AD do uwierzytelniania, usługa Azure AD tworzy token zabezpieczający, który zawiera informacje o użytkowniku. Ten token jest podpisany przez usługę Azure AD przy użyciu klucza prywatnego, zanim zostanie wysłany z powrotem do aplikacji. Aby sprawdzić, czy token jest prawidłowy i pochodzi z usługi Azure AD, aplikacja musi sprawdzić poprawność podpisu tokenu przy użyciu klucza publicznego udostępnianego przez usługę Azure AD, który znajduje się w [dokumencie odnajdywania OpenID Connect](https://openid.net/specs/openid-connect-discovery-1_0.html) dzierżawcy lub [dokumencie metadanych federacji](../azuread-dev/azure-ad-federation-metadata.md)SAML/WS-Fed.

Ze względów bezpieczeństwa klucz podpisywania usługi Azure AD jest okresowo przetaczany, a w przypadku sytuacji awaryjnej może zostać natychmiast przerzucony. Każda aplikacja, która integruje się z usługą Azure AD powinny być przygotowane do obsługi zdarzenia przerzucenia klucza, niezależnie od tego, jak często może wystąpić. Jeśli tak nie jest, a aplikacja próbuje użyć wygasłego klucza, aby zweryfikować podpis na tokenie, żądanie logowania zakończy się niepowodzeniem.

W dokumencie odnajdowania OpenID Connect i dokumencie metadanych federacji zawsze dostępny jest więcej niż jeden prawidłowy klucz. Aplikacja powinna być przygotowana do użycia dowolnego klucza określonego w dokumencie, ponieważ jeden klucz może zostać wkrótce przerzucony, inny może być jego zamiennikiem i tak dalej.

## <a name="how-to-assess-if-your-application-will-be-affected-and-what-to-do-about-it"></a>Jak ocenić, czy wniosek zostanie naruszony i co z nim zrobić
Sposób obsługi przerzucenia kluczy aplikacji zależy od zmiennych, takich jak typ aplikacji lub jaki protokół tożsamości i biblioteka została użyta. W poniższych sekcjach ocenia się, czy najpopularniejsze typy aplikacji mają wpływ na przerzucie klucza i zawierają wskazówki dotyczące aktualizowania aplikacji w celu obsługi automatycznego przerzucenia lub ręcznej aktualizacji klucza.

* [Natywne aplikacje klienckie uzyskujące dostęp do zasobów](#nativeclient)
* [Aplikacje sieci Web / interfejsy API uzyskujące dostęp do zasobów](#webclient)
* [Aplikacje sieci Web / interfejsy API chroniące zasoby i zbudowane przy użyciu usług Azure App Services](#appservices)
* [Aplikacje sieci Web / interfejsy API chroniące zasoby za pomocą oprogramowania pośredniczącego .NET OWIN OpenID Connect, WS-Fed lub WindowsAzureActiveDirectoryBearerAuthentication](#owin)
* [Aplikacje sieci Web / interfejsy API chroniące zasoby za pomocą programu .NET Core OpenID Connect lub oprogramowania pośredniczącego JwtBearerAuthentication](#owincore)
* [Aplikacje internetowe / interfejsy API chroniące zasoby za pomocą modułu Node.js passport-azure-ad](#passport)
* [Aplikacje sieci Web / interfejsy API chroniące zasoby i utworzone za pomocą programu Visual Studio 2015 lub nowszego](#vs2015)
* [Aplikacje sieci Web chroniące zasoby i utworzone za pomocą programu Visual Studio 2013](#vs2013)
* Internetowe interfejsy API chroniące zasoby i utworzone za pomocą programu Visual Studio 2013
* [Aplikacje sieci Web chroniące zasoby i utworzone za pomocą programu Visual Studio 2012](#vs2012)
* [Aplikacje sieci Web chroniące zasoby i utworzone za pomocą programu Visual Studio 2010, 2008 o przy użyciu programu Windows Identity Foundation](#vs2010)
* [Aplikacje sieci Web / interfejsy API chroniące zasoby przy użyciu innych bibliotek lub ręcznie implementujące którykolwiek z obsługiwanych protokołów](#other)

Niniejsze wytyczne **nie** mają zastosowania do:

* Aplikacje dodane z galerii aplikacji usługi Azure AD (w tym niestandardowe) mają oddzielne wskazówki dotyczące podpisywania kluczy. [Więcej informacji.](../manage-apps/manage-certificates-for-federated-single-sign-on.md)
* Aplikacje lokalne publikowane za pośrednictwem serwera proxy aplikacji nie muszą się martwić o klucze podpisywania.

### <a name="native-client-applications-accessing-resources"></a><a name="nativeclient"></a>Natywne aplikacje klienckie uzyskujące dostęp do zasobów
Aplikacje, które uzyskują dostęp tylko do zasobów (tj. Microsoft Graph, KeyVault, Outlook API i inne interfejsy API firmy Microsoft) zazwyczaj tylko uzyskać token i przekazać go wraz z właścicielem zasobu. Biorąc pod uwagę, że nie chronią żadnych zasobów, nie sprawdzają tokenu i dlatego nie muszą zapewniać, że jest poprawnie podpisany.

Natywne aplikacje klienckie, zarówno stacjonarne, jak i mobilne, należą do tej kategorii i w związku z tym nie mają wpływu na przerzucanie.

### <a name="web-applications--apis-accessing-resources"></a><a name="webclient"></a>Aplikacje sieci Web / interfejsy API uzyskujące dostęp do zasobów
Aplikacje, które uzyskują dostęp tylko do zasobów (tj. Microsoft Graph, KeyVault, Outlook API i inne interfejsy API firmy Microsoft) zazwyczaj tylko uzyskać token i przekazać go wraz z właścicielem zasobu. Biorąc pod uwagę, że nie chronią żadnych zasobów, nie sprawdzają tokenu i dlatego nie muszą zapewniać, że jest poprawnie podpisany.

Aplikacje sieci Web i interfejsy API sieci Web, które używają przepływu tylko do aplikacji (poświadczenia klienta / certyfikat klienta), należą do tej kategorii i w związku z tym nie mają wpływu na przerzucanie.

### <a name="web-applications--apis-protecting-resources-and-built-using-azure-app-services"></a><a name="appservices"></a>Aplikacje sieci Web / interfejsy API chroniące zasoby i zbudowane przy użyciu usług Azure App Services
Funkcja uwierzytelniania / autoryzacji usług Azure App Services (EasyAuth) ma już logikę niezbędną do automatycznego obchodzenia się z kluczami.

### <a name="web-applications--apis-protecting-resources-using-net-owin-openid-connect-ws-fed-or-windowsazureactivedirectorybearerauthentication-middleware"></a><a name="owin"></a>Aplikacje sieci Web / interfejsy API chroniące zasoby za pomocą oprogramowania pośredniczącego .NET OWIN OpenID Connect, WS-Fed lub WindowsAzureActiveDirectoryBearerAuthentication
Jeśli aplikacja korzysta z oprogramowania pośredniczącego .NET OWIN OpenID Connect, WS-Fed lub WindowsAzureActiveDirectoryBearerAuthentication, ma już logikę niezbędną do automatycznego obchodzenia kluczy.

Można potwierdzić, że aplikacja używa dowolnego z nich, wyszukując którykolwiek z następujących fragmentów w Startup.cs lub Startup.Auth.cs aplikacji

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

### <a name="web-applications--apis-protecting-resources-using-net-core-openid-connect-or--jwtbearerauthentication-middleware"></a><a name="owincore"></a>Aplikacje sieci Web / interfejsy API chroniące zasoby za pomocą programu .NET Core OpenID Connect lub oprogramowania pośredniczącego JwtBearerAuthentication
Jeśli aplikacja używa oprogramowania pośredniczącego .NET Core OWIN OpenID Connect lub JwtBearerAuthentication, ma już logikę niezbędną do automatycznego obchodzenia się z kluczami.

Można potwierdzić, że aplikacja używa dowolnego z nich, wyszukując którykolwiek z następujących fragmentów w Startup.cs lub Startup.Auth.cs aplikacji

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

### <a name="web-applications--apis-protecting-resources-using-nodejs-passport-azure-ad-module"></a><a name="passport"></a>Aplikacje internetowe / interfejsy API chroniące zasoby za pomocą modułu Node.js passport-azure-ad
Jeśli aplikacja korzysta z modułu Node.js passport-ad, ma już logikę niezbędną do automatycznego obchodzenia się z kluczami.

Możesz potwierdzić, że twoja reklama paszportowa aplikacji wyszukuje następujący fragment kodu w aplikacji app.js

```
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

passport.use(new OIDCStrategy({
    //...
));
```

### <a name="web-applications--apis-protecting-resources-and-created-with-visual-studio-2015-or-later"></a><a name="vs2015"></a>Aplikacje sieci Web / interfejsy API chroniące zasoby i utworzone za pomocą programu Visual Studio 2015 lub nowszego
Jeśli aplikacja została sbudowana przy użyciu szablonu aplikacji sieci web w programie Visual Studio 2015 lub nowszym i wybrano **konta pracy lub szkoły** z menu **Uwierzytelnianie zmian,** ma już logikę niezbędną do automatycznego obchodzenia kluczy. Ta logika, osadzona w oprogramowaniu pośredniczącym OWIN OpenID Connect, pobiera i buforuje klucze z dokumentu odnajdywania OpenID Connect i okresowo je odświeża.

Jeśli dodano uwierzytelnianie do rozwiązania ręcznie, aplikacja może nie mieć logiki rolowania kluczy niezbędne. Musisz napisać go samodzielnie lub wykonać kroki opisane w [aplikacjach sieci Web / interfejsach API przy użyciu innych bibliotek lub ręcznie implementując którykolwiek z obsługiwanych protokołów](#other).

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>Aplikacje sieci Web chroniące zasoby i utworzone za pomocą programu Visual Studio 2013
Jeśli aplikacja została zbudowana przy użyciu szablonu aplikacji sieci web w programie Visual Studio 2013 i wybrano **konta organizacyjne** z menu **Uwierzytelnianie zmian,** ma już logikę niezbędną do automatycznego obchodzenia się z kluczem. Ta logika przechowuje unikatowy identyfikator organizacji i informacje o kluczu podpisywania w dwóch tabelach bazy danych skojarzonych z projektem. Ciąg połączenia bazy danych można znaleźć w pliku Web.config projektu.

Jeśli dodano uwierzytelnianie do rozwiązania ręcznie, aplikacja może nie mieć logiki rolowania kluczy niezbędne. Musisz napisać go samodzielnie lub wykonać kroki opisane w [aplikacjach sieci Web / interfejsach API przy użyciu innych bibliotek lub ręcznie implementując którykolwiek z obsługiwanych protokołów.](#other).

Poniższe kroki pomogą Ci sprawdzić, czy logika działa poprawnie w aplikacji.

1. W programie Visual Studio 2013 otwórz rozwiązanie, a następnie kliknij kartę **Eksplorator serwera** w prawym oknie.
2. Rozwiń węzeł **Połączenia danych**, **Połączenie domyślne**, a następnie **tabele**. Znajdź tabelę **IssuingAuthorityKeys,** kliknij ją prawym przyciskiem myszy, a następnie kliknij polecenie **Pokaż dane tabeli**.
3. W **IssuingAuthorityKeys** tabeli będzie co najmniej jeden wiersz, który odpowiada wartości odcisk palca dla klucza. Usuń wszystkie wiersze w tabeli.
4. Kliknij prawym przyciskiem myszy tabelę **Dzierżawcy,** a następnie kliknij polecenie **Pokaż dane tabeli**.
5. W **tabeli Dzierżawcy** będzie co najmniej jeden wiersz, który odpowiada unikatowy identyfikator dzierżawy katalogu. Usuń wszystkie wiersze w tabeli. Jeśli nie usuniesz wierszy w tabeli **Dzierżawy** i **IssuingAuthorityKeys,** otrzymasz błąd w czasie wykonywania.
6. Skompiluj i uruchom aplikację. Po zalogowaniu się na swoje konto możesz zatrzymać aplikację.
7. Wróć do **Eksploratora serwera** i spójrz na wartości w **IssuingAuthorityKeys** i **Tenants** tabeli. Można zauważyć, że zostały one automatycznie ponownie wypełnione odpowiednimi informacjami z dokumentu metadanych federacji.

### <a name="web-apis-protecting-resources-and-created-with-visual-studio-2013"></a><a name="vs2013"></a>Internetowe interfejsy API chroniące zasoby i utworzone za pomocą programu Visual Studio 2013
Jeśli w programie Visual Studio 2013 utworzono aplikację interfejsu API sieci Web przy użyciu szablonu interfejsu API sieci Web, a następnie wybrano **konta organizacyjne** z menu **Zmień uwierzytelnianie,** w aplikacji jest już niezbędna logika.

Jeśli uwierzytelnianie jest konfigurowane ręcznie, postępuj zgodnie z poniższymi instrukcjami, aby dowiedzieć się, jak skonfigurować internetowy interfejs API do automatycznego aktualizowania jego kluczowych informacji.

Poniższy fragment kodu pokazuje, jak uzyskać najnowsze klucze z dokumentu metadanych federacji, a następnie użyć [programu obsługi tokenów JWT](https://msdn.microsoft.com/library/dn205065.aspx) do sprawdzania poprawności tokenu. Fragment kodu przyjęto założenie, że użyjesz własnego mechanizmu buforowania do utrwalania klucza, aby sprawdzić poprawność przyszłych tokenów z usługi Azure AD, niezależnie od tego, czy jest on w bazie danych, pliku konfiguracyjnym, czy w innym miejscu.

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
Jeśli aplikacja została zbudowana w programie Visual Studio 2012, prawdopodobnie użyto narzędzia tożsamości i dostępu do skonfigurowania aplikacji. Jest również prawdopodobne, że używasz [rejestru sprawdzania poprawności nazwy wystawcy (VINR).](https://msdn.microsoft.com/library/dn205067.aspx) Vinr jest odpowiedzialny za przechowywanie informacji o zaufanych dostawców tożsamości (Azure AD) i klucze używane do sprawdzania poprawności tokenów wystawionych przez nich. Vinr ułatwia również automatyczną aktualizację kluczowych informacji przechowywanych w pliku Web.config, pobierając najnowszy dokument metadanych federacji skojarzony z katalogiem, sprawdzając, czy konfiguracja jest nieaktualna z najnowszym dokumentem, i aktualizując aplikację, aby w razie potrzeby użyć nowego klucza.

Jeśli aplikacja została utworzona przy użyciu dowolnego przykładów kodu lub dokumentacji instruktażowej dostarczonej przez firmę Microsoft, logika przerzucenia klucza jest już uwzględniona w projekcie. Można zauważyć, że poniższy kod już istnieje w projekcie. Jeśli aplikacja nie ma jeszcze tej logiki, wykonaj poniższe czynności, aby ją dodać i sprawdzić, czy działa poprawnie.

1. W **Eksploratorze rozwiązań**dodaj odwołanie do zestawu **System.IdentityModel** dla odpowiedniego projektu.
2. Otwórz plik **Global.asax.cs** i dodaj następujące elementy za pomocą dyrektyw:
   ```
   using System.Configuration;
   using System.IdentityModel.Tokens;
   ```
3. Dodaj następującą metodę do pliku **Global.asax.cs:**
   ```
   protected void RefreshValidationSettings()
   {
    string configPath = AppDomain.CurrentDomain.BaseDirectory + "\\" + "Web.config";
    string metadataAddress =
                  ConfigurationManager.AppSettings["ida:FederationMetadataLocation"];
    ValidatingIssuerNameRegistry.WriteToConfig(metadataAddress, configPath);
   }
   ```
4. Wywołaj metodę **RefreshValidationSettings()** w **metodzie Application_Start()** w **Global.asax.cs** jak pokazano na rysunku:
   ```
   protected void Application_Start()
   {
    AreaRegistration.RegisterAllAreas();
    ...
    RefreshValidationSettings();
   }
   ```

Po wykonać następujące kroki, web.config aplikacji zostaną zaktualizowane o najnowsze informacje z dokumentu metadanych federacji, w tym najnowsze klucze. Ta aktualizacja będzie występować za każdym razem, gdy pula aplikacji odtwarza w usługach IIS; domyślnie usługi IIS są ustawione na recykling aplikacji co 29 godzin.

Wykonaj poniższe czynności, aby sprawdzić, czy logika przerzucenia klucza działa.

1. Po sprawdzeniu, że aplikacja używa powyższego kodu, otwórz plik **Web.config** i przejdź do bloku ** \<>issuerNameRegistry,** w szczególności szukając następujących wierszy:
   ```
   <issuerNameRegistry type="System.IdentityModel.Tokens.ValidatingIssuerNameRegistry, System.IdentityModel.Tokens.ValidatingIssuerNameRegistry">
        <authority name="https://sts.windows.net/ec4187af-07da-4f01-b18f-64c2f5abecea/">
          <keys>
            <add thumbprint="3A38FA984E8560F19AADC9F86FE9594BB6AD049B" />
          </keys>
   ```
2. W ustawieniach ** \<dodaj odcisk palca=">** zmień wartość odcisku palca, zastępując dowolny znak innym. Zapisz plik **Web.config.**
3. Skompiluj aplikację, a następnie uruchom ją. Jeśli można ukończyć proces logowania, aplikacja jest pomyślnie aktualizowanie klucza, pobierając wymagane informacje z dokumentu metadanych federacji katalogu. Jeśli występują problemy z logowaniem się, upewnij się, że zmiany w aplikacji są poprawne, czytając artykuł [Dodawanie logowania do aplikacji sieci Web przy użyciu usługi Azure AD](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect) lub pobierając i sprawdzając następujący przykład kodu: [Wielodostępna aplikacja w chmurze dla usługi Azure Active Directory](https://code.msdn.microsoft.com/multi-tenant-cloud-8015b84b).

### <a name="web-applications-protecting-resources-and-created-with-visual-studio-2008-or-2010-and-windows-identity-foundation-wif-v10-for-net-35"></a><a name="vs2010"></a>Aplikacje sieci Web chroniące zasoby i utworzone za pomocą programu Visual Studio 2008 lub 2010 i Windows Identity Foundation (WIF) w wersji 1.0 dla platformy .NET 3.5
Jeśli aplikacja została sbudona na WIF w wersji 1.0, nie ma żadnego dostarczonego mechanizmu automatycznego odświeżania konfiguracji aplikacji w celu użycia nowego klucza.

* *Najprostszy sposób* Użyj narzędzia FedUtil dołączonego do zestawu WIF SDK, który może pobrać najnowszy dokument metadanych i zaktualizować konfigurację.
* Zaktualizuj aplikację do platformy .NET 4.5, która zawiera najnowszą wersję WIF znajdującą się w obszarze nazw systemu. Następnie można użyć [rejestru sprawdzania poprawności nazwy wystawcy (VINR)](https://msdn.microsoft.com/library/dn205067.aspx) do automatycznych aktualizacji konfiguracji aplikacji.
* Wykonaj ręczne najazd zgodnie z instrukcjami na końcu tego dokumentu.

Instrukcje dotyczące korzystania z FedUtil w celu zaktualizowania konfiguracji:

1. Sprawdź, czy masz WIF v1.0 SDK zainstalowany na komputerze deweloperskim dla programu Visual Studio 2008 lub 2010. Możesz [go pobrać stąd,](https://www.microsoft.com/en-us/download/details.aspx?id=4451) jeśli jeszcze go nie zainstalowałeś.
2. W programie Visual Studio otwórz rozwiązanie, a następnie kliknij prawym przyciskiem myszy odpowiedni projekt i wybierz polecenie **Aktualizuj metadane federacji**. Jeśli ta opcja nie jest dostępna, zestaw FedUtil i/lub WIF v1.0 SDK nie został zainstalowany.
3. W wierszu polecenia wybierz **pozycję Aktualizuj,** aby rozpocząć aktualizowanie metadanych federacji. Jeśli masz dostęp do środowiska serwera, w którym aplikacja jest hostowana, możesz opcjonalnie użyć [harmonogramu automatycznej aktualizacji metadanych](https://msdn.microsoft.com/library/ee517272.aspx)FedUtil.
4. Kliknij **przycisk Zakończ,** aby zakończyć proces aktualizacji.

### <a name="web-applications--apis-protecting-resources-using-any-other-libraries-or-manually-implementing-any-of-the-supported-protocols"></a><a name="other"></a>Aplikacje sieci Web / interfejsy API chroniące zasoby przy użyciu innych bibliotek lub ręcznie implementujące którykolwiek z obsługiwanych protokołów
Jeśli używasz innej biblioteki lub ręcznie zaimplementowano dowolny z obsługiwanych protokołów, musisz przejrzeć bibliotekę lub implementację, aby upewnić się, że klucz jest pobierany z dokumentu odnajdywania OpenID Connect lub dokumentu metadanych federacji. Jednym ze sposobów, aby sprawdzić to jest do wyszukiwania w kodzie lub kod biblioteki dla wszelkich wywołań do dokumentu odnajdywania OpenID lub dokumentu metadanych federacji.

Jeśli klucz jest przechowywany gdzieś lub zakodowane na stałe w aplikacji, można ręcznie pobrać klucz i zaktualizować go odpowiednio, wykonując ręczne najazd zgodnie z instrukcjami na końcu tego dokumentu. **Zdecydowanie zaleca się, aby ulepszyć aplikację do obsługi automatycznego przerzucania** przy użyciu dowolnego z podejść z konspektu w tym artykule, aby uniknąć przyszłych zakłóceń i narzutów, jeśli usługa Azure AD zwiększa jego rytm przerzucania lub ma awaryjne przerzucanie poza pasmem.

## <a name="how-to-test-your-application-to-determine-if-it-will-be-affected"></a>Jak przetestować aplikację, aby ustalić, czy będzie ona miała wpływ
Można sprawdzić, czy aplikacja obsługuje automatyczne przerzucanie kluczy, pobierając skrypty i postępując zgodnie z instrukcjami w [tym repozytorium GitHub.](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey)

## <a name="how-to-perform-a-manual-rollover-if-your-application-does-not-support-automatic-rollover"></a>Jak wykonać ręczne najazd, jeśli aplikacja nie obsługuje automatycznego narzucenia
Jeśli aplikacja **nie** obsługuje automatycznego przerzucenia, należy ustanowić proces, który okresowo monitoruje klucze podpisywania usługi Azure AD i wykonuje ręczne przerzucie odpowiednio. [To repozytorium GitHub](https://github.com/AzureAD/azure-activedirectory-powershell-tokenkey) zawiera skrypty i instrukcje, jak to zrobić.
