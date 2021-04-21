---
title: Automatyzowanie zadań i przepływów pracy za pomocą Visual Studio Code
description: Tworzenie lub edytowanie definicji przepływu pracy aplikacji logiki przy użyciu Visual Studio Code (VS Code)
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, deli, logicappspm
ms.topic: quickstart
ms.custom: mvc
ms.date: 03/24/2021
ms.openlocfilehash: 03cfb49dabd5039453f84ef0e636d3948af70d8d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764841"
---
# <a name="quickstart-create-and-manage-logic-app-workflow-definitions-by-using-visual-studio-code"></a>Szybki start: Tworzenie definicji przepływu pracy aplikacji logiki i zarządzanie nimi za pomocą programu Visual Studio Code

Za [Azure Logic Apps](../logic-apps/logic-apps-overview.md) i Visual Studio Code można tworzyć aplikacje logiki i zarządzać nimi, które ułatwiają automatyzowanie zadań, przepływów pracy i procesów w celu integracji aplikacji, danych, systemów i usług między organizacjami i przedsiębiorstwami. W tym przewodniku Szybki start pokazano, jak tworzyć i edytować bazowe definicje przepływu pracy, które używają kodu JavaScript Object Notation (JSON) dla aplikacji logiki za pośrednictwem interfejsu opartego na kodzie. Możesz również pracować nad istniejącymi aplikacjami logiki, które zostały już wdrożone na platformie Azure.

