---
title: Atrybuty profilu użytkownika w Azure Active Directory B2C
description: Dowiedz się więcej o atrybutach typu zasobu użytkownika obsługiwanych przez profil użytkownika katalogu Azure AD B2C. Dowiedz się więcej na temat wbudowanych atrybutów, rozszerzeń i sposobu mapowania atrybutów na Microsoft Graph.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7dfad71d05a882e3a3941a96e12489adb5fb3234
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102500533"
---
# <a name="user-profile-attributes"></a>Atrybuty profilu użytkownika

Profil użytkownika katalogu usługi Azure Active Directory (Azure AD) B2C zawiera wbudowany zestaw atrybutów, takich jak imię i nazwisko, nazwisko, miasto, kod pocztowy i numer telefonu. Profil użytkownika można rozłożyć na własne dane aplikacji bez konieczności korzystania z zewnętrznego magazynu danych. 

Większość atrybutów, które mogą być używane z Azure AD B2C profile użytkowników, jest również obsługiwanych przez Microsoft Graph. W tym artykule opisano obsługiwane Azure AD B2C atrybuty profilu użytkownika. Są w nim również opisane atrybuty, które nie są obsługiwane przez Microsoft Graph, a także atrybuty Microsoft Graph, które nie powinny być używane z Azure AD B2C.

> [!IMPORTANT]
> Nie należy używać atrybutów wbudowanych ani rozszerzeń do przechowywania poufnych danych osobowych, takich jak poświadczenia konta, numery identyfikacyjne instytucji rządowych, dane dotyczące posiadaczy kart, dane konta finansowego, informacje o opiece zdrowotnej lub poufne informacje w tle.

Można także zintegrować z systemami zewnętrznymi. Na przykład można użyć Azure AD B2C do uwierzytelniania, ale delegować do zewnętrznego źródła zarządzania relacjami z klientami (CRM) lub z usługi lojalnościowej klienta jako autorytatywne źródło danych klienta. Aby uzyskać więcej informacji, zobacz Rozwiązanie [profilu zdalnego](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile) .

## <a name="azure-ad-user-resource-type"></a>Typ zasobu użytkownika usługi Azure AD

W poniższej tabeli wymieniono atrybuty [typu zasobu użytkownika](/graph/api/resources/user) , które są obsługiwane przez profil użytkownika katalogu Azure AD B2C. Podaje następujące informacje dotyczące każdego atrybutu:

- Nazwa atrybutu używana przez Azure AD B2C (po którym następuje nazwa Microsoft Graph w nawiasach, jeśli różni się)
- Typ danych atrybutu
- Opis atrybutu
- Jeśli atrybut jest dostępny w Azure Portal
- Jeśli atrybut może być używany w przepływie użytkownika
- Jeśli atrybut może być używany w ramach zasad niestandardowych [profilu usługi Azure AD](active-directory-technical-profile.md) i w którym sekcji ( &lt; InputClaims &gt; , &lt; OutputClaims &gt; lub &lt; PersistedClaims &gt; )

