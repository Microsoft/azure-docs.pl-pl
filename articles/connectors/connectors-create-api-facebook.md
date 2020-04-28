---
title: Łączenie z serwisem Facebook
description: Automatyzowanie zadań i przepływów pracy, które zarządzają osią czasu i stroną w serwisie Facebook przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 11/07/2016
tags: connectors
ms.openlocfilehash: 803453291b5cab2c51fec6641f8b096f62336325
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75665806"
---
# <a name="manage-your-facebook-timeline-and-page-by-using-azure-logic-apps"></a>Zarządzanie osią czasu i stroną w serwisie Facebook przy użyciu Azure Logic Apps

Nawiąż połączenie z serwisem Facebook i Opublikuj je na osi czasu, Pobierz kanał informacyjny strony i nie tylko. W usłudze Facebook można:

* Utwórz przepływ biznesowy na podstawie danych uzyskanych z serwisu Facebook. 
* Użyj wyzwalacza, gdy zostanie odebrany nowy wpis.
* Użyj akcji, które są ogłaszane na osi czasu, uzyskaj kanał informacyjny strony i nie tylko. Te akcje odbierają odpowiedź, a następnie udostępniają dane wyjściowe dla innych akcji. Na przykład, gdy na osi czasu jest nowy wpis, możesz wykonać ten wpis i wypchnąć go do kanału informacyjnego usługi Twitter. 

Teraz możesz zacząć od utworzenia aplikacji logiki, zobacz [Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-facebook"></a>Utwórz połączenie z usługą Facebook

Po dodaniu tego łącznika do usługi Logic Apps musisz autoryzować Aplikacje logiki, aby połączyć się z serwisem Facebook.

1. Zaloguj się do swojego konta w serwisie Facebook.

2. Wybierz pozycję **Autoryzuj**, aby umożliwić aplikacjom logiki łączenie się z usługą Facebook i korzystanie z niej. 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne, takie jak wyzwalacze, akcje i limity, zgodnie z opisem w pliku OpenAPI łącznika (dawniej Swagger), zobacz [stronę odwołania łącznika](/connectors/facebook/).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)