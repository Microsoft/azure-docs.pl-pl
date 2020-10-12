---
title: Interfejs API współpracy B2B i dostosowywanie — Azure Active Directory
description: Azure Active Directory współpracy B2B obsługuje relacje między firmami, umożliwiając partnerom biznesowym selektywne uzyskiwanie dostępu do aplikacji firmowych.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/11/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2cc959d23aebea082d873e9474998e7f17b1b320
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91295162"
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>Azure Active Directory interfejs API współpracy B2B i dostosowywanie

Mamy wielu klientów, którzy chcą dostosować proces zaproszenia w sposób, który najlepiej sprawdza się w swoich organizacjach. Korzystając z naszego interfejsu API, możesz to zrobić. [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>Możliwości interfejsu API zaproszenia

Interfejs API oferuje następujące możliwości:

1. Zaproś użytkownika zewnętrznego o *dowolny* adres e-mail.

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. Dostosuj miejsce, w którym użytkownicy mają być grunty po zaakceptowaniu zaproszenia.

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. Wybierz, aby wysłać do nas standardową wiadomość e-mail z zaproszeniem

    ```
    "sendInvitationMessage": true
    ```

   z komunikatem do adresata, który można dostosować

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. I wybierz pozycję DW: osoby, które chcesz zachować w pętli na temat zapraszania tego współpracownika.

5. Lub całkowicie dostosuj przepływ pracy zaproszenia i dołączania, wybierając opcję nie wysyłaj powiadomień za pomocą usługi Azure AD.

    ```
    "sendInvitationMessage": false
    ```

   W tym przypadku otrzymujesz adres URL umorzenia z interfejsu API, który można osadzić w szablonie wiadomości e-mail, wiadomości błyskawicznych lub innej wybranej metodzie dystrybucji.

6. Na koniec, jeśli jesteś administratorem, możesz wybrać opcję zapraszania użytkownika jako członka.

    ```
    "invitedUserType": "Member"
    ```


## <a name="authorization-model"></a>Model autoryzacji

Interfejs API można uruchomić w następujących trybach autoryzacji:

### <a name="app--user-mode"></a>Aplikacja i tryb użytkownika

W tym trybie interfejs API musi mieć uprawnienia do tworzenia zaproszeń B2B.

### <a name="app-only-mode"></a>Tryb tylko aplikacji

W kontekście tylko aplikacji aplikacja wymaga elementu User. zaprasza. All, aby zaproszenie zostało zakończone pomyślnie.

Aby uzyskać więcej informacji, zobacz: https://developer.microsoft.com/graph/docs/authorization/permission_scopes


## <a name="powershell"></a>Program PowerShell

Za pomocą programu PowerShell możesz łatwo dodawać i zapraszać użytkowników zewnętrznych do organizacji. Utwórz zaproszenie przy użyciu polecenia cmdlet:

```powershell
New-AzureADMSInvitation
```

Możesz użyć następujących opcji:

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -SendInvitationMessage
* -InvitedUserMessageInfo

### <a name="invitation-status"></a>Stan zaproszenia

Po wysłaniu zaproszenia użytkownika zewnętrznego możesz użyć polecenia cmdlet **Get-AzureADUser** , aby sprawdzić, czy zaakceptuje to polecenie. Następujące właściwości Get-AzureADUser są wypełniane, gdy użytkownik zewnętrzny otrzymuje zaproszenie:

* **UserState** wskazuje, czy zaproszenie jest **PendingAcceptance** lub **zaakceptowane**.
* **UserStateChangedOn** pokazuje sygnaturę czasową dla najnowszej zmiany właściwości **userState** .

Możesz użyć opcji **filtru** , aby odfiltrować wyniki według **userState**. W poniższym przykładzie pokazano, jak filtrować wyniki, aby pokazać tylko użytkowników, którzy mają Oczekujące zaproszenia. W przykładzie pokazano również opcję **listy format** , która pozwala określić właściwości do wyświetlenia. 
 

```powershell
Get-AzureADUser -Filter "UserState eq 'PendingAcceptance'" | Format-List -Property DisplayName,UserPrincipalName,UserState,UserStateChangedOn
```

> [!NOTE]
> Upewnij się, że masz najnowszą wersję modułu AzureAD PowerShell lub moduł AzureADPreview PowerShell. 

## <a name="see-also"></a>Zobacz też

Zapoznaj się z dokumentacją interfejsu API zaproszenia w temacie [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation) .

## <a name="next-steps"></a>Następne kroki

- [Czym jest współpraca B2B w usłudze Azure AD?](what-is-b2b.md)
- [Elementy wiadomości e-mail z zaproszeniem do współpracy B2B](invitation-email-elements.md)
- [Realizacja zaproszenia do współpracy B2B](redemption-experience.md)
- [Dodawanie użytkowników współpracy B2B bez zaproszenia](add-user-without-invite.md)