|Nazwa     |Typ     |Opis|Azure Portal|Przepływy użytkowników|Zasady niestandardowe|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |Wartość logiczna|Niezależnie od tego, czy konto użytkownika jest włączone, czy wyłączone: **prawda** , jeśli konto jest włączone, w przeciwnym razie **false**.|Tak|Nie|Utrwalony, wyjściowy|
|grupa_wiekowa        |Ciąg|Grupa wiekowa użytkownika. Możliwe wartości: null, undefined, moll, dorosły, NotAdult.|Tak|Nie|Utrwalony, wyjściowy|
|alternativeSecurityId ([tożsamości](#identities-attribute))|Ciąg|Tożsamość pojedynczego użytkownika od zewnętrznego dostawcy tożsamości.|Nie|Nie|Dane wejściowe, utrwalone i wyjściowe|
|alternativeSecurityIds ([tożsamości](#identities-attribute))|Alternatywna kolekcja securityId|Kolekcja tożsamości użytkowników od zewnętrznych dostawców tożsamości.|Nie|Nie|Utrwalony, wyjściowy|
|city            |Ciąg|Miasto, w którym znajduje się użytkownik. Maksymalna długość 128.|Tak|Tak|Utrwalony, wyjściowy|
|consentProvidedForMinor|Ciąg|Czy podano zgodę dla elementu pomocniczego. Dozwolone wartości: null, udzielono, odmowy lub notRequired.|Tak|Nie|Utrwalony, wyjściowy|
|country         |Ciąg|Kraj/region, w którym znajduje się użytkownik. Przykład: "US" lub "UK". Maksymalna długość 128.|Tak|Tak|Utrwalony, wyjściowy|
|createdDateTime|DateTime|Data utworzenia obiektu użytkownika. Tylko do odczytu.|Nie|Nie|Utrwalony, wyjściowy|
|Jeżeli    |Ciąg|Jeśli konto użytkownika zostało utworzone jako konto lokalne dla dzierżawy Azure Active Directory B2C, wartość to LocalAccount lub nameCoexistence. Tylko do odczytu.|Nie|Nie|Utrwalony, wyjściowy|
|dateOfBirth     |Date (Data)|Data urodzenia.|Nie|Nie|Utrwalony, wyjściowy|
|działu,      |Ciąg|Nazwa działu, w którym pracuje użytkownik. Maksymalna długość 64.|Tak|Nie|Utrwalony, wyjściowy|
|displayName     |Ciąg|Nazwa wyświetlana użytkownika. Maksymalna długość 256.|Tak|Tak|Utrwalony, wyjściowy|
|facsimileTelephoneNumber<sup>1</sup>|Ciąg|Numer telefonu służbowego komputera faksowego użytkownika.|Tak|Nie|Utrwalony, wyjściowy|
|givenName       |Ciąg|Imię i nazwisko (imię) użytkownika. Maksymalna długość 64.|Tak|Tak|Utrwalony, wyjściowy|
|jobTitle        |Ciąg|Stanowisko użytkownika. Maksymalna długość 128.|Tak|Tak|Utrwalony, wyjściowy|
|immutableId     |Ciąg|Identyfikator, który jest zazwyczaj używany w przypadku użytkowników migrowanych z Active Directory lokalnych.|Nie|Nie|Utrwalony, wyjściowy|
|legalAgeGroupClassification|Ciąg|Klasyfikacja grupy wiek prawny. Tylko do odczytu i obliczone na podstawie właściwości grupy wiekowej i consentProvidedForMinor. Dozwolone wartości: null, minorWithOutParentalConsent, minorWithParentalConsent, minorNoParentalConsentRequired, notAdult i Dorosła.|Tak|Nie|Utrwalony, wyjściowy|
|legalCountry<sup>1</sup>  |Ciąg|Kraj/region do celów prawnych.|Nie|Nie|Utrwalony, wyjściowy|
|mail (poczta)            |Ciąg|Adres SMTP użytkownika, na przykład " bob@contoso.com ". Tylko do odczytu.|Nie|Nie|Utrwalony, wyjściowy|
|mailNickName    |Ciąg|Alias poczty dla użytkownika. Maksymalna długość 64.|Nie|Nie|Utrwalony, wyjściowy|
|Mobile (mobilePhone) |Ciąg|Podstawowy numer telefonu komórkowego użytkownika. Maksymalna długość 64.|Tak|Nie|Utrwalony, wyjściowy|
|netId           |Ciąg|Identyfikator sieci.|Nie|Nie|Utrwalony, wyjściowy|
|objectId        |Ciąg|Unikatowy identyfikator globalny (GUID), który jest unikatowym identyfikatorem dla użytkownika. Przykład: 12345678-9ABC-DEF0-1234-56789abcde. Tylko do odczytu, niezmienny.|Tylko do odczytu|Tak|Dane wejściowe, utrwalone i wyjściowe|
|otherMails      |Kolekcja ciągów|Lista innych adresów e-mail użytkownika. Przykład: [" bob@contoso.com ", " Robert@fabrikam.com "].|Tak (alternatywny adres e-mail)|Nie|Utrwalony, wyjściowy|
|hasło        |Ciąg|Hasło konta lokalnego podczas tworzenia użytkownika.|Nie|Nie|Trwały|
|passwordPolicies     |Ciąg|Zasady dotyczące hasła. Jest to ciąg składający się z różnych nazw zasad oddzielonych przecinkami. Na przykład "DisablePasswordExpiration, DisableStrongPassword".|Nie|Nie|Utrwalony, wyjściowy|
|physicalDeliveryOfficeName (officeLocation)|Ciąg|Lokalizacja biura w miejscu pracy użytkownika. Maksymalna długość 128.|Tak|Nie|Utrwalony, wyjściowy|
|postalCode      |Ciąg|Kod pocztowy dla adresu pocztowego użytkownika. Kod pocztowy jest specyficzny dla kraju/regionu użytkownika. W Stany Zjednoczone w Ameryce ten atrybut zawiera kod pocztowy. Maksymalna długość 40.|Tak|Nie|Utrwalony, wyjściowy|
|preferredLanguage    |Ciąg|Preferowany język użytkownika. Preferowany format języka jest oparty na dokumencie RFC 4646. Nazwa jest kombinacją kodu ISO 639 2 literą małymi literami związanymi z językiem i litery ISO 3166 2 z wielką literą połączoną z krajem lub regionem. Przykład: "en-US" lub "es-ES".|Nie|Nie|Utrwalony, wyjściowy|
|refreshTokensValidFromDateTime (signInSessionsValidFromDateTime)|DateTime|Wszystkie tokeny odświeżania wystawione przed tym czasem są nieprawidłowe, a w przypadku korzystania z nieprawidłowego tokenu odświeżania aplikacje będą otrzymywać błędy. W takim przypadku aplikacja będzie musiała uzyskać nowy token odświeżania, wysyłając żądanie do punktu końcowego autoryzacji. Tylko do odczytu.|Nie|Nie|Dane wyjściowe|
|signInNames ([tożsamości](#identities-attribute)) |Ciąg|Unikatowa nazwa logowania użytkownika konta lokalnego dowolnego typu w katalogu. Użyj tego atrybutu, aby uzyskać użytkownika z wartością logowania bez określenia typu konta lokalnego.|Nie|Nie|Dane wejściowe|
|signInNames. userName ([tożsamości](#identities-attribute)) |Ciąg|Unikatowa nazwa użytkownika konta lokalnego w katalogu. Użyj tego atrybutu, aby utworzyć lub pobrać użytkownika z określoną nazwą użytkownika logowania. Określenie tej opcji w PersistedClaims samej podczas operacji patch spowoduje usunięcie innych typów signInNames. Jeśli chcesz dodać nowy typ signInNames, musisz również zachować istniejące signInNames.|Nie|Nie|Dane wejściowe, utrwalone i wyjściowe|
|signInNames. numer telefonu ([tożsamości](#identities-attribute)) |Ciąg|Unikatowy numer telefonu użytkownika konta lokalnego w katalogu. Użyj tego atrybutu, aby utworzyć lub pobrać użytkownika z określonym numerem telefonu logowania. Określenie tego atrybutu w PersistedClaims samo podczas operacji patch spowoduje usunięcie innych typów signInNames. Jeśli chcesz dodać nowy typ signInNames, musisz również zachować istniejące signInNames.|Nie|Nie|Dane wejściowe, utrwalone i wyjściowe|
|signInNames. emailAddress ([tożsamości](#identities-attribute))|Ciąg|Unikatowy adres e-mail użytkownika konta lokalnego w katalogu. Użyj tego elementu, aby utworzyć lub uzyskać użytkownika z określonym adresem e-mail logowania. Określenie tego atrybutu w PersistedClaims samo podczas operacji patch spowoduje usunięcie innych typów signInNames. Jeśli chcesz dodać nowy typ signInNames, musisz również zachować istniejące signInNames.|Nie|Nie|Dane wejściowe, utrwalone i wyjściowe|
|stan           |Ciąg|Województwo w adresie użytkownika. Maksymalna długość 128.|Tak|Tak|Utrwalony, wyjściowy|
|streetAddress   |Ciąg|Ulica siedziby firmy użytkownika. Maksymalna długość 1024.|Tak|Tak|Utrwalony, wyjściowy|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|Ciąg|Dodatkowy numer telefonu użytkownika używany do uwierzytelniania wieloskładnikowego.|Tak|Nie|Utrwalony, wyjściowy|
|strongAuthenticationEmailAddress<sup>1</sup>|Ciąg|Adres SMTP użytkownika. Przykład: " bob@contoso.com " ten atrybut jest używany do logowania przy użyciu zasad username, aby przechowywać adres e-mail użytkownika. Adres e-mail używany w ramach przepływu resetowania hasła.|Tak|Nie|Utrwalony, wyjściowy|
|strongAuthenticationPhoneNumber<sup>2</sup>|Ciąg|Podstawowy numer telefonu użytkownika używany do uwierzytelniania wieloskładnikowego.|Tak|Nie|Utrwalony, wyjściowy|
|surname         |Ciąg|Nazwisko użytkownika (Nazwa rodziny lub nazwisko). Maksymalna długość 64.|Tak|Tak|Utrwalony, wyjściowy|
|numer telefonu (pierwszy wpis businessPhones)|Ciąg|Podstawowy numer telefonu miejsca pracy użytkownika.|Tak|Nie|Utrwalony, wyjściowy|
|userPrincipalName    |Ciąg|Główna nazwa użytkownika (UPN). Nazwa UPN jest nazwą logowania użytkownika w stylu internetowym opartą na standardzie internetowym RFC 822. Domena musi być obecna w kolekcji zweryfikowanych domen dzierżawcy. Ta właściwość jest wymagana podczas tworzenia konta. Niezmienne.|Nie|Nie|Dane wejściowe, utrwalone i wyjściowe|
|usageLocation   |Ciąg|Wymagane dla użytkowników, którzy będą przypisani do licencji z powodu wymogu prawnego do sprawdzenia dostępności usług w krajach/regionach. Nie dopuszcza wartości null. Dwuliterowy kod kraju/regionu (ISO standard 3166). Przykłady: "US", "JP" i "GB".|Tak|Nie|Utrwalony, wyjściowy|
|userType        |Ciąg|Wartość ciągu, która może służyć do klasyfikowania typów użytkowników w katalogu. Wartość musi być elementem członkowskim. Tylko do odczytu.|Tylko do odczytu|Nie|Utrwalony, wyjściowy|
|userState (externalUserState)<sup>3</sup>|Ciąg|Tylko w przypadku konta usługi Azure AD B2B wskazuje, czy zaproszenie jest PendingAcceptance czy zaakceptowane.|Nie|Nie|Utrwalony, wyjściowy|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|DateTime|Pokazuje sygnaturę czasową dla najnowszej zmiany właściwości UserState.|Nie|Nie|Utrwalony, wyjściowy|

<sup>1 </sup> Nieobsługiwane przez Microsoft Graph<br><sup>2 </sup> Aby uzyskać więcej informacji, zobacz [atrybut numeru telefonu MFA](#mfa-phone-number-attribute)<br><sup>3 </sup> Nie powinien być używany z Azure AD B2C

## <a name="display-name-attribute"></a>Atrybut nazwy wyświetlanej

`displayName`Jest to nazwa, która ma być wyświetlana w Azure Portal zarządzanie użytkownikami dla użytkownika, a w tokenie dostępu Azure AD B2C powraca do aplikacji. Ta właściwość jest wymagana.

## <a name="identities-attribute"></a>Atrybut tożsamości

Konto klienta, które może być odbiorcą, partnerem lub obywatelem, może być skojarzone z tymi typami tożsamości:

- **Lokalna** tożsamość — nazwa użytkownika i hasło są przechowywane lokalnie w katalogu Azure AD B2C. Często odwołują się do tych tożsamości jako "konta lokalne".
- Tożsamość **federacyjna** — znana także jako konta *społecznościowe* *lub firmowe* , tożsamość użytkownika jest zarządzana przez federacyjnego dostawcę tożsamości, takiego jak Facebook, Microsoft, ADFS lub Salesforce.

Użytkownik z kontem klienta może zalogować się przy użyciu wielu tożsamości. Na przykład nazwa użytkownika, adres e-mail, identyfikator pracownika, identyfikator instytucji rządowych i inne. Pojedyncze konto może mieć wiele tożsamości, zarówno lokalnych, jak i społecznościowych, przy użyciu tego samego hasła. 

W interfejsie API Microsoft Graph zarówno tożsamość lokalna, jak i federacyjna są przechowywane w `identities` atrybucie użytkownika, który jest typu [objectIdentity](/graph/api/resources/objectidentity). `identities`Kolekcja reprezentuje zestaw tożsamości używany do logowania się do konta użytkownika. Ta kolekcja umożliwia użytkownikowi zalogowanie się do konta użytkownika przy użyciu dowolnej skojarzonej tożsamości. Atrybut tożsamości może zawierać maksymalnie dziesięć obiektów [objectIdentity](/graph/api/resources/objectidentity) . Każdy obiekt zawiera następujące właściwości:

| Nazwa   | Typ |Opis|
|:---------------|:--------|:----------|
|signInType|ciąg| Określa typy logowania użytkownika w katalogu. Dla konta lokalnego:  `emailAddress` , `emailAddress1` , `emailAddress2` , `emailAddress3`  `userName` lub dowolnego innego typu. Konto społecznościowe musi być ustawione na wartość  `federated` .|
|issuer|ciąg|Określa wystawcę tożsamości. W przypadku kont lokalnych (  gdzie nie signInType `federated` ) ta właściwość jest domyślną nazwą domeny dzierżawy lokalnej usługi B2C, na przykład `contoso.onmicrosoft.com` . W przypadku tożsamości społecznościowej (gdzie **signInType** is  `federated` ) wartością jest nazwa wystawcy, na przykład `facebook.com`|
|issuerAssignedId|ciąg|Określa unikatowy identyfikator przypisany do użytkownika przez wystawcę. Kombinacja **wystawców** i **issuerAssignedId** musi być unikatowa w ramach dzierżawy. W przypadku konta lokalnego, gdy **signInType** jest ustawiona na `emailAddress` lub `userName` , reprezentuje nazwę logowania dla użytkownika.<br>Gdy **signInType** jest ustawiona na: <ul><li>`emailAddress` (lub zaczyna się od `emailAddress` podobnej do `emailAddress1` ) **issuerAssignedId** musi być prawidłowym adresem e-mail</li><li>`userName` (lub jakakolwiek inna wartość) **issuerAssignedId** musi być prawidłową [lokalną częścią adresu e-mail](https://tools.ietf.org/html/rfc3696#section-3)</li><li>`federated`, **issuerAssignedId** reprezentuje unikatowy identyfikator konta federacyjnego</li></ul>|

Następujący atrybut **tożsamości** z tożsamością konta lokalnego z nazwą logowania, adresem e-mail jako logowaniem i tożsamością społecznościową. 

 ```json
 "identities": [
     {
       "signInType": "userName",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "johnsmith"
     },
     {
       "signInType": "emailAddress",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "jsmith@yahoo.com"
     },
     {
       "signInType": "federated",
       "issuer": "facebook.com",
       "issuerAssignedId": "5eecb0cd"
     }
   ]
 ```

W przypadku tożsamości federacyjnych, w zależności od dostawcy tożsamości, **issuerAssignedId** jest unikatową wartością dla danego użytkownika dla aplikacji lub konta deweloperskiego. Skonfiguruj zasady Azure AD B2C przy użyciu tego samego identyfikatora aplikacji, który został wcześniej przypisany przez dostawcę usług społecznościowych lub inną aplikację w ramach tego samego konta deweloperskiego. 

## <a name="password-profile-property"></a>Właściwość profilu hasła

Dla lokalnej tożsamości wymagany jest atrybut **passwordProfile** i zawiera on hasło użytkownika. Ten `forceChangePasswordNextSignIn` atrybut wskazuje, czy użytkownik musi zresetować hasło przy następnym logowaniu. Aby obsłużyć wymuszone Resetowanie hasła, [Skonfiguruj wymuszony przepływ resetowania hasła](force-password-reset.md).

W przypadku tożsamości federacyjnej (społecznej) atrybut **passwordProfile** nie jest wymagany.

```json
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

## <a name="password-policy-attribute"></a>Atrybut zasad haseł

Zasady haseł Azure AD B2C (dla kont lokalnych) są oparte na zasadach Azure Active Directory [silnych haseł](../active-directory/authentication/concept-sspr-policy.md) . Zasady rejestrowania i logowania Azure AD B2C i resetowania hasła wymagają silnego siły hasła i nie wygasają hasła.

W scenariuszach migracji użytkowników, jeśli konta, które mają zostać poddane migracji, mają słabsze siły hasła niż [silne siły hasła](../active-directory/authentication/concept-sspr-policy.md) wymuszone przez Azure AD B2C można wyłączyć wymaganie silnego hasła. Aby zmienić domyślne zasady haseł, należy ustawić `passwordPolicies` atrybut na `DisableStrongPassword` . Na przykład można zmodyfikować polecenie Utwórz żądanie użytkownika w następujący sposób:

```json
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="mfa-phone-number-attribute"></a>Atrybut numeru telefonu MFA

W przypadku korzystania z telefonu do uwierzytelniania wieloskładnikowego (MFA) telefon komórkowy służy do weryfikowania tożsamości użytkownika. Aby [dodać](/graph/api/authentication-post-phonemethods) nowy numer telefonu programowo, [zaktualizować](/graph/api/b2cauthenticationmethodspolicy-update), [pobrać](/graph/api/b2cauthenticationmethodspolicy-get)lub [usunąć](/graph/api/phoneauthenticationmethod-delete) numer telefonu, użyj [metody uwierzytelniania](/graph/api/resources/phoneauthenticationmethod)MS interfejs API programu Graph Phone.

W Azure AD B2C [zasad niestandardowych](custom-policy-overview.md)numer telefonu jest dostępny za pomocą `strongAuthenticationPhoneNumber` typu zgłoszenia.

## <a name="extension-attributes"></a>Atrybuty rozszerzenia

Każda aplikacja dołączona do klienta ma unikatowe wymagania dotyczące zbieranych informacji. Dzierżawca Azure AD B2C zawiera wbudowany zestaw informacji przechowywanych we właściwościach, takich jak imię i nazwisko oraz kod pocztowy. Za pomocą Azure AD B2C można rozłożyć zestaw właściwości przechowywanych na poszczególnych kontach klientów. Aby uzyskać więcej informacji, zobacz [Dodawanie atrybutów użytkownika i dostosowywanie danych wejściowych użytkownika w Azure Active Directory B2C](configure-user-input.md)

Atrybuty rozszerzenia [rozszerzają schemat](/graph/extensibility-overview#schema-extensions) obiektów użytkownika w katalogu. Atrybuty rozszerzenia mogą być rejestrowane tylko w obiekcie aplikacji, nawet jeśli mogą zawierać dane dla użytkownika. Atrybut rozszerzenia jest dołączony do aplikacji o nazwie `b2c-extensions-app` . Nie należy modyfikować tej aplikacji, ponieważ jest ona używana przez Azure AD B2C do przechowywania danych użytkownika. Tę aplikację można znaleźć w obszarze Azure Active Directory Rejestracje aplikacji.

> [!NOTE]
> - Do 100 atrybutów rozszerzeń można zapisywać na dowolnym koncie użytkownika.
> - Jeśli aplikacja B2C-Extensions-App zostanie usunięta, te atrybuty rozszerzenia zostaną usunięte ze wszystkich użytkowników wraz z wszelkimi zawartymi w nich danymi.
> - Jeśli atrybut rozszerzenia zostanie usunięty przez aplikację, zostanie on usunięty z wszystkich kont użytkowników i wartości są usuwane.

Atrybuty rozszerzenia w interfejs API programu Graph są nazwane przy użyciu konwencji, w `extension_ApplicationClientID_AttributeName` której `ApplicationClientID` jest **Identyfikator aplikacji (klienta)** `b2c-extensions-app` aplikacji (znajdujący się w **rejestracje aplikacji**  >  **wszystkie aplikacje** w Azure Portal). Należy zauważyć, że **Identyfikator aplikacji (klienta)** , która jest reprezentowana w nazwie atrybutu rozszerzenia, nie zawiera łączników. Na przykład:

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

Podczas definiowania atrybutu w rozszerzeniu schematu obsługiwane są następujące typy danych:

|Typ |Uwagi  |
|--------------|---------|
|Wartość logiczna    | Możliwe wartości: **true** lub **false**. |
|DateTime   | Musi być określony w formacie ISO 8601. Będą przechowywane w formacie UTC.   |
|Liczba całkowita    | 32 — wartość bitowa.               |
|Ciąg     | maksymalnie 256 znaków.     |

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat atrybutów rozszerzenia:

- [Rozszerzenia schematu](/graph/extensibility-overview#schema-extensions)
- [Definiowanie atrybutów niestandardowych](user-flow-custom-attributes.md)
