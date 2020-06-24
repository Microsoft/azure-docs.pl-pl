---
title: Microsoft Graph interfejs API dla Azure Active Directory Identity Protection
description: Dowiedz się, jak badać Microsoft Graph wykrywanie ryzyka i powiązane informacje z Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54b78526ea1409f22b000aed8b20db90dfc9d143
ms.sourcegitcommit: bf99428d2562a70f42b5a04021dde6ef26c3ec3a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85253566"
---
# <a name="get-started-with-azure-active-directory-identity-protection-and-microsoft-graph"></a>Wprowadzenie do Azure Active Directory Identity Protection i Microsoft Graph

Microsoft Graph to punkt końcowy Microsoft Unified API i Strona główna [Azure Active Directory Identity Protection](../active-directory-identityprotection.md) interfejsów API. Istnieją cztery interfejsy API, które ujawniają informacje o ryzykownych użytkownikach i logowaniach. Pierwszy interfejs API, **riskDetection**, umożliwia wysyłanie zapytań do Microsoft Graph, aby uzyskać listę wykrytych i powiązanych z nimi wykrytych zagrożeń oraz informacje o wykrywaniu. Drugi interfejs API, **riskyUsers**, umożliwia wysyłanie zapytań do Microsoft Graph, aby uzyskać informacje o ochronie tożsamości użytkowników wykryte jako ryzyko. Trzeci interfejs API, usługa **logowania**, umożliwia wysyłanie zapytań do Microsoft Graph, aby uzyskać informacje na temat logowania do usługi Azure AD z określonymi właściwościami związanymi ze stanem ryzyka, szczegółami i poziomami. Czwarty interfejs API, **identityRiskEvents**, umożliwia wysyłanie zapytań do Microsoft Graph, aby uzyskać listę wykrytych [zagrożeń](../reports-monitoring/concept-risk-events.md) i związanych z nimi informacji. Interfejs API identityRiskEvents zostanie uznany za przestarzały 10 stycznia 2020; Zalecamy użycie interfejsu API **riskDetections** . W tym artykule opisano rozpoczęcie łączenia się z Microsoft Graph i wykonywania zapytań dotyczących tych interfejsów API. Aby uzyskać szczegółowe informacje, pełną dokumentację i dostęp do Eksploratora grafów, zapoznaj się z [witryną Microsoft Graph](https://graph.microsoft.io/) lub z konkretną dokumentacją dotyczącą tych interfejsów API:

* [Interfejs API riskDetection](/graph/api/resources/riskdetection?view=graph-rest-beta)
* [Interfejs API riskyUsers](/graph/api/resources/riskyuser?view=graph-rest-beta)
* [Interfejs API logowania](/graph/api/resources/signin?view=graph-rest-beta)
* [interfejs API identityRiskEvents](/graph/api/resources/identityriskevent?view=graph-rest-beta) *będzie przestarzały 10 stycznia 2020*

## <a name="connect-to-microsoft-graph"></a>Połącz z programem Microsoft Graph

Aby uzyskać dostęp do danych ochrony tożsamości za poorednictwem Microsoft Graph, należy wykonać cztery kroki:

1. Pobierz nazwę domeny.
2. Utwórz nową rejestrację aplikacji. 
3. Użyj tego wpisu tajnego i kilku innych informacji do uwierzytelnienia w Microsoft Graph, gdzie otrzymujesz token uwierzytelniania. 
4. Użyj tego tokenu, aby przetworzyć żądania do punktu końcowego interfejsu API i odzyskać dane z usługi Identity Protection.

Przed rozpoczęciem należy:

* Uprawnienia administratora do tworzenia aplikacji w usłudze Azure AD
* Nazwa domeny dzierżawy (na przykład contoso.onmicrosoft.com)

## <a name="retrieve-your-domain-name"></a>Pobierz nazwę domeny 

1. [Zaloguj](https://portal.azure.com) się do Azure Portal jako administrator. 
1. W okienku nawigacji po lewej stronie kliknij pozycję **Active Directory**. 

   ![Tworzenie aplikacji](./media/howto-identity-protection-graph-api/41.png)

1. W sekcji **Zarządzanie** kliknij pozycję **Właściwości**.

   ![Tworzenie aplikacji](./media/howto-identity-protection-graph-api/42.png)

1. Skopiuj nazwę domeny.

## <a name="create-a-new-app-registration"></a>Utwórz nową rejestrację aplikacji

1. Na stronie **Active Directory** w sekcji **zarządzanie** kliknij pozycję **rejestracje aplikacji**.

   ![Tworzenie aplikacji](./media/howto-identity-protection-graph-api/42.png)

1. W menu u góry kliknij pozycję **rejestracja nowej aplikacji**.

   ![Tworzenie aplikacji](./media/howto-identity-protection-graph-api/43.png)

1. Na stronie **Tworzenie** wykonaj następujące czynności:

   ![Tworzenie aplikacji](./media/howto-identity-protection-graph-api/44.png)

   1. W polu tekstowym **Nazwa** wpisz nazwę aplikacji (na przykład: aplikacja interfejsu API wykrywania ryzyka usługi Azure AD).

   1. Jako **Typ**wybierz pozycję **aplikacja sieci Web i/lub interfejs API sieci Web**.

   1. W polu tekstowym **adres URL logowania** wpisz `http://localhost` .

   1. Kliknij pozycję **Utwórz**.
1. Aby otworzyć stronę **Ustawienia** , na liście Aplikacje kliknij nowo utworzoną rejestrację aplikacji. 
1. Skopiuj **Identyfikator aplikacji**.

## <a name="grant-your-application-permission-to-use-the-api"></a>Przyznaj aplikacji uprawnienia do korzystania z interfejsu API

1. Na stronie **Ustawienia** kliknij pozycję **wymagane uprawnienia**.

   ![Tworzenie aplikacji](./media/howto-identity-protection-graph-api/15.png)

1. Na stronie **wymagane uprawnienia** na pasku narzędzi u góry kliknij przycisk **Dodaj**.

   ![Tworzenie aplikacji](./media/howto-identity-protection-graph-api/16.png)

1. Na stronie **Dodawanie dostępu do interfejsu API** kliknij pozycję **Wybierz interfejs API**.

   ![Tworzenie aplikacji](./media/howto-identity-protection-graph-api/17.png)

1. Na stronie **Wybierz interfejs API** wybierz opcję **Microsoft Graph**, a następnie kliknij przycisk **Wybierz**.

   ![Tworzenie aplikacji](./media/howto-identity-protection-graph-api/18.png)

1. Na stronie **Dodawanie dostępu do interfejsu API** kliknij pozycję **Wybierz uprawnienia**.

   ![Tworzenie aplikacji](./media/howto-identity-protection-graph-api/19.png)

1. Na stronie **Włączanie dostępu** kliknij pozycję **Odczytaj wszystkie informacje o ryzyku tożsamości**, a następnie kliknij przycisk **Wybierz**.

   ![Tworzenie aplikacji](./media/howto-identity-protection-graph-api/20.png)

1. Na stronie **Dodawanie dostępu do interfejsu API** kliknij przycisk **gotowe**.

   ![Tworzenie aplikacji](./media/howto-identity-protection-graph-api/21.png)

1. Na stronie **wymagane uprawnienia** kliknij pozycję **Udziel uprawnień**, a następnie kliknij przycisk **tak**.

   ![Tworzenie aplikacji](./media/howto-identity-protection-graph-api/22.png)

## <a name="get-an-access-key"></a>Uzyskiwanie klucza dostępu

1. Na stronie **Ustawienia** kliknij pozycję **klucze**.

   ![Tworzenie aplikacji](./media/howto-identity-protection-graph-api/23.png)

1. Na stronie **klucze** wykonaj następujące czynności:

   ![Tworzenie aplikacji](./media/howto-identity-protection-graph-api/24.png)

   1. W polu tekstowym **Opis klucza** wpisz opis (na przykład *wykrywanie ryzyka w usłudze Azure AD*).
   1. Jako **czas trwania**wybierz **za 1 rok**.
   1. Kliknij pozycję **Zapisz**.
   1. Skopiuj wartość klucza, a następnie wklej ją do bezpiecznej lokalizacji.   
   
   > [!NOTE]
   > Jeśli ten klucz zostanie utracony, musisz wrócić do tej sekcji i utworzyć nowy klucz. Zachowaj klucz tajny: każdy, kto ma dostęp do danych.
   > 

## <a name="authenticate-to-microsoft-graph-and-query-the-identity-risk-detections-api"></a>Uwierzytelnianie w celu Microsoft Graph i zbadania interfejsu API wykrywania ryzyka tożsamości

W tym momencie należy:

- Nazwa domeny dzierżawy
- Identyfikator klienta 
- Klucz 

Aby przeprowadzić uwierzytelnianie, Wyślij żądanie post do `https://login.microsoft.com` następujących parametrów w treści:

- grant_type: "**client_credentials**"
- zasoby`https://graph.microsoft.com`
- client_id:\<your client ID\>
- client_secret:\<your key\>

Jeśli to się powiedzie, zwraca token uwierzytelniania.  
Aby wywołać interfejs API, Utwórz nagłówek z następującym parametrem:

```
`Authorization`="<token_type> <access_token>"
```

Podczas uwierzytelniania można znaleźć token tokenu i tokenu dostępu w zwracanym tokenie.

Wyślij ten nagłówek jako żądanie do następującego adresu URL interfejsu API:`https://graph.microsoft.com/beta/identityRiskEvents`

Odpowiedź, jeśli to się powiedzie, jest kolekcją wykrywania ryzyka tożsamości i skojarzonych danych w formacie JSON OData, który można przeanalizować i obsłużyć zgodnie z oczekiwaniami.

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

        $url = "https://graph.microsoft.com/beta/identityRiskEvents"
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
GET https://graph.microsoft.com/beta/riskDetections?$filter=detectionTimingType eq 'offline'
```

### <a name="get-all-of-the-users-who-successfully-passed-an-mfa-challenge-triggered-by-risky-sign-ins-policy-riskyusers-api"></a>Pobierz wszystkich użytkowników, którzy pomyślnie przekazały wyzwanie usługi MFA wyzwalane przez zasady ryzykownego logowania (riskyUsers API)

Aby zrozumieć wpływ zasad opartych na zagrożeniu ochrony tożsamości na organizację, można wysłać zapytanie do wszystkich użytkowników, którzy pomyślnie przekazały wyzwanie usługi MFA wyzwalane przez zasady ryzykownego logowania. Te informacje mogą pomóc w zrozumieniu, którzy użytkownicy mogą być zagrożeni błędnie wykrytymi, a którzy z uprawnionych użytkowników mogą wykonywać działania, które są uważane za ryzykowne.

```
GET https://graph.microsoft.com/beta/riskyUsers?$filter=riskDetail eq 'userPassedMFADrivenByRiskBasedPolicy'
```

### <a name="get-all-the-risky-sign-ins-for-a-specific-user-signin-api"></a>Pobierz wszystkie ryzykowne logowania dla określonego użytkownika (interfejs API logowania)

W przypadku podejrzenia, że użytkownik mógł zostać naruszony, można lepiej zrozumieć stan swojego ryzyka, pobierając wszystkie ryzykowne logowania. 

```
https://graph.microsoft.com/beta/identityRiskEvents?`$filter=userID eq '<userID>' and riskState eq 'atRisk'
```
## <a name="next-steps"></a>Następne kroki

Gratulacje, wykonano już pierwsze wywołanie do Microsoft Graph!  
Teraz można wykonywać zapytania dotyczące wykrywania ryzyka tożsamości i używania danych, które są widoczne.

Aby dowiedzieć się więcej o Microsoft Graph i sposobach tworzenia aplikacji przy użyciu interfejs API programu Graph, zapoznaj się z [dokumentacją](/graph/overview) i więcej w [witrynie Microsoft Graph](https://developer.microsoft.com/graph). 

Aby uzyskać powiązane informacje, zobacz:

- [Ochrona tożsamości w usłudze Azure Active Directory](../active-directory-identityprotection.md)
- [Typy wykrycia ryzyka wykryte przez Azure Active Directory Identity Protection](../reports-monitoring/concept-risk-events.md)
- [Microsoft Graph](https://developer.microsoft.com/graph/)
- [Overview of Microsoft Graph](https://developer.microsoft.com/graph/docs) (Omówienie programu Microsoft Graph)
- [Katalog główny usługi Azure AD Identity Protection](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/identityprotection_root)
