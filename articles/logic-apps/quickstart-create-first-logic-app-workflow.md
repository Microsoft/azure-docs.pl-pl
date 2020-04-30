---
title: Tworzenie pierwszego zautomatyzowanego przepływu pracy
description: Szybki Start — Tworzenie pierwszego zautomatyzowanego przepływu pracy przy użyciu Azure Logic Apps na potrzeby rozwiązań integracji systemu i integracji aplikacji w przedsiębiorstwie (EAI)
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/20/2019
ms.openlocfilehash: 3087b964ff5f9754d6552fc95625541ce94a6535
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82147995"
---
# <a name="quickstart-create-your-first-workflow-by-using-azure-logic-apps---azure-portal"></a>Szybki Start: Tworzenie pierwszego przepływu pracy przy użyciu Azure Logic Apps-Azure Portal

W tym przewodniku szybki start przedstawiono podstawowe ogólne pojęcia związane z tworzeniem pierwszego przepływu pracy przy użyciu [Azure Logic Apps](../logic-apps/logic-apps-overview.md), takich jak tworzenie pustej aplikacji logiki, Dodawanie wyzwalacza i akcji, a następnie testowanie aplikacji logiki. W tym przewodniku szybki start utworzysz aplikację logiki, która regularnie sprawdza kanał informacyjny RSS witryny sieci Web pod kątem nowych elementów. Jeśli istnieją nowe elementy, aplikacja logiki wysyła wiadomość e-mail dotyczącą każdego elementu. Po ukończeniu aplikacja logiki będzie ogólnie wyglądać jak ten przepływ pracy:

![Przykładowy przepływ pracy aplikacji logiki wysokiego poziomu](./media/quickstart-create-first-logic-app-workflow/quickstart-workflow-overview.png)

