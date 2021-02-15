---
title: Monitoruj kondycję logowania aplikacji pod kątem odporności w Azure Active Directory
description: Twórz zapytania i powiadomienia, aby monitorować kondycję logowania aplikacji.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 01/10/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40bfa27dba905cb2e9a363c7739f0a43e7c2afdf
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100101378"
---
# <a name="monitoring-application-sign-in-health-for-resilience"></a>Monitorowanie kondycji logowania aplikacji pod kątem odporności

Aby zwiększyć odporność infrastruktury, skonfiguruj monitorowanie kondycji logowania aplikacji dla kluczowych aplikacji, aby otrzymać Alert w przypadku wystąpienia zdarzenia wpływającego na nie. Aby pomóc w tym wysiłku, można skonfigurować alerty w oparciu o skoroszyt kondycji logowania. 

Ten skoroszyt umożliwia administratorom monitorowanie żądań uwierzytelniania dla aplikacji w dzierżawie. Dostępne są następujące kluczowe funkcje:

* Skonfiguruj skoroszyt, aby monitorować wszystkie lub pojedyncze aplikacje niemal w czasie rzeczywistym.

* Skonfiguruj alerty powiadamiające o zmianie wzorców uwierzytelniania, aby umożliwić badanie i podejmowanie działań.

* Porównaj trendy w danym okresie, na przykład tydzień w tygodniu, który jest domyślnym ustawieniem skoroszytu.

> [!NOTE]
> Aby wyświetlić wszystkie dostępne skoroszyty i wymagania wstępne dotyczące ich używania, zapoznaj się z tematem [jak używać skoroszytów Azure monitor dla raportów](../reports-monitoring/howto-use-azure-monitor-workbooks.md).

Podczas zdarzenia wpływającego mogą wystąpić dwie rzeczy:

* Liczba logowań dla aplikacji może być precipitously, ponieważ użytkownicy nie mogą się zalogować.

* Liczba błędów logowania może wzrosnąć. 

Ten artykuł zawiera instrukcje dotyczące konfigurowania skoroszytu kondycji logowania w celu monitorowania zakłóceń dla logowania użytkowników.

## <a name="prerequisites"></a>Wymagania wstępne 

* Dzierżawa usługi Azure AD.

* Użytkownik z rolą administratora globalnego lub administratora zabezpieczeń dla dzierżawy usługi Azure AD.

