---
title: Zapraszanie użytkowników wewnętrznych do współpracy B2B — Azure AD
description: Jeśli masz wewnętrzne konta użytkowników dla partnerów, dystrybutorów, dostawców, dostawców i innych Gości, możesz przejść do współpracy B2B w usłudze Azure AD, zapraszając je do zalogowania się przy użyciu własnych poświadczeń zewnętrznych lub logowania. Użyj programu PowerShell lub interfejsu API zaproszenia Microsoft Graph.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 04/12/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8acc547552cecaebb60888bb7b9777f6279b9b7c
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/08/2021
ms.locfileid: "98015763"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>Zapraszanie użytkowników wewnętrznych do współpracy B2B

> [!NOTE]
> Zapraszanie użytkowników wewnętrznych do korzystania z współpracy B2B jest publiczną funkcją w wersji zapoznawczej Azure Active Directory. Aby uzyskać więcej informacji na temat wersji zapoznawczych, zobacz [dodatkowe warunki użytkowania wersji](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)zapoznawczych Microsoft Azure.

Przed udostępnieniem funkcji współpracy B2B usługi Azure AD organizacje mogą współpracować z dystrybutorami, dostawcami, dostawcami i innymi użytkownikami-gośćmi przez skonfigurowanie dla nich poświadczeń wewnętrznych. Jeśli masz użytkowników z wewnętrznymi Gośćmi, możesz zaprosić ich do korzystania ze współpracy B2B, aby móc korzystać z korzyści B2B usługi Azure AD. Użytkownicy korzystający z programu B2B będą mogli korzystać z własnych tożsamości i poświadczeń w celu zalogowania się i nie będzie potrzeby zachowywania haseł ani zarządzania cyklem życia konta.

Wysłanie zaproszenia do istniejącego konta wewnętrznego pozwala zachować ten identyfikator obiektu użytkownika, nazwy UPN, członkostwa w grupach i przypisania aplikacji. Nie musisz ręcznie usuwać ani ponownie zapraszać użytkowników ani przypisywać zasobów. Aby zaprosić użytkownika, należy użyć interfejsu API zaproszenia do przekazywania zarówno wewnętrznego obiektu użytkownika, jak i adresu e-mail użytkownika-gościa wraz z zaproszeniem. Gdy użytkownik zaakceptuje zaproszenie, usługa B2B zmieni istniejącego wewnętrznego obiektu użytkownika na użytkownika B2B. W przyszłości użytkownik musi zalogować się do usług zasobów w chmurze przy użyciu swoich poświadczeń B2B. Mogą oni nadal używać swoich wewnętrznych poświadczeń do uzyskiwania dostępu do zasobów lokalnych, ale można to uniknąć przez zresetowanie lub zmianę hasła na koncie wewnętrznym.

> [!NOTE]
> Zaproszenie jest jednokierunkowe. Można zaprosić użytkowników wewnętrznych do korzystania z współpracy B2B, ale nie można usunąć poświadczeń B2B po ich dodaniu. Aby zmienić użytkownika z powrotem na użytkownika wewnętrznego, należy usunąć obiekt użytkownika i utworzyć nowy.

W publicznej wersji zapoznawczej nie można używać metody opisanej w tym artykule w przypadku zapraszania użytkowników wewnętrznych do współpracy B2B w następujących przypadkach:

- Użytkownik wewnętrzny ma przypisaną licencję programu Exchange.
- Użytkownik należy do domeny, która jest skonfigurowana dla Federacji bezpośredniej w Twoim katalogu.
- Użytkownik wewnętrzny jest kontem tylko w chmurze, a jego główne konto nie znajduje się w usłudze Azure AD.

W tych przypadkach, jeśli użytkownik wewnętrzny musi zostać zmieniony na użytkownika B2B, należy usunąć konto wewnętrzne i wysłać użytkownikowi zaproszenie do współpracy B2B.

**Użytkownicy z synchronizacją lokalną**: w przypadku kont użytkowników synchronizowanych między środowiskiem lokalnym i chmurą katalog lokalny pozostaje źródłem urzędu po zaproszeniu do korzystania z współpracy B2B. Wszelkie zmiany wprowadzone na koncie lokalnym będą synchronizowane z kontem w chmurze, w tym wyłączaniem lub usuwaniem konta. W związku z tym nie można uniemożliwić użytkownikowi zalogowanie się do swojego konta lokalnego podczas zachowywania konta w chmurze przez usunięcie konta lokalnego. Zamiast tego można ustawić hasło konta lokalnego na losowy identyfikator GUID lub inną nieznaną wartość.

## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>Jak zaprosić użytkowników wewnętrznych do współpracy B2B

Możesz użyć programu PowerShell lub interfejsu API zaproszenia do wysłania zaproszenia B2B do użytkownika wewnętrznego. Upewnij się, że adres e-mail, który ma być używany dla zaproszenia, jest ustawiony jako zewnętrzny adres e-mail w obiekcie użytkownika wewnętrznego.

- Musisz użyć adresu e-mail we właściwości User. mail dla zaproszenia.
- Domena we właściwościach poczty użytkownika musi być zgodna z kontem używanym do logowania. W przeciwnym razie niektóre usługi, takie jak zespoły, nie będą mogły uwierzytelnić użytkownika.

Domyślnie zaproszenie wyśle użytkownikowi wiadomość e-mail z informacją o tym, że zostały zaproszone, ale możesz pominąć tę wiadomość e-mail i wysłać własne.

> [!NOTE]
> Aby wysłać własną wiadomość e-mail lub inną komunikację, możesz użyć New-AzureADMSInvitation z-SendInvitationMessage: $false, aby zapraszać użytkowników w trybie dyskretnym, a następnie wysyłać własne wiadomości e-mail do konwertowanego użytkownika. Zobacz [interfejs API współpracy i dostosowywanie usługi Azure AD B2B](customize-invitation-api.md).

## <a name="use-powershell-to-send-a-b2b-invitation"></a>Wysyłanie zaproszenia B2B przy użyciu programu PowerShell

Użyj następującego polecenia, aby zaprosić użytkownika do współpracy B2B:

```powershell
Uninstall-Module AzureADPreview
Install-Module AzureADPreview
$ADGraphUser = Get-AzureADUser -objectID "UPN of Internal User"
$msGraphUser = New-Object Microsoft.Open.MSGraph.Model.User -ArgumentList $ADGraphUser.ObjectId
New-AzureADMSInvitation -InvitedUserEmailAddress <<external email>> -SendInvitationMessage $True -InviteRedirectUrl "http://myapps.microsoft.com" -InvitedUser $msGraphUser
```

## <a name="use-the-invitation-api-to-send-a-b2b-invitation"></a>Korzystanie z interfejsu API zaproszenia do wysyłania zaproszenia B2B

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

Odpowiedź na interfejs API jest taka sama, jak w przypadku zapraszania nowego użytkownika-gościa do katalogu.

## <a name="next-steps"></a>Następne kroki

- [Realizacja zaproszenia do współpracy B2B](redemption-experience.md)
