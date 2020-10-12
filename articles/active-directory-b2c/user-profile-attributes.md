---
title: Atrybuty profilu użytkownika w Azure Active Directory B2C
description: Dowiedz się więcej o atrybutach typu zasobu użytkownika obsługiwanych przez profil użytkownika katalogu Azure AD B2C. Dowiedz się więcej na temat wbudowanych atrybutów, rozszerzeń i sposobu mapowania atrybutów na Microsoft Graph.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1e6965e15b7482935148ae7fcd2edf0f3cc722b2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83738561"
---
# <a name="user-profile-attributes"></a>Atrybuty profilu użytkownika

Profil użytkownika katalogu usługi Azure Active Directory (Azure AD) B2C zawiera wbudowany zestaw atrybutów, takich jak imię i nazwisko, nazwisko, miasto, kod pocztowy i numer telefonu. Profil użytkownika można rozłożyć na własne dane aplikacji bez konieczności korzystania z zewnętrznego magazynu danych. Większość atrybutów, które mogą być używane z Azure AD B2C profile użytkowników, jest również obsługiwanych przez Microsoft Graph. W tym artykule opisano obsługiwane Azure AD B2C atrybuty profilu użytkownika. Są w nim również opisane atrybuty, które nie są obsługiwane przez Microsoft Graph, a także atrybuty Microsoft Graph, które nie powinny być używane z Azure AD B2C.

> [!IMPORTANT]
> Nie należy używać atrybutów wbudowanych ani rozszerzeń do przechowywania poufnych danych osobowych, takich jak poświadczenia konta, numery identyfikacyjne instytucji rządowych, dane dotyczące posiadaczy kart, dane konta finansowego, informacje o opiece zdrowotnej lub poufne informacje w tle.

Można także zintegrować z systemami zewnętrznymi. Na przykład można użyć Azure AD B2C do uwierzytelniania, ale delegować do zewnętrznego źródła zarządzania relacjami z klientami (CRM) lub z usługi lojalnościowej klienta jako autorytatywne źródło danych klienta. Aby uzyskać więcej informacji, zobacz Rozwiązanie [profilu zdalnego](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile) .

