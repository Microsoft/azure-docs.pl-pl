---
title: Zapraszanie użytkowników wewnętrznych do współpracy B2B — Azure AD
description: Jeśli masz wewnętrzne konta użytkowników dla partnerów, dystrybutorów, dostawców, dostawców i innych Gości, możesz przejść do współpracy B2B w usłudze Azure AD, zapraszając je do zalogowania się przy użyciu własnych poświadczeń zewnętrznych lub logowania. Użyj programu PowerShell lub interfejsu API zaproszenia Microsoft Graph.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/03/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: 802307a21873d15242c2e387ec0defe35f50bb20
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99576434"
---
# <a name="invite-internal-users-to-b2b-collaboration"></a>Zapraszanie użytkowników wewnętrznych do współpracy B2B

Przed udostępnieniem funkcji współpracy B2B usługi Azure AD organizacje mogą współpracować z dystrybutorami, dostawcami, dostawcami i innymi użytkownikami-gośćmi przez skonfigurowanie dla nich poświadczeń wewnętrznych. Jeśli masz użytkowników z wewnętrznymi Gośćmi, możesz zaprosić je do korzystania z funkcji współpracy B2B. Ci użytkownicy-Goście B2B będą mogli korzystać z własnych tożsamości i poświadczeń w celu zalogowania się i nie będzie konieczne zachowywanie haseł ani Zarządzanie cyklem życia konta.

Wysłanie zaproszenia do istniejącego konta wewnętrznego pozwala zachować ten identyfikator obiektu użytkownika, nazwy UPN, członkostwa w grupach i przypisania aplikacji. Nie musisz ręcznie usuwać ani ponownie zapraszać użytkowników ani przypisywać zasobów. Aby zaprosić użytkownika, należy użyć interfejsu API zaproszenia do przekazywania zarówno wewnętrznego obiektu użytkownika, jak i adresu e-mail użytkownika-gościa wraz z zaproszeniem. Gdy użytkownik zaakceptuje zaproszenie, usługa B2B zmieni istniejącego wewnętrznego obiektu użytkownika na użytkownika B2B. W przyszłości użytkownik musi zalogować się do usług zasobów w chmurze przy użyciu swoich poświadczeń B2B.

## <a name="things-to-consider"></a>Rzeczy do rozważenia

- **Dostęp do zasobów lokalnych**: po zaproszeniu użytkownika do współpracy z firmą B2B nadal mogą oni korzystać z wewnętrznych poświadczeń w celu uzyskania dostępu do zasobów lokalnych. Można to uniknąć przez zresetowanie lub zmianę hasła na koncie wewnętrznym. Wyjątek to [jednorazowe uwierzytelnianie kodu dostępu w wiadomości e-mail](one-time-passcode.md). Jeśli metoda uwierzytelniania użytkownika zostanie zmieniona na jednorazowy kod dostępu, nie będą one mogły już korzystać z poświadczeń wewnętrznych.

- **Rozliczanie**: Ta funkcja nie zmienia elementu UserType dla użytkownika, dlatego nie przełączaj modelu rozliczeń użytkownika na [tożsamości zewnętrzne comiesięcznie aktywnych użytkowników (Mau)](external-identities-pricing.md). Aby aktywować ceny MAU dla użytkownika, Zmień wartość UserType dla użytkownika na `guest` . Należy również pamiętać, że dzierżawa usługi Azure AD musi być [połączona z subskrypcją platformy Azure](external-identities-pricing.md#link-your-azure-ad-tenant-to-a-subscription) w celu aktywowania rozliczeń Mau.

- **Zaproszenie jest jednokierunkowe**: można zaprosić użytkowników wewnętrznych do korzystania z współpracy B2B, ale nie można usunąć poświadczeń B2B po ich dodaniu. Aby zmienić użytkownika z powrotem na użytkownika wewnętrznego, należy usunąć obiekt użytkownika i utworzyć nowy.

- **Zespoły**: gdy użytkownik uzyskuje dostęp do zespołów przy użyciu ich poświadczeń zewnętrznych, ich dzierżawa nie będzie dostępna początkowo w selektorze dzierżawy zespołu. Użytkownik może uzyskać dostęp do zespołów przy użyciu adresu URL, który zawiera kontekst dzierżawy, na przykład: `https://team.microsoft.com/?tenantId=<TenantId>` . Następnie dzierżawa stanie się dostępna w selektorze dzierżaw zespołów.

- **Użytkownicy z synchronizacją lokalną**: w przypadku kont użytkowników synchronizowanych między środowiskiem lokalnym i chmurą katalog lokalny pozostaje źródłem urzędu po zaproszeniu do korzystania z współpracy B2B. Wszelkie zmiany wprowadzone na koncie lokalnym będą synchronizowane z kontem w chmurze, w tym wyłączaniem lub usuwaniem konta. W związku z tym nie można uniemożliwić użytkownikowi zalogowanie się do swojego konta lokalnego podczas zachowywania konta w chmurze przez usunięcie konta lokalnego. Zamiast tego można ustawić hasło konta lokalnego na losowy identyfikator GUID lub inną nieznaną wartość.

## <a name="how-to-invite-internal-users-to-b2b-collaboration"></a>Jak zaprosić użytkowników wewnętrznych do współpracy B2B

Możesz użyć programu PowerShell lub interfejsu API zaproszenia do wysłania zaproszenia B2B do użytkownika wewnętrznego. Upewnij się, że adres e-mail, który ma być używany dla zaproszenia, jest ustawiony jako zewnętrzny adres e-mail w obiekcie użytkownika wewnętrznego.

- Musisz użyć adresu e-mail we właściwości User. mail dla zaproszenia.
- Domena we właściwościach poczty użytkownika musi być zgodna z kontem używanym do logowania. W przeciwnym razie niektóre usługi, takie jak zespoły, nie będą mogły uwierzytelnić użytkownika.

Domyślnie zaproszenie wyśle użytkownikowi wiadomość e-mail z informacją o tym, że zostały zaproszone, ale możesz pominąć tę wiadomość e-mail i wysłać własne.

> [!NOTE]
> W celu wysłania własnej poczty e-mail lub innej komunikacji możesz użyć programu `New-AzureADMSInvitation` z usługą, `-SendInvitationMessage:$false` Aby zapraszać użytkowników w trybie dyskretnym, a następnie wysyłać własne wiadomości e-mail do konwertowanego użytkownika. Zobacz [interfejs API współpracy i dostosowywanie usługi Azure AD B2B](customize-invitation-api.md).

## <a name="use-powershell-to-send-a-b2b-invitation"></a>Wysyłanie zaproszenia B2B przy użyciu programu PowerShell

Będziesz potrzebować modułu Azure AD PowerShell w wersji 2.0.2.130 lub nowszej. Użyj poniższego polecenia, aby zaktualizować do najnowszego modułu programu AzureAD PowerShell i zaprosić użytkownika wewnętrznego do współpracy B2B:

```powershell
Uninstall-Module AzureAD
Install-Module AzureAD
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
