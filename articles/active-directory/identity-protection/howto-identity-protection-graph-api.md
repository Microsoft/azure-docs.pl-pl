---
title: Microsoft Graph interfejs API dla Azure Active Directory Identity Protection
description: Dowiedz się, jak badać Microsoft Graph wykrywanie ryzyka i powiązane informacje z Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 10/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: e16f33cb8aa7c6ceeb1398dd23ccba31b5f936b1
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776140"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Wprowadzenie do Azure Active Directory Identity Protection i Microsoft Graph

Microsoft Graph to punkt końcowy Microsoft Unified API i Strona główna [Azure Active Directory Identity Protection](./overview-identity-protection.md) interfejsów API. Istnieją trzy interfejsy API, które ujawniają informacje o ryzykownych użytkownikach i logowaniach. Pierwszy interfejs API, **riskDetection**, umożliwia wysyłanie zapytań do Microsoft Graph, aby uzyskać listę wykrytych i powiązanych z nimi wykrytych zagrożeń oraz informacje o wykrywaniu. Drugi interfejs API, **riskyUsers**, umożliwia wysyłanie zapytań do Microsoft Graph, aby uzyskać informacje o ochronie tożsamości użytkowników wykryte jako ryzyko. Trzeci interfejs API, usługa **logowania**, umożliwia wysyłanie zapytań do Microsoft Graph, aby uzyskać informacje na temat logowania do usługi Azure AD z określonymi właściwościami związanymi ze stanem ryzyka, szczegółami i poziomami. 