W poniższej tabeli wymieniono atrybuty [typu zasobu użytkownika](https://docs.microsoft.com/graph/api/resources/user) , które są obsługiwane przez profil użytkownika katalogu Azure AD B2C. Podaje następujące informacje dotyczące każdego atrybutu:

- Nazwa atrybutu używana przez Azure AD B2C (po którym następuje nazwa Microsoft Graph w nawiasach, jeśli różni się)
- Typ danych atrybutu
- Opis atrybutu
- Jeśli atrybut jest dostępny w Azure Portal
- Jeśli atrybut może być używany w przepływie użytkownika
- Jeśli atrybut może być używany w ramach zasad niestandardowych [profilu usługi Azure AD](active-directory-technical-profile.md) i w którym sekcji ( &lt; InputClaims &gt; , &lt; OutputClaims &gt; lub &lt; PersistedClaims &gt; )

|Nazwa     |Typ     |Opis|Azure Portal|Przepływy użytkowników|Zasady niestandardowe|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |Boolean (wartość logiczna)|Niezależnie od tego, czy konto użytkownika jest włączone, czy wyłączone: **prawda** , jeśli konto jest włączone, w przeciwnym razie **false**.|Tak|Nie|Utrwalony, wyjściowy|
|grupa_wiekowa        |Ciąg|Grupa wiekowa użytkownika. Możliwe wartości: null, undefined, moll, dorosły, NotAdult.|Tak|Nie|Utrwalony, wyjściowy|
|alternativeSecurityId ([tożsamości](manage-user-accounts-graph-api.md#identities-property))|Ciąg|Tożsamość pojedynczego użytkownika od zewnętrznego dostawcy tożsamości.|Nie|Nie|Dane wejściowe, utrwalone i wyjściowe|
|alternativeSecurityIds ([tożsamości](manage-user-accounts-graph-api.md#identities-property))|Alternatywna kolekcja securityId|Kolekcja tożsamości użytkowników od zewnętrznych dostawców tożsamości.|Nie|Nie|Utrwalony, wyjściowy|
|city            |Ciąg|Miasto, w którym znajduje się użytkownik. Maksymalna długość 128.|Tak|Tak|Utrwalony, wyjściowy|
|consentProvidedForMinor|Ciąg|Czy podano zgodę dla elementu pomocniczego. Dozwolone wartości: null, udzielono, odmowy lub notRequired.|Tak|Nie|Utrwalony, wyjściowy|
|country         |Ciąg|Kraj/region, w którym znajduje się użytkownik. Przykład: "US" lub "UK". Maksymalna długość 128.|Tak|Tak|Utrwalony, wyjściowy|
|createdDateTime|DateTime|Data utworzenia obiektu użytkownika. Tylko do odczytu.|Nie|Nie|Utrwalony, wyjściowy|
|Jeżeli    |Ciąg|Jeśli konto użytkownika zostało utworzone jako konto lokalne dla dzierżawy Azure Active Directory B2C, wartość to LocalAccount lub nameCoexistence. Tylko do odczytu.|Nie|Nie|Utrwalony, wyjściowy|
|dateOfBirth     |Date|Data urodzenia.|Nie|Nie|Utrwalony, wyjściowy|
|działu,      |Ciąg|Nazwa działu, w którym pracuje użytkownik. Maksymalna długość 64.|Tak|Nie|Utrwalony, wyjściowy|
|displayName     |Ciąg|Nazwa wyświetlana użytkownika. Maksymalna długość 256.|Tak|Tak|Utrwalony, wyjściowy|
|facsimileTelephoneNumber<sup>1</sup>|Ciąg|Numer telefonu służbowego komputera faksowego użytkownika.|Tak|Nie|Utrwalony, wyjściowy|
|givenName       |Ciąg|Imię i nazwisko (imię) użytkownika. Maksymalna długość 64.|Tak|Tak|Utrwalony, wyjściowy|
|Stanowiska        |Ciąg|Stanowisko użytkownika. Maksymalna długość 128.|Tak|Tak|Utrwalony, wyjściowy|
|immutableId     |Ciąg|Identyfikator, który jest zazwyczaj używany w przypadku użytkowników migrowanych z Active Directory lokalnych.|Nie|Nie|Utrwalony, wyjściowy|
|legalAgeGroupClassification|Ciąg|Klasyfikacja grupy wiek prawny. Tylko do odczytu i obliczone na podstawie właściwości grupy wiekowej i consentProvidedForMinor. Dozwolone wartości: null, minorWithOutParentalConsent, minorWithParentalConsent, minorNoParentalConsentRequired, notAdult i Dorosła.|Tak|Nie|Utrwalony, wyjściowy|
|legalCountry<sup>1</sup>  |Ciąg|Kraj/region do celów prawnych.|Nie|Nie|Utrwalony, wyjściowy|
|mail (poczta)            |Ciąg|Adres SMTP użytkownika, na przykład " bob@contoso.com ". Tylko do odczytu.|Nie|Nie|Utrwalony, wyjściowy|
|mailNickName    |Ciąg|Alias poczty dla użytkownika. Maksymalna długość 64.|Nie|Nie|Utrwalony, wyjściowy|
|Mobile (mobilePhone) |Ciąg|Podstawowy numer telefonu komórkowego użytkownika. Maksymalna długość 64.|Tak|Nie|Utrwalony, wyjściowy|
|netId           |Ciąg|Identyfikator sieci.|Nie|Nie|Utrwalony, wyjściowy|
|Obiektu        |Ciąg|Unikatowy identyfikator globalny (GUID), który jest unikatowym identyfikatorem dla użytkownika. Przykład: 12345678-9ABC-DEF0-1234-56789abcde. Tylko do odczytu, niezmienny.|Tylko do odczytu|Tak|Dane wejściowe, utrwalone i wyjściowe|
|otherMails      |Kolekcja ciągów|Lista dodatkowych adresów e-mail dla użytkownika. Przykład: [" bob@contoso.com ", " Robert@fabrikam.com "].|Tak (alternatywny adres e-mail)|Nie|Utrwalony, wyjściowy|
|hasło        |Ciąg|Hasło konta lokalnego podczas tworzenia użytkownika.|Nie|Nie|Trwały|
|passwordPolicies     |Ciąg|Zasady dotyczące hasła. Jest to ciąg składający się z różnych nazw zasad oddzielonych przecinkami. tj. "DisablePasswordExpiration, DisableStrongPassword".|Nie|Nie|Utrwalony, wyjściowy|
|physicalDeliveryOfficeName (officeLocation)|Ciąg|Lokalizacja biura w miejscu pracy użytkownika. Maksymalna długość 128.|Tak|Nie|Utrwalony, wyjściowy|
|Pocztowy      |Ciąg|Kod pocztowy dla adresu pocztowego użytkownika. Kod pocztowy jest specyficzny dla kraju/regionu użytkownika. W Stany Zjednoczone w Ameryce ten atrybut zawiera kod pocztowy. Maksymalna długość 40.|Tak|Nie|Utrwalony, wyjściowy|
|preferredLanguage    |Ciąg|Preferowany język użytkownika. Powinien być zgodny z kodem ISO 639-1. Przykład: "en-US".|Nie|Nie|Utrwalony, wyjściowy|
|refreshTokensValidFromDateTime|DateTime|Wszystkie tokeny odświeżania wystawione przed tym czasem są nieprawidłowe, a w przypadku korzystania z nieprawidłowego tokenu odświeżania aplikacje będą otrzymywać błędy. W takim przypadku aplikacja będzie musiała uzyskać nowy token odświeżania, wysyłając żądanie do punktu końcowego autoryzacji. Tylko do odczytu.|Nie|Nie|Dane wyjściowe|
|signInNames ([tożsamości](manage-user-accounts-graph-api.md#identities-property)) |Ciąg|Unikatowa nazwa logowania użytkownika konta lokalnego dowolnego typu w katalogu. Użyj tej opcji, aby uzyskać użytkownika z wartością logowania bez określenia typu konta lokalnego.|Nie|Nie|Dane wejściowe|
|signInNames. userName ([tożsamości](manage-user-accounts-graph-api.md#identities-property)) |Ciąg|Unikatowa nazwa użytkownika konta lokalnego w katalogu. Użyj tego, aby utworzyć lub pobrać użytkownika z określoną nazwą użytkownika logowania. Określenie tej opcji w PersistedClaims samej podczas operacji patch spowoduje usunięcie innych typów signInNames. Jeśli chcesz dodać nowy typ signInNames, musisz również zachować istniejące signInNames.|Nie|Nie|Dane wejściowe, utrwalone i wyjściowe|
|signInNames. numer telefonu ([tożsamości](manage-user-accounts-graph-api.md#identities-property)) |Ciąg|Unikatowy numer telefonu użytkownika konta lokalnego w katalogu. Użyj tego, aby utworzyć lub uzyskać użytkownika z określonym numerem telefonu logowania. Określenie tej opcji w PersistedClaims samej podczas operacji patch spowoduje usunięcie innych typów signInNames. Jeśli chcesz dodać nowy typ signInNames, musisz również zachować istniejące signInNames.|Nie|Nie|Dane wejściowe, utrwalone i wyjściowe|
|signInNames. emailAddress ([tożsamości](manage-user-accounts-graph-api.md#identities-property))|Ciąg|Unikatowy adres e-mail użytkownika konta lokalnego w katalogu. Użyj tego elementu, aby utworzyć lub uzyskać użytkownika z określonym adresem e-mail logowania. Określenie tej opcji w PersistedClaims samej podczas operacji patch spowoduje usunięcie innych typów signInNames. Jeśli chcesz dodać nowy typ signInNames, musisz również zachować istniejące signInNames.|Nie|Nie|Dane wejściowe, utrwalone i wyjściowe|
|stan           |Ciąg|Województwo w adresie użytkownika. Maksymalna długość 128.|Tak|Tak|Utrwalony, wyjściowy|
|streetAddress   |Ciąg|Ulica siedziby firmy użytkownika. Maksymalna długość 1024.|Tak|Tak|Utrwalony, wyjściowy|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|Ciąg|Dodatkowy numer telefonu użytkownika używany do uwierzytelniania wieloskładnikowego.|Tak|Nie|Utrwalony, wyjściowy|
|strongAuthenticationEmailAddress<sup>1</sup>|Ciąg|Adres SMTP użytkownika. Przykład: " bob@contoso.com " ten atrybut jest używany do logowania przy użyciu zasad username, aby przechowywać adres e-mail użytkownika. Adres e-mail używany w ramach przepływu resetowania hasła.|Tak|Nie|Utrwalony, wyjściowy|
|strongAuthenticationPhoneNumber<sup>1</sup>|Ciąg|Podstawowy numer telefonu użytkownika używany do uwierzytelniania wieloskładnikowego.|Tak|Nie|Utrwalony, wyjściowy|
|surname         |Ciąg|Nazwisko użytkownika (Nazwa rodziny lub nazwisko). Maksymalna długość 64.|Tak|Tak|Utrwalony, wyjściowy|
|numer telefonu (pierwszy wpis businessPhones)|Ciąg|Podstawowy numer telefonu miejsca pracy użytkownika.|Tak|Nie|Utrwalony, wyjściowy|
|userPrincipalName    |Ciąg|Główna nazwa użytkownika (UPN). Nazwa UPN jest nazwą logowania użytkownika w stylu internetowym opartą na standardzie internetowym RFC 822. Domena musi być obecna w kolekcji zweryfikowanych domen dzierżawcy. Ta właściwość jest wymagana podczas tworzenia konta. Niezmienne.|Nie|Nie|Dane wejściowe, utrwalone i wyjściowe|
|usageLocation   |Ciąg|Wymagane dla użytkowników, którzy będą przypisani do licencji z powodu wymogu prawnego do sprawdzenia dostępności usług w krajach/regionach. Nie dopuszcza wartości null. Dwuliterowy kod kraju/regionu (ISO standard 3166). Przykłady: "US", "JP" i "GB".|Tak|Nie|Utrwalony, wyjściowy|
|userType        |Ciąg|Wartość ciągu, która może służyć do klasyfikowania typów użytkowników w katalogu. Wartość musi być elementem członkowskim. Tylko do odczytu.|Tylko do odczytu|Nie|Utrwalony, wyjściowy|
|userState (externalUserState)<sup>2</sup>|Ciąg|Tylko w przypadku konta usługi Azure AD B2B wskazuje, czy zaproszenie jest PendingAcceptance czy zaakceptowane.|Nie|Nie|Utrwalony, wyjściowy|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|DateTime|Pokazuje sygnaturę czasową dla najnowszej zmiany właściwości UserState.|Nie|Nie|Utrwalony, wyjściowy|
|<sup>1 </sup> Nieobsługiwane przez Microsoft Graph<br><sup>2 </sup> Nie powinien być używany z Azure AD B2C||||||


## <a name="extension-attributes"></a>Atrybuty rozszerzenia

Często trzeba utworzyć własne atrybuty, tak jak w następujących przypadkach:

- Aplikacja mająca dostęp do klienta musi zachować dostęp do atrybutu, takiego jak **LoyaltyNumber**.
- Dostawca tożsamości ma unikatowy identyfikator użytkownika, taki jak **uniqueUserGUID** , który musi zostać zapisany.
- Niestandardowa podróż użytkownika musi utrzymywać się w stanie użytkownika, na przykład **migrationStatus**.

Azure AD B2C rozszerza zestaw atrybutów przechowywanych na poszczególnych kontach użytkowników. Atrybuty rozszerzenia [rozszerzają schemat](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions) obiektów użytkownika w katalogu. Atrybuty rozszerzenia mogą być rejestrowane tylko w obiekcie aplikacji, nawet jeśli mogą zawierać dane dla użytkownika. Atrybut rozszerzenia jest dołączany do aplikacji o nazwie B2C-Extensions-App. Nie należy modyfikować tej aplikacji, ponieważ jest ona używana przez Azure AD B2C do przechowywania danych użytkownika. Tę aplikację można znaleźć w obszarze Azure Active Directory Rejestracje aplikacji.

> [!NOTE]
> - Do 100 atrybutów rozszerzeń można zapisywać na dowolnym koncie użytkownika.
> - Jeśli aplikacja B2C-Extensions-App zostanie usunięta, te atrybuty rozszerzenia zostaną usunięte ze wszystkich użytkowników wraz z wszelkimi zawartymi w nich danymi.
> - Jeśli atrybut rozszerzenia zostanie usunięty przez aplikację, zostanie on usunięty z wszystkich kont użytkowników i wartości są usuwane.
> - Podstawowa nazwa atrybutu rozszerzenia jest generowana w formacie "Extension_" + Identyfikator aplikacji + "_" + nazwa atrybutu. Na przykład, jeśli utworzysz atrybut rozszerzenia LoyaltyNumber, a identyfikator aplikacji B2C-Extensions-App to 831374b3-bd50-41bf-aa54-263ec9e050fc, nazwa podstawowego atrybutu rozszerzenia będzie: extension_831374b3bd5041bfaa54263ec9e050fc_LoyaltyNumber. Nazwa podstawowa jest używana podczas uruchamiania zapytań interfejs API programu Graph w celu utworzenia lub zaktualizowania kont użytkowników.

Podczas definiowania właściwości w rozszerzeniu schematu obsługiwane są następujące typy danych:

|Typ właściwości |Uwagi  |
|--------------|---------|
|Boolean (wartość logiczna)    | Możliwe wartości: **true** lub **false**. |
|DateTime   | Musi być określony w formacie ISO 8601. Będą przechowywane w formacie UTC.   |
|Liczba całkowita    | 32 — wartość bitowa.               |
|Ciąg     | maksymalnie 256 znaków.     |

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej na temat atrybutów rozszerzenia:
- [Rozszerzenia schematu](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions)
- [Definiowanie atrybutów niestandardowych przy użyciu przepływu użytkownika](user-flow-custom-attributes.md)
- [Definiowanie atrybutów niestandardowych przy użyciu zasad niestandardowych](custom-policy-custom-attributes.md)
