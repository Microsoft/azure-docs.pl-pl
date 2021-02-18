---
title: Automatyzacja procesów Application Insights platformy Azure przy użyciu Logic Apps
description: Dowiedz się, jak szybko zautomatyzować powtarzalne procesy przez dodanie łącznika Application Insights do aplikacji logiki.
ms.topic: conceptual
ms.date: 03/11/2019
ms.openlocfilehash: d7ff75be3cb847235405a740df4a20803cdc87b3
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589916"
---
# <a name="automate-application-insights-processes-by-using-logic-apps"></a>Automatyzacja procesów Application Insights przy użyciu Logic Apps

Czy na danych telemetrycznych można wielokrotnie uruchamiać te same zapytania, aby sprawdzić, czy usługa działa prawidłowo? Czy chcesz zautomatyzować te zapytania do znajdowania trendów i anomalii, a następnie utworzyć własne przepływy pracy? Łącznik usługi Azure Application Insights dla Logic Apps jest odpowiednim narzędziem do tego celu.

> [!NOTE]
> Łącznik usługi Azure Application Insights został zastąpiony [łącznikiem Azure monitor](../logs/logicapp-flow-connector.md) , który jest zintegrowany z Azure Active Directory zamiast wymagać klucza interfejsu API, a także umożliwia pobieranie danych z obszaru roboczego log Analytics.

Dzięki tej integracji można zautomatyzować wiele procesów bez konieczności pisania jednego wiersza kodu. Można utworzyć aplikację logiki za pomocą łącznika Application Insights, aby szybko zautomatyzować każdy proces Application Insights. 

Można również dodać dodatkowe akcje. Funkcja Logic Apps Azure App Service zapewnia setki dostępnych akcji. Na przykład za pomocą aplikacji logiki można automatycznie wysłać powiadomienie e-mail lub utworzyć usterkę w usłudze Azure DevOps. Możesz również użyć jednego z wielu dostępnych [szablonów](../../logic-apps/logic-apps-create-logic-apps-from-templates.md) , aby przyspieszyć proces tworzenia aplikacji logiki. 

## <a name="create-a-logic-app-for-application-insights"></a>Tworzenie aplikacji logiki dla Application Insights

W tym samouczku dowiesz się, jak utworzyć aplikację logiki, która używa algorytmu autoklastrów analizy do grupowania atrybutów w danych aplikacji sieci Web. Przepływ automatycznie wysyła wyniki za pośrednictwem poczty e-mail, tylko jeden przykład sposobu użycia Application Insights analiz i Logic Apps. 

### <a name="step-1-create-a-logic-app"></a>Krok 1. Tworzenie aplikacji logiki
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Kliknij pozycję **Utwórz zasób**, wybierz pozycję **Sieć Web + aplikacje mobilne**, a następnie wybierz pozycję **aplikacja logiki**.

    ![Nowe okno aplikacji logiki](./media/automate-with-logic-apps/1createlogicapp.png)

### <a name="step-2-create-a-trigger-for-your-logic-app"></a>Krok 2. Tworzenie wyzwalacza dla aplikacji logiki
1. W oknie **Projektant aplikacji logiki** w obszarze **Rozpocznij ze wspólnym wyzwalaczem** wybierz pozycję **cykl**.

    ![Okno projektanta aplikacji logiki](./media/automate-with-logic-apps/2logicappdesigner.png)

1. W polu  **Interwał** wpisz **1** , a następnie pole **częstotliwość** , a następnie wybierz pozycję **dzień**.

    ![Okno "cykl" projektanta aplikacji logiki](./media/automate-with-logic-apps/3recurrence.png)

### <a name="step-3-add-an-application-insights-action"></a>Krok 3. Dodawanie akcji Application Insights
1. Kliknij pozycję **nowy krok**.

1. W polu wyszukiwania **Wybierz akcję** wpisz **Application Insights platformy Azure**.

1. W obszarze **Akcje** kliknij pozycję **Azure Application Insights-Wizualizuj zapytanie analityczne**.

    ![Okno projektanta aplikacji logiki "Wybieranie akcji"](./media/automate-with-logic-apps/4visualize.png)

### <a name="step-4-connect-to-an-application-insights-resource"></a>Krok 4. Nawiązywanie połączenia z zasobem Application Insights

Aby ukończyć ten krok, potrzebny jest identyfikator aplikacji i klucz interfejsu API dla zasobu. Można je pobrać z Azure Portal, jak pokazano na poniższym diagramie:

