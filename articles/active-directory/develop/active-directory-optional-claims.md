---
title: Dostarczanie opcjonalnych oświadczeń do aplikacji usługi Azure AD
titleSuffix: Microsoft identity platform
description: Jak dodać niestandardowe lub dodatkowe oświadczenia do tokenów tokenów sieci Web SAML 2,0 i JSON (JWT) wystawionych przez platformę tożsamości firmy Microsoft.
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/30/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, keyam
ms.custom: aaddev
ms.openlocfilehash: 3fd91bb86b32a958344dc366cfcd142bff369c1f
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108148"
---
# <a name="how-to-provide-optional-claims-to-your-app"></a>Instrukcje: dostarczanie opcjonalnych oświadczeń do aplikacji

Deweloperzy aplikacji mogą używać opcjonalnych oświadczeń w swoich aplikacjach usługi Azure AD, aby określić, które oświadczenia mają być wysyłane do aplikacji.

Opcjonalne oświadczenia można używać do:

- Wybierz dodatkowe oświadczenia do uwzględnienia w tokenach aplikacji.
- Zmień zachowanie niektórych oświadczeń zwracanych przez platformę tożsamości firmy Microsoft w tokenach.
- Dodawanie niestandardowych oświadczeń do aplikacji i uzyskiwanie do nich dostępu.

Listę oświadczeń standardowych można znaleźć w dokumentacji [token dostępu](access-tokens.md) i [id_token](id-tokens.md) oświadczenia.

Chociaż opcjonalne oświadczenia są obsługiwane zarówno w tokenach w formacie v 1.0, jak i 2.0, jak również w przypadku tokenów języka SAML, zapewniają większość wartości podczas przechodzenia z wersji 1.0 do wersji 2.0. Jednym z celów [punktu końcowego platformy tożsamości w wersji 2.0 firmy Microsoft](./v2-overview.md) jest mniejszy rozmiar tokenu, aby zapewnić optymalną wydajność przez klientów. W związku z tym kilka oświadczeń wcześniej uwzględnionych w tokenach dostępu i identyfikatorów nie jest już obecny w tokenach v 2.0 i musi być poproszony o odszukanie poszczególnych aplikacji.

**Tabela 1: możliwość zastosowania**

| Typ konta               | tokeny v 1.0 | tokeny v 2.0 |
|----------------------------|-------------|-------------|
| konto Microsoft osobiste | Nie dotyczy         | Obsługiwane   |
| Konto usługi Azure AD           | Obsługiwane   | Obsługiwane   |

## <a name="v10-and-v20-optional-claims-set"></a>zestaw oświadczeń opcjonalnych 1.0 i v 2.0

