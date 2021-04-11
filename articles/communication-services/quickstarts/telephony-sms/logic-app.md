---
title: Szybki Start — wysyłanie wiadomości SMS w Azure Logic Apps przy użyciu usług Azure Communications Services
titleSuffix: An Azure Communication Services quickstart
description: W tym przewodniku szybki start dowiesz się, jak wysyłać wiadomości SMS w przepływach pracy Azure Logic Apps przy użyciu łącznika usługi Azure Communications Services.
author: tophpalmer
manager: anvalent
services: azure-communication-services
ms.author: chpalm
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 11cce223a0802c20c5fc144eed681327f989a949
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103488384"
---
# <a name="quickstart-send-sms-messages-in-azure-logic-apps-with-azure-communication-services"></a>Szybki Start: wysyłanie wiadomości SMS w Azure Logic Apps przy użyciu usług Azure Communications Services

Korzystając z łącznika [SMS usługi Azure Communication Services](../../overview.md) i [Azure Logic Apps](../../../logic-apps/logic-apps-overview.md), można tworzyć zautomatyzowane przepływy pracy lub *Aplikacje logiki*, które mogą wysyłać wiadomości SMS. W tym przewodniku szybki start pokazano, jak automatycznie wysyłać wiadomości SMS w odpowiedzi na zdarzenie wyzwalacza, czyli pierwszy krok w przepływie pracy aplikacji logiki. Zdarzenie wyzwalacza może być przychodzącą wiadomością e-mail, harmonogramem cyklu, [Azure Event Grid](../../../event-grid/overview.md) zdarzeniem zasobu lub innym [wyzwalaczem, który jest obsługiwany przez Azure Logic Apps](/connectors/connector-reference/connector-reference-logicapps-connectors).

:::image type="content" source="./media/logic-app/azure-communication-services-connector.png" alt-text="Zrzut ekranu pokazujący Azure Portal, który jest otwarty w Projektancie aplikacji logiki, i pokazuje przykładową aplikację logiki, która używa akcji Wyślij wiadomość SMS dla łącznika usługi Azure Communications Services.":::

Mimo że ten przewodnik Szybki Start koncentruje się na używaniu łącznika do reagowania na wyzwalacz, możesz również użyć łącznika, aby odpowiedzieć na inne akcje, które są czynnościami, które obserwują wyzwalacz w przepływie pracy. Jeśli dopiero zaczynasz Logic Apps, zapoznaj [się z tematem Azure Logic Apps](../../../logic-apps/logic-apps-overview.md) przed rozpoczęciem pracy.

