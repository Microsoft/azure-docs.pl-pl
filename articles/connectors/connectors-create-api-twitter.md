---
title: Nawiązywanie połączenia z usługą Twitter z poziomu usługi Azure Logic Apps
description: Automatyzuj zadania i przepływy pracy, które monitorują tweety i zarządzają nimi, a także pobierają dane dotyczące obserwatorów, zaobserwowanego użytkownika, innych użytkowników, osi czasu i innych informacji z konta usługi Twitter przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: 8ffd0fd558cf759fadd912de9dff4acf49d9659f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74789090"
---
# <a name="monitor-and-manage-twitter-by-using-azure-logic-apps"></a>Monitoruj usługi Twitter i zarządzaj nimi za pomocą Azure Logic Apps

Za pomocą Azure Logic Apps i łącznika usługi Twitter można tworzyć automatyczne zadania i przepływy pracy, które monitorują i zarządzają danymi, które są potrzebne w serwisie Twitter, takich jak tweety, obserwatorzy, użytkownicy i zaobserwowane użytkownicy, osie czasu i inne, a także inne akcje, na przykład:

* Monitoruj, Publikuj i wyszukuj tweety.
* Pobieraj dane, takie jak obserwatorzy, zaobserwowane użytkownicy, osie czasu i nie tylko.

Możesz użyć wyzwalaczy, które pobierają odpowiedzi z konta usługi Twitter i udostępniają dane wyjściowe innym akcjom. Możesz użyć akcji, które wykonują zadania przy użyciu konta w usłudze Twitter. Możesz również mieć inne akcje, które używają danych wyjściowych z akcji usługi Twitter. Na przykład po pojawieniu się nowego tweetu z określonym elementem hasztagów można wysłać komunikaty z łącznikiem zapasowym. Jeśli jesteś nowym sposobem logiki aplikacji, zapoznaj [się z tematem Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Twoje konto i poświadczenia użytkownika usługi Twitter

   Twoje poświadczenia autoryzują aplikację logiki, aby utworzyć połączenie i uzyskać dostęp do konta usługi Twitter.

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta usługi Twitter. Aby rozpocząć pracę z wyzwalaczem usługi Twitter, [Utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji usługi Twitter, uruchom aplikację logiki z innym wyzwalaczem, na przykład wyzwalaczem **cyklu** .

## <a name="connect-to-twitter"></a>Łączenie z usługą Twitter

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i Otwórz aplikację logiki w Projektancie aplikacji logiki, jeśli nie jest jeszcze otwarta.

1. Wybierz ścieżkę: 

   * W przypadku pustych aplikacji logiki w polu wyszukiwania wprowadź wartość "Twitter" jako filtr. 
   Na liście Wyzwalacze wybierz wyzwalacz, który chcesz. 

     — lub —

   * Dla istniejących aplikacji logiki: 
   
     * W ostatnim kroku, w którym chcesz dodać akcję, wybierz pozycję **nowy krok**. 

       — lub —

     * Między krokami, do których chcesz dodać akcję, przesuń wskaźnik myszy nad strzałkę między krokami. 
     Wybierz wyświetlony znak plus (**+**), a następnie wybierz pozycję **Dodaj akcję**.
     
       W polu wyszukiwania wprowadź wartość "Twitter" jako filtr. 
       Na liście Akcje wybierz żądaną akcję.

1. Jeśli zostanie wyświetlony monit o zalogowanie się do usługi Twitter, zaloguj się teraz, aby można było autoryzować dostęp do aplikacji logiki.

1. Podaj niezbędne szczegóły wybranego wyzwalacza lub akcji i Kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="examples"></a>Przykłady

### <a name="twitter-trigger-when-a-new-tweet-is-posted"></a>Wyzwalacz usługi Twitter: po opublikowaniu nowego tweetu

Ten wyzwalacz uruchamia przepływ pracy aplikacji logiki, gdy wyzwalacz wykryje nowy Tweet, na przykład przy użyciu #Seattle. Na przykład po znalezieniu tych tweetów możesz dodać plik z zawartością tweetów do magazynu, na przykład konto usługi Dropbox przy użyciu łącznika usługi Dropbox. 

Opcjonalnie możesz dołączyć warunek, który uprawnieni tweety muszą pochodzić od użytkowników, którzy mają co najmniej określoną liczbę obserwatorów.

**Przykład przedsiębiorstwa**: ten wyzwalacz służy do monitorowania tweetów dotyczących firmy i przekazywania zawartości tweetów do bazy danych SQL.

### <a name="twitter-action-post-a-tweet"></a>Akcja usługi Twitter: publikowanie tweetu

Ta akcja służy do ogłaszania tweetu, ale można skonfigurować akcję tak, aby Tweet zawierał zawartość tweetów znalezionych przez poprzednio opisany wyzwalacz. 

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane w opisie OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę odwołania](/connectors/twitterconnector/)łącznika.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)
