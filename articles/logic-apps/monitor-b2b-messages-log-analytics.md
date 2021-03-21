---
title: Monitorowanie komunikatów B2B przy użyciu Azure Monitor
description: Rozwiązywanie problemów z AS2, X12 i EDIFACT przez skonfigurowanie Azure Monitor dzienników i zbieranie danych diagnostycznych dla Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 765c15897bd5d435503d3bef07e76a93b148971c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "100596853"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-b2b-messages-in-azure-logic-apps"></a>Konfigurowanie dzienników usługi Azure Monitor i zbieranie danych diagnostycznych dotyczących komunikatów B2B w usłudze Azure Logic Apps

Po skonfigurowaniu komunikacji B2B między partnerami handlowymi na Twoim koncie integracji partnerzy mogą wymieniać komunikaty przy użyciu protokołów, takich jak AS2, X12 i EDIFACT. Aby sprawdzić, czy ta komunikacja działa w oczekiwany sposób, możesz skonfigurować [Azure monitor dzienników](../azure-monitor/logs/data-platform-logs.md) dla konta integracji. [Azure monitor](../azure-monitor/overview.md) ułatwia monitorowanie środowisk w chmurze i lokalnych, dzięki czemu można łatwiej zachować swoją dostępność i wydajność. Korzystając z dzienników Azure Monitor, można rejestrować i przechowywać dane dotyczące danych i zdarzeń środowiska uruchomieniowego, takich jak zdarzenia wyzwalania, zdarzenia uruchamiania i zdarzenia akcji w [obszarze roboczym log Analytics](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace). W przypadku komunikatów, rejestrowanie zbiera również informacje takie jak:

* Liczba komunikatów i stan
* Stan potwierdzeń
* Korelacje między komunikatami i potwierdzeniami
* Szczegółowe opisy błędów dla niepowodzeń

Azure Monitor umożliwia tworzenie [zapytań dzienników](../azure-monitor/logs/log-query-overview.md) , aby ułatwić znajdowanie i przeglądanie tych informacji. Możesz również [używać tych danych diagnostycznych z innymi usługami platformy Azure](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data), takimi jak Azure Storage i Azure Event Hubs.

