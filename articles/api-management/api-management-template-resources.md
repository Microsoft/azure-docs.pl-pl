---
title: Zasoby szablonów API Management platformy Azure | Microsoft Docs
description: Dowiedz się więcej o typach zasobów dostępnych do użycia w szablonach portalu dla deweloperów w usłudze Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 51a1b4c6-a9fd-4524-9e0e-03a9800c3e94
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: c3b6123c63bf530463379a175745ef86baf2c5a3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84689991"
---
# <a name="azure-api-management-template-resources"></a>Zasoby szablonów API Management platformy Azure
Usługa Azure API Management udostępnia następujące typy zasobów do użycia w szablonach portalu dla deweloperów.  
  
-   [Zasoby ciągów](#strings)  
  
-   [Zasoby symboli](#glyphs)  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
##  <a name="string-resources"></a><a name="strings"></a> Zasoby ciągów  
 API Management zapewnia kompleksowy zestaw zasobów ciągów do użycia w portalu dla deweloperów. Te zasoby są zlokalizowane we wszystkich językach obsługiwanych przez API Management. Domyślny zestaw szablonów używa tych zasobów dla nagłówków stron, etykiet i wszystkich stałych ciągów, które są wyświetlane w portalu dla deweloperów. Aby użyć zasobu ciągu w szablonach, podaj prefiks ciągu zasobu, a po nim nazwę ciągu, jak pokazano w poniższym przykładzie.  
  
```  
{% localized "Prefix|Name" %}  
  
```  
  
 Poniższy przykład pochodzi z szablonu Lista produktów i zawiera **produkty** w górnej części strony.  
  
```  
<h2>{% localized "ProductsStrings|PageTitleProducts" %}</h2>  
  
```  
  
Obsługiwane są następujące opcje lokalizacji:

| Regionalne    | Język               |
|-----------|------------------------|
| „pl”      | Anglia              |
| Rejestr      | "Čeština"              |
| Ukryj      | Deutsch              |
| AK      | "Español"              |
| fr      | Français             |
| Węgry      | Magyar               |
| go      | Italiano             |
| "ja-JP"   | "日本語"                |
| Ko      | "한국어"                |
| NL      | Nederlands           |
| zysków      | Polski               |
| "pt-br"   | "Português (Brazylia)"   |
| "pt-pt"   | "Português (Portugalia)" |
| ru      | "Русский"              |
| OHR      | Svenska              |
| zdawczy      | "Türkçe"               |
| "zh-Hans" | "中文(简体)"           |
| "zh-Hant" | "中文(繁體)"           |

 W poniższych tabelach zawarto informacje dotyczące zasobów ciągów dostępnych do użycia w szablonach portalu dla deweloperów. Użyj nazwy tabeli jako prefiksu dla zasobów ciągów w tej tabeli.  
  
-   [ApisStrings](#ApisStrings)  
  
-   [ApplicationListStrings](#ApplicationListStrings)  
  
-   [AppDetailsStrings](#AppDetailsStrings)  
  
-   [AppStrings](#AppStrings)  
  
-   [CommonResources](#CommonResources)  
  
-   [CommonStrings](#CommonStrings)  
  
-   [Dokumentacja](#Documentation)  
  
-   [ErrorPageStrings](#ErrorPageStrings)  
  
-   [IssuesStrings](#IssuesStrings)  
  
-   [NotFoundStrings](#NotFoundStrings)  
  
-   [ProductDetailsStrings](#ProductDetailsStrings)  
  
-   [ProductsStrings](#ProductsStrings)  
  
-   [ProviderInfoStrings](#ProviderInfoStrings)  
  
-   [SigninResources](#SigninResources)  
  
-   [SigninStrings](#SigninStrings)  
  
-   [SignupStrings](#SignupStrings)  
  
-   [SubscriptionListStrings](#SubscriptionListStrings)  
  
-   [SubscriptionStrings](#SubscriptionStrings)  
  
-   [UpdateProfileStrings](#UpdateProfileStrings)  
  
-   [UserProfile](#UserProfile)  
  
###  <a name="apisstrings"></a><a name="ApisStrings"></a> ApisStrings  
  
|Nazwa|Tekst|  
|----------|----------|  
|PageTitleApis|Interfejsy API|  
  
###  <a name="appdetailsstrings"></a><a name="AppDetailsStrings"></a> AppDetailsStrings  
  
|Nazwa|Tekst|  
|----------|----------|  
|WebApplicationsDetailsTitle|Podgląd aplikacji|  
|WebApplicationsRequirementsHeader|Wymagania|  
|WebApplicationsScreenshotAlt|Zrzut ekranu|  
|WebApplicationsScreenshotsHeader|Zrzuty ekranu|  
  
###  <a name="applicationliststrings"></a><a name="ApplicationListStrings"></a> ApplicationListStrings  
  
|Nazwa|Tekst|  
|----------|----------|  
|WebDevelopersAppDeleteConfirmation|Czy na pewno chcesz usunąć aplikację?|  
|WebDevelopersAppNotPublished|Nie opublikowano|  
|WebDevelopersAppNotSubmitted|Nieprzesłane|  
|WebDevelopersAppTableCategoryHeader|Kategoria|  
|WebDevelopersAppTableNameHeader|Nazwa|  
|WebDevelopersAppTableStateHeader|Stan|  
|WebDevelopersEditLink|Edytuj|  
|WebDevelopersRegisterAppLink|Rejestrowanie aplikacji|  
|WebDevelopersRemoveLink|Usuń|  
|WebDevelopersSubmitLink|Prześlij|  
|WebDevelopersYourApplicationsHeader|Twoje aplikacje|  
  
###  <a name="appstrings"></a><a name="AppStrings"></a> AppStrings  
  
|Nazwa|Tekst|  
|----------|----------|  
|WebApplicationsHeader|Aplikacje|  
  
###  <a name="commonresources"></a><a name="CommonResources"></a> CommonResources  
  
|Nazwa|Tekst|  
|----------|----------|  
|NoItemsToDisplay|Nie znaleziono wyników.|  
|GeneralExceptionMessage|Coś nie jest prawidłowe. Może to być błąd tymczasowy lub usterka. Spróbuj ponownie.|  
|GeneralJsonExceptionMessage|Coś nie jest prawidłowe. Może to być błąd tymczasowy lub usterka. Załaduj ponownie stronę i spróbuj ponownie.|  
|ConfirmationMessageUnsavedChanges|Istnieją niezapisane zmiany. Czy na pewno chcesz anulować i odrzucić zmiany?|  
|Usługi azureactivedirectory|Azure Active Directory|  
|HttpLargeRequestMessage|Treść żądania HTTP jest za duża.|  
  
###  <a name="commonstrings"></a><a name="CommonStrings"></a> CommonStrings  
  
|Nazwa|Tekst|  
|----------|----------|  
|ButtonLabelCancel|Anuluj|  
|ButtonLabelSave|Zapisz|  
|GeneralExceptionMessage|Coś nie jest prawidłowe. Może to być błąd tymczasowy lub usterka. Spróbuj ponownie.|  
|NoItemsToDisplay|Brak elementów do wyświetlenia.|  
|PagerButtonLabelFirst|Pierwsze|  
|PagerButtonLabelLast|Ostatnie|  
|PagerButtonLabelNext|Następne|  
|PagerButtonLabelPrevious|Przeglądan|  
|PagerLabelPageNOfM|Strona {0} z {1}|  
|PasswordTooShort|Hasło jest za krótkie|  
|EmailAsPassword|Nie używaj swojego adresu e-mail jako hasła|  
|PasswordSameAsUserName|Hasło nie może zawierać Twojej nazwy użytkownika|  
|PasswordTwoCharacterClasses|Użyj innych klas znaków|  
|PasswordTooManyRepetitions|Zbyt wiele powtórzeń|  
|PasswordSequenceFound|Hasło zawiera sekwencje|  
|PagerLabelPageSize|Rozmiar strony|  
|CurtainLabelLoading|Trwa ładowanie...|  
|TablePlaceholderNothingToDisplay|Brak danych dla wybranego okresu i zakresu|  
|ButtonLabelClose|Zamknij|  
  
###  <a name="documentation"></a><a name="Documentation"></a> Łączoną  
  
|Nazwa|Tekst|  
|----------|----------|  
|WebDocumentationInvalidHeaderErrorMessage|Nieprawidłowy nagłówek " {0} "|  
|WebDocumentationInvalidRequestErrorMessage|Nieprawidłowy adres URL żądania|  
|TextboxLabelAccessToken|Token dostępu *|  
|DropdownOptionPrimaryKeyFormat|Głównym{0}|  
|DropdownOptionSecondaryKeyFormat|Dodatkowych{0}|  
|WebDocumentationSubscriptionKeyText|Klucz subskrypcji|  
|WebDocumentationTemplatesAddHeaders|Dodaj wymagane nagłówki HTTP|  
|WebDocumentationTemplatesBasicAuthSample|Przykład autoryzacji podstawowej|  
|WebDocumentationTemplatesCurlForBasicAuth|w przypadku uwierzytelniania podstawowego:--User {username}: {Password}|  
|WebDocumentationTemplatesCurlValuesForPath|Określ wartości dla parametrów ścieżki (pokazanych jako {...}), klucz subskrypcji i wartości parametrów zapytania|  
|WebDocumentationTemplatesDeveloperKey|Określ klucz subskrypcji|  
|WebDocumentationTemplatesJavaApache|Ten przykład korzysta z klienta Apache HTTP ze składników HTTP (http://hc.apache.org/httpcomponents-client-ga/)|  
|WebDocumentationTemplatesOptionalParams|W razie potrzeby określ wartości dla parametrów opcjonalnych|  
|WebDocumentationTemplatesPhpPackage|Ten przykład używa pakietu HTTP_Request2. (Aby uzyskać więcej informacji: https://pear.php.net/package/HTTP_Request2)|  
|WebDocumentationTemplatesPythonValuesForPath|W razie potrzeby określ wartości dla parametrów ścieżki (pokazane jako {...}) i treść żądania|  
|WebDocumentationTemplatesRequestBody|Określ treść żądania|  
|WebDocumentationTemplatesRequiredParams|Określ wartości dla następujących wymaganych parametrów|  
|WebDocumentationTemplatesValuesForPath|Określ wartości dla parametrów ścieżki (pokazanych jako {...})|  
|OAuth2AuthorizationEndpointDescription|Punkt końcowy autoryzacji jest używany do współdziałania z właścicielem zasobu i uzyskiwania uprawnień autoryzacji.|  
|OAuth2AuthorizationEndpointName|Punkt końcowy autoryzacji|  
|OAuth2TokenEndpointDescription|Punkt końcowy tokenu jest używany przez klienta do uzyskiwania tokenu dostępu przez przedprezentowanie jego autoryzacji lub tokenu odświeżenia.|  
|OAuth2TokenEndpointName|Punkt końcowy tokenu|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Description|<p \>         klient inicjuje przepływ, kierując do punktu końcowego autoryzacji właściciela zasobu.  Klient zawiera identyfikator klienta, żądany zakres, stan lokalny i identyfikator URI przekierowania, do którego serwer autoryzacji wyśle agenta użytkownika po udzieleniu dostępu (lub odmowie).     </p \>     <p \>         serwer autoryzacji uwierzytelnia właściciela zasobu (za pośrednictwem agenta użytkownika) i ustali, czy właściciel zasobu przyznaje lub nie odmówi żądania dostępu klienta.     </p \>     <p \>         założenie, że właściciel zasobu przyznaje dostęp, serwer autoryzacji przekierowuje agenta użytkownika z powrotem do klienta przy użyciu podanego wcześniej identyfikatora URI przekierowania (w żądaniu lub podczas rejestracji klienta).  Identyfikator URI przekierowania zawiera kod autoryzacji i wszystkie stany lokalne dostarczone przez klienta wcześniej.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ErrorDescription|<p \>     , jeśli użytkownik odrzuca żądanie dostępu, jeśli żądanie jest nieprawidłowe, klient zostanie poinformowany za pomocą następujących parametrów dodanych do przekierowania: </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_Name|Żądanie autoryzacji|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_RequestDescription|<p \>         aplikacja kliencka musi wysłać użytkownika do punktu końcowego autoryzacji w celu zainicjowania procesu uwierzytelniania OAuth.          W punkcie końcowym autoryzacji użytkownik jest uwierzytelniany, a następnie udziela lub nie odmawia dostępu do aplikacji.     </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_AuthorizationRequest_ResponseDescription|<p \>     założenie, że właściciel zasobu przyznaje dostęp, serwer autoryzacji przekierowuje agenta użytkownika z powrotem do klienta przy użyciu podanego wcześniej identyfikatora URI przekierowania (w żądaniu lub podczas rejestracji klienta).  Identyfikator URI przekierowania zawiera kod autoryzacji i wszystkie stany lokalne dostarczone przez klienta wcześniej. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_Description|<p \>  klient żąda tokenu dostępu od punktu końcowego tokenu serwera autoryzacji, dołączając kod autoryzacji otrzymany w poprzednim kroku.  Podczas wykonywania żądania klient jest uwierzytelniany przy użyciu serwera autoryzacji.  Klient zawiera identyfikator URI przekierowania używany do uzyskania kodu autoryzacji w celu weryfikacji. </p \> <p \>     serwer autoryzacji uwierzytelnia klienta, weryfikuje kod autoryzacji i zapewnia, że otrzymany identyfikator URI przekierowania jest zgodny z identyfikatorem URI używanym do przekierowania klienta w kroku (C).  Jeśli jest prawidłowy, serwer autoryzacji reaguje ponownie z tokenem dostępu i, opcjonalnie, tokenem odświeżania. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ErrorDescription|<p \>     Jeśli uwierzytelnienie klienta nie powiodło się lub jest nieprawidłowe, serwer autoryzacji reaguje na kod stanu HTTP 400 (zły żądanie) (chyba że określono inaczej) i zawiera następujące parametry z odpowiedzią. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_RequestDescription|<p \>   klient wysyła żądanie do punktu końcowego tokenu przez wysłanie następujących parametrów przy użyciu formatu "application/x-www-form-urlencoded" z kodowaniem znaków UTF-8 w treści żądania HTTP. </p\>|  
|OAuth2Flow_AuthorizationCodeGrant_Step_TokenRequest_ResponseDescription|<p \>  serwer autoryzacji wystawia token dostępu i opcjonalny token odświeżania i konstruuje odpowiedź poprzez dodanie następujących parametrów do treści jednostki odpowiedzi HTTP z kodem stanu 200 (ok). </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_Description|<p \>  klient uwierzytelnia się przy użyciu serwera autoryzacji i żąda tokenu dostępu od punktu końcowego tokenu. </p \> <p \>  serwer autoryzacji uwierzytelnia klienta, a jeśli jest prawidłowy, wystawia token dostępu. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ErrorDescription|<p \>     , jeśli żądanie uwierzytelnienia klienta nie powiodło się lub jest nieprawidłowy, serwer autoryzacji reaguje na kod stanu HTTP 400 (zły żądanie) (chyba że określono inaczej) i zawiera następujące parametry z odpowiedzią. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_RequestDescription|<p \>   klient wysyła żądanie do punktu końcowego tokenu przez dodanie następujących parametrów przy użyciu formatu "application/x-www-form-urlencoded" z kodowaniem znaków UTF-8 w treści żądania HTTP. </p\>|  
|OAuth2Flow_ClientCredentialsGrant_Step_TokenRequest_ResponseDescription|<p \>  Jeśli żądanie tokenu dostępu jest prawidłowe i autoryzowane, serwer autoryzacji wystawia token dostępu i opcjonalny token odświeżania i konstruuje odpowiedź, dodając następujące parametry do treści jednostki odpowiedzi HTTP z kodem stanu 200 (ok). </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_Description|<p \>   klient inicjuje przepływ, kierując do punktu końcowego autoryzacji właściciela zasobu "User-Agent".  Klient zawiera identyfikator klienta, żądany zakres, stan lokalny i identyfikator URI przekierowania, do którego serwer autoryzacji wyśle agenta użytkownika po udzieleniu dostępu (lub odmowie). </p \> <p \>        serwer autoryzacji uwierzytelnia właściciela zasobu (za pośrednictwem agenta użytkownika) i ustali, czy właściciel zasobu przyznaje lub nie odmówi żądania dostępu klienta. </p \> <p \>        założenie, że właściciel zasobu przyznaje dostęp, serwer autoryzacji przekierowuje agenta użytkownika z powrotem do klienta przy użyciu podanego wcześniej identyfikatora URI przekierowania.  Identyfikator URI przekierowania zawiera token dostępu do fragmentu identyfikatora URI. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ErrorDescription|<p, \>     Jeśli właściciel zasobu odmówił żądania dostępu lub jeśli żądanie nie powiedzie się z powodu braku lub nieprawidłowego identyfikatora URI przekierowania, serwer autoryzacji informuje klienta, dodając następujące parametry do składnika fragmentu identyfikatora URI przekierowania przy użyciu formatu "application/x-www-form-urlencoded". </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_RequestDescription|<p \>     aplikacja kliencka musi wysłać użytkownika do punktu końcowego autoryzacji w celu zainicjowania procesu uwierzytelniania OAuth.      W punkcie końcowym autoryzacji użytkownik jest uwierzytelniany, a następnie udziela lub nie odmawia dostępu do aplikacji. </p\>|  
|OAuth2Flow_ImplicitGrant_Step_AuthorizationRequest_ResponseDescription|<p \>     , jeśli właściciel zasobu przyznaje żądanie dostępu, serwer autoryzacji wystawia token dostępu i dostarcza go do klienta, dodając następujące parametry do składnika fragmentu identyfikatora URI przekierowania przy użyciu formatu "application/x-www-form-urlencoded". </p\>|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Description|Przepływ kodu autoryzacji jest zoptymalizowany pod kątem klientów, którzy mogą utrzymywać poufność poświadczeń (np. aplikacje serwera sieci Web zaimplementowane przy użyciu języka PHP, Java, Python, Ruby, ASP.NET itp.).|  
|OAuth2Flow_ObtainAuthorization_AuthorizationCodeGrant_Name|Przyznanie kodu autoryzacji|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Description|Przepływ poświadczeń klienta jest odpowiedni w przypadkach, gdy klient (aplikacja) żąda dostępu do chronionych zasobów w ramach jego kontroli. Klient jest traktowany jako właściciel zasobu, więc nie jest wymagane interakcja z użytkownikiem końcowym.|  
|OAuth2Flow_ObtainAuthorization_ClientCredentialsGrant_Name|Przyznanie poświadczeń klienta|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Description|Niejawny przepływ jest zoptymalizowany pod kątem klientów, którzy nie mogą utrzymywać poufności poświadczeń znanych do obsługi określonego identyfikatora URI przekierowania. Ci klienci są zwykle zaimplementowani w przeglądarce przy użyciu języka skryptowego, takiego jak JavaScript.|  
|OAuth2Flow_ObtainAuthorization_ImplicitGrant_Name|Niejawne przyznanie|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Description|Przepływ poświadczeń hasła właściciela zasobu jest odpowiedni w przypadkach, gdy właściciel zasobu ma relację zaufania z klientem (aplikacją), taką jak system operacyjny urządzenia lub wysoce uprzywilejowana aplikacja. Ten przepływ jest odpowiedni dla klientów z możliwością uzyskania poświadczeń właściciela zasobu (nazwy użytkownika i hasła, zazwyczaj przy użyciu formularza interaktywnego).|  
|OAuth2Flow_ObtainAuthorization_ResourceOwnerPasswordCredentialsGrant_Name|Przyznanie poświadczeń hasła właściciela zasobu|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_Description|<p \>   właściciel zasobu dostarcza klientowi nazwę użytkownika i hasło. </p \> <p \> klient żąda tokenu dostępu od punktu końcowego tokenu serwera autoryzacji, dołączając poświadczenia otrzymane od właściciela zasobu.  Podczas wykonywania żądania klient jest uwierzytelniany przy użyciu serwera autoryzacji. </p \> <p \>     serwer autoryzacji uwierzytelnia klienta i sprawdza poprawność poświadczeń właściciela zasobu, a jeśli jest prawidłowy, wystawia token dostępu. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ErrorDescription|<p \>  , jeśli żądanie uwierzytelnienia klienta nie powiodło się lub jest nieprawidłowy, serwer autoryzacji reaguje na kod stanu HTTP 400 (zły żądanie) (chyba że określono inaczej) i zawiera następujące parametry z odpowiedzią. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_RequestDescription|<p \>    klient wysyła żądanie do punktu końcowego tokenu przez dodanie następujących parametrów przy użyciu formatu "application/x-www-form-urlencoded" z kodowaniem znaków UTF-8 w treści żądania HTTP. </p\>|  
|OAuth2Flow_ResourceOwnerPasswordCredentialsGrant_Step_TokenRequest_ResponseDescription|<p \>   Jeśli żądanie tokenu dostępu jest prawidłowe i autoryzowane, serwer autoryzacji wystawia token dostępu i opcjonalny token odświeżania i konstruuje odpowiedź, dodając następujące parametry do treści jednostki odpowiedzi HTTP z kodem stanu 200 (ok). </p\>|  
|OAuth2Step_AccessTokenRequest_Name|Żądanie tokenu dostępu|  
|OAuth2Step_AuthorizationRequest_Name|Żądanie autoryzacji|  
|OAuth2AccessToken_AuthorizationCodeGrant_TokenResponse|Wymagane. Token dostępu wystawiony przez serwer autoryzacji.|  
|OAuth2AccessToken_ClientCredentialsGrant_TokenResponse|Wymagane. Token dostępu wystawiony przez serwer autoryzacji.|  
|OAuth2AccessToken_ImplicitGrant_AuthorizationResponse|Wymagane. Token dostępu wystawiony przez serwer autoryzacji.|  
|OAuth2AccessToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Wymagane. Token dostępu wystawiony przez serwer autoryzacji.|  
|OAuth2ClientId_AuthorizationCodeGrant_AuthorizationRequest|Wymagane. Identyfikator klienta.|  
|OAuth2ClientId_AuthorizationCodeGrant_TokenRequest|WYMAGANE, jeśli klient nie jest uwierzytelniany na serwerze autoryzacji.|  
|OAuth2ClientId_ImplicitGrant_AuthorizationRequest|Wymagane. Identyfikator klienta.|  
|OAuth2Code_AuthorizationCodeGrant_AuthorizationResponse|Wymagane. Kod autoryzacji generowany przez serwer autoryzacji.|  
|OAuth2Code_AuthorizationCodeGrant_TokenRequest|Wymagane. Kod autoryzacji otrzymany z serwera autoryzacji.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_AuthorizationErrorResponse|Obowiązkowe. Czytelny dla człowieka tekst ASCII zawierający dodatkowe informacje.|  
|OAuth2ErrorDescription_AuthorizationCodeGrant_TokenErrorResponse|Obowiązkowe. Czytelny dla człowieka tekst ASCII zawierający dodatkowe informacje.|  
|OAuth2ErrorDescription_ClientCredentialsGrant_TokenErrorResponse|Obowiązkowe. Czytelny dla człowieka tekst ASCII zawierający dodatkowe informacje.|  
|OAuth2ErrorDescription_ImplicitGrant_AuthorizationErrorResponse|Obowiązkowe. Czytelny dla człowieka tekst ASCII zawierający dodatkowe informacje.|  
|OAuth2ErrorDescription_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Obowiązkowe. Czytelny dla człowieka tekst ASCII zawierający dodatkowe informacje.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_AuthorizationErrorResponse|Obowiązkowe. Identyfikator URI identyfikujący stronę sieci Web czytelną dla człowieka z informacjami o błędzie.|  
|OAuth2ErrorUri_AuthorizationCodeGrant_TokenErrorResponse|Obowiązkowe. Identyfikator URI identyfikujący stronę sieci Web czytelną dla człowieka z informacjami o błędzie.|  
|OAuth2ErrorUri_ClientCredentialsGrant_TokenErrorResponse|Obowiązkowe. Identyfikator URI identyfikujący stronę sieci Web czytelną dla człowieka z informacjami o błędzie.|  
|OAuth2ErrorUri_ImplicitGrant_AuthorizationErrorResponse|Obowiązkowe. Identyfikator URI identyfikujący stronę sieci Web czytelną dla człowieka z informacjami o błędzie.|  
|OAuth2ErrorUri_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Obowiązkowe. Identyfikator URI identyfikujący stronę sieci Web czytelną dla człowieka z informacjami o błędzie.|  
|OAuth2Error_AuthorizationCodeGrant_AuthorizationErrorResponse|Wymagane. Pojedynczy kod błędu ASCII z następujących: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_AuthorizationCodeGrant_TokenErrorResponse|Wymagane. Pojedynczy kod błędu ASCII z następujących: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ClientCredentialsGrant_TokenErrorResponse|Wymagane. Pojedynczy kod błędu ASCII z następujących: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2Error_ImplicitGrant_AuthorizationErrorResponse|Wymagane. Pojedynczy kod błędu ASCII z następujących: invalid_request, unauthorized_client, access_denied, unsupported_response_type, invalid_scope, server_error, temporarily_unavailable.|  
|OAuth2Error_ResourceOwnerPasswordCredentialsGrant_TokenErrorResponse|Wymagane. Pojedynczy kod błędu ASCII z następujących: invalid_request, invalid_client, invalid_grant, unauthorized_client, unsupported_grant_type, invalid_scope.|  
|OAuth2ExpiresIn_AuthorizationCodeGrant_TokenResponse|Rekomendowane. Okres istnienia w sekundach tokenu dostępu.|  
|OAuth2ExpiresIn_ClientCredentialsGrant_TokenResponse|Rekomendowane. Okres istnienia w sekundach tokenu dostępu.|  
|OAuth2ExpiresIn_ImplicitGrant_AuthorizationResponse|Rekomendowane. Okres istnienia w sekundach tokenu dostępu.|  
|OAuth2ExpiresIn_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Rekomendowane. Okres istnienia w sekundach tokenu dostępu.|  
|OAuth2GrantType_AuthorizationCodeGrant_TokenRequest|Wymagane. Wartość musi być równa "authorization_code".|  
|OAuth2GrantType_ClientCredentialsGrant_TokenRequest|Wymagane. Wartość musi być równa "client_credentials".|  
|OAuth2GrantType_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Wymagane. Wartość musi być równa "Password" (hasło).|  
|OAuth2Password_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Wymagane. Hasło właściciela zasobu.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_AuthorizationRequest|Obowiązkowe. Identyfikator URI punktu końcowego przekierowania musi być bezwzględnym identyfikatorem URI.|  
|OAuth2RedirectUri_AuthorizationCodeGrant_TokenRequest|WYMAGANE, jeśli parametr "redirect_uri" został uwzględniony w żądaniu autoryzacji i ich wartości muszą być identyczne.|  
|OAuth2RedirectUri_ImplicitGrant_AuthorizationRequest|Obowiązkowe. Identyfikator URI punktu końcowego przekierowania musi być bezwzględnym identyfikatorem URI.|  
|OAuth2RefreshToken_AuthorizationCodeGrant_TokenResponse|Obowiązkowe. Token odświeżania, który może służyć do uzyskiwania nowych tokenów dostępu.|  
|OAuth2RefreshToken_ClientCredentialsGrant_TokenResponse|Obowiązkowe. Token odświeżania, który może służyć do uzyskiwania nowych tokenów dostępu.|  
|OAuth2RefreshToken_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Obowiązkowe. Token odświeżania, który może służyć do uzyskiwania nowych tokenów dostępu.|  
|OAuth2ResponseType_AuthorizationCodeGrant_AuthorizationRequest|Wymagane. Wartość musi być równa "Code".|  
|OAuth2ResponseType_ImplicitGrant_AuthorizationRequest|Wymagane. Wartość musi być równa "token".|  
|OAuth2Scope_AuthorizationCodeGrant_AuthorizationRequest|Obowiązkowe. Zakres żądania dostępu.|  
|OAuth2Scope_AuthorizationCodeGrant_TokenResponse|OPCJONALNE, jeśli są identyczne z zakresem żądanym przez klienta; w przeciwnym razie jest to wymagane.|  
|OAuth2Scope_ClientCredentialsGrant_TokenRequest|Obowiązkowe. Zakres żądania dostępu.|  
|OAuth2Scope_ClientCredentialsGrant_TokenResponse|OPCJONALNE, jeśli taka sama jak zakres żądany przez klienta; w przeciwnym razie jest to wymagane.|  
|OAuth2Scope_ImplicitGrant_AuthorizationRequest|Obowiązkowe. Zakres żądania dostępu.|  
|OAuth2Scope_ImplicitGrant_AuthorizationResponse|OPCJONALNE, jeśli są identyczne z zakresem żądanym przez klienta; w przeciwnym razie jest to wymagane.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Obowiązkowe. Zakres żądania dostępu.|  
|OAuth2Scope_ResourceOwnerPasswordCredentialsGrant_TokenResponse|OPCJONALNE, jeśli taka sama jak zakres żądany przez klienta; w przeciwnym razie jest to wymagane.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationErrorResponse|WYMAGANE, jeśli parametr "State" był obecny w żądaniu autoryzacji klienta.  Dokładna wartość odebrana od klienta.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationRequest|Rekomendowane. Wartość nieprzezroczysta używana przez klienta do utrzymania stanu między żądaniem a wywołaniem zwrotnym.  Serwer autoryzacji zawiera tę wartość podczas przekierowywania agenta użytkownika do klienta programu.  Parametr powinien być używany do zapobiegania fałszerstwu żądań między lokacjami.|  
|OAuth2State_AuthorizationCodeGrant_AuthorizationResponse|WYMAGANE, jeśli parametr "State" był obecny w żądaniu autoryzacji klienta.  Dokładna wartość odebrana od klienta.|  
|OAuth2State_ImplicitGrant_AuthorizationErrorResponse|WYMAGANE, jeśli parametr "State" był obecny w żądaniu autoryzacji klienta.  Dokładna wartość odebrana od klienta.|  
|OAuth2State_ImplicitGrant_AuthorizationRequest|Rekomendowane. Wartość nieprzezroczysta używana przez klienta do utrzymania stanu między żądaniem a wywołaniem zwrotnym.  Serwer autoryzacji zawiera tę wartość podczas przekierowywania agenta użytkownika do klienta programu.  Parametr powinien być używany do zapobiegania fałszerstwu żądań między lokacjami.|  
|OAuth2State_ImplicitGrant_AuthorizationResponse|WYMAGANE, jeśli parametr "State" był obecny w żądaniu autoryzacji klienta.  Dokładna wartość odebrana od klienta.|  
|OAuth2TokenType_AuthorizationCodeGrant_TokenResponse|Wymagane. Typ wystawionego tokenu.|  
|OAuth2TokenType_ClientCredentialsGrant_TokenResponse|Wymagane. Typ wystawionego tokenu.|  
|OAuth2TokenType_ImplicitGrant_AuthorizationResponse|Wymagane. Typ wystawionego tokenu.|  
|OAuth2TokenType_ResourceOwnerPasswordCredentialsGrant_TokenResponse|Wymagane. Typ wystawionego tokenu.|  
|OAuth2UserName_ResourceOwnerPasswordCredentialsGrant_TokenRequest|Wymagane. Nazwa użytkownika właściciela zasobu.|  
|OAuth2UnsupportedTokenType|Typ tokenu " {0} " nie jest obsługiwany.|  
|OAuth2InvalidState|Nieprawidłowa odpowiedź z serwera autoryzacji|  
|OAuth2GrantType_AuthorizationCode|Kod autoryzacji|  
|OAuth2GrantType_Implicit|Niejawna|  
|OAuth2GrantType_ClientCredentials|Poświadczenia klienta|  
|OAuth2GrantType_ResourceOwnerPassword|Hasło właściciela zasobu|  
|WebDocumentation302Code|302 znaleziono|  
|WebDocumentation400Code|400 (Nieprawidłowe żądanie)|  
|OAuth2SendingMethod_AuthHeader|Nagłówek autoryzacji|  
|OAuth2SendingMethod_QueryParam|Parametr zapytania|  
|OAuth2AuthorizationServerGeneralException|Wystąpił błąd podczas autoryzowania dostępu za pośrednictwem {0}|  
|OAuth2AuthorizationServerCommunicationException|Nie można nawiązać połączenia HTTP z serwerem autoryzacji lub został on nieoczekiwanie zamknięty.|  
|WebDocumentationOAuth2GeneralErrorMessage|Wystąpił nieoczekiwany błąd.|  
|AuthorizationServerCommunicationException|Wystąpił wyjątek komunikacji z serwerem autoryzacji. Skontaktuj się z administratorem.|  
|TextblockSubscriptionKeyHeaderDescription|Klucz subskrypcji, który zapewnia dostęp do tego interfejsu API. Znaleziono w <profilu href = "/Developer" \></a \> .|  
|TextblockOAuthHeaderDescription|Token dostępu OAuth 2,0 otrzymany z <i \> {0}</i \> . Obsługiwane typy dotacji: <i \> {1}</i \> .|  
|TextblockContentTypeHeaderDescription|Typ nośnika treści wysyłanej do interfejsu API.|  
|ErrorMessageApiNotAccessible|Interfejs API, który próbujesz wywołać, jest w tej chwili niedostępny. Skontaktuj się z wydawcą interfejsu API <a href = "/Issues" \> tutaj</a \> .|  
|ErrorMessageApiTimedout|Interfejs API, który próbujesz wywołać, trwa dłużej niż zwykle, aby można było odzyskać odpowiedź. Skontaktuj się z wydawcą interfejsu API <a href = "/Issues" \> tutaj</a \> .|  
|BadRequestParameterExpected|{0}oczekiwano parametru "" "|  
|TooltipTextDoubleClickToSelectAll|Kliknij dwukrotnie, aby zaznaczyć wszystkie.|  
|TooltipTextHideRevealSecret|Pokaż/Ukryj|  
|ButtonLinkOpenConsole|Wypróbuj|  
|SectionHeadingRequestBody|Treść żądania|  
|SectionHeadingRequestParameters|Parametry żądania|  
|SectionHeadingRequestUrl|Adres URL żądania|  
|SectionHeadingResponse|Reakcja|  
|SectionHeadingRequestHeaders|Nagłówki żądań|  
|FormLabelSubtextOptional|optional|  
|SectionHeadingCodeSamples|Przykłady kodu|  
|TextblockOpenidConnectHeaderDescription|Token identyfikatora połączenia OpenID Connect uzyskany z <i \> {0}</i \> . Obsługiwane typy dotacji: <i \> {1}</i \> .|  
  
###  <a name="errorpagestrings"></a><a name="ErrorPageStrings"></a> ErrorPageStrings  
  
|Nazwa|Tekst|  
|----------|----------|  
|LinkLabelBack|wstecz|  
|LinkLabelHomePage|Strona główna|  
|LinkLabelSendUsEmail|Wyślij do nas wiadomość e-mail|  
|PageTitleError|Niestety, wystąpił problem z zażądaną stroną|  
|TextblockPotentialCauseIntermittentIssue|Może to być sporadyczny problem z dostępem do danych, który już nie został usunięty.|  
|TextblockPotentialCauseOldLink|Kliknięty link może być już stary i nie wskazuje na poprawną lokalizację.|  
|TextblockPotentialCauseTechnicalProblem|Na naszym końcu może wystąpić problem techniczny.|  
|TextblockPotentialSolutionRefresh|Spróbuj odświeżyć stronę.|  
|TextblockPotentialSolutionStartOver|Zacznij od firmy Microsoft {0} .|  
|TextblockPotentialSolutionTryAgain|Przejdź {0} i spróbuj ponownie wykonać akcję.|  
|TextReportProblem|{0} Zapoznaj się z błędami i poinformujemy o tym, jak tylko będzie to możliwe.|  
|TitlePotentialCause|Potencjalna Przyczyna|  
|TitlePotentialSolution|Prawdopodobnie tylko tymczasowy problem — kilka rzeczy do wypróbowania|  
  
###  <a name="issuesstrings"></a><a name="IssuesStrings"></a> IssuesStrings  
  
|Nazwa|Tekst|  
|----------|----------|  
|WebIssuesIndexTitle|Problemy|  
|WebIssuesNoActiveSubscriptions|Nie masz aktywnych subskrypcji. Musisz subskrybować produkt, aby zgłosić problem.|  
|WebIssuesNotSignin|Użytkownik nie jest zalogowany. {0}Aby zgłosić problem lub opublikować komentarz.|  
|WebIssuesReportIssueButton|Zgłoś problem|  
|WebIssuesSignIn|logowanie|  
|WebIssuesStatusReportedBy|Stan: {0} &#124; zgłoszone przez {1}|  
  
###  <a name="notfoundstrings"></a><a name="NotFoundStrings"></a> NotFoundStrings  
  
|Nazwa|Tekst|  
|----------|----------|  
|LinkLabelHomePage|Strona główna|  
|LinkLabelSendUsEmail|Wyślij do nas wiadomość e-mail|  
|PageTitleNotFound|Niestety, nie można odnaleźć strony, której szukasz|  
|TextblockPotentialCauseMisspelledUrl|Jeśli wpisano ten adres URL, być może został on wpisany nieprawidłowo.|  
|TextblockPotentialCauseOldLink|Kliknięty link może być już stary i nie wskazuje na poprawną lokalizację.|  
|TextblockPotentialSolutionRetype|Spróbuj ponownie wpisać adres URL.|  
|TextblockPotentialSolutionStartOver|Zacznij od firmy Microsoft {0} .|  
|TextReportProblem|{0} Zapoznaj się z błędami i poinformujemy o tym, jak tylko będzie to możliwe.|  
|TitlePotentialCause|Potencjalna Przyczyna|  
|TitlePotentialSolution|Potencjalne rozwiązanie|  
  
###  <a name="productdetailsstrings"></a><a name="ProductDetailsStrings"></a> ProductDetailsStrings  
  
|Nazwa|Tekst|  
|----------|----------|  
|WebProductsAgreement|Subskrybując {0} produkt, zgadzam się na `<a data-toggle='modal' href='#legal-terms'\>Terms of Use</a\>` .|  
|WebProductsLegalTermsLink|Warunki użytkowania|  
|WebProductsSubscribeButton|Subskrybuj|  
|WebProductsUsageLimitsHeader|Limity użycia|  
|WebProductsYouAreNotSubscribed|Subskrybujesz ten produkt.|  
|WebProductsYouRequestedSubscription|Zażądano subskrypcji tego produktu.|  
|ErrorYouNeedToAgreeWithLegalTerms|Przed kontynuowaniem należy wyrazić zgodę na warunki użytkowania.|  
|ButtonLabelAddSubscription|Dodawanie subskrypcji|  
|LinkLabelChangeSubscriptionName|zmienianie|  
|ButtonLabelConfirm|Confirm|  
|TextblockMultipleSubscriptionsCount|Masz {0} subskrypcje na ten produkt:|  
|TextblockSingleSubscriptionsCount|Masz {0} subskrypcję tego produktu:|  
|TextblockSingleApisCount|Ten produkt zawiera {0} interfejs API:|  
|TextblockMultipleApisCount|Ten produkt zawiera {0} interfejsy API:|  
|TextblockHeaderSubscribe|Subskrybowanie produktu|  
|TextblockSubscriptionDescription|Zostanie utworzona nowa subskrypcja:|  
|TextblockSubscriptionLimitReached|Osiągnięto limit subskrypcji.|  
  
###  <a name="productsstrings"></a><a name="ProductsStrings"></a> ProductsStrings  
  
|Nazwa|Tekst|  
|----------|----------|  
|PageTitleProducts|Produkty|  
  
###  <a name="providerinfostrings"></a><a name="ProviderInfoStrings"></a> ProviderInfoStrings  
  
|Nazwa|Tekst|  
|----------|----------|  
|TextboxExternalIdentitiesDisabled|Logowanie jest w tej chwili wyłączone przez administratorów.|  
|TextboxExternalIdentitiesSigninInvitation|Alternatywnie Zaloguj się przy użyciu|  
|TextboxExternalIdentitiesSigninInvitationPrimary|Zaloguj się przy użyciu:|  
  
###  <a name="signinresources"></a><a name="SigninResources"></a> SigninResources  
  
|Nazwa|Tekst|  
|----------|----------|  
|PrincipalNotFound|Nie znaleziono podmiotu zabezpieczeń lub podpis jest nieprawidłowy|  
|ErrorSsoAuthenticationFailed|Uwierzytelnianie SSO nie powiodło się|  
|ErrorSsoAuthenticationFailedDetailed|Podano nieprawidłowy token lub nie można zweryfikować podpisu.|  
|ErrorSsoTokenInvalid|Token logowania jednokrotnego jest nieprawidłowy|  
|ValidationErrorSpecificEmailAlreadyExists|Wiadomość e-mail " {0} " została już zarejestrowana|  
|ValidationErrorSpecificEmailInvalid|Adres e-mail " {0} " jest nieprawidłowy|  
|ValidationErrorPasswordInvalid|Hasło jest nieprawidłowe. Popraw błędy i spróbuj ponownie.|  
|PropertyTooShort|{0} jest za krótki|  
|WebAuthenticationAddresserEmailInvalidErrorMessage|Nieprawidłowy adres e-mail.|  
|ValidationMessageNewPasswordConfirmationRequired|Potwierdź nowe hasło|  
|ValidationErrorPasswordConfirmationRequired|Potwierdzenie hasła jest puste|  
|WebAuthenticationEmailChangeNotice|Wiadomość e-mail z potwierdzeniem zmiany jest w sposób do {0} . Aby potwierdzić swój nowy adres e-mail, postępuj zgodnie z instrukcjami w tym obszarze. Jeśli wiadomość e-mail nie dotarła do skrzynki odbiorczej w ciągu najbliższych kilku minut, sprawdź folder wiadomości-śmieci.|  
|WebAuthenticationEmailChangeNoticeHeader|Żądanie zmiany adresu e-mail zostało pomyślnie przetworzone|  
|WebAuthenticationEmailChangeNoticeTitle|Żądanie zmiany adresu e-mail|  
|WebAuthenticationEmailHasBeenRevertedNotice|Adres e-mail już istnieje. Żądanie zostało cofnięte|  
|ValidationErrorEmailAlreadyExists|Adres e-mail już istnieje|  
|ValidationErrorEmailInvalid|Nieprawidłowy adres e-mail|  
|TextboxLabelEmail|E-mail|  
|ValidationErrorEmailRequired|Adres e-mail jest wymagany.|  
|WebAuthenticationErrorNoticeHeader|Błąd|  
|WebAuthenticationFieldLengthErrorMessage|{0} musi mieć maksymalną długość równą {1}|  
|TextboxLabelEmailFirstName|Imię|  
|ValidationErrorFirstNameRequired|Imię jest wymagane.|  
|ValidationErrorFirstNameInvalid|Nieprawidłowa imię|  
|NoticeInvalidInvitationToken|Należy pamiętać, że linki potwierdzające są prawidłowe tylko przez 48 godzin. Jeśli nadal znajduje się w tym przedziale czasowym, upewnij się, że link jest prawidłowy. Jeśli link wygasł, powtórz akcję, którą próbujesz potwierdzić.|  
|NoticeHeaderInvalidInvitationToken|Nieprawidłowy token zaproszenia|  
|NoticeTitleInvalidInvitationToken|Błąd potwierdzenia|  
|WebAuthenticationLastNameInvalidErrorMessage|Nieprawidłowa nazwisko|  
|TextboxLabelEmailLastName|Nazwisko|  
|ValidationErrorLastNameRequired|Nazwisko jest wymagane.|  
|WebAuthenticationLinkExpiredNotice|Link do potwierdzenia został wysłany do Ciebie. `<a href={0}?token={1}>Resend confirmation email.</a\>`|  
|NoticePasswordResetLinkInvalidOrExpired|Łącze do resetowania hasła jest nieprawidłowe lub wygasło.|  
|WebAuthenticationLinkExpiredNoticeTitle|Wysłano link|  
|WebAuthenticationNewPasswordLabel|Nowe hasło|  
|ValidationMessageNewPasswordRequired|Nowe hasło jest wymagane.|  
|TextboxLabelNotificationsSenderEmail|Adres e-mail nadawcy powiadomień|  
|TextboxLabelOrganizationName|Nazwa organizacji|  
|WebAuthenticationOrganizationRequiredErrorMessage|Nazwa organizacji jest pusta|  
|WebAuthenticationPasswordChangedNotice|Twoje hasło zostało pomyślnie zaktualizowane|  
|WebAuthenticationPasswordChangedNoticeTitle|Hasło zostało zaktualizowane|  
|WebAuthenticationPasswordCompareErrorMessage|Hasła nie są zgodne|  
|WebAuthenticationPasswordConfirmLabel|Potwierdź hasło|  
|ValidationErrorPasswordInvalidDetailed|Hasło jest zbyt słabe.|  
|WebAuthenticationPasswordLabel|Hasło|  
|ValidationErrorPasswordRequired|Hasło jest wymagane.|  
|WebAuthenticationPasswordResetSendNotice|Zmiana hasła wiadomość e-mail z potwierdzeniem jest w sposób do {0} . Postępuj zgodnie z instrukcjami w wiadomości e-mail, aby kontynuować proces zmiany hasła.|  
|WebAuthenticationPasswordResetSendNoticeHeader|Żądanie zresetowania hasła zostało pomyślnie przetworzone|  
|WebAuthenticationPasswordResetSendNoticeTitle|Żądanie zresetowania hasła|  
|WebAuthenticationRequestNotFoundNotice|Nie znaleziono żądania|  
|WebAuthenticationSenderEmailRequiredErrorMessage|Adres e-mail nadawcy powiadomień jest pusty|  
|WebAuthenticationSigninPasswordLabel|Potwierdź zmianę, wprowadzając hasło|  
|WebAuthenticationSignupConfirmNotice|Wiadomość e-mail z potwierdzeniem rejestracji jest w drodze do {0} . <br/ \> postępuj zgodnie z instrukcjami w wiadomości e-mail, aby aktywować konto. <br/ \> Jeśli wiadomość e-mail nie dotarła do skrzynki odbiorczej w ciągu najbliższych kilku minut, sprawdź folder wiadomości-śmieci.|  
|WebAuthenticationSignupConfirmNoticeHeader|Twoje konto zostało pomyślnie utworzone|  
|WebAuthenticationSignupConfirmNoticeRepeatHeader|Wiadomość e-mail z potwierdzeniem rejestracji została wysłana ponownie|  
|WebAuthenticationSignupConfirmNoticeTitle|Utworzono konto|  
|WebAuthenticationTokenRequiredErrorMessage|Token jest pusty|  
|WebAuthenticationUserAlreadyRegisteredNotice|Wydaje się, że użytkownik z tą wiadomością e-mail jest już zarejestrowany w systemie. Jeśli nie pamiętasz hasła, spróbuj je przywrócić lub skontaktuj się z naszym zespołem pomocy technicznej.|  
|WebAuthenticationUserAlreadyRegisteredNoticeHeader|Użytkownik jest już zarejestrowany|  
|WebAuthenticationUserAlreadyRegisteredNoticeTitle|Już zarejestrowano|  
|ButtonLabelChangePassword|Zmień hasło|  
|ButtonLabelChangeAccountInfo|Zmień informacje o koncie|  
|ButtonLabelCloseAccount|Zamknij konto|  
|WebAuthenticationInvalidCaptchaErrorMessage|Wprowadzony tekst nie jest zgodny z tekstem na obrazku. Spróbuj ponownie.|  
|ValidationErrorCredentialsInvalid|Adres e-mail lub hasło są nieprawidłowe. Popraw błędy i spróbuj ponownie.|  
|WebAuthenticationRequestIsNotValid|Żądanie jest nieprawidłowe|  
|WebAuthenticationUserIsNotConfirm|Potwierdź rejestrację przed podjęciem próby zalogowania się.|  
|WebAuthenticationInvalidEmailFormated|Adres e-mail jest nieprawidłowy: {0}|  
|WebAuthenticationUserNotFound|Nie znaleziono użytkownika|  
|WebAuthenticationTenantNotRegistered|Twoje konto należy do dzierżawy Azure Active Directory, która nie ma autoryzacji do uzyskiwania dostępu do tego portalu.|  
|WebAuthenticationAuthenticationFailed|Uwierzytelnianie nie powiodło się.|  
|WebAuthenticationGooglePlusNotEnabled|Uwierzytelnianie nie powiodło się. Jeśli aplikacja została autoryzowana, skontaktuj się z administratorem, aby upewnić się, że uwierzytelnianie Google zostało prawidłowo skonfigurowane.|  
|ValidationErrorAllowedTenantIsRequired|Dozwolona dzierżawa jest wymagana|  
|ValidationErrorTenantIsNotValid|Dzierżawca Azure Active Directory " {0} " jest nieprawidłowy.|  
|WebAuthenticationActiveDirectoryTitle|Azure Active Directory|  
|WebAuthenticationLoginUsingYourProvider|Zaloguj się przy użyciu swojego {0} konta|  
|WebAuthenticationUserLimitNotice|Ta usługa osiągnęła maksymalną liczbę dozwolonych użytkowników. Wykonaj `<a href="mailto:{0}"\>contact the administrator</a\>` uaktualnienie swojej usługi i ponownie Włącz rejestrację użytkownika.|  
|WebAuthenticationUserLimitNoticeHeader|Rejestracja użytkownika została wyłączona|  
|WebAuthenticationUserLimitNoticeTitle|Rejestracja użytkownika została wyłączona|  
|WebAuthenticationUserRegistrationDisabledNotice|Rejestracja użytkowników została wyłączona przez administratora. Zaloguj się przy użyciu zewnętrznego dostawcy tożsamości.|  
|WebAuthenticationUserRegistrationDisabledNoticeHeader|Rejestracja użytkownika została wyłączona|  
|WebAuthenticationUserRegistrationDisabledNoticeTitle|Rejestracja użytkownika została wyłączona|  
|WebAuthenticationSignupPendingConfirmationNotice|Zanim będziemy mogli zakończyć tworzenie konta, musimy zweryfikować swój adres e-mail. Wysłaliśmy wiadomość e-mail na adres {0} . Aby aktywować konto, postępuj zgodnie z instrukcjami w wiadomości e-mail. Jeśli wiadomość e-mail nie dotarła w ciągu kilku następnych minut, sprawdź folder wiadomości-śmieci.|  
|WebAuthenticationSignupPendingConfirmationAccountFoundNotice|Znaleźliśmy niepotwierdzone konto dla adresu e-mail {0} . Aby ukończyć tworzenie konta, musimy zweryfikować swój adres e-mail. Wysłaliśmy wiadomość e-mail na adres {0} . Aby aktywować konto, postępuj zgodnie z instrukcjami w wiadomości e-mail. Jeśli wiadomość e-mail nie dotarła w ciągu kilku następnych minut, sprawdź folder wiadomości-śmieci.|  
|WebAuthenticationSignupConfirmationAlmostDone|Prawie gotowe|  
|WebAuthenticationSignupConfirmationEmailSent|Wysłaliśmy wiadomość e-mail na adres {0} . Aby aktywować konto, postępuj zgodnie z instrukcjami w wiadomości e-mail. Jeśli wiadomość e-mail nie dotarła w ciągu kilku następnych minut, sprawdź folder wiadomości-śmieci.|  
|WebAuthenticationEmailSentNotificationMessage|Wiadomość e-mail została pomyślnie wysłana do {0}|  
|WebAuthenticationNoAadTenantConfigured|Brak skonfigurowanej dzierżawy Azure Active Directory dla usługi.|  
|CheckboxLabelUserRegistrationTermsConsentRequired|Zgadzam się na `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use</a\>` .|  
|TextblockUserRegistrationTermsProvided|Przejrzyj `<a data-toggle="modal" href="#" data-target="#terms"\>Terms of Use.</a\>`|  
|DialogHeadingTermsOfUse|Warunki użytkowania|  
|ValidationMessageConsentNotAccepted|Przed kontynuowaniem należy wyrazić zgodę na warunki użytkowania.|  
  
###  <a name="signinstrings"></a><a name="SigninStrings"></a> SigninStrings  
  
|Nazwa|Tekst|  
|----------|----------|  
|WebAuthenticationForgotPassword|Forgot your password?|  
|WebAuthenticationIfAdministrator|Jeśli jesteś administratorem, musisz się zalogować `<a href="{0}"\>here</a\>` .|  
|WebAuthenticationNotAMember|Nie jesteś jeszcze członkiem? `<a href="/signup"\>Sign up now</a\>`|  
|WebAuthenticationRemember|Zapamiętaj mnie na tym komputerze|  
|WebAuthenticationSigininWithPassword|Zaloguj się przy użyciu nazwy użytkownika i hasła|  
|WebAuthenticationSigninTitle|Zaloguj|  
|WebAuthenticationSignUpNow|Utwórz konto teraz|  
  
###  <a name="signupstrings"></a><a name="SignupStrings"></a> SignupStrings  
  
|Nazwa|Tekst|  
|----------|----------|  
|PageTitleSignup|Rejestrowanie|  
|WebAuthenticationAlreadyAMember|Jesteś już członkiem?|  
|WebAuthenticationCreateNewAccount|Utwórz nowe konto API Management|  
|WebAuthenticationSigninNow|Zaloguj się teraz|  
|ButtonLabelSignup|Rejestrowanie|  
  
###  <a name="subscriptionliststrings"></a><a name="SubscriptionListStrings"></a> SubscriptionListStrings  
  
|Nazwa|Tekst|  
|----------|----------|  
|SubscriptionCancelConfirmation|Czy na pewno chcesz anulować tę subskrypcję?|  
|SubscriptionRenewConfirmation|Czy na pewno chcesz odnowić tę subskrypcję?|  
|WebDevelopersManageSubscriptions|Zarządzanie subskrypcjami|  
|WebDevelopersPrimaryKey|Klucz podstawowy|  
|WebDevelopersRegenerateLink|Regenerate|  
|WebDevelopersSecondaryKey|Klucz pomocniczy|  
|ButtonLabelShowKey|Pokaż|  
|ButtonLabelRenewSubscription|Renew|  
|WebDevelopersSubscriptionRequested|Zażądano {0}|  
|WebDevelopersSubscriptionRequestedState|Zażądano|  
|WebDevelopersSubscriptionTableNameHeader|Nazwa|  
|WebDevelopersSubscriptionTableStateHeader|Stan|  
|WebDevelopersUsageStatisticsLink|Raporty analityczne|  
|WebDevelopersYourSubscriptions|Twoje subskrypcje|  
|SubscriptionPropertyLabelRequestedDate|Zażądano|  
|SubscriptionPropertyLabelStartedDate|Data rozpoczęcia|  
|PageTitleRenameSubscription|Zmień nazwę subskrypcji|  
|SubscriptionPropertyLabelName|Nazwa subskrypcji|  
  
###  <a name="subscriptionstrings"></a><a name="SubscriptionStrings"></a> SubscriptionStrings  
  
|Nazwa|Tekst|  
|----------|----------|  
|SectionHeadingCloseAccount|Chcesz zamknąć konto?|  
|PageTitleDeveloperProfile|Profil|  
|ButtonLabelHideKey|Ukryj|  
|ButtonLabelRegenerateKey|Regenerate|  
|InformationMessageKeyWasRegenerated|Czy na pewno chcesz ponownie wygenerować ten klucz?|  
|ButtonLabelShowKey|Pokaż|  
  
###  <a name="updateprofilestrings"></a><a name="UpdateProfileStrings"></a> UpdateProfileStrings  
  
|Nazwa|Tekst|  
|----------|----------|  
|ButtonLabelUpdateProfile|Aktualizuj profil|  
|PageTitleUpdateProfile|Aktualizowanie informacji o koncie|  
  
###  <a name="userprofile"></a><a name="UserProfile"></a> UserProfile  
  
|Nazwa|Tekst|  
|----------|----------|  
|ButtonLabelChangeAccountInfo|Zmień informacje o koncie|  
|ButtonLabelChangePassword|Zmień hasło|  
|ButtonLabelCloseAccount|Zamknij konto|  
|TextboxLabelEmail|E-mail|  
|TextboxLabelEmailFirstName|Imię|  
|TextboxLabelEmailLastName|Nazwisko|  
|TextboxLabelNotificationsSenderEmail|Adres e-mail nadawcy powiadomień|  
|TextboxLabelOrganizationName|Nazwa organizacji|  
|SubscriptionStateActive|Aktywna|  
|SubscriptionStateCancelled|Anulowano|  
|SubscriptionStateExpired|Wygasłe|  
|SubscriptionStateRejected|Odrzucone|  
|SubscriptionStateRequested|Zażądano|  
|SubscriptionStateSuspended|Suspended|  
|DefaultSubscriptionNameTemplate|{0}  wartooć|  
|SubscriptionNameTemplate|Dostęp dla deweloperów #{0}|  
|TextboxLabelSubscriptionName|Nazwa subskrypcji|  
|ValidationMessageSubscriptionNameRequired|Nazwa subskrypcji nie może być pusta.|  
|ApiManagementUserLimitReached|Ta usługa osiągnęła maksymalną liczbę dozwolonych użytkowników. Przeprowadź uaktualnienie do wyższej warstwy cenowej.|  
  
##  <a name="glyph-resources"></a><a name="glyphs"></a> Zasoby symboli  
 Szablony portalu dla deweloperów API Management mogą używać glifów z [Glyphicons z Bootstrap](https://getbootstrap.com/components/#glyphicons). Ten zestaw symboli zawiera ponad 250 symboli w formacie czcionki z zestawu [Glyphicon](https://glyphicons.com/) Halflings. Aby użyć symbolu z tego zestawu, użyj następującej składni.  
  
```html  
<span class="glyphicon glyphicon-user">  
```  
  
 Aby uzyskać pełną listę symboli, zobacz [Glyphicons from Bootstrap](https://getbootstrap.com/components/#glyphicons).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji na temat pracy z szablonami, zobacz [How to dostosowywanie portalu deweloperów API Management przy użyciu szablonów](api-management-developer-portal-templates.md).