![Zrzut ekranu przedstawia stronę dostępu do interfejsu API w Azure Portal z wybranym przyciskiem Utwórz klucz interfejsu API.](./media/automate-with-logic-apps/5apiaccess.png)

![Identyfikator aplikacji w Azure Portal](./media/automate-with-logic-apps/6apikey.png)

Podaj nazwę połączenia, identyfikator aplikacji i klucz interfejsu API.

![Okno połączenia przepływu projektanta aplikacji logiki](./media/automate-with-logic-apps/7connection.png)

### <a name="step-5-specify-the-analytics-query-and-chart-type"></a>Krok 5. Określanie typu zapytania i wykresu analitycznego
W poniższym przykładzie zapytanie wybiera żądania zakończone niepowodzeniem w ciągu ostatniego dnia i skorelowane z wyjątkami, które wystąpiły w ramach operacji. Analiza skorelowana z niepomyślnymi żądaniami na podstawie identyfikatora operation_Id. Kwerenda następnie segmentuje wyniki przy użyciu algorytmu Autocluster. 

Podczas tworzenia własnych zapytań Sprawdź, czy działają prawidłowo w analizie, zanim dodasz ją do przepływu.

1. W polu **zapytania** Dodaj następujące zapytanie analityczne:

    ```
    requests
    | where timestamp > ago(1d)
    | where success == "False"
    | project name, operation_Id
    | join ( exceptions
        | project problemId, outerMessage, operation_Id
    ) on operation_Id
    | evaluate autocluster()
    ```

1. W polu **Typ wykresu** wybierz opcję **tabela HTML**.

    ![Okno konfiguracji zapytania analitycznego](./media/automate-with-logic-apps/8query.png)

### <a name="step-6-configure-the-logic-app-to-send-email"></a>Krok 6. Konfigurowanie aplikacji logiki do wysyłania wiadomości e-mail

1. Kliknij pozycję **nowy krok**.

1. W polu wyszukiwania wpisz **Office 365 Outlook**.

1. Kliknij pozycję **Office 365 Outlook — Wyślij wiadomość e-mail**.

    ![Wybór pakietu Office 365 dla programu Outlook](./media/automate-with-logic-apps/9sendemail.png)

1. W oknie **wysyłanie wiadomości e-mail** wykonaj następujące czynności:

   a. Wpisz adres e-mail adresata.

   b. Wpisz temat wiadomości e-mail.

   c. Kliknij w dowolnym miejscu w polu **treść** , a następnie w menu zawartość dynamiczna, które jest otwierane po prawej stronie, wybierz pozycję **treść**.
    
   d. Kliknij listę rozwijaną **Dodaj nowy parametr** , a następnie wybierz pozycję załączniki i jest to kod HTML.

      ![Zrzut ekranu przedstawia okno Wyślij wiadomość e-mail z wyróżnionym polem Body i menu zawartości dynamicznej z wyróżnioną treścią po prawej stronie.](./media/automate-with-logic-apps/10emailbody.png)

      ![Konfiguracja programu Outlook pakietu Office 365](./media/automate-with-logic-apps/11emailparameter.png)

1. W menu zawartość dynamiczna wykonaj następujące czynności:

    a. Wybierz pozycję **nazwa załącznika**.

    b. Wybierz **zawartość załącznika**.
    
    c. W polu **kod HTML** wybierz pozycję **tak**.

      ![Ekran konfiguracji poczty e-mail pakietu Office 365](./media/automate-with-logic-apps/12emailattachment.png)

### <a name="step-7-save-and-test-your-logic-app"></a>Krok 7. Zapisywanie i testowanie aplikacji logiki
* Kliknij przycisk **Zapisz**, aby zapisać zmiany.

Możesz poczekać na uruchomienie aplikacji logiki przez wyzwalacz lub uruchomić aplikację logiki natychmiast, wybierając pozycję **Uruchom**.

![Ekran tworzenia aplikacji logiki](./media/automate-with-logic-apps/13save.png)

Po uruchomieniu aplikacji logiki adresaci określeni na liście wiadomości e-mail otrzymają wiadomość e-mail, która wygląda następująco:

![Wiadomość e-mail dotycząca aplikacji logiki](./media/automate-with-logic-apps/flow9.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat tworzenia [zapytań analitycznych](../logs/get-started-queries.md).
- Dowiedz się więcej na temat usługi [Logic Apps](../../logic-apps/logic-apps-overview.md).



<!--Link references-->

