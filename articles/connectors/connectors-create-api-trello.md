---
title: Nawiązywanie połączenia z usługą Trello z poziomu Azure Logic Apps
description: Automatyzowanie zadań i przepływów pracy, które monitorują listy, tablice i karty w projektach Trello oraz zarządzają nimi przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 5c4fcb9b4fea1a4d982b5cf665564599d371b7cb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74789141"
---
# <a name="monitor-and-manage-trello-with-azure-logic-apps"></a>Monitoruj Trello i zarządzaj nimi za pomocą Azure Logic Apps

Za pomocą Azure Logic Apps i łącznika Trello można tworzyć automatyczne zadania i przepływy pracy, które monitorują listy Trello, karty, tablice, członków zespołu i zarządzają nimi, na przykład:

* Monitoruj, gdy nowe karty są dodawane do tablic i list. 
* Twórz i pobieraj tablice, karty i listy oraz zarządzaj nimi.
* Dodaj komentarze i członków do kart.
* Tablice list, etykiety tablicowe, karty na tablicach, komentarze karty, elementy członkowskie kart, członków zespołu i zespoły, w których jesteś członkiem. 
* Pobierz zespoły.

Można użyć wyzwalaczy, które odbierają odpowiedzi z konta usługi Trello, i udostępniają dane wyjściowe innym akcjom. Możesz użyć akcji, które wykonują zadania przy użyciu konta Trello. Możesz również mieć inne akcje, które używają danych wyjściowych z akcji Trello. Na przykład po dodaniu nowej karty do tablicy lub listy można wysłać komunikaty z łącznikiem zapasowym. Jeśli jesteś nowym sposobem logiki aplikacji, zapoznaj [się z tematem Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Twoje konto Trello i poświadczenia użytkownika

  Twoje poświadczenia autoryzują aplikację logiki, aby utworzyć połączenie i uzyskać dostęp do konta usługi Trello.

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, do której chcesz uzyskać dostęp do konta Trello. Aby rozpocząć od wyzwalacza Trello, [Utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji Trello, uruchom aplikację logiki z wyzwalaczem, na przykład wyzwalaczem **cyklu** .

## <a name="connect-to-trello"></a>Nawiązywanie połączenia z usługą Trello

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i Otwórz aplikację logiki w Projektancie aplikacji logiki, jeśli nie jest jeszcze otwarta.

1. W przypadku pustych aplikacji logiki w polu wyszukiwania wprowadź ciąg "Trello" jako filtr. Na liście Wyzwalacze wybierz wyzwalacz, który chcesz. 

   — lub —

   W przypadku istniejących aplikacji logiki w ostatnim kroku, w którym chcesz dodać akcję, wybierz pozycję **nowy krok**. 
   W polu wyszukiwania wprowadź ciąg "Trello" jako filtr. 
   Na liście Akcje wybierz żądaną akcję.

   Aby dodać akcję między krokami, przesuń wskaźnik myszy nad strzałkę między krokami. 
   Wybierz wyświetlony znak plus (**+**), a następnie wybierz pozycję **Dodaj akcję**.

1. Jeśli zostanie wyświetlony monit o zalogowanie się do usługi Trello, Autoryzuj dostęp do aplikacji logiki i zaloguj się.

1. Podaj niezbędne szczegóły wybranego wyzwalacza lub akcji i Kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane w opisie OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę odwołania](/connectors/trello/)łącznika.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)