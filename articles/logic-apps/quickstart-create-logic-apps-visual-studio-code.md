---
title: Automatyzowanie zadań i przepływów pracy za pomocą Visual Studio Code
description: Tworzenie lub edytowanie definicji przepływu pracy aplikacji logiki przy użyciu Visual Studio Code (VS Code)
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, deli, logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 10/28/2020
ms.openlocfilehash: f46c093bffcc26b5f7975d25ccaca648bdc527da
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99054690"
---
# <a name="quickstart-create-and-manage-logic-app-workflow-definitions-by-using-visual-studio-code"></a>Szybki start: Tworzenie definicji przepływu pracy aplikacji logiki i zarządzanie nimi za pomocą programu Visual Studio Code

Dzięki [Azure Logic Apps](../logic-apps/logic-apps-overview.md) i Visual Studio Code można tworzyć i zarządzać aplikacjami logiki, które ułatwiają automatyzację zadań, przepływów pracy i procesów integracji aplikacji, danych, systemów i usług w różnych organizacjach i przedsiębiorstwach. Ten przewodnik Szybki Start przedstawia sposób tworzenia i edytowania podstawowych definicji przepływu pracy, które używają JavaScript Object Notation (JSON) dla usługi Logic Apps za pośrednictwem środowiska opartego na kodzie. Możesz również korzystać z istniejących aplikacji logiki, które są już wdrożone na platformie Azure.

