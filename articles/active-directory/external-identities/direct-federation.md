---
title: Bezpośrednia Federacja z dostawcą tożsamości dla B2B — Azure AD
description: Bezpośrednio sfederować z dostawcą tożsamości SAML lub WS-Fed, aby Goście mogli logować się do aplikacji usługi Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 598cbf303c8a87675833b8d87f05055771e46f55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101687247"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>Bezpośrednia Federacja z dostawcami AD FS i innych firm dla użytkowników-Gości (wersja zapoznawcza)

> [!NOTE]
>  Federacja bezpośrednia jest publiczną funkcją w wersji zapoznawczej Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

W tym artykule opisano sposób konfigurowania Federacji bezpośredniej z inną organizacją do współpracy B2B. Można skonfigurować bezpośrednią Federacji z każdą organizacją, której dostawca tożsamości (dostawcy tożsamości) obsługuje protokół SAML 2,0 lub WS-Fed.
Po skonfigurowaniu bezpośredniej Federacji z dostawcy tożsamości partnera nowi użytkownicy-Goście z tej domeny mogą używać własnego konta organizacji zarządzanego przez dostawcy tożsamości do logowania się do dzierżawy usługi Azure AD i rozpoczynania współpracy z Twoimi użytkownikami. Użytkownik-Gość nie musi tworzyć oddzielnego konta usługi Azure AD.

## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>Kiedy jest uwierzytelniany przez użytkownika-gościa za pomocą Federacji bezpośredniej?
Po skonfigurowaniu bezpośredniej Federacji z organizacją Wszyscy nowi zaproszeni użytkownicy-Goście będą uwierzytelniani za pomocą Federacji bezpośredniej. Należy pamiętać, że skonfigurowanie Federacji bezpośredniej nie zmienia metody uwierzytelniania dla użytkowników-Gości, którzy już wykorzystali zaproszenie. Oto kilka przykładów:
 - Jeśli użytkownicy-Goście już wykorzystali zaproszenia z Ciebie, a następnie skonfigurujesz bezpośrednią Federacji z organizacją, Ci użytkownicy będą nadal używać tej samej metody uwierzytelniania, która została użyta przed skonfigurowaniem Federacji bezpośredniej.
 - Jeśli skonfigurujesz bezpośrednią Federacji z organizacją partnera i zapraszasz użytkowników-Gości, a następnie organizacja partnera później przejdzie do usługi Azure AD, użytkownicy-Goście, którzy już wykorzystali zaproszenia, będą nadal korzystać z Federacji bezpośredniej, o ile istnieją bezpośrednie zasady Federacji w dzierżawie.
 - W przypadku usunięcia bezpośredniej Federacji z organizacją partnera wszyscy użytkownicy-Goście korzystający obecnie z Federacji bezpośredniej nie będą mogli się zalogować.

W każdym z tych scenariuszy można zaktualizować metodę uwierzytelniania użytkownika-gościa, usuwając konto użytkownika-gościa z katalogu i zapraszając je.

Federacja bezpośrednia jest powiązana z przestrzeniami nazw domen, takimi jak contoso.com i fabrikam.com. Podczas ustanawiania bezpośredniej konfiguracji federacji z AD FS lub dostawcy tożsamości innych firm organizacje kojarzą co najmniej jedną przestrzeń nazw domeny z tymi dostawców tożsamości. 

