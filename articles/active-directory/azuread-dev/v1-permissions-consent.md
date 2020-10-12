---
title: Uprawnienia w usłudze Azure Active Directory | Microsoft Docs
description: Dowiedz się więcej na temat uprawnień w usłudze Azure Active Directory i sposobu ich użycia.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: jesakowi
ms.custom: aaddev
ROBOTS: NOINDEX
ms.openlocfilehash: c600e1fddc0089a508ff0cfebbbb3476f3a90008
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88117621"
---
# <a name="permissions-and-consent-in-the-azure-active-directory-v10-endpoint"></a>Uprawnienia i zgoda w punkcie końcowym Azure Active Directory v 1.0

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Usługa Azure Active Directory (Azure AD) szeroko wykorzystuje użycie uprawnień dla przepływów OAuth i OIDC (OpenID Connect). Po odebraniu przez aplikację tokenu dostępu od usługi Azure AD aplikacja uwzględnia oświadczenia z opisami uprawnień, które aplikacja ma w odniesieniu do określonego zasobu.

*Uprawnienia*, znane także jako *zakresy*, ułatwiają autoryzację zasobu, ponieważ wymaga tylko sprawdzenia, czy token zawiera odpowiednie uprawnienia do interfejsu API, do którego nastąpi wywołanie aplikacji.

## <a name="types-of-permissions"></a>Typy uprawnień

W usłudze Azure AD zdefiniowano dwa rodzaje uprawnień:

* **Uprawnienia delegowane** — są używane przez aplikacje, w których użytkownik jest obecnie zalogowany. W przypadku takich aplikacji zgodę na uprawnienia żądane przez aplikację może wyrazić użytkownik lub administrator. Podczas wywołań interfejsu API do aplikacji są delegowane uprawnienia do działania w roli zalogowanego użytkownika. W zależności od interfejsu API użytkownik może nie być w stanie bezpośrednio wyrazić zgodę na interfejs API, a zamiast tego [wymagać od administratora podania "zgody administratora"](../develop/howto-convert-app-to-be-multi-tenant.md).
* **Uprawnienia aplikacji** — są używane przez aplikacje i nie wymagają zalogowanego użytkownika. Są to np. aplikacje działające jako usługi w tle lub demony. Uprawnienia aplikacji mogą być [wysyłane tylko przez administratorów](../develop/v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant) , ponieważ są zwykle zaawansowane i umożliwiają dostęp do danych w granicach użytkowników lub danych, które w przeciwnym razie byłyby ograniczone do administratorów. Użytkownicy, którzy są zdefiniowani jako właściciele aplikacji zasobów (tj. interfejs API, który publikuje uprawnienia), również mogą przyznawać uprawnienia aplikacji do interfejsów API, których są właścicielami.

Czynne uprawnienia to uprawnienia, które aplikacja będzie posiadać podczas wysyłania żądań do interfejsu API. 

* W przypadku uprawnień delegowanych czynne uprawnienia aplikacji będą najmniejszą wspólną częścią delegowanych uprawnień przyznanych aplikacji (poprzez wyrażenie zgody) oraz uprawnień aktualnie zalogowanego użytkownika. Aplikacja nigdy nie może mieć większych uprawnień niż zalogowany użytkownik. Uprawnienia zalogowanego użytkownika mogą być określone w organizacji na podstawie zasad lub członkostwa w co najmniej jednej roli administratora. Aby dowiedzieć się, które role administratorów mogą wyrazić zgodę na delegowane uprawnienia, zobacz [uprawnienia roli administratora w usłudze Azure AD](../users-groups-roles/directory-assign-admin-roles.md).
    Załóżmy na przykład, że aplikacja ma przyznane uprawnienie delegowane `User.ReadWrite.All` w programie Microsoft Graph. Uprawnienie to przyznaje aplikacji nominalne uprawnienia do odczytu i aktualizowania profilu każdego użytkownika w organizacji. Jeśli zalogowany użytkownik jest administratorem globalnym, aplikacja będzie mogła zaktualizować profil każdego użytkownika w organizacji. Jednak jeśli zalogowany użytkownik nie ma roli administratora, aplikacja będzie mogła aktualizować tylko profil zalogowanego użytkownika. Użytkownik nie będzie mógł zaktualizować profili innych użytkowników w organizacji, ponieważ użytkownik mający uprawnienia do działania w imieniu innych osób nie ma odpowiednich uprawnień.
