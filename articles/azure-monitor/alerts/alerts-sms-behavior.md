---
title: Zachowanie alertów SMS w grupach akcji
description: Format wiadomości SMS i odpowiadanie na wiadomości SMS w celu anulowania subskrypcji, ponownej subskrypcji lub poprosić o pomoc.
author: dkamstra
ms.author: dukek
services: monitoring
ms.topic: conceptual
ms.date: 02/16/2018
ms.openlocfilehash: e46517865ba01a0d4d113696fbadabf5ae7b0105
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037935"
---
# <a name="sms-alert-behavior-in-action-groups"></a>Zachowanie alertów SMS w grupach akcji

## <a name="overview"></a>Omówienie 
Grupy akcji umożliwiają skonfigurowanie listy akcji. Te grupy są używane podczas definiowania alertów. upewnienie się, że dana grupa akcji zostanie powiadomiona o wyzwoleniu alertu. Jedną z obsługiwanych akcji jest wiadomość SMS; Powiadomienia SMS obsługują komunikację dwukierunkową. Użytkownik może odpowiedzieć na wiadomość SMS, aby:

- **Anuluj subskrypcję alertów:** Użytkownik może anulować subskrypcję wszystkich alertów programu SMS dla wszystkich grup akcji lub pojedynczej grupy akcji.
- **Zasubskrybuj alerty:** Użytkownik może dokonać ponownej subskrypcji wszystkich alertów programu SMS dla wszystkich grup akcji lub pojedynczej grupy akcji.  
- **Poproś o pomoc:** Użytkownik może zażądać więcej informacji na temat wiadomości SMS. Są one przekierowywane do tego artykułu.

W tym artykule opisano zachowanie alertów SMS oraz akcje odpowiedzi, które użytkownik może wykonać na podstawie ustawień regionalnych użytkownika:

## <a name="receiving-an-sms-alert"></a>Otrzymywanie alertu programu SMS
Odbiornik SMS skonfigurowany jako część grupy akcji odbiera wiadomość SMS po wyzwoleniu alertu. Wiadomość SMS zawiera następujące informacje:
* Krótka wartość grupy akcji, do której wysłano ten alert
* Tytuł alertu

| REPLY | Opis |
| ----- | ----------- |
| WYŁĄCZA `<Action Group Short name>` | Wyłącza dalsze wiadomości SMS z grupy akcji |
| MOGŁY `<Action Group Short name>` | Umożliwia ponowne włączenie wiadomości SMS z grupy akcji |
| KOMUNIKAT | Wyłącza dalsze wiadomości SMS ze wszystkich grup akcji |
| START | Ponowne włączenie programu SMS ze wszystkich grup akcji |
| POMOC | Do użytkownika jest wysyłana odpowiedź z linkiem do tego artykułu. |

>[!NOTE]
>Jeśli użytkownik anulował subskrypcję alertów SMS, ale zostanie dodany do nowej grupy akcji; otrzymają one alerty programu SMS dla nowej grupy akcji, ale pozostaną bez subskrypcji ze wszystkich poprzednich grup akcji.

## <a name="next-steps"></a>Następne kroki
Zapoznaj się z [omówieniem alertów dziennika aktywności](./alerts-overview.md) i Dowiedz się, jak uzyskać alerty  
Dowiedz się więcej na temat [ograniczania szybkości SMS](alerts-rate-limiting.md)  
Dowiedz się więcej na temat [grup akcji](./action-groups.md)