W tym artykule opisano rozpoczęcie łączenia się z Microsoft Graph i wykonywania zapytań dotyczących tych interfejsów API. Aby uzyskać szczegółowe informacje, pełną dokumentację i dostęp do Eksploratora grafów, zapoznaj się z [witryną Microsoft Graph](https://graph.microsoft.io/) lub z konkretną dokumentacją dotyczącą tych interfejsów API:

* [Interfejs API riskDetection](/graph/api/resources/riskdetection?view=graph-rest-v1.0)
* [Interfejs API riskyUsers](/graph/api/resources/riskyuser?view=graph-rest-v1.0)
* [Interfejs API signIn](/graph/api/resources/signin?view=graph-rest-v1.0)

## <a name="connect-to-microsoft-graph"></a>Połącz z programem Microsoft Graph

Aby uzyskać dostęp do danych ochrony tożsamości za poorednictwem Microsoft Graph, należy wykonać cztery kroki:

- [Pobierz nazwę domeny](#retrieve-your-domain-name)
- [Utwórz nową rejestrację aplikacji](#create-a-new-app-registration)
- [Konfigurowanie uprawnień interfejsu API](#configure-api-permissions)
- [Skonfiguruj prawidłowe poświadczenie](#configure-a-valid-credential)

### <a name="retrieve-your-domain-name"></a>Pobierz nazwę domeny 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).  
1. Przejdź do **Azure Active Directory**  >  **niestandardowych nazw domen**. 
1. Zanotuj `.onmicrosoft.com` tę domenę. te informacje będą potrzebne w późniejszym kroku.

### <a name="create-a-new-app-registration"></a>Utwórz nową rejestrację aplikacji

1. W Azure Portal przejdź do **Azure Active Directory**  >  **rejestracje aplikacji**.
1. Wybierz pozycję **Nowa rejestracja**.
1. Na stronie **Tworzenie** wykonaj następujące czynności:
   1. W polu tekstowym **Nazwa** wpisz nazwę aplikacji (na przykład: interfejs API wykrywania ryzyka usługi Azure AD).
   1. W obszarze **obsługiwane typy kont**wybierz typ kont, które będą używać interfejsów API.
   1. Wybierz pozycję **Zarejestruj**.
1. Skopiuj **Identyfikator aplikacji**.

### <a name="configure-api-permissions"></a>Konfigurowanie uprawnień interfejsu API

1. Z utworzonej **aplikacji** wybierz pozycję **uprawnienia interfejsu API**.
1. Na stronie **skonfigurowane uprawnienia** na pasku narzędzi u góry kliknij pozycję **Dodaj uprawnienie**.
1. Na stronie **Dodawanie dostępu do interfejsu API** kliknij pozycję **Wybierz interfejs API**.
1. Na stronie **Wybierz interfejs API** wybierz opcję **Microsoft Graph**, a następnie kliknij przycisk **Wybierz**.
1. Na stronie **uprawnienia interfejsu API żądania** : 
   1. Wybierz pozycję **Uprawnienia aplikacji**.
   1. Zaznacz pola wyboru obok `IdentityRiskEvent.Read.All` i `IdentityRiskyUser.Read.All` .
   1. Wybierz pozycję **Dodaj uprawnienia**.
1. Wybierz pozycję **Udziel zgody administratora na domenę** 

### <a name="configure-a-valid-credential"></a>Skonfiguruj prawidłowe poświadczenie

1. Z utworzonej **aplikacji** wybierz pozycję **Certyfikaty & wpisy tajne**.
1. W obszarze wpisy **tajne klienta**wybierz pozycję **nowy klucz tajny klienta**.
   1. Podaj wpis tajny klienta w **opisie** i Ustaw okres czasu wygaśnięcia zgodnie z zasadami organizacji.
   1. Wybierz pozycję **Dodaj**.

   > [!NOTE]
   > Jeśli ten klucz zostanie utracony, musisz wrócić do tej sekcji i utworzyć nowy klucz. Zachowaj klucz tajny: każdy, kto ma dostęp do danych.

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-detections-api"></a>Uwierzytelnianie w celu Microsoft Graph i zbadania interfejsu API wykrywania ryzyka tożsamości

W tym momencie należy:

- Nazwa domeny dzierżawy
- Identyfikator aplikacji (klienta) 
- Klucz tajny klienta lub certyfikat 

Aby przeprowadzić uwierzytelnianie, Wyślij żądanie post do `https://login.microsoft.com` następujących parametrów w treści:

- grant_type: "**client_credentials**"
- zasoby `https://graph.microsoft.com`
- client_id: \<your client ID\>
- client_secret: \<your key\>

Jeśli to się powiedzie, żądanie zwróci token uwierzytelniania.  
Aby wywołać interfejs API, Utwórz nagłówek z następującym parametrem:

```
`Authorization`="<token_type> <access_token>"
```

Podczas uwierzytelniania można znaleźć token tokenu i tokenu dostępu w zwracanym tokenie.

Wyślij ten nagłówek jako żądanie do następującego adresu URL interfejsu API: `https://graph.microsoft.com/v1.0/identityProtection/riskDetections`

Odpowiedź, jeśli to się powiedzie, jest kolekcją wykrywania ryzyka tożsamości i skojarzonych danych w formacie JSON OData, który można przeanalizować i obsłużyć zgodnie z oczekiwaniami.

### <a name="sample"></a>Przykład

Ten przykład pokazuje użycie wspólnego klucza tajnego do uwierzytelniania. W środowisku produkcyjnym przechowywanie wpisów tajnych w kodzie jest zwykle niezadowolenie. Organizacje mogą korzystać z zarządzanych tożsamości dla zasobów platformy Azure, aby zabezpieczyć te poświadczenia. Aby uzyskać więcej informacji o tożsamościach zarządzanych, zobacz artykuł [dotyczący tożsamości zarządzanych dla zasobów platformy Azure](../managed-identities-azure-resources/overview.md).

Oto przykładowy kod służący do uwierzytelniania i wywoływania interfejsu API przy użyciu programu PowerShell.  
Po prostu Dodaj swój identyfikator klienta, klucz tajny i domenę dzierżawy.

```PowerShell
    $ClientID       = "<your client ID here>"        # Should be a ~36 hex character string; insert your info here
    $ClientSecret   = "<your client secret here>"    # Should be a ~44 character string; insert your info here
    $tenantdomain   = "<your tenant domain here>"    # For example, contoso.onmicrosoft.com

    $loginURL       = "https://login.microsoft.com"
    $resource       = "https://graph.microsoft.com"

    $body       = @{grant_type="client_credentials";resource=$resource;client_id=$ClientID;client_secret=$ClientSecret}
    $oauth      = Invoke-RestMethod -Method Post -Uri $loginURL/$tenantdomain/oauth2/token?api-version=1.0 -Body $body

    Write-Output $oauth

    if ($oauth.access_token -ne $null) {
        $headerParams = @{'Authorization'="$($oauth.token_type) $($oauth.access_token)"}

        $url = "https://graph.microsoft.com/v1.0/identityProtection/riskDetections"
        Write-Output $url

        $myReport = (Invoke-WebRequest -UseBasicParsing -Headers $headerParams -Uri $url)

        foreach ($event in ($myReport.Content | ConvertFrom-Json).value) {
            Write-Output $event
        }

    } else {
        Write-Host "ERROR: No Access Token"
    } 
```

## <a name="query-the-apis"></a>Wykonywanie zapytań dotyczących interfejsów API

Te trzy interfejsy API zapewniają wiele możliwości pobierania informacji o ryzykownych użytkownikach i logowaniach w organizacji. Poniżej przedstawiono niektóre typowe przypadki użycia tych interfejsów API i skojarzone przykładowe żądania. Można uruchamiać te zapytania przy użyciu przykładowego kodu powyżej lub za pomocą [Eksploratora grafów](https://developer.microsoft.com/graph/graph-explorer).

### <a name="get-all-of-the-offline-risk-detections-riskdetection-api"></a>Pobierz wszystkie wykrycia ryzyka w trybie offline (riskDetection API)

Zasady dotyczące ryzyka związanego z logowaniem do programu Identity Protection umożliwiają stosowanie warunków w przypadku wykrycia ryzyka w czasie rzeczywistym. Ale informacje o wykryciu wykrywanych w trybie offline? Aby zrozumieć, jakie wykryte wystąpienia wystąpiły w trybie offline i w rezultacie nie wywołały zasad dotyczących ryzyka związanego z logowaniem, można wysłać zapytanie do interfejsu API riskDetection.

```
GET https://graph.microsoft.com/v1.0/identityProtection/riskDetections?$filter=detectionTimingType eq 'offline'
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>Pobierz wszystkich użytkowników, którzy pomyślnie przekazały wyzwanie usługi MFA wyzwalane przez zasady ryzykownego logowania (riskyUsers API)

Aby zrozumieć wpływ zasad opartych na zagrożeniu ochrony tożsamości na organizację, można wysłać zapytanie do wszystkich użytkowników, którzy pomyślnie przekazały wyzwanie usługi MFA wyzwalane przez zasady ryzykownego logowania. Te informacje mogą pomóc w zrozumieniu, którzy użytkownicy mogą być zagrożeni błędnie wykrytymi, a którzy z uprawnionych użytkowników mogą wykonywać działania, które są uważane za ryzykowne.

```
GET https://graph.microsoft.com/v1.0/identityProtection/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

## <a name="next-steps"></a>Następne kroki

Gratulacje, wykonano już pierwsze wywołanie do Microsoft Graph!  
Teraz można wykonywać zapytania dotyczące wykrywania ryzyka tożsamości i używania danych, które są widoczne.

Aby dowiedzieć się więcej o Microsoft Graph i sposobach tworzenia aplikacji przy użyciu interfejs API programu Graph, zapoznaj się z [dokumentacją](/graph/overview) i więcej w [witrynie Microsoft Graph](https://developer.microsoft.com/graph). 

Aby uzyskać powiązane informacje, zobacz:

- [Ochrona tożsamości w usłudze Azure Active Directory](./overview-identity-protection.md)
- [Typy wykrycia ryzyka wykryte przez Azure Active Directory Identity Protection](./overview-identity-protection.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [Omówienie programu Microsoft Graph](https://developer.microsoft.com/graph/docs)
- [Katalog główny usługi Azure AD Identity Protection](/graph/api/resources/identityprotectionroot?view=graph-rest-1.0)