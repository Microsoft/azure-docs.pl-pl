---
title: Szybki Start — Tworzenie pierwszego Logic Appsego przepływu pracy — Azure Portal
description: Utwórz pierwszy zautomatyzowany przepływ pracy Logic Apps w Azure Portal za pomocą tego przewodnika Szybki Start. Poznaj podstawy rozwiązań integracji systemu i integracji aplikacji przedsiębiorstwa (EAI) w Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/30/2020
ms.openlocfilehash: d90b9e38158d951990fffc21a43317c688da12c9
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99052046"
---
# <a name="quickstart-create-your-first-logic-apps-workflow---azure-portal"></a>Szybki Start: Tworzenie pierwszego Logic Appsego przepływu pracy — Azure Portal

Ten przewodnik Szybki Start wyjaśnia, jak utworzyć swój pierwszy przepływ pracy w [Azure Logic Apps](logic-apps-overview.md) przez [Azure Portal](https://portal.azure.com). W tym przewodniku wprowadzającego objaśniono również podstawowe pojęcia dotyczące usługi Logic Apps, w tym informacje na temat tworzenia nowej aplikacji logiki, dodawania wyzwalacza i akcji do aplikacji logiki oraz testowania aplikacji logiki. Postępuj zgodnie z tym przewodnikiem Szybki Start, aby utworzyć przykładową aplikację logiki, która regularnie sprawdza kanał informacyjny RSS i wysyła powiadomienie e-mail o nowych elementach. Poniższy zrzut ekranu przedstawia przepływ pracy wysokiego poziomu tego przykładowej aplikacji logiki:

![Zrzut ekranu programu Logic Apps Designer z przykładową aplikacją logiki, w której wyzwalacz jest kanałem informacyjnym RSS, a akcja wysyła wiadomość e-mail.](./media/quickstart-create-first-logic-app-workflow/quickstart-workflow-overview.png)

<a name="prerequisites"></a>

Jeśli chcesz dowiedzieć się, jak utworzyć pierwszą aplikację logiki i zarządzać nią za poorednictwem innych interfejsów i aplikacji, zobacz następujące informacje Logic Apps przewodników szybki start: 

* [Tworzenie aplikacji logiki i zarządzanie nimi przy użyciu interfejsu Command-Line platformy Azure (interfejs wiersza polecenia platformy Azure)](quickstart-logic-apps-azure-cli.md)
* [Tworzenie aplikacji logiki i zarządzanie nimi w Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md)
* [Tworzenie aplikacji logiki i zarządzanie nimi w programie Visual Studio](quickstart-create-logic-apps-with-visual-studio.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Konto i subskrypcja platformy Azure. Jeśli go nie masz, [zarejestruj się, aby skorzystać z bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Konto e-mail z usługi obsługiwanej przez Logic Apps (na przykład Office 365 Outlook lub Outlook.com). W przypadku innych obsługiwanych dostawców poczty e-mail [Przejrzyj listę łączników](/connectors/).

    > [!IMPORTANT]
    > Jeśli używasz [łącznika usługi Gmail](/connectors/gmail/), pamiętaj, że tylko konta usługi G Suite mogą używać tego łącznika bez ograniczeń w Logic Apps. Jeśli masz konto odbiorcy usługi Gmail, możesz używać tego łącznika tylko z określonymi usługami zatwierdzonymi przez firmę Google, chyba że [utworzysz aplikację kliencką Google, która będzie używana do uwierzytelniania za pomocą łącznika usługi Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). Aby uzyskać więcej informacji, zobacz [zabezpieczenia danych i zasady ochrony prywatności dla łączników Google w Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Jeśli aplikacja logiki musi komunikować się przez zaporę, która ogranicza ruch do określonych adresów IP, zapora musi zezwolić na dostęp *zarówno* do [przychodzącego](logic-apps-limits-and-config.md#inbound) , jak i [wychodzącego](logic-apps-limits-and-config.md#outbound) adresu IP używanego przez usługę Logic Apps lub środowisko uruchomieniowe w regionie platformy Azure, w którym znajduje się aplikacja logiki. Jeśli aplikacja logiki używa również łączników [zarządzanych](../connectors/apis-list.md#managed-api-connectors), takich jak łącznik usługi Office 365 Outlook lub łącznik SQL, lub używa [łączników niestandardowych](/connectors/custom-connectors/), zapora musi również zezwolić na dostęp *wszystkich* [wychodzących adresów IP łącznika zarządzanego](logic-apps-limits-and-config.md#outbound) w regionie platformy Azure aplikacji logiki.

<a name="create-logic-app"></a>

## <a name="create-your-logic-app"></a>Tworzenie aplikacji logiki

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta Azure.

1. W polu wyszukiwania Azure Portal wprowadź wartość `logic apps` , a następnie wybierz pozycję **Logic Apps**.

   ![Zrzut ekranu przedstawiający Azure Portal, który zawiera pole wyszukiwania z "usługą Logic Apps" jako termin wyszukiwania i "Logic Apps" jako wybrany wynik wyszukiwania.](./media/quickstart-create-first-logic-app-workflow/find-select-logic-apps.png)

1. Na stronie **Logic Apps** wybierz pozycję **Dodaj**.

   ![Zrzut ekranu przedstawiający stronę usługi Logic Apps w programie Azure Portal z listą aplikacji logiki i wybranym przyciskiem "Dodaj".](./media/quickstart-create-first-logic-app-workflow/add-new-logic-app.png)

1. W okienku **aplikacja logiki** Podaj podstawowe informacje i ustawienia dla aplikacji logiki. Utwórz nową [grupę zasobów](../azure-resource-manager/management/overview.md#terminology) na potrzeby tej przykładowej aplikacji logiki.

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Nazwa** | <*Logic-App-Name*> | Nazwa aplikacji logiki, która musi być unikatowa w różnych regionach. Nazwa może zawierać tylko litery, cyfry, łączniki ( `-` ), podkreślenia ( `_` ), nawiasy ( `(` , `)` ) i kropki ( `.` ). W tym przykładzie zastosowano "My-First-Logic-App". |
   | **Subskrypcja** | <*Azure — nazwa subskrypcji*> | Nazwa subskrypcji platformy Azure. |
   | **Grupa zasobów** | <*Azure-Resource-Group-Name*> | Nazwa [grupy zasobów platformy Azure](../azure-resource-manager/management/overview.md#terminology) , w której tworzysz aplikację logiki. Nazwa grupy zasobów musi być unikatowa w różnych regionach. W tym przykładzie jest stosowana wartość "My-First-LA-RG". |
   | **Lokalizacja** | <*Platforma Azure — region*> | Region świadczenia usługi Azure, w którym są przechowywane informacje o aplikacji logiki. W tym przykładzie zastosowano "zachodnie stany USA". |
   | **Log Analytics** | Wyłączone | Ustawienie rejestrowania diagnostycznego, które jest domyślnie **wyłączone** . Na potrzeby tego przykładu pozostaw ustawienie **wyłączone** . |
   ||||

   ![Zrzut ekranu przedstawiający stronę tworzenia Logic Apps pokazującą okienko ze szczegółowymi informacjami o nowej aplikacji logiki.](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

1. Gdy wszystko będzie gotowe, wybierz pozycję **Przegląd + Utwórz**. Potwierdź podane szczegóły, a następnie wybierz pozycję **Utwórz**.

1. Po pomyślnym wdrożeniu aplikacji przez platformę Azure wybierz pozycję **Przejdź do zasobu**. Możesz też znaleźć i wybrać aplikację logiki, wpisując nazwę w polu wyszukiwania.

   ![Zrzut ekranu przedstawiający stronę wdrażanie zasobów z wybranym przyciskiem "przejdź do zasobu".](./media/quickstart-create-first-logic-app-workflow/go-to-new-logic-app-resource.png)

   Zostanie otwarty Projektant aplikacji usługi Logic Apps wyświetlający stronę z wprowadzającym wideo i najczęściej używanymi wyzwalaczami. W obszarze **Szablony** wybierz pozycję **Pusta aplikacja logiki**.

   ![Zrzut ekranu przedstawiający projektanta Logic Apps, wyświetlanie galerii szablonów i wybranego szablonu "pusta aplikacja logiki".](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Następnie [Dodaj wyzwalacz do aplikacji logiki](#add-rss-trigger).

<a name="add-rss-trigger"></a>

## <a name="add-the-rss-trigger"></a>Dodawanie wyzwalacza RSS

Każda aplikacja logiki musi rozpoczynać się od [wyzwalacza](../logic-apps/logic-apps-overview.md#how-do-logic-apps-work), który jest uruchamiany w przypadku wystąpienia konkretnego zdarzenia lub spełnienia określonego warunku. Za każdym razem, gdy wyzwalacz znajdzie nowe elementy, wyzwalane i aparat Logic Apps tworzy wystąpienie aplikacji logiki, które uruchamia i uruchamia przepływ pracy. Jeśli wyzwalacz nie znajdzie nowych elementów, wyzwalacz nie uruchomi się i nie utworzy wystąpienia ani nie uruchomi przepływu pracy przy tym sprawdzaniu.

W tym przykładzie przewodnika Szybki Start po [utworzeniu aplikacji logiki](#create-your-logic-app)zostanie dodany wyzwalacz, który wyszukuje nowe elementy w kanale informacyjnym RSS i uruchamiany, gdy pojawią się nowe elementy. Można również tworzyć aplikacje logiki z różnymi typami wyzwalaczy, takimi jak w samouczku do [tworzenia przepływów pracy opartych na zatwierdzaniu automatycznym](tutorial-process-mailing-list-subscriptions-workflow.md).

1. W **Projektancie aplikacji logiki**, w polu wyszukiwania zaznacz **wszystko**.

1. Aby znaleźć łącznik RSS, w polu wyszukiwania wpisz `rss` . Z listy **wyzwalacze** Wybierz wyzwalacz RSS, **gdy zostanie opublikowany element kanału informacyjnego**.

   ![Zrzut ekranu przedstawiający projektanta Logic Apps z "RSS" w polu wyszukiwania i wybranym wyzwalaczem RSS "po opublikowaniu elementu kanału informacyjnego".](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-new-feed-item.png)

1. Podaj adres URL źródła danych RSS dla wyzwalacza. Następnie zdefiniuj harmonogram wyzwalacza, ustawiając interwał i częstotliwość.

   | Właściwość | Wartość | Opis |
   | -------- | ----- | ----------- |
   | **Adres URL źródła danych RSS** | <*RSS-źródło — adres URL*> | Adres URL źródła danych RSS, które chcesz monitorować. W tym przykładzie używamy kanału informacyjnego RSS arkusza ściennego w `https://feeds.a.dj.com/rss/RSSMarketsMain.xml` . Jednak na potrzeby tego przykładu można użyć dowolnego kanału informacyjnego RSS, który nie wymaga autoryzacji HTTP. Wybierz kanał informacyjny RSS, który często publikuje się, aby można było później przetestować aplikację logiki. |
   | **Interwał** | 1 | Liczba interwałów oczekiwania między sprawdzeniami kanału informacyjnego RSS. W tym przykładzie zastosowano 1 minutowe interwały. |
   | **Częstotliwość** | Minuta | Jednostka czasu dla każdego interwału między sprawdzaniem źródła danych RSS. W tym przykładzie zastosowano 1 minutowe interwały. |
   ||||

   ![Zrzut ekranu przedstawiający projektanta Logic Apps z ustawieniami wyzwalacza RSS, w tym adres URL RSS, częstotliwość i interwał.](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-settings.png)

1. Zwiń teraz szczegóły wyzwalacza, klikając wewnątrz jego paska tytułu.

   ![Zrzut ekranu przedstawiający projektanta Logic Apps z zwiniętym kształtem aplikacji logiki.](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. Zapisz aplikację logiki, wybierając pozycję **Zapisz** na pasku narzędzi projektanta.

Twoja aplikacja logiki jest już aktywna, ale nie wykonuje żadnych działań poza sprawdzeniem źródła danych RSS. Następnie [Dodaj akcję](#add-email-action) , aby określić, co się stanie po wyzwoleniu wyzwalacza.

<a name="add-email-action"></a>

## <a name="add-the-send-email-action"></a>Dodaj akcję "Wyślij wiadomość e-mail"

Po [dodaniu wyzwalacza do aplikacji logiki](#add-rss-trigger)należy dodać [akcję](../logic-apps/logic-apps-overview.md#logic-app-concepts) , aby określić odpowiedź, gdy aplikacja logiki sprawdzi kanał informacyjny RSS i pojawi się nowy element. Możesz również tworzyć aplikacje logiki z znacznie bardziej złożonymi akcjami, takimi jak samouczek dotyczący [przetwarzania wiadomości e-mail za pomocą Logic Apps, Azure Functions i usługi Azure Storage](./tutorial-process-email-attachments-workflow.md).

> [!NOTE]
> W tym przykładzie jako usługi poczty e-mail jest stosowany pakiet Office 365 Outlook. Jeśli używasz innej obsługiwanej usługi poczty e-mail w aplikacji logiki, interfejs użytkownika może wyglądać inaczej. Jednak podstawowe koncepcje dotyczące łączenia się z inną usługą poczty e-mail pozostają bez zmian.

1. W obszarze wyzwalacz **po opublikowaniu elementu kanału informacyjnego** wybierz pozycję **nowy krok**.

   ![Zrzut ekranu projektanta Logic Apps, pokazujący przepływ pracy z wybranym przyciskiem "nowy krok".](./media/quickstart-create-first-logic-app-workflow/add-new-step-under-trigger.png)

1. W obszarze **Wybierz akcję** i pole wyszukiwania wybierz pozycję **wszystkie**.

1. W polu wyszukiwania wprowadź, `send an email` Aby można było znaleźć łączniki, które oferują tę akcję. Aby odfiltrować listę akcji do określonej aplikacji lub usługi, możesz najpierw wybrać tę aplikację lub usługę.

   Jeśli na przykład używasz konta służbowego firmy Microsoft i chcesz korzystać z pakietu Office 365 Outlook, wybierz pozycję **office 365 Outlook**. Lub, jeśli używasz osobistego konto Microsoft, możesz wybrać pozycję Outlook.com. Ten przykład kontynuuje działanie z pakietem Office 365 Outlook:

   ![Zrzut ekranu projektanta Logic Apps, pokazujący krok akcji z wybranym łącznikiem poczty e-mail "Office 365 Outlook".](./media/quickstart-create-first-logic-app-workflow/select-connector.png)

   Teraz można łatwiej znaleźć i wybrać akcję, która ma zostać użyta, na przykład `send an email` :

   ![Zrzut ekranu projektanta Logic Apps, pokazujący listę filtrowanych akcji łącznika poczty e-mail "Office 365 Outlook".](./media/quickstart-create-first-logic-app-workflow/filtered-actions-list.png)

1. Jeśli wybrany łącznik poczty e-mail będzie monitował o uwierzytelnienie tożsamości, wykonaj ten krok teraz. Aby ten przykład działał, należy utworzyć połączenie między aplikacją logiki a usługą poczty e-mail. 

    > [!NOTE]
    > Ten przykład pokazuje ręczne uwierzytelnianie łącznika Office 365 Outlook. Inne łączniki mogą jednak obsługiwać różne typy uwierzytelniania.
    > Możesz również obsługiwać uwierzytelnianie dla aplikacji logiki na różne sposoby, w zależności od przypadku użycia. Na przykład w przypadku użycia szablonów Azure Resource Manager do wdrożenia można Sparametryzuj, aby zwiększyć bezpieczeństwo danych wejściowych, które często zmieniają się, na przykład szczegóły połączenia. Więcej informacji można znaleźć w następujących tematach:
   > * [Parametry szablonu dla wdrożenia](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)
   > * [Autoryzuj połączenia OAuth](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)
   > * [Uwierzytelnianie dostępu za pomocą tożsamości zarządzanych](../logic-apps/create-managed-service-identity.md)
   > * [Uwierzytelnianie połączeń dla wdrożenia aplikacji logiki](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)

1. W akcji **Wyślij wiadomość e-mail** Określ informacje do uwzględnienia w powiadomieniu e-mail.

   1. W polu **Do** wprowadź adres e-mail adresata. Na potrzeby tego przykładu Użyj swojego adresu e-mail.

        > [!NOTE]
        > Lista **Dodaj zawartość dynamiczną** pojawia się po kliknięciu wewnątrz pola **do** i niektórych innych pól wejściowych w projektancie Logic Apps. Ten przykład używa zawartości dynamicznej w późniejszym kroku. Lista **Dodaj zawartość dynamiczną** pokazuje wszystkie dostępne dane wyjściowe z poprzedniego kroku, których można użyć jako danych wejściowych dla bieżącej akcji.

   1. W polu **temat** wprowadź temat powiadomienia e-mail. Na potrzeby tego przykładu wprowadź następujący tekst z końcowym pustym miejscem: `New RSS item: `

      ![Zrzut ekranu projektanta Logic Apps, pokazujący akcję "Wyślij wiadomość e-mail" i kursor w polu właściwości "podmiot".](./media/quickstart-create-first-logic-app-workflow/send-email-subject.png)

   1. Z listy **Dodaj zawartość dynamiczną** wybierz pozycję **tytuł źródła** danych, który jest wyprowadzany z wyzwalacza, **gdy zostanie opublikowany element kanału informacyjnego**. Twoje powiadomienie e-mail używa tych danych wyjściowych do uzyskania tytułu elementu RSS.

      ![Zrzut ekranu projektanta Logic Apps, pokazujący akcję "Wyślij wiadomość e-mail" i kursor w polu właściwości "podmiot" z otwartą listą zawartości dynamicznej i wybranym wyjściem "tytuł źródła danych".](./media/quickstart-create-first-logic-app-workflow/send-email-subject-dynamic-content.png)

      > [!TIP]
      > Jeśli na liście zawartości dynamicznej nie ma żadnych danych wyjściowych z wyzwalacza **gdy element kanału informacyjnego jest publikowany** , obok nagłówka akcji wybierz pozycję **Zobacz więcej**.
      > 
      > ![Zrzut ekranu projektanta Logic Apps, pokazujący otwartą listę zawartości dynamicznej i "Zobacz więcej" wybrany dla wyzwalacza.](./media/quickstart-create-first-logic-app-workflow/dynamic-content-list-see-more-actions.png)

      Gdy wszystko będzie gotowe, temat wiadomości e-mail będzie wyglądać następująco:

      ![Zrzut ekranu projektanta Logic Apps, w którym jest wyświetlana akcja "Wyślij wiadomość e-mail" i Przykładowa wiadomość e-mail z uwzględnieniem zawartej właściwości "tytuł źródła danych".](./media/quickstart-create-first-logic-app-workflow/send-email-feed-title.png)

      Jeśli w projektancie pojawi się pętla "for each", wybrano token dla tablicy; na przykład **Kategorie — token elementu** . W przypadku takiego typu tokenów projektant automatycznie dodaje pętlę do akcji, która odwołuje się do tego tokenu. W ten sposób Twoja aplikacja logiki wykonuje tę samą akcję dla każdego elementu tablicy. Aby usunąć pętlę, wybierz **wielokropek** (**...**) na pasku tytułu pętli, a następnie wybierz pozycję **Usuń**.

   1. W polu **treść** wprowadź treść wiadomości e-mail. W tym przykładzie zawartość zawiera trzy właściwości z tekstem opisowym dla każdej z nich: `Title:` , właściwość **title źródła** danych `Date published:` ,, **Źródło danych opublikowanych we** właściwości, a `Link:` także właściwość **linku podstawowego źródła danych** . Aby dodać puste wiersze w polu edycji, naciśnij klawisze Shift + Enter.

      | Właściwość | Opis |
      |----------|-------------|
      | **Tytuł źródła danych** | Tytuł elementu |
      | **Data opublikowania kanału informacyjnego** | Data i godzina opublikowania elementu |
      | **Link podstawowego źródła danych** | Adres URL elementu |
      |||

      ![Zrzut ekranu projektanta Logic Apps, w którym zostanie wyświetlona akcja "Wyślij wiadomość e-mail" i wybrane właściwości w polu "treść".](./media/quickstart-create-first-logic-app-workflow/send-email-body.png)

1. Zapisz aplikację logiki. Wybierz pozycję **Zapisz** w menu projektanta.

Następnie [Sprawdź, czy aplikacja logiki działa](#test-logic-app).

<a name="test-logic-app"></a>

## <a name="run-your-logic-app"></a>Uruchamianie aplikacji logiki

Po utworzeniu przykładowej aplikacji logiki Sprawdź, czy przepływ pracy jest prawidłowo skonfigurowany. Możesz poczekać, aż aplikacja logiki sprawdzi kanał informacyjny RSS zgodnie z określonym harmonogramem. Możesz też ręcznie uruchomić aplikację logiki, wybierając pozycję **Uruchom** na pasku narzędzi projektanta Logic Apps, jak pokazano na poniższym zrzucie ekranu. 

Jeśli źródło danych RSS będzie zawierać nowe elementy, aplikacja logiki wyśle wiadomość e-mail dla każdego nowego elementu. W przeciwnym razie aplikacja logiki czeka do następnego interwału, aby ponownie sprawdzić kanał informacyjny RSS. 

![Zrzut ekranu projektanta Logic Apps, na którym jest wyświetlany przycisk "Uruchom" wybrany na pasku narzędzi projektanta.](./media/quickstart-create-first-logic-app-workflow/run-logic-app-test.png)

Poniższy zrzut ekranu przedstawia przykładowe powiadomienie e-mail z tej przykładowej aplikacji logiki. Wiadomość e-mail zawiera szczegółowe informacje dotyczące każdego elementu kanału informacyjnego RSS wybranego w projektancie, a także opisowy tekst dodany dla każdej z nich.

![Zrzut ekranu programu Outlook, pokazujący przykładową wiadomość e-mail otrzymaną po wyświetleniu nowego elementu kanału informacyjnego RSS z tytułem elementu, datą opublikowania i linkiem.](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Jeśli nie otrzymujesz wiadomości e-mail z powiadomieniem z aplikacji logiki zgodnie z oczekiwaniami:

* Sprawdź folder wiadomości-śmieci lub spamu konta e-mail, na wypadek gdyby komunikat został niepoprawnie przefiltrowany.
* Upewnij się, że używane źródło danych RSS zawiera elementy opublikowane od czasu ostatniego sprawdzenia zaplanowanego lub ręcznego.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po zakończeniu testowania tej przykładowej aplikacji logiki Wyczyść aplikację logiki i wszystkie powiązane zasoby, usuwając grupę zasobów utworzoną w ramach tego przykładu.

> [!NOTE]
> Po [usunięciu aplikacji logiki](manage-logic-apps-with-azure-portal.md#delete-logic-apps)nie są tworzone żadne nowe uruchomienia. Wszystkie trwające i oczekujące przebiegi zostają anulowane. Anulowanie kilku tysięcy przebiegów może zająć dużo czasu.

1. W polu wyszukiwania na platformie Azure wprowadź wartość `resource groups` , a następnie wybierz pozycję **grupy zasobów**.

   ![Zrzut ekranu przedstawiający pole wyszukiwania Azure Portal z wyszukiwanym terminem "grupy zasobów".](./media/quickstart-create-first-logic-app-workflow/find-resource-groups.png)

1. Znajdź i wybierz grupę zasobów aplikacji logiki. W okienku **Przegląd** wybierz pozycję **Usuń grupę zasobów**.

   ![Zrzut ekranu przedstawiający Azure Portal z wybraną grupą zasobów i przyciskiem "Usuń grupę zasobów".](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. Gdy zostanie wyświetlone okienko potwierdzenia, wprowadź nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.

   ![Zrzut ekranu przedstawiający Azure Portal z okienkiem potwierdzenia i wprowadzoną nazwę grupy zasobów do usunięcia.](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start utworzono pierwszą aplikację logiki w Azure Portal, aby sprawdzić źródło danych RSS pod kątem aktualizacji zgodnie z harmonogramem i wysłać powiadomienie e-mail dotyczące każdego nowego elementu kanału informacyjnego. 

Aby dowiedzieć się, jak utworzyć bardziej zaawansowane przepływy pracy oparte na harmonogramie w Logic Apps, zobacz następujący samouczek:

> [!div class="nextstepaction"]
> [Sprawdzanie ruchu za pomocą aplikacji logiki opartej na harmonogramie](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)