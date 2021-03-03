---
title: Łącznik zarządzania usługami IT — Bezpieczny eksport w programie Azure Monitor — konfiguracja przy użyciu usługi ServiceNow
description: W tym artykule pokazano, jak połączyć narzędzia ITSM produkty/usługi z usługą usługi ServiceNow w przypadku bezpiecznego eksportowania w Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: 02b167219a4f1604d340d72f9dc47e67919c2542
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101714140"
---
# <a name="connect-servicenow-to-azure-monitor"></a>Połącz usługi ServiceNow z Azure Monitor

W poniższych sekcjach znajdują się szczegółowe informacje dotyczące sposobu łączenia produktu usługi ServiceNow i bezpiecznego eksportowania na platformie Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że zostały spełnione następujące wymagania wstępne:

* Usługa Azure AD jest zarejestrowana.
* Dostępna jest obsługiwana wersja usługi ServiceNow zarządzania zdarzeniami — ITOM (wersja New York lub nowsza).
* [Aplikacja](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ac4c9c57dbb1d090561b186c1396191a/1.3.1?referer=%2Fstore%2Fsearch%3Flistingtype%3Dallintegrations%25253Bancillary_app%25253Bcertified_apps%25253Bcontent%25253Bindustry_solution%25253Boem%25253Butility%26q%3DEvent%2520Management%2520Connectors&sl=sh) została zainstalowana w wystąpieniu usługi ServiceNow.

## <a name="configure-the-servicenow-connection"></a>Konfigurowanie połączenia usługi usługi ServiceNow

1. Użyj linku https://(nazwa wystąpienia). Service-teraz. com/API/sn_em_connector/em/inbound_event? Source = azuremonitor identyfikator URI dla definicji bezpiecznego eksportu.

2. Postępuj zgodnie z instrukcjami w zależności od wersji:
   * [Paryż](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [Orlando](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [Nowy Jork](https://docs.servicenow.com/bundle/newyork-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
