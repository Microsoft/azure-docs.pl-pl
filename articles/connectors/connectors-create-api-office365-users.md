---
title: Nawiązywanie połączenia z użytkownikami pakietu Office 365
description: Automatyzowanie zadań i przepływów pracy, które pobierają profile i zarządzają nimi w profilach użytkowników pakietu Office 365 przy użyciu Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.date: 08/18/2016
ms.topic: article
tags: connectors
ms.openlocfilehash: 179c7538d8072f0e1a4f7d049cf521971efd7a21
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75666860"
---
# <a name="get-and-manage-profiles-in-office-365-users-by-using-azure-logic-apps"></a>Uzyskaj profile i zarządzaj nimi w programie Office 365 przy użyciu Azure Logic Apps

Połącz się z użytkownikami pakietu Office 365, aby uzyskać profile, przeszukiwać użytkowników i nie tylko. Użytkownicy korzystający z pakietu Office 365 mogą:

* Utwórz przepływ biznesowy na podstawie danych uzyskanych od użytkowników pakietu Office 365. 
* Użyj akcji, które uzyskują bezpośrednie raporty, uzyskaj profil użytkownika Menedżera i nie tylko. Te akcje odbierają odpowiedź, a następnie udostępniają dane wyjściowe dla innych akcji. Na przykład Pobierz raporty bezpośrednie osoby, a następnie podejmij te informacje i zaktualizuj bazę danych SQL Azure. 

Teraz możesz zacząć od utworzenia aplikacji logiki, zobacz [Tworzenie aplikacji logiki](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="create-a-connection-to-office-365-users"></a>Utwórz połączenie z użytkownikami pakietu Office 365

Po dodaniu tego łącznika do aplikacji logiki należy zalogować się do konta użytkowników programu Office 365, aby Azure Logic Apps mógł nawiązać połączenie z kontem.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

Po utworzeniu połączenia należy wprowadzić właściwości użytkowników pakietu Office 365, takie jak identyfikator użytkownika. **Dokumentacja interfejsu API REST** w tym artykule opisuje te właściwości.

## <a name="connector-specific-details"></a>Szczegóły dotyczące łącznika

Aby uzyskać szczegółowe informacje techniczne na temat wyzwalaczy, akcji i limitów, które są opisane w opisie struktury Swagger łącznika, przejrzyj [stronę odwołania łącznika](/connectors/officeusers/).

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej na temat innych [łączników Logic Apps](apis-list.md)