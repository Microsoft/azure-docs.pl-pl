---
title: Konfiguracja aplikacji klienckiej (MSAL) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o opcjach konfiguracji publicznych aplikacji klienckich i poufnych przy użyciu biblioteki uwierzytelniania firmy Microsoft (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/27/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: b4595a63613afa3c6fef2fa2a85647d8b70b1388
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534469"
---
# <a name="application-configuration-options"></a>Opcje konfiguracji aplikacji

W kodzie można zainicjować nową publiczną lub poufną aplikację kliencką (lub agenta użytkownika dla msal.js) do uwierzytelniania i uzyskiwania tokenów. Podczas inicjowania aplikacji klienckiej w bibliotece uwierzytelniania Firmy Microsoft (MSAL) można ustawić wiele opcji konfiguracji. Opcje te dzielą się na dwie grupy:

- Opcje rejestracji, w tym:
    - [Urząd](#authority) (składający się z [wystąpienia](#cloud-instance) dostawcy tożsamości i [grupy odbiorców](#application-audience) logowania dla aplikacji i ewentualnie identyfikatora dzierżawy).
    - [Identyfikator klienta](#client-id).
    - [Przekieruj identyfikator URI](#redirect-uri).
    - [Klucz tajny klienta](#client-secret) (dla poufnych aplikacji klienckich).
- [Opcje rejestrowania,](#logging)w tym poziom dziennika, kontrola danych osobowych i nazwa składnika za pomocą biblioteki.

## <a name="authority"></a>Urząd

Urząd jest adresem URL, który wskazuje katalog, z których msal może żądać tokenów. Wspólne organy to:

- https\://login.microsoftonline.com/\<dzierżawy\>/, gdzie &lt;dzierżawa&gt; jest identyfikatorem dzierżawy dzierżawy usługi Azure Active Directory (Azure AD) dzierżawy lub domeny skojarzonej z tą dzierżawą usługi Azure AD. Służy tylko do logowania użytkowników określonej organizacji.
- https\://login.microsoftonline.com/common/. Służy do logowania użytkowników za pomocą kont służbowych i szkolnych lub osobistych kont Microsoft.
- https\://login.microsoftonline.com/organizations/. Służy do logowania użytkowników za pomocą kont służbowych i szkolnych.
- https\://login.microsoftonline.com/consumers/. Służy do logowania użytkowników przy użyciu tylko osobistych kont Microsoft (dawniej nazywanych kontami identyfikatorów Windows Live).

Ustawienie urzędu musi być zgodne z tym, co jest zadeklarowane w portalu rejestracji aplikacji.

Adres URL urzędu składa się z wystąpienia i odbiorców.

Organ może być:
- Urząd chmury usługi Azure AD.
- Urząd usługi Azure AD B2C. Zobacz [szczegóły B2C](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics).
- Urząd Federacyjne usługi Active Directory (AD FS). Zobacz [ad fs wsparcie](https://aka.ms/msal-net-adfs-support).

Urzędy chmury usługi Azure AD mają dwie części:
- *Wystąpienie* dostawcy tożsamości
- Grupa *odbiorców* logowania w aplikacji

Wystąpienie i odbiorców mogą być łączone i dostarczane jako adres URL urzędu. W wersjach MSAL.NET wcześniej niż MSAL 3. *x*, trzeba było samodzielnie skomponować uprawnienia, w oparciu o chmurę, na którą chcesz kierować reklamy i odbiorców logowania.  Na tym diagramie przedstawiono sposób tworzenia adresu URL urzędu:

![Jak składa się adres URL urzędu](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Wystąpienie w chmurze

*Wystąpienie* służy do określenia, czy aplikacja podpisuje użytkowników z chmury publicznej platformy Azure lub z chmur krajowych. Za pomocą msal w kodzie, można ustawić wystąpienie chmury platformy Azure przy użyciu wyliczenia lub przekazując adres URL do [wystąpienia chmury krajowej](authentication-national-cloud.md#azure-ad-authentication-endpoints) jako `Instance` członka (jeśli go znasz).

MSAL.NET zda jawny wyjątek, jeśli oba `Instance` i `AzureCloudInstance` są określone.

Jeśli nie określisz wystąpienia, aplikacja będzie kierowana na wystąpienie chmury `https://login.onmicrosoftonline.com`publicznej platformy Azure (wystąpienie adresu URL).

## <a name="application-audience"></a>Odbiorcy aplikacji

Grupa odbiorców logowania zależy od potrzeb biznesowych aplikacji:
- Jeśli jesteś deweloperem linii biznesowej (LOB), prawdopodobnie stworzysz aplikację z jedną dzierżawą, która będzie używana tylko w Twojej organizacji. W takim przypadku należy określić organizację, albo przez jej identyfikator dzierżawy (identyfikator wystąpienia usługi Azure AD) lub przez nazwę domeny skojarzone z wystąpieniem usługi Azure AD.
- Jeśli jesteś niezależną instytucją żytą, możesz zalogować użytkowników za pomocą ich kont służbowych i szkolnych w dowolnej organizacji lub w niektórych organizacjach (aplikacja wielodostępna). Ale możesz też chcieć, aby użytkownicy logowali się za pomocą swoich osobistych kont Microsoft.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>Jak określić grupę odbiorców w kodzie/konfiguracji

Korzystając z usługi MSAL w kodzie, można określić odbiorców przy użyciu jednej z następujących wartości:
- Wyliczenie odbiorców urzędu usługi Azure AD
- Identyfikator dzierżawy, który może być:
  - Identyfikator GUID (identyfikator wystąpienia usługi Azure AD) dla aplikacji z jedną dzierżawą
  - Nazwa domeny skojarzona z wystąpieniem usługi Azure AD (również dla aplikacji z jedną dzierżawą)
- Jeden z tych symboli zastępczych jako identyfikator dzierżawy zamiast wyliczenia odbiorców urzędu usługi Azure AD:
    - `organizations`dla aplikacji wielodostępne
    - `consumers`aby zalogować się tylko za pomocą ich kont osobistych
    - `common`aby zalogować użytkowników za pomocą swoich kont służbowych i szkolnych lub osobistych kont Microsoft

MSAL zda znaczący wyjątek, jeśli określisz zarówno odbiorców urzędu usługi Azure AD i identyfikator dzierżawy.

Jeśli nie określisz grupy odbiorców, twoja aplikacja będzie kierowana na usługę Azure AD i osobiste konta Microsoft jako odbiorców. (Oznacza to, że będzie `common` zachowywać się tak, jakby zostały określone.)

### <a name="effective-audience"></a>Skuteczna publiczność

Skuteczna grupa odbiorców aplikacji będzie minimalna (jeśli jest przecięcie) odbiorców ustawionych w aplikacji i odbiorców określonych w rejestracji aplikacji. W rzeczywistości środowisko [rejestracji aplikacji](https://aka.ms/appregistrations) pozwala określić odbiorców (typy obsługiwanych kont) dla aplikacji. Aby uzyskać więcej informacji, zobacz [Szybki start: Rejestrowanie aplikacji na platformie tożsamości firmy Microsoft](quickstart-register-app.md).

Obecnie jedynym sposobem, aby uzyskać aplikację do logowania użytkowników tylko osobistych kont Microsoft jest skonfigurowanie obu tych ustawień:
- Ustaw odbiorców rejestracji `Work and school accounts and personal accounts`aplikacji na .
- Ustaw odbiorców w kodzie/konfiguracji `AadAuthorityAudience.PersonalMicrosoftAccount` `TenantID` na (lub ="konsumenci").

## <a name="client-id"></a>Identyfikator klienta

Identyfikator klienta to unikatowy identyfikator aplikacji (klienta) przypisany do aplikacji przez usługę Azure AD podczas rejestracji aplikacji.

## <a name="redirect-uri"></a>Identyfikator URI przekierowania

Identyfikator URI przekierowania jest identyfikatorem URI, do który dostawca tożsamości wyśle tokeny zabezpieczające z powrotem.

### <a name="redirect-uri-for-public-client-apps"></a>Przekierowywanie identyfikatora URI dla publicznych aplikacji klienckich

Jeśli jesteś deweloperem aplikacji klienckiej, który korzysta z usługi MSAL:
- Chcesz używać `.WithDefaultRedirectUri()` w aplikacjach komputerowych lub platformach uniwersalnych systemu Windows (MSAL.NET 4.1+). Ta metoda ustawi właściwość przekierowania uri aplikacji klienta publicznego do domyślnego zalecanego przekierowania uri dla publicznych aplikacji klienckich.

  Platforma  | Identyfikator URI przekierowania
  ---------  | --------------
  Aplikacja komputerowa (.NET FW) | `https://login.microsoftonline.com/common/oauth2/nativeclient`
  Platforma UWP | wartości `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()`. Umożliwia to SSO z przeglądarką, ustawiając wartość na wynik WebAuthenticationBroker.GetCurrentApplicationCallbackUri(), które należy zarejestrować
  .NET Core | `https://localhost`. Dzięki temu użytkownik może używać przeglądarki systemowej do uwierzytelniania interaktywnego, ponieważ program .NET Core nie ma obecnie interfejsu użytkownika dla osadzonego widoku sieci Web.

- Nie musisz dodawać identyfikatora URI przekierowania, jeśli budujesz aplikację platformy Xamarin android i iOS, która nie `msal{ClientId}://auth` obsługuje brokera (identyfikator URI przekierowania jest automatycznie ustawiany na system Xamarin Android i iOS

- Należy skonfigurować identyfikator URI przekierowania w [rejestracjach aplikacji:](https://aka.ms/appregistrations)

   ![Przekierowywanie identyfikatora URI w rejestracjach aplikacji](media/msal-client-application-configuration/redirect-uri.png)

Można zastąpić przekierowanie identyfikatora URI `RedirectUri` przy użyciu właściwości (na przykład, jeśli używasz brokerów). Oto kilka przykładów przekierowania identyfikatorów URI dla tego scenariusza:

- `RedirectUriOnAndroid`= "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos`= $"msauth. {Bundle.ID}://auth";

Aby uzyskać dodatkowe szczegółowe informacje na temat systemu iOS, zobacz [Migrowanie aplikacji systemu iOS korzystających z funkcji Microsoft Authenticator z ADAL.NET do MSAL.NET](msal-net-migration-ios-broker.md) i korzystanie z funkcji [brokera w systemie iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS).
Aby uzyskać dodatkowe informacje na temat [Androida, zobacz Brokered auth w Systemie Android](brokered-auth.md).

### <a name="redirect-uri-for-confidential-client-apps"></a>Przekierowywanie identyfikatora URI dla poufnych aplikacji klienckich

W przypadku aplikacji sieci web identyfikator URI (lub identyfikator URI odpowiedzi) jest identyfikatorem URI używanym przez usługę Azure AD do wysyłania tokenu z powrotem do aplikacji. Ten identyfikator URI może być adresem URL interfejsu API aplikacji sieci web/sieci Web, jeśli jedną z nich jest aplikacja poufna. Identyfikator URI przekierowania musi być zarejestrowany w rejestracji aplikacji. Ta rejestracja jest szczególnie ważna podczas wdrażania aplikacji, która została początkowo przetestowana lokalnie. Następnie należy dodać adres URL odpowiedzi wdrożonej aplikacji w portalu rejestracji aplikacji.

W przypadku aplikacji demonów nie trzeba określać identyfikatora URI przekierowania.

## <a name="client-secret"></a>Klucz tajny klienta

Ta opcja określa klucz tajny klienta dla poufnej aplikacji klienckiej. Ten klucz tajny (hasło aplikacji) jest dostarczany przez portal rejestracji aplikacji lub dostarczany do usługi Azure AD podczas rejestracji aplikacji w programie PowerShell AzureAD, PowerShell AzureRM lub platformie Azure CLI.

## <a name="logging"></a>Rejestrowanie

Inne opcje konfiguracji umożliwiają rejestrowanie i rozwiązywanie problemów. Zobacz [rejestrowanie](msal-logging.md) artykułu, aby uzyskać szczegółowe informacje na temat korzystania z nich.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [tworzeniem wystąpienia aplikacji klienckich przy użyciu MSAL.NET](msal-net-initializing-client-applications.md).
Dowiedz się więcej o [tworzeniem wystąpienia aplikacji klienckich przy użyciu pliku MSAL.js](msal-js-initializing-client-applications.md).