Zestaw opcjonalnych oświadczeń dostępnych domyślnie dla aplikacji do użycia znajduje się poniżej. Aby dodać niestandardowe oświadczenia opcjonalne dla aplikacji, zobacz [rozszerzenia katalogów](#configuring-directory-extension-optional-claims)poniżej. W przypadku dodawania oświadczeń do **tokenu dostępu** oświadczenia są stosowane do tokenów dostępu żądanych *dla* aplikacji (internetowego interfejsu API), a nie oświadczeń wymaganych *przez* aplikację. Niezależnie od tego, w jaki sposób klient uzyskuje dostęp do interfejsu API, odpowiednie dane są obecne w tokenie dostępu używanym do uwierzytelniania w interfejsie API.

> [!NOTE]
> Większość tych oświadczeń można uwzględnić w JWTs dla tokenów v 1.0 i v 2.0, ale nie tokenów SAML, z wyjątkiem sytuacji, w których zaznaczono w kolumnie Typ tokenu. Konta konsumentów obsługują podzbiór tych oświadczeń, które są oznaczone w kolumnie Typ użytkownika.  Wiele z wymienionych oświadczeń nie dotyczy użytkowników indywidualnych (nie mają dzierżawy, więc `tenant_ctry` nie ma żadnej wartości).

**Tabela 2: zestaw opcjonalnych zestawów zgłoszeń 1.0 i v 2.0**

| Nazwa                       |  Opis   | Typ tokenu | Typ użytkownika | Uwagi  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | Godzina ostatniego uwierzytelnienia użytkownika. Zobacz OpenID Connect Connect spec.| JWT        |           |  |
| `tenant_region_scope`      | Region dzierżawy zasobów | JWT        |           | |
| `sid`                      | Identyfikator sesji używany do wylogowywania użytkownika na sesję. | JWT        |  Konta osobiste i usługi Azure AD.   |         |
| `verified_primary_email`   | Źródłem z PrimaryAuthoritativeEmail użytkownika      | JWT        |           |         |
| `verified_secondary_email` | Źródłem z SecondaryAuthoritativeEmail użytkownika   | JWT        |           |        |
| `vnet`                     | Informacje o specyfikatorze sieci wirtualnej. | JWT        |           |      |
| `fwd`                      | Adres IP.| JWT    |   | Dodaje oryginalny adres IPv4 klienta żądającego (w sieci wirtualnej) |
| `ctry`                     | Kraj/region użytkownika | JWT |  | Usługa Azure AD zwraca `ctry` opcjonalne pole, jeśli jest obecne, a wartość pola to standardowy dwuliterowy kod kraju/regionu, taki jak fr, JP, sz i tak dalej. |
| `tenant_ctry`              | Kraj dzierżawy zasobu | JWT | | Taka sama jak `ctry` z wyjątkiem ustawionych na poziomie dzierżawy przez administratora.  Musi być również standardowa dwuliterowa wartość. |
| `xms_pdl`             | Preferowana lokalizacja danych   | JWT | | W przypadku dzierżaw z wieloma lokalizacjami geograficznymi preferowaną lokalizacją danych jest kod składający się z regionu geograficznego, w którym znajduje się użytkownik. Aby uzyskać więcej informacji, zobacz [dokumentację Azure AD Connect dotyczącą preferowanej lokalizacji danych](../hybrid/how-to-connect-sync-feature-preferreddatalocation.md).<br/>Na przykład: `APC` dla Azja i Pacyfik. |
| `xms_pl`                   | Preferowany język użytkownika  | JWT ||Preferowany język użytkownika, jeśli jest ustawiony. Pochodzący od swojej dzierżawy domowej w scenariuszach dostępu gościa. Sformatowane — DW ("pl-US"). |
| `xms_tpl`                  | Preferowany język dzierżawy| JWT | | Preferowany język dzierżawy zasobu, jeśli został ustawiony. Sformatowane w szystkie ("en"). |
| `ztdid`                    | Identyfikator wdrożenia bez dotknięcia | JWT | | Tożsamość urządzenia używana na potrzeby [Autopilotażu systemu Windows](/windows/deployment/windows-autopilot/windows-10-autopilot) |
| `email`                    | Adres e-mail dla tego użytkownika, jeśli użytkownik go ma.  | JWT, SAML | MSA, Azure AD | Ta wartość jest uwzględniana domyślnie, jeśli użytkownik jest gościem w dzierżawie.  W przypadku użytkowników zarządzanych (użytkowników w ramach dzierżawy) należy zażądać ich przez to opcjonalne żądanie lub tylko w wersji 3.0, z zakresem OpenID Connect.  W przypadku użytkowników zarządzanych adres e-mail musi być ustawiony w [portalu administracyjnym pakietu Office](https://portal.office.com/adminportal/home#/users).|
| `acct`                | Stan konta użytkowników w dzierżawie | JWT, SAML | | Jeśli użytkownik jest członkiem dzierżawy, wartość jest `0` . Jeśli jest gościem, wartość to `1` . |
| `groups`| Opcjonalne formatowanie oświadczeń grupy |JWT, SAML| |Używany w połączeniu z ustawieniem GroupMembershipClaims w [manifeście aplikacji](reference-app-manifest.md), który musi być również ustawiony. Aby uzyskać szczegółowe informacje, zobacz poniższe [oświadczenia grupy](#configuring-groups-optional-claims) . Aby uzyskać więcej informacji na temat oświadczeń grup, zobacz [jak skonfigurować oświadczenia grupy](../hybrid/how-to-connect-fed-group-claims.md)
| `upn`                      | UserPrincipalName | JWT, SAML  |           | Identyfikator dla użytkownika, którego można użyć z parametrem username_hint.  Nie jest to trwały identyfikator użytkownika i nie należy go używać do unikatowej tożsamości informacji o użytkowniku (np. klucza bazy danych). Zamiast tego należy użyć identyfikatora obiektu użytkownika ( `oid` ) jako klucza bazy danych. Użytkownicy logujący się przy użyciu [alternatywnego identyfikatora logowania](../authentication/howto-authentication-use-email-signin.md) nie powinny być pokazywane nazwy głównej użytkownika (UPN). Zamiast tego należy użyć następujących oświadczeń tokenu identyfikatora do wyświetlania stanu logowania dla użytkownika: `preferred_username` lub `unique_name` tokenów V1 oraz `preferred_username` tokenów v2. Chociaż to zgłoszenie jest automatycznie dołączane, można je określić jako opcjonalne, aby dołączyć dodatkowe właściwości, aby zmodyfikować jego zachowanie w przypadku użytkownika-gościa.  |
| `idtyp`                    | Typ tokenu   | Tokeny dostępu JWT | Specjalne: tylko w tokenach dostępu tylko do aplikacji |  Wartość jest `app` , gdy token jest tokenem obsługującym tylko aplikację. Jest to najdokładniejszy sposób, aby określić, czy token jest tokenem aplikacji, czy aplikacją i tokenem użytkownika.|

## <a name="v20-specific-optional-claims-set"></a>v 2.0 — zestaw oświadczeń opcjonalnych specyficznych

Te oświadczenia są zawsze uwzględniane w tokenach usługi Azure AD w wersji 1.0, ale nie są uwzględniane w tokenach v 2.0, chyba że jest to wymagane. Te oświadczenia mają zastosowanie tylko do JWTs (tokeny ID i tokeny dostępu).

**Tabela 3: v 2.0 — tylko opcjonalne oświadczenia**

| Claim JWT     | Nazwa                            | Opis                                | Uwagi |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | Adres IP                      | Adres IP, z którego zalogował się klient.   |       |
| `onprem_sid`  | Lokalny identyfikator zabezpieczeń |                                             |       |
| `pwd_exp`     | Czas wygaśnięcia hasła        | Data i godzina wygaśnięcia hasła. |       |
| `pwd_url`     | Zmień adres URL hasła             | Adres URL, który użytkownik może odwiedzić, aby zmienić hasło.   |   |
| `in_corp`     | Wewnątrz sieci firmowej        | Sygnalizuje, czy klient loguje się z sieci firmowej. W przeciwnym razie oświadczenia nie są uwzględniane.   |  Na podstawie ustawień [zaufanych adresów IP](../authentication/howto-mfa-mfasettings.md#trusted-ips) w usłudze MFA.    |
| `family_name` | Nazwisko                       | Zawiera nazwisko, nazwisko lub nazwę rodziny użytkownika, zgodnie z definicją w obiekcie użytkownika. <br>"family_name": "Miller" | Obsługiwane w usłudze MSA i usłudze Azure AD. Wymaga `profile` zakresu.   |
| `given_name`  | Imię                      | Określa imię i nazwisko użytkownika, zgodnie z ustawieniem obiektu użytkownika.<br>"given_name": "Piotr"                   | Obsługiwane w usłudze MSA i usłudze Azure AD.  Wymaga `profile` zakresu. |
| `upn`         | Nazwa główna użytkownika | Identyfikator dla użytkownika, którego można użyć z parametrem username_hint.  Nie jest to trwały identyfikator użytkownika i nie należy go używać do unikatowej tożsamości informacji o użytkowniku (np. klucza bazy danych). Zamiast tego należy użyć identyfikatora obiektu użytkownika ( `oid` ) jako klucza bazy danych. Użytkownicy logujący się przy użyciu [alternatywnego identyfikatora logowania](../authentication/howto-authentication-use-email-signin.md) nie powinny być pokazywane nazwy głównej użytkownika (UPN). Zamiast tego należy użyć następujących oświadczeń tokenu identyfikatora do wyświetlania stanu logowania dla użytkownika: `preferred_username` lub `unique_name` tokenów V1 oraz `preferred_username` tokenów v2. | Zapoznaj się z [dodatkowymi właściwościami](#additional-properties-of-optional-claims) poniżej w celu skonfigurowania żądania. Wymaga `profile` zakresu.|

### <a name="additional-properties-of-optional-claims"></a>Dodatkowe właściwości oświadczeń opcjonalnych

Niektóre opcjonalne oświadczenia można skonfigurować w celu zmiany sposobu zwracania oświadczenia. Te dodatkowe właściwości są najczęściej używane do migracji aplikacji lokalnych z różnymi oczekiwaniami danych. Program pomaga na przykład `include_externally_authenticated_upn_without_hash` klientom, którzy nie mogą obsłużyć znaków hash ( `#` ) w nazwy UPN.

**Tabela 4: wartości konfiguracji opcjonalnych oświadczeń**

| Nazwa właściwości  | Dodatkowa nazwa właściwości | Opis |
|----------------|--------------------------|-------------|
| `upn`          |                          | Może być używany w przypadku odpowiedzi SAML i JWT oraz dla tokenów v 1.0 i v 2.0. |
|                | `include_externally_authenticated_upn`  | Zawiera nazwę UPN gościa przechowywaną w dzierżawie zasobów. Na przykład `foo_hometenant.com#EXT#@resourcetenant.com` |
|                | `include_externally_authenticated_upn_without_hash` | Tak samo jak powyżej, z tą różnicą, że znaki hash ( `#` ) są zastępowane znakami podkreślenia ( `_` ), na przykład `foo_hometenant.com_EXT_@resourcetenant.com` |

#### <a name="additional-properties-example"></a>Przykład dodatkowych właściwości

```json
"optionalClaims": {
    "idToken": [
        {
            "name": "upn",
            "essential": false,
            "additionalProperties": [
                "include_externally_authenticated_upn"
            ]
        }
    ]
}
```

Ten obiekt OptionalClaims powoduje, że token identyfikatora zwrócony klientowi do uwzględnienia `upn` zgłoszenia z dodatkowymi informacjami dzierżawy głównej i dzierżawy zasobów. To zgłoszenie `upn` jest zmieniane w tokenie, jeśli użytkownik jest gościem w dzierżawie (który korzysta z innego dostawcy tożsamości na potrzeby uwierzytelniania).

## <a name="configuring-optional-claims"></a>Konfigurowanie oświadczeń opcjonalnych

> [!IMPORTANT]
> Tokeny dostępu są **zawsze** generowane przy użyciu manifestu zasobu, a nie klienta.  Dlatego w żądaniu `...scope=https://graph.microsoft.com/user.read...` zasób jest interfejsem API Microsoft Graph.  W takim przypadku token dostępu jest tworzony przy użyciu manifestu interfejsu API Microsoft Graph, a nie manifestu klienta.  Zmiana manifestu dla aplikacji nigdy nie spowoduje, że tokeny dla interfejsu API Microsoft Graph będą wyglądały inaczej.  Aby sprawdzić, czy `accessToken` zmiany są obowiązujące, zażądaj tokenu dla aplikacji, a nie innej aplikacji.

Opcjonalne oświadczenia dla aplikacji można skonfigurować za pomocą interfejsu użytkownika lub manifestu aplikacji.

1. Przejdź do witryny [Azure Portal](https://portal.azure.com). Wyszukaj i wybierz pozycję **Azure Active Directory**.
1. W sekcji **Zarządzanie** wybierz pozycję **rejestracje aplikacji**.
1. Wybierz aplikację, dla której chcesz skonfigurować oświadczenia opcjonalne.

**Konfigurowanie opcjonalnych oświadczeń za pomocą interfejsu użytkownika:**

[![Konfigurowanie opcjonalnych oświadczeń w interfejsie użytkownika](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. W sekcji **Zarządzanie** wybierz pozycję **Konfiguracja tokenu**.
1. Wybierz pozycję **Dodaj opcjonalne** pole.
1. Wybierz typ tokenu, który chcesz skonfigurować.
1. Wybierz opcjonalne oświadczenia do dodania.
1. Wybierz pozycję **Dodaj**.

> [!NOTE]
> Blok **Konfiguracja tokenu** opcji interfejsu użytkownika nie jest dostępny dla aplikacji zarejestrowanych w dzierżawie Azure AD B2C. W przypadku aplikacji zarejestrowanych w dzierżawie B2C opcjonalne oświadczenia można skonfigurować przez modyfikację manifestu aplikacji. Aby uzyskać więcej informacji [, zobacz Dodawanie oświadczeń i dostosowywanie danych wejściowych użytkownika przy użyciu zasad niestandardowych w Azure Active Directory B2C](../../active-directory-b2c/configure-user-input.md) 

**Konfigurowanie opcjonalnych oświadczeń za pomocą manifestu aplikacji:**

[![Pokazuje, jak skonfigurować oświadczenia opcjonalne przy użyciu manifestu aplikacji](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. W sekcji **Zarządzanie** wybierz pozycję **manifest**. Zostanie otwarty Edytor manifestu oparty na sieci Web, który umożliwia edycję manifestu. Opcjonalnie możesz wybrać pozycję **Pobierz** i edytować manifest lokalnie, a następnie użyć pozycji **Przekaż** w celu ponownego zastosowania go dla aplikacji. Aby uzyskać więcej informacji na temat manifestu aplikacji, zobacz [artykuł Omówienie manifestu aplikacji usługi Azure AD](reference-app-manifest.md).

    Następujący wpis manifestu aplikacji dodaje opcjonalne oświadczenia auth_time, IPADDR i UPN do identyfikatorów, dostępu i tokenów SAML.

    ```json
    "optionalClaims": {
        "idToken": [
            {
                "name": "auth_time",
                "essential": false
            }
        ],
        "accessToken": [
            {
                "name": "ipaddr",
                "essential": false
            }
        ],
        "saml2Token": [
            {
                "name": "upn",
                "essential": false
            },
            {
                "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                "source": "user",
                "essential": false
            }
        ]
    }
    ```

2. Po zakończeniu wybierz pozycję **Zapisz**. Teraz określone opcjonalne oświadczenia zostaną uwzględnione w tokenach aplikacji.


### <a name="optionalclaims-type"></a>Typ OptionalClaims

Deklaruje opcjonalne oświadczenia wymagane przez aplikację. Aplikacja może skonfigurować opcjonalne oświadczenia, które mają być zwracane w każdym z trzech typów tokenów (token identyfikatora, token dostępu, token SAML 2), które mogą być odbierane z usługi tokenu zabezpieczającego. Aplikacja może skonfigurować inny zestaw opcjonalnych oświadczeń do zwrócenia w każdym typie tokenu. Właściwość OptionalClaims obiektu aplikacji jest obiektem OptionalClaims.

**Tabela 5: właściwości typu OptionalClaims**

| Nazwa          | Typ                       | Opis                                           |
|---------------|----------------------------|-------------------------------------------------------|
| `idToken`     | Kolekcja (OptionalClaim) | Opcjonalne oświadczenia zwracane w tokenie identyfikatora JWT.     |
| `accessToken` | Kolekcja (OptionalClaim) | Opcjonalne oświadczenia zwracane w tokenie dostępu JWT. |
| `saml2Token`  | Kolekcja (OptionalClaim) | Opcjonalne oświadczenia zwracane w tokenie języka SAML.       |

### <a name="optionalclaim-type"></a>Typ OptionalClaim

Zawiera opcjonalne powiązanie skojarzone z aplikacją lub jednostką usługi. Właściwości idToken, accessToken i saml2Token typu [OptionalClaims](/graph/api/resources/optionalclaims) to kolekcja OptionalClaim.
Jeśli jest to obsługiwane przez określone zgłoszenie, można również zmodyfikować zachowanie OptionalClaim przy użyciu pola AdditionalProperties.

**Tabela 6: właściwości typu OptionalClaim**

| Nazwa                   | Typ                    | Opis                                                                                                                                                                                                                                                                                                   |
|------------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | Nazwa opcjonalnego żądania.                                                                                                                                                                                                                                                                               |
| `source`               | Edm.String              | Źródło (obiekt katalogu) żądania. Istnieją wstępnie zdefiniowane oświadczenia i zdefiniowane przez użytkownika oświadczenia ze wszystkich właściwości rozszerzenia. Jeśli wartość źródłowa jest równa null, to jest to wstępnie zdefiniowane opcjonalne zgłoszenie. Jeśli wartością źródłową jest użytkownik, wartość we właściwości Nazwa jest właściwością rozszerzenia z obiektu użytkownika. |
| `essential`            | Edm.Boolean             | Jeśli wartość jest równa true, żądanie określone przez klienta jest konieczne, aby zapewnić bezproblemowe środowisko autoryzacji dla określonego zadania żądanego przez użytkownika końcowego. Wartość domyślna to false.                                                                                                                 |
| `additionalProperties` | Kolekcja (EDM. String) | Dodatkowe właściwości żądania. Jeśli właściwość istnieje w tej kolekcji, modyfikuje zachowanie opcjonalnego żądania określonego we właściwości Nazwa.                                                                                                                                                   |

## <a name="configuring-directory-extension-optional-claims"></a>Konfigurowanie opcjonalnych oświadczeń rozszerzenia katalogu

Oprócz standardowego opcjonalnego zestawu oświadczeń można także skonfigurować tokeny do dołączania rozszerzeń. Aby uzyskać więcej informacji, zobacz [dokumentację dotyczącą Microsoft Graph extensionProperty](/graph/api/resources/extensionproperty).

Rozszerzenia schematu i otwarte nie są obsługiwane przez oświadczenia opcjonalne, tylko rozszerzenia katalogu stylów AAD-Graph. Ta funkcja jest przydatna do dołączania dodatkowych informacji o użytkownikach, które mogą być używane przez aplikację — na przykład dodatkowego identyfikatora lub ważnej opcji konfiguracji ustawionej przez użytkownika. Przykład znajduje się na końcu tej strony.

> [!NOTE]
> Rozszerzenia schematu katalogu są funkcją tylko usługi Azure AD. Jeśli manifest aplikacji żąda rozszerzenia niestandardowego, a użytkownik MSA zaloguje się do aplikacji, te rozszerzenia nie zostaną zwrócone.

### <a name="directory-extension-formatting"></a>Formatowanie rozszerzenia katalogu

Podczas konfigurowania opcjonalnych oświadczeń rozszerzenia katalogu przy użyciu manifestu aplikacji należy użyć pełnej nazwy rozszerzenia (w formacie: `extension_<appid>_<attributename>` ). `<appid>`Musi być zgodna z identyfikatorem aplikacji żądającej żądania.

W ramach tokenu JWT te oświadczenia będą emitowane z następującym formatem nazw:  `extn.<attributename>` .

W ramach tokenów SAML te oświadczenia będą emitowane przy użyciu następującego formatu identyfikatora URI: `http://schemas.microsoft.com/identity/claims/extn.<attributename>`

## <a name="configuring-groups-optional-claims"></a>Konfigurowanie opcjonalnych oświadczeń grup

   > [!NOTE]
   > Możliwość emisji nazw grup dla użytkowników i grup synchronizowanych z lokalnej wersji zapoznawczej.

W tej sekcji omówiono opcje konfiguracji w obszarze opcjonalne oświadczenia dotyczące zmiany atrybutów grupy używanych w oświadczeniach grup z domyślnego identyfikatora obiektu grupy na atrybuty synchronizowane z lokalnymi Active Directory systemu Windows. Można skonfigurować grupy opcjonalne oświadczenia dla aplikacji za pomocą interfejsu użytkownika lub manifestu aplikacji.

> [!IMPORTANT]
> Aby uzyskać więcej informacji, w tym ważne zastrzeżenia dla publicznej wersji zapoznawczej oświadczeń grup z atrybutów lokalnych, zobacz [Konfigurowanie oświadczeń grup dla aplikacji w usłudze Azure AD](../hybrid/how-to-connect-fed-group-claims.md).

**Konfigurowanie grup opcjonalnych oświadczeń za pomocą interfejsu użytkownika:**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po uwierzytelnieniu wybierz dzierżawę usługi Azure AD, wybierając ją w prawym górnym rogu strony.
1. Wybierz **Azure Active Directory** z menu po lewej stronie.
1. W sekcji **Zarządzanie** wybierz pozycję **rejestracje aplikacji**.
1. Wybierz aplikację, dla której chcesz skonfigurować oświadczenia opcjonalne.
1. W sekcji **Zarządzanie** wybierz pozycję **Konfiguracja tokenu**.
1. Wybierz pozycję **Dodaj grupę**.
1. Wybierz typy grup do zwrócenia (**grupy zabezpieczeń** lub **role katalogu**, **wszystkie grupy** i/lub **grupy przypisane do aplikacji**). **Grupy przypisane do opcji aplikacji** obejmują tylko grupy przypisane do aplikacji. Opcja **wszystkie grupy** zawiera **zabezpieczenia**, **DirectoryRole** i **DistributionList**, ale nie **grup przypisanych do aplikacji**. 
1. Opcjonalne: wybierz właściwości określonego typu tokenu, aby zmodyfikować wartość roli role, aby zawierała atrybuty grupy lokalnej lub aby zmienić typ obiektu na rolę.
1. Wybierz pozycję **Zapisz**.

**Konfigurowanie grup opcjonalnych oświadczeń za pomocą manifestu aplikacji:**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po uwierzytelnieniu wybierz dzierżawę usługi Azure AD, wybierając ją w prawym górnym rogu strony.
1. Wybierz **Azure Active Directory** z menu po lewej stronie.
1. Wybierz aplikację, dla której chcesz skonfigurować oświadczenia opcjonalne.
1. W sekcji **Zarządzanie** wybierz pozycję **manifest**.
1. Dodaj następujący wpis przy użyciu edytora manifestu:

   Prawidłowe wartości to:

   - "All" (Ta opcja obejmuje zabezpieczenia, DirectoryRole i DistributionList)
   - Grupy securitygroup
   - "DirectoryRole"
   - "Aplikacja" (Ta opcja obejmuje tylko grupy, które są przypisane do aplikacji)

   Na przykład:

    ```json
    "groupMembershipClaims": "SecurityGroup"
    ```

   Domyślnie identyfikatory obiektów grupy będą emitowane w wartości Claim Group.  Aby zmodyfikować wartość żądania w taki sposób, aby zawierała atrybuty grupy lokalnej, lub aby zmienić typ typu "rola", użyj konfiguracji OptionalClaims w następujący sposób:

1. Określ ustawienia nazw grup opcjonalne oświadczenia.

   Jeśli chcesz, aby grupy w tokenie zawierały atrybuty lokalnych grup usługi AD w sekcji opcjonalne oświadczenia, określ, które opcjonalne oświadczenie o typie tokenu należy zastosować do, nazwę opcjonalnego oświadczenia, a także wszystkie wymagane właściwości.  Można wymienić wiele typów tokenów:

   - idToken dla tokenu identyfikatora OIDC
   - accessToken dla tokenu dostępu OAuth
   - Saml2Token dla tokenów SAML.

   > [!NOTE]
   > Typ Saml2Token ma zastosowanie do tokenów w formacie SAML 1.1 i SAML 2.0.

   Dla każdego odpowiedniego typu tokenu zmodyfikuj je, aby użyć sekcji OptionalClaims w manifeście. Schemat OptionalClaims jest następujący:

    ```json
    {
        "name": "groups",
        "source": null,
        "essential": false,
        "additionalProperties": []
    }
    ```

   | Opcjonalny schemat oświadczeń | Wartość |
   |----------|-------------|
   | **Nazwij** | Musi być "grupami" |
   | **zewnętrz** | Nie używany. Pomiń lub określ wartość null |
   | **olejk** | Nie używany. Pomiń lub określ wartość false |
   | **AdditionalProperties** | Lista dodatkowych właściwości.  Prawidłowe opcje to "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name", "emit_as_roles" |

   W additionalProperties są wymagane tylko jeden z "sam_account_name", "dns_domain_and_sam_account_name", "netbios_domain_and_sam_account_name".  Jeśli istnieje więcej niż jeden, zostanie użyta pierwsza wartość i wszystkie pozostałe zostały zignorowane.

   Niektóre aplikacje wymagają informacji o grupie dla użytkownika w ramach roszczeń ról.  Aby zmienić typ zgłoszenia z żądania grupy do roszczeń roli, Dodaj "emit_as_roles" do dodatkowych właściwości.  Wartości grupy będą emitowane w ramach roszczeń ról.

   > [!NOTE]
   > Jeśli zostanie użyta wartość "emit_as_roles", wszystkie role aplikacji skonfigurowane do przypisywania użytkownika nie będą wyświetlane w ramach tego żądania.

**Przykłady:**

1) Emituj grupy jako nazwy grup w tokenach dostępu OAuth w formacie dnsDomainName\sAMAccountName

    **Konfiguracja interfejsu użytkownika:**

    [![Konfigurowanie opcjonalnych oświadczeń](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png)

    **Wpis manifestu aplikacji:**

    ```json
    "optionalClaims": {
        "accessToken": [
            {
                "name": "groups",
                "additionalProperties": [
                    "dns_domain_and_sam_account_name"
                ]
            }
        ]
    }
    ```

2) Emituj nazwy grup, które mają być zwracane w formacie netbiosDomain\sAMAccountName jako rolę roszczeń w tokenach identyfikatorów SAML i OIDC

    **Konfiguracja interfejsu użytkownika:**

    [![Opcjonalne oświadczenia w manifeście](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

    **Wpis manifestu aplikacji:**

    ```json
    "optionalClaims": {
        "saml2Token": [
            {
                "name": "groups",
                "additionalProperties": [
                    "netbios_name_and_sam_account_name",
                    "emit_as_roles"
                ]
            }
        ],
        "idToken": [
            {
                "name": "groups",
                "additionalProperties": [
                    "netbios_name_and_sam_account_name",
                    "emit_as_roles"
                ]
            }
        ]
    }
    ```

## <a name="optional-claims-example"></a>Przykład opcjonalnych oświadczeń

W tej sekcji można zapoznać się z scenariuszem, aby dowiedzieć się, jak można użyć opcjonalnej funkcji oświadczeń dla aplikacji.
Dostępnych jest wiele opcji aktualizowania właściwości konfiguracji tożsamości aplikacji w celu włączenia i skonfigurowania oświadczeń opcjonalnych:

- Możesz użyć interfejsu użytkownika **konfiguracji tokenu** (Zobacz przykład poniżej)
- Możesz użyć **manifestu** (Zobacz przykład poniżej). Przeczytaj [dokument manifestu aplikacji usługi Azure AD](./reference-app-manifest.md) najpierw, aby zapoznać się z wprowadzeniem do manifestu.
- Istnieje również możliwość napisania aplikacji, która używa [interfejsu API Microsoft Graph](/graph/use-the-api) do aktualizowania aplikacji. Typ [OptionalClaims](/graph/api/resources/optionalclaims) w podręczniku Skorowidz interfejsu API Microsoft Graph może pomóc w konfigurowaniu opcjonalnych oświadczeń.

**Przykład:**

W poniższym przykładzie użyjesz interfejsu użytkownika i manifestu **konfiguracji tokenu** w celu  dodania opcjonalnych oświadczeń do tokenów dostępu, identyfikatora i SAML przeznaczonych dla aplikacji. Różne opcjonalne oświadczenia zostaną dodane do każdego typu tokenu, który aplikacja może odbierać:

- Tokeny identyfikatora będą teraz zawierać nazwę UPN dla użytkowników federacyjnych w pełnej postaci ( `<upn>_<homedomain>#EXT#@<resourcedomain>` ).
- Tokeny dostępu, które inne klienci żądają tej aplikacji, będą teraz zawierać auth_timeą.
- Tokeny SAML będą teraz zawierać rozszerzenie schematu katalogu skypeId (w tym przykładzie identyfikator aplikacji dla tej aplikacji to ab603c56068041afb2f6832e2a17e237). Tokeny SAML będą uwidaczniać identyfikator Skype jako `extension_skypeId` .

**Konfiguracja interfejsu użytkownika:**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po uwierzytelnieniu wybierz dzierżawę usługi Azure AD, wybierając ją w prawym górnym rogu strony.

1. Wybierz **Azure Active Directory** z menu po lewej stronie.

1. W sekcji **Zarządzanie** wybierz pozycję **rejestracje aplikacji**.

1. Znajdź aplikację, dla której chcesz skonfigurować opcjonalne oświadczenia na liście, a następnie wybierz ją.

1. W sekcji **Zarządzanie** wybierz pozycję **Konfiguracja tokenu**.

1. Wybierz pozycję **Dodaj oświadczenie opcjonalne**, wybierz typ tokenu **identyfikatora** , wybierz **nazwę UPN** z listy oświadczeń, a następnie wybierz pozycję **Dodaj**.

1. Wybierz pozycję **Dodaj oświadczenie opcjonalne**, wybierz typ tokenu **dostępu** , wybierz pozycję **auth_time** z listy oświadczeń, a następnie wybierz pozycję **Dodaj**.

1. Na ekranie przegląd konfiguracji tokenu wybierz ikonę ołówka obok pozycji **UPN**, wybierz przełącznik **uwierzytelnienie zewnętrznie** , a następnie wybierz pozycję **Zapisz**.

1. Wybierz pozycję **Dodaj oświadczenie opcjonalne**, wybierz typ tokenu **SAML** , wybierz pozycję **Extn. skypeID** z listy oświadczeń (dotyczy to tylko sytuacji, gdy utworzono obiekt użytkownika usługi Azure AD o nazwie skypeID), a następnie wybierz pozycję **Dodaj**.

    [![Opcjonalne oświadczenia dla tokenu SAML](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**Konfiguracja manifestu:**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Po uwierzytelnieniu wybierz dzierżawę usługi Azure AD, wybierając ją w prawym górnym rogu strony.
1. Wybierz **Azure Active Directory** z menu po lewej stronie.
1. Znajdź aplikację, dla której chcesz skonfigurować opcjonalne oświadczenia na liście, a następnie wybierz ją.
1. W sekcji **Zarządzanie** wybierz pozycję **manifest** , aby otworzyć wbudowany edytor manifestu.
1. Możesz bezpośrednio edytować manifest za pomocą tego edytora. Manifest jest zgodny ze schematem dla [jednostki aplikacji](./reference-app-manifest.md)i automatycznie formatuje manifest po zapisaniu. Nowe elementy zostaną dodane do `OptionalClaims` właściwości.

    ```json
    "optionalClaims": {
        "idToken": [
            {
                "name": "upn",
                "essential": false,
                "additionalProperties": [
                    "include_externally_authenticated_upn"
                ]
            }
        ],
        "accessToken": [
            {
                "name": "auth_time",
                "essential": false
            }
        ],
        "saml2Token": [
            {
                "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                "source": "user",
                "essential": true
            }
        ]
    }
    ```

1. Po zakończeniu aktualizowania manifestu wybierz pozycję **Zapisz** , aby zapisać manifest.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat standardowych oświadczeń dostarczonych przez usługę Azure AD.

- [Tokeny identyfikatorów](id-tokens.md)
- [Tokeny dostępu](access-tokens.md)
