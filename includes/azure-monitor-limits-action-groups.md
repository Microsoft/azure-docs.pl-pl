---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 10/01/2020
ms.author: robb
ms.custom: include file
ms.openlocfilehash: 6f9405e1b402b029b628821824a1727dd825a031
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101734048"
---
| Zasób | Limit domyślny | Limit maksymalny |
| --- | --- | --- |
| Wypychanie aplikacji platformy Azure | 10 akcji aplikacji platformy Azure na grupę akcji. | Analogicznie jak domyślne |
| E-mail | 1 000 akcji poczty e-mail w grupie akcji.<br>Nie więcej niż 100 wiadomości e-mail w ciągu godziny.<br>Zobacz również [informacje dotyczące ograniczania szybkości](../articles/azure-monitor/alerts/alerts-rate-limiting.md). | Analogicznie jak domyślne |
| ITSM | 10 akcji narzędzia ITSM w grupie akcji. | Analogicznie jak domyślne | 
| Aplikacja logiki | 10 akcji aplikacji logiki w grupie akcji. | Analogicznie jak domyślne |
| Element Runbook | 10 akcji elementu Runbook w grupie akcji. | Analogicznie jak domyślne |
| SMS | 10 akcji programu SMS w grupie akcji.<br>Nie więcej niż 1 komunikat SMS co 5 minut.<br>Zobacz również [informacje dotyczące ograniczania szybkości](../articles/azure-monitor/alerts/alerts-rate-limiting.md). | Analogicznie jak domyślne |
| Połączenia głosowe | 10 akcji głosowych w grupie akcji.<br>Nie więcej niż 1 wywołanie głosowe co 5 minut.<br>Zobacz również [informacje dotyczące ograniczania szybkości](../articles/azure-monitor/alerts/alerts-rate-limiting.md). | Analogicznie jak domyślne |
| Webhook | 10 akcji elementu webhook w grupie akcji.  Maksymalna liczba wywołań elementu webhook wynosi 1500 na minutę na subskrypcję. Inne limity są dostępne w [informacjach specyficznych dla akcji](../articles/azure-monitor/alerts/action-groups.md#action-specific-information).  | Analogicznie jak domyślne |