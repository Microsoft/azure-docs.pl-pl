---
title: Nawiązywanie połączenia z usługą SendGrid z poziomu Azure Logic Apps
description: Automatyzuj zadania i przepływy pracy, które wysyłają wiadomości e-mail i zarządzają listami adresowymi w programie SendGrid przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: a140ae0f27c61959d8ebc6854c5bcb2a916a0fc6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87290435"
---
# <a name="send-emails-and-manage-mailing-lists-in-sendgrid-by-using-azure-logic-apps"></a>Wysyłanie wiadomości e-mail i zarządzanie listami adresowymi w programie SendGrid przy użyciu Azure Logic Apps

Za pomocą Azure Logic Apps i łącznika SendGrid można tworzyć automatyczne zadania i przepływy pracy, które wysyłają wiadomości e-mail i zarządzają listami adresatów, na przykład:

* Wyślij wiadomość e-mail.
* Dodaj adresatów do list.
* Pobierz, Dodaj i Zarządzaj globalnym pomijaniem.

Aby wykonać te zadania, można użyć akcji SendGrid w aplikacjach logiki. Możesz również mieć inne akcje, które używają danych wyjściowych z akcji SendGrid. 

Ten łącznik zawiera tylko akcje, więc aby uruchomić aplikację logiki, użyj oddzielnego wyzwalacza, takiego jak wyzwalacz **cyklu** . Na przykład w przypadku regularnego dodawania adresatów do list możesz wysyłać wiadomości e-mail dotyczące adresatów i list przy użyciu łącznika usługi Office 365 Outlook lub łącznika Outlook.com.
Jeśli jesteś nowym sposobem logiki aplikacji, zapoznaj [się z tematem Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* [Konto SendGrid](https://www.sendgrid.com/) i [klucz interfejsu API SendGrid](https://sendgrid.com/docs/ui/account-and-settings/api-keys/)

   Klucz interfejsu API autoryzuje aplikację logiki, aby utworzyć połączenie i uzyskać dostęp do konta usługi SendGrid.

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, do której chcesz uzyskać dostęp do konta SendGrid. Aby użyć akcji SendGrid, uruchom aplikację logiki z innym wyzwalaczem, na przykład wyzwalaczem **cyklu** .

## <a name="connect-to-sendgrid"></a>Nawiązywanie połączenia z usługą SendGrid

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i Otwórz aplikację logiki w Projektancie aplikacji logiki, jeśli nie jest jeszcze otwarta.

1. Wybierz ścieżkę: 

   * W ostatnim kroku, w którym chcesz dodać akcję, wybierz pozycję **nowy krok**. 

     -lub-

   * Między krokami, do których chcesz dodać akcję, przesuń wskaźnik myszy nad strzałkę między krokami. 
   Wybierz wyświetlony znak plus ( **+** ), a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania wprowadź ciąg "SendGrid" jako filtr. Na liście Akcje wybierz żądaną akcję.

1. Podaj nazwę połączenia. 

1. Wprowadź klucz interfejsu API SendGrid, a następnie wybierz pozycję **Utwórz**.

1. Podaj niezbędne szczegóły wybranej akcji i Kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane w opisie OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę odwołania](/connectors/sendgrid/)łącznika.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Pytania można znaleźć w witrynie [Microsoft Q&pytanie dotyczące Azure Logic Apps](/answers/topics/azure-logic-apps.html).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)
