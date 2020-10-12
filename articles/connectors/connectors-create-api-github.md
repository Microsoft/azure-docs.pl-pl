---
title: Dostęp do repozytorium GitHub i zarządzanie nim
description: Monitoruj zdarzenia usługi GitHub i Zarządzaj repozytorium GitHub, tworząc zautomatyzowane przepływy pracy za pomocą Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/02/2018
tags: connectors
ms.openlocfilehash: b67d2e860e8c69f6f2f43441144ef2f60ed3a104
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75378453"
---
# <a name="monitor-and-manage-your-github-repo-by-using-azure-logic-apps"></a>Monitorowanie repozytorium GitHub i zarządzanie nim za pomocą Azure Logic Apps

GitHub to usługa hostingu repozytorium git oparta na sieci Web, która oferuje wszystkie rozproszone funkcje kontroli poprawek i zarządzania kodem źródłowym (SCM) w systemie Git oraz inne funkcje.

Aby rozpocząć pracę z łącznikiem usługi GitHub, [najpierw Utwórz aplikację logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-github"></a>Utwórz połączenie z usługą GitHub

Aby skorzystać z łącznika usługi GitHub w aplikacji logiki, należy najpierw utworzyć *połączenie* , a następnie podać szczegóły tych właściwości: 

| Właściwość | Wymagane | Opis | 
| -------- | -------- | ----------- | 
| Token | Tak | Podaj swoje poświadczenia usługi GitHub. |

Po utworzeniu połączenia można wykonać akcje i wysłuchać wyzwalacze opisane w tym artykule.

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 

## <a name="connector-reference"></a>Dokumentacja łączników

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane w opisie OpenAPI łącznika (dawniej Swagger), przejrzyj [stronę odwołania łącznika](/connectors/github/).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)