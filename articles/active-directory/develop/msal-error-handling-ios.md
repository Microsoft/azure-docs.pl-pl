---
title: Obsługa błędów i wyjątków w bibliotece MSAL.js dla systemu iOS/macOS
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak obsługiwać błędy i wyjątki, wyzwania dotyczące oświadczeń dostępu warunkowego i ponawianie prób w MSAL dla aplikacji iOS/macOS.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2020
ms.author: marsma
ms.reviewer: saeeda, oldalton
ms.custom: aaddev
ms.openlocfilehash: f7f2abadb7586e1b19168eb46a54bad53caa05cc
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761107"
---
# <a name="handle-errors-and-exceptions-in-msal-for-iosmacos"></a>Obsługa błędów i wyjątków w bibliotece MSAL.js dla systemu iOS/macOS

[!INCLUDE [Active directory error handling introduction](../../../includes/active-directory-develop-error-handling-introduction.md)]

## <a name="error-handling-in-msal-for-iosmacos"></a>Obsługa błędów w MSAL dla systemu iOS/macOS

Pełna lista MSAL dla błędów systemu iOS i macOS jest wymieniona w [wyliczeniu MSALError](https://github.com/AzureAD/microsoft-authentication-library-for-objc/blob/master/MSAL/src/public/MSALError.h#L128).

Wszystkie błędy wygenerowane przez MSAL są zwracane z `MSALErrorDomain` domeną.

W przypadku błędów systemowych MSAL zwraca oryginalny `NSError` z interfejsu API systemu. Na przykład jeśli pozyskiwanie tokenu nie powiedzie się z powodu braku łączności sieciowej, MSAL zwraca błąd w `NSURLErrorDomain` domenie i `NSURLErrorNotConnectedToInternet` kodzie.

Zalecamy, aby obsłużyć co najmniej dwa następujące błędy MSAL po stronie klienta:

- `MSALErrorInteractionRequired`: Użytkownik musi wykonać żądanie interaktywne. Istnieje wiele warunków, które mogą prowadzić do tego błędu, takich jak wygasła sesja uwierzytelniania lub konieczność dodatkowych wymagań dotyczących uwierzytelniania. Wywołaj interfejs API pozyskiwania tokenów MSAL Interactive do odzyskania. 

- `MSALErrorServerDeclinedScopes`: Niektóre lub wszystkie zakresy zostały odrzucone. Zdecyduj, czy chcesz kontynuować z przyznanymi zakresami, czy zatrzymać proces logowania.

> [!NOTE]
> `MSALInternalError`Wyliczenie powinno być używane tylko do celów referencyjnych i debugowania. Nie należy próbować automatycznie obsłużyć tych błędów w czasie wykonywania. Jeśli w aplikacji wystąpią jakieś błędy, które znajdują się w obszarze, warto wyświetlić ogólny komunikat z informacjami o tym `MSALInternalError` , co się stało.

Na przykład `MSALInternalErrorBrokerResponseNotReceived` oznacza, że użytkownik nie wykonał uwierzytelniania i został ręcznie zwrócony do aplikacji. W takim przypadku aplikacja powinna wyświetlić ogólny komunikat o błędzie z informacją o tym, że uwierzytelnianie nie zostało ukończone i zasugeruj, że próbują ponownie przeprowadzić uwierzytelnianie.

Poniższy przykładowy kod języka C przedstawia najlepsze rozwiązania dotyczące obsługi niektórych typowych warunków błędu.

```objc
    MSALInteractiveTokenParameters *interactiveParameters = ...;
    MSALSilentTokenParameters *silentParameters = ...;
    
    MSALCompletionBlock completionBlock;
    __block __weak MSALCompletionBlock weakCompletionBlock;
    
    weakCompletionBlock = completionBlock = ^(MSALResult *result, NSError *error)
    {
        if (!error)
        {
            // Use result.accessToken
            NSString *accessToken = result.accessToken;
            return;
        }
        
        if ([error.domain isEqualToString:MSALErrorDomain])
        {
            switch (error.code)
            {
                case MSALErrorInteractionRequired:
                {
                    // Interactive auth will be required
                    [application acquireTokenWithParameters:interactiveParameters
                                            completionBlock:weakCompletionBlock];
                    
                    break;
                }
                    
                case MSALErrorServerDeclinedScopes:
                {
                    // These are list of granted and declined scopes.
                    NSArray *grantedScopes = error.userInfo[MSALGrantedScopesKey];
                    NSArray *declinedScopes = error.userInfo[MSALDeclinedScopesKey];
                    
                    // To continue acquiring token for granted scopes only, do the following
                    silentParameters.scopes = grantedScopes;
                    [application acquireTokenSilentWithParameters:silentParameters
                                                  completionBlock:weakCompletionBlock];
                    
                    // Otherwise, instead, handle error fittingly to the application context
                    break;
                }
                    
                case MSALErrorServerProtectionPoliciesRequired:
                {
                    // Integrate the Intune SDK and call the
                    // remediateComplianceForIdentity:silent: API.
                    // Handle this error only if you integrated Intune SDK.
                    // See more info here: https://aka.ms/intuneMAMSDK
                    
                    break;
                }
                    
                case MSALErrorUserCanceled:
                {
                    // The user cancelled the web auth session.
                    // You may want to ask the user to try again.
                    // Handling of this error is optional.
                    
                    break;
                }
                    
                case MSALErrorInternal:
                {
                    // Log the error, then inspect the MSALInternalErrorCodeKey
                    // in the userInfo dictionary.
                    // Display generic error message to the end user
                    // More detailed information about the specific error
                    // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                    NSLog(@"Failed with error %@", error);
                    
                    break;
                }
                    
                default:
                    NSLog(@"Failed with unknown MSAL error %@", error);
                    
                    break;
            }
            
            return;
        }
        
        // Handle no internet connection.
        if ([error.domain isEqualToString:NSURLErrorDomain] && error.code == NSURLErrorNotConnectedToInternet)
        {
            NSLog(@"No internet connection.");
            return;
        }
        
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        NSLog(@"Failed with error %@", error);
    };
    
    // Acquire token silently
    [application acquireTokenSilentWithParameters:silentParameters
                                  completionBlock:completionBlock];

     // or acquire it interactively.
     [application acquireTokenWithParameters:interactiveParameters
                             completionBlock:completionBlock];
```

```swift
    let interactiveParameters: MSALInteractiveTokenParameters = ...
    let silentParameters: MSALSilentTokenParameters = ...
            
    var completionBlock: MSALCompletionBlock!
    completionBlock = { (result: MSALResult?, error: Error?) in
                
        if let result = result
        {
            // Use result.accessToken
            let accessToken = result.accessToken
            return
        }

        guard let error = error as NSError? else { return }

        if error.domain == MSALErrorDomain, let errorCode = MSALError(rawValue: error.code)
        {
            switch errorCode
            {
                case .interactionRequired:
                    // Interactive auth will be required
                    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)

                case .serverDeclinedScopes:
                    let grantedScopes = error.userInfo[MSALGrantedScopesKey]
                    let declinedScopes = error.userInfo[MSALDeclinedScopesKey]

                    if let scopes = grantedScopes as? [String] {
                        silentParameters.scopes = scopes
                        application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
                    }
                        
                    case .serverProtectionPoliciesRequired:
                        // Integrate the Intune SDK and call the
                        // remediateComplianceForIdentity:silent: API.
                        // Handle this error only if you integrated Intune SDK.
                        // See more info here: https://aka.ms/intuneMAMSDK
                        break
                        
                    case .userCanceled:
                       // The user cancelled the web auth session.
                       // You may want to ask the user to try again.
                       // Handling of this error is optional.
                       break
                        
                    case .internal:
                        // Log the error, then inspect the MSALInternalErrorCodeKey
                        // in the userInfo dictionary.
                        // Display generic error message to the end user
                        // More detailed information about the specific error
                        // under MSALInternalErrorCodeKey can be found in MSALInternalError enum.
                        print("Failed with error \(error)");
                        
                    default:
                        print("Failed with unknown MSAL error \(error)")
            }
        }
                
        // Handle no internet connection.
        if error.domain == NSURLErrorDomain && error.code == NSURLErrorNotConnectedToInternet
        {
            print("No internet connection.")
            return
        }
                
        // Other errors may require trying again later,
        // or reporting authentication problems to the user.
        print("Failed with error \(error)");    
    }
   
    // Acquire token silently
    application.acquireToken(with: interactiveParameters, completionBlock: completionBlock)
 
    // or acquire it interactively.
    application.acquireTokenSilent(with: silentParameters, completionBlock: completionBlock)
```

---

[!INCLUDE [Active directory error handling claims challenges](../../../includes/active-directory-develop-error-handling-claims-challenges.md)]

MSAL dla systemów iOS i macOS umożliwia żądanie określonych oświadczeń w scenariuszach pozyskiwania tokenów interaktywnych i dyskretnych.

Aby zażądać oświadczeń niestandardowych, określ `claimsRequest` w `MSALSilentTokenParameters` lub `MSALInteractiveTokenParameters` .

Aby uzyskać więcej informacji [, zobacz żądanie oświadczeń niestandardowych przy użyciu MSAL dla systemów iOS i macOS](request-custom-claims.md) .

[!INCLUDE [Active directory error handling retries](../../../includes/active-directory-develop-error-handling-retries.md)]

## <a name="next-steps"></a>Następne kroki

Rozważ włączenie [rejestrowania w MSAL dla systemu iOS/macOS](msal-logging-ios.md) , aby ułatwić diagnozowanie i Debugowanie problemów.
