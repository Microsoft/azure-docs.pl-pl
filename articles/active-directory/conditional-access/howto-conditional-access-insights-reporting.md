---
title: Szczegółowe informacje o dostępie warunkowym i skoroszycie raportowania — Azure Active Directory
description: Korzystanie z skoroszytu usługi Azure AD Insights i raportowania w celu rozwiązywania problemów z zasadami
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/27/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c2364eae0d04da8f8e6fe38ae80db7adb8666ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89049421"
---
# <a name="conditional-access-insights-and-reporting"></a>Szczegółowe informacje i raportowanie dostępu warunkowego

Skoroszyt usługi Dostęp warunkowy usługi Insights i raportowania pozwala zrozumieć wpływ zasad dostępu warunkowego w organizacji w miarę upływu czasu. Podczas logowania można zastosować co najmniej jedną zasadę dostępu warunkowego, udzielając dostępu, jeśli pewne kontrolki grantu są spełnione lub odmówili dostępu w przeciwnym razie. Ze względu na to, że podczas każdego logowania można ocenić wiele zasad dostępu warunkowego, skoroszyt usługi Insights i raportów umożliwia badanie wpływu poszczególnych zasad lub podzbiór wszystkich zasad.  

## <a name="prerequisites"></a>Wymagania wstępne

Aby włączyć szczegółowe informacje i skoroszyt raportowania, dzierżawca musi mieć obszar roboczy Log Analytics, aby zachować dane dzienników logowania. Aby można było korzystać z dostępu warunkowego, użytkownicy muszą mieć licencje na Azure AD — wersja Premium P1 lub P2.

Następujące role mogą uzyskać dostęp do usługi Insights i raportowania:  

- Administrator dostępu warunkowego 
- Czytelnik zabezpieczeń 
- Administrator zabezpieczeń 
- Czytnik globalny 
- Administrator globalny 

Użytkownicy muszą także mieć jedną z następujących Log Analytics ról obszaru roboczego:  

- Współautor  
- Właściciel 

### <a name="stream-sign-in-logs-from-azure-ad-to-azure-monitor-logs"></a>Przesyłanie strumieniowe dzienników logowania z usługi Azure AD do dzienników Azure Monitor 

Jeśli dzienniki usługi Azure AD nie zostały zintegrowane z dziennikami Azure Monitor, przed załadowaniem skoroszytu należy wykonać następujące czynności:  

