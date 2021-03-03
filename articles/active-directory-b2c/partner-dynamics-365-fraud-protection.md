---
title: Samouczek konfigurowania Azure Active Directory B2C przy użyciu ochrony oszustwa dla systemu Microsoft Dynamics 365
titleSuffix: Azure AD B2C
description: Samouczek dotyczący konfigurowania Azure Active Directory B2C przy użyciu ochrony oszustwa dla systemu Microsoft Dynamics 365 w celu identyfikowania ryzykownych i fałszywych kont
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: cf441108c9fd0ae87f265604f6f0706d92516746
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101646558"
---
# <a name="tutorial-configure-microsoft-dynamics-365-fraud-protection-with-azure-active-directory-b2c"></a>Samouczek: Konfigurowanie ochrony oszustwa systemu Microsoft Dynamics 365 za pomocą Azure Active Directory B2C

W tym przykładowym samouczku przedstawiono wskazówki dotyczące integracji [ochrony oszustwa systemu Microsoft Dynamics 365](/dynamics365/fraud-protection/overview) (DFP) z usługą Azure Active Directory (AD) B2C.

Program Microsoft DFP zapewnia klientom możliwość oceny, czy ryzyko prób tworzenia nowych kont i próby zalogowania się do ekosystemu klienta jest fałszywe. Program Microsoft DFP Assessment może być używany przez klienta do blokowania lub zakwestionowania podejrzanych prób w celu utworzenia nowych, fałszywych kont lub naruszenia istniejących kont. Ochrona konta obejmuje sztuczną transportową kartę sieciową, interfejsy API do oceny ryzyka w czasie rzeczywistym, zasady i listę w celu optymalizacji strategii ryzyka zgodnie z potrzebami biznesowymi klienta oraz kartę wyników umożliwiającą monitorowanie skuteczności i trendów w ekosystemie klienta.

W tym przykładzie będziemy zintegrować funkcje ochrony konta programu Microsoft DFP z przepływem użytkownika Azure AD B2C. Usługa będzie zewnętrznie odciskiem palca po każdym zalogowaniu lub zarejestrowaniu się w poszukiwaniu wcześniejszych lub obecnych podejrzanych zachowań. Azure AD B2C wywołuje punkt końcowy decyzji z DFP firmy Microsoft, która zwraca wynik na podstawie wszystkich przeszłych i obecnych zachowań ze wskazanego użytkownika, a także reguł niestandardowych określonych w usłudze Microsoft DFP Service. Azure AD B2C podejmuje decyzję o zatwierdzeniu w oparciu o ten wynik i przekazuje ją z powrotem do programu Microsoft DFP.

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, musisz:

