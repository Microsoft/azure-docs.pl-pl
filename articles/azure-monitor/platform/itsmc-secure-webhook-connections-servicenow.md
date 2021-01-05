---
title: Łącznik zarządzania usługami IT — Bezpieczny eksport w programie Azure Monitor — konfiguracja przy użyciu usługi ServiceNow
description: W tym artykule pokazano, jak połączyć narzędzia ITSM produkty/usługi z usługą usługi ServiceNow w przypadku bezpiecznego eksportowania w Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: fc384ffbc246f5ce9fa84b8161cbc4a5226fa5c8
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97843626"
---
# <a name="connect-servicenow-to-azure-monitor"></a>Połącz usługi ServiceNow z Azure Monitor

W poniższych sekcjach znajdują się szczegółowe informacje dotyczące sposobu łączenia produktu usługi ServiceNow i bezpiecznego eksportowania na platformie Azure.

## <a name="prerequisites"></a>Wymagania wstępne

Upewnij się, że zostały spełnione następujące wymagania wstępne:

* Usługa Azure AD jest zarejestrowana.
* Dostępna jest obsługiwana wersja usługi ServiceNow zarządzania zdarzeniami — ITOM (wersja Orlando lub nowsza).

## <a name="configure-the-servicenow-connection"></a>Konfigurowanie połączenia usługi usługi ServiceNow

1. Użyj linku https://(nazwa wystąpienia). Service-teraz. com/API/sn_em_connector/em/inbound_event? Source = azuremonitor identyfikator URI dla definicji bezpiecznego eksportu.

2. Postępuj zgodnie z instrukcjami w zależności od wersji:
   * [Paryż](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [Orlando](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [Nowy Jork](https://docs.servicenow.com/bundle/newyork-it-operations-management/page/product/event-management/task/azure-events-authentication.html)