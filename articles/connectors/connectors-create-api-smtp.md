---
title: Nawiązywanie połączenia z usługą SMTP z Azure Logic Apps
description: Automatyzowanie zadań i przepływów pracy wysyłających wiadomości e-mail za pośrednictwem konta SMTP (Simple Mail Transfer Protocol) za pomocą Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 9989d0ebd95bfe5ee49be2ba76b73e07630b519a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87283949"
---
# <a name="send-email-from-your-smtp-account-with-azure-logic-apps"></a>Wyślij wiadomość e-mail z konta SMTP przy użyciu Azure Logic Apps

Za pomocą Azure Logic Apps i łącznika Simple Mail Transfer Protocol (SMTP) można tworzyć automatyczne zadania i przepływy pracy, które wysyłają wiadomości e-mail z konta SMTP. Możesz również mieć inne akcje, które używają danych wyjściowych z akcji SMTP. Na przykład po wysłaniu wiadomości e-mail przez protokół SMTP można powiadomić zespół w zapasach czasu za pomocą łącznika zapasowego. Jeśli jesteś nowym sposobem logiki aplikacji, zapoznaj [się z tematem Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Twoje konto SMTP i poświadczenia użytkownika

  Twoje poświadczenia autoryzują aplikację logiki, aby utworzyć połączenie i uzyskać dostęp do konta SMTP.

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta SMTP. Aby użyć akcji SMTP, uruchom aplikację logiki z wyzwalaczem, takim jak wyzwalacz usługi Salesforce, jeśli masz konto usługi Salesforce.

  Na przykład możesz uruchomić aplikację logiki przy użyciu wyzwalacza usługi Salesforce, **gdy rekord zostanie utworzony** . 
  Ten wyzwalacz jest uruchamiany za każdym razem, gdy w usłudze Salesforce zostanie utworzony nowy rekord, taki jak potencjalny klient. 
  Następnie można użyć tego wyzwalacza z akcją **Wyślij pocztą e-mail** . Dzięki temu po utworzeniu nowego rekordu aplikacja logiki wysyła wiadomość e-mail z konta SMTP o nowym rekordzie.

## <a name="connect-to-smtp"></a>Nawiązywanie połączenia z usługą SMTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i Otwórz aplikację logiki w Projektancie aplikacji logiki, jeśli nie jest jeszcze otwarta.

1. W ostatnim kroku, w którym chcesz dodać akcję SMTP, wybierz pozycję **nowy krok**. 

   Aby dodać akcję między krokami, przesuń wskaźnik myszy nad strzałkę między krokami. 
   Wybierz wyświetlony znak plus ( **+** ), a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania wprowadź wartość "SMTP" jako filtr. Na liście Akcje wybierz żądaną akcję.

1. Po wyświetleniu monitu podaj następujące informacje o połączeniu:

   | Właściwość | Wymagane | Opis |
   |----------|----------|-------------|
   | **Nazwa połączenia** | Tak | Nazwa połączenia z serwerem SMTP | 
   | **Adres serwera SMTP** | Tak | Adres serwera SMTP | 
   | **Nazwa użytkownika** | Tak | Twoja nazwa użytkownika dla konta SMTP | 
   | **Hasło** | Tak | Hasło do konta SMTP | 
   | **Port serwera SMTP** | Nie | Określony port na serwerze SMTP, który ma być używany | 
   | **Włączyć protokół SSL?** | Nie | Włącz lub wyłącz szyfrowanie TLS/SSL. | 
   |||| 

1. Podaj niezbędne szczegóły wybranej akcji. 

1. Zapisz aplikację logiki lub Kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać więcej szczegółowych informacji technicznych dotyczących tego łącznika, takich jak wyzwalacze, akcje i limity, zgodnie z opisem w pliku Swagger łącznika, zobacz [stronę odwołania łącznika](/connectors/smtpconnector/).

> [!NOTE]
> W przypadku aplikacji logiki w [środowisku usługi integracji (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md), wersja tego łącznika z oznaczeniem ISE w zamian używa [limitów komunikatów ISE](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) .

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)
