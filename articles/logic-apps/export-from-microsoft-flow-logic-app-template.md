---
title: Eksportuj przepływy z automatyzacji, aby Azure Logic Apps
description: Migrowanie przepływów z poziomu automatyzacji do Azure Logic Apps przez eksportowanie jako Azure Resource Manager szablonów
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 616f10b32d0a9c1a05d759a0e27550cd2808808b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75428881"
---
# <a name="export-flows-from-power-automate-and-deploy-to-azure-logic-apps"></a>Eksportowanie przepływów z usługi Power Automate i wdrażanie w usłudze Azure Logic Apps

Aby rozszerzać i rozszerzać możliwości przepływu, można migrować ten przepływ z [automatyzacji](https://flow.microsoft.com) , aby [Azure Logic Apps](../logic-apps/logic-apps-overview.md). Możesz wyeksportować przepływ jako szablon Azure Resource Manager dla aplikacji logiki, wdrożyć ten szablon aplikacji logiki w grupie zasobów platformy Azure, a następnie otworzyć tę aplikację logiki w Projektancie aplikacji logiki.

> [!NOTE]
> Nie wszystkie łączniki automatyzacji oszczędzają energię są dostępne w Azure Logic Apps. Można importować przepływy, które mają [równoważne łączniki](../connectors/apis-list.md) w Azure Logic Apps. Na przykład wyzwalacz przycisku, łącznik zatwierdzania i łącznik powiadomień są specyficzne dla automatyzacji.
>
> Przepływy oparte na OpenAPIach wyeksportowane z poziomu automatyzacji nie są obecnie obsługiwane w przypadku wdrożeń jako szablonów aplikacji logiki. 

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Przepływ, który ma zostać wyeksportowany z automatyzacji

## <a name="export-a-flow"></a>Eksportowanie przepływu

1. Zaloguj się w usłudze [Automatyzacja](https://flow.microsoft.com)i wybierz pozycję **Moje przepływy**. Znajdź i wybierz swój przepływ. Na pasku narzędzi wybierz przycisk wielokropka (**...**). Wybierz pozycję **Eksportuj** > **szablon Logic Apps (JSON)**.

   ![Eksportowanie przepływu](./media/export-from-microsoft-flow-logic-app-template/export-flow.png)

1. Zapisz szablon w pożądanej lokalizacji.

Aby uzyskać więcej informacji, zobacz [zwiększanie do Azure Logic Apps](https://flow.microsoft.com/blog/grow-up-to-logic-apps/).

## <a name="deploy-template-by-using-the-azure-portal"></a>Wdróż szablon przy użyciu Azure Portal

1. Zaloguj się [Azure Portal](https://portal.azure.com) przy użyciu konta platformy Azure.

1. Z głównego menu platformy Azure wybierz pozycję **Utwórz zasób**. W polu wyszukiwania wprowadź "wdrożenie szablonu". Wybierz pozycję **Template Deployment (Wdróż przy użyciu szablonów niestandardowych)**, a następnie wybierz pozycję **Utwórz**.

   ![Wybierz pozycję "Template deployment"](./media/export-from-microsoft-flow-logic-app-template/select-template-deployment.png)

1. W obszarze **wdrażanie niestandardowe**wybierz opcję **Kompiluj własny szablon w edytorze**.

   ![Wybierz pozycję "Kompiluj własny szablon w edytorze"](./media/export-from-microsoft-flow-logic-app-template/build-template-in-editor.png)

1. Na pasku narzędzi **edytowanie szablonu** wybierz pozycję **Załaduj plik**. Znajdź i wybierz szablon JSON wyeksportowany z usługi Automatyzacja i wybierz pozycję **Otwórz**.

   ![Wybierz pozycję "Załaduj plik"](./media/export-from-microsoft-flow-logic-app-template/load-file.png)

1. Gdy w edytorze zostanie wyświetlony kod JSON, parametry i zasoby w szablonie, wybierz pozycję **Zapisz**.
  
   ![Zapisywanie szablonu](./media/export-from-microsoft-flow-logic-app-template/save-template.png)

1. Teraz określ następujące parametry wejściowe dla szablonu:

   * Subskrypcja platformy Azure do użycia na potrzeby rozliczeń
   * Grupa zasobów platformy Azure
   * Lokalizacja grupy zasobów platformy Azure
   * Nazwa zasobu aplikacji logiki
   * Lokalizacja zasobu aplikacji logiki, jeśli jest inna niż grupa zasobów platformy Azure
   * Nazwa dla wszystkich utworzonych wcześniej połączeń, które mogą być używane przez aplikację logiki

      W przypadku tworzenia pierwszej aplikacji logiki wszystkie połączenia są tworzone jako nowe, więc można zaakceptować domyślne nazwy. W przeciwnym razie można określić nazwy dla wcześniej utworzonych połączeń, które mogą być używane w wielu aplikacjach logiki.

   Po podaniu tych informacji dla szablonu zapoznaj się z tematem i zaakceptuj warunki i postanowienia dotyczące usługi Azure Marketplace dotyczące tworzenia niezbędnych zasobów platformy Azure i rozliczania subskrypcji platformy Azure zgodnie z potrzebami, a następnie wybierz pozycję **Kup**.
  
   ![Określ parametry wejściowe dla szablonu](./media/export-from-microsoft-flow-logic-app-template/template-input-parameters.png)

   Platforma Azure wdraża szablon jako aplikację logiki do określonej grupy zasobów. Wszystkie aplikacje logiki, które zostały zmigrowane z poziomu automatyzacji, są wdrażane w stanie wyłączenia.

1. Przed aktywowaniem aplikacji logiki Autoryzuj wszystkie nowe połączenia, wykonując następujące czynności:

   1. Otwórz utworzoną aplikację logiki. W menu aplikacji logiki wybierz pozycję **Projektant aplikacji logiki**.

      Każde połączenie, które wymaga autoryzacji, zawiera ikonę ostrzeżenia:

      ![Ikona ostrzeżenia](./media/export-from-microsoft-flow-logic-app-template/authorize-connections.png)

   1. Dla każdego kroku wymagającego autoryzowanego połączenia rozwiń ten krok, a następnie wybierz pozycję **Dodaj nowy**.

      ![Dodaj nowe połączenie](./media/export-from-microsoft-flow-logic-app-template/add-new-connection.png)

   1. Zaloguj się do każdej usługi lub podaj poświadczenia niezbędne do autoryzowania połączenia.

1. Zapisz aplikację logiki. Gdy wszystko będzie gotowe do aktywacji aplikacji logiki, w menu aplikacji logiki wybierz pozycję **Przegląd**, a następnie wybierz pozycję **Włącz**.

   ![Włącz aplikację logiki](./media/export-from-microsoft-flow-logic-app-template/enable-logic-app.png)

1. Aby uniknąć uruchamiania zduplikowanych przepływów pracy, należy się upewnić, że użytkownik dezaktywuje lub usunie oryginalny przepływ.

## <a name="deploy-template-by-using-visual-studio"></a>Wdrażanie szablonu przy użyciu programu Visual Studio

Jeśli skonfigurowano program Visual Studio z [wymaganiami wstępnymi](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md#prerequisites) dotyczącymi tworzenia aplikacji logiki, można wdrożyć wyeksportowany szablon z programu Visual Studio, aby Azure Logic Apps.

1. W programie Visual Studio Otwórz plik szablonu wyeksportowany z poziomu usługi Automatyzacja.

1. W programie Visual Studio Utwórz projekt grupy zasobów platformy Azure i wybierz szablon **aplikacji logiki** , wykonując czynności opisane w [przewodniku szybki start: Tworzenie zautomatyzowanych zadań, procesów i przepływów pracy przy użyciu programu Azure Logic Apps-Visual Studio](../logic-apps/quickstart-create-logic-apps-with-visual-studio.md), na przykład:

   ![Tworzenie projektu grupy zasobów platformy Azure](./media/export-from-microsoft-flow-logic-app-template/create-azure-resource-group-project.png)

1. W Eksplorator rozwiązań otwórz plik **LogicApp. JSON** , jeśli plik nie jest jeszcze otwarty.

1. Skopiuj zawartość z wyeksportowanego szablonu i Zastąp jej zawartość w pliku **LogicApp. JSON** .

1. Przed wdrożeniem aplikacji logiki Autoryzuj nowe połączenia, wykonując następujące czynności:

   1. Otwórz menu skrótów **LogicApp. JSON** , a następnie wybierz polecenie **Otwórz za pomocą projektanta aplikacji logiki**.

      ![Otwieranie szablonu przy użyciu projektanta aplikacji logiki](./media/export-from-microsoft-flow-logic-app-template/open-logic-app-designer.png)

   1. Jeśli zostanie wyświetlony monit, wybierz subskrypcję platformy Azure i grupę zasobów, której chcesz użyć do wdrożenia aplikacji logiki.

      ![Wybierz subskrypcję platformy Azure i grupę zasobów](./media/export-from-microsoft-flow-logic-app-template/select-azure-subscription-resource-group-deployment.png)

      Gdy aplikacja logiki zostanie wyświetlona w projektancie, wszystkie połączenia, które wymagają pokazywania ikon ostrzeżeń:

      ![Połączenia z ikonami ostrzeżeń](./media/export-from-microsoft-flow-logic-app-template/authorize-connections-vs.png)

   1. Dla każdego kroku wymagającego autoryzowanego połączenia rozwiń ten krok, a następnie wybierz pozycję **Dodaj nowy**.

      ![Dodaj nowe połączenie](./media/export-from-microsoft-flow-logic-app-template/add-new-connection-vs.png)

   1. Zaloguj się do każdej usługi lub podaj poświadczenia niezbędne do autoryzowania połączenia.

   1. Zapisz rozwiązanie przed wdrożeniem aplikacji logiki.

1. W Eksplorator rozwiązań otwórz menu skrótów projektu i wybierz polecenie **Wdróż** > **nowe**. Po wyświetleniu monitu zaloguj się przy użyciu konta platformy Azure.

1. Po wyświetleniu monitu potwierdź subskrypcję platformy Azure, grupę zasobów platformy Azure oraz inne ustawienia, które mają być używane do wdrażania, takie jak [plik parametrów](../azure-resource-manager/templates/parameter-files.md) , który ma być używany do przekazywania wartości parametrów szablonu, a następnie wybierz pozycję **Wdróż**.

   ![Potwierdź ustawienia wdrożenia](./media/export-from-microsoft-flow-logic-app-template/confirm-azure-subscription-resource-group-deployment.png)

1. Jeśli zostanie wyświetlone okno **Edytuj parametry** , podaj nazwę zasobu aplikacji logiki na platformie Azure i wybierz pozycję **Zapisz**.  

   ![Edytuj parametry wdrożenia](./media/export-from-microsoft-flow-logic-app-template/edit-parameters-deployment.png)

   Po rozpoczęciu wdrażania stan wdrożenia aplikacji zostanie wyświetlony w oknie **Dane wyjściowe** programu Visual Studio. Jeśli stan się nie pojawi, otwórz listę **Pokaż dane wyjściowe z** i wybierz grupę zasobów platformy Azure. Przykład:

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
