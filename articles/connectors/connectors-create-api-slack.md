---
title: Połącz z zapasem czasu z Azure Logic Apps
description: Automatyzuj zadania i przepływy pracy, które monitorują pliki i zarządzają kanałami, grupami i komunikatami w ramach konta zapasowego za pomocą Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 75f4cea0e5cd2b0bf42c28cbd81e199115834e11
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87283966"
---
# <a name="monitor-and-manage-slack-with-azure-logic-apps"></a>Monitoruj zapasy czasu i zarządzaj nimi za pomocą Azure Logic Apps

Korzystając z Azure Logic Apps i łącznika zapasowego, można tworzyć automatyczne zadania i przepływy pracy, które monitorują pliki zapasowe i zarządzać kanałami czasu, wiadomościami, grupami i tak dalej, na przykład:

* Monitoruj po utworzeniu nowych plików.
* Tworzenie, wyświetlanie i sprzęganie kanałów 
* Komunikaty post.
* Utwórz grupy i ustaw nie przeszkadzać.

Można użyć wyzwalaczy, które odbierają odpowiedzi z konta zapasowego i umożliwiają udostępnienie danych wyjściowych innym akcjom. Możesz użyć akcji wykonujących zadania z kontem zapasowym. Możesz również mieć inne akcje, używając danych wyjściowych z akcji zapasowych. Na przykład po utworzeniu nowego pliku można wysłać wiadomość e-mail za pomocą łącznika programu Outlook pakietu Office 365. Jeśli jesteś nowym sposobem logiki aplikacji, zapoznaj [się z tematem Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Konto [czasu](https://slack.com/) i poświadczenia użytkownika

  Twoje poświadczenia autoryzują aplikację logiki, aby utworzyć połączenie i uzyskać dostęp do konta zapasowego.

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, do której chcesz uzyskać dostęp do konta zapasowego. Aby rozpocząć pracę z wyzwalaczem zapasowym, [Utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji zapasowej, uruchom aplikację logiki z wyzwalaczem, takim jak wyzwalacz zapasowy lub inny wyzwalacz, taki jak wyzwalacz **cyklu** .

## <a name="connect-to-slack"></a>Połącz ze zapasem czasu

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i Otwórz aplikację logiki w Projektancie aplikacji logiki, jeśli nie jest jeszcze otwarta.

1. W przypadku pustych aplikacji logiki w polu wyszukiwania wprowadź wartość "zapasowy" jako filtr. Na liście Wyzwalacze wybierz wyzwalacz, który chcesz. 

   -lub-

   W przypadku istniejących aplikacji logiki w ostatnim kroku, w którym chcesz dodać akcję, wybierz pozycję **nowy krok**. 
   W polu wyszukiwania wprowadź wartość "zapasowy" jako filtr. 
   Na liście Akcje wybierz żądaną akcję.

   Aby dodać akcję między krokami, przesuń wskaźnik myszy nad strzałkę między krokami. 
   Wybierz wyświetlony znak plus ( **+** ), a następnie wybierz pozycję **Dodaj akcję**.

1. Jeśli zostanie wyświetlony monit o zalogowanie się do zapasu czasu, zaloguj się do obszaru roboczego zapasu. 

   ![Obszar roboczy logowanie w usłudze unsigner](./media/connectors-create-api-slack/slack-sign-in-workspace.png)

1. Autoryzuj dostęp do aplikacji logiki.

   ![Autoryzuj dostęp do zapasu czasu](./media/connectors-create-api-slack/slack-authorize-access.png)

1. Podaj niezbędne szczegóły dotyczące wybranego wyzwalacza lub akcji. Aby kontynuować tworzenie przepływu pracy aplikacji logiki, Dodaj więcej akcji.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane w opisie OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę odwołania](/connectors/slack/)łącznika.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Pytania można znaleźć w witrynie [Microsoft Q&pytanie dotyczące Azure Logic Apps](/answers/topics/azure-logic-apps.html).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)

