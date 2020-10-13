---
title: Połącz z programem SharePoint z Azure Logic Apps
description: Automatyzowanie zadań i przepływów pracy, które monitorują zasoby i zarządzają nimi w usłudze SharePoint Online lub SharePoint Server lokalnie przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
tags: connectors
ms.openlocfilehash: c72330792e508361830c1bf391f85eefe78bdd1e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87283983"
---
# <a name="monitor-and-manage-sharepoint-resources-with-azure-logic-apps"></a>Monitorowanie zasobów SharePoint i zarządzanie nimi za pomocą usługi Azure Logic Apps

Za pomocą Azure Logic Apps i łącznika programu SharePoint można tworzyć automatyczne zadania i przepływy pracy, które monitorują zasoby i zarządzają nimi, takie jak pliki, foldery, listy, elementy, osoby itd., w usłudze SharePoint Online lub w programie SharePoint Server lokalnie, na przykład:

* Monitoruj, kiedy pliki lub elementy są tworzone, zmieniane lub usuwane.
* Tworzenie, pobieranie, aktualizowanie lub usuwanie elementów.
* Dodawanie, pobieranie lub usuwanie załączników. Pobierz zawartość z załączników.
* Tworzenie, kopiowanie, aktualizowanie i usuwanie plików. 
* Aktualizowanie właściwości pliku. Pobierz zawartość, metadane lub właściwości pliku.
* Wyświetl listę lub Wyodrębnij foldery.
* Pobierz listy lub widoki listy.
* Ustaw stan zatwierdzenia zawartości.
* Rozpoznaj osoby.
* Wysyłaj żądania HTTP do programu SharePoint.
* Pobierz wartości jednostki.

Można użyć wyzwalaczy, które odbierają odpowiedzi z programu SharePoint i udostępniają dane wyjściowe innym akcjom. Za pomocą akcji w aplikacjach logiki można wykonywać zadania w programie SharePoint. Możesz również mieć inne akcje, które używają danych wyjściowych z akcji programu SharePoint. Na przykład, jeśli regularnie pobierasz pliki z programu SharePoint, możesz wysyłać komunikaty do zespołu za pomocą łącznika zapasowego.
Jeśli jesteś nowym sposobem logiki aplikacji, zapoznaj [się z tematem Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Adres witryny programu SharePoint i poświadczenia użytkownika

  Twoje poświadczenia autoryzują aplikację logiki, aby utworzyć połączenie i uzyskać dostęp do konta programu SharePoint. 

* Aby można było połączyć Aplikacje logiki z systemami lokalnymi, takimi jak SharePoint Server, należy [zainstalować i skonfigurować lokalną bramę danych](../logic-apps/logic-apps-gateway-install.md). Dzięki temu można określić, czy podczas tworzenia połączenia z programem SharePoint Server dla aplikacji logiki ma być używana instalacja bramy.

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta programu SharePoint. Aby rozpocząć pracę z wyzwalaczem programu SharePoint, [Utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji programu SharePoint, uruchom aplikację logiki z wyzwalaczem, takim jak wyzwalacz usługi Salesforce, jeśli masz konto usługi Salesforce.

  Na przykład możesz uruchomić aplikację logiki przy użyciu wyzwalacza usługi Salesforce, **gdy rekord zostanie utworzony** . 
  Ten wyzwalacz jest uruchamiany za każdym razem, gdy w usłudze Salesforce zostanie utworzony nowy rekord, taki jak potencjalny klient. 
  Następnie można użyć tego wyzwalacza z akcją **Utwórz plik** programu SharePoint. Dzięki temu po utworzeniu nowego rekordu aplikacja logiki tworzy plik w programie SharePoint z informacjami o nowym rekordzie.

## <a name="connect-to-sharepoint"></a>Nawiązywanie połączenia z programem SharePoint

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i Otwórz aplikację logiki w Projektancie aplikacji logiki, jeśli nie jest jeszcze otwarta.

1. W przypadku pustych aplikacji logiki w polu wyszukiwania wprowadź ciąg "SharePoint" jako filtr. Na liście Wyzwalacze wybierz wyzwalacz, który chcesz. 

   -lub-

   W przypadku istniejących aplikacji logiki w ostatnim kroku, w którym chcesz dodać akcję programu SharePoint, wybierz pozycję **nowy krok**. 
   W polu wyszukiwania wprowadź wartość "SharePoint" jako filtr. 
   Na liście Akcje wybierz żądaną akcję.

   Aby dodać akcję między krokami, przesuń wskaźnik myszy nad strzałkę między krokami. 
   Wybierz wyświetlony znak plus ( **+** ), a następnie wybierz pozycję **Dodaj akcję**.

1. Po wyświetleniu monitu o zalogowanie się podaj niezbędne informacje dotyczące połączenia. Jeśli używasz programu SharePoint Server, upewnij się, że wybrano opcję **Połącz za pośrednictwem lokalnej bramy danych**. Gdy wszystko będzie gotowe, wybierz pozycję **Utwórz**.

1. Podaj niezbędne szczegóły wybranego wyzwalacza lub akcji i Kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane w opisie OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę odwołania](/connectors/sharepoint/)łącznika.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Pytania można znaleźć w witrynie [Microsoft Q&pytanie dotyczące Azure Logic Apps](/answers/topics/azure-logic-apps.html).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)

