---
title: Rozwiązywanie problemów z zasadami niestandardowymi za pomocą Application Insights
titleSuffix: Azure AD B2C
description: Jak skonfigurować Application Insights do śledzenia wykonywania zasad niestandardowych.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/05/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 074bffb8614be1f71ba1956fd5a238bc19354c58
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107028747"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>Zbieranie dzienników Azure Active Directory B2C z Application Insights

W tym artykule przedstawiono procedurę zbierania dzienników z programu Active Directory B2C (Azure AD B2C), dzięki czemu można zdiagnozować problemy z zasadami niestandardowymi. Usługa Application Insights umożliwia diagnozowanie wyjątków i wizualizowanie problemów z wydajnością aplikacji. Usługa Azure AD B2C zawiera funkcję wysyłania danych do usługi Application Insights.

Szczegółowe dzienniki działań opisane tutaj należy włączyć **tylko** podczas opracowywania zasad niestandardowych.

> [!WARNING]
> Nie ustawiaj `DeploymentMode` do `Development` w środowiskach produkcyjnych. Dzienniki zbierają wszystkie oświadczenia wysyłane do i od dostawców tożsamości. Deweloper przyjmuje odpowiedzialność za wszelkie dane osobowe zbierane w dziennikach Application Insights. Te szczegółowe dzienniki są zbierane tylko wtedy, gdy zasady są umieszczane w **trybie dewelopera**.

## <a name="set-up-application-insights"></a>Skonfiguruj Application Insights

Jeśli jeszcze tego nie masz, Utwórz wystąpienie Application Insights w subskrypcji.