1. [Utwórz obszar roboczy log Analytics w Azure monitor](../../azure-monitor/learn/quick-create-workspace.md).
1. [Integrowanie dzienników usługi Azure AD z dziennikami Azure monitor](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

## <a name="how-it-works"></a>Jak to działa 

Aby uzyskać dostęp do szczegółowych informacji i skoroszytu raportów:  

1. Zaloguj się w witrynie **Azure Portal**.
1. Przejdź do **Azure Active Directory**  >  **zabezpieczenia**  >  i raportowanie**dostępu warunkowego**  >  **Insights and reporting**.

### <a name="get-started-select-parameters"></a>Wprowadzenie: Wybieranie parametrów 

Pulpit nawigacyjny szczegółowe informacje i raporty pozwala zobaczyć wpływ jednego lub kilku zasad dostępu warunkowego w określonym przedziale czasu. Zacznij od ustawienia każdego z parametrów w górnej części skoroszytu. 

![Pulpit nawigacyjny uzyskiwania dostępu warunkowego i raportowania w Azure Portal](./media/howto-conditional-access-insights-reporting/conditional-access-insights-and-reporting-dashboard.png)

**Zasady dostępu warunkowego**: Wybierz co najmniej jedną zasadę dostępu warunkowego, aby wyświetlić łączny wpływ. Zasady są rozdzielone na dwie grupy: włączone i zasady tylko do raportowania. Domyślnie zaznaczone są wszystkie włączone zasady. Te włączone zasady są obecnie wymuszane w dzierżawie.  

**Zakres czasu**: Wybierz zakres czasu z przedziału od 4 do 90 dni. Jeśli zostanie wybrany zakres czasu w dalszej części niż w przypadku zintegrowania dzienników usługi Azure AD z Azure Monitor, zostaną wyświetlone tylko logowania po upływie czasu integracji.  

**Użytkownik**: domyślnie pulpit nawigacyjny pokazuje wpływ wybranych zasad dla wszystkich użytkowników. Aby odfiltrować według pojedynczego użytkownika, wpisz nazwę użytkownika w polu tekstowym. Aby filtrować według wszystkich użytkowników, wpisz "Wszyscy użytkownicy" w polu tekstowym lub pozostaw pusty parametr. 

**Aplikacja**: domyślnie pulpit nawigacyjny pokazuje wpływ wybranych zasad dla wszystkich aplikacji. Aby odfiltrować według pojedynczej aplikacji, wpisz nazwę aplikacji w polu tekstowym. Aby filtrować według wszystkich aplikacji, wpisz "wszystkie aplikacje" w polu tekstowym lub pozostaw pusty parametr. 

**Widok danych**: Wybierz, czy chcesz, aby pulpit nawigacyjny pokazywał wyniki w postaci liczby użytkowników lub liczby logowań. Pojedynczy użytkownik może mieć setki logowania do wielu aplikacji z wieloma różnymi wynikami w danym przedziale czasu. W przypadku wybrania widoku danych przeznaczonego dla użytkowników można uwzględnić zarówno liczbę powodzeń, jak i niepowodzeń (na przykład jeśli nie ma 10 użytkowników, 8 z nich mogło wynikać z sukcesu w ciągu ostatnich 30 dni, a 9 z nich mogło wynikać z błędów w ciągu ostatnich 30 dni).

## <a name="impact-summary"></a>Podsumowanie wpływu 

Po ustawieniu parametrów, podsumowanie wpływu zostanie załadowane. Podsumowanie pokazuje, ile użytkowników lub logowań w przedziale czasu spowodowało "powodzenie", "Niepowodzenie", "Akcja wymagana przez użytkownika" lub "nie zastosowano" podczas oceny wybranych zasad.  

![Podsumowanie wpływu w skoroszycie dostępu warunkowego](./media/howto-conditional-access-insights-reporting/workbook-impact-summary.png)

**Łącznie**: liczba użytkowników lub logowań w okresie, w którym została oceniona co najmniej jedna z wybranych zasad.

**Sukces**: liczba użytkowników lub logowań w okresie, w którym połączony wynik wybranych zasad miał wartość "powodzenie" lub "tylko raport: powodzenie".

**Niepowodzenie**: liczba użytkowników lub logowań w okresie, w którym wynik co najmniej jednej z wybranych zasad miał wartość "Niepowodzenie" lub "tylko raport: niepowodzenie".

**Wymagana akcja użytkownika**: liczba użytkowników lub logowań w okresie, w którym połączony wynik wybranych zasad miał wartość "tylko raport: wymagana akcja użytkownika". Akcja użytkownika jest wymagana, gdy interaktywna kontrolka, taka jak uwierzytelnianie wieloskładnikowe, jest wymagana przez zasady dostępu warunkowego tylko do raportów. Ponieważ interaktywny formant Grant nie jest wymuszany przez zasady tylko do raportowania, nie można określić sukcesu lub niepowodzenia.  

**Nie zastosowano**: liczba użytkowników lub logowań w okresie, w którym nie zastosowano żadnych wybranych zasad.

### <a name="understanding-the-impact"></a>Zrozumienie wpływu 

![Podział skoroszytu na warunek i stan](./media/howto-conditional-access-insights-reporting/workbook-breakdown-condition-and-status.png)

Wyświetl podział użytkowników lub logowania dla każdego z tych warunków. Można filtrować logowania określonego wyniku (na przykład powodzenie lub niepowodzenie), wybierając pozycję na kafelkach podsumowania w górnej części skoroszytu. Można zobaczyć podział logowań dla poszczególnych warunków dostępu warunkowego: stan urządzenia, platforma urządzenia, aplikacja kliencka, lokalizacja, aplikacja i ryzyko związane z logowaniem.  

## <a name="sign-in-details"></a>Szczegóły logowania 

![Szczegóły logowania skoroszytu](./media/howto-conditional-access-insights-reporting/workbook-sign-in-details.png)

Możesz również zbadać logowania określonego użytkownika, wyszukując logowania w dolnej części pulpitu nawigacyjnego. Zapytanie po lewej stronie wyświetla najbardziej częste użytkowników. Wybranie użytkownika spowoduje odfiltrowanie zapytania po prawej stronie.  

> [!NOTE]
> Podczas pobierania dzienników logowania wybierz format JSON, aby dołączyć raport dostępu warunkowego — tylko dane wynikowe.

## <a name="configure-a-conditional-access-policy-in-report-only-mode"></a>Konfigurowanie zasad dostępu warunkowego w trybie tylko do raportowania

Aby skonfigurować zasady dostępu warunkowego w trybie tylko do raportowania:

1. Zaloguj się do **Azure Portal** jako administrator dostępu warunkowego, administrator zabezpieczeń lub Administrator globalny.
1. Przejdź do **Azure Active Directory**  >  **Security**  >  **dostępu warunkowego**zabezpieczeń.
1. Wybierz istniejące zasady lub Utwórz nowe zasady.
1. W obszarze **Włączanie zasad** ustaw opcję Przełącz do trybu **tylko raportowanie** .
1. Wybierz pozycję **Zapisz**

> [!TIP]
> Edytowanie stanu **zasad włączania** istniejących zasad z **w** do **raportu —** powoduje wyłączenie tylko istniejącego wymuszania zasad. 

## <a name="troubleshooting"></a>Rozwiązywanie problemów

### <a name="why-are-queries-failing-due-to-a-permissions-error"></a>Dlaczego zapytania kończą się niepowodzeniem z powodu błędu uprawnień?

Aby można było uzyskać dostęp do skoroszytu, potrzebne są odpowiednie uprawnienia usługi Azure AD, a także uprawnienia do Log Analytics obszaru roboczego. Aby sprawdzić, czy masz odpowiednie uprawnienia obszaru roboczego, uruchamiając przykładowe zapytanie usługi log Analytics:

1. Zaloguj się w witrynie **Azure Portal**.
1. Przejdź do **Azure Active Directory**  >  **dzienników**Azure Active Directory.
1. Wpisz `SigninLogs` w polu zapytania i wybierz polecenie **Uruchom**.
1. Jeśli zapytanie nie zwraca żadnych wyników, być może obszar roboczy nie został poprawnie skonfigurowany. 

![Rozwiązywanie problemów z nieudanymi zapytaniami](./media/howto-conditional-access-insights-reporting/query-troubleshoot-sign-in-logs.png)

Aby uzyskać więcej informacji o sposobach przesyłania strumieniowego dzienników logowania usługi Azure AD do obszaru roboczego Log Analytics, zobacz artykuł [integracja dzienników usługi Azure AD z dziennikami Azure monitor](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md).

### <a name="why-are-the-queries-in-the-workbook-failing"></a>Dlaczego zapytania w skoroszycie kończą się niepowodzeniem?

Klienci zauważyły, że zapytania czasami kończą się niepowodzeniem, jeśli z skoroszytem skojarzone są nieprawidłowe lub wiele obszarów roboczych. Aby rozwiązać ten problem, kliknij pozycję **Edytuj** w górnej części skoroszytu, a następnie ustawienia koła zębatego. Wybierz, a następnie usuń obszary robocze, które nie są skojarzone ze skoroszytem. Z każdym skoroszytem powinien być skojarzony tylko jeden obszar roboczy.

### <a name="why-is-the-conditional-access-policies-parameter-is-empty"></a>Dlaczego parametr zasady dostępu warunkowego jest pusty?

Lista zasad jest generowana przez przejrzenie zasad ocenionych dla ostatniego zdarzenia logowania. Jeśli w dzierżawie nie ma żadnych ostatnich logowań, może być konieczne odczekanie kilku minut, zanim skoroszyt załaduje listę zasad dostępu warunkowego. Może to nastąpić natychmiast po skonfigurowaniu Log Analytics lub może trwać dłużej, jeśli dzierżawa nie ma najnowszego działania związanego z logowaniem.

### <a name="why-is-the-workbook-taking-a-long-time-to-load"></a>Dlaczego pobieranie skoroszytu trwa dużo czasu?  

W zależności od wybranego zakresu czasu i rozmiaru dzierżawy skoroszyt może oceniać bardzo dużą liczbę zdarzeń logowania. W przypadku dużych dzierżawców wielkość logowań może przekroczyć pojemność zapytania wynoszącą Log Analytics. Spróbuj skrócić zakres czasu do 4 godzin, aby sprawdzić, czy skoroszyt został załadowany.  

### <a name="after-loading-for-a-few-minutes-why-is-the-workbook-returning-zero-results"></a>Po załadowaniu przez kilka minut, dlaczego skoroszyt zwróci zero wyników? 

Gdy objętość logowań przekracza pojemność kwerendy Log Analytics, skoroszyt zwróci zero wyników. Spróbuj skrócić zakres czasu do 4 godzin, aby sprawdzić, czy skoroszyt został załadowany.  

### <a name="can-i-save-my-parameter-selections"></a>Czy mogę zapisać moje wybrane parametry?  

Możesz zapisać wybrane opcje parametrów w górnej części skoroszytu, przechodząc do **Azure Active Directory**  >  **skoroszyty**  >  **dostępu warunkowego i raportowania**. W tym miejscu znajdziesz szablon skoroszytu, w którym można edytować skoroszyt i zapisać kopię w obszarze roboczym, włącznie z wybranymi parametrami, w **raportach** lub **raportach udostępnionych**. 

### <a name="can-i-edit-and-customize-the-workbook-with-additional-queries"></a>Czy mogę edytować i dostosowywać skoroszyt z dodatkowymi zapytaniami? 

Można edytować i dostosowywać skoroszyt, przechodząc do **Azure Active Directory**  >  **skoroszyty**  >  **dostępu warunkowego i raportowania**. W tym miejscu znajdziesz szablon skoroszytu, w którym można edytować skoroszyt i zapisać kopię w obszarze roboczym, włącznie z wybranymi parametrami, w **raportach** lub **raportach udostępnionych**. Aby rozpocząć edytowanie zapytań, kliknij przycisk **Edytuj** w górnej części skoroszytu.  
 
## <a name="next-steps"></a>Następne kroki

- [Tryb tylko do raportowania dostępu warunkowego](concept-conditional-access-report-only.md)

- Aby uzyskać więcej informacji na temat skoroszytów usługi Azure AD, zapoznaj się z artykułem [dotyczącym Azure Active Directory raportów za pomocą Azure monitor skoroszytów](../reports-monitoring/howto-use-azure-monitor-workbooks.md).

- [Wspólne zasady dostępu warunkowego](concept-conditional-access-policy-common.md)
