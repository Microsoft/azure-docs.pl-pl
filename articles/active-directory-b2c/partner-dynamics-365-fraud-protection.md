---
title: Samouczek konfigurowania usługi Azure Active Directory B2C microsoft Dynamics 365 Fraud Protection
titleSuffix: Azure AD B2C
description: Samouczek konfigurowania Azure Active Directory B2C usługą Microsoft Dynamics 365 Fraud Protection w celu identyfikowania ryzykownych i fałszywych kont
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 8c9d760ed888eb194ad8f282f180a634e3c09538
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587820"
---
# <a name="tutorial-configure-microsoft-dynamics-365-fraud-protection-with-azure-active-directory-b2c"></a>Samouczek: konfigurowanie usługi Microsoft Dynamics 365 Fraud Protection przy użyciu Azure Active Directory B2C

Ten przykładowy samouczek zawiera wskazówki dotyczące sposobu integracji usługi [Microsoft Dynamics 365 Fraud Protection](https://docs.microsoft.com/dynamics365/fraud-protection/overview) (DFP) z usługą Azure Active Directory (AD) B2C.

Program Microsoft DFP zapewnia klientom możliwość oceny, czy ryzyko próby utworzenia nowych kont i prób zalogowania się do ekosystemu klienta są fałszywe. Ocena DFP firmy Microsoft może być używana przez klienta do blokowania lub zakwestionowania podejrzanych prób utworzenia nowych fałszywych kont lub naruszenia istniejących kont. Ochrona konta obejmuje odciski palców urządzeń z możliwością sztucznej inteligencji, interfejsy API do oceny ryzyka w czasie rzeczywistym, reguły i środowisko listy w celu optymalizacji strategii ryzyka zgodnie z potrzebami biznesowymi klienta oraz kartę wyników do monitorowania skuteczności ochrony przed oszustwami i trendów w ekosystemie klienta.

W tym przykładzie zintegrowamy funkcje ochrony konta programu Microsoft DFP z przepływem Azure AD B2C użytkownika. Usługa będzie zewnętrznie odciskiem palca przy każdej próbie logowania lub rejestracji i będzie obserwować wszelkie wcześniejsze lub obecne podejrzane zachowania. Azure AD B2C wywołuje punkt końcowy decyzji z Microsoft DFP, który zwraca wynik na podstawie wszystkich przeszłych i obecnych zachowań zidentyfikowanego użytkownika, a także reguł niestandardowych określonych w usłudze Microsoft DFP. Azure AD B2C decyzja o zatwierdzeniu na podstawie tego wyniku i przekazuje to samo z powrotem do Microsoft DFP.

## <a name="prerequisites"></a>Wymagania wstępne

Do rozpoczęcia pracy potrzebne są:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz uzyskać bezpłatne [konto](https://azure.microsoft.com/free/).

- Dzierżawa [Azure AD B2C dzierżawy](./tutorial-create-tenant.md). Dzierżawa jest połączona z subskrypcją platformy Azure.

- Uzyskaj subskrypcję programu Microsoft [DFP.](https://dynamics.microsoft.com/pricing/#Sales) Możesz również skonfigurować wersję [próbną](https://dynamics.microsoft.com/ai/fraud-protection/signin/?RU=https%3A%2F%2Fdfp.microsoft.com%2Fsignin) klienta.

## <a name="scenario-description"></a>Opis scenariusza

Integracja programu Microsoft DFP obejmuje następujące składniki:

- **Azure AD B2C dzierżawy:** uwierzytelnia użytkownika i działa jako klient programu Microsoft DFP. Hostuje skrypt odcisku palca zbierający dane identyfikacyjne i diagnostyczne każdego użytkownika, który wykonuje zasady docelowe. Późniejsze blokowanie lub wyzwania logowania lub rejestracji prób, jeśli Microsoft DFP znajdzie je podejrzanie.

- **Niestandardowa usługa aplikacji:** aplikacja internetowa, która służy do dwóch celów.

  - Obsługuje strony HTML do Identity Experience Framework interfejsu użytkownika. Odpowiada za osadzanie skryptu odcisku palca usługi Microsoft Dynamics 365.

  - Kontroler interfejsu API z punktami końcowymi RESTful, który łączy program Microsoft DFP z Azure AD B2C. Obsługa przetwarzania danych, struktury i jest zgodna z wymaganiami bezpieczeństwa obu tych zasad.

- Usługa odcisków palców Microsoft **DFP:** dynamicznie osadzony skrypt, który rejestruje dane telemetryczne urządzenia i samodzielnie potwierdzane szczegóły użytkownika w celu utworzenia unikatowego odcisku palca, który będzie używany w dalszej części procesu podejmowania decyzji.

- **Punkty końcowe interfejsu API programu Microsoft DFP:** dostarcza wynik decyzji i akceptuje stan końcowy odzwierciedlający operację podejmowana przez aplikację kliencową. Azure AD B2C nie komunikuje się bezpośrednio z punktami końcowymi ze względu na różne wymagania dotyczące zabezpieczeń i ładunku interfejsu API, zamiast tego używa usługi App Service jako pośredniego.

Na poniższym diagramie architektury przedstawiono implementację.

![Obraz przedstawiający diagram architektury ochrony przed oszustwem microsoft dynamics365](./media/partner-dynamics365-fraud-protection/microsoft-dynamics-365-fraud-protection-diagram.png)

|Krok | Opis |
|:-----| :-----------|
| 1. | Użytkownik pojawia się na stronie logowania. Użytkownicy mogą wybrać opcję rejestracji, aby utworzyć nowe konto i wprowadzić informacje na stronie. Azure AD B2C zbiera atrybuty użytkownika.
| 2. | Azure AD B2C interfejs API warstwy środkowej i przekazuje atrybuty użytkownika.
| 3. | Interfejs API warstwy środkowej zbiera atrybuty użytkownika i przekształca je do formatu, który może być używany przez interfejs API DFP firmy Microsoft. Następnie program wysyła go do interfejsu API programu Microsoft DFP.
| 4. | Gdy interfejs API programu Microsoft DFP zużyje informacje i przetnie je, zwraca wynik do interfejsu API warstwy środkowej.
| 5. | Interfejs API warstwy środkowej przetwarza informacje i wysyła odpowiednie informacje do Azure AD B2C.
| 6. | Azure AD B2C odbiera informacje z interfejsu API warstwy środkowej. Jeśli zostanie wyświetlona odpowiedź Niepowodzenie, użytkownikowi zostanie wyświetlony komunikat o błędzie. Jeśli zostanie pokazana odpowiedź Powodzenie, użytkownik zostanie uwierzytelniony i zapisany w katalogu.

## <a name="set-up-the-solution"></a>Konfigurowanie rozwiązania

1. [Utwórz aplikację serwisu Facebook skonfigurowaną](./identity-provider-facebook.md#create-a-facebook-application) tak, aby zezwalać federacji na Azure AD B2C.
2. [Dodaj utworzony wpis tajny](./tutorial-create-user-flows.md?pivots=b2c-custom-policy#create-the-facebook-key) serwisu Facebook jako Identity Experience Framework zasad.

## <a name="configure-your-application-under-microsoft-dfp"></a>Konfigurowanie aplikacji w programie Microsoft DFP

[Skonfiguruj dzierżawę usługi Azure AD do](/dynamics365/fraud-protection/integrate-real-time-api) korzystania z programu Microsoft DFP.

## <a name="deploy-to-the-web-application"></a>Wdrażanie w aplikacji internetowej

### <a name="implement-microsoft-dfp-service-fingerprinting"></a>Implementowanie odcisków palców usługi Microsoft DFP

[Odcisk palca urządzenia Microsoft DFP](/dynamics365/fraud-protection/device-fingerprinting) jest wymagane do ochrony konta DFP firmy Microsoft.

>[!NOTE]
>Oprócz Azure AD B2C interfejsu użytkownika klient może również zaimplementować usługę pobierania odcisków palców wewnątrz kodu aplikacji w celu bardziej kompleksowego profilowania urządzeń. Usługa pobierania odcisków palców w kodzie aplikacji nie jest uwzględniona w tym przykładzie.

### <a name="deploy-the-azure-ad-b2c-api-code"></a>Wdrażanie kodu Azure AD B2C API

Wdrażanie [dostarczonego kodu interfejsu API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/API) w usłudze platformy Azure. Kod można opublikować [z Visual Studio](/visualstudio/deployment/quickstart-deploy-to-azure).

Konfigurowanie cors i dodawanie **dozwolonego źródła**`https://{your_tenant_name}.b2clogin.com`

>[!NOTE]
>Później będziesz potrzebować adresu URL wdrożonej usługi, aby skonfigurować usługę Azure AD z wymaganymi ustawieniami.

Aby [dowiedzieć się więcej,](../app-service/app-service-web-tutorial-rest-api.md) zobacz dokumentację usługi App Service.

### <a name="add-context-dependent-configuration-settings"></a>Dodawanie ustawień konfiguracji zależnych od kontekstu

Skonfiguruj ustawienia aplikacji w usłudze [App Service na platformie Azure.](../app-service/configure-common.md#configure-app-settings) Dzięki temu ustawienia mogą być bezpiecznie konfigurowane bez sprawdzania ich w repozytorium. Interfejs API REST wymaga podanych następujących ustawień:

| Ustawienia aplikacji | Element źródłowy | Uwagi |
| :-------- | :------------| :-----------|
|FraudProtectionSettings:InstanceId | Konfiguracja programu Microsoft DFP |     |
|FraudProtectionSettings:DeviceFingerprintingCustomerId | Urządzenie firmy Microsoft odciskiem palca identyfikatora klienta |     |
| FraudProtectionSettings:ApiBaseUrl |  Twój podstawowy adres URL z portalu Microsoft DFP   | Zamiast tego usuń "-int", aby wywołać produkcyjny interfejs API|
|  TokenProviderConfig: zasób  | Twój podstawowy adres URL — https://api.dfp.dynamics-int.com     | Zamiast tego usuń "-int", aby wywołać produkcyjny interfejs API|
|   TokenProviderConfig:ClientId       |Identyfikator aplikacji klienckiej usługi Azure AD sprzedawcy usługi Fraud Protection      |       |
| TokenProviderConfig:Authority | https://login.microsoftonline.com/<directory_ID> | Urząd dzierżawy usługi Azure AD dla sprzedawcy usługi Fraud Protection |
| TokenProviderConfig:CertificateThumbprint* | Odcisk palca certyfikatu do użycia w celu uwierzytelnienia w aplikacji klienckiej usługi Azure AD sprzedawcy |
| TokenProviderConfig:ClientSecret* | Klucz tajny dla sprzedawcy aplikacji klienckiej usługi Azure AD | Zalecane do używania menedżera wpisów tajnych |

*Ustaw tylko 1 z 2 oznaczonych parametrów w zależności od tego, czy uwierzytelniasz się za pomocą certyfikatu lub tajnego hasła.

## <a name="azure-ad-b2c-configuration"></a>Azure AD B2C konfiguracji

### <a name="replace-the-configuration-values"></a>Zastępowanie wartości konfiguracji

W podanych [zasadach niestandardowych](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies)znajdź następujące symbole zastępcze i zastąp je odpowiednimi wartościami z wystąpienia.

| Symbol zastępczy | Zamień na | Uwagi |
| :-------- | :------------| :-----------|
|{your_tenant_name} | Krótka nazwa dzierżawy |  "Twojanajedyńczasze" z yourtenant.onmicrosoft.com   |
|{your_tenantId} | Identyfikator dzierżawy dzierżawy Azure AD B2C dzierżawy |  01234567-89ab-cdef-0123-456789abcdef   |
|  {your_tenant_IdentityExperienceFramework_appid}    |   Identyfikator aplikacji IdentityExperienceFramework skonfigurowanej w dzierżawie Azure AD B2C dzierżawy    |  01234567-89ab-cdef-0123-456789abcdef   |
|  {your_tenant_ ProxyIdentityExperienceFramework _appid}     |  Identyfikator aplikacji ProxyIdentityExperienceFramework skonfigurowanej w dzierżawie Azure AD B2C dzierżawy      |   01234567-89ab-cdef-0123-456789abcdef     |
|  {your_tenant_extensions_appid}   |  Identyfikator aplikacji magazynu dzierżawy   |  01234567-89ab-cdef-0123-456789abcdef  |
|   {your_tenant_extensions_app_objectid}  | Identyfikator obiektu aplikacji magazynu dzierżawy    | 01234567-89ab-cdef-0123-456789abcdef   |
|   {your_app_insights_instrumentation_key}  |   Klucz instrumentacji wystąpienia usługi App Insights*  |   01234567-89ab-cdef-0123-456789abcdef |
|  {your_ui_base_url}   | Punkt końcowy w usłudze App Service, z którego są obsługiwane pliki interfejsu użytkownika    | `https://yourapp.azurewebsites.net/B2CUI/GetUIPage`   |
|   {your_app_service_url}  | Adres URL usługi App Service    |  `https://yourapp.azurewebsites.net`  |
|   {twój-facebook-app-id}  |  Identyfikator aplikacji serwisu Facebook skonfigurowanej do federacji z usługą Azure AD B2C   | 000000000000000   |
|  {your-facebook-app-secret}   |  Nazwa klucza zasad, jako który został zapisany wpis tajny aplikacji serwisu Facebook   | B2C_1A_FacebookAppSecret   |

*Szczegółowe informacje o aplikacji mogą być w innej dzierżawie. Ta czynność jest opcjonalna. Usuń odpowiednie parametry TechnicalProfiles i OrechestrationSteps, jeśli nie są potrzebne.

### <a name="call-microsoft-dfp-label-api"></a>Wywołanie interfejsu API etykiety DFP firmy Microsoft

Klienci muszą zaimplementować [interfejs API etykiet](/dynamics365/fraud-protection/integrate-ap-api). Aby dowiedzieć się więcej, zobacz [Interfejs API programu Microsoft DFP.](https://apidocs.microsoft.com/services/dynamics365fraudprotection#/AccountProtection/v1.0)

`URI: < API Endpoint >/v1.0/label/account/create/<userId>`

Wartość userID musi być taka sama jak wartość w odpowiedniej wartości Azure AD B2C konfiguracji (ObjectID).

>[!NOTE]
>Dodaj powiadomienie o zgody do strony kolekcji atrybutów. Powiadamianie o tym, że dane telemetryczne użytkowników i informacje o tożsamości użytkownika będą rejestrowane na potrzeby ochrony konta.

## <a name="configure-the-azure-ad-b2c-policy"></a>Konfigurowanie zasad Azure AD B2C konfiguracji

1. Przejdź do [zasad Azure AD B2C w](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies) folderze Zasady.

2. Postępuj zgodnie [z tym dokumentem,](./tutorial-create-user-flows.md?pivots=b2c-custom-policy?tabs=applications#custom-policy-starter-pack) aby [pobrać pakiet startowy LocalAccounts](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)

3. Skonfiguruj zasady dla dzierżawy Azure AD B2C dzierżawy.

>[!NOTE]
>Zaktualizuj podane zasady, aby odnosiły się do określonej dzierżawy.

## <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Otwórz dzierżawę Azure AD B2C i w obszarze Zasady wybierz **pozycję Identity Experience Framework**.

2. Wybierz wcześniej utworzoną **wcześniej pozycję SignUpSignIn.**

3. Wybierz **pozycję Uruchom przepływ użytkownika** i wybierz ustawienia:

   a. **Aplikacja:** wybierz zarejestrowaną aplikację (przykład to JWT)

   b. **Adres URL odpowiedzi:** wybierz **adres URL przekierowania**

   c. Wybierz **pozycję Uruchom przepływ użytkownika.**

4. Przejdź przez przepływ tworzenia konta i utwórz konto

5. Usługa Microsoft DFP zostanie wywołana podczas przepływu po utworzeniu atrybutu użytkownika. Jeśli przepływ jest niekompletny, sprawdź, czy użytkownik nie został zapisany w katalogu.

>[!NOTE]
>Zaktualizuj reguły bezpośrednio w portalu Microsoft DFP w przypadku korzystania z [aparatu reguł programu Microsoft DFP.](/dynamics365/fraud-protection/rules)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe informacje, zapoznaj się z następującymi artykułami:

- [Przykłady programu Microsoft DFP](https://github.com/Microsoft/Dynamics-365-Fraud-Protection-Samples)

- [Zasady niestandardowe w usłudze Azure AD B2C](./custom-policy-overview.md)

- [Wprowadzenie do zasad niestandardowych w Azure AD B2C](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)