> [!TIP]
> Pojedyncze wystąpienie Application Insights może być używane dla wielu dzierżawców Azure AD B2C. Następnie w zapytaniu można filtrować według dzierżawy lub nazwy zasad. Aby uzyskać więcej informacji, [Zobacz dzienniki w Application Insights](#see-the-logs-in-application-insights) Samples.

Aby użyć wystąpienia wyjścia Application Insights w ramach subskrypcji, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog, który zawiera subskrypcję platformy Azure (nie katalog Azure AD B2C).
1. Otwórz utworzony wcześniej zasób Application Insights.
1. Na stronie **Przegląd** i rejestrowanie **klucza Instrumentacji**

Aby utworzyć wystąpienie Application Insights w ramach subskrypcji, wykonaj następujące kroki:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Wybierz filtr **katalogów i subskrypcji** w górnym menu, a następnie wybierz katalog, który zawiera subskrypcję platformy Azure (nie katalog Azure AD B2C).
1. Wybierz pozycję **Utwórz zasób** w menu nawigacji po lewej stronie.
1. Wyszukaj i wybierz pozycję **Application Insights**, a następnie wybierz pozycję **Utwórz**.
1. Wypełnij formularz, wybierz pozycję **Przegląd + Utwórz**, a następnie wybierz pozycję **Utwórz**.
1. Po zakończeniu wdrożenia wybierz pozycję **Przejdź do zasobu**.
1. W menu **Konfiguruj** w Application Insights wybierz pozycję **Właściwości**.
1. Zapisz **klucz Instrumentacji** do użycia w późniejszym kroku.

## <a name="configure-the-custom-policy"></a>Konfigurowanie zasad niestandardowych

1. Otwórz plik jednostki uzależnionej (RP), na przykład *SignUpOrSignin.xml*.
1. Dodaj następujące atrybuty do `<TrustFrameworkPolicy>` elementu:

   ```xml
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. Jeśli jeszcze nie istnieje, Dodaj `<UserJourneyBehaviors>` węzeł podrzędny do `<RelyingParty>` węzła. Musi ona znajdować się po `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />` .
1. Dodaj następujący węzeł jako element podrzędny `<UserJourneyBehaviors>` elementu. Pamiętaj, aby zastąpić `{Your Application Insights Key}` **klucz instrumentacji** Application Insights zarejestrowany wcześniej.

    ```xml
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * `DeveloperMode="true"` informuje ApplicationInsights o przyspieszeniu telemetrii za pomocą potoku przetwarzania. Dobre dla rozwoju, ale ograniczone do dużych woluminów. W obszarze produkcja ustaw wartość `DeveloperMode` na `false` .
    * `ClientEnabled="true"` wysyła skrypt po stronie klienta ApplicationInsights na potrzeby śledzenia widoku strony i błędów po stronie klienta. Można je wyświetlić w tabeli **browserTimings** w portalu Application Insights. Przez ustawienie `ClientEnabled= "true"` , należy dodać Application Insights do skryptu strony i uzyskać chronometraż obciążeń strony i wywołań AJAX, liczniki, szczegóły wyjątków przeglądarki i błędów AJAX, a liczby użytkowników i sesji. To pole jest **opcjonalne** i jest domyślnie ustawione na wartość `false` .
    * `ServerEnabled="true"` wysyła istniejący kod JSON UserJourneyRecorder jako zdarzenie niestandardowe do Application Insights.

    Na przykład:

    ```xml
    <TrustFrameworkPolicy
      ...
      TenantId="fabrikamb2c.onmicrosoft.com"
      PolicyId="SignUpOrSignInWithAAD"
      DeploymentMode="Development"
      UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
    >
    ...
    <RelyingParty>
      <DefaultUserJourney ReferenceId="UserJourney ID from your extensions policy, or equivalent (for example: SignUpOrSigninWithAzureAD)" />
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
    </TrustFrameworkPolicy>
    ```

1. Przekaż zasady.

## <a name="see-the-logs-in-application-insights"></a>Zapoznaj się z dziennikami w Application Insights

Istnieje krótkie opóźnienie, zazwyczaj mniej niż pięć minut, zanim będzie można zobaczyć nowe dzienniki w Application Insights.

1. Otwórz zasób Application Insights, który został utworzony w [Azure Portal](https://portal.azure.com).
1. Na stronie **Przegląd** wybierz pozycję **dzienniki**.
1. Otwórz nową kartę w Application Insights.

Poniżej znajduje się lista zapytań, których można użyć, aby wyświetlić dzienniki:

| Zapytanie | Opis |
|---------------------|--------------------|
| `traces` | Pobierz wszystkie dzienniki wygenerowane przez Azure AD B2C |
| `traces | where timestamp > ago(1d)` | Pobierz wszystkie dzienniki wygenerowane przez Azure AD B2C przez ostatni dzień.|
| `traces | where message contains "exception" | where timestamp > ago(2h)`|  Pobierz wszystkie dzienniki z błędami z ostatnich dwóch godzin.|
| `traces | where customDimensions.Tenant == "contoso.onmicrosoft.com" and customDimensions.UserJourney  == "b2c_1a_signinandup"` | Pobierz wszystkie dzienniki wygenerowane przez Azure AD B2C dzierżawy *contoso.onmicrosoft.com* , a podróż użytkownika jest *b2c_1a_signinandup*. |
| `traces | where customDimensions.CorrelationId == "00000000-0000-0000-0000-000000000000"`| Pobierz wszystkie dzienniki wygenerowane przez Azure AD B2C dla identyfikatora korelacji. Zastąp identyfikator korelacji IDENTYFIKATORem korelacji. | 

Wpisy mogą być długie. Eksportuj do pliku CSV w celu bliższego wyglądu.

Aby uzyskać więcej informacji na temat wykonywania zapytań, zobacz [Omówienie zapytań dzienników w Azure monitor](../azure-monitor/logs/log-query-overview.md).

## <a name="see-the-logs-in-vs-code-extension"></a>Zobacz dzienniki w VS Code rozszerzeniu

Zalecamy zainstalowanie [rozszerzenia Azure AD B2C](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) [vs Code](https://code.visualstudio.com/). Dzięki rozszerzeniu Azure AD B2C dzienniki są zorganizowane według nazwy zasad, identyfikatora korelacji (Application Insights przedstawia pierwszą cyfrę identyfikatora korelacji) oraz sygnatury czasowej dziennika. Ta funkcja ułatwia znalezienie odpowiedniego dziennika w oparciu o lokalną sygnaturę czasową i przeglądanie podróży użytkownika w sposób wykonywany przez Azure AD B2C.

> [!NOTE]
> Społeczność opracowała rozszerzenie programu vs Code dla Azure AD B2C, aby pomóc deweloperom w zakresie tożsamości. Rozszerzenie nie jest obsługiwane przez firmę Microsoft i jest udostępniane w sposób niezgodny z oczekiwaniami.

### <a name="set-application-insights-api-access"></a>Ustaw Application Insights dostęp do interfejsu API

Po skonfigurowaniu Application Insights i skonfigurowaniu zasad niestandardowych należy uzyskać **identyfikator interfejsu api** Application Insights i utworzyć **klucz interfejsu API**. Identyfikator interfejsu API i klucz interfejsu API są używane przez rozszerzenie Azure AD B2C do odczytywania zdarzeń Application Insights (telemetrii). Klucze interfejsu API powinny być zarządzane na przykład hasła. Zachowaj tajemnicę IT.

> [!NOTE]
> Application Insights klucz instrumentacji, który jest używany przez Azure AD B2C do wysyłania telemetrii do Application Insights. Klucz Instrumentacji jest używany tylko w zasadach Azure AD B2C, a nie w rozszerzeniu programu vs Code.

Aby uzyskać identyfikator Application Insights i klucz:

1. W Azure Portal Otwórz zasób Application Insights dla swojej aplikacji.
1. Wybierz pozycję **Ustawienia**, a następnie pozycję **dostęp do interfejsu API**.
1. Kopiuj **Identyfikator aplikacji**
1. Wybierz pozycję **Utwórz klucz interfejsu API**
1. Zaznacz pole **Odczytaj dane telemetryczne** .
1. Skopiuj **klucz** przed zamknięciem bloku Utwórz klucz interfejsu API i Zapisz go w bezpiecznym miejscu. Jeśli utracisz klucz, musisz utworzyć inny.

    ![Zrzut ekranu, który pokazuje, jak utworzyć klucz dostępu interfejsu API.](./media/troubleshoot-with-application-insights/application-insights-api-access.png)

### <a name="set-up-azure-ad-b2c-vs-code-extension"></a>Konfigurowanie rozszerzenia VS Code Azure AD B2C

Teraz masz identyfikator i klucz interfejsu API usługi Azure Application Insights, można skonfigurować rozszerzenie programu vs Code w celu odczytania dzienników. Rozszerzenie Azure AD B2C VS Code oferuje dwa zakresy dla ustawień:

- **Globalne ustawienia użytkownika** — ustawienia, które są stosowane globalnie do dowolnego wystąpienia vs Code otwarte.
- **Ustawienia obszaru roboczego** — ustawienia przechowywane w obszarze roboczym i stosowane tylko podczas otwierania obszaru roboczego (przy użyciu vs Code **Otwórz folder**).

1. W Eksploratorze **śledzenia Azure AD B2C** kliknij ikonę **Ustawienia** .

    ![Zrzut ekranu pokazujący Wybieranie ustawień usługi Application Insights.](./media/troubleshoot-with-application-insights/app-insights-settings.png)

1. Podaj **Identyfikator** i **klucz** usługi Azure Application Insights.
1. Kliknij pozycję **Zapisz**.

Po zapisaniu ustawień dzienniki usługi Application Insights są wyświetlane w oknie **śledzenie Azure AD B2C (App Insights)** .

![Zrzut ekranu przedstawiający rozszerzenie Azure AD B2C programu vscode, które prezentuje ślad usługi Azure Application Insights.](./media/troubleshoot-with-application-insights/vscode-extension-application-insights-trace.png)


## <a name="configure-application-insights-in-production"></a>Konfigurowanie Application Insights w środowisku produkcyjnym

W celu poprawy wydajności środowiska produkcyjnego i lepszego środowiska użytkownika należy skonfigurować zasady w taki sposób, aby ignorował nieważne komunikaty. Użyj następującej konfiguracji, aby wysyłać do Application Insights tylko krytyczne komunikaty o błędach. 

1. Ustaw `DeploymentMode` atrybut [TrustFrameworkPolicy](trustframeworkpolicy.md) na `Production` . 

   ```xml
   <TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0"
   TenantId="yourtenant.onmicrosoft.com"
   PolicyId="B2C_1A_signup_signin"
   PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin"
   DeploymentMode="Production"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights">
   ```

1. Ustaw wartość `DeveloperMode` [JourneyInsights](relyingparty.md#journeyinsights) na `false` .

   ```xml
   <UserJourneyBehaviors>
     <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="false" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
   </UserJourneyBehaviors>
   ```
   
1. Przekazanie i przetestowanie zasad.



## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [rozwiązywać problemy Azure AD B2C zasadami niestandardowymi](troubleshoot-custom-policies.md)