> [!NOTE]
> W ramach tego przewodnika Szybki Start powiąże się niewielką opłatą za kilka centów USD lub mniej na koncie platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją lub [Utwórz bezpłatne konto platformy Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Aktywny zasób usługi Azure Communications Services lub [Tworzenie zasobu usług komunikacyjnych](../create-communication-resource.md).

- Aktywny zasób Logic Apps (aplikacja logiki) lub [Utwórz pustą aplikację logiki, ale z wyzwalaczem, którego chcesz użyć](../../../logic-apps/quickstart-create-first-logic-app-workflow.md). Obecnie łącznik programu SMS usługi Azure Communications Services zapewnia tylko akcje, więc aplikacja logiki wymaga co najmniej wyzwalacza.

  Ten przewodnik Szybki Start używa wyzwalacza **po nadejściu nowej wiadomości e-mail** , który jest dostępny dla [łącznika Office 365 Outlook Connector](/connectors/office365/).

- Numer telefonu z włączoną obsługą wiadomości SMS lub [Uzyskaj numer telefonu](./get-phone-number.md).

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

## <a name="add-an-sms-action"></a>Dodaj akcję SMS

Aby dodać akcję **Wyślij wiadomość SMS** jako nowy krok w przepływie pracy przy użyciu łącznika programu SMS usługi Azure Communications Services, wykonaj następujące kroki w [Azure Portal](https://portal.azure.com) za pomocą przepływu pracy aplikacji logiki, który jest otwarty w Projektancie aplikacji logiki:

1. W projektancie, w obszarze kroku, w którym chcesz dodać nową akcję, wybierz pozycję **nowy krok**. Alternatywnie, aby dodać nową akcję między krokami, przesuń wskaźnik myszy nad strzałkę między tymi krokami, wybierz znak plus ( **+** ), a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania **Wybierz operację** wprowadź wartość `Azure Communication Services` . Z listy Akcje wybierz pozycję **Wyślij wiadomość SMS**.

   :::image type="content" source="./media/logic-app/select-send-sms-action.png" alt-text="Zrzut ekranu przedstawiający projektanta aplikacji logiki i łącznik usługi Azure Communications Services z wybraną akcją Wyślij wiadomość SMS.":::

1. Teraz Utwórz połączenie z zasobem usług komunikacyjnych.

   1. Podaj nazwę połączenia.

   1. Wybierz zasób usługi Azure Communications Services.

   1. Wybierz przycisk **Utwórz**.

   :::image type="content" source="./media/logic-app/send-sms-configuration.png" alt-text="Zrzut ekranu pokazujący konfigurację akcji wysyłania SMS z przykładowymi informacjami.":::

1. W akcji **Wyślij wiadomość SMS** podaj następujące informacje: 

   * Źródłowe i docelowe numery telefonów. Do celów testowych możesz użyć własnego numeru telefonu jako docelowego numeru telefonu.

   * Zawartość wiadomości, która ma zostać wysłana, na przykład "Hello from Logic Apps!".

   Oto Akcja **wysyłania wiadomości SMS** z przykładowymi informacjami:

   :::image type="content" source="./media/logic-app/send-sms-action.png" alt-text="Zrzut ekranu pokazujący akcję Wyślij wiadomość SMS z przykładowymi informacjami.":::

1. Gdy skończysz, na pasku narzędzi projektanta wybierz pozycję **Zapisz**.

Następnie uruchom aplikację logiki w celu przetestowania.

## <a name="test-your-logic-app"></a>Testowanie aplikacji logiki

Aby ręcznie uruchomić aplikację logiki, na pasku narzędzi projektanta wybierz pozycję **Uruchom**. Możesz też poczekać, aż aplikacja logiki zostanie wyzwolona. W obu przypadkach aplikacja logiki powinna wysłać wiadomość SMS na określony docelowy numer telefonu. Aby uzyskać więcej informacji na temat uruchamiania aplikacji logiki, zapoznaj [się z tematem jak uruchomić aplikację logiki](../../../logic-apps/quickstart-create-first-logic-app-workflow.md#run-your-logic-app)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby usunąć subskrypcję usług komunikacyjnych, Usuń zasób lub grupę zasobów usług komunikacyjnych. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich innych zasobów w tej grupie. Aby uzyskać więcej informacji, zobacz [Jak oczyścić zasoby usług komunikacyjnych](../create-communication-resource.md#clean-up-resources).

Aby wyczyścić przepływ pracy aplikacji logiki i powiązane zasoby, zapoznaj [się z tematem czyszczenie Logic Apps zasobów](../../../logic-apps/quickstart-create-first-logic-app-workflow.md#clean-up-resources).

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start przedstawiono sposób wysyłania wiadomości SMS przy użyciu Azure Logic Apps i usług Azure Communications Services. Aby dowiedzieć się więcej, Kontynuuj subskrybowanie zdarzeń programu SMS:

> [!div class="nextstepaction"]
> [Subskrybowanie zdarzeń SMS](./handle-sms-events.md)

Aby uzyskać więcej informacji na temat programu SMS w usłudze Azure Communications Services, zobacz następujące artykuły:

- [Pojęcia dotyczące wiadomości SMS](../../concepts/telephony-sms/concepts.md)
- [Typy numerów telefonów](../../concepts/telephony-sms/plan-solution.md)
- [ZESTAW SDK PROGRAMU SMS](../../concepts/telephony-sms/sdk-features.md)
