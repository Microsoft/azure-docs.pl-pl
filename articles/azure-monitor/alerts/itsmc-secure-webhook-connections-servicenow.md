---
title: Łącznik zarządzania usługami IT — Bezpieczny eksport w programie Azure Monitor — konfiguracja przy użyciu usługi ServiceNow
description: W tym artykule pokazano, jak połączyć narzędzia ITSM produkty/usługi z usługą usługi ServiceNow w przypadku bezpiecznego eksportowania w Azure Monitor.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: f296037a56b25255c7f7c7d48a8e8b7d4889052f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041641"
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
