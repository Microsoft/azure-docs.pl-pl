---
title: Logowanie jednokrotne między aplikacjami ADAL & MSAL (iOS/macOS) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak udostępniać Logowanie jednokrotne między aplikacjami ADAL i MSAL
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2019
ms.author: marsma
ms.reviewer: ''
ms.custom: aaddev
ms.openlocfilehash: 396e9cfeace8791a59dec4a9c9c7203212f57304
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99584251"
---
# <a name="how-to-sso-between-adal-and-msal-apps-on-macos-and-ios"></a>Instrukcje: Logowanie jednokrotne między aplikacjami ADAL i MSAL w systemach macOS i iOS

Biblioteka Microsoft Authentication Library (MSAL) dla systemu iOS może współdzielić stan logowania jednokrotnego z elementem [ADAL-C](https://github.com/AzureAD/azure-activedirectory-library-for-objc) między aplikacjami. Aplikacje można migrować do MSAL w swoim własnym tempie, dzięki czemu użytkownicy nadal będą mogli korzystać z logowania jednokrotnego między aplikacjami — nawet przy użyciu kombinacji aplikacji biblioteki ADAL i MSAL.

Jeśli szukasz wskazówek dotyczących konfigurowania logowania jednokrotnego między aplikacjami przy użyciu zestawu SDK MSAL, zobacz [ciche logowanie JEDNOkrotne między wieloma aplikacjami](single-sign-on-macos-ios.md#silent-sso-between-apps). Ten artykuł koncentruje się na rejestracji jednokrotnej między ADAL i MSAL.

Informacje dotyczące implementowania logowania jednokrotnego są zależne od używanej wersji biblioteki ADAL.

## <a name="adal-27x"></a>Biblioteka ADAL 2.7. x

W tej sekcji opisano różnice między usługą MSAL a ADAL 2.7. x

### <a name="cache-format"></a>Format pamięci podręcznej

Biblioteka ADAL 2.7. x może odczytać format pamięci podręcznej MSAL. Nie musisz wykonywać żadnych specjalnych czynności związanych z LOGOWANIEm jednokrotnym dla wielu aplikacji w wersji ADAL 2.7. x. Należy jednak pamiętać o różnicach w identyfikatorach kont obsługiwanych przez te dwie biblioteki.

### <a name="account-identifier-differences"></a>Różnice identyfikatorów kont

MSAL i ADAL używają różnych identyfikatorów kont. Biblioteka ADAL używa nazwy UPN jako podstawowego identyfikatora konta. MSAL korzysta z niedrukowalnego identyfikatora konta, który jest oparty na IDENTYFIKATORze obiektu i IDENTYFIKATORze dzierżawy dla kont usługi AAD, a następnie w `sub` przypadku innych typów kont.

Gdy otrzymasz `MSALAccount` obiekt w wyniku MSAL, zawiera on identyfikator konta we `identifier` właściwości. Aplikacja powinna używać tego identyfikatora dla kolejnych żądań dyskretnych.

Oprócz `identifier` , `MSALAccount` obiekt zawiera identyfikator, który został wywołany `username` . Który jest tłumaczony na `userId` w bibliotece ADAL. `username` nie jest uznawany za unikatowy identyfikator i może się zmienić w dowolnym czasie, więc należy go używać tylko w scenariuszach zgodności z poprzednimi wersjami z biblioteką ADAL. MSAL obsługuje zapytania pamięci podręcznej przy użyciu albo `username` lub `identifier` , gdzie `identifier` jest zalecane wykonywanie zapytań.

Poniższa tabela zawiera podsumowanie różnic identyfikatorów kont między bibliotekami ADAL i MSAL:

| Identyfikator konta                | BIBLIOTEKA MSAL                                                         | Biblioteka ADAL 2.7. x      | Starsza wersja ADAL (przed ADAL 2.7. x) |
| --------------------------------- | ------------------------------------------------------------ | --------------- | ------------------------------ |
| Identyfikator, który ma być odtwarzany            | `username`                                                   | `userId`        | `userId`                       |
| unikatowy identyfikator, który nie jest odtwarzany | `identifier`                                                 | `homeAccountId` | Nie dotyczy                            |
| Brak znanego identyfikatora konta               | Wykonywanie zapytania dotyczącego wszystkich kont za poorednictwem `allAccounts:` interfejsu API w `MSALPublicClientApplication` | NIE DOTYCZY             | NIE DOTYCZY                            |

Jest to `MSALAccount` interfejs udostępniający te identyfikatory:

```objc
@protocol MSALAccount <NSObject>

/*!
 Displayable user identifier. Can be used for UI and backward compatibility with ADAL.
 */
@property (readonly, nullable) NSString *username;

/*!
 Unique identifier for the account.
 Save this for account lookups from cache at a later point.
 */
@property (readonly, nullable) NSString *identifier;

/*!
 Host part of the authority string used for authentication based on the issuer identifier.
 */
@property (readonly, nonnull) NSString *environment;

/*!
 ID token claims for the account.
 Can be used to read additional information about the account, e.g. name
 Will only be returned if there has been an id token issued for the client Id for the account's source tenant.
 */
@property (readonly, nullable) NSDictionary<NSString *, NSString *> *accountClaims;

@end
```

### <a name="sso-from-msal-to-adal"></a>Logowanie jednokrotne z usługi MSAL do biblioteki ADAL

Jeśli masz aplikację MSAL i aplikację ADAL, a użytkownik po raz pierwszy zaloguje się do aplikacji opartej na usłudze MSAL, możesz pobrać Logowanie jednokrotne w aplikacji ADAL przez zapisanie `username` z `MSALAccount` obiektu i przekazanie go do aplikacji opartej na bibliotece ADAL jako `userId` . Biblioteki ADAL mogą następnie znaleźć informacje o koncie dyskretnie przy użyciu `acquireTokenSilentWithResource:clientId:redirectUri:userId:completionBlock:` interfejsu API.

### <a name="sso-from-adal-to-msal"></a>Logowanie jednokrotne z biblioteki ADAL do MSAL

Jeśli masz aplikację MSAL i aplikację ADAL, a użytkownik najpierw zaloguje się do aplikacji opartej na bibliotece ADAL, można użyć identyfikatorów użytkowników ADAL do wyszukiwania kont w MSAL. Dotyczy to również migracji z biblioteki ADAL do MSAL.

#### <a name="adals-homeaccountid"></a>HomeAccountId biblioteki ADAL

Biblioteka ADAL 2.7. x zwraca wartość `homeAccountId` w `ADUserInformation` obiekcie w wyniku za pośrednictwem tej właściwości:

```objc
/*! Unique AAD account identifier across tenants based on user's home OID/home tenantId. */
@property (readonly) NSString *homeAccountId;
```

`homeAccountId` w bibliotece ADAL jest równoważne `identifier` w MSAL. Ten identyfikator można zapisać do użycia w programie MSAL na potrzeby wyszukiwania kont przy użyciu `accountForIdentifier:error:` interfejsu API.

#### <a name="adals-userid"></a>Biblioteka ADAL `userId`

Jeśli `homeAccountId` Funkcja jest niedostępna lub masz tylko możliwy do odtworzenia identyfikator, można użyć biblioteki ADAL `userId` do przeszukania konta w MSAL.

W programie MSAL najpierw wyszukać konto w usłudze `username` lub `identifier` . Zawsze używaj `identifier` do wykonywania zapytań, jeśli masz, i używaj tylko `username` jako Fallback. Jeśli konto zostanie znalezione, użyj konta w `acquireTokenSilent` wywołaniach.

Cel-C:

```objc
NSString *msalIdentifier = @"previously.saved.msal.account.id";
MSALAccount *account = nil;
    
if (msalIdentifier)
{
    // If you have MSAL account id returned either from MSAL as identifier or ADAL as homeAccountId, use it
    account = [application accountForIdentifier:@"my.account.id.here" error:nil];
}
else
{
    // Fallback to ADAL userId for migration
    account = [application accountForUsername:@"adal.user.id" error:nil];
}
    
if (!account)
{
  // Account not found.
  return;
}

MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Adres

```swift
        
let msalIdentifier: String?
var account: MSALAccount
        
do {
  if let msalIdentifier = msalIdentifier {
    account = try application.account(forIdentifier: msalIdentifier)
  }
  else {
    account = try application.account(forUsername: "adal.user.id") 
  }
             
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)          
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result
  }  
} catch let error as NSError {
  // handle error or account not found
}
```



Obsługiwane przez MSAL interfejsy API wyszukiwania kont:

```objc
/*!
 Returns account for the given account identifier (received from an account object returned in a previous acquireToken call)
 
 @param  error      The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
 */
