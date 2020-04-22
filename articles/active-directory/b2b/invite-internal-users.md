---
title: Zapraszanie użytkowników wewnętrznych do współpracy b2b — usługa Azure AD
description: Jeśli masz wewnętrzne konta użytkowników dla partnerów, dystrybutorów, dostawców, dostawców i innych gości, możesz zmienić na współpracę usługi Azure AD B2B, zapraszając ich do logowania się przy użyciu własnych zewnętrznych poświadczeń lub logowania. Użyj programu PowerShell lub interfejsu API zaproszenia programu Microsoft Graph.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 04/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 783fc0fa6f6c4e6c918fa3ff5fe0b53a71fa0178
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680173"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>Zapraszanie użytkowników wewnętrznych do współpracy z B2B

|     |
| --- |
| Zapraszanie użytkowników wewnętrznych do korzystania ze współpracy B2B jest publiczną funkcją w wersji zapoznawczej usługi Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawców, zobacz [Dodatkowe warunki użytkowania w wersji Zapoznawczej platformy Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). |
|     |

Przed udostępnieniem współpracy usługi Azure AD B2B organizacje mogły współpracować z dystrybutorami, dostawcami, dostawcami i innymi użytkownikami- konfiguruj dla nich poświadczenia wewnętrzne. Jeśli masz wewnętrznych użytkowników gości, takich jak ten, można zaprosić ich do korzystania z współpracy B2B, dzięki czemu można korzystać z korzyści usługi Azure AD B2B. Użytkownicy-goście b2B będą mogli używać własnych tożsamości i poświadczeń do logowania i nie trzeba będzie używać haseł ani zarządzać cyklami życia konta.

Wysłanie zaproszenia na istniejące konto wewnętrzne umożliwia zachowanie identyfikatora obiektu użytkownika, nazwy UPN, członkostwa w grupach i przypisań aplikacji. Nie trzeba ręcznie usuwać i ponownie zapraszać użytkownika ani ponownie przypisywać zasobów. Aby zaprosić użytkownika, użyjesz interfejsu API zaproszenia, aby przekazać zarówno wewnętrzny obiekt użytkownika, jak i adres e-mail użytkownika-gościa wraz z zaproszeniem. Gdy użytkownik zaakceptuje zaproszenie, usługa B2B zmienia istniejący obiekt użytkownika wewnętrznego na użytkownika B2B. W przyszłości użytkownik musi zalogować się do usług zasobów w chmurze przy użyciu poświadczeń B2B. Nadal mogą używać swoich wewnętrznych poświadczeń, aby uzyskać dostęp do zasobów lokalnych, ale można temu zapobiec, resetując lub zmieniając hasło na koncie wewnętrznym.

> [!NOTE]
> Zaproszenie jest jednokierunkowe. Możesz zaprosić użytkowników wewnętrznych do korzystania ze współpracy B2B, ale nie można usunąć poświadczeń B2B po ich dodaniu. Aby zmienić użytkownika z powrotem na użytkownika wewnętrznego, musisz usunąć obiekt użytkownika i utworzyć nowy.

Podczas gdy w publicznej wersji zapoznawczej, metoda opisana w tym artykule do zapraszania użytkowników wewnętrznych do współpracy B2B nie może być używana w tych przypadkach:

- Użytkownikowi wewnętrznej przypisano już licencję programu Exchange.
- Użytkownik pochodzi z domeny skonfigurowanej do bezpośredniej federacji w katalogu.
- Użytkownik wewnętrzny jest kontem tylko w chmurze, a ich konto główne nie znajduje się w usłudze Azure AD.

W takich przypadkach, jeśli użytkownik wewnętrzny musi zostać zmieniony na użytkownika B2B, należy usunąć konto wewnętrzne i wysłać użytkownikowi zaproszenie do współpracy B2B.

**Użytkownicy zsynchronizowane lokalnie:** w przypadku kont użytkowników, które są synchronizowane między środowiskiem lokalnym a chmurą, katalog lokalny pozostaje źródłem uprawnień po zaproszeniu ich do korzystania ze współpracy B2B. Wszelkie zmiany wprowadzone na koncie lokalnym zostaną zsynchronizowane z kontem w chmurze, w tym wyłączenie lub usunięcie konta. W związku z tym nie można uniemożliwić użytkownikowi zalogowanie się do konta lokalnego przy zachowaniu konta w chmurze, po prostu usuwając konto lokalne. Zamiast tego można ustawić hasło konta lokalnego na losowy identyfikator GUID lub inną nieznaną wartość.

## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>Jak zaprosić użytkowników wewnętrznych do współpracy b2b

Za pomocą programu PowerShell lub interfejsu API zaproszenia można wysłać zaproszenie B2B do użytkownika wewnętrznego. Upewnij się, że adres e-mail, którego chcesz użyć dla zaproszenia, jest ustawiony jako zewnętrzny adres e-mail w obiekcie użytkownika wewnętrznego.

- W przypadku użytkownika tylko w chmurze użyj adresu e-mail we właściwości User.OtherMails dla zaproszenia.
- W przypadku użytkownika zsynchronizowanego lokalnie należy użyć wartości we właściwości User.Mail dla zaproszenia.
- Domena w uobiekcie Mail użytkownika musi być zgodna z kontem, z których korzysta do logowania. W przeciwnym razie niektóre usługi, takie jak Teams, nie będą mogły uwierzytelnić użytkownika.

Domyślnie zaproszenie wyśle użytkownikowi wiadomość e-mail z informacją, że został zaproszony, ale możesz pominąć tę wiadomość e-mail i zamiast tego wysłać własną.

> [!NOTE]
> Aby wysłać własną wiadomość e-mail lub inną komunikację, można użyć new-AzureADMSInvitation z -SendInvitationMessage:$false zaprosić użytkowników po cichu, a następnie wysłać własną wiadomość e-mail do przekonwertowanego użytkownika. Zobacz [interfejs API współpracy usługi Azure AD B2B i dostosowywania](customize-invitation-api.md).

## <a name="use-powershell-to-send-a-b2b-invitation"></a>Wysyłanie zaproszenia do korzystania z programu PowerShell za pomocą programu PowerShell

Użyj następującego polecenia, aby zaprosić użytkownika do współpracy B2B:

```powershell
Uninstall-Module AzureADPreview
Install-Module AzureADPreview
$ADGraphUser = Get-AzureADUser -searchstring "<<external email>>"
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser
```

## <a name="use-the-invitation-api-to-send-a-b2b-invitation"></a>Wysyłanie zaproszenia za pomocą interfejsu API zaproszenia

W poniższym przykładzie pokazano, jak wywołać interfejs API zaproszenia, aby zaprosić użytkownika wewnętrznego jako użytkownika B2B.

```json
POST https://graph.microsoft.com/v1.0/invitations
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
        "id": "<<ID for the user you want to convert>>"
    }
}
```

Odpowiedź na interfejs API jest taka sama odpowiedź, jaka jest w przypadku zaproszenia nowego użytkownika-gościa do katalogu.

## <a name="next-steps"></a>Następne kroki

- [Wykup zaproszenia do współpracy B2B](redemption-experience.md)