- Subskrypcja platformy Azure. Jeśli nie masz subskrypcji, możesz uzyskać [bezpłatne konto](https://azure.microsoft.com/free/).

- [Dzierżawa Azure AD B2C](./tutorial-create-tenant.md). Dzierżawca jest połączony z subskrypcją platformy Azure.

- Uzyskaj [subskrypcję](https://dynamics.microsoft.com/pricing/#Sales)programu Microsoft DFP. Możesz również skonfigurować [wersję próbną klienta](https://dynamics.microsoft.com/ai/fraud-protection/signin/?RU=https%3A%2F%2Fdfp.microsoft.com%2Fsignin) .

## <a name="scenario-description"></a>Opis scenariusza

Integracja z programem Microsoft DFP obejmuje następujące składniki:

- **Azure AD B2C dzierżawca**: uwierzytelnia użytkownika i działa jako klient programu Microsoft DFPe. Hostuje skrypt odcisku palca, zbierając dane identyfikacyjne i diagnostyczne każdego użytkownika, który wykonuje zasady docelowe. Późniejsze bloki lub wyzwania próbuje zalogowanie się lub zarejestrowanie się, jeśli program Microsoft DFP je znalazł podejrzane.

- **Niestandardowa Usługa aplikacji**: aplikacja sieci Web, która służy do dwóch celów.

  - Służy do używania stron HTML jako interfejsu użytkownika struktury środowiska tożsamości. Odpowiedzialny za osadzenie skryptu odcisku palca systemu Microsoft Dynamics 365.

  - Kontroler interfejsu API z punktami końcowymi RESTful, który łączy program Microsoft DFP z Azure AD B2C. Dojście, struktura i zgodność z wymaganiami dotyczącymi zabezpieczeń.

- **Usługa Microsoft DFP odcisk palca**: skrypt dynamicznie osadzony, który rejestruje dane telemetryczne urządzenia i własne osoby, aby utworzyć unikatowy odcisk palca, który będzie używany później w procesie podejmowania decyzji.

- **Punkty końcowe interfejsu API usługi Microsoft DFP**: przedstawiają wynik decyzji i akceptują końcowy stan odzwierciedlający operację podejmowaną przez aplikację kliencką. Azure AD B2C nie komunikuje się bezpośrednio z punktami końcowymi ze względu na różne wymagania dotyczące zabezpieczeń i ładunków interfejsów API, zamiast tego używa usługi App Service jako pośredniej.

Na poniższym diagramie architektury przedstawiono implementację.

![Obraz przedstawia diagram architektury ochrony oszustw firmy Microsoft dynamics365](./media/partner-dynamics365-fraud-protection/microsoft-dynamics-365-fraud-protection-diagram.png)

|Krok | Opis |
|:-----| :-----------|
| 1. | Użytkownik dotarł do strony logowania. Użytkownicy wybierają konto, aby utworzyć nowe konto, a następnie wprowadzić informacje na stronie. Azure AD B2C zbiera atrybuty użytkownika.
| 2. | Azure AD B2C wywołuje interfejs API warstwy środkowej i przekazuje atrybuty użytkownika.
| 3. | Interfejs API warstwy środkowej zbiera atrybuty użytkownika i przekształca je w format, który może wykorzystać interfejs API Microsoft DFP. Następnie po wysłaniu go do usługi Microsoft DFP API.
| 4. | Gdy interfejs API Microsoft DFP korzysta z informacji i przetwarza je, zwraca wynik do interfejsu API warstwy środkowej.
| 5. | Interfejs API warstwy środkowej przetwarza informacje i wysyła odpowiednie informacje do Azure AD B2C.
| 6. | Azure AD B2C otrzymuje informacje z powrotem z interfejsu API warstwy środkowej. Jeśli zostanie wyświetlona odpowiedź na awarię, zostanie wyświetlony komunikat o błędzie. Jeśli zostanie wyświetlona odpowiedź o powodzeniu, użytkownik zostanie uwierzytelniony i zapisany w katalogu.

## <a name="set-up-the-solution"></a>Skonfiguruj rozwiązanie

1. [Utwórz aplikację Facebook](./identity-provider-facebook.md#create-a-facebook-application) skonfigurowaną tak, aby umożliwić federacyjnym Azure AD B2C.
2. [Dodaj wpis tajny serwisu Facebook](./custom-policy-get-started.md#create-the-facebook-key) utworzony jako klucz zasad struktury środowiska tożsamości.

## <a name="configure-your-application-under-microsoft-dfp"></a>Konfigurowanie aplikacji w programie Microsoft DFP

[Skonfiguruj dzierżawę usługi Azure AD](/dynamics365/fraud-protection/integrate-real-time-api) pod kątem korzystania z programu Microsoft DFP.

## <a name="deploy-to-the-web-application"></a>Wdrażanie w aplikacji sieci Web

### <a name="implement-microsoft-dfp-service-fingerprinting"></a>Implementowanie odcisku palca usługi Microsoft DFP

[Odcisk palca urządzenia Microsoft DFP](/dynamics365/fraud-protection/device-fingerprinting) jest wymagany w przypadku ochrony konta programu Microsoft DFP.

>[!NOTE]
>Oprócz Azure AD B2C stron interfejsu użytkownika klient może również zaimplementować usługę odcisków palców w kodzie aplikacji, aby uzyskać bardziej kompleksowe profilowanie urządzeń. W tym przykładzie nie jest uwzględniona usługa odcisku palca w kodzie aplikacji.

### <a name="deploy-the-azure-ad-b2c-api-code"></a>Wdrażanie kodu interfejsu API Azure AD B2C

Wdróż [podany kod interfejsu API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/API) w usłudze platformy Azure. Kod można [opublikować w programie Visual Studio](/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019).

Mechanizm CORS konfiguracji, Dodawanie **dozwolonego pochodzenia**`https://{your_tenant_name}.b2clogin.com`

>[!NOTE]
>Później będzie potrzebny adres URL wdrożonej usługi w celu skonfigurowania usługi Azure AD przy użyciu wymaganych ustawień.

Aby dowiedzieć się więcej, zobacz [dokumentację usługi App Service](../app-service/app-service-web-tutorial-rest-api.md) .

### <a name="add-context-dependent-configuration-settings"></a>Dodaj ustawienia konfiguracji zależne od kontekstu

Skonfiguruj ustawienia aplikacji w [usłudze App Service na platformie Azure](../app-service/configure-common.md#configure-app-settings). Pozwala to na bezpieczne skonfigurowanie ustawień bez sprawdzania ich w repozytorium. Interfejs API REST wymaga podania następujących ustawień:

| Ustawienia aplikacji | Element źródłowy | Uwagi |
| :-------- | :------------| :-----------|
|FraudProtectionSettings: InstanceId | Konfiguracja programu Microsoft DFP |     |
|FraudProtectionSettings:DeviceFingerprintingCustomerId | Identyfikator klienta dotyczącego odcisku palca urządzenia firmy Microsoft |     |
| FraudProtectionSettings:ApiBaseUrl |  Podstawowy adres URL z witryny Microsoft DFP Portal   | Usuń element "-int", aby wywołać produkcyjny interfejs API
|  TokenProviderConfig: zasób | https://api.dfp.dynamics-int.com |   Usuń element "-int", aby wywołać produkcyjny interfejs API  |
|   TokenProviderConfig: ClientId       |Identyfikator aplikacji klienta usługi Azure AD dla handlowca ochrony oszustwa      |       |
| TokenProviderConfig: Urząd | https://login.microsoftonline.com/<directory_ID> | Urząd dzierżawy usługi Azure AD w ramach ochrony oszustwa |
| TokenProviderConfig: CertificateThumbprint * | Odcisk palca certyfikatu, który ma być używany do uwierzytelniania w aplikacji klienckiej usługi Azure AD dla sprzedawcy |
| TokenProviderConfig: ClientSecret * | Wpis tajny aplikacji klienckiej usługi Azure AD dla sprzedawcy | Zalecane do korzystania z Menedżera kluczy tajnych |

* W zależności od tego, czy jest uwierzytelniany przy użyciu certyfikatu lub wpisu tajnego, takiego jak hasło, można ustawić tylko 1 z 2 oznaczonych parametrów.

## <a name="azure-ad-b2c-configuration"></a>Konfiguracja Azure AD B2C

### <a name="replace-the-configuration-values"></a>Zastąp wartości konfiguracyjne

W podanych [zasad niestandardowych](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies)Znajdź następujące symbole zastępcze i zastąp je odpowiednimi wartościami z danego wystąpienia.

| Symbol zastępczy | Zamień na | Uwagi |
| :-------- | :------------| :-----------|
|{your_tenant_name} | Krótka nazwa dzierżawy |  "yourtenant" z yourtenant.onmicrosoft.com   |
|{your_tenantId} | Identyfikator dzierżawy dzierżawy Azure AD B2C |  01234567-89ab-cdef-0123-456789ABCDEF   |
|  {your_tenant_IdentityExperienceFramework_appid}    |   Identyfikator aplikacji IdentityExperienceFramework skonfigurowanej w dzierżawie Azure AD B2C    |  01234567-89ab-cdef-0123-456789ABCDEF   |
|  {your_tenant_ ProxyIdentityExperienceFramework _appid}     |  Identyfikator aplikacji ProxyIdentityExperienceFramework skonfigurowanej w dzierżawie Azure AD B2C      |   01234567-89ab-cdef-0123-456789ABCDEF     |
|  {your_tenant_extensions_appid}   |  Identyfikator aplikacji magazynu Twojej dzierżawy   |  01234567-89ab-cdef-0123-456789ABCDEF  |
|   {your_tenant_extensions_app_objectid}  | Identyfikator obiektu aplikacji magazynu dzierżawy    | 01234567-89ab-cdef-0123-456789ABCDEF   |
|   {your_app_insights_instrumentation_key}  |   Klucz Instrumentacji wystąpienia usługi App Insights *  |   01234567-89ab-cdef-0123-456789ABCDEF |
|  {your_ui_base_url}   | Punkt końcowy w usłudze App Service, z której są obsługiwane pliki interfejsu użytkownika    | https://yourapp.azurewebsites.net/B2CUI/GetUIPage   |
|   {your_app_service_url}  | Adres URL usługi App Service    |  https://yourapp.azurewebsites.net  |
|   {Identyfikator aplikacji w serwisie Facebook}  |  Identyfikator aplikacji usługi Facebook skonfigurowany dla Federacji z Azure AD B2C   | 000000000000000   |
|  {Twoje-Facebook-app-Secret}   |  Nazwa klucza zasad, który został zapisany jako wpis tajny aplikacji w serwisie Facebook jako   | B2C_1A_FacebookAppSecret   |

* Szczegółowe informacje o aplikacji mogą znajdować się w innej dzierżawie. Ta czynność jest opcjonalna. Usuń odpowiednie TechnicalProfiles i OrechestrationSteps, jeśli nie jest to trzeba.

### <a name="call-microsoft-dfp-label-api"></a>Wywoływanie interfejsu API etykiet DFP firmy Microsoft

Klienci muszą [zaimplementować interfejs API etykiet](/dynamics365/fraud-protection/integrate-ap-api). Aby dowiedzieć się więcej, zobacz [interfejs API programu Microsoft DFP](https://apidocs.microsoft.com/services/dynamics365fraudprotection#/AccountProtection/v1.0) .

`URI: < API Endpoint >/v1.0/label/account/create/<userId>`

Wartość identyfikatora użytkownika musi być taka sama jak nazwa w odpowiedniej wartości konfiguracji Azure AD B2C (ObjectID).

>[!NOTE]
>Dodaj powiadomienie o zgodzie do strony kolekcji atrybutów. Powiadamianie o tym, że informacje o telemetrii i tożsamości użytkownika są rejestrowane na potrzeby ochrony konta.

## <a name="configure-the-azure-ad-b2c-policy"></a>Konfigurowanie zasad Azure AD B2C

1. Przejdź do [zasad Azure AD B2C](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies) w folderze zasady.

2. Postępuj zgodnie z tym [dokumentem](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) , aby pobrać [LocalAccounts Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)

3. Skonfiguruj zasady dla dzierżawy Azure AD B2C.

>[!NOTE]
>Zaktualizuj podane zasady powiązane z konkretną dzierżawą.

## <a name="test-the-user-flow"></a>Testowanie przepływu użytkownika

1. Otwórz dzierżawcę Azure AD B2C i w obszarze zasady wybierz pozycję **platforma obsługi tożsamości**.

2. Wybierz wcześniej utworzone **SignUpSignIn**.

3. Wybierz pozycję **Uruchom przepływ użytkownika** i wybierz ustawienia:

   a. **Aplikacja**: wybór zarejestrowanej aplikacji (przykład: JWT)

   b. **Adres URL odpowiedzi**: Wybierz **adres URL przekierowania**

   c. Wybierz pozycję **Uruchom przepływ użytkownika**.

4. Przejdź przez przepływ rejestracji i Utwórz konto

5. Usługa Microsoft DFP zostanie wywołana podczas przepływu, po utworzeniu atrybutu użytkownika. Jeśli przepływ jest niekompletny, sprawdź, czy użytkownik nie jest zapisany w katalogu.

>[!NOTE]
>Aktualizuj reguły bezpośrednio w witrynie Microsoft DFP Portal, jeśli używany jest [aparat reguł programu Microsoft DFP](/dynamics365/fraud-protection/rules).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe informacje, zapoznaj się z następującymi artykułami:

- [Przykłady programu Microsoft DFP](https://github.com/Microsoft/Dynamics-365-Fraud-Protection-Samples)

- [Zasady niestandardowe w usłudze Azure AD B2C](./custom-policy-overview.md)

- [Wprowadzenie do zasad niestandardowych w Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
