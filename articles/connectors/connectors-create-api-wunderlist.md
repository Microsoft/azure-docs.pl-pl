---
title: Połącz z usługą Wunderlist z Azure Logic Apps
description: Automatyzuj zadania i przepływy pracy, które monitorują listy, zadania, przypomnienia i nie tylko w ramach konta usługi Wunderlist oraz zarządzają nimi przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 5ac13595bd77238aaede5fa3bdc3a35ef69e8504
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74789124"
---
# <a name="monitor-and-manage-wunderlist-by-using-azure-logic-apps"></a>Monitoruj i Zarządzaj usługą Wunderlist przy użyciu Azure Logic Apps

Korzystając z Azure Logic Apps i łącznika usługi Wunderlist, możesz tworzyć automatyczne zadania i przepływy pracy, które monitorują listy zadań, zadania, przypomnienia i inne na koncie usługi Wunderlist oraz zarządzają nimi, a także z innymi akcjami, na przykład:

* Monitoruj po utworzeniu nowych zadań, gdy zadania są zaległe lub przypomnienia są wykonywane.
* Twórz listy, notatki, zadania i podzadania oraz zarządzaj nimi.
* Ustawianie przypomnień.
* Pobieranie list, zadań, podzadań, przypomnień, plików, notatek, komentarzy i innych.

[Wunderlist](https://www.wunderlist.com/) to usługa, która ułatwia planowanie i kończenie projektów oraz zarządzanie nimi, listy zadań do wykonania i zadania — na dowolnym urządzeniu i w dowolnym miejscu. Możesz użyć wyzwalaczy, które pobierają odpowiedzi z konta usługi Wunderlist, i udostępniają dane wyjściowe innym akcjom. Możesz użyć akcji, które wykonują zadania przy użyciu konta aplikacji Wunderlist. Możesz również mieć inne akcje, które używają danych wyjściowych z akcji aplikacji Wunderlist. Na przykład, gdy nowe zadania są zaległe, można publikować komunikaty za pomocą łącznika zapasowego. Jeśli jesteś nowym sposobem logiki aplikacji, zapoznaj [się z tematem Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Twoje konto aplikacji Wunderlist i poświadczenia użytkownika

   Twoje poświadczenia autoryzują aplikację logiki, aby utworzyć połączenie i uzyskać dostęp do konta usługi Wunderlist.

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta w usłudze Yammer. Aby rozpocząć pracę z wyzwalaczem aplikacji Wunderlist, [Utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji Wunderlist, uruchom aplikację logiki z innym wyzwalaczem, na przykład wyzwalaczem **cyklu** .

## <a name="connect-to-wunderlist"></a>Łączenie z usługą Wunderlist

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i Otwórz aplikację logiki w Projektancie aplikacji logiki, jeśli nie jest jeszcze otwarta.

1. Wybierz ścieżkę: 

   * W przypadku pustych aplikacji logiki w polu wyszukiwania wprowadź ciąg "Wunderlist" jako filtr. 
   Na liście Wyzwalacze wybierz wyzwalacz, który chcesz. 

     — lub —

   * Dla istniejących aplikacji logiki: 
   
     * W ostatnim kroku, w którym chcesz dodać akcję, wybierz pozycję **nowy krok**. 

       — lub —

     * Między krokami, do których chcesz dodać akcję, przesuń wskaźnik myszy nad strzałkę między krokami. 
     Wybierz wyświetlony znak plus (**+**), a następnie wybierz pozycję **Dodaj akcję**.
     
       W polu wyszukiwania wprowadź ciąg "Wunderlist" jako filtr. 
       Na liście Akcje wybierz żądaną akcję.

1. Jeśli zostanie wyświetlony monit o zalogowanie się do aplikacji Wunderlist, zaloguj się teraz, aby zezwolić na dostęp.

1. Podaj niezbędne szczegóły wybranego wyzwalacza lub akcji i Kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane w opisie OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę odwołania](/connectors/wunderlist/)łącznika.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)