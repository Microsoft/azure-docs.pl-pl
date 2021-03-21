---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/18/2021
ms.author: mimart
ms.openlocfilehash: 4f6ba0892438d49dcc982a01a6b30dfa36ed43b5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98674241"
---
## <a name="add-a-user-journey"></a>Dodawanie podróży użytkownika 

W tym momencie dostawca tożsamości został skonfigurowany, ale nie jest jeszcze dostępny na żadnej stronie logowania. Jeśli nie masz własnej niestandardowej podróży użytkownika, Utwórz duplikat istniejącej pracy przez użytkownika szablonu, w przeciwnym razie przejdź do następnego kroku. 

1. Otwórz plik *TrustFrameworkBase.xml* z pakietu początkowego.
1. Znajdź i Skopiuj całą zawartość elementu **UserJourney** , który zawiera `Id="SignUpOrSignIn"` .
1. Otwórz *TrustFrameworkExtensions.xml* i Znajdź element **UserJourneys** . Jeśli element nie istnieje, Dodaj go.
1. Wklej całą zawartość elementu **UserJourney** , który został skopiowany jako element podrzędny elementu **UserJourneys** .
1. Zmień nazwę identyfikatora podróży użytkownika. Na przykład `Id="CustomSignUpSignIn"`.

## <a name="add-the-identity-provider-to-a-user-journey"></a>Dodawanie dostawcy tożsamości do podróży użytkownika 

Teraz, gdy już masz użytkownika, Dodaj nowego dostawcę tożsamości do podróży użytkownika. Najpierw Dodaj przycisk logowania, a następnie Połącz przycisk z akcją. Akcja jest utworzonym wcześniej profilem technicznym.

1. Znajdź element kroku aranżacji obejmujący `Type="CombinedSignInAndSignUp"` lub `Type="ClaimsProviderSelection"` w podróży użytkownika. Zwykle jest to pierwszy krok aranżacji. Element **ClaimsProviderSelections** zawiera listę dostawców tożsamości, za pomocą których użytkownik może się zalogować. Kolejność elementów steruje kolejnością przycisków logowania prezentowanych użytkownikowi. Dodaj element XML **ClaimsProviderSelection** . Ustaw wartość **TargetClaimsExchangeId** na przyjazną nazwę.

1. W następnym kroku aranżacji Dodaj element **ClaimsExchange** . Ustaw **Identyfikator** na wartość docelowego identyfikatora wymiany oświadczeń. Zaktualizuj wartość **TechnicalProfileReferenceId** na identyfikator utworzonego wcześniej profilu technicznego.

Poniższy kod XML demonstruje pierwsze dwa kroki aranżacji użytkownika podczas podróży z dostawcą tożsamości: