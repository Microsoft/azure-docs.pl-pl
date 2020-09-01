---
title: Co nowego w usłudze Azure ARC z obsługą serwerów (wersja zapoznawcza) Agent
description: W tym artykule znajdują się informacje o wersji dla agenta usługi Azure Arc Enabled (wersja zapoznawcza). W przypadku wielu problemów podsumowujących istnieją linki do dodatkowych szczegółów.
ms.topic: conceptual
ms.date: 08/31/2020
ms.openlocfilehash: 0f5060189d6f9cac620b49f414e0e27bed3e356e
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236769"
---
# <a name="whats-new-with-azure-arc-enabled-servers-preview-agent"></a>Co nowego w usłudze Azure ARC z obsługą serwerów (wersja zapoznawcza) Agent

Agent połączonej maszyny z obsługą usługi Azure ARC (wersja zapoznawcza) nie otrzymuje ulepszeń na bieżąco. Aby zachować aktualność w zakresie najnowszych zmian, ten artykuł zawiera informacje na temat:

- Najnowsze wersje
- Znane problemy
- Poprawki błędów

## <a name="august-2020"></a>Sierpień 2020 r.

Wersja: 0,11

- Obsługa Ubuntu 20,04.

- Ulepszenia niezawodności wdrożeń rozszerzeń.

### <a name="known-issues"></a>Znane problemy

W przypadku korzystania ze starszej wersji agenta systemu Linux i skonfigurowania jej do korzystania z serwera proxy należy ponownie skonfigurować ustawienie serwera proxy po uaktualnieniu. Aby to zrobić, uruchom `sudo azcmagent_proxy add http://proxyserver.local:83`.

## <a name="next-steps"></a>Następne kroki

Przed dokonaniem oceny lub włączeniem serwerów z obsługą łuku (wersja zapoznawcza) na wielu maszynach hybrydowych Przejrzyj [Omówienie agenta połączonej maszyny](agent-overview.md) , aby poznać wymagania, szczegóły techniczne dotyczące agenta i metod wdrażania.