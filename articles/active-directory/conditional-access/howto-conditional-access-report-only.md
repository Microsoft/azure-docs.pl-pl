---
title: Konfigurowanie zasad dostępu warunkowego w trybie tylko do raportowania — Azure Active Directory
description: Używanie trybu tylko do raportowania w przypadku dostępu warunkowego do pomocy w przyjęciu
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46a00d55c58992be1009da1de5441ebe4e589a70
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83994973"
---
# <a name="configure-a-conditional-access-policy-in-report-only-mode"></a>Konfigurowanie zasad dostępu warunkowego w trybie tylko do raportowania

Aby skonfigurować zasady dostępu warunkowego w trybie tylko do raportowania:

> [!IMPORTANT]
> Jeśli Twoja organizacja jeszcze nie została [skonfigurowana, skonfiguruj Azure monitor integrację z usługą Azure AD](#set-up-azure-monitor-integration-with-azure-ad). Ten proces musi zostać wykonany, aby dane były dostępne do przejrzenia.

1. Zaloguj się do **Azure Portal** jako administrator dostępu warunkowego, administrator zabezpieczeń lub Administrator globalny.
1. Przejdź do **Azure Active Directory**  >  **Security**  >  **dostępu warunkowego**zabezpieczeń.
1. Wybierz pozycję **nowe zasady**.
1. Skonfiguruj warunki zasad i wymagane kontrole dotacji zgodnie z potrzebami.
1. W obszarze **Włączanie zasad** ustaw opcję Przełącz do trybu **tylko raportowanie** .
1. Wybierz pozycję **Zapisz**

> [!TIP]
> Można edytować stan **zasad włączania** istniejących zasad z **w programie w** celu **tylko raportowania** , ale spowoduje to wyłączenie wymuszania zasad. 

Wyświetl raport — wynik tylko w dziennikach logowania usługi Azure AD.

Aby wyświetlić wynik zasad dotyczących tylko raportów dotyczących określonego logowania:

1. Zaloguj się do **Azure Portal** jako czytelnik raportów, czytelnik zabezpieczeń, administrator zabezpieczeń lub Administrator globalny.
1. Przejdź do **Azure Active Directory**  >  **logowania**.
1. Wybierz pozycję Zaloguj lub Dodaj filtry, aby zawęzić wyniki.
1. W szufladzie **szczegółów** wybierz kartę **tylko raport** , aby wyświetlić zasady oceniane podczas logowania.

> [!NOTE]
> Podczas pobierania dzienników logowania wybierz format JSON, aby dołączyć raport dostępu warunkowego — tylko dane wynikowe.

## <a name="set-up-azure-monitor-integration-with-azure-ad"></a>Konfigurowanie integracji Azure Monitor z usługą Azure AD

Aby wyświetlić zagregowany wpływ zasad dostępu warunkowego przy użyciu nowego skoroszytu dostępu warunkowego, należy zintegrować Azure Monitor z usługą Azure AD i wyeksportować dzienniki logowania. Aby skonfigurować tę integrację, należy wykonać dwie czynności: 

1. [Zarejestruj się, aby uzyskać subskrypcję Azure monitor i utworzyć obszar roboczy](/azure/azure-monitor/learn/quick-create-workspace).
1. [Wyeksportuj dzienniki logowania z usługi Azure AD do Azure monitor](/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics).

Więcej informacji o cenach Azure Monitor można znaleźć na [stronie cennika Azure monitor](https://azure.microsoft.com/pricing/details/monitor/). Zasoby do oszacowania kosztów, ustawiania dziennego limitu lub dostosowywania okresu przechowywania danych można znaleźć w artykule, [zarządzać użyciem i kosztami z dzienników Azure monitor](../../azure-monitor/platform/manage-cost-storage.md#estimating-the-costs-to-manage-your-environment).

## <a name="view-conditional-access-insights-workbook"></a>Wyświetl skoroszyt usługi Dostęp warunkowy Insights

Po zintegrowaniu dzienników usługi Azure AD z Azure Monitor można monitorować wpływ zasad dostępu warunkowego na nowe skoroszyty usługi Dostęp warunkowy.

1. Zaloguj się do **Azure Portal** jako administrator zabezpieczeń lub Administrator globalny.
1. Przejdź do **Azure Active Directory**  >  **skoroszytów**.
1. Wybierz pozycję **dostęp warunkowy szczegółowe informacje**.
1. Wybierz co najmniej jedną zasadę z listy rozwijanej **zasady dostępu warunkowego** . Wszystkie włączone zasady są domyślnie zaznaczone.
1. Wybierz zakres czasu (Jeśli zakres czasu przekracza dostęp do dostępnego zestawu danych, raport będzie zawierać wszystkie dostępne dane). Po ustawieniu **zasad dostępu warunkowego** i parametrów **zakresu czasu** raport zostanie załadowany.
   1. Opcjonalnie można wyszukać poszczególnych **użytkowników** lub **aplikacje** , aby zawęzić zakres raportu.
1. Wybierz między wyświetlaniem danych w przedziale czasowym przez liczbę użytkowników lub liczbę logowań.
1. W zależności od **widoku danych**, **Podsumowanie wpływu** wyświetla liczbę użytkowników lub logowania w zakresie wybranych parametrów, pogrupowane według łącznej liczby, **sukcesu**, **niepowodzenia**, **wymaganej akcji użytkownika**i **nie zostały zastosowane**. Wybierz kafelek, aby zapoznać się z logowaniem określonego typu wyniku. 
   1. Jeśli zmieniono parametry skoroszytu, możesz zapisać kopię do użycia w przyszłości. Wybierz ikonę Zapisz u góry raportu i podaj nazwę i lokalizację, w której chcesz zapisać.
1. Przewiń w dół, aby wyświetlić podział logowań dla każdego warunku.
1. Wyświetl **szczegóły logowania** w dolnej części raportu, aby sprawdzić, czy poszczególne zdarzenia logowania zostały przefiltrowane według powyższych opcji.

> [!TIP] 
> Musisz przejść do szczegółów konkretnego zapytania lub wyeksportować szczegóły logowania? Wybierz przycisk z prawej strony dowolnego zapytania, aby otworzyć zapytanie w Log Analytics. Wybierz pozycję Eksportuj, aby wyeksportować do pliku CSV lub Power BI.

## <a name="common-problems-and-solutions"></a>Typowe problemy i rozwiązania

### <a name="why-are-the-queries-in-the-workbook-failing"></a>Dlaczego zapytania w skoroszycie kończą się niepowodzeniem?

Klienci zauważyły, że zapytania czasami kończą się niepowodzeniem, jeśli z skoroszytem skojarzone są nieprawidłowe lub wiele obszarów roboczych. Aby rozwiązać ten problem, kliknij pozycję **Edytuj** w górnej części skoroszytu, a następnie ustawienia koła zębatego. Wybierz, a następnie usuń obszary robocze, które nie są skojarzone ze skoroszytem. Z każdym skoroszytem powinien być skojarzony tylko jeden obszar roboczy.

### <a name="why-doesnt-the-conditional-access-policies-dropdown-parameter-contain-my-policies"></a>Dlaczego parametr listy rozwijanej zasady dostępu warunkowego nie zawiera moich zasad?

Lista rozwijana zasady dostępu warunkowego jest wypełniana przez wykonywanie zapytań dotyczących najnowszych logowań w okresie 4 godzin. Jeśli dzierżawa nie ma żadnych logowań w ciągu ostatnich 4 godzin, istnieje możliwość, że lista rozwijana będzie pusta. Jeśli to opóźnienie jest problemem trwałym, takim jak w małych dzierżawach z rzadko występującymi logowaniami, Administratorzy mogą edytować zapytanie dla listy rozwijanej zasady dostępu warunkowego i wydłużyć czas wykonywania zapytania na czas dłuższy niż 4 godziny.

## <a name="next-steps"></a>Następne kroki

[Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)

Aby uzyskać więcej informacji na temat skoroszytów usługi Azure AD, zapoznaj się z artykułem [dotyczącym Azure Active Directory raportów za pomocą Azure monitor skoroszytów](../reports-monitoring/howto-use-azure-monitor-workbooks.md).
