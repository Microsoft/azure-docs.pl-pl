---
title: Resetowanie stanu realizacji użytkownika-gościa — Azure AD
description: Dowiedz się, jak zresetować stan realizacji zaproszenia dla użytkowników Azure Active Directory B2B w Azure AD External Identities.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/06/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: f5bfce7ef2621cbe3bbbfdd95bf9a75e427c8cbd
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531885"
---
# <a name="reset-redemption-status-for-a-guest-user-preview"></a>Resetowanie stanu realizacji dla użytkownika-gościa (wersja zapoznawcza)

Gdy użytkownik-gość zrealizuj zaproszenie na potrzeby współpracy B2B, może być konieczne zaktualizowanie informacji logowania, na przykład gdy:

- Użytkownik chce zalogować się przy użyciu innego dostawcy poczty e-mail i tożsamości
- Konto użytkownika w jego dzierżawie głównej zostało usunięte i ponownie utworzone
- Użytkownik został przeniesiony do innej firmy, ale nadal potrzebuje tego samego dostępu do Twoich zasobów
- Obowiązki użytkownika zostały przekazane do innego użytkownika

Aby wcześniej zarządzać tymi scenariuszami, trzeba było ręcznie usunąć konto użytkownika-gościa z katalogu i ponownie go zaimprocować. Teraz możesz użyć programu PowerShell lub interfejsu API zaproszenia do programu Microsoft Graph, aby zresetować stan realizacji użytkownika i ponownie go zaaprocować przy zachowaniu identyfikatora obiektu użytkownika, członkosstwa w grupach i przypisań aplikacji. Gdy użytkownik zrealizuj nowe zaproszenie, nazwa UPN użytkownika nie zmieni się, ale nazwa logowania użytkownika zmieni się na nową wiadomość e-mail. Użytkownik może następnie zalogować się przy użyciu nowej wiadomości e-mail lub wiadomości e-mail dodanej do właściwości `otherMails` obiektu użytkownika.

## <a name="reset-the-email-address-used-for-sign-in"></a>Resetowanie adresu e-mail używanego do logowania

Jeśli użytkownik chce zalogować się przy użyciu innej poczty e-mail:

1. Upewnij się, że nowy adres e-mail został dodany do `mail` właściwości lub `otherMails` obiektu użytkownika. 
2.  Zastąp adres e-mail we `InvitedUserEmailAddress` właściwości nowym adresem e-mail.
3. Użyj jednej z poniższych metod, aby zresetować stan realizacji użytkownika.

> [!NOTE]
>W publicznej wersji zapoznawczej dostępne są dwa zalecenia:
>- W przypadku resetowania adresu e-mail użytkownika do nowego adresu zalecamy ustawienie `mail` właściwości . W ten sposób użytkownik może zrealizować zaproszenie, logując się do katalogu oraz korzystając z linku do realizacji w zaproszeniu.
>- W przypadku resetowania stanu użytkownika-gościa B2B należy to zrobić w kontekście użytkownika. Wywołania tylko aplikacji nie są obecnie obsługiwane.
>
## <a name="use-powershell-to-reset-redemption-status"></a>Resetowanie stanu realizacji przy użyciu programu PowerShell

Zainstaluj najnowszy moduł AzureADPreview programu PowerShell i utwórz nowe zaproszenie z ustawieniem na nowy adres `InvitedUserEmailAddress` e-mail i ustaw `ResetRedemption` wartość `true` .

```powershell  
Uninstall-Module AzureADPreview 
Install-Module AzureADPreview 
Connect-AzureAD 
$ADGraphUser = Get-AzureADUser -objectID "UPN of User to Reset"  
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId 
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser -ResetRedemption $True 
```

## <a name="use-microsoft-graph-api-to-reset-redemption-status"></a>Resetowanie stanu realizacji przy użyciu Microsoft Graph API

Używając [interfejsu API Microsoft Graph z zaproszeniem,](/graph/api/resources/invitation?view=graph-rest-beta&preserve-view=true)ustaw właściwość na i określ nowy adres `resetRedemption` `true` e-mail we właściwości `invitedUserEmailAddress` .

```json
POST https://graph.microsoft.com/beta/invitations  
Authorization: Bearer eyJ0eX...  
ContentType: application/json  
{  
   "invitedUserEmailAddress": "<<external email>>",  
   "sendInvitationMessage": true,  
   "invitedUserMessageInfo": {  
      "messageLanguage": "en-US",  
      "ccRecipients": [  
         {  
            "emailAddress": {  
               "name": null,  
               "address": "<<optional additional notification email>>"  
            }  
         } 
      ],  
      "customizedMessageBody": "<<custom message>>"  
},  
"inviteRedirectUrl": "https://myapps.microsoft.com?tenantId=",  
"invitedUser": {  
   "id": "<<ID for the user you want to reset>>"  
}, 
"resetRedemption": true 
}
```

## <a name="next-steps"></a>Następne kroki

- [Dodawanie Azure Active Directory współpracy B2B przy użyciu programu PowerShell](customize-invitation-api.md#powershell)
- [Właściwości użytkownika-gościa usługi Azure AD B2B](user-properties.md)
