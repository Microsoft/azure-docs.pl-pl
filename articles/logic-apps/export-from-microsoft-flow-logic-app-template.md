---
title: Eksportuj przepływy z automatyzacji, aby Azure Logic Apps
description: Migrowanie przepływów z poziomu automatyzacji do Azure Logic Apps przez eksportowanie jako Azure Resource Manager szablonów
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, sneshaf, pinath, logicappspm
ms.topic: conceptual
ms.date: 01/20/2021
ms.openlocfilehash: a90f75db6961ea06b1cf9c2958556c1c2ef24805
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100380138"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>Eksportowanie przepływów z usługi Power Automate i wdrażanie w usłudze Azure Logic Apps

> W przypadku przepływów utworzonych po październiku 2020 nie można już wyeksportować z usługi Automatyzacja i wdrożyć do Azure Logic Apps.

Aby rozszerzać i rozszerzać możliwości przepływu, można migrować ten przepływ z [automatyzacji](https://flow.microsoft.com) , aby [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Możesz wyeksportować przepływ jako szablon Azure Resource Manager dla aplikacji logiki, wdrożyć ten szablon aplikacji logiki w grupie zasobów platformy Azure, a następnie otworzyć tę aplikację logiki w Projektancie aplikacji logiki.

> [!NOTE]
> Nie wszystkie łączniki automatyzacji oszczędzają energię są dostępne w Azure Logic Apps. Można migrować tylko przepływy automatyzacji, które mają równoważne łączniki w Azure Logic Apps. Na przykład wyzwalacz przycisku, łącznik zatwierdzania i łącznik powiadomień są specyficzne dla automatyzacji. Obecnie przepływy oparte na OpenAPI w programie do automatyzacji nie są obsługiwane w przypadku eksportowania i wdrażania jako szablonów aplikacji logiki.
>
> * Aby dowiedzieć się, które łączniki automatyzuje nie mają odpowiedników Logic Apps, zobacz [Łączniki automatyzacji](/connectors/connector-reference/connector-reference-powerautomate-connectors).
>
> * Aby dowiedzieć się, które łączniki Logic Apps nie mają odpowiedników, zobacz [Logic Apps łączników](/connectors/connector-reference/connector-reference-logicapps-connectors).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Przepływ, który ma zostać wyeksportowany z automatyzacji

## <a name="export-your-flow"></a>Eksportowanie przepływu

1. Zaloguj się w usłudze [Automatyzacja](https://flow.microsoft.com)i wybierz pozycję **Moje przepływy**. Znajdź i wybierz swój przepływ. Na pasku narzędzi wybierz przycisk wielokropka (**...**) > **Eksportuj**  >  **szablon Logic Apps (JSON)**.

   ![Eksportowanie przepływu z automatyzacji](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. Zapisz plik JSON szablonu w żądanym miejscu.

Aby uzyskać więcej informacji, zobacz [zwiększanie do Azure Logic Apps](https://flow.microsoft.com/blog/grow-up-to-logic-apps/).

## <a name="deploy-template-by-using-the-azure-portal"></a>Wdróż szablon przy użyciu Azure Portal

1. Zaloguj się [Azure Portal](https://portal.azure.com) przy użyciu konta platformy Azure.

1. Na stronie głównej platformy Azure w polu wyszukiwania wprowadź ciąg `custom template` . Na podstawie wyników wybierz pozycję **Wdróż szablon niestandardowy**  >  **Utwórz**.

   ![Znajdź i wybierz pozycję "Template deployment"](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. W obszarze **wdrażanie niestandardowe** wybierz opcję **Kompiluj własny szablon w edytorze**.

   ![Wybierz pozycję "Kompiluj własny szablon w edytorze"](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. Na pasku narzędzi **edytowanie szablonu** wybierz pozycję **Załaduj plik**.

   ![Wybierz pozycję "Załaduj plik"](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. Przejdź do lokalizacji, w której zapisano plik szablonu JSON wyeksportowany z usługi Automatyzacja. Wybierz plik szablonu > **Otwórz**.

1. Gdy w edytorze zostanie wyświetlony kod JSON, parametry i zasoby w szablonie, wybierz pozycję **Zapisz**.

   ![Zapisywanie szablonu](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. Teraz podaj więcej informacji dotyczących aplikacji logiki.

   1. Wybierz lub określ wartości parametrów wejściowych dla szablonu.

      | Właściwość | Opis |
      |----------|-------------|
      | **Subskrypcja** | Subskrypcja platformy Azure do użycia na potrzeby rozliczeń |
      | **Grupa zasobów** | Grupa zasobów platformy Azure, która ma być używana przez aplikację logiki. Możesz użyć istniejącej grupy lub utworzyć nową grupę. |
      | **Lokalizacja** | Region świadczenia usługi Azure, który ma być używany w przypadku tworzenia nowej grupy zasobów |
      | **Nazwa aplikacji logiki** | Nazwa do użycia dla zasobu aplikacji logiki |
      | **Lokalizacja aplikacji logiki** | Region świadczenia usługi Azure, w którym chcesz utworzyć zasób aplikacji logiki, jeśli różni się od grupy zasobów platformy Azure |
      | <*Nazwa połączenia*> | Jedna lub wiele nazw dla wszystkich wcześniej utworzonych połączeń, które mogą być używane przez aplikację logiki <p><p>**Uwaga**: Jeśli ta aplikacja logiki jest w pierwszej kolejności, wszystkie połączenia są tworzone jako nowe, więc można zaakceptować domyślne nazwy. W przeciwnym razie można określić nazwy dla wcześniej utworzonych połączeń, które mogą być używane w wielu aplikacjach logiki. |
      |||

      Na przykład:

      ![Określ parametry wejściowe dla szablonu](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   1. Gdy skończysz, zapoznaj się z **warunkami i postanowieniami** dotyczącymi tworzenia niezbędnych zasobów platformy Azure i rozliczania subskrypcji platformy Azure zgodnie z potrzebami.

   1. Gdy wszystko będzie gotowe, wybierz pozycję **Wyrażam zgodę na warunki i postanowienia podane powyżej**  >  .

      Platforma Azure wdraża szablon jako aplikację logiki do określonej grupy zasobów.

1. Wszystkie aplikacje logiki, które zostały zmigrowane z poziomu automatyzacji, są wdrażane w stanie wyłączenia. Przed włączeniem aplikacji logiki Autoryzuj nowe połączenia, wykonując następujące czynności:

   1. W Azure Portal Otwórz utworzoną aplikację logiki. W menu aplikacji logiki wybierz pozycję **Projektant aplikacji logiki**.

      Każde połączenie, które wymaga autoryzacji, zawiera ikonę ostrzeżenia:

      ![Ikona ostrzeżenia](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. Dla każdego kroku wymagającego autoryzowanego połączenia rozwiń ten krok, a następnie wybierz pozycję **Dodaj nowy**.

      ![Zrzut ekranu pokazujący przycisk "Dodaj nowy" wybrany w oknie "połączenia" programu Outlook.](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. Zaloguj się do każdej usługi lub podaj poświadczenia niezbędne do autoryzowania połączenia.

   1. Po zaktualizowaniu połączeń na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

1. Gdy wszystko będzie gotowe do aktywacji aplikacji logiki, w menu aplikacji logiki wybierz pozycję **Przegląd**, a następnie wybierz pozycję **Włącz**.

   ![Włącz aplikację logiki](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. Aby uniknąć uruchamiania zduplikowanych przepływów pracy, należy się upewnić, że użytkownik dezaktywuje lub usunie oryginalny przepływ.

## <a name="deploy-template-by-using-visual-studio"></a>Wdrażanie szablonu przy użyciu programu Visual Studio

Jeśli skonfigurowano program Visual Studio z [wymaganiami wstępnymi](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites) dotyczącymi tworzenia aplikacji logiki, można wdrożyć wyeksportowany szablon do Azure Logic Apps przy użyciu programu Visual Studio.

1. W programie Visual Studio przejdź do pliku JSON dla szablonu aplikacji logiki, który został wyeksportowany z usługi Automatyzacja, i otwórz go.

1. W programie Visual Studio Utwórz projekt **grupy zasobów platformy Azure** , który używa szablonu **aplikacji logiki** , wykonując czynności opisane w [przewodniku szybki start: Tworzenie zautomatyzowanych zadań, procesów i przepływów pracy przy użyciu programu Azure Logic Apps-Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md).

   Ten przykład tworzy rozwiązanie programu Visual Studio o nazwie "ImportedLogicApp".

   ![Tworzenie projektu grupy zasobów platformy Azure](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. Po utworzeniu rozwiązania w Eksplorator rozwiązań Otwórz **LogicApp.js** pliku, jeśli plik nie jest jeszcze otwarty.

1. Skopiuj zawartość z wyeksportowanego szablonu i Zastąp zawartość w **LogicApp.js** pliku.

1. Przed wdrożeniem aplikacji logiki Autoryzuj nowe połączenia, wykonując następujące czynności:

   1. Otwórz **LogicApp.jsw** menu skrótów, a następnie wybierz polecenie **Otwórz za pomocą projektanta aplikacji logiki**.

      ![Otwieranie szablonu przy użyciu projektanta aplikacji logiki](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. Jeśli zostanie wyświetlony monit, wybierz subskrypcję platformy Azure i grupę zasobów, której chcesz użyć do wdrożenia aplikacji logiki.

      ![Wybierz subskrypcję platformy Azure i grupę zasobów](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      Gdy aplikacja logiki zostanie wyświetlona w projektancie, wszystkie połączenia, które wymagają pokazywania ikon ostrzeżeń:

      ![Połączenia z ikonami ostrzeżeń](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. Dla każdego kroku wymagającego autoryzowanego połączenia rozwiń ten krok, a następnie wybierz pozycję **Dodaj nowy**.

      ![Dodaj nowe połączenie](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. Zaloguj się do każdej usługi lub podaj poświadczenia niezbędne do autoryzowania połączenia.

   1. Zapisz rozwiązanie przed wdrożeniem aplikacji logiki.

1. W Eksplorator rozwiązań otwórz menu skrótów projektu i wybierz polecenie **Wdróż**  >  **nowe**. Po wyświetleniu monitu zaloguj się przy użyciu konta platformy Azure.

1. Po wyświetleniu monitu potwierdź subskrypcję platformy Azure, grupę zasobów platformy Azure oraz inne ustawienia, które mają być używane do wdrażania, takie jak [plik parametrów](../azure-resource-manager/templates/parameter-files.md) , który ma być używany do przekazywania wartości parametrów szablonu, a następnie wybierz pozycję **Wdróż**.

   ![Potwierdź ustawienia wdrożenia](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. Jeśli zostanie wyświetlone okno **Edytuj parametry** , podaj nazwę zasobu aplikacji logiki na platformie Azure i wybierz pozycję **Zapisz**.  

   ![Edytuj parametry wdrożenia](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   Po rozpoczęciu wdrażania stan wdrożenia aplikacji zostanie wyświetlony w oknie **Dane wyjściowe** programu Visual Studio. Jeśli stan się nie pojawi, otwórz listę **Pokaż dane wyjściowe z** i wybierz grupę zasobów platformy Azure. Na przykład:

   ![Okno wyniku](./media/export-from-microsoft-flow-logic-app-template/output-window.png)

   Jeśli wszystkie połączenia w aplikacji logiki wymagają danych wejściowych, okno programu PowerShell zostanie otwarte w tle i wyświetli monit o podanie wszelkich niezbędnych haseł lub kluczy tajnych. Po wprowadzeniu tych informacji wdrażanie będzie kontynuowane.

   ![Uwierzytelnianie połączeń](./media/export-from-microsoft-flow-logic-app-template/logic-apps-powershell-window.png)

   Po zakończeniu wdrażania aplikacja logiki zostanie opublikowana, ale nie zostanie aktywowana w Azure Portal.

1. Gdy wszystko będzie gotowe do aktywacji aplikacji logiki w Azure Portal, Znajdź i Otwórz aplikację logiki w Projektancie aplikacji logiki. W menu aplikacji logiki wybierz pozycję **Przegląd**, a następnie wybierz pozycję **Włącz**.

1. Aby uniknąć uruchamiania zduplikowanych przepływów pracy, należy się upewnić, że użytkownik dezaktywuje lub usunie oryginalny przepływ.

Aby uzyskać więcej informacji na temat tych kroków wdrażania, zobacz [Szybki Start: Tworzenie zautomatyzowanych zadań, procesów i przepływów pracy przy użyciu Azure Logic Apps — Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#deploy-to-Azure)

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej [na temat łączników dla Azure Logic Apps](../connectors/apis-list.md)
* Dowiedz się więcej o [Azure Logic Apps](../logic-apps/logic-apps-overview.md)
