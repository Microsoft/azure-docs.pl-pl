---
title: Wdrażanie & zarządzanie Azure Container Instances
description: Automatyzowanie zadań i przepływów pracy, które tworzą i zarządzają wdrożeniami kontenerów w Azure Container Instances przy użyciu Azure Logic Apps
services: logic-apps, container-instances
ms.service: logic-apps
ms.suite: integration
author: dlepow
ms.author: danlep
ms.manager: gwallace
ms.reviewer: estfan, macolso
ms.topic: article
tags: connectors
ms.date: 01/14/2020
ms.openlocfilehash: ecb1049d64197f2a60438df7eedfb244907f7327
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "76046294"
---
# <a name="deploy-and-manage-azure-container-instances-by-using-azure-logic-apps"></a>Wdrażanie Azure Container Instances i zarządzanie nimi za pomocą Azure Logic Apps

Za pomocą Azure Logic Apps i łącznika usługi Azure Container instance można skonfigurować automatyczne zadania i przepływy pracy, które wdrażają [grupy kontenerów](../container-instances/container-instances-container-groups.md)i zarządzają nimi. Łącznik wystąpienia kontenera obsługuje następujące akcje:

* Tworzenie lub usuwanie grupy kontenerów
* Pobierz właściwości grupy kontenerów
* Pobierz listę grup kontenerów
* Pobieranie dzienników wystąpienia kontenera

Użyj tych akcji w aplikacjach logiki do wykonywania zadań, takich jak uruchamianie obciążenia kontenera w odpowiedzi na wyzwalacz Logic Apps. Możesz również mieć inne akcje, używając danych wyjściowych z akcji wystąpienia kontenera. 

Ten łącznik zawiera tylko akcje, więc aby uruchomić aplikację logiki, użyj oddzielnego wyzwalacza, takiego jak wyzwalacz **cykliczny** do uruchomienia obciążenia kontenera zgodnie z regularnym harmonogramem. Może być konieczne wyzwolenie wdrożenia grupy kontenerów po wystąpieniu zdarzenia, takiego jak nadejście wiadomości e-mail programu Outlook. 

Jeśli jesteś nowym sposobem logiki aplikacji, zapoznaj [się z tematem Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Jeśli nie masz subskrypcji platformy Azure, [zarejestruj się w celu założenia bezpłatnego konta platformy Azure](https://azure.microsoft.com/free/). 

* Podstawowa wiedza [na temat tworzenia aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md) oraz [tworzenia wystąpień kontenerów i zarządzania nimi](../container-instances/container-instances-quickstart.md)

* Aplikacja logiki, w której chcesz uzyskać dostęp do wystąpień kontenerów. Aby użyć akcji, uruchom aplikację logiki z innym wyzwalaczem, na przykład wyzwalaczem **cyklu** .

## <a name="add-a-container-instance-action"></a>Dodaj akcję wystąpienia kontenera

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Zaloguj się do [Azure Portal](https://portal.azure.com)i Otwórz aplikację logiki w Projektancie aplikacji logiki, jeśli nie jest jeszcze otwarta.

1. Wybierz ścieżkę: 

   * W ostatnim kroku, w którym chcesz dodać akcję, wybierz pozycję **nowy krok**. 

     -lub-

   * Między krokami, do których chcesz dodać akcję, przesuń wskaźnik myszy nad strzałkę między krokami. 
   Wybierz wyświetlony znak plus ( **+** ), a następnie wybierz pozycję **Dodaj akcję**.

1. W polu wyszukiwania wprowadź "wystąpienie kontenera" jako filtr. Na liście Akcje wybierz żądaną akcję łącznika usługi Azure Container instance.

1. Podaj nazwę połączenia. 

1. Podaj niezbędne szczegóły wybranej akcji i Kontynuuj tworzenie przepływu pracy aplikacji logiki.

  Na przykład wybierz pozycję **Utwórz grupę kontenerów** i wprowadź właściwości grupy kontenerów oraz co najmniej jedno wystąpienie kontenera w grupie, jak pokazano na poniższej ilustracji (częściowa Szczegółowa):

  ![Tworzenie grupy kontenerów](./media/connectors-create-api-container-instances/logic-apps-aci-connector.png)

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane w opisie OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę referencyjną](/connectors/aci/) łącznika lub [odwołanie do YAML](../container-instances/container-instances-reference-yaml.md)grupy kontenerów.

## <a name="next-steps"></a>Następne kroki

* Zobacz [przykładową aplikację logiki](https://github.com/Azure-Samples/aci-logicapps-integration) , która uruchamia kontener w Azure Container Instances, aby przeanalizować tonacji wiadomości e-mail lub serwisu Twitter

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)