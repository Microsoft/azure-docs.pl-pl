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
ms.date: 10/12/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ddc0dc433a5d8c09c692e6304647fb391694e8c8
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2020
ms.locfileid: "91993163"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>Zbieranie dzienników Azure Active Directory B2C z Application Insights

W tym artykule przedstawiono procedurę zbierania dzienników z programu Active Directory B2C (Azure AD B2C), dzięki czemu można zdiagnozować problemy z zasadami niestandardowymi. Application Insights zapewnia sposób diagnozowania wyjątków i wizualizacji problemów z wydajnością aplikacji. Azure AD B2C zawiera funkcję wysyłania danych do Application Insights.

Szczegółowe dzienniki działań opisane tutaj należy włączyć **tylko** podczas opracowywania zasad niestandardowych.

> [!WARNING]
> Nie włączaj trybu deweloperskiego w środowisku produkcyjnym. Dzienniki zbierają wszystkie oświadczenia wysyłane do i od dostawców tożsamości. Deweloper przyjmuje odpowiedzialność za wszelkie dane osobowe zbierane w dziennikach Application Insights. Te szczegółowe dzienniki są zbierane tylko wtedy, gdy zasady są umieszczane w **trybie dewelopera**.

## <a name="set-up-application-insights"></a>Skonfiguruj Application Insights

Jeśli jeszcze tego nie masz, Utwórz wystąpienie Application Insights w subskrypcji.

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

1. Jeśli jeszcze nie istnieje, Dodaj `<UserJourneyBehaviors>` węzeł podrzędny do `<RelyingParty>` węzła. Musi ona znajdować się bezpośrednio po `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />` .
1. Dodaj następujący węzeł jako element podrzędny `<UserJourneyBehaviors>` elementu. Pamiętaj, aby zastąpić `{Your Application Insights Key}` **klucz instrumentacji** Application Insights zarejestrowany wcześniej.

    ```xml
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * `DeveloperMode="true"` informuje ApplicationInsights o przyspieszeniu telemetrii za pomocą potoku przetwarzania. Dobre dla rozwoju, ale ograniczone do dużych woluminów.
    * `ClientEnabled="true"` wysyła skrypt po stronie klienta ApplicationInsights na potrzeby śledzenia widoku strony i błędów po stronie klienta. Można je wyświetlić w tabeli **browserTimings** w portalu Application Insights. Przez ustawienie `ClientEnabled= "true"` , należy dodać Application Insights do skryptu strony i uzyskać chronometraż obciążeń strony i wywołań AJAX, liczniki, szczegóły wyjątków przeglądarki i błędów AJAX, a liczby użytkowników i sesji. To pole jest **opcjonalne**i jest domyślnie ustawione na wartość `false` .
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
`traces` | Zobacz wszystkie dzienniki wygenerowane przez Azure AD B2C |
`traces | where timestamp > ago(1d)` | Zobacz wszystkie dzienniki wygenerowane przez Azure AD B2C w ciągu ostatniego dnia

Wpisy mogą być długie. Eksportuj do pliku CSV w celu bliższego wyglądu.

Aby uzyskać więcej informacji na temat wykonywania zapytań, zobacz [Omówienie zapytań dzienników w Azure monitor](../azure-monitor/log-query/log-query-overview.md).

## <a name="next-steps"></a>Następne kroki

Społeczność opracowała przeglądarkę podróży użytkowników w celu ułatwienia deweloperom tożsamości. Odczytuje dane z wystąpienia Application Insights i udostępnia widok dobrze skonstruowany zdarzeń podróży użytkownika. Kod źródłowy zostanie uzyskany i wdrożony we własnym rozwiązaniu.

Gracz podróży użytkownika nie jest obsługiwany przez firmę Microsoft i jest udostępniany w sposób niezgodny z oczekiwaniami.

Wersja przeglądarki, która odczytuje zdarzenia z Application Insights w witrynie GitHub, można znaleźć tutaj:

[Azure-Samples/Active-Directory-B2C-Advanced-policies](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/wingtipgamesb2c/src/WingTipUserJourneyPlayerWebApplication)
