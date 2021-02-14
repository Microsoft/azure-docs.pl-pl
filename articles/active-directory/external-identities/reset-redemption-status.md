---
title: Resetowanie stanu wykupu użytkownika-gościa — Azure AD
description: Informacje na temat resetowania stanu wykupu zaproszeń dla Azure Active Directory użytkowników systemu B2B w usłudze Azure AD.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: d843755847d074e00aec9ed2830cb873b6bb3382
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100365416"
---
# <a name="reset-redemption-status-for-a-guest-user"></a>Zresetuj stan wykupu dla użytkownika-gościa

Po wykorzystaniu przez użytkownika-gościa zaproszenia do współpracy B2B mogą wystąpić sytuacje, w których należy zaktualizować informacje logowania, na przykład:

- Użytkownik chce zalogować się przy użyciu innego dostawcy poczty e-mail i tożsamości
- Konto użytkownika w swojej dzierżawie domowej zostało usunięte i utworzone na nowo
- Użytkownik został przeniesiony do innej firmy, ale nadal musi mieć ten sam dostęp do zasobów
- Obowiązki użytkownika zostały przekazano do innego użytkownika

Aby zarządzać tymi scenariuszami wcześniej, trzeba ręcznie usunąć konto użytkownika-gościa z katalogu i zaprosić użytkownika. Teraz można użyć programu PowerShell lub interfejsu API zaproszenia Microsoft Graph do resetowania stanu wykupu użytkownika i ponownego zaproszenia użytkownika przy zachowaniu identyfikatora obiektu użytkownika, członkostwa w grupach i przypisań aplikacji. Gdy użytkownik zrealizuje nowe zaproszenie, nazwa UPN użytkownika nie ulegnie zmianie, ale nazwy logowania użytkownika zmienią się na nową wiadomość e-mail. Użytkownik może następnie zalogować się przy użyciu nowego adresu e-mail lub wiadomości e-mail dodanej do `otherMails` właściwości obiektu użytkownika.

## <a name="use-powershell-to-reset-redemption-status"></a>Resetowanie stanu wykupu przy użyciu programu PowerShell

Zainstaluj najnowszy moduł AzureADPreview PowerShell i Utwórz nowe zaproszenie z `InvitedUserEMailAddress` ustawionym nowym adresem e-mail, a następnie `ResetRedemption` Ustaw wartość `true` .

```powershell  
Uninstall-Module AzureADPreview 
Install-Module AzureADPreview 
Connect-AzureAD 
$ADGraphUser = Get-AzureADUser -objectID "UPN of User to Reset"  
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId 
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser -ResetRedemption $True 
```

## <a name="use-microsoft-graph-api-to-reset-redemption-status"></a>Resetowanie stanu wykupu przy użyciu interfejsu API Microsoft Graph

Używając [interfejsu API zaproszenia Microsoft Graph](/graph/api/resources/invitation?view=graph-rest-1.0), ustaw `resetRedemption` Właściwość na `true` i określ nowy adres e-mail we `invitedUserEmailAddress` właściwości.

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

- [Dodawanie Azure Active Directory użytkowników współpracy B2B przy użyciu programu PowerShell](customize-invitation-api.md#powershell)
- [Właściwości użytkownika Gość usługi Azure AD B2B](user-properties.md)