W tym scenariuszu potrzebna jest subskrypcja platformy Azure lub można [utworzyć bezpłatne konto platformy Azure](https://azure.microsoft.com/free/)— konto e-mail z usługi obsługiwanej przez Azure Logic Apps, na przykład Office 365 Outlook, Outlook.com lub gmail. W przypadku innych obsługiwanych usług poczty e-mail [Przejrzyj listę łączników tutaj](https://docs.microsoft.com/connectors/). W tym przykładzie aplikacja logiki używa konta programu Outlook w programie Office 365. Jeśli używasz innej usługi poczty e-mail, ogólne kroki są takie same, ale interfejs użytkownika może się nieco różnić.

> [!IMPORTANT]
> Jeśli chcesz korzystać z łącznika usługi Gmail, tylko konta firmowe z zestawu G-Suite mogą używać tego łącznika bez ograniczeń w usłudze Logic Apps. Jeśli masz konto użytkownika usługi Gmail, możesz użyć tego łącznika z tylko określonymi usługami zatwierdzonymi przez firmę Google lub możesz [utworzyć aplikację kliencką Google, która będzie używana do uwierzytelniania za pomocą łącznika usługi Gmail](https://docs.microsoft.com/connectors/gmail/#authentication-and-bring-your-own-application). Aby uzyskać więcej informacji, zobacz [zabezpieczenia danych i zasady ochrony prywatności dla łączników Google w Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta Azure.

## <a name="create-your-logic-app"></a>Tworzenie aplikacji logiki

1. Na stronie głównej platformy Azure w polu wyszukiwania Znajdź i wybierz **Logic Apps**.

   ![Znajdź i wybierz pozycję "Logic Apps"](./media/quickstart-create-first-logic-app-workflow/find-select-logic-apps.png)

1. Na stronie **Logic Apps** wybierz pozycję **Dodaj**.

   ![Dodaj nową aplikację logiki](./media/quickstart-create-first-logic-app-workflow/add-new-logic-app.png)

1. W okienku **aplikacja logiki** podaj szczegółowe informacje o aplikacji logiki, jak pokazano poniżej. Po zakończeniu wybierz pozycję **Utwórz**.

   ![Podaj szczegóły nowej aplikacji logiki](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Nazwa** | <*Logic-App-Name*> | Nazwa aplikacji logiki, która może zawierać tylko litery, cyfry,`-`łączniki (), podkreślenia (`_`), nawiasy (`(`, `)`) i kropki (`.`). W tym przykładzie zastosowano "My-First-Logic-App". |
   | **Subskrypcja** | <*Azure — nazwa subskrypcji*> | Nazwa subskrypcji platformy Azure |
   | **Grupa zasobów** | <*Azure-Resource-Group-Name*> | Nazwa [grupy zasobów platformy Azure](../azure-resource-manager/management/overview.md) używanej do organizowania powiązanych zasobów. W tym przykładzie jest stosowana wartość "My-First-LA-RG". |
   | **Lokalizacja** | <*Platforma Azure — region*> | Region, w którym są przechowywane informacje o aplikacji logiki. W tym przykładzie zastosowano "zachodnie stany USA". |
   | **Log Analytics** | Wyłączone | Ustawienie **Wyłączone** umożliwia rejestrowanie w celach diagnostycznych. |
   ||||

1. Po wdrożeniu aplikacji przez platformę Azure na pasku narzędzi platformy Azure wybierz pozycję **powiadomienia** > **Przejdź do pozycji zasób** dla wdrożonej aplikacji logiki.

   ![Przejdź do nowo utworzonego zasobu aplikacji logiki](./media/quickstart-create-first-logic-app-workflow/go-to-new-logic-app-resource.png)

   Możesz też znaleźć i wybrać aplikację logiki, wpisując nazwę w polu wyszukiwania.

   Zostanie otwarty Projektant aplikacji usługi Logic Apps wyświetlający stronę z wprowadzającym wideo i najczęściej używanymi wyzwalaczami. W obszarze **Szablony** wybierz pozycję **Pusta aplikacja logiki**.

   ![Wybieranie pustego szablonu dla aplikacji logiki](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Następnie dodaj [wyzwalacz](../logic-apps/logic-apps-overview.md#logic-app-concepts), który będzie aktywowany po pojawieniu się nowego elementu w kanale informacyjnym RSS. Każda aplikacja logiki musi rozpoczynać się od wyzwalacza, który jest aktywowany w momencie wystąpienia konkretnego zdarzenia lub spełnienia określonego warunku. Za każdym razem, gdy wyzwala wyzwalacz, aparat Azure Logic Apps tworzy wystąpienie aplikacji logiki, które uruchamia i uruchamia przepływ pracy.

<a name="add-rss-trigger"></a>

## <a name="add-the-rss-trigger"></a>Dodawanie wyzwalacza RSS

1. W **Projektancie aplikacji logiki**, w polu wyszukiwania zaznacz **wszystko**.

1. W polu wyszukiwania wpisz ciąg `rss` , aby znaleźć łącznik RSS. Z listy Wyzwalacze wybierz wyzwalacz, **gdy element kanału informacyjnego jest publikowany** .

   ![Wybierz wyzwalacz "gdy element kanału informacyjnego jest opublikowany"](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-new-feed-item.png)

1. Podaj te informacje dla wyzwalacza, jak pokazano i opisano tutaj:

   ![Konfigurowanie kanału informacyjnego RSS, częstotliwości i interwału dla wyzwalacza](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-settings.png)

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Adres URL źródła danych RSS** | `http://feeds.reuters.com/reuters/topNews` | Link do źródła danych RSS, które chcesz monitorować |
   | **Dat** | 1 | Liczba interwałów do odczekania między sprawdzaniami |
   | **Częstotliwość** | Minuta | Jednostka czasu dla każdego interwału między sprawdzaniami  |
   ||||

   Interwał i częstotliwość określają harmonogram wyzwalacza aplikacji logiki. Ta aplikacja logiki sprawdza kanał informacyjny co minutę.

1. Aby teraz zwinąć szczegóły wyzwalacza, kliknij wewnątrz paska tytułu wyzwalacza.

   ![Zwiń kształt aplikacji logiki, aby ukryć szczegóły](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. Zapisz aplikację logiki. Na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Twoja aplikacja logiki jest już aktywna, ale nie robi niczego poza sprawdzaniem źródła danych RSS. Dodaj więc akcję reagującą na aktywowanie wyzwalacza.

## <a name="add-the-send-email-action"></a>Dodaj akcję "Wyślij wiadomość e-mail"

Teraz Dodaj [akcję](../logic-apps/logic-apps-overview.md#logic-app-concepts) , która wysyła wiadomość e-mail po pojawieniu się nowego elementu w kanale informacyjnym RSS.

1. W obszarze wyzwalacz **po opublikowaniu elementu kanału informacyjnego** wybierz pozycję **nowy krok**.

   ![W obszarze wyzwalacza wybierz pozycję "nowy krok".](./media/quickstart-create-first-logic-app-workflow/add-new-step-under-trigger.png)

1. W obszarze **Wybierz akcję** i pole wyszukiwania wybierz pozycję **wszystkie**.

1. W polu wyszukiwania wpisz ciąg `send an email` , aby znaleźć łączniki, które oferują tę akcję. Z listy Akcje wybierz akcję "Wyślij wiadomość e-mail" dla usługi poczty e-mail, której chcesz użyć. W tym przykładzie jest używany łącznik Office 365 Outlook, który ma akcję **Wyślij wiadomość e-mail** .

   ![Wybierz akcję "Wyślij wiadomość e-mail" dla pakietu Office 365 Outlook](./media/quickstart-create-first-logic-app-workflow/add-action-send-email.png)

   Aby na liście akcji wyświetlić akcje tylko dla konkretnej aplikacji lub usługi, możesz najpierw wybrać tę aplikację lub usługę:

   * Dla kont służbowych platformy Azure wybierz pozycję Office 365 Outlook.
   * Dla osobistych kont Microsoft wybierz pozycję Outlook.com.

1. Jeśli wybrany łącznik poczty e-mail będzie monitował o uwierzytelnienie tożsamości, wykonaj ten krok teraz, aby utworzyć połączenie między aplikacją logiki a usługą poczty e-mail.

   > [!NOTE]
   > W tym konkretnym przykładzie ręcznie uwierzytelniasz swoją tożsamość. Jednak łączniki, które wymagają uwierzytelniania, różnią się w typach uwierzytelniania, które są przez nie obsługiwane. Dostępne są również opcje konfigurowania sposobu obsługi uwierzytelniania. Na przykład w przypadku używania szablonów Azure Resource Manager do wdrożenia można Sparametryzuj i poprawić zabezpieczenia danych wejściowych, które mają być często lub łatwo zmieniane, takie jak informacje o połączeniu. Więcej informacji można znaleźć w następujących tematach:
   >
   > * [Parametry szablonu dla wdrożenia](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)
   > * [Autoryzuj połączenia OAuth](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)
   > * [Uwierzytelnianie dostępu za pomocą tożsamości zarządzanych](../logic-apps/create-managed-service-identity.md)
   > * [Uwierzytelnianie połączeń dla wdrożenia aplikacji logiki](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)

1. W obszarze akcji **Wyślij wiadomość e-mail** podaj dane, które powinny znaleźć się w wiadomości e-mail.

   1. W polu **Do** wprowadź adres e-mail adresata. Do celów testowych możesz użyć swojego adresu e-mail.

      Na razie zignoruj wyświetloną listę **Dodaj zawartość dynamiczną**. Ta lista pojawia się po kliknięciu wewnątrz niektórych pól edycji. Znajdują się na niej wszelkie dostępne parametry z poprzedniego kroku, które można dołączyć jako dane wejściowe w przepływie pracy.

   1. W polu **Temat** wprowadź następujący tekst ze spacją na końcu: `New RSS item: `

      ![We właściwości "podmiot" Wprowadź temat wiadomości e-mail.](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject.png)

   1. Z listy **Dodaj zawartość dynamiczną** wybierz pozycję **Tytuł źródła danych**, aby uwzględnić tytuł elementu kanału RSS.

      ![Z listy zawartości dynamicznej wybierz właściwość "tytuł źródła danych"](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-subject-dynamic-content.png)

      Gdy wszystko będzie gotowe, temat wiadomości e-mail będzie wyglądać następująco:

      ![Zakończono przykład tematu wiadomości e-mail dla dodanego tytułu kanału informacyjnego](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-feed-title.png)

      Jeśli w projektancie pojawi się pętla „For each”, oznacza to, że wybrano token dla tablicy, na przykład token **categories-Item**. W przypadku takiego typu tokenów projektant automatycznie dodaje pętlę do akcji, która odwołuje się do tego tokenu. W ten sposób Twoja aplikacja logiki wykonuje tę samą akcję dla każdego elementu tablicy. Aby usunąć pętlę, wybierz **wielokropek** (**...**) na pasku tytułu pętli, a następnie wybierz pozycję **Usuń**.

   1. W polu **Treść** wprowadź następujący tekst i wybierz widoczne tokeny dla treści wiadomości e-mail. Aby dodać puste wiersze w polu edycji, naciśnij klawisze Shift + Enter.

      ![Wybierz właściwości zawartości wiadomości e-mail](./media/quickstart-create-first-logic-app-workflow/add-action-send-email-body.png)

      | Właściwość | Opis |
      |----------|-------------|
      | **Tytuł źródła danych** | Tytuł elementu |
      | **Data opublikowania kanału informacyjnego** | Data i godzina opublikowania elementu |
      | **Link podstawowego źródła danych** | Adres URL elementu |
      |||

1. Zapisz aplikację logiki.

Następnie przeprowadź testowanie aplikacji logiki.

## <a name="run-your-logic-app"></a>Uruchamianie aplikacji logiki

Aby ręcznie uruchomić aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom**. Możesz też zaczekać, aż aplikacja logiki sprawdzi źródło danych RSS zgodnie z określonym harmonogramem (co minutę). Jeśli źródło danych RSS będzie zawierać nowe elementy, aplikacja logiki wyśle wiadomość e-mail dla każdego nowego elementu. W przeciwnym razie aplikacja logiki będzie czekać z ponownym sprawdzeniem do następnego interwału. Jeśli nie dostaniesz żadnych wiadomości e-mail, sprawdź folder wiadomości-śmieci.

Oto przykładowa wiadomość e-mail wysyłana przez tę aplikację logiki.

![Przykładowa wiadomość e-mail wysłana po wyświetleniu nowego elementu kanału informacyjnego RSS](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Technicznie, gdy wyzwalacz sprawdza kanał informacyjny RSS i odnajduje nowe elementy, uruchamiany jest wyzwalacz, a aparat Azure Logic Apps tworzy wystąpienie przepływu pracy aplikacji logiki, który uruchamia akcje w przepływie pracy. Jeśli wyzwalacz nie znajdzie nowych elementów, to nie zostanie wyzwolony i pominie tworzenie wystąpienia przepływu pracy.

Gratulacje, udało Ci się pomyślnie skompilować i uruchomić swoją pierwszą aplikację logiki przy użyciu Azure Portal.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy ten przykład nie będzie już potrzebny, usuń grupę zasobów zawierającą aplikację logiki i powiązane zasoby.

1. W głównym menu platformy Azure wybierz pozycję **grupy zasobów**, a następnie wybierz grupę zasobów aplikacji logiki. W okienku **Przegląd** wybierz pozycję **Usuń grupę zasobów**.

   ![Znajdowanie, wybieranie i usuwanie grupy zasobów](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. Gdy zostanie wyświetlone okienko potwierdzenia, wprowadź nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.

   ![Aby potwierdzić usunięcie, wybierz pozycję "Usuń"](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

> [!NOTE]
> Po usunięciu aplikacji logiki nie są tworzone wystąpienia nowych przebiegów. Wszystkie trwające i oczekujące przebiegi zostają anulowane. Anulowanie kilku tysięcy przebiegów może zająć dużo czasu.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono pierwszą aplikację logiki, która sprawdza aktualizacje kanału informacyjnego RSS według określonego harmonogramu (co minutę) i jeśli istnieją aktualizacje, wykonuje akcję (wysyła wiadomość e-mail). Aby dowiedzieć się więcej, kontynuuj pracę z tym samouczkiem nad tworzeniem bardziej zaawansowanych przepływów pracy opartych na harmonogramie:

> [!div class="nextstepaction"]
> [Sprawdzanie ruchu za pomocą aplikacji logiki opartej na harmonogramie](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)
