---
title: Przewodnik migracji biblioteki ADAL do MSAL dla systemu Android | Azure
description: Dowiedz się, jak przeprowadzić migrację aplikacji dla systemu Android biblioteki Azure Active Directory Authentication Library (ADAL) do biblioteki uwierzytelniania firmy Microsoft (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Android
ms.workload: identity
ms.date: 09/6/2019
ms.author: marsma
ms.reviewer: shoatman
ms.custom: aaddev
ms.openlocfilehash: b2a6722cfff392a18629c8bb47fad0ad5ac1a95b
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91966002"
---
# <a name="adal-to-msal-migration-guide-for-android"></a>Przewodnik migracji biblioteki ADAL do MSAL dla systemu Android

W tym artykule przedstawiono zmiany, które należy wykonać w celu przeprowadzenia migracji aplikacji, która Azure Active Directory używa biblioteki MSAL Authentication Library (ADAL) do korzystania z biblioteki uwierzytelniania firmy Microsoft.

## <a name="difference-highlights"></a>Różnice między wyróżnieniami

Biblioteka ADAL współpracuje z punktem końcowym Azure Active Directory v 1.0. Biblioteka Microsoft Authentication Library (MSAL) współpracuje z platformą tożsamości firmy Microsoft — znana wcześniej jako punkt końcowy Azure Active Directory v 2.0. Platforma tożsamości firmy Microsoft różni się od Azure Active Directory v 1.0:

Uguje
  - Tożsamość organizacji (Azure Active Directory)
  - Tożsamości nieorganizacyjne, takie jak Outlook.com, Xbox Live i tak dalej
  - (Tylko B2C) Logowanie federacyjne w usługach Google, Facebook, Twitter i Amazon

- Są zgodne ze standardami:
  - OAuth 2.0
  - OpenID Connect Connect (OIDC)

Publiczny interfejs API MSAL wprowadza ważne zmiany, w tym:

- Nowy model do uzyskiwania dostępu do tokenów:
  - Biblioteka ADAL zapewnia dostęp do tokenów za pośrednictwem klasy `AuthenticationContext` , która reprezentuje serwer. MSAL zapewnia dostęp do tokenów za pośrednictwem `PublicClientApplication` , który reprezentuje klienta. Deweloperzy klienta nie muszą tworzyć nowego `PublicClientApplication` wystąpienia dla każdego urzędu, którego potrzebują do współpracy z usługą. `PublicClientApplication`Wymagana jest tylko jedna konfiguracja.
  - Obsługa żądania tokenów dostępu przy użyciu zakresów oprócz identyfikatorów zasobów.
  - Obsługa przyrostowej zgody. Deweloperzy mogą żądać zakresów, ponieważ użytkownik uzyskuje dostęp do większej i większej funkcjonalności w aplikacji, łącznie z tymi, które nie są uwzględnione podczas rejestracji aplikacji.
  - Urzędy nie są już weryfikowane w czasie wykonywania. Zamiast tego deweloper deklaruje listę "znanych urzędów" podczas opracowywania.
- Zmiany interfejsu API tokenu:
  - W bibliotece ADAL `AcquireToken()` najpierw jest wysyłane żądanie dyskretne. Niepowodzenie oznacza, że wykonuje interaktywne żądanie. Takie zachowanie spowodowało, że niektórzy deweloperzy opierają się tylko na tym `AcquireToken` , co spowodowało nieoczekiwane wyświetlenie monitu o poświadczenia w czasie. MSAL wymaga, aby deweloperzy mogli zamierzać, kiedy użytkownik otrzyma monit o interfejs użytkownika.
    - `AcquireTokenSilent` zawsze powoduje, że żądanie dyskretne lub zakończy się niepowodzeniem.
    - `AcquireToken` zawsze powoduje żądanie, które monituje użytkownika za pośrednictwem interfejsu użytkownika.
- MSAL obsługuje logowanie z poziomu domyślnej przeglądarki lub osadzonego widoku sieci Web:
  - Domyślnie używana jest domyślna przeglądarka na urządzeniu. Pozwala to MSAL na używanie stanu uwierzytelniania (plików cookie), które mogą już być obecne dla jednego lub więcej zalogowanych kont. Jeśli stan uwierzytelniania nie jest obecny, uwierzytelnianie w trakcie autoryzacji za pomocą usługi MSAL powoduje utworzenie stanu uwierzytelniania (plików cookie) w celu skorzystania z zalet innych aplikacji sieci Web, które będą używane w tej samej przeglądarce.
- Nowy model wyjątku:
  - Wyjątki dokładniej definiują typ błędu, który wystąpił i jakie musi wykonać Deweloper, aby rozwiązać ten problem.
- MSAL obsługuje obiekty parametrów dla `AcquireToken` `AcquireTokenSilent` wywołań i.
- MSAL obsługuje konfigurację deklaratywną dla:
  - Identyfikator klienta, identyfikator URI przekierowania.
  - Osadzona przeglądarka vs
  - Wykazu
  - Ustawienia protokołu HTTP, takie jak Odczyt i limit czasu połączenia

## <a name="your-app-registration-and-migration-to-msal"></a>Rejestracja i migracja aplikacji do MSAL

Nie musisz zmieniać istniejącej rejestracji aplikacji, aby używać MSAL. Jeśli chcesz korzystać ze zbyt przyrostowej lub progresywnej zgody, może być konieczne przejrzenie rejestracji w celu zidentyfikowania określonych zakresów, które mają być przeżądane przyrostowo. Więcej informacji na temat zakresów i przyrostowej zgody znajduje się poniżej.

W przypadku rejestracji aplikacji w portalu zostanie wyświetlona karta **uprawnienia interfejsu API** . Zawiera listę interfejsów API i uprawnień (zakresów), dla których aplikacja jest obecnie skonfigurowana do żądania dostępu do programu. Pokazuje także listę nazw zakresów skojarzonych z poszczególnymi uprawnieniami interfejsu API.

### <a name="user-consent"></a>Zgoda użytkownika

Za pomocą biblioteki ADAL i punktu końcowego usługi AAD V1 użytkownik wyraża zgodę na posiadane zasoby. Dzięki MSAL i platformie tożsamości firmy Microsoft można zażądać ich przyrostowo. Poprawna zgoda jest przydatna w przypadku uprawnień, które użytkownik może rozważyć wysoki poziom uprawnień, lub w przeciwnym razie, jeśli nie podano z oczywistym wyjaśnieniem przyczyny tego uprawnienia. W bibliotece ADAL te uprawnienia mogły spowodować, że użytkownik porzucają logowanie do aplikacji.

> [!TIP]
> Zalecamy użycie przyrostowej zgody w scenariuszach, w których należy zapewnić użytkownikowi dodatkowy kontekst dotyczący tego, Dlaczego aplikacja wymaga uprawnień.

### <a name="admin-consent"></a>zgoda administratora

Administratorzy organizacji mogą wyrazić zgodę na uprawnienia aplikacji w imieniu wszystkich członków swojej organizacji. Niektóre organizacje mogą zezwalać administratorom na wyrażanie zgody na aplikacje. Zgoda administratora wymaga dołączenia wszystkich uprawnień interfejsu API i zakresów używanych przez aplikację w ramach rejestracji aplikacji.

> [!TIP]
> Mimo że możesz zażądać zakresu przy użyciu MSAL dla elementu nie dołączanego do rejestracji aplikacji, Zalecamy zaktualizowanie rejestracji aplikacji w taki sposób, aby obejmowała wszystkie zasoby i zakresy, do których użytkownik może uzyskać uprawnienia.

## <a name="migrating-from-resource-ids-to-scopes"></a>Migrowanie z identyfikatorów zasobów do zakresów

### <a name="authenticate-and-request-authorization-for-all-permissions-on-first-use"></a>Uwierzytelniaj i Żądaj autoryzacji wszystkich uprawnień przy pierwszym użyciu

Jeśli obecnie korzystasz z biblioteki ADAL i nie musisz korzystać z wyrażania zgody, najprostszym sposobem na rozpoczęcie korzystania z programu MSAL jest `acquireToken` zażądanie przy użyciu nowego `AcquireTokenParameter` obiektu i ustawienie wartości identyfikatora zasobu.

> [!CAUTION]
> Nie można ustawić zarówno zakresów, jak i identyfikatora zasobu. Próba ustawienia obu tych wartości spowoduje wystąpienie elementu `IllegalArgumentException` .

 Spowoduje to użycie tego samego zachowania w wersji v1. Podczas pierwszej interakcji zażądano wszystkich uprawnień w rejestracji aplikacji od użytkownika.

### <a name="authenticate-and-request-permissions-only-as-needed"></a>Uwierzytelnianie i żądanie uprawnień tylko w razie konieczności

Aby wypróbować użycie przyrostowej zgody, należy utworzyć listę uprawnień (zakresów) używanych przez aplikację w ramach rejestracji aplikacji i zorganizować je na dwie listy w oparciu o:

- Zakresy, które chcesz zażądać podczas pierwszej interakcji użytkownika z aplikacją podczas logowania.
- Uprawnienia, które są skojarzone z ważną funkcją aplikacji, należy również wyjaśnić użytkownikowi.

Po przydzieleniu zakresów Organizuj poszczególne listy według zasobów (API), dla których chcesz zażądać tokenu. A także inne zakresy, które użytkownik chce autoryzować w tym samym czasie.

Obiekt Parameters służący do żądania MSAL obsługuje:

- `Scope`: Lista zakresów, dla których chcesz zażądać autoryzacji i otrzymać token dostępu.
- `ExtraScopesToConsent`: Dodatkowa lista zakresów, dla których chcesz zażądać autoryzacji w trakcie żądania tokenu dostępu dla innego zasobu. Ta lista zakresów pozwala zminimalizować liczbę przypadków, w których należy zażądać autoryzacji użytkownika. Co oznacza mniejszą autoryzację użytkownika lub prośbę o zgodę.

## <a name="migrate-from-authenticationcontext-to-publicclientapplications"></a>Migrowanie z AuthenticationContext do PublicClientApplications

### <a name="constructing-publicclientapplication"></a>Konstruowanie PublicClientApplication

Jeśli używasz MSAL, tworzysz wystąpienie `PublicClientApplication` . Ten obiekt modeluje tożsamość aplikacji i służy do zgłaszania żądań do jednego lub kilku urzędów. Za pomocą tego obiektu skonfigurujesz tożsamość klienta, identyfikator URI przekierowania, domyślny urząd, czy ma być używana przeglądarka urządzeń, a także osadzony widok sieci Web, poziom dziennika i wiele więcej.

Można deklaratywnie skonfigurować ten obiekt w formacie JSON, który jest udostępniany jako plik lub magazyn jako zasób w ramach APK.

Chociaż ten obiekt nie jest klasą pojedynczą, wewnętrznie używa udostępnionych `Executors` dla żądań interaktywnych i dyskretnych.

### <a name="business-to-business"></a>Działalność biznesowa dla firm

W bibliotece ADAL każda organizacja, do której żąda się tokeny dostępu, wymaga oddzielnego wystąpienia `AuthenticationContext` . W MSAL nie jest to już wymagane. Możesz określić urząd, z którego chcesz zażądać tokenu w ramach żądania dyskretnego lub interaktywnego.

### <a name="migrate-from-authority-validation-to-known-authorities"></a>Migrowanie z weryfikacji urzędu do znanych urzędów

MSAL nie ma flagi umożliwiającej włączenie lub wyłączenie walidacji urzędu. Sprawdzanie poprawności urzędu jest funkcją w wersji ADAL i we wczesnych wersjach MSAL, które uniemożliwiają kodowi żądanie tokenów od potencjalnie złośliwego urzędu. MSAL teraz pobiera listę urzędów znanych do firmy Microsoft i scala tę listę z urzędami określonymi w konfiguracji.

> [!TIP]
> Jeśli jesteś użytkownikiem platformy Azure Business-Consumer (B2C), oznacza to, że nie trzeba już wyłączać weryfikacji urzędu. Zamiast tego należy uwzględnić wszystkie obsługiwane zasady Azure AD B2C jako urzędy w konfiguracji MSAL.

Jeśli spróbujesz użyć urzędu, który nie jest znany przez firmę Microsoft i nie jest uwzględniony w konfiguracji, zostanie wyświetlony komunikat `UnknownAuthorityException` .

### <a name="logging"></a>Rejestrowanie
Teraz można deklaratywnie skonfigurować rejestrowanie w ramach konfiguracji, w tym:

 ```
 "logging": {
    "pii_enabled": false,
    "log_level": "WARNING",
    "logcat_enabled": true
  }
  ```

## <a name="migrate-from-userinfo-to-account"></a>Migrowanie z UserInfo do konta

W bibliotece ADAL `AuthenticationResult` udostępnia `UserInfo` obiekt służący do pobierania informacji o uwierzytelnianym koncie. Termin "użytkownik", który jest przeznaczony dla agentów ludzkich lub programowych, został zastosowany w taki sposób, że sprawia, że niektóre aplikacje obsługują jednego użytkownika (bez względu na to, czy Agent ludzki lub programowy) ma wiele kont.

Weź pod uwagę konto bankowe. Może istnieć więcej niż jedno konto w więcej niż jednej instytucji finansowej. Po otwarciu konta użytkownik (użytkownik) jest wystawiony poświadczenia, takie jak karta ATM & numer PIN, który jest używany do uzyskiwania dostępu do salda, płatności rozliczanych itd. dla każdego konta. Tych poświadczeń można używać tylko w instytucji finansowej, która je wystawiła.

Analogicznie, podobnie jak konta w instytucji finansowej, do kont na platformie tożsamości firmy Microsoft uzyskuje się dostęp przy użyciu poświadczeń. Te poświadczenia są zarejestrowane w programie lub przez firmę Microsoft. Lub przez firmę Microsoft w imieniu organizacji.

W takim przypadku platforma tożsamości firmy Microsoft różni się od instytucji finansowej, ponieważ platforma tożsamości firmy Microsoft udostępnia strukturę, która umożliwia użytkownikowi korzystanie z jednego konta i skojarzonych z nim poświadczeń w celu uzyskania dostępu do zasobów należących do wielu osób i organizacji. Jest to takie samo, jak w przypadku korzystania z karty wystawionej przez jeden Bank, a jeszcze innej instytucji finansowej. Wynika to z faktu, że wszystkie zainteresowane organizacje korzystają z platformy tożsamości firmy Microsoft, która umożliwia użycie jednego konta w wielu organizacjach. Oto przykład:

Sam działa dla Contoso.com, ale zarządza maszynami wirtualnymi platformy Azure należącymi do Fabrikam.com. Aby zarządzać maszynami wirtualnymi firmy Fabrikam, musi mieć autoryzację w celu uzyskania dostępu do nich. Ten dostęp można udzielić przez dodanie konta sam do Fabrikam.com i przyznanie swojemu kontu roli, która umożliwia mu współdziałanie z maszynami wirtualnymi. Można to zrobić za pomocą Azure Portal.

Dodanie konta Contoso.com użytkownika sam jako elementu członkowskiego Fabrikam.com spowoduje utworzenie nowego rekordu w Azure Active Directory fabrikam. com dla sam. Rekord sam w Azure Active Directory jest znany jako obiekt użytkownika. W takim przypadku ten obiekt użytkownika będzie wskazywał z powrotem do obiektu użytkownika sam w Contoso.com. Obiekt użytkownika Fabrikam firmy sam jest lokalną reprezentacją sam i służy do przechowywania informacji o koncie skojarzonym z sam w kontekście Fabrikam.com. W Contoso.com, tytuł sam jest wyższym konsultantem DevOps. W Fabrikam, tytuł sam to Contractor-Virtual maszyny. W Contoso.com, sam nie jest odpowiedzialny ani autoryzowany do zarządzania maszynami wirtualnymi. W Fabrikam.com jest to jedyna funkcja zadania. Jeszcze sam ma tylko jeden zestaw poświadczeń do śledzenia, które są poświadczeniami wydanymi przez Contoso.com.

Po pomyślnym wykonaniu wywołania zobaczysz `acquireToken` odwołanie do `IAccount` obiektu, którego można użyć w późniejszych `acquireTokenSilent` żądaniach.

### <a name="imultitenantaccount"></a>IMultiTenantAccount

Jeśli masz aplikację, która uzyskuje dostęp do oświadczeń dotyczących konta z poszczególnych dzierżawców, w których konto jest reprezentowane, możesz rzutować `IAccount` obiekty na `IMultiTenantAccount` . Ten interfejs udostępnia mapę z `ITenantProfiles` identyfikatorem dzierżawy, która umożliwia dostęp do oświadczeń należących do konta w każdej dzierżawie, z której zażądano tokenu, względem bieżącego konta.

Oświadczenia w katalogu głównym `IAccount` i `IMultiTenantAccount` zawsze zawierają oświadczenia z dzierżawy głównej. Jeśli nie zgłoszono jeszcze żądania tokenu w ramach dzierżawy głównej, ta kolekcja będzie pusta.

## <a name="other-changes"></a>Inne zmiany

### <a name="use-the-new-authenticationcallback"></a>Użyj nowego AuthenticationCallback

```java
// Existing ADAL Interface
public interface AuthenticationCallback<T> {

    /**
     * This will have the token info.
     *
     * @param result returns <T>
     */
    void onSuccess(T result);

    /**
     * Sends error information. This can be user related error or server error.
     * Cancellation error is AuthenticationCancelError.
     *
     * @param exc return {@link Exception}
     */
    void onError(Exception exc);
}
```

```java
// New Interface for Interactive AcquireToken
public interface AuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException}
     */
    void onError(final MsalException exception);

    /**
     * Will be called if user cancels the flow.
     */
    void onCancel();
}

// New Interface for Silent AcquireToken
public interface SilentAuthenticationCallback {

    /**
     * Authentication finishes successfully.
     *
     * @param authenticationResult {@link IAuthenticationResult} that contains the success response.
     */
    void onSuccess(final IAuthenticationResult authenticationResult);

    /**
     * Error occurs during the authentication.
     *
     * @param exception The {@link MsalException} contains the error code, error message and cause if applicable. The exception
     *                  returned in the callback could be {@link MsalClientException}, {@link MsalServiceException} or
     *                  {@link MsalUiRequiredException}.
     */
    void onError(final MsalException exception);
}


```

## <a name="migrate-to-the-new-exceptions"></a>Migrowanie do nowych wyjątków

W bibliotece ADAL istnieje jeden typ wyjątku, `AuthenticationException` który obejmuje metodę pobierania `ADALError` wartości enum.
W programie MSAL istnieje hierarchia wyjątków, a każda z nich ma swój własny zestaw skojarzonych określonych kodów błędów.

| Wyjątek                                        | Opis                                                         |
|--------------------------------------------------|---------------------------------------------------------------------|
| `MsalException`                                  | Domyślny sprawdzony wyjątek zgłoszony przez MSAL.                           |
| `MsalClientException`                            | Zgłaszany, jeśli błąd jest po stronie klienta.                                 |
| `MsalArgumentException`                          | Zgłaszany, jeśli co najmniej jeden argument wejściowy jest nieprawidłowy.                 |
| `MsalServiceException`                           | Zgłaszany, jeśli błąd jest po stronie serwera.                                 |
| `MsalUserCancelException`                        | Zgłaszany, jeśli użytkownik anulował przepływ uwierzytelniania.                |
| `MsalUiRequiredException`                        | Zgłaszany, jeśli nie można odświeżyć tokenu w trybie dyskretnym.                    |
| `MsalDeclinedScopeException`                     | Zgłaszany, jeśli co najmniej jeden żądany zakres został odrzucony przez serwer. |
| `MsalIntuneAppProtectionPolicyRequiredException` | Zgłaszany, jeśli dla zasobu włączono zasady ochrony MAMCA.         |

### <a name="adalerror-to-msalexception-errorcode"></a>ADALError do MsalException ErrorCode

### <a name="adal-logging-to-msal-logging"></a>Rejestrowanie ADAL w rejestrowaniu MSAL

```java
// Legacy Interface
    StringBuilder logs = new StringBuilder();
    Logger.getInstance().setExternalLogger(new ILogger() {
            @Override
            public void Log(String tag, String message, String additionalMessage, LogLevel logLevel, ADALError errorCode) {
                logs.append(message).append('\n');
            }
        });
```

```java
// New interface
  StringBuilder logs = new StringBuilder();
  Logger.getInstance().setExternalLogger(new ILoggerCallback() {
            @Override
            public void log(String tag, Logger.LogLevel logLevel, String message, boolean containsPII) {
                logs.append(message).append('\n');
            }
        });

// New Log Levels:
public enum LogLevel
{
        /**
         * Error level logging.
         */
        ERROR,
        /**
         * Warning level logging.
         */
        WARNING,
        /**
         * Info level logging.
         */
        INFO,
        /**
         * Verbose level logging.
         */
        VERBOSE
}
```
