---
title: Przekazuj połączenie hybrydowe jako procedurę obsługi zdarzeń dla zdarzeń Azure Event Grid
description: Opisuje, w jaki sposób można użyć Azure Relay połączeń hybrydowych jako programów obsługi zdarzeń Azure Event Grid zdarzeń.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: spelluru
ms.openlocfilehash: 0631724e688a71d7e9685f5f0ad738d81e2a8034
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598430"
---
# <a name="relay-hybrid-connection-as-an-event-handler-for-azure-event-grid-events"></a>Przekazuj połączenie hybrydowe jako procedurę obsługi zdarzeń dla zdarzeń Azure Event Grid
Program obsługi zdarzeń jest miejscem, w którym zdarzenie jest wysyłane. Procedura obsługi wykonuje kilka dalszych działań w celu przetworzenia zdarzenia. Kilka usług platformy Azure jest automatycznie konfigurowanych do obsługi zdarzeń, a **Azure Relay** jest jednym z nich. 

Za pomocą usługi Azure **Relay połączenia hybrydowe** wysyłać zdarzenia do aplikacji znajdujących się w sieci przedsiębiorstwa i nie mają dostępnego publicznie punktu końcowego.

## <a name="tutorials"></a>Samouczki
Zapoznaj się z poniższym samouczkiem, aby zapoznać się z przykładem użycia połączenia hybrydowego Azure Relay jako procedury obsługi zdarzeń. 

|Tytuł  |Opis  |
|---------|---------|
| [Samouczek: wysyłanie zdarzeń do połączenia hybrydowego](custom-event-to-hybrid-connection.md) | Wysyła niestandardowe zdarzenie do istniejącego połączenia hybrydowego w celu przetworzenia przez aplikację odbiornika. |

## <a name="next-steps"></a>Następne kroki
Listę obsługiwanych programów obsługi zdarzeń zawiera artykuł [obsługi zdarzeń](event-handlers.md) . 