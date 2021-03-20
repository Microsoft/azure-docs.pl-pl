---
title: Migrowanie aplikacji do najnowszego schematu
description: Jak migrować definicje JSON przepływu pracy aplikacji logiki do najnowszej wersji schematu definicji przepływu pracy
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 08/25/2018
ms.openlocfilehash: 114b8b32d4abb1fd9b7e641625cd1b132470bafd
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87281450"
---
# <a name="migrate-logic-apps-to-latest-schema-version"></a>Migrowanie aplikacji logiki do najnowszej wersji schematu

Aby przenieść istniejące aplikacje logiki do najnowszego schematu, wykonaj następujące kroki: 

1. W [Azure Portal](https://portal.azure.com)Otwórz aplikację logiki w Projektancie aplikacji logiki.

2. W menu aplikacji logiki wybierz pozycję **Przegląd**. Na pasku narzędzi wybierz pozycję **Aktualizuj schemat**.

   > [!NOTE]
   > Po wybraniu opcji **Aktualizuj schemat** Azure Logic Apps automatycznie uruchamia kroki migracji i udostępnia dane wyjściowe kodu. Możesz użyć tych danych wyjściowych do zaktualizowania definicji aplikacji logiki. Należy jednak przestrzegać najlepszych rozwiązań zgodnie z opisem w sekcji **najlepsze rozwiązania** .

   ![Aktualizuj schemat](./media/connectors-schema-migration/update-schema.png)

   Zostanie wyświetlona strona aktualizowanie schematu z linkiem do dokumentu opisującego ulepszenia w nowym schemacie.

## <a name="best-practices"></a>Najlepsze rozwiązania

Poniżej przedstawiono niektóre najlepsze rozwiązania dotyczące migrowania aplikacji logiki do najnowszej wersji schematu:

* Skopiuj zmigrowany skrypt do nowej aplikacji logiki. Nie zastępuj starej wersji do momentu ukończenia testowania i upewnienia się, że migrowana aplikacja działa zgodnie z oczekiwaniami.

* Przetestuj aplikację logiki **przed** wprowadzeniem do środowiska produkcyjnego.

* Po zakończeniu migracji Zacznij aktualizować Aplikacje logiki, aby używać [zarządzanych interfejsów API](../connectors/apis-list.md) , jeśli jest to możliwe. Na przykład Zacznij korzystać z usługi Dropbox v2 wszędzie tam, gdzie używasz usługi DropBox v1.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się, jak [ręcznie migrować Aplikacje logiki](../logic-apps/logic-apps-schema-2016-04-01.md)

