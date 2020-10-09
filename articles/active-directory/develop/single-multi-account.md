---
title: Aplikacje klienckie o pojedynczym i wielu kontach Azure
description: Omówienie jednej i wielu publicznych aplikacji klienta.
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/26/2019
ms.author: shoatman
ms.custom: aaddev
ms.reviewer: shoatman
ms.openlocfilehash: 89a383aabf3487a0938604bc28ddb06c0541d13e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80881354"
---
# <a name="single-and-multiple-account-public-client-apps"></a>Aplikacje publiczne klienta z jednym i wieloma kontami

Ten artykuł ułatwi zapoznanie się z typami używanymi w publicznych aplikacjach klienckich z jednym kontem i wieloma kontami z fokusem na jednym koncie. 

Biblioteka Azure Active Directory Authentication Library (ADAL) modeluje serwer.  Biblioteka Microsoft Authentication Library (MSAL) modeluje aplikację kliencką.  Większość aplikacji systemu Android jest traktowana jako klienci publiczni. Klient publiczny to aplikacja, która nie może bezpiecznie zachować klucza tajnego.  

MSAL określa powierzchnię interfejsu API, `PublicClientApplication` Aby uprościć i wyjaśnić środowisko programistyczne dla aplikacji, które zezwalają na użycie tylko jednego konta jednocześnie. `PublicClientApplication` jest podklasą przez `SingleAccountPublicClientApplication` i `MultipleAccountPublicClientApplication` .  Na poniższym diagramie przedstawiono relację między tymi klasami.

![Diagram klas UML SingleAccountPublicClientApplication](./media/single-multi-account/single-and-multiple-account.png)

## <a name="single-account-public-client-application"></a>Publiczna aplikacja kliencka z jednym kontem

`SingleAccountPublicClientApplication`Klasa umożliwia utworzenie aplikacji opartej na MSAL, która umożliwia tylko Logowanie jednokrotne w danym momencie. `SingleAccountPublicClientApplication` różni `PublicClientApplication` się od programu w następujący sposób:

- MSAL śledzi obecnie zalogowane konto.
  - Jeśli aplikacja korzysta z brokera (domyślnego podczas Azure Portal rejestracji aplikacji) i jest zainstalowana na urządzeniu, na którym znajduje się Broker, MSAL sprawdzi, czy konto jest nadal dostępne na urządzeniu.
- `signIn` umożliwia logowanie się na koncie jawnie i oddzielnie od zakresów żądania.
- `acquireTokenSilent` nie wymaga parametru konta.  Jeśli podasz konto, a wprowadzone konto nie jest zgodne z bieżącym kontem śledzonym przez MSAL, `MsalClientException` zostanie zgłoszone.
- `acquireToken` nie zezwala użytkownikowi na przełączanie kont. Jeśli użytkownik próbuje przełączyć się na inne konto, zostanie zgłoszony wyjątek.
- `getCurrentAccount` zwraca obiekt wynikowy, który zawiera następujące elementy:
  - Wartość logiczna wskazująca, czy konto zostało zmienione. Konto może zostać zmienione w wyniku usunięcia z urządzenia, na przykład.
  - Poprzednie konto. Jest to przydatne, jeśli trzeba wykonać wszystkie lokalne czyszczenie danych, gdy konto zostanie usunięte z urządzenia lub gdy nowe konto jest zalogowane.
  - CurrentAccount.
- `signOut` usuwa z urządzenia wszystkie tokeny skojarzone z klientem.  

Gdy na urządzeniu jest zainstalowany Broker uwierzytelniania systemu Android, taki jak Microsoft Authenticator lub Intune — Portal firmy, a Twoja aplikacja jest skonfigurowana do korzystania z brokera, `signOut` nie będzie można usunąć tego konta z urządzenia.

## <a name="single-account-scenario"></a>Scenariusz pojedynczego konta

Poniższy pseudo kod ilustruje użycie `SingleAccountPublicClientApplication` .

```java
// Construct Single Account Public Client Application
ISingleAccountPublicClientApplication app = PublicClientApplication.createSingleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will get a UI prompt before getting the token.
app.signIn(getActivity(), scopes, new AuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
    }
);

// Load Account Specific Data
getDataForAccount(account);

// Get Current Account
ICurrentAccountResult currentAccountResult = app.getCurrentAccount();
if (currentAccountResult.didAccountChange())
{
    // Account Changed Clear existing account data
    clearDataForAccount(currentAccountResult.getPriorAccount());
    mAccount = currentAccountResult.getCurrentAccount();
    if (account != null)
    {
        //load data for new account
        getDataForAccount(account);
    }
}

// Sign out
if (app.signOut())
{
    clearDataForAccount(mAccount);
    mAccount = null;
}
```

## <a name="multiple-account-public-client-application"></a>Publiczna aplikacja kliencka z wieloma kontami

`MultipleAccountPublicClientApplication`Klasa służy do tworzenia aplikacji opartych na MSAL, które zezwalają na logowanie wielu kont w tym samym czasie. Umożliwia to pobieranie, Dodawanie i usuwanie kont w następujący sposób:

### <a name="add-an-account"></a>Dodaj konto

Użyj co najmniej jednego konta w aplikacji, wywołując `acquireToken` jeden lub więcej razy.  

### <a name="get-accounts"></a>Pobierz konta

- Wywołanie `getAccount` w celu uzyskania określonego konta.
- Wywołaj, `getAccounts` Aby uzyskać listę kont, które są obecnie znane dla aplikacji.

Aplikacja nie będzie mogła wyliczyć wszystkich kont platformy tożsamości firmy Microsoft na urządzeniu znanym przez aplikację brokera. Można tylko wyliczać konta, które były używane przez aplikację.  Te funkcje nie będą zwracały kont, które zostały usunięte z urządzenia.

### <a name="remove-an-account"></a>Usuwanie konta

Usuń konto, dzwoniąc `removeAccount` z identyfikatorem konta.

Jeśli aplikacja jest skonfigurowana do korzystania z brokera, a na urządzeniu jest zainstalowany Broker, konto nie zostanie usunięte z brokera podczas wywoływania `removeAccount` .  Tylko tokeny skojarzone z klientem są usuwane.

## <a name="multiple-account-scenario"></a>Scenariusz z wieloma kontami

Następujący pseudo kod pokazuje, jak utworzyć aplikację z wieloma kontami, wyświetlić listę kont na urządzeniu i uzyskać tokeny.

```java
// Construct Multiple Account Public Client Application
IMultipleAccountPublicClientApplication app = PublicClientApplication.createMultipleAccountPublicClientApplication(getApplicationContext(), R.raw.msal_config);

String[] scopes = {"User.Read"};
IAccount mAccount = null;

// Acquire a token interactively
// The user will be required to interact with a UI to obtain a token
app.acquireToken(getActivity(), scopes, new AuthenticationCallback()
 {

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }

        @Override
        public void onCancel()
        {
        }
 });

...

// Get the default authority
String authority = app.getConfiguration().getDefaultAuthority().getAuthorityURL().toString();

// Get a list of accounts on the device
List<IAccount> accounts = app.getAccounts();

// Pick an account to obtain a token from without prompting the user to sign in
IAccount selectedAccount = accounts.get(0);

// Get a token without prompting the user
app.acquireTokenSilentAsync(scopes, selectedAccount, authority, new SilentAuthenticationCallback()
{

        @Override
        public void onSuccess(IAuthenticationResult authenticationResult) 
        {
            mAccount = authenticationResult.getAccount();
        }

        @Override
        public void onError(MsalException exception)
        {
        }
});
```
