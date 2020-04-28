---
title: Połącz z usługą Project Online z Azure Logic Apps
description: Automatyzowanie przepływów pracy, które monitorują, tworzą i zarządzają projektami, zadaniami i zasobami usługi Project Online przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 116922e018410f260b33f4dfd76e5983fbbcc0a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74789328"
---
# <a name="manage-project-online-projects-tasks-and-resources-by-using-azure-logic-apps"></a>Zarządzanie projektami, zadaniami i zasobami usługi Project Online przy użyciu Azure Logic Apps

Dzięki Azure Logic Apps i łącznika usługi Project Online można tworzyć automatyczne zadania i przepływy pracy dla projektów, zadań i zasobów w usłudze Project Online za pomocą pakietu Office 365. Przepływy pracy mogą wykonywać te akcje i inne, na przykład:

* Monitoruj, gdy tworzone są nowe projekty, zadania lub zasoby. Lub Monitoruj, kiedy nowe projekty są publikowane.
* Twórz nowe projekty, zadania lub zasoby.
* Wyświetl listę istniejących projektów lub zadań.
* Wyewidencjonowywanie, ewidencjonowanie lub publikowanie projektów.

Program Project Online ułatwia planowanie, określanie priorytetów i zarządzanie projektami oraz inwestycjami w portfolio projektów z niemal dowolnego miejsca na prawie każdego urządzenia, zapewniając zaawansowane możliwości zarządzania projektami. Możesz użyć wyzwalaczy usługi Project Online, które odbierają odpowiedzi z usługi Project Online i udostępniają dane wyjściowe innym akcjom. W usłudze Logic Apps można używać akcji do wykonywania różnych zadań w usłudze Project Online. Jeśli jesteś nowym sposobem logiki aplikacji, zapoznaj [się z tematem Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Project Online, dostępne za poorednictwem [konta Office 365](https://www.office.com/), 

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do danych usługi Project Online. Aby rozpocząć pracę z wyzwalaczem usługi Project Online, [Utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby korzystać z akcji usługi Project Online, uruchom aplikację logiki z innym wyzwalaczem, na przykład wyzwalaczem **cyklu** .

## <a name="connect-to-project-online"></a>Połącz z usługą Project Online

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i Otwórz aplikację logiki w Projektancie aplikacji logiki, jeśli nie jest jeszcze otwarta.

1. Wybierz ścieżkę: 

   * W przypadku pustych aplikacji logiki w polu wyszukiwania wprowadź ciąg "Project Online" jako filtr. 
   Na liście Wyzwalacze wybierz wyzwalacz, który chcesz. 

     — lub —

   * W przypadku istniejących aplikacji logiki w kroku, w którym chcesz dodać akcję, wybierz pozycję **nowy krok**. W polu wyszukiwania wprowadź wartość "Project Online" jako filtr. Na liście Akcje wybierz żądaną akcję.

1. Jeśli zostanie wyświetlony monit o zalogowanie się do usługi Project Online, zaloguj się teraz.

   Twoje poświadczenia autoryzują aplikację logiki, aby utworzyć połączenie z usługą Project Online i uzyskać dostęp do danych.

1. Podaj niezbędne szczegóły wybranego wyzwalacza lub akcji i Kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane w opisie OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę odwołania](/connectors/projectonline/)łącznika.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)