* W przypadku uprawnień aplikacji efektywne uprawnienia aplikacji są na poziomie przywilejów wynikających z uprawnień. Na przykład aplikacja, która ma uprawnienie aplikacji `User.ReadWrite.All`, może aktualizować profile każdego użytkownika w organizacji.

## <a name="permission-attributes"></a>Atrybuty uprawnień
Uprawnienia w usłudze Azure AD mają wiele właściwości, które pomagają użytkownikom, administratorom i deweloperom aplikacji w podejmowaniu świadomych decyzji dotyczących przydzielania uprawnień.

> [!NOTE]
> W witrynie Azure Portal i programie PowerShell można wyświetlić uprawnienia udostępniane przez aplikację usługi Azure AD lub Service Principal. Uruchom ten skrypt, aby wyświetlić uprawnienia udostępniane przez program Microsoft Graph.
> ```powershell
> Connect-AzureAD
> 
> # Get OAuth2 Permissions/delegated permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").OAuth2Permissions
> 
> # Get App roles/application permissions
> (Get-AzureADServicePrincipal -filter "DisplayName eq 'Microsoft Graph'").AppRoles
> ```

| Nazwa właściwości | Opis | Przykład |
| --- | --- | --- |
| `ID` | Wartość identyfikatora GUID, która unikatowo identyfikuje to uprawnienie. | 570282fd-fa5c-430d-a7fd-fc8dc98a9dca |
| `IsEnabled` | Wskazuje, czy to uprawnienie jest dostępne do użycia. | true |
| `Type` | Wskazuje, czy to uprawnienie wymaga zgody użytkownika, czy też zgody administratora. | Użytkownik |
| `AdminConsentDescription` | Opis widziany przez administratorów podczas procesu wyrażania przez nich zgody | Zezwala aplikacji na odczytywanie wiadomości e-mail w skrzynkach pocztowych użytkowników. |
| `AdminConsentDisplayName` | Przyjazna nazwa widziana przez administratorów podczas procesu wyrażania przez nich zgody. | Odczytuj pocztę użytkownika |
| `UserConsentDescription` | Opis widziany przez użytkowników podczas procesu wyrażania przez nich zgody. |  Zezwala aplikacji na odczytywanie wiadomości e-mail w skrzynce pocztowej użytkownika. |
| `UserConsentDisplayName` | Przyjazna nazwa widziana przez użytkowników podczas procesu wyrażania przez nich zgody. | Odczytywanie wiadomości |
| `Value` | Ciąg, który służy do identyfikowania uprawnienia podczas przepływów autoryzacji OAuth 2.0. Ciąg `Value` można również być połączyć z ciągiem identyfikatora URI aplikacji w celu utworzenia nazwy FQDN uprawnień. | `Mail.Read` |

## <a name="types-of-consent"></a>Typy zgody

Przy uzyskiwaniu dostępu do niezbędnych zasobów lub interfejsów API aplikacje w usłudze Azure AD muszą uzyskać zgodę. Istnieje wiele rodzajów zgody, które powinny być znane aplikacji, aby jej praca zakończyła się pomyślnie. Podczas definiowania uprawnień należy także znać sposoby używane przez użytkowników podczas uzyskiwania dostępu do Twojej aplikacji lub interfejsu API.

