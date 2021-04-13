---
title: Federacja bezpośrednia z dostawcą tożsamości dla usługi B2B — Azure AD
description: Bezpośrednio federuj z dostawcą tożsamości SAML lub WS-Fed, aby goście mogą logować się do aplikacji usługi Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/06/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 830119a5b3a7781e8b12e3d4df870f539a2cd63a
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107364910"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>Federacja bezpośrednia z AD FS i dostawcami innych firm dla użytkowników-gości (wersja zapoznawcza)

> [!NOTE]
>  Federacja bezpośrednia jest funkcją publicznej wersji zapoznawczej Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz Dodatkowe warunki użytkowania dotyczące [wersji Microsoft Azure zapoznawczych.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

W tym artykule opisano sposób skonfigurowania federacji bezpośredniej z inną organizacją w celu współpracy B2B. Federację bezpośrednią można skonfigurować z dowolną organizacją, której dostawca tożsamości obsługuje protokół SAML 2.0 lub WS-Fed tożsamości.
Po skonfigurowaniu federacji bezpośredniej z tożsamością partnera nowi użytkownicy-goście z tej domeny mogą używać własnego konta organizacyjnego zarządzanego przez idP, aby zalogować się do dzierżawy usługi Azure AD i rozpocząć współpracę z to użytkownikiem. Użytkownik-gość nie musi tworzyć oddzielnego konta usługi Azure AD.

## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>Kiedy użytkownik-gość jest uwierzytelniany za pomocą federacji bezpośredniej?
Po skonfigurowaniu federacji bezpośredniej z organizacją nowi użytkownicy-goście zostaną uwierzytelnieni przy użyciu federacji bezpośredniej. Należy pamiętać, że skonfigurowanie federacji bezpośredniej nie zmienia metody uwierzytelniania dla użytkowników-gości, którzy już zrealizli zaproszenie od Ciebie. Oto kilka przykładów:
 - Jeśli użytkownicy-goście już zrealizują zaproszenia od Ciebie, a następnie skonfigurujemy federację bezpośrednią z ich organizacją, ci użytkownicy-goście będą nadal korzystać z tej samej metody uwierzytelniania, z których korzystali przed skonfigurowaniem federacji bezpośredniej.
 - Jeśli skonfigurujemy federację bezpośrednią z organizacją partnerską i zaproszą użytkowników-gości, a następnie organizacja partnerska zostanie później przeniesiony do usługi Azure AD, użytkownicy-goście, którzy już zrealizują zaproszenia, będą nadal korzystać z federacji bezpośredniej, o ile w dzierżawie istnieją bezpośrednie zasady federacji.
 - Jeśli usuniesz federację bezpośrednią z organizacją partnerską, użytkownicy-goście korzystający obecnie z federacji bezpośredniej nie będą mogli się zalogować.

W każdym z tych scenariuszy możesz zaktualizować metodę uwierzytelniania użytkownika-gościa, [resetując jego stan realizacji](reset-redemption-status.md).

Federacja bezpośrednia jest powiązana z przestrzeniami nazw domeny, takimi jak contoso.com i fabrikam.com. Podczas ustanawiania bezpośredniej konfiguracji federacyjnych z AD FS lub dostawcy tożsamości innej firmy organizacje kojarzą z tymi tożsamościami co najmniej jedną przestrzeń nazw domeny. 

## <a name="end-user-experience"></a>Środowisko użytkownika końcowego 
W przypadku federacji bezpośredniej użytkownicy-goście logują się do dzierżawy usługi Azure AD przy użyciu własnego konta organizacyjnego. Gdy użytkownicy mają dostęp do udostępnionych zasobów i są monitowane o zalogowanie, bezpośredni użytkownicy federatora są przekierowywani do ich idP. Po pomyślnym zalogowaniu są one zwracane do usługi Azure AD w celu uzyskania dostępu do zasobów. Tokeny odświeżania użytkowników federacji bezpośredniej są ważne przez 12 godzin, czyli domyślną długość [tokenu](../develop/active-directory-configurable-token-lifetimes.md#configurable-token-lifetime-properties) odświeżania przekazania w usłudze Azure AD. Jeśli federowany identyfikator tożsamości ma włączoną funkcję logowania jednokrotnego, użytkownik będzie widzieć logowanie jednokrotne i po początkowym uwierzytelnieniu nie zobaczy żadnego monitu o zalogowanie.

## <a name="sign-in-endpoints"></a>Punkty końcowe logowania

Bezpośredni federacyjni użytkownicy-goście mogą teraz logować się do aplikacji wielodostępnych lub aplikacji firmy Microsoft firmy Microsoft przy użyciu wspólnego punktu końcowego [(innymi](redemption-experience.md#redemption-and-sign-in-through-a-common-endpoint) słowy, ogólnego adresu URL aplikacji, który nie zawiera kontekstu dzierżawy). Podczas procesu logowania użytkownik-gość wybiera opcje **logowania,** a następnie wybiera pozycję **Zaloguj się do organizacji.** Następnie użytkownik typi nazwę organizacji i kontynuuje logowanie przy użyciu własnych poświadczeń.

Bezpośredni użytkownicy-goście federacji mogą również używać punktów końcowych aplikacji, które zawierają informacje o dzierżawie, na przykład:

  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`
  * `https://portal.azure.com/<your tenant ID>`

Użytkownikom-gościom federacji bezpośredniej można również nadać bezpośredni link do aplikacji lub zasobu, uwzględniając informacje o dzierżawie, na przykład `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>` .

## <a name="limitations"></a>Ograniczenia

### <a name="dns-verified-domains-in-azure-ad"></a>Domeny zweryfikowane przez system DNS w usłudze Azure AD
Domena, z którą chcesz wykonać federacyjną migrację, nie ***może być*** zweryfikowana przez system DNS w usłudze Azure AD. Możesz skonfigurować bezpośrednią federację z nieza zarządzaniem dzierżawami usługi Azure AD (zweryfikowanymi za pośrednictwem poczty e-mail lub "marketingowym"), ponieważ nie są one weryfikowane przez system DNS.

### <a name="authentication-url"></a>Adres URL uwierzytelniania
Federacja bezpośrednia jest dozwolona tylko w przypadku zasad, w których domena adresu URL uwierzytelniania pasuje do domeny docelowej lub gdy adres URL uwierzytelniania jest jednym z tych dozwolonych dostawców tożsamości (ta lista może ulec zmianie):

-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   oktapreview.com
-   okta-emea.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

Na przykład podczas konfigurowania federacji bezpośredniej dla fabrikam.com **adres** URL uwierzytelniania `https://fabrikam.com/adfs` przejdzie weryfikację. Host w tej samej domenie również przejdzie, na przykład `https://sts.fabrikam.com/adfs` . Jednak adres URL `https://fabrikamconglomerate.com/adfs` uwierzytelniania lub `https://fabrikam.com.uk/adfs` dla tej samej domeny nie zostanie przekaże.

### <a name="signing-certificate-renewal"></a>Odnawianie certyfikatu podpisywania
Jeśli określisz adres URL metadanych w ustawieniach dostawcy tożsamości, usługa Azure AD automatycznie odnowi certyfikat podpisywania po jego wygaśnięciu. Jeśli jednak certyfikat zostanie obrócony z jakiegokolwiek powodu przed upływem czasu wygaśnięcia lub jeśli nie podaniem adresu URL metadanych, usługa Azure AD nie będzie mogła go odnowić. W takim przypadku należy ręcznie zaktualizować certyfikat podpisywania.

### <a name="limit-on-federation-relationships"></a>Ograniczanie relacji federacji
Obecnie obsługiwanych jest maksymalnie 1000 relacji federacji. Ten limit obejmuje zarówno [federacje wewnętrzne, jak](/powershell/module/msonline/set-msoldomainfederationsettings) i federacje bezpośrednie.

### <a name="limit-on-multiple-domains"></a>Ograniczanie wielu domen
Obecnie nie obsługujemy federacji bezpośredniej z wieloma domenami z tej samej dzierżawy.

## <a name="frequently-asked-questions"></a>Często zadawane pytania
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>Czy mogę skonfigurować federację bezpośrednią z domeną, dla której istnieje dzierżawa niezamażona (zweryfikowana za pośrednictwem poczty e-mail)? 
Tak. Jeśli domena nie została zweryfikowana, a dzierżawa nie została poddana przejęcia przez [administratora,](../enterprise-users/domains-admin-takeover.md)możesz skonfigurować federację bezpośrednią z tej domeny. Dzierżawy nieza zarządzania lub zweryfikowane za pomocą poczty e-mail są tworzone, gdy użytkownik realizuje zaproszenie B2B lub wykonuje samoobsługowe rejestracja w usłudze Azure AD przy użyciu domeny, która obecnie nie istnieje. Można skonfigurować federację bezpośrednią z tymi domenami. Jeśli spróbujesz skonfigurować federację bezpośrednią z domeną zweryfikowaną przez system DNS, w witrynie Azure Portal lub za pomocą programu PowerShell, zostanie wyświetlony błąd.
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>Jeśli włączono federację bezpośrednią i uwierzytelnianie za pomocą jednego kodu dostępu w wiadomości e-mail, która metoda ma pierwszeństwo?
Po nadaniu federacji bezpośredniej z organizacją partnerską ma ona pierwszeństwo przed uwierzytelnianiem za pomocą jednego kodu dostępu w wiadomości e-mail dla nowych użytkowników-gości z tej organizacji. Jeśli przed skonfigurowaniem federacji bezpośredniej użytkownik-gość zrealił zaproszenie przy użyciu uwierzytelniania za pomocą jednego kodu dostępu, będzie nadal używać uwierzytelniania za pomocą jednego kodu dostępu. 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>Czy federacja bezpośrednia rozsyła problemy z logowaniem spowodowane częściowo zsynchronizowaną usługą?
Nie, w tym [scenariuszu należy](one-time-passcode.md) użyć funkcji kodu dostępu w wiadomości e-mail z jednym kodem dostępu. "Częściowo zsynchronizowana dzierżawa" odnosi się do dzierżawy usługi Azure AD partnera, w której tożsamości użytkowników lokalnych nie są w pełni synchronizowane z chmurą. Gość, którego tożsamość jeszcze nie istnieje w chmurze, ale który spróbuje zrealizować zaproszenie B2B, nie będzie mógł się zalogować. Funkcja kodu dostępu tylko raz umożliwiłaby temu gościowi zalogowanie się. Funkcja federacji bezpośredniej dotyczy scenariuszy, w których gość ma własne konto organizacyjne zarządzane przez idP, ale organizacja w ogóle nie ma obecności w usłudze Azure AD.
### <a name="once-direct-federation-is-configured-with-an-organization-does-each-guest-need-to-be-sent-and-redeem-an-individual-invitation"></a>Czy po skonfigurowaniu federacji bezpośredniej z organizacją każdy gość musi zostać wysłany i zrealizować indywidualne zaproszenie?
Skonfigurowanie federacji bezpośredniej nie zmienia metody uwierzytelniania dla użytkowników-gości, którzy już zrealizli zaproszenie od Ciebie. Możesz zaktualizować metodę uwierzytelniania użytkownika-gościa, [resetując jego stan realizacji](reset-redemption-status.md).
## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>Krok 1. Konfigurowanie dostawcy tożsamości organizacji partnerskiej
Najpierw organizacja partnerska musi skonfigurować swojego dostawcę tożsamości przy użyciu wymaganych oświadczeń i relacji zaufania jednostki zależnej. 

> [!NOTE]
> Aby zilustrować sposób konfigurowania dostawcy tożsamości na użytek federacji bezpośredniej, użyjemy Active Directory Federation Services (AD FS) jako przykładu. Zobacz artykuł Konfigurowanie federacji bezpośredniej za pomocą usługi [AD FS](direct-federation-adfs.md), który zawiera przykłady konfigurowania usługi AD FS jako dostawcy tożsamości SAML 2.0 lub WS-Fed w ramach przygotowania do federacji bezpośredniej.

### <a name="saml-20-configuration"></a>Konfiguracja SAML 2.0

Usługę Azure AD B2B można skonfigurować tak, aby federowała z dostawcami tożsamości, którzy korzystają z protokołu SAML, z określonymi wymaganiami wymienionymi poniżej. Aby uzyskać więcej informacji na temat konfigurowania relacji zaufania między dostawcą tożsamości SAML i usługą Azure AD, zobacz  [Use a SAML 2.0 Identity Provider (IdP) for Single Sign-On (Używanie dostawcy tożsamości SAML 2.0](../hybrid/how-to-connect-fed-saml-idp.md)na użytek logowania pojedynczego).  

> [!NOTE]
> Domena docelowa federacji bezpośredniej nie może być weryfikowana przez system DNS w usłudze Azure AD. Domena adresu URL uwierzytelniania musi odpowiadać domenie docelowej lub musi być domeną dozwolonego dostawcy tożsamości. Aby uzyskać [szczegółowe informacje,](#limitations) zobacz sekcję Ograniczenia. 

#### <a name="required-saml-20-attributes-and-claims"></a>Wymagane atrybuty i oświadczenia SAML 2.0
W poniższych tabelach przedstawiono wymagania dotyczące określonych atrybutów i oświadczeń, które muszą być skonfigurowane u zewnętrznego dostawcy tożsamości. Aby skonfigurować federację bezpośrednią, w odpowiedzi SAML 2.0 dostawcy tożsamości muszą zostać odebrane następujące atrybuty. Te atrybuty można skonfigurować, łącząc się z plikiem XML usługi tokenu zabezpieczającego online lub wprowadzając je ręcznie.

Atrybuty wymagane dla odpowiedzi SAML 2.0 od idp:

|Atrybut  |Wartość  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Grupy odbiorców     |`urn:federation:MicrosoftOnline`         |
|Wystawca     |Identyfikator URI wystawcy partnera, na przykład `http://www.example.com/exk10l6w90DHM0yi...`         |


Wymagane oświadczenia dla tokenu SAML 2.0 wystawionego przez idP:

|Atrybut  |Wartość  |
|---------|---------|
|NameID Format     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>WS-Fed konfiguracji 
Usługę Azure AD B2B można skonfigurować tak, aby federowała z dostawcami tożsamości, którzy używają protokołu WS-Fed z określonymi wymaganiami wymienionymi poniżej. Obecnie dwaj dostawcy WS-Fed zostały przetestowane pod kątem zgodności z usługą Azure AD: AD FS i Shibboleth. Aby uzyskać więcej informacji na temat ustanawiania zaufania jednostki zależnej między dostawcą zgodnym z usługą WS-Fed za pomocą usługi Azure AD, zobacz "Dokument integracyjny usługi STS korzystający z protokołów WS" dostępny w dokumencie Azure [AD Identity Provider Compatibility (Dokument](https://www.microsoft.com/download/details.aspx?id=56843)zgodności dostawcy tożsamości usługi Azure AD).

> [!NOTE]
> Domena docelowa federacji bezpośredniej nie może być zweryfikowana przez system DNS w usłudze Azure AD. Domena adresu URL uwierzytelniania musi odpowiadać domenie docelowej lub domenie dozwolonego dostawcy tożsamości. Aby uzyskać [szczegółowe informacje,](#limitations) zobacz sekcję Ograniczenia. 

#### <a name="required-ws-fed-attributes-and-claims"></a>Wymagane WS-Fed atrybutów i oświadczeń

W poniższych tabelach przedstawiono wymagania dotyczące określonych atrybutów i oświadczeń, które muszą być skonfigurowane u dostawcy tożsamości WS-Fed innej firmy. Aby skonfigurować federację bezpośrednią, następujące atrybuty muszą zostać odebrane w WS-Fed od dostawcy tożsamości. Te atrybuty można skonfigurować, łącząc się z plikiem XML usługi tokenu zabezpieczającego online lub wprowadzając je ręcznie.

Wymagane atrybuty w WS-Fed od idp:
 
|Atrybut  |Wartość  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Grupy odbiorców     |`urn:federation:MicrosoftOnline`         |
|Wystawca     |Identyfikator URI wystawcy partnera, na przykład `http://www.example.com/exk10l6w90DHM0yi...`         |

Wymagane oświadczenia dla tokenu WS-Fed wystawionego przez idP:

|Atrybut  |Wartość  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>Krok 2. Konfigurowanie federacji bezpośredniej w usłudze Azure AD 
Następnie skonfigurujesz federację z dostawcą tożsamości skonfigurowanym w kroku 1 w usłudze Azure AD. Możesz użyć portalu usługi Azure AD lub programu PowerShell. Zanim zasady federacji bezpośredniej zajdą w życie, może mi potrwać 5–10 minut. W tym czasie nie należy próbować zrealizować zaproszenia dla domeny federacji bezpośredniej. Wymagane są następujące atrybuty:
- Identyfikator URI wystawcy partnera
- Pasywny punkt końcowy uwierzytelniania partnera IP (obsługiwany jest tylko protokół HTTPS)
- Certyfikat

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>Aby skonfigurować federację bezpośrednią w portalu usługi Azure AD

1. Przejdź do witryny [Azure Portal](https://portal.azure.com/). W lewym okienku wybierz pozycję **Azure Active Directory**. 
2. Wybierz **External Identities**  >  **wszyscy dostawcy tożsamości.**
3. Wybierz **pozycję Nowy identyfikator SAML/WS-Fed.**

    ![Zrzut ekranu przedstawiający przycisk dodawania nowego identyfikatora SAML lub WS-Fed tożsamości](media/direct-federation/new-saml-wsfed-idp.png)

4. Na stronie **Nowy dostawca tożsamości SAML/WS-Fed** w obszarze **Identity provider protocol**(Protokół dostawcy tożsamości) wybierz pozycję **SAML** lub **WS-FED.**

    ![Zrzut ekranu przedstawiający przycisk analizowania na stronie saml lub WS-Fed tożsamości](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. Wprowadź nazwę domeny organizacji partnerskiej, która będzie nazwą domeny docelowej federacji bezpośredniej
6. Możesz przekazać plik metadanych, aby wypełnić szczegóły metadanych. Jeśli zdecydujesz się wprowadzić metadane ręcznie, wprowadź następujące informacje:
   - Nazwa domeny partnera IP
   - Identyfikator jednostki partnera IP
   - Pasywny punkt końcowy żądania partnera IP
   - Certyfikat
   > [!NOTE]
   > Adres URL metadanych jest opcjonalny, jednak zdecydowanie go zalecamy. W przypadku podania adresu URL metadanych usługa Azure AD może automatycznie odnowić certyfikat podpisywania po jego wygaśnięciu. Jeśli certyfikat zostanie obrócony z jakiegokolwiek powodu przed upływem czasu wygaśnięcia lub jeśli nie podaniem adresu URL metadanych, usługa Azure AD nie będzie mogła go odnowić. W takim przypadku należy ręcznie zaktualizować certyfikat podpisywania.

7. Wybierz pozycję **Zapisz**. 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>Aby skonfigurować federację bezpośrednią w usłudze Azure AD przy użyciu programu PowerShell

1. Zainstaluj najnowszą wersję modułu Azure AD PowerShell dla programu Graph[(AzureADPreview).](https://www.powershellgallery.com/packages/AzureADPreview) (Jeśli potrzebujesz szczegółowych kroków, przewodnik Szybki start dla dodawania użytkownika-gościa zawiera sekcję Instalowanie najnowszego modułu [AzureADPreview).](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module) 
2. Uruchom następujące polecenie: 
   ```powershell
   Connect-AzureAD
   ```
1. Po wyświetleniu monitu logowania zaloguj się przy użyciu zarządzanego konta administratora globalnego. 
2. Uruchom następujące polecenia, zastępując wartości z pliku metadanych federacji. W AD FS Server i Okta plik federowy jest federationmetadata.xml, na przykład: `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml` . 

   ```powershell
   $federationSettings = New-Object Microsoft.Open.AzureAD.Model.DomainFederationSettings
   $federationSettings.PassiveLogOnUri ="https://sts.totheclouddemo.com/adfs/ls/"
   $federationSettings.LogOffUri = $federationSettings.PassiveLogOnUri
   $federationSettings.IssuerUri = "http://sts.totheclouddemo.com/adfs/services/trust"
   $federationSettings.MetadataExchangeUri="https://sts.totheclouddemo.com/adfs/services/trust/mex"
   $federationSettings.SigningCertificate= <Replace with X509 signing cert’s public key>
   $federationSettings.PreferredAuthenticationProtocol="WsFed" OR "Samlp"
   $domainName = <Replace with domain name>
   New-AzureADExternalDomainFederation -ExternalDomainName $domainName  -FederationSettings $federationSettings
   ```

## <a name="step-3-test-direct-federation-in-azure-ad"></a>Krok 3. Testowanie federacji bezpośredniej w usłudze Azure AD
Teraz przetestuj konfigurację federacji bezpośredniej, zapraszając nowego użytkownika-gościa B2B. Aby uzyskać szczegółowe informacje, [zobacz Dodawanie użytkowników współpracy B2B](add-users-administrator.md)usługi Azure AD w Azure Portal .
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>Jak mogę edytować bezpośrednią relację federacyjną?

1. Przejdź do witryny [Azure Portal](https://portal.azure.com/). W lewym okienku wybierz pozycję **Azure Active Directory**. 
2. Wybierz **External Identities**.
3. Wybierz **pozycję Wszyscy dostawcy tożsamości**
4. W **obszarze Dostawcy tożsamości SAML/WS-Fed** wybierz dostawcę.
5. W okienku szczegółów dostawcy tożsamości zaktualizuj wartości.
6. Wybierz pozycję **Zapisz**.


## <a name="how-do-i-remove-direct-federation"></a>Jak mogę usunąć federację bezpośrednią?
Możesz usunąć konfigurację federacji bezpośredniej. Jeśli tak zrobisz, bezpośredni federacyjni użytkownicy-goście, którzy już zrealizują swoje zaproszenia, nie będą mogli się zalogować. Można jednak ponownie udzielić im dostępu do [zasobów, resetując ich stan realizacji](reset-redemption-status.md). Aby usunąć bezpośrednią federację z dostawcą tożsamości w portalu usługi Azure AD:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com/). W lewym okienku wybierz pozycję **Azure Active Directory**. 
2. Wybierz **External Identities**.
3. Wybierz **pozycję Wszyscy dostawcy tożsamości.**
4. Wybierz dostawcę tożsamości, a następnie wybierz pozycję **Usuń.** 
5. Wybierz **pozycję Tak,** aby potwierdzić usunięcie. 

Aby usunąć federację bezpośrednią z dostawcą tożsamości przy użyciu programu PowerShell:
1. Zainstaluj najnowszą wersję modułu Azure AD PowerShell dla programu Graph[(AzureADPreview).](https://www.powershellgallery.com/packages/AzureADPreview)
2. Uruchom następujące polecenie: 
   ```powershell
   Connect-AzureAD
   ```
3. Po wyświetleniu monitu logowania zaloguj się przy użyciu zarządzanego konta administratora globalnego. 
4. Wprowadź następujące polecenie:
   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [obsługi realizacji zaproszenia,](redemption-experience.md) gdy użytkownicy zewnętrzni logują się u różnych dostawców tożsamości.
