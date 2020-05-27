---
title: Nawiązywanie połączenia z usługą Yammer z Azure Logic Apps
description: Automatyzuj zadania i przepływy pracy, które monitorują, publikują i zarządzają wiadomościami, źródłami danych i innymi w usłudze Yammer przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 3023aa72d713dc25351a6e509319e1c4c0ed609a
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83829668"
---
# <a name="monitor-and-manage-your-yammer-account-by-using-azure-logic-apps"></a>Monitorowanie konta usługi Yammer i zarządzanie nim przy użyciu Azure Logic Apps

Za pomocą Azure Logic Apps i łącznika usługi Yammer można tworzyć automatyczne zadania i przepływy pracy, które monitorują komunikaty, kanały informacyjne i nie tylko na koncie w usłudze Yammer, a także inne akcje, na przykład:

* Monitoruj, kiedy nowe komunikaty pojawiają się w odwiedzonych źródłach i grupach.
* Pobieranie komunikatów, grup, sieci, szczegółów użytkowników i nie tylko.
* Komunikaty post i like.

Można użyć wyzwalaczy, które odbierają odpowiedzi z konta usługi Yammer i udostępniają dane wyjściowe innym akcjom. Możesz użyć akcji, które wykonują zadania przy użyciu konta w usłudze Yammer. Możesz również mieć inne akcje, które używają danych wyjściowych z akcji usługi Yammer. Na przykład, gdy nowe komunikaty są wyświetlane w obszarze źródła lub grupy, można je udostępnić za pomocą łącznika zapasowego. Jeśli jesteś nowym sposobem logiki aplikacji, zapoznaj [się z tematem Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Twoje konto w usłudze Yammer i poświadczenia użytkownika

   Twoje poświadczenia autoryzują aplikację logiki, aby utworzyć połączenie i uzyskać dostęp do konta w usłudze Yammer.

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta w usłudze Yammer. Aby rozpocząć pracę z wyzwalaczem usługi Yammer, [Utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji usługi Yammer, uruchom aplikację logiki z innym wyzwalaczem, na przykład wyzwalaczem **cyklu** .

## <a name="connect-to-yammer"></a>Nawiązywanie połączenia z usługą Yammer

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i Otwórz aplikację logiki w Projektancie aplikacji logiki, jeśli nie jest jeszcze otwarta.

1. Wybierz ścieżkę: 

   * W przypadku pustych aplikacji logiki w polu wyszukiwania wprowadź ciąg "Yammer" jako filtr. 
   Na liście Wyzwalacze wybierz wyzwalacz, który chcesz. 

     -lub-

   * Dla istniejących aplikacji logiki: 
   
     * W ostatnim kroku, w którym chcesz dodać akcję, wybierz pozycję **nowy krok**. 

       -lub-

     * Między krokami, do których chcesz dodać akcję, przesuń wskaźnik myszy nad strzałkę między krokami. 
     Wybierz wyświetlony znak plus ( **+** ), a następnie wybierz pozycję **Dodaj akcję**.
     
       W polu wyszukiwania wprowadź ciąg "Yammer" jako filtr. 
       Na liście Akcje wybierz żądaną akcję.

1. Jeśli zostanie wyświetlony monit o zalogowanie do usługi Yammer, zaloguj się teraz, aby zezwolić na dostęp.

1. Podaj niezbędne szczegóły wybranego wyzwalacza lub akcji i Kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane w opisie OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę odwołania](/connectors/yammer/)łącznika.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Pytania można znaleźć w witrynie [Microsoft Q&pytanie dotyczące Azure Logic Apps](https://docs.microsoft.com/answers/topics/azure-logic-apps.html).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)