---
title: Łączenie ze źródłami danych RSS z Azure Logic Apps
description: Automatyzowanie zadań i przepływów pracy, które monitorują kanały informacyjne RSS i zarządzają nimi przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 1e95b7bfce3d57dfe579a5622c3c13fac2b6af68
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/25/2020
ms.locfileid: "83834819"
---
# <a name="manage-rss-feeds-by-using-azure-logic-apps"></a>Zarządzanie źródłami danych RSS przy użyciu Azure Logic Apps

Za pomocą Azure Logic Apps i łącznika RSS można tworzyć automatyczne zadania i przepływy pracy dla dowolnego kanału informacyjnego RSS, na przykład:

* Monitoruj, gdy elementy kanału informacyjnego RSS są publikowane.
* Wyświetl listę wszystkich elementów kanału informacyjnego RSS.

Funkcja RSS (Podsumowanie witryny bogatej), nazywana również naprawdę prostym Zespalaniem, jest popularnym formatem dla zespalania sieci Web i służy do publikowania często aktualizowanej zawartości, takiej jak wpisy w blogu i wiadomości. Wielu wydawców zawartości udostępnia kanał informacyjny RSS, aby użytkownicy mogli subskrybować tę zawartość. 

Można użyć wyzwalacza RSS, który pobiera odpowiedzi ze źródła danych RSS i udostępnia dane wyjściowe innym akcjom. W aplikacjach logiki można używać akcji RSS do wykonywania zadań ze źródłem danych RSS. Jeśli jesteś nowym sposobem logiki aplikacji, zapoznaj [się z tematem Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Adres URL źródła danych RSS

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, do której chcesz uzyskać dostęp do kanału informacyjnego RSS. Aby rozpocząć pracę z wyzwalaczem RSS, [Utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji RSS, uruchom aplikację logiki z innym wyzwalaczem, na przykład wyzwalaczem **cyklu** .

## <a name="connect-to-an-rss-feed"></a>Nawiązywanie połączenia ze źródłem danych RSS

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i Otwórz aplikację logiki w Projektancie aplikacji logiki, jeśli nie jest jeszcze otwarta.

1. Wybierz ścieżkę: 

   * W przypadku pustych aplikacji logiki w polu wyszukiwania wprowadź wartość "RSS" jako filtr. Na liście Wyzwalacze wybierz wyzwalacz, który chcesz. 

     -lub-

   * W przypadku istniejących aplikacji logiki w kroku, w którym chcesz dodać akcję, wybierz pozycję **nowy krok**. W polu wyszukiwania jako filtr wprowadź „rss”. Na liście Akcje wybierz żądaną akcję.

1. Podaj niezbędne szczegóły wybranego wyzwalacza lub akcji i Kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane w opisie OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę odwołania](/connectors/rss/)łącznika.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Pytania można znaleźć w witrynie [Microsoft Q&pytanie dotyczące Azure Logic Apps](https://docs.microsoft.com/answers/topics/azure-logic-apps.html).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)