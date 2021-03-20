---
title: Łączenie z usługą Salesforce z Azure Logic Apps
description: Automatyzowanie zadań i przepływów pracy, które monitorują i tworzą rekordy i zadania usługi Salesforce oraz zarządzają nimi przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/24/2018
tags: connectors
ms.openlocfilehash: 9950951ab5189c8c7b72de78bca9465ec5f22748
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87290597"
---
# <a name="monitor-create-and-manage-salesforce-resources-by-using-azure-logic-apps"></a>Monitorowanie i tworzenie zasobów usługi Salesforce oraz zarządzanie nimi za pomocą usługi Azure Logic Apps

Za pomocą Azure Logic Apps i łącznika usługi Salesforce można tworzyć automatyczne zadania i przepływy pracy dla zasobów usługi Salesforce, takie jak rekordy, zadania i obiekty, na przykład:

* Monitoruj, gdy rekordy są tworzone lub zmieniane. 
* Twórz i pobieraj zadania i rekordy oraz zarządzaj nimi, w tym akcje insert, Update i DELETE.

Można użyć wyzwalaczy usługi Salesforce, które uzyskują odpowiedzi z usługi Salesforce i udostępnić dane wyjściowe innym akcjom. W aplikacjach logiki można używać akcji do wykonywania zadań z zasobami usługi Salesforce. Jeśli jesteś nowym sposobem logiki aplikacji, zapoznaj [się z tematem Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* [Konto usługi Salesforce](https://salesforce.com/)

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do konta usługi Salesforce. Aby rozpocząć pracę z wyzwalaczem usługi Salesforce, [Utwórz pustą aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md). Aby użyć akcji Salesforce, uruchom aplikację logiki z innym wyzwalaczem, na przykład wyzwalaczem **cyklu** .

## <a name="connect-to-salesforce"></a>Nawiązywanie połączenia z usługą Salesforce

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i Otwórz aplikację logiki w Projektancie aplikacji logiki, jeśli nie jest jeszcze otwarta.

1. Wybierz ścieżkę: 

   * W przypadku pustych aplikacji logiki w polu wyszukiwania wprowadź ciąg "Salesforce" jako filtr. 
   Na liście Wyzwalacze wybierz wyzwalacz, który chcesz. 

     -lub-

   * W przypadku istniejących aplikacji logiki w kroku, w którym chcesz dodać akcję, wybierz pozycję **nowy krok**. W polu wyszukiwania wprowadź ciąg "Salesforce" jako filtr. Na liście Akcje wybierz żądaną akcję.

1. Jeśli zostanie wyświetlony monit o zalogowanie się do usługi Salesforce, zaloguj się teraz i zezwól na dostęp.

   Twoje poświadczenia autoryzują aplikację logiki, aby utworzyć połączenie z usługą Salesforce i uzyskać dostęp do danych.

1. Podaj niezbędne szczegóły wybranego wyzwalacza lub akcji i Kontynuuj tworzenie przepływu pracy aplikacji logiki.

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane w opisie OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę odwołania](/connectors/salesforce/)łącznika.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

* Pytania można znaleźć w witrynie [Microsoft Q&pytanie dotyczące Azure Logic Apps](/answers/topics/azure-logic-apps.html).
* Aby przesłać pomysły dotyczące funkcji lub zagłosować na nie, odwiedź [witrynę opinii użytkowników usługi Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)