* **Statyczna zgoda użytkownika** — zachodzi automatycznie podczas [przepływu autoryzacji OAuth 2.0](v1-protocols-oauth-code.md#request-an-authorization-code) po podaniu zasobu, z którym chce współdziałać Twoja aplikacja. W scenariuszu statycznej zgody użytkownika aplikacja musi już znać wszystkie uprawnienia, które są niezbędne do skonfigurowania aplikacji w witrynie Azure Portal. Jeśli użytkownik (lub administrator, zależnie od potrzeb) nie udzielił zgody dla tej aplikacji, wtedy usługa Azure AD będzie monitować użytkownika o zgodę. 

    Dowiedz się więcej o rejestrowaniu aplikacji usługi Azure AD, która żąda dostępu do statycznego zestawu interfejsów API.
* **Dynamiczna zgoda użytkownika** — jest to funkcja dostępna w modelu aplikacji usługi Azure AD w wersji 2. W tym scenariuszu aplikacja żąda zbioru uprawnień, których potrzebuje w [przepływie autoryzacji OAuth 2.0 dla aplikacji v2](../develop/v2-permissions-and-consent.md#requesting-individual-user-consent). Jeśli użytkownik nie wyraził jeszcze zgody, w tym momencie zostanie wyświetlony monit o jej wyrażenie. [Dowiedz się więcej na temat dynamicznej zgody](./azure-ad-endpoint-comparison.md#incremental-and-dynamic-consent).

    > [!IMPORTANT]
    > Dynamiczne udzielanie zgody może być wygodne, ale stanowi duże wyzwanie w przypadku uprawnień wymagających zgody administratora, ponieważ w momencie wyrażania zgody administrator nie dysponuje informacjami dotyczącymi uprawnień. Jeśli wymagane są uprawnienia administratora lub jeśli aplikacja korzysta z zgody dynamicznej, należy zarejestrować wszystkie uprawnienia w Azure Portal (nie tylko podzbiór uprawnień, które wymagają zgody administratora). Dzięki temu administratorzy dzierżawy mogą wyrazić zgodę w imieniu wszystkich użytkowników.
  
* **Zgoda administratora** — jest wymagana, gdy aplikacja potrzebuje dostępu do niektórych uprawnień wysokiego poziomu. Zgoda administratora zapewnia, że administratorzy mają dodatkową kontrolę nad autoryzowaniem dostępu aplikacji lub użytkowników do danych organizacji wymagających szczególnych uprawnień. [Dowiedz się więcej na temat sposobu udzielania zgody administratora](../develop/v2-permissions-and-consent.md#using-the-admin-consent-endpoint).

## <a name="best-practices"></a>Najlepsze rozwiązania

### <a name="client-best-practices"></a>Najlepsze rozwiązania klientów

- Żądaj tylko tych uprawnień, których potrzebuje Twoja aplikacja. Aplikacje ze zbyt dużą liczbą uprawnień niosą ze sobą ryzyko ujawnienia danych użytkowników w przypadku naruszenia bezpieczeństwa aplikacji.
- Wybierz między uprawnieniami delegowanymi i uprawnieniami aplikacji, biorąc pod uwagę scenariusz obsługiwany przez aplikację.
    - Zawsze używaj uprawnień delegowanych, jeśli wywołanie jest wykonywane w imieniu użytkownika.
    - Uprawnień aplikacji używaj tylko wtedy, jeśli aplikacja jest nieinterakcyjna i nie wykonuje wywołań w imieniu żadnego określonego użytkownika. Uprawnienia aplikacji są wysoce uprzywilejowane i należy ich używać tylko w razie absolutnej konieczności.
- W przypadku używania aplikacji opartej na punkcie końcowym w wersji 2.0 zawsze konfiguruj uprawnienia statyczne (określone w rejestracji aplikacji) tak, aby były nadzbiorem uprawnień dynamicznych, żądanych w czasie wykonywania (są to uprawnienia określone w kodzie i wysyłane jako parametry zapytania w żądaniu autoryzacji), aby scenariusze, takie jak zgoda administratora, działały poprawnie.

### <a name="resourceapi-best-practices"></a>Najlepsze rozwiązania dotyczące zasobów/interfejsu API

- Zasoby, które udostępniają interfejsy API, powinny szczegółowo definiować uprawnienia dla chronionych przez nie danych lub akcji. Dzięki zastosowaniu tego najlepszego rozwiązania klienci nie otrzymują uprawnień do niepotrzebnych danych oraz są poinformowani, do jakich danych mają dostęp.
- Zasoby powinny w sposób jawny osobno definiować uprawnienia `Read` i `ReadWrite`.
- Zasoby powinny oznaczyć wszystkie uprawnienia umożliwiające dostęp do danych przekraczający granice użytkownika jako uprawnienia `Admin`.
- Nazwy zasobów powinny być zgodne ze wzorcem `Subject.Permission[.Modifier]`, gdzie:
  - `Subject` odpowiada typowi danych, które są dostępne
  - `Permission` odnosi się do akcji, którą użytkownik może wykonać w przypadku tych danych
  - `Modifier` jest używany opcjonalnie do opisywania specjalizacji innego uprawnienia
    
    Na przykład:
  - Mail.Read — umożliwia użytkownikom odczytywanie wiadomości e-mail.
  - Mail.ReadWrite — umożliwia użytkownikom odczytywanie i pisanie wiadomości e-mail.
  - Mail.ReadWrite.All — umożliwia administratorowi lub użytkownikom dostęp do wszystkich wiadomości w organizacji.