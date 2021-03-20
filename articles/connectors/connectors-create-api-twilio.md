---
title: Nawiązywanie połączenia z usługą Twilio z poziomu Azure Logic Apps
description: Automatyzuj zadania i przepływy pracy służące do zarządzania globalnymi wiadomościami SMS, MMS i IP za pośrednictwem konta usługi Twilio przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: d144960972f5a1b45e88cc3a0ea015925cae3b91
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87288143"
---
# <a name="manage-messages-in-twilio-with-azure-logic-apps"></a>Zarządzanie komunikatami w programie Twilio przy użyciu Azure Logic Apps

Za pomocą Azure Logic Apps i łącznika Twilio można tworzyć automatyczne zadania i przepływy pracy, które pobierają, wysyłają i wyświetlają komunikaty w Twilio, które obejmują globalne wiadomości SMS, MMS i IP. Te akcje służą do wykonywania zadań przy użyciu konta Twilio. Możesz również mieć inne akcje, które używają danych wyjściowych z akcji Twilio. Na przykład po nadejściu nowej wiadomości można wysłać zawartość wiadomości za pomocą łącznika zapasowego. Jeśli jesteś nowym sposobem logiki aplikacji, zapoznaj [się z tematem Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Z [Twilio](https://www.twilio.com/): 

  * Identyfikator konta Twilio i [token uwierzytelniania](https://support.twilio.com/hc/en-us/articles/223136027-Auth-Tokens-and-How-to-Change-Them), które można znaleźć na pulpicie nawigacyjnym Twilio

    Twoje poświadczenia autoryzują aplikację logiki, aby utworzyć połączenie i uzyskać dostęp do konta usługi Twilio z poziomu aplikacji logiki. 
    Jeśli używasz konta próbnego Twilio, możesz wysyłać wiadomości SMS tylko do *zweryfikowanych* numerów telefonów.

  * Zweryfikowany numer telefonu Twilio, który może wysyłać wiadomości SMS

  * Zweryfikowany numer telefonu Twilio, który może odbierać wiadomości SMS

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, do której chcesz uzyskać dostęp do konta Twilio. Aby użyć akcji Twilio, uruchom aplikację logiki z innym wyzwalaczem, na przykład wyzwalaczem **cyklu** .

## <a name="connect-to-twilio"></a>Nawiązywanie połączenia z usługą Twilio

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i Otwórz aplikację logiki w Projektancie aplikacji logiki, jeśli nie jest jeszcze otwarta.

1. Wybierz ścieżkę: 

     * W ostatnim kroku, w którym chcesz dodać akcję, wybierz pozycję **nowy krok**. 

       -lub-

     * Między krokami, do których chcesz dodać akcję, przesuń wskaźnik myszy nad strzałkę między krokami. 
     Wybierz wyświetlony znak plus ( **+** ), a następnie wybierz pozycję **Dodaj akcję**.
     
       W polu wyszukiwania wprowadź ciąg "Twilio" jako filtr. 
       Na liście Akcje wybierz żądaną akcję.

1. Podaj niezbędne szczegóły dotyczące połączenia, a następnie wybierz pozycję **Utwórz**:

   * Nazwa do użycia dla połączenia
   * Identyfikator konta Twilio 
   * Token dostępu Twilio (Authentication)

1. Podaj niezbędne szczegóły wybranej akcji i Kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane w opisie OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę odwołania](/connectors/twilio/)łącznika.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Pytania można znaleźć w witrynie [Microsoft Q&pytanie dotyczące Azure Logic Apps](/answers/topics/azure-logic-apps.html).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)
