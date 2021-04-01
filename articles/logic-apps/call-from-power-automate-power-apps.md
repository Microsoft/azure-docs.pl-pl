---
title: Wywoływanie aplikacji logiki z usług Power Automate i Power Apps
description: Wywołaj Aplikacje logiki z usługi Microsoft energooszczędne przepływy, eksportując Aplikacje logiki jako łączniki.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, logicappspm
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: b402dab4c6e94a7634e11f0330b5379315e43abf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91762476"
---
# <a name="call-logic-apps-from-power-automate-and-power-apps"></a>Wywoływanie aplikacji logiki z usług Power Automate i Power Apps

Aby wywoływać aplikacje logiki z usługi Microsoft PowerShell i Microsoft PowerShell, możesz wyeksportować Aplikacje logiki jako łączniki. Po udostępnieniu aplikacji logiki jako łącznika niestandardowego w środowisku automatyzacji lub aplikacji zaawansowanych można wywołać aplikację logiki z tam dostępnych przepływów.

Jeśli chcesz migrować przepływ z poziomu usługi Automatyzacja lub do Logic Apps, zobacz [Eksportowanie przepływów z usługi Automatyzacja i wdrażanie do Azure Logic Apps](export-from-microsoft-flow-logic-app-template.md).

> [!NOTE]
> Nie wszystkie łączniki automatyzacji oszczędzają energię są dostępne w Azure Logic Apps. Można migrować tylko przepływy automatyzacji, które mają równoważne łączniki w Azure Logic Apps. Na przykład wyzwalacz przycisku, łącznik zatwierdzania i łącznik powiadomień są specyficzne dla automatyzacji. Obecnie przepływy oparte na OpenAPI w programie do automatyzacji nie są obsługiwane w przypadku eksportowania i wdrażania jako szablonów aplikacji logiki.
>
> * Aby dowiedzieć się, które łączniki automatyzuje nie mają odpowiedników Logic Apps, zobacz [Łączniki automatyzacji](/connectors/connector-reference/connector-reference-powerautomate-connectors).
>
> * Aby dowiedzieć się, które łączniki Logic Apps nie mają odpowiedników, zobacz [Logic Apps łączników](/connectors/connector-reference/connector-reference-powerautomate-connectors).

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/).

* Licencja na automatyzację lub aplikacje dla aplikacji zaawansowanych.

* Aplikacja logiki z wyzwalaczem żądania do eksportowania.

* Przepływ w programie do automatyzacji lub przetwarzania aplikacji, z którego chcesz wywołać aplikację logiki.

## <a name="export-your-logic-app-as-a-custom-connector"></a>Eksportowanie aplikacji logiki jako łącznika niestandardowego

Aby można było wywołać aplikację logiki z poziomu automatyzacji lub aplikacji zaawansowanych, należy najpierw wyeksportować aplikację logiki jako łącznik niestandardowy.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W polu wyszukiwania Azure Portal wprowadź wartość `Logic Apps` . W wynikach w obszarze **usługi** wybierz pozycję **Logic Apps**.

1. Wybierz aplikację logiki, która ma zostać wyeksportowana.

1. Z menu aplikacji logiki wybierz pozycję **Eksportuj**.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/export-logic-app.png" alt-text="Zrzut ekranu strony aplikacji logiki w Azure Portal, wyświetlanie menu z wybranym przyciskiem &quot;Eksportuj&quot;.":::

1. W okienku **Eksportuj** w polu **Nazwa** wprowadź nazwę łącznika niestandardowego w aplikacji logiki. Z listy **środowisko** Wybierz środowisko automatyzacji lub aplikacji zaawansowanych, z którego chcesz wywołać aplikację logiki. Gdy skończysz, wybierz opcję **OK**.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/export-logic-app2.png" alt-text="Zrzut ekranu przedstawiający okienko eksportowania dla aplikacji logiki, w której wyświetlane są wymagane pola dla nazwy i środowiska łącznika niestandardowego.":::

1. Aby upewnić się, że aplikacja logiki została pomyślnie wyeksportowana, Sprawdź okienko powiadomienia.

### <a name="exporting-errors"></a>Eksportowanie błędów

Poniżej przedstawiono błędy, które mogą wystąpić podczas eksportowania aplikacji logiki jako łącznik niestandardowy i sugerowanych rozwiązań:

* **Nie można pobrać środowisk. Upewnij się, że Twoje konto zostało skonfigurowane do automatyzacji, a następnie spróbuj ponownie.** Sprawdź, czy Twoje konto platformy Azure ma plan automatyzacji.

