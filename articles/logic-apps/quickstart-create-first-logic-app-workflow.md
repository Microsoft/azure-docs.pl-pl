---
title: Szybki start — tworzenie pierwszego przepływu pracy Logic Apps — Azure Portal
description: Skompilowanie pierwszego zautomatyzowanego Logic Apps przepływu pracy w Azure Portal użyciu tego przewodnika Szybki start. Poznaj podstawy integracji systemu i rozwiązań enterprise application integration (EAI) w Logic Apps.
services: logic-apps
ms.suite: integration
ms.reviewer: logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/24/2021
ms.openlocfilehash: d05566c0734f95e14335c6165de0b2104fa19bc6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764859"
---
# <a name="quickstart-create-your-first-logic-apps-workflow---azure-portal"></a>Szybki start: tworzenie pierwszego przepływu pracy Logic Apps — Azure Portal

W tym przewodniku Szybki start wyjaśniono, jak utworzyć pierwszy przepływ pracy w Azure Logic Apps [za](logic-apps-overview.md) pomocą [Azure Portal.](https://portal.azure.com) W tym przewodniku wprowadzającym wyjaśniono również podstawowe pojęcia dotyczące usługi Logic Apps, w tym sposób tworzenia nowej aplikacji logiki, dodawania wyzwalacza i akcji do aplikacji logiki oraz testowania aplikacji logiki. Postępuj zgodnie z tym przewodnikem Szybki start, aby utworzyć przykładową aplikację logiki, która regularnie sprawdza źródło danych RSS i wysyła powiadomienie e-mail dotyczące nowych elementów. Poniższy zrzut ekranu przedstawia przepływ pracy wysokiego poziomu dla tej przykładowej aplikacji logiki:

![Zrzut ekranu Logic Apps Designer przedstawiający przykładową aplikację logiki, w której wyzwalacz jest kanałem informacyjnym RSS, a akcja wysyła wiadomość e-mail.](./media/quickstart-create-first-logic-app-workflow/quickstart-workflow-overview.png)

<a name="prerequisites"></a>

Jeśli chcesz dowiedzieć się, jak utworzyć pierwszą aplikację logiki i zarządzać nimi za pomocą innych interfejsów i aplikacji, zobacz następujące inne Logic Apps Szybki start: 

* [Tworzenie aplikacji logiki i zarządzanie nimi przy użyciu interfejsu azure Command-Line (interfejs wiersza polecenia platformy Azure)](quickstart-logic-apps-azure-cli.md)
* [Tworzenie aplikacji logiki i zarządzanie nimi w Visual Studio Code](quickstart-create-logic-apps-visual-studio-code.md)
* [Tworzenie aplikacji logiki i zarządzanie nimi w Visual Studio](quickstart-create-logic-apps-with-visual-studio.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Konto i subskrypcja platformy Azure. Jeśli go nie masz, zarejestruj [się, aby uzyskać bezpłatne konto platformy Azure.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

* Konto e-mail z usługi obsługiwanej przez usługę Logic Apps (np. Office 365 Outlook lub Outlook.com). W przypadku innych obsługiwanych dostawców poczty [e-mail zapoznaj się z listą łączników](/connectors/).

    > [!IMPORTANT]
    > Jeśli używasz łącznika [Usługi Gmail,](/connectors/gmail/)pamiętaj, że tylko konta usługi G Suite mogą używać tego łącznika bez ograniczeń w Logic Apps. Jeśli masz konto użytkownika usługi Gmail, możesz używać tego łącznika tylko z określonymi usługami zatwierdzonymi przez Google, chyba że utworzysz aplikację kliencową Google do użycia z uwierzytelnianiem za pomocą łącznika [usługi Gmail.](/connectors/gmail/#authentication-and-bring-your-own-application) Aby uzyskać więcej informacji, zobacz [Zasady zabezpieczeń danych i ochrony prywatności dla łączników Google](../connectors/connectors-google-data-security-privacy-policy.md)w Azure Logic Apps .

* Jeśli aplikacja logiki musi komunikować się za pośrednictwem zapory, która ogranicza  ruch [](logic-apps-limits-and-config.md#inbound) do określonych adresów [IP,](logic-apps-limits-and-config.md#outbound) musi zezwalać na dostęp zarówno dla adresów IP dla ruchu przychodzącego, jak i wychodzącego używanego przez usługę Logic Apps lub środowisko uruchomieniowe w regionie świadczenia usługi Azure, w którym istnieje aplikacja logiki. Jeśli aplikacja logiki używa również łączników zarządzanych, takich jak łącznik office 365 Outlook lub łącznik  SQL, lub łączników niestandardowych, [](/connectors/custom-connectors/)zapora musi również zezwalać na dostęp do wszystkich wychodzących adresów [IP](logic-apps-limits-and-config.md#outbound) łącznika zarządzanego w regionie platformy Azure aplikacji logiki. [](../connectors/managed.md)

<a name="create-logic-app"></a>

## <a name="create-your-logic-app"></a>Tworzenie aplikacji logiki

1. Zaloguj się do [witryny Azure Portal](https://portal.azure.com) przy użyciu poświadczeń konta Azure.

1. W polu Azure Portal wpisz , `logic apps` a następnie wybierz pozycję **Logic Apps**.

   ![Zrzut ekranu Azure Portal przedstawiający pole wyszukiwania z terminem wyszukiwania "logic apps" i "Logic Apps" jako wybranym wynikiem wyszukiwania.](./media/quickstart-create-first-logic-app-workflow/find-select-logic-apps.png)

1. Na stronie **Logic Apps** wybierz pozycję **Dodaj**.

   ![Zrzut ekranu Logic Apps usługi w Azure Portal z listą aplikacji logiki i wybranym przyciskiem "Dodaj".](./media/quickstart-create-first-logic-app-workflow/add-new-logic-app.png)

1. W **okienku Aplikacja logiki** podaj podstawowe szczegóły i ustawienia aplikacji logiki. Na potrzeby [tej przykładowej](../azure-resource-manager/management/overview.md#terminology) aplikacji logiki utwórz nową grupę zasobów.

   | Właściwość | Wartość | Opis |
   |----------|-------|-------------|
   | **Nazwa** | <*logic-app-name*> | Nazwa aplikacji logiki, która musi być unikatowa w różnych regionach. Nazwa może zawierać tylko litery, cyfry, łączniki ( `-` ), podkreślenia ( `_` ), nawiasy ( , ) i `(` `)` kropki ( `.` ). W tym przykładzie użyto "My-First-Logic-App". |
   | **Subskrypcja** | <*Nazwa subskrypcji platformy Azure*> | Nazwa subskrypcji platformy Azure. |
   | **Grupa zasobów** | <*Nazwa grupy zasobów platformy Azure*> | Nazwa grupy zasobów [platformy Azure,](../azure-resource-manager/management/overview.md#terminology) w której tworzysz aplikację logiki. Nazwa grupy zasobów musi być unikatowa w różnych regionach. W tym przykładzie użyto ciągu "My-First-LA-RG". |
   | **Lokalizacja** | <*Region świadczenia usługi Azure*> | Region świadczenia usługi Azure, w którym mają być przechowywane informacje o aplikacji logiki. W tym przykładzie użyto "Zachodnie stany USA". |
   | **Log Analytics** | Wyłączone | Ustawienie rejestrowania diagnostycznego, które jest **domyślnie** wyłączone. W tym przykładzie zachowaj ustawienie **Wyłączone.** |
   ||||

   ![Zrzut ekranu Logic Apps tworzenia aplikacji z okienkiem ze szczegółami nowej aplikacji logiki.](./media/quickstart-create-first-logic-app-workflow/create-logic-app-settings.png)

1. Gdy wszystko będzie gotowe, wybierz pozycję **Przejrzyj i utwórz.** Potwierdź podane szczegóły, a następnie wybierz pozycję **Utwórz**.

1. Po pomyślnym wdrożeniu aplikacji na platformie Azure wybierz **pozycję Przejdź do zasobu.** Możesz też znaleźć i wybrać aplikację logiki, wpisując nazwę w polu wyszukiwania.

   ![Zrzut ekranu przedstawiający stronę wdrażania zasobów z wybranym przyciskiem "Przejdź do zasobu".](./media/quickstart-create-first-logic-app-workflow/go-to-new-logic-app-resource.png)

   Zostanie otwarty Projektant aplikacji usługi Logic Apps wyświetlający stronę z wprowadzającym wideo i najczęściej używanymi wyzwalaczami. W obszarze **Szablony** wybierz pozycję **Pusta aplikacja logiki**.

   ![Zrzut ekranu Logic Apps Designer przedstawiający galerię szablonów i wybrany szablon "Pusta aplikacja logiki".](./media/quickstart-create-first-logic-app-workflow/choose-logic-app-template.png)

Następnie [dodaj wyzwalacz do aplikacji logiki](#add-rss-trigger).

<a name="add-rss-trigger"></a>

## <a name="add-the-rss-trigger"></a>Dodawanie wyzwalacza RSS

Każda aplikacja logiki musi rozpoczynać się od [wyzwalacza](../logic-apps/logic-apps-overview.md#how-do-logic-apps-work), który jest wyzwalany w przypadku wystąpienia określonego zdarzenia lub gdy zostanie spełniony określony warunek. Za każdym razem, gdy wyzwalacz znajdzie nowe elementy, jest wyzwalany, a aparat Logic Apps tworzy wystąpienie aplikacji logiki, które uruchamia i uruchamia przepływ pracy. Jeśli wyzwalacz nie znajdzie nowych elementów, wyzwalacz nie zostanie uruchomiony i nie utworzy wystąpienia ani nie uruchomi przepływu pracy podczas tego sprawdzania.

W tym przykładzie szybkiego startu po utworzeniu aplikacji [logiki](#create-your-logic-app)dodasz wyzwalacz, który sprawdza, czy w kanale informacyjnym RSS znajdują się nowe elementy, i jest wyzwalany, gdy istnieją nowe elementy. Możesz również tworzyć aplikacje logiki z różnymi typami wyzwalaczy, na przykład w samouczku tworzenia automatycznych [przepływów pracy opartych na zatwierdzaniu.](tutorial-process-mailing-list-subscriptions-workflow.md)

1. W **Projektancie aplikacji logiki** w polu wyszukiwania wybierz pozycję **Wszystkie.**

1. Aby znaleźć łącznik RSS, w polu wyszukiwania wprowadź `rss` . Z listy **Wyzwalacze** wybierz wyzwalacz RSS Po **opublikowaniu elementu kanału informacyjnego.**

   ![Zrzut ekranu Logic Apps projektanta z "rss" w polu wyszukiwania i wybranym wyzwalaczem RSS "Po opublikowaniu elementu kanału informacyjnego".](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-new-feed-item.png)

1. Podaj adres URL kanału informacyjnego RSS dla wyzwalacza. Następnie zdefiniuj harmonogram wyzwalacza, ustawiając interwał i częstotliwość.

   | Właściwość | Wartość | Opis |
   | -------- | ----- | ----------- |
   | **Adres URL źródła danych RSS** | <*Adres URL kanału informacyjnego RSS*> | Adres URL kanału informacyjnego RSS, który chcesz monitorować. W tym przykładzie użyto kanału informacyjnego RSS dziennika Wall Street Journal na stronie `https://feeds.a.dj.com/rss/RSSMarketsMain.xml` . Jednak na potrzeby tego przykładu można użyć dowolnego źródła danych RSS, które nie wymaga autoryzacji HTTP. Wybierz kanał informacyjny RSS, który jest często publikowany, aby można było łatwo przetestować aplikację logiki później. |
   | **Interwał** | 1 | Liczba interwałów oczekiwania między sprawdzeniami kanału informacyjnego RSS. W tym przykładzie użyto interwałów 1-minutowych. |
   | **Częstotliwość** | Minuta | Jednostka czasu dla każdego interwału między sprawdzaniem kanału informacyjnego RSS. W tym przykładzie użyto interwałów 1-minutowych. |
   ||||

   ![Zrzut ekranu przedstawiający Logic Apps z ustawieniami wyzwalacza RSS, w tym adresem URL RSS, częstotliwością i interwałem.](./media/quickstart-create-first-logic-app-workflow/add-rss-trigger-settings.png)

1. Na razie zwiń szczegóły wyzwalacza, klikając wewnątrz jego paska tytułu.

   ![Zrzut ekranu przedstawiający Logic Apps Projektanta ze zwiniętym kształtem aplikacji logiki.](./media/quickstart-create-first-logic-app-workflow/collapse-trigger-shape.png)

1. Zapisz aplikację logiki, wybierając pozycję **Zapisz** na pasku narzędzi projektanta.

Aplikacja logiki jest teraz żywa, ale nie robi nic poza sprawdzaniem kanału informacyjnego RSS. Następnie dodaj [akcję, aby](#add-email-action) zdefiniować, co się stanie po aktywowaniu wyzwalacza.

<a name="add-email-action"></a>

## <a name="add-the-send-email-action"></a>Dodawanie akcji "Wyślij wiadomość e-mail"

Po [dodaniu wyzwalacza](#add-rss-trigger)dla aplikacji logiki [](../logic-apps/logic-apps-overview.md#logic-app-concepts) należy dodać akcję w celu określenia odpowiedzi, gdy aplikacja logiki sprawdzi kanał informacyjny RSS i pojawi się nowy element. Możesz również tworzyć aplikacje logiki z bardziej złożonymi akcjami, takimi jak samouczek dotyczący przetwarzania wiadomości e-mail za pomocą usług [Logic Apps, Azure Functions i Azure Storage.](./tutorial-process-email-attachments-workflow.md)

> [!NOTE]
> W tym przykładzie użyto usługi Office 365 Outlook jako usługi poczty e-mail. Jeśli używasz innej obsługiwanej usługi poczty e-mail w aplikacji logiki, interfejs użytkownika może wyglądać inaczej. Podstawowe pojęcia dotyczące nawiązywania połączenia z inną usługą poczty e-mail pozostają jednak takie same.

1. W obszarze **wyzwalacza Gdy element kanału informacyjnego jest publikowany** wybierz **pozycję Nowy krok**.

   ![Zrzut ekranu przedstawiający Logic Apps Projektanta z przepływem pracy z wybranym przyciskiem "Nowy krok".](./media/quickstart-create-first-logic-app-workflow/add-new-step-under-trigger.png)

1. W **obszarze Wybierz akcję** i pole wyszukiwania wybierz pozycję **Wszystkie.**

1. W polu wyszukiwania wpisz , aby `send an email` znaleźć łączniki, które oferują tę akcję. Aby filtrować listę akcji do określonej aplikacji lub usługi, możesz najpierw wybrać tę aplikację lub usługę.

   Jeśli na przykład używasz konta służbowego Microsoft i chcesz używać usługi Office 365 Outlook, wybierz pozycję **Office 365 Outlook.** Jeśli używasz konta osobistego, możesz konto Microsoft Outlook.com. Ten przykład jest kontynuowany w usłudze Office 365 Outlook:

   ![Zrzut ekranu przedstawiający Logic Apps z wybranym łącznikiem poczty e-mail "Office 365 Outlook".](./media/quickstart-create-first-logic-app-workflow/select-connector.png)

   Teraz można łatwiej znaleźć i wybrać akcję, której chcesz użyć, na przykład `send an email` :

   ![Zrzut ekranu przedstawiający Logic Apps Designer z listą odfiltrowanych akcji łącznika poczty e-mail "Office 365 Outlook".](./media/quickstart-create-first-logic-app-workflow/filtered-actions-list.png)

1. Jeśli wybrany łącznik poczty e-mail wyświetli monit o uwierzytelnienie tożsamości, wykonaj ten krok teraz. Aby ten przykład działał, musisz utworzyć połączenie między aplikacją logiki i usługą poczty e-mail. 

    > [!NOTE]
    > W tym przykładzie przedstawiono ręczne uwierzytelnianie w łączniku usługi Office 365 Outlook. Jednak inne łączniki mogą obsługiwać różne typy uwierzytelniania.
    > Uwierzytelnianie dla aplikacji logiki można również obsługiwać na różne sposoby, w zależności od przypadku użycia. Jeśli na przykład używasz szablonów Azure Resource Manager do wdrożenia, możesz sparametryzować, aby zwiększyć bezpieczeństwo często zmienianych danych wejściowych, takich jak szczegóły połączenia. Więcej informacji można znaleźć w następujących tematach:
   > * [Parametry szablonu do wdrożenia](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#template-parameters)
   > * [Autoryzowanie połączeń OAuth](../logic-apps/logic-apps-deploy-azure-resource-manager-templates.md#authorize-oauth-connections)
   > * [Uwierzytelnianie dostępu przy użyciu tożsamości zarządzanych](../logic-apps/create-managed-service-identity.md)
   > * [Uwierzytelnianie połączeń na potrzeby wdrażania aplikacji logiki](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md#authenticate-connections)

1. W akcji **Wyślij wiadomość e-mail** określ informacje, które mają być zawarte w powiadomieniu e-mail.

   1. W polu **Do** wprowadź adres e-mail adresata. W tym przykładzie użyj swojego adresu e-mail.

        > [!NOTE]
        > Lista **Dodaj zawartość dynamiczną** jest  wyświetlana po kliknięciu wewnątrz pola Do i niektórych innych pól wejściowych w Logic Apps Projektanta. W tym przykładzie w późniejszym kroku jest używana zawartość dynamiczna. Na **liście Dodaj zawartość dynamiczną** są dostępne dane wyjściowe z poprzedniego kroku, których można użyć jako danych wejściowych dla bieżącej akcji.

   1. W polu **Temat** wprowadź temat powiadomienia e-mail. W tym przykładzie wprowadź następujący tekst z pustym spacją na końcowej trasie: `New RSS item: `

      ![Zrzut ekranu przedstawiający Logic Apps "Wyślij wiadomość e-mail" i kursor w polu właściwości "Temat".](./media/quickstart-create-first-logic-app-workflow/send-email-subject.png)

   1. Z listy **Dodaj zawartość dynamiczną** wybierz pozycję Tytuł kanału **informacyjnego**, który jest wyjściem z wyzwalacza. Gdy element kanału informacyjnego **jest publikowany.** Powiadomienie e-mail używa tych danych wyjściowych do uzyskania tytułu elementu RSS.

      ![Zrzut ekranu Logic Apps Designer przedstawiający akcję "Wyślij wiadomość e-mail" i kursor wewnątrz pola właściwości "Temat" z otwartą dynamiczną listą zawartości i wybranymi danych wyjściowych "Tytuł źródła danych".](./media/quickstart-create-first-logic-app-workflow/send-email-subject-dynamic-content.png)

      > [!TIP]
      > Jeśli na liście zawartości dynamicznej nie  zostaną wyświetlone żadne dane wyjściowe z wyzwalacza Gdy element kanału informacyjnego jest publikowany, obok nagłówka akcji wybierz pozycję **Zobacz więcej.**
      > 
      > ![Zrzut ekranu przedstawiający Logic Apps Projektanta zawartości dynamicznej z otwartą listą zawartości dynamicznej i wybranymi opcjami "Zobacz więcej" dla wyzwalacza.](./media/quickstart-create-first-logic-app-workflow/dynamic-content-list-see-more-actions.png)

      Gdy wszystko będzie gotowe, temat wiadomości e-mail będzie wyglądać następująco:

      ![Zrzut ekranu przedstawiający Logic Apps Projektanta danych z akcją "Wyślij wiadomość e-mail" i przykładowym tematem wiadomości e-mail z uwzględnioną właściwością "Tytuł źródła danych".](./media/quickstart-create-first-logic-app-workflow/send-email-feed-title.png)

      Jeśli w projektancie pojawi się pętla "For each", wybrano token dla tablicy; na przykład token **categories-Item.** W przypadku takiego typu tokenów projektant automatycznie dodaje pętlę do akcji, która odwołuje się do tego tokenu. W ten sposób Twoja aplikacja logiki wykonuje tę samą akcję dla każdego elementu tablicy. Aby usunąć pętlę, wybierz wielokropek **(** **...**) na pasku tytułu pętli, a następnie wybierz pozycję **Usuń**.

   1. W polu **Treść** wprowadź zawartość treści wiadomości e-mail. W tym przykładzie zawartość zawiera trzy właściwości z tekstem opisowym dla każdej z nich: , właściwość tytuł źródła danych, , źródło danych opublikowane we właściwości i , właściwość Link podstawowego `Title:`  `Date published:`  `Link:` **źródła** danych. Aby dodać puste wiersze w polu edycji, naciśnij klawisze Shift + Enter.

      | Właściwość | Opis |
      |----------|-------------|
      | **Tytuł źródła danych** | Tytuł elementu |
      | **Data opublikowania kanału informacyjnego** | Data i godzina opublikowania elementu |
      | **Link podstawowego źródła danych** | Adres URL elementu |
      |||

      ![Zrzut ekranu przedstawiający Logic Apps "Wyślij wiadomość e-mail" i wybrane właściwości w polu "Treść".](./media/quickstart-create-first-logic-app-workflow/send-email-body.png)

1. Zapisz aplikację logiki. Wybierz **pozycję Zapisz** w menu projektanta.

Następnie [przetestuj działanie aplikacji logiki.](#test-logic-app)

<a name="test-logic-app"></a>

## <a name="run-your-logic-app"></a>Uruchamianie aplikacji logiki

Po utworzeniu przykładowej aplikacji logiki upewnij się, że przepływ pracy jest poprawnie skonfigurowany. Możesz poczekać, aż aplikacja logiki sprawdzi źródło danych RSS na podstawie określonego harmonogramu. Możesz też ręcznie uruchomić aplikację logiki, wybierając pozycję Uruchom na pasku narzędzi Logic Apps Designer, jak pokazano na poniższym zrzucie ekranu.  

Jeśli źródło danych RSS będzie zawierać nowe elementy, aplikacja logiki wyśle wiadomość e-mail dla każdego nowego elementu. W przeciwnym razie aplikacja logiki czeka do następnego interwału, aby ponownie sprawdzić źródło danych RSS. 

![Zrzut ekranu przedstawiający Logic Apps Projektant z wybranym przyciskiem "Uruchom" na pasku narzędzi projektanta.](./media/quickstart-create-first-logic-app-workflow/run-logic-app-test.png)

Poniższy zrzut ekranu przedstawia przykładowe powiadomienie e-mail z tej przykładowej aplikacji logiki. Wiadomość e-mail zawiera szczegóły dla każdego elementu kanału informacyjnego RSS wybranego w projektancie, a także opisowy tekst dodany dla każdego z nich.

![Zrzut ekranu programu Outlook przedstawiający przykładową wiadomość e-mail odebraną, gdy pojawi się nowy element źródła danych RSS z tytułem elementu, datą opublikowania i linkiem.](./media/quickstart-create-first-logic-app-workflow/monitor-rss-feed-email.png)

Jeśli nie otrzymujesz wiadomości e-mail z powiadomieniami z aplikacji logiki zgodnie z oczekiwaniami:

* Sprawdź folder wiadomości-śmieci lub spamu na koncie e-mail, jeśli wiadomość została niepoprawnie odfiltrowana.
* Upewnij się, że źródło danych RSS, z których korzystasz, ma opublikowane elementy od ostatniego zaplanowanego lub ręcznego sprawdzenia.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Po przetestowaniu tej przykładowej aplikacji logiki wyczyść aplikację logiki i wszystkie powiązane zasoby, usuwając grupę zasobów utworzoną dla tego przykładu.

> [!NOTE]
> Po [usunięciu aplikacji logiki](manage-logic-apps-with-azure-portal.md#delete-logic-apps)nie są uruchamiane żadne nowe przebiegi. Wszystkie trwające i oczekujące przebiegi zostają anulowane. Anulowanie kilku tysięcy przebiegów może zająć dużo czasu.

1. W polu wyszukiwania platformy Azure wprowadź `resource groups` , a następnie wybierz pozycję Grupy **zasobów.**

   ![Zrzut ekranu Azure Portal pole wyszukiwania z terminem wyszukiwania "grupy zasobów".](./media/quickstart-create-first-logic-app-workflow/find-resource-groups.png)

1. Znajdź i wybierz grupę zasobów aplikacji logiki. W **okienku** Przegląd wybierz pozycję **Usuń grupę zasobów.**

   ![Zrzut ekranu Azure Portal z wybraną grupą zasobów i przyciskiem "Usuń grupę zasobów".](./media/quickstart-create-first-logic-app-workflow/delete-resource-group.png)

1. Gdy zostanie wyświetlone okienko potwierdzenia, wprowadź nazwę grupy zasobów, a następnie wybierz pozycję **Usuń**.

   ![Zrzut ekranu Azure Portal z okienkiem potwierdzenia i wprowadzono nazwę grupy zasobów do usunięcia.](./media/quickstart-create-first-logic-app-workflow/delete-resource-group-2.png)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono pierwszą aplikację logiki w aplikacji Azure Portal w celu sprawdzenia kanału informacyjnego RSS pod Azure Portal aktualizacji zgodnie z harmonogramem i wysłania powiadomienia e-mail dotyczącego każdego nowego elementu kanału informacyjnego. 

Aby dowiedzieć się, jak tworzyć bardziej zaawansowane przepływy pracy oparte na harmonogramie w Logic Apps, zobacz następujący samouczek:

> [!div class="nextstepaction"]
> [Sprawdzanie ruchu za pomocą aplikacji logiki opartej na harmonogramie](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md)