Aby skonfigurować rejestrowanie dla konta integracji, [Zainstaluj rozwiązanie Logic Apps B2B](#install-b2b-solution) w Azure Portal. To rozwiązanie zawiera zagregowane informacje dotyczące zdarzeń wiadomości B2B. Następnie, aby włączyć rejestrowanie i utworzyć zapytania dotyczące tych informacji, skonfiguruj [dzienniki Azure monitor](#set-up-resource-logs).

W tym artykule pokazano, jak włączyć rejestrowanie Azure Monitor dla konta integracji.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Obszar roboczy usługi Log Analytics. Jeśli nie masz obszaru roboczego Log Analytics, zapoznaj się [z tematem tworzenie log Analytics obszaru roboczego](../azure-monitor/logs/quick-create-workspace.md).

* Aplikacja logiki, która została skonfigurowana przy użyciu Azure Monitor rejestrowania i wysyłania tych informacji do Log Analytics obszaru roboczego. Dowiedz się [, jak skonfigurować dzienniki Azure monitor dla aplikacji logiki](../logic-apps/monitor-logic-apps.md).

* Konto integracji połączone z aplikacją logiki. Dowiedz się [, jak połączyć konto integracji z aplikacją logiki](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

<a name="install-b2b-solution"></a>

## <a name="install-logic-apps-b2b-solution"></a>Zainstaluj Logic Apps B2B rozwiązanie

Zanim dzienniki Azure Monitor będą mogły śledzić wiadomości B2B dla aplikacji logiki, Dodaj rozwiązanie **Logic Apps B2B** do obszaru roboczego log Analytics.

1. W polu wyszukiwania [Azure Portal](https://portal.azure.com)wprowadź wartość `log analytics workspaces` , a następnie wybierz pozycję **log Analytics obszary robocze**.

   ![Wybierz pozycję "Log Analytics obszary robocze"](./media/monitor-b2b-messages-log-analytics/find-select-log-analytics-workspaces.png)

1. W obszarze **obszary robocze log Analytics** wybierz swój obszar roboczy.

   ![Wybierz obszar roboczy Log Analytics](./media/monitor-b2b-messages-log-analytics/select-log-analytics-workspace.png)

1. W okienku Przegląd w obszarze **wprowadzenie do log Analytics**  >  **konfigurowania rozwiązań monitorowania** wybierz pozycję **Wyświetl rozwiązania**.

   ![W okienku Przegląd wybierz pozycję "Wyświetl rozwiązania"](./media/monitor-b2b-messages-log-analytics/log-analytics-workspace.png)

1. W okienku Przegląd wybierz pozycję **Dodaj**.

   ![W okienku Przegląd Dodaj nowe rozwiązanie](./media/monitor-b2b-messages-log-analytics/add-logic-apps-management-solution.png)

1. Po otwarciu **portalu Marketplace** w polu wyszukiwania wpisz `logic apps b2b` , a następnie wybierz pozycję **Logic Apps B2B**.

   ![W witrynie Marketplace wybierz pozycję "Logic Apps Management".](./media/monitor-b2b-messages-log-analytics/select-logic-apps-b2b-solution.png)

1. W okienku opis rozwiązania wybierz pozycję **Utwórz**.

   ![Wybierz pozycję "Utwórz", aby dodać rozwiązanie "Logic Apps B2B"](./media/monitor-b2b-messages-log-analytics/create-logic-apps-b2b-solution.png)

1. Przejrzyj i Potwierdź obszar roboczy Log Analytics, w którym chcesz zainstalować rozwiązanie, a następnie wybierz pozycję **Utwórz** ponownie.

   ![Wybierz pozycję "Utwórz" dla "Logic Apps B2B"](./media/monitor-b2b-messages-log-analytics/confirm-log-analytics-workspace.png)

   Gdy platforma Azure wdroży rozwiązanie w grupie zasobów platformy Azure, która zawiera obszar roboczy Log Analytics, rozwiązanie zostanie wyświetlone w okienku podsumowania obszaru roboczego. Po przetworzeniu komunikatów B2B liczba komunikatów w tym okienku jest aktualizowana.

   ![Okienko podsumowania obszaru roboczego](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>Konfigurowanie dzienników Azure Monitor

Rejestrowanie Azure Monitor można włączyć bezpośrednio z poziomu konta integracji.

1. W [Azure Portal](https://portal.azure.com)Znajdź i wybierz swoje konto integracji.

   ![Znajdź i wybierz swoje konto integracji](./media/monitor-b2b-messages-log-analytics/find-integration-account.png)

1. W menu konta integracji w obszarze **monitorowanie** wybierz pozycję **Ustawienia diagnostyczne**. Wybierz pozycję **Dodaj ustawienia diagnostyczne**.

   ![W obszarze "monitorowanie" Wybierz pozycję "Ustawienia diagnostyki".](./media/monitor-b2b-messages-log-analytics/monitor-diagnostics-settings.png)

1. Aby utworzyć ustawienie, wykonaj następujące kroki:

   1. Podaj nazwę ustawienia.

   1. Wybierz pozycję **Wyślij do log Analytics**.

   1. W obszarze **subskrypcja** wybierz subskrypcję platformy Azure, która jest skojarzona z obszarem roboczym log Analytics.

   1. W **obszarze obszar roboczy log Analytics** wybierz obszar roboczy, którego chcesz użyć.

   1. W obszarze **Dziennik** wybierz kategorię **IntegrationAccountTrackingEvents** , która określa kategorię zdarzenia, która ma zostać zarejestrowana.

   1. Po zakończeniu wybierz pozycję **Zapisz**.

   Na przykład: 

   ![Konfigurowanie dzienników Azure Monitor w celu zbierania danych diagnostycznych](./media/monitor-b2b-messages-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-message-status"></a>

## <a name="view-message-status"></a>Wyświetl stan komunikatu

Po uruchomieniu aplikacji logiki można wyświetlić stan i dane dotyczące tych komunikatów w obszarze roboczym Log Analytics.

1. W polu wyszukiwania [Azure Portal](https://portal.azure.com) Znajdź i Otwórz obszar roboczy log Analytics.

1. W menu obszaru roboczego wybierz pozycję **Podsumowanie obszaru roboczego**  >  **Logic Apps B2B**.

   ![Okienko podsumowania obszaru roboczego](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

   > [!NOTE]
   > Jeśli kafelek Logic Apps B2B nie pokaże natychmiast wyników po uruchomieniu, spróbuj wybrać pozycję **Odśwież** lub poczekaj na chwilę przed ponowieniem próby.

   Domyślnie na kafelku **Logic Apps B2B** są wyświetlane dane oparte na jednym dniu. Aby zmienić zakres danych na inny interwał, zaznacz kontrolkę zakres w górnej części strony:

   ![Zmień interwał](./media/monitor-b2b-messages-log-analytics/change-summary-interval.png)

1. Po wyświetleniu pulpitu nawigacyjnego stan komunikatu można wyświetlić więcej szczegółów dotyczących określonego typu wiadomości, który pokazuje dane na podstawie jednego dnia. Wybierz kafelek dla **AS2**, **X12** lub **EDIFACT**.

   ![Wyświetlanie stanów komunikatów](./media/monitor-b2b-messages-log-analytics/workspace-summary-b2b-messages.png)

   Zostanie wyświetlona lista komunikatów dla wybranego kafelka. Załóżmy na przykład, że lista komunikatów AS2 może wyglądać następująco:

   ![Stany i szczegóły komunikatów AS2](./media/monitor-b2b-messages-log-analytics/as2-message-results-list.png)

   Aby dowiedzieć się więcej o właściwościach poszczególnych typów wiadomości, zobacz następujące opisy właściwości komunikatów:

   * [Właściwości komunikatu AS2](#as2-message-properties)
   * [Właściwości komunikatu X12](#x12-message-properties)
   * [Właściwości komunikatu EDIFACT](#EDIFACT-message-properties)

<!--
1. To view or export the inputs and outputs for specific messages, select those messages, and select **Download**. When you're prompted, save the .zip file to your local computer, and then extract that file.

   The extracted folder includes a folder for each selected message. If you set up acknowledgements, the message folder also includes files with acknowledgement details. Each message folder has at least these files:
   
   * Human-readable files with the input payload and output payload details
   * Encoded files with the inputs and outputs

   For each message type, you can find the folder and file name formats here:

   * [AS2 folder and file name formats](#as2-folder-file-names)
   * [X12 folder and file name formats](#x12-folder-file-names)
   * [EDIFACT folder and file name formats](#edifact-folder-file-names)

   ![Download message files](./media/monitor-b2b-messages-log-analytics/download-messages.png)

1. To view all actions that have the same run ID, on the **Log Search** page, select a message from the message list.

   You can sort these actions by column, or search for specific results.

   * To search results with prebuilt queries, select **Favorites**.

   * Learn [how to build queries by adding filters](../logic-apps/create-monitoring-tracking-queries.md). Or learn more about [how to find data with log searches in Azure Monitor logs](../azure-monitor/logs/log-query-overview.md).

   * To change query in the search box, update the query with the columns and values that you want to use as filters.
-->

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Opisy właściwości i formaty nazw dla komunikatów AS2, X12 i EDIFACT

Dla każdego typu komunikatu poniżej przedstawiono opisy właściwości i formaty nazw pobranych plików wiadomości.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>Opisy właściwości komunikatów AS2

Poniżej przedstawiono opisy właściwości dla każdego komunikatu AS2.

| Właściwość | Opis |
|----------|-------------|
| **Nadawca** | Partner gościa określony w **ustawieniach odbierania** lub partner hosta określony w polu **Wyślij ustawienia** dla umowy AS2 |
| **Odbiornik** | Partner hosta określony w obszarze **Ustawienia odbierania** lub partner gościa określony w polu **Wyślij ustawienia** dla umowy AS2 |
| **Aplikacja logiki** | Aplikacja logiki, w której są konfigurowane akcje AS2 |
| **Stan** | Stan komunikatu AS2 <br>Powodzenie = odebrano lub wysłano prawidłowy komunikat AS2. Nie skonfigurowano żadnych powiadomienia MDN. <br>Powodzenie = odebrano lub wysłano prawidłowy komunikat AS2. POWIADOMIENIA MDN jest skonfigurowany i odbierany lub wysyłany jest powiadomienia MDN. <br>Niepowodzenie = Odebrano nieprawidłowy komunikat AS2. Nie skonfigurowano żadnych powiadomienia MDN. <br>Oczekiwanie: Odebrano lub wysłano prawidłowy komunikat AS2. POWIADOMIENIA MDN jest skonfigurowany, a powiadomienia MDN jest oczekiwany. |
| **KOMUNIKATY** | Stan komunikatu powiadomienia MDN <br>Zaakceptowano = odebrano lub wysłano pozytywny powiadomienia MDN. <br>Oczekiwanie = oczekiwanie na otrzymanie lub wysłanie powiadomienia MDN. <br>Odrzucono = odebrano lub wysłano ujemną powiadomienia MDN. <br>Niewymagane = powiadomienia MDN nie jest skonfigurowany w umowie. |
| **Kierunek** | Kierunek komunikatu AS2 |
| **Identyfikator śledzenia** | Identyfikator, który jest skorelowany ze wszystkimi wyzwalaczami i akcjami w aplikacji logiki |
| **Identyfikator komunikatu** | Identyfikator komunikatu AS2 z nagłówków wiadomości AS2 |
| **Znacznik czasu** | Godzina przetworzenia komunikatu przez akcję AS2 |
|||

<!--
<a name="as2-folder-file-names"></a>

### AS2 name formats for downloaded message files

Here are the name formats for each downloaded AS2 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_AS2\_[correlation-ID]\_[message-ID]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_outputs.txt |
|||
-->

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>Opisy właściwości komunikatów X12

Poniżej przedstawiono opisy właściwości dla każdego komunikatu X12.

| Właściwość | Opis |
|----------|-------------|
| **Nadawca** | Partner gościa określony w **ustawieniach odbierania** lub partner hosta określony w polu **Wyślij ustawienia** dla umowy X12 |
| **Odbiornik** | Partner hosta określony w obszarze **Ustawienia odbierania** lub partner gościa określony w polu **Wyślij ustawienia** dla umowy X12 |
| **Aplikacja logiki** | Aplikacja logiki, w której są konfigurowane akcje X12 |
| **Stan** | Stan komunikatu X12 <br>Powodzenie = odebrano lub wysłano prawidłowy komunikat X12. Nie skonfigurowano żadnego potwierdzenia funkcjonalności. <br>Powodzenie = odebrano lub wysłano prawidłowy komunikat X12. Zostanie skonfigurowana i odebrana potwierdzenie funkcjonalne lub zostanie wysłane potwierdzenie funkcjonalne. <br>Niepowodzenie = odebrano lub wysłano nieprawidłowy komunikat X12. <br>Oczekiwanie: Odebrano lub wysłano prawidłowy komunikat X12. Zostanie skonfigurowane potwierdzenie funkcjonalne i oczekiwana jest potwierdzenie funkcjonalne. |
| **KOMUNIKATY** | Stan potwierdzenia funkcjonalnego (997) <br>Zaakceptowano = odebrano lub wysłano pozytywne potwierdzenie funkcjonalne. <br>Odrzucono = odebrano lub wysłano negatywną funkcję potwierdzenia funkcjonalności. <br>Oczekiwanie = oczekiwano funkcji ACK, ale nie została ona odebrana. <br>Oczekiwanie = Wygenerowano funkcję ACK, ale nie można wysłać jej do partnera. <br>Niewymagane = nie jest skonfigurowane potwierdzenie funkcjonalne. |
| **Kierunek** | Kierunek komunikatu X12 |
| **Identyfikator śledzenia** | Identyfikator, który jest skorelowany ze wszystkimi wyzwalaczami i akcjami w aplikacji logiki |
| **Typ komunikatu** | Typ komunikatu EDI X12 |
| **ICN** | Numer kontrolny wymiany komunikatu X12 |
| **TSCN** | Numer kontrolny zestawu transakcji dla komunikatu X12 |
| **Znacznik czasu** | Godzina przetworzenia komunikatu przez akcję X12 |
|||

<!--
<a name="x12-folder-file-names"></a>

### X12 name formats for downloaded message files

Here are the name formats for each downloaded X12 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_X12\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_outputs.txt |
|||
-->

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>Opisy właściwości komunikatów EDIFACT

Poniżej przedstawiono opisy właściwości dla każdego komunikatu EDIFACT.

| Właściwość | Opis |
|----------|-------------|
| **Nadawca** | Partner gościa określony w **ustawieniach odbierania** lub partner hosta określony w polu **Wyślij ustawienia** dla umowy EDIFACT |
| **Odbiornik** | Partner hosta określony w obszarze **Ustawienia odbierania** lub partner gościa określony w polu **Wyślij ustawienia** dla umowy EDIFACT |
| **Aplikacja logiki** | Aplikacja logiki, w której są konfigurowane akcje EDIFACT |
| **Stan** | Stan komunikatu EDIFACT <br>Powodzenie = odebrano lub wysłano prawidłowy komunikat EDIFACT. Nie skonfigurowano żadnego potwierdzenia funkcjonalności. <br>Powodzenie = odebrano lub wysłano prawidłowy komunikat EDIFACT. Zostanie skonfigurowana i odebrana potwierdzenie funkcjonalne lub zostanie wysłane potwierdzenie funkcjonalne. <br>Niepowodzenie = odebrano lub wysłano nieprawidłowy komunikat EDIFACT <br>Oczekiwanie: Odebrano lub wysłano prawidłowy komunikat EDIFACT. Zostanie skonfigurowane potwierdzenie funkcjonalne i oczekiwana jest potwierdzenie funkcjonalne. |
| **KOMUNIKATY** | Stan potwierdzenia funkcjonalności (CONTRL) <br>Zaakceptowano = odebrano lub wysłano pozytywne potwierdzenie funkcjonalne. <br>Odrzucono = odebrano lub wysłano negatywną funkcję potwierdzenia funkcjonalności. <br>Oczekiwanie = oczekiwano funkcji ACK, ale nie została ona odebrana. <br>Oczekiwanie = Wygenerowano funkcję ACK, ale nie można wysłać jej do partnera. <br>Niewymagane = nie jest skonfigurowane potwierdzenie funkcjonalne. |
| **Kierunek** | Kierunek komunikatu EDIFACT |
| **Identyfikator śledzenia** | Identyfikator, który jest skorelowany ze wszystkimi wyzwalaczami i akcjami w aplikacji logiki |
| **Typ komunikatu** | Typ komunikatu EDIFACT |
| **ICN** | Numer kontrolny wymiany komunikatu EDIFACT |
| **TSCN** | Numer kontrolny zestawu transakcji dla komunikatu EDIFACT |
| **Znacznik czasu** | Godzina przetworzenia komunikatu przez akcję EDIFACT |
|||

<!--
<a name="edifact-folder-file-names"></a>

### EDIFACT name formats for downloaded message files

Here are the name formats for each downloaded EDIFACT message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_outputs.txt |
|||
-->

## <a name="next-steps"></a>Następne kroki

* [Tworzenie zapytań monitorowania i śledzenia](../logic-apps/create-monitoring-tracking-queries.md)