- (nullable MSALAccount *)accountForIdentifier:(nonnull NSString *)identifier
                                         error:(NSError * _Nullable __autoreleasing * _Nullable)error;
    
/*!
Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)
    
@param  username    The displayable value in UserPrincipleName(UPN) format
@param  error       The error that occurred trying to get the accounts, if any, if you're
                    not interested in the specific error pass in nil.
*/
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

## <a name="adal-2x-266"></a>ADAL 2. x-2.6.6

W tej sekcji opisano różnice między elementami MSAL i ADAL 2. x-2.6.6.

Starsze wersje ADAL nie obsługują natywnie formatu pamięci podręcznej MSAL. Aby jednak zapewnić bezproblemową migrację z biblioteki ADAL do MSAL, MSAL może odczytać starszy format pamięci podręcznej ADAL bez monitowania o podanie poświadczeń użytkownika.

Ponieważ usługa `homeAccountId` jest niedostępna w starszych wersjach ADAL, należy wyszukać konta przy użyciu `username` :

```objc
/*!
 Returns account for for the given username (received from an account object returned in a previous acquireToken call or ADAL)

 @param  username    The displayable value in UserPrincipleName(UPN) format
 @param  error       The error that occurred trying to get the accounts, if any.  If you're not interested in the specific error pass in nil.
 */
- (MSALAccount *)accountForUsername:(NSString *)username
                              error:(NSError * __autoreleasing *)error;
```

