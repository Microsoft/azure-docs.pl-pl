---
title: Łączenie z wideo pakietu Office 365
description: Automatyzowanie zadań i przepływów pracy, które zarządzają klipami wideo w wideo pakietu Office 365 przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 05/18/2016
tags: connectors
ms.openlocfilehash: 8ac6b7b411e7f42dd076c5b16e7b500a819c617f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75665789"
---
# <a name="manage-videos-in-office365-video-by-using-azure-logic-apps"></a>Zarządzanie filmami wideo w usłudze Office 365 za pomocą Azure Logic Apps

Połącz się z filmem wideo pakietu Office 365, aby uzyskać informacje na temat wideo z pakietem Office 365, uzyskać listę filmów wideo i wiele więcej. Film wideo z pakietem Office 365 umożliwia:

* Kompiluj swój przepływ biznesowy na podstawie danych uzyskanych z wideo pakietu Office 365. 

* Użyj akcji, które sprawdzają stan portalu wideo, uzyskaj listę wszystkich filmów wideo w kanale i nie tylko. Te akcje odbierają odpowiedź, a następnie udostępniają dane wyjściowe dla innych akcji. 

Na przykład możesz użyć łącznika Wyszukiwanie Bing, aby wyszukać wideo pakietu Office 365, a następnie skorzystać z łącznika wideo pakietu Office 365, aby uzyskać informacje o tym filmie wideo. Jeśli film wideo spełnia Twoje wymagania, możesz ogłosić ten film wideo w serwisie Facebook.

Teraz możesz zacząć od utworzenia aplikacji logiki, zobacz [Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-office365-video"></a>Łączenie z wideo w usłudze Office 365

Po dodaniu tego łącznika do aplikacji logiki należy zalogować się do konta wideo pakietu Office 365 i zezwolić aplikacjom logiki na łączenie się ze swoim kontem.

> [!INCLUDE [Steps to create a connection to Office 365 Video](../../includes/connectors-create-api-office365video.md)]

Po utworzeniu połączenia należy wprowadzić właściwości wideo pakietu Office 365, takie jak nazwa dzierżawy lub identyfikator kanału. 

## <a name="connector-specific-details"></a>Szczegóły dotyczące łącznika

Wyświetlanie wszystkich wyzwalaczy i akcji zdefiniowanych w strukturze Swagger, a także wszystkich ograniczeń w [szczegółach łącznika](/connectors/office365videoconnector/).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](../connectors/apis-list.md)