---
title: Ograniczanie szybkości dla wiadomości SMS, wiadomości e-mail i powiadomień wypychanych
description: Dowiedz się, w jaki sposób platforma Azure ogranicza liczbę powiadomień SMS, poczty e-mail, aplikacji wypychanych lub elementów webhook z grupy akcji.
author: dkamstra
ms.author: dukek
ms.topic: conceptual
ms.date: 3/12/2018
ms.subservice: alerts
ms.openlocfilehash: 39b9fd8396601d777aa802a147bee3acc4a22cd0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87045304"
---
# <a name="rate-limiting-for-voice-sms-emails-azure-app-push-notifications-and-webhook-posts"></a>Ograniczanie szybkości dla głosu, wiadomości SMS, wiadomości e-mail, powiadomień wypychanych aplikacji platformy Azure i wpisów elementu webhook
Ograniczanie szybkości polega na zawieszeniu powiadomień, które są wykonywane, gdy zbyt wiele jest wysyłanych na określony numer telefonu, adres e-mail lub urządzenie. Ograniczanie szybkości daje gwarancję, że alerty są zarządzane i umożliwiają podejmowanie akcji.

Progi limitu szybkości są następujące:

- **SMS**: nie więcej niż 1 SMS co 5 minut.
- **Głos**: nie więcej niż 1 wywołanie głosowe co 5 minut.
- **Poczta e-mail**: nie więcej niż 100 wiadomości e-mail w ciągu godziny.
 
  Inne akcje nie są ograniczone.

## <a name="rate-limit-rules"></a>Reguły limitu szybkości
- Określony numer telefonu lub adres e-mail jest naliczany proporcjonalnie, gdy otrzymuje więcej komunikatów niż zezwala na wartość progowa.
- Numer telefonu lub adres e-mail może być częścią grup akcji w wielu subskrypcjach. Ograniczanie szybkości ma zastosowanie do wszystkich subskrypcji. Stosuje się to zaraz po osiągnięciu progu, nawet jeśli komunikaty są wysyłane z wielu subskrypcji.
- Jeśli adres e-mail jest ograniczony, do przekazywania ograniczeń szybkości są wysyłane dodatkowe powiadomienia. Wiadomości e-mail po wygaśnięciu ograniczenia szybkości.

## <a name="next-steps"></a>Następne kroki ##
* Dowiedz się więcej na temat [zachowania alertu programu SMS](alerts-sms-behavior.md).
* Zapoznaj się z [omówieniem alertów dziennika aktywności](alerts-overview.md)i Dowiedz się, jak otrzymywać alerty.  
* Informacje o sposobie [konfigurowania alertów za każdym razem, gdy jest ogłaszane powiadomienie o kondycji usługi](../../service-health/alerts-activity-log-service-notifications-portal.md).