* **Nie można wyeksportować bieżącej aplikacji logiki. Aby wyeksportować, wybierz aplikację logiki z wyzwalaczem żądania.**: Sprawdź, czy aplikacja logiki rozpoczyna się od [wyzwalacza żądania](./logic-apps-workflow-actions-triggers.md#request-trigger).

## <a name="connect-to-your-logic-app-from-power-automate"></a>Nawiązywanie połączenia z aplikacją logiki przy użyciu automatyzacji

Aby nawiązać połączenie z aplikacją logiki, która została wyeksportowana przy użyciu przepływu automatyzacji:

1. Zaloguj się do usługi [Power Automate](https://flow.microsoft.com).

1. Z menu strony głównej **automatyzacji zaawansowane** wybierz pozycję **Moje przepływy**.

1. Na stronie **przepływy** wybierz przepływ, który chcesz połączyć z aplikacją logiki.

1. Z menu strony przepływu wybierz pozycję **Edytuj**.

1. W edytorze przepływu wybierz pozycję **&#43; nowy krok**.

1. W obszarze **Wybierz akcję** w polu wyszukiwania wprowadź nazwę łącznika aplikacji logiki. Opcjonalnie, aby wyświetlić tylko łączniki niestandardowe w danym środowisku, przefiltrować wyniki, wybierając kartę **niestandardową** .

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/power-automate-custom-connector-action.png" alt-text="Zrzut ekranu przedstawiający Edytor przepływu automatyzacji, pokazujący nowy krok dodawany do łącznika niestandardowego i dostępne akcje.":::

1. Wybierz akcję, którą chcesz wykonać przy użyciu łącznika aplikacji logiki. 

1. Podaj informacje, które akcja przekazuje do łącznika aplikacji logiki.

1. Aby zapisać zmiany, w menu Edytor usługi Automatyzacja, wybierz pozycję **Zapisz**.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W usłudze Logic Apps Znajdź wyeksportowaną aplikację logiki.

1. Upewnij się, że aplikacja logiki działa w oczekiwany sposób w przepływie automatyzacji.

## <a name="delete-logic-app-connector-from-power-automate"></a>Usuwanie łącznika aplikacji logiki z automatyzacji

1. Zaloguj się do usługi [Power Automate](https://flow.microsoft.com).

1. Na stronie głównej narzędzia do **automatyzowania** wybierz pozycję **dane** &gt; **Niestandardowe Łączniki** w menu.

1. Znajdź łącznik niestandardowy na liście, a następnie wybierz przycisk wielokropka (**...**) &gt; .

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/delete-custom-connector.png" alt-text="Zrzut ekranu przedstawiający stronę &quot;łączniki niestandardowe&quot; z niestandardowymi przyciskami zarządzania łącznikami aplikacji logiki.":::

1. Aby potwierdzić usunięcie, wybierz **przycisk OK**.

## <a name="connect-to-your-logic-app-from-power-apps"></a>Nawiązywanie połączenia z aplikacją logiki z poziomu aplikacji zaawansowanych

Aby nawiązać połączenie z aplikacją logiki wyeksportowaną za pomocą przepływu aplikacji zaawansowanych:

1. Zaloguj się do usługi [Power Apps](https://powerapps.microsoft.com/).

1. Na stronie głównej **aplikacje zaawansowane** wybierz pozycję **przepływy** w menu.

1. Na stronie **przepływy** wybierz przepływ, który chcesz połączyć z aplikacją logiki.

1. Na stronie przepływu wybierz pozycję **Edytuj** w menu przepływu.

1. W edytorze przepływu wybierz przycisk **&#43; nowy krok** .

1. W obszarze **Wybierz akcję** w nowym kroku w polu wyszukiwania wprowadź nazwę łącznika aplikacji logiki. Opcjonalnie można filtrować wyniki według karty **niestandardowej** , aby wyświetlić tylko łączniki niestandardowe w danym środowisku.

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/power-apps-custom-connector-action.png" alt-text="Zrzut ekranu edytora przepływu aplikacji zaawansowanych, pokazujący nowy krok dodawany do łącznika niestandardowego i dostępne akcje.":::

1. Wybierz akcję, którą chcesz wykonać przy użyciu łącznika. 

1. Skonfiguruj informacje przekazywane przez akcję do łącznika aplikacji logiki.

1. W menu Edytor aplikacji zaawansowanych wybierz pozycję **Zapisz** , aby zapisać zmiany. 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W usłudze Logic Apps Znajdź wyeksportowaną aplikację logiki.

1. Upewnij się, że aplikacja logiki działa zgodnie z oczekiwaniami w ramach przepływu aplikacji zaawansowanych.

## <a name="delete-logic-app-connector-from-power-apps"></a>Usuwanie łącznika aplikacji logiki z aplikacji zaawansowanych

1. Zaloguj się do usługi [Power Apps](https://powerapps.microsoft.com).

1. Na stronie głównej **aplikacje zaawansowane** wybierz pozycję **dane** &gt; **Niestandardowe Łączniki** w menu.

1. Znajdź łącznik niestandardowy na liście, a następnie wybierz przycisk wielokropka (**...**) &gt; .

    :::image type="content" source="./media/call-logic-apps-from-power-automate-power-apps/delete-custom-connector.png" alt-text="Zrzut ekranu przedstawiający stronę łączniki niestandardowe aplikacji zaawansowanych — przyciski zarządzania łącznikami niestandardowymi aplikacji logiki.":::

1. Aby potwierdzić usunięcie, wybierz **przycisk OK**.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej [na temat łączników dla Azure Logic Apps](../connectors/apis-list.md)
* Dowiedz się więcej o [Azure Logic Apps](../logic-apps/logic-apps-overview.md)