Mimo że te same zadania można wykonywać w programach [Azure Portal](https://portal.azure.com) i Visual Studio, możesz szybciej rozpocząć pracę w programie Visual Studio Code, gdy znasz już definicje aplikacji logiki i chcesz pracować bezpośrednio w kodzie. Na przykład możesz wyłączyć, włączyć, usunąć i odświeżyć już utworzone aplikacje logiki. Ponadto możesz pracować nad aplikacjami logiki i kontami integracji z dowolnej platformy deweloperską, na której działa Visual Studio Code, takich jak Linux, Windows i Mac.

W tym artykule możesz utworzyć tę samą aplikację logiki z tego [przewodnika](../logic-apps/quickstart-create-first-logic-app-workflow.md)Szybki start, który koncentruje się bardziej na podstawowych pojęciach. Możesz również dowiedzieć [się,](quickstart-create-logic-apps-with-visual-studio.md)jak utworzyć przykładną aplikację w usłudze Visual Studio oraz jak tworzyć aplikacje i zarządzać nimi za pomocą interfejsu [azure Command-Line Interface (interfejs](quickstart-logic-apps-azure-cli.md)wiersza polecenia platformy Azure). W Visual Studio Code aplikacja logiki wygląda następująco:

![Przykładowa definicja przepływu pracy aplikacji logiki](./media/quickstart-create-logic-apps-visual-studio-code/visual-studio-code-overview.png)

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że masz następujące elementy:

* Jeśli nie masz konta i subskrypcji platformy Azure, zarejestruj się, aby [uzyskać bezpłatne konto platformy Azure.](https://azure.microsoft.com/free/)

* Podstawowa wiedza na [temat definicji przepływu pracy aplikacji logiki](../logic-apps/logic-apps-workflow-definition-language.md) i ich struktury zgodnie z opisem w danych JSON

  Jeśli jesteś nowym użytkownikom usługi Logic Apps, wypróbuj ten przewodnik Szybki start, który tworzy pierwsze aplikacje logiki w Azure Portal i koncentruje się bardziej na podstawowych pojęciach. [](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Dostęp do Internetu w celu logowania się do platformy Azure i subskrypcji platformy Azure

* Pobierz i zainstaluj te narzędzia, jeśli jeszcze ich nie masz:

  * [Visual Studio Code wersji 1.25.1](https://code.visualstudio.com/)lub nowszej , która jest bezpłatna

  * Visual Studio Code dla Azure Logic Apps

    To rozszerzenie można pobrać i zainstalować z Visual Studio Marketplace [bezpośrednio](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-logicapps) z Visual Studio Code. Pamiętaj, aby ponownie załadować Visual Studio Code instalacji.

    ![Znajdź "rozszerzenie Visual Studio Code dla Azure Logic Apps"](./media/quickstart-create-logic-apps-visual-studio-code/find-install-logic-apps-extension.png)

    Aby sprawdzić, czy rozszerzenie jest poprawnie zainstalowane, wybierz ikonę platformy Azure wyświetlaną na pasku Visual Studio Code narzędzi.

    ![Potwierdź, że rozszerzenie jest poprawnie zainstalowane](./media/quickstart-create-logic-apps-visual-studio-code/confirm-installed-visual-studio-code-extension.png)

    Aby uzyskać więcej informacji, zobacz [Extension Marketplace](https://code.visualstudio.com/docs/editor/extension-gallery). Aby współtworować wersję open source tego rozszerzenia, odwiedź stronę rozszerzenia Azure Logic Apps dla Visual Studio Code [w witrynie GitHub.](https://github.com/Microsoft/vscode-azurelogicapps)

* Jeśli aplikacja logiki musi komunikować się za pośrednictwem zapory, która ogranicza  ruch [](logic-apps-limits-and-config.md#inbound) do określonych adresów IP, musi zezwalać na dostęp zarówno dla przychodzących, jak i wychodzących adresów [IP](logic-apps-limits-and-config.md#outbound) używanych przez usługę Logic Apps lub środowisko uruchomieniowe w regionie świadczenia usługi Azure, w którym istnieje aplikacja logiki. Jeśli aplikacja logiki używa również łączników zarządzanych, takich jak łącznik office 365 Outlook lub łącznik  SQL, lub łączników niestandardowych, [](/connectors/custom-connectors/)zapora musi również zezwalać na dostęp dla wszystkich wychodzących adresów [IP](logic-apps-limits-and-config.md#outbound) łącznika zarządzanego w regionie platformy Azure aplikacji logiki. [](../connectors/managed.md)

<a name="access-azure"></a>

## <a name="access-azure-from-visual-studio"></a>Uzyskiwanie dostępu do platformy Azure z Visual Studio

1. Otwórz program Visual Studio Code. Na pasku Visual Studio Code wybierz ikonę platformy Azure.

   ![Wybieranie ikony platformy Azure na pasku Visual Studio Code narzędzi](./media/quickstart-create-logic-apps-visual-studio-code/open-extensions-visual-studio-code.png)

1. W oknie Azure w obszarze **Logic Apps** wybierz **pozycję Zaloguj się do platformy Azure.** Gdy zostanie wyświetlony monit na stronie logowania firmy Microsoft, zaloguj się przy użyciu konta platformy Azure.

   ![Wybierz pozycję "Zaloguj się do platformy Azure"](./media/quickstart-create-logic-apps-visual-studio-code/sign-in-azure-visual-studio-code.png)

   1. Jeśli logowanie trwa dłużej niż zwykle, Visual Studio Code monit o zalogowanie się za pośrednictwem witryny internetowej uwierzytelniania firmy Microsoft przez podanie kodu urządzenia. Aby zamiast tego zalogować się przy użyciu kodu, wybierz **pozycję Użyj kodu urządzenia.**

      ![Zamiast tego kontynuuj korzystanie z kodu urządzenia](./media/quickstart-create-logic-apps-visual-studio-code/use-device-code-prompt.png)

   1. Aby skopiować kod, wybierz pozycję **Kopiuj & Otwórz**.

      ![Kopiowanie kodu logowania do platformy Azure](./media/quickstart-create-logic-apps-visual-studio-code/sign-in-prompt-authentication.png)

   1. Aby otworzyć nowe okno przeglądarki i kontynuować witrynę internetową uwierzytelniania, wybierz **pozycję Otwórz link.**

      ![Potwierdź otwarcie przeglądarki i otwarcie witryny sieci Web uwierzytelniania](./media/quickstart-create-logic-apps-visual-studio-code/confirm-open-link.png)

   1. Na **stronie Zaloguj się do swojego konta** wprowadź kod uwierzytelniania, a następnie wybierz pozycję **Dalej.**

      ![Wprowadź kod uwierzytelniania logowania do platformy Azure](./media/quickstart-create-logic-apps-visual-studio-code/authentication-code-azure-sign-in.png)

1. Wybierz konto platformy Azure. Po zalogowaniu się możesz zamknąć przeglądarkę i wrócić do Visual Studio Code.

   W okienku Azure w **sekcjach Logic Apps** i **Konta** integracji są teraz wyświetlane subskrypcje platformy Azure skojarzone z Twoim kontem. Jeśli jednak nie widzisz spodziewanych subskrypcji lub jeśli w sekcjach jest zbyt wiele subskrypcji, wykonaj następujące kroki:

   1. Przesuń wskaźnik na **etykietę Logic Apps** danych. Gdy pojawi się pasek narzędzi, wybierz **pozycję Wybierz subskrypcje** (ikona filtru).

      ![Znajdowanie lub filtrowanie subskrypcji platformy Azure](./media/quickstart-create-logic-apps-visual-studio-code/find-or-filter-subscriptions.png)

   1. Z wyświetlonej listy wybierz subskrypcje, które chcesz wyświetlić.

1. W **Logic Apps** wybierz subskrypcję, której potrzebujesz. Węzeł subskrypcji rozszerza się i pokazuje wszystkie aplikacje logiki, które istnieją w tej subskrypcji.

   ![Wybierz subskrypcję platformy Azure](./media/quickstart-create-logic-apps-visual-studio-code/select-azure-subscription.png)

   > [!TIP]
   > W **obszarze Konta** integracji wybranie subskrypcji pokazuje wszystkie konta integracji istniejące w tej subskrypcji.

<a name="create-logic-app"></a>

## <a name="create-new-logic-app"></a>Tworzenie nowej aplikacji logiki

1. Jeśli jeszcze nie zalogowano się do konta i subskrypcji platformy Azure z Visual Studio Code, wykonaj poprzednie kroki, aby [się teraz zalogować.](#access-azure)

1. W Visual Studio Code obszarze **Logic Apps** otwórz menu skrótów subskrypcji, a następnie wybierz **pozycję Utwórz aplikację logiki.**

   ![W menu subskrypcji wybierz pozycję "Utwórz aplikację logiki"](./media/quickstart-create-logic-apps-visual-studio-code/create-logic-app-visual-studio-code.png)

   Zostanie wyświetlona lista wszystkich grup zasobów platformy Azure w ramach subskrypcji.

1. Z listy grupy zasobów wybierz pozycję **Utwórz nową grupę zasobów** lub istniejącą grupę zasobów. W tym przykładzie utwórz nową grupę zasobów.

   ![Tworzenie nowej grupy zasobów platformy Azure](./media/quickstart-create-logic-apps-visual-studio-code/select-or-create-azure-resource-group.png)

1. Podaj nazwę grupy zasobów platformy Azure i naciśnij klawisz ENTER.

   ![Podaj nazwę grupy zasobów platformy Azure](./media/quickstart-create-logic-apps-visual-studio-code/enter-name-resource-group.png)

1. Wybierz region świadczenia usługi Azure, w którym chcesz zapisać metadane aplikacji logiki.

   ![Wybieranie lokalizacji platformy Azure w celu zapisywania metadanych aplikacji logiki](./media/quickstart-create-logic-apps-visual-studio-code/select-azure-location-new-resources.png)

1. Podaj nazwę aplikacji logiki i naciśnij klawisz Enter.

   ![Podaj nazwę aplikacji logiki](./media/quickstart-create-logic-apps-visual-studio-code/enter-name-logic-app.png)

   W oknie Azure w obszarze subskrypcji platformy Azure zostanie wyświetlona nowa i pusta aplikacja logiki. Visual Studio Code otwiera również plik JSON (.logicapp.json), który zawiera szkieletową definicję przepływu pracy dla aplikacji logiki. Teraz możesz rozpocząć ręczne tworzenie definicji przepływu pracy aplikacji logiki w tym pliku JSON. Aby uzyskać informacje techniczne dotyczące struktury i składni definicji przepływu pracy, zobacz schemat języka definicji przepływu pracy dla Azure Logic Apps [.](../logic-apps/logic-apps-workflow-definition-language.md)

   ![Pusty plik JSON definicji przepływu pracy aplikacji logiki](./media/quickstart-create-logic-apps-visual-studio-code/empty-logic-app-workflow-definition.png)

   Na przykład poniżej znajduje się przykładowa definicja przepływu pracy aplikacji logiki, która rozpoczyna się od wyzwalacza RSS i akcji programu Office 365 Outlook. Zazwyczaj elementy JSON są wyświetlane alfabetycznie w każdej sekcji. Jednak w tym przykładzie te elementy są wyświetlane mniej więcej w kolejności, w których kroki aplikacji logiki są wyświetlane w projektancie.

   > [!IMPORTANT]
   > Jeśli chcesz ponownie użyć tej przykładowej definicji aplikacji logiki, potrzebujesz konta organizacyjnego, na przykład @fabrikam.com . Pamiętaj, aby zastąpić fikcyjny adres e-mail własnym adresem e-mail. Aby użyć innego łącznika poczty e-mail, takiego jak Outlook.com lub Gmail, zastąp akcję podobną akcją dostępną w łączniku poczty e-mail obsługiwanym przez `Send_an_email_action` [usługę Azure Logic Apps](../connectors/apis-list.md).
   >
   > Jeśli chcesz używać łącznika usługi Gmail, tylko konta firmowe usługi G Suite mogą używać tego łącznika bez ograniczeń w aplikacjach logiki. 
   > Jeśli masz konto użytkownika usługi Gmail, możesz użyć tego łącznika tylko z określonymi usługami zatwierdzonymi przez Google lub utworzyć aplikację klieniencową Google do uwierzytelniania za pomocą łącznika [usługi Gmail.](/connectors/gmail/#authentication-and-bring-your-own-application) 
   > Aby uzyskać więcej informacji, zobacz [Zasady zabezpieczeń danych i ochrony prywatności dla łączników Google](../connectors/connectors-google-data-security-privacy-policy.md)w Azure Logic Apps .

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

1. Gdy wszystko będzie gotowe, zapisz definicję przepływu pracy aplikacji logiki. (Menu Plik > zapisz lub naciśnij klawisze Ctrl+S)

1. Po wyświetleniu monitu o przekazanie aplikacji logiki do subskrypcji platformy Azure wybierz pozycję **Przekaż**.

   Ten krok powoduje opublikowanie aplikacji logiki w Azure Portal [,](https://portal.azure.com)co sprawia, że logika działa i działa na platformie Azure.

   ![Przekazywanie nowej aplikacji logiki do subskrypcji platformy Azure](./media/quickstart-create-logic-apps-visual-studio-code/upload-new-logic-app.png)

## <a name="view-logic-app-in-designer"></a>Wyświetlanie aplikacji logiki w projektancie

W Visual Studio Code możesz otworzyć aplikację logiki w widoku projektu tylko do odczytu. Mimo że nie można edytować aplikacji logiki w projektancie, możesz wizualnie sprawdzić przepływ pracy aplikacji logiki przy użyciu widoku projektanta.

W oknie platformy Azure w **Logic Apps** otwórz menu skrótów aplikacji logiki, a następnie wybierz **pozycję Otwórz w projektancie**.

Projektant tylko do odczytu zostanie otwarty w osobnym oknie i pokaże przepływ pracy aplikacji logiki, na przykład:

![Wyświetlanie aplikacji logiki w projektancie tylko do odczytu](./media/quickstart-create-logic-apps-visual-studio-code/logic-app-designer-view.png)

## <a name="view-in-azure-portal"></a>Wyświetlanie w witrynie Azure Portal

Aby przejrzeć aplikację logiki w Azure Portal, wykonaj następujące kroki:

1. Zaloguj się do [Azure Portal](https://portal.azure.com) przy użyciu tego samego konta platformy Azure i subskrypcji, które są skojarzone z Twoją aplikacją logiki.

1. W Azure Portal wyszukiwania aplikacji logiki wprowadź nazwę aplikacji logiki. Z listy wyników wybierz aplikację logiki.

   ![Twoja nowa aplikacja logiki w Azure Portal](./media/quickstart-create-logic-apps-visual-studio-code/published-logic-app-in-azure.png)

<a name="disable-enable-logic-app"></a>

## <a name="disable-or-enable-logic-app"></a>Wyłączanie lub włączanie aplikacji logiki

Jeśli Visual Studio Code opublikowaną aplikację logiki i zapiszesz zmiany,  zastąpisz już wdrożoną aplikację. Aby uniknąć przerwania działania aplikacji logiki w środowisku produkcyjnym i zminimalizować zakłócenia, najpierw zdezaktywuj aplikację logiki. Następnie możesz reaktywować aplikację logiki po potwierdzeniu, że aplikacja logiki nadal działa.

1. Jeśli jeszcze nie zalogowano się do konta i subskrypcji platformy Azure z Visual Studio Code, wykonaj poprzednie kroki, aby [się teraz zalogować.](#access-azure)

1. W oknie Azure w **Logic Apps** rozwiń subskrypcję platformy Azure, aby wyświetlić wszystkie aplikacje logiki w tej subskrypcji.

   1. Aby wyłączyć aplikację logiki, otwórz menu aplikacji logiki i wybierz pozycję **Wyłącz.**

      ![Wyłączanie aplikacji logiki](./media/quickstart-create-logic-apps-visual-studio-code/disable-published-logic-app.png)

   1. Gdy wszystko będzie gotowe do ponownej aktywacji aplikacji logiki, otwórz menu aplikacji logiki i wybierz pozycję **Włącz**.

      ![Włączanie aplikacji logiki](./media/quickstart-create-logic-apps-visual-studio-code/enable-published-logic-app.png)

<a name="edit-logic-app"></a>

## <a name="edit-deployed-logic-app"></a>Edytowanie wdrożonej aplikacji logiki

W Visual Studio Code można otworzyć i edytować definicję przepływu pracy dla już wdrożonej aplikacji logiki na platformie Azure.

> [!IMPORTANT] 
> Przed rozpoczęciem edytowania aktywnie uruchomionej aplikacji logiki w środowisku produkcyjnym unikaj ryzyka związanego z przerywaniem działania tej aplikacji logiki i minimalizuj zakłócenia, wyłączając najpierw aplikację [logiki.](#disable-enable-logic-app)

1. Jeśli jeszcze nie zalogowano się do konta i subskrypcji platformy Azure z Visual Studio Code, wykonaj poprzednie kroki, aby [się teraz zalogować.](#access-azure)

1. W oknie Azure w **Logic Apps** rozwiń subskrypcję platformy Azure i wybierz aplikację logiki.

1. Otwórz menu aplikacji logiki i wybierz pozycję **Otwórz w edytorze.** Lub obok nazwy aplikacji logiki wybierz ikonę edycji.

   ![Otwieranie edytora dla istniejącej aplikacji logiki](./media/quickstart-create-logic-apps-visual-studio-code/open-editor-existing-logic-app.png)

   Visual Studio Code plik .logicapp.jsw lokalnym folderze tymczasowym, aby można było wyświetlić definicję przepływu pracy aplikacji logiki.

   ![Wyświetlanie definicji przepływu pracy dla opublikowanej aplikacji logiki](./media/quickstart-create-logic-apps-visual-studio-code/edit-published-logic-app-workflow-definition.png)

1. Wprowadzaj zmiany w definicji przepływu pracy aplikacji logiki.

1. Gdy wszystko będzie gotowe, zapisz zmiany. (Menu Plik > Zapisz lub naciśnij klawisze Ctrl+S)

1. Po wyświetleniu monitu o przekazanie  zmian i zastąpienie istniejącej aplikacji logiki w Azure Portal wybierz **pozycję Przekaż**.

   Ten krok umożliwia opublikowanie aktualizacji aplikacji logiki w Azure Portal [.](https://portal.azure.com)

   ![Przekazywanie zmian do definicji aplikacji logiki na platformie Azure](./media/quickstart-create-logic-apps-visual-studio-code/upload-logic-app-changes.png)

## <a name="view-or-promote-other-versions"></a>Wyświetlanie lub podniesienie innych wersji

W Visual Studio Code można otwierać i przeglądać wcześniejsze wersje aplikacji logiki. Możesz również promować wcześniejszą wersję do bieżącej wersji.

> [!IMPORTANT] 
> Zanim zmienisz aktywnie uruchamianą aplikację logiki w środowisku produkcyjnym, unikaj ryzyka związanego z przerwaniem działania tej aplikacji logiki i zminimalizuj zakłócenia, wyłączając najpierw aplikację [logiki.](#disable-enable-logic-app)

1. W oknie Azure w **Logic Apps** rozwiń subskrypcję platformy Azure, aby wyświetlić wszystkie aplikacje logiki w tej subskrypcji.

1. W ramach subskrypcji rozwiń aplikację logiki i rozwiń w obszarze **Wersje**.

   Lista **Wersje** zawiera wcześniejsze wersje aplikacji logiki, jeśli istnieją.

   ![Poprzednie wersje aplikacji logiki](./media/quickstart-create-logic-apps-visual-studio-code/view-previous-versions.png)

1. Aby wyświetlić wcześniejszą wersję, wybierz jeden z kroków:

   * Aby wyświetlić definicję JSON, w **obszarze Wersje** wybierz numer wersji dla tej definicji. Możesz też otworzyć menu skrótów tej wersji i wybrać **pozycję Otwórz w edytorze**.

     Na komputerze lokalnym zostanie otwarty nowy plik z definicją JSON tej wersji.

   * Aby wyświetlić wersję w widoku projektanta tylko do odczytu, otwórz menu skrótów tej wersji i wybierz **pozycję Otwórz w projektancie**.

1. Aby promować wcześniejszą wersję do bieżącej wersji, wykonaj następujące kroki:

   1. W **obszarze** Wersje otwórz menu skrótów starszej wersji, a następnie wybierz pozycję **Podniesiej .**

      ![Podniesienie starszej wersji](./media/quickstart-create-logic-apps-visual-studio-code/promote-earlier-version.png)

   1. Aby kontynuować po Visual Studio Code monitu o potwierdzenie, wybierz pozycję **Tak.**

      ![Potwierdzanie promowania starszej wersji](./media/quickstart-create-logic-apps-visual-studio-code/confirm-promote-version.png)

      Visual Studio Code podniesie wybraną wersję do bieżącej wersji i przypisze nowy numer do wersji, do których poziom został promowany. Wcześniej bieżąca wersja jest teraz wyświetlana w obszarze wersji, która została promowana.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Tworzenie stanowych i bez stanowych aplikacji logiki w Visual Studio Code (wersja zapoznawcza)](../logic-apps/create-stateful-stateless-workflows-visual-studio-code.md)