Chociaż te same zadania można wykonać w [Azure Portal](https://portal.azure.com) i w programie Visual Studio, można szybciej rozpocząć pracę w Visual Studio Code, gdy znasz już definicje aplikacji logiki i chcesz pracować bezpośrednio w kodzie. Można na przykład wyłączyć, włączyć, usunąć i odświeżać już utworzone aplikacje logiki. Ponadto można korzystać z usługi Logic Apps i kont integracji z dowolnego platformy deweloperskiej, w której działa Visual Studio Code, takich jak Linux, Windows i Mac.

W tym artykule można utworzyć tę samą aplikację logiki z tego [przewodnika Szybki Start](../logic-apps/quickstart-create-first-logic-app-workflow.md), która koncentruje się na podstawowych koncepcjach. Możesz również [uzyskać informacje na temat tworzenia przykładowej aplikacji w programie Visual Studio](quickstart-create-logic-apps-with-visual-studio.md)i dowiedzieć się, jak [tworzyć aplikacje i zarządzać nimi za pomocą interfejsu Command-Line platformy Azure (interfejs wiersza polecenia platformy Azure)](quickstart-logic-apps-azure-cli.md). W Visual Studio Code aplikacja logiki wygląda podobnie do tego przykładu:

![Przykładowa definicja przepływu pracy aplikacji logiki](./media/quickstart-create-logic-apps-visual-studio-code/visual-studio-code-overview.png)

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz następujące elementy:

* Jeśli nie masz konta i subskrypcji platformy Azure, [zarejestruj się, aby skorzystać z bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Podstawowa wiedza na temat [definicji przepływu pracy aplikacji logiki](../logic-apps/logic-apps-workflow-definition-language.md) i ich struktury zgodnie z opisem w pliku JSON

  Jeśli jesteś nowym do Logic Apps, Wypróbuj ten [Przewodnik Szybki Start](../logic-apps/quickstart-create-first-logic-app-workflow.md), który tworzy pierwsze Aplikacje logiki w Azure Portal i koncentruje się na podstawowych pojęciach.

* Dostęp do sieci Web w celu logowania się do platformy Azure i subskrypcji platformy Azure

* Pobierz i zainstaluj te narzędzia, jeśli jeszcze ich nie masz:

  * [Visual Studio Code wersja 1.25.1 lub nowsza](https://code.visualstudio.com/), która jest bezpłatna

  * Visual Studio Code rozszerzenie dla Azure Logic Apps

    To rozszerzenie można pobrać i zainstalować z poziomu [Visual Studio Marketplace](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) lub bezpośrednio z poziomu Visual Studio Code. Przed zainstalowaniem upewnij się, że załadujesz Visual Studio Code.

    ![Znajdź "Visual Studio Code rozszerzenie dla Azure Logic Apps"](./media/quickstart-create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    Aby sprawdzić, czy rozszerzenie zostało poprawnie zainstalowane, wybierz ikonę platformy Azure, która pojawia się na pasku narzędzi Visual Studio Code.

    ![Potwierdź, że rozszerzenie zostało poprawnie zainstalowane](./media/quickstart-create-logic-apps-visual-studio-code/confirm-installed-visual-studio-code-extension.png)

    Aby uzyskać więcej informacji, zobacz temat [rozszerzanie witryny Marketplace](https://code.visualstudio.com/docs/editor/extension-gallery). Aby współtworzyć wersję "open source" tego rozszerzenia, odwiedź [Azure Logic Apps rozszerzenia Visual Studio Code w witrynie GitHub](https://github.com/Microsoft/vscode-azurelogicapps).

* Jeśli aplikacja logiki musi komunikować się przez zaporę, która ogranicza ruch do określonych adresów IP, zapora musi zezwolić na dostęp *zarówno* do [przychodzącego](logic-apps-limits-and-config.md#inbound) , jak i [wychodzącego](logic-apps-limits-and-config.md#outbound) adresu IP używanego przez usługę Logic Apps lub środowisko uruchomieniowe w regionie platformy Azure, w którym znajduje się aplikacja logiki. Jeśli aplikacja logiki używa również łączników [zarządzanych](../connectors/apis-list.md#managed-api-connectors), takich jak łącznik usługi Office 365 Outlook lub łącznik SQL, lub używa [łączników niestandardowych](/connectors/custom-connectors/), zapora musi również zezwolić na dostęp *wszystkich* [wychodzących adresów IP łącznika zarządzanego](logic-apps-limits-and-config.md#outbound) w regionie platformy Azure aplikacji logiki.

<a name="access-azure"></a>

## <a name="access-azure-from-visual-studio"></a>Dostęp do platformy Azure z poziomu programu Visual Studio

1. Otwórz program Visual Studio Code. Na pasku narzędzi Visual Studio Code wybierz ikonę platformy Azure.

   ![Wybierz ikonę platformy Azure na pasku narzędzi Visual Studio Code](./media/quickstart-create-logic-apps-visual-studio-code/open-extensions-visual-studio-code.png)

1. W oknie Azure w obszarze **Logic Apps** wybierz pozycję **Zaloguj się do platformy Azure**. Gdy na stronie logowania Microsoft zostanie wyświetlony komunikat z prośbą o zalogowanie się, zaloguj się przy użyciu konta platformy Azure.

   ![Wybierz pozycję "Zaloguj się do platformy Azure"](./media/quickstart-create-logic-apps-visual-studio-code/sign-in-azure-visual-studio-code.png)

   1. Jeśli logowanie trwa dłużej niż zwykle, Visual Studio Code zostanie wyświetlony komunikat z prośbą o zalogowanie się za pomocą witryny sieci Web uwierzytelniania firmy Microsoft przez udostępnienie kodu urządzenia. Aby zamiast tego zalogować się przy użyciu kodu, wybierz pozycję **Użyj kodu urządzenia**.

      ![Zamiast tego Kontynuuj korzystanie z kodu urządzenia](./media/quickstart-create-logic-apps-visual-studio-code/use-device-code-prompt.png)

   1. Aby skopiować kod, wybierz pozycję **kopiuj & Otwórz**.

      ![Kopiuj kod dla logowania do platformy Azure](./media/quickstart-create-logic-apps-visual-studio-code/sign-in-prompt-authentication.png)

   1. Aby otworzyć nowe okno przeglądarki i przejść do witryny sieci Web uwierzytelniania, wybierz pozycję **Otwórz link**.

      ![Potwierdź otwarcie przeglądarki i przechodzenie do witryny sieci Web uwierzytelniania](./media/quickstart-create-logic-apps-visual-studio-code/confirm-open-link.png)

   1. Na stronie **Logowanie do konta** wprowadź kod uwierzytelniania i wybierz pozycję **dalej**.

      ![Wprowadź kod uwierzytelniania dla logowania do platformy Azure](./media/quickstart-create-logic-apps-visual-studio-code/authentication-code-azure-sign-in.png)

1. Wybierz swoje konto platformy Azure. Po zalogowaniu możesz zamknąć przeglądarkę i wrócić do Visual Studio Code.

   W okienku platformy Azure w sekcji konta **Logic Apps** i **integracji** są teraz wyświetlane subskrypcje platformy Azure skojarzone z Twoim kontem. Jeśli jednak nie widzisz oczekiwanych subskrypcji lub jeśli w sekcji przedstawiono zbyt wiele subskrypcji, wykonaj następujące kroki:

   1. Przesuń wskaźnik myszy na etykietę **Logic Apps** . Gdy pojawi się pasek narzędzi, wybierz pozycję **wybierz subskrypcje** (ikona filtru).

      ![Znajdowanie lub filtrowanie subskrypcji platformy Azure](./media/quickstart-create-logic-apps-visual-studio-code/find-or-filter-subscriptions.png)

   1. Z wyświetlonej listy wybierz subskrypcje, które mają być wyświetlane.

1. W obszarze **Logic Apps** wybierz żądaną subskrypcję. Węzeł subskrypcji rozszerza i pokazuje wszystkie aplikacje logiki, które istnieją w danej subskrypcji.

   ![Wybierz subskrypcję platformy Azure](./media/quickstart-create-logic-apps-visual-studio-code/select-azure-subscription.png)

   > [!TIP]
   > W obszarze **konta integracji** wybranie subskrypcji powoduje wyświetlenie wszystkich kont integracji istniejących w danej subskrypcji.

<a name="create-logic-app"></a>

## <a name="create-new-logic-app"></a>Utwórz nową aplikację logiki

1. Jeśli nie zalogowano się na koncie platformy Azure i subskrypcjach jeszcze z poziomu Visual Studio Code, wykonaj [poprzednie kroki, aby zalogować się teraz](#access-azure).

1. W Visual Studio Code w obszarze **Logic Apps** Otwórz menu skrótów subskrypcji i wybierz pozycję **Utwórz aplikację logiki**.

   ![W menu subskrypcja wybierz pozycję "Utwórz aplikację logiki".](./media/quickstart-create-logic-apps-visual-studio-code/create-logic-app-visual-studio-code.png)

   Zostanie wyświetlona lista, w której znajdują się wszystkie grupy zasobów platformy Azure w ramach subskrypcji.

1. Z listy Grupa zasobów wybierz opcję **Utwórz nową grupę zasobów** lub istniejącą grupę zasobów. Na potrzeby tego przykładu Utwórz nową grupę zasobów.

   ![Tworzenie nowej grupy zasobów platformy Azure](./media/quickstart-create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Podaj nazwę grupy zasobów platformy Azure, a następnie naciśnij klawisz ENTER.

   ![Podaj nazwę grupy zasobów platformy Azure](./media/quickstart-create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. Wybierz region platformy Azure, w którym chcesz zapisać metadane aplikacji logiki.

   ![Wybierz lokalizację platformy Azure na potrzeby zapisywania metadanych aplikacji logiki](./media/quickstart-create-logic-apps-visual-studio-code/select-azure-location-new-resources.png)

1. Podaj nazwę aplikacji logiki, a następnie naciśnij klawisz ENTER.

   ![Podaj nazwę aplikacji logiki](./media/quickstart-create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   W oknie platformy Azure w obszarze subskrypcji platformy Azure zostanie wyświetlona nowa i pusta aplikacja logiki. Visual Studio Code otwiera również plik JSON (.logicapp.json), który zawiera szkieletową definicję przepływu pracy dla aplikacji logiki. Teraz można rozpocząć ręczne tworzenie definicji przepływu pracy aplikacji logiki w tym pliku JSON. Aby uzyskać informacje techniczne na temat struktury i składni dla definicji przepływu pracy, zobacz [schemat języka definicji przepływu pracy dla Azure Logic Apps](../logic-apps/logic-apps-workflow-definition-language.md).

   ![Pusty plik JSON definicji przepływu pracy aplikacji logiki](./media/quickstart-create-logic-apps-visual-studio-code/empty-logic-app-workflow-definition.png)

   Przykładowo poniżej przedstawiono definicję przykładowej przepływu pracy aplikacji logiki, która rozpoczyna się od wyzwalacza RSS i akcji programu Outlook w programie Office 365. Zazwyczaj elementy JSON są wyświetlane alfabetycznie w każdej sekcji. Jednak ten przykład pokazuje te elementy w kolejności, w jakiej są wyświetlane kroki aplikacji logiki w projektancie.

   > [!IMPORTANT]
   > Jeśli chcesz ponownie użyć tej przykładowej definicji aplikacji logiki, musisz mieć konto organizacyjne, na przykład @fabrikam.com . Pamiętaj, aby zamienić fikcyjny adres e-mail na własny adres e-mail. Aby użyć innego łącznika poczty e-mail, takiego jak Outlook.com lub Gmail, Zastąp `Send_an_email_action` akcję podobną akcją dostępną z [łącznika poczty e-mail obsługiwanego przez Azure Logic Apps](../connectors/apis-list.md).
   >
   > Jeśli chcesz korzystać z łącznika usługi Gmail, tylko konta firmowe z zestawu G-Suite mogą używać tego łącznika bez ograniczeń w usłudze Logic Apps. 
   > Jeśli masz konto użytkownika usługi Gmail, możesz użyć tego łącznika z tylko określonymi usługami zatwierdzonymi przez firmę Google lub możesz [utworzyć aplikację kliencką Google, która będzie używana do uwierzytelniania za pomocą łącznika usługi Gmail](/connectors/gmail/#authentication-and-bring-your-own-application). 
   > Aby uzyskać więcej informacji, zobacz [zabezpieczenia danych i zasady ochrony prywatności dla łączników Google w Azure Logic Apps](../connectors/connectors-google-data-security-privacy-policy.md).

   ```json
   {
      "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
         "$connections": {
            "defaultValue": {},
            "type": "Object"
         }
      },
      "triggers": {
         "When_a_feed_item_is_published": {
            "recurrence": {
               "frequency": "Minute",
               "interval": 1
            },
            "splitOn": "@triggerBody()?['value']",
            "type": "ApiConnection",
            "inputs": {
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['rss']['connectionId']"
                  }
               },
               "method": "get",
               "path": "/OnNewFeed",
               "queries": {
                  "feedUrl": "http://feeds.reuters.com/reuters/topNews"
               }
            }
         }
      },
      "actions": {
         "Send_an_email_(V2)": {
            "runAfter": {},
            "type": "ApiConnection",
            "inputs": {
               "body": {
                  "Body": "<p>Title: @{triggerBody()?['title']}<br>\n<br>\nDate published: @{triggerBody()?['updatedOn']}<br>\n<br>\nLink: @{triggerBody()?['primaryLink']}</p>",
                  "Subject": "RSS item: @{triggerBody()?['title']}",
                  "To": "sophia-owen@fabrikam.com"
               },
               "host": {
                  "connection": {
                     "name": "@parameters('$connections')['office365']['connectionId']"
                  }
               },
               "method": "post",
               "path": "/v2/Mail"
            }
         }
      },
      "outputs": {}
   }
   ```

1. Gdy skończysz, Zapisz definicję przepływu pracy aplikacji logiki. (Menu plik > Zapisz lub naciśnij klawisze CTRL + S)

1. Gdy zostanie wyświetlony monit o przekazanie aplikacji logiki do subskrypcji platformy Azure, wybierz pozycję **Przekaż**.

   Ten krok umożliwia opublikowanie aplikacji logiki w [Azure Portal](https://portal.azure.com), co spowoduje, że logika działa na platformie Azure.

   ![Przekaż nową aplikację logiki do subskrypcji platformy Azure](./media/quickstart-create-logic-apps-visual-studio-code/upload-new-logic-app.png)

## <a name="view-logic-app-in-designer"></a>Wyświetl aplikację logiki w projektancie

W Visual Studio Code można otworzyć aplikację logiki w widoku projektu tylko do odczytu. Chociaż nie możesz edytować aplikacji logiki w projektancie, możesz wizualnie sprawdzić przepływ pracy aplikacji logiki przy użyciu widoku projektanta.

W oknie platformy Azure w obszarze **Logic Apps** Otwórz menu skrótów aplikacji logiki, a następnie wybierz pozycję **Otwórz w projektancie**.

Projektant tylko do odczytu zostanie otwarty w osobnym oknie i zostanie wyświetlony przepływ pracy aplikacji logiki, na przykład:

![Wyświetlanie aplikacji logiki w projektancie tylko do odczytu](./media/quickstart-create-logic-apps-visual-studio-code/logic-app-designer-view.png)

## <a name="view-in-azure-portal"></a>Wyświetlanie w witrynie Azure Portal

Aby przejrzeć aplikację logiki w Azure Portal, wykonaj następujące kroki:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu tego samego konta platformy Azure i subskrypcji, które są skojarzone z aplikacją logiki.

1. W polu wyszukiwania Azure Portal wprowadź nazwę aplikacji logiki. Z listy wyników wybierz aplikację logiki.

   ![Twoja nowa aplikacja logiki w Azure Portal](./media/quickstart-create-logic-apps-visual-studio-code/published-logic-app-in-azure.png)

<a name="disable-enable-logic-app"></a>

## <a name="disable-or-enable-logic-app"></a>Wyłączanie lub włączanie aplikacji logiki

W Visual Studio Code, Jeśli edytujesz opublikowaną aplikację logiki i zapiszesz zmiany, *nadpiszesz* już wdrożoną aplikację. Aby uniknąć przerwania działania aplikacji logiki w środowisku produkcyjnym i zminimalizować zakłócenia, najpierw zdezaktywuj aplikację logiki. Następnie możesz ponownie uaktywnić aplikację logiki po potwierdzeniu, że aplikacja logiki nadal działa.

1. Jeśli nie zalogowano się na koncie platformy Azure i subskrypcjach jeszcze z poziomu Visual Studio Code, wykonaj [poprzednie kroki, aby zalogować się teraz](#access-azure).

1. W oknie platformy Azure w obszarze **Logic Apps** Rozwiń swoją subskrypcję platformy Azure, aby wyświetlić wszystkie aplikacje logiki w tej subskrypcji.

   1. Aby wyłączyć aplikację logiki, otwórz menu aplikacji logiki, a następnie wybierz pozycję **Wyłącz**.

      ![Wyłączanie aplikacji logiki](./media/quickstart-create-logic-apps-visual-studio-code/disable-published-logic-app.png)

   1. Gdy wszystko będzie gotowe do ponownej aktywacji aplikacji logiki, otwórz menu aplikacji logiki i wybierz pozycję **Włącz**.

      ![Włączanie aplikacji logiki](./media/quickstart-create-logic-apps-visual-studio-code/enable-published-logic-app.png)

<a name="edit-logic-app"></a>

## <a name="edit-deployed-logic-app"></a>Edytuj wdrożoną aplikację logiki

W Visual Studio Code można otworzyć i edytować definicję przepływu pracy dla już wdrożonej aplikacji logiki na platformie Azure.

> [!IMPORTANT] 
> Przed edytowaniem aktywnie działającej aplikacji logiki w środowisku produkcyjnym należy unikać ryzyka związanego z uszkodzeniem tej aplikacji logiki i zminimalizować zakłócenia, [wyłączając najpierw aplikację logiki](#disable-enable-logic-app).

1. Jeśli nie zalogowano się na koncie platformy Azure i subskrypcjach jeszcze z poziomu Visual Studio Code, wykonaj [poprzednie kroki, aby zalogować się teraz](#access-azure).

1. W oknie platformy Azure w obszarze **Logic Apps** Rozwiń swoją subskrypcję platformy Azure i wybierz żądaną aplikację logiki.

1. Otwórz menu aplikacji logiki, a następnie wybierz pozycję **Otwórz w edytorze**. Lub obok nazwy aplikacji logiki wybierz ikonę edycji.

   ![Otwieranie edytora dla istniejącej aplikacji logiki](./media/quickstart-create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   Visual Studio Code otwiera .logicapp.jspliku w lokalnym folderze tymczasowym, aby można było wyświetlić definicję przepływu pracy aplikacji logiki.

   ![Wyświetl definicję przepływu pracy dla opublikowanej aplikacji logiki](./media/quickstart-create-logic-apps-visual-studio-code/edit-published-logic-app-workflow-definition.png)

1. Wprowadź zmiany w definicji przepływu pracy aplikacji logiki.

1. Gdy wszystko będzie gotowe, zapisz zmiany. (Menu plik > Zapisz lub naciśnij klawisze CTRL + S)

1. Po wyświetleniu monitu o przekazanie zmian i *zastępowanie* istniejącej aplikacji logiki w Azure Portal wybierz pozycję **Przekaż**.

   Ten krok powoduje opublikowanie aktualizacji aplikacji logiki w [Azure Portal](https://portal.azure.com).

   ![Przekazywanie zmian definicji aplikacji logiki na platformie Azure](./media/quickstart-create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="view-or-promote-other-versions"></a>Wyświetl lub Podwyższ poziom innych wersji

W Visual Studio Code można otwierać i przeglądać wcześniejsze wersje aplikacji logiki. Możesz również promować wcześniejszą wersję do bieżącej wersji.

> [!IMPORTANT] 
> Przed zmianą aktywnie działającej aplikacji logiki w środowisku produkcyjnym należy unikać ryzyka związanego z uszkodzeniem tej aplikacji logiki i zminimalizować zakłócenia, [wyłączając najpierw aplikację logiki](#disable-enable-logic-app).

1. W oknie platformy Azure w obszarze **Logic Apps** Rozwiń swoją subskrypcję platformy Azure, aby wyświetlić wszystkie aplikacje logiki w tej subskrypcji.

1. W obszarze subskrypcja rozwiń aplikację logiki i rozwiń pozycję **wersje**.

   Lista **wersje** zawiera wcześniejsze wersje aplikacji logiki, jeśli takie istnieją.

   ![Poprzednie wersje aplikacji logiki](./media/quickstart-create-logic-apps-visual-studio-code/view-previous-versions.png)

1. Aby wyświetlić wcześniejszą wersję, wybierz jeden z kroków:

   * Aby wyświetlić definicję JSON, w obszarze **wersje** wybierz numer wersji dla tej definicji. Lub Otwórz menu skrótów tej wersji, a następnie wybierz polecenie **Otwórz w edytorze**.

     Zostanie otwarty nowy plik na komputerze lokalnym i zostanie wyświetlona definicja JSON tej wersji.

   * Aby wyświetlić wersję w widoku projektanta tylko do odczytu, otwórz menu skrótów tej wersji, a następnie wybierz polecenie **Otwórz w projektancie**.

1. Aby promować wcześniejszą wersję do bieżącej wersji, wykonaj następujące kroki:

   1. W obszarze **wersje** Otwórz menu skrótów starszej wersji, a następnie wybierz polecenie **Podwyższ poziom**.

      ![Podwyższ poziom wcześniejszej wersji](./media/quickstart-create-logic-apps-visual-studio-code/promote-earlier-version.png)

   1. Aby kontynuować po Visual Studio Code zostanie wyświetlony monit o potwierdzenie, wybierz pozycję **tak**.

      ![Potwierdź podwyższenie poziomu wcześniejszej wersji](./media/quickstart-create-logic-apps-visual-studio-code/confirm-promote-version.png)

      Visual Studio Code promuje wybraną wersję do bieżącej wersji i przypisze nową liczbę do awansowanej wersji. Wcześniej bieżąca wersja jest teraz wyświetlana w ramach awansowanej wersji.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Twórz stanowe i bezstanowe Aplikacje logiki w Visual Studio Code (wersja zapoznawcza)](../logic-apps/create-stateful-stateless-workflows-visual-studio-code.md)
