---
title: Rozwiązywanie problemów z weryfikacją wydawcy | Azure
titleSuffix: Microsoft identity platform
description: Opisuje sposób rozwiązywania problemów z weryfikacją wydawcy dla platformy tożsamości firmy Microsoft przez wywołanie Microsoft Graph interfejsów API.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: troubleshooting
ms.workload: identity
ms.date: 01/28/2021
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jesakowi
ms.openlocfilehash: acce282dcaac971716a97d7722543d78e28b7a34
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102517671"
---
# <a name="troubleshoot-publisher-verification"></a>Rozwiązywanie problemów z weryfikacją wydawcy
Jeśli nie można zakończyć procesu lub występują nieoczekiwane zachowanie podczas [weryfikacji wydawcy](publisher-verification-overview.md), należy zacząć od następującej procedury, Jeśli otrzymujesz błędy lub widzisz nieoczekiwane zachowanie: 

1. Zapoznaj się z [wymaganiami](publisher-verification-overview.md#requirements) i upewnij się, że zostały one spełnione.

1. Zapoznaj się z instrukcjami, aby [oznaczyć aplikację jako zweryfikowaną przez wydawcę](mark-app-as-publisher-verified.md) i upewnić się, że wszystkie kroki zostały wykonane pomyślnie.

1. Zapoznaj się z listą [typowych problemów](#common-issues).

1. Odtwórz żądanie przy użyciu [Eksploratora grafów](#making-microsoft-graph-api-calls) , aby zebrać dodatkowe informacje i wykluczyć wszelkie problemy w interfejsie użytkownika.

## <a name="common-issues"></a>Typowe problemy
Poniżej przedstawiono niektóre typowe problemy, które mogą wystąpić w trakcie procesu. 

- **Nie znam mojego identyfikatora Microsoft Partner Network (identyfikator MPN) lub nie wiem, kim jest główny kontakt dla konta** 
    1. Przejdź do [strony rejestracji MPN](https://partner.microsoft.com/dashboard/account/v3/enrollment/joinnow/basicpartnernetwork/new)
    1. Zaloguj się przy użyciu konta użytkownika w podstawowej dzierżawie usługi Azure AD w organizacji 
    1. Jeśli konto MPN już istnieje, zostanie ono rozpoznane i zostanie dodane do konta 
    1. Przejdź do [strony profilu partnera](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) , w której zostanie wyświetlony identyfikator MPN i kontakt z kontem głównym

- **Nie wiem, kto jest moim administratorem globalnym usługi Azure AD (znanym także jako administrator firmy lub Administrator dzierżawy), jak je znaleźć? Co z administratorem aplikacji lub administratorem aplikacji w chmurze?**
    1. Zaloguj się do [portalu usługi Azure AD](https://aad.portal.azure.com) przy użyciu konta użytkownika w podstawowej dzierżawie Twojej organizacji
    1. Przejdź do [zarządzania rolami](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators)
    1. Kliknij żądaną rolę administratora.
    1. Zostanie wyświetlona lista użytkowników, którym przypisano tę rolę

- **Nie wiem, komu Administratorzy dla mojego konta usługi MPN** Przejdź do [strony zarządzania użytkownikami MPN](https://partner.microsoft.com/pcv/users) i przefiltruj listę użytkowników, aby zobaczyć, którzy użytkownicy znajdują się w różnych rolach administratora.

- **Otrzymuję komunikat o błędzie informujący, że mój identyfikator MPN jest nieprawidłowy lub nie ma do niego dostępu.**
    1. Przejdź do [profilu partnera](https://partner.microsoft.com/pcv/accountsettings/connectedpartnerprofile) i sprawdź, czy: 
        - IDENTYFIKATOR MPN jest poprawny. 
        - Nie ma żadnych błędów lub "oczekujących akcji", a stan weryfikacji w obszarze informacje o profilu biznesowym i partnerze są "autoryzowane" lub "powodzenie".
    1. Przejdź do [strony zarządzania dzierżawcą MPN](https://partner.microsoft.com/dashboard/account/v3/tenantmanagement) i upewnij się, że dzierżawa, w której zarejestrowano aplikację, i podpisywanie przy użyciu konta użytkownika znajduje się na liście skojarzonych dzierżawców. Aby dodać dodatkową dzierżawę, postępuj zgodnie z instrukcjami znajdującymi się [tutaj](/partner-center/multi-tenant-account). Należy pamiętać, że wszyscy administratorzy globalni dowolnie dodanej dzierżawy otrzymają uprawnienia administratora globalnego na Twoim koncie Centrum partnerskiego.
    1. Przejdź do [strony zarządzania użytkownikami MPN](https://partner.microsoft.com/pcv/users) i Potwierdź, że zalogowany użytkownik jest administratorem globalnym, administratorem MPN lub administratorem kont. Aby dodać użytkownika do roli w centrum partnerskim, postępuj zgodnie z instrukcjami znajdującymi się [tutaj](/partner-center/create-user-accounts-and-set-permissions).

- **Po zalogowaniu się do portalu usługi Azure AD nie są wyświetlane żadne aplikacje zarejestrowane. Zalet?** 
    Rejestracje aplikacji mogły zostać utworzone przy użyciu innego konta użytkownika w tej dzierżawie, konta osobistego/konsumenta lub w innej dzierżawie. Upewnij się, że zalogowano się przy użyciu odpowiedniego konta w dzierżawie, w którym zostały utworzone rejestracje aplikacji.

- **Otrzymuję błąd związany z uwierzytelnianiem wieloskładnikowym. Co mam zrobić?** 
    Upewnij się, że [uwierzytelnianie wieloskładnikowe](../fundamentals/concept-fundamentals-mfa-get-started.md) jest włączone i **wymagane** dla użytkownika, za pomocą którego logujesz się w tym scenariuszu. Na przykład uwierzytelnianie wieloskładnikowe może być następujące:
    - Zawsze wymagane dla użytkownika, za pomocą którego się logujesz
    - [Wymagane do zarządzania platformą Azure](../conditional-access/howto-conditional-access-policy-azure-management.md).
    - [Wymagane dla typu administratora](../conditional-access/howto-conditional-access-policy-admin-mfa.md) , za pomocą którego się logujesz.

## <a name="making-microsoft-graph-api-calls"></a>Wykonywanie wywołań interfejsu API Microsoft Graph 

Jeśli występuje problem, ale nie można zrozumieć, dlaczego jest on oparty na tym, co widzisz w interfejsie użytkownika, może być przydatne dalsze Rozwiązywanie problemów przy użyciu Microsoft Graph wywołań, aby wykonać te same operacje, które można wykonać w portalu rejestracji aplikacji.

Najprostszym sposobem, aby te żądania były przy użyciu [Eksploratora grafów](https://developer.microsoft.com/graph/graph-explorer). Możesz również rozważyć inne opcje, takie jak użycie programu [Poster](https://www.postman.com/)lub [wywołanie żądania sieci Web](/powershell/module/microsoft.powershell.utility/invoke-webrequest)za pomocą środowiska PowerShell.  

Możesz użyć Microsoft Graph do ustawiania i cofania zweryfikowanego wydawcy aplikacji i sprawdzenia wyniku po wykonaniu jednej z tych operacji. Wynik może być widoczny zarówno dla obiektu [aplikacji](/graph/api/resources/application) odpowiadającego rejestracji aplikacji, jak i dla wszystkich [podmiotów usługi](/graph/api/resources/serviceprincipal) , które zostały utworzone na podstawie tej aplikacji. Aby uzyskać więcej informacji na temat relacji między tymi obiektami, zobacz: [obiekty główne aplikacji i usługi w Azure Active Directory](app-objects-and-service-principals.md).  

Oto przykłady pewnych przydatnych żądań:  

### <a name="set-verified-publisher"></a>Ustaw zweryfikowanego wydawcę 

Żądanie

```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/setVerifiedPublisher 

{ 

    "verifiedPublisherId": "12345678" 

} 
```
 
Reakcja 
```
204 No Content 
```
> [!NOTE]
> *verifiedPublisherID* jest identyfikatorem MPN. 

### <a name="unset-verified-publisher"></a>Cofnij ustawienie zweryfikowanego wydawcy 

Żądanie:  
```
POST /applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec/unsetVerifiedPublisher 
```
 
Reakcja 
```
204 No Content 
```
### <a name="get-verified-publisher-info-from-application"></a>Uzyskaj informacje o zweryfikowanym wydawcy z aplikacji 
 
```
GET https://graph.microsoft.com/v1.0/applications/0cd04273-0d11-4e62-9eb3-5c3971a7cbec 

HTTP/1.1 200 OK 

{ 
    "id": "0cd04273-0d11-4e62-9eb3-5c3971a7cbec", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

### <a name="get-verified-publisher-info-from-service-principal"></a>Uzyskaj informacje o zweryfikowanym wydawcy z jednostki usługi 
```
GET https://graph.microsoft.com/v1.0/servicePrincipals/010422a7-4d77-4f40-9335-b81ef5c23dd4 

HTTP/1.1 200 OK 

{ 
    "id": "010422a7-4d77-4f40-9335-b81ef5c22dd4", 

    ... 

    "verifiedPublisher" : { 
        "displayName": "myexamplePublisher", 
        "verifiedPublisherId": "12345678", 
        "addedDateTime": "2019-12-10T00:00:00" 
    } 
} 
```

## <a name="error-reference"></a>Informacje o błędzie 

Poniżej znajduje się lista potencjalnych kodów błędów, które mogą zostać wyświetlone podczas rozwiązywania problemów z Microsoft Graph lub przechodzenia przez proces w portalu rejestracji aplikacji.

### <a name="mpnaccountnotfoundornoaccess"></a>MPNAccountNotFoundOrNoAccess

Podany identyfikator MPN () nie `MPNID` istnieje lub nie masz do niego dostępu. Podaj prawidłowy identyfikator MPN i spróbuj ponownie.
    
Najczęściej spowodowane przez zalogowanego użytkownika nie jest członkiem właściwej roli dla konta MPN w centrum partnerskim — Aby uzyskać więcej informacji, zobacz [wymagania](publisher-verification-overview.md#requirements) dotyczące listy kwalifikujących się ról i zobacz [typowe problemy](#common-issues) . Może być także spowodowany przez dzierżawę, w której aplikacja jest zarejestrowana, nie jest dodawana do konta MPN lub nieprawidłowy identyfikator MPN.

### <a name="mpnglobalaccountnotfound"></a>MPNGlobalAccountNotFound

Podany identyfikator MPN ( `MPNID` ) jest nieprawidłowy. Podaj prawidłowy identyfikator MPN i spróbuj ponownie.
    
Najczęściej jest to spowodowane tym, że podany identyfikator MPN odnosi się do konta lokalizacji partnera (PLA). Obsługiwane są tylko konta globalne partnera. Aby uzyskać więcej informacji, zobacz [strukturę konta Centrum partnerskiego](/partner-center/account-structure) .

### <a name="mpnaccountinvalid"></a>MPNAccountInvalid

Podany identyfikator MPN ( `MPNID` ) jest nieprawidłowy. Podaj prawidłowy identyfikator MPN i spróbuj ponownie.
    
Najczęściej przyczyną jest podano nieprawidłowy identyfikator MPN.

### <a name="mpnaccountnotvetted"></a>MPNAccountNotVetted

Podany identyfikator MPN ( `MPNID` ) nie ukończył procesu przed sprawdzeniem. Ukończ ten proces w centrum partnerskim i spróbuj ponownie. 
    
Najczęściej jest to spowodowane tym, że konto MPN nie ukończyło procesu [weryfikacji](/partner-center/verification-responses) .

### <a name="nopublisheridonassociatedmpnaccount"></a>NoPublisherIdOnAssociatedMPNAccount

Podany identyfikator MPN ( `MPNID` ) jest nieprawidłowy. Podaj prawidłowy identyfikator MPN i spróbuj ponownie. 
   
Najczęściej przyczyną jest podano nieprawidłowy identyfikator MPN.

### <a name="mpniddoesnotmatchassociatedmpnaccount"></a>MPNIdDoesNotMatchAssociatedMPNAccount

Podany identyfikator MPN ( `MPNID` ) jest nieprawidłowy. Podaj prawidłowy identyfikator MPN i spróbuj ponownie.
    
Najczęściej przyczyną jest podano nieprawidłowy identyfikator MPN.

### <a name="applicationnotfound"></a>ApplicationNotFound

Nie można znaleźć aplikacji docelowej ( `AppId` ). Podaj prawidłowy identyfikator aplikacji i spróbuj ponownie.
    
Najczęściej jest to spowodowane tym, że weryfikacja odbywa się za pośrednictwem interfejs API programu Graph, a identyfikator podanej aplikacji jest nieprawidłowy. Uwaga — należy podać identyfikator aplikacji, a nie AppId/ClientId.

### <a name="b2ctenantnotallowed"></a>B2CTenantNotAllowed

Ta funkcja nie jest obsługiwana w dzierżawie Azure AD B2C.

### <a name="emailverifiedtenantnotallowed"></a>EmailVerifiedTenantNotAllowed

Ta funkcja nie jest obsługiwana w przypadku zweryfikowanej dzierżawy poczty e-mail.

### <a name="nopublisherdomainonapplication"></a>NoPublisherDomainOnApplication

Aplikacja docelowa ( `AppId` ) musi mieć ustawioną domenę wydawcy. Ustaw domenę wydawcy i spróbuj ponownie.

Występuje, gdy [domena wydawcy](howto-configure-publisher-domain.md) nie jest skonfigurowana w aplikacji.

### <a name="publisherdomainmismatch"></a>PublisherDomainMismatch

Domena wydawcy aplikacji docelowej () nie jest `publisherDomain` zgodna z domeną używaną do weryfikacji wiadomości e-mail w centrum partnerskim ( `pcDomain` ). Upewnij się, że te domeny pasują do siebie, i spróbuj ponownie. 
    
Występuje, gdy żadna [domena wydawcy](howto-configure-publisher-domain.md) aplikacji ani jedna z [domen niestandardowych](../fundamentals/add-custom-domain.md) nie została dodana do DZIERŻAWy usługi Azure AD nie jest zgodna z domeną używaną do weryfikacji wiadomości e-mail w centrum partnerskim.

### <a name="notauthorizedtoverifypublisher"></a>NotAuthorizedToVerifyPublisher

Nie masz uprawnień do ustawiania zweryfikowanej właściwości wydawcy dla aplikacji (<`AppId` ) 
  
Najczęściej spowodowane przez zalogowanego użytkownika nie jest członkiem właściwej roli dla konta MPN w usłudze Azure AD — Zobacz [wymagania](publisher-verification-overview.md#requirements) dotyczące listy kwalifikujących się ról i zobacz [typowe problemy](#common-issues) , aby uzyskać więcej informacji.

### <a name="mpnidwasnotprovided"></a>MPNIdWasNotProvided

Nie podano identyfikatora MPN w treści żądania lub typem zawartości żądania nie był "Application/JSON".

### <a name="msanotsupported"></a>MSANotSupported 

Ta funkcja nie jest obsługiwana w przypadku kont użytkowników Microsoft. Obsługiwane są tylko aplikacje zarejestrowane w usłudze Azure AD za pomocą użytkownika usługi Azure AD.

### <a name="interactionrequired"></a>InteractionRequired

Występuje, gdy nie wykonano uwierzytelniania wieloskładnikowego przed podjęciem próby dodania zweryfikowanego wydawcy do aplikacji. Aby uzyskać więcej informacji, zobacz [typowe problemy](#common-issues) . Uwaga: usługi MFA należy wykonać w tej samej sesji podczas próby dodania zweryfikowanego wydawcy. Jeśli funkcja MFA jest włączona, ale nie jest wymagana do wykonania w sesji, żądanie zakończy się niepowodzeniem. 

Wyświetlany jest komunikat o błędzie: "z powodu zmiany konfiguracji wprowadzonej przez administratora lub przeniesiono ją do nowej lokalizacji, należy użyć uwierzytelniania wieloskładnikowego, aby przejść do".

### <a name="unabletoaddpublisher"></a>UnableToAddPublisher

Wyświetlany komunikat o błędzie: "zweryfikowanego wydawcy nie można dodać do tej aplikacji. Skontaktuj się z administratorem, aby uzyskać pomoc ".

Najpierw sprawdź, czy zostały spełnione [wymagania dotyczące weryfikacji wydawcy](publisher-verification-overview.md#requirements).

Gdy zostanie wykonane żądanie dodania zweryfikowanego wydawcy, do oceny ryzyka bezpieczeństwa są używane różne sygnały. Jeśli żądanie zostanie uznane za ryzykowne, zostanie zwrócony błąd. Ze względów bezpieczeństwa firma Microsoft nie ujawnia określonych kryteriów, które są używane do określenia, czy żądanie jest ryzykowne czy nie.

## <a name="next-steps"></a>Następne kroki

Jeśli zostały sprawdzone wszystkie poprzednie informacje i nadal pojawiają się błędy z Microsoft Graph, Zbierz możliwie najwięcej z poniższych informacji związanych z błędnym żądaniem i [skontaktuj się z pomocą techniczną firmy Microsoft](developer-support-help-options.md#create-an-azure-support-request).

- Znacznik czasu 
- CorrelationId 
- ObjectID lub UserPrincipalName zalogowanego użytkownika 
- Identyfikator obiektu aplikacji docelowej
- Identyfikator aplikacji docelowej
- TenantId, gdzie zarejestrowano aplikację
- Identyfikator MPN
- Podejmowane żądania REST 
- Zwrócony kod błędu i komunikat