## <a name="end-user-experience"></a>Środowisko użytkownika końcowego 
W przypadku federacji bezpośredniej użytkownicy-Goście logują się do dzierżawy usługi Azure AD przy użyciu konta organizacyjnego. Gdy uzyskują dostęp do zasobów udostępnionych i zostanie wyświetlony monit o zalogowanie się, użytkownicy Federacji bezpośrednich są przekierowywani do ich dostawcy tożsamości. Po pomyślnym zalogowaniu są one zwracane do usługi Azure AD w celu uzyskania dostępu do zasobów. Tokeny odświeżania bezpośrednich użytkowników federacyjnych są prawidłowe przez 12 godzin, a [Domyślna długość tokenu odświeżania przekazywania](../develop/active-directory-configurable-token-lifetimes.md#exceptions) w usłudze Azure AD. Jeśli Federacja federacyjna dostawcy tożsamości ma włączone Logowanie jednokrotne, użytkownik będzie miał Logowanie jednokrotne i nie zobaczy monitu logowania po uwierzytelnieniu początkowym.

## <a name="sign-in-endpoints"></a>Punkty końcowe logowania

Bezpośredni dostęp Gości w Federacji może teraz zalogować się do aplikacji firmowych z wieloma dzierżawcami lub firmy Microsoft przy użyciu [wspólnego punktu końcowego](redemption-experience.md#redemption-and-sign-in-through-a-common-endpoint) (innymi słowy, ogólny adres URL aplikacji, który nie obejmuje kontekstu dzierżawy). Podczas procesu logowania użytkownik-Gość wybierze **Opcje logowania**, a następnie wybierze opcję **Zaloguj się do organizacji**. Następnie użytkownik wpisze nazwę organizacji i kontynuuje Logowanie przy użyciu własnych poświadczeń.

Bezpośredni dostęp gościa Federacji może również używać punktów końcowych aplikacji, które zawierają informacje o dzierżawie, na przykład:

  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`
  * `https://portal.azure.com/<your tenant ID>`

Możesz również udzielić bezpośredniemu użytkownikowi gościa federacyjnego bezpośredniego linku do aplikacji lub zasobu, dołączając informacje o dzierżawie, na przykład `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>` .

## <a name="limitations"></a>Ograniczenia

### <a name="dns-verified-domains-in-azure-ad"></a>Domeny zweryfikowane przez usługę DNS w usłudze Azure AD
Domena, której chcesz sfederować, ***nie*** może być zweryfikowana przez system DNS w usłudze Azure AD. Istnieje możliwość skonfigurowania Federacji bezpośredniej z niezarządzanymi (zweryfikowanymi pocztą e-mail lub "wirusami") dzierżaw usługi Azure AD, ponieważ nie są one zweryfikowane przez system DNS.

### <a name="authentication-url"></a>Adres URL uwierzytelniania
Federacja bezpośrednia jest dozwolona tylko w przypadku zasad, w których domena adresu URL uwierzytelniania jest zgodna z domeną docelową lub jeśli adres URL uwierzytelniania jest jednym z tych dozwolonych dostawców tożsamości (Ta lista może ulec zmianie):

-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   oktapreview.com
-   okta-emea.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

Na przykład podczas konfigurowania Federacji bezpośredniej dla **fabrikam.com**, adres URL uwierzytelniania `https://fabrikam.com/adfs` przekaże weryfikację. Host w tej samej domenie również zostanie przekazany na przykład `https://sts.fabrikam.com/adfs` . Jednak adres URL uwierzytelniania `https://fabrikamconglomerate.com/adfs` lub `https://fabrikam.com.uk/adfs` dla tej samej domeny nie zostanie przekazany.

### <a name="signing-certificate-renewal"></a>Odnawianie certyfikatu podpisywania
Jeśli określisz adres URL metadanych w ustawieniach dostawcy tożsamości, usługa Azure AD automatycznie odnowi certyfikat podpisywania po jego wygaśnięciu. Jeśli jednak certyfikat jest obrócony z dowolnego powodu przed upływem czasu wygaśnięcia lub jeśli nie podano adresu URL metadanych, usługa Azure AD nie będzie mogła go odnowić. W takim przypadku należy ręcznie zaktualizować certyfikat podpisywania.

### <a name="limit-on-federation-relationships"></a>Limit relacji federacyjnych
Obecnie obsługiwane są maksymalnie 1 000 relacje federacyjne. Ten limit obejmuje [wewnętrzne Federacji](/powershell/module/msonline/set-msoldomainfederationsettings) i bezpośrednie Federacji.

### <a name="limit-on-multiple-domains"></a>Ogranicz dla wielu domen
Obecnie nie obsługujemy bezpośredniej Federacji z wieloma domenami z tej samej dzierżawy.

## <a name="frequently-asked-questions"></a>Często zadawane pytania
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>Czy można skonfigurować bezpośrednią Federacji z domeną, dla której istnieje niezarządzana dzierżawa (zweryfikowana za pośrednictwem poczty e-mail)? 
Tak. Jeśli domena nie została zweryfikowana i dzierżawa nie przeszła [przejęcia przez administratora](../enterprise-users/domains-admin-takeover.md), można skonfigurować bezpośrednią Federacji z tą domeną. Niezarządzane lub zweryfikowane pocztą e-mail dzierżawy są tworzone, gdy użytkownik zrealizuje zaproszenie B2B lub wykonuje samoobsługowe Tworzenie konta w usłudze Azure AD przy użyciu domeny, która obecnie nie istnieje. Można skonfigurować bezpośrednią Federacji z tymi domenami. W przypadku próby skonfigurowania bezpośredniej Federacji z domeną zweryfikowaną przez system DNS w Azure Portal lub za pomocą programu PowerShell zostanie wyświetlony komunikat o błędzie.
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>Jeśli w przypadku federacji bezpośredniej i poczty e-mail jednorazowe uwierzytelnianie kodu dostępu jest włączone, która metoda ma pierwszeństwo?
W przypadku ustanowienia bezpośredniej Federacji z organizacją partnera ma ona wyższy priorytet niż jednorazowe uwierzytelnianie kodu dostępu do poczty e-mail dla nowych użytkowników-Gości z tej organizacji. Jeśli użytkownik-Gość wykorzystał zaproszenie przy użyciu jednorazowego uwierzytelniania kodu dostępu, przed skonfigurowaniem Federacji bezpośredniej będzie nadal korzystać z uwierzytelniania jednorazowego kodu dostępu. 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>Czy bezpośrednie problemy z logowaniem do adresu federacyjnego są spowodowane częściowo zsynchronizowaną dzierżawą?
Nie. w tym scenariuszu należy użyć funkcji [jednokrotnego kodu dostępu w wiadomości e-mail](one-time-passcode.md) . "Częściowo zsynchronizowana dzierżawa" odnosi się do dzierżawy usługi Azure AD partnera, w której tożsamości użytkowników lokalnych nie są w pełni zsynchronizowane z chmurą. Gość, którego tożsamość nie istnieje jeszcze w chmurze, ale próbujących zrealizować zaproszenie B2B nie będzie mógł się zalogować. Funkcja jednorazowy kod dostępu zezwoli temu gościa na logowanie. Funkcja Federacji bezpośredniej dotyczy scenariuszy, w których Gość ma własne konto organizacyjne zarządzane przez dostawcy tożsamości, ale w ogóle nie ma obecności usługi Azure AD.
### <a name="once-direct-federation-is-configured-with-an-organization-does-each-guest-need-to-be-sent-and-redeem-an-individual-invitation"></a>Czy po skonfigurowaniu bezpośredniej Federacji z organizacją należy wysłać każdy gościa i zrealizować pojedyncze zaproszenie?
Skonfigurowanie Federacji bezpośredniej nie powoduje zmiany metody uwierzytelniania dla użytkowników-Gości, którzy już skorzystali z zaproszenia. Można zaktualizować metodę uwierzytelniania użytkownika-gościa, usuwając konto użytkownika-gościa z katalogu i zapraszając je.
## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>Krok 1. Konfigurowanie dostawcy tożsamości organizacji partnerskiej
Najpierw organizacja partnera musi skonfigurować swojego dostawcę tożsamości z wymaganymi oświadczeniami i relacjami zaufania jednostek uzależnionych. 

> [!NOTE]
> Aby zilustrować sposób konfigurowania dostawcy tożsamości dla Federacji bezpośredniej, będziemy używać Active Directory Federation Services (AD FS) jako przykładu. Zapoznaj się z artykułem [Konfigurowanie bezpośredniej Federacji z AD FS](direct-federation-adfs.md), która zawiera przykłady konfigurowania AD FS jako dostawcy tożsamości SAML 2,0 lub WS-Fed w przygotowaniu do bezpośredniego Federacji.

### <a name="saml-20-configuration"></a>Konfiguracja protokołu SAML 2,0

Usługę Azure AD B2B można skonfigurować tak, aby sfederować się z dostawcami tożsamości, którzy korzystają z protokołu SAML z określonymi wymaganiami wymienionymi poniżej. Aby uzyskać więcej informacji na temat konfigurowania zaufania między dostawcą tożsamości SAML i usługą Azure AD, zobacz  [Używanie dostawcy tożsamości saml 2,0 (dostawcy tożsamości) na potrzeby logowania jednokrotnego](../hybrid/how-to-connect-fed-saml-idp.md).  

> [!NOTE]
> Domena docelowa dla Federacji bezpośredniej nie może być zweryfikowana przez system DNS w usłudze Azure AD. Domena adresu URL uwierzytelniania musi być zgodna z domeną docelową lub musi być domeną dozwolonego dostawcy tożsamości. Szczegółowe informacje znajdują się w sekcji [ograniczenia](#limitations) . 

#### <a name="required-saml-20-attributes-and-claims"></a>Wymagane atrybuty i oświadczenia SAML 2,0
W poniższych tabelach przedstawiono wymagania dotyczące określonych atrybutów i oświadczeń, które muszą być skonfigurowane w dostawcy tożsamości innej firmy. Aby skonfigurować bezpośrednią Federacji, należy odebrać następujące atrybuty w odpowiedzi SAML 2,0 od dostawcy tożsamości. Te atrybuty można skonfigurować, łącząc się z plikiem XML usługi tokenu zabezpieczającego w trybie online lub wprowadzając je ręcznie.

Atrybuty wymagane dla odpowiedzi SAML 2,0 z dostawcy tożsamości:

|Atrybut  |Wartość  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|Grupy odbiorców     |`urn:federation:MicrosoftOnline`         |
|Wystawca     |Identyfikator URI wystawcy partnera dostawcy tożsamości, na przykład `http://www.example.com/exk10l6w90DHM0yi...`         |


Wymagane oświadczenia dla tokenu SAML 2,0 wystawionego przez dostawcy tożsamości:

|Atrybut  |Wartość  |
|---------|---------|
|Format NameID     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>Konfiguracja WS-Fed 
Usługę Azure AD B2B można skonfigurować tak, aby sfederować się z dostawcami tożsamości, którzy używają protokołu WS-Fed z pewnymi określonymi wymaganiami wymienionymi poniżej. Obecnie dwaj dostawcy WS-Fed zostali przetestowani pod kątem zgodności z usługą Azure AD, w tym AD FS i Shibboleth. Aby uzyskać więcej informacji na temat ustanawiania zaufania jednostki uzależnionej między dostawcą zgodnym WS-Fed a usługą Azure AD, zobacz "papier integracji usługi STS przy użyciu protokołów WS" dostępnych w dokumentach [zgodności dostawcy tożsamości usługi Azure AD](https://www.microsoft.com/download/details.aspx?id=56843).

> [!NOTE]
> Domena docelowa dla Federacji bezpośredniej nie może być zweryfikowana przez system DNS w usłudze Azure AD. Domena adresu URL uwierzytelniania musi być zgodna z domeną docelową lub domeną dozwolonego dostawcy tożsamości. Szczegółowe informacje znajdują się w sekcji [ograniczenia](#limitations) . 

#### <a name="required-ws-fed-attributes-and-claims"></a>Wymagane WS-Fed atrybuty i oświadczenia

W poniższych tabelach przedstawiono wymagania dotyczące określonych atrybutów i oświadczeń, które muszą być skonfigurowane w ramach dostawcy tożsamości WS-Fed innej firmy. Aby skonfigurować bezpośrednią Federacji, należy odebrać następujące atrybuty w komunikacie WS-Fed od dostawcy tożsamości. Te atrybuty można skonfigurować, łącząc się z plikiem XML usługi tokenu zabezpieczającego w trybie online lub wprowadzając je ręcznie.

Wymagane atrybuty w komunikacie WS-Fed z dostawcy tożsamości:
 
|Atrybut  |Wartość  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|Grupy odbiorców     |`urn:federation:MicrosoftOnline`         |
|Wystawca     |Identyfikator URI wystawcy partnera dostawcy tożsamości, na przykład `http://www.example.com/exk10l6w90DHM0yi...`         |

Wymagane oświadczenia dla WS-Fed tokenu wystawionego przez dostawcy tożsamości:

|Atrybut  |Wartość  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>Krok 2. Konfigurowanie bezpośredniej Federacji w usłudze Azure AD 
Następnie skonfigurujesz Federacji z dostawcą tożsamości skonfigurowanym w kroku 1 w usłudze Azure AD. Możesz użyć portalu usługi Azure AD lub programu PowerShell. Przed zastosowaniem bezpośrednich zasad Federacji może upłynąć 5-10 minut. W tym czasie nie należy próbować zrealizować zaproszenia dla domeny Federacji bezpośredniej. Wymagane są następujące atrybuty:
- Identyfikator URI wystawcy partnera dostawcy tożsamości
- Punkt końcowy uwierzytelniania pasywnego dostawcy tożsamości partnera (obsługiwana jest tylko obsługa protokołu HTTPS)
- Certyfikat

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>Aby skonfigurować bezpośrednią Federacji w portalu usługi Azure AD

1. Przejdź do witryny [Azure Portal](https://portal.azure.com/). W lewym okienku wybierz pozycję **Azure Active Directory**. 
2. Wybierz **tożsamości zewnętrzne**  >  **Wszyscy dostawcy tożsamości**.
3. Wybierz pozycję **Nowy dostawcy tożsamości SAML/WS-karmione**.

    ![Zrzut ekranu przedstawiający przycisk dodawania nowego elementu SAML lub WS-Fed dostawcy tożsamości](media/direct-federation/new-saml-wsfed-idp.png)

4. Na **nowej stronie dostawcy tożsamości SAML/WS-karmione** w obszarze **Protokół dostawcy tożsamości** wybierz pozycję **SAML** lub **WS-karmione**.

    ![Zrzut ekranu przedstawiający przycisk analizy na stronie dostawcy tożsamości SAML lub WS-Fed](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. Wprowadź nazwę domeny organizacji partnera, która będzie nazwą domeny docelowej dla Federacji bezpośredniej
6. Aby wypełnić szczegóły metadanych, można przekazać plik metadanych. Jeśli zdecydujesz się na ręczne wprowadzanie metadanych, wprowadź następujące informacje:
   - Nazwa domeny dostawcy tożsamości partnera
   - Identyfikator jednostki dostawcy tożsamości partnera
   - Punkt końcowy żądania pasywnego dostawcy tożsamości partnera
   - Certyfikat
   > [!NOTE]
   > Adres URL metadanych jest opcjonalny, jednak zdecydowanie zalecamy. W przypadku podania adresu URL metadanych usługa Azure AD może automatycznie odnowić certyfikat podpisywania po jego wygaśnięciu. Jeśli certyfikat jest obrócony z dowolnego powodu przed upływem czasu wygaśnięcia lub jeśli nie podasz adresu URL metadanych, usługa Azure AD nie będzie mogła go odnowić. W takim przypadku należy ręcznie zaktualizować certyfikat podpisywania.

7. Wybierz pozycję **Zapisz**. 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>Aby skonfigurować bezpośrednią Federacji w usłudze Azure AD przy użyciu programu PowerShell

1. Zainstaluj najnowszą wersję modułu PowerShell usługi Azure AD dla programu Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)). (Jeśli potrzebujesz szczegółowych kroków, Przewodnik Szybki Start dotyczący dodawania użytkownika-gościa zawiera sekcję [Instalowanie najnowszego modułu AzureADPreview](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module)). 
2. Uruchom następujące polecenie: 
   ```powershell
   Connect-AzureAD
   ```
1. W wierszu logowania zaloguj się przy użyciu zarządzanego konta administratora globalnego. 
2. Uruchom następujące polecenia, zastępując wartości z pliku metadanych Federacji. W przypadku AD FS Server i usługi okta plik federacyjny federationmetadata.xml, na przykład: `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml` . 

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

## <a name="step-3-test-direct-federation-in-azure-ad"></a>Krok 3. Testowanie Federacji bezpośredniej w usłudze Azure AD
Teraz Przetestuj konfigurację Federacji bezpośredniej, zapraszając nowego użytkownika-gościa B2B. Aby uzyskać szczegółowe informacje, zobacz [Dodawanie użytkowników współpracy B2B usługi Azure AD w Azure Portal](add-users-administrator.md).
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>Jak mogę edytować bezpośredniej relacji Federacji?

1. Przejdź do witryny [Azure Portal](https://portal.azure.com/). W lewym okienku wybierz pozycję **Azure Active Directory**. 
2. Wybierz **tożsamości zewnętrzne**.
3. Wybierz **wszystkich dostawców tożsamości**
4. W obszarze **dostawcy tożsamości protokołu SAML/WS-karmionego** wybierz dostawcę.
5. W okienku szczegółów dostawcy tożsamości zaktualizuj wartości.
6. Wybierz pozycję **Zapisz**.


## <a name="how-do-i-remove-direct-federation"></a>Jak mogę usunąć Federacji bezpośredniej?
Możesz usunąć konfigurację Federacji bezpośredniej. Jeśli to zrobisz, wszyscy użytkownicy-Goście Federacji, którzy już korzystali z zaproszenia, nie będą mogli się zalogować. Można jednak umożliwić im ponowne uzyskanie dostępu do zasobów, usuwając je z katalogu i ponownie zapraszając. Aby usunąć bezpośrednią Federacji z dostawcą tożsamości w portalu usługi Azure AD:

1. Przejdź do witryny [Azure Portal](https://portal.azure.com/). W lewym okienku wybierz pozycję **Azure Active Directory**. 
2. Wybierz **tożsamości zewnętrzne**.
3. Wybierz pozycję **Wszyscy dostawcy tożsamości**.
4. Wybierz dostawcę tożsamości, a następnie wybierz pozycję **Usuń**. 
5. Wybierz pozycję **tak** , aby potwierdzić usunięcie. 

Aby usunąć bezpośrednią Federacji z dostawcą tożsamości przy użyciu programu PowerShell:
1. Zainstaluj najnowszą wersję modułu PowerShell usługi Azure AD dla programu Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Uruchom następujące polecenie: 
   ```powershell
   Connect-AzureAD
   ```
3. W wierszu logowania zaloguj się przy użyciu zarządzanego konta administratora globalnego. 
4. Wprowadź następujące polecenie:
   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [środowisku wykupu zaproszeń](redemption-experience.md) , gdy użytkownicy zewnętrzni logują się przy użyciu różnych dostawców tożsamości.