Na przykład:

Cel-C:


```objc
MSALAccount *account = [application accountForUsername:@"adal.user.id" error:nil];;
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:account];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Adres

```swift
do {
  let account = try application.account(forUsername: "adal.user.id")          
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: account)
  application.acquireTokenSilent(with: silentParameters) { 
    (result: MSALResult?, error: Error?) in
    // handle result
  }   
} catch let error as NSError { 
  // handle error or account not found
}
```



Alternatywnie można odczytać wszystkie konta, co spowoduje również odczytanie informacji o koncie z biblioteki ADAL:

Cel-C:

```objc
NSArray *accounts = [application allAccounts:nil];
    
if ([accounts count] == 0)
{
  // No account found.
  return; 
}
if ([accounts count] > 1)
{
  // You might want to display an account picker to user in actual application
  // For this sample we assume there's only ever one account in cache
  return;
}
    ``
MSALSilentTokenParameters *silentParameters = [[MSALSilentTokenParameters alloc] initWithScopes:@[@"user.read"] account:accounts[0]];
[application acquireTokenSilentWithParameters:silentParameters completionBlock:completionBlock];
```

Adres

```swift
      
do {
  let accounts = try application.allAccounts()
  if accounts.count == 0 {
    // No account found.
    return
  }
  if accounts.count > 1 {
    // You might want to display an account picker to user in actual application
    // For this sample we assume there's only ever one account in cache
    return
  }
  
  let silentParameters = MSALSilentTokenParameters(scopes: ["user.read"], account: accounts[0])
  application.acquireTokenSilent(with: silentParameters) {
    (result: MSALResult?, error: Error?) in
    // handle result or error  
  }  
} catch let error as NSError { 
  // handle error
}
```



## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na temat [przepływów uwierzytelniania i scenariuszy aplikacji](authentication-flows-app-scenarios.md)