* Log Analytics obszar roboczy w ramach subskrypcji platformy Azure w celu wysyłania dzienników do Azure Monitor dzienników. 

   * Dowiedz się [, jak utworzyć obszar roboczy log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

* Dzienniki usługi Azure AD zintegrowane z dziennikami Azure Monitor

   * Dowiedz się, jak [zintegrować dzienniki logowania usługi Azure AD ze strumieniem Azure monitor.](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

 

## <a name="configure-the-app-sign-in-health-workbook"></a>Skonfiguruj skoroszyt kondycji aplikacji 

Aby uzyskać dostęp do skoroszytów, Otwórz **Azure Portal**, wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **skoroszyty**.

Zobaczysz skoroszyty w obszarze użycie, dostęp warunkowy i rozwiązywanie problemów. W sekcji użycie zostanie wyświetlony skoroszyt dotyczący kondycji logowania aplikacji.

Po użyciu skoroszytu może on pojawić się w sekcji ostatnio modyfikowane skoroszyty.

![Zrzut ekranu przedstawiający galerię skoroszytów w Azure Portal.](./media/monitor-sign-in-health-for-resilience/sign-in-health-workbook.png)


Skoroszyt dotyczący kondycji aplikacji umożliwia wizualizację tego, co dzieje się z logowaniem. 

Domyślnie skoroszyt przedstawia dwa wykresy. Te wykresy porównują, co dzieje się z aplikacjami, w tym samym czasie tygodnia temu. Niebieskie linie są aktualne, a pomarańczowy wiersze to poprzedni tydzień.

![Zrzut ekranu przedstawiający wykresy dotyczące kondycji.](./media/monitor-sign-in-health-for-resilience/sign-in-health-graphs.png)

**Pierwszy wykres jest godzinnym zużyciem (liczba pomyślnych użytkowników)**. Porównanie bieżącej liczby pomyślnych użytkowników z typowym okresem użytkowania pomaga w wykorzystaniu pozostałego użycia, które może wymagać zbadania. Pozostała szybkość użycia może pomóc w wykrywaniu problemów z wydajnością i wykorzystaniem, których nie może użyć współczynnika błędów. Jeśli na przykład użytkownicy nie mogą nawiązać połączenia z aplikacją w celu podjęcia próby zalogowania się, nie wystąpiły żadne błędy, ale tylko w przypadku użycia. Przykładowe zapytanie dotyczące tych danych można znaleźć w poniższej sekcji.

Drugi wykres jest wskaźnikiem błędu co godzinę. Stopień wzrostu współczynnika błędów może wskazywać na problem z mechanizmami uwierzytelniania. Współczynnik błędów można mierzyć tylko wtedy, gdy użytkownicy będą mogli próbować uwierzytelnić. Jeśli użytkownicy nie mogą uzyskać dostępu w celu podjęcia próby, nie będą wyświetlane błędy.

Można skonfigurować alert, który powiadamia określoną grupę, gdy współczynnik użycia lub niepowodzeń przekroczy określony próg. Przykładowe zapytanie dotyczące tych danych można znaleźć w poniższej sekcji.

 ## <a name="configure-the-query-and-alerts"></a>Konfigurowanie zapytania i alertów

Reguły alertów są tworzone w Azure Monitor i mogą automatycznie uruchamiać zapisane zapytania lub niestandardowe przeszukiwania dzienników w regularnych odstępach czasu.

Poniższe instrukcje służą do tworzenia alertów e-mail na podstawie zapytań uwzględnionych na wykresach. Poniższe przykładowe skrypty wyślą powiadomienie e-mail, gdy

* pomyślne użycie spadnie o 90% od tej samej godziny dwa dni temu, jak w grafie użycia godzinowego w poprzedniej sekcji. 

* Współczynnik awaryjności zwiększa się o 90% od tej samej godziny dwa dni temu, jak w przypadku wykresu współczynnika niepowodzeń w poprzedniej sekcji. 

 Aby skonfigurować zapytanie bazowe i ustawić alerty, wykonaj następujące czynności. Przykładowa kwerenda zostanie użyta jako podstawa dla konfiguracji. Wyjaśnienie struktury zapytania pojawia się na końcu tej sekcji.

Aby uzyskać więcej informacji na temat tworzenia i wyświetlania alertów dzienników oraz zarządzania nimi za pomocą Azure Monitor zobacz [Zarządzanie alertami dzienników](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log).

 
1. W skoroszycie wybierz pozycję **Edytuj**, a następnie wybierz **ikonę zapytania** tuż nad prawą stroną grafu.   

   [![Zrzut ekranu przedstawiający edytowanie skoroszytu.](./media/monitor-sign-in-health-for-resilience/edit-workbook.png)](./media/monitor-sign-in-health-for-resilience/edit-workbook.png)

   Zostanie otwarty dziennik zapytania.

  [![Zrzut ekranu przedstawiający dziennik zapytań.](./media/monitor-sign-in-health-for-resilience/query-log.png)](/media/monitor-sign-in-health-for-resilience/query-log.png)
‎

2. Skopiuj jeden z następujących przykładowych skryptów dla nowej kwerendy Kusto.

**Zapytanie Kusto do porzucenia w użyciu**

```Kusto

let thisWeek = SigninLogs

| where TimeGenerated > ago(1h)

| project TimeGenerated, AppDisplayName, UserPrincipalName

//| where AppDisplayName contains "Office 365 Exchange Online"

| summarize users = dcount(UserPrincipalName) by bin(TimeGenerated, 1hr)

| sort by TimeGenerated desc

| serialize rn = row_number();

let lastWeek = SigninLogs

| where TimeGenerated between((ago(1h) - totimespan(2d))..(now() - totimespan(2d)))

| project TimeGenerated, AppDisplayName, UserPrincipalName

//| where AppDisplayName contains "Office 365 Exchange Online"

| summarize usersPriorWeek = dcount(UserPrincipalName) by bin(TimeGenerated, 1hr)

| sort by TimeGenerated desc

| serialize rn = row_number();

thisWeek

| join

(

 lastWeek

)

on rn

| project TimeGenerated, users, usersPriorWeek, difference = abs(users - usersPriorWeek), max = max_of(users, usersPriorWeek)

| where (difference * 2.0) / max > 0.9

```

 

**Kusto zapytanie o zwiększenie współczynnika niepowodzeń**


```kusto

let thisWeek = SigninLogs

| where TimeGenerated > ago(1 h)

| project TimeGenerated, UserPrincipalName, AppDisplayName, status = case(Status.errorCode == "0", "success", "failure")

| where AppDisplayName == **APP NAME**

| summarize success = countif(status == "success"), failure = countif(status == "failure") by bin(TimeGenerated, 1h)

| project TimeGenerated, failureRate = (failure * 1.0) / ((failure + success) * 1.0)

| sort by TimeGenerated desc

| serialize rn = row_number();

let lastWeek = SigninLogs

| where TimeGenerated between((ago(1 h) - totimespan(2d))..(ago(1h) - totimespan(2d)))

| project TimeGenerated, UserPrincipalName, AppDisplayName, status = case(Status.errorCode == "0", "success", "failure")

| where AppDisplayName == **APP NAME**

| summarize success = countif(status == "success"), failure = countif(status == "failure") by bin(TimeGenerated, 1h)

| project TimeGenerated, failureRatePriorWeek = (failure * 1.0) / ((failure + success) * 1.0)

| sort by TimeGenerated desc

| serialize rn = row_number();

thisWeek

| join (lastWeek) on rn

| project TimeGenerated, failureRate, failureRatePriorWeek

| where abs(failureRate – failureRatePriorWeek) > **THRESHOLD VALUE**

```

3. Wklej zapytanie w oknie i wybierz polecenie **Uruchom**. Upewnij się, że na poniższym obrazie zostanie wyświetlony komunikat ukończono, a wyniki poniżej tego komunikatu.

   [![Zrzut ekranu przedstawiający wyniki przebiegu kwerendy.](./media/monitor-sign-in-health-for-resilience/run-query.png)](./media/monitor-sign-in-health-for-resilience/run-query.png)

4. Zaznacz zapytanie i wybierz pozycję + **Nowa reguła alertu**. 
 
   [![Zrzut ekranu przedstawiający ekran Nowa reguła alertu.](./media/monitor-sign-in-health-for-resilience/new-alert-rule.png)](./media/monitor-sign-in-health-for-resilience/new-alert-rule.png)


5. Skonfiguruj warunki alertów. W sekcji warunek wybierz łącze, **gdy średnie wyszukiwanie w dzienniku niestandardowym jest większe niż liczba zdefiniowana przez logikę**. W okienku Konfigurowanie logiki sygnału przewiń do logiki alertu

   [![Zrzut ekranu przedstawiający ekran Konfigurowanie alertów.](./media/monitor-sign-in-health-for-resilience/configure-alerts.png)](./media/monitor-sign-in-health-for-resilience/configure-alerts.png)
 
   * **Wartość progowa**: 0. Ta wartość będzie wysyłać alerty dotyczące wszystkich wyników.

   * **Okres próbny (w minutach)**: 60. Ta wartość jest wyszukiwana w godzinie

   * **Częstotliwość (w minutach)**: 60. Ta wartość ustawia okres próbny na godzinę w ciągu ostatniej godziny.

   * Kliknij **Gotowe**.

6. W sekcji **Akcje** skonfiguruj następujące ustawienia:  

    [![Zrzut ekranu przedstawiający stronę Tworzenie reguły alertu.](./media/monitor-sign-in-health-for-resilience/create-alert-rule.png)](./media/monitor-sign-in-health-for-resilience/create-alert-rule.png)

   * W obszarze **Akcje** wybierz **pozycję Wybierz grupę akcji**, a następnie Dodaj grupę, dla której chcesz otrzymywać powiadomienia o alertach.

   * W obszarze **Dostosowywanie akcji** wybierz pozycję **alerty e-mail**.

   * Dodaj **wiersz tematu**.

7. W obszarze **szczegóły reguły alertu** skonfiguruj następujące ustawienia:

   * Dodawanie opisowej nazwy i opisu.

   * Wybierz **grupę zasobów** , do której chcesz dodać alert.

   * Wybierz domyślną **ważność** alertu.

   * Wybierz pozycję **Włącz regułę alertów po utworzeniu** , jeśli chcesz, aby była aktywna natychmiast, w przeciwnym razie wybierz pozycję **Pomiń alerty**.

8. Wybierz pozycję **Utwórz regułę alertu**.

9. Wybierz pozycję **Zapisz**, wprowadź nazwę zapytania, **Zapisz jako zapytanie z kategorią alertu**. Następnie wybierz pozycję **Zapisz** ponownie.  

   [![Zrzut ekranu przedstawiający przycisk Zapisz zapytanie.](./media/monitor-sign-in-health-for-resilience/save-query.png)](./media/monitor-sign-in-health-for-resilience/save-query.png)



### <a name="refine-your-queries-and-alerts"></a>Uściślij zapytania i alerty
Modyfikowanie zapytań i alertów w celu uzyskania maksymalnej skuteczności.

* Pamiętaj o przetestowaniu alertów.

* Zmodyfikuj czułość i częstotliwość alertów, aby otrzymywać ważne powiadomienia. Administratorzy mogą stać się Desensitized do alertów, jeśli wystąpią zbyt wiele i nie przegapą znaczenia. 

* Upewnij się, że wiadomość e-mail, z której są wysyłane alerty, jest dodawana do listy dozwolonych nadawców. W przeciwnym razie możesz pominąć powiadomienia ze względu na filtr spamu na kliencie poczty e-mail. 

* Zapytanie o alerty w Azure Monitor może zawierać tylko wyniki z ostatnich 48 godzin. [Jest to bieżące ograniczenie według projektu](https://github.com/MicrosoftDocs/azure-docs/issues/22637).

## <a name="create-processes-to-manage-alerts"></a>Tworzenie procesów w celu zarządzania alertami

Po skonfigurowaniu zapytania i alertów Utwórz procesy biznesowe, aby zarządzać alertami.

* Kto będzie monitorować skoroszyt i kiedy?
* Po wygenerowaniu alertu, który sprawdzi?

* Jakie są wymagania dotyczące komunikacji? Kto zostanie utworzony i będzie je otrzymywał?

* Jeśli wystąpi awaria, jakie procesy biznesowe muszą zostać wyzwolone?

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o skoroszytach](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks)

 

 

 
